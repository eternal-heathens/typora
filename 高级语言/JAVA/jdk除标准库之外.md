# 常用类库

以下所有的包都是jdk提供的类包，都是在rt.jar中。rt.jar是JAVA基础类库（java核心框架中很重要的包），
 包含lang在内的大部分功能，而且rt.jar默认就在根classloader的加载路径里面

1. java.*
    JavaSE的标准库，是java标准的一部分，是对外承诺的java开发接口，通常要保持向后兼容，一般不会轻易修改。包括其他厂家(IBMJDK/HPJDK/OpenJDK)在内，所有jdk的实现，在java.*上都是一样的。
2. javax.*
    也是java标准的一部分，但是没有包含在标准库中，一般属于标准库的扩展。通常属于某个特定领域，不是一般性的api。

此上两者都属于java标准库，公有的API，遵循java平台规范，

1. com.sun.*
    是sun的hotspot虚拟机中java.* 和javax.*的扩展实现类。因为包含在rt中，所以我们也可以调用。但是因为不是sun对外公开承诺的接口，所以根据根据实现的需要随时增减，因此在不同版本的hotspot中可能是不同的，而且在其他的jdk实现中是没有的，调用这些类，可能不会向后兼容，所以一般不推荐使用。

2. org.*
    是由企业或者组织提供的java类库，大部分不是sun公司提供的，同com.sun.*，不具备向后兼容性，会根据需要随时增减。其中比较常用的是w3c提供的对XML、网页、服务器的类和接口。

3. sun.*包：
   1、不是API公开接口的一部分，调用sun包的程序并不能确保工作在所有Java平台上，不同的操作系统中的实现可能不相同。

   2、不同的jdk版本sun包中的类也可能不定期的变化，因此sun.*包中的类没有提供API文档及源码。

# sun.misc

## Unsafe

Unsafe是位于sun.misc包下的一个类，主要提供一些用于执行低级别、不安全操作的方法，如直接访问系统内存资源、自主管理内存资源等，这些方法在提升Java运行效率、增强Java语言底层资源操作能力方面起到了很大的作用。但由于Unsafe类使Java语言拥有了类似C语言指针一样操作内存空间的能力，这无疑也增加了程序发生相关指针问题的风险。在程序中过度、不正确使用Unsafe类会使得程序出错的概率变大，使得Java这种安全的语言变得不再“安全”，因此对Unsafe的使用一定要慎重。

其所有方法基本仅提供一个native接口，具体实现均为用JNV在JVM中的C实现。

### 基本介绍

如下Unsafe源码所示，Unsafe类为一单例实现，提供静态方法getUnsafe获取Unsafe实例，当且仅当调用getUnsafe方法的类为引导类加载器所加载时才合法，否则抛出SecurityException异常。

```java
public final class Unsafe {
  // 单例对象
  private static final Unsafe theUnsafe;

  private Unsafe() {
  }
  @CallerSensitive
  public static Unsafe getUnsafe() {
    Class var0 = Reflection.getCallerClass();
    // 仅在引导类加载器`BootstrapClassLoader`加载时才合法
    if(!VM.isSystemDomainLoader(var0.getClassLoader())) {    
      throw new SecurityException("Unsafe");
    } else {
      return theUnsafe;
    }
  }
}
```

那如若想使用这个类，该如何获取其实例？有如下两个可行方案。

其一，从`getUnsafe`方法的使用限制条件出发，通过Java命令行命令`-Xbootclasspath/a`把调用Unsafe相关方法的类A所在jar包路径追加到默认的bootstrap路径中，使得A被引导类加载器加载，从而通过`Unsafe.getUnsafe`方法安全的获取Unsafe实例。

```bash
java -Xbootclasspath/a: ${path}   // 其中path为调用Unsafe相关方法的类所在jar包路径 
```

其二，通过反射获取单例对象theUnsafe。

