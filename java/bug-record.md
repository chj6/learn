# JAVA BUG记录

- **时间保存时会莫名的多加一个小时**   
  原因：[夏令时](https://baike.baidu.com/item/%E5%A4%8F%E4%BB%A4%E6%97%B6)，要看当前使用的框架是否对这个`夏令时`进行了兼容处理。
