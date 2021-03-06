---
title: Взаимодействие функций с группами доступности и прослушивателем DNN
description: 'Дополнительные сведения о дополнительных вопросах при работе с определенными SQL Serverными функциями и прослушивателем распределенного сетевого имени (DNN) с группой доступности Always On на SQL Server на виртуальных машинах Azure. '
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 10/08/2020
ms.author: mathoma
ms.openlocfilehash: 19b4b7407468b19419e2f85193b1f8fb6ace39c3
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/12/2020
ms.locfileid: "97359410"
---
# <a name="feature-interoperability-with-ag-and-dnn-listener"></a>Взаимодействие компонентов с помощью DNN и прослушивателя AG 
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Существуют некоторые SQL Server функции, использующие жестко запрограммированное имя виртуальной сети (VNN). Таким образом, при использовании прослушивателя "имя распределенной сети" (DNN) с группой доступности Always On и SQL Server на виртуальных машинах Azure могут возникнуть некоторые дополнительные соображения. 

В этой статье подробно рассматриваются SQL Server возможности и взаимодействие с прослушивателем DNN группы доступности. 


## <a name="client-drivers"></a>Клиентские драйверы

Для драйверов ODBC, OLEDB, ADO.NET, JDBC, PHP и Node.js пользователям необходимо явным образом указать имя и порт прослушивателя DNN в качестве имени сервера в строке подключения. Чтобы обеспечить быстрое подключение при отработке отказа, добавьте `MultiSubnetFailover=True` в строку подключения, если клиент SQL его поддерживает. 

## <a name="tools"></a>Инструменты

Пользователям [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms), [sqlcmd](/sql/tools/sqlcmd-utility), [Azure Data Studio](/sql/azure-data-studio/what-is)и [SQL Server Data Tools](/sql/ssdt/sql-server-data-tools) необходимо явно указать имя прослушивателя DNN и порт в качестве имени сервера в строке подключения для подключения к прослушивателю. 

Создание прослушивателя DNN с помощью графического пользовательского интерфейса SQL Server Management Studio (SSMS) в настоящее время не поддерживается. 


## <a name="availability-groups-and-fci"></a>Группы доступности и FCI

Группу доступности Always On можно настроить с помощью экземпляра отказоустойчивого кластера (FCI) в качестве одной из реплик. Чтобы эта конфигурация работала с прослушивателем DNN, [экземпляр отказоустойчивого кластера также должен использовать DNN](failover-cluster-instance-distributed-network-name-dnn-configure.md) , так как невозможно разместить виртуальный IP-адрес FCI в списке доступности DNN IP. 

В этой конфигурации URL-адрес конечной точки зеркального отображения для реплики FCI должен использовать DNN FCI. Аналогично, если FCI используется в качестве реплики только для чтения, маршрутизация только для чтения в реплике FCI должна использовать FCI DNN. 

Для конечной точки зеркального отображения используется формат: `ENDPOINT_URL = 'TCP://<FCI DNN DNS name>:<mirroring endpoint port>'` . 

Например, если DNS-имя FCI DNN имеет значение `dnnlsnr` , а `5022` — Порт конечной точки зеркального отображения FCI, фрагмент кода TRANSACT-SQL (T-SQL) для создания URL-адреса конечной точки выглядит следующим образом: 

```sql
ENDPOINT_URL = 'TCP://dnnlsnr:5022'
```

Аналогично, формат URL-адреса маршрутизации только для чтения: `TCP://<FCI DNN DNS name>:<SQL Server instance port>` . 

Например, если DNS-имя DNN — `dnnlsnr` , а `1444` — порт, используемый целевым объектом, предназначенным только для чтения, SQL Server FCI, фрагмент кода T-SQL для создания URL-адреса маршрутизации только для чтения выглядит следующим образом: 

```sql
READ_ONLY_ROUTING_URL = 'TCP://dnnlsnr:1444'
```

