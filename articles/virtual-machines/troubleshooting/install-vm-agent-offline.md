---
title: Установка агента виртуальной машины Azure в автономном режиме | Документация Майкрософт
description: Узнайте, как установить агент виртуальной машины Azure в автономном режиме.
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 07/06/2020
ms.author: genli
ms.openlocfilehash: 456aa225fa8eed47ca794c54e61b77a30c93fa9a
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/25/2020
ms.locfileid: "96002618"
---
# <a name="install-the-azure-virtual-machine-agent-in-offline-mode"></a>Установка агента виртуальной машины Azure в автономном режиме 

Агент виртуальной машины Azure обеспечивает полезные функции, такие как сброс пароля локального администратора и принудительная отправка сценария. В этой статье описывается, как установить агент виртуальной машины для автономной виртуальной машины Windows. 

## <a name="when-to-use-the-vm-agent-in-offline-mode"></a>Когда нужно использовать агент виртуальной машины в автономном режиме

Установите агент виртуальной машины в автономном режиме в таких случаях:

- На развернутой виртуальной машине Azure агент не установлен или он не работает.
- Вы забыли пароль администратора для виртуальной машины или не можете получить к ней доступ.

## <a name="how-to-install-the-vm-agent-in-offline-mode"></a>Как установить агент виртуальной машины в автономном режиме

Выполните шаги ниже, чтобы установить агент виртуальной машины в автономном режиме.

### <a name="step-1-attach-the-os-disk-of-the-vm-to-another-vm-as-a-data-disk"></a>Шаг 1. Подключение диска операционной системы виртуальной машины к другой виртуальной машине в качестве диска данных

1. Создайте моментальный снимок диска ОС затронутой виртуальной машины, создайте диск из моментального снимка, затем подключите диск к виртуальной машине, на которой выполняется устранение неполадок. Дополнительные сведения см. в статье [Устранение неполадок с виртуальной машиной Windows при подключении диска операционной системы к виртуальной машине восстановления с помощью портала Azure](troubleshoot-recovery-disks-portal-windows.md). Для классической виртуальной машины удалите виртуальную машину и сохраните диск операционной системы, а затем подключите диск операционной системы к виртуальной машине для устранения неполадок.

2.  Подключитесь к виртуальной машине для устранения неполадок. Откройте **Computer management** оснастку "  >  **Управление дисками**" управления компьютером. Убедитесь, что диск операционной системы подключен и что разделам диска назначены буквы диска.

### <a name="step-2-modify-the-os-disk-to-install-the-azure-vm-agent"></a>Шаг 2. Изменение диска операционной системы для установки агента виртуальной машины Azure

1.  Установите подключение к удаленному рабочему столу для виртуальной машины для устранения неполадок.

2.  В виртуальной машине для устранения неполадок перейдите к подключенному диску ОС, откройте папку \WINDOWS\System32\Config. Скопируйте все файлы в этой папке как файл резервной копии. Это необходимо, если потребуется откат.

3.  Запустите **редактор реестра** (файл regedit.exe).

4.  Выберите раздел **HKEY_LOCAL_MACHINE**. В меню выберите **файл**  >  **Загрузить куст**:

    ![Загрузка куста реестра](./media/install-vm-agent-offline/load-hive.png)

5.  В подключенном диске операционной системы перейдите в системную папку \windows\system32\config\SYSTEM. Для имени куста введите **BROKENSYSTEM**. В разделе **HKEY_LOCAL_MACHINE** появится новый куст реестра.

6.  В подключенном диске операционной системы перейдите в папку \windows\system32\config\SOFTWARE. Для имени программного обеспечения куста введите **BROKENSOFTWARE**.

7. Если на подключенном диске ОС установлен агент виртуальной машины, создайте резервную копию текущей конфигурации. В противном случае перейдите к выполнению следующего шага.
      
    1. Переименуйте папку \windowsazure на \windowsazure.old.

    2. Экспортируйте следующие реестры:
        - HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet001\Services\WindowsAzureGuestAgent
        - HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet001\Services\RdAgent

