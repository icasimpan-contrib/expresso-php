# Expresso PHP

This is a fast and simple Docker setup for all your PHP development. Quick but not dirty. 

Tested with:

* Drupal 7 and Drupal 8
* Wordpress
* Laravel

We won't force you to download our own images. Everything is done using the official images of PHP, Nginx ... etc.

## LAMP or LEMP + friends

What's included:
  - Apache
  - Nginx
  - PHP
  - MariaDB
  - PhpMyAdmin
  - Mailhog
  - Solr

All using on official images.

### Simplified Usage
A wrapper script named 'expresso-php' can now be used with the following syntax:

```
Usage: expresso-php <stack> <action>
  where:
    > stack - either of the following:
                * lamp  - lamp without solr
                * lemp  - lemp without solr
                * lamps - lamp with solr
                * lemps - lamp with solr
              NOTE: lamp = Linux, Apache, MariaDB
                    lemp = Linux, Nginx, MariaDB
              All stack comes with phpmyadmin and mailhog

    > action - except for 'status' either of the following applied to docker-compose:
                * start    - 'up -d'
                * stop     - 'stop'
                * recreate - 'up --build -d'
                * clean    - 'down -v'
                * status   - 'docker ps -a -f name=epdev'
```

### Official containers only
We won't force you to download our own images.
All of this is based on officials containers, so you won't download any specific containers to use this project. 

All you have all the options available as soon as they are released on Docker Hub by PHP, Nginx, Apache ... etc.

### PHP Dockerfile
You have two PHP Dockerfile, depending on web server used.
* docker/php_apache/Dockerfile - for Apache
* docker/php_nginx/Dockerfile - for Nginx
It's important to note that only one web server can be active at a time.

### PHP 7.0? PHP 7.1? or PHP 5.6?
You can switch between the latest release of PHP 7 or the latest of PHP 5, by
changing the first line of the PHP Dockerfile. For the latest version of PHP 5:
```
FROM php:5-fpm
```

Extra PHP libraries?
Check the same PHP Dockerfile
You can add your own libraries.

Everytime you change a dockerfile (example "docker/php_nginx/Dockerfile"), you need to rebuild or recreate your containers as follows:
```
 ~$ ./expresso-php <stack> recreate
```

### More PHP versions?
This project uses the official images from PHP, see all available options on: [PHP's official Docker Hub page](https://hub.docker.com/_/php/).

## Get started
1. Install the prerequisites.
2. Create the docker containers.
3. Place your files in web.

### Install the prerequisites
*Linux*  
You can use the official documentation on installing Docker: https://docs.docker.com/install/

*macOS*  
We recommend you to use "Dinghy" for faster development using Docker on MacOS.
Follow the steps: https://github.com/codekitchen/dinghy#install  
Then install docker-compose using brew:
``
brew install docker docker-machine
``

*Windows*  
It seems that Docker is supported only by Windows 10 pro edition. The official documentation on installing 
[Docker](https://docs.docker.com/install/)

### Create the docker containers

*Steps*
1. Download this project and rename the folder according to your project.
A good practice is to create a folder called "docker" in your home directory, this is where you will keep all your docker projects. In command line, this would be:
```
$ cd ~
$ mkdir docker
$ git clone https://github.com/expresso-php/expresso-php.git myproject
```
2. Create the Expresso PHP containers for your project:
```
$ ./expresso-php <stack> start
```
3. Check which port is Nginx using with:
```
$ ./expresso-php <stack> status
```

In the example output below, look for the nginx line that maps to port 80:

```
$ ./expresso-php lemps status
CONTAINER ID        IMAGE                   COMMAND                  CREATED             STATUS              PORTS                             NAMES
5ea212a4c1f5        epdev_php_nginx         "docker-php-entrypoi…"   19 seconds ago      Up 17 seconds       9000/tcp                          epdev_php_nginx_1
dd4be0b7efd0        mariadb                 "docker-entrypoint.s…"   19 seconds ago      Up 17 seconds       3306/tcp                          epdev_db_1
aec749aa1ed5        phpmyadmin/phpmyadmin   "/run.sh supervisord…"   19 seconds ago      Up 17 seconds       9000/tcp, 0.0.0.0:32868->80/tcp   epdev_phpmyadmin_1
60f40b270854        epdev_nginx             "nginx -g 'daemon of…"   19 seconds ago      Up 17 seconds       0.0.0.0:32867->80/tcp             epdev_nginx_1
0e7bb2c23c9e        solr                    "docker-entrypoint.s…"   19 seconds ago      Up 17 seconds       0.0.0.0:8983->8983/tcp            epdev_solr_1
```
which in this case is "32867".

That's it! Visit http://localhost:32867 (replace 32867 with your application's port) or http://192.168.99.100:32867 (if you use a VM like [dinghy](https://github.com/codekitchen/dinghy)).

### Place your files in web.
Expresso PHP will be looking for PHP files placed into the folder web.
You can delete the existing web folder, and replace it by your project.

If your project is on git, a good practice is to do a symlink to your project. According to the previous step in this README, here is how to do in command line:
```
$ cd docker
$ cd myproject
$ rm -rf web
```
Git clone or download your project, example:
```
git clone https://github.com/myproject/myproject.git myproject
```
Create a symlink with your project's php files and web, example:
```
$ ln -s myproject/docroot web
```

## Useful commands
To start Expresso PHP:
```
$ ./expresso-php <stack> start
```

To stop Expresso PHP:
```
$ ./expresso-php <stack> stop
```

To switch from using Nginx to Apache:
```
$ ./expresso-php lemp clean
$ ./expresso-php lamp start
```

To SSH the containers
```
$ docker exec -it <container_name> /bin/bash
```

Drush (command line tool for Drupal)
```
$  docker exec -it <container_name> drush help
```

Import database with Drupal and Drush
```
$  docker run --rm <container_name> bash -c '$(drush sql-connect)' < my_database.sql
```

### Database credentials
This is for development !!

* User: expresso-php
* Password: expresso-php
* Database: expresso-php

### MySQL and PhpMyAdmin credentials
To access PhpMyAdmin, run `docker ps` to get its dynamic port.
* Username: root
* Password: root

## SOLR Integration
To enable SOLR, use the <stack> with an 's' at the end: 'lamps' or 'lemps'.
SOLR can be accessed in [local-domain]:8983/solr/#/ ( e.g. http://192.168.99.100:8983/solr/#/ )
