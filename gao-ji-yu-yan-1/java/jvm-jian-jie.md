#  JVM

Java 虚拟机 Java 虚拟机（Java virtual machine，JVM）是运行 Java 程序必不可少的机制。JVM实现了Java语言最重要的特征：即平台无关性。原理：编译后的 Java 程序指令并不直接在硬件系统的 CPU 上执行，而是由 JVM 执行。JVM屏蔽了与具体平台相关的信息，使Java语言编译程序只需要生成在JVM上运行的目标字节码（.class）,就可以在多种平台上不加修改地运行。Java 虚拟机在执行字节码时，把字节码解释成具体平台上的机器指令执行。因此实现java平台无关性。它是 Java 程序能在多平台间进行无缝移植的可靠保证，同时也是 Java 程序的安全检验引擎（还进行安全检查）。

JVM 是 编译后的 Java 程序（.class文件）和硬件系统之间的接口 （ 编译后：javac 是收录于 JDK 中的 Java 语言编译器。该工具可以将后缀名为. java 的源文件编译为后缀名为. class 的可以运行于 Java 虚拟机的字节码。）

------

JVM architecture:

![img](https://image-static.segmentfault.com/363/197/3631975667-54f7c08e5bf91_articlex)



JVM = 类加载器 classloader + 执行引擎 execution engine + 运行时数据区域 runtime data area
classloader 把硬盘上的class 文件加载到JVM中的运行时数据区域, 但是它不负责这个类文件能否执行，而这个是 执行引擎 负责的。

------

## classloader

作用：装载.class文件
classloader 有两种装载class的方式 （时机）：

1. 隐式：运行过程中，碰到new方式生成对象时，隐式调用classLoader到JVM
2. 显式：通过class.forname()动态加载

**双亲委派模型（Parent Delegation Model）：**

类的加载过程采用双亲委托机制，这种机制能更好的保证 Java 平台的安全。
该模型要求除了顶层的Bootstrap class loader启动类加载器外，其余的类加载器都应当有自己的`父类加载器`。子类加载器和父类加载器`不是以继承（Inheritance）的关系`来实现，而是通过`组合（Composition）关系`来复用父加载器的代码。每个类加载器都有自己的命名空间（由该加载器及所有父类加载器所加载的类组成，在同一个命名空间中，不会出现类的完整名字（包括类的包名）相同的两个类；在不同的命名空间中，有可能会出现类的完整名字（包括类的包名）相同的两个类）

双亲委派模型的**工作过程为：**

1. 当前 ClassLoader 首先从自己已经加载的类中查询是否此类已经加载，如果已经加载则直接返回原来已经加载的类。

```
每个类加载器都有自己的加载缓存，当一个类被加载了以后就会放入缓存，
等下次加载的时候就可以直接返回了。
```

2. 当前 classLoader 的缓存中没有找到被加载的类的时候，委托父类加载器去加载，父类加载器采用同样的策略，首先查看自己的缓存，然后委托父类的父类去加载，一直到 bootstrap ClassLoader.

3. 当所有的父类加载器都没有加载的时候，再由当前的类加载器加载，并将其放入它自己的缓存中，以便下次有加载请求的时候直接返回。

**使用这种模型来组织类加载器之间的关系的好处:
主要是为了`安全性`，避免用户自己编写的类动态替换 Java 的一些核心类，比如 String，同时也避免了`重复加载`，因为 JVM 中区分不同类，不仅仅是根据类名，相同的 class 文件被不同的 ClassLoader 加载就是不同的两个类，如果相互转型的话会抛java.lang.ClassCaseException.**

类加载器 classloader 是具有层次结构的，也就是父子关系。其中，Bootstrap 是所有类加载器的父亲。如下图所示：![img](http://segmentfault.com/img/bVk0W2)
**Bootstrap class loader： 父类
当运行 java 虚拟机时，这个类加载器被创建，它负责加载虚拟机的核心类库，如 java.lang.\* 等。例如 java.lang.Object 就是由根类加载器加载的。需要注意的是，这个类加载器不是用 java 语言写的，而是用 C/C++ 写的。
Extension class loader:
这个加载器加载出了基本 API 之外的一些拓展类。
AppClass Loader:
加载应用程序和程序员自定义的类。**

除了以上虚拟机自带的加载器以外，用户还可以定制自己的类加载器（**User-defined Class Loader）。Java 提供了抽象类 java.lang.ClassLoader，所有用户自定义的类加载器应该继承 ClassLoader 类。**

这是JVM分工自治生态系统的一个很好的体现。

http://www.importnew.com/6581.html

**一.类加载或类初始化**：当程序主动使用某个类时，如果该类还未被加载到内存中，则JVM会通过加载、连接、初始化3个步骤来对该类进行初始化。如果没有意外，JVM将会连续完成3个步骤。

**二.类加载时机:** 

 1.创建类的实例，也就是new一个对象

2.访问某个类或接口的静态变量，或者对该静态变量赋值

3.调用类的静态方法

***4.反射（Class.forName("com.lyj.load")）***

****5.初始化一个类的子类（会首先初始化子类的父类）****

****6.JVM启动时标明的启动类，即文件名和类名相同的那个类  

****

 

 ![img](https://img2018.cnblogs.com/blog/1715922/201907/1715922-20190722091009820-814119375.png)

**三.类加载详细过程：**

1.加载：

   加载指的是将类的class文件读入jvm的方法区，并创建一个java.lang.class对象，表示这个类的运行时元数据（相当于Java层的Class对象）。

  1）方法区类的class文件详解：

   *classLoader是如何加载class文件和存储文件信息的：*

​	当一个classLoder启动的时候，classLoader的生存地点在jvm中的堆，然后它会去主机硬盘上将A.class装载到jvm的方法区，方法区中的这个字节文件会被虚拟机拿来new A字节码()，然后在堆内存生成了一个A字节码的对象，然后**A字节码这个内存文件有两个引用一个指向A的class对象，一个指向加载自己的classLoader。那么方法区中的字节码内存块，除了记录一个class自己的class对象引用和一个加载自己的ClassLoader引用之外，还记录了什么信息呢？？见下图：**

 

 **![img](https://img2018.cnblogs.com/blog/1715922/201907/1715922-20190722092115244-1534165739.png)**

 

   **执行new A()的时候，JVM 做了什么工作：**

​      

　　　　首先，如果这个类没有被加载过，JVM就会进行类的加载，并在JVM内部创建一个instanceKlass对象表示这个类的运行时元数据（相当于Java层的Class对象）。初始化对象的时候（执行invokespecial A::），JVM就会创建一个instanceOopDesc对象表示这个对象的实例，然后进行Mark Word的填充，将元数据指针指向Klass对象，并填充实例变量。

​       元数据—— instanceKlass 对象会存在元空间（方法区），

​       对象实例—— instanceOopDesc 会存在Java堆。Java虚拟机栈中会存有这个对象实例的引用。



 

   2）堆中实例化对象的结构：

​      在hotSpot虚拟机中，对象在内存中存储的布局可以分为3块区域：对象头（Header）、实例数据（Instance Data）和对齐填充（Padding）。下图是普通对象实例与数组对象实例的数据结构：

 ![img](https://img2018.cnblogs.com/blog/1715922/201907/1715922-20190722093621024-1696054548.png)

 对象头
　　HotSpot虚拟机的对象头包括两部分信息：

markword
　　第一部分markword,用于存储对象自身的运行时数据，如哈希码（HashCode）、GC分代年龄、锁状态标志、线程持有的锁、偏向线程ID、偏向时间戳等，这部分数据的长度在32位和64位的虚拟机（未开启压缩指针）中分别为32bit和64bit，官方称它为“MarkWord”。

klass
　　对象头的另外一部分是klass类型指针，即对象指向它的类元数据的指针，虚拟机通过这个指针来确定这个对象是哪个类的实例.
数组长度（只有数组对象有）
如果对象是一个数组, 那在对象头中还必须有一块数据用于记录数组长度.
　

实例数据
　　实例数据部分是对象真正存储的有效信息，也是在程序代码中所定义的各种类型的字段内容。无论是从父类继承下来的，还是在子类中定义的，都需要记录起来。

 

对齐填充
　　第三部分对齐填充并不是必然存在的，也没有特别的含义，它仅仅起着占位符的作用。由于HotSpot VM的自动内存管理系统要求对象起始地址必须是8字节的整数倍，换句话说，就是对象的大小必须是8字节的整数倍。而对象头部分正好是8字节的倍数（1倍或者2倍），因此，当对象实例数据部分没有对齐时，就需要通过对齐填充来补全。



 

 

 

 

**2.链接**

   **1）验证：验证阶段用于检验被加载的类是否有正确的内部结构，并和其他类协调一致。**

   2）准备：类准备阶段负责为类的静态变量分配内存，并设置默认初始值。

　 3）解析：将类的二进制数据中的符号引用替换成直接引用。

**3.初始化：初始化是为类的静态变量赋予正确的初始值。**

​    如果类中有语句：private static int a = 10，它的执行过程是这样的，首先字节码文件被加载到内存后，先进行链接的验证这一步骤，验证通过后准备阶段，给a分配内存，因为变量a是static的，所以此时a等于int类型的默认初始值0，即a=0,然后到解析（后面在说），到初始化这一步骤时，才把a的真正的值10赋给a,此时a=10。



**三、类加载器**


  类加载器负责加载所有的类，其为所有被载入内存中的类生成一个java.lang.Class实例对象。一旦一个类被加载如JVM中，同一个类就不会被再次载入了。正如一个对象有一个唯一的标识一样，一个载入JVM的类也有一个唯一的标识。在Java中，一个类用其全限定类名（包括包名和类名）作为标识；但在JVM中，一个类用其全限定类名和其类加载器作为其唯一标识。例如，如果在pg的包中有一个名为Person的类，被类加载器ClassLoader的实例kl负责加载，则该Person类对应的Class对象在JVM中表示为(Person.pg.kl)。这意味着两个类加载器加载的同名类：（Person.pg.kl）和（Person.pg.kl2）是不同的、它们所加载的类也是完全不同、互不兼容的。

  JVM预定义有三种类加载器，当一个 JVM启动的时候，Java开始使用如下三种类加载器：

 1)根类加载器（bootstrap class loader）:它用来加载 Java 的核心类，是用原生代码来实现的，并不继承自 java.lang.ClassLoader（负责加载$JAVA_HOME中jre/lib/rt.jar里所有的class，由C++实现，不是ClassLoader子类）。由于引导类加载器涉及到虚拟机本地实现细节，开发者无法直接获取到启动类加载器的引用，所以不允许直接通过引用进行操作。

 2)扩展类加载器（extensions class loader）：它负责加载JRE的扩展目录，lib/ext或者由java.ext.dirs系统属性指定的目录中的JAR包的类。由Java语言实现，父类加载器为null。

 3)系统类加载器（system class loader）：被称为系统（也称为应用）类加载器，它负责在JVM启动时加载来自Java命令的-classpath选项、java.class.path系统属性，或者CLASSPATH换将变量所指定的JAR包和类路径。程序可以通过ClassLoader的静态方法getSystemClassLoader()来获取系统类加载器。如果没有特别指定，则用户自定义的类加载器都以此类加载器作为父加载器。由Java语言实现，父类加载器为ExtClassLoader。

**类加载器加载Class大致要经过如下8个步骤：**

1.检测此Class是否载入过，即在缓冲区中是否有此Class，如果有直接进入第8步，否则进入第2步。
2.如果没有父类加载器，则要么Parent是根类加载器，要么本身就是根类加载器，则跳到第4步，如果父类加载器存在，则进入第3步。
3.请求使用父类加载器去载入目标类，如果载入成功则跳至第8步，否则接着执行第5步。
4.请求使用根类加载器去载入目标类，如果载入成功则跳至第8步，否则跳至第7步。
5.当前类加载器尝试寻找Class文件，如果找到则执行第6步，如果找不到则执行第7步。
6.从文件中载入Class，成功后跳至第8步。
7.抛出ClassNotFountException异常。
8.返回对应的java.lang.Class对象。

 

**四、类加载机制：**

 

1.JVM的类加载机制主要有如下3种。

全盘负责：所谓全盘负责，就是当一个类加载器负责加载某个Class时，该Class所依赖和引用其他Class也将由该类加载器负责载入，除非显示使用另外一个类加载器来载入。
双亲委派：所谓的双亲委派，则是先让父类加载器试图加载该Class，只有在父类加载器无法加载该类时才尝试从自己的类路径中加载该类。通俗的讲，就是某个特定的类加载器在接到加载类的请求时，首先将加载任务委托给父加载器，依次递归，如果父加载器可以完成类加载任务，就成功返回；只有父加载器无法完成此加载任务时，才自己去加载。
缓存机制。缓存机制将会保证所有加载过的Class都会被缓存，当程序中需要使用某个Class时，类加载器先从缓存区中搜寻该Class，只有当缓存区中不存在该Class对象时，系统才会读取该类对应的二进制数据，并将其转换成Class对象，存入缓冲区中。这就是为很么修改了Class后，必须重新启动JVM，程序所做的修改才会生效的原因。

 

2.这里说明一下双亲委派机制：

​    双亲委派机制，其工作原理的是，如果一个类加载器收到了类加载请求，它并不会自己先去加载，而是把这个请求委托给父类的加载器去执行，如果父类加载器还存在其父类加载器，则进一步向上委托，依次递归，请求最终将到达顶层的启动类加载器，如果父类加载器可以完成类加载任务，就成功返回，倘若父类加载器无法完成此加载任务，子加载器才会尝试自己去加载，这就是双亲委派模式，即每个儿子都很懒，每次有活就丢给父亲去干，直到父亲说这件事我也干不了时，儿子自己才想办法去完成。

   双亲委派机制的优势：采用双亲委派模式的是好处是Java类随着它的类加载器一起具备了一种带有优先级的层次关系，通过这种层级关可以避免类的重复加载，当父亲已经加载了该类时，就没有必要子ClassLoader再加载一次。其次是考虑到安全因素，java核心api中定义类型不会被随意替换，假设通过网络传递一个名为java.lang.Integer的类，通过双亲委托模式传递到启动类加载器，而启动类加载器在核心Java API发现这个名字的类，发现该类已被加载，并不会重新加载网络传递的过来的java.lang.Integer，而直接返回已加载过的Integer.class，这样便可以防止核心API库被随意篡改。



------

## 执行引擎

作用： 执行字节码，或者执行本地方法

## runtime data area

JVM 运行时数据区 (JVM Runtime Area) 其实就是指 JVM 在运行期间，其对JVM内存空间的划分和分配。JVM在运行时将数据划分为了6个区域来存储。

程序员写的所有程序都被加载到`运行时数据区域`中，不同类别存放在heap, java stack, native method stack, PC register, method area.

下面对各个部分的功能和存储的内容进行描述：

![img](http://segmentfault.com/img/bVmxl8)

1、**PC程序计数器：一块较小的内存空间，可以看做是当前`线程`所执行的字节码的行号指示器, NAMELY存储每个线程下一步将执行的JVM指令，如该方法为native的，则PC寄存器中不存储任何信息。Java 的多线程机制离不开程序计数器，每个线程都有一个自己的PC，以便完成不同线程上下文环境的切换。**

2、**java虚拟机栈：与 PC 一样，java 虚拟机栈也是线程私有的。每一个 JVM 线程都有自己的 java 虚拟机栈，这个栈与线程同时创建，它的生命周期与线程相同。虚拟机栈描述的是`Java 方法执行的内存模型`：每个方法被执行的时候都会同时创建一个`栈帧（Stack Frame）`用于存储局部变量表、操作数栈、动态链接、方法出口等信息。`每一个方法被调用直至执行完成的过程就对应着一个栈帧在虚拟机栈中从入栈到出栈的过程`。**

3、**本地方法栈：与虚拟机栈的作用相似，虚拟机栈为虚拟机执行执行java方法服务，而本地方法栈则为虚拟机使用到的本地方法服务。**

4、**Java堆：被所有线程共享的一块存储区域，在虚拟机启动时创建，它是JVM用来存储对象实例以及数组值的区域，可以认为Java中所有通过new创建的对象的内存都在此分配。**

Java堆在JVM启动的时候就被创建，堆中储存了各种对象，这些对象被自动管理内存系统（Automatic Storage Management System，也即是常说的 “Garbage Collector（垃圾回收器）”）所管理。这些对象无需、也无法显示地被销毁。

JVM将Heap分为两块：新生代New Generation和旧生代Old Generation

Note:

- 堆在JVM是所有线程共享的，因此在其上进行对象内存的分配均需要进行加锁，这也是new开销比较大的原因。
- 鉴于上面的原因，Sun Hotspot JVM为了提升对象内存分配的效率，对于所创建的线程都会分配一块独立的空间，这块空间又称为TLAB
- TLAB仅作用于新生代的Eden Space，因此在编写Java程序时，通常多个小的对象比大的对象分配起来更加高效

5、**方法区
方法区和堆区域一样，是各个线程共享的内存区域，它用于存储每一个`类的结构信息`，例如运行时常量池，成员变量和方法数据，构造函数和普通函数的字节码内容，还包括一些在类、实例、接口初始化时用到的特殊方法。当开发人员在程序中通过Class对象中的getName、isInstance等方法获取信息时，这些数据都来自方法区。**

方法区也是全局共享的，在虚拟机启动时候创建。在一定条件下它也会被GC。这块区域对应Permanent Generation 持久代。 XX：PermSize指定大小。

6、**运行时常量池
其空间从方法区中分配，存放的为类中固定的常量信息、方法和域的引用信息。**



# 时钟周期、机器周期、指令周期

**时钟周期**

 时钟周期也称为振荡周期，定义为时钟脉冲的倒数(可以这样来理解，时钟周期就是单片机外接晶振的倒数，例如12M的晶振，它的时间周期就是1/12 us)，是计算机中最基本的、最小的时间单位。

 在一个时钟周期内，CPU仅完成一个最基本的动作。对于某种单片机，若采用了1MHZ的时钟频率，则时钟周期为1us;若采用4MHZ的时钟频率，则时钟周期为250ns。由于时钟脉冲是计算机的基本工作脉冲，它控制着计算机的工作节奏(使计算机的每一步都统一到它的步调上来)。显然，对同一种机型的计算机，时钟频率越高，计算机的工作速度就越快。但是，由于不同的计算机硬件电路和器件的不完全相同，所以其所需要的时钟周频率范围也不一定相同。我们学习的8051单片机的时钟范围是1.2MHz-12MHz。

 在8051单片机中把一个时钟周期定义为一个节拍(用P表示)，二个节拍定义为一个状态周期(用S表示)。

 

**机器周期**

 在计算机中，为了便于管理，常把一条指令的执行过程划分为若干个阶段，每一阶段完成一项工作。例如，取指令、存储器读、存储器写等，这每一项工作称为一个基本操作。完成一个基本操作所需要的时间称为机器周期。一般情况下，一个机器周期由若干个S周期(状态周期)组成。8051系列单片机的一个机器周期同6个S周期(状态周期)组成。前面已说过一个时钟周期定义为一个节拍(用P表示)，二个节拍定义为一个状态周期(用S表示)，8051单片机的机器周期由6个状态周期组成，也就是说一个机器周期=6个状态周期=12个时钟周期。

**指令周期**

 指令周期是执行一条指令所需要的时间，一般由若干个机器周期组成。指令不同，所需的机器周期数也不同。对于一些简单的的单字节指令，在取指令周期中，指令取出到指令寄存器后，立即译码执行，不再需要其它的机器周期。对于一些比较复杂的指令，例如转移指令、乘法指令，则需要两个或者两个以上的机器周期。

 通常含一个机器周期的指令称为单周期指令，包含两个机器周期的指令称为双周期指令。

> 移位运算为什么比乘法除法快?
>
> 使用[移位指令](https://www.baidu.com/s?wd=移位指令&tn=44039180_cpr&fenlei=mv6quAkxTZn0IZRqIHckPjm4nH00T1dBuAfkujRLuWbvmHDkuhm10ZwV5Hcvrjm3rH6sPfKWUMw85HfYnjn4nH6sgvPsT6KdThsqpZwYTjCEQLGCpyw9Uz4Bmy-bIi4WUvYETgN-TLwGUv3EPHTknjmvnj0Y)有更高的效率，因为[移位指令](https://www.baidu.com/s?wd=移位指令&tn=44039180_cpr&fenlei=mv6quAkxTZn0IZRqIHckPjm4nH00T1dBuAfkujRLuWbvmHDkuhm10ZwV5Hcvrjm3rH6sPfKWUMw85HfYnjn4nH6sgvPsT6KdThsqpZwYTjCEQLGCpyw9Uz4Bmy-bIi4WUvYETgN-TLwGUv3EPHTknjmvnj0Y)占2个机器周期，而乘除法指令占4个机器周期。
>
> 两个64位的数按位与 和 一个64位的数右移32位 哪个操作快些？
> 移位快，只有一次寻址，逻辑运算和写操作，按位与需要两次寻址，一次逻辑运算和一次写

# 深入理解Java字节码结构

https://blog.csdn.net/u011810352/article/details/80316870

# 机器码/指令/汇编语言/字节码

## 机器码

机器语言（Machine Language）―― 处理器的指令集及使用它们编写程序的规则。 

机器码(机器指令)：

**每个机器指令对应一个二进制数0和1组成的代码（Code）**，每一串的0和1的组合会被CPU硬件所解读成不同的指令==（CPU电路板上刻录了一个指令集，传入CPU的二级制文件会依据他而解读）==这是处理器能够直接执行的命令。

**一个机器语言程序就是一段二进制代码序列。一条指令（Instruction）就是机器语言的一个语句。**

## 指令

指令的基本格式：

**| 操作码字段 | 地址码字段 |**

依据不同CPU种类上的CPU指令集，将指令分为变长和定长，一般采用的变长（依据操作码决定由需要多少地址码）

**指令集＝指令系统（Instruction Set）―― 处理器支持的所有指令的集合。**

而==指令集也分为CISC（如：x86）和RISC（如：ARM）==两种，因为耗能和需要的晶体管数量的不同一般分别用于PC和移动设备

> **CISC的英文全称为“Complex Instruction Set Computer”，即“复杂指令系统计算机”**
>
> **RISC的英文全称为“Reduced Instruction Set Computer”，即“精简指令集计算机”**
>
> **1、指令系统**
>
> 　　CISC
> 　　计算机的指令系统比较丰富，有专用指令来完成特定的功能。因此，处理特殊任务效率较高。
>
> 　　RISC
>
> 　　设计者把主要精力放在那些经常使用的指令上，尽量使它们具有简单高效的特色。对不常用的功能，常通过组合指令来完成。因此，在RISC 机器上实现特殊功能时，效率可能较低。但可以利用流水技术和超标量技术加以改进和弥补。
>
> 　　**2、存储器操作**
>
> 　　CISC
> 　　机器的存储器操作指令多，操作直接。
>
> 　　RISC
>
> 　　对存储器操作有限制，使控制简单化。
>
> 　　**3、程序**
>
> 　　CISC
>
> 　　汇编语言程序编程相对简单，科学计算及复杂操作的程序社设计相对容易，效率较高。
>
> 　　RISC
>
> 　　汇编语言程序一般需要较大的内存空间，实现特殊功能时程序复杂，不易设计。
>
> 　　**4、中断**
>
> 　　CISC
>
> 　　机器是在一条指令执行结束后响应中断。
>
> 　　RISC
>
> 　　机器在一条指令执行的适当地方可以响应中断。
>
> 　　**5、CPU**
>
> 　　CISC
>
> 　　CPU包含有丰富的电路单元，因而功能强、面积大、功耗大。
>
> 　　RISC
>
> 　　CPU包含有较少的单元电路，因而面积小、功耗低。
>
> 　　**6、设计周期**
>
> 　　CISC
>
> 　　微处理器结构复杂，设计周期长。
>
> 　　RISC
>
> 　　微处理器结构简单，布局紧凑，设计周期短，且易于采用最新技术。
>
> 　　**7、用户使用**
>
> 　　CISC
>
> 　　微处理器结构复杂，功能强大，实现特殊功能容易。
>
> 　　RISC
>
> 　　微处理器结构简单，指令规整，性能容易把握，易学易用。
>
> 　　**8、应用范围**
> 　　CISC
>
> 　　机器则更适合于通用机。
>
> 　　RISC
>
> 　　由于RISC指令系统的确定与特定的应用领域有关，故RISC 机器更适合于专用机。

**汇编语言（Assembly Language）――  用==助记符表示的指令==以及使用它们编写程序的规则。** 

**汇编（Assembly）――  将汇编语言书写的程序翻译成机器语言程序的过程。** 

**汇编程序（Assembler）――  将汇编语言书写的程序翻译成机器语言程序的软件。不要与汇编语言程序这个说法混淆，后者表示用汇编语言书写的程序，或称汇编语言源程序。**

**反汇编（Disassembly）—— 将机器码转换为汇编代码**（常用于我们阅读编译器编译后生成机器码时，方便我们阅读时使用）

**不同的平台需要依据其指令集用不同的汇编或反汇编适配器**

## 字节码

字节码（Bytecode）是一种包含执行程序、由一序列 op 代码/数据对 组成的**二进制文件**。**字节码是一种中间码**，它比机器码更抽象，需要直译器转译后才能成为机器码的中间代码。

通常情况下它是已经经过编译，但与特定机器码无关。字节码通常不像源码一样可以让人阅读，而是编码后的数值常量、引用、指令等构成的序列。

字节码主要为了实现特定软件运行和软件环境、与硬件环境无关。字节码的实现方式是通过编译器和虚拟机器。编译器将源码编译成字节码，特定平台上的虚拟机器将字节码转译为可以直接执行的指令。字节码的典型应用为Java bytecode（）通过前端编译器JDK的javac。

再由后端的即时编译器将其编译为机器码（二进制代码）进行运行

# jdk与JRE

jdk与jre的主要区别便是在编译器的不同上

jre中除了基于JVM规范的即时编译器的实现如HotSpot等还有其相关的lib类库外，不包含其他的编译器

jdk包含了前端编译器和jre与提前编译器、依赖的类库与测试时依据不同参数的检测工具等。

许多jdk中不开放的源码都被用.dll等动态链接文件所保护起来

![image-20200907141213065](F:\Typora数据储存\高级语言\JAVA\JAVA虚拟机简介.assets\image-20200907141213065.png)

# HotSpot

因为吸收了Sun和Oracle融合了SunJDK和BEASpot的优点而有了JDK8之后(移除掉永久代,添加了Java Mission Control监控工具),Sun/OeacleJDK的统治地位导致其JVM的实现HotSpot也具有了很高的地位.

为了保证HotSpot和生命力,在逐步修改HotSpot代码保证开发接口后不被侵入HotSpot源码后逐步开放了虚拟机的工具接口

现在，HotSpot虚拟机也有了与J9类似的能力，能够在编译时指定一系列特性开关，让编译输出的 HotSpot虚拟机可以裁剪成不同的功能，譬如支持哪些编译器，支持哪些收集器，是否支持JFR现在，HotSpot虚拟机也有了与J9类似的能力，能够在编译时指定一系列特性开关，让编译输出的 HotSpot虚拟机可以裁剪成不同的功能，譬如支持哪些编译器，支持哪些收集器，是否支持JFR、 AOT、CDS、NMT等都可以选择。能够实现这些功能特性的组合拆分，反映到源代码不仅仅是条件编 译，更关键的是接口与实现的分离。

早期（JDK 1.4时代及之前）的HotSpot虚拟机为了提供监控、调试等不会在《Java虚拟机规范》 中约定的内部功能和数据，就曾开放过Java虚拟机信息监控接口（Java Virtual Machine ProfilerInterface，JVMPI）与Java虚拟机调试接口（Java Virtual Machine Debug Interface，JVMDI）供运维和性 能监控、IDE等外部工具使用。

到了JDK 5时期，又抽象出了层次更高的Java虚拟机工具接口（JavaVirtual Machine Tool Interface，JVMTI）来为所有Java虚拟机相关的工具提供本地编程接口集合，到 JDK 6时**JVMTI就完全整合代替了JVMPI和JVMDI的作用**。 

**在JDK 9时期，HotSpot虚拟机开放了Java语言级别的编译器接口**[3]（Java Virtual Machine CompilerInterface，JVMCI），使得在Java虚拟机外部增加、替换即时编译器成为可能，这个改进实现起来并不费劲，但比起之前JVMPI、JVMDI和JVMTI却是更深层次的开放，它为不侵入HotSpot代码而增加或修改HotSpot虚拟机的固有功能逻辑提供了可行性。Graal编译器就是通过这个接口植入到HotSpot之 中.

**到了JDK 10，HotSpot又重构了Java虚拟机的垃圾收集器接口**[4]（Java Virtual Machine CompilerInterface），统一了其内部各款垃圾收集器的公共行为。有了这个接口，才可能存在日后（今天尚未） 某个版本中的CMS收集器退役，和JDK 12中Shenandoah这样由Oracle以外其他厂商领导开发的垃圾收 集器进入HotSpot中的事情。如果未来这个接口完全开放的话，甚至有可能会出现其他独立于HotSpot 的垃圾收集器实现。 AOT、CDS、NMT等都可以选择。能够实现这些功能特性的组合拆分，反映到源代码不仅仅是条件编译，更关键的是接口与实现的分离。 



# Graal VM

Graal VM的基本工作原理是**将这些语言的源代码（例如JavaScript）或源代码编译后的中间格式 （例如LLVM字节码）通过解释器转换为能被Graal VM接受的中间表示（Intermediate Representation， IR），**譬如设计一个解释器专门对LLVM输出的字节码进行转换来支持C和C++语言，这个过程称为程 序特化（Specialized，也常被称为Partial Evaluation）。Graal VM提供了**Truffle工具**集来快速构建面向一 种新语言的解释器，并用它构建了一个称为Sulong的高性能LLVM字节码解释器。 

对Java而言，Graal VM本来就是在HotSpot基础上诞生的，天生就可作为一套完整的符合Java SE 8 标准的Java虚拟机来使用。它和标准的HotSpot的差异主要在即时编译器上，其执行效率、编译质量目前与标准版的HotSpot相比也是互有胜负。但是HotSpot以及其编译器Client和Server Complier都是C编写的，**而Graal VM则是java编写的，可以与前端编译器一样更好地阅读**



# 前端编译器

·前端编译器：JDK的Javac、Eclipse JDT中的增量式编译器（ECJ）

>  把*.java文件转变成*.class文件的过程；

## Javac编译器 

在JDK 6以前，Javac并不属于标准Java SE API的一部分，它实现代码单独存放在tools.jar中，要在程序中使用的话就必须把这个库放到类路径上。在JDK 6发布时通过了JSR 199编译器API的提案，使得Javac编译器的实现代码晋升成为标准Java类库之一，它的源码就改为放在 

JDK_SRC_HOME/langtools/src/share/classes/com/sun/tools/javac中[1]。到了JDK 9时，整个JDK所有的Java类库都采用模块化进行重构划分，Javac编译器就被挪到了jdk.compiler模块（路径为：JDK_SRC_HOME/src/jdk.compiler/share/classes/com/sun/tools/javac）里面。

Javac编译器除了JDK自身的标准类库外，就只引用了 JDK_SRC_HOME/langtools/src/share/classes/com/sun/*里面的代码，所以我们的代码编译环境建立时基 本无须处理依赖关系，相当简单便捷。

导入了Javac的源码后，就可以运行com.sun.tools.javac.Main的main()方法来执行编译了，可以使用 的参数与命令行中使用的Javac命令没有任何区别，编译的文件与参数在Eclipse的“Debug Configurations”面板中的“Arguments”页签中指定。 

> 《Java虚拟机规范》中严格定义了Class文件格式的各种细节，可是对如何把Java源码编译为Class 文件却描述得相当宽松。规范里尽管有专门的一章名为“Compiling for the Java Virtual Machine”，但这 章也仅仅是以举例的形式来介绍怎样的Java代码应该被转换为怎样的字节码，并没有使用编译原理中 常用的描述工具（如文法、生成式等）来对Java源码编译过程加以约束。这是给了Java前端编译器较大的实现灵活性，但也导致Class文件编译过程在某种程度上是与具体的JDK或编译器实现相关的，譬如 在一些极端情况下，可能会出现某些代码在Javac编译器可以编译，但是ECJ编译器就不可以编译的问题（反过来也有可能，后文中将会给出一些这样的例子）。 

==从Javac代码的总体结构来看，编译过程大致可以分为1个准备过程和3个处理过程，它们分别如下所示。==

==1）准备过程：初始化插入式注解处理器。== 

==2）解析与填充符号表过程，包括：== 

==·词法、语法分析。将源代码的字符流转变为标记集合，构造出抽象语法树。== 

==·填充符号表。产生符号地址和符号信息。==

==3）插入式注解处理器的注解处理过程：插入式注解处理器的执行阶段==

==4）分析与字节码生成过程，包括：== 

==·标注检查。对语法的静态信息进行检查。== 

==·数据流及控制流分析。对程序动态运行过程进行检查。== 

==·解语法糖。将简化代码编写的语法糖还原为原有的形式。== 

==·字节码生成。将前面各个步骤所生成的信息转化成字节码。== 

==上述3个处理过程里，执行插入式注解时又可能会产生新的符号，如果有新的符号产生，就必须转回到之前的解析、填充符号表的过程中重新处理这些新符号，从总体来看，三者之间的关系与交互顺序如图==

![image-20200907165240464](F:\Typora数据储存\高级语言\JAVA\JAVA虚拟机简介.assets\image-20200907165240464.png)



我们可以把上述处理过程对应到代码中，Javac编译动作的入口是 com.sun.tools.javac.main.JavaCompiler类，上述3个过程的代码逻辑集中在这个类的compile()和compile2() 方法里，其中主体代码如图10-5所示，整个编译过程主要的处理由图中标注的8个方法来完成。

![image-20200907165515959](F:\Typora数据储存\高级语言\JAVA\JAVA虚拟机简介.assets\image-20200907165515959.png)

# 即时编译器

即时编译器：HotSpot虚拟机的C1、C2编译器，Graal编译器。 

> （常称JIT编译器，Just In Time Compiler）运行期把字节码转变成本地机器码的过程；

HotSpot虚拟机中含有两个即时编译器，分别是编译耗时短但输出代码优化程度较低的客户端编译器（简称为C1）以及编译耗时长但输出代码优化质量也更高的服务端编译器（简称为C2），通常它们 会在分层编译机制下与解释器互相配合来共同构成HotSpot虚拟机的执行子系统。

自JDK 10起，HotSpot中又加入了一个全新的即时编译器：Graal编译器。C2的历史已经非常长了，可以追溯到Cliff Click大神读博士期间的作品，这个由C++写成的编译器尽管目前依然效果拔 群，但已经复杂到连Cliff Click本人都不愿意继续维护的程度。

而Graal编译器本身就是由Java语言写 成，实现时又刻意与C2采用了同一种名为“Sea-of-Nodes”的高级中间表示（High IR）形式，使其能够 更容易借鉴C2的优点。

## C1 / C2

譬如HotSpot、OpenJ9等，内部都同时包含解释器与编译器[1]，解释器与编译器两者各有优势：

**如果没有做过任何设置，执行引擎默认不会同步等待编译请求完成，而是继续进入解释器按照解释方式执行字节码，直到提交的请求被即时编译器编译完成。**

> 当程序需要迅速启动和执行的时候，解释器可以首先发挥作用，省去编译的时间，立即运行。当程序 启动后，随着时间的推移，编译器逐渐发挥作用，把越来越多的代码编译成本地代码，这样可以减少解释器的中间损耗，获得更高的执行效率。
>
> 当程序运行环境中内存资源限制较大，可以使用解释执行节约内存（如部分嵌入式系统中和大部分的JavaCard应用中就只有解释器的存在），反之可以使用编译执行来提升效率。

同时，解释器还可以作为编译器激进优化时后备的“逃生门”（如果情况允许， HotSpot虚拟机中也会采用不进行激进优化的客户端编译器充当“逃生门”的角色），让编译器根据概率 选择一些不能保证所有情况都正确，但大多数时候都能提升运行速度的优化手段，当激进优化的假设不成立，如加载了新类以后，类型继承结构出现变化、出现“罕见陷阱”（Uncommon Trap）时可以通 过逆优化（Deoptimization）退回到解释状态继续执行，因此在整个Java虚拟机执行架构里，解释器与 编译器经常是相辅相成地配合工作，其交互关系如图11-1所示。

![image-20200907171454557](F:\Typora数据储存\高级语言\JAVA\JAVA虚拟机简介.assets\image-20200907171454557.png)

> 解释器
>
> 解释器是一行一行地将字节码解析成机器码，解释到哪就执行到哪，狭义地说，就是for循环100次，你就要将循环体中的代码逐行解释执行100次。当程序需要迅速启动和执行时，解释器可以首先发挥作用，省去编译的时间，立即执行。
>
> 无论采用的编译器是客户端编译器还是服务端编译器，解释器与编译器搭配使用的方式在虚拟机中被称为“混合模式”（Mixed Mode）
>
> 用户也可以使用参数“-Xint”强制虚拟机运行于“解释模式”（Interpreted Mode），这时候编译器完全不介入工作，全部代码都使用解释方式执行。另外，也 
>
> 可以使用参数“-Xcomp”强制虚拟机运行于“编译模式”（Compiled Mode），这时候将优先采用编译方式执行程序，但是解释器仍然要在编译无法进行的情况下介入执行过程。

直到JDK 6时期才被初步实现，后来一直处于改进阶段，最终在JDK 7的服务端模式虚拟机中作为默认 

编译策略被开启。分层编译根据编译器编译、优化的规模与耗时，划分出不同的编译层次，其中包 

括：

·第0层。程序纯解释执行，并且解释器不开启性能监控功能（Profiling）。 

·第1层。使用客户端编译器将字节码编译为本地代码来运行，进行简单可靠的稳定优化，不开启 性能监控功能。 

·第2层。仍然使用客户端编译器执行，仅开启方法及回边次数统计等有限的性能监控功能。 

·第3层。仍然使用客户端编译器执行，开启全部性能监控，除了第2层的统计信息外，还会收集如 分支跳转、虚方法调用版本等全部的统计信息。 

·第4层。使用服务端编译器将字节码编译为本地代码，相比起客户端编译器，服务端编译器会启 用更多编译耗时更长的优化，还会根据性能监控信息进行一些不可靠的激进优化。 

以上层次并不是固定不变的，根据不同的运行参数和版本，虚拟机可以调整分层的数量。各层次 

编译之间的交互、转换关系如图11-2所示。实施分层编译后，解释器、客户端编译器和服务端编译器就会同时工作，热点代码都可能会被多 次编译，用客户端编译器获取更高的编译速度，用服务端编译器来获取更好的编译质量，在解释执行 的时候也无须额外承担收集性能监控信息的任务，而在服务端编译器采用高复杂度的优化算法时，客 户端编译器可先采用简单优化来为它争取更多的编译时间。 

![image-20200907182241153](F:\Typora数据储存\高级语言\JAVA\JAVA虚拟机简介.assets\image-20200907182241153.png)

#### 编译对象与触发条件 

在运行过程中会被即时编译器编译的目标是“热点代码”，这里所指的热点代 码主要有两类，包括：(对于这两种情况，编译的目标对象都是整个方法体，而不会是单独的循环体。) 

·**被多次调用的方法**(被调用得多了，方法体内代码执行的次数自然就多，它成为“热点代码”) 

·被多次执行的循环体(当一个方法只被调用过一次或少量的几次，但是方法体内部存在循环次数较多的循环体，这样循环体的代码也被重复执行多次，因此这些代码也应该认为是“热点代码”。)

这个行为称为“热点探测”（HotSpot Code Detection），其实进行热点探测并不一定要知道方法具体被调用了多少次，目前主流的热点 

探测判定方式有两种[2]，分别是： 

·**基于采样的热点探测（Sample Based Hot Spot Code Detection）。**采用这种方法的虚拟机会周期性地检查各个线程的调用栈顶，如果**发现某个（或某些）方法经常出现在栈顶，那这个方法就是“热点方法”**。基于采样的热点探测的好处是实现简单高效，还可以很容易地获取方法调用关系（将调用堆栈展开即可），**缺点是很难精确地确认一个方法的热度**，容易因为受到线程阻塞或别的外界因素的影响而扰乱热点探测。 

·**基于计数器的热点探测（Counter Based Hot Spot Code Detection）**。采用这种方法的虚拟机会**为每个方法（甚至是代码块）建立计数器，统计方法的执行次数，如果执行次数超过一定的阈值就认为 它是“热点方法”。**这种统计方法实现起来要麻烦一些，需要为每个方法建立并维护计数器，而且不能直接获取到方法的调用关系。但是它的统计结果相对来说更加精确严谨.

J9用过第一种采样热点探测，而在**HotSpot虚拟机中使用的是第二种基于计数器的热点探测方法**，为了实现热点计数,HotSpot为每个方法准备了两类计数器：**方法调用计数器（Invocation Counter）和回边计数器（Back Edge Counter，“回边”的意思就是指在循环边界往回跳转）**。当虚拟机运行参数确定的前提下，这两个计数器都有一个明确的阈 值，计数器阈值一旦溢出，就会触发即时编译。 

###### 方法调用计数器

这个计数器就是用于统计方法被调用的次数，它的 默认阈值在客户端模式下是1500次，在服务端模式下是10000次，这个阈值可以通过虚拟机参数-XX： CompileThreshold来人为设定。

当一个方法被调用时，虚拟机会先检查该方法是否存在被即时编译过的 版本，如果存在，则优先使用编译后的本地代码来执行。

如果不存在已被编译过的版本，则将该方法的调用计数器值加一，然后判断方法调用计数器与回边计数器值之和是否超过方法调用计数器的阈值。一旦已超过阈值的话，将会向即时编译器提交一个该方法的代码编译请求。

如果没有做过任何设置，执行引擎默认不会同步等待编译请求完成，而是继续进入解释器按照解 释方式执行字节码，直到提交的请求被即时编译器编译完成。当编译工作完成后，这个方法的调用入口地址就会被系统自动改写成新值，下一次调用该方法时就会使用已编译的版本了，整个即时编译的交互过程如图11-3所示。 (客户端)

> 在默认设置下，方法调用计数器统计的并不是方法被调用的绝对次数，而是一个相对的执行频 率，即一段时间之内方法被调用的次数。
>
> 当超过一定的时间限度，如果方法的调用次数仍然不足以让 它提交给即时编译器编译，那该方法的调用计数器就会被减少一半，这个过程被称为方法调用计数器热度的衰减（Counter Decay），而这段时间就称为此方法统计的半衰周期（Counter Half Life Time）， 进行热度衰减的动作是在虚拟机进行垃圾收集时顺便进行的，可以使用虚拟机参数-XX：-UseCounterDecay来关闭热度衰减，让方法计数器统计方法调用的绝对次数，这样只要系统运行时间足够长，程序中绝大部分方法都会被编译成本地代码。另外还可以使用-XX：CounterHalfLifeTime参数设置半衰周期的时间，单位是秒。

![image-20200907183628783](F:\Typora数据储存\高级语言\JAVA\JAVA虚拟机简介.assets\image-20200907183628783.png)

###### 回边计数器

它的作用是统计一个方法中循环体代码执行的次数，在字节码中遇到控制流向后跳转的指令就称为“回边（Back Edge）”，很显然建立回边计数器统计的目的是为了触发栈上的替换编译。 

> 关于回边计数器的阈值，虽然HotSpot虚拟机也提供了一个类似于方法调用计数器阈值-XX：CompileThreshold的参数-XX：BackEdgeThreshold供用户设置，但是当前的HotSpot虚拟机(jdk1.8)实际上并未使用此参数，我们必须设置另外一个参数-XX：OnStackReplacePercentage来间接调整回边计数器的阈值，其计算公式有如下两种。 

当解释器遇到一条回边指令时，会先查找将要执行的代码片段是否有已经编译好的版本，如果有的话，它将会优先执行已编译的代码，否则就把回边计数器的值加一，然后判断方法调用计数器与回边计数器值之和是否超过回边计数器的阈值。当超过阈值的时候，将会提交一个栈上替换编译请求， 并且把回边计数器的值稍微降低一些，以便继续在解释器中执行循环，等待编译器输出编译结果，整个执行过程如图11-4所示。(客户端)

> 与方法计数器不同，回边计数器没有计数热度衰减的过程，**因此这个计数器统计的就是该方法循环执行的绝对次数。当计数器溢出的时候，它还会把方法计数器的值也调整到溢出状态**，这样下次再进入该方法的时候就会执行标准编译过程。 

![image-20200907183929242](F:\Typora数据储存\高级语言\JAVA\JAVA虚拟机简介.assets\image-20200907183929242.png)

#### 编译过程 

服务端编译器和客户端编译器的编译 过程是有所差别的。对于客户端编译器来说，它是一个相对简单快速的三段式编译器，主要的关注点在于局部性的优化，而放弃了许多耗时较长的全局优化手段.

在第一个阶段，一个平台独立的前端将字节码构造成一种高级中间代码表示（High-Level Intermediate Representation，HIR，即与目标机器指令集无关的中间表示）。HIR使用静态单分配 （Static Single Assignment，SSA）的形式来代表代码值，这可以使得一些在HIR的构造过程之中和之后进行的优化动作更容易实现。在此之前编译器已经会在字节码上完成一部分基础优化，如方法内联、 

常量传播等优化将会在字节码被构造成HIR之前完成。在第二个阶段，一个平台相关的后端从HIR中产生低级中间代码表示（Low-Level IntermediateRepresentation，LIR，即与目标机器指令集相关的中间表示），而在此之前会在HIR上完成另外一些优化，如空值检查消除、范围检查消除等，以便让HIR达到更高效的代码表示形式。 最后的阶段是在平台相关的后端使用线性扫描算法（Linear Scan Register Allocation）在LIR上分配寄存器，并在LIR上做窥孔（Peephole）优化，然后产生机器代码。客户端编译器大致的执行过程如图 11-5所示

![image-20200907184444332](F:\Typora数据储存\高级语言\JAVA\JAVA虚拟机简介.assets\image-20200907184444332.png)

而服务端编译器则是专门面向服务端的典型应用场景，并为服务端的性能配置针对性调整过的编 译器，也是一个能容忍很高优化复杂度的高级编译器，几乎能达到GNU C++编译器使用-O2参数时的 优化强度。它会执行大部分经典的优化动作，如：无用代码消除（Dead Code Elimination）、循环展开 （Loop Unrolling）、循环表达式外提（Loop Expression Hoisting）、消除公共子表达式（Common Subexpression Elimination）、常量传播（Constant Propagation）、基本块重排序（Basic BlockReordering）等，还会实施一些与Java语言特性密切相关的优化技术，如范围检查消除（Range CheckElimination）、空值检查消除（Null Check Elimination，不过并非所有的空值检查消除都是依赖编译器优化的，有一些是代码运行过程中自动优化了）等。另外，还可能根据解释器或客户端编译器提供的性能监控信息，进行一些不稳定的预测性激进优化，如守护内联（Guarded Inlining）、分支频率预测（Branch Frequency Prediction）等.

服务端编译采用的寄存器分配器是一个全局图着色分配器，它可以充分利用某些处理器架构（如RISC）上的大寄存器集合。

> 观察细节:
>
> 1. 编译后若是要查看机器码内容,需要反汇编才能更加方便地阅读指令,虚拟机(如HotSpot)为我们提供了反汇编接口,我们只需要下载相应的反汇编适配器，如使用32位x86平台应选用hsdis-i386适配器，64位则需要选用hsdis-amd64[2]，其余平台的 适配器还有如hsdis-sparc、hsdis-sparcv9和hsdis-aarch64等，读者可以下载或自己编译出与自己机器相 符合的反汇编适配器，之后将其放置在JAVA_HOME/lib/amd64/server下[3]，只要与jvm.dll或libjvm.so的路径相同即可被虚拟机调用。为虚拟机安装了反汇编适配器之后，我们就可以使用-XX：+PrintAssembly参数要求虚拟机打印编译方法的汇编代码了
>
> 2. 如果除了本地代码的生成结果外，还想再进一步跟踪本地代码生成的具体过程，那可以使用参数- XX：+PrintCFGToFile（用于客户端编译器）或-XX：PrintIdealGraphFile（用于服务端编译器）要求 Java虚拟机将编译过程中各个阶段的数据（譬如对客户端编译器来说包括字节码、HIR生成、LIR生成、寄存器分配过程、本地代码生成等数据）输出到文件中。然后使用Java HotSpot Client CompilerVisualizer[4]（用于分析客户端编译器）或Ideal Graph Visualizer[5]（用于分析服务端编译器）打开这些数据文件进行分析。
>
>    服务端编译器的中间代码表示是一种名为理想图（Ideal Graph）的程序依赖图（ProgramDependence Graph，PDG），在运行Java程序的FastDebug或SlowDebug优化级别的虚拟机上的参数中加入“-XX：PrintIdealGraphLevel=2-XX：PrintIdeal-GraphFile=ideal.xml”，即时编译后将会产生一个名为ideal.xml的文件，它包含了服务端编译器编译代码的全过程信息，可以使用Ideal Graph Visualizer对这些信息进行分析。

## Graal编译器

Graal编译器在JDK 9时以Jaotc提前编译工具的形式首次加入到官方的JDK中，从JDK 10起，Graal编译器可以替换服务端编译器，成为HotSpot分层编译中最顶层的即时编译器。这种可替换的即时编译 器架构的实现，得益于HotSpot编译器接口的出现。早期的Graal曾经同C1及C2一样，与HotSpot的协作是紧耦合的，这意味着每次编译Graal均需重新 编译整个HotSpot。

JDK 9时发布的JEP 243：Java虚拟机编译器接口（Java-Level JVM Compiler Interface，JVMCI）使得Graal可以从HotSpot的代码中分离出来。JVMCI主要提供如下三种功能： 

·==响应HotSpot的编译请求，并将该请求分发给Java实现的即时编译器。== 

==·允许编译器访问HotSpot中与即时编译相关的数据结构，包括类、字段、方法及其性能监控数据等，并提供了一组这些数据结构在Java语言层面的抽象表示。== 

==·提供HotSpot代码缓存（Code Cache）的Java端抽象表示，允许编译器部署编译完成的二进制机器码。==

综合利用上述三项功能，我们就可以把一个在HotSpot虚拟机外部的、用Java语言实现的即时编译器（不局限于Graal）集成到HotSpot中，响应HotSpot发出的最顶层的编译请求，并将编译后的二进制代码部署到HotSpot的代码缓存中。此外，单独使用上述第三项功能，又可以绕开HotSpot的即时编译系统，让该编译器直接为应用的类库编译出二进制机器码，将该编译器当作一个提前编译器去使用(如Jaotc）。 

#### 构建编译调试环境

**由于Graal编译器要同时支持Graal VM下的各种子项目，如Truffle、Substrate VM、Sulong等（Graal编译器除了比C2容易维护外，还应有这些工具子项目的支持而扩展性更好），还要支持作为HotSpot和Maxine虚拟机的即时编译器**，所以只用Maven或Gradle的话，配置管理过程会相当复杂。为了降低代码管理、依赖项管理、编译和测试等环节的复杂度，Graal团队专门用Python 2写了一个名为==mx==的小工具来自动化做好这些事情。

#### JVMCI编译器接口 

![image-20200908172944613](F:\Typora数据储存\高级语言\JAVA\JAVA虚拟机简介.assets\image-20200908172944613.png)

![image-20200908172854956](F:\Typora数据储存\高级语言\JAVA\JAVA虚拟机简介.assets\image-20200908172854956.png)

#### 代码中间表示

Graal编译器在设计之初就刻意采用了与HotSpot服务端编译器一致（略有差异但已经非常接近） 的中间表示形式，也即是被称为**Sea-of-Nodes的中间表示**，或者**与其等价的被称为理想图（Ideal Graph，在代码中称为Structured Graph）的程序依赖图（Program Dependence Graph，PDG）形式。**

在理想图上翻译和 优化输入代码的整体过程，从编译器内部来看即：字节码→理想图→优化→机器码（以Mach NodeGraph表示）的转变过程。

## 编译后机器码存储

链接：https://www.zhihu.com/question/52487484/answer/130785455

“JIT编译器”的部分实现的就是题主所提到的“把Class文件编译到机器码”的功能。

把Class文件（中的Java字节码）编译到机器码的功能是JVM的实现细节，而不是Java语言规范或JVM规范所要求的功能。所以只能针对每个JVM实现单独讨论。

就一个有JIT编译器的JVM而言，==JIT编译发生在运行时，通常是直接在内存里把Java字节码编译到机器码的。也就是说生成的机器码会直接生成在内存里，然后直接投入执行，而不会被保存到磁盘（或其它持久化存储媒体）上。==
有少量JVM实现会带有“dynamic AOT编译”功能，可以把JIT编译的结果存在磁盘上，以后再运行的时候读出来直接用，例如IBM J9 JVM。这是比较少见的、有趣的做法。请参考传送门：[IBM Knowledge Center](https://link.zhihu.com/?target=http%3A//www.ibm.com/support/knowledgecenter/SSYKE2_7.0.0/com.ibm.java.aix.70.doc/diag/understanding/aot.html)

就一个有AOT编译器的JVM而言，AOT（Ahead-of-Time）编译发生在Java程序运行之前，通常会把Class文件读进来然后把编译生成的机器码存储到磁盘上。等到实际运行Java程序的时候直接从磁盘上读出之前编译好的结果来执行。使用这种模型的典型例子有例如Excelsior JET、GCJ、Android Runtime（ART）等。
Oracle JDK9里的新的JAOTC也是这样的例子。请参考传送门：[JEP 295: Ahead-of-Time Compilation](https://link.zhihu.com/?target=http%3A//openjdk.java.net/jeps/295)

回到“如何查看编译出来的机器码”的问题。这个只能针对具体的实现来说。

- Oracle/Sun JDK的HotSpot VM：对于其中的JIT编译器，查看它生成的代码的办法是指导 -XX:+UnlockDiagnosticVMOptions -XX:+PrintAssembly 参数，搭配一个叫做“hsdis”的插件来使用。我以前写过相关的答复：[X86 asm代码 是怎么才能看到？](https://link.zhihu.com/?target=http%3A//hllvm.group.iteye.com/group/topic/21769%23post-159708) 现在相关资料已经很多了，题主可以用“PrintAssembly hsdis”关键字搜出各方面的资料来参考。

- - 现在还有一个[JITWatch](https://link.zhihu.com/?target=https%3A//github.com/AdoptOpenJDK/jitwatch/wiki)项目，以可视化图像界面来现实PrintAssembly的结果，很是方便。题主感兴趣的话可以参考它的文档来切入

- Oracle JDK9新添加的JAOTC：这是一个AOT编译器，可以把指定的Java Class文件 / module文件给编译成机器码。目前它只支持在Linux/x86-64上使用，==生成的机器码被包装在ELF格式的文件中==。所以用常规的工具就可以查看它的内容，例如用Linux自带的objdump。

- IBM J9的JIT编译器：它也有提供trace出编译出来的机器码的功能，不过我不知道具体的命令是怎样的。以前我还在学校的时候，把玩J9研究它生成的代码，是通过trace JIT编译事件，在日志文件中得到编译生成的代码的内存地址，然后连接一个native调试器上去把这个地址开始的机器码dump出来，这样的办法来弄的。

- Android Runtime的AOT编译器：ART的AOT编译器生成的OAT文件虽然也是ELF格式的包装，但它的ELF结构…并不是特别完整。要查看它的内容最好用ART自带的oatdump工具。

- Maxine VM：用Maxine Inspector简直无敌。

下面放一张Maxine Inspector的截图给同学们感受一下Java字节码 -> 机器码的对应关系以及整个JVM的执行状态：

![img](https://pic3.zhimg.com/80/v2-6db8f6b1d5d0517ceb3d6a9f8c840882_720w.jpg?source=1940ef5c)

# 提前编译器

·提前编译器：JDK的Jaotc、GNU Compiler for the Java（GCJ）、Excelsior JET。

> （常称AOT编译器，Ahead Of Time Compiler）直接把程 序编译成与目标机器指令集相关的二进制代码的过程。

* 违反了“一次编写，到处运行”的承诺,但是适应了微服务的发展

对不需要长时间运行的，或者小型化的应用而言，Java（而不是指Java ME）天生就带有一些劣势，这里并不只是指跑个HelloWorld也需要百多兆的JRE之类的问题，更重要的是指近几年在从大型单体应用架构向小型微服务应用架构发展的技术潮流下，Java表现出来的不适应。 (比如一个手机应用启动,没有人喜欢在启动页面等半天,即使启动后可能用起来会越来越流畅)

==在微服务架构的视角下==，应用拆分后，单个微服务很可能就不再需要面对数十、数百GB乃至TB 的内存，有了高可用的服务集群，也无须追求单个服务要7×24小时不间断地运行，**它们随时可以中断和更新**；但相应地**，Java的启动时间相对较长，需要预热才能达到最高性能等特点就显得相悖于这样的应用场景**。在无服务架构中，矛盾则可能会更加突出，比起服务，一个函数的规模通常会更小，执行时间会更短，当前最热门的无服务运行环境AWS Lambda所允许的最长运行时间仅有15分钟。

一直把软件服务作为重点领域的Java自然不可能对此视而不见，在最新的几个JDK版本的功能清单中，已经陆续推出了跨进程的、可以面向用户程序的类型信息共享（Application Class Data Sharing，AppCDS，允许把加载解析后的类型信息缓存起来，从而提升下次启动速度，原本CDS只支 持Java标准库，在JDK 10时的AppCDS开始支持用户的程序代码）、无操作的垃圾收集器（Epsilon， 只做内存分配而不做回收的收集器，对于运行完就退出的应用十分合适）等改善措施。而酝酿中的一个更彻底的解决方案，是逐步开始对提前编译（Ahead of Time Compilation，AOT）提供支持。

但是提前编译的坏处也很明显，它破坏了Java“一次编写，到处运行”的承诺，==必须为每个不同的 硬件、操作系统去编译对应的发行包==；也显著降低了Java链接过程的动态性，==必须要求加载的代码在编译期就是全部已知的，而不能在运行期才确定，否则就只能舍弃掉已经提前编译好的版本，退回到原来的即时编译执行状态==

大家原本期望的是类似于Excelsior JET那样的编译过后能生成本地代码完全脱离Java虚拟机运行的 解决方案，但Jaotc其实仅仅是代替即时编译的一部分作用而已，**仍需要运行于HotSpot之上。** 

直到Substrate VM出现，才算是满足了人们心中对Java提前编译的全部期待**。Substrate VM是在 Graal VM 0.20版本里新出现的一个极小型的运行时环境，包括了独立的异常处理、同步调度、线程管理、内存管理（垃圾收集）和JNI访问等组件，目标是代替HotSpot用来支持提前编译后的程序执行。**(少了HotSpot一些对于提前编译不需要的工具与监控资源的消耗) 它还包含了一个本地镜像的构造器（Native Image Generator），用于为用户程序建立基于Substrate VM的本地运行时镜像。这个构造器采用指针分析（Points-To Analysis）技术，从用户提供的程序入口出发，搜索所有可达的代码。在搜索的同时，它还将执行初始化代码，并在最终生成可执行文件时，将已初始化的堆保存至一个堆快照之中。

这样一来，Substrate VM就可以直接从目标程序开始运行，而无须重复进行Java虚拟机的初始化过程。但相应地，原理上也决定了Substrate VM必须要求目标程序是完全封闭的，即不能动态加载其他编译器不可知的代码和类库。基于这个假设，Substrate VM才能探索整个编译空间，并通过静态分析推算出所有虚方法调用的目标方法。 Substrate VM带来的好处是能显著降低内存占用及启动时间，由于HotSpot本身就会有一定的内存消耗（通常约几十MB），这对最低也从几GB内存起步的大型单体应用来说并不算什么，但在微服务 下就是一笔不可忽视的成本。根据Oracle官方给出的测试数据，运行在Substrate VM上的小规模应用，其内存占用和启动时间与运行在HotSpot上相比有5倍到50倍的下降。

## 提前编译的优劣得失

第一条:这是传统的提前编译应用形式，它在Java中存在的价值直指即时编译的最大弱点：==即时编译要占用程序运行时间和运算资源。==

> 在编译过程中最耗时的优化措施之一是通过==“过程间分析”==（Inter-Procedural Analysis，IPA，也经常被称为全程序分析，即Whole Program Analysis）来 获得诸如某个程序点上某个变量的值是否一定为常量、某段代码块是否永远不可能被使用、在某个点 调用的某个虚方法是否只能有单一版本等的分析结论。
>
> 这些信息对生成高质量的优化代码有着极为巨 大的价值，但是要精确（譬如对流敏感、对路径敏感、对上下文敏感、对字段敏感）得到这些信息， 必须在全程序范围内做大量极耗时的计算工作，目前所有常见的Java虚拟机对过程间分析的支持都相当有限，要么借助大规模的方法内联来打通方法间的隔阂，以过程内分析（Intra-Procedural Analysis， 只考虑过程内部语句，不考虑过程调用的分析）来模拟过程间分析的部分效果；要么借助可假设的激进优化，不求得到精确的结果，只求按照最可能的状况来优化，有问题再退回来解析执行。
>
> 但如果是在程序运行之前进行的静态编译，这些耗时的优化就可以放心大胆地进行了，譬如Graal VM中的Substrate VM，在创建本地镜像的时候，就会采取许多原本在HotSpot即时编译中并不会做的全程序优化措施以获得更好的运行时性能，反正做镜像阶段慢一点并没有什么大影响。
>
> 但提前编译需要耗费大量的时间和资源,从Android 7.0版本起重新启用了解释执行和即时编译,此时的即时编译大部分的C1等级的事情,减少资源消耗（但这已与Dalvik无关，它彻底凉透了,从优化过程到优化效率全面落后），等空闲时系统再在后台自动进行提前编译。 

第二条路径，本质是==给即时编译器做缓存加速==，去改善Java程序的启动时间，以及需要一段时间预热后才能到达最高性能的问题。这种提前编译被称为==动态提前编译（DynamicAOT）==或者索性就大大方方地直接叫==即时编译缓存（JIT Caching）。==

这是一个基于Graal编译器实现的新工具，==目的是让用户可以针对目标机器，为应用程序进行提前编译(一些基本必用到的类库,如java.base)。==HotSpot运行时可以直接加载这些编译的结果，实现加快程序启动速度，减少程序达到全速运行状态所需时间的目的。

**Jaotc做的提前编译属于本节开头所说的“第二条分支”，即做即时编译的缓存；而Substrate VM则是选择的“第一条分支”，做的是传统的静态提前编译**（热部署可能原理就是基于这个，若是即时编译的话，没有相应的字节码，不然就要重新调用javac生成字节码）

**即时编译器相对于提前编译器的天然优势**

> ==性能分析制导优化（Profile-Guided Optimization，PGO）。==
>
> 在解释器或者客户端编译器运行过程中，会不断收集性能监控信息，譬如某个程序点抽象类通常会是什么实际类型、条件判断通常会走哪条分支、方法调用通常会选择哪个版本、循环通常会进行多少次等
>
> **这些数据一般在静态分析时是无法得到的，或者不可能存在确定且唯一的解，最多只能依照一些启发性的条件去进行猜测。**但在动态运行时却能看出它们具有非常明显的偏好性。 
>
> 如果一个条件分支的某一条路径执行特别频繁，而其他路径鲜有问津，那就可以把热的代码集中放到一起，集中优化和分配更好的资源（分支预测、寄存器、缓存等）给它。 
>
> ==激进预测性优化（Aggressive Speculative Optimization）==
>
> 静态优化无论如何都必须保证优化后所有的程序外部可见影响（不仅仅是执行结果） 与优化前是等效的，不然优化之后会导致程序报错或者结果不对，若出现这种情况，则速度再快也是 没有价值的。
>
> 然而，相对于提前编译来说，即时编译的策略就可以不必这样保守，如果性能监控信息能够支持它做出一些正确的可能性很大但无法保证绝对正确的预测判断，就已经可以大胆地按照高概率的假设进行优化，万一真的走到罕见分支上，大不了退回到低级编译器甚至解释器上去执行，并不 会出现无法挽救的后果。
>
> 只要出错概率足够低，这样的优化往往能够大幅度降低目标程序的复杂度，输出运行速度非常高的代码。
>
> 譬如在Java语言中，默认方法都是虚方法调用，部分C、C++程序员（甚至一些老旧教材）会说虚方法是不能内联的，但如果Java虚拟机真的遇到虚方法就去查虚表而不做内联的话，Java技术可能就已经因性能问题而被淘汰很多年了。
>
> ==链接时优化（Link-Time Optimization，LTO）==
>
> Java语言天生就是动态链接的，一个个Class文件在运行期被加载到虚拟机内存当中，然后在即时编译器里产生优化后的本地代码，这类事情 在Java程序员眼里看起来毫无违和之处。
>
> 但如果类似的场景出现在使用提前编译的语言和程序上，譬如C、C++的程序要调用某个动态链接库的某个方法，就会出现很明显的边界隔阂，还难以优化。这是因为主程序与动态链接库的代码在它们编译时是完全独立的，两者各自编译、优化自己的代码。
>
> 这些代码的作者、编译的时间，以及编译器甚至很可能都是不同的，当出现跨链接库边界的调用时，那些理论上应该要做的优化——譬如做对调用方法的内联，就会执行起来相当的困难。
>
> 如果刚才说的虚方法内联让C、C++程序员理解还算比较能够接受的话（其实C++编译器也可以通过一些技巧来做到虚方 法内联），那这种跨越动态链接库的方法内联在他们眼里可能就近乎于离经叛道了（但实际上依然是可行的）。 



# 编译器优化技术 

![image-20200908105813595](F:\Typora数据储存\高级语言\JAVA\JAVA虚拟机简介.assets\image-20200908105813595.png)

![image-20200908105827635](F:\Typora数据储存\高级语言\JAVA\JAVA虚拟机简介.assets\image-20200908105827635.png)



# Native

* 所谓的native准确的说是借由虚拟机实现的JNI接口调用的操作系统提供的API
* JNI使得class中的ACC_NATIVE标至的方法能借由JNI类的实例转换为JNI规范（如全限定名）的c实现方法实例（已经由.lib在虚拟机初始化时加载或者借由已经加载的类库的load方法，用java等语言加入内存），该实例会调用本地方法栈中的方法（操作系统提供的API）

## .h、.cpp、.lib和.dll

.h头文件和.cpp是编译时必须的，lib是链接时需要的，dll是运行时需要的。

> .h:声明函数接口
>
> .cpp:c++语言实现的功能源码
>
> .lib :
>
> LIB有两种，一种是静态库，比如C-Runtime库，这种LIB中有函数的实现代码，一般用在静态连编上，它是将LIB中的代码加入目标模块(EXE或者DLL)文件中，所以链接好了之后，LIB文件就没有用了。
>
> 一种LIB是和DLL配合使用的，里面没有代码，代码在DLL中，这种LIB是用在静态调用DLL上的，所以起的作用也是链接作用，链接完成了，LIB也没用了。至于动态调用DLL的话，根本用不上LIB文件。 目标模块（EXE或者DLL）文件生成之后，就用不着LIB文件了。
>
> .dll:
>
> 动态链接库英文为**DLL，是Dynamic Link Library**的缩写。DLL是一个包含可由多个程序，同时使用的代码和数据的库。
>
> 当程序使用 DLL 时，具有以下的优点： 使用较少的资源，当多个程序使用同一个函数库时，DLL 可以减少在磁盘和物理内存中加载的代码的重复量(运行时需要的库是需要加入内存的)。

.h和.cpp编译后会生成.lib和.dll 或者 .dll 文件

我们的程序引用别的文件的函数，需要调用其头文件，但是头文件找到相应的实现有两种方式，一种是同个项目目录下的其他cpp文件（公用性差），一种是链接时的lib文件（静态，lib中自己有实现代码），一种是运行时的dll文件，一种是lib和dll 的结合（动态，lib放索引，dll为具体实现）

> 还要指定编译器链接相应的库文件。在IDE环境下，一般是一次指定所有用到的库文件，编译器自己寻找每个模块需要的库；在命令行编译环境下，需要指定每个模块调用的库。

一般不开源的系统是后面三种方式，因为可以做到接口开放，源码闭合

#### 静态链接库

静态链接库(Static Libary，以下简称“静态库”)，静态库是一个或者多个obj文件的打包，所以有人干脆把从obj文件生成lib的过程称为Archive，即合并到一起。比如你链接一个静态库，如果其中有错，它会准确的找到是哪个obj有错，即静态lib只是壳子，但是静态库本身就包含了实际执行代码、符号表等等。

如果采用静态链接库，在链接的时候会将lib链接到目标代码中,结果便是lib 中的指令都全部被直接包含在最终生成的 EXE 文件中了。

这个lib文件是静态编译出来的，索引和实现都在其中。

静态编译的lib文件有好处：给用户安装时就不需要再挂动态库了。但也有缺点，就是导致应用程序比较大，而且失去了动态库的灵活性，在版本升级时，同时要发布新的应用程序才行。

#### 动态链接库(DLL)

.dll + .lib : 导入库形式，在动态库的情况下，有两个文件，而一个是引入库（.LIB）文件，一个是DLL文件，引入库文件包含被DLL导出的函数的名称和位置，DLL包含实际的函数和数据，应用程序使用LIB文件链接到所需要使用的DLL文件，库中的函数和数据并不复制到可执行文件中，因此在应用程序的可执行文件中，存放的不是被调用的函数代码，而是DLL中所要调用的函数的内存地址，这样当一个或多个应用程序运行是再把程序代码和被调用的函数代码链接起来，从而节省了内存资源。

从上面的说明可以看出，DLL和.LIB文件必须随应用程序一起发行，否则应用程序将会产生错误。

.dll形式： 单独的可执行文件形式，因为没有lib 的静态载入，需要自己手动载入，LoadLibary调入DLL文件，然后再手工GetProcAddress获得对应函数了，若是java 会调用System的LoadLibary，但是也是调用JVM中对于操作系统的接口，使用操作系统的LoadLibary等方法真正的将.dll读入内存，再调用生成的相应函数。

.dll+ .lib和.dll本质上是一样的，只是前者一般用于通用库的预设置，是的我们通过lib直接能查询到.dll文件，不用我们自己去查询，虽会消耗一部分性能，但是实用性很大。.dll 每一个需要到的文件都需自己调用加载命令，容易出错与浪费较多时间（但是我们测试时却可以很快的看出功能实现情况，而且更灵活地调用）

## JNI

JNI是Java Native Interface的缩写，通过使用 [Java](https://baike.baidu.com/item/Java/85979)本地接口书写程序，可以确保代码在不同的平台上方便移植，它允许Java代码和其他语言写的代码进行交互。

java生成符合JNI规范的C接口文件（头文件）：

1. 编写带有native声明的方法的java类

2. 使用[javac](https://baike.baidu.com/item/javac)命令编译所编写的java类

3. 然后使用javah + java类名生成扩展名为h的头文件

4. 使用C/C++实现本地方法

5. 将C/C++编写的文件生成动态连接库 （linux gcc windows 可以用VS）

编写范例：https://blog.csdn.net/wzgbgz/article/details/82979728

生成的.h的样例：

```cpp
/* DO NOT EDIT THIS FILE - it is machine generated */
#include "jni.h"
/* Header for class NativeDemo */
 
#ifndef _Included_NativeDemo
#define _Included_NativeDemo
#ifdef __cplusplus
extern "C" {
#endif
/*
 * Class:     NativeDemo
 * Method:    sayHello
 * Signature: ()V
 */
JNIEXPORT void JNICALL Java_NativeDemo_sayHello
  (JNIEnv *, jobject);
 
#ifdef __cplusplus
}
#endif
#endif
```

> “jni.h” 是必须要导入的，因为JNIEXPORT等都需要他的支持才行,而且有些方法中需要借助里面的函数。
>
> Java_NativeDemo_sayHello这样的规范命名是生成的.dll在被操作系统dlopen读取入内存时返回的handle能经由dlsym截取出正确的函数名，他可能将xxx.dll全都加载入内存，放入一个handle或者一个handle集合中，这时就需要包的全限定类名来确定到底获取的是handle中的哪个方法了

### **JNIEnv** **，jobject** **，jclass**

**1. JNIEnv**类实际代表了Java环境，通过这个JNIEnv 指针，就可以对Java端的代码进行操作。例如，创建Java类的对象，调用Java对象的方法，获取Java对象的属性等等，JNIEnv的指针会被JNI传入到本地方法的实现两数中來对Java端的代码进行操作。

JNIEnv类中有很多函数用可以用如下所示其中：TYPE代表属性或者方法的类型（比如：int float double byte ......）

```cpp
1.NewObject/NewString/New<TYPE>Array
2.Get/Set<TYPE>Field
3.Get/SetStatic<TYPE>Field
4.Call<TYPE>Method/CallStatic<TYPE>Method等许许多多的函数
```

**2.** **jobject**代表了在java端调用本地c/c++代码的那个类的一个实例（对象）。在修改和调用java端的属性和方法的时候，用jobject 作为参数，代表了修改了jobject所对应的java端的对象的属性和方法

**3. jclass** : 为了能够在c/c++中使用java类,JNI.h头文件中专门定义了jclass类型来表示java中的Class类

JNIEvn中规定可以用以下几个函数来取得jclass

```
1.jclass FindClass(const char* clsName) ;
2.jclass GetObjectClass(jobject obj);
3.jclass GetSuperClass(jclass obj);
```

## JNI原理

我们编译xxx.h和xxx.cpp生成了dll文件，运行java文件JNI会帮我们调用dll中的方法， 但是java对象是如何具体调用他的我们不清楚

我们自己实现的dll需要大概如下的模板：

Test.java

```java
package hackooo;
public class Test{
        static{
            	// java层调用.dll文件进入内存，但是底层仍是由虚拟机调用JNI用C实现对操作系统的提供的接口加载入内存
                System.loadLibrary("bridge");
        }
        public native int nativeAdd(int x,int y);
        public int add(int x,int y){
                return x+y;
        }
        public static void main(String[] args){
                Test obj = new Test();
                System.out.printf("%d\n",obj.nativeAdd(2012,3));
                System.out.printf("%d\n",obj.add(2012,3));
        }
}
```

我们需要先看到System.loadLibrary("bridge")的作用

```java
@CallerSensitive
public static void loadLibrary(String libname) {
    // Runtime类是Application进程的建立后，用来查看JVM当前状态和控制JVM行为的类
    // Runtime是单例模式，且只能用静态getRuntime获取，不能实例化
    // 其中load是加载动态链接库的绝对路径方法
    // loadLibrary是读取相对路径的，动态链接库需要在java.library.path中，一般为系统path，也可以设置启动项的 -VMoption
    // 通过ClassLoader.loadLibrary0(fromClass, filename, true);中的第三个参数判断
    Runtime.getRuntime().loadLibrary0(Reflection.getCallerClass(), libname);
}
```

java.lang.Runtime

```java
 @CallerSensitive
    public void loadLibrary(String libname) {
        loadLibrary0(Reflection.getCallerClass(), libname);
    }

    synchronized void loadLibrary0(Class<?> fromClass, String libname) {
        SecurityManager security = System.getSecurityManager();
        if (security != null) {
            security.checkLink(libname);
        }
        if (libname.indexOf((int)File.separatorChar) != -1) {
            throw new UnsatisfiedLinkError(
    "Directory separator should not appear in library name: " + libname);
        }
        // false，调用相对路径
        ClassLoader.loadLibrary(fromClass, libname, false);
    }
```

 java.lang.ClassLoader

```java
static void loadLibrary(Class<?> fromClass, String name,
                        boolean isAbsolute) {
    // 通过方法区中的class类找到相应的类加载器
    ClassLoader loader =
        (fromClass == null) ? null : fromClass.getClassLoader();
    if (sys_paths == null) {
        // 加载的绝对路径
        // 系统环境变量
        usr_paths = initializePath("java.library.path");
        // 我们启动时加入的依赖项
        sys_paths = initializePath("sun.boot.library.path");
    }
    if (isAbsolute) {
        // 若是决定路径，调用真正的执行方法
        if (loadLibrary0(fromClass, new File(name))) {
            return;
        }
        throw new UnsatisfiedLinkError("Can't load library: " + name);
    }
    if (loader != null) {
        // 判断当前类加载器及其双亲是否有该lib的类信息
        String libfilename = loader.findLibrary(name);
        if (libfilename != null) {
            File libfile = new File(libfilename);
            if (!libfile.isAbsolute()) {
                throw new UnsatisfiedLinkError(
"ClassLoader.findLibrary failed to return an absolute path: " + libfilename);
            }
            if (loadLibrary0(fromClass, libfile)) {
                return;
            }
            throw new UnsatisfiedLinkError("Can't load " + libfilename);
        }
    }
    // 查询sys_paths路径下是否有.dll文件
    for (int i = 0 ; i < sys_paths.length ; i++) {
        File libfile = new File(sys_paths[i], System.mapLibraryName(name));
        if (loadLibrary0(fromClass, libfile)) {
            return;
        }
        libfile = ClassLoaderHelper.mapAlternativeName(libfile);
        if (libfile != null && loadLibrary0(fromClass, libfile)) {
            return;
        }
    }
    // 查询usr_paths路径下是否有.dll文件
    if (loader != null) {
        for (int i = 0 ; i < usr_paths.length ; i++) {
            File libfile = new File(usr_paths[i],
                                    System.mapLibraryName(name));
            if (loadLibrary0(fromClass, libfile)) {
                return;
            }
            libfile = ClassLoaderHelper.mapAlternativeName(libfile);
            if (libfile != null && loadLibrary0(fromClass, libfile)) {
                return;
            }
        }
    }
    // Oops, it failed
    throw new UnsatisfiedLinkError("no " + name + " in java.library.path");
}
```

```java
private static boolean loadLibrary0(Class<?> fromClass, final File file) {
    // Check to see if we're attempting to access a static library
    // 查看是否调用的lib为静态链接库
    String name = findBuiltinLib(file.getName());
    boolean isBuiltin = (name != null);
    // 若是静态链接库则跳过，否则获取file的路径
    if (!isBuiltin) {
        boolean exists = AccessController.doPrivileged(
            new PrivilegedAction<Object>() {
                public Object run() {
                    return file.exists() ? Boolean.TRUE : null;
                }})
            != null;
        if (!exists) {
            return false;
        }
        try {
            name = file.getCanonicalPath();
        } catch (IOException e) {
            return false;
        }
    }
    ClassLoader loader =
        (fromClass == null) ? null : fromClass.getClassLoader();
    // 
    Vector<NativeLibrary> libs =
        loader != null ? loader.nativeLibraries : systemNativeLibraries;
    synchronized (libs) {
        int size = libs.size();
        for (int i = 0; i < size; i++) {
            NativeLibrary lib = libs.elementAt(i);
            if (name.equals(lib.name)) {
                return true;
            }
        }

        synchronized (loadedLibraryNames) {
            if (loadedLibraryNames.contains(name)) {
                throw new UnsatisfiedLinkError
                    ("Native Library " +
                     name +
                     " already loaded in another classloader");
            }
            /* If the library is being loaded (must be by the same thread,
             * because Runtime.load and Runtime.loadLibrary are
             * synchronous). The reason is can occur is that the JNI_OnLoad
             * function can cause another loadLibrary invocation.
             *
             * Thus we can use a static stack to hold the list of libraries
             * we are loading.
             *
             * If there is a pending load operation for the library, we
             * immediately return success; otherwise, we raise
             * UnsatisfiedLinkError.
             */
            //如果我们突然发现library已经被加载，可能是我们执行一半被挂起了或者其他线程在synchronized前也调用了该classLoader，执行JNI_OnLoad又一次调用了启用了同个线程中过的另一个loadLibrary方法，加载了我们的文件
            //之所以是同个线程中的，因为run一个application对应一个java.exe/javaw.extin进程，一个JVM实例，一个Runtime实例，且其是实现了synchronized的。
            // 查看此时nativeLibraryContext中存储了什么
            int n = nativeLibraryContext.size();
            for (int i = 0; i < n; i++) {
                NativeLibrary lib = nativeLibraryContext.elementAt(i);
                if (name.equals(lib.name)) {
                    if (loader == lib.fromClass.getClassLoader()) {
                        return true;
                    } else {
                        throw new UnsatisfiedLinkError
                            ("Native Library " +
                             name +
                             " is being loaded in another classloader");
                    }
                }
            }
            NativeLibrary lib = new NativeLibrary(fromClass, name, isBuiltin);
            nativeLibraryContext.push(lib);
            try {
                // 尝试加载
                lib.load(name, isBuiltin);
            } finally {
                nativeLibraryContext.pop();
            }
            if (lib.loaded) {
                // 加入已加载Vetor中
                loadedLibraryNames.addElement(name);
                libs.addElement(lib);
                return true;
            }
            return false;
        }
    }
}
```

```java
native void load(String name, boolean isBuiltin);
```

最后的load是虚拟机中实现的方法（用来加载我们自己要加入的.dll的），我们通过调用他来调用操作系统的API来真正将其放入内存

而那些已经编译好的库函数，虚拟机初始化时就调用LoadLibrary（Linux是dlopen）等操作系统API（本地方法栈）加入了内存中

(windows的)LoadLibrary与dlopen原理相似，若是还未加载过的dll，会调用相关方法，windows会用DLL_PROCESS_ATTACH调用[DllMain](https://docs.microsoft.com/en-us/windows/desktop/Dlls/dllmain) 方法，若是成功则返回一个handle对象可以调用**GetProcAddress**（linux 为dlsym）获得函数进行使用。

load是在jVM初始化就加载了lib文件，通过jvm.h就能通过该lib找到调用的函数的入口，调用相应的.dll二进制文件

LoadLibrary是操作系统初始化时加载的windows.lib加载入内存的，我们需要调用windows.h文件，调用该函数的.dll入内存（延迟加载的话）

我们java中的native方法的实现和到此时load便接轨了，我们来看看native如何被解析的

编译：

```shell
javac hackooo/Test.java
javap -verbose hackooo.Test
```

Test.class:

```java
  public native int nativeAdd(int, int);
    flags: ACC_PUBLIC, ACC_NATIVE

  public int add(int, int);
    flags: ACC_PUBLIC
    Code:
      stack=2, locals=3, args_size=3
         0: iload_1       
         1: iload_2       
         2: iadd          
         3: ireturn       
      LineNumberTable:
        line 8: 0
```

普通的“add”方法是直接把字节码放到code属性表中，而native方法，与普通的方法通过一个标志“ACC_NATIVE”区分开来。java在执行普通的方法调用的时候，可以通过找方法表，再找到相应的code属性表，最终解释执行代码，那么，对于native方法，在class文件中，并没有体现native代码在哪里，只有一个“ACC_NATIVE”的标识，那么在执行的时候改怎么找到动态链接库的代码呢？

​     

到了这一步，我们就需要开始钻研JVM到底运行逻辑是什么了

刚开始时，我们通过javac 编译一个xxx.java 成一个字节码文件,javac进行前端编译时包括了词法分析,语法分析生成抽象语法树,在生成字节码指令流(编译期)后交由解释器/即时编译器进行解释/编译优化(运行期)                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     

然后用java xxx 命令在操作系统中初始化一个进程,这个进程为我们分配了一块内存空间,我们开始新建一个JVM(或者说是JRE)在该内存中并进行初始化(该步骤是操作系统通过java这个命令(其为windows的一个脚本),调用其他系统命令将我们预先编译好的二进制指令集放入CPU运行生成)

虚拟机的实例创建好后,java脚本的最后一条命令便是执行JVM中的main方法,jvm会帮我们创建BoostrapClassLoader,其是用C实现的,并不符合加入class区后的实例化流程,因此我们的java代码并不能引用他,创建完他后,BoostrapClassLoader会帮我们将一些jdk的核心class文件通过它加载入方法区中,紧接着JVM会通过launcher的c实现通过JNI(还需看源码确定是不是这样,JNI是JVM初始化时创建的?不在JVM运行时区域中,在执行引擎中),依据导入java实现的Launcher的class信息通过帮我们创建sun.misc.Launcher对象并初始化(单例),他的创建还会伴随着ExtClassLoader的初始化和appClassLoader的创建(三层和双亲),这里涉及类的加载过程.

> 更好的了解java实现的ClassLoaderhttps://blog.csdn.net/briblue/article/details/54973413

接着,线程会默认调用APPClassLoader帮我们将命令中的 xxx参数的class装入方法区(之所以要通过classLoader来加载是为了只在需要时我们加载类,而不是全部加载,节约内存空间,而这里加载的class不止硬盘,只要是二进制字节流就可以),并为main函数在java栈中预留一个栈帧,经生成的后端编译器的实例进行字节码的解释执行优化和编译优化代替执行(后端编译器大部分既有解释器又有编译器参数设置,决定如何编译优化).

# 类的加载

从APPClassLader将class装入方法区开始,就是类的加载过程了

## 加载

(既可以由JVM本身加载入方法区,也可自定义的classLoder选取需要加载的class,通过JNI调用)

> 通过一个类的全限定类名来获取定义此类的二进制字节流
>
> 将这个字节流所代表的静态结构转化为方法区的运行时数据结构（此时除了常量池其他的如字段方法都放入了class文件所开辟的内存空间中，后面解析阶段的字面量变为直接引用便是从这里查询地址入口）
>
> 在内存（堆）中生成一个代表这个类的java.lang.Class对象（InstanceKlass和Jave mio）,作为方法区这个类的各种数据的访问入口（单例模式）
>
> 至于什么时候加载，除了遇到new、getstatic、putstatic、invokestatic四条指令时，必须立刻加载··到初始化完成

### 体系总览

在JVM内部定义了3种结构去描述一种类型 ：oop 、klass 和 handle 类。

![image-20201211150017204](F:\Typora数据储存\高级语言\JAVA\JVM简介.assets\image-20201211150017204.png)



### 两模型三维度

JVM使用 oop-klass 这种一分为二的模型描述一个 Java 类 ，虽然模型只有两种，但是其实从 3 个不同的维度对一个 Java 类进行了描述。侧重于描述 Java 类的实例数据的第一种模型 oop 主要为 Java 类生成一张 “实例数据视图”，从数据维度描述一个Java类实例对象中各个属性在运行期的值。而第二种模型 klass 则又分别从两个维度去描述一个 Java 类 ，第一个维度是 Java 类的“元信息视图”，另一个维度则是虚函数列表，或者叫作方法分发规则。元信息视图为JVM在运行期呈现Java类的“全息”数据结构信息，这是JVM在运行期得以动态反射出类信息的基础。

![image-20201210170426725](F:\Typora数据储存\高级语言\JAVA\JVM简介.assets\image-20201210170426725.png)

### OOP-KLASS

Hotspot 虚拟机在内部使用两组类来表示Java的对象和类。

- oop(ordinary  object  pointer)，用来描述对象实例信息。
- klass，用来描述 Java 类，是虚拟机内部Java类型结构的对等体 。

> https://zhuanlan.zhihu.com/p/51695160

![image-20201210105735239](F:\Typora数据储存\高级语言\JAVA\JVM简介.assets\image-20201210105735239.png)



#### oopDesc

在 Java 程序运行的过程中，每创建一个新的对象，在 JVM 内部就会相应地创建一个对应类型的 oop（普通对象指针） 对象。各种 oop 类的共同基类为 `oopDesc` 类。

在 JVM 内部，一个 Java 对象在内存中的布局可以连续分成两部分：对象头（instanceOopDesc） 和实例数据（成员变量）。

`instanceOopDesc` 对象头包含两部分信息：**Mark Word** 和 **元数据指针**(`Klass*`)：

```c++
class oopDesc{
    friend class VMStructs;
private:
	volatile markOop  _mark;
    union _metadata {
        Klass*      _klass;
        narrowKlass _compressed_klass;
    } _metadata;
    static BarrierSet* _bs;
}
```

> 1. **Mark Word**：markOop  _mark，它用于存储对象的运行时记录信息，如哈希值、GC 分代年龄(Age)、锁状态标志（偏向锁、轻量级锁、重量级锁）、线程持有的锁、偏向线程 ID、偏向时间戳等。
>
> 2. **元数据指针**：union _metadata，它是联合体，可以表示未压缩的 Klass 指针(`_klass`)和压缩的 Klass 指针。对应的 klass 指针指向一个存储类的元数据的 Klass 对象。
>
>    Java类的结构信息在编译期被编译为字节码格式，JVM则在运行期进一步解析字节码格式，从字节码二进制流中还原出一个Java在源码期间所定义的全部数据结构信息，JVM需要将解析出来结果保存到内存中，以便在运行期进行各种操作，例如反射，而_metadata便起到指针的作用，指向 Java 类的数据结构被解析后所保存的内存位置。
>
>    instanceOop 内部会有一个指针指向 instanceKlass ，其实这个指针便是 oopDesc 中所定义的一_metadata。klass 是 Java类型的对等体 ，而 Java 类型 ，便是 Java 编程语言中用于描述客观事物的数据结构，而数据结构包含一个客观事物的全部属性和行为 ，所以叫做 “类元”信息，这便是_metadata的本意。
>
> 3. **BarrierSet： **_bs： 用于内存屏障

#### oop

> ordinary object pointer（普通对象指针）

```c++
typedef class 	oopDesc*                    oop;
typedef class   instanceOopDesc*            instanceOop;
typedef class   arrayOopDesc*               arrayOop;
typedef class   objArrayOopDesc*            objArrayOop;
typedef class   typeArrayOopDesc*           typeArrayOop;
// 之后本来是由GC管理的有关java对象对应的元数据的oop对象，1.8之后klazz的实现都继承自MetaspaceObj，原先的以下的oop都会以成员变量（或指针）的形式存在于 klass 体系中。
typedef class 	methodOopDesc*				methodOop;
typedef class	methodDataOopDesc*			methodDataOop;
typedef class	markOopDesc*				markOop;
typedef class 	constMethodOopDesc* 		constMethodOop；
typedef class 	constantPoolOopDesc* 		constantPoolOop；
typedef class 	constantPoolCacheOopDesc* 	constantPoolCacheOop；
```

> klassOop -> Klass*
> klassKlass 不再需要
> methodOop -> Method*
> methodDataOop -> MethodData*
> constMethodOop -> ConstMethod*
> constantPoolOop -> ConstantPool*
> constantPoolCacheOop -> ConstantPoolCache*

1 ) Hotspot里的 oop 指啥

Hotspot里的oop 其实就是 GC 所托管的指针，每一个 oop 都是一种 xxxOopDesc*类型的指针。所有oopDesc及其子类（ 除神奇的 markOopDesc 外 ） 的实例都由 GC 所管理，这才是最最重要的，是 oop 区分 Hotspot 里所使用的其他指针类型的地方。

2）对象指针之前为何要冠以“普通”二字

对象指针从本质上而言就是一个指针，指向xxxOopDesc的指针也是普通得不能再普通的 指针，可是为何在 Hotspot 领域还要加一个“普通”来修饰？要回答这个问题，需要追溯到OOP（ 这里的OOP 是指面向对象编程 ）的鼻祖SmallTalk 语言。

SmallTalk语言里的对象也由 GC 来管理，但是 SmallTalk 里面的一些简单的值类型对象都会使用所谓的 “直接对象”的机制来实现，例如SmallTalk里面的整数类型。所谓 “直接对象”( immediate object） 就是并不在 GC 堆上分配对象实例，而是直接将实例内容存在对象指针里的对象。这样的指针也叫做 “带标记的指针”（tagged pointer）。

**这一点倒是与markOopDesc类型如出一辙，因为 markOopDesc 也是将整数值直接存储在指针里面 ，这个指针实际上并无“指向”内存的功能。**

所以在SmallTalk的运行期 ，每当拿到一个对象指针时，都得先校验这个对象指针是一个直接对象还是一个真的指针？如果是真的指针，它就是一个“普通”的对象指针了。这样对象指针就有了“普通”与“不普通”之分。

所以，在Hotspot里面 ，oop 就是指一个真的指针，而 markOop 则是一个看起来像指针但实际上是藏在指针里的对象（数据）。这也正是 markOop 实例不受 GC 托管的原因，因为只要出了函数作用域，指针变量就会直接被从堆枝上释放掉了不需要垃圾回收了。

![image-20201211153900603](F:\Typora数据储存\高级语言\JAVA\JVM简介.assets\image-20201211153900603.png)

#### **Klass**

```c++
//所有klass共同基类
class 	Klass;
//JVM内部与Java类对等的结构
class   InstanceKlass;
//描述java.lang.Class实例
class   InstanceMirrorKlass;
//描述java.lang.ref.Reference子类
class   InstanceRefKlass;
//Java类方法
class 	methodKlass;
//Java类方法对应的字节码指令
class 	constMethodKlass;
class	methodDataKlass;
//klass链路的末端
class 	klassKlass;
class 	instanceKlass;
//标识Java数组
class   arrayKlass;
//标识引用类型数组
class   objArrayKlass;
//标识基本类型数组
class   typeArrayKlass;
//.class文件中的常量池
class	constantPoolKlass;
//常量池缓存
class	constantPoolCacheKlass;
class	compliedICHolderKlass;
```

> jdk 1.8 后Klss对象的继承关系：Klass 对象的继承关系：`xxxKlass <:< Klass <:< Metadata <:< MetaspaceObj`

klass主要提供下面2种能力 ：

- ©klass提供一个与 Java 类对等的 C++类型描述。
- ©klass提供虚拟机内部的函数分发机制 。

每个Java对象的对象头里，_klass字段会指向一个VM内部用来记录类的元数据用的**InstanceKlass对象**；InstanceKlass里有个_java_mirror字段，指向该类所对应的Java镜像——java.lang.Class实例。HotSpot VM会给Class对象注入一个隐藏字段“klass”，用于指回到其对应的InstanceKlass对象。这样，klass与mirror之间就有双向引用，可以来回导航。
**这个模型里，java.lang.Class实例并不负责记录真正的类元数据，而只是对VM内部的InstanceKlass对象的一个包装供Java的反射访问用，为了以java的形式更好的使用InstanceKlass对象。**

> 在JDK 6及之前的HotSpot VM里，静态字段依附在InstanceKlass对象的末尾；而在JDK 7开始的HotSpot VM里，静态字段依附在java.lang.Class对象的末尾。

![image-20201211154159528](F:\Typora数据储存\高级语言\JAVA\JVM简介.assets\image-20201211154159528.png)



#### handle

handle封装了oop，由于通过oop可以拿到 klass ，而 klass 是对 Java 类数据结构和方法的描述 ，因此 handle 间接封装了 klass。JVM内部使用一个 table 来存储 oop 指针。

> 如果说oop是对普通对象的直接引用，那么 handle 就是对普通对象的一种间接引用，中间隔了一层。但是JVM内部为何要使用这种间接引用呢？答案是，这完全是为GC考虑。具体表现在2个地方 ：
>
> 通过handle，能够让 GC 知道其内部代码都有哪些地方持有 GC 所管理的对象的引用，这只需要扫描 handle 所对应的 table ，这样 JVM 便无须关注其内部到底哪些地方持有对普通对象的引用。
>
> 在GC过程中如果发生了对象移动（例如从新生代移到了老年代），那么JVM的内部引用无须跟着更改为被移动对象的新地址，JVM 只需要更改 handle table 里对应的指针即可 。

当然实际的handle作为对 Java 类方法的访问的包装，远不止上面所描述的这么简单。这里涉及 Java 类的类继承和接口继承的话题，在 C++领域，类的继承和多态性最终通过vptr（虚函数表）来实现。在klass内部，记录了每一个类的vptr信息，具体而言分为两部分来描述。

**1.vtable虚函数表**

vtable中存放 Java 类中非静态和非 private 的方法入口，JVM调用 Java 类的方法 （非静态和非 private）时，最终会访问vtable，找到对应的方法入口。

**2.itable 接口函数表**

itable中存放 Java 类所实现的接口的类方法。同样，JVM调用接口方法时，最终会访问itable,找到对应的接口方法入口。

不过要注意，vtable和itable 里面存放的并不是Java类方法和接口方法的直接入口，而是指向了 Method 对象入口，JVM会通过Method最终拿到真正的 Java 类方法入口，得到方法所对应的字节码／二进制机器码并执行。当然，对于被JIT进行动态编译后的方法，JVM最终拿到的是其对应的被编译后的本地方法的入口。

![image-20201211154322156](F:\Typora数据储存\高级语言\JAVA\JVM简介.assets\image-20201211154322156.png)

> 这里有个问题，前面不是一直在说handle是对 oop 的直接封装和对 klass 的间接封装吗，为什么这里却分别给 oop 和 klass 定义了 2 套不同的 handle 体系呢？这给人的感觉好像是，封 装 oop 的 handle 和封装 klass 的 handle 并不是同一个 handle ，既然不是同一个handle ，那么通 过封装 oop 的handle 还怎么去得到所对应的 klass 信息呢？
>
> 其实这正是常常容易使人迷惑的地方。在JVM中，使用oop-klass这种一分为二的模型去描述 Java 类以及 只叫内部的特殊类群体，为此JVM内部特定义了各种oop和 klass类型。但是，对于每一个oop，其实都是一个 C++类型，也即 klass；而对于每一个 klass 所对应的 class ，在JVM内部又都会被封装成 oop。只在具体描述一个类型时，会使用 oop 去存储这个类型的实例数据，并使用 klass 去存储这个类型的元数据和虚方法表。
>
> 而当一个类型完成其生命周期后，JVM会触发 GC 去回收，在回收时，既要回收一个类实例所对应的实例数据 oop , 也要回收其所对应的元数据和虚方法表（当然，两者并不是同时回收，一个是堆区的垃圾回收， 一个是永久区的垃圾回收：虽然方法区jvm规范中并没强制要求回收，但hotspot是如此实现的）。
>
> **为了让 GC 既能回收 oop 也能回收 klass，因此 oop 本身被封装成了 oop ，而 klass 也被封装成 oop。而使用时仍是通过oop-handle体系调用klass的，但1.8后永久区的内容如klass、method等放入了Metaspace即原空间，使用时oop中的元数据klass指针直接指向元空间的klass内存地址，klass有method等指针导向原空间方法等的入口地址，回收时由操作系统决定，因此不需要再将klass封装成oop形式，也不需要相应的handle-klass体系的标记来完成永久区的回收了，这也是永久代消失，方法区更加变成逻辑层的原因**
>
> 而恰好将描述类实例的 oop 全都定义成类名以 oop 结尾的类，并将描述类结构和方法信息的 klass 全都定义成类名以 klass 结尾的类 ，而描述类信息的模型恰巧也叫作 oop-klass，与类名存在重合，这就导致了很多人的疑惑，这些疑惑完全是因为叫法上的重合而产生。

## 验证

(java源码本身的编译是相对安全的,但是字节码的内容可能会加入恶意代码,因此需要验证)

> 文件格式验证(字节流的各个部分划分是否符合规范)
>
> 元数据验证(对元数据信息中的数据类型检验)
>
> 字节码校验(对方法体中的内容进行校验,较为复杂耗时,jdk6后可以将权重部分移向了javac)
>
> 符号引用校验(在解析阶段同时进行)

## 准备

> 正式为类中定义的变量（即静态变量，被static修饰的变量）分配内存并设置类变量JVM自身默认初始值的阶段。从概念上讲，这些变量所使用的内存都应当在方法区中进行分配，但需要注意的是方法区本身是一个逻辑层面的概念，其实现在不同的版本，不同的虚拟机上可能分布在不同的内存空间。**注意此时只是分配静态成员变量的存储空间，不包含实例成员变量**
>

## 解析

> 解析阶段是java虚拟机将常量池内的符号引用（存放在方法区的常量池中）替换为直接引用（我们当初在堆中创建的Class对象的具体内存地址）的过程，即将我们最初的ACC_NATIVE等字面量进替换。
>
> 加载阶段只是将字节码按表静态翻译成字节码对应的表示按约定大小划分入内存中，常量池中只存放字面量并被翻译的方法表中的方法引用作为所存储内存的部分信息保存，只有在解析阶段才专门将常量池中的字符引用依据Class对象中分出的各个内存中预先存储的部分信息匹配返回地址换成直接引用。放入运行时常量池直接调用
>
> * 至jdk13常量池中存有 17类常量表，每一个tag用u1长度（两个字节）代表一类常量表，对应的常量表中规定了后面需要读取多少字节分别，分为几个部分代表哪些东西。
>
> 我们需要了解一份class文件大概有哪些信息（xx信息便是xx表集合）
>
> ![image-20200918005655799](F:\Typora数据储存\高级语言\JAVA\JVM简介.assets\image-20200918005655799.png)
>
> 解析可以发生在任何时间，包括运行时再被确定也是可能的，只要求了在执行anewarray，checkcast, getfield, getstatic, instanceof, invokedynamic, invokeinterface, invokespecial, 等17个用于操作符号引用的字节码指令之前，需要对他们所使用的符号引用进行解析
>
> 符号引用可以将第一次的解析结果进行缓存，如在运行时直接引用常量池中的记录。不过对于invokedynamic指令，上面的规则就不使用了，它要求程序在解释器基于栈或者编译器基于寄存器解读方法时实际运行到这条指令时，解析动作才能进行。
>
> 解析动作主要针对类或接口、字段、类方法、接口方法、方法类型、方法句柄和调用点限定符这七类
>
> 分别对应CONSTANT_Class_info、CONSTANT_Fieldref_info、CONSTANT_Methodref_info、CONSTANT_InterfaceMethodref_info，这前四种基本都是在解析时便可以替换为直接引用
>
> CONSTANT_MethodType_info、CONSTANT_MethodHandle_info、CONSTANT_Dynamic_info和CONSTANT_InvokeDynamic_info
>
> 这四种于动态语言联系紧密，为此我们需要明白解析与分派的区别
>
> 先从前四种开始说起
>
> 我们前面的8个符号引用，分别有自己单独的常量表，其中记录了去往哪查询自己的代码的索引值，去调用字段表和方法表中对于字段和方法的定义
>
> 编译器通过方法区预存的常量表解读了class文件中的字节码中的各个常量，创建了常量池，但是常量池中的存储仍是依据字面量的索引，由字面量项保存了一些字面量实现的信息，并没有真正的内存保留他，而我们的字段表，方法表等依据name_index引用常量池中的常量项，但他们只保存声明的部分，至于初始化和方法体的实现，常常是放置在code中，code一般会在字段表或方法表的后面，在加载阶段放入方法区时分配内存
>
> 而我们的解析的作用就是将如CONSTANT_Class_info的字符引用找到字面量记录的全限定类名交由classLoader加载（加载阶段传输时为字段表、方法表分配内存空间，将这个字节流所代表的静态结构转化为方法区的运行时数据结构，放入运行时常量池中了）
>
> 将字段表中的对于CONSTANT_Fieldref_info存储的索引的字面量的读取出的简单名称和字段描述符去匹配class_index中由类加载器加载出来的类是否有相应字段，有则返回直接引用
>
> 接口方法解析也是与字段大致一样的查询逻辑，但是都只是找到了方法的入口，并非实现了其中的代码 ，这时候我们可以思考一下native的直接引用的地址是哪里呢，个人认为此时已经是相应的javah实现的.h文件的实现cpp了（还不知道如何调试查看）
>
> **方法解析不仅是方法表中常量项的解析，其中code属性中字节码指令中对于方法的调用的那部分的解析需要依据字节码指令的类型进行**
>
> 方法的调用并不如同字段、方法等的入口等将字符引用换成直接引用保存一个入口就可，而是依据code中的字节码指令的类型进行解析，使得引用时可以直接调用指令进行方法的执行，其中jvm若是解释执行，则是依据操作栈来进行字节码指令的运作的通过调用操作系统对CPU操作的API来实现功能，若是基于编译后实现的寄存器的，则是直接交由寄存器硬件实现的指令集执行（如x86）.
>
> 而如何执行code中的指令，也是依据字节码指令来的：
>
> 1. invokestatic 用于调用静态方法
> 2. invokespecial 用于调用实例构造器<init>()方法、私有方法、父类中的方法
> 3. invokevirtual 用于调用所有的虚方法
> 4. invokeinterface 用于调用接口方法，会在运行时再确定一个实现该接口的对象
> 5. invokedynamic  现在运行时动态解析出调用点限定符所引用的方法，然后在执行该方法
>
> 其中invokestatic和invokespecial是在符合运行时指令集是固定的（包括1和2的四种和final，final是用invokevirtual实现，但是因为不可修改），一个方法的多个语句，在常量池中为多个常量，其中每个方法或字段开始都是字面量，解析都会换到class的对应方法的内存地址，其中，方法的实现都在其结构最后的code集合中，code集合每一条都为相应的指令集，其中对方法的调用会标志为相应的静态类型字节码指令，在字节码指令的参数中便直接固定了跳转的常量池项。
>
> 而其他方法称为虚方法（如普通方法，又重写了，不像前面的静态类型便是自己class中的方法地址直接返回，而是需要依据上面的指针类型进行运行时解析过程，如invokevirtual的解析便依据操作数栈的元素指向的对象的实际类型中查找与常量中的描述符与简单名称都相符的方法，再权限校验，若是不通过或没有则继续往父类搜索和验证的过程，这是在执行到运行时常量池的该方法，方法的code集合中有invokevirtual指令才开始依据栈顶的元素的实际类型，在相应的class中查询匹配，在匹配权限校验通过后再返回直接引用到字节码指令参数中）
>
> 而虚方法的调用需要依靠分派调用（重载与重写）
>
> 1. 静态分派（重载）：编译后的class文件的code中的方法调用已在指令后参数记录了调用的常量项的编号，而该常数项因为是静态会更早解析，所以此时指向的该常数项是直接引用
>
> > 1. 对象作为形参，重载形参调用引用类型（静态类型）
> > 2. 重载时，实参不符合形参类型，形参匹配优先级
> > 3. 调用的方法只能为引用类型有的方法
> > 4. 使用的方法为实际实现的类型的方法
>
> ```java
> public class OverLode_Test {
>     static abstract class Human{
> 
>     }
> 
>     static class Man extends Human{
>         public String show(Man obj){
>             return ("A and A");
>         }
>         public String show(Man_3 obj){
>             return ("A and D");
>         }
> 
>     }
> 
>     static class Man_1 extends Man{
>         public String show(Man obj){
>             return ("B and A");
>         }
>         public String show(Man_1 obj){
>             return ("B and B");
>         }
>     }
>     static class Man_2 extends Man{
> 
>     }
>     static class Man_3 extends Man_1{
> 
>     }
> 
>     static class Women extends Human{
> 
>     }
> 
>     public void sayHello(Human guy){
>         System.out.println("hello,guy!");
>     }
>     public void sayHello(Man guy){
>         System.out.println("hello,man!");
>     }
>     public void sayHello(Women guy){
>         System.out.println("hello,women!");
>     }
> 
>     public static void main(String[] args){
>         // 静态分派中，重载部分对于形参静态类型和实际类型的处理方式
>         // 对象的静态类型和实际类型不一致
>         Human man = new Man();
>         Human women = new Women();
>         OverLode_Test sr = new OverLode_Test();
>         // overload时若形参的静态类型（引用类型）为父类，则编译时静态分派（重载）的无论实际类型如何都是选取静态类型的重载方法，
>         // 但对静态类型其中方法的引用，实际类型种若重写了，则动态分派为重写的方法.z
>         // 这样设计的目的是为了符合能调用哪些方法由引用类型决定，所以传入的对象的静态类型能使得不会调用引用类型没有的方法。
>         sr.sayHello(man);
>         sr.sayHello(women);
> 
>         //
>         // 知识点：
>         // 依赖静态类型的分派动作称为静态分派，表现有重载，并且重载时会匹配优先级
>         // 1. 能调用哪些方法，由引用类型决定;实际使用的方法由实际类型决定。
>         // 2. 重载方法的形参的对应静态类型的对象若是没有匹配项，会往上匹配其父类（接口）看引用类型的重载方法有无匹配，匹配则进入3，直至没有则报错（匹配优先级）
>         // 3. 匹配到引用类型的方法，方法的实现则需要依据实际内存对象类型决定，若重写则调用重写的，没有则调用引用类型的。
>         //（4.若是有Man man_3 = new Man_3();由于重载时静态分派（编译期）可知，则依据静态类型匹配为show(Man obj)）
>         Man man_0 = new Man_1();
>         // 静态类型与实际类型一致
>         Man_1 man_1 = new  Man_1();
>         Man_2 man_2 = new Man_2();
>         Man man_3 = new Man_3();
>         System.out.println(man_0.show(man_1));
>         System.out.println(man_0.show(man_2)); 
>         System.out.println(man_0.show(man_3));
>     }
> 
> }
> 
> //运行结果
> hello,guy!
> hello,guy!
> B and A
> B and A
> B and A
> ```
>
> 
>
> 1. 动态分派（重写）：也有指令与方法的常数项编号作为参数，但其会将此时操作数栈的栈顶的对象中的方法与常量的描述符与简单名称匹配，再在此时将符号引用改为对象方法的直接引用                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               
> 2. 单分派与多分派
>
> 为了提高动态分派效率，我们还专门在方法区中建立了虚方法表
>
> 其中无论静态还是非静态方法都存在方法区的运行时常量池中，是为了出于不同对象不需要重复为方法的相同指令集开辟重复的地址空间，而静态变量与局部变量等，便是在各个对象实例的堆的内存空间中（其中静态的放在Class对象中，至于Class对象的具体作用，便是对接自己的class的运行时常量池实现与不同的对象间的交接处），通过this区别调用的非静态方法的对象。

## 初始化

最后便是初始化，用<clinit>,<init>收敛初始化类变量等，<其中client 会经常调用<init>，准备阶段的初始化是系统变量的默认值，这里是我们自定义的>将运行权重转移到程序本身的code实现上      

> new 一个对象调用该对象类的 constructor 方法时才会执行init方法，而clinit是类构造器方法，也就是在jvm进行类**加载—–验证—-解析—–初始化**，中的初始化阶段jvm会调用clinit方法。因为init所对应的是oop所

> 初始化阶段就是执行类构造器<clinit>()方法的过程。<clinit>()方法是由编译器自动收集类中的所有类变量的赋值动作和静态代码块static{}中的语句合并产生的，其中编译器收集的顺序是由语句在源文件中出现的顺序所决定。
>
> 　　**类构造器<clinit>()与实例构造器<init>()不同，它不需要程序员进行显式调用，虚拟机会保证在子类类构造器<clinit>()执行之前，父类的类构造<clinit>()执行完毕。**由于父类的构造器<clinit>()先执行，也就意味着父类中定义的静态代码块/静态变量的初始化要优先于子类的静态代码块/静态变量的初始化执行。特别地，类构造器<clinit>()对于类或者接口来说并不是必需的，如果一个类中没有静态代码块，也没有对类变量的赋值操作，那么编译器可以不为这个类生产类构造器<clinit>()。此外，在同一个类加载器下，一个类只会被初始化一次，但是一个类可以任意地实例化对象。也就是说，**在一个类的生命周期中，类构造器<clinit>()最多会被虚拟机调用一次，而实例构造器<init>()则会被虚拟机调用多次，只要程序员还在创建对象。**

>  关于类加载的初始化阶段，在虚拟机规范严格规定了有且只有5种场景必须对类进行初始化：
>
>  - 使用new关键字实例化对象时、读取或者设置一个类的静态字段(不包含编译期常量)以及调用静态方法的时候，必须触发类加载的初始化过程(类加载过程最终阶段)。
>  - 使用反射包(java.lang.reflect)的方法对类进行反射调用时，如果类还没有被初始化，则需先进行初始化，这点对反射很重要。
>  - 当初始化一个类的时候，如果其父类还没进行初始化则需先触发其父类的初始化。
>  - 当Java虚拟机启动时，用户需要指定一个要执行的主类(包含main方法的类)，虚拟机会先初始化这个主类
>  - 当使用JDK 1.7 的动态语言支持时，如果一个java.lang.invoke.MethodHandle 实例最后解析结果为REF_getStatic、REF_putStatic、REF_invokeStatic的方法句柄，并且这个方法句柄对应类没有初始化时，必须触发其初始化(这点看不懂就算了，这是1.7的新增的动态语言支持，其关键特征是它的类型检查的主体过程是在运行期而不是编译期进行的，这是一个比较大点的话题，这里暂且打住)
>  - 当一个接口中定义了JDK 8新加入的默认方法（被default关键字修饰的接口方法）时，如果有 这个接口的实现类发生了初始化，那该接口要在其之前被初始化。
>
>   Java编译器会收集所有的类变量初始化语句和类型的静态初始化器，将这些放到一个特殊的方法中：clinit。                 
>
>  除此之外，所有引用类的方式，都不会触发初始化，称为 **被动引用**。
>
>  　　特别需要指出的是，类的实例化与类的初始化是两个完全不同的概念：
>
>  - 类的实例化是指创建一个类的实例(对象)的过程；
>  - 类的初始化是指为类中各个类成员(被static修饰的成员变量)赋初始值的过程，是类生命周期中的一个阶段。
>
>  被动引用的几种经典场景
>
>  　　**1)、通过子类引用父类的静态字段，不会导致子类初始化**
>
>     ```java
>  public class SSClass{
>      static{
>          System.out.println("SSClass");
>      }
>  }  
>  
>  public class SClass extends SSClass{
>      static{
>          System.out.println("SClass init!");
>      }
>  
>      public static int value = 123;
>  
>      public SClass(){
>          System.out.println("init SClass");
>      }
>  }
>  
>  public class SubClass extends SClass{
>      static{
>          System.out.println("SubClass init");
>      }
>  
>      static int a;
>  
>      public SubClass(){
>          System.out.println("init SubClass");
>      }
>  }
>  
>  public class NotInitialization{
>      public static void main(String[] args){
>          System.out.println(SubClass.value);
>      }
>  }/* Output: 
>          SSClass
>          SClass init!
>          123     
>   *///:~
>     ```
>
>  　　**2)、通过数组定义来引用类，不会触发此类的初始化**
>
>  ```java
>  public class NotInitialization{
>      public static void main(String[] args){
>          SClass[] sca = new SClass[10];
>      }
>  }
>  ```
>
>  　　**3)、常量在编译阶段会存入调用类的常量池中，本质上并没有直接引用到定义常量的类，因此不会触发定义常量的类的初始化**
>
>  ```java
>  public class ConstClass{
>  
>      static{
>          System.out.println("ConstClass init!");
>      }
>  
>      public static  final String CONSTANT = "hello world";
>  }
>  
>  public class NotInitialization{
>      public static void main(String[] args){
>          System.out.println(ConstClass.CONSTANT);
>      }
>  }/* Output: 
>          hello world
>   *///:~
>  ```

  

此时Thread帮助我们将方法入栈并进行管理，每个栈帧都有对运行常量池中的方法的入口的引用，但是《java虚拟机规范》中并没有规定解析阶段发生的具体时间，因此运行时常量池并非刚开始便是全部解析的了（若是运行时解析的需要借助new等指令依据指令参数解析需要的符号引用，如引用其他类的方法时，需要new一个其他的类来通过classLoader进行class的导入，若是非静态内部类，也需要通过new将该类的在运行常量池中的符号引用替换为直接引用），我们开始依据运行时常量池中的方法顺序依据直接引用的地址调用code中的字节码指令（此时，解释器帮我们将字节码指令借助字节码指令表翻译成相应CPU指令集格式，无论是哪种指令集，指令集只是我们将二进制按位数划分助记而已，已经都是0101这种cpu能解读的模式了，但都需要按照（操作码字段|地址码字段）来传送给CPU，不同的指令集只是将二进制串划分成不同的段交给CPU，CPU需要依据自身对寄存器/栈的存取方式设计自己的指令集（如不同数量的寄存器），CPU所能读取指令长度会依据指令的操作码判断是几地址指令，比如add它可以有00，01，10，11 分别表示1234地址指令，解释器帮我们将字节码指令转为二进制指令）

若是基于栈的解释执行，我们会依据各个方法创建栈帧，并用栈帧中的操作数栈作为存取空间，实现字节码指令对操作系统对于CPUapi的调用运行code中的字节码指令，而字节码指令基本上都是零地址指令（他会对指令的读取和数值的取出读入等由几个固定栈结构进行操作）。若是经过编译的，则是依据编译器，则依据寄存器的硬件实现的指令集进行解读。两者的不同主要在运行时前者需要将操作数出栈计算再入栈保存，而后者则可以在cpu计算后直接保存回寄存器操作数地址的位置上。可移植性并不是就不用在意cpu自身的指令集了，而是CPU的带地址指令集大多与寄存器的寻址等有关，而零地址指令只需知道操作码的长度即可，可移植性大幅度提高。

无论是c还是java，都是最后都是经过CPU对内存中某个内存地址那一部分的存储值依据指令集进行修改，jni也不过是起到使得c方法编译后的指令集的地址查询能符合java地址直接引用的规则，而其会将入口地址放入lib中使得能通过c中的表查询到入口（c入口地址都通过链接写到了lib中，而java的虚方法还接收者需要运行时根据实际类型选择版本等），因此无论是JNI中java对于C对象的调用还是c对于java对象的调用，只要有相应的地址，源码编译成的相应的指令集都可以实现对不同语言对象的操作，操作系统也无外乎用自己实现的指令集组合用cpu修改其他各个硬件的电平状态来达到控制所有硬件各种语言的目的。

而解释器和编译器通过操作数栈或者寄存器都调用系统API的实现，都是基于执行引擎调用该些后端编译器进行的，<client>等javac自己加上去的方法会调用执行引擎依据自己的实现选择使用上两者。

执行引擎是我们与操作系统交互的最直接的部分,我们最后将class类加入方法区后并不是就可以直接加入对JVM的其他结构,而是需要执行引擎使用后端编译器进行解释编译时,javac输出的字节码指令流,基本上是一种基于栈的指令集结构,是解释器和即时编译器运行优化的方式,是基本将中间码在JVM栈上运行的,由栈保存值的,

而提前编译编译后的或者即时编译后的直接的二进制文件,则是多基于寄存器直接实现(如x86的二地址指令集),但若是源码启动,需要你的程序刚开始需要较长的时间去编译,若是二进制版本的,则需要为每一个指令集专门编译一个版本而且也不一定完全适配,效率也没有源码编译的更快(但其实相差无几)

我们这时候也不难想象ACC_NATIVE是如何通过本地方法栈找到对c方法地址的直接引用放入运行时常量池中，调用方法时java栈通过操作数栈找到虚拟机c的方法指令的位置（而其中多是对操作系统API的调用），将方法中的指令经由CPU（用户线程（协同式 的协程）/内核线程）计算结果传给操作系统API（也是地址，再调用操作系统实现的指令，至于是直接汇编语言编译结果还是高级语言的编译结果就不得而知了），操作系统将自身编译的指令送入CPU计算，返回我们想要的结果的了，到了这一步我终于明白为什么知道面试官为什么喜欢懂得操作系统内核的了，因为操作系统中实现了很多如网络，shell显示，IO的，其中的API就是相应实现后编译的指令集的入口（而无论是Linux还是windows都是通过查询API再依据全限定类名等命名规则查询匹配返回入口地址的，因此可以无论开不开源只要有api就可以调用系统的功能，至于哪块内存在存储这部分匹配功能的，是系统还是实例实现的，是调用后直接调用触发还是如何处理暂时不清楚），而且要考虑很多的优化和并发，其中特别是要自己实现用户线程去调用CPU还是要自己的用户线程调用操作系统的API经过操作系统的内核线程使用CPU，线程调用CPU后得到的运算结果，要自己去调用IO等还是回操作系统的API实现都是很复杂的需要考虑编译器能否实现准确的编译后能否适配的，还需要借助汇编语言来查看调试优化，太难了

我们JVM等各种结构也是源码的抽象，便于我们理解和使用

> 本地方法栈和操作系统的关系可以参考：https://blog.csdn.net/yfqnihao/article/details/8289363

栈帧中的动态链接是如何直接引用到运行时常量池对应的方法的？

> 若是静态的，解析时便在线程的方法调用中所用的方法已经换成了相应的地址，线程说到底作为类也有解析的过程，若是动态的则如上，这样其每个栈帧的创建时就能依据上一个栈帧中的方法调用的地址而将其放入动态链接中

为什么不在静态常量池直接更换成直接地址，而需要个运行时常量池？

> 常量项的对应表中的字面量长度可能与地址的长度不一

# **GC**

为什么需要学习GC？

> 即使虚拟机的垃圾收集器越来越“自动化了”

学习GC首先需要了解整个内存管理领域有哪些需要管理的。

## Java内存区域

> 前提:本文讲的基本都是以Sun HotSpot虚拟机为基础的，Oracle收购了Sun后目前得到了两个（Sun的HotSpot和JRockit(以后可能合并这两个),还有一个是IBM的IBMJVM）

之所以扯了那么多计算机内存模型，是因为java内存模型的设定符合了计算机的规范。

**Java程序内存的分配是在JVM虚拟机内存分配机制下完成**。

**Java内存模型（Java Memory Model ,JMM）就是一种符合内存模型规范的，屏蔽了各种硬件和操作系统的访问差异的，保证了Java程序在各种平台下对内存的访问都能保证效果一致的机制及规范。**

> 简要言之，jmm是jvm的一种规范，定义了jvm的内存模型。它屏蔽了各种硬件和操作系统的访问差异，不像c那样直接访问硬件内存，相对安全很多，它的主要目的是解决由于多线程通过共享内存进行通信时，存在的本地内存数据不一致、编译器会对代码指令重排序、处理器会对代码乱序执行等带来的问题。可以保证并发编程场景中的原子性、可见性和有序性。

从下面这张图可以看出来，Java数据区域分为五大数据区域。这些区域各有各的用途，创建及销毁时间。

```undefined
其中方法区和堆是所有线程共享的，栈，本地方法栈和程序虚拟机则为线程私有的。
```

根据java虚拟机规范，java虚拟机管理的内存将分为下面五大区域。

![img](https:////upload-images.jianshu.io/upload_images/10006199-a4108d8fb7810a71.jpeg?imageMogr2/auto-orient/strip|imageView2/2/w/1200/format/webp)

### 五大内存区域

> 无论是哪一块内存区域，都无非是为了GC等功能的实现对一类接口的抽象的概念，像堆的概念是基于OopDesc为基类的一系列oop对象使得GC可以借助handle标记回收，早期的方法区之所以被叫做永久区，也是为了更好的利用内存空间，而将JVM中的C++实现的klass对象封装为oop借助klass-handle体系实现永久区的回收，这也是jdk后klass、method等放到MetaSpace元空间后，klass等方法区的部件不需要GC回收后，kalssOop等klass的oop对象实现被废弃，永久层成为历史的原因。

#### 1、 程序计数器

>  程序计数器是一块很小的内存空间，它是线程私有的，可以认作为当前线程的行号指示器。

**为什么需要程序计数器**

> 我们知道对于一个处理器(如果是多核cpu那就是一核)，在一个确定的时刻都只会执行一条线程中的指令，一条线程中有多个指令，为了线程切换可以恢复到正确执行位置，每个线程都需有独立的一个程序计数器，不同线程之间的程序计数器互不影响，独立存储。
>
> 注意：如果线程执行的是个java方法，那么计数器记录虚拟机字节码指令的地址。如果为native【底层方法】，那么计数器为空。**这块内存区域是虚拟机规范中唯一没有OutOfMemoryError的区域**。

#### 2、 Java栈（虚拟机栈）

同计数器也为线程私有，生命周期与相同，就是我们平时说的栈，**栈描述的是Java方法执行的内存模型**。

**每个方法被执行的时候都会创建一个栈帧用于存储局部变量表，操作栈，动态链接，方法出口等信息。每一个方法被调用的过程就对应一个栈帧在虚拟机栈中从入栈到出栈的过程。【栈先进后出，下图栈1先进最后出来】**

对于栈帧的解释参考 [Java虚拟机运行时栈帧结构](https://links.jianshu.com/go?to=https%3A%2F%2Fwww.cnblogs.com%2FnoKing%2Fp%2F8167700.html)

> 栈帧: 是用来存储数据和部分过程结果的数据结构。
> 栈帧的位置:  内存 -> 运行时数据区 -> 某个线程对应的虚拟机栈 -> here[在这里]
> 栈帧大小确定时间: 编译期确定，不受运行期数据影响。

通常有人将java内存区分为栈和堆，实际上java内存比这复杂，这么区分可能是因为我们最关注，与对象内存分配关系最密切的是这两个。

**平时说的栈一般指局部变量表部分。**

> 局部变量表:一片连续的内存空间，用来存放方法参数，以及方法内定义的局部变量，存放着编译期间已知的数据类型(八大基本类型和对象引用(reference类型),returnAddress类型。它的最小的局部变量表空间单位为Slot，虚拟机没有指明Slot的大小，但在jvm中，long和double类型数据明确规定为64位，这两个类型占2个Slot，其它基本类型固定占用1个Slot。
>
> reference类型:与基本类型不同的是它不等同本身，即使是String，内部也是char数组组成，它可能是指向一个对象起始位置指针，也可能指向一个代表对象的句柄或其他与该对象有关的位置。
>
> returnAddress类型:指向一条字节码指令的地址

![img](https:////upload-images.jianshu.io/upload_images/10006199-728567b81e7abff5.png?imageMogr2/auto-orient/strip|imageView2/2/w/1200/format/webp)

​										栈帧

**需要注意的是，局部变量表所需要的内存空间在编译期完成分配，当进入一个方法时，这个方法在栈中需要分配多大的局部变量空间是完全确定的，在方法运行期间不会改变局部变量表大小。**

> Java虚拟机栈可能出现两种类型的异常：
>
> 1. 线程请求的栈深度大于虚拟机允许的栈深度，将抛出StackOverflowError。
> 2. 虚拟机栈空间可以动态扩展，当动态扩展是无法申请到足够的空间时，抛出OutOfMemory异常。

#### 3、 本地方法栈

本地方法栈是与虚拟机栈发挥的作用十分相似,区别是虚拟机栈执行的是Java方法(也就是字节码)服务，而本地方法栈则为虚拟机使用到的native方法服务，可能底层调用的c或者c++,我们打开jdk安装目录可以看到也有很多用c编写的文件，可能就是native方法所调用的c代码。

#### 4、 堆

对于大多数应用来说，**堆是java虚拟机管理内存最大的一块内存区域，因为堆存放的对象是线程共享的，所以多线程的时候也需要同步机制**。因此需要重点了解下。

java虚拟机规范对这块的描述是:所有对象实例及数组都要在堆上分配内存，但随着JIT编译器的发展和逃逸分析技术的成熟，这个说法也不是那么绝对，但是大多数情况都是这样的。

> 即时编译器:可以把把Java的字节码，包括需要被解释的指令的程序）转换成可以直接发送给处理器的指令的程序)
>
> 逃逸分析:通过逃逸分析来决定某些实例或者变量是否要在堆中进行分配，如果开启了逃逸分析，即可将这些变量直接在栈上进行分配，而非堆上进行分配。这些变量的指针可以被全局所引用，或者其其它线程所引用。
>
> [参考逃逸分析](https://www.jianshu.com/p/20bd2e9b1f03)
>
> 逃逸分析并不是直接的优化手段，而是一个代码分析，通过动态分析对象的作用域，为其它优化手段如栈上分配、标量替换和同步消除等提供依据，发生逃逸行为的情况有两种：方法逃逸和线程逃逸。
>  1、方法逃逸：当一个对象在方法中定义之后，作为参数传递到其它方法中；
>  2、线程逃逸：如类变量或实例变量，可能被其它线程访问到；

> 注意:它是所有线程共享的，它的目的是存放对象实例。同时它也是GC所管理的主要区域，因此常被称为GC堆，又由于现在收集器常使用分代算法，Java堆中还可以细分为新生代和老年代，再细致点还有Eden(伊甸园)空间之类的不做深究。
>
> 根据虚拟机规范，Java堆可以存在物理上不连续的内存空间，就像磁盘空间只要逻辑是连续的即可。它的内存大小可以设为固定大小，也可以扩展。
>
> 当前主流的虚拟机如HotPot都能按扩展实现(通过设置 -Xmx和-Xms)，如果堆中没有内存内存完成实例分配，而且堆无法扩展将报OOM错误(OutOfMemoryError)

#### 5、 方法区

方法区同堆一样，是所有线程共享的内存区域，为了区分堆，又被称为非堆。

**从概念上讲，这些变量所使用的内存都应当在方法区中进行分配，但需要注意的是方法区本身是一个逻辑层面的概念，jvm规范中并没有限制GC对其的内存分配的，因此其可以有不同的存储与回收方式，其实现在不同的版本，不同的虚拟机上可能分布在不同的内存空间。去永久代后方法区越来越是一个逻辑抽象层面的东西，是为了更好的区分类对象和静态变量与实例对象和普通变量进行不同的使用和处理而存在的**

用于存储已被虚拟机加载的类信息、常量、静态变量，如static修饰的变量加载类的时候就被加载到方法区中。

> 运行时常量池是方法区的一部分，class文件除了有类的字段、接口、方法等描述信息之外，还有常量池用于存放编译期间生成的各种字面量和符号引用。
>

在老版jdk，方法区也被称为永久代【因为没有强制要求方法区必须实现垃圾回收，HotSpot虚拟机以永久代来实现方法区，从而JVM的垃圾收集器可以像管理堆区一样管理这部分区域，从而不需要专门为这部分设计垃圾回收机制。不过自从JDK7之后，Hotspot虚拟机便将运行时常量池从永久代移除了。】

> 类的静态变量是存储在类对象中的（类对象是属于方法区的，但Jdk7 后存储再堆区的），具体可参考BytecodeInterpreter针对putstatic指令的处理(putstatic指令往往用于静态域的赋值操作)：

class对象是在堆中还是在方法区中：

> https://www.cnblogs.com/xy-nb/p/6773051.html
>
> **方法区变迁**
>
> jdk6
> Klass元数据信息
> 每个类的运行时常量池(字段、方法、类、接口等符号引用)、编译后的代码
> 静态字段(无论是否有final)在instanceKlass末尾（位于PermGen内）
> oop 其实就是Class对象实例
> 全局字符串常量池StringTable，本质上就是个Hashtable
> 符号引用（类型指针是SymbolKlass）
>
>
> jdk7
> Klass元数据信息
> 每个类的运行时常量池(字段、方法、类、接口等符号引用)、编译后的代码
> 静态字段从instanceKlass末尾移动到了java.lang.Class对象（oop）的末尾（位于普通Java heap内）
> oop与全局字符串常量池移到java heap上
> 符号引用被移动到native heap中
>
> jdk8
> 移除永久代：
> Klass元数据信息、每个类的运行时常量池、编译后的代码移到了另一块与堆不相连的本地内存————元空间（Metaspace）
> 参数控制-XX:MetaspaceSize与-XX:MaxMetaspaceSize。
>
> **Class实例在堆中还是方法区中？**
>
> jdk6：总结，从klassOop构建流程看出，JDK1.6中Class实例在方法区，而且和JDK7创建流程有了很大差异
>
> jdk7：总结：JDK7创建Class实例存在堆中；因为JDK7中JavaObjectsInPerm参数值固定为false。
>
> jdk8:  总结：JDK8移除了永久代，转而使用元空间来实现方法区，创建的Class实例在java heap中
>
> 

jdk 8之前，HotSpot团队选择把收集器的分代扩展至方法区，由垃圾收集器统一收集，省去专门写一个独立的管理方法区的方法，而方法区的存储内容与前面的分代的更新换代条件大不相同，所以专门划分了个永久代，但这容易导致更多的内存溢出问题

jdk6hotspot就将舍弃永久代放进了发展策略，逐步改用成了用直接内存（Direct Memory）中的元空间等来存储方法区的内容，实现单独的回收管理

jdk7已经将字符串常量池、静态变量等移出，jdk8以全部移出

> jdk1.7开始逐步去永久代。从String.interns()方法可以看出来
> String.interns()
> native方法:作用是如果字符串常量池已经包含一个等于这个String对象的字符串，则返回代表池中的这个字符串的String对象，在jdk1.6及以前常量池分配在永久代中。可通过 -XX:PermSize和-XX:MaxPermSize限制方法区大小。

jdk8 时类变量会随着Class对象一起存放到Java堆中，类型信息则放到了直接内存中了。

图网上找的（其中类信息也称为静态常量池）

![java内存结构](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9jZG4uanNkZWxpdnIubmV0L2doL2xlaXl1MTk5Ny9QaWNCZWRAbWFzdGVyL2Jsb2dzL3BpY3R1cmVzL2phdmElRTUlODYlODUlRTUlQUQlOTglRTclQkIlOTMlRTYlOUUlODQucG5n?x-oss-process=image/format,png)



```java
public class StringIntern {
    //运行如下代码探究运行时常量池的位置
    public static void main(String[] args) throws Throwable {
        //用list保持着引用 防止full gc回收常量池
        List<String> list = new ArrayList<String>();
        int i = 0;
        while(true){
            list.add(String.valueOf(i++).intern());
        }
    }
}
//如果在jdk1.6环境下运行 同时限制方法区大小 将报OOM后面跟着PermGen space说明方法区OOM，即常量池在永久代
//如果是jdk1.7或1.8环境下运行 同时限制堆的大小  将报heap space 即常量池在堆中
```

[idea设置相关内存大小设置](https://links.jianshu.com/go?to=https%3A%2F%2Fwww.cnblogs.com%2Fyingsong%2Fp%2F5896207.html)

这边不用全局的方式，设置main方法的vm参数。

做相关设置，比如说这边设定堆大小。（-Xmx5m -Xms5m -XX:-UseGCOverheadLimit）

```css
这边如果不设置UseGCOverheadLimit将报java.lang.OutOfMemoryError: GC overhead limit exceeded，
这个错是因为GC占用了多余98%（默认值）的CPU时间却只回收了少于2%（默认值）的堆空间。目的是为了让应用终止，给开发者机会去诊断问题。一般是应用程序在有限的内存上创建了大量的临时对象或者弱引用对象，从而导致该异常。虽然加大内存可以暂时解决这个问题，但是还是强烈建议去优化代码，后者更加有效，也可通过UseGCOverheadLimit避免[不推荐，这里是因为测试用，并不能解决根本问题]
```

![img](https:////upload-images.jianshu.io/upload_images/10006199-b55cc68293d1807d.png?imageMogr2/auto-orient/strip|imageView2/2/w/1200/format/webp)

![img](https:////upload-images.jianshu.io/upload_images/10006199-76054110706ff110.png?imageMogr2/auto-orient/strip|imageView2/2/w/1200/format/webp)

**jdk8真正开始废弃永久代，而使用元空间(Metaspace)**

> java虚拟机对方法区比较宽松，除了跟堆一样可以不存在连续的内存空间，定义空间和可扩展空间，还可以选择不实现垃圾收集。

### 对象的内存布局

在HotSpot虚拟机中。对象在内存中存储的布局分为

> 1.对象头
> 2.实例数据
> 3.对齐填充

#### 1、 对象头【markword】

在32位系统下，对象头8字节，64位则是16个字节【未开启压缩指针，开启后12字节】。

> markword很像网络协议报文头，划分为多个区间，并且会根据对象的状态复用自己的存储空间。
> 为什么这么做:省空间，对象需要存储的数据很多，32bit/64bit是不够的，它被设计成非固定的数据结构以便在极小的空间存储更多的信息，

> 假设当前为32bit，在对象未被锁定情况下。25bit为存储对象的哈希码、4bit用于存储分代年龄，2bit用于存储锁标志位，1bit固定为0。

不同状态下存放数据

![img](https:////upload-images.jianshu.io/upload_images/10006199-b0fd456c33c09fce.png?imageMogr2/auto-orient/strip|imageView2/2/w/700/format/webp)

这其中锁标识位需要特别关注下。**锁标志位与是否为偏向锁对应到唯一的锁状态**。

锁的状态分为四种`无锁状态`、`偏向锁`、`轻量级锁`和`重量级锁`

不同状态时对象头的区间含义，如图所示。

![img](https:////upload-images.jianshu.io/upload_images/10006199-9b3fe05daab42136.jpg?imageMogr2/auto-orient/strip|imageView2/2/w/1024/format/webp)

对象头.jpg

HotSpot底层通过markOop实现Mark Word，具体实现位于`markOop.hpp`文件。

```java
markOop中提供了大量方法用于查看当前对象头的状态，以及更新对象头的数据，为synchronized锁的实现提供了基础。[比如说我们知道synchronized锁的是对象而不是代码，而锁的状态保存在对象头中，进而实现锁住对象]。
```

关于对象头和锁之间的转换，网上大神总结

<img src="https:////upload-images.jianshu.io/upload_images/10006199-318ad80ccb29abe4.png?imageMogr2/auto-orient/strip|imageView2/2/w/1200/format/webp" alt="img" style="zoom: 200%;" />

​																											偏向锁轻量级锁重量级锁.png

#### 2 、实例数据



> 存放对象程序中各种类型的字段类型，不管是从父类中继承下来的还是在子类中定义的。
> 分配策略:相同宽度的字段总是放在一起，比如double和long

#### 3、 对齐填充

这部分没有特殊的含义，仅仅起到占位符的作用满足JVM要求。



> 由于HotSpot规定对象的大小必须是8的整数倍，对象头刚好是整数倍，如果实例数据不是的话，就需要占位符对齐填充。

### 对象的访问定位

java程序需要通过引用(ref)数据来操作堆上面的对象，那么如何通过引用定位、访问到对象的具体位置。

```undefined
对象的访问方式由虚拟机决定，java虚拟机提供两种主流的方式
1.句柄访问对象
2.直接指针访问对象。(Sun HotSpot使用这种方式)
```

参考[Java对象访问定位](https://links.jianshu.com/go?to=https%3A%2F%2Fblog.csdn.net%2Fu011080472%2Farticle%2Fdetails%2F51321769)

#### 1 、句柄访问

> 简单来说就是java堆划出一块内存作为句柄池,引用中存储对象的句柄地址,句柄中包含对象实例数据、类型数据的地址信息。
>
> ##### 优点:引用中存储的是稳定的句柄地址,在对象被移动【垃圾收集时移动对象是常态】只需改变句柄中实例数据的指针，不需要改动引用【ref】本身。

![img](https:////upload-images.jianshu.io/upload_images/10006199-27ef5c978077ed1c.jpg?imageMogr2/auto-orient/strip|imageView2/2/w/1200/format/webp)

​																															句柄访问.jpg

#### 2 、直接指针

> 与句柄访问不同的是，ref中直接存储的就是对象的实例数据,但是类型数据跟句柄访问方式一样。
>
> 优点:优势很明显，就是速度快，**相比于句柄访问少了一次指针定位的开销时间**。【可能是出于Java中对象的访问时十分频繁的,平时我们常用的JVM HotSpot采用此种方式】

![img](https:////upload-images.jianshu.io/upload_images/10006199-6cefc46d23c2d549.jpg?imageMogr2/auto-orient/strip|imageView2/2/w/1200/format/webp)

​																																			直接指针.jpg



### 内存溢出

> 两种内存溢出异常[注意内存溢出是error级别的]
> 1.StackOverFlowError:当请求的栈深度大于虚拟机所允许的最大深度
> 2.OutOfMemoryError:虚拟机在扩展栈时无法申请到足够的内存空间[一般都能设置扩大]

java -verbose:class -version 可以查看刚开始加载的类，可以发现这两个类并不是异常出现的时候才去加载，而是jvm启动的时候就已经加载。这么做的原因是在vm启动过程中我们把类加载起来，并创建几个没有堆栈的对象缓存起来，只需要设置下不同的提示信息即可，当需要抛出特定类型的OutOfMemoryError异常的时候，就直接拿出缓存里的这几个对象就可以了。

比如说OutOfMemoryError对象，jvm预留出4个对象【固定常量】，这就为什么最多出现4次有堆栈的OutOfMemoryError异常及大部分情况下都将看到没有堆栈的OutOfMemoryError对象的原因。

[参考OutOfMemoryError解读](https://links.jianshu.com/go?to=http%3A%2F%2Flovestblog.cn%2Fblog%2F2016%2F08%2F29%2Foom%2F)

![img](https:////upload-images.jianshu.io/upload_images/10006199-07496d628d676815.png?imageMogr2/auto-orient/strip|imageView2/2/w/1200/format/webp)

​																																Snip20180904_8.png

两个基本的例子

```java
public class MemErrorTest {
    public static void main(String[] args) {
        try {
            List<Object> list = new ArrayList<Object>();
            for(;;) {
                list.add(new Object()); //创建对象速度可能高于jvm回收速度
            }
        } catch (OutOfMemoryError e) {
            e.printStackTrace();
        }

        try {
            hi();//递归造成StackOverflowError 这边因为每运行一个方法将创建一个栈帧，栈帧创建太多无法继续申请到内存扩展
        } catch (StackOverflowError e) {
            e.printStackTrace();
        }

    }

    public static void hi() {
        hi();
    }
}
```

![img](https:////upload-images.jianshu.io/upload_images/10006199-04a7ea1247b98809.png?imageMogr2/auto-orient/strip|imageView2/2/w/1200/format/webp)

## 垃圾收集器与内存分配策略

只要Java虚拟机能够工作，垃圾收集器便不可能是真正“无操作”的。原因是“垃圾收集器”这个名字并不能形容它全部的职责，更贴切的名字应该是本书为这一部分 所取的标题——==“自动内存管理子系统”==。

**一个垃圾收集器除了垃圾收集这个本职工作之外，它还要负责堆的管理与布局、对象的分配、与解释器的协作、与编译器的协作、与监控子系统协作等职责，其 中至少堆的管理和对象的分配这部分功能是Java虚拟机能够正常运作的必要支持，是一个最小化功能的垃圾收集器也必须实现的内容。**从JDK 10开始，为了隔离垃圾收集器与Java虚拟机解释、编译、监控等子系统的关系，RedHat提出了垃圾收集器的统一接口，即JEP 304提案，Epsilon是这个接口的有效性验证和参考实现，同时也用于需要剥离垃圾收集器影响的性能测试和压力测试。 

**在垃圾回收器回收内存之前，还需要一些清理工作。
因为垃圾回收gc只能回收通过new关键字申请的内存（在堆上），但是堆上的内存并不完全是通过new申请分配的。还有一些本地方法（一般是调用的C方法）。这部分“特殊的内存”如果不手动释放，就会导致内存泄露，gc是无法回收这部分内存的。
所以需要在finalize中用本地方法(native method)如free操作等，再使用gc方法。显示的GC方法是`system.gc()`**

### 1、 判断对象是否存活算法

```bash
1.引用计数算法
早期判断对象是否存活大多都是以这种算法，这种算法判断很简单，简单来说就是给对象添加一个引用计数器，每当对象被引用一次就加1，引用失效时就减1。当为0的时候就判断对象不会再被引用。
优点:实现简单效率高，如微软COM。
缺点:1.每个对象都占用了一部分内存
	2.难以解决循环引用的问题，就是假如两个对象互相引用已经不会再被其它其它引用，导致一直不会为0就无法进行回收。

2.可达性分析算法
目前主流的商用语言[如java、c#]采用的是可达性分析算法判断对象是否存活。这个算法有效解决了循环利用的弊端。
它的基本思路是通过一个称为“GC Roots”的对象为起始点，搜索所经过的路径称为引用链，当一个对象到GC Roots没有任何引用跟它连接则证明对象是不可用的。
```

![img](https:////upload-images.jianshu.io/upload_images/10006199-854e1de91f66764b.png?imageMogr2/auto-orient/strip|imageView2/2/w/1200/format/webp)

​																														gc.png

可作为GC Roots的对象有四种

> ①虚拟机栈(栈桢中的本地变量表)中的引用的对象，就是平时所指的java对象，存放在堆中
>
> ②方法区中的类静态属性引用的对象，一般指被static修饰引用的对象，加载类的时候就加载到内存中
>
> ③方法区中的常量引用的对象
>
> ④本地方法栈中JNI（native方法)引用的对象
>
> ⑤Java虚拟机内部的引用，如基本数据类型对应的Class对象，一些常驻的异常对象（NullPointException等），还有系统类加载器
>
> ⑥所有被同步锁（synchronized关键字）持有的对象
>
> ⑦反应Java虚拟机内部情况的JMXBean、JVMTI中注册的回调、本地缓存代码等

除了这些固定的还要依据区域等，如分代中的跨代引用，非收集区域中跨代引用的对象的加入

即使可达性算法中不可达的对象，也不是一定要马上被回收，还有可能被抢救一下。

> 要真正宣告对象死亡需经过两个过程。
> 1.可达性分析后没有发现引用链
> 2.查看对象是否有finalize方法，如果有重写且在方法内完成自救[比如再建立引用]，还是可以抢救一下，注意这边一个类的finalize只执行一次
>
> 如果这个对象被判定为确有必要执行finalize()方法，那么该对象将会被放置在一个名为==F-Queue的队列==之中，并在稍后由一条由虚拟机自动建立的、低调度优先级的==Finalizer线程==去执行它们的finalize()方法。
>
> 这里所说的“执行”是指虚拟机会触发这个方法开始运行，但并**不承诺一定会等待它运行结束**。 这样做的原因是，如果某个对象的finalize()方法执行缓慢，或者更极端地发生了死循环，将很可能导致F-Queue队列中的其他对象永久处于等待，甚至导致整个内存回收子系统的崩溃。
>
> finalize()方法是对象逃脱死亡命运的最后一次机会，稍后收集器将对F-Queue中的对象进行第二次小规模的==标记==，如果对象要在finalize()中成功拯救自己——只要**重新与引用链上的任何一个对象建立关联**即可，譬如把自己（this关键字）赋值给某个类变量或者对象的成员变量，那在第二次标记时它将被==移出==“即将回收”的集合；如果对象这时候还没有逃脱，那基本上它就真的要被回收了。
>
> 但是笔者==建议大家尽量避免使用它==，因为它并不能等同 于C和C++语言中的析构函数，而是Java刚诞生时为了使传统C、C++程序员更容易接受Java所做出的一项妥协。它的运行代价高昂，不确定性大，无法保证各个对象的调用顺序，如今已被官方明确声明为不推荐使用的语法。使用==try-finally或者其他方式==都可以做得更好、更及时。

### 2、引用

无论是通过引用计数算法判断对象的引用数量，还是通过可达性分析算法判断对象是否引用链可达，都离不开引用，但是若只有“引用”和“未引用”并不能适应所有的情况。

因此JVM中将对象的引用分为了四种类型，不同的对象引用类型会造成GC采用不同的方法进行回收：
（1）强引用：默认情况下，对象采用的均为强引用（GC不会回收）
（2）软引用：软引用是Java中提供的一种比较适合于缓存场景的应用（只有在内存不够用的情况下才会被GC）
（3）弱引用：在GC时一定会被GC回收
（4）虚引用：在GC时一定会被GC回收

#### 强引用

强引用是最传统的“引用”的定义，是指在程序代码之中普遍存在的引用赋值，即类似“Object obj=new Object()”这种引用关系。无论任何情况下，只要强引用关系还存在，==垃圾收集器就永远不会回收掉被引用的对象==。

#### 软引用

软引用是用来描述一些还有用，但非必须的对象。只被软引用关联着的对象，**在系统将要发生内存溢出异常前，会把这些对象列进回收范围之中进行第二次回收**，如果这次回收还没有足够的内存，才会抛出内存溢出异常。在JDK 1.2版之后提供了SoftReference类来实现软引用。

#### 弱引用

弱引用也是用来描述那些非必须对象，但是它的强度比软引用更弱一些，被弱引用关联的对象只能生存到下一次垃圾收集发生为止。==当垃圾收集器开始工作，无论当前内存是否足够，都会回收掉只被弱引用关联的对象==。在JDK 1.2版之后提供了WeakReference类来实现弱引用。 

#### 虚引用

> 虚引用是使用PhantomReference创建（jdk1.2后）的引用，虚引用也称为幽灵引用或者幻影引用，是所有引用类型中最弱的一个。一个对象是否有虚引用的存在，完全不会对其生命周期构成影响，也无法通过虚引用获得一个对象实例。

虚引用，正如其名，对一个对象而言，这个引用形同虚设，有和没有一样。

> 如果一个对象与GC Roots之间仅存在虚引用，则称这个对象为`虚可达（phantom reachable）`对象。

当试图通过虚引用的get()方法取得强引用时，总是会返回null，并且，虚引用必须和引用队列一起使用。既然这么虚，那么它出现的意义何在？？

别慌别慌，自然有它的用处。它的作用在于==跟踪垃圾回收过程==，在对象被收集器回收时收到一个系统通知。 **当垃圾回收器准备回收一个对象时，如果发现它还有虚引用，就会在垃圾回收后，将这个虚引用加入引用队列，在其关联的虚引用出队前，不会彻底销毁该对象。** 所以可以通过检查引用队列中是否有相应的虚引用来判断对象是否已经被回收了。

**如果一个对象没有强引用和软引用，对于垃圾回收器而言便是可以被清除的，在清除之前，会调用其finalize方法，如果一个对象已经被调用过finalize方法但是还没有被释放，它就变成了一个虚可达对象。**

**但是，除非确实有必要，应该避免使用finalization。Finalization的过程是不确定并且不可预测的。因此我们可以使用PhantomReference来代替finalize方法**

> https://zhuanlan.zhihu.com/p/55114919

与软引用和弱引用不同，显式使用虚引用可以阻止对象被清除，只有在程序中显式或者隐式移除这个虚引用时，这个已经执行过finalize方法的对象才会被清除。想要显式的移除虚引用的话，只需要将其从引用队列中取出然后扔掉（置为null）即可。

使用虚引用的目的就是为了得知对象被GC的时机，所以可以利用虚引用来进行销毁前的一些操作，比如说资源释放等。这个虚引用对于对象而言完全是无感知的，有没有完全一样，但是对于虚引用的使用者而言，就像是待观察的对象的把脉线，可以通过它来观察对象是否已经被回收，从而进行相应的处理。

事实上，虚引用有一个很重要的用途就是用来做==堆外内存的释放==，DirectByteBuffer就是通过虚引用来实现堆外内存的释放的。

- 虚引用是最弱的引用
- 虚引用对对象而言是无感知的，对象有虚引用跟没有是完全一样的
- 虚引用不会影响对象的生命周期
- 虚引用可以用来做为对象是否存活的监控

### 3、回收方法区

方法区的垃圾收集主要回收两部分内容：废弃的常量和不再使用的类型。

**常量：**

举个常量池中字面量回收的例子，假如一个字符串“java”曾经进入常量池中，但是当前系统又没有任何一个字符串对象的值是“java”，换句话说，已经没有任何字符串对象引用常量池中的“java”常量，且虚拟机中也没有其他地方引用这个字面量。如果在这时发生内存回收，而且垃圾收集器判断确有必要的话，这个“java”常量就将会被系统清理出常量池。常量池中其他类（接口）、方法、字段的符号引用也与此类似。 

**类型**：

需要同时满足下面三个条件： 

·该类所有的实例都已经被回收，也就是Java堆中不存在该类及其任何派生子类的实例。 

·加载该类的类加载器已经被回收，这个条件除非是经过精心设计的可替换类加载器的场景，如OSGi、JSP的重加载等，否则通常是很难达成的。 

·该类对应的java.lang.Class对象没有在任何地方被引用，无法在任何地方通过反射访问该类的方法。

> Java虚拟机被允许对满足上述三个条件的无用类进行回收，这里说的仅仅是“被允许”，而并不是和对象一样，没有引用了就必然会回收。关于是否要对类型进行回收，HotSpot虚拟机提供了-Xnoclassgc参数进行控制，还可以使用-verbose：class以及-XX：+TraceClass-Loading、-XX：+TraceClassUnLoading查看类加载和卸载信息，其中-verbose：class和-XX：+TraceClassLoading可以在Product版的虚拟机中使用，-XX：+TraceClassUnLoading参数需要FastDebug版[1]的虚拟机支持。
>
> 在大量使用反射、动态代理、CGLib等字节码框架，动态生成JSP以及OSGi这类==频繁自定义类加载器==的场景中，通常都==需要Java虚拟机具备类型卸载的能力==，以保证不会对方法区造成过大的内存压力

### 4、垃圾回收算法

分代收集名为理论，实质是一套符合大多数程序运行实际情况的经验法则，它建立在两个分代假说之上： 

1）弱分代假说（Weak Generational Hypothesis）：绝大多数对象都是朝生夕灭的。 

2）强分代假说（Strong Generational Hypothesis）：熬过越多次垃圾收集过程的对象就越难以消亡。

因为对象不是孤立的，对象之间会存在跨代引用； 为了分代理论为基础进行设计，引出了第三条假说

3）跨代引用假说（Intergenerational Reference Hypothesis）：跨代引用相对于同代引用来说仅占极少数。 

依据第三条我们就可以为跨代引用设计一个专门的设计区域（记忆集，Remembered Set）进行相关信息的存储而不是每次都扫描整个区。

**因此大致有该三个分代：**

1. 新生代 Young Generation
   1. Eden Space 任何新进入运行时数据区域的实例都会存放在此
   2. S0 Suvivor Space 存在时间较长，经过垃圾回收没有被清除的实例，就从Eden 搬到了S0
   3. S1 Survivor Space 同理，存在时间更长的实例，就从S0 搬到了S1
2. 旧生代 Old Generation/tenured
   同理，存在时间更长的实例，对象多次回收没被清除，就从S1 搬到了tenured
3. Perm 存放运行时数据区的方法区

#### **分代收集定义**

·部分收集（Partial GC）：指目标不是完整收集整个Java堆的垃圾收集，其中又分为： 

> 新生代收集（Minor GC/Young GC）：指目标只是新生代的垃圾收集。 
>
> 老年代收集（Major GC/Old GC）：指目标只是老年代的垃圾收集。**目前只有CMS收集器会有单独收集老年代的行为**。另外请注意“Major GC”这个说法现在有点混淆，在不同资料上常有不同所指，读者需按上下文区分到底是指老年代的收集还是整堆收集。 （CMS的特殊模式可能需要特殊的记忆集等，用的情况比较少）
>
> 混合收集（Mixed GC）：指目标是收集整个新生代以及部分老年代的垃圾收集。**只有G1有这个模式，ZGC和Shenandoah还没有分代，直接full gc。**

·整堆收集（Full GC）：收集整个Java堆和==方法区的==垃圾收集。 

> **==Major GC和Full GC的区别是什么？==**
>
> 针对HotSpot VM的实现，它里面的GC其实准确分类只有两大种：
>
> - Partial GC：并不收集整个GC堆的模式
>
> - - Young GC：只收集young gen的GC
>   - Old GC：只收集old gen的GC。只有CMS的concurrent collection是这个模式
>   - Mixed GC：收集整个young gen以及部分old gen的GC。
>
> - Full GC：收集整个堆，包括young gen、old gen、perm gen（如果存在的话）等所有部分的模式。
>
> **Major GC通常是跟full GC是等价的，收集整个GC堆。**但因为HotSpot VM发展了这么多年，外界对各种名词的解读已经完全混乱了，当有人说“major GC”的时候一定要问清楚他想要指的是上面的full GC还是old GC。
>
> 最简单的分代式GC策略，按HotSpot VM的serial GC的实现来看，触发条件是：
>
> - young GC：当young gen中的eden区分配满的时候触发。注意young GC中有部分存活对象会晋升到old gen，所以young GC后old gen的占用量通常会有所升高。
> - full GC：当准备要触发一次young GC时，如果发现统计数据说之前young GC的平均晋升大小比目前old gen剩余的空间大，则不会触发young GC而是转为触发full GC（因为HotSpot VM的GC里，除了CMS的concurrent collection之外，其它能收集old gen的GC都会同时收集整个GC堆，包括young gen，所以不需要事先触发一次单独的young GC）；或者，如果有perm gen的话，要在perm gen分配空间但已经没有足够空间时，也要触发一次full GC；或者System.gc()、heap dump带GC，默认也是触发full GC。
>
> HotSpot VM里其它非并发GC的触发条件复杂一些，不过大致的原理与上面说的其实一样。
> 当然也总有例外。Parallel Scavenge（-XX:+UseParallelGC）框架下，默认是在要触发full GC前先执行一次young GC，并且两次GC之间能让应用程序稍微运行一小下，以期降低full GC的暂停时间（因为young GC会尽量清理了young gen的死对象，减少了full GC的工作量）。控制这个行为的VM参数是-XX:+ScavengeBeforeFullGC。这是HotSpot VM里的奇葩嗯。可跳传送门围观：[JVM full GC的奇怪现象，求解惑？ - RednaxelaFX 的回答](https://www.zhihu.com/question/48780091/answer/113063216)
>
> 并发GC的触发条件就不太一样。以CMS GC为例，它主要是定时去检查old gen的使用量，当使用量超过了触发比例就会启动一次CMS GC，对old gen做并发收集。

#### 标记-清除算法

算法分为“标记”和“清除”两个阶段：首先标记处所有需要回收的对象，在标记完成后，统一回收掉所有被标记的对象，也可以反过来，标记存活对象，统一回收所有未被标记的对象。

优点：

1. 延迟低

缺点：

1. 执行效率不稳定，标记和清除都随对象数量的增长而降低
2. 内存空间的碎片化，大对象没有地方存储时会触发其他GC，吞吐量下降

![image-20201003102144536](F:\Typora数据储存\高级语言\JAVA\JVM简介.assets\image-20201003102144536.png)

#### 标记-复制算法

先是“半区复制”，即还存活的复制到另外半区，当前半区清除，但缺点较为明显，需要浪费一般的内存空间。

![image-20201003102557905](F:\Typora数据储存\高级语言\JAVA\JVM简介.assets\image-20201003102557905.png)

后来，Andrew Appel提出了“Appel式回收”，Appel式回收的具体做法是把新生代分为一块较大的Eden空间和两块较小的Survivor空间，**每次分配内存只使用Eden和其中一块Survivor。**

> 两个Survivor的作用是轮换使用
>
> 为什么复制算法要分两个Survivor，而不直接移到老年代？
>
> 这样做的话效率可能会更高，但是old区一般都是熬过多次可达性分析算法过后的存活的对象，要求比较苛刻且空间有限，而不能直接移过去，这将导致一系列问题(比如老年代容易被撑爆)
>
> 分两个Survivor(from/to)，自然是为了保证复制算法运行以提高效率。

发生垃圾搜集时，将Eden和Survivor中仍然存活的对象一次性复制到另外一块Survivor空间上，然后直接清理掉Eden和已用过的那块Survivor空间。HotSpot虚拟机默认Eden和Survivor的大小比例是8∶1，也即每次新生代中可用内存空间为整个新 生代容量的90%（Eden的80%加上一个Survivor的10%），只有一个Survivor空间，即10%的新生代是会被“浪费”的。

当然，98%的对象可被回收仅仅是“普通场景”下测得的数据，任何人都没有办法百分百保证每次回收都只有不多于10%的对象存活，因此Appel式回收还有一个充当罕见情况的“逃生门”的安全设计，**当Survivor空间不足以容纳一次Minor GC之后存活的对象时，就需要依赖其他内存区域（实际上大多就是老年代）进行分配担保（Handle Promotion）。** 

#### 标记-整理算法

标记过程仍然与“标记-清除”算法一样，但后续为让所有存活的对象都向内存空间一端移动，然后直接清除掉边界以外的内存。

![image-20201003105013414](F:\Typora数据储存\高级语言\JAVA\JVM简介.assets\image-20201003105013414.png)

标记整理相较于标记复制：

> 1. 标记复制需要提前占用一个Surivor，内存空间占用比标记整理大
>
> 2. 标记-复制适合存活对象较少时，可以一口气通过连续复制达成整理；标记-整理适合存活对象较多的情况，通过逐个移动减少复制操作；
>
>    相同对象数的情况下还是连续复制的标记-复制算法还是较快，但标记-整理可大幅度减少需要复制的对象来调高效率

无论是标记-复制还是标记-整理算法，都需要移动存活对象，需要注意用户进程对存活对象的引用情况，有两种方式，各有不同的区别

> 1. 暂停用户进程（“Stop the Wrold”）
>
>    缺点：
>
>    ​		1. 取决于存活对象的多少与大小，时间难以判定
>
>    优点：
>
>    1. 实现简单
>
>    2. 减少对记忆集的维护难度
>    3. 往后的吞吐量性能较好
>
> 2. 并发用户线程
>
>    缺点：
>
>    1. 实现难度大
>    
>     	2. 记忆集/染色指针的维护较复杂
>     	3. 整个清理阶段时间变长
>  	4. 对吞吐量可能有较小的影响
> 
> 优点：
> 
>    1. 用户线程可异步执行，用户体验更好
>    2. 在强调低延迟的环境中有更好的发挥

而标记-清除算法，则需要解决碎片化空间问题，如用“分区空闲分配链表”（大文件分散存储），但会导致内存访问时吞吐量性能急速下降。

我们用不同的算法实现垃圾收集器时，不一定只能采用其中一种，可以结合使用，如CMS的标记-清除+标记-整理，甚至用不同于的内存布局如不同于整片区域分代的传统方式，用Region等。

### 5、HotSpot的算法实现细节

#### 根节点枚举

==迄今为止，所有收集器在根节点枚举这一步骤时都是必须暂停用户线程的==，现在可达性分析算法耗时最长的查找引用链的过程已经可以做到与用户线程一起并发，但根节点枚举始终还是必须在一个能==保障一致性的快照==中才得以进行——这里“一致性”的意思是整个枚举期间执行子系统看起来就像被冻结在某个时间点上，不会出现分析过程中，根节点集合的对象引用关系还在不断变化的情况，若这点不能满足的话，分析结果准确性也就无法保证。

由于目前主流Java虚拟机使用的都是==准确式垃圾收集==，所以当用户线程停顿下来之后，其实并不需要一个不漏地检查完所有执行上下文和全局的引用位置，虚拟机应当是有办法直接得到哪些地方存放着对象引用的。在HotSpot的解决方案里，是使用一组称为==OopMap的数据结构==来达到这个目的。一旦类加载动作完成的时候，HotSpot就会把对象内什么偏移量上是什么类型的数据计算出来，在即时编译过程中，也会==在特定的位置记录下栈里和寄存器里哪些位置是引用==。这样收集器在扫描时就可以直接得知这些信息了，并不需要真正一个不漏地从方法区等GC Roots开始查找。 

#### 安全点

如果为每一条指令都生成对应的OopMap，那将会需要大量的额外存储空间，实际上HotSpot也的确没有为每条指令都生成OopMap，前面已经提到，只是在“特定的位置”记录了这些信息，这些位置被称为==安全点（Safepoint）==。

用户程序执行时 并非在代码指令流的任意位置都能够停顿下来开始垃圾收集，而是==强制要求必须执行到达安全点后==才能够暂停。

安全点位置的选取基本上是**以“是否具有让程序长时间执行的特征”为标准**进行选定的，因为每条指令执行的时间都非常短暂，程序不太可能因为指令流长度太长这样的原因而长时间执行，**“长时间执行”的最明显特征就是指令序列的复用**，例如方法调用、循环跳转、异常跳转等都属于指令序列复用，所以只有具有这些功能的指令才会产生安全点。 

对于安全点，另外一个需要考虑的问题是，如何在垃圾收集发生时让所有线程（这里其实不包括执行JNI调用的线程）都跑到最近的安全点，然后停顿下来。这里有两种方案可供选择：抢先式中断（Preemptive Suspension）和主动式中断（Voluntary Suspension）

==**抢先式中断**==

抢先式中断不需要线程的执行代码主动去配合，在垃圾收集发生时，系统**首先把所有用户线程全部中断**，**如果发现有用户线程中断的地方不在安全点上，就恢复这条线程执行，让它一会再重新中断，直到跑到安全点上。**现在**几乎没有**虚拟机实现采用抢先式中断来暂停线程响应GC事件。 

==**主动式中断**==

当垃圾收集需要中断线程的时候，不直接对线程操作，仅仅简单地设置一个标志位，各个线程执行过程时会不停地主动去轮询这个标志，一旦发现中断标志为真时就自己在最近的安全点上主动中断挂起。==轮询标志的地方和安全点是重合的，另外还要加上所有创建对象和其他需要在Java堆上分配内存的地方（返回上一个最近的安全点指令）==，即所有上述的地方都会进行轮询操作，这是为了检查是否即将要发生垃圾收集，避免没有足够内存分配新对象。

由于轮询操作在代码中会频繁出现，这要求它必须足够高效。HotSpot使用内存保护陷阱的方式，**把轮询操作精简至只有一条汇编指令的程度**。

#### 安全区域

上面的安全点需要线程**正在运行**，能够响应虚拟机的**中断请求**，若是用户线程处于Sleep状态或者Blocked状态，此时线程无法响应虚拟机的中断请求。对于这种情况，就必须引入**安全区域（Safe Region）**来解决。

> 安全区域是指能够确保某一个代码片段中，Sleep和Blocked也属于此，引用关系不会发生改变

当用户线程执行到安全区域里面的代码时，首先会标识自己已经进入了安全区域，那样当这段时间里虚拟机要发起垃圾收集时就不必去管这些已声明自己在安全区域内的线程了。当线程要离开安全区域时，它要检查虚拟机是否已经完成了根节点枚举（或者垃圾收集过程中其他需要暂停用户线程的阶段）**，如果完成了，那线程就当作没事发生过，继续执行；否则它就必须一直等待，直到收到可以离开安全区域的信号为止。**

#### 记忆集与卡表

为解决对象跨代引用所带来的问题，垃圾收集器在新生代中建立了名为记忆集（Remembered Set）的数据结构，用以避免把整个老年代加进GC Roots扫描范围。

**为什么老年代不用专门的记忆集呢，因为新生代的更新速度较快，维护成本高，而且新生代gc后的存活对象较少，不如gc后直接扫描新生代的对象的引用关系。**

记忆集是一种用于记录从非收集区域指向收集区域的指针集合的抽象数据结构。最简单的实现可以用非收集区域中所有含跨代引用的对象数组来实现这个数据结 

构，而其中存储的数据代表的精度也需要加以选择，即记录粒度来节省记忆集的存储和维护成本。

> ·字长精度：每个记录精确到一个机器字长（就是处理器的寻址位数，如常见的32位或64位，这个精度决定了机器访问物理内存地址的指针长度），该字包含跨代指针。 
>
> ·对象精度：每个记录精确到一个对象，该对象里有字段含有跨代指针。 
>
> ·卡精度：每个记录精确到一块内存区域，该区域内有对象含有跨代指针。 

“卡精度”有一种称为**“卡表”（Card Table）**的方式去实现记忆集，记忆集其实是一种“抽象”的数据结构，抽象的意思是只定义了记忆集的行为意图，并没有定义其行为的具体实现。卡表就是记忆集的一种具体实现，它定义了记忆集的记录精度、与堆内存的映射关系等。

卡表最简单的形式可以只是一个字节数组：

```java
CARD_TABLE [this address >> 9] = 0;
```

字节数组CARD_TABLE的每一个元素都对应着其标识的内存区域中一块特定大小的内存块，这个内存块被称作**“卡页”（Card Page）**。一般来说，卡页大小都是以2的N次幂的字节数，通过上面代码可以看出HotSpot中使用的卡页是2的9次幂，即512字节（地址右移9位，相当于用地址除以512）。

一个卡页的内存中通常包含**不止一个对象，只要卡页内有一个（或更多）对象的字段存在着跨代指针**，那就**将对应卡表的数组元素的值标识为1**，称为这个元素变脏（Dirty），**没有则标识为0**。在垃圾收集发生时，只要筛选出卡表中变脏的元素，就能轻易得出哪些卡页内存块中包含跨代指针，把它们加入GC Roots中一并扫描.

#### 写屏障

在HotSpot虚拟机里是通过**写屏障（Write Barrier）技术维护卡表状态的**。写屏障可以看作在虚拟机层面对“引用类型字段赋值”这个动作的AOP切面，在引用对象赋值时会产生一个环形（Around）通知，供程序执行额外的动作，也就是说赋值的前后都在写屏障的覆盖范畴内。在赋值前的部分的写屏障叫作写前屏障（Pre-Write Barrier），在赋值后的则叫作写后屏障（Post-Write Barrier）。直至G1收集器出现之前，其他收集器都只用到了写后屏障。

应用写屏障后，虚拟机就会为所有赋值操作生成相应的指令

> “伪共享”（False Sharing）问题
>
> 伪共享是处 理并发底层细节时一种经常需要考虑的问题，现代中央处理器的缓存系统中是以缓存行（Cache Line）为单位存储的，当多线程修改互相独立的变量时，如果这些变量恰好共享同一个缓存行，就会彼此影响（写回、无效化或者同步）而导致性能降低，这就是伪共享问题.
>
> 假设处理器的缓存行大小为64字节，由于一个卡表元素占1个字节，64个卡表元素将共享同一个缓存行。这64个卡表元素对应的卡页总的内存为32KB（64×512字节），也就是说如果**不同线程更新的对象正好处于这32KB的内存区域内**，就会导致更新卡表时正好写入同一个缓存行而影响性能。为了避免伪共享问题
>
> 一种简单的解决方案是不采用无条件的写屏障，而是先检查卡表标记，只有当该卡表元素未被标记过时才将其标记为变脏。
>
> （参数-XX：+UseCondCardMark，用来决定是否开启卡表更新的条件判断。开启会增加一次额外判断的开销，但能够避免伪共享问题）

#### 并发的可达性分析

可达性分析算法理论上要求都基于一个能保障一致性的快照中才能够进行分析。

> 1. 冻结全部用户线程
> 2. 并发用户线程但要破坏“对象消失”问题

由于GC Roots相比起整个Java堆中全部的对象毕竟还算是极少数，且在各种优化技巧（如OopMap）的加持下，它带来的停顿已经是非常短暂且相对固定（不随堆容量而增长）的了。而遍历之后依据GC Roots的对象图则会与Java堆容量直接成正比例关系了，若是能并发则延迟降低收益将提升较大。

想解决或者降低用户线程的停顿，就要先搞清楚为什么必须在一个能保障一致性的快照上才能进行对象图的遍历？为了能解释清楚这个问题，我们引入三色标记（Tri-color Marking）作为工具来辅助推导，把遍历对象图过程中遇到的对象，按照“是否访问过”这个条件标记成以下三种颜色： 

> ·白色：表示对象尚未被垃圾收集器访问过。显然在可达性分析刚刚开始的阶段，所有的对象都是白色的，若在分析结束的阶段，仍然是白色的对象，即代表不可达。 
>
> ·黑色：表示对象已经被垃圾收集器访问过，且这个对象的所有引用都已经扫描过。黑色的对象代表已经扫描过，它是安全存活的，如果有其他对象引用指向了黑色对象，无须重新扫描一遍。黑色对象不可能直接（不经过灰色对象）指向某个白色对象。 
>
> ·灰色：表示对象已经被垃圾收集器访问过，但这个对象上至少存在一个引用还没有被扫描过。 

收集器在对象图上标记颜色，同时用户线程在修改引用关系——即修改对象图的结构，这样可能出现两种后果。一种是**把原本消亡的对象错误标记为存活**，这不是好事，但其实是可以容忍的，只不过产生了一点逃过本次收集的==浮动垃圾==而已，下次收集清理掉就好。另一种是==把原本存活的对象错误标记为已消亡==，这就是非常致命的后果了，程序肯定会因此发生错误

![image-20201004103437136](F:\Typora数据储存\高级语言\JAVA\JVM简介.assets\image-20201004103437136.png)

Wilson于1994年在理论上证明了，当且仅当以下两个条件同时满足时，会产生“对象消失”的问题，即原本应该是黑色的对象被误标为白色： 

> ·赋值器插入了一条或多条从黑色对象到白色对象的新引用； 
>
> ·赋值器删除了全部从灰色对象到该白色对象的直接或间接引用。



要解决并发扫描时的对象消失问题，只需破坏这两个条件的任意一个即可。由此分别产生了两种解决方案：==增量更新（Incremental Update）和原始快照（Snapshot At The Beginning，SATB）。== 

> 增量更新要破坏的是第一个条件，当黑色对象插入新的指向白色对象的引用关系时，就将这个新插入的引用记录下来，等并发扫描结束之后，再将这些记录过的引用关系中的黑色对象为根，重新扫描一次。这可以简化理解为，黑色对象一旦新插入了指向白色对象的引用之后，它就变回灰色对象了。
>
> 原始快照要破坏的是第二个条件，当灰色对象要删除指向白色对象的引用关系时，就将这个**要删除的引用记录下来**，在并发扫描结束之后，再将这些记录过的引用关系中的灰色对象为根，**重新扫描一次**。这也可以简化理解为，无论引用关系删除与否，都会按照刚刚开始扫描那一刻的对象图快照来进行搜索。 

**但是要注意的是破坏一个必要条件并不是解决了所有的问题了，还有其他的衍生问题如如何减少破坏条件1的浮动垃圾，破坏条件2时新增的黑色对象的引用要如何处理（G1单独划分空间，新增的放入这里并默认为全部存活），上面的只是一个要必须主干，还需要很多的完善措施**

> 已有对象本来已经删除了引用，GC开始后，突然被黑的引用？(不知道在破坏第二种条件下，这种如何处理，)
>
> 新增对象？（破坏第二个条件不足满足，需特殊处理）

### 6、经典垃圾收集器

如果说垃圾回收算法是内存回收的方法论，那么垃圾收集器就是具体实现。jvm会结合针对不同的场景及用户的配置使用不同的收集器。

```css
年轻代收集器
Serial、ParNew、Parallel Scavenge
老年代收集器
Serial Old、Parallel Old、CMS收集器
混合收集器
G1收集器
```

![image-20201004125809249](F:\Typora数据储存\高级语言\JAVA\JVM简介.assets\image-20201004125809249.png)

#### Serial

最基本、发展最久的收集器，在jdk3以前是gc收集器的唯一选择，Serial是**单线程收集器**，Serial收集器只能使用一条线程进行收集工作，在收集的时候必须得停掉其它线程，等待收集工作完成其它线程才可以继续工作。

![image-20201004125958637](F:\Typora数据储存\高级语言\JAVA\JVM简介.assets\image-20201004125958637.png)

> 虽然Serial看起来很坑，需停掉别的线程以完成自己的gc工作，但是也不是完全没用的，比如说Serial在运行**在Client模式下优于其它收集器**[简单高效,不过一般都是用Server模式，64bit的jvm甚至没Client模式]，**它是所有收集器里额外内存消耗最小的，没有线程交互的开销。**

> JVM的Client模式与Server模式为JIT的编译优化程度 



优点:

1. 对于Client模式下的jvm来说是个好的选择，它是所有收集器里额外内存消耗最小的，适用于单核CPU【现在基本都是多核了】

缺点:
1. 收集时要暂停其它线程，有点浪费资源，多核下显得鸡肋。



#### ParNew收集器

可以认为是Serial的升级版，因为它**支持多线程[GC线程]**，而且收集算法（标记-复制）、Stop The World、回收策略和Serial一样，就是可以有多个GC线程并发运行，它是HotSpot第一个真正意义实现并发的收集器。默认开启线程数和当前cpu数量相同【几核就是几个】

> 如果cpu核数很多不想用那么多，可以通过*-XX:ParallelGCThreads*来控制垃圾收集线程的数量。

![image-20201004131513703](F:\Typora数据储存\高级语言\JAVA\JVM简介.assets\image-20201004131513703.png)

优点:

1. 支持GC线程多线程，多核CPU下可以充分的利用CPU资源
2. 运行在Server模式下新生代首选的收集器【重点是因为新生代的这几个收集器==只有它和Serial可以配合CMS收集器一起使用==】

> CMS作为老年代的收集器，却无法与JDK 1.4.0中已经存在的新生代收集器ParallelScavenge配合工作[1]，所以在JDK 5中使用CMS来收集老年代的时候，新生代只能选择ParNew或者Serial收集器中的一个。
>
> 除了一个面向低延迟一个面向高吞吐量的目标不一致外，**技术上的原因是Parallel Scavenge收集器及后面提到的G1收集器等都没有使用HotSpot中原本设计的垃圾收集器的分代框架，而选择另外独立实现。**
>
> 以自JDK 9开始，ParNew加CMS收集器的组合就不再是官 推荐的服务端模式下的收集器解决方案了。官方希望它能完全被G1所取代，甚至还取消了ParNew加 Serial Old以及Serial加CMS这两组收集器组合的支持（其实原本也很少人这样使用），并直接取消了-XX：+UseParNewGC参数，这意味着**ParNew和CMS从此只能互相搭配使用，再也没有其他收集器能够和它们配合了**。读者也可以理解为从此以后，ParNew合并入CMS，成为它专门处理新生代的组成部分。ParNew可以说是HotSpot虚拟机中第一款退出历史舞台的垃圾收集器。 

缺点: 

1. 在单核下表现不会比Serial好，由于在单核能利用多核的优势，在线程收集过程中可能会出现频繁上下文切换，导致额外的开销。



#### Parallel Scavenge

采用==标记-复制算法==的收集器，和ParNew一样支持多线程。

但是该收集器重点关心的是吞吐量【吞吐量 = 代码运行时间 / (代码运行时间 + 垃圾收集时间)  如果代码运行100min垃圾收集1min，则为99%】

对于用户界面，适合使用GC停顿时间短,不然因为卡顿导致交互界面卡顿将很影响用户体验。

对于后台，高吞吐量可以高效率的利用cpu尽快完成程序运算任务，适合后台运算

> Parallel Scavenge收集器提供了两个参数用于精确控制吞吐量，分别是控制**最大垃圾收集停顿时间的-XX：MaxGCPauseMillis参数以及直接设置吞吐量大小的-XX：GCTimeRatio参数。**
>
> Parallel Scavenge收集器还有一个参数**-XX：+UseAdaptiveSizePolicy**值得我们关注。这是一个开关参数，当这个参数被激活之后，就不需要人工指定新生代的大小（-Xmn）、Eden与Survivor区 的比例（-XX：SurvivorRatio）、晋升老年代对象大小（-XX：PretenureSizeThreshold）等细节参数了，虚拟机会根据当前系统的运行情况收集性能监控信息，动态调整这些参数以提供最合适的停顿时间或者最大的吞吐量。这种调节方式称为**垃圾收集的自适应的调节策略**（GC Ergonomics）
>
> **只需要把基本的内存数据设置好（如-Xmx设置最大堆），然后使用-XX：MaxGCPauseMillis参数（更关注最大停顿时间）或-XX：GCTimeRatio（更关注吞吐量）参数给虚拟机设立一个优化目标，那具体细节参数的调节工作就由虚拟机完成了。**



#### Serial Old

和新生代的Serial一样为单线程，Serial的老年代版本，不过它采用"==标记-整理算法"==，这个模式主要是给==Client模式==下的JVM使用。

如果是Server模式有两大用途

1.**jdk5前**和Parallel Scavenge搭配使用，jdk5前也只有这个老年代收集器可以和它搭配。

2.作为CMS收集器的后备（浮动垃圾太多导致容纳不了大对象时）。

![image-20201004155240230](F:\Typora数据储存\高级语言\JAVA\JVM简介.assets\image-20201004155240230.png)

#### Parallel Old

支持多线程，Parallel Scavenge的老年版本，jdk6开始出现， 采用=="标记-整理算法"==【老年代的收集器大都采用此算法】

在jdk6以前，新生代的Parallel Scavenge只能和Serial Old配合使用【没有这个的话只剩Serial Old，而Parallel Scavenge又不能和CMS配合使用】，而且Serial Old为单线程Server模式下会拖后腿【多核cpu下无法充分利用】，这种结合并不能让应用的吞吐量最大化。

> Parallel Old的出现结合Parallel Scavenge，真正的形成“吞吐量优先”的收集器组合，在注重吞吐量或者处理器资源较为稀缺的场合考虑

![image-20201004155436142](F:\Typora数据储存\高级语言\JAVA\JVM简介.assets\image-20201004155436142.png)

#### CMS

CMS收集器(Concurrent Mark Sweep)是以一种**获取最短回收停顿时间为目标**的收集器。【重视响应，可以带来好的用户体验，被sun称为并发低停顿收集器】

> **cms收集器是否会扫描年轻代**？
>
> 会，在初始标记的时候会扫描新生代。
>
> 虽然cms是老年代收集器，但是我们知道年轻代的对象是可以晋升为老年代的，为了空间分配担保，还是有必要去扫描年轻代。

```css
启用CMS：-XX:+UseConcMarkSweepGC
```

正如其名，CMS采用的是=="标记-清除"(Mark Sweep)==算法，而且是支持并发(Concurrent)的

它的运作分为4个阶段

> 1.初始标记:标记一下GC Roots能直接关联到的对象，速度很快
> 2.并发标记:GC Roots Tarcing过程，即可达性分析
> 3.重新标记:为了修正因并发标记期间用户程序运作而产生变动的那一部分对象的标记记录（增量更新），会有些许停顿，时间上一般 初始标记 < 重新标记 < 并发标记
> 4.并发清除

以上==初始标记和重新标记需要stw==(停掉用户线程)

![image-20201005095146584](F:\Typora数据储存\高级语言\JAVA\JVM简介.assets\image-20201005095146584.png)

之所以说CMS的用户体验好，是因为CMS收集器的内存回收工作是可以和用户线程一起并发执行。

总体上CMS是款优秀的收集器，但是它也有些缺点。

> 1. cms堆cpu特别敏感，cms运行线程和应用程序并发执行需要多核cpu，如果cpu核数多的话可以发挥它并发执行的优势，但是cms默认配置启动的时候垃圾线程数为 (cpu数量+3)/4，它的性能很容易受cpu核数影响，当cpu的数目少的时候比如说为为2核，如果这个时候cpu运算压力比较大，还要分一半给cms运作，这可能会很大程度的影响到计算机性能。
>
> 2. 为了并发用户线程的需要，需要预留足够内存空间提供给用户线程使用，为了解决这个问题cms提供了-XX：CMSInitiatingOccu-pancyFraction的选项，为CMS收集器启动阈值，到了JDK 6时，CMS收集器的启动阈值就已经默认提升至92%。
>
> 3. 若浮动垃圾或预留空间的不足导致分配对象失败（增量更新的一大缺点，黑色引用断裂但不会变白），可能导致Concurrent Mode Failure（并发模式故障）而触发full GC，会冻结用户线程的执行，临时启用Serial Old收集器来重新进行老年代的垃圾收集。
>
> 4. 空间碎片过于严重无法存储大对象时也会触发Full GC





#### G1收集器

G1(garbage first:尽可能多收垃圾，避免full gc)收集器HotSpot开发团队最初赋予它的期望是（在比较长期的）未来可以替换掉JDK 5中发布的CMS收集器。现在这个期望目标已经实现过半了，**JDK 9**发布之日，G1宣告取代Parallel Scavenge加Parallel Old组合，**成为服务端模式下的默认垃圾收集器**，而CMS则沦落至被声明为不推荐使用（Deprecate）的收集器。（JDK 10 的“统一垃圾收集器接口”，也是为如CMS等的退出铺路）



G1是基于“停顿时间模型”实现的，停顿时间模型的意思是能够支持**指定在一个长度为M毫秒的时间片段内，消耗在垃圾收集上的时间大概率不超过N毫秒**这样的目标，这几乎已经是实时Java（RTSJ）的中软实时垃圾收集器特征了。 

G1面向堆内存任何部分来组成**回收集（Collection Set，一般简称CSet）**进行回收，衡量标准不再是它属于哪个分代，而是哪块内存中存放的垃圾数量最多，回收收益最大，这就是G1收集器的Mixed GC模式。



而实现上述的目标，G1开创的基于**Region的堆内存布局是它能够实现这个目标的关键**。

G1不再坚持固定大小及固定数量的分代区域划分，而是把连续的Java堆划分为多个大小相等的独立区域（Region），每一个Region都可以根据需要，扮演新生代的Eden空间、Survivor空间，或者老年代空间。==收集器能够对扮演不同角色的Region采用不同的策略去处理，这样可以根据我们的延迟要求去选取Region回收，实现我们低停顿时间的要求，而若是所有Region都进行回收，延迟则说不定比之前固定分代大小的更大。==



Region中还有一类特殊的Humongous区域，专门用来存储大对象。G1认为只要大小**超过了一个Region容量一半的对象即可判定为大对象**。每个Region的大小可以通过参数-XX：G1HeapRegionSize设定，取值范围为1MB～32MB，且应为2的N次幂。而对于那些**超过了整个Region容量的超级大对象，将会被存放在N个连续的Humongous Region之中**，G1的大多数行为都把Humongous Region作为老年代的一部分来进行看待。



G1收集器==之所以能建立可预测的停顿时间模型==，是因为它将Region作为单次回收的最小单元，即每次收集到的内存空间都是Region大小的整数倍，这样可以有计划地避免在整个Java堆中进行全区域的垃圾收集。更具体的处理思路是让G1收集器去跟踪各个Region里面的垃圾堆积的“价值”大小，价值即回收所获得的空间大小以及回收所需时间的经验值，然后在后台维护一个优先级列表，每次根据用户设定允许的收集停顿时间（使用参数-XX：MaxGCPauseMillis指定，默认值是200毫秒），优先处理回收价值收益最大的那些Region，这也就是“Garbage First”名字的由来。



![image-20201005105237150](F:\Typora数据储存\高级语言\JAVA\JVM简介.assets\image-20201005105237150.png)



**G1收集器需要解决的关键问题：**

1. Region里面存在的跨Region引用对象如何解决？

> 每个Region都有维护自己的记忆集，这些记忆集会记录下别的Region指向自己的指针，并标记这些指针分别在哪些卡页范围之内。G1的记忆集在存储结构的本质上是一种哈希表，key是别的Region的起始地址（减少需要记录全部Region），Value是一个集合，里面存储的元素是卡表的索引号（一个Region可以分为多个卡表项）。**key别人指向自己，value自己指向别人。**
>
> 这样也导致G1收集器比其他的传统收集器有着更高的内存占用负担，至少要耗费大约相当于Java堆容量10%至20%的额外内存来维持收集器工作。

2. 并发阶段保证收集线程与用户线程互不干扰地运行？

> CMS收集器采用增量更新算法实现，而G1收集器通过原始快照（STAB）来实现。
>
> 新对象被创建时，G1为每一个Region设计了名为TAMS（Top at Mark Start）的指针，并发回收时新分配的对象地址都必须要在这两个指针以上
>
> **G1收集器默认在这个地址以上的对象是被隐式标记过的，即默认它们是存活的，不纳入回收范围。**与CMS中的“Concurrent Mode Failure”失败会导致Full GC类似，如果内存回收的速度赶不上内存分配的速度，G1收集器也要被迫冻结用户线程执行，导致Full GC而产生长时间“Stop The World”。 

3. 可靠的停顿预测模型

> 不同于Parallel Scavenge -XX:MaxGCPauseMillis参数**直接控制新生代大小**来完成最长停顿时间的控制，G1的停顿预测模型是以衰减均值（Decaying Average）为理论基础来实现的，在垃圾收集过程中，G1收集器会记录每个Region的回收耗时、每个Region记忆集里的脏卡数量等各个可测量的步骤花费的成本，并分析得 出平均值、标准偏差、置信度等统计信息。这里强调的“衰减平均值”是指它会比普通的平均值更容易 受到新数据的影响，平均值代表整体平均状态，但==衰减平均值更准确地代表“最近的”平均状态==。换句话说，Region的统计状态越新越能决定其回收的价值。然后通过这些信息预测现在开始回收的话，由哪些Region组成回收集才可以在不超过期望停顿时间的约束下获得最高的收益。 
>
> g是自适应的回收器，提供了若干个默认值，无需修改就可高效运作
> -XX:G1HeapRegionSize=n  设置g1 region大小，不设置的话自己会根据堆大小算，目标是根据最小堆内存划分2048个区域
> -XX:MaxGCPauseMillis=200 最大停顿时间 默认200毫秒

**G1 收集的四个步骤：**

**·初始标记（Initial Marking）**：仅仅只是标记一下**GC Roots能直接关联到的对象，并且修改TAMS指针的值**，让下一阶段用户线程并发运行时，能正确地在可用的Region中分配新对象。这个阶段**需要停顿线程**，但耗时很短，而且是**借用进行Minor GC的时候同步完成的**（新生代的仍是标记-复制算法，**==且全程STW==**，因为存活对象较少需要标记的时间较短且复制时需要停顿，仍会停止用户线程来完成），所以G1收集器在这个阶段实际并没有额外的停顿。 

**·并发标记（Concurrent Marking）**：从GC Root开始**对堆中对象进行可达性分析**，递归扫描整个堆里的对象图，找出要回收的对象，这阶段耗时较长，但可与用户程序并发执行。当对象图扫描完成以后，还要重新处理SATB记录下的在并发时有引用变动的对象。 

**·最终标记（Final Marking）**：对用户线程做另一个短暂的暂停，用于**处理并发阶段结束后仍遗留下来的最后那少量的SATB记录。** 

**·筛选回收（Live Data Counting and Evacuation）**：负责更新Region的统计数据，对各个Region的回收价值和成本进行排序，根据用户所期望的停顿时间来制定回收计划，可以自由选择任意多个**Region构成回收集**，然后把决定回收的那一部分Region的存活对象复制到空的Region中，再清理掉整个旧Region的全部空间。这里的操作涉及存活对象的移动，是**必须暂停用户线程，由多条收集器线程并行完成的**。

![image-20201005161759703](F:\Typora数据储存\高级语言\JAVA\JVM简介.assets\image-20201005161759703.png)

G1与CMS

> 1. 与CMS 的“标记-清除”算法不同，G1==从整体来看是基于“标记-整理”算法==实现的收集器，但从**局部（两个Region之间）上看又是基于“标记-复制”算法实现**，无论如何，这两种算法都意味着G1运作期间不会产生内存空间碎片，垃圾收集完成之后能提供规整的可用内存。这种特性有利于程序长时间运行，在程序为大对象分配内存时不容易因无法找到连续内存空间而提前触发下一次收集
>
> 2. G1无论是为了垃圾收集产生的**内存占用（Footprint）**还是程序运行时的**额外执行负载 （Overload）**都要比CMS要==高。== 
>
>    **内存占用方面：**
>
>    虽然G1和CMS都使用卡表来处理跨代指针，但G1的卡表实现更为复杂，而且堆中**每个Region**，无论扮演的是新生代还是老年代角色，**都必须有一份卡表**，这导致G1的记忆集（和 其他内存消耗）可能会占整个堆容量的20%乃至更多的内存空间；相比起来**CMS的卡表就相当简单， 只有唯一一份**，**而且只需要处理老年代到新生代的引用，反过来则不需要**，由于新生代的对象具有朝生夕灭的不稳定性，引用变化频繁，能省下这个区域的维护开销是很划算的
>
>    * 为什么只有老年代到新生代的引用呢，因为新生代存活少，这意味着维护记忆集的操作将很频繁，不如GC一次新生代再全部扫描一次来的效率高
>
>    **执行负载方面：**
>
>    它们都使用到写屏障，**CMS用写后屏障来更新维护卡表**；而G1**除了使用写后屏障**来进行同样的（由于G1的卡表结构复杂，其实是更烦琐的）卡表维护操作外，为了实现原始快照搜索 （SATB）算法，**还需要使用写前屏障来跟踪并发时的指针变化情况。**
>
>    * 相比起增量更新算法，**原始快照搜索能够减少并发标记和重新标记阶段的消耗**，避免CMS那样在最终标记阶段停顿时间过长的缺点，但是在用户程序运行过程中确实会产生由跟踪引用变化带来的额外负担。
>
>    由于G1对写屏障的复杂操作要比CMS消耗更多的运算资源，所以CMS的**写屏障实现是直接的同步操作**，而G1就不得不将其实现为类似于消息队列的结构，把**写前屏障和写后屏障中要做的事情都放到队列里，然后再异步处理。** 
>
>    目前在**小内存应用上CMS的表现大概率仍然要会优于G1，而在大内存应用上G1则大多能发挥其优势**，这个优劣势的Java堆容量平衡点通常在6GB至8GB之间

### 7、低延迟收集器

在==内存占用、吞吐量和延迟==这三项指标里，延迟的重要性日益凸显

> 其原因是随着计算机硬件的发展、性能的提升，我们越来越能容忍收集器多占用一点点内存；硬件性能增长，对软件系统的处理能力是有直接助益的，硬件的规格和性能越高，也有助于降低收集器运行时对应用程序的影响，换句话说，吞吐量会更高。但对延迟则不是这样，硬件规格提升，准确地说是**内存的扩大，对延迟反而会带来负面的效果**，这点也是很符合直观思维的：虚拟机要回收完整的1TB的堆内存，毫无疑问要比回收1GB的堆内存耗费更多时间。

**已接触过的垃圾收集器的停顿状况**

![image-20201005171445753](F:\Typora数据储存\高级语言\JAVA\JVM简介.assets\image-20201005171445753.png)

> CMS使用标记-清除算法，虽然避免了整理阶段收集器带来的停顿，但是清除算法不论如何优化改进，在设计原理上避免不了空间碎片的产生，随着空间碎片不断淤积最终依然逃不过“Stop The World”的命运。G1虽然可以按更小的粒度进行回收，从而抑制整理阶段出现时间过长的停顿，但毕竟也还是要暂停的。 

实现垃圾收集的停顿都不超过十毫秒这种以前听起来是天方夜谭、匪夷所思的目标。

> Young GC中经典的垃圾收集器因为复制移动对象与都需要STW，Old GC即使CMS在最后为了吞吐量或者处理浮动垃圾也需要用标记-整理方法，移动对象与更新引用时，也需要STW

#### Shenandoah收集器

那Shenandoah作为第一款不由Oracle（包括以前的Sun）公司的虚拟机团队所领导开发的HotSpot垃圾收集器，由RedHat开发而成，Shenandoah是一款只有OpenJDK才会包含，而OracleJDK里反而不存在的收集器。

Shenandoah反而更像是G1的下一代继承者，**甚至代码可以互相借鉴，因此可以互相修改**，但Shenandoah还不支持分代，因为工作量和优先级问题。

**Initial Partial**

**Concurrent Partial**

**Final Partial**

以上可以不严谨地定义为Minor GC的工作

**·初始标记（Initial Marking）：**与G1一样，首先标记与GC Roots直接关联的对象，**这个阶段仍是“Stop The World”的**，但停顿时间与堆大小无关，只与GC Roots的数量相关。 

**·并发标记（Concurrent Marking）：**与G1一样，**遍历对象图**，标记出全部可达的对象，这个阶段是与用户线程一起并发的，时间长短取决于堆中存活对象的数量以及对象图的结构复杂程度。 

> Shenandoah摒弃了在G1中耗费大量内存和计算资源去维护的记忆集，改用名为“连接矩阵”（ConnectionMatrix）的全局数据结构来记录跨Region的引用关系，降低了处理跨代指针时的记忆集维护消耗，也降 低了伪共享问题（见3.4.4节）的发生概率。

**·最终标记（Final Marking）：**与G1一样，**处理剩余的SATB扫描**，并在这个阶段**统计出回收价值最高的Region**，将这些Region构成一组**回收集（Collection Set）**。**最终标记阶段也会有一小段短暂的停顿。**

**·并发清理（Concurrent Cleanup）：**这个阶段用于清理那些整个区域内连**一个存活对象都没有找到**的Region（这类Region被称为Immediate Garbage Region）。 

==**·并发回收（Concurrent Evacuation）：**==并发回收阶段是Shenandoah与之前HotSpot中其他收集器的核心差异。在这个阶段，Shenandoah要把回收集里面的存活对象先复制一份到其他未被使用的Region之 中。**复制对象**这件事情如果将用户线程冻结起来再做那是相当简单的，但如果两者必须要**同时并发进行**的话，就变得复杂起来了。其困难点是在移动对象的同时，用户线程仍然可能不停对被移动的对象进行读写访问，移动对象是一次性的行为，但移动之后整个内存中所有指向该对象的引用都还是旧对象的地址，这是很难一瞬间全部改变过来的。对于并发回收阶段遇到的这些困难，**Shenandoah将会通过读屏障和被称为“Brooks Pointers”的转发指针来解决**。并发回收阶段运行的时间长短取决于回收集的大小。 （用Brooks Pointers而不是直接更改handle）

**·初始引用更新（Initial Update Reference）：**并发回收阶段复制对象结束后，还需要把堆中所有指向旧对象的引用修正到复制后的新地址，这个操作称为引用更新。引用更新的初始化阶段实际上并未做什么具体的处理，设立这个阶段**只是为了建立一个线程集合点**，**确保所有并发回收阶段中进行的收集器线程都已完成分配给它们的对象移动任务**而已。初始引用更新时间很短，会产生一个非常短暂的停顿。

**·并发引用更新（Concurrent Update Reference）：**真正开始进行引用更新操作，这个阶段是**与用户线程一起并发**的，时间长短取决于内存中涉及的引用数量的多少。并发引用更新与并发标记不同，它不再需要沿着对象图来搜索，**只需要按照内存物理地址的顺序，线性地搜索出引用类型，把旧值改为新值即可**。 

**·最终引用更新（Final Update Reference）：**解决了堆中的引用更新后，还要**修正存在于GC Roots中的引用**。这个阶段是**Shenandoah的最后一次停顿**，停顿时间只与GC Roots的数量相关。 

**·并发清理（Concurrent Cleanup）：**经过并发回收和引用更新之后，整个**回收集**中所有的Region已再无存活对象，这些Region都变成Immediate Garbage Regions了，最后再调用一次**并发清理过程来回收这些Region的内存空间**，供以后新对象分配使用。

![image-20201006094826933](F:\Typora数据储存\高级语言\JAVA\JVM简介.assets\image-20201006094826933.png)

**Shenandoah用以支持并行整理的核心概念**

> 使用转发指针（Forwarding Pointer，也常被称为Indirection Pointer）来实现对象移动与用户程序并发的一种解决方案。此前，要做类似的并发操作，通常是在被移动对象原有的内存上设置保护陷阱（MemoryProtection Trap），一旦用户程序访问到归属于旧对象的内存空间就会产生**自陷中断**，进入预设好的异常处理器中，再由其中的代码逻辑把访问转发到复制后的新对象上。虽然确实能够实现对象移动与用户线程并发，但是如果没有操作系统层面的直接支持，这种方案将导致**用户态频繁切换到核心态**，代价是非常大的，不能频繁使用。
>
> Brooks提出的新方案不需要用到内存保护陷阱，而是在原有对象布局结构的最前面统一增加一个新的引用字段，在正常不处于并发移动的情况下，该引用指向对象自己
>
> ![image-20201006104248986](F:\Typora数据储存\高级语言\JAVA\JVM简介.assets\image-20201006104248986.png)
>
> 缺点：
>
> 1. 每次对象访问会带来一次额外的转向开销
>
> 2. 多线程竞争问题（与volatile 又不太一样，volatile 主要是防止重排序的作用，这边正常流程中间可能被别的线程并发操作）
>
>    如出现下列情况：
>
>    1）收集器线程复制了新的对象副本； 
>
>    2）用户线程更新对象的某个字段； （将导致更改发生在旧对象上）
>
>    3）收集器线程更新转发指针的引用值为新副本地址。 
>
>    所以这里必须针对转发指针的访问操作采取同步措施，让收集器线程或者用户线程对转发指针的访问只有其中之一能够成功，另外一个必须等待，避免两者交替进行。实际上Shenandoah收集器是**通过比较并交换（Compare And Swap，CAS）操作来保证并发时对象的访问正确性的**
>
> 3. 为了实现Brooks Pointer，Shenandoah在读、写屏障中都加入了额外的转发处理，尤其是使用读屏障的代价，这是比写屏障更大的。
>
>    数量庞大的读屏障带来的性能开销会是Shenandoah被诟病的关键点之一，所以计划在JDK 13中将Shenandoah的内存屏障模型**改进为基于引用访问屏障（Load Reference Barrier）的实现**，所谓“引用访问屏障”是指内存屏障**只拦截对象中数据类型为引用类型的读写操作**，而不去管原生数据类型等其他非引用字段的读写，这能够省去大量对原生类型、对象比较、对象加锁等场景中设置内存屏障所带来的消耗。 

#### ZGC收集器

ZGC和Shenandoah的目标是高度相似的，都希望在尽可能对吞吐量影响不太大的前提下，实现在任意堆内存大小下都可以把垃圾收集的停顿时间限制在十毫秒以内的低延迟。

ZGC收集器是一款基于Region内存布局的，（暂时）不设分代的，使用了读屏障、染色指针和内存多重映射等技术来实现可并发的标记-整理算法的，以低延迟为首要目标的一款垃圾收集器。

**ZGC的内存布局**

与Shenandoah和G1一样，ZGC也采用基于Region的堆内存布局， 与它们不同的是，ZGC的Region（在一些官方资料中将它称为Page或者ZPage，本章为行文一致继续称为Region）**具有动态性——动态创建和销毁，以及动态的区域容量大小。**（Shenandoah和G1是划分好了的，只是其中包含着Humongous Region）

> ·小型Region（Small Region）：容量固定为2MB，用于放置小于256KB的小对象。 
>
> ·中型Region（Medium Region）：容量固定为32MB，用于放置大于等于256KB但小于4MB的对象。
>
> ·大型Region（Large Region）：容量不固定，可以动态变化，但必须为2MB的整数倍，用于**放置4MB或以上的大对象**。**每个大型Region中只会存放一个大对象**，这也预示着虽然名字叫作“大型Region”，但**它的实际容量完全有可能小于中型Region**，最小容量可低至4MB。大型Region在ZGC的实现中是不会被重分配（重分配是ZGC的一种处理动作，用于复制对象的收集器阶段，稍后会介绍到）的，因为复制一个大对象的代价非常高昂。

**并发整理算法的实现**

ZGC收集器有一个标志性的设计是它采用的**染色指针技术（Colored Pointer**，其他类似的技术中可能将它称为Tag Pointer或者Version Pointer）

ZGC如何跟shenandoah一样实现并发地整理呢，不同于shenandoah在并发回收的时候的为旧Region添加转发指针，其通过染色指针在对象标记时就开始为后面的整理的并发做准备了。

从前，如果我们要在**对象上存储**一些额外的、只供收集器或者虚拟机本身使用的数据，通常会在对象头中增加额外的存储字段，如对象的哈希码、分代年龄、锁记录等就是这样存储的。这种记录方式在**有对象访问的场景下**是很自然流畅的，不会有什么额外负担。

而对于对象移动和引用关系的判断，我们不能保证能否正确访问到对象时，就不能保证对象上的信息能读取到，我们希望能从指针或者与对象内存无关的地方得到这些信息，这样能提高判断的准确性与速度

例如引用关系上，追踪式收集算法的标记阶段就**可能存在**只跟指针打交道而不必涉及指针所引用的对象本身的场景，如对象标记阶段的过程中需要给对象打上三色标记，**这些标记本质上就只和对象的引用有关，而与对象本身无关**

HotSpot虚拟机的几种收集器有不同的标记实现方案，有的把标记直接记录在对象头上（如Serial收集器，仍与对象有关，但不与用户线程并发，降低了标记时对象信息改变破坏标记的可能），有的把标记记录在与对象相互独立的数据结构上（如G1、Shenandoah使用了一种相当于堆内存的1/64大小的，称为BitMap的结构来记录标记信息），而ZGC的染色指针是最直接的、最纯粹的，它直接把标记信息记在引用对象的指针上

==染色指针是一种直接将少量额外的信息存储在指针上的技术==(从理论上来说G1也可以强制实现一个普通地址的转发表，但是用户线程访问时要如何保证效率呢，需要每次都去查询是否在重分配集还是每个都去转发表查询？用户线程效率下降较低；shenandoah的在原始对象的布局上加的转发指针BrooksPointer，而若是在旧对象的转发指针则需要自陷中断（需要陷入内核态，开销较大）)

> 为什么指针本身也可以存储额外信息呢？在64位系统中，理论可以访问的内存高达16EB（2的64次幂）字节。实际上，基于需求（用不到那么多内存）、性能（地址越宽在做地址转换时需要的页表级数越多）和成本（消耗更多晶 体管）的考虑，**在AMD64架构中只支持到52位（4PB）的地址总线和48位（256TB）的虚拟地址空 间，所以目前64位的硬件实际能够支持的最大内存只有256TB**。此外，操作系统一侧也还会施加自己的约束，**64位的Linux则分别支持47位（128TB）的进程虚拟地址空间和46位（64TB）的物理地址空间，64位的Windows系统甚至只支持44位（16TB）的物理地址空间。** 
>
> 尽管Linux下64位指针的高18位不能用来寻址，但剩余的46位指针所能支持的64TB内存在今天仍然能够充分满足大型服务器的需要。鉴于此，ZGC的染色指针技术继续盯上了这剩下的46位指针宽度，**将其高4位提取出来存储四个标志信息**。通过这些标志位，虚拟机可以直接**从指针中看到其引用对象的三色标记状态、是否进入了重分配集（即被移动过）、是否只能通过finalize()方法才能被访问到**。当然，由于这些标志位进一步压缩了原本就只有46位的地址空间，也直接导致ZGC能够管理的内存**不可以超过4TB**（2的42次幂）。 
>
> ![image-20201007140246930](F:\Typora数据储存\高级语言\JAVA\JVM简介.assets\image-20201007140246930.png)

虽然染色指针有4TB的内存限制，不能支持32位平台，不能支持压缩指针（-XX： +UseCompressedOops）等诸多约束，但它带来的收益也是非常可观的，在JEP 333的描述页中，ZGC的设计者Per Liden在“描述”小节里花了全文过半的篇幅来陈述染色指针的三大优势：

> * ==染色指针可以使得一旦某个Region的存活对象被移走之后，这个Region立即就能够被释放和重用掉==，而不必等待整个堆中所有指向该Region的引用都被修正后才能清理。（通过自愈特性），而Shenandoah需要**等到引用更新阶段结束以后才能释放回收集中的Region**，这意味着堆中**几乎所有对象都存活的极端情况**，需要1∶1复制对象到新Region的话，**就必须要有一半的空闲Region来完成收集**。
> * ==染色指针可以大幅减少在垃圾收集过程中内存屏障的使用数量==，设置内存屏障，尤其是写屏障的目的通常是为了记录对象引用的变动情况，到目前为止ZGC都并未使用任何写屏障，只使用了读屏障（一部分是染色指针的功劳，一部分是ZGC现在还不支持分代收集，天然就没有跨代引用的问题）。
>
> * 染色指针可以作为一种可扩展的存储结构用来记录更多与对象标记、重定位过程相关的数据，以便日后进一步提高性能。如在Linux下的64位指针还有前18位并未使用

要顺利应用染色指针有一个必须解决的前置问题：Java虚拟机作为一个普普通通的进程，这样随意重新定义内存中某些指针的其中几位，操作系统是否支持？处理器是否支持？这是很现实的问题，无论中间过程如何，程序代码最终都要转换为机器指令流交付给处理器去执行，**处理器**可不会管指令流中的指针哪部分存的是标志位，哪部分才是真正的寻址地址，**只会把整个指针都视作一个内存地址来对待**。

> * 在Solaris/SPARC平台上比较容易解决，因为SPARC硬件层面本身就支持虚拟地址掩码，设置之后其机器指令**直接就可以忽略掉染色指针中的标志位**。
>
> * x86计算机体系中，处理器会使用分页管理机制把线性地址空间和物理地址空间分别划分为大小相同的块，这样的内存块被称为“页”（Page）。通过在线性虚拟空间的页与物理地址空间的页之间建立的映射表，分页管理机制会进行线性地址到物理地址空间的映射，完成线性地址到物理地址的转换。
>
>   Linux/x86-64平台上的ZGC使用了**多重映射（Multi-Mapping）**将**多个不同的虚拟内存地址映射到同一个物理内存地址**上，这是一种**多对一映射**，意味着ZGC在虚拟内存中看到的地址空间要比实际的堆内存容量来得**更大**。把**染色指针中的标志位看作是地址的分段符**，那只要将这些不同的地址段都映射到同一个物理内存空间，经过多重映射转换后，就可以使用染色指针正常进行寻址了
>
>   ![image-20201007143033486](F:\Typora数据储存\高级语言\JAVA\JVM简介.assets\image-20201007143033486.png)

**ZGC的运作过程：**

> ![image-20201007145728965](F:\Typora数据储存\高级语言\JAVA\JVM简介.assets\image-20201007145728965.png)
>
> **Pause Mark Start**： GC Roots 扫描
>
> **并发标记（Concurrent Mark）**：与G1、Shenandoah一样，并发标记是遍历对象图做可达性分析的阶段，前后也要经过类似于G1、Shenandoah的初始标记、最终标记（尽管ZGC中的名字不叫这些）的 短暂停顿，而且这些停顿阶段所做的事情在目标上也是相类似的。与G1、Shenandoah不同的是，ZGC的标记是在指针上而不是在对象上进行的，**标记阶段会更新染色指针中的Marked 0、Marked 1标志位。**
>
> **Pause Mark End**：处理STAB或增量更新剩余的Region
>
> **并发预备重分配（Concurrent Prepare for Relocate）**：这个阶段需要**根据特定的查询条件**统计得出本次收集过程要清理哪些Region，将这些Region组成重分配集（Relocation Set）。重分配集与G1收集器 的回收集（Collection Set）还是有区别的，ZGC划分Region的目的并非为了像G1那样做收益优先的增量回收。相反，**ZGC每次回收都会扫描所有的Region**，用范围更大的扫描成本换取省去G1中记忆集的维护成本。（G1会依据卡表中的dirty进行判断扫描）此外，在JDK 12的ZGC中开始支持的类卸载以及弱引用的处理，也是在这个阶段中完成的。 
>
> **Pause Relocate Start**： 等待所以线程都预备重分配完成
>
> **并发重分配（Concurrent Relocate**）：**重分配是ZGC执行过程中的核心阶段**，这个过程要**把重分配集中的存活对象复制到新的Region上，并为重分配集中的每个Region维护一个转发表（ForwardTable），记录从旧对象到新对象的转向关系。**得益于染色指针的支持，ZGC收集器能仅从引用上就明确得知一个对象是否处于重分配集之中，**如果用户线程此时并发访问了位于重分配集中的对象**，这次访问将会被预置的内存屏障所截获，然后立即根据Region上的转发表记录将访问转发到新复制的对象上，==并同时修正更新该引用的值==，使其直接指向新对象，ZGC将这种行为称为指针的**“自愈”（Self-Healing）能力。**
>
> > 这样做的好处是**只有第一次访问旧对象会陷入转发**，也就是只慢一次，对比Shenandoah的Brooks转发指针，那是每次对象访问都必须付出的固定开销，简单地说就是每次都慢，因此ZGC对用户程序的运行时负载要比Shenandoah来得更低一些。（若更改的是引用关系，Shenandoah不能也这样实现吗？）
> >
> > 还有另外一个直接的好处是由于染色指针的存在，一旦重分配集中某个Region的存活对象都复制完毕后，这个Region就可以**立即释放**用于新对象的分配（但是转发表还得留着不能释放掉），哪怕堆中还有很多指向这个对象的未更新指针也没有关系，这些旧指针一旦被使用，它们都是可以自愈的。 
>
> **并发重映射（Concurrent Remap）**：重映射所做的就是修正整个堆中指向重分配集中旧对象的所有引用，这一点从目标角度看是与Shenandoah并发引用更新阶段一样的，但是ZGC的并发重映射并不是一个必须要“迫切”去完成的任务，因为前面说过，即使是旧引用，它也是可以自愈的，最多只是第一次使用时多一次转发和修正操作。**重映射清理这些旧引用的主要目的是为了不变慢（还有清理结束后可以释放转发表这样的附带收益）**，所以说这并不是很“迫切”。因此，ZGC很巧妙地**把并发重映射阶段要做的工作，==合并到了下一次垃圾收集循环中的并发标记阶段里去完成==**，反正它们都是要遍历所有对象的，这样合并就节省了一次遍历对象图的开销。一旦所有指针都被修正之后，原来记录新旧对象关系的转发表就可以释放掉了。 

ZGC的优点：

> 1. 没有分代
>
> ZGC没有分代措施，因而完全没有用到写屏障，所以给**用户线程带来的运行负担也要小得多**。但ZGC的这种选择也限制了它能承受的对象分配速率不会太高，不能依据不同分代进行各自频率与速度的收集。**若是过大容易来不及处理浮动垃圾，若是过小则容易浪费CPU性能。**目前唯一的办法就是尽可能地**增加堆容量**大小，获得更多喘息的时间。但是若要从根本上提升ZGC能够应对的对象分配速率，还是需要引**入分代收集**，让新生对象都在一个专门的区域中创建，然后专门针对这 个区域进行更频繁、更快的收集。Azul的C4收集器实现了分代收集后，能够应对的对象分配速率就比分代的PGC收集器提升了十倍之多。 
>
> 2. 支持“NUMA-Aware”的内存分配
>
> ZGC还有一个常在技术资料上被提及的优点是支持“NUMA-Aware”的内存分配。NUMA（Non-Uniform Memory Access，非统一内存访问架构）是一种为多处理器或者多核处理器的计算机所设计的内存架构。由于摩尔定律逐渐失效，现代处理器因频率发展受限转而向多核方向发展，以前原本在北桥芯片中的**内存控制器也被集成到了处理器内核中，这样每个处理器核心所在的裸晶（DIE）都有属于自己内存管理器所管理的内存，**如果要访问被其他处理器核心管理的内存，就必须通过Inter-Connect通道来完成，这要比访问处理器的本地内存慢得多。在NUMA架构下，**ZGC收集器会优先尝试在请求线程当前所处的处理器的本地内存上分配对象，以保证高效内存访问**。在ZGC之前的收集器就只有针对吞吐量设计的Parallel Scavenge支持NUMA内存分配[13]，如今ZGC也成为另外一个选择

ZGC 性能测试：

在ZGC的“弱项”吞吐量方面，以低延迟为首要目标的ZGC**已经达到了以高吞吐量为目标Parallel Scavenge的99%，直接超越了G1**。如果将吞吐量测试设定为面向SLA（Service Level Agreements）应用的“Critical Throughput”的话，ZGC的表现甚至还反超了Parallel Scavenge收集器

而在ZGC的强项停顿时间测试上，**它就毫不留情地与Parallel Scavenge、G1拉开了两个数量级的差距。**不论是平均停顿，还是95%停顿、99%停顿、99.9%停顿，抑或是最大停顿时间，ZGC均能毫不费劲地控制在十毫秒之内。

### 8、选择合适的垃圾收集器

#### Epsilon收集器 

只要Java虚拟机能够工作，垃圾收集器便不可能是真正“无操作”的。原因是“垃圾收集器”这个名字并不能形容它全部的职责，更贴切的名字应该是本书为这一部分 所取的标题——“自动内存管理子系统”。==一个垃圾收集器除了垃圾收集这个本职工作之外，它还要负责堆的管理与布局、对象的分配、与解释器的协作、与编译器的协作、与监控子系统协作等职责，其中至少堆的管理和对象的分配这部分功能是Java虚拟机能够正常运作的必要支持，是一个最小化功能的垃圾收集器也必须实现的内容。==从JDK 10开始，为了隔离垃圾收集器与Java虚拟机解释、编译、监控等子系统的关系，RedHat提出了垃圾收集器的统一接口，即JEP 304提案，Epsilon是这个接口的有效性验证和参考实现，同时也用于需要剥离垃圾收集器影响的性能测试和压力测试。 

Epsilon，这是一款以不能够进行垃圾收集为“卖点”的垃圾收集器，**对短时间、小规模的服务形**式，只要Java虚拟机能正确分配内存，在堆耗尽之前就会退出，那显然运行负载极小、没有任何回收行为的Epsilon便是很恰当的选择。

#### 收集器的权衡 

这个问题的答案主要受以下三个因素影响：

>  ·应用程序的主要关注点是什么？如果是数据分析、科学计算类的任务，目标是能尽快算出结果， 那吞吐量就是主要关注点；如果是SLA应用，那停顿时间直接影响服务质量，严重的甚至会导致事务超时，这样延迟就是主要关注点；而如果是客户端应用或者嵌入式应用，那垃圾收集的内存占用则是不可忽视的。 
>
> ·运行应用的基础设施如何？譬如硬件规格，要涉及的系统架构是x86-32/64、SPARC还是ARM/Aarch64；处理器的数量多少，分配内存的大小；选择的操作系统是Linux、Solaris还是Windows等。
>
> ·使用JDK的发行商是什么？版本号是多少？是ZingJDK/Zulu、OracleJDK、Open-JDK、OpenJ9抑或是其他公司的发行版？该JDK对应了《Java虚拟机规范》的哪个版本？
>
> 一般来说，

收集器的选择就从以上这几点出发来考虑。举个例子，假设某个直接面向用户提供服务的B/S系统准备选择垃圾收集器，一般来说延迟时间是这类应用的主要关注点，那么： 

> ·如果**你有充足的预算但没有太多调优经验**，那么一套带**商业技术支持**的专有硬件或者软件解决方案是不错的选择，Azul公司以前主推的Vega系统和现在主推的Zing VM是这方面的代表，这样你就可以使用传说中的C4收集器了。 
>
> ·如果你虽然没有足够预算去使用商业解决方案，但能够掌控软硬件型号，使用较新的版本，同时又特别注重延迟，那ZGC很值得尝试。（Linux/x86-64与Solaris/SPARC，jkd11以上） 
>
> ·如果你对还处于实验状态的收集器的稳定性有所顾虑，或者应用==必须运行在Win-dows操作系统下==，那ZGC就无缘了，试试Shenandoah吧。 （jdk12以上，OpenJDK）
>
> ·**如果你接手的是遗留系统，软硬件基础设施和JDK版本都比较落后**，那就根据内存规模衡量一下，对于大概4GB到6GB以下的堆内存，CMS一般能处理得比较好，而对于更大的堆内存，可重点考察一下G1。 

### 9、查看GC日志

在JDK 9以前，HotSpot并没 有提供统一的日志处理框架，虚拟机各个功能模块的日志开关分布在不同的参数上，日志级别、循环 日志大小、输出格式、重定向等设置在不同功能上都要单独解决。直到JDK 9，这种混乱不堪的局面才终于消失，**HotSpot所有功能的日志都收归到了“-Xlog”参数**

```bash
-Xlog[:[selector][:[output][:[decorators][:output-options]]]]
```

> 命令行中最关键的参数是**选择器（Selector）**，它由标签（Tag）和日志级别（Level）共同组成。
>
> **以下为tag：**（可以看出HotSpot支持多个模块的日志输出）深入JVM P127
>
> add，age，alloc，annotation，aot，arguments，attach，barrier，biasedlocking，blocks，bot，breakpoint，bytecode，census，class，classhisto，cleanup，compaction，comparator，constraints，constantpool，coops，cpu，cset，data，defaultmethods，dump，ergo，event，exceptions，exit，fingerprint，freelist，==gc==，hashtables，heap，humongous，ihop，iklass，init，itables，jfr，jni，jvmti，liveness，load，loader，logging，mark，marking，metadata，metaspace，method，mmu，modules，monitorinflation，monitormismatch，nmethod，normalize，objecttagging，obsolete，oopmap，os，pagesize，parser，patch，path，phases，plab，preorder，promotion，protectiondomain，purge，redefine，ref，refine，region，remset，resolve，safepoint，scavenge，scrub，setting，stackmap，stacktrace，stackwalk，start，startuptime，state，stats，stringdedup，stringtable，subclass，survivor，sweep，system，task，thread，time，timer，tlab，unload，update，verification，verify，vmoperation，vtables，workgang 
>
> **日志级别**从低到高，共有Trace，Debug，Info，Warning，Error，Off六种级别，日志级别决定了输 出信息的详细程度，默认级别为Info，HotSpot的日志规则与Log4j、SLF4j这类Java日志框架大体上是一致的。
>
> 还可以使用修饰器（Decorator）来要求每行日志输出**都附加上额外的内容**，支持附加在日志行上的信息包括： 
>
> ·time：当前日期和时间。 
>
> ·uptime：虚拟机启动到现在经过的时间，以秒为单位。 
>
> ·timemillis：当前时间的毫秒数，相当于System.currentTimeMillis()的输出。 
>
> ·uptimemillis：虚拟机启动到现在经过的毫秒数。 
>
> ·timenanos：当前时间的纳秒数，相当于System.nanoTime()的输出。 
>
> ·uptimenanos：虚拟机启动到现在经过的纳秒数。 
>
> ·pid：进程ID。 
>
> ·tid：线程ID。 
>
> ·level：日志级别。·tags：日志输出的标签集。 
>
> 如果不指定，默认值是uptime、level、tags这三个
>
> ```bash
> [3.080s][info][gc,cpu] GC(5) User=0.03s Sys=0.00s Real=0.01s（前三个分别为uptime、level、tage）
> ```

####  离线工具查看

比如sun的[gchisto](https://links.jianshu.com/go?to=https%3A%2F%2Fjava.net%2Fprojects%2Fgchisto)，[gcviewer](https://links.jianshu.com/go?to=https%3A%2F%2Fgithub.com%2Fchewiebug%2FGCViewer)离线分析工具，做个笔记先了解下还没用过，可视化好像很好用的样子。

#### 自带的jconsole工具、jstat命令

终端输入jconsole就会出现jdk自带的gui监控工具

![img](https:////upload-images.jianshu.io/upload_images/10006199-d409f452f8364937.png?imageMogr2/auto-orient/strip|imageView2/2/w/1200/format/webp)

jconsole

可以根据内存使用情况间接了解内存使用和gc情况

![img](https:////upload-images.jianshu.io/upload_images/10006199-c0eb6418cf4bade9.png?imageMogr2/auto-orient/strip|imageView2/2/w/1200/format/webp)

jconsole

jstat命令

比如jstat -gcutil pid查看对应java进程gc情况

![img](https:////upload-images.jianshu.io/upload_images/10006199-6ed3ee78469592e8.png?imageMogr2/auto-orient/strip|imageView2/2/w/1200/format/webp)

jstat



```css
s0: 新生代survivor space0简称 就是准备复制的那块 单位为%
s1:指新生代s1已使用百分比，为0的话说明没有存活对象到这边
e:新生代eden(伊甸园)区域(%)
o:老年代(%)
ygc:新生代  次数
ygct:minor gc耗时
fgct:full gc耗时(秒)
GCT: ygct+fgct 耗时
```

### 10、内存与回收策略

#### 什么时候触发GC

简单来说，触发的条件就是GC算法区域满了或将满了。

```css
minor GC(young GC):当年轻代中eden区分配满的时候触发[值得一提的是因为young GC后部分存活的对象会已到老年代(比如对象熬过15轮)，所以过后old gen的占用量通常会变高]

full GC:
①手动调用System.gc()方法 [增加了full GC频率，不建议使用而是让jvm自己管理内存，可以设置-XX:+ DisableExplicitGC来禁止RMI调用System.gc]
②发现perm gen（如果存在永久代的话)需分配空间但已经没有足够空间
③老年代空间不足，比如说新生代的大对象大数组晋升到老年代就可能导致老年代空间不足。
④CMS GC时出现Promotion Faield[pf]
⑤统计得到的Minor GC晋升到旧生代的平均大小大于老年代的剩余空间。
这个比较难理解，这是HotSpot为了避免由于新生代晋升到老年代导致老年代空间不足而触发的FUll GC。
比如程序第一次触发Minor GC后，有5m的对象晋升到老年代，姑且现在平均算5m，那么下次Minor GC发生时，先判断现在老年代剩余空间大小是否超过5m，如果小于5m，则HotSpot则会触发full GC(这点挺智能的)
Promotion Faield:minor GC时 survivor space放不下[满了或对象太大]，对象只能放到老年代，而老年代也放不下会导致这个错误。
Concurrent Model Failure:cms时特有的错误，因为cms时垃圾清理和用户线程可以是并发执行的，如果在清理的过程中
可能原因：
1 cms触发太晚，可以把XX:CMSInitiatingOccupancyFraction调小[比如-XX:CMSInitiatingOccupancyFraction=70 是指设定CMS在对内存占用率达到70%的时候开始GC(因为CMS会有浮动垃圾,所以一般都较早启动GC)]
2 垃圾产生速度大于清理速度，可能是晋升阈值设置过小，Survivor空间小导致跑到老年代，eden区太小，存在大对象、数组对象等情况
3.空间碎片过多，可以开启空间碎片整理并合理设置周期时间
```

#### 对象优先在Eden分配 

大多数情况下，对象在新生代Eden区中分配。当Eden区没有足够空间进行分配时，虚拟机将发起一次Minor GC。 

#### 大对象直接进入老年代 

大对象就是指需要大量连续内存空间的Java对象，最典型的大对象便是那种很长的字符串，或者元素数量很庞大的数组，。HotSpot虚拟机提供了-XX：PretenureSizeThreshold参数，指定大于该设置值的对象直接在老年代分配，这样做的目的是避免在Eden区及两个Survivor区之间来回复制，产生大量的内存复制操作。 

#### 长期存活的对象将进入老年代

对象通常在Eden区里诞生，如果经过第一次 Minor GC后仍然存活，并且能被Survivor容纳的话，该对象会被移动到Survivor空间中，并且将其对象年龄设为1岁。**对象在Survivor区中每熬过一次Minor GC，年龄就增加1岁**，**当它的年龄增加到一定程度（默认为15）**，就会被晋升到老年代中。对象晋升老年代的年龄阈值，可以通过参数-XX：MaxTenuringThreshold设置。

#### 动态对象年龄判定

为了能更好地适应不同程序的内存状况，HotSpot虚拟机并不是永远要求对象的年龄必须达到-XX：MaxTenuringThreshold才能晋升老年代，**如果在Survivor空间中相同年龄所有对象大小的总和大于Survivor空间的一半**，**年龄大于或等于该年龄的对象就可以直接进入老年代**，无须等到-XX：MaxTenuringThreshold中要求的年龄。

#### 空间分配担保

在minor gc前，jvm会先检查老年代最大可用空间是否大于新生代所有对象总空间，如果是的话，则minor gc可以确保是安全的，

> 如果担保失败,会检查一个配置(HandlePromotionFailire),即是否允许担保失败。
>
> 如果允许:继续检查老年代最大可用可用的连续空间是否大于之前晋升的平均大小，比如说剩10m，之前每次都有9m左右的新生代到老年代，那么将尝试一次minor gc(大于的情况)，这会比较冒险。
>
> 如果不允许，而且还小于的情况，则会触发full gc。【为了避免经常full GC 该参数建议打开，jdk6 之后称为为默认，更改选项参数废除】
>
> 这边为什么说是冒险是因为minor gc过后如果出现大对象，由于新生代采用复制算法，survivor无法容纳将跑到老年代，所以才会去计算之前的平均值作为一种担保的条件与老年代剩余空间比较，这就是分配担保。
>
> 这种担保是动态概率的手段，但是也有可能出现之前平均都比较低，突然有一次minor gc对象变得很多远高于以往的平均值，这个时候就会导致担保失败【Handle Promotion Failure】，这就只好再失败后再触发一次FULL GC，

#### 总结：新生代什么样的情况会晋升为老年代

对象优先分配在eden区，eden区满时会触发一次minor GC

> 对象晋升规则
> 1 长期存活的对象进入老年代，对象每熬过一次GC年龄+1(默认年龄阈值15，可配置)。
> 2 对象太大新生代无法容纳则会分配到老年代
> 3 eden区满了，进行minor gc后，eden和一个survivor区仍然存活的对象无法放到(to survivor区)则会通过分配担保机制放到老年代，这种情况一般是minor gc后新生代存活的对象太多。
> 4 动态年龄判定，为了使内存分配更灵活，jvm不一定要求对象年龄达到MaxTenuringThreshold(15)才晋升为老年代，若survior区相同年龄对象总大小大于survior区空间的一半，则大于等于这个年龄的对象将会在minor gc时移到老年代

### 11、各个版本的JVM使用的垃圾收集器是怎么样的

准确来说，垃圾收集器的使用跟当前jvm也有很大的关系，比如说g1是jdk7以后的版本才开始出现。

并不是所有的垃圾收集器都是默认开启的，有些得通过设置相应的开关参数才会使用。比如说cms，需设置(XX:+UseConcMarkSweepGC)

这边有几个实用的命令，比如说server模式下



```shell
#UnlockExperimentalVMOptions UnlockDiagnosticVMOptions解锁获取jvm参数，PrintFlagsFinal用于输出xx相关参数，以Benchmark类测试，这边会有很多结果 大都看不懂- - 在这边查(usexxxxxxgc会看到jvm不同收集器的开关情况)
java -server -XX:+UnlockExperimentalVMOptions -XX:+UnlockDiagnosticVMOptions -XX:+PrintFlagsFinal Benchmark

#后面跟| grep ":"获取已赋值的参数[加:代表被赋值过]
java -server -XX:+UnlockExperimentalVMOptions -XX:+UnlockDiagnosticVMOptions -XX:+PrintFlagsFinal Benchmark| grep ":"

#获得用户自定义的设置或者jvm设置的详细的xx参数和值
java -server -XX:+PrintCommandLineFlags Benchmark
```

![img](https:////upload-images.jianshu.io/upload_images/10006199-a3524986c654e356.png?imageMogr2/auto-orient/strip|imageView2/2/w/1200/format/webp)

本人用的jdk8，这边UseParallelGC为true，参考深入理解jvm那本书说这个是Parallel Scavenge+Serial old搭配组合的开关，但是网上又说8默认是Parallel Scavenge+Parallel Old,我还是信书的吧 - -。

更多相关参数[来源](https://links.jianshu.com/go?to=https%3A%2F%2Fupload-images.jianshu.io%2Fupload_images%2F4914401-4503c1ac0196db78.png)

![img](https:////upload-images.jianshu.io/upload_images/10006199-324780351133d59a.png?imageMogr2/auto-orient/strip|imageView2/2/w/1200/format/webp)

常用参数

> 据说更高版本的jvm默认使用g1



 
