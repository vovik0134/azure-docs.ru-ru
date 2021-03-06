---
title: Архитектура аварийного восстановления Azure в Azure в Azure Site Recovery
description: Общие сведения об архитектуре, используемой при настройке аварийного восстановления между регионами Azure для виртуальных машин Azure с помощью службы Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 3/13/2020
ms.author: raynew
ms.openlocfilehash: 64d1084fd7025c74676977f065062e5e94dabf1d
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/17/2020
ms.locfileid: "97652251"
---
# <a name="azure-to-azure-disaster-recovery-architecture"></a>Архитектура аварийного восстановления из Azure в Azure


В этой статье описывается архитектура, компоненты и процессы, используемые при развертывании аварийного восстановления для виртуальных машин Azure на основе службы [Azure Site Recovery](site-recovery-overview.md). После настройки аварийного восстановления виртуальные машины Azure непрерывно реплицируются в другой целевой регион. В случае сбоя вы сможете выполнить для таких виртуальных машин отработку отказа в дополнительный регион и получать доступ к ним в этом регионе. Когда нормальная работа возобновится, можно восстановить исходное размещение и продолжить работу в основном расположении.



## <a name="architectural-components"></a>Компоненты архитектуры

В следующей таблице перечислены компоненты, участвующие в аварийном восстановлении виртуальных машин Azure.

