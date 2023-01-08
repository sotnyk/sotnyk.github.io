---
id: 1203
title: 'Autofac RegisterType vs. Register'
date: '2012-07-08T22:30:03+00:00'
author: serge
layout: post
guid: 'http://sotnyk.com/?p=1203'
permalink: /2012/07/08/autofac-registertype-vs-register/
---

![](https://sotnyk.github.io/wp-content/uploads/2012/07/Autofac.png "Autofac")

Autofac (как, наверное, и многие другие IoC-контейнеры) – штука хорошая и удобная. С их помощью, снижается связанность компонентов системы. Она (система) набирается из существующих компонентов как из кубиков детского конструктора. Но, как обычно, любую штуку нужно уметь “готовить”. В процессе рефакторинга кода реального проекта, уже несколько раз натыкался на ситуацию, когда за счет того, что IoC контейнер активно использует рефлексию, можно получить код, который прекрасно компилируется, но не работает (эксепшены возникают при сборке “кубиков”, а иногда и позже). Приведу пример того, о чем я говорю и предложение по решению.

Итак, создадим небольшой демо-пример из 2-х интерфейсов и 3-х классов.  
  
IParameters – интерфейс, возвращающий информацию о том, с каким каталогом мы будем работать:

```csharp
public interface IParameters  
{  
 string GetPath();  
}  
```

У нас есть 2 реализации. Первая возвращает путь, откуда запускается приложение:

```csharp
public class ParametersLocal : IParameters  
{  
 public string GetPath()  
 {  
 string exeName = new Uri(Assembly  
 .GetExecutingAssembly().Location).LocalPath;  
 return Path.GetDirectoryName(exeName);  
 }  
}  
```

Вторая позволяет задать любой путь в конструкторе.  

```csharp
public class ParametersCustom : IParameters  
{  
 private string _customPath;

 public ParametersCustom(string customPath)  
 {  
  _customPath = customPath;  
 }

 public string GetPath()  
 {  
 return _customPath;  
 }  
}  
```

Данные параметры нужны для некоторого обработчика. У нас он будет просто выводить список файлов в заданном параметре. Чтобы было интереснее, у реализации будет 2 конструктора. Один будет принимать IParameter, а другой – string. Тут есть некоторая некрасивость, связанная с тем, что мы в конструкторе, принимающем строку, напрямую работаем с одной из реализаций. Но так просто проще показать проблему – в реальном приложении все правильно, но намного сложнее…  

```csharp
public interface IProcessor  
{  
 IList<string> ShowContents();  
}

public class Processor : IProcessor  
{  
 IParameters _parameters;  
 public Processor(IParameters parameters)  
 {  
  _parameters = parameters;  
 }

 public Processor(string path)  
 {  
  _parameters = new ParametersCustom(path);  
 }

 public IList<string> ShowContents()  
 {  
  return Directory.GetFiles(_parameters.GetPath());  
 }  
}  
```

А теперь программа – это консольное приложение, имеющее метод Register, в котором собирается нужная конфигурация. Далее мы вызываем зарегистрированную реализацию IProcessor, показывая содержимое заданного в конфигурации каталога:

```csharp
class Program  
{  
 static IContainer _container;

 static void Main(string[] args)  
 {  
 Register();

 IProcessor processor = _container  
 .Resolve<IProcessor>();

 Console.WriteLine("Content of path:");  
 processor.ShowContents().ToList().ForEach(  
  line => Console.WriteLine(line)  
 );  
 Console.ReadLine();  
 }

 private static void Register()  
 {  
 ContainerBuilder builder = new ContainerBuilder();  
 builder.RegisterType<ParametersLocal>()  
 .As<IParameters>().SingleInstance();

 builder.RegisterType<Processor>().As<IProcessor>()  
 .WithParameter(new TypedParameter(typeof(string),  
 "C:\\"))  
 .UsingConstructor(typeof(string));  
 _container = builder.Build();  
 }  
}  
```

Все работает, показывается содержимое корневой папки диска C:. Можем поменять регистрацию Processor на такую:

```csharp
 builder.RegisterType<Processor>().As<IProcessor>();
```

Покажется содержимое папки, откуда запущено приложение. Теперь можем получить содержимое диска C:, если поменяем строку, регистрирующую IParameters на следующую:

```csharp
 builder.RegisterType<ParametersCustom>().As<IParameters>()  
 .WithParameter(new TypedParameter(typeof(string),  
 "C:\\")).SingleInstance();  
```

Теперь собственно проблема. Во всех перечисленных выше примерах, почти любые анализаторы кода будут нам говорить, что оба варианта конструкторов не используются в коде. И мы можем, с чистой совестью, удалить их, а проект успешно откомпилируется. И только во время исполнения мы получим сообщение об ошибке – Autofac не сможет найти необходимый конструктор. В реальном проекте ситуация может быть еще сложнее – IoC контейнеры используют достаточно сложные методы поиска необходимого конструктора, поэтому они могут таки найти какой-то конструктор (если мы явно не указали сигнатуру), для которого зарегистрированы необходимые параметры. Тогда мы будем иметь конфигурацию, которая не была предусмотрена автором кода. Она может выглядеть работоспособной, но выдавать непонятные ошибки (тоже случай из практики).

Предложение по решению проблемы состоит в том, чтобы активнее использовать явное создание класса через использование расширения Register, параметром которого является лямбда-выражение, создающее необходимый компонент. Для конструктора, принимающего строку, оно будет выглядеть так:

```csharp
 builder.Register(c => new Processor("C:\\"))  
 .As<IProcessor>();  
```

Для конструктора, принимающего объект с параметрами, их необходимо “отресолвить”:

```csharp
 builder.Register(c =>  
 new Processor(_container.Resolve<IParameters>()))  
 .As<IProcessor>();  
```

В обоих случаях, компилятор не даст откомпилировать проект, в котором удалены используемые конструкторы – ведь теперь мы их используем явно. Также, утилиты, анализирующие код, будут теперь работать правильно. И еще один плюс, который больше виден при сопровождении кода, а не при написании – работают функции GUI “Go to definition” и “Find all references”. Зачастую, это сильно экономит время.

Помимо конструкторов, похожая ситуация складывается при инициализации свойств. Как неявной, так и с использованием .WithProperty(…). Тут тоже можно воспользоваться инициализационным выражением и получить все преимущества статической типизации компилируемого языка.

Понимаю, что автоматизация через Reflection придумана не просто так, поэтому интересно было бы получить примеры, когда она дает больше преимуществ, чем неудобств – отписывайтесь в комментариях, обсудим…