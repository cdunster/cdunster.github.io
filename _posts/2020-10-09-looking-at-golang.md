---
title: "Looking at GoLang"
date: 2020-10-09
summary: "Taking a look at the Go programming language."
tags: [development, go]
---

Time to have a look at the Go programming language, the one made by Google, not to be confused with [Go!] 😅.

In this article I'll go through my thoughts and first impressions and then move onto features that I think are noteworthy with examples so that I can review them in the future.

# First Impressions
I have to say that, in my opinion, Go seems to have some strange choices like having the type come after the name but omitting the colon `:`, this looks a bit weird to me but maybe I'll get used to it, however, in the same vain functions are declared as `func foo(i int) int` and I think this makes the return type of the function harder to read. I much prefer the Rust way `fn foo(i: int) -> int` especially when you start to get multiple returns and pointers i.e. `func foo(p *int, x int) (int, *int)` vs `fn foo(p: &int, x: int) -> (int, &int)`.

I do like that you can name return parameters so you can have something like this:
```go
func split(sum int) (x, y int) {
	x = sum * 4 / 9
	y = sum - x
	return
}
```
but if this is used for long and complicated functions it might cause confusion.

Coming from C/C++ and doing some Rust it seems strange to not end statements with a semicolon. I like that function naming conventions are enforced and you know that all capitalised functions are exported, enforced coding guidelines are always great so we don't have to waste time with style debates!

One last ~~thing~~ complaint; something that I find really confusing in Go is the naming of modules vs packages; they seem to be named the wrong way around. A module, by definition, is a single component that can usually be swapped out or replaced, whereas, a package is a collection of parts or things that are grouped together. In most programming languages that I know a module is often just a file, or two for things like header files, that can be packaged together to create a releasable package that can be shipped and used externally. In Go though, it seems to be the other way around so each file is a package and packages are grouped together to make modules. I'm not really sure for the reason of this but I guess it's not that big of a deal, it's just weird.

# Noteworthy

## Return parameters can be named
```go
func split(sum int) (x int, y int) {
	x = sum * 4 / 9
	y = sum - x
	return
}
```

## Variable types can be inferred
```go
var i = 42           // int
var f = 3.142        // float64
var g = 0.867 + 0.5i // complex128
```

## Variable declaration shorthand
Within a function a shorthand can be used to declare variables and assign it a value.
```go
i := 1
// Is shorthand for
var i int = 1
```

## Type conversion is explicit
You can convert/cast to a different type with the expression `T(v)` which converts `v` to type `T`.
```go
i := 42
f := float64(i)
u := uint(f)
```

## Constants
Constants are declared the same as variables but with the `const` keyword instead of `var`.
```go
const Pi = 3.14
```

## Only one loop
Go only has the `for` keyword for loops which is used for all loop constructs.
```go
// Normal for loop
for i := 0; i < 10; i++ {
	sum += i
}

// While loop
for sum < 1000 {
	sum += sum
}

// Infinite loop
for {
}

// foreach loop with index
for index, item := range items {
	// index and item are used here.
}

// foreach loop no index
for _, item := range items {
	// item used here.
}

// foreach loop only the index (shorthand of `for index, _ := range items`)
for index := range items {
	// index used here.
}
```

## If statements
In Go you can have an initialiser statement in an if like a for loop.
```go
if x := foo(); x < 10 {
	// Do something.
}
// `x` is out of scope here.
```
This could be very useful for checking multiple conditions:
```go
if val := foo(); val == 5 || val == 8 {
	// Do something if `val` is 5 or 8.
}
// `val` is out of scope here.
```
For example getting a state as an enum and then doing something if the value is some enum values but not others.

This also works for the else statements
```go
if x := foo(); x < 10 {
	// Do something with `x`.
} else {
	// Do something else with `x`.
}
// `x` is out of scope here.
```

## Switch statements
In Go a switch statement are more like if-else chains as they will only run one case statement, the first one to evaluate to true, and not all that follow so the `break` keyword is not required. Also, the case statements do not need to be constant.
```go
fmt.Println("When's Saturday?")
today := time.Now().Weekday()
switch time.Saturday {
case today + 0:
	fmt.Println("Today.")
case today + 1:
	fmt.Println("Tomorrow.")
case today + 2:
	fmt.Println("In two days.")
default:
	fmt.Println("Too far away.")
}
```
You can also have an empty switch statement which is the same as `switch true`.
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

