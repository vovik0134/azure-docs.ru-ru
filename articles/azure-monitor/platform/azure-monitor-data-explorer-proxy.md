---
title: Запрос перекрестных ресурсов Azure обозреватель данных с помощью Azure Monitor
description: Используйте Azure Monitor для выполнения межплатформенных запросов между обозреватель данных Azure, рабочими областями Log Analytics и классическими Application Insights приложениями в Azure Monitor.
author: orens
ms.author: bwren
ms.reviewer: bwren
ms.subservice: logs
ms.topic: conceptual
ms.date: 12/02/2020
ms.openlocfilehash: cb586d15e762f88620fe0c91152af41b3f607d74
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/18/2020
ms.locfileid: "97674435"
---
# <a name="cross-resource-query-azure-data-explorer-by-using-azure-monitor"></a>Запрос перекрестных ресурсов Azure обозреватель данных с помощью Azure Monitor
Azure Monitor поддерживает запросы между службами между обозреватель данныхами Azure, [Application Insights](/azure/azure-monitor/app/app-insights-overview)и [log Analytics](/azure/azure-monitor/platform/data-platform-logs). Затем можно выполнить запрос к кластеру Azure обозреватель данных с помощью средств Log Analytics/Application Insights и обратиться к нему в запросах между службами. В этой статье показано, как выполнить запрос между службами.

На следующей схеме показан Azure Monitorный поток между службами.

:::image type="content" source="media\azure-data-explorer-monitor-proxy\azure-monitor-data-explorer-flow.png" alt-text="Схема, показывающая последовательность запросов между пользователем, Azure Monitor, прокси-сервером и обозреватель данных Azure.":::

>[!NOTE]
> Azure Monitor запрос между службами находится в закрытой предварительной версии. Алловлистинг является обязательным. Свяжитесь с [группой обслуживания](mailto:ADXProxy@microsoft.com) с любыми вопросами.

## <a name="cross-query-your-log-analytics-or-application-insights-resources-and-azure-data-explorer"></a>Перекрестное выполнение запросов к Log Analytics или Application Insights ресурсам и обозреватель данных Azure

Запросы между ресурсами можно выполнять с помощью клиентских средств, поддерживающих запросы Kusto. Примерами таких средств могут служить веб-интерфейс Log Analytics, книги, PowerShell и REST API.

Введите идентификатор кластера Azure обозреватель данных в запросе в `adx` шаблоне, за которым следует имя базы данных и таблица.

```kusto
adx('https://help.kusto.windows.net/Samples').StormEvents
```
:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-cross-service-query-example.png" alt-text="Снимок экрана, на котором показан пример запроса между службами.":::

> [!NOTE]
>* В именах баз данных учитывается регистр.
>* Запрос перекрестных ресурсов в виде предупреждения не поддерживается.

## <a name="combine-azure-data-explorer-cluster-tables-with-a-log-analytics-workspace"></a>Объединение таблиц кластера Azure обозреватель данных с рабочей областью Log Analytics

Используйте `union` команду, чтобы объединить таблицы кластера с рабочей областью log Analytics.

```kusto
union customEvents, adx('https://help.kusto.windows.net/Samples').StormEvents
| take 10
```
```kusto
let CL1 = adx('https://help.kusto.windows.net/Samples').StormEvents;
union customEvents, CL1 | take 10
```
:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-union-cross-query.png" alt-text="Снимок экрана, на котором показан пример запроса между службами с помощью команды Union.":::

> [!Tip]
> Допустимый сокращенный формат: *имя_кластера* / *InitialCatalog*. Например, `adx('help/Samples')` преобразуется в `adx('help.kusto.windows.net/Samples')` .

## <a name="join-data-from-an-azure-data-explorer-cluster-in-one-tenant-with-an-azure-monitor-resource-in-another"></a>Объединение данных из кластера Azure Data Explorer в одном арендаторе с ресурсом Azure Monitor в другом

Межклиентские запросы между службами не поддерживаются. Вы вошли в один клиент для выполнения запроса, охватывающего оба ресурса.

Если ресурс обозреватель данных Azure находится в клиенте а и Рабочая область Log Analytics находится в клиенте B, используйте один из следующих методов.

*  Azure Data Explorer позволяет добавлять роли для субъектов в разные арендаторы. Добавьте свой идентификатор пользователя в клиенте B в качестве полномочного пользователя в кластере Azure обозреватель данных. Проверьте, что свойство [трустедекстерналтенант](https://docs.microsoft.com/powershell/module/az.kusto/update-azkustocluster) в кластере Azure обозреватель данных содержит клиент б. Выполните перекрестный запрос полностью в клиенте b.
*  Используйте [лигхсаусе](https://docs.microsoft.com/azure/lighthouse/) для проецирования ресурса Azure Monitor в клиент а.

## <a name="connect-to-azure-data-explorer-clusters-from-different-tenants"></a>Подключение к кластерам Azure Data Explorer из разных арендаторов

Kusto Explorer автоматически выполняет вход в клиент, к которому изначально принадлежит учетная запись пользователя. Чтобы получить доступ к ресурсам в других клиентах с одной и той же учетной записью пользователя, необходимо явно указать `TenantId` в строке подключения:

`Data Source=https://ade.applicationinsights.io/subscriptions/SubscriptionId/resourcegroups/ResourceGroupName;Initial Catalog=NetDefaultDB;AAD Federated Security=True;Authority ID=TenantId`

## <a name="next-steps"></a>Дальнейшие действия
* [Написание запросов](https://docs.microsoft.com/azure/data-explorer/write-queries)
* [Запрос данных в Azure Monitor с помощью обозреватель данных Azure](https://docs.microsoft.com/azure/data-explorer/query-monitor-data)
* [Выполнение запросов журнала между ресурсами в Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/cross-workspace-query)
