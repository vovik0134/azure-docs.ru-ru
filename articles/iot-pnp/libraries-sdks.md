---
title: Библиотеки и пакеты SDK для Plug and Play IoT
description: Сведения о библиотеках устройств и служб, доступных для разработки решений IoT Plug and Play.
author: rido-min
ms.author: rmpablos
ms.date: 07/22/2020
ms.topic: reference
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 3ff01455c56dd259ea64878f912eb3f2431547e7
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/23/2020
ms.locfileid: "95495259"
---
# <a name="microsoft-sdks-for-iot-plug-and-play"></a>Пакеты SDK для Microsoft Plug and Play IoT

Библиотеки Plug and Play IoT и пакеты SDK позволяют разработчикам создавать решения IoT с помощью различных языков программирования на различных платформах. В следующей таблице приведены ссылки на примеры и краткие руководства, которые помогут вам приступить к работе.

## <a name="device-sdks"></a>Пакеты SDK для устройств

| Язык | Пакет | Репозиторий кода | Примеры | Краткое руководство | Справочник |
|---|---|---|---|---|---|
| C-устройство | [vcpkg 1.3.9](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/setting_up_vcpkg.md) | [GitHub](https://github.com/Azure/azure-iot-sdk-c) | [Примеры](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/pnp) | [Подключение к Центру Интернета вещей](quickstart-connect-device.md) | [Ссылки](/azure/iot-hub/iot-c-sdk-ref/) |
| .NET — устройство | [1.31.0 NuGet](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client) | [GitHub](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/) | [Примеры](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/iot-hub/Samples/device/PnpDeviceSamples) | [Подключение к Центру Интернета вещей](quickstart-connect-device.md) | [Ссылки](/dotnet/api/microsoft.azure.devices.client?preserve-view=true&view=azure-dotnet) |
| Java — устройство | [Maven 1.26.0](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/iot-device-client) | [GitHub](https://github.com/Azure/azure-iot-sdk-java/tree/master/) | [Примеры](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples/pnp-device-sample) | [Подключение к Центру Интернета вещей](quickstart-connect-device.md) | [Ссылки](/java/api/com.microsoft.azure.sdk.iot.device?preserve-view=true&view=azure-java-stable) |
| Python — устройство | [PIP 2.3.0](https://pypi.org/project/azure-iot-device/) | [GitHub](https://github.com/Azure/azure-iot-sdk-python/tree/master/) | [Примеры](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/pnp) | [Подключение к Центру Интернета вещей](quickstart-connect-device.md) | [Ссылки](/python/api/azure-iot-device/azure.iot.device?preserve-view=true&view=azure-python) |
| Узел-устройство | [NPM 1.17.2](https://www.npmjs.com/package/azure-iot-device)  | [GitHub](https://github.com/Azure/azure-iot-sdk-node/tree/master/) | [Примеры](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples/pnp) | [Подключение к Центру Интернета вещей](quickstart-connect-device.md) | [Ссылки](/javascript/api/azure-iot-device/?preserve-view=true&view=azure-node-latest) |
| Встроенное C-устройство | Недоступно | [GitHub](https://github.com/Azure/azure-sdk-for-c/)| [Примеры](howto-use-embedded-c.md#samples) | [Как использовать внедренный C](howto-use-embedded-c.md) | Недоступно

## <a name="service-sdks"></a>Пакеты SDK службы

| Платформа  | Пакет | Репозиторий кода | Примеры | Краткое руководство | Справочник |
|---|---|---|---|---|---|
| .NET — служба центра Интернета вещей | [1.27.1 NuGet](https://www.nuget.org/packages/Microsoft.Azure.Devices ) | [GitHub](https://github.com/Azure/azure-iot-sdk-csharp) | [Примеры](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/iot-hub/Samples/service/PnpServiceSamples) | Недоступно | [Ссылки](/dotnet/api/microsoft.azure.devices?preserve-view=true&view=azure-dotnet) |
| Java — служба центра Интернета вещей | [Maven 1.26.0](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/iot-service-client/1.26.0) | [GitHub](https://github.com/Azure/azure-iot-sdk-java) | [Примеры](https://github.com/Azure/azure-iot-sdk-java/tree/master/service/iot-service-samples/pnp-service-sample) | Недоступно | [Ссылки](/java/api/com.microsoft.azure.sdk.iot.service?preserve-view=true&view=azure-java-stable) |
| Узел — Служба центра Интернета вещей | [NPM 1.13.0](https://www.npmjs.com/package/azure-iothub) | [GitHub](https://github.com/Azure/azure-iot-sdk-node) | [Примеры](https://github.com/Azure/azure-iot-sdk-node/tree/master/service/samples) | Недоступно | [Ссылки](/javascript/api/azure-iothub/?preserve-view=true&view=azure-node-latest) |
| Python — Служба Digital двойников | [PIP 2.2.3](https://pypi.org/project/azure-iot-hub) | [GitHub](https://github.com/Azure/azure-iot-sdk-python) | [Примеры](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-hub/samples) | [Взаимодействие с API Digital двойников для центра Интернета вещей](quickstart-service.md) | Недоступно |
| Узел — Служба Digital двойников | [NPM 1.13.0](https://www.npmjs.com/package/azure-iot-digitaltwins-service) | [GitHub](https://github.com/Azure/azure-iot-sdk-node) | [Примеры](https://github.com/Azure/azure-iot-sdk-node/tree/master/service/samples/javascript) | [Взаимодействие с API Digital двойников для центра Интернета вещей](quickstart-service.md) | Недоступно |

## <a name="next-steps"></a>Дальнейшие действия

Чтобы испытать пакеты SDK и библиотеки, ознакомьтесь с  [руководством для разработчиков](concepts-developer-guide-device.md) и [краткими](quickstart-connect-device.md) руководствами и [краткими](quickstart-service.md)руководствами по службам.