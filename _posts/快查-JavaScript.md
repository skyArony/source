---
title: 快查-JavaScript
tags:
  - JavaScript
categories:
  - 快查
  - JavaScript
date: 2018-04-12 17:05:10
thumbnail:
---

整理一些基础语法，主要记了下容易和其他语言混淆的一些地方和 JavaScript 的一些特点。



# 一、语法

## 1. 数据类型

在 JavaScript 中有 5 种不同的数据类型：

- string
- number
- boolean
- object
- function

3 种对象类型：

- Object
- Date
- Array

2 个不包含任何值的数据类型：

- null
- undefined

声明新变量时，可以使用关键词 "new" 来声明其类型：

```javascript
var carname=new String;
var x=      new Number;
var y=      new Boolean;
var cars=   new Array;
var person= new Object;
```

### 数组-Array

三种方式

```javascript
// #1
var cars=new Array();
cars[0]="Saab";
cars[1]="Volvo";
cars[2]="BMW";

// #2
var cars=new Array("Saab","Volvo","BMW");

// #3
var cars=["Saab","Volvo","BMW"];

// array 会把数值字符串转化为合适的数字
var a=new Array();
a['a1']=1;	// 这是对象属性
a[2]=2;	    // 这才是数组元素
a.length  // 输出3

var b = new Array();
b['100'] = 100;
b.length  // 输出101
```

### 对象-Object

```JavaScript
// #1 对象定义方式
var person = {
    firstName: "John",
    lastName : "Doe",
    id : 5566,
    fullName : function() 
	{
       return this.firstName + " " + this.lastName;
    }
};

// #2 或者先创建一个对象再赋予赋予一个新方法
var person=new Object();
person.firstName = "John";
person.lastName = "Doe";
person.method=function(){
  return this.firstname+this.lastname;
}

// #3 对象构造器的方法创建
function person(firstname,lastname,age,eyecolor)
{
    this.firstname=firstname;
    this.lastname=lastname;
    this.age=age;
    this.eyecolor=eyecolor;
}
var myFather=new person("John","Doe",50,"blue");


// 取值
name=person.lastname;
name=person["lastname"];
```

### 字符串-String

字符串属性

| 属性          | 描述           |
| ----------- | ------------ |
| constructor | 返回创建字符串属性的函数 |
| length      | 返回字符串的长度     |
| prototype   | 向对象添加属性和方法   |

常用方法

| 方法            | 描述                                     |
| :------------ | -------------------------------------- |
| charAt()      | 返回指定索引位置的字符，也可以直接 str[7] 这样取           |
| indexOf()     | 返回字符串中检索指定字符第一次出现的位置                   |
| lastIndexOf() | 返回字符串中检索指定字符最后一次出现的位置                  |
| match()       | 找到一个或多个正则表达式的匹配                        |
| replace()     | 替换与正则表达式匹配的子串                          |
| search()      | 检索与正则表达式相匹配的值                          |
| slice()       | 提取字符串的片断，并在新的字符串中返回被提取的部分，参数为开始和结束（可选） |
| split()       | 把字符串分割为子字符串数组，第二个参数表示切割出多少个            |
| substr()      | 从起始索引号提取字符串中指定数目的字符                    |
| substring()   | 提取字符串中两个指定的索引号之间的字符                    |
| toString()    | 返回一个字符串对象值，Number.toSring()            |
| valueOf()     | 返回某个字符串对象的原始值                          |
| trim()        | 移除字符串首尾空白                              |
| String()      | String(100 + 23)  // 将数字表达式转换为字符串并返回   |

### 数字-Number

**无穷大 Infinity：**

```JavaScript
var x = 2/0;	// Infinity	
var y = -2/0;	// -Infinity
```

**非数字值 NaN：**

可以用 `isNaN` 这个函数来判断一个值是否为 NaN 值

```JavaScript
var x = 1000 / "Apple";
isNaN(x); // 返回 true	
var y = 100 / "1000";
isNaN(y); // 返回 false
```

### 日期-Date

实现一个网页钟表

