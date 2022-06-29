## panic

It's a function that stops the normal execution of the goroutine. All the
deferred functions are still being called, and once they complete this goroutine
returns to the caller. To the caller, the panic on the callee triggers a
termination on itself. This is a recursive mechanism that can eventually terminate
in the program exiting with a non-zero code. However, it's possible to control it
using recover. `panic` has different variants, and some are implemented in assembly,
while some other purely in Go.

```go
// Some are implemented in go

func panicshift()
func panicdivide()
```

```go
// Some others are implemented in assembly

func panicIndex(x, y int)
func panicIndexU(x uint, y int)
```
