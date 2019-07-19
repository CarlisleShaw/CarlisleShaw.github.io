---
layout:     post
title:      String、StringBuffer、StringBuilder 的区别 | Java 学习笔记#2
subtitle:   String、StringBuffer、StringBuilder 的区别 | Java 学习笔记#2
date:       2019-05-29
author:     Shaw
header-img: img/post-bg-altitude.jpg
catalog: true
tags:
---

# String、StringBuffer、StringBuilder有什么区别？   
---
## String
---
`String`存储字符串的底层实现是:   
```java
private final char value[]; // JDK 8
private final byte[] value; // JDK 9 and later
```
（关于JDK9中将`char`改为`byte`，详见：[Java9后String的空间优化](https://juejin.im/post/5aff7f10518825426e0233ea)）    

故`String`一经初始化后就无法被修改，对字符串的拼接或修改都是通过构造一个新的`String`对象来实现的。而频繁地创建新对象会影响性能，降低效率。  

## StringBuffer & StringBuilder   
---
### AbstractStringBuilder   

`StringBuffer`和`StringBuilder`都继承了抽象类`AbstractStringBuilder`。   

`AbstractStringBuilder`存储字符串的底层实现是:   
```java
char[] value; // JDK 8
byte[] value; // JDK 9 and later
```
故`StringBuffer`和`StringBuilder`可以通过`append`、`insert`、`replace`、`delete`、`deleteCharAt`等方法修改这个对象中的字符串数组，而不需要创建新对象。   

### 区别   

`StringBuffer`除构造方法外的所有方法都被声明为`synchronized`，所以`StringBuffer`是线程安全的，随之而来的是加锁和释放锁带来的额外的性能开销。    

`StringBuilder`和`StringBuffer`在能力上几乎没有区别，唯一的区别是去掉了`synchronized`关键字，所以在没有线程安全的需求下，为了减少性能开销，`StringBuilder`是进行字符串操作的首选。        




