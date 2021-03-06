---
title: Создание приложения регистрации для Android с реагированием
titleSuffix: Azure Cognitive Services
description: Узнайте, как настроить среду разработки и развернуть приложение регистрации лиц для получения согласия от клиентов.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 11/17/2020
ms.author: pafarley
ms.openlocfilehash: 085dd18214f795566669fb862bba63b67eb9115d
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/30/2020
ms.locfileid: "96350370"
---
# <a name="build-an-enrollment-app-for-android-with-react"></a>Создание приложения регистрации для Android с реагированием

В этом руководство показано, как приступить к работе с примером приложения регистрации лиц. В приложении демонстрируются рекомендации по получению осмысленного согласия для регистрации пользователей в службе распознавания лиц и получения высокоточной информации о лицах. Интегрированная система может использовать приложение регистрации, например, для обеспечения управления доступом без вмешательства, проверки личности, отслеживания присутствия, киоска персонализации или проверки личности на основе данных о лицах.

При запуске приложение отображает подробный экран с согласия пользователя. Если пользователь предоставил согласие, приложение запрашивает имя пользователя и пароль, а затем записывает высококачественный образ лицевой стороны с помощью камеры устройства.

Пример приложения регистрации написан с помощью JavaScript и реагирует на собственную инфраструктуру. В настоящее время его можно развернуть на устройствах Android. в будущем появятся дополнительные варианты развертывания.

## <a name="prerequisites"></a>Предварительные требования 

