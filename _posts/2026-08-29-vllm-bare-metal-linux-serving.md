---
layout: post
title: "Serving open-weight models with vLLM on bare metal"
categories: [linux, tools, tutorial, open-source]
tags: [vllm, llm, gpu, inference, bare-metal, self-hosting, open-weight-models, cuda]
description: "A practical guide to running vLLM on bare-metal Linux: installation, GPU memory tuning, multi-GPU pitfalls, security, and monitoring."
---

**Running** an open-weight model on your own hardware has stopped being a weekend experiment. The weights are Apache or MIT licensed, the serving engine is open source, and the hardware is whatever you can get your hands on. What has not become easier is the operational middle: the gap between a model that loads and a service that survives contact with concurrent users.

vLLM is the most common answer to that gap. Here is what running it on bare metal actually involves, and where it bites.

## Why bare metal rather than a container?

Containers are fine, and for most deployments they are the right call. Bare metal earns its place when you want the driver stack under your control, when you are chasing the last few percent of throughput, or when a hypervisor between you and the GPU is a problem rather than a convenience.

The tradeoff is that nothing is pinned for you. Your driver version, your CUDA runtime, your Python environment and your kernel are all yours to keep consistent, and a mismatch between any two of them surfaces as an error message that rarely names the actual cause.

## What do you actually need installed?

Less than people expect. Per the vLLM installation docs, the requirements are Linux, Python 3.10 to 3.13, and a GPU of compute capability 7.5 or higher, which covers everything from a T4 or an RTX 20-series card up to H100 and B200. Windows is not supported natively, so WSL if you must.

The part that trips people up is CUDA. The published wheels ship pre-compiled CUDA 12.9 binaries, so you do not need a full system CUDA Toolkit to run vLLM. You need it only if you intend to build from source. A working NVIDIA driver is enough. Blackwell cards are the exception worth noting, requiring CUDA 12.8 as a minimum.

```bash
uv venv --python 3.12 --seed
source .venv/bin/activate
uv pip install vllm --torch-backend=auto
```

One caveat from the project's own troubleshooting notes: PyTorch installed through conda statically links NCCL, which causes problems when vLLM tries to use it. If you are on conda and multi-GPU is in your future, fix that before you build anything on top of it.

## How do you get a model serving?

One command. It downloads the weights, allocates the cache and starts an OpenAI-compatible HTTP server on port 8000.

```bash
vllm serve Qwen/Qwen2.5-1.5B-Instruct
```

From there you get `/v1/completions`, `/v1/chat/completions`, `/v1/models` and `/v1/embeddings`, which means any client library written against the OpenAI API points at your box with a changed base URL and nothing else. That compatibility is most of why vLLM won: migrating off a hosted API becomes a configuration change rather than a rewrite.

## Where does the GPU memory actually go?

This is the part worth understanding before you tune anything. vLLM pre-allocates a fraction of the card and manages it itself. The flag is `--gpu-memory-utilization` and it defaults to 0.9, meaning 90 percent of the GPU is reserved at startup. Model weights come out of that pool first, and whatever remains becomes the KV cache.

The KV cache is the thing that determines how many users you can serve at once. Every in-flight sequence holds keys and values for its whole context, so cache capacity, not compute, sets your concurrency ceiling. This is what PagedAttention addresses: by paging the cache the way an OS pages memory, vLLM achieves near-zero waste in KV cache memory, and reports a 2 to 4 times throughput improvement over FasterTransformer and Orca at the same latency.

**Practical consequences.** If a model loads fine but you hit out-of-memory under load, your cache is too small: lower `--max-model-len`, cap concurrency with `--max-num-seqs`, or set `--kv-cache-dtype` to `fp8` on hardware that supports it, which roughly doubles effective cache capacity. If it fails at startup instead, the weights themselves do not fit, and quantisation is the lever: `--quantization` accepts `awq`, `gptq`, `fp8` and `bitsandbytes` among others. There is also `--swap-space`, defaulting to 4 GiB per GPU, which spills to host memory under pressure rather than failing outright.

If you are memory-starved and can spare throughput, `--enforce-eager` disables CUDA graph capture and hands back the memory those graphs occupy.

## What breaks when you add a second GPU?

Tensor parallelism is one flag, `--tensor-parallel-size`, and one constraint people meet the hard way. The model's attention heads must divide evenly by the parallel size. If they do not, vLLM refuses to start with a message naming both numbers, and no amount of flag-tweaking fixes it because the arithmetic is real.

The documented workaround for an awkward GPU count is to leave tensor parallelism at 1 and use `--pipeline-parallel-size` instead, which splits by layer and tolerates uneven divisions. Throughput characteristics differ, but it runs.

The second multi-GPU trap is shared memory. PyTorch coordinates its worker processes through `/dev/shm`, and the default allocation inside a constrained environment is too small. In Docker that means `--ipc=host` or an explicit `--shm-size`; in Kubernetes it means an `emptyDir` mounted at `/dev/shm` with `medium` set to `Memory`. Get it wrong and you see an NCCL initialisation failure, which the vLLM troubleshooting guide attributes to exactly this cause or to a missing `IPC_LOCK` capability. When you are debugging, `NCCL_DEBUG=TRACE` tells you whether traffic is moving over InfiniBand or falling back to sockets, and a socket fallback across nodes will destroy your throughput.

## What should you lock down before it faces anything?

One detail deserves more attention than it gets. vLLM supports an `--api-key` flag, and it is easy to assume that secures the server. It does not secure all of it. Per the project's own documentation, the key authenticates requests under the `/v1`, `/v2` and `/inference` path prefixes only. The `/invocations` endpoint offers the same inference capability and is not authenticated.

If your server is reachable from anything you do not control, put a reverse proxy in front of it and allow only the paths you intend to expose. Do not treat `--api-key` as a boundary. While you are there, note that `VLLM_PORT` and `VLLM_HOST_IP` are internal variables and have nothing to do with where the API server listens, which is a genuinely confusing piece of naming.

## How do you know it is healthy?

There is a `/metrics` endpoint in Prometheus exposition format, with metric names prefixed `vllm:`. Scrape it from the start rather than after your first incident. The numbers that matter are queue depth and KV cache utilisation, because those are the two that tell you whether you are near the concurrency wall before your users find it. There is also a `/health` endpoint, which is what you point a liveness probe at.

## When does self-hosting stop making sense?

Self-hosting is worth it while the work is interesting and the constraints are yours. It gets less appealing when you are maintaining driver compatibility across a fleet, holding capacity for peak traffic that arrives twice a week, and carrying an on-call rotation for a service that is not your product.

The usual next step is not abandoning open weights, it is moving them somewhere else. The same models run on rented dedicated GPUs, or behind [managed inference on European GPU infrastructure](https://orionfactory.ai/inference.php) where someone else owns the driver stack and the pager, which for teams with data residency requirements also settles where the weights and the traffic physically live. The engine and the weights stay open source either way, which is the point of picking them.

Start on your own metal regardless. Understanding where the memory goes is knowledge you keep, whichever way you deploy later.