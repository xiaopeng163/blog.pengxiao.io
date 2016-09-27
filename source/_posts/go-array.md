title: Go Array
date: 2014-04-10
categories:
- Go
tags:
- Go
---

![](/thumbnails/install-go-from-source/1.png)

source https://github.com/xiaopeng163/www.pengxiao.me/blob/master/source/_posts/go-array.md


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

Please see the example:

```go
package main

import "fmt"

func main() {
	a := [4]int{1, 2, 3, 4}
	fmt.Printf("the pointer is %p\n", &a)
	for i := 0; i < len(a); i++ {
		fmt.Println(a[i])
	}
	test(a)
}

func test(b [4]int) {
	fmt.Printf("the pointer is %p\n", &b)
	for i := range b {
		fmt.Println(b[i])
	}
}


```

The output is:

```
the pointer is 0xc0820065c0
1
2
3
4
the pointer is 0xc082006620
1
2
3
4
```

We can see that the array value is the same, but they have different addresses. The reason is because we give the value
to the function `test`, not the array's address, we can change to that:

```go
package main

import "fmt"

func main() {
	a := [4]int{1, 2, 3, 4}
	fmt.Printf("the pointer is %p\n", &a)
	for i := 0; i < len(a); i++ {
		fmt.Println(a[i])
	}
	test(&a)
}

func test(b *[4]int) {
	fmt.Printf("the pointer is %p\n", b)
	for i := range *b {
		fmt.Println(b[i])
	}
}
```

And the output is:

```
the pointer is 0xc0820045c0
1
2
3
4
the pointer is 0xc0820045c0
1
2
3
4
```

They have the same value and the same address

### Multidimensional Array

For example `[3][5]int`，`[2][2][2]string`

```go
package main

import "fmt"

func main() {
	const (
		X = 4
		Y = 6
	)

	var a [4][6]int

	for i := 0; i < X; i++ {
		for j := 0; j < Y; j++ {
			a[i][j] = i * j
		}
	}
	fmt.Println(a)
	fmt.Print(a[1][3])

}
```

The output is:

```
[[0 0 0 0 0 0] [0 1 2 3 4 5] [0 2 4 6 8 10] [0 3 6 9 12 15]]
3
```

Done!