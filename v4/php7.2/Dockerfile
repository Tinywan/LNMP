
FROM php:7.2.19-fpm-alpine

COPY ./extensions /tmp/extensions
WORKDIR /tmp/extensions

ENV EXTENSIONS=",pdo_mysql,opcache,redis,"
ENV MC="-j$(nproc)"

RUN export MC="-j$(nproc)" \
    && chmod +x install.sh \
    && chmod +x php72.sh \
    && sh install.sh \
    && sh php72.sh \
    && rm -rf /tmp/extensions

WORKDIR /var/www