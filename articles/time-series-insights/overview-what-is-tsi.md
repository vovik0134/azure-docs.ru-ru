---
title: Общие сведения. Что собой представляет служба "Аналитика временных рядов Azure" 2-го поколения? Аналитика временных рядов Azure 2-го поколения | Документация Майкрософт
description: Сведения об основных изменениях, улучшениях и функциях в службе "Аналитика временных рядов Azure" 2-го поколения.
ms.service: time-series-insights
services: time-series-insights
author: shreyasharmamsft
ms.author: shresha
manager: dpalled
ms.workload: big-data
ms.topic: overview
ms.date: 10/02/2020
ms.custom: seodec18
ms.openlocfilehash: 8fdbb42d06f26849d14e093c340c6d2457ee6ec9
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/21/2020
ms.locfileid: "95020101"
---
# <a name="what-is-azure-time-series-insights-gen2"></a>Что собой представляет служба "Аналитика временных рядов Azure" 2-го поколения

"Аналитика временных рядов Azure" 2-го поколения — это открытая и масштабируемая комплексная служба аналитики Интернета вещей, которая предоставляет лучшие в своем классе пользовательские интерфейсы и API для интеграции мощных функциональных возможностей в существующие рабочие процессы и приложения.

Эту службу можно использовать для сбора, обработки, хранения, визуализации данных и запросов по ним в масштабе Интернета вещей. Это данные сильно контекстуализированы и оптимизированы для временных рядов.

Служба "Аналитика временных рядов Azure" 2-го поколения предназначена для изучения данных и оперативного анализа в произвольной форме, позволяя выявлять скрытые тенденции, обнаруживать аномалии и проводить анализ первопричин. Это открытое и гибкое предложение, которое соответствует требованиям промышленных развертываний Интернета вещей.

## <a name="video"></a>Видеоролик

Узнайте больше о службе "Аналитика временных рядов Azure" 2-го поколения.

> [!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Using-Azure-Time-Series-Insights-to-create-an-Industrial-IoT-analytics-platform/player]

## <a name="definition-of-iot-data"></a>Определение данных Интернета вещей

Промышленные данные Интернета вещей в организациях, интенсивно использующих ресурсы, часто структурно несогласованны из-за особенностей различных устройств и датчиков в промышленных средах. Для данных из этих потоков характерны значительные пробелы, а иногда — поврежденные сообщения и ложные показания. Данные Интернета вещей часто имеют смысл в контексте дополнительных входных данных, полученных из основных или сторонних источников, таких как CRM или ERP, которые добавляют контекст для комплексных рабочих процессов. Входные данные из сторонних источников данных, такие как данные о погоде, могут дополнять потоки данных телеметрии в рамках заданной среды.

Все это подразумевает, что только часть данных используется для эксплуатации и деловой деятельности, а для анализа требуется контекстуализация. Промышленные данные часто накапливаются на протяжении более длинных временных интервалов для углубленного анализа, что позволяет понять и учесть тенденции. Превращение собранных данных Интернета вещей в практические аналитические сведения требует следующего:

* Обработка данных для очистки, фильтрации, интерполирования, преобразования и подготовки данных к анализу.
* Структура для перехода по данным и их распознавания, то есть для нормализации и контекстуализации данных.
* Экономное хранилище для длительного или неограниченного хранения большого объема обработанных (или производных) и необработанных данных.

Такие данные обеспечивают согласованные, комплексные, актуальные и правильные сведения для бизнес-анализа и отчетности.

На следующем рисунке показан типичный поток данных Интернета вещей.

