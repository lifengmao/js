# Canvas绘图
本文参考《JavaScript高级程序设计（第3版）》第15章。
## 基本用法

使用<canvas>元素时，必须先设置其width和height属性，指定可以绘图的区域大小。
```html
<canvas id="drawing" width = "200" height = "200" >A drawing of something.</canvas>
```
如果浏览器不支持<canvas>元素，则会显示标签中的文字，即A drawing of something.其长度和宽度也可以通过CSS或者JS进行设置.

要在画布上绘图，需要取得绘图上下文。
```javascript
var drawing = document.getElementById('drawing');

//确定浏览器支持canvas
if(drawing.getContext) {
  var context = drawing.getContext('2d');
  //更多代码
}
```
使用toDataURL()方法，可以导出在canvas元素上绘制的图像
```javascript
if(drawing.getContext) {
  //取得图像的数据URI
  var imageURI = drawing.toDataURL("image/png");
  //显示图像
  var image = document.createElement('img');
  image.src = imageURI;
  document.body.appendChild(image);
}
```

默认情况下，浏览器会将图像编码为PNG格式.

## 2D上下文

### 填充和描边

2D上下文的两种基本绘图操作是填充和描边。填充就是用指定的样式填充图形；描边就是在图形的边缘画线。操作的结果取决于两个属性：fillStyle和strokeStyle。

这两个属性的值可以是字符串、渐变对象或模式对象，而它们的默认值都是"#000000"。

```javascript
if(drawing.getContext) {
  var context = drawing.getContext('2d');
  context.strokeStyle = "red";
  context.fillStyle = "#0000ff";
}
```

### 绘制矩形

矩形是唯一一个可以在2D上下文中绘制的形状。与矩形相关的方法包括：
* fillRect()

```javascript
var context = getContext('2d');
//绘制红色矩形
context.fillStyle = "#ff0000";
context.fillRect(10,10,50,50);
//绘制半透明蓝色矩形
context.fillStyle = "rgba(0,0,255,0.5)";
context.fillRect(30,30,50,50);
```

* strokeRect()

```javascript
var context = drawing.getContext('2d');
//绘制红色描边矩形
context.strokeStyle = "#ff0000";
context.strokeRect(10,10,50,50);
//绘制半透明的蓝色描边矩形
context.strokeStyle = "rgba(0,0,255,.5)";
context.strokeRect(30,30,50,50);
```
* clearRect()
```javascript
var context = getContext('2d');
//绘制红色矩形
context.fillStyle = "#ff0000";
context.fillRect(10,10,50,50);
//绘制半透明蓝色矩形
context.fillStyle = "rgba(0,0,255,0.5)";
context.fillRect(30,30,50,50);
//在两个矩形的重叠之处清除一个小矩形
context.clearRect(40,40,10,10);
```
它们都接收四个参数:矩形的x坐标、y坐标、矩形的宽度和高度。

### 绘制路径

要绘制路径，首先必须要调用beginPath()方法，然后再通过调用下列方法来实际的绘制路径。

* arc(x,y,radius,startAngel,endAngel,counterclockwise):以(x,y)为圆心绘制一条弧线，半径为radius，起始和结束角度为startAngel和endAngel。counterclockwise表示是否按逆时针旋转.
* arcTo(x1,y1,x2,y2,radius): 从上一点开始绘制，到(x2,y2)为止，并且以给定的半径radius穿过(x1,y1)。
* bezierCurveTo(c1x,c1y,c2x,c2y,x,y):从上一点开始绘制，到(x,y)为止，并且以前面两个点为控制点.
* lineTo(x,y):从上一点开始绘制，到(x,y)为止。
* moveTo(x,y):将绘图游标移到(x,y)，不画线。
* quadraticCurveTo(cx,cy,x,y):从上一点开始绘制二次曲线，到（x，y）为止,并且以(cx,cy)为控制点。
* rect(x,y,width,height)

创建了路径后，如果想绘制一条连接到路径起点的线条，可以调用closePath()方法。路径完成后，可以调用fill()和stroke()方法对路径进行填充和描边。最后还可以调用clip()在路径上创建一个剪切区域。

```javascript
//绘制一个不带数字的时钟表盘

var drawing = document.getElementById("drawing");

if(drawing.getContext){
  var context = drawing.getContext('2d');

  //开始路径
  context.beginPath();
  //绘制外圆
  context.arc(100,100,99,0,2*Math.PI,false);

  //绘制内圆
  context.moveTo(194,100); //避免绘制出多余的线条
  context.arc(100,100,94,0,2*Math.PI,false);

  //绘制分针
  context.moveTo(100,100);
  context.lineTo(100,15);

  //绘制时针
  context.moveTo(100,100);
  context.lineTo(35,100);

  //描边路径
  context.stroke();
}
```

由于路径使用很频繁，所以有一个名为isPointInpath()方法，用于在路径结束前判断点是否在路径上。
```javascript
if(isPointInpath(100,100)){
  alert("Point(100,100) is in the path");
}
```

### 绘制文本

绘制文本主要有两个方法：fillText()和strokeText()。它们都接收四个参数：要绘制的字符串、x坐标、y坐标和可选的最大像素宽度。
这两个方法都以下列三个属性为基础：
* font:文本的样式、大小和字体。
* textAlign：表示文本的对齐方式。可能的值有：start、end、left、right和center。
* textBaseline:表示文本的基线。可能的值有：top、hanging、middle、alphabetic、ideographic和bottom。

```javascript
//在上面绘制的时钟表盘绘制数字12
context.font = "bold 14px Arial";
context.textAlign = "center";
context.textBaseline = "middle";
context.fillText("12",100,20);
```
### 变换

