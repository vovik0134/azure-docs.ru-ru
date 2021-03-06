---
title: Заметки о выпуске Azure Stack ребра Pro | Документация Майкрософт
description: В этой статье описываются критические открытые проблемы и способы их устранения для Azure Stack пограничных устройств с предварительной версией доступности.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 09/07/2020
ms.author: alkohli
ms.openlocfilehash: 25db4e7f3e4e1f7056979c4c40c6ffc61f340439
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/30/2020
ms.locfileid: "96345377"
---
# <a name="azure-stack-edge-pro-with-gpu-preview-release-notes"></a>Заметки о выпуске Azure Stack ребра Pro с предварительной версией GPU

В следующих заметках о выпуске указываются критические открытые проблемы и разрешенные проблемы для предварительной версии 2008 для устройств Azure Stack пограничных Pro с графическим процессором.

Заметки о выпуске постоянно обновляются: обнаруживаются и добавляются критические проблемы, требующие временного решения. Перед развертыванием устройства Azure Stack пограничной Pro внимательно прочтите сведения, содержащиеся в заметках о выпуске.

Эта статья относится к следующему выпуску программного обеспечения — **Azure Stack ребр Pro 2008**. 

<!--- **2.1.1328.1904**-->

## <a name="whats-new"></a>Новое

В выпуске Azure Stack ребра 2008 добавлены следующие новые функции. В зависимости от конкретной используемой версии программного обеспечения вы можете увидеть подмножество этих функций. 

