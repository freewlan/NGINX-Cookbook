## 1.0 介绍
要开始使用开源版本的NGINX或NGINX Plus，首先需要将其安装在系统上并学习一些基础知识。 在本章中，您将学习如何安装NGINX以及管理命令。 您还将学习如何验证安装并向默认服务器发出请求。

## 1.1 在Debian/Ubuntu上安装
### 需求
你需要在Debian/Ubuntu的机器上安装开源版本的NGINX
### 解决方案
创建一个名为 */etc/apt/sources.list.d/nginx.list* 的文件，其中包含以下内容：
>      deb http://nginx.org/packages/mainline/OS/ CODENAME nginx  
>      deb-src http://nginx.org/packages/mainline/OS/ CODENAME nginx

更改文件，用ubuntu或debian替换URL末尾的OS，具体取决于您的系统发行版。 将CODENAME替换为您的分发代码名称; 如果是Debian,一般是jessie或者stretch，如果是Ubuntu，一般是trusty, xenial, artful, bionic。 然后，运行以下命令：
```sh
wget http://nginx.org/keys/nginx_signing.key  
apt-key add nginx_signing.key  
apt-get update  
apt-get install -y nginx  
/etc/init.d/nginx start
```
### 详解
您刚刚创建的文件指示apt软件包管理系统使用NGINX官方软件包存储库。 下面的命令下载NGINX GPG包签名密钥并将其导入apt。 提供apt签名密钥使apt系统能够验证来自存储库的包。 apt-get update命令指示apt系统从其已知的存储库刷新其包列表。 刷新包列表后，您可以从官方NGINX存储库安装NGINX。 安装后，最后一个命令启动NGINX。