## Defer statements
You can use the `defer` keyword to defer the execution of that statement until the surrounding function returns. However, the statement is evaluated at the point it is deferred.
```go
func SayHello() {
	text := "World!"
	defer fmt.Println(text)

	text = "Hello, "
	fmt.Print(text)
}
```
This prints `Hello, World!` as you'd expect.

Calls to `defer` are pushed onto the stack and so are executed in reverse order (LIFO).
```go
func SayHello() {
	text := "World!"
	defer fmt.Println(text)

	text = "Hello, "
	defer fmt.Print(text)
}
```
This also prints `Hello, World!` as you'd expect.

## Pointers
Pointers in Go work the same way as in C but there is no pointer arithmetic.
```go
j := 2701
p := &j        // point to j
*p = *p / 37   // divide j through the pointer
fmt.Println(j) // see the new value of j
```

## Structs
Structs in Go work the same as in C. They can also be defined as a named type or not, just like C.
```go
type Foo struct {
	A int
	B int
}

func main() {
	// Named struct
	foo := Foo{1, 2}
	fmt.Println(foo)

	// Anonymous struct
	bar := struct {
		C int
		D int
	}{
		3,
		4,
	}
	fmt.Println(bar)
}
```
Fields are accessed using a dot `struct.field` even when using a pointer to a struct.
```go
foo := Foo{1, 2}
fmt.Println(foo.A)

p := &foo
fmt.Println(p.B)
```
Fields can be initialised in order or with their name and a colon `A: 1`.
```go
foo := Foo{1, 2}
// Is the same as.
foo := Foo{
	B: 2,
	A: 1,
}
```

## Arrays
Arrays in Go are declared with the number of elements `[n]T` and they cannot be resized.
```go
var a [2]string // `a` is an array of 2 strings.
b := [4]int{1, 2, 3, 4} // `b` is an array of 4 ints.
```
The number of elements can be inferred from the assignment if you use `...` in place of `n`.
```go
array := [...]int{1, 2, 3, 4} // Array of 4 ints.
```

## Slices
Slices are references to arrays and are declared with `[]T`. Unlike arrays they are dynamically sized.
```go
slice := []int{}
```
You can create a slice from an array by specifying the inclusive start index and the exclusive end index `slice[start : end]`.
```go
array := [6]int{0, 1, 2, 3, 4, 5}
slice := array[2:4] // [2 3]
```
As expected; these indices/bounds can also be omitted to use the start or length of the underlining array.
```go
array := [6]int{0, 1, 2, 3, 4, 5}
slice1 := array[0:6]
slice2 := array[:6]
slice3 := array[0:]
slice4 := array[:]
// All the above slices are the same.
```
As slices are references to arrays then any changes to them also modifies the referenced array and vice versa.
```go
array := [6]int{0, 1, 2, 3, 4, 5}
slice1 := array[2:4] // [2 3]
slice2 := array[3:5] // [3 4]
slice1[1] = 42
// array is now [0 1 2 42 4 5]
// slice1 is [2 42]
// slice2 is [42 4]
```
Slice literals can be created the same as array literals and it just crates the underlying array literal and then creates a slice to reference it.
```go
array := [3]bool{true, false, true} // Array literal.
slice := []bool{true, false, true}  // Slice literal.
```
Slices have length and capacity that can be read with `len(slice)` and `cap(slice)`. The slice can be resized (changing its length) as long as it doesn't extend past its capacity.
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
	
	// Shift right one value
	s = s[1:3]
	printSlice(s)
}

func printSlice(s []int) {
	fmt.Printf("len=%d cap=%d %v\n", len(s), cap(s), s)
}

```
Prints
```
len=6 cap=6 [2 3 5 7 11 13]
len=0 cap=6 []
len=4 cap=6 [2 3 5 7]
len=2 cap=4 [5 7]
len=2 cap=3 [7 11]
```
Slices can be `nil`. A `nil` slice has a length and capacity of 0 and no underlying array.

A slice can also be created with the builtin `make()` function and you can pass in just the length or the capacity as well. This will create a zeroed array.
```go
func main() {
	a := make([]int, 5)
	printSlice("a", a)

	b := make([]int, 0, 5)
	printSlice("b", b)

	c := b[:2]
	printSlice("c", c)

	d := c[2:5]
	printSlice("d", d)
}

