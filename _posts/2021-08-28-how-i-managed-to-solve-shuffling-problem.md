---
layout: post
title:  "How I managed to solve a text shuffling problem in shell script"
date:   2021-08-28 07:40:47 +0200
categories: jekyll update
---

## The problem

I have a shell script that displays multiple choice questions in terminal, in the following format:

```
What is the best disney movie?

a.	Aladin
b.	The Lion King
c.	Beauty and the Beast
d.	Alice in Wonderland
```

Each of the possible answers is fetched from the database and stored in a shell variable: `$ans1`, `$ans2`, `$ans3`, `$ans4`.
Every time the question is displayed, I want the answers to be shuffled.

So, next time this question is fetched, it could be displayed like so:

```
What is the best disney movie?

a.      The Lion King
b.	Beauty and the Beast      
c.	Alice in Wonderland	     
d.      Aladin
```

## Working towards the solution

Since it is a shell script, I needed some shell-ready tools. no external packages to install.

### The shuf command

the [`shuf`](https://linux.die.net/man/1/shuf) command can take a file, and display its lines in random order (shuffled). 
Using the `-e` flag, we can read input from command line, instead of a file.

So using `shuf -e $ans1 $ans2 $ans3 $ans4` gives a shuffled output, such as the folloing:

```
Beauty and the Beast
Aladin
The Lion King
Alice in Wonderland
```

This is cool, but still, it is missing the prepending answer letter. (the desired output is as in the first example above).

enter `awk`.

### Using awk

[awk](https://linux.die.net/man/1/awk) is a powerful tool for dealing with a structured data. Awk process a file line by line. each line can be regarded
as a *record* (like a record in a database that has fields), consisting of *fileds*. Each field is separated by a *field separator* (FS) which is by default a space.

So by default, each word in a file is a field. Each field can be accessed by the field variable: `$1`,...,`$n` where `n` is the number of words in the line.
the variable `$0` holds the whole line.

For example, consider the folloing line:
```
John Doe, 02-6525555, 27 Pine st., Jerusalem, Israel
```

using awk, we can print only the first name and phone:
```bash
awk '{ print $1 " " $2 " " $3}'

John Doe, 02-6525555,
```

Field separators can be changed, to be another character,  or even a regular expression. 
So in the last example, if we specify the field separator to be a comma `,`. the variables would change accordingly:
```
$1 - John Doe
$2 - 02-6525555
$3 - 27 Pine st.
$4 - Jerusalem
$5 - Israel
```

note that these fields (except the first one) are no preceded with a space!

Back to our problem - If we could somehow pipe the output of `shuf` to `awk`, we could have inserted the line letters dynamically, using print and field variables.

The problem is that `awk` works on each line separately, and the output of shuf is 4 different lines. so each line only has a single field, captured by `$1`.

So we need somehow to combine all lines into a single line, separated by a comma, and feed this to `awk`.

Here comes the `paste` command.

## Using paste

the [`paste`](https://linux.die.net/man/1/paste) command is used to merge lines of a file, using a delimiter.

So we can do the following:

```
paste -s -d,
```

the flag `-d,` tells `paste` to use a comma as a separator. the flag `-s` tells `paste` to process each file serially (instead of in parallel).

feeding this command the output from `shuf` we can get something like the following:

```bash
shuf -e $ans1 $ans2 $ans3 $ans4 | paste -d, -s

Alice in Wonderland,Aladin,The Lion King,Beauty And the Beast
```

now we are ready with a line to feed to `awk`.


## Putting it all together

Feeding a line like `Alice in Wonderland,Aladin,The Lion King,Beauty And the Beast` to `awk`, we can specify the comma as a field separator, and thus have
each movie name in its own field variable.

Then we can use `printf` inside awk, which behaves like `printf` in `C`, to insert our answer option index dynamically.

the complete script line looks like so:

```bash
shuf -e $ans1 $ans2 $ans3 $ans4 | paste -d, -s | awk -F ',' '{ printf "a.\t%s\nb.\t%s\nc.\t%s\nd.\t%s", $1, $2, $3, $4 }'
```

this yields output like this:
```
a.      The Lion King
b.      Beauty and the Beast
c.      Alice in Wonderland
d.      Aladin

```

Exactly as we wanted.


Tags: `linux`, `shell`, `awk`, `text manipulation`, `shuf`




