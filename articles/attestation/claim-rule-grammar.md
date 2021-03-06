---
title: Грамматика правил утверждения в службе "Аттестация Azure"
description: Сведения об утверждениях политик и правилах утверждений в службе "Аттестация Azure".
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: 3ed5c3f8232047787c6f05628f1eef35a7533999
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91285424"
---
# <a name="claim-and-claim-rules"></a>Утверждения и правила утверждений

Чтобы разобраться в грамматике правил утверждения, сначала важно понять принцип утверждений политики аттестации.

## <a name="claim"></a>Утверждение

Утверждением называется набор свойств, которые сгруппированы для предоставления важной информации. В службе "Аттестация Azure" утверждение содержит следующие свойства:

- **type**. Строковое значение, представляющее тип утверждения.
- **value**: логическое, целочисленное или строковое значение, которое определяет значение этого утверждения.
- **valueType.** Тип данных, в котором хранится информация в свойстве value. Поддерживаются следующие типы: String, Integer и Boolean. Если значение не указано, по умолчанию используется String.
- **issuer.** Сведения об издателе этого утверждения. Издатель может относиться к одному из следующих типов:
  - **AttestationService.** Некоторые утверждения предоставляются авторам политики службой "Аттестация Azure", и они могут использовать их для создания нужных политик.
  - **AttestationPolicy.** Сама политика, определенная администратором, может добавлять утверждения во входящие свидетельства при их обработке. В этом случае в качестве издателя будет указано значение AttestationPolicy.
  - **CustomClaim.** Аттестующая сторона (клиент) также может добавлять утверждения в свидетельство аттестации. В этом случае в качестве издателя будет указано значение CustomClaim.

Если это значение не определено, будет использоваться значение по умолчанию (CustomClaim).

## <a name="claim-rule"></a>Правило утверждения

Набор входящих утверждений используется подсистемой обработки политик для вычисления результата аттестации. Правило утверждения представляет собой набор условий, которые используются для проверки входящих утверждений и выполнения определенного действия.

```
Conditions list => Action (Claim)
```

Оценка правила утверждений в службе "Аттестация Azure" включает следующие шаги:

- Если список условий отсутствует, выполняется действие с указанным утверждением. 
- В противном случае выполняется оценка условий в списке условий.
- Если для списка условий получено значение False, обработка останавливается. В противном случае обработка продолжается.

Условия в правиле утверждений применяются для определения, нужно ли выполнять действие. Список условий содержит последовательность условий, разделенных оператором "&&".

Список условий имеет следующую структуру:

```
Condition && Condition && ...
```

Каждое отдельное условие имеет следующую структуру:

```
Identifier:[ClaimPropertyCondition, ClaimPropertyCondition,…]
```

Список условий состоит из нескольких условий, применяемых к разным свойствам утверждения. Условие может иметь необязательный идентификатор, который можно использовать для создания ссылки на одно или несколько утверждений, соответствующих условию. Эту ссылку можно применять в других условиях или в действии этого правила.

Например.

```
F1:[type=="OSName" , issuer=="CustomClaim"] && 
[type=="OSName" , issuer=="AttestationService", value== F1.value ] 
=> issueproperty(type="report_validity_in_minutes", value=1440);

F1:[type=="OSName" , issuer=="CustomClaim"] && 
C2:[type=="OSName" , issuer=="AttestationService", value== F1.value ] 
=> issue(claim = C2);
```

Ниже приведены операторы, которые можно использовать для проверки условий.

| Тип значения | Поддерживаемые операции |
|--|--|
| Целое число | == (равно), \!= (не равно), <= (меньше или равно), < (меньше), >= (больше или равно), > (больше) |
| Строка | == (равно), \!= (не равно) |
| Логическое | == (равно), \!= (не равно) |

Оценка списка условий

- Использование оператора && означает, что список условий получает значение True только в том случае, если все условия из этого списка получают значение True.
- Каждое условие содержит критерии фильтрации для набора утверждений. Само условие получает значение True, если найдено хотя бы одно утверждение, удовлетворяющее указанному для него условию.
- Утверждение считается удовлетворяющим критерию фильтрации, которое указано в условии, если каждое из его свойств удовлетворяет условиям для соответствующего свойства утверждения, указанным в условии.  

Ниже описан набор действий, которые допускаются в политике.

| Команда действия | Описание | Разделы политики, к которым она применяется |
|--|--|--|
| permit() | Набор входящих утверждений можно использовать для вычисления **issuancerules**. Не принимает утверждения в качестве параметров. | **authorizationrules** |
| deny() | Набор входящих утверждений не следует использовать для вычислений **issuancerules**. Не принимает утверждения в качестве параметров. | **authorizationrules** |
| add(claim) | Добавляет утверждение в набор входящих утверждений. Любое утверждение, добавленное в набор входящих утверждений, становится доступно для всех последующих правил утверждения. |**authorizationrules**, **issuancerules** |
| issue(claim) | Добавляет утверждение в наборы входящих и исходящих утверждений. | **issuancerules** |
| issueproperty(claim) | Добавляет утверждение в наборы входящих утверждений и утверждений свойств. | **issuancerules**

## <a name="next-steps"></a>Дальнейшие действия

- [Как создать и подписать файл политики аттестации](author-sign-policy.md)
- [Настройка службы "Аттестация Azure" с помощью PowerShell](quickstart-powershell.md)

