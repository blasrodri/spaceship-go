## IO

> Package io provides basic interfaces to I/O primitives.
> Its primary job is to wrap existing implementations of such primitives,
> such as those in package os, into shared public interfaces that abstract
> the functionality, plus some other related primitives.

`io` is a very important package in Go. It provides a set of primitives
and interfaces that every developer needs to understand in order to work
properly with bytes, standard input, output, error and pipes among others.

An important note is that these primitives are not thread safe by default.
This means that they shouldn't be used by multiple goroutines at the same time.
