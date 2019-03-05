# JAVA BUG记录

- **时间保存时会莫名的多加一个小时**   
  原因：[夏令时](https://baike.baidu.com/item/%E5%A4%8F%E4%BB%A4%E6%97%B6)，要看当前使用的框架是否对这个`夏令时`进行了兼容处理。

- **chrome下载文件提示：ERR_RESPONSE_HEADERS_MULTIPLE_CONTENT_DISPOSITION**   
  原因：文件在上传时，文件名中有特殊字符；然后再下载这个文件时，由于在文件名上添加引用会导致浏览器在解析这个文件名会有点问题；
```java
// 之前写法
response.setHeader("Content-Disposition", "attachment;filename=" + downloadName);

// 修改之后的  
// 在下载文件时，文件名加上引号，防止特殊符号导致某些浏览器响应错误
response.setHeader("Content-Disposition", "attachment;filename=\"" + downloadName+"\"");
```
