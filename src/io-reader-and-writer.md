## Writer

Writer is the interface that wraps the basic Write method. `io` package makes
extensive use of this interface. `Writer` is implemented by `os.File`. Which
in the `io` package is used to reference to `Stdin`, `StdOut`, and `StdErr`:

```go
var (
    Stdin  = NewFile(uintptr(syscall.Stdin), "/dev/stdin")
    Stdout = NewFile(uintptr(syscall.Stdout), "/dev/stdout")
    Stderr = NewFile(uintptr(syscall.Stderr), "/dev/stderr")
)
```

### How does writer works?

> Write writes len(p) bytes from p to the underlying data stream.
> It returns the number of bytes written from p (0 <= n <= len(p))
> and any error encountered that caused `write` function to stop
> early. Write must return a non-nil error if it returns n < len(p).
> `Write` must not modify the slice data, even temporarily.

Implementations must not retain p.

```go
type Writer interface {
    Write(p []byte) (n int, err error)
}
```

To see an implementation, we can check the one for `os.File`. There
are some OS dependent helper functions that help `Write` doing their
job. But essentially it is a transparent copy of its description.

Check a fragment of the POSIX implementation of `Write` from the source
code in the `os` package:

```go
// Write writes len(b) bytes to the File.
// It returns the number of bytes written and an error, if any.
// Write returns a non-nil error when n != len(b).

func (f *File) Write(b []byte) (n int, err error) {
    // for posix f.checkValid returns nil unless f is nil
	if err := f.checkValid("write"); err != nil {
		return 0, err
	}
	n, e := f.write(b)
	if n < 0 {
		n = 0
	}
	if n != len(b) {
		err = io.ErrShortWrite
	}
	epipecheck(f, e)

if e != nil {
		err = f.wrapErr("write", e)
	}
	return n, err
}
```

Side note: it's important to see that `write` will block the thread. There's
an ongoing [discussion](https://github.com/golang/go/issues/6817) to provide
an async API for files, but it's still being considered.

## Reader

Similarly, `Reader` is the reading counterpart. It is also an interface that wraps
the `Read` method.

> `Read` reads up to len(p) bytes into p. It returns the number of bytes
> read (0 <= n <= len(p)) and any error encountered. Even if `Read` returns
> n < len(p), it may use all of p as scratch space during the call.
> If some data is available but not len(p) bytes, `Read` conventionally
> returns what is available instead of waiting for more.

It is defined as:

```go
type Reader interface {
    Read(p []byte) (n int, err error)
}
```

As an implementor of `Read`, when there is nothing else to read, the Standard
Library suggest to return 0, `EOF` instead. 0, nil is only acceptable when
`p` length is equal to zero. But that's not happening very often.

Additionally, the Standard Library suggest first reading the `n` bytes, and then
handling the error if there was any. It adds that:

> Doing so correctly handles I/O errors that happen after reading some bytes and
> also both of the allowed EOF behaviors.

`Read` implementation for type `os.File` relies on a helper function `read` that
does all the work (except error handling). `read` itself relies on `poll.FD` (on \*nix),
which is in charge of checking whether there is anything to be read on that
file descriptor. For the very curious reader, go and check its implementation of
`FD`'s `Read` [here](https://golang.org/src/internal/poll/fd_unix.go?s=4169:4210#L135).

```go
func (f *File) Read(b []byte) (n int, err error) {
	if err := f.checkValid("read"); err != nil {
		return 0, err
	}

	n, e := f.read(b)
	return n, f.wrapErr("read", e)
}
```
