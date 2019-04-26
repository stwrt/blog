---
title: "Yes in Go"
date: 2019-04-25T19:49:43-07:00
type: posts
draft: false
---

Yes is the second coreutil program I decided to tackle. Yes works by endlessly filling your terminal with `y` characters. If you decide to pass it a string it will fill your terminal with those characters instead.

```bash
$ yes
y
y
y
y
y
```

If I want to print out no instead of yes I would write.
```bash
$ yes n
n
n
n
n
n
```

The basic implementation of yes is quite similar to echo, which I showed in my last blog post. The main difference is I add a for loop to endlessly print out the phrase until the program is killed.

```Go
func main() {
    phrase := "y"
    if len(os.Args) > 1 {
        phrase = strings.Join(os.Args[1:], " ")
    }
    for {
        fmt.Println(phrase)
    }
}
```

When benchmarking my program vs the reference GNU implementation I found a surprising difference in speeds.

```
$ go-yes | pv -r > /dev/null
[3.09 MiB/s]
$ gnu-yes | pv -r > /dev/null
[6.59 GiB/s]
```

The GNU implementation was outputting `y` several magnitudes faster than mine. The issue is occurring due to standard Go printing being non buffered. Modifying a few lines of code to add buffering gave an implementation even slightly faster than the default GNU yes.


```
$ buffered-go-yes | pv -r > /dev/null
[7.80 GiB/s]
$ gnu-yes | pv -r > /dev/null
[6.59 GiB/s]
```

In the new implementation I fill up a buffer of 16KB, which I then write out to the console. Instead of dumping each `y` individually it will dump thousands at a time.

```go
/* We need to create to a buffer to achieve fast output */
const bufferSize = 16384

func main() {
    phrase := "y"
    if len(os.Args) > 1 {
        phrase = strings.Join(os.Args[1:], " ")
    }
    phrase += "\n"

    /* Fill up the buffer */
    buf := []byte(phrase)
    for i := len(phrase) - 1; i + len(phrase) < bufferSize; i += len(phrase) {
        buf = append(buf, phrase...)
    }

    /* Perform the output */
    for {
        os.Stdout.Write(buf)
    }
}
```
Even a relatively simple command like `yes` can have some very neat tricks to drastically increase performance. You can find the implementation on Github [here](https://github.com/stwrt/go-coreutils/blob/master/yes/yes.go).
