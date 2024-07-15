# **Docker LEMPP stack**
A simple Docker LEMPP stack for developing PHP applications.
Author: Lars Eissink

## Description
This is a docker compose / All-in-one solution for developing PHP applications within docker. This stack uses NGINX, MariaDB 11.4 and PHP8.3, but can be fully customized using
the docker-compose file. This projects builds the PHP container itself, so you can add more (or less) PHP extensions. Read the documentation below to see how.

## Documentation
A small documentation on how this project works.

### Setting up a new project
Setting up a new project is as simple as cloning the repo and changing the values below.

### 1. Setting up PHP
Setting up PHP is done by using the following files:
* `./docker/php/Dockerfile` -> Edit PHP build values
* `./docker/php/www.conf` -> Change PHP values like e.g. upload_max_filesize or post_max_size. This file is a FPM configuration file, so it can be used for changing FPM values as well.

#### Changing PHP Version
This project utilizes the PHP-FPM images from phpdockerio. Changing a PHP version is as easy as changing the tag in the 'FROM' section:
**Examples**
```
FROM phpdockerio/php:8.3-fpm
FROM phpdockerio/php:8.2-fpm
FROM phpdockerio/php:8.1-fpm
```

#### Changing PHP values
As earlier said, this can be configured in the `www.conf` file, located in `./docker/php/www.conf` under the `; PHP overrides` section:`

```
; PHP Overrides
php_value[upload_max_filesize] = 100M
php_value[post_max_size] = 108M
```

**!! These are PHP FPM configuration options, so it's important that you use the right format. E.g. `php_value[option_here] = value`**

#### Changing PHP settings
Need more power? No problem! You can configure the settings in the www.conf file. This can be configured in the `www.conf` file, located in `./docker/php/www.conf` under the ` ; FPM Settings` section:

```
; FPM settings
pm = dynamic
pm.max_children = 50
pm.start_servers = 5
pm.min_spare_servers = 5
pm.max_spare_servers = 35
```

#### Changing other PHP settings
Since this project is using PHP-FPM, you can make all kind of necessary changes in the `www.conf` file. **Just make sure to leave the user, group and listen options the same**. Otherwise the container might not start.

#### Default configuration:
```
[www]
user = www-data
group = www-data
listen = /run/php/php8.3-fpm.sock
listen.owner = www-data
listen.group = www-data
listen.mode = 0660

; FPM settings
pm = dynamic
pm.max_children = 50
pm.start_servers = 5
pm.min_spare_servers = 5
pm.max_spare_servers = 35

; PHP Overrides
php_value[upload_max_filesize] = 100M
php_value[post_max_size] = 108M
```

### 2. Setting up Mariadb
Setting up MariaDB is done by using the following files:
* `./docker/docker.env` -> This file is used for setting up the credentials
* `./docker/mariadb/conf` -> Here you can add custom MariaDB settings. See this as a replacement for the `/etc/my.cnf.d` directory.
* `./docker-compose.yml` -> Obvious, but: here you can change the MariaDB versions. Default for this project is the latest LTS release. At the moment this is 11.4. *if needed*, you can also open the MariaDB port so you can use programs like DBeaver. **This is at your OWN risk, since this CAN be a security risk!**

#### Configuring credentials
Setting up MariaDB credentials is as easy as editting the `docker.env` file, located in `./docker/docker.env`. By loading the env file, the values will be added in the docker compose file.
**Example configuration**
```
# Mariadb settings
MYSQL_ROOT_PASSWORD=ROOT_PASSWORD
MYSQL_DATABASE=DATABASE_NAME
MYSQL_USER=DATABASE_USER
MYSQL_PASSWORD=MYSQL_PASSWORD
```

#### Configuring MariaDB values
E.g. if you want to change the `buffer_pool_size`, you can do this in `./docker/mariadb/conf`. Simply create a new file e.g. `bufferpool.cnf` and the container will load this file and its values.
**Example configuration**
```
[mysqld]
innodb_buffer_pool_size = 4096M
```

### 3. Setting up NGINX / Your application
Your application can be deployed in the `./docker/app` directory. Your public-facing content can be placed in the `public_html` directory. If you're developing a Laravel application, you can change the path ofcourse. Just make sure you make the same changes in the PHP and NGINX Vhost config.

---
If you're facing any issues, please let me know by creating a issue :) Contributors are welcome!

