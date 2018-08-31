# linux系统命令
### MAC OS  

- 运行gulp命令时，提示必须使用权限不够；   
  原因（应该是）：git下载的文件，当前登录用户没有操作的权限;  
  
  通过`chown`命令将`folder`文件夹的所有权移动给`chj`， `-R`（<b>大写</b>）是表示递归`folder`下所有的文件，这样就不会报权限不够的错误了。
  ``` 
  sudo chown -R chj /Users/chj/Desktop/folder/
  ```
  [chown参考文档](http://man.linuxde.net/chmod)


 