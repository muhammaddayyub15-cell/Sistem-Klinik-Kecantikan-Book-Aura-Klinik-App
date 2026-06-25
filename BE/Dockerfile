FROM php:8.4-apache

RUN apt-get update && apt-get install -y \
    curl zip unzip git \
    libpng-dev libxml2-dev libzip-dev \
    libonig-dev \
    && docker-php-ext-install pdo pdo_mysql mbstring xml zip gd \
    && curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer \
    && a2enmod rewrite

WORKDIR /var/www/html
COPY . .

RUN composer install --optimize-autoloader --no-dev \
    && chmod -R 775 storage bootstrap/cache \
    && chown -R www-data:www-data storage bootstrap/cache

COPY .docker/apache.conf /etc/apache2/sites-available/000-default.conf

CMD ["sh", "-c", "php artisan config:clear 2>&1 | tee /tmp/boot.log && php artisan cache:clear 2>&1 | tee -a /tmp/boot.log && php artisan migrate --force 2>&1 | tee -a /tmp/boot.log && echo 'Starting Apache' | tee -a /tmp/boot.log && apache2-foreground 2>&1 | tee -a /tmp/boot.log || (cat /tmp/boot.log && sleep 30)"]
