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

# Git Navigation

The second trick(My favorite), is the ability to search through the git history with fzf.

![fzf_git_navigation](/assets/fzf_git_navigation.gif)

First, you'll need to install the git delta plugin and config it. Check [Delta](https://github.com/dandavison/delta). Now, it's time to write some git alias(just a standard alias, but for the git command), open the .gitconfig file, and let's go hack some things.

You can find a more chad version of that file on my [dotfiles](https://github.com/zedbydade/dotfiles) repo.

```bash
# /home/user/.gitignore
[core]
    pager = delta
[interactive]
    diffFilter = delta --color-only
[delta]
    navigate = true    # use n and N to move between diff sections
    light = false      # set to true if you're in a terminal w/ a light background color (e.g. the default macOS terminal)
[alias]
	browse = !"                                                                                                                                                                              \
		function gfzf() {                                                                                                                                                                 \
			local filter=\"$@\";                                                                                                                                                          \
			local -a show_filter;                                                                                                                                                         \
			while [[ \"$#\" -gt 0 ]]; do                                                                                                                                                  \
				if [[ \"$1\" == \"--\" ]] || [[ \"$1\" != '-'* ]] || [[ \"${#show_filter[@]}\" -gt 0 ]]; then                                                                             \
					show_filter+=( \"$1\" );                                                                                                                                              \
				fi;                                                                                                                                                                       \
				shift;                                                                                                                                                                    \
			done;                                                                                                                                                                         \
			if [[ \"${show_filter[0]}\" == '--' ]]; then                                                                                                                                  \
				show_filter=( \"${show_filter[@]:1}\" );                                                                                                                                  \
			fi;                                                                                                                                                                           \
			export LESS='-R';                                                                                                                                                             \
			export BAT_PAGER='less -S -R -M -i';                                                                                                                                          \
			git log                                                                                                                                                                       \
				--graph --color=always --abbrev=7 --glob=\"refs/heads/*\"                                                                                                                 \
				--format=format:\"%C(bold blue)%h%C(reset) %C(dim white)%an%C(reset)%C(bold yellow)%d%C(reset) %C(white)%s%C(reset) %C(bold green)(%ar)%C(reset)\" $filter |              \
					fzf --ansi --no-sort --layout=reverse --tiebreak=index                                                                                                                \
						--preview=\"f() {                                                                                                                                                 \
								set -- \\$(echo -- \\$@ | rg -o '\\b[a-f0-9]{7,}\\b');                                                                                                    \
								[ \\$# -eq 0 ] || git show --color=always --format=fuller \\$1 -- \"${show_filter[@]}\" | delta --line-numbers;                                           \
							};                                                                                                                                                            \
							f {}\"                                                                                                                                                        \
						--preview-window=right:60%;                                                                                                                                       \
		};                                                                                                                                                                                \
		gfzf                                                                                                                                                                              \
	"
```

That alias get the git log history, and then passes the content to ab fzf filter window. Cmon bro, it;s time to learn Bash

# Autocomplete

Now, you know that fzf can filter files. That's meaning we can filter through things like process and ssh host, because.. Unix is a file!!.
Here are some of the options you can use by default:

```bash
# /home/user/.gitignore
kill -9 <TAB>

ssh <TAB>

telnet <TAB>

unset <TAB>
eport <TAB>
unalias <TAB>
```

If you are unemployed, you can use something like [fzf-tab](https://github.com/Aloxaf/fzf-tab)

# Preview Files

Another cool thing to try out is the preview feature of the fzf. It allows you to preview files before you open them. To access that magic world, you need to pass --preview- to fzf. Let’s try:

![fzf_preview](/assets/fzf_preview.gif)

The command i type is:
```bash
# bat btw
fzf --preview 'bat --style=numbers --color=always --line-range :200 {}'
```

Of course, you can integrate that feature with things like Vim, Telescope and Harppon and have a nice search string/file workflow

# Conclusion

I hope that article gives you some insights about the tools behind the IDEs and Vim/Emacs distribution workflow we have today. I will give some useful alias in the footnotes. Goodbye.

```bash
# bash | zsh
alias sd="cd ~ && cd \$(find * -type d | fzf)"
alias gbr="git browse"

# fish
alias sd "cd ~ && cd (find * -type d | fzf)"
alias gbr "git browse"
```









