---
title: Справочник по атрибутам SAP SuccessFactors
description: Сведения о том, какие атрибуты SuccessFactors поддерживаются при подготовке c помощью решения по управлению персоналом SuccessFactors.
services: active-directory
author: cmmdesai
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: reference
ms.workload: identity
ms.date: 08/05/2020
ms.author: chmutali
ms.reviewer: celested
ms.openlocfilehash: ef2da377c7720cfb7b431d1ce0fed56656a2b8c7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "87808513"
---
# <a name="sap-successfactors-attribute-reference"></a>Справочник по атрибутам SAP SuccessFactors

В этой статье вы найдете сведения о:

- [Сущности и атрибуты SuccessFactors](#supported-successfactors-entities-and-attributes)
- [Сопоставление атрибутов по умолчанию](#default-attribute-mapping)

## <a name="supported-successfactors-entities-and-attributes"></a>Поддерживаемые сущности и атрибуты SuccessFactors

В следующей таблице приведен список атрибутов SuccessFactors, включаемых по умолчанию в следующие два приложения подготовки:

- [SuccessFactors Active Directory подготовки пользователей](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md)
- [SuccessFactors для подготовки пользователей Azure AD](../saas-apps/sap-successfactors-inbound-provisioning-cloud-only-tutorial.md)

Сведения о расширении схемы для дополнительных атрибутов см. в [справочнике по интеграции SAP SuccessFactors](./sap-successfactors-integration-reference.md#retrieving-additional-attributes) . 

| \# | Сущность SuccessFactors                  | Атрибут SuccessFactors     | Тип операции |
|----|----------------------------------------|------------------------------|----------------|
| 1  | перперсон                              | персонидекстернал             | Чтение           |
| 2  | перперсон                              | personId                     | Чтение           |
| 3  | перперсон                              | перперсонууид                | Чтение           |
| 4  | перперсонал                            | displayName                  | Чтение           |
| 5  | перперсонал                            | firstName                    | Чтение           |
| 6  | перперсонал                            | gender                       | Чтение           |
| 7  | перперсонал                            | lastName                     | Чтение           |
| 8  | перперсонал                            | middleName                   | Чтение           |
| 9  | перперсонал                            | преферреднаме                | Чтение           |
| 10 | Пользователь                                   | addressLine1                 | Чтение           |
| 11 | Пользователь                                   | addressLine2                 | Чтение           |
| 12 | Пользователь                                   | addressLIne3                 | Чтение           |
| 13 | Пользователь                                   | бусинессфоне                | Чтение           |
| 14 | Пользователь                                   | целлфоне                    | Чтение           |
| 15 | Пользователь                                   | city                         | Чтение           |
| 16 | Пользователь                                   | country                      | Чтение           |
| 17 | Пользователь                                   | custom01                     | Чтение           |
| 18 | Пользователь                                   | custom02                     | Чтение           |
| 19 | Пользователь                                   | custom03                     | Чтение           |
| 20 | Пользователь                                   | custom04                     | Чтение           |
| 21 | Пользователь                                   | custom05                     | Чтение           |
| 22 | Пользователь                                   | custom06                     | Чтение           |
| 23 | Пользователь                                   | custom07                     | Чтение           |
| 24 | Пользователь                                   | custom08                     | Чтение           |
| 25 | Пользователь                                   | custom09                     | Чтение           |
| 26 | Пользователь                                   | custom10                     | Чтение           |
| 27 | Пользователь                                   | custom11                     | Чтение           |
| 28 | Пользователь                                   | custom12                     | Чтение           |
| 29 | Пользователь                                   | custom13                     | Чтение           |
| 30 | Пользователь                                   | custom14                     | Чтение           |
| 31 | Пользователь                                   | empId                        | Чтение           |
| 32 | Пользователь                                   | homePhone                    | Чтение           |
| 33 | Пользователь                                   | жобфамили                    | Чтение           |
| 34 | Пользователь                                   | псевдоним                     | Чтение           |
| 35 | Пользователь                                   | state                        | Чтение           |
| 36 | Пользователь                                   | timeZone                     | Чтение           |
| 37 | Пользователь                                   | username                     | Чтение           |
| 38 | Пользователь                                   | zipCode                      | Чтение           |
| 39 | перфоне                               | ареакоде                     | Чтение           |
| 40 | перфоне                               | countryCode                  | Чтение           |
| 41 | перфоне                               | Расширение                    | Чтение           |
| 42 | перфоне                               | phoneNumber                  | Чтение           |
| 43 | перфоне                               | фонетипе                    | Чтение           |
| 44 | перемаил                               | emailAddress                 | Чтение и запись    |
| 45 | перемаил                               | емаилтипе                    | Чтение           |
| 46 | емпемплоймент                          | фирстдатеворкед              | Чтение           |
| 47 | емпемплоймент                          | ластдатеворкед               | Чтение           |
| 48 | емпемплоймент                          | userId                       | Чтение           |
| 49 | емпемплоймент                          | исконтинжентворкер           | Чтение           |
| 50 | емпжоб                                 | каунтрйофкомпани             | Чтение           |
| 51 | емпжоб                                 | емплстатус                   | Чтение           |
| 52 | емпжоб                                 | endDate                      | Чтение           |
| 53 | емпжоб                                 | startDate                    | Чтение           |
| 54 | емпжоб                                 | jobTitle                     | Чтение           |
| 55 | емпжоб                                 | position                     | Чтение           |
| 65 | емпжоб                                 | customString13               | Чтение           |
| 56 | емпжоб                                 | managerId                    | Чтение           |
| 57 | Емпжоб \. businessunit                   | businessUnit                 | Чтение           |
| 58 | Емпжоб \. businessunit                   | бусинессунитид               | Чтение           |
| 59 | \.Компания емпжоб                        | company                      | Чтение           |
| 60 | \.Компания емпжоб                        | companyId                    | Чтение           |
| 61 | Емпжоб \. \. каунтрйофрегистратион компании | твочаркаунтрикоде           | Чтение           |
| 62 | Емпжоб \. CostCenter                     | costCenter                   | Чтение           |
| 63 | Емпжоб \. CostCenter                     | костцентерид                 | Чтение           |
| 64 | Емпжоб \. CostCenter                     | костцентердескриптион        | Чтение           |
| 65 | Емпжоб \. отдел                     | department                   | Чтение           |
| 66 | Емпжоб \. отдел                     | departmentId                 | Чтение           |
| 67 | Подразделение Емпжоб \.                       | division                     | Чтение           |
| 68 | Подразделение Емпжоб \.                       | divisionId                   | Чтение           |
| 69 | Емпжоб \. жобкоде                        | жобкоде                      | Чтение           |
| 70 | Емпжоб \. жобкоде                        | жобкодеид                    | Чтение           |
| 71 | \.Расположение емпжоб                       | локатионнаме                 | Чтение           |
| 72 | \.Расположение емпжоб                       | оффицелокатионаддресс        | Чтение           |
| 73 | \.Расположение емпжоб                       | оффицелокатионЦити           | Чтение           |
| 74 | \.Расположение емпжоб                       | officeLocationCustomString4  | Чтение           |
| 75 | \.Расположение емпжоб                       | оффицелокатионзипкоде        | Чтение           |
| 76 | Емпжоб \. пайграде                       | пайграде                     | Чтение           |
| 77 | емпемплойменттерминатион               | активимплойментскаунт       | Чтение           |
| 78 | емпемплойменттерминатион               | латесттерминатиондате        | Чтение           |

## <a name="default-attribute-mapping"></a>Сопоставление атрибутов по умолчанию

В следующей таблице представлено сопоставление атрибутов по умолчанию между атрибутами SuccessFactors, перечисленными выше, и атрибутами AD или Azure AD. В колонке "сопоставление" для приложения подготовки Azure AD можно изменить это сопоставление по умолчанию, включив в него атрибуты из списка выше. 

| \# | Сущность SuccessFactors                  | Атрибут SuccessFactors | Сопоставление атрибутов AD или Azure AD по умолчанию   | Обработка комментария                                                                            |
|----|----------------------------------------|--------------------------|-----------------------------------------|----------------------------------------------------------------------------------------------|
| 1  | перперсон                              | персонидекстернал         | employeeId                              | Используется как атрибут сопоставления                                                                   |
| 2  | перперсон                              | перперсонууид            | \[Не сопоставлено \- , используемое в качестве привязки к источнику\] | Во время начальной синхронизации Служба подготовки связывает Персонууид с существующим Обжектгуид\.  |
| 3  | перперсонал                            | displayName              | displayName                             | Н/Д                                                                                           |
| 4  | перперсонал                            | firstName                | givenName                               | Н/Д                                                                                           |
| 5  | перперсонал                            | lastName                 | sn                                      | Н/Д                                                                                           |
| 6  | Пользователь                                   | addressLine1             | streetAddress                           | Н/Д                                                                                           |
| 7  | Пользователь                                   | city                     | l                                       | Н/Д                                                                                           |
| 8  | Пользователь                                   | country                  | co                                      | Н/Д                                                                                           |
| 9  | Пользователь                                   | state                    | st                                      | Н/Д                                                                                           |
| 10 | Пользователь                                   | username                 | samAccountName                          | Н/Д                                                                                           |
| 11 | Пользователь                                   | zipCode                  | postalCode                              | Н/Д                                                                                           |
| 12 | перемаил                               | emailAddress             | mail                                    | Н/Д                                                                                           |
| 13 | емпжоб                                 | jobTitle                 | title                                   | Н/Д                                                                                           |
| 14 | емпжоб                                 | managerId                | manager                                 | Н/Д                                                                                           |
| 15 | Емпжоб \. \. каунтрйофрегистратион компании | твочаркаунтрикоде       | c                                       | Н/Д                                                                                           |
| 16 | Емпжоб \. отдел                     | department               | department                              | Н/Д                                                                                           |
| 17 | Подразделение Емпжоб \.                       | division                 | company                                 | Н/Д                                                                                           |
| 18 | \.Расположение емпжоб                       | оффицелокатионаддресс    | streetAddress                           | Н/Д                                                                                           |
| 19 | \.Расположение емпжоб                       | оффицелокатионзипкоде    | postalCode                              | Н/Д                                                                                           |
| 20 | емпемплойменттерминатион               | активимплойментскаунт   | AccountEnabled                          | Если Активимплойментскаунт = 0, отключите аккаунт\.                                           |
