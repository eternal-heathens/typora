# 并行与并发

如果某个系统支持两个或者多个动作（Action）**同时存在**，那么这个系统就是一个**并发系统**。

如果某个系统支持两个或者多个动作**同时执行**，那么这个系统就是一个**并行系统**。

并发系统与并行系统这两个定义之间的关键差异在于**“存在”**这个词。

在并发程序中可以同时拥有两个或者多个线程。这意味着，如果程序在单核处理器上运行，那么这两个线程将交替地换入或者换出内存。这些线程是同时“存在”的——每个线程都处于执行过程中的某个状态。如果程序能够并行执行，那么就一定是运行在多核处理器上。此时，程序中的每个线程都将分配到一个独立的处理器核上，因此可以同时运行。

你吃饭吃到一半，电话来了，你一直到吃完了以后才去接，这就说明你不支持并发也不支持并行。
你吃饭吃到一半，电话来了，你停了下来接了电话，接完后继续吃饭，这说明你支持并发。
你吃饭吃到一半，电话来了，你一边打电话一边吃饭，这说明你支持并行。

并发的关键是你有处理多个任务的能力，不一定要同时。
并行的关键是你有同时处理多个任务的能力。

所以我认为它们最关键的点就是：是否是『同时』。

# JMM 

* JMM是依据cup、缓冲池和内存的物理结构设计出的抽象模型(**JMM和物理硬件的关系，前者便如同你定义了一个类，后者便是他会存放的地方**，JVM的5个区中设计的操作分别在这些类中执行，其**分配**需要不同硬件、操作系统、JVM的协力)，但大致上会将**寄存器和缓冲区作为工作区，将主内存作为主内存**，以此作为JVM内存结构的一个规范，**其中的程序计数器、堆、栈等可能存在于以上每个区域**。
* 有JMM的中依据内存模型的同步规则对线程需要的变量的约束,才能使得线程安全得以实现

![img](https://img-blog.csdnimg.cn/20190114091526545.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dhbmduYW53bHc=,size_16,color_FFFFFF,t_70)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20190114091502645.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dhbmduYW53bHc=,size_16,color_FFFFFF,t_70)



#### Java内存模型-同步规则

* 主内存与工作内存间的交互操作，对于32位的基本类型，有以下原子操作

> lock（锁定）：作用于主内存的变量，它把一个变量标识为一条线程独占的状态。 
>
> unlock（解锁）：作用于主内存的变量，它把一个处于锁定状态的变量释放出来，释放后的变量 
>
> 才可以被其他线程锁定。
>
> read：可以简单的看为主存到cache
>
> load：可以简单的看为cache到寄存器
>
> use：将值传递给执行引擎，再交由CPU进行运算（每当虚拟机遇到一个需要使用变量的值的操作，就会执行这个操作）
>
> assign：将值从执行引擎接收并付给工作内存的变量
>
> store：寄存器到cache
>
> write：cache到主存
>
> ·不允许read和load、store和write操作之一单独出现，即不允许一个变量从主内存读取了但工作内 存不接受，或者工作内存发起回写了但主内存不接受的情况出现。 
>
> ·不允许一个线程丢弃它最近的assign操作，即变量在工作内存中改变了之后必须把该变化同步回 主内存。
>
> ·不允许一个线程无原因地（没有发生过任何assign操作）把数据从线程的工作内存同步回主内存 中。·一个新的变量只能在主内存中“诞生”，不允许在工作内存中直接使用一个未被初始化（load或assign）的变量，换句话说就是对一个变量实施use、store操作之前，必须先执行assign和load操作。 
>
> ·一个变量在同一个时刻只允许一条线程对其进行lock操作，但lock操作可以被同一条线程重复执行多次，多次执行lock后，只有执行相同次数的unlock操作，变量才会被解锁。 
>
> ·如果对一个变量执行lock操作，那将会清空工作内存中此变量的值，在执行引擎使用这个变量前，需要重新执行load或assign操作以初始化变量的值。 
>
> ·如果一个变量事先没有被lock操作锁定，那就不允许对它执行unlock操作，也不允许去unlock一个被其他线程锁定的变量。 
>
> ·对一个变量执行unlock操作之前，必须先把此变量同步回主内存中（执行store、write操作）。

![image-20200802161309873](F:\Typora数据储存\高级语言\JAVA\JAVA并发.assets\image-20200802161309873.png)

* 应用JMM设计出的JVM才能基于物理硬件和操作系统对其中的数据交流进行管理
* 依据内存模型实现了JVM，才有内存屏障的出现。

#### volatile

* （可见性和有序性，**本身不保证原子性**，但引用到的内存间的操作是原子性的）

> 1. 可见性
>
> * 1. use前必须先从主内存中load：每次使用前都必须从主内存刷新获取最新的值（只能保证store时会通知其他的工作内存清空缓存，因此write到主存后才能保证其他线程read时得到的值准确，但如++等会有非原子性的操作，在read和store间还有其他的操作，若是没有如sychronized等来保持更大范围操作的原子性，则有可能会发发生并发问题）
> * 2. assign后必须store到主内存的基于MESI等缓存一致性临时区：每次修改后都必须立刻同步到主存区
>
> 2. 禁止重排序
>
> * 在一个线程中，两个volatile变量A、B，A先于B被use/assign，无论两者load或store顺序，A都要先于B read或write
> * 内存屏障
>
> 同一个处理器中，指令的重排序会正确处理指令依赖情况保障程序能得出正确的执行结果，再加上lock addl$0x0，(%esp)在程序的末尾，因此，该处理器中的**“指令重排序无法越过内存屏障”**。
>
> 因为上一因素，其实只有一个处理器时也无需内存屏障，但==如果有两个或以上访问同一块内存，且一个在观测另一个，就需要内存屏障保证一致性了。==双重校验锁的问题基于此，但是需要另一个线程在该线程加载时会设置一个等待时间或者jvm能判断是否有别的线程会生成需要的类对象，否则可能会产生死锁现象。
>
> 都是针对同一个处理器的线程里自己的指令重排序问题，不同处理器只是监听可能用同一内存需要到，但是**不同线程的指令不会混合后在不同处理器执行**，一个线程一般对应自己的一个工作区，自己的指令也存在那个工作区中。
>
> **内存屏障也仅针对自己处理器中的指令，仅对violate修饰的变量的操作后加内存屏障**
>
> **所谓内存屏障：其会在编译后的文件中多执行一个lock addl$0x0，(%esp)（相当于一个内存屏障“Memory Barrier”），他的作用是：将本处理器的缓存写入内存中，并会触发MESI等缓存一致性协议，通过对总线的侦听机制，清空其他工作区该变量的缓存。**
>
> ==若是只单靠lock和重排序不能排序的有关联的指令，便能完成禁止重排序则可以有这样的思路：若是单个变量的相关指令没有lock则各自执行完各自store并write到主存中，则别的工作内存能read到，而voletile则使通过lock他们逐个store到总线通知其他线程清空缓存，最后将lock addl$0x0前指令的操作结果一起write到主内存中。==

#### double和long

Java内存模型要求lock、unlock、read、load、assign、use、store、write这八种操作都具有原子性， 但是对于64位的数据类型（long和double），在模型中特别定义了一条宽松的规定：允许虚拟机将没有 被volatile修饰的64位数据的读写操作划分为**两次32位的操作来进行**如果有多个线程共享一个并未声明为volatile的long或double类型的变量，并且同时对它们进行读取 和修改操作，那么某些线程可能会读取到一个既不是原值，也不是其他线程修改值的代表了“半个变 量”的数值。

从JDK 9起， HotSpot增加了一个实验性的参数-XX：+AlwaysAtomicAccesses（这是JEP 188对Java内存模型更新的一部分内容来约束虚拟机对所有数据类型进行原子性的访问。而针对double类型，由于现代中央处理器中一般都包含专门用于处理浮点数据的浮点运算器（Floating Point Unit，FPU），用来专门处理 单、双精度的浮点数据，所以哪怕是32位虚拟机中通常也不会出现非原子性访问的问题，实际测试也证实了这一点。

#### 原子性/可见性/有序性

* 原子性

由Java内存模型来直接保证的原子性变量操作包括read、load、assign、use、store和write这六个

我们大致可以认为，基本数据类型的访问、读写都是具备原子性的（例外就是long和double的非原子性 协定，读者只要知道这件事情就可以了，无须太过在意这些几乎不会发生的例外情况）。 

如果应用场景需要一个**更大范围的原子性保证**（经常会遇到），Java内存模型还提供了lock和 unlock操作来满足这种需求，尽管虚拟机未把**lock和unlock**操作直接开放给用户使用，但是却提供了更 高层次的字节码指令**monitorenter和monitorexit**来隐式地使用这两个操作。这两个字节码指令反映到Java 代码中就是同步块——**synchronized关键字**，因此在synchronized块之间的操作也具备原子性。 



* 可见性

可见性就是指当一个线程修改了共享变量的值时，其他线程能够立即得知这个修改。（即用前load和改后store）

除了volatile之外，Java还有两个关键字能实现可见性，它们是synchronized和final。

同步块的可见性：

是由“对一个变量执行unlock操作之前，必须先把此变量同步回主内存中（执行store、write操作）”这条规则获得的。

final关键字的可见性是指：

被final修饰的字段在构造器中一旦被初始化完  成，并且构造器没有把“this”的引用传递出去（this引用逃逸是一件很危险的事情，其他线程有可能通过这个引用访问到“初始化了一半”的对象），那么在其他线程中就能看见final字段的值。



* 有序性

Java程序中天然的有序性可以 

总结为一句话：如果在本线程内观察，所有的操作都是有序的；如果在一个线程中观察另一个线程， 所有的操作都是无序的。前半句是指“线程内似表现为串行的语义”（Within-Thread As-If-Serial Semantics），后半句是指“指令重排序”现象和“工作内存与主内存同步延迟”现象。 

Java语言提供了volatile和synchronized两个关键字来保证线程之间操作的有序性，volatile关键字本 身就包含了禁止指令重排序的语义，而synchronized则是由“一个变量在同一个时刻只允许一条线程对 其进行lock操作”这条规则获得的，这个规则决定了持有同一个锁的两个同步块只能串行地进入。 



# happens-before原则有哪些：

程序顺序规则：单线程内，按照程序代码顺序，书写在前面的操作先行发生于书写在后面的操作；

锁定规则：一个unlock操作先行发生于对同一个锁的lock操作；

volatile变量规则：对一个Volatile变量的写操作先行发生于对这个变量的读操作；

线程启动规则：Thread对象的start()方法先行发生于此线程的其他动作；

线程中断规则：对线程interrupt()方法的调用先行发生于被中断线程的代码检测到中断事件的发生；

线程终止规则：线程中所有的操作都先行发生于线程的终止检测，我们可以通过Thread.join()方法结束、Thread.isAlive()的返回值手段检测到线程已经终止执行；

对象终结规则：一个对象的初始化完成先行发生于它的finalize()方法的开始；

传递规则：如果操作A先行发生于操作B，而操作B又先行发生于操作C，则可以得出操作A先行发生于操作C；

那如果一个操作**“先行发生”**，是否就能推导出这个操作必定是**“时间上的先发生**”呢？很遗憾，这 个推论也是**不成立**的。一个典型的例子就是多次提到的“**指令重排序**”



**时间先后顺序与先行发生原则之间基本没有因果关系， 所以我们衡量并发安全问题的时候不要受时间顺序的干扰，一切必须以先行发生原则为准。**

 

# Thread和Runnable

首先，多线程的实现方式两种：一种是继承Thread类，另一种是实现Runnable接口。


那么这两种方法的区别何在？该如何选择？



第一：他们之间的关系
查看J2EE的API看到

Thread类中：  public class Thread extends Object implements Runnable

Runnable接口：public interfaceRunnable

明显可知两者：Thread类是Runnable接口的一个实现类，那么Runnable接口是何用？


文档解释：

Runnable 接口应该由那些打算通过某一线程执行其实例的类来实现。类必须定义一个称为 run 的无参数方法。

设计该接口的目的是为希望在活动时执行代码的对象提供一个公共协议。例如，Thread 类实现了 Runnable。激活的意思是说某个线程已启动并且尚未停止。

也就是说Runnable提供的是一种线程运行规范，具体运行线程需要通过它的实现类



第二：通过源码分析
我们以public class Thread1 extends Thread 这个自定义线程来追本溯源：首先查看Thread类



其中定义了一个private Runnable target;  定义了Runnable类型的属性target，查看哪里有引用

几个方法：

```java
 private void init(ThreadGroup g, Runnable target, String name,
                      long stackSize) {。。。}

public Thread() {
    init(null, null, "Thread-" + nextThreadNum(), 0);
   }

public Thread(Runnable target) {
	init(null, target, "Thread-" + nextThreadNum(), 0);
    }

public Thread(ThreadGroup group, Runnable target) {
	init(group, target, "Thread-" + nextThreadNum(), 0);
    }

public Thread(String name) {
	init(null, null, name, 0);
    }

 public Thread(ThreadGroup group, String name) {
	init(group, null, name, 0);
    }

public Thread(Runnable target, String name) {
	init(null, target, name, 0);
    }

public Thread(ThreadGroup group, Runnable target, String name) {
	init(group, target, name, 0);
    }

 public Thread(ThreadGroup group, Runnable target, String name,
                  long stackSize) {
	init(group, target, name, stackSize);
    }
```

以上列出了Thread的一个初始化方法init()和所有的构造方法：可以知道，构造方法需要调用init()方法初始化线程对象，有一个Runnable类型的target对象也参与初始化。
我们所知道的Thread类进行运行线程时是调用start()方法，我们也来查看这个方法：

```java
public synchronized void start() {
        /**
	 * This method is not invoked for the main method thread or "system"
	 * group threads created/set up by the VM. Any new functionality added 
	 * to this method in the future may have to also be added to the VM.
	 *
	 * A zero status value corresponds to state "NEW".
         */
        if (threadStatus != 0)
            throw new IllegalThreadStateException();
        group.add(this);
        start0();
        if (stopBeforeStart) {
	    stop0(throwableFromStop);
	}
    }
```

可知：当调用这个start()方法时，使该线程开始执行；Java 虚拟机调用该线程的 run 方法。结果是两个线程并发地运行；当前线程（从调用返回给 start 方法）和另一个线程（执行其 run 方法）。 这个方法仅作了线程状态的判断（保证一个线程不多次启动，多次启动在JVM看来这是非法的），然后把该线程添加到线程组（不多做解释）等待运行。
那么继续看run()方法：**thread的run只是调用了runable的对像，再调用runable对象的方法**

```java
public void run() {
	if (target != null) {
	    target.run();
	}
    }
```

可知，当Runnable实现类对象没有内容为null，则方法什么都不执行，如果有实现类对象，就调用它实现类对象实现的run()方法。这让我们想到了一个经典的设计模式：代理模式
到此我们知道：线程的运行是依靠Thread类中的start()方法执行，并且由虚拟机调用run()方法，所以我们必须实现run()方法


那么还是要看一下Runnable接口的设计：

```java
public
interface Runnable {
    /**
     * When an object implementing interface <code>Runnable</code> is used 
     * to create a thread, starting the thread causes the object's 
     * <code>run</code> method to be called in that separately executing 
     * thread. 
     * <p>
     * The general contract of the method <code>run</code> is that it may 
     * take any action whatsoever.
     *
     * @see     java.lang.Thread#run()
     */
    public abstract void run();
}
```

可知它只有一个抽象的run()方法，完全是实实在在的线程运行规范
第三：通过他们之间的设计模式：代理模式  再次深入
代理模式如图：



可知，Thread也是Runnable接口的子类，但其没有完全实现run()方法，所以说如果继承Thread类实现多线程，仍旧需要覆写run()方法。


看两种实现多线程的基本方式


继承Thread：

```java
class MyThread extends Thread{
	private int ticket = 5;
	public void run(){
		for(int i = 0;i<100;i++){
			if(ticket>0){//判断是否还有剩余票
				System.out.println("卖票,ticket = "+ticket--);
			}
		}
	}
};
public class ThreadDemo04{
	public static void main(String args[]){
		MyThread mt1 = new MyThread();
		MyThread mt2 = new MyThread();
		MyThread mt3 = new MyThread();
		mt1.start();//调用线程主体让其运行
		mt2.start();//三个地方同时卖票
		mt3.start();
	}
};	

实现Runnable：

class MyThread implements Runnable{
	private int ticket=5;
	public void run(){
		for(int i = 0;i<100;i++){
			if(ticket>0){
				System.out.println("卖票,ticket = "+ticket--);
			}
		}
	}
};
public class RunnableDemo02{
	public static void main(String args[]){
		MyThread my1 = new MyThread();
		new Thread(my1).start(); //启动三个线程
		new Thread(my1).start(); //共享my1中资源
		new Thread(my1).start(); 
	}
};
```

可知最后：无论哪种方法都需要实现run()方法，run方法是线程的运行主体。并且，线程的运行都是调用Thread的start()方法。
那么代理模式中Thread类就充当了代理类，它在线程运行主体运行前作了一些操作然后才运行线程的run()。首先说一下代理模式的基本特征就是对【代理目标进行增强】代理模式就不在这里详述。总之，Thread提供了很多有关线程运行前、后的操作，然后通过它的start()方法让JVM自动调用目标的run()方法



第四：继承Thread与实现Runnable接口方法区别
首先看一段代码：

```java
new Thread(
    new Runnable(){

              public void run() {
            while(true){
                try {
                        Thread.sleep(500);
                } catch (InterruptedException e) {e.printStackTrace(); }
                    System.out.println("runnable :" + Thread.currentThread().getName());
            }                            
        }
    }

){

        public void run() {
        while(true){
            try {
                Thread.sleep(500);
            } catch (InterruptedException e) {e.printStackTrace();}
     
                       System.out.println("thread :" + Thread.currentThread().getName());
        }    
    }

}.start();
```

可以预测一下这段代码是执行哪一个run()方法？
根据以前java中基础知识可知：执行start()方法后，JVM去找run()方法，然后它找到了自己的run()方法，那么就直接运行自己的run()方法。如果找不到自己的方法它才会去找被代理的run()方法。所以它应该执行的是"thread：。。。"代码部分。可以把它放到一个main方法中，通过测试验证推断正确：



想说明的是一个面向对象的思想：即如果没有上方第二个run()块，那么它执行的就是匿名Runnable实现类的run()方法。这说明什么，说明，Thread相当于一个执行者，而执行的代码块在Runnable实现类中定义好。这样实现执行与源码的分离，体现了面向对象的思想。这也是他们之间的一个比较大的区别。

其他区别：

实现Runnable接口可以实现资源共享，Thread无法完成资源共享 ----- 讨论第三点的两段代码中：继承Thread的：结果卖出了15张，各有各的票数。实现Runnable接口的方法：卖出5张，共享了资源

实现Runnable接口比继承Thread类来实现多线程有如下明显优点：

适合多个相同程序代码使用共同资源；

避免由单继承局限带来的影响；


增强程序的健壮性，代码能够被多个线程共享，代码数据是独立的；

使用的选择
通过比对区别可知：
由于面向对象的思想，以及资源共享，代码健壮性等，一般都是使用实现Runnable接口来实现多线程，也比较推荐

# ThreadLocal

在需要保存线程本身的信息时可以直接创建ThreadLocal并（重写initialValue设置初始值或者直接在set(value)）来导入需要保存的信息，再之后线程需要调用本身的信息只需要调用get（）方法即可，get会调用Thread的静态native方法currentThread()获取当前线程并且调取Thread实例中的ThreadLocal.ThreadLocalMap对象（ThreadLocal为key，因此可以有多个ThreadLocal为索引存储不同类型的副本），若有该对象并且Entry不为Null，则直接读取Entry数组，若为Null则会调用setInitialValue()依据map是否为空调用map.set插入value还是创建map（此时会带上Thread作为形参为其中的map创建实例）

ThreadLocal与Thread不是一对一的关系的，Thread与Thre（adLocalMap是一对一的，ThreadLocal与ThreadLocalmap并没有直接关系，而是通过Thread 进行调用，只是一般需要在当前线程中创建ThreadLocal示例）

 IheritableThreadLocal允许一个线程以及该线程创建的所有子线程都可以访问他保存的值（通过parentMap的的引用）

建议使用static修饰，免得线程多次生成同一类型的副本的时候重复生成ThreadLocal  

# Java并发编程：线程池的使用

* 文章的主题是基于下面的旧版的一些线程池的讲解，再加上1.8的情况与一些补充，下面为旧版原文链接

　　http://www.cnblogs.com/dolphin0520/p/3932921.html　

在前面的文章中，我们使用线程的时候就去创建一个线程，这样实现起来非常简便，但是就会有一个问题：

　　如果并发的线程数量很多，并且每个线程都是执行一个时间很短的任务就结束了，这样频繁创建线程就会大大降低系统的效率，因为频繁创建线程和销毁线程需要时间。

　　那么有没有一种办法使得线程可以复用，就是执行完一个任务，并不被销毁，而是可以继续执行其他的任务？

　　在Javmaa中可以通过线程池来达到这样的效果。今天我们就来详细讲解一下Java的线程池，首先我们从最核心的ThreadPoolExecutor类中的方法讲起，然后再讲述它的实现原理，接着给出了它的使用示例，最后讨论了一下如何合理配置线程池的大小。

　　以下是本文的目录大纲：

　　一.Java中的ThreadPoolExecutor类

　　二.深入剖析线程池实现原理

　　三.使用示例

　　四.如何合理配置线程池的大小　

　　若有不正之处请多多谅解，并欢迎批评指正。

## 一.Java中的ThreadPoolExecutor类

　　java.uitl.concurrent.ThreadPoolExecutor类是线程池中最核心的一个类，因此如果要透彻地了解Java中的线程池，必须先了解这个类。下面我们来看一下ThreadPoolExecutor类的具体实现源码。

　　在ThreadPoolExecutor类中提供了四个构造方法：

```java
public class ThreadPoolExecutor extends AbstractExecutorService {

​```
    .....
    
    public ThreadPoolExecutor(int corePoolSize,int maximumPoolSize,long keepAliveTime,TimeUnit unit,
    
            BlockingQueue<Runnable> workQueue);

 


    public ThreadPoolExecutor(int corePoolSize,int maximumPoolSize,long keepAliveTime,TimeUnit unit,
    
            BlockingQueue<Runnable> workQueue,ThreadFactory threadFactory);

 


    public ThreadPoolExecutor(int corePoolSize,int maximumPoolSize,long keepAliveTime,TimeUnit unit,
    
            BlockingQueue<Runnable> workQueue,RejectedExecutionHandler handler);

 


    public ThreadPoolExecutor(int corePoolSize,int maximumPoolSize,long keepAliveTime,TimeUnit unit,
    
        BlockingQueue<Runnable> workQueue,ThreadFactory threadFactory,RejectedExecutionHandler handler);
    
    ...

}
​```


```

 　　从上面的代码可以得知，ThreadPoolExecutor继承了AbstractExecutorService类，并提供了四个构造器，事实上，通过观察每个构造器的源码具体实现，发现前面三个构造器都是调用的第四个构造器进行的初始化工作。

 　　下面解释下一下构造器中各个参数的含义：

* corePoolSize：核心池的大小，这个参数跟后面讲述的线程池的实现原理有非常大的关系。在创建了线程池后，默认情况下，线程池中并没有任何线程，而是等待有任务到来才创建线程去执行任务，除非调用了prestartAllCoreThreads()或者prestartCoreThread()方法，从这2个方法的名字就可以看出，是预创建线程的意思，即在没有任务到来之前就创建corePoolSize个线程或者一个线程。默认情况下，在创建了线程池后，线程池中的线程数为0，当有任务来之后，就会创建一个线程去执行任务，当线程池中的线程数目达到corePoolSize后，就会把到达的任务放到缓存队列当中；
* maximumPoolSize：线程池最大线程数，这个参数也是一个非常重要的参数，它表示在线程池中最多能创建多少个线程；
* keepAliveTime：表示线程没有任务执行时最多保持多久时间会终止。默认情况下，只有当线程池中的线程数大于corePoolSize时，keepAliveTime才会起作用，直到线程池中的线程数不大于corePoolSize，即当线程池中的线程数大于corePoolSize时，如果一个线程空闲的时间达到keepAliveTime，则会终止，直到线程池中的线程数不超过corePoolSize。但是如果调用了allowCoreThreadTimeOut(boolean)方法，在线程池中的线程数不大于corePoolSize时，keepAliveTime参数也会起作用，直到线程池中的线程数为0；
* unit：参数keepAliveTime的时间单位，有7种取值，在TimeUnit类中有7种静态属性：

```
TimeUnit.DAYS;               //天
TimeUnit.HOURS;             //小时
TimeUnit.MINUTES;           //分钟
TimeUnit.SECONDS;           //秒
TimeUnit.MILLISECONDS;      //毫秒
TimeUnit.MICROSECONDS;      //微妙
TimeUnit.NANOSECONDS;       //纳秒
```

