---
id: 1215
title: 'Немножко Open Source'
date: '2012-09-09T21:14:24+00:00'
author: serge
layout: post
guid: 'http://sotnyk.com/?p=1215'
permalink: /2012/09/09/nemnozhko-open-source/
---

![](https://sotnyk.github.io/wp-content/uploads/2012/09/codeplex-300x133.jpg "codeplex")Не секрет, что многие open-source проекты активно используются в очень даже платной деятельности различных компаний. И наоборот – часто компании поддерживают различные open-source, не приносящие им прямого дохода. И [наш Iveonik](http://www.iveonik.com) не исключение.

У себя мы давно использовали чужое. Конечно, в рамках лицензий. Как правило, Apache или BSD. Конечно же, давно хотелось дать что-то взамен. Недавно решили выложить две наработки, которые базируются на основе свободных проектов [Lucene.net](http://incubator.apache.org/lucene.net/) и [Snowball](http://snowball.tartarus.org/).

1. [Flucene](http://flucene.codeplex.com/) – библиотека, позволяющая работать с документами известной поисковой библиотеки Lucene.net в стиле, в котором ORM-библиотеки работают с базами. Поскольку в индексе Люсина хранятся документы, то подобный класс библиотек логично назвать ODM – Object-documet mapper. Nuget-пакет можно найти по ключевому слову Flucene, либо выполнив команду “Install-Package Flucene” в консоли менеджера пакетов.
2. [StemmersNet](http://stemmersnet.codeplex.com/) – порт на C# стеммеров из набора проекта Snowball. Nuget-пакет загружается так: “Install-Package StemmersNet”

Если есть замечания, предложения – welcome!