---
title: Nginx负载均衡配置 
tags: nginx,负载均衡
date: 2018-8-05 20:11:10
grammar_cjkRuby: true
---
nging.conf的配置如下
``` shell
user  www www;
worker_processes  2;
error_log  logs/error.log;
error_log  logs/error.log  notice;
error_log  logs/error.log  info;
pid        logs/nginx.pid;
events {
    use epoll;
    worker_connections  1024;
}
http {
    	include	mime.types;
    	default_type	application/octet-stream;
   		sendfile	on;
		client_header_timeout 10;
        client_body_timeout 10;
        reset_timedout_connection on;
        send_timeout 10;
        tcp_nodelay on;
    	tcp_nopush  on;
    	keepalive_timeout  20;
		ssi on;
        ssi_silent_errors on;
		gzip on;
        gzip_min_length  1k;
        gzip_buffers     4 16k;
        gzip_http_version 1.1;
        gzip_comp_level 2;
        gzip_types       text/plain application/javascript application/html pplication/x-javascript text/css application/xml;
        gzip_vary on;
		upstream api {   
    		server   192.168.1.55:18088 weight=5;
    		server   192.168.1.58:18088;
		}   
      	include vhosts-api.conf;
}
```
vhosts-api.conf配置如下

``` shell
server
  {
    listen      18080;
    server_name api.allinmed.cn;
    location / {
        proxy_pass http://api;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        client_max_body_size    20m;
        proxy_connect_timeout   300;
        proxy_send_timeout      300;
        proxy_read_timeout      300;
        client_body_buffer_size 128k;
		access_log  /usr/local/nginx/logs/access.log;
		error_log   /usr/local/nginx/logs/error.log;
	}
}
```
说明：在`nging.conf`中配置一个服务`api`，转发的目标是如下配置的`192.168.1.55、192.168.1.58`，同时配置了`192.168.1.55`的权重是5，这样一般会是转发给`192.168.1.55`5个请求，转发给`192.168.1.58`1个请求。通过`include vhosts-api.conf`加载`api`这个转发的具体配置，可以看到`vhosts-api.conf`监听的是`18080`端口，名称是`api.allinmed.cn`，转发的是`/`即所有的请求