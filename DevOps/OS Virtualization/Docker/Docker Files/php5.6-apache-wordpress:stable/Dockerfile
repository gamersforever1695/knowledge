FROM ubuntu:latest

MAINTAINER Joshua Clifford Reyes <reyesjoshuaclifford@gmail.com>

RUN apt-get update -y
RUN apt-get install -y software-properties-common
RUN apt-get install -y python-software-properties
RUN LC_ALL=C.UTF-8 add-apt-repository ppa:ondrej/php

# Supplimentary Programs (cron, git, vim, etc.)
RUN apt-get update --fix-missing -y && apt-get install -y \
    wget \
    curl \
    zip \
    vim \
    nano \
    wkhtmltopdf \
    libxrender1 \
    git \
    cron \
    openssl \
    lynx-cur \
    php-xdebug \
    php-gd \
    libapache2-mod-php5.6

# Install Apache2, PHP 5.6 and Extensions related.
RUN apt-get update --fix-missing -y && apt-get install -y \ 
    apache2 \
    php5.6 \
    php5.6-fpm \
    php5.6-mbstring \
    php5.6-curl \
    php5.6-mcrypt \
    php5.6-tokenizer \
    php5.6-dom \
    php5.6-zip \
    php5.6-mysql \
    php5.6-imap \
    php5.6-xml \
    php5.6-cli \
    php5.6-gd

# Install required wordpress app to run WP CLI testing tool.
RUN apt-get update --fix-missing -y && apt-get install -y \
    subversion \
    mysql-client

# Install PHP Composer
RUN php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
RUN php -r "if (hash_file('SHA384', 'composer-setup.php') === '544e09ee996cdf60ece3804abc52599c22b1f40f4323403c44d44fdfdd586475ca9813a858088ffbc1f233e9b180f061') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;"
RUN php composer-setup.php --install-dir=/usr/local/bin --filename=composer
RUN php -r "unlink('composer-setup.php');"

# Enable apache mods.
RUN a2enmod php5.6
RUN a2enmod rewrite
RUN a2enmod headers

# Update the php.ini file, enable <? ?> tags and quieten logging.
RUN sed -i "s/short_open_tag = Off/short_open_tag= On/" /etc/php/5.6/apache2/php.ini
RUN sed -i "s/error_reporting = .*$/error_reporting = E_ERROR | E_WARNING | E_PARSE/" /etc/php/5.6/apache2/php.ini

# Manually set up the apache environment variables.
ENV APACHE_RUN_USER www-data
ENV APACHE_RUN_GROUP www-data
ENV APACHE_LOG_DIR /var/log/apache2
ENV APACHE_LOCK_DIR /var/lock/apache2
ENV APACHE_PID_FILE /var/run/apache2.pid

RUN echo "ServerName localhost" >> /etc/apache2/apache2.conf

# Expose ports.
EXPOSE 80

# Update the default apache site with the following we created.
ADD apache-config.conf /etc/apache2/sites-enabled/000-default.conf

# Change permission in this folder.
RUN chown -R www-data:www-data /var/www \ 
    && find /var/www -type d -exec chmod 755 {} \; \
    && find /var/www -type f -exec chmod 644 {} \;

# Replace the php environment to development mode.
RUN cp /usr/lib/php/5.6/php.ini-development /etc/php/5.6/apache2
RUN mv /etc/php/5.6/apache2/php.ini /etc/php/5.6/apache2/php.ini.bak
RUN mv /etc/php/5.6/apache2/php.ini-development /etc/php/5.6/apache2/php.ini

ENTRYPOINT ["/bin/sh"]
