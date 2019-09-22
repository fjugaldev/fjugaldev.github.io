---
title: "Docker: crear entorno de desarrollo web local - Parte I"
date: "2019-02-16"
---

En esta serie de posts que iré publicado, quiero enseñarte todo lo necesario, lo básico y quizás un poco de lo avanzado, para crear tu entorno de desarrollo web local utilizando esta tecnología de contenedores que en la actualidad esta marcando tendencias a la hora de desarrollar aplicaciones.

> Docker es una plataforma para desarrolladores y sysadmins (o lo que ahora llamamos **DevOps**) que permite **desarrollar**, **desplegar** y **ejecutar** aplicaciones con contenedores.

Lo interesante radica principalmente en su flexibilidad a la hora de definir los requerimientos de sistema operativo, tecnologías, servicios y a su vez lo versátil que es para estandarizar nuestros entornos de desarrollo.

Docker basa su funcionamiento en la creación, despliegue y ejecución de contenedores. Seguramente este término no te resultará del todo nuevo, ya que este proviene del sistema operativo Linux.

Pero ahora bien, lo que quiero lograr con este post es expresarles lo que yo entiendo al respecto de una manera un poco más sencilla y no tan técnica. Y a su vez, mostrarles un poco, mediante un ejemplo, como **crear tu entorno de desarrollo web local** utilizando **Docker.**

Pues no perdamos más el tiempo y manos a la obra.

#### Requerimientos necesarios:

- Sistema operativo preferiblemente **macOS** ó **Línux**. Esta guía podría funcionar perfectamente en **Windows** pese a que no he podido comprobarlo.
- Instalador de **Docker**
- Muchísimo ánimo por aprender ?

> Para no extender el post, no detallaré los pasos de instalación, pero que te aseguro no tienen gran nivel de dificultad, basta con descargar el instalador y seguir los pasos tanto en macOS como en Windows. En linux, según la distribución que utilices, deberás ejecutar el gestor de paquetes correspondiente desde la terminal para instalar docker.

Para nuestro entorno de desarrollo vamos a configurar docker para generar 3 contenedores con NGINX, PHP 7.2 y MySQL Server 5.7 respectivamente

Pero antes de ponernos manos a la obra, quiero explicarte de forma sencilla como funciona **Docker** y la opción que a mi particularmente me parece más cómoda a la hora de desplegar un entorno de desarrollo con **Docker**.

## ¿Cómo funciona Docker?

**Docker**, entre una de las distintas formas que provee, permite crear contenedores mediante la definición de servicios en un fichero **Yaml** comúnmente llamado "**docker-compose.yml**"

Este fichero contiene la definición de cada servicio que deseamos crear. Para que se entienda mejor, un servicio es un conjunto de definiciones vía parámetros que permiten la creación de un contenedor.

Un contenedor se pudiese interpretar como una caja la cual alberga un sistema operativo empotrado con los servicios respectivos ejecutándose y expuestos para ser consumidos por nosotros o por otro contenedor en si.

Visto de otra manera, en nuestro caso práctico definiremos servicios para crear tres contenedores, cada contenedor tendrá un sistema operativo ejecutándose y con los servicios de NGINX, PHP 7.2 y MySQL en cada contenedor.

Pero, pudiésemos tener en caso de ser necesario la cantidad de contenedores que requerimos, por ejemplo un contenedor con el servidor de elastic search, otro con el servidor de cache Redis, y así sucesivamente. O simplemente empotrar varios de estos servicios en un mismo contenedor.

Un punto importante a destacar, es que, la comunidad de **Docker** nos provee de imágenes, que no son más que repositorios con ciertas configuraciones congeladas las cuales podemos reutilizar para la creación de nuestro entorno de desarrollo en **Docker**.

Es decir, **Docker** nos permite tirar de repositorios para la creación de nuestros contenedores, y así ahorrarnos trabajo, o en su defecto, podemos definir los famosos "**.Dockerfile**" que de seguro también habrás escuchado hablar.

Estos ficheros no son más que ficheros de ejecución que permiten a su vez tirar de imágenes oficiales bien sea de sistemas operativos o de imágenes que contienen sistema operativo y servicios como PHP instalado.

La ventaja de estos **Dockerfile** es que nos permiten adaptar nuestros contenedores mas hacia nuestras necesidades reales.

Por ejemplo yo pudiese crear un **Dockerfile** que tire de un repositorio oficial que contiene Ubuntu + PHP 7.2 y adicionalmente, mediante este fichero, darle instrucciones para que instale ciertos módulos adicionales que necesite para PHP en concreto y que no vienen por defecto.

Otro punto importante a conocer son algunos comandos útiles que debes tener a la mano para cuando desees monitorizar, gestionar o acceder a los contenedores, imágenes, entre otros.

## Comandos útiles para Docker

```
# Listar contenedores en ejecución
docker ps
# o también
docker container ls

# Eliminar contenedor por ID
docker container rm CONTAINER_ID 

# Listar todos los contenedores
docker ps -a

# Verificar recursos consumidos por los contenedores
docker stats

# Listar todas las imágenes
docker image ls

# Eliminar imágen por ID
docker image rm IMAGE_ID 

# Iniciar contenedores
docker-compose up -d 

# Detener contenedores
docker-compose stop

# Detener y borrar contenedores, imagenes, networks, volumes.
docker-compose down

# Entrar a un contenedor 
docker exec -it CONTAINER_ID_OR_CONTAINER_NAME bash
```

En la [Parte II](https://www.franciscougalde.com/2019/02/22/docker-entorno-de-desarrollo-web-local-parte-ii/) de esta serie de posts, te mostraré finalmente como configurar tu fichero docker-compose.yml para crear tu entorno de desarrollo web local.

Si te gustó este post, ayúdame a que pueda servirle a muchas más personas, compartiendo mis contenidos en tus redes sociales.

Espero que este post haya sido de gran ayuda para ti, y como siempre, cualquier inquietud o duda que tengas, puedes contactarme por cualquiera de las vías disponibles, o dejando tus comentarios al final de este post. También puedes sugerir que temas o post te gustaría leer a futuro.

* * *

Si te gustó el post, regálame tus aplausos!!!

\[wp-applause-button style="width:60px;height:60px;margin: 0 auto;" color="black"\]
