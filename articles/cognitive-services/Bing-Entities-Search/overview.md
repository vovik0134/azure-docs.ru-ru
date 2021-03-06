---
title: Основные сведения об API Bing для поиска сущностей
titleSuffix: Azure Cognitive Services
description: Дополнительные сведения об API "Поиск сущностей Bing" и о том, как извлекать и искать сущности и места на основе поисковых запросов.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: overview
ms.date: 12/18/2019
ms.author: scottwhi
ms.openlocfilehash: 2a3d971ce9a4f89555eb3ffa489f8b19172a4b83
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/30/2020
ms.locfileid: "96351475"
---
# <a name="what-is-bing-entity-search-api"></a>Основные сведения об API Bing для поиска сущностей

> [!WARNING]
> API Поиска Bing будут перенесены из Cognitive Services в службы Поиска Bing. С **30 октября 2020 г.** подготовку всех новых экземпляров Поиска Bing необходимо будет выполнять в соответствии с процедурой, описанной [здесь](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> API-интерфейсы Поиска Bing, подготовленные с помощью Cognitive Services, будут поддерживаться в течение следующих трех лет или до завершения срока действия вашего Соглашения Enterprise (в зависимости от того, какой период окончится раньше).
> Инструкции по миграции см. в статье о [службах Поиска Bing](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

API Bing для поиска сущностей отправляет запрос на поиск в Bing и получает результаты, которые включают сущности и места. Результаты поиска мест включают рестораны, отели или другие местные учреждения. Bing возвращает расположения, если в запросе указывается имя местной организации, или запрашивает ее тип (например, рестораны рядом со мной). Bing возвращает сущности, если в запросе указаны хорошо известные люди, места (туристическая достопримечательность, штаты, страны, регионы и т. д.) или вещи.

|Компонент  |Описание  |
|---------|---------|
|[Поисковые предложения, предоставляемые в режиме реального времени](concepts/search-for-entities.md#suggest-search-terms-with-the-bing-autosuggest-api)     | Предоставляйте поисковые предложения в виде раскрывающегося списка по мере ввода данных пользователями.       | 
| [Уточнение сущностей](concepts/search-for-entities.md#the-bing-entity-search-api-response)  | Получение нескольких сущностей для запросов с несколькими возможными значениями. |
| [Поиск мест](concepts/search-for-entities.md#find-places) | Поиск и возврат сведений о местных учреждениях и сущностях.  |

## <a name="workflow"></a>Рабочий процесс

API Bing для поиска сущностей является веб-службой RESTful, которую легко вызвать с любого языка программирования, поддерживающего выполнение HTTP-запросов и анализа JSON. Вы можете использовать службу с помощью REST API или пакета SDK.

1. Создайте [учетную запись API Cognitive Services](../cognitive-services-apis-create-account.md) с доступом к API-интерфейсам поиска Bing. Если у вас нет подписки Azure, создайте бесплатную [учетную запись](https://azure.microsoft.com/free/cognitive-services/).
2. Отправьте запрос к API с допустимым поисковым запросом.
3. Обработайте ответ API путем анализа возвращенного сообщения JSON.

## <a name="next-steps"></a>Дальнейшие действия

* Попробуйте [интерактивную демоверсию](https://azure.microsoft.com/services/cognitive-services/bing-entity-search-api/) API Bing для поиска сущностей. 
* Сведения о том, как быстро создать первый запрос, см. в [этом кратком руководстве](quickstarts/csharp.md).
* См. раздел справочника по [API Bing для поиска сущностей версии 7](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference).
* В статье [Требования к использованию и отображению API поиска Bing](../bing-web-search/use-display-requirements.md) рассматриваются приемлемые варианты использования содержимого и информации, получаемой с помощью API Bing для поиска.
* Сведения о других доступных API "Поиск Bing" см. на [главной странице](../bing-web-search/overview.md).