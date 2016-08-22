title: TypeScript Helloworld
date: 2016-08-21
categories:
- TypeScript

tags:
- TypeScript
- JavaScript

---

## install

$ npm install -g typescript


## Helloworld

Create a file `helloworld.ts`

```
var hello : string = "Hello World"
console.log(hello)
```

use `tsc` command to compile typescript to JavaScript

```
$ tsc helloworld.ts
```

then you can find a new JavaScript file generated in the current folder

```
var hello = "Hello World!";
console.log(hello);

```
