FROM php:7.4.30-fpm-alpine
ENV PHPIZE_DEPS \
    autoconf \
    cmake \
    file \
    g++ \
    gcc \
    libc-dev \
    pcre-dev \
    pkgconf \
    re2c
RUN apk update
RUN sed -i "s/dl-cdn.alpinelinux.org/mirrors.aliyun.com/g" /etc/apk/repositories \
    # && apk add --no-cache --virtual .persistent-deps \
    && apk add --no-cache \
    # for intl extension
    icu-dev \
    # for mcrypt extension
    libmcrypt \
    libmcrypt-dev \
    # libmagickcore-dev \
    # libmagickwand-dev \
    # imagemagick \
    # for databases
    postgresql-dev \
    mysql-client \
#    postgresql-client \
    # for gd extension
    libwebp-dev \
    # libfreetype6-dev \
    freetype-dev \
    libjpeg-turbo-dev \
    libpng-dev \
    # zip extension
    libzip-dev \
    bzip2-dev \
    zip \
    zlib-dev \
    make \
    gettext \
    libevent \
    libevent-dev \
    git \
    libmemcached \
    libmemcached-dev 
    # Lightweight ssh client to ensure low-cost services communications
    # dropbear-ssh

# Install Requirements
RUN set -xe \
#  && apk add --no-cache --virtual .build-deps \
 && apk add --no-cache \
    $PHPIZE_DEPS \
    # zlib-dev \
    wget \
    openssh \
    # openssh-server \
    openrc 
RUN php -r "copy('https://install.phpcomposer.com/installer', 'composer-setup.php');" \
    && php composer-setup.php \
    && php -r "unlink('composer-setup.php');" \
    && mv composer.phar /usr/local/bin/composer
RUN pecl install redis \
    && pecl install mcrypt\
    && pecl install xdebug \
    # && pecl install nsq \
    && pecl install mongodb \
    # && pecl install imagick \
    && docker-php-ext-enable mcrypt redis xdebug mongodb
RUN docker-php-ext-configure pdo_pgsql --with-pdo-pgsql \ 
    && docker-php-ext-configure pdo_mysql --with-pdo-mysql \
    && docker-php-ext-configure mysqli --with-mysqli=mysqlnd \
    && docker-php-ext-configure zip \
    && docker-php-ext-configure bz2 --with-bz2 \
    && docker-php-ext-configure gd --with-freetype --with-jpeg \
    && docker-php-ext-install -j$(nproc) \
    pdo_mysql \
    pdo_pgsql \
    mysqli \
    # mongodb \
    # bcmath \
    zip \
    bz2 \
    gd
RUN wget -O /usr/src/memcached.tar.gz https://github.com/php-memcached-dev/php-memcached/archive/refs/tags/v3.2.0.tar.gz \
    && cd /usr/src/ && tar -xzvf memcached.tar.gz \
    && cd php-memcached-3.2.0/ \
	&& phpize \
    && ./configure \
    && make \
    && make install
ADD php/php.ini /usr/local/etc/php
ADD php/docker-php-ext-xdebug.ini /usr/local/etc/php/conf.d