FROM yannickvh/php:5.3-fpm
MAINTAINER Yannick Vanhaeren

# Install dependencies
RUN apt-get update && apt-get install -y --no-install-recommends \
        libfreetype6 \
        libjpeg62-turbo \
        libpng12-0 \
        git \
        sudo \
    && apt-get clean \
    && rm -r /var/lib/apt/lists/*

# Add php extensions
RUN set -xe \
    && PHP_BUILD_DEPS=" \
        libfreetype6-dev \
        libjpeg-dev \
        libpng-dev \
        libldap2-dev \
        libsasl2-dev \
    " \
    && apt-get update && apt-get install -y --no-install-recommends $PHP_BUILD_DEPS \
    && ln -s /usr/include/freetype2 /usr/include/freetype2/freetype \
    && docker-php-ext-configure gd --enable-gd-native-ttf --with-jpeg-dir=/usr/lib/x86_64-linux-gnu --with-png-dir=/usr/lib/x86_64-linux-gnu --with-freetype-dir=/usr/lib/x86_64-linux-gnu \
    && docker-php-ext-configure ldap --with-libdir=lib/x86_64-linux-gnu --with-ldap-sasl \
    && docker-php-ext-install -j$(nproc) gd ldap \
    && pecl install apc xdebug-2.2.7 \
    && pecl clear-cache \
    && apt-get purge -y --auto-remove -o APT::AutoRemove::RecommendsImportant=false -o APT::AutoRemove::SuggestsImportant=false $PHP_BUILD_DEPS \
    && rm -rf /usr/include/freetype2

RUN set -xe \
    && docker-php-source extract \
    && cp /usr/src/php/php.ini-development /usr/local/etc/php/php.ini \
    && cd /usr/local/etc/php \
    && sed -i -e "s|max_execution_time = 30|max_execution_time = 60|" php.ini \
    && sed -i -e "s|memory_limit = 128M|memory_limit = 512M|" php.ini \
    && sed -i -e "s|post_max_size = 8M|post_max_size = 55M|" php.ini \
    && sed -i -e "s|upload_max_filesize = 2M|upload_max_filesize = 50M|" php.ini \
    && sed -i -e "s|;date.timezone =|date.timezone = Europe/Brussels|" php.ini \
    && sed -i -e "s|;cgi.fix_pathinfo=1|cgi.fix_pathinfo = 0|" php.ini \
    && docker-php-source delete

RUN set -xe \
    && cd /usr/local/etc/php/conf.d \
    && echo "extension=$(find /usr/local/lib/php/extensions/ -name apc.so)" > apc.ini \
    && echo "apc.enabled=1" >> apc.ini \
    && echo "apc.shm_segments=1" >> apc.ini \
    && echo "apc.shm_size=128M" >> apc.ini \
    && echo "apc.ttl=7200" >> apc.ini \
    && echo "apc.enable_cli=0" >> apc.ini

RUN set -xe \
    && cd /usr/local/etc/php/conf.d \
    && echo ";zend_extension=$(find /usr/local/lib/php/extensions/ -name xdebug.so)" > xdebug.ini \
    && echo "xdebug.default_enable=1" >> xdebug.ini \
    && echo "xdebug.profiler_enable=0" >> xdebug.ini \
    && echo "xdebug.idekey=PHPSTORM" >> xdebug.ini \
    && echo "xdebug.max_nesting_level=200" >> xdebug.ini \
    && echo "xdebug.remote_enable=1" >> xdebug.ini \
    && echo "xdebug.remote_autostart=0" >> xdebug.ini
