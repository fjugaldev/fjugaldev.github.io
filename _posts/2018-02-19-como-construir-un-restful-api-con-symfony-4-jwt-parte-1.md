---
layout: post
title: "Como construir un RESTful API con Symfony 4 + JWT - Parte 1"
author: fjugaldev
categories: [ Desarrollo Web ]
tags: [API, Frameworks PHP, PHP, REST, RESTful, Symfony, Symfony 4, Symfony Flex]
image: assets/images/posts/como-construir-restful-api-symfony-4-jwt.jpg
description: "Aprende como crear un RESTful API con Symfony 4 + JWT. Conoce las ventajas de utilizar esta arquitectura y además entender como funcionan los verbos HTTP."
featured: false
hidden: false
---

En esta serie de posts, trataré de explicar de la forma más detallada posible, **como construir un RESTful API con Symfony 4 + JWT**, haciendo énfasis en la parte técnica y a su vez en explicar el porque deberíamos apostar en la mayoría de nuestros desarrollos, hacia una arquitectura basada en servicios **RESTful**.

Actualmente, y desde hace algún tiempo, se ha venido implementando esta arquitectura (**RESTful**), ya que entre otras cosas, garantiza que nuestras aplicaciones puedan crecer (escalar) en el tiempo y a su vez, podamos de cierta manera reutilizar toda esta estructura (backend, servicios, etc), en todas aquellas plataformas donde deseemos integrar nuestras aplicaciones.

Todo esto nos lleva a pensar que es una muy buena práctica, implementar siempre que podamos, servicios **RESTful API** en nuestras aplicaciones, ya que sin importar si nuestra aplicación se integrará en multiples plataformas, es mejor siempre pensar en futuro ya que si en algún momento llegase a ser necesario, ya poseeremos nuestra estructura definida para soportar servicios **RESTful**, y lo mejor de todo es que esta arquitectura permite también que aplicaciones de terceros puedan consumir de nuestros servicios lo que abre una extensa ventana de oportunidades para nuestras aplicaciones.

Hoy en día, grandes aplicaciones web como **Facebook**, **Twitter**, **Instagram**, entre otros, ponen a disposición esta arquitectura **RESTful** mediante los famosos **API´s** (Application Programming Interface ó Interfaz de Programación de Aplicaciones), lo cual gracias a esto, podemos acceder a cierta data de estas aplicaciones para el uso de terceros.

Entendiendo un poco mejor esto de REST y RESTful, procedo a definir un poco más estos conceptos.

## ¿Qué significa REST y RESTful?

**REST** (Representational State Transfer) es una arquitectura que se ejecuta sobre **HTTP**, es decir, cualquier interfaz entre sistemas que use el protocolo **HTTP** para obtener datos o generar operaciones sobre esos datos en todos los formatos posibles, como **XML** y **JSON**. 

Por lo tanto, **RESTful** hace referencia a un servicio web que implementa la arquitectura **REST**. Por lo que podríamos decir que **RESTful** se refiere a todos aquellos servicios (endpoints de nuestra API) que implementen la arquitectura **REST.** Estos servicios, implementarán los famosos VERBOS de **HTTP**, que no son más que los métodos (**GET**, **POST**, **PUT**, **PATH**, **DELETE**, **OPTIONS**) que permiten realizar acciones especificas en nuestra estructura backend definida. Esto lo explicaré un poco más adelante para entender un poco como definir los recursos o servicios para cada verbo, para que sirve cada uno, y que hay que tener en cuenta al momento de definirlos.

Ahora bien, conociendo un poco la teoría de los **RESTful API**, vamos a adentrarnos un poco a ver como aprovechar esta arquitectura utilizando el framework **Symfony 4 + JWT**. JWT (JSON Web Tokens, es una librería para autenticación y autorización de nuestros recursos y servicios web).

## **¿Que es la autenticación basada en token?**

La autenticación tradicional, se gestiona mediante sesiones. El usuario se loguea con sus credenciales, el servidor las valida y guarda en una sesión la información del usuario.

La autenticación basada en tokens es stateless. Esto quiere decir que no guarda ningún tipo de información del usuario en el servidor ni en la sesión.

El funcionamiento es el siguiente, un usuario se autentica contra la API, por ejemplo mediante el envío de un usuario y una contraseña. El servidor valida las credenciales y si son correctas, envía al usuario un Token encriptado. El usuario almacena el Token y a partir de entonces, cada petición HTTP que haga el usuario, va acompañada del Token en la cabecera. Este Token no es más que una firma cifrada que permite a nuestra API identificar al usuario.

