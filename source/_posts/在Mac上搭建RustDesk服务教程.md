---
title: 在Mac上搭建RustDesk服务教程
date: 2023-09-04 19:42:58
tags:
---

# 简介
RustDesk是一款开箱即用的开源免费的全平台远程桌面软件，包括`MacOS`、`Windows`和`Android`的客户端支持。它可以替代类似于`向日葵远程桌面`、`ToDesk`等远程桌面服务，这可以让你完全掌控数据，不用担心安全问题。你可以选择使用官方的注册/中继服务器，也可以自建服务器，甚至基于官方代码开发自己的版本。

rustdesk-server仓库地址: https://github.com/rustdesk/rustdesk-server

官方网站地址: https://rustdesk.com

<!--more-->

# 服务器部署
## 环境准备
1. 服务器需要有安装Docker和DockerCompose环境，如果没有安装，可以参考以下方法
> - 方法一：[Docker 与 Docker Compose的安装](https://www.hash070.top/archives/docker-and-docker-compose-install.html)
> - 方法二：[MacOS Docker 安装](https://www.runoob.com/docker/macos-docker-install.html)，安装`Docker Desktop`

2. 首次安装`Docker Desktop`后，有可能会报`Is Docker daemon running`，需要点击`菜单栏`里`Docker`图标，然后在下拉列表中，点击`Restart`

3. 安装`rustdesk-serve`镜像
- 修改镜像源，`Preference` -> `Docker Engine`添加如下，并点击`Apply & Restart`，应用会对应修改`~/.docker/daemon.json`
```
"registry-mirrors": [
    "https://hub-mirror.c.163.com",
    "https://ustc-edu-cn.mirror.aliyuncs.com",
    "https://ghcr.io",
    "https://mirror.baidubce.com"
  ]
```
- 安装`rustdesk-serve`镜像
在`Docker Desktop`左边栏选中`Images`，然后在搜索框中输入`rustdesk-serve`，如下图
![img](/images/2309042036.jpg)

4. 检查Docker Desktop是否安装好
```
docker search rustdesk-serve
或
docker info
```

5. 新建Docker Container

5.1 hbbs服务
```
docker run --name hbbs -p 21115:21115 -p 21116:21116 -p 21116:21116/udp -p 21118:21118 -v ~/.docker/rustdesk/hbbs/root:/root -td rustdesk/rustdesk-server:latest hbbs -r 服务器公开ip -m 邮箱 -k 密码
```
- 服务器公开ip: 填写服务器域名或者ip
- 邮箱: 任意可用邮箱
- 密码: 可以任意

5.2 hbbr服务
```
docker run --name hbbr -p 21117:21117 -p 21119:21119 -v ~/.docker/rustdesk/hbbr:/root -td rustdesk/rustdesk-server:latest hbbr -m 邮箱
```
- 邮箱: 任意可用邮箱(与hbbs服务中邮箱保持一致)


5.3 检查hbbr、hbbs服务
当新建好hbbs、hbbr服务后，会在RestDesk应用中看到对应的两个服务；如下图
![img](/images/1694930079747.jpg)

5.4 rustdesk-server编译和运行
- 【下载】rustdesk-server仓库代码
- 【编译】cargo build --release
- 【检查】./target/release/hbbr 和 ./target/release/hbbs
- 【运行】执行以上编译出来的服务
- 当运行以上服务后，会在对应的根目录下生成id_ed25519和id_ed25519.pub文件

5.5 RustDesk应用中继服务器
![img](/images/1694931305486.jpg)

6. 遇到问题
6.1 问题一：如果发生`docker info`等docker命令会卡住。
  解决办法：打开Mac电脑`监视器`，将带有docker的进程全部关闭，如果有开着vpn，建议先关掉vpn
