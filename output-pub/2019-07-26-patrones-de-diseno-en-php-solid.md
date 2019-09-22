---
title: "Patrones de diseño en PHP: SOLID"
date: "2019-07-26"
---

## Patrón de diseño SOLID en PHP

Hoy quiero abrir una serie de post en donde estaré escribiendo acerca del patrón de diseño **SOLID** en **PHP,** uno de los patrones de diseño en PHP más común hoy en día y así, poco a poco, puedas implementar buenas prácticas de desarrollo que te permitirán escribir código limpio, robusto y sobre todo, escalable.

En esta ocasión quiero mostrarles el patrón de diseño **SOLID** en **PHP** el cual se basa en 5 principios básicos que se deben combinar para evitar un mal diseño de nuestro software.

Este patrón fué promovido por Robert C. Martin, conocido como Uncle Bob (Tío Bob) el cual es considerado el padre de la arquitectura de software ya que gracias a su ingenio, nos ha puesto a disposición toda su experiencia y buenas prácticas a seguir en pro de desarrollar sistemas con arquitectura limpia, legibles, robustos y sobre todo, escalables.

Adaptarnos a estos patrones de diseño, al principio suele tomar tiempo, sobre todo entender cuándo y dónde puedo implementarlos, pero una vez que agarras el ritmo y entiendes a ciencia cierta como hacerlo, verás como se convertirá en tu mejor aliado.

Sin más preámbulos, permíteme mostrarte un poco sobre **SOLID**.

**SOLID** es el acrónimo creado para representar y recordar fácilmente sus 5 principios.

## 1\. SINGLE RESPONSABILITY PRINCIPLE

Principio de responsabilidad única: Una clase debe tener una y solo una razón para cambiar o lo que es lo mismo: Una clase debe tener una única responsabilidad.

Visto en un ejemplo real, si necesitamos exportar un documento en distintos formatos como HTML y PDF, no podemos delegar todas las funciones e implementaciones a una única clase, la idea sería repartir cada tarea a una clase por separado. Veamos un ejemplo de como quedaría una implementación aplicando este principio:

### Clase Document

```
class Document
{
    protected $title;
    protected $content;

    public function __construct(string $title, string $content)
    {
        $this->title = $title;
        $this->content= $content;
    }

    public function getTitle(): string
    {
        return $this->title;
    }

    public function getContent(): string
    {
        return $this->content;
    }
}
```

### Interface ExportableDocument

```
interface ExportableDocumentInterface
{
    public function export(Document $document);
}
```

### Clase HtmlExportableDocument

```
class HtmlExportableDocument implements ExportableDocumentInterface
{
    public function export(Document $document)
    {
        echo "DOCUMENT EXPORTED TO HTML".PHP_EOL;
        echo "Title: ".$document->getTitle().PHP_EOL;
        echo "Content: ".$document->getContent().PHP_EOL.PHP_EOL;
    }
}
```

### Clase PdfExportableDocument

```
class PdfExportableDocument implements ExportableDocumentInterface
{
    public function export(Document $document)
    {
        echo "DOCUMENT EXPORTED TO PDF".PHP_EOL;
        echo "Title: ".$document->getTitle().PHP_EOL;
        echo "Content: ".$document->getContent().PHP_EOL.PHP_EOL;
    }
}
```

## OPEN/CLOSED PRINCIPLE

El segundo principio nos habla de que una clase debe estar abierta para su extensión pero cerrada para su modificación

Imaginemos que necesitamos implementar un sistema de login. Inicialmente para autenticar a nuestro usuario necesitamos de un usuario y una contraseña, hasta aquí todo bien, pero ¿qué sucede si requerimos que el usuario se autentique mediante twiter o gmail?

Una implementación que rompe con este principio seria:

```
class LoginService
{
    public function login($user)
    {
        if ($user instanceof User) {
            $this->authenticateUser($user);
        } else if ($user instanceOf ThirdPartyUser) {
            $this->authenticateThirdPartyUser($user);
        }
    }
}
```

