---
title: Использовать полный синтаксис запроса Lucene
titleSuffix: Azure Cognitive Search
description: Синтаксис запроса Lucene для поиска нечетких результатов, поиска с учетом расположения, повышения терминов, поиска регулярных выражений и поиска с подстановочными знаками в службе Когнитивный поиск Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
tags: Lucene query analyzer syntax
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/05/2020
ms.openlocfilehash: df26cfc3b220f40a7e73ff1c750d2b2ae37e7625
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/14/2020
ms.locfileid: "97401463"
---
# <a name="use-the-full-lucene-search-syntax-advanced-queries-in-azure-cognitive-search"></a>Использование полного синтаксиса поиска Lucene (расширенные запросы в Azure Когнитивный поиск)

При создании запросов для Когнитивный поиск Azure вы можете заменить средство [синтаксического анализа простых запросов](query-simple-syntax.md) по умолчанию более мощным [средством синтаксического анализа запросов Lucene в Azure когнитивный Поиск](query-lucene-syntax.md) , чтобы сформулировать специализированные и расширенные определения запросов. 

Средство синтаксического анализа Lucene поддерживает сложные конструкции запросов, такие как запросы с областью видимости полей, Поиск нечетких запросов, инфиксные и подстановочный знак суффикса, поиск по сходству, повышение термина и поиск регулярных выражений. Дополнительные возможности поставляются с дополнительными требованиями к обработке, поэтому следует предусмотреть немного больше времени для выполнения. В этой статье вы можете пошагово пройти примеры, демонстрирующие операции запросов на основе полного синтаксиса.

