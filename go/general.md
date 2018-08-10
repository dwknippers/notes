# Go
## Synopsis
Collection of general info about the Go programming language, mostly derived from the **Go Tour**.

## Packages
Programs start running in package `main`.

By convention, the package name is the same as the last element of the import path.

## Imports
"Factored" import statement:
```go
import (
	"fmt"
	"math"
)
```

## Exported names
A name is exported if it begins with a capital letter.

## Functions
A function can return any number of results.
These results can also **be named**.

```go
func split(sum int) (x, y int) {
	x = sum * 4 / 9
	y = sum - x
	return
}

func main() {
	fmt.Println(split(17))
}
```

A `return` statement without arguments returns the named return values.
This is known as a **naked** return.

## Variables
The `var` statement declares a list of variables; as in function argument lists, the type is last.

```go
var c, python, java bool = true, false, "no!"
```

The `var` statements can be at package or function level.
If an initializer is present, the type can be omitted; the variable will take the type of the initializer.

### Short assignment statement
Inside a function, the `:=` short assignment statement can be used in place of a `var` declaration with implicit type.

>Outside a function, every statement begins with a keyword (`var`,`func`, and so on) and so the `:=` construct is not available.

### Constants
Constants can be character, string, boolean or numeric values.

Constants cannot be declared using the `:=` syntax.

```go
const Pi = 3.14
```

Numeric constants are high-precision **values**.
An untyped constant takes the type needed by its context.

## Types
```go
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

Variable declarations may be "factored" into blocks, as with import statements.

```go
var (
	ToBe   bool       = false
	MaxInt uint64     = 1<<64 - 1
	z      complex128 = cmplx.Sqrt(-5 + 12i)
)
```

### Zero values
`0` for numeric types  
`false` for boolean type, and  
`""` for strings.

### Type conversions
The expression `T(v)` converts the value `v` to the type `T`.

```go
var i int = 42
var f float64 = float64(i)
var u uint = uint(f)
```

### Type inference
When declaring a variable without specifying an explicit type (either by using the `:=` syntax or `var = ` expression syntax), the variable's type is inferred from the value on the right side.

```go
i := 42           // int
f := 3.142        // float64
g := 0.867 + 0.5i // complex128
```

### Structs
A `struct` is a collection of fields.

```go
type Vertex struct {
	X int
	Y int
}

func main() {
	fmt.Println(Vertex{1, 2});
	v.X = 4
	fmt.Println(v.X)
}
```

Struct fields can be accessed through a struct pointer.  
To access the field `X` of a struct when we have the struct pointer `p` we could write `(*p).X`.  

However, that notation is cumbersome, so the language permits us instead to write just `p.X`, without the explicit dereference.

```go
v:= Vertex{1, 2}
p := &v
p.X = 1e9
```

#### Struct literals
A struct literal denotes a newly allocated struct value by listing the values of its fields.

You can list a subset of fields by using the `Name:` syntax. (And the order of named fields is irrelevant.)

```go
var (
	v1	= Vertex{1, 2}	// has type Vertex
	v2	= Vertex{X: 1}	// Y:0 is implicit
	v3	= Vertex{}	// X:0 and Y:0
	p	= &Vertex{1, 2}	// has type *Vertex
)
```

### Arrays
The type `[n]T` is an array of `n` values of type `T`.

```go
var a [2]string
a[0] = "Hello"
a[1] = "World"
fmt.Println(a[0], a[1]); //output: Hello World
fmt.Println(a) // output: [Hello World]
```

An array's length is part of its type, so arrays cannot be resized. This seems limiting, but don't worry; Go provides a convenient way of working with arrays.

#### Slices
An array has a fixed size. A slice, on the other hand, is a **dynamically-sized, flexible view** into the elements of an array. In practice, slices are much more common than arrays.

The type `[]T` is a slice with elements of type `T`.

A slice is formed by specifying two indices, a low and high bound, seperated by a colon:

```go
a[low : high]
```

This selects a half-open range which includes the first element, but excludes the last one.

The following expression creates a slice which includes elements 1 through 3 of `a`.

```go
a[1:4]
```

```go
primes := [6]int{2, 3, 5, 7, 11, 13}

