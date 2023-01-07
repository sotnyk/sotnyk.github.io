---
id: 137
title: 'И снова лямбды'
date: '2010-04-11T18:27:42+00:00'
author: serge
layout: post
guid: 'http://sotnyk.com/?p=137'
permalink: /2010/04/11/i-snova-lyambdyi/
---

Erty Hackward в своем [комментарии](http://localhost/?p=103#comment-61) на [предыдущий тест производительности лямбда-выражений](http://localhost/?p=103), заставил провести некоторые изменения в коде и перетестировать. Процитирую комментарий:  
  
—————————-  
*Спасибо за тест, позновательно.  
Но тест немного читерский в сторону не лямбда методов. Дело в том, что метод FindAll создает свой список и расширяет его по мере добавления элементов, когда остальные методы используют заранее подготовленный массив. Мне кажется, для большей объективности, следует в каждом case’е создавать res = new List(); (кроме лямбда) кстати, для подсчета скорости выполнения кода есть специальный высокоточный класс Stopwatch.*  
—————————-

Прежде всего, про Stopwatch – я знаю про точные таймеры, но, поскольку не было нужды в сверхточных замерах (точность в 2-3% считаю здесь достаточной), посчитал возможным, проводить циклические тесты, выгрузив, по возможности посторонние приложения. Также, необходимо отбрасывать крайние значения, связанными с неожиданными проявлениями активности сторонними приложениями. Но это уже не зависит от примененного таймера.

Конечно, определяя заранее размер коллекции, мы уменьшаем потери на процедуру увеличения её размера, когда буфера становится мало. Но дело в том, что при использовании лямбда-выражений мы данным процессом не управляем, а при явном определении коллекций – вполне. Так что здесь все по честному.

Но обратил внимание на свою ошибку в коде – коллекция инициализируется всегда, даже, если мы тестируем лямбды. А это уже неправильно. Поэтому исправил ошибку и чуть изменил код, как предложил Erty – теперь коллекцию конструирую не передавая ей размера. Таким образом, тестируем самый распространенный случай работы с коллекциями.

\[csharp\]  
class Program  
{  
 static void Main(string\[\] args)  
 {  
 int testNum = ParseTest(args);  
 if (testNum src = new List<int>();</int>

 Random rnd = new Random();  
 const int ElementsNumber = 10000000;

 for (int i = 0; i res;  
 string testName = “{none}”;  
 switch (testNum)  
 {  
 case 1:  
 res = src.FindAll(x =&gt; x &gt; 0);  
 testName = “Lambda”;  
 break;  
 case 2:  
 res = new List<int>();  
 for (int i = 0; i 0)  
 res.Add(src\[i\]);  
 testName = @”Cycle “”for”””;  
 break;  
 case 3:  
 res = new List</int><int>();  
 foreach (int i in src)  
 if (i &gt; 0)  
 res.Add(i);  
 testName = @”Cycle “”foreach”””;  
 break;  
 case 4:  
 res = new List</int><int>();  
 for (int i = ElementsNumber – 1, j = 0; i &gt;= 0; )  
 {  
 if (0 4)  
 return 0;  
 return res;  
 }</int>

 private static void ShowUsage()  
 {  
 Console.WriteLine(“Usage: “);  
 Console.WriteLine(“LambdaTest.exe {test number}”);  
 Console.WriteLine(” {test number} = 1: Lambda test.”);  
 Console.WriteLine(” {test number} = 2: Simple cycle test.”);  
 Console.WriteLine(” {test number} = 3: For-each cycle test.”);  
 Console.WriteLine(” {test number} = 4: Optimized cycle test.”);  
 Console.ReadLine();  
 }  
}  
\[/csharp\]

Вот средние результаты (они с другой машины, поэтому абсолютные значения отличаются, но нам важно отношение):

Lambda search ended at 234,375 msec.  
Cycle “for” search ended at 218,75 msec.  
Cycle “foreach” search ended at 234,375 msec.  
Cycle “optimized for” search ended at 203,125 msec.

Как видим, лямбда-выражения в данном случае эквивалентны циклу foreach. Так что мой предыдущий вывод остается в силе – майкрософт реализовали лямбда-выражения достаточно производительно и не стоит бояться потери производительности при их использовании.