```java
private static Unsafe reflectGetUnsafe() {
    try {
      Field field = Unsafe.class.getDeclaredField("theUnsafe");
      field.setAccessible(true);
      return (Unsafe) field.get(null);
    } catch (Exception e) {
      log.error(e.getMessage(), e);
      return null;
    }
}
```

### 功能介绍

![image-20201216171528050](F:\Typora数据储存\高级语言\JAVA\jdk除标准库之外.assets\image-20201216171528050.png)

### 内存操作

这部分主要包含堆外内存的分配、拷贝、释放、给定地址值操作等方法。

```java
//分配内存, 相当于C++的malloc函数
public native long allocateMemory(long bytes);
//扩充内存
public native long reallocateMemory(long address, long bytes);
//释放内存
public native void freeMemory(long address);
//在给定的内存块中设置值
public native void setMemory(Object o, long offset, long bytes, byte value);
//内存拷贝
public native void copyMemory(Object srcBase, long srcOffset, Object destBase, long destOffset, long bytes);
//获取给定地址值，忽略修饰限定符的访问限制。与此类似操作还有: getInt，getDouble，getLong，getChar等
public native Object getObject(Object o, long offset);
//为给定地址设置值，忽略修饰限定符的访问限制，与此类似操作还有: putInt,putDouble，putLong，putChar等
public native void putObject(Object o, long offset, Object x);
//获取给定地址的byte类型的值（当且仅当该内存地址为allocateMemory分配时，此方法结果为确定的）
public native byte getByte(long address);
//为给定地址设置byte类型的值（当且仅当该内存地址为allocateMemory分配时，此方法结果才是确定的）
public native void putByte(long address, byte x);
```

通常，我们在Java中创建的对象都处于堆内内存（heap）中，堆内内存是由JVM所管控的Java进程内存，并且它们遵循JVM的内存管理机制，JVM会采用垃圾回收机制统一管理堆内存。与之相对的是堆外内存，存在于JVM管控之外的内存区域，Java中对堆外内存的操作，依赖于Unsafe提供的操作堆外内存的native方法。需要注意的是堆外内存仍属于进程的管理。

#### 使用堆外内存的原因

- 对垃圾回收停顿的改善。由于堆外内存是直接受操作系统管理而不是JVM，所以当我们使用堆外内存时，即可保持较小的堆内内存规模。从而在GC时减少回收停顿对于应用的影响。
- 提升程序I/O操作的性能。通常在I/O通信过程中，会存在堆内内存到堆外内存的数据拷贝操作，对于需要频繁进行内存间数据拷贝且生命周期较短的暂存数据，都建议存储到堆外内存。

#### 典型应用DirectByteBuffer

https://www.jianshu.com/p/007052ee3773

DirectByteBuffer是Java用于实现堆外内存的一个重要类，通常用在通信过程中做缓冲池，如在Netty、MINA等NIO框架中应用广泛。DirectByteBuffer对于堆外内存的创建、使用、销毁等逻辑均由Unsafe提供的堆外内存API来实现。

下图为DirectByteBuffer构造函数，创建DirectByteBuffer的时候，通过Unsafe.allocateMemory分配内存、Unsafe.setMemory进行内存初始化，而后构建Cleaner对象用于跟踪DirectByteBuffer对象的垃圾回收，以实现当DirectByteBuffer被垃圾回收时，分配的堆外内存一起被释放。

![image-20201216172242941](F:\Typora数据储存\高级语言\JAVA\jdk除标准库之外.assets\image-20201216172242941.png)

> address只会被直接缓存给使用到。之所以将address属性升级放在Buffer中，是为了在JNI调用GetDirectBufferAddress时提升它调用的速率。

