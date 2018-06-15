# Go Programming Language Notes

Golang's website has one of the best resource list found [here](https://golang.org/doc/). From this link, beginners can find most information they need. Particularly, I've used [A Tour of Go](https://tour.golang.org/welcome/1) to compile these notes.

Other resources I've used:
- https://www.golang-book.com/books/intro
- https://golang.org/doc/effective_go.html
- https://medium.com/@saginadir/why-i-love-golang-90085898b4f7

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

## Packages, variables, functions, and types
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
There are different ways to declare variables in Go. Here is some self documented code to demonstrate this:

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

### Functions
Go's function declaration syntax is different from C/C++ and other languages. Here's a good [blog post](https://blog.golang.org/gos-declaration-syntax) about why it was designed the way it is.

As before, here's some self documented code:
```go
// 0 input args
// 0 output args
func printFoo() {
    fmt.Println("foo")
}

// 2 input args with type
// 1 output arg
func addInts(x int, y int) int {
    return x + y
}

// 2 input args (type specified once since all input args have same type)
// 1 output arg
func add(x, y float32) float32 {
    return x + y
}

// 2 input args
// 2 outputs args (yes, in go, we can have multiple outputs)
func swap(x, y string) (string, string) {
    return y, x
}

// 1 input arg
// 2 output args (named)  ||||||  ||||||
func EightyTwenty(x int) (eighty, twenty float32) {
    eighty = 0.8 * float32(x)
    twenty = 0.2 * float32(x)
    return // "naked return" returns all named ouputs (in order)
}

func main() {
    printFoo()
    fmt.Println(addInts(5, 7))
    fmt.Println(add(5.0, 7.2))
    fmt.Println(swap("hello", "world"))
    fmt.Println(EightyTwenty(98))
}
```

### Types
Rather than typing stuff myself, I copied some content from the [here](https://tour.golang.org/basics/11):

Go's basic types are

```
bool

string

int  int8  int16  int32  int64
uint uint8 uint16 uint32 uint64 uintptr

byte // alias for uint8

rune // alias for int32
     // represents a Unicode code point

float32 float64

complex64 complex128
```

The example shows variables of several types, and also that variable declarations may be "factored" into blocks, as with import statements.

The int, uint, and uintptr types are usually 32 bits wide on 32-bit systems and 64 bits wide on 64-bit systems. When you need an integer value you should use int unless you have a specific reason to use a sized or unsigned integer type.

Also, a note about type conversions:

The expression T(v) converts the value v to the type T.

Some numeric conversions:

```
var i int = 42
var f float64 = float64(i)
var u uint = uint(f)
```

Or, put more simply:

```
i := 42
f := float64(i)
u := uint(f)
```

Unlike in C, in Go assignment between items of different type requires an explicit conversion. Try removing the float64 or uint conversions in the example and see what happens.
