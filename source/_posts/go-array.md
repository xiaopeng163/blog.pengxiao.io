title: Go Array
date: 2014-04-10
categories:
- Go
tags:
- Go
---

![](/thumbnails/install-go-from-source/1.png)

source https://github.com/xiaopeng163/www.pythoner.io/blob/master/source/_posts/go-array.md


## Defination

How to define an array in Go? Use `var identifier [len]type`. Array's length is fixed after defination, like

``` go
var a [10]int
```

`a` is an array, and its length is 10, you can not change that, all the values in this array is interger.
You can use a[i] to access each value of it, and `i` start from 0 to 9.

```go
package main

import "fmt"

func main(){
    var a [10]int
    for i :=0; i < len(a); i++{
        a[i] = i
    }
    for _, value := range a{
        fmt.Println(value)
    }
}
```

We use `for` to initalize the arry, and use `for-range` to access each value.
The print results is:

```
0
1
2
3
4
5
6
7
8
9
```

### Array Constants

If we already known all the values in an array(or know some of them), you can use array constants, for example:

```go
var a = [4]int {1， 2, 3， 4}
```

or you can use:

```go
var a = [...]int {1, 2, 3, 4}
```

If you only some of the values in an array, you can use:

```
var a = [4]int {1: 1, 3:4}
```


```go
package main

import "fmt"

var a = [4]string {"a", "b", "c", "d"}

func main() {

    for i := range a {
        fmt.Println("Array item", i, "is", a[i])
    }
}
```

output is :

```
Array item 0 is a
Array item 1 is b
Array item 2 is c
Array item 3 is d
```

### Array Pointer

