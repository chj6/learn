# 安装mysql记录（centos）

### 1.下载包文件
`wget http://dev.mysql.com/get/mysql-community-release-el7-5.noarch.rpm `

### 2.安装
`rpm -ivh mysql-community-release-el7-5.noarch.rpm`

### 3.启动mysql的服务
`service mysqld start`

### 4.更改root密码
`mysqladmin -u root password 'yourpassword'`

### 5.设置root可以远程登陆  
  默认mysql是无法使用**MySqlWorkbench**远程登陆的，需要修改root的host值
```
use mysql
select t.host from user t where t.user='root';
update user set host='%' where user='root';
```

### 6.重启mysql
`service mysqld restart`

这样就可以使用远程客户端连接mysql了。