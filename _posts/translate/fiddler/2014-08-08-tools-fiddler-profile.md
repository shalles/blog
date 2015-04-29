###入门Fiddler

---

#####首先，安装Fiddler。

[下载Fiddler](http://www.telerik.com/download/fiddler)

#####接下来，配置Fiddler服务器。

Fiddler服务器就是当前安装fiddler的windows PC。
某些情况下可能需要特定的配置。如下：

- 流量类型，如HTTPS进行解密，并与通道绑定证书认证
- 操作系统，如Windows 8中和的Mac OSX
- 网络配置，像监视远程计算机，链接到上游代理，使用Fiddler作为一个反向代理，监控本地流量或监视拨号(dal-up)和VPN连接

#####最后，配置客户端。

客户端是Fiddler网络流量监控的源。某些客户端应用程序，操作系统，和设备可能需要特定的步骤，以发送和接收与Fiddler通信。这包括：

- 浏览器：如火狐，Opera或IE（当发送通信到localhost）
- 应用程序：如.NET应用程序，WinHTTP的应用，Java应用程序和PHP /cURL apps
- 设备：如安卓，iOS的，Windows Phone 7的，和掌上电脑设备


####配置浏览器
配置浏览器使用系统代理，

在Fiddler中设置：File > Capture Traffic。或F12 或点击左下角图标显示Capturing
