FROM php:8.4-apache

RUN apt-get update && apt-get install -y \
    curl zip unzip git \
    libpng-dev libxml2-dev libzip-dev \
    libonig-dev nodejs npm \
    && docker-php-ext-install pdo pdo_mysql mbstring xml zip gd \
    && curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer \
    && a2enmod rewrite

WORKDIR /var/www/html
COPY . .

RUN npm install && npm run build

RUN composer install --optimize-autoloader --no-dev \
    && chmod -R 775 storage bootstrap/cache \
    && chown -R www-data:www-data storage bootstrap/cache

COPY .docker/apache.conf /etc/apache2/sites-available/000-default.conf

CMD ["sh", "-c", "php artisan config:clear && php artisan cache:clear && php artisan migrate --force && apache2-foreground"]
