FROM yannickvh/php:5.3-apache
LABEL maintainer="Yannick Vanhaeren"

ARG DEBIAN_FRONTEND=noninteractive

# Install dependencies
RUN set -xe; \
    apt-get update && apt-get install -y --no-install-recommends \
        git \
        vim \
        openssh-client; \
    apt-get clean; \
    rm -r /var/lib/apt/lists/*

# Add php extensions
RUN set -eux; \
    pecl install -Z apc xdebug-2.2.7; \
    docker-php-ext-enable apc xdebug; \
    pecl clear-cache;

RUN set -eux; \
    docker-php-source extract; \
    cp /usr/src/php/php.ini-development /usr/local/etc/php/php.ini; \
    cd /usr/local/etc/php; \
    sed -i -e "s|max_execution_time = 30|max_execution_time = 60|" php.ini; \
    sed -i -e "s|memory_limit = 128M|memory_limit = 512M|" php.ini; \
    sed -i -e "s|post_max_size = 8M|post_max_size = 55M|" php.ini; \
    sed -i -e "s|upload_max_filesize = 2M|upload_max_filesize = 50M|" php.ini; \
    sed -i -e "s|;date.timezone =|date.timezone = Europe/Brussels|" php.ini; \
    sed -i -e "s|;cgi.fix_pathinfo=1|cgi.fix_pathinfo = 0|" php.ini; \
    sed -i -e "s|;realpath_cache_size = 16k|realpath_cache_size = 4096K|" php.ini; \
    sed -i -e "s|;realpath_cache_ttl = 120|realpath_cache_ttl = 600|" php.ini; \
    docker-php-source delete

RUN set -eux; \
    cd /usr/local/etc/php/conf.d; \
    echo "apc.enabled=1" >> docker-php-ext-apc.ini; \
    echo "apc.shm_segments=1" >> docker-php-ext-apc.ini; \
    echo "apc.shm_size=128M" >> docker-php-ext-apc.ini; \
    echo "apc.ttl=7200" >> docker-php-ext-apc.ini; \
    echo "apc.enable_cli=0" >> docker-php-ext-apc.ini

RUN set -eux; \
    cd /usr/local/etc/php/conf.d; \
    sed -i -e "s|zend_extension|;zend_extension|" docker-php-ext-xdebug.ini; \
    echo "xdebug.default_enable=1" >> docker-php-ext-xdebug.ini; \
    echo "xdebug.profiler_enable=0" >> docker-php-ext-xdebug.ini; \
    echo "xdebug.idekey=PHPSTORM" >> docker-php-ext-xdebug.ini; \
    echo "xdebug.max_nesting_level=200" >> docker-php-ext-xdebug.ini; \
    echo "xdebug.remote_enable=1" >> docker-php-ext-xdebug.ini; \
    echo "xdebug.remote_autostart=0" >> docker-php-ext-xdebug.ini
