# Pipe

In Go a pipe is **synchronous in-memory pipe**. `pipe` is a type that
is not publicly exposed. However, `io` allows us to interact with
it through `PipeWriter` and `PipeReader`. In order to create
these two halves of the `pipe`, `io` has a function:

```go
func Pipe() (*PipeReader, *PipeWriter)
```

Just for having the full picture, and to see how it works under the hood, this
is how `pipe` is defined:

```go
// A pipe is the shared pipe structure underlying PipeReader and PipeWriter.
type pipe struct {
	wrMu sync.Mutex // Serializes Write operations
	wrCh chan []byte
	rdCh chan int
	once sync.Once // Protects closing done
	done chan struct{}
	rerr onceError
	werr onceError
}
```

Again, as consumers of `pipe` we only care about `PipeReader` and `PipeWriter`.

> It can be used to connect code expecting an `io.Reader` with code expecting
> an `io.Writer`.

Sometimes it can happen that we hold an `io.Reader`, but we need to call a function
that expects an `io.Writer`. `Pipe` is a really good tool for achieving it.

> The data is copied directly from the `io.Write` to the corresponding `io.Read`
> (or Reads) there is no internal buffering.

## Video about pipes

I really suggest anyone to visit [this video](https://www.youtube.com/watch?v=LHZ2CAZE6Gs)
to get a deeper understanding on
how to use pipes.
