---
title: Добавление или удаление пользователей в Azure Active Directory | Документация Майкрософт
description: Инструкции по добавлению новых или удалению существующих пользователей с помощью Azure Active Directory.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 11/12/2019
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: de529a8ffb0c72854904717c71dbc322c919f1a8
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/25/2020
ms.locfileid: "95996701"
---
# <a name="add-or-delete-users-using-azure-active-directory"></a>Добавление или удаление пользователей с помощью Azure Active Directory

Добавление новых пользователей или удаление существующих пользователей из Организации Azure Active Directory (Azure AD). Для добавления или удаления пользователей необходимо быть администратором или глобальным администратором.

## <a name="add-a-new-user"></a>Добавление нового пользователя

Вы можете создать пользователя с помощью портала Azure Active Directory.

Чтобы добавить нового пользователя, выполните следующие действия.

1. Войдите в [портал Azure](https://portal.azure.com/) в качестве администратора пользователя для Организации.

1. Найдите и выберите *Azure Active Directory* на любой странице.

1. Выберите **Пользователи**, а затем щелкните **Новый пользователь**.

    ![Добавление пользователя с помощью пользователей — все пользователи в Azure AD](media/add-users-azure-active-directory/add-user-in-users-all-users.png)

1. На странице **пользователь** введите сведения для этого пользователя:

   - **Имя**. Обязательный. Имя и фамилия нового пользователя. Например, *Мэри Parker*.

   - **Имя пользователя**. Обязательный. Имя нового пользователя. Например, `mary@contoso.com`.

     Доменная часть имени пользователя должна использовать начальное имя домена по умолчанию, *\<yourdomainname> . onmicrosoft.com* или имя пользовательского домена, например *contoso.com*. Дополнительные сведения о создании пользовательского доменного имени см. [в статье Добавление имени личного домена с помощью портала Azure Active Directory](add-custom-domain.md).

   - **Группы**. При необходимости можно добавить пользователя к одной или нескольким существующим группам. Вы также можете добавить пользователя в группы позже. Дополнительные сведения о добавлении пользователей в группы см. в статьях [Создание базовой группы и добавление членов с помощью Azure Active Directory](active-directory-groups-create-azure-portal.md).

   - **Роль каталога**. Чтобы предоставить пользователю права администратора Azure AD, ему можно присвоить роль Azure AD. Вы можете назначить пользователя глобальным администратором или одной или несколькими ограниченными ролями администратора в Azure AD. Дополнительные сведения о назначении ролей см. в разделе [Назначение ролей пользователям](active-directory-users-assign-role-azure-portal.md).

   - **Сведения о задании**: вы можете добавить дополнительные сведения о пользователе или сделать это позже. Дополнительные сведения о добавлении информации о пользователе см. в разделе [How to add or change user profile information](active-directory-users-profile-azure-portal.md) (Как добавлять и изменять сведения в профиле пользователя).

1. Скопируйте автоматически сформированный пароль, указанный в поле **пароль** . Для первого входа вам потребуется предоставить этот пароль пользователю.

1. Выберите **Создать**.

Пользователь будет создан и добавлен в вашу организацию Azure AD.

## <a name="add-a-new-guest-user"></a>Добавление нового гостевого пользователя

Вы также можете пригласить нового гостевого пользователя для совместной работы с вашей организацией, выбрав **пригласить пользователя** на **новой странице пользователя** . Если параметры внешнего взаимодействия вашей организации настроены таким образом, что вы можете приглашать гостей, пользователь будет отправлять по электронной почте приглашение, которое необходимо принять, чтобы начать совместную работу. Дополнительные сведения о приглашении пользователей службы совместной работы B2B см. [в статье приглашение пользователей B2B в Azure Active Directory](../external-identities/add-users-administrator.md)

## <a name="add-a-consumer-user"></a>Добавление пользователя-получателя

Могут возникнуть ситуации, в которых необходимо вручную создать учетные записи получателей в каталоге Azure Active Directory B2C (Azure AD B2C). Дополнительные сведения о создании учетных записей потребителей см. [в разделе Создание и удаление пользователей-получателей в Azure AD B2C](../../active-directory-b2c/manage-users-portal.md).

## <a name="add-a-new-user-within-a-hybrid-environment"></a>Добавление нового пользователя в гибридной среде

Если у вас есть среда с Azure Active Directory (облако) и Windows Server Active Directory (локально), можно добавить новых пользователей путем синхронизации существующих данных учетной записи пользователя. Дополнительные сведения о гибридных средах и пользователях см. в статье [Интеграция локальных каталогов с Azure Active Directory](../hybrid/whatis-hybrid-identity.md).

## <a name="delete-a-user"></a>Удаление пользователя

Вы можете удалить существующего пользователя с помощью портала Azure Active Directory.

Чтобы удалить пользователя, выполните следующие действия.

1. Войдите в [портал Azure](https://portal.azure.com/) , используя учетную запись администратора пользователя для Организации.

1. Найдите и выберите *Azure Active Directory* на любой странице.

1. Найдите и выберите пользователя, которого нужно удалить из клиента Azure AD. Например, _Мэри Parker_.

1. Выберите **Удалить пользователя**.

    ![Страница "Пользователи — Все пользователи" с выделенным вариантом "Удалить пользователя"](media/add-users-azure-active-directory/delete-user-all-users-blade.png)

Пользователь удален и больше не отображается на странице **Пользователи — Все пользователи**. Пользователь может отображаться на странице **Удаленные пользователи** в течение 30 дней. В этот период его можно восстановить. Дополнительные сведения о восстановлении пользователя см. [в разделе Восстановление или удаление недавно удаленного пользователя с помощью Azure Active Directory](active-directory-users-restore.md).

При удалении пользователя все лицензии, используемые этим пользователем, становятся доступными для других пользователей.

>[!Note]
>Чтобы обновить удостоверение, контактные данные или сведения о задании для пользователей, источником которых является Windows Server Active Directory, необходимо использовать Windows Server Active Directory. После завершения обновления изменения будут отображаться после следующего цикла синхронизации.

## <a name="next-steps"></a>Следующие шаги

После добавления пользователей можно выполнять следующие основные процессы.

- [Добавление или изменение данных профиля](active-directory-users-profile-azure-portal.md)

- [Назначение ролей пользователям](active-directory-users-assign-role-azure-portal.md)

- [Создание простой группы и добавление участников](active-directory-groups-create-azure-portal.md)

- [Работа с динамическими группами и пользователями](../enterprise-users/groups-create-rule.md)

Кроме того, можно выполнять другие задачи управления пользователями, например [добавлять гостевых пользователей из другого каталога](../external-identities/what-is-b2b.md) или [восстанавливать удаленных пользователей](active-directory-users-restore.md). Дополнительные сведения о других доступных действиях см. в [документации по управлению пользователями Azure Active Directory](../enterprise-users/index.yml).