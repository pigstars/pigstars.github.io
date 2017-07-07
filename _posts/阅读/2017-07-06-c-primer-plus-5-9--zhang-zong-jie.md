---
layout: post
title: "C++ Primer Plus [5-9] 章总结"
description: ""
category: "阅读"
tags: "CppPrimerPlus"
---
    
# C++ Primer Plus[5-9] 章总结
## 第5章循环和关系表达式
* 读取char值时，cin将忽略空格和换行符
* cin.get两个版本
  - cin.get(name,ArSize) 接受字符串的地址和长度
  - cin.get(ch) // 返回cin对象
  - ch = cin.get()  //返回字符对象

  类型 | 传递输入字符的方式 | 用于字符输入时的函数返回值 | 到达EOF时的函数返回值
  --- | --- | --- |--- 
  cin.get(ch) | 赋值给参数 | istream对象（执行bool转换后为true）| istream对象 （执行bool为false）
  ch = cin.get() | 将函数返回给ch | int类型的字符编码 | EOF 
  
## 第6章 分支语句和逻辑运算符
和C类似

## 第7章 函数--C++的编程模块
* 若定义的为数字类型的，cin时输入字符类型的，将设置错误调节，禁止进一步读取输入，必须使用cin.clear()重置输入。
* 函数指针

```C
void ToUpper (char * ) // 函数声明

void (*pf)(char * ) // 声明函数指针pf.该指针指向的函数接受一个字符指针，返回值为void.

void (*pf[3])(char *) //声明函数包含三个函数指针的数组

void * (*pf[3])(char *) //声明一个指向包含三个函数指针的数组的指针

auto pf2 = pf // 使用C++11的自动类型推断功能，可以直接使得pf2的类型为pf的类型

(*pf)(mis) 
ToUpper(mis) // 两者等价，都表示执行函数ToUpper
```
## 第8章 函数探幽
* 内联函数，适合小型，非递归函数。编译器使用相应的函数代码替换函数调用，内联函数的运行速度比常规的函数稍快。
 - 在函数的声明和定义前加上关键字 inline

```C++
inline double square(double x){return x*x};
```
* **引用变量**,引用是已定义的变量的别名，主要用途：用作函数的形参。

```C++
int rats;
int & rodents 　　= rats; // 必须在声明引用时将其初始化，参数必须时变量。不能是表达式。
int && jerf = 2.0*j + 18.5 // C++11 新增了另一种引用，右值引用，可指向右值
```
* 返回引用的函数实际上时被引用的变量的别名，避免返回函数终止时不在内存中的内存单元的引用。

```C++
const free_throw & clone(free_throw & ft)
{
    free_throw newguy;
    newguy = ft;
    return newguy;
}
//函数结束时，newguy内存被回收，
// 解决方法：
// 返回一个作为参数传递给函数的引用
// 用new分配新的存储空间，
```
使用引用参数原因：程序员能够修改调用函数的中的数据对象
通过传递引用而不是整个数据对象，可以提高程序的运行速度。
数组的函数调用只能使用指针
* 默认参数，

```C++
int harpo(int n,int m = 4,int j = 5) // 必须从右向左添加默认值
```
* 函数重载的关键时函数的参数列表（函数的特征标），参数数目和／或参数类型不同，特征标不同。

```C++
long gronk(int n,float m);
double gronk(int n,float m);
// 非法，两个声明互斥，无法重载。函数重载返回类型可以不同，但特征标也必须不同
```
* 名称修饰：C++用来跟踪每一个重载函数，根据函数原型中指定的形参类型对每个函数名进行加密
* 函数模板：通用函数的描述

```
template <typename AnyType>
void swap(AnyType &a , AnyType &b); // 原型

template <typename AnyType>
void swap(AnyType &a ,AnyType &b)
{
}
```
* 编写的模板函数很有可能无法处理某些类型：
- 显示具体化：提供一个具体化函数定义
 具体化优先于常规模版，而非模板函数优先于具体化和常规模板
 
```C++
template <> void Swap<job> (job & ,job &); // 函数原型

template <> void Swap<job> (job & j1,job & j2) // 函数定义
{ 

}  // <job>可省略
```
- 实例化
```C++
template void Swap<int>(int &,int &); //显式实例化只需声明，不需要重新定义。编译器根据模板实现实例声明和实例定义。 
```
* 重载解析将寻找最匹配的函数。
    - 只存在一个，则选择
    - 多个适合函数，只有一个非模板，选择非模板
    - 多个适合函数，都为模板函数，选择最具体的模板函数
    - 多个同样合适的非模板函数或模板函数，则错误

* 自定义选择  

```
lesser<>(m,n) // 编译器应该选择模板函数，而不是非模板函数
lesser<int>(m,n) //使用显示实例化得到的函数
```
* decltype 用于查询表达式的数据类型
 
```
int x;
decltype(x) w;
long indeed(int);
decltype (indeed(3)) m // 不会实际调用函数，编译器通过查看函数的原型来获悉返回类型，而无需实际调用函数
```
* 后置返回类型  

```C++
auto h(int x, int y) -> decltype(x+y)
{

}
```
## 第9章 内存模型和名称空间
* C++11使用四种方式存储数据
  自动存储持续性，静态存储持续性，线程存储持续性（C++11） thread_local，动态存储持续性 new delete 
* 所有未被初始化的静态变量的所有位都被设置为0
* 单定义规则，变量只能有一次定义。C++提供两种变量声明：一种定义声明，给变量分配存储空间。另一种引用声明，不给变量分配空间，关键字extern。
如果要在多个文件中使用外部变量，只需在一个文件中包含该变量的定义，但在使用该变量的其他所有文件中，使用extern声明它。
C++提供了作用域解析运算符::，放在变量名前面时，表示使用变量的全局版本
* volatile 表明即使程序代码没有对内存单元进行修改，其值也可能发生变化。告诉编译器，不能进行一些优化
* const 表明内存被初始化后，程序编不能再对它它进行修改。const全局变量的链接性为内部，全局const定义相当于使用了static说明符。如果希望常量为外部链接性：extern const int states = 50
* 函数的链接性
  所有函数的存储性都自动为静态，默认为外部。使用static将函数的链接性设置为内部，使之只能在一个文件中使用。
* 语言的链接性

```C++
 extern "C" void spiiff (int)
 extern "C++" void spaff(int)
```
* 定位new运算符 

```C++
char buffer[512];
double * p1x
p1 = new (buffer) double [N];  // new运算符使用传递给它的地址
```
* 名称空间：
用户定义名称空间：

```C
namespace Jack{
    double pail;
    void fetch();
    int pal;
}
namespace Jill{         // 名称空间嵌套
    double fetch;
    namespace Bill
    {
        int flame;
    }
    float pal;
}   // using namespace Jill::Bill
    
namespace
{
    int ice;
    int bandyycoot;
}    // 潜在作用域为从声明点到声明区域末尾
```





