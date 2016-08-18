---
layout: post
title: 第七章 类 （C++ Primer 5th ）
excerpt: "C++ Primer 5th学习笔记（含课后练习答案）。"
date: 2016-08-11
modified:
categories: articles
author: flhk
tags:
  - C++
  - 类
comments: true
share: true
---
从最基本的层面理解，数据结构是把一组相关的数据元素组织起来然后使用它们的策略和方法，在C++语言中允许用户以类的形式自定义数据类型。类的基本思想是**数据抽象**和**封装**，数据抽象的实现依赖于接口和实现分离的编程技术（如封装），接口包括用户所能执行的操作，类的实现包括类的数据成员、私有函数及接口函数。

## 7.1 定义抽象数据类型

抽象数据类型：

 1.首先，类的用户不能直接访问类的数据成员（即封装）。

 2.其次，定义一些操作供类用户使用。


### 练习7.1 

sales_data.h：


```c++
#include<iostream>
#include<string>
using namespace std;
class Sales_data{
public:
  string bookNo;
  unsigned units_sold=0;
  double revenue=0.0;
};
```
7.1.cpp:


```c++
#include<iostream>
#include<string>
#include "sales_data.h"
using namespace std;
int main(){
  Sales_data total;
  if(cin>>total.bookNo>>total.units_sold>>total.revenue){
    Sales_data trans;
    while(cin>>trans.bookNo>>trans.units_sold>>trans.revenue){
      if(total.bookNo == trans.bookNo){
        total.units_sold += trans.units_sold;
        total.revenue += trans.revenue;
      }
      else{
        cout<<total.bookNo<<" "<<total.units_sold<<" "<<total.revenue<<endl;
        total=trans;
      }
    } 
    cout<<total.bookNo<<total.units_sold<<total.revenue<<endl;
  }
  else{
    cout<<"input data!"<<endl;
  }    
}
```

注意：

1.类以struct或class关键字开始，这两个关键字唯一的区别是默认的访问权限不太一样，紧跟着类名和类体（类体可以为空）。<br\>
2.类体右侧表示结束的花括号后面必须写一个分号，分号表明声明符的结束。<br\>
3.`total=trans`使用了默认构造函数，即对对象的每个成员进行赋值。<br\>


