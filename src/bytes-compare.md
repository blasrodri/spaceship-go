## Compare

First, we can compare two bytes. That is, having a function that tells us whether
these two bytes are the same or not.

```go
func Compare(a, b []byte) int
```
The function Compare does this for us. It checks whether two **byte slices** are
the same, and returns the result as an integer. The function performs a
[lexicographic comparison](https://en.wikipedia.org/wiki/Lexicographical_order)
and returns 0 if they are the same, -1 if a < b and +1 if a > b.

It's interesting to note how this function is implemented. Diving into Go's source code
we can see the following:

```go
func Compare(a, b []byte) int {

	return bytealg.Compare(a, b)

}
```
Something as _simple_ as just comparing bytes, involves going down to assembly language.
The function Compare calls the function Compare in the package `bytealg`, which defines 
`Compare` like this:

```go
//go:noescape

func Compare(a, b []byte) int
```

The directive `//go:noespace` tells the compiler that the values passed to the function
must be stored in the stack. And additionally that the function implementation is not written
in Go. This means that the byte comparison function is implemented in a lower language, like
assembly.

Check [Compiler directives](https://golang.org/cmd/compile/#hdr-Compiler_Directives) if you
want to get more details in this topic.
