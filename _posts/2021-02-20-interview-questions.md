---
layout: post
title: "2021暑期实习面试题整理"
---

[toc]

## JAVA

### HashMap 与 ConcurrentHashMap 的实现原理是怎样的？ConcurrentHashMap 是如何保证线程安全的？

#### HashMap 实现原理

本质数据结构是一个 bucket 数组，bucket 中存放 entry list 或者 entry tree，entry 中存放 key 与 value。状态：

```java
// Simplified
public class HashMap<K, V> {
    /**
     * The table, initialized on first use, and resized as
     * necessary. When allocated, length is always a power of two.
     */
    Node<K,V>[] table;

    // a cache of entrySet()
    Set<Map.Entry<K, V>> entrySet;

    // The number of key-value mappings in the map. this.size != table.size().
    int size;

    /**
     * The next size value at which to resize. It is compared with size,
     * to invoke resize() to double table.size() and threshold.
     */
    int threshold;

    final float loadFactor;

    int modCount;
}
```

其中，我认为最核心的两个参数是：

- **Initial Capacity**: The capacity is the number of buckets in the hash table,
  The initial capacity is simply the capacity at the time the hash table is
  created. (default value: 16)
- **Load Factor**: The load factor is a measure of how full the hash table is
  allowed to get before its capacity is automatically increased. (default value: 0.75)

还有一个需要注意的参数是：

- **Treeify threshold**: The bin count threshold for using a tree rather than list for a bin. (default value: 8, max: 64)