**Компонент** | **Требования**
--- | ---
**Виртуальные машины в исходном регионе** | Одна или несколько виртуальных машин Azure в [поддерживаемом исходном регионе](azure-to-azure-support-matrix.md#region-support).<br/><br/> Виртуальные машины могут работать под управлением любой [поддерживаемой операционной системы](azure-to-azure-support-matrix.md#replicated-machine-operating-systems).
**Хранилище исходной виртуальной машины** | На виртуальных машинах Azure могут использоваться управляемые или неуправляемые диски, распределенные между несколькими учетными записями хранения.<br/><br/>См. дополнительные сведения [о поддерживаемых хранилищах Azure](azure-to-azure-support-matrix.md#replicated-machines---storage).
**Сети исходных виртуальных машин** | Виртуальные машины могут размещаться в одной или нескольких подсетях виртуальной сети в исходном регионе. См. дополнительные сведения о [требованиях к сети](azure-to-azure-support-matrix.md#replicated-machines---networking).
**Учетная запись хранения кэша.** | Вам нужна учетная запись хранения кэша в исходной сети. В процессе репликации все изменения виртуальной машины сначала сохраняются в кэше, а затем отправляются в целевое хранилище.  Учетные записи хранения кэша должны быть стандартными.<br/><br/> Использование кэша сводит к минимуму влияние на рабочие приложения, запущенные на виртуальной машине.<br/><br/> См. дополнительные сведения о [требованиях к хранилищу кэша](azure-to-azure-support-matrix.md#cache-storage). 
**Целевые ресурсы** | Целевые ресурсы используются во время репликации и при отработке отказа. Site Recovery может назначить целевой ресурс по умолчанию, или вы можете создать и настроить ресурсы самостоятельно.<br/><br/> Убедитесь, что вы можете создавать виртуальные машины в целевом регионе, а ваша подписка имеет достаточно ресурсов для поддержки тех размеров виртуальных машин, которые потребуются в целевом регионе. 

![Схема, показывающая исходную и целевую репликацию.](./media/concepts-azure-to-azure-architecture/enable-replication-step-1-v2.png)

## <a name="target-resources"></a>Целевые ресурсы

Когда вы включаете репликацию для виртуальной машины, Site Recovery предоставляет возможность автоматически создать целевые ресурсы. 

**Целевой ресурс** | **Параметр по умолчанию**
--- | ---
**Целевая подписка** | Совпадает с исходной подпиской.
**Целевая группа ресурсов** | Группа ресурсов, в которой размещаются виртуальные машины после отработки отказа.<br/><br/> Она может располагаться в любом регионе, за исключением исходного.<br/><br/> Site Recovery создает в целевом регионе новую группу ресурсов с суффиксом asr.<br/><br/>
**Целевая виртуальная сеть** | Виртуальная сеть, в которой реплицированные виртуальные машины будут находиться после отработки отказа. Сетевое сопоставление создается между исходными и целевыми виртуальными сетями и наоборот.<br/><br/> Site Recovery создает новую виртуальную сеть и подсеть с суффиксом asr.
**Целевая учетная запись хранения** |  Если виртуальная машина не использует управляемый диск, данные реплицируются в эту учетную запись хранения.<br/><br/> Site Recovery создает в целевом регионе новую учетную запись хранения, которая будет зеркальной копией исходной учетной записи хранения.
**Управляемые диски реплики** | Если виртуальная машина использует управляемый диск, данные реплицируются на эти управляемые диски.<br/><br/> Site Recovery создает в регионе хранения управляемые диски реплики, которые будут зеркальной копией исходных.
**Целевая группа доступности** |  Группа доступности, в которой размещаются реплицированные виртуальные машины после отработки отказа.<br/><br/> Site Recovery создает в целевом регионе группу доступности с суффиксом asr для размещения виртуальных машин, включенных в группу доступности в исходном расположении. Если группа доступности уже существует, используется именно она, а новая не создается.
**Целевые зоны доступности** | Если целевой регион поддерживает зоны доступности, Site Recovery назначает в нем тот же номер зоны, что и в исходном регионе.

### <a name="managing-target-resources"></a>Управление целевыми ресурсами

Вы можете управлять целевыми ресурсами следующим образом.

- Можно изменять параметры целевого объекта при включении репликации.
- Можно изменять параметры целевого объекта, когда репликация уже работает. Обратите внимание, что номер SKU по умолчанию для виртуальной машины целевого региона совпадает с номером SKU исходной виртуальной машины (или следующим лучшим доступным номером SKU в сравнении с номером SKU исходной виртуальной машины). Аналогично другим ресурсам, таким как Целевая группа ресурсов, целевое имя и другие, номер SKU виртуальной машины целевого региона можно также обновить после выполнения репликации. Ресурс, который не может быть обновлен, является типом доступности (одиночный экземпляр, набор или зона). Чтобы изменить этот параметр, придется отключить репликацию и повторно включить ее после изменения. 


## <a name="replication-policy"></a>Политика репликации 

По умолчанию при включении репликации виртуальной машины Azure Site Recovery создает политику репликации, стандартные параметры которой представлены в следующей таблице.

**Параметр политики** | **Сведения** | **Default**
--- | --- | ---
**Хранение точки восстановления** | Указывает, как долго в Site Recovery хранятся точки восстановления. | 24 часа
**Периодичность создания моментальных снимков с согласованием приложений** | Определяет, как часто Site Recovery создает моментальные снимки с согласованностью на уровне приложений. | Каждые четыре часа

### <a name="managing-replication-policies"></a>Управление политиками репликации

Вы можете выполнять следующие действия для изменения заданных по умолчанию параметров политик репликации и управления ими.
- Можно изменять параметры при включении репликации.
- Можно в любой момент создать политику репликации и применить ее при включении репликации.

### <a name="multi-vm-consistency"></a>Согласованность нескольких виртуальных машин

Если вы хотите, чтобы несколько виртуальных машин реплицировались вместе и для них создавались отказоустойчивые и согласованные на уровне приложений точки восстановления для отработки отказа, объедините такие машины в группу репликации. Согласованность нескольких виртуальных машин влияет на производительность рабочей нагрузки, и ее следует применять только для виртуальных машин, на которых требуется согласованность рабочих нагрузок между всеми компьютерами. 



## <a name="snapshots-and-recovery-points"></a>Моментальные снимки и точки восстановления

Точки восстановления создаются на основе моментальных снимков дисков виртуальной машины, сделанных в определенный момент времени. При отработке отказа виртуальной машины эти точки восстановления используются для восстановления виртуальной машины в целевом расположении.

Обычно при отработке отказа важно, чтобы виртуальная машина запускалась без повреждения или потери данных и чтобы данные на этой виртуальной машине сохраняли согласованность на уровне операционной системы и выполняемых приложений. Это зависит от типа создаваемых моментальных снимков.

Site Recovery создает моментальные снимки следующим образом.

1. Site Recovery по умолчанию создает моментальные снимки данных без учета состояния приложений, а также моментальные снимки, согласованные на уровне приложений, если вы укажете для них частоту создания.
2. Точки восстановления создаются на основе моментальных снимков и сохраняются в соответствии с параметрами хранения, указанными в политике репликации.

### <a name="consistency"></a>Согласованность

В следующей таблице описываются различные виды согласованности.

### <a name="crash-consistent"></a>Без учета состояния приложений

**Описание** | **Сведения** | **Рекомендация**
--- | --- | ---
Моментальный снимок без учета состояния приложения содержит все данные, которые были записаны на диск в момент создания этого моментального снимка. Он не содержит никакой информации из памяти компьютера.<br/><br/> Он сохраняет данные точно так же, как если бы в момент создания моментального снимка произошел сбой виртуальной машины или отключение питания сервера.<br/><br/> Моментальный снимок без учета состояния не гарантирует согласованность данных для операционной системы или приложений этой виртуальной машины. | По умолчанию Site Recovery создает точки восстановления без учета состояния приложений каждые пять минут. Этот параметр нельзя изменить.<br/><br/>  | Сейчас большинство приложений успешно восстанавливаются по точкам восстановления без учета состояния.<br/><br/> Точек восстановления без учета состояния приложений обычно вполне достаточно для репликации операционных систем и приложений, таких как DHCP-серверы и серверы печати.

### <a name="app-consistent"></a>Согласованность на уровне приложений

**Описание** | **Сведения** | **Рекомендация**
--- | --- | ---
Точки восстановления с согласованностью на уровне приложений создаются на основе моментальных снимков с согласованностью на уровне приложений.<br/><br/> Моментальные снимки с согласованностью на уровне приложений содержат все сведения, включаемые в моментальный снимок без учета состояния приложений, а также все данные в памяти и незаконченные транзакции. | Моментальные снимки с согласованностью на уровне приложений создаются с помощью службы теневого копирования томов (VSS).<br/><br/>   1) Azure Site Recovery использует метод резервного копирования только для копирования (VSS_BT_COPY), который не изменяет время и порядковый номер резервной копии журнала транзакций Microsoft SQL. </br></br> 2) при инициации создания моментального снимка VSS выполняет операцию копирования на томе (COW).<br/><br/>   3) перед выполнением COW служба VSS информирует каждое приложение на компьютере о том, что ему требуется очистить его резидентные данные на диск.<br/><br/>   4) VSS позволяет приложению резервного копирования и аварийного восстановления (в данном случае Site Recovery) считывать данные моментального снимка и продолжать работу. | Вы можете указать частоту создания моментальных снимков с согласованностью на уровне приложений. Эта частота должна быть меньше, чем период хранения точек восстановления. Например, если для хранения точек восстановления используется значение по умолчанию 24 часа, настройте частоту создания с интервалом менее 24 часов.<br/><br/>Такие моментальные снимки более сложны и требуют больше времени на создание, чем моментальные снимки без учета состояния приложений.<br/><br/> Они снижают производительность приложений, которые выполняются на реплицируемой виртуальной машине. 

