FROM php:7.2-fpm

RUN apt-get update && apt-get install -y \
    libicu-dev \
    libmcrypt-dev \
    default-mysql-client \
    git \
    libzip-dev \
    zip \
    unzip \
    libpng-dev \
    zlib1g-dev procps libgmp-dev wget

RUN docker-php-ext-configure zip --with-libzip
RUN docker-php-ext-configure pdo_mysql --with-pdo-mysql=mysqlnd
RUN docker-php-ext-install intl mbstring pcntl pdo_mysql zip opcache gd

RUN pecl install mcrypt-1.0.3 \
    && docker-php-ext-enable mcrypt

RUN pecl install -o -f redis \
    &&  rm -rf /tmp/pear \
    && docker-php-ext-enable redis

ENV LIBRDKAFKA_VERSION v0.11.0
RUN apt-get update \
    && apt-get install -y build-essential libsasl2-dev libssl-dev python-minimal \
    && cd /tmp \
    && git clone \
        --branch ${LIBRDKAFKA_VERSION} \
        --depth 1 \
        https://github.com/edenhill/librdkafka.git \
&& cd librdkafka \
    && ./configure \
    && make \
    && make install \
    && pecl install rdkafka \
    && docker-php-ext-enable rdkafka \
    && rm -rf /tmp/librdkafka

RUN curl --silent --show-error https://getcomposer.org/installer | php && \
    mv composer.phar /usr/local/bin/composer

RUN wget https://getcomposer.org/download/1.10.13/composer.phar && \
    chmod +x composer.phar && \
    mv composer.phar /usr/local/bin/composer1

RUN mv "$PHP_INI_DIR/php.ini-production" "$PHP_INI_DIR/php.ini"

ENV APP_HOME /var/www/html

#change uid and gid of apache to docker user uid/gid
RUN usermod -u 1000 www-data && groupmod -g 1000 www-data

RUN chown www-data:www-data $APP_HOME

WORKDIR "/var/www/html/cods"