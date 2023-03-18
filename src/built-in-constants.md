## Constants

Booleans true and false are defined here. And in a very smart and concise way:
```go
const (
    true = 0 == 0 // this is always true!
    false = 0 != 0 // this is always false!
)
```
Iota (view https://github.com/golang/go/wiki/Iota and https://golang.org/ref/spec#Iota)
represents successive untyped integer constants.
```go
const iota = 0 // Untyped int.
```
These two examples, describe how iota can be useful:
```go
const (
	c0 = iota  // c0 == 0
	c1 = iota  // c1 == 1
	c2 = iota  // c2 == 2
)

const (
	a = 1 << iota  // a == 1  (iota == 0)
	b = 1 << iota  // b == 2  (iota == 1)
	c = 3          // c == 3  (iota == 2, unused)
	d = 1 << iota  // d == 8  (iota == 3)
)
```
Note: The `<<` is a left shift operand, which shift lefts the operand by an integer
(in this case `iota` value). So, for instance `1 << 2` equals `1 * 2 * 2`.

`nil` is a widely used variable. It is defined as:
```go
var nil Type
```
It represents the zero value of a type, within these classes: slices, pointer,
map, func, interface, or channel.
