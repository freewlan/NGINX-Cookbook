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

## 1.4 验证你的安装
### 需求
您想要验证NGINX的安装并检查安装的版本。
### 解决方案
您可以使用以下命令验证是否已安装并检查NGINX版本：
```sh
$ nginx -v
nginx version: nginx/1.15.3
```
如此示例所示，响应显示NGINX版本。
您可以使用以下命令确认NGINX正在运行：
```sh
$ ps -ef | grep nginx
root      1738     1  0 19:54 ?  00:00:00 nginx: master process
nginx     1739  1738  0 19:54 ?  00:00:00 nginx: worker process
```
ps命令列出正在运行的进程。通过使用管道符grep，您可以在输出中搜索特定单词。此示例使用grep搜索nginx。结果显示了两个正在运行的进程，一个master和一个worker。如果NGINX正在运行，您将始终看到主服务器和一个或多个工作进程。有关启动NGINX的说明，请参阅下一节。要了解如何将NGINX作为守护程序启动，请使用init.d或systemd方法。
要验证NGINX是否正确返回请求，请使用浏览器向您的计算机发出请求或使用curl：
```sh
$ curl localhost
```
您将看到NGINX的默认HTML站点。
### 详解
nginx命令允许您与NGINX二进制文件交互以检查版本，列出已安装的模块，测试配置以及向主进程发送信号。 NGINX必须运行才能满足请求。 ps命令是确定NGINX是作为守护程序运行还是作为前台运行的万无一失的方法。默认情况下，NGINX提供的默认配置在端口80上运行静态站点HTTP服务器。您可以通过向本地主机上的计算机发出HTTP请求以及主机的IP和主机名来测试此默认站点。
