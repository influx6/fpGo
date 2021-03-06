# fpGo
Monad, Functional Programming features for Golang

# Why

I love functional programing, Rx-style coding, and Optional usages.

However it's hard to implement them in Golang, and there're few libraries to achieve parts of them.

Thus I implemented fpGo. I hope you would like it :)

# Features

* Optional

* Maybe

* Publisher



* Pattern matching

* Fp functions



* Java8Stream-like Collection

* PythonicGenerator-like Coroutine(yield/yieldFrom)


# Usage

## Optional (IsPresent/IsNil, Or, Let)

```go
var m MaybeDef
var orVal int
var boolVal bool

// IsPresent(), IsNil()
m = Maybe.JustVal(1)
boolVal = m.IsPresent() // true
boolVal = m.IsNil() // false
m = Maybe.Just(nil)
boolVal = m.IsPresent() // false
boolVal = m.IsNil() // true

// Or()
m = Maybe.JustVal(1)
fmt.Println(*(m.OrVal(3))) // 1
m = Maybe.Just(nil)
fmt.Println(*(m.OrVal(3))) // 3

// Let()
var letVal int
letVal = 1
m = Maybe.JustVal(1)
m.Let(func() {
  letVal = 2
})
fmt.Println(letVal) // letVal would be 2

letVal = 1
m = Maybe.Just(nil)
m.Let(func() {
  letVal = 3
})
fmt.Println(letVal) // letVal would be still 1
```

## MonadIO (RxObserver-like)

Example:
```go
var m *MonadIODef
var actualInt int

m = MonadIO.JustVal(1)
actualInt = 0
m.Subscribe(Subscription{
  OnNext: func(in *interface{}) {
    actualInt, _ = Maybe.Just(in).ToInt()
  },
})
fmt.Println(actualInt) // actualInt would be 1

m = MonadIO.JustVal(1).FlatMap(func(in *interface{}) *MonadIODef {
  v, _ := Maybe.Just(in).ToInt()
  return MonadIO.JustVal(v + 1)
})
actualInt = 0
m.Subscribe(Subscription{
  OnNext: func(in *interface{}) {
    actualInt, _ = Maybe.Just(in).ToInt()
  },
})
fmt.Println(actualInt) // actualInt would be 2
```

## Stream (inspired by Collection libs)

Example:
```go
var s *StreamDef
var tempString = ""

s = Stream.FromArrayInt([]int{}).Append(Maybe.JustVal(1).Ref()).Extend(Stream.FromArrayInt([]int{2, 3, 4})).Extend(Stream.FromArray([]*interface{}{Maybe.Just(nil).Ref()}))
tempString = ""
for _, v := range s.ToArray() {
  tempString += Maybe.Just(v).ToMonad().ToString()
}
fmt.Println(tempString) // tempString would be "1234<nil>"
s = s.Distinct()
tempString = ""
for _, v := range s.ToArray() {
  tempString += Maybe.Just(v).ToMonad().ToString()
}
fmt.Println(tempString) // tempString would be "1234"
```

## Compose

Example:

```go
var fn01 = func(obj *interface{}) *interface{} {
  val, _ := Maybe.Just(obj).ToInt()
  return Maybe.JustVal(val + 1).Ref()
}
var fn02 = func(obj *interface{}) *interface{} {
  val, _ := Maybe.Just(obj).ToInt()
  return Maybe.JustVal(val + 2).Ref()
}
var fn03 = func(obj *interface{}) *interface{} {
  val, _ := Maybe.Just(obj).ToInt()
  return Maybe.JustVal(val + 3).Ref()
}

// Result would be 6
result := *Compose(fn01, fn02, fn03)(Maybe.JustVal(0).Ref())
```
