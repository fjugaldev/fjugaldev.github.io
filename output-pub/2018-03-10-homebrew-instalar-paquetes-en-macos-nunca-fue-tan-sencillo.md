---
title: "Homebrew: Instalar paquetes en macOS nunca fué tan sencillo"
date: "2018-03-10"
---

Para los que no conocen **Homebrew**, una potente y sensacional herramienta, déjenme decirles que se están complicando la vida de gratis. Y es que yo lo hacia también años atrás, era de los que prefería instalar cada paquete, librería, aplicación manualmente o desde la web del autor.

A la larga me di cuenta que mantener actualizadas todas las aplicaciones o paquetes que tenia instalado se volvió algo super engorroso por lo que decidí investigar y ver si existía algún gestor de paquetes para **macOS** tal como existen en **Linux**. Fué entonces cuando descubrí a **Homebrew **y gracias a esto, mis días ahora son mas felices.

## ¿Cómo instalar Homebrew?

El primer paso es instalar desde la terminal, las **Command Line Tools (CLT) de Xcode** utilizando el siguiente comando:

xcode-select –install

Una vez terminada la instalación de las **Command Line Tools**, debemos ejecutar el siguiente comando para descargar e instalar **Homebrew**:

ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"

Y de la misma forma que en los sistemas basados en GNU/Linux, tenemos que mantener actualizado los repositorios y sus paquetes, para ello utilizamos el siguiente comando:

brew update

A continuación algunos comandos más utilizados y que serán los que debes tener a la mano puesto que serán los que quizás utilizarás mas a menudo:

## Paquetes instalados que necesitan ser actualizados:

brew outdated

## Actualizar todos los paquetes que necesiten ser actualizados:

brew upgrade

## Actualizar un paquete en especifico que necesite ser actualizado:

brew upgrade nombre-del-paquete

## Instalar un paquete nuevo:

brew install nombre-del-paquete

## Desinstalar un paquete:

brew uninstall nombre-del-paquete

## Buscar parquete dentro de los repositorios de Homebrew:

brew search nombre-del-paquete

Para obtener mas información sobre Homebrew, puedes dirigirte al siguiente enlace: [https://brew.sh/index\_es.html](https://brew.sh/index_es.html)

Espero que te haya gustado esta maravillosa herramienta, estoy seguro de que sera parte de tu pool de herramientas a utilizar a diario.

Si te gustó este post, ayúdame a que pueda servirle a muchas más personas, compartiendo mis contenidos en tus redes sociales.

Espero que este post haya sido de gran ayuda para ti, y como siempre, cualquier inquietud o duda que tengas, puedes contactarme por cualquiera de las vías disponibles, o dejando tus comentarios al final de este post. También puedes sugerir que temas o post te gustaría leer a futuro.

* * *

Si te gustó el post, regálame tus aplausos!!!

\[wp-applause-button style="width:60px;height:60px;margin: 0 auto;" color="black"\]
