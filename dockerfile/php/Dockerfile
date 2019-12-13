FROM php:7.4-fpm-alpine3.10

RUN echo 'http://mirrors.aliyun.com/alpine/latest-stable/main' > /etc/apk/repositories

RUN apk update && apk upgrade

ENV TIMEZONE Asia/Shanghai
RUN apk add tzdata
RUN ln -snf /usr/share/zoneinfo/$TIMEZONE /etc/localtime
RUN echo $TIMEZONE > /etc/timezone 
ENV PHPREDIS_VERSION 5.1.1

RUN docker-php-source extract && \
  curl -L -o /tmp/redis.tar.gz https://github.com/phpredis/phpredis/archive/$PHPREDIS_VERSION.tar.gz && \
  tar xfz /tmp/redis.tar.gz && \
  rm -r /tmp/redis.tar.gz && \
  mkdir -p /usr/src/php/ext && \
  mv phpredis-$PHPREDIS_VERSION /usr/src/php/ext/redis && \
  docker-php-ext-install redis && \
  docker-php-ext-install pdo && \
  docker-php-ext-install pdo_mysql && \
  docker-php-ext-install mbstring && \
  docker-php-ext-install tokenizer && \
  docker-php-source delete && \
  rm -rf /var/cache/apk/*

RUN apk add --update --no-cache autoconf g++ imagemagick-dev imagemagick pcre-dev libtool make \
    && pecl install imagick \
    && docker-php-ext-enable imagick \
    && apk del autoconf g++ libtool make pcre-dev

RUN apk add --update --no-cache \
        freetype-dev \
        libjpeg-turbo-dev \
        libmcrypt-dev \
        libpng-dev \
    && docker-php-ext-install -j"$(getconf _NPROCESSORS_ONLN)" iconv \
    && docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ \
    && docker-php-ext-install -j"$(getconf _NPROCESSORS_ONLN)" gd
