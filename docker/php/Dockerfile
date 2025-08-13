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