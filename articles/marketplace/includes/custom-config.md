---
title: включить файл
description: файл
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: include
author: emuench
ms.author: mingshen
ms.date: 10/09/2020
ms.openlocfilehash: a1bc7cf1fd339ca3660c7b39326f37d2763c74b2
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92284830"
---
Если требуется дополнительная настройка, используйте запланированную задачу, запускаемую при запуске, чтобы внести окончательные изменения в виртуальную машину после ее развертывания. Учтите также следующие рекомендации:

- Если это задача однократного запуска, она должна удалять себя после успешного завершения.
- Конфигурации должны базироваться лишь на дисках C или D, так как всегда гарантируется наличие только этих двух дисков (диск C является диском операционной системы, а диск D — временным локальным диском).

Дополнительные сведения о настройке Linux см. в обзоре [расширений и компонентов виртуальной машины для Linux](../../virtual-machines/extensions/features-linux.md).