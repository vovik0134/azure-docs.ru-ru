---
title: Анализ журналов и метрик в Azure Веснного облака | Документация Майкрософт
description: Узнайте, как анализировать диагностические данные в Azure с пружинным облаком
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: 11f5fcd74b228fa2d57658f5c268e3bebc3c7e93
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/02/2020
ms.locfileid: "96499532"
---
# <a name="analyze-logs-and-metrics-with-diagnostics-settings"></a>Анализ журналов и метрик с помощью параметров диагностики

**Эта статья применима к:** ✔️ Java ✔️ C#

Используя функции диагностики в Azure Веснного облака, можно анализировать журналы и метрики с помощью любой из следующих служб:

* Используйте Log Analytics Azure, где данные записываются в службу хранилища Azure. При экспорте журналов в Log Analytics возникает задержка.
* Сохранение журналов в учетной записи хранения для аудита или проверки вручную. Можно указать время хранения (в днях).
* Потоковая передача журналов в концентратор событий для приема сторонними службами или решениями пользовательской аналитики.

Выберите категорию журнала и категорию метрик, которые требуется отслеживать.

> [!TIP]
> Просто хотите выполнить потоковую передачу журналов? Ознакомьтесь с этой [командой Azure CLI](/cli/azure/ext/spring-cloud/spring-cloud/app?preserve-view=true&view=azure-cli-latest#ext-spring-cloud-az-spring-cloud-app-logs)!

## <a name="logs"></a>Журналы

|Журнал | Описание |
|----|----|
| **аппликатионконсоле** | Журнал консоли для всех приложений клиента. |
| **системлогс** | В настоящее время в этой категории регистрируются только журналы [сервера конфигурации "Весна Cloud Configuration](https://cloud.spring.io/spring-cloud-config/reference/html/#_spring_cloud_config_server) ". |

## <a name="metrics"></a>Метрики

Полный список метрик см. в разделе [метрики пружинного облака](./spring-cloud-concept-metrics.md#user-metrics-options).

Чтобы начать работу, включите одну из этих служб для получения данных. Дополнительные сведения о настройке Log Analytics см. [в статье Приступая к работе с log Analytics в Azure Monitor](../azure-monitor/log-query/log-analytics-tutorial.md).

## <a name="configure-diagnostics-settings"></a>Настройка параметров диагностики

1. В портал Azure перейдите к своему экземпляру Azure весны в облаке.
1. Выберите **параметры диагностики** и нажмите кнопку **Добавить параметр диагностики**.
1. Введите имя параметра, а затем выберите место, куда нужно отправить журналы. Можно выбрать любое сочетание следующих трех параметров.
    * **Архивировать в учетной записи хранения.**
    * **Передать в концентратор событий.**
    * **Отправить в Log Analytics.**

1. Выберите категорию журнала и категорию метрик, которые требуется отслеживать, а затем укажите время хранения (в днях). Время хранения применяется только к учетной записи хранения.
1. Щелкните **Сохранить**.

> [!NOTE]
> 1. Между созданием журналов и метрик и их появлением в учетной записи хранения, концентраторе событий или Log Analytics может существовать промежуток в течение 15 минут.
> 1. Если экземпляр облака Azure весны удален или перемещен, операция не будет каскадной для ресурсов **параметров диагностики** . Ресурсы **параметров диагностики** должны быть удалены вручную перед операцией с родительским объектом, т. е. экземпляром Azure весны в облаке. В противном случае, если новый облачный экземпляр Azure весны подготавливается с тем же ИДЕНТИФИКАТОРом ресурса, что и у удаленного, или если экземпляр облачного облака Azure перемещен назад, предыдущие ресурсы **параметров диагностики** продолжат его расширение.

## <a name="view-the-logs-and-metrics"></a>Просмотр журналов и метрик
Существуют различные методы просмотра журналов и метрик, как описано в следующих заголовках.

### <a name="use-the-logs-blade"></a>Использование колонки "журналы"

1. В портал Azure перейдите к своему экземпляру Azure весны в облаке.
1. Чтобы открыть панель **поиска по журналам** , выберите **журналы**.
1. В поле поиска **таблиц**
   * Чтобы просмотреть журналы, введите простой запрос, например:

    ```sql
    AppPlatformLogsforSpring
    | limit 50
    ```
   * Чтобы просмотреть метрики, введите простой запрос, например:

    ```sql
    AzureMetrics
    | limit 50
    ```
1. Чтобы просмотреть результат поиска, выберите **выполнить**.

### <a name="use-log-analytics"></a>Использование Log Analytics

1. В портал Azure в левой области выберите **log Analytics**.
1. Выберите рабочую область Log Analytics, которая была выбрана при добавлении параметров диагностики.
1. Чтобы открыть панель **поиска по журналам** , выберите **журналы**.
1. В поле поиска **таблиц**
   * чтобы просмотреть журналы, введите простой запрос, например:

    ```sql
    AppPlatformLogsforSpring
    | limit 50
    ```
    * чтобы просмотреть метрики, введите простой запрос, например:

    ```sql
    AzureMetrics
    | limit 50
    ```

1. Чтобы просмотреть результат поиска, выберите **выполнить**.
1. Можно выполнить поиск в журналах конкретного приложения или экземпляра, задав условие фильтра:

    ```sql
    AppPlatformLogsforSpring
    | where ServiceName == "YourServiceName" and AppName == "YourAppName" and InstanceName == "YourInstanceName"
    | limit 50
    ```
> [!NOTE]
> `==` учитывает регистр, но `=~` не имеет.

Дополнительные сведения о языке запросов, который используется в Log Analytics, см. в разделе [Azure Monitor запросы журналов](/azure/data-explorer/kusto/query/).

### <a name="use-your-storage-account"></a>Использование учетной записи хранения

1. В портал Azure найдите **учетные записи хранения** в левой панели навигации или в поле поиска.
1. Выберите учетную запись хранения, выбранную при добавлении параметров диагностики.
1. Чтобы открыть панель **BLOB-контейнера** , выберите **BLOB-объекты**.
1. Чтобы просмотреть журналы приложений, выполните поиск контейнера с именем **Insights-Logs-аппликатионконсоле**.
1. Чтобы просмотреть метрики приложения, выполните поиск контейнера с именем **Insights-метрики-pt1m**.

Дополнительные сведения об отправке диагностических сведений в учетную запись хранения см. [в статье хранение и Просмотр диагностических данных в службе хранилища Azure](../storage/common/storage-introduction.md).

### <a name="use-your-event-hub"></a>Использование концентратора событий

1. В портал Azure найдите **концентраторы событий** в левой панели навигации или в поле поиска.

1. Найдите и выберите концентратор событий, выбранный при добавлении параметров диагностики.
1. Чтобы открыть панель **списка концентраторов событий** , выберите **концентраторы событий**.
1. Чтобы просмотреть журналы приложений, выполните поиск концентратора событий с именем **Insights-Logs-аппликатионконсоле**.
1. Чтобы просмотреть метрики приложения, выполните поиск концентратора событий с именем **Insights-метрики-pt1m**.

Дополнительные сведения об отправке диагностических сведений в концентратор событий см. в статье [потоковая передача система диагностики Azure данных в критическом пути с помощью концентраторов событий](../azure-monitor/platform/diagnostics-extension-stream-event-hubs.md).

## <a name="analyze-the-logs"></a>Анализ журналов

Azure Log Analytics работает с ядром Kusto, чтобы вы могли запрашивать анализ журналов. Краткие сведения о запросах журналов с помощью Kusto см. в [руководстве по log Analytics](../azure-monitor/log-query/log-analytics-tutorial.md).

Журналы приложений содержат важную информацию и подробные журналы, относящиеся к работоспособности, производительности и многому приложению. В следующих разделах приведены некоторые простые запросы, помогающие понять текущее и предыдущее состояния приложения.

### <a name="show-application-logs-from-azure-spring-cloud"></a>Отображение журналов приложений из Azure Веснного облака

Чтобы просмотреть список журналов приложений из Azure Веснного облака, отсортированный по времени с самыми последними журналами, показанными в первую очередь, выполните следующий запрос:

```sql
AppPlatformLogsforSpring
| project TimeGenerated , ServiceName , AppName , InstanceName , Log
| sort by TimeGenerated desc
```

### <a name="show-logs-entries-containing-errors-or-exceptions"></a>Отображение записей журналов, содержащих ошибки или исключения

Чтобы проверить несортированные записи журнала, в которых упоминается ошибка или исключение, выполните следующий запрос:

```sql
AppPlatformLogsforSpring
| project TimeGenerated , ServiceName , AppName , InstanceName , Log
| where Log contains "error" or Log contains "exception"
```

Используйте этот запрос для поиска ошибок или изменения условий запроса для поиска конкретных кодов ошибок или исключений.

### <a name="show-the-number-of-errors-and-exceptions-reported-by-your-application-over-the-last-hour"></a>Отображение количества ошибок и исключений, о которых сообщило приложение за последний час

Чтобы создать круговую диаграмму, отображающую количество ошибок и исключений, зарегистрированных приложением за последний час, выполните следующий запрос:

```sql
AppPlatformLogsforSpring
| where TimeGenerated > ago(1h)
| where Log contains "error" or Log contains "exception"
| summarize count_per_app = count() by AppName
| sort by count_per_app desc
| render piechart
```

### <a name="learn-more-about-querying-application-logs"></a>Дополнительные сведения о запросах журналов приложений

Azure Monitor предоставляет обширную поддержку для запросов журналов приложений с помощью Log Analytics. Дополнительные сведения об этой службе см. в разделе Начало [работы с запросами журналов в Azure Monitor](../azure-monitor/log-query/get-started-queries.md). Дополнительные сведения о создании запросов для анализа журналов приложений см. в разделе [Обзор запросов журналов в Azure Monitor](../azure-monitor/log-query/log-query-overview.md).

## <a name="frequently-asked-questions-faq"></a>Часто задаваемые вопросы

### <a name="how-to-convert-multi-line-java-stack-traces-into-a-single-line"></a>Как преобразовать многострочные трассировки стека Java в одну строку?

Существует обходной путь для преобразования многострочных трассировок стека в одну строку. Вы можете изменить выходные данные журнала Java, чтобы переформатировать сообщения трассировки стека, заменив символы новой строки маркером. При использовании библиотеки Java Logback можно переформатировать сообщения трассировки стека, добавив `%replace(%ex){'[\r\n]+', '\\n'}%nopex` следующим образом:

```xml
<configuration>
    <appender name="CONSOLE" class="ch.qos.logback.core.ConsoleAppender">
        <encoder>
            <pattern>
                level: %level, message: "%logger{36}: %msg", exceptions: "%replace(%ex){'[\r\n]+', '\\n'}%nopex"%n
            </pattern>
        </encoder>
    </appender>
    <root level="INFO">
        <appender-ref ref="CONSOLE"/>
    </root>
</configuration>
```
После этого снова можно заменить маркер символами новой строки в Log Analytics, как показано ниже:

```sql
AppPlatformLogsforSpring
| extend Log = array_strcat(split(Log, '\\n'), '\n')
```
Вы можете использовать ту же стратегию для других библиотек журналов Java.

## <a name="next-steps"></a>Дальнейшие действия

* [Краткое руководство. Развертывание первого приложения Azure Spring Cloud](spring-cloud-quickstart.md)