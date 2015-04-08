title: Variables and Constants in Go
date: 2015-04-05
categories:
- Go
tags:
- Go
---

![](/thumbnails/install-go-from-source/1.png)

source https://github.com/xiaopeng163/www.pythoner.io/blob/master/source/_posts/go-variables-constants.md


## Constants Defination

A constant `const` contains data which does not change

It is defined as `const identifier [type] = value`, for example `const PORT int = 22`.

Constants can only be numbers, strings or booleans.

The type specifier [type] is optional, the compiler can implicitly derive the type from the value.

For example, 

```
const PORT int = 22
```

is equal as:

```
const PORT = 22
```

We can define many constants together through:

```
const (
	BGP_FSM_IDLE        = 1
	BGP_FSM_CONNECT     = 2
	BGP_FSM_ACTIVE      = 3
	BGP_FSM_OPENSENT    = 4
	BGP_FSM_OPENCONFIRM = 5
	BGP_FSM_ESTABLISHED = 6
)
```

We can use `iota` to simplify this defination:

```
const (
	_ = iota
	BGP_FSM_IDLE
	BGP_FSM_CONNECT
	BGP_FSM_ACTIVE
	BGP_FSM_OPENSENT
	BGP_FSM_OPENCONFIRM
	BGP_FSM_ESTABLISHED
)
```

The values of iota start at zero within each const block and increment by one each time it is seen. 
This can be combined with the constant shorthand (leaving out everything after the constant name) to very concisely define related constants.

Please reference http://golang.org/doc/go_spec.html#Iota




