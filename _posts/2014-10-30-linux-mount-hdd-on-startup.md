---
layout: post
title: Mounting a hard drive on startup
tags: Linux
type: note
description: In Linux, you need to mount a hard drive before you have access to its files. Here's how to mount a hard drive automatically on startup.
---

*Current system: Ubuntu 14.04*

I have a few hard drives in my desktop. I have one ssd for Windows, another ssd for Linux, and one big platter drive for data. This speeds up the operating systems, especially during boot, and any applications I choose to install on the ssd's. For my big files (pictures, movies), a standard platter hard drive is fine for speed because those files aren't called very often. I maximize the benefit of speed on my ssd's and the low-cost of storage on a platter drive.

In Windows, you'd be familiar with pathing to files on the operating system as *C:\my\oper\file* and the data drive as *D:\my\data\file*. These are consistent every time you boot. In Linux, hard drive partitions external to the operating system drive are not automatically mounted on boot.

In Linux, if I want shortcuts to point to my data drive, that's fine, but I need to make sure that I've mounted that drive first. This got annoying and I wanted to keep the Windows standard of mounting my data drive on startup, so I found an article in the [Ubuntu documentation][ubuntu auto-mount partitions] for how to do that.

Complete all the steps in the Per-User Mounts section. Make sure to mount by UUID and not by a "sb1"-type partition name because a UUID will stay the same every time, while a name may change. I had an issue on my first pass at this with mounting by a partition name because occasionally Linux would mount a System Recovery partition and throw off the partition name of my data drive.

[ubuntu auto-mount partitions]: https://help.ubuntu.com/community/AutomaticallyMountPartitions "HDD mount on boot"