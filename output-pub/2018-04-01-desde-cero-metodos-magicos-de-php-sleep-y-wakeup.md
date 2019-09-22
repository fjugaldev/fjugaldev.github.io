---
title: "DESDE CERO: métodos mágicos en PHP: __sleep y __wakeup"
date: "2018-04-01"
---

Lo que voy a mostrarles a continuación es algo que seguramente pasará a formar parte de tus cartas bajo la manga. Y es que los **métodos mágicos en PHP **pueden ser muy útiles cuando se trata de trabajar con programación orientada a objetos. 

Para explicar brevemente de que se tratan estos dos **métodos** **mágicos**, comenzaré con comentarles que tanto el método **\_\_sleep()** como el método **\_\_wakeup()**, son disparados al **serializar** (**serialize**) y **deserializar** (**deserialize**) una clase que contenga dichos métodos.

El método \_\_sleep() se ejecuta al serializar la clase, y el método \_\_wakeup() al deserealizarla. Veamos un ejemplo práctico para entender un poco como funciona y ver lo útil que pueden ser estos métodos.

## Ejemplo práctico de los métodos mágicos en PHP:

Comenzaremos primero con crear una simple tabla en donde simularemos un sistema de envío de notificaciones en donde almacenaremos un nombre descriptivo, la fecha en que se debe enviar y la configuración de envío de la notificación (titulo, mensaje, destinatario, tipo).

Esta será nuestra simple estructura:

