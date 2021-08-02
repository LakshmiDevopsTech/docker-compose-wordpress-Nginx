# Docker-compose Wordpress with Nginx
## Description
Its a compose file for wordpress setup with nginx. Docker Compose manages to simplify the installation process to a single deployment command greatly reducing the time and effort required.
## Features
- Easy to deploy a container using compose file. We can create more than one containers using a compose file.
- compose file writing in a .yml file
- In this project creating extra network other than Default docker network for wordpress.
- creating Volumes and mounting in to container.
- The requests to .php files will hit to PHP-FPM container
## Pre-Requests
- Install Docker and docker-compose on your machine.
## Docker-compose
Docker installation
```
# curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
#chmod +x /usr/local/bin/docker-compose
# docker-compose version  <--- for checking status.
```
Different Section in the compose file:
- Version
- services
- volumes
- networks

network Commands:
```
#docker network create <network_name> <--- to create a network
#docker network ls <-- list available networks
```

## Build containers from docker-compose file
The availble data's in my current working directory is:
```
docker-nginx-fpm]# ll
-rw-r--r-- 1 root root 1007 Jul 30 11:25 docker-compose.yml
-rw-r--r-- 1 root root  673 Jul 30 11:28 nginx.conf
[root@ip-172-31-33-253 docker-nginx-fpm]#
```
Build container using compose file:
```
docker-compose up -d
```
## Behind the playbook
vim docker-compose.yml
```
version: "3"
services:
  database:  <---------------------database container
    image: mysql:5.6
    container_name: database
    restart: always
    volumes:
      - mysql-volume:/var/kib/mysql  <--------- mounted volume
    networks:
      - wp-net    <--------assigned network for database cintainer
    environment:
      MYSQL_ROOT_PASSWORD: rootpass@123
      MYSQL_DATABASE: wordpress
      MYSQL_USER: wordpress
      MYSQL_PASSWORD: wordpress

  wordpress:
    image: wordpress:php7.3-fpm-alpine  <--------- wordpress container with FPM
    container_name: wordpress
    restart: always
    volumes:
      - wp-volume:/var/www/html
    depends_on:
      - database
    environment:
      WORDPRESS_DB_HOST: database
      WORDPRESS_DB_USER: wordpress
      WORDPRESS_DB_PASSWORD: wordpress
      WORDPRESS_DB_NAME: wordpress
    networks:
      - wp-net

  nginx:         
    image: nginx:alpine  <-------------------Nginx container
    restart: always
    container_name: nginx
    volumes:
      - wp-volume:/var/www/html
      - ./nginx.conf:/etc/nginx/conf.d/default.conf <----- configuration file
    networks:
      - wp-net
    ports:
      - 80:80  <------------- port mapped
networks:   <----------------- network
  wp-net:
volumes:  <------------------ volumes
  mysql-volume:
  wp-volume:
```
vim nginx.conf
```
server {
  listen 80;
  listen [::]:80;
  access_log off;
  root /var/www/html;
  index index.php;
  server_name example.com;
  server_tokens off;
  location / {
    # First attempt to serve request as file, then
    # as directory, then fall back to displaying a 404.
    try_files $uri $uri/ /index.php?$args;
  }
  # pass the PHP scripts to FastCGI server listening on wordpress:9000
  location ~ \.php$ {
    fastcgi_split_path_info ^(.+\.php)(/.+)$;
    fastcgi_pass wordpress:9000;  <--- here will mention, id any request for.php file, pass to the fpm container (here its is wordpress)
    fastcgi_index index.php;
    include fastcgi_params;
    fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
    fastcgi_param SCRIPT_NAME $fastcgi_script_name;
  }
}
```
##### screenshots
![alt text](https://i.ibb.co/kKj249G/docker-compose.png)
![alt_text](https://i.ibb.co/8rMxfwK/docker-compose1.png)
![alt_text](https://i.ibb.co/D7Z6pR9/container.png)


## Conclusion
It's just a simple wordpress application with Nginx. Please contact me if you need any more details.
### ⚙️ Connect with Me

<p align="center">
<a href="mailto:lakshmipriya458@gmail.com"><img src="https://img.shields.io/badge/Gmail-D14836?style=for-the-badge&logo=gmail&logoColor=white"/></a>
<a href="https://www.linkedin.com/in/lakshmipriya-p-c-7b5a2b88/"><img src="https://img.shields.io/badge/LinkedIn-0077B5?style=for-the-badge&logo=linkedin&logoColor=white"/></a>  
