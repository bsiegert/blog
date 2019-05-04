---
title: "Supporting Go Modules in pkgsrc, a Proposal"
date: 2018-12-29T13:12:02+01:00
categories:
 - pkgsrc
 - go
---


Go 1.11 introduced a new way of building Go code that no longer needs a `GOPATH` at all. In due course, this will
become the default way of building. What's more, sooner or later, we are going to want to package software that
*only* builds with modules.

There should be some package-settable variable that controls whether you want to use modules or not. If you are going 
to use modules,  then the repo should have a `go.mod` file. Otherwise (e.g. if there is a `dep` file or something), 
the build could start by doing `go mod init` (which needs to be after `make extract`).

## fetch

There can be two implementations of the `fetch` phase:

1.  Run `go mod download`. 

    It should download required packages into a cache directory, `$GOPATH/pkg/mod/cache/download`.
    Then, I propose tarring up the whole tree into a single .tar.gz and putting that into the distfile directory
    for `make checksum`. Alternatively, we could have the individual files from the cache as "distfiles". Note however
    (see below) that the filenames alone do not contain the module name, so there will be tons of files named `v1.0.zip`
    and so on.
   
2.  "Regular fetch"

    Download the .tar.gz (or the set of individual files) from above from the `LOCAL_PORTS` directory on ftp.n.o,
    as usual.

The files that `go mod download` creates are different from any of the ones
that upstream provides. Notably, the zip files are based on a VCS checkout followed by re-zipping. Here is an example
for the piece of a cache tree corresponding to a single dependency (ignore the lock files):
  
```
./github.com/nsf/termbox-go/@v:
list                                           v0.0.0-20180613055208-5c94acc5e6eb.lock        v0.0.0-20180613055208-5c94acc5e6eb.ziphash
list.lock                                      v0.0.0-20180613055208-5c94acc5e6eb.mod
v0.0.0-20180613055208-5c94acc5e6eb.info        v0.0.0-20180613055208-5c94acc5e6eb.zip
```

As an additional complication, (2) needs to run after "make extract".  Method (1) cannot always be the default,
as it needs access to some kind of hosting. A non-developer cannot easily upload the distfile.

## extract

In a `GOPATH` build, we do some gymnastics to move the just-extracted source code into the correct place in a
`GOPATH`. This is no longer necessary, and module builds can just use the same `$WRKSRC` logic as other software.

## build

The dependencies tarball (or individual dependencies files) should be extracted into `$GOPATH`, which in non-mod
builds is propagated through buildlink3.mk files of dependent packages. After this, in all invocations of the `go`
tool, we set `GOPROXY=file://$GOPATH/pkg/mod/cache/download`, as per this comment from the help:

> A Go module proxy is any web server that can respond to GET requests for
> URLs of a specified form. The requests have no query parameters, so even
> a site serving from a fixed file system (including a file:/// URL)
> can be a module proxy.
>
> Even when downloading directly from version control systems,
> the go command synthesizes explicit info, mod, and zip files
> and stores them in its local cache, $GOPATH/pkg/mod/cache/download,
> the same as if it had downloaded them directly from a proxy.
