---
layout: post
title:  "IAW Practica 16"
date:   2022-03-07 05:17:03 -0600
categories: jekyll update
---

# Práctica 13: Instalación de WordPress con Bitnami

1. Creamos una máquina Ubuntu en AWS Ubuntu t2.medium (Mas de 2Gb de RAM).

2. Abrimos los puertos SSH, HTTP y HTTPS. 

    ![puertos](./img/1.PNG)

3. Para realizar la instalación de WordPress con Bitnami ejecutaremos el script [install_bitnami.sh](install_bitnami.sh). Que se encarga de borrar alguna instalación previa, bajarse el archivo .run, darle permisos y ejecutarlo. Para ello le damos permisos de ejecucion a [install_bitnami.sh](install_bitnami.sh) con `sudo chmod +x install_bitnami.sh`. 

4. Ejecutamos el script con privilegios de administrador `sudo ./install_bitnami.sh ` y seguimos los siguientes pasos:
    + Al ejecutar install_bitnami.sh comenzará un panel de instalción en el cual no pedirá algunas cosas para configurar el sitio WordPress. Primero preguntará sobre el idioma que se va a usar para el sitio.

    ![paso 1](./img/2.PNG)

    * Nos pedirá que indiquemos que elementos querems instalar.

    ![paso 2](./img/3.PNG)

    - Preguntará sobre que directorio queremos instalar la confguración del sitio, lo dejamos por defecto.

    ![paso 3](./img/5.PNG)

    + Pedirá que indiques un nombre, email, nombre de usuario y contraseña.

    ![paso 4](./img/6.PNG)

    * Pedirá un nombre para el sitio, y si queremos instalar WordPress en Bitnami, comenzará la instalación.

    ![paso 5](./img/7.PNG)

    - Finalmente WordPress estara intalado con Bitnami.

    ![paso 6](./img/8.PNG)
    
5. Una vez terminada la instalación podemos entrar en la IP Publica de la máquina para comprobar la instalación.

    La página de inicio tendra este aspecto. 

    ![inicio](./img/9.png)

6. Para evitar el inicio de bitnami y que entre al inicio del sitio en WordPress hacemos lo siguiente.

    Ejecutaremos el siguiente comando que lo que hará es cambiar el inicio de bitnami al inicio de la página wordpress.

    ```
    sudo /opt/wordpress-5.9-0/apps/wordpress/bnconfig --appurl /
    ```

    Despues de ejecutarlo el inicio sera el sitio de WordPress.

    ![inicio-wp](./img/10.png)

7. Si nos fijamos ahora el inicio tiene un banner en la esquina de abajo de la derecha. Para desacernos de el haremos lo siguiente.

    ![banner](./img/11.png)

    Para quitar este banner lo que haremos es acceder al fichero `/opt/wordpress-5.9-0/apps/wordpress/conf/httpd-app.conf`. E ir al final del fichero y comentar la linea `Include "/opt/wordpress-5.9-0/apps/wordpress/conf/banner.conf"`.

    Editamos el fichero httpd-app.conf.
    ```
    sudo nano /opt/wordpress-5.9-0/apps/wordpress/conf/httpd-app.conf
    ```
    ![coment banner](./img/12.png)

    Guardamos la configuración y ahora solo queda reiniciar los servicios de apache para ello hacemos lo siguiente.

    ```
    cd /opt/wordpress-5.9-0
    ```

    Veremos que hay un archivo llamado ctlscript.sh. Podemos ver lo que puede hacer ejecutandolo.

    ```
    ubuntu@ip-172-31-90-71:/opt/wordpress-5.9-0$ sudo ./ctlscript.sh
    usage:  ./ctlscript.sh help
            ./ctlscript.sh (start|stop|restart|status)
            ./ctlscript.sh (start|stop|restart|status) mariadb
            ./ctlscript.sh (start|stop|restart|status) apache

    help       - this screen
    start      - start the service(s)
    stop       - stop  the service(s)
    restart    - restart or start the service(s)
    status     - show the status of the service(s)
    ```

    Reiniciamos apache.
    ```
    ubuntu@ip-172-31-90-71:/opt/wordpress-5.9-0$ sudo ./ctlscript.sh restart apache
    Syntax OK
    /opt/wordpress-5.9-0/apache2/scripts/ctl.sh : httpd stopped
    Syntax OK
    /opt/wordpress-5.9-0/apache2/scripts/ctl.sh : httpd started at port 80
    ```

    Una vez terminado este proceso el banner ya no estará visible en la página web.

