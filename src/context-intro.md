# Context

Package context defines the Context type. A context can include deadlines,
cancellation signals, and other request-scoped values across API boundaries
and between processes. A key feature of this pattern is that it allows signals
to be propagated. So for instance, when a context is cancelled then all the 
contexts derived from it are cancelled as well. 

Context is a very singular pattern, that I have not seen implemented in the
standard libraries of other languages. And since it's widely adopted in different
projects, I thought it was worth covering it.

Go proposes an interface to work with `Context`, whichh is 
> Programs that use Contexts should follow these rules to keep interfaces
> consistent across packages and enable static analysis tools to check context propagation:
> 
> Do not store Contexts inside a struct type; instead, pass a Context explicitly to each function that needs it.
> The Context should be the first parameter, typically named ctx:

```go
func DoSomething(ctx context.Context, arg Arg) error {
	// ... use ctx ...
}
```

Next, we describe some of the different functionalities offered  by the package
and show alternatives of how it can be used. Much of it has been taken from 
[this blog post](https://blog.golang.org/context) by Sameer Ajmani.
