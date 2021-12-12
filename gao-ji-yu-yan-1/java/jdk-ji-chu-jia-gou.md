# JDK 版本

## JDK 8

#### Lambda表达式和函数式接口

#### 接口的默认方法和静态方法

#### 方法引用

#### 重复注解

#### Streams

#### Date/Time API(JSR 310)

## JDK9

#### Java平台模块化系统

 该特性是Java 9 最大的一个特性，Java 9起初的代号就叫Jigsaw，最近被更改为Modularity，Modularity提供了类似于OSGI框架的功能，模块之间存在相互的依赖关系，可以导出一个公共的API，并且隐藏实现的细节，Java提供该功能的主要的动机在于，减少内存的开销，我们大家都知道，在JVM启动的时候，至少会有30～60MB的内存加载，主要原因是JVM需要加载rt.jar，不管其中的类是否被classloader加载，第一步整个jar都会被JVM加载到内存当中去，模块化可以根据模块的需要加载程序运行需要的class，那么JVM是如何知道需要加载那些class的呢？这就是在Java 9 中引入的一个新的文件module.java我们大致来看一下一个例子（module-info.java）

**模块描述器**

模块化的 JAR 文件都包含一个额外的模块描述器。在这个模块描述器中, 对其它模块的依赖是通过 “requires”  来表示的。另外, “exports” 语句控制着内部的哪些包是可以被其它模块访问到的。所有不被导出的包默认都封装在模块的里面。如下是一个模块描述器的示例，存在于 “module-info.java” 文件中

```java
module blog {
 exports com.pluralsight.blog;
 
 requires cms;
}
```

