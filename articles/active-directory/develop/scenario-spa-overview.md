---
title: Сценарий приложения с одной страницей JavaScript — платформа Microsoft Identity | Службы
description: Узнайте, как создать одностраничное приложение (обзор сценария) с помощью платформы Microsoft Identity.
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: nacanuma
ms.custom: aaddev, identityplatformtop40, devx-track-js
ms.openlocfilehash: 17acb2bc5e96a136f31371c0be912c2c758c0f76
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/10/2020
ms.locfileid: "94443863"
---
# <a name="scenario-single-page-application"></a>Scenario: Одностраничное приложение

Узнайте все, что нужно для создания одностраничного приложения (SPA).

## <a name="getting-started"></a>Начало работы

Если вы еще этого не сделали, создайте свое первое приложение, выполнив инструкции из руководства по JavaScript SPA:

[Краткое руководство. одностраничное приложение](./quickstart-v2-javascript.md)

## <a name="overview"></a>Обзор

Многие современные веб-приложения создаются как одностраничные приложения на стороне клиента. Разработчики пишут их с помощью JavaScript или платформы одностраничных приложений, например, Angular, Vue и React. Эти приложения работают в веб-браузере и имеют характеристики проверки подлинности, отличные от традиционных серверных веб-приложений.

Платформа Microsoft Identity предоставляет **два** варианта для включения одностраничных приложений для входа пользователей и получения маркеров для доступа к серверным службам или веб-API:

- [Поток кода авторизации OAuth 2.0 (с PKCE)](./v2-oauth2-auth-code-flow.md). Поток кода авторизации позволяет приложению обмениваться кодом авторизации для маркеров **ID** для представления пользователя, прошедшего проверку подлинности, и маркеров **доступа** , необходимых для вызова защищенных интерфейсов API. Кроме того, он возвращает маркеры **обновления** , которые предоставляют долгосрочный доступ к ресурсам от имени пользователей без необходимости взаимодействия с этими пользователями. **Рекомендуем** использовать этот подход.

![Авторизация в одностраничных приложениях](./media/scenarios/spa-app-auth.svg)

- [Неявный поток OAuth 2.0](./v2-oauth2-implicit-grant-flow.md). Поток неявного предоставления позволяет приложению получать маркеры **ID** и **Доступа**. В отличие от потока кода авторизации, поток неявного предоставления не возвращает маркер **обновления**.

![Одностраничные приложения. Неявное предоставление разрешений](./media/scenarios/spa-app.svg)

Этот поток проверки подлинности не содержит сценарии приложений, в которых используются межплатформенные среды JavaScript, такие как Electron и React-Native. Для взаимодействия с собственными платформами требуются дополнительные возможности.

## <a name="specifics"></a>Особенности

Чтобы включить этот сценарий для приложения, вам потребуется:

* Регистрация приложения с помощью Azure Active Directory (Azure AD). Процедура регистрации различается в потоке неявного предоставления и потока кода авторизации.
* Конфигурация приложения с зарегистрированными свойствами приложения, например идентификатор приложения.
* Использование библиотеки проверки подлинности Майкрософт для JavaScript (MSAL.js) для выполнения входа и получения маркеров в потоке проверки подлинности.

## <a name="recommended-reading"></a>Рекомендуемые материалы

[!INCLUDE [recommended-topics](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="next-steps"></a>Дальнейшие действия

Перейдите к следующей статье в этом сценарии — [Регистрация приложения](scenario-spa-app-registration.md).