* workQueue：一个阻塞队列，用来存储等待执行的任务，这个参数的选择也很重要，会对线程池的运行过程产生重大影响，一般来说，这里的阻塞队列有以下几种选择：
```
ArrayBlockingQueue;
LinkedBlockingQueue;
SynchronousQueue;
```
　　ArrayBlockingQueue和PriorityBlockingQueue使用较少，一般使用LinkedBlockingQueue和Synchronous。线程池的排队策略与BlockingQueue有关。

* threadFactory：线程工厂，主要用来创建线程；
* handler：表示当拒绝处理任务时的策略，有以下四种取值：

```
ThreadPoolExecutor.AbortPolicy:丢弃任务并抛出RejectedExecutionException异常。 
ThreadPoolExecutor.DiscardPolicy：也是丢弃任务，但是不抛出异常。 
ThreadPoolExecutor.DiscardOldestPolicy：丢弃队列最前面的任务，然后重新尝试执行任务（重复此过程）
ThreadPoolExecutor.CallerRunsPolicy：由调用线程处理该任务

```

　　从上面给出的ThreadPoolExecutor类的代码可以知道，ThreadPoolExecutor继承了AbstractExecutorService，我们来看一下AbstractExecutorService的实现：

```java
public abstract class AbstractExecutorService implements ExecutorService {

    protected <T> RunnableFuture<T> newTaskFor(Runnable runnable, T value) { };
    
    protected <T> RunnableFuture<T> newTaskFor(Callable<T> callable) { };
    
    public Future<?> submit(Runnable task) {};
    
    public <T> Future<T> submit(Runnable task, T result) { };
    
    public <T> Future<T> submit(Callable<T> task) { };
    
    private <T> T doInvokeAny(Collection<? extends Callable<T>> tasks,boolean timed, long nanos)
    
        throws InterruptedException, ExecutionException, TimeoutException {
    
    };
    
    public <T> T invokeAny(Collection<? extends Callable<T>> tasks)
    throws InterruptedException, ExecutionException {
    
    };
    
    public <T> T invokeAny(Collection<? extends Callable<T>> tasks,
    
                           long timeout, TimeUnit unit)
    
        throws InterruptedException, ExecutionException, TimeoutException {
    
    };
    
    public <T> List<Future<T>> invokeAll(Collection<? extends Callable<T>> tasks)
    
        throws InterruptedException {
    
    };
    
    public <T> List<Future<T>> invokeAll(Collection<? extends Callable<T>> tasks,
    
                                         long timeout, TimeUnit unit)
    
        throws InterruptedException {
    
    };

}
```
 　　AbstractExecutorService是一个抽象类，它实现了ExecutorService接口。

　　我们接着看ExecutorService接口的实现：
```java
public interface ExecutorService extends Executor {

    void shutdown();
    
    boolean isShutdown();
    
    boolean isTerminated();
    
    boolean awaitTermination(long timeout, TimeUnit unit)
    
        throws InterruptedException;
    
    <T> Future<T> submit(Callable<T> task);
    
    <T> Future<T> submit(Runnable task, T result);
    
    Future<?> submit(Runnable task);
    
    <T> List<Future<T>> invokeAll(Collection<? extends Callable<T>> tasks)
    
        throws InterruptedException;
    
    <T> List<Future<T>> invokeAll(Collection<? extends Callable<T>> tasks,
    
                                  long timeout, TimeUnit unit)
    
        throws InterruptedException;

    <T> T invokeAny(Collection<? extends Callable<T>> tasks)
    
        throws InterruptedException, ExecutionException;
    
    <T> T invokeAny(Collection<? extends Callable<T>> tasks,
    
                    long timeout, TimeUnit unit)
    
        throws InterruptedException, ExecutionException, TimeoutException;
}
```
 　　而ExecutorService又是继承了Executor接口，我们看一下Executor接口的实现：

```java
public interface Executor {

    void execute(Runnable command);

}
```
 　　到这里，大家应该明白了ThreadPoolExecutor、AbstractExecutorService、ExecutorService和Executor几个之间的关系了。

　　Executor是一个顶层接口，在它里面只声明了一个方法execute(Runnable)，返回值为void，参数为Runnable类型，从字面意思可以理解，就是用来执行传进去的任务的；

　　然后ExecutorService接口继承了Executor接口，并声明了一些方法：submit、invokeAll、invokeAny以及shutDown等；

　　抽象类AbstractExecutorService实现了ExecutorService接口，基本实现了ExecutorService中声明的所有方法；

　　然后ThreadPoolExecutor继承了类AbstractExecutorService。

　　在ThreadPoolExecutor类中有几个非常重要的方法：

 ```java
execute()
submit()
shutdown()
shutdownNow()
 ```

 　　execute()方法实际上是Executor中声明的方法，在ThreadPoolExecutor进行了具体的实现，这个方法是ThreadPoolExecutor的核心方法，通过这个方法可以向线程池提交一个任务，交由线程池去执行。

　　submit()方法是在ExecutorService中声明的方法，在AbstractExecutorService就已经有了具体的实现，在ThreadPoolExecutor中并没有对其进行重写，这个方法也是用来向线程池提交任务的，但是它和execute()方法不同，它能够返回任务执行的结果，去看submit()方法的实现，会发现它实际上还是调用的execute()方法，只不过它利用了Future来获取任务执行结果（Future相关内容将在下一篇讲述）。

　　shutdown()和shutdownNow()是用来关闭线程池的。

　　还有很多其他的方法：

　　比如：getQueue() 、getPoolSize() 、getActiveCount()、getCompletedTaskCount()等获取与线程池相关属性的方法，有兴趣的朋友可以自行查阅API。

## 二.深入剖析线程池实现原理

　　在上一节我们从宏观上介绍了ThreadPoolExecutor，下面我们来深入解析一下线程池的具体实现原理，将从下面几个方面讲解：

　　1.线程池状态

　　2.任务的执行

　　3.线程池中的线程初始化

　　4.任务缓存队列及排队策略

　　5.任务拒绝策略

　　6.线程池的关闭

　　7.线程池容量的动态调整

### 1.线程池状态

　　在ThreadPoolExecutor中定义了一个volatile变量，另外定义了几个static final变量表示线程池的各个状态

```java
volatile int runState;

static final int RUNNING    = 0;

static final int SHUTDOWN   = 1;

static final int STOP       = 2;

static final int TERMINATED = 3;
```

* runState表示当前线程池的状态，它是一个volatile变量用来保证线程之间的可见性；

　　下面的几个static final变量表示runState可能的几个取值。

　　当创建线程池后，初始时，线程池处于RUNNING状态；

* 如果调用了shutdown()方法，则线程池处于SHUTDOWN状态，此时线程池不能够接受新的任务，它会等待所有任务执行完毕；

* 如果调用了shutdownNow()方法，则线程池处于STOP状态，此时线程池不能接受新的任务，并且会去尝试终止正在执行的任务；

* 当线程池处于SHUTDOWN或STOP状态，并且所有工作线程已经销毁，任务缓存队列已经清空或执行结束后，线程池被设置为TERMINATED状态。

### 2.任务的执行

　　在了解将任务提交给线程池到任务执行完毕整个过程之前，我们先来看一下ThreadPoolExecutor类中其他的一些比较重要成员变量：

```java
private final BlockingQueue<Runnable> workQueue;              //任务缓存队列，用来存放等待执行的任务
private final ReentrantLock mainLock = new ReentrantLock();   //线程池的主要状态锁，对线程池状态（比如线程池大小 ）（runState等）的改变都要使用这个锁
private final HashSet<Worker> workers = new HashSet<Worker>();  //用来存放工作集
private volatile long  keepAliveTime;    //线程存货时间   
private volatile boolean allowCoreThreadTimeOut;   //是否允许为核心线程设置存活时间
private volatile int   corePoolSize;     //核心池的大小（即线程池中的线程数目大于这个参数时，提交的任务会被放进任务缓存队列）
private volatile int   maximumPoolSize;   //线程池最大能容忍的线程数
private volatile int   poolSize;       //线程池中当前的线程数
private volatile RejectedExecutionHandler handler; //任务拒绝策略
private volatile ThreadFactory threadFactory;   //线程工厂，用来创建线程
private int largestPoolSize;   //用来记录线程池中曾经出现过的最大线程数
private long completedTaskCount;   //用来记录已经执行完毕的任务个数
```

 　　每个变量的作用都已经标明出来了，这里要重点解释一下corePoolSize、maximumPoolSize、largestPoolSize三个变量。

* corePoolSize在很多地方被翻译成核心池大小，其实我的理解这个就是线程池的大小。

* 这个例子中的corePoolSize就是10，而maximumPoolSize就是14（10+4）。

* 也就是说corePoolSize就是线程池大小，maximumPoolSize在我看来是线程池的一种补救措施，即任务量突然过大时的一种补救措施。

　　不过为了方便理解，在本文后面还是将corePoolSize翻译成核心池大小。

* largestPoolSize只是一个用来起记录作用的变量，用来记录线程池中曾经有过的最大线程数目，跟线程池的容量没有任何关系。

　　下面我们进入正题，看一下任务从提交到最终执行完毕经历了哪些过程。

　　在ThreadPoolExecutor类中，最核心的任务提交方法是execute()方法，虽然通过submit也可以提交任务，但是实际上submit方法里面最终调用的还是execute()方法，所以我们只需要研究execute()方法的实现原理即可：

```java
public void execute(Runnable command) {//新版，本质上差不多，将addIfUnderCorePoolSize(command)直接加到了addworker里面
        if (command == null)
            throw new NullPointerException();
        int c = ctl.get();
        if (workerCountOf(c) < corePoolSize) {
            if (addWorker(command, true))
                return;
            c = ctl.get();
        }
        if (isRunning(c) && workQueue.offer(command)) {
            int recheck = ctl.get();
            if (! isRunning(recheck) && remove(command))
                reject(command);
            else if (workerCountOf(recheck) == 0)
                addWorker(null, false);
        }
        else if (!addWorker(command, false))
            reject(command);
    }
```

 　　上面的代码可能看起来不是那么容易理解，下面我们一句一句解释：

　　首先，判断提交的任务command是否为null，若是null，则抛出空指针异常；

　　接着是这句，这句要好好理解一下：


if (poolSize >= corePoolSize || !addIfUnderCorePoolSize(command))

 　　由于是或条件运算符，所以先计算前半部分的值，如果线程池中当前线程数不小于核心池大小，那么就会直接进入下面的if语句块了。

　　如果线程池中当前线程数小于核心池大小，则接着执行后半部分，也就是执行


addIfUnderCorePoolSize(command)

　　如果执行完addIfUnderCorePoolSize这个方法返回false，则继续执行下面的if语句块，否则整个方法就直接执行完毕了。

　　如果执行完addIfUnderCorePoolSize这个方法返回false，然后接着判断：


if (runState == RUNNING && workQueue.offer(command))

 　　如果当前线程池处于RUNNING状态，则将任务放入任务缓存队列；如果当前线程池不处于RUNNING状态或者任务放入缓存队列失败，则执行：


addIfUnderMaximumPoolSize(command)

　　如果执行addIfUnderMaximumPoolSize方法失败，则执行reject()方法进行任务拒绝处理。

　　回到前面：if (runState == RUNNING && workQueue.offer(command))

 　　这句的执行，如果说当前线程池处于RUNNING状态且将任务放入任务缓存队列成功，则继续进行判断：


if (runState != RUNNING || poolSize == 0)

 　　这句判断是为了防止在将此任务添加进任务缓存队列的同时其他线程突然调用shutdown或者shutdownNow方法关闭了线程池的一种应急措施。如果是这样就执行：ensureQueuedTaskHandled(command)

 　　进行应急处理，从名字可以看出是保证 添加到任务缓存队列中的任务得到处理。

　　我们接着看2个关键方法的实现：addIfUnderCorePoolSize和addIfUnderMaximumPoolSize：**重点**

```java
private boolean addWorker(Runnable firstTask, boolean core) {//新版，将 addIfUnderCorePoolSize与addThread结合了起来。
        retry:
        for (;;) {
            int c = ctl.get();
            int rs = runStateOf(c);

            // Check if queue empty only if necessary.
            if (rs >= SHUTDOWN &&
                ! (rs == SHUTDOWN &&
                   firstTask == null &&
                   ! workQueue.isEmpty()))
                return false;

            for (;;) {
                int wc = workerCountOf(c);
                if (wc >= CAPACITY ||
                    wc >= (core ? corePoolSize : maximumPoolSize))
                    return false;
                if (compareAndIncrementWorkerCount(c))
                    break retry;
                c = ctl.get();  // Re-read ctl
                if (runStateOf(c) != rs)
                    continue retry;
                // else CAS failed due to workerCount change; retry inner loop
            }
        }

        boolean workerStarted = false;
        boolean workerAdded = false;
        Worker w = null;
        try {
            w = new Worker(firstTask);
            final Thread t = w.thread;
            if (t != null) {
                final ReentrantLock mainLock = this.mainLock;
                mainLock.lock();
                try {
                    // Recheck while holding lock.
                    // Back out on ThreadFactory failure or if
                    // shut down before lock acquired.
                    int rs = runStateOf(ctl.get());

                    if (rs < SHUTDOWN ||
                        (rs == SHUTDOWN && firstTask == null)) {
                        if (t.isAlive()) // precheck that t is startable
                            throw new IllegalThreadStateException();
                        workers.add(w);
                        int s = workers.size();
                        if (s > largestPoolSize)
                            largestPoolSize = s;
                        workerAdded = true;
                    }
                } finally {
                    mainLock.unlock();
                }
                if (workerAdded) {
                    t.start();//线程是在这里启动，但是可能会有疑惑下面新版worker代码中的runWorker有什么用
                    workerStarted = true;
                }
            }
        } finally {
            if (! workerStarted)
                addWorkerFailed(w);
        }
        return workerStarted;
    }
```

 　　这个是addIfUnderCorePoolSize方法的具体实现，从名字可以看出它的意图就是当低于核心吃大小时执行的方法。

下面看其具体实现，首先获取到锁，因为这地方涉及到线程池状态的变化，先通过if语句判断当前线程池中的线程数目是否小于核心池大小，

有朋友也许会有疑问：前面在execute()方法中不是已经判断过了吗，只有线程池当前线程数目小于核心池大小才会执行addIfUnderCorePoolSize方法的，为何这地方还要继续判断？原因很简单，前面的判断过程中并没有加锁，因此可能在execute方法判断的时候poolSize小于corePoolSize，而判断完之后，在其他线程中又向线程池提交了任务，就可能导致poolSize不小于corePoolSize了，所以需要在这个地方继续判断。然后接着判断线程池的状态是否为RUNNING，原因也很简单，因为有可能在其他线程中调用了shutdown或者shutdownNow方法。然后就是执行

```
t = addThread(firstTask);
```

 　　这个方法也非常关键，传进去的参数为提交的任务，返回值为Thread类型。然后接着在下面判断t是否为空，为空则表明创建线程失败（即poolSize>=corePoolSize或者runState不等于RUNNING），否则调用t.start()方法启动线程。

　　我们来看一下addThread方法的实现：

```java
private Thread addThread(Runnable firstTask) {

    Worker w = new Worker(firstTask);
    
    Thread t = threadFactory.newThread(w);  //创建一个线程，执行任务   
    
    if (t != null) {
    
        w.thread = t;            //将创建的线程的引用赋值为w的成员变量       
    
        workers.add(w);
    
        int nt = ++poolSize;     //当前线程数加1       
    
        if (nt > largestPoolSize)
    
            largestPoolSize = nt;
    
    }
    
    return t;

}
```

 　　在addThread方法中，首先用提交的任务创建了一个Worker对象，然后调用线程工厂threadFactory创建了一个新的线程t，然后将线程t的引用赋值给了Worker对象的成员变量thread，接着通过workers.add(w)将Worker对象添加到工作集当中。

　　下面我们看一下Worker类的实现：**重点**

```java
private final class Worker
        extends AbstractQueuedSynchronizer
        implements Runnable//新版，其中与旧版一样，线程是再addworker（addThread）后启动的，那么worker中的run方法以及方法中的runWorker又是如何实现的呢？  关键就在于构造方法中的this.thread = getThreadFactory().newThread(this)这个语句，其中过的this为当前的worker对象本身，而上面可以看出worker是实现了Runable接口的，因此addworker中的线程t是基于Worker对象本身启动的，而thread的run方法就是对runable对象的run的调用，而worker重写了run方法，因此就能start0（）调用JVM后调用run时调用runworker方法，继而调用gettask等。
    {
        /**
         * This class will never be serialized, but we provide a
         * serialVersionUID to suppress a javac warning.
         */
        private static final long serialVersionUID = 6138294804551838833L;

        /** Thread this worker is running in.  Null if factory fails. */
        final Thread thread;
        /** Initial task to run.  Possibly null. */
        Runnable firstTask;
        /** Per-thread task counter */
        volatile long completedTasks;

        /**
         * Creates with given first task and thread from ThreadFactory.
         * @param firstTask the first task (null if none)
         */
        Worker(Runnable firstTask) {
            setState(-1); // inhibit interrupts until runWorker
            this.firstTask = firstTask;
            this.thread = getThreadFactory().newThread(this);
        }

        /** Delegates main run loop to outer runWorker  */
        public void run() {
            runWorker(this);
        }

        // Lock methods
        //
        // The value 0 represents the unlocked state.
        // The value 1 represents the locked state.

        protected boolean isHeldExclusively() {
            return getState() != 0;
        }

        protected boolean tryAcquire(int unused) {
            if (compareAndSetState(0, 1)) {
                setExclusiveOwnerThread(Thread.currentThread());
                return true;
            }
            return false;
        }

        protected boolean tryRelease(int unused) {
            setExclusiveOwnerThread(null);
            setState(0);
            return true;
        }

        public void lock()        { acquire(1); }
        public boolean tryLock()  { return tryAcquire(1); }
        public void unlock()      { release(1); }
        public boolean isLocked() { return isHeldExclusively(); }

        void interruptIfStarted() {
            Thread t;
            if (getState() >= 0 && (t = thread) != null && !t.isInterrupted()) {
                try {
                    t.interrupt();
                } catch (SecurityException ignore) {
                }
            }
        }
    }

```

 　　它实际上实现了Runnable接口，因此上面的Thread t = threadFactory.newThread(w);效果跟下面这句的效果基本一样：

>  Thread t = new Thread(w);

 　　相当于传进去了一个Runnable任务，在线程t中执行这个Runnable。

　　既然Worker实现了Runnable接口，那么自然最核心的方法便是run()方法了：

```java
public void run() {

    try {
    
        Runnable task = firstTask;
    
        firstTask = null;
    
        while (task != null || (task = getTask()) != null) {
    
            runTask(task);
    
            task = null;
    
        }
    
    } finally {
    
        workerDone(this);
    
    }

}

    final void runWorker(Worker w) {//新版 worker自己作为runnable对象（以便thread启动时基于worker启动来实现自己需要的逻辑）时接收了runnable对象，最后还是用接收的runnable对象run。
        Thread wt = Thread.currentThread();
        Runnable task = w.firstTask;
        w.firstTask = null;
        w.unlock(); // allow interrupts
        boolean completedAbruptly = true;
        try {
            while (task != null || (task = getTask()) != null) {
                w.lock();
                // If pool is stopping, ensure thread is interrupted;
                // if not, ensure thread is not interrupted.  This
                // requires a recheck in second case to deal with
                // shutdownNow race while clearing interrupt
                if ((runStateAtLeast(ctl.get(), STOP) ||
                     (Thread.interrupted() &&
                      runStateAtLeast(ctl.get(), STOP))) &&
                    !wt.isInterrupted())
                    wt.interrupt();
                try {
                    beforeExecute(wt, task);
                    Throwable thrown = null;
                    try {
                        task.run();
                    } catch (RuntimeException x) {
                        thrown = x; throw x;
                    } catch (Error x) {
                        thrown = x; throw x;
                    } catch (Throwable x) {
                        thrown = x; throw new Error(x);
                    } finally {
                        afterExecute(task, thrown);
                    }
                } finally {
                    task = null;
                    w.completedTasks++;
                    w.unlock();
                }
            }
            completedAbruptly = false;
        } finally {
            processWorkerExit(w, completedAbruptly);
        }
    }

```

 　　从run方法的实现可以看出，它首先执行的是通过构造器传进来的任务firstTask，在调用runTask()执行完firstTask之后，在while循环里面不断通过getTask()去取新的任务来执行，那么去哪里取呢？自然是从任务缓存队列里面去取，getTask是ThreadPoolExecutor类中的方法，并不是Worker类中的方法，下面是getTask方法的实现：

```java
Runnable getTask() {

    for (;;) {
    
        try {
    
            int state = runState;
    
            if (state > SHUTDOWN)
    
                return null;
    
            Runnable r;
    
            if (state == SHUTDOWN)  // Help drain queue
    
                r = workQueue.poll();
    
            else if (poolSize > corePoolSize || allowCoreThreadTimeOut) //如果线程数大于核心池大小或者允许为核心池线程设置空闲时间，
    
                //则通过poll取任务，若等待一定的时间取不到任务，则返回null
    
                r = workQueue.poll(keepAliveTime, TimeUnit.NANOSECONDS);
    
            else
    
                r = workQueue.take();
    
            if (r != null)
    
                return r;
    
            if (workerCanExit()) {    //如果没取到任务，即r为null，则判断当前的worker是否可以退出
    
                if (runState >= SHUTDOWN) // Wake up others
    
                    interruptIdleWorkers();   //中断处于空闲状态的worker
    
                return null;
    
            }
    
            // Else retry
    
        } catch (InterruptedException ie) {
    
            // On interruption, re-check runState
    
        }
    
    }

}

 private Runnable getTask() {//新版
        boolean timedOut = false; // Did the last poll() time out?

        for (;;) {
            int c = ctl.get();
            int rs = runStateOf(c);

            // Check if queue empty only if necessary.
            if (rs >= SHUTDOWN && (rs >= STOP || workQueue.isEmpty())) {
                decrementWorkerCount();
                return null;
            }

            int wc = workerCountOf(c);

            // Are workers subject to culling?
            boolean timed = allowCoreThreadTimeOut || wc > corePoolSize;

            if ((wc > maximumPoolSize || (timed && timedOut))
                && (wc > 1 || workQueue.isEmpty())) {
                if (compareAndDecrementWorkerCount(c))
                    return null;
                continue;
            }

            try {
                Runnable r = timed ?
                    workQueue.poll(keepAliveTime, TimeUnit.NANOSECONDS) :
                    workQueue.take();
                if (r != null)
                    return r;
                timedOut = true;
            } catch (InterruptedException retry) {
                timedOut = false;
            }
        }
    }
```

 　　在getTask中，先判断当前线程池状态，如果runState大于SHUTDOWN（即为STOP或者TERMINATED），则直接返回null。

　　如果runState为SHUTDOWN或者RUNNING，则从任务缓存队列取任务。

　　如果当前线程池的线程数大于核心池大小corePoolSize或者允许为核心池中的线程设置空闲存活时间，则调用poll(time,timeUnit)来取任务，这个方法会等待一定的时间，如果取不到任务就返回null。

　　然后判断取到的任务r是否为null，为null则通过调用workerCanExit()方法来判断当前worker是否可以退出，我们看一下workerCanExit()的实现：

```java
private boolean workerCanExit() {

    final ReentrantLock mainLock = this.mainLock;
    
    mainLock.lock();
    
    boolean canExit;
    
    //如果runState大于等于STOP，或者任务缓存队列为空了
    
    //或者  允许为核心池线程设置空闲存活时间并且线程池中的线程数目大于1
    
    try {
    
        canExit = runState >= STOP ||
    
            workQueue.isEmpty() ||
    
            (allowCoreThreadTimeOut &&
    
             poolSize > Math.max(1, corePoolSize));
    
    } finally {
    
        mainLock.unlock();
    
    }
    
    return canExit;

}
```

 　　也就是说如果线程池处于STOP状态、或者任务队列已为空或者允许为核心池线程设置空闲存活时间并且线程数大于1时，允许worker退出。如果允许worker退出，则调用interruptIdleWorkers()中断处于空闲状态的worker，我们看一下interruptIdleWorkers()的实现：

```java
void interruptIdleWorkers() {

    final ReentrantLock mainLock = this.mainLock;
    
    mainLock.lock();
    
    try {
    
        for (Worker w : workers)  //实际上调用的是worker的interruptIfIdle()方法
    
            w.interruptIfIdle();
    
    } finally {
    
        mainLock.unlock();
    
    }

}
```

 　　从实现可以看出，它实际上调用的是worker的interruptIfIdle()方法，在worker的interruptIfIdle()方法中：

