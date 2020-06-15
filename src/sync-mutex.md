## Mutex

A `Mutex` is a flag that can be held by at most one goroutine at at time. Its
name is derived from _mutually exclusive_, which indicates its purpose. The
_raison d’etre_ of a `Mutex` is synchronization, by preventing that no other
goroutine can perform an operation over the resources that are being mutexed.

It fulfills the `Locker` interface, thus it defines `Lock` and `Unlock`.

It's important to note that a lock doesn't belong to a particular goroutine.
Thus, it's allowed for a goroutine to lock the `Mutex` and another to `Unlock`
it. Therefore, it's still important how the different goroutines interact with
the resource in order both to avoid deadlocks but also to avoid accessing to
a resource when it's not locked.

## Lock

This is how `Lock` is implemented. But the real meat lies in `lockSlow`, which
we won't cover here. I'm mostly copying the definition just to see how the pieces
are put together. The complete implementation is quite complicated, and it involves
assembly code in the very end.

```go
func (m *Mutex) Lock() {
	// Fast path: grab unlocked mutex.
	if atomic.CompareAndSwapInt32(&m.state, 0, mutexLocked) {
		if race.Enabled {
			race.Acquire(unsafe.Pointer(m))
		}
		return
	}
	m.lockSlow()
}
```

If the `Mutex` is already locked, then the goroutine that calls `Lock` will block
until the `Mutex` gets released, and can be locked by it. 

## Unlock 
This method is responsible for unlocking the `Mutex`. It can be called from any
goroutine, and it's not necessary that is the one that locked it. However, if
it's unlocked, it cannot be unlocked right away. Otherwise, it panics. 
The implementation of this behavior is as follows:

```go
const (
    mutexLocked = 1 << iota // mutex is locked
)

func (m *Mutex) unlockSlow(new int32) {
	if (new+mutexLocked)&mutexLocked == 0 {
		throw("sync: unlock of unlocked mutex")
    }
    /*
    ...
    */
}
```