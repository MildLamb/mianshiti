## 什么是面向对象?
- 对比面向过程，是两种不同的处理问题的角度  
- 面向过程更注重事情的每一个步骤及顺序，面向对象更注重事情有哪些参与者(对象)、及各自需要做什么

## JDK,JRE,JVM的联系和区别?
- JDK：Java Develpment Kit(Java开发工具包)
- JRE：Java Runtime Environment(Java运行时环境)
- JVM：Java Virtual(Java虚拟机)

![image](https://user-images.githubusercontent.com/92672384/152710278-d4c118f2-db7b-4119-8571-daa61487e946.png)

## ==和equals的区别?
- ==对比的是栈中的值，基本数据类型是变量值，引用类型是堆中内存对象的地址

## final的作用?
最终的  
- 修饰类：表示该类为最终类，这个类不可以再被继承
- 修饰方法：表示该方法为最终方法，表示方法不能被子类重写，但是可以重载
- 修饰变量：表示变量一旦被赋值就不可以再改变它的值


(1) 修饰成员变量  
- 如果final修饰的是(static)类变量，只能在静态初始化块中指定初始值或者声明该类变量时指定初始值。
- 如果final修饰的是成员变量，可以在非静态初始化块、声明该变量或者构造器中执行初始值。

![image](https://user-images.githubusercontent.com/92672384/152711231-d10d6a82-9f6f-48a3-8da3-3323cdf1e90e.png)  

(2) 修饰局部变量  
- 系统不会为局部变量进行初始化，局部变量必须由程序员显示初始化。因此使用final修饰局部变量时，即可以在定义时指定默认值(后面的代码不能对变量再赋值)，也可以不指定默认值，而在后面的代码中对final变量赋初值(仅一次)

![image](https://user-images.githubusercontent.com/92672384/152711683-e916abd4-9429-470b-b22d-396076a32c61.png)  

(3) 修饰基本类型数据和引用类型数据  
- 如果是基本数据类型的变量，则其数值一旦在初始化之后便不能更改
- 如果是引用类型的变量，则在对其初始化之后便不能再让其指向另一个对象。但是**引用的值是可变的**。

## 为什么局部内部类和匿名内部类只能访问局部final变量(JDK8默认会加上final，所以不会报错)?
- 下面代码编译后会生成两个class文件，Test.class和Test1.class
```java
public class Test {
    public static void main(String[] args) {
        new OutClass().outPrint(23);
    }

    // 局部final变量 a,b
    public void test(int b){
        int a = 10;
        new Thread(){
            @Override
            public void run() {
                System.out.println(a);
                System.out.println(b);
            }
        }.start();
    }

}


class OutClass {
    private int age = 12;
    public void outPrint(int x) {
        class InClass {
            public void InPrint() {
                System.out.println(x);
                System.out.println(age+1);
            }
        }
        new InClass().InPrint();
    }
}
```
- 首先需要知道的一点是:内部类和外部类是处于同一个级别的，内部类不会因为定义在方法中就会随着方法的执行完毕就被销毁。
- 这里就会产生问题:当外部类的方法结束时，局部变量就会被销毁了，但是内部类对象可能还存在(只有没有人再引用它时，才会死亡)。这里就出现了一个矛盾:内部类对象访问了一个不存在的变量。为了解决这个问题，就将局部变量复制了一份作为内部类的成员变量，这样当局部变量死亡后，内部类仍可以访问它，实际访问的是局部变量的"copy"。这样就好像延长了局部变量的生命周期

## String,StringBuffer,SreingBuilder区别以及使用场景?
- String是final修饰的，不可变，每次操作都会产生新的String对象
- StringBuffer和StringBuilder都是可以在原对象上操作
- StringBuffer是线程安全的，StringBuilder是线程不安全的
- StringBuffer的方法都是有synchronized修饰的
- 性能：StringBuilder > StringBuffer > String

场景：需要经常改变字符串内容的时候使用后两个  
优先使用StringBuilder，多线程共享变量时使用StringBuffer  

## 重载和重写的区别?
- 重载:发生在同一个类中，方法名必须相同，参数类型不同、个数不同、顺序不同,方法返回值和访问修饰符可以不同(意思就是任然报错)，发生在编译时。  
- 重写:发生在父子类中，方法名、参数列表必须相同，返回值范围小于等于父类，抛出的异常范围小于等于父类，访问修饰符范围大于等于父类;如果父类方法访问修饰符为private则子类就不能重写该方法。

## 接口和抽象类的区别?
- 抽象类可以存在普通成员函数，而接口中只能存在public abstract方法。
- 抽象类中的成员变量可以是各种类型的，而接口中的成员变量只能是public static final类型的。
- 抽象类只能继承一个，接口可以实现多个。

## List和Set的区别?
- List:有序，按对象进入的顺序保存对象，可重复，允许多个Null元素对象，可以使用lterator取出所有元素，在逐一遍历，还可以使用get(int index)获取指定下表的元素
- Set:无序，不可重复，最多允许有一个Null元素对象，取元素时只能用lterator接口取得所有元素，在逐一遍历各个元素

## hashCode和equals
### hashCode介绍
hashCode()的作用是获取哈希码，也称为散列码;它实际上是返回一个int整数。这个哈希码的作用是确定该对象在哈希表中的索引位置。hashCode()定义在JDK的Object.java中，Java中的任何类都包含有hashCode()函数。散列表存储的是键值对(key-value)，它的特点是:能根据"键"快速的检索出对应的“值"。这其中就利用到了散列码!(可以快速找到所需要的对象)

- 如果两个对象相等，则hashcode—定也是相同的
- 两个对象相等,对两个对象分别调用equals方法都返回true
- 两个对象有相同的hashcode值，它们也不一定是相等的因此
- equals方法被覆盖过，则hashCode方法也必须被覆盖
- hashCode()的默认行为是对堆上的对象产生独特值。如果没有重写hashCode()，则该class的两个对象无论如何都不会相等（即使这两个对象指向相同的数据)

## ArrayList和LinkedList区别
- ArrayList:基于动态数组，连续内存存储，适合下标访问（随机访问)，扩容机制:因为数组长度固定，超出长度存数据时需要新建数组，然后将老数组的数据拷贝到新数组，如果不是尾部插入数据还会涉及到元素的移动(往后复制一份，插入新元素)，使用尾插法并指定初始容量可以极大提升性能、甚至超过linkedList(需要创建大量的node对象)
- LinkedList:基于链表，可以存储在分散的内存中，适合做数据插入及删除操作，不适合查询:需要逐一遍历遍历LinkedList必须使用iterator不能使用for循环，因为每次for循环体内通过get(i取得某一元素时都需要对list重新进行遍历，性能消耗极大。另外不要试图使用indexOf等返回元素索引，并利用其进行遍历，使用indexlof对list进行了遍历，当结果为空时会遍历整个列表。

## HashMap和Hashtable的区别
(1) HashMap方法没有synchronized修饰，线程非安全，HashTable线程安全;  
(2) HashMap允许key和value为null，而HashTable不允许  

jdk8开始链表高度到8、数组长度超过64，链表转变为红黑树，元素以内部类Node节点存在  
- 计算key的hash值，二次hash然后对数组长度取模，对应到数组下标
- 如果没有产生hash冲突(下标位置没有元素)，则直接创建Node存入数组
- 如果产生hash冲突，先进行equal比较，相同则取代该元素，不同，则判断链表高度插入链表，链表高度达到8，并且数组长度到64则转变为红黑树，长度低于6则将红黑树转回链表
- key为null，存在下标0的位置

容量默认是16，加载因子默认是0.75，阈值=容量*加载因子，默认是12，当元素数量超过阈值时便会触发扩容，扩容为原来的2倍

## ConcurrentHashMap原理，jdk7和jdk8的区别
jdk7：  
- 数据结构: ReentrantLock+Segment+HashEntry，一个Segment中包含一个HashEntry数组，每个HashEntry又是一个链表结构Ⅰ  
- 元素查询: 二次hash，第一次Hash定位到Segment，第二次Hash定位到元素所在的链表的头部   
- 锁:Segment分段锁Segment继承了ReentrantLock，锁定操作的Segment，其他的Segment不受影响，并发度为segment个数，可以通过构造函数指定，数组扩容不会影响其他的segment  
- get方法无需加锁,volatile保证

jdk8：  
- 数据结构: synchronized+CAS+Node+红黑树，Node的val和next都用volatile修饰，保证可见性查找，替换，赋值操作都使用CAS
- 锁:锁链表的head节点，不影响具他兀系的读与，认性，扩容
- 读操作无锁:Node的val和next使用volatile修饰，读写线程对该变量互相可见数组用volatile修饰，保证扩容时被读线程感知

## 如何实现一个IOC容器?
1. 配置文件配置包扫描路径
2. 递归包扫描获取.class文件
3. 反射、确定需要交给Ioc管理的类
4. 对需要注入的类进行依赖注入

- 配置文件中指定需要扫描的包路径
- 从配置文件中获取需要扫描的包路径，获取到当前路径下的文件信息及文件夹信息，我们将当前路径下所有以.class结尾的文件添加到一个Set集合中进行存储
- 遍历这个set集合，获取在类上有指定注解的类，并将其交给IOC容器，定义一个安全的Map用来存储这些对象
- 遍历这个IOC容器，获取到每一个类的实例，判断里面是有有依赖其他的类的实例，然后进行递归注入

## 什么是字节码?使用字节码有什么好处?
- Java中引入了虚拟机的概念，即在机器和编译程序力间加入了一层抽象的虚拟的机器。这台虚拟的机器在任何平台上都提供给编译程序一个的共同的接口。  
- 编译程序只需要面向虚拟机，生成虚拟机能够理解的代码，然后由解释器来将虚拟机代码转换为特定系统的机器码执行。在Java中，这种供虚拟机理解的代码叫做字节码(即扩展名为.class的文件)，它不面向任何特定的处理器，只面向虚拟机。  
好处：  
Java语言通过字节码的方式，在一定程度上解决了传统解释型语言执行效率低的问题，同时又保留了解释型语言可移植的特点。所以ava程序运行时比较高效，而且，由于字节码并不专对一种特定的机器，因此，Java程序无须重新编译便可在多种不同的计算机上运行。

## Java的类加载器
- JDK自带有三个类加载器: bootstrap ClassLoader、ExtClassLoader、AppClassLoader。
- BootStrapClassLoader是ExtClassLoader的父类加载器，默认负责加载%JAVA_HOME%lib下的jar包和class文件。
- ExtClassLoader是AppClassLoader的父类加载器，负责加载%JAVA_HOME%/lib/ext文件夹下的jar包和class类。
- AppClassLoader是自定义类加载器的父类，负责加载classpath下的类文件。
- 继承ClassLoader实现自定义类加载器

## 双亲委派机制
![image](https://user-images.githubusercontent.com/92672384/153102744-c4c0ae09-7fb2-4405-b0ee-3fff2a579c91.png)

双亲委派的好处：  
- 主要是为了安全性，避免用户自己编写的类动态替换Java的一些核心类，比如String。
- 同时也避免了类的重复加载，因为JⅣM中区分不同类，不仅仅是根据类名，相同的class文件被不同的ClassLoader加载就是不同的两个类

## Java的异常体系
- Java中的所有异常都来自顶级父类Throwable。
- Throwable下有两个子类Exception和Error（如OOM，OutOfMemory）。
- Error是程序无法处理的错误，一旦出现这个错误，则程序将被迫停止运行。
- Exception不会导致程序停止，又分为两个部分RunTimeException运行时异常和CheckedException检查异常。
- RunTimeException常常发生在程序运行过程中，会导致程序当前线程执行失败。CheckedException常常发生在程序编译过程中，会导致程序编译不通过。

1、常见编译时异常：  

- IOException                    输入输出流异常 
- FileNotFoundException          文件找不到的异常  
- ClassNotFoundException         类找不到异常  
- DataFormatException            数据格式化异常  
- NoSuchFieldException           没有匹配的属性异常  
- NoSuchMethodException          没有匹配的方法异常  
- SQLException                   数据库操作异常  
- TimeoutException               执行超时异常  

2、运行时异常（RuntimeException）：  

- ArrayIndexOutofBoundsException     数组越界异常 
- ClassCastException                 类型转换异常 
- NullPointerException               空指针异常   
- IllegalAccessException             非法的参数异常 
- InputMismatchException             输入不匹配异常
- ArithmeticException                算术异常

## GC如何判断对象可以回收
- 引用计数法:每个对象有一个引用计数属性，新增一个引用时计数加1，引用释放时计数减1，计数为0时可以回收
- 可达性分析法:从GCRoots开始向下搜索，搜索所走过的路径称为引用链。当一个对象到GCRoots没有任何引用链相连时，则证明此对象是不可用的，那么虚拟机就判断是可回收对象。

GC Root：  
- 虚拟机栈(栈帧中的本地变量表)中引用的对象
- 方法区中类静态属性引用的对象
- 方法区中常量引用的对象
- 本地方法栈中JNI(即一般说的Native方法)引用的对象

## 线程的生命周期，线程有哪些状态?
1. 线程通常有五种状态，创建，就绪，运行、阻塞和死亡状态。
2. 阻塞的情况又分为三种:  
(1)、等待阻塞:运行的线程执行wait方法，该线程会释放占用的所有资源，JVM会把该线程放入“等待池"中。进入这个状态后，是不能自动唤醒的，必须依靠其他线程调用notify或notifyAll方法才能被唤醒,wait是object类的方法  
(2)、同步阻塞:运行的线程在获取对象的同步锁时，若该同步锁被别的线程占用，则VM会把该线程放入"锁池"中。  
(3)、其他阻塞:运行的线程执行sleep或join方法，或者发出了IO请求时，JVM会把该线程置为阻塞状态。当sleep状态超时、 join等待线程终止或者超时、或者I/O处理完毕时，线程重新转入就绪状态。sleep是Thread类的方法  

1. 新建状态(New) :新创建了一个线程对象。
2. 就绪状态(Runnable):线程对象创建后，其他线程调用了该对象的start方法。该状态的线程位于可运行线程池中，变得可运行，等待获取CPU的使用权。
3. 运行状态(Running)∶就绪状态的线程获取了CPU，执行程序代码。
4. 阻塞状态(Blocked):阻塞状态是线程因为某种原因放弃CPU使用权，暂时停止运行。直到线程进入就绪状态，才有机会转到运行状态。
5. 死亡状态(Dead):线程执行完了或者因异常退出了run方法，该线程结束生命周期。

## sleep，wait，join，yeild的区别?
1. 锁池
所有需要竞争同步锁的线程都会放在锁池当中，比如当前对象的锁已经被其中一个线程得到，则其他线程需要在这个锁池进行等待，当前面的线程释放同步锁后锁池中的线程去竞争同步锁，当某个线程得到后会进入就绪队列进行等待cpu资源分配。  
2. 等待池
当我们调用wait()方法后，线程会放到等待池当中，等待池的线程是不会去竞争同步锁。只有调用了notify ()或notifyAll()后等待池的线程才会开始去竞争锁，notify ()是随机从等待池选出一个线程放到锁池，而notifyAlI()是将等待池的所有线程放到锁池当中

1. sleep是Thread类的静态本地方法，wait则是Object类的本地方法。
2. sleep方法不会释放lock，但是wait会释放，而且会加入到等待队列中。
3. sleep方法不依赖于同步器synchronized，但是wait需要依赖synchronized关键字。
4. sleep不需要被唤醒（休眠之后退出阻塞)，但是wait需要（不指定时间需要被别人中断)。
5. sleep一般用于当前线程休眠，或者轮循暂停操作，wait则多用于多线程之间的通信。
6. sleep会让出CPU执行时间且强制上下文切换，而wait则不一定，wait后可能还是有机会重新竞争到锁继续执行的。

- yield ()执行后线程直接进入就绪状态，马上释放了cpu的执行权，但是依然保留了cpu的执行资格，所以有可能cpu下次进行线程调度还会让这个线程获取到执行权继续执行  
- join ()执行后线程进入阻塞状态，例如在线程B中调用线程A的join ()，那线程B会进入到阻塞队列，直到线程A结束或中断线程

## 谈谈对线程安全的理解
- 线程安全指的是，在堆内存中的数据由于可以被任何线程访问到，在没有限制的情况下存在被意外修改的风险
- 当多个线程访问一个对象时，如果不用进行额外的

## Thread和Runable的区别
- Runnable的实现方式是实现其接口即可
- Thread的实现方式是继承其类
- Runnable接口支持多继承，但基本上用不到
- Thread实现了Runnable接口并进行了扩展，而Thread和Runnable的实质是实现的关系，不是同类东西，所以Runnable或Thread本身没有可比性。

## 说说对守护线程的理解?
守护线程:为所有非守护线程提供服务的线程;任何一个守护线程都是整个VM中所有非守护线程的保姆;当所有的用户进程结束了之后就会中断守护线程。注意:由于守护线程的终止是自身无法控制的，因此千万不要把IO、File等重要操作逻辑分配给它;因为它可能突然就中断了;  
- thread.setDaemon(true)必须在thread.start()之前设置，否则会跑出一个llegalThreadStateException异常。你不能把正在运行的常规线程设置为守护线程。
- 在Daemon线程中产生的新线程也是Daemon的。
- 守护线程不能用于去访问固有资源，比如读写操作或者计算逻辑。因为它会在任何时候甚至在一个操作的中间发生中断。

## ThreadLocal使用场景和原理
- 每一个Thread对象均含有一个ThreadLoca1Map类型的成员变量threadLocals，它存储本线程中所有ThreadLocal对象及其对应的值
- ThreadLoca1Map由一个个Entry对象构成
- Entry继承自weakReference<ThreadLocal<?>>，一个Entry由 ThreadLoca1对象和object构成。由此可见，Entry的key是ThreadLocal对象，并且是一个弱引用。当没指向key的强引用后，该key就会被垃圾收集器回收
- 当执行set方法时，ThreadLocal首先会获取当前线程对象，然后获取当前线程的ThreadLocalMap对象。再以当前ThreadLocal对象为key，将值存储进ThreadLocalMap对象中。
- get方法执行过程类似。ThreadLocal首先会获取当前线程对象，然后获取当前线程的ThreadLocalMap对象。再以当前ThreadLocal对象为key，获取对应的value。
- 由于每一条线程均含有各自私有的ThreadLocalMap容器，这些容器相互独立互不影响，因此不会存在线程安全性问题，从而也无需使用同步机制来保证多条线程访问容器的互斥性。 

使用场景：  
1. 在进行对象跨层传递的时候，使用ThreadLocal可以避免多次传递，打破层次间的约束。
2. 线程间数据隔离
3. 进行事务操作，用于存储线程事务信息。
4. 数据库连接，Session会话管理。

## Threadlocal内存泄漏原因以及如何避免
内存泄露为程序在申请内存后，无法释放已申请的内存空间，一次内存泄露危害可以忽略，但内存泄露堆积后果很严重，无论多少内存,迟早会被占光，  
不再会被使用的对象或者变量占用的内存不能被回收，就是内存泄露。  
- 强引用:使用最普遍的引用(new)，一个对象具有强引用，不会被垃圾回收器回收。当内存空间不足，Java虚拟机宁愿抛出OutOfMemoryError错误，使程序异常终止，也不回收这种对象。
- 弱引用:JVM进行垃圾回收时，无论内存是否充足，都会回收被弱引用关联的对象。在java中，用java.lang.ref.WeakReference类来表示。可以在缓存中使用弱引用。

![image](https://user-images.githubusercontent.com/92672384/153321258-c3cb3714-bcc6-40e7-a375-61ee739d3b42.png)   

ThreadLocalMap使用ThreadLocal的弱引用作为key，如果一个ThreadLocal不存在外部强引用时，Key(ThreadLocal)势必会被GC回收，这样就会导致ThreadLocalMap中key为null，而value还存在着强引用，只有thead线程退出以后,value的强引用链条才会断掉，但如果当前线程再迟迟不结束的话，这些key为null的Entry的value就会一直存在一条强引用链(红色链条)  

ThreadLocal内存泄漏的根源是:由于ThreadLocalMap的生命周期跟Thread一样长，如果没有手动删除对应key就会导致内存泄漏，而不是因为弱引用。

## 串行，并行和并发的区别
- 串行在时间上不可能发生重叠，前一个任务没搞定，下一个任务就只能等着
- 并行在时间上是重叠的，两个任务在同一时刻互不干扰的同时执行。
- 并发允许两个任务彼此干扰。统—时间点、只有一个任务运行，交替执行

## 并发的三大特性
- 原子性：原子性是指在一个操作中cpu不可以在中途暂停然后再调度，即不被中断操作，要不全部执行完成，要不都不执行，关键字：synchronized
- 可见性：当多个线程访问同一个变量时，一个线程修改了这个变量的值，其他线程能够立即看得到修改的值。关键字：volatile，synchronized
- 有序性：虚拟机在进行代码编译时，对于那些改变顺序之后不会对最终结果造成影响的代码，虚拟机不一定会按照我们写的代码的顺序来执行，有可能将他们重排序。实际上，对于有些代码进行重排序之后，虽然对变量的值没有造成影响，但有可能会出现线程安全问题。

## 为什么使用线程池，参数介绍
1. 降低资源消耗;提高线程利用率，降低创建和销毁线程的消耗。
2. 提高响应速度;任务来了，直接有线程可用可执行，而不是先创建线程，再执行。
3. 提高线程的可管理性;线程是稀缺资源，使用线程池可以统一分配调优监控。

- corePoo1Size代表核心线程数，也就是正常情况下创建工作的线程数，这些线程创建后并不会消除，而是—种常驻线程
- maxinumPoo1size代表的是最大线程数，它与核心线程数相对应，表示最大允许被创建的线程数，比如当前任务较多，将核心线程数都用完了，还无法满足需求时，此时就会创建新的线程，但是线程池内线程总数不会超过最大线程数
- keepAliveTime、unit表示超出核心线程数之外的线程的空闲存活时间，也就是核心线程不会消除，但是超出核心线程数的部分线程如果空闲一定的时间则会被消除,我们可以通过setKeepAliveTime来设置空闲时间
- workQueue用来存放待执行的任务，假设我们现在核心线程都已被使用，还有任务进来则全部放入队列，直到整个队列被放满但任务还再持续进入则会开始创建新的线程
- ThreadFactory实际上是一个线程工厂，用来生产线程执行任务。我们可以选择使用默认的创建工厂，产生的线程都在同一个组内，拥有相同的优先级，且都不是守护线程。当然我们也可以选择自定义线程工厂，一般我们会根据业务来制定不同的线程工厂
- Hand1er任务拒绝策略，有两种情况，第一种是当我们调用shutdown等方法关闭线程池后，这时候即使线程池内部还有没执行完的任务正在执行，但是由于线程池已经关闭，我们再继续想线程池提交任务就会遭到拒绝。另一种情况就是当达到最大线程数，线程池已经没有能力继续处理新提交的任务时，这是也就拒绝
  - ThreadPoolExecutor.AbortPolicy:丢弃任务并抛出RejectedExecutionException异常。 
  - ThreadPoolExecutor.DiscardPolicy：丢弃任务，但是不抛出异常。 
  - ThreadPoolExecutor.DiscardOldestPolicy：丢弃队列最前面的任务，然后重新提交被拒绝的任务 
  - ThreadPoolExecutor.CallerRunsPolicy：由调用线程（提交任务的线程）处理该任务

## 简述线程池执行流程

![线程池执行流程](https://user-images.githubusercontent.com/92672384/153329503-fcee183d-99f8-4ec0-b969-c4dad43d1143.png)

## 线程池中阻塞队列作用?为什么是先添加队列而不是先创建到最大线程?
1. 一般的队列只能保证作为一个有限长度的缓冲区，如果超出了缓冲长度，就无法保留当前的任务了，阻塞队列通过阻塞可以保留住当前想要继续入队的任务。  
阻塞队列可以保证任务队列中没有任务时阻塞获取任务的线程，使得线程进入wait状态，释放cpu资源。  
阻塞队列自带阻塞和唤醒的功能，不需要额外处理，无任务执行时,线程池利用阻塞队列的take方法挂起，从而维持核心线程的存活、不至于一直占用cpu资源  
2. 在创建新线程的时候，是要获取全局锁的，这个时候其它的就得阻塞，影响了整体效率。

## 线程池中线程的复用原理
在线程池中，同一个线程可以从阻塞队列中不断获取新任务来执行，其核心原理在于线程池对Thread进行了封装，并不是每次执行任务都会调用Thread.start()来创建新线程，而是让每个线程去执行一个"循环任务”，在这个"循环任务"中不停检查是否有任务需要被执行，如果有则直接执行，也就是调用任务中的run方法，将run方法当成一个普通的方法执行，通过这种方式只使用固定的线程就将所有任务的run方法串联起来。

## Spring是什么?
- 轻量级的开源的J2EE框架。它是一个容器框架，用来装javabean (java对象)，中间层框架（万能胶)可以起一个连接作用，比如说把SpringMVC和Mybatis粘合在一起运用，可以让我们的企业开发更快、更简洁  
- Spring是一个轻量级的控制反转(loC)和面向切面(AOP)的容器框架
  - 从大小与开销两方面而言Spring都是轻里教的。
  - 通过控制反转(IoC)的技术达到松耦合的目的
  - 提供了面向切面编程的丰富支持，允许通过分离应用的业务逻辑与系统级服务进行内聚性的开发
  - 包含并管理应用对象(Bean)的配置和生命周期，这个意义上是一个容器。
  - 将简单的组件配置、组合成为复杂的应用，这个意义上是一个框架。

## AOP的理解?
- 系统是由许多不同的组件所组成的，每一个组件各负责一块特定功能。除了实现自身核心功能之外，这些组件还经常承担着额外的职责。例如日志、事务管理和安全这样的核心服务经常融入到自身具有核心业务逻辑的组件中去。这些系统服务经常被称为横切关注点，因为它们会跨越系统的多个组件。  
- 当我们需要为分散的对象引入公共行为的时候，OOP则显得无能为力。也就是说，OOP允许你定义从上到下的关系，但并不适合定义从左到右的关系。例如日志功能。  
- AOP:将程序中的交叉业务逻辑(比如安全，日志，事务等)，封装成一个切面，然后注入到目标对象（具体业务逻辑)中去。AOP可以对某个对象或某些对象的功能进行增强，比如对象中的方法进行增强，可以在执行某个方法之前额外的做一些事情，在某个方法执行之后额外的做一些事情

## IOC的理解?
- ioc容器:实际上就是个map (key,value)，里面存的是各种对象（在xml里配置的bean节点、@repository,service、@controller、@component)，在项目启动的时候会读取配置文件里面的bean节点，根据全限定类名使用反射创建对象放到map里、扫描到打上上述注解的类还是通过反射创建对象放到map里。
- 控制反转：没有引入IOC容器之前，对象A依赖于对象B，那么对象A在初始化或者运行到某一点的时候，自己必须主动去创建对象B或者使用已经创建的对象B。无论是创建还是使用对象B，控制权都在自己手上。引入IOC容器之后，对象A与对象B之间失去了直接联系，当对象A运行到需要对象B的时候，IOC容器会主动创建一个对象B注入到对象A需要的地方。通过前后的对比，不难看出来:对象A获得依赖对象B的过程,由主动行为变为了被动行为，控制权颠倒过来了，这就是"控制反转"这个名称的由来。
- 依赖注入：依赖注入是实现IOC的方法，就是由IOC容器在运行期间，动态地将某种依赖关系注入到对象之中。

