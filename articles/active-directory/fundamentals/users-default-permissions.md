---
title: Разрешения пользователя по умолчанию в Azure Active Directory | Документация Майкрософт
description: Дополнительные сведения о различных разрешениях пользователя в Azure Active Directory.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 08/17/2020
ms.author: ajburnle
ms.reviewer: vincesm
ms.custom: it-pro, seodec18, contperf-fy21q1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2c556940ace02f2e7bf0354e67d32df7c5c6714e
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/10/2020
ms.locfileid: "97032768"
---
# <a name="what-are-the-default-user-permissions-in-azure-active-directory"></a>Разрешения пользователя по умолчанию в Azure Active Directory
В Azure Active Directory (Azure AD) набор разрешений по умолчанию предоставляется всем пользователям. Доступ пользователей зависит от типа пользователя, [назначений ролей](active-directory-users-assign-role-azure-portal.md) и владения отдельными объектами. В этой статье описываются эти разрешения по умолчанию и содержится сравнение значений по умолчанию для участников и гостевых пользователей. Разрешения пользователя по умолчанию можно изменить только в параметрах пользователя в Azure AD.

## <a name="member-and-guest-users"></a>Участники и гостевые пользователи
Набор полученных разрешений по умолчанию зависит от того, является ли пользователь собственным членом клиента (пользователь-участник) или пользователь перенесен из другого каталога в качестве гостя (пользователь-гость) в рамках совместной работы B2B. Дополнительные сведения о добавлении гостевых пользователей см. в статье [Что представляет собой гостевой доступ в службе совместной работы Azure Active Directory B2B?](../external-identities/what-is-b2b.md).
* Пользователи-участники могут регистрировать приложения, управлять фотографией собственного профиля и мобильным номером, изменять пароль и приглашать гостей B2B. Кроме того, пользователи могут считывать все данные каталога (с некоторыми исключениями). 
* Гостевые пользователи имеют ограниченные разрешения каталога. Они могут управлять собственным профилем, изменять свой пароль и получать некоторые сведения о других пользователях, группах и приложениях, однако они не могут читать все данные каталога. Например, гостевые пользователи не могут перечислить список всех пользователей, групп и других объектов каталога. Гостей можно добавить в роли администраторов, которые предоставят им полные разрешения на чтение и запись, содержащиеся в роли. Гости также могут приглашать других гостей.

## <a name="compare-member-and-guest-default-permissions"></a>Сравнение стандартных разрешений участника и гостя