```java
void interruptIfIdle() {

    final ReentrantLock runLock = this.runLock;
    
    if (runLock.tryLock()) {    //注意这里，是调用tryLock()来获取锁的，因为如果当前worker正在执行任务，锁已经被获取了，是无法获取到锁的
    
                                //如果成功获取了锁，说明当前worker处于空闲状态
    
        try {
    
    if (thread != Thread.currentThread())  
    
    thread.interrupt();
    
        } finally {
    
            runLock.unlock();
    
        }
    
    }

}
```

  　　这里有一个非常巧妙的设计方式，假如我们来设计线程池，可能会有一个任务分派线程，当发现有线程空闲时，就从任务缓存队列中取一个任务交给空闲线程执行。但是在这里，并没有采用这样的方式，因为这样会要额外地对任务分派线程进行管理，无形地会增加难度和复杂度，这里直接让执行完任务的线程去任务缓存队列里面取任务来执行。

 　　我们再看addIfUnderMaximumPoolSize方法的实现，这个方法的实现思想和addIfUnderCorePoolSize方法的实现思想非常相似，唯一的区别在于addIfUnderMaximumPoolSize方法是在线程池中的线程数达到了核心池大小并且往任务队列中添加任务失败的情况下执行的：

```java
private boolean addIfUnderMaximumPoolSize(Runnable firstTask) {

    Thread t = null;
    
    final ReentrantLock mainLock = this.mainLock;
    
    mainLock.lock();
    
    try {
    
        if (poolSize < maximumPoolSize && runState == RUNNING)
    
            t = addThread(firstTask);
    
    } finally {
    
        mainLock.unlock();
    
    }
    
    if (t == null)
    
        return false;
    
    t.start();
    
    return true;

}
```

 　　看到没有，其实它和addIfUnderCorePoolSize方法的实现基本一模一样，只是if语句判断条件中的poolSize < maximumPoolSize不同而已。

　　到这里，大部分朋友应该对任务提交给线程池之后到被执行的整个过程有了一个基本的了解，下面总结一下：

　　1）首先，要清楚corePoolSize和maximumPoolSize的含义；

　　2）其次，要知道Worker是用来起到什么作用的；

#### 总体流程

　　3）要知道任务提交给线程池之后的处理策略，这里总结一下主要有4点：

如果当前线程池中的线程数目小于corePoolSize，则每来一个任务，就会创建一个线程去执行这个任务；
如果当前线程池中的线程数目>=corePoolSize，则每来一个任务，会尝试将其添加到任务缓存队列当中，若添加成功，则该任务会等待空闲线程将其取出去执行；若添加失败（一般来说是任务缓存队列已满），则会尝试创建新的线程去执行这个任务；
如果当前线程池中的线程数目达到maximumPoolSize，则会采取任务拒绝策略进行处理；
如果线程池中的线程数量大于 corePoolSize时，如果某线程空闲时间超过keepAliveTime，线程将被终止，直至线程池中的线程数目不大于corePoolSize；如果允许为核心池中的线程设置存活时间，那么核心池中的线程空闲时间超过keepAliveTime，线程也会被终止。

### 3.线程池中的线程初始化

　　默认情况下，创建线程池之后，线程池中是没有线程的，需要提交任务之后才会创建线程。

　　在实际中如果需要线程池创建之后立即创建线程，可以通过以下两个方法办到：

prestartCoreThread()：初始化一个核心线程；
prestartAllCoreThreads()：初始化所有核心线程
　　下面是这2个方法的实现：

```java
public boolean prestartCoreThread() {

    return addIfUnderCorePoolSize(null); //注意传进去的参数是null

}

 

public int prestartAllCoreThreads() {

    int n = 0;
    
    while (addIfUnderCorePoolSize(null))//注意传进去的参数是null
    
        ++n;
    
    return n;

}
```

 　　注意上面传进去的参数是null，根据第2小节的分析可知如果传进去的参数为null，则最后执行线程会阻塞在getTask方法中的

1

r = workQueue.take();

 　　即等待任务队列中有任务。

### 4.任务缓存队列及排队策略

　　在前面我们多次提到了任务缓存队列，即workQueue，它用来存放等待执行的任务。

　　workQueue的类型为BlockingQueue<Runnable>，通常可以取下面三种类型：

　　1）ArrayBlockingQueue：基于数组的先进先出队列，此队列创建时必须指定大小；

　　2）LinkedBlockingQueue：基于链表的先进先出队列，如果创建时没有指定此队列大小，则默认为Integer.MAX_VALUE；

　　3）synchronousQueue：这个队列比较特殊，它不会保存提交的任务，而是将直接新建一个线程来执行新来的任务。

### 5.任务拒绝策略

　　当线程池的任务缓存队列已满并且线程池中的线程数目达到maximumPoolSize，如果还有任务到来就会采取任务拒绝策略，通常有以下四种策略：

> ThreadPoolExecutor.AbortPolicy:丢弃任务并抛出RejectedExecutionException异常。
>
> ThreadPoolExecutor.DiscardPolicy：也是丢弃任务，但是不抛出异常。
>
> ThreadPoolExecutor.DiscardOldestPolicy：丢弃队列最前面的任务，然后重新尝试执行任务（重复此过程）
>
> ThreadPoolExecutor.CallerRunsPolicy：由调用线程处理该任务



从runworker方法中我们可以看到对应的Exception都是保存在thrownn中，在finally中交给了 `afterExecute`进行了处理。

```java
try {
    beforeExecute(wt, task);
    Throwable thrown = null;
    try {
        task.run();
    } catch (RuntimeException x) {
        thrown = x; throw x;
    } catch (Error x) {
        thrown = x; throw x;
    } catch (Throwable x) {
        thrown = x; throw new Error(x);
    } finally {
        afterExecute(task, thrown);
    }
} finally {
    task = null;
    w.completedTasks++;
    w.unlock();
}
```

### 6.线程池的关闭

ThreadPoolExecutor提供了两个方法，用于线程池的关闭，分别是shutdown()和shutdownNow()，其中：

shutdown()：不会立即终止线程池，而是要等所有任务缓存队列中的任务都执行完后才终止，但再也不会接受新的任务
shutdownNow()：立即终止线程池，并尝试打断正在执行的任务，并且清空任务缓存队列，返回尚未执行的任务
7.线程池容量的动态调整

　　ThreadPoolExecutor提供了动态调整线程池容量大小的方法：setCorePoolSize()和setMaximumPoolSize()，

setCorePoolSize：设置核心池大小
setMaximumPoolSize：设置线程池最大能创建的线程数目大小
　　当上述参数从小变大时，ThreadPoolExecutor进行线程赋值，还可能立即创建新的线程来执行任务。

## 三.使用示例

　　前面我们讨论了关于线程池的实现原理，这一节我们来看一下它的具体使用：

```java
public class Test {

     public static void main(String[] args) {   
    
         ThreadPoolExecutor executor = new ThreadPoolExecutor(5, 10, 200, TimeUnit.MILLISECONDS,
    
                 new ArrayBlockingQueue<Runnable>(5)       
         for(int i=0;i<15;i++){
    
             MyTask myTask = new MyTask(i);
    
             executor.execute(myTask);
    
             System.out.println("线程池中线程数目："+executor.getPoolSize()+"，队列中等待执行的任务数目："+
    
             executor.getQueue().size()+"，已执行玩别的任务数目："+executor.getCompletedTaskCount());
    
         }
    
         executor.shutdown();
    
     }

}

class MyTask implements Runnable {

    private int taskNum;
    public MyTask(int num) {
    
        this.taskNum = num;
    
    }
    @Override
    
    public void run() {
    
        System.out.println("正在执行task "+taskNum);
    
        try {
    
            Thread.currentThread().sleep(4000);
    
        } catch (InterruptedException e) {
    
            e.printStackTrace();
    
        }
    
        System.out.println("task "+taskNum+"执行完毕");
    
    }

}
```

 　　执行结果：

```

正在执行task 0
线程池中线程数目：1，队列中等待执行的任务数目：0，已执行玩别的任务数目：0
线程池中线程数目：2，队列中等待执行的任务数目：0，已执行玩别的任务数目：0
正在执行task 1
线程池中线程数目：3，队列中等待执行的任务数目：0，已执行玩别的任务数目：0
正在执行task 2
线程池中线程数目：4，队列中等待执行的任务数目：0，已执行玩别的任务数目：0
正在执行task 3
线程池中线程数目：5，队列中等待执行的任务数目：0，已执行玩别的任务数目：0
正在执行task 4
线程池中线程数目：5，队列中等待执行的任务数目：1，已执行玩别的任务数目：0
线程池中线程数目：5，队列中等待执行的任务数目：2，已执行玩别的任务数目：0
线程池中线程数目：5，队列中等待执行的任务数目：3，已执行玩别的任务数目：0
线程池中线程数目：5，队列中等待执行的任务数目：4，已执行玩别的任务数目：0
线程池中线程数目：5，队列中等待执行的任务数目：5，已执行玩别的任务数目：0
线程池中线程数目：6，队列中等待执行的任务数目：5，已执行玩别的任务数目：0
正在执行task 10
线程池中线程数目：7，队列中等待执行的任务数目：5，已执行玩别的任务数目：0
正在执行task 11
线程池中线程数目：8，队列中等待执行的任务数目：5，已执行玩别的任务数目：0
正在执行task 12
线程池中线程数目：9，队列中等待执行的任务数目：5，已执行玩别的任务数目：0
正在执行task 13
线程池中线程数目：10，队列中等待执行的任务数目：5，已执行玩别的任务数目：0
正在执行task 14
task 3执行完毕
task 0执行完毕
task 2执行完毕
task 1执行完毕
正在执行task 8
正在执行task 7
正在执行task 6
正在执行task 5
task 4执行完毕
task 10执行完毕
task 11执行完毕
task 13执行完毕
task 12执行完毕
正在执行task 9
task 14执行完毕
task 8执行完毕
task 5执行完毕
task 7执行完毕
task 6执行完毕
task 9执行完毕
```

　　从执行结果可以看出，当线程池中线程的数目大于5时，便将任务放入任务缓存队列里面，当任务缓存队列满了之后，便创建新的线程。如果上面程序中，将for循环中改成执行20个任务，就会抛出任务拒绝异常了。

　　不过在java doc中，并不提倡我们直接使用ThreadPoolExecutor，而是使用Executors类中提供的几个静态方法来创建线程池：

#### ThreadPoolExecutor的四个静态线程池

```
Executors.newCachedThreadPool();        //创建一个缓冲池，缓冲池容量大小为Integer.MAX_VALUE

Executors.newSingleThreadExecutor();   //创建容量为1的缓冲池

Executors.newFixedThreadPool(int);    //创建固定容量大小的缓冲池
```

 　　下面是这三个静态方法的具体实现;

```
public static ExecutorService newFixedThreadPool(int nThreads) {

    return new ThreadPoolExecutor(nThreads, nThreads,
    
                                  0L, TimeUnit.MILLISECONDS,
    
                                  new LinkedBlockingQueue<Runnable>());

}

public static ExecutorService newSingleThreadExecutor() {

    return new FinalizableDelegatedExecutorService
    
        (new ThreadPoolExecutor(1, 1,
    
                                0L, TimeUnit.MILLISECONDS,
    
                                new LinkedBlockingQueue<Runnable>()));

}

public static ExecutorService newCachedThreadPool() {

    return new ThreadPoolExecutor(0, Integer.MAX_VALUE,
    
                                  60L, TimeUnit.SECONDS,
    
                                  new SynchronousQueue<Runnable>());

}
```

　　从它们的具体实现来看，它们实际上也是调用了ThreadPoolExecutor，只不过参数都已配置好了。

> 　newFixedThreadPool创建的线程池corePoolSize和maximumPoolSize值是相等的，它使用的LinkedBlockingQueue；
>
> 　newSingleThreadExecutor将corePoolSize和maximumPoolSize都设置为1，也使用的LinkedBlockingQueue；
>
> 　newCachedThreadPool将corePoolSize设置为0，将maximumPoolSize设置为Integer.MAX_VALUE，使用的SynchronousQueue，也就是说来了任务就创建线程运行，当线程空闲超过60秒，就销毁线程。
>
> 　newScheduledThreadPool：创建一个大小无限的线程池。此线程池支持定时以及周期性执行任务的需求。

　　实际中，如果Executors提供的三个静态方法能满足要求，就尽量使用它提供的三个方法，因为自己去手动配置ThreadPoolExecutor的参数有点麻烦，要根据实际任务的类型和数量来进行配置。

　　另外，如果ThreadPoolExecutor达不到要求，可以自己继承ThreadPoolExecutor类进行重写。

## 四.如何合理配置线程池的大小

　　本节来讨论一个比较重要的话题：如何合理配置线程池大小，仅供参考。

　　一般需要根据任务的类型来配置线程池大小：

　　如果是CPU密集型任务，就需要尽量压榨CPU，参考值可以设为 NCPU+1

　　如果是IO密集型任务，参考值可以设置为2*NCPU

　　当然，这只是一个参考值，具体的设置还需要根据实际情况进行调整，比如可以先将线程池大小设置为参考值，再观察任务运行情况和系统负载、资源利用率来进行适当调整。

#### callable+Future+ScheduledThreadPoolExecutor



- Callable和Future，它俩很有意思的，一个产生结果，一个拿到结果。

> 在Future接口中声明了5个方法，下面依次解释每个方法的作用：
> 1、cancel方法用来取消任务，如果取消任务成功则返回true，如果取消任务失败则返回false。参数mayInterruptIfRunning表示是否允许取消正在执行却没有执行完毕的任务，如果设置true，则表示可以取消正在执行过程中的任务。如果任务已经完成，则无论mayInterruptIfRunning为true还是false，此方法肯定返回false，即如果取消已经完成的任务会返回false；如果任务正在执行，若mayInterruptIfRunning设置为true，则返回true，若mayInterruptIfRunning设置为false，则返回false；如果任务还没有执行，则无论mayInterruptIfRunning为true还是false，肯定返回true。
> 2、isCancelled方法表示任务是否被取消成功，如果在任务正常完成前被取消成功，则返回 true。
> 3、isDone方法表示任务是否已经完成，若任务完成，则返回true；
> 4、get()方法用来获取执行结果，这个方法会产生阻塞，会一直等到任务执行完毕才返回；
> 5、get(long timeout, TimeUnit unit)用来获取执行结果，如果在指定时间内，还没获取到结果，就直接返回null。　　
> 也就是说Future提供了三种功能：　
> 　1）判断任务是否完成；　
> 　2）能够中断任务；　　
> 　3）能够获取任务执行结果。　　
> 　因为Future只是一个接口，所以是无法直接用来创建对象使用的，因此就有了下面的FutureTask。

- Future表示一个可能还没有完成的异步任务的结果，针对这个结果可以添加Callback以便在任务执行成功或失败后作出相应的操作。
- Callable接口中只有一个call()方法，和Runnable相比，该方法有返回值并允许抛出异常
- FutureTask是Runnable, Future接口的实现类。

> FutureTask里面有两个构造方法，不过一般用第一个，至于为什么要有第二个构造方法，我认为是为了兼容以前的类，使得ScheduledThreadPoolExecutor在extends FutureTask时既可以用到callable也可以用到runnable。会把一个实现Runnable接口的类通过new RunnableAdapter<T>（Runnable task, T result)方法转化成Callable接口的类来继续运行。
> 而RunnableFuture接口又是继承自Runnable和Future.
>
> 所以FutureTask间接的实现了Future接口，并且为Future里面的5个抽象方法添加具体的实现。

- RunnableFuture
  - 这个接口同时继承Future接口和Runnable接口，在成功执行run（）方法后，可以通过Future访问执行结果。这个接口都实现类是FutureTask,一个可取消的异步计算，这个类提供了Future的基本实现，后面我们的demo也是用这个类实现，它实现了启动和取消一个计算，查询这个计算是否已完成，恢复计算结果。计算的结果只能在计算已经完成的情况下恢复。如果计算没有完成，get方法会阻塞，一旦计算完成，这个计算将不能被重启和取消，除非调用runAndReset方法。
  - FutureTask能用来包装一个Callable或Runnable对象，因为它实现了Runnable接口，而且它能被传递到Executor进行执行。为了提供单例类，这个类在创建自定义的工作类时提供了protected构造函数。

* ScheduledThreadPoolExecutor继承了ThreadPoolExecutor，里面多添加了对callable线程池的实现，以及实现了ScheduledExecutorService接口，添加了一些基于时间的特殊功能

* future 是对callable对象的操作的接口
* ScheduledFutureTask是对ThreadPoolExecutor中runnable对象的操作的扩展以及callable对象的类似于worker功能的实现。

```java
public class ScheduledThreadPoolExecutor
        extends ThreadPoolExecutor
        implements ScheduledExecutorService {
private class ScheduledFutureTask<V>
        extends FutureTask<V> implements RunnableScheduledFuture<V> {

    /** Sequence number to break ties FIFO */
    private final long sequenceNumber;

    /** The time the task is enabled to execute in nanoTime units */
    private long time;

    /**
     * Period in nanoseconds for repeating tasks.  A positive
     * value indicates fixed-rate execution.  A negative value
     * indicates fixed-delay execution.  A value of 0 indicates a
     * non-repeating task.
     */
    private final long period;

    /** The actual task to be re-enqueued by reExecutePeriodic */
    RunnableScheduledFuture<V> outerTask = this;

    /**
     * Index into delay queue, to support faster cancellation.
     */
    int heapIndex;

    /**
     * Creates a one-shot action with given nanoTime-based trigger time.
     */
    ScheduledFutureTask(Runnable r, V result, long ns) {
        super(r, result);
        this.time = ns;
        this.period = 0;
        this.sequenceNumber = sequencer.getAndIncrement();
    }

    /**
     * Creates a periodic action with given nano time and period.
     */
    ScheduledFutureTask(Runnable r, V result, long ns, long period) {
        super(r, result);
        this.time = ns;
        this.period = period;
        this.sequenceNumber = sequencer.getAndIncrement();
    }

    /**
     * Creates a one-shot action with given nanoTime-based trigger time.
     */
    ScheduledFutureTask(Callable<V> callable, long ns) {
        super(callable);
        this.time = ns;
        this.period = 0;
        this.sequenceNumber = sequencer.getAndIncrement();
    }

    public long getDelay(TimeUnit unit) {
        return unit.convert(time - now(), NANOSECONDS);
    }

    public int compareTo(Delayed other) {
        if (other == this) // compare zero if same object
            return 0;
        if (other instanceof ScheduledFutureTask) {
            ScheduledFutureTask<?> x = (ScheduledFutureTask<?>)other;
            long diff = time - x.time;
            if (diff < 0)
                return -1;
            else if (diff > 0)
                return 1;
            else if (sequenceNumber < x.sequenceNumber)
                return -1;
            else
                return 1;
        }
        long diff = getDelay(NANOSECONDS) - other.getDelay(NANOSECONDS);
        return (diff < 0) ? -1 : (diff > 0) ? 1 : 0;
    }

    /**
     * Returns {@code true} if this is a periodic (not a one-shot) action.
     *
     * @return {@code true} if periodic
     */
    public boolean isPeriodic() {
        return period != 0;
    }

    /**
     * Sets the next time to run for a periodic task.
     */
    private void setNextRunTime() {
        long p = period;
        if (p > 0)
            time += p;
        else
            time = triggerTime(-p);
    }

    public boolean cancel(boolean mayInterruptIfRunning) {
        boolean cancelled = super.cancel(mayInterruptIfRunning);
        if (cancelled && removeOnCancel && heapIndex >= 0)
            remove(this);
        return cancelled;
    }

    /**
     * Overrides FutureTask version so as to reset/requeue if periodic.
     */
    public void run() {
        boolean periodic = isPeriodic();
        if (!canRunInCurrentRunState(periodic))
            cancel(false);
        else if (!periodic)
            ScheduledFutureTask.super.run();
        else if (ScheduledFutureTask.super.runAndReset()) {
            setNextRunTime();
            reExecutePeriodic(outerTask);
        }
    }
}
```

# 进程

这个状态包括存放在某个线程的上下文中，上下文是由程序正确运行所需的状态组成的，包括存放在内存中的程序的代码和数据，他的栈、通用目的寄存器内容、程序计数器、环境变量以及打开文件描述符的集合。

## 用户模式和内核模式

处理器通常用某个寄存器中的一个模式为（mode bit）来提供这种功能的，该寄存器描述了进程当前享有的特权。

当设置了模式位时，进程就运行在内存模式，一个运行在内核模式的进程可以执行指令集中的任何指令，并且可以访问系统中的任何内存位置。

没有时在用户模式，进程就运行在用户模式中，用户模式中的进程不允许执行特权指令（privileged instruction），比如停止处理器、改变模式位，或者发起一个I/O操作。也不允许用户模式中的进程直接引用地址空间中内核区内的代码和数据，需要通过系统调用接口间接地访问内核代码和数据

运行应用程序代码的进程初始时是在用户模式中的。进程从用户模式变为内核模式的唯一方法是通过诸如==中断、故障或者陷入系统调用这样的异常。==

## 上下文切换

内核为每个进程维持一个上下文，上下文就是内核重新启动一个被抢占的进程所需的状态

![image-20200925163736862](F:\Typora数据储存\高级语言\JAVA\JAVA并发.assets\image-20200925163736862.png)

# JAVA与线程

## 1、内核线程的实现

使用内核线程实现的方式也被称为1：1实现。内核线程（Kernel-Level Thread，KLT）就是直接由操作系统内核（Kernel，下称内核）支持的线程，这种线程由内核来完成线程切换，内核通过操纵调度器（Scheduler）对线程进行调度，并负责将线程的任务映射到各个处理器上。每个内核线程可以视为内核的一个分身，这样操作系统就有能力同时处理多件事情，支持多线程的内核就称为多线程内核（Multi-Threads Kernel）。

程序一般不会直接使用内核线程，而是使用内核线程的一种高级接口——轻量级进程（LightWeight Process，LWP），轻量级进程就是我们通常意义上所讲的线程，由于每个轻量级进程都由一个内核线程支持，因此只有先支持内核线程，才能有轻量级进程。这种轻量级进程与内核线程之间1：1的关系称为一对一的线程模型，如图12-3所示。**LWP调用KLT暴露的接口，由KLT使用CPU**

