## As

`As` finds the **first error in err's chain that matches target**. If it
succeeds, it sets target to that error value and returns true. Otherwise,
it returns false.

`As` panics if target is not a non-nil pointer to either a type that implements
error, or to any interface type.

```go
var e *QueryError
if errors.As(err, &e) {
    // err is a *QueryError, and e is set to the error's value
}
```

## Is

`Is` reports whether **any error** (as opposed to finding the first with `As`)
in err's chain matches target.

The chain consists of err itself followed by the **sequence of errors obtained**
**by repeatedly calling Unwrap**.

```go
if errors.Is(err, ErrPermission) {
    // ...
}
```

This equivalent to the following:

```go
if e, ok := err.(*QueryError); ok && e.Err == ErrPermission {
    // ...
}
```

## Remarks

One of the biggest advantages of having these two methods, is when working with
highly nested errors. They bring all the necessary machinery to unwrap as much
as needed until a match is found. Or if there is no match, we are sure that all
the inner errors were also compared.
