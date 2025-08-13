# add-docker-laravel

Nginx + PHP-FPM + MySQL

mkdir laravel-docker
cd laravel-docker


2) Arquivos do Docker
docker-compose.yml
Crie este arquivo na raiz:

yaml
Copiar
Editar
services:
  app:
    build: ./.docker/php
    container_name: laravel-app
    volumes:
      - ./:/var/www/html
    depends_on:
      - mysql

  nginx:
    image: nginx:alpine
    container_name: laravel-nginx
    ports:
      - "8000:80"
    volumes:
      - ./:/var/www/html
      - ./.docker/nginx/default.conf:/etc/nginx/conf.d/default.conf
    depends_on:
      - app

  mysql:
    image: mysql:8
    container_name: laravel-mysql
    environment:
      MYSQL_DATABASE: laravel
      MYSQL_USER: laravel
      MYSQL_PASSWORD: laravel
      MYSQL_ROOT_PASSWORD: root
    ports:
      - "3306:3306"
    volumes:
      - mysql_data:/var/lib/mysql

volumes:
  mysql_data:



  ./.docker/php/Dockerfile
Crie a pasta .docker/php e o arquivo:

dockerfile
Copiar
Editar
FROM php:8.3-fpm

RUN apt-get update && apt-get install -y \
    git unzip libpng-dev libonig-dev libxml2-dev libzip-dev libicu-dev \
    && rm -rf /var/lib/apt/lists/*

RUN docker-php-ext-install pdo_mysql mbstring bcmath intl zip

# (Opcional) GD para manipular imagens
RUN docker-php-ext-configure gd && docker-php-ext-install gd

# Composer dentro da imagem
COPY --from=composer:2 /usr/bin/composer /usr/bin/composer

WORKDIR /var/www/html
./.docker/nginx/default.conf
Crie a pasta .docker/nginx e o arquivo:

server {
    listen 80;
    server_name localhost;
    root /var/www/html/public;

    index index.php index.html;

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

    location ~ \.php$ {
        include fastcgi_params;
        fastcgi_pass app:9000;
        fastcgi_param SCRIPT_FILENAME $realpath_root$fastcgi_script_name;
        fastcgi_param DOCUMENT_ROOT $realpath_root;
    }

    client_max_body_size 20M;
}
