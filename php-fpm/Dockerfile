FROM php:7.0-fpm

RUN apt-get update && apt-get install -y \
    libicu-dev libmcrypt-dev \
    mysql-client \
    git \
    libzip-dev \
    zip \
    unzip \
    libpng-dev \
    zlib1g-dev procps libgmp-dev wget

RUN docker-php-ext-configure zip --with-libzip
RUN docker-php-ext-configure pdo_mysql --with-pdo-mysql=mysqlnd
RUN docker-php-ext-install intl mbstring mcrypt pcntl pdo_mysql zip opcache gd
RUN docker-php-ext-configure gmp
RUN docker-php-ext-install gmp

RUN pecl install -o -f redis \
    &&  rm -rf /tmp/pear \
    && docker-php-ext-enable redis

RUN curl --silent --show-error https://getcomposer.org/installer | php && \
    mv composer.phar /usr/local/bin/composer

RUN wget https://getcomposer.org/download/1.10.13/composer.phar && \
    chmod +x composer.phar && \
    mv composer.phar /usr/local/bin/composer1

RUN mv "$PHP_INI_DIR/php.ini-production" "$PHP_INI_DIR/php.ini"

RUN echo 'alias pp="php Console/cake.php acl_extras aco_sync && php Console/cake.php acl_table updatePermissions"' >> ~/.bashrc

ENV APP_HOME /var/www/html

#change uid and gid of apache to docker user uid/gid
RUN usermod -u 1000 www-data && groupmod -g 1000 www-data

RUN chown -R www-data:www-data $APP_HOME

WORKDIR "/var/www/html/ecom"