---
layout: post
title: "TheTV, Clean Architecture en Android: primeros pasos"
date: 2020-10-07 21:00:00 +0300
image: boy.jpg
tags: [TheTV, Android, Clean Architecture]
---

Un niño frente a un ordenador. Hace un par de días, cuando estuve buscando una foto para poner en la portada de
este post, me encontré con esta, y según la vi decidí que iba a ser la elegida. Siempre que programo me siento un poco como un niño. Lo primero, no nos engañemos, por esa sensación de no saber a ciencia cierta si lo que estamos haciendo está bien o no :joy:. Pero también por esa ilusión que es tan necesaria para hacer bien nuestro trabajo. Y como también este proyecto está en pañales y este post va a servir para dar una introducción de cómo está organizado el mismo, pues eso.
{: .text-justify}

Sin más dilación, vamos al lío.
{: .text-justify}

<h2>:dart: Arquitectura del proyecto</h2>
Si habéis leído [el primer post del proyecto][primer-post]{:target="_blank"}, habréis visto que la idea para la realización del mismo es seguir una arquitectura "clean". Para ello, partiremos de una división por módulos de cada una de las capas de la arquitectura. En mi caso he preferido dividir el proyecto en: capa de dominio, capa de datos, capa de presentación y capa de aplicación. A continuación iré detallando qué nos encontraremos en cada capa, y los pros y contras de esta división.
{: .text-justify}

En la primera iteración del proyecto, obtendremos simplemente una lista capítulos de series, los cuales estarán organizados en tres pestañas: "Emitido hoy", "Populares" y "Mejor valorados". Simplemente eso, una funcionalidad que consultará los datos al servicio que nos provee el API de [The Movie Database.][tmdb]
{: .text-justify}

<h2>:briefcase: La capa de dominio</h2>

<img src="../img/thetv-domain.png" width="400"/>
{: .text-center}

En esta capa se define toda la lógica de negocio de la aplicación. Es decir, aquí es donde se definen los modelos de datos que se usarán para representar las series, capítulos, temporadas, actores... así como la lógica asociada a los mismos. De momento sólo tenemos el objeto `TVShow` que contiene la información que necesitamos para mostrar las miniaturas de cada serie.
{: .text-justify}

El paquete **usecases** contiene los casos de uso que se usarán para la consulta en este caso de las listas de series. Cada caso de uso a su vez se abstrae de saber de dónde le llegan los datos, ya sea un origen de datos local o remoto, usando el [patrón repository][repository-pattern], y devuelve un objeto funcional `Either<L, R>` que representa un valor con dos posibles opciones, fallo (`Left`) y éxito (`Right`). En nuestro caso tenemos el objeto `Failure`, una [sealed class][sealed-classes] que simplemente identifica el tipo de error en caso de ser `Left`. Para el caso `Right` dependerá del caso de uso correspondiente, pero de momento todos los casos de uso existentes devolverán una lista de series (`List<TVShow>`).
{: .text-justify}

Esta es una capa escrita puramente en Kotlin y sin ninguna dependencia del framework de Android, por lo que podría reutilizarse esta lógica en otro tipo de aplicación.
{: .text-justify}

<h2>:earth_africa: La capa de datos</h2>

<img src="../img/thetv-data.png" width="400"/>
{: .text-center}

Esta capa será la encargada del acceso a los datos necesarios para modelar el dominio. Primeramente se puede encontrar la implementación del repositorio definido en la capa de dominio. Este tiene definido una variable `isOnline` la cual nos da el estado de la conexión a Internet del dispositivo, y dos orígenes de datos que se consultarán dependiendo del estado de esta conexión, para así tener siempre información que mostrar al usuario. Ambos orígenes de datos son interfaces cuya implementación se abstrae para así poder usar la herramienta que necesitemos en cada caso. Si por ejemplo en un futuro necesitáramos cambiar la base de datos usada en el origen de datos local de SQLite a Room o Realm, sólo deberíamos cambiar la implementación de esta interfaz.
{: .text-justify}

En el paquete **entities** encontramos la definición de los modelos que usan los orígenes de datos, así como los métodos para pasar de modelo de datos a modelo de dominio. Finalmente, tenemos los paquetes **local** y **remote** en los que se definen las implementaciones de los orígenes de datos.
{: .text-justify}

