---
layout: post
title: "Conoce lo nuevo de Symfony 4, ¡te vas a enamorar!"
author: fjugaldev
categories: [ Desarrollo Web ]
tags: [Frameworks PHP, PHP, Symfony, Symfony 4]
image: assets/images/posts/symfony-4-a.png
description: "Conoce lo nuevo que traje consigo esta nueva versión de Symtony, Symfony 4 y sus nuevas herramientas, nuevas maneras de hacer las cosas y mucho mas"
featured: false
hidden: false
---

Luego de dos arduos años de desarrollo, trabajo duro y miles de commits y pull requests por parte de ciento de programadores/ras de toda la comunidad, se publicó hace unos meses la mejor versión de este maravilloso framework, Symfony 4.

Con la llegada de esta nueva versión, se pudo materializar las grandes ideas que Fabien Potencier, líder del framework, tenía para esta nueva versión. Para ello decidió cambiar las reglas del juego y promovió a todo el equipo de desarrolladores de la comunidad para que esta nueva versión sufriera grandes e importantes cambios en la forma de hacer las cosas.

## Todo es diferente, todo es mejor

El lanzamiento de Symfony 3 supuso una mejora significativa sobre Symfony 2, pero incomparable respecto al cambio disruptivo que supone esta nueva versión.

