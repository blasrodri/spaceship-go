# Sync

When working on a concurrent setup, Go's capabilities shine. It exposes natively
channels and goroutines. It makes it really easy to get off the ground with a
program that can -in many cases- run much faster.

The hard part of concurrency stems from having moving parts, which need
to communicate with each other and where **sequence** of that exchange really
matters. There are times when channels fall short. And that's why resorting
to the `sync` package can be a really good option.

Normally, I only pick a selection of functions, types, methods and interfaces to
cover from each package. But `sync`is an exception. I will cover everything from
it, because I really believe it's a critical package. And mastering makes a
difference.