De esta forma, no se guarda ningún estado entre las diferentes peticiones contra la API por lo que la autenticación se puede realizar desde diferentes tipos de clientes.

## **¿Que es JSON Web Tokens o JWT?**

JWT es es un estándar abierto (RFC 7519) que nos permite la transmisión de datos de forma segura. Los token pueden ser firmados mediante clave simétrica (algoritmo HMAC) o mediante clave asimétrica (RSA). JWT contiene toda la información que necesitemos sobre el usuario, de forma que se evita la necesidad de repetir consultas a base de datos para obtener estos datos.

## Aplicación Ejemplo: Tablero Kanban

Para ello, vamos a aprender a través de una aplicación de ejemplo, como instalar, configurar e implementar todas las librerías y paquetes necesarios para nuestro proyecto en **Symfony 4**.

La aplicación que vamos a desarrollar será la de un tablero kanban tipo [Trello.com](https://trello.com/), algo sencillo obviamente, por lo que nuestra funcionalidad estará limitada a:

- Registro de Usuario.
- Login de usuario.
- CRUD para administrar tableros.
- CRUD para administrar tareas.

A manera general, los tableros tendrán por defecto los siguientes estados para las tareas:

- Backlog
- Working
- Done

Lo primero que siempre recomiendo hacer antes de escribir nuestras lineas de código, es planificar un poco como será la estructura de nuestra aplicación, siempre es bueno, antes de poner manos a la obra, diseñar, aunque sea de forma sencilla, como queremos que nuestra aplicación funcione y tratar en lo posible de tomar en cuenta aquellos puntos que consideremos más importantes, pensando incluso un poco en el futuro que queremos para con ella.

Dicho esto, empezaremos en primer lugar diseñando la estructura de campos que tendrán nuestras entidades, así como también las rutas (URI´s) que tendremos para cada recurso o endpoint (servicio), esto es en este caso práctico, lo más importante que debemos tomar en cuenta antes de empezar a desarrollar.

### Entidades

En cuanto a las entidades, solo tendremos 3, las cuales son: user (almacena datos personales y de acceso de cada usuario), board (los tablones creados para cada usuario), task (las tareas creadas para cada usuario)

Definición de propiedades de las entidades

```text
User:
 - id :integer
 - name :string(150)
 - email :string(255)
 - username :string(255) 
 - password :string(255)
 - roles :json\_array 
 - createdAt :datetime
 - updatedAt :datetime

Board:
 - id :integer
 - name :string(100)
 - user :integer
 - createdAt :datetime
 - updatedAt :datetime

Task:
 - id :integer
 - title :string(150)
 - description :text
 - board :integer 
 - status :string(50)
 - priority :string(10)
 - createdAt :datetime
 - updatedAt :datetime
```

### Rutas (URI´s) de nuestros endpoints:

```text
user:
 - register: POST /api/register
 - login: POST /api/login\_check

board:
 - add: POST /api/v1/board
 - edit: PUT /api/v1/board
 - delete: DELETE /api/v1/board/{id}
 - list: GET /api/v1/board
 - list-item: GET /api/v1/board/{id}

task:
 - add: POST /api/v1/task
 - edit: PUT /api/v1/task
 - delete: DELETE /api/v1/task/{id}
```

Con esto ya tenemos una idea de como serán nuestras entidades básicamente, y a su vez, las rutas que vamos a definir en nuestro API para poner estos recursos o servicios a disposición de nuestra aplicación para consumirlos a medida que sea necesario.

Hasta aquí dejaré este post para no extenderlo demasiado. Puedes ir a Como construir un [**RESTful API con Symfony 4 + JWT - Parte 2**]({{site.url}}/2018/02/19/construir-restful-api-symfony-4-jwt-parte-2/), para continuar con esta serie de post.

Si te gustó este post, ayúdame a que pueda servirle a muchas más personas, compartiendo mis contenidos en tus redes sociales.

Espero que este post haya sido de gran ayuda para ti, y como siempre, cualquier inquietud o duda que tengas, puedes contactarme por cualquiera de las vías disponibles, o dejando tus comentarios al final de este post. También puedes sugerir que temas o post te gustaría leer a futuro. 

* * *
