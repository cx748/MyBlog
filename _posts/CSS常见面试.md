# 水平垂直居中
## 居中元素高宽是固定的
* absolute + 负margin  
绝对定位的百分比是相对于父元素的宽高，通过这个特性可以让子元素的居中显示，但绝对定位是基于子元素的左上角，期望的效果是子元素的中心居中显示
为了修正这个问题，可以借助外边距的负值，负的外边距可以让元素向相反方向定位，通过指定子元素的外边距为子元素宽度一半的负值，就可以让子元素居中了
```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Document</title>
  </head>
  <body>
    <div class="wp">
      <div class="box size">123123</div>
    </div>;
    <style>
      /* 公共代码 */
      .wp {
        border: 1px solid red;
        width: 300px;
        height: 300px;
      }

      .box {
        background: green;
      }

      .box.size {
        width: 100px;
        height: 100px;
      }
      /* 公共代码 */
      .wp{
        position: relative;
      }
      .box{
        position: absolute;
        left: 50%;
        top:50%;
        margin-top: -50px;
        margin-left: -50px;
      }
    </style>
    <script>
    </script>
  </body>
</html>
```


* absolute + margin auto  
将absolute的top,right,left,bottom都设置为0，然后margin：auto。
```css
      /* 公共代码 */
      .wp {
        border: 1px solid red;
        width: 300px;
        height: 300px;
      }

      .box {
        background: green;
      }

      .box.size {
        width: 100px;
        height: 100px;
      }
      /* 公共代码 */
      .wp{
        position: relative;
      }
      .box{
        position: absolute;
        top: 0;
        left: 0;
        right:0;
        bottom: 0;
        margin: auto;
      }
```

* absolute + calc
css3带来了计算属性，可以在top和left的基础上，减去一半的长宽，就能水平垂直居中了
```css
.wp {
    position: relative;
}
.box {
    position: absolute;;
    top: calc(50% - 50px);
    left: calc(50% - 50px);
}
```
## 居中元素不定宽高
* absolute + transform
修复绝对定位的问题，还可以使用css3新增的transform，transform的translate属性也可以设置百分比，其是相对于自身的宽和高，所以可以讲translate设置为-50%，就可以做到居中了，代码如下
```css
.wp{
    position: relative;
}

.box{
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%,-50%);
}
```

* flex
```css
.wp{
    display: flex;
    justify-content:center;
    align-items: center;
}
```
# css实现div宽度相等
```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Document</title>
  </head>
  <body>
    <div class="box">
      <div class="left">
      </div>
      <div class="middle"></div>
      <div class="right"></div>
    </div>
    <style>
      .box{
        height: 600px;
        width: 960px;
        overflow: hidden;
      }
      .left{
        width: 300px;
        height: 300px;
        background-color: red;
        float: left;
        margin: 0;
        padding: 0;
      }
      .middle{
        width: 300px;
        height: 300px;
        background-color: blue;
        float: left;
        margin: 0;
        padding: 0;
      }
      .right{
        width: 300px;
        height: 300px;
        background-color: orange;
        float:left;
        margin: 0;
        padding: 0;
      }
    </style>
    <script>
    </script>
  </body>
</html>
```

# css实现三栏布局
1. float
2. position
3. flex

# display常见属性
1. display: block. block元素独占一行，可以设置width，height，margin，padding等属性。
2. display: none. 将元素设置为none之后，这个元素和他的子元素都会从普通文本流中移除，页面的整个元素内容也会消失。
3. display: inline. inline元素不会独占一行，多个相邻的行内元素会排列在同一行，排不下去时会换行。width和height属性无效，元素的高度取决于这一行的内联盒子的最高行高。水平方向的padding和margin会有效果，但是竖直方向的padding和margin无效。
4. display: inline-block. inline-block的元素既是排成一列，也能设置width，height，margin和padding等属性。

# position属性常见属性
1. position：static. position的默认属性，不会对于这个元素的布局有影响，它会在他本来的位置。
2. positon： absolute. 绝对定位 对象脱离正常文本流，没有父元素，上下左右的设置是针对于浏览器窗口，会随着页面窗口大小一起改变。
3. position: relative. 相对定位 对象遵循正常文本流，将依据上下左右属性对设置为absolute的父元素进行偏移。
4. position: fixed. 固定定位 对象脱离正常文本流。使用上下左右等属性以浏览器窗口为参考点进行定位。当出现滚动条时，对象不会随着滚动
5. position: inherit. 从父元素继承position的属性
6. position：sticky. 粘性定位 在目标区域以内，它的行为就像 position:relative;在滑动过程中，某个元素距离其父元素的距离达到sticky粘性定位的要求时(比如top：100px)；position:sticky这时的效果相当于fixed定位，固定到适当位置。