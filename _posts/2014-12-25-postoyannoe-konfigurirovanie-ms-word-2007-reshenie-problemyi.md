---
id: 1453
title: 'Постоянное конфигурирование MS Word 2007 (решение проблемы)'
date: '2014-12-25T17:50:18+00:00'
author: serge
layout: post
guid: 'http://sotnyk.com/?p=1453'
permalink: /2014/12/25/postoyannoe-konfigurirovanie-ms-word-2007-reshenie-problemyi/
---

[![fixitbutton1](https://sotnyk.github.io/wp-content/uploads/2014/12/fixitbutton1.jpg)](https://sotnyk.github.io/wp-content/uploads/2014/12/fixitbutton1.jpg)Последнее время появилась одна проблемка, до которой никак не доходили руки. MS Word 2007 (я все еще им пользуюсь) начал при каждом запуске стартовать процедуру конфигурирования, которая занимает на моем компе до минуты. К этому моменту обычно уже спешишь что-то сделать, а потом забываешь о проблеме до следующего запуска.

Быстро в русскоязычном интернете ничего не нашел, поэтому решил дать описание того, что сработало у меня. Может еще кому пригодится. Найдено в ответ на запрос “ms word 2007 configures every time i open it”

Итак, нужно запустить в режиме администратора консоль. Для этого вбиваем в поиск Windows (в Windows 7 для этого нужно просто начать что-то вводить, после нажатия на кнопку Win, в Windows 8 просто начинаем печатать) сочетание “cmd”. Появившийся пункт кликаем правой кнопкой мышки и выбираем “Запустить как администратор” (у меня под руками только английская винда, поэтому скриншот с англоязычным меню):

[![CmdAdmin](https://sotnyk.github.io/wp-content/uploads/2014/12/CmdAdmin1.jpg)](https://sotnyk.github.io/wp-content/uploads/2014/12/CmdAdmin1.jpg)

Соглашаемся с запросом на запуск программы от администратора.

И теперь, копируем из этого поста такую строку:

`reg add HKCUSoftwareMicrosoftOffice12.0WordOptions /v NoReReg /t REG_DWORD /d 1`

Вставляем её в консоль (клик правой кнопкой мыши, “вставить”):

[![CmdPaste](https://sotnyk.github.io/wp-content/uploads/2014/12/CmdPaste-300x152.jpg)](https://sotnyk.github.io/wp-content/uploads/2014/12/CmdPaste.jpg)

Нажимаем “ОК”. У меня после этого все заработало нормально.

Для MS Office 2010 рекомендуются аналогичные действия, только используется такая строка-команда:

`reg add HKCUSoftwareMicrosoftOffice14.0WordOptions /v NoReReg /t REG_DWORD /d 1`

Источники:

- [http://answers.microsoft.com/en-us/office/forum/office\_2007-word/my-word-2007-has-to-configure-every-time-i-use-it/c339771b-ee0b-4cad-8e5e-bfb303686247](http://answers.microsoft.com/en-us/office/forum/office_2007-word/my-word-2007-has-to-configure-every-time-i-use-it/c339771b-ee0b-4cad-8e5e-bfb303686247)
- <http://support.microsoft.com/kb/2528748>