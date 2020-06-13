## recover

Recover is a built-in function allows a program to manage behavior of a panicking
goroutine. It is useful only **inside a deferred function**. It stops the panicking
sequence by restoring normal execution and retrieves the error value passed to the
call of panic. If recover is called outside the deferred function it will not stop
a panicking sequence. In this case, or when the goroutine is not panicking, or if
the argument supplied to panic was nil, recover returns nil. Thus the return value
from recover reports whether the goroutine is panicking.

An example (https://blog.golang.org/defer-panic-and-recover) of the mechanisms of
panic and defer:

```go
package main

import "fmt"

func main() {
    f()
    fmt.Println("Returned normally from f.")
}

func f() {
    defer func() {
        if r := recover(); r != nil {
            fmt.Println("Recovered in f", r)
        }
    }()
    fmt.Println("Calling g.")
    g(0)
    fmt.Println("Returned normally from g.")
}

func g(i int) {
    if i > 3 {
        fmt.Println("Panicking!")
        panic(fmt.Sprintf("%v", i))
    }
    defer fmt.Println("Defer in g", i)
    fmt.Println("Printing in g", i)
    g(i + 1)
}

// outputs
// Calling g.
// Printing in g 0
// Printing in g 1
// Printing in g 2
// Printing in g 3
// Panicking!
// Defer in g 3
// Defer in g 2
// Defer in g 1
// Defer in g 0
// Recovered in f 4
// Returned normally from f.
```

Note that the call to referred functions goes in the opposite direction.
This is because the defer statement places the goroutine in the goroutine stack.
And thus, they get popped in LIFO order.

The same example, but without calling `recover` inside a defer is presented below:

```go
package main

import "fmt"

func main() {
    f()
    fmt.Println("Returned normally from f.")
}

func f() {
//    defer func() {
//        if r := recover(); r != nil {
//            fmt.Println("Recovered in f", r)
//       }
//    }()
    fmt.Println("Calling g.")
    g(0)
    fmt.Println("Returned normally from g.")
}

func g(i int) {
    if i > 3 {
        fmt.Println("Panicking!")
        panic(fmt.Sprintf("%v", i))
    }
    defer fmt.Println("Defer in g", i)
    fmt.Println("Printing in g", i)
    g(i + 1)
}

// outputs
// Calling g.
// Printing in g 0
// Printing in g 1
// Printing in g 2
// Printing in g 3
// Panicking!
// Defer in g 3
// Defer in g 2
// Defer in g 1
// Defer in g 0

// AND NOW, this is different!

// panic: 4
// panic PC=0x2a9cd8
// [stack trace omitted]
```
