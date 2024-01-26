FROM php:8.1-fpm-alpine

ENV DEBIAN_FRONTEND noninteractive

ENV LIBRARY_PATH=/lib:/usr/lib

RUN set -x \
    && apk update \
    && apk upgrade \
    && apk --no-cache add pcre-dev ${PHPIZE_DEPS} \
    && apk add curl-dev \
    libmemcached-dev \
    unzip \
    libzip-dev \
    zip \
    ca-certificates \
    wget \
    less \
    nano \
    mc \
    sudo \
    git \
    imagemagick \
    imagemagick-dev \
    jpeg-dev \
    libpng-dev \
    libxml2-dev \
    zlib-dev \
    && pecl install apcu-5.1.21 imagick memcached \
    && mkdir /run/php \
    && docker-php-ext-install pdo_mysql exif pcntl bcmath gd xml zip curl \
# THIS DELETION IS BREAKS IMAGICK
#    && apk del pcre-dev ${PHPIZE_DEPS} \
    && rm -rf /var/cache/apk/*

RUN docker-php-ext-enable apcu opcache imagick memcached

RUN curl --silent --show-error https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer \
    && mkdir -pv /local/logs \
    && echo "www-data ALL=(ALL) NOPASSWD: ALL" >> /etc/sudoers.d/www-data

COPY php /

RUN mkdir /local/www/  \
    && chown -R www-data:www-data /local/www \
    && chown -R www-data:www-data /home/www-data

USER www-data
VOLUME /local/www

EXPOSE 80 443 3306