var s []int primes[1:4]
fmt.Println(s) // output: [3 5 7]
```

Slices can contain any type, including other slices.

```go
// Create a tic-tac-toe board.
board := [][]string{
	[]string{"_", "_", "_"},
	[]string{"_", "_", "_"},
	[]string{"_", "_", "_"},
}

// The players take turns.
board[0][0] = "X"
board[2][2] = "O"
board[1][2] = "X"
board[1][0] = "O"
board[0][2] = "X"

for i := 0; i < len(board); i++ {
	fmt.Printf("%s\n", strings.Join(board[i], " "))
}
```

##### Slices are like references to arrays
A slice does not store any data, it just describes a section of an underlying array.

Changing the elements of a slice modifies the corresponding elements of its underlying array.

Other slices that share the same underlying array will see those changes.

##### Slice literals
A slice literal is like an array literal without the length.

This is an array literal:

```go
[3]bool{true, true, true}
```

And this **creates the same array as above**, then builds a slice that references to it.

```go
[]bool{true, true, false}
```

The default is zero for the low bound and the length of the slice for the high bound.

For the array

```go
var a [10]int
```

these slices expressions are equivalent:

```go
a[0:10]
a[:10]
a[0:]
a[:]
```

A slice has both a **length** and a **capacity**.

The length of a slice is the number of elements it contains.

The capacity of a slice is the number of elements in the underlying array, counting from the first element in the slice.

The length and capacity of a slice `s` can be obtained using the expressions `len(s)` and `cap(s)`.

You can extend a slice's length by re-slicing it, provided it has sufficient capacity.

```go
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

output

```
len=6 cap=6 [2 3 5 7 11 13]
len=0 cap=6 []
len=4 cap=6 [2 3 5 7]
len=2 cap=4 [5 7]
```

##### Creating a slice with make

Slices can be created with the built-in `make` function; this is how you create dynamically-sized arrays.

The `make` functions allocates a zeroed array and returns a slice that refers to that array.

```go
a := make([]int, 5) // len(a)=5
```

To specify a capacity, pass a third argument to make

```go
b := make([]int, 0, 5) // len(b)=0, cap(b)=5

b = b[:cap(b)]	// len(b)=5, cap(b)=5
b = b[1:]	// len(b)=4, cap(b)=4
```

##### Appending to a slice
Go provides a built-in `append` function.

```go
func append(s []T, vs ...T]) []T
```

The first parameter `s` of `append` is a slice of type `T`, and the rest are `T` values to append to the slice.

The resulting value of `append` is a slice containing all the elements of the original slice, plus the provided values.

If the backing array of `s` is too small to fit all the given values a bigger array will be allocated. The returned slice will point to the newly allocated array.

