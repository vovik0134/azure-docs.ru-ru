---
title: Примеры шаблонов Resource Manager для правил сбора данных
description: Примеры шаблонов Azure Resource Manager для создания связей между правилами сбора данных и виртуальными машинами в Azure Monitor.
ms.subservice: logs
ms.topic: sample
author: bwren
ms.author: bwren
ms.date: 11/17/2020
ms.openlocfilehash: 12e6aa85f7d8c2c4aebe80efc92f771917e6a1d5
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/06/2020
ms.locfileid: "96746018"
---
# <a name="resource-manager-template-samples-for-data-collection-rules-in-azure-monitor"></a>Примеры шаблонов Resource Manager для правил сбора данных в Azure Monitor
Эта статья содержит примеры [шаблонов Azure Resource Manager](../../azure-resource-manager/templates/template-syntax.md) для развертывания и настройки [агента Log Analytics](../platform/log-analytics-agent.md) и [расширения системы диагностики](../platform/diagnostics-extension-overview.md) для виртуальных машин в Azure Monitor. Каждый пример включает файл шаблона и файл параметров с примерами значений для предоставления шаблона.

[!INCLUDE [azure-monitor-samples](../../../includes/azure-monitor-resource-manager-samples.md)]


## <a name="create-association-with-azure-vm"></a>Создание связи с виртуальной машиной Azure

В приведенном ниже примере на виртуальной машине Windows Azure устанавливается агент Azure Monitor. Между виртуальной машиной Azure и правилом сбора данных устанавливается связь.

### <a name="template-file"></a>Файл шаблона

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string",
            "metadata": {
                "description": "Name of the virtual machine."
            }
        },
        "associationName": {
            "type": "string",
            "metadata": {
                "description": "Name of the association."
            }
        },
        "dataCollectionRuleId": {
            "type": "string",
            "metadata": {
                "description": "Resource ID of the data collection rule."
            }
        }
    },
    "resources": [
        {
            "type": "Microsoft.Compute/virtualMachines/providers/dataCollectionRuleAssociations",
            "name": "[concat(parameters('vmName'),'/microsoft.insights/', parameters('associationName'))]",
            "apiVersion": "2019-11-01-preview",
            "properties": {
                "description": "Association of data collection rule. Deleting this association will break the data collection for this virtual machine.",
                "dataCollectionRuleId": "[parameters('dataCollectionRuleId')]"
            }
        }
    ]
}
```

### <a name="parameter-file"></a>Файл параметров

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "vmName": {
        "value": "my-windows-vm"
      },
      "location": {
        "value": "eastus"
      }
  }
}
```

## <a name="create-association-ith-azure-arc"></a>Создание связи с Azure Arc

В приведенном ниже примере на виртуальной машине Windows Azure устанавливается агент Azure Monitor. Между сервером с поддержкой Azure Arc и правилом сбора данных устанавливается связь.

### <a name="template-file"></a>Файл шаблона

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string",
            "metadata": {
                "description": "Name of the virtual machine."
            }
        },
        "associationName": {
            "type": "string",
            "metadata": {
                "description": "Name of the association."
            }
        },
        "dataCollectionRuleId": {
            "type": "string",
            "metadata": {
                "description": "Resource ID of the data collection rule."
            }
        }
    },
    "resources": [
        {
            "type": "Microsoft.HybridCompute/machines/providers/dataCollectionRuleAssociations",
            "name": "[concat(parameters('machineName'),'/microsoft.insights/', parameters('associationName'))]",
            "apiVersion": "2019-11-01-preview",
            "properties": {
                "description": "Association of data collection rule. Deleting this association will break the data collection for this Arc server.",
                "dataCollectionRuleId": "[parameters('dataCollectionRuleId')]"
            }
        }
    ]
}
```

### <a name="parameter-file"></a>Файл параметров

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "vmName": {
        "value": "my-windows-vm"
      },
      "location": {
        "value": "eastus"
      }
  }
}
```


## <a name="next-steps"></a>Дальнейшие действия

* [Другие примеры шаблонов для Azure Monitor](resource-manager-samples.md).
* [Общие сведения об агенте Log Analytics](../platform/log-analytics-agent.md).
* [Общие сведения о расширении диагностики Azure ](../platform/diagnostics-extension-overview.md).
