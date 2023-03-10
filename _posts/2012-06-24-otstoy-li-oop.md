---
id: 1197
title: 'Отстой ли ООП?'
date: '2012-06-24T22:01:25+00:00'
author: serge
layout: post
guid: 'http://sotnyk.com/?p=1197'
permalink: /2012/06/24/otstoy-li-oop/
---

![](https://sotnyk.github.io/wp-content/uploads/2012/06/Niklaus_Wirth.jpg "Niklaus Wirth")
<sub><sup>Никлаус Вирт во время визита в Россию (Уральский университет, 2005 год)</sup></sub>

Некоторое время назад, я перевел [эссе Джо Армстронга – “Почему ООП – полный отстой”](https://sotnyk.github.io/?p=1168). Обещал дать свои комментарии по этому поводу. Выполняю.

Безусловно, Джо Армстронг, являющийся архитектором языка Erlang, говорит важные вещи. Но, говорит это человек, который не является специалистом в ООП, поэтому их нужно воспринимать с некоторым пониманием. В объектно-ориентированном стиле можно писать ужасные программы. Можно делать “божественные” классы, можно реализовывать антипаттерн “паблик Морозов” и еще тысячей способов делать так, чтобы программу сложно было поддерживать, модифицировать, анализировать.

Но ведь хорошие программисты пишут не так? Искусство создания правильной архитектуры как раз заключается в том, чтобы внутренние состояния объектов не мешали, а помогали спрятать сложность, существуют определенные договоренности, где помещать определения, какую схему именований использовать. Более строгая аскета в лице ограничений [компонено-ориентированного программирования](http://ru.wikipedia.org/wiki/%D0%9A%D0%BE%D0%BC%D0%BF%D0%BE%D0%BD%D0%B5%D0%BD%D1%82%D0%BD%D0%BE-%D0%BE%D1%80%D0%B8%D0%B5%D0%BD%D1%82%D0%B8%D1%80%D0%BE%D0%B2%D0%B0%D0%BD%D0%BD%D0%BE%D0%B5_%D0%BF%D1%80%D0%BE%D0%B3%D1%80%D0%B0%D0%BC%D0%BC%D0%B8%D1%80%D0%BE%D0%B2%D0%B0%D0%BD%D0%B8%D0%B5), делает классы еще менее хрупкими. [Аспектно-ориентированное программирование](http://ru.wikipedia.org/wiki/%D0%90%D1%81%D0%BF%D0%B5%D0%BA%D1%82%D0%BD%D0%BE-%D0%BE%D1%80%D0%B8%D0%B5%D0%BD%D1%82%D0%B8%D1%80%D0%BE%D0%B2%D0%B0%D0%BD%D0%BD%D0%BE%D0%B5_%D0%BF%D1%80%D0%BE%D0%B3%D1%80%D0%B0%D0%BC%D0%BC%D0%B8%D1%80%D0%BE%D0%B2%D0%B0%D0%BD%D0%B8%D0%B5) позволяет централизовать рассыпанную по коду функциональность. [Предметно-ориентированное программирование](http://ru.wikipedia.org/wiki/%D0%9F%D1%80%D0%B5%D0%B4%D0%BC%D0%B5%D1%82%D0%BD%D0%BE-%D0%BE%D1%80%D0%B8%D0%B5%D0%BD%D1%82%D0%B8%D1%80%D0%BE%D0%B2%D0%B0%D0%BD%D0%BD%D1%8B%D0%B9_%D1%8F%D0%B7%D1%8B%D0%BA_%D0%BF%D1%80%D0%BE%D0%B3%D1%80%D0%B0%D0%BC%D0%BC%D0%B8%D1%80%D0%BE%D0%B2%D0%B0%D0%BD%D0%B8%D1%8F) позволяет приблизить язык программирования к языку, на котором общаются специалисты предметной области.

Есть еще масса приемов и технологий, которые позволяют заработать ООП. Наверняка, масса подобных вещей есть и в функциональном программировании. И Джо Армстронг об этом знает. А вот желание поглубже изучить ООП – отсутствует. Возможно, он прав – лучше потратить свое время на улучшение своего детища – Erlang, чем углубляться в то, что непосредственно вряд ли применится здесь. Но, таким образом можно лишиться возможности адаптировать удачную идею у конкурента (если, конечно, в области языков еще не стали вестись ненавистные всем прогрессивным человечеством патентные войны).

И в общем, жалобы Джо Армстронга мне чем-то напоминают жалобы глубоко уважаемого мною [Никлауса Вирта](http://ru.wikipedia.org/wiki/%D0%92%D0%B8%D1%80%D1%82,_%D0%9D%D0%B8%D0%BA%D0%BB%D0%B0%D1%83%D1%81) на оператор “=” в языке C, которые повторяются [снова и снова](http://citforum.ru/programming/digest/wirth/) (сама статья, кстати, очень познавательная). Но у меня вызывают скорее легкую улыбку понимания того, что вот здесь “личная неприязнь детектед”. Подобные вещи присутствуют и у меня, и у любого другого человека. И очень тяжело заставить себя изучить то, что чем-то не нравится.