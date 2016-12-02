---
title: 利用GitHub搭建一个你的博客
date: 2016-11-28 08:48:59
tags: Git笔记
---
![Photo From Google](http://upload-images.jianshu.io/upload_images/1917079-bbc62e5e352027e8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>*为什么要写博客*
作为一只程序猿，踩到坑是一件非常正常的事，当我们踩到坑的时候就会花心思去研究它，可能我们能够在当时把问题弄懂并把问题给解决掉。可是过一段时间我们又遇到了同样的坑的时候，难道还要再去 百毒 Google 重新搜索一遍吗？这样做效率难免太低了，倒不如在第一次解决问题的时候就把解决方法写到我们的博客了，当我们再一次遇到相同的坑的时候翻一翻我们之前写的博客就能快速的把问题给解决掉，何乐而不为。而且我们学习新技术的时候也可以将当时学到的内容写到我们的博客，再次遇到的时候我们就可以找回当时学习的思路，继续学习。废话不多说，马上开始行动起来，搭建博客！

*声明：本文在Windows下进行操作的，Mac以及其它操作系统请做参考* **多图警告！**
<!--more-->
>如果你觉得太麻烦，欢迎 Fork/Star 我的 [GitHub](https://github.com/AD-feiben/hexo) 但是环境搭建以及怎么使用命令还是要看一看的。

# 1.环境搭建
首先需要下载两个东西
* node.js
* git

具体的下载，安装就不用多说了，基本上下载完默认安装即可，**安装的路径最好先记住。**Git 安装的时候会弹出下面的窗口，我们选择第二个即可。这样我们在Windows的命令窗口也可以进行Git操作了。
![Git Setup Detail](http://upload-images.jianshu.io/upload_images/1917079-d279f8f09a9d0587.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1200)两个都安装完了之后，打开命令窗口（按住Win+R后输入CMD即可打开命令窗口），分别输入 **node -v** 、**npm -v** 及  **git --version** 这三个命令是为了查看刚才我们安装的软件的版本，如果你能够看到他们的版本号（如同下图，也许版本号会有不同），那么恭喜你，环境搭建这一个大难关你已经过了，可以进入下一步骤了。
![Check Version](http://upload-images.jianshu.io/upload_images/1917079-2c0ae66c2886eedb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/840)
## 1.1有问题看这里
>打开我们刚才安装软件的路径，例如我的路径“D:\Program Files\nodejs”、“D:\Program Files\Git”。
复制我们刚才安装的路径,打开计算机>右键单击属性，选择高级系统设置>选择环境变量>双击 PATH >将我们安装的路径追加到变量值之后  *！注意分号以及确定保存*
这个时候再试一下 **node -v** 、**npm -v** 及  **git --version** 这三个命令，一般都不会有问题的了。

![PATH](http://upload-images.jianshu.io/upload_images/1917079-f5ebafd2c0256c5f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
# 2.配置 GitHub
## 2.1注册 GitHub
先到[GitHub](https://github.com/)官网Sign up(注册)一个账号。
![](http://upload-images.jianshu.io/upload_images/1917079-937b97e258d38e41.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)填好用户名、邮箱、密码进入下一步
![](http://upload-images.jianshu.io/upload_images/1917079-b03b792d91ab0671.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
## 2.2SSH授权
注册好账号之后我们可以随意的查看其他人的项目，甚至是clone下载，但是要提交代码就必须完成SSH授权，如果可以不用授权就提交代码的话，那么Github岂不是乱了套。
### 2.2.1生成SSH key
打开Git Bash，输入**ssh-keygen -t rsa**然后按三下回车，如下图所示
![](http://upload-images.jianshu.io/upload_images/1917079-79e0d9366df4b6d6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)接着就会在C:\Users\Administrator.ssh目录下生成到id_rsa和id_rsa.pub两个文件，id_rsa是密钥，id_rsa.pub是公钥，接下来需要将id_rsa.pub的内容添加到GitHub上，这样本地的id_rsa密钥才能跟GitHub上的id_rsa.pub公钥进行配对，才能够授权成功。
### 2.2.2在GitHub上添加SSH Key
首先点击右上角的倒三角进入Settings
![](http://upload-images.jianshu.io/upload_images/1917079-e3d0c7eea2332037.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)紧接着选择左侧SSH and GPG keys,然后选择右上角的New SSH key，再把id_sra.pub的内容复制粘贴到key（id_sra.pub可以使用 sublime 或者 记事本打开），最后Add SSH key就可以了。
![](http://upload-images.jianshu.io/upload_images/1917079-9082e2dc5100b5ed.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)SSH key 添加成功之后，输入 **ssh -T git@github.com **进行测试，如果出现以下提示证明添加成功了。
![](http://upload-images.jianshu.io/upload_images/1917079-d8426bee3c9c6214.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
>这一步一般没什么问题，有问题的话留言评论（顺便来一波打赏）就好了。直接进入下一步！

# 3.创建 GitHub 仓库

![](http://upload-images.jianshu.io/upload_images/1917079-112ad11f7974fd44.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![](http://upload-images.jianshu.io/upload_images/1917079-aaa2539f1226b466.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
>需要特别注意的是，项目名称一定要使用 **你的名字 + .github.io**
这一步也没什么问题，如果有问题，一定是你没有给我打赏(∩_∩)

# 4.设置本地博客的配置
## 4.1安装Hexo
在你认为合适的地方创建一个文件夹，然后在文件夹空白处按住 Shift+鼠标右键，然后点击在此处打开命令行窗口。*（同样要记住啦，下文中会使用在当前目录打开命令行来代指上述的操作）*
在命令行输入**npm install -g hexo**
![](http://upload-images.jianshu.io/upload_images/1917079-f3663d24b77c657f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)然后输入  **npm install hexo --save**  这时候你会看到命令窗口刷了一堆白字，然后输入  **hexo -v**  查看hexo是否安装成功了。
![](http://upload-images.jianshu.io/upload_images/1917079-2ee108b687f83785.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)如果出现与上图一样的情况的话，就说明你离成功就近在咫尺了。
## 4.2初始化Hexo
同样是在命令窗口中，继续输入 **hexo init**，等待下载好了之后输入 **hexo s**
![](http://upload-images.jianshu.io/upload_images/1917079-0ef349bf31778b1f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![](http://upload-images.jianshu.io/upload_images/1917079-e2459abaa5856b55.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)这时候我们就可以打开浏览器了，在地址栏中输入 **http://localhost:4000/** 我们就可以看到如下图的界面，这个就是我们的博客。没错，我们的博客就这样建好了。不过这个只是我们本地的博客，下面就要考虑怎么把我们的本地博客上传到我们的GitHub上了。
![](http://upload-images.jianshu.io/upload_images/1917079-76c91b2b5b635921.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
接下来先看一下我们的博客文章放在哪里。打开我们的文件夹下面的**source**文件夹，你会发现里面有一个**_posts**文件夹，再进入就会看到一片初始化的文章**hello-world.md**也就是上图显示在页面的文章。如果我们想新建文章的话，可以通过命令窗口输入**hexo new 'filename'**我们的文件夹下面就会生成一个新的md文件，然后我们打开编辑就可以了。
![](http://upload-images.jianshu.io/upload_images/1917079-3c1d6dc9e4fd0599.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 4.3发布博客
首先复制我们的GitHub项目地址，如下图。
![](http://upload-images.jianshu.io/upload_images/1917079-d3b4db2b572d72d8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)然后打开我们新建的文件夹下面生成的**_config.yml**文件，在最下方作如下修改。
![](http://upload-images.jianshu.io/upload_images/1917079-dd0cc5ee5ab8c286.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
deploy 是部署的意思，**type: git** 就是使用 git 进行部署，**repo: github仓库地址**
>注意：repo 原本是没有的，在最后自己加上就好。**冒号之后有一个空格 冒号之后有一个空格 冒号之后有一个空格**

接下来回到命令窗口，输入 **npm install hexo-deployer-git --save**
![](http://upload-images.jianshu.io/upload_images/1917079-ef7dd8222dc93fa9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)安装好Git上传插件之后，输入 **hexo g**，然后输入 **hexo d**就可以将我们的博客上传到我们的GitHub了，而且以后更新文章就只需要用这两个命令就可以了。这样别人也可以通过 [https://yourname.github.io](https://ad-feiben.github.io) 来访问我们的博客了（开头一定要用**https**，yourname是你的github的名字）。

# 5.个性化设置（更换主题）
有木有觉得这个博客的默认主题特别的丑，如果不觉得可以忽略这一步（哈哈）。
这里以我使用的主题为例。
第一步去找我们想要的主题，然后下载下来。我用的是next主题，在命令窗口输入**`git clone https://github.com/iissnan/hexo-theme-next themes/next`**
![](http://upload-images.jianshu.io/upload_images/1917079-1e544cdcfc69626f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![](http://upload-images.jianshu.io/upload_images/1917079-575bd78b61c2f0d7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)然后打开配置文件，找到 theme 将原来默认的 landscape 替换 next。然后在命令窗口输入 **hexo clean** 、**hexo g** 及 **hexo s**，先看一下本地博客是什么样子，确认好了在输入 **hexo d** 部署到GitHub
![](http://upload-images.jianshu.io/upload_images/1917079-ac979cf65087b7b3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
每一个主题都有一个使用文档，next的使用文档为 http://theme-next.iissnan.com/getting-started.html 我们可以为我们的主题修改名字，添加评论等等，具体的你们就自己去研究了。如果你们觉得太麻烦的话，欢迎大家直接 Fork 我的，地址为 https://github.com/AD-feiben/hexo 当然里面的也有大家要修改的地方。

>如果文章对你有所帮助，那么请您点一下❤
由于本人水平有限，如有错误，欢迎大家指正。如果你在操作过程中发现一些没有讲到的错误或者问题，欢迎在评论留言，一起探讨，共同学习进步！
**有钱的来波赞赏，没钱的来波Star**