---
layout: post
category : [Tools, Linux]
title : "一些工具的安装及配置笔记"
tagline: "利其器"
tags : [Linux, vim, ssh]
excerpt: 开发过程中我们不仅是写代码，我们还要了解我们使用的一些工具的安装及配置来让我们更愉快的开发。
---
{% include JB/setup %}

###npm gem配置淘宝源

[ http://npm.taobao.org/ ](http://npm.taobao.org/)

**npm**

```
$ npm --registry=https://regstry.npm.taobao.org
```
**gem**

[ http://ruby.taobao.org/ ](http://ruby.taobao.org/)

```
$ gem sources --remove https://rubygems.org/
$ gem sources -a https://ruby.taobao.org/
$ gem sources -l
```

###sublime 开启console


[ https://sublime.wbond.net/installation#st3 ](https://sublime.wbond.net/installation#st3)


###sublime其他配置

**一般配置**

```json
"translate_tabs_to_spaces": true,
"draw_white_space": "all",
```

**快捷键**

```
Ctrl/command+Shift+Enter:光标前插入行。
Ctrl/command+Shift+[ 折叠代码
Ctrl/command+Shift+K 删除整行
Ctrl/command+Shift+P：打开命令面板
Ctrl/command+P：搜索项目中的文件
Ctrl/command+G：跳转到第几行
Ctrl/command+W：关闭当前打开文件
Ctrl/command+Shift+W：关闭所有打开文件
Ctrl/command+Shift+V：粘贴并格式化
Ctrl/command+D：选择单词，重复可增加选择下一个相同的单词
Ctrl/command+L：选择行，重复可依次增加选择下一行
Ctrl/command+Shift+Enter：在当前行前插入新行
Ctrl/command+X：删除当前行
Ctrl/command+M：跳转到对应括号
Ctrl/command+U：软撤销，撤销光标位置
Ctrl/command+J：选择标签内容
Ctrl/command+F [+shift]：查找内容[文件夹内查找替换所有文件的内容]
(Ctrl+Shift)/(option/command)+F：查找并替换
Ctrl/command+R：前往 @ method
Ctrl/command+N：新建窗口
Ctrl/command+K+B：开关侧栏
Ctrl/command+F2：设置/删除标记
Ctrl/command+/：注释当前行
(Ctrl+Shift)/(option+command)+/：当前位置插入注释
F11：全屏
(Alt+Shift)/(option+command)+数字：分屏显示
Alt/option+数字：切换打开第N个文件
```

###linux开启ssh支持远程登陆

```
1.检查是否安装了ssh 
$ rpm -qa |grep ssh

没有安装的话安装rpm/ssh
$ sudo apt install ssh

2.安装后启动ssh
$ service ssd start/restart

启动后可以查看端口
$ grep Port /etc/ssh/sshd_config

4.可能需要关闭防火墙
$ /etc/init.d/iptables stop

```

###vim主题配置

[ https://github.com/sickill/vim-monokai ](https://github.com/sickill/vim-monokai)

###iTerm2主题配置

[ https://github.com/mbadolato/iTerm2-Color-Schemes ](https://github.com/mbadolato/iTerm2-Color-Schemes)

###nginx安装配置

**安装**

[ http:www.cnblog.com/converted/p/3492342.html ](http:www.cnblog.com/converted/p/3492342.html)

**https**

```js
server { 
    listen 443; 
    server_name src.shalles.org; 
    ssl on; 
    ssl_certificate /home/nginx/conf/shalles.org.crt; 
    ssl_certificate_key /home/nginx/conf/shalles.org.key; 
    ssl_session_timeout 6m; 
    ssl_protocols SSLv3 TLSv1; 
    ssl_ciphers ALL:!ADH:!EXPORT56:RC4+RSA:+HIGH:+MEDIUM:+LOW:+SSLv2:+EXP; ssl_prefer_server_ciphers on; 
    concat on; 

    
    //添加头
    location ~ /dev/ { 
        expires -1; 
        rewrite ^(.*?)-(\d+|[\d\.]+)\.(js|css)$ $1-dev.$3 last; 
        add_header Access-Control-Allow-Origin *; 
    }
}
```

###linux改密码

```
$ sudo passwd username
```

###mysql修改密码
方法一: 
(适用于管理员或者有全局权限的用户重设其它用户的密码)
进入命令行模式

```
$ mysql -u root -p
```

```sql
mysql>use mysql;
mysql> UPDATE user SET password=PASSWORD("new password") WHERE user='username';
mysql> FLUSH PRIVILEGES;
mysql> quit;
```

方法二:

```sql
mysql -u root -p 
mysql>use mysql; 
mysql> SET PASSWORD FOR username=PASSWORD('new password');
mysql> QUIT
```
 
 方法三:

```
$ mysqladmin -u root "old password" "new password"
```

###gitlab安装

[ http://doc.gitlab.com/ce/install/installation.html ](http://doc.gitlab.com/ce/install/installation.html)

###mac brew

[ http://brew.sh/ ](http://brew.sh/)

```
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```