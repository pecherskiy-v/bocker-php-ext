FROM php:8.2-cli-alpine3.17 AS extension-builder

LABEL org.opencontainers.image.authors="Pecherskiy Valeriy <pecherskiy.v@yandex.ru>"

RUN apk update && apk add --update linux-headers && apk --no-cache add pcre-dev ${PHPIZE_DEPS}

FROM extension-builder AS ext
ENV PHP_EXT_DIR=/usr/local/lib/php/extensions/no-debug-non-zts-20220829
ENV PHP_CONFIG_DIR=/usr/local/etc/php/conf.d

#############################################################################
# section: gd
#############################################################################
RUN apk update && apk --no-cache add pcre-dev ${PHPIZE_DEPS} zlib \
    libpng-dev libwebp-dev libjpeg-turbo-dev freetype-dev \
    && docker-php-ext-configure gd --enable-gd --with-freetype --with-jpeg --with-webp \
    && docker-php-ext-install -j$(getconf _NPROCESSORS_ONLN) gd \
    && docker-php-source delete \
    && apk del pcre-dev ${PHPIZE_DEPS}


# Build the final image with just the files we need
FROM scratch AS gd
ENV PHP_EXT_DIR=/usr/local/lib/php/extensions/no-debug-non-zts-20220829
ENV PHP_CONFIG_DIR=/usr/local/etc/php/conf.d

# Copy things we installed to the final image
#COPY --from=ext ${PHP_EXT_DIR}/bcmath.so ${PHP_EXT_DIR}/bcmath.so
#COPY --from=ext ${PHP_EXT_DIR}/zip.so ${PHP_EXT_DIR}/zip.so
COPY --from=ext ${PHP_EXT_DIR}/gd.so ${PHP_EXT_DIR}/gd.so
#COPY --from=ext ${PHP_CONFIG_DIR}/docker-php-ext-zip.ini ${PHP_CONFIG_DIR}/ext-zip.ini
#COPY --from=ext ${PHP_CONFIG_DIR}/docker-php-ext-bcmath.ini ${PHP_CONFIG_DIR}/ext-bcmath.ini
COPY --from=ext ${PHP_CONFIG_DIR}/docker-php-ext-gd.ini ${PHP_CONFIG_DIR}/ext-gd.ini

#############################################################################
# BUILD:
#
# docker build -t pecherskiy/php82-gd .
#
# docker push pecherskiy/php82-gd
#
# docker buildx build --push --platform linux/arm/v7,linux/arm64/v8,linux/amd64 -t pecherskiy/php82-gd .
#
#############################################################################
