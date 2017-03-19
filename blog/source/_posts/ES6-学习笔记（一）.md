---
title: ES6 学习笔记（一）
date: 2016-03-19 14:50:39
categories: 学习笔记
tags:
- 前端
- JavaScript
---

## ES6 的简介

<img src="https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1489920666226&di=e1349cd2bb01dd7c6e407645b9b26dd4&imgtype=0&src=http%3A%2F%2Fimage98.360doc.com%2FDownloadImg%2F2016%2F08%2F0421%2F77224158_1.jpg" class="full-image" alt ="ES6" />

> ECMAScript 6.0 (简称 ES6) 已经在2015年6月正式发布了。ES6 的目标是使得 JavaScript 语言可以用来编写复杂的大型应用程序，成为企业级开发语言。

<!-- more -->
> 本文参考阮一峰老师的 [ECMAScript 6 入门](http://es6.ruanyifeng.com/)

## let 和 const

### 1. let 命令
#### 基本用法
   ES6 新增了 let 命令，用来声明变量。let 的用法类似于 var，但是 let 声明的变量不存在声明提前，而且 let 声明的变量只能在代码块内有效。例子如下：
```JavaScript
   console.log("声明提前的 a :", a); // ==> 声明提前的 a : undefined
   // console.log("b 声明不会提前" ,b); // ==> Uncaught ReferenceError: b is not defined

   {
   	var a = "var a";
   	let b = "let b";
   		
   	console.log("代码块里的 a :", a); // ==> 代码块里的 a : var a
   	console.log("代码块里的 b :" ,b); // ==> 代码块里的 b : let b
   }

   console.log("代码块外面的 a :", a); // ==> 代码块外面的 a : var a
   console.log("代码块外面的 b :" ,b); // ==> Uncaught ReferenceError: b is not defined
```
   > 从上面的例子就可以知道 let 声明的变量只能在变量所在的代码块中调用，而且只能在变量声明之后调用。let 声明的变量特别适合充当个 fori 循环中声明的条件变量 i， 因为我们只需要在 for 循环中使用变量 i，一旦循环结束，就把变量 i 销毁。

   let 还可以用来解决闭包的问题，在 es5 中我们经常会遇到以下的情况

```JavaScript
	var fns = [];
	for (var i = 0; i < 3; i++){
		fns[i] = function (){
  			console.log(i);
		}
	}
	fns[0](); // ==> 3
	fns[1](); // ==> 3
	fns[2](); // ==> 3
```

   上面的代码就是传说中的闭包，关于闭包就不在这里过多讲解了。上面的代码的逻辑想要实现的效果是 fns[1] ==> 1 ... 结果应为闭包的问题导致不管执行哪个函数，输出的结果都是 i 的最终值。在 es5 中解决闭包的办法有很多种，但是为了实现我们的逻辑，往往要写很多代码。有了 let 之后，我们就可以更加优雅地解决这类问题了。我们只需要将 var 换成 let 即可。

```JavaScript
	let fns = [];
	for (let i = 0; i < 3; i++){
		fns[i] = function (){
  			console.log(i);
		}
	}
	fns[0](); // ==> 0
	fns[1](); // ==> 1
	fns[2](); // ==> 2
```

>上面代码中，变量 i 是 let 声明的，当前的 i 只在本轮循环有效，所以每一次循环的 i 其实都是一个新的变量

   for 循环还是一个特殊的地方，就是循环语句部分是一个父作用域，而循环体内部是一个单独的子作用域。
```JavaScript
   for (let i = 0; i < 3; i++) {
    let i = 'abc';
    console.log(i);
  }
```
>上面代码输出了 3 次 abc，这表明函数内部的变量 i 和外部的变量 i 是分离的。

####　暂存性死区（temporal dead zone，简称 TDZ）
只要在块级作用域内存在 let 命令，那么在变量声明之前，该变量都是不可以使用的。
```JavaScript
if(true){
  // TDZ 开始
  tmp = "abc"; // ReferenceError
  console.log(tmp); // ReferenceError
  
  let tmp; // TDZ 结束
  console.log(tmp); // undefined
  
  tmp = 123;
  console.log(tmp); // 123
}
```

*暂存性死区*就意味着 typeof 不再是一个百分百安全的操作。
```JavaScript
typeof x; // ReferenceError
let x;
```

#### 不允许重复声明
在 es5 中，var 声明的变量是允许我们进行重复声明的，但是在 es6 的 let 中不允许在相同作用域内，重复声明同一个变量
```JavaScript
// 报错
function () {
  let a = 10;
  var a = 1;
}

function func(arg) {
  let arg; // 报错
}

function func(arg) {
  {
    let arg; // 不报错
  }
}
```


### 2. const 命令
#### 基本用法
const 声明一个只读的常量。一旦声明，常量的值就不能改变。
```JavaScript
const PI = 3.1415;
PI // 3.1415

PI = 3;
// TypeError: Assignment to constant variable.
```
const 声明的变量不得改变值，这意味着，const 一旦声明变量，就必须立即初始化，不能留到以后赋值。

const 的作用域与 let 命令相同：只在声明所在的块级作用域内有效。
```JavaScript
if (true) {
  const MAX = 5;
}

MAX // Uncaught ReferenceError: MAX is not defined
```

const 命令声明的常量也是不提升，同样存在暂时性死区，只能在声明的位置后面使用。

const 声明的常量，也与 let 一样不可重复声明。

#### 本质
const 实际上保证的，并不是变量的值不得改动，而是变量指向的那个内存地址不得改动。对于简单类型的数据（数值、字符串、布尔值），值就保存在变量指向的那个内存地址，因此等同于常量。但对于复合类型的数据（主要是对象和数组），变量指向的内存地址，保存的只是一个指针，const 只能保证这个指针是固定的，至于它指向的数据结构是不是可变的，就完全不能控制了。因此，将一个对象声明为常量必须非常小心。
```JavaScript
const foo = {};

// 为 foo 添加一个属性，可以成功
foo.prop = 123;
foo.prop // 123

// 将 foo 指向另一个对象，就会报错
foo = {}; // TypeError: "foo" is read-only
```
