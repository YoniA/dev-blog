---
layout: post
title:  "Text manipulation power tools"
date:   2021-08-01 22:07:00 +02
categories: linux shell text-manipulation regex sed awk
---

## Introduction

Every developer working on Unix-based machines (Linux/MacOs), is probably using the builtin [grep](https://linux.die.net/man/1/grep){:target="blank"} command on a daily basis to extract lines matching a pattern.

While this tool is very useful, it has its limitations - it can only print matching lines.

But why stop here? What if we want to modify each matched line? What if we want to extrcat only a range of lines matching a pattern (and not all matching lines)? 
What if we want to apply several commands on a matching line?

All these and more can be done with another builtin tool called [sed](https://linux.die.net/man/1/sed){:target="blank"}.

If the lines in a file has some structure or pattern, like in `.csv` files for example, another tool which is called [awk](https://linux.die.net/man/1/awk){:target="blank"} is very useful.


## In praise of Unix tools

True, today most of these things can be done in advanced editors like *VScode*, but still, learning these tools has its benefits.

First, opening a file with an editor like *VScode* is very slow, and the editor itself consumes tons of memory. And if the file is huge, it will be even slower.
On the oher hand, these tools are working line by line, without having to read whole file in advance (thus they are called *stream editors*), so big files are
processed withut any problem.

Second, these builtin tools can be combined in shell scripts to do all sorts of things. With these tools we can edit files without even bothering to open them.
This can be used to automate the process of working with files and make it more efficient.

Third, the syntax of all these tools is similar, and also similar to the syntax of the *vim* editor. so all these tools can come together as a unified tool.

Lastly, in embedded systems sometimes a GUI editor is not available, and some people prefer to work from terminal by default. 

True, these tools has some learning curve, but the the potential of combining them all is worth it, for those working with text regularly and those who like inventing stuff.



## Order of tools

The basis for all text manipulation tools is a solid understanding of *Regular Expressions* (separate post). After mastering this, the first tool to learn is `grep`.  

Then we should learn `sed` and `awk`. Each of them has its use cases, and `awk` can do everything `sed` can do, but generally, the rule of thumb is this:

For simple edits like find-and-replace, delete lines, etc. we can use `sed`.

If the data is structured in some way, i.e, the lines follow some pattern, like in `.csv` files, we can use *awk* to do manipulate the pattern and extract some of it, or display it
in a tabular format.


## Conclusion

0. (regex)
1. grep
2. sed
3. awk
4. (perl)


In the next series of posts we will discuss the basics of these tools, see some use cases of using them.

Tags: `linux`, `shell`, `text-manipulation`, `regex`, `sed`, `awk`.
