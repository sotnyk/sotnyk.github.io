---
id: 1099
title: 'Тестируем Invoke и его альтернативы'
date: '2012-04-07T10:23:19+00:00'
author: serge
layout: post
guid: 'http://sotnyk.com/?p=1099'
permalink: /2012/04/07/testiruem-invoke-i-ego-alternativyi/
---

![](https://sotnyk.github.io/wp-content/uploads/2012/04/InvokeTest.jpg "Invoke Test")

[Мы](http://www.iveonik.com), в своей работе активно используем широко известный движок для полнотекстового поиска [Lucene.net](http://incubator.apache.org/lucene.net/). Библиотека достойная. Но, как обычно, не все гладко. Недавно, например, с удивлением обнаружил в исходном коде использование Invoke. Причем, не где-то при инициализации, а при каждом вызове Snowball-[стеммера](http://ru.wikipedia.org/wiki/%D0%A1%D1%82%D0%B5%D0%BC%D0%BC%D0%B8%D0%BD%D0%B3). Вот этот кусок из SnowballFilter.cs (я операторные скобки {} расставил в Java-стиле, чтобы код был компактнее):  

```csharp
stemmer.SetCurrent(token.TermText());  
try {  
 stemMethod.Invoke(stemmer, (System.Object\[]) EMPTY_ARGS);  
} catch (System.Exception e) {  
 throw new System.SystemException(e.ToString());  
}  
Token newToken = new Token(stemmer.GetCurrent(),  
 token.StartOffset(), token.EndOffset(), token.Type());  
```

Что мы здесь видим:

1\. Методом SetCurrent, устанавливается строка, от которой нам нужно получить стем.  
2\. Через reflection вызывается метод “Stem”.  
3\. Стем исходного слова возвращается методом GetCurrent.  
  
Рефлекшен здесь понадобился только потому, что у предка стеммеров не объявлен такой виртуальный метод. Но он есть у каждого наследника. Почему так сделано – вопрос к архитекторам библиотеки. Возможно, здесь возникли какие-то сложности при портировании с Java оригинальной библиотеки Lucene. В любом случае, кусок достаточно некрасивый.

Но сейчас меня заинтересовало другое – в операции, которая вызывается очень часто при индексировании (от каждого слова нам необходимо получить стем), используется вызов метода через Reflection. Что можно сделать?

На ум сразу приходят еще два варианта работы с классами, как с плагинами:

1\. Один раз получить от метода делегат и дальше использовать уже только его.  
2\. Использовать dynamic-тип, введенный в C#4. Это тоже будет Reflection, но реализация его “под капотом” отличается.

Протестируем эти способы, а заодно, сравним их скорость со скоростью прямого вызова метода.

Тестировать я буду в своем обычном стиле. Т.е., без использования профилировщиков и т.п. Вместо этого, напишем небольшое консольное приложение, которое будет многократно повторять одну и ту же операцию, которую мы и хотим вычленить из общего окружения. Во время запуска выходим из Visual Studio, поскольку даже если она просто запущена, то может подтормаживать некоторые операции (наталкивался на такое). А уже при запуске из-под неё, результаты могут очень отличаться.

Итак, прежде всего класс, который будем тестировать. Это простенькая имитация стеммера, который в нашем случае делает что-то простое со строкой:  

```csharp
public class Stemmer{  
 public string GetStem(string word) {  
 return word.ToUpperInvariant();  
 }  
}  
```

Мы будем вызывать его метод GetStem разными способами.

Трассировку времени будем выполнять таким образом:  
```csharp
static void TraceTime(string title, Action action){  
 DateTime start = DateTime.Now;  
 action();  
 DateTime finish = DateTime.Now;  
 Console.WriteLine("{0}. Processed at {1} msec", title,  
 (int)((finish-start).TotalMilliseconds));  
}  
```
Далее, в цикле 5 раз будем вызывать последовательность из разных типов вызовов. Почему 5 раз? Потому, что в многопоточном окружении Windows, любые эксперименты лучше повторять несколько раз. Кроме того, первый прогон всегда дает немного другие результаты – сказывается, что в нем происходит подгрузка и компиляция сборок, кеширование данных процессором и т.п. Поскольку сейчас мы тестируем не инициализацию, то первый прогон мы просто будем отбрасывать. Он будет просто “разогревочным”.

**Метод 1: Прямой вызов.**  
Ну, тут все просто:  
```csharp
const int loops = 1000000;  
…  
Stemmer directStemmer = new Stemmer();  
TraceTime("Direct calling", () =>  
{  
 for (int i = 0; i &lt; loops; ++i) {  
 string stem = directStemmer.GetStem("woRd");  
 };  
});  
```

**Метод 2: Используем предварительно подготовленный делегат**, честно доставая его через рефлекшен:  
```csharp
public delegate String StemDelegate(string word);  
…  
object stemmerObj = new Stemmer();  
StemDelegate stemmer = (StemDelegate)Delegate  
 .CreateDelegate(typeof(StemDelegate),  
 stemmerObj, "GetStem");  
TraceTime(“Using delegate”, () =>
{  
 for (int i = 0; i &lt; loops; ++i) {  
 string stem = stemmer("woRd");  
 };  
});  
```

**Метод 3: Постоянный Invoke** – это тот метод, который вызвал у меня некоторое недоумение. Чтобы приблизить его к оригиналу, где результат достается не через рефлекшен, я закомментировал приведение результата к типу string:  
```csharp
object reflectionStemmer = new Stemmer();  
MethodInfo mi = typeof(Stemmer).GetMethod("GetStem");  
object[] arguments = new object[]{"woRd"};  
TraceTime(“Using Invoke”, () =>  
{  
 for (int i = 0; i < loops; ++i) {  
 /*string stem = (string)*/  
 mi.Invoke(reflectionStemmer, arguments);  
 };  
});  
```

**Метод 4: Использование dynamic:**  
```csharp
dynamic dynamicStemmer = new Stemmer();  
TraceTime("Dynamic calling", () =>
{  
 for (int i = 0; i < loops; ++i) {  
 string stem = dynamicStemmer.GetStem("woRd");  
 };  
});  
```

Результаты прогонки на моем домашнем компьютере. Win7x64, AnyCPU, Release. Кстати, еще момент – недавно столкнулся с тем, что некоторые вычисления в режиме x86 выполняются ощутимо быстрее, а другие медленнее. Так что для полноты картины нужно запускать в обоих режимах (x86, x64), но в нашем случае отличия несущественные – я проверил, так что приводить результаты не буду.

```

Direct calling. Processed at 283 msec
Using delegate. Processed at 274 msec
Using Invoke. Processed at 2043 msec
Dynamic calling. Processed at 428 msec

Direct calling. Processed at 262 msec
Using delegate. Processed at 274 msec
Using Invoke. Processed at 2031 msec
Dynamic calling. Processed at 353 msec

Direct calling. Processed at 261 msec
Using delegate. Processed at 274 msec
Using Invoke. Processed at 2032 msec
Dynamic calling. Processed at 340 msec

Direct calling. Processed at 270 msec
Using delegate. Processed at 272 msec
Using Invoke. Processed at 2035 msec
Dynamic calling. Processed at 339 msec

Direct calling. Processed at 283 msec
Using delegate. Processed at 272 msec
Using Invoke. Processed at 2033 msec
Dynamic calling. Processed at 341 msec
```

Отбрасываем результаты первого прогона – как я уже говорил, он “прогревочный”. Что видим.

1\. При использовании делегата, вызов происходит примерно с такой же скоростью, что и прямой вызов. Это логично – ведь все проверки уже должны быть выполнены, поэтому операции идентичны.  
2\. Invoke очень медленный. Он на **порядок(!)** медленнее прямого вызова.  
3\. Dynamic реализован достаточно эффективно. Скорее всего, он использует кеширование информации, доставаемой через reflection. Он медленнее прямого вызова примерно на 20%, не является типобезопасным в том плане, что ошибки с приведением типов не проверятся компилятором, но, в общем, при достаточной осторожности, можно использовать.

Конечно, вклад накладных расходов на вызов метода сильно зависит от сложности обработки внутри. Но, исходя из результатов, тут у разработчиков Lucene.Net есть проблемное место. На ровном месте.

Какие рекомендации для случая, если вам необходимо работать с плагинами и вы не хотите завязываться на общий класс-предок?

1\. Используйте делегат, полученный через Delegate.CreateDelegate.  
2\. Инициализацию проводите (по возможности) при запуске программы. Это, конечно, не проверка компилятором, но в случае каких-то ошибок биндинга метода, они будут выплывать сразу, а не где-то, после какой-то последовательности действий. А с проинициализированным делегатом работа идет в обычном, строготипизированном стиле.  
3\. В крайнем случае, если не хочется морочиться при написании и вы готовы к головной боли от поддержки кода с нестрогой типизацией, используйте dynamic. Майкрософт постарался сделать его эффективным.

Полный исходный код проекта вы можете взять [по этой ссылке](https://sotnyk.github.io/code/FuncFromReflection.rar).