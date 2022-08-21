FROM php:8.1-cli-alpine3.16 as extension-builder

MAINTAINER Pecherskiy Valeriy <pecherskiy.v@yandex.ru>
LABEL org.opencontainers.image.authors="Pecherskiy Valeriy <pecherskiy.v@yandex.ru>"

RUN apk update && apk --no-cache add pcre-dev ${PHPIZE_DEPS}

FROM extension-builder as ext
ENV PHP_EXT_DIR /usr/local/lib/php/extensions/no-debug-non-zts-20210902
ENV PHP_CONFIG_DIR /usr/local/etc/php/conf.d

#############################################################################
# section: swoole
#############################################################################
ENV EXT_OPENSWOOLE_VERSION 4.11.1

RUN set -ex \
    && if [ `pear config-get ext_dir` != ${PHP_EXT_DIR} ]; then echo PHP_EXT_DIR must be `pear config-get ext_dir` && exit 1; fi \
    && apk add postgresql-dev openssl-dev \
    && docker-php-ext-install pdo \
    && docker-php-ext-install pdo_pgsql \
    && docker-php-ext-install sockets \
    && curl -L https://github.com/openswoole/swoole-src/archive/refs/tags/v${EXT_OPENSWOOLE_VERSION}.tar.gz | tar xz \
    && cd swoole-src-${EXT_OPENSWOOLE_VERSION} \
    && phpize \
    && ./configure \
      --enable-http2 \
      --enable-openssl \
      --enable-sockets \
      --enable-swoole-json \
      --with-postgres \
    && make \
    && make install

RUN cp `php-config --extension-dir`/openswoole.so /tmp/openswoole.so && \
    cp `php-config --extension-dir`/opcache.so /tmp/opcache.so && \
    cp `php-config --extension-dir`/sockets.so /tmp/sockets.so && \
    cp `php-config --extension-dir`/sodium.so /tmp/sodium.so && \
    echo "extension="${PHP_EXT_DIR}"/openswoole.so" > /tmp/ext-openswoole.ini && \
    echo "extension="${PHP_EXT_DIR}"/opcache.so" > /tmp/ext-opcache.ini && \
    echo "extension="${PHP_EXT_DIR}"/sockets.so" > /tmp/ext-sockets.ini && \
    echo "extension="${PHP_EXT_DIR}"/sodium.so" > /tmp/ext-sodium.ini

# Build the final image with just the files we need
FROM scratch as openswoole
ENV PHP_EXT_DIR /usr/local/lib/php/extensions/no-debug-non-zts-20210902
ENV PHP_CONFIG_DIR /usr/local/etc/php/conf.d

# Copy things we installed to the final image
COPY --from=ext /tmp/openswoole.so ${PHP_EXT_DIR}/openswoole.so
#COPY --from=ext /tmp/opcache.so ${PHP_EXT_DIR}/opcache.so
#COPY --from=ext /tmp/sodium.so ${PHP_EXT_DIR}/sodium.so
COPY --from=ext /tmp/ext-openswoole.ini ${PHP_CONFIG_DIR}/ext-openswoole.ini
#COPY --from=ext /tmp/ext-opcache.ini ${PHP_CONFIG_DIR}/ext-opcache.ini
#COPY --from=ext /tmp/ext-sodium.ini ${PHP_CONFIG_DIR}/ext-sodium.ini

#############################################################################
# BUILD:
#
# docker build -t pecherskiy/php81-openswoole .
#
# docker push pecherskiy/php81-openswoole
#
#############################################################################
