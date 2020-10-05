---
draft: true
title: "Customizing neovim for markdown blog post - Mastering Vim 2"
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
* [Markdown TOC](#markdown-toc)
* [Undo and redo inside interactive mode](#undo-and-redo-inside-interactive-mode)
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

Spell utility doesn't require any plugin as it is build into nvim, just paste those three lines into *init.vim*
```bash
autocmd FileType markdown setlocal spell
set spelllang=en
hi SpellBad ctermfg=white ctermbg=red
```
First two lines are require to spellchecker work, last line was added to customize how mistakes look inside editor. By default errors in my neovim were highlighted with pink/purple color and I find the red highlight more readable for me.

### Thesaurus synonyms

After installing plugin (Already done in Plugin Manager step) the only think you need to know are shortcuts:
```bash
<Leader>cs
```

> **INFO**:
> Leader is the key you can configure by yourself. By default <Leader> is a `\`(frontslash) key

### Markdown TOC

Just like previous section. Plugin is already installed if you followed Plugin Manager section. To create Tree Of Content that is automarically updating on each save call command `:GenTocGFM`. For other useful command check plugin github repository ([Link](https://github.com/mzlogin/vim-markdown-toc))

### Undo and redo inside interactive mode

This one is pretty interesting. In vim you can customize everything and test it on the fly. E.g this configuration can be pasted into _init.vim_ or typed one by one inside vim to test it only for this one open vim window:
```bash
"Mappings
:inoremap <C-z> <ESC>ui
:inoremap <C-r> <ESC><C-r>i
```

With this configuration, `Ctrl+z` in interactive mode will work like in any other modern editor. There are plenty of other options like for example, you can print current time with <F3> - `:nnoremap <F3> :echo system("date")<CR>`
There are plenty other configuration you can create. It's all up to what works best for you.

> **INFO:** Do not forget about that **i** at the end of command mapping cause, without it you will leave interactive mode.

### Conclusion

Previous part can be found [here](https://apiotrowski.xyz/posts/vim/start-of-the-great-adventure/). It was about how I come up with this idea. That's all

