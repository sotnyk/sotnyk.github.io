---
id: 103
title: 'Тест производительности лямбда-выражений в C#'
date: '2010-03-10T14:10:28+00:00'
author: serge
layout: post
guid: 'http://sotnyk.com/?p=103'
permalink: /2010/03/10/test-lyambda-vyirazheniy-v-c/
---

И снова тестирую производительность. Как известно, 3-я версия C# внесла такой элемент функционального программирования, как лямбда выражения. Выглядит это очень привлекательно – вместо нескольких строк кода одна. Такая компактность (за счет скрытия несущественных деталей) приводит к увеличению удобочитаемости. Но у меня в таких случаях всегда возникает вопрос – за счет чего это достигается? Не проигрываем ли мы, например, в производительности?  
  
Для проверки производительности, было написано небольшое консольное приложение, которое запускает однотипную операцию (выбор из целочисленного массива положительных элементов). Тип запускаемого теста выбирается параметром командной строки. Дело в том, что здесь выделяется довольно много памяти и начинает вмешиваться сборщик мусора – результаты зависят от того, каким по счету запускается тест.

В принципе, еще для полноты можно было бы добавить цикл, который бы проходил по массиву с помощью указателей (используя конструкцию fixed) – когда-то таким образом получалось достичь максимальную производительность. Но думаю, что потеря читабельности в данном случае совершенно не окупает небольшое увеличение производительности.

Собственно исследуемые циклы лежат внутри case-веток оператора switch.

\[csharp\]  
class Program  
{  
 static void Main(string\[\] args)  
 {  
 int testNum = ParceTest(args);  
 if (testNum &lt;= 0)  
 {  
 ShowUsage();  
 return;  
 }

 List&lt;int&gt; src = new List&lt;int&gt;();

 Random rnd = new Random();  
 const int ElementsNumber = 10000000;

 for (int i = 0; i &lt; ElementsNumber; ++i)  
 {  
 src.Add(rnd.Next(int.MinValue, int.MaxValue));  
 }

 DateTime start = DateTime.Now;  
 List&lt;int&gt; res = new List&lt;int&gt;(ElementsNumber);  
 string testName = “{none}”;  
 switch (testNum)  
 {  
 case 1:  
 res = src.FindAll(x =&gt; x &gt; 0);  
 testName = “Lambda”;  
 break;  
 case 2:  
 for (int i = 0; i &lt; ElementsNumber; ++i)  
 if (src\[i\] &gt; 0)  
 res.Add(src\[i\]);  
 testName = @”Cycle “”for”””;  
 break;  
 case 3:  
 foreach (int i in src)  
 if (i &gt; 0)  
 res.Add(i);  
 testName = @”Cycle “”foreach”””;  
 break;  
 case 4:  
 for (int i = ElementsNumber – 1, j = 0; i &gt;= 0; )  
 {  
 if (0 &lt; (j = src\[i–\]))  
 res.Add(j);  
 }  
 testName = @”Cycle “”optimized for”””;  
 break;  
 }  
 DateTime finish = DateTime.Now;

 Console.WriteLine(“{0} search ended at {1} msec.”, testName, (finish-start).TotalMilliseconds);  
 Console.ReadLine();  
 }

 private static int ParceTest(string\[\] args)  
 {  
 if (args.Length != 1)  
 return 0;  
 int res;  
 if (!int.TryParse(args\[0\], out res))  
 return 0;  
 if (res &lt; 1 || res &gt; 4)  
 return 0;  
 return res;  
 }

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

Компилируем в релиз-конфигурации, выходим из студии, чтобы не вмешивалась в тесты и запускаем их. Вот полученные усредненные результаты для пяти прогонов каждого теста и отброса двух крайних значений (результаты в миллисекундах):

```

Lambda         183,2563333
For            125,314
Foreach        140,9366667
For-optimized  116,2
```

Ну что сказать? Думаю, что не так уж и плохо для Лямбд. Ведь использовался простой тип (int), с ним производилась простейшая операция (сравнение). На любом более сложном примере отставание лямбд в производительности будет существенно меньше.

В общем, я бы одобрил использование таких выражений в большинстве случаев.