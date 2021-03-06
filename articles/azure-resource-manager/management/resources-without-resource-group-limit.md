---
title: Ресурсы без ограничения числа 800
description: Список типов ресурсов Azure, которые могут иметь более 800 экземпляров в группе ресурсов.
ms.topic: conceptual
ms.date: 10/28/2020
ms.openlocfilehash: c2d5797a7ec354f41d510ec6e884968bf9dbdbcd
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/18/2020
ms.locfileid: "97672370"
---
# <a name="resources-not-limited-to-800-instances-per-resource-group"></a>Ресурсы, не ограниченные 800 экземплярами на группу ресурсов

По умолчанию в каждой группе ресурсов можно развернуть до 800 экземпляров типа ресурса. Однако некоторые типы ресурсов исключены из пределов числа экземпляров 800. В этой статье перечислены типы ресурсов Azure, которые могут содержать более 800 экземпляров в группе ресурсов. Все остальные типы ресурсов ограничены 800 экземплярами.

Для некоторых типов ресурсов необходимо обратиться в службу поддержки, чтобы снять ограничение на число экземпляров 800. Эти типы ресурсов указаны в этой статье.


## <a name="microsoftautomation"></a>Microsoft.Automation

* automationAccounts

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

* еджесубскриптионс
* линкедсубскриптионс
* registrations
* registrations/customerSubscriptions
* registrations/products

## <a name="microsoftbotservice"></a>Microsoft.BotService

* Ботсервицес — по умолчанию ограничено до 800 экземпляров. Это ограничение можно увеличить, обратившись в службу поддержки.

## <a name="microsoftcompute"></a>Microsoft.Compute;

* disks
* galleries
* galleries/images
* galleries/images/versions
* images
* snapshots
* virtualMachineScaleSets — по умолчанию ограничено до 800 экземпляров. Это ограничение можно увеличить, обратившись в службу поддержки.
* virtualMachines

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

* containerGroups

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

* registries/buildTasks
* реестров/Буилдтаскс/Листсаурцерепоситорипропертиес
* registries/buildTasks/steps
* реестры/Буилдтаскс/шаги/Листбуилдаргументс
* registries/eventGridFilters
* registries/replications
* registries/tasks
* registries/webhooks

## <a name="microsoftd365customerinsights"></a>Microsoft. D365CustomerInsights

* instances

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

* servers

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

* флексиблесерверс
* servers

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

* флексиблесерверс
* серверграупс
* servers
* serversv2

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

* Labs/virtualMachines — по умолчанию ограничено до 800 экземпляров. 
* schedules

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

* services;

## <a name="microsofteventhub"></a>Microsoft.EventHub

* clusters
* пространства имен

## <a name="microsoftexperimentation"></a>Microsoft.Experimentation

* експериментворкспацес

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

* аутоманажедвмконфигуратионпрофилес
* конфигуратионпрофилеассигнментс
* guestConfigurationAssignments
* software
* софтвареупдатепрофиле
* софтвареупдатес

## <a name="microsoftinsights"></a>microsoft.insights

* metricalerts

## <a name="microsoftlogic"></a>Microsoft.Logic

* integrationAccounts
* workflows

## <a name="microsoftmedia"></a>Microsoft.Media

* mediaservices/liveEvents

## <a name="microsoftnetapp"></a>Microsoft.NetApp

* нетаппаккаунтс
* Нетаппаккаунтс/КапаЦитипулс
* Нетаппаккаунтс/КапаЦитипулс/тома
* Нетаппаккаунтс/КапаЦитипулс/Volumes/Маунттаржетс
* Нетаппаккаунтс/КапаЦитипулс/тома/моментальные снимки

## <a name="microsoftnetwork"></a>Microsoft.Network.

* аппликатионгатевайвебаппликатионфиреваллполиЦиес
* applicationSecurityGroups
* bastionHosts
* ddosProtectionPlans
* dnszones
* dnszones/A
* dnszones/AAAA
* dnszones/CAA
* dnszones/CNAME
* dnszones/MX
* dnszones/NS
* dnszones/PTR
* dnszones/SOA
* dnszones/SRV
* dnszones/TXT
* dnszones/all
* dnszones/recordsets
* networkIntentPolicies
* networkInterfaces
* privateDnsZones
* Приватеднсзонес/A
* Приватеднсзонес/AAAA
* Приватеднсзонес и CNAME
* Приватеднсзонес/MX
* Приватеднсзонес/PTR
* Приватеднсзонес/SOA
* Приватеднсзонес/SRV
* Приватеднсзонес/TXT
* Приватеднсзонес/все
* Приватеднсзонес/Виртуалнетворклинкс
* приватиндпоинтс
* privateLinkServices
* publicIPAddresses — по умолчанию ограничено до 800 экземпляров. Это ограничение можно увеличить, обратившись в службу поддержки.
* serviceEndpointPolicies
* trafficmanagerprofiles
* virtualNetworkTaps

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk

* rootResources

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

* Воркспацеколлектионс — по умолчанию ограничено до 800 экземпляров. Это ограничение можно увеличить, обратившись в службу поддержки.

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

* емкости — по умолчанию ограничение составляет 800 экземпляров. Это ограничение можно увеличить, обратившись в службу поддержки.

## <a name="microsoftrelay"></a>Microsoft.Relay

* пространства имен

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

* jobcollections

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

* пространства имен

## <a name="microsoftsingularity"></a>Microsoft. Единственное,

* accounts
* Accounts/АккаунткуотаполиЦиес
* Accounts/ГраупполиЦиес
* учетные записи и задания
* Accounts/Сторажеконтаинерс

## <a name="microsoftstorage"></a>Microsoft.Storage;

* storageAccounts

## <a name="microsoftsql"></a>Microsoft.Sql

* servers/databases

## <a name="microsoftweb"></a>Microsoft.Web

* apiManagementAccounts/apis
* sites

## <a name="next-steps"></a>Следующие шаги

Полный список квот и ограничений см. в статье [Подписка Azure, границы, квоты и ограничения службы](azure-subscription-service-limits.md).
