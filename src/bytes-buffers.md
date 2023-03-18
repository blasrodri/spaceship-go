## Working with buffers

Sometimes our program needs to manipulate bytes. And we might or might not know
what's the size of this stream of bytes coming. Or perhaps we need to work with
chunks... who knows!

Buffers can help you, when you find yourself in these waters. But wait. What's a buffer?
Think of it as a variable sized array of bytes. It's a data structure that can be
resized to accommodate more or less bytes. It lets you write (`WriteByte`) new bytes to it,
read bytes from it (`ReadByte`), remove some bytes (`Truncate`), remove all (`Reset`), etc.

To create a Buffer, we need to call either `NewBuffer` or `NewBufferString`.
The difference is in the input that they take. The former accepts a byte slice `[]byte`
while the latter expects a string.

For testing purposes let's say we create a buffer with some bytes:

```go
// we call
b := bytes.NewBuffer([]byte("some bytes"))
```

To get the slice back, the `Bytes` method that can be used:

```go
b.Bytes() // [115 111 109 101 32 98 121 116 101 115]
```

We can read a fixed amount of bytes by using `Next(n int)`. It's important to note
that calling this method will actually **read the bytes**. And thus, the buffer
will have a new length of the previous one minus `n`. In case the buffer's length
was less than n, then the method will return the entire buffer.

Alternatively, buffers can grow, be truncated and reset. For that, we have:

```go
func (b *Buffer) Grow(n int) // increases the buffer's capacity
func (b *Buffer) Truncate(n int)
func (*Buffer) Reset() // same as Truncate(0)
```

`Truncate(n int)` will discard all the bytes from the buffer, except for the first n ones.
It does not shrink the capacity of the buffer. So this means that the buffer can still
accommodate the same amount of elements without having to perform a memory allocation.
If n < 0 or n > b.Len() then it panics.

If you want to shrink a buffer, Go does not offer a built in way of doing it.
An alternative of achieving it would be:

```go
// https://stackoverflow.com/questions/16748330/does-go-have-no-real-way-to-shrink-a-slice-is-that-an-issue
b = append([]T(nil), b[:newSize]...)
```

It is possible to work with the `Reader` interface. But it won't be covered in this chapter.
