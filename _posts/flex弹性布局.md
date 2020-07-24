# Display
display属性 指网页的布局，主要有两个重要的布局方式：flex和grid
## 弹性布局 flex
布局的传统解决方案是基于盒状模型，依赖display属性 + position属性 + float属性。这种解决方案对于那些特殊的布局非常不方便，比如垂直居中。

## flex语法
Flex 是 flexible box的缩写，意思是弹性布局，用来为盒状模型提供更大的灵活性。
### 概念
采用flex布局的元素，称为flex容器，它的所有子元素成为容器成员，成为flex项目(flex item)，简称项目。
![avatar](http://www.ruanyifeng.com/blogimg/asset/2015/bg2015071004.png)
容器默认存在两个轴，main axis--主轴， cross axis交叉轴。项目默认沿主轴排列。单个项目占据的主轴空间叫做main size，占据的交叉轴的空间叫做cross-size。
### 容器的属性
* flex-direction
* flex-warp
* flex-flow
* justify-content
* align-items
* align-content

#### flex-direction
flex-direction属性决定主轴的方向。

```css
.box{
    flex-direction: row | row-reverse | column | column-reverse;
}
```
* row(默认值)：主轴为水平方向，起点在左端。
* row-reverse: 主轴为水平方向，起点在右端。
* column：主轴为垂直方向，起点在上沿。
* column-reverse：主轴为垂直方向，起点在下沿。
### flex-wrap
默认情况下，项目是沿着轴线排列，flex-wrap属性决定在一条轴线排不下的情况下如何换行。
```css
.box{
    flex-wrap: nowrap | wrap | wrap-reverse;
}
```
* nowrap(默认)：不换行

![avatar](http://www.ruanyifeng.com/blogimg/asset/2015/bg2015071007.png)
* wrap：换行，第一行在上方

![avatar](http://www.ruanyifeng.com/blogimg/asset/2015/bg2015071008.jpg)
* wrap-reverse：换行，第一行在下方

![avatar](http://www.ruanyifeng.com/blogimg/asset/2015/bg2015071009.jpg)

### flex-flow
flex-flow是属性flex-direction与属性flex-flow的简写，默认值是row nowrap

### justify-content
justify-content定义了项目在主轴上的排列方式
```css
.box{
    justify-content: flex-start|flex-end|center|space-between|space-around;
}
```
![avatar](http://www.ruanyifeng.com/blogimg/asset/2015/bg2015071010.png)
它可能取5个值，具体的对齐方式与主轴有关，假设主轴是从左到右。
* flex-start(默认值): 左对齐。
* flex-end：右对齐 
* center：居中对齐
* space-between：两端对齐，项目之间的间隔相等。
* space-around：每个项目两侧的间隔相等。

### align-item
align-item属性定义在交叉轴上的对齐方式。
```css
.box{
    align-item: flex-start|flex-end|center|baseline|stretch;
}
```
![avatar](http://www.ruanyifeng.com/blogimg/asset/2015/bg2015071012.png)

stretch(默认值)：轴线占满整个交叉轴

### align-content
align-content属性定义了多根轴线的对齐方式，如果只有一根轴线则补齐作用。

## 项目的属性
以下的6个属性设置在项目上
* order
* flex-grow
* flex-shrink
* flex-basis
* flex
* align-self

### order
order属性定义项目的顺序。数值越小越靠前
```css
.item{
    order: <integer>;
}
```
### flex-grow
flex-grow属性定义项目放大比例，默认为0，既如果存在剩余空间，也不放大。
```css
.item{
    flex-grow: <number> /*默认为0，不放大*/
}
```
![avatar](http://www.ruanyifeng.com/blogimg/asset/2015/bg2015071014.png)
如果所有项目的flex-grow属性都为1，则他们将等分剩余空间。如果一个项目的flex-grow属性为2，其他的项目都为1，则这个项目分得的空间是其他项目的两倍。

### flex-shrink
flex-shrink 属性定义了项目的缩小比例，默认为1，即如果空间不足，该项目将缩小。
```css
.item{
    flex-shrink: <number>/*默认为1，项目会在空间不足的时候缩小*/
}
```
![avatar](http://www.ruanyifeng.com/blogimg/asset/2015/bg2015071015.jpg)

如果所有的项目item的flex-shrink属性值都是1，当空间不足时都会等比例缩小。如果一个项目的flex-shrink的值是0，其他项目的属性值是1，则空间不足时，前者不缩小。

### flex-basis
flex-basis属性定义了在分配多余空间之前，项目占据的主轴空间(main size)。浏览器根据这个属性，计算主轴是否有多余的空间。它的默认值是auto，就是项目本来的大小。
```css
.item{
    flex-basis: <length> /* 默认值是auto*/
}
```
### flex
flex属性是flex-grow，flex-shrink，flex-basis的简写，默认值是 0 1 auto。后两个属性值可选
```css
.item{
    flex: none | auto | [ <'flex-grow'> <'flex-shrink'>? || <'flex-basis'> ];
}
```
该属性有两个快捷值，auto(1 1 auto) 和 none(0 0 auto)。
优先使用flex属性，没必要分开写成三个属性。

### align-self
align-self属性允许单个项目与其他项目不一样的对齐方式，可覆盖align-items属性。默认值为auto，表示可继承父元素的align-item属性，如果没有父元素，则等同于stretch。