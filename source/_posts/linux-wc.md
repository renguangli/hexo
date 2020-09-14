---
title: Linux 常用命令 wc 使用详解
photos: https://renguangli.gitee.io/images/linux-wc.png
date: 2017-10-03 17:22:54
tags:
---

Linux 下使用 `wc` 命令可以统计指定文件的字节数，字符数，行数，最大行的长度以及字数。

<!-- more -->

```bash
[root@localhost]# wc --help
Usage: wc [OPTION]... [FILE]...
  or:  wc [OPTION]... --files0-from=F
Print newline, word, and byte counts for each FILE, and a total line if
more than one FILE is specified.  With no FILE, or when FILE is -,
read standard input.  A word is a non-zero-length sequence of characters
delimited by white space.
The options below may be used to select which counts are printed, always in
the following order: newline, word, character, byte, maximum line length.
  -c, --bytes            print the byte counts
  -m, --chars            print the character counts
  -l, --lines            print the newline counts
      --files0-from=F    read input from the files specified by
                           NUL-terminated names in file F;
                           If F is - then read names from standard input
  -L, --max-line-length  print the length of the longest line
  -w, --words            print the word counts
      --help     display this help and exit
      --version  output version information and exit
```

## 统计 a.txt 文件的字节数

```bash
[root@localhost]# wc -c a.txt 
1574135528 a.txt
```
## 统计 a.txt 文件的字符数

```bash
[root@localhost]# wc -m a.txt 
1574135528 a.txt
```

## 统计 a.txt 文件的行数

```bash
[root@localhost]# wc -l a.txt 
131177569 a.txt
```

## 统计 a.txt 文件的字数

```bash
[root@localhost]# wc -w a.txt 
131177569 a.txt
```

参数可以组合使用，如统计 a.txt 文件的字节数，字符数，行数

```bash
[root@localhost]# wc -cml a.txt 
 131177569 1574135528 1574135528 a.txt
```

需要注意的输出列的顺序不受选项的顺序的影响，总是按行数、字数、字节数、文件名的顺序输出。



