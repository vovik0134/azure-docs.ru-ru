---
title: Рекомендации по конфигурации приложений Azure | Документация Майкрософт
description: Ознакомьтесь с рекомендациями при использовании конфигурации приложений Azure. В число рассматриваемых тем входят группирования ключей, присвоить значения ключей, начальная загрузка конфигурации приложения и многое другое.
services: azure-app-configuration
documentationcenter: ''
author: AlexandraKemperMS
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: alkemper
ms.custom: devx-track-csharp, mvc
ms.openlocfilehash: 038d19270fbdb672d397eb2bd56bd27e17ea7af9
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/09/2020
ms.locfileid: "96929095"
---
# <a name="azure-app-configuration-best-practices"></a>Рекомендации по настройке приложений Azure

В этой статье рассматриваются распространенные шаблоны и рекомендации при использовании конфигурации приложений Azure.

## <a name="key-groupings"></a>Группирование ключей

Конфигурация приложения предоставляет два варианта организации ключей:

* Префиксы ключей
* Подписи

Для группирования ключей можно использовать один или оба параметра.

*Префиксы ключей* — это начальные части ключей. Набор ключей можно логически сгруппировать, используя один и тот же префикс в именах. Префиксы могут содержать несколько компонентов, Соединенных разделителем, например `/` , аналогично URL-пути, для формирования пространства имен. Такие иерархии полезны при хранении ключей для многих приложений, служб компонентов и сред в одном хранилище конфигураций приложений.

Важно помнить, что ключи представляют собой ссылки на код приложения для получения значений соответствующих параметров. Ключи не должны изменяться, иначе вам придется изменять код каждый раз, когда это происходит.

*Метки* являются атрибутом для ключей. Они используются для создания вариантов ключа. Например, можно назначить метки нескольким версиям ключа. Версия может быть итерацией, средой или другими контекстными сведениями. Приложение может запросить совершенно другой набор ключевых значений, указав другую метку. В результате все ключевые ссылки остаются без изменений в коде.

## <a name="key-value-compositions"></a>Композиции "ключ-значение"

Конфигурация приложения рассматривает все ключи, хранящиеся в нем, как независимые сущности. Конфигурация приложения не пытается определить связь между ключами или наследовать значения ключей на основе их иерархии. Однако можно выполнить статистическую обработку нескольких наборов ключей с помощью меток, связанных с правильной настройкой стека в коде приложения.

Давайте рассмотрим пример. Предположим, у вас есть параметр с именем **Asset1**, значение которого может меняться в зависимости от среды разработки. Создайте ключ с именем "Asset1" с пустой меткой и меткой "Разработка". В первой метке вы замещаете значение по умолчанию для **Asset1**, а в последнем — заданное значение для «разработки».

В коде сначала извлекаются ключевые значения без меток, а затем один и тот же набор значений ключа извлекается второй раз с меткой "Разработка". При извлечении значений во второй раз предыдущие значения ключей перезаписываются. Система конфигурации .NET Core позволяет «Stack» несколько наборов данных конфигурации поверх других. Если ключ существует более чем в одном наборе, используется последний набор, содержащий его. Благодаря современной платформе программирования, такой как .NET Core, вы получаете возможность использовать эту функцию для поддержки стеков, если для доступа к конфигурации приложения используется собственный поставщик конфигурации. В следующем фрагменте кода показано, как можно реализовать стек в приложении .NET Core:

```csharp
// Augment the ConfigurationBuilder with Azure App Configuration
// Pull the connection string from an environment variable
configBuilder.AddAzureAppConfiguration(options => {
    options.Connect(configuration["connection_string"])
           .Select(KeyFilter.Any, LabelFilter.Null)
           .Select(KeyFilter.Any, "Development");
});
```

[Используйте метки, чтобы включить различные конфигурации для разных сред](./howto-labels-aspnet-core.md) . полный пример.

## <a name="app-configuration-bootstrap"></a>Начальная загрузка конфигурации приложения

Чтобы получить доступ к хранилищу конфигураций приложений, можно использовать строку подключения, которая доступна в портал Azure. Так как строки подключения содержат учетные данные, они считаются секретными. Эти секреты должны храниться в Azure Key Vault, и код должен пройти проверку подлинности в Key Vault, чтобы получить их.

