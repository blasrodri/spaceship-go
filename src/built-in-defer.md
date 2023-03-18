## defer

This function is not defined in the built-in package. But since it is used in
the example for `recover` I wanted to mention it.

`defer` is used to ensure that a function will be executed right before the 
function that calls it terminates. As [Go By example](https://gobyexample.com/defer)
mentions, other languages provide the construct _finally_ in some contexts.

The syntax is simple:
```go
defer func() { // ... 
}()
```

Or directly

```go
defer closeFile(f)
```

`defer` takes a closure as argument. Which means that it is possible to include a wide
range of elements inside it.

As noted in the [recover](./built-in-recover.md) section, if we call `defer` multiple times, the order in which
these functions will be executed is **last in, first out**. So, for instance:

```go
import (
    "fmt"
)
func main() {
    fmt.Println("Let's count backwards:")
    defer func() {
        fmt.Println(1)
    }()
    defer func() {
        fmt.Println(2)
    }()
    defer func() {
        fmt.Println(3)
    }()
}
```
This will output

```
Let's count backwards:
3
2
1
```
