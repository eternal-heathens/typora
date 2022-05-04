# RequestInterceptor

在使用[feign](https://so.csdn.net/so/search?q=feign&spm=1001.2101.3001.7020)做服务间调用的时候，如何修改请求的头部或编码信息呢，可以通过实现RequestInterceptor接口的apply方法，feign在发送请求之前都会调用该接口的apply方法，所以我们也可以通过实现该接口来记录请求发出去的时间点。

## Interface

### RequestInterceptor

* RequestInterceptor接口定义了apply方法，其参数为RequestTemplate；它有一个抽象类为BaseRequestInterceptor，还有几个实现类分别为BasicAuthRequestInterceptor、FeignAcceptGzipEncodingInterceptor、FeignContentGzipEncodingIntercept

## AbstractClass

### BasicAuthRequestInterceptor

* BasicAuthRequestInterceptor实现了RequestInterceptor接口，其apply方法往RequestTemplate添加名为Authorization的header

```java
public class BasicAuthRequestInterceptor implements RequestInterceptor {
 
  private final String headerValue;
 

  public BasicAuthRequestInterceptor(String username, String password) {
    this(username, password, ISO_8859_1);
  }

  public BasicAuthRequestInterceptor(String username, String password, Charset charset) {
    checkNotNull(username, "username");
    checkNotNull(password, "password");
    this.headerValue = "Basic " + base64Encode((username + ":" + password).getBytes(charset));
  }

  private static String base64Encode(byte[] bytes) {
    return Base64.encode(bytes);
  }
 
  @Override
  public void apply(RequestTemplate template) {
    template.header("Authorization", headerValue);
  }
}
```

### BaseRequestInterceptor

* BaseRequestInterceptor定义了addHeader方法，往requestTemplate添加非重名的header

```java
public abstract class BaseRequestInterceptor implements RequestInterceptor {
 
    /**
     * The encoding properties.
     */
    private final FeignClientEncodingProperties properties;

    protected BaseRequestInterceptor(FeignClientEncodingProperties properties) {
        Assert.notNull(properties, "Properties can not be null");
        this.properties = properties;
    }
 

    protected void addHeader(RequestTemplate requestTemplate, String name,
            String... values) {
 
        if (!requestTemplate.headers().containsKey(name)) {
            requestTemplate.header(name, values);
        }
    }
 
    protected FeignClientEncodingProperties getProperties() {
        return this.properties;
    }
 
}
```

### FeignAcceptGzipEncodingInterceptor

* FeignAcceptGzipEncodingInterceptor继承了BaseRequestInterceptor，它的apply方法往RequestTemplate添加了名为Accept-Encoding，值为gzip,deflate的header

```java
public class FeignAcceptGzipEncodingInterceptor extends BaseRequestInterceptor {
 
 
    protected FeignAcceptGzipEncodingInterceptor(
            FeignClientEncodingProperties properties) {
        super(properties);
    }
 

    @Override
    public void apply(RequestTemplate template) {
 
        addHeader(template, HttpEncoding.ACCEPT_ENCODING_HEADER,
                HttpEncoding.GZIP_ENCODING, HttpEncoding.DEFLATE_ENCODING);
    }
 
}
```

## 配置方式

1. 实现RequestInterceptor的实现类

2. 配置到FeignClient

```java
1. @FeignClient(value = "${feign.client.config.house-asset-api.name}",
    url = "${feign.client.config.house-asset-api.url}",
    configuration = {FeignConfiguration.class})
2. 实例化bean时，new fegin.build.xxxx.requestInterceptor(new xxRequestInterceptor).target(xxx,xxx)
```

## 使用方式

### 配置header

```java
@Component
public class FeignRequestInterceptor implements RequestInterceptor {
	@Override
	public void apply(RequestTemplate requestTemplate) {
		ServletRequestAttributes attributes = (ServletRequestAttributes) RequestContextHolder.getRequestAttributes();
		if (attributes != null) {
			HttpServletRequest request = attributes.getRequest();
			Enumeration<String> headerNames = request.getHeaderNames();
			if (headerNames != null) {
				while (headerNames.hasMoreElements()) {
					String name = headerNames.nextElement();
					String values = request.getHeader(name);
					requestTemplate.header(name, values);
				}
			}
		}
	}
}

```

通过RequestContextHolder.getRequestAttributes()获取不到请求：

原因分析：

> debug查看相关代码后，发现代码走到拦截器中时，当前线程变成"hystrix-"开头的线程名称，并不是http线程池中的线程，由于从RequestContextHolder获取request时是从ThreadLocal中获取，线程变了当然也就获取不到了。因为线程变成"hystrix-"开头的线程名称所以猜测是由于开启了熔断导致的，网上搜索相关信息后才知道，开启熔断后feign调用会根据hystrix默认的并发策略，在单独的线程池中运行。

解决方式：

1. 关闭hystrix 修改配置

   ```properties
   feign.hystrix.enabled=false //直接关闭hystrix当然是可以解决的，但是显然不合适
   ```
   
2. 配置hystrix并发策略为信号量模式 

```properties
hystrix.command.default.execution.isolation.strategy=SEMAPHORE //所谓信号量模式就是不单独为每一个FeignClient分配线程池，而是限制每一个FeignClient调用的线程数，线程池还是用的http的线程池，feign调用线程不会变换就可以获取到request。但是Hystrix官方并不建议使用这种模式，特别是下游接口响应不快的时候会长时间http线程池影响性能。
```

3. 自定义hystrix并发策略

``` java
//https://blog.csdn.net/chengpei147/article/details/121385433
public class RequestHeaderHystrixConcurrencyStrategy extends HystrixConcurrencyStrategy {
    xxx
}
```





# http和rpc

成熟的rpc库相对普通的http容器，更多的是封装了“服务发现”，"负载均衡"，“熔断降级”一类面向服务的高级特性。可以这么理解，rpc框架是面向服务的更高级的封装。**如果把一个http servlet容器上封装一层服务发现和函数代理调用，那它就已经可以做一个rpc框架了。**

传统rpc中tcp传输协议的自定义可以让传输效率定制化，但是随着http2的推出很多效率问题已经不大了，而且http协议有跨语言的好处，因此也可以依据http封装一层服务治理相关的特性生成一套组件，也是一个rpc的发展方向（如以前的grpc和springcloud的fegin）





# Feign和RestTemplate的区别

请求方式不一样

RestTemplate需要每个请求都拼接url+参数+类文件，灵活性高但是消息封装臃肿。

feign可以伪装成类似SpringMVC的controller一样，将rest的请求进行隐藏，不用再自己拼接url和参数，可以便捷优雅地调用HTTP API。

底层实现方式不一样

RestTemplate在拼接url的时候，可以直接指定ip地址+端口号，不需要经过服务注册中心就可以直接请求接口；也可以指定服务名，请求先到服务注册中心（如nacos）获取对应服务的ip地址+端口号，然后经过HTTP转发请求到对应的服务接口（注意：这时候的restTemplate需要添加@LoadBalanced注解，进行负载均衡）。

Feign的底层实现是动态代理，如果对某个接口进行了@FeignClient注解的声明，Feign就会针对这个接口创建一个动态代理的对象，在调用这个接口的时候，其实就是调用这个接口的代理对象，代理对象根据@FeignClient注解中name的值在服务注册中心找到对应的服务，然后再根据@RequestMapping等其他注解的映射路径构造出请求的地址，针对这个地址，再从本地实现HTTP的远程调用。
