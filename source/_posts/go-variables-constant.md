title: Basic Variables and Constants in Go
date: 2014-04-05
categories:
- Go
tags:
- Go
---

![](/thumbnails/install-go-from-source/1.png)

source https://github.com/xiaopeng163/www.pengxiao.me/blob/master/source/_posts/go-variables-constant.md


## Constants Defination

A constant `const` contains data which does not change

It is defined as `const identifier [type] = value`, for example `const PORT int = 22`.

Constants can only be numbers, strings or booleans.

The type specifier [type] is optional, the compiler can implicitly derive the type from the value.

For example, 

```go
const PORT int = 22
```

is equal as:

```go
const PORT = 22
```

We can define many constants together through:

```go
const (
	BGP_FSM_IDLE        = 1
	BGP_FSM_CONNECT     = 2
	BGP_FSM_ACTIVE      = 3
	BGP_FSM_OPENSENT    = 4
	BGP_FSM_OPENCONFIRM = 5
	BGP_FSM_ESTABLISHED = 6
)
```

We can use `iota` to simplify this defination:

```go
const (
	_ = iota
	BGP_FSM_IDLE
	BGP_FSM_CONNECT
	BGP_FSM_ACTIVE
	BGP_FSM_OPENSENT
	BGP_FSM_OPENCONFIRM
	BGP_FSM_ESTABLISHED
)
```

The values of iota start at zero within each const block and increment by one each time it is seen. 
This can be combined with the constant shorthand (leaving out everything after the constant name) to very concisely define related constants.

Please reference http://golang.org/doc/go_spec.html#Iota

## Variables

Variable can be defined as `var identifier [type] = value`, `type` is optional just like constant defination.

### Numbers

well known type is `int`

```go
var a int = 2
```

The length of `int` is based on your machine, if your machine is 32bit, then `int` is 32bit, if your machine is 64bit, then
`int` is 64bit.

If you want to be explicit about the length you can have that too with int32, or uint32.
The full list for (signed and unsigned) integers is int8, int16, int32, int64 and byte,
uint8, uint16, uint32, uint64.

some example for int defination:

```go
package main

import "fmt"

var (
    a = 1
    b = 2
    c = 3
)

var d int

var e int = 5

func main(){
    f := 6
    fmt.Printf("a is %d\n", a)
    fmt.Printf("b is %d\n", b)
    fmt.Printf("c is %d\n", c)
    fmt.Printf("d is %d\n", d)
    fmt.Printf("e is %d\n", e)
    fmt.Printf("f is %d\n", f)
}
```

The running results:

```go
a is 1
b is 2
c is 3
d is 0
e is 5
f is 6
```

### String

The type is `string`. Strings in Go are a sequence of UTF-8 characters enclosed in double quotes (”). If you use
the single quote (’) you mean one character (encoded in UTF-8) — which is not a string in
Go.

example:

```go
package main

import "fmt"

var (

    a = "1"
    b = "2"
    c = "3"
)

var d string

var e string = "5"

func main(){

    f := "6"
    fmt.Printf("a is %s\n", a)
    fmt.Printf("b is %s\n", b)
    fmt.Printf("c is %s\n", c)
    fmt.Printf("d is %s\n", d)
    fmt.Printf("e is %s\n", e)
    fmt.Printf("f is %s\n", f)
}
```

results:

```
a is 1
b is 2
c is 3
d is 
e is 5
f is 6
```

### Booleans

A boolean type represents the set of boolean truth values denoted by the predeclared
constants `true` and `false`. The boolean type is `bool`.

```go
var flag bool = false
```

examples:

```go
package main

import "fmt"

var (
    a = false
    b = true
)

var c bool

var d bool = true

func main(){
    e := true
    fmt.Printf("a is %t\n", a)
    fmt.Printf("b is %t\n", b)
    fmt.Printf("c is %t\n", c)
    fmt.Printf("d is %t\n", d)
    fmt.Printf("e is %t\n", e)
}
```

results:

```
a is false
b is true
c is false
d is true
e is true
```


Done!