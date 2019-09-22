---
layout: post
title: "Desde cero: Cómo crear aplicaciones móviles híbridas con Cordova + Vue.js 2"
author: fjugaldev
categories: [ Desarrollo Movil ]
tags: [Apache Cordova, Aplicaciones Híbridas, Aplicaciones Móviles, Cordova, Desde Cero, Frameworks JS, Vue.js]
image: assets/images/posts/crear-app-hibridas-cordova-vue-js.2.jpg
description: "En este post vamos a ver paso a paso como crear una aplicación móvil híbrida utilizando Apache Cordova y Vue.js. Compila tu App en multiples plataformas."
featured: false
hidden: false
---

En este post vamos a ver paso a paso como crear una aplicación móvil híbrida utilizando **Apache Cordova** y **Vue.js** **2**.

Los pasos que verás a continuación, describen el proceso de configuración de una plataforma de desarrollo en la que podrás crear aplicaciones móviles híbridas utilizando **Cordova + Vue.js 2**

Esto incluye:

- Instalación y configuración de Cordova + Webpack + Vue.js.
- Vista previa de la aplicación en el navegador.
- Hot / Live: Recarga tu aplicación en el navegador automáticamente al detectar nuevos cambios.
- Ejecutar la aplicación en dispositivos móviles Android / iOS (Utilizaremos los emuladores respectivos).

Antes de empezar, debo definirles lo que es una aplicación móvil hibrida:

## ¿Qué es una aplicación móvil híbrida?

Las aplicaciones híbridas son aplicaciones móviles diseñadas en un lenguaje de programación web empleando tecnologías como HTML5, CSS y JavaScript, y que gracias a un framework (en nuestro caso **Cordova**), permite adaptar la vista de nuestra aplicación web a cualquier vista de un dispositivo móvil mediante un navegador empotrado (**WebView**).

Adicionalmente, este framework (**Cordova**), mediante plugins, nos permite acceder a los componentes nativos de los dispositivos tales como la cámara, GPS, Wifi, Acelerómetros, Libreta de Contactos, entre otros.

Lo interesante de todo esto es que cada día el look and feel de una aplicación híbrida es casi igual al de una nativa, esto gracias a que poco a poco esta tecnología ha ido madurando y haciendo que sea más fácil integrar estas aplicaciones en los dispositivos móviles.

Otro aspecto importante es que estas aplicaciones híbridas se distribuyen igual que una nativa, mediante los markets como **Google Play Store** y el **App Store**.

Y por último y no menos importante está el hecho de que las **aplicaciones híbridas** brindan ventajas notables frente a las nativas, algunas de estas son:

- El costo de desarrollo es muchisimo inferior al de una aplicación nativa ya que el código se escribe una sola vez y es compilado hacia las distintas plataformas deseadas.
- No se requiere de sólidos conocimientos nativos para cada plataforma ya que la curva de aprendizaje y de implementación es muy corta.
- El mantenimiento de nuestras aplicaciones es muchisimo mas eficiente ya que solo tenemos que cambiar el código una sola vez y luego compilar hacia las distintas plataformas.
- No se requiere conocimiento de lenguajes de programación natívos como Java (Android), Swift u Objective C (iOS), entre otros.

### Pre-Requisitos

Para llevar a cabo este ejemplo, debemos tener instalado los siguientes pre-requisitos:

- **Sistema Operativo macOS** — High Sierra 10.13.2
- **Node.js** — v9.2.1—Ir a la [web oficial](https://nodejs.org/es/) para instalar Node.js
- **Apache Cordova** — 8.0.0 —  npm install -g cordova
- **Vue-CLI** — 2.9.3 — `npm install-g vue-cli`
- **Gradle** — 4.5.1 — brew install gradle — [¿Cómo Instalar Homebrew?](https://brew.sh/index_es.html)
- **Android SDK** — brew tap caskroom/cask — brew cask install android-sdk
- **Android Platform Tools** — brew cask install android-platform-tools
- **Xcode**—xcode-select --install— [Descargar Xcode](https://developer.apple.com/xcode/)
- **ios-deploy**—npm install -g ios-deploy

### Iniciando el proyecto con Cordova + Vue.js + Webpack

Antes de todo, debemos situarnos en el path donde deseamos que se cree nuestro proyecto.

1. Creamos el proyeto Cordova:
```shell
# Creating a new cordova project.
cordova create mi-app-hibrida com.franciscougalde.apps
```

1. Creamos el proyecto Vue.js  + Webpack:
```shell
vue init webpack mi-app-hibrida
```

**Vue** te preguntará:
```shell
? Target directory exists. Continue? (Y/n) Y
```

A lo que debemos contestar “Y” para aceptar y continuar ya que el directorio fué creado y configurado previamente por el proyecto de cordova en el paso anterior.

Seguidamente, **vue** te pedirá información para configurar el proyecto, los datos que yo introduje son los siguientes:

```shell
? Target directory exists. Continue? Yes
? Project name mi-app-hibrida
? Project description Mi Aplicación Móvil Hibrida con Cordova + Vue.js 2
? Author Francisco Ugalde
? Vue build standalone
? Install vue-router? Yes
? Use ESLint to lint your code? No
? Set up unit tests No
? Setup e2e tests with Nightwatch? No
? Should we run `npm install` for you after the project has been created? (recommended) npm

   vue-cli · Generated "mi-app-hibrida".

# Installing project dependencies ...
# ========================
```

### Unir los procesos de “build” del proyecto Cordova y Vue.js-WebPack

El directorio **./www** de tu proyecto **Cordova** debe lucir actualmente así:
```yaml
www
├── css
│   └── index.css
├── img
│   └── logo.png
├── index.html
└── js
    └── index.js
```

Lo primero que debemos hacer es eliminar el contenido de la carpeta **./www** de **Cordova**, ya que construiremos este contenido con WebPack en su lugar.

Tendremos que eliminar el directorio ./www ya que este es el código que Cordova integrará en el dispositivo móvil.

```shell
cd mi-app-hibrida
sudo rm -r www/\*
# Password: \*\*\*\*\*\*\*\*\*\*\*\*
```
Ahora debemos abrir el archivo de configuación de Webpack: ./config/index.js y actualiza los siguientes paths:

- Cambia los paths de **index** y `**assestsRoot**` para apuntar al directorio ./www y así el código de nuestra aplicación se genere en la ruta ./www/dist antes de ser empaquetado en el dispositivo móvil.
- Cambia el valor del path de `**assetsPublicPath **para que sea una cadena vacía`. Esto va a permitir a tu móvil servir la vista vía el protocolo file:///. Esto es importante puesto que no estaremos ejecutando un servidor web en nuestro dispositivo móvil (usualmente).

Actualiza tu ./config/index.js para que luzca de la siguiente manera:

```javascript
build: {
    // Template for index.html
    index: path.resolve(__dirname, '../www/dist/index.html'),

    // Paths
    assetsRoot: path.resolve(__dirname, '../www/dist'),
    assetsSubDirectory: 'static',
    assetsPublicPath: '',
}
```

Abre el archivo `**./config.xml** en tu IDE preferido y actualiza el punto de entrada del WebView de Cordova.`
```xml
<content src=”dist/index.html” />
```
Ahora procedemos a crear el paquete de distribución de nuestra aplicación:

```shell
npm run build

mi-app-hibrida@1.0.0 build /Users/fjugaldev/Sites/mi-app-hibrida
node build/build.js

Hash: 0faab7ddba58ce23a830
Version: webpack 3.11.0
Time: 16236ms
                                                  Asset       Size  Chunks             Chunk Names
               static/js/vendor.5973cf24864eecc78c48.js     111 kB       0  [emitted]  vendor
                  static/js/app.b22ce679862c47a75225.js    11.6 kB       1  [emitted]  app
             static/js/manifest.2ae2e69a05c33dfc65f8.js  857 bytes       2  [emitted]  manifest
    static/css/app.30790115300ab27614ce176899523b62.css  432 bytes       1  [emitted]  app
static/css/app.30790115300ab27614ce176899523b62.css.map  828 bytes          [emitted]
           static/js/vendor.5973cf24864eecc78c48.js.map     548 kB       0  [emitted]  vendor
              static/js/app.b22ce679862c47a75225.js.map    22.2 kB       1  [emitted]  app
         static/js/manifest.2ae2e69a05c33dfc65f8.js.map    4.97 kB       2  [emitted]  manifest
                                             index.html  516 bytes          [emitted]

  Build complete.

  Tip: built files are meant to be served over an HTTP server.
  Opening index.html over file:// won't work.
```

El directorio `./www/dist` debe lucir ahora algo parecido a esto:
```yaml
www
└── dist
    ├── index.html
    └── static
        ├── css
        │   ├── app.30790115300ab27614ce176899523b62.css
        │   └── app.30790115300ab27614ce176899523b62.css.map
        └── js
            ├── app.b22ce679862c47a75225.js
            ├── app.b22ce679862c47a75225.js.map
            ├── manifest.2ae2e69a05c33dfc65f8.js
            ├── manifest.2ae2e69a05c33dfc65f8.js.map
            ├── vendor.5973cf24864eecc78c48.js
            └── vendor.5973cf24864eecc78c48.js.map
```
Abre el archivo `**./www/dist/index.html**` en tu navegador para verificar que todo funciona perfectamente vía el protocolo file:///.

```shell
open ./www/dist/index.html
```
![Aplicaciones móviles híbridas con Cordova + Vue.js 2](/assets/images/posts/Screen-Shot-2018-02-18-at-01.13.19.png)

Puedes notar en la barra de dirección el protocolo **file:///**

Seguidamente debemos abrir el archivo ./index.html de Vue.js en tu IDE y actualiza el meta tag “Content-Security-Policy” para permitir los sockets web locales. Puedes hacer esto agregando: connect-src ‘self’ ws:;. Esto permitirá a Webpack conocer cuando se ha creado el paquete de distribución de la aplicación y cuando se ha recargado o actualizado el codigo en la vista previa del navegador. Esto deberá suceder cada vez que hagas un cambio en el codigo fuente.

```html
<meta http-equiv=”Content-Security-Policy” content=”default-src ‘self’ data: gap: <a href="https://ssl.gstatic.com/" target="_blank" rel="nofollow noopener noopener" data-href="https://ssl.gstatic.com" data-mce-href="https://ssl.gstatic.com/">https://ssl.gstatic.com</a> ‘unsafe-eval’; style-src ‘self’ ‘unsafe-inline’; media-src *; img-src ‘self’ data: content:; connect-src ‘self’ ws:;”>
```

Ahora podemos probar que el modo dev funciona correctamente:

```shell
npm run dev

mi-app-hibrida@1.0.0 dev /Users/fjugaldev/Sites/mi-app-hibrida
webpack-dev-server --inline --progress --config build/webpack.dev.conf.js

 95% emitting

 DONE  Compiled successfully in 8968ms                                                                                                            1:37:22 AM

 I  Your application is running here: http://localhost:8080
```

Ahora el Servidor hot-reload de Vue-Webpack esta online, Entra en [http://localhost:8080](http://localhost:8080/) para ver la aplicación.

Deberas ver la siguiente pantalla:

![Aplicaciones móviles híbridas con Cordova + Vue.js 2](/assets/images/posts/Screen-Shot-2018-02-18-at-01.41.21.png)

Nótese el mensaje de web-sockets en el log de la consola, y el host:puerto en la barra de direcciones del navegador.

### Ejecutando la aplicación en Android

Vamos ahora a probar que todo funciona en un dispositivo móvil Android. Asegurate que tienes conectado uno en tu computador.

Procedemos a agregar a Android como plataforma en Cordova:

```shell
cordova platform add android

Using cordova-fetch for cordova-android@~7.0.0
Adding android project...
Creating Cordova project for the Android platform:
	Path: platforms/android
	Package: com.franciscougalde.apps
	Name: HelloCordova
	Activity: MainActivity
	Android target: android-26
Subproject Path: CordovaLib
Subproject Path: app
Android project created with cordova-android@7.0.0
Android Studio project detected
Android Studio project detected
Discovered plugin "cordova-plugin-whitelist" in config.xml. Adding it to the project
Installing "cordova-plugin-whitelist" for android

               This plugin is only applicable for versions of cordova-android greater than 4.0. If you have a previous platform version, you do \*not\* need this plugin since the whitelist will be built in.

Adding cordova-plugin-whitelist to package.json
Saved plugin info for "cordova-plugin-whitelist" to config.xml
--save flag or autosave detected
Saving android@~7.0.0 into config.xml file ...
```

Luego de esto procedemos a ejecutar la aplicación en nuestro dispositivo Android:

```shell
cordova run android

Android Studio project detected
ANDROID\_HOME=/usr/local/Caskroom/android-sdk/3859397
JAVA\_HOME=/Library/Java/JavaVirtualMachines/jdk1.8.0\_162.jdk/Contents/Home
studio
Subproject Path: CordovaLib
Subproject Path: app
publishNonDefault is deprecated and has no effect anymore. All variants are now published.
The Task.leftShift(Closure) method has been deprecated and is scheduled to be removed in Gradle 5.0. Please use Task.doLast(Action) instead.
	at build\_8u9efqela5ohwabjwm0tr50nm.run(/Users/fjugaldev/Sites/mi-app-hibrida/platforms/android/app/build.gradle:143)
:CordovaLib:preBuild UP-TO-DATE
:CordovaLib:preDebugBuild UP-TO-DATE
:CordovaLib:compileDebugAidl UP-TO-DATE
:CordovaLib:compileDebugRenderscript UP-TO-DATE
:CordovaLib:checkDebugManifest UP-TO-DATE
:CordovaLib:generateDebugBuildConfig UP-TO-DATE
:CordovaLib:prepareLintJar UP-TO-DATE
:CordovaLib:generateDebugResValues UP-TO-DATE
:CordovaLib:generateDebugResources UP-TO-DATE
:CordovaLib:packageDebugResources UP-TO-DATE
:CordovaLib:platformAttrExtractor UP-TO-DATE
:CordovaLib:processDebugManifest UP-TO-DATE
:CordovaLib:processDebugResources UP-TO-DATE
:CordovaLib:generateDebugSources UP-TO-DATE
:CordovaLib:javaPreCompileDebug UP-TO-DATE
:CordovaLib:compileDebugJavaWithJavac UP-TO-DATE
:CordovaLib:processDebugJavaRes NO-SOURCE
:CordovaLib:transformClassesAndResourcesWithPrepareIntermediateJarsForDebug UP-TO-DATE
:app:preBuild UP-TO-DATE
:app:preDebugBuild UP-TO-DATE
:app:compileDebugAidl UP-TO-DATE
:CordovaLib:packageDebugRenderscript NO-SOURCE
:app:compileDebugRenderscript UP-TO-DATE
:app:checkDebugManifest UP-TO-DATE
:app:generateDebugBuildConfig UP-TO-DATE
:app:prepareLintJar UP-TO-DATE
:app:generateDebugResValues UP-TO-DATE
:app:generateDebugResources UP-TO-DATE
:app:mergeDebugResources UP-TO-DATE
:app:createDebugCompatibleScreenManifests UP-TO-DATE
:app:processDebugManifest UP-TO-DATE
:app:splitsDiscoveryTaskDebug UP-TO-DATE
:app:processDebugResources UP-TO-DATE
:app:generateDebugSources UP-TO-DATE
:app:javaPreCompileDebug UP-TO-DATE
:app:compileDebugJavaWithJavac UP-TO-DATE
:app:compileDebugNdk NO-SOURCE
:app:compileDebugSources UP-TO-DATE
:CordovaLib:mergeDebugShaders UP-TO-DATE
:CordovaLib:compileDebugShaders UP-TO-DATE
:CordovaLib:generateDebugAssets UP-TO-DATE
:CordovaLib:mergeDebugAssets UP-TO-DATE
:app:mergeDebugShaders UP-TO-DATE
:app:compileDebugShaders UP-TO-DATE
:app:generateDebugAssets UP-TO-DATE
:app:mergeDebugAssets UP-TO-DATE
:app:extractTryWithResourcesSupportJarDebug UP-TO-DATE
:app:transformClassesWithStackFramesFixerForDebug UP-TO-DATE
:app:transformClassesWithDesugarForDebug UP-TO-DATE
:app:transformClassesWithDexBuilderForDebug UP-TO-DATE
:app:transformDexArchiveWithExternalLibsDexMergerForDebug UP-TO-DATE
:app:transformDexArchiveWithDexMergerForDebug UP-TO-DATE
:CordovaLib:compileDebugNdk NO-SOURCE
:CordovaLib:mergeDebugJniLibFolders UP-TO-DATE
:CordovaLib:transformNativeLibsWithMergeJniLibsForDebug UP-TO-DATE
:CordovaLib:transformNativeLibsWithIntermediateJniLibsForDebug UP-TO-DATE
:app:mergeDebugJniLibFolders UP-TO-DATE
:app:transformNativeLibsWithMergeJniLibsForDebug UP-TO-DATE
:app:processDebugJavaRes NO-SOURCE
:app:transformResourcesWithMergeJavaResForDebug UP-TO-DATE
:app:validateSigningDebug
:app:packageDebug UP-TO-DATE
:app:assembleDebug UP-TO-DATE
:app:cdvBuildDebug UP-TO-DATE

BUILD SUCCESSFUL in 2s
47 actionable tasks: 1 executed, 46 up-to-date
Built the following apk(s):
	/Users/fjugaldev/Sites/mi-app-hibrida/platforms/android/app/build/outputs/apk/debug/app-debug.apk
ANDROID\_HOME=/usr/local/Caskroom/android-sdk/3859397
JAVA\_HOME=/Library/Java/JavaVirtualMachines/jdk1.8.0\_162.jdk/Contents/Home
No target specified, deploying to device '634034256815'.
none
Skipping build...
Built the following apk(s):
	/Users/fjugaldev/Sites/mi-app-hibrida/platforms/android/app/build/outputs/apk/debug/app-debug.apk
Using apk: /Users/fjugaldev/Sites/mi-app-hibrida/platforms/android/app/build/outputs/apk/debug/app-debug.apk
Package name: com.franciscougalde.apps
LAUNCH SUCCESS
```

Luego de esto deberás ver la aplicación corriendo en tu dispositivo Android.

![Aplicaciones móviles híbridas con Cordova + Vue.js 2](/assets/images/posts/Screenshot_20180218-021735.png)

### Ejecutando la aplicación en iOS

Es tiempo de verificar cosas en un dispositivo iOS. Asegúrate de que lo hayas conectado a tu computador.

Primero, Agreguemos la plataforma **iOS** a nuestro proyecto **Cordova:**

```shell
cordova platform add ios

Using cordova-fetch for cordova-ios@~4.5.4
Adding ios project...
Creating Cordova project for the iOS platform:
	Path: platforms/ios
	Package: com.franciscougalde.apps
	Name: HelloCordova
iOS project created with cordova-ios@4.5.4
Installing "cordova-plugin-whitelist" for ios
--save flag or autosave detected
Saving ios@~4.5.4 into config.xml file ...
```

Abre el proyecto de nuestra aplicación en el Xcode ejecutando lo siguiente:

```shell
open platforms/ios/HelloCordova.xcodeproj
```

Lo siguiente será seleccionar el certificado de Equipo (Team certificate) en el menú desplegable “Build Signing”.

![Aplicaciones móviles híbridas con Cordova + Vue.js 2](assets/images/posts/Screen-Shot-2018-02-18-at-17.39.10.jpg)

Crear el paquete para iOS.

```shell
cordova build ios

Building project: /Users/fjugaldev/Sites/mi-app-hibrida/platforms/ios/HelloCordova.xcworkspace
     Configuration: Debug
     Platform: device
 User defaults from command line:
     IDEArchivePathOverride = /Users/fjugaldev/Sites/mi-app-hibrida/platforms/ios/HelloCordova.xcarchive
 
 Build settings from command line:
     CONFIGURATION\_BUILD\_DIR = /Users/fjugaldev/Sites/mi-app-hibrida/platforms/ios/build/device
     SHARED\_PRECOMPS\_DIR = /Users/fjugaldev/Sites/mi-app-hibrida/platforms/ios/build/sharedpch
 
 Build settings from configuration file '/Users/fjugaldev/Sites/mi-app-hibrida/platforms/ios/cordova/build-debug.xcconfig':
     CLANG\_ALLOW\_NON\_MODULAR\_INCLUDES\_IN\_FRAMEWORK\_MODULES = YES
     CODE\_SIGN\_ENTITLEMENTS = $(PROJECT\_DIR)/$(PROJECT\_NAME)/Entitlements-$(CONFIGURATION).plist
     CODE\_SIGN\_IDENTITY = iPhone Developer
     ENABLE\_BITCODE = NO
     GCC\_PREPROCESSOR\_DEFINITIONS = DEBUG=1
     HEADER\_SEARCH\_PATHS = "$(TARGET\_BUILD\_DIR)/usr/local/lib/include" "$(OBJROOT)/UninstalledProducts/include" "$(OBJROOT)/UninstalledProducts/$(PLATFORM\_NAME)/include" "$(BUILT\_PRODUCTS\_DIR)"
     OTHER\_LDFLAGS = -ObjC
     SWIFT\_OBJC\_BRIDGING\_HEADER = $(PROJECT\_DIR)/$(PROJECT\_NAME)/Bridging-Header.h
 
 === BUILD TARGET CordovaLib OF PROJECT CordovaLib WITH CONFIGURATION Debug ===
 
 Check dependencies
 
 Write auxiliary files
 
 ...
 
 \*\* ARCHIVE SUCCEEDED \*\*
 
 Non-system Ruby in use. This may cause packaging to fail.
 If you use RVM, please run \`rvm use system\`.
 If you use chruby, please run \`chruby system\`.
 2018-02-01 16:43:32.249 xcodebuild\[82807:848721\] \[MT\] IDEDistribution: -\[IDEDistributionLogging \_createLoggingBundleAtPath:\]: Created bundle at path '/var/folders/kw/5t8w91\_n6rb0z8b2z2grsj1r0000gn/T/HelloCordova\_2018-02-01\_16-43-32.247.xcdistributionlogs'.
 Exported HelloCordova.xcarchive to: /Users/fjugaldev/Sites/mi-app-hibrida/platforms/ios/build/device

 \*\* EXPORT SUCCEEDED \*\*
```

Procedemos a ejecutar la aplicación en nuestro dispositivo iOS:

```shell
cordova run ios

 Building project: /Users/fjugaldev/Sites/mi-app-hibrida/platforms/ios/HelloCordova.xcworkspace
     Configuration: Debug
     Platform: device
 User defaults from command line:
     IDEArchivePathOverride = /Users/fjugaldev/Sites/mi-app-hibrida/platforms/ios/HelloCordova.xcarchive
 
 Build settings from command line:
     CONFIGURATION\_BUILD\_DIR = /Users/fjugaldev/Sites/mi-app-hibrida/platforms/ios/build/device
     SHARED\_PRECOMPS\_DIR = /Users/fjugaldev/Sites/mi-app-hibrida/platforms/ios/build/sharedpch
 
 Build settings from configuration file '/Users/fjugaldev/Sites/mi-app-hibrida/platforms/ios/cordova/build-debug.xcconfig':
     CLANG\_ALLOW\_NON\_MODULAR\_INCLUDES\_IN\_FRAMEWORK\_MODULES = YES
     CODE\_SIGN\_ENTITLEMENTS = $(PROJECT\_DIR)/$(PROJECT\_NAME)/Entitlements-$(CONFIGURATION).plist
     CODE\_SIGN\_IDENTITY = iPhone Developer
     ENABLE\_BITCODE = NO
     GCC\_PREPROCESSOR\_DEFINITIONS = DEBUG=1
     HEADER\_SEARCH\_PATHS = "$(TARGET\_BUILD\_DIR)/usr/local/lib/include" "$(OBJROOT)/UninstalledProducts/include" "$(OBJROOT)/UninstalledProducts/$(PLATFORM\_NAME)/include" "$(BUILT\_PRODUCTS\_DIR)"
     OTHER\_LDFLAGS = -ObjC
     SWIFT\_OBJC\_BRIDGING\_HEADER = $(PROJECT\_DIR)/$(PROJECT\_NAME)/Bridging-Header.h
 
 === BUILD TARGET CordovaLib OF PROJECT CordovaLib WITH CONFIGURATION Debug ===
 
 Check dependencies
 
 Write auxiliary files
 
 ...
 
 (lldb)     connect
 (lldb)     run
 success
 (lldb)     safequit
 Process 1502 detached
```
Deberías poder ver la aplicación Vue.js en tu dispositivo iOS tal como se muestra a continuación:

![Aplicaciones móviles híbridas con Cordova + Vue.js 2](/assets/images/posts/Archivo-18-2-18-18-19-30.png)

Y ya con esto hemos logrado entonces combinar dos tecnologías ideales para el desarrollo de aplicaciones móviles híbridas de una forma sencilla y que estoy seguro que a muchos de ustedes les va a servir para iniciarse en este mundo de las aplicaciones móviles.

Si gustas revisar el código resultante de nuestra aplicación de ejemplo, puedes clonarlo desde [mi repositorio](https://github.com/fjugaldev/aplicaciones-moviles-hibridas-cordova-vuejs) en github.com

Si te gustó este post, ayúdame a que pueda servirle a muchas más personas, compartiendo mis contenidos en tus redes sociales.

Espero que este post haya sido de gran ayuda para ti, y como siempre, cualquier inquietud o duda que tengas, puedes contactarme por cualquiera de las vías disponibles, o dejando tus comentarios al final de este post. También puedes sugerir que temas o post te gustaría leer a futuro.

* * *
