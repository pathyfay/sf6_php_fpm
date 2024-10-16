# Utiliser une image de base PHP 7.4
FROM php:8.1-fpm

ENV NGINX_DOCUMENT_ROOT="/var/www/html/public"
ENV NGINX_SERVER_NAME="symfony-php.local"
ENV COMPOSER_ALLOW_SUPERUSER=1
ENV COMPOSER_PROCESS_TIMEOUT=600

# Installer des dépendances système nécessaires
RUN apt-get update && apt-get install -y \
    libfreetype6-dev \
    libjpeg62-turbo-dev \
    libpng-dev \
    libzip-dev \
    zip \
    unzip \
    git \
    curl \
    supervisor \
    cron \
    && docker-php-ext-configure gd --with-freetype --with-jpeg \
    && docker-php-ext-install opcache \
    && docker-php-ext-install -j$(nproc) gd pdo pdo_mysql zip \
    && apt-get clean && rm -rf /var/lib/apt/lists/*

# Installer Composer
COPY --from=composer:latest /usr/bin/composer /usr/bin/composer

# Installer Symfony CLI
RUN curl -sS https://get.symfony.com/cli/installer | bash
RUN mv /root/.symfony*/bin/symfony /usr/local/bin/symfony && \
    chmod +x /usr/local/bin/symfony

#Install supervisor & cron
RUN apt-get update && apt-get install -y supervisor cron
ADD ./.docker/php/config/supervisord.conf /etc/supervisor/conf.d/

# Configuring configuration php & nginx & start & supervisord
ENV COMPOSER_ORIGINAL_INIS=${PHP_INI_DIR}/conf.d/php-composer.ini
COPY ./.docker/php/config/php-composer.ini $COMPOSER_ORIGINAL_INIS
COPY ./.docker/php/config/uploads.ini $PHP_INI_DIR/conf.d/uploads.ini
COPY ./.docker/php/config/timezone.ini $PHP_INI_DIR/conf.d/timezone.ini
COPY ./.docker/php/config/php-fpm.conf /usr/local/etc/php-fpm.d/php-fpm.conf
COPY ./.docker/php/config/opcache.ini $PHP_INI_DIR/conf.d/opcache.ini
COPY ./.docker/php/config/supervisord.conf /etc/supervisor/conf.d/

COPY ./.docker/script/start-backend /usr/local/bin/

# Droits pour exécuter Symfony
RUN chown -R www-data:www-data /var/www/html \
    && chmod -R 755 /var/www/html

# Définir le répertoire de travail
WORKDIR /var/www/html

# Optimisation des permissions pour PHP-FPM et www-data
RUN usermod -u 1000 www-data \
    && usermod -a -G www-data root \
    && chmod +x /usr/local/bin/start-backend

# Exposer le port 9000 (par exemple pour PHP-FPM)
EXPOSE 9000

# Démarrer PHP-FPM
CMD ["start-backend"]