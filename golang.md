# Go Programming Language Notes

Golang's website has one of the best resource list found [here](https://golang.org/doc/). From this link, beginners can find most information they need. Particularly, I've used [A Tour of Go](https://tour.golang.org/welcome/1) to compile these notes.

Other resources I've used:
- https://www.golang-book.com/books/intro
- https://golang.org/doc/effective_go.html
- https://medium.com/@saginadir/why-i-love-golang-90085898b4f7

We'll cover:

1. Packages, variables, functions, and types
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

## Flow control (if, else, switch) and Defer
### Flow control (if, else, switch)

This one's pretty straight forward. Here's some self documenting code loops:

```go
package main

import (
    "fmt"
)

func main() {
    sum := 0
    for i := 0; i < 10; i++ {
        sum += i
    }
    fmt.Println(sum)
}
```

This will print **45**.

Note that the for-loop has a similar C structure: init statement, condition expression, and post statement. In Go, there are no while loops. However, we can skip init and post statements (and remove semi colons), which essentially reduces to a while with for loop syntax. In fact, "while true" can be represented by skipping all three things in a loop.

If conditions are also very similar:
```go
package main

import (
    "fmt"
    "math"
)

func sqrt(x float64) string {
    if x < 0 {
        return sqrt(-x) + "i"
    }
    return fmt.Sprint(math.Sqrt(x))
}

func main() {
    fmt.Println(sqrt(2), sqrt(-4))
}
```

Note: ```go if x := 0; x < 10 {}``` is also possible
Also: ```go if x < 10 { } else {}``` is also possible.

Switch is very similar to C as well, with the 'break' semantics. Here's is an excerpt from Go's tour document:

A switch statement is a shorter way to write a sequence of if - else statements. It runs the first case whose value is equal to the condition expression.

Go's switch is like the one in C, C++, Java, JavaScript, and PHP, except that Go only runs the selected case, not all the cases that follow. In effect, the break statement that is needed at the end of each case in those languages is provided automatically in Go. Another important difference is that Go's switch cases need not be constants, and the values involved need not be integers.

Here's the code that they showed:

```go
package main

import (
	"fmt"
	"runtime"
)

func main() {
	fmt.Print("Go runs on ")
	switch os := runtime.GOOS; os {
	case "darwin":
		fmt.Println("OS X.")
	case "linux":
		fmt.Println("Linux.")
	default:
		// freebsd, openbsd,
		// plan9, windows...
		fmt.Printf("%s.", os)
	}
}
```
Note that switch evaluation happens from top to bottom, and whenever
a condition is met, Go breaks out of the switch.

Note: A handy semantic is that of Go's switch without a condition that
can neatly represent long if/elseif/else chains.

Another thing not mentioned above is the use of binary logical operators like &&, ||, etc., which can be used in Go's conditions, just like C.

### Defer
From Go's tour document:

A defer statement defers the execution of a function until the surrounding function returns.

The deferred call's arguments are evaluated immediately, but the function call is not executed until the surrounding function returns.

```go
package main

import "fmt"

func main() {
	defer fmt.Println("world")

	fmt.Println("hello")
}
```

This prints "hello world".

Defer's are pushed onto the stack, so the following code:

```go
package main

import "fmt"

func main() {
	fmt.Println("counting")

	for i := 0; i < 10; i++ {
		defer fmt.Println(i)
	}

	fmt.Println("done")
}
```

outputs 9 to 0, with decrements of 1.

## Types, pointers, structs, slices, maps, closures, and more!

https://tour.golang.org/moretypes/1

### Pointers

Pointers in Go are behave similarly to those in C. However, at least in my head, Go's pointer semantics are a subset of C's pointer semantics. For instance, there is no pointer arithmetic in Go.

Here's some code to demonstrate basic pointer functionality:
```go
package main

import "fmt"

func main() {
	var i, j int = 21, 7 // can also be i, j := 21, 7

	var p *int = &i // can also be p := &i
	fmt.Println(*p)

	*p = *p * 2
	fmt.Println(*p) // p's value
	fmt.Println(p) // p's address

	q := &j
	fmt.Println(*q)
	*q = *q * 2
	fmt.Println(*q)
}
```

This produces:
```bash
$ go build && go run hello.go
21
42
0xc4200140d8
7
14
```

### Structs

A struct in Go is a type which encapsulates a collection of fields, each of which can have its own type.

Here is basic example:

```go
package main

import "fmt"

type Vertex struct {
	X int
	Y int
	Z int
}

func main() {
	fmt.Println(Vertex{2, 7, -6})
	v := Vertex{-1, -3, 9}
	v.X = 8
	fmt.Println(v)
	p := &v
    // Note: To access the field X of a struct when we have the struct pointer
    // p we could write (*p).X. However, that notation is cumbersome, so the
    // language permits us instead to write just p.X, without the explicit dereference.
	p.Y = p.Z * 2
	fmt.Println(v)
}
```

This produces:
```bash
$ go build && go run hello.go
{2 7 -6}
{8 -3 9}
{8 18 9}
```

There are different ways to initialize a struct, using struct literals. Here is some text from Tour of Go:

A struct literal denotes a newly allocated struct value by listing the values of its fields.

You can list just a subset of fields by using the Name: syntax. (And the order of named fields is irrelevant.)

The special prefix & returns a pointer to the struct value.

```go
package main

import "fmt"

type Vertex struct {
	X, Y int
}

var (
	v1 = Vertex{1, 2}  // has type Vertex
	v2 = Vertex{X: 1}  // Y:0 is implicit
	v3 = Vertex{}      // X:0 and Y:0
	p  = &Vertex{1, 2} // has type *Vertex
)

func main() {
	fmt.Println(v1, p, v2, v3)
}
```

This produces:
```bash
$ go build && go run hello.go
{1 2} &{1 2} {1 0} {0 0}
```

### Arrays
From Tour of Go:

The type [n]T is an array of n values of type T.

The expression:

var a [10]int

declares a variable a as an array of ten integers.

An array's length is part of its type, so arrays cannot be resized. This seems limiting, but don't worry; Go provides a convenient way of working with arrays.

Here's some code:

```go
package main

import "fmt"

func main() {
	var a[2] string
	a[0] = "hello"
	a[1] = "world"
	fmt.Println(a[0], a[1])
	fmt.Println(a)

	primes := [6]int{2, 3, 5, 7, 11, 13}
	fmt.Println(primes)
}
```

This produces:
```bash
$ go build && go run hello.go
hello world
[hello world]
[2 3 5 7 11 13]
```

Note: Generally, people write "var a[2] string" to make an array, "a", of size 2 and type string.
I prefer: "var a [2]string" i.e. the size goes with the type, which is how Go is designed as well i.e.
size and type go together.

### Slices
Slices are variable sized or dynamically sized portions of an array. Remember that arrays in Go
are fixed size, and once an array is created, we can't modify the size (or at least I don't of a
way to do that, yet).

