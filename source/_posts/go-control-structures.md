title: Go Control Structure
date: 2015-04-06
categories:
- Go
tags:
- Go
---

![](/thumbnails/install-go-from-source/1.png)

source https://github.com/xiaopeng163/www.pythoner.io/blob/master/source/_posts/go-control-structures.md


## if else

The if else structure is like:

```go
if condition1 {
    // do something
} else if condition2 {
    // do something else
} else {
    // catch-all or default
}
```

for example:

```go
package main

import "fmt"

var flag bool = true


func main(){
    if flag {
        fmt.Print("flag is true")
    } else {
        fmt.Print("flag is false")
    }
}
```

## switch case

```go
package main

import "fmt"

func main(){
    fmt.Print(Season(12))
}


func Season(month int) string {
    switch month {
        case 12, 1, 2: return "winter"
        case 3, 4, 5: return "spring"
        case 6, 7, 8: return "summer"
        case 9, 10, 11: return "autumn"
    }
    return "unknown"
}
```

## for 

### based on counter

The general format is: `for init; condition; modif { }`

Example:

```go
package main

import "fmt"

func main() {

    for i := 0; i <= 10; i ++ {
        fmt.Println(i)
    }
}
```

```go
package main

import "fmt"

func main() {
    str := "HelloWorld"
    for i := 0; i < len(str); i++ {
        fmt.Println(str[i])
    }
}
```

### Condition-controlled iteration

Just like `while` in the other languages, the general format: `for condition { }`

```go
package main

import "fmt"

func main(){
    i := 10

    for i > 0{
        i -= 1
        fmt.Println(i)
    }
}

```

### Infinite loops

like in `for i:=0; ; i++` or `for { }` (or for ;; { } but the ; ; is
removed by gofmt): these are in fact infinite loops.

```go
package main

import "fmt"

func main(){

    i := 1
    for {
        if i < 10 {
            fmt.Println(i)
            i += 1
        } else {
            break
        }
    }

}

```

### The for-range

```
for pos, char := range str {
...
}
```

example:

```go
package main

import "fmt"

func main(){
    var str string = "HelloWorld"
    for pos, char := range str {
        fmt.Printf("The position is %d, the char is %c\n", pos, char)
    }
}

```

the output is:

```
The position is 0, the char is H
The position is 1, the char is e
The position is 2, the char is l
The position is 3, the char is l
The position is 4, the char is o
The position is 5, the char is W
The position is 6, the char is o
The position is 7, the char is r
The position is 8, the char is l
The position is 9, the char is d
```

## break and continue

for `break`:

```go
package main

import "fmt"

func main(){

    i := 1
    for {
        if i < 10 {
            fmt.Println(i)
            i += 1
        } else {
            break
        }
    }

}
```

for continue:

```go
package main

import "fmt"

func main(){

    for i:=1; i < 10; i ++ {
        if i == 5 {
            continue
        }
        fmt.Println(i)
    }
}
```

the output is:

```
1
2
3
4
6
7
8
9
```

## others

`lables` and `goto`

you can get some information from internet.

done!