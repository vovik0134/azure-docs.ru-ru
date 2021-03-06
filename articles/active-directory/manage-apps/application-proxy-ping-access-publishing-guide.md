---
title: Аутентификация на основе заголовка с помощью PingAccess для Azure AD Application Proxy
description: Публикация приложений с использованием PingAccess и прокси приложения для реализации аутентификации на основе заголовка.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 10/24/2019
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: e09bb0b07112a962b709c380c48f2a656c16097b
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/17/2020
ms.locfileid: "94663726"
---
# <a name="header-based-authentication-for-single-sign-on-with-application-proxy-and-pingaccess"></a>Аутентификация на основе заголовка для единого входа с использованием прокси приложения и PingAccess

Azure Active Directory прокси приложения (Azure AD) взаимоотношений с PingAccess, чтобы клиенты Azure AD могли получить доступ к большему числу ваших приложений. PingAccess предоставляет еще один вариант помимо встроенного [единого входа на основе заголовков](application-proxy-configure-single-sign-on-with-headers.md).

## <a name="whats-pingaccess-for-azure-ad"></a>Что такое PingAccess для Azure AD?

С помощью PingAccess для Azure AD вы можете предоставить пользователям доступ и единый вход (SSO) для приложений, использующих заголовки для проверки подлинности. Прокси приложения обрабатывает такие приложения, как и любые другие, используя Azure AD для аутентификации доступа и передавая трафик через службу соединителя. PingAccess располагается перед приложениями и преобразует маркер доступа из Azure AD в заголовок. Затем приложение получает проверку подлинности в формате, который он может читать.

Получая доступ к корпоративным приложениям, пользователи не заметят никаких отличий в процедуре входа. Они по-прежнему смогут работать в любом расположении и на любом устройстве. Соединители прокси приложения направляют удаленный трафик всем приложениям без учета их типа проверки подлинности, поэтому они по-прежнему будут автоматически распределять нагрузку.

## <a name="how-do-i-get-access"></a>Как мне получить доступ?

Так как этот сценарий взят из партнерства между Azure Active Directory и PingAccess, вам понадобятся лицензии для обеих служб. Тем не менее подписки Azure Active Directory Premium включает в себя базовую лицензию PingAccess, которая поддерживает до 20 приложений. Если необходимо опубликовать более 20 приложений, использующих заголовки, вы можете приобрести дополнительную лицензию PingAccess.

Дополнительные сведения см. в разделе [Выпуски Azure Active Directory](../fundamentals/active-directory-whatis.md).

## <a name="publish-your-application-in-azure"></a>Публикация приложения в Azure