```JavaScript
function startTime(){
	var today=new Date();		// 获取当前时间
	var h=today.getHours();		// 获取时间的小时
	var m=today.getMinutes();
	var s=today.getSeconds();
	m=checkTime(m);
	s=checkTime(s);
	document.getElementById('txt').innerHTML=h+":"+m+":"+s;
	t=setTimeout(function(){startTime()},500);
}
// 在小于10的数字前加一个‘0’
function checkTime(i){
	if (i<10){
		i="0" + i;
	}
	return i;
}
```

四种初始化日期的方法

```JavaScript
new Date() // 当前日期和时间
new Date(milliseconds) //返回从 1970 年 1 月 1 日至今的毫秒数
new Date(dateString)
new Date(year, month, day, hours, minutes, seconds, milliseconds)

var today = new Date()
var d1 = new Date("October 13, 1975 11:13:00")
var d2 = new Date(79,5,24)
var d3 = new Date(79,5,24,11,33,0)
```









## 2. 关键字

### typeof

typeof 用来检测变量的数据类型。

```JavaScript
typeof "John"                // 返回 string 
typeof 3.14                  // 返回 number
typeof false                 // 返回 boolean
typeof [1,2,3,4]             // 返回 object
typeof {name:'John', age:34} // 返回 object
typeof null				    // 返回 object
typeof NaN                    // 返回 number
typeof myCar                  // 返回 undefined (如果 myCar 没有声明)

// 由于 Date 和 Array 的返回都为 Object ，所以无法通过它来判断一个对象到底是日期还是数组
// 所以可以通过以下这种方式
function isArray(myArray) {
    return myArray.constructor.toString().indexOf("Array") > -1;	// 为数组时返回 true
}
function isDate(myDate) {
    return myDate.constructor.toString().indexOf("Date") > -1;		// 为Date时返回 true
}
```

### instanceof

可通过 instanceof 操作符来判断对象的具体类型，语法格式:

```JavaScript
// var result = objectName instanceof objectType   返回布尔值
arr = [1,2,3];
if(arr instanceof Array){
    document.write("arr 是一个数组");
} else {
    document.write("arr 不是一个数组");
}
```

### undifined 和 null

undefined 这个值表示变量不含有值，可以用来将变量清空，也可以通过将变量的值设置为 null 来清空变量。

**null** 用于对象, **undefined** 用于变量，属性和方法。

```javascript
var x = 5;
typeof x;	// 5
var x = null;	
typeof x;	// null
var x = undefined;
typeof x 	// undefinded

// 两者的布尔属性都为 false
if(typeof a!="undefined"){}    // 最好还是以这种方法，不然如果 a 没有被定义就会报错
```

### debugger 关键字

相当于设置一个断点；

```JavaScript
var x = 15 * 5;
debugger;
document.getElementbyId("demo").innerHTML = x;
```

### constructor 属性

返回构造函数

```JavaScript
"John".constructor                 // 返回函数 String()  { [native code] }
(3.14).constructor                 // 返回函数 Number()  { [native code] }
false.constructor                  // 返回函数 Boolean() { [native code] }
[1,2,3,4].constructor              // 返回函数 Array()   { [native code] }
{name:'John', age:34}.constructor  // 返回函数 Object()  { [native code] }
new Date().constructor             // 返回函数 Date()    { [native code] }
function () {}.constructor         // 返回函数 Function(){ [native code] }
```



## 3. 正则表达式

### 正则式和修饰符

```JavaScript
var patt = /runoob/i;
```

| 修饰符  | 描述                           |
| ---- | ---------------------------- |
| i    | 执行对大小写不敏感的匹配。                |
| g    | 执行全局匹配（查找所有匹配而非在找到第一个匹配后停止）。 |
| m    | 执行多行匹配。                      |

### search

返回子串的起始位置

```javascript
var n = str.search(/Runoob/i);	// 用正则
var n = str.search("Runoob");	// 用字符串
```

### replace

查找并替换

```JavaScript
var txt = str.replace(/microsoft/i,"Runoob");
var txt = str.replace("Microsoft","Runoob");	// 同样可以用字符串
```

### RegExp 对象