Вы можете опустить порт в URL-адресе, если это порт 1433 по умолчанию. Для именованного экземпляра настройте статический порт для именованного экземпляра и укажите его в URL-адресе маршрутизации только для чтения.  

## <a name="distributed-availability-group"></a>Распределенная группа доступности

Распределенные группы доступности в настоящее время не поддерживаются прослушивателем DNN. 

## <a name="replication"></a>Репликация

Транзакционная, слияние и репликация моментальных снимков поддерживают замену прослушивателя VNN прослушивателем DNN и портом в объектах репликации, подключающихся к прослушивателю. 

Дополнительные сведения об использовании репликации с группами доступности см. в статье [издатель, AG](/sql/database-engine/availability-groups/windows/configure-replication-for-always-on-availability-groups-sql-server), [подписчик и AG](/sql/database-engine/availability-groups/windows/replication-subscribers-and-always-on-availability-groups-sql-server), а также [распространитель и AG](/sql/relational-databases/replication/configure-distribution-availability-group).

## <a name="msdtc"></a>MSDTC

Как локальные, так и кластеризованные MSDTC поддерживаются, но MSDTC использует динамический порт, для которого требуется стандартная Azure Load Balancer для настройки порта высокой доступности. Таким образом, виртуальная машина должна использовать стандартное резервирование IP-адресов или не может быть предоставлена в Интернете. 

Определите два правила: один для порта сопоставителя конечных точек RPC 135 и один для реального порта MSDTC. После отработки отказа измените правило балансировки нагрузки на новый порт MSDTC после его изменения на новом узле. 

Если координатор MSDTC является локальным, убедитесь, что разрешена Исходящая связь. 

## <a name="distributed-query"></a>Распределенный запрос 

Распределенный запрос основан на связанном сервере, который можно настроить с помощью прослушивателя DNN группы доступности и порта. Если порт не 1433, выберите параметр **использовать другой источник данных** в SQL Server Management Studio (SSMS) при настройке связанного сервера. 

## <a name="filestream"></a>FileStream

FILESTREAM поддерживается, но не для сценариев, в которых пользователи обращаются к общей папке с заданной областью с помощью API файлов Windows. 

## <a name="filetable"></a>FileTable

Таблица FileTable поддерживается, но не предназначена для сценариев, в которых пользователи обращаются к общей папке с заданной областью с помощью API файлов Windows. 

## <a name="linked-servers"></a>Связанные серверы

Настройте связанный сервер, используя имя и порт DNN группы доступности. Если порт не 1433, выберите параметр **использовать другой источник данных** в SQL Server Management Studio (SSMS) при настройке связанного сервера. 


## <a name="frequently-asked-questions"></a>Часто задаваемые вопросы


- Какая версия SQL Server предоставляет поддержку прослушивателя AG DNN? 

   SQL Server 2019 CU8 и более поздних версий.

- Каково ожидаемое время отработки отказа при использовании прослушивателя DNN?

   Для прослушивателя DNN время отработки отказа — это просто время отработки отказа группы доступности без дополнительного времени (например, время проверки при использовании Azure Load Balancer).

- Есть ли требования к версии для клиентов SQL, поддерживающих DNN с OLEDB и ODBC?

   Мы рекомендуем `MultiSubnetFailover=True` поддерживать строку подключения для прослушивателя DNN. Он доступен начиная с SQL Server 2012 (11. x).

- Требуются ли SQL Server изменения конфигурации, необходимые для использования прослушивателя DNN? 

   SQL Server не требует изменения конфигурации для использования DNN, но некоторые функции SQL Server могут потребовать больше внимания. 

- Поддерживает ли DNN кластеры с несколькими подсетями?

   Да. Кластер привязывает DNN в DNS с физическими IP-адресами всех реплик в доступности независимо от подсети. Клиент SQL пытается выполнить все IP-адреса DNS-имени независимо от подсети. 



## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения см. в разделе: 

- [технологии кластера под управлением Windows](/windows-server/failover-clustering/failover-clustering-overview);   
- [Группа доступности Always on](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)