Lo correcto seria hacer algo como esto:

```
interface LoginInterface
{
    public function authenticateUser($user);
}

class UserAuthentication implements LoginInterface
{
    public function authenticateUser($user)
    {
        // TODO: Implement authenticateUser() method.
    }
}

Class ThirdPartyUserAuthentication implements LoginInterface
{
    public function authenticateUser($user)
    {
        // TODO: Implement authenticateUser() method.
    }
}

class LoginService
{
    public function login(LoginInterface $user)
    {
        $user->authenticateUser($user);
    }
}
```

Como puedes observar, la clase LoginService es agnóstica de que método de autenticación (Normal, vía google o twitter, etc.) va a realizar, toda la lógica esta dentro de cada Clase de los N tipos de autenticación que podamos tener.

## LISKOV SUBSTITUTION PRINCIPLE

El principio de sustitución de Liskov (conocido como LSP) es un concepto importante cuando se trata de programación orientada a objetos.

> Si H es una clase hija de P, entonces los objetos de P podrían ser substituidos por objetos del tipo H sin alterar las propiedades del problema.

Lo que quiere decir es que cualquier clase hija debería poder ser sustituible por la clase padre sin necesidad de conocer las diferencias entre ellas.

Otra forma de pensar en esto es: si tienes una clase (base), y luego tienes otras cinco clases que extienden esa clase base, cualquier código que use la clase base también debería funcionar si reemplaza esa clase base con alguna de sus clases hijas y viceversa.

Veamos un ejemplo donde se requiere calcular el precio total con impuesto incluido según país del usuario.

```
<?php

interface PriceCalculatorInterface
{
    public static function getTotal(float $price): string;
}

class PriceCalculator implements PriceCalculatorInterface
{
    const DEFAULT_VAT = 7;

    public static function getTotal(float $price): string
    {
        $vat = (($price * self::DEFAULT_VAT) / 100);

        return number_format(round($price + $vat, 2), 1, ',', '.');
    }
}

class SpainPriceCalculator extends PriceCalculator
{
    const DEFAULT_VAT = 21;

    public static function getTotal(float $price): string
    {
        $vat = (($price * self::DEFAULT_VAT) / 100);

        return number_format(round($price + $vat, 2), 2, ',', '.');
    }
}

class ItalyPriceCalculator extends PriceCalculator
{
    const DEFAULT_VAT = 22;

    public static function getTotal(float $price): string
    {
        $vat = (($price * self::DEFAULT_VAT) / 100);

        return number_format(round($price + $vat, 3), 3, '.', ',');
    }
}

class PriceService
{
    public function getTotalPrice(PriceCalculator $priceCalculator): string
    {
        return $priceCalculator::getTotal(1197.45);
    }
}

$priceService = new PriceService();

$defaultTotal = $priceService->getTotalPrice(new PriceCalculator());
$spainTotal   = $priceService->getTotalPrice(new SpainPriceCalculator());
$italyTotal   = $priceService->getTotalPrice(new ItalyPriceCalculator());

echo "Default Total: " . $defaultTotal . PHP_EOL . PHP_EOL;
echo "Spain Total: " . $spainTotal . PHP_EOL . PHP_EOL;
echo "Italy Total: " . $italyTotal;
```

## INTERFACE SEGREGATION PRINCIPLE

> Un cliente (entiéndase una clase) nunca debe ser obligado a implementar una interfaz que no use o los clientes no deben ser forzados a depender de métodos que no usen.

En este punto, me gustaría mencionar una jerga más, los Fat Interface. Esto es que debemos evitar hacer que nuestras interfaces se vuelvan gordas cuando se manejan demasiados contratos. Una interfaz gorda viola también el principio de Responsabilidad Unica (Single Responsability Principle) ya que seguramente dicha interfaz estaría manejando más de una responsabilidad a la vez.

En resumen, debemos garantizar separar funcionalidades y definir los contratos mediante interfaces cuya responsabilidad sea única.

Veamos un ejemplo donde se rompe este principio:

```
interface Worker {
 
  public function takeBreak()
 
  public function code()
 
  public function callToClient()
 
  public function attendMeetings()
 
  public function getPaid()
}

class Manager implements Worker {
  public function code() {
    return false;
  }
}

class Developer implements Worker {
  public function callToClient() {
    echo "I'll ask my manager.";
  }
}
```

Como se puede observar, la interfaz obliga a ambas clases a implementar métodos que no necesita, por ejemplo un Manager no tiene porque implementar el método "**code(..)"** pero si "**attendMeeting(...)**" y un Developer no tiene porque implementar el método "**attendMeeting(...)**" pero si "**code(...)**".

## DEPENDENCY INVERSION PRINCIPLE

Este es quizás el mas simple de los principios y establece que las clases deben depender de abstracciones, no de concreciones. Esencialmente, no depender de clases concretas, y si depender de interfaces.

Tomemos el siguiente ejemplo:

```
class MySqlConnection {
    public function connect() {}
}
 
class PageLoader {
    private $connection;
    public function __construct(MySqlConnection $connection) {
        $this->connection = $connection;
    }
}
```

Esta estructura significa que esencialmente estamos estancados con el uso de **MySQL** para nuestra capa de base de datos.

¿Qué sucede si queremos cambiar esto por un adaptador de base de datos diferente? Podríamos extender la clase **MySqlConnection** para crear una conexión con **Memcache** o **[Redis](https://redis.io/)** por ejemplo, pero eso violaría el principio de la sustitución de **Liskov**.

Lo más probable es que los administradores de bases de datos alternativos puedan usarse para cargar las páginas, por lo que necesitamos encontrar una manera de hacerlo.

La solución aquí es crear una interfaz llamada **ConnectionInterface** y luego implementar esta interfaz en la clase **MySqlConnection** y en la clase **RedisConnection** por ejemplo.

Luego, en lugar de confiar en un objeto **MySqlConnection** que se pasa a la clase **PageLoader**, confiamos en cualquier clase que implemente la interfaz **ConnectionInterface**.

Veamos como quedaría:

```
interface ConnectionInterface {
    public function connect();
} 
 
class MySqlConnection implements ConnectionInterface {
    public function connect() {}
}

class RedisConnection implements ConnectionInterface {
    public function connect() {}
}
 
class PageLoader {
    private $connection;
    public function __construct(ConnectionInterface $connection) {
        $this->connection = $connection;
    }
}
```

Con esta implementación, fue posible crear una clase **RedisConnection** y siempre que implemente la interfaz **ConnectionInterface**, podemos usarlo en la clase **PageLoader** para cargar páginas.

Este enfoque también nos obliga a escribir código de tal manera que evite detalles de implementación específicos en clases que no se preocupan por ello. Debido a que hemos pasado una clase **MySqlConnection** a nuestra clase **PageLoader**, ya no tendríamos porque escribir consultas nativas **SQL** en la clase **PageLoader**, o hacer sentencias IF para determinar que tipo de conexión es e implementar por ejemplo una conexión a **Redis**.

Esto significa que cuando pasamos en un objeto **RedisConnection** se comportará de la misma manera que cualquier otro tipo de clase de conexión.

Y con esto damos por terminada la explicación en detalle de cada uno de los principios de uno de los patrones de diseño en PHP más conocido como lo es el patrón de diseño **SOLID** en **PHP**

Recuerda que si tienes alguna sugerencia o pregunta, no dudes en dejar tus comentarios al final del post.

Si te gustó este post, ayúdame a que pueda servirle a muchas más personas, compartiendo mis contenidos en tus redes sociales.

Espero que este post haya sido de gran ayuda para ti, y como siempre, cualquier inquietud o duda que tengas, puedes contactarme por cualquiera de las vías disponibles, o dejando tus comentarios al final de este post. También puedes sugerir que temas o post te gustaría leer a futuro.  

Si te gustó el post, regálame tus aplausos!!!

\[wp-applause-button style="width:60px;height:60px;margin: 0 auto;" color="black"\]
