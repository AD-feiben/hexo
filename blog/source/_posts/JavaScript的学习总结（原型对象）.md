---
title: JavaScript 的学习总结（原型对象）
date: 2016-1-8 19:54:39
categories: 原创
tags: 
- 前端
---

>上一篇文章中介绍了JS中的面向对象，如果不了解的话给你一个[传送门](/JavaScript的学习总结（面向对象）/)。这篇文章主要介绍JS的面向对象中继承的原理。

##一、什么是原型
JavaScript的继承就是基于原型的继承。所以想要学好JavaScript的面向对象就必须要学好JavaScript中的原型。

**1. 原型对象**
在JavaScript中，我们创建一个函数A(**就是声明一个函数**), 那么浏览器就会在内存中创建一个原型对象B，而且每个函数都默认会有一个属性 **prototype** 指向了这个对象( 即：**prototype的属性的值是这个对象** )。这个对象B就是函数A的原型对象，简称函数的原型。这个原型对象B 默认会有一个属性 **constructor** 指向了这个函数A ( 意思就是说：constructor属性的值是函数A )。

<!-- more -->

```JavaScript
<body>
    <script type="text/javascript">
    	/*
    		声明一个函数，则这个函数默认会有一个属性叫 prototype 。而且浏览器会自动按照一定的规则
    		创建一个对象，这个对象就是这个函数的原型对象，prototype属性指向这个原型对象。这个原型对象
    		有一个属性叫constructor 指向了这个函数
			
			注意：原型对象默认只有属性：constructor。其他都是从Object继承而来，暂且不用考虑。
		*/
	    function Person () {
	    	
	    }	    
    </script>
</body>
```
上面声明了Person函数之后，浏览器做下图一样的处理。
![](http://upload-images.jianshu.io/upload_images/1917079-f4594e21110975d5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**2. 使用构造函数创建对象**
上一篇文章中已经介绍过了怎么使用构造函数创建对象了。这里主要是介绍一下使用构造函数创建对象时的一些细节。

当把一个函数作为构造函数 (理论上任何函数都可以作为构造函数) 使用new创建对象的时候，那么这个对象就会存在一个默认的不可见的属性**[[prototype]]**，来指向了构造函数的原型对象。 **这个不可见的属性我们一般用"[[ ]]"来表示，只是这个属性没有办法直接访问到。**

直接上代码：
```JavaScript
<body>
    <script type="text/javascript">
	    function Person () {
	    	
	    }	
        /*
        	利用构造函数创建一个对象，则这个对象会自动添加一个不可见的属性 [[prototype]], 而且这个属性
        	指向了构造函数的原型对象。
        */
      	var p1 = new Person();
    </script>
</body>
```
这是的原理图就要变成下图了。
![](http://upload-images.jianshu.io/upload_images/1917079-9a37ffdf7ca5d852.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
* 从上面的图示中可以看到，创建p1对象虽然使用的是Person构造函数，但是对象创建出来之后，这个p1对象其实已经与Person构造函数没有任何关系了，p1对象的[[ prototype ]]属性指向的是Person构造函数的原型对象。

* 如果使用new Person()创建多个对象，则多个对象都会同时指向Person构造函数的原型对象。我们可以手动给这个原型对象添加属性和方法，那么p1,p2,p3...这些对象就会共享这些在原型中添加的属性和方法。

* 如果我们访问p1中的一个属性name，如果在p1对象中找到，则直接返回。如果p1对象中没有找到，则直接去p1对象的[[prototype]]属性指向的原型对象中查找，如果查找到则返回。(如果原型中也没有找到，则继续向上找原型的原型---原型链。 后面再讲)。

* 如果通过p1对象添加了一个属性name，则对p1对象来说就屏蔽了原型中的属性name。 换句话说：在p1中就没有办法访问到原型的属性name了。

* 通过p1对象只能读取原型中的属性name的值，而不能修改原型中的属性name的值。 p1.name = "李四"; 并不是修改了原型中的值，而是在p1对象中给添加了一个属性name。

```JavaScript
<script type="text/javascript">
	function Person() {}
	// 可以使用Person.prototype 直接访问到原型对象
	//给Person函数的原型对象中添加一个属性 name并且值是 "张三"
	Person.prototype.name = "张三";
	Person.prototype.age = 20;

	var p1 = new Person();
	/*
		访问p1对象的属性name，虽然在p1对象中我们并没有明确的添加属性name，但是
		p1的 [[prototype]] 属性指向的原型中有name属性，所以这个地方可以访问到属性name
		就值。
		注意：这个时候不能通过p1对象删除name属性，因为只能删除在p1中删除的对象。
	*/
	alert(p1.name); // 张三

	var p2 = new Person();
	alert(p2.name); // 张三  都是从原型中找到的，所以一样。

	alert(p1.name === p2.name); // true

	// 由于不能修改原型中的值，则这种方法就直接在p1中添加了一个新的属性name，然后在p1中无法再访问到
	//原型中的属性。
	p1.name = "李四";
	alert("p1：" + p1.name);
	// 由于p2中没有name属性，则对p2来说仍然是访问的原型中的属性。	
	alert("p2:" + p2.name); // 张三
</script>
```
这时候的原理图如下：
![](http://upload-images.jianshu.io/upload_images/1917079-bac46a4e9ef6bd1e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

##二、组合使用原型模型和构造函数模型创建对象
**1. 原型模型创建对象的缺陷** 
原型中的所有的属性都是共享的。也就是说，用同一个构造函数创建的对象去访问原型中的属性的时候，大家都是访问的同一个对象，如果一个对象对原型的属性进行了修改，则会反映到所有的对象上面。

但是在实际使用中，每个对象的属性一般是不同的。张三的姓名是张三，李四的姓名是李四。

​ **但是，这个共享特性对方法(属性值是函数的属性)又是非常合适的。**所有的对象共享方法是最佳状态。

**2. 使用构造函数模型创建对象的缺陷**
在构造函数中添加的属性和方法，每个对象都有自己独有的一份，大家不会共享。这个特性对属性比较合适，但是对方法又不太合适。因为对所有对象来说，他们的方法应该是一份就够了，没有必要每人一份，造成内存的浪费和性能的低下。

**3. 解决的方法**
```JavaScript
<script type="text/javascript">
	function Person() {
	    this.name = "李四";
	    this.age = 20;
	    this.eat = eat;
	}
  	function eat() {
	    alert("吃完东西");
    }
	var p1 = new Person();
	var p2 = new Person();
	//因为eat属性都是赋值的同一个函数，所以是true
	alert(p1.eat === p2.eat); //true
</script>
```
>但是上面的这种解决方法具有致命的缺陷：封装性太差。使用面向对象，目的之一就是封装代码，这个时候为了性能又要把代码抽出对象之外，这是反人类的设计。

**4. 使用组合模式解决上述两种缺陷**
原型模式适合封装方法，构造方法模式适合封装属性，综合两种模式的优点就有了组合模式。
```JavaScript
<script type="text/javascript">
	//在构造方法内部封装属性
	function Person(name, age) {
	    this.name = name;
	    this.age = age;
	}
	//在原型对象内封装方法
	Person.prototype.eat = function (food) {
		alert(this.name + "爱吃" + food);
	}
	Person.prototype.play = function (playName) {
		alert(this.name + "爱玩" + playName);
	}
    
	var p1 = new Person("李四", 20);
	var p2 = new Person("张三", 30);
	p1.eat("苹果");
	p2.eat("香蕉");
	p1.play("志玲");
	p2.play("凤姐");
</script>
```
组合模式，也并非完美无缺，有一点也是感觉不是很完美。把构造方法和原型分开写，总让人感觉不舒服，应该想办法把构造方法和原型封装在一起，所以就有了动态原型模式。

**5. 动态原型模式**
动态原型模式把所有的属性和方法都封装在构造方法中，而仅仅在需要的时候才去在构造方法中初始化原型，又保持了同时使用构造函数和原型的优点。
```JavaScript
<script type="text/javascript">
	//构造方法内部封装属性
	function Person(name, age) {
		//每个对象都添加自己的属性
	    this.name = name;
	    this.age = age;
	    /*
	    	判断this.eat这个属性是不是function，如果不是function则证明是第一次创建对象，
	    	则把这个funcion添加到原型中。
	    	如果是function，则代表原型中已经有了这个方法，则不需要再添加。
	    	perfect！完美解决了性能和代码的封装问题。
	    */
	    if(typeof this.eat !== "function"){
	    	Person.prototype.eat = function () {
	    		alert(this.name + " 在吃");
	    	}
	    }
	}
	var p1 = new Person("志玲", 40);
	p1.eat();	
</script>
```
建议以后使用动态原型模式。他解决了组合模式的封装不彻底的缺点。
>如果文章对你有所帮助，那么请您点一下❤
>由于本人水平有限，如有错误，欢迎大家指正。如果你在操作过程中发现一些没有讲到的错误或者问题，欢迎在评论留言，一起探讨，共同学习进步！
