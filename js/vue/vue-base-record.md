# Vue基础记录

## 按需加载
注意点，如果需要按需加载，一定不能使用 `import a from @"componets/a"`引用组件，这样之后打包时对`a`组件就不会进行分割了；

[官方参考文档](https://router.vuejs.org/zh/guide/advanced/lazy-loading.html)

## 在index.html引用图片
``` html
<link rel="apple-touch-icon" href="<%= require('@/assets/images/icon-57.png') %>" />
```

## router.js中处理路由切换时还原滚动条位置
``` javascript
new Router({
  mode: "history",
  base: "mobile",
  scrollBehavior(to, from, savedPosition) {
    if (savedPosition) {
        return savedPosition
    } else {
        if (from.meta.keepAlive) {
            from.meta.savedPosition = document.body.scrollTop
        }
        return {
            x: 0,
            y: to.meta.savedPosition || 0
        }
    }
  },
  routes:[{
      path: "/404",
      alias: "*", //未知路径、404的统一处理
      name: "404",
      component: P404
  }]
);
```

## 低版本的浏览器处理
``` javascript
npm install --save-dev babel-polyfill  
// 然后在/build/webpack.base.config.js（不同的脚手架生成的webpack配置文件路径和名称可能不一致）
const webpackConfig = {
  context: path.resolve(__dirname, '../'),
  entry: {
    // app: './src/main.js' 替换成下面的
    app: ['babel-polyfill', './src/main.js']
  }
}
```