> Hemos repensado las ideas y fundamentos básicos del framework, mejorando o eliminando todas ellas para hacer un framework más moderno y adaptado a las nuevas necesidades del mercado.
> 
> Javier Eguiluz, [Symfony.es](http://www.symfony.es)

Hasta ahora todos los conceptos que conocemos sobre Symfony (controladores, servicios, eventos, entre otros) se mantienten, pero la forma de implementarlos cambia radicalmente, y afortunadamente para mejor ya que se han removido muchas "ideas propias de Symfony" para dar paso a ideas estándares y globales en la industria actual del software.

El resultado de esto ha sido sin duda, dejar atrás errores cometidos en el pasado para darle paso a nuevas y buenas prácticas, de hecho, Symfony 4 promueve la premisa de aprender menos cosas, configurar menos cosas, tocar menos cosas, programar menos cosas, reinventar menos cosas y por supuesto, dedicar menos tiempo al framework para así dedicarnos a lo que realmente nos importa, nuestros proyectos.

## Principales novedades

## Symfony Flex: ¡Automatización!

Symfony Flex es la gran novedad en la nueva versión del framework. Esta herramienta permitirá instalar y desinstalar dependencias automáticamente a través de un sistema de recetas controlado por el equipo de Symfony.

Esto rompe sin duda con la forma en que estábamos acostumbrados a  instalar y configurar los bundles, ya que no es necesario registrar los bundles en nuestro AppKernel.php  e incorporar la respectiva configuración en el archivo config.yml . Cuando se instale un Bundle, Symfony 4 lo configurará automáticamente y se adaptará a las posibles dependencias que ya tengas instaladas.

Con esto se busca que las dependencias de nuestra aplicación deberán COMPONER en vez de HEREDAR.

> La automatización no termina aqui. Gracias al [autowiring de servicios](https://symfony.com/doc/current/service_container/3.3-di-changes.html), puedes utilizar los servicios en tu aplicación sin tener que configurarlos. Symfony crea automáticamente los servicios por ti y también añade las etiquetas para los servicios que lo requieran. Pero no te preocupes, esto no es magia, es solo automatización. Si un servicio no puede configurarse de forma inequívoca, Symfony te avisará con un mensaje de error muy claro.
> 
> Javier Eguiluz - [Symfony.es](http://www.symfony.es)

## Aplicaciones Desacopladas: ¡Fuera Bundles!

Esta nueva versión permite generar aplicaciones sin bundles. Anteriormente se estilaba (práctica heredadas desde symfony 1), crear un bundle por cada módulo o función del sistema, luego se hizo buena práctica, que si la aplicación no iba a ser destinada a un bundle desacoplado (vendor), lo mejor era usar un solo bundle, en comúnmente conocido  AppBundle.

Pero ahora esto cambió, no es necesario que el código de la aplicación dependa de un bundle y el código estará ubicado directamente en el directorio src/ .Este cambio permite que el código esté desacoplado del framework, uno de los conceptos que de cierta manera han permanecido desde al menos Symfony 2 ya que su función es mantener separada la lógica del negocio con el resto de las capas. 

Esto beneficia arduamente a Symfony ya que es una realidad que tanto otros frameworks como aplicaciones de terceros, utilicen componentes del framework dentro de su código. Ejemplo Laravel Framework y Drupal 8. 

## Variables de entorno

En esta versión desaparece por completo el archivo parameters.yml  y en su lugar se emplearán variables de entorno. Se trata de una tendencia que están tomando la mayoría de frameworks y se recomienda como buena práctica en las mayoría de plataformas de despliegue en la nube.

El uso de variables de entorno además de ser un estándar permite la configuración del entorno de ejecución de una manera más cómoda. Éstas se pueden leer desde calquier software, además de ser independiente del código fuente, framework y lenguaje. Para el desarrollo en Symfony proponen utilizar el componente [DotEnv](https://symfony.com/doc/master/components/dotenv.html) que viene nativo en Symfony 4. El cambio entre un archivo .env  y las variables de entorno “de verdad” será automático y transparente.

## Estructura de directorios sencilla

Symfony 3 persiguió el objetivo de hacer que la estructura de un proyecto de Symfony se pareciera a la de UNIX. Para esta nueva versión se incluyó el directorio /var. En esta nueva versión se sigue en esta misma dirección por lo que se harán cambios como:

- Se renonbró el directorio app/   por /etc   (directorio de unix donde se almacenan los archivos de configuración).
- Los tests se ubican en el directorio tests/ 
- Las plantillas se almacenarán en templates/   de tal manera que src/   está reservado exclusivamente a clases PHP.

Al crear un nuevo proyecto, estos son los directorios que verás:

```text
bin/
    console
config/
public/
    index.php
src/
    Kernel.php
tests/
var/
    cache/
    log/
vendor/
```

Y cuando la aplicación crezca y añadas assets o plantillas o archivos de traducción, esta será la estructura de directorios:

```text
assets/
bin/
    console
config/
public/
    index.php
src/
    Kernel.php
templates/
tests/
translations/
var/
    cache/
    log/
vendor/
```

## Aplicaciones mucho más pequeñas

Symfony 4 no se basa desde la edición estándar de Symfony. Al crear una nueva aplicación, no se instalarán todos los componentes y bundles que estábamos acostumbrados a ver al inicio de nuestros proyectos, solamente se instalan los componentes mínimos para poder desarrollar una aplicación (HttpKernel, HttpFoundation, Routing, etc.), lo cual supone de que debes ir agregando aquellos componentes según los vayas necesitando en base a los requerimientos de tu aplicación.

La consecuencia es que las aplicaciones son mucho más pequeñas. Una aplicación Symfony 4 nueva contiene un 70% menos de código y de archivos que una aplicación nueva de Symfony 3.

Además, las aplicaciones utilizan un micro-kernel y ya no están optimizadas exclusivamente para hacer aplicaciones web tradicionales. Symfony 4 es adecuado para hacer microservicios, aplicaciones web monolíticas, aplicaciones de consola, backends para aplicaciones JavaScript, etc.

## #Homework: Demo de Symfony Flex

Como Tarea para la casa de este post, dejo el siguiente vídeo publicado por [SensioLabs](https://www.youtube.com/channel/UCLdVmxwj9dQqM8tJJp2LYGw) en el que se muestra una demo de como utilizar Symfony Flex. Esta es la carta bajo de la manga que trae consigo el framework para esta nueva versión y que estoy seguro de que marcará la pauta y camino a seguir para otros frameworks. 

<iframe width="1276" height="712" src="https://www.youtube.com/embed/o9N1nOYfAl4" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

Si te gustó este post, ayúdame a que pueda servirle a muchas más personas, compartiendo mis contenidos en tus redes sociales.

Espero que este post haya sido de gran ayuda para ti, y como siempre, cualquier inquietud o duda que tengas, puedes contactarme por cualquiera de las vías disponibles, o dejando tus comentarios al final de este post. También puedes sugerir que temas o post te gustaría leer a futuro.

* * *
