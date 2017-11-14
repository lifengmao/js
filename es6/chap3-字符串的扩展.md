# 字符串的扩展

---

## 字符的Unicode表示法

在js中，我们可以采用Unicode码点的形式来表示一个字符，但是在ES5中如果码值超出了范围（\u0000~\uFFFF），则必须用两个双字节的形式表示。

在ES6中，对这一点进行了改进，只要将码点放入大括号，就能正确解读该字符。

```javascript
{
  "\u{20BB7}"
  // "𠮷" 在es5中为“7”

  "\u{41}\u{42}\u{43}"
  // "ABC"

  let hello = 123;
  hell\u{6F} // 123

  '\u{1F680}' === '\uD83D\uDE80'
  // true
}
```
---

## codePointAt()

在js内部，字符以utf-16的格式存储，每个字符固定位两个字节。如果碰到某些需要以4个字节储存的字符，js会认为它们是两个字符。在ES6之前，对于这种4个字节的字符，js不能正确处理。

ES6提供了codePointAt方法，能够正确处理4个字节储存的字符，返回一个字符的码点。

codePointAt(index):接收字符在字符串中的位置为参数。返回码点的十进制。

```javascript
{
  let s = '𠮷a';//注意'𠮷'不是'吉'

  s.codePointAt(0);//134071
  s.codePointAt(1);//57271
  s.codePointAt(2);//97
}
```
codePointAt方法是测试一个字符是由两个字节还是四个字节组成的最简单方法。
```javascript
{
  function is32Bit(c) {
    return c.codePointAt(0) > 0xFFFF;
  }

  is32Bit("𠮷");//true
  is32Bit("a");//false
}
```

---

## String.fromCodePoint()

ES5提供了String.fromCharCode方法，用于从码点返回对应字符，但是这个方法同样不能识别32位的utf-16字符。

ES6提供了String.fromCodePoint方法，可以识别大于0xFFFF的字符。起作用与codePointAt正好相反。

```javascript
{
  String.fromCodePoint(0x20BB7) //"𠮷"

  String.fromCodePoint(0x78,0x1f680,0x79) =='x\uD83D\uDE80y' //true
}
```

---

## 一些常用的方法

### for...of

ES6为字符串添加了遍历器接口，因此可以被for...of循环遍历，其最大优点是可以识别大于0xFFFF的码点，传统的for循环就无法识别。

```javascript
{
  let text = String.fromCodePoint(0x20BB7);

  for (let i = 0; i < text.length; i++) {
    console.log(text[i]);
  }
  // " "
  // " "

  for (let i of text) {
    console.log(i);
  }
  // "𠮷"
}
```

### at()

ES5中的chatAt()方法，返回字符串给定位置的字符，但是同样不能识别超出界限的字符，因此，目前有一个提案，提出了at方法，它可以识别那些超出界限的字符。

```javascript
{
  //该方法目前需要polyfill实现
  'abc'.at(0) //"a"
  '𠮷'.at(0) //"𠮷"
}
```
---

### normalize()

ES6 提供字符串实例的normalize()方法，用来将字符的不同表示方法统一为同样的形式，这称为 Unicode 正规化。

normalize方法可以接受一个参数来指定normalize的方式，参数的四个可选值如下。

- NFC，默认参数，表示“标准等价合成”（Normalization Form Canonical Composition），返回多个简单字符的合成字符。所谓“标准等价”指的是视觉和语义上的等价。
- NFD，表示“标准等价分解”（Normalization Form Canonical Decomposition），即在标准等价的前提下，返回合成字符分解的多个简单字符。
- NFKC，表示“兼容等价合成”（Normalization Form Compatibility Composition），返回合成字符。所谓“兼容等价”指的是语义上存在等价，但视觉上不等价，比如“囍”和“喜喜”。（这只是用来举例，normalize方法不能识别中文。）
- NFKD，表示“兼容等价分解”（Normalization Form Compatibility Decomposition），即在兼容等价的前提下，返回合成字符分解的多个简单字符。

---

### includes() startsWith() endsWith()

* includes():返回布尔值，表示是否找到了参数字符串
* startsWith:返回布尔值，表示参数字符串是否在原字符串的头部、
* endsWith(): 返回布尔值，表示参数字符串是否在元字符串的尾部

这三个方法都支持第二个参数，表示开始搜索的位置。

---

### repeat()

repeat方法返回一个新字符串，表示将原字符串重复n次。

---

### padStart(),padEnd()

ES2017引入了字符串补全长度的功能。padStart用于头部补齐，padEnd用于尾部补全。

---

## 模板字符串

模板字符串是增强版的字符串，用反引号(位于tab键上方)标识。如果使用模板字符串表示多行字符串，所有的空格和缩进都会被保留在输出之中。模板字符串中嵌入变量，需要将变量名写在${}之中。

```javascript
  {
    let name = "list";
    let info = "hello world";
    // 模板字符串
    let m = `i am ${name},${info}`;
    console.log(m);
  }
  {
    // 标签模板
    let user = {
        name:'list',
        info:'hello world'
    };
    abc`i am ${user.name},${user.info}`;
    function abc(s,v1,v2) {
        console.log(s+v1+v2); //i am ,,,listhello world
    }
  }
  ```
