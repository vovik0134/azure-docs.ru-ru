---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/04/2020
ms.author: glenga
ms.openlocfilehash: 72588ccfd9ad00305cbdeaf8132999731d86b9b1
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/18/2020
ms.locfileid: "92164930"
---
*Уровень ведения журнала* назначается каждому журналу. Значение является целым числом, указывающим относительную важность:

|LogLevel    |Код| Описание |
|------------|---|--------------|
|Трассировка       | 0 |Журналы, содержащие наиболее подробные сообщения. Эти сообщения могут содержать конфиденциальные данные приложения. Эти сообщения по умолчанию отключены, и их никогда не следует включать в рабочей среде.|
|Отладка       | 1 | Журналы, используемые для интерактивного исследования во время разработки. Эти журналы в основном содержат сведения, полезные при отладки и не представляющие ценности в долгосрочной перспективе. |
|Сведения | 2 | Журналы, отслеживающие общий поток работы приложения. Эти журналы должны быть полезны в долгосрочной перспективе. |
|Предупреждение     | 3 | Журналы, которые выделяют ненормальное или неожиданное событие в потоке приложения, но не вызывают прекращение выполнения приложения. |
|Error       | 4 | Журналы, которые выделяют, когда текущий поток выполнения остановлен из-за сбоя. Эти ошибки должны указывать на сбой в текущем действии, а не на уровне приложения. |
|Critical    | 5 | Журналы, описывающие неустранимый сбой приложения или системы либо неустранимый сбой, который требует немедленного внимания. |
|None        | 6 | Отключает ведение журнала для указанной категории. |

[ *host.jsв* конфигурации файла](../articles/azure-functions/functions-host-json.md) определяет, сколько записей приложение функций регистрирует для отправки в Application Insights.  
