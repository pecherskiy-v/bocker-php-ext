FROM php:8.2-cli-alpine3.17 AS extension-builder

LABEL org.opencontainers.image.authors="Pecherskiy Valeriy <pecherskiy.v@yandex.ru>"

RUN apk update && apk add --update linux-headers && apk --no-cache add pcre-dev ${PHPIZE_DEPS}

FROM extension-builder AS ext
ENV PHP_EXT_DIR=/usr/local/lib/php/extensions/no-debug-non-zts-20220829
ENV PHP_CONFIG_DIR=/usr/local/etc/php/conf.d

#############################################################################
# section: swoole
#############################################################################
ENV EXT_SWOOLE_VERSION=5.1.3

RUN set -ex \
    && if [ `pear config-get ext_dir` != ${PHP_EXT_DIR} ]; then echo PHP_EXT_DIR must be `pear config-get ext_dir` && exit 1; fi \
    && apk add openssl-dev curl-dev\
    && docker-php-ext-install sockets \
    && docker-php-ext-install pdo \
    && docker-php-ext-install pdo_mysql \
    && curl -L https://github.com/swoole/swoole-src/archive/refs/tags/v${EXT_SWOOLE_VERSION}.tar.gz | tar xz \
    && cd swoole-src-${EXT_SWOOLE_VERSION} \
    && phpize \
    && ./configure \
      --enable-debug-log \
      --enable-http2 \
      --enable-mysqlnd \
      --enable-openssl \
      --enable-sockets \
      --enable-swoole \
      --enable-swoole-coro-time \
      --enable-swoole-curl \
      --enable-swoole-json \
      --enable-thread-context \
    && make \
    && make install

RUN cp `php-config --extension-dir`/swoole.so /tmp/swoole.so && \
    cp `php-config --extension-dir`/opcache.so /tmp/opcache.so && \
    cp `php-config --extension-dir`/sockets.so /tmp/sockets.so && \
    cp `php-config --extension-dir`/sodium.so /tmp/sodium.so && \
    echo "extension="${PHP_EXT_DIR}"/swoole.so" > /tmp/ext-swoole.ini && \
    echo "extension="${PHP_EXT_DIR}"/opcache.so" > /tmp/ext-opcache.ini && \
    echo "extension="${PHP_EXT_DIR}"/sockets.so" > /tmp/ext-sockets.ini && \
    echo "extension="${PHP_EXT_DIR}"/sodium.so" > /tmp/ext-sodium.ini

# Build the final image with just the files we need
FROM scratch AS swoole
ENV PHP_EXT_DIR=/usr/local/lib/php/extensions/no-debug-non-zts-20220829
ENV PHP_CONFIG_DIR=/usr/local/etc/php/conf.d

# Copy things we installed to the final image
COPY --from=ext /tmp/swoole.so ${PHP_EXT_DIR}/swoole.so
#COPY --from=ext /tmp/opcache.so ${PHP_EXT_DIR}/opcache.so
#COPY --from=ext /tmp/sodium.so ${PHP_EXT_DIR}/sodium.so
#COPY --from=ext /tmp/sockets.so ${PHP_EXT_DIR}/sockets.so
COPY --from=ext /tmp/ext-swoole.ini ${PHP_CONFIG_DIR}/ext-swoole.ini
#COPY --from=ext /tmp/ext-opcache.ini ${PHP_CONFIG_DIR}/ext-opcache.ini
#COPY --from=ext /tmp/ext-sodium.ini ${PHP_CONFIG_DIR}/ext-sodium.ini
#COPY --from=ext /tmp/ext-sockets.ini ${PHP_CONFIG_DIR}/ext-sockets.ini

#############################################################################
# BUILD:
#
# docker build -t pecherskiy/php82-swoole-mysql .
#
# docker push pecherskiy/php82-swoole-mysql
#
# docker buildx build --push --platform linux/arm64/v8,linux/amd64 -t pecherskiy/php82-swoole-mysql .
#
#############################################################################
