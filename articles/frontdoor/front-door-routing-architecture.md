---
title: Передняя дверца Azure — Архитектура маршрутизации | Документация Майкрософт
description: В этой статье вы получите глобальное представление об архитектуре Front Door.
services: front-door
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: duau
ms.openlocfilehash: 948cf3c65dfdc912f2f807dfac34076985f1bc89
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91449198"
---
# <a name="routing-architecture-overview"></a>Общие сведения об архитектуре решений для маршрутизации

Когда передняя дверца Azure получает клиентские запросы, она выполняет одно из двух действий. Либо отвечает на них, если вы включаете кэширование, либо перенаправляете их в соответствующую серверную часть приложения в качестве обратных прокси-серверов.

## <a name="selecting-the-front-door-environment-for-traffic-routing-anycast"></a><a name = "anycast"></a>Выбор среды Front Door для маршрутизации трафика (произвольная передача)

Трафик, находящийся в средах передней дверцы Azure [, использует](https://en.wikipedia.org/wiki/Anycast) незаполненное распространение для трафика DNS (доменное имя) и HTTP (протокол передачи гипертекста), что позволяет запросам пользователей достичь ближайшей среды в наименьшем количестве прыжков сети. Эта архитектура обеспечивает лучшее время кругового пути для конечных пользователей, увеличивая преимущества разбиения TCP. Front Door упорядочивает среды в первичные и резервные "кольца". Во внешнем кольце находятся среды, которые ближе к пользователям, что обеспечивает низкую задержку.  Внутреннее кольцо имеет среды, которые могут выполнять отработку отказа для среды внешнего кольца в случае возникновения любых проблем. Внешнее кольцо является предпочтительным целевым объектом для всего трафика, и внутреннее кольцо заключается в обработке переполнения трафика от внешнего кольца. Каждый интерфейсный узел или домен, обслуживаемый передней дверце, получает первичный виртуальный IP-адрес (адреса виртуальных IP-адресов), который объявляется средами как в внутреннем, так и внешнем кольцах. Резервный виртуальный IP-адрес объявляется только окружениями внутреннего кольца. 

Эта архитектура гарантирует, что запросы от конечных пользователей всегда попадают в ближайшую среду с передней дверцей. Даже если предпочтительная среда с передней дверцей является неработоспособной, весь трафик автоматически переместится в следующую ближайшую среду.

## <a name="connecting-to-front-door-environment-split-tcp"></a><a name = "splittcp"></a>Подключение к среде Front Door (разделенный протокол TCP)

[Разделенный протокол TCP](https://en.wikipedia.org/wiki/Performance-enhancing_proxy) представляет собой метод сокращения задержек и проблем с протоколом TCP путем разделения подключения, которое иначе привело бы к большому времени кругового пути, на более мелкие части. В средах с передней дверцей ближе к конечным пользователям, TCP-подключения завершаются в среде с передней дверцей. Подключение TCP с большим временем приема-передачи (RTT) на серверную часть приложения разделяется на два отдельных соединения. "Короткое подключение" между конечным пользователем и средой передней дверцы означает, что соединение устанавливается на три коротких обращения вместо трех длинных циклов обработки, что приводит к экономии задержек. "Длинное подключение" между средой передней дверцы и серверной частью можно предварительно установить, а затем повторно использовать в других запросах пользователей, которые сохраняют время подключения. Результат разбиения TCP умножается при установлении подключения SSL/TLS (безопасность транспортного уровня), так как для защиты соединения требуется больше обращений.

## <a name="processing-request-to-match-a-routing-rule"></a>Обработка запроса для сопоставления с правилом маршрутизации
После установления соединения и завершения подтверждения TLS первым шагом после запроса в среде с передней дверцей будет сопоставление с правилом маршрутизации. Сопоставление определяется конфигурациями на передней дверце, к которой конкретное правило маршрутизации соответствует запросу. Ознакомьтесь с тем, как Front Door выполняет [сопоставление маршрутов](front-door-route-matching.md).

## <a name="identifying-available-backends-in-the-backend-pool-for-the-routing-rule"></a>Определение доступных внутренних серверов во внутреннем пуле для правила маршрутизации
После того, как передняя дверца соответствует правилу маршрутизации для входящего запроса, следующим шагом является получение состояния проверки работоспособности для серверного пула, связанного с правилом маршрутизации, если кэширование отсутствует. Узнайте о том, как Front Door контролирует работоспособность серверной части с помощью [проб работоспособности](front-door-health-probes.md).

## <a name="forwarding-the-request-to-your-application-backend"></a>Перенаправление запроса в серверную часть приложения
Наконец, если кэширование не настроено, запрос пользователя перенаправляется на "лучший" серверную часть в зависимости от конфигурации [метода маршрутизации](front-door-routing-methods.md) .

## <a name="next-steps"></a>Дальнейшие действия

- Дополнительные сведения о [создании Front Door](quickstart-create-front-door.md).