```javascript
var pattern = /e/;     // 构建一个正则对象

// test 方法，用于检测是否匹配，匹配则返回true，否则返回false
patt.test("The best things in life are free!");	// true
/e/.test("The best things in life are free!"); 	// 你也可以这么写

// exec 方法，用于返回一个匹配结果的数组
/e/.exec("The best things in life are free!");
```



## 4. 函数

 ### 匿名函数

```JavaScript
var x = function (a, b) {return a * b};
var z = x(4, 3);
```

### 自调用函数

创建了一个匿名的函数，并立即执行它，由于外部无法引用它内部的变量，因此在执行完后很快就会被释放，关键是这种机制不会污染全局对象。这种函数的的定义和调用是一体的。

```JavaScript
// 实际上是一个匿名自调用函数
(function () {
    var x = "Hello!!";      // 我将调用自己
})();
```

### 显式参数(Parameters)

```JavaScript
function functionName(parameter1, parameter2, parameter3) {
    // 要执行的代码……
}
```

### 隐式参数(Arguments)

- arguments不管函数声明时的参数个数，而是调用实际传递给函数的参数;
- arguments[index]获得参数值；
- arguments.length获得实际传递的参数个数；

```javascript
function functionName(){
	console.log(arguments);
}

// 还有一个 callee 属性，实现递归调用
var sum = function(n){  
    if(1==n) {  
        return 1;  
　  } else {  
        return n + arguments.callee(n-1);  
    }  
}  
console.log(sum(10));  
```

### 默认参数

两种写法：

```JavaScript
function myFunction(x, y) {
    if (y === undefined) {
          y = 0;
    } 
}
// or
function myFunction(x, y) {
    y = y || 0;
}
```

### call() 和 apply()

在特定的作用域中调用函数,能改变函数的作用域，实际上是改变函数体内 `this` 的值 。

说通俗一点就是，借你家函数用一下，call 和 apply 效果一行，只是传参不同。

```javascript
foo.call(this, arg1,arg2,arg3) == foo.apply(this, [arg1, arg2, arg3]) == this.foo(arg1, arg2, arg3)
```

**call方法:**
语法：call(thisObj，Object)
定义：调用一个对象的一个方法，以另一个对象替换当前对象。
说明：
call 方法可以用来代替另一个对象调用一个方法。call 方法可将一个函数的对象上下文从初始的上下文改变为由 thisObj 指定的新对象。
如果没有提供 thisObj 参数，那么 Global 对象被用作 thisObj。

**apply方法:**
语法：apply(thisObj，[argArray])
定义：应用某一对象的一个方法，用另一个对象替换当前对象。
说明：
如果 argArray 不是一个有效的数组或者不是 arguments 对象，那么将导致一个 TypeError。
如果没有提供 argArray 和 thisObj 任何一个参数，那么 Global 对象将被用作 thisObj， 并且无法被传递任何参数。



## 5. 其他

### 事件

事件是指浏览器和用户的某种行为，以下是一些常见的事件：

| 事件          | 描述               |
| ----------- | ---------------- |
| onchange    | HTML 元素改变        |
| onclick     | 用户点击 HTML 元素     |
| onmouseover | 用户在一个HTML元素上移动鼠标 |
| onmouseout  | 用户从一个HTML元素上移开鼠标 |
| onkeydown   | 用户按下键盘按键         |
| onload      | 浏览器已完成页面的加载      |

### 循环

```JavaScript
// JS 支持的一种遍历写法
var person={fname:"John",lname:"Doe",age:25}; 
 
for (x in person)  // x 为属性名
{
    txt=txt + person[x];
}
```

### 标签

给一段代码块打上标签，然后就能通过 `continue` 和 `break` 跳出代码块。

```JavaScript
cars=["BMW","Volvo","Saab","Ford"];
list: 								// 这里是标签
{
    document.write(cars[0] + "<br>"); 
    document.write(cars[1] + "<br>"); 
    document.write(cars[2] + "<br>"); 
    break list;
    document.write(cars[3] + "<br>"); 
    document.write(cars[4] + "<br>"); 
    document.write(cars[5] + "<br>"); 
}
```

### 错误

```javascript
function myFunction() {
    var message, x;
    message = document.getElementById("message");
    message.innerHTML = "";
    x = document.getElementById("demo").value;
    try { 										// 测试错误
        if(x == "")  throw "值为空";			    // 抛出错误
        if(isNaN(x)) throw "不是数字";
        x = Number(x);
        if(x < 5)    throw "太小";
        if(x > 10)   throw "太大";
    }
    catch(err) {								// 处理错误
        message.innerHTML = "错误: " + err;
    }
}
```

### 变量提升

JavaScript 中，函数及变量的声明都将被提升到函数的最顶部。

所以 JavaScript 中，变量可以在使用后声明，也就是变量可以先使用再声明。

但是初始化是不会被提升的。

### 严格模式

严格模式是 ES5 新增的，在这个模式下你不能使用未声明的变量

```JavaScript
"use strict";	// 只允许出现在脚本或函数的开头
x = 3.14;       // 报错 (x 未定义)
var x = 3.14	// 不报错
```

###  "+" 号运算符

1. 和逻辑值进行运算时，false 变成 0，true 变成 1：

   ```javascript
   var one=13;
   var two=true;
   var three=one+two;
   // 结果 three:14
   ```

2. 取模运算的结果符号只与左边值的符号有关：

   ```javascript
   var x = 7 % 3; // 结果为 1
   var y = 7 % (-3); // 结果为 1
   var z = (-7) % 3; // 结果为 -1
   ```

3. 数字与 null(空值) 相加，null 转化为数字 0：

   ```javascript
   var car=null+3+4;    // 结果为7
   ```

   字符串与 null(空值) 相加，null 转化为字符串：

   ```javascript
   var car=null+"a";    // 结果为 nulla
   ```

4. 将变量转换为数字，不能转换的话变成 NaN：

   ```javascript
   var y = "5";      // y 是一个字符串
   var x = + y;      // x 是一个数字

   var y = "John";   // y 是一个字符串
   var x = + y;      // x 是一个数字 (NaN)
   ```

5. “-” 号

   ```javascript
   "5" - 1     // 返回 4         "5" 转换为 5
   ```

### JSON

注意 json 是键值都是包在双引号之中的：

```json
{
    "name":"yangf",
    "sex":1
}
```

把 Json 字符串解析为 JavaScript 对象：

```JavaScript
var obj = JSON.parse(text);		// text 为 Json 格式的字符串
var jsonStr = JSON.stringify(obj); 	// 上面的反函数
```

###  javascript:void(0)

表示一个死链接，和 `href="#[pos]"` 存在区别，后者是一个锚点，默认为页面上端，而前者是纯粹的一个死链接。你也可以这么用 `href="javascript:void(alert('Warning!!!'))"`





# 二、常见用法

### 1. 正则校验中文

```JavaScript
var reg=/^[\u4E00-\u9FA5]{2,4}$/;   /*定义验证表达式*/
```

### 2. 表单校验

**onsubmit="return validateForm()"**

```html
<script>
function validateForm() {
    var x = document.forms["myForm"]["fname"].value;
    if (x == null || x == "") {
        alert("需要输入名字。");
        return false;
    }
}
</script>

<form name="myForm" action="demo_form.php" onsubmit="return validateForm()" method="post">
名字: <input type="text" name="fname">
<input type="submit" value="提交">
</form>
```

### 3. 算术运算

主要用到 `Math` 对象，进行算术运算。

```JavaScript
Math.round(4.7);  // 5
```



# 三、注意

### 1. var 与变量作用域

```JavaScript
var int a;		// 全局
int b;		    // 全局
function get(){
    int x;		// 全局
    var int y;	// 局部
}
```



### 2. this

总是指向调用它所在方法的对象，或者说：this指向函数执行时的当前对象。



### 3. 闭包

JavaScript 中子函数可以访问父函数的变量，但是父函数不可以访问子变量。

**能够读取其他函数内部变量的函数**，放到 JavaScript 中就是，定义在函数内部的函数，以为 JavaScript 是链式作用域，即子对象可以访问父对象的数据，但是反过来不行。所以父对象需要访问子对象变量数据时就要用到闭包。

