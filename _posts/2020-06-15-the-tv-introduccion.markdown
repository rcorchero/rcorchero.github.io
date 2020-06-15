---
layout: post
title: "TheTV, Clean Architecture en Android: introducción"
date: 2020-06-15 21:00:00 +0300
image: shows-app.jpg
tags: [TheTV, Android, Clean Architecture]
---

Hace ya un tiempo que quería realizar un proyecto como el que voy a comentar a continuación, proyecto que en esta serie de posts irá evolucionando, añadiendo nuevas funcionalidades y refactorizando un código base para intentar mejorarlo y mostrar varias alternativas a la hora de abordar el desarrollo de una aplicación Android usando una arquitectura Clean.
{: .text-justify}

<h2>:triangular_ruler: ¿Pero, que es Clean Architecture?</h2>

Hace ya unos años, allá por 2012, Robert C. Martin publica un [post][uncle-bob] donde propone una nueva arquitectura para estructurar los proyectos de software, que rápidamente va ganando adeptos y se convierte en un referente para la comunidad.
{: .text-justify}

![]({{site.baseurl}}/img/clean.jpg)

Este tipo de arquitectura permite desacoplar el código es capas distintas, que nos ayudarán a tener un proyecto más organizado, fácil de modificar y de testear. En una aplicación Android, este tipo de arquitecture se puede plantear de varias maneras, esto es por módulos o por paquetes. En este proyecto partiremos de una división en módulos por capas (data, domain, presentation y app), y después estudiaremos otras alternativas que pueden mejorar la arquitectura inicial (como la división por features).
{: .text-justify}

Ya que pretendo presentar una visión más práctica que teórica en estos posts, si queréis encontrar más información sobre Clean Architecture en Android, podéis revisar la [serie de posts][fernando-cejas] de Fernando Cejas sobre el tema, o en [este otro][antonio-leiva] de Antonio Leiva, en los cuales veréis me he inspirado para la realización del proyecto.
{: .text-justify}

<h2>:tv: TheTV, una app para saber más de tus series</h2>

Para el ejemplo a seguir empezaremos con el desarrollo de una aplicación escrita en Kotlin, que consultara un listado de series, en las cuales podremos:

- Consultar los capítulos emitidos hoy.
- Consultar las series más populares del momento.
- Consultar las series mejor valoradas.
- Consultar el detalle de una serie.
- Buscar una serie.

Para ello, empezaré por una primera iteracción del proyecto donde estableceremos el esqueleto del proyecto, explicaré cada capa y realizaremos completamente la primera funcionalidad, que será la consulta de los capítulos emitidos en el día actual.
{: .text-justify}

La aplicación funcionará también offline y estará completamente testada.

Para obtener los datos nos basaremos el API de [The Movie Database.][tmdb]

Esta primera parte de la aplicación tendrá una pinta como esta:

<img src="../img/thetv-load.gif" width="300"/> <img src="../img/thetv-no-connection.gif" width="300"/>
{: .text-center}

Tras esto iremos intentando ver los fallos y posibles mejoras que podemos ir implementando en el proyecto, así como probaremos otras librerías o patrones de diseño que nos puedan facilitar la vida.
{: .text-justify}

Si os parece interesante la idea, ¡estad atentos al siguiente post! :wink:

[uncle-bob]: https://blog.cleancoder.com/uncle-bob/2012/08/13/the-clean-architecture.html
[fernando-cejas]: https://fernandocejas.com/2018/05/07/architecting-android-reloaded/
[antonio-leiva]: https://devexperto.com/clean-architecture-android/
[tmdb]: https://www.themoviedb.org/
