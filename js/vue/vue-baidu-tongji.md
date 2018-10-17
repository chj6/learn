
# 单页应用使用百度统计
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
2.在src的main.js中添加，**router的`beforeEach`一定要放在`new Vue`的前面，否则会导致第一次无法进入router的beforeEach**
``` javascript
// 百度统计
router.beforeEach((to, from, next) => {
  if (to.path) {
    window._hmt && window._hmt.push(["_trackPageview", "/" + to.fullPath]);
  }
  next();
});

/* eslint-disable no-new */
new Vue({
  el: "#app",
  router,
  store,
  components: {
    App
  },
  template: "<App/>"
});

```
