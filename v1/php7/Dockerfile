# 基础镜像
FROM ubuntu:18.04

# 作者信息
MAINTAINER tinywan@163.com

# 设置环境变量
ENV PHP_VERSION 7.2.13
ENV PHP_REDIS_VERSION 4.2.0
# 报错debconf: unable to initialize frontend: Dialog
ENV DEBIAN_FRONTEND noninteractive

# 复制命令，把本机的文件复制到镜像中。提前都放进基础镜像的/usr/local/src目录下，方便编译安装
# ADD php-7.0.0.tar.gz /usr/local/src
# 更新源sources.list 
ADD ./sources.list /etc/apt 

# 安装依赖
# RUN apt-get update && apt-get install --assume-yes apt-utils
RUN apt-get update && apt-get install --assume-yes apt-utils
RUN apt-get upgrade -y
RUN set -x \
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
    # && groupadd -f www-data \
    # && useradd -r -g www-data www-data \ 
    # 检查配置文件
    && ./configure \
    --prefix=/usr/local/php \
    --with-config-file-path=/usr/local/php/etc \
    --with-config-file-scan-dir=/usr/local/php/conf.d/ \
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
    && make \
    && make install \
    # 复制配置文件
    && cp ~/download/php-$PHP_VERSION/php.ini-production /usr/local/php/etc/php.ini \
    && cp /usr/local/php/etc/php-fpm.conf.default /usr/local/php/etc/php-fpm.conf \
    && cp /usr/local/php/etc/php-fpm.d/www.conf.default /usr/local/php/etc/php-fpm.d/www.conf \
    # 安装 Redis 扩展
    && cd ~/download \
    # && wget https://github.com/phpredis/phpredis/archive/$PHP_REDIS_VERSION.tar.gz \
    && wget http://tinywan-oss.oss-cn-shanghai.aliyuncs.com/uploads/$PHP_REDIS_VERSION.tar.gz \
    && tar -zxvf $PHP_REDIS_VERSION.tar.gz \
    && cd phpredis-$PHP_REDIS_VERSION \
    && /usr/local/php/bin/phpize \
    && ./configure --with-php-config=/usr/local/php/bin/php-config \
    && make \
    && make install \
    # 加入环境变量
    && echo "export PATH=$PATH:/usr/local/php/bin:/usr/local/php/sbin" >> ~/.bashrc ["/bin/bash", "-c", "source ~/.bashrc"] \
    # 安装 Composer
    && cd ~/download \
    && /usr/local/php/bin/php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');" \
    && /usr/local/php/bin/php composer-setup.php --install-dir=/usr/local/sbin --filename=composer \
    && /usr/local/php/bin/php -r "unlink('composer-setup.php');" \
    # 删除安装文件
    && rm -rf ~/download \
    && apt-get clean 

# 设置容器启动时要运行的命令只有在你执行 docker run 或者 docker start 命令是才会运行，其他情况下不运行。
CMD ["/usr/local/php/sbin/php-fpm"]

# 设置暴露端口号，注意是容器暴露端口号，并不是暴露到物理机上的端口号
EXPOSE 9000