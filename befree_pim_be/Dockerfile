FROM php:7.4-fpm

RUN apt-get update && apt-get upgrade -y \
    && apt-get install apt-utils -y \
#
#    устанавливаем необходимые пакеты
    && apt-get install mc git zip vim wget iputils-ping procps libzip-dev libgmp-dev \
        libpq-dev libpng-dev zsh curl libssl-dev libxml2-dev -y \
#    && apt-get install -y --no-install-recommends supervisor \
    && rm -rf /var/lib/apt/lists/*
#
#    Включаем необходимые расширения
RUN docker-php-ext-install -j$(nproc) pdo exif pgsql pdo_pgsql pcntl \
    ftp json soap sockets zip gmp bcmath dom xml gd \
#    && pecl install xdebug-3.0.4 \
#    && docker-php-ext-enable xdebug \
#    Чистим временные файлы
    && docker-php-source delete \
    && apt-get autoremove --purge -y && apt-get autoclean -y && apt-get clean -y
#    Ставим composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer
RUN sh -c "$(wget -O- https://github.com/deluan/zsh-in-docker/releases/download/v1.1.1/zsh-in-docker.sh)" -- \
    -t robbyrussell

WORKDIR /var/www/befree_pim_be

# Copy existing application directory permissions
COPY . /var/www/befree_pim_be
COPY --chown=www-data:www-data . /var/www/befree_pim_be

RUN chmod -R guo+w storage

# ENTRYPOINT ["/usr/bin/supervisord", "-n", "-c",  "/docker/supervisor/supervisord.conf"]

# На 9000 порту запускаем php-fpm
EXPOSE 9000
CMD ["php-fpm"]