创建绘制上下文时，会以默认值初始化变换矩阵，在默认的变换矩阵下，所有的处理都按描述直接绘制。
可以通过如下方法修改变换矩阵：
* rotate(angle):围绕原点旋转图像angle弧度。
* scale(scaleX,scaleY):缩放图像，x方向乘以scaleX,y方向乘以scaleY。
* translate(x,y):将坐标原点移到(x,y)。
* transform(m1_1,m1_2,m2_1,m2_2,dx,dy)：直接修改变换矩阵，方式是乘以以下矩阵
  m1_1  m1_2 dx
  m2_1  m2_2 dy
  0     0     1
* setTransform(m1_1,m1_2,m2_1,m2_2,dx,dy):将变换矩阵重置为默认状态，然后再调用transform()。

```javascript
//绘制表盘
var context = getContext)('2d');

context.beginPath();
//绘制外圆
context.arc(100,100,99,0,2*Math.PI,false);
//绘制内圆
context.moveTo(194,100);
context.arc(100,100,94,0,2*Math.PI,false);

//变换原点
context.translate(100,100);
//旋转表针
context.rotate(1);
//绘制分针
//此时(0,0)即为原先的(100,100)
context.moveTo(0,0);
context.lineTo(0,-85);
// 绘制时针
context.moveTo(0,0);
context.lineTo(-65,0);
//描边路径
context.stroke();
```

save()方法可以将当时的所有设置保存进一个栈结构，然后可以对上下文进行其他修改。如果想要回到之前保存的设置时，可以调用restore()方法，恢复之前的状态.

```javascript
context.fillStyle = "#ff0000";
context.save();

context.fillStyle = "#00ff00";
context.translate(100,100);
context.save();

context.fillStyle = "#0000ff";
context.fillRect(0,0,100,200); //蓝色 (100,100)

context.restore();
context.fillRect(10,10,100,200);//绿色 (110,110)

context.restore();
context.fillRect(0,0,100,200); //红色 (0,0)
 ```
 save()方法保存的只是对绘图上下文的设置和变换，不会保存绘图上下文的内容。

### 绘制图像

 可以使用drawImage()方法将图像绘制到画布上
 drawImage()接收9个参数：要绘制的图像、源图像的x坐标、y坐标、源图像的宽度、高度、目标图像的x坐标、y坐标、目标图像的宽度、高度。

 ```javascript
 var image = document.images[0];
 context.drawImage(image,0,10,50,50,0,100,40,60);
 ```

### 阴影

2D上下文会根据以下几个属性的值，自动为形状或路径绘制出阴影。
* shadowColor:默认为黑色
* shadowOffsetX:x轴的偏移量，默认为0
* shadowOffsetY:y轴的偏移量，默认为0
* shadowBlur:模糊的像素数，默认为0

### 渐变

* 线性渐变 createLinearGradient()，接收四个参数：起点的x坐标，y坐标，终点的x坐标，y坐标
          然后使用addColorStop()来指定色标。接收两个参数：色标位置和CSS颜色值。

  ```javascript
  var gradient = createLinearGradient(30,30,70,70);
  gradient.addColorStop(0,"white");
  gradient.addColorStop(1,"black");

  context.fillStyle = gradient;
  context.fillRect(30,30,50,50);
  ```

* 径向渐变 createRadialGradient() 接收6个参数：起点圆的圆心和半径以及终点圆的圆心及半径。

  ```javascript
  var gradient = context.createRadialGradient(55,55,10,55,55,30);
  gradient.addColorStop(0,"white");
  gradient.addColorStop(1,"black");

  context.fillStyle = gradient;
  context.fillRect(30,30,50,50);
  ```

### 模式

模式其实就是重复的图像，可以用来填充或描边图形。
要创建模式，可以调用createPattern()方法，它接收两个参数：一个HTML<img>元素（或者<video>和另外一个<canvas>元素）和一个表示如何重复图像的字符串。

```javascript
var image = document.images[0],
    pattern = context.createPattern(image,"repeat");

//绘制矩形

context.fillStyle = pattern;
context.fillRect(10,10,150,150);
```
### 使用图像数据

2D上下文可以通过getImageData()来取得原始图像数据。它接收4个参数：要取得的数据的画面区域及该区域的像素宽度和高度。
每个ImageData对象都有三个属性：width,height,data
其中data属性是一个数组，保存着每一个像素的数据，分别为其红、绿、蓝和透明度值。

```javascript
//简单的灰阶过滤器
var drawing = document.getElementById('drawing');

if(drawing.getContext){
  var context = drawing.getContext("2d"),
      image = document.images[0],
      imageData,data,
      i,len,average,
      red,green,blue,alpha;

  //绘制原始图像
  context.drawImage(image,0,0);

  //取得图像数据
  imageData = context.getImageData(0,0,image.width,image.height);
  data = imageData.data;

  for(i = 0; i < data.length; i += 4){
    red = data[i];
    green = data[i+1];
    blue = data[i+2];
    alpha = data[i+3];

    //求得rgb平均值
    average = Math.floor((red+green+blue) / 3);
    //设置颜色值，透明度不变
    data[i] = average;
    data[i+1] = average;
    data[i+2] = average;

  }

  //回写图像数据并显示结果
  imageData.data = data;
  context.putImageData(imageData,0,0);
}
```
### 合成

有两个会应用到2D上下文中所有绘制操作的属性：globalAlpha和globalCompositionOperation
* globalAlpha:指定所有绘制的透明度
* globalCompositionOperation:表示后绘制的图形怎么与先绘制的图形结合

---

## WebGL

WebGL是针对Canvas的3D上下文，它并不是W3C标准。因为其还是一个正在制定和发展中的规范，目前只适合实验性教学，不适合真正开发。
内容待以后补充。
