ARG IMAGE_BASE="ubuntu"
ARG IMAGE_TAG="bionic"

FROM ${IMAGE_BASE}:${IMAGE_TAG}

LABEL maintainer="Tinywan <etinywan@163.com>"

ENV PHP_VERSION 5.6.39
ENV PHP_REDIS_VERSION 4.2.0
ENV PHP_INI_DIR /usr/local/php/etc
ENV PHP_CONFIG_DIR /usr/local/php/bin/php-config
ENV PHP_PHPIZE /usr/local/php/bin/phpize

ENV DEBIAN_FRONTEND noninteractive
ENV LANG C.UTF-8
ENV TZ Asia/Shanghai

# 复制命令，把本机的文件复制到镜像中。提前都放进基础镜像的/usr/local/src目录下，方便编译安装
# ADD php-7.0.0.tar.gz /usr/local/src
# 更新源sources.list 
ADD ./sources.list /etc/apt 
ADD ./composer.phar /usr/local/bin/composer 
WORKDIR /var/www

# 安装依赖
# RUN apt-get update && apt-get install --assume-yes apt-utils
RUN apt-get update && apt-get install --assume-yes apt-utils
RUN apt-get upgrade -y \
    # allow running as an arbitrary user (https://github.com/docker-library/php/issues/743)
    [ ! -d /var/www ]; \
    mkdir -p /var/www; \
    chown www-data:www-data /var/www; \
    chmod 777 /var/www

RUN set -x \
    && ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone \
    && apt-get install tzdata \
    && apt-get install -y make \
    wget \
    autoconf \
    libiconv-hook-dev \
    libmcrypt-dev \
    libxml2-dev \
    libmysqlclient-dev \
    libcurl4-openssl-dev \
    libjpeg8-dev \
    libpng-dev \
    libfreetype6-dev \
    openssl \
    libssl-dev \
    # 建立目录
    && mkdir ~/download \
    && cd ~/download \   
    # 下载源码
    # && wget http://cn2.php.net/distributions/php-$PHP_VERSION.tar.gz \
    && wget http://tinywan-oss.oss-cn-shanghai.aliyuncs.com/uploads/php-$PHP_VERSION.tar.gz \
    && tar -zxf php-$PHP_VERSION.tar.gz \
    && cd php-$PHP_VERSION \
    # Write Permission
    && usermod -u 1000 www-data \ 
    # 检查配置文件
    && ./configure \
    --prefix=/usr/local/php \
    --with-config-file-path=${PHP_INI_DIR} \
    --with-config-file-scan-dir=${PHP_INI_DIR}/conf.d/ \
    --enable-fpm \
    --enable-pcntl \
    --enable-mysqlnd \
    --enable-opcache \
    --enable-sockets \
    --enable-sysvmsg \
    --enable-sysvsem \
    --enable-sysvshm \
    --enable-shmop \
    --enable-zip \
    --enable-soap \
    --enable-xml \
    --enable-mbstring \
    --disable-rpath \
    --disable-debug \
    --with-mysqli=mysqlnd \
    --with-pdo-mysql=mysqlnd \
    --with-mysql-sock=/var/run/mysqld/mysqld.sock \
    --with-pcre-regex   \
    --with-iconv   \
    --with-zlib   \
    --with-mcrypt  \
    --with-gd  \
    --with-openssl  \
    --with-mhash  \
    --with-xmlrpc  \
    --with-curl \
    --with-imap-ssl \
    --with-fpm-user=www-data \
    --with-fpm-group=www-data \
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

# 设置容器启动时要运行的命令只有在你执行 docker run 或者 docker start 命令是才会运行，其他情况下不运行。
CMD ["/usr/local/php/sbin/php-fpm"]

# 设置暴露端口号，注意是容器暴露端口号，并不是暴露到物理机上的端口号
EXPOSE 9000