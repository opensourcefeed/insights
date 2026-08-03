---
layout: post
title: "I ran every major immutable Linux distro for a month"
categories: [linux, tutorial, open-source]
tags: [immutable-linux, fedora-silverblue, nixos, bazzite, opensuse-aeon, vanilla-os, linux-desktop]
description: "A hands-on, week-per-distro comparison of Silverblue, Aeon, Vanilla OS, Bazzite, and NixOS on a real work laptop — including proxy setup for blocked mirrors."
---

I've been breaking Linux installs since 2011.

My first one was Ubuntu 11.04, and I killed it in nine days by force-installing an Nvidia driver from a forum post written by someone who clearly hated me personally. No backup. No snapshots. Just a black screen and a blinking cursor that felt like judgment.

I got better at fixing things. I never got better at not breaking them.

That's the whole pitch for immutable distros. The root filesystem is read-only, and your updates arrive as complete images instead of a thousand individual packages that might fight each other at 2AM. If something goes wrong, you pick the previous boot entry and you're back.

No rescue USB. No chroot. No forum post from someone who hates you.

I'd read about all of this for years without ever actually living in it.

So I did what any reasonable person would do. I wiped my ThinkPad T14 five separate times over five weeks, ran each distro as my only machine for a full working week, and took notes on every single thing that annoyed me.

After all of it, **Fedora Silverblue is the one I'd hand to most people, and Bazzite is the one that stayed on my laptop.**

Here's everything I found out.

![Five immutable Linux distros compared on a ThinkPad T14](/insights/assets/images/post-images/immutable-linux-distros-review.png)

A split-screen desktop showing five different Linux environments — GNOME on Silverblue, KDE on Bazzite, NixOS terminal — on a dark-themed ThinkPad laptop.

## The contenders

Five projects. Same hardware. Same seven-day test window each.

**Fedora Silverblue** is the reference implementation: rpm-ostree, GNOME, Flatpak-first, tracking the same [Fedora Workstation releases](/distribution/fedora) and backed by Red Hat's engineering. If you've heard the phrase "immutable Linux," you've probably heard this name attached to it.

