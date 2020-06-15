## WaitGroup

> A WaitGroup waits for a collection of goroutines to finish.
> The main goroutine calls `Add` to set the number of goroutines to wait for.
> Then each of the goroutines runs and calls `Done` when finished.
> At the same time, `Wait` can be used to block until all goroutines have finished. 

It's possible to have another goroutine also to wait on this group. The only
real constraint is that `Add` can only be called by the main one.

### Add

```go
func (wg *WaitGroup) Add(delta int)
```

The **delta** defines how many more - or less - goroutines, the goroutines
waiting need to actually wait for. When the counter gets to zero, then
all the waiting goroutines get released. If the counter is negative, then
`Add` panics.

### Done

It decrements the counter by one.

### Wait

Blocks the goroutine until the counter of the `WaitGroup` gets to zero.