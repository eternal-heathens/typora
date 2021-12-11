https://doc.codingdict.com/gradle-user-guide/the_gradle_daemon/

# gradle目录结构：

1. bin：gradle启动脚本
2. lib：依赖使用的jar包

user目录下的.gradle:

1. daemon:服务启动脚本
2. init.d:全局配置
3. wrapper：使用当前项目的./gradlew wrapper 时下载的安装包会放在此目录下

GradleUserHome：gradle项目depandency下载的jar包



# gradle服务运行方式

gradle3.0之后，gradle不同于以往与meavn一样的基于jvm 的进程启动，并在任务完成后销毁，而是基于一个Client和daemon 的两个进程间的交互，其中client非常的轻，只是一个传递命令参数的作用，daemon只要不销毁便一直热启动（默认3小时再销毁），减少了新的项目的启动时间。

* 要主要client的daemon各自版本/内存参数等的需求，若是client与damen不兼容，client 会新启动一个daemon。
* 若是不想要daemon的形式，则需要启动时使用- - no daemon 的参数
* 3.0之前因为不稳定所以CI默认使用 - - no daemon，4.0 默认使用daemon

# 什么时候不使用Gradle守护进程

建议在开发环境中使用Gradle的守护进程,*不建议*在持续集成环境和构建服务器环境中使用守护进程.

守护进程可以更快的构建,这对于一个正坐在椅子前构建项目的人来说非常重要.对于CI构建来说,稳定性和可预见性是最重要的.为每个构建运行时用一个新的,完全孤立于以前的版本的程序,更加可靠。

# 闭包

