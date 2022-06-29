## Errors

Error handling is a very important topic in software engineering. There are
plenty different schools of thought. Go's approach to it is very simple.
It treats [**errors as values**](https://blog.golang.org/errors-are-values),
and to some people is insufficient. But we won't get into this discussion here.

The `error` type is used to indicate an _abnormal state_. When this happens,
there are several ways to act. In this chapter, we will only focus on the
availability of tools that Go provides the developer to characterize and respond
to these _anomalies_.

`error` is defined in the `built-in` package as:

```go
type error interface {
    Error() string
}
```

However, this package provides nothing else than this interface. Most of the
surrounding infrastructure is provided within the `errors` package instead.
