---
id: 753
title: 'Проект Eclipse-Android и SVN'
date: '2011-06-13T10:28:21+00:00'
author: serge
layout: post
guid: 'http://sotnyk.com/?p=753'
permalink: /2011/06/13/proekt-eclipse-android-i-svn/
---

![](http://localhost/wp-content/uploads/2011/06/eclipse_svn.jpg "eclipse svn")У любого программиста возникает необходимость копировать проект на другой компьютер, делать архивные копии, совместно работать с другими программистами. Я все эти цели достигаю, используя Subversion a.k.a. SVN. Одним из первых же вопросов, при работе с этой системой хранения версий, является такой – «что нужно хранить в хранилище, а что возникает само». Данные, которые генерируются средой разработки, компилятором или являются специфическими для компьютера, хранить не только не нужно, но и вредно – трафик увеличивается, начинаешь мешать другим пользователям, затирая их настройки.

Методом «научного тыка» пришел к следующему списку того, что не нужно помещать под SVN для Eclipse-проекта:

1\. Корневая папка workspace .metadata.  
2\. Папки bin внутри проектов.  
  
Теперь перенос android-проекта на новый компьютер выглядит следующим образом:

1\. Делаем Update SVN-хранилищу. Я пока использую привычный мне TortoiseSVN прямо из проводника.  
2\. Запускаем Eclipse.  
3\. Создаем WorkSpace для проекта – я пока его создаю прямо в корневой папке проектов. Не знаю, насколько это правильно.  
4\. Делаем File/Import/General/Existing Projects into Workspace. Выбираем загруженные проекты и добавляем их в текущий WorkSpace без копирования.  
[![](http://localhost/wp-content/uploads/2011/06/import-256x300.png "import")](http://localhost/wp-content/uploads/2011/06/import.png)  
[![](http://localhost/wp-content/uploads/2011/06/import2-247x300.png "import2")](http://localhost/wp-content/uploads/2011/06/import2.png)  
5\. У каждого Workspace свои настройки, поэтому нужно добавить правильный путь к Android SDK (Window/Preferences/Android/SDK Location).  
[![](http://localhost/wp-content/uploads/2011/06/preferences_android-300x256.png "preferences_android")](http://localhost/wp-content/uploads/2011/06/preferences_android.png)  
[![](http://localhost/wp-content/uploads/2011/06/preferences_android_set-300x256.png "preferences_android_set")](http://localhost/wp-content/uploads/2011/06/preferences_android_set.png)  
6\. И, наконец, в свойствах проекта почему-то по умолчанию подставляется неправильная версия компилятора. Это выражается сообщением об ошибке: «Android requires compiler compliance level 5.0. Please fix project properties». Чтобы исправить ситуацию, заходим в свойства проекта, Java Compiler и назначаем версию 1.6 компилятора всему WorkSpace (ссылка Configure Workspace Settings). После этого делаем Project / Clean, чтобы IDE перекомпилировала все с новыми настройками.  
[![](http://localhost/wp-content/uploads/2011/06/compiler_options-300x230.png "compiler_options")](http://localhost/wp-content/uploads/2011/06/compiler_options.png)

Все, можем работать. Теперь достаточно делать Update/Commit в SVN, добавлять новые файлы и проекты (при добавлении новых проектов придется повторить процедуру импорта).

Чуть позже планирую посмотреть, что можно сделать через плагины к Eclipse (subclipse). Возможно, это будет удобнее, чем добавлять все руками. Но я уже буду знать немного внутренности этой кухни.