---
title: "Slim Framework: Una breve introducción"
date: "2019-08-27"
---

Slim en principio es un micro framework cuya función principal es ayudarte a escribir simples, pero poderosas aplicaciones y API's.

> En su núcleo, **Slim** es un despachador que recibe una petición **HTTP**, invoca un rutina **callback**, y devuelve una respuesta **HTTP**
> 
> Así de sencillo!

## Cuando usar Slim?

**Slim** **Framework** es una herramienta ideal para crear **API**´**s** que **consuman**, **reutilicen** o **publiquen** datos.

También es recomendada cuándo requieras hacer un prototyping de una aplicación en poco tiempo, es decir, cuando necesites poner en marcha un proyecto lo antes posible.

Es que, incluso puedes crear aplicaciones web que incluyan interfaces de usuario. Sinceramente este framework es potente y lo interesante de todo es que es rápido y muy ligero de código.

Otro punto a favor es que su curva de aprendizaje es muy corta por lo que de seguro te podrás adentrar en él en pocos días, e incluso hasta en una misma tarde.

## Cómo funciona?

Lo primero que necesitamos es un servidor web como **Nginx** o **Apache**. Para ello, te sugiero leer el siguiente **[documento](http://www.slimframework.com/docs/v4/start/web-servers.html)**.

Debes configurar tu servidor web de tal forma que envíe todas las peticiones apropiadas a un archivo **PHP** que actúa de "**controlador frontal**" ó "**front-controller**".

Desde este fichero **PHP** es que iniciamos y ejecutamos nuestra app con **Slim Framework**

Una aplicación **Slim** contiene rutas que responden a una petición **HTTP** específica. Cada ruta invoca un "**callback**" que no es más que un método que se ejecuta y retorna una respuesta **HTTP**.

Para comenzar, lo primero que hay que hacer es instanciar y configurar la aplicación **Slim**. Luego, definir las rutas de la aplicación y finalmente ejecutar la aplicación **Slim**.

Un ejemplo de como quedaría nuestro controlador frontal sería:

```
<?php
use Psr\Http\Message\ResponseInterface as Response;
use Psr\Http\Message\ServerRequestInterface as Request;
use Slim\Factory\AppFactory;

require __DIR__ . '/../vendor/autoload.php';

/**
 * Instantiate App
 *
 * In order for the factory to work you need to ensure you have installed
 * a supported PSR-7 implementation of your choice e.g.: Slim PSR-7 and a supported
 * ServerRequest creator (included with Slim PSR-7)
 */
$app = AppFactory::create();

// Add Routing Middleware
$app->addRoutingMiddleware();

/*
 * Add Error Handling Middleware
 *
 * @param bool $displayErrorDetails -> Should be set to false in production
 * @param bool $logErrors -> Parameter is passed to the default ErrorHandler
 * @param bool $logErrorDetails -> Display error details in error log
 * which can be replaced by a callable of your choice.
 
 * Note: This middleware should be added last. It will not handle any exceptions/errors
 * for middleware added after it.
 */
$errorMiddleware = $app->addErrorMiddleware(true, true, true);

// Define app routes
$app->get('/hello/{name}', function (Request $request, Response $response, $args) {
    $name = $args['name'];
    $response->getBody()->write("Hello, $name");
    return $response;
});

// Run app
$app->run();

```

## Request y Response

Cuando creamos una aplicación **Slim**, a menudo estaremos trabajando directamente con los objetos **Request** y **Response**. Estos objetos representan la solicitud HTTP real recibida por el servidor web y la eventual respuesta HTTP devuelta al cliente.

Cada ruta de la aplicación **Slim** recibe los objetos de **Request** y **Response** actuales como argumentos del **callback**. Es decir, nuestro método invocado cuando se recibe un request, tendrá como argumentos el **Request** y **Response** de la petición actual.

Estos objetos implementan las populares interfaces **PSR-7**. La ruta de la aplicación **Slim** puede inspeccionar o manipular estos objetos según sea necesario. En definitiva, cada ruta de aplicación Slim **DEBE** devolver un objeto de respuesta PSR-7.

Muy bien, y como empiezo a usar **Slim framework**?

## Instalación de Slim Framework

### Requerimientos del Sistema

- Servidor Web con reescritura de URL
- PHP 7.1 ó superior

### Paso 1: Instalar Composer

No tienes instalado **Composer**? Es sencillo de instalar, puedes ir a **[pagina de descarga](https://getcomposer.org/download/)** de su web y seguir las instrucciones de instalación.

## Paso 2: Instalar Slim Framework

Se recomienda instalar **Slim Framework** usando **[Composer](https://getcomposer.org/download/)**.

Sitúate en el directorio raíz del proyecto y ejecuta el siguiente comando:

```
composer require slim/slim:4.0.0
```

Este comando descarga Slim Framework y todas sus dependencias de terceros en la carpeta **vendor** de tu directorio raíz del proyecto.

## Paso 3: Instalar una implementación de PSR-7 y ServerRequest Creator

Antes de ponerte en marcha con **Slim Framework** necesitas elegir una implementación PSR-7 que se adapte mejor a los requerimientos y necesidades de tu aplicación.

Para que la detección automática funcione y te permita usar **AppFactory::create(...)** y **App::run(...)** sin tener que crear manualmente un **ServerRequest**, es necesario instalar una de las siguientes implementaciones:

#### [Slim PSR-7](https://github.com/slimphp/Slim-Psr7)

```
composer require slim/psr7
```

#### [Nyholm PSR-7](https://github.com/Nyholm/psr7) y [Nyholm PSR-7 Server](https://github.com/Nyholm/psr7-server)

```
composer require nyholm/psr7 nyholm/psr7-server
```

#### [Guzzle PSR-7](https://github.com/guzzle/psr7) y [Guzzle HTTP Factory](https://github.com/http-interop/http-factory-guzzle)

```
composer require guzzlehttp/psr7 http-interop/http-factory-guzzle
```

#### [Zend Diactoros](https://github.com/zendframework/zend-diactoros)

```
composer require zendframework/zend-diactoros
```

### Paso 4: ¡Hello World!

```
<?php
use Psr\Http\Message\ResponseInterface as Response;
use Psr\Http\Message\ServerRequestInterface as Request;
use Slim\Factory\AppFactory;

require __DIR__ . '/../vendor/autoload.php';

$app = AppFactory::create();

$app->get('/', function (Request $request, Response $response, $args) {
    $response->getBody()->write("Hello world!");
    return $response;
});

$app->run();
```

Y con esto, ya tienes todo lo necesario para empezar a construir aplicaciones usando **Slim Framework**.

Recuerda que si tienes alguna sugerencia o pregunta, no dudes en dejar tus comentarios al final del post.

Si te gustó este post, ayúdame a que pueda servirle a muchas más personas, compartiendo mis contenidos en tus redes sociales.

Espero que este post haya sido de gran ayuda para ti, y como siempre, cualquier inquietud o duda que tengas, puedes contactarme por cualquiera de las vías disponibles, o dejando tus comentarios al final de este post. También puedes sugerir que temas o post te gustaría leer a futuro.

* * *

Si te gustó el post, regálame tus aplausos!!!

\[wp-applause-button style="width:60px;height:60px;margin: 0 auto;" color="black"\]