* Подписка Azure — [создайте ее бесплатно](https://azure.microsoft.com/free/cognitive-services/).  
* Создав подписку Azure, [Создайте ресурс лица](https://portal.azure.com/#create/Microsoft.CognitiveServicesFace) в портал Azure, чтобы получить ключ и конечную точку. После развертывания ресурса выберите элемент **Перейти к ресурсу**.  
  * Вам потребуется ключ и конечная точка из созданного ресурса, чтобы подключить приложение к API распознавания лиц.  
  * Для локальной разработки и тестирования можно вставить ключ API и конечную точку в файл конфигурации. Для окончательного развертывания Храните ключ API в безопасном месте и никогда в коде.  

> [!IMPORTANT]
> Эти ключи подписки используются для доступа к API Cognitive Service. Не предоставляйте доступ к ключам другим пользователям. Храните их безопасно, например, с помощью Azure Key Vault. Также рекомендуется регулярно повторно создавать эти ключи. Для вызова API необходим только один ключ. При повторном создании первого ключа второй ключ можно использовать для бесперебойного доступа к службе.

## <a name="set-up-the-development-environment"></a>Настройка среды разработки

1. Клонировать репозиторий Git для [примера приложения регистрации](https://github.com/azure-samples/cognitive-services-FaceAPIEnrollmentSample).
1. Чтобы настроить среду разработки, следуйте собственной документации реагирования на собственную документацию <a href="https://reactnative.dev/docs/environment-setup"  title=" "  target="_blank"> <span class="docon docon-navigate-external x-hidden-focus"></span> </a> . В качестве операционной системы разработки выберите пункт **отреагировать на собственное краткое руководство CLI** и выберите **Android** в качестве целевой ОС. Выполните действия в разделах **Установка зависимостей** и **среды разработки Android**.
1. Откройте файл env.jsв предпочтительном текстовом редакторе, например [Visual Studio Code](https://code.visualstudio.com/), и добавьте конечную точку и ключ. Вы можете получить конечную точку и ключ в портал Azure на вкладке **Обзор** ресурса. Этот шаг предназначен только для локальных целей тестирования &mdash; . не возвращать ключ API распознавания лиц в удаленный репозиторий.
1. Запустите приложение с помощью эмулятора виртуального устройства Android из Android Studio или собственного устройства Android. Чтобы протестировать приложение на физическом устройстве, следуйте соответствующей документации по <a href="https://reactnative.dev/docs/running-on-device"  title=" реагированию на собственную "  target="_blank"> документацию <span class="docon docon-navigate-external x-hidden-focus"></span> </a> .  


## <a name="create-an-enrollment-experience"></a>Создание процесса регистрации  

Теперь, когда вы настроили пример приложения регистрации, его можно адаптировать под свои нужды.

Например, может потребоваться добавить сведения о конкретной ситуации на странице согласия:

> [!div class="mx-imgBorder"]
> ![страница согласия приложения](./media/enrollment-app/1-consent-1.jpg)

Служба обеспечивает проверку качества изображения, позволяя выбрать, имеет ли изображение достаточное качество для регистрации клиента или попробовать распознавание лиц. В этом приложении показано, как получить доступ к кадрам с камеры устройства, выбрать кадры самого высокого качества и зарегистрировать обнаруженную грань в службе API распознавания лиц. 

Многие проблемы распознавания лиц вызваны некачественными эталонными образами. Ниже перечислены некоторые факторы, которые могут привести к снижению производительности модели.
* Размер лица (лиц, удаленных от камеры)
* Ориентация лица (фрагменты, которые отсвечены или наклонены от камеры)
* Условия низкого освещения (низкая освещенность или освещение), где изображение может быть плохо раскрыто или иметь слишком много шума
* Перекрытия (частично скрытые или неограниченные лица), включая такие принадлежности, как шляпы или плотные обрамления очков)
* Размытие (например, при быстром перемещении от лица к фотографиям). 

> [!div class="mx-imgBorder"]
> ![страница инструкции по записи образа приложения](./media/enrollment-app/4-instruction.jpg)

Обратите внимание, что приложение также предлагает функции для удаления регистрации пользователя и повторной регистрации.

> [!div class="mx-imgBorder"]
> ![Страница управления профилями](./media/enrollment-app/10-manage-2.jpg)

Чтобы расширить функциональные возможности приложения, чтобы охватить всю процедуру регистрации, ознакомьтесь с [обзором](enrollment-overview.md) дополнительных функций для реализации и рекомендаций.

## <a name="deploy-the-enrollment-app"></a>Развертывание приложения регистрации

### <a name="android"></a>Android

Во-первых, убедитесь, что приложение готово к развертыванию в рабочей среде. Удалите все ключи или секреты из кода приложения и убедитесь, что выполнены рекомендации по [обеспечению безопасности](../cognitive-services-security.md?tabs=command-line%2ccsharp).

Когда вы будете готовы выпустить приложение для рабочей среды, вы создадите файл APK, готовый к выпуску, который является форматом файла пакета для приложений Android. Этот файл APK должен быть подписан закрытым ключом. С помощью этой сборки выпуска можно приступить к распространению приложения на устройства напрямую. 

<a href="https://developer.android.com/studio/publish/preparing#publishing-build"  title=" "  target="_blank"> <span class="docon docon-navigate-external x-hidden-focus"></span> </a> Чтобы узнать, как создать закрытый ключ, подписать приложение и создать apk выпуска, следуйте инструкциям в документации подготовка к выпуску подготовка к выпуску.  

После создания подписанного APK см. статью публикация приложения <a href="https://developer.android.com/studio/publish"  title=" Публикация приложения, "  target="_blank"> <span class="docon docon-navigate-external x-hidden-focus"></span> </a> чтобы узнать больше о том, как выпустить приложение.

## <a name="next-steps"></a>Дальнейшие действия  

В этом руководство вы узнали, как настроить среду разработки и приступить к работе с примером приложения регистрации. Если вы не знакомы с машинным кодом, вы можете ознакомиться с их [документацией по началу работы](https://reactnative.dev/docs/getting-started) , чтобы получить дополнительные сведения. Также может быть полезно ознакомиться с [API распознавания лиц](Overview.md). Ознакомьтесь с другими разделами документации по приложению регистрации, прежде чем приступить к разработке.