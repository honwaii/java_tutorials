1. Java类加载过程

   加载 -> 验证 -> 准备 -> 解析 -> 初始化 -> 使用 -> 卸载

2. 描述一下JVM加载Class文件的原理机制

   Java语言是一种具有动态性解释型语言，类(class)只有被加载到jvm后才能运行。
   当运行指定程序时，JVM会将编译生成的.class文件按照需求和一定的规则加载到内存中， 并组织为一个完整的java应用程序。 这个加载过程是由类加载器完成，具体来说，就是由ClassLoader和他的子类来实现的。 类加载器本身也是一个类，， 器实质是把类文件从硬盘读取到内存中。

   类的加载方式分为**隐式加载**和**显式加载**。
   隐式加载指的是程序在使用new等方式创建对象时，会隐式地调用类的加载器把对应的类加载到JVM中。 
   显式加载指的是通过直接调用class.forName()方法来把所需的类加载到JVM中。

   任何一个工程项目都是由许多类组成的，当程序启动时，只需把需要的类加载到JVM中，其他类只有被使用的时候才会被加载，这样可加快加载速度，也可节约程序运行时对内存的开销。 java中每个类或接口都对应一个.class文件，这些文件可看成是一个个可被动态加载的单元，故当部分类修改时，只需重新编译变化的类即可，而不用编译所有文件，加快了编译速度。

   java语言中，类的加载时动态的，并不会一次性将所有类全部加载后在运行，而是保证程序运行的基础类(如基类)完全加载到JVM中，至于其他类，需要时再加载。

3. Java 内存分配：

   堆，栈、虚拟机栈、程序计数器、方法区

4. GC是什么？为什么要有GC？
    GC是垃圾收集的意思（Garbage Collection），内存处理是编程人员容易出现问题的地方，忘记或者错误地内存回收会导致程序或系统的不稳定甚至崩溃， Java 提供的GC 功能可以自动监测对象是否超过作用域从而达到自动回收内存的目的，Java 语言没有提供释放已分配内存的显示操作方法。 

5. 简述JAVA垃圾回收机制。
    在  Java 中，程序员是不需要显示的去释放一个对象的内存的，而是由虚拟机自行执行。在JVM 中，有一个垃圾回收线程，它是低优先级的，在正常情况下是不会执行的，**只有在虚拟机空闲或者当前堆内存不足时，才会触发执行**，扫面那些没有被任何引用的对象，并将它们添加到要回收的集合中，进行回收。 

6. 如何判断一个对象是否存活？（或者  GC 对象的判定方法）

     判断一个对象是否存活有两种方法： 

     **a) 引用计数法** 
     所谓引用计数法就是给**每一个对象设置一个<u>引用计数器**</u>，每当有一个地方引用这个对象时，就将计数器加1，引用失效时，计数器就减1。当一个对象的引用计数器为0时，说明此对象没有被引用，也就是“死对象”,将会被垃圾回收. 引用计数法有一个缺陷就是**无法解决循环引用问题**，也就是说当对象  A 引用对象  B，对象  B 又引用者对象  A，那么此时  A、B 对象的引用计数器都不为零，也就造成无法完成垃圾回收，所以**主流的虚拟机都没有采用这种算法**。 

     **b) 可达性算法（引用链法）** 

     该算法的思想是：从一个被称为 **GC Roots 的对象开始向下搜索**，如果一个对象到  GC Roots 没有任何引用链相连时，则说明此对象不可用。 

     在  Java 中可以作为  GC Roots 的对象有以下几种： 
       **•  虚拟机栈中引用的对象**** 
       **•  方法区类静态属性引用的对象** 
       **•  方法区常量池引用的对象** 
       **•  本地方法栈JNI引用的对象**

     虽然这些算法可以判定一个对象是否能被回收，但是**当满足上述条件时，一个对象比不一定会被回收**。当一个对象不可达  GC Root 时，这个对象并不会立马被回收，而是出于一个死缓阶段，**若要被真正的回收需要经历两次标记.** 如果对象在可达性分析中没有与  GC Root 的引用链，那么此时就会被**第一次标记并且进行一次筛选**，筛选的条件**是是否有必要执行 finalize() 方法**：

     当对象没有覆盖  finalize() 方法或者已被虚拟机调用过，那么就认为是没必要的。

     如果该对象有必要执行finalize() 方法，那么这个对象将会放在一个称为  **F-Queue** 的对队列中，虚拟机会触发一个  Finalize() 线程去执行，此线程是低优先级的，并且虚拟机不会承诺一直等待它运行完，这是因为如果 finalize() 执行缓慢或者发生了死锁，那么就会造成  F-Queue 队列一直等待，造成了内存回收系统的崩溃。GC 对处于  F-Queue 中的对象进行第二次被标记，这时，该对象将被移除”  即将回收” 集合，等待回收。 

