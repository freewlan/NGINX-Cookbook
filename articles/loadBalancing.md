## 2.0 介绍
今天良好的互联网用户体验需要良好的性能和高可用的服务。 为此，运行同一系统的多个副本，并在其上分配负载。 随着负载的增加，系统的另一个副本可以接入处理任务。 这种架构技术称为水平扩展。 基于软件的基础设施因其灵活性而日益普及，开辟了广阔的可能性。 对于只有两个实例或者在全球有数千个实例的系统，需要一个与基础架构一样动态的负载平衡解决方案。 NGINX以多种方式满足了这一需求，例如HTTP，TCP和UDP负载平衡，我们将在本章中介绍。

在平衡负载时，重要的是对客户产生好的影响。许多现代Web体系结构使用无状态应用程序层，将状态存储在共享内存或数据库中。然而，并不是所有的情况都这样。会话状态在交互式应用程序中非常有价值且非常庞大。出于多种原因，此状态可能存储在应用程序服务器的本地;例如，在应用程序中，所处理的数据太大，以致网络开销在性能上产生了比较大的影响。当状态存储在本地应用程序服务器上时，对于用户体验来说，后续请求继续传递到同一服务器是非常重要的。这种情况的另一个方面是在会话结束之前不应该释放服务器。大规模处理有状态的应用程序需要智能负载平衡器。 NGINX Plus通过跟踪cookie或路由提供多种方法来解决此问题。本章介绍会话持久性，因为它与NGINX和NGINX Plus的负载平衡有关。

确保NGINX服务良好运行也很重要。有多种原因导致会应用不能访问，可能是因为网络连接，服务器故障或应用程序故障。因此，代理和负载均衡器必须足够智能，以检测上游服务器的故障并停止向它们传递流量;否则，客户端将一直等待导致超时。服务器发生故障时缓解服务质量下降的一种方法是让代理检查上游服务器的运行状况。 NGINX提供两种不同类型的健康检查：被动检查，可用于开源版本;主动检查，仅在NGINX Plus中可用。定期进行的主动运行状况检查将与上游服务器建立连接或请求，并可以验证响应是否正确。当客户端发出请求或连接时，被动运行状况检查会监视上游服务器的连接或响应。您可能希望使用被动运行状况检查来减少上游服务器的负载，并且您可能希望在客户端发生故障之前使用主动运行状况检查来确定上游服务器的故障。本章的尾部将介绍检查监视负载均衡的上游应用程序服务器的运行状况。

## 2.1 HTTP负载均衡
### 需求
您需要在两个或更多的HTTP服务器之间分配负载。

### 解决方案
使用NGINX的HTTP负载均衡需要使用upstream模块
```
upstream backend {
	server 10.10.12.45:80      weight=1;
	server app.example.com:80  weight=2;
}
server {
	location / {
		proxy_pass http://backend;
	} 
}
```
此配置在两个HTTP服务器的80端口上进行分配负载。weight参数表示权重，weight参数默认为1。

### 详解
HTTP upstream 模块控制HTTP的负载均衡。 此模块通过Unix套接字、IP地址、DNS记录的任意组合定义目标池。 upstream模块还定义了如何将单个请求分配给upstream服务器。
每个upstream目标都是由server指令在upstream池中定义的。 server指令提供了Unix套接字，IP地址或FQDN，以及许多可选参数。 可选参数可以更好地控制请求的路由。 这些参数包括平衡算法中服务器的权重; 服务器是处于待机模式，可用还是不可用; 以及如何确定服务器是否不可用.NGINX Plus提供了许多其他方便的参数，例如服务器的连接限制，高级DNS解析控制，以及在启动后缓慢增加与服务器的连接的能力。