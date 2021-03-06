---
title: Настройка ключа безопасности в качестве метода проверки — Azure AD
description: Настройка страницы сведений о безопасности (предварительная версия) для проверки личности с помощью ключа безопасности Fast Identity Online (FIDO2) в качестве метода проверки.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: librown
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 07/18/2019
ms.author: curtand
ms.openlocfilehash: c056e439deac71417ff14dcfc3f2c3c95db41946
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "88797672"
---
# <a name="set-up-a-security-key-as-your-verification-method"></a>Настройка ключа безопасности в качестве метода проверки

Ключи безопасности можно использовать в качестве метода входа без пароля в вашей организации. Ключ безопасности — это физическое устройство, которое используется с уникальным ПИН-кодом для входа в рабочую или учебную учетную запись. Так как для ключей безопасности требуется физическое устройство и что-то известное только вам, оно считается более надежным методом проверки по сравнению с именем пользователя и паролем.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-key.md)]

>[!Note]
> Если вы не видите варианта ключа безопасности, возможно, ваша организация не позволяет использовать этот вариант для проверки. Если это так, вам нужно выбрать другой метод или обратиться за помощью в службу поддержки своей организации.

## <a name="security-verification-versus-password-reset-authentication"></a>Сравнение проверки безопасности и аутентификации для сброса пароля

Методы проверки сведений для защиты используются как при двухфакторной проверке безопасности, так и при сбросе пароля. При этом не все методы можно использовать для обоих видов аутентификации.

| Метод | Используется для |
| ------ | -------- |
| Приложение Authenticator | Двухфакторная проверка и аутентификация для сброса пароля. |
| Текстовые сообщения | Двухфакторная проверка и аутентификация для сброса пароля. |
| Телефонные звонки | Двухфакторная проверка и аутентификация для сброса пароля. |
| Ключ безопасности | Двухфакторная проверка и аутентификация для сброса пароля. |
| Учетная запись электронной почты | Только аутентификация для сброса пароля. Для двухфакторной проверки необходимо выбрать другой метод. |
| Контрольные вопросы | Только аутентификация для сброса пароля. Для двухфакторной проверки необходимо выбрать другой метод. |

## <a name="what-is-a-security-key"></a>Сведения о ключе безопасности

