# Index

Let's say we have some bytes, represented as a slice of byte, which in Go lingo means
`[]byte`. If we want to verify whether these bytes contain a certain subset of bytes,
and we're particularly interested in knowing where in the slice they are located
we can use the function `Index`, provided in the package **bytes**.

```go
func Index(s, sep []byte) int
```

The way it is implemented is as follows:

- If the length of the separator is 0, then it returns 0. Nothing more to do
- If it is 1, it calls another function called IndexByte to do the work.
  This is also an assembly function. One of its variants can be found [here](https://golang.org/src/internal/bytealg/indexbyte_amd64.s)
- If the sizes between sep and s match, then it checks whether they are equal or not. For that, it calls Equal, which we have already mentioned.
- If the separator length is bigger than one, then it's no longer a byte. It then goes to a more complex logic involving `bytealg.Index`.

Besides the full implementation, sometimes it's useful to follow how things work under the hood.
And in such a low level package, we can find the need of working with assembly and targeting
the different architectures. Check [bytealg package](https://golang.org/src/internal/bytealg/) for an overview.
