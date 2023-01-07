---
id: 1879
title: 'Об AutoML-Zero'
date: '2020-04-19T20:02:29+00:00'
author: serge
layout: post
guid: 'https://sotnyk.ga/?p=1879'
permalink: /2020/04/19/ob-automl-zero/
---

Пост [Анатолия Левенчука об искусственной эволюции](https://ailev.livejournal.com/1512741.html) затронул тему, которая меня всегда интересовала и продолжает волновать.

Центральной темой поста стала статья группы авторов из Google Research: Evolving Normalization-Activation Layers (<https://arxiv.org/abs/2004.02967>). Статья очень интересная и стоит того, чтобы её прочесть (можно также поиграться с кодом, который там идет, но они предупреждают, что этот момент займет немало времени).

Из моих мысленных замечаний:

К сожалению, нет никакого обмена генетическим материалом между особями. Фактически, идет случайный (почти) поиск удачных решений, модифицированных из одного из уже имеющихся. Даже такой подход дал возможность получить многое из того, что изобрели люди. Но мне кажется, что этот путь будет сложно масштабировать на более сложные модели. Используемый метод изменения моделей имеет слишком высокую вероятность получить “урода”.

Здесь как в природе – сравнительно простые вирусы могут позволить себе такой уровень ошибок при репликации. У тех же коронавирусов, у каждого нового хозяина фактически свой собственный штамм, что затрудняет адаптацию иммунной системы, а также дает возможность иногда преодолевать межвидовой барьер. Но более сложные системы (даже бактерии) так не могут – при таком же уровне мутаций, они в большинстве своем, не дадут жизнеспособного потомства. Вот и используют уже другие, более сложные, но более устойчивые схемы. Поэтому предполагаю, что на уровне более сложных систем, AutoML-zero даст сбой.

Чтобы с нуля построить всю систему, сам алгоритм создания системы должен иметь возможность модифицировать рефлексивно и себя так же. Вполне возможно, что это должна быть даже не программа в общепринятом смысле (набор инструкций), а набор параллельно работающих фрагментов (автоматов), экспрессия (уровень активности) которых управляется каким-то ансамблевым алгоритмом.