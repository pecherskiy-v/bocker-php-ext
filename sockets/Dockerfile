FROM php:8.2-cli-alpine3.17 as extension-builder

MAINTAINER Pecherskiy Valeriy <pecherskiy.v@yandex.ru>
LABEL org.opencontainers.image.authors="Pecherskiy Valeriy <pecherskiy.v@yandex.ru>"

RUN apk update && apk add --update linux-headers && apk --no-cache add pcre-dev ${PHPIZE_DEPS}

FROM extension-builder as ext
ENV PHP_EXT_DIR /usr/local/lib/php/extensions/no-debug-non-zts-20220829
ENV PHP_CONFIG_DIR /usr/local/etc/php/conf.d

#############################################################################
# section: sockets
#############################################################################
RUN set -ex \
    && if [ `pear config-get ext_dir` != ${PHP_EXT_DIR} ]; then echo PHP_EXT_DIR must be `pear config-get ext_dir` && exit 1; fi \
    && docker-php-ext-install sockets

RUN cp `php-config --extension-dir`/sockets.so /tmp/sockets.so && \
    echo "extension="${PHP_EXT_DIR}"/sockets.so" > /tmp/ext-sockets.ini

#RUN ls -al `php-config --extension-dir` && sleep 555

# Build the final image with just the files we need
FROM scratch as openswoole
ENV PHP_EXT_DIR /usr/local/lib/php/extensions/no-debug-non-zts-20220829
ENV PHP_CONFIG_DIR /usr/local/etc/php/conf.d

# Copy things we installed to the final image
COPY --from=ext /tmp/sockets.so ${PHP_EXT_DIR}/sockets.so
COPY --from=ext /tmp/ext-sockets.ini ${PHP_CONFIG_DIR}/ext-sockets.ini

#############################################################################
# BUILD:
#
# docker build -t pecherskiy/php82-sockets .
#
# docker push pecherskiy/php82-sockets
#
# docker buildx build --push --platform linux/arm/v7,linux/arm64/v8,linux/amd64 -t pecherskiy/php82-sockets .
#
#############################################################################
