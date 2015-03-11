---
layout: post
title: "Resizing splits"
date: 2015-03-11 09:00:00 -0700
comments: true
author: Fellipe Brito
categories: [vim]
---

_by [@fellipebrito](https://github.com/fellipebrito)_

One of the things I most like in VIM is the easy way to split its environment. Using VIM splits + [ctrlP](https://github.com/kien/ctrlp.vim) + [tMux](http://en.wikipedia.org/wiki/Tmux) makes me a lot more productive.

Sometimes I want to read a manual, a helper, a README, or even just want to read my code in 'full screen'. This is how I do it:

Maximize the current sprint in the vertical:
```
ctrl + w _
```
> _ctrl + w means you have to press the key Ctrl and the key w at the same time and then release them to press the next key_

Maximize the current sprint in the horizontal:
```
ctrl + w |
```

Resize all splits to be the same (also called 'normalize')
```
ctrl + w =
```

Ask for help! VIM's helper is pretty useful, let's say you like the examples above and want to go further. I'd suggest that before you google it, you invest sometime → some time reading VIM's help.
```
:help :split
```
