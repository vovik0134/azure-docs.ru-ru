---
title: Настройка Azure Backup Server для решения Azure VMware
description: Настройте среду решения Azure VMware для резервного копирования виртуальных машин с помощью Azure Backup Server.
ms.topic: how-to
ms.date: 10/23/2020
ms.openlocfilehash: e71ec19402d22643d51f1435d1abcf56b20a290b
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/24/2020
ms.locfileid: "92517384"
---
# <a name="set-up-azure-backup-server-for-azure-vmware-solution"></a>Настройка Azure Backup Server для решения Azure VMware

Azure Backup Server влияет на стратегию непрерывности бизнес-процессов и аварийного восстановления (BCDR). Решение VMware для Azure позволяет настроить резервное копирование на уровне виртуальной машины только с помощью Azure Backup Server. 

Azure Backup Server может хранить данные резервной копии в:

- **Диск**. для краткосрочного хранения Azure Backup Server создает резервные копии данных в пулах дисков.
- **Azure**. для краткосрочного и долгосрочного хранения в локальной среде Azure Backup Server данные, хранящиеся в пулах дисков, можно архивировать в Microsoft Azure облаке с помощью Azure Backup.

Используйте Azure Backup Server для восстановления данных в исходном или альтернативном расположении. Таким образом, если исходные данные недоступны из-за запланированных или непредвиденных проблем, можно восстановить данные в альтернативное расположение.

Эта статья поможет вам подготовить среду решения Azure VMware для резервного копирования виртуальных машин с помощью Azure Backup Server. Мы рассмотрим следующие шаги: 

> [!div class="checklist"]
> * Определите рекомендуемый тип диска виртуальной машины и размер для использования.
> * Создайте хранилище служб восстановления, в котором хранятся точки восстановления.
> * Настройте репликацию хранилища для хранилища служб восстановления.
> * Добавьте хранилище в Azure Backup Server.

## <a name="supported-vmware-features"></a>Поддерживаемые функции VMware

- **Резервное копирование без агента:** Azure Backup Server не требует установки агента на сервере vCenter или ESXi для резервного копирования виртуальной машины. Вместо этого просто укажите IP-адрес или полное доменное имя (FQDN) и учетные данные для входа, используемые для проверки подлинности сервера VMware с Azure Backup Server.
- **Встроенная в облако резервная копия:** Azure Backup Server защищает рабочие нагрузки на диск и облако. Рабочий процесс резервного копирования и восстановления Azure Backup Server помогает управлять долгосрочным хранением и автономным резервным копированием.
- **Обнаружение и защита виртуальных машин, управляемых vCenter:** Azure Backup Server обнаруживает и защищает виртуальные машины, развернутые на сервере vCenter или ESXi. Azure Backup Server также обнаруживает виртуальные машины под управлением vCenter, что позволяет защищать крупные развертывания.
- **Автоматическая защита на уровне папок.** vCenter позволяет упорядочивать виртуальные машины в ПАПКАХ виртуальных машин. Azure Backup Server обнаружит эти папки. Его можно использовать для защиты виртуальных машин на уровне папок, включая все вложенные папки. При защите папок Azure Backup Server защищает виртуальные машины в этой папке и защищает виртуальные машины, добавленные позже. Azure Backup Server ежедневно обнаруживает новые виртуальные машины, защищая их автоматически. При упорядочивании виртуальных машин в рекурсивных папках Azure Backup Server автоматически обнаруживает и защищает новые виртуальные машины, развернутые в рекурсивных папках.
- **Azure Backup Server продолжит защищать VMotion виртуальных машин в кластере:** Так как виртуальные машины распределены для балансировки нагрузки в кластере, Azure Backup Server автоматически обнаруживает и возобновляет защиту виртуальных машин.
- **Быстрое восстановление необходимых файлов:** Azure Backup Server может восстанавливать файлы или папки с виртуальной машины Windows без восстановления всей виртуальной машины.

## <a name="limitations"></a>Ограничения

