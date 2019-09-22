---
title: "Docker: crear entorno de desarrollo web local – Parte II"
date: "2019-02-22"
---

En la **parte  I** de **_[Docker: crear entorno de desarrollo web local](https://www.franciscougalde.com/2019/02/16/docker-entorno-de-desarrollo-web-local-parte-i/)_** [](https://www.franciscougalde.com/2019/02/16/docker-entorno-de-desarrollo-web-local-parte-i/) te mostré de forma clara y sencilla lo que es Docker y cómo funciona. También te facilité algunos comandos útiles que una vez que domines con más fluidez el uso de contenedores, serán tu mejor aliado.

Ahora bien, manos a la obra y comencemos a configurar nuestro **_entorno de desarrollo web local usando Docker_**. Aprovecho para recordarte que configuraremos nuestros servicios y contenedores para usar **_Nginx_** + **_PHP 7.2_** + **_MySQL 5.7_** y probaremos que todo funciona con una instalación de **_Symfony 4_**.

La estructura de directorios que usaremos sera esta:

```
/var/www/html/docker-devenv-sf4/
|-- docker
|   |-- app
|   |   |-- Dockerfile
|   |   |-- php.ini
|   |   |-- xdebug.ini
|   |-- mysql
|   |   |-- Dockerfile
|   |   |-- mysql.cnf
|   |-- nginx
|   |   |-- Dockerfile
|   |   |-- default.template
|   |   |-- nginx.conf
|   |   |-- openssl.cnf
|-- symfony42
|-- docker-compose.yml
|-- .env
```

Los pasos a seguir para la creación de nuestro **entorno de desarrollo web local** con **Docker** son:

## 1\. Crear nuestros servicios y contenedores.

**Docker**, entre uno de sus tantas formas para generar y ejecutar contenedores de servicios, utiliza una herramienta llamada **docker-compose** la cual requiere de un fichero "**docker-compose.yml**" en donde definiremos todos aquellos servicios que deseamos para nuestro entorno de desarrollo.

Visto de forma sencilla, este fichero es como el "**composer.json**" de "**Composer**", o el "**package.json**" de **npm**

Antes de comenzar, debemos situarnos en el directorio donde queremos tener nuestro proyecto, en mi caso suelo utilizar el directorio base "**/var/www/html**".

Ejecutamos:

```
# Nos situamos en el directorio base.
cd /var/www/html
```

Seguidamente, crearemos un directorio para nuestro proyecto ejemplo de Symfony 4.

```
# Creamos el directorio.
mkdir docker-devenv-sf4

# Accedemos al directorio.
cd docker-devenv-sf4
```

Ahora vamos a crear el resto de directorios que vamos a necesitar más adelante:

```
# Directorios que vamos a necesitar
mkdir docker
mkdir -p docker/app
mkdir -p docker/mysql
mkdir -p docker/nginx
```

Procedemos a crear los ficheros necesarios, que en conjunto con el "**docker-compose.yml**" armarán nuestro entorno de desarrollo.

#### /var/www/html/docker-devenv-sf4/docker/app/Dockerfile

```
# /var/www/html/docker-devenv-sf4/docker/app/Dockerfile
FROM php:7.2-fpm

ARG timezone

RUN apt-get update && apt-get install --no-install-recommends --no-install-suggests -y \
		git \
        unzip \
        libicu-dev \
        zlib1g-dev \
        libssl-dev \
        pkg-config \
        libpq-dev \
    && rm -rf /var/lib/apt/lists/*

RUN set -xe \
    && docker-php-ext-configure \
        intl \
    && docker-php-ext-install \
        intl \
        opcache \
        zip \
        pdo \
        pdo_mysql \
        pdo_pgsql \
    && pecl install \
        apcu \
        xdebug \
        mongodb \
        redis \
    && docker-php-ext-enable \
        apcu \
        xdebug \
        mongodb \
        redis

COPY php.ini /usr/local/etc/php/php.ini
RUN sed -i -e "s#TIMEZONE#$timezone#g" /usr/local/etc/php/php.ini

COPY xdebug.ini /tmp/xdebug.ini
RUN cat /tmp/xdebug.ini >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini
RUN rm /tmp/xdebug.ini

COPY --from=composer:1.6 /usr/bin/composer /usr/bin/composer
# https://getcomposer.org/doc/03-cli.md#composer-allow-superuser
ENV COMPOSER_ALLOW_SUPERUSER 1
# create composer cache directory
RUN mkdir -p /var/www/.composer && chown -R www-data /var/www/.composer

RUN usermod -u 1000 www-data

WORKDIR /var/www/html
```

#### /var/www/html/docker-devenv-sf4/docker/app/php.ini

```
# /var/www/html/docker-devenv-sf4/docker/app/php.ini
apc.enable_cli = 1
date.timezone = TIMEZONE
session.auto_start = Off
short_open_tag = Off
# http://symfony.com/doc/current/performance.html
opcache.max_accelerated_files = 20000
realpath_cache_size = 4096K
realpath_cache_ttl = 600
```

#### /var/www/html/docker-devenv-sf4/docker/app/xdebug.ini

```
# /var/www/html/docker-devenv-sf4/docker/app/xdebug.ini
xdebug.remote_enable=1
xdebug.remote_connect_back=1
xdebug.idekey="PHPSTORM"
xdebug.remote_port=9001
```

#### /var/www/html/docker-devenv-sf4/docker/mysql/Dockerfile

```
# /var/www/html/docker-devenv-sf4/docker/mysql/Dockerfile 
FROM mysql:5.7

COPY mysql.cnf /etc/mysql/conf.d/custom.cnf
```

#### /var/www/html/docker-devenv-sf4/docker/mysql/mysql.cnf

```
# /var/www/html/docker-devenv-sf4/docker/mysql/mysql.cnf
[mysqld]
# Version 5.5.3 introduced "utf8mb4", which is recommended
collation-server     = utf8mb4_unicode_ci
character-set-server = utf8mb4
```

#### /var/www/html/docker-devenv-sf4/docker/nginx/Dockerfile

```
# /var/www/html/docker-devenv-sf4/docker/nginx/Dockerfile
FROM nginx:1.13

ARG nginx_host

RUN apt-get update && apt-get install --no-install-recommends --no-install-suggests -y \
        openssl \
    && rm -rf /var/lib/apt/lists/*

COPY openssl.cnf /etc/ssl/openssl.cnf
RUN sed -i -e "s/NGINX_HOST/$nginx_host/g" /etc/ssl/openssl.cnf

RUN openssl genrsa -des3 -passout pass:NotSecure -out /etc/ssl/server.pass.key 2048
RUN openssl rsa -passin pass:NotSecure -in /etc/ssl/server.pass.key -out /etc/ssl/server.key
RUN rm /etc/ssl/server.pass.key
RUN openssl req -new -passout pass:NotSecure -key /etc/ssl/server.key -out /etc/ssl/server.csr \
    -subj "/C=MX/ST=Mexico/L=Mexico/O=Symfony/CN=$nginx_host" -config /etc/ssl/openssl.cnf
RUN openssl x509 -req -sha256 -days 365 -in /etc/ssl/server.csr -signkey /etc/ssl/server.key \
    -out /etc/ssl/server.crt -extensions v3_req -extfile /etc/ssl/openssl.cnf

ADD nginx.conf /etc/nginx/

RUN rm /var/log/nginx/access.log /var/log/nginx/error.log
```

#### /var/www/html/docker-devenv-sf4/docker/nginx/default.template

```
# /var/www/html/docker-devenv-sf4/docker/nginx/default.template
server {
    listen 80;
    listen 443 ssl;
    server_name ${NGINX_HOST};
    ssl_certificate /etc/ssl/server.crt;
    ssl_certificate_key /etc/ssl/server.key;
    root /var/www/html/public;

    location / {
            # try to serve file directly, fallback to index.php
            try_files $uri /index.php$is_args$args;
    }

    location ~ ^/index\.php(/|$) {
            fastcgi_pass app:9000;
            fastcgi_split_path_info ^(.+\.php)(/.*)$;
            include fastcgi_params;

            # optionally set the value of the environment variables used in the application
            # fastcgi_param APP_ENV prod;
            # fastcgi_param APP_SECRET <app-secret-id>;
            # fastcgi_param DATABASE_URL "mysql://db_user:db_pass@host:3306/db_name";

            # When you are using symlinks to link the document root to the
            # current version of your application, you should pass the real
            # application path instead of the path to the symlink to PHP
            # FPM.
            # Otherwise, PHP's OPcache may not properly detect changes to
            # your PHP files (see https://github.com/zendtech/ZendOptimizerPlus/issues/126
            # for more information).
            fastcgi_param SCRIPT_FILENAME $realpath_root$fastcgi_script_name;
            fastcgi_param DOCUMENT_ROOT $realpath_root;

            # Prevents URIs that include the front controller. This will 404:
            # http://domain.tld/index.php/some-path
            # Remove the internal directive to allow URIs like this
            internal;
    }

    # return 404 for all other php files not matching the front controller
    # this prevents access to other php files you don't want to be accessible.
    location ~ \.php$ {
        return 404;
    }

    error_log /var/log/nginx/app_error.log;
    access_log /var/log/nginx/app_access.log;
}
```

#### /var/www/html/docker-devenv-sf4/docker/nginx/nginx.conf

```
# /var/www/html/docker-devenv-sf4/docker/nginx/nginx.conf
user  nginx;
worker_processes  1;

error_log  /var/log/nginx/error.log warn;
pid        /var/run/nginx.pid;


events {
    worker_connections  1024;
}


http {
    include       /etc/nginx/mime.types;
    default_type  application/octet-stream;

    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  /var/log/nginx/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    keepalive_timeout  65;

    #gzip  on;

    include /etc/nginx/conf.d/*.conf;
}
```

#### /var/www/html/docker-devenv-sf4/docker/nginx/openssl.cnf

```
# /var/www/html/docker-devenv-sf4/docker/nginx/openssl.cnf

# OpenSSL example configuration file.
# This is mostly being used for generation of certificate requests.
#

# This definition stops the following lines choking if HOME isn't
# defined.
HOME			= .
RANDFILE		= $ENV::HOME/.rnd

# Extra OBJECT IDENTIFIER info:
#oid_file		= $ENV::HOME/.oid
oid_section		= new_oids

# To use this configuration file with the "-extfile" option of the
# "openssl x509" utility, name here the section containing the
# X.509v3 extensions to use:
# extensions		= 
# (Alternatively, use a configuration file that has only
# X.509v3 extensions in its main [= default] section.)

[ new_oids ]

# We can add new OIDs in here for use by 'ca', 'req' and 'ts'.
# Add a simple OID like this:
# testoid1=1.2.3.4
# Or use config file substitution like this:
# testoid2=${testoid1}.5.6

# Policies used by the TSA examples.
tsa_policy1 = 1.2.3.4.1
tsa_policy2 = 1.2.3.4.5.6
tsa_policy3 = 1.2.3.4.5.7

####################################################################
[ ca ]
default_ca	= CA_default		# The default ca section

####################################################################
[ CA_default ]

dir		= ./demoCA		# Where everything is kept
certs		= $dir/certs		# Where the issued certs are kept
crl_dir		= $dir/crl		# Where the issued crl are kept
database	= $dir/index.txt	# database index file.
#unique_subject	= no			# Set to 'no' to allow creation of
					# several certs with same subject.
new_certs_dir	= $dir/newcerts		# default place for new certs.

certificate	= $dir/cacert.pem 	# The CA certificate
serial		= $dir/serial 		# The current serial number
crlnumber	= $dir/crlnumber	# the current crl number
					# must be commented out to leave a V1 CRL
crl		= $dir/crl.pem 		# The current CRL
private_key	= $dir/private/cakey.pem# The private key
RANDFILE	= $dir/private/.rand	# private random number file

x509_extensions	= usr_cert		# The extensions to add to the cert

# Comment out the following two lines for the "traditional"
# (and highly broken) format.
name_opt 	= ca_default		# Subject Name options
cert_opt 	= ca_default		# Certificate field options

# Extension copying option: use with caution.
# copy_extensions = copy

# Extensions to add to a CRL. Note: Netscape communicator chokes on V2 CRLs
# so this is commented out by default to leave a V1 CRL.
# crlnumber must also be commented out to leave a V1 CRL.
# crl_extensions	= crl_ext

default_days	= 365			# how long to certify for
default_crl_days= 30			# how long before next CRL
default_md	= default		# use public key default MD
preserve	= no			# keep passed DN ordering

# A few difference way of specifying how similar the request should look
# For type CA, the listed attributes must be the same, and the optional
# and supplied fields are just that :-)
policy		= policy_match

# For the CA policy
[ policy_match ]
countryName		= match
stateOrProvinceName	= match
organizationName	= match
organizationalUnitName	= optional
commonName		= supplied
emailAddress		= optional

# For the 'anything' policy
# At this point in time, you must list all acceptable 'object'
# types.
[ policy_anything ]
countryName		= optional
stateOrProvinceName	= optional
localityName		= optional
organizationName	= optional
organizationalUnitName	= optional
commonName		= supplied
emailAddress		= optional

####################################################################
[ req ]
default_bits		= 2048
default_keyfile 	= privkey.pem
distinguished_name	= req_distinguished_name
attributes		= req_attributes
x509_extensions	= v3_ca	# The extensions to add to the self signed cert

# Passwords for private keys if not present they will be prompted for
# input_password = secret
# output_password = secret

# This sets a mask for permitted string types. There are several options. 
# default: PrintableString, T61String, BMPString.
# pkix	 : PrintableString, BMPString (PKIX recommendation before 2004)
# utf8only: only UTF8Strings (PKIX recommendation after 2004).
# nombstr : PrintableString, T61String (no BMPStrings or UTF8Strings).
# MASK:XXXX a literal mask value.
# WARNING: ancient versions of Netscape crash on BMPStrings or UTF8Strings.
string_mask = utf8only

# req_extensions = v3_req # The extensions to add to a certificate request

[ req_distinguished_name ]
countryName			= Country Name (2 letter code)
countryName_default		= AU
countryName_min			= 2
countryName_max			= 2

stateOrProvinceName		= State or Province Name (full name)
stateOrProvinceName_default	= Some-State

localityName			= Locality Name (eg, city)

0.organizationName		= Organization Name (eg, company)
0.organizationName_default	= Internet Widgits Pty Ltd

# we can do this but it is not needed normally :-)
#1.organizationName		= Second Organization Name (eg, company)
#1.organizationName_default	= World Wide Web Pty Ltd

organizationalUnitName		= Organizational Unit Name (eg, section)
#organizationalUnitName_default	=

commonName			= Common Name (e.g. server FQDN or YOUR name)
commonName_max			= 64

emailAddress			= Email Address
emailAddress_max		= 64

# SET-ex3			= SET extension number 3

[ req_attributes ]
challengePassword		= A challenge password
challengePassword_min		= 4
challengePassword_max		= 20

unstructuredName		= An optional company name

[ usr_cert ]

# These extensions are added when 'ca' signs a request.

# This goes against PKIX guidelines but some CAs do it and some software
# requires this to avoid interpreting an end user certificate as a CA.

basicConstraints=CA:FALSE

# Here are some examples of the usage of nsCertType. If it is omitted
# the certificate can be used for anything *except* object signing.

# This is OK for an SSL server.
# nsCertType			= server

# For an object signing certificate this would be used.
# nsCertType = objsign

# For normal client use this is typical
# nsCertType = client, email

# and for everything including object signing:
# nsCertType = client, email, objsign

# This is typical in keyUsage for a client certificate.
# keyUsage = nonRepudiation, digitalSignature, keyEncipherment

# This will be displayed in Netscape's comment listbox.
nsComment			= "OpenSSL Generated Certificate"

# PKIX recommendations harmless if included in all certificates.
subjectKeyIdentifier=hash
authorityKeyIdentifier=keyid,issuer

# This stuff is for subjectAltName and issuerAltname.
# Import the email address.
# subjectAltName=email:copy
# An alternative to produce certificates that aren't
# deprecated according to PKIX.
# subjectAltName=email:move

# Copy subject details
# issuerAltName=issuer:copy

#nsCaRevocationUrl		= http://www.domain.dom/ca-crl.pem
#nsBaseUrl
#nsRevocationUrl
#nsRenewalUrl
#nsCaPolicyUrl
#nsSslServerName

# This is required for TSA certificates.
# extendedKeyUsage = critical,timeStamping

[ v3_req ]

# Extensions to add to a certificate request

basicConstraints = CA:TRUE
keyUsage = nonRepudiation, digitalSignature, keyEncipherment

subjectAltName = @alt_names
[alt_names]
DNS.1 = NGINX_HOST

[ v3_ca ]


# Extensions for a typical CA


# PKIX recommendation.

subjectKeyIdentifier=hash

authorityKeyIdentifier=keyid:always,issuer

basicConstraints = critical,CA:true

# Key usage: this is typical for a CA certificate. However since it will
# prevent it being used as an test self-signed certificate it is best
# left out by default.
# keyUsage = cRLSign, keyCertSign

# Some might want this also
# nsCertType = sslCA, emailCA

# Include email address in subject alt name: another PKIX recommendation
# subjectAltName=email:copy
# Copy issuer details
# issuerAltName=issuer:copy

# DER hex encoding of an extension: beware experts only!
# obj=DER:02:03
# Where 'obj' is a standard or added object
# You can even override a supported extension:
# basicConstraints= critical, DER:30:03:01:01:FF

[ crl_ext ]

# CRL extensions.
# Only issuerAltName and authorityKeyIdentifier make any sense in a CRL.

# issuerAltName=issuer:copy
authorityKeyIdentifier=keyid:always

[ proxy_cert_ext ]
# These extensions should be added when creating a proxy certificate

# This goes against PKIX guidelines but some CAs do it and some software
# requires this to avoid interpreting an end user certificate as a CA.

basicConstraints=CA:FALSE

# Here are some examples of the usage of nsCertType. If it is omitted
# the certificate can be used for anything *except* object signing.

# This is OK for an SSL server.
# nsCertType			= server

# For an object signing certificate this would be used.
# nsCertType = objsign

# For normal client use this is typical
# nsCertType = client, email

# and for everything including object signing:
# nsCertType = client, email, objsign

# This is typical in keyUsage for a client certificate.
# keyUsage = nonRepudiation, digitalSignature, keyEncipherment

# This will be displayed in Netscape's comment listbox.
nsComment			= "OpenSSL Generated Certificate"

# PKIX recommendations harmless if included in all certificates.
subjectKeyIdentifier=hash
authorityKeyIdentifier=keyid,issuer

# This stuff is for subjectAltName and issuerAltname.
# Import the email address.
# subjectAltName=email:copy
# An alternative to produce certificates that aren't
# deprecated according to PKIX.
# subjectAltName=email:move

# Copy subject details
# issuerAltName=issuer:copy

#nsCaRevocationUrl		= http://www.domain.dom/ca-crl.pem
#nsBaseUrl
#nsRevocationUrl
#nsRenewalUrl
#nsCaPolicyUrl
#nsSslServerName

# This really needs to be in place for it to be a proxy certificate.
proxyCertInfo=critical,language:id-ppl-anyLanguage,pathlen:3,policy:foo

####################################################################
[ tsa ]

default_tsa = tsa_config1	# the default TSA section

[ tsa_config1 ]

# These are used by the TSA reply generation only.
dir		= ./demoCA		# TSA root directory
serial		= $dir/tsaserial	# The current serial number (mandatory)
crypto_device	= builtin		# OpenSSL engine to use for signing
signer_cert	= $dir/tsacert.pem 	# The TSA signing certificate
					# (optional)
certs		= $dir/cacert.pem	# Certificate chain to include in reply
					# (optional)
signer_key	= $dir/private/tsakey.pem # The TSA private key (optional)
signer_digest  = sha256			# Signing digest to use. (Optional)
default_policy	= tsa_policy1		# Policy if request did not specify it
					# (optional)
other_policies	= tsa_policy2, tsa_policy3	# acceptable policies (optional)
digests     = sha1, sha256, sha384, sha512  # Acceptable message digests (mandatory)
accuracy	= secs:1, millisecs:500, microsecs:100	# (optional)
clock_precision_digits  = 0	# number of digits after dot. (optional)
ordering		= yes	# Is ordering defined for timestamps?
				# (optional, default: no)
tsa_name		= yes	# Must the TSA name be included in the reply?
				# (optional, default: no)
ess_cert_id_chain	= no	# Must the ESS cert id chain be included?
				# (optional, default: no)
```

#### /var/www/html/docker-devenv-sf4/.env

```
# /var/www/html/docker-devenv-sf4/.env
# Nginx
NGINX_HOST=docker.sf4.local

# TIMEZONE
TZ=Europe/Madrid

# MySQL
MYSQL_ROOT_PASSWORD=root
MYSQL_DATABASE=sf4_db
MYSQL_USER=dockersf
MYSQL_PASSWORD=dockersf

# Xdebug
# get containers IP address: echo $(docker network inspect bridge | grep Gateway | grep -o -E '[0-9\.]+')
XDEBUG_REMOTE_HOST=172.17.0.1
```

Lo siguiente que haremos para poder ilustrarte y explicarte a nivel general los aspectos más importante para la definición de nuestro entorno, es crear dicho fichero "**docker-compose.yml**", para esto ejecutamos:

```
# Creamos el fichero docker-compose.yml
touch docker-compose.yml

# Editamos el fichero 
vim docker-compose.yml
```

Nuestro fichero final debe lucir de la siguiente forma:

```
#/var/www/html/docker-devenv-sf4/docker-compose.yml

version: '3'

services:
    app:
        container_name: sf4_app
        build:
            context: ./docker/app
            args:
                - timezone=${TZ}
        depends_on:
            - mysql
        volumes:
            - ./symfony42:/var/www/html:rw,cached
        env_file:
            - .env
        environment:
            XDEBUG_CONFIG: remote_host=${XDEBUG_REMOTE_HOST}
            PHP_IDE_CONFIG: serverName=${NGINX_HOST}

    nginx:
        container_name: sf4_nginx
        build:
            context: ./docker/nginx
            args:
                - nginx_host=${NGINX_HOST}
        ports:
            - "8008:80"
            - "443:443"
        depends_on:
            - app
        volumes:
            - ./symfony42/public:/var/www/html/public:ro
            - ./docker/nginx/default.template:/etc/nginx/conf.d/default.template
            - nginx_log:/var/log/nginx
        env_file:
            - .env
        command: /bin/bash -c "envsubst '$$NGINX_HOST'
            < /etc/nginx/conf.d/default.template
            > /etc/nginx/conf.d/default.conf
            && exec nginx -g 'daemon off;'"

    mysql:
        container_name: sf4_mysql
        build:
            context: ./docker/mysql
        volumes:
            - ./.data/mysql:/var/lib/mysql:rw
        env_file:
            - .env
        ports:
            - "3306:3306"

volumes:
    nginx_log:
```

Como puedes notar, es un simple fichero con una serie de parámetros que sirven para indicarle a docker que debe hacer, ejecutar y configurar para desplegar cada servicio en un contenedor.

Algunos parámetros hablan por sí solos, pero trataré de detallarte los más importantes.

- **build**: Le indica a docker que la base del contenedor debe ser generada partiendo de un **Dockerfile** o una imagen de repositorio.
    - **context**: Le indica a al build que debe tirar de un **Dockerfile** para la instalación del sistema operativo y cualquier otra instrucción que se haya definido en él.
    - **args**: Son argumentos que se pueden pasar al **Dockerfile** como parte de la configuración necesaria del contenedor.
- **ports**: Corresponde al mapping de puertos desde el cliente hacia el host, en nuestro caso por ejemplo para nginx, accedemos al servicio por el puerto 8008 hacia un servicio de nginx que corre en el puerto 80 dentro del contenedor.
- **depends\_on**: Crea dependencia entre contenedores, de tal forma que hasta que el contenedor indicado en este parámetro no este inicializado, el servicio que se esta definiendo con este parámetro, no se iniciará.
- **env\_file**: Su nombre lo indica, permite indicar que fichero de variables de entorno se va a utilizar para la configuración de cada contenedor de servicios.
- **volumes**: Permite mapear mediante enlaces simbólicos los directorios que deseemos. En nuestro caso por ejemplo para nginx, realizamos un enlace simbólico de nuestro directorio symfony42 para que los ficheros puedan ser accedidos por el contenedor como si en realidad estuviesen dentro de el. Aquí es donde esta la magia en la cual podemos programar desde fuera del contenedor y ejecutar desde el navegador nuestra aplicación siendo el contenedor quien posee el servidor web, mysql, entre otros.

## 2\. Generar el Build.

Para generar el build solo basta con ejecutar el siguiente comando:

```
# Ejecutar el build
docker-compose build
```

Con esto, **Docker**, leerá el fichero que acabamos de crear y comenzará a descargar las imágenes necesarias, a crear los contenedores e instalar todo lo necesario en base a lo que hayamos predefinido anteriormente.

Este proceso puede tomar ciertos minutos ya que irá creando según los contenedores y servicios definidos en el fichero "**docker-composer.yml**" y ejecutando las instrucciones definidas en cada Dockerfile para la instalación de sistema operativo, servicios, configuraciones entre otros.

Si todo marcha bien, podremos pasar al paso número 3.

## 3\. Añadir dominio al fichero "hosts"

Ya con todo preparado para el despliegue de nuestros contenedores, debemos añadir a nuestro fichero hosts un "mapping" de nuestro dominio a utilizar para el proyecto para que apunte a localhost.

Para esto debemos editar el fichero "/etc/hosts":

```
# Editamos nuestro fichero hosts
vim /etc/hosts
```

y añadir lo siguiente:

```
# Añadimos nuestro dominio a usar
127.0.0.1	docker.sf4.local
```

## 4\. Inicializar nuestros contenedores.

Lo siguiente es inicializar nuestros contenedores, y por ende, nuestros servicios. Para ello debemos ejecutar lo siguiente:

```
# Iniciar contenedores de servicios para el proyecto 
docker-compose up -d
```

El parámetro -d es para ejecutar los contenedores de forma desacoplada, es decir, que una vez que se ejecuten, libere el proceso en la terminal y queden funcionando en segundo plano.

Este comando siempre va a verificar que si hubo algún cambio en nuestro fichero "**docker-compose.yml**", realice el build antes de inicializar los respectivos contenedores.

También, es posible forzar hacer el build mediante el parámetro "**\--build**", por ejemplo:

```
# Iniciar contenedores de servicios para el proyecto 
docker-compose up -d --build
```

Si todo marcha bien veremos lo siguiente:

```
Creating network "docker-devenv-sf4_default" with the default driver
Creating volume "docker-devenv-sf4_nginx_log" with default driver
Creating sf4_mysql ... done
Creating sf4_app   ... done
Creating sf4_nginx ... done
```

## 5\. Probar nuestro entorno de desarrollo.

Para probar nuestro entorno, vamos a realizar una simple instalación de Symfony 4 y probar que todo vaya bien. Los pasos a seguir son:

1.- Lo primero que haremos es, situarnos dentro del directorio del proyecto (**/var/www/html/docker-devenv-sf4**)

```
# Entramos en el directorio del proyecto
cd /var/www/html/docker-devenv-sf4
```

2.- Instalaremos el "**Instalador de Symfony**" ejecutando lo siguiente:

```
# Instalador de Symfony
curl -sS https://get.symfony.com/cli/installer | bash
```

3.- Instalamos la versión web de **Symfony 4.2**:

```
# Instalamos la versión full
symfony new --full symfony42
```

4.- El paso anterior nos creará un nuevo directorio llamado "**symfomy42**" con toda la instalación de **Symfony**. Lo siguiente que haremos será instalar los paquetes que vienen definidos en el "**composer.json**" para esto:

```
# Instalar componentes de Symfony
# NOTA: Debes tener instalado composer, si aún no lo tienes revisa --> https://getcomposer.org/download/
composer update # o composer install
# Con el installer de symfony ya no es requerido hacer composer update o composer install al menos que desees verificar más adelante si hay actualizaciones de los componentes.
```

5.- Finalmente podemos ir a nuestro navegador para verificar que todo este en orden, para ello ingresamos en la barra de de direcciones:

```
http://docker.sf4.local:8008
```

Nos debe aparecer esto:

![](images/sf4-success-install-1.png)

Y de esta forma ya tenemos listo **nuestro entorno de desarrollo web local** con **Docker**. Puedes descargar todo el proyecto de ejemplo desde **[mi repositorio.](https://github.com/franciscougalde-com/docker-devenv-sf4)**

Recuerda que si tienes alguna sugerencia o pregunta, no dudes en dejar tus comentarios al final del post.  

Si te gustó este post, ayúdame a que pueda servirle a muchas más personas, compartiendo mis contenidos en tus redes sociales.

Espero que este post haya sido de gran ayuda para ti, y como siempre, cualquier inquietud o duda que tengas, puedes contactarme por cualquiera de las vías disponibles, o dejando tus comentarios al final de este post. También puedes sugerir que temas o post te gustaría leer a futuro.

* * *

Si te gustó el post, regálame tus aplausos!!!

\[wp-applause-button style="width:60px;height:60px;margin: 0 auto;" color="black"\]
