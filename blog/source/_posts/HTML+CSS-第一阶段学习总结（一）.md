---
title: HTML+CSS-第一阶段学习总结（一）
date: 2015-6-27 13:46:38
categories: 原创
tags: 
- 前端
---
## 一、前端认知
**1. 前端是做什么的？**
前端是做 IT系统工程的，负责信息化系统的设计、建设，包括设备、系统、数据库、应用系统的建设。
**2. 开发流程**
![开发流程](http://upload-images.jianshu.io/upload_images/1917079-cd8f0030de994046.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
<!-- more -->
**3. 前端开发的核心语言**
* HTML——结构
* CSS——样式
* JS——行为

## 二、HTML
>HTML（Hypertext Markup Language）翻译过来就是 超文本标记语言。

超文本即超越文本，可以显示 文字 图片 视频 音频，最重要的是可以包含超链接。

标记语言：当我们把特定的英文单词放入到我们的标记（单标记:</>、双标记<></>）当中，我们的标记具有了新的语义,而由具有特定语义的标记的规范，我们可以称之为标记语言。

当我们将英语单词放入到标记当中，这时候我们可以称之为 标签（单标签、双标签）。

**HTML的基本结构**
```HTML
<!DOCTYPE html>
<!-- 
	文档头（类型）声明
		不是标签，代表的是 HTML 5 的标准
-->
<html>
<!--
	根元素
		所有的标签都要放在根元素中
-->
<head>
<!--
	头部
		里面包含的绝大部分内容都是不可见的，
		里面的内容主要用于辅助页面的展示
-->
	<title>页面标题</title><!-- 定义页面标题 -->
	<meta charset="utf-8"/><!-- 定义页面的元数据 -->
	<!-- chasrt="utf-8" 针对搜索引擎和解析格式的属性 -->
</head>
<body>
<!-- 
	里面包含的绝大部分在页面中都是可见的，
	主要用于搭建 HTML 结构
-->
</body>

</html>
```
## 三、CSS
>CSS(Cascading Style Sheets )翻译过来就是层叠样式表。
>CSS 就是用来装饰我们的 HTML 的。就像我用 HTML 写了一段文字，但是文字的颜色，文字大小，字体等等就得依靠 CSS 来修饰了。

**1. CSS 的引入方式**
* 内联样式表
```HTML
  <div style="width:100px;height:100px;background-color:red;"></div>
```
  ![内联样式表](http://upload-images.jianshu.io/upload_images/1917079-fd6773e850543844.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
* 内部样式表
    写在`<head></head>`内部
```HTML
<head>
        <style type="text/css">
          div{
            width: 100px;
            height: 100px;
            background: red;
          }
        </style>
</head>
```
![内部样式表](http://upload-images.jianshu.io/upload_images/1917079-09a7d068cb7defba.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
* 外部样式表
    单独写在一个文件中，通过 link 进行引入
    rel：当前引入文件和文件本身的关系
    type：当前引入文件的编码格式
    href：用于书写引入外部样式所处位置
```HTML
<head>
        <link rel="stylesheet" type="text/css" href="css/style.css">
</head>
```
![外部样式表](http://upload-images.jianshu.io/upload_images/1917079-8d284e4277675b22.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**2. CSS 选择器**
当我们使用内联样式的时候，CSS 样式可以明确的修改我们想要修改样式的标签。如果我们把 CSS 样式写在内部或者外部的时候，就需要通过 CSS 选择器来选出我们想要修改样式的标签。

CSS 选择器分为三种
1. 标签名选择器`div{width:100px;...}`
   会直接选择某一类标签，会针对这一类标签全部生效。
   优先级：1
2. 类选择器`.div{width:100px;...}`
   使用类选择器时，需要我们给标签写上类名，如`<div class="div"></div>`。类选择器会针对某一类具有相同类名的标签，同名 class 可以存在多个。
   优先级：10
3. ID 选择器`#div{width:100px;...}`
   使用 ID 选择器时，需要我们给标签写上 ID 名，如`<div id="div"></div>`。ID 选择器就会针对这一个 ID 名的标签，同名 ID 只能存在一个。
   优先级为：100

**3. 引入方式的优先级**
内联>内部 和 外部；
内部 和 外部 谁生效
* 如果选择器优先级相同的话，谁后引入谁生效。
* 如果选择器优先级不同，选择器优先级高的生效。

## 四、盒模型
>在 HTML 中，万物皆是盒模型，只要是 HTML 中的标签，我们都可以通过设置 盒模型 相关的内容，对这个元素产生影响。

盒模型由四个部分组成，分别为 content、padding、border以及margin。如下图所示。
![盒模型](http://upload-images.jianshu.io/upload_images/1917079-ea1bc800ea726f58.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
**1. content（盒子模型中的内容）**
下面通过一个 Demo 一起来学习 盒模型
```HTML
<!DOCTYPE html>
<html>
<head>
    <title>盒模型</title>
    <meta charset="utf-8"/>
    <style>
        div{
            width: 100px;
            height: 100px;
            background: red;
        }
    </style>
</head>
<body>
    <div></div>
</body>
</html>
```
![Demo](http://upload-images.jianshu.io/upload_images/1917079-29e5f99d9c6919b5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
通过上面的这一段代码，我们可以在页面中看到一个宽高分别为100px的红色块。从这里可以看出，盒模型中的content不仅可以设置宽高（内联元素除外）还可以加背景。（在浏览器中按F12即可打开如上图的开发者工具）

**1.1. background 的使用**
1.  background-color 设置背景颜色
         属性值即是颜色值有五种写法
    1. 英文 如 red,yellow,blue,cyan...
    2. 十六进制 如 #ffffff...
    3. 十六进制简写 如 #fff...
       1. rgb如	rgb(255,0,255)...
       2. rgba 如 rgba(255,255,255,0.5)...

2.  background-image 设置背景图片
        background-image=url("图片路径");

3.  background-position 设置背景定位
        background-position 有两个属性值，第一个一般为横向偏移量，第二个一般为纵向偏移量。如果只设置一个数值，另一个数值默认是 center。
        属性值可以用：left、right、top、bottom、center 以及 像素。

4.  backfround-repeat 设置背景的重复方式
    1. 默认值为 repeat 会将背景铺满整个盒模型。
    2. repeat-x 背景横向平铺。
    3. repeat-y 背景纵向平铺。
    4. no-repeat 不重复。

5.  background 的复合写法
        书写顺序为： 颜色、图片、定位、重复方式。

6.  background-attachment 设置背景图片是否随页面滚动
        background-attachment 的默认值为 scroll 即背景图片随着页面的滚动而滚动。也可以填写 fixed 即背景图片不会随着页面滚动，但是会造成偏移量，一般不使用。
        接下来稍微修改一下 Demo，看一下例子。
```HTML
<style>
    div{
        width: 70%;
		height: 1000px;
		background: red url("http://cdn-qn0.jianshu.io/assets/apple-touch-icons/
		            228-0765f118055a1d942fc286fb55f37773.png")center top no-repeat;
		background-attachment: fixed;
	}
</style>
```
![Demo.gif](http://upload-images.jianshu.io/upload_images/1917079-f20ca36a7d42a528.gif?imageMogr2/auto-orient/strip)
可以看到简书的背景图是固定不动的，将`background-attachment: fixed;`注释掉即可观察到偏移量了。

**2. padding（盒子模型中的内边距）**
还是用一个栗子来看看内边距到底是什么。
```HTML
<!DOCTYPE html>
<html>
<head>
    <title>盒模型</title>
    <meta charset="utf-8"/>
    <style>
        div{
            width: 100px;
            height: 100px;
            padding: 100px;
            background: red;
        }
    </style>
</head>
<body>
    <div></div>
</body>
</html>
```
![Demo](http://upload-images.jianshu.io/upload_images/1917079-f5dc5e967ae06471.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
当我的鼠标移到 div 标签上是，显示出来 div 的宽高是 300 x 300 。

这是因为我给 div 设置了 `width: 100px;` ` height: 100px;`所以 div 的宽高为 100 x 100，中间那块就是 100 x 100。

另外我给 div 设置了 `padding: 100px;`所以 div 的上右下左四个方向都有了 100px 的内边距，为图中外面一圈所示的，因为内边距是在盒子内部的，所以内边距会把我们的盒子撑大。

所以 div 的宽为：`padding-left + width+ padding-right = 100 + 100 + 100`。同理 div 的高为：`padding-top + height + padding-bottom = 100 + 100 + 100`。

从上面的式子中可以知道 padding 是分 上右下左 的，我们也可以给每一个边设置不同的宽度。例如 上右下左 分别为 10px 20px 30px 40px 就可以这样写。
```HTML
<style>
  div{
     padding-top: 10px;
     padding-right: 20px;
     padding-bottom: 30px;
     padding-left: 40px;
  }
</style>
```
但是这样写还是太过于麻烦了，还有一种更加简洁的写法。
```HTML
<style>
  div{
    padding: 10px 20px 30px 40px;
  }
</style>
```
这种写法同样能够实现 上右下左 分别为 10px 20px 30px 40px 的内边距。
当然，padding 的属性值可以填写 1 ~ 4 个不等，不同个数的属性值所控制的方向如下图所示。
![padding 属性值所对应的边](http://upload-images.jianshu.io/upload_images/1917079-c2781d043b273d3e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**3. border（盒子模型中的边框）**
在原来的基础上给 div 加上 border
```HTML
  <style>
    div{
      border: 5px solid blue;
    }
  </style>
```
![border](http://upload-images.jianshu.io/upload_images/1917079-2686dfc0f2011da6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
可以看到 border 也是算在盒模型之内的，border 同样会影响盒模型的大小。我用的是 border 的复合写法，其中包括 边框的宽度 、边框的样式、边框的颜色，我们也可以拆开来写。属性的写法见下图。
![border](http://upload-images.jianshu.io/upload_images/1917079-6af6dbc7ea740655.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
**4. margin（盒子模型中的外边距）**
margin 和 padding 比较相似，但是 margin 是在盒子之外的，用来控制盒子与盒子之间的位置。margin 可以设置负数。

*margin 有两种特殊情况*
* 如果两个元素是兄弟关系时，第一个元素的 margin-bottom 与 第二个元素的 margin-top 产生叠压
```HTML
<!DOCTYPE html>
<html>
<head>
    <title>盒模型</title>
    <meta charset="utf-8"/>
    <style>
    	div{
            width: 100px;
            height: 100px;

            margin: 100px;
    	}
        .div_1{
            background: red;
        }
        .div_2{
            background: blue;
        }
    </style>
</head>
<body>
    <div class="div_1"></div>
    <div class="div_2"></div>
</body>
</html>
```
![margin 的叠压](http://upload-images.jianshu.io/upload_images/1917079-81d11bfa0c3c0573.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
从代码上来看，红色块与蓝色块的距离应该是 红色块的 margin-bottom，加上蓝色块的 margin-top，但实际上是取盒子之间最大的 margin 值。*但是 margin-right 与 margin-left 就不是这样的情况。*

* 如果两个元素是父子关系，子级第一个元素的 margin-top 会传递给父级
```HTML
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<title>盒模型</title>
	<style>
		.div_1{
			width: 300px;
			height: 300px;
			background: cyan;
		}
		.div_2{
			width: 100px;
			height: 100px;
			background: red;
		}
	</style>
</head>
<body>
	<div class="div_1">
		<div class="div_2"></div>
	</div>
</body>
</html>
```
然后给 div_2 设置 `margin-top: 50px;`
![margin-top 的传递.png](http://upload-images.jianshu.io/upload_images/1917079-7d3de8eb0f5417c2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
这明明不是我想要的效果，那怎么才能够解决这个问题呢？下面一起来看一下解决办法。
  1. 给父级设置 border
  2. 给父级设置 padding
  3. 给父级设置 `overflow:"hidden";`

  办法已经告诉你们了，具体的还得你们自己去试。

  接下来看一下盒模型的实际大小的计算
横向：border-left + padding-left + width + padding-right + border-right
纵向：border-top + padding-top + height + padding-bottom + border-bottom

>如果文章对你有所帮助，那么请您点一下❤
>由于本人水平有限，如有错误，欢迎大家指正。如果你在操作过程中发现一些没有讲到的错误或者问题，欢迎在评论留言，一起探讨，共同学习进步！
