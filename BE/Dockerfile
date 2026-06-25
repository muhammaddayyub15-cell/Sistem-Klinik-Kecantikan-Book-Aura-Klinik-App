FROM php:8.4-apache

RUN apt-get update && apt-get install -y \
    curl zip unzip git nodejs npm \
    libpng-dev libxml2-dev libzip-dev libonig-dev \
    && docker-php-ext-install pdo pdo_mysql mbstring xml zip gd \
    && curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer \
    && a2enmod rewrite headers \
    && a2dismod mpm_event mpm_worker 2>/dev/null; a2enmod mpm_prefork

WORKDIR /var/www/html
COPY . .

RUN npm install && npm run build

RUN composer install --optimize-autoloader --no-dev \
    && chmod -R 775 storage bootstrap/cache \
    && chown -R www-data:www-data storage bootstrap/cache

RUN echo '<VirtualHost *:80>\n\
    DocumentRoot /var/www/html/public\n\
    <Directory /var/www/html/public>\n\
        AllowOverride All\n\
        Require all granted\n\
    </Directory>\n\
</VirtualHost>' > /etc/apache2/sites-available/000-default.conf

CMD ["sh", "-c", "sed -i \"s/Listen 80/Listen ${PORT:-80}/\" /etc/apache2/ports.conf && sed -i \"s/*:80/*:${PORT:-80}/\" /etc/apache2/sites-available/000-default.conf && apache2-foreground"]
