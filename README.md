# docker-drupal7


Database docker container
=====================================


We can run mysql database using this command:

```shell

    docker run --name mysql -e MYSQL_ROOT_PASSWORD=my-secret-pw -d solfisk/mariadb-percona:latest

```


Configuration options
=====================================
This values are loaded from process environment and can be used to configure application.

Set timezone

- `TIMEZONE` `Europe/Moscow`

Set database configuration:

- `MYSQL_HOST` `mysql`

- `MYSQL_PORT` `3306`

- `MYSQL_USER` `admin`

- `MYSQL_DATABASE` `nota_dk`

- `MYSQL_PASSWORD` `admin`

Set site name

- `SITENAME` `my-site.com`

Set PHP configuration parameters

- `PHP_MEMORY_LIMIT` `512M`

- `MAX_UPLOAD` `50M`

- `PHP_MAX_FILE_UPLOAD` `200`

- `PHP_MAX_POST` `100M`



Configuriing site config
===================================

Examine the contents of `sites-availble/` folder and tune/generate yours one.
Upload your SSL certificates to `ssl/` folder, that are used by your site `.conf` file.



Starting container
====================================

Under construction - not the final way of starting it;

Firstly, we need to start the database container

```

    # docker run --name mysql -e MYSQL_ROOT_PASSWORD=my-secret-pw -d solfisk/mariadb-percona:latest

```

Than we need to inspect it to get it's IP address on local network being bridged

```

    # docker inspect mysql

```

I have it running on `IPAddress": "172.17.0.2"`.

Than we need to create database on this host. Unfortunatly, linking hosts is not working when we try to build the container!

```

    $ mysql_setpermission --host 172.17.0.2 -user root --password

```

You need to create user `admin` with password `admin` and database of name of `nota_dk` and grant 
SELECT,INSERT,UPDATE,DELETE,CREATE,DROP,INDEX,ALTER permissions on it from any host.
I used mysql workbench for doing this.

Than you need to set database host on Dockerfile - i know, this is wierd, but it is required step, because 
the build script requires the drush to be started to populate database with initial values.

So, we need to set the 

```

    ENV MYSQL_HOST 172.17.0.2

```
here https://github.com/vodolaz095/docker-drupal7/blob/master/Dockerfile#L14 in the dockerfile


Than you can try to build the container

```

    # docker -t docker-drupal7 build .

```

This container has this:

- nginx running on 0.0.0.0:80, 0.0.0.0:443 ports

- PHP-FPM running on 127.0.0.1:9000 port

- memcached running on 127.0.0.1:11211 port

- drupal files saved in `/var/www/localhost/htdocs`

- script to backup data

- script to restore data

Info
====================================

Start and link mysql container in private network

https://docs.docker.com/engine/userguide/networking/work-with-networks/#linking-containers-in-user-defined-networks

Start and link drupal container in private network



docker -t docker-drupal7 build .



