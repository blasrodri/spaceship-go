## Unwrap

_(feature added in Go 1.13)_

This is an interesting feature, when building errors that
**contain other errors**.

`Unwrap` is actually defined as follows:

```go
func Unwrap(err error) error {

	u, ok := err.(interface {
		Unwrap() error
	})
	if !ok {
		return nil
	}
	return u.Unwrap()

}
```

It first checks whether `err` has itself the `Unwrap` method. If it doesn't, then
nothing else can be done. There is nothing to unwrap. Nada. But if the method
is defined, it calls it and returns its value, which is an `error` itself.

This is a very useful way of composing errors, and thus adding much more
information. We could think of the following scenario:

```go
package main

import (
	"fmt"
	"log"
	"errors"
)
type databaseError struct {
    err error
}

type queryMissingParamsError struct {
    params string
}

func (de *databaseError) Unwrap() error {
    return de.err
}

func (de *databaseError) Error() string {
    return fmt.Sprintf("Database error. %s", de.err)
}

func (qmp *queryMissingParamsError) Error() string {
    return fmt.Sprintf("Some parameter were missing: %s", qmp.params)
}

// bogus query
type Query struct {}

func (q *Query) Check() error {
    return &databaseError {
        err: &queryMissingParamsError {
            params: "name",
        },
    }
}

func handleDatabaseQuery(query *Query) error {
    err := query.Check()
    if err == nil {
        return nil
    }
    log.Print(err)
    return errors.Unwrap(err)
}
func main() {
	q := &Query{}
	err := handleDatabaseQuery(q)
	fmt.Println(err)
}

```

In this example, we can see how an error can pack and unpack other errors. And
we can choose to define a strategy for the different scenarios. So, in this
particular example, we could ask the user to make the query again and remind
her not to forget to include _name_ as a parameter.
