---
author: mattchenderson
ms.service: azure-functions
ms.topic: include
ms.date: 10/08/2021
ms.author: mahender
ms.openlocfilehash: b0451d7a3f14002ec53cdb410144c6e69f1337a9
ms.sourcegitcommit: 5361d9fe40d5c00f19409649e5e8fed660ba4800
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/18/2021
ms.locfileid: "130137391"
---
런타임 시 이벤트 허브에 대 한 액세스를 제공 하는 역할 할당을 만들어야 합니다. 역할 할당의 범위는 이벤트 허브 자체가 아닌 Event Hubs 네임 스페이스에 대 한 것 이어야 합니다. [Owner](../articles/role-based-access-control/built-in-roles.md#owner) 와 같은 관리 역할은 충분 하지 않습니다. 다음 표에서는 정상적인 작업에서 Event Hubs 확장을 사용할 때 권장 되는 기본 제공 역할을 보여 줍니다. 응용 프로그램에는 사용자가 작성 하는 코드에 따라 추가 사용 권한이 필요할 수 있습니다.

| 바인딩 유형   | 기본 제공 역할 예                                          |
|----------------|-----------------------------------------------------------------|
| 트리거        | [Azure Event Hubs 데이터 수신기], [Azure Event Hubs 데이터 소유자] |
| 출력 바인딩 | [Azure Event Hubs 데이터 보내는 사람]                                  |

[Azure Event Hubs 데이터 받는 사람]: ../articles/role-based-access-control/built-in-roles.md#azure-event-hubs-data-receiver
[Azure Event Hubs 데이터 보내는 사람]: ../articles/role-based-access-control/built-in-roles.md#azure-event-hubs-data-sender
[Azure Event Hubs 데이터 소유자]: ../articles/role-based-access-control/built-in-roles.md#azure-event-hubs-data-owner