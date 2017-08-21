FROM yannickvh/php:5.4-fpm
MAINTAINER Yannick Vanhaeren

# Install dependencies
RUN apt-get update && apt-get install -y --no-install-recommends \
        libfreetype6 \
        libjpeg62-turbo \
        libpng12-0 \
        git \
        sudo \
        vim \
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
    && docker-php-ext-install -j$(nproc) exif gd ldap \
    && pecl install apcu-4.0.11 xdebug-2.4.1 ZendOpcache-7.0.4 \
    && docker-php-ext-enable apcu xdebug opcache \
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
    && sed -i -e "s|;realpath_cache_size = 16k|realpath_cache_size = 4096K|" php.ini \
    && sed -i -e "s|;realpath_cache_ttl = 120|realpath_cache_ttl = 600|" php.ini \
    && docker-php-source delete

RUN set -xe \
    && cd /usr/local/etc/php/conf.d \
    && echo "apc.enabled=1" >> docker-php-ext-apcu.ini \
    && echo "apc.shm_segments=1" >> docker-php-ext-apcu.ini \
    && echo "apc.shm_size=128M" >> docker-php-ext-apcu.ini \
    && echo "apc.ttl=7200" >> docker-php-ext-apcu.ini \
    && echo "apc.enable_cli=0" >> docker-php-ext-apcu.ini

RUN set -xe \
    && cd /usr/local/etc/php/conf.d \
    && echo "opcache.enable=1" >> docker-php-ext-opcache.ini \
    && echo "opcache.enable_cli=1" >> docker-php-ext-opcache.ini \
    && echo "opcache.memory_consumption=128" >> docker-php-ext-opcache.ini \
    && echo "opcache.interned_strings_buffer=8" >> docker-php-ext-opcache.ini \
    && echo "opcache.fast_shutdown=1" >> docker-php-ext-opcache.ini \
    && echo "opcache.enable_cli=1" >> docker-php-ext-opcache.ini \
    && echo "opcache.revalidate_freq=2" >> docker-php-ext-opcache.ini \
    && echo "opcache.max_accelerated_files = 20000" >> docker-php-ext-opcache.ini

RUN set -xe \
    && cd /usr/local/etc/php/conf.d \
    && sed -i -e "s|zend_extension|;zend_extension|" docker-php-ext-xdebug.ini \
    && echo "xdebug.default_enable=1" >> docker-php-ext-xdebug.ini \
    && echo "xdebug.profiler_enable=0" >> docker-php-ext-xdebug.ini \
    && echo "xdebug.idekey=PHPSTORM" >> docker-php-ext-xdebug.ini \
    && echo "xdebug.max_nesting_level=200" >> docker-php-ext-xdebug.ini \
    && echo "xdebug.remote_enable=1" >> docker-php-ext-xdebug.ini \
    && echo "xdebug.remote_autostart=0" >> docker-php-ext-xdebug.ini
