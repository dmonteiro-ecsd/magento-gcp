FROM php:7.1-apache

MAINTAINER Daniel Monteiro <Daniel@ecs-digital.co.uk>

RUN a2enmod rewrite

ENV MAGENTO_VERSION 2.1.1

RUN rm -rf /var/www/html/*
COPY magento2.tar.gz .
RUN tar -xvf magento2.tar.gz

RUN curl -sS https://getcomposer.org/installer | php
RUN mv composer.phar /usr/local/bin/composer
RUN requirements="libpng12-dev libmcrypt-dev libmcrypt4 libcurl3-dev libfreetype6 libjpeg62-turbo libjpeg62-turbo-dev libpng12-dev libfreetype6-dev libicu-dev libxslt1-dev" \
    && apt-get update && apt-get install -y $requirements && rm -rf /var/lib/apt/lists/* \
    && docker-php-ext-install pdo_mysql \
    && docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ \
    && docker-php-ext-install gd \
    && docker-php-ext-install mcrypt \
    && docker-php-ext-install mbstring \
    && docker-php-ext-install zip \
    && docker-php-ext-install intl \
    && docker-php-ext-install xsl \
    && docker-php-ext-install soap \
    && requirementsToRemove="libpng12-dev libmcrypt-dev libcurl3-dev libpng12-dev libfreetype6-dev libjpeg62-turbo-dev" \
    && apt-get purge --auto-remove -y $requirementsToRemove

RUN chsh -s /bin/bash www-data
RUN chown -R www-data:www-data /var/www
RUN su www-data -c "cd /var/www/html && composer install"
RUN cd /var/www/html \
    && find . -type d -exec chmod 770 {} \; \
    && find . -type f -exec chmod 660 {} \; \
    && chmod u+x bin/magento

RUN echo "memory_limit=1024M" > /usr/local/etc/php/conf.d/memory-limit.ini

RUN apt-get clean && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

WORKDIR /var/www/html

VOLUME /var/www/html/var
VOLUME /var/www/html/pub