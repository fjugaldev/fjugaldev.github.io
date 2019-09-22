---
layout: post
title: "Elasticsearch como arquitectura para proyectos Big Data"
author: fjugaldev
categories: [ Desarrollo Web ]
tags: [API, Herramientas, REST, RESTFful]
image: assets/images/posts/postman-featured.jpg
description: "Conoce y aprende sobre esta poderosa herramienta para gestión y construcción de API´s. Postman, una herramienta que amaras y se convertirá en tu mejor"
featured: false
hidden: false
---

Hoy en día, el Big Data, término que explicaré más adelante, nos pone en frente grandes retos a la hora de estructurar nuestros proyectos, y que indudablemente sin la ayuda de herramientas como Elasticsearch, sería aún más dificil.

En este post, conoceremos un poco sobre lo que significa el Big Data y su importancia, y sobre una herramienta que nos brinda la posibilidad de implementar una estructura de datos adaptada hacia el Big Data.

Conoceremos también entre otras cosas, las características de Elasticsearch, ventajas de su uso y porque elegirlo como primera opción para nuestros proyectos Big Data.

## ¿Que es Big Data?

Big Data es el término que se utiliza para describir un gran volumen de datos, tanto estructurados como no estructurados, los cuales ahogan a las empresas cada día. Pero realmente no es la cantidad de datos lo que importa, si no lo que se hace con estos. Big Data es capaz de analizar esta data para obtener ideas que conduzcan a la toma de mejores decisiones y estrategias de negocio exitosas.