7. 垃圾回收的优点和原理

   Java 程序员在编写程序的时候不再需要考虑内存管理。由于有个垃圾回收机制，
   Java 中的对象不再有“作用域”的概念，只有对象的引用才有"作用域"。垃圾回收可以有效的防止内存泄露，有效的使用可以使用的内存。

   回收机制有**分代复制垃圾回收**和**标记垃圾回收**，**增量垃圾回收**。

8. 垃圾回收器的基本原理是什么？垃圾回收器可以马上回收内存吗？有什么办法主动通知虚拟机进行垃圾回收？ 

   **对于GC来说，当程序员创建对象时，GC 就开始监控这个对象的地址、大小以及使用情况。通常，GC 采用有向图的方式记录和管理堆（heap）中的所有对象。通过这种方式确定哪些对象是”可达的”，哪些对象是”不可达的”。当  GC 确定一些对象为“不可达”时，GC 就有责任回收这些内存空间。**

   可以。程序员可以手动执行  **System.gc()**，通知  GC 运行，但是  Java 语言规范并不保证GC一定会执行。

9. Java 中会存在内存泄漏吗，请简单描述。

   **内存泄露**：<u>指一个不再被程序使用的对象或变量一直被占据在内存中</u>。Java 中有垃圾回收机制，它可以保证一对象不再被引用的时候，即对象变成了孤儿的时候，对象将自动被垃圾回收器从内存中清除掉。由于  Java 使用有向图的方式进行垃圾回收管理，可以消除引用循环的问题，例如有两个对象，相互引用，只要它们和根进程不可达的，那么  GC 也是可以回收它们的。

   Java 中的内存泄露的情况：**长生命周期的对象持有短生命周期对象的引用就很可能发生内存泄露**，尽管短生命周期对象已经不再需要，但是因为长生命周期对象持有它的引用而导致不能被回收，这就是  Java 中内存泄露的发生场景，通俗地说，就是程序员可能创建了一个对象，以后一直不再使用这个对象，这个对象却一直被引用，即这个对象无用但是却无法被垃圾回收器回收的，这就是  java 中可能出现内存泄露的情况，例如，**缓存系统**，我们加载了一个对象放在缓存中  (例如放在一个全局 map 对象中)，然后一直不再使用它，**这个对象一直被缓存引用，但却不再被使用**。 

   如果一个**外部类的实例对象的方法返回了一个内部类的实例对象**，这个内部类对象被长期引用了，即使那个外部类实例对象不再被使用，但由于内部类持久外部类的实例对象，这个外部类对象将不会被垃圾回收，这也会造成内存泄露。 

   内存泄露的另外一种情况：当一个对象被存储进  HashSet 集合中以后，就不能修改这个对象中的那些参与计算哈希值的字段了，否则，对象修改后的哈希值与最初存储进HashSet 集合中时的哈希值就不同了，在这种情况下，即使在  contains 方法使用该对象的当前引用作为的参数去HashSet 集合中检索对象，也将返回找不到对象的结果，这也会导致无法从  HashSet 集合中单独删除当前对象，造成内存泄露。 