> [!Note]
> Многие специализированные конструкции запросов, обеспечиваемые благодаря полному синтаксису запросов Lucene, не поддерживают [анализ текста](search-lucene-query-architecture.md#stage-2-lexical-analysis), что может оказаться неожиданным, если вы хотите использовать выделение корней слов или лемматизацию. Лексический анализ выполняется только для полными терминами (запрос термина или запрос фразы). Типы запросов с неполными терминами (запрос с префиксом, запрос с подстановочными знаками, запрос с регулярными выражениями, нечеткий запрос) добавляются непосредственно к дереву запроса в обход этапа анализа. Единственным преобразованием, выполняемым в условиях частичного запроса, является нижний регистр. 
>

## <a name="nyc-jobs-examples"></a>Примеры заданий Нью

В следующих примерах используется [индекс поиска заданий Нью](https://azjobsdemo.azurewebsites.net/)  , состоящий из заданий, доступных на основе набора данных, предоставленного в городе Нью- [Йорк опендата](https://nycopendata.socrata.com/). Эти данные не являются текущими или завершенными. Индекс находится в службе "песочницы", предоставляемой корпорацией Майкрософт. Это означает, что для работы с этими запросами не требуется подписка Azure или Azure Когнитивный поиск.

Для отправки HTTP-запроса по запросу GET или POST требуется соответствующее средство. Если вы не знакомы с этими инструментами, см. статью [Краткое руководство. изучение Azure Когнитивный поиск REST API](search-get-started-rest.md).

## <a name="set-up-the-request"></a>Настройка запроса

1. Заголовки запроса должны иметь следующие значения:

   | Ключ | Значение |
   |-----|-------|
   | Content-Type | `application/json`|
   | api-key  | `252044BE3886FE4A8E3BAA4F595114BB` </br> (это фактический ключ API запроса для службы поиска "песочницы", в которой размещается индекс заданий Нью). |

1. Задайте для команды значение **`GET`** .

1. Задайте для URL-адреса значение **`https://azs-playground.search.windows.net/indexes/nycjobs/docs/search=*&api-version=2020-06-30&queryType=full`**

   + Коллекция Documents в индексе содержит все содержимое, доступное для поиска. Ключ API запроса, указанный в заголовке запроса, работает только для операций чтения, предназначенных для коллекции Documents.

   + **`$count=true`** Возвращает количество документов, соответствующих условиям поиска. В пустой строке поиска счетчик будет содержать все документы в индексе (около 2558 в случае заданий Нью).

   + **`search=*`** является неопределенным запросом, эквивалентным null или пустым поиском. Это не особенно полезно, но это самый простой поиск, который можно сделать, и Показать все доступные для получения поля в индексе со всеми значениями.

   + **`queryType=full`** вызывает полный анализатор Lucene.

1. В качестве шага проверки вставьте приведенный ниже запрос в раздел GET и щелкните **Отправить**. Будут возвращены результаты в виде подробных документов JSON.

   ```http
   https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&search=*&queryType=full
   ```

### <a name="how-to-invoke-full-lucene-parsing"></a>Способы вызова полного синтаксического анализа Lucene

Добавьте **`queryType=full`** , чтобы вызвать полный синтаксис запроса, переопределив синтаксис простого запроса по умолчанию. Во всех примерах в этой статье указывается **`queryType=full`** параметр поиска, указывающий, что полный синтаксис обрабатывается средством синтаксического анализа запросов Lucene. 

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
{
    "queryType": "full"
}
```

## <a name="example-1-query-scoped-to-a-list-of-fields"></a>Пример 1. запрос с областью действия списка полей

Первый пример не связан с синтаксическим анализатором, но мы начали с него, чтобы представить первое фундаментальное понятие запроса: автономность. Этот пример ограничивает как выполнение запроса, так и ответ на несколько конкретных полей. Знать, как структурировать читаемый ответ JSON, важно, если используется инструмент Postman или обозреватель поиска. 

Этот запрос предназначен только для *business_title* в **`searchFields`** , указывая с помощью **`select`** параметра то же поле в ответе.

```http
POST https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "*",
    "searchFields": "business_title",
    "select": "business_title"
}
```

Результат запроса должен выглядеть, как на снимке экрана ниже.

  ![Публикация образца ответа с результатами](media/search-query-lucene-examples/postman-sample-results.png)

Вы могли заметить оценку поиска в ответе. Однородные баллы, равные **1** , происходят, когда нет ранга, так как поиск не является полнотекстовым поиском или не был предоставлен ни один критерий. Для пустого поиска строки возвращаются в произвольном порядке. Когда вы добавите условие поиска, вы увидите, как оценки поиска превратятся в понятные значения.

## <a name="example-2-fielded-search"></a>Пример 2. Поиск по полям

Полный синтаксис Lucene поддерживает определение области отдельных выражений поиска для определенного поля. В этом примере выполняется поиск названий предприятий с термином старший в них, но не младшим. Можно указать несколько полей с помощью и.

```http
POST /indexes/nycjobs/docs?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "business_title:(senior NOT junior) AND posting_type:external",
    "searchFields": "business_title, posting_type",
    "select": "business_title, posting_type"
}
```

Ответ для этого запроса должен выглядеть, как на следующем снимке экрана (posting_type не отображается).

  :::image type="content" source="media/search-query-lucene-examples/intrafieldfilter.png" alt-text="Пример выражения поиска ответа после примера" border="false":::

Выражение поиска может представлять собой одно слово или фразу или более сложное выражение в круглых скобках, при необходимости с логическими операторами. Вот несколько примеров.

+ `business_title:(senior NOT junior)`
+ `state:("New York" OR "New Jersey")`
+ `business_title:(senior NOT junior) AND posting_type:external`

Если требуется, чтобы обе строки вычислить как единую сущность, необходимо поместить несколько строк в кавычки, как в этом случае искать два различных расположения в `state` поле. В зависимости от средства может потребоваться экранирование ( `\` ) кавычек. 

Поле, указанное в **fieldname: сеарчекспрессион** , должно быть полем с возможностью поиска. Дополнительные сведения об использовании атрибутов индекса в определениях полей см. в статье [Создание индекса (REST API когнитивный Поиск Azure)](/rest/api/searchservice/create-index) .

> [!NOTE]
> В приведенном выше примере **`searchFields`** параметр опускается, так как в каждой части запроса указано явное имя поля. Однако можно по-прежнему использовать, **`searchFields`** Если запрос содержит несколько частей (с помощью инструкций и). Например, запрос `search=business_title:(senior NOT junior) AND external&searchFields=posting_type` будет сопоставляться `senior NOT junior` только с `business_title` полем, тогда как он будет сопоставляться с `posting_type` полем external. Имя поля, указанное в параметре `fieldName:searchExpression` , всегда имеет приоритет над **`searchFields`** , поэтому в этом примере можно опустить `business_title` **`searchFields`** .

## <a name="example-3-fuzzy-search"></a>Пример 3. Поиск нечетких соответствий

Полный синтаксис Lucene также поддерживает поиск нечетких соответствий, то есть сопоставление терминов с подобной конструкцией. Чтобы выполнить поиск нечетких соответствий, необходимо добавить символ тильды `~` в конце слова. Дополнительно можно поставить цифру от 0 до 2, указывающую расстояние редактирования. Например, `blue~` или `blue~1` вернет результаты с blue, blues и glue.

```http
POST /indexes/nycjobs/docs?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "business_title:asosiate~",
    "searchFields": "business_title",
    "select": "business_title"
}
```

Фразы не поддерживаются напрямую, но можно указать нечеткое соответствие для каждого термина многокомпонентной фразы, например `search=business_title:asosiate~ AND comm~` .  На следующем снимке экрана ответ включает совпадение со *связанным сообществом*.

  :::image type="content" source="media/search-query-lucene-examples/fuzzysearch.png" alt-text="Ответ на поиск нечетких соответствий" border="false":::

> [!Note]
> Запросы с нечетким соответствием не [анализируются](search-lucene-query-architecture.md#stage-2-lexical-analysis). Типы запросов с неполными терминами (запрос с префиксом, запрос с подстановочными знаками, запрос с регулярными выражениями, нечеткий запрос) добавляются непосредственно к дереву запроса в обход этапа анализа. Единственным преобразованием, выполняемым в условиях частичного запроса, является нижний регистр.
>

## <a name="example-4-proximity-search"></a>Пример 4. Поиск с учетом расположения

Операция поиска с учетом расположения позволяет найти слова, расположенные рядом в документе. Вставьте символ тильды "~" в конце фразы, а затем — цифру, обозначающую количество слов, определяющее границу близости. Например, если ввести "hotel airport"~5, будут найдены слова "hotel" и "airport", расположенные в пределах 5 слов друг от друга в документе.

Этот запрос выполняет поиск терминов «старший» и «аналитик», где каждый термин отделяется не более чем одним словом, а кавычки () заменяются escape-символами ( `\"` ) для сохранения фразы.

