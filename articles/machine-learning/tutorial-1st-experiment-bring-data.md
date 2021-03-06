---
title: Руководство по использованию собственных данных
titleSuffix: Azure Machine Learning
description: В четвертой части серии о начале работы с Машинным обучением Azure показано, как использовать собственные данные при запуске удаленного обучения.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: aminsaied
ms.author: amsaied
ms.reviewer: sgilley
ms.date: 09/15/2020
ms.custom: tracking-python
ms.openlocfilehash: 52b46d67d745017237a8c648abed66e2693d9d6a
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/03/2020
ms.locfileid: "96573023"
---
# <a name="tutorial-use-your-own-data-part-4-of-4"></a>Руководство по использованию собственных данных (часть 4 из 4)

В этом учебнике показано, как отправлять и использовать собственные данные для обучения моделей Машинного обучения Azure.

Этот учебник является *четвертой частью серии из четырех учебников*, в рамках которой вы ознакомитесь с основами Машинного обучения Azure и выполните задачи машинного обучения на основе заданий в Azure. Этот учебник создан на основе работы, выполненной в рамках статьи [Часть 1. настройка](tutorial-1st-experiment-sdk-setup-local.md), [Части 2: запуск скрипта "Hello World!"](tutorial-1st-experiment-hello-world.md) и [Часть 3: обучение моделей](tutorial-1st-experiment-sdk-train.md).

В [Части 3: обучение моделей](tutorial-1st-experiment-sdk-train.md) данные были скачаны с помощью встроенного метода `torchvision.datasets.CIFAR10` в API PyTorch. Однако во многих случаях при запуске удаленного обучения необходимо использовать собственные данные. В этой статье показан рабочий процесс, который можно использовать для работы с собственными данными в Машинном обучении Azure.

Изучив это руководство, вы:

> [!div class="checklist"]
> * настроите учебный скрипт для использования данных в локальном каталоге;
> * протестируете учебный скрипт локально;
> * отправите данные в Azure.
> * Создание скрипта элемента управления
> * изучите новые концепции Машинного обучения Azure (передача параметров, наборов данных, хранилищ данных);
> * отправите и запустите свой сценарий обучения;
> * просмотрите выходные данные кода в облаке;

## <a name="prerequisites"></a>Prerequisites

* Завершение [Части 3](tutorial-1st-experiment-sdk-train.md) в серии учебников.
* Вводные знания о языке Python и рабочих процессах машинного обучения.
* Локальная среда разработки, например Visual Studio Code, Jupyter или PyCharm.
* Python (версия от 3.5 до 3.7).

## <a name="adjust-the-training-script"></a>Настройка скрипта обучения

Теперь учебный скрипт (tutorial/src/train.py) выполняется в Машинном обучении Azure, и вы можете отслеживать производительность модели. Теперь можно параметризировать учебный скрипт, введя аргументы. Использование аргументов позволит легко сравнивать разные гиперпараметры.

В настоящее время наш учебный скрипт настроен на загрузку набора данных CIFAR10 при каждом запуске. Приведенный ниже код Python был скорректирован для считывания данных из каталога.

>[!NOTE] 
> Использование `argparse` параметризует скрипт.

:::code language="python" source="~/MachineLearningNotebooks/tutorials/get-started-day1/code/pytorch-cifar10-your-data/train.py":::

### <a name="understanding-the-code-changes"></a>Основные сведения об изменениях кода

Код в `train.py` использовал библиотеку `argparse` для настройки `data_path`, `learning_rate` и `momentum`.

```python
# .... other code
parser = argparse.ArgumentParser()
parser.add_argument('--data_path', type=str, help='Path to the training data')
parser.add_argument('--learning_rate', type=float, default=0.001, help='Learning rate for SGD')
parser.add_argument('--momentum', type=float, default=0.9, help='Momentum for SGD')
args = parser.parse_args()
# ... other code
```

Кроме того, скрипт `train.py` был адаптирован, чтобы обновить оптимизатор для использования определяемых пользователем параметров:

