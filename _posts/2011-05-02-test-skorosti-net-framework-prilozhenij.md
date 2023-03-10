---
id: 638
title: 'Тест скорости работы .NET Framework приложений на смешанных платформах'
date: '2011-05-02T22:27:10+00:00'
author: serge
layout: post
guid: 'http://sotnyk.com/?p=638'
permalink: /2011/05/02/test-skorosti-net-framework-prilozhenij/
---

![](https://sotnyk.github.io/wp-content/uploads/2011/05/NetFramework.jpg "NetFramework") 

Не секрет, что сборки, которые используются в большом количестве проектов, как правило, имеют более низкую версию .NET Framework, чем основное приложение. Типичное на сегодняшний день состояние – основное приложение использует 4-ю версию, а какая-то невизуальная библиотека классов – 2-ю. Сравнительно недавно услышал мнение, что использование в одном приложении нескольких версий, замедляет работу приложения, поскольку требует определенной работы при «проходе» в код с другой версией. Проверим?  
Идея простая. Один и тот же код (отличающийся только именем класса), помещаем в 3 места – в сборку приложения (4-й фреймворк), в другую сборку с 4-м фреймворком и в другую сборку с 3-м фреймворком. Вот код класса:  

```csharp
public class V2  
{  
 public static Int64 Sum(Int64 a, Int64 b)  
 {  
 return a + b;  
 }  
}  
```

В основном приложении (консольное) выполняется его вызов 100 000 000 раз внутри следующего цикла:  

```csharp
Int64 tmp = 0;  
for (int i = 0; i &lt; Repeats; ++i)  
{  
 tmp = V2.Sum(-tmp, i);  
}  
```

Во избежание отброса переменной tmp оптимизатором, производится её вывод на консоль, который я приводить не буду. Также здесь опущен шаг предварительного вызова класса, чтобы этап инициализации не сказывался на хронометраже. Все это можно посмотреть в [полном исходнике тестового проекта](https://sotnyk.github.io/code/V4andV2SpeedTest.rar).

Теперь, что получили. Каждый тест я выполнял в цикле 3 раза и первый проход опущу как самый нестабильный. Приведу типичные показания. Компилировалось в релизный проект, на время запуска студия выгружалась из памяти.

**Результат 1**. Windows XP 32бита. Процессор Pentium Dual Core E2160 1.8GHz.

`Same assembly calls time: 765,625 msec<br></br>V4 -- V4 calls time: 781,25 msec<br></br>V4 -- V2 calls time: 765,625 msec<br></br>Same assembly calls time: 765,625 msec<br></br>V4 -- V4 calls time: 781,25 msec<br></br>V4 -- V2 calls time: 765,625 msec`  
В общем, без особых отличий, но вот только вызов в другой сборке с 4-й версией фреймворка почему-то выполнялся слегка медленнее.

**Результат 2**. Windows 7 64 бита. Процессор i5-460M, 2.53GHz

`Same assembly calls time: 87,005 msec<br></br>V4 -- V4 calls time: 89,0051 msec<br></br>V4 -- V2 calls time: 86,0049 msec<br></br>Same assembly calls time: 88,005 msec<br></br>V4 -- V4 calls time: 85,0048 msec<br></br>V4 -- V2 calls time: 86,0049 msec`  
Тоже примерно одинаково, только существенно быстрее, чем на первой машине. Сказывается и разрядность OS при работе с 64-битными числами, да и просто процессор быстрее.

Мои выводы – перевод всех сборок в самую высокую версию фреймворка ничего не даст. По крайней мере, в плане скорости работы приложения в установившемся режиме. Результаты могут отличаться на других платформах (мобильные, сильверлайт, mono). Тут проверю, когда возникнет необходимость.