[![Поток данных Интернета вещей](media/v2-update-overview/overview-one.png)](media/v2-update-overview/overview-one.png#lightbox)

## <a name="azure-time-series-insights-gen2-for-industrial-iot"></a>Служба "Аналитика временных рядов Azure" 2-го поколения для промышленного Интернета вещей

Среда Интернета вещей весьма разнообразна ввиду клиентов, охватывающих разные отраслевые сегменты, включая производство, автомобильную промышленность, энергетику, коммунальные службы, интеллектуальные здания и консалтинг. На этом обширном отраслевом рынке Интернета вещей облачные решения для комплексного анализа больших объемов данных Интернета вещей все еще продолжают развиваться.

Служба "Аналитика временных рядов Azure" 2-го поколения удовлетворяет эту потребность на рынке, предоставляя полноценное комплексное решение для анализа данных Интернета вещей с широкими возможностями семантического моделирования для контекстуализации данных временных рядов и предоставления аналитических сведений о ресурсах. Кроме того, это решение предлагает лучший в своем классе пользовательский интерфейс для распознавания, выявления тенденций, обнаружения аномалий и операционной аналитики.

Благодаря мощной платформе операционной аналитики и возможностям интерактивного анализа данных Аналитика временных рядов Azure 2-го поколения позволяет получать больше возможностей от данных, собранных из ресурсов Интернета вещей. Предложение 2-го поколения поддерживают следующие возможности.

* Многоуровневая система хранения данных с поддержкой "горячей" и "холодной" аналитики позволяет клиентам переносить данные между "горячим" и "холодным" хранилищами для выполнения интерактивной аналитики на основе "горячих" данных и операционной аналитики на основе исторических данных, накопленных за десятилетия.

  * Высокоинтерактивное решение для "горячей" аналитики выполняет много частых запросов к данным в течение короткого временного интервала.
  * Масштабируемое, оптимизированное для производительности и экономное озеро данных временных рядов на основе службы хранилища Azure позволяет клиентам выявлять тенденции в данных временных рядов за несколько лет за считанные секунды.

* Поддержка семантической модели для описания предметной области и метаданных, связанных с производными и необработанными сигналами из ресурсов и устройств.

* Гибкая платформа аналитики для хранения исторических данных временных рядов в учетной записи хранения Azure, принадлежащей клиенту, что позволяет клиентам распоряжаться своими данными Интернета вещей. Данные хранятся в формате Apache Parquet с открытым кодом, который обеспечивает подключение и взаимодействие в различных сценариях обработки данных, включая прогнозную аналитику, машинное обучение и другие специализированные вычисления, выполняемые с помощью привычных технологий, включая Spark и Databricks.

* Богатые возможности аналитики с помощью интерфейсов API расширенных запросов и пользовательским интерфейсом, объединяющим аналитические сведения о ресурсах и мощную специализированную аналитику с поддержкой интерполяции, скалярных и агрегатных функций, переменных категорий, точечных диаграмм и сигналов временного сдвига во временных рядах для углубленного анализа.

* Платформа корпоративного уровня для удовлетворения потребностей в масштабировании, производительности, безопасности и надежности наших корпоративных клиентов, использующих Интернет вещей.

* Поддержка расширяемости и интеграции для комплексного анализа. Служба "Аналитика временных рядов Azure" 2-го поколения предоставляет расширяемую платформу аналитики для различных сценариев обработки данных. Соединитель Power BI позволяет клиентам перенести в Power BI запросы, которые традиционно выполняются в службе "Аналитика временных рядов Azure" 2-го поколения, чтобы получить комплексное представление бизнес-аналитики и аналитики временных рядов на одной панели.

Детализированный поток данных показан на схеме ниже.

  [![Ключевые возможности](media/v2-update-overview/overview-two.png)](media/v2-update-overview/overview-two.png#lightbox)

Служба "Аналитика временных рядов Azure" 2-го поколения предоставляет масштабируемую модель с оплатой по мере использования для обработки, хранения (данных и метаданных) и запроса данных, позволяя клиентам адаптировать их использование в соответствии с потребностями компании.

После добавления этих ключевых возможностей промышленного Интернета вещей в службе "Аналитика временных рядов" 2-го поколения также предоставляются следующие преимущества.  

| Функция | Преимущество |
| ---| ---|
| Многоуровневое хранение для масштабируемых данных временных рядов Интернета вещей | С помощью общего конвейера обработки данных можно принимать данные как в "горячее", так и в "холодное" хранилище. Используйте "горячее" хранилище для выполнения интерактивных запросов, а "холодное" хранилище — для хранения больших объемов данных. Чтобы узнать больше о том, как использовать преимущества высокопроизводительных запросов на основе ресурсов, ознакомьтесь с [запросами](./concepts-query-overview.md). |
| Модель временных рядов для контекстуализации необработанных данных телеметрии и получения аналитики на основе ресурсов | Модель временных рядов можно использовать для создания экземпляров, иерархий, типов и переменных для данных временных рядов. Чтобы узнать больше о модели временных рядов, ознакомьтесь с разделом [Модель временных рядов](./concepts-model-overview.md).  |
| Удобная и непрерывная интеграция с другими решениями для работы с данными | Данные в холодном хранилище Аналитики временных рядов Azure 2-го поколения [хранятся](./concepts-storage.md) в файлах в формате Apache Parquet с открытым кодом. Это обеспечивает интеграцию данных с другими решениями для обработки данных (от основных или сторонних производителей) для реализации сценариев, которые включают в себя бизнес-аналитику, расширенное машинное обучение и прогнозную аналитику. |
| Изучение данных почти в реальном времени | Пользовательский интерфейс [обозревателя службы "Аналитика временных рядов Azure" 2-го поколения](./concepts-ux-panels.md) предоставляет визуализацию всех потоков данных, проходящих через конвейер приема данных. После подключении источника событий можно просматривать, изучать и запрашивать данные события. Таким образом, вы можете проверить, отправляет ли устройство данные должным образом. Вы также можете отслеживать работоспособность, производительность и общую эффективность ресурса Интернета вещей. |
| Расширяемость и интеграция | Интеграция с соединителем Power BI выполняется непосредственно в обозревателе временных рядов с помощью параметра **Экспорт**, что позволяет экспортировать в Power BI Desktop запросы временных рядов, которые создаются в пользовательском интерфейсе, и просматривать диаграммы временных рядов вместе с другими данными бизнес-аналитики. Это открывает возможности для нового класса сценариев для промышленных предприятий Интернета вещей, которые вкладывают средства в Power BI, предоставляя им единую панель для анализа данных из различных источников, включая временные ряды Интернета вещей. |
| Пользовательские приложения, созданные на платформе Аналитики временных рядов Azure 2-го поколения | Аналитика временных рядов Azure 2-го поколения поддерживает [пакет SDK для JavaScript](https://github.com/microsoft/tsiclient/blob/master/docs/API.md). Пакет SDK предоставляет широкие возможности элементов управления и упрощенный доступ к запросам. Используйте пакет SDK для создания пользовательских приложений Интернета вещей на основе службы "Аналитика временных рядов Azure" 2-го поколения в соответствии с потребностями своей компании. Вы также можете использовать [интерфейсы API для запросов](./concepts-query-overview.md) службы "Аналитика временных рядов Azure" 2-го поколения, чтобы передавать данные в пользовательские приложения Интернета вещей. |

## <a name="next-steps"></a>Дальнейшие действия

Приступите к работе со службой "Аналитика временных рядов Azure" 2-го поколения:

> [!div class="nextstepaction"]
> [Краткое руководство](./quickstart-explore-tsi.md)

Ниже приведены примеры использования:

> [!div class="nextstepaction"]
> [Примеры использования службы "Аналитика временных рядов Azure" 2-го поколения](./overview-use-cases.md)