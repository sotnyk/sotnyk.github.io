---
id: 332
title: 'Заработок в интернете $300-500 в день'
date: '2010-10-11T22:52:50+00:00'
author: serge
layout: post
guid: 'http://sotnyk.com/?p=332'
permalink: /2010/10/11/zarabotok-v-internete-300-500-v-den/
---

[![](https://sotnyk.github.io/wp-content/uploads/2010/10/roulette_wheel.jpg "Колесо рулетки")](https://sotnyk.github.io/2010/10/11/zarabotok-v-internete-300-500-v-den/roulette_wheel/)  
В интернете полно историй о том, как можно заработать в интернет-казино. Вынесенное в заголовок стянуто мною со следующей страницы: <http://vzarabotke.ru/>. Подозреваю, что скоро сайт может измениться (вот и автор называется то Евгений Гольдман, то Григорий Перельман), поэтому здесь вкратце опишу предлагаемую методику, а затем её проверку.  
  
Итак, собственно алгоритм (приведу с сокращениями):

*Начни со ставки в 1$ на красное (только 1$!!). Если выпадет черное, ставь на красное снова. В этот раз ставка должна быть 2$ (ты удваиваешь свою ставку). Если снова выпало черное – поставь 4$ (удвой последнюю ставку). Продолжай, пока не выпадет красное и ты в выигрыше! После выпадения красного ты получаешь ровно в два раза больше сделанной ставки. Таким образом при ставке в 16$ ты получаешь 32$, тем самым заработав 1$ (размер первоначальной ставки).*

ВАЖНО: Выиграв один раз с одним цветом, следуй такой же процедуре с другим. Начни со ставки в 1$ на второй цвет – если выиграл, поставьте на первый. Если проиграл, продолжай, удваивая, ставить на тот же цвет, пока не выиграешь. Всего через несколько минут ты увидишь, как существенно увеличится твой выигрыш.

Надеюсь принцип понятен? Выглядит все правдоподобно – ведь если мы будем каждый раз при проигрыше удваивать ставку, то когда-нибудь выиграем! Причем, выиграем больше, чем ставили до сих пор. Ничего, что выиграем всего лишь размер первоначальной ставки (все проигрыши идут в казино) – но зато гарантированно!

Автор методики правда замалчивает один факт – увеличение ставок не может продолжаться бесконечно. Оно ограничено либо имеющимися у нас средствами, либо лимитами, установленными в казино. На самом деле, это существенный факт.

Мне стало интересно, что получится в реальности, если применить предложенную стратегию. Честно скажу, мне лень было выводить результат аналитически – тем более, что вероятностные стратегии в формульном исполнении не являются моим коньком. Вместо этого было написано несложное приложение, которое решает задачу путем имитационного моделирования.

Да, еще один момент – перед тем, как написать приложение, я зарегистрировался на первом из предложенных “автором” методики сайте (это был <http://bigazart.com>), чтобы лучше ознакомиться с правилами игры – ведь я ни разу в казино не был… На этом сайте есть бесплатный режим, в котором можно потренироваться, что я и сделал. Но мне очень быстро надоело производить однотипные действия, приводящие к мизерному выигрышу (к вопросу демо-выигрыша я вернусь далее).

Само приложение и исходные коды к нему вы можете [загрузить здесь](https://sotnyk.github.io/code/RouletteStrategyTest.zip). Я не делал инсталлятор – чтобы запустить его, вам потребуется только .Net Framework 3.5 (он уже есть в Windows XP/Vista со всеми апдейтами или в Windows 7 изначально) и клик по exe-файлу внутри архива. Если по каким-то причинам у вас это микроприложение не запускается, то просто поверьте мне на слово.

После запуска мы можем задать правила казино. Сразу после запуска заданы правила, действующие в платном режиме европейской рулетки на сайте <http://bigazart.com> – минимальная ставка на чет-нечет / красное-черное (это эквивалентные варианты, доказывать здесь это я не буду) равна 1. Максимальная – 250. Первоначальная ставку выбираем равной минимальной, после неудачи удваиваем.

[![](https://sotnyk.github.io/wp-content/uploads/2010/10/roulette_start-300x143.png "Начальное состояние")](https://sotnyk.github.io/2010/10/11/zarabotok-v-internete-300-500-v-den/roulette_start/)

Для того, чтобы начать, нажимаем старт. Частота ставок сделана такой, чтобы было быстро, но глаз еще замечал динамику. Сколько я ни нажимал старт, в конце концов получал следующую картину (простите за английский интерфейс – такая уж привычка, “loss” – проиграл):

[![](https://sotnyk.github.io/wp-content/uploads/2010/10/roulette_finish-300x152.png "Обычное состояние через некоторое время")](https://sotnyk.github.io/2010/10/11/zarabotok-v-internete-300-500-v-den/roulette_finish/)

В данном случае, “игрок” еще долго продержался. Целых 462 вращения рулетки. Обычно все заканчивается быстрее. Если хочется, можно настроить другие параметры игры и нажать Reset, после чего повторить моделирование. Для того, чтобы посмотреть весь ход в текстовом виде, можно нажать кнопку “Copy to clipboard”, после чего посмотреть результат в любом текстовом редакторе.

При любых параметрах, у меня рано или поздно игрок выходил в минуса (проигрывал все). Так что же не так в предложенной методике?

Если обойтись без формул, то дело в следующем. Действительно, вероятность проиграть в длинной серии мала. Чем длиннее серия (у нас это ставки в 1, 2, 4, 8, 16, 32, 64, 128 – всего 8 градаций), тем вероятность меньше. С каждой новой ступенью в 2 раза. Но! Стоимость этого проигрыша тоже увеличивается в 2 раза! Поэтому, если бы все числа были бы четными или нечетными (красными/черными), то мы бы все время болтались бы около начальной ставки, медленно поднимаясь и изредка сильно опускаясь. Но у рулетки есть такой сектор, как Зеро, не принадлежащий к этим “половинам”. Время от времени он выпадает, делая вероятность выигрыша при каждом вращении чуть меньше половины. И этого достаточно, чтобы гарантировать казино общий выигрыш.

У проверяемой стратегии есть одно свойство – длительные периоды роста. Мне кажется, что именно они психологически захватывают игрока. Ведь вот, столько выигрываю, выигрываю, а тут разок проиграл. Ну да, много проиграл, но ведь разок…

И еще момент – в демо режиме я немного выиграл. Это правда. Возможно повезло – я ведь не могу так методично и долго нажимать на кнопки, как программа. Но, честно говоря, на месте казино, в демо режиме я бы обеспечил игрока большей вероятностью выиграть, чтобы он решился играть на реальные деньги. И здесь (если догадка верна) обвинять владельцев не в чем – ведь они ни в одном, ни в другом случае не будут играть против игрока, ухудшая его показатели. Просто в демо-режиме они ему могут немного помочь. А на деньги играть честно – ведь эта стратегия, как мы видим, ведет к их выигрышу.

Исходя из таких рассуждений, я подозреваю, что сайт <http://vzarabotke.ru/> с большой вероятностью работает на владельцев он-лайн казино, перечисленных на его единственной странице.

**Update 12.10.2010**: После личной переписки с Димой Прасковьиным, решил также добавить график усредненного (по 100000 запускам) количества денег при условии, что можно залезть в долг (в минуса).

[![](https://sotnyk.github.io/wp-content/uploads/2010/10/ManyRuns-300x210.png "Нажмите, чтобы посмотреть крупно.")](https://sotnyk.github.io/2010/10/11/zarabotok-v-internete-300-500-v-den/manyruns/)

По иксам – вращения рулетки, по игрекам – деньги. Исходники уже не буду выкладывать.