8.  Используйте имеющиеся файлы на виртуальной машине для устранения неполадок, как репозиторий для установки агента виртуальной машины. Выполните следующие шаги:

    1. Из виртуальной машины для устранения неполадок экспортируйте следующие подразделы в формате реестра (REG): 
        - HKEY_LOCAL_MACHINE  \SYSTEM\ControlSet001\Services\WindowsAzureGuestAgent
        - HKEY_LOCAL_MACHINE  \SYSTEM\ControlSet001\Services\RdAgent

          ![Экспорт подразделов реестра](./media/install-vm-agent-offline/backup-reg.png)

    2. Измените файлы реестра. В каждом файле измените значение записи **SYSTEM** на **BROKENSYSTEM** (как показано на рисунке ниже) и сохраните файл. Запомните значение **ImagePath** текущего агента виртуальной машины. Необходимо будет скопировать соответствующую папку на подключенный диск ОС. 

        ![Изменение значений подраздела реестра](./media/install-vm-agent-offline/change-reg.png)

    3. Импортируйте файлы реестра в репозиторий, дважды щелкнув каждый файл реестра.

    4. Убедитесь, что следующие два подраздела успешно импортированы в куст **BROKENSYSTEM** :
        - WindowsAzureGuestAgent
        - RdAgent

    5. Скопируйте папку установки текущего агента виртуальной машины на подключенный диск ОС: 

        1.  На присоединенном диске ОС создайте папку с именем WindowsAzure в корневом пути.

        2.  Откройте папку C:\WindowsAzure на виртуальной машине для устранения неполадок и найдите папку с именем C:\WindowsAzure\GuestAgent_X.X.XXXX.XXX. Скопируйте папку GuestAgent с номером последней версии из папки C:\WindowsAzure в папку WindowsAzure на подключенном диске ОС. Если вы не уверены, какую папку нужно копировать, скопируйте все папки GuestAgent. Ниже показан пример папки GuestAgent, которая копируется на подключенный диск ОС.

             ![Копирование папки GuestAgent](./media/install-vm-agent-offline/copy-files.png)

9.  Выберите **BROKENSYSTEM**. В меню выберите **файл**  >  **Выгрузить куст**.

10.  Выберите **BROKENSOFTWARE**. В меню выберите **файл**  >  **Выгрузить куст**.

11.  Отключите диск операционной системы, а затем [Измените диск операционной системы для затронутой виртуальной машины](troubleshoot-recovery-disks-portal-windows.md#swap-the-os-disk-for-the-vm). Для классической виртуальной машины создайте новую виртуальную машину с помощью исправленного диска ОС.

12.  Войдите на виртуальную машину. Обратите внимание, что агент RdAgent работает, а журналы создаются.

Если вы создали виртуальную машину с помощью модели развертывания Resource Manager, дополнительные шаги не требуются.

### <a name="use-the-provisionguestagent-property-for-classic-vms"></a>Использование свойства ProvisionGuestAgent для классических виртуальных машин

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

При создании виртуальной машины с помощью классической модели используйте модуль Azure PowerShell для обновления свойства **ProvisionGuestAgent**. Это свойство сообщает Azure об установке агента виртуальной машины.

Чтобы задать свойство **ProvisionGuestAgent**, выполните следующие команды в Azure PowerShell:

   ```powershell
   $vm = Get-AzureVM –ServiceName <cloud service name> –Name <VM name>
   $vm.VM.ProvisionGuestAgent = $true
   Update-AzureVM –Name <VM name> –VM $vm.VM –ServiceName <cloud service name>
   ```

Затем выполните команду `Get-AzureVM` . Обратите внимание, что свойство **GuestAgentStatus** теперь заполняется такими данными:

   ```powershell
   Get-AzureVM –ServiceName <cloud service name> –Name <VM name>
   GuestAgentStatus:Microsoft.WindowsAzure.Commands.ServiceManagement.Model.PersistentVMModel.GuestAgentStatus
   ```

## <a name="next-steps"></a>Следующие шаги

- [Обзор агента виртуальной машины Azure](../extensions/agent-windows.md)
- [Обзор расширений и компонентов виртуальной машины под управлением Windows](../extensions/features-windows.md)
