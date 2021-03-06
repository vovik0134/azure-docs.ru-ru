---
title: Рекомендации по мониторингу — база данных Azure для MySQL
description: В этой статье описываются рекомендации по мониторингу базы данных Azure для MySQL.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 11/23/2020
ms.openlocfilehash: 59301e26f4d42056322ba5a7cdaff1555c531096
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/30/2020
ms.locfileid: "96355053"
---
# <a name="best-practices-for-monitoring-azure-database-for-mysql--single-server"></a>Рекомендации по мониторингу базы данных Azure для MySQL — одиночный сервер

Узнайте о рекомендациях, которые можно использовать для наблюдения за операциями базы данных и обеспечения того, что производительность не будет скомпрометирована при увеличении размера данных. По мере добавления новых возможностей на платформу мы будем продолжать уточнять рекомендации, описанные в этом разделе.

## <a name="layout-of-the-current-monitoring-toolkit"></a>Макет текущего набора средств мониторинга

База данных Azure для MySQL предоставляет средства и методы, которые можно использовать для упрощения мониторинга использования, добавления или удаления ресурсов (например, ЦП, памяти или операций ввода-вывода), устранения потенциальных проблем и повышения производительности базы данных. Вы можете регулярно [отслеживать метрики производительности](concepts-monitoring.md#metrics) , чтобы просматривать средние, максимальные и минимальные значения для различных диапазонов времени.

Вы можете [настроить оповещения](howto-alert-on-metric.md#create-an-alert-rule-on-a-metric-from-the-azure-portal) для порогового значения метрики, чтобы получать уведомления о том, что сервер достиг этих ограничений, и предпринять соответствующие действия.  

Выполните мониторинг сервера базы данных, чтобы убедиться, что ресурсы, назначенные базе данных, могут справиться с рабочей нагрузкой приложения. Если база данных достигает пределов ресурсов, учитывайте следующее.
    * Определение и оптимизация основных ресурсоемких запросов. 
    * Добавление дополнительных ресурсов путем обновления уровня служб.

### <a name="cpu-utilization"></a>загрузка ЦП;
Мониторинг использования ЦП и нехватки ресурсов ЦП в базе данных. Если загрузка ЦП составляет 90% или более, необходимо увеличить масштаб вычислительных ресурсов, увеличив число виртуальных ядер или масштаб до следующей ценовой категории.  Убедитесь, что пропускная способность или параллелизм должны быть ожидаемыми при масштабировании ЦП. 

### <a name="memory"></a>Память 
Объем памяти, доступный для сервера базы данных, пропорционален [количеству виртуальных ядер](concepts-pricing-tiers.md). Убедитесь, что объем памяти достаточно велик для рабочей нагрузки. Нагрузочное тестирование приложения для проверки достаточности памяти для операций чтения и записи. Если потребление памяти базы данных часто увеличивается сверх заданного порогового значения, это означает, что необходимо обновить экземпляр, увеличив уровень производительности виртуальных ядер или выше. Используйте [хранилище запросов](concepts-query-store.md), [рекомендации по производительности запросов](concepts-performance-recommendations.md) , чтобы выявление запросов с наибольшей длительностью, наиболее выполненной. Узнайте о возможностях оптимизации. 

### <a name="storage"></a>Служба хранилища 
[Объем памяти](howto-create-manage-server-portal.md#scale-compute-and-storage) , подготовленный для сервера MySQL, определяет операции ввода-вывода для сервера. Хранилище, используемое службой, включает файлы базы данных, журналы транзакций, журналы сервера и моментальные снимки резервных копий. Убедитесь, что объем потребляемого дискового пространства не превышает 85 процентов от общего объема подготовленного диска. В этом случае необходимо удалить или заархивировать данные с сервера базы данных, чтобы освободить место. 

### <a name="network-traffic"></a>Сетевой трафик 

**Пропускная способность приема сети, пропускная способность передачи в сети** — частота сетевого трафика от экземпляра MySQL и от него в мегабайтах в секунду. Необходимо оценить требования к пропускной способности для сервера и постоянно отслеживать трафик, если пропускная способность меньше ожидаемой. 

### <a name="database-connections"></a>подключения к базе данных; 
**Подключения к базе данных** . количество клиентских сеансов, подключенных к базе данных Azure для MySQL, должно быть согласовано с [ограничениями на количество подключений для выбранного размера SKU](concepts-server-parameters.md#max_connections) . 


## <a name="next-steps"></a>Дальнейшие действия

- [Рекомендации по производительности базы данных Azure для MySQL](concept-performance-best-practices.md)
- [Рекомендации по работе сервера с базой данных Azure для MySQL](concept-operation-excellence-best-practices.md)