- Необходимо установить накопительный пакет обновления 1 для Azure Backup Server v3.
- Вы не можете создавать резервные копии моментальных снимков пользователей перед первой Azure Backup Server резервной копией. После того, как Azure Backup Server завершит первую архивацию, можно выполнить резервное копирование моментальных снимков пользователей.
- Azure Backup Server не может защищать виртуальные машины VMware с транзитными дисками и сопоставлениями физических необработанных устройств (Прдмс).
- Azure Backup Server не удается обнаружить или защитить VMware vApp.

Чтобы настроить Azure Backup Server для решения Azure VMware, выполните следующие действия.

- Настройте необходимые компоненты и среду.
- Создайте хранилище служб восстановления,
- Скачайте и установите Azure Backup Server.
- Добавьте хранилище в Azure Backup Server.

### <a name="deployment-architecture"></a>Архитектура развертывания

Azure Backup Server развертывается как виртуальная машина Azure "инфраструктура как услуга" (IaaS) для защиты виртуальных машин Azure VMware.

:::image type="content" source="media/azure-vmware-solution-backup/deploy-backup-server-azure-vmware-solution-diagram.png" alt-text="Azure Backup Server развертывается как виртуальная машина Azure &quot;инфраструктура как услуга&quot; (IaaS) для защиты виртуальных машин Azure VMware." border="false":::

## <a name="prerequisites-for-the-azure-backup-server-environment"></a>Предварительные требования для среды Azure Backup Server

При установке Azure Backup Server в среде Azure учитывайте рекомендации, приведенные в этом разделе.

### <a name="azure-virtual-network"></a>Виртуальная сеть Azure

Убедитесь, что вы [настроили сетевые подключения для частного облака VMware в Azure](tutorial-configure-networking.md).

### <a name="determine-the-size-of-the-vm"></a>Определение размера виртуальной машины

Следуйте инструкциям в руководстве по [созданию первой виртуальной машины Windows в портал Azure](../virtual-machines/windows/quick-create-portal.md) .  Вы создадите виртуальную машину в виртуальной сети, созданной на предыдущем шаге. Начните с образа коллекции Windows Server 2019 Datacenter для запуска Azure Backup Server. 

В таблице перечислены максимальные количество защищенных рабочих нагрузок для каждого Azure Backup Server размера виртуальной машины. Информация основана на внутренних тестах производительности и масштабируемости с каноническими значениями для размеров рабочей нагрузки и изменений в ней. Фактический размер рабочей нагрузки может быть больше, но должен соответствовать дискам, подключенным к Azure Backup Server виртуальной машине.

| Максимальное число защищенных рабочих нагрузок | Средний размер рабочей нагрузки | Средний размер ежедневных изменений | Минимальное число операций ввода-вывода в хранилище | Рекомендуемый тип или размер диска      | Рекомендуемый размер виртуальной машины |
|-------------------------|-----------------------|--------------------------------|------------------|-----------------------------------|---------------------|
| 20                      | 100 ГБ                | 5-процентное изменение                   | 2 000             | HDD (цен. категория "Стандартный") (8 ТБ или больше размера на диск)  | A4V2       |
| 40                      | 150 ГБ                | 10-процентное изменение                  | 4 500             | SSD (цен. категория "Премиум") * (размер 1 ТБ или выше для каждого диска) | DS3_V2     |
| 60                      | 200 Мб                | 10-процентное изменение                  | 10 500            | SSD (цен. категория "Премиум") * (8 ТБ или превышает размер на диск) | DS3_V2     |

* Чтобы получить необходимые операции ввода-вывода, используйте диски минимального рекомендуемого или более высокого размера. Диски меньшего размера обеспечивают более низкие операции ввода-вывода.

> [!NOTE]
> Компонент Azure Backup Server предназначен для запуска на выделенном специализированном сервере. Нельзя установить Azure Backup Server на компьютере, который:
> * Выполняется как контроллер домена.
> * Имеет установленную роль сервера приложений.
> * Является сервером управления System Center Operations Manager.
> * Выполняет сервер Exchange.
> * — Это узел кластера.

