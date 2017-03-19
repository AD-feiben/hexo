---
title: JavaScript 的学习总结（面向对象）
date: 2016-1-2 11:14:36
categories: 原创
tags: 
- 前端
---


![](http://upload-images.jianshu.io/upload_images/1917079-114550f960e709c6.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
##一、什么是面向对象
面向对象编程并不是像上图一样找个对象来一起编程，下面看一下来自维基百科对面向对象的介绍。
>面向对象程序设计（英语：Object-oriented programming，缩写：OOP）是种具有对象概念的程序编程范型，同时也是一种程序开发的方法。它可能包含数据、属性、代码与方法。对象则指的是类的实例。它将对象作为程序的基本单元，将程序和数据封装其中，以提高软件的重用性、灵活性和扩展性，对象里的程序可以访问及经常修改对象相关连的数据。在面向对象程序编程里，计算机程序会被设计成彼此相关的对象。



>重要的面向对象编程语言包含Common Lisp、Python、C++、Objective-C、Smalltalk、Delphi、Java、Swift、C#、Perl、Ruby 与 PHP等。

<!-- more -->

面向对象是把构成问题事务分解成各个对象，建立对象的目的不是为了完成一个步骤，而是为了描叙某个事物在整个解决问题的步骤中的行为。 
**面向对象是一种思维方法**
**面向对象是一种编程方法**
**面向对象并不只针对某一种编程语言**

面向对象的三大特征：
* 封装
  也就是把客观事物封装成抽象的类或具体的对象，并且类或对象可以把自己的数据和方法只让可信的类或者对象操作，对不可信的进行信息隐藏。

* 继承
  可以让某个类型的对象获得另一个类型的对象的属性的方

* 多态
  不同实例的相同方法在不同情形有不同表现形式。多态机制使具有不同内部结构的对象可以共享相同的外部接口。

面向对象的优点：
* 结构清晰，程序便于模块化，结构化，抽象化，更加符合人类的思维方式；
* 封装性，将事务高度抽象，从而便于流程中的行为分析，也便于操作和自省； 
* 容易扩展，代码重用率高，可继承，可覆盖；
* 实现简单，可有效地减少程序的维护工作量，软件开发效率高。

缺点是：
* 效率低，面向对象在面向过程的基础上高度抽象，从而和代码底层的直接交互非常少机会，从而不适合底层开发和游戏甚至多媒体开发；
* 复杂性，对于事务开发而言，事务本身是面向过程的，过度的封装导致事务本身的复杂性提高。

##二、JavaScript的面向对象
JavaScript的面向对象并不像Java的面向对象一样是基于类的面向对象。

先看一下在**Java**中是把对象的基本特征抽象出一个类，在这个类里面定义对象的属性以及方法，然后通过new className( )生成一个对象。比如把人抽象成一个类，人都有名字、年龄的属性，然后就可以在Person类里面定义两个私有变量用来存储对象的名字以及年龄,当然还有一些方法就不举例了。在new Person( )的时候把名字和年龄的值传递给构造函数(构造函数名与类名相同)，这样就可以生成一个对象了。代码如下：
```Java
public class Person { 

  private String name; 
  private int age; 

  public Person(String name, int age) 
  { 
    this.name = name;
    this.age = age; 
  } 
  //生成各属性的getter方法  
  public String getName() { 
    return name; 
  } 
  public int getAge() { 
    return age; 
  }
}
```

***
在**JavaScrip**t中（ES6之前）没有类的概念，那在JavaScript中怎么才能new出一个对象呢。JavaScript是基于原型的面向对象，在JavaScript中一切皆对象。就生成对象的方法就有很多种，下面就一一来介绍。
1. 用字面量的形式
```JavaScript
<script type="text/javascript">
    var person =  {
        name : "张三",
        age : "20",
        eat : function(){
          console.log("吃");
        }
    }
</script>
```
>name : "张三" 一个键值对表示JavaScript对象的一个属性。 name是属性名， "张三" 属性值。



  >属性可以是任意类型的。可以包括简单数据类型，也可以是函数，也可以是其他的对象。



  >当一个属性的值是函数的时候，我们更喜欢说这个属性为方法。 我们一般说person对象具有了一个方法eat. 将来访问eat的时候，也和调用一个函数一样一样的。

  ***
  接下来先看一下怎么访问对象的属性或方法。访问一个对象的属性，我们可以直接通过 **对象.属性名** 和 **对象[属性名]** 来访问。
```JavaScript
alert(person.name);  // 访问person对象的 name属性值
person.age = 30;  //修改person对象的 age 属性
person.eat();  //既然是调用方法(函数) 则一定还要添加 ()来表示方法的调用
alert(person["name"]);  //
```
>****两种使用方式有一些不同的地方：****
>对象.属性名的方式，只适合知道了属性的名字，可以直接写。比如： person.age 。如果属性名是个变量，则这种方法无效， 对象.变量名 会出现语法错误。



 >对象[属性名]，这种方式使用无限制。如果是字符串常量，则应该用""引起来，如果是变量，可以直接使用。

1. 使用new Object()创建
```JavaScript
<script type="text/javascript">
	//使用object创建一个对象	完全等同于 var person = {};
	var person = new Object();
	//给对象添加属性
	person.name = "李四";
  	//给对象添加方法
	person.eat = function () {
		alert("好好吃")
	}
</script>
```
1. 工厂模式创建
>虽然 Object 构造函数或对象字面量都可以用来创建单个对象，但这些方式有个明显的缺点：使用同一个接口创建很多对象，会产生大量的重复代码。为解决这个问题，人们开始使用工厂模式的一种变体。

```JavaScript
<script type="text/javascript">
	function createPerson(name, age, job) {
	    var o = new Object();
	    o.name = name;
	    o.age = age;
	    o.job = job;
	    o.sayName = function() {
	        alert(this.name);
	    };
	    return o;
	}
	var person1 = createPerson("张三", 29, "js开发者");
	var person2 = createPerson("李四", 27, "java开发者");
</script>
```

1. 构造函数模式创建
   为了解决对象类型识别问题，又提出了构造函数模式。这种模式，其实在我们创建一些原生对象的时候，比如Array、Object都是调用的他们的构造函数。
   看下面的代码
```JavaScript
<script type="text/javascript">
	function Person (name, age, sex) {
		this.name = name;
		this.age = age;
		this.sex = sex;
		this.eat = function () {
			alert(this.name + "在吃东西");
		}
	}
	var p1 = new Person("张三", 20, "男");
	p1.eat();	//张三在在吃东西
	var p1 = new Person("李四", 30, "男");
	p1.eat();	//李四在在吃东西
	alert(p1 instanceof Person);	//true
</script>
```
>使用构造函数创建对象，必须使用关键字new ，后面跟着构造函数的名，根据需要传入相应的参数。
>其实使用new 构造函数()的方式创建对象，经历了下面几个步骤。
  1. 创建出来一个新的对象
>1. 将构造函数的作用域赋给新对象。意味着这个时候 **this就代表了这个新对象**。
>2. 执行构造函数中的代码。 在本例中就是给新对象添加属性，并给属性初始化值。
>3. 构造函数执行完毕之后，默认返回新对象。 所以外面就可以拿到这个刚刚创建的新对象了。

1. 构造函数与普通函数的关系
* 他们都是函数。构造函数也是函数，也可以像普通的函数一样进行调用。 做普通函数调用的时候，因为没有创建新的对象，所以this其实指向了window对象。
* 构造函数和普通函数仅仅也仅仅是调用方式的不同。也就是说，随便一个函数你如果用new 的方式去使用，那么他就是一个构造函数。

    *  为了区别，如果一个函数想作为构造函数，作为国际惯例，最好把这个函数的首字母大写。

>如果文章对你有所帮助，那么请您点一下❤
>由于本人水平有限，如有错误，欢迎大家指正。如果你在操作过程中发现一些没有讲到的错误或者问题，欢迎在评论留言，一起探讨，共同学习进步！