Just like the type of an array is [SIZE]T, the type of a slice is []T.

A slice is formed by specifying starting and ending indices, like: "a[low : high]". Mathematically,
this is equivalent to a[low, high). Not sure why the Go designers didn't choose a[low, high]. Will
have to read more to know.

Slices are also like references to an array i.e. they don't store any array data itself, they just
point to the underlying array i.e. there's no deep copy involved when we create a slice of an array. This means
that if we change the slice's element, the array's underlying element also changes, because they are basically
the same piece of memory.

Here's some code:

```go
package main

import "fmt"

func main() {
	primes := [6]int{2, 3, 5, 7, 11, 13}
	var part []int = primes[0:4]
	fmt.Println(primes)
	fmt.Println(part)
	part = primes[1:5] // can change the size of a slice
	fmt.Println(part)
	anotherPart := primes[0:2]
	fmt.Println(anotherPart)
	anotherPart[0] = -10
	part[1] = -20
	fmt.Println(primes)
}
```

This produces:

```bash
$ go build && go run hello.go
[2 3 5 7 11 13]
[2 3 5 7]
[3 5 7 11]
[2 3]
[-10 3 -20 7 11 13]
```

Regarding slice defaults from Tour of Go:

When slicing, you may omit the high or low bounds to use their defaults instead.

The default is zero for the low bound and the length of the slice for the high bound.

For the array

var a [10]int

these slice expressions are equivalent:

a[0:10]
a[:10]
a[0:]
a[:]

Here's another good example from Tour of Go that demonstrates slice literals:

```go
package main

import "fmt"

func main() {
	q := []int{2, 3, 5, 7, 11, 13}
	fmt.Println(q)

	r := []bool{true, false, true, true, false, true}
	fmt.Println(r)

	s := []struct {
		i int
		b bool
	}{
		{2, true},
		{3, false},
		{5, true},
		{7, true},
		{11, false},
		{13, true},
	}
	fmt.Println(s)
}
```

This produces:
```bash
$ go build && go run hello.go
[2 3 5 7 11 13]
[true false true true false true]
[{2 true} {3 false} {5 true} {7 true} {11 false} {13 true}]
```

From Tour of Go again:

A slice has both a length and a capacity.

The length of a slice is the number of elements it contains.

The capacity of a slice is the number of elements in the underlying array, counting from the first element in the slice.

The length and capacity of a slice s can be obtained using the expressions len(s) and cap(s).

You can extend a slice's length by re-slicing it, provided it has sufficient capacity. Try changing one of the slice operations in the example program to extend it beyond its capacity and see what happens.

```go
package main

import "fmt"

func main() {
	s := []int{2, 3, 5, 7, 11, 13}
	printSlice(s)

	// Slice the slice to give it zero length.
	s = s[:0]
	printSlice(s)

	// Extend its length.
	s = s[:4]
	printSlice(s)

	// Drop its first two values.
	s = s[2:]
	printSlice(s)
}

func printSlice(s []int) {
	fmt.Printf("len=%d cap=%d %v\n", len(s), cap(s), s)
}
```

This produces:

```bash
$ go build && go run hello.go
len=6 cap=6 [2 3 5 7 11 13]
len=0 cap=6 []
len=4 cap=6 [2 3 5 7]
len=2 cap=4 [5 7]
```

Note that the zero value of a slice is "nil", which has 0 length, 0 capacity, and no associated array.

## Methods and interfaces

https://tour.golang.org/methods/1

## Concurrency (Goroutines, Channels, etc.)

https://tour.golang.org/concurrency/1

Go has very interesting semantics when it comes to concurrency. Here are some excellent talks about it:

- [Go Concurrency Patterns]( https://www.youtube.com/watch?v=f6kdp27TYZs&t=531s&index=2&list=PLmO4vvdsKXyIIyQ2O0gfsZk20FEusvJrB)
- [Concurrency is not parallelism]( https://www.youtube.com/watch?v=cN_DpYBzKso&t=470s&index=4&list=PLmO4vvdsKXyIIyQ2O0gfsZk20FEusvJrB)
- [Program your next server in Go]( https://www.youtube.com/watch?v=5bYO60-qYOI&t=0s&index=6&list=PLmO4vvdsKXyIIyQ2O0gfsZk20FEusvJrB)
- [Simulating a real-world system in Go]( https://www.youtube.com/watch?v=_YK0viplIl4&t=0s&index=5&list=PLmO4vvdsKXyIIyQ2O0gfsZk20FEusvJrB)
