FROM php:8.2-cli-alpine3.17 as extension-builder

MAINTAINER Pecherskiy Valeriy <pecherskiy.v@yandex.ru>
LABEL org.opencontainers.image.authors="Pecherskiy Valeriy <pecherskiy.v@yandex.ru>"

RUN apk update && apk add --update linux-headers && apk --no-cache add pcre-dev ${PHPIZE_DEPS}

FROM extension-builder as ext
ENV PHP_EXT_DIR /usr/local/lib/php/extensions/no-debug-non-zts-20220829
ENV PHP_CONFIG_DIR /usr/local/etc/php/conf.d

#############################################################################
# section: MySQL
#############################################################################
ENV EXT_VERSION 1.0.2

RUN docker-php-ext-install pdo \
    && docker-php-ext-install pdo_mysql \
    && docker-php-source delete \
    && apk del pcre-dev ${PHPIZE_DEPS}


# Build the final image with just the files we need
FROM scratch as mysql
ENV PHP_EXT_DIR /usr/local/lib/php/extensions/no-debug-non-zts-20220829
ENV PHP_CONFIG_DIR /usr/local/etc/php/conf.d

# Copy things we installed to the final image
COPY --from=ext ${PHP_EXT_DIR}/pdo_mysql.so ${PHP_EXT_DIR}/pdo_mysql.so
COPY --from=ext ${PHP_CONFIG_DIR}/docker-php-ext-pdo_mysql.ini ${PHP_CONFIG_DIR}/ext-mysql.ini

#############################################################################
# BUILD:
#
# docker build -t pecherskiy/php82-pdo_mysql .
#
# docker push pecherskiy/php82-pdo_mysql
#
#############################################################################
