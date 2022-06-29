# Bytes

Bytes are program's building blocks.

In Go, a byte is a type alias of `uint8`, and thus it is defined as:

```go
type byte = uint8
```

And thus, when talking about **bytes**, we mean a slice of `byte`, namely: `[]byte`.

## Why and when use bytes?

Bytes are pervasive, especially when working in systems programming. If you want
to work on an application related to networking, operating systems, databases,
you will likely end up working with bytes. And that is why it's very important
to understand what tools are available off the shelf.

In the following sections, we will address some operations Go allows us to
perform on bytes.