func printSlice(s string, x []int) {
	fmt.Printf("%s len=%d cap=%d %v\n",
		s, len(x), cap(x), x)
}
```
Prints
```
a len=5 cap=5 [0 0 0 0 0]
b len=0 cap=5 []
c len=2 cap=5 [0 0]
d len=3 cap=3 [0 0 0]
```
Slices can be used as dynamically sized arrays by using the builtin `append()` function this function takes a slice and some new values to append and returns a slice containing the original values with the new values appended. If the underlying array is too small to append the new values then a new array will be allocated with the original values and the appended ones and a slice of that new array will be returned.
```go
func main() {
	var s []int
	printSlice(s)

	// append() works on nil slices.
	s = append(s, 0)
	printSlice(s)

	// The slice grows as needed.
	s = append(s, 1)
	printSlice(s)

	// We can add more than one element at a time.
	s = append(s, 2, 3, 4)
	printSlice(s)
}

func printSlice(s []int) {
	fmt.Printf("len=%d cap=%d %v\n", len(s), cap(s), s)
}
```
Prints
```
len=0 cap=0 []
len=1 cap=1 [0]
len=2 cap=2 [0 1]
len=5 cap=6 [0 1 2 3 4]
```
The original slice passed into the `append()` function is not modified though so likely wants to be re-assigned the new slice `s = append(s, 1, 2)` which overwrites the old slice with the new. This behaviour could cause confusion as depending on the capacity of the original slice then the slices may or may not point to the same underlying array so changing the value of one slice may or may not change the value of the other.
```go
slice1 := []int{1, 2} 		 // len = 2, cap = 2
slice2 := append(slice1 , 3) // len = 3, cap = 3, new underlying array created.
slice2[0] = 5
// slice1 = [1 2], slice2 = [5 2 3]

slice3 := make([]int, 2, 3)  // len = 2, cap = 3
slice3[0] = 1
slice3[1] = 2
slice4 := append(slice3 , 3) // len = 3, cap = 3, same underlying array used.
slice4[0] = 5
// slice3 = [5 2], slice4 = [5 2 3]
```
In the first example the capacity increases so a new array is created so any modifications to one slice does not affect the other, whereas, in the second example the capacity does not increase so the same array is used and any changes to one slice will affect the other.

## Maps
Go supports maps, which are basically key-value-pairs, they have the type `map[keyT]valueT`. A zero value map is `nil`, a `nil` map has no keys and cannot have keys added. The `make()` function can be used to initialise an empty map that **can** have keys added to it.
```go
m1 := map[string]string		  // Creates an uninitialised map.
m1 = make(map[string]string)  // Initialise the map.
m2 := make(map[string]string) // Creates an empty but initialised map.
```
You can create map literals.
```go
m := map[string]string{
	"key1": "value1",
	"key2": "value2",
}
```
When creating a map of structs, the name can be omitted:
```go
m := map[string]Vertex{
	"Bell Labs": Vertex{
		40.68433, -74.39967,
	},
	"Google": Vertex{
		37.42202, -122.08408,
	},
}
// Can be shortened to
m = map[string]Vertex{
	"Bell Labs": {40.68433, -74.39967},
	"Google":    {37.42202, -122.08408},
}
```
Using a map is fairly standard:
* Insert or update an element: `m[key] = value`.
* Retrieve an element: `value = m[key]`.
* Delete an element: `delete(m, key)`.
* Test that a key is present: `value, is_present = m[key]` or `value, is_present := m[key]`. `value` is the zero value for its type if the key is not present.

## Function values
Functions can be used as values/parameters just like function pointers in C.

They can be declared as a variable:
```go
hypot := func(x, y float64) float64 {
	return math.Sqrt(x*x + y*y)
}
```
and they can be passed as a parameter:
```go
func compute(fn func(float64, float64) float64) float64 {
	return fn(3, 4)
}
```

## Closures
A closure is a function that is defined within a function and can access the variables of its parent function. This is actually possible in C but I've never come across a legitimate reason to use them.
```go
sum := 3
summer := func(x int) int {
	sum += x
	return sum
}

