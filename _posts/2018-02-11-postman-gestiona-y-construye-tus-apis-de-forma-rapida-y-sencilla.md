---
layout: post
title:  "Postman: gestiona y construye tus API’s de forma rápida y sencilla"
author: fjugaldev
categories: [ Desarrollo Web ]
tags: [API, Herramientas, REST, RESTFful]
image: assets/images/posts/postman-featured.jpg
description: "Conoce y aprende sobre esta poderosa herramienta para gestión y construcción de API´s. Postman, una herramienta que amaras y se convertirá en tu mejor"
featured: false
hidden: false
---

En este post quiero mostrarte una increíble herramienta, se llama: **Postman**, que si no la conocías, hará que tus días de trabajo para la construcción de **API's**, se conviertan en algo apasionante y adictivo. Creéme , vas a amar esta herramienta.

## ¿Qué es Postman?

Postman es una poderosa herramienta que surgió inicialmente como una extensión para el navegador **Google Chrome**. Hoy en día, se ofrece a millones de programadores como una aplicación nativa para macOS, Linux y Windows.

Por fortuna, Postman **es gratis** hasta cierto punto, pero lo suficiente como para hacernos la vida más fácil.

Entre algunas de sus funcionalidades, permite realizar tareas diferentes dentro del mundo **API REST**: creación de peticiones a APIs internas o de terceros, elaboración de tests para validar el comportamiento de APIs, posibilidad de crear entornos de trabajo diferentes (con variables globales y locales), y todo ello con la posibilidad de ser compartido con otros compañeros del equipo de manera gratuita mediante la exportación de toda esta información a través de un archivo en formato JSON el cual puede ser importado en cualquier momento.

Adicional a esto, en su versión de pago, dispone una modalidad colaborativa para que equipos de trabajo puedan trabajar en conjunto en el desarrollo de API's, 100% sincronizadas en la nube para una integración y escalabilidad más eficiente y a su vez ágil.

Listando sus funcionalidades mas importantes tenemos:

- Una interfaz intuitiva para enviar peticiones, guardar respuestas, agregar test y crear flujos de trabajo.
- Historial de las peticiones enviadas
- Creación de Variables
- Creación de Entornos (Environtments)
- Creación de Collections y descripción de peticiones.

## Colecciones de APIs

El interés principal de **Postman** es que utilicemos la herramienta para preparar peticiones a **API's**, pero basado en esto, permitir generar colecciones de peticiones que permitan por un lado ordenar los recursos y servicios, y por otro lado, probar cada petición de una forma más rápida y sencilla.

Las colecciones no son más que carpetas donde se almacenan las peticiones y que permiten ser estructuradas por recursos, módulos o como el equipo lo desee. En la siguiente imagen vemos una estructura de una colección de ejemplo.

![Colecciones de Postman](/assets/images/posts/Screen-Shot-2018-02-08-at-00.07.07.png)

Como se puede apreciar, se puede definir el método de la petición (GET, POST, etc.), tokens de autenticación, cabeceras asociadas a la petición, cuerpo de la petición, entre otras cosas, y todo de una manera muy sencilla.

Todas las llamadas almacenadas en nuestra colección pueden ser exportadas a múltiples lenguajes haciendo clic en el apartado Code que puedes ver en la parte superior derecha de la captura anterior.

Es decir, **Postman** generará un snippet de código con la configuración establecida para la petición en el lenguaje que elijas entre aquellos que **Postman** soporta. Por ejemplo:

```php
<?php

$curl = curl\_init();

curl\_setopt\_array($curl, array(
CURLOPT\_URL => "https://echo.getpostman.com/post",
CURLOPT\_RETURNTRANSFER => true,
CURLOPT\_ENCODING => "",
CURLOPT\_MAXREDIRS => 10,
CURLOPT\_TIMEOUT => 30,
CURLOPT\_HTTP\_VERSION => CURL\_HTTP\_VERSION\_1\_1,
CURLOPT\_CUSTOMREQUEST => "POST",
CURLOPT\_POSTFIELDS => "Duis posuere augue vel cursus pharetra. In luctus a ex nec pretium. Praesent neque quam, tincidunt nec leo eget, rutrum vehicula magna.\\nMaecenas consequat elementum elit, id semper sem tristique et. Integer pulvinar enim quis consectetur interdum volutpat.",
CURLOPT\_HTTPHEADER => array(
"Cache-Control: no-cache",
"Content-Type: text/plain",
"Postman-Token: b45bbf66-076e-d3e4-d75e-be55f6bc706a"
),
));

$response = curl\_exec($curl);
$err = curl\_error($curl);

curl\_close($curl);

if ($err) {
echo "cURL Error #:" . $err;
} else {
echo $response;
}
```

Este snippet de código lo puedes copiar y pegar en un archivo y ejecutarlo según sea el lenguaje al que hayas exportado. En este caso en un archivo php.

## Entornos de trabajo

Particularmente, uno de los puntos mas fuertes de Postman, es la posibilidad de configurar tu **entorno de trabajo**, en donde puedes definir variables, clasificar dichas variables según entornos de desarrollo establecidos. Esto entre otras cosas es muy útil, por un lado cuando se tienen muchos proyectos o necesitamos definir diferentes escenarios para un mismo proyecto, por ejemplo, diferentes cabeceras, URLs de cada uno de los entornos de trabajo, entre otros.

Por otro lado, nos brinda la posibilidad de ahorrarnos tiempo a la hora de probar nuestros servicios, ya que a veces perdemos tiempo configurando peticiones con parámetros y variables que muchos de ellos son constantes y se repiten en varios servicios de nuestro **API**.

