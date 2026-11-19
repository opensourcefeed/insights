---
layout: post
title: "Open-source image-to-video models: self-host or not?"
categories: [open-source, tools, ai]
tags: [image-to-video, wan2-2, comfyui, ltx-video, hunyuanvideo, self-hosting, ai-video]
description: "Compare open-source image-to-video models like Wan2.2, LTX-Video and HunyuanVideo, what self-hosting costs, and when a hosted tool is the better pick."
---

**Two** years ago, turning a single photo into a convincing video clip was something you rented from a handful of closed services. That is no longer the case. Several labs have released image-to-video models with open weights, the community has wrapped them in node-based workflows, and a decent gaming GPU can now produce short 720p clips on a desk at home.

That raises a fair question for anyone who prefers open tools: should you self-host image-to-video, or is a hosted service still the more sensible choice? The honest answer is that it depends on what you are making, how often and on what hardware. This piece walks through the main open options, what it really takes to run them, and where the trade-offs sit.

## What is actually available

The open image-to-video landscape changes quickly, but a few projects have become the usual starting points.

**Wan.** Alibaba's Wan family is probably the most widely used open video model right now. The Wan2.2 repository ships code under the Apache 2.0 license and includes a 14B image-to-video model that supports 480p and 720p, plus a smaller 5B model that handles both text-to-video and image-to-video at 720p and, according to the project, runs on a consumer card such as an RTX 4090. That smaller model is the reason many hobbyists can experiment at all.

**HunyuanVideo-I2V.** Tencent's image-to-video variant of HunyuanVideo produces strong results, particularly with people, but it is heavier to run and comes under Tencent's own community license rather than a standard open-source one.

**LTX-Video.** Lightricks built LTX-Video with speed in mind. It will not always match the larger models on detail, but it generates quickly, which makes it handy for iterating on ideas.

**CogVideoX.** One of the earlier open models with an image-to-video version. It has been overtaken on quality, but it is well documented and still turns up in tutorials and research code.

Most people do not run these models from raw Python scripts. They use ComfyUI, the node-based interface that has become the de facto front end for open image and video generation. Community workflows for each model are easy to find, and they handle things like loading quantised weights, setting frame counts and upscaling the output.

## What self-hosting really costs

The model weights are free. Running them is not.

**VRAM is the hard limit.** Video models need far more memory than image models, because they work on many frames at once. A 24 GB card is the comfortable baseline for the mid-sized models at 720p. With 12 or 16 GB you can still get results, but you will lean on quantised weights, CPU offloading and lower resolutions, all of which cost time or quality. Below that, most people give up and rent a GPU.

**Time adds up.** On a single consumer GPU, a five-second clip from one of the larger models can take anywhere from a few minutes to well over ten, depending on resolution, step count and how aggressively you offload. That is fine for an evening project. It is less fine when you need twenty variations by tomorrow morning.

**Setup is not trivial.** Getting the right versions of PyTorch, CUDA, custom nodes and model files to agree with each other is a rite of passage for anyone using ComfyUI. Things break when a node updates. Expect to spend real time reading GitHub issues.

**Licenses need reading.** "Open weights" is not the same thing as "open source," and the difference matters if you plan to use clips commercially. Apache 2.0 code is straightforward. Some model releases come with custom community licenses that add conditions on use, scale or region. Check the license file in the repository and on the model card before you build anything on top.

## Where open models shine

Self-hosting makes a lot of sense in a few situations.

**Privacy.** If you are working with photos you would rather not upload anywhere, such as family pictures, unreleased product shots or client material under NDA, running locally keeps everything on your own machine.

**Control.** Open models let you change things hosted services do not expose: sampler settings, seeds, frame interpolation, LoRA fine-tunes trained on a particular character or style, and custom pipelines that chain several models together. For researchers and tinkerers, this is the whole point.

**Volume on owned hardware.** If you already own a capable GPU and generate a lot of clips, the marginal cost of each one is mostly electricity. Over hundreds of renders, that can be far cheaper than paying per second of output.

**Learning.** There is no better way to understand how these models behave than to run them, break them and read the code. Plenty of people who end up using hosted tools still started here.

## Where hosted tools still make sense

There are equally good reasons not to self-host.

**No suitable GPU.** Laptops with integrated graphics, Macs without enough unified memory, and older cards simply are not practical for video generation. Renting a cloud GPU is possible, but by the time you have set up the environment and paid for idle time, a hosted service is often cheaper for occasional use.

**Access to closed models.** Some of the strongest image-to-video models, especially for natural human motion and generated sound, are still closed and only available through an API. If you want to compare open and closed results on the same photo, a hosted service is the only way to reach the closed side.

**Occasional use.** If you make a handful of clips a month for social posts or a presentation, spending a weekend on ComfyUI setup is hard to justify. A browser-based [Image to Video AI](https://stivio.ai/) service lets you upload a photo, pick a model and download an MP4 in a few minutes, with nothing installed and no driver versions to track.

**Deadlines.** Hosted services run on server-grade hardware and do not tie up your own machine while they render. When the clip is needed today, that matters more than ideological purity.

## A practical way to decide

A simple rule of thumb works for most people.

If you have a GPU with 16 GB or more of VRAM, enjoy tinkering and care about privacy or fine control, start with Wan2.2's smaller model in ComfyUI. Get one workflow running end to end before you try anything fancier.

If you have weaker hardware, need results quickly or only make clips now and then, use a hosted tool and spend your time on the photo and the prompt instead of the toolchain.

And if you are somewhere in between, there is nothing wrong with doing both. Many creators prototype locally with an open model, then send the shots that need the best possible motion to a hosted model. The skills carry over either way: a sharp, well-lit source photo, a prompt that describes movement rather than the scene, and short clips that leave the model less room to drift.

## The bigger picture

The gap between open and closed video models has narrowed far faster than most people expected. Open weights have made it possible for researchers, small studios and hobbyists to experiment without asking permission or paying per frame, and that pressure has pushed the whole field forward.

Hosted services are not going away, and for many users they will remain the easier option. But for the first time, choosing open-source image-to-video is a practical decision rather than a compromise. If you have the hardware and the patience, it is well worth an evening to find out what your own machine can do.