10. 深拷贝和浅拷贝

    即复制和克隆

    浅拷贝: 对对象中的数据成员进行简单赋值，如果存在动态成员或者指针就会报错。 
    深拷贝: 对对象中存在的动态成员或指针重新开辟内存空间。 

11. System.gc() 和  Runtime.gc() 会做什么事情？

    这两个方法用来提示  JVM 要进行垃圾回收。但是，立即开始还是延迟进行垃圾回收是取决于  JVM 的。 

12. finalize() 方法什么时候被调用？析构函数  (finalization) 的目的是什么？ 

    垃圾回收器（garbage colector）决定回收某对象时，就会运行该对象的  finalize() 方法 但是在  Java 中很不幸，如果内存总是充足的，那么垃圾回收可能永远不会进行，也就是说  filalize() 可能永远不被执行。

13. 十三. 如果对象的引用被置为  null，垃圾收集器是否会立即释放对象占用的内存？ 
    不会，在下一个垃圾回收周期中，这个对象将是可被回收的。 

14. 什么是分布式垃圾回收（DGC）？它是如何工作的？

    DGC 叫做分布式垃圾回收。RMI 使用  DGC 来做自动垃圾回收。因为  RMI 包含了跨虚拟机的远程对象的引用，垃圾回收是很困难的。DGC 使用**引用计数算法**来给远程对象提供自动内存管理。

15. 串行（serial）收集器和吞吐量（throughput）收集器的区别是什么？ 

    吞吐量收集器使用并行版本的新生代垃圾收集器，它用于**中等规模和大规模数据**的应用程序。 而**串行收集器对大多数的小应用**（在现代处理器上需要大概  100M 左右的内存）就足够了。 

16. 简述  Java 内存分配与回收策率以及 Minor GC 和 Major GC

    •  对象优先在堆的  Eden 区分配 
    •  大对象直接进入老年代 
    •  长期存活的对象将直接进入老年代 

    **当Eden 区没有足够的空间进行分配时，虚拟机会执行一次 Minor GC**。**Minor GC 通常发生在新生代的  Eden 区**，在这个区的对象生存期短，往往发生GC的频率较高，回收速度比较快；**Full GC/Major GC 发生在老年代**，一般情况下，**触发老年代  GC 的时候不会触发 Minor GC**，但是通过配置，可以在  Full GC 之前进行一次 Minor GC 这样可以加快老年代的回收速度。 

17. Java 中垃圾收集的方法有哪些？

    **标记  - 清除**：这是垃圾收集算法中最基础的，它的思想就是标记哪些要被回收的对象，然后统一回收。

    会有两个主要问题：

    1. 效率不高，标记和清除的效率都很低； 
    2. 会产生大量不连续的内存碎片，导致以后程序在分配较大的对象时，由于没有充足的连续内存而提前触发一次  GC 动作。  

    **复制算法**：为了解决效率问题，复制算法将可用内存按容量划分为相等的两部分，然后每次只使用其中的一块，当一块内存用完时，就将还存活的对象复制到第二块内存上，然后一次性清楚完第一块内存，再将第二块上的对象复制到第一块。这种方式，内存的
    代价太高，每次基本上都要浪费一半的内存。

    于是将该算法进行了改进，内存区域不再是按照1:1 去划分，而是将内存划分为  8:1:1 三部分，较大那份内存叫 Eden 区，其余是两块较小的内存区叫  Survior 区。每次都会优先使用Eden区，若Eden区满，就将对象复制到第二块内存区上，然后清除  Eden 区，如果此时存活的对象太多，以至于  Survivor 不够时，会将这些对象通过分配担保机制复制到老年代中。（java 堆又分为新生代和老年代） 

    **标记  - 整理：**该算法主要是为了解决标记  - 清除，**产生大量内存碎片的问题**；当对象存活率较高时，也解决了复制算法的效率问题。它的不同之处就是**在清除对象的时候现将可回收对象移动到一端，然后清除掉端边界以外的对象**，这样就不会产生内存碎片了。

    **分代收集**：<u>现在的虚拟机垃圾收集大多采用这种方式</u>，它<u>根据对象的生存周期，将堆分为新生代和老年代</u>。在新生代中，由于对象生存期短，每次回收都会有大量对象死去，那么这时就采用复制算法。老年代里的对象存活率较高，没有额外的空间进行分配担保。  

