---
title: Жизненный цикл активации и деактивации Azure Service Fabric
description: Описание жизненного цикла приложения и ServicePackage на узле
author: tugup
ms.topic: conceptual
ms.date: 05/1/2020
ms.author: tugup
ms.openlocfilehash: f049b19703d37412d1ee1961aee6cb327efabe7c
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/07/2020
ms.locfileid: "96779606"
---
# <a name="azure-service-fabric-hosting-lifecycle"></a>Жизненный цикл размещения Service Fabric Azure

В этой статье представлен обзор событий, происходящих в Azure Service Fabric при активации приложения на узле и различных конфигурациях кластера, используемых для управления поведением.

Прежде чем продолжить, убедитесь, что вы понимаете различные понятия и связи, описанные в статье [Моделирование приложения в структуре службы][a1]. 

> [!NOTE]
> Если явно не указано иное, в этой статье:
>
> - *Реплика* относится как к реплике службы с отслеживанием состояния, так и к экземпляру службы без отслеживания состояния.
> - *CodePackage* обрабатывается как эквивалент процесса *ServiceHost* , который регистрирует *serviceType*, и размещает реплики служб этого *serviceType*.
>

## <a name="activation-of-applicationservicepackage"></a>Активация приложения или ServicePackage

Конвейер для активации выглядит следующим образом:

1. Скачайте ApplicationPackage. Например: ApplicationManifest.xml и т. д.
2. Настройка среды для приложения, например создание пользователей и т. д.
3. Запуск отслеживания приложения для деактивации.
4. Скачайте ServicePackage. Например: ServiceManifest.xml, код, конфигурация, пакеты данных и т. д.
5. Настройте среду для пакета службы для ex: Настройка брандмауэра, выделение портов для конечных точек и т. д.
6. Запустите отслеживание ServicePackage для деактивации.
7. Запустите SetupEntryPoint содержащиеся и дождитесь завершения.
8. Запустите Маинентрипоинт из содержащиеся.

### <a name="servicetype-blocklisting"></a>ServiceType Добавление
**Сервицетипедисаблефаилуресрешолд** определяет количество сбоев (активаций, загрузки, CodePackageных сбоев), по истечении которых у serviceType будет запланировано добавление. При первой ошибке активации, сбое загрузки или CodePackage сбой будет запланировать ServiceType добавление. Конфигурация **сервицетипедисаблеграцеинтервал** определяет интервал отсрочки, после которого serviceType помечается как добавлен на этом узле. Хотя все это происходит, повторные попытки активации, загрузки и CodePackage перезапускаются параллельно. Повторная попытка, например, CodePackage будет запускаться снова после сбоя или Service Fabric попытается загрузить пакеты снова.

Когда ServiceType имеет значение добавлен, вы увидите ошибку работоспособности "System. Hosting", которая сообщила об ошибке для свойства "ServiceTypeRegistration: ServiceType". ServiceType отключен на узле. "

ServiceType будет включен на узле снова, если произойдет одно из следующих действий:
- Активация выполняется успешно или достигает **активатионмаксфаилурекаунт** повторных попыток после сбоя.
- Загрузка прошла успешно или достигает **деплойментмаксфаилурекаунт** повторных попыток после сбоя.
- CodePackage запускается и успешно регистрирует ServiceType.

**Активатионмаксфаилурекаунт** и **деплойментмаксфаилурекаунт** — это максимальное количество попыток Service Fabric, которые будут предприняты для активации или скачивания приложения на узле, после чего Service Fabric включит serviceType для повторной активации. Это позволяет предоставить службе другую возможность для активации, которая может быть успешной, в результате чего проблема будет автоматически восстановлена. Новая операция активации или загрузки, активируемая путем размещения и активации реплики, может активировать ServiceType Добавление повторно или может быть выполнена.

> [!NOTE]
> Если CodePackage, не регистрирующий ServiceType, не повлияет на ServiceType. Только CodePackage, где происходит сбой реплики, повлияет на ServiceType.
>

### <a name="codepackage-crash"></a>Сбой CodePackage
При сбое CodePackage Service Fabric использует перегрузку для повторного запуска. Режим отхода не зависит от того, зарегистрировал ли пакет кода тип с Service Fabricой средой выполнения.

Значение отхода — min (RetryTime, **активатионмаксретринтервал**), а значение отхода увеличивается в виде константных, линейных или экспоненциальных сумм на основе параметра конфигурации **активатионретрибаккоффекспонентиатионбасе** .

