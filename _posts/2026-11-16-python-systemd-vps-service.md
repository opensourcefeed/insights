---
layout: post
title: "Run a Python service on a VPS with systemd"
categories: [linux, tutorial, tools]
tags: [python, systemd, vps, virtualenv, devops, linux-server, deployment]
description: "Run a Python service on a VPS with systemd and venv: set paths, user, environment variables, restarts, and logs so it survives reboots."
---

**A** Python process started in an SSH terminal can demonstrate that code works. It cannot, by itself, demonstrate that the service will start after a reboot, use the intended environment, write useful logs, or recover from an ordinary crash. On a VPS, those operational details become part of the application. systemd gives Linux a standard way to define a long-running service, while a Python virtual environment gives the application a specific interpreter and dependency set. Treating both as explicit configuration makes a service easier to run and easier to diagnose.

![Terminal window showing a systemd status output for a Python service running on a Linux VPS](/insights/assets/images/post-images/python-systemd-vps-service.png)

<!-- Image prompt: A dark terminal screen showing systemctl status output for a Python service, green "active (running)" line, Linux server rack softly blurred in the background, flat modern style. -->

Consider three common, conditional situations. A small webhook process needs to return after a host restart. A background worker needs a different environment from a developer's laptop. A web API crashes on one malformed input and should restart, but not spin forever. These are not reasons to make a unit file elaborate. They are reasons to state the working directory, executable, environment, user, restart policy, and log path rather than leaving them implicit in a shell history.

Deployment is complete when the service can be started, stopped, observed, and restarted by a documented system interface.

## Separate the interpreter from the service manager

A Python virtual environment is an isolated directory containing a Python interpreter and installed packages. A systemd service unit describes how the operating system starts and supervises a process.

Python documents venv as a mechanism for creating isolated environments. The practical consequence is simple: use the absolute path to the environment's interpreter or executable in ExecStart, not an assumption that an interactive shell has activated it. systemd does not read your shell profile in the way a manual SSH session may. A command that works after 'source venv/bin/activate' can therefore fail in a unit unless the unit names the executable and working directory precisely.

| **Concern** | **Explicit unit setting** | **Verification** |
|---|---|---|
| Program path | ExecStart with absolute path | systemctl status shows command |
| Project files | WorkingDirectory | relative files resolve correctly |
| Privileges | User and Group | process cannot edit unrelated paths |
| Configuration | EnvironmentFile or Environment | required variables are present |
| Recovery | Restart and RestartSec | controlled failure is observable |

## Environment variables need an ownership model

Environment variables are convenient for a port number, deployment mode, endpoint, or a reference to a secret. They are not automatically safe merely because they are outside the source file. Put non-secret configuration in a documented file with controlled permissions; give secrets a separate lifecycle, narrow file permissions, and a rotation process. Do not print the entire environment into application logs when troubleshooting. That shortcut can expose credentials to everyone who can read a journal.

Do not run the Python service as root simply because it needs to bind a port or read a file. Root turns an application defect, a compromised dependency, or an accidental file write into control over the VPS. Use a dedicated unprivileged account, adjust ownership only for the directories the process must use, and place a reverse proxy in front when a public low port is needed. Test the service under that account before enabling automatic restarts.

### A small unit is easier to reason about

A useful initial unit names Description, After=network.target when appropriate, User, WorkingDirectory, ExecStart, Restart, and the configuration source. Some services also need a timeout, a writable state directory, or explicit network ordering; add those because the process needs them, not because another example had them. The systemd service manual documents unit behavior, so use the version installed on the VPS as the final authority for options and their interactions.

1. Create a dedicated service user without a general interactive login where possible.
2. Create the virtual environment and install pinned application dependencies through the project process.
3. Place configuration in a protected file and document its owner and update path.
4. Write the unit with absolute paths, WorkingDirectory, User, and deliberate restart behavior.
5. Reload systemd, start the unit, and inspect status and journal output.
6. Reboot or simulate the relevant failure, then verify the health endpoint and dependent services.

## Restarting is a policy, not a cure

Restart=on-failure can recover a process that exits unexpectedly, but it cannot correct an invalid configuration, exhausted disk, unavailable database, or programming loop. Repeated restart attempts can conceal the original exception and consume resources. Set a pause between restarts, alert on failures, and make the application fail clearly when a dependency is unavailable. For a queue worker, distinguish a lost broker connection from a task error; for an HTTP process, expose a health signal that reflects whether it can serve its promised work.

Do not edit a unit, reload it, and declare success because systemctl start returns quickly. A service can start with the wrong environment, fail after its first request, or restart in a loop. Inspect the journal, check the process user and listening socket, make a harmless request, and verify the declared restart behavior with a controlled failure.

Logs should answer operational questions: what version started, what configuration class was selected, why did it exit, and which request or job failed? Avoid logging tokens or full request bodies. systemd's journal can be queried by unit, which is useful during a release; centralized logs may be needed if the VPS itself becomes unavailable. Pair logs with a simple runbook: how to see status, how to restart safely, how to roll back, and who owns the next decision.

When planning the hosting layer, [consider a US VPS from DeltaHost](https://deltahost.com/vps/vps-us.html) together with your application's CPU, memory, storage, network exposure, and operating responsibilities. **systemd does not make Python code faultless; it makes the process contract explicit enough to test and operate.**

The first goal is modest: after a reboot or a known failure, you should be able to explain exactly why the service is up, down, or waiting. Once that answer is routine, automated deployment and monitoring have a stable foundation.

A release procedure should preserve that clarity. Build or obtain the intended artifact, update the virtual environment through a controlled dependency process, validate configuration before restart, then observe the unit and a functional check. Keep the preceding known-good version or deployment reference long enough to roll back deliberately; do not rely on a half-edited directory as a recovery strategy. If database migrations are part of the release, state their compatibility order separately from the systemd restart. The service manager supervises one process, while the release remains a coordinated change across code, data, and configuration.

This boundary also improves incident handoff. Another operator can inspect one unit, one environment file, and one journal identifier rather than reconstructing a screen session. That is a practical benefit even for a very small service.