需要注意的是 HashMap 是不可并行的。关于 HashMap 更详细的解释[link](https://yikun.github.io/2015/04/01/Java-HashMap%E5%B7%A5%E4%BD%9C%E5%8E%9F%E7%90%86%E5%8F%8A%E5%AE%9E%E7%8E%B0/)。

#### ConcurrentHashMap 实现原理

给每一个 node 头部加锁

### Java transient

是在序列化中用到的关键词，如果不想要序列化某个变量，那就把它设为 transient

**transient and static**: 由于 static 的变量不是对象的状态，因此 static 的变量不需要加 transient。

**transient and final**: final variables are directly serialized by their values, so there is no use/impact of declaring final variable as **transient**. There is no compile-time error though.

### 自旋锁与互斥锁的区别

- **互斥锁**：线程会从 sleep（加锁）——>running（解锁），过程中有上下文的切换，cpu 的抢占，信号的发送等开销。

- **自旋锁**：线程一直是 running(加锁—— >解锁)，死循环检测锁的标志位，机制不复杂。

简单来说, 自旋锁是忙等待, 等待时会占用一整个 core, 而互斥锁在等待时会释放 core.

### Java 中垃圾回收机制中如何判断对象需要回收？

- 引用计数法（Reference Counting Collector）：每个对象都有一个引用计数器。优点是执行简单、判定效率高；缺点是难以检测对象的循环应用。
- 根搜索法（Tracing Collector）：这种方法的基本思路是：以一系列 GC 根出发，根据引用遍历找到所有的对象。
  - 注意在开始遍历前，需要暂停线程。这种暂停在 JVM 中被称为安全点(Safe Point)；
  - GC 根对象包括：栈中的对象；方法区中的常量引用的对象；方法区中类静态属性引用的对象；本地方法栈中 JNI 的引用对象；活跃线程。

### 常见的 GC 回收算法有哪些？

1. **Tracing 算法 或 标记—清除算法**：首先标记出所需回收的对象，在标记完成后统一回收所有被标记的对象。

   缺点：标记和清楚过程的效率都不高；标记清除会产生大量的内存碎片；

2. **Compacting 算法 或 标记—整理算法**：与 tracing 算法类似，只不过是将所有的标记的对象先往边界移动，再统一清理。缺点：GC 的暂停时间过长。

3. **Copying 算法**：将内存区域分为两块：对象面与空闲面。当一面用满了之后，将其中活跃的都复制到另一面。这个算法比较适合新生代；

   缺点：内存被压缩了

4. **Adaptive 算法**：结合以上三种算法动态调整。

### JVM 垃圾回收器

1. 串行垃圾回收器
2. 并行垃圾回收器 （jvm 默认）
3. 并发标记扫描垃圾回收器
4. G1 垃圾回收器

### 简述 Synchronized，Volatile，可重入锁的不同使用场景及优缺点

**可重入锁**（ReentrantLock）：粒度相比 synchronized 更小，速度更快，个人认为可以替代 synchronized

**synchronized**：写法更简洁；

**volatile**：是用来修饰变量，从而保证写入该变量时直接写入内存而不是写入缓存。

### synchronized 关键字底层是如何实现的？它与 Lock 相比优缺点分别是什么？

### JVM 中内存模型是怎样的，简述新生代与老年代的区别？

内存模型分为三块：栈、堆、静态方法区。栈存储每个线程的信息，堆存储对象实例。

新生代：新生代又可以划分为一个 Eden 区和两个 Survivor（幸存）区。 按照规定，新对象会首先分配在 Eden 中（如果对象过大，比如大数组，将会直接放到老年代）。在 GC 中，Eden 中的对象会被移动到 survivor 中，直至对象满足一定的年纪（定义为熬过 minor GC 的次数），会被移动到老年代。

老生代：新生代中的实例熬过一定的 GC 后，便会放到老生代

### 乐观锁和悲观锁，以及他们的应用场景

**乐观锁**：总是假设拿数据时别人不会修改，因此不上锁，但是会在更新时判断该数据是否被更改，通过版本号机制或者 CAS 算法实现。乐观锁多用于**多读**的场景，提高吞吐率。

 注：CAS，即 Compare And Swap(比较与交换)，是一种无锁算法，基于硬件原语实现，能够在不使用锁的情况下实现多线程之间的变量同步。 但是存在 ABA 问题，JDK1.5 以后的 compareAndSet 方法提供了原子能力来解决这个问题。

**悲观锁**：总是假设最坏的情况，常见的 synchronized 以及可重入锁就是悲观锁的方式。适用于多写的场景。

### Redis 的持久化存储

提供了两种方法：

- Redis Database，简称 RDB
  - 指定时间间隔进行快照存储
  - 优点：使用单独的子进程进行持久化，主进程不需要进行 IO 操作，保证了 redis 的高性能；
  - 缺点：在持久化之间发生故障，会发生数据丢失。
- Append-only file，简称 AOF
  - 将每次操作写入 log file（或其对应的缓存），你可以指定不同的 fsync 策略
  - 优点：比起 RDB 可以实现更安全的存储
  - 缺点：在高安全策略下，AOF 的速度会比 RDB 慢；AOF 文件相比 RDB 更大；重启速度更慢

### Redis 数据结构

- 字符串
- list
- 哈希
- set
- zset

### MySQL 索引构建方式

1. 哈希索引
2. 全文索引
3. BTree 索引和 B+Tree 索引
4. 聚簇索引和非聚簇索引

### 设计模式

- 创造型模式: 工厂, **抽象工厂**, 建造者, 原型, **单例**;
- 结构型模式: **适配器**, 桥接, 组合, 装饰器, 外观, 享元, **代理**;
- 行为型模式: 责任链, 命令, 解释器, 迭代器, 中介, 备忘录, **观察者**, 状态, 策略, 模板方法, **访问者**.

### Mysql 引擎：Innodb 与 MyISAM 的区别

主要区别：Innodb 支持事务处理、外键、行级锁。

### 常见的负载均衡算法

1. 轮询法，加权轮询法
2. 随机法，加权随机法
3. 源地址哈希法
6. 最小连接数法，最小吞吐率法

## C++

### 简述 STL 中的 map 的实现原理

红黑树，ordered，O(log(n))的插入与查询时间

### 什么是字节对齐，为什么要采用这种机制？

- Such alignment restrictions simplify the design of the hardware forming the
  interface between the processor and the memory system.
- Intel recommends that data be aligned to improve memory system performance.

### 什么是 RAII

_Resource Acquisition Is Initialization_：将资源的生命周期与（获取该资源的）对象的生命周期绑定

虽然 RAII 和 finally 都能保证资源管理时的异常安全，但相对来说，使用 RAII 的代码相对更加简洁。在真实环境中，调用资源释放代码的次数远多于资源类型的个数，所以相对于使用 finally 来说，使用 RAII 能减少代码量。

### C 语言实现多态

```c
struct animal {
    char name[20];
    void (*Speak)();
};

void Animal(struct Animal* this, const char* name, void (*fun)) {
    strcpy(this->name, name);
    this->Speak = fun;
}

// ====================================

struct cat {
    struct animal base;
    char color[20];
}

void CatSpeak(struct cat* this)
{
	printf("miao miao miao miao miao?????\n");
}

void Cat(struct cat* this, const char* color) {
    Animal((struct cat*)this, "cat", CatSpeak);
    strcpy(this->color, color);
}
```

## 操作系统与网络

### 进程间有哪些通信方式？

一共有七种方式

- 管道/匿名管道：是半双工的；只能用于有亲缘关系的进程；每次写入的内容都会添加到管道缓冲区的末尾，并每次从头部读出。
  - 单独构成一种独立的文件系统：管道对于管道两端的进程而言，就是一个文件，但它不是普通的文件，它不属于某种文件系统，而是自立门户，单独构成一种文件系统，并且只存在与内存中。
- FIFO，有名管道：以文件形式存在于文件系统中，使得不具有亲缘关系的进程也能使用。严格遵循 first in first out；FIFO 的名字存在于文件系统中，而内容仅在内存中；
- 信号（Signal）：用于进程间相互通信或者操作的一种机制，可以无关进程状态进行发送。
  - 常用信号：SIGHUP, SIGINT, SIGQUIT, SIGBUS, SIGSEGV, SIGKILL, SIGCLD
  - 信号生命周期和处理流程：
    1. 信号被某个进程产生，并设置传递对象（根据 pid），然后传递给操作系统；
    2. 根据接收进程的设置而选择性地发送给接收者；
    3. 目的进程接收到此信号后，将根据当前进程对此信号设置的预处理方式，暂时终止当前代码的执行，保护上下文（主要包括临时寄存器数据，当前程序位置以及当前 CPU 的状态）、转而执行中断服务程序，执行完成后在回复到中断的位置。当然，对于抢占式内核，在中断返回时还将引发新的调度。
- 消息队列（message queue）
  - 存放在内核中的消息链表，每个消息队列由消息队列标识符表示；
  - 与管道不同的是，消息队列存放在内核中，只有在内核重启(即，操作系统重启)或者显示地删除一个消息队列时，该消息队列才会被真正的删除。
  - 另外与管道不同的是，消息队列在某个进程往一个队列写入消息之前，并不需要另外某个进程在该队列上等待消息的到达。
    - 消息队列是消息的链表,具有特定的格式,存放在内存中并由消息队列标识符标识.
    - 消息队列允许一个或多个进程向它写入与读取消息.
    - 管道和消息队列的通信数据都是先进先出的原则。
    - 消息队列可以实现消息的随机查询,消息不一定要以先进先出的次序读取,也可以按消息的类型读取.比 FIFO 更有优势。
    - 消息队列克服了信号承载信息量少，管道只能承载无格式字 节流以及缓冲区大小受限等缺。
    - 目前主要有两种类型的消息队列：POSIX 消息队列以及 System V 消息队列，System V 消息队列目前被大量使用。系统 V 消息队列是随内核持续的，只有在内核重起或者人工删除时，该消息队列才会被删除。
- 共享内存：是最快的 IPC 形式，但是需要某种同步机制来达到进程间的同步
- 信号量：创建、等待、挂载
- 套接字（socket）：TCP/IP 的基本操作单元，
  - 套接字有两个域：
    - AF_INET 指的是 Internet 网络
    - AF_UNIX 指的是 UNIX 文件系统，他的地址就是文件名。

### 进程通信中的管道实现原理是什么？

在 Linux 中，管道的实现并没有使用专门的数据结构，而是借助了文件系统的 file 结构和 VFS 的索引节点 inode。
通过将两个 file 结构指向同一个临时的 VFS 索引节点，而这个 VFS 索引节点又指向一个物理页面而实现的。
如下图有两个 file 数据结构，但它们定义文件操作例程地址是不同的，其中一个是向管道中写入数据的例程地址，
而另一个是从管道中读出数据的例程地址。
这样，用户程序的系统调用仍然是通常的文件操作，而内核却利用这种抽象机制实现了管道这一特殊操作。

### 读写文件时的 buffer 与 cache

cache：高速缓存，用于 CPU 与内存之间的缓冲；

buffer：I/O 缓存，用于内存与磁盘的缓冲，主要用于缓冲写。

buffer cache 与 page cache：页缓存 page cache 面向的是虚拟内存，块 I/O 缓存 Buffer cache 是面向块设备。

#### page cache

页缓存是面向文件，面向内存的。通俗来说，它位于内存和文件之间缓冲区，文件 IO 操作实际上只和 page cache 交互，不直接和内存交互。page cache 可以用在所有以文件为单元的场景下，比如网络文件系统等等。page cache 通过一系列的数据结构，比如 inode, address_space, struct page，实现将一个文件映射到页的级别.

#### 读写文件基本流程

**读文件**：

1、进程调用库函数向内核发起读文件请求；

2、内核通过检查进程的文件描述符定位到虚拟文件系统的已打开文件列表表项；

3、调用该文件可用的系统调用函数 read()

3、read()函数通过文件表项链接到目录项模块，根据传入的文件路径，在目录项模块中检索，找到该文件的 inode；

4、在 inode 中，通过文件内容偏移量计算出要读取的页；

5、通过 inode 找到文件对应的 address_space；

6、在 address_space 中访问该文件的页缓存树，查找对应的页缓存结点：

（1）如果页缓存命中，那么直接返回文件内容；

（2）如果页缓存缺失，那么产生一个页缺失异常，创建一个页缓存页，同时通过 inode 找到文件该页的磁盘地址，读取相应的页填充该缓存页；重新进行第 6 步查找页缓存；

7、文件内容读取成功。

**写文件**

前 5 步和读文件一致，在 address_space 中查询对应页的页缓存是否存在：

6、如果页缓存命中，直接把文件内容修改更新在页缓存的页中。写文件就结束了。这时候文件修改位于页缓存，并没有写回到磁盘文件中去。

7、如果页缓存缺失，那么产生一个页缺失异常，创建一个页缓存页，同时通过 inode 找到文件该页的磁盘地址，读取相应的页填充该缓存页。此时缓存页命中，进行第 6 步。

8、一个页缓存中的页如果被修改，那么会被标记成脏页。脏页需要写回到磁盘中的文件块。有两种方式可以把脏页写回磁盘：

（1）手动调用 sync()或者 fsync()系统调用把脏页写回

（2）pdflush 进程会定时把脏页写回到磁盘

同时注意，脏页不能被置换出内存，如果脏页正在被写回，那么会被设置写回标记，这时候该页就被上锁，其他写请求被阻塞直到锁释放。

### HTTP 与 HTTPS 有哪些区别？

https 是 HTTP 下加入 SSL 层，https 协议需要到 ca 申请证书，一般免费证书较少，因而需要一定费用。

https 工作原理：

1. 客户端发起 https 请求；

2. 服务端传送证书；

3. 客户端解析证书；

   这部分工作是有客户端的 TLS 来完成的，首先会验证公钥是否有效，比如颁发机构，过期时间等等，如果发现异常，则会弹出一个警告框，提示证书存在问题。

   如果证书没有问题，那么就生成一个随机值，然后用证书对该随机值进行加密。

4. 传送加密信息（对称加密的私钥）；

5. 服务端解密信息，并根据该私钥将请求的数据进行对称加密，传输加密后的信息。

6. 客户端解密信息。

### 简述 TCP 三次握手以及四次挥手的流程。为什么需要三次握手以及四次挥手？

#### 三次握手

1. 第一次握手：client 发送 syn 给 server；

2. 第二次握手：server 发送 ack+syn 给 client；
3. 第三次握手：client 发送 ack 给 server；

目的是 client 与 server 确认双方都具有*发送*与*接受*数据的能力。

需要第三次握手的原因：第一、二次握手后，服务端并不知道客户端的接收能力以及自己的发送能力是否正常。

#### 四次挥手

1. 第一二次挥手：server 发送 fin 给 client，client 返回 ack；
2. 第三四次挥手：client 发送 fin 给 server，server 返回 ack。

第一二次的目的是 server 告知 client 自己已经发送完毕，有结束的意愿；

第三四次的目的是 client 告诉 server 自己的数据已经发送完毕，可以结束了。

### 什么是跨域，什么情况下会发生跨域请求？

**跨域请求**就是指：当前发起请求的域与该请求指向的资源所在的域不一样。这里的域指的是这样的一个概念：我们认为若协议 + 域名 + 端口号均相同，那么就是同域。

### 什么是幂等操作，如何实现

在编程中一个幂等操作的特点是其任意多次执行所产生的影响均与一次执行的影响相同。

实现的常见思路：

1. MVCC 多版本并发控制：在数据更新时更新版本号，使得版本号不一致的操作无法操作成功；
2. 去重表：
3. Token 机制：

<!--
java
---

## 简述 Spring AOP 的原理

## 简述 Java 的反射机制及其应用场景

## Java 是如何实现线程安全的，哪些数据结构是线程安全的？

## 简述 ArrayList 与 LinkedList 的底层实现以及常见操作的时间复杂度

## Java 类的加载流程是怎样的？什么是双亲委派机制？

## Java 中 sleep() 、wait() 、yield()的区别

## Java 线程池里的 arrayblockingqueue 与 linkedblockingqueue 的使用场景和区别

## JVM 是怎么去调优的？简述过程和调优的结果

## String 类能不能被继承？为什么？

## 线程池是如何实现的？简述线程池的任务策略

## 简述 vector 实现原理

## C++ 中多态是怎么实现的

## 简述 C++ 右值引用与转移语义

## 简述 C++ 中智能指针的特点，简述 new 与 malloc 的区别

## STL 中 vector 与 list 具体是怎么实现的？常见操作的时间复杂度是多少？

## C++ 11 有什么新特性

## 深拷贝与浅拷贝区别是什么？

## C++ 中虚函数与纯虚函数的区别

## 编译时链接有几种方式？静态链接和动态链接的区别是什么？

## 内存中堆与栈的区别是什么？

## const、static 关键字有什么区别

## 类默认的构造函数是什么？

## 简述 C++ 中内存对齐的使用场景

## 只定义析构函数，会自动生成哪些构造函数？

## 变量的声明和定义有什么区别？

## C/C++内存存储区有哪几种类型？

## 简述 C++ 从代码到可执行二进制文件的过程

### 从输入 URL 到展现页面的全过程

### TCP 中常见的拥塞控制算法有哪些？

### DNS 查询服务器的基本流程是什么？DNS 劫持是什么？

### Cookie 和 Session 的关系和区别是什么？

### 简述 HTTP 1.0，1.1，2.0 的主要区别

### TCP 怎么保证可靠传输？

### 什么是 TCP 粘包和拆包？

### TCP 中 SYN 攻击是什么？如何防止？

### 简述 WebSocket 是如何进行传输的

### 从系统层面上，UDP如何保证尽量可靠？

### TCP 四次挥手的时候 CLOSE_WAIT 的话怎么处理？

### 简述 socket 中 select 与 epoll 的使用场景以及区别，epoll 中水平触发以及边缘触发有什么不同？

### Linux 下如何排查 CPU 以及 内存占用过多？

### Linux 下如何查看 CPU 荷载，正在运行的进程，某个端口对应的进程？

### 如何调试服务器内存占用过高的问题？

### Linux 如何查看实时的滚动日志？

### 线程有多少种状态，状态之间如何转换

### 进程有多少种状态？

-->