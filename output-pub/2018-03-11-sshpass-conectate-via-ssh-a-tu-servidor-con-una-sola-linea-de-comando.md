---
title: "sshpass: conéctate vía ssh a tu servidor con una sola línea de comando"
date: "2018-03-11"
---

A menudo, los que nos dedicamos al desarrollo web, necesitamos conectarnos por **ssh** a los servidores para realizar alguna tarea en particular. Y no solamente a uno, si no a varios, lo que puede convertirse en una tarea rutinaria y que muchos casos llega a cansar el hecho de  tener que aprendernos claves, hosts, entre otras cosas.

Particularmente, en la empresa que trabajo, poseemos varios servidores a los cuales debo diariamente conectarme a cada uno para actualizar el código desde el repositorio git, revisar procesos en el servidor para unos cronjobs que deben ejecutarse, verificar logs y pare de contar.

Es por todo esto y más que hoy quiero mostrarles una herramienta que estoy seguro les será, al igual que a mi, de muchísima utilidad. Se llama "**sshpass**" y permite incluir en la misma linea de comando, la clave de acceso vía ssh a un servidor.

Comenzaré explicando como instalar la herramienta. Para ello debemos ejecutar el siguiente comando en nuestra terminal:

### Instalación en macOS

brew install https://raw.githubusercontent.com/kadwanev/bigboybrew/master/Library/Formula/sshpass.rb

**Nota: **Debes tener instalado Homebrew para poder instalarlo, si no aún no lo tienes, revisa mi post: [**Homebrew: Instalar paquetes en macOS nunca fué tan sencillo**](https://www.franciscougalde.com/?p=628&preview=true)

### Instalación en Linux

sudo apt-get install sshpass

Generalmente, para conectarnos vía ssh a un servidor ejecutamos un comando parecido a esto:

ssh usuario@host.com

En el que luego nos pedirá la clave de acceso para poder conectarnos finalmente al servidor.

Con **sshpass**, la cosa cambia y es que ahora podemos incluir la contraseña en la misma linea de comando para conectarnos, para esto, debemos ejecutar lo siguiente:

sshpass -p 'PASSWORD' ssh usuario@host.com 

Y ya con esto, estaremos dentro del servidor sin tantas complicaciones.

Ahora bien, de seguro te preguntarás, y en que ayuda esto en sí si debo de igual forma memorizar miles de passwords para cada servidor?, aquí es donde viene lo interesante y es que podemos combinar esto con los alias de nuestro shell.

Lo que yo hago es crear alias de este comando en mi **shell** para que luego con tan solo ejecutar un comando que me ayude a recordar al servidor al que deseo conectarme, este alias sea traducido en la linea de comando de acceso al servidor, incluyendo ya el password de acceso:

En mi caso utilizo **macOS** y el shell '**ZSH**' por lo que podemos hacer lo siguiente:

## Crear alias para el acceso ssh al servidor usando sshpass

Editamos el archivo ~/.zshrc :

sudo vim ~/.zshrc

Creamos los respectivos alias al final del archivo:

alias server1='sshpass "PASSWORD\_1" ssh usuario1@servidor1.com'
alias server2='sshpass "PASSWORD\_2" ssh usuario1@servidor2.com'
alias server3='sshpass "PASSWORD\_3" ssh usuario1@servidor3.com'

Guardamos  y luego ejecutamos:

source ~/.zshrc

Ahora basta con escribir en la terminal cualquiera de los 3 alias, **server1**, **server2**, o **server3**, nos estaremos conectando a cada uno respectivamente sin necesidad de colocar usuarios, passwords y hosts. Esto es solo un ejemplo, podemos crear cualquier cantidad de alias para cada uno de los servidores a los que diariamente debamos acceder y queramos ahorrar tiempo y evitar tener que recordar tantos datos para acceder.

Si te gustó este post, ayúdame a que pueda servirle a muchas más personas, compartiendo mis contenidos en tus redes sociales.

Espero que este post haya sido de gran ayuda para ti, y como siempre, cualquier inquietud o duda que tengas, puedes contactarme por cualquiera de las vías disponibles, o dejando tus comentarios al final de este post. También puedes sugerir que temas o post te gustaría leer a futuro.

* * *

Si te gustó el post, regálame tus aplausos!!!

\[wp-applause-button style="width:60px;height:60px;margin: 0 auto;" color="black"\]
