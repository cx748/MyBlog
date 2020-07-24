# 学习与实战过程中遇到的CSS问题

## 导航栏在页面变化时，格式发生了错误
网页放大会将窗口大小缩小相应的放大倍数；
网页缩小会将窗口大小放大相应的缩小倍数的倒数分之一。
网页缩放时，有定长宽的元素不会受到缩放的影响；
如果元素有设置max-width或者min-width，缩放到一定程度是该元素就不会受到缩放倍数的影响（放大时页面会产生滚动条）；
如果页面是百分比布局，没有设置max-width或者min-width，窗口不会产生滚动条

## 盒模型的两种模式
标准模式：box-sizing: content-box(默认)；宽高不包括内边距和外边距
怪异模式：box-border；宽高包括内边距和外边距

## 清除浮动
一是利用 clear 属性，包括在浮动元素末尾添加一个带有 clear: both 属性的空 div 来闭合元素，其实利用 :after 伪元素的方法也是在元素末尾添加一个内容为一个点并带有 clear: both 属性的元素实现的。

二是触发浮动元素父元素的BFC，使到该父元素可以包含浮动元素，关于这一点

# CSS3 特性
## 选择器
### 新增选择器
-:first-child: 选取属于父元素的首个子元素的指定选择器
-:last-child: 选取属于父元素的最后一个元素的指定选择器
-:nth-child: 匹配属于父元素的第n个子元素，不论元素类型
-:nth-last-child：匹配属于父元素的倒数第n个子元素
#### 目标伪类选择器
:target 选择器用于获取当前活动的目标元素
#### 属性选择器
div[class^=font] {
  color: pink
}
### 伪元素选择器
E::first-letter 文本的第一个单词或字
E::first-line 文本第一行
E::selection 选中中的文本
### 伪类与伪元素的区别
CSS3规范中，':'用来表示伪类，'::'用来表示伪元素
根本区别在于他们是否创造了新的元素
* 伪类：用于向某些选择器添加特殊的效果
```css
:last-child
:first-child
a:link { color: #FF0000}
a:visted {color: #FFF000} /*已访问的链接*/
a:hover {color: #FF00FF}
a:active{color: #FFFFFF} /* 选定的链接*/
```
* 伪元素：创建了html中不存在的元素，用于将特殊的效果添加到某些选择器
```css
::before {} /*选择器再被选元素的前面插入内容和定义css，content属性指定要出人的内容*/
::after {} /*选择器在被选元素的后面插入内容和定义css*/
```
## 背景和边框
### 背景
background-size: 规定背景图片的尺寸(cover:填充，100% 100%：拉伸)  
background-origin: 规定图片的定位区域，content-box，padding-box，border-box
### 边框
border-radius  
box-shadow/text-shadow
border-image

## 文本效果
* text-shadow 文本阴影
* text-justify 文本对齐方式
* text-wrap 文本的换行规则
* text-outline 规定文本的轮廓
* text-overflow 规定文本溢出包含元素时发生的事情
* text-decoration 文本修饰符：overline，line-through，underline分别是上划线，中划线，下划线

## 渐变
* linear-gradient 线性渐变
* radial-gradient 径向渐变

## 2D与3D转换
### 2D转换(transform)
1. translate(): 元素从当前位置移动，根据给定的位置参数x，y。transform: translate(40px,40px)
2. rotate(): 元素顺时针旋转给定的角度，负值则逆时针旋转。transform: rotate(30deg)
3. scale(): 元素的尺寸的增加或者减少。transform: scale(2,3)
4. skew(): 元素翻转给定的角度，根据给定的水平线与垂直线参数 transform: skew(30deg,20deg)
### 3D转换
1. rotateX(): 元素围绕其X轴以给定的度数进行旋转。
2. rotateY(): 元素围绕其X轴以给定的度数进行旋转。
3. perspective: 规定3D的透视效果

## 动画、过渡
### 过渡(transition)
1. transition-property: 执行动画对应的属性，如color，background
2. transition-duration：过渡动画的一个持续时间
3. transition-delay：延迟多久开始动画
4. transition-timeing-function：持续时间内动画变化的速率，常见的有ease/linear/ease-in/ease-out/cubic-bezier
### 动画(animation)