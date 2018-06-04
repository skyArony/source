---
title: 快查-C++
tags:
  - C++
categories:
  - 快查
date: 2018-04-09 16:15:03
thumbnail:
---

为了刷题。

```c++
#include <iostream>
using namespace std;
int main(){
    cout << "Hello World!" << endl;
    return 0;
}
```



# 一、语法

支持面向对象开发的四大特性：封装、抽象、继承、多态。



## 1. 标准库

- C++ 标准库：提供大量常用函数
- 标准模板库（STL）：提供大量方法，用于操作数据结构




## 2. 数据类型

仅列出比 C 语言多的。

| 类型   | 关键字                         |
| ---- | --------------------------- |
| 布尔型  | bool                        |
| 宽字符型 | wchar_t（2~4字节）              |
| 字符串型 | string（#include \<string\>） |

> wechar_t 是为了国际化，因为 char 的长度只有 1 字节，存储汉字等字符时会乱码。

### string

C++ 新增的更便于操作的字符串类型。

```c++
#include <iostream>
#include <string>
 
using namespace std;
 
int main ()
{
   string str1 = "Hello";
   string str2 = "World";
   string str3;
   string name[5]; 	// 一个容量为 5 的字符串数组
   int  len ;
 
   // 复制 str1 到 str3
   str3 = str1;
   cout << "str3 : " << str3 << endl;
 
   // 连接 str1 和 str2
   str3 = str1 + str2;
   cout << "str1 + str2 : " << str3 << endl;
 
   // 连接后，str3 的总长度
   len = str3.size();	// str3.length() 一样的效果
   cout << "str3.size() :  " << len << endl;	// 10
 	
   // 可通过下标的方式及其简单的遍历
   for (int i = 0;i < len; i++) cout << str3[i] << endl;
    
   return 0;
}
```

> 通过此例可以看出，C++ 下的 string 数据类型出奇的方便：
>
> - 用 = 赋值
> - 用 + 连接
> - 用 size() / length() 求长度，C++ 中的 string 不会包含 ‘\0’
> - 可通过下标的方式及其简单的遍历

string 类型还提供很多针对字符串的操作

```c++
#include <iostream>
#include <string>
using namespace std;

int main()
{
    // 定义一个string类对象，下面都是假设 http 每一次操作后又恢复原状
    string http = "blog.yfree.cc";

   	// 打印字符串长度
   	cout<<http.length()<<endl;

    // 拼接
    http.append("/C++");	// blog.yfree.cc/C++

    // 插入
    http.insert(2，"--"); // bl--og.yfree.cc 
    
    // 删除字符
    cout << http.erase (2, 3); << endl;  // 删除从位置 2 开始的 3 个字符 blyfree.cc  
    
    // 替换
    http.replace(0,4,"md");		// 位置，长度，要替换的串 md.yfree.cc
    
    // 删除最后一个字符
    http.pop_back();		// blog.yfree.c
    
    // 查找，返回第一次找到的位置
    http.find("blog", 0); 	// 第二个参数可以省略，默认为 0，也可以字符 'b'
    
    // 查找，返回最后一次找到的位置
    http.rfind("blog", 0);
    
    // 其他查找
    find_first_of(".");		//从头开始寻找字符'.'的位置
    find_last_of(".");		//从尾开始寻找字符'.'的位置
    find_first_not_of(".");	// 从头开始找不是字符'.'的位置
    find_last_not_of(".");  // 从尾开始找不是字符'.'的位置
    
    // 找子串 yfree
    int first = http.find_first_of("."); 
    int last = http.find_last_of("."); 
    // 子串截取
    cout<<http.substr(first+1, last-first-1)<<endl; //提取"yfree"子串并打印

    // 比较字符串
    string str1="hi,test,hello";  
    string str2="hi,test";
    
    // 第一种方式
    if(str1.compare(str2)>0)  
        printf("str1>str2\n");  
    else if(str1.compare(str2)<0)  
        printf("str1<str2\n");  
    else  
        printf("str1==str2\n");  
      
    // str1的子串（从索引3开始，包含4个字符）与str2进行比较  
    if(str1.compare(3,4,str2)==0)  
        printf("str1的指定子串等于str2\n");  
    else  
        printf("str1的指定子串不等于str2\n");  
      
    // str1指定子串与str2的指定子串进行比较  
    if(str1.compare(3,4,str2,3,4)==0)  
        printf("str1的指定子串等于str2的指定子串\n");  
    else  
        printf("str1的指定子串不等于str2的指定子串\n");  
      
    // str1指定子串与字符串的前n个字符进行比较  
    if(str1.compare(0,2,"hi,hello",2)==0)  
        printf("str1的指定子串等于指定字符串的前2个字符组成的子串\n");  
    else  
        printf("str1的指定子串不等于指定字符串的前2个字符组成的子串\n");  
    
    // 类型转换
    std::stoi 	   		// 将字符串转化成带符号（Signed）整数
	std::stol 			// 将字符串转化成带符号整数
	std::stoll 			// 将字符串转化成带符号整数
	std::stoul 			// 将字符串转化成无符号（Unsigned）整数
	std::stoull 		// 将字符串转化成无符号整数
	std::stof 			// 将字符串转化成浮点数
	std::stod 			// 将字符串转化成浮点数
	std::stold 			// 将字符串转化成浮点数
	std::to_string 		// 将一个整数或浮点数转化成字符串
	std::to_wstring 	// 将一个整数或浮点数转化成宽字符串
    
    return 0;
}
```

