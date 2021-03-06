---
title: Общие сведения о клиентской библиотеке для вызовов Служб коммуникации Azure
titleSuffix: An Azure Communication Services concept document
description: Обзор клиентской библиотеки для вызовов.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: f621d11553101c2c0bcfce804b26c218ae58670c
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/03/2020
ms.locfileid: "96576474"
---
# <a name="calling-client-library-overview"></a>Общие сведения о клиентской библиотеке для вызовов

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]

Различают два отдельных класса клиентских библиотек для вызовов: для *клиентов* и *служб.* Современные клиентские библиотеки предназначены для работы с конечными пользователями: веб-сайтами и собственными приложениями.

Клиентские библиотеки службы в настоящее время недоступны. Однако они обеспечивают доступ к необработанным плоскостям данных речи и видео, которые подходят для интеграции с чат-ботами и другими службами.

## <a name="calling-client-library-capabilities"></a>Возможности клиентской библиотеки для вызовов

В следующем списке содержится набор функций, которые сейчас доступны в клиентских библиотеках для вызовов Служб коммуникации Azure.

| Группа функций | Функция                                                                                                          | JS  | Java (Android) | Objective-C (iOS) 
| ----------------- | ------------------------------------------------------------------------------------------------------------------- | ---  | -------------- | -------------
| Основные возможности | Настройка индивидуального вызова между двумя пользователями                                                                           | ✔️   | ✔️            | ✔️  
|                   | Настройка группового вызова с более чем двумя пользователями (не более 350 пользователей)                                                       | ✔️   | ✔️            | ✔️ 
|                   | Перевести индивидуальный вызов с двумя пользователями в групповой вызов с более чем двумя пользователями                                 | ✔️   | ✔️            | ✔️ 
|                   | Присоединение к групповому вызову после вызова                                                                              | ✔️   | ✔️            | ✔️ 
|                   | Приглашение другого участника голосовых вызовов через IP присоединиться к текущему групповому вызову                                                       | ✔️   | ✔️            | ✔️
|                   | Включение или отключение видео                                                         | ✔️   | ✔️            | ✔️ 
|                   | Включение или отключение микрофона                                                                                                     | ✔️   | ✔️            | ✔️         
|                   | Переключение между камерами                                                                                              | ✔️   | ✔️            | ✔️           
|                   | Локальное удержание/отмена удержания                                                                                                  | ✔️   | ✔️            | ✔️           
|                   | Активный динамик                                                                                                      | ✔️   | ✔️            | ✔️           
|                   | Выбор динамика для вызовов                                                                                            | ✔️   | ✔️            | ✔️           
|                   | Выбор микрофона для вызовов                                                                                         | ✔️   | ✔️            | ✔️           
|                   | Отображение состояния участника<br/>*Бездействие, Предварительные сигналы и сообщения, Подключение, Подключено, На удержании, В зале ожидания, Разъединенный*         | ✔️   | ✔️            | ✔️           
|                   | Отображение состояния вызова<br/>*Предварительные сигналы и сообщения, Входящий, Подключение, Звонок, Подключено, На удержании, Отключение, Разъединенный* | ✔️   | ✔️            | ✔️           
|                   | Отображение того, отключен ли у участника микрофон                                                                                      | ✔️   | ✔️            | ✔️           
|                   | Отображение причины, по которой участник закончил вызов                                                                       | ✔️   | ✔️            | ✔️     
| Общий доступ к экрану    | Общий доступ ко всему экрану в приложении                                                                 | ✔️   | ❌            | ❌           
|                   | Предоставление общего доступа к определенному приложению (из списка запущенных приложений)                                                | ✔️   | ❌            | ❌           
|                   | Предоставление общего доступа к вкладке веб-браузера из списка открытых вкладок                                                                  | ✔️   | ❌            | ❌           
|                   | Участник может просмотреть демонстрацию удаленного экрана                                                                            | ✔️   | ✔️            | ✔️         
| Список            | Список участников                                                                                                   | ✔️   | ✔️            | ✔️           
|                   | Удалить участника                                                                                                | ✔️   | ✔️            | ✔️         
| ТСОП              | Создать индивидуальный вызов с участником ТСОП                                                                     | ✔️   | ✔️            | ✔️   
|                   | Создать групповой вызов с участниками ТСОП                                                                           | ✔️   | ✔️            | ✔️
|                   | Перевести индивидуальный вызов с участником ТСОП в групповой вызов                                                 | ✔️   | ✔️            | ✔️
|                   | Возможность отключиться от группового вызова в качестве участника ТСОП                                                                    | ✔️   | ✔️            | ✔️   
| Общие сведения           | Проверьте микрофон, динамик и камеру с помощью службы проверки звука (доступна по вызову 8:echo123)                   |  ✔️  | ✔️            | ✔️   

## <a name="calling-client-library-browser-support"></a>Поддержка браузера клиентской библиотеки для вызовов

В следующей таблице представлен набор поддерживаемых браузеров и доступных в настоящее время версий.

|                                  | Windows          | MacOS          | Ubuntu | Linux  | Android | iOS    |
| -------------------------------- | ---------------- | -------------- | ------- | ------ | ------ | ------ |
| **Клиентская библиотека для вызовов** | Chrome*, новый Edge | Chrome *, Safari** | Chrome*  | Chrome* | Chrome* | Safari** |


* Обратите внимание, что для Chrome кроме двух предыдущих выпусков поддерживается также последняя версия.<br/>

** Обратите внимание, что поддерживаются браузеры Safari версии 13.1 +. Исходящее видео в Safari macOS пока не поддерживается, однако оно поддерживается в iOS. Возможность общего доступа к исходящему экрану поддерживается только в настольных устройствах iOS.

## <a name="calling-client---browser-security-model"></a>Модель безопасности вызова между клиентом и браузером

### <a name="user-webrtc-over-https"></a>Работа с WebRTC по протоколу HTTPS

Интерфейсы API WebRTC, например `getUserMedia`, требуют, чтобы вызывающее их приложение обслуживалось по протоколу HTTPS.

Для локальной разработки можно использовать `http://localhost`.

### <a name="embed-the-communication-services-calling-sdk-in-an-iframe"></a>Внедрение пакета SDK для вызова Служб коммуникации в iframe

В различных браузерах была внедрена новая [политика разрешений (также называемая политикой компонентов)](https://www.w3.org/TR/permissions-policy-1/#iframe-allow-attribute). Эта политика влияет на сценарии вызова, контролируя то, как приложения могут получать доступ к камере и микрофону устройства через элемент iframe с различными источниками.

Если вы хотите использовать элемент iframe для размещения части приложения из другого домена, необходимо добавить в него атрибут `allow` с правильным значением.

Например, этот iframe обеспечивает доступ как к камере, так и к микрофону:

```html
<iframe allow="camera *; microphone *">
```

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Начало работы с вызовами](../../quickstarts/voice-video-calling/getting-started-with-calling.md)

Дополнительные сведения см. в следующих статьях:
- Знакомство с общими сведениями о [потоках вызовов](../call-flows.md)
- Узнайте о [типах вызовов](../voice-video-calling/about-call-types.md)
- [Планирование решения ТСОП](../telephony-sms/plan-solution.md)
