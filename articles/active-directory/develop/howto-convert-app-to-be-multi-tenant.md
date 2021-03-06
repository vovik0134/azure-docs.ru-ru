---
title: Создание приложений, которые входят в систему пользователей Azure AD
titleSuffix: Microsoft identity platform
description: Показано, как создать приложение с несколькими клиентами, которое может входить в систему пользователя из любого Azure Active Directory клиента.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 10/27/2020
ms.author: ryanwi
ms.reviewer: marsma, jmprieur, lenalepa, sureshja, kkrishna
ms.custom: aaddev
ms.openlocfilehash: 22d6cacc36363b17f9bc32d354982eb71974b31d
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/07/2020
ms.locfileid: "96779759"
---
# <a name="how-to-sign-in-any-azure-active-directory-user-using-the-multi-tenant-application-pattern"></a>Реализация входа любого пользователя Azure Active Directory с помощью шаблона мультитенантного приложения

Если вы предлагаете приложение по схеме "программное обеспечение как сервис" (SaaS) для многих организаций, вы можете настроить его так, чтобы оно поддерживало вход из любого клиента Azure AD (AD). Эта конфигурация называется *созданием нескольких клиентов приложения*. Пользователи из любого клиента Azure AD смогут входить в приложение после того, как согласятся использовать свою учетную запись с вашим приложением.

Если у вас есть приложение, которое имеет собственную систему учетных записей или поддерживает вход с помощью других поставщиков облачных служб, добавить вход в Azure AD с любого клиента не составит труда. Просто зарегистрируйте приложение, добавьте код входа через OAuth2, OpenID Connect Connect или SAML и вставьте в приложение [кнопку "Войдите с помощью учетной записи Майкрософт"][AAD-App-Branding] .

> [!NOTE]
> В этой статье предполагается, что вы уже знакомы с созданием приложения с одним клиентом для Azure AD. В противном случае ознакомьтесь с [одним из кратких руководств][AAD-Dev-Guide].

Преобразование приложения в приложение с несколькими клиентами Azure AD выполняется в четыре этапа:

1. [Измените тип регистрации вашего приложения на мультитенантную.](#update-registration-to-be-multi-tenant)
2. [Обновите код для отправки запросов в конечную точку "/common".](#update-your-code-to-send-requests-to-common)
3. [Обновите код для обработки нескольких значений издателя.](#update-your-code-to-handle-multiple-issuer-values)
4. [Изучите особенности получения согласия пользователя и администратора и внесите соответствующие изменения в код.](#understand-user-and-admin-consent)

Давайте рассмотрим каждый из этих шагов подробнее. Вы также можете перейти непосредственно к образцу [создать многоклиентское веб-приложение SaaS, которое вызывает Microsoft Graph с помощью Azure AD и OpenID Connect Connect](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/2-WebApp-graph-user/2-3-Multi-Tenant/README.md).

## <a name="update-registration-to-be-multi-tenant"></a>Обновление регистрации с изменением ее на мультитенантную

По умолчанию регистрация веб-приложения или API в Azure AD выполняется на одном клиенте. Вы можете сделать регистрацию нескольких клиентов, настроив для параметра **Поддерживаемые типы учетных записей** переключатель на панели **Проверка подлинности** регистрации приложения в [портал Azure][AZURE-portal] и задав для него **учетные записи в любом каталоге Организации**.

Прежде чем сделать приложение мультитенантным, убедитесь, что URI идентификатора приложения глобально уникальный согласно требованию Azure AD. URI кода приложения является одним из способов идентификации приложения в сообщениях протокола. Для приложения с одним клиентом достаточно, чтобы URI идентификатора приложения был уникальным в рамках клиента. Для мультитенантного приложения он должен быть глобально уникальным, чтобы служба Azure AD могла найти приложение по всем клиентам. Глобальная уникальность обеспечивается за счет требования, чтобы URI кода приложения имел имя узла, соответствующее проверенному домену клиента Azure AD.

По умолчанию при создании приложений с помощью портала Azure им присваивается глобальный уникальный URI идентификатор приложения, но позже это значение можно изменить. Например, если имя клиента — contoso.onmicrosoft.com, то допустимым URI кода приложения будет `https://contoso.onmicrosoft.com/myapp`. Если проверенный домен клиента — `contoso.com`, то допустимым URI кода приложения тоже будет `https://contoso.com/myapp`. Если URI идентификатора приложения не соответствует этому шаблону, вы не сможете сделать приложение мультитенантным.

## <a name="update-your-code-to-send-requests-to-common"></a>Обновление кода для отправки запросов в конечную точку "/common"

В приложении с одним клиентом запросы на вход отправляются в конечную точку входа клиента. Например для contoso.onmicrosoft.com конечная точка будет выглядеть так: `https://login.microsoftonline.com/contoso.onmicrosoft.com`. Запросы, отправленные в конечную точку клиента, могут выполнять вход пользователей (или гостей) этого клиента в приложения этого клиента.

Если используется мультитенантное приложение, то оно не знает заранее, из какого клиента тот или иной пользователь, поэтому невозможно отправить запрос в конечную точку клиента. Вместо этого запросы отправляются в конечную точку, которая мультиплексируется по всем клиентам Azure AD: `https://login.microsoftonline.com/common`

Когда платформа Microsoft Identity получает запрос на конечную точку "/Common", он подписывает пользователя в и, как следствие, обнаруживает, из какого клиента он из пользователей. Конечная точка "/Common" работает со всеми протоколами проверки подлинности, поддерживаемыми Azure AD: OpenID Connect Connect, OAuth 2,0, SAML 2,0 и WS-Federation.

Ответ приложению на вход содержит маркер, представляющий пользователя. Значение издателя в маркере сообщает приложению, из какого клиента этот пользователь. Когда из конечной точки "/common" возвращается ответ, значение издателя в маркере соответствует клиенту пользователя.

> [!IMPORTANT]
> Конечная точка /common — это просто мультиплексор, а не клиент или издатель. Учитывая этот факт, при использовании "/common" логику проверки маркеров в приложении необходимо обновить.

## <a name="update-your-code-to-handle-multiple-issuer-values"></a>Обновите код для обработки нескольких значений издателя.

Веб-приложения и веб-API получают и проверяют маркеры на платформе Microsoft Identity.

> [!NOTE]
> Хотя собственные клиентские приложения запрашивают и получают маркеры от платформы Microsoft Identity, они пересылают их в интерфейсы API, где они проверяются. Собственные приложения не проверяют маркеры доступа и должны обрабатывать их как непрозрачные.

Рассмотрим, как приложение проверяет маркеры, получаемые от платформы Microsoft Identity. Приложение с одним клиентом обычно принимает значение конечной точки, например:

```http
https://login.microsoftonline.com/contoso.onmicrosoft.com
```

... и использует его для создания URL-адреса метаданных (в данном случае OpenID Connect Connect) следующим образом:

```http
https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration
```

С его помощью скачиваются два критически важных фрагмента информации, используемые для проверки маркеров: значение издателя и ключи подписи клиента.

Каждый клиент Azure AD имеет уникальное значение издателя, которое можно представить в такой форме:

```http
https://sts.windows.net/31537af4-6d77-4bb9-a681-d2394888ea26/
```

... где значение GUID — это переименованная версия идентификатора клиента клиента. Если щелкнуть указанную выше ссылку на метаданные для `contoso.onmicrosoft.com`, вы увидите это значение издателя в документе.

Когда приложение с одним клиентом проверяет маркер, оно проверяет подпись маркера на соответствие ключам подписывания из документа метаданных. Эта проверка позволяет убедиться, что значение издателя в маркере соответствует значению в документе метаданных.

Так как конечная точка /common не соответствует клиенту и не является издателем, при проверке значения издателя в метаданных для точки /common вместо фактического значения отображается шаблон URL-адреса:

```http
https://sts.windows.net/{tenantid}/
```

Таким образом, мультитенантное приложение не может проверить маркеры, просто сопоставив значение издателя в метаданных со значением `issuer` в маркере. Мультитенантному приложению требуется логика, чтобы решить, какие значения издателя являются допустимыми, а какие не являются, исходя из части значения издателя, в которой указан идентификатор клиента.

Например, если мультитенантное приложение разрешает вход только из определенных клиентов, которые зарегистрировались в службе, то оно должно проверить значение издателя или значение утверждения `tid` в токене, чтобы убедиться, что этот клиент внесен в его список подписчиков. Если мультитенантное приложение работает только с отдельными пользователями и не принимает решений о доступе на основе данных клиентов, то оно может полностью игнорировать значение издателя.

В [примерах мультитенантных приложений][AAD-Samples-MT] проверка издателя отключена, чтобы можно было включить возможность входа для любого клиента Azure AD.

## <a name="understand-user-and-admin-consent"></a>Получение согласия пользователя и администратора

Чтобы пользователь мог войти в приложение в Azure AD, это приложение должно быть представлено в клиенте пользователя. Это дает возможность организации применять уникальные политики входа в приложение пользователей из клиента. Для приложения с одним клиентом эта регистрация упрощается; Это то, что происходит при регистрации приложения в [портал Azure][AZURE-portal].

Для мультитенантного приложения его начальная регистрация находится в клиенте Azure AD, используемом разработчиком. При первом входе в приложение пользователя из другого клиента Azure AD предлагает принять разрешения, запрошенные приложением. Если пользователь соглашается, то в его клиенте создается представление приложения, называемое *субъектом-службой*, после чего можно продолжить вход. В каталоге также создается делегирование, которое записывает согласие пользователя в приложение. Дополнительные сведения о приложениях и объектах ServicePrincipal, а также о том, как они связаны друг с другом, см. в разделе [объекты приложения и объекты субъекта-службы][AAD-App-SP-Objects].

![Показывает согласие на одноуровневое приложение][Consent-Single-Tier]

Для получения согласия используются разрешения, запрашиваемые приложением. Платформа Microsoft Identity поддерживает два вида разрешений: только для приложений и делегированные.

* Делегированное разрешение предоставляет приложению возможность выступать в качестве выполнившего вход пользователя и выполнять ряд действий, доступных пользователю. Например, можно предоставить приложению делегированное разрешение на чтение календаря выполнившего вход пользователя.
* Разрешение "только для приложения" предоставляется непосредственно удостоверению приложения. Например, если предоставить приложению разрешение "только для приложения" на чтение списка пользователей в клиенте, независимо от того, кто выполнил вход в приложение.

Некоторые разрешения могут быть приняты обычным пользователем, а для других требуется согласие администратора клиента.

Дополнительные сведения о согласии пользователей и администраторов см. в разделе [Настройка рабочего процесса согласия администратора](../manage-apps/configure-admin-consent-workflow.md).

### <a name="admin-consent"></a>Согласие администратора

Для разрешений "только для приложения" всегда требуется согласие администратора клиента. Если приложение запрашивает разрешение "только для приложения" и пользователь пытается войти в приложение, появится сообщение об ошибке. Это сообщение говорит о том, что пользователь не может принять это разрешение.

Для некоторых делегированных разрешений также требуется согласие администратора клиента. Например, оно требуется для возможности обратной записи в Azure AD в качестве выполнившего вход пользователя. Как и в случае с разрешениями "только для приложения", если обычный пользователь пытается войти в приложение, запрашивающее делегированное разрешение, для которого требуется согласие администратора, в приложении появится сообщение об ошибке. Требует ли разрешение согласия администратора, определяет разработчик, опубликовавший ресурс. Эти сведения можно найти в документации по данному ресурсу. В документации по разрешениям [Microsoft Graph API][MSFT-Graph-permission-scopes] указываются разрешения, требующие согласия администратора.

Если приложение использует разрешения, требующие согласия администратора, то в приложении должен быть элемент, такой как кнопка или ссылка, с помощью которого администратор может инициировать действие. Запрос, отправляемый приложением для этого действия, является обычным запросом авторизации OAuth2 или OpenID Connect, но он также включает в себя параметр строки запроса `prompt=admin_consent`. После того как администратор предоставит свое согласие, а в клиенте пользователя будет создан субъект-служба, в последующих запросах входа не нужно будет указывать параметр `prompt=admin_consent`. После того как администратор решил, что запрошенные разрешения являются приемлемыми, у других пользователей клиента согласие запрашиваться не будет.

Администратор клиента может отключить возможность обычным пользователям предоставлять согласие для приложений. Если эта возможность отключена, то для использования приложения в клиенте всегда требуется согласие администратора. Если вы хотите протестировать приложение с отключенным согласием конечного пользователя, можно найти параметр конфигурации в [портал Azure][AZURE-portal] в разделе **[Параметры пользователя](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/)** в разделе **корпоративные приложения**.

Параметр `prompt=admin_consent` может использоваться приложениями, запрашивающими разрешения, которые не требуют согласия администратора. Это делается, например, в тех случаях, когда приложению требуется возможность одноразовой регистрации администратора клиента, после которой у других пользователей согласие не запрашивается.

Если приложению требуется разрешение администратора и администратор выполняет вход, но параметр `prompt=admin_consent` не передается, администратор предоставит приложению согласие **только для своей учетной записи пользователя**. Обычные пользователи по-прежнему не смогут выполнять вход и давать свое согласие приложению. Это удобно в тех случаях, когда администратору клиента нужно просмотреть приложение, прежде чем предоставлять доступ к приложению другим пользователям.

### <a name="consent-and-multi-tier-applications"></a>Согласие и многоуровневые приложения

Приложение может иметь несколько уровней, каждый из которых представлен в Azure AD собственной регистрацией. Например, собственное приложение, выполняющее вызовы веб-API, или веб-приложение, выполняющее вызовы веб-API. В обоих случаях клиент (собственное приложение или веб-приложение) запрашивает разрешения на вызов ресурса (веб-API). Чтобы клиент мог получить согласие от клиента пользователя, все ресурсы, к которым он запрашивает разрешения, должны существовать в клиенте пользователя. Если это условие не выполняется, Azure AD выдаст ошибку и сообщит, что сначала необходимо добавить ресурс.

#### <a name="multiple-tiers-in-a-single-tenant"></a>Несколько уровней в одном клиенте

Это может быть проблемой, если ваше логические приложение состоит из двух или более регистраций приложения, например, отдельно для клиента и ресурса. Как сначала поместить ресурс в клиент пользователя? На этот случай в Azure AD предусмотрена возможность получения согласия для клиента и ресурса за один шаг. Пользователь видит общее число разрешений, запрошенных клиентом и ресурсом на странице получения согласия. Чтобы активировать такое поведение, регистрация приложения ресурса должна включать в себя идентификатор приложения клиента как `knownClientApplications` в [манифесте соответствующего приложения][AAD-App-Manifest]. Пример.

```json
"knownClientApplications": ["94da0930-763f-45c7-8d26-04d5938baab2"]
```

Это демонстрируется в примере многоуровневого собственного клиента, выполняющего вызов веб-API, в разделе [Связанная информация](#related-content) в конце этой статьи. На следующей схеме представлен обзор согласия для многоуровневого приложения, зарегистрированного в одном клиенте:

![Показывает согласие на многоуровневое известное клиентское приложение][Consent-Multi-Tier-Known-Client]

#### <a name="multiple-tiers-in-multiple-tenants"></a>Несколько уровней в нескольких клиентах

Аналогичная ситуация складывается, когда различные уровни приложения регистрируются в разных клиентах. Например, рассмотрим случай создания собственного клиентского приложения, вызывающего API Exchange Online. Для разработки собственного приложения, а затем и для его запуска в клиенте пользователя необходимо наличие субъекта-службы Exchange Online. В этом случае разработчик и пользователь должен приобрести Exchange Online, чтобы в их клиентах был создан субъект-служба.

Если это API, созданный Организацией, отличной от корпорации Майкрософт, разработчику API необходимо предоставить своим клиентам способ согласия приложения с клиентами. Рекомендуемым вариантом разработки является то, что разработчик стороннего разработчика создает API таким образом, что он также может работать в качестве веб-клиента для реализации регистрации. Выполните указанные ниже действия.

1. Выполните действия из предыдущих разделов, чтобы убедиться, что API реализует требования к регистрации и коду мультитенантного приложения.
2. Помимо предоставления областей и ролей API, убедитесь, что регистрация включает разрешение "вход и чтение профиля пользователя" (предоставляется по умолчанию).
3. Создайте страницу входа и регистрации в веб-клиенте, следуя инструкциям из руководства по [предоставлению согласия администратора](#admin-consent).
4. После того как пользователь предоставит свое согласие в приложении, в клиенте создаются субъект-служба и ссылки на делегирование согласия, а собственное приложение может получать маркеры для API.

На следующей схеме представлен обзор согласия для многоуровневого приложения, зарегистрированного в разных клиентах:

![Показывает согласие на многоуровневое приложение с несколькими участниками][Consent-Multi-Tier-Multi-Party]

### <a name="revoking-consent"></a>Отзыв согласия

Пользователи и администраторы в любой момент могут отозвать свое согласие, предоставленное приложению.

* Чтобы отозвать доступ к отдельным приложениям, пользователям необходимо удалить их из списка [Приложения панели доступа][AAD-Access-Panel].
* Администраторы отменяют доступ к приложениям, удаляя их с помощью раздела " [корпоративные приложения](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps) " [портал Azure][AZURE-portal].

Если администратор дает свое согласие на доступ к приложению для всех пользователей в клиенте, то пользователи не могут отзывать доступ индивидуально. В этом случае отозвать доступ может только администратор и только для всего приложения.

## <a name="multi-tenant-applications-and-caching-access-tokens"></a>Мультитенантные приложения и кэширование маркеров доступа

Мультитенантные приложения также могут получать маркеры доступа для вызова API, защищенных службой Azure AD. Распространенной ошибкой при использовании библиотеки проверки подлинности Майкрософт (MSAL) с приложением с несколькими клиентами является первоначальный запрос маркера для пользователя с помощью "/Common", получение ответа, а затем запрос следующего маркера для того же пользователя с помощью/Коммон. Так как ответ от Azure AD поступает от клиента, а не "/Common", MSAL кэширует маркер от клиента. Последующий вызов точки "/common" для получения маркера доступа для пользователя не записывается в кэш (происходит промах кэша), и пользователю предлагается выполнить вход повторно. Во избежание промахов кэша убедитесь, что последующие вызовы для выполнившего вход пользователя осуществляются в конечную точку клиента.

## <a name="related-content"></a>См. также

* [Пример приложения с несколькими клиентами](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/2-WebApp-graph-user/2-3-Multi-Tenant/README.md)
* [Рекомендации по фирменной символике для приложений][AAD-App-Branding]
* [Объекты приложения и объекты субъекта-службы][AAD-App-SP-Objects]
* [Интеграция приложений с Azure Active Directory][AAD-Integrating-Apps]
* [Обзор платформы согласия][AAD-Consent-Overview]
* [Справочник по разрешениям Microsoft Graph][MSFT-Graph-permission-scopes]

## <a name="next-steps"></a>Дальнейшие действия

В этой статье было показано, как создать приложение, поддерживающее вход пользователя с любого клиента Azure AD. После включения единого Sign-On (SSO) между приложением и Azure AD можно также обновить приложение для доступа к API, предоставляемым ресурсами Майкрософт, такими как Microsoft 365. Это позволит вам предлагать своим пользователям персонализированные возможности при работе с приложением, например отображать контекстную информацию — такую, как изображение профиля или следующая встреча из календаря.

Дополнительные сведения о вызовах API в Azure AD и Microsoft 365 службах, таких как Exchange, SharePoint, OneDrive, OneNote и др., см. [Microsoft Graph API][MSFT-Graph-overview].

<!--Reference style links IN USE -->
[AAD-Access-Panel]:  https://myapps.microsoft.com
[AAD-App-Branding]:howto-add-branding-in-azure-ad-apps.md
[AAD-App-Manifest]:reference-azure-ad-app-manifest.md
[AAD-App-SP-Objects]:app-objects-and-service-principals.md
[AAD-Auth-Scenarios]:authentication-scenarios.md
[AAD-Consent-Overview]:consent-framework.md
[AAD-Dev-Guide]:azure-ad-developers-guide.md
[AAD-Integrating-Apps]:quickstart-v1-integrate-apps-with-azure-ad.md
[AAD-Samples-MT]: /samples/browse/?products=azure-active-directory
[AAD-Why-To-Integrate]: ./active-directory-how-to-integrate.md
[AZURE-portal]: https://portal.azure.com
[MSFT-Graph-overview]: /graph/
[MSFT-Graph-permission-scopes]: /graph/permissions-reference

<!--Image references-->
[AAD-Sign-In]: ./media/active-directory-devhowto-multi-tenant-overview/sign-in-with-microsoft-light.png
[Consent-Single-Tier]: ./media/howto-convert-app-to-be-multi-tenant/consent-flow-single-tier.svg
[Consent-Multi-Tier-Known-Client]: ./media/howto-convert-app-to-be-multi-tenant/consent-flow-multi-tier-known-clients.svg
[Consent-Multi-Tier-Multi-Party]: ./media/howto-convert-app-to-be-multi-tenant/consent-flow-multi-tier-multi-party.svg

<!--Reference style links -->
[AAD-App-Manifest]:reference-azure-ad-app-manifest.md
[AAD-App-SP-Objects]:app-objects-and-service-principals.md
[AAD-Auth-Scenarios]:authentication-scenarios.md
[AAD-Integrating-Apps]:quickstart-v1-integrate-apps-with-azure-ad.md
[AAD-Dev-Guide]:azure-ad-developers-guide.md
[AAD-How-To-Integrate]: ./active-directory-how-to-integrate.md
[AAD-Security-Token-Claims]: ./active-directory-authentication-scenarios/#claims-in-azure-ad-security-tokens
[AAD-Tokens-Claims]:access-tokens.md
[AAD-V2-Dev-Guide]: v2-overview.md
[AZURE-portal]: https://portal.azure.com
[Duyshant-Role-Blog]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/
[JWT]: https://tools.ietf.org/html/draft-ietf-oauth-json-web-token-32
[O365-Perm-Ref]: /graph/permissions-reference
[OAuth2-Access-Token-Scopes]: https://tools.ietf.org/html/rfc6749#section-3.3
[OAuth2-AuthZ-Grant-Types]: https://tools.ietf.org/html/rfc6749#section-1.3
[OAuth2-Client-Types]: https://tools.ietf.org/html/rfc6749#section-2.1
[OAuth2-Role-Def]: https://tools.ietf.org/html/rfc6749#page-6
[OpenIDConnect]: https://openid.net/specs/openid-connect-core-1_0.html
[OpenIDConnect-ID-Token]: https://openid.net/specs/openid-connect-core-1_0.html#IDToken