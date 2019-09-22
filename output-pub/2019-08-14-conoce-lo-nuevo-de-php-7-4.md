---
title: "Conoce lo nuevo de PHP 7.4"
date: "2019-08-14"
---

Como es de saber, esta nueva versión será liberada el **_28 de Noviembre de 2019_**. Conoce lo nuevo de **PHP 7.4** y empieza a tomar ventaja de sus novedades.

Puede ver la lista completa de funciones y novedades en la [**página oficial de RFC.**](https://wiki.php.net/rfc#php_74)

> Aún cuando **PHP 7.4** aumenta de forma significativa el desempeño y mejora la legibilidad del código, **PHP 8** será el logro más importante para el desempeño de PHP, ya que la propuesta para la [**inclusión de JIT**](https://wiki.php.net/rfc/jit) ya ha sido aprobada.
> 
> A tomar en cuenta...

Y para no extendernos mucho e ir al grano, haré un resumen sencillo de lo nuevo que trae esta versión **7.4** de **PHP**.

## Arrow Functions en PHP 7.4

Esta actualización ha sido añadida a mi lista de favoritas para esta versión. 😉

Arrow functions, también llamadas "**short closures**", permiten definir funciones one-liner menos verbosas.

Anteriormente hacíamos cosas como:

```
array_map(function (User $user) { 
    return $user->id; 
}, $users)
```

Ahora, podrás definir lo anterior como:

```
array_map(fn (User $user) => $user->id, $users)
```

Una sintaxis un poco parecida a como se hace en typescript :).

Adicionalmente, hay algunas notas acerca de los Arrow Functions:

- Ellas pueden acceder al ambito principal sin necesidad de utilizar la palabra clave "**use**".
- `$this` esta disponible como un closure normal.
- Pueden contender solo una linea, que también es la linea de retorno.

## Typed Properties en PHP 7.4

Una de mis favoritas, algo que deseaba tener desde hace tiempo. 😜

Ahora las propiedades pueden ser tipadas al igual que como ya se implementó con los argumentos de los métodos y funciones.

```
class User
{
    private int $id;

    private string $name;
    
    private int $age;

    private float $salary;
}
```

### Consideraciones adicionales

- Las propiedades tipadas requieren un modificador de acceso como **private**, **protected**, **public** ó **var**.

- Todos los tipos son permitidos menos **void** y **callable**.

- Las propiedades tipadas deben ser inicializadas previamente antes de ser accedidas / leidas. Bien séa asignandoles valores por defecto o estableciendole los valores desde el constructor o mediante un setter.

- Solo podremos inicializar a **null** una propiedad que haya sido configurada como **nullable**. Ejemplo:

```
class User
{
    private ?string $address = null;
}
```

#### Listado de tipados disponibles:

- bool
- int
- float
- string
- array
- iterable
- object
- ? (nullable)
- self & parent
- Classes & interfaces

## Improved type variance en PHP 7.4

Esta es otra de mis favoritas 😬

Type variance propone poder usar tipos de retornos covariantes y argumentos contra variantes.

Esto significa que podremos definir el tipo de retorno de un método pero al ser implementado o sobre escrito por una clase, podemos variar el tipo de retorno. O en el caso de los argumentos de un método, podemos igualmente variarlo. Veamos un ejemplo:

### Covariación de tipos de retorno

```
class ParentType {}
class ChildType extends ParentType {}

class A
{
    public function covariantReturnTypes(): ParentType
    { /* … */ }
}

class B extends A
{
    public function covariantReturnTypes(): ChildType
    { /* … */ }
}
```

### Contravariación de argumentos

```
class ParentType {}
class ChildType extends ParentType {}

class A
{
    public function contraVariantArguments(ChildType $type)
    { /* … */ }
}

class B extends A
{
    public function contraVariantArguments(ParentType $type)
    { /* … */ }
}
```

## Null coalescing assignment operator en PHP 7.4

El operador de coalescencia nos permitía de una forma corta, asignar un valor null en caso de que la primera condición no se cumpliera, ejemplo:

```
$variable = $this->getParameter('id') ?? null;
```

Ahora podremos resumirlo aún más usando:

```
$variable ??= $this->getParameter('id');
```

## Array spread operator [RFC](https://wiki.php.net/rfc/spread_operator_for_array)

Ahora es posible usar el operador de propagación en arrays, veamos un ejemplo:

```
$arrayA = [1, 2, 3];

$arrayB = [4, 5];

$result = [0, ...$arrayA, ...$arrayB, 6 ,7];

//output will be..
[0, 1, 2, 3, 4, 5, 6, 7]
```

> Esto solo funciona con arrays que contengan keys numéricos.
> 
> Nota.

## Numeric Literal Separator [RFC](https://wiki.php.net/rfc/numeric_literal_separator)

PHP 7.4 permite usar underscores o guiones bajos para visualmente separar valores numéricos, ejemplo:

```
$unformattedNumber = 107925284.88;

$formattedNumber = 107_925_284.88;
```

Los "\_" serán ignorados por el motor de PHP.

## Preloading

Otro de los features de bajo nivel es el precargado. Es una adición sorprendente al núcleo de PHP, lo cual puede resultar en una significante mejora de rendimiento.

En pocas palabras, si utilizas un framework, los archivos de clases y demás deben ser cargados y enlazados en cada petición. La Precarga le permite al servidor cargar los archivos PHP en memoria en el arranque, y luego ponerlos permanentemente disponibles para todas las peticiones posteriores.

La ganancia de rendimiento tiene, por supuesto, un costo: si se cambia la fuente de los archivos precargados, el servidor debe reiniciarse.

### Custom object serialization

Dos nuevos métodos mágicos han sido añadidos para el release de esta versión: `__serialize`y `__unserialize`. La diferencia entre estos metodos y `__sleep` y `__wakeup` es tan expuestas en el RFC discussed in the **[RFC](https://wiki.php.net/rfc/custom_object_serialization)**.

## Deprecations[](https://kinsta.com/es/blog/php-7-4/#deprecations)

Las siguientes funciones/funcionalidades serán deprecadas con PHP 7.4. para ver una lista más comprensiva de las depreciaciones. Lea las Notas de [Actualización de PHP 7.4.](https://github.com/php/php-src/blob/PHP-7.4/UPGRADING)

Estas son las novedades mas importantes que vendrán en noviembre, si quieres conocer más sobre lo que vendrá puedes echarle un ojo a este [**link**](https://wiki.php.net/rfc).

Recuerda que si tienes alguna sugerencia o pregunta, no dudes en dejar tus comentarios al final del post.

Si te gustó este post, ayúdame a que pueda servirle a muchas más personas, compartiendo mis contenidos en tus redes sociales.

Espero que este post haya sido de gran ayuda para ti, y como siempre, cualquier inquietud o duda que tengas, puedes contactarme por cualquiera de las vías disponibles, o dejando tus comentarios al final de este post. También puedes sugerir que temas o post te gustaría leer a futuro.

* * *

Si te gustó el post, regálame tus aplausos!!!

\[wp-applause-button style="width:60px;height:60px;margin: 0 auto;" color="black"\]
