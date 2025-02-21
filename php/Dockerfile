FROM php:7.4-fpm-alpine as minimum

# necessary if pecl has to be used later
ENV LIBRARY_PATH=/lib:/usr/lib
RUN apk add zlib-dev libpng-dev libjpeg-turbo-dev freetype-dev libzip-dev oniguruma-dev imagemagick imagemagick-dev gettext gettext-dev $PHPIZE_DEPS \
    && docker-php-ext-configure gd --with-freetype --with-jpeg \
    && docker-php-ext-install pdo_mysql gd opcache zip gettext \
#    && docker-php-ext-enable apcu \
    && cp "$PHP_INI_DIR/php.ini-development" "$PHP_INI_DIR/php.ini"

## add gibbon specific settings to php.ini
COPY ./gibbon-php-settings.ini "$PHP_INI_DIR/conf.d/gibbon-php-settings.ini"
COPY ./docker-php-mailrelay.ini "$PHP_INI_DIR/conf.d/docker-php-mailrelay.ini"

RUN cp "$PHP_INI_DIR/php.ini-production" "$PHP_INI_DIR/php.ini"

FROM minimum as dev

COPY ./docker-php-gibbonsetup /usr/local/bin/

HEALTHCHECK --interval=5s --timeout=5s\
  CMD php -r "echo '0' || exit 1"

RUN apk add rsync \
    && cp "$PHP_INI_DIR/php.ini-development" "$PHP_INI_DIR/php.ini" \
    && chmod 0755 /usr/local/bin/docker-php-gibbonsetup \
    && sed -i "`wc -l < /usr/local/bin/docker-php-entrypoint`i\\/usr/local/bin/docker-php-gibbonsetup\\" /usr/local/bin/docker-php-entrypoint

COPY --from=composer/composer /usr/bin/composer /usr/bin/composer
