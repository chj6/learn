# webpack中require用法

**webpack模块方法参考：[api地址](https://www.webpackjs.com/api/module-methods/)**    
官方推荐使用es6中的import、export语法，并且建议在一个项目中尽量使用一致的语法，防止一些奇怪的bug；使用import本质上还是会转换成cmd。


```javascript
// 同步加载
// a.js会被打包到引用的文件中（比如：main.js）
var a = require('./a.js');
a.show();

// 异步加载
// require.ensure是webpack自定义的，已经被import()取代
// a.js会被打包到单独的chunk文件，默认生成的文件名格式：数字.md5.js，如果name传递了值，则以：name.md5.js
require.ensure([], function(require){
  var a = require('./a.js');
  a.show();
  // 支持多个文件，生成到一个文件中去
  var b =require('./b.js');
  b.show();
}, name)


// 预加载懒执行
require.ensure(['./a.js'], function(){ // 这里的a.js只会下载下来，但是不会执行
  var a = require('./a.js'); // 这里才是evaluate的时候
  a.show();
})
```