> - append() -- 在字符串的末尾添加字符
> - insert() -- 插入字符
> - erase() -- 删除
> - replace() -- 替换字符串
> - find() -- 在字符串中查找字符串
> - substr() -- 返回某个子字符串
> - compare() -- 比较
>
>
> - empty() -- 检验是否为空 -- 返回 0 / 1 
> - pop_back() -- 删除最后一个字符
> - back() -- 返回最后一个元素 -- front() -- 返回第一个元素

### 结构体

```c++
struct ListNode {
  int val;
  ListNode *next;
  ListNode(int x) : val(x), next(NULL) {}	// 这是结构体的构造函数
};
```

### nullptr

C++11 中的标准，代表空指针，所以后表示空指针就不要使用 NULL 了，因为 NULL 会被编译器翻译成 0。



## 3. 输入 & 输出

### cin 和 cout

```c++
// 输入
cin >> a;

// 输出
cout << "我的名字是" << "Arony" << endl;  // << 用于向屏幕打印多个值，加上 endl 表示换行
```

> \n 和 endl 的区别: `std::endl` 不仅换行还会立即刷新缓冲区。



## 4. 引用

相当于别名，和指针很相似，但是也有不同之处。

- 不存在空引用。引用必须连接到一块合法的内存。
- 一旦引用被初始化为一个对象，就不能被指向到另一个对象。指针可以在任何时候指向到另一个对象。
- 引用必须在创建时被初始化。指针可以在任何时间被初始化。

```c++
// 创建引用
int i = 17;
int& r = i;

void swap(int& x, int& y)； // 把引用当做参数

int& func(int x) {
   int arr[2] = {0,1};
   return arr[x];     // 返回 arr[x] 的引用
}    
```



## 5. 函数

与C语言的不同之处：

### 支持设置默认参数

```c++
int sum(int a, int b=20)
{
  int result;
 
  result = a + b;
  
  return (result);
}
```

### Lambada 函数

匿名函数，也叫 Lambada 表达式，可以像对象一样使用，可以赋给变量和作为参数传递。

```c++
[capture](parameters) mutable ->return-type{body}	// mutable 取消传入捕获的常量性

// 典型样例
[](int x, int y){ return x < y ; }

// 没有返回值的样例
[]{ ++global_x; } 

// 明确指定返回类型
[](int x, int y) -> int { int z = x + y; return z + x; }

// 最简单的 lambda 函数， parameters 和 返回值 在没有填入时可以连同符号一起省略
[]{};

// auto 可以在这这么用
auto closure = [](const int&, const int&) {}
```

### 闭包

可以在 Lambda 表达式中访问当前作用域的变量，这是 Lambda 表达式的闭包行为。在 C++ 中，变量的有传值和传引用的区别，可以通过前面的 [] 来指定：