- Константа: Если **активатионретрибаккоффекспонентиатионбасе** = = 0, то RetryTime = **активатионретрибаккоффинтервал**;
- Линейный: Если  **активатионретрибаккоффекспонентиатионбасе** = = 0, то RetryTime = Континуаусфаилурекаунт * **Активатионретрибаккоффинтервал** , где ContinousFailureCount — число сбоев или неудачных попыток активации CodePackage.
- Экспонента: RetryTime = (**активатионретрибаккоффинтервал** в секундах) * (**активатионретрибаккоффекспонентиатионбасе** ^ континуаусфаилурекаунт);
    
Поведением можно управлять, изменяя значения. Например, если требуется несколько попыток быстрого перезапуска, можно использовать линейную шкалу, установив для **активатионретрибаккоффекспонентиатионбасе** значение 0, а для параметра **активатионретрибаккоффинтервал** — значение 10. При использовании этих параметров при сбое CodePackage интервал начала будет составлять через 10 секунд. Если выполнение пакета завершится сбоем, то переход будет изменен на, 20, 30, 40 секунд и так далее до тех пор, пока активация CodePackage не будет успешно выполнена или не будет отключен пакет кода. 
    
Максимальное время, в течение которого Service Fabric отключается (ожидает) после того, как ошибка регулируется **активатионмаксретринтервал**.
    
Если ваш CodePackage аварийно завершает работу и создает резервную копию, он должен остаться на **кодепаккажеконтинуаусекситфаилурересетинтервал** , чтобы Service Fabric считать его работоспособным, после чего он перезапишет предшествующий отчет о работоспособности ошибки как OK и сбросит континаусфаилурекаунт.

### <a name="codepackage-not-registering-servicetype"></a>CodePackage не регистрирует ServiceType
Если CodePackage остается в активном состоянии и ожидается, что он регистрирует ServiceType с нами, но не Service Fabric выдаст предупреждение HealthReport после **параметра servicetyperegistrationtimeout** , что serviceType не был зарегистрирован в течение ожидаемого времени.

### <a name="activation-failure"></a>Сбой активации
Service Fabric всегда использует линейную отработку отказа (то же, что и CodePackage сбой) при обнаружении ошибки во время активации. Это означает, что операция активации будет возобновлена после (от 0 + 10 + 20 + 30 + 40) = 100 сек (первая повторная попытка выполняется немедленно). После повторной попытки активации.
    
Максимальная задержка активации может быть **активатионмаксретринтервал** и повторите **активатионмаксфаилурекаунт**.

### <a name="download-failure"></a>Сбой скачивания
Service Fabric всегда использует линейную отрезку при возникновении ошибки во время загрузки. Это означает, что операция активации будет возобновлена после (от 0 + 10 + 20 + 30 + 40) = 100 сек (первая повторная попытка выполняется немедленно). После этого загрузка не будет повторена. Линейная отключающая выгрузка для загрузки равна Континуаусфаилурекаунт **_деплойментретрибаккоффинтервал_* и может быть максимально возможной в **деплойментмаксретринтервал**. Как и активация, операция скачивания может повторить попытку для **активатионмаксфаилурекаунт**.

> [!NOTE]
> Перед изменением этих параметров необходимо учитывать несколько примеров.

* Если CodePackage поддерживает аварийное завершение работы и выполняет резервное копирование, ServiceType будет отключен. Но если конфигурация активации имеет быстрый перезапуск, CodePackage может быть несколько раз, прежде чем ServiceType будет фактически добавлен. Например, предположим, что у вас есть CodePackage, регистрирует ServiceType с Service Fabric, а затем завершается. В этом случае, когда размещение получает регистрацию типа, **сервицетипедисаблеграцеинтервал** период отменяется. И это может повторяться до тех пор, пока CodePackage не получит значение больше  **сервицетипедисаблеграцеинтервал** , а затем serviceType будет добавлен на узле. Он Майтаке дольше, чем вы видите добавлен ServiceType.