- **Классы хранения** . в предыдущем выпуске можно было только статически подготавливать хранилище с помощью общих папок SMB или NFS для приложений с отслеживанием состояния, развернутых в кластере Kubernetes, работающем на устройстве с Azure Stack погранично Pro. В этом выпуске добавлены классы хранения, позволяющие динамически подготавливать хранилище. Дополнительные сведения см. [в статье Kubernetes Storage Management on a Azure Stack ребра Pro GPU Device](azure-stack-edge-gpu-kubernetes-storage.md#dynamicprovisioning). 
- **Панель мониторинга Kubernetes с сервером метрик** . в этом выпуске панель мониторинга Kubernetes добавляется с надстройкой сервера метрик. Вы можете использовать панель мониторинга, чтобы получить общие сведения о приложениях, выполняемых на устройстве Azure Stack пограничной Pro, просмотреть состояние ресурсов кластера Kubernetes и просмотреть ошибки, произошедшие на устройстве. Сервер метрик собирает сведения об использовании ЦП и памяти для ресурсов Kubernetes на устройстве. Дополнительные сведения см. [в статье Использование панели мониторинга Kubernetes для мониторинга устройства GPU Azure Stack](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md).
- Служба " **дуга Azure для Azure Stack** ". Начиная с этого выпуска, вы можете развертывать рабочие нагрузки приложений на устройстве Azure Stack с помощью службы "Дуга" в Azure. Служба "Дуга Azure" — это гибридное средство управления, которое позволяет развертывать приложения в кластерах Kubernetes. Дополнительные сведения см. [в статье Развертывание рабочих нагрузок с помощью дуги Azure на устройстве Azure Stack пограничном Pro](azure-stack-edge-gpu-deploy-arc-kubernetes-cluster.md).  

## <a name="known-issues"></a>Известные проблемы 

В следующей таблице приведена сводка известных проблем для устройства Azure Stack ребра Pro.

| Нет. | Компонент | Проблема | Временное решение и комментарии |
| --- | --- | --- | --- |
| **1.** |Azure Stack пограничных Pro + SQL Azure | Для создания базы данных SQL требуется доступ администратора.   |Выполните следующие действия вместо шагов 1-2 в [https://docs.microsoft.com/azure/iot-edge/tutorial-store-data-sql-server#create-the-sql-database](../iot-edge/tutorial-store-data-sql-server.md#create-the-sql-database) . <ul><li>В локальном пользовательском интерфейсе устройства Включите интерфейс вычислений. Выберите **вычисление > порт # > включить для вычислений > применить.**</li><li>Скачайте `sqlcmd` на клиентский компьютер с https://docs.microsoft.com/sql/tools/sqlcmd-utility </li><li>Подключитесь к IP-адресу интерфейса вычислений (включенному порту), добавив ", 1401" в конец адреса.</li><li>Окончательная команда будет выглядеть следующим образом: sqlcmd-S {Interface IP}, 1401-U SA-P «strong! Passw0rd".</li>После этого шаги 3-4 из текущей документации должны быть идентичны. </li></ul> |
| **2.** |Обновить| Добавочные изменения в больших двоичных объектах, восстановленных с помощью **обновления** , не поддерживаются |Для конечных точек большого двоичного объекта частичные обновления больших двоичных объектов после обновления могут привести к тому, что обновления не будут отправлены в облако. Например, последовательность действий, например:<ul><li>Создание большого двоичного объекта в облаке. Или удалите ранее отправленный BLOB-объект с устройства.</li><li>Обновите BLOB-объект из облака в устройство с помощью функции обновления.</li><li>Обновление только части большого двоичного объекта с помощью API-интерфейсов Azure SDK.</li></ul>Эти действия могут привести к тому, что обновленные разделы BLOB-объекта не будут обновлены в облаке. <br>**Обходное решение**. Используйте такие средства, как Robocopy, или регулярное копирование файлов с помощью Explorer или командной строки, чтобы заменить все большие двоичные объекты.|
|**3.**|Регулирование|При регулировании, если новые операции записи в устройство не разрешены, операции записи, выполненные клиентом NFS, завершаются ошибкой "отказ в разрешении".| Эта ошибка будет показана ниже:<br>`hcsuser@ubuntu-vm:~/nfstest$ mkdir test`<br>mkdir: не удается создать каталог "тест": отказано в разрешении|
|**4.**|Прием хранилища BLOB-объектов|При использовании AzCopy версии 10 для приема хранилища BLOB-объектов запустите AzCopy со следующим аргументом: `Azcopy <other arguments> --cap-mbps 2000`| Если эти ограничения не указаны для AzCopy, это может привести к отправке большого количества запросов на устройство и возникновению проблем со службой.|
|**5.**|Многоуровневые учетные записи хранения|При использовании многоуровневых учетных записей хранения применяются следующие условия.<ul><li> Поддерживаются только блочные BLOB-объекты. Страничные BLOB-объекты не поддерживаются.</li><li>Поддержка API моментальных снимков или копирования отсутствует.</li><li> Прием рабочей нагрузки Hadoop с помощью `distcp` не поддерживается, так как он интенсивно использует операцию копирования.</li></ul>||
|**6.**|Подключение к общему ресурсу NFS|Если несколько процессов копируются в одну общую папку и `nolock` атрибут не используется, во время копирования могут возникнуть ошибки.|`nolock`Для копирования файлов в общую папку NFS необходимо передать атрибут в команду mount. Например, `C:\Users\aseuser mount -o anon \\10.1.1.211\mnt\vms Z:`.|
|**7.**|Кластер Kubernetes|При применении обновления на устройстве, на котором работает кластер kubernetes, виртуальные машины kubernetes перезапускаются и перезагружаются. В этом случае только те модули, которые развернуты с указанными репликами, автоматически восстанавливаются после обновления.  |Если вы создали отдельные модули Pod за пределами контроллера репликации без указания набора реплик, эти модули не будут автоматически восстановлены после обновления устройства. Вам потребуется восстановить эти модули.<br>Набор реплик заменяет удаляемые или завершенные модули Pod по какой-либо причине, например сбой узла или аварийное обновление узла. По этой причине рекомендуется использовать набор реплик, даже если приложению требуется только один модуль.|
|**8.**|Кластер Kubernetes|Kubernetes на Azure Stack пограничной Pro поддерживается только в версии Helm V3 или более поздней. Дополнительные сведения см. в подразделах " [часто задаваемые вопросы": удаление ящика](https://v3.helm.sh/docs/faq/).|
|**9.**|Azure Arc + Azure Stack пограничная Pro|Развертывания ARC в Azure не поддерживаются, если на устройстве Azure Stack пограничной Pro настроен веб-прокси.||
|**штук.**|Kubernetes |Порт 31000 зарезервирован для панели мониторинга Kubernetes. Аналогично, в конфигурации по умолчанию IP-адреса 172.28.0.1 и 172.28.0.10 зарезервированы для службы Kubernetes и основной службы DNS соответственно.|Не используйте зарезервированные IP-адреса.|
|**стр.**|Kubernetes |В настоящее время Kubernetes не поддерживает службы балансировки нагрузки с несколькими протоколами. Например, служба DNS должна прослушивать TCP и UDP. |Чтобы обойти это ограничение Kubernetes с Металлб, две службы (одна для TCP, одна для UDP) можно создать на одном селекторе Pod. Эти службы используют один и тот же ключ общего доступа и Spec. Лоадбаланцерип для совместного использования одного и того же IP-адреса. IP-адреса также могут быть общими, если у вас больше служб, чем доступные. <br> Дополнительные сведения см. в разделе [совместное использование IP-адресов](https://metallb.universe.tf/usage/#ip-address-sharing).|
|**Двенадцать.**|Кластер Kubernetes|Существующие модули Azure IoT Edge Marketplace не будут работать в кластере Kubernetes, как платформа размещения для IoT Edge на Azure Stack пограничном устройстве.|Необходимо изменить модули, прежде чем они будут развернуты на Azure Stack пограничном устройстве. Дополнительные сведения см. в статье изменение модулей Azure IoT Edge из Marketplace для запуска на Azure Stack пограничном устройстве.<!-- insert link-->|
|**решением.**|Kubernetes |Подключения на основе файлов не поддерживаются для Azure IoT Edge в Kubernetes на Azure Stack пограничном устройстве.|IoT Edge использует слой перевода для преобразования `ContainerCreate` параметров в конструкции Kubernetes. Создание `Binds` сопоставлений с каталогом хостпас или создание и, таким же, присоединение файловой привязки на основе файлов не может быть привязано к путям в контейнерах IOT Edge.|
|**открыт.**|Kubernetes |Если вы используете собственные сертификаты для IoT Edge и добавляете их на Azure Stack пограничной устройстве, новые сертификаты не будут выбраны в ходе обновления Helm диаграмм.|Чтобы решить эту проблему, [подключитесь к интерфейсу PowerShell устройства](azure-stack-edge-gpu-connect-powershell-interface.md). Перезагрузите и модули Pod `iotedged` `edgehub` .|
|**15.**|Сертификаты |В некоторых случаях обновление состояния сертификата в локальном пользовательском интерфейсе может занять несколько секунд. |Могут повлиять на следующие сценарии в локальном пользовательском интерфейсе.<ul><li>Столбец **Status (состояние** ) на странице **Сертификаты** .</li><li>Плитка " **Безопасность** " на странице " **Начало работы** ".</li><li>Плитка **настройки** на странице **обзора** .</li></ul>  |

## <a name="next-steps"></a>Дальнейшие действия

- [Подготовка к развертыванию Azure Stack пограничных устройств Pro с помощью GPU](azure-stack-edge-gpu-deploy-prep.md)