---
title: Использование динамического SQL
description: Советы по разработке решений с использованием динамического SQL для выделенных пулов SQL в Azure синапсе Analytics.
services: synapse-analytics
author: MSTehrani
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: emtehran
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 52bc7bdc63f754d52bf4a69097c1dd309a6dc3ec
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/01/2020
ms.locfileid: "96462781"
---
# <a name="dynamic-sql-for-dedicated-sql-pools-in-azure-synapse-analytics"></a>Динамический SQL для выделенных пулов SQL в Azure синапсе Analytics

В эту статью включены советы по разработке решений с использованием динамического SQL в выделенных пулах SQL.

## <a name="dynamic-sql-example"></a>Пример динамического SQL

При разработке кода приложения для выделенных пулов SQL может потребоваться использовать динамический SQL, чтобы обеспечить гибкие, универсальные и модульные решения. В настоящее время выделенные пулы SQL не поддерживают типы данных большого двоичного объекта.

Это может ограничивать размер строк, так как к двоичным относятся типы данных nvarchar(max) и varchar(max).

Если вы использовали эти типы в коде приложения для создания больших строк, необходимо разбить код на блоки и использовать вместо этого инструкцию EXEC.

Вот простой пример:

```sql
DECLARE @sql_fragment1 VARCHAR(8000)=' SELECT name '
,       @sql_fragment2 VARCHAR(8000)=' FROM sys.system_views '
,       @sql_fragment3 VARCHAR(8000)=' WHERE name like ''%table%''';

EXEC( @sql_fragment1 + @sql_fragment2 + @sql_fragment3);
```

Если строка короткая, то можно использовать [sp_executesql](/sql/relational-databases/system-stored-procedures/sp-executesql-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) как обычно.

> [!NOTE]
> Инструкции, выполняемые как динамические SQL, будут по-прежнему подвергаться всем правилам проверки T-SQL.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные советы по разработке приведены в [обзоре разработки](sql-data-warehouse-overview-develop.md).
