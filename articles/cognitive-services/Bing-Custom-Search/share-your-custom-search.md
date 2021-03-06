---
title: Совместное использование экземпляра пользовательского поиска (Пользовательский поиск Bing)
titleSuffix: Azure Cognitive Services
description: Легко разрешите совместное редактирование и тестирование вашего экземпляра, отправляя его членам вашей команды.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: 6141bd67681df757536792981d47756a20ed33db
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/30/2020
ms.locfileid: "96353329"
---
# <a name="share-your-custom-search-instance"></a>Совместное использование экземпляра пользовательского поиска

> [!WARNING]
> API Поиска Bing будут перенесены из Cognitive Services в службы Поиска Bing. С **30 октября 2020 г.** подготовку всех новых экземпляров Поиска Bing необходимо будет выполнять в соответствии с процедурой, описанной [здесь](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> API-интерфейсы Поиска Bing, подготовленные с помощью Cognitive Services, будут поддерживаться в течение следующих трех лет или до завершения срока действия вашего Соглашения Enterprise (в зависимости от того, какой период окончится раньше).
> Инструкции по миграции см. в статье о [службах Поиска Bing](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Вы можете разрешить совместное редактирование и тестирование своего экземпляра, предоставив к нему доступ участникам вашей команды. Вы можете предоставить доступ к своему экземпляру любому пользователю. Для этого нужен только его адрес электронной почты. Предоставление общего доступа к экземпляру:

- Войдите в службу [Пользовательский поиск](https://customsearch.ai).
- Выберите экземпляр службы.
- Щелкните значок параметров (в виде шестеренки). 
- В разделе **Share Your Instance** (Совместное использование экземпляра) введите адрес электронной почты пользователя, которому необходимо предоставить доступ к экземпляру, и нажмите кнопку **Share** (Общий доступ). 

После указания адреса электронной почты он добавляется в список **Instance shared with** (Экземпляр используется совместно с). Повторите этот процесс для каждого пользователя, которому вы хотите предоставить доступ к экземпляру. 

Для добавления адреса электронной почты в список владелец адреса не обязан иметь учетную запись пользовательского поиска. Ему необходимо будет зарегистрироваться в службе пользовательского поиска для внесения изменений в конфигурацию. После предоставления пользователю доступа к экземпляру он сможет увидеть его в своем списке экземпляров пользовательского поиска. Одновременно изменять экземпляр может только один пользователь. Если вы попытаетесь внести изменения в экземпляр, который редактируется другим пользователем, то отобразится предупреждение. Экземпляр можно использовать совместно с не более чем 10 пользователями.

## <a name="stop-sharing"></a>Прекращение общего доступа

Чтобы прекратить совместное использование экземпляра с другим пользователем, удалите из списка его адрес электронной почты с помощью значка удаления. Это также приводит к удалению экземпляра из списка экземпляров пользователя.

## <a name="next-steps"></a>Дальнейшие действия

- [Настройка пользовательского автозаполнения](define-custom-suggestions.md)