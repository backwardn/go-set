# go-set - type safe sets for Go

This package is a type safe continuation of the package excellent [fatih/set](https://github.com/fatih/set).

## Motivation

We found ourselves using the [fatih/set](https://github.com/fatih/set) package a lot in our code and we like
it's simplicity and easy of use a lot. We did notice however that it is no longer maintained so we decided
to adopt it. We are however fans of type safe code so in the process we decided to make it generic and simply
generate code for the types we needed. This repository contains most of the simple types and is considered a
ready to use as is set of packages for when you need sets.

## Code generation

For code generation we use the [gVisor](https://github.com/google/gvisor) tool [go_generate](https://github.com/google/gvisor/tree/master/tools/go_generics). To install this tool you need to follow gVisor
instructions for building gVisor and use the resulting go_generate binary. There is unfortunately no way to `go get`
the tool at the time of this writing.

Once you have `go_generate` installed properly you can regenerate the code using `go generate` in the top level
directory.

## Performance

The improvement in performance by using concrete types over `interface{}` is notable although in a real app
it might be an insignificant overhead given there are so many other things going on.

There is however a small benchmark suite comparing the original implementation to the generated and as you can see
below there is a notable difference.

```
BenchmarkTypeSafeSetHasNonExisting-8        200000000        5.19 ns/op
BenchmarkInterfaceSetHasNonExisting-8        50000000        78.4 ns/op
BenchmarkTypeSafeSetHasExisting-8           200000000        7.09 ns/op
BenchmarkInterfaceSetHasExisting-8           30000000         122 ns/op
BenchmarkTypeSafeSetHasExistingMany-8       100000000        15.3 ns/op
BenchmarkInterfaceSetHasExistingMany-8       30000000         145 ns/op
BenchmarkTypeSafeSetAdd-8                     5000000         330 ns/op
BenchmarkInterfaceSetAdd-8                    2000000         726 ns/op
BenchmarkBaselineAdd-8                       50000000        72.0 ns/op
```

## Using the library

To use any of the pregenerated sets just import them in your code as per the usual Go manner. All packages have a
factory function `New` that should be used to create sets.

Example code using the generated string set:

```go
s := sset.New()
s.Add("entry 1")
s.Add("entry 2")

if s.Has("entry 2") {
    // do something
} else {
    // do something else
}
```

The library has several of the more convenient set operations for `unions` and `disjunctions` etc as well.

To calculate the intersection of two sets do this:

```go
s1 := sset.New()
s1.Add("entry 1")
s1.Add("entry 2")

s2 := sset.New()
s2.Add("entry 2")
s2.Add("entry 3")

s3 := sset.Intersection(s1, s2)

// s3 no contains only "entry 2"
```