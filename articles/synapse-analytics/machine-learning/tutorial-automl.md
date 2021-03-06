---
title: Руководство. Обучение моделей машинного обучения с помощью AutoML
description: Руководство по обучению модели машинного обучения в Azure Synapse с помощью AutoML.
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 11/20/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: 8dd99b60a548e3c392bbe468ddde484081e6eb8b
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/01/2020
ms.locfileid: "96464491"
---
# <a name="tutorial-code-free-machine-learning-model-training-in-azure-synapse-with-automl-preview"></a>Руководство. Обучение модели машинного обучения в Azure Synapse без написания кода с помощью AutoML (предварительная версия)

Узнайте, как можно легко дополнить данные в таблицах Spark с помощью новых моделей машинного обучения, обученных с использованием [AutoML в Машинном обучении Azure](https://docs.microsoft.com/azure/machine-learning/concept-automated-ml).  Пользователь в Synapse может выбрать таблицу Spark в рабочей области Azure Synapse, чтобы применить ее в качестве обучающего набора данных для создания моделей машинного обучения без необходимости писать код.

Из этого руководства вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> - Обучение моделей машинного обучения в Azure Synapse Studio без написания кода, используя AutoML в Машинном обучении Azure. Тип модели, которую вам предстоит обучить, зависит от решаемой проблемы.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись, прежде чем начинать работу](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Предварительные требования

- [Рабочая область Synapse Analytics](../get-started-create-workspace.md) с учетной записью хранения ADLS 2-го поколения, настроенной в качестве хранилища по умолчанию. Необходимо быть **участником для данных BLOB-объектов хранилища** файловой системы ADLS 2-го поколения, с которой вы работаете.
- Пул Spark в рабочей области Azure Synapse Analytics. Дополнительные сведения см. в статье [Создание пула Spark в Azure Synapse](../quickstart-create-sql-pool-studio.md).
- Связанная служба Машинного обучения Azure в рабочей области Azure Synapse Analytics. Дополнительные сведения см. в разделе [Создание связанной службы Машинного обучения Azure в Azure Synapse](quickstart-integrate-azure-machine-learning.md).

## <a name="sign-in-to-the-azure-portal"></a>Вход на портал Azure

Войдите на [портал Azure](https://portal.azure.com/)

## <a name="create-a-spark-table-for-training-dataset"></a>Создание таблицы Spark для обучающего набора данных

Для работы с этим руководством вам потребуется таблица Spark. Нужную таблицу Spark можно создать с помощью следующей записной книжки.

1. Скачайте записную книжку [Create-Spark-Table-NYCTaxi-Data.ipynb](https://go.microsoft.com/fwlink/?linkid=2149229)

1. Импортируйте эту записную книжку в Azure Synapse Studio.
![Импорт записной книжки](media/tutorial-automl-wizard/tutorial-automl-wizard-00a.png)

1. Выберите нужный пул Spark и щелкните `Run all`. Запустите эту записную книжку, чтобы получить данные о такси Нью Йорка из открытого набора данных и сохранить их в базе данных Spark по умолчанию.
![Выполнить все](media/tutorial-automl-wizard/tutorial-automl-wizard-00b.png)

1. Когда выполнение этой записной книжки завершится, в базе данных Spark по умолчанию появится новая таблица Spark. Перейдите в центр данных и найдите таблицу с именем `nyc_taxi`.
![Таблица Spark](media/tutorial-automl-wizard/tutorial-automl-wizard-00c.png)

## <a name="launch-automl-wizard-to-train-a-model"></a>Запуск мастера AutoML для обучения модели

Щелкните правой кнопкой мыши таблицу Spark, созданную на предыдущем шаге. Чтобы открыть мастер, выберите Machine Learning (Машинное обучение) -> Enrich with existing model (Дополнить с использованием существующей модели).
![Запуск мастера AutoML](media/tutorial-automl-wizard/tutorial-automl-wizard-00d.png)

Откроется панель конфигурации, и вам будет предложено указать сведения о конфигурации для создания выполнения эксперимента AutoML в Машинном обучении Azure. Это выполнение обучит несколько моделей, из которых лучшая по результатам успешного выполнения будет зарегистрирована в реестре моделей Машинного обучения Azure:

![Настройка выполнения — шаг 1](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00a.png)

- **Рабочая область Машинного обучения Azure.** Рабочая область Машинного обучения Azure является обязательной для создания выполнения эксперимента AutoML. Также вам нужно связать рабочую область Azure Synapse с рабочей областью Машинного обучения Azure, используя [связанную службу](quickstart-integrate-azure-machine-learning.md). Когда все предварительные условия будут соблюдены, вы сможете указать рабочую область Машинного обучения Azure для этого выполнения AutoML.

- **Имя эксперимента.** Укажите имя эксперимента. При отправке выполнения AutoML нужно указать имя эксперимента. Сведения о выполнении хранятся в указанном эксперименте в рабочей области Машинного обучения Azure. Этап процедура создаст новый эксперимент по умолчанию и предложит для него имя, но вы можете также указать имя существующего эксперимента.

- **Наилучшая модель.** Укажите имя наилучшей модели из выполнения AutoML. Наилучшая модель будет сохранена с указанным именем в реестре моделей Машинного обучения Azure по завершении выполнения. Выполнение AutoML создаст разные модели машинного обучения. Эти модели будут сравниваться друг с другом по основной метрике, которую вы выберете на более позднем шаге, и из них будет выбрана наилучшая модель.

- **Целевой столбец.** Это тот элемент, который модель будет учиться прогнозировать. Выберите столбец, для которого вы хотите создать прогноз.

- **Пул Spark.** Это пул Spark для выполнения эксперимента AutoML. Все вычисления будут выполняться в указанном пуле.

- **Сведения о конфигурации Spark.** Помимо пула Spark, вы можете указать необязательные сведения о конфигурации сеанса.

В этом руководстве в качестве целевого используется числовой столбец `fareAmount`.

Нажмите "Продолжить".

## <a name="choose-task-type"></a>Выбор типа задачи

Выберите тип модели машинного обучения для эксперимента в зависимости от того, на какой вопрос вы пытаетесь ответить. Так как мы указали `fareAmount` в качестве целевого столбца с числовыми значениями, мы выберем вариант *Регрессия*.

Щелкните "Продолжить", чтобы настроить дополнительные параметры.

![Выбор типа задачи](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00b.png)

## <a name="additional-configurations"></a>Дополнительные конфигурации

Если выбран тип *Классификация* или *Регрессия*, будут доступны следующие дополнительные параметры:

- **Основная метрика.** Это метрика, используемая для оценки работы модели. Она будет использоваться для сравнения нескольких моделей, созданных в ходе выполнения AutoML, и выбора наиболее точной из них.

- **Training job time (hours)** (Время задания обучения (в часах)). Максимальное время (в часах) для выполнения эксперимента и обучения моделей. Обратите внимание, что здесь можно указать значения меньше 1. Например, `0.5`.

- **Max concurrent iterations** (Максимальное число одновременных итераций). Это максимальное количество итераций, которые могут выполняться параллельно.

- **Совместимость с моделью ONNX.** Если включен этот параметр, все обученные в AutoML модели будут преобразованы в формат ONNX. Это особенно важно, если вы хотите использовать модель для оценки в пулах Azure Synapse SQL.

Эти параметры имеют значения по умолчанию, которые можно настраивать.
![Дополнительные настройки](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00c.png)

> Обратите внимание, что при выборе варианта "Прогнозирование временных рядов" требуются дополнительные настройки. Также режим прогнозирования не поддерживает совместимость с моделью ONNX.

После завершения настройки можно начать выполнение AutoML.

Начать выполнение AutoML в Azure Synapse можно двумя способами. Чтобы обойтись без кода, можно выбрать **Создать выполнение**. Если вам удобнее работать с кодом, выберите вариант **Открыть в записной книжке**, где вы увидите код, который создает выполнение, и выполните эту записную книжку.

### <a name="create-run-directly"></a>Непосредственное создание запуска

Нажмите кнопку "Начать выполнение", чтобы выполнить AutoML непосредственно. Появится уведомление о начале выполнения AutoML.

Когда выполнение AutoML успешно завершится, вы увидите еще одно уведомление. Также можно нажать кнопку уведомления, чтобы проверить состояние отправки выполнения.
Откройте Машинное обучение Azure, щелкнув ссылку в уведомлении об успешном выполнении.
![Уведомление об успешном выполнении](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00d.png)

### <a name="create-run-with-notebook"></a>Создание выполнения с помощью записной книжки

Выберите *Открыть в записной книжке*, чтобы создать записную книжку. Щелкните *Выполнить все*, чтобы выполнить эту записную книжку.
Этот вариант позволяет добавить дополнительные параметры для выполнения AutoML.

![Открытие записной книжки](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00e.png)

Когда выполнение будет успешно отправлено из записной книжки, в выходных данных записной книжки появится ссылка на выполнение эксперимента в рабочей области Машинного обучения Azure. Вы можете щелкнуть эту ссылку, чтобы отслеживать выполнение AutoML в Машинном обучении Azure.
![Записная книжка — "Выполнить все"](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00f.png))

## <a name="next-steps"></a>Дальнейшие действия

- [Руководство. Оценка моделей машинного обучения в выделенных пулах Azure Synapse SQL](tutorial-sql-pool-model-scoring-wizard.md).
- [Краткое руководство. Создание новой связанной службы Машинного обучения Azure в Azure Synapse](quickstart-integrate-azure-machine-learning.md)
- [Возможности машинного обучения в Azure Synapse Analytics](what-is-machine-learning.md)