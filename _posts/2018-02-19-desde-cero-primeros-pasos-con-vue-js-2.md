---
layout: post
title: "Desde cero: Primeros pasos con Vue.js 2"
author: fjugaldev
categories: [ Desarrollo Web ]
tags: [Desde Cero, Frameworks JS, Vue.js]
image: assets/images/posts/primeros-pasos-vue-js-2.jpg
description: "Aprende desde cero a iniciarte con Vue.js 2, conoce como instalar el framework, cual es la mejor manera de hacerlo, como se encuentra estructurado el proyecto"
featured: false
hidden: false
---

La intención de este post es introducirte paso a paso con el framework de javascript Vue.js, el cual ha venido creciendo y aumentando su popularidad día a día por su facilidad de uso, potencia y rendimiento.

## ¿Qué es Vue.js 2?

Como mencioné anteriormente, [Vue.js](https://vuejs.org/) es un framework de JavaScript  enfocado primordialmente en la construción de interfaces de usuarios. Debido a que se dedica a gestionar todo lo relacionado con la capa de la vista, se integra fácilmente con librerías de terceros. Vue.js es bastante ligero, solo pesa 17 kb lo que garantiza que al ser incorporado en nuestros proyectos no se ponga en riesgo la velocidad y el rendimiento de nuestra aplicación. Pero pese a su ligeresa, viene cargado de una gran cantidad de funcionalidad perfecta para crear aplicaciones de página simple (SPA: Single Page Applicacions). Algunas de las funcionalidades que nos ofrece son:

- Filtros
- Directivas
- Enlace de datos (data binding)
- Componentes
- Manejo de Eventos (event handling)
- Propiedades computadas
- Render declarativo
- Animaciones
- Transiciones
- Lógica en la plantilla

## ¿Cómo empezar con Vue.js?

Hay varias formas para usar este framework en nuestros proyectos, las más populares son:

- Usando el CLI (Command Line Interface).
- Instalándolo usando NPM (Node Package Manager).
- Insertando la librería de vue.js en una etiqueta <script>.

De estas tres opciones, la primera es la más robusta y flexible y pese a que no voy a profundizar sobre vue-cli (CLI de vue.js), voy a utilizar esta opción para enseñarte como empezar con vue.js.

### Vue CLI

Para poder usar el CLI tenemos que instalarlo en nuestro sistema. El CLI está disponible como un paquete en NPM, por lo tanto debemos tener instalado Node.js y NPM.

Para instalar el CLI usamos el siguiente comando en la consola:

```shell
sudo npm install -g vue-cli
```
>Nota: En linux o macOS es necesario usar sudo.

Luego de instalar el CLI de vue.js podemos iniciarlizar nuestro proyecto nuevo, para esto debemos situarnos en el directorio de nuestro servidor web y ejecutar:
```shell
vue init webpack mi_app_vue 
```
Este comando tiene 4 partes:

1. El comando **vue**, lo usamos para llamar el CLI de vue.js
2. **init**, estamos diciéndole al CLI que queremos inicializar un nuevo proyecto.
3. **webpack**, es la plantilla o arquitectura que queremos usar. Hay varias plantillas, más información al respecto más adelante
4. **mi_app_vue**, es el nombre que le queremos dar a nuestro proyecto

Para completar la creación del proyecto tenemos que contestar unas preguntas, estos son los datos que yo ingresé en las opciones de configuracion para nuestro proyecto de ejemplo:
```shell
? Project name mi_app_vue
? Project description Mi aplicación usando vue.js
? Author Francisco Ugalde
? Vue build standalone
? Install vue-router? No
? Use ESLint to lint your code? No
? Set up unit tests No
? Setup e2e tests with Nightwatch? No
? Should we run \`npm install\` for you after the project has been created? (recommended) npm

   vue-cli · Generated "mi_app_vue".

# Installing project dependencies ...
# ========================

> fsevents@1.1.3 install /Users/fjugaldev/Sites/mi_app_vue/node_modules/fsevents
> node install

\[fsevents\] Success: "/Users/fjugaldev/Sites/mi_app_vue/node_modules/fsevents/lib/binding/Release/node-v57-darwin-x64/fse.node" already installed
Pass --update-binary to reinstall or --build-from-source to recompile

> uglifyjs-webpack-plugin@0.4.6 postinstall /Users/fjugaldev/Sites/mi_app_vue/node_modules/webpack/node_modules/uglifyjs-webpack-plugin
> node lib/post_install.js

npm notice created a lockfile as package-lock.json. You should commit this file.
npm WARN ajv-keywords@3.1.0 requires a peer of ajv@^6.0.0 but none is installed. You must install peer dependencies yourself.

added 1259 packages in 63.797s

   ╭─────────────────────────────────────╮
   │                                     │
   │   Update available 5.4.0 → 5.6.0    │
   │     Run npm i -g npm to update      │
   │                                     │
   ╰─────────────────────────────────────╯

# Project initialization finished!
# ========================

To get started:

  cd mi_app_vue
  npm run dev

Documentation can be found at https://vuejs-templates.github.io/webpack
```
> Nota: En unas de las opciones nos pregunta si queremos que ejecute el comando “npm install”, aquí debemos elegir la opción **NPM** para tal procedimiento.

Seguido a esto, nos situamos sobre el directorio de nuestro proyecto y podemos ejecutar nuestra aplicación en un servidor de desarrollo con el siguiente comando:
```shell
npm run dev
```
En la terminal (o consola) vamos a ver el siguiente mensaje:
```shell
DONE  Compiled successfully in 3553ms                                                                                                        6:07:20 PM

I  Your application is running here: http://localhost:8080
```
Ya con esto nuestra aplicación esta servida en la ruta http://localhost:8080 y nos mostrará lo siguiente:

![Vue.js](/assets/images/posts/Screen-Shot-2018-02-16-at-18.08.51.png)

## Estructura de nuestro proyecto en Vue.js

La estructura básica de un proyecto con vue.js es la siguiente:
```yaml
├── build/
├── config/
├── node_modules/
├── src/
├── static/
├── index.html
├── package-lock.json
├── package.json
├── README.md
```
Te explicaré de forma sencilla los archivos más importantes a tomar en cuenta:

### index.html

Este archivo es el punto de inicio a nuestra aplicación, el código contenido es el siguiente:
```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width,initial-scale=1.0">
    <title>mi_app_vue</title>
  </head>
  <body>
    <div id="app"></div>
    <!-- built files will be auto injected -->
  </body>
</html>
```
Como podemos observar, en el se incluye un div con el id “app”, este div es donde se va a incluir posteriormente el codigo html generado por vue.js.

### src/main.js

Aquí es donde se define e inicializa nuestra aplicación, el código contenido en este archivo es:
```javascript
// The Vue build version to load with the \`import\` command
// (runtime-only or standalone) has been set in webpack.base.conf with an alias.
import Vue from 'vue'
import App from './App'

Vue.config.productionTip = false

/* eslint-disable no-new */
new Vue({
  el: '#app',
  components: { App },
  template: '<App/>'
})
```
En la parte superior de este archivo podemos ver que se importan dos clases, una es la clase principal del framework (Vue) y la otra (App) es el nombre del componente raiz de nuestra aplicación.

Posteriormente instanciamos un nuevo objeto de la clase Vue y la inicializamos pasandole un objeto a su construtor. Las propiedades de este objeto son 3 y las describo a continuación:

1. **el**: Representa el id del elemento en donde queremos que se incluya el contenido de nuestra apliación
2. **template**: Es la etiqueta HTML que define nuestra aplicación, es decir el contenido html que queremos mostrar al usuario. En este caso podemos observar que se definió la etiqueta “<App/>, esto quiere decir que se va a incluir el contenido de la plantilla (template) del componente App.vue de nuestra aplicación.
3. **components**: La lista de componentes que son necesarios en la plantilla (template).

### src/App.vue

En este archivo se define el componente “App” de nuestra aplicación, vamos a detallar el código contenido en el:
```vue
<template>
  <div id="app">
    <img src="./assets/logo.png">
    <HelloWorld/>
  </div>
</template>

<script>
import HelloWorld from './components/HelloWorld'

export default {
  name: 'App',
  components: {
    HelloWorld
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
```
Este tipo de archivo se conoce como un componente de archivo simple (single-file component) porque definimos el html, js y css en el mismo archivo en las secciones template, script y style respectivamente.

En la seccion “template” definimos el código HTML que va a representar el componente en nuestra página. Luego definimos la sección “script”, en la cual se define un objeto, “default”, el cual se exporta para poder ser usado en nuestra aplicación. Este objeto “default” tiene por los momentos solo 2 propiedades:

**name**: el nombre del id sobre el cual este componente va a actuar. En este caso es ‘app’ y en la sección template podemos ver como tenemos un div con el mismo id. El componente va a tener acceso a todo el div.

**components**: los componentes que el componente actual (en este caso el componente raíz) necesita (Dependencias).

De la misma forma que lo vimos con el archivo main.js, en App.vue estamos diciendo que necesitamos el componente “HelloWorld.vue” dentro del componente App. Este subcomponente lo podemos encontrar en la carpeta components:

### HelloWorld.vue
```vue
<template>
  <div class="hello">
    <h1>{% raw %}{{ msg }}{% endraw %}</h1>
    <h2>Essential Links</h2>
    <ul>
      <li>
        <a
          href="https://vuejs.org"
          target="_blank"
        >
          Core Docs
        </a>
      </li>
      <li>
        <a
          href="https://forum.vuejs.org"
          target="_blank"
        >
          Forum
        </a>
      </li>
      <li>
        <a
          href="https://chat.vuejs.org"
          target="_blank"
        >
          Community Chat
        </a>
      </li>
      <li>
        <a
          href="https://twitter.com/vuejs"
          target="_blank"
        >
          Twitter
        </a>
      </li>
      <br>
      <li>
        <a
          href="http://vuejs-templates.github.io/webpack/"
          target="_blank"
        >
          Docs for This Template
        </a>
      </li>
    </ul>
    <h2>Ecosystem</h2>
    <ul>
      <li>
        <a
          href="http://router.vuejs.org/"
          target="_blank"
        >
          vue-router
        </a>
      </li>
      <li>
        <a
          href="http://vuex.vuejs.org/"
          target="_blank"
        >
          vuex
        </a>
      </li>
      <li>
        <a
          href="http://vue-loader.vuejs.org/"
          target="_blank"
        >
          vue-loader
        </a>
      </li>
      <li>
        <a
          href="https://github.com/vuejs/awesome-vue"
          target="_blank"
        >
          awesome-vue
        </a>
      </li>
    </ul>
  </div>
</template>

<script>
export default {
  name: 'HelloWorld',
  data () {
    return {
      msg: 'Welcome to Your Vue.js App'
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h1, h2 {
  font-weight: normal;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
```
Este archivo también representa una plantilla de archivo simple con las 3 secciones tal cual como las describimos anteriormente: template, script y style.

Y de la misma forma estamos exportando, en la sección script, el objeto de configuración del componente. En el componente **HelloWorld** vemos definido un método llamado “data()”. Este método retorna un objeto el cual representa el modelo del componente. Propiedades definidas en el modelo pueden ser usadas en el template del componente usando interpolación ({% raw %}{{ }}{% endraw %}). Este modelo solo tiene una propiedad, llamada msg. Y estamos mostrando el contenido de esta variable en el template usando interpolación:
```html
<h1>{% raw %}{{ msg }}{% endraw %}</h1>
```
La interpolación requiere el uso de llaves ({% raw %}{{ }}{% endraw %}) dobles con el nombre de la propiedad que deseamos mostrar en nuestro template.

En las sección style,  podemos notar que se añade la opción “scoped”. Scoped se refiera a que todo el CSS que se defina en la sección styles será utilizado únicamente en la plantilla actual y no va a afectar a otras plantillas existentes.

Hasta aquí dejo estos primeros pasos para empezar a involucrarnos con este maravilloso framework de javascript llamado Vue.js. Un framework que como comenté anteriormente, promete muchisimo y que no debemos dejar pasar por alto puesto que se encuentra en un crecimiento constante y a pasos agigantados.

Adicionalmente todo el código generado puedes clonarlo desde [mi repositorio](https://github.com/fjugaldev/primeros-pasos-vuejs-2) en github.

En próximos post estaré abarcando otros aspectos importantes del framework como por ejemplo directivas, routing, templating, filtros, componentes, entre otros.

Si te gustó este post, ayúdame a que pueda servirle a muchas más personas, compartiendo mis contenidos en tus redes sociales.

Espero que este post haya sido de gran ayuda para ti, y como siempre, cualquier inquietud o duda que tengas, puedes contactarme por cualquiera de las vías disponibles, o dejando tus comentarios al final de este post. También puedes sugerir que temas o post te gustaría leer a futuro.

* * *
