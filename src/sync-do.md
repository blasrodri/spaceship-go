## Once - Do

A very simple - but sometimes useful - method is `Do`. Its role is simply to
guarantee that a function run through it won't run more than once. This means
that if we have:

```go
var once sync.Once
a := make([]int, 0)
once.Do(func() {a = append(a, 1)})
once.Do(func() {a = append(a, 2)})
fmt.Print(a) // [1] and not [1 2]
```
`Once` is the right type, when it's important that a certain resource gets
called or initialized only once. In order to be able to call it again, and
that the function is actually run, another instance of `Once` needs to be
defined. And `Do` called from that new instance.