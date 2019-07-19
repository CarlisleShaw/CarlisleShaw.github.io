---
layout:     post
title:      int 和 Integer 的区别 | Java 学习笔记#1
subtitle:   int 和 Integer 的区别 | Java 学习笔记#1
date:       2019-03-26
author:     Shaw
header-img: img/post-bg-altitude.jpg
catalog: true
tags:
---

# int 和 Integer 有什么区别？谈谈 Integer 的值缓存范围。   

## int   
---
`int` 是 Java 的 `8` 个原始数据类型（`Primitive Types`）之一。   
Primitive Types: `boolean`, `byte`, `short`, `char`, `int`, `float`, `double`, `long`.    

## Integer   
---
`Integer` 是 int 对应的包装类，它有一个 int 类型的字段存储数据。  

```java
private final int value; // 可见，你无法修改一个Integer对象的值，你只能指向一个新的Integer对象并销毁原来的对象
```
 
`Integer` 提供了一系列方法：int 转 String，String 转 int，max()，min()等。    

在 Java 5 中，引入了`自动装箱`和`自动拆箱`功能（`boxing`/`unboxing`），Java 可以根据上下文，自动进行 int/Integer 的转换。

**Integer 的`值缓存`：**   
构建 Integer 对象的传统方式是直接调用构造器，直接 new 一个对象。   
但是实际上，大部分数据操作都集中在有限的、较小的数值范围内。   
因此，Java 5 中新增了方法 `valueOf()`，在调用它的时候会利用一个缓存机制，带来了明显的性能改进。   
值缓存默认是 -128 到 127 之间。    

# 详解    

## 理解自动装箱、拆箱   
---
得益于自动拆装箱功能，我们的代码可以写成这样：   

```java
Integer i = 1;   

int unboxing = i++;
```   

问题来了！

- 怎么能把一个int型的数字赋值给一个对象？！
- 自增运算符`++`一般只能用在原始数据类型（int, double, char等）上，而`i`是对象，理论上不可以使用`++`。   

那么这其中发生了什么，让 `++` 可以在 `Integer` 对象上使用呢？   

用 `javap -v -p` 反编译看看，发生了哪些函数调用。这里省略了各种操作码，只保留了方法调用。   

反编译输出：   
```
 1: invokestatic  #2 // Method java/lang/Integer.valueOf:(I)Ljava/lang/Integer;
    ......
 8: invokevirtual #3 // Method java/lang/Integer.intValue:()I
    ......
13: invokestatic  #2 // Method java/lang/Integer.valueOf:(I)Ljava/lang/Integer;
    ......
21: invokevirtual #3 // Method java/lang/Integer.intValue:()I
    ......
25: return
```   

可以看到，那段代码多次调用了`valueOf()`和`intValue()`两个方法。   

接下来详细说明一下。

注释版：   
```
// Integer i = 1;
 1: invokestatic  #2 // 调用 Integer.valueOf(1)，将 1 转为 Integer 对象（自动装箱）
    ......           // 让 i 指向上面那个值为 1 的 Integer 对象
    
// int unboxing = i++;
 8: invokevirtual #3 // 调用 i.intValue() 得到 i 的 int 值为 1（自动拆箱）
    ......           // 上面得到的 int 值 + 1  得到 2   
    
13: invokestatic  #2 // 调用 Integer.valueOf(2)，将 2 转为 Integer 对象（自动装箱）
    ......           // 让 i 指向值为 2 的新 Integer 对象   
    
21: invokevirtual #3 // 调用 i.intValue() 得到 i 的 int 值为 2（自动拆箱）
    ......           // 把 unboxing 变量 的值改为上面得到的 int 值 2   
    
25: return
```   

可见，上面那两行代码实际上等价于：   

```java
Integer i = Integer.valueOf(1);   

i = Integer.valueOf(i.intValue() + 1);   

int unboxing = i.intValue();
```   

