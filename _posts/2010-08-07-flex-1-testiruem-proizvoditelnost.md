---
id: 238
title: 'Flex 1: Тестируем производительность'
date: '2010-08-07T16:22:23+00:00'
author: serge
layout: post
guid: 'http://sotnyk.com/?p=238'
permalink: /2010/08/07/flex-1-testiruem-proizvoditelnost/
---

[В прошлом посте, посвященном Flex](https://sotnyk.github.io/?p=222), я описал процедуру установки и настройки свободной IDE FlexDevelop на компьютер. Сегодня затронем следующий вопрос.

До того, как что-то делать на новой платформе, интересно «пощупать» её возможности. Прежде всего, скоростные. Как человеку, привыкшему к языкам со строгой типизацией, возможность писать нетипизированный код на ActionScript 3, кажется подозрительной. Для себя, я уже давно решил, что строгая типизация – это безопасность. Как только сталкиваюсь с системами, где типы проверяются в Run-time, резко возрастает количество ошибок, отлавливающихся с большой нервотрепкой. Так что это скорее ремень безопасности, а не рабские оковы. Здесь еще хочется вспомнить знакомство с Явой в середине 90-х. На тот момент это был один из самых строгих языков из тех, что реально применялись. Я его начал изучать и написал первый пример (простенький HTTP-сервер). И к моему удивлению, после того, как были выловлены все синтаксические ошибки, он заработал! Без этапа отладки, который до того занимал не менее времени, чем собственно написание кода! Такого ранее еще не бывало, когда я пользовался менее строгими языками с возможностью (а зачастую и необходимостью) работы с указателями и явной работой с памятью. Конечно, это было не особо сложное приложение, но для моей невнимательности это было большим достижением. К счастью (для большинства задач – системное программирование это отдельная песня), автоматическая сборка мусора и отсутствие указателей есть и в ActionScript.  
  
Осталась только строгая типизация, которая явно была привнесена в язык как дополнительная возможность. Я знаю много людей, которые допускают меньше опечаток и других мелких помарок, чем я, поэтому допускаю, что кому-то вполне будет комфортно работать и без опеки компилятором. Особенно, если используется TDD-подход, который во времена моей юности не был широко применяем – в этом случае ошибки отлавливать гораздо легче. Но есть еще один аспект, который часто связывается со строгой типизацией – производительность систем в RunTime. Если на этапе компиляции программы отсутствует проверка совместимости значений, то эта проверка должна выполняться во время исполнения. Что конечно сказывается на скорости работы программы. Конечно, когда скриптовый (под этим термином я здесь буду подразумевать язык с нестрогой типизацией) язык используется в основном для запуска других приложений или вызовов библиотечных методов, встроенных в среду, то процессор большую часть времени будет проводить не в коде скриптов, и его скорость будет не очень важна. Но не так уж часто встречаются ситуации, когда бывает нужно реализовать и что-то чуть более сложное.

В общем, я надеюсь, что обосновал, почему меня скорость платформы, на которой работаю, интересует. Если вы не согласны, тогда скажу – мне просто интересно.

Исходные коды проектов и бинарные файлы можно найти здесь (<https://sotnyk.github.io/code/FlexSharpHash.7z>).  
  
В качестве тестовой задачи выберем вычисление хэш-кода для длинного текста. В качестве текста были взяты склеенные литературные произведения на английском языке, общей длиной более 8 млн. символов. Алгоритм вычисления хэш-кода взят из книги Brian Kernighan and Donald Ritchie «The C Programming Language».

Эталонная реализация на C# (я опущу незначащие секции импорта и названия класса). Консольное приложение:

\[csharp\]  
static void Main(string\[\] args)  
{  
 string text = File.ReadAllText(  
 Path.Combine(Path.GetDirectoryName(new Uri(  
 Assembly.GetEntryAssembly().CodeBase).LocalPath),  
 @”en.txt”)  
 );

 DateTime start = DateTime.Now;  
 uint hash = 0;  
 for (int i=0; i&lt;100; ++i)  
 hash = HashBKDR(text);  
 DateTime finish = DateTime.Now;

 Console.WriteLine(“Hash = ” + hash + “; Time = ” +  
 (finish – start).TotalMilliseconds + ” msec.”);  
}  
\[/csharp\]

Думаю, что здесь особых пояснений не требуется, кроме того, что сам тест выполняется 100 раз. Время исполнения тестируемого кода на исследуемом тексте составило слишком малую величину, чтобы полагаться на системное время. Выходом было либо увеличить количество итераций, либо использовать более точный таймер. Чтобы не усложнять код, пошел по первому пути. Тест исполнялся 100 раз. Вот собственно сама хэш-функция:

\[csharp\]  
private static uint HashBKDR(string str)  
{  
 //{Note: this hash function is described in “The C Programming Language”  
 // by Brian Kernighan and Donald Ritchie, Prentice Hall}  
 uint bitsInLongint = sizeof(uint) \* 8;  
 uint result = 0;  
 for (int i = 0; i   
Теперь попробуем повторить аналогичные действия на AS3. Только сделаем для интереса сразу два варианта – один с использованием строгой типизации, а второй – без.  
\[as3\]  
import flash.display.Sprite;  
import flash.events.Event;  
import flash.display.Loader;  
import flash.net.URLLoader;  
import flash.net.URLRequest;  
import flash.text.TextField;  
import flash.text.TextFieldAutoSize;

public class Main extends Sprite  
{  
 private var txt\_loader:URLLoader = new URLLoader();  
 private var source:String;  
 var my\_txt:TextField = new TextField();

 public function Main():void  
 {  
 trace(“Start text loading”);

 txt\_loader.addEventListener(Event.COMPLETE, onTxtComplete);  
 txt\_loader.load(new URLRequest(“en.txt”));  
 }

 private function onTxtComplete(e:Event = null):void  
 {  
 trace(“Text loading complete”);  
 txt\_loader.removeEventListener(Event.COMPLETE, init);  
 source = e.target.data;  
 trace(“text length = ” + source.length + ” chars.”);

 // Test typed variant  
 var start1:Date = new Date();  
 var hash:uint = HashBKDR(source);  
 var finish1:Date = new Date();  
 trace(“HashCode = ” + hash);  
 trace(“Time to calc typed = ”  
\+ (finish1.time – start1.time) + ” msec”);

 // Test non-typed variant  
 var start2:Date = new Date();  
 var hash2:uint = HashBKDR\_nontyped(source);  
 var finish2:Date = new Date();  
 trace(“HashCode = ” + hash2);  
 trace(“Time to calc non-typed = ”  
\+ (finish2.time – start2.time) + ” msec”);

 my\_txt.text = “Time to calc typed = ” +  
 (finish1.time – start1.time) + ” msec”;  
 my\_txt.appendText(“rnTime to calc non\_typed = ” +  
 (finish2.time – start2.time) + ” msec”);  
 addChild(my\_txt);

 my\_txt.width=500;  
 my\_txt.x=(stage.stageWidth-my\_txt.width)/2;  
 my\_txt.y=20;  
 my\_txt.border = true;  
 my\_txt.autoSize=TextFieldAutoSize.LEFT;  
 my\_txt.wordWrap=true;  
 }

 private static function HashBKDR(str:String):uint  
 {  
 var bitsInLongint:uint = 4/\*sizeof(uint)\*/ \* 8;  
 var result:uint = 0;  
 var len:int = str.length;  
 for (var i:int = 0; i   
Теперь собственно тестирование. Время работы приложений я усреднил после 5 запусков, а C# еще и поделил на 100 (количество выполняемых циклов). Запуск выполнялся при закрытых средах приложений, с настройками компиляции «Release». Запуск выполнялся на одном и том же ноутбуке, процессор AMD Turion64x2.

`AS3 typed     – 2221 msec.<br></br>AS3 non_typed – 5067 msec.<br></br>C#            – 28 msec.<br></br>`

**Выводы.**

Я в лёгком шоке. С одной стороны, разница в два порядка очень велика. Настолько велика, что не хочется делать какие-то численные методы на AS3. Или же попиксельно обрабатывать картинки. В то же время я видел огромное количество довольно «живых» проектов и игр на флеше/флексе. Видимо, такая ситуация сложилась потому, что там как раз для большинства функций используются вызовы библиотечных функций, которые либо написаны на native-коде, либо, вообще, нагружают процессор видеокарты.

В любом случае, если я что-то неправильно реализовал на AS3, то жду замечаний, после которых вполне можно произвести новые замеры.

**P.S.**: Столкнулся с тем, что откомпилированный swf-файл при запуске из любого другого места, кроме того, куда он был помещен, вызывает ошибку

SecurityError: Error #2148: SWF-файл file:///D|/Temp/FlexSharpHash/HashTest/bin/HashTest.swf не может осуществить доступ к локальному ресурсу file:///D|/Temp/FlexSharpHash/HashTest/bin/en.txt. Доступ к локальному ресурсу могут осуществлять только SWF-файлы из local-with-filesystem и доверенные локальные SWF-файлы.  
 at flash.net::URLStream/load()  
 at flash.net::URLLoader/load()  
 at Main()

Пока еще не разобрался, как сделать локальный файл доверенным, поэтому рекомендую его у себя перекомпилировать, либо довериться моим результатам.