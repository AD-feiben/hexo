---
title: 初识版本控制工具Git
date: 2016-11-22 9:39:38
tags: Git笔记
---

**Git介绍**

![](http://upload-images.jianshu.io/upload_images/1917079-a54bfdc8f9602dc2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

Git是目前最先进的分布式版本控制工具，学习Git对安卓开发有着十分重要的作用，特别是在团队开发。要想使用Git来管理代码，首先需要安装Git。如何安装git这里就不过多介绍了，自行google。[安卓开发工具下载](http://www.androiddevtools.cn/)

<!-- more -->

在Windows平台上Git是可以在图形界面上进行操作的，作为初学者还是通过命令来使用Git，这样就以后无论在哪个平台下都能熟练使用Git。

**配置Git**

安装完Git之后，打开Git Bash，首先配置一下自己的身份，这样在提交代码的时候Git就可以知道是谁提交的了。使用以下命令就可以配置自己的身份了。
```Git
git config --global user.name "your name"
git config --global user.email "your email"
```
配置完重新输入上面的命令，将引号以及引号的内容删掉，就可以查看是否配置成功。下图为配置成功之后的效果

![](http://upload-images.jianshu.io/upload_images/1917079-3c11af73ac63f9aa.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
配置成功之后，就来看看如何创建代码仓库。

**创建代码仓库**
- 使用cd命令进入需要创建仓库的项目下
- 使用git init命令完成代码仓库的创建，你没看错，一句命令即可搞定，就这么简单

在这里我使用一个以前的项目来创建仓库，仓库创建完成后会在项目根目录下生成一个隐藏的.git文件夹，利用ls -al命令就可以查看到该文件夹，效果如下

![](http://upload-images.jianshu.io/upload_images/1917079-e8dba0132ffb7811.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
仓库创建好了之后，就可以向仓库提交代码了。

**提交代码**

提交代码的命令同样十分简单，使用git add “文件名”/“文件夹名”即可向仓库中添加相应的文件或者文件夹下的所有文件。比如要向仓库提交AndroidManifest.xml文件，输入git add AndroidManifest.xml就可以了。再添加完相应的文件之后使用git commit -m “description”。**每次提交都需要通过-m加上描述信息，没有描述信息的提交会被认为是不合法的。** 

git add “文件夹名”虽然可以添加这个文件夹下的所有文件，对于要添加整个项目还是太过于麻烦。这时就可以利用git add .将整个项目添加到仓库中了。提交成功后效果如下

![](http://upload-images.jianshu.io/upload_images/1917079-3372b2e5cdca7e43.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
>如果添加文件后出现警告的话，重新执行一次git add命令就可以解决了







