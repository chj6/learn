# css 选择器记录

```html
<div class="main" id="id">
   <div>0</div>
   <div class="child">
      1
      <div>1-1</div>
      <div>1-2</div>
      <div>1-3</div>
   </div>
   <div>2</div>
   <div>3</div>
   <div>4</div>
   <div>5</div>
</div>
```

- id选择器（#）
```css
#id {  }
```

- 类选择器（.）
```css
.main {  }
```

- 伪类选择器
```css
/* main下面的第一个div宽度是100px */
.main div:first-child{
    width: 100px;
}
```
可参考：[pseudo-classes-elements](pseudo-classes-elements.md)

- 后代选择器（空格）   
```css
/* main下面的所有div宽度都是100px */
.main div{
    width: 100px;
}
```

- 直系后代选择器（>）
```css
/* main下面的一级div宽度都是100px */
.main > div{
    width: 100px;
}
```

- 后续兄弟选择器（~）
```css
/* child的后面平级div元素宽度都是100px */
/* div2、div3、div4、div5 */
.child ~ div{
    width: 100px;
}
```

- 下一个兄弟选择器（+）
```css
/* child的下一个div元素宽度都是100px */
/* div2 */
.child + div{
    width: 100px;
}

.child div:first-child{

}

```
**因为浏览器是渐进显示css的，所以当文档加载多少就会显示多少内容，而不用等整个文档加载完毕。所以一个节点所适用的样式只取决于它和它之前的节点。如果存在上一个元素选择器，就会要求在解析到一个新的节点之后，需要重新往前计算之前的节点样式，这样就会引起回溯。**
