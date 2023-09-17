---
title: 使用Hexo+Github搭建个人博客
tags:
  hexo
categories: 随笔
date: 2022-04-17 23:22:33
---

![img](/images/220410112400.webp)
<!--more-->
## 准备工作
开始之前,先大致介绍一下用到的技术和相关概念:

### Github
GitHub是一个利用Git进行版本控制、专门用于存放软件代码与内容的共享虚拟主机服务;

### Github Pages
Github Pages设计的初衷是为托管在GitHub上的项目提供介绍页面,开发者们可以通过GitHub Pages为他们的每个项目创建一个用于介绍该项目的静态网站,不过由于他的空间免费而且稳定,因此非常适合用来搭建个人博客网站;

### Git
Git是一个开源的分布式版本控制系统,可以有效、高速的处理从很小到非常大的项目版本管理.它的作用和Svn类似,就是一个版本控制的工具,用它可以将我们写的代码提交到Github;

### Jekyll
jekyll是一个简单的免费的Blog生成工具,将纯文本转化为静态网站和博客;由于咱们的GitHub Pages生成的是静态页面,每次更新博客都需要手动更改HTML,这就使得每次写博客都变得很麻烦,而用了这个工具以后,它会根据预先设置好的格式来生成博客内容,你就无需关心html代码,只需要把重心放在博客的写作上;

## 安装工具
### Node.js
安装
``` bash
    $ brew install node
```
等待安装完成后，查看版本
``` bash
    $ node -v
    v12.17.0
    $ npm -v
    6.14.4
```

### Git
安装
``` bash
    $ brew install git
```
等待安装完成后，查看版本
``` bash
    $ git --version
    git version 2.24.3 (Apple Git-128)
```

### Hexo
安装脚手架
``` bash
    $ npm install hexo-cli -g
```
等待安装完成后，查看版本
``` bash
    $ hexo -v
    hexo-cli: 4.3.0
```
到这一步如果所有都安装成功，并测试无问题，那么本地配置已经搞定，下面开始在github上建项目了。

##  搭建博客

