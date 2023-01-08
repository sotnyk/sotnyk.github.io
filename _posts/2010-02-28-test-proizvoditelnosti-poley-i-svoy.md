---
id: 86
title: 'Тест производительности полей и свойств'
date: '2010-02-28T22:39:32+00:00'
author: serge
layout: post
guid: 'http://sotnyk.com/?p=86'
permalink: /2010/02/28/test-proizvoditelnosti-poley-i-svoy/
---

Начав тестировать производительность, сложно остановиться. [Потестировав WCF (кликните здесь, чтобы посмотреть статью)](https://sotnyk.github.io/?p=82), захотелось посмотреть, как влияет на производительность инкапсуляция полей в свойствах. Имеются в виду простые свойства, без особых наворотов. Они нужны для того, чтобы был возможен биндинг WPF. Интуитивно есть ощущение, что здесь должно быть некоторое падение производительности.  
  
Сказано – сделано. Создадим простой класс:

```csharp
class ClassWithPropertyAndField  
{  
 public int IntField = 0;

 public int IntProperty {get; set;}  
}  
```

А теперь попытаемся поработать с ним так, чтобы вычленить собственно операцию доступа. Это должно быть что-то очень простое. Например, суммирование.

Для этого используем вот такой код:  

```csharp
class Program
{
delegate int TestSumProc(int repeats);

static void Main(string[] args)
{
const int repeats = 10000000;
for (int i = 0; i < 5; ++i) { TestSum(SumInField, repeats, "SumInField"); TestSum(SumInProperty, repeats, "SumInProperty"); TestSum(SumByPointer, repeats, "SumByPointer"); } Console.WriteLine("Press to exit.”);
Console.ReadLine();
}

static void TestSum(TestSumProc proc, int repeats, string title)
{
DateTime start = DateTime.Now;
int res = proc(repeats);
Console.WriteLine("Test {0} for {1} repeats. Total processing time: {2} msec. Result={3}",
title, repeats, (DateTime.Now – start).TotalMilliseconds, res);
}

static int SumInField(int repeats)
{
ClassWithPropertyAndField instance = new ClassWithPropertyAndField();
for (int i = repeats; i > 0; –-i)
instance.IntField += 2;
return instance.IntField;
}

static int SumInProperty(int repeats)
{
ClassWithPropertyAndField instance = new ClassWithPropertyAndField();
for (int i = repeats; i > 0; –-i)
instance.IntProperty += 2;
return instance.IntProperty;
}

unsafe static int SumByPointer(int repeats)
{
ClassWithPropertyAndField instance = new ClassWithPropertyAndField();
fixed (int* _field = &instance.IntField)
{
while (-–repeats >= 0)
(*_field) += 2;
}
return instance.IntField;
}
}
```

Как мы видим, здесь используется 3 типа доступа к целочисленным полям – прямое, через свойство и через указатель. Запуск каждого теста производится по нескольку раз (в коде прописано 5), чтобы отбросить прогоны, результаты на которых резко отличаются от среднего и исключить последствия “холодного” старта.

Нажимаем в Visual Studio кнопку F5 и получаем следующий результат (приведен результат только одной типичной серии):

- Test SumInField for 10000000 repeats. Total processing time: 26,3655 msec. Result=20000000
- Test SumInProperty for 10000000 repeats. Total processing time: 117,18 msec. Result=20000000
- Test SumByPointer for 10000000 repeats. Total processing time: 45,8955 msec. Result=20000000

\* Примечание: Результат выводится для того, чтобы компилятор гарантированно не вздумал удалить неиспользуемый код.

В общем-то ожидаемо победило поле (более, чем в 4 раза). Немного непонятно, почему отстал “указательный” способ.

И только для очистки совести выходим из-под Visual Studio и еще разок запускаем тест напрямую. Вот результаты:

- Test SumInField for 10000000 repeats. Total processing time: 29,295 msec. Result=20000000
- Test SumInProperty for 10000000 repeats. Total processing time: 32,2245 msec. Result=20000000
- Test SumByPointer for 10000000 repeats. Total processing time: 28,3185 msec. Result=20000000

С ума сойти!!! Это что-же такое? Простой выход из студии и все с ног на голову! Или, как говорили жители одной планеты в дословном переводе – “масаракш”!

**Выводы**  
Компилятор и виртуальная машина CLR довольно неплохо работают с простыми свойствами. Особого ускорения переход на поля не дает. Примерно то же можно сказать и про указатели. По крайней мере для такого применения. В общем, используйте везде свойства – это кошерно.  
Когда проверяете скорость – выходите из Visual Studio. Иначе можете сделать выводы, далекие от правильных.