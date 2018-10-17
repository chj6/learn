# Vue基础记录

## 按需加载
注意点，如果需要按需加载，一定不能使用 <b>import a from @"componets/a"</b>引用组件，这样之后打包时对<b>a</b>组件就不会进行分割了；

[官方参考文档](https://router.vuejs.org/zh/guide/advanced/lazy-loading.html)

## 在index.html引用图片
``` html
<link rel="apple-touch-icon" href="<%= require('@/assets/images/icon-57.png') %>" />
```