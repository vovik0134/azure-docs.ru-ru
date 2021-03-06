---
title: Экспорт метрик соединителя Интернета вещей Azure для FHIR (предварительная версия) с помощью параметров диагностики
description: В этой статье объясняется, как экспортировать данные для метрик соединителя Azure IoT для FHIR (Предварительная версия) с помощью параметров диагностики.
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: how-to
ms.date: 11/13/2020
ms.author: jasteppe
ms.openlocfilehash: 14fd5378f37ebfc20b2d7084c08f15ea8f7a00b2
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/14/2020
ms.locfileid: "94630538"
---
# <a name="export-azure-iot-connector-for-fhir-preview-metrics-through-diagnostic-settings"></a>Экспорт метрик соединителя Интернета вещей Azure для FHIR (предварительная версия) с помощью параметров диагностики

В этой статье вы узнаете, как экспортировать соединитель Azure IoT для быстрого получения ресурсов о взаимодействии здравоохранения (FHIR&#174;) * журналы метрик. Функция, позволяющая вести журнал метрик, — это [**параметры диагностики**](../azure-monitor/platform/diagnostic-settings.md) в портал Azure. 

> [!TIP]
> Следуйте указаниям в статье [Включение ведения журнала диагностики в Azure API для FHIR и соединителя Azure IOT для FHIR](enable-diagnostic-logging.md#enable-diagnostic-logging-in-azure-api-for-fhir) , чтобы настроить ведение журнала аудита.

## <a name="enable-metrics-logging-for-the-azure-iot-connector-for-fhir-preview"></a>Включение ведения журнала метрик для соединителя Azure IoT для FHIR (Предварительная версия)
1. Чтобы включить ведение журнала метрик для соединителя Azure IoT для FHIR, выберите службу API Azure для службы FHIR в портал Azure 

2. Перейдите к **параметрам диагностики** . 

3. Выберите **+ Добавить параметр диагностики**

   :::image type="content" source="media/iot-metrics-export/diagnostic-settings-main.png" alt-text="Connector1 IoT" lightbox="media/iot-metrics-export/diagnostic-settings-main.png"::: 

4. Введите имя в диалоговом окне **имя параметра диагностики** .

5. Выберите метод, который вы хотите использовать для доступа к журналам диагностики:

    1. **Архивация в учетную запись хранения** для аудита или проверки вручную. Учетная запись хранения, которую вы хотите использовать, должна быть уже создана.
    2. **Поток в концентратор событий** для приема от сторонней службы или пользовательского аналитического решения. Прежде чем можно будет настроить этот шаг, необходимо создать пространство имен концентратора событий и политику концентратора событий.
    3. **Поток** в рабочую область Log Analytics в Azure Monitor. Чтобы выбрать этот параметр, необходимо создать рабочую область журналы Analytics.

6. Выберите **ошибки, трафик и задержку** для соединителя Azure IOT для FHIR.  Выберите дополнительные категории метрик, которые вы хотите захватить, для API Azure для FHIR.

7. Нажмите кнопку **Сохранить**.

   :::image type="content" source="media/iot-metrics-export/diagnostic-setting-add.png" alt-text="Connector2 IoT" lightbox="media/iot-metrics-export/diagnostic-setting-add.png":::

> [!Note] 
> Для того чтобы первые журналы метрик отображались в выбранном репозитории, может потребоваться до 15 минут.  
 
Дополнительные сведения о работе с журналами диагностики см. в [документации по журналу ресурсов Azure](../azure-monitor/platform/platform-logs-overview.md) .

## <a name="conclusion"></a>Заключение 
Наличие доступа к журналам метрик очень важно для мониторинга и устранения неполадок.  Соединитель Azure IoT для FHIR позволяет выполнять эти действия с помощью журналов метрик. 

## <a name="next-steps"></a>Дальнейшие действия

Ознакомьтесь с часто задаваемыми вопросами о соединителе Azure IoT для FHIR.

>[!div class="nextstepaction"]
>[Вопросы и ответы о соединителе Azure IoT для FHIR](fhir-faq.md)

* В портал Azure соединитель Azure IoT для FHIR называется соединителем IoT (Предварительная версия). FHIR является охраняемым товарным знаком HL7 и используется с разрешением HL7.