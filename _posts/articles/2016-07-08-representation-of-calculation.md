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
    cout<<total.bookNo<<" "<<total.units_sold<<" "<<total.revenue<<endl;
  }
  else{
    cout<<"input data!"<<endl;
  }    
}
```

注意：

1.类以struct或class关键字开始，这两个关键字唯一的区别是默认的访问权限不太一样，紧跟着类名和类体（类体可以为空）。

2.类体右侧表示结束的花括号后面必须写一个分号，分号表明声明符的结束。

3.`total=trans`使用了默认构造函数，即对对象的每个成员进行赋值。

#### 练习7.2

```c++
#include<iostream>
#include<string>
using namespace std;
class Sales_data{
public:
  string isbn() const{return bookNo;}
  void combine(const Sales_data &);
  string bookNo;
  unsigned units_sold = 0;
  double revenue = 0.0;
};
void Sales_data::combine(const Sales_data &trans){
    units_sold += trans.units_sold;
    revenue += trans.revenue;
}
```
注意:

1.类成员函数的声明必须在类的内部，它的定义既可以在类的内部也可以在类的外部。

2.定义在类的内部的成员函数是隐式的`inline`函数。

3.函数名`Sales_data::combine`使用了作用域运算符来说明这样一个事实：我们定义了一个名为combine的函数，并且该函数被定义在类Sales_data
作用域内。当编译器看到这个函数名，就能理解剩余的代码是位于类作用域内的。因此，当使用units_sold和revenue时，实际上隐式地使用了Sales_data的成员。

4.引入const成员函数。isbn函数的参数列表之后紧跟着const关键字，这里const的作用是修改隐式this指针的类型。

#### 练习7.3

```c++
#include<iostream>
using namespace std;
#include "sales_data.h"
int main(){
  Sales_data total;
  if(cin>>total.bookNo>>total.units_sold>>total.revenue){
    Sales_data trans;
    while(read(cin,trans)){
      if(total.bookNo==trans.bookNo){
        total.combine(trans);
      }
      else{
        cout<<total.bookNo<<" "<<total.units_sold<<" "<<total.revenue<<endl;
        total=trans;
      }
    }
     cout<<total.bookNo<<" "<<total.units_sold<<" "<<total.revenue<<endl;
  }
  else{
    cerr<<"No Number!"<<endl;
  }
  return 0;
}
```

#### 练习7.4-7.5
person.h:

```c++
class Person{
public:
    string name;
    string address;
    string getName() const{return name;}
    string getAddress() const{return address;}
};
```
注意：

1.默认情况下，this的类型是指向类类型非常量版本的常量指针。

2.尽管this是隐式的但它仍然要遵守初始化规则，即不能把this绑定到常量对象上，那么常量对象也不能调用一般成员函数。
所以在不改变对象的情况下使用const成员函数有助于提高函数的灵活性。


#### 练习7.6-7.7

```c++
istream & read(istream &is,Sales_data &item){
   //  copy is not support by IO.
    is>>item.bookNo>>item.units_sold>>item.revenue;
    return is;
}
ostream & print(ostream & os,const Sales_data &item){
    os<<item.bookNo<<" "<<item.units_sold<<" "<<item.revenue;
    return os;
}

Sales_data add(const Sales_data &lhs,const Sales_data &rhs ){
    Sales_data sum;
    sum=lhs;
    sum.combine(rhs);
    return sum;
}
```

```c++
#include<iostream>
using namespace std;
#include "sales_data2.h"
int main(){
    Sales_data total;
    if(read(cin,total)){
  Sales_data trans;
  while(read(cin,trans)){
      if(total.bookNo==trans.bookNo){
    total=add(total,trans);
    //total.combine(trans);
      }
      else{
    print(cout,total)<<endl;
    total=trans;
      }
  }
  print(cout,total)<<endl;;
    }
    else{
  cerr<<"No Number!"<<endl;
    }
    return 0;
}
```

注意：

1.作为接口组成部分的非成员函数，如add、read等，它们的定义和声明都在类的外部。

2.一般来说，如果非成员函数是类的接口组成部分，则它一般与类的声明定义在同一文件内。

4.read和print函数接收了IO类型的引用作为参数，IO类为不能拷贝类型，因此可通过引用来传递。