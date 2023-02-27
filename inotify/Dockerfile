FROM php:8.2-cli-alpine3.16 as extension-builder

MAINTAINER Pecherskiy Valeriy <pecherskiy.v@yandex.ru>
LABEL org.opencontainers.image.authors="Pecherskiy Valeriy <pecherskiy.v@yandex.ru>"

RUN apk update && apk --no-cache add pcre-dev ${PHPIZE_DEPS}

############################################################################
# inotify      Hot Module Reload (HMR)
############################################################################
FROM extension-builder as ext
ENV PHP_EXT_DIR /usr/local/lib/php/extensions/no-debug-non-zts-20220829
ENV PHP_CONFIG_DIR /usr/local/etc/php/conf.d

ENV EXT_INOTIFY_VERSION 3.0.0

RUN curl -L https://github.com/arnaud-lb/php-inotify/archive/refs/tags/${EXT_INOTIFY_VERSION}.tar.gz | tar xz \
    && cd php-inotify-${EXT_INOTIFY_VERSION} \
    && phpize \
    && ./configure \
    && make \
    && make install && \
    cp `php-config --extension-dir`/inotify.so /tmp/inotify.so && \
    echo "extension="${PHP_EXT_DIR}"/inotify.so" > /tmp/ext-inotify.ini

# Build the final image with just the files we need
FROM scratch as xdebug
ENV PHP_EXT_DIR /usr/local/lib/php/extensions/no-debug-non-zts-20220829
ENV PHP_CONFIG_DIR /usr/local/etc/php/conf.d

# Copy things we installed to the final image
COPY --from=ext /tmp/inotify.so ${PHP_EXT_DIR}/inotify.so
COPY --from=ext /tmp/ext-inotify.ini ${PHP_CONFIG_DIR}/ext-inotify.ini

#############################################################################
# BUILD:
#
# docker build -t pecherskiy/php82-inotify .
#
# docker push pecherskiy/php82-inotify
#
#############################################################################