```c++
[]      // 沒有定义任何变量。使用未定义变量会引发错误。
[x, &y] // x以传值方式传入（默认），y以引用方式传入。
[&]     // 任何被使用到的外部变量都隐式地以引用方式加以引用。
[=]     // 任何被使用到的外部变量都隐式地以传值方式加以引用。
[&, x]  // x显式地以传值方式加以引用。其余变量以引用方式加以引用。
[=, &z] // z显式地以引用方式加以引用。其余变量以传值方式加以引用。

// this 指针显式传入
[this]() { this->someFunc(); }();

// this 使用样例
void hello() {
    cout << "test hello!";
};

auto fun = [this]{ // 捕获了 this 指针
    this->hello(); // 这里 this 调用的就是 class test 的对象了
};
fun();
```



## 6. 类 & 面向对象

### 类

```c++
void set(int n, int m){
    ;
}

class Box
{
    public:
      double length;   // 盒子的长度
      double breadth;  // 盒子的宽度
      double height;   // 盒子的高度
   	
    // 成员函数的定义
    double getVolume(void)
    {
	  return length * breadth * height;
    }
    // 在类的内部先声明，然后在外部利用 :: 定义
    void setLength( double len );
};

// 使用 :: 在类的外部定义成员函数
double Box::getVolume(void)
{
    ::set(n, m); 	// 直接用 :: 表示调用全局函数或数据
    return length * breadth * height;
}

Box box1;
Box box2；

box1.length = 10.1；
```

> 类和结构体的区别：
>
> - class中默认的成员访问权限是private的，而struct中则是public的。　　
> - 从class继承默认是private继承，而从struct继承默认是public继承。

> :: 可以不跟类名，表示全局数据或全局函数（即非成员函数）。

### 构造函数和析构函数

```c++
class Line
{
   public:
      void setLength( double len );
      double getLength( void );
      Line();   // 这是构造函数声明
      ~Line();  // 这是析构函数声明
 
   private:
      double length;
};
 
// 成员函数定义，包括构造函数
Line::Line(void)
{
    cout << "Object is being created" << endl;
}
Line::~Line(void)
{
    cout << "Object is being deleted" << endl;
}
```

### 拷贝构造函数

类的对象需要拷贝时，拷贝构造函数将被调用。如以下三种情况：

- 一个对象以值传递的方式传入函数体
- 一个对象以值传递的方式从函数返回
- 一个对象需要通过另外一个对象进行初始化

```c++
A x(2);　　//直接初始化，调用构造函数
A y = x;　　//拷贝初始化，调用拷贝构造函数
```

```c++
class Line
{
   public:
      int getLength( void );
      Line( int len );             // 简单的构造函数
      Line( const Line &obj);      // 拷贝构造函数
      ~Line();                     // 析构函数
 
   private:
      int *ptr;
};
 
// 成员函数定义，包括构造函数
Line::Line(int len)
{
    cout << "调用构造函数" << endl;
    // 为指针分配内存
    ptr = new int;
    *ptr = len;
}
 
Line::Line(const Line &obj)
{
    cout << "调用拷贝构造函数并为指针 ptr 分配内存" << endl;
    ptr = new int;
    *ptr = *obj.ptr; // 拷贝值
}
 
Line::~Line(void)
{
    cout << "释放内存" << endl;
    delete ptr;
}
```

### 友元函数

声明在类的内部，但是定义在类的外部，有权调用类的 private 和 protected 成员。

```c++
class Box {
  public:
    friend void printWidth( Box box );
};

// 请注意：printWidth() 不是任何类的成员函数
void printWidth( Box box ) {
   /* 因为 printWidth() 是 Box 的友元，它可以直接访问该类的任何成员 */
   cout << "Width of box : " << box.width <<endl;
}
```

> 1. 用关键字 friend 来修饰友元函数，但该函数并不是类的成员函数，其声明可以放在类的私有部分，也可放在共有部分。友元函数的定义在类体外实现，不需要加类限定。
> 2. 一个类中的成员函数可以是另外一个类的友元函数，而且一个函数可以是多个类友元函数。
> 3. 友元函数可以访问类中的私有成员和其他数据，但是访问不可直接使用数据成员，需要通过对对象进行引用。
> 4. 友元函数在调用上同一般函数一样，不必通过对对象进行引用。

### 继承类

