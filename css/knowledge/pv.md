
## Retina屏幕的了解
设备像素比dpr（物理像素 / 设备独立像素）：正常屏幕的1px显示一个像素点，但是在retina屏幕下1px会显示多倍像素点；比如1px的分割线条，在retina屏幕下就会显示的相对比较粗，可以通过transform的translateX的缩放来解决；图片可能就需要通过2倍、3倍图去进行处理了（处理方法之一：通过js：window.devicePixelRatio，css：以通过-webkit-device-pixel-ratio，-webkit-min-device-pixel-ratio和 -webkit-max-device-pixel-ratio，去检查分配率然后去设置图片的名称比如@2x @3x等）；


## Retina屏幕上1px有几种实现方法？
- **0.5px边框**      
通过js检查浏览器是否能处理0.5px，如果可以给html元素表情添加个class
```javascript
if (window.devicePixelRatio && devicePixelRatio >= 2) {
  var testElem = document.createElement('div');
  testElem.style.border = '.5px solid transparent';
  document.body.appendChild(testElem);
}
if (testElem.offsetHeight == 1) {
  document.querySelector('html').classList.add('hairlines');
}
  document.body.removeChild(testElem);
}
// 脚本应该放在内，如果在里面运行，需要包装 $(document).ready(function() {})

// css
div {
  border: 1px solid #bbb;
}
.hairlines div {
  border-width: 0.5px;
}
```
    特点：  
    1.简单
    缺点：
    1.无法兼容android、ios8以下设备
- **border-image实现**   
 图片模拟
```css
.border-bottom-1px {
  border-width: 0 0 1px 0;
  -webkit-border-image: url(linenew.png) 0 0 2 0 stretch;
  border-image: url(linenew.png) 0 0 2 0 stretch;
}
```
上文是把border设置在边框的底部，所以使用的图片是2px高，上部的1px颜色为透明，下部的1px使用视觉规定的border的颜色。如果边框底部和顶部同时需要border，可以使用下面的border-image：
```css
.border-image-1px {
  border-width: 1px 0;
  -webkit-border-image: url(linenew.png) 2 0 stretch;
  border-image: url(linenew.png) 2 0 stretch;
}
```
到目前为止，我们已经能在iphone上展现1px border的效果了。但是我们发现这样的方法在非视网膜屏上会出现border显示不出来的现象，于是使用Media Query做了一些兼容，样式设置如下：
```css
.border-image-1px {
  border-bottom: 1px solid #666;
}
@media only screen and (-webkit-min-device-pixel-ratio: 2) {
  .border-image-1px {
    border-bottom: none;
    border-width: 0 0 1px 0;
    -webkit-border-image: url(../img/linenew.png) 0 0 2 0 stretch;
    border-image: url(../img/linenew.png) 0 0 2 0 stretch;
  }
}
```
    优点：
    1.可以设置单条,多条边框
    2.没有性能瓶颈的问题
    缺点：
    1.修改颜色麻烦, 需要替换图片
    2.圆角需要特殊处理，并且边缘会模糊
- **background-image实现**   
```css
.background-image-1px {
  background: url(../img/line.png) repeat-x left bottom;
  -webkit-background-size: 100% 1px;
  background-size: 100% 1px;
}
```
    优点：
    1.可以设置单条,多条边框
    2.没有性能瓶颈的问题
    缺点：
    1.修改颜色麻烦, 需要替换图片
    2.圆角需要特殊处理，并且边缘会模糊
- **多背景渐变实现**   
与background-image方案类似，只是将图片替换为css3渐变。设置1px的渐变背景，50%有颜色，50%透明
```css
.background-gradient-1px {
  background:
    linear-gradient(#000, #000 100%, transparent 100%) left / 1px 100% no-repeat,
    linear-gradient(#000, #000 100%, transparent 100%) right / 1px 100% no-repeat,
    linear-gradient(#000,#000 100%, transparent 100%) top / 100% 1px no-repeat,
    linear-gradient(#000,#000 100%, transparent 100%) bottom / 100% 1px no-repeat
}
/* 或者 */
.background-gradient-1px{
  background:
    -webkit-gradient(linear, left top, right bottom, color-stop(0, transparent), color-stop(0, #000), to(#000)) left / 1px 100% no-repeat,
    -webkit-gradient(linear, left top, right bottom, color-stop(0, transparent), color-stop(0, #000), to(#000)) right / 1px 100% no-repeat,
    -webkit-gradient(linear, left top, right bottom, color-stop(0, transparent), color-stop(0, #000), to(#000)) top / 100% 1px no-repeat,
    -webkit-gradient(linear, left top, right bottom, color-stop(0, transparent), color-stop(0, #000), to(#000)) bottom / 100% 1px no-repeat
}
```
- **box-shadow实现**   
利用css 对阴影处理的方式实现0.5px的效果
```css
.box-shadow-1px {
  box-shadow: inset 0px -1px 1px -1px #c8c7cc;
}
```
    优点：
    1.代码量少
    2.可以满足所有场景
    缺点：
    1.边框有阴影，颜色变浅