В настоящее время поддерживается несколько видов и поставщиков ключей безопасности с помощью протоколов проверки подлинности без пароля [Fast Identity Online (FIDO)](https://fidoalliance.org/fido2/) (FIDO2). Эти ключи позволяют входить в рабочую или учебную учетную запись для доступа к облачным ресурсам организации на поддерживаемом устройстве и в веб-браузере.

Администратор или ваша организация предоставит вам ключ безопасности, если он требуется для вашей рабочей или учебной учетной записи. Существуют различные типы ключей безопасности, например USB-ключ, который вы подключаете к устройству, или NFC-ключ, который вы кладете на устройство для чтения NFC. Дополнительные сведения о ключе безопасности, включая его тип, см. в документации производителя.

> [!Note]
> Если вы не можете использовать ключ безопасности FIDO2, можно использовать другие методы проверки без пароля, например приложение Microsoft Authenticator или Windows Hello. Чтобы получить дополнительные сведения о приложении Microsoft Authenticator, ознакомьтесь со статьей [Что такое приложение Microsoft Authenticator?](user-help-auth-app-overview.md). Дополнительные сведения о Windows Hello см. в разделе [Обзор Windows Hello](https://www.microsoft.com/windows/windows-hello).

## <a name="before-you-begin"></a>Перед началом

Чтобы зарегистрировать ключ безопасности, должны выполняться следующие условия.

- Администратор включил эту функцию для использования в вашей организации.

- Вы работаете на устройстве под Windows 10, обновление 2019 и используете поддерживаемый браузер.

- У вас есть физический ключ безопасности, одобренный администратором или вашей организацией. Ключ безопасности должен быть совместим с FIDO2 и Майкрософт. Если у вас возникнут вопросы о ключе безопасности, является ли он совместимым, обратитесь в службу технической поддержки своей организации.

## <a name="register-your-security-key"></a>Регистрация ключа безопасности

Чтобы войти в рабочую или учебную учетную запись с помощью ключа, необходимо создать свой ключ безопасности и присвоить ему уникальный ПИН-код. У вас может быть до 10 ключей, зарегистрированных в вашей учетной записи. 

1. Перейдите на страницу **Мой профиль** на https://myaccount.microsoft.com и выполните вход, если вы еще этого не сделали.

2. Выберите **Сведения о безопасности**, **Добавить метод**, а затем выберите **Ключ безопасности** из раскрывающегося списка **Добавить метод**.

    ![Поле "Добавить метод" с выбранным ключом безопасности](media/security-info/security-info-security-key-add-method.png)

3. Выберите **Добавить**, а затем выберите тип ключа безопасности **USB-устройство** или **NFC-устройство**.

    ![Выберите тип ключа безопасности (USB или NFC)](media/security-info/security-info-security-key-choose-type.png)

    > [!Note]
    > Если вы не знаете, какой тип ключа безопасности вы используете, обратитесь к документации производителя. Если вы не знаете точное имя производителя, обратитесь за помощью в службу технической поддержки организации.

4. Убедитесь, что ключ безопасности физически доступен, а затем в поле **Ключ безопасности** выберите **Далее**.

    ![Поле регистрации начала работы ключа безопасности](media/security-info/security-info-security-key-start-setup.png)

    Появится новое поле, которое поможет настроить новый метод входа.

5. В **Настройке нового метода входа** выберите **Далее**, а затем:

    - Если ключ безопасности является USB-устройством, вставьте ключ безопасности в USB-порт устройства.

    - Если ваш ключ безопасности является NFC-устройством, коснитесь ключом безопасности считывающего устройства.

6. Введите уникальный PIN-код ключа безопасности в поле **Безопасность Windows**, а затем выберите **ОК**.

    Вы вернетесь в поле **Настройки нового метода входа**.

7. Выберите **Далее**.

8. Вернитесь на страницу **Сведения о безопасности**, введите имя для нового ключа безопасности, а затем выберите **Далее**.

    ![Страница сведений о безопасности, выбор имени ключа безопасности](media/security-info/security-info-security-key-name.png)

    Ваш ключ безопасности зарегистрирован, и его можно использовать для входа в рабочую или учебную учетную запись.

9. Нажмите кнопку **Готово** , чтобы закрыть поле **Ключ безопасности**.

    На странице **Сведения о безопасности** обновляется информация о ключах безопасности.

    ![Страница "Сведения о безопасности" со всеми зарегистрированными методами](media/security-info/security-info-security-key-configured.png)

## <a name="delete-a-security-key-from-your-security-info"></a>Удаление ключа безопасности из сведений о безопасности

Если вы неправильно разместили или не хотите использовать ключ безопасности, вы можете удалить ключ из сведений о безопасности. Несмотря на то что ключ безопасности не будет использоваться с рабочей или учебной учетной записью, ключ безопасности продолжит хранить данные и учетные данные. Чтобы удалить данные и учетные данные из самого ключа безопасности, следуйте инструкциям в разделе [Сброс ключа безопасности, совместимого с Майкрософт](#reset-your-security-key) этой статьи.

1. Выберите ссылку **Удалить** для ключа безопасности, который нужно удалить.

2. Выберите **ОК** в поле **Удалить ключ безопасности**.

    Ваш ключ безопасности удален, и вы больше не сможете использовать его для входа в рабочую или учебную учетную запись.

>[!Important]
>Если ключ безопасности удален по ошибке, его можно зарегистрировать снова, следуя инструкциям в разделе [Регистрация ключа безопасности](#register-your-security-key) этой статьи.

## <a name="manage-your-security-key-settings-from-windows-settings"></a>Управление параметрами ключа безопасности из Параметров Windows

Вы можете управлять параметрами ключа безопасности из приложения **Параметры Windows**, включая сброс ключа безопасности и создание нового PIN-кода ключа безопасности.

### <a name="reset-your-security-key"></a>Сброс ключа безопасности

Если вы хотите удалить все данные учетной записи, хранящиеся на физическом ключе безопасности, необходимо вернуть ключ к заводским значениям по умолчанию. Сброс ключа безопасности удаляет все из ключа, что позволяет начать заново.

>[!IMPORTANT]
>Сброс ключа безопасности удаляет все из ключа, что позволяет вернуться к заводским значениям по умолчанию.
>
> **Все данные и учетные данные будут удалены.**

#### <a name="to-reset-your-security-key"></a>Сброс ключа безопасности

1. Откройте приложение "Параметры Windows", выберите **Учетные записи**, выберите **Параметры входа**, выберите **Ключ безопасности**, а затем выберите **Управление**.

2. Вставьте ключ безопасности в USB-порт или коснитесь считывающего устройства NFC, чтобы подтвердить свою личность.

3. Следуйте инструкциям на экране в зависимости от конкретного производителя ключа безопасности. Если изготовитель ключа не указан в инструкциях на экране, дополнительные сведения см. на сайте изготовителя.

4. Выберите **Закрыть**, чтобы закрыть окно **Управление**.

### <a name="create-a-new-security-key-pin"></a>Создание нового ПИН-кода безопасности

Можно создать новый ПИН-код ключа безопасности для ключа безопасности.

#### <a name="to-create-a-new-security-key-pin"></a>Создание нового ПИН-кода безопасности

1. Откройте приложение "Параметры Windows", выберите **Учетные записи**, выберите **Параметры входа**, выберите **Ключ безопасности**, а затем выберите **Управление**.

2. Вставьте ключ безопасности в USB-порт или коснитесь считывающего устройства NFC, чтобы подтвердить свою личность.
3. Выберите **Добавить** в области **ПИН-код ключа безопасности**, введите и подтвердите новый ПИН-код ключа безопасности, а затем выберите **ОК**.

     Ключ безопасности обновляется новым ПИН-кодом ключа безопасности для использования с рабочей или учебной учетной записью. Если вы решили изменить ПИН-код еще раз, можно нажать кнопку **Изменить**.
4. Выберите **Закрыть**, чтобы закрыть окно **Управление**.

## <a name="additional-security-info-methods"></a>Дополнительные методы в сведениях для защиты

Чтобы зарегистрировать ключ безопасности, необходимо зарегистрировать по крайней мере один дополнительный метод проверки безопасности. Дополнительные сведения см. на странице [Раздел "Обзор"](./security-info-setup-auth-app.md). 

## <a name="next-steps"></a>Дальнейшие действия

- Дополнительные сведения о методах проверки без пароля см. в статье блога [Microsoft Azure AD начинает публичный просмотр предварительной версии ключей безопасности FIDO2 для входа в систему без пароля](https://www.onmsft.com/news/microsofts-azure-ad-begins-public-preview-of-fido2-security-keys-enabling-passwordless-logins) или прочтите статьи [Что такое приложение Microsoft Authenticator?](user-help-auth-app-overview.md) и [Обзор Windows Hello](https://www.microsoft.com/windows/windows-hello).

- Для получения более подробных сведений о [Безопасных ключах, совместимых с Майкрософт](/windows/security/identity-protection/hello-for-business/microsoft-compatible-security-key).

- Если вы забыли свой пароль, сбросьте его на [портале сброса пароля](https://passwordreset.microsoftonline.com/) или выполните действия, описанные в статье о [сбросе пароля рабочей или учебной учетной записи](active-directory-passwords-update-your-own-password.md).

- Советы по устранению неполадок и справку по проблемам со входом в систему см. в статье [Не удается войти в учетную запись Майкрософт](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant).