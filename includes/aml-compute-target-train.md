---
title: включить файл
description: включить файл
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 09/17/2020
ms.openlocfilehash: 87b1f4ab7b7091970d7bb76ae1e00b06549fb0b4
ms.sourcegitcommit: 2e9643d74eb9e1357bc7c6b2bca14dbdd9faa436
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/25/2020
ms.locfileid: "96152703"
---
**Целевые объекты вычислений могут использоваться повторно для разных заданий обучения**. Например, после подключения удаленной виртуальной машины к вашей рабочей области ее можно многократно использовать для нескольких заданий. В конвейерах машинного обучения используйте правильный [шаг конвейера](/python/api/azureml-pipeline-steps/azureml.pipeline.steps?preserve-view=true&view=azure-ml-py) для каждого целевого объекта вычислений.

Для большинства заданий можно использовать любой из приведенных ниже ресурсов для обучающего целевого объекта вычислений. Не все ресурсы можно использовать для автоматизированного машинного обучения, конвейеров машинного обучения или конструктора.

|Целевые объекты &nbsp;обучения|[Автоматизированное машинное обучение](../articles/machine-learning/concept-automated-ml.md) | [Конвейеры машинного обучения](../articles/machine-learning/concept-ml-pipelines.md) | [Конструктор Машинного обучения Azure](../articles/machine-learning/concept-designer.md)
|----|:----:|:----:|:----:|
|[Локальный компьютер](../articles/machine-learning/how-to-attach-compute-targets.md#local)| Да | &nbsp; | &nbsp; |
|[Вычислительный кластер Машинного обучения Azure](../articles/machine-learning/how-to-create-attach-compute-cluster.md)| Да | Да | Да |
|[Вычислительная операция Машинного обучения Azure](../articles/machine-learning/how-to-create-manage-compute-instance.md) | да (с помощью пакета SDK)  | Да |  |
|[Удаленная виртуальная машина](../articles/machine-learning/how-to-attach-compute-targets.md#vm) | Да  | Да | &nbsp; |
|[Azure&nbsp;Databricks](../articles/machine-learning/how-to-attach-compute-targets.md#databricks)| да (только в локальном режиме SDK) | Да | &nbsp; |
|[Azure Data Lake Analytics](../articles/machine-learning/how-to-attach-compute-targets.md#adla) | &nbsp; | Да | &nbsp; |
|[Azure HDInsight](../articles/machine-learning/how-to-attach-compute-targets.md#hdinsight) | &nbsp; | Да | &nbsp; |
|[Пакетная служба Azure](../articles/machine-learning/how-to-attach-compute-targets.md#azbatch) | &nbsp; | Да | &nbsp; |
