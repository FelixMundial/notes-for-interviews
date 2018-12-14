1. [保证稳定性与兼容性](#保证稳定性与兼容性)
   1. [非静态成员的```sizeof````](#非静态成员的sizeof)
   2. [扩展的```friend```语法](#扩展的friend语法)
   3. [```final```/```override```控制](#finaloverride控制)
   4. [模板函数的默认模板参数](#模板函数的默认模板参数)

# 保证稳定性与兼容性
## 非静态成员的```sizeof````
相比于C++ 98只支持类静态成员及对象实例的成员的```sizeof```操作，C++ 11支持对类的非静态成员直接使用```sizeof```。
```cpp
    // C++ 98中获得类成员大小的方法
    sizeof(((SomeClass *)0) -> someMember);
    // ...
```
```cpp
    // C++ 11
    sizeof(SomeClass::someMember);
```
## 扩展的```friend```语法
C++ 11声明一个类为另一个类的友元时，无需使用```class```关键字，在友元声明中也可以使用类型别名。
```cpp
class Poly;
typedef Poly P;
class A {
    friend P; // ok
    // ...
}
```
C++ 11还支持在类模板中直接声明友元，当模板参数为内置类型时，友元声明会被忽略：
```cpp
class P;
template <typename T> class A {
    friend T;
}
// ...
A<P> aP;
a<int> aInt; // aInt被实例化为不含有友元定义的类型
```
此特性可用于某些测试用例：
```cpp
template <typename T> class DefenderT {
public:
    friend T;
    void Defend() {}
    // ...
private:
    int pos_x;
    int pos_y;
    // ...
};
template <typename T> class AttackerT {
public:
    friend T;
    void Attack() {}
    // ...
private:
    int pos_x;
    int pos_y;
    // ...
};

using Defender = DefenderT<int>
using Attacker = AttackerT<int> // 普通的类，没有友元，保证了封装性。

#ifdef UNIT_TEST
class Validator {
public:
    void Validate() {}
};
using DefenderTest = DefenderT<Validator>;
using AttackingTest = AttackerT<Validator>; // 测试专用类，Validator成为友元，可以访问任何成员，达到测试目的。

int main() {
    // 实例操作与测试
}
#endif
```
## ```final```/```override```控制
类似Java，C++ 11中可使用```final```关键字修饰类中的虚函数，以阻止派生类重写该虚函数：
```cpp
class Object {
    virtual void foo() = 0;
};
class Base : public Object {
    void foo() final {
        // ...
    }
};
class Derived : public Base {
    void foo() { // 编译出错
        // ...
    }
}
```
如果派生类函数在重写时使用了```override```描述符，该函数必须重写基类中的同签名函数，否则无法通过编译：
```cpp
class Base {
    virtual void Turing() const;
};
class Derived : protected Base {
    void Turing() override { // 常量性不一致，编译出错
        // ...
    }
}
```
## 模板函数的默认模板参数