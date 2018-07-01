---
layout: post
title: HTML-CSS-JS 基础知识
date: 2018-07-01
tag: JavaScript
---

html css javascript 基础回顾
===========================================================================

### HTML & CSS 部分

- HTML

lable 标签有什么用
label主要作用于表单填写，当用点击了标签，就会聚焦到绑定的输入框
eg:
```
<label for="Name">username</lable>
<input type="text" name="Name" id="Name" />
```

如何进行跨页面访问或者说跨标签访问
主要通过localStorge、sessionStorage来进行通信

b和strong标签的区别
b和strong都是进行对元素进行加粗显示，b是个实体标签，会对所包含的字体设为bold粗体，是html语言的
strong则是逻辑标签，作用是加强语气而加粗字体，是xhtml的，主要是强调作用，更加语义化。


- css常用布局

CSS 选择符有哪些？哪些属性可以继承？优先级算法如何计算？ CSS3新增伪类有那些？
CSS 选择符：
1 id选择器(#myid)
2 类选择器(.myclassname)
3 标签选择器(div, h1, p)
4 相邻选择器(h1 + p)
5 子选择器(ul > li)
6 后代选择器(li a)
7 通配符选择器(\*)
8 属性选择器(a[rel="external"])
9 伪类选择器(a: hover, li:nth-child)

可继承的样式
1 font-size
2 font-family
3 color
4 text-indent

不可继承样式
1 border
2 padding
3 margin
4 width
5 height

优先级算法
1 同权重情况下样式定义最近者为准
2 载入样式以最后载入的定位为准
3 !important > id > class > tag
4 !important > 内联样式 > id > class

CSS3新增伪类：
p:first-of-type 选择属于其父元素的首个 <p> 元素的每个 <p> 元素

p:last-of-type  选择属于其父元素的最后 <p> 元素的每个 <p> 元素

p:only-of-type  选择属于其父元素唯一的 <p> 元素的每个 <p> 元素

p:only-child    选择属于其父元素的唯一子元素的每个 <p> 元素

p:nth-child(2)  选择属于其父元素的第二个子元素的每个 <p> 元素

:enabled :disabled 控制表单控件的禁用状态

:checked        单选框或复选框被选中

CSS 实现正方形等比放大缩小
```
div {
  width: 1vw;
  height: 1vw;
  background: red;
}

div {
  padding-bottom: 100%;
  background: red;
}
```

CSS 3 新增属性
1 css 边框 (border: 完整为border-top-left,
border-top-right, border-bottom-right,
border-bottom-left; border-radius: border-top-left-radius,
border-top-right-radius, border-bottom-right, border-bottom-left-radius)

2 边框阴影 (box-shadow)
语法 {boxf-shadow: [inset] x-offset y-offset blur-radius extension-radius spread-radiuscolor}
```
div{
  /* 内阴影，向右偏移10px，向下偏移10px，模糊半径5px，阴影缩小10px */
  box-shadow: inset 10px 10px 5px -10px #888888;
}

阴影类型：此参数可选。如不设值，默认投影方式是外阴影；如取其唯一值inset ，其投影为内阴影；

X-offset:阴影水平偏移量，其值可以是正负值。如果值为正值，则阴影在对象的右边，其值为负值时，阴影在对象的左边；

Y-offset:阴影垂直偏移量，其值也可以是正负值。如果为正值，阴影在对象的底部，其值为负值时，阴影在对象的顶部；

Blur-radius:阴影模糊半径：此参数可选，但其值只能是为正值，如果其值为0时，表示阴影不具有模糊效果，其值越大阴影的边缘就越模糊；

Extension-radius阴影扩展半径：此参数可选，其值可以是正负值，如果值为正，则整个阴影都延展扩大，反之值为负值时，则缩小；

阴影颜色：此参数可选。如不设定颜色，浏览器会取默认色，但各浏览器默认取色不一致，特别是在webkit内核下的safari和chrome浏览器下表现为透明色，在Firefox/Opera下表现为黑色（已验证），建议不要省略此参数。
```

3 边框图片
```
border-image : border-image-source || border-image-slice [ / border-image-width] || border-image-repeat
```

4 CSS3 背景

background-size
在 CSS3 之前，背景图片的尺寸是由图片的实际尺寸决定的。在 CSS3 中，可以设置背景图片的尺寸，这就允许我们在不同的环境中重复使用背景图片。可以像素或百分比规定尺寸。如果以百分比规定尺寸，那么尺寸相对于父元素的宽度和高度
```
div{
  background:url(bg_flower.gif);
  /* 通过像素规定尺寸 */
  background-size:63px 100px;

  /* 通过百分比规定尺寸 */
  background-size:100% 50%;
  background-repeat:no-repeat;
}
```

background-origin
规定背景图片的定位区域，背景图片可以放置于 content-box、padding-box 或 border-box 区域
```
div{
  background:url(bg_flower.gif);
  background-repeat:no-repeat;
  background-size:100% 100%;
  /* 规定背景图片的定位区域 */
  background-origin:content-box;
}
```

background-clip
与background-origin 属性相似，规定背景颜色的绘制区域，区域划分与background-origin 属性相同
```
div{
  background-color:yellow;
  background-clip:content-box;
}
```

CSS3 多重背景图片
```
body{
  background-image:url(bg_flower.gif),url(bg_flower_2.gif);
}
```

5 CSS3 文本效果
给为本添加阴影，能够设置水平阴影、垂直阴影、模糊距离，以及阴影的颜色。

text-shadow
```
h1{
  text-shadow: 5px 5px 5px #FF0000;
}
```

text-wrap 属性
```
text-wrap: normal | none | unrestricted | suppress | break-word

p {word-wrap: break-word;}
```

6 CSS3 字体
```
/* 定义字体 */
@font-face{
  font-family: myFont;
  src: url('Sansation_Light.ttf'),
       url('Sansation_Light.eot');     /* IE9+ */
}

div{
  font-family:myFont;
}
```

7 CSS3 2D 转换

通过 CSS3 转换，我们能够对元素进行移动、缩放、转动、拉长或拉伸，转换是使元素改变形状、尺寸和位置的一种效果。

translate() 方法(移动)
```
div{
  transform: translate(50px,100px);
  -ms-transform: translate(50px,100px);        /* IE 9 */
  -webkit-transform: translate(50px,100px);     /* Safari and Chrome */
  -o-transform: translate(50px,100px);         /* Opera */
  -moz-transform: translate(50px,100px);        /* Firefox */
}
```

rotate (旋转,正值顺时针,负值逆时针)
```
div{
  transform: rotate(30deg);
  -ms-transform: rotate(30deg);        /* IE 9 */
  -webkit-transform: rotate(30deg);    /* Safari and Chrome */
  -o-transform: rotate(30deg);         /* Opera */
  -moz-transform: rotate(30deg);       /* Firefox */
}
```

scale() 方法 （缩放）
```
div{
  transform: scale(2,4);
  -ms-transform: scale(2,4);         /* IE 9 */
  -webkit-transform: scale(2,4);     /* Safari 和 Chrome */
  -o-transform: scale(2,4);         /* Opera */
  -moz-transform: scale(2,4);       /* Firefox */
}
```

skew() （扭曲）
```
/* 设置围绕 X 轴把元素翻转 30 度，围绕 Y 轴翻转 20 度。 */
div{
  transform: skew(30deg,20deg);
  -ms-transform: skew(30deg,20deg);         /* IE 9 */
  -webkit-transform: skew(30deg,20deg);     /* Safari and Chrome */
  -o-transform: skew(30deg,20deg);          /* Opera */
  -moz-transform: skew(30deg,20deg);        /* Firefox */
}
```

matrix()

matrix() 方法把所有 2D 转换方法组合在一起。matrix() 方法需要六个参数，包含数学函数，允许旋转、缩放、移动以及倾斜元素。

```
/* 使用 matrix 方法将 div 元素旋转 30 度 */
div{
  transform:matrix(0.866,0.5,-0.5,0.866,0,0);
  -ms-transform:matrix(0.866,0.5,-0.5,0.866,0,0);          /* IE 9 */
  -moz-transform:matrix(0.866,0.5,-0.5,0.866,0,0);         /* Firefox */
  -webkit-transform:matrix(0.866,0.5,-0.5,0.866,0,0);      /* Safari and Chrome */
  -o-transform:matrix(0.866,0.5,-0.5,0.866,0,0);           /* Opera */
}
```

8 CSS3 3D 转换

CSS3 允许使用 3D 转换来对元素进行格式化

rotateX() rotateY()

```
/* 设置元素围绕其 X 轴以给定的度数进行旋转 */
div{
  transform: rotateX(120deg);
  -webkit-transform: rotateX(120deg);   /* Safari 和 Chrome */
  -moz-transform: rotateX(120deg);  /* Firefox */
}

/* 设置元素围绕其 Y 轴以给定的度数进行旋转 */
div{
  transform: rotateY(130deg);
  -webkit-transform: rotateY(130deg);   /* Safari 和 Chrome */
  -moz-transform: rotateY(130deg);  /* Firefox */
}
```

9 CSS3 过度

通过 CSS3可以在不使用 Flash 动画或 JavaScript 的情况下，当元素从一种样式变换为另一种样式时为元素添加效果。

```
/* 设置将变化效果添加在“宽度”上，时长为2秒；该时长在其他属性上并不适用 */
div{
  transition: width 2s;
  -moz-transition: width 2s;         /* Firefox 4 */
  -webkit-transition: width 2s;      /* Safari 和 Chrome */
  -o-transition: width 2s;           /* Opera */
}
/* 配合在一起使用的效果就是当鼠标移上去的时候宽度变为300px，这个过程耗时2秒 */
div:hover{
  width:300px;
}

|    属性     | 描述    |
| --------   | -----:   |
| transition | 简写属性，用于在一个属性中设置四个过渡属性   |
| transition-property        | 规定应用过渡的 CSS 属性的名称。      |
| transition-duration        | 定义过渡效果花费的时间。默认是 0。      |
| transition-timing-function | 规定过渡效果的时间曲线。默认是 "ease"。|
|transition-delay | 规定过渡效果何时开始。默认是 0。 |
```

9 CSS3 动画

通过 CSS3可以创建动画，这些动画可以取代网页中的画图片、Flash 动画以及 JavaScript。

```
/* 通过@keyframes 创建动画 */
@keyframes myfirst{
  from {background: red;}
  to {background: yellow;}
}
/* Firefox */
@-moz-keyframes myfirst {
  from {background: red;}
  to {background: yellow;}
}
/* Safari 和 Chrome */
@-webkit-keyframes myfirst {
  from {background: red;}
  to {background: yellow;}
}
/* Opera */
@-o-keyframes myfirst {
  from {background: red;}
  to {background: yellow;}
}

/*
   将创建的动画绑定到选择器，并至少指定以下两项 CSS3 动画属性
   1.指定动画的名称；
   2.指定动画的时长；
*/
div{
  animation: myfirst 5s;
  -moz-animation: myfirst 5s;       /* Firefox */
  -webkit-animation: myfirst 5s;    /* Safari 和 Chrome */
  -o-animation: myfirst 5s;         /* Opera */
}
```

| 属性  | 描述 |
| ----- | ----- |
| @keyframes  | 规定动画。 |
| animation | 所有动画属性的简写属性，| 除了 animation-play-state 属性。|
| animation-name | 规定 @keyframes 动画的名称。|
|animation-duration | 规定动画完成一个周期所花费的秒或毫秒。默认是 0。|
| animation-timing-function | 规定动画的速度曲线。默认是 "ease"。|
| animation-delay | 规定动画何时开始。默认是 0。|
| animation-iteration-count | 规定动画被播放的次数。默认是 1。|
| animation-direction | 规定动画是否在下一周期逆向地播放。默认是 "normal"。|
| animation-play-state  | 规定动画是否正在运行或暂停。默认是 "running"。|
| animation-fill-mode | 规定对象动画时间之外的状态。|

10 CSS3 多列
```
/* 将div中的文本分为3列 */
div{
  column-count:3;
  -moz-column-count:3;        /* Firefox */
  -webkit-column-count:3;     /* Safari 和 Chrome */
}
```

11 CSS3 用户界面

CSS3 resize
```
/* 设置div可以由用户调整大小 */
div{
  resize:both;
  overflow:auto;
}
```

CSS3 box-sizing
```
/* 规定两个并排的带边框方框 */
div{
  box-sizing:border-box;
  -moz-box-sizing:border-box;        /* Firefox */
  -webkit-box-sizing:border-box;     /* Safari */
  width:50%;
  float:left;
}
```

css 实现一个三角形
```
1 .test {
  width: 0;
  height: 0;
  margin: 0 auto;
  border: 6px solid transparent;
  border-top: red;
}

2 .test {
  width: 0;
  height: 0;
  margin: 0 auto;
  border-width: 6px;
  border-color: red transparent transparent transparent;
  border-style: solid dashed dashed dashed;
}
```

css 等高布局

原理：利用padding补偿法，设置父类超出隐藏，要等列的元素设置padding-bottom为一个很大的值，目的是为了填充，然后再设置margin-bottom去抵消这个值
这样就可以做成等高的布局

```
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8" />
<title>高度自适应布局</title>
<style>
body{ padding:0; margin:0; color:#f00;}
.container{ margin:0 auto; width:600px; border:3px solid #00C;
    overflow:hidden; /*这个超出隐藏的声明在IE6里不写也是可以的*/
}
.left{ float:left; width:150px; background:#B0B0B0;
    padding-bottom:2000px;
    margin-bottom:-2000px;
}
.right{ float:left; width:450px; background:#6CC;
   padding-bottom:2000px;
   margin-bottom:-2000px;
}
</style>
</head>
<body>
<div class="container">
    <div class="left">我是left</div>
    <div class="right">我是right<br><br><br>现在我的高度比left高，但left用它的padding-bottom补偿了这部分高度</div>
    <div style="clear:both"></div>
</div>
</body>
</html>
```

overflow: scroll 不能平滑滚动
```
-webkit-overflow-touching: touch;
```

盒模型：内容+内边距+边框+外边距
布局：定位布局、流布局、浮动布局、flex布局、grid布局，还有三栏布局中的圣杯和双飞翼布局。

圣杯：
```
<div id="container">
    <div id="center">center</div>
    <div id="left">left</div>
    <div id="right">right</div>
</div>

#container {
            padding: 0 100px 0 200px;/*左宽度为200px 右边宽度为100px*/
}
.col {
    float: left;
    position: relative;
    height: 300px;
}
#center {
    width: 100%;
    background: #eee;
}
#left {
    width: 200px;
    background: blue;
    margin-left: -100%;
    right: 200px;
}
#right {
    width: 100px;
    background: red;
    margin-right: -100px;
}

```

双飞翼布局：
```
<div id="container">
    <div id="center" class="col">
        <div class="wrap">center</div>
    </div>
    <div id="left" class="col">left</div>
    <div id="right" class="col">right</div>
</div>

#container {
            padding: 0 100px 0 200px;/*左宽度为200px 右边宽度为100px*/
}
.col {
    float: left;
    position: relative;
    height: 300px;
}
#center {
    width: 100%;
    background: #eee;
}
#left {
    width: 200px;
    background: blue;
    margin-left: -100%;
    right: 200px;
}
#right {
    width: 100px;
    background: red;
    margin-right: -100px;
}
```
两种布局的区别
这两种布局实现的都是两边固定宽度，中间自适应，中间栏放在最前面优先渲染。
不同的是，双飞翼布局多创建一个包裹的div，去掉了相对定位，css相对少写一些。
个人认为圣杯布局结构更简洁，平常工作中就看大家自己的选择了。

flex 容器
```
display: flex
```
包含水平的主轴（main axis） 和垂直的交叉轴（cross axis）。主轴开始的位置叫main start, 主轴结束的位置叫做main end,交叉轴开始的地方叫cross axis，交叉轴结束的位置叫cross axis。主轴是从左到右，交叉轴从上到下
主要有6个属性：
- flex-direction: row(水平方向，起点在左边) | row-reverse(水平方向，起点在右边) | column(垂直方向，起点在上面) | column-reverse(垂直方向，起点在下面);  (决定主轴的方向)
- flex-wrap: nowrap(不换行) | wrap(换行，第一行在上方) | wrap-reverse(换行，第一行在下方)（换行定义）
- flex-flow: （flex-direction 与 flex-wrap的组合,默认为row nowrap）
- justify-content: flex-start(左对齐) | flex-end(右对齐) | center(居中) | space-between(两端对齐，项目之前的间距都相等) | space-around(每个项目两侧的间距相等，项目之前的间距俾项目与边框的间距大); （主轴的对齐方式）
- align-items: flex-start(交叉轴起点对齐) | flex-end(交叉轴终点点对齐) | center(交叉轴中点对齐) | baseline(项目第一行文字的基线对齐) | stretch(交叉轴占满整个容器的高度); （交叉轴的对齐方式）
- align-content: flex-start(与交叉轴起点对齐) | flxe-end(与交叉轴终点对齐) | center(与交叉轴中点对齐) | space-between(与交叉轴两端对齐) | space-around(每根轴线之前的距离相等) | strech(默认，轴线充满整个交叉轴);（多根轴对齐方式，若只有一条轴线，该属性不起作用）

项目 flex
```
display: inline-flex;
```
- order: 1 （项目的排列顺序，数值越小，排列越靠前）
- flex-grow: <number> （定义项目放大比例，默认为0）
- flex-shrink: <number>（定义项目缩小比例，默认为1，负值无效）
- flex-basis: <length | auto> （定义项目占据主轴的空间，默认是0，可以是具体大小（350px））
- flex: none | [<'flex-grwo'> <'flex-shrink'>?||<'flex-basis'>](是flex-grow,flex-shrink,flex-basic，默认是0 1 auto)
- align-self: auto | flex-start | flex-end | center | baseline | stretch(与容器的align-items)（定义单个项目与其他项目不一样的对齐方式，可覆盖align-items属性，默认为auto）

- BFC(block formatting context) 块级格式化上下文
浮动元素和绝对定位元素，非块级盒子（inline-block,table-cell,table-captions）,overflow值不为'visiable'的块级盒子，就会创建块级格式化上下文

1 float 不为none
2 positon 的值不是 static和 relative
3 display的值是inline-block table-cell flex table-caption inline-flex
4 overflow 不是visiable

好处
1 元素布局不受外界的影响，块盒与行盒都会垂直沿着父元素的边框排列
2 解决元素之间的折叠问题
3 解决浮动元素，文字环绕的问题
4 overflow: none;

- 居中问题
水平居中：
1 定宽
```
.center {
    width: 960px;
    margin-left: auto;
    margin-right: auto;
}
```

2 父元素为块元素，属性为text-align: center，子元素设置为display: inline-block;

3 浮动实现水平居中的方法
```
.pagination {
  float: left;
  width: 100%;
  overflow: hidden;
  position: relative;
}
.pagination ul {
  clear: left;
  float: left;
  position: relative;
  left: 50%;/*整个分页向右边移动宽度的50%*/
  text-align: center;
}
.pagination li {
  line-height: 25px;
  margin: 0 5px;
  display: block;
  float: left;
  position: relative;
  right: 50%;/*将每个分页项向左边移动宽度的50%*/
}
.pagination a {
  display: block;
}
```

4 绝对定位实现水平居中
```
.ele {
    position: absolute;
    width: 500px;
    left: 50%;
    marign-left: -250px;
}
```

5 CSS3的flex实现水平居中
```
display: flex;
justify-content: center;
```

6 fit-content实现水平居中
```
.ele {
    width: fit-content;
    marign-left: auto;
    margin-right: auto;
}
```

垂直居中
1 行内元素垂直居中
.one {
    height: 100px;
    line-height: 100px;
}

2 利用display:table-cell使内容垂直居中
.two {
    display: table-cell;
    vertical-align:middle;
    text-align: center;
    width: 120px;
    height: 120px;
}

3 利用flex进行垂直居中
.three {
    display: flex;
    flex-direction: row;
    flex-wrap: nowrap;
    flex-grow: row nowrap;
    align-items: center;
}

4 使用css3的新属性transfrom:translate(x,y)属性(可以不设定宽高，移动端常用)
.four {
    position: absolute;
    width: 80px;
    height: 80px;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
}

5 最高大上的一种，用:before元素
.five {
    position: fixed;
    display: block;
    top: 0;
    right: 0;
    bottom: 0;
    left: 0;
    text-align: center;
}

.five:before {
    content: '';
    display: inline-block;
    vertical-align: middle;
    height: 100%;
}

.five.content {
    display: inline-block;
    vertical-align: middle;
    width: 60px;~~~~
    height: 60px;
    line-height: 60px;
}

- session、cookie、sessionStorage、localStorage等区别
Cookie
Cookie 是小甜饼的意思。顾名思义，cookie 确实非常小，它的大小限制为4KB左右，是网景公司的前雇员 Lou Montulli 在1993年3月的发明。它的主要用途有保存登录信息，比如你登录某个网站市场可以看到“记住密码”，这通常就是通过在 Cookie 中存入一段辨别用户身份的数据来实现的。（由服务器生成，可设置失效时间，如果在浏览器生成的cookie，关闭浏览器就会消失）

localStorage
是HTML5新加入的的技术，是一种本地存储

sessionStorage 与 session
sessionStorage与localStorge的接口类似，session即是会话，在刷新浏览器时数据依旧存在，但是关闭浏览器后，数据就清空了，而localStorge却永久不会消失

- px/em/rem的区别
px: 在页面缩放的时候无法调整大小
em: 值不是固定的，是根据父元素的字体大小，代表倍数
rem: 值不是固定的，是基于父元素根元素<html>字体大小的，也代表倍数

- transform、animation和transiton的相关属性

两者区别：
transition注重变换，注重几个属性
animation注重帧数变换，复杂的动画更适合

transition主要有四个属性：
transition-property:  none | all | [indent]（执行变换的属性）
transition-duration: <time> | [, <time>]默认值是0 持续时间
transition-timing-function: ease | linear | ease-in | ease-out | ease-in-out | cubic-bzier(<number>, <number>, <number>, <number>) 变换的速率
transition-delay:  <time> [, <time>] 默认大小是"0" 变换延迟时间

1、ease：（逐渐变慢）默认值，ease函数等同于贝塞尔曲线(0.25, 0.1, 0.25, 1.0).

2、linear：（匀速），linear 函数等同于贝塞尔曲线(0.0, 0.0, 1.0, 1.0).

3、ease-in：(加速)，ease-in 函数等同于贝塞尔曲线(0.42, 0, 1.0, 1.0).

4、ease-out：（减速），ease-out 函数等同于贝塞尔曲线(0, 0, 0.58, 1.0).

5、ease-in-out：（加速然后减速），ease-in-out 函数等同于贝塞尔曲线(0.42, 0, 0.58, 1.0)

6、cubic-bezier：（该值允许你去自定义一个时间曲线）， 特定的cubic-bezier曲线。 (x1, y1, x2, y2)四个值特定于曲线上点P1和点P2。所有值需在[0, 1]区域内，否则无效。

animation:
@keyframes IDENT {
     from {
       Properties:Properties value;
     }
     Percentage {
       Properties:Properties value;
     }
     to {
       Properties:Properties value;
     }
   }
   或者全部写成百分比的形式：
   @keyframes IDENT {
      0% {
         Properties:Properties value;
      }
      Percentage {
         Properties:Properties value;
      }
      100% {
         Properties:Properties value;
      }
    }

 调用动画
 .demo1 {
     width: 50px;
     height: 50px;
     margin-left: 100px;
     background: blue;
     -webkit-animation-name:'wobble';/*动画属性名，也就是我们前面keyframes定义的动画名*/
     -webkit-animation-duration: 10s;/*动画持续时间*/
     -webkit-animation-timing-function: ease-in-out; /*动画频率，和transition-timing-function是一样的*/
     -webkit-animation-delay: 2s;/*动画延迟时间*/
     -webkit-animation-iteration-count: 10;/*定义循环资料，infinite为无限次*/
     -webkit-animation-direction: alternate;/*定义动画方式*/
  }

transform:
transform: rotate(旋转) | scale(缩放) | skew(扭曲) | translate(移动) |matrix(矩阵);

### JavaScript部分
- JavaScript数据类型分哪些
六种原型数据类型：
1 Boolean 布尔值
2 null 空值
3 undefined 未定义
4 Number 数字
5 String 字符串
6 Symbol es6 新增 唯一的不可改变 主要作用判断变量属于哪一种类型

以及 Object 对象
Object 和 functions 是本语言的其他两个的基本元素

- JavaScript闭包
我的看法是：所谓闭包就是内部函数可以访问其函数的内部变量，返回其内部函数，供函数外的环境使用

官方：闭包是函数和声明该函数的词法环境的组合。
经典实例：
```
function init() {
    var name = "Mozilla"; // name is a local variable created by init
    function displayName() { // displayName() is the inner function, a closure
        alert (name); // displayName() uses variable declared in the parent function
    }
    displayName();
}
init();
```
运行这段代码和之前的 init() 示例的效果完全一样。其中的不同 — 也是有意思的地方 — 在于内部函数 displayName() 在执行前，被外部函数返回。

第一眼看上去，也许不能直观的看出这段代码能够正常运行。在一些编程语言中，函数中的局部变量仅在函数的执行期间可用。一旦 makeFunc() 执行完毕，我们会认为 name 变量将不能被访问。然而，因为代码运行得没问题，所以很显然在 JavaScript 中并不是这样的。

这个谜题的答案是，JavaScript中的函数会形成闭包。 闭包是由函数以及创建该函数的词法环境组合而成。这个环境包含了这个闭包创建时所能访问的所有局部变量。在我们的例子中，myFunc 是执行 makeFunc 时创建的 displayName 函数实例的引用，而 displayName 实例仍可访问其词法作用域中的变量，即可以访问到 name 。由此，当 myFunc 被调用时，name 仍可被访问，其值 Mozilla 就被传递到alert中。

- 前端跨域<br/>

跨域是指访问的东西来源，只要是协议、域名、端口不同就会限制访问。

1 通过document.domain跨域

2 通过location.hash跨域
```
try {
    parent.location.hash = 'data';
} catch (e) {
    // ie、chrome的安全机制无法修改parent.location.hash，
    var ifrproxy = document.createElement('iframe');
    ifrproxy.style.display = 'none';
    ifrproxy.src = "http://www.baidu.com/proxy.html#data";
    document.body.appendChild(ifrproxy);
}
```

3 通过HTML5的postMessage方法跨域
```
a 页面
window.onload = function() {
    var ifr = document.getElementById('ifr');
    var targetOrigin = "http://www.google.com";
    ifr.contentWindow.postMessage('hello world!', targetOrigin);
};
b页面
var onmessage = function (event) {
  var data = event.data;//消息
  var origin = event.origin;//消息来源地址
  var source = event.source;//源Window对象
  if(origin=="http://www.baidu.com"){
console.log(data);//hello world!
  }
};
if (typeof window.addEventListener != 'undefined') {
  window.addEventListener('message', onmessage, false);
} else if (typeof window.attachEvent != 'undefined') {
  //for ie
  window.attachEvent('onmessage', onmessage);
}
```

4 通过jsonp跨域
```
<script type="text/javascript">
    function dosomething(jsondata){
        //处理获得的json数据
    }
</script>
<script src="http://example.com/data.php?callback=dosomething"></script>
执行服务器返回的一段js代码，从而获取值
```

5 通过CORS跨域（Cross-Origin Resource Sharing）跨域资源共享
服务器端对于CORS的支持，主要就是通过设置Access-Control-Allow-Origin来进行的。如果浏览器检测到相应的设置，就可以允许Ajax进行跨域的访问
```
服务器需要配合设置
Access-Control-Allow-Origin: http://api.bob.com
Access-Control-Allow-Credentials: true
Access-Control-Expose-Headers: FooBar
Content-Type: text/html; charset=utf-8
```

6  通过window.name跨域

### JavaScript继承
javascript 是基于原型的
javascirpt对象都是基于原型链顶端的Object的实例

继承：
JavaScript 对象是动态的属性“包”（指其自己的属性）。JavaScript 对象有一个指向一个原型对象的链。当试图访问一个对象的属性时，它不仅仅在该对象上搜寻，还会搜寻该对象的原型，以及该对象的原型的原型，依次层层向上搜索，直到找到一个名字匹配的属性或到达原型链的末尾。
```
// 让我们假设我们有一个对象 o, 其有自己的属性 a 和 b：
// {a: 1, b: 2}
// o 的 [[Prototype]] 有属性 b 和 c：
// {b: 3, c: 4}
// 最后, o.[[Prototype]].[[Prototype]] 是 null.
// 这就是原型链的末尾，即 null，
// 根据定义，null 没有[[Prototype]].
// 综上，整个原型链如下:
// {a:1, b:2} ---> {b:3, c:4} ---> null

console.log(o.a); // 1
// a是o的自身属性吗？是的，该属性的值为1

console.log(o.b); // 2
// b是o的自身属性吗？是的，该属性的值为2
// 原型上也有一个'b'属性,但是它不会被访问到.这种情况称为"属性遮蔽 (property shadowing)"

console.log(o.c); // 4
// c是o的自身属性吗？不是，那看看原型上有没有
// c是o.[[Prototype]]的属性吗？是的，该属性的值为4

console.log(o.d); // undefined
// d是o的自身属性吗？不是,那看看原型上有没有
// d是o.[[Prototype]]的属性吗？不是，那看看它的原型上有没有
// o.[[Prototype]].[[Prototype]] 为 null，停止搜索
// 没有d属性，返回undefined
```

继承方法
JavaScript 并没有其他基于类的语言所定义的“方法”。在 JavaScript 里，任何函数都可以添加到对象上作为对象的属性。函数的继承与其他的属性继承没有差别，包括上面的“属性遮蔽”（这种情况相当于其他语言的方法重写）。

当继承的函数被调用时，this 指向的是当前继承的对象，而不是继承的函数所在的原型对象。
```
var o = {
  a: 2,
  m: function(){
    return this.a + 1;
  }
};

console.log(o.m()); // 3
// 当调用 o.m 时,'this'指向了o.

var p = Object.create(o);
// p是一个继承自 o 的对象

p.a = 4; // 创建 p 的自身属性 a
console.log(p.m()); // 5
// 调用 p.m 时, 'this'指向 p.
// 又因为 p 继承 o 的 m 函数
// 此时的'this.a' 即 p.a，即 p 的自身属性 'a'
```

使用不同的方法来创建对象和生成原型链
语法结构创建的对象
```
var o = {a: 1};

// o 这个对象继承了Object.prototype上面的所有属性
// o 自身没有名为 hasOwnProperty 的属性
// hasOwnProperty 是 Object.prototype 的属性
// 因此 o 继承了 Object.prototype 的 hasOwnProperty
// Object.prototype 的原型为 null
// 原型链如下:
// o ---> Object.prototype ---> null

var a = ["yo", "whadup", "?"];

// 数组都继承于 Array.prototype
// (Array.prototype 中包含 indexOf, forEach等方法)
// 原型链如下:
// a ---> Array.prototype ---> Object.prototype ---> null

function f(){
  return 2;
}

// 函数都继承于Function.prototype
// (Function.prototype 中包含 call, bind等方法)
// 原型链如下:
// f ---> Function.prototype ---> Object.prototype ---> null
```

构造器创建的对象
```
function Graph() {
  this.vertices = [];
  this.edges = [];
}

Graph.prototype = {
  addVertex: function(v){
    this.vertices.push(v);
  }
};

var g = new Graph();
// g是生成的对象,他的自身属性有'vertices'和'edges'.
// 在g被实例化时,g.[[Prototype]]指向了Graph.prototype.
```

Object.create 创建的对象
 ```
 var a = {a: 1};
// a ---> Object.prototype ---> null

var b = Object.create(a);
// b ---> a ---> Object.prototype ---> null
console.log(b.a); // 1 (继承而来)

var c = Object.create(b);
// c ---> b ---> a ---> Object.prototype ---> null

var d = Object.create(null);
// d ---> null
console.log(d.hasOwnProperty); // undefined, 因为d没有继承Object.prototype
 ```

class 关键字创建的对象
 ```
"use strict";
class Polygon {
  constructor(height, width) {
    this.height = height;
    this.width = width;
  }
}
class Square extends Polygon {
  constructor(sideLength) {
    super(sideLength, sideLength);
  }
  get area() {
    return this.height * this.width;
  }
  set sideLength(newLength) {
    this.height = newLength;
    this.width = newLength;
  }
}
var square = new Square(2);
 ```

性能
在原型链上查找属性比较耗时，对性能有副作用，这在性能要求苛  刻的情况下很重要。另外，试图访问不存在的属性时会遍历整个原 型链。

### JavaScript的节流和防抖

函数防抖（debounce）
当调用动作过n毫秒后，才会执行该动作，若在这n毫秒内又调用此动作则将重新计算执行时间

函数节流（throttle）
预先设定一个执行周期，当调用动作的时刻大于等于执行周期则执 行该动作，然后进入下一个新周期

区别
函数防抖：如果有人进电梯（触发事件），那电梯将在10秒钟后出发（执行事件监听器），这时如果又有人进电梯了（在10秒内再次触发该事件），我们又得等10秒再出发（重新计时）。
函数节流 ：保证如果电梯第一个人进来后，10秒后准时运送一次，这个时间从第一个人上电梯开始计时，不等待，如果没有人，则不运行

函数防抖
```
function _debounce(fn,wait){
    var timer = null;
    return function(){
        clearTimeout(timer)
        timer = setTimeout(()=>{
            fn()
        },wait)
    }
}
function _log(){
    console.log(1)
}
window.onscroll = _debounce(_log,500)
```


函数节流
```
function _throttle(fn,wait,time){
    var previous = null; //记录上一次运行的时间
    var timer = null;
    return function(){
        var now = +new Date();
        if(!previous) previous = now;
        //当上一次执行的时间与当前的时间差大于设置的执行间隔时长的话，就主动执行一次
        if(now - previous > time){
            clearTimeout(timer);
            fn();
            previous = now;// 执行函数后，马上记录当前时间
        }else{
            clearTimeout(timer);
            timer = setTimeout(function(){
                fn();
            },wait);
        }
    }
}
function _log(){
    console.log(1)
}
window.onscroll = _debounce(_log,500,2000)
```

### JavaScript的事件
委托-捕获-冒泡-目标
```
ele.addEventListener('click', funciton, true) //捕获（从上到下，父节点到子节点的顺序）
ele.addEventListener('click', function, false) // 冒泡 （从里到外，子节点到父节点的顺序）
```

### ajax请求方式
ajax是asynchronous javascript and XML的简写
```
//创建xhr对象
    var xhr;
    if(window.XMLHttpRequest){
        xhr = new XMLHttpRequest();
    }else{
        xhr = new ActiveXObject('Microsoft.XMLHTTP');
    }
    //发送请求
    xhr.open('get','/uploads/rs/26/ddzmgynp/message.xml',false);
    xhr.send();
    //同步接受响应
    if(xhr.readyState == 4){
        if(xhr.status == 200){
            //实际操作
            result.innerHTML += xhr.responseText;
        }
    }

    0(UNSENT):未初始化。尚未调用open()方法
    1(OPENED):启动。已经调用open()方法，但尚未调用send()方法
    2(HEADERS_RECEIVED):发送。己经调用send()方法，且接收到头信息
    3(LOADING):接收。已经接收到部分响应主体信息
    4(DONE):完成。已经接收到全部响应数据，而且已经可以在客户端使用了
```

### this指向问题
具体看this所在环境
首先看方法的调用的方式，有直接调用，方法调用，new调用，bind后调用，call apply调用 ，es6箭头函数调用
1 直接调用
函数内部的 this 指向全局对象

```
// 简单兼容浏览器和 NodeJs 的全局对象
const _global = typeof window === "undefined" ? global : window;

function test() {
    console.log(this === _global);    // true
}

test();    // 直接调用
```

2 bind() 对直接调用的影响
还有一点需要注意的是 bind() 的影响。Function.prototype.bind() 的作用是将当前函数与指定的对象绑定，并返回一个新函数，这个新函数无论以什么样的方式调用，其 this 始终指向绑定的对象。还是来看例子：
```
const obj = {};

function test() {
    console.log(this === obj);
}

const testObj = test.bind(obj);
test();     // false
testObj();  // true

自己实现一个bind函数
const obj = {};

function test() {
    console.log(this === obj);
}

// 自定义的函数，模拟 bind() 对 this 的影响
function myBind(func, target) {
    return function() {
        return func.apply(target, arguments);
    };
}

const testObj = myBind(test, obj);
test();     // false
testObj();  // true
```

3 call 和 apply 对 this 的影响
```
const obj = {};

function test() {
    console.log(this === obj);
}

// 绑定到一个新对象，而不是 obj
const testObj = test.bind({});
test.apply(obj);    // true

// 期望 this 是 obj，即输出 true
// 但是因为 testObj 绑定了不是 obj 的对象，所以会输出 false
testObj.apply(obj); // false
```

4 方法调用
```
const obj = {
    // 第一种方式，定义对象的时候定义其方法
    test() {
        console.log(this === obj);
    }
};

// 第二种方式，对象定义好之后为其附加一个方法(函数表达式)
obj.test2 = function() {
    console.log(this === obj);
};

// 第三种方式和第二种方式原理相同
// 是对象定义好之后为其附加一个方法(函数定义)
function t() {
    console.log(this === obj);
}
obj.test3 = t;

// 这也是为对象附加一个方法函数
// 但是这个函数绑定了一个不是 obj 的其它对象
obj.test4 = (function() {
    console.log(this === obj);
}).bind({});

obj.test();     // true
obj.test2();    // true
obj.test3();    // true

// 受 bind() 影响，test4 中的 this 指向不是 obj
obj.test4();    // false
```

5 new 调用
```
var data = "Hi";    // 全局变量

function AClass(data) {
    this.data = data;
}

var a = new AClass("Hello World");
console.log(a.data);    // Hello World
console.log(data);      // Hi

var b = new AClass("Hello World");
console.log(a === b);   // false
```

6 箭头函数中的 this
这里已经清楚了说明了，箭头函数没有自己的 this 绑定。箭头函数中使用的 this，其实是直接包含它的那个函数或函数表达式中的 this。比如
```
const obj = {
    test() {
        const arrow = () => {
            // 这里的 this 是 test() 中的 this，
            // 由 test() 的调用方式决定
            console.log(this === obj);
        };
        arrow();
    },

    getArrow() {
        return () => {
            // 这里的 this 是 getArrow() 中的 this，
            // 由 getArrow() 的调用方式决定
            console.log(this === obj);
        };
    }
};

obj.test();     // true

const arrow = obj.getArrow();
arrow();        // true
```

### es6相关知识点<br />

1 let const

2 解构赋值

3 扩展

4 函数赋值默认参数

5 箭头函数

6 promise

7 generator（生成器）

8 symbol（类型判定）























