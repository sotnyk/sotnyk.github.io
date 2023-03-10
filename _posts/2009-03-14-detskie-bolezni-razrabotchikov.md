---
id: 28
title: 'Детские болезни разработчиков'
date: '2009-03-14T13:32:07+00:00'
author: serge
layout: post
guid: 'http://sotnyk.com/?p=28'
permalink: /2009/03/14/detskie-bolezni-razrabotchikov/
---

Очень часто, совсем немного взглянув на демонстрационное или учебное приложение, написанное начинающим разработчиком, можно сразу же сказать, что оно написано новичком. Некоторые вещи, которые сразу же бросаются в глаза, постараюсь перечислить ниже:

1\. Еще до просмотра кода, обычно запускается GUI. У новичков часто форма (а новички любят именно формы, а не консоль, даже если взаимодействие с пользователем отсутствует) может изменять размер, раскрываться на полный экран, но… Все контролы при этом оказываются прилепленными к левому верхнему углу формы, и нет никакой адаптации к изменению размера. Смотрится очень беспомощно.

Возможные решения:

Слабое, но терпимое: заблокируйте изменение размера формы.

Самое правильное: сделайте “резиновый” дизайн, используя помещение контролов на панели и придание им (и панелям и контролам) различных значений свойств Dock и Anchor. В тонких случаях также бывает нужным использовать Margin, MaximumSize, MinimumSize. Это для WinForms. Для WPF используйте различные менеджеры раскладки – StackPanel, Grid, etc.

2\. Заглянем в код. Как только находим что-то типа:

```private void button1\_Click(object sender, EventArgs e)```

или

```zg1.Invalidate()```,

хватаемся за голову. Каждый контрол, ссылка на который встречается в тексте программы, должен быть назван. Я правда встречал одного достаточно опытного разработчика, у которого на форме были button1…button10, и это было принципиально. Но это скорее исключение. Да и отношение к делу (как мне кажется). В любом случае, такие моменты очень настораживают, поскольку затрудняют чтение кода через месяц, год. И если разработчик об этом не задумывается, то начинает об этом задумываться тот, кто работает с ним.

3\. Позиция окна, которое не раскрыто на весь экран. Новички, как правило, оставляют значение по умолчанию – ```StartPosition = WindowsDefaultPosition```. Лучше использовать ```StartPosition=CenterScreen``` или ```CenterParent```.

P.S.: Если еще на что-то обращу внимание, допишу в этот блог, но первые две болезни бросаются в глаза сразу же.