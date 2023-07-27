FROM php:8.2-fpm

RUN apt-get update && apt-get upgrade -y \
    && apt-get install apt-utils -y \
#
#    устанавливаем необходимые пакеты
    && apt-get install cron mc git zip vim wget iputils-ping procps openssh-server libzip-dev libgmp-dev \
        libpq-dev libpng-dev librabbitmq-dev zsh curl libssl-dev libxml2-dev libmagickwand-dev -y \
#    && apt-get install -y --no-install-recommends supervisor \
    && rm -rf /var/lib/apt/lists/*
#
RUN printf "\n" | pecl install imagick redis amqp
#    Включаем необходимые расширения
RUN docker-php-ext-install -j$(nproc) pdo exif mysqli pdo_mysql pgsql pdo_pgsql pcntl \
    ftp soap sockets zip gmp bcmath dom xml gd \
    && pecl install xdebug \
    && docker-php-ext-enable imagick redis amqp xdebug\
#    Чистим временные файлы
    && docker-php-source delete \
    && apt-get autoremove --purge -y && apt-get autoclean -y && apt-get clean -y
#    Ставим composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer
RUN sh -c "$(wget -O- https://github.com/deluan/zsh-in-docker/releases/download/v1.1.1/zsh-in-docker.sh)" -- \
    -t robbyrussell

#ssh setting up
RUN mkdir /var/run/sshd
RUN echo 'root:root' | chpasswd
RUN sed -i 's/PermitRootLogin prohibit-password/PermitRootLogin yes/' /etc/ssh/sshd_config
# SSH login fix. Otherwise user is kicked off after login
RUN sed 's@session\s*required\s*pam_loginuid.so@session optional pam_loginuid.so@g' -i /etc/pam.d/sshd
ENV NOTVISIBLE "in users profile"
RUN echo "export VISIBLE=now" >> /etc/profile
EXPOSE 22
CMD ["/usr/sbin/sshd", "-D"]


WORKDIR /var/www/befree_auth_be

# Copy existing application directory permissions
COPY . /var/www/befree_auth_be
COPY --chown=www-data:www-data . /var/www/befree_auth_be

RUN chmod -R guo+w storage

# ENTRYPOINT ["/usr/bin/supervisord", "-n", "-c",  "/docker/supervisor/supervisord.conf"]

# На 9000 порту запускаем php-fpm
EXPOSE 9000
CMD ["php-fpm"]
