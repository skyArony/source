---
title: C++刷题常用操作
tags:
  - 算法
  - C++
categories:
  - 算法
  - C++
date: 2018-04-11 01:46:55
thumbnail:
---

整理一些常用 C++ 刷题操作。



# 一、容器

### 1. 常用容器

```c++
// 建议用 C++ 支持的数组
array<int, 3> arr = {0,1,2};

// 可自动增删的容器，方便的后端增删
vector<int> vec;

// 可自动增删的容器，方便的双端增删
deque<int> deq;

// 可自动增删的容器，方便地删除指定值（remove），和去除重复值（unique），不支持下标访问
list<int> list;

// 可自动增删的容器，自动去重，方便的查找元素位置（find-迭代器）,数元素（count），不支持下标访问
set<int> set;  // multset-可重复的set

// 键值数组
map<string, int> m;

// 对
pair<int, int> p(1, 1.2);
// = 赋值
p = make_pair(1, 1.2);
// 元素访问
p.first；
p.second；
```

> 以上容器都支持如下操作：
>
> - size - 返回有效元素个数，但是对于 array 只会返回预定义的大小
> - empty - 判断是否为空
>
> 某些容器支持如下操作：
>
> - front - 获取第一个元素
> - back - 获取最后一个元素
> - insert - 插入一个元素
> - erase - 删除一个元素
>
> 所有容器都支持如下操作：
>
> - begin - 获取第一个元素的迭代器，加 * 号获取其值
> - end - 获取最后一个元素的迭代器，加 * 号获取其值
>
> 容器之间如何选择：
>
> - 如果你需要高效的随即存取，而不在乎插入和删除的效率，使用vector
> - 如果你需要大量的插入和删除，而不关心随即存取，则应使用list
> - 如果你需要随即存取，而且关心两端数据的插入和删除，则应使用deque。
> - 在搞不清状态时，通常vector是最佳选择。



### 2. 容器的遍历

#### 通过下标

```c++
for(int n = 0; n < vec.size(); n++)
```

#### 通过迭代器

```c++
for(auto it = vec.begin(); it != s.end(); it++)
// 如果你想逆向遍历
for(auto it = vec.rbegin(); it != s.rend(); it++)
// 求两个迭代器之间的距离
distance(first, last);//返回last - first的距离      
```

#### std::for_each

```c++
// func 在这可以用 Lambda
std::for_each(vec.begin(), vec.end(), func);
```

#### each

```c++
for each(auto item in vec)
```

#### C++11 最新的写法

```c++
for(auto item : vec)  
```



# 二、STL

### 1. STL 常用函数

| 函数               | 作用                                       |
| ---------------- | ---------------------------------------- |
| count            | 返回某个元素的个数——还有 count_if                   |
| find             | 查找某个元素的位置，返回迭代器——还有 find_if              |
| search           | 从左向右查找符合的子串位置，返回迭代器                      |
| find_end         | 从右向左查找符合的子串位置，返回迭代器                      |
| fill             | 给一个范围的元素赋予给定值                            |
| remove           | 删除一个范围中等价于给定值得元素                         |
| reverse          | 反转一个容器的元素                                |
| swap             | 交换两个元素的位置                                |
| unique           | 删除容器中的重复元素                               |
| sort             | 升序排序一个范围的元素                              |
| sort-降序          | sort(begin,end,greater\<data-type>())     less是升序 |
| stable_sort      | 稳定排序                                     |
| includes         | 输入需排好序，判断一个集合是否是另一个集合的子集，返回 bool         |
| binary_search    | 二分查找判断是否存在给定元素                           |
| max              | 返回两个值中的最大值                               |
| max_element      | 返回容器中的最大值                                |
| min              | 返回两个值中的最小值                               |
| min_element      | 返回容器中的最小值                                |
| accumulate       | 累加范围内的元素，第三个参数为初值                        |
| rotate           | 旋转元素，就是头部按顺序放到尾部，尾部按顺序放到头部               |
| next_permutation | 传入前最后先排序，不然返回的只是从输入串开始的排列，下一个全排列，最大排列，返回false，反之返回true |
| prev_permutation | 传入前最后先排序，不然返回的只是从输入串开始的排列，上一个全排列，最小排列，返回false，反之返回true |
| lower_bound      | 传入前要先排序， 返回第一个大于等于给定值的元素的迭代器，如果没有则返回最后一个元素之后的位置 |
| upper_bound      | 传入前要先排序， 返回第一个大于给定值的元素的迭代器，如果没有则返回最后一个元素之后的位置 |
| partition        | 按照比较函数将元素分别置于容器两端，返回第二个序列开始的一个元素的迭代器， stable_partition 是稳定划分 |

```c++
// 在普通数组中使用 sort
sort(&arr[i], &arr[j]);
sort(arr, arr + n);		// 另外注意这里的范围写法，在迭代器慢的时候可以这么写

// 全排列
while (next_permutation(str.begin(), str.end()))
{
    cout << str << endl;
}
```



### 2. [更多参考-ClassFoo](http://classfoo.com/ccby/article/tZTzs#sec_17PStx)



# 三、更快

## 1. cin 的优化

[这篇博客以及他提到的几篇博客](https://liam0205.me/2016/11/19/the-speed-of-reading-files-in-Cpp/) 详细的解释了这个问题，总结如下：

```c++
// 通过解除 cin 和 cout 的流绑定加速
std::cin.tie(nullptr);

// 通过解除 cin 和scanf 的流绑定加速
std::ios::sync_with_stdio(false);

// 通用的一个模板
static const auto __ = [](){
    ios::sync_with_stdio(false);
    cin.tie(nullptr);
    return nullptr;
}();

ios::sync_with_stdio(false);
cin.tie(nullptr);
```



## 2. 指针的指针

通过双重指针可以修改原指针的值，但是通过单指针却不可以：

```c++
int a = 3;
int b = 4;
int* c = &a;

int* x = c;		// 指针
int** y = &c;	// 指针的指针

x = &b;   cout << *c; // 结果为 3
*y = &b;  cout << *c; // 结果为 4	
```

如果看不懂的话，看下面这个就知道什么意思了：

```c++
int a = 3;
int b = 4;

int c = a;
int* d = &a;

c = b;   cout << a; // 结果为 3
d = &b;  cout << a; // 结果为 4
```

> 把一个指针的值赋给另一个指针，然后修改指针
>
> ====== 等价于 ======
>
> 把 a 变量的值赋给 c，然后修改 c 的值
>
>   
>
> 把一个指针的地址值赋给一个指针的指针，然后修改指针的指针
>
> ====== 等价于 ======
>
> 把 a 变量的地址赋给一个指针 c，然后修改 c 的值



# 四、一些注意

## 1. 链表

LeetCode 上链表的题大部分所给的样例都是不带头结点的链表。

```c++
// 数据结构
struct ListNode {
    int val;
    ListNode *next;
    ListNode(int x) : val(x), next(NULL) {}
};

// 不带头结点的空单链表，该怎么表示？head 为头结点指针
head == NULL;
```

