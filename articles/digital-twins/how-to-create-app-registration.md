---
title: Регистрация приложения
titleSuffix: Azure Digital Twins
description: См. раздел Создание регистрации приложения Azure AD в качестве параметра проверки подлинности для клиентских приложений.
author: baanders
ms.author: baanders
ms.date: 10/13/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: f252471cd3cd7e3a950bf2cfe324e580da129041
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/19/2020
ms.locfileid: "92210172"
---
# <a name="create-an-app-registration-to-use-with-azure-digital-twins"></a>Создание регистрации приложения для использования с цифровым двойников Azure

При работе с экземпляром Microsoft Azure Digital двойников, как правило, взаимодействие с этим экземпляром осуществляется через клиентские приложения, такие как пользовательское клиентское приложение или пример, например [ADT Explorer](quickstart-adt-explorer.md). Эти приложения должны пройти проверку подлинности в Azure Digital двойников для взаимодействия с ним, а некоторые [механизмы проверки подлинности](how-to-authenticate-client.md) , **которые приложения могут**использовать, — [Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md) .

Это не является обязательным для всех сценариев проверки подлинности. Однако если вы используете стратегию проверки подлинности или пример кода, для которого требуется регистрация приложения, включая **идентификатор клиента** и **идентификатор клиента**, в этой статье показано, как настроить их.

## <a name="using-azure-ad-app-registrations"></a>Использование регистрации приложений Azure AD

[Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md) — это облачная служба управления удостоверениями и доступом Майкрософт. Настройка **регистрации приложения** в Azure AD — это один из способов предоставить клиентскому приложению доступ к Azure Digital двойников.

Регистрация приложения — это место, где вы настраиваете разрешения на доступ к [API-интерфейсам цифровых двойников Azure](how-to-use-apis-sdks.md). Позже клиентские приложения могут проходить проверку подлинности в регистрации приложения, используя **значения идентификатора клиента и идентификаторов**регистрации, и в результате ему предоставляются настроенные разрешения на доступ к API.

>[!TIP]
> Вы можете настроить регистрацию нового приложения каждый раз, когда это необходимо, *или* сделать это только один раз, установив единую регистрацию приложения, которая будет использоваться всеми сценариями, требующими этого.

## <a name="create-the-registration"></a>Создание регистрации

Начните с перехода к [Azure Active Directory](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) в портал Azure (вы можете использовать эту ссылку или найти ее с помощью панели поиска портала). Выберите *Регистрация приложений* в меню служба, а затем *+ Новая регистрация*.

:::image type="content" source="media/how-to-create-app-registration/new-registration.png" alt-text="Просмотрите страницу службы Azure AD в портал Azure, выделите пункт меню &quot;Регистрация приложений&quot; и нажмите кнопку &quot;+ Новая регистрация&quot;.":::

На следующей странице *Регистрация приложения* Введите запрошенные значения:
* **Имя**: отображаемое имя приложения Azure AD, связываемое с регистрацией.
* **Поддерживаемые типы учетных записей**: выберите *учетные записи только в этом каталоге организации (только каталог по умолчанию — один клиент)* .
* **URI перенаправления**: *URL-адрес ответа приложения Azure AD* для приложения Azure AD. Добавьте *общедоступный клиент/собственный (мобильный & Desktop)* URI для `http://localhost` .

По завершении нажмите кнопку Register ( *зарегистрировать* ).

:::image type="content" source="media/how-to-create-app-registration/register-an-application.png" alt-text="Просмотрите страницу службы Azure AD в портал Azure, выделите пункт меню &quot;Регистрация приложений&quot; и нажмите кнопку &quot;+ Новая регистрация&quot;.":::

После завершения настройки регистрации портал перенаправит вас на страницу сведений о ней.

## <a name="collect-client-id-and-tenant-id"></a>Получение идентификатора клиента и идентификатора клиента

Затем собирайте важные значения о регистрации приложения на странице сведений:

:::image type="content" source="media/how-to-create-app-registration/app-important-values.png" alt-text="Просмотрите страницу службы Azure AD в портал Azure, выделите пункт меню &quot;Регистрация приложений&quot; и нажмите кнопку &quot;+ Новая регистрация&quot;.":::

Запишите _**идентификатор приложения (клиента)**_ и _**идентификатор каталога (клиента)**_ , показанные на **странице.** Это значения, которые клиентское приложение будет использовать для аутентификации в Azure Digital двойников.

## <a name="provide-azure-digital-twins-api-permission"></a>Предоставление разрешения API цифровых двойников Azure

Затем настройте регистрацию приложения, созданную с базовыми разрешениями для API-интерфейсов Azure Digital двойников.

На странице портала для регистрации приложения в меню выберите *разрешения API* . На следующей странице разрешения нажмите кнопку *+ Добавить разрешение* .

:::image type="content" source="media/how-to-create-app-registration/add-permission.png" alt-text="Просмотрите страницу службы Azure AD в портал Azure, выделите пункт меню &quot;Регистрация приложений&quot; и нажмите кнопку &quot;+ Новая регистрация&quot;.":::

На следующей странице *разрешений API запроса* перейдите к *API-интерфейсам, используемым моей организацией* , и выполните поиск по запросу *Azure Digital двойников*. Выберите _**Azure Digital двойников**_ из результатов поиска, чтобы продолжить назначение разрешений для API-интерфейсов цифровой двойников Azure.