## <a name="replication-process"></a>Процесс репликации

При включении репликации для виртуальной машины Azure происходит следующее.

1. На виртуальной машине автоматически устанавливается расширение службы мобильности Site Recovery.
2. Это расширение регистрирует виртуальную машину в Site Recovery.
3. Для виртуальной машины начинается постоянная репликация.  Все операции записи данных немедленно передаются в учетную запись хранения кэша в исходном расположении.
4. Site Recovery обрабатывает данные кэша и отправляет их в целевую учетную запись хранения или на управляемые диски реплики.
5. По результатам обработки этих данных каждые пять минут создаются отказоустойчивые точки восстановления. Точки восстановления с согласованностью на уровне приложений создаются в соответствии с параметром, указанным в политике репликации.

![Схема, показывающая процесс репликации, шаг 2.](./media/concepts-azure-to-azure-architecture/enable-replication-step-2-v2.png)

**Процесс репликации**

## <a name="connectivity-requirements"></a>Требования к подключению

 Реплицируемая виртуальная машина Azure должна иметь возможность исходящего подключения. Для Site Recovery никогда не требуется входящее подключение к виртуальной машине. 

### <a name="outbound-connectivity-urls"></a>Исходящие подключения для URL-адресов

Если виртуальной машине разрешен исходящий доступ только к определенным URL-адресам, включите в этот список следующие URL-адреса.

| **имя**;                  | **Коммерческие организации**                               | **Государственные организации**                                 | **Описание** |
| ------------------------- | -------------------------------------------- | ---------------------------------------------- | ----------- |
| Память                   | `*.blob.core.windows.net`                  | `*.blob.core.usgovcloudapi.net` | Позволяет записывать данные из виртуальной машины в учетную запись хранения кэша в исходном регионе. |
| Azure Active Directory    | `login.microsoftonline.com`                | `login.microsoftonline.us`                   | Обеспечивает авторизацию и проверку подлинности URL-адресов службы Site Recovery. |
| Репликация               | `*.hypervrecoverymanager.windowsazure.com` | `*.hypervrecoverymanager.windowsazure.com`     | Позволяет виртуальной машине взаимодействовать со службой Site Recovery. |
| Служебная шина               | `*.servicebus.windows.net`                 | `*.servicebus.usgovcloudapi.net`             | Позволяет виртуальной машине записывать данные мониторинга и диагностики службы Site Recovery. |
| Key Vault                 | `*.vault.azure.net`                        | `*.vault.usgovcloudapi.net`                  | Разрешает доступ для включения репликации для виртуальных машин с поддержкой ADE через портал |
| Служба автоматизации Azure          | `*.automation.ext.azure.com`               | `*.azure-automation.us`                      | Позволяет включить автоматическое обновление агента мобильности для реплицированного элемента с помощью портала |

### <a name="outbound-connectivity-for-ip-address-ranges"></a>Исходящие подключения для диапазонов IP-адресов