Эта статья предназначена для пользователей, которые публикуют приложение с этим сценарием в первый раз. Помимо подробной детализации этапов публикации, он помогает в начале работы с прокси приложения и PingAccess. Если вы уже настроили обе службы, но хотите обновить их на этапе публикации, перейдите к разделу [Добавление приложения в Azure AD с помощью прокси приложения](#add-your-application-to-azure-ad-with-application-proxy) .

> [!NOTE]
> Так как этот сценарий предполагает совместное использование Azure AD и PingAccess, некоторые инструкции доступны на сайте Ping Identity.

### <a name="install-an-application-proxy-connector"></a>Установка соединителя прокси приложения

Если вы включили поддержку прокси приложения и уже установили соединитель, можно пропустить этот раздел и перейти к [добавлению приложения в Azure AD с помощью прокси приложения](#add-your-application-to-azure-ad-with-application-proxy).

Соединитель прокси приложения — это служба Windows Server, которая направляет трафик от удаленных сотрудников к опубликованным приложениям. Подробные инструкции по установке см. [в разделе Учебник. Добавление локального приложения для удаленного доступа через прокси приложения в Azure Active Directory](application-proxy-add-on-premises-application.md).

1. Войдите на [портал Azure Active Directory](https://aad.portal.azure.com/) в качестве администратора приложения. Откроется страница **центра администрирования Azure Active Directory** .
1. Выберите **Azure Active Directory**  >  **прокси-сервер приложений**  >  **скачать службу соединителя**. Откроется страница **скачивания соединителя прокси приложения** .

   ![Скачивание соединителя прокси приложения](./media/application-proxy-configure-single-sign-on-with-ping-access/application-proxy-connector-download.png)

1. Следуйте инструкциям по установке.

При скачивании соединителя необходимо автоматически включить прокси приложения для каталога, но если нет, можно выбрать параметр **включить прокси приложения**.

### <a name="add-your-application-to-azure-ad-with-application-proxy"></a>Добавление приложения в Azure AD с помощью прокси приложения

Существуют два действия, которые необходимо выполнить на портале Azure. Во-первых, необходимо опубликовать приложение с помощью прокси приложения. Затем необходимо получить информацию о приложении, которое можно использовать во время действий PingAccess.

#### <a name="publish-your-application"></a>Публикация приложения

Сначала необходимо опубликовать приложение. Это действие включает в себя следующее:

- Добавление локального приложения в Azure AD
- Назначение пользователя для тестирования приложения и выбор единого входа на основе заголовков
- Настройка URL-адреса перенаправления приложения
- Предоставление пользователям и другим приложениям разрешений на использование локального приложения

Чтобы опубликовать локальное приложение, выполните следующие действия.

1. Если вы не вошли в последний раздел, войдите на [портал Azure Active Directory](https://aad.portal.azure.com/) в качестве администратора приложения.
1. Выберите **корпоративные приложения**  >  **новое приложение**  >  **Добавить локальное приложение**. Откроется страница **Добавить собственное локальное приложение** .

   ![Добавление собственного локального приложения](./media/application-proxy-configure-single-sign-on-with-ping-access/add-your-own-on-premises-application.png)
1. Заполните обязательные поля сведениями о новом приложении. Используйте приведенные ниже инструкции для настройки параметров.

   > [!NOTE]
   > Более подробное пошаговое руководство см. в статье [Добавление локального приложения в Azure AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad).

   1. **Внутренний URL-адрес**. обычно указывается URL-адрес страницы входа приложения, когда вы находитесь в корпоративной сети. В этом сценарии соединитель должен рассматривать прокси-сервер PingAccess как переднюю страницу приложения. Используйте следующий формат: `https://<host name of your PingAccess server>:<port>`. Порт 3000 используется по умолчанию, но его можно настроить в PingAccess.

      > [!WARNING]
      > Для этого типа единого входа внутренний URL-адрес должен использовать `https` и не может использовать `http` . Кроме того, существует ограничение при настройке приложения, которое не должно иметь двух приложений с таким же внутренним URL-адресом, что позволяет прокси приложения поддерживать различие между приложениями.

   1. **Метод предварительной проверки подлинности**: выберите **Azure Active Directory**.
   1. **Перевод URL-адреса в заголовках**: выберите **нет**.

   > [!NOTE]
   > Если это ваше первое приложение, для начала используйте порт 3000, а затем измените этот параметр, если измените конфигурацию доступа PingAccess. Для последующих приложений порт должен соответствовать прослушивателю, настроенному в PingAccess. Узнайте больше о [прослушивателях в PingAccess](https://support.pingidentity.com/s/document-item?bundleId=pingaccess-52&topicId=reference/ui/pa_c_Listeners.html).

1. Нажмите кнопку **Добавить**. Откроется страница обзора для нового приложения.

Теперь назначьте пользователя для тестирования приложений и выберите единый вход на основе заголовка:

1. На боковой панели приложения выберите **Пользователи и группы**  >  **добавить** пользователей  >  **и группы ( \<Number> Выбранные)**. Отобразится список пользователей и групп.

   ![Отображение списка пользователей и групп](./media/application-proxy-configure-single-sign-on-with-ping-access/users-and-groups.png)

1. Выберите пользователя для тестирования приложений и нажмите **кнопку Выбрать**. Убедитесь, что у тестовой учетной записи есть доступ к локальному приложению.
1. Выберите **Назначить**.
1. На боковой панели приложения выберите **заголовок единый вход**  >  **Header-based**.

   > [!TIP]
   > Если вы впервые используете единый вход на основе заголовка, необходимо установить PingAccess. Чтобы обеспечить автоматическую привязку подписки Azure к установленной службе PingAccess, используйте ссылку на этой странице единого входа для скачивания PingAccess. Вы можете прямо сейчас перейти на сайт скачивания или вернуться на эту страницу позже.

   ![Отображает экран входа на основе заголовка и PingAccess](./media/application-proxy-configure-single-sign-on-with-ping-access/sso-header.png)

1. Нажмите кнопку **Сохранить**.

Затем убедитесь, что для URL-адреса перенаправления задан внешний URL-адрес:

1. На боковой панели **центра администрирования Azure Active Directory** выберите **Azure Active Directory**  >  **Регистрация приложений**. Откроется список приложений.
1. Выберите приложение.
1. Щелкните ссылку рядом с **URI перенаправления**, в которых отображается количество URI перенаправления, настроенных для веб-клиентов и общедоступных. Откроется страница **\<application name> -Проверка подлинности** .
1. Убедитесь, что внешний URL-адрес, назначенный приложению ранее, находится в списке **URI перенаправления** . Если это не так, добавьте внешний URL-адрес, используя тип URI перенаправления в **Интернете**, и нажмите кнопку **сохранить**.

В дополнение к внешнему URL-адресу в список URI перенаправления должна быть добавлена конечная точка авторизации Azure Active Directory во внешнем URL-адресе.

`https://*.msappproxy.net/pa/oidc/cb`
`https://*.msappproxy.net/`

Наконец, настройте локальное приложение, чтобы пользователи имели доступ на чтение и другие приложения имели доступ на чтение и запись:

1. На боковой панели **Регистрация приложений** приложения выберите **разрешения API**  >  **Добавить разрешение**  >  **Microsoft API**  >  **Microsoft Graph**. Откроется страница **разрешений API запроса** для **Microsoft Graph** , которая содержит интерфейсы API для Windows Azure Active Directory.

   ![Отображение страницы разрешений API запросов](./media/application-proxy-configure-single-sign-on-with-ping-access/required-permissions.png)

1. Выберите **делегированные разрешения**  >  **пользователь**  >  **пользователь. чтение**.
1. Выберите **разрешения приложения**  >  **приложение приложения**  >  **. ReadWrite. ALL**.
1. Выберите **Добавить разрешения**.
1. На странице **разрешения API** выберите **предоставить согласие администратора для \<your directory name>**.

#### <a name="collect-information-for-the-pingaccess-steps"></a>Сбор сведений об этапах PingAccess

Чтобы настроить приложение с помощью PingAccess, необходимо состоять из этих трех данных (всех идентификаторов GUID):

| Имя поля Azure AD | Имя поля PingAccess | Формат данных |
| --- | --- | --- |
| **Идентификатор приложения (клиента)** | **Идентификатор клиента** | Код GUID |
| **Идентификатор каталога (клиента)** | **Издатель** | Код GUID |
| `PingAccess key` | **Секрет клиента** | Произвольная строка |

Чтобы получить эти сведения, сделайте следующее:

1. На боковой панели **центра администрирования Azure Active Directory** выберите **Azure Active Directory**  >  **Регистрация приложений**. Откроется список приложений.
1. Выберите приложение. Откроется страница **Регистрация приложений** приложения.

   ![Обзор регистрации для приложения](./media/application-proxy-configure-single-sign-on-with-ping-access/registration-overview-for-an-application.png)

1. Рядом со значением **идентификатор приложения (клиента)** выберите значок **Копировать в буфер обмена** , затем скопируйте и сохраните его. Это значение указывается позже, как идентификатор клиента PingAccess.
1. После значения **идентификатора каталога (клиента)** также выберите **Копировать в буфер обмена**, затем скопируйте и сохраните его. Это значение указывается позже как издатель PingAccess.
1. На боковой панели **Регистрация приложений** приложения выберите **Сертификаты и секреты**  >  **новый секрет клиента**. Откроется страница **Добавление секрета клиента** .

   ![Отображение страницы "Добавление секрета клиента"](./media/application-proxy-configure-single-sign-on-with-ping-access/add-a-client-secret.png)

1. В окне **Описание** введите `PingAccess key` .
1. В разделе **срок действия** выберите, как задать ключ PingAccess: **через 1 год**, **в 2 года** или **никогда**.
1. Нажмите кнопку **Добавить**. Ключ PingAccess появляется в таблице секретов клиента со случайной строкой, заполняемой в поле **значение** .
1. Рядом с полем **значение** ключа PingAccess выберите значок **Копировать в буфер обмена** , затем скопируйте и сохраните его. Это значение указывается позже в качестве секрета клиента PingAccess.

**Обновите `acceptMappedClaims` поле:**

1. Войдите на [портал Azure Active Directory](https://aad.portal.azure.com/) в качестве администратора приложения.
1. Выберите **Azure Active Directory** > **Регистрация приложений**. Откроется список приложений.
1. Выберите приложение.
1. На боковой панели страницы **Регистрация приложений** приложения выберите **Манифест**. Появится код JSON манифеста для регистрации приложения.
1. Найдите `acceptMappedClaims` поле и измените его значение на `True` .
1. Нажмите кнопку **Сохранить**.

### <a name="use-of-optional-claims-optional"></a>Использование необязательных утверждений (необязательно)

Необязательные утверждения позволяют добавлять стандартные, но не включаемые утверждения по умолчанию, которые имеют все пользователи и клиенты. Можно настроить необязательные утверждения для приложения, изменив манифест приложения. Дополнительные сведения см. в [статье Общие сведения о манифесте приложения Azure AD](../develop/reference-app-manifest.md) .

Пример включения адреса электронной почты в access_token, который будет использовать PingAccess:
```
    "optionalClaims": {
        "idToken": [],
        "accessToken": [
            {
                "name": "email",
                "source": null,
                "essential": false,
                "additionalProperties": []
            }
        ],
        "saml2Token": []
    },
```

### <a name="use-of-claims-mapping-policy-optional"></a>Использование политики сопоставления утверждений (необязательно)

[Политика сопоставления утверждений (Предварительная версия)](../develop/active-directory-claims-mapping.md#claims-mapping-policy-properties) для атрибутов, которые не существуют в AzureAD. Сопоставление утверждений позволяет перенести старые локальные приложения в облако, добавив дополнительные настраиваемые утверждения, которые поддерживаются объектами ADFS или User.

Чтобы приложение использовало пользовательское утверждение и включало дополнительные поля, убедитесь, что вы также [создали пользовательскую политику сопоставления утверждений и назначили ее приложению](../develop/active-directory-claims-mapping.md#claims-mapping-policy-assignment).

> [!NOTE]
> Чтобы использовать настраиваемое утверждение, также необходимо определить настраиваемую политику и назначить ее приложению. Эта политика должна включать все обязательные настраиваемые атрибуты.
>
> Определение политики и назначение можно выполнять с помощью PowerShell или Microsoft Graph. Если вы делаете это в PowerShell, то, возможно, потребуется сначала использовать `New-AzureADPolicy` и назначить его приложению с помощью `Add-AzureADServicePrincipalPolicy` . Дополнительные сведения см. в разделе [Назначение политики сопоставления утверждений](../develop/active-directory-claims-mapping.md#claims-mapping-policy-assignment).

Пример
```powershell
$pol = New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema": [{"Source":"user","ID":"employeeid","JwtClaimType":"employeeid"}]}}') -DisplayName "AdditionalClaims" -Type "ClaimsMappingPolicy"

Add-AzureADServicePrincipalPolicy -Id "<<The object Id of the Enterprise Application you published in the previous step, which requires this claim>>" -RefObjectId $pol.Id
```

### <a name="enable-pingaccess-to-use-custom-claims"></a>Включение PingAccess для использования настраиваемых утверждений

Включение PingAccess для использования настраиваемых утверждений является необязательным, но требуется, если предполагается, что приложение будет использовать дополнительные утверждения.

Когда вы настроите PingAccess на следующем шаге, веб-сеанс, который вы создадите (Settings->Access->веб-сеансы), должен иметь значение отменить выбор **профиля запроса** и **обновить атрибуты пользователя** на **нет** .

## <a name="download-pingaccess-and-configure-your-application"></a>Скачивание PingAccess и настройка приложения

Теперь, когда вы выполнили все этапы установки Azure Active Directory, можно перейти к настройке PingAccess.

Подробные инструкции для PingAccess части этого сценария см. в документации по удостоверениям проверки связи. Следуйте инструкциям в статье [Настройка PingAccess для Azure AD, чтобы защитить приложения, опубликованные с помощью Microsoft Azure AD прокси приложения](https://support.pingidentity.com/s/document-item?bundleId=pingaccess-52&topicId=agents/azure/pa_c_PAAzureSolutionOverview.html) на веб-сайте удостоверений проверки связи, и скачайте [последнюю версию PingAccess](https://www.pingidentity.com/en/lp/azure-download.html?).

Эти действия помогут вам установить PingAccess и настроить учетную запись PingAccess (если она еще не установлена). Затем, чтобы создать подключение Azure AD OpenID Connect Connect (OIDC), необходимо настроить поставщик маркеров с помощью значения **идентификатора каталога (клиента)** , скопированного на портале Azure AD. Далее, чтобы создать веб-сеанс на PingAccess, используйте идентификатор и значения **приложения (клиента)** `PingAccess key` . После этого вы сможете настроить сопоставление удостоверений и создать виртуальный узел, сайт и приложение.

### <a name="test-your-application"></a>Тестирование приложения

После выполнения всех этих действий приложение должно быть работает. Чтобы протестировать его, откройте браузер и перейдите по внешнему URL-адресу, созданному при публикации приложения в Azure. Войдите с помощью тестовой учетной записи, назначенной приложению.

## <a name="next-steps"></a>Дальнейшие действия

- [Настройка PingAccess для Azure AD для защиты приложений, опубликованных с помощью Microsoft Azure AD прокси приложения](https://docs.pingidentity.com/bundle/pingaccess-60/page/jep1564006742933.html)
- [Единый вход в приложениях в Azure Active Directory](what-is-single-sign-on.md)
- [Устранение неполадок и сообщения об ошибках прокси приложения](application-proxy-troubleshoot.md)