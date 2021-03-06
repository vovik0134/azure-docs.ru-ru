---
title: Подготовка образа к просмотру приложения MSIX Windows Virtual Desktop Предварительная версия — Azure
description: Создание образа подключения к MSIX приложению для пула узлов виртуальных рабочих столов Windows.
author: Heidilohr
ms.topic: how-to
ms.date: 12/14/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 204cc9a05d62caf62179100fa3496be422a3ec0c
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/14/2020
ms.locfileid: "97426016"
---
# <a name="prepare-an-msix-image-for-windows-virtual-desktop"></a>Подготовка образа MSIX для виртуального рабочего стола Windows

> [!IMPORTANT]
> Присоединение приложения MSIX в настоящее время находится в общедоступной предварительной версии.
> Эта предварительная версия предоставляется без соглашения об уровне обслуживания и не рекомендована для использования рабочей среде. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены. Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Присоединение приложения MSIX (Предварительная версия) — это решение уровня приложения, которое позволяет динамически подключать приложения из пакета MSIX к сеансу пользователя. Система пакетов MSIX отделяет приложения от операционной системы, что упрощает создание образов для виртуальных машин. Пакеты MSIX также обеспечивают больший контроль над тем, к каким приложениям пользователи могут обращаться на своих виртуальных машинах. Вы можете даже отделять приложения от главного образа и предоставлять их пользователям позже.

## <a name="create-a-vhd-or-vhdx-package-for-msix"></a>Создание пакета VHD или VHDX для MSIX

Для правильной работы пакеты MSIX должны быть в формате VHD или VHDX. Это означает, что чтобы начать работу, необходимо создать пакет VHD или VHDX.

>[!NOTE]
>Если вы этого еще не сделали, убедитесь, что вы включили Hyper-V, выполнив инструкции из статьи [Установка Hyper-v в Windows 10](/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v).

Чтобы создать пакет VHD или VHDX для MSIX, выполните следующие действия.

1. Сначала откройте PowerShell.
2. Затем выполните следующий командлет, чтобы создать виртуальный жесткий диск:

    ```powershell
    New-VHD -SizeBytes <size>MB -Path c:\temp\<name>.vhd -Dynamic -Confirm:$false
    ```

    >[!NOTE]
    > Убедитесь, что виртуальный жесткий диск достаточно велик для размещения развернутого пакета MSIX.

3. Выполните следующий командлет, чтобы подключить только что созданный виртуальный жесткий диск:

    ```powershell
    $vhdObject = Mount-VHD c:\temp\<name>.vhd -Passthru
    ```

4. Затем запустите этот командлет для инициализации подключенного виртуального жесткого диска:

    ```powershell
    $disk = Initialize-Disk -Passthru -Number $vhdObject.Number
    ```

5. После этого выполните командлет, чтобы создать новый раздел для инициализированного виртуального жесткого диска.

    ```powershell
    $partition = New-Partition -AssignDriveLetter -UseMaximumSize -DiskNumber $disk.Number
    ```

6. Выполните следующий командлет, чтобы отформатировать этот раздел:

    ```powershell
    Format-Volume -FileSystem NTFS -Confirm:$false -DriveLetter $partition.DriveLetter -Force
    ```

7. Наконец, создайте родительскую папку на подключенном виртуальном жестком диске. Этот шаг является обязательным, так как у пакета MSIX должна быть родительская папка для правильной работы. Имя родительской папки не имеет значения, пока существует родительская папка.

## <a name="expand-msix"></a>Развертывание MSIX

После этого необходимо развернуть образ MSIX, выполнив распаковку своих файлов в виртуальный жесткий диск.

Чтобы развернуть образ MSIX, сделайте следующее:

1. [Скачайте инструмент msixmgr](https://aka.ms/msixmgr) и сохраните ZIP-архив в папку на виртуальной машине с узлом сеансов.

2. Распакуйте ZIP-архив с инструментом msixmgr.

3. Поместите исходный пакет MSIX в ту же папку, в которую вы распаковали инструмент msixmgr.

4. Откройте командную строку от имени администратора и перейдите к папке, в которую вы скачали и распаковали инструмент msixmgr.

5. Выполните следующий командлет, чтобы распаковать MSIX в виртуальный жесткий диск, созданный в предыдущем разделе.

    ```powershell
    msixmgr.exe -Unpack -packagePath <package>.msix -destination "f:\<name of folder you created earlier>" -applyacls
    ```

    После завершения распаковки должно появиться следующее сообщение:

    > Успешно распакованы и применены списки ACL для пакета: <package name> . msix

    >[!NOTE]
    > Если вы используете пакеты из Microsoft Store для бизнеса или образования в сети или на устройствах, не подключенных к Интернету, необходимо скачать и установить лицензии пакета из Microsoft Store для запуска приложений. Сведения о получении лицензий см. в статье [Использование пакетов в автономном режиме](app-attach.md#use-packages-offline).

6. Перейдите к подключенному виртуальному жесткому диску и откройте папку приложения, чтобы убедиться в наличии содержимого пакета.

7. Отключите виртуальный жесткий диск.

## <a name="upload-msix-image-to-share"></a>Отправить образ MSIX в общую папку

После создания пакета MSIX необходимо передать полученный файл VHD, VHDX или CIM в общую папку, в которой виртуальные машины пользователей могут получить к ним доступ.

## <a name="next-steps"></a>Дальнейшие действия

Запросите наши вопросы сообщества об этой функции в [Windows Virtual Desktop течкоммунити](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop).

Вы также можете оставить отзыв о виртуальном рабочем столе Windows в [центре обратной связи по виртуальным рабочим столам Windows](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app).

Ниже приведены некоторые другие статьи, которые могут оказаться полезными.

- [Глоссарий по присоединению приложений MSIX](app-attach-glossary.md)
- [Часто задаваемые вопросы о присоединении приложения MSIX](app-attach-faq.md)