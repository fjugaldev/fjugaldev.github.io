---
title: "DESDE CERO: Como crear un componente en Vue.js"
date: "2018-03-04"
---

## Vue JS - Conceptos

**Vue.js** implementa una forma interesante de agrupar nuestras funcionalidades en los bien llamados "**componentes**" para de esta manera poder reutilizarlos en cualquier parte de nuestra aplicación.

Dicho de otra forma, el sistema de componentes en **Vue.js** es una abstracción que nos permite construir aplicaciones de gran escala compuestas por componentes pequeños, autocontenidos y, normalmente, reutilizables. 

Pensando un poco en esto último podemos decir que la interfaz gráfica de nuestra aplicación puede estar representada de manera abstracta como un árbol de componentes.

Es decir, nuestra interfaz gráfica puede, o se recomienda, segmentarla en un conjunto de componentes, por ejemplo, crear un componente para: formulario de login, sidebar colapsable, mapa con marcadores dinámicos, entre otros.

En **Vue.js**, un componente esencialmente se crea como instancia de la librería principal de **Vue** la cual incorpora opciones predefinidas.

Para definir un componente basta con escribir lo siguiente:

// Define un nuevo componente llamado todo-tasks
Vue.component('todo-tasks', {
  template: '<li>Esto es una tarea</li>'
})

Con esto podríamos fácilmente incluir nuestro componente en el HTML, por ejemplo:

<ul>
  <!-- Crea una instancia del componente todo-taks -->
  <todo-tasks></todo-tasks>
</ul>

Para entender mejor como trabajar con los componentes, como funcionan y donde va cada cosa, y sobre todo, como hacerlos dinámicos, vamos a realizar un pequeño ejemplo desde cero. Pero antes procedo a explicar como se define y las propiedades que contiene.

Para definir un componente basta con escribir lo siguiente:

Vue.component('nombre-componente');

Seguido a esto debemos indicar sobre que elemento se va a 

Lo primero que debemos hacer es incluir **Vue.js** en nuestro documento index.html:

## index.html

<!DOCTYPE html>
<html>
<head>
  <title>Desde Cero: Cromo crear un componente en Vue.js</title>
  <!-- Incluimos Vue.js desde CDN -->
  <script src="https://cdn.jsdelivr.net/npm/vue"></script>
</head>
<body>
</body>
</html>

Luego inicializamos nuestra aplicación, para ello creamos un elemento cuyo id será "**app**" y luego instanciamos la clase **Vue** indicándole mediante el parámetro "el" en donde se va a ejecutar nuestra aplicación:

<!DOCTYPE html>
<html>
<head>
  <title>Desde Cero: Cromo crear un componente en Vue.js</title>
  <!-- Incluimos Vue.js desde CDN -->
  <script src="https://cdn.jsdelivr.net/npm/vue"></script>
</head>
<body>
  <div id="app"></div>
  <script>
    var app = new Vue({
      el: '#app',
    });
  </script>
</body>
</html>

Como pueden ver, el parametro "**el**" indica que nuestra aplicación se va a ejecutar en un elemento cuyo id sea "**app**".

Ya con esto tenemos todo listo para empezar a crear e implementar nuestro componente. Para ello debemos definirlo de la siguiente manera:

// Define un nuevo componente llamado newsletter-users
Vue.component('newsletter-users', {});

Este componente sencillamente va a simular el despliegue de una lista de correos de los usuarios suscritos al newsletter.

Lo siguiente a que haremos es definir el template que va a tener nuestro componente y una propiedad que se encargará de recibir los datos para su despliegue. Quedaría entonces de la siguiente manera:

Vue.component('newsletter-users', {
  props: \['email'\],
  template: '<li>{{ email.text }}</li>'
})

Otro de los parámetros que pueden definirse es "**data()**" el cual es una función que permite devolver estructura de datos para ser usados como variables dentro de nuestro componente.

Para este ejemplo, vamos a utilizar este parámetro para simular ciertos registros de usuarios registrados en el newsletter:

Vue.component('newsletter-users', {
  props: \['email'\],
  template: '<li>{{ email.text }}</li>',
  data: {
    registeredUsers: \[
      { text: 'usuario1@franciscougalde.com' },
      { text: 'usuario2@franciscougalde.com' },
      { text: 'usuario3@franciscougalde.com' },
      { text: 'usuario4@franciscougalde.com' },
      { text: 'usuario5@franciscougalde.com' }
    \]
  }
})

Y ya con esto podemos utilizar nuestro nuevo componente "**newsletter-users**" en nuestra aplicación, para ello hacemos lo siguiente:

<div id="app">
  <ul>
    <!-- Ahora le pasamos a cada newsletter-users la propiedad email     -->
    <!-- que representa un objeto cuyo contenido dinámico proviene del array 
         de objetos registerdUsers -->
    <newsletter-users v-for="user in registerdUSers" v-bind:email="user"></newsletter-users>
  </ul>
</div>

Como podrán notar vemos varias cosas importantes que para el ejemplo tuve que utilizar pero que no lo he explicado anteriormente:

- **v-for**: permite iterar arreglos de contenido y repetir elementos según la dimención de nuestro arreglo.
- **v-bind**: Se encarga de asignarle contenido en este caso a nuestra propiedad email que reside dentro de nuestro componente, con uno de los items de nuestro arreglo registeredUsers.

Quedando entonces nuestro código completo de la siguiente forma:

<!DOCTYPE html>
<html>
<head>
  <title>Desde Cero: Cromo crear un componente en Vue.js</title>
  <!-- Incluimos Vue.js desde CDN -->
  <script src="https://cdn.jsdelivr.net/npm/vue"></script>
</head>
<body>
  <div id="app">
    <ul>
      <!-- Ahora le pasamos a cada newsletter-users la propiedad email     -->
      <!-- que representa un objeto cuyo contenido dinámico proviene del array 
           de objetos registerdUsers -->
      <newsletter-users v-for="user in registeredUsers" v-bind:email="user"></newsletter-users>
    </ul>
  </div>
  <script>
    Vue.component('newsletter-users', {
      props: \['email'\],
      template: '<li>{{ email.text }}</li>'
    })
    
    var app = new Vue({
      el: '#app',
      data: {
        registeredUsers: \[
          { text: 'usuario1@franciscougalde.com' },
          { text: 'usuario2@franciscougalde.com' },
          { text: 'usuario3@franciscougalde.com' },
          { text: 'usuario4@franciscougalde.com' },
          { text: 'usuario5@franciscougalde.com' }
        \]
      }
    });
  </script>
</body>
</html>

A continuación, veamos como funciona:

Si te gustó este post, ayúdame a que pueda servirle a muchas más personas, compartiendo mis contenidos en tus redes sociales.

Espero que este post haya sido de gran ayuda para ti, y como siempre, cualquier inquietud o duda que tengas, puedes contactarme por cualquiera de las vías disponibles, o dejando tus comentarios al final de este post. También puedes sugerir que temas o post te gustaría leer a futuro.

* * *

Si te gustó el post, regálame tus aplausos!!!

\[wp-applause-button style="width:60px;height:60px;margin: 0 auto;" color="black"\]
