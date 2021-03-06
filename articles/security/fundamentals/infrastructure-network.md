---
title: Сетевая архитектура Azure
description: Эта статья содержит общее описание сетевой инфраструктуры Microsoft Azure.
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/08/2020
ms.author: terrylan
ms.openlocfilehash: 3b047489f9cfa3623c11e324cf58114b707c10b7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "89567879"
---
# <a name="azure-network-architecture"></a>Сетевая архитектура Azure
Архитектура сети Azure обеспечивает подключение из Интернета к центрам обработки данных Azure. Любая развернутая Рабочая нагрузка (IaaS, PaaS и SaaS) в Azure использует сеть центра обработки данных Azure.

## <a name="network-topology"></a>Топология сети
Сетевая архитектура центра обработки данных Azure состоит из следующих компонентов:

- Сеть периметра
- Глобальная сеть
- Сеть региональных шлюзов
- Сеть центра обработки данных

![Схема сети Azure](./media/infrastructure-network/network-arch.png)

## <a name="network-components"></a>Сетевые компоненты
Краткое описание сетевых компонентов.

- Сеть периметра

   - Разделительной точка между сетями Microsoft Networking и другими сетями (например, Интернет, Корпоративная сеть)
   - Предоставление пиринга Интернета и [ExpressRoute](../../expressroute/expressroute-introduction.md) в Azure

- Глобальная сеть

   - Интеллектуальная магистральная сеть Майкрософт, охватывающая глобус
   - Обеспечивает подключение между [регионами Azure](https://azure.microsoft.com/global-infrastructure/geographies/)

- Региональный шлюз

   - Точка агрегирования для всех центров обработки данных в регионе Azure
   - Обеспечивает широкие возможности подключения между центрами обработки данных в регионе Azure (например, с несколькими сотнями терабитс для каждого центра обработки данных).

- Сеть центра обработки данных

   - Обеспечивает подключение между серверами в центре обработки данных с низкой пропускной способностью подписки

Указанные выше сетевые компоненты предназначены для обеспечения максимальной доступности для поддержки постоянного, постоянно доступного облачного бизнеса. Избыточность разрабатывается и встроена в сеть из физического аспекта, вплоть до управления протоколом.

## <a name="datacenter-network-resiliency"></a>Устойчивость сети центра обработки данных
Давайте продемонстрируем принцип создания устойчивости с использованием сети центра обработки данных.

Сеть центра обработки данных — это измененная версия [Закрыть сети](https://en.wikipedia.org/wiki/Clos_network), которая обеспечивает большую пропускную способность для трафика масштаба облака. Эта сеть создается с использованием большого количества товарных устройств для снижения влияния, вызванных отдельными сбоями оборудования. Эти устройства стратегически размещаются в разных физических расположениях с отдельным доменом питания и охлаждением, чтобы снизить влияние события среды.  На плоскости управления все сетевые устройства работают в режиме маршрутизации «модель OSI уровня 3», что позволяет избежать возникновения проблем с предысторией цикла трафика. Все пути между разными уровнями активны для обеспечения высокой избыточности и пропускной способности с помощью маршрутизации Equal-Cost с несколькими путями (ECMP).

На следующей схеме показано, что сеть центра обработки данных построена на разных уровнях сетевых устройств. Линии на диаграмме представляют собой группы сетевых устройств, обеспечивающие избыточность и подключение с высокой пропускной способностью.

![Сеть центра обработки данных](./media/infrastructure-network/datacenter-network.png)

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения о действиях корпорации Майкрософт для защиты инфраструктуры Azure приведены в следующих статьях:

- [Объекты, локальная среда и физическая безопасность в Azure](physical-security.md)
- [Доступность инфраструктуры Azure](infrastructure-availability.md)
- [Компоненты и границы информационной системы Azure](infrastructure-components.md)
- [Рабочая сеть Azure](production-network.md)
- [Возможности безопасности Базы данных SQL Azure](infrastructure-sql.md)
- [Операции и управление в рабочей среде Azure](infrastructure-operations.md)
- [Мониторинг инфраструктуры Azure](infrastructure-monitoring.md)
- [Целостность инфраструктуры Azure](infrastructure-integrity.md)
- [Защита данных клиентов в Azure](protection-customer-data.md)
