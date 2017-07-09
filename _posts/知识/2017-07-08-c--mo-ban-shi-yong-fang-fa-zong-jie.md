---
layout: post
title: "C++ 模板使用方法总结"
description: "description"
category: "知识"
tags: "CPP"
---

# C++ 模板使用方法总结
**泛型**
## 函数模板
### 模板示例：

```C
template <typename AnyType>
void Swap(AnyType &a ,AnyType &b)
{
    AnyType temp;
    temp = a;
    a = b;
    b = temp;
}
```
第一行指出要建立一个模板函数，类型命名为AnyType(常用 T，Type)，typename可用关键字class代替

### 模版重载

```C
template <typename AnyType>
void Swap(AnyType &a ,AnyType &b,int t)
{
    AnyType temp;
    temp = a;
    a = b;
    b = temp;
}
```

* 模板函数显式具体化
    - 具体化优先于常规模板，非模板函数优先于具体化和常规模板
    - 显示具体化的原型和定义应该以template<>打头，并通过名称来指出类型

```C
struct job
{
    char name[40];
    double salary;
    int floor;
}
// 只想交换salary和floor；

template<> void Swap<job>(job &,job &);

//job可选 因为参数类型可表明
template<> void Swap(job &,job &);
```
### 实例化与具体化
代码中包含函数模板本身并不会生成函数定义，指示一个用于生成函数定义的方案。编译器使用模板为特定类型生成函数定义，得到模板实例。

显式实例化

```C
template void Swap<int>(int ,int);
// 告诉编译器，使用Swap模板生成int类型的函数定义
```

显式具体化

```C
template <> void Swap<int>(int &,int &);
template <> void Swap(int &,int &);
// 告诉编译器，不要使用Swap模板生成int类型的定义
```
* 重载解析
* 关键字decltype （C++11）
decltype(*expression*) var;
 - 如果expression是没有用括号括起的标志符，则var类型与该标志符相同。
 - 如果expression是一个函数调用，则var的类型与函数的返回类型相同（但是不会执行该函数）
 - 如果expression是一个左值，则var为指向其类型的引用
* 后置返回型 (C++11)

```C
template <class T1,calss T2>
?type? gt(T1 x,T2 y)
{
    return x+y;
}

新语法
template <class T1,calss T2>
auto gt(T1 x,T2 y) -> dcltype(x+y)
{
    return x+y;
}

```

## 类模板
### 类模板示例

```C
template <class Type>
class Stack
{
private:
    Type item[MAX];
    int top;
public:
    Stack();
    bool isEmpty();
    bool isFull();
    bool push(const Type & item);
    bool pop(Type & item);
    Stack & oprator = (const Stack &st) //可以在模板类声明或模板函数定义内使用Stack，但在指定返回类型或使用作用域解析运算符时，必须使用完整的Stack<Type>。
}
template <class Type>
Stack<Type> ::Stack()   // 需要修改类限定符
{
top = 0;
}
template <class Type>
bool Stack<Type>::isEmpty()
{
return top = 0;
}
```

* 使用模板类

```C
Stack<int> kernels;
Stack<string> clonels;
```
* template<_class T,int n_> : _为非类型或表达式参数。
表达式参数有一些限制，表达式参数可以是整型，枚举，引用或指针。
表达式参数方法使用的是为自动变量维护的内存栈。
* 模板多功能性
 - 递归使用模版

 ```C
 ArrayTP< ArrayTP<int,5>,10> twodee;
 ```
 - 使用多个类型参数
 - 默认类型参数

```C
tempalte <class T1,class T2 = int>
class Topo
{...}
```

* 模板类的实例化与具体化
实例化：告诉编译器生成函数定义
 - 隐式实例化：需要对象之前，不会生成类的隐式实例化
 
 ```C
 ArrayTP<double ,30> stuff
 ```
 - 显示实例化：生成类声明以及方法定义

 ```C
 template class ArrayTP<string,100>;
 ```

具体化：告诉编译器不要用模版生成该定义

- 显示具体化：特定类型（用于替换模版中的泛型）的定义。

```C
template <> class Classname<specialized-type-name>{...}
template <> class SortedArray<const char char *>
{
...
}
```

- 部分具体化：部分限制模版的通用性，例如：部分具体化可以给类型参数之一指定具体的类型

```C
template <class T1,class T2> class Pair{...};

template <class T1> class Pair<T1,int>{...};
// 将T2具体化为int

template <class T1> class Trio<T1,T1* T1*>
// 将T2，T3设置为 T1*
```

* 成员模板：模板可用作结构、类或模板类的成员。
根据调用时传入的值，一一确定模板类型。

* 将模板作为参数

```C
template <template <typename T> class Thing>
// template <typename T> class是类型，Thing是参数
例如：
template <template <typename T> class Thing>
class Crab
{
Thing<int> s1;
Thing<double> s2;
}
//
template <typename T>
class King{...}
// 使用一下声明
Crab<King> legs
//则 s1为 King<int> s2为King<double>
```

### 模版类和友元
- 非模板友元：

```C
template <class T>
class HasFriend
{
public:
    friend void counts();
    friend void report(HasFriend<T> &);
}
```

- 约束模板友元：友元的类型取决于类被实例化时的类型

```C
//1. 类定义前声明每个模版函数
template <typename T> void count();
template <typename T> void report(T &);

//2.函数中再次将模版声明为友元
template <class T>
class HasFriend
{
public:
    friend void counts<T>();
    friend void report<T>(HasFriend<T> &);
}

//3. 使用
```

- 非约束模版友元函数：友元的所有具体化都是类的每一个具体化的友元

```C++
template <typename T>
class ManyFriends
{
    tempalte <typename C,typename D> friend void show(C &,D &);
}
```

## 可变参数模板


