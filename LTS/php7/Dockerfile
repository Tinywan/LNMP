ARG IMAGE_BASE="ubuntu"
ARG IMAGE_TAG="bionic"

FROM ${IMAGE_BASE}:${IMAGE_TAG}

LABEL maintainer="Tinywan <etinywan@163.com>"

# Docker Build Arguments

# Docker Environment
ENV PHP_VERSION 7.2.13
ENV PHP_REDIS_VERSION 4.2.0
ENV PHP_INI_DIR /usr/local/php/etc
ENV PHP_CONFIG_DIR /usr/local/php/bin/php-config
ENV PHP_PHPIZE /usr/local/php/bin/phpize

# Sysytem Environment
ENV DEBIAN_FRONTEND noninteractive
ENV LANG C.UTF-8
ENV TZ Asia/Shanghai

ADD ./sources.list /etc/apt 
ADD ./composer.phar /usr/local/bin/composer 

WORKDIR /var/www

RUN apt-get update && apt-get install --assume-yes apt-utils
RUN apt-get upgrade -y \
    # allow running as an arbitrary user (https://github.com/docker-library/php/issues/743)
	[ ! -d /var/www/html ]; \
	mkdir -p /var/www; \
	chown www-data:www-data /var/www; \
	chmod 777 /var/www

RUN set -x \
    && ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone \
    && apt-get install tzdata \
    && apt-get install -y libkrb5-dev wget \
    libc-client2007e     \
    libc-client2007e-dev \
    libcurl4-openssl-dev \
    libbz2-dev           \
    libjpeg-dev          \
    libmcrypt-dev        \
    libxslt1-dev         \
    libxslt1.1           \
    libpq-dev            \
    libfreetype6-dev     \
    libzip-dev           \
    libpng-dev           \
    build-essential      \
    git                  \
    make \
    bison \
    re2c \
    vim \
    autoconf \
    cron \
    unzip \
    # crontab
    && sed -i "s/session    required     pam_loginuid.so/\#session    required     pam_loginuid.so/g" /etc/pam.d/cron \
    && /etc/init.d/cron start \
    # 建立目录
    && mkdir ~/download \
    && cd ~/download \   
    # 下载源码
    # && wget http://cn2.php.net/distributions/php-$PHP_VERSION.tar.gz \
    && wget http://tinywan-oss.oss-cn-shanghai.aliyuncs.com/uploads/php-$PHP_VERSION.tar.gz \
    && tar -zxf php-$PHP_VERSION.tar.gz \
    && cd php-$PHP_VERSION \
    # Write Permission
    # && usermod -u 1000 www-data \ 
    # 检查配置文件
    && ./configure \
    --prefix=/usr/local/php \
    --with-config-file-path=${PHP_INI_DIR} \
    --with-config-file-scan-dir=${PHP_INI_DIR}/conf.d/ \
    --with-zlib-dir \
    --with-freetype-dir \
    --enable-mbstring                            \
    --with-libxml-dir=/usr                       \
    --enable-soap                                \
    --enable-calendar                            \
    --with-curl                                  \
    --with-zlib                                  \
    --with-gd                                    \
    --disable-rpath                              \
    --enable-inline-optimization                 \
    --with-bz2                                   \
    --with-zlib                                  \
    --enable-sockets                             \
    --enable-sysvsem                             \
    --enable-sysvshm                             \
    --enable-pcntl                               \
    --enable-mbregex                             \
    --enable-exif                                \
    --enable-bcmath                              \
    --with-mhash                                 \
    --enable-zip                                 \
    --with-pcre-regex                            \
    --with-pdo-mysql                             \
    --with-mysqli                                \
    --with-mysql-sock=/var/run/mysqld/mysqld.sock \
    --with-jpeg-dir=/usr                         \
    --with-png-dir=/usr                          \
    --with-openssl                               \
    --with-fpm-user=www-data                     \
    --with-fpm-group=www-data                    \
    --enable-ftp                                 \
    --with-imap                                  \
    --with-imap-ssl                              \
    --with-kerberos                              \
    --with-gettext                               \
    --with-xmlrpc                                \
    --with-xsl                                   \
    --enable-opcache                             \
    --enable-fpm    \
    # 编译安装    
    && make -j "$(nproc)" \
    && find -type f -name '*.a' -delete \
    && make install \
    && { find /usr/local/bin /usr/local/sbin -type f -executable -exec strip --strip-all '{}' + || true; } \
    && make clean \ 
    # 复制配置文件
    && cp ~/download/php-$PHP_VERSION/php.ini-production ${PHP_INI_DIR}/php.ini \
    && cp /usr/local/php/etc/php-fpm.conf.default ${PHP_INI_DIR}/php-fpm.conf \
    && cp /usr/local/php/etc/php-fpm.d/www.conf.default ${PHP_INI_DIR}/php-fpm.d/www.conf \
    # 安装 Redis 扩展
    && cd ~/download \
    # && wget https://github.com/phpredis/phpredis/archive/$PHP_REDIS_VERSION.tar.gz \
    && wget http://tinywan-oss.oss-cn-shanghai.aliyuncs.com/uploads/$PHP_REDIS_VERSION.tar.gz \
    && tar -zxvf $PHP_REDIS_VERSION.tar.gz \
    && cd phpredis-$PHP_REDIS_VERSION \
    && ${PHP_PHPIZE} \
    && ./configure --with-php-config=${PHP_CONFIG_DIR} \
    && make -j "$(nproc)" \
    && find -type f -name '*.a' -delete \
    && make install \ 
    && make clean \ 
    # 加入环境变量
    && echo "export PATH=$PATH:/usr/local/php/bin:/usr/local/php/sbin" >> ~/.bashrc ["/bin/bash", "-c", "source ~/.bashrc"] \
    # 安装 Composer
    && chmod 777 /usr/local/bin/composer \
    # 删除安装文件
    && rm -rf ~/download \
    && apt-get clean \
    && apt-get autoclean \
    && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

# https://github.com/php/php-src/blob/17baa87faddc2550def3ae7314236826bc1b1398/sapi/fpm/php-fpm.8.in#L163
STOPSIGNAL SIGQUIT

EXPOSE 9000
CMD ["/usr/local/php/sbin/php-fpm"]