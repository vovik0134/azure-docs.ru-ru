---
title: Руководство по интеграции единого входа Azure Active Directory с приложением ServiceNow | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и ServiceNow.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/09/2020
ms.author: jeedes
ms.openlocfilehash: 7a15248300150c9b79c4678701f81fac923d56a3
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/30/2020
ms.locfileid: "96353618"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-servicenow"></a>Руководство по интеграции единого входа Azure Active Directory с приложением ServiceNow

В этом учебнике описано, как интегрировать ServiceNow с Azure Active Directory (Azure AD). Интеграция ServiceNow с Azure AD обеспечивает следующие возможности:

* Управление доступом к ServiceNow с помощью Azure AD.
* Включение автоматического входа пользователей в ServiceNow с помощью учетных записей Azure AD.
* Централизованное управление учетными записями через портал Azure.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4Jao6]

## <a name="prerequisites"></a>Предварительные требования

Чтобы приступить к работе, потребуется следующее.

* Подписка Azure AD. Если у вас нет подписки, вы можете получить [бесплатную учетную запись](https://azure.microsoft.com/free/).
* Подписка ServiceNow с поддержкой единого входа.
* Для ServiceNow экземпляр или клиент ServiceNow поддерживает версии для Калгари, Кингстона, Лондона, Мадрида, Нью Йорка, Орландо и Парижа или более поздние версии.
* Экземпляр ServiceNow Express версии Helsinki или более поздней (для ServiceNow Express).
* В клиенте ServiceNow должен быть включен [подключаемый модуль единого входа для нескольких поставщиков](https://wiki.servicenow.com/index.php?title=Multiple_Provider_Single_Sign-On#gsc.tab=0).
* Чтобы применить автоматическую настройку, включите подключаемый модуль поддержки нескольких поставщиков для ServiceNow.
* Для установки приложения ServiceNow Classic (Mobile) перейдите в соответствующее хранилище и выполните поиск по запросу ServiceNow Classic. Скачайте это приложение.

> [!NOTE]
> Эту интеграцию также можно использовать в облачной среде Azure AD для государственных организаций США. Это приложение можно найти в коллекции облачных приложений с поддержкой Azure AD для государственных организаций США и настроить таким же образом, как и в общедоступном облаке.

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде. 

* ServiceNow поддерживает единый вход, инициированный **пакетом обновления**.

* ServiceNow поддерживает [автоматическую подготовку пользователей](servicenow-provisioning-tutorial.md).

* Теперь для мобильного приложения ServiceNow Classic (Mobile) можно настроить Azure AD для обеспечения единого входа. Поддерживаются пользователи Android и iOS. В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.

## <a name="add-servicenow-from-the-gallery"></a>Добавление ServiceNow из коллекции.

Чтобы настроить интеграцию ServiceNow с Azure AD, необходимо добавить ServiceNow из коллекции в список управляемых приложений SaaS.

1. Войдите на портал Azure с личной, рабочей или учебной учетной записью Майкрософт.
1. Слева выберите службу **Azure Active Directory**.
1. Перейдите к разделу **Корпоративные приложения** и выберите **Все приложения**.
1. Чтобы добавить новое приложение, выберите **Новое приложение**.
1. В разделе **Добавление из коллекции** в поле поиска введите **ServiceNow**.
1. Выберите **ServiceNow** в области результатов и добавьте это приложение. Подождите несколько секунд, пока приложение не будет добавлено в ваш клиент.

## <a name="configure-and-test-azure-ad-sso-for-servicenow"></a>Настройка и проверка единого входа Azure AD для ServiceNow

Настройте и проверьте единый вход Azure AD в ServiceNow с помощью тестового пользователя **B.Simon**. Для обеспечения работы единого входа необходимо установить связь между пользователем Azure AD и соответствующим пользователем в ServiceNow.

Чтобы настроить и проверить единый вход Azure AD в ServiceNow, выполните действия из следующих стандартных блоков:

1. [Настройка единого входа Azure AD](#configure-azure-ad-sso) необходима, чтобы пользователи могли использовать эту функцию.
    1. [Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user) требуется для проверки работы единого входа Azure AD от имени пользователя B.Simon.
    1. [Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user) необходимо, чтобы разрешить B.Simon использовать единый вход Azure AD.
    1. [Настройка единого входа Azure AD в ServiceNow Express](#configure-azure-ad-sso-for-servicenow-express) необходима, чтобы пользователи могли использовать эту функцию.
2. [Настройка ServiceNow](#configure-servicenow) необходима, чтобы настроить параметры единого входа на стороне приложения.
    1. [Создание тестового пользователя ServiceNow](#create-servicenow-test-user) требуется, чтобы в ServiceNow существовал пользователь B.Simon, связанный с представлением этого пользователя в Azure AD.
    1. [Настройка единого входа в ServiceNow Express](#configure-servicenow-express-sso) необходима, чтобы настроить параметры единого входа на стороне приложения.  
3. [Проверка единого входа](#test-sso) позволяет убедиться в правильности конфигурации.
4. [Проверка единого входа для ServiceNow Classic (Mobile)](#test-sso-for-servicenow-classic-mobile) требуется, чтобы убедиться в правильности конфигурации.

## <a name="configure-azure-ad-sso"></a>Настройка единого входа Azure AD

Выполните следующие действия, чтобы включить единый вход Azure AD на портале Azure.

1. На портале Azure на странице интеграции с приложением **ServiceNow** найдите раздел **Управление**. Выберите **Единый вход**.
1. На странице **Выбрать метод единого входа** выберите **SAML**.
1. На странице **Настройка единого входа с помощью SAML** щелкните значок пера, чтобы открыть диалоговое окно **Базовая конфигурация SAML** и изменить эти параметры.

   ![Снимок экрана: настройка единого входа на странице SAML с выделенным значком пера](common/edit-urls.png)

1. В разделе **Базовая конфигурация SAML** сделайте следующее:

    а. В поле **URL-адрес для входа** введите URL-адрес в формате `https://<instancename>.service-now.com/navpage.do`.

    b. В поле **Идентификатор сущности** введите URL-адрес в формате `https://<instance-name>.service-now.com`.

    c. В качестве **URL-адреса ответа** введите один из следующих шаблонов URL-адреса:

    | URL-адрес ответа|
    |----------|
    | `https://<instancename>.service-now.com/navpage.do` |
    | `https://<instancename>.service-now.com/customer.do` | 

    d. В поле **URL-адрес выхода** введите URL-адрес в формате: `https://<instancename>.service-now.com/navpage.do`

    > [!NOTE]
    > Если в значение идентификатора добавлен символ "/", удалите его вручную.

    > [!NOTE]
    > Эти значения приведены в качестве примера. Необходимо заменить эти значения фактическим URL-адресом для входа, URL-адресом ответа, URL-адресом для выхода и идентификатором, как описано ниже. Можно также посмотреть шаблоны в разделе **Базовая конфигурация SAML** на портале Azure.

1. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** выберите **Сертификат (Base64)** . 

   ![Снимок экрана: раздел "Сертификат подписи SAML" с выделенным пунктом "Скачать"](common/certificatebase64.png)

   а. Нажмите кнопку "Копировать", чтобы скопировать **URL-адрес метаданных федерации приложения**, а затем вставьте его в Блокнот. Этот URL-адрес мы применим позже.

    b. Щелкните **Скачать** чтобы скачать **Сертификат (Base64)** и сохраните файл сертификата на компьютере.

1. Скопируйте требуемые URL-адреса из раздела **Настройка ServiceNow**.

   ![Снимок экрана: раздел настройки ServiceNow с выделенными URL-адресами](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

В этом разделе описано, как создать на портале Azure тестового пользователя с именем B.Simon.

1. Слева на портале Azure выберите **Azure Active Directory** > **Пользователи** > **Все пользователи**.
1. В верхней части экрана выберите **Новый пользователь**.
1. В разделе **Свойства пользователя** выполните следующие действия.
   1. Для параметра **Имя** введите `B.Simon`.  
   1. В поле **Имя пользователя** введите значение username@companydomain.extension. Например, `B.Simon@contoso.com`.
   1. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.
   1. Нажмите кнопку **создания**.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как предоставить пользователю B. Simon доступ к ServiceNow, чтобы он мог использовать единый вход Azure.

1. На портале Azure выберите **Корпоративные приложения** > **Все приложения**.
1. В списке приложений выберите **ServiceNow**.
1. На странице сводных сведений о приложении откройте раздел **Управление** и выберите **Пользователи и группы**.
1. Выберите **Добавить пользователя**. В диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.
1. В диалоговом окне **Пользователи и группы** выберите **B.Simon** в списке пользователей, а затем щелкните **Выбрать**.
1. Если пользователям необходимо назначить роль, вы можете выбрать ее из раскрывающегося списка **Выберите роль**. Если для этого приложения не настроена ни одна роль, будет выбрана роль "Доступ по умолчанию".
1. В диалоговом окне **Добавление назначения** выберите **Назначить**.

### <a name="configure-azure-ad-sso-for-servicenow-express"></a>Настройка единого входа в Azure AD для ServiceNow Express

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **ServiceNow** выберите **Единый вход**.

    ![Снимок экрана: страница интеграции с приложением ServiceNow с выделенным единым входом](common/select-sso.png)

2. В диалоговом окне **Выбор метода единого входа** выберите режим **SAML/WS-Fed**, чтобы включить единый вход.

    ![Снимок экрана: выбор метода единого входа с выделенным элементом SAML](common/select-saml-option.png)

3. На странице **Настройка единого входа с помощью SAML** щелкните значок пера, чтобы открыть диалоговое окно **Базовая конфигурация SAML**.

    ![Снимок экрана: настройка единого входа на странице SAML с выделенным значком пера](common/edit-urls.png)

4. В разделе **Базовая конфигурация SAML** сделайте следующее:

    а. В поле **URL-адрес для входа** введите URL-адрес в формате `https://<instancename>.service-now.com/navpage.do`.

    b. В поле **Идентификатор сущности** введите URL-адрес в формате `https://<instance-name>.service-now.com`.

    c. В качестве **URL-адреса ответа** введите один из следующих URL-адресов:

    | URL-адрес ответа |
    |-----------|
    | `https://<instancename>.service-now.com/navpage.do` |
    | `https://<instancename>.service-now.com/customer.do` |

    d. В поле **URL-адрес выхода** введите URL-адрес в формате: `https://<instancename>.service-now.com/navpage.do`
    
    > [!NOTE]
    > Если в значение идентификатора добавлен символ "/", удалите его вручную.

    > [!NOTE]
    > Эти значения приведены в качестве примера. Необходимо заменить эти значения фактическим URL-адресом для входа, URL-адресом ответа, URL-адресом для выхода и идентификатором, как описано ниже. Можно также посмотреть шаблоны в разделе **Базовая конфигурация SAML** на портале Azure.

5. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** щелкните **Скачать**, чтобы скачать требуемый **сертификат (Base64)** из предложенных вариантов. Сохраните его на компьютере.

    ![Снимок экрана: раздел "Сертификат подписи SAML" с выделенным пунктом "Скачать"](common/certificatebase64.png)

6. Вы можете разрешить Azure AD автоматически настроить ServiceNow для аутентификации на основе SAML. Чтобы включить эту службу, перейдите в раздел **Настройка ServiceNow** и щелкните **Просмотреть пошаговые инструкции** чтобы открыть окно **Настройка входа**.

    ![Снимок экрана: раздел настройки ServiceNow с выделенными пошаговыми инструкциями](./media/servicenow-tutorial/tutorial-servicenow-configure.png)

7. В форме **Настройка входа** введите имя экземпляра ServiceNow, имя администратора и его пароль. Щелкните **Настроить сейчас**. Этому пользователю с правами администратора должна быть назначена роль **security_admin** в ServiceNow. Вы также можете вручную настроить в ServiceNow использование Azure AD в качестве поставщика удостоверений SAML. Для этого щелкните **Вручную настроить это приложение для единого входа**. Скопируйте **URL-адрес выхода, идентификатор Azure AD и URL-адрес входа** из раздела "Краткий справочник".

    ![Снимок экрана: форма Configure sign-on (Настройка входа) с выделенным пунктом Configure Now (Настроить сейчас)](./media/servicenow-tutorial/configure.png "Настройка URL-адреса приложения")

## <a name="configure-servicenow"></a>Настройка ServiceNow

1. Войдите в приложение ServiceNow в качестве администратора.

1. Активируйте подключаемый модуль **Integration - Multiple Provider single sign-on Installer** (Интеграция — установщик единого входа для нескольких поставщиков), выполнив следующие действия:

    а. В области навигации слева найдите раздел **System Definition** (Определение системы) с помощью панели поиска и щелкните **Plugins** (Подключаемые модули).

    ![Снимок экрана: раздел System Definition (Определение систем) с выделенным пунктом System Definition and Plugins (Определение системы и подключаемые модули)](./media/servicenow-tutorial/tutorial-servicenow-03.png "Активация подключаемого модуля")

    b. Найдите **Integration - Multiple Provider single sign-on Installer** (Интеграция — установщик единого входа для нескольких поставщиков).

     ![Снимок экрана: страница системных подключаемых модулей с выделенным пунктом "Integration — Multiple Provider single sign-on Installer (Интеграция — установщик единого входа для нескольких поставщиков)"](./media/servicenow-tutorial/tutorial-servicenow-04.png "Активация подключаемого модуля")

    c. Выберите подключаемый модуль. Щелкните правой кнопкой мыши и выберите **Activate/Upgrade** (Активировать или обновить).

     ![Снимок экрана: контекстное меню подключаемого модуля, где выделен пункт Activate/Upgrade (Активировать или обновить)](./media/servicenow-tutorial/tutorial-activate.png "Активация подключаемого модуля")

    d. Выберите **Активировать**.

     ![Снимок экрана: диалоговое окно активации подключаемого модуля, где выделен пункт Activate (Активировать)](./media/servicenow-tutorial/tutorial-activate-1.png "Активация подключаемого модуля")

1. На панели слева найдите раздел **Multi-Provider SSO** (Единый вход с помощью нескольких поставщиков) с помощью панели поиска, затем щелкните **Properties** (Свойства).

    ![Снимок экрана: раздел Multi-Provider SSO (Единый вход с помощью нескольких поставщиков), где выделены пункты Multi-Provider SSO (Единый вход с помощью нескольких поставщиков) и Properties (Свойства)](./media/servicenow-tutorial/tutorial-servicenow-06.png "Настройка URL-адреса приложения")

1. В диалоговом окне **Multiple Provider SSO Properties** (Свойства единого входа для нескольких поставщиков) выполните следующие действия.

    ![Снимок экрана: диалоговое окно Multiple Provider SSO Properties (Свойства единого входа для нескольких поставщиков)](./media/servicenow-tutorial/ic7694981.png "Настройка URL-адреса приложения")

    * Для параметра **Enable multiple provider SSO** (Включить единый вход для нескольких поставщиков) выберите значение **Yes** (Да).
  
    * Для параметра **Enable Auto Importing of users from all identity providers into the user table** (Включить автоматический импорт пользователей из всех поставщиков удостоверений в таблицу пользователей) выберите значение **Yes** (Да).

    * Для параметра **Enable debug logging for the multiple provider SSO integration** (Включить ведение журнала отладки для интеграции нескольких поставщиков единого входа) выберите значение **Yes** (Да).

    * В поле **The field on the user table that** (Поле в пользовательской таблице) введите **адрес электронной почты**.
  
    * Щелкните **Сохранить**.

1. Вы можете настроить ServiceNow автоматически или вручную. Чтобы настроить ServiceNow автоматически, выполните следующие действия:

    1. Откройте страницу единого входа для **ServiceNow** на портале Azure.

    1. Для ServiceNow предоставляется служба настройки одним щелчком. Чтобы включить эту службу, перейдите в раздел **Конфигурация ServiceNow** и щелкните **Настроить ServiceNow**. Откроется окно **Настройка единого входа**.

        ![Снимок экрана: настройка ServiceNow с выделенными пошаговыми инструкциями](./media/servicenow-tutorial/tutorial-servicenow-configure.png)

    1. В форме **Настройка входа** введите имя экземпляра ServiceNow, имя администратора и его пароль. Щелкните **Настроить сейчас**. Этому пользователю с правами администратора должна быть назначена роль **security_admin** в ServiceNow. Вы также можете вручную настроить в ServiceNow использование Azure AD в качестве поставщика удостоверений SAML. Для этого щелкните **Вручную настроить это приложение для единого входа**. Скопируйте **URL-адрес выхода, идентификатор сущности SAML и URL-адрес службы единого входа SAML**  из раздела "Краткий справочник".

        ![Снимок экрана: форма Configure sign-on (Настройка входа) с выделенным пунктом Configure Now (Настроить сейчас)](./media/servicenow-tutorial/configure.png "Настройка URL-адреса приложения")

    1. Войдите в приложение ServiceNow в качестве администратора.

       * В режиме автоматической настройки все необходимые параметры определяются на стороне **ServiceNow**, но **сертификат X.509** по умолчанию не включен и предоставляет значение **Single Sign-On Script** (Сценарий единого входа) в качестве **MultiSSOv2_SAML2_custom**. Его необходимо вручную сопоставить в ServiceNow с поставщиком удостоверений. Выполните следующие действия.

         1. На панели слева найдите раздел **Multi-Provider SSO** (Единый вход с помощью нескольких поставщиков) с помощью панели поиска, затем щелкните **Identity Providers** (Поставщики удостоверений).

            ![Снимок экрана: раздел Multi-Provider SSO (Единый вход с помощью нескольких поставщиков), где выделен пункт Identity Providers (Поставщики удостоверений)](./media/servicenow-tutorial/tutorial-servicenow-07.png "Настройка единого входа")

         1. Щелкните автоматически созданный поставщик удостоверений.

            ![Снимок экрана: раздел Identity Providers (Поставщики удостоверений), где выделен автоматически созданный поставщик удостоверений](./media/servicenow-tutorial/tutorial-servicenow-08.png "Настройка единого входа")

         1.  В разделе **Identity Provider** (Поставщик удостоверений) выполните следующие действия:

             ![Снимок экрана: раздел Identity Provider (Поставщик удостоверений)](./media/servicenow-tutorial/automatic-config.png "Настройка единого входа")

               a. В поле **Name** (Имя) введите имя конфигурации (например, **Единый вход Microsoft Azure Federated**).

               b. Скопируйте значение **ServiceNow Homepage** (Домашняя страница ServiceNow) и вставьте его в текстовое поле **URL-адрес для входа** в разделе **Базовая конфигурация SAML для приложения ServiceNow** на портале Azure.

                > [!NOTE]
                > URL-адрес домашней страницы экземпляра ServiceNow состоит из **URL-адреса клиента ServiceNow** и **/navpage.do** (например:`https://fabrikam.service-now.com/navpage.do`).

              c. Скопируйте значение **Entity ID / Issuer** (Идентификатор сущности и издатель) и вставьте его в текстовое поле **Идентификатор** в разделе **Базовая конфигурация SAML для приложения ServiceNow** на портале Azure.

              d. Убедитесь, что параметр **NameID Policy** (Политика NameID) имеет значение `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified`. 

              д) Щелкните **Дополнительно** и укажите значение **Single Sign-On Script** (Сценарий единого входа) в качестве **MultiSSOv2_SAML2_custom**.

         1. Прокрутите вниз до раздела **X.509 Certificate** (Сертификат X.509) и щелкните **Edit** (Правка).

             ![Снимок экрана: раздел X.509 Certificate (Сертификат X.509), где выделен пункт Edit (Правка)](./media/servicenow-tutorial/tutorial-servicenow-09.png "Настройка единого входа")

         1. Выберите сертификат и щелкните значок со стрелкой вправо, чтобы добавить его.

            ![Снимок экрана: раздел Collection (Коллекция), где выделены сертификат и стрелка вправо](./media/servicenow-tutorial/tutorial-servicenow-11.png "Настройка единого входа")

          1. Щелкните **Сохранить**.

          1. В правом верхнем углу этой страницы щелкните **Test Connection** (Проверить подключение).

             ![Снимок экрана: страница с выделенным пунктом Test Connection (Проверить подключение)](./media/servicenow-tutorial/tutorial-activate-2.png "Активация подключаемого модуля")

             > [!NOTE]
             > Если проверка подключения завершается неудачно и вы не можете активировать его, можно использовать параметр переопределения ServiceNow. Введите **Sys_properties.LIST** в поле **Навигация поиска**. Откроется новая страница свойств системы. Здесь необходимо создать новое свойство с именем **glide.authenticate.multisso.test.connection.mandatory** и **типом данных** **True или False**. Затем задайте **значение** **False**.

             > ![Снимок экрана страницы "Проверить подключение"](./media/servicenow-tutorial/test-connection-fail.png "Настройка единого входа")
        
          1. Введите учетные данные, когда появится соответствующий запрос. Появится следующая страница. Здесь может возникнуть ошибка **SSO Logout Test Results** (Результаты теста единого выхода). Проигнорируйте эту ошибку и щелкните **Activate** (Активировать).

             ![Снимок экрана страницы учетных данных](./media/servicenow-tutorial/servicenow-activate.png "Настройка единого входа")
  
1. Чтобы настроить **ServiceNow** вручную, выполните следующие действия:

    1. Войдите в приложение ServiceNow в качестве администратора.

    1. В области слева выберите **Identity Providers** (Поставщики удостоверений).

        ![Снимок экрана: Multi-Provider SSO (Единый вход с помощью нескольких поставщиков), где выделен пункт Identity Providers (Поставщики удостоверений)](./media/servicenow-tutorial/tutorial-servicenow-07.png "Настройка единого входа")

    1. В диалоговом окне **Identity Providers** (Поставщики удостоверений) щелкните **New** (Создать).

        ![Снимок экрана: диалоговое окно Identity Providers (Поставщики удостоверений), где выделен пункт New (Создать)](./media/servicenow-tutorial/ic7694977.png "Настройка единого входа")

    1. В диалоговом окне **Identity Providers** (Поставщики удостоверений) щелкните **SAML**.

        ![Снимок экрана: диалоговое окно Identity Providers (Поставщики удостоверений), где выделен пункт SAML](./media/servicenow-tutorial/ic7694978.png "Настройка единого входа")

    1. В окне **Import Identity Provider Metadata** (Импорт метаданных поставщика удостоверений) выполните следующие действия:

        ![Снимок экрана: страница Import Identity Provider Metadata (Импорт метаданных поставщика удостоверений), где выделены пункты URL (URL-адрес) и Import (Импорт)](./media/servicenow-tutorial/idp.png "Настройка единого входа")

        1. Введите **URL-адрес метаданных федерации приложений**, который вы скопировали на портале Azure.

        1. Выберите **Импортировать**.

    1. Будет прочитан URL-адрес метаданных поставщика удостоверений, а также заполнены все поля сведений.

        ![Снимок экрана: Identity Provider (Поставщик удостоверений)](./media/servicenow-tutorial/ic7694982.png "Настройка единого входа")

        a. В поле **Name** (Имя) введите имя конфигурации (например, **Единый вход Microsoft Azure Federated**).

        b. Скопируйте значение параметра **ServiceNow Homepage** (Домашняя страница ServiceNow). Вставьте это значение в текстовое поле **URL-адрес для входа** в разделе **Базовая конфигурация SAML для приложения ServiceNow** на портале Azure.

        > [!NOTE]
        > URL-адрес домашней страницы экземпляра ServiceNow состоит из **URL-адреса клиента ServiceNow** и **/navpage.do** (например:`https://fabrikam.service-now.com/navpage.do`).

        c. Скопируйте значение **Entity ID / Issuer** (Идентификатор сущности / Издатель). Вставьте это значение в текстовое поле **Идентификатор** в разделе **Базовая конфигурация SAML для приложения ServiceNow** на портале Azure.

        d. Убедитесь, что параметр **NameID Policy** (Политика NameID) имеет значение `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified`.

        д) Выберите **Дополнительно**. В **поле пользователя** введите **адрес электронной почты**.

        > [!NOTE]
        > Azure AD можно настроить так, чтобы в качестве уникального идентификатора в токене SAML выдавался идентификатор пользователя Azure AD (имя участника-пользователя) или его электронный адрес. Для этого перейдите в раздел **ServiceNow** > **Атрибуты** > **Единый вход** на портале Azure и сопоставьте нужное поле с атрибутом **nameidentifier**. Значение, хранящееся для выбранного атрибута в Azure  AD (например, имя участника-пользователя), должно соответствовать значению, хранящемуся в ServiceNow для заполненного поля (например, user_name).

        ж. В правом верхнем углу этой страницы щелкните **Test Connection** (Проверить подключение).

        > [!NOTE]
        > Если проверка подключения завершается неудачно и вы не можете активировать его, можно использовать параметр переопределения ServiceNow. Введите **Sys_properties.LIST** в поле **Навигация поиска**. Откроется новая страница свойств системы. Здесь необходимо создать новое свойство с именем **glide.authenticate.multisso.test.connection.mandatory** и **типом данных** **True или False**. Затем задайте **значение** **False**.

          > ![Снимок экрана проверки подключения](./media/servicenow-tutorial/test-connection-fail.png "Настройка единого входа")

        h. Введите учетные данные, когда появится соответствующий запрос. Появится следующая страница. Здесь может возникнуть ошибка **SSO Logout Test Results** (Результаты теста единого выхода). Проигнорируйте эту ошибку и щелкните **Activate** (Активировать).

          ![credentials](./media/servicenow-tutorial/servicenow-activate.png "Настройка единого входа")

### <a name="create-servicenow-test-user"></a>Создание тестового пользователя ServiceNow

Цель этого раздела — создать пользователя с именем B.Simon в приложении ServiceNow. ServiceNow поддерживает автоматическую подготовку пользователей, которая по умолчанию включена.

> [!NOTE]
> Если вам нужно вручную создать пользователя, обратитесь в [группу поддержки клиентов ServiceNow](https://www.servicenow.com/support/contact-support.html).

### <a name="configure-servicenow-express-sso"></a>Настройка единого входа в ServiceNow Express

1. Войдите в приложение ServiceNow Express в качестве администратора.

2. В области слева выберите **Single Sign-On** (Единый вход).

    ![Снимок экрана: приложение ServiceNow Express с выделенным пунктом Single Sign-On (Единый вход)](./media/servicenow-tutorial/ic7694980ex.png "Настройка URL-адреса приложения")

3. В диалоговом окне **Single Sign-On** (Единый вход) щелкните значок конфигурации в правом верхнем углу и настройте следующие свойства.

    ![Снимок экрана: диалоговое окно Single Sign-On (Единый вход)](./media/servicenow-tutorial/ic7694981ex.png "Настройка URL-адреса приложения")

    а. Передвиньте переключатель **Enable multiple provider SSO** (Включить единый вход для нескольких поставщиков) вправо.

    b. Передвиньте переключатель **Enable debug logging for the multiple provider SSO integration** (Включить ведение журнала отладки для интеграции нескольких поставщиков единого входа) вправо.

    c. В поле **The field on the user table that** (Поле в пользовательской таблице) введите значение **user_name**.

4. В диалоговом окне **Single Sign-On** (Единый вход) щелкните **Add New Certificate** (Добавить новый сертификат).

    ![Снимок экрана: диалоговое окно Single Sign-On (Единый вход), где выделен пункт Add New Certificate (Добавить новый сертификат)](./media/servicenow-tutorial/ic7694973ex.png "Настройка единого входа")

5. В диалоговом окне **X.509 Certificates** (Сертификаты X.509) выполните следующие действия.

    ![Снимок экрана: диалоговое окно X.509 Certificates (Сертификаты X.509)](./media/servicenow-tutorial/ic7694975.png "Настройка единого входа")

    а. В поле **Name** (Имя) введите имя конфигурации (например, **TestSAML2.0**).

    b. Установите флажок **Активно**.

    c. В поле **Format** (Формат) выберите **PEM**.

    d. В поле **Type** (Тип) выберите **Trust Store Cert** (Сертификат хранилища доверия).

    д) Откройте в Блокноте сертификат в кодировке Base64, скачанный с портала Azure. Скопируйте его содержимое в буфер обмена и вставьте в текстовое поле **сертификата PEM**.

    е) Щелкните **Обновить**.

6. В диалоговом окне **Single Sign-On** (Единый вход) щелкните **Add New IdP** (Добавить новый поставщик удостоверений).

    ![Снимок экрана: диалоговое окно Single Sign-On (Единый вход), где выделен пункт Add New IdP (Добавить новый поставщик удостоверений)](./media/servicenow-tutorial/ic7694976ex.png "Настройка единого входа")

7. В диалоговом окне **Add New Identity Provider** (Добавление нового поставщика удостоверений) в разделе **Configure Identity Provider** (Настройка поставщика удостоверений) выполните следующие действия.

    ![Снимок экрана: раздел Add New Identity Provider (Добавление нового поставщика удостоверений)](./media/servicenow-tutorial/ic7694982ex.png "Настройка единого входа")

    а. В поле **Name** (Имя) введите имя конфигурации (например, **SAML 2.0**).

    b. В поле **Identity Provider URL** (URL-адрес поставщика удостоверений) вставьте значение идентификатора поставщика удостоверений, которое вы скопировали на портале Azure.

    c. В поле **Identity Provider AuthnRequest** (AuthnRequest поставщика удостоверений) вставьте значение URL-адреса для запроса аутентификации, которое вы скопировали на портале Azure.

    d. В поле **Identity Provider SingleLogoutRequest** (SingleLogoutRequest поставщика удостоверений) вставьте значение URL-адреса для выхода, которое вы скопировали на портале Azure.

    д) В поле **Identity Provider Certificate** (Сертификат поставщика удостоверений) выберите сертификат, созданный на предыдущем шаге.

8. Щелкните **Advanced Settings** (Дополнительные параметры). В разделе **Additional Service Provider Properties** (Дополнительные свойства поставщика услуг) выполните следующие действия.

    ![Снимок экрана: диалоговое окно Add New Identity Provider (Добавление нового поставщика удостоверений), где выделен пункт Advanced Settings (Дополнительные параметры)](./media/servicenow-tutorial/ic7694983ex.png "Настройка единого входа")

    а. В поле **Protocol Binding for the IDP's SingleLogoutRequest** (Привязка протокола для запроса на единый выход поставщика удостоверений) введите **urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect**.

    b. В поле **NameID Policy** (Политика идентификатора имени) введите **urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified**.

    c. В поле **AuthnContextClassRef Method** (Метод AuthnContextClassRef) введите значение `http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password`.

    d. Отключите параметр **Create an AuthnContextClass** (Создать AuthnContextClass).

9. В разделе **Additional Service Provider Properties** (Дополнительные свойства поставщика услуг) выполните следующие действия.

    ![Снимок экрана: диалоговое окно Add New Identity Provider (Добавление нового поставщика удостоверений), где выделен пункт различными свойствами](./media/servicenow-tutorial/ic7694984ex.png "Настройка единого входа")

    а. В текстовое поле **ServiceNow Homepage** (Домашняя страница ServiceNow) введите URL-адрес домашней страницы экземпляра ServiceNow.

    > [!NOTE]
    > URL-адрес домашней страницы экземпляра ServiceNow состоит из **URL-адреса клиента ServiceNow** и **/navpage.do** (например: `https://fabrikam.service-now.com/navpage.do`).

    b. В поле **Entity ID / Issuer** (Идентификатор сущности или издатель) введите URL-адрес клиента ServiceNow.

    c. В поле **Audience URI** (URI аудитории) введите URL-адрес клиента ServiceNow.

    d. В поле **Clock Skew** (Разница в показаниях часов) введите значение **60**.

    д) В **поле пользователя** введите **адрес электронной почты**.

    > [!NOTE]
    > Azure AD можно настроить так, чтобы в качестве уникального идентификатора в токене SAML выдавался идентификатор пользователя Azure AD (имя участника-пользователя) или его электронный адрес. Для этого перейдите в раздел **ServiceNow** > **Атрибуты** > **Единый вход** на портале Azure и сопоставьте нужное поле с атрибутом **nameidentifier**. Значение, хранящееся для выбранного атрибута в Azure  AD (например, имя участника-пользователя), должно соответствовать значению, хранящемуся в ServiceNow для заполненного поля (например, user_name).

    е) Щелкните **Сохранить**.

## <a name="test-sso"></a>Проверка единого входа

Выбрав плитку "ServiceNow" на Панели доступа, вы автоматически войдете в приложение ServiceNow, для которого настроили единый вход. См. дополнительные сведения о [панели доступа](../user-help/my-apps-portal-end-user-access.md)

## <a name="test-sso-for-servicenow-classic-mobile"></a>Проверка единого входа для приложения ServiceNow Classic (Mobile)

1. Откройте приложение **ServiceNow Classic (Mobile)** и сделайте следующее:

    а. Выберите значок "плюс" в правом нижнем углу.

    ![Снимок экрана: приложение ServiceNow Classic, где выделен значок "плюс"](./media/servicenow-tutorial/test-03.png)

    b. Введите имя экземпляра ServiceNow, и щелкните **Continue**(Продолжить).

    ![Снимок экрана: страница Add Instance (Добавление экземпляра) с выделенным пунктом Continue (Продолжить)](./media/servicenow-tutorial/test-04.png)

    c. На странице **Log in** (Вход) сделайте следующее:

    ![Снимок экрана: страница входа с выделенным пунктом Use external login (Использовать внешнее имя для входа)](./media/servicenow-tutorial/test-01.png)

    *  Введите **имя пользователя**, например B.simon@contoso.com.

    *  Щелкните **USE EXTERNAL LOGIN** (Использовать внешнее имя для входа). Вы перейдете на страницу Azure AD для входа.

    *  Введите свои учетные данные. Если используется сторонняя проверка подлинности или любой другой механизм безопасности, пользователю нужно будет выполнить соответствующие дополнительные действия. Откроется **домашняя страница** приложения.

        ![Снимок экрана: домашняя страница приложения](./media/servicenow-tutorial/test-02.png)

## <a name="next-steps"></a>Next Steps

После настройки ServiceNow можно применять элементы управления сеансами, которые защищают от хищения и несанкционированного доступа к конфиденциальным данным вашей организации в режиме реального времени. Элементы управления сеансом являются расширением функции условного доступа. [Узнайте, как применять управление сеансами с помощью Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad).
