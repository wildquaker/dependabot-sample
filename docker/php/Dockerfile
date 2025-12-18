FROM php:8.2-fpm

# Install git and other system dependencies in a single layer
# I've moved 'apt-get update' here and changed 'libpcre3-dev' to 'libpcre2-dev'
RUN apt-get update && apt-get install -y \
        git \
        libzip-dev \
        zip \
        zlib1g-dev \
        libpng-dev \
        libjpeg-dev \
        libwebp-dev \
        libpcre2-dev \
        redis-tools \
        cron

# Install required PHP extensions
RUN docker-php-ext-install pdo_mysql \
  && docker-php-ext-install zip \
  && docker-php-ext-install opcache \
  && pecl install apcu \
  && docker-php-ext-enable apcu

# Install the Redis extension
RUN pecl install redis-5.3.7 && docker-php-ext-enable redis

# Configure and install the GD extension
RUN docker-php-ext-configure gd --with-jpeg --with-webp \
    && docker-php-ext-install gd

# Install Composer
COPY --from=composer:2.1 /usr/bin/composer /usr/bin/composer

# Install cron
COPY ./crontab /etc/cron.d/app-cron
RUN chmod 0644 /etc/cron.d/app-cron
RUN crontab /etc/cron.d/app-cron
RUN touch /var/log/cron.log

WORKDIR /var/www

CMD service cron start && php-fpm