**openSUSE Aeon** is the opinionated one, the atomic cousin of [openSUSE Tumbleweed](https://www.opensourcefeed.org/opensuse-tumbleweed-review/). GNOME only. BTRFS snapshots via transactional-update. Aeon doesn't ask what you want. Aeon tells you.

**Vanilla OS** is the ambitious one, with ABRoot A/B partitioning plus Apx, which lets you install packages from Arch, Fedora, or Debian repos inside managed containers. Wild idea. Genuinely wild.

**Bazzite** is the gaming one, built on Fedora's atomic base via the [Universal Blue project](https://github.com/ublue-os/bazzite). It ships with the drivers and codecs already handled, which is either cheating or exactly the point depending on your mood.

**NixOS** is the odd one out. It isn't image-based like the others, but it's declarative and atomic in its own way. Your entire system lives in a config file, and every generation is a rollback point.

All five promise the same core thing: an OS you can't accidentally destroy.

Only some of them mean it in a way you'll enjoy.

## Rollbacks separate the pretenders from the contenders

This is the headline feature, so I tested it the honest way.

I broke each system on purpose.

Bad kernel argument. Half-finished driver install. A layered package that conflicts with a base package. The kind of mistake you make when you're tired and confident, which is the most dangerous combination in computing.

**Silverblue was flawless.** Run rpm-ostree rollback, reboot, done. And even when I didn't run the command, the previous deployment was sitting right there in the GRUB menu waiting for me. Two deployments are pinned by default. It has never not worked.

**Aeon was equally solid but much quieter about it.** Snapshots happen and you don't think about them. When I torched the system, the boot menu had a snapshot from before the damage and it came back clean. The recovery is less discoverable than Silverblue's, but it's just as real.

**Bazzite inherits Silverblue's machinery** and layers a friendlier update daemon on top. Rollbacks worked identically. Nothing to add here, which is the highest compliment I can pay a recovery system.

**Vanilla OS took the most interesting approach and gave me the most anxiety.** ABRoot swaps between two full root partitions, so you're always running one while the other stages the next update. Conceptually it's the cleanest model in the test. In practice, updates took noticeably longer, and I had one transaction that stalled long enough for me to start writing an angry note about it before it finished. It did finish. But I noticed.

**NixOS is a different animal.** Rollbacks aren't a recovery feature there, they're a side effect of how the whole system works. Every rebuild creates a generation, and every generation stays in the boot menu until you garbage-collect it. I broke it four separate times and never once felt worried.

**Bottom line:** all five deliver on the core promise. Silverblue and NixOS make recovery obvious, Aeon makes it invisible, and Vanilla OS makes it slow.

## The part nobody warns you about: layering

Here's what the marketing doesn't emphasize.

On a traditional distro, installing software takes fifteen seconds. On most of these, if the software isn't available as a Flatpak, it takes fifteen seconds *and a reboot*.

Layering a package with [rpm-ostree install](https://github.com/coreos/rpm-ostree) doesn't put it on your running system. It builds a new deployment that includes it, and you reboot into that deployment.

Want to install a font utility? Reboot.

Want a CLI tool that isn't in your container? Reboot.

You adapt fast. You batch your installs. You start living inside distrobox for anything development-related, brushing up on your [essential Linux commands](https://www.opensourcefeed.org/1-essential-linux-commands/) along the way, which honestly you should be doing anyway.

But the first three days are a genuine adjustment, and anyone who tells you otherwise is selling something.

Aeon handles this most gracefully by pushing you hard toward Flatpak and containers, and by simply not making layering feel like a normal thing to do. That sounds restrictive. It's actually kind, because it stops you fighting the design.

Vanilla OS tries to solve it with Apx, and when Apx works it's the most impressive thing in this entire test. Installing an Arch package on a Debian-based immutable system, in a managed container, with a desktop entry that just appears in your menu, is genuinely a little bit magic.

When it doesn't work, you're debugging a container abstraction on top of a partition abstraction, and the error messages assume you already know which layer failed.

NixOS sidesteps the whole issue. You don't layer, you declare. Add the package to your config, rebuild, and it's there, usually without a reboot. The tradeoff is that you've now signed up for learning the Nix language, and that is not a weekend project.

## Hardware and drivers

This is where the gap opened up.

The T14 has an AMD chip, so I got the easy path. Everything worked out of the box on all five: Wi-Fi, suspend, external displays, and the fingerprint reader on four of the five.

But I also tested each install on a second machine with Nvidia graphics, because that's where immutable systems have historically fallen over.

**Bazzite won this outright, and it isn't close.** The Nvidia image ships with the drivers baked in. You boot, you have working acceleration, you move on with your life. No layering, no reboot cycle, no akmods.

**Silverblue** works fine, but you're layering the RPM Fusion driver and rebooting, and every major version upgrade becomes a moment where you hold your breath.

**Aeon** doesn't officially support Nvidia in the way you'd want it to. Getting there is possible. Getting there is also not the experience Aeon is designed to give you.

**Vanilla OS** managed it, eventually.

**NixOS** required exactly two lines in the config and then worked perfectly, which is either the best or the most annoying answer in this test depending on how you feel about reading documentation.

## Codecs, Flathub, and the small stuff

Fresh Silverblue ships with a filtered Flatpak remote and no proprietary codecs. Before you can watch a video, you're enabling Flathub and installing the [FFmpeg stack](https://www.opensourcefeed.org/ffmpeg-commands/). It's five minutes of terminal work that a new user will find baffling and that Fedora has good legal reasons for.

Bazzite has already done all of it for you.

Aeon has Flathub enabled and steers you to it immediately.

Vanilla OS handles it during first-run setup, which is the right place for it.

NixOS expects you to have opinions about it and write them down.

Small thing. Sets the tone of the entire first hour.

## When the mirrors won't talk to you

This one caught me off guard, and it's the section I wish someone had written before I needed it.

Atomic distros don't fetch a handful of packages, they fetch a whole system image. That's fine on home broadband. It's considerably less fine on a corporate network with an egress filter, a university connection that throttles anything resembling a bulk transfer, or a region where the CDN endpoint your distro uses simply isn't reachable.

On a traditional distro you'd swap mirrors and move on. On an atomic one, a blocked remote means you can't update the operating system at all. Not slowly. At all.

I hit this at a client site and lost two days before I worked out what was happening.

The fix is a proxy, and the annoying part is that setting one in your shell does nothing. These updates don't run in your shell. They run in a system daemon, and daemons don't inherit your environment.

Here's what actually works on each system.

### Silverblue and Bazzite (rpm-ostree)

You have two places to set this, and they solve different problems.

For the OSTree remote itself, `/etc/ostree/remotes.d/` holds one `.conf` file per remote, and libostree accepts a `proxy=` key inside the remote section:

```
[remote "fedora"]
url=https://ostree.fedoraproject.org
gpg-verify=true
proxy=http://user:pass@proxy.example.net:8080
```

For everything else rpm-ostree does, including overlay packages, metadata, and the container pull path on Universal Blue images, you want the daemon's own environment:

```
sudo systemctl edit rpm-ostreed.service

[Service]
Environment="https_proxy=http://user:pass@proxy.example.net:8080"
Environment="http_proxy=http://user:pass@proxy.example.net:8080"
Environment="no_proxy=localhost,127.0.0.1"
```

Then run `sudo systemctl restart rpm-ostreed`. Note the lowercase variable names. Curl-based tooling is inconsistent about case, and [NixOS has an open issue about exactly this](https://github.com/NixOS/nixpkgs/issues/401710), so setting both cases is the pragmatic move.

### Flatpak

System-wide installs run through `flatpak-system-helper.service`, not through your terminal, so it's the same drop-in pattern:

```
sudo systemctl edit flatpak-system-helper.service
```

Add the same `Environment=` lines. Per-user installs (`flatpak --user`) do read your shell environment, which is why people set a proxy, watch a user install succeed, and then get baffled when the system install still hangs.

### openSUSE Aeon

Aeon does it the SUSE way, which means `/etc/sysconfig/proxy`:

```
PROXY_ENABLED="yes"
HTTP_PROXY="http://proxy.example.net:8080"
HTTPS_PROXY="http://proxy.example.net:8080"
NO_PROXY="localhost, 127.0.0.1"
```

transactional-update picks this up because zypper does. Cleanest implementation of the five.

### NixOS

Declarative, naturally. The [networking.proxy options](https://search.nixos.org/options?query=networking.proxy) go straight into `configuration.nix`:

```nix
networking.proxy.default = "http://proxy.example.net:8080";
networking.proxy.noProxy = "127.0.0.1,localhost";
```

That sets the variables for the nix-daemon and for system services generally. Rebuild, and it becomes part of the system definition, which means the next machine you provision from this config already knows.

### Vanilla OS

ABRoot pulls OCI images, so you're really configuring the container stack underneath it. Podman honours the standard proxy variables, and setting them on the relevant service unit with the same `systemctl edit` pattern was what got mine working. This is the most fiddly of the five, which will surprise nobody who has read the rest of this article.

### A note on choosing the proxy itself

If you're behind a corporate proxy you don't get a choice. Use what IT gave you and skip this paragraph.

If you're working around a geo-blocked mirror, or checking whether a distro's CDN is reachable from outside your own region, you need something you actually control. That means endpoints in the region you're targeting and enough bandwidth that a 2GB image transfer doesn't time out halfway through. Free proxy lists are useless here, and I've watched three separate OSTree pulls die at 60% on scraped endpoints. Since you're fetching from public CDNs rather than anything that cares who you are, [datacenter proxies](https://roundproxies.com/datacenter-proxy/){:rel="nofollow sponsored"} are the sensible fit: they're the cheapest option per gigabyte and by far the fastest, which is exactly what a multi-gigabyte image pull rewards.

Whatever you use, test it with a small flatpak update before you point a full system upgrade at it.

And because `/etc` stays writable on every distro in this test, all of this configuration survives image updates. Which is, pleasingly, the design promise working exactly as intended.

## The numbers

**Fedora Silverblue.** GNOME, rpm-ostree, roughly 10 GB installed. The reference build, with the best documentation, the largest community, and the most predictable upgrade path. Layering costs you reboots and Nvidia costs you nerve. Still the safest recommendation for anyone who wants immutable Fedora and nothing weird.

**openSUSE Aeon.** GNOME only, transactional-update, roughly 9 GB installed. The most coherent vision in the test, and boring in the way good infrastructure is boring. If you accept its opinions completely you'll have a wonderful time. If you want to argue with it, pick something else.

**Vanilla OS.** GNOME, ABRoot plus Apx, roughly 11 GB installed. The most interesting ideas here by a distance, and Apx is a genuinely novel answer to a real problem. Updates are the slowest in the test and the abstractions stack up fast when things break. Watch this one.

**Bazzite.** KDE or GNOME, Universal Blue base, roughly 15 GB installed. Everything handled: drivers, codecs, gaming stack, hardware quirks for handhelds. The largest image and the least amount of work. It isn't just for gaming machines. It's the easiest atomic desktop in this test, full stop.

**NixOS.** Any desktop, declarative config, size varies wildly. The most powerful and the least approachable, with reproducibility that nothing else here can match. The learning curve will consume a solid month before it starts paying you back.

## The verdict

**For most people:** Fedora Silverblue. It's the well-trodden path, the docs are good, and when you search an error message you'll find someone who has already solved it.

**For people who want it to just work on day one:** Bazzite. Drivers done, codecs done, no first-hour terminal ritual. The "gaming distro" label undersells it badly.

**For minimalists who like strong opinions:** openSUSE Aeon. Clean, quiet, thoughtfully constrained.

**For tinkerers who want to see where this is going:** Vanilla OS. Not the smoothest ride today, but some of the best ideas in the space.

**For infrastructure people, and anyone who has ever wanted to rebuild their exact laptop from a text file:** NixOS. Pay the learning cost or don't. There's no partial credit.

## Who should skip all of this?

Be honest with yourself.

If you install packages from source regularly, if your workflow depends on system-level tools that expect a writable /usr, or if you've got proprietary software with an installer that assumes a traditional layout, immutable will fight you every day.

There's no shame in a conventional Fedora or Debian install with BTRFS snapshots. You get most of the safety with none of the friction.

Immutable isn't better. It's a different set of tradeoffs, and the trade is flexibility for resilience.

Know which one you actually need.

## Frequently asked questions

**Does immutable mean I can't change anything?** No. Your home directory is fully writable, /etc is writable, and you can layer packages onto the base image. What's locked down is the core system image, which is exactly the part you never wanted to edit by hand anyway.

**Do I lose access to normal package managers?** Not really. You get Flatpak for graphical apps, containers via distrobox or toolbox for CLI and development tools, and package layering for the rare thing that has to live on the host. Different habits, same software.

**Is it slower?** Day to day, no. Updates are slower because you're pulling an image rather than individual packages, and they apply on reboot rather than live. Actual running performance is unchanged.

**Can I game on these?** Yes, and Bazzite in particular is built for it. Steam, Proton, and controller support all work. Anti-cheat limitations are the same as on any Linux distro, which is a game developer problem rather than a distro problem.

**What happens if an update breaks something?** You reboot into the previous deployment. That's the entire feature. It takes about ninety seconds and requires no rescue media.

**My network blocks the update mirrors. Am I stuck?** No, but the fix isn't obvious. Setting a proxy in your shell won't help, because updates run in a system daemon that doesn't inherit your environment. You need it set on the service itself: a systemctl edit drop-in on rpm-ostreed and flatpak-system-helper, /etc/sysconfig/proxy on Aeon, or networking.proxy in configuration.nix on NixOS. See the mirrors section above.

**Which should a complete beginner pick?** Bazzite or Silverblue. Bazzite if you want fewer decisions, Silverblue if you want the biggest pool of documentation and community answers.

## My laptop right now

Bazzite is on the T14. Not because I game much on it, but because after five weeks of testing it was the one that stopped asking me for things.

Silverblue is on the desktop, where I don't mind doing a bit of setup work.

NixOS is on the home server, where reproducibility is worth every hour of the learning curve and I never have to think about the desktop experience.

Aeon and Vanilla OS live on a spare SSD I swap in when I want to check on them. I'll be checking on Vanilla OS often, because those ideas are going somewhere.

And that Ubuntu 11.04 install from 2011?

Still dead. Still my fault.

But I've broken all five of these systems deliberately, repeatedly, and with real enthusiasm, and every single time I was back at my desktop before the coffee went cold.

That's the whole point.