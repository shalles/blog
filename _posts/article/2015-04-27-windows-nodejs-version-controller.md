---
layout: post
category : Tools
title : "windows下管理多个版本nodejs"
tagline: "利其器"
tags : [node, 版本管理, windows, gnvm]
excerpt: 工作中我们可能需要用到一些工具，但这些工具依赖不同版本的node环境，那我们需要来回的切换node的环境吗， NO, 只是因为有了GNVM
---
{% include JB/setup %}

###多版本node
工作中我们可能需要用到一些工具，但这些工具依赖不同版本的node环境，那我们需要来为的切换node的环境吗， window msi安装的用户需要卸载重装的循环吗？ 一切都变得很好，只是因为有了GNVM
####what is GNVM
Node.js version manager on Windows by GO
(一个windows上用Go语言实现的node版本管理工具)

![gnvm的设计]({{ BASE_PATH }}/assets/images/node/gnvm.png)

####怎样获取
- 下载

    [32-bit](https://app.box.com/gnvm/1/2014967291) | [64-bit](https://app.box.com/gnvm/1/2014967689)  Box.com 源

    [32-bit](https://github.com/Kenshin/gnvm-bin/blob/master/32-bit/gnvm.exe?raw=true) | [64-bit](https://github.com/Kenshin/gnvm-bin/blob/master/64-bit/gnvm.exe?raw=true)  Github.com 源

- 源代码生成
    - go语言用户

    ```
    go get github.com/Kenshin/gnvm
    ```
    
    - git 用户

    ```
    git clone git@github.com:Kenshin/gnvm-bin.git
    ```
    
    - curl 用户

    ```
    // 32位
    curl -L https://github.com/Kenshin/gnvm-bin/blob/master/32-bit/gnvm.exe?raw=true -o gnvm.exe
    
    // 64位
    curl -L https://github.com/Kenshin/gnvm-bin/blob/master/64-bit/gnvm.exe?raw=true -o gnvm.exe
    ```



####怎么安装
- 安装过node且已加入环境变量
把下载下来的gnvm.exe放到node.exe所在的目录下
- 没有安装过node
把gnvm.exe放到自定义的一个目录下，并把这个目录添加到环境变量path中

然后打开命令行，运行`gnvm version`输出`Current version x.x.x`则表示安装成功
>#### `note:`
> 通过图形界面设置的环境变量需要在确定并关掉环境变量设置窗口后打开的cmd中才会生效。

####使用gnvm安装node
gnvm的命令集

```
Usage:
  gnvm
  gnvm [command]

Available Commands:
  version                   :: 输出当前gnvm的版本
  install                   :: 安装指定版本的nodejs
  uninstall                 :: 卸载指定版本的nodejs
  use                       :: 切换使用已安装的指定版本的nodejs
  update                    :: Update latest node.exe
  ls                        :: 显示所有已安装的nodejs版本
  node-version              :: 显示<global> <latest>的nodejs版本
  config                    :: Setter and getter registry
  help [command]            :: Help about any command
```

安装前可能要做一些资源源的配置

```
> gnvm config INIT 
Config file C:\Program Files\nodejs\\.gnvmrc init success.
```

```
> gnvm config registry http://dist.u.qiniudn.com
Set success, registry new value is http://dist.u.qiniudn.com/
```

```
> gnvm update latest
> gnvm ls
> gnvm node-version
Node.exe latest version is 0.12.2.
Node.exe global version is 0.10.25.
```

`global` 当前node.exe版本 == `node -v`.
`latest` 已安装的可用node.exe版本，可通过`gnvm use x.x.x`切换成`global`.

```
> gnvm install 0.11.1 0.11.2 0.11.3
```

批量安装多个nodejs版本

```
> gnvm use 0.11.1
```

通过`use`使用指定nodejs版本

```
> gnvm uninstall 0.11.1
```

卸载指定node版本

####好使用`gnvm use x.x.x`切换版本
我们可以愉快的安装和使用依赖各种nodejs版本的工具或库了

####相关链接

[gnvm官网: http://ksria.com/gnvm/](http://ksria.com/gnvm/)

[gnvm github: https://github.com/kenshin/gnvm](https://github.com/kenshin/gnvm)