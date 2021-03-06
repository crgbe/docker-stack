FROM php:7.1-apache

ARG owner=crgbe
ARG dockergrp=docker
ARG projectdir=/var/www/fixi

WORKDIR ${projectdir}

# Update
RUN apt-get -y update --fix-missing && \
    apt-get upgrade -y && \
    apt-get --no-install-recommends install -y apt-utils && \
    rm -rf /var/lib/apt/lists/*


# Install useful tools and important libaries and set pecl extensions dir to php.ini
RUN apt-get -y update && \
    apt-get -y --no-install-recommends install nano wget \
dialog \
libsqlite3-dev \
libsqlite3-0 \
unzip && \
    apt-get -y --no-install-recommends install default-mysql-client \
zlib1g-dev \
libzip-dev \
libicu-dev && \
    apt-get -y --no-install-recommends install --fix-missing apt-utils \
build-essential \
git \
curl \
libonig-dev && \
    apt-get -y --no-install-recommends install --fix-missing libcurl4 \
libcurl4-openssl-dev \
zip \
openssl && \
    pecl config-set php_ini /usr/local/etc/php/php.ini && \
    apt-get -y --no-install-recommends install acl && \
    rm -rf /var/lib/apt/lists/* && \
    curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer


# Install xdebug
RUN pecl install xdebug-2.8.0 && \
    docker-php-ext-enable xdebug


# Install imagick
RUN apt-get -y update && \
    apt-get -y --no-install-recommends install --fix-missing libmagickwand-dev && \
    rm -rf /var/lib/apt/lists/* && \
    pecl install imagick && \
    docker-php-ext-enable imagick

# Install mongodb extension and others pecl extensions
RUN apt-get -y update && \
    apt-get -y install --no-install-recommends libssl-dev && \
    pecl install mongodb && \
    echo "extension=mongodb.so" >> /usr/local/etc/php/conf.d/mongodb.ini && \
    pecl install apcu_bc-stable &&\
    echo "extension=apcu.so\nextension=apc.so\napc.shm_size=256M\napc.ttl=7200\napc.enable_cli=1\napc.gc_ttl=3600\napc.entries_hint=4096\napc.slam_defense=1\napc.serializer=php\n" >> /usr/local/etc/php/conf.d/apcu.ini &&\
    rm -rf /var/lib/apt/lists/* &&\
    docker-php-ext-enable mongodb &&\
    docker-php-ext-enable apcu &&\
    docker-php-ext-enable apc

# Other PHP7 Extensionsextension=apc.so
RUN docker-php-ext-install pdo_mysql && \
    docker-php-ext-install pdo_sqlite && \
    docker-php-ext-install mysqli && \
    docker-php-ext-install curl && \
    docker-php-ext-install tokenizer && \
    docker-php-ext-install json && \
    docker-php-ext-install zip && \
    docker-php-ext-install -j$(nproc) intl && \
    docker-php-ext-install mbstring && \
    docker-php-ext-install gettext && \
    docker-php-ext-install exif && \
    docker-php-ext-install sockets

# Enable apache modules
RUN a2enmod rewrite headers

# Cleanup
RUN rm -rf /usr/src/*

# Installing sudo user
RUN su - && \
    apt-get -y update && \
    apt-get -y --no-install-recommends install sudo && \
    rm -rf /var/lib/apt/lists/* && \
    sudo addgroup --gid 1024 ${dockergrp} && \
    sudo adduser --gecos 'GECOS' --force-badname --gid 1024 ${owner} && \
    sudo passwd -d ${owner} && \
    sudo usermod -a -G ${dockergrp} ${owner} && \
    sudo usermod -a -G sudo ${owner} &&\
    sudo chown -R ${owner} ${projectdir} && \
    sudo chmod -R u+rwx ${projectdir}

#USER ${owner}