18. 什么是类加载器，类加载器有哪些？ 

    实现通过类的全限定名获取该类的二进制字节流的**代码块**叫做类加载器。 

    主要有以下**四种类加载器**： 

    •  **启动类加载器**（Bootstrap ClassLoader）用来加载  Java 核心类库，无法被  Java 程序直接引用。 
    •  **扩展类加载器**（extensions class loader）：它用来加载  Java 的扩展库。Java 虚拟机的实现会提供一个扩展库目录。该类加载器在此目录里面查找并加载  Java 类。 
    • **系统类加载器**（system class loader）：它根据  Java 应用的类路径（CLASSPATH）来加载  Java 类。一般来说，Java 应用的类都是由它来完成加载的。可以通过 ClassLoader.getSystemClassLoader() 来获取它。 
    •  **用户自定义类加载器**，通过继承  java.lang.ClassLoader 类的方式实现。 

19.  **类加载器双亲委派模型机制**？ 

    <u>当一个类加载器收到了类加载请求时，不会自己先去加载这个类，而是将其委派给父类加载器，由父类去加载，依次向上，如果此时父类不能加载，反馈给子类，由子类去完成类的加载。</u> 

20.  JVM 的内存模型以及分区情况和作用

    **方法区**：用于存储虚拟机加载的类信息、常量，静态变量等数据,

    **堆**：存放对象实例，所有的对象和数组都要在堆上分配。是JVM所管理的内存中最大的一块 区域。

    **栈**：Java方法执行的内存模型：存储局部变量表f操作数栈，动态链接，方法出口等信息。 生命周期与线程相同4

    **本地方法栈**：作用与虚拟机栈类似，不同点本地方法栈为native方法执行服务，虚拟机栈为虚拟机执行的Java方法服务。

    **程序计数器：**当前线程所执行的<u>行号指示器</u>。是JVM内存区域最小的一块区域。执行字节码工作时就是<u>利用程序计数器来选取下一条需要执行的字节码指令</u>。

21. **JVM 对象创建步骤流程是什么？**（重要）

    ![](.\java对象创建流程.png)

    第1步：虚拟机遇到一个new指令，首先将去检查这个指令的参数是否能在常量池中定位到这个类的符号引用，并且检査这个符号引用的类是否己经被加载&解析&初始化。

    第2步：如果类已经被加载那么进行第3步；如果没有进行加载，那么就就需要先进行 类的加载。

    第3步：类加载检查通过之后，接下来进行新生对象的内存分配。

    第4步：对象生成需要的内存大小在类加载完成后便可完全确定，为对象分配空间等同 于把一块确定大小内存从Java堆中划分出来

    第5步：内存大小的划分分为两种情况：

    第一种情况：JVM的内存是规整的，所有的使用的内存都放到一边，空闲的内存在另外 一边，中间放一个指针作为分界点的指示器. 那么这时候分配内存就比较简单，只要 将指针向空闲空间那边挪动一段与对象大小相同的距离。这种就是“**指针碰撞**'。

    第二种情况：JVM的内存不是规整的，也就是说已使用的内存与未使用的内存相互交 错。这时候就没办法利用指正碰撞了。这时就需要维护一张表，用于记录哪些内存可用，在分配的时候从列表中找到一块足够大的空间划分给对象实例，并更新到记录上。

    第6步：空间申请完成之后.JVM需要将内存的空间都初始化为0值。如果使用TLAB, 就可以在TLAB分配的时候就可以进行该工作.

    第7步：JVM对对象进行必要的设置。例如，这个对象是哪个类的实例、对象的哈希 码、GC年代等信息。

    第8步：完成了上面的步骤之后从JVM来看一个对象基本上完成了 .但从Java程序代码来看，对象创建才刚刚开始，需要执行<init>方法，按照程序中设定的初始化 操作初始化,这时候一个真正的程序对象生成了。

