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
您刚刚创建的文件指示apt软件包管理系统使用NGINX官方软件包存储库。 下面的命令下载NGINX GPG包签名密钥并将其导入apt。 提供apt签名密钥使apt系统能够验证来自存储库的包。 apt-get update命令指示apt系统从其已知的存储库刷新其包列表。 刷新包列表后，您可以从NGINX官方存储库安装NGINX。 安装后，最后一个命令启动NGINX。

## 1.2 在RedHat/CentOS上安装
### 需求
你需要在RedHat/CentOS的机器上安装开源版本的NGINX
### 解决方案
创建一个名为 */etc/yum.repos.d/nginx.repo* 的文件，其中包含以下内容：
>      [nginx]  
>      name=nginx repo  
>      baseurl=http://nginx.org/packages/mainline/OS/OSRELEASE/$basearch/  
>      gpgcheck=0  
>      enabled=1  

更改文件，将URL末尾的操作系统替换为rhel或centos，具体取决于您的系统发行版本。 对于版本6.x或7.x，分别用6或7替换OSRELEASE。 然后，运行以下命令：
```sh
yum -y install nginx
systemctl enable nginx
systemctl start nginx  
firewall-cmd --permanent --zone=public --add-port=80/tcp  
firewall-cmd --reload
```
### 详解
您刚刚创建的文件指示yum软件包管理系统使用NGINX官方开源软件包存储库。 下面的命令从官方存储库安装NGINX，指示systemd在启动时启用NGINX，并且现在启动NGINX服务。 为TCP协议打开80端口，这是HTTP的默认端口。 最后一个命令重新加载防火墙以提交更改。

## 1.3 安装NGINX Plus
### 需求
你需要安装NGINX Plus
### 解决方案
访问 http://cs.nginx.com/repo_setup 从下拉菜单中，选择您要安装的操作系统，然后按照说明操作。 安装方法与开源版本的NGINX安装类似; 但是，您需要安装证书才能验证NGINX Plus存储库。
### 详解
NGINX使该存储库安装指南保持最新，并附有安装NGINX Plus的说明。 根据您的操作系统和版本，这些说明略有不同，但有一个共性。 您必须登录NGINX官网才能下载用于对NGINX Plus存储库进行身份验的证书和密钥。
