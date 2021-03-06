---
title: Руководство по изучению наборов ресурсов, сведений, схем и классификаций в Azure Purview (предварительная версия).
description: В этом учебнике описывается, как использовать наборы ресурсов, сведения о ресурсах, схемы и классификации.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 12/01/2020
ms.openlocfilehash: c39ed746630c646b0ce305a9535ae6d610f5161c
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/03/2020
ms.locfileid: "96549764"
---
# <a name="tutorial-explore-resource-sets-details-schemas-and-classifications-in-azure-purview-preview"></a>Руководство по изучению наборов ресурсов, сведений, схем и классификаций в Azure Purview (предварительная версия).

> [!IMPORTANT]
> Сейчас предоставляется предварительная версия Azure Purview. [Дополнительные условия использования Azure для предварительных версий в Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) содержат дополнительные юридические условия, применимые к функциям Azure, которые предоставляются в бета-версии, предварительной версии или еще не выпущены в общедоступной версии по другим причинам.

В этом учебнике рассматриваются ключевые компоненты каталога: наборы ресурсов, сведения о ресурсах, схемы и классификации.

Этот учебник является *четвертой из пяти частей серии руководств*, в которой вы изучаете основы управления данными в пространстве данных с помощью Azure Purview. Этот учебник создан на основе работы, выполненной в рамках статьи [Часть 3. Просмотрите ресурсы в Azure Purview (предварительная версия) и просмотрите их происхождение данных](tutorial-browse-and-view-lineage.md).

В этом руководстве описано следующее:

> [!div class="checklist"]
>
> * Просмотр наборов ресурсов.
> * Просмотр сведений о ресурсе.
> * Изменение схемы и добавление классификаций.

## <a name="prerequisites"></a>Предварительные требования

* Выполните инструкции из [руководства по  Просмотрите ресурсы в Azure Purview (предварительная версия) и просмотрите их происхождение данных](tutorial-browse-and-view-lineage.md).

## <a name="sign-in-to-azure"></a>Вход в Azure

Войдите на [портал Azure](https://portal.azure.com).

## <a name="view-resource-sets"></a>Просмотр наборов ресурсов

Набор ресурсов — это отдельный объект в каталоге, представляющий большое число физических объектов в хранилище. Обычно такие объекты имеют общую схему, а в большинстве случаев и общее соглашение об именовании или структуру папок. Например, даты в формате *гггг/мм/дд*. Дополнительные сведения о наборах ресурсов см. в разделе [Знакомство с наборами ресурсов](concept-resource-sets.md).

1. Перейдите к ресурсу Azure Purview на портале Azure и выберите элемент **Open Purview Studio** (Открыть Purview Studio). Вы автоматически перейдете на домашнюю страницу Purview Studio.

2. Введите **contoso_staging_positivecashflow_n** в поле **Поиск ресурсов**, а затем выберите **Contoso_Staging_PositiveCashFlow_{N}.csv** в результатах поиска.

## <a name="view-asset-details"></a>Просмотр сведений о ресурсе

1. На вкладке **Обзор** отображается **Описание**, **Глоссарные термины** и **Свойства**, например **qualifiedName**.

   :::image type="content" source="./media/tutorial-schemas-and-classifications/overview-tab.png" alt-text="Снимок экрана с вкладкой &quot;Обзор&quot; на странице ресурса набора ресурсов.":::

1. В разделе **Свойства** обратите внимание на следующие два поля:

   * **partitionCount**: Указывает число физических файлов, связанных с этим набором ресурсов.
   * **schemaCount**: Указывает число вариантов схемы, найденных в этом наборе ресурсов.

   Azure Purview заполняет эти свойства в течение 24 часов после завершения проверки из [части 1 этой серии руководств](tutorial-scan-data.md).

1. Перейдите на вкладку **Контакты**, чтобы ознакомиться со значениями **Специалисты** и **Владельцы**.

## <a name="edit-the-schema-and-add-classifications"></a>Изменение схемы и добавление классификаций

1. Выберите вкладку **Схема**. Обратите внимание на имена столбцов и связанные с ними классификации. Обратите внимание, что при сканировании свойства заполняются автоматически.

   :::image type="content" source="./media/tutorial-schemas-and-classifications/schema-tab.png" alt-text="Снимок экрана с вкладкой &quot;Схема&quot;.":::

1. Чтобы изменить ресурс, нажмите кнопку **Изменить** в левом верхнем углу. Затем выберите вкладку **Схема**.

1. Добавьте **Описание** для столбца или переименуйте столбец, дав ему более понятное имя.

1. Добавьте классификацию на уровне ресурса, выбрав раскрывающийся список **Классификация на уровне столбцов** для имени столбца, для которого не задана классификация.

   :::image type="content" source="./media/tutorial-schemas-and-classifications/edit-schema.png" alt-text="Страница изменения схемы":::

1. Завершив работу с изменениями, выберите команду **Сохранить**.

## <a name="next-steps"></a>Дальнейшие действия

В этом руководстве вы узнали, как выполнять следующие задачи:

> [!div class="checklist"]
>
> * Просмотр наборов ресурсов.
> * Просмотр сведений о ресурсе.
> * Изменение схемы и добавление классификаций.

Перейдите к следующему руководству, чтобы узнать о глоссарии и определить и импортировать новые термины для ресурсов.

> [!div class="nextstepaction"]
> [Создание и импорт глоссарных терминов](tutorial-import-create-glossary-terms.md)