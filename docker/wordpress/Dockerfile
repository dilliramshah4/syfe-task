FROM php:8.1-apache

RUN apt-get update && apt-get install -y \
    libpng-dev \
    libjpeg-dev \
    libfreetype6-dev \
    libzip-dev \
    unzip \
    && docker-php-ext-configure gd --with-freetype --with-jpeg \
    && docker-php-ext-install -j$(nproc) gd mysqli pdo pdo_mysql zip \
    && rm -rf /var/lib/apt/lists/*

RUN curl -O https://wordpress.org/latest.tar.gz \
    && tar xzf latest.tar.gz \
    && cp -R wordpress/* /var/www/html/ \
    && rm -rf wordpress latest.tar.gz \
    && chown -R www-data:www-data /var/www/html

COPY wp-config.php /var/www/html/

EXPOSE 80
