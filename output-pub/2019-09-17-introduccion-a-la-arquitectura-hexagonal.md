---
title: "Introducción a la Arquitectura Hexagonal"
date: "2019-09-17"
---

En el [**episodio 5 de mi podcast** **de** **tecnología**](https://www.franciscougalde.com/2019/08/09/introduccion-a-la-arquitectura-hexagonal-ep-5-codingrocks/) estuve hablando brevemente y de forma genérica sobre esta arquitectura y hoy quiero darte una **Introducción a la Arquitectura Hexagonal** un poco mas explicita.

## Arquitectura de Software

Antes de empezar, es importante saber que la arquitectura de software es un conjunto de convenciones, reglas y normas que se deben cumplir durante el **desarrollo** **de** **software** para garantizar **calidad**, **rendimiento** y **escalabilidad** a lo largo del tiempo.

El implementar una arquitectura de software nos va a permitir entre otras cosas, que todos los involucrados en el proyecto, desarrolladores, analistas, devops, etc. puedan compartir una misma linea de trabajo y de cierta manera ir hacia una misma dirección.

Vale la pena resaltar que la arquitectura de software representa un pilar fundamental en la creación de software y que debe situarse en la parte mas alta del diseño de software.

Una arquitectura de software bien definida nos va a garantizar entre otras cosas, escalabilidad, rendimiento y sobre todo éxito al momento de desarrollar el proyecto.

Algunos ejemplos de arquitecturas que ya muchos de ustedes conocen y utilizan en sus proyectos son el **Modelo Vista Controlador** (**MVC**), que ya muchos frameworks lo incorporan nativamente. **Arquitectura Orientada a Servicios** (**SOA** por sus siglas en ingles) o tan sencillo como la arquitectura **Cliente-Servidor**.

## Arquitectura Hexagonal

Dado que la complejidad de los sistemas a desarrollar hoy dia se ha incrementado notablemente, se ha requerido cada vez mas el uso de "**Arquitecturas Limpias**" o "**Clean Architecture**" que nos permitan separar las responsabilidades en capas y por ende la definición de reglas entre ellas.

La arquitectura hexagonal, también conocida como "**Arquitectura de Puertos y Adaptadores**" por si gustas leer un poco mas en profundidad.

Se refiere a Puertos y Adaptadores por:

- **Puerto**: definición de una interfaz pública.
- **Adapter**: especialización de un puerto para un contexto concreto. Es decir, una clase que implemente una interfaz o puerto.

Esta arquitectura también se basa precisamente en un conjunto de normas, patrones y reglas, expresadas en capas que se añaden para identificar responsabilidades, definir la forma en que se accede a ellas y el flujo que tendrán a lo largo del proyecto.

Lo interesante de esta arquitectura es que pese a que nos obliga a seguir lineamientos y reglas, es totalmente flexible al momento de ser implementada en nuestros proyectos.

He visto cómo en distintos proyectos su implementación, la estructura de directorios, la identificación de las capas, varia y sin embargo sigue manteniendo y haciendo caso fiel a sus principios.

## Beneficios de la Arquitectura Hexagonal

- Mediante sus reglas, nos guía netamente hacia la construcción de un software de Calidad.
- La Arquitectura Hexagonal abraza al patrón **SOLID** por lo que estamos ante una arquitectura que fomenta el uso de patrones de diseño altamente efectivos.
- Nos obliga a que el core business o nuestra lógica de negocios no este acoplada a ninguna tecnología o framework.
- Facilita estar desacoplado del método de entrega haciendo que sea más sencillo que un caso de uso funcione para una **aplicación móvil**, un **API Rest**, una web tradicional, una web **SPA** por **[API Rest](https://www.franciscougalde.com/2018/02/19/como-construir-un-restful-api-con-symfony-4-jwt-parte-1/)**, etc…
- Como cualquier arquitectura basada en la inversión de dependencias facilita que los componentes se puedan _unit testear_.

## Entendiendo la Arquitectura Hexagonal

![Resultado de imagen de wait what meme](images/black-lady-confused-face-wait-what-meme.jpg)

Ahora bien, muy bonito eso de las capas, de los puertos y adaptadores, de las reglas y principios, pero y de que va todo esto?

Pues bien, en esta Introducción a la **Arquitectura Hexagonal**, quiero mostrarte cuales son esas capas que se añaden, cuál es la función de cada capa y cuál es el flujo entre ellas.

La **arquitectura hexagonal**, como su nombre lo dice, puede ser vista como un simple hexágono, sin importar el numero de lados, es solo a manera representativa.

También, he visto algunos artículos donde explican la arquitectura hexagonal basándose en el diagrama de cebolla.

En este tipo de diagrama, cada capa de la cebolla representa una capa de la arquitectura visualizándolas desde lo mas externo a lo más interno.

## Capas de la Arquitectura Hexagonal

- Infrastructure
- Application
- Domain

![](images/1*yR4C1B-YfMh5zqpbHzTyag.png)

**Infrasctructure**: Corresponde a todo lo que tenga que ver con tecnologías y frameworks, por ejemplo un servicio o adaptador de Email, o un sistema de colas RabbitMQ, los controladores, migraciones de doctrine, los Commands de terminal, entre otros.

**Application**: Aquí es donde residen los Casos de Uso ó Application Services que son los encargados de orquestar cada funcionalidad de la aplicación.

**Domain**: El domain es el núcleo de esta arquitectura y en esta capa reside todo lo que debemos proteger de nuestro core business.

Por ejemplo, los modelos, objetos de valor, interfaces, validaciones, entre otros.

Por último y no menos importante, es importante conocer el flujo entre ellas, ya que las capas mas externas pueden conocer lo que hay en las capas mas internas, pero una capa interna no conoce lo que se implementa en su capa mas externa.

Esto último cumple con el principio de inversión de dependencias del patrón **[SOLID](https://www.franciscougalde.com/2019/07/26/patrones-de-diseno-en-php-solid/)**. Si quieres leer un poco más sobre este patrón, puedes ir a [**este** **post**](https://www.franciscougalde.com/2019/07/26/patrones-de-diseno-en-php-solid/).

## Consideraciones finales

He de decirte estimado lector, que hoy en día, la tecnología avanza a pasos agigantados, y es por ello que debemos mantenernos al pie del cañon con todo este tema de lo contrario quedaremos deprecados.

No obstante es importante que incorporemos arquitectura de software en nuestros proyectos independientemente del framework o tecnología que utilicemos.

Esto para garantizar y proteger nuestro legado más preciado que es el núcleo de la aplicación, para que si el día de mañana la tecnología o framework muere, nuestro desarrollo pueda ser re-implementado nuevamente sin tanto trauma.

A simple vista esto es lo que representa la arquitectura hexagonal. En próximos post trataré de ampliar un poco más la información y de añadir un poco más como por ejemplo: **Domain** **Driven** **Design** (**DDD**) y como combinarlo con la **arquitectura hexagonal**.

Recuerda que si tienes alguna sugerencia o pregunta, no dudes en dejar tus comentarios al final del post.

Si te gustó este post, ayúdame a que pueda servirle a muchas más personas, compartiendo mis contenidos en tus redes sociales.

Espero que este post haya sido de gran ayuda para ti, y como siempre, cualquier inquietud o duda que tengas, puedes contactarme por cualquiera de las vías disponibles, o dejando tus comentarios al final de este post. También puedes sugerir que temas o post te gustaría leer a futuro.

* * *

Si te gustó el post, regálame tus aplausos!!!

\[wp-applause-button style="width:60px;height:60px;margin: 0 auto;" color="black"\]
