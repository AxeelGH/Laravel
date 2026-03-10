# Usa imagen oficial PHP con Apache
FROM php:8.2-apache

RUN apk update && apk add curl && \
  curl -sS https://getcomposer.org/installer | php \
  && chmod +x composer.phar && mv composer.phar /usr/local/bin/composer

RUN apk --no-cache add --virtual .build-deps $PHPIZE_DEPS \
  && apk --no-cache add --virtual .ext-deps libmcrypt-dev freetype-dev \
  libjpeg-turbo-dev libpng-dev libxml2-dev msmtp bash openssl-dev pkgconfig \
  && docker-php-source extract \
  && docker-php-ext-configure gd --with-freetype-dir=/usr/include/ \
                                   --with-png-dir=/usr/include/ \
                                   --with-jpeg-dir=/usr/include/ \
  && docker-php-ext-install gd mcrypt mysqli pdo pdo_mysql zip opcache \
  && pecl install mongodb redis xdebug \
  && docker-php-ext-enable mongodb \
  && docker-php-ext-enable redis \
  && docker-php-ext-enable xdebug \
  && docker-php-source delete \
  && apk del .build-deps

WORKDIR /var/www/html

COPY composer.json composer.lock ./
RUN composer install --no-scripts --no-autoloader

COPY . .
RUN chmod +x artisan

RUN composer dump-autoload --optimize && composer run-script post-install-cmd


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