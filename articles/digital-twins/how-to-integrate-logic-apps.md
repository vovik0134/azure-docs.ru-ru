---
title: Интеграция с Logic Apps
titleSuffix: Azure Digital Twins
description: См. раздел Подключение Logic Apps к Azure Digital двойников с помощью настраиваемого соединителя.
author: baanders
ms.author: baanders
ms.date: 9/11/2020
ms.topic: how-to
ms.service: digital-twins
ms.reviewer: baanders
ms.openlocfilehash: 3fbd9016bcbfa83574d894af7ca728b863f54344
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93129326"
---
# <a name="integrate-with-logic-apps-using-a-custom-connector"></a>Интеграция с Logic Apps с помощью настраиваемого соединителя

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) — это облачная служба, которая помогает автоматизировать рабочие процессы в приложениях и службах. Подключив Logic Apps к API-интерфейсам Azure Digital двойников, вы можете создавать такие автоматизированные потоки в Azure Digital двойников и их данные.

В Azure Digital двойников в настоящее время нет сертифицированного (предварительно созданного) соединителя для Logic Apps. Вместо этого текущий процесс использования Logic Apps с Azure Digital двойников заключается в создании [**настраиваемого соединителя Logic Apps**](../logic-apps/custom-connector-overview.md)с помощью [настраиваемого цифрового Двойникова Swagger Azure](/samples/azure-samples/digital-twins-custom-swaggers/azure-digital-twins-custom-swaggers/) , который был изменен для работы с Logic Apps.

> [!NOTE]
> Существует несколько версий Swagger, содержащихся в пользовательском образце Swagger, связанном выше. Последняя версия будет найдена во вложенной папке с самой последней датой, но более ранние версии, содержащиеся в этом примере, также все еще поддерживаются.

