FROM php:8.1.11-fpm-alpine3.16

# dependencies required for running "phpize"
# these get automatically installed and removed by "docker-php-ext-*" (unless they're already installed)
ENV PHPIZE_DEPS \
        autoconf \
        dpkg-dev \
        dpkg \
        file \
        g++ \
        gcc \
        libc-dev \
        make \
        pkgconf \
        re2c \
        wget

# Install packages
RUN set -eux; \
    # Packages needed only for build
    apk add --no-cache --virtual .build-deps \
        $PHPIZE_DEPS \
    && \
    # Packages to install
    apk add --no-cache \
        bzip2-dev \
        ca-certificates \
        curl \
        fcgi \
        freetype-dev \
        gettext-dev \
        icu-dev \
        icu-data-full \
        imagemagick \
        imagemagick-dev \
        libjpeg-turbo-dev \
        libmcrypt-dev \
        libpng \
        libpng-dev \
        libressl-dev \
        libtool \
        libwebp-dev \
        libxml2-dev \
        libzip-dev \
        oniguruma-dev \
        unzip \
        su-exec \
        gifsicle \
        jpegoptim \
        libwebp-tools \
        nano \
        optipng \
        mysql-client \        
    && \
    # pecl PHP extensions
    pecl install \
        redis \
        imagick \
    && \
    # install imagick
    # use github version for now until release from https://pecl.php.net/get/imagick is ready for PHP 8
    # ref: https://github.com/Imagick/imagick/issues/358
    #mkdir -p /usr/src/php/ext/imagick \
    #&& \
    #curl -fsSL https://github.com/Imagick/imagick/archive/06116aa24b76edaf6b1693198f79e6c295eda8a9.tar.gz | tar xvz -C "/usr/src/php/ext/imagick" --strip 1 \
    #&& \
    # Configure PHP extensions
    docker-php-ext-configure \
        # ref: https://github.com/docker-library/php/issues/920#issuecomment-562864296
        gd --enable-gd --with-freetype --with-jpeg --with-webp \
    && \
    # Install PHP extensions
    docker-php-ext-install \
        bcmath \
        bz2 \
        exif \
        ftp \
        gettext \
        gd \
        intl \
        mbstring \
        opcache \
        pdo \
        shmop \
        soap \
        sockets \
        sysvmsg \
        sysvsem \
        sysvshm \
        zip \
        pdo_mysql \
    && \
    # Enable PHP extensions
    docker-php-ext-enable \
        imagick \
        redis \
    && \
    # Install Composer
    curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin/ --filename=composer \
    && \
    # Remove the build deps
    apk del .build-deps \
    && \
    # Clean out directories that don't need to be part of the image
    rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

# https://github.com/docker-library/php/issues/1121
#ENV LD_PRELOAD /usr/lib/preloadable_libiconv.so php

# install languages
ENV MUSL_LOCALE_DEPS cmake make musl-dev gcc gettext-dev libintl 
ENV MUSL_LOCPATH /usr/share/i18n/locales/musl

RUN apk add --no-cache \
    $MUSL_LOCALE_DEPS \
    && wget https://gitlab.com/rilian-la-te/musl-locales/-/archive/master/musl-locales-master.zip \
    && unzip musl-locales-master.zip \
      && cd musl-locales-master \
      && cmake -DLOCALE_PROFILE=OFF -D CMAKE_INSTALL_PREFIX:PATH=/usr . && make && make install \
      && cd .. && rm -r musl-locales-master

# Copy the `zzz-docker-php.ini` file into place for php
COPY zzz-docker-php.ini /usr/local/etc/php/conf.d/

# Copy the `zzz-docker-php-fpm.conf` file into place for php-fpm
COPY zzz-docker-php-fpm.conf /usr/local/etc/php-fpm.d/