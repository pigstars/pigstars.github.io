---
layout: post
title: "C++ Primer Plus[14-18] 章总结"
description: "description"
category: "阅读"
tags: "CppPrimerPlus"
---
# C++ Primer Plus[14-18] 章总结
## 第14章 C++中的代码重用
* is-a 模型：公有继承
* has-a 模型：包含对象成员的类或私有继承
包含情况下：**类可以获得成员对象的实现，但没有继承接口**；

```C
// 初始化被包含对象.初始化列表中使用成员名,初始化列表中的每一项都调用与之匹配的构造函数。
// 若不使用初始化列表语法，则默认使用成员对象所属的类的默认构造函数
Student(const char *str,const double *pd,int n):name(str),scores(pd,n)
{
}

//访问方法
double Student::Average() const
{
    if(scores.size() > 0)
        return scores/sum()/scores.size();
    else
        return 0;
}
```

私有继承情况：私有继承，基类的公有成员和保护成员都将成为派生类的私有成员

```C
// 包含版本提供了两个被显式命名的对象成员，私有继承本本提供了两个无名称的字对象成员

class Student : private std::string,private std::valarray<double>
{
}

Student(const char *str,const double *pd,int n):std::string(str),std::valarray(pd,n)
{
}

//访问方法
double Student::Average() const
{
    if(std::valarray::size > 0)
        return std::valarray::sum()/std::valarray::size();
    else
        return 0;
}

//访问基类对象 使用强制类型转换
const string & Student::Name() const
{
    return (const string &) *this;
}

// 私有继承中，未进行显式类型转换的派生类引用或指针，无法赋值给基类的引用或指针
```
通常，应使用包含来建立has-a关系，如果新类需要访问原有类的保护成员，或需要重新定义虚函数，则使用私有继承。

* 多重继承
    - 虚基类：虚基类使得从多个类（基类相同）派生出的对象只继承一个基类对象。声明中使用关键字：virtual

```C
Woker<-Singer<-SingerWaiter
Woker<-Waiter<-SingerWaiter

构造函数
// 非虚基类 ： 自动传递
SingingWaiter(const Worker &wk,int p = 0,int v = Singer::other):
Waiter(wk,p),Singer(wk,v)
{
}

// 虚基类 ： 无自动传递
SingingWaiter(const Woker &wk,int p = 0 ,int v = Singer::other):Worker(wk),Waiter(wk,p),Singer(wk,v)
{
}
在祖先相同时，使用多重继承必须引入虚基类，并修改构造函数初始化列表的规则。否则，在将派生类地址赋给基类地址时产生二义性。
```

* 模板
见

