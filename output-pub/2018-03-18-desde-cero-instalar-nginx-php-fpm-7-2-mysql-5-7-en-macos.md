---
title: "DESDE CERO: Instalar NGINX + PHP-FPM 7.2 + MySQL 5.7 en macOS"
date: "2018-03-18"
---

En este post quiero mostrarles paso a paso como instalar y configurar tu ambiente de desarrollo en macOS para trabajar con **NGINX + PHP-FPM 7.2 + MySQL 5.7**.

**NGINX** es un potente servidor web/proxy inverso que a su vez incluye servicios de correo electrónico. Es una alternativa al **APACHE** que a mi parecer ha venido ganando mucha reputación últimamente y se ha convertido en el segundo servidor web más utilizado.

Entre alguna de sus bondades mas relevantes tenemos:

- Es ligero y potente a la vez, permite equilibrar la carga entre los servidores back-end, o para proporcionar almacenamiento en caché para un servidor back-end lento.
- Permite ejecutar PHP como un servicio y no como un módulo.
- Permite ejecutar varias versiones de PHP al mismo tiempo, es decir, cada proyecto puede configurarse en el virtual host para que utilice una versión distinta de PHP según sea requerido.

Y para no extendernos tanto vamos a proceder con los pasos de instalación y configuración de nuestro ambiente de desarrollo.

Vale la pena destacar que estaré utilizando **Homebrew** para instalar cada paquete, si deseas saber como instalar **Homebrew**, échale un vistazo a mi post: [Homebrew: Instalar paquetes en macOS nunca fué tan sencillo](https://www.franciscougalde.com/2018/03/10/homebrew-instalar-paquetes-en-macos-nunca-fue-tan-sencillo/). 

> **Importante**: Asegúrate de eliminar primero el apache o en su defecto, detener los servicios y evitar que arranquen con cada inicio del sistema

## INSTALAR Y CONFIGURAR **NGINX + PHP-FPM 7.2 + MySQL 5.7**

## INSTALAR NGINX

El primer paso es ejecutar el siguiente comando para instalar **NGINX**:

brew install nginx

Luego de esto, hay que permitir que NGINX arranque automáticamente al iniciar nuestro sistema:

sudo cp /usr/local/opt/nginx/\*.plist /Library/LaunchDaemons/
sudo chown root:wheel /Library/LaunchDaemons/homebrew.mxcl.nginx.plist

Arrancamos el servidor por primera vez:

sudo launchctl load /Library/LaunchDaemons/homebrew.mxcl.nginx.plist

Generalmente, la configuración por defecto apunta hacia nuestro puerto 8080, por lo que hagamos una prueba de nuestro servidor para verificar que esté ejecutandose:

curl -IL http://localhost:8080

Deberíamos recibir una respuesta parecida a esta:

HTTP/1.1 200 OK
Server: nginx/1.12.1
Date: Sun, 18 Mar 2018 13:22:15 GMT
Content-Type: text/html
Content-Length: 419
Last-Modified: Mon, 14 Aug 2017 20:51:57 GMT
Connection: keep-alive
ETag: "59920d6d-1a3"
Accept-Ranges: bytes

Si por el contrario recibimos una respuesta "**HTTP/1.1 404 Not Found**" podemos probar de nuevo ejecutando:

curl -IL http://localhost:80

Si todo ba bien, entonces procedemos a detener el servicio de **NGINX**:

sudo launchctl unload /Library/LaunchDaemons/homebrew.mxcl.nginx.plist

### CONFIGURAR NGINX

Lo siguiente será configurar lo básico de NGINX para tener todo preparado para nuestros proyectos futuros, para ello crearemos algunos directorios necesarios:

mkdir -p /usr/local/etc/nginx/logs
mkdir -p /usr/local/etc/nginx/sites-available
mkdir -p /usr/local/etc/nginx/sites-enabled
mkdir -p /usr/local/etc/nginx/conf.d
mkdir -p /usr/local/etc/nginx/ssl

#En mi caso utilizo esta ruta para el webroot de mi servidor NGINX
sudo mkdir -p /Users/fjugaldev/Sites

Asignamos permisos a la carpeta de nuestros proyectos:

sudo chown :staff /Users/fjugaldev/Sites
sudo chmod 775 /Users/fjugaldev/Sites

Eliminamos el archivo nginx.conf (que tambien lo tienes disponible en nginx.conf.default por si lo quieres recuperar) y utilizaremos una configuración mejorada:

rm /usr/local/etc/nginx/nginx.conf

curl -L https://gist.githubusercontent.com/fjugaldev/7948bc77aefa24539de5cf45dcde6639/raw/b743ce39ea9a6c73b26d3dbcd667cc479268175d/nginx.conf">https://gist.githubusercontent.com/fjugaldev/7948bc77aefa24539de5cf45dcde6639/raw/b743ce39ea9a6c73b26d3dbcd667cc479268175d/nginx.conf -o /usr/local/etc/nginx/nginx.conf

Utilizaremos una configuración específica para PHP-FPM, para ello lo copiaremos de un repositorio en Github:

curl -L https://gist.github.com/frdmn/7853158/raw/php-fpm -o /usr/local/etc/nginx/conf.d/php-fpm

Vamos a añadir un host virtual por defecto, para esto crearemos un fichero en la ruta "**/usr/local/etc/nginx/sites-availabe"** llamado "**default**"

Y añadimos el siguiente contenido:

server {
    listen       80;
    server\_name  localhost;
    root       /Users/fjugaldev/Sites/;
 
    access\_log  /usr/local/etc/nginx/logs/default.access.log  main;
 
    location / {
        include   /usr/local/etc/nginx/conf.d/php-fpm;
    }

    location = /info {
        allow   127.0.0.1;
        deny    all;
        rewrite (.\*) /.info.php;
    }

    error\_page  404     /404.html;
    error\_page  403     /403.html;
}

> **Nota**: Recuerda cambiar el parámetro root por la ruta que hayan elegido para el **webroot** de su servidor web.

Luego creamos un archivo index.html y info.php en nuestro **webroot **definido.

**index.html**

<!DOCTYPE HTML>
<html lang="en">
	<head>
		<!-- Page title -->
		<title>Nginx</title>

		<!-- Charset -->
		<meta charset="iso-8859-1" />
	</head>
	<body>	
		<div id="content"> 
			<h1 class="headline">Default website</h1>
			<p>Congratulations, your Nginx seems to work just fine. :)</p>
			<p>Let me know, in case you have any problems:</p>
			<ul>
				<li>Twitter: @gil0mendes</li>
			</ul>
		</div>
	</body>
