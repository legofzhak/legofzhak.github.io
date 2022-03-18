---
layout: post
title:  "IAW Practica 16"
date:   2022-03-07 05:17:03 -0600
categories: jekyll update
---
# Práctica 16: «Dockerizar» una aplicación LAMP

En esta práctica creamos un archivo [Dockerfile](./apache/Dockerfile) para crear una imagen Docker que contenga una aplicación web LAMP.

## Requisitos
---
1. Crear una maquina en AWS EC2.
2. Instalar Docker y Docker compose ejecutando el script [install_docker.sh](install_docker.sh).

## Configuración de Dockerfile para implementar una Pila LAMP.
---
```
FROM ubuntu:21.04

LABEL AUTHOR="Zakariya El Khattabi"

ENV DEBIAN_FRONTEND noninteractive

RUN apt update && \
    apt install -y apache2 && \
    apt install -y php && \
    apt install -y libapache2-mod-php && \
    apt install -y php-mysql && \
    rm -rf /var/lib/apt/lists/*

ENTRYPOINT ["/usr/sbin/apache2ctl", "-D", "FOREGROUND"]
```

## Configuración del archivo docker-compose.yml
---
Contiene build ./apache para coger la configuración del archivo [Dockerfile](./apache/Dockerfile) y aplicar la pila LAMP como una imagen y contiene dos 2 imagenes oficiales, una de MySQL y otra de phpMyAdmin. Y para los volumenes se le indica que aplique la configuracion de la aplicación que hay en el repositorio [iaw-practica-lamp](https://github.com/josejuansanchez/iaw-practica-lamp). Y para la aplicación se usa la base de datos que hay adjunta en el repositorio que la usamos como volumen en MySQL.

```YML
version: '3.3'

services:
  apache:
    build: ./apache
    ports:
      - 80:80
    restart: always
    networks:
      - frontend_network
      - backend_network
    depends_on:
      - mysql
    volumes:
      - ./iaw-practica-lamp/src:/var/www/html

  mysql:
    image: mysql:8.0
    environment:
      - MYSQL_ROOT_PASSWORD=${MYSQL_ROOT_PASSWORD}
      - MYSQL_DATABASE=${MYSQL_DATABASE}
      - MYSQL_USER=${MYSQL_USER}
      - MYSQL_PASSWORD=${MYSQL_PASSWORD}
    restart: always
    volumes:
      - mysql_data:/var/lib/mysql
      - ./iaw-practica-lamp/db:/docker-entrypoint-initdb.d
    networks:
      - backend_network
    security_opt:
      - seccomp:unconfined
      
  phpmyadmin:
    image: phpmyadmin:5
    restart: always
    ports:
      - 8080:80
    environment:
      - PMA_HOST=mysql
    depends_on:
      - mysql
    networks:
      - frontend_network
      - backend_network

 
volumes:
  mysql_data:

networks:
  frontend_network:
  backend_network:
```

## Comprobamos la funcionalidad.
--- 
Antes de comenzar debemos editar el archivo config.php de la aplicacion para que nos coja la base de datos. En DB_HOST pondrá localhost debemos cambiarlo a mysql.

![php]({{ site.url }}/img/2.png)

Ejecutamos el archivo docker-compose.yml con el comando `docker-compose up`. Y veremos que comenzara un build y hará la descarga de la pila LAMP.

![lamp]({{ site.url }}/img/1.png)

Accedemos a localhost desde el navegador y añadimos un nuevo datos a la base de datos para comprobar la conexión.

![localhost]({{ site.url }}/img/3.png)