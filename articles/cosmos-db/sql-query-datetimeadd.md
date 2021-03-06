---
title: Датетимеадд на языке запросов Azure Cosmos DB
description: Дополнительные сведения о функции SQL System Датетимеадд в Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 07/09/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: dc81849a13fdaef748c5fd631ee66ea5e3eb67c4
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2020
ms.locfileid: "93335795"
---
# <a name="datetimeadd-azure-cosmos-db"></a>Датетимеадд (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Возвращает строковое значение даты и времени, полученное при добавлении указанного числового значения (в виде целого числа со знаком) в указанную строку DateTime  
  
## <a name="syntax"></a>Синтаксис
  
```sql
DateTimeAdd (<DateTimePart> , <numeric_expr> ,<DateTime>)
```

## <a name="arguments"></a>Аргументы
  
*датетимепарт*  
   Часть даты, до которой Датетимеадд добавляет целое число. В этой таблице перечислены все допустимые аргументы Датетимепарт:

| DateTimePart | сокращения;        |
| ------------ | -------------------- |
| Year;         | "Year", "гггг", "гг" |
| Month        | "month", "mm", "m"   |
| День          | "Day", "DD", "d"     |
| Час         | "Hour", "HH"         |
| Минута       | "Minute", "MI", "n"  |
| Секунда       | "Second", "SS", "s"  |
| Миллисекунда  | "миллисекунда", "MS"  |
| Микросекунды  | "микросекунда", "MCS" |
| Потребовавшееся   | "наносекундных", "NS"   |

*numeric_expr*  
   Целое число со знаком, которое будет добавлено к Датетимепарт указанного значения DateTime

*DateTime*  
   Значение даты и времени в формате UTC 8601 строка в формате, `YYYY-MM-DDThh:mm:ss.fffffffZ` где:
  
  |Формат|Описание|
  |-|-|
  |YYYY|год из четырех цифр|
  |ММ|месяц (2 цифры) (01 = Январь и т. д.)|
  |DD|четырехзначный день месяца (от 01 до 31)|
  |T|обозначает начало элементов времени|
  |hh|час с двумя цифрами (от 00 до 23)|
  |mm|двузначное число минут (от 00 до 59)|
  |ss|двузначное число секунд (от 00 до 59)|
  |. fffffff|семь цифр в долях секунды|
  |Z|Обозначение времени в формате UTC (Универсальное координированное время)||
  
  Дополнительные сведения о формате ISO 8601 см. в разделе [ISO_8601](https://en.wikipedia.org/wiki/ISO_8601)

## <a name="return-types"></a>Типы возвращаемых данных

Возвращает строковое значение даты и времени ISO 8601 по ГРИНВИЧу в формате, `YYYY-MM-DDThh:mm:ss.fffffffZ` где:
  
  |Формат|Описание|
  |-|-|
  |YYYY|год из четырех цифр|
  |ММ|месяц (2 цифры) (01 = Январь и т. д.)|
  |DD|четырехзначный день месяца (от 01 до 31)|
  |T|обозначает начало элементов времени|
  |hh|час с двумя цифрами (от 00 до 23)|
  |mm|двузначное число минут (от 00 до 59)|
  |ss|двузначное число секунд (от 00 до 59)|
  |. fffffff|семь цифр в долях секунды|
  |Z|Обозначение времени в формате UTC (Универсальное координированное время)||

## <a name="remarks"></a>Комментарии

Датетимеадд будет возвращать `undefined` по следующим причинам:

- Указано недопустимое значение Датетимепарт
- Указанное numeric_expr не является допустимым целым числом
- Значение DateTime в аргументе или Result не является допустимым значением DateTime ISO 8601.

## <a name="examples"></a>Примеры
  
В следующем примере в дату и время добавляется 1 месяц: `2020-07-09T23:20:13.4575530Z`

```sql
SELECT DateTimeAdd("mm", 1, "2020-07-09T23:20:13.4575530Z") AS OneMonthLater
```

```json
[
    {
        "OneMonthLater": "2020-08-09T23:20:13.4575530Z"
    }
]
```  

В следующем примере вычитается 2 часа из даты и времени: `2020-07-09T23:20:13.4575530Z`

```sql
SELECT DateTimeAdd("hh", -2, "2020-07-09T23:20:13.4575530Z") AS TwoHoursEarlier
```

```json
[
    {
        "TwoHoursEarlier": "2020-07-09T21:20:13.4575530Z"
    }
]
```  

## <a name="next-steps"></a>Дальнейшие шаги

- [Функции даты и времени Azure Cosmos DB](sql-query-date-time-functions.md)
- [Системные функции Azure Cosmos DB](sql-query-system-functions.md)
- [Знакомство со службой Azure Cosmos DB. API DocumentDB](introduction.md)
