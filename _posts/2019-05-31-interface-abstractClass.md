---
layout:     post
title:      接口和抽象类的区别 | Java 学习笔记#3
subtitle:   接口和抽象类的区别 | Java 学习笔记#3
date:       2019-05-31
author:     Shaw
header-img: img/post-bg-altitude.jpg
catalog: true
tags:
---

# 谈谈接口和抽象类有什么区别？   
---
## 接口    

- **`API`的定义和实现分离**：接口是抽象方法的集合，方法在接口中统一声明，不同的类中有不同的具体实现。       
- **定义行为规范**：接口定义了行为规范，将相同行为统一为接口，例如看到一个类实现了`Comparable`接口，就知道可以通过`compareTo`方法去比较对象。     
- **抽象**：不能实例化；成员只能是`public static final`；方法只能是抽象方法和静态方法。     
- 一个Java类可以实现多个接口     
- JDK中的接口：`java.util.List`      

## 抽象类    

- **抽象**：不能实例化；可以有0到n个抽象方法和具体方法；可以包含具体数据；        
- **代码重用**：抽象类抽取相关类的共用方法实现或者是共同成员变量，通过继承的方式达到代码复用的目的      
- 一个Java类只能继承一个父类       
- JDK中的`Collection`框架，很多通用部分就被抽取成为抽象类，例如：`java.util.AbstractList`    


## 总结      

- `extends` 是 `is-a` 关系，表明类的本质或者说骨架。       
- `implements` 是 `has-a` 关系，表明类包含的行为。           
- 本质只能有一个，多继承会导致看不清类的本质。       
- 行为可以有很多种，与本质无关的共有的行为就通过`implements`接口来实现。       

