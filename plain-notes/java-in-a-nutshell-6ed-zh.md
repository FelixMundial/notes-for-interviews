<!-- TOC -->
* [Java基本概念与基本句法](#Java基本概念与基本句法)
    * [1.基本概念](#1-基本概念)
    * [2.词法结构与数据类型](#2-词法结构与数据类型)
<!-- TOC -->

<br>

# Java基本概念与基本句法

## 1. 基本概念

在操作系统一个进程中启动JVM，为所指定的程序提供Java运行时环境：
 ``` bash
  java <arguments> <program name> 
 ```
该程序是编译过后的字节码程序，即.class类文件。

[x] JVM设计架构的优势：
* 较C/C++，执行环境更为安全
* 代开发者管理内存
* 提供一个跨平台的执行环境
* 使用运行时信息进行自我优化与管理（JVM监控运行过程并收集运行时信息，从而优化代码执行过程）
[x] HotSpot JVM引入的JIT编译技术
JVM将程序中被调用最频繁的“热点方法”（跳过JVM解释器）直接编译成机器码（“热点方法”的判定算法）

[x] 字节码
每个指令码（操作码）只占一个字节，因此只有不到256个可用指令。

## 2. 词法结构与数据类型

* Java采用Unicode字符，故字符型变量占据2个字节（16位）。char型可与整数类型（16位无符号整型，不是short）互相进行强制转换：

 ```java
    char ch = '9';
    System.out.println((int)ch - (int)('0'));
 ```

Character类的其它字符处理静态方法：

 ```java
    
    Character.isDigit();
    Character.isJavaLetter();
    Character.isLowerCase();
    Character.toUpperCase();
 ```

* Java对布尔类型的要求较为严格，不能将整数类型或对象类型用作boolean类型，如：

 ```java
    Object o = new Object();
    int i;
    if (o) {
        while (i) {
            // ...
        }
    }
 ```

应写为：

 ```java
    Object o = new Object();
    int i;
    if (o != null) {
        while (i != 0) {
            // ...
        }
    }
 ```

 布尔值是Java中唯一一种不能和其它基本类型互相转换的基本类型。

* Java整数类型均为有符号类型，没有对应于C中unsigned的类型。Java中如果整数运算超过了指定整数类型的范围，不会产生溢出，而是安全“回绕”：

```java
    byte b1 = 127, b2 = 1; // -128 ~ 127
    byte sum = (byte)(b1 + b2); // sum = -128
```

整数类型发生隐式类型转换时能安全执行放大转换，但执行缩小转换时编译器会发出警告，此时只能进行强制转换（cast）：

```java
    int i = 13;
    byte b = i;
    byte b1 = (byte) i;
```

二进制与十进制整数的互相转换：

```java
    int srcInt;
    BigInteger bi = new BigInteger(String.valueOf(srcInt), radix); // radix默认为10，// 若逆过程读入二进制字符串则radix为2
    String res = bi.toString(2);
```

整数类型的包装类都有解析字符串的静态方法：

```java
    Integer.parseInt();
    Byte.parseByte();
```

* 浮点数类型默认为64位double：

```java
    double d = 6.02e23;
    float f = 6.02E23f;
```

浮点数即使执行非法运算也不会抛出异常，只会得到四个特殊值：正无穷（Double.POSITUVE_INFINITY）、负无穷、0（正零和负零）及NaN。

```java
    float res = 0.0f / 0.0f;
    if (Float.isNaN(res)) {
        // ...
    }
```

* 使用instanceof操作符可以对引用类型安全地进行cast

```java
    if (object instanceof Point)) {
        Point p = (Point) object;
    }
```

## 3. 语句

* switch语句关联的表达式不能是浮点类型和布尔类型，也不能是long类型；case标注不能包含运行时表达式，如变量或方法调用等。（Java 11可能采用拓展switch语句，能后接表达式，并支持模式匹配）

* 

# Java面向对象编程

# Java类型系统

# Java的面向对象设计

# Java实现内存管理和并发编程的方式