## BeanFactory和ApplicationContext的区别?
- ApplicationContext是BeanFactory的子接口
- ApplicationContext提供了更完整的功能:  
  - 继承MessageSource，因此支持国际化。
  - 统一的资源文件访问方式。
  - 提供在监听器中注册bean的事件。
  - 同时加载多个配置文件。
  - 载入多个(有继承关系)上下文，使得每一个上下文都专注于一个特定的层次，比如应用的web层。

- BeanFactroy采用的是延迟加载形式来注入Bean的，即只有在使用到某个Bean时(调用getBean(1)，才对该Bean进行加载实例化。这样，我们就不能发现一些存在的Spring的配置问题。如果Bean的某一个属性没有注入，BeanFacotry加载后，直至第一次使用调用getBean方法才会抛出异常。
- ApplicationContext，它是在容器启动时，一次性创建了所有的Bean。这样，在容器启动时，我们就可以发现Spring中存在的配置错误，这样有利于检查所依赖属性是否注入。ApplicationContext启动后预载入所有的单实例Bean，通过预载入单实例bean ,确保当你需要的时候，你就不用等待，因为它们已经创建好了。
- 相对于基本的BeanFactory，ApplicationContext 唯一的不足是占用内存空间。当应用程序配置Bean较多时，程序启动较慢。
- BeanFactory和ApplicationContext都支持BeanPostProcessor、BeanFactoryPostProcessor的使用，但两者之间的区别是:BeanFactory需要手动注册，而ApplicationContext则是自动注册。
- BeanFactory通常以编程的方式被创建，ApplicationContext还能以声明的方式创建，如使用ContextLoader。

## SpringBean的生命周期
![8960725A303273A73DDA54FC5798B048](https://user-images.githubusercontent.com/92672384/153692681-7bca0b70-44ec-406b-a6cd-625bca7cc1a2.jpg)

1. 解析类得到BeanDefinition
2. 如果有多个构造方法，则要推断构造方法
3. 确定好构造方法后，进行实例化得到一个对象
4. 对对象中的加了@Autowired注解的属性进行属性填充
5. 回调Aware方法，比如BeanNameAware，BeanFactoryAware
6. 调用BeanPostProcessor的初始化前的方法
7. 调用初始化方法
8. 调用BeanPostProcessor的初始化后的方法，在这里会进行AOP
9. 如果当前创建的bean是单例的则会把bean放入单例池
10. 使用bean
11. Spring容器关闭时调用DisposableBean中destory()方法

## Spring中Bean的作用域?
- singleton:默认，每个容器中只有一个bean的实例，单例的模式由BeanFactory自身来维护。该对象的生周期是与Spring lOC容器一致的（但在第一次被注入时才会创建)。
- prototype:为每一个bean请求提供一个实例。在每次注入时都会创建一个新的对象
- request: bean被定义为在每个HTTP请求中创建一个单例对象，也就是说在单个请求中都会复用这一个单例对象。
- session:与request范围类似，确保每个session中有一个bean的实例，在session过期后，bean会随之失效。
- application: bean被定义为在ServletContext的生命周期中复用一个单例对象。
- websocket: bean被定义为在websocket的生命周期中复用一个单例对象。

## Spring中的单例Bean是线程安全的吗?
- 不安全
- Spring中的Bean默认是单例模式的，框架并没有对bean进行多线程的封装处理。
- 如果Bean是有状态的那就需要开发人员自己来进行线程安全的保证，最简单的办法就是改变bean的作用域把"singleton"改为"protopyte'这样每次请求Bean就相当于是new Bean()这样就可以保证线程的安全了。
  - 有状态就是有数据存储功能
  - 无状态就是不会保存数据，controller、service和dao层本身并不是线程安全的，只是如果只是调用里面的方法，而且多线程调用一个实例的方法，会在内存中复制变量，这是自己的线程的工作内存，是安全的。

## Spring框架中用到了哪些设计模式?
- 简单工厂:由一个工厂类根据传入的参数，动态决定应该创建哪一个产品类。
```text
Spring中的BeanFactory就是简单工厂模式的体现，根据传入一个唯一的标识来获得Bean对象，
但是否是在传入参数后创建还是传入参数前创建这个要根据具体情况来定。
```
- 工厂方法
```text
实现了FactoryBean接口的bean是一类叫做factory的bean。其特点是，spring会在使用getBean()调用获得bean时，
会自动调用该bean的getobject()方法，所以返回的不是factory这个bean，而是这个bean.getojbect()方法的返回值。
```
- 单例模式:保证一个类仅有一个实例，并提供一个访问它的全局访问点
```text
spring对单例的实现: spring中的单例模式完成了后半句话，即提供了全局的访问点BeanFactory。
但没有从构造器级别去控制单例，这是因为spring管理的是任意的java对象。
```
- 适配器模式
```text
spring定义了一个适配接口，使得每一种Contro11er有一种对应的适配器实现类，让适配器代替controller执行相应的方法。
这样在扩展Contro11er时，只需要增加一个适配器类就完成了SpringMVC的扩展了。
```
- 动态代理
```text
切面在应用运行的时刻被织入。一般情况下，在织入切面时，AOP容器会为目标对象创建动态的创建一个代理对象。
springAOP就是以这种方式织入切面的。
```

## Spring事务的实现方式和隔离级别?
- 在使用Spring框架时，可以有两种使用事务的方式，一种是编程式的，一种是申明式的，@Transactional注解就是申明式的。
- 我们可以通过在某个方法上增加@Transactional注解，就可以开启事务，这个方法中所有的sql都会在一个事务中执行，统—成功或失败。
- 在一个方法上加了@Transactional注解后，Spring会基于这个类生成一个代理对象，会将这个代理对象作为bean，当在使用这个代理对象的方法时，如果这个方法上存在@Transactional注解，那么代理逻辑会先把事务的自动提交设置为false，然后再去执行原本的业务逻辑方法，如果执行业务逻辑方法没有出现异常，那么代理逻辑中就会将事务进行提交，如果执行业务逻辑方法出现了异常，那么则会将事务进行回滚。

隔离级别:  
- read uncommitted(未提交读)
- read committed(提交读、不可重复读)
- repeatable read (可重复读)
- serializable (可串行化)

- 如果数据库的隔离级别和spring配置的隔离级别冲突，以spring配置的为准，如果spring设置的隔离级别数据库不支持，效果取决于数据库

## Spring事务的传播方式?

多个事务方法相互调用时,事务如何在这些方法间传播  
```text
方法A是一个事务的方法，方法A执行过程中调用了方法B，那么方法B有无事务以及方法B对事务的要求不同都会对方法A的事务具体执行造成影响，
同时方法A的事务对方法B的事务执行也有影响，这种影响具体是什么就由两个方法所定义的事务传播类型所决定。
```

- REQUIRED(Spring默认的事务传播类型):如果当前没有事务，则自己新建一个事务，如果当前存在事务，则加入这个事务
- SUPPORTS:当前存在事务，则加入当前事务，如果当前没有事务，就以非事务方法执行
- MANDATORY:当前存在事务，则加入当前事务，如果当前事务不存在，则抛出异常。
- REQUIRES_NEW:创建一个新事务，如果存在当前事务，则挂起该事务。
- NOT_SUPPORTED:以非事务方式执行,如果当前存在事务，则挂起当前事务
- NEVER:不使用事务，如果当前事务存在，则抛出异常
- NESTED:如果当前事务存在，则在嵌套事务中执行，否则REQUIRED的操作一样(开启一个事务)

## Spring事务什么时候会失效?
spring事务的原理是AOP，进行了切面增强，那么失效的根本原因是这个AOP不起作用了!常见情况有如下几种:  
1. 发生自调用，类里面使用this调用本类的方法(this通常省略)，此时这个this对象不是代理类，而是UserService对象本身!
2. 方法不是public的，@Transactiona1 只能用于 public 的方法上，否则事务不会失效，如果要用在非 public 方法上，可以开启Aspect]代理模式。
3. 数据库不支持事务
4. 对象没有被Spring管理
5. 异常被捕获，事务不会回滚(或者抛出的异常没有被定义，默认为RuntimeException)

