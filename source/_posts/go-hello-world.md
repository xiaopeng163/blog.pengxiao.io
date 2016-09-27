title: Hello World for Go
date: 2014-04-04
categories:
- Go
tags:
- Go
---

![](/thumbnails/install-go-from-source/1.png)

source https://github.com/xiaopeng163/www.pengxiao.me/blob/master/source/_posts/go-hello-world.md


## Hello World

```go
$ more helloworld.go 
package main

import "fmt" // https://golang.org/pkg/fmt/

/* main function
print something */
func main() {
    fmt.Print("Hello World! or 你好，世界!\n")
}
```

* The first line is required, all go source file must start with `package xxx`, the purpose is you maybe want to use
this file's content in other go files, if you want to run this file diectly, the package name must be `main`

* The sencond line imports fmt to main, just like other languages `from xxx import xx` or `include xxx.h`. At the end of 
the line is comment which is started with `//`, this is a one line comment

* The third line also is comment, but this comment is multi-line comment, enclosed in `/* */`.

* The fourth line is function defination. `main` function is the first function go program will call.


## Compiling and running code

You can directly run the source code like:

```
$ go run helloworld.go 
Hello World! or 你好，世界!
```

Or you can complie the source code into executable file:

```
$ go build helloworld.go 
$ ls
helloworld  helloworld.go
```

Then you can run this file:

```
$ ./helloworld 
Hello World! or 你好，世界!
```

Done!
