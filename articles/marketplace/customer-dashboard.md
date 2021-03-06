---
title: Панель мониторинга клиентов в Microsoft Marketplace Analytics в центре партнеров, Azure Marketplace и Microsoft AppSource
description: Узнайте, как получить доступ к информации о клиентах, в том числе о тенденциях роста, с помощью панели мониторинга клиентов в коммерческом аналитике Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 11/09/2020
author: sayantanroy83
ms.author: sroy
ms.openlocfilehash: 878fa181ff09d547ef6594a238e9c1d883cc89b7
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/01/2020
ms.locfileid: "96460485"
---
# <a name="customers-dashboard-in-commercial-marketplace-analytics"></a>Панель мониторинга клиентов в коммерческой аналитике Marketplace

Эта статья содержит сведения о панели мониторинга клиентов в центре партнеров. На этой панели мониторинга отображаются сведения о клиентах, включая тенденции роста, представленные в графическом и загружаемом формате.

Чтобы получить доступ к панели мониторинга клиентов в центре партнеров, в разделе **коммерческий Marketplace** выберите **[анализ](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)**  >  **клиентов**.

>[!NOTE]
> Подробное описание терминологии аналитики см. в статье [терминология анализа коммерческих рынков и распространенные вопросы](./analytics-faq.md).

## <a name="customers-dashboard"></a>Панель мониторинга клиентов

На панели мониторинга клиенты отображаются данные для клиентов, получивших ваши предложения. Можно просматривать следующие графические представления:

- Активная и обработанная тенденция клиентов
- Тенденция роста клиента, включая существующие, новые и обработанные клиенты
- Клиенты по заказам и использованию
- Процентиль для клиентов 
- Тип клиента по заказам и использованию
- Клиенты по географическим регионам
- Таблица сведений о клиентах
- Фильтры страниц клиентов

> [!NOTE]
> Максимальная задержка между получением клиентов и отчетами в центре партнеров составляет 48 часов.

## <a name="elements-of-the-customers-dashboard"></a>Элементы панели мониторинга "клиенты"

В следующих разделах описывается использование панели мониторинга "клиенты" и чтение данных.

### <a name="month-range"></a>Диапазон месяцев

Выбор диапазона месяца можно найти в правом верхнем углу каждой страницы. Настройте выходные данные графиков страниц **клиентов** , выбрав диапазон месяцев на основе последних 6 или 12 месяцев или выбрав диапазон настраиваемых месяцев с максимальной длительностью 12 месяцев. Диапазон месяцев по умолчанию (расчетный период) — 6 месяцев.

:::image type="content" source="./media/customer-dashboard/month-range-filters.png" alt-text="Иллюстрирует фильтры месяцев на странице клиенты.":::

> [!NOTE]
> Все метрики в мини-приложениях визуализации и отчетах экспорта учитывают период вычислений, выбранный пользователем.

### <a name="active-and-churned-customers-trend"></a>Активная и обработанная тенденция клиентов

В этом разделе вы найдете тенденцию роста ваших клиентов за выбранный период вычислений. Показатели и тенденции роста представлены графиком и отображают значения для каждого месяца, наведя на нее указатель мыши. Процентное значение ниже **активных клиентов** в мини-приложении представляет объем роста за выбранный период вычислений. Например, на следующем снимке экрана показан рост на 0,92% за выбранный период вычислений.

