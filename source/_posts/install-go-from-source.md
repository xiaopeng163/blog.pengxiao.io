title: Install Go from Source Code on Linux
date: 2014-04-03
categories:
- Go
tags:
- Go
---

![](/thumbnails/install-go-from-source/1.png)

source https://github.com/xiaopeng163/www.pythoner.io/blob/master/source/_posts/install-go-from-source.md

## Setup Environment

Try to install Go from source code, my test host is Ubuntu.

```bash
penxiao@pythoner:~$ lsb_release -a
No LSB modules are available.
Distributor ID:  Ubuntu
Description:    Ubuntu 14.04.1 LTS
Release:        14.04
Codename:       trusty
```

Because we need to use some tools to build Go, so we will install.

```
penxiao@pythoner:~$ sudo apt-get install bison ed gawk gcc libc6-dev make
```

Set go environments, add below to `.bashrc`

```
export GOROOT=$HOME/go
export PATH=$PATH:$GOROOT/bin
export GOPATH=$HOME/GoProjects
```

## Install from source

Download Go source and 

```
penxiao@pythoner:~$ wget https://storage.googleapis.com/golang/go1.4.2.src.tar.gz
penxiao@pythoner:~$ tar zxvf go1.4.2.src.tar.gz
penxiao@pythoner:~$ cd go/src/
penxiao@pythoner:~/go/src$ ./all.bash
```

and after you see this, congratulations.

```
Checking API compatibility.

Skipping cmd/api checks; hg not available

real    0m1.240s
user    0m0.525s
sys     0m0.161s

ALL TESTS PASSED

---
Installed Go for linux/amd64 in /home/penxiao/go
Installed commands in /home/penxiao/go/bin
penxiao@pythoner:~/go/src$ 
```

Check and test

```
penxiao@pythoner:~$ go version
go version go1.4.2 linux/amd64
penxiao@pythoner:~$ 
```


Go helloworld

```go
penxiao@pythoner:~/GoProjects/test$ more helloworld.go 
package main

func main() {
    println("Hello", "world")
}
penxiao@pythoner:~/GoProjects/test$ go run helloworld.go 
Hello world
penxiao@pythoner:~/GoProjects/test$ 
```


done!