> Q： 那为什么不直接在堆内分配内存，由操作系统直接访问Java堆内的内存区域了？
>
> A： 因为操作系统所做的操作并不通过JVM，此时若是操作系统进行I/O等读取时，JVM进行GC，此时便会出现错误，因此我们通过操作系统通过JNI的操作需要保证不会出现该问题，如：JNI调用的内存不能进行GC操作。
>
> Q： JNI调用的内存是不能进行GC操作的，那如何解决保证JNI调用的数据的安全性呢？
>
> A： 1. 堆内内存与堆外内存之间数据拷贝的方式，若是需要操作系统通过JNI读写的数据，则读写到堆外内存中，而程序需要打的数据则由堆外内存复制到对内内存
>
> ​	2. 直接堆外内存，程序通过JNI直接在堆外内存进行读写操作，但由native方法来管理堆外内存的创建与销毁，保证操作系统回收的安全性

##### 堆外内存的创建

```java
    DirectByteBuffer(int cap) {                   // package-private
        super(-1, 0, cap, cap);
        boolean pa = VM.isDirectMemoryPageAligned();
        int ps = Bits.pageSize();
        long size = Math.max(1L, (long)cap + (pa ? ps : 0));
        // 判断系统是否有足够的内存，方法判断是否有足够内存不是判断物理机是否有足够内存，而是判断 JVM 启动时，指定的堆外内存空间大小是否有剩余的空间。这个大小由参数 -XX:MaxDirectMemorySize=<size> 设置
        Bits.reserveMemory(size, cap);

        long base = 0;
        try {
            // 通过unsafe.allocateMemory分配堆外内存，并返回堆外内存的基地址
            base = unsafe.allocateMemory(size);
        } catch (OutOfMemoryError x) {
            Bits.unreserveMemory(size, cap);
            throw x;
        }
        unsafe.setMemory(base, size, (byte) 0);
        if (pa && (base % ps != 0)) {
            // Round up to page boundary
            address = base + ps - (base & (ps - 1));
        } else {
            address = base;
        }
        // 构建Cleaner对象用于跟踪DirectByteBuffer对象的垃圾回收，以实现当DirectByteBuffer被垃圾回收时，堆外内存也会被释放
        cleaner = Cleaner.create(this, new Deallocator(base, size, cap));
        att = null;
    }
```

