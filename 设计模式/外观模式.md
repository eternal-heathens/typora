# 外观模式(Facade)

#### 一、概述

为子系统中的一组接口提供一个一致的界面，Facade模式定义了一个高层接口，这个接口使得这一子系统更加容易使用。

#### 二、适用性

1.当你要为一个复杂子系统提供一个简单接口时。子系统往往因为不断演化而变得越来越 复杂。大多数模式使用时都会产生更多更小的类。这使得子系统更具可重用性，也更容 易对子系统进行定制，但这也给那些不需要定制子系统的用户带来一些使用上的困难。 Facade可以提供一个简单的缺省视图，这一视图对大多数用户来说已经足够，而那些需 要更多的可定制性的用户可以越过facade层。

2.客户程序与抽象类的实现部分之间存在着很大的依赖性。引入facade将这个子系统与客 户以及其他的子系统分离，可以提高子系统的独立性和可移植性。

3.当你需要构建一个层次结构的子系统时，使用facade模式定义子系统中每层的入口点。 如果子系统之间是相互依赖的，你可以让它们仅通过facade进行通讯，从而简化了它们 之间的依赖关系。

#### 三、参与者

1.Facade 知道哪些子系统类负责处理请求。 将客户的请求代理给适当的子系统对象。

2.Subsystemclasses 实现子系统的功能。 处理由Facade对象指派的任务。 没有facade的任何相关信息；即没有指向facade的指针。

#### 四、类图

![img](https://img-blog.csdn.net/20150507232644221?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbDEwMjgzODY4MDQ=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

#### 五、示例

**Facade**



```java
package com.lyz.design.facade;

/**
 * Facade 
 * @author liuyazhuang

 *

 */

public class Facade {

    ServiceA sa;

    ServiceB sb;

    ServiceC sc;
    public Facade() {
        sa = new ServiceAImpl();
        sb = new ServiceBImpl();
        sc = new ServiceCImpl(); 
    }
    public void methodA() {
        sa.methodA();
      	sb.methodB();
    }

    public void methodB() {
        sb.methodB();
        sc.methodC();
    }
    public void methodC() {
        sc.methodC();
  	    sa.methodA();



    }
}
```


**Subsystemclasses**





```java
package com.lyz.design.facade;



/**



 * Subsystemclasses 



 * @author liuyazhuang



 *



 */



public class ServiceAImpl implements ServiceA {



    public void methodA() {



        System.out.println("这是服务A");



    }



}
```



```java
package com.lyz.design.facade;



/**



 * Subsystemclasses 



 * @author liuyazhuang



 *



 */



public class ServiceBImpl implements ServiceB {



    public void methodB() {



        System.out.println("这是服务B");



    }



}
```



```java
package com.lyz.design.facade;



/**



 * Subsystemclasses 



 * @author liuyazhuang



 *



 */



public class ServiceCImpl implements ServiceC {



    public void methodC() {



        System.out.println("这是服务C");



    }



}
```



```java
package com.lyz.design.facade;



/**



 * Test



 * @author liuyazhuang



 *



 */



public class Test {



    public static void main(String[] args) {



    	ServiceA sa = new ServiceAImpl();



    	ServiceB sb = new ServiceBImpl();



        sa.methodA();



        sb.methodB();



        System.out.println("========");



        //facade



        Facade facade = new Facade();



        facade.methodA();



        facade.methodB();



    }



}
```


**result**





```java
这是服务A



这是服务B



========



这是服务A



这是服务B



这是服务B
```