---
title: "Symfony: ¿Cómo usar el Componente HttpClient?"
date: "2019-08-07"
---

## El Componente HttpClient de Symfony será el reemplazo de Guzzle?

> El componente **[HttpClient](https://symfony.com/doc/current/components/http_client.html)** de **Symfony** fue introducido en la versión **4.3** y aún esta considerado un feature experimental
> 
> Nota a considerar antes de comenzar a usarlo en producción.

Una necesidad casi infalible en cada aplicación que desarrollamos, es el uso de una librería que nos provea un **Cliente** **HTTP**, que nos permita conectarnos al **API** de un tercero o en algunos casos para consumir nuestros propios **API**.

Así pues que no es de sorprenderse cuando se trata de nuevos componentes de **Symfony** ya que la visión del framework es siempre de proveernos de herramientas útiles las cuales nos permitan desarrollar nuestras aplicaciones de forma rápida, segura y sobre todo, escalable.

Estoy seguro de que este componente será el reemplazo de **Guzzle** no solo en proyectos hechos con **Symfony** si no en cualquier otro framework puesto que está pensado para trabajar desacoplado independientemente de donde se esté utilizando.

## Instalación del Componente HttpClient de Symfony

> Se requiere tener instalado [Composer](https://getcomposer.org/)
> 
> Requerimientos de instalación

Para instalar el componente debemos ejecutar el siguiente comando:

```
composer require symfony/http-client
```

## Funcionalidades del Componente

A pesar de haber sido lanzado hace muy poco, este componente [**HttpClient**](https://symfony.com/doc/current/components/http_client.html) de **Symfony** ya incorpora una numerosa cantidad de funcionalidades que desde ya podrás disponer de ellas.

- Soporta para [autenticación](https://symfony.com/doc/master/components/http_client.html#authentication), tanto básica como basada en "bearer tokens".
- Permite pasar [parámetros en la query string](https://symfony.com/doc/master/components/http_client.html#query-string-parameters) y también [cabeceras HTTP personalizadas](https://symfony.com/doc/master/components/http_client.html#headers).
- Posibilidad de [enviar datos](https://symfony.com/doc/master/components/http_client.html#uploading-data) mediante cadenas de texto, _closures_ y recursos de PHP.
- [Streaming de respuestas](https://symfony.com/doc/master/components/http_client.html#streaming-responses) lo cual nos permite ir recibiendo trozos de la respuesta en vez de esperar a que se complete la petición para recibirla completa.
- [Cacheado de peticiones y respuestas](https://symfony.com/doc/master/components/http_client.html#caching-requests-and-responses).
- [Auto-configuración del cliente HTTP](https://symfony.com/doc/master/components/http_client.html#scoping-client) en función de la URL de la petición.
- [Compatibilidad con PSR-7 y PSR-18](https://symfony.com/doc/master/components/http_client.html#psr-7-and-psr-18-compatibility).
- Test sencillos gracias a un [cliente HTTP especial para tests](https://symfony.com/doc/master/reference/configuration/framework.html#http-client)

## Uso básico del componente

La clase **Symfony\\Component\\HttpClient\\HttpClient** es la base para iniciar nuestras peticiones **HTTP**. Por lo cual podemos hacer uso de ella de la siguiente manera:

```
use Symfony\Component\HttpClient\HttpClient;

$httpClient = HttpClient::create();
$response = $httpClient->request('GET', 'https://api.github.com/repos/symfony/symfony-docs');
```

Una de las diferencias más importantes respecto a otros clientes HTTP es que el método** `request()`** es no bloqueante.

Esto quiere decir que, el objeto `$**response**` está disponible inmediatamente después de hacer la petición y el programa puede continuar su ejecución sin esperar una respuesta.

Cuando llames al método `**getStatusCode()**`, la ejecución de la aplicación se detiene hasta que las cabeceras **HTTP** estén disponibles. Y si llamas al método `**getContent()**`, se para hasta recibir todos los contenidos de la respuesta (aunque también puedes usar [streaming](https://symfony.com/doc/master/components/http_client.html#streaming-responses) en las respuestas):

```
$statusCode = $response->getStatusCode();
// $statusCode = 200

$contentType = $response->getHeaders()['content-type'][0];
// $contentType = 'application/json'

$content = $response->getContent();
// $content = '{"id":521583, "name":"symfony-docs", ...}'

$content = $response->toArray();
// $content = ['id' => 521583, 'name' => 'symfony-docs', ...]
```

Gracias a este comportamiento no bloqueante, es posible llamar varias veces al método `**request()**` para hacer peticiones asíncronas y acceder a las respuestas solo cuando se hayan realizado todas las peticiones.

Por defecto este componente utiliza las funciones nativas de PHP para realizar las peticiones, así que no tienes que instalar ninguna dependencia. No obstante, si en tu máquina has instalado/activado la [librería cURL](https://curl.haxx.se/) y la [extensión cURL de PHP](https://php.net/curl), entonces usará cURL automáticamente.

Si el código de estado **HTTP** de la respuesta no está en el rango **200**\-**299** (es decir, que es `3xx`, `4xx` o `5xx`) tu aplicación deberar manejar el error, de lo contrario cuando ejecutes los métodos `**getHeaders()**` y `**getContent()**` verás una excepción:

// Esta petición devuelve un código de estado 403 en su respuesta.
$response = $httpClient->request('GET', 'https://httpbin.org/status/403');
 
// Si no compruebas el código de estado de la respuesta, verás una excepción de tipo
// Symfony\\Component\\HttpClient\\Exception\\ClientException 

$content = $response->getContent();
 
// Lo que debemos hacer es validad explícitamente el codigo de respuesta
// (en este caso, no se lanza ninguna excepción)
if (200 !== $response->getStatusCode()) {

    // handle the HTTP request error (e.g. retry the request)

} else {
    $content = $response->getContent();
}

## Integración con Symfony

Si deseas utilizar el componente **HttpClient** de **Symfony** dentro del **Framework**, en lugar de usarlo como un componente desacoplado, puedes configurar la opción **http\_client** en el fichero **config/packages/framework.yaml**:

```
# config/packages/framework.yaml
framework:
    # ...
    http_client:
        max_redirects: 7
        max_host_connections: 10
```

> (echa un vistazo también a [todas](https://symfony.com/doc/master/components/http_client.html#testing-http-clients-and-responses) [las](https://symfony.com/doc/master/components/http_client.html#configuration) [opciones de configuración de HttpClient](https://symfony.com/doc/master/components/http_client.html#testing-http-clients-and-responses):
> 
> Nota de referencia

Y ya con esto podrías hacer uso del **Autowiring** para inyectar el cliente **[HttpClient](https://symfony.com/doc/current/components/http_client.html)** de **Symfony** en tus servicios o clases personalizadas:

use Symfony\\Contracts\\HttpClient\\HttpClientInterface;

class SomeService
{
    private $httpClient;

    public function \_\_construct(HttpClientInterface $httpClient)
    {
        $this->httpClient = $httpClient;
    }

    public function getRequest()
    {
        $response = $httpClient->request('GET', 'https://httpbin.org/status/403');

        if (200 !== $response->getStatusCode()) {

            // handle the HTTP request error (e.g. retry the request)

        } else {
            $content = $response->getContent();
        }

    }
}

Es todo por ahora con todo lo relacionado con este maravilloso componente **HttpClient** de **Symfony**. Estaré publicando actualizaciones a medida que el componente vaya incorporando nuevas funcionalidades.

Recuerda que si tienes alguna sugerencia o pregunta, no dudes en dejar tus comentarios al final del post.

Si te gustó este post, ayúdame a que pueda servirle a muchas más personas, compartiendo mis contenidos en tus redes sociales.

Espero que este post haya sido de gran ayuda para ti, y como siempre, cualquier inquietud o duda que tengas, puedes contactarme por cualquiera de las vías disponibles, o dejando tus comentarios al final de este post. También puedes sugerir que temas o post te gustaría leer a futuro.

* * *

Si te gustó el post, regálame tus aplausos!!!

\[wp-applause-button style="width:60px;height:60px;margin: 0 auto;" color="black"\]