> Bits#reserveMemory()方法中会使用System.gc显式触发full gc，之所以这样做是因为很多DirectByteBuffer对象是适合生命周期较长的对象的，会进入老年代，因此单单YGC是不够的。若是禁用了System.gc – JVM参数DisableExplicitGC，禁用了显式GC，则可能会有明明Heap还有空间却有OutOfMemory的错误
>
> 总的来说，Bits.reserveMemory(size, cap)方法在可用堆外内存不足以分配给当前要创建的堆外内存大小时，会实现以下的步骤来尝试完成本次堆外内存的创建：
>  ① 触发一次非堵塞的Reference#tryHandlePending(false)。该方法会将已经被JVM垃圾回收的DirectBuffer对象的堆外内存释放。
>  ② 如果进行一次堆外内存资源回收后，还不够进行本次堆外内存分配的话，则进行 System.gc()。System.gc()会触发一个full gc，但你需要知道，调用System.gc()并不能够保证full gc马上就能被执行。所以在后面打代码中，会进行最多9次尝试，看是否有足够的可用堆外内存来分配堆外内存。并且每次尝试之前，都对延迟等待时间，已给JVM足够的时间去完成full gc操作。
>  注意，如果你设置了-XX:+DisableExplicitGC，将会禁用显示GC，这会使System.gc()调用无效。
>  ③ 如果9次尝试后依旧没有足够的可用堆外内存来分配本次堆外内存，则抛出OutOfMemoryError("Direct buffer memory”)异常。

##### 堆外内存回收

Cleaner是PhantomReference的子类，并通过自身的next和prev字段维护的一个双向链表。PhantomReference的作用在于跟踪垃圾回收过程，并不会对对象的垃圾回收过程造成任何的影响。
 所以cleaner = Cleaner.create(this, new Deallocator(base, size, cap)); 用于对当前构造的DirectByteBuffer对象的垃圾回收过程进行跟踪。
 PhantomReference继承自Reference，当某个被Cleaner引用的对象将被回收时，JVM垃圾收集器会将此Reference对象放入到对象引用中的pending链表中，等待Reference-Handler进行相关处理。其中，Reference-Handler为一个拥有最高优先级的守护线程，会循环不断的处理pending链表中的对象引用，执行Cleaner的clean方法进行相关清理工作。

Reference-Handler是Reference的内部类， 一般添加Reference对象都会其ensureClassInitialized()方法，来启动轮询。

> Reference对象有两个由JVM进行赋值的属性（需要注意的是，不同的reference由discovered 进行关联，基于链表实现，没有专门用队列等存储方式）
>
> - discovered 表示要处理的对象的下一个对象。即可以理解要处理的对象也是一个链表，通过discovered进行排队，这边只需要不停地拿到pending，然后再通过discovered 不断地拿到下一个对象赋值给pending即可，直到取到了最有一个。它是被JVM 使用的。
> - pending 是等待被入队的引用列表。JVM 收集器会添加引用到这个列表，直到Reference-handler线程移除了它们。这个列表使用 discovered 字段来连接它下一个元素（即 pending 的下一个元素就是discovered对象。r = pending; pending = r.discovered）。

![image-20201216210250609](F:\Typora数据储存\高级语言\JAVA\jdk除标准库之外.assets\image-20201216210250609.png)

##### 什么情况下使用堆外内存

- 堆外内存适用于生命周期中等或较长的对象。( 如果是生命周期较短的对象，在YGC的时候就被回收了，就不存在大内存且生命周期较长的对象在FGC对应用造成的性能影响 )。
- 直接的文件拷贝操作，或者I/O操作。程序和操作系统通过堆外内存进行交互，因为I/O操作是系统内核和设备间的通信，而不是通过程序直接和外设通信的。
- 同时，还可以使用 池+堆外内存 的组合方式，来对生命周期较短，但涉及到I/O操作的对象进行堆外内存的再使用。( Netty中就使用了该方式 )

##### 堆外内存 VS 堆内内存

- 堆内内存：主要用于两类对象：①生命周期较短，且结构简单的对象，在内存池中重复利用这些对象能增加CPU缓存的命中率，从而提高性能；②加载含有大量重复对象的大片数据，此时使用内存池能减少垃圾回收的时间。
- 堆外内存：它和内存池一样，也能缩短垃圾回收时间，但是它适用的对象和内存池完全相反。内存池往往适用于生命期较短的可变对象，而生命期中等或较长的对象，正是堆外内存要解决的。

* **堆内存分配回收快，可被JVM自动管理，缺点是多一次复制，需要从内核缓冲区复制到堆缓冲区。**

  **直接内存缓冲区需要自己处理回收相关的操作，但是减少了一次复制。**



### CAS相关

如下源代码释义所示，这部分主要为CAS相关操作的方法。

```java
/**
	*  CAS
  * @param o         包含要修改field的对象
  * @param offset    对象中某field的偏移量
  * @param expected  期望值
  * @param update    更新值
  * @return          true | false
  */
public final native boolean compareAndSwapObject(Object o, long offset,  Object expected, Object update);

public final native boolean compareAndSwapInt(Object o, long offset, int expected,int update);
  
public final native boolean compareAndSwapLong(Object o, long offset, long expected, long update);
```

什么是CAS? 即比较并替换，实现并发算法时常用到的一种技术。CAS操作包含三个操作数——内存位置、预期原值及新值。执行CAS操作的时候，将内存位置的值与预期原值比较，如果相匹配，那么处理器会自动将该位置值更新为新值，否则，处理器不做任何操作。我们都知道，CAS是一条CPU的原子指令（cmpxchg指令），不会造成所谓的数据不一致问题，Unsafe提供的CAS方法（如compareAndSwapXXX）底层实现即为CPU指令cmpxchg。

#### 典型应用

CAS在java.util.concurrent.atomic相关类、Java AQS、CurrentHashMap等实现上有非常广泛的应用。如下图所示，AtomicInteger的实现中，静态字段valueOffset即为字段value的内存偏移地址，valueOffset的值在AtomicInteger初始化时，在静态代码块中通过Unsafe的objectFieldOffset方法获取。在AtomicInteger中提供的线程安全方法中，通过字段valueOffset的值可以定位到AtomicInteger对象中value的内存地址，从而可以根据CAS实现对value字段的原子操作。



### 线程调度

这部分，包括线程挂起、恢复、锁机制等方法。

```java
//取消阻塞线程
public native void unpark(Object thread);
//阻塞线程
public native void park(boolean isAbsolute, long time);
//获得对象锁（可重入锁）
@Deprecated
public native void monitorEnter(Object o);
//释放对象锁
@Deprecated
public native void monitorExit(Object o);
//尝试获取对象锁
@Deprecated
public native boolean tryMonitorEnter(Object o);
```

如上源码说明中，方法park、unpark即可实现线程的挂起与恢复，将一个线程进行挂起是通过park方法实现的，调用park方法后，线程将一直阻塞直到超时或者中断等条件出现；unpark可以终止一个挂起的线程，使其恢复正常。

#### 典型应用

Java锁和同步器框架的核心类AbstractQueuedSynchronizer，就是通过调用`LockSupport.park()`和`LockSupport.unpark()`实现线程的阻塞和唤醒的，而LockSupport的park、unpark方法实际是调用Unsafe的park、unpark方式来实现。

### Class相关

此部分主要提供Class和它的静态字段的操作相关方法，包含静态字段内存定位、定义类、定义匿名类、检验&确保初始化等。

```java
//获取给定静态字段的内存地址偏移量，这个值对于给定的字段是唯一且固定不变的
public native long staticFieldOffset(Field f);
//获取一个静态类中给定字段的对象指针
public native Object staticFieldBase(Field f);
//判断是否需要初始化一个类，通常在获取一个类的静态属性的时候（因为一个类如果没初始化，它的静态属性也不会初始化）使用。 当且仅当ensureClassInitialized方法不生效时返回false。
public native boolean shouldBeInitialized(Class<?> c);
//检测给定的类是否已经初始化。通常在获取一个类的静态属性的时候（因为一个类如果没初始化，它的静态属性也不会初始化）使用。
public native void ensureClassInitialized(Class<?> c);
//定义一个类，此方法会跳过JVM的所有安全检查，默认情况下，ClassLoader（类加载器）和ProtectionDomain（保护域）实例来源于调用者
public native Class<?> defineClass(String name, byte[] b, int off, int len, ClassLoader loader, ProtectionDomain protectionDomain);
//定义一个匿名类
public native Class<?> defineAnonymousClass(Class<?> hostClass, byte[] data, Object[] cpPatches);
```

#### 典型应用

从Java 8开始，JDK使用invokedynamic及VM Anonymous Class结合来实现Java语言层面上的Lambda表达式。

- **invokedynamic**： invokedynamic是Java 7为了实现在JVM上运行动态语言而引入的一条新的虚拟机指令，它可以实现在运行期动态解析出调用点限定符所引用的方法，然后再执行该方法，invokedynamic指令的分派逻辑是由用户设定的引导方法决定。
- **VM Anonymous Class**：可以看做是一种模板机制，针对于程序动态生成很多结构相同、仅若干常量不同的类时，可以先创建包含常量占位符的模板类，而后通过Unsafe.defineAnonymousClass方法定义具体类时填充模板的占位符生成具体的匿名类。生成的匿名类不显式挂在任何ClassLoader下面，只要当该类没有存在的实例对象、且没有强引用来引用该类的Class对象时，该类就会被GC回收。故而VM Anonymous Class相比于Java语言层面的匿名内部类无需通过ClassClassLoader进行类加载且更易回收。

在Lambda表达式实现中，通过invokedynamic指令调用引导方法生成调用点，在此过程中，会通过ASM动态生成字节码，而后利用Unsafe的defineAnonymousClass方法定义实现相应的函数式接口的匿名类，然后再实例化此匿名类，并返回与此匿名类中函数式方法的方法句柄关联的调用点；而后可以通过此调用点实现调用相应Lambda表达式定义逻辑的功能。下面以如下图所示的Test类来举例说明。

![image-20201216220640631](F:\Typora数据储存\高级语言\JAVA\jdk除标准库之外.assets\image-20201216220640631.png)

Test类编译后的class文件反编译后的结果如下图一所示（删除了对本文说明无意义的部分），我们可以从中看到main方法的指令实现、invokedynamic指令调用的引导方法BootstrapMethods、及静态方法`lambda$main$0`（实现了Lambda表达式中字符串打印逻辑）等。在引导方法执行过程中，会通过Unsafe.defineAnonymousClass生成如下图二所示的实现Consumer接口的匿名类。其中，accept方法通过调用Test类中的静态方法`lambda$main$0`来实现Lambda表达式中定义的逻辑。而后执行语句`consumer.accept（"lambda"）`其实就是调用下图二所示的匿名类的accept方法。

![image-20201216220701086](F:\Typora数据储存\高级语言\JAVA\jdk除标准库之外.assets\image-20201216220701086.png)

> 总结来说， 1. 编译时，lambda表达式会解语法糖，生成一个在本类中生成一个lambda表达式实现的静态方法,如lambda$main$0， 因为是方法调用，还会生成一条invokedynamic指令
>
> ​					2. invokedynamic指令调用引导方法生成调用点，在此过程中，会通过ASM动态生成字节码，与通过Unsafe.defineAnonymousClass生成匿名内部类，匿名内部类为静态类型的接口的实现，其方法实现为动态生成对前面lambda表达式实现的静态方法的传参调用
>
> ​					3. 然后再实例化此匿名类，并返回与此匿名类中函数式方法的方法句柄关联的调用点 

### 对象操作

此部分主要包含对象成员属性相关操作及非常规的对象实例化方式等相关方法。

```java
//返回对象成员属性在内存地址相对于此对象的内存地址的偏移量
public native long objectFieldOffset(Field f);
//获得给定对象的指定地址偏移量的值，与此类似操作还有：getInt，getDouble，getLong，getChar等
public native Object getObject(Object o, long offset);
//给定对象的指定地址偏移量设值，与此类似操作还有：putInt，putDouble，putLong，putChar等
public native void putObject(Object o, long offset, Object x);
//从对象的指定偏移量处获取变量的引用，使用volatile的加载语义
public native Object getObjectVolatile(Object o, long offset);
//存储变量的引用到对象的指定的偏移量处，使用volatile的存储语义
public native void putObjectVolatile(Object o, long offset, Object x);
//有序、延迟版本的putObjectVolatile方法，不保证值的改变被其他线程立即看到。只有在field被volatile修饰符修饰时有效
public native void putOrderedObject(Object o, long offset, Object x);
//绕过构造方法、初始化代码来创建对象
public native Object allocateInstance(Class<?> cls) throws InstantiationException;
```

#### 典型应用

- **常规对象实例化方式**：我们通常所用到的创建对象的方式，从本质上来讲，都是通过new机制来实现对象的创建。但是，new机制有个特点就是当类只提供有参的构造函数且无显示声明无参构造函数时，则必须使用有参构造函数进行对象构造，而使用有参构造函数时，必须传递相应个数的参数才能完成对象实例化。
- **非常规的实例化方式**：而Unsafe中提供allocateInstance方法，仅通过Class对象就可以创建此类的实例对象，而且不需要调用其构造函数、初始化代码、JVM安全检查等。它抑制修饰符检测，也就是即使构造器是private修饰的也能通过此方法实例化，只需提类对象即可创建相应的对象。由于这种特性，allocateInstance在java.lang.invoke、Objenesis（提供绕过类构造器的对象生成方式）、Gson（反序列化时用到）中都有相应的应用。

如下图所示，在Gson反序列化时，如果类有默认构造函数，则通过反射调用默认构造函数创建实例，否则通过UnsafeAllocator来实现对象实例的构造，UnsafeAllocator通过调用Unsafe的allocateInstance实现对象的实例化，保证在目标类无默认构造函数时，反序列化不够影响。

### 数组相关

这部分主要介绍与数据操作相关的arrayBaseOffset与arrayIndexScale这两个方法，两者配合起来使用，即可定位数组中每个元素在内存中的位置。

```java
//返回数组中第一个元素的偏移地址
public native int arrayBaseOffset(Class<?> arrayClass);
//返回数组中一个元素占用的大小
public native int arrayIndexScale(Class<?> arrayClass);
```

#### 典型应用

这两个与数据操作相关的方法，在java.util.concurrent.atomic 包下的AtomicIntegerArray（可以实现对Integer数组中每个元素的原子性操作）中有典型的应用，如下图AtomicIntegerArray源码所示，通过Unsafe的arrayBaseOffset、arrayIndexScale分别获取数组首元素的偏移地址base及单个元素大小因子scale。后续相关原子性操作，均依赖于这两个值进行数组中元素的定位，getAndAdd方法即通过checkedByteOffset方法获取某数组元素的偏移地址，而后通过CAS实现原子性操作。

### 内存屏障

在Java 8中引入，用于定义内存屏障（也称内存栅栏，内存栅障，屏障指令等，是一类同步屏障指令，是CPU或编译器在对内存随机访问的操作中的一个同步点，使得此点之前的所有读写操作都执行后才可以开始执行此点之后的操作），避免代码重排序。

```java
//内存屏障，禁止load操作重排序。屏障前的load操作不能被重排序到屏障后，屏障后的load操作不能被重排序到屏障前
public native void loadFence();
//内存屏障，禁止store操作重排序。屏障前的store操作不能被重排序到屏障后，屏障后的store操作不能被重排序到屏障前
public native void storeFence();
//内存屏障，禁止load、store操作重排序
public native void fullFence();
```

#### 典型应用

在Java 8中引入了一种锁的新机制——StampedLock，它可以看成是读写锁的一个改进版本。StampedLock提供了一种乐观读锁的实现，这种乐观读锁类似于无锁的操作，完全不会阻塞写线程获取写锁，从而缓解读多写少时写线程“饥饿”现象。由于StampedLock提供的乐观读锁不阻塞写线程获取读锁，当线程共享变量从主内存load到线程工作内存时，会存在数据不一致问题，所以当使用StampedLock的乐观读锁时，需要遵从如下图用例中使用的模式来确保数据的一致性。

![image-20201216223824208](F:\Typora数据储存\高级语言\JAVA\jdk除标准库之外.assets\image-20201216223824208.png)

> tampedLock的validate方法校验锁状态，其中，校验锁状态这步操作至关重要，需要判断锁状态是否发生改变，从而判断之前copy到线程工作内存中的值是否与主内存的值存在不一致。
>
> StampedLock.validate方法，通过锁标记与相关常量进行位运算、比较来校验锁状态，在校验逻辑之前，会通过Unsafe的loadFence方法加入一个load内存屏障，目的是避免上图用例中步骤②和StampedLock.validate中锁状态校验运算发生重排序导致锁状态校验不准确的问题。

### 系统相关

这部分包含两个获取系统相关信息的方法。

```java
//返回系统指针的大小。返回值为4（32位系统）或 8（64位系统）。
public native int addressSize();  
//内存页的大小，此值为2的幂次方。
public native int pageSize();
```

#### 典型应用

java.nio下的工具类Bits中计算待申请内存所需内存页数量的静态方法，其依赖于Unsafe中**pageSize**方法获取系统内存页大小实现后续计算逻辑。