## 什么是bean的自动装配?有哪些方式?
- no-缺省情况下，自动配置是通过“ref"属性手动设定。
- byName-根据bean的属性名称进行自动装配。
- byType-根据bean的类型进行自动装配。
- constructor-类似byType，不过是应用于构造器的参数。如果一个bean与构造器参数的类型形同，则进行自动装配，否则导致异常。
- autodetect-如果有默认的构造器，则通过constructor方式进行自动装配，否则使用byType方式进行自动装配。

## Spring，SpringMVC和SpringBoot的区别?
- spring是一个IOC容器，用来管理Bean，使用依赖注入实现控制反转，可以很方便的整合各种框架，提供AOP机制弥补oOP的代码重复问题、更方便将不同类不同方法中的共同处理抽取成切面、自动注入给方法执行，比如日志、异常等
- springmvc是spring对web框架的一个解决方案，提供了一个总的前端控制器Servlet，用来接收请求，然后定义了一套路由策略(url到handle的映射）及适配执行handle，将handle结果使用视图解析技术生成视图展现给前端
- springboot是spring提供的一个快速开发工具包，让程序员能更方便、更快速的开发spring+springmvc应用，简化了配置(约定了默认配置)，整合了一系列的解决方案(starter机制) 、redis、mongodb、es，可以开箱即用

## SpringMVC的处理流程?
1. 用户发送请求至前端控制器|DispatcherServlet。
2. DispatcherServlet收到请求调用HandlerMapping处理器映射器。
3. 处理器映射器找到具体的处理器(可以根据xml配置、注解进行查找)，生成处理器及处理器拦截器(如果有则生成)—并返回给DispatcherServlet。
4. DispatcherServlet 调用HandlerAdapter 处理器适配器。
5. HandlerAdapter经过适配调用具体的处理器(Controller，也叫后端控制器)
6. Controller执行完成返回ModelAndView。
7. HandlerAdapter将controller执行结果ModelAndView返回给DispatcherServlet。
8. DispatcherServlet将ModelAndView传给ViewReslover视图解析器。
9. ViewReslover解析后返回具体View。
10. DispatcherServlet根据View进行渲染视图(即将模型数据填充至视图中)。
11. DispatcherServlet响应用户。

![image](https://user-images.githubusercontent.com/92672384/153787785-5a5255ff-ff00-47b7-ae3c-61987258383a.png)

## SpringMVC的九大组件?
1. HandlerMapping  
initHandlerMappings(context)，处理器映射器，根据用户请求的资源uri来查找Handler的。在SpringMVC中会有很多请求，每个请求都需要一个Handler处理，具体接收到一个请求之后使用哪个Handler进行，这就是HandlerMapping需要做的事。  
2. HandlerAdapter  
initHandlerAdapters(context)，适配器。因为SpringMVC中的Handler可以是任意的形式，只要能处理请求就ok，但是Servlet需要的处理方法的结构却是固定的，都是以request和response为参数的方法。如何让固定的Servlet处理方法调用灵活的Handler来进行处理呢?这就是HandlerAdapter要做的事情。Handler是用来干活的工具;HandlerMapping用于根据需要干的活找到相应的工具;HandlerAdapter是使用工具干活的人。  
3. HandlerExceptionResolver  
initHandlerExceptionResolvers(context)，其它组件都是用来干活的。在干活的过程中难免会出现问题,出问题后怎么办呢?这就需要有一个专门的角色对异常情况进行处理，在SpringMVC中就是HandlerExceptionResolver。具体来说，此组件的作用是根据异常设置ModelAndView，之后再交给render方法进行渲染。  
4. ViewResolver  
initViewResolvers(context),ViewResolver用来将String类型的视图名和Locale解析为Vview类型的视图。  
5. RequestToViewNameTranslator  
initRequestToViewNameTranslator(context),ViewResolver是根据ViewName查找View，但有的Handler处理完后并没有设置View也没有设置ViewName，这时就需要从request获取ViewName了，如何从request中获取ViewName就是RequestToViewNameTranslator要做的事情了。RequestToViewNameTranslator在Spring MVC容器里只可以配置一个，所以所有request到ViewName的转换规则都要在一个Translator里面全部实现。  
6. LocaleResolver  
initLocaleResolver(context)，解析视图需要两个参数:一是视图名，另一个是Locale。视图名是处理器返回的,Locale是从哪里来的?这就是LocaleResolver要做的事情。LocaleResolver用于从request解析出Locale，Locale就是zh-cn之类，表示一个区域，有了这个就可以对不同区域的用户显示不同的结果。SpringMVC主要有两个地方用到了Locale:一是ViewResolver视图解析的时候;二是用到国际化资源或者主题的时候。  
7. ThemeResolver
initThemeResolver(context)，用于解析主题。SpringMVC中一个主题对应一个properties文件，里面存放着跟当前主题相关的所有资源、如图片、css样式等。SpringMVC的主题也支持国际化，同一个主题不同区域也可以显示不同的风格。SpringMVC中跟主题相关的类有ThemeResolver、ThemeSource和Theme。主题是通过一系列资源来具体体现的，要得到一个主题的资源，首先要得到资源的名称，这是ThemeResolver的工作。然后通过主题名称找到对应的主题（可以理解为一个配置）文件，这是ThemeSource的工作。  
8. MultipartResolver  
initMultipartResolver(context)，用于处理上传请求。  
9. FlashMapManager  
initFlashMapManager(context)，用来管理FlashMap的，FlashMap主要用在redirect中传递参数。  

## SpringBoot自动配置原理
![image](https://user-images.githubusercontent.com/92672384/153790658-1898f2cc-dd63-4fbf-8683-c405862792f3.png)

## 如何理解Springboot的starter
- starter就是定义一个starter的jar包，写一个@Configuration配置类、将需要的bean定义在里面，然后在starter包的META-INF/spring.factories中写入该配置类,springboot会按照约定来加载该配置类 
- 开发人员只需要将相应的starter包依赖进应用，进行相应的属性配置（使用默认配置时，不需要配置)，就可以直接进行代码开发，使用对应的功能了，比如mybatis-spring-boot--starter，spring-boot-starter-redis

## 什么是嵌入式服务器?为什么使用嵌入式服务器?
节省了下载安装tomcat，应用也不需要再打war包，然后放到webapp目录下再运行,只需要一个安装了Java的虚拟机，就可以直接在上面部署应用程序了,springboot已经内置了tomcat.jar，运行main方法时会去启动tomcat，并利用tomcat的spi机制加载springmvc

## Mybatis的优缺点
- 优点：  
1. 基于SQL语句编程，相当灵活，不会对应用程序或者数据库的现有设计造成任何影响，SQL写在XML里，解除sql与程序代码的耦合，便于统一管理;提供XML标签，支持编写动态SQL语句，并可重用。
2. 与JDBC相比，减少了50%以上的代码量，消除了JDBC大量冗余的代码，不需要手动开关连接;
3. 很好的与各种数据库兼容（因为MyBatis 使用JDBC来连接数据库，所以只要JDBC支持的数据库MyBatis都支持)。
4. 能够与Spring 很好的集成;
5. 提供映射标签，支持对象与数据库的ORM字段关系映射;提供对象关系映射标签，支持对象关系组件维护。

- 缺点：  
1. SQL语句的编写工作量较大，尤其当字段多、关联表多时，对开发人员编写SQL语句的功底有一定要求。
2. SQL语句依赖于数据库，导致数据库移植性差，不能随意更换数据库。

## Mybatis和Hibernate区别
1. hibernate是全自动，而mybatis是半自动
2. hibernate数据库移植性远大于mybatis
3. mybatis相比hibernate需要关心很多细节
4. sql直接优化上，mybatis要比hibernate方便很多

## #{}和${}的区别?
- #{}是预编译处理、是占位符，${}是字符串替换、是拼接符。
- Mybatis在处理#{}时，会将sql中的#{}替换为?号，调用PreparedStatement来赋值;
- Mybatis在处理${}时，就是把${}替换成变量的值，调用Statement来赋值;
- 使用#{}可以有效的防止SQL注入，提高系统安全性。
- #{}的变量替换是在DBMS中、变量替换后，#{}对应的变量自动加上单引号
- ${}的变量替换是在DBMS外、变量替换后，${}对应的变量不会加上单引号

## Mybatis插件运行原理以及开发流程
- Mybatis只支持针对ParameterHandler、ResultSetHandler、StatementHandler、Executor这4种接口的插件，Mybatis使用JDK的动态代理，为需要拦截的接口生成代理对象以实现接口方法拦截功能，每当执行这4种接口对象的方法时，就会进入拦截方法，具体就是InvocationHandler的invoke()方法，拦截那些你指定需要拦截的方法。

- 编写插件:实现Mybatis的 Interceptor接口并复写intercept()方法，然后在给插件编写注解，指定要拦截哪个接口的哪些方法即可，在配置文件中配置编写的插件。

## 索引的原理
![image](https://user-images.githubusercontent.com/92672384/154183103-2954559c-10fa-4791-98bd-0c549e0b0191.png)


## 索引的分类以及对性能的影响
- 普通索引：
最基本的索引，没有任何限制  
普通索引允许被索引的数据列包含重复的值  
一个索引只包含一个列，一个表中可以有多个单列索引  
- 唯一索引：
与"普通索引"类似，不同的就是：索引列的值必须唯一，但允许有空值。  
- 主键索引：
它是一种特殊的唯一索引，不允许有空值。  
- 联合索引：
多列值组成一个索引，专门用于组合搜索，其效率大于索引合并  
- 全文索引：
全文索引主要使用来查找文本中的关键字，全文索引基本上不可能用到。如果有这样的需求，请用第三方框架  

影响：  
- 索引可以极大的提高数据的查询速度。
- 通过使用索引，可以在查询的过程中，使用优化隐藏器，提高系统的性能。
- 但是会降低插入、删除、更新表的速度，因为在执行这些写操作时，还要操作索引文件
- 索引需要占物理空间，除了数据表占数据空间之外，每一个索引还要占一定的物理空间

## 聚簇索引和非聚簇索引区别
- 熟悉的InnoDB和MyISAM两大引擎，InnoDB的默认数据结构是聚簇索引，而MyISAM是非聚簇索引。
- 聚簇索引:将数据存储与索引放到了一块、并且是按照一定的顺序组织的，找到索引也就找到了数据，数据的物理存放顺序与索引顺序是一致的，即:只要索引是相邻的，那么对应的数据一定也是相邻地存放在磁盘上的
- 非聚簇索引:叶子节点不存储数据、存储的是数据行地址，也就是说根据索引查找到数据行的位置再取磁盘查找数据，这个就有点类似一本树的目录，比如我们要找第三章第一节，那我们先在这个目录里面找，找到对应的页码后再去对应的页码看文章。

## 索引的数据结构
- B+树  
B+树是一个平衡的多叉树，从根节点到每个叶子节点的高度差值不超过1，而且同层级的节点间有指针相互链接。在B+树上的常规检索，从根节点到叶子节点的搜索效率基本相当，不会出现大幅波动，而且基于索引的顺序扫描时，也可以利用双向指针快速左右移动，效率非常高。因此，B+树索引被广泛应用于数据库、文件系统等场景。  
- 哈希索引  
哈希索引就是采用一定的哈希算法，把键值换算成新的哈希值，检索时不需要类似B+树那样从根节点到叶子节点逐级查找，只需一次哈希算法即可立刻定位到相应的位置，速度非常快  
如果是等值查询，那么哈希索引明显有绝对优势，因为只需要经过一次算法即可找到相应的键值;前提是键值都是唯一的。如果键值不是唯一的，就需要先找到该键所在位置，然后再根据链表往后扫描，直到找到相应的数据;  
如果是范围查询检索，这时候哈希索引就毫无用武之地了，因为原先是有序的键值，经过哈希算法后，有可能变成不连续的了，就没办法再利用索引完成范围查询检索;  

## 索引的设计原则
- 适合索引的列是出现在where子句中的列，或者连接子句中指定的列
- 基数较小的类，索引效果较差，没有必要在此列建立索引
- 使用短索引，如果对长字符串列进行索引，应该指定一个前缀长度，这样能够节省大量索引空间，如果搜索词超过索引前缀长度，则使用索引排除不匹配的行，然后检查其余行是否可能匹配。
- 不要过度索引。索引需要额外的磁盘空间，并降低写操作的性能。在修改表内容的时候，索引会进行更新甚至重构，索引列越多，这个时间就会越长。所以只保持需要的索引有利于查询即可。
- 定义有外键的数据列—定要建立索引。
- 更新频繁字段不适合创建索引
- 若是不能有效区分数据的列不适合做索引列(如性别，男女未知，最多也就三种，区分度实在太低)
- 尽量的扩展索引，不要新建索引。比如表中已经有a的索引，现在要加(a,b)的索引，那么只需要修改原来的索引即可。
- 对于那些查询中很少涉及的列，重复值比较多的列不要建立索引。
- 对于定义为text、 image和bit的数据类型的列不要建立索引。

## mysql锁的类型?
- 基于锁的属性分类:共享锁、排他锁。
- 基于锁的粒度分类:行级锁(INNODB)、表级锁(INNODB、MYISAM)、页级锁(BDB引擎)、记录锁、间隙锁、临键锁。

- 共享锁
```text
共享锁又称读锁，简称S锁;当一个事务为数据加上读锁之后，其他事务只能对该数据加读锁，而不能对数据加写锁,
直到所有的读锁释放之后其他事务才能对其进行加持写锁。共享锁的特性主要是为了支持并发的读取数据，读取数
据的时候不支持修改，避免出现重复读的问题。
```
- 排他锁
```text
排他锁又称写锁，简称x锁;当一个事务为数据加上写锁时，其他请求将不能再为数据加任何锁，直到该锁释放之后，
其他事务才能对数据进行加锁。排他锁的目的是在数据修改时候，不允许其他人同时修改，也不允许其他人读取。
避免了出现脏数据和脏读的问题。
```
- 表锁
```text
表锁是指上锁的时候锁住的是整个表，当下一个事务访问该表的时候，必须等前一个事务释放了锁才能进行对表进行访问;
特点:粒度大，加锁简单，容易冲突;
```
- 行锁
```text
行锁是指上锁的时候锁住的是表的某一行或多行记录，其他事务访问同一张表时，只有被锁住的记录不能访问，其他的记录可正常访问;
特点:粒度小，加锁比表锁麻烦，不容易冲突，相比表锁支持的并发要高;
```
- 记录锁
```text
记录锁也属于行锁中的一种，只不过记录锁的范围只是表中的某一条记录，记录锁是说事务在加锁后锁住的只是表的某一条记录。
```
- 页锁
```text
页级锁是MySQL中锁定粒度介于行级锁和表级锁中间的一种锁。表级锁速度快，但冲突多，行级冲突少，但速度慢。所以取了折衷的页级，一次锁定相邻的一组记录。
特点:开销和加锁时间界于表锁和行锁之间;会出现死锁;锁定粒度界于表锁和行锁之间，并发度一般
```

## mysql执行计划这么看?
- 执行计划就是sql的执行查询的顺序，以及如何使用索引查询，返回的结果集的行数
- EXPLAIN SELECT * from A where X=? and Y=?

![image](https://user-images.githubusercontent.com/92672384/154395123-a5ca189c-23ad-4973-829a-2510122579a0.png)  

- id :是一个有顺序的编号，是查询的顺序号，有几个select就显示几行。id的顺序是按select出现的顺序增长的。id列的值越大执行优先级越高越先执行，id列的值相同则从上往下执行，id列的值为NULL最后执行
- selectType表示查询中每个select子句的类型
- table:表示该语句查询的表
- type:优化sql的重要字段，也是我们判断sql性能和优化程度重要指标。他的取值类型范围:
  - const:通过索引一次命中，匹配一行数据
  - system:表中只有一行记录，相当于系统表;
  - eq_ref:唯一性索引扫描，对于每个索引键，表中只有一条记录与之匹配
  - ref:非唯一性索引扫描,返回匹配某个值的所有
  - range:只检索给定范围的行，使用一个索引来选择行，一般用于between、<、>;
  - index:只遍历索引树;
  - ALL:表示全表扫描，这个类型的查询是性能最差的查询之一。那么基本就是随着表的数量增多，执行效率越慢。

执行效率：  
ALL < index < range < ref < eq_ref < const < system。最好是避免ALL和index  

- possible_keys:它表示Mysql在执行该sql语句的时候，可能用到的索引信息，仅仅是可能，实际不一定会用到。
- key:此字段是 mysql在当前查询时所真正使用到的索引。他是possible_keys的子集
- key_len:表示查询优化器使用了索引的字节数，这个字段可以评估组合索引是否完全被使用，这也是我们优化sql时，评估索引的重要指标
- rows: mysql查询优化器根据统计信息，估算该sql返回结果集需要扫描读取的行数，这个值相关重要，索引优化之后，扫描读取的行数越多，说明索引设置不对，或者字段传入的类型之类的问题，说明要优化空间越大
- filtered:返回结果的行占需要读到的行(rows列的值)的百分比，就是百分比越高，说明需要查询到数据越准确，百分比越小，说明查询到的数据量大，而结果集很少

## 事务的基本特性和隔离级别?
基本特性：  
- 原子性：指的是一个事务中的操作要么全部成功，要么全部失败。
- 一致性：指的是数据库总是从一个一致性的状态转换到另外一个一致性的状态。
- 隔离性：指的是一个事务的修改在最终提交前，对其他事务是不可见的。
- 持久性：指的是一旦事务提交，所做的修改就会永久保存到数据库中。

隔离级别：  
- read uncommit：读未提交，可能会读到其他事务未提交的数据，也叫做脏读。用户本来应该读取到id=1的用户age应该是10，结果读取到了其他事务还没有提交的事务，结果读取结果age=20，这就是脏读。
- read commit：读已提交，两次读取结果不一致，叫做不可重复读。不可重复读解决了脏读的问题，他只会读取已经提交的事务。用户开启事务读取id=1用户，查询到age=10，再次读取发现结果=20，在同一个事务里同一个查询读取到不同的结果叫做不可重复读。
- repeatable read：可重复复读，这是mysql的默认级别，就是每次读取结果都一样，但是有可能产生幻读。事务 A 根据条件查询得到了 N 条数据，但此时事务 B 删除或者增加了 M 条符合事务 A 查询条件的数据，这样当事务 A 再次进行查询的时候真实的数据集已经发生了变化，但是A却查询不出来这种变化，因此产生了幻读
- serializable：串行，一般是不会使用的，他会给每一行读取的数据加锁，会导致大量超时和锁竞争的问题

## 对慢查询如何优化?
- 首先分析语句，看看是否load了额外的数据，可能是查询了多余的行并且抛弃掉了，可能是加载了许多结果中并不需要的列，对语句进行分析以及重写。
- 分析语句的执行计划，然后获得其使用索引的情况，之后修改语句或者修改索引，使得语句可以尽可能的命中索引。
- 如果对语句的优化已经无法进行，可以考虑表中的数据量是否太大，如果是的话可以进行横向或者纵向的分表。

## ACID靠什么保证?
- A 原子性由undo log日志保证，它记录了需要回滚的日志信息，事务回滚时撤销已经执行成功的sql
- C 一致性由其他三大特性保证、程序代码要保证业务上的一致性
- I 隔离性由MVCC来保证
- D 持久性由内存+redo log来保证，mysql修改数据同时在内存和redo log记录这次操作，宕机的时候可以从redolog恢复
```text
InnoDB redo log写盘，InnoDB 事务进入 prepare状态。
如果前面 prepare 成功，binlog写盘，再继续将事务日志持久化到 binlog，如果持久化成功，
那么、InnoDB事则进入commit 状态(在 redo log 里面写一个commit 记录)
```

## 什么是MVCC?
- 多版本并发控制:读取数据时通过一种类似快照的方式将数据保存下来，这样读锁就和写锁不冲突了，不同的事务session会看到自己特定版本的数据，版本链
- MVCC只在READ COMMITTED和REPEATABLE READ 两个隔离级别下工作。其他两个隔离级别够和MVCC不兼容,因为READ UNCOMMITTED总是读取最新的数据行,而不是符合当前事务版本的数据行。而SERIALIZABLE则会对所有读取的行都加锁。

聚簇索引记录中有两个必要的隐藏列:  
- trx_id:用来存储每次对某条聚簇索引记录进行修改的时候的事务id。
- roll_pointer:每次对哪条聚簇索引记录有修改的时候，都会把老版本写入undo日志中。这个roll_pointer就是存了一个指针，它指向这条聚簇索引记录的上一个版本的位置，通过它来获得上一个版本的记录信息。(注意插入操作的undo日志没有这个属性，因为它没有老版本)

- 开始事务时创建readview,readView维护当前活动的事务id，即未提交的事务id，排序生成一个数组访问数据，获取数据中的事务id(获取的是事务id最大的记录)，对比readview:
- 如果在readview的左边(比readview都小)，可以访问(在左边意味着该事务已经提交)
- 如果在readview的右边(比readview都大）或者就在readview中，不可以访问，获取roll_pointer，取上一版本重新对比(在右边意味着，该事务在readview生成之后出现，在readview中意味着该事务还未提交)
- 已提交读隔离级别下的事务在每次查询的开始都会生成一个独立的ReadView,而可重复读隔离级别则在第一次读的时候生成一个ReadView，之后的读都复用之前的ReadView。
- 这就是Mysql的MVCC,通过版本链，实现多版本，可并发读·写，写-读。通过ReadView生成策略的不同实现不同的隔离级别。

## Mysql主从同步原理
Mysql的主从复制中主要有三个线程: master (binlog dump thread). slave(I/o thread . sQLthread） , Master—条线程和Slave中的两条线程。  
- 主节点binlog，主从复制的基础是主库记录数据库的所有变更记录到binlog。binlog是数据库服务器启动的那一刻起，保存所有修改数据库结构或内容的一个文件。
- 主节点log dump线程，当binlog有变动时，log dump线程读取其内容并发送给从节点。
- 从节点I/o线程接收binlog内容，并将其写入到relay log 文件中。
- 从节点的SQL线程读取relay log文件内容对数据更新进行重放，最终保证主从数据库的一致性。

## 简述MylSAM和lnnoDB的区别
1. InnoDB支持事务，MyISAM不支持
2. InnoDB支持外键，而MyISAM不支持。
3. InnoDB是聚集索引，使用B+Tree作为索引结构，数据文件是和（主键）索引绑在一起的
4. Innodb存储文件有frm、ibd，而Myisam是frm、MYD、MYI
5. InnoDB表必须有唯一索引（如主键）（用户没有指定的话会自己找/生产一个隐藏列Row_id来充当默认主键），而Myisam可以没有
6. InnoDB支持表、行(默认)级锁，而MyISAM支持表级锁

## RDB和AOF?
RDB: Redis DataBase  
在指定的时间间隔内将内存中的数据集快照写入磁盘，实际操作过程是fork一个子进程，先将数据集写入临时文件，写入成功后，再替换之前的文件，用二进制压缩存储。  
好处：  
1. 整个Redis数据库将只包含一个文件dump.rdb，方便持久化。
2. 容灾性好，方便备份。
3. 性能最大化，fork子进程来完成写操作，让主进程继续处理命令，所以是IO最大化。使用单独子进程来进行持久化，主进程不会进行任何IO操作，保证了redis的高性能
4. 相对于数据集大时，比AOF的启动效率更高。

缺点:  
1. 数据安全性低。RDB是间隔一段时间进行持久化，如果持久化之间redis 发生故障，会发生数据丢失。所以这种方式更适合数据要求不严谨的时候)
2. 由于RDB是通过fork子进程来协助完成数据持久化工作的，因此，如果当数据集较大时，可能会导致整个服务器停止服务几百毫秒，甚至是1秒钟。

AOF:Append Only File  
以日志的形式记录服务器所处理的每一个写、册除操作。查询操作不会记录，以文木的方式记录，可以打开文件看到详细的操作记录  

好处：  
1. 数据安全，Redis中提供了3中同步策略，即每秒同步、每修改同步和不同步。
2. 通过 append模式写文件，即使中途服务器宕机也不会破坏已经存在的内容，可以通过redis-check-aof 工具解决数据一致性问题。
3. AOF机制的rewrite模式。定期对AOF文件进行重写，以达到压缩的目的

缺点:  
1. AOF文件比RDB文件大，且恢复速度慢。
2. 数据集大的时候，比rdb启动效率低。
3. 运行效率没有RDB高

AOF文件比RDB更新频率高，优先使用AOF还原数据。AOF比RDB更安全也更大,RDB性能比AOF好,如果两个都配了优先加载AOF.

## Redis过期键的删除策略
（1）：立即删除。在设置键的过期时间时，创建一个回调事件，当过期时间达到时，由时间处理器自动执行键的删除操作。立即删除对cpu是最不友好的。因为删除操作会占用cpu的时间，如果刚好碰上了cpu很忙的时候，比如正在做交集或排序等计算的时候，就会给cpu造成额外的压力。    
（2）：惰性删除。键过期了就过期了，不管。每次从dict字典中按key取值时，先检查此key是否已经过期，如果过期了就删除它，并返回nil，如果没过期，就返回键值。该策略可以最大化地节省CPU资源，却对内存非常不友好。极端情况可能出现大量的过期key没有再次被访问，从而不会被清除，占用大量内存。  
（3）：定时删除。每隔一段时间，对expires字典进行检查，删除里面的过期键。  

## Redis线程模型，以及单线程为什么还效率高

Redis基于Reactor模式开发了网络事件处理器，这个处理器叫做文件事件处理器file event handler。这个文件事件处理器，它是单线程的，所以Redis 才叫做单线程的模型，它采用IO多路复用机制来同时监听多个Socket，根据Socket上的事件类型来选择对应的事件处理器来处理这个事件。  

- 文件事件处理器的结构包含4个部分:多个Socket、IO多路复用程序、文件事件分派器以及事件处理器（命令请求处理器、命令回复处理器、连接应答处理器等)。
- 多个Socket 可能并发的产生不同的操作，每个操作对应不同的文件事件，但是IO多路复用程序会监听多个Socket，会将Socket放入一个队列中排队，每次从队列中取出一个Socket给事件分派器，事件分派器把Socket给对应的事件处理器。然后一个Socket的事件处理完之后，IO多路复用程序才会将队列中的下一个Socket给事件分派器。文件事件分派器会根据每个Socket当前产生的事件，来选择对应的事件处理器来处理。

1）纯内存操作  
2) 核心是基于非阻塞的IO多路复用机制  
3）单线程反而避免了多线程的频繁上下文切换带来的性能问题

## 缓存雪崩、缓存穿透、缓存击穿
[https://github.com/MildLamb/Learn_Redis/blob/main/Redis%E7%BC%93%E5%AD%98%E7%A9%BF%E9%80%8F%E5%92%8C%E9%9B%AA%E5%B4%A9.md](https://github.com/MildLamb/Learn_Redis/blob/main/Redis%E7%BC%93%E5%AD%98%E7%A9%BF%E9%80%8F%E5%92%8C%E9%9B%AA%E5%B4%A9.md)

## Redis事务的实现
[https://github.com/MildLamb/Learn_Redis/blob/main/Redis%E4%BA%8B%E5%8A%A1.md](https://github.com/MildLamb/Learn_Redis/blob/main/Redis%E4%BA%8B%E5%8A%A1.md)

涉及到的一些命令：  
- WATCH命令是一个乐观锁，可以为Redis事务提供check-and-set (CAS)行为。可以监控一个或多个键,一旦其中有一个键被修改（或删除)，之后的事务就不会执行，监控一直持续到EXEC命令。
- MULTI命令用于开启一个事务，它总是返回OK。MULTl执行之后，客户端可以继续向服务器发送任意多条命令，这些命令不会立即被执行，而是被放到一个队列中，当EXEC命令被调用时，所有队列中的命令才会被执行。
- EXEC∶执行所有事务块内的命令。返回事务块内所有命令的返回值，按命令执行的先后顺序排列。当操作被打断时，返回空值nil 。
- DISCARD：通过调用DISCARD，客户端可以清空事务队列，并放弃执行事务，并且客户端会从事务状态中退出。
- UNWATCH：UNWATCH命令可以取消watch对所有key的监控。

## Redis 支持三种集群方案

- 主从复制模式
- Sentinel（哨兵）模式
- Cluster 模式

## Redis主从复制的核心原理
通过执行slaveof命令或设置slaveof选项，让一个服务器去复制另一个服务器的数据。主数据库可以进行读写操作，当写操作导致数据变化时会自动将数据同步给从数据库。而从数据库一般是只读的，并接受主数据库同步过来的数据。一个主数据库可以拥有多个从数据库，而一个从数据库只能拥有一个主数据库。  

全量复制：  
(1) 主节点通过bgsave命令fork子进程进行RDB持久化，该过程是非常消耗CPU、内存(页表复制)、硬盘IO的  
(2) 主节点通过网络将RDB文件发送给从节点，对主从节点的带宽都会带来很大的消耗  
(3) 从节点清空老数据、载入新RDB文件的过程是阻塞的，无法响应客户端的命令

增量复制：  
1. 复制偏移量:执行复制的双方，主从节点，分别会维护一个复制偏移量offset  
2. 复制积压缓冲区︰主节点内部维护了一个固定长度的、先进先出(FIFO)队列作为复制积压缓冲区，当主从节点offset的差距过大超过缓冲区长度时，将无法执行部分复制，只能执行全量复制。  
3. 服务器运行ID(runid):每个Redis节点，都有其运行ID，运行ID由节点在启动时自动生成，主节点会将自己的运行ID发送给从节点，从节点会将主节点的运行ID存起来。从节点Redis断开重连的时候，就是根据运行ID来判断同步的进度:  
  - 如果从节点保存的runid与主节点现在的runid相同，说明主从节点之前同步过，主节点会继续尝试使用部分复制(到底能不能部分复制还要看offset和复制积压缓冲区的情况);
  - 如果从节点保存的runid与主节点现在的runid不同，说明从节点在断线前同步的Redis节点并不是当前的主节点，只能进行全量复制。