[Article: Slices: usage and internals](https://blog.golang.org/go-slices-usage-and-internals)

##### Iterating over slices
The `range` form of the `for` loop iteraties over a slice or map.

When ranging over a slice, two values are returned for each iteration. The firest is the index, and the second is a **copy** of the element at that index.

```go
for i, v:= range pow {
	fmt.Printf("2**%d\n", i, v);
}
```

You can skip the indexo r value by assigning to `_`.

If you only want the index, drop the `, value` entirely.

```go
pow := make([]int, 10)
for i := range pow {
	pow[i] = 1 << uint(i) // == 2**i
}
for _, value := range pow {
	fmt.Printf("%d\n", value)
}
```

### Maps
A map maps keys to values.

The `make` function returns a map of a given type, initialized and ready for use.

```go
package main

import "fmt"

type Vertex struct {
	Lat, Long float64
}

var m map[string]Vertex

func main() {
	m = make(map[string]Vertex)
	m["Bell Labs"] = Vertex{
		40.68433, -74.39967,
	}
	fmt.Println(m["Bell Labs"])
}
```

#### Map literals
Map literals are like struct literals, but the keys are required.

```go
var m = map[string]Vertex{
	"Bell Labs": Vertex{
		40.68433, -74.39967,
	},
	"Google": Vertex{
		37.42202, -122.08408,
	},
}
```

If the top-level type is just a type name, you can omit it from the elements of the literal.

```go
var m = map[string]Vertex{
	"Bell Labs": {40.68433, -74.39967},
	"Google":    {37.42202, -122.08408},
}
```

#### Mutating maps
Insert or update an element in map `m`:
```go
m[key] = elem
```

Retrieve an element:
```go
elem = m[key]
```

Delete an element:
```go
delete(m, key)
```

Test that a key is present with a two-value assignment:
```go
elem, ok = m[key]
```

if `key` is in `m`, `ok`, is `true`.  
If not, `ok` is `false`.

**Note:** if `elem` or `ok` have not yet been declared, you couild use the short declaration operator `:=`.

### Functions
FUnctions are values too. They can be passed around just like other values.

Function values may be used as function arguments and return values.

```go
func compute(fn func(float64, float64) float64) float64 {
	return fn(3, 4)
}

func main() {
	hypot := func(x, y float64) float64 {
		return math.Sqrt(x*x + y*y)
	}
	fmt.Println(hypot(5, 12))

	fmt.Println(compute(hypot))
	fmt.Println(compute(math.Pow))
}
```

#### Closures
Go functions may be closures. A closure is a function value that references variable from outside its body.
The function may access and assign to the referenced variables; in this sense the function is "bound" to the variables.

For example, the `adder` function returns a closure. Each closure is bound to its own `sum` variable.

```go
func adder() func(int) int {
	sum := 0
	return func(x int) int {
		sum += x
		return sum
	}
}

func main() {
	pos, neg := adder(), adder()
	for i := 0; i < 10; i++ {
		fmt.Println(
			pos(i),
			neg(-2*i),
		)
	}
}
```


## Constructs
### For
Go has only one looping construct, the `for` loop.

```go
for i := 0; i < 10; i++ {
	sum += i
}
```

The basic `for` loop has three components seperated by semicolons:
* The **init statement**: executed before the first iteration.
* The **condition expression**: evaluated before every iteration.
* The **post statement**: executed at the end of every iteration.

The loop will stop iterating once the boolean condition evaluates to `false`.

The **init** and **post** statements are optional.

#### Forms
`while` is `for`.
```go
for sum < 1000 {
}
```

Forever.
```go
for {
}
```

### If
Like `for`, the `if` statement can start with a short statement to execute before the condition.
```go
if v := math.Pow(x, n); v < lim {
	return v
} else {
	return v - (v - lim)
}
```

Variables declared by the statement are only in scope until the end of the `if`.

### Switch
Go only runs the selected case, not all the cases that follow. In effect, the `break` statement that is needed at the end of each case in those languages is provided automatically in Go.

Another import difference is that Go's switch cases need not be constants, and the values involved need not be integers.

Switch cases evaluate cases from top to bottom, stopping when a case succeeds.

Switch without a condition is the same as `switch true`.

This construct can be a clean way to write long if-then-else chains.

```go
t := time.Now()
switch {
case t.Hour() < 12:
	fmt.Println("Good morning!")
case t.Hour() < 17:
	fmt.Println("Good afternoon.")
default:
	fmt.Println("Good evening.")
}
```

### Defer
A defer statement defers the execution of a function until the surrounding function returns.

The deferred call's arguments are evaluated immediately, but the function call is not executed until the surrounding function returns.

```go
defer fmt.Println("world")

fmt.Println("hello")
```
```
hello
world
```

#### Stacking defers
Deferred function calls are pushed onto a stack. When a function returns, its deferred calls are executed in last-in-first-out order.

To learn more about defer statements read this [blog post](https://blog.golang.org/defer-panic-and-recover).

### Pointers
A pointer holds the memory address of a value.

The type `*T` is a pointer to the `T` value. Its zero value is `nil`.

The `&` operator generates a pointer to its operand.

```go
i := 42
p = &i // points to i
```

The `*` operator denotes the pointer's underlying value.

```go
fmt.Println(*p)	// read i through the pointer p
*p = 21		// set i through the pointer p
```

This is known as **dereferencing** or **indirecting**.

