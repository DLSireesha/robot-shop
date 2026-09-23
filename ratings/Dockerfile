#
# Build the app
#
FROM php:8.2-apache

# Install required packages and PHP extensions
RUN apt-get update && apt-get install -y --no-install-recommends unzip libzip-dev \
    && docker-php-ext-install xml pdo_mysql opcache zip \
    && rm -rf /var/lib/apt/lists/*

# Enable AutoProfile for PHP which is currently opt-in beta
RUN echo "instana.enable_auto_profile=1" > "/usr/local/etc/php/conf.d/zzz-instana-extras.ini"

# relax permissions on status
COPY status.conf /etc/apache2/mods-available/status.conf
# Enable Apache mod_rewrite and status
RUN a2enmod rewrite && a2enmod status

WORKDIR /var/www/html

# Copy composer files first (for caching)
COPY html/composer.json html/composer.lock* ./
# Install Composer(official imgae as build stage)
COPY --from=composer:2 /usr/bin/composer /usr/bin/composer
RUN composer install --no-dev --optimize-autoloader --no-interaction --no-progress

# Copy application source
COPY html/ /var/www/html

# This is important. Symfony needs write permissions and we
# dont know the context in which the container will run, i.e.
# which user will be forced from the outside so better play
# safe for this simple demo.
RUN rm -Rf /var/www/var/* \
    && chown -R www-data /var/www \
    && chmod -R 775 /var/www

