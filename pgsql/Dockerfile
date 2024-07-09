FROM php:8.2-cli-alpine3.17 as extension-builder

MAINTAINER Pecherskiy Valeriy <pecherskiy.v@yandex.ru>
LABEL org.opencontainers.image.authors="Pecherskiy Valeriy <pecherskiy.v@yandex.ru>"

RUN apk update && apk add --update linux-headers && apk --no-cache add pcre-dev ${PHPIZE_DEPS}

FROM extension-builder as ext
ENV PHP_EXT_DIR /usr/local/lib/php/extensions/no-debug-non-zts-20220829
ENV PHP_CONFIG_DIR /usr/local/etc/php/conf.d

#############################################################################
# section: PgSQL
#############################################################################

RUN apk add postgresql-dev \
    && docker-php-ext-install pdo \
    && docker-php-ext-install pdo_pgsql \
    && docker-php-source delete \
    && apk del pcre-dev ${PHPIZE_DEPS}


# Build the final image with just the files we need
FROM scratch as pgsql
ENV PHP_EXT_DIR /usr/local/lib/php/extensions/no-debug-non-zts-20220829
ENV PHP_CONFIG_DIR /usr/local/etc/php/conf.d

# Copy things we installed to the final image
COPY --from=ext ${PHP_EXT_DIR}/pdo_pgsql.so ${PHP_EXT_DIR}/pdo_pgsql.so
COPY --from=ext ${PHP_CONFIG_DIR}/docker-php-ext-pdo_pgsql.ini ${PHP_CONFIG_DIR}/ext-pdo_pgsql.ini

#############################################################################
# BUILD:
#
# docker build -t pecherskiy/php82-pdo_pgsql .
#
# docker push pecherskiy/php82-pdo_pgsql
#
# docker buildx build --push --platform linux/arm/v7,linux/arm64/v8,linux/amd64 -t pecherskiy/php82-pdo_pgsql .
#
#############################################################################