```http
POST /indexes/nycjobs/docs?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "business_title:\"senior analyst\"~1",
    "searchFields": "business_title",
    "select": "business_title"
}
```

Ответ для этого запроса должен выглядеть как на следующем снимке экрана 

  :::image type="content" source="media/search-query-lucene-examples/proximity-before.png" alt-text="Запрос с учетом расположения" border="false":::

Попробуйте еще раз, удалив расстояние ( `~0` ) между терминами «старший аналитик». Обратите внимание на то, что для этого запроса возвращены 8 документов, в отличие от 10 документов для предыдущего запроса.

```http
POST /indexes/nycjobs/docs?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "business_title:\"senior analyst\"~0",
    "searchFields": "business_title",
    "select": "business_title"
}
```

## <a name="example-5-term-boosting"></a>Пример 5. Повышение приоритета терминов

При повышении приоритета слов документы сортируются по приоритету, т. е. документы, в которых содержится условие поиска, имеют высший приоритет по отношению к документам, в которых его нет. Чтобы увеличить термин, используйте знак крышки, `^` символ с коэффициентом увеличения (число) в конце искомого термина.

Этот запрос before выполняет поиск вакансий, содержащих термин *computer analyst*. Обратите внимание на то, что вакансии, содержащие оба слова *computer* и *analyst*, не найдены, а вакансии со словом *computer* выведены в верхней части результатов.

```http
POST /indexes/nycjobs/docs?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "business_title:computer analyst",
    "searchFields": "business_title",
    "select": "business_title"
}
```

В запросе after выполните этот поиск снова, при этом повышая приоритет результатов, содержащих термин *analyst*, относительно результатов, содержащих слово *computer*, если оба слова не встречаются одновременно. Понятная для человека версия запроса — `search=business_title:computer analyst^2` . Для работоспособного запроса в Post `^2` он кодируется как `%5E2` .

```http
POST /indexes/nycjobs/docs?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "business_title:computer analyst%5e2",
    "searchFields": "business_title",
    "select": "business_title"
}
```

Результат запроса должен выглядеть, как на снимке экрана ниже.

  :::image type="content" source="media/search-query-lucene-examples/termboostingafter.png" alt-text="Повышение приоритета термина after" border="false":::

