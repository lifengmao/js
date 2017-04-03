# [JavaScript教程学习][1]笔记



---

## Map和Set
---
### Map
JavaScript的默认对象表示方法{}可以视为其他语言中的Map或Dictionary的数据结构，即一组键值对，但是JS中的对象中的键必须是字符串，而其他数据类型作为字符串也是非常合理的。
为了解决这个问题，ES6规范引入了新的数据类型Map。

Map是一组键值对的结构，其查找速度极快。
初始化Map需要一个二维数组，或者直接初始化一个空Map。

    var m = new Map(); //空Map
    m.set('Adam',67);//添加新的键值对
    m.set('Bob',59);
    m.has('Adam'); //判断是否存在Adam键
    m.get('Adam'); //67
    m.delete('Adam'); //删除Adam键
    m.get('Adam'); //undefined
    
一个key只能对于一个value，所以，多次对一个key设置value，后面的值会冲掉前面的值。

### Set

Set也是一组Key的集合，但其不存储Value。
要创建一个Set，需要提供一个Array作为输入，或者直接创建一个空Set

    var s1 = new Set(); //空Set
    var s2 = new Set([1,2,3]); //含1,2,3
    s2.add(4);
    s2; //[1,2,3,4]
    s2.delete(3); //[1,2,4]
    
重复元素在Set会被自动过滤

---

## iterable

遍历数组Array可以使用小标循环，而Map和Set无法使用下标，因此，ES6引入了新的iterable类型，Array、Map、Set都属于iterable类型。

具有iterable类型的集合可以通过新的for ... of循环遍历。

    var a = ['A','B','C'];
    var s = new Set(['A','B','C']);
    var m = new Map([[1,'x'],[2,'y'],[3,'z']]);
    for(var x of a){ //遍历Array
       alert(x);
    }
    for(var x of s) { //遍历Set
        alert(x);
    }
    for(var x of m){ //遍历Map
        alert(x[0]+'='+x[1]);
    }

for...in循环由于历史遗留问题，它遍历的实际上是对象的属性名称。当我们给Array手动添加了额外的属性后

    var a =['A','B','C'];
    a.name = 'Hello';
    for(var x in a) {
        alert(x); //'0','1','2','name'
    }
    
for...in 会把name包括在内，但是Array的length属性却没增加
    
for...of修复了这一问题，它只循环集合本身的元素。
    
更好的方法是直接使用iterable类型内置的foreach方法

    var s = new Set(['A','B','C']);
    s.forEach（function(element,index,array) {
        //element 指向当前的元素
        //index 指向当前索引
        //array：指向Array对象本身
        alert(element);
    }）

---
## 高阶函数

如果一个函数能够接收另一个函数作为参数，这个函数就是高阶函数（Higher-order function）

### map/reduce

map()方法定义在javaScript中的Array中，我们调用Array的map（）方法，传入自己的函数，就可以得到一个新的Array

    function pow(x) {
        return x*x;
    }
    var arr = [1,2,3];
    arr.map(pow); // [1,4,9]

map()传入的是函数对象本身，即pow

reduce方法把一个函数依次作用在Array的元素身上

    var arr = [1,3,5,7,9];
    arr.reduce(function(x,y) {
        return x+y;
    }); //25
    
### filter
filter可以把Array中的某些元素过滤掉，返回剩下的元素。

    var arr = [1,2,3,4,5,6,9,10,15];
    var r = arr.filter(function(x) {
        return x%2 !== 0;
    });
    r; // [1,3,5,9,15]
    
filter()接收的回调函数，其实可以有很多参数。总共可以接收三个参数

    var arr = ['A','B','C'];
    var r = arr.filter(function(element,index,self) {
        //element A,B,C
        //index 0,1,2
        //self arr
        return true;
    })；
### sort
sort()是用于排序的算法，但是它默认把所有元素先转换为string再排序。sort()也是告诫函数，可以接收一个比较函数来实现自定义的排序

    var arr = [10,5,7,15,20];
    arr.sort(function(x,y) {
        if(x<y) {
            return -1;
        }
        if(x>y) {
            return 1;
            }
            return 0;
            }); //[5,7,10,15,20]

---
## generator

generator是ES6引入的新的数据类型，它可以返回多次

    function* foo(x) {
        yield x+1;
        yield x+2;
        yield x+3;
        return x+4;
    }

直接调用generator仅仅是创建了一个generator对象，还没有去执行它。有两种调用执行的方法
第一种：

    var f = foo(5);
    f.next();
    f.next();
第二种：

    for(var x of foo(5)) {
        console.log(x);
    }

---
## 标准对象

 - 不要使用new Number()、new Boolean()、new String()创建包装对象；
 - 用parseInt()或parseFloat()来转换任意类型到number；
 - 用String()来转换任意类型到string，或者直接调用某个对象的toString()方法；
 - 通常不必把任意类型转换为boolean再判断，因为可以直接写if (myVar) {...}；
 - typeof操作符可以判断出number、boolean、string、function和undefined；
 - 判断Array要使用Array.isArray(arr)；
 - 判断null请使用myVar === null；
 - 判断某个全局变量是否存在用typeof window.myVar === 'undefined'；
 - 函数内部判断某个变量是否存在用typeof myVar === 'undefined'。
 
number对象调用toString（）报SyntaxError，要进行特殊处理

    123..toString(); //'123',两个点
    (123).toString();//'123'

### Date

Date对象用来表示日期和时间。

    var now = new Date();
    now; // Wed Jun 24 2015 19:49:22 GMT+0800 (CST)
    now.getFullYear(); // 2015, 年份
    now.getMonth(); // 5, 月份，注意月份范围是0~11，5表示六月
    now.getDate(); // 24, 表示24号
    now.getDay(); // 3, 表示星期三
    now.getHours(); // 19, 24小时制
    now.getMinutes(); // 49, 分钟
    now.getSeconds(); // 22, 秒
    now.getMilliseconds(); // 875, 毫秒数
    now.getTime(); // 1435146562875, 以number形式表示的时间戳
    
注意，JS中月份的范围用整数表示是0-11,0表示一月份，11表示12月份。

Date对象表示的时间总是按浏览器所在时区显示的，不过我们既可以显示本地时间，也可以显示调整后的UTC时间：

    var d = new Date(1435146562875);
    d.toLocaleString(); // '2015/6/24 下午7:49:22'，本地时间（北京时区+8:00），显示的字符串与操作系统设定的格式有关
    d.toUTCString(); // 'Wed, 24 Jun 2015 11:49:22 GMT'，UTC时间，与本地时间相差8小时

### RegExp

关于正则打算写一个专门的文档，这里暂时略过。

### JSON
JSON是JavaScript ObjectNotation的缩写，它是一种数据交换格式。

    var xiaoming = {
    name: '小明',
    age: 14,
    gender: true,
    height: 1.65,
    grade: null,
    'middle-school': '\"W3C\" Middle School',
    skills: ['JavaScript', 'Java', 'Python', 'Lisp']
    };

    JSON.stringify(xiaoming); // '{"name":"小明","age":14,"gender":true,"height":1.65,"grade":null,"middle-school":"\"W3C\" Middle School","skills":["JavaScript","Java","Python","Lisp"]}'
    
---


    

  [1]: http://www.liaoxuefeng.com/wiki/001434446689867b27157e896e74d51a89c25cc8b43bdb3000/0014345007434430758e3ac6e1b44b1865178e7aff9082e000