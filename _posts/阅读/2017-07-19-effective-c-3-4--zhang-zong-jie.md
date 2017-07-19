---
layout: post
title: "Effective C++ [3-4] 章总结"
description: "description"
category: "阅读"
tags: "EffectiveCPP"
---
## Effective C++ [3-4] 章总结
## 条款 13:以对象管理资源
问题

```C
void f()
{
    Investment * pInv = createInvestment();
    ...
    delete pInv;
}

// 若中间过早的返回，则可能导致pInv指向的内容没有被释放，导致内存泄漏
```
解决方法：

```C
// 标准程序库 auto_prt 以管理资源对象
std::auto_ptr<Investment> pInv(creatInvestment());
```

- 获得资源后立刻放进管理对象 ：资源取得便是初始化时机
- 管理对象运用析构函数确保资源被释放
auto_prt ： 若通过copy构造函数或copy assignment操作符复制它们，它们会变成null，而复制所得的指针将取得资源的唯一拥有权

std::tr1::shared_ptr ：持续追踪共有多少对象指向某笔资源，并在无人指向它时自动删除该资源。

## 条款 14:在资源管理类中小心copying行为
当某些情况下，需要自己编写资源管理类时，在copy时有一下策略
1 禁止复制
2 对底层资源祭出“引用计数法”
3 复制底部资源
4 转移底部资源的拥有权

* 复制RAII（resource acquisition is initialization 资源取得时机便是初始化时机）对象必须一并复制它所管理的资源，所以资源的copying行为决定RAII对象的copying行为。
* 普遍而见的RAII class copying行为时：抑制copying，施行引用计数法。不过其他行为也可能被实现。

## 条款 15:在资源管理类中提供对原始资源的访问

tr1:shared_prt 和 auto_ptr 都提供一个get成员函数，用来执行显式转换，返回智能指针内部的原始指针（的复件）

RAII class 设计时两种策略

1. 提供显式转换函数
2. 提供隐式转换函数：容易增加错误发生机会

## 条款 16:成对使用new和delete时要采取相同形式
* 如果在new表达式中使用[]，必须在相应的delete表达式中也使用[]
* 如果在new表达式不使用[]，一定不要在相应的delete表达式中使用[]

## 条款 17:以独立语句将new对象置入智能指针

```C
processWidget(std::tr1::shared_ptr<Widget> (new Widget),priority());

//编译器必须创建代码，做以下三件事情：
// 1.调用priority
// 2.执行 “new Widget”
// 3.调用 tr1::shared_ptr构造函数

// 1 与其他两者顺序位置 ,若顺序为 2 1 3 对priority调用导致异常，则new Widget返回的指针将移失
```
解决方法

```C
std::tr1::shared_prt<Widget> pw(new Widget);
// 在单独语句内以智能指针存储new所得对象
processWidget(pw,priority()); 
```

## 条款 18:让接口容易被正确使用，不易被误用

* “促进正确使用”的办法包括接口的一致性，以及与内置类型的行为兼容
* “阻止误用”的办法包括建立新类型，阻止类型上的操作，束缚对象值（通过枚举函数），以及消除客户的资源管理责任
* tr1:：shared_ptr 支持定制型删除器。可防范DLL（动态连接程序库）问题。

shared_prt 比原始指针大且慢，而且使用辅助动态内存。在许多应用程序中这些额外的执行成本并不显著，然而其“降低客户错误”的成效却是每个人都看的到的。

## 条款 19:设计class 犹如设计type

设计类时，应该考虑的设计规范：

* 新type的对象应该如何被创建和销毁？
* 对象的初始化和对象的赋值该有什么样的差别？
* 新type的对象如果被传值调用，意味着什么？ copy构造函数用来定义一个type的pass-by-value 该如何实现？
* 什么是新type的“合法值”？
* 你的新type需要配合某个继承图系吗？
* 你的新type需要什么样的转换？ 类型转换函数之类
* 什么样的操作符合函数对此新type而言是合理的？
* 什么样的标准函数应该驳回？ 应该声明为private者
* 谁该取用新type的成员？
* 什么是新type的“未声明的接口”？ 效率、异常安全性以及资源运用提供何保证
* 你的新type有多么一般化？  是否应该定义tempalte

## 条款 20：宁以pass-by-reference-to-const 替换 pass-by-value

* 当为传值调用时，会获得实参的复件为初值，还容易造成 对象切割
* 而用传引用调用时，没有任何构造函数或析构函数被调用
* 当为内置类型以及STL迭代器和函数对象，则采用传值调用

## 条款 21：必须返回对象时，别妄想返回其reference
* 绝对不能返回指针或引用指向的定义在栈中的对象。
* 绝对不能返回引用指向的堆中的对象（因为无法揪其指针进行释放）
* 绝对不能返回指针或引用指向局部静态对象而有可能同时需要多个这样的对象（因为始终为同一个）

## 条款 22：将成员变量声明为private
* 将成员变量声明为private。可赋予客户访问数据的一致性、可细微划分访问控制、允诺约束条件获得保证、并提供class作者以充分的实现弹性
* protected 并不比 public更具封装性

## 条款 23：宁以non-member、non-friend 替换member函数
* 拿non-member non-friend 替换member函数可以增加封装性、包裹弹性和技能扩充性

## 条款 24：若所有参数皆需类型转换，请为此采用non-member函数
* 如果需要为某个函数的所有参数进行类型转换，那么这个函数必须是一个non-member 函数，若为成员函数则包含隐式的参数this无法转换。

## 条款 25：考虑写一个不抛出异常的swap函数


