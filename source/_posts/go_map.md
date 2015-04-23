title: Go Map
date: 2014-04-20
categories:
- Go
tags:
- Go
---

![](/thumbnails/install-go-from-source/1.png)

source https://github.com/xiaopeng163/www.pythoner.io/blob/master/source/_posts/go_map.md

### Basic

Map is a build-in Go data structure. If you know Python dictionary well, then it will be easy for you to use map.

We can use make to create an empty map.

```
make(map[key type] value type)
```

and we can use `name[key] = value` to set value of each key, use `name[key]` to get the value of the given key,
use `len` to get the length of this map, use `delete` to delete one key/value pair from map. 

```go
package main

import "fmt"

func main(){
    a := make(map[int]string)
    a[1] = "a"
    a[2] = "b"
    fmt.Println(a)
    fmt.Println(a[1])
    fmt.Println(len(a))
    delete(a, 1)
    fmt.Println(a)
    b := a[10]
    fmt.Println(b)
}

```
the output is:

```
map[1:a 2:b]
a
2
map[2:b]
false
```

What will happened if we want to get the value of the key which does not exist? it will return empty.

what should we do if the key exist and its value really is empty? we can use:

```go
_, b := a[10]
```

if the key does not exist, the `b` will be false.

### for range in map

We can use `range` to get each key and its value in a map.

```go
package main

import "fmt"

func main(){
    a := map[int]string{1: "a", 2: "b"}
    for i, j := range a {
        fmt.Printf("%d - > %s\n", i, j)
    }
}
```

output is:
```
1 - > a
2 - > b
```

Done!