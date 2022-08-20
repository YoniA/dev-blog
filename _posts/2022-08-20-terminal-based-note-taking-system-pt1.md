---
layout: post
title:  "Custom terminal-based note-taking system"
date:   2022-08-20 10:04:00 +0200
categories: zettelkasten
---

I have come aross `zettelkasten` method and I wanted to create my own system.

## Requirements

First, I defined my baseline requirements:

* native in terminal
* completely text-based
* git and github integration
* no lock up in a specialized software (Obsidian etc)
* no specialized syntax (vimwiki etc)
* configurable and expandable
* efficient searching
* markdown as default text format
* link in documents
* easily searchable
* universal and available across platforms


## Implementation details 

Since I prefer to stay native in terminal, vim is the natural default text-editor for me. I wanted a system that is lightweight, fast and reliable.

In addition, git and github integration guarantee that my system is tracked, backed up in cloud and available anytime.

Markdown is supported in Gihtub and allow links, diagrams (memaid), math notation (mathjax) and more.
And it suppots my blogging needs.

this workfolw ensure that my documents are backed up, availabel anytime and across all devices.

In addition, it enables easy manipultion - search, change format - HTML...



### modified zshrc
I added some functions to my `.zshrc` file:

```bash
function zet() {
  vim $(date "+%Y%m%d%H%M").md
}
```

Now, whenever I type `zet` in my terminal, it opens a new vim buffer named datetime.md, ready for editing.

(e.g, `202207031052.md`)

This also let me see at glance which ideas came before other...

note: zet is a shortcut for `zettle` which means `a note` in German, and is part of the Zettlekasten jargon.


As for searching, I used `@` as hastag symbol (since `#` is already taken by markdown syntax)

Now I defined the following command (function): 

```bash
function zgrep() {
  cat $(grep -rl $1 .)
}
```

Now whenever I type `zgrep text` from the main directory, where text is the text to find, it prints the content of all the files containting the search word.



## Linking files

I use `gf` to jump from file to another filename under the cursor. by default, `gf` works only for files under current directories.

In order to make it work with any nested file under current directory, add the following to your `~/.vimrc`:

```vim
vim
set path+=**
```

This way, all nested directories are added to the search path.