![内核级线程的实现方式](https://img-blog.csdn.net/20160712204335517)

适用：线程切换切换次数较少的

#### 优点

在多处理器系统中，内核能够同时调度同一进程中多个线程并行执行到**多个处理器**中；如果进程中的一个线程被阻塞，内核可以调度同一个进程中的另一个线程；内核支持线程具有很小的数据结构和堆栈，线程的切换比较快，切换开销小；内核本身也可以使用多线程的方式来实现。

#### 缺点

即使CPU在同一个进程的多个线程之间切换，也需要陷入内核，因此其速度和效率不如用户级线程。



## 2、 用户线程的实现

使用用户线程实现的方式被称为1：N实现。广义上来讲，一个线程只要不是内核线程，都可以认为是用户线程（User Thread，UT）的一种，因此从这个定义上看，轻量级进程也属于用户线程，但轻量级进程的实现始终是建立在内核之上的，许多操作都要进行系统调用，因此效率会受到限制，并不具备通常意义上的用户线程的优点。

而狭义上的用户线程指的是完全建立在用户空间的线程库上，系统内核不能感知到用户线程的存在及如何实现的。**用户线程的建立、同步、销毁和调度完全在用户态中完成，不需要内核的帮助。如果程序实现得当**，这种线程不需要切换到内核态，因此操作可以是非常快速且低消耗的，也能够支持规模更大的线程数量，部分高性能数据库中的多线程就是由用户线程实现的。这种**进程与用户线程之间1：N**的关系称为一对多的线程模型，如图12-4所示。

用户线程有两种实现方式，一种还是使用操作系统提供的API，这就需要内核线程的参与，但是切换的时候，内核线程的部分不再需要保存恢复，只是针对用户线程进行调度，最轻便是仅改变内核线程对应用户线程的栈的地址入口参数。（M:N?）

另一种是通过一种运行时（内核模式？）直接调用CPU等硬件与线程调度(1:N?)

​																![用户级线程的实现方式,](https://img-blog.csdn.net/20160712204159672)  

适用：线程切换较频繁的

#### 优点

线程的切换无需陷入内核，故切换开销小，速度非常快；

#### 缺点

系统调用的阻塞问题：对应用程序来讲，**同一进程中只能同时有一个线程在运行**，一个线程的阻塞将导致整个进程中所有线程的阻塞；由于这里的处理器时间片分配是以进程为基本单位，所以每个线程执行的时间相对减少。

## 3、 混合实现

线程除了依赖内核线程实现和完全由用户程序自己实现之外，还有一种将内核线程与用户线程一起使用的实现方式，被称为N：M实现。在这种混合实现下，既存在用户线程，也存在轻量级进程。 用户线程还是完全建立在用户空间中，因此用户线程的创建、切换、析构等操作依然廉价，并且可以支持大规模的用户线程并发。而操作系统支持的轻量级进程则作为用户线程和内核线程之间的桥梁，这样可以使用内核提供的线程调度功能及处理器映射，并且用户线程的系统调用要通过轻量级进程来完成，**==这大大降低了整个进程被完全阻塞的风险。==**在这种混合模式中，用户线程与轻量级进程的数量比是不定的，是N：M的关系，如图12-5所示，这种就是多对多的线程模型。 

​																		![用户级与内核级的组合实现方式](https://img-blog.csdn.net/20160712204353861)

## 4、 用户级线程和内核级线程的区别

1. 在只有用户级线程的系统内，CPU调度以进程为单位，处于运行状态的进程中的多个线程，由用户程序控制线程的轮换运行；

   在1：1的系统内，CPU调度则以线程为单位，由OS的线程调度程序负责线程的调度。

2.  用户级线程的创建、撤消和调度不需要OS内核的支持，是在语言（如Java）这一级处理的；而内核支持线程的创建、撤消和调度都需OS内核提供支持，而且与进程的创建、撤消和调度大体是相同的。**若是在用户级线程则依据自己的实现在分配的进程空间中进行线程创建、撤销和调度，即线程内存块的创建、撤销、地址返回调度，可以较为方便的实现，而OS为了本身的可靠性，除了需要同上的步骤，还需要进行更多的死锁预防措施等的校验和避免。**

![用户级线程和内核级线程的区别](https://img-blog.csdn.net/20160712204436623)

## 5、Java线程调度 

线程调度是指系统为线程分配处理器使用权的过程，调度主要方式有两种，分别是协同式（Cooperative Threads-Scheduling）线程调度和抢占式（Preemptive Threads-Scheduling）线程调度。 

如果使用协同式调度的多线程系统，线程的执行时间由线程本身来控制，线程把自己的工作执行完了之后，要主动通知系统切换到另外一个线程上去。

如果使用抢占式调度的多线程系统，那么每个线程将由系统来分配执行时间，线程的切换不由线程本身来决定。

可以设置线程优先级，但是java线程优先级和操作系统优先级不一定一一对应，需要借助相应表完成对应，若是1：1线程最终还是取决于操作系统

## 6、 线程状态

![image-20200922161735588](F:\Typora数据储存\高级语言\JAVA\JAVA并发.assets\image-20200922161735588.png)

# JAVA 与 协程

java为什么内核线程调度切换起来成本就要更高？

内核线程的调度成本主要来自于用户态与核心态之间的状态转换，而这两种状态转换的**开销主要来自于响应中断、保护和恢复执行现场的成本**。请读者试想以下场景，假设发生了这样一次线程切换：

线程A -> 系统中断 -> 线程B 

处理器要去执行线程A的程序代码时，并不是仅有代码程序就能跑得起来，程序是数据与代码的组合体，代码执行时还必须要有上下文数据的支撑。而这里说的“上下文”，以程序员的角度来看，是方法调用过程中的各种局部的变量与资源；以线程的角度来看，是方法的调用栈中存储的各类信息； 而以操作系统和硬件的角度来看，则是存储在内存、缓存和寄存器中的一个个具体数值。物理硬件的各种存储设备和寄存器是被操作系统内所有线程共享的资源，==当中断发生，从线程A切换到线程B去执行之前，操作系统首先要把线程A的上下文数据妥善保管好，然后把寄存器、内存分页等恢复到线程B挂起时候的状态，这样线程B被重新激活后才能仿佛从来没有被挂起过==。这种保护和恢复现场的工作，免不了涉及一系列数据在各种寄存器、缓存中的来回拷贝，当然不可能是一种轻量级的操作。 

如果说内核线程的切换开销是来自于保护和恢复现场的成本，那**如果改为采用用户线程，这部分开销就能够省略掉吗？答案是“不能”。**但是，一旦把保护、恢复现场及调度的工作从操作系统交到程序员手上，那我们就可以打开脑洞，通过玩出很多新的花样来缩减这些开销。操作系统只会从他的角度出发采取最安全的做法，而不能考虑到多个语言的特性来设计优化。

==由于最初多数的用户线程是被设计成协同式调度（Cooperative Scheduling）的，所以它有了一个别名——“协程”（Coroutine）。==又由于这时候的协程会完整地做调用栈的保护、恢复工作，所以今天也被称为“有栈协程”（Stackfull Coroutine），起这样的名字是为了便于跟后来的“无栈协程”（Stackless Coroutine）区分开。

不过还是可以简单提一下它的典型应用，即各种语言中的await、async、yield这类关键字。无栈协程本质上是一种有限状态机，状态保存在闭包里，自然比有栈协程恢复调用栈要轻量得多，但功能也相对更有限。

协程的主要优势是轻量，无论是有栈协程还是无栈协程，都要比传统内核线程要轻量得多。



## 纤程

Loom项目背后的意图是重新提供对用户线程的支持，但**与过去的绿色线程不同，这些新功能不是为了取代当前基于操作系统的线程实现**，而是会有两个并发编程模型在Java虚拟机中并存，可以在程序中同时使用。

在新并发模型下，一段使用纤程并发的代码会被分为两部分——**执行过程（Continuation）和调度器（Scheduler）**。执行过程主要用于维护执行现场，保护、恢复上下文状态，而调度器则负责编排所有要执行的代码的顺序。



# 线程安全与锁优化 

## 1、 不可变

在JAVA语言里面，不可变（Immutable）的对象一定是线程安全的，无论是对象的方法实现还是方法的调用者，都不需要再进行任何线程的安全保障措施。

Java语言中，**如果多线程共享的数据是一个基本数据类型**，那么只要在定义时**使用final关键字修饰它就可以保证它是不可变的**。**如果共享数据是一个对象**，由于Java语言目前暂时还没有提供值类型的支持，那就**需要对象自行保证其行为不会对其状态产生任何影响才行。**

比如:

> java.lang.String类它是一个典型的不可变对象，用户调用它的substring()、replace()和concat()这些方法都不会影响它原来的值，只会返回一个新构造的字符串对象。 
>
> 常用的还有枚举类型及 java.lang.Number的部分子类，如Long和Double等数值包装类型、BigInteger和BigDecimal等大数据类型。但同为Number子类型的原子类AtomicInteger和AtomicLong则是可变的

## 2、 绝对线程安全

**不管运行时环境如何，调用者都不需要任何额外的同步措施.**

为了这个的实现，可能需要放弃很多功能或者本身就上了很多的锁

如：java.util.Vector是一个线程安全的容器，但仅是各个方法各自通过synchronized实现了原子性、可见性、有序性，各个方法还是依赖于同一个对象，Vector的get()、remove()和size()方法都是同步的，但是在多线程的环境中，如果不在方法调用端做额外的同步措施，使用这段代码仍然是不安全的。因为如果另一个线程恰好在错误的时间里删除了一个元素，导致序号i已经不再可用，再用i访问数组就会抛出一个ArrayIndexOutOfBoundsException异常。

## 3、 相对线程安全 

相对线程安全就是我们通常意义上所讲的线程安全，它需要保证对这个对象单次的操作是线程安全的，我们在调用的时候不需要进行额外的保障措施，但是对于一些特定顺序的连续调用，就可能需要在调用端使用额外的同步手段来保证调用的正确性。

> 在Java语言中，大部分声称线程安全的类都属于这种类型，例如Vector、HashTable、Collections的synchronizedCollection()方法包装的集合等。 

## 4、 线程兼容 

线程兼容是指对象本身并不是线程安全的，但是可以通过在调用端正确地使用同步手段来保证对象在并发环境中可以安全地使用。我们平常说一个类不是线程安全的，通常就是指这种情况。Java类库API中大部分的类都是线程兼容的，如与前面的Vector和HashTable相对应的集合类ArrayList和HashMap等。

## 5、线程对立 

线程对立是指不管调用端是否采取了同步措施，都无法在多线程环境中并发使用代码。

> 由于Java语言天生就支持多线程的特性，线程对立这种排斥多线程的代码是很少出现的，而且通常都是有害的，应当尽量避免。 
>
> 一个线程对立的例子是Thread类的suspend()和resume()方法。假如suspend()中断的线程就是即将要执行resume()的那个线程，那就 肯定要产生死锁了。也正是这个原因，suspend()和resume()方法都已经被声明废弃了。常见的线程对立的操作还有System.setIn()、Sytem.setOut()和System.runFinalizersOnExit()等

#  线程安全的实现方法

## 1.互斥同步 

互斥同步（Mutual Exclusion & Synchronization）是一种最常见也是最主要的并发正确性保障手段。同步是指在多个线程并发访问共享数据时，保证共享数据在同一个时刻只被一条（或者是一些，当使用信号量的时候）线程使用。而互斥是实现同步的一种手段，临界区（Critical Section）、互斥量（Mutex）和信号量（Semaphore）都是常见的互斥实现方式。==互斥是方法，同步是目的。==

在Java里面，最基本的互斥同步手段就是synchronized关键字，这是一种块结构（BlockStructured）的同步语法。synchronized关键字经过Javac编译之后，会在同步块的前后分别形成monitorenter和monitorexit这两个字节码指令。这两个字节码指令都需要一个reference类型的参数来指明要锁定和解锁的对象。

> ·被synchronized修饰的同步块对同一条线程来说是可重入的。这意味着同一线程反复进入同步块也不会出现自己把自己锁死的情况。 
>
> ·被synchronized修饰的同步块在持有锁的线程执行完毕并释放锁之前，会无条件地阻塞后面其他 线程的进入。这意味着无法像处理某些数据库中的锁那样，强制已获取锁的线程释放锁；也无法强制正在等待锁的线程中断等待或超时退出。 

从执行成本的角度看，==持有锁是一个重量级（Heavy-Weight）的操作==。Java的线程是映射到操作系统的原生内核线程之上的，如果要阻塞或唤醒一条线程，则需要操作系统来帮忙完成，这就不可避免地陷入用户态到核心态的转换中，进行这种状态转换需要耗费很多的处理器时间。尤其是对于代码特别简单的同步块（譬如被synchronized修饰的getter()或setter()方法），状态转换消耗的时间甚至会比用户代码本身执行的时间还要长。

Java类库中新提供了java.util.concurrent包（下文称J.U.C包），其中的java.util.concurrent.locks.Lock接口便成了Java的另一种全新的互斥同步手段。。基于Lock接口，用==户能够以非块结构（Non-Block Structured）来实现互斥同步==，从而摆脱了语言特性的束缚，改为在类库层面去实现同步，这也为日后扩展出不同调度算法、不同特征、不同性能、不同语义的各种锁提供了广阔的空间.

synchronized是在Java语法层面的同步容易理解，而且虚拟机可以在线程和对象的元数据记录synchronized中锁的相关消息，而lock的实现没有记录与线程的互相关系，只是有相应的标志，Java虚拟机很难得知具体哪些锁对象由特定线程锁持有的。

ReentrantLock与synchronized相比增加了一些高级功能，主要有以下三项：==**等待可中断、可实现公平锁及锁可以绑定多个条件**==

·等待可中断：是指当持有锁的线程长期不释放锁的时候，正在等待的线程可以选择放弃等待，改为处理其他事情。可中断特性对处理执行时间非常长的同步块很有帮助。 

·公平锁：是指多个线程在等待同一个锁时，必须按照申请锁的时间顺序来依次获得锁；而非公平锁则不保证这一点，在锁被释放时，任何一个等待锁的线程都有机会获得锁。==synchronized中的锁是非公平的，ReentrantLock在默认情况下也是非公平的，==但可以通过带布尔值的构造函数要求使用公平锁。不过一旦使用了公平锁，将会导致ReentrantLock的性能急剧下降，会明显影响吞吐量。 

·锁绑定多个条件：是指一个ReentrantLock对象可以同时绑定多个Condition对象。在synchronized 中，锁对象的wait()跟它的notify()或者notifyAll()方法配合可以实现一个隐含的条件，如果要和多于一个的条件关联的时候，就不得不额外添加一个锁；而ReentrantLock则无须这样做，多次调用newCondition()方法即可。

==jdk5前代ReentrantLock曾经在性能上领先过synchronized，但6开始，基本持平==

## 2. 非阻塞同步 

互斥同步面临的主要问题是进行线程阻塞和唤醒所带来的性能开销，因此这种同步也被称为阻塞同步（Blocking Synchronization）。从解决问题的方式上看，互斥同步属于一种悲观的并发策略

随着硬件指令集的发展，我们已经有了另外一个选择：基于冲突检测的乐观并发策略通俗地说就是不管风险，先进行操作，如果没有其他线程争用共享数据，那操作就直接成功了；如果共享的数据的确被争用，**产生了冲突，那再进行其他的补偿措施，最常用的补偿措施是不断地重试**，直到出现没有竞争的共享数据为止。这种乐观并发策略的实现不再需要把线程阻塞挂起，因此这种同步操作被 称为非阻塞同步（Non-Blocking Synchronization），使用这种措施的代码也常被称为无锁（Lock-Free）编程。

为什么笔者说使用乐观并发策略需要“硬件指令集的发展”？**因为我们必须要求操作和冲突检测这两个步骤具备原子性**。靠什么来保证原子性？如果这里再使用互斥同步来保证就完全失去意义了，所以我们只能靠硬件来实现这件事情，硬件保证某些从语义上看起来需要多次操作的行为可以只通过一条处理器指令就能完成，这类指令常用的有：

·测试并设置（Test-and-Set）； 

·获取并增加（Fetch-and-Increment）； 

·交换（Swap）； 

·比较并交换（Compare-and-Swap，下文称CAS）； 

·加载链接/条件储存（Load-Linked/Store-Conditional，下文称LL/SC）。

其中，前面的三条是20世纪就已经存在于大多数指令集之中的处理器指令，后面的两条是现代 理器新增的，而且这两条指令的目的和功能也是类似的。在**IA64、x86指令集中有用cmpxchg指令完成的CAS功能**，在SPARC-TSO中也有用casa指令实现的，而在ARM和PowerPC架构下，则需要使用一对ldrex/strex指令来完成LL/SC的功能。因为Java里最终暴露出来的是CAS操作。

**CAS指令需要有三个操作数，分别是内存位置（在Java中可以简单地理解为变量的内存地址，用V表示）、旧的预期值（用A表示）和准备设置的新值（用B表示）。CAS指令执行时，当且仅当V符合 A时，处理器才会用B更新V的值，否则它就不执行更新。但是，不管是否更新了V的值，都会返回V的旧值，上述的处理过程是一个原子操作，执行期间不会被其他线程中断。** 

JDK5后，JAVA类库中才开始使用CAS，而且该操作由**sun.misc.Unsafe类**里面的compareAndSwapInt()和compareAndSwapLong()等几个方法包装提供。HotSpot虚拟机在内部对这些方法做了特殊处理，即时编译出来的结果就是一条平台相关的处理器CAS指令。Integer类中的实现AutoInteger类就用了如上方法。

不过由于Unsafe类在设计上就不是提供给用户程序调用的类 （Unsafe::getUnsafe()的代码中限制了只有启动类加载器（Bootstrap ClassLoader）加载的Class才能访问它），因此在JDK 9之前只有Java类库可以使用CAS，譬如J.U.C包里面的整数原子类，其中的 compareAndSet()和getAndIncrement()等方法都使用了Unsafe类的CAS操作来实现。而如果用户程序也有使用CAS操作的需求，那要么就采用反射手段突破Unsafe的访问限制，要么就只能通过Java类库API来间接使用它。直到JDK 9之后，Java类库才在VarHandle类里开放了面向用户程序使用的CAS操作。

> “ABA问题”

## 3.无同步方案 

可重入代码（Reentrant Code）：这种代码又称纯代码（Pure Code），是指可以在代码执行的任何时刻中断它，转而去执行另外一段代码（包括递归调用它本身），而在控制权返回后，原来的程序不会出现任何错误，也不会对结果有所影响。

**可重入代码有一些共同的特征，例如，不依赖全局变量、存储在堆上的数据和公用的系统资源，用到的状态量都由参数中传入，不调用非可重入的方法等。**我们可以通过一个比较简单的原则来判断代码是否具备可重入性：如果一个方法的返回结果是可以预测的，只要输入了相同的数据，就都能返回相同的结果，那它就满足可重入性的要求，当然也就是线程安全的。

 线程本地存储（Thread Local Storage）：如果一段代码中所需要的数据必须与其他代码共享，那就看看这些共享数据的代码是否能保证在同一个线程中执行。如果能保证，我们就可以把共享数据的==可见范围限制在同一个线程之内==，这样，无须同步也能保证线程之间不出现数据争用的问题。 

通过java.lang.ThreadLocal类来实现线程本地存储的功能。每一个线程的Thread对象中都 有一个ThreadLocalMap对象，这个对象存储了一组以ThreadLocal.threadLocalHashCode为键，以本地线程变量为值的K-V值对，ThreadLocal对象就是当前线程的ThreadLocalMap的访问入口，每一个 ThreadLocal对象都包含了一个独一无二的threadLocalHashCode值，使用这个值就可以在线程K-V值对中找回对应的本地线程变量

# lock()和unlock()是怎么实现的呢？

由lock()和unlock的源码可以看到，它们只是分别调用了sync对象的lock()和release(1)方法。而Sync是ReentrantLock的内部类， 其扩展了AbstractQueuedSynchronizer。

```java
lock()：
final void lock() {
    if (compareAndSetState(0, 1))
    	setExclusiveOwnerThread(Thread.currentThread());
    else
   		acquire(1);
}
}
```

首先用一个CAS操作，判断state是否是0（表示当前锁未被占用），如果是0则把它置为1，并且设置当前线程为该锁的独占线程，表示获取锁成功。当多个线程同时尝试占用同一个锁时，CAS操作只能保证一个线程操作成功，剩下的只能乖乖的去排队啦。（ “非公平”即体现在这里）。
设置state失败，走到了else里面。我们往下看acquire。

第一步。尝试去获取锁。如果尝试获取锁成功，方法直接返回。

第二步，入队。（ 自旋+CAS组合来实现非阻塞的原子操作）

第三步，挂起。 让已经入队的线程尝试获取锁，若失败则会被挂起

```java
public final void acquire(int arg) {
if (!tryAcquire(arg) &&
    acquireQueued(addWaiter(Node.EXCLUSIVE), arg))
    selfInterrupt();
}

```

流程大致为先尝试释放锁，若释放成功，那么查看头结点的状态是否为SIGNAL，如果是则唤醒头结点的下个节点关联的线程，
如果释放失败那么返回false表示解锁失败。这里我们也发现了，每次都只唤起头结点的下一个节点关联的线程。

```java
public void unlock() {
	sync.release(1);
}
public final boolean release(int arg) {
	if (tryRelease(arg)) {
		Node h = head;
		if (h != null && h.waitStatus != 0)
			unparkSuccessor(h);
			return true;
	}
	return false;
}
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20190428152031219.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0J1dHRlcmZseV9yZXN0aW5n,size_16,color_FFFFFF,t_70)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20190428152037165.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0J1dHRlcmZseV9yZXN0aW5n,size_16,color_FFFFFF,t_70)

# synchronized

## 一、概念

synchronized 是 Java 中的关键字，是利用锁的机制来实现同步的。

锁机制有如下两种特性：

- 互斥性：即在同一时间只允许一个线程持有某个对象锁，通过这种特性来实现多线程中的协调机制，这样在同一时间只有一个线程对需同步的代码块(复合操作)进行访问。互斥性我们也往往称为操作的原子性。
- 可见性：必须确保在锁被释放之前，对共享变量所做的修改，对于随后获得该锁的另一个线程是可见的（即在获得锁时应获得最新共享变量的值），否则另一个线程可能是在本地缓存的某个副本上继续操作从而引起不一致。

## 二、对象锁和类锁

### 1. 对象锁

在 Java 中，每个对象都会有一个 monitor 对象，这个对象其实就是 Java 对象的锁，通常会被称为“内置锁”或“对象锁”。类的对象可以有多个，所以每个对象有其独立的对象锁，互不干扰。

### 2. 类锁

在 Java 中，针对每个类也有一个锁，可以称为“类锁”，类锁实际上是通过对象锁实现的，即类的 Class 对象锁。每个类只有一个 Class 对象，所以每个类只有一个类锁，通过类对象的Monitor实现。

##  三、synchronized 的用法分类

synchronized 的用法可以从两个维度上面分类：

### 1. 根据修饰对象分类

synchronized 可以修饰方法和代码块

- 修饰代码块
  - synchronized(this|object) {}
  - synchronized(类.class) {}
- 修饰方法
  - 修饰非静态方法
  - 修饰静态方法

### 2. 根据获取的锁分类

- 获取对象锁
  - synchronized(this|object) {}
  - 修饰非静态方法
- 获取类锁
  - synchronized(类.class) {}
  - 修饰静态方法

## 四、synchronized 的用法详解

这里根据获取的锁分类来分析 synchronized 的用法

### 1. 获取对象锁

- 同步线程类：

```java
class SyncThread implements Runnable {

    @Override
    public void run() {
        String threadName = Thread.currentThread().getName();
        if (threadName.startsWith("A")) {
            async();
        } else if (threadName.startsWith("B")) {
            sync1();
        } else if (threadName.startsWith("C")) {
            sync2();
        }

    }

    /**
     * 异步方法
     */
    private void async() {
        try {
            System.out.println(Thread.currentThread().getName() + "_Async_Start: " + new SimpleDateFormat("HH:mm:ss").format(new Date()));
            Thread.sleep(2000);
            System.out.println(Thread.currentThread().getName() + "_Async_End: " + new SimpleDateFormat("HH:mm:ss").format(new Date()));
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }

    /**
     * 方法中有 synchronized(this|object) {} 同步代码块
     */
    private void sync1() {
        System.out.println(Thread.currentThread().getName() + "_Sync1: " + new SimpleDateFormat("HH:mm:ss").format(new Date()));
        synchronized (this) {
            try {
                System.out.println(Thread.currentThread().getName() + "_Sync1_Start: " + new SimpleDateFormat("HH:mm:ss").format(new Date()));
                Thread.sleep(2000);
                System.out.println(Thread.currentThread().getName() + "_Sync1_End: " + new SimpleDateFormat("HH:mm:ss").format(new Date()));
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }

    /**
     * synchronized 修饰非静态方法
     */
    private synchronized void sync2() {
        System.out.println(Thread.currentThread().getName() + "_Sync2: " + new SimpleDateFormat("HH:mm:ss").format(new Date()));
        try {
            System.out.println(Thread.currentThread().getName() + "_Sync2_Start: " + new SimpleDateFormat("HH:mm:ss").format(new Date()));
            Thread.sleep(2000);
            System.out.println(Thread.currentThread().getName() + "_Sync2_End: " + new SimpleDateFormat("HH:mm:ss").format(new Date()));
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}
```

#### 1.1 对于同一对象

- 测试代码：

```java
public class SyncTest {

    public static void main(String... args) {
        SyncThread syncThread = new SyncThread();
        Thread A_thread1 = new Thread(syncThread, "A_thread1");
        Thread A_thread2 = new Thread(syncThread, "A_thread2");
        Thread B_thread1 = new Thread(syncThread, "B_thread1");
        Thread B_thread2 = new Thread(syncThread, "B_thread2");
        Thread C_thread1 = new Thread(syncThread, "C_thread1");
        Thread C_thread2 = new Thread(syncThread, "C_thread2");
        A_thread1.start();
        A_thread2.start();
        B_thread1.start();
        B_thread2.start();
        C_thread1.start();
        C_thread2.start();
    }
}
```

- 运行结果：

```java
B_thread2_Sync1: 14:44:20
A_thread1_Async_Start: 14:44:20
B_thread1_Sync1: 14:44:20
C_thread1_Sync2: 14:44:20
A_thread2_Async_Start: 14:44:20
C_thread1_Sync2_Start: 14:44:20
A_thread1_Async_End: 14:44:22
A_thread2_Async_End: 14:44:22
C_thread1_Sync2_End: 14:44:22
B_thread1_Sync1_Start: 14:44:22
B_thread1_Sync1_End: 14:44:24
B_thread2_Sync1_Start: 14:44:24
B_thread2_Sync1_End: 14:44:26
C_thread2_Sync2: 14:44:26
C_thread2_Sync2_Start: 14:44:26
C_thread2_Sync2_End: 14:44:28
```

- 结果分析：

  - A 类线程访问方法中没有同步代码块，A 类线程是异步的，所以有线程访问对象的同步代码块时，另外的线程可以访问该对象的非同步代码块：

    ```java
    A_thread1_Async_Start: 14:44:20
    A_thread2_Async_Start: 14:44:20
    A_thread1_Async_End: 14:44:22
    A_thread2_Async_End: 14:44:22
    ```

  - B 类线程访问的方法中有同步代码块，B 类线程是同步的，一个线程在访问对象的同步代码块，另一个访问对象的同步代码块的线程会被阻塞：

    ```
    B_thread1_Sync1_Start: 14:44:22
    B_thread1_Sync1_End: 14:44:24
    B_thread2_Sync1_Start: 14:44:24
    B_thread2_Sync1_End: 14:44:26
    ```

  - synchronized(this|object) {} 代码块 {} 之外的代码依然是异步的：

    ```
    B_thread2_Sync1: 14:44:20
    B_thread1_Sync1: 14:44:20
    ```

  - C 类线程访问的是 synchronized 修饰非静态方法，C 类线程是同步的，一个线程在访问对象的同步代方法，另一个访问对象同步方法的线程会被阻塞：

    ```
    C_thread1_Sync2_Start: 14:44:20
    C_thread1_Sync2_End: 14:44:22
    C_thread2_Sync2_Start: 14:44:26
    C_thread2_Sync2_End: 14:44:28
    ```

  - synchronized 修饰非静态方法，作用范围是整个方法，所以方法中所有的代码都是同步的：

    ```
    C_thread1_Sync2: 14:44:20
    C_thread2_Sync2: 14:44:26
    ```

  - 同一个类中的加对象锁方法顺序执行，由结果可知 B 类和 C 类线程顺序执行，**类中 synchronized(this|object) {} 代码块和 synchronized 修饰非静态方法获取的锁是同一个锁，即该类的对象的对象锁。**所以 B 类线程和 C 类线程也是同步的：

    ```
    B_thread1_Sync1_Start: 14:44:22
    B_thread1_Sync1_End: 14:44:24
    C_thread1_Sync2_Start: 14:44:20
    C_thread1_Sync2_End: 14:44:22
    B_thread2_Sync1_Start: 14:44:24
    B_thread2_Sync1_End: 14:44:26
    C_thread2_Sync2_Start: 14:44:26
    C_thread2_Sync2_End: 14:44:28
    ```

#### 1.2 对于不同对象

- 修改测试代码为：

```java
public class SyncTest {

    public static void main(String... args) {
        Thread A_thread1 = new Thread(new SyncThread(), "A_thread1");
        Thread A_thread2 = new Thread(new SyncThread(), "A_thread2");
        Thread B_thread1 = new Thread(new SyncThread(), "B_thread1");
        Thread B_thread2 = new Thread(new SyncThread(), "B_thread2");           
        Thread C_thread1 = new Thread(new SyncThread(), "C_thread1");
        Thread C_thread2 = new Thread(new SyncThread(), "C_thread2");
        A_thread1.start();
        A_thread2.start();
        B_thread1.start();
        B_thread2.start();
        C_thread1.start();
        C_thread2.start();
    }
}
```

- 运行结果：

```
A_thread2_Async_Start: 15:01:34
C_thread2_Sync2: 15:01:34
B_thread2_Sync1: 15:01:34
C_thread1_Sync2: 15:01:34
B_thread2_Sync1_Start: 15:01:34
B_thread1_Sync1: 15:01:34
C_thread1_Sync2_Start: 15:01:34
A_thread1_Async_Start: 15:01:34
C_thread2_Sync2_Start: 15:01:34
B_thread1_Sync1_Start: 15:01:34
C_thread1_Sync2_End: 15:01:36
A_thread1_Async_End: 15:01:36
C_thread2_Sync2_End: 15:01:36
B_thread2_Sync1_End: 15:01:36
B_thread1_Sync1_End: 15:01:36
A_thread2_Async_End: 15:01:36
```

- 结果分析：
  - 两个线程访问不同对象的 synchronized(this|object) {} 代码块和 synchronized 修饰非静态方法是异步的，同一个类的不同对象的对象锁互不干扰。

### 2 获取类锁

- 同步线程类：

```java
class SyncThread implements Runnable {

    @Override
    public void run() {
        String threadName = Thread.currentThread().getName();
        if (threadName.startsWith("A")) {
            async();
        } else if (threadName.startsWith("B")) {
            sync1();
        } else if (threadName.startsWith("C")) {
            sync2();
        }

    }

    /**
     * 异步方法
     */
    private void async() {
        try {
            System.out.println(Thread.currentThread().getName() + "_Async_Start: " + new SimpleDateFormat("HH:mm:ss").format(new Date()));
            Thread.sleep(2000);
            System.out.println(Thread.currentThread().getName() + "_Async_End: " + new SimpleDateFormat("HH:mm:ss").format(new Date()));
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }

    /**
     * 方法中有 synchronized(类.class) {} 同步代码块
     */
    private void sync1() {
        System.out.println(Thread.currentThread().getName() + "_Sync1: " + new SimpleDateFormat("HH:mm:ss").format(new Date()));
        synchronized (SyncThread.class) {
            try {
                System.out.println(Thread.currentThread().getName() + "_Sync1_Start: " + new SimpleDateFormat("HH:mm:ss").format(new Date()));
                Thread.sleep(2000);
                System.out.println(Thread.currentThread().getName() + "_Sync1_End: " + new SimpleDateFormat("HH:mm:ss").format(new Date()));
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }

    /**
     * synchronized 修饰静态方法
     */
    private synchronized static void sync2() {
        System.out.println(Thread.currentThread().getName() + "_Sync2: " + new SimpleDateFormat("HH:mm:ss").format(new Date()));
        try {
            System.out.println(Thread.currentThread().getName() + "_Sync2_Start: " + new SimpleDateFormat("HH:mm:ss").format(new Date()));
            Thread.sleep(2000);
            System.out.println(Thread.currentThread().getName() + "_Sync2_End: " + new SimpleDateFormat("HH:mm:ss").format(new Date()));
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}
```

#### 2.1对于同一对象

- 测试代码：

```java
public class SyncTest {

    public static void main(String... args) {
        SyncThread syncThread = new SyncThread();
        Thread A_thread1 = new Thread(syncThread, "A_thread1");
        Thread A_thread2 = new Thread(syncThread, "A_thread2");
        Thread B_thread1 = new Thread(syncThread, "B_thread1");
        Thread B_thread2 = new Thread(syncThread, "B_thread2");
        Thread C_thread1 = new Thread(syncThread, "C_thread1");
        Thread C_thread2 = new Thread(syncThread, "C_thread2");
        A_thread1.start();
        A_thread2.start();
        B_thread1.start();
        B_thread2.start();
        C_thread1.start();
        C_thread2.start();
    }
}
```

- 运行结果：

```
B_thread1_Sync1: 15:08:13
C_thread1_Sync2: 15:08:13
B_thread2_Sync1: 15:08:13
A_thread1_Async_Start: 15:08:13
C_thread1_Sync2_Start: 15:08:13
A_thread2_Async_Start: 15:08:13
C_thread1_Sync2_End: 15:08:15
A_thread2_Async_End: 15:08:15
A_thread1_Async_End: 15:08:15
B_thread2_Sync1_Start: 15:08:15
B_thread2_Sync1_End: 15:08:17
B_thread1_Sync1_Start: 15:08:17
B_thread1_Sync1_End: 15:08:19
C_thread2_Sync2: 15:08:19
C_thread2_Sync2_Start: 15:08:19
C_thread2_Sync2_End: 15:08:21
```

- 结果分析：
  - 由结果可以看出，在同一对象的情况下，synchronized(类.class) {} 代码块或 synchronized 修饰静态方法和 synchronized(this|object) {} 代码块和 synchronized 修饰非静态方法的行为一致。

#### 2.2 对于不同对象

- 修改测试代码为：

```java
public class SyncTest {

    public static void main(String... args) {
        Thread A_thread1 = new Thread(new SyncThread(), "A_thread1");
        Thread A_thread2 = new Thread(new SyncThread(), "A_thread2");
        Thread B_thread1 = new Thread(new SyncThread(), "B_thread1");
        Thread B_thread2 = new Thread(new SyncThread(), "B_thread2");
        Thread C_thread1 = new Thread(new SyncThread(), "C_thread1");
        Thread C_thread2 = new Thread(new SyncThread(), "C_thread2");
        A_thread1.start();
        A_thread2.start();
        B_thread1.start();
        B_thread2.start();
        C_thread1.start();
        C_thread2.start();
    }
}
```

- 运行结果：

```
A_thread2_Async_Start: 15:17:28
B_thread2_Sync1: 15:17:28
A_thread1_Async_Start: 15:17:28
B_thread1_Sync1: 15:17:28
C_thread1_Sync2: 15:17:28
C_thread1_Sync2_Start: 15:17:28
C_thread1_Sync2_End: 15:17:30
A_thread2_Async_End: 15:17:30
B_thread1_Sync1_Start: 15:17:30
A_thread1_Async_End: 15:17:30
B_thread1_Sync1_End: 15:17:32
B_thread2_Sync1_Start: 15:17:32
B_thread2_Sync1_End: 15:17:34
C_thread2_Sync2: 15:17:34
C_thread2_Sync2_Start: 15:17:34
C_thread2_Sync2_End: 15:17:36
```

- 结果分析：

  * 两个线程访问不同对象的 synchronized(类.class) {} 代码块或 synchronized 修饰静态方法还是同步的，类中 synchronized(类.class) {} 代码块和 synchronized 修饰静态方法获取的锁是类锁。对于同一个类的不同对象的类锁是同一个。
  * 不同对象同一加类锁方法同步，不同加类锁方法间也同步。

### 3.类锁和对象锁同时存在

**类中同时有 synchronized(类.class) {} 代码块或 synchronized 修饰静态方法和 synchronized(this|object) {} 代码块和 synchronized 修饰非静态方法时会怎样？**

- 修改同步线程类：

```java
class SyncThread implements Runnable {

    @Override
    public void run() {
        String threadName = Thread.currentThread().getName();
        if (threadName.startsWith("A")) {
            async();
        } else if (threadName.startsWith("B")) {
            sync1();
        } else if (threadName.startsWith("C")) {
            sync2();
        }

    }

    /**
     * 异步方法
     */
    private void async() {
        try {
            System.out.println(Thread.currentThread().getName() + "_Async_Start: " + new SimpleDateFormat("HH:mm:ss").format(new Date()));
            Thread.sleep(2000);
            System.out.println(Thread.currentThread().getName() + "_Async_End: " + new SimpleDateFormat("HH:mm:ss").format(new Date()));
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }

    /**
     * synchronized 修饰非静态方法
     */
    private synchronized void sync1() {
        try {
            System.out.println(Thread.currentThread().getName() + "_Sync1_Start: " + new SimpleDateFormat("HH:mm:ss").format(new Date()));
            Thread.sleep(2000);
            System.out.println(Thread.currentThread().getName() + "_Sync1_End: " + new SimpleDateFormat("HH:mm:ss").format(new Date()));
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }

    /**
     * synchronized 修饰静态方法
     */
    private synchronized static void sync2() {
        try {
            System.out.println(Thread.currentThread().getName() + "_Sync2_Start: " + new SimpleDateFormat("HH:mm:ss").format(new Date()));
            Thread.sleep(2000);
            System.out.println(Thread.currentThread().getName() + "_Sync2_End: " + new SimpleDateFormat("HH:mm:ss").format(new Date()));
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}
```

- 修改测试代码：

```
public class SyncTest {

    public static void main(String... args) {
        Thread B_thread1 = new Thread(syncThread, "B_thread1");
        Thread C_thread1 = new Thread(syncThread, "C_thread1");
        B_thread1.start();
        C_thread1.start();
    }
}
```

- 运行结果：

```
B_thread1_Sync1_Start: 15:35:21
C_thread1_Sync2_Start: 15:35:21
B_thread1_Sync1_End: 15:35:23
C_thread1_Sync2_End: 15:35:23
```

- 运行结果分析：
  - 由结果可以看到，同一对象， B 类线程和 C 类线程是异步的，即 synchronized 修饰静态方法和 synchronized 修饰非静态方法是异步的，对于 synchronized(类.class) {} 代码块和 synchronized(this|object) {} 代码块也是一样的。
  - **所以对象锁和类锁是独立的，互不干扰。**

### 4 补充

1. synchronized关键字不能继承。

   对于父类中的 synchronized 修饰方法，子类在覆盖该方法时，默认情况下不是同步的，必须显式的使用 synchronized 关键字修饰才行。

2. 在定义接口方法时不能使用synchronized关键字。

3. 构造方法不能使用synchronized关键字，但可以使用synchronized代码块来进行同步。

4. **使用synchronized锁定方法块的为通过指令monitorenter和monitorexit来完成**

5. **方法的同步并没有通过指令monitorenter和monitorexit来完成（理论上其实也可以通过这两条指令来实现），不过相对于普通方法【没加synchronized的】，其常量池中多了ACC_SYNCHRONIZED标示符。JVM就是根据该标示符来实现方法的同步的：当方法调用时，调用指令将会检查方法的 ACC_SYNCHRONIZED 访问标志是否被设置，如果设置了，执行线程将先获取monitor，获取成功之后才能执行方法体，方法执行完后再释放monitor。在方法执行期间，其他任何线程都无法再获得同一个monitor对象。 其实本质上没有区别，只是方法的同步是一种隐式的方式来实现，无需通过字节码来完成**

6. 下面是摘自《Java虚拟机规范》的话：

   　　Java虚拟机可以支持方法级的同步和方法内部一段指令序列的同步，这两种同步结构都是使用管程（Monitor）来支持的。

      　　方法级的同步是隐式，即无需通过字节码指令来控制的，它实现在方法调用和返回操作之中。**虚拟机可以从方法常量池中的方法表结构中的access_flags设置为ACC_SYNCHRONIZED访问标志区分一个方法是否同步方法。**当方法调用时，调用指令将会检查方法的ACC_SYNCHRONIZED访问标志是否被设置，如果设置了，执行线程将先持有管程，然后再执行方法，最后在方法完成（无论是正常完成还是非正常完成）时释放管程。在方法执行期间，执行线程持有了管程，其他任何线程都无法再获得同一个管程。如果一个同步方法执行期间抛出了异常，并且在方法内部无法处理此异常，那这个同步方法所持有的管程将在异常抛到同步方法之外时自动释放。

      　　同步一段指令集序列通常是由Java语言中的synchronized块来表示的，Java虚拟机的指令集中有monitorenter和monitorexit两条指令来支持synchronized关键字的语义，正确实现synchronized关键字需要编译器与Java虚拟机两者协作支持。

      　　**Java虚拟机中的同步（Synchronization）基于进入和退出管程（Monitor）对象实现。**无论是显式同步（有明确的monitorenter和monitorexit指令）还是隐式同步（依赖方法调用和返回指令实现的）都是如此。

      　　编译器必须确保无论方法通过何种方式完成，方法中调用过的每条monitorenter指令都必须有执行其对应monitorexit指令，而无论这个方法是正常结束还是异常结束。为了保证在方法异常完成时monitorenter和monitorexit指令依然可以正确配对执行，编译器会自动产生一个异常处理器，这个异常处理器声明可处理所有的异常，它的目的就是用来执行monitorexit指令。

### 5、线程安全的单例模式

##### 饿汉式

```java
public class Singleton_hungry {
	/*
	 * 饿汉式：一上来就初始化对象，线程安全， 容易产生垃圾对象，类加载是就初始化，浪费内存
	 */
	private static Singleton_hungry singleton = new Singleton_hungry();

	private Singleton_hungry() {
	}
	
	public static synchronized Singleton_hungry getInstance() {
		return singleton;
	}
}
```

优点：没有加锁，执行效率会提高。
缺点：类加载时就初始化，浪费内存。

##### 懒汉式

借助sychronized

```java
public class Singleton_lazy {
	// 懒汉式:用的时候创建对象，每个判断都需要对象锁，效率较低，仅适合多线程，不适合单线程
	private static Singleton_lazy singleton;

	private Singleton_lazy() {
	}
	
	public static synchronized Singleton_lazy getInstance() {
		if (null == singleton) {
			singleton = new Singleton_lazy();
		}
		return singleton;
	}
}
```

优点：第一次调用才初始化，避免内存浪费
缺点：必须加锁 synchronized 才能保证单例，但加锁会影响效率

##### 双重校验锁

借助sychronized保证线程安全，用volatile禁止重排序，兼容多线程与单线程

```java
public class Singleton_DCL {
	/*
	 * 采用了双锁机制，安全且在多线程情况下能保持高性能,getSingleton() 的性能对应用程序很关键
	 */
	private volatile static Singleton_DCL singleton;

	private Singleton_DCL() {
	}
	
	public static Singleton_DCL getSingleton() {
		if (singleton == null) {
			synchronized (Singleton_DCL.class) {
				if (singleton == null) {
					singleton = new Singleton_DCL();
				}
			}
		}
		return singleton;
	}
```

DCL: double-checked locking(双重锁/双重校验锁)
优点：线程安全，延迟加载，效率较高
singleton 采用 volatile 关键字修饰也是很有必要的， singleton = new Singleton_DCL(); 

这段代码其实是分为三步执行：

1. 为 singleton 分配内存空间

2. 初始化 singleton

3. 将 singleton 指向分配的内存地址

   >  但是由于 JVM 具有指令重排的特性，执行顺序有可能变成 1->3->2。指令重排在单线程环境下不会出先问题，但是在多线程环境下会导致一个线程获得还没有初始化的实例。例如，线程 T1 执行了 1 和 3，此时 T2 调用getUniqueInstance() 后发现 uniqueInstance 不为空，因此返回 uniqueInstance，但此时 uniqueInstance 还未被初始化。使用 volatile 可以禁止 JVM 的指令重排，保证在多线程环境下也能正常运行。

 1.传统的例子

非常经典的例子，基本上对java有了解的同学都可以写出来，我们的例子，可能存在一个BUG，这个BUG的原因是，JMM出于对效率的考虑，是在happens-before原则内（out-of-order）乱序执行.

```java
public class LazySingleton {
    private int id;
    private static LazySingleton instance;
    private LazySingleton() {
        this.id= new Random().nextInt(200)+1;                 // (1)
    }
    public static LazySingleton getInstance() {
        if (instance == null) {                               // (2)
            synchronized(LazySingleton.class) {               // (3)
                if (instance == null) {                       // (4)
                    instance = new LazySingleton();           // (5)
                }
            }
        }
        return instance;                                      // (6)
    }
    public int getId() {
        return id;                                            // (7)
    }
}
```

  2. 简单的原理性介绍。

我们初始化一个类，会产生多条汇编指令，然而总结下来，是执行下面三个事情：

1.给LazySingleton 的实例分配内存。
 2.初始化LazySingleton 的构造器
 3.将instance对象指向分配的内存空间（注意到这步instance就非null了）

Java编译器允许处理器乱序执行（out-of-order），我们有可能是1->2->3也有可能是1->3->2。即我们有可能在先返回instance实例，然后执行构造方法。

即：double-check-locking可能存在线程拿到一个没有执行构造方法的对象。

 3.一个简单可能出错的执行顺序。

线程A、B执行getInstance().getId()

在某一时刻，线程A执行到(5)，并且初始化顺序为：1->3->2，当执行完将instance对象指向分配空间时。此时线程B执行(1)，发现instance!=null，继续执行，最后调用getId()返回0。此时切换到线程B对构造方法初始化。

  4. 解决方案

*  方案一：

利用类第一次使用才加载，加载时同步的特性。
 优点是：官方推荐，可以保证实现懒汉模式，代码少。
 缺点是：第一次加载比较慢，而且多了一个类多了一个文件，总觉得不爽。

```java
public class SingletonKerriganF {     
      
    private static class SingletonHolder {     
        static final SingletonKerriganF INSTANCE = new SingletonKerriganF();     
    }     
      
    public static SingletonKerriganF getInstance() {     
        return SingletonHolder.INSTANCE;     
    }     
}    
```

*  方案二：利用volatile关键字

volatile禁止了指令重排序，所以确保了初始化顺序一定是1->2->3，所以也就不存在拿到未初始化的对象引用的情况。
 优点：保持了DCL，比较简单
 确定：volatile这个关键字多少会带来一些性能影响吧。

**volatile只能用来保证该变量的可见性（不是立即的，即get的全都需要到主存中调取）和禁止指令重排序优化，即getstatic值到栈顶的时候他是正确的，从主存中拿到了，但是在工作内存（CPU、缓冲区中）中，因为在putstatic前需要进行别的机器指令，别的线程在别的工作区中可能在此时执行了多次get、put操作，这时put的是过时的值。**

**除非保证原子性（多线程时不需要依靠当前值/不用参与其他的改变），不然仍需要加锁来保证多线程安全**

**因为实现了内存模型，才能用volatile**

```java
public class Singleton(){  
    private volatile static Singleton singleton;  
    private Sington(){};  
    public static Singleton getInstance(){  
        if(singleton == null){  
            synchronized (Singleton.class){  
                if(singleton == null){  
                     singleton = new Singleton();    
                }  
            }
        }           
        return singleton;  
    }  
}  
```

*  方案三：初始化完后赋值。

通过一个temp，来确定初始化结束后其他线程才能获得引用。
 同时注意，JIT可能对这一部分优化，我们必须阻止JTL这部分的"优化"。

缺点是有点难理解，优点是：可以不用volatile关键字，又可以用DLC，岂不妙哉。

```java
public class Singleton {    
    
    private static Singleton singleton; // 这类没有volatile关键字    
    
    private Singleton() {    
    }    
    
    public static Singleton getInstance() {    
        // 双重检查加锁    
        if (singleton == null) {    
            synchronized (Singleton.class) {    
                // 延迟实例化,需要时才创建    
                if (singleton == null) {    
                        
                    Singleton temp = null;  
                    try {  
                        temp = new Singleton();    
                    } catch (Exception e) {  
                    }  
                    if (temp != null)    //为什么要做这个看似无用的操作，因为这一步是为了让虚拟机执行到这一步的时会才对singleton赋值，虚拟机执行到这里的时候，必然已经完成类实例的初始化。所以这种写法的DCL是安全的。由于try的存在，虚拟机无法优化temp是否为null  
                        singleton = temp; 
                }    
            }    
        }    
        return singleton;    
    }  
}  
```

##### 使用静态内置类实现单例模式

volatile关键字就可以从语义上解决这个问题，值得关注的是volatile的禁止指令重排序优化功能在Java 1.5后才得以实现，因此1.5前的版本仍然是不安全的，即使使用了volatile关键字。或许我们可以利用静态内部类来实现更安全的机制，静态内部类单例模式如下：

DCL解决了多线程并发下的线程安全问题，其实使用其他方式也可以达到同样的效果，代码实现如下：

```java
package org.mlinge.s06;
 
public class MySingleton {
	
	//内部类
	private static class MySingletonHandler{
		private static MySingleton instance = new MySingleton();
	} 
	
	private MySingleton(){}
	 
	public static MySingleton getInstance() { 
		return MySingletonHandler.instance;
	}
}
```



##### 枚举

```java
public enum Singleton {  
    INSTANCE;   
}

```

优点：它不仅能避免多线程同步问题，而且还自动支持序列化机制，防止反序列化重新创建新的对象，绝对防止多次实例化。
缺点：当想实例化一个单例类的时候，必须要记住使用相应的获取对象的方法，而不是使用new，可能会给其他开发人员造成困扰，特别是看不到源码的时候。（在实际工作中，很少使用）

> 就算你没有用到过其他的设计模式，但是单例模式你肯定接触过，比如，Spring 中 bean 默认就是单例模式的，所有用到这个 bean 的实例其实都是同一个。

# Synchronized优化：

  在JDK 1.5中，synchronized的性能是很低的，因为这是一个重量级的操作，它对性能最大的影响是阻塞的实现、挂起线程、和恢复线程都需要转入内核态完成，这些给操作系统的并发性带来了很大的压力。
  但是在JDK1.6，发生了变化，对synchronized加入了很多优化措施，
  之前我们已经对synchronized有一定的了解，它最大的特征就是在同一时刻只有一个线程能够获得对象的监视器（monitor），从而2进入到同步代码块或者同步方法中，即表现为互斥性（排它性）。这种方式效率低下，每次只能通过一个线程，既然每次都只能通过一个，如果这种形式不能改变的话，那么我们能不能让每次通过的速度变快一点呢?
  比如:去收银台付款，之前的方式是大家都去排队，然后取纸币付款收银员找零，付款的时候需要在包里拿出钱包再拿出钱，这个过程是比较耗时的，然后支付宝解放了大家去钱包找钱的过程，现在只需要扫描二维码就可以完成付款，也省去了收银员找零的时间，同样是需要排队付款，但整个付款的时间大大缩短，整体效率也变快了。这种优化同样可以引申到锁优化上，缩短获取锁的时间。
在锁优化之前，需要关注两个知识点（1）CAS操作 （2）Java对象头



# 一、理解储备-Java对象头和Monitor

## 1、java对象

　　在JVM中，实例对象在内存中的布局分为三块区域：对象头、实例变量和填充数据。如下：

　　　　![img](https://img2018.cnblogs.com/blog/292888/201903/292888-20190319175938865-743362758.png)

　　实例变量：存放类的属性数据信息，包括父类的属性信息，如果是数组的实例部分还包括数组的长度，这部分内存按**4字节对齐**。其实就是在java代码中能看到的属性和他们的值。 

　　填充数据：由于虚拟机要求对象起始地址必须是8字节的整数倍。填充数据不是必须存在的，仅仅是为了字节对齐，这点了解即可。

　　**对象头：**Hotspot虚拟机的对象头主要包括两部分数据：Mark Word（标记字段）、Klass Pointer（类型指针）、Array length（数组长度，只有数组类型才有）。

　　　　其中Klass Point【Class Metadata Address 】是对象指向它的类元数据的指针，虚拟机通过这个指针来确定这个对象是哪个类的实例，Mark Word用于存储对象自身的运行时数据，它是实现轻量级锁和偏向锁的关键。

### 1.1、对象头-Mark Word（标记字段）

　　Mark Word记录了对象和锁有关的信息，当这个对象被synchronized关键字当成同步锁时，围绕这个锁的一系列操作都和Mark Word有关。

　　Mark Word在32位JVM中的长度是32bit，在64位JVM中长度是64bit。

　　Mark Word在不同的锁状态下存储的内容不同，在32位JVM中是：

![image-20200924210402204](F:\Typora数据储存\高级语言\JAVA\JAVA并发.assets\image-20200924210402204.png)

锁的级别从低到高：无锁、偏向锁、轻量级锁、重量级锁。

　　其中无锁和偏向锁的锁标志位都是01，只是在前面的1bit区分了这是无锁状态还是偏向锁状态。

　　JDK1.6以后的版本在处理同步锁时存在锁升级的概念，1.6以前默认不开启偏向锁，1.6之后JVM对于同步锁的处理是从偏向锁开始的，随着竞争越来越激烈，处理方式从偏向锁升级到轻量级锁，最终升级到重量级锁。

　　JVM一般是这样使用锁和Mark Word的：

　　　　1，当没有被当成锁时，这就是一个普通的对象，Mark Word记录对象的HashCode，锁标志位是01，是否偏向锁那一位是0。

　　　　2，当对象被当做同步锁并有一个线程A抢到了锁时，锁标志位还是01，但是否偏向锁那一位改成1，前23bit记录抢到锁的线程id，表示进入偏向锁状态。

　　　　3，当线程A再次试图来获得锁时，JVM发现同步锁对象的标志位是01，是否偏向锁是1，也就是偏向状态，Mark Word中记录的线程id就是线程A自己的id，表示线程A已经获得了这个偏向锁，可以执行同步锁的代码。 

　　　　4，当线程B试图获得这个锁时，JVM发现同步锁处于偏向状态，但是Mark Word中的线程id记录的不是B，那么线程B会先用CAS操作试图获得锁，这里的获得锁操作是有可能成功的，因为线程A一般不会自动释放偏向锁。如果抢锁成功，就把Mark Word里的线程id改为线程B的id，代表线程B获得了这个偏向锁，可以执行同步锁代码。如果抢锁失败，则继续执行步骤5。

　　　　5，偏向锁状态抢锁失败，代表当前锁有一定的竞争，偏向锁将升级为轻量级锁。JVM会在当前线程的线程栈中开辟一块单独的空间，里面保存指向对象锁Mark Word的指针，同时在对象锁Mark Word中保存指向这片空间的指针。上述两个保存操作都是CAS操作，如果保存成功，代表线程抢到了同步锁，就把Mark Word中的锁标志位改成00，可以执行同步锁代码。如果保存失败，表示抢锁失败，竞争太激烈，继续执行步骤6。

　　　　6，轻量级锁抢锁失败，JVM会使用自旋锁，自旋锁不是一个锁状态，只是代表不断的重试，尝试抢锁。从JDK1.7开始，自旋锁默认启用，自旋次数由JVM决定。如果抢锁成功则执行同步锁代码，如果失败则继续执行步骤7。

　　　　7，自旋锁重试之后如果抢锁依然失败，同步锁会升级至重量级锁，锁标志位改为10。在这个状态下，未抢到锁的线程都会被阻塞。

### 1.2、对象头-指向类的指针

　　该指针在32位JVM中的长度是32bit，在64位JVM中长度是64bit。

　　Java对象的类数据保存在方法区。

### 1.3、对象头-数组长度

　　只有数组对象保存了这部分数据。

　　该数据在32位和64位JVM中长度都是32bit。

## 2、**Monior**

**提供了对信号量互斥和调度能力的更高级别的抽象，可以用信号量来实现**　　

Monitor可以理解为一种同步工具，也可理解为一种同步机制，常常被描述为一个Java对象。

　　　　1）互斥：一个Monitor在一个时刻只能被一个线程持有，即Monitor中的所有方法都是互斥的。

　　　　2）signal机制：如果条件变量不满足，允许一个正在持有Monitor的线程暂时释放持有权，当条件变量满足时，当前线程可以唤醒正在等待该条件变量的线程，然后重新获取Monitor的持有权。

　　所有的Java对象是天生的Monitor，每一个Java对象都有成为Monitor的潜质，因为在Java的设计中 ，每一个Java对象自打娘胎里出来就带了一把看不见的锁，它叫做内部锁或者Monitor锁。

　　Monitor的本质是依赖于底层操作系统的Mutex Lock实现，操作系统实现线程之间的切换需要从用户态到内核态的转换，成本非常高。

　　Monitor 是线程私有的数据结构，每一个线程都有一个可用monitor record列表，同时还有一个全局的可用列表。每一个被锁住的对象都会和一个monitor关联（对象头的MarkWord中的LockWord指向monitor的起始地址），同时monitor中有一个Owner字段存放拥有该锁的线程的唯一标识，表示该锁被这个线程占用。其结构如下：

​																					　　　　![img](https://img2018.cnblogs.com/blog/292888/201903/292888-20190319182105108-502408702.png)

　　1.Owner字段：初始时为NULL表示当前没有任何线程拥有该monitor record，当线程成功拥有该锁后保存线程唯一标识，当锁被释放时又设置为NULL
　　2.EntryQ字段：关联一个系统互斥锁（semaphore），阻塞所有试图锁住monitor record失败的线程
　　3.RcThis字段：表示blocked或waiting在该monitor record上的所有线程的个数
　　4.Nest字段：用来实现重入锁的计数
　　5.HashCode字段：保存从对象头拷贝过来的HashCode值（可能还包含GC age）
　　6.Candidate字段：用来避免不必要的阻塞或等待线程唤醒，因为每一次只有一个线程能够成功拥有锁，如果每次前一个释放锁的线程唤醒所有正在阻塞或等待的线程，会引起不必要的上下文切换（从阻塞到就绪然后因为竞争锁失败又被阻塞）从而导致性能严重下降；Candidate只有两种可能的值0表示没有需要唤醒的线程1表示要唤醒一个继任线程来竞争锁

### 2.1、Monitor具体实现方式

　　1.Monitor是在jvm底层实现的，底层代码是c++
　　2.Monitor的enter方法：获取锁
　　3.Monitor的exit方法：释放锁
　　4.Monitor的wait方法：为java的Object的wait方法提供支持
　　5.Monitor的notify方法：为java的Object的notify方法提供支持
　　6.Monitor的notifyAll方法：为java的Object的notifyAll方法提供支持

### 2.2、Monitor机制

　　![img](https://img2018.cnblogs.com/blog/292888/201903/292888-20190319182711834-1761626215.png)

　　Monitor可以类比为一个特殊的房间，这个房间中有一些被保护的数据，Monitor保证每次只能有一个线程能进入这个房间进行访问被保护的数据，进入房间即为持有Monitor，退出房间即为释放Monitor。

　　当一个线程需要访问受保护的数据（即需要获取对象的Monitor）时，它会首先在entry-set入口队列中排队（这里并不是真正的按照排队顺序），如果没有其他线程正在持有对象的Monitor，那么它会和entry-set队列和wait-set队列中的被唤醒的其他线程进行竞争（即通过CPU调度），选出一个线程来获取对象的Monitor，执行受保护的代码段，执行完毕后释放Monitor，如果已经有线程持有对象的Monitor，那么需要等待其释放Monitor后再进行竞争。

　　再说一下wait-set队列。当一个线程拥有Monitor后，经过某些条件的判断（比如用户取钱发现账户没钱），这个时候需要调用Object的wait方法，线程就释放了Monitor，进入wait-set队列，等待Object的notify方法（比如用户向账户里面存钱）。当该对象调用了notify方法或者notifyAll方法后，wait-set中的线程就会被唤醒，然后在wait-set队列中被唤醒的线程和entry-set队列中的线程一起通过CPU调度来竞争对象的Monitor，最终只有一个线程能获取对象的Monitor。

注意：

- 当一个线程在wait-set中被唤醒后，并不一定会立刻获取Monitor，它需要和其他线程去竞争
- 如果一个线程是从wait-set队列中唤醒后，获取到的Monitor，它会去读取它自己保存的PC计数器中的地址，从它调用wait方法的地方开始执行。

## 3、Monitor与java对象以及线程是如何关联 

1.如果一个java对象被某个线程锁住，则该java对象的Mark Word字段中LockWord指向monitor的起始地址，即对象头会有指向重量级锁的

2.Monitor的Owner字段会存放拥有相关联对象锁的线程id

## 4、锁的内存语义

　　内存可见性：同步块的可见性是由“如果对一个变量执行lock操作，将会清空工作内存中此变量的值，在执行引擎使用这个变量前需要重新执行load或assign操作初始化变量的值”、“对一个变量执行unlock操作之前，必须先把此变量同步回主内存中（执行store和write操作）”这两条规则获得的。

　　操作原子性：持有同一个锁的两个同步块只能串行地进入

　　锁的内存语义：

　　　　当线程释放锁时，JMM会把该线程对应的本地内存中的共享变量刷新到主内存中

　　　　当线程获取锁时，JMM会把该线程对应的本地内存置为无效。从而使得被监视器保护的临界区代码必须从主内存中读取共享变量

　　锁释放和锁获取的内存语义：

　　　　线程A释放一个锁，实质上是线程A向接下来将要获取这个锁的某个线程发出了（线程A对共享变量所做修改的）消息。

　　　　线程B获取一个锁，实质上是线程B接收了之前某个线程发出的（在释放这个锁之前对共享变量所做修改的）消息。

　　　　线程A释放锁，随后线程B获取这个锁，这个过程实质上是线程A通过主内存向线程B发送消息

![img](https://img2018.cnblogs.com/blog/292888/201903/292888-20190320142945743-1995482233.png)

## 5、Mutex Lock　　

　　监视器锁（Monitor）本质是依赖于底层的操作系统的Mutex Lock（互斥锁）来实现的。每个对象都对应于一个可称为" 互斥锁" 的标记，这个标记用来保证在任一时刻，只能有一个线程访问该对象。

　　互斥锁：用于保护临界区，确保同一时间只有一个线程访问数据。对共享资源的访问，先对互斥量进行加锁，如果互斥量已经上锁，调用线程会阻塞，直到互斥量被解锁。在完成了对共享资源的访问后，要对互斥量进行解锁。

　　mutex的工作方式：

　　　　![img](https://img2018.cnblogs.com/blog/292888/201903/292888-20190320145245904-604667995.png)

　　1) 申请mutex

　　2) 如果成功，则持有该mutex

　　3) 如果失败，则进行spin自旋. spin的过程就是在线等待mutex, 不断发起mutex gets, 直到获得mutex或者达到spin_count限制为止

　　4) 依据工作模式的不同选择yiled还是sleep

　　5) 若达到sleep限制或者被主动唤醒或者完成yield, 则重复1)~4)步，直到获得为止

　　由于Java的线程是映射到操作系统的原生线程之上的，如果要阻塞或唤醒一条线程，都需要操作系统来帮忙完成，无论什么线程都需要交由操作系统，因此状态转换需要耗费很多的处理器时间。所以synchronized是Java语言中的一个重量级操作。在JDK1.6中，虚拟机进行了一些优化，譬如在通知操作系统阻塞线程之前加入一段自旋等待过程，避免频繁地切入到核心态中：

什么是上下文切换？上下文切换的时机？
CPU通过分配时间片来执行任务，当一个任务的时间片用完，就会切换到另一个任务。在切换之前会保存上一个任务的状态，当下次再切换到该任务，就会加载这个状态。
——任务从保存到再加载的过程就是一次上下文切换。

按导致上下文切换的因素划分，可将上下文切换分为两点：

自发性上下文切换
非自发性上下文切换
自发性上下文切换指线程由于自身因素导致的切出。

通过调用下列方法会导致自发性上下文切换：

```
Thread.sleep()
Object.wait()
Thread.yeild()
Thread.join()
LockSupport.park()
```

非自发性上下文切换指线程由于线程调度器的原因被迫切出。

```
发生下列情况可能导致非自发性上下文切换：
切出线程的时间片用完
有一个比切出线程优先级更高的线程需要被运行
虚拟机的垃圾回收动作
```
上下文切换的开销
上下文切换的开销包括直接开销和间接开销。

直接开销有如下几点：

```
操作系统保存恢复上下文所需的开销
线程调度器调度线程的开销
```

间接开销有如下几点：

```
处理器高速缓存重新加载的开销
上下文切换可能导致整个一级高速缓存中的内容被冲刷，即被写入到下一级高速缓存或主存
互斥锁与自旋锁
```

重量级锁需要通过操作系统自身的互斥量（mutex lock，也称为互斥锁）来实现，然而这种实现方式需要通过用户态与和核心态的切换来实现，但这个切换的过程会带来很大的性能开销。

```
申请锁时，从用户态进入内核态，申请到后从内核态返回用户态（两次切换）；没有申请到时阻塞睡眠在内核态。使用完资源后释放锁，从用户态进入内核态，唤醒阻塞等待锁的进程，返回用户态（又两次切换）；被唤醒进程在内核态申请到锁，返回用户态（可能其他申请锁的进程又要阻塞）。所以，使用一次锁，包括申请，持有到释放，当前进程要进行四次用户态与内核态的切换。同时，其他竞争锁的进程在这个过程中也要进行一次切换。
```

## 6、未解

用户态能否直接调用CPU,还是还需要内核级线程的支持，如何判断能调用的硬件，通过线程和不通过线程？

> 

用户态的系统调用和中断的关系？

> 

用户模式和内核模式是存在同一个进程中的，即内核区是所有用户进程共享的？

> 内核空间存放的是操作系统内核代码和数据，是被所有程序**共享**的
>
> **让内核拥有完全独立的地址空间，就是让内核处于一个独立的进程中，这样每次进行系统调用都需要切换进程。切换进程的消耗是巨大的，不仅需要寄存器进栈出栈，还会使CPU中的数据缓存失效、MMU中的页表缓存失效，这将导致内存的访问在一段时间内相当低效。**
>
> 而让内核和用户程序共享地址空间，发生系统调用时进行的是模式切换，模式切换仅仅需要寄存器进栈出栈，不会导致缓存失效；现代CPU也都提供了快速进出内核模式的指令，与进程切换比起来，效率大大提高了。

用户模式进程通过中断等进入内核模式调用内核区中的代码和数据是否会进行上下文的切换，线程从内核态到用户态是否也如此？

> 会，但是模式切换仅仅需要寄存器进栈出栈，不会导致缓存失效

通过内核线程的调度是如何作用到用户线程的？

> 

内核中的进程表和线程表具体存储的内容，用户态有用户线程单独的线程表还是与内核态中的线程表合并使用？

> 

线程和指令和CPU的具体关系

> 操作系统或者我们自己实现的对CPU的调度，是基于进程或线程为单位进行分配的，一般以线程为单位进行分配，减少上下文中需要切换的开销，线程只是一个使CPU能知道执行哪些方法的类似索引的内存块，操作系统或我们将线程编译的指令传入CPU，线程的方法调用便如普通类一样都是调用code中的字节码指令了。

1：1相较于1：n线程的切换到底是用户态切到内核态的的中断和上下文现场保存恢复等损耗大，还是切换到内核态后1：1中调切换内核线程的上下文保护恢复造成的额外损耗大，以上需要验证，用户态是否可以单独调用CPU还是还需要经过内核线程。

> 用户态到内核态本质上不过是CPU负责的进程的寄存器的模式位的改变而已，但是用户态到内核态需要中断等异常流的操作，内核态到用户态又需要设置标志位，频繁的切换消耗也不容小觑，内核态的内核线程上下文的切换和用户态的用户线程上下文的保护恢复等反而很多都相似。



自旋锁与互斥锁不同的是自旋锁不会引起调用者睡眠。如果自旋锁已经被别的进程保持，调用者就轮询（不断的消耗CPU的时间）是否该自旋锁的保持者已经释放了锁（"自旋"一词就是因此而得名）。

为什么线程切换会导致用户态与内核台的切换？
现在来回答就很简单了：
因为线程的调度是在内核态运行的，而线程中的代码是在用户态运行。若是使用用户线程，也避免不了上下文现场的保护和恢复、调度，但是可以使得程序员自己实现相关的功能，操作系统只会从系统安全的角度出发进行处理。

# 二、 JDK1.6对synchronized锁的优化实现

　　synchronized用的锁是存在Java对象头里的。

　　synchronized锁的状态总共有四种，无锁状态、偏向锁【jdk6之后】、轻量级锁【jdk6之后】和重量级锁【jdk6之前的synchronized实现】。随着锁的竞争，锁可以从偏向锁升级到轻量级锁，再升级的重量级锁，但是锁的升级是单向的，也就是说只能从低到高升级，不会出现锁的降级，关于重量级锁，前面我们已详细分析过，下面我们将介绍偏向锁和轻量级锁以及JVM的其他优化手段。

源码详解：

https://github.com/farmerjohngit/myblog/issues/13

https://github.com/farmerjohngit/myblog/issues/13

https://github.com/farmerjohngit/myblog/issues/13

## 1、CAS操作

**什么是CAS？**
  基于冲突检测的乐观并发策略通俗地说就是不管风险，先进行操作，如果没有其他线程争用共享数据，那操作就直接成功了；如果共享的数据的确被争用，**产生了冲突，那再进行其他的补偿措施，最常用的补偿措施是不断地重试**，直到出现没有竞争的共享数据为止。
  
**CAS的操作过程**
CAS可以通俗的理解为CAS（V,O,N）其中包含三个值分别是
	V：内存地址中实际存放的值；
	O：预期的值；
	N：更新后的值。

> 当V==O相同时，也就是说预期值和内存中实际的值相同，表明该值没有被其他线程更改过，即预期值O就是目前来说最新的值了，可以将N赋给V。
> 当V!=O不相同，表明V值已经被其他线程改过了，即预期值0不是最新值了，所以不能将新值N赋给V，返回V值，将O值改为V。

当多个线程使用CAS操作一个变量时，只有一个线程会成功并成功更新，其余会失败，失败的线程会重新尝试（自旋），当然也可以选择挂起线程（阻塞）。未优化的synchronized最主要的问题是：在存在线程竞争的情况下会出现线程阻塞和唤醒锁带来的性能问题，这是一种互斥同步（阻塞同步），而CAS并不是武断的将线程挂起，当CAS操作失败后会进行一定的尝试，而非进行耗时的挂起唤醒的操作，因此也叫做非阻塞同步。

1.1.2 CAS带来的问题
1.1.2.1 CAS带来的ABA问题
  线程1准备用CAS将变量的值由A替换为B，在此之前，线程2将变量的值由A替换为C，又由C替换为A，然后线程1执行CAS时发现变量的值仍然为A，所以CAS成功。但实际上这时的现场已经和最初不同了。
  例如一个单向链表：

![img](https://img-blog.csdnimg.cn/20181123163612653.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2ExNTkyOTc0ODUwMg==,size_16,color_FFFFFF,t_70)

解决方案：
  atomic包中提供了AtomicStampedReference来解决ABA问题相当于添加了版本号

 **大量的自旋会浪费处理器资源**
  大量的自旋会使得处理器一直忙于处理CAS指令，造成处理器资源的浪费，在一定量自旋后，应该进行锁的升级甚至挂起线程，减少CPU资源的浪费

 **CAS带来的公平性问题**

自旋状态还带来另外一个副作用，不公平的锁机制。处于阻塞状态的线程，无法立刻竞争被释放的锁。然而处于自旋状态的线程，则很有可能优先获得这把锁。

## 2、自旋锁向自适应自旋

　　引入自旋锁的原因：互斥同步对性能最大的影响是阻塞的实现，因为挂起线程和恢复线程的操作都需要转入内核态中完成，这些操作给系统的并发性能带来很大的压力。同时虚拟机的开发团队也注意到在许多应用上面，共享数据的锁定状态只会持续很短一段时间，为了这一段很短的时间频繁地阻塞和唤醒线程是非常不值得的。

**自旋锁**

　　==让该线程执行一段无意义的忙循环（自旋）等待一段时间，不会被立即挂起（自旋不放弃处理器额执行时间），看持有锁的线程是否会很快释放锁。自旋锁在JDK 1.4.2中引入，默认关闭，但是可以使用-XX:+UseSpinning开开启；在JDK1.6中默认开启。==

**自旋锁的缺点**　　

　　自旋等待不能替代阻塞，虽然它可以避免线程切换带来的开销，但是它占用了处理器的时间。如果持有锁的线程很快就释放了锁，那么自旋的效率就非常好；反之，自旋的线程就会白白消耗掉处理器的资源，它不会做任何有意义的工作，这样反而会带来性能上的浪费。所以说，自旋等待的时间（自旋的次数）必须要有一个限度，例如让其循环10次，如果自旋超过了定义的时间仍然没有获取到锁，则应该被挂起（进入阻塞状态）。通过参数-XX:PreBlockSpin可以调整自旋次数，默认的自旋次数为10。

**自适应的自旋锁**【**Adaptive Spinning**】　　

　　JDK1.6引入自适应的自旋锁，自适应就意味着自旋的次数不再是固定的，它是由前一次在同一个锁上的自旋时间及锁的拥有者的状态来决定：如果在同一个锁的对象上，自旋等待刚刚成功获得过锁，并且持有锁的线程正在运行中，那么虚拟机就会认为这次自旋也很有可能再次成功，进而它将允许自旋等待持续相对更长的时间。如果对于某个锁，自旋很少成功获得过，那在以后要获取这个锁时将可能省略掉自旋过程，以避免浪费处理器资源。简单来说，就是线程如果自旋成功了，则下次自旋的次数会更多，如果自旋失败了，则自旋的次数就会减少。

　　从轻量级锁获取的流程中我们知道**，**当线程在获取轻量级锁的过程中执行CAS操作失败时，是要通过自旋来获取重量级锁的。问题在于，自旋是需要消耗CPU的，如果一直获取不到锁的话，那该线程就一直处在自旋状态，白白浪费CPU资源。解决这个问题最简单的办法就是指定自旋的次数，例如让其循环10次，如果还没获取到锁就进入阻塞状态。但是JDK采用了更聪明的方式——适应性自旋，简单来说就是线程如果自旋成功了，则下次自旋的次数会更多，如果自旋失败了，则自旋的次数就会减少。

自旋锁使用场景

　　当线程在获取轻量级锁的过程中执行CAS操作失败时，是要通过自旋来获取重量级锁的。轻量级锁失败后，虚拟机为了避免线程真实地在操作系统层面挂起，还会进行一项称为自旋锁的优化手段。这是基于在大多数情况下，线程持有锁的时间都不会太长，如果直接挂起操作系统层面的线程可能会得不偿失，毕竟操作系统实现线程之间的切换时需要从用户态转换到核心态，这个状态之间的转换需要相对比较长的时间，时间成本相对较高，因此自旋锁会假设在不久将来，当前的线程可以获得锁，因此虚拟机会让当前想要获取锁的线程做几个空循环(这也是称为自旋的原因)，一般不会太久，可能是50个循环或100循环，在经过若干次循环后，如果得到锁，就顺利进入临界区。如果还不能获得锁，那就会将线程在操作系统层面挂起，这就是自旋锁的优化方式，这种方式确实也是可以提升效率的。最后没办法也就只能升级为重量级锁了。

## 3、偏向锁

　　偏向锁是Java 6之后加入的新锁，它是一种针对加锁操作的优化手段，经过研究发现，在大多数情况下，锁不仅不存在多线程竞争，而且总是由同一线程多次获得，因此为了减少同一线程获取锁(会涉及到一些CAS操作,耗时)的代价而引入偏向锁。==即适合很长时间只有一个线程获取该锁，到线程结束/自己挂起后才会有别的线程来使用该对象的情况==

　　偏向锁的核心思想是，如果一个线程获得了锁，那么锁就进入偏向模式，此时Mark Word 的结构也变为偏向锁结构，当这个线程再次请求锁时，无需再做任何同步操作，即获取锁的过程，这样就省去了大量有关锁申请的操作，从而也就提供程序的性能。

　　优化点：在没有多线程竞争的情况下，==减少轻量级锁的不必要的CAS操作。在无竞争情况下，完全消除同步。==

　　优化方法：锁对象的Mark Word中记录获取锁的线程ID。

**偏向锁对象头的Mark Word：**

偏向锁	| 线程ID  |	Epoch	| 对象分代年龄	 | 1	 | 01

#### 3.1 偏向锁的获取

相应源码：bytecodeInterpreter.cpp

偏向锁的获取过程：
  获取锁步骤：

　　　　1）判断锁对象是否是偏向锁（即锁标志位为01，偏向锁位为1），若为偏向锁状态执行2。

　　　　2）判断锁对象的线程ID是否为当前线程的ID，如果是则说明已经获取到锁，执行代码块；否则执行3。

　　　　3）当前线程使用CAS更新锁对象的线程ID为当前线程ID。如果成功，获取到锁；否则执行4

　　　　4）当到达全局安全点，当前线程根据Mark Word中的线程ID通知持有锁的线程挂起，将锁对象Mark Word中的锁对象指针指向当前堆栈中最近的一个锁记录，偏向锁升级为轻量级锁，恢复被挂起的线程。



![在这里插入图片描述](https://img-blog.csdnimg.cn/20190218141613110.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3poYW9fbWlhbw==,size_16,color_FFFFFF,t_70)

#### 3.2 偏向锁的撤销

[InterpreterRuntime::monitorenter](http://hg.openjdk.java.net/jdk8u/jdk8u/hotspot/file/9ce27f0a4683/src/share/vm/interpreter/interpreterRuntime.cpp#l608)方法

**偏向锁使用了一种等到竞争出现才释放锁的机制，所以当其他线程尝试竞争偏向锁时，持有偏向锁的线程才会释放锁。**

撤销锁步骤：（撤销偏向线程/撤销为无锁/撤销为轻量级锁）（也会用到Lock Record）

　　　　偏向锁采用一种等到竞争出现时才释放锁的机制。当其他线程尝试竞争偏向锁时，当前线程才会释放释放锁，==线程不会主动去释放偏向锁==。偏向锁的撤销需要等待全局安全点。

​			   在`BiasedLocking::revoke_and_rebias`方法判断epoch等的状态，需要先依据占用线程的状态判断是否撤销，若不撤销可重偏向为匿名偏向模式（未偏向），若撤销需要看调用的方法的类型，看仅是更改为无锁状态还是升级为轻量级锁。

　　　　1） 如果偏向的线程已经不存活了，允许重偏向则将对象mark word设置为匿名偏向状态，否则设置为无锁状态

　　　　2）若偏向的线程还存活，则遍历线程栈中所有的Lock Record，如果能找到对应的Lock Record说明偏向的线程还在执行同步代码块中的代码， 需要升级为轻量级锁，直接修改偏向线程栈中的Lock Record。且修改第一个Lock Record为无锁状态，然后将obj的mark word设置为指向该Lock Record的指针

​				3）若没有相应的Lock Record，说明偏向线程已经不在同步块中了，允许重偏向则将对象mark word设置为匿名偏向状态，否则设置为无锁状态

偏向锁的撤销过程：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20190218141410280.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3poYW9fbWlhbw==,size_16,color_FFFFFF,t_70)

　　

#### 3.3关闭偏向锁：

　　　　偏向锁在Java 6和Java 7里是默认启用的。由于偏向锁是为了在只有一个线程执行同步块时提高性能，如果你确定应用程序里所有的锁通常情况下处于竞争状态，可以通过JVM参数关闭偏向锁：-XX:-UseBiasedLocking=false，那么程序默认会进入轻量级锁状态。

## 4、轻量级锁

　　倘若偏向锁失败，虚拟机并不会立即升级为重量级锁，它还会尝试使用一种称为轻量级锁的优化手段(1.6之后加入的)，此时Mark Word 的结构也变为轻量级锁的结构。轻量级锁能够提升程序性能的依据是“==对绝大部分的锁，在整个同步周期内都不存在竞争==”，注意这是经验数据。需要了解的是，==轻量级锁所适应的场景是线程交替执行同步块的场合==，如果存在同一时间访问同一锁的场合，竞争失败的在一定自旋尝试后仍未获得，就会导致轻量级锁膨胀为重量级锁。==此时的膨胀的意思是告诉线程有别的线程已经不自旋而阻塞放入了moniter列表的RcThis中了，需要去唤醒他们。==

　　优化点：在没有多线程竞争的情况下，==通过CAS减少重量级锁使用操作系统互斥量产生的性能消耗==。

　　什么情况下使用：关闭偏向锁或由于多线程竞争导致的偏向锁升级为轻量级锁。

**轻量级锁的Mark Word：**

轻量级锁 |	指向栈中锁记录的指针	|  00

#### 4.1 轻量级锁的获取

　　　　1）判断是否处于无锁状态，若是，则JVM在当前线程的栈帧中创建锁记录（Lock ）空间，用于存放锁对象中的Mark Word的拷贝，接着执行步骤2；否则执行步骤3。

> 虚拟机首先将在当前线程的栈帧中建立一个名为锁记录（Lock Record）的空间，用于存储锁对象目前的Mark Word的拷贝（官方为这份拷贝加了一个Displaced前缀，即Displaced Mark Word），这时候线程堆栈与对象头的状态如图:
>
> ![image-20200925202937597](F:\Typora数据储存\高级语言\JAVA\JAVA并发.assets\image-20200925202937597.png)

　　　　2）当前线程尝试利用CAS将锁对象的Mark word更新为指向Lock Record的指针。如果更新成功意味着获取到锁，将锁标志位置为00，执行同步代码块；如果更新失败，执行步骤3，**查看有无竞争到锁**。

　　　　3）判断锁对象的Mark word否指向当前线程的栈帧，若是说明当前线程已经获取了锁，执行同步代码，否则说明其他线程已经获取了该锁对象，执行步骤4。

　　　　4）当前线程尝试使用自旋来获取锁，自旋期间会不断的执行步骤1），直到获取到锁或自旋结束。因为自旋锁会消耗CPU，所限的自旋。如果自旋期间获取到锁（其他线程释放锁），执行同步块；否则锁膨胀为重量级锁，==更换当前对象头的标志位（不能此时就更换为monitor指针，在该线程未释放锁前再次调用该对象验证时，若已经被更改为重量级锁，则其第三步会一直失败，进入死锁，不知道有没有实现是获得锁后的对象调用不需要再进行锁校验的）==，当前线程阻塞，等待持有锁的线程释放醒。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20190217203239663.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3poYW9fbWlhbw==,size_16,color_FFFFFF,t_70)

#### 4.2 释放锁步骤：

　　　　1）从当前线程的栈帧中取出Displaced Mark Word存储的锁记录的内容。

　　　　2）当前线程尝试使用CAS将锁记录内容更新到锁对象中的Mark 。如果更新成功，则释放锁成功，将锁标志位置为01无锁状态；否则，执行3。

　　　　3）CAS更新失败，说明有其他线程尝试获取锁，==并且已经将对象头Mark Word中的锁记录的指针换成了指向重量级锁的指针==。需要释放锁并同时唤醒等待的线程。

## 5、重量级锁

　　**重量级锁基于Monitor实现，成本高。也就是synchronized在jdk1.6之前的实现。**

　　如果存在同一时间访问同一锁的情况，就会导致轻量级锁膨胀为重量级锁。Mark Word的锁标记位更新为10，Mark Word指向互斥量（重量级锁）

　　Synchronized的重量级锁是通过对象内部的一个叫做监视器锁（monitor）来实现的【即文章第一部分说明的实现】，监视器锁本质又是依赖于底层的操作系统的Mutex Lock（互斥锁）来实现的。而操作系统实现线程之间的切换需要从用户态转换到核心态，这个成本非常高，状态之间的转换需要相对比较长的时间，这就是为什么Synchronized在jdk1.6之前效率低的原因，不被推荐的原因，但是在jdk1.6之后，推荐优先使用，毕竟使用ReentrantLock还需主动加锁，关锁操作。

## 6、锁粗化（Lock Coarsening）

锁粗化的概念应该比较好理解，就是将多次连接在一起的加锁、解锁操作合并为一次，将多个连续的锁扩展成一个范围更大的锁。

```java
public class Test{
    //sb为全局变量，全局变量有线程安全问题
    public static StringBuffer sb = new StringBuffer();
    public static void main(String[] args) {
        sb.append("a");
        sb.append("b");
        sb.append("c");
    }
}
```

　　这里每次调用stringBuffer.append方法都需要加锁和解锁，如果虚拟机检测到有一系列连串的对同一个对象加锁和解锁操作，就会将其合并成一次范围更大的加锁和解锁操作，即在第一次append方法时进行加锁，最后一次append方法结束后进行解锁。

## 7、 锁消除（Lock Elimination）

　　消除锁是虚拟机另外一种锁的优化，这种优化更彻底，Java虚拟机在JIT编译时(可以简单理解为当某段代码即将第一次被执行时进行编译，又称即时编译)，通过对运行上下文的扫描，去除不可能存在共享资源竞争的锁，通过这种方式消除没有必要的锁，可以节省毫无意义的请求锁时间，如下StringBuffer的append是一个同步方法，但是在add方法中的StringBuffer属于一个局部变量，并且不会被其他线程所使用，因此StringBuffer不可能存在共享资源竞争的情景，所以其实这过程是线程安全的，JVM会自动将其锁消除。

```java
public class Test{
    public static void main(String[] args) {
        //sb变量是局部变量，不会有线程安全问题，加锁、解锁没有意义
        StringBuffer sb = new StringBuffer();
        sb.append("a");
        sb.append("b");
        sb.append("c");
    }
}
```

## 8、偏向锁、轻量级锁、重量级锁之间转换

　　可以结合上述，以及对象头，参看此图

　　![img](https://img2018.cnblogs.com/blog/292888/201903/292888-20190320093720616-1849176813.png)

细心的读者看到这里可能会发现一个问题：==当对象进入偏向状态的时候，Mark Word大部分的空间（23个比特）都用于存储持有锁的线程ID了，这部分空间占用了原有存储对象哈希码的位置，那原来对象的哈希码怎么办呢？==

在Java语言里面一个对象如果计算过哈希码，就应该一直保持该值不变（强烈推荐但不强制，因为用户可以重载hashCode()方法按自己的意愿返回哈希码），否则很多依赖对象哈希码的API都可能存在出错风险。而作为绝大多数对象哈希码来源的Object::hashCode()方法，返回的是对象的一致性哈希码（Identity Hash Code），这个值是能强制保证不变的，它通过在对象头中存储计算结果来保证第一次计算之后，再次调用该方法取到的哈希码值永远不会再发生改变。==因此，当一个对象已经计算过一致性哈希码后，它就再也无法进入偏向锁状态了；而当一个对象当前正处于偏向锁状态，又收到需要计算其一致性哈希码请求时，它的偏向状态会被立即撤销，并且锁会膨胀为重量级锁（较为耗时，容易发生竞争）。==在重量级锁的实现中，对象头指向了重量级锁的位置，代表重量级锁的ObjectMonitor类里有字段可以记录非加锁状态（标志位为“01”）下的Mark Word，其中自然可以存储原来的哈希码。 

==除了偏向锁，其他的都需要在栈中有位置存放对象的hash Code。==

## 9、几种锁之间对比

　　JDk中采用轻量级锁和偏向锁等对Synchronized的优化，但是这两种锁也不是完全没缺点的，比如竞争比较激烈的时候，不但无法提升效率，反而会降低效率，因为多了一个锁升级的过程，这个时候就需要通过-XX:-UseBiasedLocking来禁用偏向锁。

　　synchronized锁：由对象头中的Mark Word根据锁标志位的不同而被复用

| 锁       |                                                              | 优点                                                         | 缺点                                             | 适用场景                             |
| -------- | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------ | ------------------------------------ |
| 偏向锁   | Mark Word存储锁偏向的线程ID，以后该线程在进入和退出同步块时不需要进行CAS操作来加锁和解锁，只需简单比较ThreadID。特点：只有等到线程竞争出现才释放偏向锁，持有偏向锁的线程不会主动释放偏向锁。之后的线程竞争偏向锁，会先检查持有偏向锁的线程是否存活，如果不存在，则对象变为无锁状态，重新偏向；如果仍存活，则偏向锁升级为轻量级锁，此时轻量级锁由原持有偏向锁的线程持有，继续执行其同步代码，而正在竞争的线程会进入自旋等待获得该轻量级锁 | 加锁和解锁不需要额外的消耗，和执行非同步方法比仅存在纳秒级的差距。 | 如果线程间存在锁竞争，会带来额外的锁撤销的消耗。 | 适用于只有一个线程访问同步块场景。   |
| 轻量级锁 | 在当前线程的栈帧中建立一个名为锁记录（Lock Record）的空间，尝试拷贝锁对象目前的Mark Word到栈帧的Lock Record，若拷贝成功：虚拟机将使用CAS操作尝试将对象的Mark Word更新为指向Lock Record的指针，并将Lock record里的owner指针指向对象的Mark Word。若拷贝失败：若当前只有一个等待线程，则可通过自旋稍微等待一下，可能持有轻量级锁的线程很快就会释放锁。 但是当自旋超过一定的次数，或者一个线程在持有锁，一个在自旋，又有第三个来访时，轻量级锁膨胀为重量级锁 | 竞争的线程不会阻塞，提高了程序的响应速度。                   | 如果始终得不到锁竞争的线程使用自旋会消耗CPU。    | 追求响应时间。同步块执行速度非常快。 |
| 重量级锁 | 指向互斥量（mutex），底层通过操作系统的mutex lock实现。等待锁的线程会被阻塞，由于Linux下Java线程与操作系统内核态线程一一映射，所以涉及到用户态和内核态的切换、操作系统内核态中的线程的阻塞和恢复。 | 线程竞争不使用自旋，不会消耗CPU。                            | 线程阻塞，响应时间缓慢。                         | 追求吞吐量。同步块执行速度较长。     |



未压缩头

　　![img](https://img2018.cnblogs.com/blog/292888/201903/292888-20190323162633238-400700992.png)

压缩头

　　![img](https://img2018.cnblogs.com/blog/292888/201903/292888-20190323162709383-1281930752.png)

可以看到

- 开启指针压缩时，markword占用8bytes,类型指针占用8bytes，共占用16bytes;
- 未开启指针压缩时，markword占用8bytes,类型指针占用4bytes,但由于**java内存地址按照8bytes对齐,长度必须是8的倍数**，因此会从12bytes补全到16bytes;
- 数组长度为4bytes,同样会进行对齐，补足到8bytes;

另外从上面的截图可以看到，开启指针压缩之后，对象类型指针为0xf800c005,但实际的类型指针为0x7c0060028;那么指针是如何压缩的呢？实际上由于java地址一定是8的倍数，因此将0xf800c005*8即可得到实际的指针0x7c0060028,关于指针压缩的更多知识可参考[官方文档](https://link.jianshu.com/?t=http://docs.oracle.com/javase/7/docs/technotes/guides/vm/performance-enhancements-7.html#compressedOop)。



此处，有几点要注意:

- 如果对象没有重写hashcode方法，那么默认是调用**os::random**产生hashcode,可以通过System.identityHashCode获取；os::random产生hashcode的规则为:next_rand = (16807*seed) mod (2**31-1),因此可以使用31位存储;另外一旦生成了hashcode,JVM会将其记录在markword中；
- GC年龄采用4位bit存储，最大为15，例如MaxTenuringThreshold参数默认值就是15；
- 当处于轻量级锁、重量级锁时，记录的对象指针，根据JVM的说明，此时认为指针仍然是64位，最低两位假定为0;当处于偏向锁时，记录的为获得偏向锁的线程指针，该指针也是64位；

```c++
 We assume that stack/thread pointers have the lowest two bits cleared.
ObjectMonitor* monitor() const {
    assert(has_monitor(), "check");
    // Use xor instead of &~ to provide one extra tag-bit check.
    return (ObjectMonitor*) (value() ^ monitor_value);//monitor_value=2,value最右两位为10，因此异或之后最右两位为0
  }
JavaThread* biased_locker() const {
    assert(has_bias_pattern(), "should not call this otherwise");
    return (JavaThread*) ((intptr_t) (mask_bits(value(), ~(biased_lock_mask_in_place | age_mask_in_place | epoch_mask_in_place))));
//~(biased_lock_mask_in_place | age_mask_in_place | epoch_mask_in_place)为11111111111111111111110010000000，计算后的结果中，低10位全部为0;
  }
```

由于java中内存地址都是8的倍数，因此可以理解为最低3bit为0，因此假设轻量级和重量级锁的最低2位为0是成立的；但为什么偏向锁的最低10位都是0?查看markOop.hpp文件，发现有这么一句话:

```
// Alignment of JavaThread pointers encoded in object header required by biased locking
  enum { biased_lock_alignment    = 2 << (epoch_shift + epoch_bits)
//epoch_shift+epoch_bits＝10
  };
```

thread.hpp中重载了operator new:

```java
void* operator new(size_t size) { return allocate(size, true); }

// ======= Thread ========
// Support for forcing alignment of thread objects for biased locking
void* Thread::allocate(size_t size, bool throw_excpt, MEMFLAGS flags) {
  if (UseBiasedLocking) {
    const int alignment = markOopDesc::biased_lock_alignment;//10
    size_t aligned_size = size + (alignment - sizeof(intptr_t));
    void* real_malloc_addr = throw_excpt? AllocateHeap(aligned_size, flags, CURRENT_PC)
                                          : os::malloc(aligned_size, flags, CURRENT_PC);
    void* aligned_addr     = (void*) align_size_up((intptr_t) real_malloc_addr, alignment);
    assert(((uintptr_t) aligned_addr + (uintptr_t) size) <=
           ((uintptr_t) real_malloc_addr + (uintptr_t) aligned_size),
           "JavaThread alignment code overflowed allocated storage");
    if (TraceBiasedLocking) {
      if (aligned_addr != real_malloc_addr)
        tty->print_cr("Aligned thread " INTPTR_FORMAT " to " INTPTR_FORMAT,
                      real_malloc_addr, aligned_addr);
    }
    ((Thread*) aligned_addr)->_real_malloc_address = real_malloc_addr;
    return aligned_addr;
  } else {
    return throw_excpt? AllocateHeap(size, flags, CURRENT_PC)
                       : os::malloc(size, flags, CURRENT_PC);
  }
}
```

如果开启了偏移锁,在创建线程时，线程地址会进行对齐处理，保证低10位为0

更多请参看链接：https://www.jianshu.com/p/ec28e3a59e80 

## 10、**synchronize的可重入性**

　　从互斥锁的设计上来说，当一个线程试图操作一个由其他线程持有的对象锁的临界资源时，将会处于阻塞状态，但当一个线程再次请求自己持有对象锁的临界资源时，这种情况属于重入锁，请求将会成功，在java中synchronized是基于原子性的内部锁机制，是可重入的，因此在一个线程调用synchronized方法的同时在其方法体内部调用该对象另一个synchronized方法，也就是说一个线程得到一个对象锁后再次请求该对象锁，是允许的，这就是synchronized的可重入性。

　　synchronized拥有强制原子性的内部锁机制，是一个可重入锁。因此，在一个线程使用synchronized方法时调用该对象另一个synchronized方法，即一个线程得到一个对象锁后再次请求该对象锁，是**永远可以拿到锁的**。

　　在Java内部，同一个线程调用自己类中其他synchronized方法/块时不会阻碍该线程的执行，同一个线程对同一个对象锁是可重入的，同一个线程可以获取同一把锁多次，也就是可以多次重入。原因是Java中线程获得对象锁的操作是以线程为单位的，而不是以调用为单位的。

##### synchronized可重入锁的实现

　　每个锁关联一个线程持有者和一个计数器。当计数器为0时表示该锁没有被任何线程持有，那么任何线程都都可能获得该锁而调用相应方法。当一个线程请求成功后，JVM会记下持有锁的线程，并将计数器计为1。此时其他线程请求该锁，则必须等待。而该持有锁的线程如果再次请求这个锁，就可以再次拿到这个锁，同时计数器会递增。当线程退出一个synchronized方法/块时，计数器会递减，如果计数器为0则释放该锁。

注意：锁的持有者是线程，而且锁的是当前实例

```java
public class Student {
 
    public static void main(String[] args) {
        Student student = new Student();
        student.doA();
    }
 
 
    public synchronized void doA() {
        System.out.println("do a");
        doB();
    }
 
    public synchronized void doB() {
        System.out.println("do b");
    }
}
```

# AtomicInteger

AtomicInteger 内部使用 CAS 原子语义来处理加减等操作，CAS 全称Compare And Swap（比较与交换），通过判断内存某个位置的值是否与预期值相等，如果相等则进行值更新。CAS 是内部是通过 Unsafe 类实现，而 Unsafe类的方法都是 native 的，在 JNI 里是借助于一个 CPU 指令完成的，属于原子操作。
AtmoicInteger 缺点有：
(1)、循环开销大。如果 CAS 失败，会一直尝试。如果 CAS 长时间不成功，会给 CPU 带来很大的开销；
(2)、只能保证单个共享变量的原子操作，对于多个共享变量，CAS 无法保证；
(3)、存在 ABA 问题

> 常见于数据更新的场景
> （如数据库更新、缓存更新）。就 AtomicInteger 而言，CAS 需要在操作值的时候检查内存值是否发生变化，没有发生变化才会更新内存值。但是如果内存值原来是 A，后来变成了 B，然后又变成了 A，那么 CAS 进行检查时会发现值没有发生变化，但是实际上是有变化的。
>
> 解决方案：ABA 问题一般通过版本号的机制来解决。每次变量更新的时候，版本号加 1，这样只要变量被某一个线程修改过，该版本号就会发生递增操作。ABA场景下的变化过程就从 “A－B－A” 变成了 “1A－2B－3A”。
>
> JDK 从 1.5 开始提供了 AtomicStampedReference 类来解决 ABA 问题，具体操作封装在 compareAndSet () 中。compareAndSet () 首先检查当前引用和当前标志与预期引用和预期标志是否相等，如果都相等，则以原子方式将引用值和标志的值设置为给定的更新值。

# wait/notify/sleep/park

wait是指在一个已经进入了同步锁的线程内，让自己暂时让出同步锁，以便其他正在等待此锁的线程可以得到同步锁并运行，只有其他线程调用了notify方法（notify并不释放锁，只是告诉调用过wait方法的线程可以去参与获得锁的竞争了，但不是马上得到锁，因为锁还在别人手里，别人还没释放），调用wait方法的一个或多个线程就会解除wait状态，重新参与竞争对象锁，程序如果可以再次得到锁，就可以继续向下运行。

1）wait()、notify()和notifyAll()方法是本地方法，并且为final方法，无法被重写。

2）当前线程必须拥有此对象的monitor（即锁），才能调用某个对象的wait()方法能让当前线程阻塞（这种阻塞是通过提前释放synchronized锁，重新去请求锁导致的阻塞，这种请求必须有其他线程通过notify()或者notifyAll（）唤醒重新竞争获得锁）

3）调用某个对象的notify()方法能够唤醒一个正在等待这个对象的monitor的线程，如果有多个线程都在等待这个对象的monitor，则只能唤醒其中一个线程；

 （notify()或者notifyAll()方法并不是真正释放锁，必须等到synchronized方法或者语法块执行完才真正释放锁）

4）调用notifyAll()方法能够唤醒所有正在等待这个对象的monitor的线程，唤醒的线程获得锁的概率是随机的，取决于cpu调度

> wait和sleep的区别
>
> 1) 原理不同。sleep()方法是Thread类的静态方法，是线程用来控制自身流程的，他会使此线程暂停执行一段时间，而把执行机会让给其他线程，等到计时时间一到，此线程会自动苏醒。例如，当线程执行报时功能时，每一秒钟打印出一个时间，那么此时就需要在打印方法前面加一个sleep()方法，以便让自己每隔一秒执行一次，该过程如同闹钟一样。而wait()方法是object类的方法，用于线程间通信，这个方法会使当前拥有该对象锁的进程等待，直到其他线程调用notify（）方法或者notifyAll（）时才醒来，不过开发人员也可以给他指定一个时间，自动醒来。（wait方法针对对象进行对象头的信息更改，而notify()针对的是线程，提高了效率）
>
> 2) 对锁的 处理机制不同。由于sleep()方法的主要作用是让线程暂停执行一段时间，时间一到则自动恢复，不涉及线程间的通信，因此，调用sleep()方法并不会释放锁。而wait()方法则不同，当调用wait()方法后，线程会释放掉他所占用的锁，从而使线程所在对象中的其他synchronized数据可以被其他线程使用。
>
> 3) 使用区域不同。**wait()方法必须放在同步控制方法和同步代码块中使用**（synchronized），sleep()方法则可以放在任何地方使用。sleep()方法必须捕获异常，而wait()、notify（）、notifyAll（）不需要捕获异常。在sleep的过程中，有可能被其他对象调用他的interrupt（），产生InterruptedException。由于sleep不会释放锁标志，容易导致死锁问题的发生，因此一般情况下，推荐使用wait（）方法。

### 睡眠/挂起/阻塞

下图是使用时间片轮转法的操作系统进程的状态和它们之间的转换。

![image-20201215203548375](F:\Typora数据储存\高级语言\JAVA\JAVA并发.assets\image-20201215203548375.png)

挂起和睡眠是主动的，挂起恢复需要主动完成，睡眠恢复则是自动完成的，因为睡眠有一个睡眠时间，睡眠时间到则恢复到就绪态。而阻塞是被动的，是在等待某种事件或者资源的表现，一旦获得所需资源或者事件信息就自动回到就绪态。

睡眠和挂起是两种行为，阻塞则是一种状态。

### LockSupport.park() 方法

通过`LockSupport.park()`方法，我们也可以让线程进入休眠。**它的底层也是调用了Unsafe类的park方法**：

```java
//Unsafe.java类
//唤醒指定的线程
public native void unpark(Thread jthread);
//isAbsolute表示后面的时间是绝对时间还是相对时间，time表示时间，time=0表示无限阻塞下去
public native void park(boolean isAbsolute, long time);
12345
```

调用park方法时，还允许设置一个blocker对象，主要用来给监视工具和诊断工具确定线程受阻塞的原因。

调用park方法进入休眠后，线程状态为**WAITING**。

#### 实现原理

LockSupport.park() 的实现原理是通过二元信号量做的阻塞，**要注意的是，这个信号量最多只能加到1**。我们也可以理解成获取释放许可证的场景。unpark()方法会释放一个许可证，park()方法则是获取许可证，如果当前没有许可证，则进入休眠状态，知道许可证被释放了才被唤醒。**无论执行多少次unpark()方法，也最多只会有一个许可证。**

#### 和wait的不同

park、unpark方法和wait、notify()方法有一些相似的地方。都是休眠，然后唤醒。但是wait、notify方法有一个不好的地方，就是我们在编程的时候必须能保证wait方法比notify方法先执行。如果notify方法比wait方法晚执行的话，就会导致因wait方法进入休眠的线程接收不到唤醒通知的问题。而park、unpark则不会有这个问题，我们可以先调用unpark方法释放一个许可证，这样后面线程调用park方法时，发现已经许可证了，就可以直接获取许可证而不用进入休眠状态了。

**另外，和wait方法不同，执行park进入休眠后并不会释放持有的锁**。

#### 对中断的处理

park方法不会抛出`InterruptedException`，但是它也会响应中断。当外部线程对阻塞线程调用interrupt方法时，park阻塞的线程也会立刻返回。

```java
Thread parkThread = new Thread(new Runnable() {
            @Override
            public void run() {
                System.out.println("park begin");

                //等待获取许可
                LockSupport.park();
                //输出thread over.true
                System.out.println("thread over." + Thread.currentThread().isInterrupted());

            }
        });
        parkThread.start();

        Thread.sleep(2000);
        // 中断线程
        parkThread.interrupt();

        System.out.println("main over");
12345678910111213141516171819
```

上面的demo最终会输出

```
park begin
main over
thread over.true
123
```

说明因park进入休眠的线程收到中断通知后也会立刻返回，并且可以手动通过`Thread.currentThread().isInterrupted()`获取到中断位。

![image-20201216214206475](F:\Typora数据储存\高级语言\JAVA\JAVA并发.assets\image-20201216214206475.png)

# BIO/NIO/AIO

BIO：同步阻塞式IO，服务器实现模式为一个连接一个线程，即客户端有连接请求时服务器端就需要启动一个线程进行处理，如果这个连接不做任何事情会造成不必要的线程开销，当然可以通过线程池机制改善。 
NIO：同步非阻塞式IO，服务器实现模式为一个请求一个线程，即客户端发送的连接请求都会注册到多路复用器上，多路复用器轮询到连接有I/O请求时才启动一个线程进行处理。 
AIO(NIO.2)：异步非阻塞式IO，服务器实现模式为一个有效请求一个线程，客户端的I/O请求都是由OS先完成了再通知服务器应用去启动线程进行处理。 

**BIO**  
同步阻塞式IO，相信每一个学习过操作系统网络编程或者任何语言的网络编程的人都很熟悉，在while循环中服务端会调用accept方法等待接收客户端的连接请求，一旦接收到一个连接请求，就可以建立通信套接字在这个通信套接字上进行读写操作，此时不能再接收其他客户端连接请求，只能等待同当前连接的客户端的操作执行完成。 
如果BIO要能够同时处理多个客户端请求，就必须使用多线程，即每次accept阻塞等待来自客户端请求，一旦受到连接请求就建立通信套接字同时开启一个新的线程来处理这个套接字的数据读写请求，然后立刻又继续accept等待其他客户端连接请求，即为每一个客户端连接请求都创建一个线程来单独处理，大概原理图就像这样： 
![img](http://static.oschina.net/uploads/img/201510/23094528_ZQyy.jpg) 

虽然此时服务器具备了高并发能力，即能够同时处理多个客户端请求了，但是却带来了一个问题，随着开启的线程数目增多，将会消耗过多的内存资源，导致服务器变慢甚至崩溃，NIO可以一定程度解决这个问题。


**NIO** 
同步非阻塞式IO，关键是采用了事件驱动的思想来实现了一个多路转换器。 
NIO与BIO最大的区别就是只需要开启一个线程就可以处理来自多个客户端的IO事件，这是怎么做到的呢？ 
就是多路复用器，可以监听来自多个客户端的IO事件： 
A. 若服务端监听到客户端连接请求，便为其建立**通信套接字(java中就是通道)**，然后返回继续监听，若同时有多个客户端连接请求到来也可以全部收到，依次为它们都建立通信套接字。 
B. 若服务端监听到来自已经创建了通信套接字的客户端**发送来的数据**，就会**调用对应接口**处理接收到的数据，若同时有多个客户端发来数据也可以依次进行处理。 
C. 监听多个客户端的连接请求和接收数据请求同时还能监听自己时候有数据要发送。 
![img](http://static.oschina.net/uploads/img/201510/23094528_OF9c.jpg) 

总之就是在一个线程中就可以调用多路复用接口（java中是select）阻塞同时监听来自多个客户端的IO请求，一旦有收到IO请求就调用对应函数处理。 

NIO采用的是一种多路复用的机制，利用单线程轮询事件，高效定位就绪的Channel来决定做什么，只是Select阶段是阻塞式的，能有效避免大量连接数时，频繁线程的切换带来的性能或各种问题。

![img](https://img-blog.csdn.net/20180927094928997?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xpc2hhMDA2/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

 

上图随便画的，只是方便理解，并不能作为实现的具体的参考。

首先，Requester方通过Selector.open()创建了一个Selector准备好了调度角色。

创建了SocketChannel(ServerSocketChannel) 并注册到Selector中，通过设置key（SelectionKey）告诉调度者所应该关注的连接请求。

阻塞，Selector阻塞在select操作中，如果发现有Channel发生连接请求，就会唤醒处理请求。


**各自应用场景** 

到这里你也许已经发现，一旦有请求到来(不管是几个同时到还是只有一个到)，都会调用对应IO处理函数处理，所以：

（1）NIO适合处理连接数目特别多，但是连接比较短（轻操作）的场景，Jetty，Mina，ZooKeeper等都是基于java nio实现。

（2）BIO方式适用于连接数目比较小且固定的场景，这种方式对服务器资源要求比较高，并发局限于应用中。

（3）AIO新的IO2.0，即NIO2.0，jdk1.7开始应用，叫做异步不阻塞的IO。AIO引入异常通道的概念，采用了Proactor模式，简化了程序编写，一个有效的请求才启动一个线程，它的特点是先由操作系统完成后才通知服务端程序启动线程去处理，一般适用于连接数较多且连接时间长的应用。

![img](https://img-blog.csdn.net/20180927103917548?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xpc2hhMDA2/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

 

## 一、IO流（同步、阻塞）



### 1、概述

IO流简单来说就是input和output流，IO流主要是用来处理设备之间的数据传输，Java IO对于数据的操作都是通过流实现的，而java用于操作流的对象都在IO包中。



### 2、分类

按操作数据分为：字节流（InputStream、OutputStream）和字符流（Reader、Writer）

按流向分：输入流（Reader、InputStream）和输出流（Writer、OutputStream）
![img](https://images2018.cnblogs.com/blog/1302135/201808/1302135-20180816173529402-760533731.png)



### 3、字符流

**概述**

只用来处理文本数据

数据最常见的表现形式是文件，字符流用来操作文件的子类一般是FileReader和FileWriter

字符流读写文件注意事项：

- 写入文件必须要用flush()刷新
- 用完流记得要关闭流
- 使用流对象要抛出IO异常
- 定义文件路径时，可以用"/"或者"\"
- 在创建一个文件时，如果目录下有同名文件将被覆盖
- 在读取文件时，必须保证该文件已存在，否则抛出异常

**字符流的缓冲区**

- 缓冲区的出现是为了提高流的操作效率而出现的
- 需要被提高效率的流作为参数传递给缓冲区的构造函数
- 在缓冲区中封装了一个数组，存入数据后一次取出



### 4、字节流

**概述**

用来处理媒体数据

字节流读写文件注意事项：

- 字节流和字符流的基本操作是相同的，但是想要操作媒体流就需要用到字节流
- 字节流因为操作的是字节，所以可以用来操作媒体文件（媒体文件也是以字节存储的）
- 输入流（InputStream）、输出流（OutputStream）
- 字节流操作可以不用刷新流操作
- InputStream特有方法：int available()（返回文件中的字节个数）



字节流的缓冲区
字节流缓冲区跟字符流缓冲区一样，也是为了提高效率



### 5、Java Scanner类

Java 5添加了java.util.Scanner类，这是一个用于扫描输入文本的新的实用程序

#### 关于nextInt()、next()、nextLine()的理解

nextInt()：只能读取数值，若是格式不对，会抛出java.util.InputMismatchException异常

next()：遇见第一个有效字符（非空格，非换行符）时，开始扫描，当遇见第一个分隔符或结束符（空格或换行符）时，结束扫描，获取扫描到的内容

nextLine()：可以扫描到一行内容并作为字符串而被捕获到

#### 关于hasNext()、hasNextLine()、hasNextxxx()的理解

就是为了判断输入行中是否还存在xxx的意思

#### 与delimiter()有关的方法

应该是输入内容的分隔符设置，

## 二、NIO（同步、非阻塞）

**NIO之所以是同步，是因为它的accept/read/write方法的内核I/O操作都会阻塞当前线程**

首先，我们要先了解一下NIO的三个主要组成部分：Channel（通道）、Buffer（缓冲区）、Selector（选择器）



### （1）Channel（通道）

Channel（通道）：Channel是一个对象，可以通过它读取和写入数据。可以把它看做是IO中的流，不同的是：

- Channel是双向的，既可以读又可以写，而流是单向的
- Channel可以进行异步的读写
- 对Channel的读写必须通过buffer对象

正如上面提到的，所有数据都通过Buffer对象处理，所以，您永远不会将字节直接写入到Channel中，相反，您是将数据写入到Buffer中；同样，您也不会从Channel中读取字节，而是将数据从Channel读入Buffer，再从Buffer获取这个字节。

因为Channel是双向的，所以Channel可以比流更好地反映出底层操作系统的真实情况。特别是在Unix模型中，底层操作系统通常都是双向的。

在Java NIO中的Channel主要有如下几种类型：

- FileChannel：从文件读取数据的
- DatagramChannel：读写UDP网络协议数据
- SocketChannel：读写TCP网络协议数据
- ServerSocketChannel：可以监听TCP连接



### （2）Buffer

Buffer是一个对象，它包含一些要写入或者读到Stream对象的。应用程序不能直接对 Channel 进行读写操作，而必须通过 Buffer 来进行，即 Channel 是通过 Buffer 来读写数据的。

在NIO中，所有的数据都是用Buffer处理的，它是NIO读写数据的中转池。Buffer实质上是一个数组，通常是一个字节数据，但也可以是其他类型的数组。但一个缓冲区不仅仅是一个数组，重要的是它提供了对数据的结构化访问，而且还可以跟踪系统的读写进程。

使用 Buffer 读写数据一般遵循以下四个步骤：

1.写入数据到 Buffer；

2.调用 flip() 方法；

3.从 Buffer 中读取数据；

4.调用 clear() 方法或者 compact() 方法。

当向 Buffer 写入数据时，Buffer 会记录下写了多少数据。一旦要读取数据，需要通过 flip() 方法将 Buffer 从写模式切换到读模式。在读模式下，可以读取之前写入到 Buffer 的所有数据。

一旦读完了所有的数据，就需要清空缓冲区，让它可以再次被写入。有两种方式能清空缓冲区：调用 clear() 或 compact() 方法。clear() 方法会清空整个缓冲区。compact() 方法只会清除已经读过的数据。任何未读的数据都被移到缓冲区的起始处，新写入的数据将放到缓冲区未读数据的后面。

Buffer主要有如下几种：

- ByteBuffer
- CharBuffer
- DoubleBuffer
- FloatBuffer
- IntBuffer
- LongBuffer
- ShortBuffer

copyFile实例（NIO）

CopyFile是一个非常好的读写结合的例子，我们将通过CopyFile这个实力让大家体会NIO的操作过程。CopyFile执行三个基本的操作：创建一个Buffer，然后从源文件读取数据到缓冲区，然后再将缓冲区写入目标文件。

```java
public static void copyFileUseNIO(String src,String dst) throws IOException{
//声明源文件和目标文件
        FileInputStream fi=new FileInputStream(new File(src));
        FileOutputStream fo=new FileOutputStream(new File(dst));
        //获得传输通道channel
        FileChannel inChannel=fi.getChannel();
        FileChannel outChannel=fo.getChannel();
        //获得容器buffer
        ByteBuffer buffer=ByteBuffer.allocate(1024);
        while(true){
            //判断是否读完文件
            int eof =inChannel.read(buffer);
            if(eof==-1){
                break;  
            }
            //重设一下buffer的position=0，limit=position
            buffer.flip();
            //开始写
            outChannel.write(buffer);
            //写完要重置buffer，重设position=0,limit=capacity
            buffer.clear();
        }
        inChannel.close();
        outChannel.close();
        fi.close();
        fo.close();
}   
```



### （3）Selector（选择器对象）

首先需要了解一件事情就是线程上下文切换开销会在高并发时变得很明显，这是同步阻塞方式的低扩展性劣势。

Selector是一个对象，它可以注册到很多个Channel上，监听各个Channel上发生的事件，并且能够根据事件情况决定Channel读写。这样，通过一个线程管理多个Channel，就可以处理大量网络连接了。

#### selector优点

有了Selector，我们就可以利用一个线程来处理所有的channels。线程之间的切换对操作系统来说代价是很高的，并且每个线程也会占用一定的系统资源。所以，对系统来说使用的线程越少越好。

#### 1.如何创建一个Selector

Selector 就是您注册对各种 I/O 事件兴趣的地方，而且当那些事件发生时，就是这个对象告诉您所发生的事件。

```
Selector selector = Selector.open();
```

#### 2.注册Channel到Selector

为了能让Channel和Selector配合使用，我们需要把Channel注册到Selector上。通过调用 channel.register（）方法来实现注册：

```
channel.configureBlocking(false);
SelectionKey key =channel.register(selector,SelectionKey.OP_READ);
```

注意，注册的Channel 必须设置成异步模式 才可以,否则异步IO就无法工作，这就意味着我们不能把一个FileChannel注册到Selector，因为FileChannel没有异步模式，但是网络编程中的SocketChannel是可以的。

#### 3.关于SelectionKey

请注意对register()的调用的返回值是一个SelectionKey。 SelectionKey 代表这个通道在此 Selector 上注册。当某个 Selector 通知您某个传入事件时，它是通过提供对应于该事件的 SelectionKey 来进行的。SelectionKey 还可以用于取消通道的注册。

SelectionKey中包含如下属性：

- The interest set
- The ready set
- The Channel
- The Selector
- An attached object (optional)

#### （1）Interest set

就像我们在前面讲到的把Channel注册到Selector来监听感兴趣的事件，interest set就是你要选择的感兴趣的事件的集合。你可以通过SelectionKey对象来读写interest set:

```
int interestSet = selectionKey.interestOps();
boolean isInterestedInAccept  = interestSet & SelectionKey.OP_ACCEPT;
boolean isInterestedInConnect = interestSet & SelectionKey.OP_CONNECT;
boolean isInterestedInRead    = interestSet & SelectionKey.OP_READ;
boolean isInterestedInWrite   = interestSet & SelectionKey.OP_WRITE; 
```

通过上面例子可以看到，我们可以通过用AND 和SelectionKey 中的常量做运算，从SelectionKey中找到我们感兴趣的事件。

#### （2）Ready Set

ready set 是通道已经准备就绪的操作的集合。在一次选Selection之后，你应该会首先访问这个ready set。Selection将在下一小节进行解释。可以这样访问ready集合：

```
int readySet = selectionKey.readyOps();
```

可以用像检测interest集合那样的方法，来检测Channel中什么事件或操作已经就绪。但是，也可以使用以下四个方法，它们都会返回一个布尔类型：

```
selectionKey.isAcceptable();
selectionKey.isConnectable();
selectionKey.isReadable();
selectionKey.isWritable();
```

#### （3）Channel 和 Selector

我们可以通过SelectionKey获得Selector和注册的Channel：

```
Channel  channel  = selectionKey.channel();
Selector selector = selectionKey.selector(); 
```

#### （4）Attach一个对象

可以将一个对象或者更多信息attach 到SelectionKey上，这样就能方便的识别某个给定的通道。例如，可以附加 与通道一起使用的Buffer，或是包含聚集数据的某个对象。使用方法如下：

```
selectionKey.attach(theObject);
Object attachedObj = selectionKey.attachment();
```

还可以在用register()方法向Selector注册Channel的时候附加对象。如：

```
SelectionKey key = channel.register(selector, SelectionKey.OP_READ, theObject);
```

#### 4.关于SelectedKeys()

**生产系统中一般会额外进行就绪状态检查**

一旦调用了select()方法，它就会返回一个数值，表示一个或多个通道已经就绪，然后你就可以通过调用selector.selectedKeys()方法返回的SelectionKey集合来获得就绪的Channel。请看演示方法：

```
Set<SelectionKey> selectedKeys = selector.selectedKeys();
```

当你通过Selector注册一个Channel时，channel.register()方法会返回一个SelectionKey对象，这个对象就代表了你注册的Channel。这些对象可以通过selectedKeys()方法获得。你可以通过迭代这些selected key来获得就绪的Channel，下面是演示代码：

```
Set<SelectionKey> selectedKeys = selector.selectedKeys();
Iterator<SelectionKey> keyIterator = selectedKeys.iterator();
while(keyIterator.hasNext()) { 
SelectionKey key = keyIterator.next();
if(key.isAcceptable()) {
// a connection was accepted by a ServerSocketChannel.
} else if (key.isConnectable()) {
// a connection was established with a remote server.
} else if (key.isReadable()) {
// a channel is ready for reading
} else if (key.isWritable()) {
// a channel is ready for writing
}
keyIterator.remove();
}
```

这个循环遍历selected key的集合中的每个key，并对每个key做测试来判断哪个Channel已经就绪。

请注意循环中最后的keyIterator.remove()方法。Selector对象并不会从自己的selected key集合中自动移除SelectionKey实例。我们需要在处理完一个Channel的时候自己去移除。当下一次Channel就绪的时候，Selector会再次把它添加到selected key集合中。

SelectionKey.channel()方法返回的Channel需要转换成你具体要处理的类型，比如是ServerSocketChannel或者SocketChannel等等。



#### 5、NIO多路复用

主要步骤和元素：

- 首先，通过 Selector.open() 创建一个 Selector，作为类似调度员的角色。
- 然后，创建一个 ServerSocketChannel，并且向 Selector 注册，通过指定 SelectionKey.OP_ACCEPT，告诉调度员，它关注的是新的连接请求。
- 注意，为什么我们要明确配置非阻塞模式呢？这是因为阻塞模式下，注册操作是不允许的，会抛出 IllegalBlockingModeException 异常。
- Selector 阻塞在 select 操作，当有 Channel 发生接入请求，就会被唤醒。
- 在 具体的 方法中，通过 SocketChannel 和 Buffer 进行数据操作

IO 都是同步阻塞模式，所以需要多线程以实现多任务处理。而 NIO 则是利用了单线程轮询事件的机制，通过高效地定位就绪的 Channel，来决定做什么，仅仅 select 阶段是阻塞的，可以有效避免大量客户端连接时，频繁线程切换带来的问题，应用的扩展能力有了非常大的提高

## 三、NIO2(异步、非阻塞)

AIO是异步IO的缩写，虽然NIO在网络操作中，提供了非阻塞的方法，但是NIO的IO行为还是同步的。对于NIO来说，我们的业务线程是在IO操作准备好时，得到通知，接着就由这个线程自行进行IO操作，IO操作本身是同步的。

但是对AIO来说，则更加进了一步，它不是在IO准备好时再通知线程，而是在IO操作已经完成后，再给线程发出通知。因此AIO是不会阻塞的，此时我们的业务逻辑将变成一个回调函数，等待IO操作完成后，由系统自动触发。

与NIO不同，当进行读写操作时，只须直接调用API的read或write方法即可。这两种方法均为异步的，对于读操作而言，当有流可读取时，操作系统会将可读的流传入read方法的缓冲区，并通知应用程序；对于写操作而言，当操作系统将write方法传递的流写入完毕时，操作系统主动通知应用程序。 即可以理解为，read/write方法都是异步的，完成后会主动调用回调函数。 在JDK1.7中，这部分内容被称作NIO.2，主要在Java.nio.channels包下增加了下面四个异步通道：

- AsynchronousSocketChannel
- AsynchronousServerSocketChannel
- AsynchronousFileChannel
- AsynchronousDatagramChannel

在AIO socket编程中，服务端通道是AsynchronousServerSocketChannel，这个类提供了一个open()静态工厂，一个bind()方法用于绑定服务端IP地址（还有端口号），另外还提供了accept()用于接收用户连接请求。在客户端使用的通道是AsynchronousSocketChannel,这个通道处理提供open静态工厂方法外，还提供了read和write方法。

在AIO编程中，发出一个事件（accept read write等）之后要指定事件处理类（回调函数），AIO中的事件处理类是CompletionHandler<V,A>，这个接口定义了如下两个方法，分别在异步操作成功和失败时被回调。

void completed(V result, A attachment);

void failed(Throwable exc, A attachment);

# 线程共享

- 每个线程执行的代码相同，可以使用同一个Runnable对象
- 每个线程执行的代码不同，用不同的Runnable对象,传入同一个对象作为形参/Runnable对象为同一个对象的内部类

**每个线程执行的代码相同，可以使用同一个Runnable对象**

```java
public class RunnalbleTest2 implements Runnable {
    private int threadCnt = 10;

    @Override
    public void run() {
        while (true) {
            if (threadCnt > 0) {
                System.out.println(Thread.currentThread().getName() + " 剩余个数 " + threadCnt);
                threadCnt--;
                try {
                    Thread.sleep(30);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            } else {
                break;
            }
        }
    }

    public static void main(String[] args) {
        RunnalbleTest2 runnalbleTest2 = new RunnalbleTest2();
        new Thread(runnalbleTest2).start();
        new Thread(runnalbleTest2).start();
    }
}
```

```
Thread-0 剩余个数 10
Thread-1 剩余个数 10
Thread-0 剩余个数 8
Thread-1 剩余个数 7
Thread-0 剩余个数 6
Thread-1 剩余个数 5
Thread-0 剩余个数 4
Thread-1 剩余个数 3
Thread-0 剩余个数 2
Thread-1 剩余个数 1
```

**每个线程执行的代码不同，用不同的Runnable对象**

注意：notify的线程不一定能马上使用CPU，有可能另一个线程执行几次后才重新轮到它

```java
public class Acount {
    private int money;
    Acount(int money) {
        this.money = money;
    }

    public synchronized void getMoney(int money) {
        while (this.money < money) {
            System.out.println("余额不足");
            try {
                wait();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
        try {
            Thread.sleep(500);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        this.money -= money;
        System.out.println("取出" + money + ",还剩" + this.money);
    }

    public synchronized void setMoney(int money) {
        try {
            Thread.sleep(500);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }

        this.money += money;
        System.out.println("新存入" + money + ",总额：" + this.money);
        notify();

    }

    public static void main(String[] args) {
        Acount acount = new Acount(0);
        Bank bank = new Bank(acount);
        Consumer consumer = new Consumer(acount);
        new Thread(bank).start();
        new Thread(consumer).start();

    }
}
```

```java
import java.util.Random;

public class Bank implements Runnable{
    Acount acount;
    public Bank(Acount acount) {
        this.acount = acount;
    }

    @Override
    public void run() {
        while(true) {
            int random = (int)(Math.random() * 1000);
            acount.setMoney(random);
        }
    }
}
```

```java
public class Consumer implements Runnable{
    Acount acount;

    Consumer(Acount acount) {
        this.acount = acount;
    }

    @Override
    public void run() {
        while (true) {
            int random = (int)(Math.random() * 1000);
            acount.getMoney(random);
        }
    }

}
```

```java
新存入442,总额：442
余额不足
新存入196,总额：638
新存入815,总额：1453
取出534,还剩919
新存入402,总额：1321
取出719,还剩602
取出11,还剩591
....
```

