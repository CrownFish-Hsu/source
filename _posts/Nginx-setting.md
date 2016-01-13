title: Nginx端口和伪静态
categories: TECH
tags: [Nginx, Composer]
---

### Nginx端口配置
在学习composer写一个类似Laravel的框架，Laravel的强大之处就在于便捷的路由机制和方便的包引用。大的框架已经搭好，稍后附上git地址。

nginx路径：/usr/local/etc/nginx
配置文件：nginx.conf
```linux
worker_processes  1;
error_log   /usr/local/var/logs/nginx/error.log debug;
pid         /usr/local/var/run/nginx.pid;
events {
  worker_connections  256;
}

http {
  include          mime.types;
  default_type  application/octet-stream;
  log_format main '$remote_addr - $remote_user [$time_local] '
    '"$request" $status $body_bytes_sent '
    '"$http_referer" "$http_user_agent" '
    '"$http_x_forwarded_for" $host $request_time $upstream_response_time $scheme '
    '$cookie_evalogin';
  	access_log  /usr/local/var/logs/access.log  main;
	  sendfile              on;
	  keepalive_timeout  65;
	  port_in_redirect off;
	  include /usr/local/etc/nginx/sites-enabled/*;
}
```

在site-enabled里新建default.conf文件,
```linux
server {
  listen        81;
  server_name   localhost;
  root          /Users/xcy/Projects/www/pfc/public;
  location / {
    index  index.html index.htm index.php;
    include      /usr/local/etc/nginx/conf.d/php-fpm;
  }

  location ~ .*\.(php|php5)?$
  {
      try_files $uri =404;
      fastcgi_pass  unix:/tmp/php-cgi.sock;
      fastcgi_index index.php;
  }
}
```

### Nginx伪静态
访问127.0.0.1：81没问题，但是再带上其他参数都报404，查询了下，说是nginx伪静态问题，需要rewrite
在site-enabled/default.conf新增一行 [try_files $uri $uri/ /index.php](http://nginx.org/en/docs/http/ngx_http_core_module.html#try_files);
```linux
server {
  listen        81;
  server_name   localhost;
  root          /Users/xcy/Projects/www/pfc/public;
  location / {
    index  index.html index.htm index.php;
    include      /usr/local/etc/nginx/conf.d/php-fpm;
    try_files $uri $uri/ /index.php;
  }

  location ~ .*\.(php|php5)?$
  {
      try_files $uri =404;
      fastcgi_pass  unix:/tmp/php-cgi.sock;
      fastcgi_index index.php;
  }
}
```