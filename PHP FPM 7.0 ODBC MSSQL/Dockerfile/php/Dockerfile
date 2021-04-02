FROM php:7.0-fpm
LABEL maintainer="Joao Arquimedes."

# update
RUN apt update
RUN apt upgrade -y

ENV ACCEPT_EULA=Y

# Fix debconf warnings upon build
ARG DEBIAN_FRONTEND=noninteractive

# Install selected extensions and other stuff
RUN apt-get -y install apt-utils libxml2-dev gnupg apt-transport-https git

# Install MS ODBC Driver for SQL Server
RUN curl https://packages.microsoft.com/keys/microsoft.asc | apt-key add - \
    && curl https://packages.microsoft.com/config/debian/9/prod.list > /etc/apt/sources.list.d/mssql-release.list \
    && apt-get update \
    && apt-get -y --no-install-recommends install msodbcsql17 unixodbc-dev \
    && pecl install sqlsrv-5.3.0 \
    && pecl install pdo_sqlsrv-5.3.0 \
    && echo "extension=pdo_sqlsrv.so" >> `php --ini | grep "Scan for additional .ini files" | sed -e "s|.*:\s*||"`/30-pdo_sqlsrv.ini \
    && echo "extension=sqlsrv.so" >> `php --ini | grep "Scan for additional .ini files" | sed -e "s|.*:\s*||"`/30-sqlsrv.ini

# Install required extensions
RUN docker-php-ext-install intl mysqli pdo pdo_mysql
RUN docker-php-ext-enable mysqli

# Enable dependencies
# RUN apk add bzip2-dev libpng-dev libzip-dev zip oniguruma-dev
RUN apt-get install -y libpng-dev libjpeg-dev libzip-dev libghc-bzlib-dev libzip-dev
RUN docker-php-ext-install bcmath
RUN docker-php-ext-install bz2 zip
RUN docker-php-ext-install gd
RUN docker-php-ext-install mbstring
RUN docker-php-ext-install session
RUN docker-php-ext-install sockets
RUN docker-php-ext-install zip

# Install composer versao 1.x
RUN php -r "copy('https://getcomposer.org/composer-1.phar', 'composer');"
RUN chmod +x composer && mv composer /usr/local/bin/

# Clear
RUN apt-get clean; rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* /usr/share/doc/*

WORKDIR /var/www/html

EXPOSE 9000

CMD php-fpm
