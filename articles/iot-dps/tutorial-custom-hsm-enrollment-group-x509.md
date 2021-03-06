---
title: Учебник по подготовке устройств X.509 в Центре Интернета вещей Azure с помощью настраиваемого аппаратного модуля безопасности (HSM)
description: В этом учебнике используются группы регистрации. В этом учебнике описано, как подготавливать устройства X. 509 с помощью настраиваемого аппаратного модуля безопасности (HSM) и пакета SDK для устройств C для Службы подготовки устройств к добавлению в Центр Интернета вещей Azure (DPS).
author: wesmc7777
ms.author: wesmc
ms.date: 11/18/2020
ms.topic: tutorial
ms.service: iot-dps
services: iot-dps
ms.custom: mvc
ms.openlocfilehash: 6845923d65b5fbe5a9f010474330ce2bbed948e1
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/07/2020
ms.locfileid: "96780099"
---
# <a name="tutorial-provision-multiple-x509-devices-using-enrollment-groups"></a>Руководство по инициализации нескольких устройств X.509 с помощью групп регистрации

Из этого учебника вы узнаете, как подготавливать группы устройств Интернет вещей, которые используют сертификаты X.509 для проверки подлинности. Для подготовки компьютера разработки в качестве устройства Интернета вещей будет использоваться пример кода из [пакета SDK Интернета вещей Azure для C](https://github.com/Azure/azure-iot-sdk-c). 

Служба подготовки устройств Интернета вещей Azure поддерживает два типа регистрации:

* [Группы регистрации](concepts-service.md#enrollment-group). Используются для регистрации нескольких связанных устройств.
* [Индивидуальные регистрации](concepts-service.md#individual-enrollment). Предназначены для регистрации одного устройства.

Этот учебник похож на предыдущие учебники, в которых показано, как использовать группы регистрации для подготовки наборов устройств. Однако в этом учебнике предполагается, что вместо симметричных ключей будут использоваться сертификаты X.509. Ознакомьтесь с предыдущими учебниками в этом разделе, чтобы узнать о простом методе использования [симметричных ключей](./concepts-symmetric-key-attestation.md).

В этом учебнике демонстрируется [пример настраиваемого модуля HSM](https://github.com/Azure/azure-iot-sdk-c/tree/master/provisioning_client/samples/custom_hsm_example), обеспечивающего реализацию заглушки для взаимодействия с защищенным аппаратным хранилищем. [Аппаратный модуль безопасности (HSM)](./concepts-service.md#hardware-security-module) используется для создания безопасного аппаратного хранилища секретов устройств. Для обеспечения безопасного хранение секретов HSM можно использовать с симметричным ключом, сертификатом X.509 или аттестацией доверенного платформенного модуля. Хранилище секретов устройств на основе оборудования не требуется, но мы настоятельно рекомендуем использовать это хранилище для защиты конфиденциальной информации, такой как закрытый ключ сертификата устройства.

Если вы не знакомы с процессом автоматической подготовки, см. раздел [Роли и учетные записи Azure](about-iot-dps.md#provisioning-process). Кроме того, прежде чем продолжить работу с этим учебником, выполните шаги, описанные в статье [Настройка Службы подготовки устройств к добавлению в Центр Интернета вещей на портале Azure](quick-setup-auto-provision.md). 


В этом учебнике описано, как выполнить следующие задачи:

> [!div class="checklist"]
> * Создание цепочки сертификатов для доверия для упорядочения набора устройств с помощью сертификатов X.509.
> * Выполнение проверки принадлежности с помощью сертификата для подписи, используемого с цепочкой сертификатов.
> * Создание новой регистрации группы, использующей цепочку сертификатов
> * Подготовка среды разработки для подготовки устройства из [пакета SDK Интернета вещей Azure для C](https://github.com/Azure/azure-iot-sdk-c).
> * Подготовка устройства с помощью цепочки сертификатов с использованием примера настраиваемого аппаратного модуля безопасности (HSM) в пакете SDK.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Предварительные требования

Приведенные ниже предварительные требования касаются среды разработки Windows. При использовании Linux или macOS ознакомьтесь с соответствующим разделом в статье [Подготовка среды разработки](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) из документации к пакету SDK.

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) с включенной рабочей нагрузкой [Разработка классических приложений на C++](https://docs.microsoft.com/cpp/ide/using-the-visual-studio-ide-for-cpp-desktop-development). Visual Studio 2015 или Visual Studio 2017 также поддерживаются.

* Установите последнюю версию [Git](https://git-scm.com/download/).

## <a name="prepare-the-azure-iot-c-sdk-development-environment"></a>Подготовка среды разработки для пакета SDK для языка C Azure IoT

В этом разделе вы подготовите среду разработки, которая используется для сборки [пакета SDK Azure IoT для C](https://github.com/Azure/azure-iot-sdk-c). Пакет SDK включает примеры кода и средства, используемые устройствами X.509, которые подготавливаются с помощью DPS.

1. Скачайте [систему сборки CMake](https://cmake.org/download/).

    **Перед** установкой `CMake` очень важно установить на компьютер необходимые компоненты Visual Studio ([Visual Studio](https://visualstudio.microsoft.com/vs/) с рабочей нагрузкой [Разработка классических приложений на C++](https://docs.microsoft.com/cpp/ide/using-the-visual-studio-ide-for-cpp-desktop-development)). После установки компонентов и проверки загрузки установите систему сборки CMake.

2. Найдите имя тега для [последнего выпуска](https://github.com/Azure/azure-iot-sdk-c/releases/latest) пакета SDK Azure IoT для C.

3. Откройте командную строку или оболочку Git Bash. Выполните приведенные ниже команды, чтобы клонировать репозиторий GitHub с последним выпуском [пакета SDK Azure IoT для C](https://github.com/Azure/azure-iot-sdk-c). Используйте найденный тег в качестве значения для параметра `-b`:

    ```cmd/sh
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Выполнение этой операции может занять несколько минут.

4. Создайте подкаталог `cmake` в корневом каталоге репозитория Git и перейдите в эту папку. 

    ```cmd/sh
    mkdir cmake
    cd cmake
    ```

5. Созданный вами каталог `cmake` будет содержать пример настраиваемого модуля HSM и пример кода подготовки устройства, который использует настраиваемый модуль HSM для обеспечения проверки подлинности X.509. 

    Выполните приведенную ниже команду из каталога `cmake`, чтобы создать версию пакета SDK для используемой платформы разработки. Сборка будет содержать ссылку на пример настраиваемого модуля HSM. 

    При указании пути, используемого с `-Dhsm_custom_lib`, как показано ниже, этот путь следует задать относительно созданного ранее каталога `cmake`. Показанный ниже относительный путь приведен только для примера.

    ```cmd
    $ cmake -Duse_prov_client:BOOL=ON -Dhsm_custom_lib=/d/azure-iot-sdk-c/cmake/provisioning_client/samples/custom_hsm_example/Debug/custom_hsm_example.lib ..
    ```

    Если `cmake` не удастся найти компилятор C++, могут возникнуть ошибки сборки во время выполнения предыдущей команды. В этом случае попробуйте, выполнить эту команду в [командной строке Visual Studio](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs).

    После завершения сборки в каталоге `cmake` будет создано решение Visual Studio. Последние несколько строк выходных данных выглядят следующим образом:

    ```cmd/sh
    $ cmake -Duse_prov_client:BOOL=ON -Dhsm_custom_lib=/d/azure-iot-sdk-c/cmake/provisioning_client/samples/custom_hsm_example/Debug/custom_hsm_example.lib ..
    -- Building for: Visual Studio 16 2019
    -- The C compiler identification is MSVC 19.23.28107.0
    -- The CXX compiler identification is MSVC 19.23.28107.0

    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: D:/azure-iot-sdk-c/cmake
    ```

## <a name="create-an-x509-certificate-chain"></a>Создание цепочки сертификатов X.509

В этом разделе описано, как создать цепочку сертификатов X.509 из трех сертификатов для тестирования в соответствии с указаниями этого учебника. Сертификаты будут иметь следующую иерархию.

![Цепочка сертификатов устройства в учебнике](./media/tutorial-custom-hsm-enrollment-group-x509/example-device-cert-chain.png#lightbox)

[Корневой сертификат](concepts-x509-attestation.md#root-certificate). Для проверки корневого сертификата следует [подтвердить принадлежность](how-to-verify-certificates.md). Благодаря этой проверке служба DPS сможет доверять этому сертификату и проверять подписанные им сертификаты.

[Промежуточный сертификат](concepts-x509-attestation.md#intermediate-certificate) Обычно промежуточные сертификаты используются для логической группировки устройств по линейкам продуктов, подразделениям компании или другим критериям. В этом учебнике будет рассматриваться цепочка сертификатов, состоящая из одного промежуточного сертификата. Промежуточный сертификат будет подписан корневым сертификатом. Этот сертификат также будет использоваться для логической группировки набора устройств в группе регистрации, созданной в DPS. Эта конфигурация позволяет управлять всей группой устройств, сертификаты которых подписаны одним промежуточным сертификатом. Вы можете создать группы регистрации для включения или отключения группы устройств. Дополнительные сведения об отключении группы устройств см. в разделе [Запрет промежуточного или корневого сертификата центра сертификации X.509 с помощью группы регистрации](how-to-revoke-device-access-portal.md#disallow-an-x509-intermediate-or-root-ca-certificate-by-using-an-enrollment-group).

[Сертификат устройства](concepts-x509-attestation.md#end-entity-leaf-certificate). Сертификат устройства (конечный сертификат) будет подписан промежуточным сертификатом и сохранен на устройстве вместе с его закрытым ключом. При попытке подготовки устройство будет представлять этот сертификат и закрытый ключ вместе с цепочкой сертификатов. 

Чтобы создать цепочку сертификатов, выполните следующие действия.

1. Откройте окно командной строки Git Bash. Выполните шаги 1 и 2 с помощью инструкций оболочки Bash, которые описаны в статье [Управление тестовыми сертификатами центра сертификации для примеров и учебников](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md#managing-test-ca-certificates-for-samples-and-tutorials).

    На этом шаге создается рабочий каталог для скриптов сертификатов и пример корневого и промежуточного сертификатов для цепочки сертификатов, использующей OpenSSL. 

    Обратите внимание, что в выходных данных отображается расположение самозаверяющего корневого сертификата. Позже этот сертификат будет проходить процедуру [подтверждения владения](how-to-verify-certificates.md).

    ```output
    Creating the Root CA Certificate
    CA Root Certificate Generated At:
    ---------------------------------
        ./certs/azure-iot-test-only.root.ca.cert.pem
    
    Certificate:
        Data:
            Version: 3 (0x2)
            Serial Number:
                fc:cc:6b:ab:3b:9a:3e:fe
        Signature Algorithm: sha256WithRSAEncryption
            Issuer: CN=Azure IoT Hub CA Cert Test Only
            Validity
                Not Before: Oct 23 21:30:30 2020 GMT
                Not After : Nov 22 21:30:30 2020 GMT
            Subject: CN=Azure IoT Hub CA Cert Test Only
    ```        

    Обратите внимание, что в выходных данных отображается расположение промежуточного сертификата, подписанного и выданного корневым сертификатом. Этот сертификат будет использоваться с группой регистрации, которая будет создана позже.

    ```output
    Intermediate CA Certificate Generated At:
    -----------------------------------------
        ./certs/azure-iot-test-only.intermediate.cert.pem
    
    Certificate:
        Data:
            Version: 3 (0x2)
            Serial Number: 1 (0x1)
        Signature Algorithm: sha256WithRSAEncryption
            Issuer: CN=Azure IoT Hub CA Cert Test Only
            Validity
                Not Before: Oct 23 21:30:33 2020 GMT
                Not After : Nov 22 21:30:33 2020 GMT
            Subject: CN=Azure IoT Hub Intermediate Cert Test Only
    ```    

2. Затем будет выполнена следующая команда для создания нового устройства или конечного сертификата с именем субъекта, указанным в качестве параметра. Используйте имя примера субъекта `custom-hsm-device-01`, предусмотренное для данного учебника. Это имя субъекта будет служить идентификатором вашего устройства Интернета вещей. 

    > [!WARNING]
    > Не используйте имя субъекта с пробелами. Это имя субъекта является кодом подготавливаемого устройства Интернета вещей. Оно должно соответствовать правилам для кода устройства. Дополнительные сведения см. в разделе [Свойства удостоверений устройств](../iot-hub/iot-hub-devguide-identity-registry.md#device-identity-properties).

    ```cmd
    ./certGen.sh create_device_certificate_from_intermediate "custom-hsm-device-01"
    ```

    Обратите внимание, что в следующих выходных данных указано, где находится новый сертификат устройства. Сертификат устройства подписывается (выдается) промежуточным сертификатом.

    ```output
    -----------------------------------
    ./certs/new-device.cert.pem: OK
    Leaf Device Certificate Generated At:
    ----------------------------------------
        ./certs/new-device.cert.pem
    
    Certificate:
        Data:
            Version: 3 (0x2)
            Serial Number: 9 (0x9)
        Signature Algorithm: sha256WithRSAEncryption
            Issuer: CN=Azure IoT Hub Intermediate Cert Test Only
            Validity
                Not Before: Nov 10 09:20:33 2020 GMT
                Not After : Dec 10 09:20:33 2020 GMT
            Subject: CN=custom-hsm-device-01
    ```    
    
3. Выполните следующую команду, чтобы создать PEM-файл полной цепочки сертификатов, включающий новый сертификат устройства.

    ```Bash
    cd ./certs && cat new-device.cert.pem azure-iot-test-only.intermediate.cert.pem azure-iot-test-only.root.ca.cert.pem > new-device-full-chain.cert.pem
    ```

    Используйте текстовый редактор и откройте файл цепочки сертификатов *./certs/new-device-full-chain.cert.pem*. Текст цепочки сертификатов содержит полную цепочку всех трех сертификатов. Этот текст будет использоваться в качестве цепочки сертификатов с кодом настраиваемого модуля HSM далее в этом учебнике.

    Текст полной цепочки имеет следующий формат:
 
    ```output 
    -----BEGIN CERTIFICATE-----
        <Text for the device certificate includes public key>
    -----END CERTIFICATE-----
    -----BEGIN CERTIFICATE-----
        <Text for the intermediate certificate includes public key>
    -----END CERTIFICATE-----
    -----BEGIN CERTIFICATE-----
        <Text for the root certificate includes public key>
    -----END CERTIFICATE-----
    ```

5. Обратите внимание, что закрытый ключ для нового сертификата устройства записывается в каталог *./private/new-device.key.pem*. Текст для этого ключа потребуется устройству во время подготовки. Позже этот текст будет добавлен в пример настраиваемого модуля HSM.

    > [!WARNING]
    > Текст сертификатов содержит только сведения об открытом ключе. 
    >
    > Однако устройство также должно иметь доступ к закрытому ключу сертификата устройства. Это необходимо, так как устройство использует этот ключ для проверки во время выполнения при попытке подготовки. Конфиденциальность этого ключа — это одна из основных причин, по которым для защиты закрытых ключей рекомендуется использовать аппаратное хранилище в реальном модуле HSM.



## <a name="configure-the-custom-hsm-stub-code"></a>Настройка кода заглушки настраиваемого модуля HSM

Особенности взаимодействия с фактическим защищенным аппаратным хранилищем зависят от оборудования. В результате цепочка сертификатов, используемая устройством, описанным в этом учебнике, будет жестко кодироваться в коде заглушки настраиваемого модуля HSM. В реальной ситуации цепочка сертификатов будет храниться на фактическом оборудовании HSM для обеспечения большей безопасности конфиденциальной информации. В этом случае для чтения секретов из такого аппаратного хранилища будут реализованы методы, аналогичные методам заглушки, показанным в этом примере. 

Хотя аппаратное обеспечение HSM не требуется, мы не рекомендуем возвращать конфиденциальную информацию, например закрытый ключ сертификата, в исходный код. В такой ситуации ключ будет доступен для всех, кто может просматривать код. Мы применяем такой подход только в этой статье, чтобы помочь вам в обучении.

Чтобы обновить код заглушки настраиваемого модуля HSM для выполнения инструкций этого учебника, сделайте следующее.

1. Запустите Visual Studio и откройте новый файл решения, созданный в каталоге `cmake`, который вы создали в корне репозитория Git azure-iot-sdk-c. Файл решения называется `azure_iot_sdks.sln`.

2. В Обозревателе решений для Visual Studio последовательно выберите **Provisioning_Samples > custom_hsm_example > Исходные файлы** и откройте *custom_hsm_example.c*.

3. Замените строковое значение строковой константы `COMMON_NAME` общим именем, которое использовалось при создании сертификата устройства.

    ```c
    static const char* const COMMON_NAME = "custom-hsm-device-01";
    ```

4. В том же файле обновите строковое значение строковой константы `CERTIFICATE`, используя текст цепочки сертификатов, сохраненный в каталоге *./certs/new-device-full-chain.cert.pem* после создания сертификатов.

    > [!IMPORTANT]
    > При копировании текста в Visual Studio можно заметить, что текст анализируется и обновляется с добавлением пробелов в код и т. п. В этом случае необходимо удалить эти пробелы и запустить синтаксический анализ, нажав клавиши **CTRL+Z**.

    Обновите текст сертификата, чтобы он соответствовал приведенному ниже шаблону без лишних пробелов или выполнения синтаксического анализа Visual Studio.

    ```c
    // <Device/leaf cert>
    // <intermediates>
    // <root>
    static const char* const CERTIFICATE = "-----BEGIN CERTIFICATE-----\n"
    "MIIFOjCCAyKgAwIBAgIJAPzMa6s7mj7+MA0GCSqGSIb3DQEBCwUAMCoxKDAmBgNV"
        ...
    "MDMwWhcNMjAxMTIyMjEzMDMwWjAqMSgwJgYDVQQDDB9BenVyZSBJb1QgSHViIENB"
    "\n-----END CERTIFICATE-----\n"
    "-----BEGIN CERTIFICATE-----\n"
    "MIIFPDCCAySgAwIBAgIBATANBgkqhkiG9w0BAQsFADAqMSgwJgYDVQQDDB9BenVy"
        ...
    "MTEyMjIxMzAzM1owNDEyMDAGA1UEAwwpQXp1cmUgSW9UIEh1YiBJbnRlcm1lZGlh"
    "\n-----END CERTIFICATE-----\n"
    "-----BEGIN CERTIFICATE-----\n"
    "MIIFOjCCAyKgAwIBAgIJAPzMa6s7mj7+MA0GCSqGSIb3DQEBCwUAMCoxKDAmBgNV"
        ...
    "MDMwWhcNMjAxMTIyMjEzMDMwWjAqMSgwJgYDVQQDDB9BenVyZSBJb1QgSHViIENB"
    "\n-----END CERTIFICATE-----";        
    ```

5. В том же файле замените строковое значение строковой константы `PRIVATE_KEY` закрытым ключом для сертификата устройства.

    > [!IMPORTANT]
    > При копировании текста в Visual Studio можно заметить, что текст анализируется и обновляется с добавлением пробелов в код и т. п. В этом случае необходимо удалить эти пробелы и запустить синтаксический анализ, нажав клавиши **CTRL+Z**.

    Обновите текст закрытого ключа, чтобы он соответствовал приведенному ниже шаблону без лишних пробелов или выполнения синтаксического анализа Visual Studio.

    ```c
    static const char* const PRIVATE_KEY = "-----BEGIN RSA PRIVATE KEY-----\n"
    "MIIJJwIBAAKCAgEAtjvKQjIhp0EE1PoADL1rfF/W6v4vlAzOSifKSQsaPeebqg8U"
        ...
    "X7fi9OZ26QpnkS5QjjPTYI/wwn0J9YAwNfKSlNeXTJDfJ+KpjXBcvaLxeBQbQhij"
    "\n-----END RSA PRIVATE KEY-----";
    ```

6. Сохраните *custom_hsm_example.c*.


## <a name="verify-ownership-of-the-root-certificate"></a>Проверка принадлежности корневого сертификата

1. Следуя инструкциям из раздела [Регистрация открытой части сертификата X.509 и получение кода проверки](how-to-verify-certificates.md#register-the-public-part-of-an-x509-certificate-and-get-a-verification-code), отправьте корневой сертификат (`./certs/azure-iot-test-only.root.ca.cert.pem`) и получите код проверки от DPS.

2. Получив от DPS код проверки для корневого сертификата, выполните следующую команду из рабочего каталога скриптов сертификата, чтобы создать сертификат проверки.
 
    Указанный здесь код проверки приведен только для примера. Используйте код, созданный в DPS.    

    ```Bash
    ./certGen.sh create_verification_certificate 1B1F84DE79B9BD5F16D71E92709917C2A1CA19D5A156CB9F    
    ```    

    Этот скрипт создает сертификат, подписанный корневым сертификатом с кодом проверки в качестве имени субъекта. Этот сертификат позволяет DPS проверить наличие доступа к закрытому ключу корневого сертификата. Обратите внимание на расположение сертификата проверки в выходных данных скрипта. Этот сертификат создается в формате `.pfx`.

    ```output
    Leaf Device PFX Certificate Generated At:
    --------------------------------------------
        ./certs/verification-code.cert.pfx
    ```

3. Отправьте сертификат проверки согласно инструкциям раздела [Передача подписанного сертификата проверки](how-to-verify-certificates.md#upload-the-signed-verification-certificate) и нажмите кнопку **Проверить** в DPS, чтобы завершить проверку владения корневым сертификатом.


## <a name="update-the-certificate-store-on-windows-based-devices"></a>Обновление хранилища сертификатов на устройствах с Windows

На устройствах с ОС, отличной от Windows, цепочку сертификатов можно передать из кода в качестве хранилища сертификатов.

На устройствах с Windows необходимо добавить сертификаты подписи (корневой и промежуточный) в [хранилище сертификатов](https://docs.microsoft.com/windows/win32/secauthn/certificate-stores) Windows. В противном случае сертификаты для подписи не будут перенесены в службу DPS по безопасному каналу, использующему протокол TLS.

Чтобы добавить сертификаты для подписи в хранилище сертификатов на устройствах с Windows, выполните следующие действия.

1. В командной строке Bash в Git перейдите к подкаталогу `certs`, содержащему сертификаты для подписи, и преобразуйте их в `.pfx` следующим образом.

    Корневой сертификат:

    ```bash
    winpty openssl pkcs12 -inkey ../private/azure-iot-test-only.root.ca.key.pem -in ./azure-iot-test-only.root.ca.cert.pem -export -out ./root.pfx
    ```
    
    Промежуточный сертификат:   

    ```bash
    winpty openssl pkcs12 -inkey ../private/azure-iot-test-only.intermediate.key.pem -in ./azure-iot-test-only.intermediate.cert.pem -export -out ./intermediate.pfx
    ```

2. Щелкните правой кнопкой мыши кнопку **Пуск** в Windows. Затем щелкните левой кнопкой мыши пункт **Выполнить**. Введите *certmgr.mcs* и нажмите кнопку **ОК**, чтобы запустить оснастку MMC диспетчера сертификатов.

3. В диспетчере сертификатов в разделе **Сертификаты — текущий пользователь** щелкните **Доверенные корневые центры сертификации**. Затем в меню последовательно выберите пункты **Действие** > **Все задачи** > **Импорт**, чтобы импортировать `root.pfx`.

    * Выполните поиск **файла обмена личной информацией (PFX)** .
    * Используйте `1234` в качестве пароля.
    * Поместите сертификат в хранилище сертификатов **Доверенные корневые центры сертификации**.

4. В диспетчере сертификатов в разделе **Сертификаты — текущий пользователь** щелкните **Промежуточные центры сертификации**. Затем в меню последовательно выберите пункты **Действие** > **Все задачи** > **Импорт**, чтобы импортировать `intermediate.pfx`.

    * Выполните поиск **файла обмена личной информацией (PFX)** .
    * Используйте `1234` в качестве пароля.
    * Поместите сертификат в хранилище сертификатов **Промежуточные центры сертификации**.

Сертификаты для подписи теперь являются доверенными на устройстве с Windows, и в службу DPS может быть перенесена полная цепочка.



## <a name="create-an-enrollment-group"></a>Создание группы регистрации

1. Войдите на портал Azure, нажмите кнопку **Все ресурсы** в меню слева и откройте службу подготовки устройств.

2. Щелкните вкладку **Управление регистрациями**, а затем нажмите кнопку **Добавить группу регистрации** в верхней области.

3. В области **Добавление группы регистрации** введите приведенные ниже сведения, а затем нажмите кнопку **Сохранить**.

      ![Добавление группы регистрации для аттестации X.509 на портале](./media/tutorial-custom-hsm-enrollment-group-x509/custom-hsm-enrollment-group-x509.png#lightbox)

    | Поле        | Значение           |
    | :----------- | :-------------- |
    | **Имя группы** | Для прохождения этого учебника введите **custom-hsm-x509-devices**. |
    | **Тип аттестации** | Выберите **Сертификат**. |
    | **Устройство IoT Edge** | Выберите **Ложь**. |
    | **Тип сертификата** | Выберите **Промежуточный сертификат**. |
    | **PEM- или CER-файл первичного сертификата** | Перейдите к созданному ранее промежуточному сертификату ( *./certs/azure-iot-test-only.intermediate.cert.pem*). |


## <a name="configure-the-provisioning-device-code"></a>Настройка кода подготовки устройства

В этом разделе вы узнаете, как обновить пример кода для подготовки устройства с помощью экземпляра службы подготовки устройств. Если устройство прошло проверку подлинности, оно будет назначено центру Интернета вещей, связанному с экземпляром службы подготовки устройств.

1. На портале Azure выберите вкладку **Обзор** службы подготовки устройств и запишите значение **_области идентификатора_** .

    ![Извлеките сведения о конечной точке службы подготовки устройств из колонки на портале](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

2. В Обозревателе решений для Visual Studio последовательно выберите **Provisioning_Samples > prov_dev_client_sample > Исходные файлы** и откройте *prov_dev_client_sample.c*.

3. Найдите константу `id_scope` и замените ее значение ранее скопированным значением **области идентификатора**. 

    ```c
    static const char* id_scope = "0ne00000A0A";
    ```

4. Найдите определение функции `main()` в том же файле. Убедитесь, чтобы переменной `hsm_type` было присвоено значение `SECURE_DEVICE_TYPE_X509`, как показано ниже.

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    //hsm_type = SECURE_DEVICE_TYPE_TPM;
    hsm_type = SECURE_DEVICE_TYPE_X509;
    //hsm_type = SECURE_DEVICE_TYPE_SYMMETRIC_KEY;
    ```

5. Щелкните проект **prov\_dev\_client\_sample** правой кнопкой мыши и выберите пункт **Назначить запускаемым проектом**.

6. В меню Visual Studio выберите **Отладка** > **Запуск без отладки**, чтобы запустить решение. При появлении запроса на перестройку проекта нажмите **Да**, чтобы перестроить его перед запуском.

    Следующий результат является примером успешной загрузки примера клиента подготавливаемого устройства и подключения к экземпляру службы подготовки. Устройство назначено центру Интернета вещей и зарегистрировано.

    ```cmd
    Provisioning API Version: 1.3.9
    
    Registering Device
    
    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    
    Registration Information received from service: test-docs-hub.azure-devices.net, deviceId: custom-hsm-device-01
    Press enter key to exit:
    ```

7. На портале перейдите в центр Интернета вещей, связанный со службой подготовки, и щелкните вкладку **Устройства Интернета вещей**. Когда устройство X.509 будет подготовлено для центра, в колонке **Устройства Интернета вещей** появится его код со значением **Включено** в столбце *Состояние*. Возможно, вам потребуется нажать кнопку **Обновить** в верхней области. 

    ![Настраиваемое устройство HSM зарегистрировано в центре Интернета вещей.](./media/tutorial-custom-hsm-enrollment-group-x509/hub-provisioned-custom-hsm-x509-device.png) 

## <a name="clean-up-resources"></a>Очистка ресурсов

Завершив тестирование и изучение этого примера клиента устройства, выполните следующие действия, чтобы удалить все ресурсы, созданные при прохождении этого учебника.

1. Закройте окно выходных данных примера клиентского устройства на компьютере.
1. В меню слева на портале Azure щелкните **Все ресурсы** и откройте службу подготовки устройств. Откройте раздел **Управление регистрациями** для службы, а затем щелкните вкладку **Группы регистрации**. Установите флажок рядом с *именем группы* устройств, созданной при прохождении этого учебника, и нажмите кнопку **Удалить** в верхней части области. 
1. Щелкните **Сертификаты** в DPS. Щелкните каждый сертификат, который вы загрузили и проверили при прохождении этого учебника, и нажмите кнопку **Удалить** для его удаления.
1. В меню слева на портале Azure щелкните **Все ресурсы** и выберите свой центр Интернета вещей. Откройте область **Устройства Интернета вещей** для центра. Установите флажок рядом с *кодом устройства*, зарегистрированного при прохождении этого учебника. Нажмите кнопку **Удалить** в верхней части области.

## <a name="next-steps"></a>Дальнейшие действия

Из этого учебника вы узнали, как подготовить устройство X.509, используя настраиваемый модуль HSM для центра Интернета вещей. Чтобы узнать, как подготавливать устройства Интернета вещей для нескольких центров, перейдите к следующему учебнику. 

> [!div class="nextstepaction"]
> [Учебник. Подготовка устройств в центрах Интернета вещей с балансировкой нагрузки](tutorial-provision-multiple-hubs.md)
