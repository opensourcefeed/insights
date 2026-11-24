---
layout: post
title: "Tiny11 explained: strip down Windows 11 for older PCs"
categories: [tools, open-source, windows]
tags: [tiny11, tiny11builder, windows-11, powershell, lightweight-windows, virtual-machines, older-pcs, github]
description: "Learn how Tiny11 and the open-source Tiny11builder script strip Windows 11 down for older PCs and VMs, what it removes, and what to know first."
image: /insights/assets/images/post-images/tiny11-builder-windows-11-guide.png
---

Many people who use computers have faced a common problem. You need a fast, lightweight Windows setup for testing or for older programs, but instead, you get a heavy Windows 11 installation that takes up over 20 GB of space. It comes with background services, tracking tools, and strict hardware rules.

![Tiny11 running a lightweight Windows 11 desktop built with the Tiny11builder script](/insights/assets/images/post-images/tiny11-builder-windows-11-guide.png)

[Tiny11](https://tiny11.net/) is a popular solution to this problem. It is a smaller, cleaner version of Windows 11 made by a developer named NTDEV. Many people first heard about Tiny11 when they saw it running on computers with only 2 GB of RAM, but the best part for the open-source community is a tool called Tiny11builder. It is a free script that lets you make your own stripped-down Windows image using a real Microsoft file.

Whether you want to bring an old laptop back to life, make quick virtual machines on a Linux computer, or just want a clean operating system, this guide will show you how Tiny11 works, what it removes, and what you should think about before using it.

## What is Tiny11 and Tiny11builder?

Tiny11 is a trimmed-down version of Windows 11 Pro. It is built to remove the extra files and services you do not need while keeping the main parts you do need. While a normal Windows 11 install can easily take up more than 20 GB of disk space, Tiny11 takes up only about 8 GB, and there is an even smaller version called Tiny11 Core that takes up even less.

Downloading modified files from unknown websites can be risky, so NTDEV released Tiny11builder on GitHub. Instead of downloading a ready-made image from someone else, you download the official Windows 11 image from Microsoft and run the Tiny11builder script to clean it up yourself on your own computer.

The script uses Microsoft's own tools, such as DISM (Deployment Image Servicing and Management), along with an answer file to automate the setup. Because the script is written in plain PowerShell, you can open it and inspect every line of code to see exactly what it is changing or removing.

## What Gets Removed and What Stays?

The main goal of Tiny11 is to remove the extra programs and background tasks that slow down your computer, without breaking the system.

Here is what Tiny11 removes:

- **Extra apps:** It removes pre-installed apps like Clipchamp, Weather, News, Xbox, Solitaire, and Microsoft Teams.
- **Microsoft Edge and OneDrive:** These are removed by default, which frees up memory and stops unwanted prompts.
- **Forced online accounts:** It lets you set up a local offline account without needing to find a workaround.
- **Hardware blocks:** It bypasses the requirements for TPM 2.0, Secure Boot, and newer processors.

Here is what Tiny11 keeps:

- **Core Windows tools:** You still get standard utilities like Notepad, Paint, Calculator, File Explorer, and Windows Terminal.
- **Software and driver support:** You can still install your normal desktop software and hardware drivers.
- **Windows Security and Updates:** Microsoft Defender is still active, and you can still get regular security patches through Windows Update. Keep in mind that big annual updates might still put the removed programs back, so you might have to build the image again when those come out.

## Important Things to Keep in Mind

Before you install Tiny11, you should know about a few limitations:

- **You still need a license:** Tiny11 is not a way to get Windows for free. You must still use a valid Windows 10 or Windows 11 product key to activate it.
- **No official support:** Because you are using a modified version, Microsoft will not offer support if you run into problems.
- **The smallest builds have limits:** While the standard Tiny11builder script keeps Windows Update and the Microsoft Store, the smaller Tiny11 Core version removes them to save space, meaning you cannot update the system or easily add new features later.
- **Security changes:** Bypassing TPM 2.0 and Secure Boot makes old hardware work, but you lose some built-in security features like BitLocker encryption.

## How to Build Your Own Tiny11 ISO

To ensure your installation is safe, it is best to build the image yourself:

1. Download the official Windows 11 disk image (ISO) from Microsoft.
2. Download the Tiny11builder files from the official GitHub repository.
3. Double-click the Windows 11 ISO file to mount it, and write down its drive letter, like D or E.
4. Open PowerShell as an administrator, allow it to run scripts by typing `Set-ExecutionPolicy Bypass -Scope Process`, and run the `tiny11maker.ps1` script.
5. Type in the drive letter of your mounted ISO when the script asks for it, and choose the Windows edition you want to use. The script will then make a new `tiny11.iso` file that you can use with tools like Rufus or Ventoy to install on your computer.

## Final Thoughts

Tiny11 shows how lightweight Windows 11 can be when you remove the extra apps and background services. By using the open-source Tiny11builder script, you can see exactly what goes into your system while getting better performance. For anyone who needs to run virtual machines or use older hardware, it is a very helpful tool to have.
