---
title: Защита доменных служб Azure AD | Документация Майкрософт
description: Узнайте, как отключить слабые шифры, старые протоколы и синхронизацию хэша паролей NTLM для управляемого домена доменных служб Azure Active Directory.
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 6b4665b5-4324-42ab-82c5-d36c01192c2a
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: a89c898e150facc9860d86e18a7acc42f5e0f441
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/05/2020
ms.locfileid: "96618864"
---
# <a name="disable-weak-ciphers-and-password-hash-synchronization-to-secure-an-azure-active-directory-domain-services-managed-domain"></a>Отключение слабых шифров и синхронизации хэшей паролей для обеспечения безопасности управляемого домена доменных служб Azure Active Directory

По умолчанию доменные службы Azure Active Directory (Azure AD DS) позволяют использовать такие шифры, как NTLM v1 и TLS V1. Эти шифры могут потребоваться для некоторых устаревших приложений, но считаются ненадежными и могут быть отключены, если они вам не нужны. При наличии локального гибридного подключения с помощью Azure AD Connect можно также отключить синхронизацию хэшей паролей NTLM.

В этой статье показано, как отключить шифры NTLM v1 и TLS V1 и отключить синхронизацию хэша паролей NTLM.

## <a name="prerequisites"></a>Предварительные требования

Для работы с этой статьей требуются следующие ресурсы:

* Активная подписка Azure.
    * Если у вас еще нет подписки Azure, [создайте учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Связанный с вашей подпиской клиент Azure Active Directory, синхронизированный с локальным или облачным каталогом.
    * Если потребуется, [создайте клиент Azure Active Directory][create-azure-ad-tenant] или [свяжите подписку Azure со своей учетной записью][associate-azure-ad-tenant].
* Управляемый домен доменных служб Azure Active Directory, включенный и настроенный в клиенте Azure AD.
    * При необходимости [создайте и настройте управляемый домен доменных служб Azure Active Directory][create-azure-ad-ds-instance].
* Установите и настройте Azure PowerShell.
    * При необходимости выполните инструкции по [установке модуля Azure PowerShell и его подключению к подписке Azure](/powershell/azure/install-az-ps).
    * Войдите в подписку Azure с помощью командлета [Connect-AzAccount][Connect-AzAccount].
* Установите и настройте Azure AD PowerShell.
    * При необходимости выполните инструкции по [установке модуля Azure AD PowerShell и подключению к Azure AD](/powershell/azure/active-directory/install-adv2).
    * Войдите в клиент Azure AD с помощью командлета [Connect-AzureAD][Connect-AzureAD].

## <a name="disable-weak-ciphers-and-ntlm-password-hash-sync"></a>Отключение слабых шифров и синхронизация хэша паролей NTLM

Чтобы отключить слабые комплекты шифров и синхронизацию хэша учетных данных NTLM, войдите в свою учетную запись Azure, а затем получите ресурс Azure AD DS с помощью командлета [Get-азресаурце][Get-AzResource] :

> [!TIP]
> При получении сообщения об ошибке с помощью команды [Get-азресаурце][Get-AzResource] , которая не существует в ресурсе *Microsoft. AAD/DomainServices* , [следует повысить уровень доступа, чтобы управлять всеми подписками и группами управления Azure][global-admin].

```powershell
Login-AzAccount

$DomainServicesResource = Get-AzResource -ResourceType "Microsoft.AAD/DomainServices"
```

Затем определите *домаинсекуритисеттингс* , чтобы настроить следующие параметры безопасности.

1. Отключите поддержку NTLM v1.
2. отключить синхронизацию хэшей паролей NTLM в локальном экземпляре Active Directory;
3. Отключите TLS V1.

> [!IMPORTANT]
> Пользователи и учетные записи служб не могут выполнять простые привязки LDAP при отключении синхронизации хэша паролей NTLM в управляемом домене Azure AD DS. Если необходимо выполнить простые привязки LDAP, не устанавливайте параметр *"синкнтлмпассвордс" = "Disabled"* (конфигурация безопасности) в следующей команде.

```powershell
$securitySettings = @{"DomainSecuritySettings"=@{"NtlmV1"="Disabled";"SyncNtlmPasswords"="Disabled";"TlsV1"="Disabled"}}
```

Наконец, примените определенные параметры безопасности к управляемому домену с помощью командлета [Set-азресаурце][Set-AzResource] . Укажите ресурс Azure AD DS, начиная с первого шага, и параметры безопасности, описанные на предыдущем шаге.

```powershell
Set-AzResource -Id $DomainServicesResource.ResourceId -Properties $securitySettings -Verbose -Force
```

Применение параметров безопасности к управляемому домену займет несколько секунд.

> [!IMPORTANT]
> После отключения NTLM выполните полную синхронизацию хэша паролей в Azure AD Connect, чтобы удалить все хэши паролей из управляемого домена. Если отключить NTLM, но не выполнять принудительную синхронизацию хэша паролей, хэши паролей NTLM для учетной записи пользователя удаляются только при следующей смене пароля. Такое поведение может позволить пользователю продолжить вход, если у него есть кэшированные учетные данные в системе, где в качестве метода проверки подлинности используется NTLM.
>
> После того как хэш пароля NTLM отличается от хэша пароля Kerberos, откат к NTLM не будет работать. Кэшированные учетные данные также больше не работают, если виртуальная машина подключена к управляемому контроллеру домена.  

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о процессе синхронизации см. в статье [как синхронизировать объекты и учетные данные в управляемом домене][synchronization].

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[global-admin]: ../role-based-access-control/elevate-access-global-admin.md
[synchronization]: synchronization.md

<!-- EXTERNAL LINKS -->
[Get-AzResource]: /powershell/module/az.resources/Get-AzResource
[Set-AzResource]: /powershell/module/Az.Resources/Set-AzResource
[Connect-AzAccount]: /powershell/module/Az.Accounts/Connect-AzAccount
[Connect-AzureAD]: /powershell/module/AzureAD/Connect-AzureAD
