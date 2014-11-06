---
layout: post
title: Convert standalone Tex plots to a PNG
tags: Tex
type: note
description: Create a PNG using the standalone document class and the convert option.
---

<div class="figure">
    <img src="/static/post/2014-11-05-tex-standalone-png/gpa.png" alt="PNG Plot Example">
    <p class="figure-caption">Example Tikz plot exported directly to PNG.</p>
</div>

I wanted to add a tikz pgf plot to the website and needed to export it as a PNG file. This can be accomplished with the `standalone` document class and the `convert` option.[^1]

1. In the tex document: `\documentclass[convert]{standalone}`
2. In terminal: `pdflatex -shell-escape gpa.tex`

The `-shell-escape` option is what makes this solution a little tricky.

[^1]: Originally answered by *Martin Scharrer* on [Stack Exchange](http://tex.stackexchange.com/a/11880) 
