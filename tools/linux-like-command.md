# linux系统命令
### MAC OS  

- 运行gulp命令时，提示必须使用权限不够；   
  原因（应该是）：git下载的文件，当前登录用户没有操作的权限;  
  
  通过`chown`命令将`folder`文件夹的所有权移动给`chj`， `-R`（**大写**）是表示递归`folder`下所有的文件，这样就不会报权限不够的错误了。
  ``` 
  sudo chown -R chj /Users/chj/Desktop/folder/
  ```
  [chown参考文档](http://man.linuxde.net/chmod)

- 在终端中退出文件编辑时，需要先按`ESC`，然后在运行`:wq`保存并推出；

- 软件安装相关命令
    - yum（Yellow dog Updater,Modified）命令：`yum install nginx`这样就装好nginx了。*基于RPM包管理，能够从指定的服务器自动下载RPM包并且安装，可以自动处理依赖性关系，并且一次安装所有依赖的软件包，无须繁琐地一次次下载、安装。*
    - rpm（The RPM Package Manager）是在Linux下广泛使用的软件包管理器。最早由Red Hat研制，现在也由开源社区开发。RPM仅适用于安装用RPM来打包的软件。`rpm -ivh nginx-release.rpm`，ivh（按路径安装并显示进度）：i（install）,v（verbose：安装路径）,h（hash：显示进度）
    - wget（GUN Wget）它的名字是“World Wide Web”和“Get”的结合，同时也隐含了软件的主要功能。目前它支持通过HTTP、HTTPS，以及FTP这三个最常见的TCP/IP协议协议下载。`wget https://nginx.org/download/nginx-1.15.5.tar.gz`