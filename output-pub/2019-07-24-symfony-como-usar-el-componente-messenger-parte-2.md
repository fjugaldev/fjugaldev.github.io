---
title: "Symfony: ¿Cómo usar el componente Messenger? – Parte 2"
date: "2019-07-24"
---

## Componente Messenger de Symfony

Tal como vimos en el [post anterior](https://www.franciscougalde.com/2019/07/17/symfony-como-usar-el-componente-messenger-parte-1/) sobre cómo usar el **componente**, hoy continuaremos viendo de que otra forma puede ser implementado y aprovechado para ciertos casos.

En las consideraciones finales del post anterior he planteado un caso particular en donde habría que utilizar los **Transports** y los **Workers** para evitar que una petición de envío de notificaciones tarde demasiado en generarle una respuesta al cliente.

Recordando un poco el diagrama anterior, vemos cómo se comunica cada elemento del **componente**, incluso con servicios de terceros como **[RabbitMQ](https://www.rabbitmq.com)** y **Redis**.

![](images/symfony-messenger-rabbitmq-redis.png)

En esta ocasión veremos como implementarlo usando **[RabbitMQ](https://www.rabbitmq.com/)** y hacer que todo el proceso sea asíncrono.

![bucket (420Ã320)](images/async-127296.png)

## Symfony Messenger y RabbitMQ

> A partir de este momento asumimos que tienes instalado y configurado el servidor de **[RabbitMQ](https://www.rabbitmq.com/)**.
> 
> Puedes utilizar docker para [crear tu entorno de desarrollo web local](https://www.franciscougalde.com/2019/02/16/docker-entorno-de-desarrollo-web-local-parte-i/)

Cómo mencioné anteriormente, vamos a necesitar que el message sea enviado a una cola y procesado posteriormente mediante un **Worker**, para esto, vamos a necesitar el uso del **Transport**.

El transport entre otras cosas permite:

- Enviar y recibir los mensajes que circulan a través de él.
- Permite ser configurado para utilizar un **DSN** para conectarnos con servicios externos como **[RabbitMQ](https://www.rabbitmq.com/)**
- Utiliza el **Messenger Serializer** de **Symfony** para serializar el mensaje. Permite a su vez poder utilizar el que gustes.

Procedemos entonces a definir el **Transport**, para que mediante el uso de un DSN encole los mensajes a enviar para que sean procesados luego por los **Workers**.

Editamos el fichero **config/packages/messenger.yaml** y añadimos lo siguiente:

```
# config/packages/messenger.yaml
framework:
    messenger:
        transports:
            async: '%env(MESSENGER_TRANSPORT_DSN)%'
```

Luego definimos la variable de entorno **MESSENGER\_TRANSPORT\_DSN** en nuestro fichero **.env.dev**

```
MESSENGER_TRANSPORT_DSN=amqp://guest:guest@localhost:5672/%2f/messages
```

De esta manera, hemos definido que nuestro transport, llamado **async** utilizará una conexión **DSN** contra nuestro servidor de **RabbitMQ**

Lo siguiente es asignar el **Transport** a cada **Messag**e, pare ello añadimos lo siguiente en el fichero de configuración **config/packages/messenger.yaml**:

```
# config/packages/messenger.yaml
framework:
    messenger:
        transports:
            async: '%env(MESSENGER_TRANSPORT_DSN)%'
        routing:
            # Route your messages to the transports
            'App\Message\NotificationMessage': async
```

Con esto, cada ves que se despache un mensaje de tipo "**NotificationMessage**", el mismo será enviado a nuestro **Transport** para posteriormente ser encolado en nuestro servidor de **RabbitMQ**. Y finalmente nuestro **Worker** atienda dicha cola, invocando al handler respectivo de cada mensaje, el cual estará encargado de enviar las notificaciones a los usuarios.

## Puesta en marcha

Para comprobar que todo esta funcionando bien y de forma asíncrona, debemos nuevamente ingresar en la ruta "**http://localhost/send?message=Hello from the message side**" y ver que ahora solo debe aparecer el mensaje "**All messages have been sent**"

![](https://i0.wp.com/thinksaveretire.com/wp-content/uploads/2017/07/wait-what-meme.jpg?fit=460%2C300&ssl=1)

## Consumiendo la cola de mensajes

Como mencioné anteriormente, el componente **Messenger** de **Symfony**, posee **Workers** que se encargan de procesar los mensajes que están en la cola y llamar al respectivo **MessageHandler** para cada **Message**.

Para esto, existe un comando de consola que lo que básicamente hace es decirle al **Transport** que le avise cuando hayan nuevos mensajes en cola para que un worker lo procese, lo envíe de nuevo al **MessageBus** y sea procesado por el respectivo **MessageHandler**.

El comando a ejecutar es el siguiente:

```
bin/console messenger:consume-messages async
```

Este comando queda en modo escucha, es decir, se queda en ejecución a la espera de nuevos mensajes. Para salir deberás ejecutar Ctrl \* C para detener su ejecución.

Al ejecutarlo, verás como aparecerá en la consola los mensajes pendientes:

Hello from the message side: user1@franciscougalde.com

Hello from the message side: user2@franciscougalde.com

Hello from the message side: user3@franciscougalde.com

Hello from the message side: user4@franciscougalde.com

Si dejas el comando en ejecución, verás que si vuelves al navegador y refrescas la página, los mensajes empezarán a aparecer nuevamente en la consola.

De esta forma hemos hecho que nuestro **componente Messenger de Symfony** funcione de forma asíncrona y optimizando nuestro sistema para brindarle respuesta inmediata al cliente. Esta técnica es super útil en muchísimos escenarios. No solo para procesar lotes de datos, por ejemplo puede ser usado para proyectar datos cuando se utiliza arquitectura de microservicios.

Voy a dejar este Post hasta aquí ya que mi intención es hablar más adelante sobre este componente de **Symfony** y como conjugarlo con arquitecturas de software como DDD usando **CQRS** y **Event Sourcing.**

Todo el contenido del ejemplo puedes conseguirlo en mi [repositorio](https://github.com/franciscougalde-com/sf4-messenger)

Recuerda que si tienes alguna sugerencia o pregunta, no dudes en dejar tus comentarios al final del post.

Si te gustó este post, ayúdame a que pueda servirle a muchas más personas, compartiendo mis contenidos en tus redes sociales.

Espero que este post haya sido de gran ayuda para ti, y como siempre, cualquier inquietud o duda que tengas, puedes contactarme por cualquiera de las vías disponibles, o dejando tus comentarios al final de este post. También puedes sugerir que temas o post te gustaría leer a futuro.  

Si te gustó el post, regálame tus aplausos!!!

\[wp-applause-button style="width:60px;height:60px;margin: 0 auto;" color="black"\]
