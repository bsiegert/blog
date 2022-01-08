---
title: "Go: Function pointers vs. single-method intefaces"
date: 2021-07-13T20:32:58Z
draft: true
categories:
 - Go
---
Even though I write a lot of Go in my dayjob, I have not written a lot about
Go API design and such here. Today, I want to write a surprising insight I had
recently:

**Function pointers and single-method interfaces are (almost) equivalent.**

This is because of two Go features: closures and receiver currying. (Plus
perhaps the ability to add methods to arbitrary types).

## Example: `http.Handler`

The `http.Handler` interface is one of many single-method interfaces from the
Go standard library:

```go
package http

type Handler interface {
	ServeHTTP(ResponseWriter, *Request)
}
```

That is, every type with a `ServeHTTP` method fulfils the interface and can be
added as an HTTP handler.
