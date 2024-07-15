FROM phpdockerio/php:8.3-fpm

WORKDIR "/app"

# Fix debconf warnings upon build
ARG DEBIAN_FRONTEND=noninteractive

# install php extensions
RUN apt-get update && apt-get -y --no-install-recommends install php8.3-mysql php-imagick \
    && apt-get clean; rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* /usr/share/doc/*

# install locales
RUN apt-get clean && apt-get update && apt-get install -y locales \
    && locale-gen en_US.UTF-8 \
    && locale-gen nl_NL.UTF-8