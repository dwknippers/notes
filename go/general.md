# Go
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

## Basic types
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

## Type conversions
The expression `T(v)` converts the value `v` to the type `T`.

```go
var i int = 42
var f float64 = float64(i)
var u uint = uint(f)
```

### Type inference
When declaring a variable without specifying an explicit type (either by using the `:=` sntaxo r `var = ` expression syntax), the variable's type is inferred from the value on the right side.

```
i := 42           // int
f := 3.142        // float64
g := 0.867 + 0.5i // complex128
```

## Constants
Constants can be character, string, boolean or numeric values.

Constants cannot be declared using the `:=` syntax.

```go
const Pi = 3.14
```

Numeric constants are high-precision **values**.
An untyped constant takes the type needed by its context.

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

Switch without a condition is the saem as `switch true`.

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

### Stacking defers
Deferred function calls are pushed onto a stack. When a function returns, its deferred calls are executed in last-in-first-out order.

To learn more about defer statements read this [blog post](https://blog.golang.org/defer-panic-and-recover).