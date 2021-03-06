---
title: Настройка Персонализатора
description: Конфигурация сервиса включает в себя то, как сервис рассматривает результаты, как часто он исследует, как часто переобучается модель и сколько данных хранится.
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: how-to
ms.date: 04/29/2020
ms.openlocfilehash: 9b7347cb98bcbf2e1d92f115d404197083acef4d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91292615"
---
# <a name="configure-personalizer-learning-loop"></a>Настройка цикла обучения персонализации

Конфигурация сервиса включает в себя то, как сервис рассматривает результаты, как часто он исследует, как часто переобучается модель и сколько данных хранится.

Настройте цикл обучения на странице **Конфигурация** в портал Azure для этого ресурса персонализации.

<a name="configure-service-settings-in-the-azure-portal"></a>
<a name="configure-reward-settings-for-the-feedback-loop-based-on-use-case"></a>

## <a name="planning-configuration-changes"></a>Планирование изменений конфигурации

Так как некоторые изменения конфигурации [сбрасывают модель](#settings-that-include-resetting-the-model), следует спланировать изменения конфигурации.

Если вы планируете использовать [режим области](concept-apprentice-mode.md), обязательно проверьте конфигурацию персонализации перед переключением в режим области.

<a name="clear-data-for-your-learning-loop"></a>

## <a name="settings-that-include-resetting-the-model"></a>Параметры, включающие сброс модели

Следующие действия активируют повторное обучение модели с использованием данных, доступных за последние 2 дня.

* Выигрыш
* Исследование

Чтобы [очистить](how-to-manage-model.md) все данные, используйте страницу **Параметры модели и обучения** .

## <a name="configure-rewards-for-the-feedback-loop"></a>Настройка вознаграждения для цикла обратной связи

Настройте службу для использования вознаграждения в цикле обучения. Изменение следующих значений приведет к сбросу текущей модели персонализации и повторному обучению данных за последние два дня.

> [!div class="mx-imgBorder"]
> ![Настройка значений наград для цикла обратной связи](media/settings/configure-model-reward-settings.png)

|Значение|Назначение|
|--|--|
|Время ожидания результата|Задает продолжительность времени, во время которого Персонализатор будет собирать результаты значений для вызова ранжирования, начиная с момента, когда вызов ранжирования происходит. Это значение задается с помощью запроса: "сколько времени должно дожидаться персонализации для вызовов вознаграждения?" Любой результат, полученный после этого окна, будет зарегистрирован, но не будет использоваться для обучения.|
|Результат по умолчанию|Если в течение времени ожидания результата, связанного с вызовом ранжирования, Персонализатор не получит результативный вызов, Персонализатор назначит результат по умолчанию. По умолчанию, и в большинстве случаев вознаграждение по умолчанию равно нулю (0).|
|Агрегирование результата|Если за один и тот же вызов API ранжирования получено несколько результатов, используется следующий метод агрегации: **сумма** или **самое раннее**. Самое раннее выбирает самую раннюю полученную оценку и отклоняет остальные. Это полезно, если необходимо получить уникальное вознаграждение между возможными повторяющимися вызовами. |

После изменения этих значений обязательно выберите **сохранить**.

## <a name="configure-exploration-to-allow-the-learning-loop-to-adapt"></a>Настройка исследования для адаптации цикла обучения

Персонализация позволяет обнаруживать новые закономерности и адаптировать их к изменениям поведения пользователей с течением времени, изменяя альтернативы вместо использования прогноза обученной модели. Значение **для просмотра определяет, какой** процент вызовов ранжирования отвечает на исследование.

Изменение этого значения приведет к сбросу текущей модели персонализации и повторному обучению данных за последние два дня.

![Значение для просмотра определяет, какой процент вызовов ранжирования отвечает на исследование](media/settings/configure-exploration-setting.png)

После изменения этого значения обязательно выберите **сохранить**.

<a name="model-update-frequency"></a>

## <a name="configure-model-update-frequency-for-model-training"></a>Настройка частоты обновления модели для обучения модели

**Частота обновления модели** определяет, как часто обучена модель.

|Параметр частоты|Назначение|
|--|--|
|1 минута|Частота обновления в одну минуту полезна при **отладке** кода приложения с помощью персонализации, выполнения демонстраций или интерактивного тестирования аспектов машинного обучения.|
|15 минут|Высокая частота обновления модели полезна в ситуациях, когда необходимо **тщательно отслеживать изменения** в поведении пользователей. Это могут быть сайты с новостями, вирусным содержимым или торговлей в режиме реального времени. В таких сценариях модель можно обновлять каждые 15 минут. |
|1 час|В большинстве других случаев целесообразно задать меньшую частоту обновления.|

![Параметр "Частота обновления модели" задает частоту повторного обучения новой модели Персонализатора.](media/settings/configure-model-update-frequency-settings-15-minutes.png)

После изменения этого значения обязательно выберите **сохранить**.

## <a name="data-retention"></a>Хранение данных

**Период хранения данных** устанавливает, сколько дней Персонализатор хранит журналы данных. Журналы прошлых данных необходимы для выполнения [автономных вычислений](concepts-offline-evaluation.md), которые используются для измерения эффективности Персонализатора и оптимизации политики обучения.

После изменения этого значения обязательно выберите **сохранить**.



## <a name="next-steps"></a>Дальнейшие шаги

[Узнайте, как управлять моделью](how-to-manage-model.md)