</html>

**info.php**

<?php
echo phpinfo();

Lo siguiente será activar nuestro virtual host por defecto definido, para ello crearemos un symlink hacia nuestra carpeta "**sites-enabled**":

ln -sfv /usr/local/etc/nginx/sites-available/default /usr/local/etc/nginx/sites-enabled/default

Iniciamos de nuevo nuestro servidor **NGINX**:

sudo launchctl load /Library/LaunchDaemons/homebrew.mxcl.nginx.plist

### Controla NGINX como todo un Maestro

Probablemente, más temprano que tarde, tendrás que reiniciar por cualquier razón los servicios de NGINX, PHP-FPM o ver los logs de errores. Te dejo a continuación unos alias que seguro serán de gran utilidad para ti.

Para poder usarlos, debes definir los alias en el archivo ˜/.bash\_profile o en mi caso que utilizo el shell ZSH en el archivo ˜/.zshrc, a continuación los alias:

\### SERVER SERVICES ALIAS
# Nginx
alias nginx.start='sudo launchctl load /Library/LaunchDaemons/homebrew.mxcl.nginx.plist'
alias nginx.stop='sudo launchctl unload /Library/LaunchDaemons/homebrew.mxcl.nginx.plist'
alias nginx.restart='nginx.stop && nginx.start'

# PHP-FPM
alias php72-fpm.start="launchctl load -w ~/Library/LaunchAgents/homebrew.mxcl.php72.plist"
alias php72-fpm.stop="launchctl unload -w ~/Library/LaunchAgents/homebrew.mxcl.php72.plist"
alias php72-fpm.restart='php72-fpm.stop && php72-fpm.start'

# MySQL
alias mysql.start="launchctl load -w ~/Library/LaunchAgents/homebrew.mxcl.mysql.plist"
alias mysql.stop="launchctl unload -w ~/Library/LaunchAgents/homebrew.mxcl.mysql.plist"
alias mysql.restart='mysql.stop && mysql.start'