8. Por ultimo solo falta configurar un certificado Lest`s Encrypt.

    Bitnami trar su propia utilidad para configurar certificados HTTPS. Esta utilidad es **bncert-tool**. Antes de ejecutarlo vamos a poner nuestra IP Pública en uno de los servicios de dominio DNS como [NOIP](https://www.noip.com/es-MX).

    Ejecutamos la utilidad.
    ```
    sudo /opt/wordpress-5.9-0/bncert-tool
    ```

    Seguimos los pasos y solo los necesarios.
    ```
    ubuntu@ip-172-31-90-71:~$ sudo /opt/wordpress-5.9-0/bncert-tool 
    ----------------------------------------------------------------------------
    Welcome to the Bitnami HTTPS Configuration tool.

    ----------------------------------------------------------------------------
    Domains

    Please provide a valid space-separated list of domains for which you wish to
    configure your web server.

    Domain list []: word-zelk.duckdns.org

    The following domains were not included: www.word-zelk.duckdns.org. Do you want to add them? [Y/n]: n

    Warning: No www domains (e.g. www.example.com) or non-www domains (e.g. 
    www.example.com) have been provided, so the following redirections will be
    disabled: non-www to www, www to non-www.
    Press [Enter] to continue:
    ----------------------------------------------------------------------------
    Enable/disable redirections

    Please select the redirections you wish to enable or disable on your Bitnami
    installation.

    Enable HTTP to HTTPS redirection [Y/n]: y

    Do you agree to these changes? [Y/n]: y

    ----------------------------------------------------------------------------
    Create a free HTTPS certificate with Let's Encrypt

    Please provide a valid e-mail address for which to associate your Let's Encrypt
    certificate.

    Domain list: word-zelk.duckdns.org

    Server name: word-zelk.duckdns.org

    E-mail address []: zelk991@g.educaand.es

    The Let's Encrypt Subscriber Agreement can be found at:

    https://letsencrypt.org/documents/LE-SA-v1.2-November-15-2017.pdf

    Do you agree to the Let's Encrypt Subscriber Agreement? [Y/n]: y

    ----------------------------------------------------------------------------
    Performing changes to your installation

    The Bitnami HTTPS Configuration Tool will perform any necessary actions to your 
    Bitnami installation. This may take some time, please be patient.

    ----------------------------------------------------------------------------
    Success

    The Bitnami HTTPS Configuration Tool succeeded in modifying your installation.

    The configuration report is shown below.

    Backup files:
    * /opt/wordpress-5.9-0/apache2/conf/httpd.conf.back.202203161801
    *
    /opt/wordpress-5.9-0/apache2/conf/bitnami/bitnami-apps-prefix.conf.back.202203161
    801
    * /opt/wordpress-5.9-0/apache2/conf/bitnami/bitnami.conf.back.202203161801

    Find more details in the log file:

    /tmp/bncert-202203161801.log

    If you find any issues, please check Bitnami Support forums at:

    https://community.bitnami.com

    Press [Enter] to continue:
    ```

9. Finalmente tenemos nuestros sitio listo con certificado HTTPS.

![fin](./img/13.png)

10. Creamos un tunel SSH para phpMyAdmin.

    Para acceder a phpMyAdmin tendremos que crear un tunel SSH desde nuestra máquina a la maquina romta.

    Usando la sintaxis para crear un tunel SSH `ssh -N -L 8888:127.0.0.1:80 -i KEYFILE USER@SERVER-IP`.

    En mi caso: `ssh -N -L 8888:127.0.0.1:80 -i "iaw-zaka.pem" ubuntu@ec2-3-82-163-169.compute-1.amazonaws.com`

    Accedemos al puerto 8888 desde el navegador con localhost.

    ![tunel ssh](./img/14.png)