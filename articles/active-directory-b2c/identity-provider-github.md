---
title: Настройка регистрации и входа с помощью учетной записи GitHub
titleSuffix: Azure AD B2C
description: Вы можете организовать в приложениях регистрацию и вход для клиентов с учетными записями GitHub, используя Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/07/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 68ffde11059de4809e519c1ac4f79503f25b0004
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/17/2020
ms.locfileid: "97653747"
---
# <a name="set-up-sign-up-and-sign-in-with-a-github-account-using-azure-active-directory-b2c"></a>Настройка регистрации и входа с учетной записью GitHub через Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-a-github-oauth-application"></a>Создание приложения OAuth GitHub

Чтобы использовать учетную запись GitHub в качестве [поставщика удостоверений](authorization-code-flow.md) в Azure Active Directory B2C (Azure AD B2C), необходимо создать в своем клиенте приложение, которое его представляет. Если у вас еще нет учетной записи GitHub, вы можете зарегистрироваться по адресу [https://www.github.com/](https://www.github.com/) .

1. Войдите на сайт [разработчика GitHub](https://github.com/settings/developers), используя учетные данные GitHub.
1. Выберите **Приложения OAuth**, а затем — **New OAuth App** (Создать приложение OAuth).
1. Заполните поля **Application name** (Имя приложения) и **Homepage URL** (URL-адрес домашней страницы).
1. Введите значение `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` в поле **Authorization callback URL** (URL-адрес обратного вызова авторизации). Замените `your-tenant-name` именем вашего клиента Azure AD B2C. При вводе имени вашего клиента используйте только строчные буквы, даже если в Azure AD B2C имя клиента определено с помощью прописных букв.
1. Нажмите кнопку **Зарегистрировать приложение**.
1. Скопируйте значения **идентификатора клиента** и **секрета клиента**. Оба этих значения потребуются при добавлении поставщика удостоверений для вашего клиента.

::: zone pivot="b2c-user-flow"

## <a name="configure-a-github-account-as-an-identity-provider"></a>Настройка учетной записи GitHub в качестве поставщика удостоверений

1. Войдите на [портал Azure](https://portal.azure.com/) с правами глобального администратора клиента Azure AD B2C.
1. Убедитесь, что используете каталог с клиентом Azure AD B2C, выбрав фильтр **Каталог и подписка** в меню вверху и каталог с вашим клиентом.
1. Выберите **Все службы** в левом верхнем углу окна портала Azure, найдите службу **Azure AD B2C** и выберите ее.
1. Выберите **поставщики удостоверений**, а затем — **GitHub (Предварительная версия)**.
1. Введите **Имя**. Например, *GitHub*.
1. В поле **идентификатор клиента** введите идентификатор клиента приложения GitHub, созданного ранее.
1. В качестве **секрета клиента** введите записанный секрет клиента.
1. Щелкните **Сохранить**.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-policy-key"></a>Создание ключа политики

Вам необходимо сохранить секрет клиента, который ранее был записан в клиенте Azure AD B2C.

1. Войдите на [портал Azure](https://portal.azure.com/).
1. Убедитесь, что вы используете каталог, содержащий клиент Azure AD B2C. Выберите фильтр **Каталог и подписка** в верхнем меню и выберите каталог, который содержит ваш клиент.
1. Выберите **Все службы** в левом верхнем углу окна портала Azure, а затем найдите и выберите **Azure AD B2C**.
1. На странице "Обзор" выберите **Identity Experience Framework**.
1. Выберите **Ключи политики**, а затем щелкните **Добавить**.
1. Для пункта **Параметры** выберите `Manual`.
1. Введите **имя** ключа политики. Например, `GitHubSecret`. Префикс `B2C_1A_` будет автоматически добавлен к имени ключа.
1. В поле **Секрет** введите ранее записанный секрет клиента.
1. Для параметра **Использование ключа** выберите `Signature`.
1. Нажмите кнопку **Создать**.

## <a name="add-a-claims-provider"></a>Добавление поставщика утверждений

Если вы хотите, чтобы пользователи вошли с помощью учетной записи GitHub, необходимо определить учетную запись как поставщика утверждений, который Azure AD B2C может взаимодействовать с помощью конечной точки. Конечная точка предоставляет набор утверждений, используемых Azure AD B2C, чтобы проверить, была ли выполнена проверка подлинности определенного пользователя.

Учетную запись GitHub можно определить как поставщика утверждений, добавив ее в элемент **клаимспровидерс** в файле расширения политики.

1. Откройте файл *TrustFrameworkExtensions.xml*.
1. Найдите элемент **ClaimsProviders**. Если он не существует, добавьте его в корневой элемент.
1. Добавьте новый элемент **ClaimsProvider** следующим образом.

    ```xml
    <ClaimsProvider>
      <Domain>github.com</Domain>
      <DisplayName>GitHub</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="GitHub-OAUTH2">
          <DisplayName>GitHub</DisplayName>
          <Protocol Name="OAuth2" />
          <Metadata>
            <Item Key="ProviderName">github.com</Item>
            <Item Key="authorization_endpoint">https://github.com/login/oauth/authorize</Item>
            <Item Key="AccessTokenEndpoint">https://github.com/login/oauth/access_token</Item>
            <Item Key="ClaimsEndpoint">https://api.github.com/user</Item>
            <Item Key="HttpBinding">GET</Item>
            <Item Key="scope">read:user user:email</Item>
            <Item Key="UsePolicyInRedirectUri">0</Item>
            <Item Key="UserAgentForClaimsExchange">CPIM-Basic/{tenant}/{policy}</Item>
            <!-- Update the Client ID below to the Application ID -->
            <Item Key="client_id">Your GitHub application ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_GitHubSecret"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
            <OutputClaim ClaimTypeReferenceId="numericUserId" PartnerClaimType="id" />
            <OutputClaim ClaimTypeReferenceId="issuerUserId" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="github.com" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateIssuerUserId" />
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. Задайте для параметра **client_id** значение идентификатора приложения из регистрации приложения.
1. Сохраните файл.

### <a name="add-the-claims-transformations"></a>Добавление преобразований утверждений

Технический профиль GitHub требует добавления преобразований утверждений **креатеиссуерусерид** в список клаимстрансформатионс. Если в файле не определен элемент **клаимстрансформатионс** , добавьте РОДИТЕЛЬСКИЕ XML-элементы, как показано ниже. Для преобразований утверждений также требуется новый тип утверждения, определенный с именем **нумерикусерид**.

1. Найдите элемент [BuildingBlocks](buildingblocks.md). Если такой элемент не существует, добавьте его.
1. Найдите элемент [ClaimsSchema](claimsschema.md). Если такой элемент не существует, добавьте его.
1. Добавьте утверждение Нумерикусерид в элемент **ClaimsSchema** .
1. Нахождение элемента [клаимстрансформатионс](claimstransformations.md) . Если такой элемент не существует, добавьте его.
1. Добавьте преобразования утверждений Креатеиссуерусерид в элемент **клаимстрансформатионс** .

```xml
<BuildingBlocks>
  <ClaimsSchema>
    <ClaimType Id="numericUserId">
      <DisplayName>Numeric user Identifier</DisplayName>
      <DataType>long</DataType>
    </ClaimType>
  </ClaimsSchema>
  <ClaimsTransformations>
    <ClaimsTransformation Id="CreateIssuerUserId" TransformationMethod="ConvertNumberToStringClaim">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="numericUserId" TransformationClaimType="inputClaim" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="issuerUserId" TransformationClaimType="outputClaim" />
      </OutputClaims>
    </ClaimsTransformation>
  </ClaimsTransformations>
</BuildingBlocks>
```

### <a name="upload-the-extension-file-for-verification"></a>Отправка файла расширения для проверки

Теперь вы настроили политику так, чтобы Azure AD B2C знала, как взаимодействовать с учетной записью GitHub. Попробуйте отправить файл расширения политики, чтобы убедиться, что все в порядке.

1. На странице **Пользовательские политики** в клиенте Azure AD B2C выберите **Отправить политику**.
2. Включите функцию **Перезаписать политику, если она уже существует**, а затем найдите и выберите файл *TrustFrameworkExtensions.xml*.
3. Щелкните **Отправить**.

## <a name="register-the-claims-provider"></a>Регистрация поставщика утверждений

На этом этапе поставщик удостоверений уже настроен, но еще не доступен ни на одном экране регистрации или входа. Чтобы сделать его доступным, создайте дубликат существующего шаблона пути взаимодействия пользователя, а затем измените его, чтобы у него также был поставщик удостоверений GitHub.

1. Откройте файл *TrustFrameworkBase.xml* из начального пакета.
2. Найдите и скопируйте все содержимое элемента **UserJourney**, в котором присутствует запись `Id="SignUpOrSignIn"`.
3. Откройте файл *TrustFrameworkExtensions.xml* и найдите элемент **UserJourneys**. Если элемент не существует, добавьте его.
4. Вставьте все скопированное содержимое элемента **UserJourney** в качестве дочернего элемента в элемент **UserJourneys**.
5. Переименуйте идентификатор пути взаимодействия пользователя. Например, `SignUpSignInGitHub`.

### <a name="display-the-button"></a>Отображение кнопки

Элемент **ClaimsProviderSelection** является аналогом кнопки поставщика удостоверений на экранах регистрации и входа. Если добавить элемент **клаимспровидерселектион** для учетной записи GitHub, то при переходе пользователя на страницу появится новая кнопка.

1. Найдите элемент **OrchestrationStep**, содержащий `Order="1"` в созданном пути взаимодействия пользователя.
2. Добавьте следующий элемент в тэг **ClaimsProviderSelects**. Установите для параметра **TargetClaimsExchangeId** соответствующее значение, например `GitHubExchange`:

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="GitHubExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Связывание кнопки с действием

Теперь, когда у вас есть кнопка, вам необходимо связать ее с действием. Действие, в данном случае, предназначено для Azure AD B2C для взаимодействия с учетной записью GitHub для получения маркера.

1. Найдите элемент **OrchestrationStep**, содержащий `Order="2"` в пути пользователя.
2. Добавьте следующий элемент **ClaimsExchange**, убедившись, что для идентификатора можно использовать то же значение, которое было использовано для **TargetClaimsExchangeId**:

    ```xml
    <ClaimsExchange Id="GitHubExchange" TechnicalProfileReferenceId="GitHub-OAuth" />
    ```

    Обновите значение **TechnicalProfileReferenceId**, присвоив ему значение идентификатора ранее созданного технического профиля. Например, `GitHub-OAuth`.

3. Сохраните файл *TrustFrameworkExtensions.xml* и повторно отправьте его для проверки.

::: zone-end

::: zone pivot="b2c-user-flow"

## <a name="add-github-identity-provider-to-a-user-flow"></a>Добавление поставщика удостоверений GitHub в поток пользователя 

1. В клиенте Azure AD B2C выберите **Потоки пользователей**.
1. Щелкните пользовательский поток, который вы хотите использовать в качестве поставщика удостоверений GitHub.
1. В разделе **поставщики удостоверений социальных сетей** выберите **GitHub**.
1. Щелкните **Сохранить**.
1. Чтобы проверить политику, выберите пункт **выполнить пользовательскую последовательность**.
1. Для **приложения** выберите веб-приложение с именем *testapp1* , которое вы зарегистрировали ранее. В поле **URL-адрес ответа** должно содержаться значение `https://jwt.ms`.
1. Щелкните **выполнить поток пользователя**

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="update-and-test-the-relying-party-file"></a>Обновление и тестирование файла проверяющей стороны

Обновите файл проверяющей стороны, который активирует созданный путь взаимодействия пользователя.

1. Создайте копию *SignUpOrSignIn.xml* в рабочем каталоге и переименуйте ее. Например, переименуйте его в *SignUpSignInGitHub.xml*.
1. Откройте новый файл и обновите значение атрибута **PolicyId** для **TrustFrameworkPolicy**, указав уникальное значение. Например, `SignUpSignInGitHub`.
1. Обновите значение **PublicPolicyUri**, указав URI для политики. Например: `http://contoso.com/B2C_1A_signup_signin_github`.
1. Обновите значение атрибута **ReferenceId** в **дефаултусержаурнэй** , чтобы оно совпадало с идентификатором нового пути взаимодействия пользователя, созданного пользователем (сигнупсигнгисуб).
1. Сохраните изменения и отправьте файл.
1. В разделе **Настраиваемые политики** выберите **B2C_1A_signup_signin**.
1. В поле **выберите приложение** выберите веб-приложение с именем *testapp1* , которое вы зарегистрировали ранее. В поле **URL-адрес ответа** должно содержаться значение `https://jwt.ms`.
1. Выберите **выполнить сейчас** и выберите GitHub, чтобы войти с помощью GitHub и проверить настраиваемую политику.

::: zone-end
