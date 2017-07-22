---
layout: post
title: "Effective C++ [5-6] 章总结"
description: "description"
category: "阅读"
tags: "EffectiveCPP"
---
# Effective C++ [5-6] 章总结
## 条款 26：尽可能延后变量定义式的出现时间

```C
std::string encryptPassword(const std::string & password)
{
    ...
    std::string encrypted ;
    encrypted = password;
    // 应该改为
    std::string encypted(password);
}
```
直到非得使用该变量的前一个定义，或者说延后到能直接给它初值实参为止

关于定义变量在循环内还是循环外

```C
// 方法A ： 1个构造函数 + 1个析构函数 + n个赋值操作
Widget w;
for (int i = 0;i < n;i++)
{
    w = 取决于i的某个值;
}

// 方法B ： n个构造函数 + n个析构函数
for(int i = 0;i < n;i++)
{
    Widget w(取决于i的某个值);
}

```
赋值成本比构造+析构低 或者 对效率高度敏感 选A，否则选B

## 条款 27：尽量少做转型动作

* const_cast<T>(expression)
    被用来将对象常量性转除
* dynamic_cast<T>(expression)
    多态性的基类指针或者引用转为为派生类指针或者引用
* reinterpret_cast<T>(expression)
* static_cast<T>(expression)
    强迫隐式转换，non-const转换为const或int转换为double
    
旧式转换： 调用explicit构造函数将一个对象传递给一个函数

## 提款 28:避免返回handles指向对象内部成分

```C
class Rectangle
{
public:
    ...
    Point & upperLeft() const {return pData->ulhc;}
    ... 
}
    
Point coord1(0,0);
Point coord2(100,100);
const Rectangle rec(coord1.coord2);
rec.upperLeft().serX(50); // 导致rec值改变
```
* 避免返回handles(包括 reference、指针、迭代器) 指向对象内部。这样可以增加封装性，帮助const成员函数行为像const。

## 条款 29:为“异常安全”而努力是值得的

```C
void PressyMenu::changeBackground(std::istream & imgSrc)
{
    lock(&mutex);
    delete bgImage;
    ++imageChanges;
    // 若new失败，而imageChanges已加，则违反2
    bgImage = new Image(imgSrc);
    // 若此句new失败导致异常，则互斥器就永远锁住了，违反1。
    unlock(&mutex);
}
    
```
异常安全性函数必须做到

1. 不泄漏任何资源
2. 不允许数据破坏

* 基本承诺：异常抛出，程序内的任何事物仍然保持在有效状态下。没有任何对象或数据结构会因此破坏，所有对象都处于一种内部前后一致的状态。
* 强烈保证：函数成功，则完全成功。函数失败，程序回复到调用之前。通过copy-and-swap实现
* 不抛掷保证：承诺绝不抛出异常

## 条款 30:透彻了解inlining的里里外外
inline：对此函数的每一个调用都以函数本体替换之。
将inline限制在小型，被频繁调用的函数上。
inline增加了调适的难度，无法随着程序库的升级而升级

## 条款 31:将文件间的编译依存关系降至最低
C++中，一旦对某个实现做了部分修改，需要多有关依赖等文件重新编译，比较浪费。
解决方法：
*1. 将对象分为两个类：一个只负责提供接口，另一个负责实现该接口。并且在负责提供接口的类中定义一个指向负责实现的接口类的指针，从而能够调用实现的方法。
    使用object references或object pointer可以完成任务，就不要使用objects。尽量使用class声明而不是class定义。为声明式和定义式提供不同的头文件。

*2.定义抽象基类，派生类来实现继承而来的virtual函数

## 条款 32:确定你的public继承塑模出is-a关系

* public继承意味着is-a。适用于base class身上的每一件事情一定适用于derived class身上，因为每一个derived class对象也都是一个base class对象

## 条款 33:避免遮掩继承而来的名称

```C
class Base{
public:
    virtual void mf1();
    virtual void mf1(int);
    virtual void mf2();
    void mf3();
    void mf3(double);
}

class Derived:public Base{
public:
    virtual void mf1();
    
    // using Base::mf1;
    // using Base::mf3; 解决方法一
    
    // virtual void mf1()
    //{ Base::mf1();}   解决方法二 转交函数
    void mf3();
    void mf4();
    ...
};

// 基类中的mf1与mf3都被派生类中的mf1和mf3所遮掩，导致Base::mf1和Base::mf3不再被derived所继承

```    

## 条款 34:区分接口继承和实现继承
* public继承下，派生类总是继承基类接口
* 纯虚函数只具体指定接口继承
* 非纯虚函数具体指定接口继承及缺省实现继承
* 非虚函数具体指定接口继承以及强制性实现继承

## 条款 35:考虑virtual函数以外的其他选择
1. 非虚函数接口手法实现**模版方法模式**
定义一个非虚函数包含一个私有的虚函数 -- NVI（none-virtual interface）
非虚函数称为外覆器(wrapper) 确保得意在virtual函数调用前后布置场景

2. 有Function Pointer实现Strategy模式
定义函数指针，通过函数指针传入方法，更具有弹性，同一类型的不同实体可以有不同函数，某已知对象的函数可以在运行期变更

3. 由tr1::function完成Strategy模式
通过函数对象，更加具有弹性，可接纳“与给定之目标签名式兼容”的所有可调用物

4. 古典的Strategy

## 条款 36:绝不重新定义继承而来的non-virtual函数

## 条款 37:绝不重新定义继承而来的缺省参数值
virtual 函数是动态绑定，而缺省参数值却是静态绑定。所以在调用一个定义于派生类的虚函数时，却使用基类为它所指定的缺省参数值。

## 条款 38：通过复合塑模出has-a或“根据某物实现出”
* 复合与public继承完全不同
* 应用域内，复合意味着has-a。在实现域内，复合意味着（根据某物实现出）

## 条款 39:明智而审慎地使用private继承
private继承：编译器不会自动将一个派生类对象转换为一个基类对象，私有继承类的protected 和 public成员，在派生类中都变成私有属性。

当派生类需要访问基类中保护的成员或需要重新定义继承而来的虚函数时，可设计为私有继承。

## 条款 40:明智而审慎地使用多重继承
为了防止多重继承导致最上层基类被重复使用，使用虚基类。
virtual 继承增加大小，速度，初始化复杂度等等成本
正当用途：public继承某个Interface class 和 private继承某个协助实现的class