Cuando hablamos de Big Data nos referimos a conjuntos de datos o combinaciones de conjuntos de datos cuyo tamaño (volumen), complejidad (variabilidad) y velocidad de crecimiento (velocidad) son dificiles de capturar, gestionar, procesar y sobre todo analizar mediante tecnologías y herramientas convencionales (Bases de datos relacionales, paquetes de visualización de datos, entre otros.

Aunque el tamaño utilizado para determinar si un conjunto de datos determinado se considera Big Data no está firmemente definido y sigue cambiando con el tiempo, **la mayoría de los analistas y profesionales actualmente se refieren a conjuntos de datos que van desde 30-50 Terabytes a varios Petabytes**.

La complejidad del Big Data se debe principalmente a la gran cantidad de datos no estructurados generados constantemente por las tecnologías modernas, como los web logs, sensores incorporados en dispositivos, las busquedas en internet, las redes sociales (tales como Facebook, Twitter, entre otras), computadoras portátiles, smartphones, entre otras mas.

## ¿Por qué es tan importante?

Lo que hace que Big Data sea tan útil para muchas empresas es el hecho de que proporciona respuestas a muchas preguntas que las empresas ni siquiera sabían que tenían. En otras palabras, proporciona un punto de referencia. Con una cantidad tan grande de información, los datos pueden ser moldeados o probados de cualquier manera que la empresa considere adecuada. Al hacerlo, las organizaciones son capaces de identificar los problemas de una forma más comprensible.

La recopilación de grandes cantidades de datos y la búsqueda de tendencias dentro de los datos permiten que las empresas se muevan mucho más rápidamente, sin problemas y de manera eficiente.

**El análisis de Big Data ayuda a las organizaciones a aprovechar sus datos y utilizarlos para identificar nuevas oportunidades**. Eso, a su vez, conduce a movimientos de negocios más inteligentes, operaciones más eficientes, mayores ganancias y clientes más felices. Las empresas con más éxito con Big Data consiguen valor de las siguientes formas:

- **Reducción de coste**. Las grandes tecnologías de datos, como Elasticsearch, aportan importantes ventajas en términos de costes cuando se trata de almacenar grandes cantidades de datos, además de identificar maneras más eficientes de hacer negocios.
- **Más rápido, mejor toma de decisiones**. Con la velocidad de [E](https://www.powerdata.es/el-valor-de-la-gestion-de-datos/bid/402826/5-ventajas-de-la-arquitectura-de-Hadoop)lasticsearch y la analítica en memoria, combinada con la capacidad de analizar nuevas fuentes de datos, las empresas pueden analizar la información inmediatamente y tomar decisiones basadas en lo que han aprendido.
- **Nuevos productos y servicios**. Con la capacidad de medir las necesidades de los clientes y la satisfacción a través de análisis viene el poder de dar a los clientes lo que quieren. Con la analítica de Big Data, más empresas están creando nuevos productos para satisfacer las necesidades de los clientes.

## ¿Que es Elasticsearch?

Elasticsearch es un motor de búsqueda orientado a documentos **JSON** estructurados sin **schemas**, desarrollado en JAVA de [código abierto](https://github.com/elastic/elasticsearch), una de las principales características es que nos permite tener una arquitectura distribuida, escalable y de alta disponibilidad, **ES** se basa en Lucene para las búsqueda de texto. Las búsquedas el cual soporta multi idioma, **_geolocalización_**, **_autocompletado_**, **_sugerencias_**. ideal para los proyectos en donde trabajemos con **big data**.

## Características de Elasticsearch

- Motor de búsqueda OpenSource.
- Está escrito en Java.
- Basado en Apache Lucene.
- Permite Arquitectura: distribuida, escalable, en alta disponibilidad.
- Capacidades de búsqueda y análisis en tiempo real mediante peticiones GET.
- Sus documentos se almacenan en formato JSON.
- Útil para soluciones NoSQL (sin transacciones distribuidas).
- Permite utilizarlo sobre el ecosistema Hadoop para proyectos de BigData.
- API RESTfull sobre http para consulta, indexación, administración en diferentes lenguajes.
- Permite múltiples índices en un cluster, así como alias de índices.
- Permite consultas sobre uno o varios índices.
- Full Text Search o búsqueda por texto completo.
- Indexa todos los campos de los documentos JSON (sin esquema rígido).
- Documentos con posibilidad de poseer distintas estruturas de datos.
- Búsquedas mediante ElasticSearch Query DSL (Domain Specific Language): multilenguaje, geolocalización, contextual, autocompletar, etc.

## Ventajas de usar Elasticsearch

- **Datos en tiempo real**: obtención de datos de forma rápida e instantánea.
- **Análisis de datos**: además de la función de búsqueda, Elasticsearch te permite analizar los datos de forma visual mediante gráficas que te ayudarán a mejorar diferentes aspectos de tu negocio. Adicionalmente existen diversos plugins que harán la tarea del analisis de datos, algo simple y agradable.
- **Sistema distribuido**: los datos se clasifican en diferentes sistemas que colaboran entre sí y nos muestran los resultados que les demandamos en cada momento en una sola petición.
- **Multi-tenencia de datos**: Elasticsearch, mediante la indexación, nos permite llegar a los datos que necesitamos directamente, sin tener que pasar por todas las ubicaciones en las que se almacenan los datos. Gracias al uso de índices, la herramienta nos muestra los datos atendiendo a diferentes agrupaciones o criterios o de forma independiente.
- Esta herramienta te ayudará a **optimizar la arquitectura de datos** de tu empresa.

## ¿Por qué utilizar Elasticsearch como arquitectura para proyectos de Big Data?

A diferencia de otros motores de búsqueda y recuperación de información, **Elasticsearch** actua como repositorio de información, almacenando los documentos (rows) que indexa, esto permite brindar mayor rendimiento y optimización de la estructura de datos.

Las Bases de Datos relacionales (SQL) como bien sabemos, requieren definir las tablas o esquema de la información, antes de poder introducir registros de datos. Adicionalmente es necesario crear las relaciones entre las tablas para poder garantizar integridad de datos en todo momento.

Esto hace que la tarea de diseñar, normalizar e implementar la estructura de datos, antes de empezar a desarrollar o implementar nuestro código, se plantee con muchisima cautela y pensando muy bien desde todos los escenarios posibles, ya que un cambio a medio camino puede reprensentar una migración de datos a otro esquema o tabla, adaptar nuevos esquemas, entre otros, y todo esto teniendo en cuenta que estarémos manipulando grandes volúmenes de datos lo cual se puede tornar en algo extremadamente lento y costoso para la organización.

Sin embargo, como **Elasticsearch** no requiere de un esquema predefinido de los datos, la misma colección de documentos puede contender contener documentos con estructura de datos distintas, lo cual brinda un desarrollo más ágil, flexible y escalabe en el tiempo.

Otro aspecto interesante es que Elasticsearch almacena sus documentos en formato JSON lo cual representa estructuras de datos que se asemejan muchisimo a las entidades que se manejan a nivel de negocio.

Y por último y no menos importante, **Elasticsearch** provee de un RESTful API con toda la funcionalidad de su motor de búsqueda lo cual se torna ideal para realizar integraciones con otras tecnologías que puedan estar presente en nuestro proyecto.

Dejemos a un lado toda la teoría y vamos a ver un poco más sobre la parte técnica y sobre como instalar y probar Elasticsearch de forma sencilla.

## Arquitectura de Elasticsearch

Para entender un poco como funciona **Elasticsearch**, voy a comparar algunos elementos en relación a una base de datos relacional SQL. Tambien voy a definir algunos términos que se emplean en **Elasticsearch**.

Comparativa entre base de datos relacional y **Elasticsearch**

| **Database**  | **Elastisearch**  |
|---|---|
| database  | index  |
| table  | type  |
| row  | document  |
| schema  | mapping  |
| sql  | query dsl  |

### Cluster

Un cluster es un conjunto de uno o más nodos que mantienen toda la información de manera distribuida e indexada. Cada cluster está identificado por un nombre, por defecto se llaman “elasticsearch”.

### **Node (Nodo)**

Un nodo es un server que forma parte de un cluster, almacena tu información y ayuda con las tareas de indexación y búsqueda del cluster. Los nodos están identificados por un nombre también, pero en este caso cada nodo está nombrado tras un personaje de Marvel.

Por defecto están configurados para ser parte de un cluster con el nombre de “elasticsearch”.

Puede haber tantos nodos como quieras por cada Cluster, en caso de que no haya ningún Cluster configurado en el momento de creación este lo creará y se unirá a él.

### Index (Indice)

Un índice es una colección de documentos que tienen características algo similares. Estos estan identificados por un nombre, el cual usaremos luego para indexar, buscar, actualizar y borrar.

### **Tipo**

Dentro de un índice, se pueden seleccionar uno o más tipos. Un tipo es una categoría / partición lógica de su índice de cuya semántica es totalmente suya. En general, un tipo se define para documentos con un conjunto de campos comunes.

### **Documento**

Un documento es una unidad básica de información que puede ser **_indexado_**. Este documento se expresa en **JSON** (JavaScript Object Notation), que es un formato de intercambio de datos de Internet ubicua.

### **Shard y réplicas**

Un índice puede almacenar una gran cantidad de datos que puede exceder los límites de hardware de un solo nodo.

Para resolver este problema, **Elasticsearch** ofrece la posibilidad de **subdividir** el índice en varias piezas o trozos llamados **fragmentos**. Cuando se crea un **índice**, sólo tiene que definir el número de fragmentos que desee. Cada fragmento es en sí mismo un “**índice**” totalmente funcional e independiente que se puede alojar en cualquier nodo del clúster, ofreciéndonos la posibilidad de **escalar horizontalmente** (añadiendo más máquinas), además de paralelizar y distribuir las distintas operaciones que hagamos sobre esos índices.

La **replicación** nos ofrece un mecanismo para que en caso de fallo el usuario no se vea afectado.

**Sharding** es importante por dos razones principales:

\* Permite dividir horizontalmente / o reducir el volumen de contenido.

\* Permite distribuir y paralelizar las operaciones a través de **fragmentos** (potencialmente en varios nodos)  aumentando así el rendimiento.

La **replicación** es importante por dos razones principales:

\* Proporciona **alta disponibilidad** en caso de que falle un **fragmento/nodo**. Por esta razón, es importante  tener en cuenta que un fragmento de réplica **no** se asigna en el mismo nodo que el fragmento **primario/original** que fue copiado.

\* Se le permite escalar  volumen de búsquedas y / rendimiento puesto que las búsquedas se pueden  ejecutar en todas las réplicas en paralelo.

## Instalar y configurar Elasticsearch

Instalación **macOS**:

Primero Instalamos Java

brew cask install caskroom/versions/java8

Luego instalarmos Elasticsearch

brew install elasticsearch 

Instalación **Linux**:

Primero instalamos Java

sudo apt-get install openjdk-7-jre

Descargamos **Elasticsearch**:

wget https://download.elastic.co/elasticsearch/elasticsearch/elasticsearch-1.7.2.deb

sudo dpkg -i elasticsearch-1.7.2.deb

Ejecutamos el servidor de Elasticsearch

elasticsearch

Lo siguiente es comprobar que Elasticsearch este ejecutandose, para ello ejecutamos:

curl -XGET http://localhost:9200/<br>

Deberíamos obtener la siguiente respuesta:

{
  "name": "vytx6Jd",
  "cluster\_name": "elasticsearch\_fjugaldev",
  "cluster\_uuid": "8XzlylxzTz6SVn0mhYSxRw",
  "version": {
    "number": "6.2.1",
    "build\_hash": "7299dc3",
    "build\_date": "2018-02-07T19:34:26.990113Z",
    "build\_snapshot": false,
    "lucene\_version": "7.2.1",
    "minimum\_wire\_compatibility\_version": "5.6.0",
    "minimum\_index\_compatibility\_version": "5.0.0"
  },
  "tagline": "You Know, for Search"
}

Revisamos la salud (health) del cluster:

curl -XGET 'http://localhost:9200/\_cat/health?v&pretty'

Respuesta:

epoch timestamp cluster status node.total node.data shards pri relo init unassign pending\_tasks max\_task\_wait\_time active\_shards\_percent 
1518627592 17:59:52 elasticsearch\_fjugaldev green 1 1 0 0 0 0 0 0 - 100.0%

Ahora vamos a verificar los nodos de nuestro cluster:

curl -XGET 'http://localhost:9200/\_cat/nodes?v&pretty'<br>

Respuesta:

ip heap.percent ram.percent cpu load\_1m load\_5m load\_15m node.role master name 
127.0.0.1 25 98 22 3.24 mdi \* vytx6Jd

Podemos observar que tenemos un nodo llamado “**vytx6jd**”

Verificamos los indices:

curl -XGET 'http://localhost:9200/\_cat/indices?v&pretty'<br>

Respuesta:

health status index uuid pri rep docs.count docs.deleted store.size pri.store.size

Esto indica que no tenemos ningún indice creado.

### Crear un Index

curl -XPUT 'http://localhost:9200/book?pretty'<br>

Respuesta:

{ "acknowledged" : true, "shards\_acknowledged" : true, "index" : "book" }

## Nuestro primer CRUD en Elasticsearch

A continuación vamos a realizar nuestro primer CRUD:

- **CREATE**
- **READ**
- **UPDATE**
- **DELETE**

Al indexar, el ID de cada elemento es opcional. Si no se especifica, **Elasticsearch** generará un ID aleatorio y luego utilizarlo para indexar el documento.

### **Crear documento (CREATE)**

curl -H "Content-Type: application/json" -XPOST "http://localhost:9200/book/\_doc/1?pretty" -d '{ "title" : "Deportes Extremos en Invierno" }'

O también

curl -H "Content-Type: application/json" -XPOST "http://localhost:9200/book/\_doc/?pretty" -d '{ "title" : "Deportes Extremos en Invierno" }'

Respuesta:

{
  "\_index": "book",
  "\_type": "\_doc",
  "\_id": "1",
  "\_version": 1,
  "result": "created",
  "\_shards": {
    "total": 2,
    "successful": 1,
    "failed": 0
  },
  "\_seq\_no": 0,
  "\_primary\_term": 1
}

### **Obtener documento por ID (READ)**

curl -XGET 'http://localhost:9200/book/\_doc/1?pretty'

Respuesta:

{
  "\_index": "book",
  "\_type": "\_doc",
  "\_id": "1",
  "\_version": 2,
  "found": true,
  "\_source": {
    "title": "Deportes Extremos en Invierno"
  }
}

### **Actualizar documento(UPDATE)**

curl -XPOST -H "Content-Type: application/json" 'http://localhost:9200/book/\_doc/1/\_update?pretty' -d '{"doc": { "title": "Deportes Extremos en Verano" }}'

Respuesta:

{
  "\_index": "book",
  "\_type": "\_doc",
  "\_id": "1",
  "\_version": 2,
  "found": true,
  "\_source": {
    "title": "Deportes Extremos en Verano"
  }
}

### Borrar documento por ID (DELETE)

curl -XDELETE 'http://localhost:9200/book/\_doc/1'

### Para realizar búsquedas:

curl -XGET -H "Content-Type: application/json" 'http://localhost:9200/book/\_search' -d '{"query": { "match": { "title": "Deportes" } }}'

Respuesta:

{
  "took": 7,
  "timed\_out": false,
  "\_shards": {
    "total": 5,
    "successful": 5,
    "skipped": 0,
    "failed": 0
  },
  "hits": {
    "total": 1,
    "max\_score": 0.2876821,
    "hits": \[
      {
        "\_index": "book",
        "\_type": "\_doc",
        "\_id": "aXKAlWEB4\_X33jLc0aiB",
        "\_score": 0.2876821,
        "\_source": {
          "title": "Deportes Extremos en Verano"
        }
      }
    \]
  }
}

### **Borrar Database:**

curl -XDELETE 'http://localhost:9200/book?pretty'

Respuesta:

{  
  "acknowledged" : true 
}

Y con esto tenemos una idea de como funciona el API Elasticsearch. Particularmente pienso que es una herramienta super poderosa de la cual podemos sacar bastante ventaja en nuestras aplicaciones ya que bien sea que se trate de aplicaciones cuyo volumen de datos sea muy alto, o aplicaciones con poco nivel de datos, es una excelente idea incorporar este motor de datos. De hecho trae consigo un montón de utilidades que lo convierte en un paquete completo en cuanto a manejo de datos se refiere.

Si te gustó este post, ayúdame a que pueda servirle a muchas más personas, compartiendo mis contenidos en tus redes sociales.

Espero que este post haya sido de gran ayuda para ti, y como siempre, cualquier inquietud o duda que tengas, puedes contactarme por cualquiera de las vías disponibles, o dejando tus comentarios al final de este post. También puedes sugerir que temas o post te gustaría leer a futuro.

* * *

Si te gustó el post, regálame tus aplausos!!!

\[wp-applause-button style="width:60px;height:60px;margin: 0 auto;" color="black"\]
