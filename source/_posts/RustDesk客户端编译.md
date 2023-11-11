---
title: RustDesk客户端编译
date: 2023-09-27 21:33:23
tags:
---
# 简介
RustDesk是一款开箱即用的开源免费的全平台远程桌面软件，包括`MacOS`、`Windows`和`Android`的客户端支持。它可以替代类似于`向日葵远程桌面`、`ToDesk`等远程桌面服务，这可以让你完全掌控数据，不用担心安全问题。你可以选择使用官方的注册/中继服务器，也可以自建服务器，甚至基于官方代码开发自己的版本。

rustdesk仓库地址: https://github.com/rustdesk/rustdesk

官方网站地址: https://rustdesk.com

<!--more-->

# 环境准备
- 下载libsciter库 [Windows](https://raw.githubusercontent.com/c-smile/sciter-sdk/master/bin.win/x64/sciter.dll) | [Linux](https://raw.githubusercontent.com/c-smile/sciter-sdk/master/bin.lnx/x64/libsciter-gtk.so) | [macOS](https://raw.githubusercontent.com/c-smile/sciter-sdk/master/bin.osx/libsciter.dylib)
- 准备好Rust开发环境和C++编译环境
- 安装[vcpkg](https://github.com/microsoft/vcpkg)，并正确的设置系统环境`VCPKG_ROOT`
```
cd ~/Desktop/github
git clone https://github.com/microsoft/vcpkg.git
cd vcpkg
# 在~/.bash_profile添加如下
export VCPKG_ROOT="$HOME/Desktop/github/vcpkg"
export PATH=$VCPKG_ROOT:$PATH
```
- 安装flutter
```
cd ~/Desktop/github
git clone https://github.com/flutter/flutter.git
git checkout 3.10.6
# 在~/.bash_profile添加如下
export FLUTTER_ROOT="$HOME/Desktop/github/flutter"
export PATH=$FLUTTER_ROOT/bin:$PATH
```
- 安装依赖库
1. 查看支持的架构`ls $VCPKG_ROOT/triplets/community/*.cmake`
2. 根据电脑cpu架构安装对应版本:
&ensp; a. <B>Windows:</B> `vcpkg install libvpx:x64-windows-static libyuv:x64-windows-static opus:x64-windows-static aom:x64-windows-static`
&ensp; b. <B>Linux/macOS:</B>`vcpkg install libvpx libyuv opus aom`
3. 安装成功后，会在`$VCPKG_ROOT/installed/`目录下有对应架构的库；

- rustdesk客户端
```
cd ~/Desktop/github
git clone https://github.com/rustdesk/rustdesk.git
cd rustdesk
git checkout 1.2.2
```
注：保证libsciter库在`~/Desktop/github/rustdesk`目录下。

# rustdesk编译
- 【PC】编译并运行
1. rust
```
cd ~/Desktop/github/rustdesk
cargo run 或者 cargo run --release
``` 

2. flutter
```
cd ~/Desktop/github/rustdesk/flutter
flutter pub get
./run.sh
```

3. 打包
```
cd ~/Desktop/github/rustdesk
./build.py
```

- [Phone]编译并运行
1. iOS编译
```
cd ~/Desktop/github/rustdesk/flutter
flutter pub get
./ios_arm64.sh
``` 
2. iOS打包
```
cd ~/Desktop/github/rustdesk/flutter
./build_ios.sh
``` 
- 【VSCode】编译并调试
1. 编译
```
cd ~/Desktop/github/rustdesk
cargo build --release --features "flutter,flutter_texture_render"
``` 
2. launch.json配置
```json
{
    // 使用 IntelliSense 了解相关属性。 
    // 悬停以查看现有属性的描述。
    // 欲了解更多信息，请访问: https://go.microsoft.com/fwlink/?linkid=830387
    "version": "0.2.0",
    "configurations": [
        {
            "name": "(flutter) RustDesk",
            "type": "dart",
            "request": "launch",
            "flutterMode": "debug",
            "program": "${workspaceRoot}/flutter/lib/main.dart"
        },
        {
            "name": "(OSX) RustDesk",
            "type": "lldb",
            "request": "launch",
            "program": "${workspaceRoot}/target/debug/rustdesk",
            "args": [],
            "stopAtEntry": false,
            "cwd": "${workspaceRoot}"
        },
        {
            "name": "(Windows) RustDesk",
            "type": "cppvsdbg",
            "request": "launch",
            "program": "${workspaceRoot}/target/debug/rustdesk.exe",
            "args": [],
            "stopAtEntry": false,
            "cwd": "${workspaceRoot}",
            "environment": [],
            "externalConsole": true
        }
    ]
}
```
3. 调试

# 遇到问题
1. 问题一
![img](/images/2309062320.png)
```
需要后面加上cargo build -Z namespaced-features
```

2. 问题二
![img](/images/2309061512.jpg)

3. 问题三
![img](/images/2309072123.png)
```
检查cargo版本是否1.72.0，如果不是，可通过curl https://sh.rustup.rs -sSf | sh进行安装
```
4. 问题四
![img](/images/2309061516.jpg)
```
执行方框中语句
```

5. 问题五
![img](/images/2309062107.jpg)
```
在该路径下没有指定的库和头文件，需要安装指定平台的库vcpkg install opus:arm64-ios
```

6. 问题六
![img](/images/2309080819.png)
```
这个错误是没有编译出libvpx.a没有编译出arm64架构；方法如下:
cd vcpkg/buildtrees/libvp/src
cd *
./configure —help
./configure --target="arm64-darwin-gcc armv7-darwin-gcc armv7s-darwin-gcc"
```

7. 问题七
env: python: No such file or directory
```
sudo ln -s $(which python3) /usr/local/bin/python
sudo ln -s /Library/Developer/CommandLineTools/usr/bin/python3 /Library/Developer/CommandLineTools/usr/bin/python
```

8. 问题八
执行./flutter/build_ios.sh编译出来的ipa安装后显示空白
```
Editing the Runner build setting Deployment -> Strip Style -> Non-Global Symbols
```

9. 问题九
xcode直接运行的应用可以运行，不调试应用就会崩溃
```
修改schema中run —> build configuration —> release
```

10. 问题十
执行./flutter/build_ios.sh，报exportArchive: "Runner.ap” requires a provisioning profile
```
1、修改./flutter/iOS/exportOptions.plist中对应的method:development或app-store、teamID、provisioningProfiles
2、在./flutter/build_ios.sh加上--export-options-plist=$PWD/ios/exportOptions.plist
```

11. 问题十一(Windows)
![img](/images/2310120807.png)
```
1. 报错信息提示需要设置LIBCLANG_PATH的环境变量，即使设置了llvm和gcc的环境变量后还报错，通过export -p查看下；
2. 通过export LIBCLANG_PATH="D:\\LLVM\\bin"进行设置
```
- [llvm](https://github.com/llvm/llvm-project)

12. 问题十二(Windows)
无法直接打开`RustDesk.exe`
```
需要保证sciter.dll库与RustDesk.exe是同级目录，或者C:/Windows/System32
```

13. 问题十三(Windows)
Windows应用签名，可以参考：
a. [客户端sign一个签名证书](https://blog.csdn.net/wangshubo1989/article/details/50849914)