```python
optimizer = optim.SGD(
    net.parameters(),
    lr=args.learning_rate,     # get learning rate from command-line argument
    momentum=args.momentum,    # get momentum from command-line argument
)
```
> [!div class="nextstepaction"]
> [Мной был изменен скрип обучения](?success=adjust-training-script#test-locally) [Возникла проблема](https://www.research.net/r/7C6W7BQ?issue=adjust-training-script)

## <a name="test-the-script-locally"></a><a name="test-locally"></a> Тестирование сценария локально

Теперь ваш скрипт принимает _путь к данным_ в качестве аргумента. Для начала протестируйте его локально. Добавьте в структуру каталогов учебника папку с именем `data`. Структура каталогов должна выглядеть следующим образом:

```txt
tutorial
└──.azureml
|  └──config.json
|  └──pytorch-env.yml
└──data
└──src
|  └──hello.py
|  └──model.py
|  └──train.py
└──01-create-workspace.py
└──02-create-compute.py
└──03-run-hello.py
└──04-run-pytorch.py
```

Если вы не выполнили `train.py` локально в предыдущем учебнике, у вас не будет каталога `data/`. В этом случае запустите метод `torchvision.datasets.CIFAR10` локально, используя `download=True` в скрипте `train.py`.

Чтобы запустить измененный скрипт обучения локально, вызовите:

```bash
python src/train.py --data_path ./data --learning_rate 0.003 --momentum 0.92
```

Таким образом вам не нужно будет скачивать набор данных CIFAR10 при передаче локального пути к данным. Кроме того, вы можете поэкспериментировать с разными значениями гиперпараметров _скорости обучения_ и _импульса_ без необходимости жестко задавать их в скрипте обучения.

> [!div class="nextstepaction"]
> [Сценарий протестирован мной локально](?success=test-locally#upload) [Возникла проблема](https://www.research.net/r/7C6W7BQ?issue=test-locally)

## <a name="upload-the-data-to-azure"></a><a name="upload"></a> Отправка данных в Azure

Чтобы запустить этот скрипт в Машинном обучении Azure, необходимо сделать учебные данные доступными в Azure. Ваша рабочая область Машинного обучения Azure _по умолчанию_ оснащена хранилищем данных. Это учетная запись хранилища BLOB-объектов Azure, в которой можно хранить учебные данные.

>[!NOTE] 
> Машинное обучение Azure позволяет подключать другие облачные хранилища данных, в которых хранятся ваши данные. Дополнительные сведения см. в [документации по хранилищу данных](./concept-data.md).  

Создайте новый скрипт элемента управления Python с именем `05-upload-data.py` в каталоге `tutorial`:

:::code language="python" source="~/MachineLearningNotebooks/tutorials/get-started-day1/IDE-users/05-upload-data.py":::

Значение `target_path` указывает путь хранилища данных, куда будут переданы данные CIFAR10.

>[!TIP] 
> При использовании Машинного обучения Azure для передачи данных можно использовать [Обозреватель службы хранилища Azure](https://azure.microsoft.com/features/storage-explorer/) для отправки ad-hoc-файлов. Если вам требуется средство извлечения, преобразования и загрузки, для приема данных в Azure можно использовать [Фабрику данных Azure](../data-factory/introduction.md).

Запустите файл Python, чтобы передать данные. (отправка должна выполняться быстро — меньше чем за 60 секунд.)

```bash
python 05-upload-data.py
```

Вы должны увидеть следующие стандартные выходные данные:

```txt
Uploading ./data\cifar-10-batches-py\data_batch_2
Uploaded ./data\cifar-10-batches-py\data_batch_2, 4 files out of an estimated total of 9
.
.
Uploading ./data\cifar-10-batches-py\data_batch_5
Uploaded ./data\cifar-10-batches-py\data_batch_5, 9 files out of an estimated total of 9
Uploaded 9 files
```

> [!div class="nextstepaction"]
> [Данные отправлены мной](?success=upload-data#control-script) [Возникла проблема](https://www.research.net/r/7C6W7BQ?issue=upload-data)

## <a name="create-a-control-script"></a><a name="control-script"></a> Создание скрипта элемента управления

Как и ранее, создайте новый скрипт элемента управления Python с именем `06-run-pytorch-data.py`:

:::code language="python" source="~/MachineLearningNotebooks/tutorials/get-started-day1/IDE-users/06-run-pytorch-data.py":::

### <a name="understand-the-code-changes"></a>Изучение изменений кода

Скрипт элемента управления аналогичен скрипту из [части 3 этой серии](tutorial-1st-experiment-sdk-train.md) и содержит следующие новые строки:

:::row:::
   :::column span="":::
      `dataset = Dataset.File.from_files( ... )`
   :::column-end:::
   :::column span="2":::
      Параметр [dataset](/python/api/azureml-core/azureml.core.dataset.dataset?preserve-view=true&view=azure-ml-py) используется для ссылки на данные, отправленные в хранилище BLOB-объектов Azure. Наборы данных — это слой абстрагирования над вашими данными, предназначенный для повышения надежности.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `config = ScriptRunConfig(...)`
   :::column-end:::
   :::column span="2":::
      [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig?preserve-view=true&view=azure-ml-py) изменен для включения списка аргументов, которые будут переданы в `train.py`. Аргумент `dataset.as_named_input('input').as_mount()` означает, что указанный каталог будет _подключен_ к целевому объекту вычислений.
   :::column-end:::
:::row-end:::

> [!div class="nextstepaction"]
> [Мной создан скрипт управления](?success=control-script#submit-to-cloud) [Возникла проблема](https://www.research.net/r/7C6W7BQ?issue=control-script)

## <a name="submit-the-run-to-azure-machine-learning"></a><a name="submit-to-cloud"></a> Отправка выполнения в Машинное обучение Azure

Теперь повторно отправьте запуск для использования новой конфигурации:

```bash
python 06-run-pytorch-data.py
```

Этот код в эксперименте Студии машинного обучения Azure распечатает URL-адрес. Если вы перейдете по этой ссылке, вы увидите, что ваш код будет работать.

> [!div class="nextstepaction"]
> [Выполнение отправлено мною повторно](?success=submit-to-cloud#inspect-log) [Возникла проблема](https://www.research.net/r/7C6W7BQ?issue=submit-to-cloud)

### <a name="inspect-the-log-file"></a><a name="inspect-log"></a> Проверка файла журнала

В Студии перейдите к экспериментальному запуску (выбрав предыдущие выходные данные URL-адреса) и выберите **Выходные данные и журналы**. Выберите файл `70_driver_log.txt`. Вы должны увидеть следующий результат.

```txt
Processing 'input'.
Processing dataset FileDataset
{
  "source": [
    "('workspaceblobstore', 'datasets/cifar10')"
  ],
  "definition": [
    "GetDatastoreFiles"
  ],
  "registration": {
    "id": "XXXXX",
    "name": null,
    "version": null,
    "workspace": "Workspace.create(name='XXXX', subscription_id='XXXX', resource_group='X')"
  }
}
Mounting input to /tmp/tmp9kituvp3.
Mounted input to /tmp/tmp9kituvp3 as folder.
Exit __enter__ of DatasetContextManager
Entering Run History Context Manager.
Current directory:  /mnt/batch/tasks/shared/LS_root/jobs/dsvm-aml/azureml/tutorial-session-3_1600171983_763c5381/mounts/workspaceblobstore/azureml/tutorial-session-3_1600171983_763c5381
Preparing to call script [ train.py ] with arguments: ['--data_path', '$input', '--learning_rate', '0.003', '--momentum', '0.92']
After variable expansion, calling script [ train.py ] with arguments: ['--data_path', '/tmp/tmp9kituvp3', '--learning_rate', '0.003', '--momentum', '0.92']

Script type = None
===== DATA =====
DATA PATH: /tmp/tmp9kituvp3
LIST FILES IN DATA PATH...
['cifar-10-batches-py', 'cifar-10-python.tar.gz']
```

Примечание:

- машинное обучение Azure автоматически подключило хранилище BLOB-объектов к вычислительному кластеру.
- ``dataset.as_named_input('input').as_mount()`` скрипта элемента управления выполняет разрешение для точки подключения.

> [!div class="nextstepaction"]
> [Мной изучен файл журнала](?success=inspect-log#clean-up-resources) [Возникла проблема](https://www.research.net/r/7C6W7BQ?issue=inspect-log)

## <a name="clean-up-resources"></a>Очистка ресурсов

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

Вы также можете сохранить группу ресурсов, но удалить одну рабочую область. Отобразите свойства рабочей области и нажмите кнопку **Удалить**.

## <a name="next-steps"></a>Дальнейшие действия

В этом учебнике мы рассмотрели, как отправлять данные в Azure с помощью `Datastore`. Хранилище данных служило облачным хранилищем для вашей рабочей области, предоставляя вам устойчивую и гибкую возможность для хранения данных.

Вы узнали, как изменить скрипт обучения для принятия пути к данным через командную строку. С помощью `Dataset` вы подключили каталог к удаленному запуску. 

Теперь, когда у вас есть модель, изучите следующее:

* [Развертывание моделей с помощью Машинного обучения Azure](how-to-deploy-and-where.md)