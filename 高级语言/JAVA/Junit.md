### Junit 概述

keeps bar green

keeps the code clean

#### 单元测试

* 所谓单元测试是测试应用程序的功能是否能够按需要正常运行，并且确保是在开发人员的水平上，单元测试生成图片。
* 单元测试是一个对单一实体（类或方法）的测试。单元测试是每个软件公司提高产品质量、满足客户需求的重要环节。

#### junit

* JUnit 是一个 Java 编程语言的单元测试框架。JUnit 在测试驱动的开发方面有很重要的发展，是起源于 JUnit 的一个统称为 xUnit 的单元测试框架之一。

* JUnit 促进了“先测试后编码”的理念，强调建立测试数据的一段代码，可以先测试，然后再应用。
* 这个方法就好比“测试一点，编码一点，测试一点，编码一点……”，增加了程序员的产量和程序的稳定性，可以减少程序员的压力和花费在排错上的时间。

* JUnit 是 java 编程语言理想的单元测试框架。

### Junit 的安装与使用

下载安装地址：http://www.junit.org/

（需要全部下载，单独下载junit-jupiter-api 不可使用）

####  Junit 5  VS 4

##### JUnit 5 的架构：

![JUnit 5 æ¶æç¤ºæå¾ã](https://www.ibm.com/developerworks/cn/java/j-introducing-junit5-part1-jupiter-api/Figure-1.png)

> 只要一个框架实现了 `TestEngine` 接口，就可以将它插入任何支持 `junit-platform-engine` 和 `junit-platform-launcher` API 的工具中！

##### JUnit 5 包关系图:

![JUnit 5 åç¤ºæå¾ã](https://www.ibm.com/developerworks/cn/java/j-introducing-junit5-part1-jupiter-api/Figure-2.png)



##### 注释

| 特征                             | JUNIT 4        | JUNIT 5        |
| :------------------------------- | :------------- | :------------- |
| 声明一种测试方法                 | `@Test`        | `@Test`        |
| 在当前类中的所有测试方法之前执行 | `@BeforeClass` | `@BeforeAll`   |
| 在当前类中的所有测试方法之后执行 | `@AfterClass`  | `@AfterAll`    |
| 在每个测试方法之前执行           | `@Before`      | `@BeforeEach`  |
| 每种测试方法后执行               | `@After`       | `@AfterEach`   |
| 禁用测试方法/类                  | `@Ignore`      | `@Disabled`    |
| 测试工厂进行动态测试             | NA             | `@TestFactory` |
| 嵌套测试                         | NA             | `@Nested`      |
| 标记和过滤                       | `@Category`    | `@Tag`         |
| 注册自定义扩展                   | NA             | `@ExtendWith`  |

##### 断言

| 断言方法                         | 说明                                                |
| :------------------------------- | :-------------------------------------------------- |
| `assertEquals(expected, actual)` | 如果 *expected* 不等于 *actual*，则断言失败。       |
| `assertFalse(booleanExpression)` | 如果 *booleanExpression* 不是 `false`，则断言失败。 |
| `assertNull(actual)`             | 如果 *actual* 不是 `null`，则断言失败。             |
| `assertNotNull(actual)`          | 如果 *actual* 是 `null`，则断言失败。               |
| `assertTrue(booleanExpression)`  | 如果 *booleanExpression* 不是 `true`，则断言失败。  |

在Junit 4中，[org.junit.Assert](http://junit.org/junit4/javadoc/4.12/org/junit/Assert.html)具有所有断言方法来验证预期结果和结果。
它们接受错误消息的额外参数作为方法签名中的FIRST参数。

在JUnit 5中，[org.junit.jupiter.Assertions](http://junit.org/junit5/docs/current/api/org/junit/jupiter/api/Assertions.html)包含大多数断言方法，包括附加`assertThrows()`和`assertAll()`方法。`assertAll()`到目前为止处于实验状态，并用于分组断言。
```
public static void assertEquals(long expected, long actual)

public static void assertEquals(long expected, long actual, String message)

public static void assertEquals(long expected, long actual, Supplier messageSupplier)
```

##### assumptions

在Junit 4中，[org.junit.Assume](http://junit.org/junit4/javadoc/4.12/org/junit/Assume.html)包含用于说明测试有意义的条件的假设的方法。它有以下五种方法：

1. assumeFalse（）
2. assumeNoException（）
3. assumeNotNull（）
4. 假使，假设（）
5. assumeTrue（）

在Junit 5中，[org.junit.jupiter.api.Assumptions](http://junit.org/junit5/docs/current/api/org/junit/jupiter/api/Assumptions.html)包含用于说明测试有意义的条件的假设的方法。它有以下三种方法：

1. assumeFalse（）
2. 假使，假设（）
3. assumeTrue（）

##### 嵌套单元测试
```
在JUnit 4，@RunWith和@Suite注释。例如

import org.junit.runner.RunWith;

import org.junit.runners.Suite;

 

@RunWith(Suite.class)

@Suite.SuiteClasses({

        ExceptionTest.class,

        TimeoutTest.class

})

public class JUnit4Example

{

}

在JUnit 5 @RunWith，@SelectPackages和@SelectClasses如

import org.junit.platform.runner.JUnitPlatform;

import org.junit.platform.suite.api.SelectPackages;

import org.junit.runner.RunWith;

@RunWith(JUnitPlatform.class)

@SelectPackages("com.howtodoinjava.junit5.examples")

public class JUnit5Example

{

}
```

##### 第三方整合

在Junit 4中，没有对第三方插件和IDE的集成支持。他们必须依靠反射。

JUnit 5为此专门设立了子项目，即JUnitPlatform。它定义了`TestEngine`用于开发在平台上运行的测试框架的API。

#### 测试框架

JUnit 测试框架能够轻松完成以下任意两种结合：

- Eclipse 集成开发环境
- Ant 打包工具
- Maven 项目构建管理

##### 特性

JUnit 测试框架具有以下重要特性：

- 测试工具
- 测试套件
- 测试运行器
- 测试分类

##### 测试工具

* 是一整套固定的工具用于基线测试。

目的：

* 为了确保测试能够在共享且固定的环境中运行，因此保证测试结果的可重复性它包括：
  - 在所有测试调用指令发起前的 setUp() 方法。
  - 在测试方法运行后的 tearDown() 方法。

##### 测试套件

* 意味着捆绑几个测试案例同时运行。

* 在JUnit 5 @RunWith，@SelectPackages和@SelectClasses

##### 测试运行器

* 用于执行测试案例。以下为假定测试分类成立的情况下的例子：
```
import org.junit.runner.JUnitCore;
import org.junit.runner.Result;
import org.junit.runner.notification.Failure;

public class TestRunner {
   public static void main(String[] args) {
      Result result = JUnitCore.runClasses(TestJunit.class);
      for (Failure failure : result.getFailures()) {
         System.out.println(failure.toString());
      }
      System.out.println(result.wasSuccessful());
   }
}
```

#####  Junit测试分类

**测试分类**是在编写和测试 JUnit 的重要分类。几种重要的分类如下：

- 包含一套断言方法的测试断言
- 包含规定运行多重测试工具的测试用例
- 包含收集执行测试用例结果的方法的测试结果

### Junit 5 API 

#### Package org.junit.jupiter.api

| Class                                                        | Description                                                  |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| [Assertions](https://junit.org/junit5/docs/current/api/org.junit.jupiter.api/org/junit/jupiter/api/Assertions.html) | `Assertions` is a collection of utility methods that support asserting conditions in tests. |
| [Assumptions](https://junit.org/junit5/docs/current/api/org.junit.jupiter.api/org/junit/jupiter/api/Assumptions.html) | `Assumptions` is a collection of utility methods that support conditional test execution based on *assumptions*. |
| [DisplayNameGenerator.ReplaceUnderscores](https://junit.org/junit5/docs/current/api/org.junit.jupiter.api/org/junit/jupiter/api/DisplayNameGenerator.ReplaceUnderscores.html) | `DisplayNameGenerator` that replaces underscores with spaces. |
| [DisplayNameGenerator.Standard](https://junit.org/junit5/docs/current/api/org.junit.jupiter.api/org/junit/jupiter/api/DisplayNameGenerator.Standard.html) | Standard `DisplayNameGenerator`.                             |
| [DynamicContainer](https://junit.org/junit5/docs/current/api/org.junit.jupiter.api/org/junit/jupiter/api/DynamicContainer.html) | A `DynamicContainer` is a container generated at runtime.    |
| [DynamicNode](https://junit.org/junit5/docs/current/api/org.junit.jupiter.api/org/junit/jupiter/api/DynamicNode.html) | `DynamicNode` serves as the abstract base class for a container or a test case generated at runtime. |
| [DynamicTest](https://junit.org/junit5/docs/current/api/org.junit.jupiter.api/org/junit/jupiter/api/DynamicTest.html) | A `DynamicTest` is a test case generated at runtime.         |
| [MethodOrderer.Alphanumeric](https://junit.org/junit5/docs/current/api/org.junit.jupiter.api/org/junit/jupiter/api/MethodOrderer.Alphanumeric.html) | `MethodOrderer` that sorts methods alphanumerically based on their names using [`String.compareTo(String)`](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/lang/String.html?is-external=true#compareTo(java.lang.String)). |
| [MethodOrderer.OrderAnnotation](https://junit.org/junit5/docs/current/api/org.junit.jupiter.api/org/junit/jupiter/api/MethodOrderer.OrderAnnotation.html) | `MethodOrderer` that sorts methods based on the [`@Order`](https://junit.org/junit5/docs/current/api/org.junit.jupiter.api/org/junit/jupiter/api/Order.html) annotation. |
| [MethodOrderer.Random](https://junit.org/junit5/docs/current/api/org.junit.jupiter.api/org/junit/jupiter/api/MethodOrderer.Random.html) | `MethodOrderer` that orders methods pseudo-randomly.         |

#### Package org.junit.jupiter.api.assertions
https://junit.org/junit5/docs/current/api/org.junit.jupiter.api/org/junit/jupiter/api/Assertions.html

| Modifier and Type | Method                                              | Description                                                  |
| :---------------- | :-------------------------------------------------- | :----------------------------------------------------------- |
| `static void`     | `assertAll(String heading, Collection executables)` | *Assert* that *all* supplied `executables` do not throw exceptions. |
| `static void` | `assertArrayEquals(boolean[] expected, boolean[] actual)`    | *Assert* that `expected` and `actual` boolean arrays are equal. |
| `static void` | `assertArrayEquals(boolean[] expected, boolean[] actual, String message)` | *Assert* that `expected` and `actual` boolean arrays are equal. |

`Assertions` 是支持测试中声明条件的实用程序方法的集合。

除非另有说明，否则*失败的*断言将抛出其 [`AssertionFailedError`](https://ota4j-team.github.io/opentest4j/docs/1.2.0/api/org/opentest4j/AssertionFailedError.html?is-external=true)子类或子类。

### 基本的测试方式(Junit 4)

#### 使用断言

所有的断言都包含在 Assert 类中

```
public class Assert extends java.lang.Object
```

这个类提供了很多有用的断言方法来编写测试用例。只有失败的断言才会被记录。

#### 执行过程

```
import org.junit.runner.JUnitCore;
import org.junit.runner.Result;
import org.junit.runner.notification.Failure;

public class TestRunner {
   public static void main(String[] args) {
      Result result = JUnitCore.runClasses(ExecutionProcedureJunit.class);
      for (Failure failure : result.getFailures()) {
         System.out.println(failure.toString());
      }
      System.out.println(result.wasSuccessful());
   }
} 
```

```
runClasses
public static Result runClasses(java.lang.Class<?>... classes)
Run the tests contained in classes. Write feedback while the tests are running and write stack traces for all failed tests after all tests complete. This is similar to main(String[]), but intended to be used programmatically.
Parameters:
classes - Classes in which to find tests
Returns:
a Result describing the details of the test run and the failed tests.

```



- beforeClass() 方法首先执行，并且只执行一次。
- afterClass() 方法最后执行，并且只执行一次。
- before() 方法针对每一个测试用例执行，但是是在执行测试用例之前。
- after() 方法针对每一个测试用例执行，但是是在执行测试用例之后。
- 在 before() 方法和 after() 方法之间，执行每一个测试用例。

#### 执行测试

测试用例是使用 JUnitCore 类来执行的。JUnitCore 是运行测试的外观类。它支持运行 JUnit 4 测试, JUnit 3.8.x 测试,或者他们的混合。 要从命令行运行测试，可以运行 java org.junit.runner.JUnitCore 。对于只有一次的测试运行，可以使用静态方法 runClasses(Class[])。

下面是 org.junit.runner.JUnitCore 类的声明：

```
public class JUnitCore extends java.lang.Object
```

#### 套件测试

**测试套件**意味着捆绑几个单元测试用例并且一起执行他们。在 JUnit 中，**@RunWith** 和 **@Suite** 注释用来运行套件测试。

```
Using Suite as a runner allows you to manually build a suite containing tests from many classes. It is the JUnit 4 equivalent of the JUnit 3.8.x static Test suite() method. To use it, annotate a class with @RunWith(Suite.class) and @SuiteClasses(TestClass1.class, ...). When you run this class, it will run all the tests in all the suite classes.
```



#### 忽略测试

有时可能会发生我们的代码还没有准备好的情况，这时测试用例去测试这个方法或代码的时候会造成失败。**@Ignore** 注释会在这种情况时帮助我们。

- 一个含有 @Ignore 注释的测试方法将不会被执行。
- 如果一个测试类有 @Ignore 注释，则它的测试方法将不会执行。

#### 时间测试

Junit 提供了一个暂停的方便选项。如果一个测试用例比起指定的毫秒数花费了更多的时间，那么 Junit 将自动将它标记为失败。**timeout** 参数和 @Test 注释一起使用。

#### 异常测试

Junit 用代码处理提供了一个追踪异常的选项。你可以测试代码是否它抛出了想要得到的异常。**expected** 参数和 @Test 注释一起使用。

#### 参数化测试

| `static interface` | [Parameterized.Parameters](http://junit.sourceforge.net/javadoc/org/junit/runners/Parameterized.Parameters.html)**`      Annotation for a method which provides parameters to be injected into the test class constructor by `Parameterized` |
| ------------------ | ------------------------------------------------------------ |
|                    |                                                              |

Junit 4 引入了一个新的功能**参数化测试**。参数化测试允许开发人员使用不同的值反复运行同一个测试。你将遵循 5 个步骤来创建**参数化测试**。

- 用 @RunWith(Parameterized.class) 来注释 test 类。
- 创建一个由 @Parameters 注释的公共的静态方法，它返回一个对象的集合(数组)来作为测试数据集合。
- 创建一个公共的构造函数，它接受和一行测试数据相等同的东西。
- 为每一列测试数据创建一个实例变量。
- 用实例变量作为测试数据的来源来创建你的测试用例。