### <a name="disks-and-storage"></a>Диски и хранилище

Azure Backup Server требует наличия дисков для установки. 

| Требование                      | Рекомендуемый размер  |
|----------------------------------|-------------------------|
| Установка Azure Backup Server                | Расположение установки: 3 ГБ<br />Диск для файлов базы данных: 900 МБ<br />Системный диск: 1 ГБ для установки SQL Server<br /><br />Кроме того, потребуется место для Azure Backup Server копирования каталога файлов во временную папку установки во время архивации.      |
| Диск для пула носителей<br />(Использует базовые тома, не может находиться на динамическом диске) | От двух до трех раз превышает размер защищенных данных.<br />Подробные сведения о вычислении хранилища см. в разделе [DPM планировщик ресурсов](https://www.microsoft.com/download/details.aspx?id=54301).   |

Сведения о подключении нового управляемого диска данных к существующей виртуальной машине Azure см. в статье [Подключение управляемого диска данных к виртуальной машине Windows с помощью портал Azure](../virtual-machines/windows/attach-managed-disk-portal.md).

> [!NOTE]
> Один Azure Backup Server имеет мягкое ограничение в 120 ТБ для пула носителей.

### <a name="store-backup-data-on-local-disk-and-in-azure"></a>Хранение данных резервных копий на локальном диске и в Azure

Хранение данных резервных копий в Azure сокращает инфраструктуру резервного копирования на виртуальной машине Azure Backup Server. Для операционного восстановления (Backup) Azure Backup Server хранит данные резервных копий на дисках Azure, подключенных к виртуальной машине. После присоединения дисков и дискового пространства к виртуальной машине Azure Backup Server управляет хранилищем. Объем хранилища зависит от числа и размера дисков, подключенных к каждой виртуальной машине Azure. Каждый размер виртуальной машины Azure имеет максимальное число дисков, которые можно подключить. Например, a2 — четыре диска, A3 — восемь дисков, а A4 — 16 дисков. Опять же, размер и количество дисков определяют общую емкость пула носителей резервных копий.

> [!IMPORTANT]
> *Не* следует хранить данные оперативного восстановления на подключенных к Azure Backup Server дисках более пяти дней. Если данные старше пяти дней, сохраните их в хранилище служб восстановления.

Чтобы хранить данные резервного копирования в Azure, создайте или используйте хранилище служб восстановления. При подготовке к резервному копированию Azure Backup Server рабочей нагрузки необходимо [настроить хранилище служб восстановления](#create-a-recovery-services-vault). После настройки каждый раз при запуске задания оперативного резервного копирования точка восстановления создается в хранилище. Каждое хранилище служб восстановления содержит до 9 999 точек восстановления. В зависимости от количества создаваемых точек восстановления и срока хранения данных резервного копирования можно хранить в течение многих лет. Например, можно создать ежемесячные точки восстановления и удержать их в течение пяти лет.

> [!IMPORTANT]
> Отправляйте ли резервные копии данных в Azure или не задерживайте их локально, необходимо зарегистрировать Azure Backup Server в хранилище служб восстановления.

### <a name="scale-deployment"></a>Масштабирование развертывания

Чтобы масштабировать развертывание, доступны следующие параметры:

- Увеличение **масштаба**: Увеличьте размер виртуальной машины Azure Backup Server из серии до DS3 и увеличьте локальное хранилище.
- **Разгрузка данных**: Отправка более старых данных в Azure и сохранение только самых новых данных в хранилище, подключенном к Azure Backup Server машине.
- **Масштабное развертывание**. добавьте дополнительные Azure Backup Server машины для защиты рабочих нагрузок.

### <a name="net-framework"></a>.NET Framework

На виртуальной машине должен быть установлен .NET Framework 3,5 SP1 или более поздней версии.

### <a name="join-a-domain"></a>Присоединить к домену

Azure Backup Server виртуальную машину необходимо присоединить к домену. Пользователь домена с правами администратора на виртуальной машине должен установить Azure Backup Server.

Azure Backup Server, развернутые на виртуальной машине Azure, могут выполнять резервное копирование рабочих нагрузок на виртуальных машинах в решении VMware для Azure. Для включения операции резервного копирования рабочие нагрузки должны находиться в одном домене.

## <a name="create-a-recovery-services-vault"></a>Создание хранилища служб восстановления

Хранилище служб восстановления — это сущность хранилища, в которой хранятся точки восстановления, созданные с течением времени. Он также содержит политики резервного копирования, связанные с защищенными элементами.

1. Войдите в подписку на [портале Azure](https://portal.azure.com/).

1. В меню слева выберите **Все службы**.

   ![В меню слева выберите все службы.](../backup/media/backup-create-rs-vault/click-all-services.png)

1. В диалоговом окне **все службы** введите **службы восстановления** и в списке выберите **хранилища служб восстановления** .

   ![Введите и выберите хранилища служб восстановления.](../backup/media/backup-create-rs-vault/all-services.png)

   После этого отобразится список хранилищ служб восстановления в подписке.

1. На информационной панели **Хранилища служб восстановления** выберите **Добавить**.

   ![Добавьте хранилище служб восстановления.](../backup/media/backup-create-rs-vault/add-button-create-vault.png)

   Откроется диалоговое окно **Хранилища служб восстановления**.

1. Введите значения для **имени**, **подписки**, **группы ресурсов**и **расположения**.

   ![Настройте хранилище служб восстановления.](../backup/media/backup-create-rs-vault/create-new-vault-dialog.png)

   - **Name** (Имя). Введите понятное имя для идентификации хранилища. Это имя должно быть уникальным в пределах подписки Azure. Укажите имя, которое содержит по крайней мере два символа, но не более 50 символов. Оно должно начинаться с буквы и может содержать только буквы, цифры и дефисы.
   - **Подписка**: Выберите подписку, которую нужно использовать. Если вы являетесь участником только одной подписки, будет отображено ее имя. Если неизвестно, какую подписку нужно использовать, оставьте подписку по умолчанию (или предлагаемую подписку). Вариантов будет несколько только в том случае, если рабочая или учебная учетная запись связана с несколькими подписками Azure.
   - **Группа ресурсов.** Используйте имеющуюся группу ресурсов или создайте новую. Выберите **Использовать существующий**, чтобы просмотреть список доступных групп ресурсов в своей подписке, а затем выберите ресурс из раскрывающегося списка. Чтобы создать группу ресурсов, щелкните **Создать** и укажите ее имя.
   - **Расположение.** Выберите географический регион для хранилища. Чтобы создать хранилище для защиты виртуальных машин Azure VMware, хранилище *должно* находиться в том же регионе, что и частное облако решения Azure VMware.

1. Когда все будет готово к созданию хранилища Служб восстановления, выберите **Создать**.

   ![Создайте хранилище служб восстановления.](../backup/media/backup-create-rs-vault/click-create-button.png)

   Для создания хранилища Служб восстановления может потребоваться некоторое время. Отслеживайте уведомления о состоянии в области **уведомлений** в правом верхнем углу портала. После создания хранилища оно отображается в списке хранилищ служб восстановления. Если вы не видите хранилища, выберите **Обновить**.

   ![Обновите список резервных хранилищ.](../backup/media/backup-create-rs-vault/refresh-button.png)

## <a name="set-storage-replication"></a>Настройка репликации хранилища

Параметр репликация хранилища позволяет выбрать между географически избыточным хранилищем (по умолчанию) и локально избыточным хранилищем. Геоизбыточное хранилище копирует данные в учетную запись хранения в дополнительный регион, делая их устойчивыми. Локально избыточное хранилище — это более дешевый вариант, который не является устойчивым. Дополнительные сведения о геоизбыточных и локально избыточных вариантах хранения см. в статье [избыточность хранилища Azure](../storage/common/storage-redundancy.md).

> [!IMPORTANT]
> Изменение параметра **типа репликации хранилища локально избыточное или геоизбыточное** для хранилища служб восстановления должно быть выполнено до настройки резервного копирования в хранилище. После настройки резервного копирования возможность его изменения отключается, и вы не можете изменить тип репликации хранилища.

1. В **хранилище служб восстановления**выберите новое хранилище. 

1. В разделе **Параметры** выберите **Свойства**. В разделе **Конфигурация архивации**выберите **Обновить**.

1. Выберите тип репликации хранилища и нажмите кнопку **сохранить**.

## <a name="download-and-install-the-software-package"></a>Скачивание и установка пакета программного обеспечения

Выполните действия, описанные в этом разделе, чтобы скачать, извлечь и установить пакет программного обеспечения.

### <a name="download-the-software-package"></a>Загрузка пакета программного обеспечения

1. Войдите на [портал Azure](https://portal.azure.com/).

1. Если хранилище служб восстановления уже открыто, перейдите к следующему шагу. Если хранилище служб восстановления не открыто и вы находитесь в портал Azure, в главном меню выберите **Обзор**.

   1. В списке ресурсов введите **службы восстановления**.

   1. Как только вы начнете вводить символы, список отфильтруется соответствующим образом. Когда вы увидите пункт **Хранилища служб восстановления**, щелкните его.

   ![Создание хранилища служб восстановления, шаг 1](../backup/media/backup-azure-microsoft-azure-backup/open-recovery-services-vault.png)

1. в котором нужно выбрать хранилище.

   Затем откроется панель мониторинга выбранного хранилища.

   ![Затем откроется панель мониторинга выбранного хранилища.](../backup/media/backup-azure-microsoft-azure-backup/vault-dashboard.png)

   По умолчанию открывается параметр **Параметры** . Если параметр закрыт, выберите **Параметры** , чтобы открыть его.

   ![По умолчанию открывается параметр параметры. Если параметр закрыт, выберите параметры, чтобы открыть его.](../backup/media/backup-azure-microsoft-azure-backup/vault-setting.png)

1. Выберите **резервное копирование** , чтобы открыть мастер **Начало работы** .

   ![Выберите резервное копирование, чтобы открыть мастер начало работы.](../backup/media/backup-azure-microsoft-azure-backup/getting-started-backup.png)

1. В открывшемся окне:

   1. В меню **где работает Рабочая нагрузка?** выберите **локальный**.

      :::image type="content" source="media/azure-vmware-solution-backup/deploy-mabs-on-premises-workload.png" alt-text="Azure Backup Server развертывается как виртуальная машина Azure &quot;инфраструктура как услуга&quot; (IaaS) для защиты виртуальных машин Azure VMware.":::

   1. В меню **что вы хотите создать резервную копию?** выберите рабочие нагрузки, которые требуется защитить с помощью Azure Backup Server.

   1. Выберите **Подготовка инфраструктуры** , чтобы скачать и установить Azure Backup Server и учетные данные хранилища.

      :::image type="content" source="media/azure-vmware-solution-backup/deploy-mabs-prepare-infrastructure.png" alt-text="Azure Backup Server развертывается как виртуальная машина Azure &quot;инфраструктура как услуга&quot; (IaaS) для защиты виртуальных машин Azure VMware.":::

1. В открывшемся окне **Подготовка инфраструктуры** :

   1. Щелкните ссылку **скачать** , чтобы установить Azure Backup Server.

   1. 1. Выберите **уже скачанный или используйте последнюю установку Azure Backup Server** а затем **Скачайте** , чтобы скачать учетные данные хранилища. Эти учетные данные будут использоваться при регистрации Azure Backup Server в хранилище служб восстановления. По ссылкам вы перейдите в центр загрузки, где вы загрузили пакет программного обеспечения.

   :::image type="content" source="media/azure-vmware-solution-backup/deploy-mabs-prepare-infrastructure2.png" alt-text="Azure Backup Server развертывается как виртуальная машина Azure &quot;инфраструктура как услуга&quot; (IaaS) для защиты виртуальных машин Azure VMware.":::

1. На странице загрузки выберите все файлы и нажмите кнопку **Далее**.

   > [!NOTE]
   > Необходимо загрузить все файлы в одну и ту же папку. Так как размер загружаемых файлов превышает 3 ГБ, загрузка может занять до 60 минут. 

   ![На странице загрузки выберите все файлы и нажмите кнопку Далее.](../backup/media/backup-azure-microsoft-azure-backup/downloadcenter.png)

### <a name="extract-the-software-package"></a>Извлечение пакета программного обеспечения

Если пакет программного обеспечения загружен на другой сервер, скопируйте файлы на виртуальную машину, созданную для развертывания Azure Backup Server.

> [!WARNING]
> Для извлечения файлов установки требуется минимум 4 ГБ свободного места.

1. После скачивания всех файлов дважды щелкните **MicrosoftAzureBackupInstaller.exe** , чтобы открыть мастер установки **Microsoft Azure Backup** , а затем нажмите кнопку **Далее**.

1. Выберите расположение для извлечения файлов и нажмите кнопку **Далее**.

1. Нажмите кнопку **извлечь** , чтобы начать процесс извлечения.

   ![Нажмите кнопку извлечь, чтобы начать процесс извлечения.](../backup/media/backup-azure-microsoft-azure-backup/extract/03.png)

1. После извлечения выберите параметр для **выполнения setup.exe** а затем нажмите кнопку **Готово**.

> [!TIP]
> Можно также выбрать файл setup.exe из папки, в которую был извлечен пакет программного обеспечения.

### <a name="install-the-software-package"></a>Установка программного пакета

1. В окне установки в разделе **Установка**выберите **Microsoft Azure Backup** , чтобы открыть мастер установки.

   ![В окне установки в разделе Установка выберите Microsoft Azure Backup, чтобы открыть мастер установки.](../backup/media/backup-azure-microsoft-azure-backup/launch-screen2.png)

1. На экране **приветствия** нажмите кнопку **Далее** , чтобы перейти на страницу **проверки готовности к установке** .

1. Щелкните **проверить еще раз** , чтобы определить, соответствуют ли оборудование и программное обеспечение предварительным требованиям для Azure Backup Server. Если это выполнено успешно, нажмите кнопку **Далее**.

   ![ Щелкните проверить еще раз, чтобы определить, соответствуют ли оборудование и программное обеспечение предварительным требованиям для Azure Backup Server. Если это выполнено успешно, нажмите кнопку Далее.](../backup/media/backup-azure-microsoft-azure-backup/prereq/prereq-screen2.png)

1. Пакет установки Azure Backup Server входит в состав необходимых SQL Server двоичных файлов. При запуске новой Azure Backup Server установки выберите **установить новый экземпляр SQL Server с этим** параметром установки. Затем выберите **проверить и установить**.

   ![Пакет установки Azure Backup Server входит в состав необходимых SQL Server двоичных файлов.](../backup/media/backup-azure-microsoft-azure-backup/sql/01.png)

   > [!NOTE]
   > Если вы хотите использовать собственный экземпляр SQL Server, поддерживаемыми версиями SQL Server являются SQL Server 2014 с пакетом обновления 1 (SP1) или более поздней версии, 2016 и 2017. Все версии SQL Server должны быть 64-разрядными уровня Standard или Enterprise. Экземпляр, используемый Azure Backup Server, должен быть только локальным; Он не может быть удаленным. Если для Azure Backup Server используется существующий экземпляр SQL Server, программа установки поддерживает только использование *именованных экземпляров* SQL Server.

   Если произошел сбой с рекомендацией перезагрузки компьютера, сделайте это и выберите **проверить снова**. При возникновении любых проблем с конфигурацией SQL Server перенастройте SQL Server в соответствии с рекомендациями по SQL Server. Затем повторите попытку установки или обновления Azure Backup Server с помощью существующего экземпляра SQL Server.

   **Настройка вручную**

   При использовании собственного экземпляра SQL Server убедитесь, что вы добавили Builtin \ администраторы в роль sysadmin в роль sysadmin базы данных master.

   **Настройка служб Reporting Services с помощью SQL Server 2017**

   При использовании экземпляра SQL Server 2017 необходимо вручную настроить SQL Server 2017 Reporting Services (SSRS). После настройки служб SSRS обязательно присвойте свойству "свойства **" для служб** SSRS значение **true**. Если задано **значение true**, Azure Backup Server предполагает, что службы SSRS уже настроены и пропускают конфигурацию служб SSRS.

   Чтобы проверить состояние конфигурации SSRS, выполните команду:

   ```powershell
   $configset =Get-WmiObject –namespace 
   "root\Microsoft\SqlServer\ReportServer\RS_SSRS\v14\Admin" -class 
   MSReportServer_ConfigurationSetting -ComputerName localhost

   $configset.IsInitialized
   ```

   Для этой конфигурации SSRS используйте следующие значения:

   * **Учетная запись службы**. **Используйте встроенную учетную запись** **Network Service**.
   * **URL веб-службы**: необходимо ** \<SQLInstanceName> ReportServer_** **виртуальный каталог** .
   * **База данных**: **DatabaseName** должно быть **ReportServer \<SQLInstanceName> $**.
   * **URL-адрес портала**: **виртуальный каталог** должен **быть \<SQLInstanceName> Reports_**.

   [Подробнее](/sql/reporting-services/report-server/configure-and-administer-a-report-server-ssrs-native-mode) о конфигурации SSRS.

   > [!NOTE]
   > [Условия использования Microsoft Online Services](https://www.microsoft.com/licensing/product-licensing/products) (OST) регулируют лицензирование SQL Server, используемых в качестве базы данных для Azure Backup Server. В соответствии с OST-файла используйте только SQL Server, Объединенные с Azure Backup Server в качестве базы данных для Azure Backup Server.

1. После успешного завершения установки нажмите кнопку **Далее**.

1. Укажите расположение для установки файлов Microsoft Azure Backup Server и нажмите кнопку **Далее**.

   > [!NOTE]
   > Для резервного копирования в Azure требуется вспомогательное расположение. Убедитесь, что вспомогательное расположение составляет не менее 5% данных, запланированных для резервного копирования в облако. Для защиты диска необходимо настроить отдельные диски после завершения установки. Дополнительные сведения о пулах носителей см. в статье [Настройка пулов носителей и дискового хранилища](/previous-versions/system-center/system-center-2012-r2/hh758075(v=sc.12)).

   ![Укажите расположение для установки файлов Microsoft Azure Backup Server и нажмите кнопку Далее.](../backup/media/backup-azure-microsoft-azure-backup/space-screen.png)

1. Укажите надежный пароль для ограниченных локальных учетных записей пользователей и нажмите кнопку **Далее**.

   ![Укажите надежный пароль для ограниченных локальных учетных записей пользователей и нажмите кнопку Далее.](../backup/media/backup-azure-microsoft-azure-backup/security-screen.png)

1. Выберите, следует ли использовать Центр обновления Майкрософт для проверки наличия обновлений, и нажмите кнопку **Далее**.

   > [!NOTE]
   > Рекомендуется Центр обновления Windows перенаправление на Центр обновления Майкрософт, которое обеспечивает безопасность и важные обновления для Windows и других продуктов, таких как Azure Backup Server.

   ![Выберите, следует ли использовать Центр обновления Майкрософт для проверки наличия обновлений, и нажмите кнопку Далее.](../backup/media/backup-azure-microsoft-azure-backup/update-opt-screen2.png)

1. Проверьте **сводку параметров**и нажмите кнопку **установить**.

   Установка выполняется поэтапно. 
   - Первый этап устанавливает агент Службы восстановления Microsoft Azure.
   - Второй этап проверяет подключение к Интернету. Если он доступен, можно продолжить установку. Если оно недоступно, необходимо указать сведения о прокси-сервере для подключения к Интернету. 
   - На последнем этапе проверяется необходимое программное обеспечение. Если не установлено, то любое отсутствующее программное обеспечение устанавливается вместе с агентом Службы восстановления Microsoft Azure.

1. Нажмите кнопку **Обзор** , чтобы найти учетные данные хранилища, чтобы зарегистрировать компьютер в хранилище служб восстановления, а затем нажмите кнопку **Далее**.

1. Выберите парольную фразу для шифрования или расшифровки данных, передаваемых между Azure и локальной средой.

   > [!TIP]
   > Вы можете автоматически создать парольную фразу или указать минимальную 16-символьную парольную фразу.

1. Введите расположение для сохранения парольной фразы, а затем нажмите кнопку **Далее** , чтобы зарегистрировать сервер.

   > [!IMPORTANT]
   > Сохраните парольную фразу в надежном месте, отличном от локального сервера. Настоятельно рекомендуется использовать Azure Key Vault для хранения парольной фразы.

   После завершения установки агента Службы восстановления Microsoft Azure шаг установки переходит на установку и настройку SQL Server и компонентов Azure Backup Server.

   ![После завершения установки агента Службы восстановления Microsoft Azure шаг установки переходит на установку и настройку SQL Server и компонентов Azure Backup Server.](../backup/media/backup-azure-microsoft-azure-backup/final-install/venus-installation-screen.png)

1. По завершении шага установки нажмите кнопку **Закрыть**.

### <a name="install-update-rollup-1"></a>Установка накопительного пакета обновления 1

Установка накопительного пакета обновления 1 для Azure Backup Server v3 обязательна, прежде чем можно будет защитить рабочие нагрузки.  Исправления ошибок и инструкции по установке можно найти в статье базы [знаний](https://support.microsoft.com/en-us/help/4534062/).

## <a name="add-storage-to-azure-backup-server"></a>Добавление хранилища для Azure Backup Server

Azure Backup Server v3 поддерживает Современное хранилище резервных копий, которые предлагают:

-  Экономия хранилища 50%.
-  Резервные копии выполняются в три раза быстрее.
-  Более эффективное хранилище.
-  Хранилище, поддерживающее рабочую нагрузку.

### <a name="volumes-in-azure-backup-server"></a>Тома в Azure Backup Server

Добавьте диски данных с требуемой емкостью хранилища Azure Backup Server виртуальной машины, если они еще не добавлены.

Azure Backup Server v3 принимает только тома хранилища. При добавлении тома Azure Backup Server форматирует его в отказоустойчивую файловую систему (ReFS), которая требуется Современное хранилище резервных копий.

### <a name="add-volumes-to-azure-backup-server-disk-storage"></a>Добавление томов в Azure Backup Server дисковый накопитель

1. На панели **управления** повторно просканируйте хранилище и нажмите кнопку **Добавить**. 

1. Выберите из доступных томов, которые нужно добавить в пул носителей. 

1. После добавления доступных томов дайте им понятное имя, помогающее управлять ими. 

1. Нажмите кнопку **ОК** , чтобы отформатировать эти тома до ReFS, чтобы Azure Backup Server могли использовать преимущества современное хранилище резервных копий.


## <a name="next-steps"></a>Дальнейшие действия

Перейдите к следующему руководству, чтобы узнать, как настроить резервное копирование виртуальных машин VMware, работающих в решении VMware для Azure, с помощью Azure Backup Server.

> [!div class="nextstepaction"]
> [Настройка резервного копирования виртуальных машин решения Azure VMware](backup-azure-vmware-solution-virtual-machines.md)