**闭包的用途：**

- 读取函数内部的变量（这个前面解释了）
- 让变量的值始终保存在内存中：如果闭包函数被赋给一个全局变量，那么闭包函数用到的局部变量将不会被回收

**闭包的注意点：**

- 由于闭包会使函数变量保存在内存中是，所以不能滥用闭包
- 由于闭包函数可以在函数外改变函数内的变量，所以要注意你对函数变量的定义是公有还是私有




### 4. DOM

文档对象模型（Document Object Model），一棵树。获取 DOM 元素，操纵 HTML 文档：

```JavaScript
// 获取
getElementById("main");
getElementsByTagName("p");
getElementsByClassName("intro");

// 改变HTML属性
document.getElementById(id).attribute=新属性值
document.getElementById("image").src="landscape.jpg";

// 改变CSS
document.getElementById(id).style.property=新样式
document.getElementById("p2").style.color="blue";

// 事件
document.getElementById("myBtn").onclick=function(){displayDate()};

// 添加监听，最后一个参数选择冒泡（类元素先触发）或者捕获（外元素先触发）
// 冒泡：false   捕获：true
element.addEventListener("click", function(){ alert("Hello World!"); }[, true/false]);

// 移除监听
element.removeEventListener("mousemove", myFunction);

// 增删DOM元素

// 增元素
var para=document.createElement("p");
var node=document.createTextNode("这是一个新段落。");
para.appendChild(node);

// 删除一个元素必需知道它的父元素
var child=document.getElementById("p1");
child.parentNode.removeChild(child);
```

> onclick 和 addEventListener 的区别，前者定义第二个动作时会把第一个覆盖掉，而后者不会



### 5. BOM

浏览器对象模型 (**B**rowser **O**bject **M**odel (BOM)) 使 JavaScript 有能力与浏览器"对话"。

最常用的有诸如 `alert` 和 `location`

#### 计时

- setInterval() - 间隔指定的毫秒数不停地执行指定的代码。
- setTimeout() - 在指定的毫秒数后执行指定代码。

```JavaScript
// 每三秒弹出一次
var myVar = setInterval(function(){alert("Hello")},3000);
// 停止执行
clearInterval(myVar);

// 三秒后执行
var myVar = setTimeout(function(){alert("Hello")},3000);
// 取消执行
clearTimeout(myVar);
```

#### cookie

```JavaScript
// 设置cookie
document.cookie="username=John Doe; expires=Thu, 18 Dec 2013 12:00:00 GMT; path=/";

// 获取cookie
var x = document.cookie;
```



### 6. 浮点型数据

JavaScript 中的所有数据都是以 64 位**浮点型数据(float)** 来存储。

所有的编程语言，包括 JavaScript，对浮点型数据的精确度都很难确定：

```JavaScript
var x = 0.1;
var y = 0.2;
var z = x + y            // z 的结果为 0.3
if (z == 0.3)            // 返回 false
    
var z = (x * 10 + y * 10) / 10;       // z 的结果为 0.3，可以这么写   
```



### 7. 传值和传址 

在JS中，有两种不同的方式可以操作数据的值，分别是 **传值** 和 **传址**。

**传值：**数字(Number)、布尔类型(Boolean)、字符串(String)

**传址：**对象(Object) 、数组(Array)、函数(Function)

如果要复制一个对象，简单的新建然后赋值是不行的，所以就涉及到了深拷贝和浅拷贝。

[**这里有一篇文章，后面好好整理一下**](http://www.css88.com/archives/8319)



# 四、框架

## 1. jQuery

```JavaScript
// 第一种写法：把要执行的函数用 $(document).ready(); 包起来
$(document).ready(function(){
  $("#hide").click(function(){
    $("p").hide();
  });
  $("#show").click(function(){
    $("p").show();
  });
});

// 第二种写法：把要执行的函数作为参数传入
function myFunction()
{
    $("#h01").attr("style","color:red").html("Hello jQuery")
}
$(document).ready(myFunction);
```

