---
title: Получение видимости в пределах клиента в центре безопасности Azure | Документация Майкрософт
description: В этой статье объясняется, как управлять степенью безопасности в масштабе, применяя политики ко всем подпискам, связанным с вашим клиентом Azure Active Directory.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: b85c0e93-9982-48ad-b23f-53b367f22b10
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/07/2020
ms.author: memildin
ms.openlocfilehash: d03177e3224bbd3f53320871efc6a0d6b3ea479d
ms.sourcegitcommit: fec60094b829270387c104cc6c21257826fccc54
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/09/2020
ms.locfileid: "96922640"
---
# <a name="organize-management-groups-subscriptions-and-tenant-wide-visibility"></a>Организация групп управления, подписок и видимости на уровне клиента

В этой статье описывается, как управлять степенью безопасности Организации в масштабе, применяя политики безопасности ко всем подпискам Azure, связанным с вашим клиентом Azure Active Directory.

Чтобы получить представление о безопасности всех подписок, зарегистрированных в клиенте Azure AD, необходимо назначить роль Azure с достаточными разрешениями на чтение для корневой группы управления.


## <a name="organize-your-subscriptions-into-management-groups"></a>Упорядочение подписок на группы управления

### <a name="introduction-to-management-groups"></a>Общие сведения о группах управления

Группы управления Azure предоставляют возможность эффективно управлять доступом к группам подписок, их политиками и отчетами, а также всей средой Azure за счет выполнения действий в корневой группе управления. Подписки упорядочиваются в группы управления. К ним применяются политики управления. Все подписки в группе управления автоматически наследуют применяемые к ней политики. 

Каждому клиенту Azure AD предоставляется одна группа управления верхнего уровня, которая называется **корневой группой управления**. Эта группа встроена в иерархию, чтобы в нее входили все группы управления и подписки. Эта группа позволяет применять глобальные политики и назначения ролей Azure на уровне каталога. 

