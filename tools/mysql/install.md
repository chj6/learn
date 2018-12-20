# 安装mysql记录（centos）

### 1.下载包文件
`wget http://dev.mysql.com/get/mysql-community-release-el7-5.noarch.rpm `

### 2.安装
`rpm -ivh mysql-community-release-el7-5.noarch.rpm`

### 3.启动mysql的服务
`service mysqld start`   

*注意：在阿里云的centos7.4版本上运行时会提示：`Failed to start mysqld.service: Unit not found.`，解决办法：`yum install -y mariadb-server`，然后再允许`service mysqld start`即可。*    

在运行`yum install -y mariadb-server`会提示：
```
已加载插件：fastestmirror, langpacks
/var/run/yum.pid 已被锁定，PID 为 13731 的另一个程序正在运行。
Another app is currently holding the yum lock; waiting for it to exit...
  另一个应用程序是：PackageKit
    内存：153 M RSS （567 MB VSZ）
    已启动： Thu Dec 20 17:35:19 2018 - 02:21之前
    状态  ：睡眠中，进程ID：13731
```
需要执行`sudo kill -s 9 13731`，然后再执行`yun install -y mariadb-server`

### 4.更改root密码
`mysqladmin -u root password 'yourpassword'`

### 5.设置root可以远程登陆  
  默认mysql是无法使用**MySqlWorkbench**远程登陆的，需要修改root的host值。   
  
  a.首先进入mysql
```
mysql -uroot -p
```
b.依次执行如下sql
```
use mysql
select t.host from user t where t.user='root';
update user set host='%' where user='root';
```

### 6.重启mysql
`service mysqld restart`

这样就可以使用远程客户端连接mysql了。
