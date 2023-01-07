---
id: 1523
title: 'Раздражающая нотификация в Android'
date: '2015-09-27T11:36:27+00:00'
author: serge
layout: post
guid: 'http://sotnyk.com/?p=1523'
permalink: /2015/09/27/razdrazhayushhaya-notifikatsiya-v-android/
---

[![android-app-info](http://localhost/wp-content/uploads/2015/09/android-app-info-300x256.jpg)](http://localhost/wp-content/uploads/2015/09/android-app-info.jpg)Я уже писал немножко про курс “Programming Mobile Applications for Android Handheld Systems”. Еще летом закончил [вторую его часть](https://www.coursera.org/course/androidpart2). Вторая часть была поинтереснее, чем первая, вынес уже некоторые полезные вещи. Хотя, все-равно, еще некоторые вещи остаются в старом стиле. Например, пока еще не находил в курсах, чтобы использовался Data Binding ([статья 1](http://habrahabr.ru/post/260317/), [статья 2](http://habrahabr.ru/company/dataart/blog/267735/)). Все же явная работа через findByViewId() выглядит как-то не совсем “кошерно” на фоне победившего везде подхода MVC (MVVM, MVP) и преобладания декларативного описания связей между контролами.

Но вот недавно всплыла проблема, которая освещалась в тех курсах. Бывают приложения, которые навязчиво вставляют нотификацию рекламного характера. Мелочь, но раздражает. В таких случаях даже не понятно, что за приложение её генерирует. Так вот, оказывается с версии Android 4.1, элемент нотификации реагирует на длительное нажатие – всплывет пункт App info (как на скриншоте выше). Выберите его и вы перейдете в информацию о приложении, которое сгенерировало данную нотификацию. Более того, можно легко выключить (правда уже все виды нотификаций) от этого приложения, сняв галочку, как показано ниже:

[![how-to-turn-off-notification-on-individual-android-apps](http://localhost/wp-content/uploads/2015/09/how-to-turn-off-notification-on-individual-android-apps-300x267.jpg)](http://localhost/wp-content/uploads/2015/09/how-to-turn-off-notification-on-individual-android-apps.jpg)

Нажмите на картинку, чтобы увидеть крупно.

За сим откланиваюсь