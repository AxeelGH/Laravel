FROM php:8.2-apache as php

RUN apt-get update -y

RUN apt-get install -y unzip libpq-dev libcurl4-gnutls-dev

RUN docker-php-ext-install pdo pdo_mysql bcmath

RUN apt-get install nano

RUN curl -fsSL https://deb.nodesource.com/setup_16.x | bash - \

&& apt-get install -y nodejs

COPY --from=composer:latest /usr/bin/composer /usr/bin/composer

WORKDIR /var/www/html

# Copia el resto del proyecto
COPY . /var/www/html

# Configura permisos
RUN chown -R www-data:www-data /var/www/html \
    && chmod -R 755 /var/www/html/storage \
    && chmod -R 755 /var/www/html/bootstrap/cache

# Expone el puerto 80
EXPOSE 80

# Apache ya se inicia automáticamente en php:8.2-apache
CMD ["apache2-foreground"]