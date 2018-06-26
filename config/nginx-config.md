# nginx 配置文件

nginx 配置说明：
  
  具体的文件路径：nginx-版本号\conf\nginx.conf（windows）  
  [日志格式参考链接](https://docs.nginx.com/nginx/admin-guide/monitoring/logging/)

```
http {
    include       mime.types;
    default_type  application/octet-stream;

    sendfile        on;
    keepalive_timeout  65;

    #gzip  on;

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