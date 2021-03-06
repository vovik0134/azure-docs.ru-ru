---
title: Основные сведения о наборах ресурсов
description: В этой статье объясняется, что такое наборы ресурсов и как Azure зрения их создает.
author: yaronyg
ms.author: yarong
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 10/19/2020
ms.openlocfilehash: 55efa9443fd59b66a7677c9c460e473715f201df
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/03/2020
ms.locfileid: "96553985"
---
# <a name="understanding-resource-sets"></a>Основные сведения о наборах ресурсов

Эта статья поможет вам понять, как Azure зрения использует наборы ресурсов для преобразования ресурсов данных в логические ресурсы.

## <a name="background-info"></a>Сведения о фоне

В системах обработки данных с масштабированием обычно одна таблица хранится на диске в виде нескольких файлов. Эта концепция представлена в Azure зрения с помощью наборов ресурсов. Набор ресурсов — это один объект в каталоге, представляющий большое количество ресурсов в хранилище.

Например, предположим, что в кластере Spark сохранен кадр данных в источнике ADLS 2-го поколения. Хотя в Spark таблица выглядит как единичный логический ресурс, на диске, скорее всего, тысячи Parquet файлов, каждый из которых представляет раздел общего содержимого в кадре данных. Данные и данные журнала Интернета вещей имеют одинаковую сложность. Представьте, что у вас есть датчик, который выводит файлы журналов несколько раз в секунду. Это не займет много времени, пока вы не получите сотни тысяч файлов журналов от одного датчика.

Чтобы решить проблему сопоставления большого количества ресурсов данных с одним логическим ресурсом, Azure зрения использует наборы ресурсов.

## <a name="how-azure-purview-detects-resource-sets"></a>Как Azure зрения обнаруживает наборы ресурсов

Azure зрения поддерживает обнаружение наборов ресурсов только в больших двоичных объектах Azure, ADLS 1-го поколения и ADLS 2-го поколения.

Azure зрения автоматически обнаруживает наборы ресурсов с помощью функции, называемой автоматическим обнаружением набора ресурсов. Эта функция рассматривает все данные, полученные посредством сканирования, и сравнивает их с набором определенных шаблонов.

Например, предположим, что вы просматриваете источник данных, URL-адрес которого имеет значение `https://myaccount.blob.core.windows.net/mycontainer/machinesets/23/foo.parquet` . Azure зрения просматривает сегменты пути и определяет, соответствуют ли они встроенным шаблонам. Он содержит встроенные шаблоны для идентификаторов GUID, чисел, форматов даты, кодов локализации (например, EN-US) и т. д. В этом случае шаблон номера соответствует *23*. В Azure зрения предполагается, что этот файл является частью набора ресурсов с именем `https://myaccount.blob.core.windows.net/mycontainer/machinesets/{N}/foo.parquet` .

Для URL-адреса, например `https://myaccount.blob.core.windows.net/mycontainer/weblogs/en_au/23.json` , Azure зрения соответствует шаблону локализации и шаблону числа, создавая набор ресурсов с именем `https://myaccount.blob.core.windows.net/mycontainer/weblogs/{LOC}/{N}.json` .

С помощью этой стратегии Azure зрения будет сопоставлять следующие ресурсы с одним и тем же набором ресурсов `https://myaccount.blob.core.windows.net/mycontainer/weblogs/{LOC}/{N}.json` :

- `https://myaccount.blob.core.windows.net/mycontainer/weblogs/cy_gb/1004.json`
- `https://myaccount.blob.core.windows.net/mycontainer/weblogs/cy_gb/234.json`
- `https://myaccount.blob.core.windows.net/mycontainer/weblogs/de_Ch/23434.json`

> [!Note]
> Платформа Azure Data Lake Storage 2-го поколения теперь общедоступна. Мы рекомендуем приступить к ее использованию уже сегодня. См. сведения на [странице продукта](https://azure.microsoft.com/en-us/services/storage/data-lake-storage/).

## <a name="file-types-that-azure-purview-will-not-detect-as-resource-sets"></a>Типы файлов, которые Azure зрения не будет выявлять как наборы ресурсов

Зрения намеренно не пытается классифицировать большинство типов файлов документов, таких как Word, Excel или PDF, как наборы ресурсов. Исключением являются CSV, так как это распространенный формат секционированного файла.

## <a name="how-azure-purview-scans-resource-sets"></a>Как Azure зрения сканирует наборы ресурсов

Когда Azure зрения обнаруживает ресурсы, которые он считает частью набора ресурсов, он переключается с полной проверки на образец сканирования. В примере сканирования открывается только подмножество файлов, которые посчитались в наборе ресурсов. Для каждого открываемого файла он использует схему и выполняет классификаторы. Затем Azure зрения находит самый новый ресурс из открытых ресурсов и использует схему и классификации этого ресурса в записи для всего набора ресурсов в каталоге.

## <a name="what-azure-purview-stores-about-resource-sets"></a>Что такое хранилище Azure зрения о наборах ресурсов

В дополнение к одной схеме и классификациям Azure зрения сохраняет следующие сведения о наборах ресурсов:

- Данные из последнего ресурса раздела, который он глубоко проверил.
- Статистическая информация о ресурсах секций, составляющих набор ресурсов.
- Число секций, показывающее, сколько ресурсов секций найдено.
- Число схем, показывающее, сколько уникальных схем было найдено в образце, на котором она выполняла глубокое сканирование. Это значение может быть числом от 1 до 5 или для значений больше 5, 5 +.
- Список типов разделов, если в наборе ресурсов включено более одного типа секции. Например, датчик Интернета вещей может выводить файлы XML и JSON, хотя и логически являются частью одного набора ресурсов.

## <a name="built-in-resource-set-patterns"></a>Встроенные шаблоны наборов ресурсов

Azure зрения поддерживает следующие шаблоны наборов ресурсов. Эти шаблоны могут отображаться в виде имени в каталоге или в составе имени файла.

| Имя шаблона | Отображаемое имя | Описание |
|--------------|--------------|-------------|
| Код GUID         | УСТРОЙСТВА       | Глобальный уникальный идентификатор, как определено в [RFC 4122](https://tools.ietf.org/html/rfc4122). |
| Число       | \N          | Одна или несколько цифр. |
| Форматы даты и времени | \N     | Azure зрения поддерживает различные форматы даты и времени, но все они сокращаются до серии {N} s. |
| 4ByteHex     | Hex        | Шестнадцатеричное число из четырех цифр. |
| Локализация | Loc        | Тег языка, как определено в [BCP 47](https://tools.ietf.org/html/bcp47). Azure зрения поддерживает теги, которые содержат дефис (-) или символ подчеркивания (_). Например, en_ca и en-CA. |

## <a name="issues-with-resource-sets"></a>Проблемы с наборами ресурсов

Хотя наборы ресурсов хорошо работают в большинстве случаев, вы можете столкнуться со следующими проблемами, в которых Azure зрения:

- Неправильно помечает ресурс как набор ресурсов
- Помещение ресурса в недопустимый набор ресурсов
- Неправильно помечает ресурс как не являющийся набором ресурсов

## <a name="next-steps"></a>Дальнейшие шаги

Чтобы приступить к работе с каталогом данных, см. раздел [Краткое руководство. Создание учетной записи Azure зрения](create-catalog-portal.md).
