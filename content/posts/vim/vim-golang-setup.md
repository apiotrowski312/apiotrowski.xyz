---
draft: false
title: "Basic setup for Go with Vim"
summary: "Is Vim good tool for developing golang applications? Sure, it may not be the best choice but at the same time it is really fun to work this way, you will feel a bit like in Matrix, which is nice."
date: 2020-11-23
slug: ""
tags: ["vim",  "Vim Adventure", "go"]
externalLink: ""
disable_comments: true
---

# Golang + Vim
Is Golang + Vim worth trying? Definitely, it is a really cool experience to develop application using this combination. However I tried using it for bigger projects and that wasn't that pleasing, I felt a bit lost and tired of all of that shortcuts. Anyway I think it may be a good idea to show you my setup for Go development.

## Plugins

I have used two plugins to get everything I needed:
```bash
Plug 'preservim/nerdtree'
Plug 'fatih/vim-go', { 'do': ':GoUpdateBinaries' }
``` 

### NERDTree

NERDTree is a cool tool for every developer it allows you to create/move/delete files and directories from inside a Vim, also it allows you to browse file project just like in any other editor. Whole experience was pretty good, I think some stuff is a bit unnatural for somebody who is coming from IDE word, buy you can get used to it.

Below you can see simple shortcuts for it to make it more approachable:

```bash
"NERDtree
map <C-b> :NERDTreeToggle<CR>
autocmd StdinReadPre * let s:std_in=1
autocmd VimEnter * if argc() == 0 && !exists("s:std_in") | NERDTree | endif
autocmd bufenter * if (winnr("$") == 1 && exists("b:NERDTree") && b:NERDTree.isTabTree()) | q | endif
set autochdir
let NERDTreeShowHidden=1
```

With those I was able to use NERDTree as I like. E.g. with `Ctrl+b` I can open the NERDTree anywhere. 

> `set autchdir` was really important for me as i usually open vim like this: `vim ~/path/to/file` and NERDTree wouldn't catch up and root of NERDTree was still in my current location which was annoying.

### faith/vim-go

This one is really powerful, I was able to do everything that I was used to in VSCode and even more. E.g I have always some troubles with importing my own packages, however here issue didn't appear. It maybe something else, as it use the same golang tools as VSCode but I thought it is good to mention, 

Some shortcuts and other configuration I used:
```bash
"vim-go
let g:go_fmt_command = "goimports"
let g:go_auto_type_info = 1
```

> By default fmt tool use different command to format output so I changed it, as goimports format code too and do some additional stuff.

## Summary

With those two plugins my developer experience was quite good, but as I wrote at start, only for smaller applications. I feel like I have to get used to Vim a bit more before jumping further with it. 

## Further reading

- Vim for blogging. This setup is great for anybody who like to write blog posts in markdown. I am a big fun of this setup. Read more [HERE](/posts/vim/vim-setup-for-blogging/) 
- NERDTree github, best source of NERDTree knowledge - [link](https://github.com/preservim/nerdtree)
- Why I changed to goimports - [link to github issue](https://github.com/fatih/vim-go/issues/207)