**Область** | **Разрешения пользователя-участника** | **Разрешения гостевого пользователя по умолчанию** | **Ограниченные разрешения гостевого пользователя (Предварительная версия)**
------------ | --------- | ---------- | ----------
Пользователи и контакты | <ul><li>Перечисление списка всех пользователей и контактов<li>Чтение всех открытых свойств пользователей и контактов</li><li>Приглашение гостей<li>Изменение пароля<li>Управление собственным номером мобильного телефона<li>Управление собственными фотографиями<li>Аннуляция собственных токенов обновления</li></ul> | <ul><li>Чтение собственных свойств<li>Чтение отображаемого имени, адреса электронной почты, имени входа, фотографии, имени участника-пользователя и свойств типа пользователя для других пользователей и контактов<li>Изменение пароля<li>Поиск другого пользователя по ObjectId (если разрешено)<li>Диспетчер чтения и сведения о подчиненных других пользователях</li></ul> | <ul><li>Чтение собственных свойств<li>Изменение пароля</li></ul>
Группы | <ul><li>Создание групп безопасности<li>Создание групп Microsoft 365<li>Перечислить список всех групп<li>Чтение всех свойств групп<li>Чтение не скрытого членства в группах<li>Чтение скрытого членства в группах Microsoft 365 для присоединенной группы<li>Управление свойствами, правами владения и членством в группах, которые принадлежат пользователю<li>Добавление гостей в собственные группы<li>Управление параметрами динамического членства<li>Удаление собственных групп<li>Восстановление собственных групп Microsoft 365</li></ul> | <ul><li>Чтение свойств нескрытых групп, включая членство и владение (даже не присоединенные к группам)<li>Чтение скрытых Microsoft 365 членства в группах для Объединенных групп<li>Поиск групп по отображаемому имени или ObjectId (если разрешено)</li></ul> | <ul><li>Чтение идентификатора объекта для Объединенных групп<li>Чтение членства и владение объединенными группами в некоторых Microsoft 365 приложениях (если разрешено)</li></ul>
приложения; | <ul><li>Регистрация (создание) приложения<li>Перечислить список всех приложений<li>Чтение свойств зарегистрированных и корпоративных приложений<li>Управление свойствами приложения, назначениями и учетными данными собственных приложений<li>Создание или удаление пароля приложения для пользователя<li>Удаление собственных приложений<li>Восстановление собственных приложений</li></ul> | <ul><li>Чтение свойств зарегистрированных и корпоративных приложений</li></ul> | <ul><li>Чтение свойств зарегистрированных и корпоративных приложений
Устройства</li></ul> | <ul><li>Перечисление списка всех устройств<li>Чтение всех свойств устройств<li>Управление всеми свойствами собственных устройств</li></ul> | Нет разрешений | Нет разрешений
Каталог | <ul><li>Чтение всей информации о компании<li>Чтение всех доменов<li>Чтение всех контрактов партнера</li></ul> | <ul><li>Прочитать отображаемое имя компании<li>Чтение всех доменов</li></ul> | <ul><li>Прочитать отображаемое имя компании<li>Чтение всех доменов</li></ul>
Роли и области | <ul><li>Чтение всех административных ролей и членств<li>Чтение всех свойств и членств административных единиц</li></ul> | Нет разрешений | Нет разрешений
Подписки | <ul><li>Чтение всех подписок<li>Включение участника плана обслуживания</li></ul> | Нет разрешений | Нет разрешений
Политики | <ul><li>Чтение всех свойств политик<li>Управление всеми свойствами собственной политики</li></ul> | Нет разрешений | Нет разрешений

## <a name="restrict-member-users-default-permissions"></a>Ограничить разрешения пользователей по умолчанию для участников 

Разрешения по умолчанию для пользователей участников могут быть ограничены следующими способами.

Разрешение | Описание параметра
---------- | ------------
Пользователи могут регистрировать приложения | Если установить для этого параметра значение "нет", пользователи не смогут создавать регистрации приложений. Затем эту возможность можно предоставить отдельным пользователям, добавив их в роль разработчика приложения.
Разрешить пользователям подключаться к рабочей или учебной учетной записи с помощью LinkedIn | Если установить для этого параметра значение "нет", пользователи не смогут подключать свою рабочую или учебную учетную запись к учетной записи LinkedIn. Дополнительные сведения см. в статье [Подключение к учетной записи LinkedIn общий доступ к данным и согласие](../enterprise-users/linkedin-user-consent.md).
Возможность создания групп безопасности | Если установить для этого параметра значение "Нет", пользователи не смогут создавать группы безопасности. Глобальные администраторы и администраторы пользователей по-прежнему могут создавать группы безопасности. Дополнительные сведения см. в статье [Настройка параметров групп с помощью командлетов Azure Active Directory](../enterprise-users/groups-settings-cmdlets.md).
Возможность создания групп Microsoft 365 | Если установить для этого параметра значение "нет", пользователи не смогут создавать группы Microsoft 365. Установка для этого параметра значения some позволяет выбрать набор пользователей для создания групп Microsoft 365. Глобальные администраторы и администраторы пользователей по-прежнему смогут создавать группы Microsoft 365. Дополнительные сведения см. в статье [Настройка параметров групп с помощью командлетов Azure Active Directory](../enterprise-users/groups-settings-cmdlets.md).
Ограничить доступ к порталу администрирования Azure AD | Если задать для этого параметра значение "нет", пользователи, не являющиеся администраторами, смогут читать ресурсы Azure AD и управлять ими с помощью портала администрирования Azure AD. Да — ограничивать доступ всех данных Azure AD на портале администрирования для всех, не являющихся администраторами.<p>**Примечание**. Этот параметр не ограничивает доступ к данным Azure AD с помощью PowerShell или других клиентов, таких как Visual Studio. Если задано значение "Да", пользователь, не являющийся администратором, может использовать портал администрирования Azure AD для назначения любой административной роли, такой как роль "читатели каталога".<p>Эта роль позволяет считывать основные сведения о каталоге, члены которых по умолчанию имеют пользователи (гости и субъекты-службы).
Возможность чтения других пользователей | Этот параметр доступен только в PowerShell. Если задать для этого флага значение $false, все пользователи, не являющиеся администраторами, не смогут считывать сведения о пользователях из каталога. Этот флаг не запрещает чтение сведений о пользователях в других службах Майкрософт, таких как Exchange Online. Этот параметр предназначен для особых обстоятельств, и установка этого флага в $false не рекомендуется.

