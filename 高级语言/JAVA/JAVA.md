# 三种工厂模式

## 简单工厂模式

==(属于工厂方法模式，属于静态工厂方法模式)==

一个栗子： 
我喜欢吃面条，抽象一个面条基类，(接口也可以)，这是产品的抽象类。

```
public abstract class INoodles {
    /**
     * 描述每种面条啥样的
     */
    public abstract void desc();
}(javascript:void(0);
```

先来一份兰州拉面（具体的产品类）：

```
public class LzNoodles extends INoodles {
    @Override
    public void desc() {
        System.out.println("兰州拉面 上海的好贵 家里才5 6块钱一碗");
    }
}
```

程序员加班必备也要吃泡面（具体的产品类）：

```
public class PaoNoodles extends INoodles {
    @Override
    public void desc() {
        System.out.println("泡面好吃 可不要贪杯");
    }
}
```

还有我最爱吃的家乡的干扣面（具体的产品类）：

```
public class GankouNoodles extends INoodles {
    @Override
    public void desc() {
        System.out.println("还是家里的干扣面好吃 6块一碗");
    }
}
```

准备工作做完了，我们来到一家“简单面馆”（简单工厂类），菜单如下：

```java
public class SimpleNoodlesFactory {
    public static final int TYPE_LZ = 1;//兰州拉面
    public static final int TYPE_PM = 2;//泡面
    public static final int TYPE_GK = 3;//干扣面

    public static INoodles createNoodles(int type) {
        switch (type) {
            case TYPE_LZ:
                return new LzNoodles();
            case TYPE_PM:
                return new PaoNoodles();
            case TYPE_GK:
            default:
                return new GankouNoodles();
        }
    }
}
```

简单面馆就提供三种面条（产品），你说你要啥，他就给你啥。这里我点了一份干扣面:

```
/**
 * 简单工厂模式
 */
 INoodles noodles = SimpleNoodlesFactory.createNoodles(SimpleNoodlesFactory.TYPE_GK);
 noodles.desc();
```

输出：

```
还是家里的干扣面好吃 6块一碗
```

##### 特点

1 它是一个具体的类，非接口 抽象类。有一个重要的create()方法，利用if或者 switch创建产品并返回。

2 create()方法通常是静态的，所以也称之为静态工厂。

##### 缺点

1 扩展性差（我想增加一种面条，除了新增一个面条产品类，还需要修改工厂类方法）

2 不同的产品需要不同额外参数的时候 不支持。

 

## 工厂方法模式

##### 模式描述

提供一个用于创建对象的接口(工厂接口)，让其实现类(工厂实现类)决定实例化哪一个类(产品类)，并且由该实现类创建对应类的实例。

##### 模式作用

可以一定程度上解耦，消费者和产品实现类隔离开，只依赖产品接口(抽象产品)，产品实现类如何改动与消费者完全无关。

可以一定程度增加扩展性，若增加一个产品实现，只需要实现产品接口，修改工厂创建产品的方法，消费者可以无感知（若消费者不关心具体产品是什么的情况）。
可以一定程度增加代码的封装性、可读性。清楚的代码结构，对于消费者来说很少的代码量就可以完成很多工作。
等等。
另外，抽象工厂才是实际意义的工厂模式，工厂方法只是抽象工厂的一个比较常见的情况。

##### 适用场景

消费者不关心它所要创建对象的类(产品类)的时候。

消费者知道它所要创建对象的类(产品类)，但不关心如何创建的时候。

等等。

例如：hibernate里通过sessionFactory创建session、通过代理方式生成ws客户端时，通过工厂构建报文中格式化数据的对象。

##### 模式要素

提供一个产品类的接口。产品类均要实现这个接口(也可以是abstract类，即抽象产品)。
提供一个工厂类的接口。工厂类均要实现这个接口(即抽象工厂)。
由工厂实现类创建产品类的实例。工厂实现类应有一个方法，用来实例化产品类。

##### 类图