有了自动拆装箱，代码真是优雅了很多。   

自动拆装箱发生在**`编译阶段`**，javac自动做了一些转换，使得不同的写法生成的字节码相同，在运行时等价。这种功能就是所谓的**语法糖**。   

## 滥用自动拆装箱的问题   
---
在 Java 虚拟机中，每个 Java 对象都有一个**对象头**（object header），这个由标记字段和类型指针所构成。其中，标记字段用以存储 Java 虚拟机有关该对象的运行数据，如哈希码、GC 信息以及锁信息，而类型指针则指向该对象的类。    

在 64 位的 Java 虚拟机中，对象头的标记字段占 64 位，而类型指针又占了 64 位。也就是说，每一个 Java 对象在内存中的额外开销就是 16 个字节。以 Integer 类为例，它仅有一个占 4 个字节的 int 类型的私有字段，但由于对象头的存在，每一个 Integer 对象的额外内存开销至少是`400%`。   

尽管自动拆装箱方便到让你感觉不到它的存在，但是使用对象的巨大开销却是实实在在的，所以在实践中，尤其是在性能敏感的场合，要避免无意中的自动拆装箱，能用基本数据类型的就不要使用对象。     

## Integer 缓存      
---
来看一段代码：   

```java
public class JavaTest {
    public static void main(String[] args) {
        Integer a = 100, b = 100;
        Integer c = 200, d = 200;
        Integer e = 300, f = 300;
        System.out.println(a == b);
        System.out.println(c == d);
        System.out.println(e == f);
    }
}
```

猜一下输出结果。   

看到这个代码应该要反应出最基本的两件事情：

1. 前三行用了自动装箱功能

2. 这 6 个变量都是对象，`==`比较的是指针，`equals()`方法比较的才是真实的值。    

所以，看似得到的结果应当是三个false。

实际运行结果：
`java JavaTest`
```java
true
false
false
```

不科学！   

而且神奇的是，用不同的命令运行输出结果竟然也不同！   

`java -XX:AutoBoxCacheMax=222 JavaTest`
```java
true
true
false
```

`java -XX:AutoBoxCacheMax=333 JavaTest`
```java
true
true
true
```   

为什么？    

**先上结论：`缓存`搞的鬼！**      

看代码，前三行声明变量并初始化，使用了自动装箱功能，也就是说，隐式调用了`Integer.valueOf()`方法。   

这段代码里，除了声明变量就是比较和输出，比较和输出都没啥花头，问题大概率出在自动装箱之中。   

那么来看看`Integer.valueOf()`的定义：   

```java
public final class Integer extends Number implements Comparable<Integer> {      
    ......
    
    public static Integer valueOf(int i) {
        if (i >= IntegerCache.low && i <= IntegerCache.high)
            return IntegerCache.cache[i + (-IntegerCache.low)];
        return new Integer(i);
    }   
    
    ......
}
```   

这里面果然有蹊跷！    

这个方法并没有直接返回`new Integer(i)`，而是用到了`IntegerCache`这个类，那再去看看这个类。   

```java
public final class Integer extends Number implements Comparable<Integer> {      
    ......
    
    private static class IntegerCache {
        static final int low = -128;
        static final int high;
        static final Integer cache[];

        static {
            // high value may be configured by property
            int h = 127;
            String integerCacheHighPropValue =
                sun.misc.VM.getSavedProperty("java.lang.Integer.IntegerCache.high");
            if (integerCacheHighPropValue != null) {
                try {
                    int i = parseInt(integerCacheHighPropValue);
                    i = Math.max(i, 127);
                    // Maximum array size is Integer.MAX_VALUE
                    h = Math.min(i, Integer.MAX_VALUE - (-low) -1);
                } catch ......
            }
            high = h;    

            cache = new Integer[(high - low) + 1];
            int j = low;
            for(int k = 0; k < cache.length; k++)
                cache[k] = new Integer(j++);    

            // range [-128, 127] must be interned (JLS7 5.1.7)
            assert IntegerCache.high >= 127;
        }     
        ......
    }   
    
    ......
}
```  

