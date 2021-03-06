---
title: Безопасный гибридный доступ Azure AD с руководством по развертыванию F5 | Документация Майкрософт
description: Руководство по развертыванию виртуальной машины с большим IP-адресом (VE) в Azure IaaS для безопасного гибридного доступа
services: active-directory
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 10/12/2020
ms.author: gasinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6c03009b08dcf33bf4b84bc91232af96e7ba2c71
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/10/2020
ms.locfileid: "97095191"
---
# <a name="tutorial-to-deploy-f5-big-ip-virtual-edition-vm-in-azure-iaas-for-secure-hybrid-access"></a>Руководство по развертыванию виртуальной машины с большим IP-адресом F5 в Azure IaaS для безопасного гибридного доступа

В этом руководстве описано, как завершить процесс развертывания Витурал Edition с большим IP-адресом (VE) в Azure IaaS. По завершении работы с этим руководством необходимо:

- Полностью подготовленная виртуальная машина с большими IP-адресами (VM) для моделирования подтверждения безопасного гибридного доступа (SHA)

- Промежуточный экземпляр, используемый для тестирования новых обновлений системы с большими IP-адресами и исправлений

## <a name="prerequisites"></a>Предварительные требования

Раньше F5 при больших IP-адресах или знание не требуется, однако мы рекомендуем ознакомиться с [терминологией F5 BIG-IP](https://www.f5.com/services/resources/glossary). Для развертывания больших IP-адресов в Azure для SHA требуется:

- Платная подписка Azure или бесплатная 12-месячная [пробная подписка](https://azure.microsoft.com/free/).

- Любой из следующих номеров SKU лицензий с большими IP-адресами F5

  - F5 BIG-IP® лучший пакет

  - отдельная лицензия F5 BIG-IP Access Policy Manager™ (APM).

  - Клавиша F5 BIG-IP Access Configuration Manager™ (APM) лицензия на существующий большой IP-адрес F5 BIG-IP® локальный диспетчер трафика™ (LTM)
  
  - 90-дневная [Лицензия на пробную версию](https://www.f5.com/trial/big-ip-trial.php)полного IP-адреса.

- Подстановочный знак или сертификат альтернативного имени субъекта (SAN) для публикации веб-приложений по протоколу SSL. [Шифрование](https://letsencrypt.org/) предоставляет бесплатный сертификат 90 дней для тестирования.

- SSL-сертификат для защиты интерфейса управления BIG-IPs. Сертификат, используемый для публикации веб-приложений, можно использовать, если его субъект соответствует полному доменному имени (FQDN) в большом IP-адресе. Например, шаблонный сертификат, определенный с темой *. contoso.com, подходит для `https://big-ip-vm.contoso.com:8443`

Развертывание виртуальной машины и базовая конфигурация системы занимает около 30 минут. в этом случае Ваша платформа с большими IP-адресами будет готова к реализации любого из сценариев SHA, перечисленных [здесь](f5-aad-integration.md).

Для тестирования сценариев в этом учебнике предполагается, что большой IP-адрес будет развернут в группе ресурсов Azure, содержащей среду Active Directory (AD). Среда должна состоять из виртуальных машин контроллера домена (DC) и веб-узлов (IIS). Наличие этих серверов в других расположениях для виртуальной машины с большим IP-адресом также является нормальным, что обеспечивает большой IP-адрес для каждой роли, необходимой для поддержки конкретного сценария. Также поддерживаются сценарии, в которых виртуальная машина с большим IP-адресом подключена к другой среде через VPN-подключение.

Если у вас нет упомянутых здесь элементов для тестирования, вы можете развернуть в Azure всю среду домена AD с помощью этого [сценария](https://github.com/Rainier-MSFT/Cloud_Identity_Lab). Коллекция примеров тестовых приложений также может быть программно развернута на веб-узле IIS с помощью этой [службы автоматизации на основе скриптов](https://github.com/jeevanbisht/DemoSuite).

>[!NOTE]
>[Портал Azure](https://portal.azure.com/#home) постоянно развивается, поэтому некоторые шаги в этом учебнике могут отличаться от фактических макетов, наблюдаемых в портал Azure.

## <a name="azure-deployment"></a>Развертывание Azure

БОЛЬШОЙ IP-адрес может быть развернут в разных топологиях. В этом руководством рассматривается развертывание одного сетевого интерфейса (NIC). Однако если для развертывания большого IP-адреса требуется несколько сетевых интерфейсов для обеспечения высокой доступности, разделения сети или более 1 ГБ пропускной способности, рассмотрите возможность использования Bigip предварительно скомпилированных [шаблонов Azure Resource Manager (ARM)](https://clouddocs.f5.com/cloud/public/v1/azure/Azure_multiNIC.html).

Выполните следующие задачи, чтобы развернуть BIG-IP VE из [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps).

1. Войдите в [портал Azure](https://portal.azure.com/#home) с помощью учетной записи, обладающей разрешениями на создание виртуальных машин. Например, участник

2. В поле поиска на ленте вверху введите **Marketplace**, а затем **введите**

3. Введите **F5** в фильтр Marketplace, а затем **введите**

4. Выберите **+ Добавить** на верхней ленте и введите **F5** в фильтр Marketplace, а затем **введите**

5. Выберите **F5 BIG-IP Virtual Edition (BYOL)**  >  **выберите план программного обеспечения**  >  **F5 крупный-IP VE-ALL (BYOL, 2 загрузочные расположения)**

6. Нажмите кнопку **создания**.

![На рисунке показаны шаги для выбора плана программного обеспечения](./media/f5ve-deployment-plan/software-plan.png)

7. Выполните шаг с заходом в меню " **основные** " и используйте следующие параметры.

 |  сведения о проекте;     |  Значение     |
 |:-------|:--------|
 |Подписка|Целевая подписка для развертывания виртуальной машины с большим IP-адресом|
 |Группа ресурсов | Существующая группа ресурсов Azure. Виртуальная машина с большим IP-адресом будет развернута или создана. Должна быть той же группой ресурсов для виртуальных машин DC и IIS|
 | **Сведения об экземпляре**|  |
 |Имя виртуальной машины| Пример больших IP-адресов — виртуальная машина |
 |Регион | Целевой объект Azure для больших IP-адресов — виртуальная машина |
 |Параметры доступности| Включить только при использовании виртуальной машины в рабочей среде|
 |Образ| F5 BIG-IP VE — все (BYOL, 2 загрузочные расположения)|
 |Точечный экземпляр Azure| Нет, но вы можете включить его при необходимости |
 |Размер| Минимальная спецификация должна иметь размер 2 виртуальных ЦП и 8 ГБ памяти.|
 |**Учетная запись администратора**|  |
 |Authentication type (Тип проверки подлинности)|Выберите пароль сейчас. Вы можете переключиться на пару ключей позже |
 |Имя пользователя|Удостоверение, которое будет создано как локальная учетная запись с большими IP-адресами для доступа к интерфейсам управления. Имя пользователя чувствительно к РЕГИСТРу.|
 |Пароль|Безопасный доступ администратора с надежным паролем|
 |**Правила входящего порта**|  |
 |Общедоступные входящие порты|Нет|

8. Нажмите кнопку **Далее: диски** , в которых находятся все значения по умолчанию, и выберите **Далее: сеть**.

9. В меню **Сетевые подключения** заполните эти параметры.

 |Сетевой интерфейс|      Значение |
 |:--------------|:----------------|
 |Виртуальная сеть|Одной виртуальной сети Azure, используемой виртуальными машинами DC и IIS, или создайте ее|
 |Подсеть| Ту же внутреннюю подсеть Azure, что и виртуальные машины DC и IIS, либо создайте одну из них.|
 |Общедоступный IP-адрес |  Нет|
 |Сетевая группа безопасности сетевого адаптера| Выберите Нет, если подсеть Azure, выбранная на предыдущих шагах, уже связана с группой безопасности сети (NSG). в противном случае выберите базовый.|
 |Ускорение работы в сети| Выключено |
 |**Балансировка нагрузки**|     |
 |Балансировка нагрузки виртуальной машины| Нет|

10. Выберите **Далее: Управление** и заполните эти параметры.

 |Наблюдение|    Значение |
 |:---------|:-----|
 |Подробный мониторинг| Выключено|
 |Диагностика загрузки|Включите с помощью настраиваемой учетной записи хранения. Разрешает подключение к интерфейсу Secure Shell с большим IP-адресом (SSH) с помощью параметра последовательной консоли в портал Azure. Выберите любую доступную учетную запись хранения Azure|
 |**Удостоверение**|  |
 |Управляемое удостоверение, назначенное системой|Выключено|
 |Azure Active Directory|BIG-IP сейчас не поддерживает этот параметр|
 |**Автоматическое завершение работы**|    |
 |Включить автоматическое завершение работы| При тестировании рассмотрите возможность ежедневного завершения работы с большим IP-адресом.|

11. Нажмите кнопку **Далее: дополнительно** покинуть все значения по умолчанию и выберите **Далее: Теги**.

12. Выберите **Далее: Проверка + создать** , чтобы проверить конфигурации больших IP-адресов виртуальной машины, прежде чем выбрать пункт **создать** в начале развертывания.

13. Время полного развертывания виртуальной машины с большим IP-адресом обычно составляет 5 минут. По завершении не выбирайте **Переход к ресурсу**, вместо этого разверните меню слева портал Azure и выберите **группы ресурсов** для перехода к новой большой IP-виртуальной машине. Если создание виртуальной машины завершается сбоем, нажмите кнопку **назад** и **Далее**.

## <a name="network-configuration"></a>Сетевая конфигурация

При первой загрузке виртуальной машины с большим IP-адресом ее сетевая карта будет подготовлена с помощью **основного** частного IP-адреса, выданного службой DHCP в подсети Azure, к которой он подключен. Этот IP-адрес будет использоваться операционной системой для управления трафиком больших IP-адресов (ТМОС) для взаимодействия с:

- Взаимодействие с другими узлами и службами

- Исходящий доступ к общедоступному Интернету

- Входящий доступ к BIG-IPs веб-конфигурации и интерфейсам управления SSH

Предоставление доступа к любому из этих интерфейсов управления приводит к повышению BIG-IPs уязвимости, поэтому, почему BIG-IPs первичный IP-адрес не был подготовлен с помощью общедоступного IP-адреса во время развертывания. Вместо этого вторичный внутренний IP-адрес и связанный с ним общедоступный IP-адрес будут подготовлены для служб публикации.
Это 1-1 сопоставление между общедоступным IP-адресом виртуальной машины и частным IP-адресом позволяет внешнему трафику достичь виртуальной машины. Однако правило Azure NSG также требуется, чтобы разрешить трафик таким же образом, как и брандмауэр.

На схеме показано одно развертывание сетевого адаптера с большим IP-адресом в Azure, настроенное с основным IP-адресом для общих операций и управления, а также отдельные адреса виртуального сервера для служб публикации.
В этом расположении правило NSG разрешает удаленный трафик, предназначенный для `intranet.contoso.com` маршрутизации к общедоступному IP-адресу опубликованной службы, перед пересылкой на виртуальный сервер с большим IP-адресом.

![На рисунке показано развертывание одного сетевого адаптера ](./media/f5ve-deployment-plan/single-nic-deployment.png) . частные и общедоступные IP-адреса, выданные виртуальным машинам Azure, всегда являются динамическими, поэтому, скорее всего, изменятся при каждом перезапуске виртуальной машины. Избегайте непредвиденных проблем с подключением, изменив IP-адрес управления BIG-IPs на статический и сделайте то же самое для вторичных адресов, используемых для служб публикации.

1. В меню виртуальной машины с большим IP-адресом выберите **Параметры**  >  **сеть** .

2. В представлении сети выберите ссылку справа от параметра **сетевой интерфейс** .

![На изображении показана конфигурация сети](./media/f5ve-deployment-plan/network-config.png)

>[!NOTE]
>Имена виртуальных машин создаются случайным образом во время развертывания.

3. В области слева выберите **IP-конфигурации** , а затем выберите строку **ipconfig1** .

4. Задайте для параметра **назначение IP-** адреса значение статический и при необходимости измените основной IP-адрес ВИРТУАЛЬНЫХ машин большой IP-адресов. Затем выберите **сохранить** и закройте меню ipconfig1.

>[!NOTE]
>Этот первичный IP-адрес будет использоваться для подключения и управления большим IP-адресом виртуальной машины.

5. Выберите **+ Добавить** на верхней ленте и укажите имя вторичного частного IP-адреса, например ipconfig2.

6. Задайте для параметра **выделения** частных IP-адресов значение **статический**. Предоставление следующего более высокого или более низкого порядкового IP-адреса помогает обеспечить порядок.

7. Установите **связь** с общедоступным IP-адресом и выберите **создать** .

8. Укажите имя для нового общедоступного IP-адреса, например BIG-IP-VM_ipconfig2_Public

9. При появлении запроса задайте для параметра **SKU** значение Standard.

10. При появлении запроса задайте для параметра **уровень** значение **Глобальная** и

11. Задайте для параметра **назначение** значение **статический**, а затем дважды нажмите кнопку **ОК** .

Теперь ваша большая IP-виртуальная машина готова к настройке:

- **Основной частный IP-адрес**. предназначен для управления большими IP-адресами виртуальных машин с помощью служебной программы веб-конфигурации и SSH. Она будет использоваться системой больших IP-адресов в качестве **самостоятельного IP-адреса** для подключения к опубликованным серверным службам. Кроме того, он подключается к внешним службам, таким как NTP, AD и LDAP.

- **Вторичный частный IP-адрес**. используется при создании виртуального сервера APM с большим IP-адресом для прослушивания входящего запроса к опубликованным службам.

- **Общедоступный IP-адрес**. связан с дополнительным частным IP-адресом, позволяет трафику клиента из общедоступного Интернета подключиться к виртуальному серверу с большим IP-адресом для опубликованных служб.

В примере показана связь между общедоступными и частными IP-адресами виртуальной машины с 1 до 1. Сетевая карта виртуальной машины Azure может иметь только один первичный IP-адрес, а все созданные дополнительно адреса всегда называются дополнительными.

>[!NOTE]
>Для публикации служб с большими IP-адресами потребуются дополнительные сопоставления IP.

![На этом рисунке показаны все вторичные IP-адреса](./media/f5ve-deployment-plan/secondary-ips.png)

Чтобы реализовать SHA с помощью **конфигурации** с большим IP-адресом, повторите шаги 5-11, чтобы создать дополнительные пары частных и общедоступных IP-адресов для каждой дополнительной службы, которую вы планируете опубликовать с помощью службы больших IP-адресов APM. Такой же подход также можно использовать при публикации служб с помощью BIG-IPs **расширенной конфигурации**. Однако в этом сценарии можно исключить общедоступные IP-адреса, используя конфигурацию [индикатора имени сервера (SNI)](https://support.f5.com/csp/#/article/K13452) . В этой конфигурации виртуальный сервер с большим IP-адресом будет принимать весь получаемый трафик клиента и маршрутизировать его к назначению.

## <a name="dns-configuration"></a>Настройка DNS

Важно, чтобы клиенты настроили DNS для разрешения опубликованных служб SHA в общедоступные IP-адреса виртуальных машин.

В следующих шагах предполагается, что зона DNS общедоступного домена, используемая для служб SHA, управляется в Azure. Тем не менее те же принципы DNS, которые создает запись локатора, по-прежнему применяются независимо от того, где находится ваша зона DNS.

1. Если это еще не сделано, разверните меню левого портала и перейдите к папке BIG-IP-VM с помощью параметра **группы ресурсов** .

2. В меню виртуальной машины с большим IP-адресом выберите **Параметры**  >  **сеть** .

3. В представлении сети "крупные IP-адреса — виртуальные машины" выберите первый вторичный IP-адрес из раскрывающегося списка IP-конфигурации и щелкните ссылку для **общедоступного IP-адреса NIC** .

![Снимок экрана, показывающий общедоступный IP-адрес сетевого адаптера](./media/f5ve-deployment-plan/networking.png)

4. Под разделом **Параметры** на левой панели выберите **Конфигурация** , чтобы открыть меню свойства общедоступного IP-адреса и DNS для выбранного вторичного IP-адреса.

5. Выберите и **Создайте** запись псевдонима и выберите свою **зону DNS** из раскрывающегося списка. Если зона DNS еще не существует, она может управляться за пределами Azure или создать ее для суффикса домена, который был проверен в Azure AD.

6. Чтобы создать первую запись DNS-псевдонима, используйте следующие сведения:

 | Поле | Значение |
 |:-------|:-----------|
 |Подписка| Та же подписка, что и для больших IP-адресов — ВМ;|
 |Зона DNS| Зона DNS, которая является полномочным для проверенного суффикса домена, который будет использовать опубликованные веб-сайты, например www.contoso.com |
 |Имя | Указанное имя узла будет разрешаться в общедоступный IP-адрес, связанный с выбранным вторичным IP-адресом. Убедитесь, что определены правильные сопоставления DNS и IP. См. Последнее изображение в разделе конфигурации сети, например intranet.contoso.com > 13.77.148.215|
 | Срок жизни | 1 |
 |Единицы TTL | Часы |

7. Выберите **создать** для Azure, чтобы сохранить эти параметры в общедоступной службе DNS.

8. Оставьте **метку DNS-имени (необязательно)** и выберите **сохранить** перед закрытием меню общедоступного IP-адреса.

9. Повторите шаги с 1 по 6, чтобы создать дополнительные записи DNS для каждой службы, которую планируется опубликовать с помощью интерактивной конфигурации с большим IP-адресом.

  Используя записи DNS, можно использовать любые интерактивные средства, такие как [средство проверки DNS](https://dnschecker.org/) , чтобы убедиться, что созданная запись успешно распространена на всех глобальных общедоступных DNS-серверах.

  Если вы управляете пространством имен домена DNS с помощью внешнего поставщика, например [GoDaddy](https://www.godaddy.com/), вам потребуется создать записи с помощью собственного средства управления DNS.

>[!NOTE]
>Вы также можете использовать локальный файл hosts компьютера при тестировании и частом переключении записей DNS. Доступ к файлу localhost на компьютере с Windows можно получить, нажав клавиши Win + R на клавиатуре и отправив **драйверы** Word в поле выполнить. Просто учитывать, что запись localhost будет предоставлять разрешение DNS только для локального компьютера, а не для других клиентов.

## <a name="client-traffic"></a>Трафик клиента

По умолчанию виртуальных сетей и связанные подсети Azure являются частными сетями, которые не могут получить Интернет. Сетевая карта с большими IP-адресами должна быть подключена к NSG, указанному во время развертывания. Чтобы внешний веб-трафик был доступен на виртуальной машине с большим IP-адресом, необходимо определить правило NSG для входящего трафика, разрешающее порты 443 (HTTPS) и 80 (HTTP) через общедоступный Интернет.

1. В главном меню **Обзор** виртуальной машины с большим IP-адресом выберите **сеть** .

2. Выберите **Добавить** правило для входящего трафика, чтобы ввести следующие свойства правила NSG:

 |     Поле   |   Значение        |
 |:------------|:------------|
 |Источник| Любой|
 |Диапазоны исходных портов| *|
 |IP-адреса назначения|Список с разделителями-запятыми всех дополнительных частных IP-адресов виртуальной машины.|
 |Конечные порты| 80, 443|
 |Протокол| TCP |
 |Действие| Allow|
 |Приоритет|Наименьшее доступное значение между 100-4096|
 |Имя | Описательное имя, например: `BIG-IP-VM_Web_Services_80_443`|

3. Нажмите кнопку **Добавить** , чтобы зафиксировать изменения и закрыть меню **Сетевые подключения** .

Теперь трафик HTTP и HTTPS из любого расположения будет разрешен для доступа ко всем вторичным интерфейсам виртуальных машин с большими IP-адресами. Разрешено использовать порт 80, чтобы позволить БОЛЬШОМу IP-адресу APM выполнять автоматический перенаправление пользователей с HTTP на HTTPS. Это правило можно изменить для добавления или удаления IP-адресов назначения при необходимости.

## <a name="manage-big-ip"></a>Управление большими IP-адресами

Система с большими IP-адресами управляется с помощью пользовательского интерфейса веб-настройки, доступ к которому можно получить с помощью любого из следующих рекомендуемых методов:

- С компьютера в внутренней сети с большим IP-адресом

- От VPN-клиента, подключенного к внутренней сети с большим IP-адресом

- Опубликовано с помощью [Azure AD application proxy](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application)

Прежде чем продолжить настройку, необходимо выбрать наиболее подходящий метод. При необходимости можно подключиться непосредственно к веб-конфигурации из Интернета, настроив основной IP-адрес с общедоступным IP-адресом. Затем добавьте правило NSG, чтобы разрешить трафик 8443 на этот основной IP-адрес. Убедитесь, что источник имеет свой собственный доверенный IP-адрес, иначе все сможет подключиться.

После этого убедитесь, что вы можете подключиться к веб-конфигурации виртуальной машины с большим IP-адресом и войти с учетными данными, указанными во время развертывания виртуальной машины:

- При подключении из виртуальной машины во внутренней сети или через VPN подключитесь непосредственно к BIG-IPs основному IP-адресу и порту веб-конфигурации. Например, `https://<BIG-IP-VM_Primary_IP:8443`. Браузер выдаст запрос о небезопасном подключении, но вы можете пропустить запрос, пока не будет настроен крупный IP-адрес. Если браузер настаивает на блокировании доступа, очистите его кэш и повторите попытку.

- Если веб-конфигурация опубликована через прокси приложения, используйте URL-адрес, определенный для доступа к веб-конфигурации извне, без добавления порта, например `https://big-ip-vm.contoso.com` . Внутренний URL-адрес должен быть определен с помощью порта веб-конфигурации, например `https://big-ip-vm.contoso.com:8443` 

Система с большими IP-адресами также может управляться с помощью базовой среды SSH, которая обычно используется для задач командной строки (CLI) и доступа на корневом уровне. Существует несколько вариантов подключения к интерфейсу командной строки, в том числе:

- [Служба Azure бастиона](https://docs.microsoft.com/azure/bastion/bastion-overview): обеспечивает быстрое и безопасное подключение к любой виртуальной машине в виртуальной сети из любого расположения.

- Подключение напрямую через клиент SSH, например, с помощью JIT-метода

- Последовательная консоль: предлагается в нижней части раздела "поддержка и устранение неполадок" в меню "виртуальные машины" на портале. Передача файлов не поддерживается.

- Как и в случае с веб-конфигурацией, можно также подключиться непосредственно к CLI из Интернета, настроив основной IP-адрес с общедоступным IP-адресом и добавив правило NSG, чтобы разрешить трафик SSH. Опять же, не забудьте ограничить источник своим доверенным IP-адресом при использовании этого метода.  

## <a name="big-ip-license"></a>Лицензия с большим IP-адресом

Система больших IP-адресов должна быть активирована и подготовлена с помощью модуля APM, прежде чем ее можно будет настроить для служб публикации и SHA.

1. Снова войдите в веб-конфигурацию и на странице **Общие свойства** выберите **активировать** .

2. В поле **Базовый ключ регистрации** введите ключ с учетом регистра, предоставленный клавишей F5.

3. Оставьте для параметра **метод активации** значение **автоматически** и нажмите кнопку **Далее**, крупный IP-адрес проверит лицензию и отобразит лицензионное соглашение (EULA).

4. Выберите **принять** и дождитесь завершения активации, прежде чем выбрать **продолжить**.

5. Войдите снова и в нижней части страницы сводки лицензий нажмите кнопку **Далее**. Теперь в большом IP-адресе будет отображаться список модулей, которые предоставляют различные функции, необходимые для SHA. Если вы не видите это, на главной вкладке перейдите к разделу подготовка **системных**  >  **ресурсов**.

6. Проверка столбца подготовки для политики доступа (APM)

![На рисунке показана подготовка доступа](./media/f5ve-deployment-plan/access-provisioning.png)

7. Выберите **Отправить** и примите предупреждение.

8. Подождите и дождитесь, пока большой IP-адрес завершит работу новых функций. Перед полной инициализацией она может пройти несколько раз. 

9. При готовности выберите **продолжить** и на вкладке **о программе** выберите **запустить программу установки** .

>[!IMPORTANT]
>Лицензии F5 ограничены для использования одним экземпляром BIG-IP VE в любой момент времени. Возможны причины, по которым требуется перенести лицензию с одного экземпляра на другой. Если это сделать, то обязательно [Отмените](https://support.f5.com/csp/article/K41458656) лицензию на пробную версию на активном экземпляре перед списанием, в противном случае лицензия будет безвозвратно утрачена.

## <a name="provision-big-ip"></a>Предоставление больших IP-адресов

Обеспечение безопасности входящего и исходящего трафика управления BIG-IPs веб-конфигурации имеет первостепенное значение. Настройте сертификат управления устройствами, чтобы обеспечить защиту канала веб-конфигурации от компрометации.

1. На панели навигации слева последовательно выберите **система**  >  **Управление сертификатами**  >  **трафик Управление сертификатами**  >  **Импорт списка сертификатов SSL**  >   .

2. В раскрывающемся списке **Тип импорта** выберите **PKCS 12 (IIS)** и **выберите файл**. Поиск веб-сертификата SSL с именем субъекта или SAN, в котором будет располагаться полное доменное имя. Вы назначаете для виртуальной машины большой IP-адреса в следующих шагах.

3. Укажите пароль для сертификата, а затем выберите **Импорт** .

4. На панели навигации слева перейдите к **системной**  >  **платформе** .

5. Настройте для виртуальной машины с полным IP-адресом полное имя узла и часовой пояс для его среды, а затем — **Update** .

![На изображении показаны общие свойства](./media/f5ve-deployment-plan/general-properties.png)

6. На панели навигации слева перейдите к   >    >  **устройству** конфигурация системы  >  **NTP** .

7. Укажите надежный NTP-источник и нажмите кнопку **Добавить**, а затем — **Update**. Например `time.windows.com`.

Теперь для разрешения полного доменного имени BIG-IPs, указанного на предыдущих шагах, требуется запись DNS для его основного частного IP-адреса. Запись должна быть добавлена в внутреннюю службу DNS вашей среды или в файл localhost компьютера, который будет использоваться для подключения к веб-конфигурации с большим IP. В любом случае предупреждение браузера больше не должно отображаться при непосредственном подключении к веб-конфигурации. То есть не через прокси приложения или другой обратный прокси-сервер.

## <a name="ssl-profile"></a>Профиль SSL

В качестве обратного прокси-сервера система с большими IP-адресами может работать в качестве простой службы пересылки, иначе известной как прозрачный прокси-сервер или полный прокси-сервер, который активно участвует в обмене данными между клиентами и серверами.
Полный прокси-сервер создает два отдельных подключения: клиентское соединение TCP с отдельным внутренним соединением TCP-сервера, которое отделяется от мягкого разрыва в середине. Клиенты подключаются к прослушивателю прокси-сервера на одном конце, в ином случае известном как **виртуальный сервер**, и прокси устанавливает отдельное независимое подключение к внутреннему серверу. Это двунаправленный порядок по обеим сторонам.
В этом режиме полного прокси система F5 с большими IP-адресами может проверять трафик, поэтому также возможно взаимодействие с запросами и ответами. Некоторые функции, такие как балансировка нагрузки и оптимизация веб-производительности, а также расширенные службы управления трафиком, такие как безопасность уровня приложения, веб-ускорение, маршрутизация страниц и безопасный удаленный доступ, основываются на этих функциях.
При публикации служб на основе SSL процесс расшифровки и шифрования трафика между клиентами и серверными службами осуществляется с помощью профилей больших SSL на основе IP.

Существуют два типа профилей:

- **SSL клиента**. наиболее распространенным способом настройки системы с большим IP-адресом для публикации внутренних служб с помощью SSL является создание профиля SSL клиента. При использовании профиля SSL клиента система больших IP-адресов может расшифровывать входящие клиентские запросы перед их отправкой в подчиненную службу. Затем он шифрует исходящие ответы внутреннего сервера перед их отправкой клиентам.

- **SSL-сервер**. для внутренних служб, настроенных для HTTPS, можно настроить Big-IP для использования профиля SSL сервера. При использовании профиля SSL сервера большой IP-адрес повторно шифрует клиентский запрос перед его отправкой во внутреннюю серверную службу. Когда сервер возвращает зашифрованный ответ, система больших IP-адресов расшифровывает и повторно шифрует ответ перед его отправкой клиенту через настроенный профиль SSL клиента.

Подготовка профилей SSL для клиента и сервера будет иметь предварительно настроенный и готовый для всех сценариев SHA.

1. На панели навигации слева последовательно выберите **система**  >  **Управление сертификатами**  >  **трафик Управление сертификатами**  >  **Импорт списка сертификатов SSL**  >   .

2. В раскрывающемся списке **Тип импорта** выберите **PKCS 12 (IIS)** .

3. Укажите имя для импортированного сертификата, например  `ContosoWilcardCert`

4. Выберите **выбрать файл** , чтобы перейти к веб-сертификату SSL, имя субъекта которого соответствует суффиксу домена, который вы планируете использовать для опубликованных служб.

5. Укажите **пароль** для импортированного сертификата, а затем выберите **Импорт** .

6. На панели навигации слева выберите **локальный**  >  **профиль трафика профили**  >  **SSL**  >   и нажмите кнопку **создать** .

7. На странице **новый профиль SSL клиента** укажите уникальное понятное имя для нового профиля SSL клиента и убедитесь, что для родительского профиля задано значение **клиентссл** .

![На изображении показано обновление BIG-IP](./media/f5ve-deployment-plan/client-ssl.png)

8. Установите флажок дальнего права в строке **цепочка ключей сертификатов** и нажмите кнопку **Добавить** .

9. В трех раскрывающихся списках выберите сертификат с подстановочными знаками, импортированный без парольной фразы, а затем нажмите кнопку **Добавить**  >  **завершено**.

![На изображении отображается шаблон обновления BIG-IP contoso](./media/f5ve-deployment-plan/contoso-wildcard.png)

10. Повторите шаги 6-9, чтобы создать **Профиль сертификата сервера SSL**. На верхней ленте выберите **SSL**  >  **Server**  >  **CREATE**.

11. На странице **новый профиль SSL сервера** укажите уникальное понятное имя для нового профиля SSL сервера и убедитесь, что для родительского профиля задано значение **серверссл** .

12. Установите флажок справа для параметра сертификат и ключевые строки и в раскрывающемся списке выберите импортированный сертификат, а затем — **завершено**.

![На изображении показано обновление всех профилей серверов с большим IP-адресом](./media/f5ve-deployment-plan/server-ssl-profile.png)

>[!NOTE]
>Не отчаивайтесь, если вы не можете получить SSL-сертификат, вы можете использовать интегрированные самозаверяющие сертификаты SSL-сервера и клиента. В браузере отобразится ошибка сертификата.

Последний шаг при подготовке больших IP-адресов для SHA заключается в том, чтобы убедиться в том, что он способен искать ресурсы, которые его публикуют, а также службу каталогов, от которой он зависит. БОЛЬШОЙ IP-адрес имеет два источника разрешения имен, начиная с локального файла/.../Hosts, или если запись не найдена, система больших IP-адресов использует любую службу DNS, с которой она настроена. Метод файла hosts не применяется к узлам и пулам APM, использующим полное доменное имя.

1. В веб-конфигурации перейдите к разделу Конфигурация **системы**  >    >    >  **DNS** устройства.

2. В **списке DNS-сервер поиска** введите IP-адрес DNS-сервера окружений.

3. Выберите **Добавить**  >  **Обновление**

В качестве отдельного и необязательного шага можно использовать [конфигурацию LDAP](https://somoit.net/f5-big-ip/authentication-using-active-directory) для проверки подлинности администраторов с большими IP-адресами для AD, а не для управления локальными учетными записями с большими IP-адресами.

## <a name="update-big-ip"></a>Обновление больших IP-адресов

Вы должны обновить свой большой IP-адрес виртуальной машины, чтобы разблокировать все новые функции, описанные в [руководстве на основе сценариев](f5-aad-integration.md). Вы можете проверить версию системы ТМОС, наведя указатель мыши на имя узла BIG-IPs, в левом верхнем углу главной страницы. Рекомендуется выполнить версия 15. x и более поздней версии, получить из [загрузки F5](https://downloads.f5.com/esd/productlines.jsp). Используйте [руководство](https://support.f5.com/csp/article/K34745165) по обновлению основной системной ОС (тмос).

Если обновление основного ТМОС невозможно, то следует рассмотреть как минимум обновление только интерактивной конфигурации, выполнив следующие действия.

1. На главной вкладке в веб-конфигурации BIG-IP перейдите в раздел   >  **интерактивная конфигурация** Access.

2. На странице " **интерактивная конфигурация** " выберите **обновление интерактивной конфигурации** .

![На рисунке показано, как обновить крупный IP-адрес](./media/f5ve-deployment-plan/update-big-ip.png)

3. В диалоговом окне " **пошаговая Конфигурация обновления** " **выберите файл** для отправки скачанного пакета вариантов использования и нажмите кнопку **Отправить и установить** .

4. После завершения обновления нажмите кнопку **продолжить**.

## <a name="backup-big-ip"></a>Резервная копия BIG-IP

Теперь, когда система с большими IP-адресами подготовлена полностью, мы рекомендуем создать полную резервную копию своей конфигурации:

1. Последовательно выберите **системные**  >  **архивы**  >  **создать**

2. Укажите уникальное **имя файла** и включите **Шифрование** с помощью парольной фразы

3. Установите параметр **закрытых ключей** , чтобы **включить** также резервное копирование устройств и SSL-сертификаты.

4. Выберите " **Готово** " и дождитесь завершения процесса

5. Отобразится состояние операции, предоставляющее состояние резервного копирования. Нажмите кнопку **ОК**

6. Сохраните Архив с набором конфигурации пользователя (UCS) локально, выбрав ссылку резервной копии и нажав кнопку **скачать**.

В качестве дополнительного шага можно также создать резервную копию всего системного диска с помощью [моментальных снимков Azure](https://docs.microsoft.com/azure/virtual-machines/windows/snapshot-copy-managed-disk), что, в отличие от резервной копии веб-конфигурации, представит некоторые непредвиденные обстоятельства для тестирования между версиями тмос или откат к новой системе.

```PowerShell
# Install modules
Install-module Az
Install-module AzureVMSnapshots

# Authenticate to Azure
Connect-azAccount

# Set subscription by Id
Set-AzContext -SubscriptionId ‘<Azure_Subscription_ID>’

#Create Snapshot
New-AzVmSnapshot -ResourceGroupName '<E.g.contoso-RG>' -VmName '<E.g.BIG-IP-VM>'

#List Snapshots
#Get-AzVmSnapshot -ResourceGroupName ‘<E.g.contoso-RG>'

#Get-AzVmSnapshot -ResourceGroupName ‘<E.g.contoso-RG>' -VmName '<E.g.BIG-IP-VM>' | Restore-AzVmSnapshot -RemoveOriginalDisk 

```

## <a name="restore-big-ip"></a>Восстановление больших IP-адресов

Восстановление больших IP-адресов следует аналогичной процедуре резервного копирования и может использоваться для миграции конфигураций между виртуальными машинами с большими IP-адресами. Сведения о поддерживаемых вариантах обновления следует наблюдать перед импортом резервной копии.

1. К   >  **архивам** системы

2. Выберите ссылку резервной копии, которую вы хотите восстановить, или нажмите кнопку " **Отправить** ", чтобы перейти к ранее сохраненному архиву UCS, который отсутствует в списке

3. Укажите парольную фразу для резервного копирования и нажмите кнопку " **восстановить** ".

```PowerShell
# Authenticate to Azure
Connect-azAccount

# Set subscription by Id
Set-AzContext -SubscriptionId ‘<Azure_Subscription_ID>’

#Restore Snapshot
Get-AzVmSnapshot -ResourceGroupName '<E.g.contoso-RG>' -VmName '<E.g.BIG-IP-VM>' | Restore-AzVmSnapshot

```

>[!NOTE]
>На момент написания статьи командлет Азвмснапшот ограничивается восстановлением последнего моментального снимка на основе даты. Моментальные снимки хранятся в корне группы ресурсов виртуальной машины. Имейте в виду, что восстановление моментальных снимков перезапускает виртуальную машину Azure, в то же время отменяя это.

## <a name="additional-resources"></a>Дополнительные ресурсы

-   [Сброс пароля BIG-IP VE в Azure](https://clouddocs.f5.com/cloud/public/v1/shared/azure_passwordreset.html)
    -   [Сброс пароля без использования портала](https://clouddocs.f5.com/cloud/public/v1/shared/azure_passwordreset.html#reset-the-password-without-using-the-portal)

-   [Изменение сетевого адаптера, используемого для управления большими IP-адресами](https://clouddocs.f5.com/cloud/public/v1/shared/change_mgmt_nic.html)

-   [О маршрутах в одной конфигурации сетевого адаптера](https://clouddocs.f5.com/cloud/public/v1/shared/routes.html)

-   [Microsoft Azure: Waagent](https://clouddocs.f5.com/cloud/public/v1/azure/Azure_waagent.html)

## <a name="next-steps"></a>Дальнейшие действия

Выберите [сценарий развертывания](f5-aad-integration.md) и начните внедрение.