for i := 0; i < 10; i++ {
	fmt.Println(summer(i))
}
```

Closures can also be returned and passed around, just like functions.
```go
func getSummer(sum int) func(int) int {
	return func(x int) int {
		sum += x
		return sum
	}
}

func doSummer(summer func(int) int) {
	for i := 0; i < 10; i++ {
		fmt.Println(summer(i))
	}
}

func main() {
	summer := getSummer(3)
	doSummer(summer)
	otherSummer := getSummer(42)
	doSummer(otherSummer)
}
```
Therefore, you could create multiple instances of `summer` that all start with a different value.

## Methods
In Go, methods belong to a type instead of a class (similar to traits in Rust).
You define a method on a type with a receiver argument that goes in front of the method name.
```go
type Vertex struct {
	X, Y float64
}

func (v Vertex) Abs() float64 {
	return math.Sqrt(v.X*v.X + v.Y*v.Y)
}
```

Methods can only be declared on a type that is declared in the same package. This means that you cannot extend builtin types such as int.

You can have a method with a pointer receiver which allows you to modify the value of the receiver just like a function.
```go
func (v *Vertex) Scale(f float64) {
	v.X = v.X * f
	v.Y = v.Y * f
}
```
Methods with a pointer receiver are still just called the same way as with a normal value receiver `v.Scale(10)` which is just de-sugars to `(&v).Scale(10)` and visa versa for calling value receiver methods on pointers `p.Abs()` de-sugars to `(*p).Abs()`.

## Interfaces
Interfaces are available in Go but they are implemented implicitly meaning that any type that implements the declared methods can be used as that interface so there is no need for an explicit implements block.

Make sure you use the correct receiver type when wanting to use interfaces.
```go
type Abser interface {
	Abs() float64
}

func (v *Vertex) Abs() float64 {
	return math.Sqrt(v.X*v.X + v.Y*v.Y)
}
```
In the above code `*Vertex` implements the `Abser` interface but `Vertex` does not.

Under the hood an interface in Go is a tuple of the concrete value and its concrete type.
```go
type MyInterface interface {
	PrintText()
}

type MyType struct {
	Text string
}

func (myType *MyType) PrintText() {
	fmt.Println(myType.Text)
}

func main() {
	var myInterface MyInterface = &MyType{"Hello, World!"}
	myInterface.PrintText()
	// myInterface is a tuple (&{Hello, World!}, *main.MyType)
}
```
In the above code the interface de-sugars to `(&{Hello, World!}, *main.MyType)`.

If the concrete value stored in the interface tuple is `nil` then the interface value itself is non-nil and any methods called on that interface will just be called with a nil receiver, it is common to use `nil` guards in methods where this is possible.
```go
type I interface {
	M()
}

type T struct {
	S string
}

func (t *T) M() {
	// Guard against nil receiver.
	if t == nil {
		fmt.Println("<nil>")
		return
	}
	fmt.Println(t.S)
}

func main() {
	var i I   // Interface is nil.
	var t *T  // Value is nil.
	i = t	  // Interface has value (<nil>, *main.T)
	i.M()	  // Method is called with nil receiver.
}
```
However, calling a method on a `nil` interface value will result in a runtime error.

There is such a thing as an empty interface which is an interface that declares no methods `interface{}`. As all types implement at least zero methods then all types can be used as an empty interface value. The `fmt.Println()` function for example takes any number of arguments of type `interface{}`.

## Type assertions
Type assertions can be used to assert the concrete type of the interface (see [Interfaces](#interfaces) section) and even assign the concrete value of the interface to a variable, if the type assert fails then it will trigger a panic.
```go
var i interface{} = "hello"
s := i.(string)  // s is type string with value "hello"
fmt.Println(s)   // Prints "hello"
```
A type assertion can also return two values to test if the concrete type holds the asserted type.
```go
var i interface{} = 42
s := i.(string)		 // Panics!
s, ok := i.(string)  // s == "" ok == false
```

## Type switches
If you want to check against more than one type then you can use a type switch construct which is a special switch statement for checking types instead of values.
```go
switch v := i.(type) {
	case int:
		// `v` has type `int`.
	case string:
		// `v` has type `string`.
	default:
		// Type has no matching case; `v` has same type as `i`.
}
```

## Stringers
A commonly used interface is the `Stringer` interface defined by the `fmt` package. It is defined to have a single method `String() string` which returns a string representation of the value.
```go
type Person struct {
	Name string
	Age  int
}

