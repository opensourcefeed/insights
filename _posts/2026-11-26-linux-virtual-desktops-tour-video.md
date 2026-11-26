---
layout: post
title: "Seedance 3.0 behind the opening of a Linux desktop tour"
categories: [linux, tutorial, desktop]
tags: [linux-desktop, virtual-desktops, workspaces, gnome, kde-plasma, screen-recording, obs-studio, video-tutorial]
description: "Learn how to film a Linux virtual desktops tour: plan the story, record GNOME, KDE or Xfce workspaces, add a short intro clip and edit for clarity."
---

**Most** Linux desktop tours lose viewers in the first ten seconds. They open on a wallpaper, wander around with the mouse, then jump into a settings panel. Virtual desktops are easier to teach when the video starts with a problem. What happens to your terminal and notes when you need a clean space for a video call? This guide shows how to plan, record and edit a short tour that answers that question.

## What virtual desktops do on Linux

Virtual desktops, also called workspaces, give you several separate screens on one monitor. Each workspace holds its own set of windows. You keep your terminal and notes on one, your browser on another, and your call on a third. Switching moves you between them without closing anything.

Most Linux desktop environments support this out of the box. GNOME uses dynamic workspaces that appear as you need them. KDE Plasma and Xfce let you set a fixed number in the settings. The default shortcuts differ by desktop and version, so check them on your own system before you film.

## Plan the story before you record

Decide what the viewer should understand by the end. A simple goal works best: the terminal and notes stay where they are while you work in another space. Write that goal as one spoken sentence, and build every shot around it.

A short outline keeps the video tight:

1. **The problem:** a short opening scene that shows three jobs competing for one desk.
2. **The setup:** the real desktop with a terminal and a notes window open.
3. **The switch:** you move to a new workspace and open the call app.
4. **The return:** you go back, and both original windows are still there.

## Record the real desktop

The screen recording is the part viewers can verify, so keep it accurate.

- **Use one installation.** Record everything on one machine and note the distribution, desktop environment and version. Put that detail in the video description.
- **Choose a recorder.** [OBS Studio](https://obsproject.com/) works on X11 and on Wayland through PipeWire. GNOME also has a built-in screen recorder, started with Ctrl+Shift+Alt+R.
- **Open the starting windows first.** Show the terminal and notes window before you switch, so the viewer knows what to look for later.
- **Show the shortcut on screen.** Many recorders and tools can display key presses. If yours cannot, say the shortcut aloud and add it to a caption.
- **Record at a modest window size.** A smaller capture leaves room for both the cursor and the result, and it crops better for social feeds.

Common starting points, which you should confirm on your own version:

- **GNOME:** Super+Page Up and Super+Page Down switch workspaces. Add Shift to move the active window.
- **KDE Plasma:** Ctrl+F1, Ctrl+F2 and so on jump to a numbered desktop.
- **Xfce:** Ctrl+Alt+Left and Ctrl+Alt+Right move between workspaces.

## Add a short opening clip

You do not need an illustration, but a few seconds of setup can make the problem clear before the interface appears. A desk with a notebook, a monitor and a headset says "too many things in one place" without a word.

For this kind of opening, an AI video tool such as [seedance 3.0](https://seedance3ai.ai/) can turn a still photo of your desk and a short reference clip into a few seconds of camera movement. A few habits help it work:

- Keep the camera move small, such as a slow drift across the desk that ends on the headset.
- Crop the still so the back wall and the front edge of the desk stay parallel.
- Check each result for extra objects or invented windows, and adjust the still if they appear.
- Keep the clip short, so the cut to the real screen arrives before viewers wonder how the objects relate.

If you would rather skip generated video, a simple title card from your editor does the same job. The real recording matters more than the opening.

## Edit for clarity

A few edit choices make a workspace demo easier to follow:

- **Pause on the switcher.** Let the cursor rest on the workspace indicator or the overview for a second before you change views. New users need a moment to see which control matters.
- **Cut on the new detail.** Place the cut so the workspace indicator appears just as the opening scene ends.
- **Record keyboard sounds separately.** If your microphone picks up a clean key press, place it on the timeline at the moment of the switch.
- **Keep background audio low.** Your spoken line should stay clear on laptop speakers.
- **Hold the final frame.** When you return to the first workspace, leave the mouse still so the viewer sees both original windows at once.

Put differences between distributions or desktop versions in a caption or the video description, where viewers can read them at their own pace.

## Plan wide and vertical versions together

If you want a wide version for YouTube and a vertical crop for Shorts or Reels, plan for both before you record. Capture the screen at a size that keeps the workspace switcher visible after a crop. In many cases, one recording covers both formats without new shots. If you plan to generate separate opening clips for each format, check [seedance 3.0 pricing](https://seedance3ai.ai/pricing) first and compare it with how much you can reuse from the footage you already have.

## Test the video on a new user

Before you publish, show the cut to someone who uses a computer but has never tried virtual workspaces. Afterward, ask two questions: where did the notes go, and how would you get back to them? If they can answer from what the recording showed, the tour works. If not, shorten the opening, slow down the switch, or add a caption.
