---
id: 799
title: 'Простейший пример MEF'
date: '2011-07-15T16:06:53+00:00'
author: serge
layout: post
guid: 'http://sotnyk.com/?p=799'
permalink: /2011/07/15/prosteyshiy-primer-mef/
---

![](http://localhost/wp-content/uploads/2011/07/MEFLogo.png "MEF logo")До недавнего времени в том случае, если нужно было работать с кодом, неизвестным основному приложению на этапе компиляции (плагины), я по старинке использовал механизм, известный как Reflection. Несколько громоздкий, но своей цели он достигает. Но в 4-м .NET Framework появился встроенный механизм, известный как [MEF (Managed Extensions Framework)](http://mef.codeplex.com/).

Примеров по интернету довольно много, но первое, что мне бросилось в глаза, имело некоторые недостатки:

1\. Многие примеры были написаны для старой версии MEF, которая была еще отдельным проектом. С тех пор поменялись имена классов, методов. Поэтому те примеры (например, [Simple Introduction to Extensible Applications with the Managed Extensions Framework](http://blogs.msdn.com/b/brada/archive/2008/09/29/simple-introduction-to-composite-applications-with-the-managed-extensions-framework.aspx)) сейчас, в 4-й версии даже не компилируются.  
2\. Примеры часто базируются на экспорте свойств. В то же время в моей практике больше характерен экспорт методов и классов.  
  
В общем, методом научного тыка и подсказок коллеги написал простенький пример, на котором, как мне кажется, можно понять принцип работы и использовать в некоторых случаях. Ну, а по свежим следам проще рассказать – объяснять часто лучше может не тот, кто знает, а тот, кто учится :-).

Экспортировать классы будем позже, начнем с привязки к методам. Создаем консольное приложение – в простых примерах интерфейсная часть только затуманивает смысл. В его референсы добавляем System.ComponentModel.Composition.dll. Здесь находятся классы и методы для работы с MEF.

Дописываем в program.cs такие строки:

\[csharp\]  
…  
using System.ComponentModel.Composition;  
using System.ComponentModel.Composition.Hosting;  
using System.IO;  
using System.Reflection;

namespace MEFProc  
{  
 public delegate string StringTransformer(string src);

 class Program  
 {  
 \[ImportMany(“StringTransformer”)\]  
 public IEnumerable<stringtransformer> Transformers  
 { get; set; }</stringtransformer>

 public void Run()  
 {  
 AggregateCatalog catalog = new AggregateCatalog();  
 catalog.Catalogs.Add(new DirectoryCatalog(  
 Path.GetDirectoryName(  
 new Uri(Assembly.GetExecutingAssembly()  
 .CodeBase).LocalPath)));

 var container = new CompositionContainer(catalog);  
 container.SatisfyImportsOnce(this);

 foreach (var transformer in Transformers)  
 {  
 Console.WriteLine(  
 transformer(“Sample StRiNg.”));  
 }  
 Console.WriteLine(“Press any key”);  
 Console.ReadKey();  
 }

 static void Main(string\[\] args)  
 {  
 new Program().Run();  
 }  
 }  
}  
\[/csharp\]

Что мы здесь делаем:

1\. Создали экземпляр класса Program. Насколько я понял, биндинг внешних методов и свойств для статических элементов затруднен. Хотя бы потому, что методы, осуществляющие это, принимают на вход ссылку на объект.  
2\. Создали свойство Transformers, которое представляет собой коллекцию делегатов, выполняющих произвольную трансформацию строки.  
3\. Пометили данное свойство атрибутом ImportMany. Если бы мы использовали атрибут Import, как это делалось в старых примерах, то получили бы сообщение об ошибке, что тип свойства не совпадает с типом внешнего элемента, который мы пытаемся привязать к нему.  
4\. К атрибуту я добавил строку, идентифицирующую привязку. Это сделано для того, чтобы не происходило привязки только потому, что другой экспортируемый метод подходит по типу, хотя нужен для другого. В примере это конечно не обязательно, так, на всякий случай.  
5\. В методе Run мы выполняем процедуру привязки ко всем сборкам, находящимся в том же каталоге, что и наше приложение. После вызова container.SatisfyImportsOnce мы можем работать с нашим свойством Transformers. До этого момента оно имеет значение null.  
6\. Вызываем все загруженные плагины.

Запускаем приложение. Поскольку у нас еще нет плагинов, то получаем следующую картинку:

![](http://localhost/wp-content/uploads/2011/07/woPlugins.png "Without plugins")

Создаем новый проект – я назвал его StringTransformerPlugins. Сюда также подключаем System.ComponentModel.Composition.dll. В нем два класса с тремя простенькими плагинами. Плагины мы помечаем атрибутом Export с такой же строкой, как мы использовали в атрибуте ImportMany:

Plugs.cs:  
\[csharp\]  
…  
using System.ComponentModel.Composition;

namespace StringTransformerPlugins  
{  
 public class Plugs  
 {  
 \[Export(“StringTransformer”)\]  
 public string UpperCase(string src)  
 {  
 return src.ToUpperInvariant();  
 }

 \[Export(“StringTransformer”)\]  
 public string LowerCase(string src)  
 {  
 return src.ToLowerInvariant();  
 }  
 }  
}  
\[/csharp\]

AnagramPlug.cs  
\[csharp\]  
…  
using System.ComponentModel.Composition;

namespace StringTransformerPlugins  
{  
 public class AnagramPlug  
 {  
 \[Export(“StringTransformer”)\]  
 public string MakeAnagram(string src)  
 {  
 var result = new StringBuilder();  
 for (int i = src.Length – 1; i &gt;= 0; –i)  
 result.Append(src\[i\]);  
 return result.ToString();  
 }  
 }  
}  
\[/csharp\]

В свойства основного приложения, в раздел Build Events / Post build event command line добавляем макрос, копирующий сборку StringTransformerPlugins.dll к exe-файлу:

copy $(ProjectDir)..StringTransformerPlugins$(OutDir)\*.\* $(TargetDir)

Это нам необходимо, поскольку основное приложение не ссылается на эту сборку (она вообще может быть в другом солюшене – здесь мы их компилируем вместе только для удобства).

Один еще момент – чтобы плагин скопировался, выбирайте пункт Build / Rebuild solution. Поскольку, если не было изменений в основном проекте, то его сборка не выполняется и скрипт, копирующий dll не выполняется также.

Итак, делаем полную пересборку и запускаем приложение. Получаем:

![](http://localhost/wp-content/uploads/2011/07/WithPlugins.png "With plugins")

Плагины загружены и выполнены!

Исходные коды и откомпилированный проект вы можете [взять здесь](http://localhost/code/MEFSimple.rar).