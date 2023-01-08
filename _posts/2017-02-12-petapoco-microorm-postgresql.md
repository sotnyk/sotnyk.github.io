---
id: 1659
title: 'PetaPoco microORM & PostgreSQL'
date: '2017-02-12T11:56:14+00:00'
author: serge
layout: post
guid: 'http://sotnyk.ml/?p=1659'
permalink: /2017/02/12/petapoco-microorm-postgresql/
---

[![PetaPocoLogo2_256.png](https://sotnyk.github.io/wp-content/uploads/2017/02/PetaPocoLogo2_256.png)](https://sotnyk.github.io/wp-content/uploads/2017/02/PetaPocoLogo2_256.png)

Возникла по работе необходимость использовать в проекте PostgreSQL. Решил попробовать microORM. Остановил свой выбор на PetaPoco. Еще интересен был Dapper, но немного не понравилось то, что разработчики там считают нормальным активную работу с dynamic-типом. Я же стараюсь без особой необходимости не выходить за рамки статической типизации, когда компилятор оберегает меня от банальных ошибок, которые в случае динамической типизации будут выявляться только в runtime или же нужно покрывать разные кейсы юнит-тестами. В общем, это мой выбор – вы можете писать так, как вам лучше. Может и я в следующий раз попробую Dapper.

Итак, PetaPoco + PostgreSQL. Выяснилась следующая проблема. В этой связке не все типы, поддерживаемые движком БД, маппятся на C#. Вот пример таблицы с двумя проблемными типами, которые мне понадобились:

```sql
CREATE TYPE "EventTypes"
 as ENUM ('PatientActivated', 'PatientDeactivated');

CREATE TABLE "JsonEnumMap" (  
 "Id" SERIAL PRIMARY KEY NOT NULL,  
 "Jsonb" JSONB NOT NULL,  
 "Event" "EventTypes" NOT NULL  
);  
```

Модель для работы с данными в C#:

```csharp
public enum EventTypes  
{  
 PatientActivated,  
 PatientDeactivated,  
};

public class JsonEnumMap  
{  
 public long Id { get; set; }  
 public string Jsonb { get; set; }  
 public EventTypes Event { get; set; }  
}

```

Проблема первая – Jsonb. Если просто попробовать что-то записать в базу, получим такой exception: column “Jsonb” is of type jsonb but expression is of type text. Около полугода назад по [пожеланиям пользователей](https://github.com/pleb/PetaPocoBug1/issues/1), разработчики PetaPoco [добавили](https://github.com/CollaboratingPlatypus/PetaPoco/commit/dee123251f8225cdd13ea2fe7f1e2659721b3785) возможность работы с такими типами через навешивание на модель атрибута Column следующего вида:

```csharp
 [Column(InsertTemplate = "CAST({0}{1} AS json)",  
 UpdateTemplate = "{0} = CAST({1}{2} AS json)")]  
 public string Jsonb { get; set; }  
```

Это решение работает, но оно мне не понравилось. Модель получается не [POCO](https://en.wikipedia.org/wiki/Plain_Old_CLR_Object). Если для вас это не важно, то можете остановиться на таком решении. Но к счастью, возможность внедрения шаблонов при вставке и обновлении объектов, есть и у маппера. И он дает возможность оставить чистую POCO-модель в проекте, используемом разными проектами.

С Enum проблема похожая, только чуть сложнее. Одного только преобразования средствами SQL тут недостаточно. Enum передается как integral value. Конечно, можно написать функцию, которая будет преобразовывать целое число в ENUM в базе, но тогда мы должны будем вручную отслеживать соответствие идентификаторов в SQL ENUM и C# enum. Чтобы это происходило автоматически, в маппер также были добавлены методы, которые позволяют передавать строку, которая уже с помощью шаблонов InsertTemplate/UpdateTemplate преобразовывается в значение ENUM. Чтение же модели не требует преобразований на стороне сервера.

Весь пример можно найти здесь: <https://github.com/sotnyk/PetaPocoMapping>. Он сделан максимально коротким, можете использовать маппер из примера (конструируется в методе ConstructEnumJsonMapper()) в своих проектах, где есть ENUM и jsonb поля. Просто зарегистрируйте его для своих моделей перед первым использованием. Все enum-поля автоматически транслируются в строку и к ним добавляются шаблоны преобразования, а то, что строковое поле должно на сервере быть преобразовано в тип jsonb, определяется по тому, что поле модели – строка и её идентификатор оканчивается на jsonb. Если захотите ввести другие признаки – это сделать легко.

Также в проекте использован класс EnumMapper из <http://stevedunns.blogspot.com/2011/08/fast-way-of-converting-c-enums-to.html>. Теоретически, он должен более производительно делать преобразования enum &lt;=&gt; string. Но я это пока не проверял.