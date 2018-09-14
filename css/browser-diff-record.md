# 浏览器兼容性记录
- select默认需要加上背景色：  
在不同的浏览器下默认的背景色是不一样的，ubuntu中的chrome默认的背景颜色是灰色，windows的chrome是白色；

- safari在隐私模式下，localstorage无法使用

- 如果使用a标签去模拟input button，在三击之后会导致文字也会被选中；

- box-shadow在chrome的iphoneX模拟器上设置阴影的时候，会出现问题；
  ``` javascript
  <div style="box-shadow:0 1px 0 0 red;height:100px;width:100px;background:#ccc;"></div>
  ```
  <div style="box-shadow:0 1px 0 0 red;height:100px;width:100px;background:#ccc;">
  </div>