* В случае активации, когда Service Fabric системе необходимо разместить реплику на узле, RA (Реконфигуратионажент) запрашивает размещение подсистемы для активации приложения и повторяет запрос на активацию каждые 15 секунд (регулируется параметром конфигурации **рапмессажеретринтервал** ). Чтобы Service Fabric быть уверенным в том, что ServiceType был добавлен, операция активации в размещении должна находиться в течение более длительного периода, чем интервал повтора и **сервицетипедисаблеграцеинтервал**. Например, предположим, что в кластере для **активатионмаксфаилурекаунт** задано значение 5, а **активатионретрибаккоффинтервал** — 1 секунда. Это означает, что операция активации будет возобновлена после (0 + 1 + 2 + 3 + 4) = 10 секунд (вспомним, что первая повторная попытка выполняется немедленно) и после этого размещение получит повторную попытку. В этом случае операция активации завершится и не будет повторена через 15 секунд. Это происходит потому, что Service Fabric исчерпали все допустимые попытки в течение 15 секунд. Таким образом, при каждой повторной попытке из Реконфигуратионажент создается новая операция активации в подсистеме размещения, и шаблон продолжает повторяться. В результате ServiceType никогда не будет добавлен на узле. Так как ServiceType не будет получать добавлен на узле, реплика не будет перемещена и выполнена на другом узле.
> 

## <a name="deactivation"></a>Деактивация

При активации ServicePackage на узле он будет отслеживанием для деактивации. 

Деактивация работает двумя способами:

- Периодически. при каждом **деактиватионсканинтервал** проверяется наличие сервицепаккажес, где никогда не размещалась реплика, и помечает их как кандидаты для деактивации.
- Репликаклосе: Если реплика закрыта, то деактиватор получает Декрементусажекаунт. Если счетчик имеет значение 0, это означает, что ServicePackage не размещает реплику и, следовательно, является кандидатом на деактивацию.

 В зависимости от режима активации " [монопольный"/"общий][a2]" кандидаты на деактивацию планируются после **деактиватионграцеинтервал** для шаредмоде и после **ексклусивемодедеактиватионграцеинтервал** для ексклусивемоде. Если в течение этого времени происходит новое размещение реплики, деактивация отменяется.

### <a name="periodically"></a>Проверяет
Давайте рассмотрим несколько примеров периодической деактивации.

Пример 1. Предположим, что активатор выполняет сканирование в момент времени T (**деактиватионсканинтервал**). Следующая проверка будет по адресу 2T. Предположим, что активация ServicePackage была выполнена в T + 1. В этом ServicePackage не размещена реплика, поэтому ее необходимо деактивировать. Чтобы ServicePackage быть кандидатом на деактивацию, он должен находиться в состоянии нет реплики в течение крайнего времени T. Это означает, что он будет иметь право деактивации в 2T + 1. Таким образом, проверка в 2T не найдет этот ServicePackage в качестве кандидата для деактивации. Следующий цикл деактивации 3T запланирует эту ServicePackage для деактивации, так как теперь она не находится в состоянии реплики для времени T.  

Пример 2. Предположим, что ServicePackage активируется во время T-1, а деактиватор выполняет сканирование в T. В ServicePackage не размещена реплика. Затем при следующем сканировании 2T этот ServicePackage будет найден как кандидат на деактивацию и, следовательно, будет запланирован на деактивацию.  

Пример 3. Предположим, что ServicePackage активируется в T – 1, а деактиватор выполняет сканирование в T. В ServicePackage еще не размещена реплика. Теперь в T + 1 размещена реплика, т. е. Размещение получает Инкрементусажекаунт, что означает создание реплики. Теперь на 2T этой ServicePackage не будет запланирована деактивация. Так как она содержит реплику, деактивация будет перенесена в логику Репликаклосе, описанную ниже.

Пример 4. Предположим, что ваш ServicePackage имеет большой размер, скажем 10 ГБ, и загрузка на узле может занять некоторое время. После активации приложения деактиватор отслеживает его жизненный цикл. Теперь, если для конфигурации **деактиватионсканинтервал** задано небольшое значение, вы можете столкнуться с проблемами, когда ServicePackage не получает время на активацию на узле, так как все время прошло для загрузки. Чтобы преодолеть проблему, можно [предварительно скачать ServicePackage на узле][p1] или увеличить **деактиватионсканинтервал**. 

