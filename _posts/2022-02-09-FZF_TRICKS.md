---
# the default layout is 'page'
icon: fas fa-info-circle
title: Fzf master tricks
date: 2022-03-09 21:57
categories: [LINUX]
tags: [search, cli]
order: 4
---

# Introduction


Fzf can make you become a real 'Terminal Wizard' and increase your productivy. I will tell you some magic secrets of the best software written with golang(Docker sucks).

![roxy_s2](/assets/roxy-migurdia-mushoku-tensei.gif)

# Getting started

You can install fzf in many ways; package managers(pacman btw), source build, flatpack, etc. I will show the source build way, because it is more generic.

```bash
git clone --depth 1 https://github.com/junegunn/fzf.git ~/.fzf
~/.fzf/install
```

# Basic Features

Now, we have the most basic FZF feature that is... Search for file!!. If you type fzf inside a terminal emulator and then type your file name, you will see fzf in action.

![fzf](/assets/fzf.gif)

# Change directories

Here we will start to see the magic fzf can make. You can traverse directories in such many ways;

* Using a GUI program(The normie way)
* Using fzf(The epick Haskell wizard way)

For that one magic trick, you will just need to type that command, and then you can traverse between directories in an easy way.
```bash
cd $(find * -type d | fzf)

```
![fzf_change_directory](/assets/fzf_change_directory.gif)

* Find * -type d -- gives us a list of all directories listed on that directory.
* fzf - will use the input to filter through the list.
* cd - maybe that's will change the directory.








