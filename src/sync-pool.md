## Pool
> A Pool is a set of temporary objects that may be individually saved and retrieved.

`Pool` provides a way to amortize allocation overhead. It serves for when there
are multiple users of a resource, and we would like to avoid having to create
a new time every time we need to.

`Pool` caches allocated but unused items so that they can be available for later
use. It relieves pressure from the garbage collector. However, 
**Any item stored in the Pool may be removed automatically at**
**any time without notification**.


```go
type Pool struct {

    // New optionally specifies a function to generate
    // a value when Get would otherwise return nil.
    // It may not be changed concurrently with calls to Get.
    New func() interface{}
    // contains filtered or unexported fields
}
```
The important bit from the definition is that, whenever we call
`Get`, and there are no items left in the pool, then it creates
a new one by calling `New`. 

Once an item has been used, it can be returned to the `Pool` by
calling `Put`. `Put` adds an item back into the `Pool`.


