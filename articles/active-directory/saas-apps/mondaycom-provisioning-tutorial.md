---
title: Руководство. Настройка monday.com для автоматической подготовки пользователей с помощью Azure Active Directory | Документация Майкрософт
description: Узнайте, как автоматически подготавливать и отзывать учетные записи пользователей из Azure AD в monday.com.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 7dba523e-c75a-4895-bad4-82239a263afe
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/24/2020
ms.author: Zhchia
ms.openlocfilehash: 7bd143de44ba68146e530a111eb44dc5da4b770a
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/16/2020
ms.locfileid: "97592652"
---
# <a name="tutorial-configure-mondaycom-for-automatic-user-provisioning"></a>Учебник. Настройка monday.com для автоматической подготовки пользователей

В этом руководстве описываются действия, которые необходимо выполнить в monday.com и Azure Active Directory (Azure AD) для настройки автоматической подготовки пользователей. При настройке Azure AD автоматически подготавливает и отменяет подготовку пользователей и групп для [Monday.com](https://www.monday.com/) с помощью службы подготовки Azure AD. Подробные сведения о том, что делает эта служба, как она работает, и часто задаваемые вопросы см. в статье [Автоматическая подготовка пользователей и ее отзыв для приложений SaaS в Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Поддерживаемые возможности
> [!div class="checklist"]
> * Создание пользователей в monday.com
> * Удалить пользователей в monday.com, когда им больше не нужен доступ
> * Синхронизация атрибутов пользователей между Azure AD и monday.com
> * Подготавливайте группы и членство в группах в monday.com
> * [Единый вход](mondaycom-tutorial.md) в Monday.com (рекомендуется)

## <a name="prerequisites"></a>Предварительные требования

В сценарии, описанном в этом руководстве, предполагается, что у вас уже имеется:

* [Клиент Azure AD.](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Учетная запись пользователя в Azure AD с [разрешением](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) настраивать подготовку (например, администратор приложений, администратор облачных приложений, владелец приложения или глобальный администратор). 
* Учетная запись **предприятия**  Monday.com.

## <a name="step-1-plan-your-provisioning-deployment"></a>Шаг 1. Планирование развертывания для подготовки
1. Узнайте, [как работает служба подготовки](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Определите, кто будет находиться в [области подготовки](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Определите, какие данные должны [сопоставляться между Azure AD и Monday.com](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-mondaycom-to-support-provisioning-with-azure-ad"></a>Шаг 2. Настройка monday.com для поддержки подготовки с помощью Azure AD
1. Войдите в [Monday.com](https://www.monday.com/). Щелкните изображение профиля в левой области навигации.
2. Перейдите в раздел **администрирование > безопасность** .
3. Щелкните **Открыть** в разделе **scim** на вкладке **имя входа** .

![Вкладка "Подготовка scim"](media/mondaycom-provisioning-tutorial/provisioning-tab-monday.png)

4. Нажмите кнопку **создать** . Это **URL-адрес клиента** и **маркер секрета** , которые понадобятся для шага 5.

>[!NOTE]
>Не предоставляйте общий доступ или не сохраняйте этот маркер секрета. Вы всегда можете создать новый маркер каждый раз, когда он необходим.

## <a name="step-3-add-mondaycom-from-the-azure-ad-application-gallery"></a>Шаг 3. Добавление monday.com из коллекции приложений Azure AD

Добавьте monday.com из коллекции приложений Azure AD, чтобы начать управление подготовкой в monday.com. Если вы ранее настроили monday.com для единого входа, вы можете использовать то же приложение. Однако при первоначальном тестировании интеграции рекомендуется создать отдельное приложение. Дополнительные сведения о добавлении приложения из коллекции см. [здесь](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Шаг 4. Определение пользователей для включения в область подготовки 

Служба подготовки Azure AD позволяет определить пользователей, которые будут подготовлены, на основе назначения приложению и (или) атрибутов пользователя или группы. Если вы решили указать, кто именно будет подготовлен к работе в приложении, на основе назначения, можно выполнить следующие [действия](../manage-apps/assign-user-or-group-access-portal.md), чтобы назначить пользователей и группы приложению. Если вы решили указать, кто именно будет подготовлен, на основе одних только атрибутов пользователя или группы, можете использовать фильтр задания области, как описано [здесь](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* При назначении пользователей и групп для monday.com необходимо выбрать роль, отличную от **доступа по умолчанию**. Пользователи с ролью "Доступ по умолчанию" исключаются из подготовки и будут помечены в журналах подготовки как не назначенные явно. Кроме того, если эта роль является единственной, доступной в приложении, можно [изменить манифест приложения](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps), чтобы добавить дополнительные роли. 

* Начните с малого. Протестируйте небольшой набор пользователей и групп, прежде чем выполнять развертывание для всех. Если в область подготовки включены назначенные пользователи и группы, проверьте этот механизм, назначив приложению одного или двух пользователей либо одну или две группы. Если в область включены все пользователи и группы, можно указать [фильтр области на основе атрибутов](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-mondaycom"></a>Шаг 5. Настройка автоматической подготовки пользователей в monday.com 

В этом разделе описывается, как настроить службу подготовки Azure AD для создания, обновления и отключения пользователей и (или) групп в TestApp на основе их назначений в Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-mondaycom-in-azure-ad"></a>Чтобы настроить автоматическую подготовку учетных записей пользователей для monday.com в Azure AD, сделайте следующее:

1. Войдите на [портал Azure](https://portal.azure.com). Выберите **Корпоративные приложения**, а затем **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. В списке приложений выберите **monday.com**.

    ![Ссылка на monday.com в списке приложений](common/all-applications.png)

3. Выберите вкладку **Подготовка**.

    ![Вкладка "Подготовка"](common/provisioning.png)

4. Для параметра **Режим подготовки к работе** выберите значение **Automatic** (Автоматически).

    ![Вкладка "Подготовка" с выделенным значением "Авто"](common/provisioning-automatic.png)

5. В разделе **учетные данные администратора** введите URL-адрес клиента Monday.com и маркер секрета. Щелкните **проверить подключение** , чтобы убедиться, что Azure AD может подключиться к Monday.com. Если подключение не выполняется, убедитесь, что у учетной записи monday.com есть разрешения администратора, и повторите попытку.

    ![Токен](common/provisioning-testconnection-tenanturltoken.png)

6. В поле **Почтовое уведомление** введите адрес электронной почты пользователя или группы, которые должны получать уведомления об ошибках подготовки, а также установите флажок **Отправить уведомление по электронной почте при сбое**.

    ![Почтовое уведомление](common/provisioning-notification-email.png)

7. Щелкните **Сохранить**.

8. В разделе **сопоставления** выберите **синхронизировать Azure Active Directory пользователей с Monday.com**.

9. Проверьте атрибуты пользователя, которые синхронизированы из Azure AD в monday.com в разделе " **сопоставление атрибутов** ". Атрибуты, выбранные как свойства **Matching** , используются для сопоставления учетных записей пользователей в Monday.com для операций обновления. Если вы решили изменить [соответствующий целевой атрибут](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes), необходимо убедиться, что API Monday.com поддерживает фильтрацию пользователей на основе этого атрибута. Нажмите кнопку **Сохранить**, чтобы зафиксировать все изменения.

   |attribute|Тип|Поддерживается для фильтрации|
   |---|---|---|
   |userName|Строка|&check;|
   |displayName|Строка|
   |title|Строка|
   |emails[type eq "work"].value|Строка|
   |локаль|Строка|
   |timezone|Строка|

10. В разделе **сопоставления** выберите **синхронизировать Azure Active Directory группы с Monday.com**.

11. Проверьте атрибуты группы, которые синхронизированы из Azure AD в monday.com в разделе " **сопоставление атрибутов** ". Атрибуты, выбранные как свойства **Matching** , используются для сопоставления групп в Monday.com для операций обновления. Нажмите кнопку **Сохранить**, чтобы зафиксировать все изменения.

      |attribute|Тип|Поддерживается для фильтрации|
      |---|---|---|
      |displayName|Строка|&check;|
      |members|Справочник|

12. Чтобы настроить фильтры области, ознакомьтесь со следующими инструкциями, предоставленными в [руководстве по фильтрам области](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Чтобы включить службу подготовки Azure AD для monday.com, измените значение параметра **состояние подготовки** на **включено** в разделе **Параметры** .

    ![Состояние подготовки "Включено"](common/provisioning-toggle-on.png)

14. Определите пользователей и (или) группы, которые вы хотите подготавливать к monday.com, выбрав нужные значения в **области** в разделе **Параметры** .

    ![Область действия подготовки](common/provisioning-scope.png)

15. Когда будете готовы выполнить подготовку, нажмите кнопку **Сохранить**.

    ![Сохранение конфигурации подготовки](common/provisioning-configuration-save.png)

После этого начнется цикл начальной синхронизации всех пользователей и групп, определенных в поле **Область** в разделе **Параметры**. Начальный цикл занимает больше времени, чем последующие циклы. Пока служба подготовки Azure AD запущена, они выполняются примерно каждые 40 минут. 

## <a name="step-6-monitor-your-deployment"></a>Шаг 6. Мониторинг развертывания
После настройки подготовки используйте следующие ресурсы для мониторинга развертывания.

* Используйте [журналы подготовки](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs), чтобы определить, какие пользователи были подготовлены успешно или неудачно.
* Используйте [индикатор выполнения](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user), чтобы узнать состояние цикла подготовки и приблизительное время до его завершения.
* Если конфигурация подготовки, вероятно, находится в неработоспособном состоянии, приложение перейдет в карантин. Дополнительные сведения о режимах карантина см. [здесь](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).  

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Управление подготовкой учетных записей пользователей для корпоративных приложений](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Дальнейшие действия

* [Сведения о просмотре журналов и получении отчетов о действиях по подготовке](../manage-apps/check-status-user-account-provisioning.md)