简单的来说，`IntegerCache`做了这几件事：   

1. 声明了一个`Integer`类型的数组`cache[]`   
2. 设定一个下限`low = -128`，设定一个上限`high = 127`   
3. 上限可以通过`JVM`参数指定一个比127更大的数（就是上文中的`-XX:AutoBoxCacheMax=`）   
4. 初始化`cache[]`数组，值为[-128, -127, ..., 0, 1, ..., high-1, high]（数组里的这些值不是`int`！是`Integer`对象！）   

这个`cache[]`数组就是传说中的**`缓存`**了，它会在`IntegerCache`第一次被用到时初始化放在内存中，里面存放着值为从`low`到`high`的`Integer`对象。

ok，`IntegerCache`这个类整明白了，继续回去看`Integer.valueOf()`。   

```java
public final class Integer extends Number implements Comparable<Integer> {      
    ......
    
    public static Integer valueOf(int i) {
        if (i >= IntegerCache.low && i <= IntegerCache.high)
            return IntegerCache.cache[i + (-IntegerCache.low)];
        return new Integer(i);
    }   
    
    ......
}
```   

这时候就能看明白它在干嘛了。     

先判断`i`的大小是否在缓存范围内，在的话直接返回缓存（`cache[]`）中对应的的`Integer`对象。超出缓存大小，就`new`一个 `Integer`对象返回。    

**破案了！**    

使用`java JavaTest`运行时，缓存大小默认为[-128, 127]。   

`100`在缓存区内，所以`a`和`b`实际指向的都是缓存中的`Integer`对象，是同一个东西，所以`a == b`得到的结果是`true`。   

而`200`和`300`都不在缓存区内，故都是被`new`出来的，所以指向的必不是同一个对象，`c == d`、`e == f`得到的结果都是`false`。

使用`java -XX:AutoBoxCacheMax=222 JavaTest`运行，缓存大小被设置为[-128,222]，故`100`和`200`都是分别从缓存区里拿的同一个对象，前两对得到的结果都是`true`，`300`不在缓存区内，是被分别`new`出来的2个不同的对象，所以得到的结果是`false`。

**`Integer缓存`证明了自己的存在！**

在第一次使用时一次性缓存常用的Integer对象，后续使用到大小在缓存区内的Integer对象就无需新建对象，直接取缓存中的Integer对象即可，有助于节省内存、提高性能。    

这种缓存机制并不是只有 Integer 才有，同样存在于其他的一些包装类，比如：    

- Boolean，缓存了 true/false 对应实例，确切说，只会返回两个常量实例 Boolean.TRUE/FALSE。    

- Short，同样是缓存了 -128 到 127 之间的数值。    

- Byte，数值有限，所以全部都被缓存。    

- Character，缓存范围 '\u0000' 到 '\u007F'。     


## Java 原始数据类型和引用类型局限性   
---   

- Java 泛型不能使用原始数据类型   
因为 Java 的泛型某种程度上可以算作伪泛型，它完全是一种编译期的技巧，Java 编译期会自动将类型转换为对应的特定类型，这就决定了使用泛型，必须保证相应类型可以转换为 Object。   
从这个角度看，使用泛型就只能使用Integer对象，和int相比占用了大量额外内存空间，但Integer的缓存机制巧妙的解决了这个内存大量占用的问题。

- 无法高效地表达数据，也不便于表达复杂的数据结构，比如 vector 和 tuple   
Java 的对象都是引用类型，如果是一个原始数据类型数组，它在内存里是一段连续的内存，而对象数组则不然，数据存储的是引用，对象往往是分散地存储在堆的不同位置。这种设计虽然带来了极大灵活性，但是也导致了数据操作的低效，尤其是无法充分利用 CPU 的缓存机制。   


