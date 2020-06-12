# Notes about Go docs

When visiting any of the packages in [Go Std Library](https://golang.org/pkg/),
there is a certain pattern that is being kept through. It helps to dissect it,
so that we know what to expect from these docs, and also where to get extra
information. First, there is an **Overview**, which is a simple paragraph that
explains the scope of the package. It essentially tells the reader what to
expect from the package in question. Additionally, in some cases, it can make
some references to other packages when this helps to understand some design
choices. This can, for example, happen when the API of two packages is very
similar (i.e. bytes and strings).

The **Index** lists all the contents of the package. It outlines all the constants,
variables, functions, structs, methods, and interfaces exported by the package.
It's important to note that everything that is being listed in the docs is
public elements. That means, that the author of the package has deliberately
chosen to let the user of the API call. But there are other elements, that are
not public. And these help to build functionality, but the author has chosen not
to expose them out of the package. It is sometimes useful to go through the
private items of the package. This can help to get some understanding of the
design choices and trade-offs of some implementation. These private blocks are
found directly in the source code of the package, which is found at the bottom
of the index and examples, with the title of **package files**.

## Go Specification
It's important to note that, while the scope of this book is the Standard Library,
there is a lot of useful information in the [Go Spec](https://golang.org/ref/spec).
This is a resource that needs to be considered and reviewed almost every time,
when in doubt of how internal things work in the language.
