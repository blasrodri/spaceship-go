## Cond
> Cond implements a condition variable, a rendezvous point for goroutines
> waiting for or announcing the occurrence of an event. 


## Broadcast
A typical use case for `Broacast` is when multiple goroutines are waiting
for something to happen. In order to let all of them that a certain event
happened using channels, then we would need to have one channel for each
of them. And then send each of them a message. This is what `Broadcast`
is about. Broadcast wakes all goroutines waiting on that `Cond`so that
they can continue working.

Inside a `Cond` type, there is an attribute called `notify` of type `notifyList`.
`notify` is used by a function called `runtime_notifyListNotifyAll` in order to wake
up all goroutines.

func (c *Cond) Broadcast() {
	c.checker.check()
	runtime_notifyListNotifyAll(&c.notify)
}

And even when it's not necessary to understand how `Broadcast`works, if we peek
at runtime_notifyListNotifyAll implementation, we can actually verify that it
_readies_ all the outstanding goroutines by calling `goready`.

```go

// notifyListNotifyAll notifies all entries in the list.

//go:linkname notifyListNotifyAll sync.runtime_notifyListNotifyAll

func notifyListNotifyAll(l *notifyList) {
	// Fast-path: if there are no new waiters since the last notification
	// we don't need to acquire the lock.
	if atomic.Load(&l.wait) == atomic.Load(&l.notify) {
		return
	}
    /* 
    omitted code
    */
    s := l.head
    /* 
    omitted code
    */
	for s != nil {
		next := s.next
		s.next = nil
		readyWithTime(s, 4) // this function calls goready
		s = next
	}
}
```

## Signal
Signal will only wake **one** goroutine, if there was at least one waiting.

The implementation is similar to the one for `Broadcast`. This time, it relies
on a helper function called `notifyListNotifyOne`.

## Wait

Cond has an associated `Locker` (which normally is a *Mutex or *RWMutex). This
`Locker` must be held when calling the Wait method.

If there's a goroutine that needs to be awaken to do some work,
it can be modelled as follows:

```go
go func(c *sync.Cond) {
    // hold the Locker when calling the wait method
    c.L.Lock()
    for !condition() {
        c.Wait()
    }
    // ... make use of condition ...
    c.L.Unlock()
    // Unlock the Locker when it's done
}
```

For completeness, a `Locker` is just an interface that defines two methods: `Lock` and `Unlock`.
