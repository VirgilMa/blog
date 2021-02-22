---
layout: post
title: "JAVA 面试题整理"
---

## HashMap 与 ConcurrentHashMap 的实现原理是怎样的？ConcurrentHashMap 是如何保证线程安全的？

### HashMap 实现原理

本质数据结构是一个bucket数组，bucket中存放entry list或者entry tree，entry中存放key与value。状态：

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

需要注意的是HashMap是不可并行的。关于HashMap更详细的解释[link](https://yikun.github.io/2015/04/01/Java-HashMap%E5%B7%A5%E4%BD%9C%E5%8E%9F%E7%90%86%E5%8F%8A%E5%AE%9E%E7%8E%B0/)。

### ConcurrentHashMap实现原理





## Java transient

是在序列化中用到的关键词，如果不想要序列化某个变量，那就把它设为transient

**transient and static**: 由于static的变量不是对象的状态，因此static的变量不需要加transient。

**transient and final**: final variables are directly serialized by their values, so there is no use/impact of declaring final variable as **transient**. There is no compile-time error though.

## 自旋锁与互斥锁的区别

- **互斥锁**：线程会从sleep（加锁）——>running（解锁），过程中有上下文的切换，cpu的抢占，信号的发送等开销。

- **自旋锁**：线程一直是running(加锁——>解锁)，死循环检测锁的标志位，机制不复杂。

简单来说, 自旋锁是忙等待, 等待时会占用一整个core, 而互斥锁在等待时会释放core.

## Java 中垃圾回收机制中如何判断对象需要回收？常见的 GC 回收算法有哪些？

## 简述 Synchronized，Volatile，可重入锁的不同使用场景及优缺点

## synchronized 关键字底层是如何实现的？它与 Lock 相比优缺点分别是什么？

## JVM 中内存模型是怎样的，简述新生代与老年代的区别？

## 简述 Spring AOP 的原理

## 实现单例设计模式（懒汉，饿汉）

## 简述 Java 的反射机制及其应用场景

## Java 是如何实现线程安全的，哪些数据结构是线程安全的？

## 简述 ArrayList 与 LinkedList 的底层实现以及常见操作的时间复杂度

## Java 类的加载流程是怎样的？什么是双亲委派机制？

## Java 中 sleep() 与 wait() 的区别

## Java 线程池里的 arrayblockingqueue 与 linkedblockingqueue 的使用场景和区别

## JVM 是怎么去调优的？简述过程和调优的结果

## String 类能不能被继承？为什么？

## Java 中接口和抽象类的区别

## 线程池是如何实现的？简述线程池的任务策略