Para configurar los entornos y sus variables, verás que en la esquina superior derecha de la interfaz hay un icono de engranaje que al hacer click sobre este, se despliegan opciones, entre ellas, una opción llamada "Manage environments" donde se pueden configurar cada uno de nuestros entornos requeridos.

![Enviroments Postman](/assets/images/posts/Screen-Shot-2018-02-08-at-00.44.35.png)

Una vez configurados los entornos y dentro de ellos, cada variable en particular con su respectivo valor, su uso en cualquier llamada y en cualquier colección es muy simple. Si por ejemplo definimos una variable “path” para indicar la url del recurso según el entorno de desarrollo a ejecutar, puedes utilizar esta variable escribiendo la clave entre dobles llaves. Por ejemplo:

![Enviroments Postman](/assets/images/posts/Screen-Shot-2018-02-08-at-00.48.04.png)

De esta manera, es muy simple cambiar la URL de todas las llamadas definidas en la colección (únicamente deberás seleccionar el entorno correspondiente).

## Implementa tus tests de API’s rápidamente  y automatízalos en tu Jenkins

Otra de las funcionalidades interesantes, sobre todo para los QAs de los equipos, es la posibilidad de automatizar de una manera sencilla tests de integración para nuestros proyectos. En cada una de las llamadas, Postman nos proporciona una pestaña tests en la que poder configurar los tests que se quieran.

Postman se apoya en código Javascript para programar sus tests. ¿No sabes cómo programar en JS? No te preocupes, Postman se ha preocupado de ofrecer un conjunto de snippets de tests que permiten hacer que desarrollar tus tests no sea más que un juego de niños. En el [siguiente enlace](https://www.getpostman.com/docs/writing_tests) puedes ver más documentación al respecto.

![Tests Postman](/assets/images/posts/Screen-Shot-2018-02-09-at-10.54.03.png)

Uno de los aspectos más demandados, y que Postman ha desarrollado hace no mucho, es la posibilidad de automatizar los tests para que nuestro software IC ejecute las pruebas dentro del flow de construcción de nuevas versiones. Todo aquel que esté interesado, puede visitar el [blog de Postman](http://blog.getpostman.com/2015/09/03/how-to-write-powerful-automated-api-tests-with-postman-newman-and-jenkins/), donde se cuenta paso a paso cómo integrarlos en Jenkins.

## Documentación automática de tu API

Esta es otra de las funciones increíbles de Postman y muy fundamental en el proceso de desarrollo de un API, pues permite visualizar un **API DOC** de tus colecciones, es decir, es capaz de generar automáticamente una documentación de cada petición (mostrando parámetros requeridos, headers, entre otros) y exponerla en una dirección web para que pueda ser accedida por cualquier integrante del equipo.

Adicionalmente, permite entre otras cosas, elegir con que entorno se desea visualizar la documentación, generar los famosos snippets de código para probar el API desde el lenguaje que desees, entre otras cosas más.

![Documentación Postman](/assets/images/posts/Screen-Shot-2018-02-09-at-10.41.29.png)

Para ver la documentación, elegir la colección deseada, hacer clic en el símbolo ">" y en el menú que aparece a la derecha, hacer clic en “**View in Web”**

![Documentación Postman](/assets/images/posts/Screen-Shot-2018-02-09-at-10.42.46.png)

## Mock Server

Esta opción convierte a Postman en una aplicación completa, full-stack por así decirlo, ya que nos permite crear un Mock Server (Servidor de objetos/contenido de pruebas) para poner a disposición cada uno de nuestras peticiones (Requests) definidos, en una URL accesible y que pueda responder con datos de pruebas.

Esto es super fundamental ya que nos permite realizar pruebas directas sobre el API que estemos desarrollando y tratar en la medida de lo posible, abarcar la mayor cantidad de escenarios que puedan presentarse en un entorno live.

Para crear y configurar un **Mock Server** basta con hacer click en el botón color naranja (botón New) que esta en la parte superior derecho, elegir la opción “**Mock Server**” y seguir los pasos del asistente de configuración.

![Mock Server Postman](/assets/images/posts/Screen-Shot-2018-02-09-at-10.51.51.png)

## Mi humilde opinión

No tengo la más mínima duda de que Postman es una de esas herramientas que una vez que la pruebas, se convierte en indispensable para tu labor como desarrollador. No obstante, desde su llegada ha fomentado el trabajo en equipo para generar hábitos a la hora de participar entre todos en el cumplimiento de los requerimientos en cuanto a desarrollo de API’s se trata.

Por otro lado, a medida que pasa el tiempo, ponen a nuestra disposición nuevas características y funcionalidades las cuales benefician directamente al equipo de trabajo, como por ejemplo la posibilidad de exportar en formato json toda la estructura, colecciones, variables, entornos, lo cual brinda la posibilidad de incorporarlo en nuestro repositorio de versiones (GIT) y que sea un documento de colaboración entre todos los miembros del equipo.

¿Ya lo estas utilizando? ¿Cuál es tu opinión sobre Postman?, Si todavía no lo has utilizado, anímate a probarlo y dejarnos tus comentarios e inquietudes al respecto.

Si te gustó este post, ayúdame a que pueda servirle a muchas más personas, compartiendo mis contenidos en tus redes sociales.

Espero que este post haya sido de gran ayuda para ti, y como siempre, cualquier inquietud o duda que tengas, puedes contactarme por cualquiera de las vías disponibles, o dejando tus comentarios al final de este post. También puedes sugerir que temas o post te gustaría leer a futuro.

* * *

Si te gustó el post, regálame tus aplausos!!!

\[wp-applause-button style="width:60px;height:60px;margin: 0 auto;" color="black"\]
