## Making error great again

The following is a canonical example of how horrible error handling
can become in Go:

```go
_, err = fd.Write(p0[a:b])
if err != nil {
    return err
}
_, err = fd.Write(p1[c:d])
if err != nil {
    return err
}
_, err = fd.Write(p2[e:f])
if err != nil {
    return err
}
// and so on
```

An improvement, using a helper function:

```go
var err error
write := func(buf []byte) {
    if err != nil {
        return
    }
    _, err = w.Write(buf)
}
write(p0[a:b])
write(p1[c:d])
write(p2[e:f])
// and so on
if err != nil {
    return err
}
```

However, here we need to be sure that `err` is being captured by `write`
every time we call this function. We could actually do better, by creating a
new type, and adding a method for it that does what `write` is doing here. Then,
we will not need to have any closure, nor ensure that the value is being closed
by the function.

This is even a much better way of working with erros. And in some ways, it
brings to the table some of Haskell's/FP approach to error handling:

```go
type errWriter struct {
    w   io.Writer
    err error
}

func (ew *errWriter) write(buf []byte) {
    if ew.err != nil {
        return
    }
    _, ew.err = ew.w.Write(buf)
}
```

```go
ew := &errWriter{w: fd}
ew.write(p0[a:b])
ew.write(p1[c:d])
ew.write(p2[e:f])
// and so on
if ew.err != nil {
    return ew.err
}
```

Here, `write` checks whether `errWriter` have failed before. If it
did, then it won't do anything again; we are an _abnormal state_ already.
Otherwise, it's free to do some work.

### References:

- https://blog.golang.org/errors-are-values
- http://jxck.hatenablog.com/entry/golang-error-handling-lesson-by-rob-pike