子类在 C++ 中叫做派生类

```c++
// 基类
class Shape 
{
   public:
      int width;
      int height;
};
 
// 派生类
class Rectangle: public Shape		// public 在这里表示继承类型
{
   public:
      int getArea(){ 
         return (width * height); 
      }
};
```

**继承类型：**

- 公有继承（public）：当一个类派生自**公有**基类时，基类的**公有**成员也是派生类的**公有**成员，基类的**保护**成员也是派生类的**保护**成员，基类的**私有**成员不能直接被派生类访问，但是可以通过调用基类的**公有**和**保护**成员来访问。
- 保护继承（protected）： 当一个类派生自**保护**基类时，基类的**公有**和**保护**成员将成为派生类的**保护**成员。
- 私有继承（private）：当一个类派生自**私有**基类时，基类的**公有**和**保护**成员将成为派生类的**私有**成员。

> 一般用 public 继承类型，其他两个比较少用到

### 多继承和虚继承

```c++
// 多继承
class Rectangle: public Shape, public PaintCost {};

// 虚继承
class D{......};
class B: public D{......};
class A: public D{......};
class C: public B, public A{.....};		// 这个会使 D 创建两个对象，要解决这个问题要用虚拟继承格式

class D{......};
class B: virtual public D{......};
class A: virtual public D{......};
class C: public B, public A{.....};
```

### 重载运算符和重载函数

编译器通过把所使用的参数类型与定义中的参数类型进行比较，决定选用最合适的定义。

#### 函数重载

比如很多语言定义好的函数，在输入参数不同时会有不同的效果。

```c++
class printData 
{
   public:
      void print(int i) {
        cout << "整数为: " << i << endl;
      }
      void print(double  f) {
        cout << "浮点数为: " << f << endl;
      }
};
 
int main(void)
{
   printData pd;
   // 输出整数
   pd.print(5);
   // 输出浮点数
   pd.print(500.263);
 
   return 0;
}
```

#### 运算符重载

运算符的重载能够让你定义你自己的运算符计算，如把 + 重载一个适用于对象之间的 +。

```c++
class Box
{
   public:
      // 重载 + 运算符，用于把两个 Box 对象相加
      Box operator+(const Box& b)
      {
         Box box;
         box.length = this->length + b.length;
         box.breadth = this->breadth + b.breadth;
         box.height = this->height + b.height;
         return box;
      }
};
// 程序的主函数
int main( )
{
   Box Box1;                // 声明 Box1，类型为 Box
   Box Box2;                // 声明 Box2，类型为 Box
   Box Box3;                // 声明 Box3，类型为 Box
 
   // 把两个对象相加，得到 Box3
   Box3 = Box1 + Box2;
}
```

