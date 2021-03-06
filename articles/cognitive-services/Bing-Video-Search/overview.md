---
title: Что такое API Поиска видео Bing
titleSuffix: Azure Cognitive Services
description: Узнайте, как искать видео в Интернете с помощью API Bing для поиска видео.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: overview
ms.date: 12/18/2019
ms.author: scottwhi
ms.openlocfilehash: d5a4c3110186329516f10465c5e80a10b0ceb7b7
ms.sourcegitcommit: 8a1ba1ebc76635b643b6634cc64e137f74a1e4da
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/09/2020
ms.locfileid: "94379826"
---
# <a name="what-is-the-bing-video-search-api"></a>Что такое API Поиска видео Bing

> [!WARNING]
> API Поиска Bing будут перенесены из Cognitive Services в службы Поиска Bing. С **30 октября 2020 г.** подготовку всех новых экземпляров Поиска Bing необходимо будет выполнять в соответствии с процедурой, описанной [здесь](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> API-интерфейсы Поиска Bing, подготовленные с помощью Cognitive Services, будут поддерживаться в течение следующих трех лет или до завершения срока действия вашего Соглашения Enterprise (в зависимости от того, какой период окончится раньше).
> Инструкции по миграции см. в статье о [службах Поиска Bing](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

API Bing для поиска видео позволяет легко добавлять возможности поиска видео в службы и приложения. Отправляя пользовательские поисковые запросы с помощью API, можете получить и отобразить соответствующие и высококачественные видео, которые аналогичны [Видео Bing](https://www.bing.com/video). Используйте этот API для результатов поиска, которые содержат только видео. [API Bing для поиска видео](../bing-web-search/overview.md) может возвращать другие типы веб-содержимого, включая веб-страницы, видео, новости и изображения.

## <a name="bing-video-search-api-features"></a>Функции API Bing для поиска видео

| Компонент                                                                                                                                                                                 | Описание                                                                                                                                                            |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Предложение условий поиска в режиме реального времени](concepts/sending-requests.md#suggest-search-terms-with-the-bing-autosuggest-api) | Улучшите работу приложения, используя [API автозаполнения Bing](../bing-autosuggest/get-suggested-search-terms.md), отображающий предлагаемые условия поиска по мере их ввода. |
| [Фильтрация и ограничение результатов поиска видео](concepts/get-videos.md#filtering-videos)                      | Отфильтруйте возвращаемые видео путем изменения параметров запроса.                                                                                                       |
| [Обрезка, изменения размера и отображение эскизов](../bing-web-search/resize-and-crop-thumbnails.md)                                                | Изменяйте и отображайте предварительные версии видео, возвращаемых API Bing для поиска видео.                                                                                      |
| [Получение видео, набирающих популярность](trending-videos.md) | Поиск видео, набирающих популярность, со всего мира.                                                                                                          |
| [Получение полезных сведений о видео](video-insights.md) | Настройте поиск популярных видео со всего мира.                                                                                                          |

## <a name="workflow"></a>Рабочий процесс

API Bing для поиска видео является веб-службой RESTful, которую легко вызвать с любого языка программирования, поддерживающего выполнение HTTP-запросов и анализ JSON. Вы можете использовать службу с помощью [REST API](./quickstarts/csharp.md) или [пакета SDK](./quickstarts/client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp).

1. Создайте [учетную запись API Cognitive Services](../cognitive-services-apis-create-account.md) с доступом к API-интерфейсам поиска Bing. Если у вас нет подписки Azure, создайте бесплатную [учетную запись](https://azure.microsoft.com/free/cognitive-services/).
2. Отправьте запрос к API с допустимым поисковым запросом.
3. Обработайте ответ API путем анализа возвращенного сообщения JSON.


## <a name="next-steps"></a>Дальнейшие действия

В этой интерактивной демоверсии [API Bing для поиска видео](https://azure.microsoft.com/services/cognitive-services/bing-video-search-api/) показано, как можно быстро настроить поисковый запрос и найти видео в Интернете.

Используйте [это краткое руководство](./quickstarts/csharp.md), чтобы быстро начать работу с первым запросом API.

## <a name="see-also"></a>См. также раздел

* Страница [Video Search API v7 reference](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference) (Справочник по API Bing для поиска видео версии 7) содержит список конечных точек, заголовков и параметров запроса, используемых для запроса результатов поиска.

* В статье [Требования к использованию и отображению API поиска Bing](../bing-web-search/use-display-requirements.md) рассматриваются приемлемые варианты использования содержимого и информации, получаемой с помощью API Bing для поиска.

* Сведения о других доступных API "Поиск Bing" см. на [главной странице](../bing-web-search/overview.md).