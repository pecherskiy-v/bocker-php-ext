FROM php:8.2-cli-alpine3.16 as extension-builder

MAINTAINER Pecherskiy Valeriy <pecherskiy.v@yandex.ru>
LABEL org.opencontainers.image.authors="Pecherskiy Valeriy <pecherskiy.v@yandex.ru>"

RUN apk update && apk --no-cache add pcre-dev ${PHPIZE_DEPS}

FROM extension-builder as ext
ENV PHP_EXT_DIR /usr/local/lib/php/extensions/no-debug-non-zts-20220829
ENV PHP_CONFIG_DIR /usr/local/etc/php/conf.d

ENV EXT_MSGPACK_VERSION 2.2.0RC1

RUN curl -L https://github.com/msgpack/msgpack-php/archive/refs/tags/msgpack-${EXT_MSGPACK_VERSION}.tar.gz | tar xz \
    && cd msgpack-php-msgpack-${EXT_MSGPACK_VERSION} \
    && phpize \
    && ./configure \
    && make \
    && make install \
    && pecl install -D 'enable-redis-msgpack="yes"' redis && \
    cp `php-config --extension-dir`/msgpack.so /tmp/msgpack.so && \
    cp `php-config --extension-dir`/redis.so /tmp/redis.so && \
    echo "extension="${PHP_EXT_DIR}"/msgpack.so" > /tmp/ext-msgpack.ini && \
    echo "extension="${PHP_EXT_DIR}"/redis.so" > /tmp/ext-redis.ini

# Build the final image with just the files we need
FROM scratch as redis-msgpack
ENV PHP_EXT_DIR /usr/local/lib/php/extensions/no-debug-non-zts-20220829
ENV PHP_CONFIG_DIR /usr/local/etc/php/conf.d

# Copy things we installed to the final image
COPY --from=ext /tmp/msgpack.so ${PHP_EXT_DIR}/msgpack.so
COPY --from=ext /tmp/redis.so ${PHP_EXT_DIR}/redis.so
COPY --from=ext /tmp/ext-msgpack.ini ${PHP_CONFIG_DIR}/ext-msgpack.ini
COPY --from=ext /tmp/ext-redis.ini ${PHP_CONFIG_DIR}/ext-redis.ini

#############################################################################
# BUILD:
#
# docker build -t pecherskiy/php82-redis-msgpack .
#
# docker push pecherskiy/php82-redis-msgpack
#
#############################################################################
