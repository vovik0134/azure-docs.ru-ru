---
title: Применение скидки на резервирование Azure
description: Эта статья поможет вам понять, как обычно применяются скидки на зарезервированные экземпляры.
author: bandersmsft
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 03/22/2020
ms.author: banders
ms.openlocfilehash: 1c038e896a9f9fc65c3b2201b3e763e966285c56
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/20/2020
ms.locfileid: "88684683"
---
# <a name="how-a-reservation-discount-is-applied"></a>Применение скидки на резервирование

Эта статья поможет вам понять, как обычно применяются скидки на зарезервированные экземпляры. Скидка на резервирование применяется к использованию ресурсов, которые соответствуют атрибутам, выбранным при покупке этого резервирования. Атрибуты включают в себя область, в которой выполняются виртуальные машины, базы данных SQL, Azure Cosmos DB или другие ресурсы. Например, если вы хотите использовать резервирование для получения скидки на четыре виртуальные машины D2 (цен. категория "Стандартный") в регионе "Западная часть США", выберите ту подписку, в которой работают эти виртуальные машины.

Скидка на резервирование *сгорает, если не используется*. Если у вас нет подходящих ресурсов в течение некоторого часа, вы потеряете количество ресурсов, зарезервированное на этот час. Вы не можете переносить неиспользуемые зарезервированные часы.

При завершении работы ресурса скидка на резервирование автоматически переносится на другой подходящий ресурс в указанной области резервирования. Если в указанной области не найдены подходящие ресурсы, зарезервированные часы *теряются*.

Например, вы можете позднее создать ресурс, к которому можно будет применить не полностью используемое резервирование. Скидка на это резервирование автоматически будет применена к новому подходящему ресурсу.

Если виртуальные машины работают в разных подписках в одной учетной записи, выберите вариант совместного использования. Совместное использование позволяет применить скидки резервирования сразу в нескольких подписках. Область резервирования можно изменить и после покупки. Дополнительные сведения см. в статье [Управление резервированиями в Azure](manage-reserved-vm-instance.md).

Скидка на резервирование применяется только к ресурсам в подписках, связанных с Соглашением Enterprise, Клиентским соглашением Майкрософт, CSP или оплатой по мере использования. Ресурсы, работающие в подписке с любым другим типом, не могут воспользоваться скидкой резервирования.

## <a name="when-the-reservation-term-expires"></a>Завершение срока резервирования

При завершении срока резервирования скидка на оплату отменяется и для ресурсов снова применяется оплата по мере использования. По умолчанию для резервирования не задано автоматическое продление. Вы можете включить автоматическое продление резервирования, выбрав соответствующий пункт в параметрах продления. При автоматическом продлении резервирование на замену будет приобретено по истечении срока действия существующего резервирования. По умолчанию новое резервирование имеет те же атрибуты, что и старое. При необходимости можно изменить частоту выставления счетов, срок или количество в параметрах продления. Любой пользователь с правами владельца на резервирование и подписку, используемую для выставления счетов, может установить продление.  

## <a name="discount-applies-to-different-sizes"></a>Применение скидки для различных размеров виртуальных машин

Когда вы покупаете резервирование, скидку можно применить к другим экземплярам с атрибутами, которые находятся в группе того же размера. Эта функция называется гибкостью размера экземпляра. Гибкость покрытия скидки зависит от типа резервирования и атрибутов, которые вы выбираете при покупке резервирования.

Планы обслуживания:

- Зарезервированные экземпляры виртуальных машин. Если при покупке резервирования вы выбираете **Оптимизировано для: гибкость размера экземпляра**, покрытие скидки зависит от выбранного размера виртуальной машины. Резервирование можно применить к размерам в группе виртуальных машин серии того же размера. Дополнительные сведения см. в статье [Гибкость размеров виртуальных машин при использовании зарезервированных экземпляров виртуальных машин](../../virtual-machines/reserved-vm-instance-size-flexibility.md).
- Зарезервированная емкость службы хранилища Azure. Вы можете купить зарезервированную емкость для стандартных учетных записей хранения Azure в единицах 100 ТиБ или 1 ПиБ в месяц. Сведения о том, какие регионы поддерживают зарезервированную емкость службы хранилища Azure, см. на странице [цен на блочные BLOB-объекты](https://azure.microsoft.com/pricing/details/storage/blobs/). Зарезервированная емкость службы хранилища Azure доступна для любого уровня доступа (горячий, холодный, архивный) и для любой конфигурации репликации (LRS, GRS или ZRS).
- Резервная мощность Базы данных SQL. Покрытие скидки зависит от выбранного уровня производительности. Дополнительные сведения см. в статье [Сведения о применении скидки на резервирование к Базам данных SQL](understand-reservation-charges.md).
- Резервная мощность Azure Cosmos DB. Покрытие скидки зависит от подготовленной пропускной способности. Дополнительные сведения см. в статье [Общие сведения о применении скидки на резервирование в Azure Cosmos DB](understand-cosmosdb-reservation-charges.md).

## <a name="how-discounts-apply-to-specific-azure-services"></a>Как скидки применяются к конкретным службам Azure

Ознакомьтесь со следующими статьями, из которых вы узнаете, как скидки применяются к тем или иным службам Azure.

- [Служба приложений](reservation-discount-app-service-isolated-stamp.md)
- [Кэш Azure для Redis](understand-azure-cache-for-redis-reservation-charges.md)
- [База данных Cosmos](understand-cosmosdb-reservation-charges.md)
- [База данных Azure для MariaDB](understand-reservation-charges-mariadb.md)
- [База данных Azure для MySQL](understand-reservation-charges-mysql.md)
- [База данных Azure для PostgreSQL](understand-reservation-charges-postgresql.md)
- [Databricks](reservation-discount-databricks.md)
- [Обозреватель данных](understand-azure-data-explorer-reservation-charges.md)
- [Выделенные узлы](billing-understand-dedicated-hosts-reservation-charges.md)
- [Хранилище дисков](understand-disk-reservations.md)
- [Red Hat Enterprise Linux](understand-rhel-reservation-charges.md)
- [Планы программного обеспечения](understand-suse-reservation-charges.md)
- [Память](understand-storage-charges.md)
- [База данных SQL](understand-reservation-charges.md)
- [Azure Synapse Analytics](reservation-discount-azure-sql-dw.md)
- [Виртуальные машины](../manage/understand-vm-reservation-charges.md)


## <a name="next-steps"></a>Дальнейшие действия

- [Управление Azure Reserved VM Instances](manage-reserved-vm-instance.md)
- [Общие сведения об использовании резервирования Azure для подписки с оплатой по мере использования](understand-reserved-instance-usage.md)
- [Общие сведения об использовании зарезервированных экземпляров Azure с Соглашением о регистрации Enterprise](understand-reserved-instance-usage-ea.md)
- [Затраты на программное обеспечение Windows, которые не включены в стоимость зарезервированных экземпляров Azure](reserved-instance-windows-software-costs.md)