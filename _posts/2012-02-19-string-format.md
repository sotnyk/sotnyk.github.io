---
id: 1080
title: string.Format
date: '2012-02-19T23:05:29+00:00'
author: serge
layout: post
guid: 'http://sotnyk.com/?p=1080'
permalink: /2012/02/19/string-format/
---

Сравним два фрагмента кода на C#, выполняющих одно и то же:

```csharp
int i = 15;  
string s1 = "i=" + i; // Фрагмент 1  
string s2 = string.Format("i={0}", i); // Фрагмент 2  
```

Во многом, использование первого или второго варианта является делом вкуса. Кому как нравится. Но есть одна деталь, которая не дает мне покоя. Возьмем такой вот фрагмент:

```csharp
int i = 15;  
string s2 = string.Format("i={1}", i);  
```

Этот код отлично откомпилируется, но при попытке запустить его, получим Exception (FormatException). Все просто – строка, находящаяся внутри первого параметра Format, является фактически скриптом. Простейшим, но разбираемым во время исполнения программы. Поэтому компилятор ничем нам не может помочь. А такие опечатки вполне реальны (у меня бывали), особенно, когда количество параметров в строке намного больше единицы.

Конечно, это далеко не единственный случай, когда внутри компилируемого языка со строгой типизацией, используется другой, скриптовый фрагмент. Можем вспомнить те же Regex, биндинги в WPF, тип слушателя Trace, задаваемый в App.config. Все это куски с повышенной опасностью – тут компилятор не подстрахует. Хотя и гибко, конечно, тоже. И иногда не обойтись. В общем, выбирайте сами, что вам удобнее, но я вас предупреждал…