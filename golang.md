# Go Programming Language Notes

Golang's website has one of the best resource list found [here](https://golang.org/doc/). From this link, beginners can find most information they need. Particularly, I've used [A Tour of Go](https://tour.golang.org/welcome/1) to compile these notes.

Other resources I've used:
- https://medium.com/@saginadir/why-i-love-golang-90085898b4f7
- https://www.golang-book.com/books/intro

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
Packages are like headers in C++, or modules in other languages, but they treat things like components, dependencies, and export/import functionality, as first class citizens.

Every program starts in the *main* package, and we can include (import) different packages as well:

```go
package main

import (
    "fmt"
    "math/rand"
)

func main() {
    fmt.Println("I'm generating a random number:", rand.Intn(15))
}
```

Note that **math/rand** is an *import* path, where the package is **rand** under the **math** directory. Note that any *.go* file under the **math** directory which has the line *package rand* will be included as part of our import directive above.

A more detailed read on creating your own packages can be found [here](https://medium.com/@saginadir/why-i-love-golang-90085898b4f7).

Note that functions or variables starting with capital letters are exported by design; everything else is private to the package. However, within the same directory (package), even private variables or functions can be accessed. This promotes reusability within a package but not outside.

Here's an example of what was discussed above:

**hello.go**
```go
package main

import (
    "fmt"
    "math/rand"
    "hello/utils"
)

func main() {
    fmt.Println("Generating a random number:", rand.Intn(15))
    utils.Foo()
    fmt.Println(utils.PublicVar)
}
```

**utils.go**
```go
package utils

import "fmt"

var privateVar int = 15
var PublicVar int  = 42

func Foo() {
    fmt.Println("printing foo...")
}
```

**Directory structure:**
```bash
In ~/go/src/hello
$ tree
.
├── hello
├── hello.go
└── utils
    └── utils.go

1 directory, 3 files
```

Now when we run *go build* and *go run hello.go*, we'll see:

```bash
$ go build && go run hello.go
Generating a random number: 11
printing foo...
42
```

### Variables
There are different ways to declare variables in Go. Here is some self documenting code to demonstrate this:

```go
package main

import (
    "fmt"
)

var c, python, java bool

const (
    Big float64 = 1 << 100
    Small uint32 = (1 << 100) >> 95
)

func main() {
    var i int
    fmt.Println(i, c, python, java) // default init to "zero" value based on type

    var j, k int = 1, 2 // var init value
    var l, m, n = 5.0, "This is a string", false // var type inferred from init val
    fmt.Println(j, k, l, m, n)

    o := 6 // short variable declaration (allowed only inside a function)
    fmt.Println(o)

    m = "Another string" // note: can only change value to string now, else we'll get a compilation error
    fmt.Println(m)

    const p int = 7 // const variable; type can be ommited; := can't be used
    fmt.Println(p)
    // p = 8 not allowed since p is const

    fmt.Println(Big)
    fmt.Println(Small)
}
```

This produces:
```bash
$ go build && go run hello.go
0 false false false
1 2 5 This is a string false
6
Another string
7
1.2676506002282294e+30
32
```

Note, by default, variables are initialized to "zero", where the zero value is:

- 0 for numeric types,
- false for the boolean type, and
- "" (the empty string) for strings.
