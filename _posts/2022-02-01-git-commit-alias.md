---
layout: post
title:  "Adding and alias for git commit"
date:   2022-02-01 20:40:47 +0200
categories: shorts configuration linux shell
---


## Adding an alias for git commit


Often I work on projects where the commit message is not important to me. For example, contributing content to the same database again and again. Having to come up with commit messages is tedious and repetative, and I am happy with a generit date-time message.

so we can do:
```bash
git add .
git commit -m "$(date)"
```

this will output something like this:

```
[gh-pages df7b7c2] Tue 01 Feb 2022 20:06:45 IST
```

so far so good. But even writing every time `git commit -m "$(date)"` is annoying.

To mitigate that, we can add an **alias** to our `~/.baschrc` or `~/zshrc` file:

```bash
alias commit="git commit -m \"$(date)\""
```

note that we had to escape the inner double-quoutes with a backslash.


To take effect, we have to close the terminal and reopen it again (only for the first time).

now every time we want to add changes and commit them we can just type:

```bash
git add .
commit
```  

yielding the same result: 

```
[gh-pages df7b7c2] Tue 01 Feb 2022 20:06:45 IST
```