func (p Person) String() string {
	return fmt.Sprintf("%v (%v years)", p.Name, p.Age)
}

func main() {
	a := Person{"Arthur Dent", 42}
	z := Person{"Zaphod Beeblebrox", 9001}
	fmt.Println(a, z)
}
```
Prints:
```
Arthur Dent (42 years) Zaphod Beeblebrox (9001 years)
```

## Errors
Another built-in interface is `error` which is defined to have a single method `Error() string`. The `error` type is used to return errors in Go programmes and the defined `Error() string` method is used to print these errors in a useful way.
```go
type MyError struct {
	When time.Time
	What string
}

func (e *MyError) Error() string {
	return fmt.Sprintf(`The error "%s" occurred at %v`, e.What, e.When)
}

func run() error {
	return &MyError{
		time.Now(),
		"Failed to run",
	}
}

func main() {
	if err := run(); err != nil {
		fmt.Println(err)
	}
}
```

A `nil` error value denotes a success and a non-nil values denotes an error occurred.

## Readers
The `io` package defines the `Reader` interface which has a single method `Read([]byte) (int, error)` used to read a stream of data. The standard library contains many types that implement the `Reader` interface for different types of streams such as files, network connections, compressors, ciphers, and others.
The defined `Read()` method populates the byte slice variable with data, up to its capacity, and returns a tuple with the number of bytes in the slice and an `error` value. The `io.EOF` error type is returned when the stream ends.

The following method will fill the passed slice with ascii `A` values.
```go
func (r MyReader) Read(b []byte) (int, error) {
	for i := range b {
		b[i] = 'A'
	}

	return len(b), nil
}
```

It is common practice to wrap a `Reader` with another `Reader`. For example you can have a type that implements `Reader` and contains a stream of encrypted data which implements `Reader`. When, you call the `Read()` method on this type it will in turn call `Read()` on its internal stream and then decrypt the data and return that.
```go
type Decrypter struct {
	encrypted io.Reader
}

func (d *Decrypter) Read(data []byte) (int, error)
{
	n, err := d.encrypted.Read(data)
	for i := range data {
		data[i] = decrypt(data[i])
	}
	return n, err
}

func main() {
	s := strings.NewReader("Lbh penpxrq gur pbqr!")
	d := Decrypter{s}
	io.Copy(os.Stdout, &d)
}
```

## Goroutines
The main appeal of Go is the way that it handles concurrency. Go has goroutines which are lightweight threads managed by the Go runtime. Creating a goroutine is as easy as adding the `go` keyword before a call to a function type. The function type doesn't need to be modified in any way and will simply happen concurrently. `go foo()` will execute the `foo()` function in a new goroutine. The evaluation of the parameters happens in the current goroutine so `go bar(x, y)` will evaluate `x` and `y` and then execute the `bar()` function in a new goroutine.

Goroutines run in the same address space and so shared memory needs to be managed correctly. One way to manage the memory is with the use of channels.

## Channels
Channels allow the sending and receiving of typed data with the channel operator `<-`. Data flows through the channel in the direction of the arrow.
'''go
ch <- x		// Send value of `x` to channel `ch`.
x := <- ch	// Receive value from channel `ch` and assign to `x`.
```

Channels must be created before they can be used. To create a channel use the built-in `make()` function.
```go
ch := make(chan int) // Creates a channel to send ints.
```

Both sending and receiving data via a channel is a blocking operation which allows goroutines to synchronise without using locks.
```go
func sum(s []int, c chan int) {
	sum := 0
	for _, v := range s {
		sum += v
	}
	c <- sum // Send sum to `c`. Block until someone can receive.
}

func main() {
	s := []int{7, 2, 8, -9, 4, 0}

	c := make(chan int)
	go sum(s[:len(s)/2], c)
	go sum(s[len(s)/2:], c)
	x, y := <-c, <-c // Receive from `c`. Block until two values are received.

	fmt.Println(x, y, x+y)
}
```

### Buffered channel
If you don't want the channel to block then you can add a buffer to the channel simply by adding the buffer length to the second argument when creating the channel `ch := make(chan int, 100)`. Sending to a buffered channel now only blocks when the buffer is full and receiving only blocks when the buffer is empty.

