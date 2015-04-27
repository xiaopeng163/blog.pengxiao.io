title: Go Struct
date: 2014-05-01
categories:
- Go
tags:
- Go
---

![](/thumbnails/install-go-from-source/1.png)

source https://github.com/xiaopeng163/www.pythoner.io/blob/master/source/_posts/go-struct.md

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
package main

import "fmt"

type Position struct {
	X int
	Y int
}

func main() {
	a := Position{}
	fmt.Println(a)
	a.X = 1
	a.Y = 2
	fmt.Println(a)
}
```

The output is:
```
{0 0}
{1 2}
```

