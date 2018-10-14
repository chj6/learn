# Vue记录

## 按需加载
注意点，如果需要按需加载，一定不能使用 <b>import a from @"componets/a"</b>引用组件，这样之后打包时对<b>a</b>组件就不会进行分割了；

[官方参考文档](https://router.vuejs.org/zh/guide/advanced/lazy-loading.html)

## 单页应用使用百度统计
1.首页在根目录下的index.html添加
``` javascript
<script>
  var _hmt = _hmt || [];
  (function () {
    var hm = document.createElement("script");
    hm.src = "https://hm.baidu.com/hm.js?申请的密钥";
    var s = document.getElementsByTagName("script")[0];
    s.parentNode.insertBefore(hm, s);
  })();
</script>
```
2.在src的main.js中添加
``` javascript
// 百度统计
router.beforeEach((to, from, next) => {
  if (to.path) {
    window._hmt && window._hmt.push(["_trackPageview", "/" + to.fullPath]);
  }
  next();
});
```
