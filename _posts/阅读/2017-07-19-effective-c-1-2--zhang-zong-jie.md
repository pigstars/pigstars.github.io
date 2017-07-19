---
layout: post
title: "Effective C++ [1-2] 章总结"
description: "description"
category: "阅读"
tags: "EffectiveCPP"
---
 
# Effective C++ [1-2] 章总结

## 条款 01：视C++ 为一个语言联邦
C++ 大体可分为四个次语言

* C 
* Object-Oriented C++ : 类，封装，继承，多态
* Template C++ ： 泛型编程
* STL ： template程序库，容器，迭代器，算法以及函数对象

## 条款 02：尽量以const，enum，inline替换#define

对于单纯常量，最好以const对象或enum替换#define

```C
#define ASPECT_RATIO 1.653
// 由于编译器处理源码之前被预处理器移走，而导致未进入记号表（symbol table），编译错误时可能会直接提1.653 导致难以寻找

const double ASpectRatio = 1.653 
// 进入记号表，可能导致较小量的码，因为预处理器会将宏名称替换为1.653导致目标码出现多份1.653
 
class GamePlayer {
private: 
	static const int NumTurns = 5;
	int scores[NumTurns];	
};

class GamePlayer {
private:
    enum {NumTurns = 5};
    int scores[NumTurns];
};
// 无法取enum地址
```

对于形似函数的宏定义，改用inline函数替换defines

## 条款 03：尽可能使用const

**如果关键字const出现在星号左边，表示被指物是常量；**
**如果出现在星号右边，表示指针自身是常量；**
**如果出现在星号左右两边，表示被指物和指针都是常量；**

```C
const Rational operator * (const Rational & lhs,const Rational & rhs);
// 声明为const 防止出现 （a * b) = c;
```

const 中可以通过使用mutable关键字来表示成员变量可能会被更改

* 当const和non-const成员函数有着实质等价的实现时，令non-const版本调用const版本避免代码重复

## 条款 04：确定对象被使用前已先被初始化

* C++ 规定，对象的成员变量初始化动作发生在进入构造函数本体之前。
* 使用member initialization list（成员初值列）替换赋值工作
* 规则：规定总是在初值列中列出所有成员变量，以免还得记住哪些成员变量可以无需初值。
若有多个构造函数，改用它们的赋值操作，并将那些赋值操作移往某个函数（private）

```C
// 定义于不同的编译单元内的non-local static 对象的初始化相对次序并无明确定义
class FileSystem{...};
FileSystem & tfs()
{
    static FileSystem fs;
    return fs;
}
// 定义并初始化一个local static对象，返回一个reference指向上述对象。
```
* 为免除“跨编译单元之初始化次序”问题，以local static对象替换non-local static对象。

## 条款 05: 了解C++ 默默编写并调用哪些函数
编译器默默声明：copy构造函数，copy assignment操作符和一个析构函数，以及构造函数

若其中有引用或者常量时，或者copy assignment操作符声明为private时，编译器拒绝生成默认构造函数。

## 条款 06： 若不想使用编译器自动生成的函数，就该明确拒绝
* 声明一个private成员函数，阻止编译器暗自创建其专属版本
* 继承一个uncopyeable基类

## 条款 07: 为多态基类声明virtual析构函数
若当前类将会作为基类被继承，则析构函数最好申请为virtual函数，可防止内存泄漏。
心得：只有当class内至少一个virtual函数，才为它声明virtual析构函数

析构函数运作方式：最深层派生的那个class其析构函数最先被调用，然后是其每一个base class的析构函数被调用。

## 条款 08:别让异常逃离析构函数

* 当两个异常同时存在，程序若不是结束执行就是导致不明确行为
* 因此 析构函数绝对不要吐出异常。如果一个被析构函数调用的函数可能抛出异常，析构函数应该捕捉任何异常，然后吞下它们或结束程序
* 如果客户需要对某个操作函数期间抛出的异常做出反应，那么class应该提供一个普通函数（而非在析构函数函数中）执行该操作

## 条款 09:绝不在构造和析构过程中调用virtual函数

```C
class Transaction {
public:
    Transaction();
    virtual void logTransaction() const = 0;
};
Transaction::Transaction()
{
    ...
    logTransaction();
}

class BuyTransaction:public Transaction{
public:
    virtual void log TRansaction () const;
    ...;
};


BuyTransaction b;
// 派生类对象内的基类成分在派生类自身成分被构造之前先构造妥当
// 此时 被调用logTransaction是Transaction内版本，而不是BuyTransaction版本
// 基类构造期间virtual函数不会下降到派生类阶层
```
* 构造函数和析构函数期间不要调用virtual函数，因为这类调用从不下降至派生类（比起当前构造函数和析构函数那层）

## 条款 10:令operator= 返回一个reference to *this

为了方便连续使用 = 
* 令赋值操作符返回一个reference to *this

## 条款 11：在operator= 中处理“自我赋值”

```C
// 不安全代码,当出现自我赋值时，出现问题
Widget & Widget::operator = (const Widget & rhs)
{
    delete pb;
    pb = new Bitmap(*rhs.pb);
    return * this;
}

// 加入证同测试

if(this == &rhs) return *this;

// 当new Bitmap 出现异常， pb指向一块被删除的Bitmap ，修改如下

Widget & Widget::operator = (const Widget & rhs)
{
   Bitmap * pOrig = pb;
   pb = new Bitmap(*rhs.pb); // 若new Bitmap 出现异常，则pb保持原状
   delete pOrig;
   return *this;
}
```

## 条款 12：复制对象时勿忘其每一个成分
* 当编写copying函数，确保 复制所有local成员变量 ，调用多有base classes 内的适当的copying函数
* 消除copy构造函数和 copy assignment操作符的相近代码：建立一个新的成员函数给两者调用。
 