# Nginx Logs
alias nginx.logs.error='tail -250f /usr/local/etc/nginx/logs/error.log'
alias nginx.logs.access='tail -250f /usr/local/etc/nginx/logs/access.log'

Con esto tenemos activados los siguientes comandos de consola para administrar nuestro entorno de desarrollo:

#### Nginx

nginx.start
nginx.stop
nginx.restart

#### PHP-FPM

php72-fpm.start
php72-fpm.stop
php72-fpm.restart

#### [](https://gist.github.com/mgmilcher/5eaed7714d031a12ed97#mysql-1)MySQL

mysql.start
mysql.stop
mysql.restart

#### Nginx Logs

nginx.logs.error
nginx.logs.access

## Instalar PHP-FPM 7.2

Lo próximo a realizar es instalar **PHP-FPM 7.2**, para ello debemos:

Actualizar las formular de **Homebrew** para **PHP**:

brew tap homebrew/dupes
brew tap homebrew/php

Instalar **PHP-FPM 7.2**

brew install php72 --without-apache --with-fpm --with-mysql

Ya con esto tenemos instalado PHP-FPM 7.2. Con los comandos definidos anteriormente podemos controlar nuestros servicios de PHP-FPM:

php72-fpm.start
php72-fpm.stop
php72-fpm.restart

Si accedemos a **http://localhost** y **http://localhost/info** debemos poder ver un mensaje de que nuestro **NGINX** esta funcionando correctamente y en el segundo url veremos un **phpinfo()** donde nos debe indicar que estamos ejecutando **PHP versión 7.2.x**

## Instalar MYSQL 5.7

El último paso es instalar MySQL 5.7, para ello ejecutamos:

brew install mysql

Lo cual debe instalarnos siempre la última versión de **MySQL**

Lo siguiente será incorporarle seguridad a nuestra instalación de **MySQL**, para ello ejecutamos 

mysql\_secure\_installation

Por defecto **MySQL** viene sin password en el root, por lo que es recomendable ejecutar el comando anterior y seguir los pasos que se nos presenta en el "**Asistente de configuración**"mysql\_secure\_installation

Al ejecutar el comando nos preguntará lo siguiente:

> \> Enter current password for root (enter for none):
> 
> Press ENTER since you don’t have one set.
> 
> \> Change the root password? \[Y/n\]  
> Confirm using ENTER to accept the suggested default answer (Y) and enter it here in the prompt.
> 
> \> Remove anonymous users? \[Y/n\]
> 
> Press again ENTER. They are not necessary.
> 
> \> Disallow root login remotely? \[Y/n\]
> 
> ENTER — No need to log in as root from any other IP than 127.0.0.1.
> 
> \> Remove test database and access to it? \[Y/n\]
> 
> ENTER — You don’t need the testing tables.
> 
> \> Reload privilege tables now? \[Y/n\]
> 
> ENTER — Reload the privilege tables to ensure all of the changes made so far will take effect immediately.

Luego de haber contestado a todos los pasos del asistente, vamos a asegurarnos de que todo funciona bien y que podemos conectarnos a MySQL con la nueva clave establecida, para esto ejecutamos:

mysql -u root -p

Nos pedirá la contraseña y luego de eso veremos el símbolo de sistema de mysql

mysql>

Para salir basta con escribir "**quit**"

Y esto es todo, ya tenemos nuestro entorno de desarrollo ejecutando **NGINX** como servidor web, **PHP-FPM **versión 7.2 y **MySQL 5.7** para nuestro motor de base de datos. 

Si te gustó este post, ayúdame a que pueda servirle a muchas más personas, compartiendo mis contenidos en tus redes sociales.

Espero que este post haya sido de gran ayuda para ti, y como siempre, cualquier inquietud o duda que tengas, puedes contactarme por cualquiera de las vías disponibles, o dejando tus comentarios al final de este post. También puedes sugerir que temas o post te gustaría leer a futuro.

* * *

Si te gustó el post, regálame tus aplausos!!!

\[wp-applause-button style="width:60px;height:60px;margin: 0 auto;" color="black"\]
