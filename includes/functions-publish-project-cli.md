---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/18/2020
ms.author: glenga
ms.openlocfilehash: c99cac6626cb40b8fd368e4fc1d8454bb2195521
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424877"
---
## <a name="deploy-the-function-project-to-azure"></a>Развертывание проекта функций в Azure

После того как вы успешно создадите приложение-функцию в Azure, вы сможете развернуть локальный проект функций с помощью команды [func azure functionapp publish](../articles/azure-functions/functions-run-local.md#project-file-deployment).  

В следующем примере замените `<APP_NAME>` на имя своего приложения.

```console
func azure functionapp publish <APP_NAME>
```

Команда publish показывает результаты, аналогичные приведенным ниже (усечены для простоты):

<pre>
...

Getting site publishing info...
Creating archive for current directory...
Performing remote build for functions project.

...

Deployment successful.
Remote build succeeded!
Syncing triggers...
Functions in msdocs-azurefunctions-qs:
    HttpExample - [httpTrigger]
        Invoke url: https://msdocs-azurefunctions-qs.azurewebsites.net/api/httpexample
</pre>
