---
layout: post
title: "C++ 面试题整理 (整理中)"
---

## 简述 vector实现原理

## C++ 中智能指针和指针的区别是什么？

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

## 什么是RAII

*Resource Acquisition Is Initialization*：将资源的生命周期与（获取该资源的）对象的生命周期绑定

虽然RAII和finally都能保证资源管理时的异常安全，但相对来说，使用RAII的代码相对更加简洁。在真实环境中，调用资源释放代码的次数远多于资源类型的个数，所以相对于使用finally来说，使用RAII能减少代码量。