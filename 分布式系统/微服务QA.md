# 微服务QA

这个回答里恰巧讲了一些rpc通信协议的细节，但是强调一遍通信协议不是rpc最重要的部分，不要被这个回答带偏了。如果要了解rpc请更多的去了解服务治理(soa)的一些基本策略,推荐去看看dubbo的文档。

这个问题其实是有理解误区的，首先 http 和 rpc 并不是一个并行概念。

rpc是远端过程调用，其调用协议通常包含传输协议和序列化协议。

传输协议包含: 如著名的 \[gRPC]\([grpc / grpc.io](https://link.zhihu.com/?target=http%3A//www.grpc.io)) 使用的 http2 协议，也有如dubbo一类的自定义报文的tcp协议。

序列化协议包含: 如基于文本编码的 xml json，也有[二进制编码](https://www.zhihu.com/search?q=%E4%BA%8C%E8%BF%9B%E5%88%B6%E7%BC%96%E7%A0%81\&search\_source=Entity\&hybrid\_search\_source=Entity\&hybrid\_search\_extra=\{%22sourceType%22%3A%22answer%22%2C%22sourceId%22%3A191965937})的 protobuf hessian等。

因此我理解的你想问的问题应该是：**为什么要使用自定义 tcp 协议的 rpc 做后端进程通信？**

要解决这个问题就应该搞清楚 http 使用的 tcp 协议，和我们自定义的 tcp 协议在报文上的区别。

首先要否认一点 http 协议相较于自定义[tcp报文协议](https://www.zhihu.com/search?q=tcp%E6%8A%A5%E6%96%87%E5%8D%8F%E8%AE%AE\&search\_source=Entity\&hybrid\_search\_source=Entity\&hybrid\_search\_extra=\{%22sourceType%22%3A%22answer%22%2C%22sourceId%22%3A191965937})，增加的开销在于连接的建立与断开。[http协议](https://www.zhihu.com/search?q=http%E5%8D%8F%E8%AE%AE\&search\_source=Entity\&hybrid\_search\_source=Entity\&hybrid\_search\_extra=\{%22sourceType%22%3A%22answer%22%2C%22sourceId%22%3A191965937})是支持连接池复用的，也就是建立一定数量的连接不断开，并不会频繁的创建和销毁连接。二一要说的是http也可以使用protobuf这种[二进制编码协议](https://www.zhihu.com/search?q=%E4%BA%8C%E8%BF%9B%E5%88%B6%E7%BC%96%E7%A0%81%E5%8D%8F%E8%AE%AE\&search\_source=Entity\&hybrid\_search\_source=Entity\&hybrid\_search\_extra=\{%22sourceType%22%3A%22answer%22%2C%22sourceId%22%3A191965937})对内容进行编码，因此二者最大的区别还是在传输协议上。

通用定义的http1.1协议的tcp报文包含太多废信息，一个POST协议的格式大致如下

```
HTTP/1.0 200 OK 
Content-Type: text/plain
Content-Length: 137582
Expires: Thu, 05 Dec 1997 16:00:00 GMT
Last-Modified: Wed, 5 August 1996 15:55:28 GMT
Server: Apache 0.84

<html>
  <body>Hello World</body>
</html>
```

即使编码协议也就是body是使用二进制编码协议，报文元数据也就是header头的键值对却用了文本编码，非常占字节数。如上图所使用的报文中有效字节数仅仅占约 30%，也就是70%的时间用于传输元数据废编码。当然实际情况下报文内容可能会比这个长，但是报头所占的比例也是非常可观的。

那么假如我们使用自定义[tcp协议](https://www.zhihu.com/search?q=tcp%E5%8D%8F%E8%AE%AE\&search\_source=Entity\&hybrid\_search\_source=Entity\&hybrid\_search\_extra=\{%22sourceType%22%3A%22answer%22%2C%22sourceId%22%3A191965937})的报文如下

报头占用的字节数也就只有16个byte，极大地精简了传输内容。

这也就是为什么后端进程间通常会采用自定义tcp协议的rpc来进行通信的原因。

可能回答里面没有描述清楚，这个回答仅仅是用来纠正victory的回答的。所谓的效率优势是针对http1.1协议来讲的，http2.0协议已经优化编码效率问题，像grpc这种rpc库使用的就是http2.0协议。这么来说吧http容器的性能测试单位通常是kqps，自定义tpc协议则通常是以10kqps到100kqps为基准

简单来说成熟的rpc库相对[http容器](https://www.zhihu.com/search?q=http%E5%AE%B9%E5%99%A8\&search\_source=Entity\&hybrid\_search\_source=Entity\&hybrid\_search\_extra=\{%22sourceType%22%3A%22answer%22%2C%22sourceId%22%3A191965937})，更多的是封装了“服务发现”，"负载均衡"，“熔断降级”一类面向服务的高级特性。可以这么理解，[rpc框架](https://www.zhihu.com/search?q=rpc%E6%A1%86%E6%9E%B6\&search\_source=Entity\&hybrid\_search\_source=Entity\&hybrid\_search\_extra=\{%22sourceType%22%3A%22answer%22%2C%22sourceId%22%3A191965937})是面向服务的更高级的封装。如果把一个http servlet容器上封装一层服务发现和函数代理调用，那它就已经可以做一个rpc框架了。

所以为什么要用rpc调用？

因为良好的rpc调用是面向服务的封装，针对服务的可用性和效率等都做了优化。单纯使用http调用则缺少了这些特性。