## <a name="restrict-guest-users-default-permissions"></a>Ограничить разрешения по умолчанию для гостевых пользователей

Разрешения по умолчанию для гостевых пользователей можно ограничить следующими способами.

>[!NOTE]
>Настройка ограничений доступа пользователя "гости" заменила параметр " **разрешения для гостевых пользователей" ограничен** . Рекомендации по использованию этой функции см. [в разделе ограничение разрешений гостевого доступа (Предварительная версия) в Azure Active Directory](../enterprise-users/users-restrict-guest-permissions.md).

Разрешение | Описание параметра
---------- | ------------
Ограничения доступа пользователей гостей (Предварительная версия) | Установка этого параметра для **гостевых пользователей имеет такой же доступ, как и участники** , по умолчанию, предоставляют гостевым пользователям разрешения для всех участников.<p>Установка этого параметра для **доступа к гостевым пользователям ограничена свойствами и членством собственных объектов каталога** . по умолчанию ограничивается гостевой доступ только к собственным профилям пользователей. Доступ к другим пользователям больше не разрешен даже при поиске по имени участника-пользователя, ObjectId или отображаемому имени. Доступ к сведениям о группах, включая членство в группах, также больше не разрешен.<p>**Примечание**. Этот параметр не запрещает доступ к присоединенным группам в некоторых Microsoft 365 службах, таких как Microsoft Teams. Дополнительные сведения см. в статье [гостевой доступ Microsoft Teams](https://docs.microsoft.com/MicrosoftTeams/guest-access) .<p>Гостевые пользователи по-прежнему могут добавляться в роли администратора независимо от этих параметров разрешений.
Гости могут приглашать других пользователей | Установка этого параметра в значение Да позволяет гостям приглашать других гостей. Дополнительные сведения см. в статье [Делегирование приглашений для службы совместной работы B2B](../external-identities/delegate-invitations.md#configure-b2b-external-collaboration-settings) .
Участники могут приглашать других пользователей | Если задать для этого параметра значение Да, участники каталога, не являющиеся администраторами, смогут приглашать гостей. Дополнительные сведения см. в статье [Делегирование приглашений для службы совместной работы B2B](../external-identities/delegate-invitations.md#configure-b2b-external-collaboration-settings) .
Администраторы и пользователи с ролью приглашающего гостей могут приглашать других пользователей | Установка этого параметра в значение Да позволяет администраторам и пользователям в роли "Гостевой участник" приглашать гостей. Если задано значение Да, пользователи в роли "приглашенный участник" смогут приглашать гостей, независимо от того, могут ли участники заприглашены в настройку. Дополнительные сведения см. в статье [Делегирование приглашений для службы совместной работы B2B](../external-identities/delegate-invitations.md#assign-the-guest-inviter-role-to-a-user) .

## <a name="object-ownership"></a>Владелец объекта

### <a name="application-registration-owner-permissions"></a>Разрешения владельца при регистрации приложения
Когда пользователь регистрирует приложение, он автоматически добавляется в качестве владельца приложения. Владелец может управлять метаданными приложения, такими как имя, и разрешениями приложения. Он также может управлять конфигурацией конкретного клиента приложения, например конфигурацией единого входа и назначениями пользователей. Владелец также может добавлять или удалять других владельцев. В отличие от глобальных администраторов, владельцы могут управлять только приложениями, которыми они владеют.

### <a name="enterprise-application-owner-permissions"></a>Разрешения владельца корпоративного приложения
Когда пользователь добавляет новое корпоративное приложение, он автоматически добавляется в качестве владельца. Как владелец, они могут управлять конфигурацией конкретного клиента приложения, например конфигурацией единого входа, подготовкой и назначениями пользователей. Владелец также может добавлять или удалять других владельцев. В отличие от глобальных администраторов, владельцы могут управлять только вашими приложениями.

### <a name="group-owner-permissions"></a>Разрешения владельца группы
Когда пользователь создает группу, он автоматически добавляется в качестве владельца этой группы. Владелец может управлять свойствами группы, например именем, а также членством группы. Владелец также может добавлять или удалять других владельцев. В отличие от глобальных администраторов и администраторов пользователей, владельцы могут управлять только группами, которыми они владеют. Сведения о назначении владельца группы см. в [этой статье](active-directory-accessmanagement-managing-group-owners.md).

### <a name="ownership-permissions"></a>Права владения
В следующих таблицах описаны определенные разрешения в Azure Active Directory пользователей, имеющих доступ к объектам. Пользователь имеет только эти разрешения для объектов, которыми они владеют.

#### <a name="owned-application-registrations"></a>Регистрация принадлежащих приложений
Пользователи могут выполнять следующие действия с регистрацией принадлежащих приложений.

| **Действия** | **Описание** |
| --- | --- |
| microsoft.directory/applications/audience/update | Изменение свойства applications.audience в Azure Active Directory. |
| microsoft.directory/applications/authentication/update | Изменение свойства applications.authentication в Azure Active Directory. |
| microsoft.directory/applications/basic/update | Изменение базовых свойств приложений в Azure Active Directory. |
| microsoft.directory/applications/credentials/update | Изменение свойства applications.credentials в Azure Active Directory. |
| microsoft.directory/applications/delete | Удаление приложений в Azure Active Directory. |
| microsoft.directory/applications/owners/update | Изменение свойства applications.owners в Azure Active Directory. |
| microsoft.directory/applications/permissions/update | Изменение свойства applications.permissions в Azure Active Directory. |
| microsoft.directory/applications/policies/update | Изменение свойства applications.policies в Azure Active Directory. |
| Microsoft. каталог/приложения/восстановление | Восстановление приложений в Azure Active Directory. |

#### <a name="owned-enterprise-applications"></a>Собственные корпоративные приложения
Пользователи могут выполнять следующие действия в собственных корпоративных приложениях. Корпоративное приложение состоит из субъекта-службы, одной или нескольких политик приложений, а иногда — объекта приложения в том же клиенте, что и субъект-служба.

| **Действия** | **Описание** |
| --- | --- |
| microsoft.directory/auditLogs/allProperties/read | Чтение всех свойств (включая привилегированные) в auditLogs в Azure Active Directory. |
| microsoft.directory/policies/basic/update | Обновление базовых свойств политик в Azure Active Directory. |
| microsoft.directory/policies/delete | Удаление политик в Azure Active Directory. |
| microsoft.directory/policies/owners/update | Изменение свойства policies.owners в Azure Active Directory. |
| microsoft.directory/servicePrincipals/appRoleAssignedTo/update | Изменение свойства servicePrincipals.appRoleAssignedTo в Azure Active Directory. |
| microsoft.directory/servicePrincipals/appRoleAssignments/update | Изменение свойства users.appRoleAssignments в Azure Active Directory. |
| microsoft.directory/servicePrincipals/audience/update | Обновление свойства servicePrincipals.owners в Azure Active Directory. |
| microsoft.directory/servicePrincipals/authentication/update | Обновление свойства servicePrincipals.authentication в Azure Active Directory. |
| microsoft.directory/servicePrincipals/basic/update | Обновление базовых свойств для объектов ServicePrincipal в Azure Active Directory. |
| microsoft.directory/servicePrincipals/credentials/update | Обновление свойства servicePrincipals.credentials в Azure Active Directory. |
| microsoft.directory/servicePrincipals/delete | Удаление объектов servicePrincipal в Azure Active Directory. |
| microsoft.directory/servicePrincipals/owners/update | Изменение свойства servicePrincipals.owners в Azure Active Directory. |
| microsoft.directory/servicePrincipals/permissions/update | Обновление свойства servicePrincipals.permissions в Azure Active Directory. |
| microsoft.directory/servicePrincipals/policies/update | Изменение свойства servicePrincipals.policies в Azure Active Directory. |
| microsoft.directory/signInReports/allProperties/read | Чтение всех свойств (включая привилегированные) в signInReports в Azure Active Directory. |

#### <a name="owned-devices"></a>Принадлежащие устройства
Пользователи могут выполнять следующие действия на собственных устройствах.

| **Действия** | **Описание** |
| --- | --- |
| microsoft.directory/devices/bitLockerRecoveryKeys/read | Чтение свойства devices.bitLockerRecoveryKeys в Azure Active Directory. |
| microsoft.directory/devices/disable | Отключение устройств в Azure Active Directory. |

#### <a name="owned-groups"></a>Собственные группы
Пользователи могут выполнять следующие действия в собственных группах.

| **Действия** | **Описание** |
| --- | --- |
| microsoft.directory/groups/appRoleAssignments/update | Изменение свойства groups.appRoleAssignments в Azure Active Directory. |
| microsoft.directory/groups/basic/update | Обновление базовых свойств в группах в Azure Active Directory. |
| microsoft.directory/groups/delete | Удаление групп в Azure Active Directory. |
| Microsoft. Directory/Groups/dynamicMembershipRule/обновление | Изменение свойства groups.dynamicMembershipRule в Azure Active Directory. |
| microsoft.directory/groups/members/update | Изменение свойства groups.members в Azure Active Directory. |
| microsoft.directory/groups/owners/update | Изменение свойства groups.owners в Azure Active Directory. |
| microsoft.directory/groups/restore | Восстановление групп в Azure Active Directory. |
| microsoft.directory/groups/settings/update | Изменение свойства groups.settings в Azure Active Directory. |

## <a name="next-steps"></a>Дальнейшие действия

* Дополнительные сведения о настройке ограничений доступа пользователей гостей см. в разделе [ограничение разрешений гостевого доступа (Предварительная версия) в Azure Active Directory](../enterprise-users/users-restrict-guest-permissions.md).
* Дополнительные сведения о назначении административных ролей в Azure AD см. в статье [Назначение пользователю ролей администратора в Azure Active Directory](active-directory-users-assign-role-azure-portal.md)
* Дополнительные сведения о том, как осуществляется доступ к ресурсам в Microsoft Azure, см. в статье [Основные сведения о доступе к ресурсам в Azure](../../role-based-access-control/rbac-and-directory-admin-roles.md).
* Дополнительные сведения о связи Azure Active Directory с подпиской Azure см. в статье [Связь между подписками Azure и Azure Active Directory](active-directory-how-subscriptions-associated-directory.md).
* [Управление пользователями](add-users-azure-active-directory.md)
