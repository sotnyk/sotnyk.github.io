---
id: 1236
title: 'Случаи из практики 2. Рефакторим.'
date: '2012-09-21T18:40:40+00:00'
author: serge
layout: post
guid: 'http://sotnyk.com/?p=1236'
permalink: /2012/09/21/sluchai-iz-praktiki-2-refaktorim/
---

![](http://localhost/wp-content/uploads/2012/09/Sluchai-is-praktiki-198x300.jpg "Sluchai-is-praktiki")Небольшое лирическое отступление. Долгое время это слово больше читал, чем произносил или слышал. И поперва приспособился его произносить про себя с ударением на “о” – рефактОринг. Видимо потому, что возникли ассоциации со словом “фактория”, которое слышал в фильмах про дореволюционную Россию. Меня поправили. Вроде бы стал говорить правильно, с ударением на “а”. Хотя, иногда может проскользнуть старый вариант. Надеюсь, это не так режет слух, как мне неправильное ударение в словах “позвоним”, “созвонимся” и т.п. 😛

Итак, ближе к делу. Имеется кусок кода, который занимается разбиением работы на порции и применением к ним того или иного экшена:  
  
\[csharp\]  
public static void BatchProcessing&lt;T&gt;(  
 this IEnumerable&lt;T&gt; list,  
 int batchSize,  
 Action&lt;IEnumerable&lt;T&gt;&gt; action)  
{  
 var data = list is List&lt;T&gt; ?  
 list as List&lt;T&gt; : list.ToList();  
 long total = data.Count;  
 int processed = 0;  
 while (data.Count &gt; 0)  
 {  
 int left = data.Count;  
 batchSize = left &gt; batchSize ? batchSize : left;  
 processed += batchSize;  
 var batchList = list.Take(batchSize).ToList();  
 data.RemoveRange(0, batchSize);  
 Trace.TraceInformation("Processed {0:0.00}% ….",  
 (double)100 \* processed / total);  
 action(batchList);  
 }  
}  
\[/csharp\]

**Круг первый**

Меня данный кусок кода зацепил, прежде всего, своей неэффективностью. Обратите внимание, после обработки каждой порции, оставшиеся элементы списка сдвигаются к нулевой позиции. Учитывая, что в реальном проекте данный метод работает со списками данных длиной более миллиона элементов, обрабатывая их по 100 штук – понятно, что значительные усилия тратятся только на перемещение данных (пусть только ссылок, но на миллионы объектов). Я предполагаю, что причиной этого является любовь автора кода к LINQ, поэтому он попытался реализовать функционал, используя его оператор Take().

Остальное – мелочи. Метод ведь работает, протестирован. Но такая дикая неэффективность не давала мне покоя, и я быстренько набросал другой вариант.

Стоп! Что-то слишком просто получилось. Может чего-то забыл? Возвращаем все назад и делаем правильно. А правильно – покрыть данный метод юнит-тестами. Чтобы быть уверенным в ненарушенной логике срабатывания. Что нужно проверить? А проверить нам нужно, что метод разбивает на правильное количество порций и что каждый элемент обрабатывается один раз. Первое условие тестируем, накапливая в экшене количество вызовов, а второе – суммируя сумму элементов каждой порции. Исходя из того, что сумма обладает ассоциативностью (всем известный со школы сочетательный закон), не важно, в каком порядке считать сумму и как группировать при этом элементы. В любом случае мы должны получить одно и то же значение.

Итак, вот сами тесты:

\[csharp\]  
…  
private List&lt;int&gt; \_testSequence = null;  
…  
\[TestInitialize()\]  
public void MyTestInitialize()  
{  
 \_testSequence = (new int\[\]{1, 2, 3, 4, 5, 6, 7, 8,  
 9, 10, 11, 12, 13}).ToList();  
}  
…  
\[TestMethod()\]  
public void BatchProcessing\_TestPortionsCounting()  
{  
 int portionCounter = 0;  
 \_testSequence.ToArray().BatchProcessing(2,  
 x =&gt; portionCounter++);  
 Assert.AreEqual(7, portionCounter);

 portionCounter = 0;  
 \_testSequence.ToArray().BatchProcessing(1,  
 x =&gt; portionCounter++);  
 Assert.AreEqual(13, portionCounter);

 portionCounter = 0;  
 \_testSequence.ToArray().BatchProcessing(13,  
 x =&gt; portionCounter++);  
 Assert.AreEqual(1, portionCounter);

 portionCounter = 0;  
 \_testSequence.ToArray().BatchProcessing(100,  
 x =&gt; portionCounter++);  
 Assert.AreEqual(1, portionCounter);  
}

\[TestMethod()\]  
public void BatchProcessing\_TestEveryElementMustBePlacedOneTime()  
{  
 int expected = \_testSequence.Sum();  
 int sum = 0;

 \_testSequence.ToArray().BatchProcessing(2, x =&gt;  
 {  
 sum += x.Sum();  
 });  
 Assert.AreEqual(expected, sum, "Check 2");

 sum = 0;  
 \_testSequence.ToArray().BatchProcessing(1, x =&gt;  
 {  
 sum += x.Sum();  
 });  
 Assert.AreEqual(expected, sum, "Check 1");

 sum = 0;  
 \_testSequence.ToArray().BatchProcessing(13, x =&gt;  
 {  
 sum += x.Sum();  
 });  
 Assert.AreEqual(expected, sum, "Check 13");

 sum = 0;  
 \_testSequence.ToArray().BatchProcessing(100, x =&gt;  
 {  
 sum += x.Sum();  
 });  
 Assert.AreEqual(expected, sum, "Check 100");  
}  
\[/csharp\]

Тут куча повторов, я знаю, но об этом ниже.

Маленькое пояснение – ToArray() каждый раз я делаю для того, чтобы гарантированно вызвать операцию копирования исходной коллекции. Мало ли – может реализация её подпортит. Ведь там есть вызов метода удаления части элементов.

Запускаем. Первый тест, проверяющий правильность количества порций работы выполняется правильно. А вот тест того, что каждый элемент должен быть обработан и обработан только один раз, фейлит!

Хм, в чем же дело? А дело в банальной описке. Давайте вернемся к листингу исходной реализации. Обратите внимание на строку:

\[csharp\]  
var batchList = list.Take(batchSize).ToList();  
\[/csharp\]

Таким образом, метод каждый раз берем элементы не из переменной data, а из параметра list. Удаляются же элементы правильно, у data:

\[csharp\]  
data.RemoveRange(0, batchSize);  
\[/csharp\]

Ух-ты! Как же раньше не заметили-то? Ведь метод тысячи раз обрабатывал одну и ту же, первую, порцию данных.

Ну да ладно, заменяем list.Take… на data.Take… – юнит тесты теперь отрабатывают как надо.

**Круг второй.**

Ну, теперь можно собственно рефакторить. Вот новая реализация:

\[csharp\]  
public static void BatchProcessing&lt;T&gt;(  
 this IEnumerable&lt;T&gt; list,  
 int batchSize,  
 Action&lt;IEnumerable&lt;T&gt;&gt; action)  
{  
 var data = list is List&lt;T&gt; ?  
 list as List&lt;T&gt; : list.ToList();  
 for (int i = 0; i &lt; data.Count; i += batchSize)  
 {  
 int currentPortionSize =  
 Math.Min(batchSize, data.Count – i);  
 Trace.TraceInformation("Processed {0:0.00}% ….",  
 (double)100 \* i / data.Count);  
 action(data.GetRange(i, currentPortionSize));  
 }  
}  
\[/csharp\]

Коротко и эффективно. Без сдвигов миллионов объектов. Юнит тесты проходят, все хорошо.

Но как же все-таки не заметили эту ошибку раньше???

А дело вот в чем:

\[csharp\]  
var data = list is List&lt;T&gt; ? list as List&lt;T&gt; : list.ToList();  
\[/csharp\]

Имеющийся код передавал в метод List. Соответственно, выполнение шло по ветке, которая не копирует список, а просто приводит его к нужному типу. В этом случае, мы можем обращаться хоть к data, хоть к list – мы будем работать с одним и тем же объектом. Именно это обстоятельство и спасало.

**Круг третий.**

А, в общем, мы тут наблюдаем отход от строгой типизации, за что поплатились, получив мину замедленного действия. Она срабатывает, если передан массив (тихо, безо всякого объявления) или Read-only список (тут хотя бы сразу получим эксепшен). Эта проверка на тип в рантайме плохо пахнет. Уйдем с темной дороги и вернемся к силе статической типизации, используя перегрузку:

\[csharp\]  
public static void BatchProcessing&lt;T&gt;(  
 this IEnumerable&lt;T&gt; list,  
 int batchSize,  
 Action&lt;IEnumerable&lt;T&gt;&gt; action)  
{  
 list.ToList().BatchProcessing(batchSize, action);  
}

public static void BatchProcessing&lt;T&gt;(  
 this List&lt;T&gt; list,  
 int batchSize,  
 Action&lt;IEnumerable&lt;T&gt;&gt; action)  
{  
 for (int i = 0; i &lt; list.Count; i += batchSize)  
 {  
 int currentPortionSize = Math.Min(batchSize,  
 list.Count – i);  
 Trace.TraceInformation("Processed {0:0.00}% ….",  
 (double)100 \* i / list.Count);  
 action(list.GetRange(i, currentPortionSize));  
 }  
}  
\[/csharp\]

Вот теперь, я доволен. Как мне кажется, код стал проще и понятнее (не говоря про эффективность), исправлена “мина”. Конечно, второй метод тоже нужно “закрепить” юнит-тестами. Мой первый вариант тестов, который был приведен выше, грешит повторяющимися кодами – оставляю переписать их в качестве упражнения на рефАкторинг. В реальном проекте я их поправил. И проверки входных параметров можно бы добавить – тут, я каюсь, оставил, как было…

**Мораль…**

1\. Юнит-тесты – это хорошо. Особенно при рефакторинге, но не только.  
2\. Проверка типов в рантайме без особой нужды – плохо.  
3\. Использование по максимуму возможностей строгой типизации – хорошо.