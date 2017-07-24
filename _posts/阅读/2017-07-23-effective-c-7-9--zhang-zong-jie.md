---
layout: post
title: "Effective C++ [7-9] 章总结"
description: "description"
category: "阅读"
tags: "EffectiveCPP"
---
# Effective C++ [7-9] 章总结
## 条款 41:了解隐式接口和编译期多态
* 通常显式接口由函数的签名式（函数的名称，参数类型，返回类似）构成
* 隐式接口由有效表达式组成

template 和 class 都支持接口和多态。对于class而言，接口显式的，以函数签名为中心。多态则通过virtual函数发生于运行期。对template而言，接口是隐式的，奠基于有效表达式。多态通过template具现化和函数的重载解析发生于编译器。
 
## 条款 42:了解typename的双重意义
template<class T> 和 template<typename T> 意义完全相同

> template 内出现的名称如果相依于某个template，则为从属名称 T hello     
> 如果从属名称在class内呈嵌套状，则为嵌套从属名称 T:num hello 

嵌套从属名称可能导致解析困难，使用typename标志嵌套从属类型名称，**不能**用在基类成员列或成员初值列内作为基类修饰符
 
## 条款43:学习处理模板化基类内的名称
```C
template<typename Company>
class LoggingMsgSender : public MsgSender <Company>
{
public:
    void sendClearMsg(const MsgInfo & info)
    {
        sendClear(info); // sendClear继承自基类MsgSender
    }
}
// 编译器无法识别sendClear，因为在遭遇该定义式时，不知道继承的基类Company参数，所以未知sendClear函数。
```

解决方法
1. 在base class函数调用动作前加 this->
2. 使用using声明式，using MsgSender<Company>::sendClear;
3. 明白指出被调用的函数位于base class内：MsgSender<Company>::sendClear(info);

## 条款 44:将参数无关的代码抽离template
使用template可能会导致代码膨胀，其二进制码带着重复的代码、数据等。
解决方法：

* 非类型模板参数造成的代码膨胀：将共同的操作提取出来，交由基类实现。以函数参数或class成员变量替换template参数

* 类型参数而造成的代码膨胀，让带有完全相同二进制表述的具现类型共享实现码。

## 条款 45:运用成员函数模板接受所有兼容类型
Templates和泛型编程：
使用member function templates 成员模板函数，在类内泛化copy构造函数并不会阻止编译器生成它们自己的copy构造函数和copy assignment操作符。

## 条款 46:需要类型转换时请为模板定义非成员函数
* template实参推导过程中从不将隐式类型转换函数纳入考虑

```C
template<typename T>
class Rational{
public:
    Rational(const T& numerator = 0,const T& denominator = 1);
    const T numerator () const;
    const T denominator() const;
    ...
};
template<typename T>
const Rational<T> operator * (const Rational<T> &lhs,const Rational<T> &rhs)
{...}

Rational<int> oneHalf(1,2);
Rational<int> result = oneHalf * 2;
// 无法通过编译，因为2 未能隐式的转换为Rational<int>类型
```

解决方法：
将operator * 声明为友元函数，并再内部实现或者内部调用一个外部函数。

## 条款 47:请使用traits classes表现类型信息
>Trait技术
> 非官方
> 当函数，类或者一些封装的通用算法中的某些部分会因为数据类型不同而导致处理或逻辑不同（而我们又不希望因为数据类型的差异而修改算法本身的封装时），traits技术能够很好的判断数据类型。

```C
// 模版类中的方法Compute 需求：参数为int返回为int，参数为float返回为int，参数为其他返回为其他
template <typename T>
class Test {
public:
     int Compute(int d);
private:
     T mData;
};


// 改进
template<typename T>
struct TraitsHelper{
    typedef T ret_type;
    typedef T par_type;
};
template <>
struct TraitsHelper<int>{
    typedef int ret_type;
    typedef int par_type;
};
template <>
struct TraitsHelper<float>{
    typedef float ret_type;
    typedef int par_type;
};

template <typename T>
class test
{
public:
    TraitsHelper<T>::ret_type Compute(TraitsHelper<T>::par_type d)
private:
    T mData;
}
```

* Traits classes使得“类型相关信息”在编译期可用，以templates和templates特化完成实现。
* 整合重载技术后，traits classes有可能在编译期对类型执行if...else测试

## 条款 48:认识template元编程
TMP超酷代码

```C
#include <iostream>
template<unsigned n>
struct Factorial{
	enum { value = n * Factorial<n-1>::value };
};

template <>
struct Factorial<0>{
	enum { value = 1 };
};
int main()
{
	std::cout << Factorial<10>::value << std::endl;
}
```

## 条款 49:了解new-handler的行为

* 当operator new无法满足某一内存分配需求，便抛出异常，以前会返回一个null指针。
* set_new_handler允许客户指定一个函数，在内存分配无法满足时被调用

## 条款 50:了解new和delete的合理替换时机
替换new和delete理由：
1. 用来检测运用上的错误：将new所得内存，delete失败则导致内存泄漏。各种编程错误导致overruns和underruns。
2. 为了强化效能：编译器自带new和delete主要用于一般目的，效率一般。
3. 为了收集统计上的数据：收集锻件如何使用其动态内存。
4. 为了增加分配和归还的速度。
5. 为了降低缺省内存管理器带来的空间额外开销。
6. 为了弥补缺省分配器中的非最佳对齐位

## 条款 51:编写new和delete时需固守常规
* new应该内含一个无穷循环，并在其中尝试分配内存。若无法满足，则调用new-handler，同时也应该有能力处理0bytes申请。Class专属版本还应该处理“比正确大小更大的错误申请”。
* operator delete应该在收到null指针时不做任何事。专属版本还应该处理比正确大小更大的申请。

## 条款 52:写了placement new 也要写placement delete
* 如果operator new接受的参数除了一定会有的那个size_t之外还有其他，这便是所谓的placement new.
* placement view 特别有用的一个是“接受一个指针指向对象该被构造之处”。
* 当声明placement new和placement delete，请确定不要无意识地遮掩了它们的正常版本

## 条款 53:不要忽略编译器的警告

## 条款 54:让自己熟悉包括TR1在内的标准程序库

## 条款 55:让自己熟悉Boost