Лучшим вариантом является использование функции управляемых удостоверений в Azure Active Directory. При использовании управляемых удостоверений требуется только URL-адрес конечной точки конфигурации приложения для загрузки доступа к хранилищу конфигураций приложений. URL-адрес можно внедрить в код приложения (например, в *appsettings.js* в файле). Дополнительные сведения см. [в статье интеграция с управляемыми удостоверениями Azure](howto-integrate-azure-managed-service-identity.md) .

## <a name="app-or-function-access-to-app-configuration"></a>Доступ приложения или функции к конфигурации приложения

Вы можете предоставить доступ к конфигурации приложения для веб-приложений или функций с помощью любого из следующих методов:

* В портал Azure введите строку подключения к хранилищу конфигураций приложения в параметрах приложения службы приложений.
* Сохраните строку подключения в хранилище конфигурации приложения в Key Vault и [сослаться на него из службы приложений](../app-service/app-service-key-vault-references.md).
* Используйте управляемые удостоверения Azure для доступа к хранилищу конфигураций приложений. Дополнительные сведения см. в статье [Интеграция с управляемыми удостоверениями Azure](howto-integrate-azure-managed-service-identity.md).
* Принудительная отправка конфигурации из конфигурации приложения в службу приложений. Конфигурация приложения предоставляет функцию экспорта (в портал Azure и Azure CLI), которая отправляет данные непосредственно в службу приложений. С помощью этого метода вам не нужно изменять код приложения.

## <a name="reduce-requests-made-to-app-configuration"></a>Сокращение количества запросов, сделанных в конфигурации приложения

Избыточные запросы к конфигурации приложения могут привести к регулированию или превышению расходов. Чтобы уменьшить число выполненных запросов:

* Увеличьте время ожидания обновления, особенно если значения конфигурации не меняются часто. Укажите новое время ожидания обновления с помощью [ `SetCacheExpiration` метода](/dotnet/api/microsoft.extensions.configuration.azureappconfiguration.azureappconfigurationrefreshoptions.setcacheexpiration).

* Просмотрите один *ключ-метку*, а не Просмотр отдельных ключей. Обновлять всю конфигурацию только при изменении ключа Sentinel. Пример см. [в разделе Использование динамической конфигурации в ASP.NET Core приложении](enable-dynamic-configuration-aspnet-core.md) .

* Служба "Сетка событий Azure" используется для получения уведомлений при изменении конфигурации, а не для постоянного опроса каких бы то ни было изменений. Дополнительные сведения см. в статье [Маршрутизация событий конфигурации приложений Azure в веб-конечную точку](./howto-app-configuration-event.md) .

## <a name="importing-configuration-data-into-app-configuration"></a>Импорт данных конфигурации в конфигурацию приложения

Конфигурация приложения предлагает возможность выполнить [Импорт](./howto-import-export-data.md) параметров конфигурации из текущих файлов конфигурации с помощью портал Azure или CLI. Можно также использовать те же параметры для экспорта значений из конфигурации приложения, например между связанными хранилищами. Если вы хотите настроить постоянную синхронизацию с репозиторием GitHub, вы можете использовать наше [действие GitHub](./concept-github-action.md) , чтобы продолжить использовать существующие методы управления версиями при получении преимуществ конфигурации приложений.

## <a name="multi-region-deployment-in-app-configuration"></a>Развертывание в нескольких регионах в конфигурации приложения

Конфигурация приложения — Региональная служба. Для приложений с разными конфигурациями на регион хранение этих конфигураций в одном экземпляре может создать единую точку отказа. Лучшим вариантом может быть развертывание одного экземпляра конфигурации приложения для каждого региона в нескольких регионах. Это может помочь при работе с региональными аварийным восстановлением, производительностью и системой безопасности. Настройка по регионам также повышает задержку и использует разделенные квоты регулирования, так как регулирование выполняется для каждого экземпляра. Чтобы применить устранение аварийного восстановления, можно использовать [несколько хранилищ конфигураций](./concept-disaster-recovery.md). 

## <a name="next-steps"></a>Дальнейшие действия

* [Ключи и значения](./concept-key-value.md)