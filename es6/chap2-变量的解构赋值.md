# 变量的解构赋值
---

ES6允许按照一定模式，从数组和对象中提取值，对变量进行赋值，这称为解构。

解构赋值允许指定默认值，如果默认值是一个表达式，那么这个表达式是惰性求值的，即只有在用到的时候才会求值。默认值生效的条件是，属性值严格等于undefined。

---

## 数组的解构赋值

```javascript
//大括号表示块作用域
{
    let a,b,rest;
    //数组的解构赋值
    [a,b] = [1,2];
    console.log(a,b);
}

{
    let a,b,rest;

    [a,b,...rest]=[1,2,3,4,5,6];
    console.log(a,b,rest); // 1 2 [3,4,5,6]
}
{
  let [foo = true] = [];
  foo //true

  let [x,y='b'] =['a']; //x='a',y='b'
  let [x,y='b'] = ['a',undefined];//x='a',y='b'
}
```

上面代码表示，可以从数组中提取值，按照相应位置，对变量赋值，本质上属于“模式匹配”。
事实上，只要数据结构具有Iterator接口，都可以采用数组形式的解构赋值。

---

## 对象的解构赋值

对象的属性没有次序，因此变量必须与属性同名，才能取到正确的值。
```javascript
{
    let a,b;
    //对象的解构赋值
    ({a,b} = {a:1,b:2})
    console.log(a,b);

    let {baz} = {foo:"aaa",bar:'bbb'};
    baz //undefined
}
```

如果变量名与属性名不一致，必须写成下面这样:
```javascript
{
  let {foo:baz} = {foo:'aaa',bar:'bbb'};
  baz //'aaa'

  let obj = {first: 'hello',last: 'world'};
  let {first:f, last: l} = obj;
  f //'hello'
  l //'world'
}
```

即对象的解构赋值可以简写成以下形式：

```javascript

{
  let {foo:foo,bar:bar} = {foo:"aaa",bar:"bbb"};
}

```

上面的代码中，以foo:foo为例，前面的foo是匹配的模式，后面的foo才是变量，真正被赋值的是变量foo，而不是模式foo。

对象的解构也可以指定默认值。

```javascript
{
  var {x = 3} = {};//x=3

  var {x,y=5} = {x:1}; //x=1 y=5

  var {x:y=3} = {}; //y=3

  var {x:y=3} = {x:5} //y=5
}
```

---

## 字符串的解构赋值

字符串也可以解构赋值，此时字符串被转换成了一个类似数组的对象。
类似数组的对象都有一个length属性，因此还可以对这个属性解构赋值。

```javascript
{
  const [a,b,c,d,e] = 'hello';
  a //'h'
  b //'e'
  c //'l'
  d //'l'
  e //'o'

  let {length: len} = 'hello';
  len //5
}
```

## 数组和布尔值的解构赋值

解构赋值时，如果等号右边是数值和布尔值。则会先转换为对象。
```javascript
{
  let {toString:s} =123;

  s === Number.prototype.toString //true

  let {toString:s} = true;
  s === Boolean.prototype.toString //true
}
```
只要等号右边的值不是对象或数组，就先将其转换为对象。由于undefined和null无法转换为对象，
所以对它们进行解构赋值都会报错。

---

## 函数参数的解构赋值

函数的参数也可以使用解构赋值。同样也可以使用默认值。
```javascript
{
  function move({x=0,y=0} = {}){
    return [x,y];
  }

  move({x:3,y:8});//[3,8]
  move({x:3});//[3,0]
  move({});//[0,0]
  move();//[0,0]
}
{
  //下面代码是为函数move的参数指定默认值，而不是为变量x和y指定默认值，所以会得到与前一种写法不同的结果。
  function move({x, y} = { x: 0, y: 0 }) {
  return [x, y];
  }

  move({x: 3, y: 8}); // [3, 8]
  move({x: 3}); // [3, undefined]
  move({}); // [undefined, undefined]
  move(); // [0, 0]
}
```

## 圆括号的使用问题

ES6规定，只要有可能导致解构的歧义，就不得使用圆括号。

以下三种解构赋值不得使用圆括号
(1) 变量声明语句
(2) 函数参数
(3) 赋值语句的模式

只有在赋值语句的非模式部分，可以使用圆括号。

```javascript
{
  [(b)] = [3];
  ({p:(d)}={});
  [(parseInt.prop)] = [3];
  //以上代码均正确
}
```
建议尽量不要使用圆括号

## 用途

* 交换变量的值
```javascript
{
  let x = 1;
  let y =2;
  [x,y] = [y,x];
}
```
* 从函数返回多个值
```javascript
{
  // 返回一个数组
  function example() {
    return [1, 2, 3];
  }
  let [a, b, c] = example();

  // 返回一个对象
  function example() {
    return {
      foo: 1,
      bar: 2
    };
  }
  let { foo, bar } = example();
  }
```
* 函数参数的定义
```javascript
{
  // 参数是一组有次序的值
  function f([x, y, z]) { ... }
  f([1, 2, 3]);
  // 参数是一组无次序的值
  function f({x, y, z}) { ... }
  f({z: 3, y: 2, x: 1});
  }
```
* 提取JSON数据
```javascript
{
    let jsonData = {
    id: 42,
    status: "OK",
    data: [867, 5309]
    };

    let { id, status, data: number } = jsonData;

    console.log(id, status, number);
    // 42, "OK", [867, 5309]
}
```
* 函数参数的默认值
```javascript
{
  jQuery.ajax = function (url, {
  async = true,
  beforeSend = function () {},
  cache = true,
  complete = function () {},
  crossDomain = false,
  global = true,
  // ... more config
  }) {
    // ... do stuff
  };
}
```
* 遍历Map结构
```javascript
{
  const map = new Map();
  map.set('first', 'hello');
  map.set('second', 'world');

  for (let [key, value] of map) {
    console.log(key + " is " + value);
  }
  // first is hello
  // second is world
}
```
* 输入模块的指定方法
```javascript
{
  const { SourceMapConsumer, SourceNode } = require("source-map");
}
```
