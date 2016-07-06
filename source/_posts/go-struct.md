title: Go Struct - 1
date: 2016-05-01
categories:
- Go
tags:
- Go
---

source https://github.com/xiaopeng163/www.pengxiao.me/blob/master/source/_posts/go-struct.md

### The Basic

Go does not have a concept of class, but it has `struct`. A struct is just like a simple class which only contain values.

The format of the definition of a `struct` is as follows:

```go
type identifier struct{
    filed1 type
    filed2 type
}
```

For example:

```go
type Person struct {
	Name string
	Age int
}
```

### Initialization

We can create an instance of our new Person type in a variety of ways:

```go
var p Person
```

This will create a local `Person` variable called `p`, and with a default zero value. For a struct zero means each of the fields is set to their corresponding zero value (0 for ints, 0.0 for floats, "" for strings, nil for pointers, …)

We can also use the new function:

```go
p := new(Person)
```

This allocates memory for all the fields, sets each of them to their zero value and returns a pointer. More often we want to give each of the fields a value. We can do this in two ways. Like this:

```go
p := Person{Name: "Jim", Age: 21}
```

Or we can leave off the field names if we know the order they were defined:

```go
c := Person{"Jim", 21}
```