CREATE TABLE \`notifications\` (
  \`id\` int(11) NOT NULL AUTO\_INCREMENT,
  \`name\` varchar(80) NOT NULL,
  \`scheduled\` datetime NOT NULL,
  \`configuration\` blob,
  \`created\_at\` datetime NOT NULL DEFAULT CURRENT\_TIMESTAMP,
  \`updated\_at\` datetime NOT NULL DEFAULT CURRENT\_TIMESTAMP ON UPDATE CURRENT\_TIMESTAMP,
  PRIMARY KEY (\`id\`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

Lo siguiente será crear nuestra clase de notificaciones, en donde almacenaremos los parámetros que definen la configuración de envío la cual será almacenada en la columna "**configuration**"

## Clase Notification.php

<?php

class Notification 
{

    const TYPE\_SMS   = "SMS";
    const TYPE\_EMAIL = "EMAIL";

    protected $title;
    protected $body;
    protected $to;
    protected $type;
    protected $payload;

    public function setTitle($title) {
        $this->title = $title;
    }

    public function setBody($body) {
        $this->body = $body;
    }

    public function setTo($to) {
        $this->to = $to;
    }

    public function setType($type) {
        $this->type = $type;
    }

    protected function buildPayload() {
        $configuration = array(
            'provider' => $this->type,
            'params'   => array(
                'title' => $this->title,
                'body'  => $this->body,
                'to'    => $this->to,
            ),
        );

        $this->payload = json\_encode($configuration);
    }

    public function \_\_sleep() {
        echo "Serialización detectada..." . PHP\_EOL;
        return array(
            'title',
            'body',
            'to',
            'type',
        );
    }
    public function \_\_wakeup() {
        echo "Deserializando..." . PHP\_EOL;
        $this->buildPayload();
    }
    
    public function send() {
        echo "Enviando notificación utilizando el payload: {$this->payload} ..." . PHP\_EOL;
    }

}

Ahora veamos como crear un nuevo envío de notificación en nuestra base de datos utilizando nuestra clase de notificaciones como configuración de parámetros:

## addNotification.php

<?php
require\_once "Notification.php";

$conn = mysqli\_connect("127.0.0.1", "root", "root", "sandbox");

$notification1 = new Notification();
$notification1->setTitle("Noti 1");
$notification1->setBody("Este es el cuerpo de la notificación 1");
$notification1->setTo("fjugal.dev@franciscougalde.com");
$notification1->setType(Notification::TYPE\_EMAIL);

$notification2 = new Notification();
$notification2->setTitle("Noti 2");
$notification2->setBody("Este es el cuerpo de la notificación 2");
$notification2->setTo("+34691224675");
$notification2->setType(Notification::TYPE\_SMS);

$notification3 = new Notification();
$notification3->setTitle("Noti 3");
$notification3->setBody("Este es el cuerpo de la notificación 3");
$notification3->setTo("info@franciscougalde.com");
$notification3->setType(Notification::TYPE\_EMAIL);

$scheduled = new DateTime("now");
$scheduled = $scheduled->format("Y-m-d H:i:s");

$configuration1 = serialize($notification1);
$configuration2 = serialize($notification2);
$configuration3 = serialize($notification3);

$bulk\_inserts = array(
    array(
        'name'          => 'Prueba Notificacion 1',
        'scheduled'     => $scheduled,
        'configuration' => $configuration1
    ),
    array(
        'name'          => 'Prueba Notificacion 2',
        'scheduled'     => $scheduled,
        'configuration' => $configuration2
    ),
    array(
        'name'          => 'Prueba Notificacion 3',
        'scheduled'     => $scheduled,
        'configuration' => $configuration3
    ),
);

foreach ($bulk\_inserts as $item) {
    echo 'Insertando notificación: ' . $item\['name'\] . PHP\_EOL;
    $sql = "INSERT INTO notifications (\`name\`,\`scheduled\`,\`configuration\`) VALUE ('{$item\['name'\]}', '{$item\['scheduled'\]}','{$item\['configuration'\]}')";
    mysqli\_query($conn, $sql) or die(mysqli\_error($conn));
}

echo 'Notificaciónes creadas con éxito!';

Como se puede observar, se generaron 3 instancias distintas de la clase "**Notification**" para configurar distintos tipos de envíos y posteriormente almacenarlos en nuestra estructura de datos.

Hasta aquí vimos como podemos utilizando el **método** **mágico** "**\_\_sleep()**" **serializar** nuestra clase con las propiedades definidas y sus valores específicos para cada instancia.

Para probar lo que llevamos hasta ahora, desde tu terminal, sitúate en el directorio de los archivos y ejecuta:

php addNotification.php

Si todo va bién debemos ver algo como:

Serialización detectada...
Serialización detectada...
Serialización detectada...
Insertando notificación: Prueba Notificacion 1
Insertando notificación: Prueba Notificacion 2
Insertando notificación: Prueba Notificacion 3
Notificaciónes creadas con éxito!

Si echas un vistazo a la columna "**configuration**" de nuestra tabla "**notifications**" podrás ver lo siguiente:

"O:12:"Notification":4:{s:8:"\*title";s:6:"Noti 1";s:7:"\*body";s:39:"Este es el cuerpo de la notificación 1";s:5:"\*to";s:30:"fjugal.dev@franciscougalde.com";s:7:"\*type";s:5:"EMAIL";}"

Y esto no es mas que la clase **serializada**, y que gracias a nuestro **método** **mágico** "**\_\_sleep()**" le indicamos cuales propiedades de todas las que posee, bastarán para ser **serializada**. Esto es porque no siempre queremos **serializar** toda la clase entera, si no solo lo necesario. Aquí vemos que solo **serializamos** algunas de sus propiedades.

Lo siguiente sera entonces acceder ahora a cada registro de nuestra tabla de "**notifications**" para simular el envío de las notificaciones.

## sendNotification.php

<?php
require\_once "Notification.php";

$conn = mysqli\_connect("127.0.0.1", "root", "root", "sandbox");

$sql = "SELECT \* FROM notifications";
$query = mysqli\_query($conn, $sql) or die(mysqli\_error($conn));

$notifications = mysqli\_fetch\_all($query, MYSQLI\_ASSOC);

foreach($notifications as $notification) {
    /\*\* @var Notification $objNotification \*/
    $objNotification = unserialize($notification\['configuration'\]);

    echo "---------------------- NUEVA NOTIFICACIÓN ----------------------" . PHP\_EOL;
    echo "Notificación a enviar:" . $notification\['name'\] . PHP\_EOL;
    echo "Scheduled:" . $notification\['scheduled'\] . PHP\_EOL;
    echo "Titulo: " . $objNotification->getTitle() . PHP\_EOL;
    echo "Cuerpo: " . $objNotification->getBody() . PHP\_EOL;
    echo "Destinatario: " . $objNotification->getTo() . PHP\_EOL;
    echo "Tipo: " . $objNotification->getType() . PHP\_EOL . PHP\_EOL;
    echo "Enviando notificación..." . PHP\_EOL;
    $objNotification->send();
    echo "----------------------------------------------------------------" . PHP\_EOL . PHP\_EOL;
}

Ahora veamos como funciona el **método** **mágico** "**\_\_wakeup()**" quien en este caso se dispara al momento de **deserializar** (**unserialize**) nuestra clase.

Ejecutemos:

php sendNotification.php

y veremos algo como:

Deserializando...
---------------------- NUEVA NOTIFICACIÓN ----------------------
Notificación a enviar:Prueba Notificacion 1
Scheduled:2018-04-01 20:53:13
Titulo: Noti 1
Cuerpo: Este es el cuerpo de la notificación 1
Destinatario: fjugal.dev@franciscougalde.com
Tipo: EMAIL

Enviando notificación...
Enviando notificación utilizando el payload: {"provider":"EMAIL","params":{"title":"Noti 1","body":"Este es el cuerpo de la notificaci\\u00f3n 1","to":"fjugal.dev@franciscougalde.com"}} ...
----------------------------------------------------------------

Deserializando...
---------------------- NUEVA NOTIFICACIÓN ----------------------
Notificación a enviar:Prueba Notificacion 2
Scheduled:2018-04-01 20:53:13
Titulo: Noti 2
Cuerpo: Este es el cuerpo de la notificación 2
Destinatario: +34691224675
Tipo: SMS

Enviando notificación...
Enviando notificación utilizando el payload: {"provider":"SMS","params":{"title":"Noti 2","body":"Este es el cuerpo de la notificaci\\u00f3n 2","to":"+34691224675"}} ...
----------------------------------------------------------------

Deserializando...
---------------------- NUEVA NOTIFICACIÓN ----------------------
Notificación a enviar:Prueba Notificacion 3
Scheduled:2018-04-01 20:53:13
Titulo: Noti 3
Cuerpo: Este es el cuerpo de la notificación 3
Destinatario: info@franciscougalde.com
Tipo: EMAIL

Enviando notificación...
Enviando notificación utilizando el payload: {"provider":"EMAIL","params":{"title":"Noti 3","body":"Este es el cuerpo de la notificaci\\u00f3n 3","to":"info@franciscougalde.com"}} ...
----------------------------------------------------------------

Aqui podemos ver que parte de lo que se imprime, es gracias a que nuestro **método** **mágico** "**\_\_wakeup()**" al momento de **deserializar** la clase, se ejecutó y adicional a rearmar nuestra clase **serializada** nuevamente en un objeto, procesó los datos en sus propiedades para armar nuestro payload de envío.

Y con esto podemos ver lo super útil que puede llegar a ser esta combinación de los **métodos** **mágicos** con el método **serialize** de **PHP**.

Particularmente, utilizo los **métodos mágicos en PHP** cuando necesito almacenar en una estructura de datos, configuraciones dinámicas que pueden ser basadas en diferentes tipos de objetos y que de otra forma habría que normalizar nuestra base de datos utilizando quizás muchísimas tablas, columnas, entre otros.

Lo interesante de esto es que no importa si programas en Legacy o utilizando algún framework como **Symfony**, los **métodos mágicos en PHP** siempre serán tu mejor amigo.

Todo el contenido de este ejemplo práctico puedes descargarlo desde [mi repositorio en github](https://github.com/fjugaldev/magic-methods-php).

Si te gustó este post, ayúdame a que pueda servirle a muchas más personas, compartiendo mis contenidos en tus redes sociales.

Espero que este post haya sido de gran ayuda para ti, y como siempre, cualquier inquietud o duda que tengas, puedes contactarme por cualquiera de las vías disponibles, o dejando tus comentarios al final de este post. También puedes sugerir que temas o post te gustaría leer a futuro.

* * *

Si te gustó el post, regálame tus aplausos!!!

\[wp-applause-button style="width:60px;height:60px;margin: 0 auto;" color="black"\]
