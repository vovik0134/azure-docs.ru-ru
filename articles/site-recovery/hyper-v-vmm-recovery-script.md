---
title: Добавление сценария в план восстановления в Azure Site Recovery
description: Узнайте, как добавить сценарий VMM в план восстановления для аварийного восстановления виртуальных машин Hyper-V в облаках VMM.
author: Sharmistha-Rai
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: sharrai
ms.openlocfilehash: 3217c30737a133c1c1092fc4a8a8caaa0338e980
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "89425880"
---
# <a name="add-a-vmm-script-to-a-recovery-plan"></a>Добавление скрипта VMM в план восстановления

В этой статье описывается, как создать скрипт System Center Virtual Machine Manager (VMM) и добавить его в план восстановления в [Azure Site Recovery](site-recovery-overview.md).

Комментарии или вопросы можно добавить в конце этой статьи или на [странице вопросов и ответов по службам восстановления Azure](/answers/topics/azure-site-recovery.html).

## <a name="prerequisites"></a>Предварительные требования

В планах восстановления можно использовать скрипты PowerShell. Чтобы он был доступен в плане восстановления, этот скрипт необходимо создать и поместить в библиотеку VMM. При создании скрипта необходимо учитывать следующее.

* Для аккуратной обработки исключений скрипты должны включать блоки try-catch.
    - Если в скрипте возникает исключение, его выполнение останавливается и задача помечается как невыполненная.
    - При возникновении ошибки оставшаяся часть скрипта не выполняется.
    - Если ошибка происходит при выполнении внеплановой отработки отказа, выполнение плана восстановления продолжается.
    - Если ошибка происходит при выполнении плановой отработки отказа, выполнение плана восстановления останавливается. Исправьте скрипт, убедитесь, что он выполняется должным образом, и повторно запустите план восстановления.
        - Команда `Write-Host` не работает в скрипте плана восстановления. Если вы используете в скрипте команду `Write-Host`, выполнение скрипта завершается ошибкой. Для создания выходного файла создайте скрипт прокси, который выполняется в основном скрипте. Убедитесь, что все выходные данные перенаправляются, с помощью команды **\>\>** .
        - Если скрипт не завершается в течение 600 секунд, он будет остановлен.
        - Если от скрипта поступят любые данные в вывод STDERR, он будет считаться завершившимся с ошибкой. Эти сведения отображаются в данных о выполнении скрипта.

* Скрипты в плане восстановления выполняются в контексте учетной записи службы VMM. Убедитесь, что эта учетная запись имеет разрешения на чтение для удаленной общей папки, в которой расположен скрипт. Проверьте выполнение скрипта с теми же правами пользователя, что и у учетной записи службы VMM.
* Командлеты VMM предоставляются в модуле Windows PowerShell. Этот модуль устанавливается одновременно с консолью VMM. Чтобы загрузить этот модуль в скрипт, используйте в скрипте следующую команду. 

    `Import-Module -Name virtualmachinemanager`

    Дополнительные сведения см. в статье [Приступая к работе с Windows PowerShell и VMM](/previous-versions/system-center/system-center-2012-R2/hh875013(v=sc.12)).
* Убедитесь в наличии хотя бы одного сервера библиотеки в развертывании VMM. По умолчанию общая папка библиотеки для сервера VMM размещается на сервере VMM. Имя папки — MSCVMMLibrary.

  Если ваша общая папка библиотеки находится на удаленном сервере (или расположена локально, но не совпадает с папкой MSCVMMLibrary), настройте общую папку, как описано ниже (\\\libserver2.contoso.com\share\ используется в качестве примера).
  
  1. Откройте редактор реестра и найдите запись **HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\Azure Site Recovery\Registration**.

  1. Для параметра **ScriptLibraryPath** укажите **\\\libserver2.contoso.com\share\\** . Укажите полное доменное имя. Предоставьте разрешения для расположения общей папки. Это корневой узел общего ресурса. Чтобы проверить корневой узел, в VMM перейдите к корневому узлу в библиотеке. Открывшийся путь является корневым. Этот путь необходимо использовать в переменной.

  1. Проверьте выполнение скрипта с помощью учетной записи с теми же правами, что и учетной записи службы VMM. Применяя эти права пользователей, можно убедиться, что изолированные протестированные скрипты выполняются так же, как и в планах восстановления. На сервере VMM установите обход политики выполнения следующим образом.

     а. Откройте консоль **PowerShell 64-разрядной версии Windows** с правами администратора.
     
     b. Введите **Set-executionpolicy bypass**. Дополнительные сведения см. в статье [Библиотека технической информации для Windows Server](/previous-versions/windows/it-pro/windows-powershell-1.0/ee176961(v=technet.10)).

     > [!IMPORTANT]
     > Задайте командлет **Set-executionpolicy bypass** только для 64-разрядной консоли PowerShell. Если задать его для 32-разрядной консоли PowerShell, скрипты не выполняются.

## <a name="add-the-script-to-the-vmm-library"></a>Добавление сценария в библиотеку VMM

При наличии исходного сайта VMM можно создать скрипт на сервере VMM. Затем включите этот скрипт в план восстановления.

1. Создайте новую папку в общей папке библиотеки. Например, \<VMM server name> \мссквммлибрари\рпскриптс. Разместите ее на исходном и целевом серверах VMM.
1. Создайте скрипт. Назовите его RPScript. Убедитесь, что скрипт выполняется правильно.
1. Поместите сценарий в \<VMM server name> папку \мссквммлибрари на исходном и целевом серверах VMM.

## <a name="add-the-script-to-a-recovery-plan"></a>Добавление сценария в план восстановления

Когда вы создадите план восстановления и добавите в него виртуальные машины или группы репликации, в эту группу можно добавить скрипт.

1. Откройте план восстановления.
1. Выберите элемент в списке **Шаг**. Затем выберите **Скрипт** или **Выполняемое вручную действие**.
1. Укажите, следует ли добавить скрипт или действие до или после выбранного элемента. Используя кнопки управления **Move Up** (Переместить выше) и **Move Down** (Переместить ниже), можно изменить положение скрипта.
1. Если вы добавляете скрипт VMM, выберите **Failover to VMM script** (Отработка отказа в скрипт VMM). Для параметра **Путь к скрипту** укажите относительный путь к общей папке. Например, введите **\RPScripts\RPScript.PS1**.
1. Если вы добавляете модуль Runbook службы автоматизации Azure, укажите учетную запись службы автоматизации, в которой расположен этот модуль Runbook. Затем выберите нужный скрипт модуля Runbook Azure.
1. Чтобы убедиться в исправной работе скрипта, выполните тестовую отработку отказа плана восстановления.


## <a name="next-steps"></a>Дальнейшие действия
* [Дополнительные сведения](site-recovery-failover.md) о выполнении отработки отказа.

