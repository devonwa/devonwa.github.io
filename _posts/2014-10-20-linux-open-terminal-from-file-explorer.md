---
layout: post
title: Open a terminal from file explorer
tags: Linux
type: note
description: Add a right-click option for opening a terminal to the current directory in Nautilus.
---

*System: Ubuntu 14.04*

<div class="figure">
    <img src="/static/post/2014-10-20-linux-open-terminal-from-file-explorer/open-in-terminal-result.png" alt="open-in-terminal-result.png">
    <p class="figure-caption">New right-click menu option</p>
</div>

Add a right-click menu option to open a terminal to the directory you're looking at in Nautilus (Ubuntu's default file explorer).[^1]

1. Install the option: `sudo apt-get install nautilus-open-terminal`
2. Reset Nautilus: `nautilus -q`

Terminal, by default, opens to the user's home directory. I got tired of looking up files in the file explorer and then having to go back to terminal and find it all over again. This happened especially when I was new to Linux and wasn't comfortable using terminal, but I still find this tool useful today.

[^1]: Answered by *air-dex* on [Stack Exchange](http://askubuntu.com/a/207448 "nautilus-open-terminal") 
