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







