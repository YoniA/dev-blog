---
layout: post
title:  "Tabular data display with awk"
date:   2021-01-06 20:40:47 +0200
categories: jekyll update
---

## Use case

We have a file with student grades in the following format:

```
# grades.txt
john 85 92 78 94 88
andrea 89 90 75 90 86
jasper 84 88 80 92 84
dan 91 75 88 88 82
sidhartha 92 90 77 68 95
```

using `awk` we can calculate the average score of each student, and add it to the grades list:

```bash
# avg.awk
BEGIN { N=5 }
{ print $1, $2, $3, $4, $5, $6, ($2 +$3 + $4 + $5 + $6) / N }
```

Running this little script against the grades file, we get:

```bash
$ awk -f avg.awk grades.txt 
john 85 92 78 94 88 87.4
andrea 89 90 75 90 86 86
jasper 84 88 80 92 84 85.6
dan 91 75 88 88 82 84.8
sidhartha 92 90 77 68 95 84.4
```

This is cool, but the output looks a little bit messy, with grades not aligned properly.

To fix this, we can pipe this output to the `column` command, as follows:

```bash
$ awk -f avg.awk grades.txt | column -s ' ' -t
john       85  92  78  94  88  87.4
andrea     89  90  75  90  86  86
jasper     84  88  80  92  84  85.6
dan        91  75  88  88  82  84.8
sidhartha  92  90  77  68  95  84.4  
```

And this looks much better in proper tabular format.


Tags: `Linux`, `shell`, `text manipulation`, `awk`, `column`


