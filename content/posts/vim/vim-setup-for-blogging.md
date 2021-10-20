---
title: "Blogging with Vim. Starter pack."
description: "Some tips on how to make Vim a better tool for every blog writer. Vim is a really powerful tool that will help you write posts faster than anything. Thesaurus? Check. Spellchecking? Check."
date: 2020-10-07
tags:
- "vim"
---

Some tips on how to make Vim a better tool for every blog writer. Vim is a really powerful tool that will help you write posts faster than anything. Thesaurus? Check. Spellchecking? Check.
Todays post will cover a lot of different things, from migrating to neovim, to adding spellchecking and even some basic shortcuts to make writing posts easier. 

<!-- Vim-markdown-toc GFM -->

- [Better Vim version](#better-vim-version)
  - [Basic configuration](#basic-configuration)
  - [Plugin manager](#plugin-manager)
- [Spellchecker](#spellchecker)
- [Thesaurus synonyms](#thesaurus-synonyms)
- [Markdown TOC](#markdown-toc)
- [Undo and redo inside interactive mode](#undo-and-redo-inside-interactive-mode)

<!-- Vim-markdown-toc -->

### Better Vim version 

Vim is this old school text editor that nobody wants to use anymore? Wrong! Maybe Vim is 28 years old already, but it is maintained till today and has plenty of fans.
Vim on one hand is a mature and battle tested project, however in my opinion it lacks some features that should be a standard in 2020. It is up to you, if you want to use Vim, or try refreshed fork from 2014 called neoVim. It is the same old Vim with a lot of modifications and improvements.

You should be able to install noevim via command line as it is well known and should be already in your distro repository:
```bash
sudo apt-get install neovim
```

#### Basic configuration 
Configuration files for neovim can be found in *~/.config/nvim/* directory. File *init.vim* is the main source for Vim configuration. Syntax is the same as in standard Vim, so you can use the same file for both versions of Vim. 

```bash
set number
set ruler

set expandtab
set tabstop=4
set softtabstop=4
set shiftwidth=4
```
With following configuration I don't have to worry about turning on the ruler, or having to manage tab length every time I open the editor, which is nice. 

> I will use neovim/vim/nvim names interchangeably, as everything should work on both Vim and neovim. However I tested all on neovim.

#### Plugin manager
As I found out pretty quickly, installing plugins for nVim is quite tedious. Because of that there are many add-ons which are making installing plugin a lot easier and faster. I found [vim-plug](https://github.com/junegunn/vim-plug) and stuck with it. Installation is easy as writing in the terminal:
```bash
sh -c 'curl -fLo "${XDG_DATA_HOME:-$HOME/.local/share}"/nvim/site/autoload/plug.vim --create-dirs \
       https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim'
```

After installation you have to create a directory `mkdir ~/.config/nvim/plugged` if it's not there already (as in my case, lack of this directory will cause error). When it is in place, you have to paste following code into *init.vim* file:
```bash
call plug#begin()
Plug 'ron89/thesaurus_query.vim'
Plug 'mzlogin/vim-markdown-toc'
MORE PLUGINS HERE
call plug#end()
```
Following lines will inform *vim-plug* that you want to install two modules. Save and call `:PlugInstall` and add-ons will be installed.

### Spellchecker
First thing I wanted inside Vim was a spellchecker so I will not do any stupid mistakes like e.g. Mistkae. Every misspelling will glow, when we navigate our cursor over that word, we can use `z=` shortcut to open a dictionary with plenty of propositions on how we can fix the mistake.

![Mistake](/vim/Mistkae.png)
![MistakeDist](/vim/MistkaeDict.png)

Another useful shortcuts are:
- `]s` and `[s`. They will move your cursor to next/prev mistake. 
- `zw` `zuw` `zg` `zug` - add/remove word from wrong/good word list
If you want to learn more commands for this tool, just use `:help spell` to call manual. 

Spell utility doesn't require any plugins as it is build into nvim; Just paste those three lines into *init.vim*
```bash
autocmd FileType markdown setlocal spell
set spelllang=en
hi SpellBad ctermfg=white ctermbg=red
```
First two lines are required for the spellchecker to work, last line was added to customize how mistakes look inside the editor. By default errors in my neovim were highlighted with pink/purple color. However I find the red highlight more readable.

### Thesaurus synonyms

After installing the plugin (Already done in Plugin Manager step) the only thing you need to know is the following shortcut:
```bash
<Leader>cs
```

> **INFO**:
> Leader is a key you can configure by yourself. By default <Leader> is a `\`(frontslash) key

### Markdown TOC

Just like previous section. Plugin is already installed if you followed Plugin Manager section. To create Tree Of Content that is automagically updating on each save, call command `:GenTocGFM`. For other useful commands check plugin github repository ([Link](https://github.com/mzlogin/vim-markdown-toc))

### Undo and redo inside interactive mode

This one is pretty interesting. In Vim you can customize everything and test it on the fly. E.g this configuration can be pasted into _init.vim_ to have it permanently or typed inside Vim to test only for this session:
```bash
"Mappings
:inoremap <C-z> <ESC>ui
:inoremap <C-r> <ESC><C-r>i
```

With this configuration, `Ctrl+z` in interactive mode will work like in any other modern editor. There are plenty of other options like for example, you can print current time with <F3> - `:nnoremap <F3> :echo system("date")<CR>`
There are plenty other configurations you can create. It's all up to what works best for you.

> **INFO:** Do not forget about that **i** at the end of mapping command because, without it you will leave interactive mode.
