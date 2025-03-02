---
title: "Converting between types in Go"
date: 2025-03-02T20:10:02+01:00
tags: ["go"]
draft: false
---

Converting between types, known as "casting" in some other languages, is done in two different ways in Go: `Type Conversions` and `Type Assertions`. These have some distinct differences.

### Type Conversions

Type conversion simply means changing a non-interface type to another compatible type. The conversion is statically checked by the compiler. The main condition that needs to be satisfied is [assignability](https://go.dev/ref/spec#Assignability). In the following example the conversion will fail because the types are not compatible:

```go
type Foo int

a := "foo"
fmt.Println(Foo(a)) // cannot convert a (type string) to type Foo
```

In this example however, the conversion succeeds:

```go
var x uint8 = 42
var id int64 = int64(x)
```

### Type assertions

A type assertion asserts (surprise!) that a value v satisfies an interface type T. This is done at runtime. A failed assertion results in a panic, so you should verify it:

```go
thingOfTypeT, ok := v.(T)
if ok {
    // call T-functions on thingOfTypeT
}
```

