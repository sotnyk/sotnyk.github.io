---
id: 1495
title: 'Интересные фреймворки/либы'
date: '2015-06-15T14:44:28+00:00'
author: serge
layout: post
guid: 'http://sotnyk.com/?p=1495'
permalink: /2015/06/15/interesnyie-freymvorki-libyi/
---

[![akkalogo](http://localhost/wp-content/uploads/2015/06/akkalogo.png)](http://localhost/wp-content/uploads/2015/06/akkalogo.png)Как-то давно я не писал на темы разработки. Она случается, хотя и сильно “отвлекают” менеджерские вопросы. Опишу, прежде всего для себя, что было в последнее время. Может кому-то тоже будет интересно.

Возникла задача, которая потребовала переместить обработку в дочерние процессы. Такой подход имеет следующие преимущества:

- Если есть код (особенно native), который временами падает/зависает/склонен к утечкам, мы можем воспользоваться помощью OS, которая вычистит все за упавшим процессом. Да, лучше писать код надежно, но пока есть унаследованный код, приходится как-то с этим жить.
- Можно комбинировать 32/64 код (в основном важно для native-участков старого кода).
- Можно запускать параллельно код, который рассчитан на работу в однопоточном режиме. Не всегда, конечно, но в моем случае было именно так.

Конечно, изобретать свой велосипед не хотелось, поэтому посмотрел, что уже сделано.

<http://www.crawler-lib.net/child-processes> – интересная библиотека. Для общения с дочерним процессом используется WCF/Namedpipes.

[akka.net](http://getakka.net/). Для меня этот фреймворк оказался открытием. Портированный с Java, он позволяет пощупать Actor-ориентированное программирование на C# и ощутить вкус Erlang в привычной языковой среде. Открытие было настолько впечатляющим, что прошел курс молодого бойца (можно подписаться на письма, в которых приходит по уроку – <https://petabridge.com/bootcamp/>, либо загрузить целиком на гитхабе – <https://github.com/petabridge/akka-bootcamp>). Если есть время – рекомендую изучить. Вещь необычная и красивая.

Сама библиотека активно развивается и поддерживается. В основном 2 человека из Petabridge. Они же хорошо отвечают на вопросы на StackOverflow – думаю, они отслеживают все, что там помечено как akka.net. Так что проект довольно живой.

Разбираясь с akka, набрел на интересный фреймворк – <https://github.com/Topshelf/Topshelf>. Он используется в примерах. Позволяет достаточно просто создавать виндовс-сервисы. Жаль, что он попался мне уже после того, как встраивал в стандартный сервис, созданный визардом VS, возможность регистрироваться с разными именами. С учетом того, что имя является статическим полем, генерируемым редактором студии, обход этого был не слишком красивым, а все переписать с нуля не решился. С Topshelf все было бы намного проще. Так что оставлю себе на закладку на будущие проекты.

Возвращаясь к задаче многопроцессной обработки – в конце концов, был написан свой велосипед )). Что-то было взято от child-processes (watchdog-таймеры с обоих сторон), что-то у akka (обмен небольшими сообщениями с заданиями и результатами). При этом механизм общения простейший – через стандартный ввод-вывод. Поскольку все происходит на локальном компьютере, это существенно упростило конфигурирование и перечень необходимых компонентов. Так что получился велосипед, который все же является эволюционным развитием (или упрощением) разных подходов.