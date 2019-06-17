FROM php:5.6.40-fpm-alpine

ENV PHP_EXTENSIONS="gd,mysqli,pdo-mysql,mcrypt,opcache,redis,pcntl"
ENV MORE_EXTENSION_INSTALLER="php56.sh"
ENV ALPINE_REPOSITORIES="mirrors.aliyun.com"

COPY ./extensions /tmp/extensions
WORKDIR /tmp/extensions

ENV EXTENSIONS=",${PHP_EXTENSIONS},"
ENV MC="-j$(nproc)"

RUN export MC="-j$(nproc)" \
    && chmod +x install.sh \
    && chmod +x "${MORE_EXTENSION_INSTALLER}" \
    && sh install.sh \
    && sh "${MORE_EXTENSION_INSTALLER}" \
    && rm -rf /tmp/extensions

WORKDIR /var/www