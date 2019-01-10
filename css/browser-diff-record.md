# 浏览器兼容性记录
- select默认需要加上背景色：  
在不同的浏览器下默认的背景色是不一样的，ubuntu中的chrome默认的背景颜色是灰色，windows的chrome是白色；

- safari在隐私模式下，localstorage无法使用

- 如果使用a标签去模拟input button，在三击之后会导致文字也会被选中，应该是a标签和input button的默认行为不一致；

- box-shadow在chrome的iphoneX模拟器上设置阴影的时候，会出现问题；
  ``` javascript
  <div style="box-shadow:0 1px 0 0 red;height:100px;width:100px;background:#ccc;"></div>
  ```
  <div style="box-shadow:0 1px 0 0 red;height:100px;width:100px;background:#ccc;"></div>
  
- ios的某些版本上当input处于键盘输入时，页面会变成空白，需要对input的父级元素添加`position:relative;`

- ie中的图片要设置width、height，不然会导致图片拉伸，因为IE会自动设置width、height

- ios的date格式：2000/1/1（必须要用这个格式），如果用2000-1-1用new Date()，无法初始化