- **viewport + rem实现**   
同时通过设置对应viewport的rem基准值，这种方式就可以像以前一样轻松愉快的写1px了。
在devicePixelRatio = 2 时，输出viewport：   
`<meta name="viewport" content="initial-scale=0.5, maximum-scale=0.5, minimum-scale=0.5, user-scalable=no">`   
在devicePixelRatio = 3 时，输出viewport：   
`<meta name="viewport" content="initial-scale=0.3333333333333333, maximum-scale=0.3333333333333333, minimum-scale=0.3333333333333333, user-scalable=no">`

这种兼容方案相对比较完美，适合新的项目，老的项目修改成本过大。

    优点：
    1.所有场景都能满足
    2.一套代码，可以兼容基本所有布局
    缺点：
    1.老项目修改代价过大，只适用于新项目

- **伪类 + transform实现**   
原理是把原先元素的 border 去掉，然后利用 :before 或者 :after 重做 border ，并 transform 的 scale 缩小一半，原先的元素相对定位，新做的 border 绝对定位。   
单条border样式设置：
```css
.scale-1px{
  position: relative;
  border:none;
}
.scale-1px:after{
  content: '';
  position: absolute;
  bottom: 0;
  background: #000;
  width: 100%;
  height: 1px;
  -webkit-transform: scaleY(0.5);
  transform: scaleY(0.5);
  -webkit-transform-origin: 0 0;
  transform-origin: 0 0;
}
```
四条border样式设置：
```css
.scale-1px{
  position: relative;
  margin-bottom: 20px;
  border:none;
  border-radius: 2px;
}
.scale-1px:after{
  content: '';
  position: absolute;
  top: 0;
  left: 0;
  border: 1px solid #000;
  -webkit-box-sizing: border-box;
  box-sizing: border-box;
  border-radius:4px;
  width: 200%;
  height: 200%;
  -webkit-transform: scale(0.5);
  transform: scale(0.5);
  -webkit-transform-origin: left top;
  transform-origin: left top;
}
```
最好在使用前也判断一下，结合 JS 代码，判断是否 Retina 屏：
```javascript
if(window.devicePixelRatio && devicePixelRatio >= 2){
  document.querySelector('ul').className = 'scale-1px';
}
```
    优点：
    1.所有场景都能满足
    2.支持圆角(伪类和本体类都需要加border-radius)，伪类的border-raidus是元素本身的2倍
    缺点：
    1.对于已经使用伪类的元素(例如clearfix)，可能需要多层嵌套


## 说一说移动端的布局。 flexible。
主要是为了在移动端进行自适应；（主要是根据dpr的倍数去设置根字体大小，页面元素的大小用rem作为单位）


## 怎么自己去实现一个flexible。 rem布局的实现原理。
现在（2017年12月12日）看git上面的源码只根据devicePixelRatio设置body的字体大小`document.body.style.fontSize = (12 * dpr) + 'px'`，html大小设置成clientWidth/10 px；本质上还是固定`<meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1, minimum-scale=1, user-scalable=no">`，将页面width分成10份，然后用js去设置html字体大小；  
rem：就是设置根节点字体大小，之后所有的子节点都根据这个节点去计算；


## 移动端的点透是什么，有没有了解
当手指触摸到屏幕的时候，系统生成两个事件，一个是touch 一个是click，touch先执行，touch执行完成后，A从文档树上面消失了，而且由于移动端click还有延迟200-300ms的关系，当系统要触发click的时候，发现在用户点击的位置上面，目前离用户最近的元素是B，所以就直接把click事件作用在B元素上面了；解决方案：可以在touchend的时候阻止掉默认事件e.preventDefault()；或者使用第三方的手势库；

## css布局的属性有哪些。
分为盒模型布局和flex布局；   
盒模型布局依赖display+position+float属性，但是对于一些特殊的布局比较难实现（比如：左边固定，右边动态；垂直居中；）；   
flex布局：弹性布局（display:flex/inline-flex），可实现“骰子布局（正方形中9个点）”、“网格布局”等；    
flex语法：http://www.ruanyifeng.com/blog/2015/07/flex-examples.html


## 一个有border的div，里面有一个图片，发现图片和下面的border有一定的空隙（baseline）
baseline是小写字母x下面的一条线，基线的位置取决于font-size和line-height；
