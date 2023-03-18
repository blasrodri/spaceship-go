# Context details

The `Context` carries a deadline, a cancellation signal, `Done` and other values
across processes and APIs. It is defined as an interface:

```go
type Context interface {
    Deadline() (deadline time.Time, ok bool)
    Done() <-chan struct{}
    Err() error
    Value(key interface{}) interface{}
}
```

Note: these methods may be called by multiple goroutines simultaneously.

The package provides different types of contexts, with different _service levels_:

- emptyCtx: it's one that is never cancelled, has no values and no deadline
- cancelCtx: is one that can be cancelled. When cancelled, it also cancels all
  its children that implement cancel.
- valueCtx: carries a key-value pair. Delegates all other calls (such as cancel) to its
  embedded context.
- timerCtx: carries a timer and a deadline. It embeds `cancelCtx` to implement `Done` and `Err`.
  `cancel` is implementing by stopping its timer and delegating the call to `cancelCtx`

It's important to note that as a user of the package, we only have access to these types through

```go
func WithCancel(parent Context) (ctx Context, cancel CancelFunc)
func WithDeadline(parent Context, d time.Time) (Context, CancelFunc)
func WithTimeout(parent Context, timeout time.Duration) (Context, CancelFunc)
```

## CancelFunc

```go
type CancelFunc func()
```

When creating a context, there are some factory functions. And all of them return a `CancelFunc` type.
This is, as its name indicates, a function. It tells an operation to abandon its work. It's async, and
it can be called by multiple goroutines simultaneously. After the first call, the subsequent calls are
no-ops.

As an example, for the `cancelCtx`, `cancel` method exists. It closes the `done` channel and cancels
all of its children, plus some other cleaning tasks.

## Examples

This is an extension of the original example provided in the std library.
It depicts the usage of context within several goroutines that form a tree.

Once the main goroutine triggers the `cancel` function, all the others
are also cancelled.

```go
package main

import (
	"context"
	"fmt"
	"time"
)

func main() {
	// gen generates integers in a separate goroutine and
	// sends them to the returned channel.
	// The callers of gen need to cancel the context once
	// they are done consuming generated integers not to leak
	// the internal goroutine started by gen.
	gen := func(ctx context.Context) <-chan int {
		ctx2, cancel := context.WithCancel(ctx)
		defer cancel()
		// propagating the cancel context to a new goroutine
		go doStuff(ctx2)
		dst := make(chan int)
		n := 1
		go func() {
			for {
				select {
				case <-ctx.Done():
					fmt.Println("Closing gen")
					fmt.Println(n)
					return // returning not to leak the goroutine
				case dst <- n:
					n++
				}
			}
		}()
		return dst
	}

	ctx, cancel := context.WithCancel(context.Background())
	//	defer cancel() // cancel when we are finished consuming integers
	for n := range gen(ctx) {
		fmt.Println(n)
		if n == 5 {
			break
		}
	}
	cancel()
	for {
		time.Sleep(3 * time.Second)
		break
	}
}
func doStuff(ctx context.Context) {
	fmt.Println("Starting child")
	for {
		select {
		case <-ctx.Done():
			fmt.Println("Closing child")
			return
		}
	}
}

```

A plausible output (it's non-deterministic):

```
1
2
3
4
5
Closing gen
6
Starting child
Closing child
```

### Using WithTimeout

This example is rather simple. But it shows how to propagate a timeout into a tree.
Here we show how the main goroutine receives a `Done` signal after 50 milliseconds. This
triggers (through `cancel`) subsequent `Done` signals to all the goroutines that
where created with the `WithTimeout` method.

```go
package main

import (
	"context"
	"fmt"
	"time"
)

func main() {
	// Pass a context with a timeout to tell a blocking function that it
	// should abandon its work after the timeout elapses.
	ctx, cancel := context.WithTimeout(context.Background(), 50*time.Millisecond)
	ctx2, cancel2 := context.WithTimeout(context.Background(), 50*time.Millisecond)

	func(ctx2 context.Context) {
		select {
		case <-ctx2.Done():
			fmt.Println(ctx2.Err()) // prints "context deadline exceeded"
			return
		case <-time.After(100 * time.Millisecond):
			fmt.Println("overslept 2")
		}

	}(ctx2)

	defer cancel()
	defer cancel2()

	select {
	case <-time.After(100 * time.Millisecond):
		fmt.Println("overslept")
	case <-ctx.Done():
		fmt.Println(ctx.Err()) // prints "context deadline exceeded"
	}

}
```

## Context being used in the real world

These are some libraries making heavy use of this pattern:

1. [kubernetes](https://github.com/kubernetes/kubernetes)
2. [gRPC](https://github.com/grpc/grpc-go)
3. [dgraph](https://github.com/dgraph-io/dgraph)
