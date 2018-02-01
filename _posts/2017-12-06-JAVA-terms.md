---
layout: post
title: JAVA基础：并发，JVM和常见关键字解析
tags: [JAVA]
comments: true
---
* [JAVA基础](#java%E5%9F%BA%E7%A1%80)
  * [Object中有哪些方法](#object%E4%B8%AD%E6%9C%89%E5%93%AA%E4%BA%9B%E6%96%B9%E6%B3%95)
  * [ArrayList, LinkedList以及Vector的区别](#arraylist-linkedlist%E4%BB%A5%E5%8F%8Avector%E7%9A%84%E5%8C%BA%E5%88%AB)
  * [String, StringBuilder和StringBuffer的区别](#string-stringbuilder%E5%92%8Cstringbuffer%E7%9A%84%E5%8C%BA%E5%88%AB)
  * [Map, Set, List, Queue、Stack的特点及用法。](#map-set-list-queuestack%E7%9A%84%E7%89%B9%E7%82%B9%E5%8F%8A%E7%94%A8%E6%B3%95)
  * [JAVA的四种引用方式](#java%E7%9A%84%E5%9B%9B%E7%A7%8D%E5%BC%95%E7%94%A8%E6%96%B9%E5%BC%8F)
  * [JAVA中==和equals的区别](#java%E4%B8%AD%E5%92%8Cequals%E7%9A%84%E5%8C%BA%E5%88%AB)
  * [JAVA里面final的用法](#java%E9%87%8C%E9%9D%A2final%E7%9A%84%E7%94%A8%E6%B3%95)
* [JAVA的并发](#java%E7%9A%84%E5%B9%B6%E5%8F%91)
  * [volatile](#volatile)
  * [内部锁和显示锁](#%E5%86%85%E9%83%A8%E9%94%81%E5%92%8C%E6%98%BE%E7%A4%BA%E9%94%81)
  * [HashMap的低层实现和线程安全性](#hashmap%E7%9A%84%E4%BD%8E%E5%B1%82%E5%AE%9E%E7%8E%B0%E5%92%8C%E7%BA%BF%E7%A8%8B%E5%AE%89%E5%85%A8%E6%80%A7)
* [JVM](#jvm)
  * [JVM的四大组成部分](#jvm%E7%9A%84%E5%9B%9B%E5%A4%A7%E7%BB%84%E6%88%90%E9%83%A8%E5%88%86)
  * [JVM的类加载机制](#jvm%E7%9A%84%E7%B1%BB%E5%8A%A0%E8%BD%BD%E6%9C%BA%E5%88%B6)
  * [JVM的执行引擎](#jvm%E7%9A%84%E6%89%A7%E8%A1%8C%E5%BC%95%E6%93%8E)
  * [内存区](#%E5%86%85%E5%AD%98%E5%8C%BA)
  * [堆内存](#%E5%A0%86%E5%86%85%E5%AD%98)
  * [JAVA的内存分配](#java%E7%9A%84%E5%86%85%E5%AD%98%E5%88%86%E9%85%8D)
* [JAVA的内存回收gc](#java%E7%9A%84%E5%86%85%E5%AD%98%E5%9B%9E%E6%94%B6gc)
  * [内存回收的分类](#%E5%86%85%E5%AD%98%E5%9B%9E%E6%94%B6%E7%9A%84%E5%88%86%E7%B1%BB)
  * [内存回收的触发条件](#%E5%86%85%E5%AD%98%E5%9B%9E%E6%94%B6%E7%9A%84%E8%A7%A6%E5%8F%91%E6%9D%A1%E4%BB%B6)
  * [内存回收之finalize()](#%E5%86%85%E5%AD%98%E5%9B%9E%E6%94%B6%E4%B9%8Bfinalize)
  * [内存回收之内存标记](#%E5%86%85%E5%AD%98%E5%9B%9E%E6%94%B6%E4%B9%8B%E5%86%85%E5%AD%98%E6%A0%87%E8%AE%B0)
  * [内存回收之引用计数存在的问题](#%E5%86%85%E5%AD%98%E5%9B%9E%E6%94%B6%E4%B9%8B%E5%BC%95%E7%94%A8%E8%AE%A1%E6%95%B0%E5%AD%98%E5%9C%A8%E7%9A%84%E9%97%AE%E9%A2%98)
  * [内存回收之算法](#%E5%86%85%E5%AD%98%E5%9B%9E%E6%94%B6%E4%B9%8B%E7%AE%97%E6%B3%95)
  * [内存回收之内存泄漏](#%E5%86%85%E5%AD%98%E5%9B%9E%E6%94%B6%E4%B9%8B%E5%86%85%E5%AD%98%E6%B3%84%E6%BC%8F)
  * [内存回收之性能调优](#%E5%86%85%E5%AD%98%E5%9B%9E%E6%94%B6%E4%B9%8B%E6%80%A7%E8%83%BD%E8%B0%83%E4%BC%98)
  * [内存回收之具体过程](#%E5%86%85%E5%AD%98%E5%9B%9E%E6%94%B6%E4%B9%8B%E5%85%B7%E4%BD%93%E8%BF%87%E7%A8%8B)

## JAVA基础

### Object中有哪些方法
clone(), equals(), hashCode, toString(), notify()（可用于条件队列）, notifyAll()（可用于条件队列），wait()(可用于条件队列)，finalize(), getClass()

### ArrayList, LinkedList以及Vector的区别
ArrayList: 内部采用数组存储元素，支持高效随机访问，支持动态调整大小

LinkedList: 内部采用链表来存储元素，支持快速插入/删除元素，但不支持高效地随机访问

Vector: 可以看作线程安全版的ArrayList

### String, StringBuilder和StringBuffer的区别
String: 不可变的字符序列，若要向其中添加新字符需要创建一个新的String对象。这个就会涉及到gc的问题，在loop中，尽量避免使用string，因为
这个会导致频繁地创建和销毁对象。不过不可变变量不用担心多线程访问的时候被其他线程修改了值，造成了很奇怪的不可预知的行为。

StringBuilder: 可变字符序列，支持向其中添加新字符（无需创建新对象）

StringBuffer: 可以看作线程安全版的StringBuilder

### Map, Set, List, Queue、Stack的特点及用法。
Map: 映射，健值对，其中健是一个set

Set: 集合，不可重复且无序的元素集合

List: 有序序列

Queue: 先进先出队列

Stach：先进后出栈

### JAVA的四种引用方式
强引用：有对象与强引用关联， JVM就不会回收这个对象，宁可抛出OutOfMemory错误也不会回收这种对象。一般如果想要中断，可以显式地吧这个引用赋值给null，这样一来，JVM就会在合适的时候回收该对象。
~~~java
Object object = new Object();
String str = "hello";
~~~

软引用：可能还有用，但并非必须的对象。在内存溢出之前会被回收，一般可以用来作为缓存或者内存警告。
~~~java
Object obj = new Object();
SoftReference<Object> sf = new SoftReference<Object>(obj);
obj = null; //强引用的对象，当它为null的时候，需要回收
sf.get();//有时候会返回null，可以提供一种缓存的作用，在内存足够的时候，直接通过软引用取数值。在内存不够的时候，自动删除这部分缓存数据。
~~~

弱引用：非必须的对象，不保证不被JVM回收的对象，只会生存到下一次gc，在每次JVM进行gc的时候，会回收弱引用的对象。

虚引用：和弱引用类似，区别在于无法通过引用去到对象值，主要是用来检测这个对象是否已经从内存中删除。仅仅是标记被引用的对象在被回收时自动地被加入到引用队列中，也就是说可以通过虚引用队列知道哪些对象已经被回收了。我们可以利用这个来知道这个对象被收集器回收时收到一个系统通知。

### JAVA中==和equals的区别
"=="判断的是两个对象的内存地址是否一样，这个通常用来比较两个两个原始数据类型。equals方法是对象的内置方法，通常可以被重写用来更好
地比较两个对象。

### JAVA里面final的用法
final修饰类：不能被继承 （面向对象）

final修饰变量：变量的数值不能被更改

final修饰方法：方法不能被重写 （面向对象）

final修饰对象：值可以改变，对象的引用不能改变

## JAVA的并发

### volatile
可见性：被volatile变量是一种轻量级（不会阻塞）的同步共享变量，不会被缓存到CPU寄存器中，会立即被刷到主存中，而不会被保存在CPU的快速缓存中，以变量对保证不同的线程的可见行。当使用LOCK或synchronized的时候，解锁的时候也会吧变量立刻刷到主存中。所以我们可以得知，使用volatile可以将修改的变量强制地从工作内存存入主内存中，同时另一个线程会变得行无效，这样就强制让另一个线程从主存读取这个变量。

原子性：volatile 变量并不保证原子性。

有序性：禁止指令优化重排序。

我们可以借此看到一点, volatile只能保证变量的可见性和不重新排序性，不能像同步锁sychronnized那样保证原子性。

### 内部锁和显示锁
内部锁：由对象的锁以及sychronnized关键字保护的代码块或者方法，使用起来十分方便，会自动释放，但是没有中断，超时等等锁的高级特性。

显示锁：用lock或者基于lock开发的高级锁，缺点是使用较为繁琐，需要在finally里面释放，不然会造成异常的时候没法释放锁。优点是具有中断，超时等等的高级特性可以定制。

### HashMap的低层实现和线程安全性
HashMap是Map接口的非同步实现，所以Hashmap是非线程安全的，如果多个线程同时访问这个内存对象，需要保持它的外部同步性。
HashMao有两个十分重要的参数，分别是loadFactor和initialCapacity，loadFactor表示散列表的装填程度，越大表示空间运用越充分，但是链表查找的几率就会大大上升。反之，比较浪费空间，但是基本能保证命中后不用链式查找。

HashMap是数组和引用相结合的使用，实际上是一个散列数组的数据结构，即数组和链表的结合体（一个数组，每一个数组元素又是一个链表）。
打个比方，每一个元素叫做Entry, 这个数组有一个Capacity，每个Entry有一个Key-Value对，还包含一个Next的引用变量，指向下一个Entry。

下面我们来看一下Hashmap的一些常用的操作：

PUT：首先会根据传进来的key计算对应的hashcode，然后基于这个hashcode计算数组中桶的下标。如果这个桶的位置已经有其他元素了，那就在这个
桶的下面以链表的方式进行数据的存放。每次都将新元素添加到脸变的头上，这样可以降低复杂度，没必要每次都遍历链表，将新元素放到链表的尾巴上。
这里我们可以看到，当计算这个element放在哪个位置的时候，完全没有比较考虑value的数值，只需要根据key的数值计算出hash value就行了。这里的hash func
需要尽可能的被优化，每次只需要找到那个key的位置，就能找到对应的value，而不需要遍历对应的冲突链来寻找对应的那个value。

Hashmap的resize：当hashmap里的元素个数超过ARRAYSIZE*loadFactor的时候，就需要进行数组扩容，同时数组中的元素需要重新计算它们对应的位置。

Fail-fast机制：Hashmap不是线程安全的，如果在迭代的过程中，其他线程修改了map，那就会抛出ConcurrentModificationException的错误。这里使用了
一个volatile的变量modCount，来维护状态在多个线程之间的可见性。同时在迭代里面确认这个状态没有被其他线程更改。



## JVM

### JVM的四大组成部分
类加载器，执行引擎，内存区以及本地方法接口

### JVM的类加载机制
1. 加载，在内存堆中生成一个代表这个类的java.lang.Class对象，这个对象可以从Class文件，jar包，war包等等地方获取。从这个类的全限定名获取这个类的二进制字节流，并且把静态存储结构转换为方法区中的运行时数据结构。同时可以通过前面堆中的对象来访问方法区中的数据。

   类名+包名+ClassLoader实例ID可以。一般加载类的顺序是bootstrap->extention->app->customer

   bootstrap：是最顶层的类加载器，从java的核心类库中读取，读取$JRE/lib/rt.jar中所有的class文件，这个jar文件包含了java规范定义的所有接口和实现

   extention：这个是java的扩展类库，读取$JRE/lib/ext/*.jar中的包

   app：用来读取CLASSPATH下的所有jar包和目录的类文件，这个是程序的默认类加载器。

   customer：加载的自定义类，用来读取指定的类文件。

2. 验证，验证Class文件的byte流符合当前虚拟机的要求。
3. 准备，为类中的变量分配内存并且初始化类的变量，

### JVM的执行引擎
负责执行class文件中包含的字节码

### 内存区
能被线程共享的部分主要包含方法区（存储类的元结构信息，比如属性和方法信息，final常量池，static静态变量，构造函数），堆（存储JAVA实例或者对象，这是GC的主要部分），而不能被线程共享的有栈（每个线程都会创建一个栈，而每个线程中的方法都会创建一个栈帧，用来存储对应方法的局部变量，操作栈，方法返回值等，主要用于存储此次方法调用过程中的临时变量，参数和中间结果），线程计数器（保存线程执行的内存地址，JVM的模式是多线程来回切换的，每次能恢复到原先的状态，记录被中断的地方）以及本地Native方法区（所谓的nativ方法，是那些非JAVA语言写的方法，然后java程序去调用这些方法，当我们的java程序需要去和一些底层的操作系统，或者是硬件交换信息的时候）。

### 堆内存
由于对内存是JAVA GC的“大客户”，所以这里我们着重来看下JAVA堆内存的构成。
1. 年轻代：JAVA一个新创造的对象一般都是放在年轻带里面的，年轻代主要有Eden，Survivor0和Survivor1组成。新的对象一开始主要都在Eden中产生（这个名字有点意思，是伊甸园的意思）。如果这时候发现Eden里面的空间不够了，就会出发一次GC，在这次GC中，会把Eden中的存活对象复制到Survivor0中，然后清空Eden区。当Survivor0也满的时候，将所有Eden和Survivor0的存活对象放入Survivor1中，然后清空Eden和Survivor0。然后将Survivor1和Survivor0互换。但是当Survivor1不足以放下Eden和Survivor0的时候，就将存活对象直接放到老年代。同时每次GC时候，Survivor区中那些没有被gc的对象的年龄加1，某个对象超过15岁了，就放到老年区中。老年区也满了的话，会触发一次Full GC,同时回收老年代和新生代。
2. 老年代：经历了N次垃圾回收后仍然存活的对象。一般是大对象和长期存活的对象。

### JAVA的内存分配
JVM每次都会分配一大块内存，类似一个内存池的概念。静态内存是在编译时就已经能确定的内存，比如int型变量，而动态内存是在运行时才能确定的内存大小。我们不太需要关心stack的内存分配大小，我们只需要关心对内存的内存回收机制就行了。

## JAVA的内存回收gc

### 内存回收的分类
JAVA的内存回收机制主要有Scavenge GC和Full GC这两类。Scavenge GC主要是发生在新生代的Eden区，这个由于存活率不高，往往可以使用COPY算法来实现。FULL GC主要发生在老年代，由于这是对整个堆内存的回收，所以速度比Scavenge GC要慢很多，我们要尽量减少Full GC的次数，这往往在JVM调优中很重要。

### 内存回收的触发条件
1. 当应用程序空闲时，即使没有应用线程在运行，GC也会被调用。因为GC的线程优先级很低。
2. JAVA堆内存不足。
3. 某些编译优化。
减少gc的开销，可以减少System.gc()主gc的次数，减少临时对象，将对象显式地置成null（置成null，等于告诉jvm这个对象可以被回收了，帮助jvm判定），使用stringbuffer来取代string，因为stringbuffer不会创建临时的中间string对象，多使用基本变量而不是基本变量对应的对象，少使用全局静态变量（全局变量，一直占着内存），分散对象创建删除的时间。

### 内存回收之finalize()
finialize可以被用来回收那些特殊的内存区域，通常垃圾垃圾回收器只知道那些由显示new分配的内存空间，对于某些特殊分配的空间，垃圾回收器往往不知道该怎么回收它们，比如使用本地方法调用C函数进行malloc一片内存，还有打开的文件资源等等，如果不使用finalize函数，就可能会造成内存泄漏。JAVA没有类似析构函数的感念，所以一般的做法是重写Object的finalize函数，做一些特殊的handler操作，比如发送销毁通知，等等。

### 内存回收之内存标记
我们在做gc的时候，首先需要确定被回收的对象是否是垃圾。
引用计数算法固然方便，然是它解决不了循环引用的问题。
所以我们有了根集算法，根对象包括方法区中的常量引用的对象，类静态属性的引用对象，本地方法栈中的引用对象。从而找到那些从GC根对象不可达的对象。还有一点需要提的，这里是否可达看的是强引用。
我们需要去标记那些活动对象为蓝色，这是被称为安全点，JVM会在要进行gc的时候进行暂停应用线程的操作。同时，暂停时间的长短取决于堆中存活对象的多少。同时，要在根遍历算法中宣告某一个对象死亡，必须经过两步。首先，在进行根搜索算法中，如果发现某一个节点没有与根节点相连的引用链，它会被第一次标记和筛选，看看这个对象是否有必要执行finialize（析构函数）。
如果发现有必要进行finialize，则需要把这个对象放到F-Queue中，然后另一个优先级比较低的finialize线程会去在F-Queue上执行finialize()。如果对象在这一次finalize中重新引用链上的一个对象，就能不被回收。就完成了所有的标记。

### 内存回收之引用计数存在的问题
我们知道，在JAVA的内存回收算法中，有一种叫引用技术的算法，当某一个对象它的引用计数为0的时候，说明它没有被其他任何对象引用，这个对象就能被回收。然而，引用技术却存在一个循环引用的问题。我们来看一段代码
~~~java
public class MyObject {
    public Object ref = null;
    public static void main(String[] args) {
        MyObject myObject1 = new MyObject();
        MyObject myObject2 = new MyObject();
        myObject1.ref = myObject2;
        myObject2.ref = myObject1;
        myObject1 = null;
        myObject2 = null;
    }
}
~~~
这段代码，首先object1和object2的引用技术为1，然后他们分别被对方的ref引用，当把obj1置空的时候，它还被obj2的ref引用，但是obj2的ref也没法释放，因为它被obj1引用。就有个死循环在这里。

### 内存回收之算法
1. 标记清除算法：基于根集的标记清除算法，主要就是为了解决引用计数的循环引用问题。首先先用根搜索算法来标记那些需要清楚的对象，然后在清除完后统一回收掉被标记的对象。这种算法需要一个空闲列表来记录所有空闲区域以及大小。同时，当清除后，会存在很多不连续的内存碎片。老年代GC可以用，因为老年代中的对象存活率比较高，所以可以用这个来做Full GC。
![](/assets/img/mark_clear.jpg)
![](/assets/img/after_mark_clear.jpg)
2. 标记整理算法：这个算法不是每次直接把内存清空，而是把对象都往一端移动，然后清理端边界之外的内存。但缺点也很显而易见，GC暂停用来拷贝的时间会加长，因为需要拷贝对象并且更新引用地址。老年代GC可以用，因为老年代中的对象存活率比较高，所以可以用这个来做Full GC。
![](/assets/img/mark_manage.jpg)
![](/assets/img/after_mark_manage.jpg)
3. copy算法：这个算法将内存分为两块，分别叫对象区和空闲区。当对象区用完之后，将存活的对象复制到空闲区中，然后把内存区的那些一次性删除（包括已经复制到空闲区的存活对象和没有复制的需要回收的对象）。这种算法适用于对象存活率较低的内存空间，因为如果存活率较高，会有很多的复制工作，这样算法的效率就会变低。所以一般运用在新生代堆内存中的Scavenge GC比较多，因为新生代堆内存中对象存活率不高，需要复制到空闲区的对象数量也没有那么的多。
优点有，可以一边标记一边进行复制，没有内存碎片。
![](/assets/img/copy.jpg)
![](/assets/img/after_copy.jpg)

### 内存回收之内存泄漏
java语言中，也是会发生内存泄漏的，来看一个例子
~~~java
Static Vector v = new Vector();
for (int i = 1; i<100; i++)
    {
        Object o = new Object();
        v.add(o);
        o = null;
}
~~~
这里的循环中，我们每次创建一个对象，然后把这个对象加入到静态vector中去，但是这里的o=null并不能让o被GC回收，然是o仍然被v引用中午，还是可以被访问到的，但此时obj对象已经没有任何作用，却不能被gc，所以我们认为java程序发生了内存泄漏。

### 内存回收之性能调优
主要控制Full GC的频率，关键在于保证大多数对象的生存周期不能太长，保证老年代的稳定。

### 内存回收之具体过程
我们用一段代码来实际看一下java代码在实际内存回收中的表现
~~~java
public class SlotGc{
    public static void main(String[] args){
        byte[] holder = new byte[32*1024*1024];
        System.gc();
    }
}
~~~
[Full GC 32902K->32902K(37828K), 0.0094149 sec]
垃圾回收前堆中的存活对象所占的内存大小，gull gc垃圾回收后堆中对象的大小
这段代码在内存中分配了32MB的空间，这里调用system.gc()并不会gull gc掉作用域内holder分配的内存空间。
第二次
~~~java
public class SlotGc{
    public static void main(String[] args){                  
        byte[] holder = new byte[32*1024*1024];
    }
    System.gc();
}
~~~
[Full GC 32902K->32902K(37828K), 0.0089226 secs]
堆内存中的活跃对象还是没有被回收
第三次
~~~java
public class SlotGc{
    public static void main(String[] args){
        byte[] holder = new byte[32*1024*1024];
        holder = null;
    }
    System.gc();
}
~~~
[Full GC 32902K->134K(37828K), 0.0091637 secs]
这次holder对象所引用的堆内存被释放掉了。
在第二次修改中，作为根节点检索算法，GC根节点中一部分的局部变量表中仍有对holder的关联，holder并没有变成跟节点不可达节点，所以GC没有对它进行回收。第三次修改中，通过holder=null的操作，把holder所在的局部变量表清空了，所以这次GC就把它回收掉了。当然，其他更新局部表slot的操作，比如holder作用域外再进行一次读写操作，也能达到同样的效果。



