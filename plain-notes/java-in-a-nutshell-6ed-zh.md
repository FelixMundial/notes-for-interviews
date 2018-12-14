1. [Java基本概念与基本句法](#java基本概念与基本句法)
   1. [1. 基本概念](#1-基本概念)
   2. [2. Java数据类型](#2-java数据类型)
   3. [3. Java表达式、语句与方法](#3-java表达式语句与方法)
   4. [3. Java引用类型](#3-java引用类型)
2. [Java面向对象编程](#java面向对象编程)
   1. [1. 类和对象的概念与构造](#1-类和对象的概念与构造)
   2. [2. 继承、封装与多态](#2-继承封装与多态)
3. [Java类型系统](#java类型系统)
   1. [1. 接口](#1-接口)
   2. [2. 泛型](#2-泛型)
4. [Java的面向对象设计](#java的面向对象设计)
5. [Java实现内存管理和并发编程的方式](#java实现内存管理和并发编程的方式)
   1. [1. Java并发](#1-java并发)
6. [Java平台的工具](#java平台的工具)
   1. [1. Java IO](#1-java-io)
# Java基本概念与基本句法

## 1. 基本概念

在操作系统一个进程中启动```JVM```，为所指定的程序提供Java运行时环境（```java```解释器 类文件加载路径```main()```方法所在类的完全限定名称```main()```函数的可选数组参数）：

 ``` bash
  java <arguments> <program name>
  java -classpath /usr/local/Jude com.example.jude.Jude datafile.txt
 ```

该程序是编译过后的字节码程序，即```.class```类文件。

* ```JVM```设计架构的优势：

- [x] 较```C```/```C++```，执行环境更为安全
- [x] 代开发者管理内存
- [x] 提供一个跨平台的执行环境
- [x] 使用运行时信息进行自我优化与管理（```JVM```监控运行过程并收集运行时信息，从而优化代码执行过程）

* ```HotSpot JVM```引入的```JIT```编译技术

> M将程序中被调用最频繁的“热点方法”（跳过JVM解释器）直接编译成机器码（“热点方法”的判定算法）

* 字节码

每个指令码（操作码）只占一个字节，因此只有不到256个可用指令。

## 2. Java数据类型

* ```Java```采用```Unicode```字符，故字符型变量占据2个字节（16位）。```char```型可与整数类型（16位无符号整型，不是```short```）互相进行强制转换：

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

* ```Java```对布尔类型的要求较为严格，不能将整数类型或对象类型用作```boolean```类型，如：

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

 布尔值是```Java```中唯一一种不能和其它基本类型互相转换的基本类型。

* ```Java```整数类型均为有符号类型，没有对应于C中```unsigned```的类型。```Java```中如果整数运算超过了指定整数类型的范围，不会产生溢出，而是安全“回绕”：

```java
    byte b1 = 127, b2 = 1; // -128 ~ 127
    byte sum = (byte)(b1 + b2); // sum = -128
```

整数类型发生隐式类型转换时能安全执行放大转换，但执行缩小转换时编译器会发出警告，此时只能进行强制转换（```cast```）：

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

浮点数即使执行非法运算也不会抛出异常，只会得到四个特殊值：正无穷（```Double.POSITIVE_INFINITY```）、负无穷、```0```（正零和负零）及```NaN```。

```java
    float res = 0.0f / 0.0f; // NaN
    if (Float.isNaN(res)) {
        // ...
    }
```

## 3. Java表达式、语句与方法

* 使用```instanceof```运算符可以对引用类型安全地进行cast：

```java
    if (object instanceof Point)) {
        Point p = (Point) object;
    }
```

不过，如需使用```instanceof```判断对象的运行时类型，则意味着设计缺陷。

* ```switch```语句关联的表达式不能是浮点类型和布尔类型，也不能是```long```类型；```case```标注不能包含运行时表达式，如变量或方法调用等。（```Java 11```可能采用拓展```switch```语句，能后接表达式，并支持模式匹配）

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

变长参数列表由编译器处理，将数量不定的参数转换为一个数组（和```C++```中可递归展开的参数包不同）。

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

数组的索引表达式必须是```int```类型，因此数组中的元素数量不能超过```Integer.MAX_VALUE```；如果使用```long```类型的表达式索引数组元素，即使在运行时表达式的返回值在```Integer.MAX_VALUE```范围内，也无法通过编译。

> * 数组的```clone()```方法与```System.arrayCopy(des, index1, src, index2, cnt)```方法

* 引用类型的传参与比较

数组只继承了```Object```类默认的```equals()```方法，比较的是引用而非数组的内容；若要比较数组元素是否相等可以使用```java.util.Arrays.equals()```方法。

> * 常量池

# Java面向对象编程

## 1. 类和对象的概念与构造

* 类中没有显式定义构造方法时，```javac```合成的默认构造方法（```<linit>```？）不对实例字段执行任何其它初始化操作（实例字段首先在JVM为实例对象分配堆内存时，即类加载的**准备**阶段被默认初始化）。（```C++```合成的默认构造函数不对内置类型成员变量执行默认初始化）

字段发生类加载（默认或默认+显式）初始化之后，初值被插入类的所有构造方法中（除非目标构造方法中使用```this()```显式调用其它构造方法），因此也可以基于字段（默认或显式）初值构建构造方法。

```javac```在类加载最后的**初始化**阶段运用合成的类初始化方法（```<clinit>```）为类字段进行初始化。也可使用**静态初始化块**和类字段初始化表达式显式地构建类初始化方法：

```java
    public class TestTriangle {
        private static final int NUMPTS = 100;
        private static sines[] = new int[NUMPTS];
        // 静态初始化块
        static {
            double x = 0.0;
            for (int i = 0; i < NUMPTS; i++, x += (Math.PI / 2) / (NUMPTS - 1)) {
                sines[i] = Math.sin(x);
            }
        }
    }
```

## 2. 继承、封装与多态

* 创建类的实例时，```java```保证一定会调用这个类的构造方法及超类的构造方法，即当子类没有在构造方法首行显式调用```super()```或者```this()```时，javac会在构造方法（或合成的构造方法）的首行隐式插入```super()```，构造方法链在一起递归调用，一直上溯到```Object```类的构造方法，之后再向下运行。

**如果超类没有定义无需参数的可见构造方法（但定义了含参构造方法，即```javac```不为其合成无参的默认构造方法），这种隐式调用会导致编译出错（```There's no default constructor available in super class```），因此若一个类没有特别定义无参构造方法，则它的子类必须显式调用该超类的其它含参构造方法。**（！）

**如果类没有显式定义任何构造方法，则```javac```会为其提供一个无参的默认构造方法，可见性修饰符与该类一致，因此若一个类不能公开实例化（如```java.lang.Math```），则必须至少声明一个非```public```的构造方法，以免javac自动合成```public```构造方法。**

* 字段的隐藏/遮盖

引用被隐藏的超类字段的两种方法：
1. 引用直接超类字段时，可使用```super```关键字；
2. 引用间接超类字段时，可使用（向上）转型；

```java
    class A {
        int x;
        // ...
    }

    class B extends A {
        int x;
        // ...
    }

    class C extends B {
        int x;
        // ...
        ((B) this).x; // x in B
        ((A) this).x; // x in A
        // super.super.x非法
    }

    C c = new C();
    ((A)c).x;
```

* 方法的重写/覆盖（```override```）

子类所重写方法的返回值可以是原方法返回值的子类（协变返回，covariant return）。

> 动态绑定（dynamic binding）、虚方法查找（virtual method lookup）及虚分派（virtual dispatch）

* 在不同包中的子类可以继承超类的```protected```字段，但不能直接访问任何超类实例的```protected```字段；而在同一包中的子类可以随意访问任何超类实例的```protected```字段。

```java
    class B extends A { // B is in a different package than A
        public String examineA(A a) {
            return "B sees: " + a.someField; // compilation error!
        }
        public String examineB(B b) {
            return "B sees: " + b.someField; // ok
        }
    }
```

# Java类型系统

## 1. 接口

* 接口的默认（可选）方法

```java
    interface List<E> {
        // ...
        public default void sort(Comparator<? super E> c) {
            Collections.<E>sort(this, c);
        }
    }

    // ...
    customList.sort(new Comparator<E>() {
        @override
        public int compare(Object o1, Object o2) {
            return o2.compareTo(o1); // ascending order(returning -1)
        }
    })
    // 或使用lambda：
    customList.sort((o1, o2) -> o2.compareTo(o1); // ascending order(returning -1))
```

## 2. 泛型

* 泛型擦除对方法重载的影响

* 数组允许协变，但泛型不允许，即```List<T>```不是```List<Object>```的子类型；但```List<T>```却是```List<?>```的子类型，因此：

```java
    List<?> objects = new ArrayList<String>();
```

完全合法，但```objects```的```get()```方法返回的是```Object```类型。

* 受限通配符

> **PECS（"Producer extends, consumer super"）原则：如果需要一个集合提供类型T的元素，使用```<? extends T>```，因此可以通过向上转型读取集合中的```T```类型对象，但不能向其中添加任何元素（setter不合法）；如果需要一个集合使用类型T的元素，则用```<? super T>```，因此可以向集合中添加```T```类型或其任何子类的对象，但不能读取任何元素（可以返回```Object```类对象，但基本等同于getter不合法）。**

* 枚举类型

枚举类型由```java```运行时创建，可以拥有字段和方法，可以实现接口，但不能被继承，必能被泛型化，不能在类型外部实例化，且只能有一个私有的构造方法；枚举常量可以带参数，称为“带参数的枚举”。

```java
    public enum RegularPolygon {
        TRIANGLE(3), SQUARE(4), PENTAGON(5), HEXAGON(6);

        private Shape shape;
        private RegularPolygon(int sides) {
            // constructor
            switch (sides) {
                // ...
            }
        }
    }
```

* 嵌套类型

1. 静态成员类

嵌套的接口、枚举和注解类型都隐式声明为静态成员类。

2. 非静态成员类

非静态成员类可使用外层类、超类、外层类的超类的（公有或私有）字段。

3. 局部类

局部类可使用外层类、超类、外层类的超类的（公有或私有）字段，及```final```局部字段；所用到的外界局部变量在局部类实例中都有一个私有副本（javac生成），因此这些局部变量必须被预先声明为```final```。

> * 由于局部类实例有一份```final```字段的私有副本，局部类实例的词法作用域与解释器进出定义局部类的代码块可能没有任何关系，在局部类作用域以外，残留的局部类实例引用可能还能发挥作用，这是Java的闭包实现策略，保存作用域的状态。

```java
    public class Closure {
        public static interface IntHolder {
            public int getInt();
        }

        public static void main(String[] args) {
            IntHolder[] holders = new IntHolder[10];

            for (int i = 0; i < 10; i++) {
                final int fi = i;

                class MyIntHolder implements IntHolder {
                    @override
                    public int getInt() {
                        return fi;
                    }
                }

                holders[i] = new MyIntHolder();
            }

            for (var myIntHolder : holders) {
                System.out.println(myIntHolder.getInt());
            }
        }
    }
```

4. 匿名类

# Java的面向对象设计

# Java实现内存管理和并发编程的方式

## 1. Java并发

* ```synchronized```关键字

> * ```synchronized```关键字与```wait()```及```notifyAll()```方法一同使用，以维护一个等待线程队列（如实现一个线程安全的泛型容器）：
```java
    public class WaitingQueue<E> {
        LinkedList<E> queue = new LinkedList<>();
        public synchronized void push(E o) {
            queue.add(o);
            this.notifyAll();
        }
        public synchronized E pop() {
            while (queue.size() == 0) {
                try {
                    this.wait();
                } catch (InterruptedException e) { }
                return queue.remove();
            }
        }
    }
```

* ```volatile```关键字

# Java平台的工具

## 1. Java IO


