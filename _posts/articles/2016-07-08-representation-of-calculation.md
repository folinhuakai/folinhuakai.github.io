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


#### 练习7.1 

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

5.bookNo定义在isbn之后，但isbn还是能够使用bookNo。原因是编译器分两步处理类：首先是编译成员声明，然后才编译成员函数体。因此，成员函数体可以随意使用类中的成员
而不用在意其出现的次序。


#### 练习7.3

```c++
#include<iostream>
using namespace std;
#include "sales_data.h"
int main(){
  Sales_data total;
  double price;
  if(cin>>total.bookNo>>total.units_sold>>price){
    total.revenue=price * total.units_sold;
    Sales_data trans;
    while(cin>>trans.bookNo>>trans.units_sold>>price){
      trans.revenue=price * trans.units_sold;
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
    double price;
    is>>item.bookNo>>item.units_sold>>price;
    item.revenue=price * item.units_sold;
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

3.read和print函数接收了IO类型的引用作为参数，IO类为不能拷贝类型，因此可通过引用来传递。


#### 练习7.8

答：read函数会修改Sales_data对象的值，所以其参数定义成普通引用。
相反地，print函数仅仅读取Sales_data对象的值而不涉及写操作，因此应该将其参数定义成常量引用以增加函数的灵活性。


#### 练习7.9

```c++
#include<iostream>
#include<string>
using namespace std;
class Person{
public:
    string name;
    string address;
    string getName() const{return name;}
    string getAddress() const{return address;}
};

istream & read(istream &is,Person &p){
    is>>p.name>>p.address;
    return is;
}

ostream & print(ostream & os,const Person &p){
    os<<p.name<<" "<<p.address;
    return os;
}
```

注意：

1.在c++里非引用类型的参数是传值的过程，初始值被拷贝给变量，但不会改变初始值！！！


#### 练习7.10

答：先执行最里层的read函数，读取第一组数据到data1并返回它的流参数引用。将返回的流参数引用作为外层read函数的第一个参数，
并读取第二组数据到data2，判断返回的流参数引用是否正常。


#### 练习7.11

```c++

