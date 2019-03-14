# 动画记录
- transition（翻译：过渡）突出的是过渡的效果；
- transform（翻译：改变）突出的改变之后的结果；
- animation（翻译：动画）突出的整个动画的过程，功能也最强大；

## transition
`transition`：是一个简写属性，用于transition-property（过渡属性），transition-duration（过渡时间），transition-timing-function（执行的时间曲线）、transition-delay（延迟执行时间）。用于在一个元素的不同的状态之间切换的时候定义不同的效果。

用在不同的伪元素之间切换，比如:hover，:active或者通过js实现的状态变化。但是transition需要明确知道，开始状态和结束状态的具体数值，才能计算出中间状态。比如，height从0px变化到100px，transition可以算出中间状态。但是，transition没办法计算出0px到auto的中间状态。类似的情况还有，display:none到block，background:url(a.jpg)到url(b.jpg)等，但是对于background的颜色可以使用transition来控制颜色的变换效果。

transition的优点在于简单易用，但是他有几个很大的局限：
- transition需要事件触发，所以没办法在网页加载时自动发生；
- transition是一次性的，不能重复发生，除非一再触发；
- transition只能定义开始和结束状态，不能定义中间状态，所以只有2个状态值；
- 一条transition规则，只能定义一个属性的变化，不能涉及多个属性；

```css
/* 用法 */
/* Apply to 1 property */
/* property name | duration */
transition: margin-right 4s;

/* property name | duration | delay */
transition: margin-right 4s 1s;

/* property name | duration | timing function */
transition: margin-right 4s ease-in-out;

/* property name | duration | timing function | delay */
transition: margin-right 4s ease-in-out 1s;

/* Apply to 2 properties */
transition: margin-right 4s, color 1s;

/* Apply to all changed properties */
transition: all 0.5s ease-out;

/* Global values */
transition: inherit;
transition: initial;
transition: unset;
```


## transform
`transform`：允许旋转（rotate(100deg)）、缩放（scale(0.5)）、倾斜（skew(100deg)）或者平移（translate(10%,10%)）指定元素，还有一个matrix矩阵，用的比较少；
- rotate值：角度deg（rotate(100deg)）、旋转圈turn（rotate(0.5turn)）；
- scale值：整数（scale(1)）、小数（scale(0.5)），不能添加任何单位；
- skew值：角度deg（skew(10deg)）、弧度rad（skew(1.5rad)）（90deg约等于1.5rad）；
- translate值：百分比（translate(80px, 80px)）、px、em；


## animation
`animation`：是一个简写属性，包括animation-name（动画名称由@keyframes定义的）、animation-duration（动画周期时长）、animation-timing-function（执行时间的曲线）、animation-delay（延迟执行时长）、animation-iteration-count（动画执行的循环次数）、animation-direction（动画执行方向）和animation-fill-mode（指定动画执行之前和之后的样式）、animation-play-state（动画执行状态）；

```css
/* 用法 */
/* @keyframes duration | timing-function | delay |
   iteration-count | direction | fill-mode | play-state | name */
animation: 3s ease-in 1s 2 reverse both paused slidein;

/* @keyframes duration | timing-function | delay | name */
animation: 3s linear 1s slidein;

/* @keyframes duration | name */
animation: 3s slidein;

@keyframes slidein {
    from { transform: scaleX(0); }
    to   { transform: scaleX(1); }
}
/* keyframes还可以通过百分比来指定更为精致的动画 */
@keyframes slidein {
    0% { background: #000; }
    50% { background: #ccc; }
    100% { background: #fff; }
}

```
