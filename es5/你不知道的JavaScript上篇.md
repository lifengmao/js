﻿# 你不知道的JavaScript 上篇


---
#第一部分
---
## 第一章 作用域


> 变量的赋值操作会执行两个动作，首先编译器会在当前作用域中声明一个变量（如果当前未声明，如果已经声明，则会忽略此声明），然后运行引擎时会在作用域中查找该变量，如果能够找到就会对它赋值。

遍历嵌套作用域：首先从当前作用域进行查找，如果找不到，就向上一级继续查找。当抵达最外层的全局作用域时，查找过程停止。

ReferenceError：同作用域判别失败相关； TypeError：作用域判别成功，但是对结果的操作非法或者不合理。

> 作用域是一套规则，用于确认在何处以及如何查找变量（标识符）。如果查找的目的是对变量进行复制，就会使用LHS查询；如果目的是获取变量的值，就会使用RHS查询。

---

## 第二章 词法作用域

作用域主要有两种工作模式：词法作用域和动态作用域。

> 简单来说，词法作用域就是定义在词法阶段的作用域。换句话说，词法作用域是由你在写代码时将变量和块作用域写在哪里决定的，因此当词法分析器处理代码时会保持作用域不变（并不绝对）。
作用域查找会在找到第一个匹配的标识符时停止，在多层嵌套作用域中可以定义同名的标识符，这叫做“遮蔽效应”。

全局变量会自动称为全局对象的属性。因此可以间接的通过全局对象属性的引用来对其进行访问。

    window.a

欺骗词法作用域会导致性能下降。JS中有两种欺骗词法作用域的方法。

### eval
eval（）函数可以接受一个字符串作为参数，并在你写的代码中用程序生成代码并运行。

    function foo(str, a) {
        eval(str); //欺骗！
        console.log(a,b);
    } 
    var b = 2;
    foo("var b = 3;", 1); //1,3
    

### with
with通常被当做重复引用同一个对象中的多个属性的快捷方式，可以不重复引用对象本身。

    var obj = {
        a:1,
        b:2,
        c:3
    };
    //单调重复的引用obj
    obj.a = 2;
    obj.b = 3;
    obj.c = 4;
    //使用with
    with(obj) {
        a:2;
        b:3;
        c:4;
    }

with声明会根据你传递给它的对象凭空创建一个全新的词法作用域，因此需慎用with。


eval（）和with会被严格模式所影响。with在严格模式中被完全禁用，间接或非安全的使用eval（）也被完全禁止了。

> 词法作用域意味着作用域是由书写代码时函数声明的位置来决定的。编译的词法分析阶段基本能够知道全部标识符在哪里以及是如何声明的，从而能够预测在执行过程中如何对它们进行查找。

---

## 第三章 函数作用域和块作用域

函数作用域的含义是指，属于这个函数的全部变量都可以在整个函数的范围内使用及复用（事实上在嵌套的作用域中也可以使用）。

使用let进行的声明不会在块作用域中进行提升。（let是ES6的新方法）

块作用域指的是变量和函数不仅可以属于所处的作用域，也可以属于某个代码块（通常是{...}内部）。

---

## 第四章 提升

在JavaScript中，所有声明都会在执行代码前被处理。

> 只有声明本身会被提升，而赋值或者其他运行逻辑都会留在原地。如果提升改变了代码执行的顺序，会造成非常严重的破坏。

因此，要养成良好的编程习惯，在一个作用域内，首先对所有需要用到的变量进行声明。

函数声明和变量声明都会被提升，但是函数优先于变量。

---

## 第五章 作用域闭包

> 当函数可以记住并访问所在的词法作用域时，就产生了闭包，即使函数是在当前词法作用域之外执行。

闭包的神奇之处在于，可以阻止内部作用域被销毁，使得该作用域一直存活。

> 在定时器、Ajax请求、跨窗口通信、Web Workers或者任何其他的异步（或者同步）任务中，只要使用了回调函数，实际上就是在使用闭包。


模块模式需要具备两个必要条件：1，必须要有外部的封闭函数；2，封闭函数必须返回至少一个内部函数。

---

#第二部分
---
## 第一章 关于this

this的绑定和函数声明的位置没有任何关系，只取决于函数的调用方式。

> 当一个函数被调用时，会创建一个活动记录（执行上下文）。这个记录会包含函数在哪里被调用、函数的调用方法、传入的参数等信息。this就是记录的其中一个属性，会在函数执行的过程中用到。

this既不指向函数自身，也不指向函数的词法作用域。

---

## 第二章 this全面解析

调用位置是指函数在代码中被调用的位置，this的绑定完全取决于函数的调用位置。

### 默认绑定

当函数是直接使用不带任何修饰的函数引用进行调用的，this使用默认绑定，指向全局对象。

注意当使用严格模式时，全局对象无法使用默认绑定，this会绑定到undefined。

### 隐式绑定

当函数引用有上下文对象时，隐式绑定规则会把函数调用中的this绑定到这个上下文对象。

一个最常见的this绑定问题就是在被隐式绑定的函数会丢失绑定对象，也就是说它会应用默认绑定，从而把this绑定到全局对象或者undefined上。

###  显示绑定

可以使用函数apply（）和call（）来显示的指定this的绑定队象，这两个函数的第一个参数是一个对象，会把这个对象绑定到this，接着在调用函数时指定这个this，在函数调用时指定这个this。

bind（）会返回一个硬编码的新函数，它会把参数设置为this的上下文并调用原始函数。

### new绑定

在js中，构造函数只是一些使用new操作符时被调用的函数，它们并不属于某个类，也不会实例化一个类，它们只是被new操作符调用的普通函数。

使用new操作符时，会自动执行以下操作：1.创建一个全新的对象；2.这个新对象会被执行原型连接；3.这个新对象会绑定到函数调用的this；4.如果函数没有返回其他对象，那么new表达式中的函数调用会自动返回这个新对象。

默认绑定的优先级最低，显示绑定和new绑定的优先级高于隐式绑定。new绑定的优先级高于显示绑定。

### 绑定例外

如果你把null或者undefined作为this的绑定对象传入call、apply或者bind，这些值在调用时会被忽略，实际应用的是默认的绑定规则。

一种更安全的做法是传入一个特殊的对象，把this绑定到这个对象，避免应用默认规则，导致bug。

你有可能创建一个函数的间接引用，在这种情况下，调用函数应用默认规则。

### this词法

ES6中引入了箭头函数（=>）来代替function，箭头函数不使用前面所述的四种规则，而是根据外层作用域来决定this。

箭头函数的绑定不能被修改！

---

## 第三章 对象

对象可以通过两种形式定义：

声明形式

    var myobj = {
        key:value,
        //...
    };
    
构造形式

    var myobj = new Object();
    myobj.key = value;
    //...
    
一般很少使用构造形式。

js中主要有六中基本类型：

 - boolean
 - string
 - object
 - number
 - null
 - undefined

---
### 第五章 原型

js中的对象又一个特殊的[[Prototype]]内置属性。几乎所有对象在创建时[[Prototype]]都会被赋予一个非空的值。

所有的[[Prototype]]链最终都会指向内置的Object.prototype。它包含了js中许多通用的功能。
 
