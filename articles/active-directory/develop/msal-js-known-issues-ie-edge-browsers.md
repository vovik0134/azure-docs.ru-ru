---
title: Проблемы с Internet Explorer и Microsoft Edge (MSAL.js) | Azure
titleSuffix: Microsoft identity platform
description: Сведения об известных проблемах при использовании библиотеки проверки подлинности Microsoft для JavaScript (MSAL.js) в браузерах Internet Explorer и Microsoft Edge.
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: troubleshooting
ms.workload: identity
ms.date: 05/18/2020
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 2a471504b88791b5bfb6ce6cc7c81d60bfbe5028
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "83772086"
---
# <a name="known-issues-on-internet-explorer-and-microsoft-edge-browsers-msaljs"></a>Известные проблемы с браузерами Internet Explorer и Microsoft Edge browsers (MSAL.js)

## <a name="issues-due-to-security-zones"></a>Проблемы из-за зон безопасности
Нам поступили многочисленные отчеты о проблемах с проверкой подлинности в IE и Microsoft Edge (после обновления браузера *Microsoft Edge до 40.15063.0.0*). Мы отслеживаем их и уже уведомили о происходящем команду разработчиков Microsoft Edge. Пока они работают над решением, мы представляем вам описание наиболее распространенных проблем с возможными обходными путями.

### <a name="cause"></a>Причина
Причина большинства этих проблем в следующем. Хранилище сеансов и локальное хранилище разделены в браузере Microsoft Edge зонами безопасности. В данной версии Microsoft Edge при перенаправлении приложения через зоны данные удаляются из хранилища сеансов и локального хранилища. В частности, данные из хранилища сеансов удаляются в процессе стандартной навигации по браузеру. В режиме InPrivate удаляются данные из обоих хранилищ. MSAL.js сохраняет определенное состояние в хранилище сеансов и проверяет это состояние в процессе проверки подлинности. При удалении данных из хранилища сеансов это состояние теряется, пользователь не может пройти проверку подлинности.

### <a name="issues"></a>Проблемы

- **Бесконечные циклы перенаправления и перезагрузки страниц в процессе проверки подлинности**. Когда пользователи входят в приложение в браузере Microsoft Edge, они перенаправляются со страницы входа AAD и попадают в бесконечный цикл перенаправления. В результате страницы постоянно перезагружаются. Как правило, это сопровождается ошибкой `invalid_state` в хранилище сеансов.

- **Бесконечные циклы получения маркеров и ошибка AADSTS50058**. Когда выполняемое в Microsoft Edge приложение пытается получить маркер для ресурса, приложение может попасть в бесконечный цикл вызова "получить маркер". Кроме того, в трассировке сети появляется сообщение о следующей ошибке AAD:

    `Error :login_required; Error description:AADSTS50058: A silent sign-in request was sent but no user is signed in. The cookies used to represent the user's session were not sent in the request to Azure AD. This can happen if the user is using Internet Explorer or Edge, and the web app sending the silent sign-in request is in different IE security zone than the Azure AD endpoint (login.microsoftonline.com)`

- **Всплывающее окно не закрывается или зависает при попытке входа с использованием всплывающего окна проверки подлинности**. При проверке подлинности во всплывающем окне в Microsoft Edge или IE (режим InPrivate) после ввода учетных данных и входа в систему всплывающее окно не закрывается, так как MSAL.js теряет дескриптор для всплывающего окна, если в навигации участвует несколько доменов из разных зон безопасности.  

### <a name="update-fix-available-in-msaljs-023"></a>Обновление: в MSAL.js 0.2.3 доступно исправление
В [MSAL.js 0.2.3](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases) выпущены исправления проблем, связанных с циклом перенаправления проверки подлинности. Включите флаг `storeAuthStateInCookie` в конфигурации MSAL.js, чтобы воспользоваться этим исправлением. По умолчанию этому флагу присвоено значение False.

Когда флаг `storeAuthStateInCookie` включен, MSAL.js будет использовать cookie-фалы браузера для сохранения состояния, необходимого для проверки в процессе проверки подлинности.

> [!NOTE]
> Это исправление еще не доступно для программ-оболочек msal-angular и msal-angularjs. Это исправление не решает проблему с всплывающими окнами.

Используйте обходные пути ниже.

#### <a name="other-workarounds"></a>Другие обходные пути
Не забудьте убедиться, что проблема возникает только в определенной версии браузера Microsoft Edge и отсутствует в других браузерах, прежде чем применять эти обходные пути.  
1. В качестве первого шага обхода этих проблем необходимо убедиться, что домен приложения и любые другие сайты, участвующие в перенаправлениях в процессе проверки подлинности, добавлены в качестве доверенных в настройках безопасности браузера, то есть относятся к одной и той же зоне безопасности.
Для этого выполните следующие действия.
    - Откройте **Internet Explorer** и нажмите **Настройки** (значок шестеренки) в правом верхнем углу.
    - Выберите **Свойства обозревателя**.
    - Перейдите на вкладку **Безопасность**.
    - В разделе **Доверенные сайты** нажмите кнопку **Сайты** и в открытом диалоговом окне добавьте нужные URL-адреса.

2. Как отмечено ранее, поскольку при стандартной навигации данные удаляются только из хранилища сеансов, можно настроить MSAL.js для использования только локального хранилища. Это можно сделать с помощью параметра конфигурации `cacheLocation` при инициализации MSAL.

Обратите внимание, что это не решит проблему для сеансов InPrivate, так как в этом случае данные удаляются из обоих хранилищ.

## <a name="issues-due-to-popup-blockers"></a>Проблемы, связанные с блокировщиками всплывающих окон

В некоторых случаях всплывающие окна блокируются в IE и Microsoft Edge, например если второе всплывающее окно появляется во время [многофакторной проверки подлинности](../authentication/concept-mfa-howitworks.md). В браузере отобразится оповещение и запрос на отображение всплывающих окон только сейчас или всегда. Если вы разрешите отображать всплывающие окна, браузер откроет всплывающее окно автоматически и вернет для него дескриптор `null`. В результате у библиотеки нет дескриптора для окна, закрыть всплывающее окно невозможно. Та же ситуация не возникает в Chrome, когда браузер предлагает вам разрешить отображение всплывающих окон, так как всплывающие окна в этом браузере не отображаются автоматически.

В качестве **обходного пути** разработчикам необходимо разрешить отображение всплывающих окон в IE и Microsoft Edge перед началом работы с приложением.

## <a name="next-steps"></a>Дальнейшие действия
Узнайте больше об [использовании MSAL.js в Internet Explorer](msal-js-use-ie-browser.md).