Корневая группа управления создается автоматически при выполнении любого из следующих действий: 
- Откройте **группы управления** в [портал Azure](https://portal.azure.com).
- Создание группы управления с помощью вызова API.
- Создание группы управления через PowerShell. Инструкции для PowerShell см. в статье [Создание групп управления для управления ресурсами и организациями](../governance/management-groups/create-management-group-portal.md).

Группы управления не требуются для встроенного центра безопасности, но рекомендуется создать по крайней мере один из них, чтобы Корневая группа управления была создана. После создания группы с ней связываются все подписки в клиенте Azure AD. 


Подробный обзор групп управления см. в статье [Упорядочивание ресурсов с помощью групп управления Azure](../governance/management-groups/overview.md).

### <a name="view-and-create-management-groups-in-the-azure-portal"></a>Просмотр и создание групп управления в портал Azure

1. В [портал Azure](https://portal.azure.com)используйте поле поиска на верхней панели, чтобы найти и открыть **группы управления**.

    :::image type="content" source="./media/security-center-management-groups/open-management-groups-service.png" alt-text="Доступ к группам управления":::

    Появится список групп управления.

1. Чтобы создать группу управления, выберите **Добавить группу управления**, введите нужные сведения и нажмите кнопку **сохранить**.

    :::image type="content" source="media/security-center-management-groups/add-management-group.png" alt-text="Добавление группы управления в Azure":::

    - **Идентификатор группы управления** — уникальный идентификатор каталога, используемый для отправки команд в этой группе управления. Идентификатор не редактируется после ее создания, так как с его помощью эта группа идентифицируется во всей системе Azure. 
    - Поле отображаемого имени — это имя, которое отображается во всех разделах портала Azure. Отдельное отображаемое имя можно указать в необязательном поле при создании группы управления и изменить в любой момент.  


### <a name="add-subscriptions-to-a-management-group"></a>Добавление подписок в группу управления
В созданную группу управления можно добавить подписки.

1. В разделе **группы управления** выберите группу управления для своей подписки.

    :::image type="content" source="./media/security-center-management-groups/management-group-subscriptions.png" alt-text="Выберите группу управления для своей подписки":::

1. Когда откроется страница группы, выберите **сведения** .

    :::image type="content" source="./media/security-center-management-groups/management-group-details-page.png" alt-text="Открытие страницы сведений о группе управления":::

1. На странице сведения о группе выберите **Добавить подписку**, затем выберите подписки и нажмите кнопку **сохранить**. Повторяйте до тех пор, пока не будут добавлены все подписки в области.

    :::image type="content" source="./media/security-center-management-groups/management-group-add-subscriptions.png" alt-text="Добавление подписки в группу управления":::
   > [!IMPORTANT]
   > Группы управления могут содержать подписки и дочерние группы управления. При назначении пользователю роли Azure родительской группе управления доступ наследуется подписками дочерней группы управления. То же самое касается и политик. 


## <a name="grant-tenant-wide-permissions-to-yourself"></a>Предоставьте себе разрешения для всего клиента

Пользователь с ролью Azure Active Directory **глобального администратора** может иметь обязанности в масштабах всего клиента, но не имеет разрешений Azure на просмотр сведений о всей Организации в центре безопасности Azure. 

> [!TIP]
> Если ваша организация управляет доступом к ресурсам с помощью [Azure AD privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-configure.md)или любого другого средства PIM, для пользователя, выполняющего эти изменения, должна быть активна роль глобального администратора.

Чтобы назначить себе разрешения уровня клиента:

1. Как пользователь глобального администратора без назначения корневой группы управления клиента, откройте страницу **Обзор** центра безопасности и выберите в баннере ссылку «видимость для **всего клиента** ». 

    :::image type="content" source="media/security-center-management-groups/enable-tenant-level-permissions-banner.png" alt-text="Включение разрешений уровня клиента в центре безопасности Azure":::

1. Выберите новую роль Azure, которая будет назначена. 

    :::image type="content" source="media/security-center-management-groups/enable-tenant-level-permissions-form.png" alt-text="Форма для определения разрешений уровня клиента, назначаемых пользователю":::

    > [!TIP]
    > Как правило, роль "Администратор безопасности" требуется для применения политик на корневом уровне, в то время как для обеспечения видимости на уровне клиента будет достаточно роли читателя сведений о безопасности. Дополнительные сведения о разрешениях, предоставляемых этими ролями, см. в разделе [Встроенные роли в Azure. Администратор безопасности](../role-based-access-control/built-in-roles.md#security-admin) или [Встроенные роли в Azure. Читатель сведений о безопасности](../role-based-access-control/built-in-roles.md#security-reader).
    >
    > Различия между этими ролями, характерными для центра безопасности, см. в таблице в разделе [roles and Allowed Actions](security-center-permissions.md#roles-and-allowed-actions).

    Представление в масштабе всей Организации достигается путем предоставления ролей на уровне корневой группы управления клиента.  

1. Выйдите из портал Azure и снова войдите в систему.

1. После получения повышенных прав доступа откройте или обновите центр безопасности Azure, чтобы проверить, можете ли вы отслеживать все подписки в своем клиенте Azure AD. 

## <a name="assign-azure-roles-to-other-users"></a>Назначение ролей Azure другим пользователям

### <a name="assign-azure-roles-to-users-through-the-azure-portal"></a>Назначьте роли Azure пользователям с помощью портал Azure: 
1. Войдите на [портал Azure](https://portal.azure.com). 
1. Чтобы просмотреть группы управления, в главном меню Azure выберите **Все службы**, а затем выберите **Группы управления**.
1.  Выберите группу управления и щелкните **сведения**.

    :::image type="content" source="./media/security-center-management-groups/management-group-details.PNG" alt-text="Снимок экрана сведений группы управления":::

1. Выберите **Управление доступом (IAM)** , а затем **назначения ролей**.
1. Выберите **Добавить назначение ролей**.
1. Выберите роль для назначения, а затем щелкните **сохранить**.  
   
   ![Добавление снимка экрана роли читателя сведений о безопасности](./media/security-center-management-groups/asc-security-reader.png)

### <a name="assign-azure-roles-to-users-with-powershell"></a>Назначение ролей Azure пользователям с помощью PowerShell: 
1. Установите [Azure PowerShell](/powershell/azure/install-az-ps).
2. Выполните следующие команды: 

    ```azurepowershell
    # Login to Azure as a Global Administrator user
    Connect-AzAccount
    ```

3. При появлении запроса войдите, используя учетные данные глобального администратора. 

    ![Снимок экрана запроса на вход](./media/security-center-management-groups/azurerm-sign-in.PNG)

4. Предоставьте разрешения роли читателя, выполнив следующую команду:

    ```azurepowershell
    # Add Reader role to the required user on the Root Management Group
    # Replace "user@domian.com” with the user to grant access to
    New-AzRoleAssignment -SignInName "user@domain.com" -RoleDefinitionName "Reader" -Scope "/"
    ```
5. Чтобы удалить роль, используйте следующую команду: 

    ```azurepowershell
    Remove-AzRoleAssignment -SignInName "user@domain.com" -RoleDefinitionName "Reader" -Scope "/" 
    ```

## <a name="remove-elevated-access"></a>Удаление повышенного права доступа 
После назначения ролей Azure пользователям администратор клиента должен удалить себя из роли администратора доступа пользователей.

1. Войдите на [портал Azure](https://portal.azure.com) или [центр администрирования Azure Active Directory](https://aad.portal.azure.com).

2. В списке переходов выберите **Azure Active Directory** и щелкните **свойства**.

3. В разделе **Управление доступом для ресурсов Azure** присвойте параметру значение **нет**.

4. Чтобы сохранить параметры, нажмите кнопку **сохранить**.



## <a name="next-steps"></a>Дальнейшие действия
Работая с этой статьей, вы узнали, как получить видимость в пределах клиента в центре безопасности Azure. Связанные сведения:

- [Разрешения в центре безопасности Azure](security-center-permissions.md)