> 还有很多其他运算符的样例，看[这里](http://www.runoob.com/cplusplus/cpp-overloading.html)



## 7. 多态

调用成员函数时，根据调用函数的对象的类型来执行不同的函数。多态需要结合**虚函数**才能正常使用。**动态链接**

```c++
class Shape {
   protected:
      int width, height;
   public:
      Shape( int a=0, int b=0) {
         width = a;
         height = b;
      }
      virtual int area() {                       // 需要加上虚函数的声明
         cout << "Parent class area :" <<endl;
         return 0;
      }
};
class Rectangle: public Shape{
   public:
      Rectangle( int a=0, int b=0):Shape(a, b) { }		// 继承基类的构造函数并传入默认值
      virtual int area () { 
         cout << "Rectangle class area :" <<endl;
         return (width * height); 
      }
};
class Triangle: public Shape{
   public:
      Triangle( int a=0, int b=0):Shape(a, b) { }
      virtual int area () { 
         cout << "Triangle class area :" <<endl;
         return (width * height / 2); 
      }
};
// 程序的主函数
int main( ) {
   Shape *shape;
   Rectangle rec(10,7);
   Triangle  tri(10,5);
 
   // 存储矩形的地址
   shape = &rec;
   // 调用矩形的求面积函数 area
   shape->area();
 
   // 存储三角形的地址
   shape = &tri;
   // 调用三角形的求面积函数 area
   shape->area();
   
   return 0;
}
```

> 形成多态必须具备的三个条件：
>
> 1. 必须存在继承关系；
> 2. 继承关系必须有同名虚函数（**其中虚函数是在基类中使用关键字Virtual声明的函数，在派生类中重新定义基类中定义的虚函数时，会告诉编译器不要静态链接到该函数**）；
> 3. 存在基类类型的指针或者引用，通过该指针或引用调用虚函数；



## 8. 抽象和接口

C++ 中抽象和接口通过**纯虚函数**来实现。带有纯虚函数的类为**抽象类**，无法实例化，只有实现了这个纯虚函数的子类才能实例化。纯虚函数只提供声明，是对子类的约束。

```c++
class Shape {
   protected:
      int width, height;
   public:
      Shape( int a=0, int b=0)
      {
         width = a;
         height = b;
      }
      // 以下是一个纯虚函数
      virtual int area() = 0;
};
```



## 9. STL

可以快速随机访问的，修改慢。可以快速随机修改的，随机访问慢。这是和数据结构相对应的。

C++ STL（标准模板库）是一套功能强大的 C++ 模板类。

| 组件             | 描述                                       |
| -------------- | ---------------------------------------- |
| 容器（Containers） | 容器是用来管理某一类对象的集合。C++ 提供了各种不同类型的容器，比如 deque、list、vector、map 等。 |
| 算法（Algorithms） | 算法作用于容器。它们提供了执行各种操作的方式，包括对容器内容执行初始化、排序、搜索和转换等操作。 |
| 迭代器（iterators） | 迭代器用于遍历对象集合的元素。这些集合可能是容器，也可能是容器的子集。      |

### Array-数组

```
array<int,3> = {0,1,2};
```



### Vector-向量 

- 快速的随机访问
- 快速的尾部增删
- 按需修改大小

```c++
#include <iostream>
#include <vector>							// 头文件
using namespace std;
 
int main()
{
   // 创建一个向量存储 int
   vector<int> vec; 
   int i;
 
   // 显示 vec 的原始大小
   cout << "vector size = " << vec.size() << endl;
 
   // 推入 5 个值到向量中，注意无法通过下标的方式添加元素！！！！
   for(i = 0; i < 5; i++){
      vec.push_back(i);
   }
  
  // 一种读入一组数字的方法
  int tem;
  while(cin>>tem){
      if(cin.get() != '\n') vec.push_back(tem);
      else {
          vec.push_back(tem);
          break;
      };
  }

   // 访问向量中的 5 个值
   for(i = 0; i < 5; i++){
      cout << "value of vec [" << i << "] = " << vec[i] << endl;
   }
 
   // 使用迭代器 iterator 访问值
   vector<int>::iterator v = vec.begin();
   while( v != vec.end()) {
      cout << "value of v = " << *v << endl;
      v++;
   }
 
   return 0;
}
```

> - push_back( ) 成员函数在向量的末尾插入值，如果有必要会扩展向量的大小。
> - pop_back() 删除最后一个元素
> - size( ) 函数显示向量的大小。
> - begin( ) 函数返回一个指向向量开头的迭代器。
> - end( ) 函数返回一个指向向量末尾的迭代器。

```C++
vector<int> v1(10);         // v1有10个元素，每个的值都是0
vector<int> v2{10};         // v2有1个元素，该元素的值是10

vector<int> v3(10,1);       // v3有10个元素，每个的值都是1
vector<int> v4{10,1};       // v4有2个元素，值分别是10和1

vector<string> v5{"hi"};    // v5有一个元素"hi"
vector<string> v6("hi");    // 错误，不能用字符串字面值构建vector对象
vector<string> v7{10};      // v7有10个默认初始化的元素
vector<string> v8{10,"hi"}; // v8有10个值为"hi"的元素
```

### Deque-双端队列

两段都可以插入删除的队列，可通过下标访问。

- 快速的随机访问
- 快速的双端操作
- 按需修改大小

```c++
deque<int> d;
deque<int>::iterator d;

push_back;
push_front;
pop_back;
pop_front;
```

### List-双向链表

可以双向操作的链表，不支持随机访问（下标访问）。

- 快速的随机插入和删除
- 按需修改大小

```c++
list<int> l;
list<int>::iterator l;

splice	    // 使元素从一个列表移动到另一个列表
remove	 	// 删除值为指定值的元素
remove_if	// 删除满足指定条件的元素  foo.remove_if([](int n){ return n > 10; }); 
unique	 	// 删除重复值
merge	 	// 合并已排序的列表
sort	 	// 为容器中的所有元素排序
reverse	 	// 反转元素的顺序
```

### Set-集合

内部是红黑二叉树的实现，不能重复的有序容器，不支持随机访问（下标访问）。

- 快速的随机插入和删除
- 自动排序

```c++
set<int> s;
list<int>::iterator s;

find	// 通过给定值查找元素
count	// 返回匹配给定值的元素的个数
```

### MultiSet-多键集合

可以重复的 set。

### Map-映射表

内部是 hash 实现，相当于其他语言中的键值数组，可通过 key 访问。

```c++
map<string, int> m1; // <>里的第一个参数表示key的类型,第二个参数表示value的类型
m1["Kobe"] = 100;
m1["James"] = 99;
m1["Curry"] = 98;

m1.insert(pair<string, int>("Harris", 89)); // 也可以通过insert函数来实现增加元素

m1.erase("Curry"); // 通过关键字来删除

if(m1.count("Lee")){}  // 可以这样判断元素是否存在
```

### MultiMap-多键映射

键值关系是一对多的关系，不能通过 key 访问。

```c++
multimap<string, int> m2;

m2.insert(pair<string, int>("Harris", 89)); // 新增
m2.find("Harris");  // 返回第一个
m2.count("Harris"); // 数有多少个指定 key 的元素

lower_bound(k); // 查找第一个与键 k 关联的值
upper_bound(k)：// 查找第一个键值比 k 大的元素
```

### Stack-栈

先进后出。

```c++
stack<int> s;
```

| 函数名     | 功能          | 复杂度  |
| ------- | ----------- | ---- |
| size()  | 返回栈的元素数     | O(1) |
| top()   | 返回栈顶的元素     | O(1) |
| pop()   | 从栈中取出并删除元素  | O(1) |
| push(x) | 向栈中添加元素x    | O(1) |
| empty() | 在栈为空时返回true | O(1) |

### Queue-队列

先进先出。

```c++
queue<int> q;
```

| 函数名     | 功能        |
| ------- | --------- |
| front() | 返回第一个元素   |
| back()  | 返回最后一个元素  |
| empty() | 为空是返回true |
| size()  | 返回有效元素个数  |
| push()  | 压入一个元素    |
| pop()   | 弹出一个元素    |

### priority_queue-优先级队列

底层是用堆来实现的。其第一个元素总是容器所包含的元素中按特定的严格弱序排序规则排序后最大的一个。

```c++
priority_queue<int> q;  // 这个和默认等价

priority_queue<int, vector<int>, greater<int> > q;  // 堆顶为最大元素
priority_queue<int, vector<int>, less<int> > q;     // 默认 - 堆顶为最大元素

// 优先函数
bool cmp(int a, int b) {
	return a > b;	// 符号右边的优先级高
}
```

| 函数名 | 功能                             |
| ------ | -------------------------------- |
| top    | 访问堆顶元素                     |
| pop    | 移除堆顶元素                     |
| push   | 在顶部插入元素，插入后会自动排序 |

### 堆

**大顶堆：**根结点的值要大于左右孩子

**小顶堆：**根结点的值要小于左右孩子

```c++
vector<int> vec = {8,3,4,8,9,2,3,4};

make_heap(vec.begin(), vec.end());	// 进行堆排序，默认为大顶堆
make_heap(vec.begin(), vec.end(), greater<int> ());	// 进行堆排序，小顶堆

// push_heap()，在已经构造好的堆结构中插入一个元素
vec.push_back(56);					// 首先压入堆数组的末尾
push_heap(vec.begin(), vec.end());	 // 进行一次排序调整

// pop_heap()，在已经构造好的堆结构中弹出一个元素
pop_heap(vec.begin(), vec.end());	// 实际上是把顶部元素放到了末尾
vec.pop_back();   				   // 到了这一步才是实际删除
```



### iterator-迭代器 

迭代器可以方便的遍历标准库中提供的各种容器

```c++
for(auto it = s.begin(); it != s.end(); ++it) *it = toupper(*it); 
    
vector<int>::iterator it;           // it能读写vector<int>的元素
string::iterator it2;               // it2能读写string对象中的字符

vector<int>::const_iterator it3;    // it3只能读元素，不能写元素
string::const_iterator it4;         // it4只能读字符，不能写字符
```

auto 数据类型在这里达到很方便、简洁的书写效果。

```c++
vector<int> v;

vector<int>::interator iter = v.begin(); // 完整的写法

auto I = v.begin();   // 使用 auto 的简便写法
```

> begin() 和 end() 分别指向容器的首元素和尾元素
>
> cbegin() 和 cend() 和上面类似，只是获取的只是可读
>
> *iter   返回迭代器所指向的元素

### 10. 正则

#### 头文件

```c++
#include <regex>
```

#### 正则表达式对象

构建正则表达式对象，basic_regex 是一个用来构建正则表达式的通用类模板：

```c++
regex re("(http[s]?)");			   // 构建正则式对象
```

| 用法                     | 作用                                       |
| ---------------------- | ---------------------------------------- |
| regex re               | 产生空的正则式，可以匹配任何字符序列                       |
| regex re(str)          | 正则式为str，str可以为basic_string，也可以是0结尾的char*字符串。 |
| regex re(re2)          | 拷贝构造。                                    |
| regex re(str,flag)     | 正则式为str，使用flag语法选项，flag是一组常量的组合。例如：icase可以使正则式匹配忽略大小写。 |
| regex re(beg,end)      | 使用迭代器构造正则式。可以是basic_string的迭代器，也可以是const char*。 |
| regex re(beg,end,flag) | 使用迭代器构造正则式，flag是语法选项。                    |

| flag_type | 效果          |
| --------- | ----------- |
| icase     | 在匹配过程中忽略大小写 |
| nosubs    | 不保存匹配的子表达式  |
| optimize  | 执行速度优于构造速度  |

#### 存储容器

| 实现类           | 数据类型   | 作用                                       |
| ------------- | ------ | ---------------------------------------- |
| sub_match     | 无      | 用来表示匹配的一个结果                              |
| match_results | smatch | 用来表示一次搜索或匹配算法的所有结果，类似于vector \<sub_match\>，可以下标访问 |

```c++
smatch m;  // 存放匹配的字符串
```

#### 匹配结果

| 函数            | 作用                                       |
| ------------- | ---------------------------------------- |
| regex_match   | 尝试将一个正则表达式匹配**一整个**字符序列                  |
| regex_search  | 尝试将一个正则表达式匹配字符序列的**任意部份**                |
| regex_replace | 用格化式过的替换文本（Formatted replacement text）替换正则表达式所匹配的序列 |

```c++
#include <iostream>
#include <regex>
#include <string>

int main(int argc, char* argv[])
{
    std::string str = "Hello World!";
    std::string str2 = "My first program is Hello World! oh";
    std::smatch match;          // match是regex正则表达式对象

    std::regex re("(Hel)(.*)"); // 匹配字符串以Hel开头，后可跟任意字符

    // regex_match
    if ( std::regex_match(str,re))
        std::cout << "字符串(Hel)(.*) \n";
    if ( std::regex_match(str.begin(), str.end(), re) )
        std::cout << "字符串(Hel)(.*) \n";

    // regex_search
    std::regex_search(str2,match,re);
    for (auto x : match)
        std::cout << x << std::endl;

    // regex_replace
    std::string str3 = "My first program is Hello World! oh yeah\n";
    std::regex r("Hel[a-zA-z]+");
    std::cout << std::regex_replace(str, r, "Fuck") << std::endl;

    return 0;
}
```



## *11. 不常用

### 日期 & 时间

### 存储类

- extern
- mutable
- thread_local (C++11)



# 二、开发

前面是刷题可能会用到的，后面试开发会用到的。



## 文件操作



## 异常处理



## 动态内存

- **栈：**在函数内部声明的所有变量都将占用栈内存。
- **堆：**这是程序中未使用的内存，在程序运行时可用于动态分配内存。



## 命名空间



## 模板

泛型编程



## 预处理器



## 信号处理



## 多线程



## Web编程



