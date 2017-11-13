# let和const命令

---

## 块级作用域

ES5中只有全局作用域和函数作用域，没有块级作用域。
ES6中引入了let和const方法，实际上就是为JavaScript新增了块级作用域。

* ES6允许块级作用域的任意嵌套。
* 块级作用域的出现，实际上使得ES5中广泛使用的IIEF（立即执行函数表达式）变得不再必要。

---
## let命令

### 基本用法

es6新增了let命令，用来声明变量。let与var的作用类似，其区别主要有以下几点：

* let具有块作用域
* let不存在变量提升
* 当在let命令声明变量前使用变量时，let会形成“暂时性死区”(temporal dead zone,TDZ)。
* let不允许重复声明

### 实例代码

```javascript
//ES6强制开启严格模式
function test(){
    //let具有块作用域 用let定义的变量只在自己的作用域生效。
    for(let i = 1; i < 3; i++){
        console.log(i);
    }
    //console.log(i); 会报错
    for(var i = 1; i < 3; i++){
        console.log(i);
    }
    console.log(i); //输出3

    //用let不能重复定义相同变量
    // let a = 1;
    // let a = 2; //报错！
}
```
---

## const命令

### 基本用法

const声明一个只读的常量。一旦声明，常量的值就不能改变。
这意味着，const一旦声明变量，就必须立即初始化，不能留到以后赋值。
const实际上保证的，并不是变量的值不得改动，而是变量指向的那个内存地址不得改动。
与let类似：
* const同样只在声明的块级作用域内有效
* const命令声明的常量同样不提升
* const同样存在TDZ，只能在声明的位置后面使用。
* const不可重复声明

### 实例代码

```javascript
function last(){
    //const定义常量 这个变量不能被修改
    //const声明的时候必须赋值 否则会报错
    //const foo; //SyntaxError
    const PI = 3.1415926;
    // PI = 8； //报错
    //k存储的是指向对象的指针 指针是不能变的 而对象的值是可以变得
    const k = {
        a:1
    };
    k.b = 3;
    // k = {
    //     c:3
    // };      报错
    console.log(PI,k); // 3.1415926 {a: 1, b: 3}
}
```

---

## 全局变量

在ES5中，全局变量与顶层对象的属性是等价的，在浏览器环境中即为window对象，在node中是global对象。顶层对象的不同，导致了它在各种实现里面的不统一。

- 浏览器里面，顶层对象是window，但 Node 和 Web Worker 没有window。
- 浏览器和 Web Worker 里面，self也指向顶层对象，但是 Node 没有self。
- Node 里面，顶层对象是global，但其他环境都不支持。

因此，ES6有一个提案，在语言标准的层面，引入global作为顶层对象，即，在所有环境下，global都是存在的，可以从它拿到顶层对象。
