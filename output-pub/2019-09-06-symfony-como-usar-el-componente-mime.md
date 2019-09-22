---
title: "Symfony: Cómo usar el Componente Mime?"
date: "2019-09-06"
---

## Componente MIME de Symfony

> El componente [**Mime**](https://symfony.com/doc/current/components/mime.html) de **Symfony** fue introducido en la versión **4.3** y aún esta considerado un feature experimental
> 
> Nota a considerar antes de comenzar a usarlo en producción.

Otro de los componentes nuevos creados por Symfony e incluidos en su versión 4.3 es el componente **MIME**.

Este componente nos permite crear y manipular mensajes **MIME**, que es el formato utilizado para enviar emails.

Por otro lado, incluye algunas utilidades relacionadas con "los tipos MIME". El [estandar MIME completo](https://en.wikipedia.org/wiki/MIME) _(Multipurpose Internet Mail Extensions)_ es una serie de estándares que definen las funcionalidades añadidas con el paso del tiempo a los emails originales basados en texto (como la posibilidad de adjuntar archivos o formatear los contenidos con HTML).

El componente **Mime** abstrae toda esa complejidad por nosotros para proporcionar dos formas de crear mensajes **MIME**. La primera es una API de alto nivel basada en la clase `Symfony\Component\Mime\Email` y que nos permite de forma fácil y sencilla crear emails con requerimientos básicos y comunes:

```
use Symfony\Component\Mime\Email;
 
$email = (new Email())
    ->from('fabien@symfony.com')
    ->to('foo@example.com')
    ->subject('Important Notification')
    ->text('Lorem ipsum...')
    ->html('<h1>Lorem ipsum</h1> <p>...</p>')
;
```

Como se puede ver en el ejemplo anterior, de una forma sencilla nos permite mediante un objeto de la clase `Symfony\Component\Mime\Email`, configurar los parámetros de envío a los que estamos acostumbrados normalmente.

La otra forma de crear los mensajes **MIME** es con una **API** de bajo nivel basada en la clase `Symfony\Component\Mime\Message` y que te brinda el control absoluto sobre todas y cada una de las partes del mensaje.

El siguiente ejemplo que veremos a continuación esta basado en el anterior pero profundizando a un nivel mas bajo la **API** y configurando en detalle las opciones que queremos para el envío:

```
use Symfony\Component\Mime\Header\Headers;
use Symfony\Component\Mime\Message;
use Symfony\Component\Mime\Part\Multipart\AlternativePart;
use Symfony\Component\Mime\Part\TextPart;
 
$headers = (new Headers())
    ->addMailboxListHeader('From', ['fabien@symfony.com'])
    ->addMailboxListHeader('To', ['foo@example.com'])
    ->addTextHeader('Subject', 'Important Notification')
;
 
$textContent = new TextPart('Lorem ipsum...');
$htmlContent = new TextPart('<h1>Lorem ipsum</h1> <p>...</p>', 'html');
$body = new AlternativePart($textContent, $htmlContent);
 
$email = new Message($headers, $body);
```

## Funcionalidades extra del componente MIME:

- Puedes [definir las direcciones de email](https://symfony.com/doc/master/components/mime.html#email-addresses) mediante cadenas de texto y objetos, con o sin nombre, etc.
- Soporta la posibilidad de [embeber imágenes](https://symfony.com/doc/master/components/mime.html#embedding-images), que es muy útil al crear mensajes HTML complejos.
- Soporta los [archivos adjuntos](https://symfony.com/doc/master/components/mime.html#file-attachments) usando tanto archivos físicos como recursos/streams PHP.

## Integración con Twig

Otra de las tantas funcionalidades del componente Mime es su integración total con [Twig](https://twig.symfony.com/).

La clase `Symfony\Bridge\Twig\Mime\TemplatedEmail` por ejemplo permite renderizar una plantilla Twig para generar los contenidos del email:

use Symfony\\Bridge\\Twig\\Mime\\TemplatedEmail;
 
$email = (new TemplatedEmail())
    ->from('fabien@symfony.com')
    ->to('foo@example.com')
    // ...
 
    // pasa la ruta donde está la plantilla con los contenidos
    ->htmlTemplate('messages/user/signup.html.twig')
 
    // este método define los parámetros (nombre => valor) pasados a la plantilla
    ->context(\[
        'expiration\_date' => new \\DateTime('+7 days'),
        'username' => 'foo',
    \])
;

## Funcionalidades extras del componente MIME con TWIG

- [Permite adjuntar de forma más sencilla](https://symfony.com/doc/master/components/mime.html#embedding-images-in-emails-with-twig) las imágenes.
- [Creación automática de estilos CSS inline](https://symfony.com/doc/master/components/mime.html#inlining-css-styles-in-emails-with-twig): esto es super importante puesto que no todos los clientes de email soportan estilos CSS embebidos entre las etiquetas `<style> ... </style>`.
- Permite el [Renderizado de contenidos Markdown](https://symfony.com/doc/master/components/mime.html#rendering-markdown-contents-in-emails-with-twig) si quieres definir el contenido de tus emails con este popular formato.
- [Soporte del lenguaje de plantillas Inky](https://symfony.com/doc/master/components/mime.html#using-the-inky-email-templating-language-with-twig), que es uno de los más populares para crear mensajes HTML con diseño _responsive_.

El [componente Mime](https://symfony.com/components/Mime) definitivamente es tan completo que te provee de absolutamente todo lo que necesitas para crear cualquier email que requieras para tus desarrollos

Lo que sí tienes que tener en cuenta es que no envía los emails. El envío de los mensajes esta delegado a otro de los componentes de Symfony llamado "**[Mailer](https://symfony.com/doc/current/components/mailer.html)**" y que se incluye también desde la versión **4.3** de **Symfony**. Estaré publicando acerca de este componente más adelante.

## Serializando Mensajes de Email

Los mensajes de email creados con las clases de Email o Message, pueden ser fácilmente serializadas puesto que ellas son objetos simple de datos.

```
$email = (new Email())
    ->from('fabien@symfony.com')
    // ...
;

$serializedEmail = serialize($email);
```

Un caso de uso particular es para almacenar los mensajes de email serializados, incluirlos luego en un Message y ser enviado con el [Componente Messenger](https://www.franciscougalde.com/2019/07/17/symfony-como-usar-el-componente-messenger-parte-1/) y ser recreado luego nuevamente cuando se vaya a enviar.

Debes usar la clase RawMessage para recrear el mensaje de email desde nuestros contenidos serializados:

```
use Symfony\Component\Mime\RawMessage;

// ...
$serializedEmail = serialize($email);

// later, recreate the original message to actually send it
$message = new RawMessage(unserialize($serializedEmail));
```

Es todo por ahora con todo lo relacionado con este maravilloso componente **[MIME](https://symfony.com/doc/current/components/mime.html)** de **Symfony**. Estaré publicando actualizaciones a medida que el componente vaya incorporando nuevas funcionalidades.

Recuerda que si tienes alguna sugerencia o pregunta, no dudes en dejar tus comentarios al final del post.

Si te gustó este post, ayúdame a que pueda servirle a muchas más personas, compartiendo mis contenidos en tus redes sociales.

Espero que este post haya sido de gran ayuda para ti, y como siempre, cualquier inquietud o duda que tengas, puedes contactarme por cualquiera de las vías disponibles, o dejando tus comentarios al final de este post. También puedes sugerir que temas o post te gustaría leer a futuro.

* * *

Si te gustó el post, regálame tus aplausos!!!

\[wp-applause-button style="width:60px;height:60px;margin: 0 auto;" color="black"\]
