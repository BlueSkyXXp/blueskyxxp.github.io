---
title: 如何快速使用wordpress建设一个站点
toc: true
date: 2023-11-30 15:17:20
tags:
categories:
---



```txt
```

```txt
```

```txt
upstream php {
        server php:9000;
}

server {
       
        root /var/www/html;
        index index.php;

        location = /favicon.ico {
                log_not_found off;
                access_log off;
        }

        location = /robots.txt {
                allow all;
                log_not_found off;
                access_log off;
        }

        location / {
             
                try_files $uri $uri/ /index.php?$args;
        }

        location ~ \.php$ {
        
                include fastcgi.conf;
                fastcgi_intercept_errors on;
                fastcgi_pass php;
        }

        location ~* \.(js|css|png|jpg|jpeg|gif|ico)$ {
                expires max;
                log_not_found off;
        }
}
```



```yaml
version: '2'
services:
  nginx:
    image: evild/alpine-nginx:1.11.2-libressl
    container_name: wordpress_nginx
    restart: always
    volumes:
      - wordpress-data:/var/www/html/:ro
      - ./nginx/conf/nginx.conf:/etc/nginx/conf/nginx.conf:ro
      - ./nginx/conf.d:/etc/nginx/conf.d:ro
    ports:
      - 80:80
      - 443:443
    networks:
      - front
  php:
    image: evild/alpine-wordpress:4.5.3
    container_name: wordpress_php
    restart: always
    volumes:
      - wordpress-data:/var/www/html
      - ./php/uploads.ini:/usr/local/etc/php/conf.d/uploads.ini
    environment:
      - WORDPRESS_DB_NAME=wpdb
      - WORDPRESS_TABLE_PREFIX=wp_
      - WORDPRESS_DB_HOST=db
      - WORDPRESS_DB_PASSWORD=password
    networks:
      - front
      - back
  db:
    image: mariadb:10
    container_name: wordpress_mariadb
    restart: always
    volumes:
      - wordpress-db-data:/var/lib/mysql
    environment:
      - MYSQL_ROOT_PASSWORD=password
    networks:
      - back
volumes:
  wordpress-data:
    driver: local
  wordpress-db-data:
    driver: local
networks:
  front:
  back:

```




## 参考资料
> - [docker-compose搭建wordpress](https://gitee.com/modouwifi/docker-compose-wordpress)
> - []()
