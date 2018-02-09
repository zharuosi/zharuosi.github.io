---
title: SSH免密码登录
date: 2018-02-07 22:35:15
tags: Linux
---

本文介绍如何使用SSH进行远程登录以及设置SSH免密码登录。

## SSH简介

首先简单介绍一下什么[SSH(Secure Shell)](https://en.wikipedia.org/wiki/Secure_Shell)。

SSH是一个用于计算机间的远程登录会话和其他网络服务的加密网络安全协议，由互联网工程任务组 IETF(Internet Engineering Task Force)的网络工作小組（Network Working Group）所制定。相对于传统的网络服务，如FTP、 POP等， SSH协议避免了使用明文传输数据，帐号和口令，可以有效防止信息泄漏和中间人攻击，也能够防止DNS欺骗和IP欺骗。其具体工作原理本文不作详述。

## 使用SSH登录远程主机

- 判断是否安装SSH服务：
``` bash
$ ssh localhost
```
	若显示connection refused, 则需要手动安装。

- 安装SSH服务：

	通常使用的是[openssh](https://www.openssh.com), 以Ubuntu为例。

	- 本机：
	``` bash
	$ sudo apt-get install openssh-client
	```

	- 服务器：
	``` bash
	$ sudo apt-get install openssh-server
	```

- 安装完成后确认开启SSH服务：
``` bash
$ ps -e|grep ssh
```

- 启动服务器端的SSH服务：
``` bash
$ sudo /etc/init.d/ssh start
```

- 更改默认端口号：
``` bash
$ sudo vim /etc/ssh/sshd_config
```

- 使用SSH登录：
	本机输入以下命令登录远程服务器(例如ip地址为10.20.120.10的远程服务器)：
``` bash
$ ssh user@10.20.120.10
```
- 开启图形界面：
``` bash
$ ssh -Y user@10.20.120.10
```
	即可使用远程服务器上的图形界面应用。

一般情况下，每次登录必须输入密码。在涉及大量文件传输操作时，频繁输入密码会十分不便。以下介绍如何免密码登录。


## 设置SSH免密码登录 

- 创建公钥
``` bash
$ ssh-keygen -t rsa
```

	默认设置即可，创建id_rsa（私钥）和id_rsa.pub（公钥）文件。以后再次操作记得先行备份。

- 上传公钥至服务器
``` bash
$ rsync -avPz ~/.ssh/id_rsa.pub user@server:~/.ssh/
```
	Note: 如果服务器上无.ssh目录则先创建（mkdir ~/.ssh）


- 复制公钥至authorized_keys
``` bash
$ cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
```

- 更改文件及目录权限
``` bash
$ chmod 600 ~/.ssh/authorized_keys
$ chmod 700 ~/.ssh
```

- 完成后即可不输入密码直接登录。 
