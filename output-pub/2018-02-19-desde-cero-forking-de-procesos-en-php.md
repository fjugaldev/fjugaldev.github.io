---
title: "Desde Cero: Forking de procesos en PHP"
date: "2018-02-19"
---

En este post quiero mostrarte como utilizar PHP para hacer **Forking** (clonación por así decirlo) de procesos en hilos (threads).

En sí lo que les voy a mostrar en esta oportunidad es como gestionar la creación de hilos de un proceso para distribuir la carga de ejecución en varias instancias de php. Cada instancia o hilo llevará a cabo una tarea.

Un ejemplo práctico que estaré mostrando para explicar esto, es un sistema de envío de notificaciones push, en donde mediante el forking de procesos en php, podemos hacer que cada hilo se encargue del envío de una notificación push.

## ¿Por qué hacer forking de procesos?

Cuando se programa un algoritmo, tenemos dos formas de resolver la ejecución del código y estas son: de forma secuencial, que no es mas que iterar el código en un mismo proceso php, o de forma paralela, la cual se basa en crear varias instancias la ejecución del proceso php para distribuir la carga y brindar eficiencia a nuestro algoritmo.

Para verlo de una mejor manera, voy a plantearles un ejemplo. Podemos tener un sistema que se encargue de obtener un listado de notificaciones push pendientes por enviar. Cada notificación tiene una cantidad de dispositivos que en muchos casos son numerosos y en muchos otros no tanto.

Entonces si nuestro algoritmo se ejecuta de forma secuencial, irá procesando notificación a notificación, una por una, es decir, toma la primera, selecciona los dispositivos a los que debe enviar, y empieza a enviar cada notificación push.

Hasta que no se haya terminado de enviar a todos los dispositivos, el proceso no pasará a enviar la próxima notificación pendiente a sus respectivos dispositivos. Esto nos hace pensar que el proceso a gran escala puede convertirse en un cuello de botella haciendo que nuestra aplicación se torne pesada e ineficiente.

Aquí es donde entra a destacar el forking de procesos. Y es que podemos hacer que nuestro algoritmo gestione la creación de hilos (forking o paralelización de procesos) y que cada hilo se encargue de seleccionar una notificación pendiente y enviar a cada uno de sus dispositivos la respectiva notificación push.

## ¿Cómo funciona el forking de procesos en PHP?

La base teórica del forking o paralelización nos dice que cuando proceso padre lanza un proceso hijo, se crea en memoria un proceso idéntico al padre, con un identificador de proceso (pid) propio (diferente al del padre) y ejecutándose a partir de la instrucción siguiente a la que creó al propio proceso hijo.

