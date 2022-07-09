https://mp.weixin.qq.com/s/iKwgG6pXiqeomtOjOw0mYQ

https://www.zhihu.com/question/42061683?sort=created

https://cloud.tencent.com/developer/article/1362885

# SOA定义

**面向服务的体系结构**（英语：service-oriented architecture）并不特指一种技术，而是一种分散式运算的软体设计方法。软体的部分组件（呼叫者），可以透过网路上的通用协定呼叫另一个应用软体元件执行、运作，让呼叫者获得服务。SOA原则上采用[开放标准](https://zh.m.wikipedia.org/wiki/开放标准)、与软体资源进行[交互](https://zh.m.wikipedia.org/w/index.php?title=交互&action=edit&redlink=1)并采用表示的标准方式。因此应能跨越厂商、产品与技术。一项服务应视为一个独立的功能单元，可以远端存取并独立执行与更新，例如在线查询信用卡帐单。

现在的SOA业界实现和微服务在以下几方面有区别：ESB、BPM、WebService（及其技术规范）

有网友这么形容SOA,ESB,WebService的关系

> SOA是方法论，就像建筑学一样，指导性质的；
> ESB是建筑图纸，理顺整个建筑的架构；
> Web S是具体的建筑材料，就好像预制板；

# ESB

ESB（EnterpriseServiceBus，企业服务总线），指的是**传统中间件技术与XML、Web服务等技术结合的产物**。是SOA技术架构落地的一个基本部件，现在我们说的SOA集成平台基本都需要有ESB组件，ESB最基本功能即是实现点对点集成到总线式集成的转换，在这个过程中实现了消息协议的转换和适配，数据传输，数据转换和映射，路由等基本功能。ESB提供了网络中最基本的连接中枢，是构筑企业神经系统的必要元素。简单来说ESB就是一根管道，用来连接各个服务节点。为了集成不同系统，不同协议的服务，ESB做了消息的转化解释和路由工作，让不同的服务互联互通。

SOA和微服务很相像，只是在实现上率先脱颖而出的是ESB，而ESB是由中间件技术实现并支持SOA的一组基础架构功能

基本交互过程：

1. 首先由服务请求者触发一次交互过程，产生一个服务请求消息，并将该消息按照ESB的要求标准化，然后将标准化的信息发送给服务总线
2. ESB根据请求消息中的服务名或接口名进行目的组件查找，将消息转发至目的组件，并最终将处理结果逆向返回给服务请求者

![img](https://raw.githubusercontent.com/eternal-heathens/picgoBeg/master/JavaImages/202206191510498.png)

这种交互过程不再是点到点的直接交互模式，而是由事件驱动的消息交互模式，由于将各个服务组件的依赖管理前移到了ESB中，因此为了ESB对于依赖管理的复杂度不随着时间而指数爆炸导致依赖混乱，往ESB中申请服务需要一套单独的流程管理BMP，作为支撑ESB的辅助解决方案

# BPM

![图片](https://raw.githubusercontent.com/eternal-heathens/picgoBeg/master/JavaImages/202206191517781.png)

ESB和BPM的基础中间件结合，BPM作为一个业务流程管理平台，很好的将SOA服务通过流程建模的形式，与业务流程逻辑联系在一起。那么这个过程中，BPM支撑SOA架构的业务流程协作问题，ESB支撑SOA架构的数据交换问题。

可以看出BPM这套流程明显的缺点与优点

* 优点：完善的流程可以很好地让新来的人的维护理解整个业务系统，适合本身比较稳定，复杂的系统
* 缺点：流程相较于直接注册变长了，并且需要人员水平较高

**微服务相较之下在产品评审完后便进入常规的敏捷开发的流程，不需要思考对ESB的影响**

# **WebService**

WebService是SOA设计原则下，服务组件的设计原则

其需要实现三个方向的技术规范/协议

> **WSDL（Web ServiceDescription Language，Web 服务描述语言）** 是对服务进行描述的语言，它有一套基于 XML 的语法定义。WSDL 描述的重点是服务，它包含服务实现定义和服务接口定义。
>
> **UDDI（Universal DescriptionDiscovery and Integration，统一描述、发现和集成）** 提供了一种服务发布、查找和定位的方法，是服务的信息注册规范，以便被需要该服务的用户发现和使用它。UDDI 规范描述了服务的概念，同时也定义了一种编程接口。通过 UDDI 提供的标准接口，企业可以发布自己的服务供其他企业查询和调用，也可以查询特定服务的描述信息，并动态绑定到该服务上。
>
> **SOAP（Simple ObjectAccess Protocol，简单对象访问协议）** 定义了服务请求者和服务提供者之间的消息传输规范，SOAP 用 XML 来格式化消息，用 HTTP 来承载消息。通过 SOAP，应用程序可以在网络中进行数据交换和远程过程调用（Remote Procedure Call， RPC）。

![图片](https://raw.githubusercontent.com/eternal-heathens/picgoBeg/master/JavaImages/202206191532359.png)

其中SOAP作为通信协议，在使用情况上来看，

管理方面，xml的规范性可以更容易学习与维护，如maven和gradle

而在数据方面，目前来看json性能与简易性上更占优势

在微服务上，通信协议目前跟依赖于以json格式交互的rest协议（http+json）和以socket为首的rpc



# 总结

目前来看soa基于esb的实现，因为将依赖关系全部由esb管理路由，导致容易出现单点问题，基于他的服务治理，如服务发现、负载均衡、限流、熔断、超时、重试、服务追踪，都会加重整个ESB依赖关系的复杂度，而且BPM也不利于抢占市场，webService与ESB高耦合，其基础的xml也相对于json在开发阶段显得成本较高，因此微服务API网关+点到点的直接交互模式，并以此为基础的服务治理方案，仍更适合现在模式

而现在云原生的兴起，容器docker和容器服务编排的k8s使得云原生的容器可以与paas和Iaas可以平滑的交流以及基于编排的k8s，利用基础设施即代码（IaC）和管道来实现配置的自动化从而达到DevOps，提供各种各样的DevOps工具，从根本上支持模块化以及自动化的云原生应用

在2018将service mesh加进来后，可能这套服务间的通信机制更像SOA最好的实现，换了份APi+点到点的直接交互模式的建筑图纸，只是面向每个服务的服务治理的总线，而不是由事件驱动负责各个服务之间的通信总线















