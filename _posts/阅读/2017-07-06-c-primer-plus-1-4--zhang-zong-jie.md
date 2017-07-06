---
layout: post
title: "C++ Primer Plus [1-4] 章总结"
description: ""
category: "阅读"
tags: "C++PrimerPlus"
---
    
# C++ Primer Plus [1-4] 章总结
## 第1章 预备知识
* C++诞生于贝尔实验实验室，Bjarne Stroustrup 于20世纪80年代开发出。
* 1990 美国国家标准局（ANSI）专门负责制定C++标准。国际标准化组织（ISO）很快加入行列，一起制定C++标准
	C++98 -> C++98/C++03 -> C++11
* UNIX编译和链接：编译，编译器生成一个扩展名为.o的目标代码文件，编译器自动将目标代码文件传递给系统链接程序，生成可执行文件a.out。如果只使用一个源文件，链接程序将删除spiffy.o。如果有多个则不删，方便下次链接。

## 第2章 开始学习C++
* 通常，main函数被启动代码调用，启动代码由编译器添加到程序中
* 名称空间，有助于组织程序。防止不同文件中相同的名称之间干扰

 ```C++
 using namespace std;  //放在函数之前，让文件中左右函数都能够使用名称空间std中所有的元素。
 
 using namespace std; //放在特定的函数定义中，让该函数能够使用名称空间std中的所有元素
 
 using std::string;
 using std::std; // 在特定的函数使用此，使得特定的函数能够实用制定的元素
 
 std::string // 完全不使用编译指令
 ```
## 第3章 处理数据
* 默认情况下，cout以十进制格式显示整数。
	- dec 指示cout以十进制输出
	- hex 指示cout以十六进制输出
	- oct 指示cout以八进制输出
	
	```C++
	// std空间中 
   cout<<dec
   cout<<hex
   cout<<oct
   ```
* C++ 默认将整型常量存储为int 。除非有说明 有后缀L，太大，
  对于不带后缀的十进制数，使用以下中最小类型
  int ,long 或 long long 
  对于不带后缀的十六进制或八进制整数，是用以下中最小类型
  int , unsigned int ,long ,unsigned long ,long long ,unsigned long long 
* C++11 新增char16\_t,char32\_t
使用前缀u表示char16\_t字符常量和字符串常量 u'C' u"be good"
使用前缀U表示char32\_t常量U'R' U'dirty rat'
* 类型转换
	- 初始化和赋值进行的转换：值将被转换为接受变量的类型
	- 以{}方式初始化时进行的转换：较为严格，列表初始化不允许**缩窄**
	- 表达式中的转换：
		- bool,char,unsigned char ,signed char 和 short值转换为int **整型提升**
		- 运算涉及两种类型时，较小的类型将被转换为较大的类型
		
	- 传递参数时的转换：由C++原型控制
	- 强制类型转换：
	(typename) value
	typename (value)
* **auto** C++11新增加auto,让编译器能够更加初始值的类型推断变量的类型。与C大不同

## 第4章 复合类型
* 输入读取

```C
cin>>  //cin使用空白（空格，制表符和换行符）来确定字符串的结束位置
cin.getline() // 读取一行，直到到达换行符，丢弃换行符
cin.get() // 读取一行，当遇到换行符时结束，将换行符保留在输入序列中
```
* C++98 通过string类扩展了C++库
	string 类具有自动调整大小的功能
	- 可以将一个string对象赋值给另一个string对象
	- 可以添加到另一个对象末尾
* 原始字符串raw，原始字符串中\n不表示换行符

```C++
cout<<R"(Jim "King" Tutt uses "\n" instead of endl.)";

cout<< R"+*("Who wouldn't?)",she whispered.)+*"<<endl;	// 使用“+*( 和 )+*" 替代默认界定符“(和)"
```
* C++允许在声明结构变量时省略关键字struct
* 共用体 union  与C类似，匿名共用体
* 枚举 enum

```C++
enum spectrum {red,orange,yellow,green,blue,violet,indigo,ultraviolet};
 
spectrum band;
band = 4 ;// 非法	在不进行强制类型转换的情况下，只能将定义枚举时使用的枚举量赋给这种枚举的变量 
band = red; // 可以

int color = blue;
color = 3+red; // 可以，枚举量时整型，可被提升为int类型，但int类型不能自动转换为枚举类型
```
枚举的范围：每个枚举都有取值范围，通过强制类型转换可以将取值范围中的任何整数赋给枚举变量。上限：找到大于这个最大值的最小的2的幂减1.下限：不小于0则取0，否则找到小于这个最小值的最小的2的幂加1

* 指针
	使用new来创建动态数组
	
```C++
int *psome = new int [10]; //[] 中可以是使用变量
delet [] psome;
```
    - 不用使用delete来释放不是new分配的内存
    - 不要用delete释放两次
    - 对空指针应用delete是安全的

* 数组：

```C++
short tell[10];
cout<<tell<<endl;
cout<<&tell<<endl;
//从数字上说两个地址相同。但是概念上 tell是是一个两个字节内存块的地址，
//&tell是一个指向包含20个元素的short数组的指针
```
* 模板类:vector和array (C++11)

```C++
#include <vector>
vecotor<int>vi; // vector对象在你插入或添加值时自动调整长度
vecotor<int> vd(5); // 也可以指定长度
```

```C++
#include <array>
array<int,5> ai; //长度固定的数组,可以互相赋值
```


