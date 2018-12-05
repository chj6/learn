# nginx 配置文件

nginx 配置说明：
  
  具体的文件路径：nginx-版本号\conf\nginx.conf（windows）  
  [日志格式参考链接](https://docs.nginx.com/nginx/admin-guide/monitoring/logging/)

常用命令：   
- 查看配置文件路径`nginx -t`
- 启动nginx时指定配置路径`nginx -c /your/path/nginx.conf`，前提是，需要先停止`nginx -s stop`    

注意点： 
- nginx在linux服务器上，有时会遇到权限问题，提示：**403 Forbidden**，需要设置文件位置的跟目标权限，比如：`chmod 755 /root/website`

个人总结：
- nginx代理服务器：就是指访问同一个ip时，根据不同的server_name或listen的端口号，去转发到不同的服务器或者目录上去。


```
# 开发环境配置接口转发，静态服务器
http {
    include       mime.types;
    default_type  application/octet-stream;

    sendfile        on;
    keepalive_timeout  65;

    #gzip  on;
    #gzip_types text/plain application/x-javascript text/css application/xml text/javascript application/x-httpd-php image/jpeg image/gif image/png;
    
    # 代理服务器 localhost
    server {
        listen       80;
        server_name  localhost;

        #charset koi8-r;
        #access_log  logs/host.access.log  main;
        
        location / {
            root html;
            index index.html;
        }

        # 静态资源文件访问 http://localhost/productname
        location /productname {
            # 不设置alias，默认请求路径是nginx的安装目录
            alias E:/WorkSpace/SourceCode/webapp;
            index index.html;

            # 日志：访问记录 格式一般用默认的就可以了
            access_log  logs/host.access.productname.log;

        }

        # 接口代理访问 http://localhost/api 解决跨域问题
        location /api {
            proxy_pass http://192.168.1.102:8080/api/;
            proxy_set_header Origin *;
            #add_header 'Access-Control-Allow-Methods' 'POST,GET,OPTIONS';
            proxy_set_header   X-Real-IP        $remote_addr;
            proxy_set_header   X-Forwarded-For  $proxy_add_x_forwarded_for;
            # cookie传递，防止cookie丢失
            proxy_cookie_path  /api /api;
            #access_log /var/log/nginx/api-upstream-access.log upstreamlog;
        }

        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }
    }

 #代理服务器 IP地址: 192.168.1.101
 server {
        listen       80;
        server_name  192.168.1.101;

        #access_log  logs/host.access.log  main;
        location / {
            root html;
            index index.html;
        }

        # 静态资源文件访问 http://192.168.1.101/productname
        location /productname {
            # 不设置alias，默认请求路径是nginx的安装目录
            alias E:/WorkSpace/SourceCode/webapp;
            index index.html;
        }

        # 静态资源文件访问 http://192.168.1.101/api 解决跨域问题
        location /api {
            proxy_pass http://192.168.1.102:8080/api/;

            proxy_set_header Origin *;
            #add_header 'Access-Control-Allow-Methods' 'POST,GET,OPTIONS';
            proxy_set_header   X-Real-IP        $remote_addr;
            proxy_set_header   X-Forwarded-For  $proxy_add_x_forwarded_for;
            # cookie传递，防止cookie丢失
            proxy_cookie_path  /api /api;
            # 访问日志记录
            #access_log /var/log/nginx/api-upstream-access.log upstreamlog;
        }
    }
}
```

```
# 同一个ip的域名代理配置

# 访问api.chj1.com转发到本机的8080端口
server{
  listen  80;
  server_name api.chj1.com;
  access_log /usr/local/nginx/logs/api.access.log combined;
  location /{
     proxy_pass http://127.0.0.1:8080/;
     add_header Access-Control-Allow-Origin *;
  }
}

# 访问www.chj1.com就直接访问本地的index.html
server{
    listen 80;
    server_name www.chj1.com;
    access_log /usr/local/nginx/logs/access.log combined;
    location / {
        root index.html;
        add_header Access-Control-Allow-Origin *;
    }
}

# 访问images.chj1.com就直接访问本地的/data/images目录
server{
    listen 80;
    server_name images.chj1.com;
    access_log /usr/local/nginx/logs/imges.access.log combined;
    location /{
        root /data/images/;
        add_header Access-Control-Allow-Origin *;
    }
}
```
