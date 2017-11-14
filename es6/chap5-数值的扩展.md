# 数值的扩展

## 二进制和八进制表示法

ES6 提供了二进制和八进制数值的新的写法，分别用前缀0b（或0B）和0o（或0O）表示。
如果要将0b和0o前缀的字符串数值转为十进制，要使用Number方法。

## 指数运算符

ES2016 新增了一个指数运算符。
```javascript
{
  2 **2 //4
  2 **3 //8

  let a = 1.5;
  a **=2; //===  a^2
}
```

## Number对象新增

* Number.isFinite(),Number.isNaN()
  Number.isFinite()用来检查一个数值是否为有限的（finite）。
  Number.isNaN()用来检查一个值是否为NaN。
  它们与传统的全局方法isFinite()和isNaN()的区别在于，传统方法先调用Number()将非数值的值转为数值，再进行判断，而这两个新方法只对数值有效，Number.isFinite()对于非数值一律返回false, Number.isNaN()只有对于NaN才返回true，非NaN一律返回false。

* Number.parseInt(), Number.parseFloat()
  ES6 将全局方法parseInt()和parseFloat()，移植到Number对象上面，行为完全保持不变。
  这样做的目的，是逐步减少全局性方法，使得语言逐步模块化。

* Number.isInteger()
  Number.isInteger()用来判断一个值是否为整数。需要注意的是，在 JavaScript 内部，整数和浮点数是同样的储存方法，所以 3 和 3.0 被视为同一个值

* Number.EPSILON
  ES6 在Number对象上面，新增一个极小的常量Number.EPSILON。根据规格，它表示 1 与大于 1 的最小浮点数之间的差。

*  Number.isSafeInteger()
  JavaScript 能够准确表示的整数范围在-2^53到2^53之间（不含两个端点），超过这个范围，无法精确表示这个值。ES6 引入了Number.MAX_SAFE_INTEGER和Number.MIN_SAFE_INTEGER这两个常量，用来表示这个范围的上下限。
  Number.isSafeInteger()则是用来判断一个整数是否落在这个范围之内。

---

## Math对象扩展

* Math.trunc()
  Math.trunc方法用于去除一个数的小数部分，返回整数部分

* Math.sign()
  Math.sign方法用来判断一个数到底是正数、负数、还是零。对于非数值，会先将其转换为数值。

* Math.cbrt()
  Math.cbrt方法用于计算一个数的立方根。

* Math.clz32()
  JavaScript 的整数使用 32 位二进制形式表示，Math.clz32方法返回一个数的 32 位无符号整数形式有多少个前导 0。

* Math.imul()
  Math.imul方法返回两个数以 32 位带符号整数形式相乘的结果，返回的也是一个 32 位的带符号整数。

* Math.fround()
  Math.fround 方法返回一个数的单精度浮点数形式。

* Math.hypot()
  Math.hypot方法返回所有参数的平方和的平方根。

* Math.signbit()
  Math.sign()用来判断一个值的正负，但是如果参数是-0，它会返回-0。

---

### 对数方法

*  Math.expm1():Math.expm1(x)返回 ex - 1，即Math.exp(x) - 1。
*  Math.log1p():Math.log1p(x)方法返回1 + x的自然对数，即Math.log(1 + x)。如果x小于-1，返回NaN
*  Math.log10():Math.log10(x)返回以 10 为底的x的对数。如果x小于 0，则返回 NaN。
*  Math.log2():Math.log2(x)返回以 2 为底的x的对数。如果x小于 0，则返回 NaN。

---

### 双曲函数方法

*  Math.sinh(x)  返回x的双曲正弦（hyperbolic sine）
*  Math.cosh(x) 返回x的双曲余弦（hyperbolic cosine）
*  Math.tanh(x) 返回x的双曲正切（hyperbolic tangent）
*  Math.asinh(x) 返回x的反双曲正弦（inverse hyperbolic sine）
*  Math.acosh(x) 返回x的反双曲余弦（inverse hyperbolic cosine）
*  Math.atanh(x) 返回x的反双曲正切（inverse hyperbolic tangent）

---

## Integer

JavaScript 所有数字都保存成 64 位浮点数，这决定了整数的精确程度只能到 53 个二进制位。大于这个范围的整数，JavaScript 是无法精确表示的，这使得 JavaScript 不适合进行科学和金融方面的精确计算。

现在有一个提案，引入了新的数据类型 Integer（整数），来解决这个问题。整数类型的数据只用来表示整数，没有位数的限制，任何位数的整数都可以精确表示。

为了与 Number 类型区别，Integer 类型的数据必须使用后缀n表示。

JavaScript 原生提供Integer对象，用来生成 Integer 类型的数值。转换规则基本与Number()一致。

```JavaScript
{
  1n+2n //3n

  typeof 123n //'integer'
}
```
