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

## Methods
Go does not have classes. However, you can define methods on types.

A method is a function with a special **receiver** argument.

The receiver appears in its own argument list between the `func` keyword and the method name.

In this example, the `Abs` method has a receiver of type `Vertex` named `v`.

```go
func (v Vertex) Abs() float64 {
	return math.Sqrt(v.X*v.X + v.Y*v.Y)
}
```

Remember: a method is just a function with a receiver argument.

You can declare a method on non-struct types, too.

In this example we see a numeric type `MyFloat` with an `Abs` method.

```go
type MyFloat float64

func (f MyFloat) Abs() float64 {
	if f < 0 {
		return float64(-f)
	}
	return float64(f)
}
```

You can only declare a method with a receiver whose type is defined in the same package as the method.

### Pointer receivers
Methods with pointer receivers can modify the value to which the receiver points. Since methods often need to modify their receiver, pointer receivers are more common than value receivers.

With a value receiver, the `Scale` method operates on a copy of the original `Vertex` value. (This is the same behavior as for any other function argument.) The `Scale` method must have pointer receiver to change the `Vertex` value declared in the `main` function.

### Methods and pointer indirection
Functions with a pointer argument must take a pointer:


```go
var v Vertex
ScaleFunc(v, 5)  // Compile error!
ScaleFunc(&v, 5) // OK
```

while methods with pointer receivers take either a value or a pointer as the receiver when they are called.

```go
var v Vertex
v.Scale(5)  // OK
p := &v
p.Scale(10) // OK
```

For convenience, Go interprets the statement `v.Scale(5)` as `(&v).Scale(5)` since the `Scale` method has a pointer receiver.

The equivalent thing happens in the reverse direction.

Functions that take a value argument must take a value of that specific type:

```go
var v Vertex
fmt.Println(AbsFunc(v))		// OK
fmt.Println(AbsFunc(&v))	// Compile error!
```

while methods with value receivers take either a value or a pointer as the receiver when they are called:

```go
var v Vertex
fmt.Println(v.Abs())	// OK
p := &v
fmt.Println(p.Abs())	// OK
```

In this case, the method call `p.Abs()` is interpreted as `(*p).Abs()`.

### Choosing a value or pointer receiver
There are two reasons to use a pointer receiver.

- The method can modify the value that its receiver points to.
- It avoids copying the value on each method call. This can be more efficient if the receiver is a large struct, for example.

