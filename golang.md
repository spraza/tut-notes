# Go Programming Language Notes

Golang's website has one of the best resource list found [here](https://golang.org/doc/). From this link, beginners can find most information they need. Particularly, I've used [A Tour of Go](https://tour.golang.org/welcome/1) to compile these notes.

We'll cover:

1. Packages, variables, and functions
2. Flow control (if/else, etc.)
3. Structs, slices, and maps
4. Methods, and interfaces
5. Concurrency

Before diving into specifics, let's start with a hello world program in Go. Remember that Go is a statically typed language, and requires an explicit compile step rather than an "online" interpreter.

```go
package main

import "fmt"

func main() {
    fmt.Printf("hello, world!\n")
}
```

Save this text in a file *hello.go*. Then, in bash, or your favorite shell, type in:
```bash
$ go build hello.go
$ ./hello
```

The first line compiles the code to produce a binary, which we then execute. The stdout should display the text "hello, world!" now.

## Packages, variables, and functions
### Packages