Esta capa tiene dependencia del framework de Android, ya que usa [SQLite][sqlite] como base de datos local. También se usa [Moshi][moshi] para parsear los JSON de respuesta de los servicios usados en el origen de datos remoto, así como [Retrofit][retrofit] como cliente HTTP.
{: .text-justify}

<h2>:art: La capa de presentación</h2>

<img src="../img/thetv-presentation.png" width="400"/>
{: .text-center}

Esta capa se encarga de gestionar las funcionalidades de la aplicación, teniendo acceso a los casos de uso definidos en la capa de dominio, y pasando los resultados de los mismos a la vista. Para gestionar el funcionamiento de esta capa, se hará uso del patrón [MVP][mvp], definiendo una `View` y `Presenter` para cada funcionalidad de la aplicación, y usando un modelo de vista que será el que se use para mostrar los datos en la pantalla.
{: .text-justify}

De momento tenemos una vista `TVShowListView` y un modelo de vista `TVShowView` que serán comunes, ya que todos contienen los mismos datos y métodos para actualizar la vista. El paquete **features** contendrá los presenter y view para cada funcionalidad y el paquete **model** los modelos de vista.
{: .text-justify}

El paquete **asynchrony** contiene una implementación de una interfaz para manejar la asincronía de las llamadas a los casos de uso, usando [corrutinas][coroutines], las cuales manejaremos desde cada presenter, y que nos permitirán controlar los callbacks de error y éxito de cada caso de uso.
{: .text-justify}

Esta capa, al igual que la de dominio, sólo usa código Kotlin sin ninguna dependencia, por lo que puede ser reutilizable.
{: .text-justify}

<h2>:iphone: La capa del framework</h2>

<img src="../img/thetv-app.png" width="400"/>
{: .text-center}

En la capa de aplicación es la del framework de Android, donde tendremos las activities, fragments, layouts... para cada pantalla que el usuario verá. Todas las funcionalidades están en el paquete **features**.
{: .text-justify}

Dentro del paquete **core** tenemos un **customviews** donde encontraremos un `GridRecyclerView` que define la vista del grid de las series. También tenemos un paquete con extensiones de vista y para cargar las imágenes (usando [Glide][glide]). El paquete **di** inyectará las dependencias necesarias para cada funcionalidad mediante la librería [Dagger][dagger]. Por último en el paquete **platform** encontraremos un `NetworkHandler` para poder obtener el estado de la conexión a Internet del teléfono.
{: .text-justify}

<h3> Extras </h3>
Para la carga de las animaciones se ha usado la librería [Lottie][lottie], simplemente cargando los archivos .json en los layouts y mostrando las animaciones cuando son necesarias.
{: .text-justify}

En cuanto a testing se ha usado [MockK][mockk] para mockear los datos necesarios.
{: .text-justify}
___

Si queréis ver el código relacionado con este post lo tenéis aquí:
> [https://github.com/rcorchero/TheTV][github]

Os animo a que comentéis posibles mejoras o ideas que tengáis para mejorar este código, el cual iré evolucionando en el próximo post y viendo que puntos podemos ir mejorando, comparando las herramientas, metodologías y arquitectura usadas para ver si podemos ir optimizando lo planteado hasta ahora. ¡Hasta la próxima!
{: .text-justify}

[primer-post]: ../the-tv-introduccion
[tmdb]: https://www.themoviedb.org/
[repository-pattern]: https://developer.android.com/jetpack/guide#overview
[sealed-classes]: https://kotlinlang.org/docs/reference/sealed-classes.html
[sqlite]: https://developer.android.com/training/data-storage/sqlite
[moshi]: https://github.com/square/moshi
[retrofit]: https://square.github.io/retrofit/
[mvp]: https://devexperto.com/mvp-android/
[coroutines]: https://developer.android.com/kotlin/coroutines?hl=es
[glide]: https://github.com/bumptech/glide
[dagger]: https://developer.android.com/training/dependency-injection/dagger-basics
[github]: https://github.com/rcorchero/TheTV
[lottie]: https://airbnb.design/lottie/
[mockk]: https://mockk.io/