闭包：首先需要语言允许使用内部函数－－即函数定义和函数[表达式](https://baike.baidu.com/item/表达式)位于另一个函数的函数体内。而且，这些内部函数可以访问它们所在的外部函数中声明的所有局部变量、参数和声明的其他内部函数。

其次，当其中一个这样的内部函数在包含它们的外部函数之外被调用时，就会形成闭包。

* 也就是说，内部函数会在外部函数返回后被执行。而当这个内部函数执行时，它仍然必需访问其外部函数的局部变量、参数以及其他内部函数。这些局部变量、参数和函数声明（最初时）的值是外部函数返回时的值，但也会受到内部函数的影响。这就形成了一个外调用内函数，内函数调用外数据的闭包
* 最常见的如java 的lambda表达式，而groove的语法糖可以实现lambda表达式的相同功效



# gradle配置

* gradle是100%兼容java的，因此可以在其中编写java或其他的逻辑

* idea项目启动时需要在gradle配置中选择jdk版本	  
* ext https://www.jianshu.com/p/d6662135751e

# gradle 命令

## gradlew

It is recommended to always execute a build with the Wrapper to ensure a reliable, controlled and standardized execution of the build. Using the Wrapper looks almost exactly like running the build with a Gradle installation. Depending on the operating system you either run `gradlew` or `gradlew.bat` instead of the `gradle` command. The following console output demonstrate the use of the Wrapper on a Windows machine for a Java-based project.

* 如果可以，使用graldew/gradlew.bat构建项目可以保证构建的可靠性和稳定性等



# gradle 重要方法

一般方法都是以闭包的形式使用

## task

* gradle操作的最小单元，同时也是project的方法（其中，基本所有没有特殊标明的方法一般都属于project）

## dependsOn

* task的方法，表明此任务的调用需要依赖与dependsOn（XXX）的XXX任务，需要先构建XXX任务

## doLast

* 在task构建的最后执行

## AfterEvaluate

* 钩子函数，在Lifecycle的Configuration阶段build完成后执行（也就是在build.gradle完成对project的构建后执行）

# groove

MOP(MetaObject Protocol)

MOP：元对象协议。由 Groovy 语言中的一种协议。该协议的出现为元编程提供了优雅的解决方案。而 MOP 机制的核心就是 MetaClass。
 元编程：编写能够操作程序的程序，也包括操作程序自身。

正是 Groovy 提供了 MOP 的机制，才使得 Groovy 对象更加灵活，我们可以根据对象的 metaClass，动态的查询对象的方法和属性。这里所属的动态指的是在运行时，根据所提供的方法或者属性的字符串，即可得到
 有点类似于 Java 中的反射，但是在使用上却比 Java 中的反射简单的多。



# Build Lifecycle

A Gradle build has three distinct phases.

- Initialization

  Gradle supports single and multi-project builds. During the initialization phase, Gradle determines which projects are going to take part in the build, and creates a [Project](https://docs.gradle.org/current/dsl/org.gradle.api.Project.html) instance for each of these projects.

- Configuration

  During this phase the project objects are configured. The build scripts of *all* projects which are part of the build are executed.

  * gradle在Configuration阶段会先从上到下进行构建，若是task中有println方法等（不是在doFirst/Last或者如afterEvaluate的钩子中），会按从上到下直接执行，其中还会执行一些Configuration的钩子函数如afterEvaluate

- Execution

  Gradle determines the subset of the tasks, created and configured during the configuration phase, to be executed. The subset is determined by the task name arguments passed to the `gradle` command and the current directory. Gradle then executes each of the selected tasks.

### Lifecycle

There is a one-to-one relationship between a `Project` and a `build.gradle` file. During build initialisation, Gradle assembles a `Project` object for each project which is to participate in the build, as follows:

- Create a [`Settings`]instance for the build.

- Evaluate the `settings.gradle` script, if present, against the [`Settings`]object to configure it.

- Use the configured [`Settings`]object to create the hierarchy of `Project` instances.

- Finally, evaluate each `Project` by executing its `build.gradle` file, if present, against the project. The projects are evaluated in breadth-wise order, such that a project is evaluated before its child projects. This order can be overridden by calling `Project.evaluationDependsOnChildren()` or by adding an explicit evaluation dependency using `Project.evaluationDependsOn(java.lang.String)`.

  

# gradle插件和项目依赖

给gradle导入jar包到gardle daemon的classpath：

```groovy
// 若是gradle默认配置的包，通过project类的apply方法导入gardle使用的jar包
apply plugin: org.gradle.api.plugins.JavaPlugin

//Gradle默认就导入了org.gradle.api.plugins包，因此我们也可以去掉包名：

apply plugin: JavaPlugin

//又因为实现了org.gradle.api.plugins接口的插件会有pulginid，使用pulginid是最简洁、最常用的方式：

apply plugin: 'java'

//若是第三方的对象插件，需要使用buildscrip来设置。
// gradle2.0之前
build {

	repositories {
		maven {
			url "https://plugins.gradle.org/m2/"
		}
	}

	dependencies {
		classpath "com.jfrog.bintray.gradle:gradle-bintray-plugin:1.8.4"
	}

}

apply plugin: "com.jfrog.bintray"
// gradle 2.0之后
plugins{
	id 'jar-keyname' 'version'
}

```



给java项目导入jar包到classpath

```groovy
// 可以到maven参考看gradle的key值拉取
dependancies{
	api 'xxx'
    //或者是
	implementation'xxx'
}
```

Gradle插件可以做什么呢？主要有以下的几点

- 为项目配置依赖。
- 为项目配置约定，比如约定源代码的存放位置。
- 为项目添加任务，完成测试、编译、打包等任务。
- 为项目中的核心对象和其他插件的对象添加拓展类型。

使用Gradle插件主要有以下的好处：

- 重用和减少维护在多个项目类似的逻辑的开销。
- 更高程度的模块化。
- 封装必要的逻辑，并允许构建脚本尽可能是声明性地。

# buildSrc

The directory `buildSrc` is treated as an [included build](https://docs.gradle.org/current/userguide/composite_builds.html#composite_build_intro). Upon discovery of the directory, Gradle automatically compiles and tests this code and puts it in the classpath of your build script. For multi-project builds there can be only one `buildSrc` directory, which has to sit in the root project directory. `buildSrc` should be preferred over [script plugins](https://docs.gradle.org/current/userguide/plugins.html#sec:script_plugins) as it is easier to maintain, refactor and test the code.

