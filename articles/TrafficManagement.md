## 3.0 介绍
NGINX和NGINX Plus也被归类为网络流量控制器。 您可以使用NGINX根据许多属性主动路由流量和控制流量。本章介绍NGINX基于百分比分割客户端请求，利用客户端的地理位置以及以速率，连接和带宽限制等形式控制流量的能力。在阅读本章时，请记住，您可以混合使用这些功能以实现无数可能性。

## 3.1 AB测试
### 需求
您需要在多个版本的文件或应用程序之间拆分客户端以测试接受度。

### 解决方案
使用split_clients模块将一定比例的客户端定向到不同的upstream池：

```
split_clients "${remote_addr}AAA" $variant {
        20.0%    "backendv2";
        *        "backendv1";
}
```

split_clients指令将您提供的字符串哈希作为第一个参数，并将该哈希值除以提供的百分比，以映射作为第二个参数提供的变量的值。 第三个参数是包含键值对的对象，其中键是权重百分比，值是要分配的值。 密钥可以是百分比或星号。 在采用所有百分比后，星号表示整体的其余部分。 对于20％的客户端IP地址，$variant变量的值为backendv2，对于剩余的80％，变量值为backendv1。

在此示例中，backendv1和backendv2表示上游服务器池，可以与proxy_pass指令一起使用，如下所示：

```
location / {
        proxy_pass http://$variant
}
```

使用变量$variant，流量将在两个不同的应用程序服务器池之间分配。

### 详解
在针对电子商务网站上的转化率测试不同类型的营销和前端功能时，此类A/B测试非常有用。 应用程序通常使用称为canary release的部署类型。 在此类部署中，流量会缓慢切换到新版本。 在推出新版本的代码时，在不同版本的应用程序之间拆分客户端非常有用，以便在发生错误时限制影响范围。 无论在两个不同的应用程序集之间拆分客户端的原因是什么，NGINX通过使用这个split_cli ents模块简化了这一过程。

### 其他参考
[split_client Documentation](http://nginx.org/en/docs/http/ngx_http_split_clients_module.html)