![img](https:////upload-images.jianshu.io/upload_images/8077763-ac7fc8d6816822d2.png?imageMogr2/auto-orient/strip|imageView2/2/w/1200/format/webp)

####  JShell

基于JAVA语言的脚本编写器

#### 私有接口方法

解决接口中代码复用问题

#### Unsafe类的API操作

在JDK 9中提供了一个新的包，叫做java.lang.invoke里面有一系列很重要的类比如VarHandler和MethodHandles，提供了类似于原子操作以及Unsafe操作的功能。

#### 使用G1垃圾回收器作为默认的垃圾回收器

移除很多已经被过期的GCC回收器（是移除哦，因为在Jdk 8 中只是加了过期的标记）

#### HotSpot的统一的日志处理框架



## JDK 10

#### var声明变量

自动进行局部变量类型推断

```java
var str = "ABC"; //根据推断为 字符串类型
var l = 10L;//根据10L 推断long 类型
var flag = true;//根据 true推断 boolean 类型
var flag1 = 1;//这里会推断boolean类型。0表示false 非0表示true
var list = new ArrayList<String>();  // 推断 ArrayList<String>
var stream = list.stream();          // 推断 Stream<String>
```

#### 移除javah

移除Native-Header Generation Tool （javah）
JDK10 从JDK中移除了javah 工具。该工具已被JDK8 （JDK-7150368）中添加javac高级功能所取代。此功能提供了在编译java源代码时编写本机头文件的功能，从而无需使用单独的工具。

#### IO流大家族添加charset参数

#### 其它

- 将JDK多存储库合并为单存储库
- 并行Full GC 的G1
- 垃圾回收接口
- 应用数据共享
- 线程局部管控
- 基于实验JAVA 的JIT 编译器
- 备用内存设备上分配堆内存

## JDK 11

#### HTTPClient转正

#### 密码学方面的改进

#### 更灵活的String

#### 废弃Nashorn JavaScript引擎

## jdk 12

#### switch语句

#### 数字转字符串NumberFormat

#### String的成员函数非常多



# JDK基础架构

![image-20200903234642556](F:\Typora数据储存\高级语言\JAVA\JDK基础架构.assets\image-20200903234642556.png)



## Java.util

集合又称为容器，用于存储、提取、删除数据。JDK提供的集合API都包含在 java.util 包内。

**添加一个对象到集合中时，集合里面存放的是对象的引用**

集合的分支：

集合框架两大分支：Collection接口和Map接口。

# 1、Collection接口：

![image-20200904000550943](F:\Typora数据储存\高级语言\JAVA\JDK基础架构.assets\image-20200904000550943.png)

**自从JDK 8 后Collecition 接口加入了stream（）API ，能够使得实现collection接口的类的数据进行流式处理，提高了编码效率**

stream 的实现是由final修饰的不可继承类StreamSupport实现的功能，能够依据参数选择是否进行并发。



#### ArrayList与LinkedList

###### List接口常用方法：

1、add(Object element)： 向列表的尾部添加指定的元素。

2、size()： 返回列表中的元素个数。

3、get(int index)： 返回列表中指定位置的元素，index从0开始。

4、add(int index, Object element)： 在列表的指定位置插入指定元素。

5、set(int i, Object element)： 将索引i位置元素替换为元素element并返回被替换的元素。

6、clear()： 从列表中移除所有元素。

7、isEmpty()： 判断列表是否包含元素，不包含元素则返回 true，否则返回false。

8、contains(Object o)： 如果列表包含指定的元素，则返回 true。

9、remove(int index)： 移除列表中指定位置的元素，并返回被删元素。

10、remove(Object o)： 移除集合中第一次出现的指定元素，移除成功返回true，否则返回false。

11、iterator()： 返回按适当顺序在列表的元素上进行迭代的迭代器。

ArrayList和Vector使用了数组的实现，可以认为ArrayList或者Vector封装了对内部数组的操作，比如向数组中添加，删除，插入新的元素或者数据的扩展和重定向。

LinkedList使用了循环双向链表数据结构。与基于数组ArrayList相比，这是两种截然不同的实现技术，这也决定了它们将适用于完全不同的工作场景。

LinkedList链表由一系列表项连接而成。一个表项总是包含3个部分：元素内容，前驱表和后驱表，如图所示：

![img](https://images0.cnblogs.com/i/510141/201403/302304505002745.jpg)

在下图展示了一个包含3个元素的LinkedList的各个表项间的连接关系。在JDK的实现中，无论LikedList是否为空，链表内部都有一个header表项，它既表示链表的开始，也表示链表的结尾。表项header的后驱表项便是链表中第一个元素，表项header的前驱表项便是链表中最后一个元素。

![img](https://images0.cnblogs.com/i/510141/201403/302258007343926.jpg) 

下面以增加和删除元素为例比较ArrayList和LinkedList的不同之处：

###### （1）增加元素到列表尾端：

**只要ArrayList的当前容量足够大，ArrayList比LinkedList 快**

在ArrayList中增加元素到队列尾端的代码如下：

```
public boolean add(E e){
   ensureCapacity(size+1);//确保内部数组有足够的空间
   elementData[size++]=e;//将元素加入到数组的末尾，完成添加
   return true;      
} 
```

 ArrayList中add()方法的性能决定于ensureCapacity()方法。ensureCapacity()的实现如下：

 

```
public vod ensureCapacity(int minCapacity){
  modCount++;
  int oldCapacity=elementData.length;
  if(minCapacity>oldCapacity){    //如果数组容量不足，进行扩容
      Object[] oldData=elementData;
      int newCapacity=(oldCapacity*3)/2+1;  //扩容到原始容量的1.5倍
      if(newCapacitty<minCapacity)   //如果新容量小于最小需要的容量，则使用最小
                                                    //需要的容量大小
         newCapacity=minCapacity ;  //进行扩容的数组复制
         elementData=Arrays.copyof(elementData,newCapacity);
  }
}
```

 

可以看到，只要ArrayList的当前容量足够大，add()操作的效率非常高的。只有当ArrayList对容量的需求超出当前数组大小时，才需要进行扩容。扩容的过程中，会进行大量的数组复制操作。而数组复制时，最终将调用System.arraycopy()方法，因此add()操作的效率还是相当高的。

LinkedList 的add()操作实现如下，它也将任意元素增加到队列的尾端：

```
public boolean add(E e){
   addBefore(e,header);//将元素增加到header的前面
   return true;
}
```

其中addBefore()的方法实现如下：

```
private Entry<E> addBefore(E e,Entry<E> entry){
     Entry<E> newEntry = new Entry<E>(e,entry,entry.previous);
     newEntry.provious.next=newEntry;
     newEntry.next.previous=newEntry;
     size++;
     modCount++;
     return newEntry;
}
```

 可见，LinkeList由于使用了链表的结构，因此不需要维护容量的大小。从这点上说，它比ArrayList有一定的性能优势，然而，每次的元素增加都需要新建一个Entry对象，并进行更多的赋值操作。在频繁的系统调用中，对性能会产生一定的影响。

###### （2）增加元素到列表任意位置

除了提供元素到List的尾端，List接口还提供了在任意位置插入元素的方法：void add(int index,E element);

由于实现的不同，ArrayList和LinkedList在这个方法上存在一定的性能差异，由于ArrayList是基于数组实现的，而数组是一块连续的内存空间，如果在数组的任意位置插入元素，必然导致在该位置后的所有元素需要重新排列，因此，其效率相对会比较低。

以下代码是ArrayList中的实现：

```
public void add(int index,E element){
   if(index>size||index<0)
      throw new IndexOutOfBoundsException(
        "Index:"+index+",size: "+size);
         ensureCapacity(size+1);
         System.arraycopy(elementData,index,elementData,index+1,size-index);
         elementData[index] = element;
         size++;
}  
```

可以看到每次插入操作，都会进行一次数组复制。而这个操作在增加元素到List尾端的时候是不存在的，大量的数组重组操作会导致系统性能低下。并且插入元素在List中的位置越是靠前，数组重组的开销也越大。

而LinkedList此时显示了优势：

```
public void add(int index,E element){
   addBefore(element,(index==size?header:entry(index)));
}
```

可见，对LinkedList来说，在List的尾端插入数据与在任意位置插入数据是一样的，不会因为插入的位置靠前而导致插入的方法性能降低。

###### （3）删除任意位置元素

**LinkedList的pollFirst()比remove()效率高很多，而且与删除的位置有很大关系，ArrayList与LinkedList同样用remove（0），LinkedList会慢很多，ArrayList的remove（0）与LinkedList的pollFirst效率相似。**

对于元素的删除，List接口提供了在任意位置删除元素的方法：

```
public E remove(int index);
```

对ArrayList来说，remove()方法和add()方法是雷同的。在任意位置移除元素后，都要进行数组的重组。ArrayList的实现如下：

```
public E remove(int index){
   RangeCheck(index);
   modCount++;
   E oldValue=(E) elementData[index];
  int numMoved=size-index-1;
  if(numMoved>0)
     System.arraycopy(elementData,index+1,elementData,index,numMoved);
     elementData[--size]=null;
     return oldValue;
}
```

可以看到，在ArrayList的每一次有效的元素删除操作后，都要进行数组的重组。并且删除的位置越靠前，数组重组时的开销越大。

```
public E remove(int index){
  return remove(entry(index));         
}
private Entry<E> entry(int index){
  if(index<0 || index>=size)
      throw new IndexOutBoundsException("Index:"+index+",size:"+size);
      Entry<E> e= header;
      if(index<(size>>1)){//要删除的元素位于前半段
         for(int i=0;i<=index;i++)
             e=e.next;
     }else{
         for(int i=size;i>index;i--)
             e=e.previous;
     }
         return e;
}
```

在LinkedList的实现中，首先要通过循环找到要删除的元素。如果要删除的位置处于List的前半段，则从前往后找；若其位置处于后半段，则从后往前找。因此无论要删除较为靠前或者靠后的元素都是非常高效的；但要移除List中间的元素却几乎要遍历完半个List，在List拥有大量元素的情况下，效率很低。

###### （4）容量参数

容量参数是ArrayList和Vector等基于数组的List的特有性能参数。它表示初始化的数组大小。当ArrayList所存储的元素数量超过其已有大小时。它便会进行扩容，数组的扩容会导致整个数组进行一次 System.arraycopy复制。因此合理的数组大小有助于减少数组扩容的次数，从而提高系统性能。

```
public  ArrayList(){
  this(10);  
}
public ArrayList (int initialCapacity){
   super();
   if(initialCapacity<0)
       throw new IllegalArgumentException("Illegal Capacity:"+initialCapacity)
      this.elementData=new Object[initialCapacity];
}
```

ArrayList提供了一个可以制定初始数组大小的构造函数：

```
public ArrayList(int initialCapacity) 
```

现以构造一个拥有100万元素的List为例，当使用默认初始化大小时，其消耗的相对时间为125ms左右，当直接制定数组大小为100万时，构造相同的ArrayList仅相对耗时16ms。

###### （5）遍历列表

遍历列表操作是最常用的列表操作之一，在JDK1.5之后，至少有3中常用的列表遍历方式：forEach操作，迭代器和for循环。

```
String tmp;
long start=System.currentTimeMills();    //ForEach 
for(String s:list){
    tmp=s;
}
System.out.println("foreach spend:"+(System.currentTimeMills()-start));
start = System.currentTimeMills();
for(Iterator<String> it=list.iterator();it.hasNext();){    
   tmp=it.next();
}
System.out.println("Iterator spend;"+(System.currentTimeMills()-start));
start=System.currentTimeMills();
int size=;list.size();
for(int i=0;i<size;i++){                     
    tmp=list.get(i);
}
System.out.println("for spend;"+(System.currentTimeMills()-start));
```

构造一个拥有100万数据的ArrayList和等价的LinkedList，生成一个随机访问列表，使用以上代码进行测试，测试结果的相对耗时如下表所示： ![img](https://images0.cnblogs.com/i/510141/201403/312314130322078.jpg)

可以看到，最简便的ForEach循环并没有很好的性能表现，综合性能不如普通的迭代器，而是用for循环通过随机访问遍历列表时，ArrayList表项很好，但是LinkedList的表现却无法让人接受，甚至没有办法等待程序的结束。这是因为对LinkedList进行随机访问时，总会进行一次列表的遍历操作。性能非常差，应避免使用。

#### Vector

List接口下面的AbstractList有两个实现，一个是ArrayList，另外一个是vector。 从源码的角度来看，因为Vector的方法前加了，synchronized 关键字，也就是同步的意思，sun公司希望Vector是线程安全的，而希望arraylist是高效的。

而线程不安全的例子：

> 一个 ArrayList ，在添加一个元素的时候，它可能会有两步来完成： 
> \1. 在 Items[Size] 的位置存放此元素； 
> \2. 增大 Size 的值。 
> 在单线程运行的情况下，如果 Size = 0，添加一个元素后，此元素在位置 0，而且 Size=1； 
> 而如果是在多线程情况下，比如有两个线程，线程 A 先将元素存放在位置 0。但是此时 CPU 调度线程A暂停，线程 B 得到运行的机会。线程B也向此 ArrayList 添加元素，因为此时 Size 仍然等于 0 （注意哦，我们假设的是添加一个元素是要两个步骤哦，而线程A仅仅完成了步骤1），所以线程B也将元素存放在位置0。然后线程A和线程B都继续运行，都增加 Size 的值。 
> 那好，现在我们来看看 ArrayList 的情况，元素实际上只有一个，存放在位置 0，而 Size 却等于 2。这就是“线程不安全”了。 

线程安全解决办法 ：

方法1： Collections.synchronizedList(new LinkedList<String>())

方法2:   LinkedList和ArrayList换成线程安全的集合，如CopyOnWriteArrayList，ConcurrentLinkedQueue......(ReetrantLock实现)

方法3：Vector(内部主要使用synchronized关键字实现同步)

# 2、Map接口

![image-20200904000653460](F:\Typora数据储存\高级语言\JAVA\JDK基础架构.assets\image-20200904000653460.png)

## 各个接口的区别

1. HashMap中k的值没有顺序，常用来做统计。

2. LinkedHashMap吧。它内部有一个链表，保持Key插入的顺序。迭代的时候，也是按照插入顺序迭代，而且迭代比HashMap快。

3. TreeMap的顺序是Key的自然顺序（如整数从小到大），也可以指定比较函数。但不是插入的顺序。

4. Hashtable与 HashMap类似,它继承自Dictionary类、不同的是:它不允许记录的键或者值为空;它支持线程的同步、即任一时刻只有一个线程能写Hashtable,因此也导致了 Hashtable在写入时会比较慢。
5. HashMap 非线程安全 TreeMap 非线程安全

## 一、hashmap

**哈希表**：相比上述几种数据结构，在哈希表中进行添加，删除，查找等操作，性能十分之高，不考虑哈希冲突的情况下（后面会探讨下哈希冲突的情况），仅需一次定位即可完成，时间复杂度为O(1)，接下来我们就来看看哈希表是如何实现达到惊艳的常数阶O(1)的。

我们知道，数据结构的物理存储结构只有两种：**顺序存储结构**和**链式存储结构**（像栈，队列，树，图等是从逻辑结构去抽象的，映射到内存中，也这两种物理组织形式），而在上面我们提到过，在数组中根据下标查找某个元素，一次定位就可以达到，哈希表利用了这种特性，**哈希表的主干就是数组**。

**比如我们要新增或查找某个元素，我们通过把当前元素的关键字 通过某个函数映射到数组中的某个位置，通过数组下标一次定位就可完成操作。**
　　
这个函数可以简单描述为：**存储位置 = f(关键字)** ，这个函数f一般称为哈希函数，这个函数的设计好坏会直接影响到哈希表的优劣。举个例子，比如我们要在哈希表中执行插入操作：
插入过程如下图所示
![哈希表数据插入过程](https://img-blog.csdnimg.cn/2018110221063296.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dvc2hpbWF4aWFvMQ==,size_16,color_FFFFFF,t_70)

查找操作同理，先通过哈希函数计算出实际存储地址，然后从数组中对应地址取出即可。

**哈希冲突**

然而万事无完美，**如果两个不同的元素，通过哈希函数得出的实际存储地址相同怎么办**？也就是说，当我们对某个元素进行哈希运算，得到一个存储地址，然后要进行插入的时候，发现已经被其他元素占用了，其实这就是所谓的**哈希冲突**，也叫**哈希碰撞**。前面我们提到过，哈希函数的设计至关重要，好的哈希函数会尽可能地保证 计算简单和散列地址分布均匀,但是，我们需要清楚的是，数组是一块连续的固定长度的内存空间，再好的哈希函数也不能保证得到的存储地址绝对不发生冲突。那么哈希冲突如何解决呢？哈希冲突的解决方案有多种:开放定址法（发生冲突，继续寻找下一块未被占用的存储地址），再散列函数法，链地址法，而HashMap即是采用了**链地址法**，也就是**数组+链表**的方式。

二、HashMap的实现原理

HashMap的主干是一个Entry数组。Entry是HashMap的基本组成单元，每一个Entry包含一个key-value键值对。（其实所谓Map其实就是保存了两个对象之间的映射关系的一种集合）

```java
//HashMap的主干数组，可以看到就是一个Entry数组，初始值为空数组{}，主干数组的长度一定是2的次幂。
//至于为什么这么做，后面会有详细分析。
transient Entry<K,V>[] table = (Entry<K,V>[]) EMPTY_TABLE;
123
```

Entry是HashMap中的一个静态内部类。代码如下

```java
    static class Entry<K,V> implements Map.Entry<K,V> {
        final K key;
        V value;
        Entry<K,V> next;//存储指向下一个Entry的引用，单链表结构
        int hash;//对key的hashcode值进行hash运算后得到的值，存储在Entry，避免重复计算

        /**
         * Creates new entry.
         */
        Entry(int h, K k, V v, Entry<K,V> n) {
            value = v;
            next = n
            key = k;
            hash = h;
        } 
```

所以，HashMap的总体结构如下：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20181102221702492.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dvc2hpbWF4aWFvMQ==,size_16,color_FFFFFF,t_70)

简单来说，**HashMap由数组+链表组成的**，数组是HashMap的主体，链表则是主要为了解决哈希冲突而存在的，如果定位到的数组位置不含链表（当前entry的next指向null）,那么查找，添加等操作很快，仅需一次寻址即可；如果定位到的数组包含链表，对于添加操作，其时间复杂度为O(n)，首先遍历链表，存在即覆盖，否则新增；对于查找操作来讲，仍需遍历链表，然后通过key对象的equals方法逐一比对查找。所以，性能考虑，**HashMap中的链表出现越少，性能才会越好。**

其他几个重要字段

```java
/**实际存储的key-value键值对的个数*/
transient int size;

/**阈值，当table == {}时，该值为初始容量（初始容量默认为16）；当table被填充了，也就是为table分配内存空间后，
threshold一般为 capacity*loadFactory。HashMap在进行扩容时需要参考threshold，后面会详细谈到*/
int threshold;

/**负载因子，代表了table的填充度有多少，默认是0.75
加载因子存在的原因：减少hash表的平均查找长度ASL，提高效率，用空间换取时间
所以加载因子默认为0.75，也就是说大小为16的HashMap，到了第13个元素，就会扩容成32。
加载因子为什么是0.75？可以变大或者变小吗？在理想的情况下，使用随机哈希码，节点出现的频率在hash桶中遵循泊松分布（在不同负载因子时，每个Entry处理哈希冲突时的各个链表长度出现的概率）当桶中元素到达8个的时候，概率已经变得非常小，也就是说用0.75作为加载因子，每个碰撞位置的链表长度超过８个是几乎不可能的。
即按照现有的hashcode的算法，0.75的时候时间和空间考虑上的最优解。往高了的话，提高了空间利用率，但是碰撞的出现概率也会变大，查询时间成本变大。往小了的话，空间利用率会变低并且需要更为频繁的扩容，且会降低但仍不能避免进行哈希冲突处理。

从时间考虑，最理想的情况时通过加载因子消除所有的hash冲突来减少平均查找长度，但是因为不可能，所以我们需要考虑当链表太长时的查找效率，因此超过8我们有红黑树来提高效率

HashMap底层为什么要在JDK8变成红黑树？
红黑树虽然本质上是一棵二叉查找树，但它在二叉查找树的基础上增加了着色和相关的性质使得红黑树相对平衡，从而保证了红黑树的查找、插入、删除的时间复杂度最坏为O(log n)。加快检索速率。在桶中元素数量较大时，查找效率比链表的O（n）要快


为什么不用avl树/二叉查找树？
红黑树相比avl树，在检索的时候效率其实差不多，都是通过平衡来二分查找。但对于插入删除等操作效率提高很多。红黑树不像avl树一样追求绝对的平衡，他允许局部很少的不完全平衡，这样对于效率影响不大，但省去了很多没有必要的调平衡操作，avl树调平衡有时候代价较大，所以效率不如红黑树。
二叉查找树本身并不考虑平衡性，因此效率介于log2(n)和log(n)之间，不稳定。

既然红黑树那么好，为啥hashmap不直接采用红黑树，而是当大于8个的时候才转换红黑树？
因为有加载因子的存在，因此正常情况下，是挺难到达8个，需要一定的数据量并且扩容到一定程度后才会有稍大的概率碰到
红黑树为了查询效率，终究需要考虑平衡性，而这就需要加入左旋、右旋等平衡操作，新增节点成本高，只有查询的成本在链表时大于红黑树，为了频繁的查询操作才需要牺牲一定的成本转换成红黑树。
如果元素小于8个，查询成本基本一致，链表新增成本低，选择链表
如果元素大于8个，平均查询长度相差1，链表查询成本高，大数据访问时性能影响开始明显，红黑树新增平衡成本不变，选择红黑树

HashMap在JDK1.8及以后的版本中引入了红黑树结构，若桶中链表元素个数大于等于8时，链表转换成树结构；若桶中链表元素个数小于等于6时，树结构还原成链表。因为红黑树的平均查找长度是log 2(n)，长度为8的时候，平均查找长度为3，如果继续使用链表，平均查找长度为8/2=4，这才有转换为树的必要。链表长度如果是小于等于6，6/2=3，虽然速度也很快的，但是转化为树结构和生成树的时间并不会太短。

还有选择6和8，中间有个差值7可以有效防止链表和树频繁转换。假设一下，如果设计成链表个数超过8则链表转换成树结构，链表个数小于8则树结构转换成链表，如果一个HashMap不停的插入、删除元素，链表个数在8左右徘徊，就会频繁的发生树转链表、链表转树，效率会很低。


*/
final float loadFactor;

/**HashMap被改变的次数，由于HashMap非线程安全，在对HashMap进行迭代时，
如果期间其他线程的参与导致HashMap的结构发生变化了（比如put，remove等操作），
需要抛出异常ConcurrentModificationException*/
transient int modCount;
```

HashMap有4个构造器，其他构造器如果用户没有传入initialCapacity 和loadFactor这两个参数，会使用默认值

initialCapacity默认为16，loadFactory默认为0.75

我们看下其中一个

```java
public HashMap(int initialCapacity, float loadFactor) {
　　　　　//此处对传入的初始容量进行校验，最大不能超过MAXIMUM_CAPACITY = 1<<30(230)
        if (initialCapacity < 0)
            throw new IllegalArgumentException("Illegal initial capacity: " +
                                               initialCapacity);
        if (initialCapacity > MAXIMUM_CAPACITY)
            initialCapacity = MAXIMUM_CAPACITY;
        if (loadFactor <= 0 || Float.isNaN(loadFactor))
            throw new IllegalArgumentException("Illegal load factor: " +
                                               loadFactor);

        this.loadFactor = loadFactor;
        threshold = initialCapacity;
　　　　　
        init();//init方法在HashMap中没有实际实现，不过在其子类如 linkedHashMap中就会有对应实现
    }

```

从上面这段代码我们可以看出，在常规构造器中，没有为数组table分配内存空间（有一个入参为指定Map的构造器例外），**而是在执行put操作的时候才真正构建table数组**

OK,接下来我们来看看put操作的实现

```java
public V put(K key, V value) {
        //如果table数组为空数组{}，进行数组填充（为table分配实际内存空间），入参为threshold，
        //此时threshold为initialCapacity 默认是1<<4(24=16)
        if (table == EMPTY_TABLE) {
            inflateTable(threshold);
        }
       //如果key为null，存储位置为table[0]或table[0]的冲突链上
        if (key == null)
            return putForNullKey(value);
        int hash = hash(key);//对key的hashcode进一步计算，确保散列均匀
        int i = indexFor(hash, table.length);//获取在table中的实际位置
        for (Entry<K,V> e = table[i]; e != null; e = e.next) {
        //如果该对应数据已存在，执行覆盖操作。用新value替换旧value，并返回旧value
            Object k;
            if (e.hash == hash && ((k = e.key) == key || key.equals(k))) {
                V oldValue = e.value;
                e.value = value;
                e.recordAccess(this);
                return oldValue;
            }
        }
        modCount++;//保证并发访问时，若HashMap内部结构发生变化，快速响应失败
        addEntry(hash, key, value, i);//新增一个entry
        return null;
    }

```

**inflateTable这个方法用于为主干数组table在内存中分配存储空间**，通过roundUpToPowerOf2(toSize)可以确保capacity为大于或等于toSize的最接近toSize的二次幂，比如toSize=13,则capacity=16;to_size=16,capacity=16;to_size=17,capacity=32.

```java
private void inflateTable(int toSize) {
        int capacity = roundUpToPowerOf2(toSize);//capacity一定是2的次幂
        /**此处为threshold赋值，取capacity*loadFactor和MAXIMUM_CAPACITY+1的最小值，
        capaticy一定不会超过MAXIMUM_CAPACITY，除非loadFactor大于1 */
        threshold = (int) Math.min(capacity * loadFactor, MAXIMUM_CAPACITY + 1);
        table = new Entry[capacity];
        initHashSeedAsNeeded(capacity);
    }
```

roundUpToPowerOf2中的这段处理使得数组长度一定为2的次幂，Integer.highestOneBit是用来获取最左边的bit（其他bit位为0）所代表的数值.

```java
 private static int roundUpToPowerOf2(int number) {
        // assert number >= 0 : "number must be non-negative";
        return number >= MAXIMUM_CAPACITY
                ? MAXIMUM_CAPACITY
                : (number > 1) ? Integer.highestOneBit((number - 1) << 1) : 1;
    }
```

hash函数

```java
/**这是一个神奇的函数，用了很多的异或，移位等运算
对key的hashcode进一步进行计算以及二进制位的调整等来保证最终获取的存储位置尽量分布均匀*/
final int hash(Object k) {
        int h = hashSeed;
        if (0 != h && k instanceof String) {
            return sun.misc.Hashing.stringHash32((String) k);
        }

        h ^= k.hashCode();

        h ^= (h >>> 20) ^ (h >>> 12);
        return h ^ (h >>> 7) ^ (h >>> 4);
    }
```

以上hash函数计算出的值，通过indexFor进一步处理来获取实际的存储位置

```java
/**
* 返回数组下标
*/
        static int indexFor(int h, int length) {
        return h & (length-1);
    }
```

> 我们计算出hash值后由h & (length-1)来确定
> 作用在于限制算出来的hash能在数组length下存储，可以不是2的次幂，但是我们设计hashcode的时候已经很难避免不同对象同一个hash值了，为了hash更具有他通过计算高速定位的作用，我们应该做到在数组范围内不同hash值要有不同的的地方存储，不能使得不同的对象不同的hash值还因为存储问题需要冲突处理，若是出于节省数组空间的角度，最高位固定，从数组整体长度来看，后面的也减少不了太多，而若是最高位变0则又是基于2的倍数的）
> 这也是由定义到代码到结果的，单纯研究代码也是可以有不同的结果，得不出这样做的意义
> 当然，length是2还和resize时对于oldcap计算得到得index的复用在nextab中复用减少计算量的作用，这是能直接通过代码看到的，但是我仍觉得上面的原因为需要为2的次幂的主要原因，因为这是hash表设计规范时的定义，复用只是多出来的红利。

h&（length-1）保证获取的index一定在数组范围内，举个例子，默认容量16，length-1=15，h=18,转换成二进制计算为index=2。位运算对计算机来说，性能更高一些（HashMap中有大量位运算）

所以最终存储位置的确定流程是这样的：

> **我们使用的是对象的hashcode，并在计算出存储下标的时候，再将该Key对象与计算出的存储下表的Entry元素与其链表元素进行equals比较，不同再加入链表或Entry数组中**
>
> key不能为基本数据类型，则是因为基本数据类型不能调用其hashcode()方法和equals()方法，进行比较，所以HashMap集合的key只能为引用数据类型，不能为基本数据类型，可以使用基本数据类型的包装类，例如Integer Double等。

![HashMap如何确定元素位置](https://img-blog.csdnimg.cn/20181102214046362.png)

再来看看addEntry的实现：

```java
void addEntry(int hash, K key, V value, int bucketIndex) {
        if ((size >= threshold) && (null != table[bucketIndex])) {
            resize(2 * table.length);//当size超过临界阈值threshold，并且即将发生哈希冲突时进行扩容
            hash = (null != key) ? hash(key) : 0;
            bucketIndex = indexFor(hash, table.length);
        }

        createEntry(hash, key, value, bucketIndex);
    }
```

通过以上代码能够得知，**当发生哈希冲突并且size大于阈值的时候，需要进行数组扩容，扩容时，需要新建一个长度为之前数组2倍的新的数组，然后将当前的Entry数组中的元素全部传输过去，扩容后的新数组长度为之前的2倍，所以扩容相对来说是个耗资源的操作。**

**三、为何HashMap的数组长度一定是2的次幂？**

我们来继续看上面提到的resize方法

```java
final Node<K,V>[] resize() {
        Node<K,V>[] oldTab = table;
        int oldCap = (oldTab == null) ? 0 : oldTab.length;
        int oldThr = threshold;
        int newCap, newThr = 0;
        if (oldCap > 0) {
            if (oldCap >= MAXIMUM_CAPACITY) {
                threshold = Integer.MAX_VALUE;
                return oldTab;
            }
            else if ((newCap = oldCap << 1) < MAXIMUM_CAPACITY &&
                     oldCap >= DEFAULT_INITIAL_CAPACITY)
                newThr = oldThr << 1; // double threshold  旧数组的长度乘以二
        }
        else if (oldThr > 0) // initial capacity was placed in threshold
            newCap = oldThr;
        else {               // zero initial threshold signifies using defaults
            newCap = DEFAULT_INITIAL_CAPACITY;
            newThr = (int)(DEFAULT_LOAD_FACTOR * DEFAULT_INITIAL_CAPACITY);
        }
        if (newThr == 0) {
            float ft = (float)newCap * loadFactor;
            newThr = (newCap < MAXIMUM_CAPACITY && ft < (float)MAXIMUM_CAPACITY ?
                      (int)ft : Integer.MAX_VALUE);
        }
        threshold = newThr;
        @SuppressWarnings({"rawtypes","unchecked"})
    	// 将旧数组的值的引用放入新数组中
        Node<K,V>[] newTab = (Node<K,V>[])new Node[newCap];
        table = newTab;
        if (oldTab != null) {
            for (int j = 0; j < oldCap; ++j) {
                Node<K,V> e;
                if ((e = oldTab[j]) != null) {
                    oldTab[j] = null;
                    if (e.next == null)
                        newTab[e.hash & (newCap - 1)] = e;
                    else if (e instanceof TreeNode)
                        ((TreeNode<K,V>)e).split(this, newTab, j, oldCap);
                    else { // preserve order
                        Node<K,V> loHead = null, loTail = null;
                        Node<K,V> hiHead = null, hiTail = null;
                        Node<K,V> next;
                        do {
                            next = e.next;
                            // 若是算出的hash值的oldCap的最高二进制位为1，则用e.hash & (newCap - 1)的结果应为j（j为e.hash & (oldCap - 1)） + oldCap，否则仍为j；
                            //关于因为是最高位变为1，能否依据旧的index值进行复用？因为每一个桶中的元素都不一样，因此其hashcode的最高位可能不一样，因此每一个都需要重新计算确定，不能直接利用index值，仍需要重新计算，但是因为是e.hash & (newCap - 1)所以与oldCap按位与后可以拿来作为新的数组索引的基本值使用，但是还是要重新用对象的hash值与运算，其实若不是2的倍数，直接与新的cap按位与放入数组也可。
                            
                            if ((e.hash & oldCap) == 0) {
                                if (loTail == null)
                                    loHead = e;
                                else
                                    loTail.next = e;
                                loTail = e;
                            }
                            else {
                                if (hiTail == null)
                                    hiHead = e;
                                else
                                    hiTail.next = e;
                                hiTail = e;
                            }
                        } while ((e = next) != null);
                        if (loTail != null) {
                            loTail.next = null;
                            newTab[j] = loHead;
                        }
                        if (hiTail != null) {
                            hiTail.next = null;
                            newTab[j + oldCap] = hiHead;
                        }
                    }
                }
            }
        }
        return newTab;
    }
```

这个方法将老数组中的数据逐个链表地遍历，扔到新的扩容后的数组中，我们的数组索引位置的计算是通过 对key值的hashcode进行hash扰乱运算后，再通过和 length-1进行位运算得到最终数组索引位置。

数组长度保持2的次幂，length-1的低位都为1，会使得获得的数组索引index更加均匀，增加h的辨识度，减少hash冲突处理



![在这里插入图片描述](https://img-blog.csdnimg.cn/20181102223421180.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dvc2hpbWF4aWFvMQ==,size_16,color_FFFFFF,t_70)
我们看到，上面的&运算，高位是不会对结果产生影响的（hash函数采用各种位运算可能也是为了使得低位更加散列），我们只关注低位bit，如果低位全部为1，那么对于h低位部分来说，任何一位的变化都会对结果产生影响，也就是说，要得到index=21这个存储位置，h的低位只有这一种组合。这也是数组长度设计为必须为2的次幂的原因。
![在这里插入图片描述](https://img-blog.csdnimg.cn/2018110222343145.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dvc2hpbWF4aWFvMQ==,size_16,color_FFFFFF,t_70)
如果不是2的次幂，也就是低位不是全为1此时，要使得index=21，h的低位部分不再具有唯一性了，哈希冲突的几率会变的更大

**get方法**：

```java
 public V get(Object key) {
　　　　 //如果key为null,则直接去table[0]处去检索即可。
        if (key == null)
            return getForNullKey();
        Entry<K,V> entry = getEntry(key);
        return null == entry ? null : entry.getValue();
 }
1234567
```

get方法通过key值返回对应value，如果key为null，直接去table[0]处检索。我们再看一下getEntry这个方法

```java
final Entry<K,V> getEntry(Object key) {
            
        if (size == 0) {
            return null;
        }
        //通过key的hashcode值计算hash值
        int hash = (key == null) ? 0 : hash(key);
        //indexFor (hash&length-1) 获取最终数组索引，然后遍历链表，通过equals方法比对找出对应记录
        for (Entry<K,V> e = table[indexFor(hash, table.length)];
             e != null;
             e = e.next) {
            Object k;
            if (e.hash == hash && 
                ((k = e.key) == key || (key != null && key.equals(k))))
                return e;
        }
        return null;
    }    
123456789101112131415161718
```

可以看出，get方法的实现相对简单，key(hashcode)–>hash–>indexFor–>最终索引位置，找到对应位置table[i]，再查看是否有链表，遍历链表，通过key的equals方法比对查找对应的记录。要注意的是，有人觉得上面在定位到数组位置之后然后遍历链表的时候，e.hash == hash这个判断没必要，仅通过equals判断就可以。其实不然，试想一下，如果传入的key对象重写了equals方法却没有重写hashCode，而恰巧此对象定位到这个数组位置，如果仅仅用equals判断可能是相等的，但其hashCode和当前对象不一致，这种情况，根据Object的hashCode的约定，不能返回当前对象，而应该返回null，后面的例子会做出进一步解释。

**四、重写equals方法需同时重写hashCode方法**

最后我们再聊聊老生常谈的一个问题，各种资料上都会提到，“重写equals时也要同时覆盖hashcode”，我们举个小例子来看看，如果重写了equals而不重写hashcode会发生什么样的问题

```java
public class MyTest {
    private static class Person{
        int idCard;
        String name;

        public Person(int idCard, String name) {
            this.idCard = idCard;
            this.name = name;
        }
        @Override
        public boolean equals(Object o) {
            if (this == o) {
                return true;
            }
            if (o == null || getClass() != o.getClass()){
                return false;
            }
            Person person = (Person) o;
            //两个对象是否等值，通过idCard来确定
            return this.idCard == person.idCard;
        }

    }
    public static void main(String []args){
        HashMap<Person,String> map = new HashMap<Person, String>();
        Person person = new Person(1234,"乔峰");
        //put到hashmap中去
        map.put(person,"天龙八部");
        //get取出，从逻辑上讲应该能输出“天龙八部”
        System.out.println("结果:"+map.get(new Person(1234,"萧峰")));
    }
}

实际输出结果：null
1234567891011121314151617181920212223242526272829303132333435
```

如果我们已经对HashMap的原理有了一定了解，这个结果就不难理解了。尽管我们在进行get和put操作的时候，使用的key从逻辑上讲是等值的（通过equals比较是相等的），但由于没有重写hashCode方法，所以put操作时，key(hashcode1)–>hash–>indexFor–>最终索引位置 ，而通过key取出value的时候 key(hashcode1)–>hash–>indexFor–>最终索引位置，由于hashcode1不等于hashcode2，导致没有定位到一个数组位置而返回逻辑上错误的值null（也有可能碰巧定位到一个数组位置，但是也会判断其entry的hash值是否相等，上面get方法中有提到。）

所以，在重写equals的方法的时候，必须注意重写hashCode方法，同时还要保证通过equals判断相等的两个对象，调用hashCode方法要返回同样的整数值。而如果equals判断不相等的两个对象，其hashCode可以相同（只不过会发生哈希冲突，应尽量避免）。

**五、JDK1.8中HashMap的性能优化**

假如一个数组槽位上链上数据过多（即拉链过长的情况）导致性能下降该怎么办？
JDK1.8在JDK1.7的基础上针对增加了红黑树来进行优化。即当链表超过8时，链表就转换为红黑树，利用红黑树快速增删改查的特点提高HashMap的性能，其中会用到红黑树的插入、删除、查找等算法。
关于这方面的探讨我们以后的文章再做说明。
**附：HashMap put方法逻辑图（JDK1.8）**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20181105181728652.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dvc2hpbWF4aWFvMQ==,size_16,color_FFFFFF,t_70)

 

### 遍历hashMap

**一、使用迭代器**

第一种:

```java
　　Map map = new HashMap();
　　Iterator iter = map.entrySet().iterator();
　　while (iter.hasNext()) {
　　Map.Entry entry = (Map.Entry) iter.next();//这里实现Map.Entry（Map的内部接口）的是Node（HashMap的内部类）
　　Object key = entry.getKey();
　　Object val = entry.getValue();
　　}
```

 效率高



第二种:

 

```
　　Map map = new HashMap();
　　Iterator iter = map.keySet().iterator();

　　while (iter.hasNext()) {
　　Object key = iter.next();
　　Object val = map.get(key);
　　}
```

 效率低

 

**二、for each 遍历**

第一种：

```java
Map<String, String> map = new HashMap<String, String>();

for (String key : map.keySet()) {

	map.get(key);

}
```

第二种：

```java
Map<String, String> map = new HashMap<String, String>();



for (Entry<String, String> entry : map.entrySet()) {
	entry.getKey();
	entry.getValue();

}
```



## 二、Hashtable简介

 

   Hashtable同样是基于哈希表实现的，同样每个元素是一个key-value对，其内部也是通过单链表解决冲突问题，容量不足（超过了阀值）时，同样会自动增长。

   Hashtable也是JDK1.0引入的类，是线程安全的，能用于多线程环境中。

   Hashtable同样实现了Serializable接口，它支持序列化，实现了Cloneable接口，能被克隆。

   Hashtable和HashMap比较相似，感兴趣的朋友可以看“[Hashtable源码剖析](http://blog.csdn.net/ns_code/article/details/36034955)”这篇博客：http://blog.csdn.net/ns_code/article/details/36191279

下面主要介绍一下HashTable和HashMap区别

 

## 三、HashTable和HashMap区别

###    1、继承的父类不同

Hashtable继承自Dictionary类，而HashMap继承自AbstractMap类。但二者都实现了Map接口。

###    2、线程安全性不同

 	javadoc中关于hashmap的一段描述如下：此实现不是同步的。如果多个线程同时访问一个哈希映射，而其中至少一个线程从结构上修改了该映射，则它必须保持外部同步。

​	shtable 中的方法是Synchronize的，而HashMap中的方法在缺省情况下是非Synchronize的。在多线程并发的环境下，可以直接使用Hashtable，不需要自己为它的方法实现同步，但使用HashMap时就必须要自己增加同步处理。（结构上的修改是指添加或删除一个或多个映射关系的任何操作；仅改变与实例已经包含的键关联的值不是结构上的修改。）这一般通过对自然封装该映射的对象进行同步操作来完成。如果不存在这样的对象，则应该使用 [Collections.synchronizedMap](http://write.blog.csdn.net/postedit/51556314#synchronizedMap(java.util.Map)) 方法来“包装”该映射。最好在创建时完成这一操作，以防止对映射进行意外的非同步访问，如下所示：

   Map m = Collections.synchronizedMap(new HashMap(...));

   Hashtable 线程安全很好理解，因为它每个方法中都加入了Synchronize。这里我们分析一下HashMap为什么是线程不安全的：

   HashMap底层是一个Entry数组，当发生hash冲突的时候，hashmap是采用链表的方式来解决的，在对应的数组位置存放链表的头结点。对链表而言，新加入的节点会从头结点加入。

我们来分析一下多线程访问：

 1）在hashmap做put操作的时候会调用下面方法

```java
// 新增Entry。将“key-value”插入指定位置，bucketIndex是位置索引。   

public void addEntry(int hash, K key, V value, int bucketIndex) { 

    // 保存“bucketIndex”位置的值到“e”中   

    Entry<K,V> e = table[bucketIndex];   

    // 设置“bucketIndex”位置的元素为“新Entry”，   

    // 设置“e”为“新Entry的下一个节点”   

    table[bucketIndex] = **new Entry<K,V>(hash, key, value, e);**   

    // 若HashMap的实际大小 不小于 “阈值”，则调整HashMap的大小   

    if (size++ >= threshold)   

     resize(2 * table.length);   

} 
```

 

   在hashmap做put操作的时候会调用到以上的方法。现在假如A线程和B线程同时对同一个数组位置调用addEntry，两个线程会同时得到现在的头结点，然后A写入新的头结点之后，B也写入新的头结点，那B的写入操作就会覆盖A的写入操作造成A的写入操作丢失

（2）删除键值对的代码

   当多个线程同时操作同一个数组位置的时候，也都会先取得现在状态下该位置存储的头结点，然后各自去进行计算操作，之后再把结果写会到该数组位置去，其实写回的时候可能其他的线程已经就把这个位置给修改过了，就会覆盖其他线程的修改

   （3）addEntry中当加入新的键值对后键值对总数量超过门限值的时候会调用一个resize操作，代码如下：

   这个操作会新生成一个新的容量的数组，然后对原数组的所有键值对重新进行计算和写入新的数组，之后指向新生成的数组。

 

   当多个线程同时检测到总数量超过门限值的时候就会同时调用resize操作，各自生成新的数组并rehash后赋给该map底层的数组table，结果最终只有最后一个线程生成的新数组被赋给table变量，其他线程的均会丢失。而且当某些线程已经完成赋值而其他线程刚开始的时候，就会用已经被赋值的table作为原始数组，这样也会有问题。

###    3、是否提供contains方法

 

   HashMap把Hashtable的contains方法去掉了，改成containsValue和containsKey，因为contains方法容易让人引起误解。

   Hashtable则保留了contains，containsValue和containsKey三个方法，其中contains和containsValue功能相同。

###    4、key和value是否允许null值

   其中key和value都是对象，并且不能包含重复key，但可以包含重复的value。

   通过上面的ContainsKey方法和ContainsValue的源码我们可以很明显的看出：

 	**Hashtable中，key和value都不允许出现null值。**但是如果在Hashtable中有类似put(null,null)的操作，编译同样可以通过，因为key和value都是Object类型，但运行时会抛出NullPointerException异常，这是JDK的规范规定的。
 	**HashMap中，null可以作为键，这样的键只有一个,可以有一个或多个键所对应的值为null。**当get()方法返回null值时，可能是 HashMap中没有该键，也可能使该键所对应的值为null。因此，在HashMap中不能由get()方法来判断HashMap中是否存在某个键， 而应该用containsKey()方法来判断。

###    5、两个遍历方式的内部实现上不同

   Hashtable、HashMap都使用了 Iterator。而由于历史原因，Hashtable还使用了Enumeration的方式 。

###    6、hash值不同

   哈希值的使用不同，HashTable直接使用对象的hashCode。而HashMap重新计算hash值。

   hashCode是jdk根据对象的地址或者字符串或者数字算出来的int类型的数值。

   Hashtable计算hash值，直接用key的hashCode()，而HashMap重新计算了key的hash值，Hashtable在求hash值对应的位置索引时，用取模运算，而HashMap在求位置索引时，则用与运算，且这里一般先用hash&0x7FFFFFFF后，再对length取模，&0x7FFFFFFF的目的是为了将负的hash值转化为正值，因为hash值有可能为负数，而&0x7FFFFFFF后，只有符号外改变，而后面的位都不变。

>  “ 两个对象值相同 (x.equals (y) == true) ，但却可有不同的 hash code"？
>
>  Java 对于 eqauls 方法和 hashCode 方法是这样规定的：
>    (1) 如果两个对象相同（equals 方法返回 true），那么它们的 hashCode 值一定要相同；
>    (2) 如果两个对象的 hashCode 相同，它们并不一定相同。
>
>  若是没有重写equals，equals若是没重写便是引用==即比较两个变量引用的是否为同一地址，而hashCode的判断则依据的是对象的多个属性，**若是改变对象的属性时，不能保证hashCode在equals时相同则会有相同的对象可以出现在 Set/Map 集合中**，造成冲突难以处理，效率低下。



###    7、内部实现使用的数组初始化和扩容方式不同

   HashTable在不指定容量的情况下的默认容量为11，而HashMap为16，Hashtable不要求底层数组的容量一定要为2的整数次幂，而HashMap则要求一定为2的整数次幂。
   Hashtable扩容时，将容量变为原来的2倍加1，而HashMap扩容时，将容量变为原来的2倍。

   Hashtable和HashMap它们两个内部实现方式的数组的初始大小和扩容的方式。HashTable中hash数组默认大小是11，增加的方式是 old*2+1。

## 四、WeakHashMap

**什么是WeakHashMap？**

* 一个具有弱引用结构的hashMap

**为什么需要WeakHashMap**

WeakHashMap正是由于使用的是弱引用，因此它的对象可能被随时回收。更直观的说，当使用 WeakHashMap 时，即使没有删除任何元素，它的尺寸、get方法也可能不一样。

有一种场景，最喜欢这种飘忽不定、一言不合就删除的东西。那就是缓存。**在缓存场景下，由于内存是有限的，不能缓存所有对象，因此就需要一定的删除机制，淘汰掉一些对象。**（gc时若只有弱引用的会被清除，若还有其他的引用则该次不会）

### **WeakHashMap工作原理**

![img](https://pics2.baidu.com/feed/78310a55b319ebc45ccb92de194a6afa1f1716bd.jpeg?token=5bdf601ac07972f93fd279e9ff0b0398)

从这里我们可以看到其内部的Entry继承了WeakReference，也就是弱引用，所以就具有了弱引用的特点。不过还要注意一点，那就是ReferenceQueue，他的作用是GC会**清理掉对象之后，引用对象会被放到ReferenceQueue中**（虚引用：主要用于跟踪垃圾回收过程和堆外资源的释放）。

Entry对象在堆中被清理了，但是hashmap的数组/链表可能还有他的引用，需要借助ReferenceQueue找到并清除。

WeakHashMap内部有一个expungeStaleEntries函数，在这个函数内部实现移除其内部不用的entry从而达到的自动释放内存的目的。因此我们每次访问WeakHashMap的时候，都会调用这个expungeStaleEntries函数清理一遍。这也就是为什么前两次调用WeakHashMap的size()方法有可能不一样的原因。我们可以看看是如何实现的：

![img](https://pics0.baidu.com/feed/cefc1e178a82b901c8a2b70be8e10c713912ef6a.jpeg?token=52471c453b8d7b3bf20990ac30441598)

WeakHashMap的增删改查操作都会直接或者间接的调用expungeStaleEntries()方法，达到及时清除过期entry的目的。

## 五、LinkedHashMap

**LinkedHashMap的特点：**

1. LinkedHashMap继承了HashMap ，实现了Clonable ，serialiable（可序列化） ， map接口；
2. 2.提供了AccessOrder参数，用来指定LinkedHashMap的排序方式，accessOrder =false -> 插入顺序进行排序，put时会按顺序加入tail ， accessOrder = true -> 访问顺序进行排序，get时会加入tail。

```java
 public class LinkedHashMap<K,V> 
    extends HashMap<K,V>
    implements Map<K,V>
{
       /**
     * The head (eldest) of the doubly linked list.
     */
    transient LinkedHashMap.Entry<K,V> head;

    /**
     * The tail (youngest) of the doubly linked list.
     */
    transient LinkedHashMap.Entry<K,V> tail;

    /**
     * The iteration ordering method for this linked hash map: <tt>true</tt>
     * for access-order, <tt>false</tt> for insertion-order.
     *
     * @serial
     */
    final boolean accessOrder;

}
```

2. HashMap中准备了一些没有实现的方法，使得在被继承时子类可以依据自己的特性而实现。

```JAVA
// Callbacks to allow LinkedHashMap post-actions
// 回调使得LinkedHashMap的特性能被支持
void afterNodeAccess(Node<K,V> p) { }
void afterNodeInsertion(boolean evict) { }
void afterNodeRemoval(Node<K,V> p) { }
```

3. 并且重写了几个较为关键的方法

```java
Node<K,V> newNode(int hash, K key, V value, Node<K,V> e) {}
public V get(Object key) {}
```

3. LinkedHashMap引用的是父类（HashMap的方法）的put方法

```java
public V put(K key, V value) {
    return putVal(hash(key), key, value, false, true);
}
final V putVal(int hash, K key, V value, boolean onlyIfAbsent,
                   boolean evict) {
        Node<K,V>[] tab; Node<K,V> p; int n, i;
        if ((tab = table) == null || (n = tab.length) == 0)
            n = (tab = resize()).length;
        if ((p = tab[i = (n - 1) & hash]) == null)
            // newNode被LinkedHashMap重写了，实例化的为LinkedHashMap.Entry，并设为tail；
            tab[i] = newNode(hash, key, value, null);
        else {
            Node<K,V> e; K k;
            if (p.hash == hash &&
                ((k = p.key) == key || (key != null && key.equals(k))))
                e = p;
            else if (p instanceof TreeNode)
                e = ((TreeNode<K,V>)p).putTreeVal(this, tab, hash, key, value);
            else {
                for (int binCount = 0; ; ++binCount) {
                    if ((e = p.next) == null) {
                        p.next = newNode(hash, key, value, null);
                        if (binCount >= TREEIFY_THRESHOLD - 1) // -1 for 1st
                            treeifyBin(tab, hash);
                        break;
                    }
                    if (e.hash == hash &&
                        ((k = e.key) == key || (key != null && key.equals(k))))
                        break;
                    p = e;
                }
            }
            if (e != null) { // existing mapping for key
                V oldValue = e.value;
                if (!onlyIfAbsent || oldValue == null)
                    e.value = value;
                afterNodeAccess(e);
                return oldValue;
            }
        }
        ++modCount;
        if (++size > threshold)
            resize();
        afterNodeInsertion(evict);
        return null;
    }
```

4. get时会依据accessOrder进行是否访问顺序调整的判断

```java
public V get(Object key) {
    Node<K,V> e;
    if ((e = getNode(hash(key), key)) == null)
        return null;
    if (accessOrder)
        afterNodeAccess(e);
    return e.value;
}
void afterNodeAccess(Node<K,V> e) { // move node to last
        LinkedHashMap.Entry<K,V> last;
        if (accessOrder && (last = tail) != e) {
            LinkedHashMap.Entry<K,V> p =
                (LinkedHashMap.Entry<K,V>)e, b = p.before, a = p.after;
            p.after = null;
            // 移除双向链表中原来存在的该节点
            if (b == null)
                head = a;
            else
                b.after = a;
            if (a != null)
                a.before = b;
            else
                last = b;
            if (last == null)
                head = p;
            else {
                p.before = last;
                last.after = p;
            }
            // 加入尾部
            tail = p;
            ++modCount;
        }
    }
```

5. LinkedHashMap的**containsValue**方法：首先处理value为null的情况， 其次它不像HashMap一样遍历整个数组， 而是通过遍历双线链表header来查找value；

   LinkedHashMap可以保存元素的插入顺序，**顺序有两种方式一种是按照插入顺序排序，一种按照访问做排序**。默认以插入顺序排序，性能比HashMap略低，线程也是不安全的。



 # 3、 Queue



##  1. 前言

BlockingQueue即阻塞队列，它是基于ReentrantLock，依据它的基本原理，我们可以实现Web中的长连接聊天功能，当然其最常用的还是用于实现生产者与消费者模式，大致如下图所示：

![img](https:////upload-images.jianshu.io/upload_images/4180398-89f0d2693361656e.png?imageMogr2/auto-orient/strip|imageView2/2/w/873/format/webp)

> 在Java中，BlockingQueue是一个接口，它的实现类有ArrayBlockingQueue、DelayQueue、 LinkedBlockingDeque、LinkedBlockingQueue、PriorityBlockingQueue、SynchronousQueue等，它们的区别主要体现在存储结构上或对元素操作上的不同，但是对于take与put操作的原理，却是类似的。

## 2. 阻塞与非阻塞

### **入队**

> offer(E e)：如果队列没满，立即返回true； 如果队列满了，立即返回false-->不阻塞
>
> put(E e)：如果队列满了，一直阻塞，直到队列不满了或者线程被中断-->阻塞
>
> offer(E e, long timeout, TimeUnit unit)：在队尾插入一个元素,，如果队列已满，则进入等待，直到出现以下三种情况：-->阻塞
>
> 被唤醒
>
> 等待时间超时
>
> 当前线程被中断

### **出队**

> poll()：如果没有元素，直接返回null；如果有元素，出队
>
> take()：如果队列空了，一直阻塞，直到队列不为空或者线程被中断-->阻塞
>
> poll(long timeout, TimeUnit unit)：如果队列不空，出队；如果队列已空且已经超时，返回null；如果队列已空且时间未超时，则进入等待，直到出现以下三种情况：
>
> 被唤醒
>
> 等待时间超时
>
> 当前线程被中断



## 3. LinkedBlockingQueue 源码分析

LinkedBlockingQueue是一个基于链表实现的可选容量的阻塞队列。队头的元素是插入时间最长的，队尾的元素是最新插入的。新的元素将会被插入到队列的尾部。 

LinkedBlockingQueue的容量限制是可选的，如果在初始化时没有指定容量，那么默认使用int的最大值作为队列容量。

### **底层数据结构**

LinkedBlockingQueue内部是使用链表实现一个队列的，但是却有别于一般的队列，在于该队列至少有一个节点，头节点不含有元素。结构图如下：



![img](https:////upload-images.jianshu.io/upload_images/4180398-d987601194c3e199.png?imageMogr2/auto-orient/strip|imageView2/2/w/835/format/webp)

### **原理**

LinkedBlockingQueue中维持两把锁，一把锁用于入队，一把锁用于出队，这也就意味着，同一时刻，只能有一个线程执行入队，其余执行入队的线程将会被阻塞；同时，可以有另一个线程执行出队，其余执行出队的线程将会被阻塞。换句话说，虽然入队和出队两个操作同时均只能有一个线程操作，但是可以一个入队线程和一个出队线程共同执行，也就意味着可能同时有两个线程在操作队列，那么为了维持线程安全，LinkedBlockingQueue使用一个AtomicInterger类型的变量表示当前队列中含有的元素个数，所以可以确保两个线程之间操作底层队列是线程安全的。

### 源码分析

LinkedBlockingQueue可以指定容量，内部维持一个队列，所以有一个头节点head和一个尾节点last，内部维持两把锁，一个用于入队，一个用于出队，还有锁关联的Condition对象。主要对象的定义如下：

> //容量，如果没有指定，该值为Integer.MAX_VALUE;
>
> private final int capacity;
>
> //当前队列中的元素
>
> private final AtomicInteger count =new AtomicInteger();
>
> //队列头节点，始终满足head.item==null
>
> transient Node head;
>
> //队列的尾节点，始终满足last.next==null
>
> private transient Node last;
>
> //用于出队的锁
>
> private final ReentrantLock takeLock =new ReentrantLock();
>
> //当队列为空时，保存执行出队的线程
>
> private final Condition notEmpty = takeLock.newCondition();
>
> //用于入队的锁
>
> private final ReentrantLock putLock =new ReentrantLock();
>
> //当队列满时，保存执行入队的线程
>
> private final Condition notFull = putLock.newCondition();

### put(E e)方法

put(E e)方法用于将一个元素插入到队列的尾部，其实现如下：

> public void put(E e)throws InterruptedException {
>
> //不允许元素为null
>
>   if (e ==null)
>
> throw new NullPointerException();
>
>   int c = -1;
>
>   //以当前元素新建一个节点
>
>   Node node =new Node(e);
>
>   final ReentrantLock putLock =this.putLock;
>
>   final AtomicInteger count =this.count;
>
>   //获得入队的锁
>
>   putLock.lockInterruptibly();
>
>   try {
>
> ​    //如果队列已满，那么将该线程加入到Condition的等待队列中
>
> ​    while (count.get() == capacity) {
>
> ​       notFull.await();
>
> ​    }
>
> ​    //将节点入队
>
> ​    enqueue(node);
>
> ​    //得到插入之前队列的元素个数
>
> ​    c = count.getAndIncrement();
>
> ​    //如果还可以插入元素，那么释放等待的入队线程
>
> ​    if (c +1 < capacity){
>
> ​       notFull.signal();
>
> ​    }
>
> }finally {
>
> //解锁
>
> ​    putLock.unlock();
>
>   }
>
> //通知出队线程队列非空
>
>   if (c ==0)
>
> signalNotEmpty();
>
> }

**3.1 具体入队与出队的原理图**：

图中每一个节点前半部分表示封装的数据x，后边的表示指向的下一个引用。



![img](https:////upload-images.jianshu.io/upload_images/4180398-ccb29b29b48e6192.png?imageMogr2/auto-orient/strip|imageView2/2/w/132/format/webp)

初始化之后，初始化一个数据为null，且head和last节点都是这个节点。

**3.2、入队两个元素过后**



![img](https:////upload-images.jianshu.io/upload_images/4180398-0d6b2ff6b3444abf.png?imageMogr2/auto-orient/strip|imageView2/2/w/436/format/webp)

**3.3、出队一个元素后**



![img](https:////upload-images.jianshu.io/upload_images/4180398-b482b1f470e2ad3a.png?imageMogr2/auto-orient/strip|imageView2/2/w/283/format/webp)

#### put方法总结： 

1. LinkedBlockingQueue不允许元素为null。 

2. 同一时刻，只能有一个线程执行入队操作，因为putLock在将元素插入到队列尾部时加锁了 

3. 如果队列满了，那么将会调用notFull的await()方法将该线程加入到Condition等待队列中。await()方法就会释放线程占有的锁，这将导致之前由于被锁阻塞的入队线程将会获取到锁，执行到while循环处，不过可能因为由于队列仍旧是满的，也被加入到条件队列中。 

4. 一旦一个出队线程取走了一个元素，并通知了入队等待队列中可以释放线程了，那么第一个加入到Condition队列中的将会被释放，那么该线程将会重新获得put锁，继而执行enqueue()方法，将节点插入到队列的尾部 

5. 然后得到插入一个节点之前的元素个数，如果队列中还有空间可以插入，那么就通知notFull条件的等待队列中的线程。 

6. 通知出队线程队列为空了，因为插入一个元素之前的个数为0，而插入一个之后队列中的元素就从无变成了有，就可以通知因队列为空而阻塞的出队线程了。

### E take()方法

take()方法用于得到队头的元素，在队列为空时会阻塞，知道队列中有元素可取。其实现如下：

> public E take() throws InterruptedException {
>
> ​    E x;
>
> ​    int c = -1;
>
> ​    final AtomicInteger count = this.count;
>
> ​    final ReentrantLock takeLock = this.takeLock;
>
> ​    //获取takeLock锁    
>
> ​     takeLock.lockInterruptibly();
>
> ​    try {
>
> ​      //如果队列为空，那么加入到notEmpty条件的等待队列中      
>
> ​      while (count.get() == 0) {
>
> ​        notEmpty.await();
>
> ​      }
>
> ​      //得到队头元素      
>
> ​       x = dequeue();
>
> ​      //得到取走一个元素之前队列的元素个数      
>
> ​        c = count.getAndDecrement();
>
> ​      //如果队列中还有数据可取，释放notEmpty条件等待队列中的第一个线程      
>
> ​        if (c > 1)
>
> ​        notEmpty.signal();
>
> ​    } finally {
>
> ​      takeLock.unlock();
>
> ​    }
>
> ​    //如果队列中的元素从满到非满，通知put线程    
>
> ​      if (c == capacity)
>
> ​      signalNotFull();
>
> ​    return x;
>
>   }

#### take方法总结:

当队列为空时，就加入到notEmpty(的条件等待队列中，当队列不为空时就取走一个元素，当取完发现还有元素可取时，再通知一下自己的伙伴（等待在条件队列中的线程）；最后，如果队列从满到非满，通知一下put线程。 

### remove()方法 

remove()方法用于删除队列中一个元素，如果队列中不含有该元素，那么返回false；有的话则删除并返回true。入队和出队都是只获取一个锁，而remove()方法需要同时获得两把锁，其实现如下：

> public boolean remove(Object o) {
>
> ​    //因为队列不包含null元素，返回false   
>
> ​     if (o == null) return false;
>
> ​    //获取两把锁    fullyLock();
>
> ​    try {
>
> ​      //从头的下一个节点开始遍历      
>
> ​      for (Node trail = head, p = trail.next;
>
> ​         p != null;
>
> ​         trail = p, p = p.next) {
>
> ​         //如果匹配，那么将节点从队列中移除，trail表示前驱节点        
>
> ​        if (o.equals(p.item)) {
>
> ​          unlink(p, trail);
>
> ​          return true;
>
> ​        }
>
> ​      }
>
> ​      return false;
>
> ​    } finally {
>
> ​      //释放两把锁     
>
> ​      fullyUnlock();
>
> ​    }
>
>   }

> 
> void fullyLock() {
>
> ​    putLock.lock();
>
> ​    takeLock.lock();
>
>   }

### 提问：为什么remove()方法同时需要两把锁? 

### LinkedBlockingQueue总结:

LinkedBlockingQueue是允许两个线程同时在两端进行入队或出队的操作的，但一端同时只能有一个线程进行操作，这是通过两把锁来区分的；

为了维持底部数据的统一，引入了AtomicInteger的一个count变量，表示队列中元素的个数。count只能在两个地方变化，一个是入队的方法（可以+1），另一个是出队的方法（可以-1），而AtomicInteger是原子安全的，所以也就确保了底层队列的数据同步。 

## 4. ArrayBlockingQueue源码分析

ArrayBlockingQueue底层是使用一个数组实现队列的，并且在构造ArrayBlockingQueue时需要指定容量，也就意味着底层数组一旦创建了，容量就不能改变了，因此ArrayBlockingQueue是一个容量限制的阻塞队列。因此，在队列全满时执行入队将会阻塞，在队列为空时出队同样将会阻塞。

ArrayBlockingQueue的重要字段有如下几个：

> ​    /** The queued items */ 
>
> ​     final Object[] items;
>
>    /** Main lock guarding all access */ 
>
> ​    final ReentrantLock lock;
>
>   /** Condition for waiting takes */  
>
>    private final Condition notEmpty;
>
>   /** Condition for waiting puts */  
>
>    private final Condition notFull;

### put(E e)方法

put(E e)方法在队列不满的情况下，将会将元素添加到队列尾部，如果队列已满，将会阻塞，直到队列中有剩余空间可以插入。该方法的实现如下：

> public void put(E e) throws InterruptedException {
>
> ​    //检查元素是否为null，如果是，抛出NullPointerException    
>
> ​    checkNotNull(e);
>
> ​    final ReentrantLock lock = this.lock;
>
> ​    //加锁    
>
> ​    lock.lockInterruptibly();
>
> ​    try {
>
> ​      //如果队列已满，阻塞，等待队列成为不满状态      
>
> ​      while (count == items.length)
>
> ​        notFull.await();
>
> ​      //将元素入队      
>
> ​      enqueue(e);
>
> ​    } finally {
>
> ​      lock.unlock();
>
> ​    }
>
>   }

### put方法总结:

1. ArrayBlockingQueue不允许元素为null 

2. ArrayBlockingQueue在队列已满时将会调用notFull的await()方法释放锁并处于阻塞状态 

3. 一旦ArrayBlockingQueue不为满的状态，就将元素入队

### **E take()方法**

take()方法用于取走队头的元素，当队列为空时将会阻塞，直到队列中有元素可取走时将会被释放。其实现如下：

>  public E take() throws InterruptedException {
>
> ​    final ReentrantLock lock = this.lock;
>
> ​    //首先加锁    
>
> ​     lock.lockInterruptibly();
>
> ​    try {
>
> ​      //如果队列为空，阻塞      
>
> ​      while (count == 0)
>
> ​        notEmpty.await();
>
> ​      //队列不为空，调用dequeue()出队      
>
> ​      return dequeue();
>
> ​    } finally {
>
> ​      //释放锁      
>
> ​     lock.unlock();
>
> ​    }
>
>   }

### take方法总结:

一旦获得了锁之后，如果队列为空，那么将阻塞；否则调用dequeue()出队一个元素。 

### ArrayBlockingQueue总结：

ArrayBlockingQueue的并发阻塞是通过ReentrantLock和Condition来实现的，ArrayBlockingQueue内部只有一把锁，意味着同一时刻只有一个线程能进行入队或者出队的操作。



## 5 总结

在上面分析LinkedBlockingQueue的源码之后，可以与ArrayBlockingQueue做一个比较。 

### ArrayBlockingQueue：

一个对象数组+一把锁+两个条件

入队与出队都用同一把锁

在只有入队高并发或出队高并发的情况下，因为操作数组，且不需要扩容，性能很高

采用了数组，必须指定大小，即容量有限

### LinkedBlockingQueue：

一个单向链表+两把锁+两个条件

两把锁，一把用于入队，一把用于出队，有效的避免了入队与出队时使用一把锁带来的竞争。

在入队与出队都高并发的情况下，性能比ArrayBlockingQueue高很多

采用了链表，最大容量为整数最大值，可看做容量无限

# 4、 Set

　Set具有与Collection完全一样的接口，因此没有任何额外的功能，不像前面有两个不同的List。实际上Set就是Collection,只是行为不同。(这是继承与多态思想的典型应用：表现不同的行为。)Set不保存重复的元素(至于如何判断元素相同则较为负责)

　　Set : 存入Set的每个元素都必须是唯一的，因为Set不保存重复元素。加入Set的元素必须定义equals()方法以确保对象的唯一性。Set与Collection有完全一样的接口。Set接口不保证维护元素的次序。

　　HashSet : 为快速查找设计的Set。存入HashSet的对象必须定义hashCode()。

　　TreeSet : 保存次序的Set, 底层为树结构。使用它可以从Set中提取有序的序列。

　　LinkedHashSet : 具有HashSet的查询速度，且内部使用链表维护元素的顺序(插入的次序)。于是在使用迭代器遍历Set时，结果会按元素插入的次序显示。