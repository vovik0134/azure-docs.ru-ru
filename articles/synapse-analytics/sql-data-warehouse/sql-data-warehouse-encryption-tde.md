---
title: Прозрачное шифрование данных (портал) для выделенного пула SQL (ранее — хранилище данных SQL)
description: Прозрачное шифрование данных (TDE) для выделенного пула SQL (ранее — хранилище данных SQL) в Azure синапсе Analytics
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/30/2019
ms.author: jrasnick
ms.reviewer: rortloff
ms.custom: seo-lt-2019
ms.openlocfilehash: 45c7b53d3bbe0c57e96fc5435650c4e86b0cb032
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/01/2020
ms.locfileid: "96455258"
---
# <a name="get-started-with-transparent-data-encryption-tde-for-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Начало работы с прозрачное шифрование данных (TDE) для выделенного пула SQL (ранее — хранилища данных SQL) в Azure синапсе Analytics

> [!div class="op_single_selector"]
>
> * [Обзор безопасности](sql-data-warehouse-overview-manage-security.md)
> * [Аутентификация](sql-data-warehouse-authentication.md)
> * [Шифрование (портал)](sql-data-warehouse-encryption-tde.md)
> * [Шифрование (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)

## <a name="required-permissions"></a>Необходимые разрешения

Чтобы включить прозрачное шифрование данных, необходимо иметь права администратора или участника роли dbmanager.

## <a name="enabling-encryption"></a>Включение шифрования

Чтобы включить TDE, выполните следующие действия:

1. Откройте базу данных в [портал Azure](https://portal.azure.com)
2. В колонке базы данных нажмите кнопку **Параметры** .
3. Выберите параметр **прозрачное шифрование данных** ![ Параметры портала](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings.png)
4. Выберите параметр **On** Portal Settings (вкл.) ![ на](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-on.png)
5. Выберите **сохранить** 
    ![ Параметры портала сохранить](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save.png)  

## <a name="disabling-encryption"></a>Отключение шифрования

Чтобы отключить TDE, выполните следующие действия:

1. Откройте базу данных в [портал Azure](https://portal.azure.com)
2. В колонке базы данных нажмите кнопку **Параметры** .
3. Выберите параметр **прозрачное шифрование данных** ![ Параметры портала](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings.png)
4. Установите флажок **выкл** ., чтобы ![ отключить установку параметров портала.](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-off.png)
5. Выберите **Save** 
    ![ параметр Сохранить параметры портала сохранить 2.](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save2.png)  

## <a name="encryption-dmvs"></a>Динамические административные представления шифрования

Шифрование может быть подтверждено с помощью следующих динамических административных представлений.

* [sys.databases](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
* [sys.dm_pdw_nodes_database_encryption_keys](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-nodes-database-encryption-keys-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
