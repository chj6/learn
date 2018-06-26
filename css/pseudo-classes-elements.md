# 概念
- 伪类（Pseudo-classes）：
写法（:）用来设置元素的特殊状态。
- 伪元素（Pseudo-elements）：  
写法（::）用来设置元素的某些元素的部分样式；伪元素不会出现在dom中，只是在css渲染层加入，所以无法用document.querySelector来操作它们。  
**由于IE8不支持双冒号（::）的写法，所以如果要兼容IE8的话还是使用单冒号的写法（:），另外主流浏览器都是支持单冒号写法的伪元素，所以大部分代码的伪元素大部分都是用的单冒号，如果是做移动端开发还是用标准的双冒号写法好。**

## 伪类
1. CSS1
   - :link
   - :visited
   - :active
2. CSS2
   - :lang()
   - :first-child
   - :hover
   - :focus
3. CSS3
    - :target
    - :root
    - :nth-child()
    - :nth-last-of-child()
    - :nth-of-type()
    - :nth-last-of-type() 
    - :last-child
    - :first-of-type
    - :last-of-type
    - :only-child
    - :only-of-type
    - :empty
    - :not()


## 伪元素
- ::after用来创建一个伪元素，做为已选中元素的最后一个子元素。通常会配合content属性来为该元素添加装饰内容。这个虚拟元素默认是行内元素。

- ::before用来创建一个伪元素，作为已选中元素的第一个子元素。常通过content 属性来为一个元素添加修饰性的内容。这个虚拟元素默认是行内元素。

- ::first-letter会选中某 block-level element（块级元素）第一行的第一个字母，并且文字所处的行之前没有其他内容（如图片和内联的表格） 。

- ::first-line CSS pseudo-element （CSS伪元素）在某 block-level element （块级元素）的第一行应用样式。第一行的长度取决于很多因素，包括元素宽度，文档宽度和文本的文字大小。

- ::selection CSS伪元素应用于文档中被用户高亮的部分（比如使用鼠标或其他选择设备选中的部分）。
