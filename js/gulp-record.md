# gulp记录
1. 排除a、b目录，后面的<b>**</b>一定要加上
``` javascript
 gulp.src(['./src/**/*.js', '!./src/a/**','!./src/b/**']
 ```