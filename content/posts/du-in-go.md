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

My first implementation was a simple, recursive approach. I check each of the directory entries, and if it is a folder I enter it. If it is not a folder, I use a map to store the number of bytes and number of files for each folder.

```
func walkDir(dir string) {
  for _, entry := range dirents(dir) {
    if entry.IsDir() {
        subdir := filepath.Join(dir, entry.Name())
		walkDir(subdir)
	} else {
        if entry != nil {
		  fileSizes[dir] = dirInfo{fileSizes[dir].fileSizes + entry.Size(), fileSizes[dir].numFiles + 1}
	    }
	  }
    }
}
```

Once I search through all the folders I then print out the results.

```
var nfiles, nbytes int64
for dir, info := range fileSizes {
	printDiskUsageDir(dir, info.numFiles, info.fileSizes)

	nbytes += info.fileSizes
    nfiles += info.numFiles
}

printDiskUsageTotal(nfiles, nbytes)
```

I deviate slightly from the standard `du` by printing out the total number of bytes and files instead of just for each directory.

In the next post I will implement a parallel version of `du`. You can find the code [here](https://github.com/stwrt/go-coreutils/blob/master/du/du.go).