![img](http://img.blog.csdn.net/20150114151111882?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvb29wcG9va2lk/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

##### 模式实例代码

工厂：



```java
package com.demoFound.factoryMethod.factory;  
  
import com.demoFound.factoryMethod.message.IMyMessage;  
  
/** 
 * 工厂方法模式_工厂接口 
 *  
 * @author popkidorc 
 *  
 */  
public interface IMyMessageFactory {  
  
    public IMyMessage createMessage(String messageType);  
}  
```

```java
package com.demoFound.factoryMethod.factory;  
  
import java.util.HashMap;  
import java.util.Map;  
  
import com.demoFound.factoryMethod.message.IMyMessage;  
import com.demoFound.factoryMethod.message.MyMessageEmail;  
import com.demoFound.factoryMethod.message.MyMessageOaTodo;  
import com.demoFound.factoryMethod.message.MyMessageSms;  
  
/** 
 * 工厂方法模式_工厂实现 
 *  
 * @author popkidorc 
 *  
 */  
public class MyMessageFactory implements IMyMessageFactory {  
  
    @Override  
    public IMyMessage createMessage(String messageType) {  
        // 这里的方式是：消费者知道自己想要什么产品；若生产何种产品完全由工厂决定，则这里不应该传入控制生产的参数。  
        IMyMessage myMessage;  
        Map<String, Object> messageParam = new HashMap<String, Object>();  
        // 根据某些条件去选择究竟创建哪一个具体的实现对象，条件可以传入的，也可以从其它途径获取。  
        // sms  
        if ("SMS".equals(messageType)) {  
            myMessage = new MyMessageSms();  
            messageParam.put("PHONENUM", "123456789");  
        } else  
        // OA待办  
        if ("OA".equals(messageType)) {  
            myMessage = new MyMessageOaTodo();  
            messageParam.put("OAUSERNAME", "testUser");  
        } else  
        // email  
        if ("EMAIL".equals(messageType)) {  
            myMessage = new MyMessageEmail();  
            messageParam.put("EMAIL", "test@test.com");  
        } else  
        // 默认生产email这个产品  
        {  
            myMessage = new MyMessageEmail();  
            messageParam.put("EMAIL", "test@test.com");  
        }  
        myMessage.setMessageParam(messageParam);  
        return myMessage;  
    }  
} 
```



产品：



```java
package com.demoFound.factoryMethod.message;  
  
import java.util.Map;  
  
/** 
 * 工厂方法模式_产品接口 
 *  
 * @author popkidorc 
 *  
 */  
public interface IMyMessage {  
  
    public Map<String, Object> getMessageParam();  
  
    public void setMessageParam(Map<String, Object> messageParam);  
  
    public void sendMesage() throws Exception;// 发送通知/消息  
  
}  
```



```
package com.demoFound.factoryMethod.message;  
  
import java.util.Map;  
  
/** 
 * 工厂方法模式_虚拟产品类 
 *  
 * @author popkidorc 
 *  
 */  
public abstract class MyAbstractMessage implements IMyMessage {  
  
    private Map<String, Object> messageParam;// 这里可以理解为生产产品所需要的原材料库。最好是个自定义的对象，这里为了不引起误解使用Map。  
  
    @Override  
    public Map<String, Object> getMessageParam() {  
        return messageParam;  
    }  
  
    @Override  
    public void setMessageParam(Map<String, Object> messageParam) {  
        this.messageParam = messageParam;  
    }  
}  
```

```java
package com.demoFound.factoryMethod.message;  
  
/** 
 * 工厂方法模式_email产品 
 *  
 * @author popkidorc 
 *  
 */  
public class MyMessageEmail extends MyAbstractMessage {  
  
    @Override  
    public void sendMesage() throws Exception {  
        // TODO Auto-generated method stub  
        if (null == getMessageParam() || null == getMessageParam().get("EMAIL")  
                || "".equals(getMessageParam().get("EMAIL"))) {  
            throw new Exception("发送短信,需要传入EMAIL参数");// 为了简单起见异常也不自定义了  
        }// 另外邮件内容，以及其他各种协议参数等等都要处理  
  
        System.out.println("我是邮件，发送通知给" + getMessageParam().get("EMAIL"));  
    }  
  
}  
```

```
package com.demoFound.factoryMethod.message;  
  
/** 
 * 工厂方法模式_oa待办产品 
 *  
 * @author popkidorc 
 *  
 */  
public class MyMessageOaTodo extends MyAbstractMessage {  
  
    @Override  
    public void sendMesage() throws Exception {  
        // TODO Auto-generated method stub  
        if (null == getMessageParam()  
                || null == getMessageParam().get("OAUSERNAME")  
                || "".equals(getMessageParam().get("OAUSERNAME"))) {  
            throw new Exception("发送OA待办,需要传入OAUSERNAME参数");// 为了简单起见异常也不自定义了  
        }// 这里的参数需求就比较多了不一一处理了  
  
        System.out  
                .println("我是OA待办，发送通知给" + getMessageParam().get("OAUSERNAME"));  
    }  
  
}  
```

```
package com.demoFound.factoryMethod.message;  
  
/** 
 * 工厂方法模式_sms产品 
 *  
 * @author popkidorc 
 *  
 */  
public class MyMessageSms extends MyAbstractMessage {  
  
    @Override  
    public void sendMesage() throws Exception {  
        // TODO Auto-generated method stub  
        if (null == getMessageParam()  
                || null == getMessageParam().get("PHONENUM")  
                || "".equals(getMessageParam().get("PHONENUM"))) {  
            throw new Exception("发送短信,需要传入PHONENUM参数");// 为了简单起见异常也不自定义了  
        }// 另外短信信息，以及其他各种协议参数等等都要处理  
  
        System.out.println("我是短信，发送通知给" + getMessageParam().get("PHONENUM"));  
    }  
  
} 
```



消费者：



```java
package com.demoFound.factoryMethod;  
  
import com.demoFound.factoryMethod.factory.IMyMessageFactory;  
import com.demoFound.factoryMethod.factory.MyMessageFactory;  
import com.demoFound.factoryMethod.message.IMyMessage;  
  
/** 
 * 工厂方法模式_消费者类 
 *  
 * @author popkidorc 
 *  
 */  
public class MyFactoryMethodMain {  
  
    public static void main(String[] args) {  
        IMyMessageFactory myMessageFactory = new MyMessageFactory();  
        IMyMessage myMessage;  
        // 对于这个消费者来说，不用知道如何生产message这个产品，耦合度降低  
        try {  
            // 先来一个短信通知  
            myMessage = myMessageFactory.createMessage("SMS");  
            myMessage.sendMesage();  
  
            // 来一个oa待办  
            myMessage = myMessageFactory.createMessage("OA");  
            myMessage.sendMesage();  
  
            // 来一个邮件通知  
            myMessage = myMessageFactory.createMessage("EMAIL");  
            myMessage.sendMesage();  
        } catch (Exception e) {  
            e.printStackTrace();  
        }  
    }  
}  
```

 

### 抽象工厂模式

定义：为创建一组相关或相互依赖的对象提供一个接口，而且无需指定他们的具体类。

类型：创建类模式



抽象工厂模式与工厂方法模式的区别

​    抽象工厂模式是工厂方法模式的升级版本，他用来创建一组相关或者相互依赖的对象。他与工厂方法模式的区别就在于，工厂方法模式针对的是一个产品等级结构；而抽象工厂模式则是针对的多个产品等级结构。在编程中，通常一个产品结构，表现为一个接口或者抽象类，也就是说，==工厂方法模式提供的所有产品都是衍生自同一个接口或抽象类，而抽象工厂模式所提供的产品则是衍生自不同的接口或抽象类。==

​    **在抽象工厂模式中，有一个产品族的概念：所谓的产品族，是指位于不同产品等级结构中功能相关联的产品组成的家族。抽象工厂模式所提供的一系列产品就组成一个产品族；而工厂方法提供的一系列产品称为一个等级结构。**



​    明白了等级结构和产品族的概念，就理解工厂方法模式和抽象工厂模式的区别了，如果工厂的产品全部属于同一个等级结构，则属于工厂方法模式；如果工厂的产品来自多个等级结构，则属于抽象工厂模式。

抽象工厂模式代码



```java
interface IProduct1 {  
    public void show();  
}  
interface IProduct2 {  
    public void show();  
}  
  
class Product1 implements IProduct1 {  
    public void show() {  
        System.out.println("这是1型产品");  
    }  
}  
class Product2 implements IProduct2 {  
    public void show() {  
        System.out.println("这是2型产品");  
    }  
}  
  
interface IFactory {  
    public IProduct1 createProduct1();  
    public IProduct2 createProduct2();  
}  
class Factory implements IFactory{  
    public IProduct1 createProduct1() {  
        return new Product1();  
    }  
    public IProduct2 createProduct2() {  
        return new Product2();  
    }  
}  
  
public class Client {  
    public static void main(String[] args){  
        IFactory factory = new Factory();  
        factory.createProduct1().show();  
        factory.createProduct2().show();  
    }  
} 
```



抽象工厂模式的优点

​    抽象工厂模式除了具有工厂方法模式的优点外，最主要的优点就是可以在类的内部对产品族进行约束。所谓的产品族，一般或多或少的都存在一定的关联，抽象工厂模式就可以在类内部对产品族的关联关系进行定义和描述，而不必专门引入一个新的类来进行管理。

 

抽象工厂模式的缺点

​    产品族的扩展将是一件十分费力的事情，假如产品族中需要增加一个新的产品，则几乎所有的工厂类都需要进行修改。所以使用抽象工厂模式时，对产品等级结构的划分是非常重要的。

 

适用场景

​    当需要创建的对象是一系列相互关联或相互依赖的产品族时，便可以使用抽象工厂模式。说的更明白一点，就是一个继承体系中，如果存在着多个等级结构（即存在着多个抽象类），并且分属各个等级结构中的实现类之间存在着一定的关联或者约束，就可以使用抽象工厂模式。假如各个等级结构中的实现类之间不存在关联或约束，则使用多个独立的工厂来对产品进行创建，则更合适一点。

 

总结

​    无论是简单工厂模式，工厂方法模式，还是抽象工厂模式，他们都属于工厂模式，在形式和特点上也是极为相似的，他们的最终目的都是为了解耦。在使用时，我们不必去在意这个模式到底工厂方法模式还是抽象工厂模式，因为他们之间的演变常常是令人琢磨不透的。经常你会发现，明明使用的工厂方法模式，当新需求来临，稍加修改，加入了一个新方法后，由于类中的产品构成了不同等级结构中的产品族，它就变成抽象工厂模式了；而对于抽象工厂模式，当减少一个方法使的提供的产品不再构成产品族之后，它就演变成了工厂方法模式。

​    所以，在使用工厂模式时，只需要关心降低耦合度的目的是否达到了。

# lambda表达式

* lambda 表达式的语法格式如下：

```java
(parameters) -> expression // 若是expression，则依据方法返回的相应类型，解语法糖时生成相应的return语句
    // 其中动态生成的代理类原理与虚指令有关，即会与生成的静态方法与代理类，代理类调用原类生成的静态方法。
或 
(parameters) ->{ statements; }
```

> 以下是lambda表达式的重要特征:
>
> - **可选类型声明：**不需要声明参数类型，编译器可以统一识别参数值。
> - **可选的参数圆括号：**一个参数无需定义圆括号，但多个参数需要定义圆括号。
> - **可选的大括号：**如果主体包含了一个语句，就不需要使用大括号。
> - **可选的返回关键字：**如果主体只有一个表达式返回值则编译器会自动返回值，大括号需要指定明表达式返回了一个数值。
> - lambda 表达式只能引用标记了 final 的外层局部变量，这就是说不能在 lambda 内部修改定义在域外的局部变量，否则会编译错误。

## lambda表达式与接口（匿名方法）

使用 Lambda 表达式需要注意以下两点：

- Lambda 表达式主要用来定义行内执行的方法类型接口，例如，一个简单方法接口。在上面例子中，我们使用各种类型的Lambda表达式来定义MathOperation接口的方法。然后我们定义了sayMessage的执行。
- Lambda **表达式免去了使用匿名方法的麻烦**，并且给予Java简单但是强大的函数化的编程能力。

* 想用lambda表达式方式书写，**这里一定只能书写一个抽象方法**！
* 并不是所有接口都可以使用Lambda表达式，只有函数式接口可以。按照Java8函数式接口的定义，其只能有一个问象方法，否则就不是函数时接口，就无法用Lambda表达版式。
* **函数式接口(Functional Interface)就是一个有且仅有一个抽象方法，但是可以有多个非抽象方法的接口。**
* lambda表达式引用的函数式接口会在jvm读取接口字节码文件时代理生成一个继承该接口并替换需要重写的方法的方法体后再方法区生成一个代理类的klazz对象放入方法区，并实例化入堆中

> 函数式接口可以被隐式转换为 lambda 表达式。
>
> 抽象百方法：在类中没有方法体的方法，就是抽象方法。
>
> Lambda 表达式和方法引用（实际上也可认为是Lambda表达式）上。
>
> 如定义了一个函数式接口如下：
>
> ```
> @FunctionalInterface
> interface GreetingService 
> {
>     void sayMessage(String message);
> }
> ```

## **eta-conversion**

`双冒号运算操作符`是类方法的句柄，`lambda` 表达式的一种简写，这种简写的学名叫 `eta-conversion` 或者叫 `η-conversion`。

通常的情况下：

- **eta-conversion：**
  把 `x -> System.out.println(x)` 简化为 `System.out::println` 的过程称之为`eta-conversion`；
- **eta-expansion：**
  把 `System.out::println` 简化为 `x -> System.out.println(x)` 的过程称之为`eta-expansion`；

双冒号运算就是 java 中的`[方法引用]`：

```
 [方法引用] 格式为：
 类名::方法名。
 
 # -----------------------------------------------------
表达式：
    person -> person.getAge();
可以替换成：
    Person::getAge

# -----------------------------------------------------
表达式：
    () -> new HashMap<>();
可以替换成：
    HashMap::new
```

# 抽象类和接口

|      | **抽象类** | **接口** |
| ---- | ----------- | ------------ |
| 关键字 | abstract | Interface |
| 成员变量 | 可包含任意合法成员变量（包括各种访问级别的类成员变量和实例成员变量） | 只能包含公开静态常量（默认由public static final修饰） |
| 构造方法 | 有构造方法 | 无构造方法 |
| 方法 | 可包含任意合法方法（包括各种访问级别的非抽象类方法和非抽象实例方法，也包含除private外的非静态抽象方法）**由abstract 修饰的方法为抽象方法** | JDK7及其以下版本JDK只能包含公开且抽象的方法（默认由public abstract修饰）**没写abstract 编译器会自动拼接**，而JDK8开始**可以包含default、static修饰的非抽象方法。**减轻了抽象类的负担和方便了匿名内部类与lambda表达式的使用 |
| 如何实现抽象方法 | 通过自定义类**继承**抽象类的方式实现抽象类的抽象方法 | 通过自定义类**implements**接口实现接口中的抽象方法，定义类可以implements多个接口 |
| 是否存在多继承 | 一个抽象类只能继承**一个**抽象或非抽象类 | 一个接口可以继承多个接口 |
| 方法默认访问权限 | JDK 1.8以前，抽象类的方法默认访问权限为protected； JDK 1.8时，抽象类的方法默认访问权限变为default | 接口的方法默认修饰符是 public abstract；JDK 1.8以前，接口中的方法必须是public的 ；JDK 1.8时，接口中的方法可以是public的，也可以是default的；JDK 1.9时，接口中的方法可以是private的 |

> 1. 普通类继承，并非一定要重写父类方法。
> 2. 抽象类继承，如果子类也是一个抽象类，并不要求一定重写父类方法。如果子类不是抽象类，则要求子类一定要实现父类中的抽象方法。
> 3. 接口类继承。如果是一个子接口，可以扩展父接口的方法；权如果是一个子抽象类，可以部分或全部实现父接口的方法；如果子类不是抽象类，则要求子类一定要实现父接口中定义的所有方法。

相同点：
（1）不能直接实例化。如果要实例化，抽象类变量必须实现所有抽象方法，接口变量必须实现所有接口未实现的方法。
（2） 都可以有实现方法（Java8 以前的接口不能有实现方法）。
（3）都可以不需要实现类或者继承者去实现所有方法（Java8 以前的接口，Java8 及以后的接口中可以包括默认方法，不需要实现者实现）。
不同点：
（1） 抽象类和接口所反映出的设计理念不同。抽象类表示的是对象 / 类 类的抽象，接口表示的是对行为的抽象。
（2）抽象类不可以多重继承，接口可以多重继承。即一个类只能继续一个抽象类，却可以继承多个接口。
（3）访问修饰符 —— 抽象类中的方法可以用（ public、protected 和 default ） abstract 修饰符，不能用 private、static、synchronize、native 修饰；变量可以在子类中重新定义，也可以重新赋值；接口的方法默认修饰符是 public abstract， Java8 开始出现静态方法，多加 static 关键字；变量默认是 public static final 型，且必须给其初值，在实现类中也不能重新定义，也不能改变其值。
（4）抽象类可以有构造器，接口没有构造器。

# 继承

![image-20200911202444162](F:\Typora数据储存\高级语言\JAVA\JAVA.assets\image-20200911202444162.png)

继承的基本概念：

（1）Java不支持多继承，也就是说子类至多只能有一个父类。

（2）子类继承了其父类中不是私有的成员变量和成员方法，作为自己的成员变量和方法。

（3）子类中定义的成员变量和父类中定义的成员变量相同时，则父类中的成员变量不能被继承。

（4）子类中定义的成员方法，并且这个方法的名字返回类型，以及参数个数和类型与父类的某个成员方法完全相同，则父类的成员方法不能被继承。

分析以上程序示例，主要疑惑点是“子类继承父类的成员变量，父类对象是否会实例化？私有成员变量是否会被继承？被继承的成员变量在哪里分配空间？”

1：虚拟机加载ExtendsDemo类，提取类型信息到方法区。

2：通过保存在方法区的字节码，虚拟机开始执行main方法，main方法入栈。

3：执行main方法的第一条指令，new Student(); 这句话就是给Student实例对象分配堆空间。因为Student继承Person父类，所以，虚拟机首先加载Person类到方法区，并**在堆中为父类成员变量在子类空间中初始化**。然后加载Student类到方法区，为Student类的成员变量分配空间并初始化默认值。将Student类的实例对象地址赋值给引用变量s。

4：接下来两条语句为成员变量赋值，由于name跟age是从父类继承而来，会被保存在子类父对象中(见图中堆中在子类实例对象中为父类成员变量分配了空间并保存了父类的引用，并没有实例化父类。)，所以就根据引用变量s持有的引用找到堆中的对象(子类对象)，然后给name跟age赋值。

4：调用say()方法，通过引用变量s持有的引用找到堆中的实例对象，通过实例对象持有的本类在方法区的引用，找到本类的类型信息，定位到say()方法。say()方法入栈。开始执行say()方法中的字节码。

5：say()方法执行完毕，say方法出栈，程序回到main方法，main方法执行完毕出栈，主线程消亡，虚拟机实例消亡，程序结束。

总结：相同的方法会被重写，变量没有重写之说，如果子类声明了跟父类一样的变量，那意味着子类将有两个相同名称的变量。一个存放在子类实例对象中，一个存放在子类空间中。父类的private变量，也会被继承并且初始化在子类父对象中，只不过对外不可见。

super关键字在java中的作用是使被屏蔽的成员变量或者成员方法变为可见，或者说用来引用被屏蔽的成员变量或成员方法，super只是记录在对象内部的父类特征(属性和方法)的一个引用。啥叫被屏蔽的成员变量或成员方法？就是==被子类重写了的方法和定义了跟父类相同的成员变量，由于不能被继承，所以就称作被屏蔽。==

因此

1. ==如果子类实现Serializable接口而父类未实现时，父类不会被序列化！==

2. ==如果父类实现序列化，子类自动实现序列化，不需要显式实现Serializable接口。==

# 泛型

#### 概述

*  泛型的本质是为了参数化类型（在不创建新的类型的情况下，通过泛型指定的不同类型来控制形参具体限制的类型）。

```
List arrayList = new ArrayList();
arrayList.add("aaaa");
arrayList.add(100);

for(int i = 0; i< arrayList.size();i++){
    String item = (String)arrayList.get(i);
    Log.d("泛型测试","item = " + item);
}

毫无疑问，程序的运行结果会以崩溃结束：

java.lang.ClassCastException: java.lang.Integer cannot be cast to java.lang.String

```

ArrayList可以存放任意类型，例子中添加了一个String类型，添加了一个Integer类型，再使用时都以String的方式使用，因此程序崩溃了。为了解决类似这样的问题（在编译阶段就可以解决），泛型应运而生。

我们将第一行声明初始化list的代码更改一下，编译器会在编译阶段就能够帮我们发现类似这样的问题。

```
List<String> arrayList = new ArrayList<String>();
...
//arrayList.add(100); 在编译阶段，编译器就会报错
```

#### 特性

```
List<String> stringArrayList = new ArrayList<String>();
List<Integer> integerArrayList = new ArrayList<Integer>();

Class classStringArrayList = stringArrayList.getClass();
Class classIntegerArrayList = integerArrayList.getClass();

if(classStringArrayList.equals(classIntegerArrayList)){
    Log.d("泛型测试","类型相同");
}
```

通过上面的例子可以证明，在编译之后程序会采取去泛型化的措施。也就是说Java中的泛型，只在编译阶段有效。在编译过程中，正确检验泛型结果后，会将泛型的相关信息擦出，并且在对象进入和离开方法的边界处添加类型检查和类型转换的方法。也就是说，泛型信息不会进入到运行时阶段。

对此总结成一句话：泛型类型在逻辑上看以看成是多个不同的类型，实际上都是相同的基本类型。

#### 泛型的使用

* 泛型有三种使用方式，分别为：泛型类、泛型接口、泛型方法

##### 泛型类

泛型类型用于类的定义中，被称为泛型类。通过泛型可以完成对一组类的操作对外开放相同的接口。最典型的就是各种容器类，如：List、Set、Map。

泛型类的最基本写法（这么看可能会有点晕，会在下面的例子中详解）：

```
class 类名称 <泛型标识：可以随便写任意标识号，标识指定的泛型的类型>{
  private 泛型标识 /*（成员变量类型）*/ var; 
  .....

  }
}
```

一个最普通的泛型类：



```
//此处T可以随便写为任意标识，常见的如T、E、K、V等形式的参数常用于表示泛型
//在实例化泛型类时，必须指定T的具体类型
public class Generic<T>{ 
    //key这个成员变量的类型为T,T的类型由外部指定  
    private T key;

    public Generic(T key) { //泛型构造方法形参key的类型也为T，T的类型由外部指定
        this.key = key;
    }

    public T getKey(){ //泛型方法getKey的返回值类型为T，T的类型由外部指定
        return key;
    }
}
```

```
//泛型的类型参数只能是类类型（包括自定义类），不能是简单类型
//传入的实参类型需与泛型的类型参数类型相同，即为Integer.
Generic<Integer> genericInteger = new Generic<Integer>(123456);

//传入的实参类型需与泛型的类型参数类型相同，即为String.
Generic<String> genericString = new Generic<String>("key_vlaue");
Log.d("泛型测试","key is " + genericInteger.getKey());
Log.d("泛型测试","key is " + genericString.getKey());
```



 

```
12-27 09:20:04.432 13063-13063/? D/泛型测试: key is 123456
12-27 09:20:04.432 13063-13063/? D/泛型测试: key is key_vlaue
```

定义的泛型类，就一定要传入泛型类型实参么？并不是这样，在使用泛型的时候如果传入泛型实参，则会根据传入的泛型实参做相应的限制，此时泛型才会起到本应起到的限制作用。如果不传入泛型类型实参的话，在泛型类中使用泛型的方法或成员变量定义的类型可以为任何的类型。

看一个例子：



```
Generic generic = new Generic("111111");
Generic generic1 = new Generic(4444);
Generic generic2 = new Generic(55.55);
Generic generic3 = new Generic(false);

Log.d("泛型测试","key is " + generic.getKey());
Log.d("泛型测试","key is " + generic1.getKey());
Log.d("泛型测试","key is " + generic2.getKey());
Log.d("泛型测试","key is " + generic3.getKey());
```



```
D/泛型测试: key is 111111
D/泛型测试: key is 4444
D/泛型测试: key is 55.55
D/泛型测试: key is false
```

 

**注意：**

- 泛型的类型参数只能是类类型，不能是简单类型。
- 不能对确切的泛型类型使用instanceof操作。如下面的操作是非法的，编译时会出错。

　　if(ex_num instanceof Generic<Number>){ }

##### 泛型接口

泛型接口与泛型类的定义及使用基本相同。泛型接口常被用在各种类的生产器中，可以看一个例子：

```
//定义一个泛型接口
public interface Generator<T> {
    public T next();
}
```

当实现泛型接口的类，未传入泛型实参时：



```
/**
 * 未传入泛型实参时，与泛型类的定义相同，在声明类的时候，需将泛型的声明也一起加到类中
 * 即：class FruitGenerator<T> implements Generator<T>{
 * 如果不声明泛型，如：class FruitGenerator implements Generator<T>，编译器会报错："Unknown class"
 */
class FruitGenerator<T> implements Generator<T>{
    @Override
    public T next() {
        return null;
    }
}
```



当实现泛型接口的类，传入泛型实参时：



```
/**
 * 传入泛型实参时：
 * 定义一个生产器实现这个接口,虽然我们只创建了一个泛型接口Generator<T>
 * 但是我们可以为T传入无数个实参，形成无数种类型的Generator接口。
 * 在实现类实现泛型接口时，如已将泛型类型传入实参类型，则所有使用泛型的地方都要替换成传入的实参类型
 * 即：Generator<T>，public T next();中的的T都要替换成传入的String类型。
 */
public class FruitGenerator implements Generator<String> {

    private String[] fruits = new String[]{"Apple", "Banana", "Pear"};

    @Override
    public String next() {
        Random rand = new Random();
        return fruits[rand.nextInt(3)];
    }
}
```



##### 泛型通配符

我们知道`Ingeter`是`Number`的一个子类，同时在特性章节中我们也验证过`Generic`与`Generic`实际上是相同的一种基本类型。那么问题来了，在使用`Generic`作为形参的方法中，能否使用`Generic`的实例传入呢？在逻辑上类似于`Generic`和`Generic`是否可以看成具有父子关系的泛型类型呢？

为了弄清楚这个问题，我们使用`Generic`这个泛型类继续看下面的例子：

```
public void showKeyValue1(Generic<Number> obj){
    Log.d("泛型测试","key value is " + obj.getKey());
}
```

 



```
Generic<Integer> gInteger = new Generic<Integer>(123);
Generic<Number> gNumber = new Generic<Number>(456);

showKeyValue(gNumber);

// showKeyValue这个方法编译器会为我们报错：Generic<java.lang.Integer> 
// cannot be applied to Generic<java.lang.Number>
// showKeyValue(gInteger);
```



 

通过提示信息我们可以看到`Generic`不能被看作为``Generic`的子类。由此可以看出:同一种泛型可以对应多个版本（因为参数类型是不确定的），不同版本的泛型类实例是不兼容的。

回到上面的例子，如何解决上面的问题？总不能为了定义一个新的方法来处理`Generic`类型的类，这显然与java中的多台理念相违背。因此我们需要一个在逻辑上可以表示同时是`Generic`和`Generic`父类的引用类型。由此类型通配符应运而生。

我们可以将上面的方法改一下：

```
public void showKeyValue1(Generic<?> obj){
    Log.d("泛型测试","key value is " + obj.getKey());
}
```

类型通配符一般是使用？代替具体的类型实参，注意了，此处’？’是类型实参，而不是类型形参 。重要说三遍！此处’？’是类型实参，而不是类型形参 ！ 此处’？’是类型实参，而不是类型形参 ！再直白点的意思就是，此处的？和Number、String、Integer一样都是一种实际的类型，可以把？看成所有类型的父类。是一种真实的类型。

可以解决当具体类型不确定的时候，这个通配符就是 ? ；当操作类型时，不需要使用类型的具体功能时，只使用Object类中的功能。那么可以用 ? 通配符来表未知类型。

#####  泛型方法

在java中,泛型类的定义非常简单，但是泛型方法就比较复杂了。

```
尤其是我们见到的大多数泛型类中的成员方法也都使用了泛型，有的甚至泛型类中也包含着泛型方法，这样在初学者中非常容易将泛型方法理解错了。
```

泛型类，是在实例化类的时候指明泛型的具体类型；泛型方法，是在调用方法的时候指明泛型的具体类型 。



```
/**
 * 泛型方法的基本介绍
 * @param tClass 传入的泛型实参
 * @return T 返回值为T类型
 * 说明：
 *     1）public 与 返回值中间<T>非常重要，可以理解为声明此方法为泛型方法。
 *     2）只有声明了<T>的方法才是泛型方法，泛型类中的使用了泛型的成员方法并不是泛型方法。
 *     3）<T>表明该方法将使用泛型类型T，此时才可以在方法中使用泛型类型T。
 *     4）与泛型类的定义一样，此处T可以随便写为任意标识，常见的如T、E、K、V等形式的参数常用于表示泛型。
 */
public <T> T genericMethod(Class<T> tClass)throws InstantiationException ,
  IllegalAccessException{
        T instance = tClass.newInstance();
        return instance;
}
```



 

```
Object obj = genericMethod(Class.forName("com.test.test"));
```

* 4.6.1 泛型方法的基本用法

光看上面的例子有的同学可能依然会非常迷糊，我们再通过一个例子，把我泛型方法再总结一下。



```
public class GenericTest {
   //这个类是个泛型类，在上面已经介绍过
   public class Generic<T>{     
        private T key;

        public Generic(T key) {
            this.key = key;
        }

        //我想说的其实是这个，虽然在方法中使用了泛型，但是这并不是一个泛型方法。
        //这只是类中一个普通的成员方法，只不过他的返回值是在声明泛型类已经声明过的泛型。
        //所以在这个方法中才可以继续使用 T 这个泛型。
        public T getKey(){
            return key;
        }

        /**
         * 这个方法显然是有问题的，在编译器会给我们提示这样的错误信息"cannot reslove symbol E"
         * 因为在类的声明中并未声明泛型E，所以在使用E做形参和返回值类型时，编译器会无法识别。
        public E setKey(E key){
             this.key = keu
        }
        */
    }

    /** 
     * 这才是一个真正的泛型方法。
     * 首先在public与返回值之间的<T>必不可少，这表明这是一个泛型方法，并且声明了一个泛型T
     * 这个T可以出现在这个泛型方法的任意位置.
     * 泛型的数量也可以为任意多个 
     *    如：public <T,K> K showKeyName(Generic<T> container){
     *        ...
     *        }
     */
    public <T> T showKeyName(Generic<T> container){
        System.out.println("container key :" + container.getKey());
        //当然这个例子举的不太合适，只是为了说明泛型方法的特性。
        T test = container.getKey();
        return test;
    }

    //这也不是一个泛型方法，这就是一个普通的方法，只是使用了Generic<Number>这个泛型类做形参而已。
    public void showKeyValue1(Generic<Number> obj){
        Log.d("泛型测试","key value is " + obj.getKey());
    }

    //这也不是一个泛型方法，这也是一个普通的方法，只不过使用了泛型通配符?
    //同时这也印证了泛型通配符章节所描述的，?是一种类型实参，可以看做为Number等所有类的父类
    public void showKeyValue2(Generic<?> obj){
        Log.d("泛型测试","key value is " + obj.getKey());
    }

     /**
     * 这个方法是有问题的，编译器会为我们提示错误信息："UnKnown class 'E' "
     * 虽然我们声明了<T>,也表明了这是一个可以处理泛型的类型的泛型方法。
     * 但是只声明了泛型类型T，并未声明泛型类型E，因此编译器并不知道该如何处理E这个类型。
    public <T> T showKeyName(Generic<E> container){
        ...
    }  
    */

    /**
     * 这个方法也是有问题的，编译器会为我们提示错误信息："UnKnown class 'T' "
     * 对于编译器来说T这个类型并未项目中声明过，因此编译也不知道该如何编译这个类。
     * 所以这也不是一个正确的泛型方法声明。
    public void showkey(T genericObj){

    }
    */

    public static void main(String[] args) {


    }
}
```



* 4.6.2 类中的泛型方法

当然这并不是泛型方法的全部，泛型方法可以出现杂任何地方和任何场景中使用。但是有一种情况是非常特殊的，当泛型方法出现在泛型类中时，我们再通过一个例子看一下



```
public class GenericFruit {
    class Fruit{
        @Override
        public String toString() {
            return "fruit";
        }
    }

    class Apple extends Fruit{
        @Override
        public String toString() {
            return "apple";
        }
    }

    class Person{
        @Override
        public String toString() {
            return "Person";
        }
    }

    class GenerateTest<T>{
        public void show_1(T t){
            System.out.println(t.toString());
        }

        //在泛型类中声明了一个泛型方法，使用泛型E，这种泛型E可以为任意类型。可以类型与T相同，也可以不同。
        //由于泛型方法在声明的时候会声明泛型<E>，因此即使在泛型类中并未声明泛型，编译器也能够正确识别泛型方法中识别的泛型。
        public <E> void show_3(E t){
            System.out.println(t.toString());
        }

        //在泛型类中声明了一个泛型方法，使用泛型T，注意这个T是一种全新的类型，可以与泛型类中声明的T不是同一种类型。
        public <T> void show_2(T t){
            System.out.println(t.toString());
        }
    }

    public static void main(String[] args) {
        Apple apple = new Apple();
        Person person = new Person();

        GenerateTest<Fruit> generateTest = new GenerateTest<Fruit>();
        //apple是Fruit的子类，所以这里可以
        generateTest.show_1(apple);
        //编译器会报错，因为泛型类型实参指定的是Fruit，而传入的实参类是Person
        //generateTest.show_1(person);

        //使用这两个方法都可以成功
        generateTest.show_2(apple);
        generateTest.show_2(person);

        //使用这两个方法也都可以成功
        generateTest.show_3(apple);
        generateTest.show_3(person);
    }
}
```



* 4.6.3 泛型方法与可变参数

再看一个泛型方法和可变参数的例子：

```
public <T> void printMsg( T... args){
    for(T t : args){
        Log.d("泛型测试","t is " + t);
    }
}
printMsg("111",222,"aaaa","2323.4",55.55);
```

* 4.6.4 静态方法与泛型

静态方法有一种情况需要注意一下，那就是在类中的静态方法使用泛型：静态方法无法访问类上定义的泛型；如果静态方法操作的引用数据类型不确定的时候，必须要将泛型定义在方法上。

即：如果静态方法要使用泛型的话，必须将静态方法也定义成泛型方法 。



```
public class StaticGenerator<T> {
    ....
    ....
    /**
     * 如果在类中定义使用泛型的静态方法，需要添加额外的泛型声明（将这个方法定义成泛型方法）
     * 即使静态方法要使用泛型类中已经声明过的泛型也不可以。
     * 如：public static void show(T t){..},此时编译器会提示错误信息：
          "StaticGenerator cannot be refrenced from static context"
     */
    public static <T> void show(T t){

    }
}
```



* 4.6.5 泛型方法总结

泛型方法能使方法独立于类而产生变化，以下是一个基本的指导原则：

```
无论何时，如果你能做到，你就该尽量使用泛型方法。也就是说，如果使用泛型方法将整个类泛型化，

那么就应该使用泛型方法。另外对于一个static的方法而已，无法访问泛型类型的参数。

所以如果static方法要使用泛型能力，就必须使其成为泛型方法。
```

#####  泛型上下边界

在使用泛型的时候，我们还可以为传入的泛型类型实参进行上下边界的限制，如：类型实参只准传入某种类型的父类或某种类型的子类。

为泛型添加上边界，即传入的类型实参必须是指定类型的子类型

```
public void showKeyValue1(Generic<? extends Number> obj){
    Log.d("泛型测试","key value is " + obj.getKey());
}
```

```
Generic<String> generic1 = new Generic<String>("11111");
Generic<Integer> generic2 = new Generic<Integer>(2222);
Generic<Float> generic3 = new Generic<Float>(2.4f);
Generic<Double> generic4 = new Generic<Double>(2.56);

//这一行代码编译器会提示错误，因为String类型并不是Number类型的子类
//showKeyValue1(generic1);

showKeyValue1(generic2);
showKeyValue1(generic3);
showKeyValue1(generic4);
```



如果我们把泛型类的定义也改一下:



```
public class Generic<T extends Number>{
    private T key;

    public Generic(T key) {
        this.key = key;
    }

    public T getKey(){
        return key;
    }
}
```

 

```
//这一行代码也会报错，因为String不是Number的子类
Generic<String> generic1 = new Generic<String>("11111");
```

再来一个泛型方法的例子：

 



```
//在泛型方法中添加上下边界限制的时候，必须在权限声明与返回值之间的<T>上添加上下边界，即在泛型声明的时候添加
//public <T> T showKeyName(Generic<T extends Number> container)，编译器会报错："Unexpected bound"
public <T extends Number> T showKeyName(Generic<T> container){
    System.out.println("container key :" + container.getKey());
    T test = container.getKey();
    return test;
}
```



通过上面的两个例子可以看出：泛型的上下边界添加，必须与泛型的声明在一起 。

####  关于泛型数组要提一下

看到了很多文章中都会提起泛型数组，经过查看sun的说明文档，在java中是”不能创建一个确切的泛型类型的数组”的。

也就是说下面的这个例子是不可以的：

```
List<String>[] ls = new ArrayList<String>[10];  
```

而使用通配符创建泛型数组是可以的，如下面这个例子：

```
List<?>[] ls = new ArrayList<?>[10]; 
```

这样也是可以的：

```
List<String>[] ls = new ArrayList[10];
```

下面使用[Sun](http://docs.oracle.com/javase/tutorial/extra/generics/fineprint.html)[的一篇文档](http://docs.oracle.com/javase/tutorial/extra/generics/fineprint.html)的一个例子来说明这个问题：

```
List<String>[] lsa = new List<String>[10]; // Not really allowed.    
Object o = lsa;    
Object[] oa = (Object[]) o;    
List<Integer> li = new ArrayList<Integer>();    
li.add(new Integer(3));    
oa[1] = li; // Unsound, but passes run time store check    
String s = lsa[1].get(0); // Run-time error: ClassCastException.
```



```
这种情况下，由于JVM泛型的擦除机制，在运行时JVM是不知道泛型信息的，所以可以给oa[1]赋上一个ArrayList而不会出现异常，但是在取出数据的时候却要做一次类型转换，所以就会出现ClassCastException，如果可以进行泛型数组的声明，上面说的这种情况在编译期将不会出现任何的警告和错误，只有在运行时才会出错。

而对泛型数组的声明进行限制，对于这样的情况，可以在编译期提示代码有类型安全问题，比没有任何提示要强很多。
```

 

下面采用通配符的方式是被允许的:数组的类型不可以是类型变量，除非是采用通配符的方式，因为对于通配符的方式，最后取出数据是要做显式的类型转换的。

```
List<?>[] lsa = new List<?>[10]; // OK, array of unbounded wildcard type.    
Object o = lsa;    
Object[] oa = (Object[]) o;    
List<Integer> li = new ArrayList<Integer>();    
li.add(new Integer(3));    
oa[1] = li; // Correct.    
Integer i = (Integer) lsa[1].get(0); // OK 
```

#  面向 接口的编程

实例化实际意义是在jvm的堆中开辟出一块内存空间，比如Student s = new Student();此处声明Student对象s，并且**实例化一个Student对象，实则是在堆中开辟出一块空间来存放Student对**s则是指向这块空间，也就是内存中的一块地址。这块地址中所存放的值就是我们这个Student对象的一些属性。而接口是抽象，没有具体方法的实现，但是有静态变量。

实现类 对象名 = new 类名

接口   对象名 = new 类名

有什么区别？

多写几个类实现接口，在实现类中分别写几个接口中没有的方法，然后使用

接口 对象名 = new 类名; （对象名指向new实体类的存储空间，仅为声明）

类名 对象名 = new 类名;

实例化对象调用方法，你就会发现使用接口 对象名 = new 类名; 方式实例化的对象只能调用接口中有的方法，而不能调用类中特有的方法。而使用类名 对象名 = new 类名;方式创建出来的对象可以调用所有的方法

使用接口编程的好处是统一规范化。

你会发现无论多少个实现类，无论这些实现类有什么不同，使用接口 对象名 = new 类名; 方式实例化对象都可以调用接口中定义的方法，

# 自动装箱和拆箱

| 基本数据类型 | 包装类  |
| ------------ | ------- |
| byte         | Byte    |
| boolean      | Boolean |
|short|	Short|
|char|	Character|
|int	|Integer|
|long|	Long|
|float|	Float|
|double|	Double|

Java 是一个近乎纯洁的面向对象编程语言，但是为了编程的方便还是引入了基本数据类型，而为了能够将这些基本数据类型当成对象操作，Java 为每一个基本数据类型都引入了对应的包装类型（wrapper class），int 的包装类就是Integer，从 Java 5 开始引入了自动装箱 / 拆箱机制，使得二者可以相互转换

自动装箱: 就是将基本数据类型自动转换成对应的包装类。

自动拆箱：就是将包装类自动转换成对应的基本数据类型。

```

Integer i =10;  //自动装箱
int b= i;     //自动拆箱

public static  void main(String[]args){
    Integer integer=Integer.valueOf(1); 
    int i=integer.intValue(); 
}
```

* 当需要进行自动装箱时，**如果数字在-128至127之间时，会直接使用缓存中的对象，而不是重新创建一个对象。**（IntegerCache）其中的javadoc详细的说明了缓存支持-128到127之间的自动装箱过程。最大值127可以通过-XX:AutoBoxCacheMax=size修改。实际上这个功能在Java 5中引入的时候,范围是固定的-128 至 +127。
  
* 后来在Java 6中，可以通过java.lang.Integer.IntegerCache.high设置最大值。
  
* ### 字符流和字节流
  
  什么是字节流？
  
  字节流--传输过程中，传输数据的最基本单位是字节的流。
  
  什么是字符流？
  
  字符流--传输过程中，传输数据的最基本单位是字符的流。
  
  实际上字节流在操作时本身不会用到缓冲区（内存），是文件本身直接操作的，而字符流在操作时使用了缓冲区，通过缓冲区再操作文件
  
  ![img](http://images.51cto.com/files/uploadimg/20090731/162655699.jpg)
  
  ![img](https://pics4.baidu.com/feed/e7cd7b899e510fb3d31622101b6f2090d0430c88.jpeg?token=7ef38cc82b1772aa411bf698f8f3391c&s=8D72E412D4A6250B8AD3A5C7030030AB)
  
  * **ObjectOutputStream是一个高级流， 将 Java 对象的基本数据类型和图形写入 OutputStream。**
  
  ![image-20200628192224629](F:\Typora数据储存\高级语言\JAVA\JAVA.assets\image-20200628192224629.png)

# 序列化和反序列化

一、基本概念

1、序列化和反序列化的定义：

    (1)Java序列化就是指把Java对象转换为字节序列的过程
    
        Java反序列化就是指把字节序列恢复为Java对象的过程。

   (2)序列化最重要的作用：在传递和保存对象时.保证对象的完整性和可传递性。对象转换为有序字节流,以便在网络上传输或者保存在本地文件中。

       反序列化的最重要的作用：根据字节流中保存的对象状态及描述信息，通过反序列化重建对象。

   总结：核心作用就是对象状态的保存和重建。（整个过程核心点就是字节流中所保存的对象状态及描述信息）

 

2、json/xml的数据传递：

 在数据传输(也可称为网络传输)前，先通过序列化工具类将Java对象序列化为json/xml文件。

在数据传输(也可称为网络传输)后，再将json/xml文件反序列化为对应语言的对象

 

3、序列化优点：

 ①将对象转为字节流存储到硬盘上，当JVM停机的话，字节流还会在硬盘上默默等待，等待下一次JVM的启动，把序列化的对象，通过反序列化为原来的对象，并且序列化的二进制序列能够减少存储空间（永久性保存对象）。

 ②序列化成字节流形式的对象可以进行网络传输(二进制形式)，方便了网络传输。

 ③通过序列化可以在进程间传递对象。

 

4、序列化算法需要做的事：

  ① 将对象实例相关的类元数据输出。

  ② 递归地输出类的超类描述直到不再有超类。

  ③ 类元数据输出完毕后，从最顶端的超类开始输出对象实例的实际数据值。

  ④ 从上至下递归输出实例的数据。

 

二、Java实现序列化和反序列化的过程

   1、实现序列化的必备要求：

       只有实现了Serializable或者Externalizable接口的类的对象才能被序列化为字节序列。（不是则会抛出异常） 

   2、JDK中序列化和反序列化的API：

      ①java.io.ObjectInputStream：对象输入流。
    
          该类的readObject()方法从输入流中读取字节序列，然后将字节序列反序列化为一个对象并返回。
    
     ②java.io.ObjectOutputStream：对象输出流。
    
          该类的writeObject(Object obj)方法将将传入的obj对象进行序列化，把得到的字节序列写入到目标输出流中进行输出。

 3、实现序列化和反序列化的三种实现：

  ①若Student类仅仅实现了Serializable接口，则可以按照以下方式进行序列化和反序列化。

             ObjectOutputStream采用默认的序列化方式，对Student对象的非transient的实例变量进行序列化。 
             ObjcetInputStream采用默认的反序列化方式，对Student对象的非transient的实例变量进行反序列化。

  ②若Student类仅仅实现了Serializable接口，并且还定义了readObject(ObjectInputStream in)和writeObject(ObjectOutputSteam out)，则采用以下方式进行序列化与反序列化。

           ObjectOutputStream调用Student对象的writeObject(ObjectOutputStream out)的方法进行序列化。 
           ObjectInputStream会调用Student对象的readObject(ObjectInputStream in)的方法进行反序列化。

  ③若Student类实现了Externalnalizable接口，且Student类必须实现readExternal(ObjectInput in)和writeExternal(ObjectOutput out)方法，则按照以下方式进行序列化与反序列化。

           ObjectOutputStream调用Student对象的writeExternal(ObjectOutput out))的方法进行序列化。 
           ObjectInputStream会调用Student对象的readExternal(ObjectInput in)的方法进行反序列化。

4、序列化和反序列化代码示例

```
public class SerializableTest {
        public static void main(String[] args) throws IOException, ClassNotFoundException {
            //序列化
            FileOutputStream fos = new FileOutputStream("object.out");
            ObjectOutputStream oos = new ObjectOutputStream(fos);
            Student student1 = new Student("lihao", "wjwlh", "21");
            oos.writeObject(student1);
            oos.flush();
            oos.close();
            //反序列化
            FileInputStream fis = new FileInputStream("object.out");
            ObjectInputStream ois = new ObjectInputStream(fis);
            Student student2 = (Student) ois.readObject();
            System.out.println(student2.getUserName()+ " " +
                    student2.getPassword() + " " + student2.getYear());
    }

}
public class Student implements Serializable{                             
                                                                          

    private static final long serialVersionUID = -6060343040263809614L;   
                                                                          
    private String userName;                                              
    private String password;                                              
    private String year;                                                  
                                                                          
    public String getUserName() {                                         
        return userName;                                                  
    }                                                                     
                                                                          
    public String getPassword() {                                         
        return password;                                                  
    }                                                                     
                                                                          
    public void setUserName(String userName) {                            
        this.userName = userName;                                         
    }                                                                     
                                                                          
    public void setPassword(String password) {                            
        this.password = password;                                         
    }                                                                     
                                                                          
    public String getYear() {                                             
        return year;                                                      
    }                                                                     
                                                                          
    public void setYear(String year) {                                    
        this.year = year;                                                 
    }                                                                     
                                                                          
    public Student(String userName, String password, String year) {       
        this.userName = userName;                                         
        this.password = password;                                         
        this.year = year;                                                 
    }                                                                     

}        
```


​                                                                 
 ①序列化图示



②反序列化图示



三、序列化和反序列化的注意点：

①序列化时，只对对象的状态进行保存，而不管对象的方法；

②当一个父类实现序列化，子类自动实现序列化，不需要显式实现Serializable接口；

③当一个对象的实例变量引用其他对象，序列化该对象时也把引用对象进行序列化；

④并非所有的对象都可以序列化，至于为什么不可以，有很多原因了，比如：

安全方面的原因，比如一个对象拥有private，public等field，对于一个要传输的对象，比如写到文件，或者进行RMI传输等等，在序列化进行传输的过程中，这个对象的private等域是不受保护的；

资源分配方面的原因，比如socket，thread类，如果可以序列化，进行传输或者保存，也无法对他们进行重新的资源分配，而且，也是没有必要这样实现；

⑤声明为static和transient类型的成员数据不能被序列化。因为static代表类的状态，transient代表对象的临时数据。

⑥序列化运行时使用一个称为 serialVersionUID 的版本号与每个可序列化类相关联，该序列号在反序列化过程中用于验证序列化对象的发送者和接收者是否为该对象加载了与序列化兼容的类。为它赋予明确的值。显式地定义serialVersionUID有两种用途：

在某些场合，希望类的不同版本对序列化兼容，因此需要确保类的不同版本具有相同的serialVersionUID；

在某些场合，不希望类的不同版本对序列化兼容，因此需要确保类的不同版本具有不同的serialVersionUID。

⑦Java有很多基础类已经实现了serializable接口，比如String,Vector等。但是也有一些没有实现serializable接口的；

⑧如果一个对象的成员变量是一个对象，那么这个对象的数据成员也会被保存！这是能用序列化解决深拷贝的重要原因；

注意：浅拷贝请使用Clone接口的原型模式。





# 关键词

#### public

(公共访问权限):

这是一个宽松的访问控制级别，如果一个成员(包括成员变量、方法和构造器等)或者一个外部类使用public访问控制符修饰，那么这个成员或外部类就可以被所有类(注：在该类外部，若是类成员，则需要类调用成员或外部类；若是非static的类，则应先实例化后，对象对其调用)访问，不管访问类和被访问类是否处于同一个包中，是否具有父子继承关系。



#### protected

(子类访问权限)：

如果一个成员(包括成员变量、方法和构造器等)使用protected访问控制符修饰，那么这个成员既可以被同一个包中的其他类访问，也可以被不同包中的子类访问。在通常情况下，如果使用protected来修饰一个方法，**通常是希望其子类来重写这个方法。**

#### default

(包访问权限)：

如果类里的一个成员(包括成员变量、方法和构造器等)或者一个外部类不使用任何访问控制符修饰，就称它是包访问权限，default访问控制的成员或外部类可以被相同包下的其他类访问。



default修饰方法只能在接口中使用，在接口中被default标记的方法为普通方法，可以直接写方法体。

1. **实现类会继承接口中的default方法**
2. **如果一个类同时实现接口A和B，接口A和B中有相同的default方法，这时，该类必须重写接口中的default方法**
3. **如果子类继承父类，父类中有b方法，该子类同时实现的接口中也有b方法（被default修饰），那么子类会继承父类的b方法而不是继承接口中的b方法**



#### private

(当前类访问权限)：

如果类里的一个成员(包括成员变量、方法和构造器等)使用private访问控制符来修饰，则这个成员只能在当前类的内部被访问。很显然，这个访问控制符用于修饰成员变量最合适，使用它来修饰成员变量就可以把成员变量隐藏在该类内部。

**子类不继承private其父类的成员。但是，如果超类具有用于访问其私有字段的公共或受保护的方法，则子类也可以使用这些方法**。嵌套类可以访问其封闭类的所有私有成员，包括字段和方法。因此，子类继承的公共或受保护的嵌套类可以间接访问超类的所有私有成员

| 权限      | 类内 | 同包 | 不同包子类 | 不同包非子类 |
| :-------- | :--- | :--- | :--------- | :----------- |
| private   | √    | ×    | ×          | ×            |
| default   | √    | √    | ×          | ×            |
| protected | √    | √    | √          | ×            |
| public    | √    | √    | √          | √            |



#### final

1.修饰变量  （与volatile与synchronized一样具有可见性）

凡是对成员变量或者局部变量(在方法中的或者代码块中的变量称为本地变量)声明为final的都叫作final变量。final变量经常和static关键字一起使用，作为常量。

final修饰基本数据类型的变量时，必须赋予初始值且不能被改变，修饰引用变量时，该引用变量不能再指向其他对象

当final修饰基本数据类型变量时不赋予初始值以及引用变量指向其他对象时就会报错

当final修饰基本数据类型变量被改变时，就会报错



2.修饰方法

final也可以声明方法。方法前面加上final关键字，代表这个方法不可以被子类的方法重写。如果你认为一个方法的功能已经足够完整了，子类中不需要改变的话，你可以声明此方法为final。final方法比非final方法要快，因为在编译的时候已经静态绑定了，不需要在运行时再动态绑定。

3.修饰类

使用final来修饰的类叫作final类。final类通常功能是完整的，它们不能被继承。Java中有许多类是final的，譬如String, Interger以及其他包装类。



二.深入分析final关键字

1. 被final修饰的对象内容是可变的

虽然对象被final修饰对象不可被继承，但其内容依然可以被改变

2. final关键字与static对比

static关键字修饰变量时，会使该变量在类加载时就会被初始化，不会因为对象的创建再次被加载，当变量被static 修饰时就代表该变量只会被初始化一次

3. 不可变类

不可变类(immutable)类的意思是创建该类的实例后，该实例的实例变量时不可改变的。Java提供的8个包装类和java.lang.String类都是不可变类，当创建它们的实例后，其实例的实例变量不可改变。

如果需要创建自定义的不可变类，可遵循如下规则。

- 使用private和final修饰符来修饰类的成员变量。
- 提供带参构造器，用于根据传入参数来初始化类里的成员变量。
- 仅为该类的成员变量提供getter方法，不要为该类的成员变量提供setter方法，因为普通方法无法修改final修饰的成员变量。
- 如果有必要，重写Object类的hashCode()和equals()方法。equals()方法根据关键成员变量来作为两个对象是否相等的标准，除此之外，还应该保证两个用equals()方法判断为相等的对象的hashCode()也相等。

#### transient

 java 的transient关键字为我们提供了便利，你只需要实现Serilizable接口，将不需要序列化的属性前添加关键字transient，序列化对象的时候，这个属性就不会序列化到指定的目的地中。

1）一旦变量被transient修饰，变量将不再是对象持久化的一部分，该变量内容在序列化后无法获得访问。

2）transient关键字只能修饰变量且其类实现了Serializable接口，而不能修饰方法和类。注意，**本地变量是不能被transient关键字修饰的。**

3）被transient关键字修饰的变量不再能被序列化，一个静态变量不管是否被transient修饰，均不能被序列化。一个静态变量不管是否被transient修饰，均不能被序列化），反序列化后类中static型变量username的值为当前JVM中对应static变量的值，这个值是JVM中的不是反序列化得出的。

> 对象的序列化可以通过实现两种接口来实现，若实现的是Serializable接口，则所有的序列化将会自动进行，若实现的是Externalizable接口，则没有任何东西可以自动序列化，需要在writeExternal方法中进行手工指定所要序列化的变量，这与是否被transient修饰无关。

#### abstract

(修饰抽象方法和抽象类)：抽象方法和抽象类必须使用abstract修饰符来定义，有抽象方法的类的只能被定义成抽象类，抽象类里可以没有抽象方法。

  抽象方法和抽象类的规则如下：

- 抽象类必须使用abstract修饰符，抽象方法也必须使用abstract修饰符来修饰，抽象方法不能有方法体。
- 抽象类不能被实例化，无法使用new关键字来调用抽象类的构造器创建抽象类的实例。即使抽象类里不包含抽象方法，这个抽象类也不能创建实例。
- 抽象类可以包含成员变量、方法(普通方法和抽象方法都可以)、构造器、初始化块、内部类(接口、枚举)5中成分。抽象类的构造器不能用于创建实例，主要是用于被其子类调用。
- 含有抽象方法的类(包括直接定义了一个抽象方法；或继承了一个抽象父类，但没有完全实现父类包含的抽象方法；或实现了一个接口，但没有完全实现接口包含的抽象方法三种情况)只能被定义成抽象类。

 当使用abstract修饰类时，表明这个类只能被继承；当使用abstract修饰方法时，表明这个方法必须由子类提供实现(即重写)。而final修饰的类不能被继承，final修饰的方法不能被重写。因此==final和abstract永远不能同时使用==

  除此之外，当使用static修饰一个方法时，表明这个方法属于该类本身，即通过类就可以直接调用该方法，但如果该方法被定义成抽象方法，则将导致通过该类来调用该方法时出现错误(调用了一个没有方法体的方法肯定会引起错误)。因此**==static和abstract不能同时修饰某个方法==**，即没有所谓的类抽象方法。

  static和abstract并不是绝对互斥的，static和abstract虽然不能同时修饰某个方法，但**它们可以同时修饰内部类**。（因为两者都在方法区中）

  abstract关键字修饰的方法必须被其子类重写才有意义，否则这个方法将永远不会有方法体，因此abstract方法不能定义为private访问权限，即==private和abstract不能同时修饰方法==。

#### static

1. 内部类：java里面static一般用来修饰成员变量或函数。但有一种特殊用法是**用static修饰内部类**，普通类是不允许声明为静态的，只有内部类才可以。可以降低内部类的调用深度

2. 方法：修饰方法的时候，其实跟类一样，可以直接通过类名.方法名来进行调用（在本类中调用可以直接用），而不用新建。
3. 变量：被static修饰的成员变量叫做静态变量，也叫做类变量，说明这个变量是属于这个类的，而不是属于是对象，没有被static修饰的成员变量叫做实例变量，说明这个变量是属于某个具体的对象的。
4. 代码块：静态代码块在类第一次被载入时执行

#### 类初始化的顺序：

1. 父类静态变量，若是**编译期可知的==静态常量==**会再javac编译期间保存到**静态常量池**，若是通过字面量加载类则可以直接使用而不用初始化，其他的类static变量需要到准备阶段才分配内存并且附默认初始值，而对其调用，即使是用字面量的方法导入，也会导致其进入初始化，并且会带动整个class对象的初始化. 

2. 父类静态代码块：static块的执行发生在“初始化”的阶段。初始化阶段，jvm主要完成对静态变量的初始化，静态块执行等工作。

3. 子类静态变量
4. 子类静态代码块
5. 父类普通变量
6. 父类普通代码块
7. 父类构造函数: 默认构造函数会传入this对象引用，但其不是静态的，静态的定义便是无this
8. 子类普通变量
9. 子类普通代码块
10. 子类构造函数



# 静态块

什么是静态块呢。

1、它是随着类的加载而执行，只执行一次，并优先于主函数。具体说，静态代码块是由类调用的。类调用时，先执行静态代码块，然后才执行主函数的；

2、静态代码块其实就是给类初始化的，而构造代码块是给对象初始化的；

3、静态代码块中的变量是局部变量，与普通函数中的局部变量性质没有区别；

4、一个类中可以有多个静态代码块；

他的写法是这样的：

```java
static {
        System.out.println("static executed");
   }
```

来看一下下面这个完整的实例：

```java
public class SingleStatic {

    static {
        System.out.println("static 块执行中...");
    }
    {
       System.out.println("构造代码块 执行中...");
    }
  public SingleStatic(www.120xh.cn ){
    System.out.println("构造函数 执行中");
    }

  public static void main(String[] args){
        System.out.println(www.huachengj1980.com/"main 函数执行中");
        SingleStatic singleStatic = new SingleStatic();

    }

}
```

他的执行结果是这样的：

```
static 块执行中...
main 函数执行中
构造代码块 执行中...
构造函数 执行中
```

从中可以看出他们的执行顺序分别为：

1、静态代码块

2、main 函数

3、构造代码块

4、构造函数

利用静态代码块只在类加载的时候执行，并且只执行一次这个特性，也可以用来实现单例模式，但是不是懒加载，也就是说每次类加载就会主动触发实例化。

除此之外，不考虑单例的情况，利用静态代码块的这个特性，可以实现其他的一些功能，例如上面提到的配置文件加载的功能，可以在类加载的时候就读取配置文件的内容，相当于一个预加载的功能，在使用的时候可以直接拿来就用。

# String、StringBuffer与StringBuilder

| String                                                       | StringBuffer                                                 | StringBuilder                                                |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| String的值是不可变的，这就导致每次对String的操作都会生成新的String对象，不仅效率低下，而且浪费大量优先的内存空间.private final char value [] | StringBuffer是可变类，和线程安全的字符串操作类，任何对它指向的字符串的操作都不会产生新的对象。每个StringBuffer对象都有一定的缓冲区容量，当字符串大小没有超过容量时，不会分配新的容量，当字符串大小超过容量时，会自动增加容量 | 可变类，速度更快，一般情况下 StringBuilder 相比<StringBuffer 可获得 10%~15% 左右的性能提升。 |
| 不可变                                                       | 可变                                                         | 可变                                                         |
| 因为对象是不变的，所以线程安全                               | 线程安全                                                     | 线程不安全                                                   |
| 数据较少时使用                                               | 多线程操作字符串缓冲区下操作大量数据                         | 单线程操作字符串缓冲区下操作大量数据                         |

> 1.String的不可变
> String类被final修饰，是不可继承的。当一个String变量被第二次赋值时，不是在原有内存地址上修改数据，而是在内存中重新开辟一块char[]数组的内存地址，并指向新地址。
>
> String类为什么要被设计为是final的？
>
> 　　1.不可变性支持线程安全。
> 　　2.不可变性支持字符串常量池，提升性能。
> 　　3.String字符串作为最常用数据类型之一，不可变防止了随意修改，保证了数据的安全性。
>
> 正常情况下Java的String字符串是final且每次赋值都会创建新的char[],不过可以通过特殊手段修改它的内容。
>
> 这里钻了final修饰的类不能继承但是仍可以修改其内容的空挡，且存储的为char[]数组的地址，可以不修改地址而修改值
>
> String类的主力成员字段value是个char[ ]数组，而且是用final修饰的。final修饰的字段创建以后就不可改变。因为虽然value是不可变，也只是value这个引用地址不可变。

#### String.trim()

`trim()` 方法用于删除字符串的头尾空白符。

```java
String Str = new String("    www.runoob.com    ");
Str.trim();
12
```

#### String.split()

`split()` 方法根据匹配给定的正则表达式来拆分字符串

```java
public String[] split(String regex, int limit)  //regex正则表达式分隔符。limit  分割的份数。
//使用方法
String str = new String("Welcome-to-Runoob");
str.split("-");  //用-将字符串分隔开
str.split("-", 2);  //用-将字符串分隔开，分割成两份，分别是Welcome和to-Runoob
```

# JAVA动态代理

借鉴:

https://www.zhihu.com/question/20794107/answer/658139129?utm_source=wechat_session&utm_medium=social&utm_oi=911918804941021184&utm_content=sec

https://blog.csdn.net/yangaiyu/article/details/73827043

* 若自己实现代码增强，则需要为每个目标对象单独编写一个代理Class对象然后用类加载器加载，实例化，再调用，这样每个目标对象都得**单独地编写代理Class对象**以达到目的（即静态代理）。
* 为了减少代理类地编写，我们类的消息一般都由**Class对象在JVM方法区中被加载**(反射和new都是)，若想不编写代理class还要有目标类的信息，自然而然地可以想到**接口**，若能对接口进行动态代理，则可将实现该接口并调用该接口中的方法（可能不止实现一个接口）的目标类归为以类进行代码编写（这就是为什么要使用动态代理的原因），但是，接口并不能实例化
* 但java.lang.reflect.Proxy类有个getProxyLoader（ClassLoader，interfaces）方法，只要你给他传入类加载器和一组接口，它就能给你返回代理Class对象。（意味着它变成了一个对象，可以带有构造器进行实例化）

  ![微信图片_20200719195310](F:\Typora数据储存\高级语言\JAVA\JAVA.assets\微信图片_20200719195310.jpg)
* 有了代理Class对象（**代理Class对象为代理类->数据结构,代理对象为实例化的对象**），我们可以通过该对象得到有参的构造器（基于InvocationHandle接口），我们就可以反射创建实例了，而基于上述接口的构造器可以基于实例化的InvocationHandle接口的对象实例化，使得代理对象通过handler对象中的invoke方法去增强目标对象（**InvocationHandle接口是一个规范，代理对象中会有成员变量去接收它，并为他的proxy、method、args等传参，为了便于设计把他提取出来**）

  ![微信图片_20200719205814](F:\Typora数据储存\高级语言\JAVA\JAVA.assets\微信图片_20200719205814.jpg)

 ```
 InvocationHandler handler = new DynamicProxy(realSubject);
 Object invoke(Object proxy, Method method, Object[] args) throws Throwable
 
 proxy:　　指代我们所代理的那个真实对象
 method:　　指代的是我们所要调用真实对象的某个方法的Method对象
 args:　　指代的是调用真实对象某个方法时接受的参数
 ```

> 可能会以为返回的这个代理对象会是Calculator类型的对象，或者是InvocationHandler的对象，结果却不是，首先我们解释一下为什么我们这里可以将其转化为Subject类型的对象？原因就是在getPorxyClass这个方法的第二个参数上，我们给这个代理对象提供了一组什么接口，那么我这个代理对象就会实现了这组接口，这个时候我们当然可以将这个代理对象强制类型转化为这组接口中的任意一个，因为这里的接口是Subject类型，所以就可以将其转化为Subject类型了。
>
> 同时我们一定要记住，以上获取代理Class、得到有参构造函数、实例化后创建的代理对象是在jvm运行时动态生成的一个对象，它并不是我们的InvocationHandler类型，也不是我们定义的那组接口的类型，而是在运行是动态生成的一个对象，并且命名方式都是这样的形式，以$开头，proxy为中，最后一个数字表示对象的标号。

* 上面的并没有传入目标对象，需要在invoke里新建相应的对象，我们常用的是传入相应的目标对象，而将上面的封装为一个函数，以适用于更多的对象调用，而**更常用的是Proxy.newProxyInstance()方法**，直接将上述流程都封装了。

```
* public static Object newProxyInstance(ClassLoader loader, Class<?>[] interfaces, InvocationHandler h) throws IllegalArgumentException

loader:一个ClassLoader对象，定义了由哪个ClassLoader对象来对生成的代理对象进行加载

interfaces:一个Interface对象的数组，表示的是我将要给我需要代理的对象提供一组什么接口，如果我提供了一组接口给它，那么这个代理对象就宣称实现了该接口(多态)，这样我就能调用这组接口中的方法了

h:一个InvocationHandler对象，表示的是当我这个动态代理对象在调用方法的时候，会关联到哪一个InvocationHandler对象上
```




![微信图片_20200719210822](F:\Typora数据储存\高级语言\JAVA\JAVA.assets\微信图片_20200719210822.jpg)

![image-20200719210844322](F:\Typora数据储存\高级语言\JAVA\JAVA.assets\image-20200719210844322.png)

![image-20200719210901828](F:\Typora数据储存\高级语言\JAVA\JAVA.assets\image-20200719210901828.png)

* 之后在invoke中调用method.invoke时，即为代理对象调用真实对象的方法时，其会自动的跳转到代理对象关联的handler对象的invoke方法来进行调用（反射实现），可以深入getPorxyClass与创造构造器的三个步骤或newProxyInstance中查看相关实现。
* 以上便是动态代理的全部流程了

![微信图片_20200719225118](F:\Typora数据储存\高级语言\JAVA\JAVA.assets\微信图片_20200719225118.jpg)

# equals与==

![img](https://img-blog.csdn.net/20180602183200272?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2NTIyMzA2/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

Object类的equals方法定义为：x.equals(y) 当x和y是同一个对象的引用（地址相同，和==功能一致），返回true

> String类和Date类等，重写了Object的equals方法，当x和y所引用的对象是同一类对象且属性内容相等时，返回true，否则返回false
>
> ```java
>  public boolean equals(Object anObject) {
>      	// 两个对象引用的地址相同，直接返回true
>         if (this == anObject) {
>             return true;
>         }
>      	// 判断是否是String类型
>         if (anObject instanceof String) {
>             String anotherString = (String)anObject;
>             int n = value.length;
>             // 判断两个对象的内容的长度是否相等
>             if (n == anotherString.value.length) {
>                 char v1[] = value;
>                 char v2[] = anotherString.value;
>                 int i = 0;
>                 // 逐个比较两个对象的每个char是否相等
>                 while (n-- != 0) {
>                     if (v1[i] != v2[i])
>                         return false;
>                     i++;
>                 }
>                 return true;
>             }
>         }
>         return false;
>     }
> ```
>
> 

1. ==是判断两个变量或实例是不是指向同一个内存空间，equals是判断两个变量或实例所指向的内存空间的值是不是相同 

2. ==是指对内存地址进行比较 ， equals()是对字符串的内容进行比较

3. ==指引用是否相同， equals()指的是值是否相同

4. ==如果equals相等，hashcode一定相等==。所以如果equals重写，hashCode也要重写。但hashcode相等不一定equal（哈希冲突）

   > equals是我们逻辑上判断两个个体是否相同的函数，如果我们需要的情况下下判断两个体在当前相同，那我们应该把他们当作不同存储空间的两个相同实例（Object的equals实现是直接== 物理判断，且Object的hashcode是基于地址的native方法，因此两者物理相等等于逻辑相等，复杂的如String等的重写，则依据类的类型以及各种属性进行自己逻辑上的定义）
   >
   > hashcode的目的是通过计算来快速获取我们想要的实例，因此若是逻辑相同的情况下，我们将他们视为相同，在不考虑哈希冲突的情况下，我们甚至可以一个代替另一个（或者不处理），不需要链表。
   >
   > 两者逻辑目的相同，因此若想符合两者的定义，需要符合上述约定
   >
   > 我们不能从putVal等代码层入手，因为是在上面这种条件下我们才有这种结果（逻辑相同即为相同），不同的条件下依据相同的该份代码会有不同的结果，因此我们不能从代码层入手来反证明上面的条件，只能从条件经由代码得出唯一结果
   >
   > 若是key.equals相等而hashcode不等，则当前场景下需要逻辑相等的对象将被作为两个不同种类的对象存储了，相等的方式变成了物理相同才相同了，与定义不符。（因为equals若是同一个对象，则除非用随机数来判断equals不然肯定相同）

# 分层领域模型规约

Java开发过程中，基本实体类包都以entity或者model来称呼，可是不少项目中，却以Bo、Vo来命名，面试的时候，也有可能被问到这些问题。那么，这几者分别代表什么意思呢？

#### Entity

最常用实体类，基本和数据表一一对应，一个实体一张表。

#### Bo(business object)

 代表业务对象的意思，Bo就是把业务逻辑封装为一个对象（注意是逻辑，业务逻辑），这个对象可以包括一个或多个其它的对象。通过调用Dao方法，结合Po或Vo进行业务操作。

形象描述为一个对象的形为和动作，当然也有涉及到基它对象的一些形为和动作。比如处理一个人的业务逻辑，该人会睡觉，吃饭，工作，上班等等行为，还有可能和别人发关系的行为，处理这样的业务逻辑时，我们就可以针对BO去处理。

再比如投保人是一个Po，被保险人是一个Po，险种信息也是一个Po等等，他们组合起来就是一张保单的Bo。

#### Vo(view object)

代表值对象的意思，通常用于业务层之间的数据传递，由new创建，由GC回收。
 主要体现在视图的对象，对于一个WEB页面将整个页面的属性封装成一个对象，然后用一个VO对象在控制层与视图层进行传输交换。

![img](https:////upload-images.jianshu.io/upload_images/13566833-4961b86f59a50dd6.png?imageMogr2/auto-orient/strip|imageView2/2/w/533/format/webp)

关系图.png

#### Po(persistant object)

代表持久层对象的意思，对应数据库中表的字段，数据库表中的记录在java对象中的显示状态，最形象的理解就是一个PO就是数据库中的一条记录。

好处是可以把一条记录作为一个对象处理，可以方便的转为其它对象。Vo和Po，都是属性加上属性的get和set方法；表面看没什么不同，但代表的含义是完全不同的。

#### Dto(data transfer object)

代表数据传输对象的意思
 是一种设计模式之间传输数据的软件应用系统，数据传输目标往往是数据访问对象从数据库中检索数据
 数据传输对象与数据交互对象或数据访问对象之间的差异是一个以不具任何行为除了存储和检索的数据（访问和存取器）
 简而言之，就是接口之间传递的数据封装
 表里面有十几个字段：id，name，gender（M/F)，age……
 页面需要展示三个字段：name，gender(男/女)，age
 DTO由此产生，一是能提高数据传输的速度(减少了传输字段)，二能隐藏后端表结构

#### Pojo(plian ordinary java object)

POJO是DO/DTO/BO/VO的统称

代表简单无规则java对象
 纯的传统意义的java对象，最基本的Java Bean只有属性加上属性的get和set方法

可以额转化为PO、DTO、VO；比如POJO在传输过程中就是DTO

#### Dao(data access object) 

代表数据访问对象的意思，是sun的一个标准j2ee设计模式的接口之一，负责持久层的操作 。这个基本都了解，Dao和上面几个O区别最大，基本没有互相转化的可能性和必要，主要用来封装对数据的访问，注意，是对数据的访问，不是对数据库的访问。

####  Controller

代表控制层，主要是Action/Servlet等构成（Spring MVC则是通过@Controller标签使用）此层业务层与视图层打交道的中间层，负责传输VO对象和调用BO层的业务方法，负责视图层请求的数据处理后响应给视图层。

#### View

代表视图层的意思，主要是指由JSP、HTML等文件形成的显示层。

所以实际项目中，一般都是这样应用的：
 控制层(controller-action)，业务层/服务层( bo-manager-service)，实体层(po-entity)，dao(dao)，视图对象(Vo-)，视图层(view-jsp/html)

# 数组复制

我们知道，对于数组本身而言，当它的元素是对象时，本来数组每个元素中保存的就是对象的引用，所以拷贝过来的数组自然而言也是对象的引用，**所以对于数组对象元素而言，以下均是浅拷贝。**

#### 1 for循环逐一复制

使用for循环，将数组的每个元素复制或者复制指定元素；代码灵活，但需注意数组越界异常；效率低

```java
int[] array1 = {1,2,3,4,5};
int[] array2 = new int[5];
for(int i = 0;i < array1.length;i++) 
{
    array2[i] = array1[i];
}
```

#### 2 System.arraycopy

public static native void arraycopy(Object src, int srcPos,Object dest, int destPos, int length);

从原数组中的第srcPos个位置起复制length个元素到目标数组的第destPos个位置

src：源数组

srcPos：源数组中的起始位置

dest：目标数组

destPos：目标数据中的起始位置

length：要复制的数组元素的数量

注：src和des都必须是同类型或者可以进行转换类型的数组

```java
int[] array1 = {1,2,3,4,5};
int[] array2 = {11,12,13,14,15,16}; 
System.arraycopy(array1, 1, array2, 2, 3);
```

#### 3 Arrays.copyOf

public static int[] copyOf(int[] original, int newLength);

复制指定的数组，返回原数组的副本

original：源数组，可以为byte,short,int,long,char,float,double,boolean

newLength：新数组的长度

```java
int[] array1 = {1,2,3,4,5};
int[] array2 = new int[3]; 
array2 = Arrays.copyOf(array1, 3);
```

其实现基于System.arraycopy()，所以效率低于System.arraycopy()

#### 4 Arrays.copyOfRange

public static int[] copyOfRange(int[] original, int from, int to);

复制原数组original的指定部分（从from到（to-1）位），返回原数组的副本

original：源数组，可以为byte,short,int,long,char,float,double,boolean

from：源数组被复制的起始位置

to： 源数组被复制的中止位置（不包括to本身）

其实现基于System.arraycopy()，所以效率低于System.arraycopy()

```java
int[] array1 = {1,2,3,4,5};
int[] array2 = new int[3]; 
array2 = Arrays.copyOfRange(array1, 2, 5);
```

#### 5 clone的用法

从Object中继承过来的clone默认实现的是浅拷贝。也可自己重写实现深拷贝。

clone()方法不单将一个数组引用赋值为另外一个数组引用，而且创建一个新的数组。其他的都需要自己创建。

注：使用clone方法,得返回值类型为Object类型，所以赋值时将发生强转，因此效率较低

```java
int[] array1 = {1,2,3,4,5};
int[] array2; 
array2 = array1.clone();
```

clone()方法只能直接复制一维数组，对于二维数组的复制，要在每一维上调用clone函数

```java
int[][] a={{3, 1, 4, 2, 5}, {4, 2}};
int[][] b=new int[a.length][];
for(int i=0; i<a.length; i++)
{
    b[i]=a[i].clone();
} 
```

**效率：System.arraycopy > Arrays.copyOf > for循环 > clone**



# 构造方法

构造函数有没有返回值？

```java
Constructors are invoked after the instance variables of a newly created object of the class have been assigned their default initial values and after their explicit initializers are executed. 
    构造方法是新创建的对象的实例变量缺省初始化以及显式初始化之后才执行的，也就是说在构造方法调用之前这个对象已经存在了，更谈不上你例子中的构造方法返回一个对象引用了。
```

构造方法是一种特殊的方法，具有以下特点。 
（1）构造方法的方法名必须与类名相同。 
（2）**构造方法没有返回类型，也不能定义为void，在方法名前面不声明方法类型。** 
（3）构造方法的主要作用是完成对象的初始化工作，它能够把定义对象时的参数传给对象的域。 
（4）构造方法不能由编程人员调用，而要系统调用。 
（5）一个类可以定义多个构造方法，如果在定义类时没有定义构造方法，则编译系统会自动插入一个无参数的默认构 造器，这个构造器不执行任何代码。 
（6）构造方法可以重载，以参数的个数，类型，或排列顺序区分。 

new时返回对象是new关键字实现的功能和构造方法无关，构造方法只是初始化作用。

# 异常

## 异常类别

![image-20201019005527065](F:\Typora数据储存\高级语言\JAVA\JAVA.assets\image-20201019005527065.png)



**检查异常（checked exception）**
**检查异常通常是用户错误**，程序员并不可预见的问题。例如，如果一个文件被打开，但该文件无法找到，则会出现异常。这些异常并不能在编译时被发现。（好像不被推荐）

> 1.java.lang.SQLException
>
> 该异常的解释是：sql异常。 
> sql语句异常种类十分多，通常都是sql语句、数据库执行错误导致，常见的表现有：
>
> - invalid column name 无效列名
> - table or view does not exist 表或者视图不存在
> - cannot insert NULL into () 不能将空值插入
> - 缺少表达式
> - SQL 命令未正确结束
>
> 在操作数据库时需要考虑全面，尽量避免该异常。
>
> 2.java.lang.IOException
>
> 该异常的解释是：输入输出异常。 
> 该异常种类也十分多（拥有很多子类），尤其对文件的操作，以及[Android](http://lib.csdn.net/base/15)开发。常见的表现有：
>
> - FileNotFoundException 文件找不到。
> - InvalidPropertiesFormatException 输入内容不符合属性集的正确 XML 文档类型。
>
> 3.java.lang.FileNotFoundException
>
> 该异常的解释是：文件不存在异常。该异常继承于 IOException。 
> 这个异常通常是获取文件时，文件路径或文件名称错误导致的。
>
> 4.java.lang.ReflectiveOperationException
>
> 该异常的解释是：反射操作相关的异常。 
> 由于反射的特殊性，类、方法、属性均使用String作为名称进行操作，对于该类异常一定要十分注意。 
> 了解反射看这里：什么是java中的反射
>
> 5.java.lang.ClassNotFoundException
>
> 该异常的解释是：指定的类不存在。该异常继承于ReflectiveOperationException。 
> 这个异常通常是在使用反射时，或者服务端引入jar包时抛出。 
> 使用反射时，根据类名（字符串）获取Class时，包、类名有误会造成该异常。
>
> 6.java.lang.NoSuchMethodException
>
> 该异常的解释是：指定的方法不存在。该异常继承于ReflectiveOperationException。 
> 这个异常通常是在使用反射时抛出。 
> 使用反射时，根据方法名（字符串）调用Method时，方法名有误会造成该异常。
>
> 7.java.lang.NoSuchFieldException
>
> 该异常的解释是：指定的字段不存在。该异常继承于ReflectiveOperationException。 
> 这个异常通常是在使用反射时抛出。 
> 使用反射时，根据字段名（字符串）获取、操作Field时，字段名有误会造成该异常。
>
> 8.java.lang.IllegalAccessException
>
> 该异常的解释是：没有访问权限。 
> 当应用程序要调用一个类，但当前的方法即没有对该类的访问权限便会出现这个异常。 
> 最常见的地方即在使用反射调用private方法/属性时会抛出该异常，将private方法/属性共有化public即可。 
> 
>
> 9.java.lang.InstantiationException
>
> 该异常的解释是：实例化异常。该异常继承于ReflectiveOperationException。 
> 当试图通过newInstance()方法创建某个类的实例，而该类是一个抽象类或接口时，抛出该异常。
>
> 10.java.lang.CloneNotSupportedException
>
> 该异常的解释是：不支持克隆异常。该异常继承于 ReflectiveOperationException。 
> 当没有实现Cloneable接口或者不支持克隆方法时,调用其clone()方法则抛出该异常。
>
> 11.java.lang.InterruptedException
>
> 该异常的解释是：被中止异常。 
> 当某个线程处于长时间的等待、休眠或其他暂停状态，而此时其他的线程通过Thread的interrupt方法终止该线程时抛出该异常。

**运行时异常（runtime exception也叫unchecked exception**）
运行时异常时本来可以由程序避免的异常。而不是已检查异常，运行时异常是在编译时被忽略。这里的运行时异常并不是我们所说的运行期间产生的异常，只是Java中用运行时异常这个术语来表示而已。另外，所有Exception异常都是在运行期间产生的。

> RuntimeException 没有被捕获会直达main（），在程序退出前将调用异常的printStackTrace（）方法。
>
> 只能在代码中忽略RuntimeException（及其子类）类型的异常，其他类型异常的处理都是由编译器强制实施的。究其原因RuntimeException代表的是编程错误。

**错误（error）**

表示编译时和系统错误，比如OutOfMemoryError，一般发生这种异常，JVM会选择终止程序。因此我们编写程序时不需要关心这类异常。

## 异常方法

以下是Throwable类中比较重要的方法。

```java
public String getMessage()
//返回有关已发生异常的详细消息。此消息在Throwable的构造函数中被初始化。
```



```java
public Throwable getCause()
//返回异常由一个Throwable对象所表示的错误原因。
```



```java
 public String toString()
//返回getMessage()结果的名称。
```



```java
public void printStackTrace()
//打印toString()结果以及堆栈跟踪信息到System.err，输出错误流。
```



```java
public StackTraceElement [] getStackTrace()
//返回堆栈跟踪信息的数组。索引为0的元素表示堆栈的顶部，最后一个元素表示堆栈的底部。
```

# 基本数据类型

#### 八大基本数据类型

| 简单类型   | boolean | byte | char      | short | Int     | long | float | double | void |
| ---------- | ------- | ---- | --------- | ----- | ------- | ---- | ----- | ------ | ---- |
| 二进制位数 | 1       | 8    | 16        | 16    | 32      | 64   | 32    | 64     | --   |
| 封装器类   | Boolean | Byte | Character | Short | Integer | Long | Float | Double | Void |

#### BigDecimal 

![image-20201110155508159](F:\Typora数据储存\高级语言\JAVA\JAVA.assets\image-20201110155508159.png)

因为不论是float 还是double都是浮点数，而计算机是二进制的，浮点数会失去一定的精确度。

注:根本原因是:十进制值通常没有完全相同的二进制表示形式;十进制数的二进制表示形式可能不精确。只能无限接近于那个值

【BigDecimal是什么？】

**1、简介**
Java在java.math包中提供的API类BigDecimal，用来对超过16位有效位的数进行精确的运算。双精度浮点型变量double可以处理16位有效数。在实际应用中，需要对更大或者更小的数进行运算和处理。float和double只能用来做科学计算或者是工程计算，在商业计算中要用java.math.BigDecimal。BigDecimal所创建的是对象，我们不能使用传统的+、-、*、/等算术运算符直接对其对象进行数学运算，而必须调用其相对应的方法。方法中的参数也必须是BigDecimal的对象。构造器是类的特殊方法，专门用来创建对象，特别是带有参数的对象。

**2、构造器描述** 
BigDecimal(int)    创建一个具有参数所指定整数值的对象。 
BigDecimal(double) 创建一个具有参数所指定双精度值的对象。 //不推荐使用
BigDecimal(long)   创建一个具有参数所指定长整数值的对象。 
BigDecimal(String) 创建一个具有参数所指定以字符串表示的数值的对象。//推荐使用

**3、方法描述** 
add(BigDecimal)     BigDecimal对象中的值相加，然后返回这个对象。 
subtract(BigDecimal) BigDecimal对象中的值相减，然后返回这个对象。 
multiply(BigDecimal)  BigDecimal对象中的值相乘，然后返回这个对象。 
divide(BigDecimal)   BigDecimal对象中的值相除，然后返回这个对象。 
toString()         将BigDecimal对象的数值转换成字符串。 
doubleValue()      将BigDecimal对象中的值以双精度数返回。 
floatValue()       将BigDecimal对象中的值以单精度数返回。 
longValue()       将BigDecimal对象中的值以长整数返回。 
intValue()        将BigDecimal对象中的值以整数返回。

为什么BigDecimal(double) 不推荐使用？

JDK的描述：

​	1、参数类型为double的构造方法的结果有一定的不可预知性。有人可能认为在Java中写入newBigDecimal(0.1)所创建的BigDecimal正好等于 0.1（非标度值 1，其标度为 1），但是它实际上等于0.1000000000000000055511151231257827021181583404541015625。这是因为0.1无法准确地表示为 double（或者说对于该情况，不能表示为任何有限长度的二进制小数）。这样，传入到构造方法的值不会正好等于 0.1（虽然表面上等于该值）。

​    2、另一方面，String 构造方法是完全可预知的：写入 newBigDecimal("0.1") 将创建一个 BigDecimal，它正好等于预期的 0.1。因此，比较而言，**通常建议优先使用String构造方法**

>  **当double必须用作BigDecimal的源时，**请使用`Double.toString(double)``转成String，然后`使用String构造方法，或使用BigDecimal的静态方法valueOf

这边特别提一下，**如果进行除法运算的时候**，结果不能整除，有余数，这个时候会报java.lang.ArithmeticException: ，这边我们要避免这个错误产生，在进行除法运算的时候，针对可能出现的小数产生的计算，必须要多传两个参数  divide(BigDecimal，保留小数点后几位小数，舍入模式)

舍入模式

```
ROUND_CEILING    //向正无穷方向舍入
ROUND_DOWN    //向零方向舍入
ROUND_FLOOR    //向负无穷方向舍入
ROUND_HALF_DOWN    //向（距离）最近的一边舍入，除非两边（的距离）是相等,如果是这样，向下舍入, 例如1.55 保留一位小数结果为1.5
ROUND_HALF_EVEN    //向（距离）最近的一边舍入，除非两边（的距离）是相等,如果是这样，如果保留位数是奇数，使用ROUND_HALF_UP，如果是偶数，使用ROUND_HALF_DOWN
ROUND_HALF_UP    //向（距离）最近的一边舍入，除非两边（的距离）是相等,如果是这样，向上舍入, 1.55保留一位小数结果为1.6,也就是我们常说的“四舍五入”
ROUND_UNNECESSARY    //计算结果是精确的，不需要舍入模式
ROUND_UP    //向远离0的方向舍入
```

# 命令式编程和函数式编程

### 范式编程

常见的范式编程有命令式编程、函数式编程、逻辑式编程



命令式编程是面向计算机硬件的抽象，有变量（对应着存储单元）、赋值语句（获取，存储指令）、表达式（内存引用和算数运算）和控制语句（跳转指令），命令式程序就是一个冯诺依曼机的指令序列



而函数式编程是面向数学的抽象，将计算描述为一种表达式求值，函数式程序就是一个表达式

### 函数式编程的本质

 函数式编程中的函数这个术语不是指计算机中的函数（实际上是Subroutine），而是指数学中的函数，即自变量的映射。也就是说一个函数的值仅决定于函数参数的值，不依赖其他状态。比如sqrt(x)函数计算x的平方根，只要x不变，不论什么时候调用，调用几次，值都是不变的。

 在函数式语言中，函数作为一等公民，可以在任何地方定义，在函数内或函数外，可以作为函数的参数和返回值，可以对函数进行组合。 

纯函数式编程语言中的变量也不是命令式编程语言中的变量，即存储状态的单元，而是代数中的变量，即一个值的名称。变量的值是不可变的（immutable），也就是说不允许像命令式编程语言中那样多次给一个变量赋值。比如说在命令式编程语言我们写“x = x + 1”，这依赖可变状态的事实，拿给程序员看说是对的，但拿给数学家看，却被认为这个等式为假。 

函数式语言的如条件语句，循环语句也不是命令式编程语言中的控制语句，而是函数的语法糖，比如在Scala语言中，if else不是语句而是三元运算符，是有返回值的。 

严格意义上的函数式编程意味着不使用可变的变量，赋值，循环和其他命令式控制结构进行编程。 从理论上说，函数式语言也不是通过冯诺伊曼体系结构的机器上运行的，而是通过λ演算来运行的，就是通过变量替换的方式进行，变量替换为其值或表达式，函数也替换为其表达式，并根据运算符进行计算。λ演算是图灵完全（Turing completeness）的，但是大多数情况，函数式程序还是被编译成（冯诺依曼机的）机器语言的指令执行的。



### 函数式编程的好处

 由于命令式编程语言也可以通过类似函数指针的方式来实现高阶函数，函数式的最主要的好处主要是不可变性带来的。没有可变的状态，**函数就是引用透明（Referential transparency）的和没有副作用（No Side Effect）。**

 一个好处是，函数即不依赖外部的状态也不修改外部的状态，函数调用的结果不依赖调用的时间和位置，这样写的代码容易进行推理，不容易出错。这使得单元测试和调试都更容易。

 不变性带来的另一个好处是：由于（多个线程之间）不共享状态，不会造成资源争用(Race condition)，也就不需要用锁来保护可变状态，也就不会出现死锁，这样可以更好地并发起来，尤其是在对称多处理器（SMP）架构下能够更好地利用多个处理器（核）提供的并行处理能力。

 2005年以来，计算机计算能力的增长已经不依赖CPU主频的增长，而是依赖CPU核数的增多

在多核或多处理器的环境下的程序设计是很困难的，难点就是在于共享的可变状态。在这一背景下，这个好处就有非常重要的意义。 由于函数是引用透明的，以及函数式编程不像命令式编程那样关注执行步骤，这个系统提供了优化函数式程序的空间，包括惰性求值和并性处理。 还有一个好处是，由于函数式语言是面向数学的抽象，更接近人的语言，而不是机器语言，代码会比较简洁，也更容易被理解。



### 函数式编程的特性

同样由于变量不可变，纯函数编程语言无法实现循环，这是因为For循环使用可变的状态作为计数器，而While循环或DoWhile循环需要可变的状态作为跳出循环的条件。（需要借助编译器的自动优化来提升效率）

因此在函数式语言里就只能使用递归来解决迭代问题，这使得**函数式编程严重依赖递归。** 

通常来说，算法都有递推（iterative）和递归（recursive）两种定义

> 递推定义的计算时需要使用一个累积器保存每个迭代的中间计算结果
>

# 值传递和引用传递

### 值传递

在方法被调用时，实参通过形参把它的内容副本传入方法内部，此时形参接收到的内容是实参值的一个拷贝，因此在方法内对形参的任何操作，都仅仅是对这个副本的操作，不影响原始值的内容。

如：局部变量，每个方法的栈帧独立维护自己的局部变量表，实参通过形参把它的内容副本传入方法内部的变量

### 引用传递

”引用”也就是指向真实内容的地址值，在方法调用时，实参的地址通过方法调用被传递给相应的形参，在方法体内，形参和实参指向同一块内存地址，对形参的操作会影响的真实内容。

如：对象引用，实参传递的仅是堆中对象的地址

# 内部类

内部类又称为嵌套类，可以把内部类理解为外部类的一个普通成员。**内部类之所以也是语法糖，是因为它仅仅是一个编译时的概念。**outer.java里面定义了一个内部类inner，一旦编译成功，就会生成两个完全不同的.class文件了，分别是outer.class和outer$inner.class。所以内部类的名字完全可以和它的外部类名字相同。

### 1.成员内部类

成员内部类可以无条件访问外部类的所有成员属性和成员方法（包括private成员和静态成员）。

> **1.为什么成员内部类可以无条件访问外部类的成员？**
>
> 在此之前，我们已经讨论过了成员内部类可以无条件访问外部类的成员，那具体究竟是如何实现的呢？下面通过反编译字节码文件看看究竟。事实上，编译器在进行编译的时候，会将成员内部类单独编译成一个字节码文件，下面是 Outter.java 的代码：
>
> ```java
> public class Outter {
> private Inner inner = null;
> public Outter() {
>    
> }
> 
> public Inner getInnerInstance() {
>   if(inner == null)
>       inner = new Inner();
>   return inner;
> }
> 
> protected class Inner {
>   public Inner() {
>        
>   }
> }
> }
> ```
>
> 编译之后，出现了两个字节码文件：
>
> ![image-20201123145620661](F:\Typora数据储存\高级语言\JAVA\JAVA.assets\image-20201123145620661.png)
>
> 反编译 Outter$Inner.class 文件得到下面信息：
>
> ```java
> E:\Workspace\Test\bin\com\cxh\test2>javap -v Outter$Inner
> Compiled from "Outter.java"
> public class com.cxh.test2.Outter$Inner extends java.lang.Object
> SourceFile: "Outter.java"
> InnerClass:
> #24= #1 of #22; //Inner=class com/cxh/test2/Outter$Inner of class com/cxh/tes
> t2/Outter
> minor version: 0
> major version: 50
> Constant pool:
> const #1 = class        #2;     //  com/cxh/test2/Outter$Inner
> const #2 = Asciz        com/cxh/test2/Outter$Inner;
> const #3 = class        #4;     //  java/lang/Object
> const #4 = Asciz        java/lang/Object;
> const #5 = Asciz        this$0;
> const #6 = Asciz        Lcom/cxh/test2/Outter;;
> const #7 = Asciz        <init>;
> const #8 = Asciz        (Lcom/cxh/test2/Outter;)V;
> const #9 = Asciz        Code;
> const #10 = Field       #1.#11; //  com/cxh/test2/Outter$Inner.this$0:Lcom/cxh/t
> est2/Outter;
> const #11 = NameAndType #5:#6;//  this$0:Lcom/cxh/test2/Outter;
> const #12 = Method      #3.#13; //  java/lang/Object."<init>":()V
> const #13 = NameAndType #7:#14;//  "<init>":()V
> const #14 = Asciz       ()V;
> const #15 = Asciz       LineNumberTable;
> const #16 = Asciz       LocalVariableTable;
> const #17 = Asciz       this;
> const #18 = Asciz       Lcom/cxh/test2/Outter$Inner;;
> const #19 = Asciz       SourceFile;
> const #20 = Asciz       Outter.java;
> const #21 = Asciz       InnerClasses;
> const #22 = class       #23;    //  com/cxh/test2/Outter
> const #23 = Asciz       com/cxh/test2/Outter;
> const #24 = Asciz       Inner;
> 
> {
> final com.cxh.test2.Outter this$0;
> 
> public com.cxh.test2.Outter$Inner(com.cxh.test2.Outter);
> Code:
> Stack=2, Locals=2, Args_size=2
> 0:   aload_0
> 1:   aload_1
> 2:   putfield        #10; //Field this$0:Lcom/cxh/test2/Outter;
> 5:   aload_0
> 6:   invokespecial   #12; //Method java/lang/Object."<init>":()V
> 9:   return
> LineNumberTable:
> line 16: 0
> line 18: 9
> 
> LocalVariableTable:
> Start  Length  Slot  Name   Signature
> 0      10      0    this       Lcom/cxh/test2/Outter$Inner;
> 
> 
> }
> ```
>
> 第11行到35行是常量池的内容，下面逐一第38行的内容：
>
> ```
> final com.cxh.test2.Outter this$0;
> ```
>
> 这行是一个指向外部类对象的指针，看到这里想必大家豁然开朗了。也就是说**编译器会默认为成员内部类添加了一个指向外部类对象的引用**，那么这个引用是如何赋初值的呢？下面接着看内部类的构造器：
>
> public com.cxh.test2.Outter$Inner(com.cxh.test2.Outter);
>
> 从这里可以看出，虽然我们在定义的内部类的构造器是无参构造器，编译器还是会默认添加一个参数，该参数的类型为指向外部类对象的一个引用，所以成员内部类中的 Outter this&0 指针便指向了外部类对象，因此可以在成员内部类中随意访问外部类的成员。**从这里也间接说明了成员内部类是依赖于外部类的，如果没有创建外部类的对象，则无法对 Outter this&0 引用进行初始化赋值，也就无法创建成员内部类的对象了。**


外部类中如果要访问成员内部类的成员，必须先创建一个成员内部类的对象，再通过指向这个对象的引用来访问。

成员内部类是最普通的内部类，它的定义为位于另一个类的内部，形如下面的形式：

```java
class Circle {
    double radius = 0;
     
    public Circle(double radius) {
        this.radius = radius;
    }
     
    class Draw {     //内部类
        public void drawSahpe() {
            System.out.println("drawshape");
        }
    }
}
```

不过要注意的是，当成员内部类拥有和外部类同名的成员变量或者方法时，会发生隐藏现象，即默认情况下访问的是成员内部类的成员。如果要访问外部类的同名成员，需要以下面的形式进行访问

> 外部类.this.成员变量
> 外部类.this.成员方法

如果要创建成员内部类的对象，前提是必须存在一个外部类的对象。创建成员内部类对象的一般方式如下：

```java
public class Test {
    public static void main(String[] args)  {
        //第一种方式：
        Outter outter = new Outter();
        Outter.Inner inner = outter.new Inner();  //必须通过Outter对象来创建
         
        //第二种方式：
        Outter.Inner inner1 = outter.getInnerInstance();
    }
}
 
class Outter {
    private Inner inner = null;
    public Outter() {
         
    }
     
    public Inner getInnerInstance() {
        if(inner == null)
            inner = new Inner();
        return inner;
    }
      
    class Inner {
        public Inner() {
             
        }
    }
}
```

**内部类可以拥有 private 访问权限、protected 访问权限、public 访问权限及包访问权限。**比如上面的例子，如果成员内部类 Inner 用 private 修饰，则只能在外部类的内部访问，如果用 public 修饰，则任何地方都能访问；如果用 protected 修饰，则只能在同一个包下或者继承外部类的情况下访问；如果是默认访问权限，则只能在同一个包下访问。这一点和外部类有一点不一样，**外部类只能被 public 和包访问两种权限修饰。**

### 2.局部内部类

局部内部类是定义在一个方法或者一个作用域里面的类，它和成员内部类的区别在于局部内部类的访问仅限于方法内或者该作用域内。

> **注意**: 局部内部类就像是方法里面的一个局部变量一样，是不能有 public、protected、private 以及 static 修饰符的。

```java
class People{
    public People() {
         
    }
}
 
class Man{
    public Man(){
         
    }
     
    public People getWoman(){
        class Woman extends People{   //局部内部类
            int age =0;
        }
        return new Woman();
    }
}
```

### 3.匿名内部类

匿名内部类可以使你的代码更加简洁，你可以在定义一个类的同时对其进行实例化。它与局部类很相似，不同的是它没有类名，如**果某个局部类你只需要用一次，那么你就可以使用匿名内部类**（lambda表达式也为有函数式接口的匿名内部类）

官方文档案例：

```java
public class HelloWorldAnonymousClasses {

    /**
     * 包含两个方法的HelloWorld接口
     */
    interface HelloWorld {
        public void greet();
        public void greetSomeone(String someone);
    }

    public void sayHello() {

        // 1、局部类EnglishGreeting实现了HelloWorld接口
        class EnglishGreeting implements HelloWorld {
            String name = "world";
            public void greet() {
                greetSomeone("world");
            }
            public void greetSomeone(String someone) {
                name = someone;
                System.out.println("Hello " + name);
            }
        }

        HelloWorld englishGreeting = new EnglishGreeting();

        // 2、匿名类实现HelloWorld接口
        HelloWorld frenchGreeting = new HelloWorld() {
            String name = "tout le monde";
            public void greet() {
                greetSomeone("tout le monde");
            }
            public void greetSomeone(String someone) {
                name = someone;
                System.out.println("Salut " + name);
            }
        };

        // 3、匿名类实现HelloWorld接口
        HelloWorld spanishGreeting = new HelloWorld() {
            String name = "mundo";
            public void greet() {
                greetSomeone("mundo");
            }
            public void greetSomeone(String someone) {
                name = someone;
                System.out.println("Hola, " + name);
            }
        };

        englishGreeting.greet();
        frenchGreeting.greetSomeone("Fred");
        spanishGreeting.greet();
    }

    public static void main(String... args) {
        HelloWorldAnonymousClasses myApp = new HelloWorldAnonymousClasses();
        myApp.sayHello();
    }
}
```

> 结果为：
>
> Hello world
> Salut Fred
> Hola, mundo

#### 匿名内部类的语法

**案例一，实现接口的匿名类：**

```java
HelloWorld frenchGreeting = new HelloWorld() {
   String name = "tout le monde";
   public void greet() {
         greetSomeone("tout le monde");
   }
   public void greetSomeone(String someone) {
        name = someone;
        System.out.println("Salut " + name);
   }
 };
```

 **案例二，匿名子类（继承父类）：**

```java
public class AnimalTest {

    private final String ANIMAL = "动物";

    public void accessTest() {
        System.out.println("匿名内部类访问其外部类方法");
    }

    class Animal {
        private String name;

        public Animal(String name) {
            this.name = name;
        }

        public void printAnimalName() {
            System.out.println(bird.name);
        }
    }

    // 鸟类，匿名子类，继承自Animal类，可以覆写父类方法
    Animal bird = new Animal("布谷鸟") {

        @Override
        public void printAnimalName() {
            accessTest();   　　　　　　　　// 访问外部类成员
            System.out.println(ANIMAL);  // 访问外部类final修饰的变量
            super.printAnimalName();
        }
    };

    public void print() {
        bird.printAnimalName();
    }

    public static void main(String[] args) {

        AnimalTest animalTest = new AnimalTest();
        animalTest.print();
    }
}
```

> 运行结果：
> 匿名内部类访问其外部类方法
> 动物
> 布谷鸟

#### 访问作用域内的局部变量、定义和访问匿名内部类成员

匿名内部类与局部类对作用域内的变量拥有相同的的访问权限。

(1)、匿名内部类可以访问外部内的所有成员；

(2)、匿名内部类不能访问外部类未加final修饰的变量（注意：JDK1.8即使没有用final修饰也可以访问）；

> **在JDK8之前，如果我们在匿名内部类中需要访问局部变量，那么这个局部变量必须用final修饰符修饰。**
>
> 用final修饰实际上就是为了保护数据的一致性。（防止并发问题导致的数据不一致性）
>
> 这里所说的数据一致性，对引用变量来说是引用地址的一致性，对基本类型来说就是值的一致性。(但是也都是变量保存的值的一致性)
>
> 若是基本类型，则解语法糖后，动态代理生成的内部类class的变量会有基本类型的副本，若是引用类型，则新建一个变量并引用同一个对象，即引用地址的复制
>
> **在JDK8中如果我们在匿名内部类中需要访问局部变量，那么这个局部变量不需要用final修饰符修饰。看似是一种编译机制的改变**，实际上就是一个语法糖（底层还是帮你加了final）。但通过反编译没有看到底层为我们加上final，但**我们无法改变这个局部变量的引用值，如果改变就会编译报错。**
>
> **为什么局部内部类和匿名内部类只能访问局部final变量？**（ 方法中的局部变量和形参都必须用 final 进行限定）？
>
> ```java
> public class Test {
>  public static void main(String[] args)  {
>       
>  }
>   
>  public void test() {
>      final int a = 10;
>      new Thread(){
>          public void run() {
>              System.out.println(a);
>          };
>      }.start();
>    }
>  }
> ```
> 
>这段代码会被编译成两个class文件：Test.class和Test1.class。**默认情况下，编译器会为匿名内部类和局部内部类起名为\**Outterx.class\**（x为正整数）**。
> 
>![image-20201123150007206](F:\Typora数据储存\高级语言\JAVA\JAVA.assets\image-20201123150007206.png)
> 
>当 test 方法执行完毕之后，变量a的生命周期就结束了，而此时 Thread 对象的生命周期很可能还没有结束，那么在 Thread 的 run 方法中继续访问变量 a 就变成不可能了，但是又要实现这样的效果，怎么办呢？
> 
>Java 采用了复制的手段来解决这个问题.
> 
>**如果变量编译期间可以确定**
> 
>```java
> public class Test {
>  public static void main(String[] args)  {
>          
>     }
>      
>     public void test() {
>         final int a = 10;
>         new Thread(){
>             public void run() {
>                 System.out.println(a);
>             };
>         }.start();
>     }
>    }
> ```
> 
>![image-20201123151512584](F:\Typora数据储存\高级语言\JAVA\JAVA.assets\image-20201123151512584.png)
> 
>我们看到在 run 方法中有一条指令：
> 
>```
> bipush 10
> ```
> 
>这条指令表示将操作数10压栈，表示使用的是一个本地局部变量。如果局部变量的值在编译期间就可以确定，则编译器默认会在匿名内部类（局部内部类）的常量池中添加一个内容相等的字面量或直接将相应的字节码嵌入到执行字节码中。
> 
>**如果变量编译期间不可以确定**
> 
>```java
> public class Test {
>  public static void main(String[] args)  {
>          
>     }
>      
>     public void test(final int a) {
>         new Thread(){
>             public void run() {
>                 System.out.println(a);
>             };
>         }.start();
>     }
>    }
> ```
> 
>反编译：
> 
>![image-20201123151717641](F:\Typora数据储存\高级语言\JAVA\JAVA.assets\image-20201123151717641.png)
> 
>
> 
>我们看到匿名内部类 Test$1 的构造器含有两个参数，一个是指向外部类对象的引用，一个是 int 型变量，很显然，这里是将变量 test 方法中的形参 a 以参数的形式传进来对匿名内部类中的拷贝（变量 a 的拷贝）进行赋值初始化。
> 
>**如果局部变量的值在编译期间就可以确定，则直接在匿名内部里面创建一个拷贝。如果局部变量的值无法在编译期间确定，则通过构造器传参的方式来对拷贝进行初始化赋值。**
> 
>从上面可以看出，在 run 方法中访问的变量 a 根本就不是 test 方法中的局部变量 a。这样一来就解决了前面所说的 生命周期不一致的问题。但是新的问题又来了，既然在 run 方法中访问的变量 a 和 test 方法中的变量 a 不是同一个变量，当在 run 方法中改变变量 a 的值的话，会出现什么情况？
> 
>对，会造成数据不一致性，这样就达不到原本的意图和要求。为了解决这个问题，java 编译器就限定必须将变量 a 限制为 final 变量，不允许对变量 a 进行更改（对于引用类型的变量，是不允许指向新的对象），这样数据不一致性的问题就得以解决了。

(3)、属性屏蔽，与内嵌类相同，匿名内部类定义的类型（如变量）会屏蔽其作用域范围内的其他同名类型（变量）：

(4)、**匿名内部类中不能定义静态属性、方法；**　　

```java
public class ShadowTest {
    public int x = 0;

    interface FirstLevel {
     void methodInFirstLevel(int x);
    }

    FirstLevel firstLevel =  new FirstLevel() {

        public int x = 1;

        public static String str = "Hello World";   // 编译报错

        public static void aa() {        // 编译报错
        }

        public static final String finalStr = "Hello World";  // 正常

        public void extraMethod() {  // 正常
            // do something
        }
    };
}
```

(5)、匿名内部类可以有常量属性（final修饰的属性）；

(6)、匿名内部内中可以定义属性，如上面代码中的代码:private int x = 1;

(7)、匿名内部内中可以可以有额外的方法（父接口、类中没有的方法）;

(8)、匿名内部内中可以定义内部类；

(9)、匿名内部内中可以对其他类进行实例化。

### 4.静态内部类

> **静态内部类有特殊的地方吗？**
>
> 从前面可以知道，静态内部类是不依赖于外部类的，也就说可以在不创建外部类对象的情况下创建内部类的对象。另外，静态内部类是不持有指向外部类对象的引用的，这个读者可以自己尝试反编译 class 文件看一下就知道了，是没有 **Outter this&0** 引用的。

静态内部类也是定义在另一个类里面的类，只不过在类的前面多了一个关键字static。静态内部类是不需要依赖于外部类的，这点和类的静态成员属性有点类似，并且**它不能使用外部类的非static成员变量或者方法**，这点很好理解，因为在没有外部类的对象的情况下，可以创建静态内部类的对象，如果允许访问外部类的非static成员就会产生矛盾，因为外部类的非static成员必须依附于具体的对象。

```java
public class Test {
    public static void main(String[] args)  {
        Outter.Inner inner = new Outter.Inner();
    }
}
 
class Outter {
    public Outter() {
         
    }
     
    static class Inner {
        public Inner() {
             
        }
    }
}
```

> 静态内部类的作用：**主要为了降低包的深度，方便类的使用**，静态内部类适用于包含类当中，但又不依赖于外在的类，不用使用外在类的非静态属性和方法，只是为了方便管理类结构而定义。在创建静态内部类的时候，不需要外部类对象的引用。 

### 内部类的使用场景和好处

为什么在 Java 中需要内部类？总结一下主要有以下四点：

- 1.每个内部类都能独立的继承一个接口的实现，所以无论外部类是否已经继承了某个(接口的)实现，对于内部类都没有影响。内部类使得多继承的解决方案变得完整。
- 2.方便将存在一定逻辑关系的类组织在一起，又可以对外界隐藏。
- 3.方便编写事件驱动程序。
- 4.方便编写线程代码。

关于成员内部类的继承问题。一般来说，内部类是很少用来作为继承用的。但是当用来继承的话，要注意两点：

- 1）成员内部类的引用方式必须为 Outter.Inner
- 2）构造器中必须有指向外部类对象的引用，并通过这个引用调用super()。这段代码摘自《Java编程思想》

```java
class WithInner {
    class Inner{
         
    }
}
class InheritInner extends WithInner.Inner {
      
    // InheritInner() 是不能通过编译的，一定要加上形参
    InheritInner(WithInner wi) {
        wi.super(); //必须有这句调用
    }
  
    public static void main(String[] args) {
        WithInner wi = new WithInner();
        InheritInner obj = new InheritInner(wi);
    }
}

```



# 枚举类（Enum）

https://blog.csdn.net/javazejian/article/details/71333103

## 原理

一个JAVA 在 JDK 加入的语法糖， 通过动态代理实现枚举对象的创建使用

例子如下：

```java
//枚举类型，使用关键字enum
enum Day {
    MONDAY, TUESDAY, WEDNESDAY,
    THURSDAY, FRIDAY, SATURDAY, SUNDAY
}
```

利用javac进行编译，得到的为Day.class，我们反编译后可看到解语法糖后的样子：

```java
//反编译Day.class
final class Day extends Enum
{
    //编译器为我们添加的静态的values()方法
    public static Day[] values()
    {
        return (Day[])$VALUES.clone();
    }
    //编译器为我们添加的静态的valueOf()方法，注意间接调用了Enum也类的valueOf方法
    public static Day valueOf(String s)
    {
        return (Day)Enum.valueOf(com/zejian/enumdemo/Day, s);
    }
    //私有构造函数
    private Day(String s, int i)
    {
        super(s, i);
    }
     //前面定义的7种枚举实例
    public static final Day MONDAY;
    public static final Day TUESDAY;
    public static final Day WEDNESDAY;
    public static final Day THURSDAY;
    public static final Day FRIDAY;
    public static final Day SATURDAY;
    public static final Day SUNDAY;
    private static final Day $VALUES[];

    static 
    {    
        //实例化枚举实例
        MONDAY = new Day("MONDAY", 0);
        TUESDAY = new Day("TUESDAY", 1);
        WEDNESDAY = new Day("WEDNESDAY", 2);
        THURSDAY = new Day("THURSDAY", 3);
        FRIDAY = new Day("FRIDAY", 4);
        SATURDAY = new Day("SATURDAY", 5);
        SUNDAY = new Day("SUNDAY", 6);
        $VALUES = (new Day[] {
            MONDAY, TUESDAY, WEDNESDAY, THURSDAY, FRIDAY, SATURDAY, SUNDAY
        });
    }
}
```

​	从反编译的代码可以看出编译器确实帮助我们生成了一个Day类(注意该类是final类型的，将无法被继承)而且**该类继承自java.lang.Enum类**，该类是一个抽象类(稍后我们会分析该类中的主要方法)，除此之外，**编译器还帮助我们生成了7个Day类型的实例对象分别对应枚举中定义的7个日期**，这也充分说明了我们前面使用关键字enum定义的Day类型中的每种日期枚举常量也是实实在在的Day实例对象，只不过代表的内容不一样而已。

## 常用方法

| 返回类型                      | 方法名称                                         | 方法说明                                                     |
| ----------------------------- | ------------------------------------------------ | ------------------------------------------------------------ |
| `int`                         | `compareTo(E o)`                                 | 比较此枚举与指定对象的顺序                                   |
| `boolean`                     | `equals(Object other)`                           | 当指定对象等于此枚举常量时，返回 true。                      |
| `Class<?>`                    | `getDeclaringClass()`                            | 返回与此枚举常量的枚举类型相对应的 Class 对象                |
| `String`                      | `name()`                                         | 返回此枚举常量的名称，在其枚举声明中对其进行声明             |
| `int`                         | `ordinal()`                                      | 返回枚举常量的序数（它在枚举声明中的位置，其中初始常量序数为零） |
| `String`                      | `toString()`                                     | 返回枚举常量的名称，它包含在声明中                           |
| `static<T extends Enum<T>> T` | `static valueOf(Class<T> enumType, String name)` | `返回带指定名称的指定枚举类型的枚举常量。`                   |

除此之外还要注意解语法糖时会为我们对于父类抽象类Enum的构造函数的调用，这个函数是只有编译器能调用的，**我们可以编写私有的构造函数，他会和Enum抽象类的构造函数由javac整合，但是仍无法自己进行实例化**，因为Enum能通过如对readResolve方法的修改禁止、防止反序列化单例模式的破坏，为了功能的完善，java强制由编译器实现其实例化，**因此只能靠解语法糖时编译器自动的实例化对象与初始化**

```java
//实现了Comparable
public abstract class Enum<E extends Enum<E>>
        implements Comparable<E>, Serializable {

    private final String name; //枚举字符串名称

    public final String name() {
        return name;
    }

    private final int ordinal;//枚举顺序值

    public final int ordinal() {
        return ordinal;
    }

    //枚举的构造方法，只能由编译器调用
    protected Enum(String name, int ordinal) {
        this.name = name;
        this.ordinal = ordinal;
    }

    public String toString() {
        return name;
    }

    public final boolean equals(Object other) {
        return this==other;
    }

    //比较的是ordinal值
    public final int compareTo(E o) {
        Enum<?> other = (Enum<?>)o;
        Enum<E> self = this;
        if (self.getClass() != other.getClass() && // optimization
            self.getDeclaringClass() != other.getDeclaringClass())
            throw new ClassCastException();
        return self.ordinal - other.ordinal;//根据ordinal值比较大小
    }

    @SuppressWarnings("unchecked")
    public final Class<E> getDeclaringClass() {
        //获取class对象引用，getClass()是Object的方法
        Class<?> clazz = getClass();
        //获取父类Class对象引用
        Class<?> zuper = clazz.getSuperclass();
        return (zuper == Enum.class) ? (Class<E>)clazz : (Class<E>)zuper;
    }


    public static <T extends Enum<T>> T valueOf(Class<T> enumType,
                                                String name) {
        //enumType.enumConstantDirectory()获取到的是一个map集合，key值就是name值，value则是枚举变量值   
        //enumConstantDirectory是class对象内部的方法，根据class对象获取一个map集合的值       
        T result = enumType.enumConstantDirectory().get(name);
        if (result != null)
            return result;
        if (name == null)
            throw new NullPointerException("Name is null");
        throw new IllegalArgumentException(
            "No enum constant " + enumType.get	CanonicalName() + "." + name);
    }

    //.....省略其他没用的方法
}
```

当枚举实例向上转型为Enum类型后，values()方法将会失效，**因为Enum类中并没有values()方法**，也就无法一次性获取所有枚举实例变量，但是由于Class对象的存在，有保存对所有Enum实例的map集合存储，即使不使用values()方法，还是有可能一次获取到所有枚举实例变量的，在Class对象中存在如下方法：

| 返回类型  | 方法名称             | 方法说明                                                     |
| --------- | -------------------- | ------------------------------------------------------------ |
| `T[]`     | `getEnumConstants()` | 返回该枚举类型的所有元素，如果Class对象不是枚举类型，则返回null。 |
| `boolean` | `isEnum()`           | 当且仅当该类声明为源代码中的枚举时返回 true                  |

```java
//正常使用
Day[] ds=Day.values();
//向上转型Enum
Enum e = Day.MONDAY;
//无法调用,没有此方法
//e.values();
//获取class对象引用
Class<?> clasz = e.getDeclaringClass();
if(clasz.isEnum()) {
    Day[] dsz = (Day[]) clasz.getEnumConstants();
    System.out.println("dsz:"+Arrays.toString(dsz));
}

/**
   输出结果:
   dsz:[MONDAY, TUESDAY, WEDNESDAY, THURSDAY, FRIDAY, SATURDAY, SUNDAY]
*/
```

## 枚举与单例模式：

枚举单例也是饿汉式,因为是在static代码块中实现的，因此随类的加载而加载。

但是，若是我们的一个单例类除了这个单例之外，没有其他静态变量或静态方法，那么直接用饿汉模式单例实现其实是很合适的，因为只有用到该单例才会加载该类，也是一种方式比较特殊的懒加载。

其中DCL因为是为了在方法中实现中使用，且实现要求线程安全和懒加载的功能，虽然多线程效率相交懒汉模式提升，但是因为该模式本身就是为了适应在多个方法的常规类中实现单例模式的，因此有其他很多的方法与类加载，其单例的加载可能远不如饿汉模式。

```java
/**
 * 枚举单利
 */
public enum  SingletonEnum {
    INSTANCE;
    private String name;
    public String getName(){
        return name;
    }
    public void setName(String name){
        this.name = name;
    }
}
```

枚举类的单例模式相较于其他的单例模式有两个优点：

1. 防止反序列化破坏单例模式 

> 常规的防止反序列化破坏单例模式：
>
> ```java
> //测试例子(四种写解决方式雷同)
> public class Singleton implements java.io.Serializable {     
>    public static Singleton INSTANCE = new Singleton();     
> 
>    protected Singleton() {     
>    }  
> 
>    //反序列时直接返回当前INSTANCE
>    private Object readResolve() {     
>             return INSTANCE;     
>       }    
> }   
> ```

2. 防止反射破坏单例模式 

> 常规的防止反射破坏单例模式：
>
> ```java
> public static Singleton INSTANCE = new Singleton();     
> private static volatile  boolean  flag = true;
> // 通过设置构造函数实现对构造器newInstance的改变
> private Singleton(){
>     if(flag){
>     flag = false;   
>     }else{
>         throw new RuntimeException("The instance  already exists ！");
>     }
> }
> ```

 但是上述两个若没有特殊的工具类动态实现，则需要我们逐个设置，过于繁琐，JAVA的枚举类就是出于这个目的出现的。他从JVM源码与反射源码实现针对枚举类进行了限制。

**枚举序列化是由jvm保证的,因为序列化的最终过程本身就由jvm实现**，每一个枚举类型和定义的枚举变量在JVM中都是唯一的，在枚举类型的序列化和反序列化上，Java做了特殊的规定：**在序列化时Java仅仅是将枚举对象的name属性输出到结果中，反序列化的时候则是通过java.lang.Enum的valueOf方法来根据名字查找枚举对象。同时，编译器是不允许任何对这种序列化机制的定制的并禁用了writeObject、readObject、readObjectNoData、writeReplace和readResolve等方法，从而保证了枚举实例的唯一性，**这里我们不妨再次看看Enum类的valueOf方法：

```java
public static <T extends Enum<T>> T valueOf(Class<T> enumType,
                                              String name) {
      T result = enumType.enumConstantDirectory().get(name);
      if (result != null)
          return result;
      if (name == null)
          throw new NullPointerException("Name is null");
      throw new IllegalArgumentException(
          "No enum constant " + enumType.getCanonicalName() + "." + name);
  }
```

实际上通过调用enumType(Class对象的引用)的enumConstantDirectory方法获取到的是一个Map集合，在该集合中存放了以枚举name为key和以枚举实例变量为value的Key&Value数据，因此通过name的值就可以获取到枚举实例，看看enumConstantDirectory方法源码：

```java
Map<String, T> enumConstantDirectory() {
        if (enumConstantDirectory == null) {
            //getEnumConstantsShared最终通过反射调用枚举类的values方法
            T[] universe = getEnumConstantsShared();
            if (universe == null)
                throw new IllegalArgumentException(
                    getName() + " is not an enum type");
            Map<String, T> m = new HashMap<>(2 * universe.length);
            //map存放了当前enum类的所有枚举实例变量，以name为key值
            for (T constant : universe)
                m.put(((Enum<?>)constant).name(), constant);
            enumConstantDirectory = m;
        }
        return enumConstantDirectory;
    }
    private volatile transient Map<String, T> enumConstantDirectory = null;
```

那么反射又是如何保证单例安全性的呢？

下面试图通过反射获取构造器并创建枚举:

```java
public static void main(String[] args) throws IllegalAccessException, InvocationTargetException, InstantiationException, NoSuchMethodException {
  //获取枚举类的构造函数(前面的源码已分析过)
   Constructor<SingletonEnum> constructor=SingletonEnum.class.getDeclaredConstructor(String.class,int.class);
   constructor.setAccessible(true);
   //创建枚举
   SingletonEnum singleton=constructor.newInstance("otherInstance",9);
  }
```

执行报错

```
Exception in thread "main" java.lang.IllegalArgumentException: Cannot reflectively create enum objects
    at java.lang.reflect.Constructor.newInstance(Constructor.java:417)
    at zejian.SingletonEnum.main(SingletonEnum.java:38)
    at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
    at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
    at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
    at java.lang.reflect.Method.invoke(Method.java:498)
    at com.intellij.rt.execution.application.AppMain.main(AppMain.java:144)
```

显然告诉我们不能使用反射创建枚举类，这是为什么呢？不妨看看newInstance方法源码：

```java
 public T newInstance(Object ... initargs)
        throws InstantiationException, IllegalAccessException,
               IllegalArgumentException, InvocationTargetException
    {
        if (!override) {
            if (!Reflection.quickCheckMemberAccess(clazz, modifiers)) {
                Class<?> caller = Reflection.getCallerClass();
                checkAccess(caller, clazz, null, modifiers);
            }
        }
        //这里判断Modifier.ENUM是不是枚举修饰符，如果是就抛异常
        if ((clazz.getModifiers() & Modifier.ENUM) != 0)
            throw new IllegalArgumentException("Cannot reflectively create enum objects");
        ConstructorAccessor ca = constructorAccessor;   // read volatile
        if (ca == null) {
            ca = acquireConstructorAccessor();
        }
        @SuppressWarnings("unchecked")
        T inst = (T) ca.newInstance(initargs);
        return inst;
    }
```

源码很了然，确实无法使用反射创建枚举实例，也就是说明了创建枚举实例只有编译器能够做到而已。显然枚举单例模式确实是很不错的选择，因此我们推荐使用它。**而且只是针对反射的构造器的newInstance（）方法，其他的没有限制的功能仍能使用**

## EnumMap

numMap是专门为枚举类型量身定做的Map实现，虽然使用其它的Map（如HashMap）也能完成相同的功能，但是使用EnumMap会更加高效，它只能接收同一枚举类型的实例作为键值且不能为null，由于枚举类型实例的数量相对固定并且有限，所以EnumMap使用数组来存放与枚举类型对应的值，毕竟数组是一段连续的内存空间，根据程序局部性原理，效率会相当高。

EnumMap与HashMap最大的区别在于其对不同的value对应的索引的计算方法，EnumMap是根据每个key的ordinal值作为数组的索引进行顺序存储的，因为枚举类本身的ordinal不同因此根本不需要进行冲突处理，而HashMap是进行hashCode进行计算的，因此其Entry数组需要进行冲突处理，在有hash冲突的情况下，EnumMap效率会较高。获取相应的值则读取key对象的ordinal值作为数组索引即可

```java
//创建一个具有指定键类型的空枚举映射。
EnumMap(Class<K> keyType) 
//创建一个其键类型与指定枚举映射相同的枚举映射，最初包含相同的映射关系（如果有的话）。     
EnumMap(EnumMap<K,? extends V> m) 
//创建一个枚举映射，从指定映射对其初始化。
EnumMap(Map<K,? extends V> m)       
```

与HashMap不同，它需要传递一个类型信息，即Class对象，通过这个参数EnumMap就可以根据类型信息初始化其内部数据结构，另外两只是初始化时传入一个Map集合，代码演示如下：

```java
//使用第一种构造
Map<Color,Integer> enumMap=new EnumMap<>(Color.class);
//使用第二种构造
Map<Color,Integer> enumMap2=new EnumMap<>(enumMap);
//使用第三种构造
Map<Color,Integer> hashMap = new HashMap<>();
hashMap.put(Color.GREEN, 2);
hashMap.put(Color.BLUE, 3);
Map<Color, Integer> enumMap = new EnumMap<>(hashMap);123456789
```

至于EnumMap的方法，跟普通的map几乎没有区别，注意与HashMap的主要不同在于构造方法需要传递类型参数和EnumMap保证Key顺序与枚举中的顺序一致，但请记住Key不能为null。

## EnumSet

EnumSet是与枚举类型一起使用的专用 Set 集合，EnumSet 中所有元素都必须是枚举类型。与其他Set接口的实现类HashSet/TreeSet(内部都是用对应的HashMap/TreeMap实现的)不同的是，EnumSet在内部实现是位向量(稍后分析)，它是一种极为高效的位运算操作，由于直接存储和操作都是bit，因此EnumSet空间和时间性能都十分可观，足以媲美传统上基于 int 的“位标志”的运算，重要的是我们可像操作set集合一般来操作位运算，这样使用代码更简单易懂同时又具备类型安全的优势。注意EnumSet不允许使用 null 元素。试图插入 null 元素将抛出 NullPointerException，但试图测试判断是否存在null 元素或移除 null 元素则不会抛出异常，与大多数collection 实现一样，EnumSet不是线程安全的，因此在多线程环境下应该注意数据同步问题，ok~，下面先来简单看看EnumSet的使用方式。

### EnumSet用法

创建EnumSet并不能使用new关键字，因为它是个抽象类，而应该使用其提供的静态工厂方法，EnumSet的静态工厂方法比较多，如下：

```java
//创建一个具有指定元素类型的空EnumSet。
EnumSet<E>  noneOf(Class<E> elementType)       
//创建一个指定元素类型并包含所有枚举值的EnumSet
<E extends Enum<E>> EnumSet<E> allOf(Class<E> elementType)
// 创建一个包括枚举值中指定范围元素的EnumSet
<E extends Enum<E>> EnumSet<E> range(E from, E to)
// 初始集合包括指定集合的补集
<E extends Enum<E>> EnumSet<E> complementOf(EnumSet<E> s)
// 创建一个包括参数中所有元素的EnumSet
<E extends Enum<E>> EnumSet<E> of(E e)
<E extends Enum<E>> EnumSet<E> of(E e1, E e2)
<E extends Enum<E>> EnumSet<E> of(E e1, E e2, E e3)
<E extends Enum<E>> EnumSet<E> of(E e1, E e2, E e3, E e4)
<E extends Enum<E>> EnumSet<E> of(E e1, E e2, E e3, E e4, E e5)
<E extends Enum<E>> EnumSet<E> of(E first, E... rest)
//创建一个包含参数容器中的所有元素的EnumSet
<E extends Enum<E>> EnumSet<E> copyOf(EnumSet<E> s)
<E extends Enum<E>> EnumSet<E> copyOf(Collection<E> c)
```

代码如下：

```java
package zejian;

import java.util.ArrayList;
import java.util.EnumSet;
import java.util.List;

/**
 * Created by wuzejian on 2017/5/12.
 *
 */
enum Color {
    GREEN , RED , BLUE , BLACK , YELLOW
}


public class EnumSetDemo {

    public static void main(String[] args){

        //空集合
        EnumSet<Color> enumSet= EnumSet.noneOf(Color.class);
        System.out.println("添加前："+enumSet.toString());
        enumSet.add(Color.GREEN);
        enumSet.add(Color.RED);
        enumSet.add(Color.BLACK);
        enumSet.add(Color.BLUE);
        enumSet.add(Color.YELLOW);
        System.out.println("添加后："+enumSet.toString());

        System.out.println("-----------------------------------");

        //使用allOf创建包含所有枚举类型的enumSet，其内部根据Class对象初始化了所有枚举实例
        EnumSet<Color> enumSet1= EnumSet.allOf(Color.class);
        System.out.println("allOf直接填充："+enumSet1.toString());

        System.out.println("-----------------------------------");

        //初始集合包括枚举值中指定范围的元素
        EnumSet<Color> enumSet2= EnumSet.range(Color.BLACK,Color.YELLOW);
        System.out.println("指定初始化范围："+enumSet2.toString());

        System.out.println("-----------------------------------");

        //指定补集，也就是从全部枚举类型中去除参数集合中的元素，如下去掉上述enumSet2的元素
        EnumSet<Color> enumSet3= EnumSet.complementOf(enumSet2);
        System.out.println("指定补集："+enumSet3.toString());

        System.out.println("-----------------------------------");

        //初始化时直接指定元素
        EnumSet<Color> enumSet4= EnumSet.of(Color.BLACK);
        System.out.println("指定Color.BLACK元素："+enumSet4.toString());
        EnumSet<Color> enumSet5= EnumSet.of(Color.BLACK,Color.GREEN);
        System.out.println("指定Color.BLACK和Color.GREEN元素："+enumSet5.toString());

        System.out.println("-----------------------------------");

        //复制enumSet5容器的数据作为初始化数据
        EnumSet<Color> enumSet6= EnumSet.copyOf(enumSet5);
        System.out.println("enumSet6："+enumSet6.toString());

        System.out.println("-----------------------------------");

        List<Color> list = new ArrayList<Color>();
        list.add(Color.BLACK);
        list.add(Color.BLACK);//重复元素
        list.add(Color.RED);
        list.add(Color.BLUE);
        System.out.println("list:"+list.toString());

        //使用copyOf(Collection<E> c)
        EnumSet enumSet7=EnumSet.copyOf(list);
        System.out.println("enumSet7:"+enumSet7.toString());

        /**
         输出结果：
         添加前：[]
         添加后：[GREEN, RED, BLUE, BLACK, YELLOW]
         -----------------------------------
         allOf直接填充：[GREEN, RED, BLUE, BLACK, YELLOW]
         -----------------------------------
         指定初始化范围：[BLACK, YELLOW]
         -----------------------------------
         指定补集：[GREEN, RED, BLUE]
         -----------------------------------
         指定Color.BLACK元素：[BLACK]
         指定Color.BLACK和Color.GREEN元素：[GREEN, BLACK]
         -----------------------------------
         enumSet6：[GREEN, BLACK]
         -----------------------------------
         list:[BLACK, BLACK, RED, BLUE]
         enumSet7:[RED, BLUE, BLACK]
         */
    }

}

```

`noneOf(Class<E> elementType)`静态方法，主要用于创建一个空的EnumSet集合，传递参数elementType代表的是枚举类型的类型信息，即Class对象。`EnumSet<E> allOf(Class<E> elementType)`静态方法则是创建一个填充了elementType类型所代表的所有枚举实例，奇怪的是EnumSet提供了多个重载形式的of方法，最后一个接受的的是可变参数，其他重载方法则是固定参数个数，EnumSet之所以这样设计是因为可变参数的运行效率低一些，所有在参数数据不多的情况下，强烈***不建议\***使用传递参数为可变参数的of方法，即`EnumSet<E> of(E first, E... rest)`，(**注意参数是E…**)其他方法就不分析了，看代码演示即可。至于EnumSet的操作方法，则与set集合是一样的，可以看API即可这也不过多说明。什么时候使用EnumSet比较恰当的，事实上当需要进行位域运算，就可以使用EnumSet提到位域，如下：

```java
public class EnumSetDemo {
    //定义位域变量
    public static final int TYPE_ONE = 1 << 0 ; //1
    public static final int TYPE_TWO = 1 << 1 ; //2
    public static final int TYPE_THREE = 1 << 2 ; //4
    public static final int TYPE_FOUR = 1 << 3 ; //8
    public static void main(String[] args){
        //位域运算
        int type= TYPE_ONE | TYPE_TWO | TYPE_THREE |TYPE_FOUR;
    }
}
```

诸如上述情况，我们都可以将上述的类型定义成枚举然后采用EnumSet来装载，进行各种操作，这样不仅不用手动编写太多冗余代码，而且使用EnumSet集合进行操作也将使代码更加简洁明了。

```java
enum Type{
    TYPE_ONE,TYPE_TWO,TYPE_THREE,TYPE_FOUR 
}

public class EnumSetDemo {
    public static void main(String[] args){
    EnumSet set =EnumSet.of(Type.TYPE_ONE,Type.TYPE_FOUR);
    }
}
```

**其实EnumSet最有价值的是其内部实现原理，采用的是位向量，它体现出来的是一种高效的数据处理方式，这点很值得我们去学习它。**

# 创建对象的五种方式

new和反射因为灵活性较强，使用较为方便，且很多在JDK库中抽象了功能实现，容易学习，后面的反序列化和clone其实一般这样使用较少，且很多原理都是JVM层面的实现，学习难度较大

## new

new 属于硬编码方式，在javac编译时已经静态分派进行调用，首先在方法区的常量池中查看是否有new 后面参数（也就是类名）的符号引用，并检查是否有类的加载信息也就是是否被加载解析和初始化过。如果已经加载过了就不在加载，否则执行类的加载全过程

执行完类的加载后仍需由内存管理器完成对实例对象的内存划分，然后再执行 <init> 的实例对象初始化	

> new对象的class文件必须在类路径中，new 是编译期确定的类型划分



## 反射

通过对类对象中保存的实例对象的信息进行对象创建

有两个步骤：

1. 首先我们得拿到类对象的Class

   如何获取? 有三种方式：

   - 类.class，如Person.class（字面量获取，类的加载只需到加载阶段）
   - 对象.getClass()
   - Class.forName("类全路径")

2. 通过反射创建类对象的实例对象

   * 使用Class.newInstance()创建对象

   > newInstance创建对象实例的时候会调用无参的构造函数，所以必需确保类中有无参数的**可见的**构造函数，否则将会抛出异常。

   * 使用类对象提供的构造器对象的newInstance()方法



## clone

当我们实现一个cloneable接口并且使用object类提供的默认的clone()方法 (native 实现)时，**JVM会在堆中以二进制流的方式进行==浅拷贝==，重新分配一个内存块并直接对其中新创建的基本变量类型或引用类型变量用拷贝的方式初始化，并不会用到构造函数，也没必要。**

> 注意：
>
>  1.clone是Object中的方法，Cloneable是一个标识接口，它表明这个类的对象是可以拷贝的。如果没有实现Cloneable接口却调用了clone()函数将抛出异常
>
>  2.Object.clone()未做同步处理，线程不安全 
>
>  3.clone()有深拷贝和浅拷贝两种方式

#### 深拷贝和浅拷贝

https://segmentfault.com/a/1190000010648514

1、浅拷贝：对基本数据类型进行值传递，对引用数据类型进行引用传递般的拷贝，此为浅拷贝。

![image-20201207203257274](F:\Typora数据储存\高级语言\JAVA\JAVA.assets\image-20201207203257274.png)



2、深拷贝：对基本数据类型进行值传递，对引用数据类型，创建一个新的对象，并复制其内容，此为深拷贝。

![image-20201207203320597](F:\Typora数据储存\高级语言\JAVA\JAVA.assets\image-20201207203320597.png)

3、 clone 接口的默认实现为浅拷贝

![image-20201207203540754](F:\Typora数据储存\高级语言\JAVA\JAVA.assets\image-20201207203540754.png)

4、 实现深拷贝的方式有两种（不包括自己新建一个对象，再新建所有的基本数据类型和引用对象）

> 1. 反序列化，但需要定反序列化规则
> 2. 重写clone方法

## 反序列化

- **序列化：将对象写入到IO流中**
- **反序列化：从IO流中恢复对象**

**什么情况下需要序列化？**

a)当你想把的内存中的对象状态保存到一个文件中或者数据库中时候;

b)当你想用套接字在网络上传送对象的时候;

c)当你想通过RMI（远程方法调用）传输对象的时候;

**通常建议：程序创建的每个JavaBean类都实现Serializeable接口。**

> Serializable 是java的序列化接口，使用简单但是开销比较大，序列化和反序列化都涉及到大量的I/O操作，若是用来创建新对象效率相对较低。

https://juejin.cn/post/6844903848167866375

### 序列化实现方式

#### **1、Serializable**

##### 1.1 普通序列化

write：

```java
public class Person implements Serializable {
 private String name;
 private int age;
 //我不提供无参构造器
 public Person(String name, int age) {
   this.name = name;
   this.age = age;
 }

 @Override
 public String toString() {
   return "Person{" +
       "name='" + name + '\'' +
       ", age=" + age +
       '}';
 }
}

public class WriteObject {
 public static void main(String[] args) {
   try (//创建一个ObjectOutputStream输出流
      ObjectOutputStream oos = new ObjectOutputStream(new FileOutputStream("object.txt"))) {
     //将对象序列化到文件s
     Person person = new Person("9龙", 23);
     oos.writeObject(person);
   } catch (Exception e) {
     e.printStackTrace();
   }
 }
}
```

read:

```java
public class Person implements Serializable {
 private String name;
 private int age;
 //我不提供无参构造器
 public Person(String name, int age) {
   System.out.println("反序列化，你调用我了吗？");
   this.name = name;
   this.age = age;
 }

 @Override
 public String toString() {
   return "Person{" +
       "name='" + name + '\'' +
       ", age=" + age +
       '}';
 }
}

public class ReadObject {
 public static void main(String[] args) {
   try (//创建一个ObjectInputStream输入流
      ObjectInputStream ois = new ObjectInputStream(new FileInputStream("person.txt"))) {
     Person brady = (Person) ois.readObject();
     System.out.println(brady);
   } catch (Exception e) {
     e.printStackTrace();
   }
 }
}
//输出结果
//Person{name='9龙', age=23}
```

**反序列化并不会调用构造方法。反序列的对象是由JVM自己生成的对象，==不通过构造方法生成==。**

##### 1.2 成员是引用的序列化

**如果一个可序列化的类的成员不是基本类型，也不是String类型，那这个引用类型也必须是可序列化的；否则，会导致此类不能序列化。**

##### 1.3 同一对象序列化多次的机制

**同一对象序列化多次，会将这个对象序列化多次吗？**答案是**否定**的。

##### 1.4 java序列化算法潜在的问题

由于java序利化算法不会重复序列化同一个对象，只会记录已序列化对象的编号。**如果序列化一个可变对象（对象内的内容可更改）后，更改了对象内容，再次序列化，并不会再次将此对象转换为字节序列，而只是保存序列化编号。**

##### 1.5 可选的自定义序列化

1. **使用transient关键字选择不需要序列化的字段。**

2. 重写writeObject与readObject方法

3. > ANY-ACCESS-MODIFIER Object writeReplace() throws ObjectStreamException;
   >   ANY-ACCESS-MODIFIER Object readResolve() throws ObjectStreamException; 常用于处理反序列创建单例模式的对象时，防止破坏单例模式的措施



#### 2、Externalizable：强制自定义序列化

通过实现Externalizable接口，必须实现writeExternal、readExternal方法。

区别：Serializable需要更多JVM层的跳转调用，所以效率更低

![image-20201207220255941](F:\Typora数据储存\高级语言\JAVA\JAVA.assets\image-20201207220255941.png)

### 序列化版本号serialVersionUID

我们之前说的对象的确认是依据版本号的，但是，若是JVM升级可能对于class会有不同的计算版本号的策略，而序列化到硬盘中的字节流没有升级，因此就有了序列化版本号，private static final long serialVersionUID可以作为唯一确定的标签，防止我们本地的class版本号计算和我们序列化的文件中的字节流的版本号不同的不同，导致反序列化失败。

但是，若是本身class文件内容有更改了，我们也需要更改版本号以免反序列化出不是我们想要的对象

> 如果只是修改了方法，反序列化不容影响，则无需修改版本号；
>
> 如果只是修改了静态变量，瞬态变量（transient修饰的变量），反序列化不受影响，无需修改版本号；
>
> 如果修改了非瞬态变量，则可能导致反序列化失败。**如果新类中实例变量的类型与序列化时类的类型不一致，则会反序列化失败，这时候需要更改serialVersionUID。**如果只是新增了实例变量，则反序列化回来新增的是默认值；如果减少了实例变量，反序列化时会忽略掉减少的实例变量。



### 注意事项

 被static修饰的变量应该也是不会被序列化的，因为只有堆内存会被序列化.所以静态变量会天生不会被序列化。反序列化用到静态变量时，我们都是通过类对象进行静态变量的查询的，若是类已加载则读取相应的静态变量池中的存储的静态变量，否则，进行类的加载并将类变量，静态变量会实例并初始。

## UnSafe

sun.misc.Unsafe中提供`allocateInstance`方法，仅通过Class对象就可以创建此类的实例对象，**而且不需要调用其构造函数、初始化代码、JVM安全检查等。**它抑制修饰符检测，也就是即使构造器是private修饰的也能通过此方法实例化，只需提类对象即可创建相应的对象。由于这种特性，allocateInstance在java.lang.invoke、Objenesis（提供绕过类构造器的对象生成方式）、Gson（反序列化时用到）中都有相应的应用。

> 通过构造函数和非构造函数实现的区别

```java
package cn.eft.llj.unsafe;
import java.lang.reflect.Field;

import sun.misc.Unsafe;
public class Demo9 {

    static Unsafe unsafe;

    static {
        //获取Unsafe对象
        try {
            Field field = Unsafe.class.getDeclaredField("theUnsafe");
            field.setAccessible(true);
            unsafe = (Unsafe) field.get(null);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    static class C1 {
        private String name;

        private C1() {
            System.out.println("C1 default constructor!");
        }

        private C1(String name) {
            this.name = name;
            System.out.println("C1 有参 constructor!");
        }
        
        public void test(){
            System.out.println("执行了test方法");
        }
    }

    public static void main(String[] args) throws InstantiationException {
        C1 c= (C1) unsafe.allocateInstance(C1.class);
        System.out.println(c);
        c.test();
    }
}

```

输出结果：

```java
cn.eft.llj.unsafe.Demo9$C1@6bc7c054
执行了test方法
```



# 反射

https://blog.csdn.net/javazejian/article/details/70768369#class%E5%AF%B9%E8%B1%A1%E7%9A%84%E5%8A%A0%E8%BD%BD

由反射需要借助Class对象本身或者constructor的newInstance()方法生成实例，联想new与class对象是否是一样的交互流程，这就涉及到了类加载时最好的初始化阶段<clinit>类的初始化后或者若是其中有对实例化的初始化时<init>是如何实例化对象的

实例化类对象后并进行clinit后，会将控制权交由内存管理器分配内存，再进行默认初始化，再进行对象构造函数对应的init函数调用，这个过程都是由JVM的内存管理器进行管理进行顺序调用，而不是由一个函数递归到底

## Class对象的加载

- 获取Class对象引用的方式3种，通过继承自Object类的getClass方法，Class类的静态方法forName以及字面常量的方式”.class”。
- 其中实例类的getClass方法和Class类的静态方法forName都将会触发类的初始化阶段，而字面常量获取Class对象的方式则不会触发初始化，若是定义了**静态常量**，则可以在此时经由常量池使用。
- 初始化是类加载的最后一个阶段，也就是说完成这个阶段后类也就加载到内存中(Class对象在加载阶段已被创建)，此时可以对类进行各种必要的操作了（如new对象，调用静态成员等），注意在这个阶段，才真正开始执行类中定义的Java程序代码或者字节码。

## 泛化Class的作用

在Java SE5引入泛型后，使用我们可以利用泛型来表示Class对象更具体的类型，即使在运行期间会被擦除，但编译期足以确保我们使用正确的对象类型。如下：

```java
/**
 * Created by zejian on 2017/4/30.
 * Blog : http://blog.csdn.net/javazejian [原文地址,请尊重原创]
 */
public class ClazzDemo {

    public static void main(String[] args){
        //没有泛型
        Class intClass = int.class;

        //带泛型的Class对象
        Class<Integer> integerClass = int.class;

        integerClass = Integer.class;

        //没有泛型的约束,可以随意赋值
        intClass= double.class;

        //编译期错误,无法编译通过
        integerClass = double.class
        
        //编译无法通过,Integer的Class对象并非Number的Class对象的子类
        //若是Class对象换为实例对象，则子类可通过父类的泛型检验
		Class<Number> numberClass=Integer.class;
        
        //当然我们可以利用通配符“?”来解决问题
        //编译通过！
        Class<? extends Number> clazz = Integer.class;
        //赋予其他类型
        clazz = double.class;
        clazz = Number.class;
    }
}

```

## 关于类型转换的问题

```java
package com.zejian;

/**
 * Created by zejian on 2017/4/30.
 * Blog : http://blog.csdn.net/javazejian [原文地址,请尊重原创]
 */
public class ClassCast {

 public void cast(){

     Animal animal= new Dog();
     //强制转换
     Dog dog = (Dog) animal;
     
     //在Java SE5中新增一种使用Class对象进行类型转换的方式，如下：
     //上述两句话等于：
     Class<Dog> dogType = Dog.class;
     Dog dog = dogType.cast(animal)
 }
}

interface Animal{ }

class Dog implements  Animal{ }
```

## instanceof ==关键字==与isInstance==方法==

关于instanceof 关键字，它返回一个boolean类型的值，意在告诉我们对象是不是某个特定的类型实例。如下，在强制转换前利用instanceof检测obj是不是Animal类型的实例对象，如果返回true再进行类型转换，这样可以避免抛出类型转换的异常(ClassCastException)

```java
public void cast2(Object obj){
    if(obj instanceof Animal){
          Animal animal= (Animal) obj;
      }
}12345
```

而isInstance方法则是**Class类中的一个Native方法**，也是用于判断对象类型的，看个简单例子：

```java
public void cast2(Object obj){
        //instanceof关键字
        if(obj instanceof Animal){
            Animal animal= (Animal) obj;
        }

        //isInstance方法
        if(Animal.class.isInstance(obj)){
            Animal animal= (Animal) obj;
        }
  }
```

**事实上instanceOf 与isInstance方法产生的结果是相同的。**对于instanceOf是关键字只被用于对象引用变量，检查左边对象是不是右边类或接口的实例化。如果被测对象是null值，则测试结果总是false。一般形式：

```
//判断这个对象是不是这种类型
obj.instanceof(class)
```

而isInstance方法则是Class类的Native方法，其中obj是被测试的对象或者变量，如果obj是调用这个方法的class或接口的实例，则返回true。如果被检测的对象是null或者基本类型，那么返回值是false;一般形式如下：

```
//判断这个对象能不能被转化为这个类
class.inInstance(obj)
```

## Class与Reflect

Class类通过java.lang.reflect包中的的类库对反射技术进行全力的支持,常用的类有:Constructor,Field,Method类

### Constructor类及其用法

Constructor类存在于反射包(java.lang.reflect)中，反映的是Class 对象所表示的类的构造方法。获取Constructor对象是通过Class类中的方法获取的，Class类与Constructor相关的主要方法如下：

| 方法返回值         | 方法名称                                             | 方法说明                                                  |
| ------------------ | ---------------------------------------------------- | --------------------------------------------------------- |
| `static Class<?>`  | forName(String className)                            | 返回与带有给定字符串名的类或接口相关联的 Class 对象。     |
| `Constructor<T>`   | `getConstructor(Class<?>... parameterTypes)`         | 返回指定参数类型、具有public访问权限的构造函数对象        |
| `Constructor<?>[]` | getConstructors()                                    | 返回所有具有public访问权限的构造函数的Constructor对象数组 |
| `Constructor<T>`   | `getDeclaredConstructor(Class<?>... parameterTypes)` | 返回指定参数类型、所有声明的（包括private）构造函数对象   |
| `Constructor<?>[]` | `getDeclaredConstructor()`                           | 返回所有声明的（包括private）构造函数对象                 |
| T                  | newInstance()                                        | 创建此 Class 对象所表示的类的一个新实例。                 |

> 能获取指定重载类型的原因:
>
> - 无法以返回值类型作为重载函数的区分标准。
> - 无法以访问修饰符作为重载函数的区分标准.
>
> Class类的newInstance()方法会实例化默认构造方法,且要求构造函数中存在无参可见的构造函数,否则会抛出异常

关于Constructor类本身一些常用方法如下(仅部分，其他可查API)

| 方法返回值   | 方法名称                          | 方法说明                                                     |
| ------------ | --------------------------------- | ------------------------------------------------------------ |
| `Class<T>`   | `getDeclaringClass()`             | 返回 Class 对象，该对象表示声明由此 Constructor 对象表示的构造方法的类,其实就是返回真实类型（不包含参数） |
| `Type[]`     | `getGenericParameterTypes()`      | 按照声明顺序返回一组 Type 对象，返回的就是 Constructor对象构造函数的形参类型。 |
| `String`     | `getName()`                       | 以字符串形式返回此构造方法的名称。                           |
| `Class<?>[]` | `getParameterTypes()`             | 按照声明顺序返回一组 Class 对象，即返回Constructor 对象所表示构造方法的形参类型 |
| `T`          | `newInstance(Object... initargs)` | 使用此 Constructor对象表示的构造函数来创建新实例             |
| String       | `toGenericString()`               | 返回描述此 Constructor 的字符串，其中包括类型参数。          |

> 其中关于Type类型这里简单说明一下，Type 是 Java 编程语言中所有类型的公共高级接口。它们包括原始类型、参数化类型、数组类型、类型变量和基本类型。`getGenericParameterTypes` 与 `getParameterTypes` 都是获取构成函数的参数类型，前者返回的是Type类型，后者返回的是Class类型，由于Type顶级接口，Class也实现了该接口，因此Class类是Type的子类，Type 表示的全部类型而每个Class对象表示一个具体类型的实例，如`String.class`仅代表String类型。由此看来Type与 Class 表示类型几乎是相同的，只不过 Type表示的范围比Class要广得多而已。当然Type还有其他子类，如：
>
> - TypeVariable：表示类型参数，可以有上界，比如：T extends Number
> - ParameterizedType：表示参数化的类型，有原始类型和具体的类型参数，比如：`List<String>`
> - WildcardType：表示通配符类型，比如：?, ? extends Number, ? super Integer

#### 构造方法

1. 构造方法的作用：对对象进行初始化.

2. 构造函数与普通函数的区别：

   （1）. 一般函数是用于定义对象应该具备的功能。而构造函数定义的是，对象在调用功能之前，在建立时，应该具备的一些内容。也就是对象的初始化内容。

   （2）. 构造函数是在对象建立时由jvm调用, 给对象初始化。一般函数是对象建立后，当对象调用该功能时才会执行。

   （3）. 普通函数可以使用对象多次调用，构造函数就在创建对象时调用。

   （4）. 构造函数的函数名要与类名一样，而普通的函数只要符合标识符的命名规则即可。

   （5）. 构造函数没有返回值类型。

      (6)  .  构造方法若是没有调用super (),解语法糖时都会默认添加

3. 构造函数要注意的细节：

   （1）. 当类中没有定义构造函数时，系统会指定给该类加上一个空参数的构造函数。这个是类中默认的构造函数。当类中如果自定义了构造函数，这时**默认的构造函数就没有了。**

   （2）.在一个类中可以定义多个构造函数，以进行不同的初始化。多个构造函数存在于类中，是以重载的形式体现的。因为构造函数的名称都相同。

4. 构造代码块：

   构造代码块作用：给所有的对象进行统一的初始化。

   具体作用：

    1：给对象进行初始化。对象一建立就运行并且优先于构造函数。

    2：与构造函数区别

     1：构造代码块和构造函数的区别，构造代码块是给所有对象进行统一初始化， 构造函数给对应的对象初始化。

     2：构造代码块的作用：它的作用就是将所有构造方法中公共的信息进行抽取。

### Field类及其用法

Field 提供有关类或接口的单个字段的信息，以及对它的动态访问权限。反射的字段可能是一个类（静态）字段或实例字段。同样的道理，我们可以通过Class类的提供的方法来获取代表字段信息的Field对象，Class类与Field对象相关方法如下：

| 方法返回值 | 方法名称                        | 方法说明                                                     |
| ---------- | ------------------------------- | ------------------------------------------------------------ |
| `Field`    | `getDeclaredField(String name)` | 获取指定name名称的(包含private修饰的)字段，不包括继承的字段  |
| `Field[]`  | `getDeclaredField()`            | 获取Class对象所表示的类或接口的所有(包含private修饰的)字段,不包括继承的字段 |
| `Field`    | `getField(String name)`         | 获取指定name名称、具有**public修饰**的字段，**包含继承字段** |
| `Field[]`  | `getField()`                    | 获取修饰符为public的字段，包含继承字段                       |

关于Field类还有其他常用的方法如下：

| 方法返回值 | 方法名称                        | 方法说明                                                     |
| ---------- | ------------------------------- | ------------------------------------------------------------ |
| `void`     | `set(Object obj, Object value)` | 将指定对象变量上此 Field 对象表示的字段设置为指定的新值。    |
| `Object`   | `get(Object obj)`               | 返回指定对象上此 Field 表示的字段的值                        |
| `Class<?>` | `getType()`                     | 返回一个 Class 对象，它标识了此Field 对象所表示字段的声明类型。 |
| `boolean`  | `isEnumConstant()`              | 如果此字段表示枚举类型的元素则返回 true；否则返回 false      |
| `String`   | `toGenericString()`             | 返回一个描述此 Field（包括其一般类型）的字符串               |
| `String`   | `getName()`                     | 返回此 Field 对象表示的字段的名称                            |
| `Class<?>` | `getDeclaringClass()`           | 返回表示类或接口的 Class 对象，该类或接口声明由此 Field 对象表示的字段 |
| void       | setAccessible(boolean flag)     | 将此对象的 accessible 标志设置为指示的布尔值,即设置其可访问性 |

> 需要特别注意的是被final关键字修饰的Field字段是安全的，在运行时可以接收任何修改，但最终其实际值是不会发生改变的。

### Method类及其用法

Method 提供关于类或接口上单独某个方法（以及如何访问该方法）的信息，所反映的方法可能是类方法或实例方法（包括抽象方法）。下面是Class类获取Method对象相关的方法：

| 方法返回值 | 方法名称                                                     | 方法说明                                                     |
| ---------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| `Method`   | `getDeclaredMethod(String name, Class<?>... parameterTypes)` | 返回一个指定参数的Method对象，该对象反映此 Class 对象所表示的类或接口的指定已声明方法。 |
| `Method[]` | `getDeclaredMethod()`                                        | 返回 Method 对象的一个数组，这些对象反映此 Class 对象表示的类或接口声明的所有方法，包括公共、保护、默认（包）访问和私有方法，但不包括继承的方法。 |
| `Method`   | `getMethod(String name, Class<?>... parameterTypes)`         | 返回一个 Method 对象，它反映此 Class 对象所表示的类或接口的指定公共成员方法。 |
| `Method[]` | `getMethods()`                                               | 返回一个包含某些 Method 对象的数组，这些对象反映此 Class 对象所表示的类或接口（包括那些由该类或接口声明的以及从超类和超接口继承的那些的类或接口）的公共 member 方法。 |

关于Method类本身其他常用的方法如下：

| 方法返回值   | 方法名称                             | 方法说明                                                     |
| ------------ | ------------------------------------ | ------------------------------------------------------------ |
| `Object`     | `invoke(Object obj, Object... args)` | 对带有指定参数的指定对象调用由此 Method 对象表示的底层方法。 |
| `Class<?>`   | `getReturnType()`                    | 返回一个 Class 对象，该对象描述了此 Method 对象所表示的方法的正式返回类型,即方法的返回类型 |
| Type         | `getGenericReturnType()`             | 返回表示由此 Method 对象所表示方法的正式返回类型的 Type 对象，也是方法的返回类型。 |
| `Class<?>[]` | `getParameterTypes()`                | 按照声明顺序返回 Class 对象的数组，这些对象描述了此 Method 对象所表示的方法的形参类型。即返回方法的参数类型组成的数组 |
| `Type[]`     | `getGenericParameterTypes()`         | 按照声明顺序返回 Type 对象的数组，这些对象描述了此 Method 对象所表示的方法的形参类型的，也是返回方法的参数类型 |
| `String`     | `getName()`                          | 以 String 形式返回此 Method 对象表示的方法名称，即返回方法的名称 |
| `boolean`    | `isVarArgs()`                        | 判断方法是否带可变参数，如果将此方法声明为带有可变数量的参数，则返回 true；否则，返回 false。 |
| `String`     | `toGenericString()`                  | 返回描述此 Method 的字符串，包括类型参数。                   |

## 反射包中的Array类

在Java的java.lang.reflect包中存在着一个可以动态操作数组的类，Array，它提供了动态创建和访问 Java 数组的方法。Array 允许在执行 get 或 set 操作进行取值和赋值。在Class类中与数组关联的方法是：

| 方法返回值 | 方法名称             | 方法说明                                   |
| ---------- | -------------------- | ------------------------------------------ |
| `Class<?>` | `getComponentType()` | 返回表示数组元素类型的 Class，即数组的类型 |
| `boolean`  | `isArray()`          | 判定此 Class 对象是否表示一个数组类。      |

java.lang.reflect.Array中的常用静态方法如下：

| 方法返回值      | 方法名称                                                 | 方法说明                                       |
| --------------- | -------------------------------------------------------- | ---------------------------------------------- |
| `static Object` | `set(Object array, int index)`                           | 返回指定数组对象中索引组件的值。               |
| `static int`    | `getLength(Object array)`                                | 以 int 形式返回指定数组对象的长度              |
| `static object` | `newInstance(Class<?> componentType, int... dimensions)` | 创建一个具有指定类型和维度的新数组。           |
| `static Object` | `newInstance(Class<?> componentType, int length)`        | 创建一个具有指定的组件类型和长度的新数组。     |
| `static void`   | `set(Object array, int index, Object value)`             | 将指定数组对象中索引组件的值设置为指定的新值。 |

除了上述动态修改数组长度或者动态创建数组或动态获取值或设置值外，可以利用泛型动态创建泛型数组如下：

```java
/**
  * 接收一个泛型数组，然后创建一个长度与接收的数组长度一样的泛型数组，
  * 并把接收的数组的元素复制到新创建的数组中，
  * 最后找出新数组中的最小元素，并打印出来
  * @param a
  * @param <T>
  */
 public  <T extends Comparable<T>> void min(T[] a) {
     //通过反射创建相同类型的数组
     T[] b = (T[]) Array.newInstance(a.getClass().getComponentType(), a.length);
     for (int i = 0; i < a.length; i++) {
         b[i] = a[i];
     }
     T min = null;
     boolean flag = true;
     for (int i = 0; i < b.length; i++) {
         if (flag) {
             min = b[i];
             flag = false;
         }
         if (b[i].compareTo(min) < 0) {
             min = b[i];
         }
     }
     System.out.println(min);
 }
```

毕竟我们无法直接创建泛型数组，有了Array的动态创建数组的方式这个问题也就迎刃而解了。

```java
//无效语句，编译不通
T[] a = new T[];
```

# Stream API

### map

map只是一维的1：1的映射，只能对一维的进行匹配，若是两个及以上的List/数组等Stream对象，则map不能正确读取到对象的属性等

而flatmap会将list或数组等进行拆分成最小单元，多个Stream也进行扁平化处理，处理成最底层元素，进行匹配

通过Stream生成的流对象的private属性只能通过get方法去获取，stream对象是对象本身的一个封装

List等Implement Collection接口的会默认调用Collection的 default stream方法，调用StreamSupport生成实现了Stream接口的对象，对象中(默认为ReferencePipeline)会对Stream接口的方法进行实现，其中StreamSupport会将Collection对象注入Spliterator，再将Spliterator(默认为IteratorSpliterator)作为参数传给ReferencePipeline，

# Optional

`Optional`类，这是Java 8新增的一个类，用以解决程序中常见的`NullPointerException`异常问题。并不是忽略他，而是内部将我们传入的值（需要判断）绑定到Optional对象的value上，设置了很多filiter与map操作与函数式接口，在其中加入检查操作与设置返回类型，减少我们需要编写很多if-else的检查语句的时间，增加代码规范性，与Stream的API实现有些相似。 

想要知道value和函数式接口的实现如何交互，需要自己看源码

https://lw900925.github.io/java/java8-optional.html

## 常见API

| **方法**    | 描述                                                         |
| ----------- | ------------------------------------------------------------ |
| empty       | 返回一个空的 Optional 实例                                   |
| filter      | 如果值存在并且满足提供的谓词，就返回包含该值的 Optional 对象；否则返回一个空的Optional 对象 |
| flatMap     | 如果值存在，就对该值执行提供的 mapping 函数调用，返回一个 Optional 类型的值，否则就返回一个空的 Optional 对象 |
| get         | 如果该值存在，将该值用 Optional 封装返回，否则抛出一个 NoSuchElementException 异常 |
| ifPresent   | 如果值存在，就执行使用该值的方法调用，否则什么也不做         |
| isPresent   | 如果值存在就返回 true ，否则返回 false                       |
| map         | 如果值存在，就对该值执行提供的 mapping函数调用               |
| of          | 将指定值用 Optional 封装之后返回，如果该值为 null ，则抛出一个 NullPointerException异常 |
| ofNullable  | 将指定值用 Optional 封装之后返回，如果该值为 null ，则返回一个空的 Optional 对象 |
| orElse      | 如果有值则将其返回，否则返回一个默认值                       |
| orElseGet   | 如果有值则将其返回，否则返回一个由指定的 Supplier 接口生成的值 |
| orElseThrow | 如果有值则将其返回，否则抛出一个由指定的 Supplier 接口生成的异常 |

## 使用建议

1. 尽量避免在程序中直接调用`Optional`对象的`get()`和`isPresent()`方法；
2. 避免使用`Optional`类型声明实体类的属性；

第一条建议中直接调用`get()`方法是很危险的做法，如果`Optional`的值为空，那么毫无疑问会抛出`NullPointerException`异常，而**为了调用`get()`方法而使用`isPresent()`方法作为空值检查，这种做法与传统的用`if`语句块做空值检查没有任何区别。**

第二条建议避免使用`Optional`作为实体类的属性，它在设计的时候就没有考虑过用来作为类的属性，如果你查看`Optional`的源代码，你会发现它没有实现`java.io.Serializable`接口，这在某些情况下是很重要的（比如你的项目中使用了某些序列化框架），使用了`Optional`作为实体类的属性，意味着他们不能被序列化。

#### `orElse()`方法的使用

```
return str != null ? str : "Hello World"
```

上面的代码表示判断字符串`str`是否为空，不为空就返回，否则，返回一个常量。使用`Optional`类可以表示为：

```
return strOpt.orElse("Hello World")
```

#### 简化`if-else`

```java
User user = ...
if (user != null) {
    String userName = user.getUserName();
    if (userName != null) {
        return userName.toUpperCase();
    } else {
        return null;
    }
} else {
    return null;
}
```

上面的代码可以简化成：

```java
User user = ...
Optional<User> userOpt = Optional.ofNullable(user);

return userOpt.map(User::getUserName)
            .map(String::toUpperCase)
            .orElse(null);
```

# 值传递/引用传递/指针传递

值传递：
形参是实参的拷贝，改变形参的值并不会影响外部实参的值，值传递过程中，被调函数的形式参数作为被调函数的局部变量处理，即在栈中开辟了内存空间以存放由主调函数放进来的实参的值，从而成为了实参的一个副本。当函数内部需要修改参数，并且不希望这个改变影响调用者时，采用值传递。
指针传递：
形参为指向实参地址的指针（指针传递参数本质上是值传递的方式，它所传递的是一个地址值），当对形参的指针操作时，就相当于对实参本身进行的操作。
引用传递：
形参相当于是实参的“别名”，对形参的操作其实就是对实参的操作，而在引用传递过程中，被调函数的形式参数虽然也作为局部变量在栈中开辟了内存空间，但是这时存放的是由主调函数放进来的实参变量的地址（int &a的形式）。被调函数对形参的任何操作都被处理成间接寻址，即通过栈中存放的地址访问主调函数中的实参变量。正因为如此，被调函数对形参做的任何操作都影响了主调函数中的实参变量。

> 指针传递和引用传递的区别：
>
> 1. 无论你传值还是传指针，函数都会生成一个临时变量，指针指向一块内存，**它的内容是所指内存的地址**；而引用则是**某块内存的别名**。
>2. 当你传值时，只可以引用值而不可以改变值，但传值引用时，可以改变值，

# 多层嵌套json字符串

``` java
package jansonDemo;

import com.alibaba.fastjson.JSON;
import com.alibaba.fastjson.JSONArray;
import com.alibaba.fastjson.JSONObject;

public class TestJSON {
    /**
     * JSON实际上也是键值对（"key":"value"）
     * key 必须是字符串，value 可以是合法的 JSON 数据类型（字符串, 数字, 对象, 数组, 布尔值或 null）
     * value如果是字符串，用jsonobj.getString("key")获取
     * value如果是数  字，用jsonobj.getIntValue("key"),jsonobj.getFloatValue("key")，jsonobj.getInteger("key")等基本数据类型及其包装类的方法获取
     * value如果是布尔值，用jsonobj.getBoolean("key"),jsonobj.getBooleanValue("key")获取
     * value如果是数  组，用jsonobj.getJSONArray("key")获取
     * value如果是Object对象，用jsonobj.get("key")，获取
     * value如果是JSONObject对象，用jsonobj.getJSONObject("key")获取
     */

    /**
     * 该方法用于将已有的json字符串转换为json对象，并取出该对象中相应的key对应的value值
     * 将已有的字符串转换成jsonobject，用JSON.parseObject(jsonStr)方法
     * json中只要是{}就代表一个JSONObject,[]就代表一个JSONArray
     * 获取JSONObject对象用JSONObject jsonobject.getJSONObject("key")方法
     * 获取JSONArray对象用JSONObject jsonobject.getJSONArray("key")方法
     */

    private static void strWritedToJSONObject() {
        //以下是一个json对象中嵌套一个json子对象
        String myJsonObj = "{\n" +
                "    \"name\":\"runoob\",\n" +
                "    \"alexa\":10000,\n" +
                "    \"sites\": {\n" +
                "        \"site1\":\"www.runoob.com\",\n" +
                "        \"site2\":\"m.runoob.com\",\n" +
                "        \"site3\":\"c.runoob.com\"\n" +
                "    }\n" +
                "}";
        JSONObject jsonobj = JSON.parseObject(myJsonObj); //将json字符串转换成jsonObject对象
        /***获取JSONObject中每个key对应的value值时，可以根据实际场景中想得到什么类型就分别运用不到的方法***/
        System.out.println(jsonobj.get("name")); //取出name对应的value值，得到的是一个object
        System.out.println(jsonobj.getString("name")); //取出name对应的value值，得到的是一个String
        System.out.println(jsonobj.getIntValue("alexa")); //取出name对应的value值，得到的是一个int
        System.out.println(jsonobj.get("sites")); //取出sites对应的value值，得到的是一个object
        System.out.println(jsonobj.getString("sites"));
        System.out.println(jsonobj.getJSONObject("sites")); //取出sites对应的value值，得到一个JSONObject子对象
        System.out.println(jsonobj.getJSONObject("sites").getString("site2")); //取出嵌套的JSONObject子对象中site2对应的value值，必须用getJSONObject()先获取JSONObject


        /**
         * 以下是一个json对象中包含数组，数组中又包含json子对象和子数组
         */
        String myJsonObj2 = "{\n" +
                "    \"name\":\"网站\",\n" +
                "    \"num\":3,\n" +
                "    \"sites\": [\n" +
                "        { \"name\":\"Google\", \"info\":[ \"Android\", \"Google 搜索\", \"Google 翻译\" ] },\n" +
                "        { \"name\":\"Runoob\", \"info\":[ \"菜鸟教程\", \"菜鸟工具\", \"菜鸟微信\" ] },\n" +
                "        { \"name\":\"Taobao\", \"info\":[ \"淘宝\", \"网购\" ] }\n" +
                "    ]\n" +
                "}";
        JSONObject jsonobj2 = JSON.parseObject(myJsonObj2); //将json字符串转换成jsonObject对象
        System.out.println(jsonobj2.get("sites"));
        System.out.println(jsonobj2.getString("sites"));
        System.out.println(jsonobj2.getJSONArray("sites")); //取出sites对应的value值，得到一个JSONOArray对象
        //System.out.println(jsonobj2.getJSONObject("sites")); 不能用该方法，因为sites是一个JSONOArray对象
        //取出json对象中sites对应数组中第一个json子对象的值
        System.out.println(jsonobj2.getJSONArray("sites").getJSONObject(0)); //得到结果：{"name":"Google","info":["Android","Google 搜索","Google 翻译"]}
        System.out.println(jsonobj2.getJSONArray("sites").get(0));
        System.out.println(jsonobj2.getJSONArray("sites").getString(0));
        //取出json对象中sites对应数组中第一个json子对象下面info对应的嵌套子数组值
        System.out.println(jsonobj2.getJSONArray("sites").getJSONObject(0).getJSONArray("info")); //得到结果：["Android","Google 搜索","Google 翻译"]
        //取出json对象中sites对应数组中第一个json子对象下面info对应的嵌套子数组中第二个值
        System.out.println(jsonobj2.getJSONArray("sites").getJSONObject(0).getJSONArray("info").getString(1)); //得到结果：Google 搜索

        //依次取出json对象中sites对应数组中的值
        JSONArray array = jsonobj2.getJSONArray("sites");
        getJsonArrayItem(array);
        //依次取出json对象中sites对应数组中第二个json子对象下面info对应的嵌套子数组值
        JSONArray arr = jsonobj2.getJSONArray("sites").getJSONObject(1).getJSONArray("info");
        getJsonArrayItem(arr);
     }

    /**
     * 手动添加对象到一个JSONObject
     */
    private static void writeStrToJSONObject() {
        JSONObject jsonObject = new JSONObject();
        jsonObject.put("name","tom");
        jsonObject.put("age",20);

        JSONArray jsonArray = new JSONArray();
        JSONObject jsonArrayObject1 = new JSONObject();
        jsonArrayObject1.put("name","alibaba");
        jsonArrayObject1.put("info","www.alibaba.com");
        JSONObject jsonArrayObject2 = new JSONObject();
        jsonArrayObject2.put("name","baidu");
        jsonArrayObject2.put("info","www.baidu.com");

        jsonArray.add(jsonArrayObject1);
        jsonArray.add(jsonArrayObject2);

        jsonObject.put("sites",jsonArray);

        System.out.println(jsonObject);
     }

    /**
     * 将字符串转为JSONArray
     */
    private static void strToJsonArray() {
        String arrayStr = "[\n" +
                "        {\n" +
                "            \"name\":\"alibaba\",\n" +
                "            \"info\":\"www.alibaba.com\"\n" +
                "        },\n" +
                "        {\n" +
                "            \"name\":\"baidu\",\n" +
                "            \"info\":\"www.baidu.com\"\n" +
                "        }\n" +
                "    ]";

        JSONArray array = JSON.parseArray(arrayStr);
        System.out.println(array);
     }

    /**
     * 依次取出JSONArray中的值
     */
    private static void getJsonArrayItem(JSONArray array) {
        for (int i=0; i<array.size(); i++) {
            System.out.println(array.get(i));
        }
    }

     //测试类
    public static void main(String[] args) {
        strWritedToJSONObject();
        //writeStrToJSONObject();
        //strToJsonArray();
    }

}
```

