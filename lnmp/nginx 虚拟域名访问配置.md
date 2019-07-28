# nginx 虚拟域名访问配置

```json
server {
    listen       80;
    server_name  ericgu178;
    charset      utf-8;
    root /home/web/ericgu178/public;
    index  index.html index.htm index.php;
    error_page  404              /404.html;
    location = /404.html {
        return 404 '阿拉，文件居然找不到了';
    }
    error_page  500 502 503 504  /50x.html;
    location = /50x.html {
        root   /usr/share/nginx/html; # windows用户替换这个目录
    }
    location / {
        try_files $uri @rewrite;
    }
    # 如果为真 走pathinfo 如果是假的 就不走 直接读取文件内容
    location @rewrite {
        set $static 0;
        if  ($uri ~ \.(css|js|jpg|jpeg|png|gif|ico|woff|eot|svg|css\.map|min\.map)$) {
            set $static 1;
        }
        if ($static = 0) {
            rewrite ^/(.*)$ /index.php?s=/$1;
        }
    }
    location ~ /Uploads/.*\.php$ {
        deny all;
    }
    location ~ \.php/ {
       if ($request_uri ~ ^(.+\.php)(/.+?)($|\?)) { }
       fastcgi_pass 127.0.0.1:9000;
       include fastcgi_params;
       fastcgi_param SCRIPT_NAME     $1;
       fastcgi_param PATH_INFO       $2;
       fastcgi_param SCRIPT_FILENAME $document_root$1;
    }
    location ~ \.php$ {
        fastcgi_pass 127.0.0.1:9000;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        include fastcgi_params;
    }
    location ~ /\.ht {
        deny  all;
    }
}
```

