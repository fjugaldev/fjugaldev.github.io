---
title: "Cómo configurar MongoDB en Symfony 4"
date: "2018-03-25"
---

En esta ocasión aprenderemos de forma rápida y sencilla como configurar básicamente **MongoDB** en **Symfony 4**.

> Este post asume que ya posees instalada la extensión de **MongoDB** (**ext-mongodb**) para PHP en tu servidor web.

Para conocer un poco más sobre esto, lee previamente los siguientes enlaces:

- [Instalar Extensión de MongoDB para PHP en macOS](http://php.net/manual/es/mongodb.installation.homebrew.php)
- [Instalar Extensión de MongoDB para PHP en Linux](http://php.net/manual/es/mongodb.installation.manual.php)
- [Instalar Extensión de MongoDB para PHP en Windows](http://php.net/manual/es/mongodb.installation.windows.php)

Para comenzar dividiré el post en tres secciones:

1. [Instalar MongoDB en macOS y otras plataformas.](#instalar-mongodb)
2. [Instalar Symfony 4 y paquetes necesarios](#instalar-symfony).
3. [Configurar MongoDB en Symfony 4.](#configurar-mongodb)

## Instalar MongoDB en macOS y otras plataformas.

Lo primero que debemos hacer es instalar nuestro servidor de **MongoDB**, para ello vamos a ver como instalarlo en **macOS** y otras plataformas.

### Pasos para instalar en macOS usando homebrew:

Procesamos a instalar el servidor de **MongoDB** en **macOS**.

> En mi [post anterior](https://www.franciscougalde.com/2018/03/10/homebrew-instalar-paquetes-en-macos-nunca-fue-tan-sencillo/), explicó como instalar este poderoso gestor de paquetes llamado **homebrew**, sin aún no lo tienes, échale un vistazo antes a mi [post](https://www.franciscougalde.com/2018/03/10/homebrew-instalar-paquetes-en-macos-nunca-fue-tan-sencillo/) para conocer como instalarlo.

Abrimos nuestro terminal y ejecutamos:

brew update

Al terminar, ejecutamos:

brew install mongodb

Lo siguiente será generar la ruta donde se almacenarán nuestros schemas:

sudo mkdir -p /data/db

y finalmente podemos ejecutar nuestro servidor de **MongoDB**:

sudo mongod

### Pasos para instalar en Linux:

[Click para ver documentación online.](https://docs.mongodb.com/manual/administration/install-on-linux/)

### Pasos para instalar en Windows:

[Click para ver documentación online.](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

## Instalar Symfony 4 y paquetes necesarios.

Una vez instalado el servidor de **MongoDB**, procederemos a crear nuestro proyecto en **Symfony** **4** e instalar los paquetes necesarios para integrar **MongoDB** en nuestra instalación.

Creamos un proyecto nuevo en **Symfony 4**:

composer create-project symfony/skeleton sf4-mongodb

Entramos en nuestro proyecto:

cd sf4-mongodb

Instalamos el Adaptador PHP para utilizar la extensión MongoDB en nuestra aplicación:

composer config "platform.ext-mongo" "1.6.16" && composer require alcaeus/mongo-php-adapter

Instalamos el bundle de **MongoDB**:

composer config extra.symfony.allow-contrib true

composer require doctrine/mongodb-odm-bundle

Instalamos los "**validators**" de **Symfony**:

composer req validation

Instalamos el componente "**Security**" de **Symfony**:

composer req security

## Configurar MongoDB en Symfony 4.

Hasta aquí tenemos todo lo necesario, solo nos falta unos pocos pasos para configurar nuestro proyecto y poder conectarnos a nuestro servidor de MongoDB.

Lo siguiente es editar nuestro archivo .env y establecer nuestra url por defecto al servidor de MongoDB, la cual podemos dejar tal como viene pre-configurado, y el nombre de nuestro schema (Base de datos) que estaremos utilizando en el proyecto. Nuestros parámetros a configurar son los siguientes:

MONGODB\_URL=mongodb://localhost:27017
MONGODB\_DB=sf4-mongodb

Ahora, crearemos nuestro primer documento ( que en MongoDB es el término utilizado para referirse a una tabla en modelos relacionales).

Crearemos el archivo **User.php** en el directorio "**src/Document**"

<?php
namespace App\\Document;

use Doctrine\\ODM\\MongoDB\\Mapping\\Annotations as MongoDB;
use Symfony\\Bridge\\Doctrine\\Validator\\Constraints\\UniqueEntity;
use Symfony\\Component\\Validator\\Constraints as Assert;
use Symfony\\Component\\Security\\Core\\User\\AdvancedUserInterface;

/\*\*
 \* @MongoDB\\Document()
 \* @UniqueEntity("username")
 \* @UniqueEntity("email")
 \*/

// If you want the User object to be serialized to the session, you need to implement Serializable
// https://symfony.com/doc/current/security/entity\_provider.html#what-do-the-serialize-and-unserialize-methods-do
class User implements AdvancedUserInterface
{
    const ROLE\_DEFAULT = 'ROLE\_USER';

    const ROLE\_SUPER\_ADMIN = 'ROLE\_SUPER\_ADMIN';

    /\*\*
     \* @MongoDB\\Id(strategy="auto")
     \*/
    protected $id;

    /\*\*
     \* @MongoDB\\Field(type="string")
     \* @Assert\\NotBlank()
     \*/
    protected $name;

    /\*\*
     \* @MongoDB\\Field(type="string")
     \* @Assert\\NotBlank()
     \*/
    protected $username;

    /\*\*
     \* @MongoDB\\Field(type="string")
     \* @Assert\\NotBlank()
     \*/
    protected $email;

    /\*\*
     \* @MongoDB\\Field(type="boolean")
     \*/
    protected $enabled;

    /\*\*
     \* @MongoDB\\Field(type="string")
     \*/
    protected $password;

    /\*\*
     \* @MongoDB\\Field(type="collection")
     \*/
    protected $roles;

    /\*\*
     \* User constructor.
     \*/
    public function \_\_construct()
    {
        $this->enabled = true;
        $this->roles = array();
    }

    /\*\*
     \*
     \* @param string $role
     \* @return $this
     \*/
    public function addRole($role)
    {
        $role = strtoupper($role);
        if ($role === static::ROLE\_DEFAULT) {
            return $this;
        }

        if (!in\_array($role, $this->roles, true)) {
            $this->roles\[\] = $role;
        }

        return $this;
    }

    /\*\*
     \* Get id
     \*
     \* @return id $id
     \*/
    public function getId()
    {
        return $this->id;
    }

    /\*\*
     \* Get name
     \*
     \* @return string $name
     \*/
    public function getName()
    {
        return $this->name;
    }

    /\*\*
     \* Get username
     \*
     \* @return string $username
     \*/
    public function getUsername()
    {
        return $this->username;
    }

    /\*\*
     \* Get password
     \*
     \* @return string $password
     \*/
    public function getPassword()
    {
        return $this->password;
    }

    /\*\*
     \* Get enabled
     \*
     \* @return boolean $enabled
     \*/
    public function isEnabled()
    {
        return $this->enabled;
    }

    /\*\*
     \* Get roles
     \*
     \* @return array $roles
     \*/
    public function getRoles()
    {
        $roles = $this->roles;

        // we need to make sure to have at least one role
        $roles\[\] = static::ROLE\_DEFAULT;

        return array\_unique($roles);
    }

    public function hasRole($role)
    {
        return in\_array(strtoupper($role), $this->getRoles(), true);
    }

    public function removeRole($role)
    {
        if (false !== $key = array\_search(strtoupper($role), $this->roles, true)) {
            unset($this->roles\[$key\]);
            $this->roles = array\_values($this->roles);
        }

        return $this;
    }

    /\*\*
     \* Set name
     \*
     \* @param string $name
     \* @return self
     \*/
    public function setName($name): self
    {
        $this->name = $name;

        return $this;
    }

    /\*\*
     \* Set username
     \*
     \* @param string $username
     \* @return self
     \*/
    public function setUsername($username): self
    {
        $this->username = $username;

        return $this;
    }

    /\*\*
     \* Set email
     \*
     \* @param string $email
     \* @return self
     \*/
    public function setEmail($email): self
    {
        $this->email = $email;

        return $this;
    }

    /\*\*
     \* Set password
     \*
     \* @param string $password
     \* @return self
     \*/
    public function setPassword($password): self
    {
        $this->password = $password;

        return $this;
    }

    /\*\*
     \* Set enabled
     \*
     \* @param boolean $enabled
     \* @return self
     \*/
    public function setEnabled($enabled): self
    {
        $this->enabled = (bool) $enabled;

        return $this;
    }

    /\*\*
     \* Set roles
     \*
     \* @param array $roles
     \* @return self
     \*/
    public function setRoles(array $roles): self
    {
        $this->roles = array();

        foreach ($roles as $role) {
            $this->addRole($role);
        }

        return $this;
    }

    public function getSalt()
    {
        return '';
    }

    public function eraseCredentials()
    {
        // TODO: Implement eraseCredentials() method.
    }

    public function isAccountNonExpired()
    {
        return true;
    }

    public function isAccountNonLocked()
    {
        return true;
    }

    public function isCredentialsNonExpired()
    {
        return true;
    }

    public function isSuperAdmin()
    {
        return $this->hasRole('ROLE\_SUPER\_ADMIN');
    }

    public function setSuperAdmin($boolean)
    {
        if (true === $boolean) {
            $this->addRole('ROLE\_SUPER\_ADMIN');
        } else {
            $this->removeRole('ROLE\_SUPER\_ADMIN');
        }

        return $this;
    }

    public function serialize()
    {
        return serialize(array(
            $this->password,
            $this->username,
            $this->enabled,
            $this->id,
            $this->email,
            $this->name,
        ));
    }

    public function unserialize($serialized)
    {
        $data = unserialize($serialized);

        list(
            $this->password,
            $this->username,
            $this->enabled,
            $this->id,
            $this->email,
            $this->name,
            ) = $data;
    }

    /\*\*
     \* @return string
     \*/
    public function \_\_toString()
    {
        return (string) $this->getUsername();
    }
}

Y ya podemos proceder a volcar nuestro documento "**User**" en nuestro servidor de **MongoDB**, para esto ejecutamos:

php bin/console doctrine:mongodb:schema:create

Y de esta manera, ya tenemos todo lo necesario para empezar a trabajar con **MongoDB en Symfony 4**. Para visualizar nuestro schema en el servidor de **MongoDB**, podemos instalar un IDE que nos permita conectarnos al servidor y poder ver y administrar nuestros schemas. Yo utilizo uno gratuito llamado [Robo 3T](https://robomongo.org/) el cual es para macOS y una vez instado solo basta con abrirlo y añadir un nuevo servidor en donde debemos colocar: localhost:27017 y listo.

Vale la pena destacar, que por defecto nuestro servidor MongoDB no viene con seguridad establecida, por lo que no requeriremos de un usuario y clave para conectarnos. Es aconsejable que posteriormente lo hagamos para proteger nuestro proyecto obviamente.

Si te gustó este post, ayúdame a que pueda servirle a muchas más personas, compartiendo mis contenidos en tus redes sociales.

Espero que este post haya sido de gran ayuda para ti, y como siempre, cualquier inquietud o duda que tengas, puedes contactarme por cualquiera de las vías disponibles, o dejando tus comentarios al final de este post. También puedes sugerir que temas o post te gustaría leer a futuro.

* * *

Si te gustó el post, regálame tus aplausos!!!

\[wp-applause-button style="width:60px;height:60px;margin: 0 auto;" color="black"\]
