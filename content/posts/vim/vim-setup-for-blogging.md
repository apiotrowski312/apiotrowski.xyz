---
draft: true
title: "Vim Setup for Blogging - Mastering Vim 2"
description: ""
date: 2020-10-05T10:31:11+02:00
slug: ""
tags: ["vim"]
categories: ["Vim Adventure"]
externalLink: ""
series: []
disable_comments: true
---
<!-- vim-markdown-toc GFM -->

* [Better version of vim](#better-version-of-vim)
    * [Basic config](#basic-config)
    * [Plugin manager](#plugin-manager)
* [Spellchecker](#spellchecker)
* [Thesaurus synonyms](#thesaurus-synonyms)
* [Markdown support](#markdown-support)
* [Conclusion](#conclusion)

<!-- vim-markdown-toc -->

Today post will cover topic: "Vim for markdown".
Firstly I will write a bit about neovim, then I will show you few plugins and shortcuts I found very useful for writing post in Markdown

### Better version of vim

Vim is really old school text editor that is 28 years old already. On one hand it is mature and battle tested, but on the other hand it lack of feature that in 2020 should be a standard. Because of that I will suggest to check out neovim project. Project started at 2014, by forking vim. It is the same old vim with a lot of modifications and improvements.

You should be able to install vim via command line as it is well known and should be in your distro repository:
```bash
sudo apt-get install neovim
```

#### Basic config
Configuration files for neovim can be find in *~/.config/nvim/* directory. For now I am using only the *init.vim* as it is the main source for vim configuration. Syntax is the same as in standard vim configuration. 

My bare configuration looked like that:
```bash
set number
set ruler

set expandtab
set tabstop=4
set softtabstop=4
set shiftwidth=4
```
With following configuration I don't have to worry about turning on the ruler, or having to manage tab length, which is nice. 

> I will use neovim/vim/nvim interchangeably, as everything here should work on both vim and neovim. However I tested all on neovim.

#### Plugin manager
As I found out pretty quickly, installing plugins for nVim are quite tedious. Because of that there are many plugins which are making installing plugin a lot easier and faster. I found [vim-plug](https://github.com/junegunn/vim-plug) plugin and stick with it. Installation is easy as writing in the terminal:
```bash
sh -c 'curl -fLo "${XDG_DATA_HOME:-$HOME/.local/share}"/nvim/site/autoload/plug.vim --create-dirs \
       https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim'
```

After installation you have to create directory `mkdir ~/.config/nvim/plugged` if it's not there already (as in my case, lack of this directory will cause error). When this is in place, you have to paste following into *init.vim* file:
```bash
call plug#begin()
Plug 'ron89/thesaurus_query.vim'
Plug 'mzlogin/vim-markdown-toc'
MORE PLUGINS HERE
call plug#end()
```
Following lines will inform *vim-plug* you want to install following modules. Save and call `:PlugInstall` and all plugins will be installed


### Spellchecker
First thing I wanted inside Vim was spellchecker so I will not do any stupid mistake like e.g. Mistkae. Every misspelling will glow on red, when we put cursor on that word we can use `z=` shortcut to open dictionary with plenty of propositions what word it could be.

![Mistake](/vim/Mistkae.png)
![MistakeDist](/vim/MistkaeDict.png)

Another useful shortcuts are:
- `]s` and `[s`. They will move your cursor to next/prev mistake. 
- `zw` `zuw` `zg` `zug` - add/remove word from wrong/good word list
If you want to learn more commands for this tool, just use `:help spell` to call manual. 

### Thesaurus synonyms

### Markdown support

### Conclusion

Previous part can be found [here](https://apiotrowski.xyz/posts/vim/start-of-the-great-adventure/). It was about how I come up with this idea. That's all

