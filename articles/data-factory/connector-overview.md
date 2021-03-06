---
title: Обзор соединителя фабрики данных Azure
description: Узнайте о поддерживаемых соединителях в фабрике данных.
services: data-factory
author: linda33wj
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/28/2020
ms.author: jingwang
ms.reviewer: craigg
ms.openlocfilehash: 9b346c24b6a363e66e56f6c16b70058df3864c88
ms.sourcegitcommit: ba7fafe5b3f84b053ecbeeddfb0d3ff07e509e40
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/12/2020
ms.locfileid: "91945840"
---
# <a name="azure-data-factory-connector-overview"></a>Обзор соединителя фабрики данных Azure

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Фабрика данных Azure поддерживает следующие хранилища данных и форматы с помощью копирования, потока данных, поиска, получения метаданных и удаления действий. Щелкните каждое хранилище данных, чтобы узнать о поддерживаемых возможностях и соответствующих конфигурациях.

## <a name="supported-data-stores"></a>Поддерживаемые хранилища данных

[!INCLUDE [Connector overview](../../includes/data-factory-v2-connector-overview.md)]

## <a name="integrate-with-more-data-stores"></a>Интеграция с дополнительными хранилищами данных

Фабрика данных Azure может достичь более широкого набора хранилищ данных по сравнению с упомянутым выше списком. Если необходимо переместить данные в хранилище данных, которое не входит в список встроенных соединителей фабрики данных Azure, или из него, ниже приведены некоторые расширяемые параметры.
- Для базы данных и хранилища данных обычно можно найти соответствующий драйвер ODBC, с помощью которого можно использовать [универсальный соединитель ODBC](connector-odbc.md).
- Для приложений SaaS:
    - Если он предоставляет интерфейсы API RESTFUL, можно использовать [универсальный соединитель RESTful](connector-rest.md).
    - Если в нем есть канал OData, можно использовать [универсальный соединитель OData](connector-odata.md).
    - Если он предоставляет API SOAP, можно использовать [универсальный соединитель HTTP](connector-http.md).
    - Если у него есть драйвер ODBC, можно использовать [универсальный соединитель ODBC](connector-odbc.md).
- Для других пользователей проверьте, можно ли загружать данные в какие-либо хранилища данных, поддерживаемые ADF, например, BLOB-объекты Azure, файлы, FTP/SFTP и т. д., а также разрешить ADF-файл. Вы можете вызывать пользовательский механизм загрузки данных с помощью [функции Azure](control-flow-azure-function-activity.md), [настраиваемого действия](transform-data-using-dotnet-custom-activity.md), [модуля](transform-data-databricks-notebook.md)данных / [HDInsight](transform-data-using-hadoop-hive.md), [веб-действия](control-flow-web-activity.md)и т. д.

## <a name="supported-file-formats"></a>Поддерживаемые типы файлов

Фабрика данных Azure поддерживает следующие форматы файлов. Дополнительные сведения о параметрах на основе форматирования см. в каждой статье.

- [Формат Avro](format-avro.md)
- [Двоичный формат](format-binary.md)
- [Формат Common Data Model](format-common-data-model.md)
- [Формат текста с разделителями](format-delimited-text.md)
- [Формат изменений](format-delta.md)
- [Формат Excel](format-excel.md)
- [Формат JSON](format-json.md)
- [Формат ORC](format-orc.md)
- [Формат Parquet](format-parquet.md)
- [Формат XML](format-xml.md)

## <a name="next-steps"></a>Дальнейшие шаги

- [Действие копирования](copy-activity-overview.md)
- [Поток данных для сопоставления](concepts-data-flow-overview.md)
- [Действие поиска](control-flow-lookup-activity.md)
- [Действие получения метаданных](control-flow-get-metadata-activity.md)
- [Удалить действие](delete-activity.md)
