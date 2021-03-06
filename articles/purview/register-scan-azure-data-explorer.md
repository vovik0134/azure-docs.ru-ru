---
title: Проверка обозреватель данных Azure
description: В этом разделе приведены сведения о проверке обозреватель данных Azure.
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 10/9/2020
ms.openlocfilehash: 01a1ded570d20d175b5e8eadb3e6cc8556155a85
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/03/2020
ms.locfileid: "96554965"
---
# <a name="register-and-scan-azure-data-explorer"></a>Регистрация и проверка обозреватель данных Azure

В этой статье описано, как зарегистрировать учетную запись Azure обозреватель данных в Azure зрения и настроить проверку.

## <a name="supported-capabilities"></a>Поддерживаемые возможности

Azure обозреватель данных поддерживает полные и добавочные Просмотры для записи метаданных и схемы. Проверки также классифицируют данные автоматически на основе системных и настраиваемых правил классификации.

## <a name="prerequisites"></a>Предварительные требования

- Перед регистрацией источников данных создайте учетную запись Azure зрения. Дополнительные сведения о создании учетной записи зрения см. в разделе [Краткое руководство. Создание учетной записи Azure зрения](create-catalog-portal.md).
- Необходимо быть администратором источника данных Azure зрения.

## <a name="setting-up-authentication-for-a-scan"></a>Настройка проверки подлинности при проверке

Существует только один способ настройки проверки подлинности для обозревателя данных Azure.

- Субъект-служба

### <a name="service-principal"></a>Субъект-служба

Чтобы использовать проверку подлинности субъекта-службы для проверок, можно использовать существующую или создать новую. 

> [!Note]
> Если необходимо создать субъект-службу, выполните следующие действия.
> 1. Перейдите на [портал Azure](https://portal.azure.com).
> 1. Выберите **Azure Active Directory** в меню слева.
> 1. Щелкните **Регистрация приложений**.
> 1. Выберите **Регистрация нового приложения**.
> 1. Введите имя **приложения** (имя субъекта-службы).
> 1. Выберите **учетные записи только в этом каталоге Организации**.
> 1. Для URI перенаправления выберите **Интернет** и введите любой URL-адрес. Он не должен быть реальным или работать.
> 1. Нажмите кнопку **Зарегистрировать**.

Необходимо получить идентификатор и секрет приложения субъекта-службы:

1. Перейдите к субъекту-службе в [портал Azure](https://portal.azure.com)
1. Скопируйте значения **идентификатора приложения (клиента)** из **обзора** и **секрета клиента** из **сертификатов & секреты**.
1. Перейдите в хранилище ключей.
1. Выберите **параметры > секреты**
1. Выберите **+ создать/импортировать** и введите **имя** и **значение** в качестве **секрета клиента** от субъекта-службы.
1. Выберите **создать** для завершения
1. Если хранилище ключей еще не подключено к зрения, вам потребуется [создать новое подключение к хранилищу ключей](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account) .
1. Наконец, [Создайте новые учетные данные](manage-credentials.md#create-a-new-credential) с помощью субъекта-службы для настройки проверки.

#### <a name="granting-the-service-principal-access-to-your-azure-data-explorer-instance"></a>Предоставление субъекту-службе доступа к экземпляру обозревателя данных Azure

1. Перейдите на портал Azure. Затем перейдите к экземпляру обозревателя данных Azure.

1. Добавьте субъект-службу в роль **аллдатабасесвиевер** на вкладке **разрешения** , как показано на следующем снимке экрана.

    :::image type="content" source="./media/register-scan-azure-data-explorer/permissions-auth.png" alt-text="Снимок экрана: Добавление субъекта-службы в разрешения" border="true":::

## <a name="register-an-azure-data-explorer-account"></a>Регистрация учетной записи обозреватель данных Azure

Чтобы зарегистрировать новую учетную запись Azure обозреватель данных (Kusto) в каталоге данных, выполните следующие действия.

1. Перейдите к своей учетной записи зрения
1. Выбор **источников** в левой области навигации
1. Нажмите кнопку **Зарегистрировать**.
1. В меню **Регистрация источников** выберите **Azure обозреватель данных**
1. Выберите **Продолжить**

:::image type="content" source="media/register-scan-azure-data-explorer/register-new-data-source.png" alt-text="зарегистрировать новый источник данных" border="true":::

На экране **Регистрация источников (Azure обозреватель данных (Kusto))** выполните следующие действия.

1. Введите **имя** , в котором будет указан источник данных в каталоге.
1. Выберите способ указания нужной учетной записи хранения:
   1. Выберите **из подписки Azure**, в раскрывающемся списке **Подписка Azure** выберите подходящую подписку и соответствующий кластер из раскрывающегося списка **кластер** .
   1. Можно также нажать клавишу **Ввод вручную** и ввести конечную точку службы (URL-адрес).
1. **Готово** для регистрации источника данных.

:::image type="content" source="media/register-scan-azure-data-explorer/register-sources.png" alt-text="Параметры регистрации источников" border="true":::

[!INCLUDE [create and manage scans](includes/manage-scans.md)]

## <a name="next-steps"></a>Дальнейшие шаги

- [Обзор каталога данных Azure зрения](how-to-browse-catalog.md)
- [Поиск в каталоге данных Azure зрения](how-to-search-catalog.md)
