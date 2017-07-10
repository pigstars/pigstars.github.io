---
layout: post
title: "C++ 新标准"
description: "description"
category: "知识"
tags: "CPP"
---
# 探讨 C++ 新标准

* 新类型：C++ 11 
新增long long 和unsigned long long以支持64位整型。
新增类型char16_t和char32_t 以支持16位和32位的字符表示

* 统一初始化 {} 扩大了初始化列表，使其可用于所有内置类型和用户定义的类型。使用初始化列表时，可添加等号，也可不添加等号。
防缩窄，std::initializer_list

* 声明：
auto 关键字：存储类型说明符，将其实现自动类型推断。 
decltype 关键字：将变量类型声明为表达式指定的类型。 
返回类型后置：函数名和参数列表后面指定返回类型。(常用于模版类，返回未知类型)
模板别名：using= 关键字，提供创建别名的语法
nullptr： 空指针
智能指针： 摒弃auto_ptr,新增unique_ptr,shared_ptr,weak_ptr

```C
auto maton = 123;
double fm(double ,int );
auto pf = fm; // pf 的类型为 double (*)(double,int) 

double x
decltype (x) y; // y的类型与x相同
decltype (x*y) z;

double f1(double ,int )
auto f2(double ,int ) -> double ;

typedef std::vector<std::string>::iterator itType;
using itType = std::vector<std::string>::iterator;

``` 

* 异常规范修改：
摒弃异常规范，使用关键字noexcept 指出函数不会引发异常

* 作用域内枚举：
新枚举方式 class 或 struct

```C
enum old {yes, no, maybe};
enum class new1 {never ,somtime ,often ,always};
enum class new2 {never , lever ,sever};

//使用时 new1::never new2::never 
```

* 对类修改
explicit 以禁止单参数构造函数隐式转换
C++11 扩展该用法，使得可对转换函数做类似处理

```C
class Plebe
{
    operator int() const;
    explicit operator double () const;
}

Plebe a,b;
int n = a;
double x = b; // 非法
x = double(b)；
```

## 模板和STL方面的修改
* 基于返回的for循环,对数组或对象的每个元素执行指定的操作

```C
double prices[5] = {2.3,4.6,7.8,1.5,9.9};
for(auto x : prices)
{
    std::cout<<x<<std::endl;
}
```

* 新的STL容器: forward\_list,unordered\_map,unordered\_multimap,unordered\_set,unordered_multiset.

* 新的STL方法

C++11 新增STL方法cbegin()和cend() 元素视为const 还有crbegin(),crend()

* valarray 升级
* 摒弃export
* 尖括号：
为避免与运算符>>混淆，C++要求在声明嵌套模板时使用空格将尖括号分开，而C++则不要求此

```C
std::vector<std::list<int> > vl;
std::vector<std::list<int>> vl;
```
* 右值引用
C++11新增了右值引用，&&表示。右值引用可以关联到右值，即可出现在赋值表达式右边，但不能对其引用地址运算符的值。右值包括字面常量，x+y等表达式以及返回值的函数。

```C
int x = 10;
int y = 23;
int && r1 = 13;
int && r2 = x+y;
double r3 = std::sqrt(2.0);

// 右值关联到右值引用导致该右值被存储到特定位置，且可以获取该位置地址。
```

## 移动语义和右值引用
移动语义：使得数据仍然保留在原来位置，只修改记录

* 常规复制构造函数和移动构造函数：
    - 常规复制构造函数：使用const左值引用参数，该引用关联到左值右参
    - 移动构造函数：使用右值应用作为参数，关联到右值实参

    ```C
    Useless:Useless(const Useless & f) :n(f.n)
    {
    }
    
    Useless:Useless(const Useless && f):n(f.n)
    {
    }
    
    ```

* 赋值运算符与移动赋值运算符   
* 强制移动：使用头文件utility中声明的函数std::move()

## 新类功能
特殊成员函数：默认构造，复制构造，复制赋值运算符，析构函数 
C++11 新增移动构造函数和移动赋值运算符
* 默认的方法和禁用的方法
    - default：显示地声明这些方法默认版本

```C
class Someclass
{
public:
    Someclass() = default;
    Someclass(const Someclass &) = default; 
}
```
    - delete ：用于禁止编译器使用特定方法(声明为private也可)

```C
class Someclass
{
public:
    Someclass() = delete;
    Someclass(const Someclass &) = delete; 
}
```

* 委托构造函数
C++11 允许在一个构造函数的定义中使用另一个构造函数

* 继承构造函数
C++ 允许使用using，让函数的所有重载版本可用，使用这种方法让基类的所有非特殊成员函数对派生类可用，但不会使用与派生类构造函数特征标匹配的构造函数。

* 管理虚方法：override和final
    - override：指出要覆盖的虚方法，将其放在参数列表后面
    - final：禁止派生类覆盖特定的方法

## lambda函数
C++11 引入Lambda主要目的，能够将类似于函数的表达式用作接受函数之争或函数符的函数参数

```C
[](int x){return x % 3 == 0}

可给lambda指定名称
auto mod3 = [](int x){return x % 3 == 0};
lambda 可访问作用域内的任何动态变量，将其名称放于中括号内。

[&conunt13](int x){count13 += x%13 ==0};
[&](int x){count13 += x%13 ==0;count3 += x %3 ==0};
```

## 包装器
包装器用于给其他编程接口提供更一致或更合适的接口

function在头文件functional中声明，从调用特征标的角度定义一个对象，可用于包装调用特征标相同的函数指针

```C
std::function<Returntype(argType)> 
```
## 可变参数模板

能够创建可接受可变数量的参数的模板函数和模板类。
C++11 提供了一个用省略号表示的元运算符，能够表明模板参数包的标志符。
模板参数包基本上是一个类型列表，函数参数包基本上是一个值列表

```C
template<typename... Args>  // 模版参数包
void show_list(Args... args) // 函数参数包
{
}

// 使用递归展开参数包

template<typename T, typename... Args>
void show_list3(T value,Args... args)
{
    std::cout<<value<<", ";
    show_list3(arg...);
}

```
 
## 其他功能

thread_local 关键字：并行编程
atomic 库：原子操作
...

