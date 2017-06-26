---
layout: post
title: "C Primer Plus [14-17] 章总结"
description: ""
category: "阅读"
tags: "C Prim Plus"
---

##  第14章 结构和其他数据形式
* 结构的声明中，可以使用无结构标记
  例如： 
  
```C
struct {
    char title[MAXTITL];
    float value;
} library;
```
  
* 初始化静态存储期的变量必须使用常值
* 结构的初始化器
 	
```c
struct library lib = {.title="Hello World",.value=22.5}
```
 
* ANSIC C 及以上版本结构允许双向通信。结构体之间互相赋值
* 传递结构体变量时使用结构指针，效率更高
* 复合字面量

```C
{struct library}{"Hello World",22.5}
```

* 伸缩型数组成员(flexible array member)
  - 伸缩型数组成员必须是最后一个成员
  - 结构中必须至少有一个成员
  - 声明类似数组，方括号中为空

```C
struct flex
{
	int count;
	double average;
	double scores[];
}
```
希望声明一个指向struct flex 类型的指针，然后用malloc分配足够的空间，以struct flex中常规内容和伸缩性数组成员所需要的额外空间.

```C
struct flex * pf;
pf = malloc (sizeof(struct flex)+5*sizeof(double))
```
要求：

	- 不要使用结构进行赋值或拷贝
	- 不要以按值方式把这种结构传递给结构
	- 不要使用带伸缩型数组成员的结构作为数组成员或另一个结构的成员，要把结构的地址传递给函数
	
* **联合**：一种数据类型，能在同一个内存中存储不同的数据类型。
编译器分配足够的空间以便它能存储联合声明中占用最大字节的类型。
**联合只允许初始化第1个成员**

```c++
union hold{
	int digit;
	double bigfl;
	char letter;
}
```

* 枚举类型：声明符号名称来表示整型常量。

```C
enum spectrum { red,orange,green ,blue ,violet }
```
枚举符都是int类型,默认从0开始
可以为枚举常量指定整数类型，后面的常量被赋予后续的值

* 函数指针

```C
void ToUpper (char * ) // 函数声明

void (*pf)(char * ) // 声明函数指针pf.该指针指向的函数接受一个字符指针，返回值为void.

(*pf)(mis) 
ToUpper(mis) // 两者等价，都表示执行函数ToUpper
```

## 第15章 位操作
* 用法：掩码 设置某些位的开关 flag & MASK
* 用法：打开位 需要打开一个值的特定位，同时保持其他位不变 flag | MASK
* 用法：关闭位 需要不影响其他位的情况下关闭指定的位 flag = flag & ~MASK
* 用法：切换位 打开已关闭的，或关闭已打开的。 flag = flag ^ MASK 将切换该值与MASK为1的位相对应的位
* 用法：检查位 需要检查某位的值 （flag & MASK）== MASK
* 位字段：

```c
struct {
	unsigned int autfd :1; 
	unsigned int bldfc :1;
	unsigned int undln :1;
	unsigned int itals :1;
} prnt;
```
- 声明总位数超过unsigned int 会用到下一个int，会自动移动跨界字段
- _Alignas 说明符指定一个变量或类型的对齐值

##	第16章 C预处理器和C库
* C预处理器在程序执行之前查看程序，预处理器不做计算，不对表达式求值，只进行替换

```C
#define SQUARE(X) X*X
```
X为宏参数列表，X*X为替换列表
 #define 中使用参数
	- 函数调用在程序运行时把参数的值传递给函数
	- 宏调用在编译之前把参数记号传递给程序

* 用宏参数创建字符串：#运算符

```C
#define PSQR(x) printf("The square of "#x" is %d .\n",((x)*(x)))
```
C允许在字符串中包含宏参数，在类函数宏的替换体中，#号作为一个预处理运算符，可以把记号替换为成字符串，x为宏形参，那么#x就是转换为字符串“x”的形参名

* 预处理器黏合剂：##运算符

```c
#include <stdio.h>
#define XNAME(n) x##n
#define PRINT_XN(n) print("x"#n"=%d\n",x##n)
int main()
{
	int XNAME(1) = 14;
	int XNAME(2) = 20;   // int x2=20
	int x3 = 30;
	PRINT_XN(1);	// printf("x1=%d\n",x1);
	PRINT_XN(2);
	PRINT_XN(3);
	return 0;
}

// x1=14
// x2=20
// x3=30
```
运算符把记号组合为一个新的运算符

* 变形参 ... 和 __VA_ARGS__  以及后面的可变参数库stdarg.h
* 宏：宏生成内联代码，在程序中生成语句，不用担心变量类型
* 文件包含，当预处理器发现#include 指令时会查看后面的文件名并把文件的内容包含到当前文件，替换#include指令的位置
  UNIX中，尖括号告诉预处理器在标准系统目录中查找该文件。双引号表示首先在当前目录中（或文件名中指定的其他目录）查找该文件，如果未找到再查找标准系统目录
* 使用头文件，**明示常量**，**宏函数**，**函数声明**，**结构模版定义**，**类型定义**
* #undef 指令用于“取消”已定义的#define指令

```C
#define LIMIT 400
#undef LIMIT
```
* #ifdef,#else,#endif

```C
#ifdef MAVIS
	#include "horse.h"
	#define STABLES 15
#else
	#include "cow.h"
	#define STABLES 15
```

* #ifndef：用法与#ifdef用法类似，判断后面的标志符是否是未定义的，常用于定义之前未定义的常量
* 预定义宏

宏 | 含义
---------|---------
\_\_DATE\_\_ | 预处理的日期
\_\_FILE\_\_ | 表示当前源代码文件名的字符串字面量
\_\_LINE\_\_ | 表示当前源代码文件中行号的整型常量
\_\_STDC\_\_ | 设置为1，表明实现遵循C标准
\_\_STDC_HOSTED\_\_ | 本机环境设置为1，否则设置为0
\_\_STDC_VERSION\_\_ | 支持C99标准，设置为199901L；支持C11标准，设置为201112L
\_\_TIME\_\_ | 翻译代码的时间，格式为“hh:mm:ss”
\_\_func\_\_ | 代表函数名的字符串

* #line指令重置__LINE__ 和 __FILE__
* #error 指令让预处理器发送一条错误消息
* 现在的编译器中，编译器都有自己的编译指示集，可以通过命令行参数或IDE菜单修改编译器的一些设置
	#pragma 把编译器指令放入源代码中，可以在源代码中修改编译器的一些设置
* 泛型选择，没有特定类型，但是一旦指定类型，就可以转换成指定类型的代码

```C
#define MYTYPE(X) _Generic((X),\
	int:"int",\
	float:"float",\
	double:"double",\
	default:"other"\
```
*内联函数，C99和C11标准：把函数变成内联函数尽可能快地调用该函数，编译器可能会用内联代码替换函数调用，并执行一些优化。
* 断言,assert()，若表达式为假，就在标准错误流中写入一条错误信息

## 第17章 高级数据表示
* ADT抽象数据类型：
	- 类型名：
	- 类型熟悉：
	- 类型操作：


