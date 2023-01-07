---
id: 222
title: 'Есть ли жизнь на планете Flex? Часть 0 &#8211; точим топор.'
date: '2010-07-25T20:52:27+00:00'
author: serge
layout: post
guid: 'http://sotnyk.com/?p=222'
permalink: /2010/07/25/est-li-zhizn-na-planete-flex-chast-0/
---

*“If I had eight hours to chop down a tree,  
I’d spend six hours sharpening my ax”  
Abraham Lincoln.*

«Если бы у меня было восемь часов для того,  
чтобы срубить дерево, я бы шесть часов потратил  
на заточку моего топора».  
Авраам Линкольн. Президент США, в молодости лесоруб.

Давненько не менял свой рабочий инструмент – C#. Уже больше пяти лет пишу практически только на нем. Так и «заржаветь» недолго. Но прежде всего, нужно заточить топор – инструмент, на котором будем тренироваться. Я опишу этот процесс для тех, кто работает в Windows.  
  
Посоветовавшись с людьми, которые с Flex-ом сталкивались поболе моего, решил для начала остановиться на бесплатной среде FlashDevelop. Кстати, удивительно, но она написана на C# :-). И даже должна работать в Linux на Mono (нужно будет попробовать потом и в Убунте моей любимой). Найти ее можно по адресу: <http://www.flashdevelop.org>. Заходим на страничку загрузки данного продукта и смотрим, что нам еще понадобится. А понадобится (помимо собственно IDE FlashDevelop) следующее:

Java 1.6 и выше – берем со странички SUN: <http://www.java.com/ru/download/index.jsp>. Насколько я понимаю, достаточно JRE, пакета для разработчика, который гораздо массивнее, скорее всего не требуется.

Дебажный проигрыватель SWF-файлов: http://www.adobe.com/support/flashplayer/downloads.html Ищите здесь его по слову debug и projector.

И последний пререквизит – бесплатный Flex SDK от Adobe: <http://opensource.adobe.com/wiki/display/flexsdk/Flex+SDK>. На сегодняшний день последним был следующий: [http://fpdownload.adobe.com/pub/flex/sdk/builds/flex4/flex\_sdk\_4.1.0.16076.zip](http://fpdownload.adobe.com/pub/flex/sdk/builds/flex4/flex_sdk_4.1.0.16076.zip)

Не упоминаемый на сайте FlashDevelop .NET Framework 2.0 сейчас есть практически в каждом доме каждой системе, но если вы обладатель только что установленной Windows XP, то возможно, нужно будет поставить и его, хотя, скорее всего, инсталлятор проставит его, загрузив с сайта Майкрософт. Этот вопрос я не проверял, поскольку у меня на всех компах .NET Framework уже стоит в последней редакции.

Итак, считаем, что у нас уже все это есть. Порядок установки (как мне представляется, он самый простой) следующий:

1\. Устанавливаем JAVA.  
2\. Распаковываем ZIP-архив с FLEX4.1 SDK в какой-то каталог (у меня это D:Flex).  
3\. Устанавливаем FlashDevelop (я поставил с параметрами по умолчанию).  
4\. Запускаем среду FlashDevelop.  
[![New Project Screenshot](http://localhost/wp-content/uploads/2010/07/NewProject.png "New Project")](http://localhost/wp-content/uploads/2010/07/NewProject.png)  
5\. Создаем новый проект (типа ActionScript3).  
6\. Компилируем его (F8).  
7\. Заходим в эксплорере в папку проекта bin, находим там SWF-файл и кликаем над ним правой кнопкой мыши. Переходим в диалог выбора программы по умолчанию.  
 [![Chose Default Application](http://localhost/wp-content/uploads/2010/07/ChoseApp.png "Chose Default Application for SWF")](http://localhost/wp-content/uploads/2010/07/ChoseApp.png)  
8\. Выбираем в качестве программы по умолчанию тот дебажный плейер от Adobe, который загрузили ранее.  
9\. При выскакивании окошек брендмауэра, разрешаем в них и FlashDevelop и дебажный плейер.

После выполнения этих шагов, при нажатии на F5, запускается проект, созданный по умолчанию, срабатывает точка останова, показываются значения переменных. Вроде бы все готово для дальнейшей работы. Загружал я и эталонную среду FlashBuilder (демо позволяет её использовать 60 дней) – должен отметить, что если вы ранее работали в Visual Studio, то FlashDevelop будет вам более привычен. Более похожий интерфейс, те же клавиатурные сокращения. Все-таки Eclipse есть Eclipse. Даже причесанный Adobe, это немного другая галактика, к которой нужно привыкать.

Но есть еще один важный момент для начинающих, который хотелось бы упомянуть. Это литература. Я для ознакомления затянул себе достаточно много разной литературы. Самым лучшим учебником (именно учебником, а не справочником), мною был признан “ActionScript 3.0 для Flash. Подробное руководство. Колин Мук”.  
[![AS3 for Flash book](http://localhost/wp-content/uploads/2010/07/AS3ForFlashBook.png "AS3 for Flash book")](http://localhost/wp-content/uploads/2010/07/AS3ForFlashBook.png)  
Пока у меня есть только электронный вариант, но ознакомившись, я, конечно же, куплю для подробного чтения бумажный вариант. Куплю в магазине DiaMail ([http://diamail.com.ua/cgi-bin/annot.cgi?id=3104&amp;from=bruht](http://diamail.com.ua/cgi-bin/annot.cgi?id=3104&from=bruht)). Не сочтите за скрытую рекламу – просто я здесь часто покупаю. Доставляют быстро (если выбирать опцию «Через службу ‘Новая Почта’» – обычная почта работает как обычно, т.е., безобразно долго и дорого), а цены несколько ниже, чем в других магазинах. Все это касается Украины.

Ну что же, можно приступать к работе. Продолжение следует… наверное…