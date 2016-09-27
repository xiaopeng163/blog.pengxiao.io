title: Go Function
date: 2014-04-25
categories:
- Go
tags:
- Go
---

![](/thumbnails/install-go-from-source/1.png)

source https://github.com/xiaopeng163/www.pengxiao.me/blob/master/source/_posts/go_function.md

### Basic

Just like the other languages, function is a functional code block.

Function may have parameters or return values.

```go
func function_name(arg1,arg2,...type)(re1,re2,...type){

}
```

For example:

```go
package main

import "fmt"

func main(){
    fmt.Print(test1(1, 2))
}

func test1(a, b int)(x, y int){
    return a + 1, b + 1
}
```

There are two interger parameters and two return values

```go
package main

import "fmt"

func main(){
    fmt.Print(Sum(1, 2, 3))
}


func Sum(a, b, c int) int {
    return a + b + c
}
```

There are three interger parameters and one return value.

If the number of parameter is unknow, you can use:

```go
package main

import "fmt"

func main(){
    fmt.Println(Sum(1, 2, 3, 4))
    fmt.Println(Sum(1, 2, 3, 4, 5))
}

func Sum(a ...int) int{
    result := 0
    for _, value := range a{
        result += value
    }
    return result
}

```


### Call by value or reference

The default way in Go is to pass a variable as an argument to a function by value: a copy is made
of that variable (and the data in it). The function works with and possibly changes the copy, the
original value is not changed.

For example:

```go
package main

import "fmt"

func main(){

    a := 1
    b := 2
    fmt.Println(a, b)
    test(a, b)
    fmt.Println(a, b)

}

func test(x, y int) {
    x += 1
    y += 1
    fmt.Println(x, y)
}
```

the output is:

```
1 2
2 3
1 2
```

We can see that the origin value does not change, this is call by value. If we want to change the value of a and b after running function test, we should
use call by reference, that means we will pass the addresses of a and b to function test, like:

```go
package main

import "fmt"

func main(){

    a := 1
    b := 2
    fmt.Println(a, b)
    call_by_value(a, b)
    fmt.Println(a, b)
    call_bye_reference(&a, &b)
    fmt.Println(a, b)

}

func call_by_value(x, y int) {
    x += 1
    y += 1
    fmt.Println(x, y)
}


func call_bye_reference(x, y *int){
    *x += 1
    *y += 1
    fmt.Println(*x, *y)
}
```

The output is:

```
1 2
2 3
1 2
2 3
2 3

```

Done!