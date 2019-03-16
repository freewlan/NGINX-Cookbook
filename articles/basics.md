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

## 1.5 配置文件，命令和目录
### 需求
您需要了解NGINX重要的目录和命令。
### 解决方案
#### NGINX文件和目录
*/etc/nginx/*
> /etc/nginx 目录是NGINX服务器的默认配置根目录。在此目录中，您将找到指示NGINX如何操作的配置文件。 

*/etc/nginx/nginx.conf*
> /etc/nginx/nginx.conf 文件是NGINX服务使用的默认配置入口。 此配置文件为全局设置，包括工作进程、调优、日志记录、加载动态模块以及对其他NGINX配置文件的引用等内容设置。 在默认配置中，/etc/nginx/nginx.conf文件包含顶级http块，其中包含下一个目录中的所有配置文件。

*/etc/nginx/conf.d/*
> /etc/nginx/conf.d/ 目录包含默认的HTTP服务器配置文件。 此目录中以.conf结尾的文件包含在/etc/nginx/nginx.conf文件中的顶级http块中。 最佳做法是利用包含语句并以这种方式组织配置，以使配置文件保持简洁。 在某些软件包存储库中，此文件夹名为sites-enabled，配置文件从名为site-available的文件夹链接（不推荐使用这种方式）

*/var/log/nginx/*
> /var/log/nginx/ 目录是NGINX的默认日志目录。 在此目录中，您将找到access.log文件和error.log文件。 access.log包含NGINX服务的每个请求的记录。 如果启用了debug模块，则error.log文件包含错误事件和调试信息。

#### NGINX命令
*nginx -h*
> 显示NGINX帮助菜单。

*nginx -v*
> 显示NGINX版本信息。

*nginx -V*
>显示NGINX版本，构建信息和配置参数，显示NGINX二进制文件内置的模块。

*nginx -t*
>测试NGINX配置。

*nginx -T*
>测试NGINX配置并将验证的配置打印到屏幕。 在寻求支持时，此命令很有用。

*nginx -s*
>-s 标志向NGINX主进程发送信号。您可以发送stop，quit，reload和reopen等信号。stop信号立即中断NGINX进程。quit信号在完成请求处理后停止NGINX进程。 reload信号重新加载配置。reopen信号指示NGINX重新打开日志文件。

### 详解
通过了解这些关键文件，目录和命令，您就可以开始使用NGINX了。 有了这些知识，您可以使用nginx -t命令更改默认配置文件并测试更改。 如果测试成功，您还可以使用nginx -s reload命令指示NGINX重新加载其配置。

## 1.6 静态内容服务
### 需求
您需要使用NGINX提供静态内容服务。
### 解决方案
使用以下NGINX配置覆盖/etc/nginx/conf.d/default.conf中的默认HTTP服务器配置  
示例
```
server {
        listen 80 default_server;
        server_name www.example.com;
        location / {
            root /usr/share/nginx/html;
            # alias /usr/share/nginx/html;
            index index.html index.htm;
} }
```
### 详解
此配置的意思是在80端口上通过HTTP提供静态文件服务，文件目录在/usr/share/nginx/html。此配置中的第一行定义了一个新的服务器块。 第二行指示NGINX侦听端口80，default_server参数指示NGINX使用此服务块作为80端口的默认服务.service_name指令定义应将哪些主机名请求定向到此服务器。 如果配置未将此服务块定义为default_server，则仅当HTTP主机名与提供给server_name指令的值相匹配时，NGINX才会将请求定向到此服务块。

location模块根据URL中的路径来匹配配置。该示例使用 / 来匹配所有请求。root指令指示NGINX提供内容时查找静态文件的位置。 在查找请求的文件时，请求的URI将附加到root指令的值中。 如果我们为location指令提供了一个URI前缀，那么这将包含在附加路径中，除非我们使用别名目录而不是root。 最后，如果URI中没有提供进一步的路径，则index指令为NGINX提供默认文件或要检查的文件列表。

## 1.7 平滑重启
### 需求
您需要重新加载配置而不影响当前的请求
### 解决方案
使用NGINX的reload方法可以在不停止服务的情况下实现配置的重新加载：
```sh
$ nginx -s reload
```
此示例使用NGINX二进制文件向主进程发送reload信号。
### 详解
在不停止服务的情况下重新加载NGINX配置，可以在不丢弃任何数据包的情况下即时更改配置。 比如在高可用的生产环境中，您需要在某个时刻更改负载平衡配置。 NGINX允许您在保持负载均衡器在线的同时执行此操作。 此功能支持无数种可能性，例如在生产环境中重新运行配置管理，或构建应用程序和群集感知模块以动态配置和重新加载NGINX以满足环境需求。