Повышение приоритета терминов отличается от профилей повышения, так как они повышают приоритет определенных полей, а не определенных терминов. В следующем примере показаны эти различия.

Рассмотрим профиль повышения, который повышает приоритет совпадений в определенном поле, таком как **genre** в примере musicstoreindex. Повышение значимости слов может использоваться для дальнейшего повышения приоритета определенных условий поиска относительно других. Например, при вводе запроса "rock^2 electronic" документы, содержащие это условие поиска в поле **genre** , становятся приоритетнее документов, содержащих это условие поиска в других полях, поддерживающих поиск, в индексе. Кроме того, документы, содержащие слово "rock", будут иметь более высокий приоритет, чем документы, содержащие слово "electronic", так как введен коэффициент повышения приоритета (2).

Чем выше коэффициент повышения приоритета при настройке коэффициента, тем приоритетнее условие поиска относительно других. Коэффициент повышения приоритета по умолчанию — 1. Несмотря на то, что коэффициент повышения приоритета должен быть положительным числом, он может быть меньше 1 (например, 0,2).

## <a name="example-6-regex"></a>Пример 6. Регулярное выражение

Операция поиска по регулярным выражениям позволяет найти совпадение в зависимости от содержимого между косыми чертами "/", как указано в документации [класса RegExp](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/util/automaton/RegExp.html).

```http
POST /indexes/nycjobs/docs?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "business_title:/(Sen|Jun)ior/",
    "searchFields": "business_title",
    "select": "business_title"
}
```

Результат запроса должен выглядеть, как на снимке экрана ниже.

  :::image type="content" source="media/search-query-lucene-examples/regex.png" alt-text="Запрос регулярных выражений" border="false":::

> [!Note]
> Запросы с регулярными выражениями не [анализируются](./search-lucene-query-architecture.md#stage-2-lexical-analysis). Единственным преобразованием, выполняемым в условиях частичного запроса, является нижний регистр.
>

## <a name="example-7-wildcard-search"></a>Пример 7. Поиск с использованием подстановочных знаков

Вы можете использовать распознаваемый синтаксис для поиска с использованием одного (?) или нескольких (\*) подстановочных знаков. Обратите внимание, что средство синтаксического анализа запросов Lucene поддерживает использование этих символов для поиска одного слова, а не фразы.

Этот запрос выполняет поиск вакансий, содержащих в начале"prog". В результате будут найдены вакансии со словами "programming" и "programmer". Нельзя использовать `*` символ или в `?` качестве первого символа поиска.

```http
POST /indexes/nycjobs/docs?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "business_title:prog*",
    "searchFields": "business_title",
    "select": "business_title"
}
```

Результат запроса должен выглядеть, как на снимке экрана ниже.

  :::image type="content" source="media/search-query-lucene-examples/wildcard.png" alt-text="Запрос с подстановочным знаком" border="false":::

> [!Note]
> Запросы с подстановочными знаками не [анализируются](./search-lucene-query-architecture.md#stage-2-lexical-analysis). Единственным преобразованием, выполняемым в условиях частичного запроса, является нижний регистр.
>

## <a name="next-steps"></a>Дальнейшие действия

Попробуйте указать запросы в коде. Следующие ссылки содержат сведения о настройке поисковых запросов с помощью пакетов SDK для Azure.

+ [Запрос индекса с помощью пакета SDK для .NET](search-get-started-dotnet.md)
+ [Запрос индекса с помощью пакета SDK для Python](search-get-started-python.md)
+ [Запрос индекса с помощью пакета SDK для JavaScript](search-get-started-javascript.md)

Дополнительные справочные материалы по синтаксису, архитектуре запросов и примеры можно найти по следующим ссылкам:

+ [Примеры синтаксиса запросов Lucene для создания расширенных запросов](search-query-lucene-examples.md)
+ [How full text search works in Azure Cognitive Search](search-lucene-query-architecture.md) (Как выполняется полнотекстовый поиск в Когнитивном поиске Azure)
+ [Простой синтаксис запросов](query-simple-syntax.md)
+ [Полный синтаксис запроса Lucene](query-lucene-syntax.md)
+ [Синтаксис фильтра](search-query-odata-filter.md)