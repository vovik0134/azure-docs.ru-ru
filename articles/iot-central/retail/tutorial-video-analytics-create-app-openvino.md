---
title: Учебник по созданию видеоаналитики — приложения для распознавания объектов и движения в Azure IoT Central (OpenVINO)
description: В этом учебнике показано, как создать приложение видеоаналитики в IoT Central. Его можно создать, настроить и подключить к другим службам Azure. В этом учебнике используется набор средств Intel OpenVINO&trade; для обнаружения объектов в режиме реального времени.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
author: KishorIoT
ms.author: nandab
ms.date: 10/06/2020
ms.openlocfilehash: fbe1e84525eed47127a08abc9fb7ec5d1144d02f
ms.sourcegitcommit: d6e92295e1f161a547da33999ad66c94cf334563
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/07/2020
ms.locfileid: "96763623"
---
# <a name="tutorial-create-a-video-analytics---object-and-motion-detection-application-in-azure-iot-central-openvinotrade"></a>Руководство по созданию видеоаналитики — приложения для распознавания объектов и движения в Azure IoT Central (OpenVINO&trade;)

Узнайте, как разработчики решений создают приложения для видеоаналитики с помощью шаблона приложения *видеоаналитики для распознавания объектов и движения* в IoT Central, на устройствах Azure IoT Edge, в Службах мультимедиа Azure и на аппаратно-оптимизированном сервере модели OpenVINO&trade;. Решение использует магазин розничной торговли, чтобы продемонстрировать, как удовлетворить стандартные бизнес-потребности для мониторинга с помощью камер безопасности. Решение использует автоматическое обнаружение объектов в видеоканале, чтобы быстро находить и распознавать интересующие события.

> [!TIP]
> Сведения об использовании YOLO версии 3 OpenVINO&trade; для обнаружения объектов и движения см. в статье [Учебник по созданию видеоаналитики — приложения для распознавания объектов и движения в Azure IoT Central (YOLO версии 3)](tutorial-video-analytics-create-app-yolo-v3.md).

[!INCLUDE [iot-central-video-analytics-part1](../../../includes/iot-central-video-analytics-part1.md)]

- [Scratchpad.txt.](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/Scratchpad.txt) Этот файл позволяет записывать различные параметры конфигурации, необходимые для работы с этими руководствами.
- [deployment.openvino.amd64.json](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/deployment.openvino.amd64.json)
- [LvaEdgeGatewayDcm.json](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/LvaEdgeGatewayDcm.json)
- [state.json.](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/state.json) Этот файл нужно скачать, только если вы планируете использовать устройство Intel NUC в рамках второго руководства.

[!INCLUDE [iot-central-video-analytics-part2](../../../includes/iot-central-video-analytics-part2.md)]

## <a name="edit-the-deployment-manifest"></a>Изменение манифеста развертывания

Модуль IoT Edge развертывается с помощью манифеста развертывания. В IoT Central можно импортировать манифест как шаблон устройства, а затем разрешить IoT Central управлять развертыванием модуля.

Чтобы подготовить манифест развертывания, выполните следующие действия.

1. Откройте файл *deployment.openvino.amd64.json*, сохраненный в папке *lva-configuration*, с помощью текстового редактора.

1. Найдите параметры `LvaEdgeGatewayModule` и приведите имя образа в соответствие с показанным в следующем фрагменте кода:

    ```json
    "LvaEdgeGatewayModule": {
        "settings": {
            "image": "mcr.microsoft.com/lva-utilities/lva-edge-iotc-gateway:1.0-amd64",
    ```

1. Добавьте имя учетной записи Служб мультимедиа в узел `env` в разделе `LvaEdgeGatewayModule`. Вы записали имя учетной записи Служб мультимедиа в файл *scratchpad.txt*:

    ```json
    "env": {
        "lvaEdgeModuleId": {
            "value": "lvaEdge"
        },
        "amsAccountName": {
            "value": "<YOUR_AZURE_MEDIA_SERVICES_ACCOUNT_NAME>"
        }
    }
    ```

1. Шаблон не предоставляет эти свойства в IoT Central, поэтому необходимо добавить значения конфигурации Служб мультимедиа в манифест развертывания. Найдите модуль `lvaEdge` и замените заполнители значениями, которые вы записали в файле *scratchpad.txt* при создании учетной записи Служб мультимедиа.

    `azureMediaServicesArmId` является **идентификатором ресурса**, который вы записали в файле *scratchpad.txt* при создании учетной записи Служб мультимедиа.

    В следующей таблице приведены значения из **API подключения к Службам мультимедиа (JSON)** в файле *scratchpad.txt*, который следует использовать в манифесте развертывания:

    | Манифест развертывания       | Scratchpad  |
    | ------------------------- | ----------- |
    | aadTenantId               | AADTenantId |
    | aadServicePrincipalAppId  | AadClientId |
    | aadServicePrincipalSecret | AadSecret   |

    > [!CAUTION]
    > Используйте предыдущую таблицу, чтобы добавить правильные значения в манифест развертывания. Иначе устройство не будет работать.

    ```json
    {
        "lvaEdge":{
        "properties.desired": {
            "applicationDataDirectory": "/var/lib/azuremediaservices",
            "azureMediaServicesArmId": "[Resource ID from scratchpad]",
            "aadTenantId": "[AADTenantID from scratchpad]",
            "aadServicePrincipalAppId": "[AadClientId from scratchpad]",
            "aadServicePrincipalSecret": "[AadSecret from scratchpad]",
            "aadEndpoint": "https://login.microsoftonline.com",
            "aadResourceId": "https://management.core.windows.net/",
            "armEndpoint": "https://management.azure.com/",
            "diagnosticsEventsOutputName": "AmsDiagnostics",
            "operationalMetricsOutputName": "AmsOperational",
            "logCategories": "Application,Event",
            "AllowUnsecuredEndpoints": "true",
            "TelemetryOptOut": false
            }
        }
    }
    ```

1. Сохраните изменения.

В рамках этого учебника выполняется настройка решения для использования модуля OpenVINO&trade; для обнаружения объектов и движения. В следующем фрагменте кода показана конфигурация модуля:

```json
"OpenVINOModelServerEdgeAIExtensionModule": {
    "settings": {
        "image": "marketplace.azurecr.io/intel_corporation/open_vino",
        "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"4000/tcp\":[{\"HostPort\":\"4000\"}]}},\"Cmd\":[\"/ams_wrapper/start_ams.py\",\"--ams_port=4000\",\"--ovms_port=9000\"]}"
    },
    "type": "docker",
    "version": "1.0",
    "status": "running",
    "restartPolicy": "always"
}
```

[!INCLUDE [iot-central-video-analytics-part3](../../../includes/iot-central-video-analytics-part3.md)]

### <a name="replace-the-manifest"></a>Замена манифеста

На странице **шлюза LVA Edge версии 2** выберите **+ Replace manifest** (+ Заменить манифест).

:::image type="content" source="./media/tutorial-video-analytics-create-app-openvino/replace-manifest.png" alt-text="Замена манифеста":::

Перейдите в папку *lva-configuration* и выберите файл манифеста *deployment.openvino.amd64.json*, который вы изменили ранее. Щелкните **Отправить**. После завершения проверки нажмите кнопку **Заменить**.

[!INCLUDE [iot-central-video-analytics-part4](../../../includes/iot-central-video-analytics-part4.md)]