[![Демонстрирует мини-приложение "клиенты" на странице "клиенты".](./media/customer-dashboard/customers-widget.png)](./media/customer-dashboard/customers-widget.png#lightbox)

Существует три _типа клиентов_: новые, существующие и ушедшие.

- Новый клиент — это клиент, который приобрел одно или несколько предложений в первый раз в выбранном месяце.
- Существующий клиент — клиент, который приобрел одно или несколько предложений до выбранного месяца.
- Ушедший клиент — это клиент, отменивший все предложения, приобретенные ранее. Обработанные клиенты представлены на отрицательной оси в мини-приложении тренда.

### <a name="customer-growth-trend-including-existing-new-and-churned-customers"></a>Тенденция роста клиента, включая существующие, новые и обработанные клиенты

В этом разделе вы найдете тренд и число всех клиентов, включая новые, существующие и обработанные, с тенденциями роста за месяц.

- На графике представлен общий процент роста числа клиентов.
- В столбце месяца указывается число клиентов с разбивкой на новых, существующих и ушедших.
- Число обработанных клиентов отображается в отрицательном направлении оси X.
- Можно выбрать определенные элементы условных обозначений для отображения более подробных представлений. Например, в условных обозначениях выберите новые клиенты, чтобы отобразить только новых клиентов.
- При наведении указателя мыши на столбец на диаграмме отображаются сведения только за этот месяц.

[![Демонстрирует мини-приложение тренда клиентов на странице клиенты.](./media/customer-dashboard/customers-trend.png)](./media/customer-dashboard/customers-trend.png#lightbox)

### <a name="customers-by-ordersusage"></a>Клиенты по заказам и использованию

Диаграмма **клиенты по заказам и использованию** содержит три вкладки: заказы, нормализованное использование и необработанное использование. Перейдите на вкладку **заказы** , чтобы отобразить сведения о заказе.

:::image type="content" source="./media/customer-dashboard/customers-by-orders-usage.png" alt-text="Иллюстрирует вкладку Заказы мини-приложения заказы и использование на странице клиенты.":::

Следует отметить следующее.

- На лидерской доске представлены сведения о клиентах, ранжированных по количеству заказов. После выбора клиента сведения о клиенте представлены в разделах «сведения», «заказы по номерам SKU» и «SKU по местам».
- Сведения о профиле клиента отображаются в этом пространстве при входе издателей с ролью владельца. Если издатель вошел в учетную роль участника, сведения в этом разделе будут недоступны.
- На кольцевой диаграмме **заказы по номерам SKU** отображается разбивка заказов, приобретенных для планов. Отображаются пять первых планов с наибольшим количеством заказов, а остальные заказы группируются в разделе **все остальные**.
- На кольцевой диаграмме **SKU по рабочих** местам отображается распределение мест, упорядоченных по планам. Отображаются пять ведущих планов с наибольшим количеством рабочих мест, а остальные заказы группируются в разделе **все остальные**.

Для просмотра сведений об использовании можно также выбрать вкладку **нормализованное использование** или **необработанное использование** .

- На лидерской доске представлены сведения о клиентах, ранжированных по часам использования. После выбора клиента сведения о нем отображаются в смежном разделе "сведения", "нормализованное использование по предложениям" и "нормализованное использование по размерам виртуальных машин (ВМ)".
- Сведения о профиле клиента отображаются в этом пространстве, когда издатели входят в систему с ролью владельца. Если издатели вошли в систему с ролью участника, сведения в этом разделе будут недоступны.
- На кольцевой диаграмме **с нормализованным использованием** отображается декомпозиция использования, потребляемая предложениями. Отображаются пять первых планов с наибольшим числом использований, а остальные предложения группируются в разделе **все остальные**.
- В нормированной кольцевой диаграмме **размера виртуальной машины** показана декомпозиция использования, потребляемая различными размерами виртуальных машин. Отображаются пять основных размеров виртуальных машин с самым высоким нормализованным использованием, а остальная часть использования сгруппирована в разделе **все остальные**.

### <a name="top-customers-percentile"></a>Основные клиенты — процентиль

В диаграмме " **Основные клиенты** " имеется три вкладки: "заказы", "нормализованное использование" и "необработанное использование". _Верхний процент клиента_ отображается вдоль оси x, как определено в количестве заказов. На оси Y отображается количество заказов клиента. На вспомогательной оси Y (график) отображается суммарный процент общего количества заказов. Вы можете отобразить сведения, наведя указатель мыши на точки вдоль графика.

[![Иллюстрирует вкладку заказы в мини-приложении "основные клиентские приложения" на странице "клиенты".](./media/customer-dashboard/top-customer-percentile.png)](./media/customer-dashboard/top-customer-percentile.png#lightbox)

### <a name="customer-type-by-orders-and-usage"></a>Тип клиента по заказам и использованию

На диаграмме **Заказы и использование по типу клиента** отображается число заказов, нормализованное использование и количество часов непосредственного использования по новым клиентам и существующим клиентам на основе выбора заказов, нормализованного и необработанного использования соответственно.

На этой диаграмме показано следующее:

- Новый клиент — это клиент, который приобрел одно или несколько предложений в первый раз в одном и том же месяце (ось Y) или сообщил о первом использовании предложения.
- Существующий клиент — это клиент, который ранее получал предложение от вас или сообщил об использовании до того, как был сформирован отчет по календарному месяцу (на оси Y).

[![Иллюстрирует вкладку заказы в мини-приложении тип заказов по типу клиента на странице клиенты.](./media/customer-dashboard/orders-by-customer-type.png)](./media/customer-dashboard/orders-by-customer-type.png#lightbox)

### <a name="customers-by-geography"></a>Клиенты по географическим регионам

Для выбранного периода вычислений тепловой карты отображает общее количество клиентов и процент новых добавленных клиентов по измерению geography. Количество клиентов обозначается цветом. Чем больше клиентов, тем темнее цвет. Выберите запись в таблице, чтобы увеличить страну или регион.

[![Иллюстрирует вкладку Заказы мини-приложения «заказы по географии» на странице «клиенты».](./media/customer-dashboard/customers-by-geography.png)](./media/customer-dashboard/customers-by-geography.png#lightbox)

Следует отметить следующее.

- Чтобы увидеть точное расположение, можно переместить карту.
- Также можно приблизить конкретное расположение.
- К тепловой карте добавлена вспомогательная таблица, в которой приведены сведения о количестве клиентов, количестве заказов и часах нормализованного использования в определенных расположениях.
- Вы можете найти и выбрать страну в таблице, чтобы приблизить соответствующее расположение на карте. Вернитесь к исходному представлению, нажав кнопку **Главная** на карте.

### <a name="customer-details-table"></a>Таблица сведений о клиенте

В таблице " **сведения о клиенте** " отображается нумерованный список лучших 1 000 клиентов, отсортированных по дате первого приобретения одного из ваших предложений. Раздел можно развернуть, щелкнув значок расширения на ленте сведения.

Следует отметить следующее.

- Персональные данные клиента отображаются только в том случае, если клиент дал согласие на это. Эти сведения можно просмотреть только в том случае, если вы выполнили вход с правами владельца на уровне роли.
- Каждый столбец в сетке доступен для сортировки.
- Данные могут быть извлечены в. CSV или. TSV-файл, если число записей меньше 1 000.
- Если номер записи больше 1 000, экспортированные данные будут асинхронно помещены на страницу загрузки в течение следующих 30 дней.
- Примените фильтры к таблице, чтобы отобразить только интересующие вас данные. Фильтрация данных по названию компании, ИДЕНТИФИКАТОРу клиента, ИДЕНТИФИКАТОРу подписки Marketplace, типу лицензии Azure, дате получения, дате потери, адресу электронной почты клиента, стране/региону клиента, штату, городу/ZIP, языку клиента и т. д.
- Когда предложение приобретено защищенным клиентом, информация в поле **Подробные сведения о клиенте** будет скрыта (************).
- Сведения об измерении клиента, такие как название компании, имя клиента и адрес электронной почты клиента, находятся на уровне идентификатора организации, а не в Azure Marketplace или на уровне транзакций Microsoft AppSource.

_**Таблица 1. Словарь терминов данных**_

| Имя столбца | Имя атрибута | Определение |
| ------------ | ------------- | ------------- |
| Идентификатор подписки Marketplace | Идентификатор подписки на платформу | Уникальный идентификатор, связанный с подпиской Azure, которую клиент использовал для приобретения предложения коммерческого рынка. Ранее назывался GUID подписки Azure. |
| датеаккуиред | Дата приобретения | Первая дата, когда клиент приобрел любое опубликованное предложение. |
| дателост | Дата отмены | Последняя дата, когда Клиент отменил Последнее из всех ранее приобретенных предложений. |
| Provider Name | Provider Name | Имя поставщика, участвующего в связи между корпорацией Майкрософт и клиентом. Если клиент является предприятием, работающим через торгового посредника, это будет торговый посредник. Если задействован поставщик облачных решений (CSP), это будет CSP. |
| Адрес электронной почты поставщика | Адрес электронной почты поставщика | Адрес электронной почты поставщика, участвующего в связи между корпорацией Майкрософт и клиентом. Если клиент является предприятием, работающим через торгового посредника, это будет торговый посредник. Если задействован поставщик облачных решений (CSP), это будет CSP. |
| FirstName | Имя клиента | Первое имя, предоставленное клиентом. Имя может отличаться от имени, указанного в подписке Azure клиента. |
| LastName | Фамилия клиента | Последнее имя, предоставленное клиентом. Имя может отличаться от имени, указанного в подписке Azure клиента. |
| Email | Электронная почта клиента | Адрес электронной почты, который указал клиент. Адрес электронной почты может отличаться от адреса электронной почты в подписке Azure клиента. |
| Название компании клиента | Название компании клиента | Название компании, которое указал клиент. Имя может отличаться от названия города в подписке Azure клиента. |
| CustomerCity | Город клиента | Название города, которое указал клиент. Город может отличаться от города в подписке Azure клиента. |
| Почтовый индекс клиента | Почтовый индекс клиента | Почтовый индекс, который указал клиент. Код может отличаться от почтового кода, предоставленного в подписке Azure клиента. |
| CustomerCommunicationCulture | Язык общения с клиентом | Язык, который клиент предпочитает при общении. |
| CustomerCountryRegion | Страна или регион клиента | Название страны или региона, которое указал клиент. Страна или регион могут отличаться от страны или региона в подписке Azure клиента. |
| азурелиценсетипе | Тип лицензии Azure | Тип лицензионного соглашения, используемого клиентами для покупок в Azure. Также называется _каналом_. Допустимые значения:<ul><li>Поставщик облачных решений</li><li>Предприятие</li><li>Enterprise через торгового посредника</li><li>Оплата по мере использования</li></ul> |
| промотионалкустомерс | Согласие на получение рекламных материалов | Значение позволяет определить, согласился ли клиент получать рекламные материалы от издателей. Сейчас этот параметр недоступен клиентам, поэтому повсеместно указано значение "Нет". После развертывания этой функции мы начнем обновление соответствующим образом. |
| кустомерстате | Регион клиента | Состояние проживания, предоставленное клиентом. Состояние может отличаться от состояния, указанного в подписке Azure клиента. |
| коммерцеруткустомер | Корневой клиент Commerce | Один идентификатор счета выставления счетов может быть связан с несколькими идентификаторами клиента.<br>Одно сочетание идентификатора счета для выставления счетов и идентификатора клиента может быть связано с несколькими коммерческими подписками Marketplace.<br>Корневой клиент Commerce означает имя клиента подписки. |
| Customer Id | ИД клиента | Уникальный идентификатор, назначенный клиенту. У клиента может быть не более одной подписки Azure Marketplace. |
| Идентификатор счета выставления счетов | Идентификатор счета выставления счетов | Идентификатор учетной записи, для которой формируется выставление счетов. Сопоставьте **идентификатор счета для выставления счетов с кодом** **CustomerID** , чтобы подключить отчет о транзакциях выплат с отчетами по клиентам, заказам и использованию. |
||||

### <a name="customers-page-filters"></a>Фильтры страниц клиентов

Фильтры на странице клиента применяются на уровне этой страницы. Можно выбрать несколько фильтров, чтобы отобразить диаграмму с выбранными критериями, а также данные, которые необходимо просмотреть в сетке или экспорте "подробные данные о заказе". Фильтры применяются к данным, извлекаемым в течение диапазона месяцев, выбранного в правом верхнем углу страницы клиенты.

> [!TIP]
> Для загрузки данных можно использовать значок скачивания в правом верхнем углу любого мини-приложения. Вы можете отправить отзыв о каждом из мини-приложений, щелкнув значок "палец Up" или "палец вниз".

## <a name="next-steps"></a>Дальнейшие действия

- Обзор аналитических отчетов, доступных в коммерческом магазине, см. в статье [доступ к аналитикам для коммерческого рынка в центре партнеров](./partner-center-portal/analytics.md).
- Сведения о диаграммах, трендах и значениях статистических данных, суммирующих действия платформы для вашего предложения, см. в статье [Сводная панель мониторинга в аналитике коммерческой платформы](./summary-dashboard.md).
- Сведения о заказах в графическом и загружаемом формате см. в статье [панель мониторинга заказов в коммерческом Analytics Marketplace](./orders-dashboard.md).
- Для виртуальной машины (VM) предоставляет метрики использования и измерения для выставления счетов. см. раздел [панель мониторинга "использование" в коммерческом Analytics Marketplace](./usage-dashboard.md).
- Список запросов на загрузку за последние 30 дней см. в статье [Панель мониторинга загрузок в аналитике коммерческой платформы](./partner-center-portal/downloads-dashboard.md).
- Чтобы просмотреть объединенное представление отзывов клиентов о предложениях в Azure Marketplace и Microsoft AppSource, см. статью [оценки & рецензий аналитики в центре партнеров](./partner-center-portal/ratings-reviews.md).
- Часто задаваемые вопросы о коммерческой аналитике и подробном словаре терминов данных см. в разделе [терминология и общие вопросы по анализу коммерческих рынков](./analytics-faq.md).