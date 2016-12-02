---
title: 使用Git向GitHub上传代码
date: 2016-11-22 9:39:38
tags: Git笔记
---

如果你对Git还一无所知，建议你先看一下[初识版本控制工具Git](http://www.jianshu.com/p/7e5ca3ca6e07)，对Git有一定的了解后再来看这篇文章。如果你对Git有一定的了解并且已经配置好SSH key，只是想了解如何将代码上传到GitHub，那么你可以跳过前面部分到**提交代码**部分查看。

<!-- more -->

![GitHub](http://upload-images.jianshu.io/upload_images/1917079-57c799d0c0b073fc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 什么是GitHub
[GitHub](https://github.com/)是一个面向开源及私有软件项目的托管平台，因为只支持Git作为唯一的版本库格式进行托管，故名GitHub。

- 为什么要使用Github
GitHub是目前全球最大的开源社区，全球各大科技公司纷纷在GitHub开源各自的项目，这无疑是我们学习先进技术的好地方。
>[Google](https://github.com/google)
>[苹果](https://github.com/apple)
>[twitter](https://github.com/twitter)
>[Facebook](https://github.com/facebook)
>……

- 注册GitHub账号
1.先到[GitHub](https://github.com/)官网Sign up(注册)一个账号。

![](http://upload-images.jianshu.io/upload_images/1917079-252ddb2ad7830f39.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

填好用户名、邮箱、密码进入下一步

![](http://upload-images.jianshu.io/upload_images/1917079-8c01ebd47cde02c5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
使用默认的plan即免费的，公开的就可以了，就是创建的项目是对外开放的，任何人都可以看的。点击Finish sign up就完成注册了。接下来就看看怎么向GitHub提交我们的代码。


**SSH授权**
注册好账号之后我们可以随意的查看其他人的项目，甚至是clone下载，但是要提交代码就必须完成SSH授权，如果可以不用授权就提交代码的话，那么Github岂不是乱了套。

1.生成SSH key
打开Git Bash，输入ssh-keygen -t rsa然后按三下回车，如下图所示
![](http://upload-images.jianshu.io/upload_images/1917079-1caf3ee12f72f69b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)接着就会在C:\Users\Administrator\.ssh目录下生成到id_rsa和id_rsa.pub两个文件，id_rsa是密钥，id_rsa.pub是公钥，接下来需要将id_rsa.pub的内容添加到GitHub上，这样本地的id_rsa密钥才能跟GitHub上的id_rsa.pub公钥进行配对，才能够授权成功。

2.在GitHub上添加SSH Key

首先点击右上角的倒三角进入Settings
![](http://upload-images.jianshu.io/upload_images/1917079-dce901df90f31d14.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
紧接着选择左侧SSH and GPG keys,然后选择右上角的New SSH key，再把id_sra.pub的内容复制粘贴到key（id_sra.pub可以使用记事本打开），最后Add SSH key就可以了。

![](http://upload-images.jianshu.io/upload_images/1917079-5b17cabfa308069e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
SSH key 添加成功之后，输入 **ssh -T git@github.com **进行测试，如果出现以下提示证明添加成功了。

![](http://upload-images.jianshu.io/upload_images/1917079-db1b3c8eb8f879c7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

---

**提交代码**
首先在Github新建一个仓库，回到首页，点击右上角的New repository新建仓库。

![](http://upload-images.jianshu.io/upload_images/1917079-dd563b3b85ad32c0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
接下来输入仓库名称，然后创建仓库就可以了。
![](http://upload-images.jianshu.io/upload_images/1917079-ed40de469d3dff71.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
仓库创建好了之后，按右侧按钮复制SSH地址。
![](http://upload-images.jianshu.io/upload_images/1917079-b020e0be28249d1f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
一切准备就绪，接下来就是Git的事了，首先进入想要上传到GitHub的项目的文件夹下，创建好本地仓库，将想要上传的文件先添加到本地仓库中。
![](http://upload-images.jianshu.io/upload_images/1917079-45ee27a5f6184f83.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
接下来使用git remote add origin git@github.com:InstanceFeiben/Test.git（git@github.com:InstanceFeiben/Test.git为SSH地址，在GitHub上复制）命令将本地仓库与远程仓库取得关联，最后在通过git push -u origin master命令将代码push到GitHub。
![](http://upload-images.jianshu.io/upload_images/1917079-b358f81cbda4dbc3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
接下来刷新GitHub就可以看到刚刚提交上去的代码了。

![](http://upload-images.jianshu.io/upload_images/1917079-b2d63a6d20adb784.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**解决问题**
如果出现以下问题，可以先使用git pull origin master命令后再使用git push -u origin master命令。
![](http://upload-images.jianshu.io/upload_images/1917079-7bbe89b85b1bcdaa.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



>如果文章对你有所帮助，那么请您点一下♥
由于本人水平有限，如有错误，欢迎大家指正。如果你在操作过程中发现一些没有讲到的错误或者问题，欢迎在评论留言，一起探讨，共同学习进步！