> [!NOTE]
> ServicePackage может быть деактивирован в любом месте между (**деактиватионсканинтервал** и 2 **_деактиватионсканинтервал_*) + **деактиватионграцеинтервал** /** ексклусивемодедеактиватионграцеинтервал * *. 
>

### <a name="replica-close"></a>Закрытие реплики:
Деактивация сохраняет количество реплик, которые содержит ServicePackage. Если ServicePackage владеет репликой, а реплика закрыта или отключена, размещение получает Декрементусажекаунт. При открытии реплики размещение получает Инкрементусажекаунт. Уменьшение означает, что в ServicePackage теперь размещается не менее одной реплики, а число сбрасывается в 0, то ServicePackage планирует деактивацию, а время, после которого оно будет деактивировано, будет **деактиватионграцеинтервал** / **ексклусивемодедеактиватионграцеинтервал**. 

Например, предположим, что уменьшение происходит в T, и ServicePackage планируется деактивировать в 2T + X (**деактиватионграцеинтервал** / **ексклусивемодедеактиватионграцеинтервал**). Если во время размещения получается Инкрементусаже, означающее, что реплика создана, деактивация отменяется.

> [!NOTE]
> Что означают эти параметры конфигурации?
**Деактиватионграцеинтервал** / **Ексклусивемодедеактиватионграцеинтервал**. время, переданное ServicePackage на размещение другой реплики после размещения любой реплики. 
**Деактиватионсканинтервал**: минимальное время, заданное ServicePackage для размещения реплики, если она никогда не размещает реплику, т. е. Если не используется.
>

### <a name="ctrlc"></a>CTRL+C
Когда ServicePackage передает **деактиватионграцеинтервал** / **ексклусивемодедеактиватионграцеинтервал** и все еще не размещает реплику, деактивация не может быть отменена. CodePackage выдают обработчик CTRL + C, который означает, что теперь конвейер деактивации должен пройти, чтобы обработать процесс. В течение этого времени, если новая реплика для одного и того же ServicePackageа пытается разместить ее, произойдет сбой, так как не удается перейти от деактивации к активации.

## <a name="cluster-configs"></a>Конфигурации кластера

Конфигурации со значением по умолчанию, влияющим на активацию или декативатион.

### <a name="servicetype"></a>ServiceType
**Сервицетипедисаблефаилуресрешолд**: по умолчанию 1. Пороговое значение счетчика сбоев, после которого FM (FailoverManager) получает уведомления об отключении типа службы на этом узле и попытке размещения другого узла.
**Сервицетипедисаблеграцеинтервал**: по умолчанию 30 с. Интервал времени, по истечении которого можно отключить тип службы.
**Параметра servicetyperegistrationtimeout**: по умолчанию 300 с. Время ожидания регистрации ServiceType в Service Fabric.

### <a name="activation"></a>Активация
**Активатионретрибаккоффинтервал**: по умолчанию 10 с. Интервал перезадержки при каждой ошибке активации.
**Активатионмаксфаилурекаунт**: по умолчанию 20. Максимальное число, для которого система повторит попытку повторной активации, прежде чем приступать к выполнению. 
**Активатионретрибаккоффекспонентиатионбасе**: по умолчанию 1,5.
**Активатионмаксретринтервал**: по умолчанию 3600 с. Максимальное резервирование для активации при сбоях.
**Кодепаккажеконтинуаусекситфаилурересетинтервал**: по умолчанию 300 с. Время ожидания сброса счетчика сбоев непрерывного выхода для CodePackage.

### <a name="download"></a>Скачать
**Деплойментретрибаккоффинтервал**: по умолчанию 10. Задержка интервала сбоев развертывания.
**Деплойментмаксретринтервал**: по умолчанию 3600 с. Максимальное резервное копирование для развертывания при сбоях.
**Деплойментмаксфаилурекаунт**: по умолчанию 20. Прежде чем развертывание приложения на узле завершится сбоем, это развертывание будет выполнено столько раз, сколько задано для DeploymentMaxFailureCount.

### <a name="deactivation"></a>Деактивация
**Деактиватионсканинтервал**: по умолчанию 600 с. Минимальное время, заданное ServicePackage для размещения реплики, если она никогда не размещает реплику, т. е. Если не используется.
**Деактиватионграцеинтервал**: по умолчанию 60 с. Время, переданное ServicePackage на размещение еще одной реплики, после размещения любой реплики в случае модели с **общим** процессом.
**Ексклусивемодедеактиватионграцеинтервал**: по умолчанию 1 с. Время, переданное ServicePackage на размещение еще одной реплики, после размещения любой реплики в случае модели с **монопольным** процессом.

## <a name="next-steps"></a>Дальнейшие действия
[Создайте пакет приложения][a3] и подготовьте его к развертыванию.

[Развертывание и удаление приложений с помощью PowerShell][a4]. В статье представлены сведения об управлении экземплярами приложений с помощью PowerShell.

<!--Link references--In actual articles, you only need a single period before the slash-->
[a1]: service-fabric-application-model.md
[a2]: service-fabric-hosting-model.md
[a3]: service-fabric-package-apps.md
[a4]: service-fabric-deploy-remove-applications.md

[p1]: /powershell/module/servicefabric/copy-servicefabricservicepackagetonode
