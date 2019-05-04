---
title: "Du in Go"
date: 2019-05-03T23:41:46-07:00
type: posts
draft: false
---

While reading Chapter 8 in _The Go Programming Language_, they had a section on implementing `du` in Go. Since I am currently working my way through implementing the wide range of coreutils, I decided to tackle this as my next challenge.

`du` is a simple tool at heart. It will recursively traverse down the directories and give you their size.

```
bts@bts:~/code/go-coreutils$ du . --exclude=.git
4940    ./dl
976     ./yes
4       ./.vscode
2124    ./dd
2260    ./du
1388    ./echo
11712   .
```

This is an example of the source code directory for all the coreutils, with git excluded. (Git makes quite a mess of the `du` output).