22.  介绍一下**JVM中垃圾收集器有哪些**？ 他们的特点分别是什么？

    **1）新生代垃圾收集器**
    a. Serial收集器 --> 使用算法：复制算法

    特点：

    Serial收集器只能使用一条线程进行垃圾收集工作，并且在进行垃圾收集的时候，所有 的工作线程都需要停止工作，等待垃圾收集线程完成以后，其他线程才可以继续工作。

    b. ParNew收集器 --> 使用算法：复制算法

    特点：

    ParNew垃圾收集器是Serial收集器的多线程版本，为了利用CPU多核多线程的优势， ParNew收集器可以运行多个收集线程来进行垃圾收集工作，这祥可以提高垃圾收集过裡 的效率。

    c. Parallel Scavenge 收集器 --> 使用算法：复制算法

    特点：

    Parallel Scavenge收集器是一款多线程的垃圾收集器，但是它又和ParNew有很大的不同
    Parallel Scavenge收集器和其他收集器的关注点不同.其他收集器，比如ParNew和CMS 这些收集器，它们主要关注的是如何缩短垃圾收集的时间。而Parallel Scavenge收集器 关注的是如何控制系统运行的吞吐量，这里说的吞吐量，指的是CPU用于运行应用程序 的时间和CPU总时间的占比，吞吐量 = 代码运行时间 /（代码运行时间+垃圾收集时间)。如果虚拟机运行的总的CPU时间垦100分钟，而用于执行垃圾收集的时间为1分 钟，那么吞吐量就是99%。

    **2) 老年代垃圾收集器**
    a. Serial Old收集器 --> 使用算法：标记-整理

    特点：

    Serial Old收集器是Serial收集器的老年代版本。这款收集器主要用于客户端应用程序中 作为老年代的垃圾收集器，也可以作为服务端应用程序的垃圾收集器。

    b. Parallel Old 收集器 --> 使用算法：标记-整理

    特点:

    Parallel Old收集器是Parallel  Scavenge收集器的老年代版本这个收集器是在JDK1.6版本 中出现的，所以在JDK1.6之前，新生代的Parallel Scavenge只能和 Serial Old这款单线程 的老年代收集器配合使用。Parallel Old垃圾收集器和Parallel Scavenge收集器一样，也 是一款关注吞吐量的垃圾收集器，和Parallel Scavenge收集器一起配合，可以实现对 Java堆内存的吞吐置优先的垃圾收集策略。

    c. CMS收集器 -->  使用算法：复制+标记清除

    特点：

    CMS收集器是目前老年代收集器中比较优秀的垃圾收集器。CMS  Concurrent Mark Sweep,从名字可以君出，这是一款使用“标记-清除”算法的并发收集器。

    CMS垃圾收集器是一款以获取最短停顿时间为目标的收集器，如下图所示：

    ![](.\cms垃圾收集器.png)

    从图中可以看出，CMS收集器的工作过程可以分为4个阶段:
    • 初始标记(CMS initial mark)阶段

    • 并发标记(CMS concurrent mark)阶段

    • 重新标记(CMS remark)阶段

    • 并发清除(CMS concurrent sweep)阶段

    **3) 其他**
    G1垃圾收集器 --> 使用算法：复制+标记整理

    特点：

    主要步驟：初始标记，并发标记，重新标记，复制淸除。

23. <u>什么是Class文件？ Class文件主要的信息结构有哪些</u>？

    Class文件是一组以8位字节为基础单位的二进制流，各个数据项严格按顺序排列。

    Class文件格式采用一种类似于C语言结构体的伪结构来存储数据。这样的伪结构仅仅有两种数据类型：无符号数和表。

    无符号数：是基本数据类型，以u1、u2、u4、u8分别代表1个字节、2个字节、4个字 节、8个字节的无符号数，能够用来描写叙述数字、索引引用、数量值或者依照UTF-8 编码构成的字符串值。

    表：由多个无符号数或者其它表作为数据项构成的复合数据类型。全部表都习惯性地以 _info结尾。

