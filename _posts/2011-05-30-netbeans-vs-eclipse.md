---
id: 709
title: 'Разработка под Android: NetBeans vs. Eclipse'
date: '2011-05-30T17:14:13+00:00'
author: serge
layout: post
guid: 'http://sotnyk.com/?p=709'
permalink: /2011/05/30/netbeans-vs-eclipse/
---

![](https://sotnyk.github.io/wp-content/uploads/2011/05/NBvsEclipse-300x184.png "NetBeans vs. Eclipse")

Как я [обещал ранее](https://sotnyk.github.io/2011/05/28/i-snova-android/), бегло сравнил два бесплатных варианта среды разработки под Android.

1\. Более привычная для меня среда NetBeans 7.0 с плагином [nbandroid](http://plugins.netbeans.org/plugin/19545/nbandroid).  
2\. Непривычное мне по терминологии и тому, что где находится, «затмение». Оно же Eclipse 3.6. Основной плюс этой среды в том, что [плагин для разработчика](http://developer.android.com/sdk/eclipse-adt.html) Андроид-приложений написан самой Google.

Ну, что ж, давайте сравнивать с точки зрения новичка в Андроид (коим я пока являюсь).  
  
**Создание проекта.**

NetBeans:

[![](https://sotnyk.github.io/wp-content/uploads/2011/05/image005-300x208.png "Создание проекта NetBeans")](https://sotnyk.github.io/wp-content/uploads/2011/05/image005.png)  
[![](https://sotnyk.github.io/wp-content/uploads/2011/05/image007-300x208.png "Создание проекта NetBeans. 2-я страница.")](https://sotnyk.github.io/wp-content/uploads/2011/05/image007.png)

Здесь и далее, если вы хотите посмотреть скриншот крупнее, просто кликните на нем.

Eclipse:

[![](https://sotnyk.github.io/wp-content/uploads/2011/05/image009-139x300.png "Создание проекта для Андроид. Eclipse")](https://sotnyk.github.io/wp-content/uploads/2011/05/image009.png)

Второй шаг – предложение создать тестовый проект. Вещь полезная, но в самый первый раз пропустим. Все-равно, я еще почти не знаком с junit.

[![](https://sotnyk.github.io/wp-content/uploads/2011/05/image011-271x300.png "Создание тестового проекта для Android. Eclipse.")](https://sotnyk.github.io/wp-content/uploads/2011/05/image011.png)

На этом шаге набор опций очень похож, поэтому явного победителя нет.

**Полученные проекты имеют идентичную структуру:**

NetBeans

[![](https://sotnyk.github.io/wp-content/uploads/2011/05/image013-242x300.png "Сгенерированный в NetBeans Android-проект.")](https://sotnyk.github.io/wp-content/uploads/2011/05/image013.png)

Eclipse

[![](https://sotnyk.github.io/wp-content/uploads/2011/05/image015-185x300.png "Структура нового android-проекта в Eclipse.")](https://sotnyk.github.io/wp-content/uploads/2011/05/image015.png)

**А теперь попробуем открыть разные файлы.**

В случае открытия .java, особых отличий я не заметил. Но попробуем открыть ресурсы. В NetBeans мы видим обычный текстовый редактор XML-файлов.

[![](https://sotnyk.github.io/wp-content/uploads/2011/05/image017-300x156.png "Общий XML-редактов в NetBeans")](https://sotnyk.github.io/wp-content/uploads/2011/05/image017.png)

В то же время, в Eclipse мы получаем специализированные редакторы для соответствующего типа ресурса (которые, впрочем, имеют также и закладку, позволяющую вручную править и чистый XML).  
Манифест:  
[![](https://sotnyk.github.io/wp-content/uploads/2011/05/image019-300x258.png "Редактор манифеста. Eclipse.")](https://sotnyk.github.io/wp-content/uploads/2011/05/image019.png)

Строки:

[![](https://sotnyk.github.io/wp-content/uploads/2011/05/image021-300x259.png "Редактор строковых ресурсов. Eclipse.")](https://sotnyk.github.io/wp-content/uploads/2011/05/image021.png)

И, наконец, разметка визуального интерфейса:

[![](https://sotnyk.github.io/wp-content/uploads/2011/05/image023-300x259.png "GUI-editor for Android. Eclipse.")](https://sotnyk.github.io/wp-content/uploads/2011/05/image023.png)

Не знаю, кто как, но я пока не готов весь XML писать руками. Подправлять – да, конечно. Но все… да я просто не знаю пока всего, что можно. А хороший редактор «подсказывает» возможности, о которых еще не знаешь. В общем, в моем небольшом соревновании в плане приветливости к новичку, победил Eclipse. Значит, дальше буду работать с ним.