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

### 多重继承

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
//在祖先相同时，使用多重继承必须引入虚基类，并修改构造函数初始化列表的规则。
//否则，在将派生类地址赋给基类地址时产生二义性。
```

* 模板
见知识一栏：C++模板用法

## 第15章 友元、异常和其他
* 友元类：友元类的所有方法否可以访问原始类的私有成员和保护成员

```C
class Tv
{
public:
    friend class Remote;
    // 友元类
}
```

* 友元成员函数：选择让特定的类成员成为另一个类的友元

```C
class Tv; // 前向声明Tv
class Remote
{...}; // Remote 方法调用TV，因此需要知道Tv
class Tv
{
    friend void Remote::set_chan(Tv &t,int c); 
    // 编译器需要知道Remote定义；
};
```

* 互相友元
* 共同的友元

### 异常
* about函数：向标准错误流（cerr使用的错误流）发送消息abnormal program termination(程序异常终止),然后终止程序。
* 示例：

```C
double hmean(double a ,double b)
{
    if (a == -b)
        throw "bad hmean() arguments: a = -b not allow";
        // 不是将控制权返回给调用程序，而是导致程序沿函数调用序列后退
    return 2.0*a*b/(a+b);
}

int main()
{
    ...
    while(std::cin>>x>>y)
    {
        try{
        z = hmean(x,y) throw(bad_thing);
        // 异常规范，异常规范可能出现在函数原型和函数定义中，可包含类型列表。
        // 告诉用户可能需要使用try块，还有让编译器添加执行运行阶段检查代码
        }
        catch(const char *s) // 接受throw抛出的异常，可以是对象
        {
            std::cout<<"Enter a new pair of numbers:";
            continue;
        }
        catch(...)  //用于捕获任何异常
        {
        }
    }
}
// 程序进行栈解退以回到能够捕捉异常的地方，将释放栈中的自动存储变量
// catch 块的排列顺序应该与派生顺序相反，这样才能优先接受最接近的对象类型
```

* exception 类
包含一个what()的虚拟成员函数：返回一个错误信息字符串
exception 类
![exception-w300](http://cdn.pigstars.com/img/阅读/20170708/exception类.png)
- 未捕获异常
不会导致程序立刻异常终止，首先调用函数terminate()，terminate调用about()函数，可用set_terminate修改terminate()行为
- 意外异常（在异常规范之外的异常）
程序将调用unexpected()函数，该函数调用terminate()

### RTTI(运行阶段类型识别)
* dynamic_cast 将使用一个指向基类的指针来生成一个指向派生类的指针；否则，运算符返回空指针

```C
Superb * pm = dynamic_cast<Super *>(pg);
// 指针pg类型能否可被安全地转换为Superb *？ 如果可以，运算符将返回对象的地址，否在返回一个空指针。

```
* typeid 使得能够确定两个对象是否为同种类型,可以接受两种对参数 类名和结果为对象的表达式

```C
typeid(A) == typeid(B);
```

### 类型转换运算符
const\_cast 运算符用于只有一种用途的类型转换，即改变值为const或volatile
static\_cast 
reinterpres\_cast 

## 第16章 string 类和标准模版库

### 智能指针模板类 
* 普通指针new时，分配堆中内存，但不回收，从而容易导致内存泄漏
* 智能指针模板（auto\_ptr,unique\_ptr,shared\_ptr）（包含头文件memory）都定义类似指针的对象，当指针过期时，其西沟函数将使用delete来释放内存。
- 建立所有权概念，对于特定的对象，只能有一个智能指针可拥有它，只有拥有对象的指针指针的构造函数会删除该对象，从而转让所有权 - auto_ptr(被抛弃),unique_ptr(会在编译阶段报错)
- 创建智能更高的指针，跟踪引用特定对象的智能指针，引用计数 - shared_ptr

### 标准模板库（STL）
STL 提供了一组表示个容器，迭代器，函数对象和算法的模板。
容器：一个与数组类似的单元，可以存储若干个值。容器有一定要求特征。容器类型有：deque,list,queue,priority\_queue,stack,vector,map,multimap,set,multiset和bitset。C++新增forward\_list,unordered\_map,unordered\_multimap,unordered\_multiset;
算法：完成特定任务的处方
迭代器：能够用来遍历容器，是广义指针。迭代器类型有：输入迭代器，输出迭代器，正向迭代器，双向迭代器，随机访问迭代器；
函数对象：类似函数的对象，可以是类对象或函数指针

* 模板使得算法独立与存储的数据类型，迭代器使算法独立于使用的容器类型
* 模版提供了存储在容器中的数据类型的通用表示，迭代器提供了用于遍历容器中的值的通用表示。
* 函数对象-函数符。可包括函数名，指向函数的指针和重载了()运算符的类对象。
* 算法：STL将算法库分成4组
    - 非修改式序列操作：对区间中的每个元素进行操作，不修改容器的内容
    - 修改式序列操作：对区间的每个元素进行操作，可以修改内容
    - 排序和相关操作：排序函数和其他各种函数
    - 通用数字运算：将区间的内容累计，计算两个容器内部乘积等
 

