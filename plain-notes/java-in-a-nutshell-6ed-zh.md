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

* JVM设计架构的优势：

- [x] 较C/C++，执行环境更为安全
- [x] 代开发者管理内存
- [x] 提供一个跨平台的执行环境
- [x] 使用运行时信息进行自我优化与管理（JVM监控运行过程并收集运行时信息，从而优化代码执行过程）

* HotSpot JVM引入的JIT编译技术

JVM将程序中被调用最频繁的“热点方法”（跳过JVM解释器）直接编译成机器码（“热点方法”的判定算法）

* 字节码

每个指令码（操作码）只占一个字节，因此只有不到256个可用指令。

## 2. Java数据类型

* Java采用Unicode字符，故字符型变量占据2个字节（16位）。```char```型可与整数类型（16位无符号整型，不是```short```）互相进行强制转换：

 ```java
    char ch = '9';
    System.out.println((int)ch - (int)('0'));
 ```

```Character```类的其它字符处理静态方法：

 ```java
    Character.isDigit();
    Character.isJavaLetter();
    Character.isLowerCase();
    Character.toUpperCase();
 ```

* Java对布尔类型的要求较为严格，不能将整数类型或对象类型用作```boolean```类型，如：

 ```java
    Object o = new Object();
    int i;
    if (o) { // error
        while (i) { // error
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

* Java整数类型均为有符号类型，没有对应于C中```unsigned```的类型。Java中如果整数运算超过了指定整数类型的范围，不会产生溢出，而是安全“回绕”：

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
    BigInteger bi = new BigInteger(String.valueOf(srcInt), radix); // radix默认为10，若逆过程读入二进制字符串则radix为2
    String res = bi.toString(2);
```

整数类型的包装类都有解析字符串的静态方法：

```java
    Integer.parseInt();
    Byte.parseByte();
```

* 浮点数类型默认为64位```double```：

```java
    double d = 6.02e23;
    float f = 6.02E23f;
```

浮点数即使执行非法运算也不会抛出异常，只会得到四个特殊值：正无穷（```Double.POSITIVE_INFINITY```）、负无穷、0（正零和负零）及```NaN```。

```java
    float res = 0.0f / 0.0f; // NaN
    if (Float.isNaN(res)) {
        // ...
    }
```

## 3. Java表达式、语句与方法

* 使用```instanceof```运算符可以对引用类型安全地进行cast

```java
    if (object instanceof Point)) {
        Point p = (Point) object;
    }
```

* ```switch```语句关联的表达式不能是浮点类型和布尔类型，也不能是```long```类型；```case```标注不能包含运行时表达式，如变量或方法调用等。（Java 11可能采用拓展```switch```语句，能后接表达式，并支持模式匹配）

* ```synchronized```语句

* Java异常处理

> * 如果```finally```块使用```return```、```continue```、```break```或```throw```语句，抑或内部再次抛出异常，则之前带转移的异常中止处理（语句块正常返回，或```finally```中的异常取代先前的异常）。

> * 采用带资源的```try```语句时，```try```语句参数内的资源必须实现```AutoCloseable```接口

```java
    try (InputStream is = new FileInputStream("/users/input.txt)) {
        // ...
    }
```

> * 子类方法中所声明抛出的异常类型必须是父类所声明抛出异常类型的子类或相同类型，且不允许比父类抛出更多的异常。

> * 异常链

* ```final```方法修饰符

使用final修饰的方法不能被子类覆盖/重写（```@override```）或隐藏，能获得普通方法无法得到的编译器优化；所有```private```方法都隐式添加了```final```修饰符，使用```final```声明的任何类内部的所有方法也都隐式地声明为```final```方法。

* 变长参数列表

变长参数列表由编译器处理，将数量不定的参数转换为一个数组（和C++中可递归展开的参数包不同）。

```java
    public static int max(int first, int... rest) { // 运行时等同于int[] rest
        int max = first;
        for (int i : rest) {
            if (i > max) {
                max = i;
            }
        }
        return max;
    }
```

C++可变参数模板：

```cpp
    template <typename T>
    std::ostream &print(std::ostream &os, const T &t) {
        // last element
        return os << t;
    }
    template <typename T, typename... Args>
    std::ostream &print(std::ostream &os, const T &t, const Args &... rest) {
        os << t << ", ";
        return print(os, rest...);
    }
```

## 3. Java引用类型

* 将引用类型的值插入数组中时，编译器插入运行时检查，确保运行时数组的编译时类型与运行时类型一致，否则抛出```ArrayStoreException```异常。

* 数组的创建与初始化

```java
    int[] smallPrimes = new int[] {2, 3, 5, 7, 11}; // 赋值运算符右侧表达式又称为匿名数组字面量
    int[] morePrimes = {13, 17, 19, 23, 29};
```

数组的初始化发生在**运行时**，因此数组元素可用编译时常量或运行时常量：

```java
    Point[] point = { circle1.getCenterPoint(), circle2.getCenterPoint() };
```

* 数组的使用

数组的索引表达式必须是```int```类型，因此数组中的元素数量不能超过```Integer.MAX_VALUE```；如果使用long类型的表达式索引数组元素，即使在运行时表达式的返回值在```Integer.MAX_VALUE```范围内，也无法通过编译。

> * 数组的```clone()```方法与```System.arrayCopy(des, index1, src, index2, cnt)```方法

# Java面向对象编程

# Java类型系统

# Java的面向对象设计

# Java实现内存管理和并发编程的方式
