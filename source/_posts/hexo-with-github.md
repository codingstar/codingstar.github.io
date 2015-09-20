title: 用Github Pages搭建Hexo个人博客
date: 2015-05-28 09:46:51
tags: [Github,Hexo,技术]
---

Hexo是一种静态博客程序，初次使用，具体效果还不确定，但据说很好用，于是就来试试啦~

搭建过程很简单

## 安装基本工具

首先需要安装nodejs、npm、git，这个之前都用到过，具体安装配置方法就不赘述了。

### Github相关

注册Github账号，添加ssh公钥等基本配置。然后新建一个仓库，*一定*要命名为`username.github.io`，`username`是你Github的账号。
<!--more-->
## 安装Hexo

我现在安装的是hexo3.1+，从版本3往上有些地方跟以前用法不同，按以前的资料操作可能会出现问题，因此查阅资料的时候注意下别人用的版本~

可以用Git Bash或终端输入以下命令，#后的为注释。
``` bash
    $ npm install hexo -g #全局安装hexo，有些资料写的是npm install hexo-cli -g，应该按这个来，不过没试过
    $ npm install hexo-deployer-git --save #安装git部署插件，hexo3.0开始必须要这样，不然无法部署
```

cd到你想放博客的文件夹下，比如`E:/Hexo`
``` bash
    $ hexo init #初始化，然后该文件夹下就会有一系列的文件
    $ npm install #安装依赖包，这步之前忘记过
```

然后就可以本地查看你的博客啦~
``` bash
    $ hexo g #即编译，hexo generate
    $ hexo s #即启动服务器，hexo server
```
查看`http://localhost:4000/`就可以访问到你的博客啦~

## 部署到Github
编辑`E:/Hexo`下的`_config.yml`文件，在文件最下方修改`deploy:`部分为
``` bash
    deploy: 
      type: git
      repository: git@github.com:codingstar/codingstar.github.io.git
      branch: master
```
其中repository部分填写自己的远程仓库的git地址。hexo3.0以上的type应为git而不是github。

同时在这个文件开头，也可以修改自己博客的名字、作者等信息，或者绑定已有域名。

修改完`_config.yml`文件后，我们可以通过下面的命令部署到Github
``` bash
    $ hexo g
    $ hexo d #等同于hexo deploy
```
至此，你可以访问`http://codingstar.github.io/`查看你的博客啦~

## Hexo主题
安装Hexo的主题很简单，只要打开`E:\Hexo\themes\`，然后在这里clone你想要的主题，比如我现在用的主题：
``` bash
    $ git clone https://github.com/litten/hexo-theme-yilia.git
    $ cd hexo-theme-yilia
    $ npm install
```
然后在`E:\Hexo\_config.yml`中的theme项改为你的主题的文件夹名字即可。

## 发表一个文章
1、在终端执行 `$ hexo new "my new post"`
2、在`E:\Hexo\source\_post`下打开my-new-post.md文件，参考[markdown 简明语法](http://lutaf.com/markdown-simple-usage.htm)，[Markdown 语法说明 (简体中文版)](http://wowubuntu.com/markdown/#html)，很快就能学会~

## 参考资料
1、[使用Hexo搭建个人博客(基于hexo3.0)](http://opiece.me/2015/04/09/hexo-guide/)
2、[升级hexo3遇到的坑](http://ningban.github.io/2015/03/19/hexo3-is-awesome/)
3、[Hexo搭建Github静态博客](http://www.cnblogs.com/zhcncn/p/4097881.html)
4、[如何搭建一个独立博客——简明Github Pages与Hexo教程](http://cnfeat.com/2014/05/10/2014-05-11-how-to-build-a-blog/)