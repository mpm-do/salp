# Salp

Salp is a small Go wrapper for the [libstapsdt](https://github.com/sthima/libstapsdt) library that enables Go programs to create and fire USDT probes at runtime.

## Build

### Dependencies
Salp depends on `libstapsdt` which in turn depends on `libelf` and `libdl`.
`libstapsdt` can be build from source or (for debian based distros) installed
via an Ubuntu PPA. Full instructions are available in the [docs for libstapsdt](http://libstapsdt.readthedocs.io/en/latest/getting-started/getting-started.html)

### Build and Test
If `libstdapsdt` is installed globally (e.g. from the PPA above), you should be
able to simply `go build` or `go test`. However if you have built `libstapsdt`
form source then you will need to tell the `cgo` tool how to find the headers
and .so files for `libstapsdt` using the `CGO_CFLAGS`, `CGO_LDFLAGS`, and
`LD_LIBRARY_PATH` environment variables.

## Demo
This repository contains a demo executable that will fire two different probes
every second. The demo can be observed using the `trace` and `tplist` tools from
the [bcc](https://github.com/iovisor/bcc) project. Use two terminals to run the
demo - one to execute a tracable go program and one to run the bcc tools and see
their output. In the first window run

```
$ go run cmd/demo/*.go
```

This program will print out how to monitor itself but then won't print out
anything after that. In the second window run the bcc trace program on the
`salpdemo` process, monitoring probes `p1` and `p2`.

```
# trace -p "$(pgrep salpdemo)" 'u::p1 "arg1=%d arg2=%s", arg1, arg2' 'u::p2 "arg1=%d", arg1'
```

`trace` will output the values of `arg1` and `arg2` from probe `p1` and the
value of `arg1` from probe `p2`.