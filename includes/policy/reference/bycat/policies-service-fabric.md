---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 11/20/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 70f547dc5d035b57f636719c5500ba811fc69a1a
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2020
ms.locfileid: "96007944"
---
|Имя<br /><sub>(портал Azure)</sub> |Описание |Действие |Версия<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Свойство ClusterProtectionLevel в кластерах Service Fabric должно иметь значение EncryptAndSign](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F617c02be-7f02-4efd-8836-3180d47b6c68) |Service Fabric поддерживает три уровня защиты (None (Нет), Sign (Подписывание) и EncryptAndSign (Шифрование и подписывание)) для взаимодействия между узлами с использованием основного сертификата кластера. Задайте уровень защиты, чтобы обеспечить шифрование и цифровое подписывание всех сообщений между узлами. |Audit, Deny, Disabled |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditClusterProtectionLevel_Audit.json) |
|[Кластеры Service Fabric должны использовать только Azure Active Directory для проверки подлинности клиента](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb54ed75b-3e1a-44ac-a333-05ba39b99ff0) |Аудит проверки подлинности клиента только через Azure Active Directory в Service Fabric |Audit, Deny, Disabled |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditADAuth_Audit.json) |