### 注册Github账号(已有账号可忽略)
在[Github官网](https://github.com)中注册账号，如果不懂注册可以网上搜索下注册账号的文章，或者查看[Github注册(图文详解)](https://blog.csdn.net/weixin_51674304/article/details/121525251)

### 新建仓库
在[Github官网](https://github.com)登录成功后，如下图新建仓库
![img](/images/220410124403.png)
![img](/images/220410130015.png)
**再次强调：**Repository name就是你的仓库名,这个仓库名最好使用"你的名字.github.io"这种格式来命名，方便后面访问。

### 生成SSH Key
#### 原理
SSH是一个专为远程登录会话和其他网络服务提供安全性的协议。默认状态下SSH链接是需要密码认证的，可以通过添加系统认证（即公钥-私钥）的修改，修改后系统间切换可以避免密码输入和SSH认证。

对信息的加密和解密采用不同的key，这对key分别称作private key和public key，其中，public key存放在欲登录的服务器上，而private key为特定的客户机所持有。

当客户机向服务器发出建立安全连接的请求时，首先发送自己的public key，如果这个public key是被服务器所允许的，服务器就发送一个经过public key加密的随机数据给客户机，这个数据只能通过private key解密，客户机将解密后的信息发还给服务器，服务器验证正确后即确认客户机是可信任的，从而建立起一条安全的信息通道。

通过这种方式，客户机不需要向外发送自己的身份标志“private key”即可达到校验的目的，并且private key是不能通过public key反向推断出来的。这避免了网络窃听可能造成的密码泄露。客户机需要小心的保存自己的private key，以免被其他人窃取，一旦这样的事情发生，就需要各服务器更换受信的public key列表。

#### 作用
需要使用SSH将GitHub与本地进行绑定，配置好SSH密钥后，我们便可以通过git操作实现本地代码库与Github代码库同步。

#### 操作
##### 生成SSH Key
``` bash
$ ssh-keygen -t rsa -C "email@example.com"
//引号里面填写你的注册Github时的邮箱地址，比如xxxxxxxx@gmail.com
```
【第一次回车】之后会出现：
``` bash
Generating public/private rsa key pair.
Enter file in which to save the key (~/.ssh/id_rsa):
//到这里可以直接回车将密钥按默认文件进行存储
```
【第二次回车】然后会出现：
``` bash
Enter passphrase (empty for no passphrase):
//这里是要你输入密码，其实不需要输什么密码，直接回车就行
Enter same passphrase again:
//再回车
```
【第三次回车、第四次回车】接下来屏幕会显示：
``` bash
Your identification has been saved in ~/.ssh/id_rsa.
Your public key has been saved in ~/.ssh/id_rsa.pub.
The key fingerprint is:
//这里是各种字母数字组成的字符串，结尾是你的邮箱
The key's randomart image is:
//这里也是各种字母数字符号组成的字符串
```
到这里就说明SSH Key生成成功了;

##### 获取SSH Key
``` bash
pbcopy < ~/.ssh/id_rsa.pub
```
通过上面的命令，刚才生成的ssh key公钥会被复制到mac电脑的剪切板中。

### 添加SSH Key到Github
#### 登录Github，进入Setting:
![img](/images/220410134523.png)

#### 点击 SSH and GPG Keys
![img](/images/220410134856.png)

#### 点击 New SSH Key
![img](/images/220410135134.png)

#### 添加 SSH Key
![img](/images/220410141007.png)
然后点击 ADD SSH Key。

#### 测试SSH是否绑定成功
输入以下命令：注意：git@github.com不要做任何更改！
``` bash
$ ssh -T git@github.com
``` 
回车，再次输入yes后回车，就会显示：
``` bash
Hi BaiYuQiu! You've successfully authenticated, but GitHub does not provide shell access.
```

#### 本地配置个人Github账号
Git 会根据用户的名字和邮箱来记录提交，GitHub 也是用这些信息来做权限的处理，输入以下命令进行个人信息的设置，把名称和邮箱替换成自己的，名字可以不是GitHub的昵称，但为了方便记忆，建议与GitHub一致。
``` bash
$ git config --global user.name "此处填你的用户名"
$ git config --global user.email "此处填你的邮箱"
```
到此为止，SSH就在GitHub上绑定成功了，本地与Github成功连接.

### 使用Hexo创建博客
#### 下载仓库
登录Github, 打开刚才新建的项目yourname.github.io
复制仓库的地址，仓库地址如下图
![img](/images/220410141506.png)
然后在Mac电脑桌面输入下面命令，就能将Github仓库拉到本地，后面可以使用Git工具提交内容
``` bash
cd ~/Desktop
git clone https://github.com/xxx/xxx.github.io.git
```
#### 创建博客
打开电脑“终端”应用，输入命令
``` bash
cd ~/Desktop/xxx.github.io
hexo init
```
当命令执行完成后，会在xxx.github.io文件夹下生成如下图所示的目录结构
![img](/images/220410143207.png)

#### 本地部署
接下去要安装相关依赖，并在自己电脑进行本地部署
``` bash
npm install
hexo clean && hexo g && hexo server
```
直到结果显示如下，
``` bash
INFO  Start processing
INFO  Hexo is running at http://localhost:4000 . Press Ctrl+C to stop.
```
并在浏览器中打开[http://localhost:4000](http://localhost:4000)，如果显示如下图，恭喜您，已经成功创建好了个人博客了，如果没有出现下面截图，可能哪一步出错了，再重新检测下。
![img](/images/220410144048.png)


#### 远程部署
通过上面的本地部署，接下去我们怎么将自己的博客部署到Github page上，让大家可以访问呢。
##### 安装工具
``` bash
$ npm install hexo-deployer-git --save
```

##### 修改配置
打开xxx.github.io目录下_config.yml文件，将该文件最底部的deploy关键字代码：
``` bash
deploy:
  type: 
```
修改为:
``` bash
deploy:
  type: git
  repository: https://github.com/xxx/xxx.github.io.git
  branch: main
```

##### 部署
在xxx.github.io目录下，执行
``` bash
$ hexo g && hexo deploy
```
hexo g用来生成Hexo页面，hexo d是使用hexo-deployer-git将页面部署到Github仓库中，当部署成功后，就可以可以在浏览器下输入xxx.github.io显示跟本地部署一样的内容了。