:::image type="content" source="media/how-to-create-app-registration/request-api-permissions-1.png" alt-text="Просмотрите страницу службы Azure AD в портал Azure, выделите пункт меню &quot;Регистрация приложений&quot; и нажмите кнопку &quot;+ Новая регистрация&quot;.":::

>[!NOTE]
> Если у вашей подписки по-прежнему есть существующий экземпляр Azure Digital двойников из предыдущего общедоступного предварительной версии службы (до июля 2020), вам потребуется найти и выбрать _**службу "интеллектуальные модули Azure**_ ". Это старое имя для того же набора API-интерфейсов (Обратите внимание, что *идентификатор приложения (клиента)* аналогичен показанному на снимке экрана выше), и ваш опыт не будет изменяться после этого шага.
> :::image type="content" source="media/how-to-create-app-registration/request-api-permissions-1-smart-spaces.png" alt-text="Просмотрите страницу службы Azure AD в портал Azure, выделите пункт меню &quot;Регистрация приложений&quot; и нажмите кнопку &quot;+ Новая регистрация&quot;.":::

Далее предстоит выбрать разрешения, которые будут предоставлены для этих API. Разверните разрешение **Чтение (1)** и установите флажок *чтение. запись* , чтобы предоставить этому приложению доступ для чтения и записи регистрации приложения.

:::image type="content" source="media/how-to-create-app-registration/request-api-permissions-2.png" alt-text="Просмотрите страницу службы Azure AD в портал Azure, выделите пункт меню &quot;Регистрация приложений&quot; и нажмите кнопку &quot;+ Новая регистрация&quot;.":::

По завершении нажмите кнопку *Добавить разрешения* .

### <a name="verify-success"></a>Проверка успешного выполнения

На странице *разрешения API* убедитесь, что появилась запись для Azure Digital двойников, отражающая разрешения на чтение и запись:

:::image type="content" source="media/how-to-create-app-registration/verify-api-permissions.png" alt-text="Просмотрите страницу службы Azure AD в портал Azure, выделите пункт меню &quot;Регистрация приложений&quot; и нажмите кнопку &quot;+ Новая регистрация&quot;.":::

Вы также можете проверить подключение к Azure Digital двойников в *manifest.js*регистрации приложения, которое автоматически обновлялось с помощью сведений о цифровой двойников Azure при добавлении разрешений API.

Для этого в меню выберите *Манифест* , чтобы просмотреть код манифеста регистрации приложения. Прокрутите окно кода вниз и найдите эти поля в разделе `requiredResourceAccess` . Значения должны соответствовать приведенным на снимке экрана ниже:

:::image type="content" source="media/how-to-create-app-registration/verify-manifest.png" alt-text="Просмотрите страницу службы Azure AD в портал Azure, выделите пункт меню &quot;Регистрация приложений&quot; и нажмите кнопку &quot;+ Новая регистрация&quot;.":::

Если эти значения отсутствуют, повторите действия, описанные в [разделе Добавление разрешения API](#provide-azure-digital-twins-api-permission).

## <a name="other-possible-steps-for-your-organization"></a>Другие возможные действия для вашей организации

Возможно, ваша организация требует от владельцев или администраторов Подписки дополнительных действий для успешной настройки регистрации приложения. Необходимые шаги могут отличаться в зависимости от конкретных параметров Организации.

Ниже приведены некоторые распространенные потенциальные действия, которые может потребоваться владельцу или администратору подписки. Эти и другие операции можно выполнить на странице [*регистрации Azure AD App*](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) в портал Azure.
* Предоставьте администратору согласие на регистрацию приложения. Возможно, для вашей организации требуется глобально включить *согласие администратора* в Azure AD для всех регистраций приложений в вашей подписке. Если да, владельцу или администратору потребуется нажать эту кнопку для вашей компании на странице *разрешений API* регистрации приложения, чтобы регистрация приложения была допустимой:

    :::image type="content" source="media/how-to-create-app-registration/grant-admin-consent.png" alt-text="Просмотрите страницу службы Azure AD в портал Azure, выделите пункт меню &quot;Регистрация приложений&quot; и нажмите кнопку &quot;+ Новая регистрация&quot;." (ваша компания)** _ .
   
    :::image type="content" source="media/how-to-create-app-registration/granted-admin-consent-done.png" alt-text="Просмотрите страницу службы Azure AD в портал Azure, выделите пункт меню &quot;Регистрация приложений&quot; и нажмите кнопку &quot;+ Новая регистрация&quot;.":::
* Активация общедоступного клиента
* Задать конкретные URL-адреса ответа для доступа к Web и рабочему столу
* Разрешить неявные потоки проверки подлинности OAuth2

Дополнительные сведения о регистрации приложений и его различных параметрах установки см. [*в статье регистрация приложения на платформе Microsoft Identity*](/graph/auth-register-app-v2).

## <a name="next-steps"></a>Дальнейшие действия

В этой статье вы настроите регистрацию приложения Azure AD, которая может использоваться для проверки подлинности клиентских приложений с помощью API-интерфейсов цифрового двойников Azure.

Далее ознакомьтесь с механизмами проверки подлинности, в том числе с помощью регистрации приложений и других пользователей, не следующих:
* [*Пошаговое руководство. Написание кода проверки подлинности приложения*](how-to-authenticate-client.md)