In general, all methods on a given type should either value or pointer receivers, but not a mixture of both. (We'll see why over the next few pages.)

## Interfaces
An **interface type** is defined as a set of method signatures.

A value of interface type can hold any value that implements those methods.

Interfaces are implemented implicitly. That means that it is not necessary to explicitly declare to intent to implement an interface.

```go
type I interface {
	M()
}

type T struct {
	S string
}

// This method means type T implements the interface I,
// but we don't need to explicitly declare that it does so.
func (t T) M() {
	fmt.Println(t.S)
}
```

Under the hood, interface values can be though of as a tuple of a value and a concrete type.

```
(value, type)
```

An interface holds a value of a specific underlying concrete type.

Calling a method on an interface value executes the method of the same name on its underlying type.

### Underlying value nil
If the concrete value inside the interface itself is nul, the method will be called with a nil receiver.

Note that an interface value that holds a nil concrete value is itself non-nil.

```go
type I interface {
	M()
}

type T struct {
	S string
}

func (t *T) M() {
	if t == nil {
		fmt.Println("<nil>")
		return
	}
	fmt.Println(t.S)
}

func main() {
	var i I

	var t *T
	i = t
	describe(i)
	i.M()

	i = &T{"hello"}
	describe(i)
	i.M()
}

func describe(i I) {
	fmt.Printf("(%v, %T)\n", i, i)
}
```

Output

```
(<nil>, *main.T)
<nil>
(&{hello}, *main.T)
hello
```

### Nil interface value
A nil interface value holds neither value nor concrete type.

Calling a method on a nil interface is a run-time error because there is no type inside the interface tuple to indicate which **concrete** method to call.

### Empty interface
The interface type that specifies zero methods is known as the **empty inferface**

```go
interface{}
```

An empty interface may hold values of any type. (Every type implements at least zero methods.)

Empty interfaces are used by code that handles values of unknown type. For example, `fmt.Print` takes any number of arguments of type `interface{}`.

### Type assertions
A **type assertion** provides access to an interface value's underlying concrete value.

```go
t := i.(T)
```

This statement asserts that the interface value `i` holds the concrete type `T` and assigns the underlying `T` value to the variable `t`.

If `i` does not hold a `T`, the statement will trigger a panic.

To **test** whether an interface value holds a specific type, a type assertion can return two values
- The underlying value
- A boolean value that reports whether the assertion succeeded.

```go
t, ok := i.(T)
```

If `i` holds a `T`, then `t` will be the underlying value and `ok` will be true.

If not, `ok` will be false and `t` will be the zero values of type `T`, and no panic occurs.

Note the similarity between this syntax and that of reading from a map.

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

A slice is formed by specifying two indices, a low and high bound, separated by a colon:

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
The `range` form of the `for` loop iterates over a slice or map.

When ranging over a slice, two values are returned for each iteration. The first is the index, and the second is a **copy** of the element at that index.

```go
for i, v:= range pow {
	fmt.Printf("2**%d\n", i, v);
}
```

You can skip the index value by assigning to `_`.

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

**Note:** if `elem` or `ok` have not yet been declared, you could use the short declaration operator `:=`.

### Functions
Functions are values too. They can be passed around just like other values.

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

The basic `for` loop has three components separated by semicolons:
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

## Concurrency

### Goroutines

A **goroutine** is a lightweight thread managed by the Go runtime.

```go
import (
	"fmt"
	"time"
)

func say(s string) {
	for i := 0; i < 5; i++ {
		time.Sleep(100 * time.Millisecond)
		fmt.Println(s)
	}
}

func main() {
	go say("world")
	say("hello")
}
```

```go
go f(x, y, z)
```

starts a new goroutine running

```go
f(x, y, z)
```

The evaluation of `f`, `x`, `y`, and `z` happens in the **current** goroutine and the execution of `f` happens in the **new** goroutine.

Goroutines run in the same address space, so access to share demmory must be synchronized. The `sync` package provides useful primitives, although you won't need much in Go as there are other primitives. (See the next slide.)

### Channels

Channels are a typed conduit through which you can send and receive values with the channel operator, `<-`.

```go
ch <- v		// Send v to channel ch.
v := <-ch	// Receive from ch, and assign value to v.
```

Like maps and slices, channels must be created before use:

```go
ch := make(chan int)
```

By default, sends and receives **block** unitil the other side is ready. This allows goroutines to synchronize without explicit locks or condition variables.

```go
func sum(s []int, c chan int) {
	sum := 0
	for _, v := range s {
		sum += v
	}
	c <- sum // send sum to c
}

func main() {
	s := []int{7, 2, 8, -9, 4, 0}

	c := make(chan int)
	go sum(s[:len(s)/2], c)
	go sum(s[len(s)/2:], c)
	x, y := <-c, <-c // receive from c

	fmt.Println(x, y, x+y)
}
```

The example code sums the numbers in a slice, distributing the work between two goroutines. Once both goroutines have completed their computation, it calculates the final result.

#### Buffered Channels

Channels can be **buffered**. Provide the buffer length as the second argument to `make` to initialize a buffered channel:

```go
ch := make(chan int, 100)
```

Sends to a buffered channel **block only when the buffer is full**.
Receives block when the buffer is empty.

```go
func main() {
	ch := make(chan int, 2)
	ch <- 1
	ch <- 2
	//ch <- 3
	fmt.Println(<-ch)
	fmt.Println(<-ch)
}
```

#### Range and Close

A sender can `close` a channel to indicate that no more values will be sent. Receiver can test whether a channel has been closed by assigning a second parameter to the receive expression: after

```go
v, ok := <-ch
```

`ok` is `false` if there are no more values to receive and the channel is closed.

```go
func fibonacci(n int, c chan int) {
	x, y := 0, 1
	for i := 0; i < n; i++ {
		c <- x
		x, y = y, x+y
	}
	close(c)
}

func main() {
	c := make(chan int, 10)
	go fibonacci(cap(c), c)
	for i := range c {
		fmt.Println(i)
	}
}
```

The loop `for i := range c` receives values from the channel repeatedly until it is closed.

>**Note**: Only the sender should close a channel, never the receiver. Sending on a closed channel will cause a panic.

>**Another note:**: Channels aren't like files; you don't usually need to close them. Closing is only necessary when the receiver must be told there are no more values coming, such as to terminate a `range` loop.