### Close channel
A channel can be closed by using the `close()` function and passing in the channel you want to close, this should only be done by the sender as the receiver does not know when there is no more data to receive. Receivers can test if the channel is closed by looking at the second return value of the receive expression `v, ok := <-ch` if `ok` is `false` then the channel is closed.

The `range` keyword used on a channel in a for loop will receive from the channel until it is closed.
```go
for i := range c {
	fmt.Println(i)
}
```

Sending on a closed channel will cause a panic which is why only the sender should close the channel.

It is not required to close a channel in most cases, it is just used as a way of telling the receiver that there are no more values which can terminate an `range` for loop.

### Select statements
A select statement allows you to wait on multiple channels by blocking until one of the cases can run. Sending and Receiving can by combined in a select statement as well as multiple channels. If all cases can run at the same time then one is chosen at random.
```go
func fibonacci(c, quit chan int) {
	x, y := 0, 1
	for {
		select {
		case c <- x:	// Send value of `x` to channel `c`.
			x, y = y, x+y
		case <-quit:	// Wait for value on channel `quit`.
			fmt.Println("quit")
			return
		}
	}
}
```

Select statements can also have a default case which will run if no other cases can, just like in switch statements.

## Mutexes
The Go standard library provides a mutex structure in the `sync` package with two methods `Lock()` and `Unlock()`. You should surround any mutual exclusive data with calls to these functions.
```go
c.mu.Lock()
// Lock so only one goroutine at a time can access the map c.v.
c.v[key]++
c.mu.Unlock()
```

It can be very useful to `defer` the call to `Unlock()` to ensure it is called (see [Defer statements](#defer-statements) section).
```go
c.mu.Lock()
// Lock so only one goroutine at a time can access the map c.v.
defer c.mu.Unlock() // Defer the unlock so it will run after the return statement.
return c.v[key]
```

## Wait groups
The sync package also provides a type `sync.WaitGroup` which can be used to manually count things to wait for such as goroutines. This is not automated in any way and instead is done with the concurrency safe methods `Add(n int)`, `Wait()`, and `Done()`. The `Add()` method takes a parameter of how much to increment the count by. As a `sync.WaitGroup` is concurrency safe then you can pass it between goroutines and perform operations on it.
```go
func foo(wg *sync.WaitGroup) {
	defer wg.Done() // Decrement WaitGroup count by 1 upon returning.
	fmt.Println("Hello, World!")
}

func main() {
	var wg sync.WaitGroup
	wg.Add(1)   // Increment WaitGroup count by 1.
	go foo(&wg) // Call the `foo()` function in its own goroutine but share the WaitGroup.
	wg.Wait()   // Wait until the WaitGroup count is 0.
}
```

## `sync` package
The `sync` package contains many other types to help with concurrency including a `RWMutex`, a concurrency safe `Map`, and others.

## `sync/atomic` package
There is also a `sync/atomic` package that provides low-level atomic memory primitives. The provided functions perform actions by using the actual atomic CPU instructions which means that they are concurrency safe. However, this package should not be used lightly!

It is better to communicate safely between goroutines with [channels](#channels) or the `sync` package instead of using the `sync/atomic` package to share memory and perform atomic actions on the same memory. It is also advised to not use the atomic functions with non-atomic functions.

# Conclusion
I actually spent more time on looking at Go than I expected and went into a lot more detail. After spending some time with it I have to say that I am impressed; Go is like a modern version of C and just like C it seems simple and easy to learn but hard to master. Go has taken C and added simple ways to handle concurrency and that is the main reason I would want to use it in the future.

I still prefer Rust as a language because of the amazing toolchains, package management, and environment, as well as the structure, speed, and safety of the language. Rust, however, is not a simple language like Go is and there are a lot of cases where you don't need the speed of Rust. For example writing a server backend in Rust would be safe and fast but when you have to read from a database or wait for a response from some other service as most backends need to then that speed is irrelevant and it would be much easier to write it in Go. It could also be that Go is faster in that example as the lightweight concurrency means that whilst you are waiting for a response you can have a separate goroutine that handles something else.

[Go!]: https://en.wikipedia.org/wiki/Go!_(programming_language)