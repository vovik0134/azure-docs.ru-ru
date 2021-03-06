---
title: Настройка шифрования Kerberos Нфсв 4.1 для Azure NetApp Files | Документация Майкрософт
description: Описание настройки шифрования Нфсв 4.1 Kerberos для Azure NetApp Files и влияния на производительность.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 11/09/2020
ms.author: b-juche
ms.openlocfilehash: 1ffbcc24a2ee386be1a8ce50b55375d5bd458df1
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/10/2020
ms.locfileid: "94410102"
---
# <a name="configure-nfsv41-kerberos-encryption-for-azure-netapp-files"></a>Настройка шифрования Kerberos Нфсв 4.1 для Azure NetApp Files

Azure NetApp Files поддерживает шифрование клиентов NFS в режимах Kerberos (krb5, krb5i и krb5p) с шифрованием AES-256. В этой статье описываются необходимые конфигурации для использования тома Нфсв 4.1 с шифрованием Kerberos.

## <a name="requirements"></a>Требования

К шифрованию клиента Нфсв 4.1 применяются следующие требования. 

* Подключение домен Active Directory Services (AD DS) для упрощения работы с билетами Kerberos 
* Создание записей A/PTR DNS для клиентских и Azure NetApp Filesных IP-адресов сервера NFS
* Клиент Linux  
    В этой статье приводятся рекомендации для клиентов RHEL и Ubuntu.  Другие клиенты будут работать с аналогичными действиями по настройке. 
* Доступ к NTP-серверу  
    Можно использовать один из часто используемых контроллеров домена контроллера домен Active Directory (AD DC).

## <a name="create-an-nfs-kerberos-volume"></a>Создание тома Kerberos NFS

1.  Выполните действия, описанные в статье [Создание тома NFS для Azure NetApp Files](azure-netapp-files-create-volumes.md) , чтобы создать том нфсв 4.1.   

    На странице Создание тома задайте для параметра версия NFS значение **нфсв 4.1** и установите для параметра Kerberos значение **включено**.

    > [!IMPORTANT] 
    > Вы не можете изменить выбор включения Kerberos после создания тома.

    ![Создание тома Kerberos Нфсв 4.1](../media/azure-netapp-files/create-kerberos-volume.png)  

