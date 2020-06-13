# make and new

## make

`make` is a very singular function. Its purpose is to allocate and initialize
an object of type slice, map, or chan (only). The output depends on the type
it's operating upon. You can see the differences, depending upon the type in the
excerpt below:

```go
// from src/go/cmd/compile/internal/gc/builtin/runtime.go
func makemap64(mapType *byte, hint int64, mapbuf *any) (hmap map[any]any)
func makemap(mapType *byte, hint int, mapbuf *any) (hmap map[any]any)
func makemap_small() (hmap map[any]any)
func makechan64(chanType *byte, size int64) (hchan chan any)
func makechan(chanType *byte, size int) (hchan chan any)
func makeslice(typ *byte, len int, cap int) unsafe.Pointer
func makeslice64(typ *byte, len int64, cap int64) unsafe.Pointer
func makeslicecopy(typ *byte, tolen int, fromlen int, from unsafe.Pointer) unsafe.Pointer
```

## new

It can be thought as a `make` alternative, but with a size equal to zero and
always initializing the value to its zero value.
