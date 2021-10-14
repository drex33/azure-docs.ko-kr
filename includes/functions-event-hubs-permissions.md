---
author: mattchenderson
ms.service: azure-functions
ms.topic: include
ms.date: 10/08/2021
ms.author: mahender
ms.openlocfilehash: 91ed9e6b767b4e8a9b482d937cf308684578b646
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129993317"
---
런타임에 이벤트 허브에 대한 액세스를 제공하는 역할 할당을 만들어야 합니다. [소유자와](../articles/role-based-access-control/built-in-roles.md#owner) 같은 관리 역할로는 충분하지 않습니다. 다음 표에서는 정상 작업에서 Event Hubs 확장을 사용할 때 권장되는 기본 제공 역할을 보여 드립니다. 애플리케이션에 작성하는 코드에 따라 추가 권한이 필요할 수 있습니다.

| 바인딩 유형   | 기본 제공 역할 예                                          |
|----------------|-----------------------------------------------------------------|
| 트리거        | [Azure Event Hubs 데이터 수신기,] [Azure Event Hubs 데이터 소유자] |
| 출력 바인딩 | [Azure Event Hubs 데이터 보내는 사람]                                  |

[Azure Event Hubs 데이터 받는 사람]: ../articles/role-based-access-control/built-in-roles.md#azure-event-hubs-data-receiver
[Azure Event Hubs 데이터 보내는 사람]: ../articles/role-based-access-control/built-in-roles.md#azure-event-hubs-data-sender
[Azure Event Hubs 데이터 소유자]: ../articles/role-based-access-control/built-in-roles.md#azure-event-hubs-data-owner