FROM php:8.2-cli-alpine3.17 as extension-builder

MAINTAINER Pecherskiy Valeriy <pecherskiy.v@yandex.ru>
LABEL org.opencontainers.image.authors="Pecherskiy Valeriy <pecherskiy.v@yandex.ru>"

RUN apk update && apk add --update linux-headers && apk --no-cache add pcre-dev ${PHPIZE_DEPS}

FROM extension-builder as ext
ENV PHP_EXT_DIR /usr/local/lib/php/extensions/no-debug-non-zts-20220829
ENV PHP_CONFIG_DIR /usr/local/etc/php/conf.d

#############################################################################
# section: xDebug
#############################################################################
ENV EXT_XDEBUG_VERSION 3.2.1

RUN curl -L https://github.com/xdebug/xdebug/archive/refs/tags/${EXT_XDEBUG_VERSION}.tar.gz | tar xz \
    && cd xdebug-${EXT_XDEBUG_VERSION} \
    && phpize \
    && ./configure \
    && make \
    && make install && \
    cp `php-config --extension-dir`/xdebug.so /tmp/xdebug.so && \
    echo "zend_extension="${PHP_EXT_DIR}"/xdebug.so" > /tmp/ext-xdebug.ini

# Build the final image with just the files we need
FROM scratch as xdebug
ENV PHP_EXT_DIR /usr/local/lib/php/extensions/no-debug-non-zts-20220829
ENV PHP_CONFIG_DIR /usr/local/etc/php/conf.d

# Copy things we installed to the final image
COPY --from=ext /tmp/xdebug.so ${PHP_EXT_DIR}/xdebug.so
COPY --from=ext /tmp/ext-xdebug.ini ${PHP_CONFIG_DIR}/ext-xdebug.ini

#############################################################################
# BUILD:
#
# docker build -t pecherskiy/php82-xdebug .
#
# docker push pecherskiy/php82-xdebug
#
#############################################################################