24. JVM数据运行区，哪些会造成OOM的情况？

    除了数据运行区，其他区域均有可能造成OOMd的情况。

    堆溢出：java.lang.OutOfMemoryError: Java heap space

    栈溢出：java.lang.StackOverflowError

    永久代溢出：java.lang.OutOfMemoryError: PermGen space

25. <u>详细介绍一下对象在分带内存区域的分配过程？</u>

    1. JVM会试图为相关Java对象在Eden中初始化一块内存区域。
    2. 当Eden空间足够时，内存申请结束；否则到下一步。
    3. JVM试图释放在Eden中所有不活跃的对象（这属于1或更高级的垃圾回收）。释 放后若Eden空间仍然不足以放入新对象，则试图将部分Eden中活跃对象放入 Survivor 区。
    4. Survivor区被用来作为Eden及Old的中间交换区域，当Old区空间足够时， Survivor区的对象会被移到Old区，否则会被保留在Survivor区。
    5. 当old区空间不够时，JVM会在Old区进行完全的垃圾收集.
    6. 完全垃圾收集后，若Survivor及Old区仍然无法存放从Eden复制过来的部分对 象，导致JVM无法在Eden区为新对象创建内存区域，则出现“out of memory”错 误。

26. G1 和CMS两个垃圾收集器的对比

    **a. 细节方面不同**
    1) G1在压缩空间方面有优势。

    2) 通过将内存空间分成区域（Region)的方式避免内存碎片问题。

    3) Eden, Survivor, Old区不再固定、在内存使用效率上来说更灵活。

    4) G1可以通过设置预期停顿时间（Pause Time)来控制垃圾收集时间避免应用雪崩 现象。

    5)  G1在回收内存后会马上同时做合并空闲内存的工作、而CMS默认是在STW (stop the world)的时候做。

    6) G1会在Young GC中使用、而CMS只能在Old区使用.
    **b. 整体内容不同**

    吞吐呈优先：G1

    响应优先：CMS

    1) CMS的缺点是对cpu的要求比较高。G1是将内存化成了多块，所有对内段的大小有很大 的要求*
    2) CMS是清除，所以会存在很多的内存碎片。G1是整理，所以碎片空间较小。

27. 什么是内存溢出，内存泄露？他们的区别是什么?
    **内存溢出out of memory,** <u>是指程序在申请内存时，没有足够的内存空间供其使用，出 现 out of memory;</u>

    **内存泄露memory leak**,<u>是指程序在申请内存后，无法释放已申请的内存空间</u>，一次内

    存泄露危害可以忽略，但内存泄露堆积后果很严重，无论多少内存，迟早会被占光。

    内存溢出就是你要求分配的内存超出了系统能给你的，系统不能满足需求，于是产生溢 出。
    内存泄漏是指你向系统申请分配内存进行使用（new),可是使用完了以后却不归还 (delete),结果你申请到的那块内存你自己也不能再访问（也许你把它的地址给弄丟 了），而系统也不能再次将它分配给需要的程序。

28. 引起类加载操作的行为有哪些?
    1)遇到 new、getstatic, putstatic 或 invokestatic 这四条字节码指令。

    2)反射调用的时候，如果类没有进行过初始化，则需要先触发其初始化.

    3)子类初始化的时候，如果其父类还没初始化，则需先触发其父类的初始.

    4)虚拟机执行主类的时候（有main (String[] args) ).

    5) SJDK1.7动态语言支持.

29.  Full GC、MajorGC、Minor GC 之间区别?
    Minor GC:

    从新生代空间（包括Eden和Survivor区域）回收内存被称为MinorGC,
    Major GC:

    清理Tenured区，用于回收老年代，出现Major GC通常会出现至少一次Minor GC。 FuUGC：

    Full GC是针对整个新生代、老年代、元空间（metaspace, java8以上版本取代pemi gen)的全局范围的GC。

