FROM php:8.2-fpm-alpine

WORKDIR /app

# Install system dependencies
RUN apk add --no-cache \
    curl \
    wget \
    git \
    zip \
    unzip \
    postgresql-dev \
    oniguruma-dev \
    libxml2-dev \
    libpng-dev \
    libjpeg-turbo-dev \
    freetype-dev \
    zlib-dev \
    libzip-dev \
    imagemagick-dev \
    redis \
    supervisor

# Install PHP extensions
RUN docker-php-ext-install \
    pdo \
    pdo_pgsql \
    mbstring \
    xml \
    gd \
    zip \
    bcmath \
    exif \
    pcntl

# Install Redis extension
RUN pecl install redis && docker-php-ext-enable redis

# Install Composer
COPY --from=composer:latest /usr/bin/composer /usr/bin/composer

# Copy composer files
COPY composer.json composer.lock ./

# Install PHP dependencies
RUN composer install --no-dev --optimize-autoloader

# Copy application
COPY . .

# Create necessary directories
RUN mkdir -p storage/logs storage/app/public && \
    chown -R www-data:www-data storage bootstrap/cache

# Expose port
EXPOSE 8000

CMD ["php-fpm"]
