---
id: 1306
title: 'Небольшая настройка Octave'
date: '2013-05-18T16:29:23+00:00'
author: serge
layout: post
guid: 'http://sotnyk.com/?p=1306'
permalink: /2013/05/18/nebolshaya-nastroyka-octave/
---

![](http://localhost/wp-content/uploads/2013/05/ml-octave-logo.jpg "ml-octave-logo")Как я уже писал, прохожу потихоньку курс [Machine Learning](https://class.coursera.org/ml-003/class/index). В качестве среды для программирования здесь используется бесплатный аналог Matlab – [Octave](http://www.gnu.org/software/octave/). Столкнулся с парой моментов, который забудутся со временем, если не записать. Так что это скорее записки для себя – но может еще кому-то пригодится. Если будет еще что-то, буду пополнять данный пост.

**Момент 1.** Задания, которые строили графики, у меня упорно отказывались их показать. Диаграммы показывались на мгновение, иногда мелькали при перезапуске, но в обычном режиме окно с диаграммой было пустым, курсов над ним крутился как над неотвечающим окном и в его тайтле писалось: “Not responding”.

Как оказалось, проблема в модуле oct2mat. Я по старой привычке установил октаву полностью, а не тот минимум, что был указан на сайте курсов. Проблема решилась после того, как в консоли Октавы была выполнена команда:

`pkg unload oct2mat`

**Момент 2.** В консоли Октавы достаточно длинный промт:

`octave-3.2.4.exe:1>`

Чтобы его укоротить, можно применить команду PS1:

`octave-3.2.4.exe:1> PS1('>> ');<br></br>>>`

Чтобы не вводить её каждый раз при старте среды, можно поместить её в один из [Startup-файлов](http://www.gnu.org/software/octave/doc/interpreter/Startup-Files.html). У себя я добавил её в “C:Octave3.2.4\_gcc-4.4.0shareoctavesitemstartupoctaverc”.