Partiendo de esto, voy a describirle alguno de los métodos que **PHP** pone a nuestra disposición para tal motivo. Si deseas conocer un poco más de estos métodos, puedes revisar [este enlace](http://php.net/manual/es/ref.pcntl.php).

De los métodos de forking de PHP, estos son los dos que estarémos usando en nuestro ejemplo:

- **pcntl\_fork**:Nos permite lanzar hijos de un proceso padre, devolviendo el pid del hijo lanzado. ([pcntl\_fork en PHP](http://www.php.net/manual/en/function.pcntl-fork.php))
- **pcntl\_waitpid**: Nos permite poner al padre en espera de que un hijo suyo termine su ejecución. El parámetro -1 indica que queda a la espera de que algún hijo termine, el primero que lo haga. ([pcntl\_waitpid en PHP](http://www.php.net/manual/en/function.pcntl-waitpid.php)).
- **posix\_kill**: Mata (Kill -9 pid) el proceso marcado por su pid (process id).

Existen otros tantos que puedes revisar en la [página oficial de PHP](http://php.net/manual/es/ref.pcntl.php) que posiblemente puedas llegar a necesitar más adelante según sea el caso.

## Algoritmo de ejemplo

Para este ejemplo, vamos a hacernos la idea mental del sistema de envío de notificaciones push donde cada notificación puede tener “N” cantidad de dispositivos a enviar.

Lo primero que haremos es crear una clase abstracta (**BaseTask.php**) la cual representará la estructura que debe extender la clase (**Task.php**) la cual contiene unos métodos específicos que para nuestro caso pensemos que será donde implementaremos el proceso de enviar la notificación push a cada dispositivo.

### Archivo BaseTask.php

<?php

namespace Threading\\Task;

/\*\*
 \* Clase abstracta de una tarea base la cual es heradada por todas las tareas
 \*/
abstract class BaseTask
{
    /\*\*
     \* Initialize (Ejecutada de primera por el administrador de hilos)
     \* 
     \* @return mixed
     \*/
    public function initialize() 
    {
        return true;
    }

    /\*\*
     \* Ejecutada por el administrador de hilos si el proceso se completó con exito (Cuando el metodo process() haya retornado true)
     \* 
     \* @return mixed
     \*/
    public function onSuccess()
    {
        return true;
    }

    /\*\*
     \* Ejecutada por el administrador de hilos si el proceso se completó con fallos (Cuando el metodo process() haya retornado false)
     \* 
     \* @return mixed
     \*/
    public function onFailure() 
    {
        return false;
    }

    /\*\*
     \* Método principal que contiene la lógica a ser ejecutada por la tarea
     \* 
     \* @param $params array Array asociativo de parametros
     \*
     \* @return boolean True para Éxito, false de lo contrario
     \*/
    abstract public function process(array $params = array());
}

### Archivo Task.php

<?php

namespace Threading\\Task;

/\*\*
 \* Clase Task
 \*/
class Task extends BaseTask
{
    /\*\*
     \* Initialize (Ejecutada de primera por el administrador de hilos)
     \* 
     \* @return mixed
     \*/
    public function initialize() 
    {
        return true;
    }

    /\*\*
     \* Ejecutada por el administrador de hilos si el proceso se completó con exito (Cuando el metodo process() haya retornado true)
     \* 
     \* @return mixed
     \*/
    public function onSuccess()
    {
        return true;
    }

    /\*\*
     \* Ejecutada por el administrador de hilos si el proceso se completó con fallos (Cuando el metodo process() haya retornado false)
     \* 
     \* @return mixed
     \*/
    public function onFailure() 
    {
        return false;
    }

    /\*\*
     \* Método principal que contiene la lógica a ser ejecutada por la tarea
     \* 
     \* @param $params array Array asociativo de parametros
     \*
     \* @return boolean True para Éxito, false de lo contrario
     \*/
    public function process(array $params = array())
    {
        sleep(rand(30, 60));
        echo '\[Pid:' . getmypid() . '\] Tarea ejecutada el ' . date('Y-m-d H:i:s') . PHP\_EOL;
        return true;
    }
}

Como pueden observar la clase Task sobre escribe los siguientes métodos:

- **initialize()**: Es el primer método que se ejecuta al ejecutar la tarea. Imaginemos que aquí podemos implementar el proceso de seleccionar desde la base de datos los dispositivos a los que debemos enviar una notificación push.
- **process()**: Este método es el que lleva el proceso o lógica de la tarea. Podemos decir que aquí pondríamos el proceso de enviar la notificación a cada dispositivo. Si todo sale bien, disparamos el método onSuccess(), de lo contrario, onFailure().
- **onSuccess()**: Este método se ejecuta cuando todo sale bien, puede servir para llevar un log de envíos de notificaciones enviadas.
- **onFailure()**: Este método se ejecuta cuando algo no ha salido bien, puede servir para llevar un log de los errores sucedidos y poder monitorizar lo que está pasando.

Luego crearemos una clase la cual será la encargada de gestionar la creación de hilos, esta clase se llamará “**ThreadManager**“ (**ThreadManager.php**)

### Archivo ThreadManager.php

<?php

namespace Threading;

use Threading\\Task\\BaseTask as AbstractTask;

/\*\*
 \* Multi-thread / task manager
 \*/
class ThreadManager
{
    /\*\*
     \* Array asociativo de pid con hilos activos
     \* @var array
     \*/
    protected $\_activeThreads = array();

    /\*\*
     \* Número máximo de hilos hijos que pueden ser creados por un hilo padre
     \* @var int
     \*/
    protected $maxThreads = 5;

    /\*\*
     \* Class constructor
     \*
     \* @param int $maxThreads Número máximo de hilos hijos que pueden ser creados por un hilo padre
     \*/
    public function \_\_construct($maxThreads = 5)
    {
        $this->maxThreads = $maxThreads;
    }

    /\*\*
     \* Inicia el aministrador de hilos
     \*
     \* @param AbstractTask $task Tarea a iniciar
     \*
     \* @return void
     \*/
    public function start(AbstractTask $task)
    {
        $pid = pcntl\_fork();
        if ($pid == -1) 
        {
            throw new \\Exception('\[Pid:' . getmypid() . '\] No se pudo clonar (fork) el proceso');
        } 
        // Hilo Padre
        elseif ($pid) 
        {
            $this->\_activeThreads\[$pid\] = true;

            // Logrado el numero máximo de hilos permitidos
            if($this->maxThreads == count($this->\_activeThreads)) 
            {
                // Proceso Padre : Chequea que todos los hijos hayan terminado (Para evadir los procesos hilos zombies)
                while(!empty($this->\_activeThreads)) 
                {
                    $endedPid = pcntl\_wait($status);
                    if(-1 == $endedPid) 
                    {
                        $this->\_activeThreads = array();
                    }
                    unset($this->\_activeThreads\[$endedPid\]);
                }
            }
        } 
        // Hilo Hijo
        else 
        {
            $task->initialize();

            // On success
            if ($task->process())
            {
                $task->onSuccess();
            } 
            else 
            {
                $task->onFailure();
            }
            
            // Mata el proceso hijo una vez que haya terminado de ejecutarse           
            posix\_kill(getmypid(), 9);
        }
        pcntl\_wait($status, WNOHANG);
    }
}

Esta clase contiene un único método llamado **start()** el cual es el que inicia el administrador de hilos. Aquí es donde se ejecuta el método pcntl\_fork() para crear un hilo hijo idéntico al padre pero que llevará a cabo una tarea con parámetros distintos.

Dentro de este método start() validamos si el pid que devuelve el método pcntl\_fork():

- Si es -1, no se pudo clonar el proceso en un hilo hijo.
- Si es un número distinto a 0, es el proceso padre cuyo número generado es el pid de su proceso hijo.
- Si es 0, es porque el método pcntl\_fork() se ejecutó desde un hijo hijo.

Puedes ver que si estamos en un proceso padre, verificamos que se hayan creado tantos hijos como este definido en el parámetro “**maxThreads”**. Si esto sucede, nos quedamos esperando a que cada hijo responda para poder matar el proceso hijo. Luego que ya no queden hilos hijos, se vuelven a crear tantos hijos como el parámetro “**maxThreads”** indique.

Ahora veamos como poner en marcha todo esto y como utilizar los métodos de las clases definidas anteriormente.

### Archivo forking.php

<?php

require\_once "Threading/ThreadManager.php";
require\_once "Threading/Task/BaseTask.php";
require\_once "Threading/Task/Task.php";

$maxThreads = 5;
$pushNotifications = 30;
echo 'Ejemplo de forking de procesos con PHP con un máximo de ' . $maxThreads . ' hilos' . PHP\_EOL . PHP\_EOL;
$exampleTask = new Threading\\Task\\Task();
$multithreadManager = new Threading\\ThreadManager();

$cpt = 0;
while (++$cpt <= $pushNotifications)
{
    $multithreadManager->start($exampleTask);
}

Aqui vemos que se instancia la clase “**ThreadManager**” y la clase “**Task**”. Luego con el iterador while simulamos que recorremos las notificaciones push pendientes por enviar.

La clase “**ThreadManager**” va a ir lanzando hilos hijos por cada notificación y se detiene al alcanzar el número máximo de hilos permitidos. Cuando termina con estos hilos, el while se reanuda permitiendole a la clase “**ThreadManager**“ seguir creando hilos hijos.

Para poner en marcha esto, vamos a la terminal, nos situamos sobre el directorio de nuestros archivos del proyecto y ejecutamos:

php forking.php

**Output:**

Ejemplo de forking de procesos con PHP con un máximo de 5 hilos

\[Pid:85760\] Tarea ejecutada el 2018-02-19 01:46:15
\[Pid:85756\] Tarea ejecutada el 2018-02-19 01:46:23
\[Pid:85759\] Tarea ejecutada el 2018-02-19 01:46:23
\[Pid:85758\] Tarea ejecutada el 2018-02-19 01:46:34
\[Pid:85757\] Tarea ejecutada el 2018-02-19 01:46:44
\[Pid:85889\] Tarea ejecutada el 2018-02-19 01:47:22
\[Pid:85890\] Tarea ejecutada el 2018-02-19 01:47:24
\[Pid:85891\] Tarea ejecutada el 2018-02-19 01:47:40
\[Pid:85892\] Tarea ejecutada el 2018-02-19 01:47:40
\[Pid:85888\] Tarea ejecutada el 2018-02-19 01:47:43
\[Pid:86008\] Tarea ejecutada el 2018-02-19 01:48:15
\[Pid:86011\] Tarea ejecutada el 2018-02-19 01:48:18
\[Pid:86009\] Tarea ejecutada el 2018-02-19 01:48:20
\[Pid:86012\] Tarea ejecutada el 2018-02-19 01:48:20
\[Pid:86010\] Tarea ejecutada el 2018-02-19 01:48:37
\[Pid:86119\] Tarea ejecutada el 2018-02-19 01:49:15
\[Pid:86120\] Tarea ejecutada el 2018-02-19 01:49:26
\[Pid:86121\] Tarea ejecutada el 2018-02-19 01:49:28
\[Pid:86122\] Tarea ejecutada el 2018-02-19 01:49:29
\[Pid:86123\] Tarea ejecutada el 2018-02-19 01:49:31
\[Pid:86249\] Tarea ejecutada el 2018-02-19 01:50:02
\[Pid:86246\] Tarea ejecutada el 2018-02-19 01:50:06
\[Pid:86247\] Tarea ejecutada el 2018-02-19 01:50:13
\[Pid:86248\] Tarea ejecutada el 2018-02-19 01:50:29
\[Pid:86250\] Tarea ejecutada el 2018-02-19 01:50:29
\[Pid:86371\] Tarea ejecutada el 2018-02-19 01:51:00
\[Pid:86373\] Tarea ejecutada el 2018-02-19 01:51:05
\[Pid:86372\] Tarea ejecutada el 2018-02-19 01:51:09
\[Pid:86375\] Tarea ejecutada el 2018-02-19 01:51:10
\[Pid:86374\] Tarea ejecutada el 2018-02-19 01:51:25

Podrás ver que el proceso irá imprimiendo mensajes justo cuando una tarea termine. Al principio pensaras que se trata de un proceso secuencial y que no hay ninguna magia detrás, pero la realidad es otra.

Si revisamos los procesos en nuestro sistema operativo podemos ver lo siguiente:

fjugaldev 85772 0.0 0.0 4267752 928 s000 S+ 1:45AM 0:00.00 grep php 
fjugaldev 85770 0.0 0.0 4269360 1216 s000 S+ 1:45AM 0:00.00 sh -c ps aux | grep php 
fjugaldev 85769 0.0 0.0 4330336 424 s000 S+ 1:45AM 0:00.00 watch ps aux | grep php 
fjugaldev 85760 0.0 0.0 4314884 812 s001 S+ 1:45AM 0:00.00 php forking.php 
fjugaldev 85759 0.0 0.0 4306692 844 s001 S+ 1:45AM 0:00.00 php forking.php 
fjugaldev 85758 0.0 0.0 4306692 848 s001 S+ 1:45AM 0:00.00 php forking.php 
fjugaldev 85757 0.0 0.0 4306692 844 s001 S+ 1:45AM 0:00.00 php forking.php 
fjugaldev 85756 0.0 0.0 4314884 820 s001 S+ 1:45AM 0:00.00 php forking.php 
fjugaldev 85755 0.0 0.1 4306948 10636 s001 S+ 1:45AM 0:00.04 php forking.php 
fjugaldev 81634 0.0 0.0 4330592 2808 s000 S+ 1:13AM 0:01.84 watch ps aux | grep php

Pueden notar que pese a que definimos el parametro “maxThreads” a 5, hay 6 procesos llamados **forking.php**, esto es porque uno de ellos es el padre y sus respectivos 5 hilos hijos.

**Nota:** para ver los procesos en tiempo real debes instalar el componente watch, en macOS lo instalé con Homebrew mediante el comando:

brew install watch

Y luego ejecutamos lo siguiente en una terminal aparte para poder monitorear los procesos php.

watch "ps aux | grep php"

Hasta aquí todo respecto al forking de procesos con php. Espero que en el mundo real pueda llegar a ser una técnica ideal cuando se trate de manipular algoritmos que puedan generar cuellos de botella y hacer que nuestra aplicación se vea comprometida en cuanto a rendimiento y eficiencia.

Si gustas revisar el código resultante de nuestra aplicación de ejemplo, puedes clonarlo desde [mi repositorio](https://github.com/fjugaldev/forking-procesos-php) en github.com

Si te gustó este post, ayúdame a que pueda servirle a muchas más personas, compartiendo mis contenidos en tus redes sociales.

Espero que este post haya sido de gran ayuda para ti, y como siempre, cualquier inquietud o duda que tengas, puedes contactarme por cualquiera de las vías disponibles, o dejando tus comentarios al final de este post. También puedes sugerir que temas o post te gustaría leer a futuro.

* * *

Si te gustó el post, regálame tus aplausos!!!

\[wp-applause-button style="width:60px;height:60px;margin: 0 auto;" color="black"\]
