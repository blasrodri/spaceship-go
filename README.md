# Spaceship Go

![](./controllers_brief.svg)
<img src="./src/img/cover.svg">

To view the book, click [here](https://blasrodri.github.io/spaceship-go-gh-pages/)

## Notes from the author

This is a set of notes I gathered on my way to understanding how things worked
in Go. The Standard Library is one of the best places to find answers. Especially
for Go, since it comes with _batteries included_. And its source code is
wonderfully documented.

There is official documentation, which I made extensive usage of. And some
blog posts from the Go team that helped me build my understanding. But no
matter how good these resources are, it can be difficult for a non-expert
developer to try to find some answers there. And this is what this book is
about: it serves as a bridge between these learners, and some amazing
resources that perhaps look a bit intimidating.

I expect that there will be errors and things that could be better explained. For
that is that I have made this repository public. I intend to use it as an
exchange of ideas and suggestions. PRs and issues are more than welcome. And
they will be included in the book as soon as they're merged to master.

Writing is challenging. But I found it enormously helpful, as an exercise towards
building a deeper understanding of how things work. If one can explain something,
it means that one understands it. And this is the purpose of these notes. I hope
I've learned, and I also hope you too!

## Building

To build the book:

1. [Install `mdbook`](https://rust-lang.github.io/mdBook/guide/installation.html)
2. Clone the repository (`git clone https://github.com/blasrodri/spaceship-go.git`)
3. Run `mdbook`

To optionally build the epub file format, install `mdbook-epub`:

```
cargo install mdbook-epub --version 0.4.14-alpha.0
# Then run mdbook
mdbook
```

Output will be saved as `book/Spaceship Go.epub`.
