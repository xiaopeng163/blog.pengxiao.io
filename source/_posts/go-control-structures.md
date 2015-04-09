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

```
if condition1 {
    // do something
} else if condition2 {
    // do something else
} else {
    // catch-all or default
}
```

for example:

```
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

## select

## for 

## others