В этой статье вы будете использовать [портал Azure](https://portal.azure.com) , чтобы **создать настраиваемый соединитель** , который можно использовать для подключения Logic apps к экземпляру Digital двойников Azure. Затем вы **создадите приложение логики** , которое использует это подключение для примера сценария, в котором события, активируемые таймером, будут автоматически обновлять двойника в вашем экземпляре Azure Digital двойников. 

## <a name="prerequisites"></a>Предварительные требования

Если у вас еще нет подписки Azure, **создайте [бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)** , прежде чем начинать работу.
Войдите в [портал Azure](https://portal.azure.com) с помощью этой учетной записи. 

Кроме того, в ходе установки необходимых компонентов необходимо выполнить следующие действия. В оставшейся части этого раздела будут рассмотрены следующие шаги:
- Настроить экземпляр Azure Digital Twins.
- Добавление цифрового двойника

### <a name="set-up-azure-digital-twins-instance"></a>Настройка экземпляра Azure Digital Twins

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

### <a name="add-a-digital-twin"></a>Добавление цифрового двойника

В этой статье для обновления двойника в вашем экземпляре Azure Digital двойников используется Logic Apps. Для продолжения необходимо добавить хотя бы один двойника в экземпляр. 

Вы можете добавить двойников с помощью [API дигиталтвинс](/rest/api/digital-twins/dataplane/twins), [пакета SDK для .NET (C#)](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet-preview&preserve-view=true)или [Azure Digital двойников CLI](how-to-use-cli.md). Подробные инструкции по созданию двойников с помощью этих методов см. в разделе « [*практические руководства. Управление цифровыми двойниковми*](how-to-manage-twin.md)».

Вам потребуется **_идентификатор двойника_** двойника в созданном вами экземпляре.

## <a name="set-up-app-registration"></a>Настройка регистрации приложения

[!INCLUDE [digital-twins-prereq-registration.md](../../includes/digital-twins-prereq-registration.md)]

### <a name="get-app-registration-client-secret"></a>Получение секрета клиента регистрации приложения

Кроме того, вам потребуется создать **_секрет клиента_** для регистрации приложения Azure AD. Для этого перейдите на страницу [Регистрация приложений](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) в портал Azure (эту ссылку можно использовать или найти на панели поиска на портале). Выберите регистрацию, созданную в предыдущем разделе, из списка, чтобы открыть сведения о ней. 

Нажмите *Сертификаты и секреты* в меню регистрации и выберите *+ новый секрет клиента* .

:::image type="content" source="media/how-to-integrate-logic-apps/client-secret.png" alt-text="Представление портала регистрации приложения Azure AD. В меню ресурсов есть выделение &quot;сертификаты и секреты&quot;, а также выделение на странице &quot;новый секрет клиента&quot;.":::

Введите любые значения для *описания* и *истечения срока действия* , а затем нажмите кнопку *добавить* .

:::image type="content" source="media/how-to-integrate-logic-apps/add-client-secret.png" alt-text="Представление портала регистрации приложения Azure AD. В меню ресурсов есть выделение &quot;сертификаты и секреты&quot;, а также выделение на странице &quot;новый секрет клиента&quot;.":::

Теперь убедитесь, что секрет клиента отображается на странице _сертификаты & секреты_ с _истечением срока действия_ и полями _значений_ . Запишите _значение_ , которое будет использоваться позже (вы также можете скопировать его в буфер обмена с помощью значка копирования).

:::image type="content" source="media/how-to-integrate-logic-apps/client-secret-value.png" alt-text="Представление портала регистрации приложения Azure AD. В меню ресурсов есть выделение &quot;сертификаты и секреты&quot;, а также выделение на странице &quot;новый секрет клиента&quot;.":::

## <a name="create-custom-logic-apps-connector"></a>Создание настраиваемого соединителя Logic Apps

Теперь вы готовы создать [настраиваемый соединитель Logic Apps](../logic-apps/custom-connector-overview.md) для API-интерфейсов Azure Digital двойников. После этого вы сможете подключать Azure Digital двойников при создании приложения логики в следующем разделе.

Перейдите на страницу [Logic Apps настраиваемый соединитель](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Web%2FcustomApis) в портал Azure (эту ссылку можно использовать или найти на панели поиска портала). Нажмите *+ Добавить* .

:::image type="content" source="media/how-to-integrate-logic-apps/logic-apps-custom-connector.png" alt-text="Представление портала регистрации приложения Azure AD. В меню ресурсов есть выделение &quot;сертификаты и секреты&quot;, а также выделение на странице &quot;новый секрет клиента&quot;." + "создать* ". 

:::image type="content" source="media/how-to-integrate-logic-apps/create-logic-apps-custom-connector.png" alt-text="Представление портала регистрации приложения Azure AD. В меню ресурсов есть выделение &quot;сертификаты и секреты&quot;, а также выделение на странице &quot;новый секрет клиента&quot;.":::

Откроется вкладка *Просмотр и создание* , где можно нажать кнопку *создать* внизу, чтобы создать ресурс.

:::image type="content" source="media/how-to-integrate-logic-apps/review-logic-apps-custom-connector.png" alt-text="Представление портала регистрации приложения Azure AD. В меню ресурсов есть выделение &quot;сертификаты и секреты&quot;, а также выделение на странице &quot;новый секрет клиента&quot;.":::

Откроется страница развертывания для соединителя. После завершения развертывания нажмите кнопку *Перейти к ресурсу* , чтобы просмотреть сведения о соединителе на портале.

### <a name="configure-connector-for-azure-digital-twins"></a>Настройка соединителя для Azure Digital двойников

Далее вы настроите созданный соединитель для доступа к Azure Digital двойников.

Сначала скачайте пользовательский файл Swagger Microsoft Azure Digital двойников, который был изменен для работы с Logic Apps. Скачайте пример **настраиваемого соединителя Swagger (Logic Apps) Azure Digital двойников** по [**этой ссылке**](/samples/azure-samples/digital-twins-custom-swaggers/azure-digital-twins-custom-swaggers/) , нажав кнопку *скачать ZIP-файл* . Перейдите к скачанной папке *Azure_Digital_Twins_custom_Swaggers__Logic_Apps_connector_.zip* и распакуйте ее. 

Пользовательский Swagger для этого учебника находится в папке _* * Azure_Digital_Twins_custom_Swaggers__Logic_Apps_connector_ \логикаппс **_. Эта папка содержит вложенные папки, которые называются *стабильными* и *предварительными* версиями, оба из которых содержат различные версии Swagger, упорядоченные по датам. Папка с самой последней датой будет содержать последнюю копию Swagger. Независимо от того, какая версия выбрана, файл Swagger имеет имя _** digitaltwins.jsв * * _.

> [!NOTE]
> Если вы не работаете с предварительной версией функции, обычно рекомендуется использовать самую последнюю *стабильную* версию Swagger. Тем не менее предыдущие версии и предварительные версии Swagger также поддерживаются. 

Затем перейдите на страницу обзора соединителя в [портал Azure](https://portal.azure.com) и нажмите кнопку *изменить* .

:::image type="content" source="media/how-to-integrate-logic-apps/edit-connector.png" alt-text="Представление портала регистрации приложения Azure AD. В меню ресурсов есть выделение &quot;сертификаты и секреты&quot;, а также выделение на странице &quot;новый секрет клиента&quot;." для своего цвета.
    - Описание: заполните все нужные значения.
    - Схема: HTTPS (оставьте значение по умолчанию)
    - Узел. *имя узла* для своего экземпляра Digital двойников Azure.
    - Базовый URL-адрес:/(оставьте значение по умолчанию)

Затем нажмите кнопку *Безопасность* в нижней части окна, чтобы перейти к следующему шагу настройки.

:::image type="content" source="media/how-to-integrate-logic-apps/configure-next.png" alt-text="Представление портала регистрации приложения Azure AD. В меню ресурсов есть выделение &quot;сертификаты и секреты&quot;, а также выделение на странице &quot;новый секрет клиента&quot;.":::

На этапе безопасности нажмите кнопку *изменить* и настройте следующие сведения:
* **Тип проверки подлинности** : OAuth 2,0
* **OAuth 2,0** :
    - Поставщик удостоверений: Azure Active Directory
    - Идентификатор клиента: *идентификатор приложения (клиента)* для регистрации приложения Azure AD.
    - Секрет клиента: *секрет клиента* , созданный в [*предварительных требованиях*](#prerequisites) для регистрации приложения Azure AD.
    - URL-адрес для входа: https://login.windows.net (оставьте значение по умолчанию)
    - Идентификатор клиента: *идентификатор каталога (клиента)* для регистрации приложения Azure AD.
    - URL-адрес ресурса: 0b07f429-9f4b-4714-9392-cc5e8e80c8b0
    - Область: Directory. Акцессасусер. ALL
    - URL-адрес перенаправления: (оставьте значение по умолчанию сейчас)

Обратите внимание, что в поле URL-адрес перенаправления указано *Сохранение пользовательского соединителя для создания URL-адреса перенаправления* . Сделайте это сейчас, щелкнув *соединитель обновления* в верхней части панели, чтобы подтвердить параметры соединителя.

:::image type="content" source="media/how-to-integrate-logic-apps/update-connector.png" alt-text="Представление портала регистрации приложения Azure AD. В меню ресурсов есть выделение &quot;сертификаты и секреты&quot;, а также выделение на странице &quot;новый секрет клиента&quot;.":::

<!-- Success message? didn't see one -->

Вернитесь в поле URL-адрес перенаправления и скопируйте созданное значение. Он будет использован на следующем шаге.

:::image type="content" source="media/how-to-integrate-logic-apps/copy-redirect-url.png" alt-text="Представление портала регистрации приложения Azure AD. В меню ресурсов есть выделение &quot;сертификаты и секреты&quot;, а также выделение на странице &quot;новый секрет клиента&quot;." будет выполнен весь процесс ввода параметров конфигурации. Они не заполняют значения из последнего момента, поэтому, если вы хотите сохранить обновленную конфигурацию с любыми измененными значениями, необходимо повторно ввести все остальные значения, чтобы избежать их перезаписи по умолчанию.

### <a name="grant-connector-permissions-in-the-azure-ad-app"></a>Предоставление разрешений соединителя в приложении Azure AD

Затем используйте значение *URL-адреса перенаправления* настраиваемого соединителя, скопированное на предыдущем шаге, чтобы предоставить разрешения соединителя в регистрации приложения Azure AD.

Перейдите на страницу [Регистрация приложений](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) в портал Azure и выберите регистрацию из списка. 

В разделе *Проверка подлинности* в меню регистрации Добавьте URI.

:::image type="content" source="media/how-to-integrate-logic-apps/add-uri.png" alt-text="Представление портала регистрации приложения Azure AD. В меню ресурсов есть выделение &quot;сертификаты и секреты&quot;, а также выделение на странице &quot;новый секрет клиента&quot;."::: 

Введите *URL-адрес перенаправления* пользовательского соединителя в новое поле и нажмите значок *сохранить* .

:::image type="content" source="media/how-to-integrate-logic-apps/save-uri.png" alt-text="Представление портала регистрации приложения Azure AD. В меню ресурсов есть выделение &quot;сертификаты и секреты&quot;, а также выделение на странице &quot;новый секрет клиента&quot;.":::

Теперь вы настроили настраиваемый соединитель, который может получить доступ к API-интерфейсам цифровых двойников Azure. 

## <a name="create-logic-app"></a>Создание приложения логики

Далее вы создадите приложение логики, которое будет использовать новый соединитель для автоматизации обновлений Digital двойников для Azure.

В [портал Azure](https://portal.azure.com)найдите *приложение логики* на панели поиска портала. При выборе нужно перейти на страницу *Logic Apps (приложения логики* ). Нажмите кнопку *CREATE Logic App (создать приложение логики* ), чтобы создать новое приложение логики.

:::image type="content" source="media/how-to-integrate-logic-apps/create-logic-app.png" alt-text="Представление портала регистрации приложения Azure AD. В меню ресурсов есть выделение &quot;сертификаты и секреты&quot;, а также выделение на странице &quot;новый секрет клиента&quot;." _Проверка + создать_ ".

Откроется вкладка *Просмотр и создание* , на которой можно проверить сведения и нажать кнопку *создать* внизу, чтобы создать ресурс.

Откроется страница развертывание приложения логики. После завершения развертывания нажмите кнопку *Перейти к ресурсу* , чтобы перейти к *конструктору Logic Apps* , в котором будет заполнена логика рабочего процесса.

### <a name="design-workflow"></a>Рабочий процесс разработки

В *конструкторе Logic Apps* в разделе *Запуск с общим триггером* выберите _**повторение**_ .

:::image type="content" source="media/how-to-integrate-logic-apps/logic-apps-designer-recurrence.png" alt-text="Представление портала регистрации приложения Azure AD. В меню ресурсов есть выделение &quot;сертификаты и секреты&quot;, а также выделение на странице &quot;новый секрет клиента&quot;.":::

На следующей странице *конструктора Logic Apps* измените частоту **повторений** до *секунды* , чтобы событие запускалось каждые 3 секунды. Это позволит легко увидеть результаты позже, не дожидаясь очень долго.

Нажмите *+ новый шаг* .

Откроется окно *Выбор действия* . Перейдите на вкладку *Пользовательская* . Вы должны увидеть пользовательский соединитель с более ранней версии в верхнем поле.

:::image type="content" source="media/how-to-integrate-logic-apps/custom-action.png" alt-text="Представление портала регистрации приложения Azure AD. В меню ресурсов есть выделение &quot;сертификаты и секреты&quot;, а также выделение на странице &quot;новый секрет клиента&quot;." *Управление цифровыми двойниковми* ".
* _версия API_ : Последняя версия API. В настоящее время это значение равно *2020-10-31* .

Нажмите кнопку *сохранить* в конструкторе Logic Apps.

Можно выбрать другие операции, выбрав _+ новый шаг_ в том же окне.

:::image type="content" source="media/how-to-integrate-logic-apps/save-logic-app.png" alt-text="Представление портала регистрации приложения Azure AD. В меню ресурсов есть выделение &quot;сертификаты и секреты&quot;, а также выделение на странице &quot;новый секрет клиента&quot;.":::

## <a name="query-twin-to-see-the-update"></a>Запрос двойника для просмотра обновления

Теперь, когда приложение логики создано, событие двойника Update, определенное в конструкторе Logic Apps, должно выполняться каждые три секунды. Это означает, что через три секунды вы сможете запросить двойника и увидеть новые исправленные значения.

Вы можете запросить двойника с помощью выбранного метода (например, [пользовательского клиентского приложения](tutorial-command-line-app.md), [примера приложения для цифрового двойников Explorer](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/), [пакетов SDK и API](how-to-use-apis-sdks.md)или [интерфейса командной строки](how-to-use-cli.md)). 

Дополнительные сведения о запросах к экземпляру Digital двойников для Azure см. [*в статье как выполнить запрос к графу двойника*](how-to-query-graph.md).

## <a name="next-steps"></a>Дальнейшие действия

В этой статье вы создали приложение логики, которое регулярно обновляет двойника в экземпляре Azure Digital двойников с указанным вами исправлением. Вы можете попробовать выбрать другие API в настраиваемом соединителе, чтобы создать Logic Apps для различных действий в экземпляре.

Дополнительные сведения о доступных операциях API и сведениях, которые они необходимы, см. в статье [*Использование интерфейсов API и пакетов SDK для цифровых двойников Azure*](how-to-use-apis-sdks.md).