---
title: "Como construir un RESTful API con Symfony 4 + JWT - Parte 2"
date: "2018-02-19"
---

Continuamos con esta serie de posts donde explico de manera detallada como construir un **RESTful API con Symfony 4 + JWT.**

En el [**post anterior**](https://www.franciscougalde.com/2018/02/19/como-construir-un-restful-api-con-symfony-4-jwt-parte-1/), explico como  brevemente lo que es **RESTful** y su principal ventaja al momento de implementar esta arquitectura en cada uno de nuestros desarrollos, así como también diseñamos nuestra estructura de datos a tomar en cuenta. También definimos las URI´s que nuestro API va a proveer para consumir los servicios.

También, diseñamos la estructura que tendrá nuestras entidades dentro de la aplicación y las rutas que estarán accesibles por nuestro API para el consumo de los servicios RESTful que definiremos más adelante.

Una vez diseñada de forma sencilla lo que queremos lograr con nuestra aplicación, vamos a proceder a empezar a armar las piezas necesarias para completar poco a poco lo anteriormente planeado. En este post abarcaremos lo siguiente:

1. [Instalación de Symfony 4](#instalar-symfony)
2. [Instalación de bundles y componentes necesarios para nuestra aplicación.](#instalar-paquetes)
3. [Creación de las entidades.](#crear-entidades)

## 1\. Instalación de Symfony 4

Con la llegada de **Symfony 4**, ya no se utilizará el instalador de Symfony que usábamos para crear nuevas instancias del framework. Ahora, utilizaremos **[Composer](https://getcomposer.org/)** para generar el skeleton de una aplicación **Symfony 4**, donde inicialmente se instalará lo básico y necesario y que luego, a medida que vayamos requiriendo, iremos instalando nuevos componentes y paquetes utilizando las recetas de Symfony Flex.  

> Symfony Flex se basa en la composición en vez de herencia. En si, es un simple pero poderoso plugin de composer que permite instalar y auto configurar componentes y paquetes.

Antes de comenzar, quiero aclarar que esta serie de post esta diseñada principalmente para reproducirse en ambientes bajo **macOS** y **Linux**. En Windows la cosa cambia un poco por el tema de la consola de comandos, al menos de que utilicen el bash de linux que se puede instalar desde Windows 10.

Para instalar **Symfony 4 **lo primero que debemos hacer es situarnos sobre el directorio donde queremos que se genere la estructura de nuestro proyecto, generalmente suele ser la raíz de nuestro web server, en mi caso seria algo como:

```shell 
cd /home/fjugaldev/Sites
```

Luego podemos proceder a ejecutar el comando de composer para generar el skeleton de Symfony 4.

composer create-project symfony/skeleton my\_kanban

Esto generará entonces la estructura de Symfony en un directorio llamado "my\_kanban", ustedes pueden cambiar este último parámetro por el que deseen.

Si todo sale bien, veremos al final de todo el proceso el siguiente mensaje:

![Instalación Symfony](/assets/images/posts/Screen-Shot-2018-02-02-at-17.23.50.png)

Con esto tenemos creado correctamente el skeleton de Symfony 4 para nuestra aplicación

## 2. Instalación de bundles y componentes necesarios para nuestra aplicación.

Ahora procederemos a instalar los componentes de Symfony y de terceros, necesarios para la creación de nuestro RESTful API. Los componentes a instalar son:

- **symfony/orm-pack:** Paquete ORM para instalar el bundle de Doctrine.
- **lexik/jwt-authentication-bundle**: Bundle para proteger nuestros servicios mediante JSON Web Tokens.
- **lcobucci/jwt**: Libreria complementaria para utilizar con **lexik/jwt-authentication-bundle** para proveer de muchísimos más encoders para los json web tokens a generar
- **jms/serializer-bundle**: Bundle que permite serializar nuestra data de salida en un formato personalizado (xml, json, entre otros).
- **friendsofsymfony/rest-bundle**: Bundle para convertir nuestros métodos dentro de los controladores, en recursos o servicios http (GET, POST, PUT, entre otros) utilizando anotaciones.
- **sensio/framework-extra-bundle**: Bundle necesario por FOSRestBundle para el soporte de rutas.
- **nelmio/api-doc-bundle**: Bundle para generar documentación de nuestro RESTful API.
- **Componente Twig de Symfony** (requerido para utilizar nelmio/api-doc-bundle)
- **Componente Asset de Symfony** (requerido para utilizar nelmio/api-doc-bundle)

Para instalar este listado de bundles y componentes ejecutaremos los siguientes comandos:

\# Receta para instalar symfony/orm-pack
composer req orm

\# Receta para instalar lexik/jwt-authentication-bundle:
composer req jwt-auth

\# Comando para instalar la libreria de lcobucci/jwt
composer require lcobucci/jwt

\# Receta para instalar jms/serializer-bundle:
# Este es un paquete contribuido, para poder usarlo hay que permitirle a 
# Symfony Flex para que lo use, si no lo hemos hecho anteriormente
composer config extra.symfony.allow-contrib true

# Luego ejecutar este comando
composer require jms/serializer-bundle

\# Receta para instalar friendsofsymfony/rest-bundle:
composer require friendsofsymfony/rest-bundle

\# Receta para instalar sensio/framework-extra-bundle:
composer require sensio/framework-extra-bundle

\# Receta para instalar nelmio/api-doc-bundle
composer require nelmio/api-doc-bundle

\# Receta para instalar el componente Twig de Symfony
composer req twig

\# Instalar componente Asset de Symfony
composer require symfony/asset

Hasta aquí hemos instalado todo lo necesario para seguir adelante con el desarrollo de nuestro **RESTful** **API**, pero seguramente ustedes se preguntarán, si **Symfony** instala un skeleton básico del framework, ¿En que momento se instalaron los componentes del framework necesarios para poder llevar a cabo nuestro proyecto?, por ejemplo el componente de routing, security, framework, entre otros. 

Aquí es donde entra en escena nuestro nuevo protagonista, **Symfony** **Flex**, ya que gracias a el y al uso de las recetas, es capaz de saber que componentes son requeridos por los bundles a instalar y se encarga de descargarlos, instalarlos y configurarlos por nosotros, aquí es donde hace magia **Symfony 4**.

Recordando un poco lo nuevo de Symfony 4 tenemos que la nueva estructura de directorios queda inicialmente de la siguiente manera:

├── bin/
│   └── console
├── config/
│   ├── autoload.php
│   ├── AppKernel.php
│   ├── config/
│   └── Resources/
│       └── views/
├── public/
├── src/ 
    ├── Controllers/ 
    └── Entity/
├── var/
│   ├── cache/
│   └── logs/
└── vendor/

Y luego al ir añadiendo código propio pueden crearse nuevos directorios como por ejemplo:

├── bin/
│   └── console
├── config/
│   ├── autoload.php
│   ├── AppKernel.php
│   ├── config/
│   └── Resources/
│       └── views/
├── public/
├── src/
    ├── Controllers/
    └── Entity/
├── templates/
├── translations/
├── var/
│   ├── cache/
│   └── logs/
└── vendor/

Otro aspecto a resaltar es que **Symfony** recomienda como buena práctica, poner toda la lógica de nuestra aplicación en el directorio de **src** y sin necesidad de crear un bundle base como anteriormente solíamos usar el famoso **AppBundle/**. De hecho ahora los namespaces dentro de este directorio estarán precedidos por **App\\**, Ejemplo: **App\\Controller**, **App\\Entity**.

Y por último y no menos importante, quiero recordar otro cambio significativo, y es que ahora nuestro controlador frontal fue renombrado a **index.php**, por lo que ya no existen los archivos **app.php** y **app\_dev.php**, que entre otras cosas, servía para diferenciar el entorno en el que estaríamos ejecutando nuestra aplicación.

Dicho esto, surge un nuevo archivo ubicado en la raíz del proyecto, llamado .env en el cual se definen, entre otras cosas, el entorno (**APP\_ENV**), nuestro hash secreto (**APP\_SECRET**), los datos de acceso a nuestra base de datos, entre otras cosas más.

## 3\. Creación de las entidades.

Ahora procederemos a crear las entidades necesarias para poder terminar de configurar y desarrollar nuestro **RESTful API** el cual proveerá un conjunto de endpoints básicos para simular nuestro tablero **Kanban**.

Antes de crear nuestras entidades, vamos a configurar nuestro proyecto para definir los datos de acceso a la base de datos que utilizaremos para el API. Para lograr esto, hay que editar el archivo **.env **ubicado en la raiz del proyecto.

Ubicamos el parámetro **DATABASE\_URL **y reemplazaremos los datos de acceso a nuestro servidor **MySQL** y el nombre de la **base de datos** a utilizar.

Originalmente el parámetro **DATABASE\_URL** viene por defecto de la siguiente manera:

DATABASE\_URL=mysql://db\_user:db\_password@127.0.0.1:3306/db\_name

En mi caso lo definí asi:

DATABASE\_URL=mysql://root:123456@127.0.0.1:3306/my\_kanban\_db

Seguidamente, ejecutamos el siguiente comando para decirle a doctrine que cree la base de datos en caso de que no la hayamos creado anteriormente:

php bin/console doctrine:database:create

Ahora podemos proceder a crear nuestras entidades y generar dicho esquema en la base de datos que vamos a utilizar para el proyecto. En el **post anterior**, diseñamos las entidades que usaremos en nuestra aplicación, retomando lo definido anteriormente, serian las siguientes:

- **User**: Almacenará los usuarios de la aplicación. 
- **Board: **Almacenará los tableros creados por cada usuario.
- **Task:** Almacenará las tareas definidas para cada board y el estado en que se encuentra cada una. 

Una vez implementada la estructura de datos diseñada previamente en archivos de clases para definir nuestras entidades, dejo a continuación el código de cada una. Vale la pena destacar que nuestras entidades deben residir en el directorio **src/Entity**

#### Entidad User.php:

<?php
/\*\*
 \* User.php
 \*
 \* User Entity
 \*
 \* @category   Entity
 \* @package    MyKanban
 \* @author     Francisco Ugalde
 \* @copyright  2018 www.franciscougalde.com
 \* @license    http://www.php.net/license/3\_0.txt  PHP License 3.0
 \*/

namespace App\\Entity;

use Doctrine\\Common\\Collections\\ArrayCollection;
use Doctrine\\ORM\\Mapping as ORM;
use DateTime;
use Symfony\\Component\\Security\\Core\\User\\UserInterface;
use JMS\\Serializer\\Annotation as Serializer;

/\*\*
 \* User
 \*
 \* @ORM\\Table(name="user");
 \* @ORM\\Entity(repositoryClass="App\\Repository\\UserRepository");
 \* @ORM\\HasLifecycleCallbacks()
 \*/
class User implements UserInterface
{
    /\*\*
     \* @ORM\\Column(name="id", type="integer")
     \* @ORM\\Id()
     \* @ORM\\GeneratedValue(strategy="AUTO")
     \*/
    protected $id;

    /\*\*
     \* @ORM\\Column(name="name", type="string", length=150)
     \*/
    protected $name;

    /\*\*
     \* @ORM\\Column(type="string", length=255, unique=true)
     \*/
    protected $email;

    /\*\*
     \* @ORM\\Column(name="username", type="string", length=255, unique=true)
     \*/
    protected $username;

    protected $salt;

    /\*\*
     \* @ORM\\Column(name="password", type="string", length=255)
     \* @Serializer\\Exclude()
     \*/
    protected $password;

    /\*\*
     \* @var string
     \*/
    protected $plainPassword;

    /\*\*
     \* @var array
     \*
     \* @ORM\\Column(name="roles", type="json\_array")
     \*/
    protected $roles = \[\];

    /\*\*
     \* @ORM\\Column(name="created\_at", type="datetime")
     \*/
    protected $createdAt;

    /\*\*
     \* @ORM\\Column(name="updated\_at", type="datetime")
     \*/
    protected $updatedAt;

    /\*\*
     \* @ORM\\OneToMany(targetEntity="App\\Entity\\Board", mappedBy="user")
     \*/
    protected $boards;

    public function \_\_construct()
    {
        $this->boards = new ArrayCollection();
    }

    /\*\*
     \* @return mixed
     \*/
    public function getId()
    {
        return $this->id;
    }

    /\*\*
     \* @return mixed
     \*/
    public function getName()
    {
        return $this->name;
    }

    /\*\*
     \* @param mixed $name
     \* @return self
     \*/
    public function setName($name)
    {
        $this->name = $name;

        return $this;
    }

    /\*\*
     \* Set email
     \*
     \* @param string $email
     \*
     \* @return User
     \*/
    public function setEmail($email)
    {
        $this->email = $email;

        return $this;
    }

    /\*\*
     \* Get email
     \*
     \* @return string
     \*/
    public function getEmail()
    {
        return $this->email;
    }

    /\*\*
     \* @return mixed
     \*/
    public function getUsername()
    {
        return $this->username;
    }

    /\*\*
     \* @param mixed $username
     \* @return self
     \*/
    public function setUsername($username)
    {
        $this->username = $username;

        return $this;
    }

    /\*\*
     \* @return mixed
     \*/
    public function getPassword()
    {
        return $this->password;
    }

    /\*\*
     \* @param mixed $password
     \* @return self
     \*/
    public function setPassword($password)
    {
        $this->password = $password;

        return $this;
    }

    /\*\*
     \* @return string
     \*/
    public function getPlainPassword()
    {
        return $this->plainPassword;
    }

    /\*\*
     \* @param $plainPassword
     \*/
    public function setPlainPassword($plainPassword)
    {
        $this->plainPassword = $plainPassword;

        $this->password = null;
    }

    /\*\*
     \* Set roles
     \*
     \* @param array $roles
     \*
     \* @return User
     \*/
    public function setRoles($roles)
    {
        $this->roles = $roles;

        return $this;
    }

    /\*\*
     \* Get roles
     \*
     \* @return array
     \*/
    public function getRoles()
    {
        return \["ROLE\_USER"\];
    }

    public function getSalt() {}

    public function eraseCredentials() {}

    /\*\*
     \* @return mixed
     \*/
    public function getCreatedAt()
    {
        return $this->createdAt;
    }

    /\*\*
     \* @param mixed $createdAt
     \* @return self
     \*/
    public function setCreatedAt($createdAt)
    {
        $this->createdAt = $createdAt;

        return $this;
    }

    /\*\*
     \* @return mixed
     \*/
    public function getUpdatedAt()
    {
        return $this->updatedAt;
    }

    /\*\*
     \* @param mixed $updatedAt
     \* @return self
     \*/
    public function setUpdatedAt($updatedAt)
    {
        $this->updatedAt = $updatedAt;

        return $this;
    }

    /\*\*
     \* @ORM\\PrePersist
     \* @ORM\\PreUpdate
     \*/
    public function updatedTimestamps()
    {
        $dateTimeNow = new DateTime('now');
        $this->setUpdatedAt($dateTimeNow);
        if ($this->getCreatedAt() === null) {
            $this->setCreatedAt($dateTimeNow);
        }
    }

    /\*\*
     \* @return mixed
     \*/
    public function getBoards()
    {
        return $this->boards->toArray();
    }

}

#### Entidad Board.php:

<?php
/\*\*
 \* Board.php
 \*
 \* Board Entity
 \*
 \* @category   Entity
 \* @package    MyKanban
 \* @author     Francisco Ugalde
 \* @copyright  2018 www.franciscougalde.com
 \* @license    http://www.php.net/license/3\_0.txt  PHP License 3.0
 \*/

namespace App\\Entity;

use Doctrine\\Common\\Collections\\ArrayCollection;
use Doctrine\\ORM\\Mapping as ORM;
use DateTime;
use JMS\\Serializer\\Annotation as Serializer;

/\*\*
 \* Board
 \*
 \* @ORM\\Table(name="board")
 \* @ORM\\Entity(repositoryClass="App\\Repository\\BoardRepository")
 \* @ORM\\HasLifecycleCallbacks()
 \*/
class Board
{

    /\*\*
     \* @ORM\\Column(name="id", type="integer")
     \* @ORM\\Id()
     \* @ORM\\GeneratedValue(strategy="AUTO")
     \*/
    protected $id;

    /\*\*
     \* @ORM\\Column(name="name", type="string", length=150)
     \*/
    protected $name;

    /\*\*
     \* @ORM\\ManyToOne(targetEntity="App\\Entity\\User", inversedBy="boards")
     \* @ORM\\JoinColumn(name="user\_id", referencedColumnName="id")
     \* @Serializer\\Exclude()
     \*/
    protected $user;

    /\*\*
     \* @ORM\\Column(name="created\_at", type="datetime")
     \*/
    protected $createdAt;

    /\*\*
     \* @ORM\\Column(name="updated\_at", type="datetime")
     \*/
    protected $updatedAt;

    /\*\*
     \* @ORM\\OneToMany(targetEntity="App\\Entity\\Task", mappedBy="board")
     \*/
    protected $tasks;

    public function \_\_construct()
    {
        $this->tasks = new ArrayCollection();
    }

    /\*\*
     \* @return mixed
     \*/
    public function getId()
    {
        return $this->id;
    }

    /\*\*
     \* @return mixed
     \*/
    public function getName()
    {
        return $this->name;
    }

    /\*\*
     \* @param mixed $name
     \* @return self
     \*/
    public function setName($name)
    {
        $this->name = $name;

        return $this;
    }

    /\*\*
     \* @return mixed
     \*/
    public function getUser()
    {
        return $this->user;
    }

    /\*\*
     \* @param mixed $user
     \* @return self
     \*/
    public function setUser($user)
    {
        $this->user = $user;

        return $this;
    }

    /\*\*
     \* @return mixed
     \*/
    public function getCreatedAt()
    {
        return $this->createdAt;
    }

    /\*\*
     \* @param mixed $createdAt
     \* @return self
     \*/
    public function setCreatedAt($createdAt)
    {
        $this->createdAt = $createdAt;

        return $this;
    }

    /\*\*
     \* @return mixed
     \*/
    public function getUpdatedAt()
    {
        return $this->updatedAt;
    }

    /\*\*
     \* @param mixed $updatedAt
     \* @return self
     \*/
    public function setUpdatedAt($updatedAt)
    {
        $this->updatedAt = $updatedAt;

        return $this;
    }

    /\*\*
     \* @ORM\\PrePersist
     \* @ORM\\PreUpdate
     \*/
    public function updatedTimestamps()
    {
        $dateTimeNow = new DateTime('now');
        $this->setUpdatedAt($dateTimeNow);
        if ($this->getCreatedAt() === null) {
            $this->setCreatedAt($dateTimeNow);
        }
    }

    /\*\*
     \* @return mixed
     \*/
    public function getTasks()
    {
        return $this->tasks;
    }
}

#### Entidad Task.php:

<?php
/\*\*
 \* Task.php
 \*
 \* Task Entity
 \*
 \* @category   Entity
 \* @package    MyKanban
 \* @author     Francisco Ugalde
 \* @copyright  2018 www.franciscougalde.com
 \* @license    http://www.php.net/license/3\_0.txt  PHP License 3.0
 \*/

namespace App\\Entity;

use Doctrine\\ORM\\Mapping as ORM;
use DateTime;
use JMS\\Serializer\\Annotation as Serializer;

/\*\*
 \* Task
 \* @ORM\\Table(name="task")
 \* @ORM\\Entity(repositoryClass="App\\Repository\\TaskRepository")
 \* @ORM\\HasLifecycleCallbacks()
 \*/
class Task
{
    /\*\*
     \* @ORM\\Column(name="id", type="integer")
     \* @ORM\\Id()
     \* @ORM\\GeneratedValue(strategy="AUTO")
     \*/
    protected $id;

    /\*\*
     \* @ORM\\Column(name="title", type="string", length=150)
     \*/
    protected $title;

    /\*\*
     \* @ORM\\Column(name="description", type="text")
     \*/
    protected $description;

    /\*\*
     \* @ORM\\ManyToOne(targetEntity="App\\Entity\\Board", inversedBy="tasks")
     \* @ORM\\JoinColumn(name="board\_id", referencedColumnName="id")
     \* @Serializer\\Exclude()
     \*
     \*/
    protected $board;

    /\*\*
     \* @ORM\\Column(name="status", type="string", length=50)
     \*/
    protected $status;

    /\*\*
     \* @ORM\\Column(name="priority", type="string", length=10)
     \*/
    protected $priority;

    /\*\*
     \* @ORM\\Column(name="created\_at", type="datetime")
     \*/
    protected $createdAt;

    /\*\*
     \* @ORM\\Column(name="updated\_at", type="datetime")
     \*/
    protected $updatedAt;

    /\*\*
     \* @return mixed
     \*/
    public function getId()
    {
        return $this->id;
    }

    /\*\*
     \* @return mixed
     \*/
    public function getTitle()
    {
        return $this->title;
    }

    /\*\*
     \* @param mixed $title
     \*/
    public function setTitle($title)
    {
        $this->title = $title;
    }

    /\*\*
     \* @return mixed
     \*/
    public function getDescription()
    {
        return $this->description;
    }

    /\*\*
     \* @param mixed $description
     \*/
    public function setDescription($description)
    {
        $this->description = $description;
    }

    /\*\*
     \* @return mixed
     \*/
    public function getBoard()
    {
        return $this->board;
    }

    /\*\*
     \* @param mixed $board
     \*/
    public function setBoard($board)
    {
        $this->board = $board;
    }

    /\*\*
     \* @return mixed
     \*/
    public function getStatus()
    {
        return $this->status;
    }

    /\*\*
     \* @param mixed $status
     \*/
    public function setStatus($status)
    {
        $this->status = $status;
    }

    /\*\*
     \* @return mixed
     \*/
    public function getPriority()
    {
        return $this->priority;
    }

    /\*\*
     \* @param mixed $priority
     \*/
    public function setPriority($priority)
    {
        $this->priority = $priority;
    }

    /\*\*
     \* @return mixed
     \*/
    public function getCreatedAt()
    {
        return $this->createdAt;
    }

    /\*\*
     \* @param mixed $createdAt
     \*/
    public function setCreatedAt($createdAt)
    {
        $this->createdAt = $createdAt;
    }

    /\*\*
     \* @return mixed
     \*/
    public function getUpdatedAt()
    {
        return $this->updatedAt;
    }

    /\*\*
     \* @param mixed $updatedAt
     \*/
    public function setUpdatedAt($updatedAt)
    {
        $this->updatedAt = $updatedAt;
    }

    /\*\*
     \* @ORM\\PrePersist
     \* @ORM\\PreUpdate
     \*/
    public function updatedTimestamps()
    {
        $dateTimeNow = new DateTime('now');
        $this->setUpdatedAt($dateTimeNow);
        if ($this->getCreatedAt() === null) {
            $this->setCreatedAt($dateTimeNow);
        }
    }

}

Lo siguiente a realizar es indicarle a Doctrine que reproduzca nuestra estructura de entidades en el esquema de nuestra base de datos, para esto, hay que ejecutar el siguiente comando:

php bin/console doctrine:schema:update --force

Ya con esto tenemos lista nuestra estructura de datos y podemos entonces enfocarnos en configurar e implementar nuestro **RESTful API**

Hasta aquí dejaré este post para no extenderlo demasiado. Puedes ir a Como construir un [**RESTful API con Symfony 4 + JWT - Parte 3**](https://www.franciscougalde.com/2018/02/19/construir-restful-api-symfony-4-jwt-parte-3/), para continuar con esta serie de post.

Si te gustó este post, ayúdame a que pueda servirle a muchas más personas, compartiendo mis contenidos en tus redes sociales.

Espero que este post haya sido de gran ayuda para ti, y como siempre, cualquier inquietud o duda que tengas, puedes contactarme por cualquiera de las vías disponibles, o dejando tus comentarios al final de este post. También puedes sugerir que temas o post te gustaría leer a futuro.

* * *

Si te gustó el post, regálame tus aplausos!!!

\[wp-applause-button style="width:60px;height:60px;margin: 0 auto;" color="black"\]
