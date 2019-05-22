# vue在nginx中部署history模式的单页面
PS：基于vue-cli 3.x
## 1.router.js中base
``` javascript
new Router({
  mode: "history",
  base: "mobile",
  routes:[{
      path: "/404",
      alias: "*", //未知路径、404的统一处理
      name: "404",
      component: P404
  }]
);
```

## 2.接口代理配置和publicPath
注意：publicPath要和路由文件中的base配置一直都是:/website/demo1/，否则路由请求的路径不对   
```javascript
// vue.config.js文件
module.exports = {
  publicPath: '/website/demo1/', // 请求的路径就是以：hostname/website/demo1/ 开头
  devServer: {
    proxy: {
      '/api': {
        target: process.env.NODE_ENV === 'production' ? 'https://www.example.com/api/' : 'http://www.chj1.com/', //对应自己的接口
        changeOrigin: true,
        ws: true,
        pathRewrite: {
          '^/api': ''
        }
      }
    },
    port: 8100
  }
};
```

## 3.nginx配置文件
注意：开头的/website/demo1/需要与vue.config.js中的publicPath、router.js中的base一致
```
location /website/demo1/ {
  alias /root/data/website/demo1/;
  try_files $uri $uri/ /website/demo1/index.html;
}
```
