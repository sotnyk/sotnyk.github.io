---
id: 1589
title: 'Немножко о Go'
date: '2016-03-20T22:10:12+00:00'
author: serge
layout: post
guid: 'http://sotnyk.com/?p=1589'
permalink: /2016/03/20/nemnozhko-o-go/
---

[![6117521](https://sotnyk.github.io/wp-content/uploads/2016/03/6117521.png)](https://sotnyk.github.io/wp-content/uploads/2016/03/6117521.png)На выходных стало интересно попробовать решить одну и ту же задачу на нескольких языках программирования, которые не являются моими основными. Была выбрана игра крестики-нолики. Консольный вариант, чтобы не заморачиваться с GUI.

В процессе этого выяснилось, что появилась (или я только до неё добрался) довольно приличная бесплатная среда разработки под него. Это IDEA (как раз вышла версия 2016.1) в бесплатной редакции Community Edition и плагин под Go к ней.

Получаем вполне современную среду с подсветкой, минимальными возможностями рефакторинга (самое главное, чего мне не хватало – переименования идентификаторов), отладкой и т.п.  
Для тех, кто «нулевой» в Go, алгоритм установки такой:

1. Устанавливаем [Go (сейчас это версия 1.6)](https://golang.org/dl/). Ставим все по умолчанию, не меняем путь, по которому происходит установка.
2. Устанавливаем [Java JDK](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html). По идее, для запуска IDEA достаточно будет только Java SE Runtime, но на всякий случай лучше установить именно JDK – мало ли, что там потребуется в будущем.
3. Загружаем [IntelliJ IDEA Community Edition](https://www.jetbrains.com/idea/#chooseYourEdition). Устанавливаем. Тоже все по умолчанию.
4. Теперь, после запуска IDE, выбираем пункт Settings/Plugins.  
    [![IDEA-start](https://sotnyk.github.io/wp-content/uploads/2016/03/IDEA-start.jpg)](https://sotnyk.github.io/wp-content/uploads/2016/03/IDEA-start.jpg)
5. В диалоге plugins выбираем кнопку Browse repositories.  
    [![IDEA-plugins](https://sotnyk.github.io/wp-content/uploads/2016/03/IDEA-plugins.jpg)](https://sotnyk.github.io/wp-content/uploads/2016/03/IDEA-plugins.jpg)
6. Здесь находим плагин Go (категория custom languages) и нажимаем на install. У меня в скриншоте нет этой кнопки просто потому, что он уже стоит.  
    [![IDEA-repositories](https://sotnyk.github.io/wp-content/uploads/2016/03/IDEA-repositories.jpg)](https://sotnyk.github.io/wp-content/uploads/2016/03/IDEA-repositories.jpg)

В принципе все. Можно создать свой проект, либо отрыть уже существующий. Будут еще запрошены пути к Go SDK (который по умолчанию c:go) и еще один раз нужно будет установить GOPATH. Хорошая статья насчет GOPATH для новичков: [Всё, что вы хотели знать про GOPATH и GOROOT](https://habrahabr.ru/post/249545/).

И еще один момент у меня был, который отнял некоторое время. Никак не мог понять, почему не запускается консольный проект – вроде бы и main есть и пути все установил… Оказалось, забыл, что в приложении запуск осуществляется для функции main, находящейся в пакете main. А имя пакета которое подставляется по умолчанию, соответствует имени каталога проекта.

Чтобы запустить юнит-тесты – добавьте соответствующую конфигурацию проекта.

[![project-configuration](https://sotnyk.github.io/wp-content/uploads/2016/03/project-configuration.jpg)](https://sotnyk.github.io/wp-content/uploads/2016/03/project-configuration.jpg)

[![project-add-test-configuration](https://sotnyk.github.io/wp-content/uploads/2016/03/project-add-test-configuration.jpg)](https://sotnyk.github.io/wp-content/uploads/2016/03/project-add-test-configuration.jpg)

Вот в общем-то все. Удачи начинающим гоферам!