#include<iostream>
#include<string>
using namespace std;
class Sales_data{
public:
  Sales_data()=default;
  Sales_data(const string &s,unsigned n,double p):bookNo(s),units_sold(n),revenue(p*n){} 
  Sales_data(const string &s):bookNo(s){}
  Sales_data(istream &);
  string isbn() const{return bookNo;}
  void combine(const Sales_data &);
  string bookNo;
  unsigned units_sold=0;
  double revenue=0.0;
};
void Sales_data::combine(const Sales_data &trans){
    units_sold+=trans.units_sold;
    revenue+=trans.revenue;
}
istream & read(istream &is,Sales_data &item){
   //  copy is not support by IO.
    double price=0.0;
    is>>item.bookNo>>item.units_sold>>price;
    item.revenue=price * item.units_sold;
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
Sales_data::Sales_data(istream &is){
    read(is,*this);
}
```

注意：

1.构造函数与类名相同，没有返回类型。编译器在发现类不包含任何构造函数时会合成一个默认构造函数，但是一旦定义了其他构造函数，除非显示地定义一个默认构造函数否则这个类没有默认构造函数。

2.调用类的默认构造函数可以这样 `Sales_data item`或者`Sales_data item=Sales_data()`,错误的方式会引起编译错误如`Sales_data item()`。

3.在c++11新标准中，如果需要默认的行为，可以在构造函数的参数列表后加上=default来要求编译器生成构造函数。在此例中默认构造函数之所以生效，是因为我们为内置类型的成员提供了初始值（不存在未定义行为），有些编译器不支持类内初始化则默认构造函数需要用到初始化列表。

4.构造函数不能轻易的覆盖掉类内的初始值，除非新赋的值与原值不同。如果不能使用类内初始值，则所有构造函数都应该显示的初始化每个内置类型。

5.`Sales_data(istream &is)`这个构造函数调用了一个类外部的函数接口read，需要注意这个构造函数和read函数出现的次序，类内不需要考虑次序，因为编译器是先编译成员声明，然后才编译成员函数体。


#### 练习7.12

```c++
#include<iostream>
#include<string>
using namespace std;
class Sales_data;
istream & read(istream &is,Sales_data &item);
class Sales_data{
public:
  Sales_data()=default;
  Sales_data(const string &s,unsigned n,double p):bookNo(s),units_sold(n),revenue(p*n){} 
  Sales_data(const string &s):bookNo(s){}
  Sales_data(istream & is){
      read(is,*this);
      /*double price;
      is>>bookNo>>units_sold>>price;
      revenue=units_sold * price;*/
  };
  string isbn() const{return bookNo;}
  void combine(const Sales_data &);
  string bookNo;
  unsigned units_sold=0;
  double revenue=0.0;
};

void Sales_data::combine(const Sales_data &trans){
    units_sold+=trans.units_sold;
    revenue+=trans.revenue;
}

istream & read(istream &is,Sales_data &item){
   //  copy is not support by IO.
    double price=0.0;
    is>>item.bookNo>>item.units_sold>>price;
    item.revenue=price * item.units_sold;
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

注意：

1.将只接受一个istream作为参数的构造函数定义移入类的内部，相信大家已经注意到了该构造函数调用了一个外部接口read函数，关于函数调用次序问题上一题提出过，要想调用read函数需要在类`Sales_data`定义前出现函数声明`istream & read(istream &is,Sales_data &item);`。但与此同时又出现了另一个问题read函数的其中一个参数是`Sales_data`类型，好像进入了死循环。事实上这问题还是可以解决的，就像可以把函数的声明和定义分离开一样，我们也能仅仅声明类而暂时不定义它`class Sales_data;`。

2.还有另一种简单粗暴的方法就是在构造函数体内实现read功能。

```c++
double price;
is>>bookNo>>units_sold>>price;
revenue=units_sold * price;*
```
3.关于类的声明。`class Sales_data；`这种声明有时称为前向声明，它向程序中引入了名字Sales_data并指明Sales_data是类类型.对于类型Sales_data来说，在它声明之后定义之前是一个不完全类型，我们并不清楚它包含哪些成员。


#### 练习7.13

```c++
#include<iostream>
#include "sales_data2.h"
using namespace std;
int main(){
  Sales_data total(cin);
  if(cin){
    Sales_data trans(cin);
    while(cin){
      if(total.isbn()==trans.isbn()){
        total.combine(trans);
      }
      else{
        print(cout,total);
        cout<<endl;
        total=trans;
      }
      trans=Sales_data(cin);
    }
    print(cout,total);
    cout<<endl;
  }
  else{
    cout<<"error!!"<<endl;
  }
  return 0;
}
```

#### 练习7.14

```c++
#include<iostream>
#include<string>
using namespace std;
class Sales_data;
istream & read(istream &is, Sales_data &item);
class Sales_data {
public:
  Sales_data() :bookNo(""), units_sold(0), revenue(0) {};
  Sales_data(const string &s, unsigned n, double p) :bookNo(s), units_sold(n), revenue(p*n) {}
  Sales_data(const string &s) :bookNo(s) {}
  Sales_data(istream & is) {
    read(is, *this);    
  }
  void combine(const Sales_data &);
  string isbn() const { return bookNo; }
  string bookNo;
  unsigned units_sold = 0;
  double revenue = 0.0;
};

void Sales_data::combine(const Sales_data &trans) {
  units_sold += trans.units_sold;
  revenue += trans.revenue;
}

istream & read(istream &is, Sales_data &item) {
  //  copy is not support by IO.
  double price = 0.0;
  is >> item.bookNo >> item.units_sold >> price;
  item.revenue = price * item.units_sold;
  return is;
}
ostream & print(ostream & os, const Sales_data &item) {
  os << item.bookNo << " " << item.units_sold << " " << item.revenue;
  return os;
}

Sales_data add(const Sales_data &lhs, const Sales_data &rhs) {
  Sales_data sum;
  sum = lhs;
  sum.combine(rhs);
  return sum;
}
```


#### 练习7.15

```c++
#include<iostream>
#include<string>
using namespace std;
class Person;
istream & read(istream &, Person &);
class Person {
public:
  Person() = default;
  Person(const string &na, const string &ad) :name(na), address(ad) {};
  Person(istream &is);
  string getName() const { return name; }
  string getAddress() const { return address; }
  string name;
  string address; 
};
Person::Person(istream &is) {
  read(is,*this);
}

istream & read(istream &is, Person &p) {
  is >> p.name >> p.address;
  return is;
}

ostream & print(ostream & os, const Person &p) {
  os << p.name << " " << p.address;
  return os;
}
```

#### 练习7.16

答：在类的定义中对于访问说明符出现的位置和次数都没有限定。构造函数和部分成员函数做为接口的一部分被定义在public说明符之后，数据成员和实现的函数被定义在private说明符后。


#### 练习7.17

答：关键字class和struct仅仅在形式上有所不同，实际上我们可以用这两个关键字中的任何一个定义类，唯一的区别是它们的默认访问权限不同。如果使用struct关键字，则定义在第一个访问说明符之前的成员是public；相反，如果使用的是class，则这些成员是private。


#### 练习7.18

答：封装就是用户不能直达对象内部并且控制它的实现细节。封装有两个重要的优点：一、确保用户代码不会无意间破坏封装对象的状态；二、被封装的类的具体实现细节可以随时改变，而无须调整用户级别的代码。


#### 练习7.19

```c++
#include<iostream>
#include<string>
using namespace std;
class Person;
istream & read(istream &, Person &);
class Person {
public:
  Person() = default;
  Person(const string &na, const string &ad) :name(na), address(ad) {};
  Person(istream &is);
  const string  & getName() const {     
    return name;
  }
  const string & getAddress() const { 
    return address;
  }
  void setName(string &s) { name = s; }
  void setAddress(string &ad) { address = ad; }
private:
  string name;
  string address; 
};
Person::Person(istream &is) {
  read(is,*this);
}

istream & read(istream &is, Person &p) {
  string name, address;
  is >> name >> address;
  p.setName(name);
  p.setAddress(address);
  return is;
}

ostream & print(ostream & os, const Person &p) {
  os << p.getName() << " " << p.getAddress();
  return os;
}
```

注意：1.数据成员声明成private，原因有：

一、能够提供语法一致性，写代码的时候能够马上区分要不要加函数括号；

二、提供更好的访问控制，只读、只写还是可读可写，都容易控制；

三、封装，减少与外界接触的机会，方便修改和维护。

2.构造函数和部分成员函数被声明为public。

#### 练习7.20

答：友元在其他类或非成员函数访问非公有数据成员时使用。友元一定程度上破坏了类的封装性，但增加了灵活性。


#### 练习7.21

```c++
#include<iostream>
#include<string>
using namespace std;
class Sales_data;
istream & read(istream &is, Sales_data &item);
ostream & print(ostream & os, const Sales_data &item);
class Sales_data {
public:
  friend istream & read(istream &is, Sales_data &item);
  friend ostream & print(ostream & os, const Sales_data &item);
  Sales_data() :bookNo(""), units_sold(0), revenue(0) {};
  Sales_data(const string &s, unsigned n, double p) :bookNo(s), units_sold(n), revenue(p*n) {}
  Sales_data(const string &s) :bookNo(s) {}
  Sales_data(istream & is) {
    read(is, *this);    
  } 
  void combine(const Sales_data &);
  string isbn() const { return bookNo; }
private:
  string bookNo;
  unsigned units_sold = 0;
  double revenue = 0.0;
};

void Sales_data::combine(const Sales_data &trans) {
  units_sold += trans.units_sold;
  revenue += trans.revenue;
}

istream & read(istream &is, Sales_data &item) {
  //  copy is not support by IO.
  double price = 0.0;
  is >> item.bookNo >> item.units_sold >> price;
  item.revenue = price * item.units_sold;
  return is;
}
ostream & print(ostream & os, const Sales_data &item) {
  os << item.bookNo << " " << item.units_sold << " " << item.revenue;
  return os;
}

Sales_data add(const Sales_data &lhs, const Sales_data &rhs) {
  Sales_data sum;
  sum = lhs;
  sum.combine(rhs);
  return sum;
}
```

注意：

1.read和print函数是类的接口但不属于类的成员，所以在ales_data的数据成员声明为private后这两个函数将无法通过编译，为此可将这两个函数声明为类的友元。

2.一般来说，最后在类定义的开始或者结束前的位置集中声明友元。

3.友元的声明仅仅指定了访问权限，而非一个通常意义上的声明。如果希望类的用户调用某个友元函数，那么必须在友元声明之外再专门对函数进行一次声明。

#### 练习7.22

答：代码与练习7.19相同。

注意：

1.代码中并没有把read和print声明为类的友元，而是通过调用类自身提供的读写接口来完成相应的功能。

#### 练习7.23-24

```c++
#include<iostream>
#include<vector>
using namespace std;
class Screen {
public:
  typedef string::size_type pos;
  Screen() = default;
  Screen(pos h, pos w) :height(h), width(w), contents(h*w, ' ') {}
  Screen(pos h, pos w, char c) :height(h), width(w), contents(h*w, c) {}  
  char get()const{
    return contents[cursor];
  }
private:
  pos cursor=0;
  pos height = 0, width = 0;
  string contents;
};
```

注意：

1.类可以定义某种类型在类中的别名，由类定义的类型名和其他成员一样存在访问控制。`typedef string::size_type pos;`在public中定义了定义了pos，这样用户就可以使用这个名字。

2.Screen的用户不应该知道Screen使用了一个string对象来存放它的数据，因此通过把pos定义成public成员可以隐藏Screen的实现细节。


#### 练习7.25

答：可以依赖，Screen的数据成员为基本数据类型和string类型都可以在拷贝和赋值操作中正常工作。

#### 练习7.26

```c++
inline double Sales_data::avg_price()const {
  return units_sold ? revenue / units_sold : 0;
}
```

注意：

1.我们可以在类内把inline作为声明的一部分显式地声明成员函数，同样的也能在类的外部用inline关键字修饰函数的定义，虽然我们无须在声明和定义的地方同时说明inline，但这么做其实是合法的，最好也这么做可以使类更容易理解。

2.内联说明只是像编译器发出一个请求，编译器可以选择忽略这个请求。

#### 练习7.27

```c++
public:
  Screen & move(pos row, pos col) { cursor = row*width + col; return *this; }
  Screen & set(char c) { contents[cursor] = c; return *this; }
  Screen & set(pos row, pos col, char c) { contents[row*width + col] = c; return *this; }
  Screen & display(ostream & os) { do_display(os); return *this; }
  const Screen & display(ostream & os)const { do_display(os); return *this; }
private:
  void do_display(ostream & os)const {
    os << contents;
  }
  //其他部分和之前版本相同
```

注意：

1.返回引用的函数是左值，意味着这些函数返回的是对象本身而非对象的副本。`myScreen.move(4,0).set('#').display(cout);`这个表达式的操作作用在同一对象上。

2.基于const的重载。通过区分成员函数是否是const的，我们可以对其进行重载，因为非常量版本的函数对于常量对象是不可用的，所以只能在常量对象上调用const函数。另一方面，虽然非常量对象上调用常量版本和非常量版本，但显然非常量版本是一个更好的匹配。

3.建议对于公共代码使用私有功能函数。为什么要定义`do_display`函数呢，原因有四个：

一、一个基本的愿望是避免在多处使用同样的代码。

二、我们预期随着类的规模的发展，display函数有可能变得更加复杂，此时，把相应的操作写在一处是较好的选择。

三、可能在开发过程中给do_display函数添加一些调试信息，而这些信息代码最终是要去掉的，显然只删除一处会方便得多。

四、我们在类内定义了do_display函数，这个额外的函数调用不会增加任何开销。

#### 练习 7.28-29

答：如果返回的是Screen而非Screen &，对返回值进行拷贝相当于如下代码

```c++
Screen temp=myScreen.move(4,0);//对返回值进行拷贝
temp.set('#');//不会改变myScreen的contents
```

运行结果：

```c++
//with '&'
XXXXXXXXXXXXXXXXXXXX#XXXX
XXXXXXXXXXXXXXXXXXXX#XXXX

// without '&'
XXXXXXXXXXXXXXXXXXXX#XXXX
XXXXXXXXXXXXXXXXXXXXXXXXX
```

#### 练习7.30

答：优点是使程序更加明确减少误读范围，可以使得成员函数的形参与数据成员同名。缺点是重复、多余。


#### 练习7.31

```c++
class Y;
class X {
  Y *y;
};

class Y {
  X x;
};
```

注意：

1.对于一个类来说，我们在创建它的对象前该类必须被定义而不能仅仅被声明。否则，编译器无法确定这样的对象需要多少存储空间。

2.类类型在它声明之后定义之前是一个不完全类型，不完全类型只能在有限的情况下使用：可以定义指向这种类型的指针或引用，也可以声明以不完全类型为参数或返回类型的函数。

#### 练习7.31

```c++
class Window_mgr {
public:
  using screenIndex = vector<Screen>::size_type;
  void clear(screenIndex);
  Window_mgr(Screen & scr);
  Window_mgr & add(Screen &scr);
private:
  vector<Screen> screens;
};
Window_mgr & Window_mgr::add(Screen &scr) {
  screens.push_back(scr);
  return *this;
}
Window_mgr::Window_mgr(Screen & scr) {
  screens.push_back(scr);
}

class Screen {
public:
  friend void Window_mgr::clear(screenIndex);
  friend ostream& print(ostream &os, Screen &item);
  typedef string::size_type pos;
  Screen() = default;
  Screen(pos h, pos w) :height(h), width(w), contents(h*w, ' ') {}
  Screen(pos h, pos w, char c) :height(h), width(w), contents(h*w, c) {}
  Screen(istream &is);
  char  get()const {return contents[cursor];}
  Screen  move(pos row, pos col) { cursor = row*width + col; return *this; }
  Screen  set(char c) { contents[cursor] = c; return *this; }
  Screen & set(pos row, pos col, char c) { contents[row*width + col] = c; return *this; }
  Screen  display(ostream & os) { do_display(os); return *this; }
  const Screen & display(ostream & os)const { do_display(os); return *this; }
  
private:
  void do_display(ostream & os)const {
    os << contents;
  }
  pos cursor=0;
  pos height = 0, width = 0;
  string contents;
};

void Window_mgr::clear(screenIndex i) {
  Screen &s = screens[i];
  s.contents = string(s.height*s.width, ' ');
}
```

注意：

1.先定义Window_mgr类，其中声明clear函数但不能定义它。在使用Screen的成员前先声明Screen，此时不能定义clear函数是因为其还不是Screen的友元。

2.在此题中需要特别注意类、函数的声明和定义的顺序。

#### 练习7.33

```c++
Screen::pos Screen::size()const {
  return height*width;
}
```

注意：（作用域问题）

1.每个类都会有自己的作用域。在类的作用域之外，普通数据和函数成员只能有对象、引用或指针使用成员访问符来访问。对于类的类型成员，如本题中的`pos`则使用作用域运算符访问。

2.在类的外部成员名是被隐藏起来的，在类外定义成员函数时必须提供类名和成员函数名，一旦遇到了类名定义剩余的部分就在类的作用域之内了。

3.本题出现错误的原因是返回类型名在类名出现之前，事实上它是位于Screen类的作用域之外的。想要用pos作为返回类型就必须指定哪个类定义了它。

#### 练习7.34 

答：出现未定义标识符pos错误，编译不能通过。类的两阶段处理方式只适用于成员函数中使用的名字，声明中使用的名字，包括返回类型或者参数列表中使用的名字，都必须在使用前确保可见。

#### 练习7.35

答：在`Exercise`类的开始处定义了类型`double`的别名`Type`,所以在类的作用域中使用到的`Type`类型都是`double`而不是类外定义的`string`类型。但有个问题是在类外定义成员函数时，返回类型出现在类名之前并不在类的作用域内，所以会与全局作用域中的`string`类型匹配，因此出现了`setVal`成员函数与声明不匹配的错误。正确代码如下：
```c++
typedef string Type;
Type initVal();
class Exercise{
public:
  typedef double Type;
  Type setVal(Type);
  Type initVal();
private:
  int val;
};

Exercise::Type Exercise::setVal(Type parm){
  val =parm +initVal();
  return val;
}
```

#### 练习7.36

答：正确代码如下：

```c++
struct X{
  X(int i,int j):rem(i%j),base(i){}
  int rem,base;
}
```

注意：

1.成员初始化的顺序与它在类定义中出现的顺序一致，本题中先初始化了`rem`然后再初始化`base`。

2.最好令构造函数初始化顺序与成员声明顺序保持一致。而且，如果可能的话尽量避免使用某些成员初始化其它成员。

3.当我们定义变量时习惯于立即对其进行初始化，而不是先定义再赋值。就对象的数据成员而言，初始化和赋值也有类似的区别。如果在构造函数的初始值列表中没有显示地初始化成员，则该成员将在构造函数体之前执行默认初始化。构造函数的初始值有时是必不可少的，如成员是引用、const或没有默认构造函数的类类型。

4.在很少多类中，初始化和赋值的区别事关底层的效率问题：前者直接初始化数据成员，后者先初始化后赋值。建议使用构造函数初始值。

#### 练习7.37

```c++
class Sales_data {
public:
  Sales_data(string s="") :bookNo(""){}; -->1
  Sales_data(const string &s, unsigned n, double p) :bookNo(s), units_sold(n), revenue(p*n) {}  -->2
  Sales_data(istream & is) { read(is, *this); }  -->3
  //其它与之前版本一致
private:
  string bookNo;
  unsigned units_sold = 0;
  double revenue = 0.0;
};
```
1.`Sales_data first_item(cin)`使用了第三个构造函数，数据成员值根据输入确定。

2.`Sales_data next`使用了第一个构造函数，数据成员值按顺序为：空字符串，0，0.0。

3.`Sales_data last("9-999-9999-9");`使用了第一个构造函数，数据成员值按顺序为："9-999-9999-9"，0，0.0。

#### 练习7.38

```c++
Sales_data(istream & is=cin) {
    read(is, *this);    
}
```

#### 练习7.39

答：这种行为是不合法的。在定义`Sales_data item;`对象时，有两个函数可以匹配，此时发生错误称为二义性调用。

#### 练习40

```c++
class Emplopee {
public:
  Emplopee()=default;
  Emplopee(unsigned i, string n, string po, string ph, string ad) :sno(i), name(n), post(po), phone(ph), address(ad) {}
  Emplopee(istream &is) {
    cin >> sno >> name >> post >> phone >> address;
  }
private:
  unsigned sno=0;
  string name;
  string post;
  string phone;
  string address;
};
```
在雇员类中提供了三个构造函数。数据成员应该包括雇员编号（不能为空也不能重复，最好能自动生成），雇员的姓名、职位、联系方式、地址等。





