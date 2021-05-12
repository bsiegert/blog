---
title: "More Go modules in pkgsrc"
date: 2021-05-12T18:31:25Z
categories:
 - pkgsrc
 - go
---
This weekend, I made a series of somewhat unusual changes to pkgsrc.

I removed a bunch of Go packages.

Why? Because of **Go modules**.

# What are Go modules?

Since my series of design-ish blog posts([part 1], [part 2]), Go module builds
have fully landed in pkgsrc, to the point that they are now the preferred way to
build Go packages.

To recap: There are two ways to use the `go` tool to build Go code. 

* The old way is to have a tree, below `$GOPATH`, that has all the dependencies
  in a directory tree according to their import path. For instance, the
  [`golang.org/x/net`] package would be placed in a
  `$GOPATH/src/golang.org/x/net` directory. This is what `lang/go/go-package.mk`
  implements in pkgsrc.
* The new way is to extract the source code wherever you want, just like any C
  source. The top-level source directory contains a `go.mod` file that specifies
  dependencies and their versions. The `go` tool then downloads a bunch of `.zip`
  and `.mod` files for those dependencies and unpacks them as needed. This is
  similar to how Cargo works for Rust code. 

Like with Rust, in pkgsrc, we specify a list of dependent module files to be
downloaded from the module proxy.

In actual practice, a useful pattern has emerged, where the
list of modules is in a separate file named `go-modules.mk` in the package
directory. To create or update the file, simply run

```shell
$ make patch
$ make show-go-modules > go-modules.mk
```

and then `.include` the file from the main `Makefile`.

# But why remove all these packages?

A pkgsrc package built with `lang/go/go-module.mk` does not install any source
code or `.a` files. Only the binaries are packaged, just like for C.
Go packages that just correspond to intermediate libraries and do not contain
any useful binaries are simply no longer needed. They can be deleted as soon as
nothing depends on them any more.

In particular, I changed all the packages depending on [`golang.org/x/tools`]
to be modules, then migrated the go-tools package itself. go-tools depends on a
number of other libraries that nothing else depends on. 

By the way, it is fairly simple to make a non-module into a module, even if the
source does not contain a `go.mod`:

1. Change `go-package.mk` to `go-module.mk`.
2. Run `make patch` and change into the top-level source directory.
3. Run `go mod init github.com/foo/bar` or whatever the import path is.
4. Update the file with `go get` and/or `go mod tidy`.
5. Copy the generated `go.mod` and `go.sum` files to a `files` directory and
   copy them into place in `pre-patch`.

# Future

Some future Go release will deprecate `GOPATH` builds, so we must convert all Go
code in pkgsrc to modules at some point. By the way, if upstream has not made
the jump to modules yet, they might be happy about your pull request :)


[part 1]: /posts/go-mod-support
[part 2]: /posts/go-mod-support-2
[`golang.org/x/net`]: https://pkg.go.dev/golang.org/x/net
[`golang.org/x/tools`]: https://pkg.go.dev/golang.org/x/tools
