---
title: "Starting With Go"
date: 2019-04-24T21:24:56-07:00
type: posts
draft: false
---

I recently purchased a copy of _The Go Programming Language_ by Donovan and Kerighan. The book's examples are extremely practical and often involve reimplementing some of the classic Unix tools that I love and use every day. I was suprised at how simple some of these commands were to implement.

Echo for example is just a simple one liner!
```
func main() {
	fmt.Println(strings.Join(os.Args[1:], " "))
}
```

To help really understands Go, I plan on reimplementing many of the other classic Unix commands, and documenting any interesting challenges I run into. You can follow my progress [here](https://github.com/stwrt/go-coreutils). I don't plan on implementing all the programs flag for flag, but they should function very similar to their C counterparts.
