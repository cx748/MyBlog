## 绘制矩形
```js
var canvas = document.getElementById('');
var ctx = canvas.getContext("2d");

ctx.fillRect(x,y,width,height) //填充矩形
ctx.strokeRect(x,y,width,height) //轮廓填充
ctx.clearRect(x, y, width, height) //清楚矩形区域
```
## 绘制路径
1. 创建路径起始点
2. 画图命令画出路径
3. 把路径闭合
4. 路径生成之后，可以通过描边或者填充来 渲染路径
```js
ctx.beginPath() //新建路径 
ctx.closePath() // 闭合路径
ctx.stroke() //通过线条来绘制轮廓
ctx.fill() //通过填充路径的内容区域生成实心的图形

moveTo(x,y) //将笔触移动到指定的x以及y上
lineTo(x,y)

ctx.arc(x, y, radius, startAngle, endAngle, anticlockwise) //画一个以（x,y）为圆心的以radius为半径的圆弧（圆），从startAngle开始到endAngle结束，按照anticlockwise给定的方向（默认为顺时针）来生成。

quadraticCurveTo(cp1x, cp1y, x, y)
//绘制二次贝塞尔曲线，cp1x,cp1y为一个控制点，x,y为结束点。
bezierCurveTo(cp1x, cp1y, cp2x, cp2y, x, y)
//绘制三次贝塞尔曲线，cp1x,cp1y为控制点一，cp2x,cp2y为控制点二，x,y为结束点。
```
## 线形
lineWidth = value 设置线条宽度。  
lineCap = type
设置线条末端样式。  
lineJoin = type
设定线条与线条间接合处的样式。  
miterLimit = value
限制当两条线相交时交接处最大长度；所谓交接处长度（斜接长度）是指线条交接处内角顶点到外角顶点的长度。  
getLineDash()
返回一个包含当前虚线样式，长度为非负偶数的数组。  
setLineDash(segments)
设置当前虚线样式。  
lineDashOffset = value
设置虚线样式的起始偏移量。  
## 渐变 Gradients
```js
function draw() {
  var ctx = document.getElementById('canvas').getContext('2d');

  // Create gradients
  var lingrad = ctx.createLinearGradient(0,0,0,150);
  lingrad.addColorStop(0, '#00ABEB');
  lingrad.addColorStop(0.5, '#fff');
  lingrad.addColorStop(0.5, '#26C000');
  lingrad.addColorStop(1, '#fff');

  var lingrad2 = ctx.createLinearGradient(0,50,0,95);
  lingrad2.addColorStop(0.5, '#000');
  lingrad2.addColorStop(1, 'rgba(0,0,0,0)');

  // assign gradients to fill and stroke styles
  ctx.fillStyle = lingrad;
  ctx.strokeStyle = lingrad2;
  
  // draw shapes
  ctx.fillRect(10,10,130,130);
  ctx.strokeRect(50,50,50,50);
}
```

![adatar](https://developer.mozilla.org/@api/deki/files/87/=Canvas_lineargradient.png)

## 图案样式
```js
ctx.createPatten(img,type)//type取值为repeat repeat-x repeat-y no-repeat
```
## 阴影
```js
function draw(){
  var ctx=document.getElementById('canvas').getContext('2d');
  ctx.shadowOffsetX = 2; //向右偏移
  ctx.shadowOffsetY = 2; //向下偏移
  ctx.shadowBlur = 2; //模糊
  ctx.shadowColor = "rgba(0,0,0,0.5)";

  ctx.font = "20px times new roman";
  ctx.fillStyle= "black";
  ctx.fillText = ("sample string",5,30);
}
```

# 使用图像
引入图像到canvas主要有两个步骤  
1. 获得一个图片源或者canvas元素
2. 使用drawImage()函数将图片绘在画布上

## 获得图片
canvas的API可以使用下面类型中的一种作为图片的源
1. HTMLImageElement：这些图片是有由Image函数构造出来的，或者任何的\<img>元素。
2. HTMLVideoElement元素：用一个HTML的\<Video>元素作为图片源，可是从视频中抓取当前帧作为一个图像
3. HTMLCanvasElement元素，可以使用另外一个\<canvas>作为图片源
4. ImageBitmap：这是一个高性能的位图，可以低延迟地绘制，它可以从上述的所有源以及其它几种源中生成

这些源统一由 CanvasImageSource类型来引用。

## 绘制图片
drawImage(image, x, y)  
其中 image 是 image 或者 canvas 对象，x 和 y 是其在目标 canvas 里的起始坐标.  
drawImage(image,x,y,wdith,height)  
这个方法多了2个参数：width 和 height，这两个参数用来控制 当向canvas画入时应该缩放的大小.  
drawImage(image, sx, sy, sWidth, sHeight, dx, dy, dWidth, dHeight)  
第一个参数和其它的是相同的，都是一个图像或者另一个 canvas 的引用。其它8个参数参照右图解，前4个是定义图像源的切片位置和大小，后4个则是定义切片的目标显示位置和大小。
![](https://developer.mozilla.org/@api/deki/files/79/=Canvas_drawimage.jpg)

