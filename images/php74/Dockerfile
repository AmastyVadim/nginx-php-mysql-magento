FROM composer:latest AS composer
FROM php:7.4-fpm

ARG USER_ID
ARG GROUP_ID

RUN apt-get update \
  && apt-get install -y \
    procps \
    libfreetype6-dev \
    libldap2-dev \
    libicu-dev \
    libjpeg62-turbo-dev \
    libmcrypt-dev \
    libpng-dev \
    libxslt1-dev \
    libzip-dev \
    libsodium-dev \
    libonig-dev \
    libpq-dev \
    npm \
    python-pip \
    python2.7 \
    python2.7-dev \
    webp \
    optipng \
    jpegoptim \
    jq \
    gifsicle \
    sudo

# Configure the gd library
RUN docker-php-ext-configure \
  gd --with-freetype --with-jpeg
# Install required PHP extensions
RUN docker-php-ext-install \
  dom \
  gd \
  intl \
  mbstring \
  pdo_mysql \
  iconv \
  xsl \
  zip \
  soap \
  bcmath \
  opcache \
  pcntl \
  ldap \
  xmlwriter \
  simplexml \
  mysqli \
  sockets


#Install xdebug and configure
RUN yes | pecl install xdebug-2.9.8
RUN echo "zend_extension=$(find /usr/local/lib/php/extensions/ -name xdebug.so)" > /usr/local/etc/php/conf.d/xdebug.ini \
    && echo "xdebug.remote_enable=on" >> /usr/local/etc/php/conf.d/xdebug.ini \
    && echo "xdebug.remote_autostart=off" >> /usr/local/etc/php/conf.d/xdebug.ini \
    && echo "xdebug.remote_connect_back=1" >> /usr/local/etc/php/conf.d/xdebug.ini \
    && sed -i 's/;pm.max_requests/pm.max_requests/g' /usr/local/etc/php-fpm.d/www.conf

#Configure sendmail and php
RUN echo "memory_limit=3G" > /usr/local/etc/php/conf.d/magento.ini \
    && echo "realpath_cache_size=2M" >> /usr/local/etc/php/conf.d/magento.ini \
    && echo "opcache.max_accelerated_files=200000" >> /usr/local/etc/php/conf.d/magento.ini \
    && echo "opcache.memory_consumption=256M" >> /usr/local/etc/php/conf.d/magento.ini \
    && echo "max_execution_time = 600" >> /usr/local/etc/php/conf.d/magento.ini \
    && echo "zlib.output_compression=1" >> /usr/local/etc/php/conf.d/magento.ini \
    && echo "xdebug.profiler_enable = 0" >> /usr/local/etc/php/conf.d/xdebug.ini \
    && echo "xdebug.profiler_enable_trigger = 1" >> /usr/local/etc/php/conf.d/xdebug.ini \
    && echo "xdebug.profiler_output_dir = "/home/7.3"" >> /usr/local/etc/php/conf.d/xdebug.ini

#Install composer
RUN curl --insecure https://getcomposer.org/download/1.10.16/composer.phar -o /usr/bin/composer && chmod +x /usr/bin/composer

#Install composer plugin for parallel installs

RUN composer global require hirak/prestissimo


#COPY --from=edspc/mhsendmail:alpine /go/bin/mhsendmail /usr/bin/mhsendmail
RUN apt-get update &&\
    apt-get install --no-install-recommends --assume-yes --quiet ca-certificates curl git &&\
    rm -rf /var/lib/apt/lists/* &&\
    update-ca-certificates -f
RUN curl -Lsf 'https://storage.googleapis.com/golang/go1.8.3.linux-amd64.tar.gz' | tar -C '/usr/local' -xvzf -
ENV PATH /usr/local/go/bin:$PATH
RUN go get github.com/mailhog/mhsendmail
RUN cp /root/go/bin/mhsendmail /usr/bin/mhsendmail
RUN echo 'sendmail_path = /usr/bin/mhsendmail --smtp-addr mailhog:1025' >> /usr/local/etc/php/conf.d/magento.ini

#Configure permissions

RUN apt-get install -y git

ADD ./php.ini /usr/local/etc/php/php.ini

COPY --from=composer /usr/bin/composer /usr/bin/composer

#RUN wget https://getcomposer.org/installer -O - -q   | php -- --install-dir=/bin --filename=composer --quiet

RUN usermod -u ${USER_ID} www-data && groupmod -g ${GROUP_ID} www-data

WORKDIR /var/www

USER "${USER_ID}:${GROUP_ID}"

CMD ["php-fpm"]
