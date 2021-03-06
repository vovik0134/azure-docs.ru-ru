---
title: Частные конечные точки
description: Изучите процесс создания частных конечных точек для Azure Backup и сценариев, в которых использование частных конечных точек помогает обеспечить безопасность ресурсов.
ms.topic: conceptual
ms.date: 05/07/2020
ms.openlocfilehash: 3ed71e49ebc550cb7bc2041e25aa6b9bde77b1ef
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/17/2020
ms.locfileid: "97629720"
---
# <a name="private-endpoints-for-azure-backup"></a>Частные конечные точки для Azure Backup

Azure Backup позволяет безопасно выполнять резервное копирование и восстановление данных из хранилищ служб восстановления с помощью [частных конечных точек](../private-link/private-endpoint-overview.md). Частные конечные точки используют один или несколько частных IP-адресов из виртуальной сети, что обеспечивает их эффективное подключение к виртуальной сети.

Эта статья поможет вам понять процесс создания частных конечных точек для Azure Backup и сценариев, в которых использование частных конечных точек помогает обеспечить безопасность ресурсов.

## <a name="before-you-start"></a>Перед началом работы

- Частные конечные точки можно создавать только для новых хранилищ служб восстановления (у которых нет элементов, зарегистрированных в хранилище). Поэтому необходимо создать частные конечные точки, прежде чем пытаться защитить любые элементы в хранилище.
- Одна виртуальная сеть может содержать частные конечные точки для нескольких хранилищ служб восстановления. Кроме того, одно хранилище служб восстановления может иметь частные конечные точки в нескольких виртуальных сетях. Однако максимальное количество частных конечных точек, которое можно создать для хранилища, — 12.
- После создания частной конечной точки для хранилища хранилище будет заблокировано. Она не будет доступна (для резервного копирования и восстановления) из сетей, которые содержат закрытую конечную точку для хранилища. Если все частные конечные точки хранилища удалены, хранилище будет доступно из всех сетей.
- При подключении к частной конечной точке для резервного копирования используется всего 11 частных IP-адресов в подсети, включая те, которые используются Azure Backup для хранения. Это число может быть выше (до 25) для некоторых регионов Azure. Поэтому мы рекомендуем использовать достаточно частных IP-адресов при попытке создать частные конечные точки для резервного копирования.
- Хотя хранилище служб восстановления используется (и) Azure Backup и Azure Site Recovery, в этой статье рассматривается использование частных конечных точек только для Azure Backup.
- Azure Active Directory в настоящее время не поддерживает частные конечные точки. Поэтому для Azure Active Directory работы в регионе необходимо разрешить исходящий доступ из защищенной сети при выполнении резервного копирования баз данных на виртуальных машинах Azure и резервного копирования с помощью агента MARS. Вы также можете использовать теги NSG и теги брандмауэра Azure, чтобы разрешить доступ к Azure AD, как применимо.
- Виртуальные сети с политиками сети не поддерживаются для частных конечных точек. Прежде чем продолжить, необходимо отключить политики сети.
- Необходимо повторно зарегистрировать поставщик ресурсов служб восстановления с подпиской, если вы зарегистрировали его до 1 2020 мая. Чтобы повторно зарегистрировать поставщик, перейдите к своей подписке в портал Azure, перейдите к **поставщику ресурсов** на левой панели навигации, а затем выберите **Microsoft. RecoveryServices** и щелкните " **Повторная регистрация**".
- [Восстановление между регионами](backup-create-rs-vault.md#set-cross-region-restore) для SQL и SAP HANA резервных копий баз данных не поддерживается, если в хранилище включены частные конечные точки.

## <a name="recommended-and-supported-scenarios"></a>Рекомендуемые и поддерживаемые сценарии

Хотя частные конечные точки включены для хранилища, они используются для резервного копирования и восстановления рабочих нагрузок SQL и SAP HANA в виртуальной машине Azure и резервного копирования агентов MARS. Кроме того, хранилище можно использовать для резервного копирования других рабочих нагрузок (они не нуждаются в закрытых конечных точках). Помимо резервного копирования рабочих нагрузок SQL и SAP HANA и резервного копирования с помощью агента MARS, закрытые конечные точки также используются для восстановления файлов резервной копии виртуальных машин Azure. Дополнительные сведения приведены в таблице ниже.

| Резервное копирование рабочих нагрузок на виртуальной машине Azure (SQL, SAP HANA), резервное копирование с помощью агента MARS | Использование частных конечных точек рекомендуется для обеспечения резервного копирования и восстановления без необходимости предоставления списка IP-адресов и полных доменных имен для Azure Backup или службы хранилища Azure из виртуальных сетей. |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| **Резервное копирование виртуальных машин Azure**                                         | Для резервного копирования виртуальных машин не требуется разрешать доступ к IP-адресам или полным доменным именам. Поэтому для резервного копирования и восстановления дисков не требуются частные конечные точки.  <br><br>   Однако восстановление файлов из хранилища, содержащего частные конечные точки, будет ограничено виртуальными сетями, содержащими закрытую конечную точку для хранилища. <br><br>    При использовании неуправляемых дисков Акл'ед убедитесь, что учетная запись хранения, содержащая диски, предоставляет доступ к **доверенным службам Майкрософт** , если это акл'ед. |
| **Резервное копирование файлов Azure**                                      | Резервные копии файлов Azure хранятся в локальной учетной записи хранения. Поэтому для резервного копирования и восстановления не требуются частные конечные точки. |

## <a name="creating-and-using-private-endpoints-for-backup"></a>Создание и использование частных конечных точек для резервного копирования

В этом разделе рассказывается о шагах, связанных с созданием и использованием частных конечных точек для Azure Backup в виртуальных сетях.

>[!IMPORTANT]
> Настоятельно рекомендуется выполнить действия, описанные в той же последовательности, которая указана в этом документе. Если этого не сделать, это может привести к тому, что хранилище будет несовместимо с использованием частных конечных точек и требуется перезапустить процесс с новым хранилищем.

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

Сведения о создании хранилища с помощью клиента Azure Resource Manager см. в [этом разделе](#create-a-recovery-services-vault-using-the-azure-resource-manager-client) . Это позволяет создать хранилище с уже включенным управляемым удостоверением. Дополнительные сведения о хранилищах служб восстановления см. [здесь](./backup-azure-recovery-services-vault-overview.md).

## <a name="enable-managed-identity-for-your-vault"></a>Включение управляемого удостоверения для хранилища

Управляемые удостоверения позволяют хранилищу создавать и использовать частные конечные точки. В этом разделе рассказывается о включении управляемого удостоверения для хранилища.

1. Перейдите к хранилищу служб восстановления — > **удостоверение**.

    ![Изменить состояние удостоверения на вкл.](./media/private-endpoints/identity-status-on.png)

1. Измените **состояние** на **вкл** и нажмите кнопку **сохранить**.

1. Создается **идентификатор объекта** , который является управляемым удостоверением хранилища.

    >[!NOTE]
    >После включения управляемое удостоверение **не** должно быть отключено (даже временно). Отключение управляемого удостоверения может привести к несовместимости поведения.

## <a name="grant-permissions-to-the-vault-to-create-required-private-endpoints"></a>Предоставление разрешений хранилищу для создания необходимых частных конечных точек

Чтобы создать необходимые частные конечные точки для Azure Backup, хранилище (управляемое удостоверение хранилища) должно иметь разрешения для следующих групп ресурсов:

- Группа ресурсов, содержащая целевую виртуальную сеть
- Группа ресурсов, в которой должны быть созданы частные конечные точки
- Группа ресурсов, содержащая зоны Частная зона DNS, как подробно описано [здесь](#creating-private-endpoints-for-backup) .

Мы рекомендуем предоставить роль **участника** для этих трех групп ресурсов хранилищу (управляемое удостоверение). Ниже описано, как это сделать для определенной группы ресурсов (это необходимо сделать для каждой из трех групп ресурсов):

1. Перейдите к группе ресурсов и перейдите к **элементу управление доступом (IAM)** на левой панели.
1. В **элементе управления доступом** перейдите к разделу **Добавление назначения роли**.

    ![Добавление назначения роли](./media/private-endpoints/add-role-assignment.png)

1. В области **Добавление назначения ролей** выберите **участник** в качестве **роли** и используйте **имя** хранилища в качестве **участника**. Выберите хранилище и нажмите кнопку **сохранить** после завершения.

    ![Выбор роли и участника](./media/private-endpoints/choose-role-and-principal.png)

Дополнительные сведения об управлении разрешениями на более детальном уровне см. в статье [Создание ролей и разрешений вручную](#create-roles-and-permissions-manually).

## <a name="creating-and-approving-private-endpoints-for-azure-backup"></a>Создание и утверждение частных конечных точек для Azure Backup

### <a name="creating-private-endpoints-for-backup"></a>Создание частных конечных точек для резервного копирования

В этом разделе описывается процесс создания частной конечной точки для хранилища.

1. В строке поиска найдите и выберите **Частная ссылка**. Откроется **частный центр ссылок**.

    ![Поиск закрытой ссылки](./media/private-endpoints/search-for-private-link.png)

1. На панели навигации слева выберите **частные конечные точки**. В области **частные конечные точки** выберите **+ Добавить** , чтобы начать создание частной конечной точки для хранилища.

    ![Добавление частной конечной точки в частный центр ссылок](./media/private-endpoints/add-private-endpoint.png)

1. В процессе **создания частной конечной точки** вам потребуется указать сведения для создания подключения к частной конечной точке.

    1. **Основные** сведения. заполните основные данные для частных конечных точек. Регион должен быть таким же, как и хранилище, и ресурс.

        ![Заполнение основных сведений](./media/private-endpoints/basic-details.png)

    1. **Ресурс**. на этой вкладке необходимо указать ресурс PaaS, для которого нужно создать подключение. Выберите **Microsoft. RecoveryServices/хранилища** из типа ресурса для нужной подписки. После этого выберите имя хранилища служб восстановления в качестве **ресурса** и **AzureBackup** в качестве **целевого подресурса**.

        ![Заполнение вкладки ресурсов](./media/private-endpoints/resource-tab.png)

    1. **Конфигурация**: в поле Конфигурация укажите виртуальную сеть и подсеть, в которых необходимо создать частную конечную точку. Это будет виртуальная сеть, в которой находится виртуальная машина. Вы можете **интегрировать закрытую конечную точку** с частной зоной DNS. Кроме того, можно также использовать пользовательский DNS-сервер или создать частную зону DNS.

        ![Заполните вкладку "Конфигурация"](./media/private-endpoints/configuration-tab.png)

        Ознакомьтесь с [этим разделом](#dns-changes-for-custom-dns-servers) , если вы хотите использовать пользовательские DNS-серверы вместо интеграции с зонами Azure частная зона DNS.  

    1. При необходимости можно добавить **теги** для частной конечной точки.

    1. Продолжайте **проверку и создание** после ввода подробностей. По завершении проверки выберите **создать** , чтобы создать частную конечную точку.

## <a name="approving-private-endpoints"></a>Утверждение частных конечных точек

Если пользователь, создающий частную конечную точку, также является владельцем хранилища служб восстановления, закрытая конечная точка, созданная выше, будет утверждена для автоматического выполнения. В противном случае владелец хранилища должен утвердить закрытую конечную точку, прежде чем сможет ее использовать. В этом разделе описано утверждение частных конечных точек вручную с помощью портал Azure.

См. раздел [утверждение вручную частных конечных точек с помощью клиента Azure Resource Manager](#manual-approval-of-private-endpoints-using-the-azure-resource-manager-client) , чтобы использовать клиент Azure Resource Manager для утверждения частных конечных точек.

1. В хранилище служб восстановления перейдите на страницу **частные конечные точки подключения** на левой панели.
1. Выберите подключение к частной конечной точке, которое вы хотите утвердить.
1. На верхней панели выберите **утвердить** . Можно также выбрать **отклонить** или **Удалить** , если вы хотите отклонить или удалить подключение конечной точки.

    ![Утверждение частных конечных точек](./media/private-endpoints/approve-private-endpoints.png)

## <a name="using-private-endpoints-for-backup"></a>Использование частных конечных точек для резервного копирования

После утверждения частных конечных точек, созданных для хранилища в виртуальной сети, можно приступить к их использованию для выполнения резервного копирования и восстановления.

>[!IMPORTANT]
>Прежде чем продолжать, убедитесь, что все описанные выше действия в документе выполнены успешно. Итак, необходимо выполнить действия, описанные в следующем контрольном списке:
>
>1. Создано (новое) хранилище служб восстановления
>1. Включено хранилище для использования управляемого удостоверения, назначенного системой
>1. Назначены соответствующие разрешения управляемому удостоверению хранилища
>1. Создана частная конечная точка для хранилища
>1. Утверждена частная конечная точка (если она не утверждена для автоматического утверждения)

### <a name="backup-and-restore-of-workloads-in-azure-vm-sql-sap-hana"></a>Резервное копирование и восстановление рабочих нагрузок на виртуальной машине Azure (SQL, SAP HANA)

После создания и утверждения частной конечной точки на стороне клиента не требуется никаких дополнительных изменений для использования частной конечной точки. Весь обмен данными и передача данных из защищенной сети в хранилище выполняются через закрытую конечную точку.
Однако если удалить частные конечные точки для хранилища после регистрации сервера (SQL/SAP HANA), необходимо повторно зарегистрировать контейнер в хранилище. Для них не нужно прекращать защиту.

### <a name="backup-and-restore-through-mars-agent"></a>Резервное копирование и восстановление с помощью агента MARS

При использовании агента MARS для резервного копирования локальных ресурсов убедитесь, что локальная сеть (содержащая ваши ресурсы для резервного копирования) подключена к виртуальной сети Azure, содержащей закрытую конечную точку для хранилища, поэтому ее можно использовать. Затем можно продолжить установку агента MARS и настроить резервное копирование, как описано здесь. Однако необходимо убедиться, что все соединения для резервного копирования выполняются только через одноранговую сеть.

Однако, если удалить частные конечные точки для хранилища после регистрации агента MARS, необходимо повторно зарегистрировать контейнер в хранилище. Для них не нужно прекращать защиту.

## <a name="additional-topics"></a>Дополнительные темы

### <a name="create-a-recovery-services-vault-using-the-azure-resource-manager-client"></a>Создание хранилища служб восстановления с помощью клиента Azure Resource Manager

Вы можете создать хранилище служб восстановления и включить его управляемое удостоверение (для этого необходимо включить управляемое удостоверение, как будет показано далее) с помощью клиента Azure Resource Manager. Ниже приведен пример для этого:

```rest
armclient PUT /subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>?api-version=2017-07-01-preview @C:\<filepath>\MSIVault.json
```

Файл JSON, указанный выше, должен иметь следующее содержимое:

JSON запроса:

```json
{
  "location": "eastus2",
  "name": "<vaultname>",
  "etag": "W/\"datetime'2019-05-24T12%3A54%3A42.1757237Z'\"",
  "tags": {
    "PutKey": "PutValue"
  },
  "properties": {},
  "id": "/subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>",
  "type": "Microsoft.RecoveryServices/Vaults",
  "sku": {
    "name": "RS0",
    "tier": "Standard"
  },
  "identity": {
    "type": "systemassigned"
  }
}
```

JSON ответа:

```json
{
   "location": "eastus2",
   "name": "<vaultname>",
   "etag": "W/\"datetime'2020-02-25T05%3A26%3A58.5181122Z'\"",
   "tags": {
     "PutKey": "PutValue"
   },
   "identity": {
     "tenantId": "<tenantid>",
     "principalId": "<principalid>",
     "type": "SystemAssigned"
   },
   "properties": {
     "provisioningState": "Succeeded",
     "privateEndpointStateForBackup": "None",
     "privateEndpointStateForSiteRecovery": "None"
   },
   "id": "/subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>",
   "type": "Microsoft.RecoveryServices/Vaults",
   "sku": {
     "name": "RS0",
     "tier": "Standard"
   }
 }
```

>[!NOTE]
>Хранилище, созданное в этом примере через клиент Azure Resource Manager, уже создано с управляемым удостоверением, назначенным системой.

### <a name="managing-permissions-on-resource-groups"></a>Управление разрешениями для групп ресурсов

Управляемое удостоверение для хранилища должно иметь следующие разрешения в группе ресурсов и виртуальной сети, где будут созданы частные конечные точки:

- `Microsoft.Network/privateEndpoints/*` Это необходимо для выполнения CRUD в частных конечных точках в группе ресурсов. Ее следует назначить группе ресурсов.
- `Microsoft.Network/virtualNetworks/subnets/join/action` Это необходимо в виртуальной сети, где частный IP-адрес подключается к частной конечной точке.
- `Microsoft.Network/networkInterfaces/read` Это необходимо для группы ресурсов, чтобы получить сетевой интерфейс, созданный для частной конечной точки.
- Роль участника зоны "Частная зона DNS" Эта роль уже существует и может использоваться для `Microsoft.Network/privateDnsZones/A/*` предоставления `Microsoft.Network/privateDnsZones/virtualNetworkLinks/read` разрешений и.

Для создания ролей с необходимыми разрешениями можно использовать один из следующих методов:

#### <a name="create-roles-and-permissions-manually"></a>Создание ролей и разрешений вручную

Создайте следующие файлы JSON и используйте команду PowerShell в конце раздела, чтобы создать роли:

PrivateEndpointContributorRoleDef.jsна

```json
{
  "Name": "PrivateEndpointContributor",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows management of Private Endpoint",
  "Actions": [
    "Microsoft.Network/privateEndpoints/*",
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000"
  ]
}
```

NetworkInterfaceReaderRoleDef.jsна

```json
{
  "Name": "NetworkInterfaceReader",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows read on networkInterfaces",
  "Actions": [
    "Microsoft.Network/networkInterfaces/read"
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000"
  ]
}
```

PrivateEndpointSubnetContributorRoleDef.jsна

```json
{
  "Name": "PrivateEndpointSubnetContributor",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows adding of Private Endpoint connection to Virtual Networks",
  "Actions": [
    "Microsoft.Network/virtualNetworks/subnets/join/action"
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000"
  ]
}
```

```azurepowershell
 New-AzRoleDefinition -InputFile "PrivateEndpointContributorRoleDef.json"
 New-AzRoleDefinition -InputFile "NetworkInterfaceReaderRoleDef.json"
 New-AzRoleDefinition -InputFile "PrivateEndpointSubnetContributorRoleDef.json"
```

#### <a name="use-a-script"></a>Использование сценария

1. Запустите **Cloud Shell** в портал Azure и выберите **отправить файл** в окне PowerShell.

    ![Выбор отправки файла в окне PowerShell](./media/private-endpoints/upload-file-in-powershell.png)

1. Отправьте следующий скрипт: [ваултмсипререкскрипт](https://download.microsoft.com/download/1/2/6/126a410b-0e06-45ed-b2df-84f353034fa1/VaultMsiPrereqScript.ps1)

1. Перейдите в корневую папку (например,). `cd /home/user`

1. Выполните следующий скрипт:

    ```azurepowershell
    ./VaultMsiPrereqScript.ps1 -subscription <subscription-Id> -vaultPEResourceGroup <vaultPERG> -vaultPESubnetResourceGroup <subnetRG> -vaultMsiName <msiName>
    ```

    Ниже приведены параметры.

    - **Подписка**: * * SubscriptionId, в которой есть группа ресурсов, в которой будет создана частная конечная точка хранилища, и подсеть, в которой будут присоединены закрытые конечные точки.

    - **ваултпересаурцеграуп**: Группа ресурсов, в которой будет создана частная конечная точка для хранилища

    - **ваултпесубнетресаурцеграуп**: Группа ресурсов подсети, к которой будет присоединена частная конечная точка

    - **ваултмсинаме**: имя MSI-файла хранилища, то же, что и **VaultName**

1. Завершите проверку подлинности, и скрипт примет контекст указанной подписки, указанный выше. Она создаст соответствующие роли, если они отсутствуют в клиенте и присвоит роли MSI хранилища.

### <a name="creating-private-endpoints-using-azure-powershell"></a>Создание частных конечных точек с помощью Azure PowerShell

#### <a name="auto-approved-private-endpoints"></a>Автоматические утвержденные частные конечные точки

```azurepowershell
$vault = Get-AzRecoveryServicesVault `
        -ResourceGroupName $vaultResourceGroupName `
        -Name $vaultName
  
$privateEndpointConnection = New-AzPrivateLinkServiceConnection `
        -Name $privateEndpointConnectionName `
        -PrivateLinkServiceId $vault.ID `
        -GroupId "AzureBackup"  
  
$privateEndpoint = New-AzPrivateEndpoint `
        -ResourceGroupName $vmResourceGroupName `
        -Name $privateEndpointName `
        -Location $location `
        -Subnet $subnet `
        -PrivateLinkServiceConnection $privateEndpointConnection `
        -Force
```

### <a name="manual-approval-of-private-endpoints-using-the-azure-resource-manager-client"></a>Утверждение частных конечных точек вручную с помощью клиента Azure Resource Manager

1. Чтобы получить идентификатор подключения частной конечной точки для частной конечной точки, используйте параметр " **Vault** ".

    ```rest
    armclient GET /subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/vaults/<vaultname>?api-version=2017-07-01-preview
    ```

    Будет возвращен идентификатор подключения частной конечной точки. Имя соединения можно получить с помощью первой части идентификатора соединения следующим образом:

    `privateendpointconnectionid = {peName}.{vaultId}.backup.{guid}`

1. Получите **идентификатор подключения частной конечной точки** (и **имя частной конечной точки**, где угодно требуется) из ответа и замените его в следующем JSON и Azure Resource Manager URI и попробуйте изменить состояние на "утверждено/отклонено/отключено", как показано в примере ниже:

    ```rest
    armclient PUT /subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>/privateEndpointConnections/<privateendpointconnectionid>?api-version=2020-02-02-preview @C:\<filepath>\BackupAdminApproval.json
    ```

    JSON.

    ```json
    {
    "id": "/subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>/privateEndpointConnections/<privateendpointconnectionid>",
    "properties": {
        "privateEndpoint": {
        "id": "/subscriptions/<subscriptionid>/resourceGroups/<pergname>/providers/Microsoft.Network/privateEndpoints/pename"
        },
        "privateLinkServiceConnectionState": {
        "status": "Disconnected",  //choose state from Approved/Rejected/Disconnected
        "description": "Disconnected by <userid>"
        }
    }
    }
    ```

### <a name="dns-changes-for-custom-dns-servers"></a>Изменения DNS для пользовательских DNS-серверов

#### <a name="create-dns-zones-for-custom-dns-servers"></a>Создание зон DNS для пользовательских DNS-серверов

Необходимо создать три частные зоны DNS и связать их с виртуальной сетью.

| **Зона**                                                     | **Служба** |
| ------------------------------------------------------------ | ----------- |
| `privatelink.<geo>.backup.windowsazure.com`      | Backup      |
| `privatelink.blob.core.windows.net`                            | BLOB-объект        |
| `privatelink.queue.core.windows.net`                           | Очередь       |

>[!NOTE]
>В приведенном выше тексте *Geo* относится к коду региона. Например, *вкус* и *Ne* for Западная Центральная часть США и Северная Европа соответственно.

Коды регионов см. в [этом списке](https://download.microsoft.com/download/1/2/6/126a410b-0e06-45ed-b2df-84f353034fa1/AzureRegionCodesList.docx) . См. следующие ссылки на соглашения об именовании URL-адресов в национальных регионах:

- [Китай](/azure/china/resources-developer-guide#check-endpoints-in-azure)
- [Германия](../germany/germany-developer-guide.md#endpoint-mapping)
- [US Gov](../azure-government/documentation-government-developer-guide.md)

#### <a name="adding-dns-records-for-custom-dns-servers"></a>Добавление записей DNS для пользовательских DNS-серверов

Для этого необходимо сделать записи для каждого полного доменного имени в частной конечной точке в зону Частная зона DNS.

Следует отметить, что мы будем использовать частные конечные точки, созданные для резервного копирования, больших двоичных объектов и служба очередей.

- Частная конечная точка хранилища использует имя, указанное при создании частной конечной точки
- Частные конечные точки для служб BLOB-объектов и очередей имеют префикс с одинаковым именем для хранилища.

Например, на следующем рисунке показаны три частные конечные точки, созданные для подключения к частной конечной точке с именем *pee2epe*:

![Три частные конечные точки для подключения к частной конечной точке](./media/private-endpoints/three-private-endpoints.png)

Зона DNS для службы резервного копирования ( `privatelink.<geo>.backup.windowsazure.com` ):

1. Перейдите к своей частной конечной точке для резервного копирования в **частном центре ссылок**. На странице Обзор перечислены полные доменные имена и частные IP-адреса для частной конечной точки.

1. Добавьте одну запись для каждого полного доменного имени и частного IP-адреса в качестве записи типа.

    ![Добавление записи для каждого полного доменного имени и частного IP-адреса](./media/private-endpoints/add-entry-for-each-fqdn-and-ip.png)

Зона DNS для службы BLOB-объектов ( `privatelink.blob.core.windows.net` ):

1. Перейдите к частной конечной точке для BLOB-объекта в **частном центре ссылок**. На странице Обзор перечислены полные доменные имена и частные IP-адреса для частной конечной точки.

1. Добавьте запись для полного доменного имени и частного IP-адреса в качестве записи типа.

    ![Добавление записи для полного доменного имени и частного IP-адреса в качестве записи типа для службы BLOB-объектов](./media/private-endpoints/add-type-a-record-for-blob.png)

Зона DNS для служба очередей ( `privatelink.queue.core.windows.net` ):

1. Перейдите к частной конечной точке для очереди в **частном центре ссылок**. На странице Обзор перечислены полные доменные имена и частные IP-адреса для частной конечной точки.

1. Добавьте запись для полного доменного имени и частного IP-адреса в качестве записи типа.

    ![Добавьте запись для полного доменного имени и частного IP-адреса в качестве записи типа для служба очередей](./media/private-endpoints/add-type-a-record-for-queue.png)

## <a name="frequently-asked-questions"></a>Вопросы и ответы

У. Можно ли создать частную конечную точку для существующего резервного хранилища?<br>
A. Нет, частные конечные точки можно создавать только для новых резервных хранилищ. Поэтому хранилище не должно иметь каких-либо элементов, защищенных в нем. На самом деле, перед созданием частных конечных точек не попытается защитить какие бы то ни было элементы в хранилище.

У. Я попытался защитить элемент в моем хранилище, но это не удалось, и хранилище по-прежнему не содержит элементов, защищенных для него. Можно ли создать частные конечные точки для этого хранилища?<br>
A. Нет, хранилище не должно иметь каких бы то ни было попыток защитить какие бы то ни было элементы в прошлом.

У. У меня есть хранилище, которое использует частные конечные точки для резервного копирования и восстановления. Можно ли позже добавить или удалить частные конечные точки для этого хранилища, даже если у меня есть защищенные элементы резервного копирования?<br>
A. Да. Если вы уже создали частные конечные точки для хранилища и защищенные резервные элементы, то позднее можно будет добавить или удалить частные конечные точки.

У. Можно ли также использовать закрытую конечную точку для Azure Backup Azure Site Recovery?<br>
A. Нет, Частная конечная точка для резервного копирования может использоваться только для Azure Backup. Необходимо создать новую закрытую конечную точку для Azure Site Recovery, если она поддерживается службой.

У. Я пропустил один из шагов, описанных в этой статье, и приходился защищать источник данных. Можно ли по-прежнему использовать частные конечные точки?<br>
A. Не выполнив действия, описанные в статье, и продолжение защиты элементов может привести к тому, что хранилище не сможет использовать частные конечные точки. Поэтому рекомендуется ознакомиться с этим контрольным списком перед тем, как перейти к защите элементов.

У. Можно ли использовать собственный DNS-сервер вместо использования частной зоны DNS Azure или интегрированной частной зоны DNS?<br>
A. Да, можно использовать собственные DNS-серверы. Однако убедитесь, что все необходимые записи DNS добавлены в этом разделе как предложенные.

У. Нужно ли выполнять дополнительные действия на моем сервере после выполнения процедуры в этой статье?<br>
A. После выполнения процесса, описанного в этой статье, вам не нужно выполнять дополнительную работу, чтобы использовать частные конечные точки для резервного копирования и восстановления.

## <a name="next-steps"></a>Дальнейшие действия

- Ознакомьтесь со всеми [функциями безопасности в Azure Backup](security-overview.md)
