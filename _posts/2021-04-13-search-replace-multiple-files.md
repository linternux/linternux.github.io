---
title: Search and replace multiple files
date: 2021-04-13 09:00:00
layout: post
categories: tips_and_tricks
tags: oneliner perl sed
---

At one point, you may be facing a situation where you would want to do an in place replacement of 
one string by another in multiple files. In this article, we will demonstrate a couple a way to 
do it, one solution using 'perl' and a second using 'sed'. 

<!--more-->

To demonstrate and visualize the replacement, we will use two text files, 'file1.txt' and 'file2.txt'.
Let's see the content of these two files before we begin our example. 
We will use the 'paste' command to show the file1.txt and file2.txt side by side.

```bash
$ paste file1.txt file2.txt
abc	    Batman
ABC	    Robin
Batman      robin
Robin       batcave
Joker       joker
Batcave     abc
```

<br>
## String replacement using 'perl' 
Let's begin with our first string replacement in multiple example using 'perl'. In the example 
below we are replacing every occurrence of 'Robin' with 'Boy Wonder' in all "*.txt" files in 
current path. You may want to do a backup of your files before running this command, because 
the string replacement will be done directly within each files (Or use our second example that 
do a backup of files changed). Please note, that this search and replace is case sensitive 
and that the string 'robin' was not replaced.
{: .text-justify}

```bash
$ paste file1.txt file2.txt
abc	    Batman
ABC	    Robin
Batman      robin
Robin       batcave
Joker       joker
Batcave     abc

$ perl -pi -e "s/Robin/Boy Wonder/g;" *.txt

$ paste file1.txt file2.txt
abc	    Batman
ABC	    Boy Wonder
Batman      robin
Boy Wonder  batcave
Joker       joker
Batcave     abc
```

<br>
## String replacement using 'perl' with preserving original
In this example, we replace the string "Robin" with "BatGirl" in all text files (*.txt), but 
this time the original file are preserve as a '.bak' files.

```bash
$ paste file1.txt file2.txt
abc	    Batman
ABC	    Robin
Batman      robin
Robin       batcave
Joker       joker
Batcave     abc

$ ls -l
total 8
-rw-rw-r-- 1 jacques sadmin 41 Apr 12 11:38 file1.txt
-rw-rw-r-- 1 jacques sadmin 43 Apr 12 11:38 file2.txt

$ perl -p -i'.bak' -e "s/Robin/BatGirl/g;" *.txt

$ paste file1.txt file2.txt
abc	Batman
ABC	BatGirl
Batman	robin
BatGirl	batcave
Joker	joker
Batcave	abc

$ ls -l
total 16
-rw-rw-r-- 1 jacques sadmin 38 Apr 12 10:30 file1.txt
-rw-rw-r-- 1 jacques sadmin 36 Apr 12 10:26 file1.txt.bak
-rw-rw-r-- 1 jacques sadmin 40 Apr 12 10:30 file2.txt
-rw-rw-r-- 1 jacques sadmin 38 Apr 12 10:26 file2.txt.bak
```

<br>
## String replacement in multiple file using 'sed'

In this example, we demonstrate hot to do an in place string replacement using the 'sed' command.
The example shown below replace the string 'ABC' with 'XYZ'. This replacement is case sensitive, 
notice the first line of file1.txt was not replace (See next example for case-insensitive 
replacement). 

```bash
$ paste file1.txt file2.txt
abc	Batman
ABC	Robin
Batman	robin
Robin	batcave
Joker	joker
Batcave	abc

$ sed -i 's/ABC/XYZ/g' file*

$ paste file1.txt file2.txt
abc	Batman
XYZ	Robin
Batman	robin
Robin	batcave
Joker	joker
Batcave	abc
```

If we take the same command and add a 'i' at the end of the 'sed' command, this make the search 
and replace case insensitive.

```bash
$ sed -i 's/ABC/XYZ/gi' file*

$ paste file1.txt file2.txt
XYZ	Batman
XYZ	Robin
Batman	robin
Robin	batcave
Joker	joker
Batcave	XYZ
```