30. 什么时候触发Full GC?
    1) 调用System.gc时，系统建议执行FullGC,但是不必然执行。 

    2) 老年代空间不足。

    3) 方法区空间不足。

    4）通过Minor GC后进入老年代的平均大小大于老年代的可用内存。

    5）由 Eden 区、survivor space1 (From Space)区向 survivor space2 (To Space)区复 制时，对象大小大于To Space可用内存，则把该对象转存到老年代，且老年代的 可用内存小于该对象大小。

31. 什么情况下会出现栈溢出？

    1）方法创建了一个很大的对象，如List, Array。

    2）是否产生了循环调用、死循环。

    3) 是否引用了较大的全局变量.

32. 说一下强引用、软引用、弱引用、虚引用以及他们之间和GC的关 系
    1) 强引用：new出的对象之类的引用，只要强引用还在，永远不会回收。

    2) 软引用：引用但非必须的对象，内存溢出异常之前，回收。

    3) 弱引用：非必须的对象，对象能生存到下一次垃圾收集发生之前。

    4) 虚引用：对生存时间无影响，在垃圾回收时得到通知。

33. Eden和Survivor的比例分配是什么情况？为什么？


    默认比例 8：1.大部分对象都是朝生夕死。

    复制算法的基本思想就是将内存分为两块，每次只用其中一块，当这一块内存用完，就 将还活着的对象复制到另外-块上面。复制算法不会产生内存碎片。

34. CPU资源占用过高如何分析

    1) top查看当前CPU情况，找到占用CPU过高的进程PID=123。

    2) top -H -p123找出两个CPU占用较高的线程，记录下来PID=2345,3456 转换为十六进制。

    3) jstack -I 123 > temp,txt打印出当前进程的线程栈。

    4) 查找到对应于第二步的两个线程运行栈，分析代码。

35. OOM异常排查
    1)使用top指令査询服务器系统状态。

    2) ps -aux|grep java 找出当前 Java 进程的 PID

    3) jstat -gcutil pid interva丨查看当前 GC 的状态。

    4) jmap -histo:live pid可用统计存活对象的分布情况，从高到低查看占据内存最多的对象。

    5) jmap -dump:fomnat=b，file=文件名[pid]利用 Jmap dump。

    6) 使用性能分析工具对上一dump出来的文件进行分析，工具有MAT等。

36. SafePoint是什么 ？

    比如GC的时候必须要等到Java 线程都进入到safepoint的时候VMThread 才能开始
    执行GC， 

    1.  循环的末尾  (防止大循环的时候一直不进入safepoint，而其他线程在等待它进入
    safepoint) 
    2.  方法返回前 
    3.  调用方法的call 之后 
    4.  抛出异常的位置 

37. GC的三种收集方法：标记清除、标记整理、复制算法的原理与特点，分别用在什么地方，如果让你优化收集方法，有什么思路？ 

    标记清除：先标记，标记完毕之后再清除，效率不高，会产生碎片 。

    复制算法：分为8：1 的Eden 区和survivor 区，就是上面谈到的YGC 。
    标记整理：标记完毕之后，让所有存活的对象向一端移动 。

38. Java内存模型 

    java内存模型(JMM)是线程间通信的控制机制。**JMM定义了主内存和线程之间抽象关系**。线程之间的共享变量存储在主内存（main memory）中，每个线程都有一个私有的本地内存（local memory），本地内存中存储了该线程以读/写共享变量的副本。本地内存是
    JMM 的一个抽象概念，并不真实存在。它涵盖了缓存，写缓冲区，寄存器以及其他的硬件和编译器优化。Java内存模型的抽象示意图如下： 

    ![](.\jmm.png)

    从上图来看，线程A与线程B 之间如要通信的话，必须要经历下面2 个步骤： 
    1. 首先，线程A把本地内存A中更新过的共享变量刷新到主内存中去。 
    2. 然后，线程B 到主内存中去读取线程A之前已更新过的共享变量。 