title: Go Slice
date: 2014-04-15
categories:
- Go
tags:
- Go
---

![](/thumbnails/install-go-from-source/1.png)

source https://github.com/xiaopeng163/www.pythoner.io/blob/master/source/_posts/go-slice.md

A slice is a reference to a contiguous segment(section) of an array. This section can be the entire array, or a subset of the
items indicated by a start- and an end index.


### Basic defination

The format of the declaration is: `var identifier []type` no length is needed.

For example, there is an array:

```go
package main

import "fmt"

func main(){

    a := []int{1,2,3,4,5}
    b := a[0:3]
    fmt.Println(b)

}
```

We can use `len()` to get the length of the slice and `cap()` to get the capacity of this slice which actualy is the length of the array.

```go
package main

import "fmt"

func main(){
    
    a := []int{1,2,3,4,5}
    fmt.Println(a)
    fmt.Println(len(a))
    b := a[0:3]
    fmt.Println(len(b))
    fmt.Println(cap(b))
    fmt.Println(b)

}
```

The output is:

```
[1 2 3 4 5]
5
3
5
[1 2 3]

```

We can see that the length of the slice is 3, but the capacity is 5, so we can increase the slice size within the range of the origin array.

```go
package main

import "fmt"

func main(){

    a := []int{1,2,3,4,5}
    fmt.Println(a)
    b := a[0:3]
    fmt.Println(b)

    // increase the size of the slice
    fmt.Println(b[0:4])
    fmt.Println(b[0:5])

    // out of the capacity, will raise error:
    // panic: runtime error: slice bounds out of range
    fmt.Println(b[0:6])

}
```


### use make

If the array which slice referenced by hasn't defined yet, we can then make the slice together with the array
using the `make( )` function: `var slice1 []type = make([]type, len)`
which can be shortened to: slice1 := make([]type, len)
where len is the length of the array and also the initial length of the slice.

For example:

```go
package main

import "fmt"

func main(){

    var slice1 []int = make([]int, 10)
    fmt.Println(slice1)
    fmt.Println(len(slice1))
    fmt.Println(cap(slice1))
}
```

The output is:

```
[0 0 0 0 0 0 0 0 0 0]
10
10
```

So you can see the capacity and the length are the same, if you want them different, you can use:

```go
slice1 := make([]type, len, cap)
```

The len<=cap. For example:

```go
package main

import "fmt"

func main(){

    var slice1 []int = make([]int, 5, 10)
    fmt.Println(slice1)
    fmt.Println(len(slice1))
    fmt.Println(cap(slice1))
}
```

The output is:
```
[0 0 0 0 0]
5
10
```

### Reslice

```go
package main

import "fmt"

func main(){

    var a []byte = []byte{'a', 'b', 'c', 'd', 'e'}
    fmt.Println(string(a))
    slice1 := a[1:4]
    fmt.Println(string(slice1))
    slice2 := slice1[1:4]
    fmt.Println(string(slice2))
}

```

### Append

```go
package main

import "fmt"

func main(){

    a := make([]int, 5, 10)
    fmt.Printf("%p\n", a)
    fmt.Println(a)
    fmt.Println(len(a))
    fmt.Println(cap(a))
    a = append(a, 6, 7, 8, 9, 10)
    fmt.Printf("%p\n", a)
    fmt.Println(a)
    fmt.Println(len(a))
    fmt.Println(cap(a))
    a = append(a, 11, 12)
    fmt.Printf("%p\n", a)
    fmt.Println(a)
    fmt.Println(len(a))
    fmt.Println(cap(a))
}
```

The output is:

```
0xc08200c1e0
[0 0 0 0 0]
5
10
0xc08200c1e0
[0 0 0 0 0 6 7 8 9 10]
10
10
0xc082050000
[0 0 0 0 0 6 7 8 9 10 11 12]
12
20
```

We can see that if the length of the slice exceed its capacity, the slice will have a new memery address and bigger capacity(double size).

### Copy

```
package main

import "fmt"

func main(){
    s1 := []int{1, 2, 3, 4}
    s2 := []int{5, 6}
    copy(s1, s2)
    fmt.Print(s1)
}
```

the output is:

```
[1 2 5 4]
```

Done!