2. Выберите **Экспорт политики** в соответствии с требуемым уровнем доступа и параметром безопасности (Kerberos 5, Kerberos 5i или Kerberos 5p) для тома.   

    Сведения о влиянии Kerberos на производительность см. [в статье влияние Kerberos на нфсв 4.1](#kerberos_performance).  

    Можно также изменить методы безопасности Kerberos для тома, нажав кнопку Экспорт политики в области навигации Azure NetApp Files.

3.  Щелкните **Проверка + создать** , чтобы создать том нфсв 4.1.

## <a name="configure-the-azure-portal"></a>Настройка портала Azure 

1.  Следуйте инструкциям в разделе [Создание подключения Active Directory](azure-netapp-files-create-volumes-smb.md#create-an-active-directory-connection).  

    Для использования протокола Kerberos необходимо создать хотя бы одну учетную запись компьютера в Active Directory. Предоставленные сведения об учетной записи используются для создания учетных записей для томов Kerberos *и* нфсв 4.1. Учетная запись этого компьютера создается автоматически во время создания тома.

2.  В разделе **область Kerberos** введите **имя сервера AD** и **IP-адрес KDC** .

    Сервер AD и IP-адрес KDC могут быть одним и тем же сервером. Эти сведения используются для создания учетной записи компьютера-участника-службы, используемой Azure NetApp Files. После создания учетной записи компьютера Azure NetApp Files будет использовать записи DNS-сервера для нахождение дополнительных серверов KDC по мере необходимости. 

    ![Область Kerberos](../media/azure-netapp-files/kerberos-realm.png)
 
3.  Нажмите кнопку **присоединить** , чтобы сохранить конфигурацию.

## <a name="configure-active-directory-connection"></a>Настройка подключения Active Directory 

Конфигурация Нфсв 4.1 Kerberos создает две учетные записи компьютеров в Active Directory:
* Учетная запись компьютера для общих ресурсов SMB
* Учетная запись компьютера для Нфсв 4.1 — вы можете указать эту учетную запись с помощью префикса `NFS-` . 

После создания первого тома Kerberos Нфсв 4.1 задайте тип шифрования для учетной записи компьютера с помощью следующей команды PowerShell:

`Set-ADComputer $NFSCOMPUTERACCOUNT -KerberosEncryptionType AES256`

## <a name="configure-the-nfs-client"></a>Настройка клиента NFS 

Следуйте инструкциям в разделе [Настройка клиента NFS для Azure NetApp Files](configure-nfs-clients.md) , чтобы настроить клиент NFS.  

## <a name="mount-the-nfs-kerberos-volume"></a><a name="kerberos_mount"></a>Подключение тома Kerberos NFS

1. На странице **тома** выберите том NFS, который требуется подключить.

2. Выберите **инструкции по подключению** тома, чтобы отобразить инструкции.

    Пример: 

    ![Инструкции по подключению для томов Kerberos](../media/azure-netapp-files/mount-instructions-kerberos-volume.png)  

3. Создайте каталог (точку подключения) для нового тома.  

4. Задайте для параметра Тип шифрования по умолчанию значение AES 256 для учетной записи компьютера:  
    `Set-ADComputer $NFSCOMPUTERACCOUNT -KerberosEncryptionType AES256 -Credential $ANFSERVICEACCOUNT`

    * Эту команду необходимо выполнить только один раз для каждой учетной записи компьютера.
    * Эту команду можно выполнить из контроллера домена или с компьютера с установленным [RSAT](https://support.microsoft.com/help/2693643/remote-server-administration-tools-rsat-for-windows-operating-systems) . 
    * `$NFSCOMPUTERACCOUNT`Переменная — это учетная запись компьютера, созданная в Active Directory при развертывании тома Kerberos. Это учетная запись с префиксом `NFS-` . 
    * `$ANFSERVICEACCOUNT`Переменная является непривилегированной Active Directory учетной записью пользователя с делегированными элементами управления в подразделении, в котором создана учетная запись компьютера. 

5. Подключите том на узле: 

    `sudo mount -t nfs -o sec=krb5p,rw,hard,rsize=1048576,wsize=1048576,vers=4.1,tcp $ANFEXPORT $ANFMOUNTPOINT`

    * `$ANFEXPORT`Переменная является путем, `host:/export` найденным в инструкциях по подключению.
    * `$ANFMOUNTPOINT`Переменная представляет собой папку, созданную пользователем на узле Linux.

## <a name="performance-impact-of-kerberos-on-nfsv41"></a><a name="kerberos_performance"></a>Влияние Kerberos на производительность Нфсв 4.1 

Этот раздел поможет понять влияние Kerberos на производительность Нфсв 4.1.

### <a name="available-security-options"></a>Доступные параметры безопасности 

Ниже перечислены параметры безопасности, доступные в настоящее время для томов Нфсв 4.1. 

* **sec = sys использует локальные идентификаторы** UID UNIX и GUID, используя AUTH_SYS для проверки подлинности операций NFS.
* **с = krb5** использует Kerberos V5 вместо ЛОКАЛЬНЫХ UID UNIX и GUID для проверки подлинности пользователей.
* **с = krb5i** использует Kerberos V5 для проверки подлинности пользователя и выполняет проверку целостности операций NFS с помощью безопасных контрольных сумм для предотвращения незаконного изменения данных.
* **с = krb5p** использует Kerberos V5 для проверки подлинности пользователя и проверки целостности. Он шифрует трафик NFS для предотвращения перехвата трафика. Этот параметр является наиболее безопасным, но он также включает в себя наибольшую нагрузку на производительность.

### <a name="performance-vectors-tested"></a>Протестированные векторы производительности

В этом разделе описывается влияние различных параметров на производительность на стороне клиента `sec=*` .

* Влияние на производительность было протестировано на двух уровнях: низкая степень параллелизма (низкая нагрузка) и высокая степень параллелизма (верхние пределы операций ввода-вывода и пропускной способности).  
* Были протестированы три типа рабочих нагрузок:  
    * Произвольная операция для произвольного чтения и записи (с использованием FIO)
    * Последовательное выполнение больших операций чтения и записи (с использованием FIO)
    * Интенсивная рабочая нагрузка метаданных, создаваемая приложениями, например Git

### <a name="expected-performance-impact"></a>Ожидаемое влияние на производительность 

Существует две области фокуса: интенсивная загрузка и верхний предел. В следующих списках описываются параметры безопасности, влияющие на производительность, в параметрах безопасности и сценариях. Все сравнения выполняются с `sec=sys` параметром безопасности. Тест выполнен на одном томе с использованием одного клиента. 

Влияние krb5 на производительность:

* Низкая степень параллелизма (r/w):
    * Последовательная задержка увеличена 0,3 мс.
    * Случайная задержка ввода-вывода увеличилась 0,2 мс.
    * Задержка ввода-вывода метаданных увеличена в 0,2 мс.
* Высокая степень параллелизма (r/w): 
    * Krb5 не влияет на максимальную последовательность пропускной способности.
    * Максимальное число случайных операций ввода-вывода, уменьшенное на 30% для рабочих нагрузок чистого чтения, с общим влиянием на ноль, когда Рабочая нагрузка перемещается в чистую запись. 
    * Максимальная рабочая нагрузка метаданных уменьшила 30%.

Влияние krb5i на производительность: 

* Низкая степень параллелизма (r/w):
    * Последовательная задержка увеличена 0,5 мс.
    * Случайная задержка ввода-вывода увеличилась 0,2 мс.
    * Задержка ввода-вывода метаданных увеличена в 0,2 мс.
* Высокая степень параллелизма (r/w): 
    * Максимальная последовательная пропускная способность, уменьшенная на 70%, зависит от смешанной рабочей нагрузки.
    * Максимальное число операций ввода-вывода, уменьшенное на 50% для рабочих нагрузок чистого чтения с общим влиянием снижения на 25% по мере смены рабочей нагрузки на чистую запись. 
    * Максимальная рабочая нагрузка метаданных уменьшила 30%.

Влияние krb5p на производительность:

* Низкая степень параллелизма (r/w):
    * Последовательная задержка увеличена 0,8 мс.
    * Случайная задержка ввода-вывода увеличилась 0,2 мс.
    * Задержка ввода-вывода метаданных увеличена в 0,2 мс.
* Высокая степень параллелизма (r/w): 
    * Максимальная последовательная пропускная способность, уменьшенная на 85%, зависит от смешанной рабочей нагрузки. 
    * Максимальное число операций ввода-вывода, уменьшенное на 65% для чистых рабочих нагрузок чтения, с общим влиянием снижения на 43% по мере смены рабочей нагрузки на чистую запись. 
    * Максимальная рабочая нагрузка метаданных уменьшила 30%.

## <a name="next-steps"></a>Дальнейшие действия  

* [Часто задаваемые вопросы о Azure NetApp Files](azure-netapp-files-faqs.md)
* [Создание тома NFS для Azure NetApp Files](azure-netapp-files-create-volumes.md)
* [Создание подключения Active Directory](azure-netapp-files-create-volumes-smb.md#create-an-active-directory-connection)
* [Настройка клиента NFS для Azure NetApp Files](configure-nfs-clients.md) 