Если управление исходящими подключениями виртуальной машины настроено по IP-адресам, разрешите следующие адреса.
Обратите внимание, что сведения о требованиях к сетевому подключению можно найти в [техническом документе "Сетевые](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags) подключения". 

#### <a name="source-region-rules"></a>Правила исходного региона

**Правило** |  **Сведения** | **Тег службы**
--- | --- | --- 
Allow HTTPS outbound: port 443 | Разрешает диапазоны, соответствующие учетным записям хранения в исходном регионе. | Объема.\<region-name>
Allow HTTPS outbound: port 443 | Разрешить диапазоны, соответствующие Azure Active Directory (Azure AD)  | AzureActiveDirectory
Allow HTTPS outbound: port 443 | Разрешить диапазоны, соответствующие концентратору событий в целевом регионе. | Евентшуб.\<region-name>
Allow HTTPS outbound: port 443 | Разрешить диапазоны, соответствующие Azure Site Recovery  | AzureSiteRecovery
Allow HTTPS outbound: port 443 | Разрешить диапазоны, соответствующие Azure Key Vault (это необходимо только для включения репликации виртуальных машин с поддержкой ADE через портал). | AzureKeyVault
Allow HTTPS outbound: port 443 | Разрешить диапазоны, соответствующие контроллеру службы автоматизации Azure (это требуется только для включения автоматического обновления агента мобильности для реплицируемого элемента с помощью портала) | GuestAndHybridManagement

#### <a name="target-region-rules"></a>Правила целевого региона

**Правило** |  **Сведения** | **Тег службы**
--- | --- | --- 
Allow HTTPS outbound: port 443 | Разрешить диапазоны, соответствующие учетным записям хранения в целевом регионе | Объема.\<region-name>
Allow HTTPS outbound: port 443 | Разрешить диапазоны, соответствующие Azure AD  | AzureActiveDirectory
Allow HTTPS outbound: port 443 | Разрешить диапазоны, соответствующие концентратору событий в исходном регионе. | Евентшуб.\<region-name>
Allow HTTPS outbound: port 443 | Разрешить диапазоны, соответствующие Azure Site Recovery  | AzureSiteRecovery
Allow HTTPS outbound: port 443 | Разрешить диапазоны, соответствующие Azure Key Vault (это необходимо только для включения репликации виртуальных машин с поддержкой ADE через портал). | AzureKeyVault
Allow HTTPS outbound: port 443 | Разрешить диапазоны, соответствующие контроллеру службы автоматизации Azure (это требуется только для включения автоматического обновления агента мобильности для реплицируемого элемента с помощью портала) | GuestAndHybridManagement


#### <a name="control-access-with-nsg-rules"></a>Управление доступом с помощью правил группы безопасности сети

Если для управления подключениями виртуальной машины используется фильтрация входящего и исходящего сетевого трафика сетей или подсетей Azure на основе [правил NSG](../virtual-network/network-security-groups-overview.md), учитывайте следующие требования.

- Правила NSG для исходного региона Azure должны всегда разрешать исходящий трафик репликации.
- Мы рекомендуем создать правила в тестовой среде, прежде чем применять их к рабочей среде.
- Используйте [теги служб](../virtual-network/network-security-groups-overview.md#service-tags) вместо конкретных отдельных IP-адресов.
    - Теги служб обозначают группы префиксов IP-адресов, что позволяет упростить создание правил безопасности.
    - Корпорация Майкрософт автоматически обновляет теги служб по мере необходимости. 
 
См. дополнительные сведения об [исходящих подключениях](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags) для Site Recovery и [управлении подключениями с помощью групп безопасности сети](concepts-network-security-group-with-site-recovery.md).


### <a name="connectivity-for-multi-vm-consistency"></a>Подключения для согласованности между виртуальными машинами

Если включить согласованность между виртуальными машинами, компьютеры в группе репликации будут обмениваться данными друг с другом через порт 20004.
- Убедитесь, что нет устройства брандмауэра, которое блокирует внутренний обмен данными между виртуальными машинами через порт 20004.
- Если вы хотите, чтобы виртуальные машины Linux входили в группу репликации, нужно вручную открыть исходящий трафик через порт 20004 согласно указаниям для конкретной версии Linux.




## <a name="failover-process"></a>Процесс отработки отказа

При запуске отработки отказа виртуальные машины создаются в целевой группе ресурсов, целевой виртуальной сети, целевой подсети и целевой группе доступности. Во время отработки отказа можно использовать любую точку восстановления.

![Схема, показывающая процесс отработки отказа с исходной и целевой средой.](./media/concepts-azure-to-azure-architecture/failover-v2.png)

## <a name="next-steps"></a>Дальнейшие действия

Ознакомьтесь с [кратким руководством по репликации](azure-to-azure-quickstart.md) виртуальных машин Azure в дополнительный регион.