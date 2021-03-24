---
layout: post
title: "C++ 面试题整理 (整理中)"
---

## C++中 struct 与 class 的区别

Quoting [The C++ FAQ](http://www.faqs.org/faqs/C++-faq/part3/),

> [7.8] What's the difference between the keywords struct and class?
>
> The members and base classes of a struct are public by default, while in class, they default to private. Note: you should make your base classes explicitly public, private, or protected, rather than relying on the defaults.
>
> Struct and class are otherwise functionally equivalent.
>
> OK, enough of that squeaky clean techno talk. Emotionally, most developers make a strong distinction between a class and a struct. A struct simply feels like an open pile of bits with very little in the way of encapsulation or functionality. A class feels like a living and responsible member of society with intelligent services, a strong encapsulation barrier, and a well defined interface. Since that's the connotation most people already have, you should probably use the struct keyword if you have a class that has very few methods and has public data (such things do exist in well designed systems!), but otherwise you should probably use the class keyword.

## C++ 中智能指针的介绍

智能指针：

- auto_ptr：弃用，被 unique_ptr 替换
- unique_ptr：只允许基础指针的一个所有者。大小为一个指针
- shared_ptr：采用引用计数的智能指针。大小为两个指针：一个用于对象，另一个用于包含引用计数的共享控制块。
- weak_ptr：与 shared_ptr 一同使用。提供对一个或多个 shared_ptr 实例拥有的对象的访问，但不参与计数。可用于断开 shared_ptr 实例之间的循环引用。

需要引入头文件<memory>

后三个在 c++11 中支持。

智能指针在脱离类的作用域时就会自动调用析构函数

疑问：智能指针与在栈上分配内存有什么区别呢？

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

## 简述 STL 中的 map 的实现原理

红黑树，ordered，O(log(n))的插入与查询时间

## 什么是字节对齐，为什么要采用这种机制？

- Such alignment restrictions simplify the design of the hardware forming the
  interface between the processor and the memory system.
- Intel recommends that data be aligned to improve memory system performance.

## 什么是 RAII

_Resource Acquisition Is Initialization_：将资源的生命周期与（获取该资源的）对象的生命周期绑定

虽然 RAII 和 finally 都能保证资源管理时的异常安全，但相对来说，使用 RAII 的代码相对更加简洁。在真实环境中，调用资源释放代码的次数远多于资源类型的个数，所以相对于使用 finally 来说，使用 RAII 能减少代码量。

## C 语言实现多态

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
