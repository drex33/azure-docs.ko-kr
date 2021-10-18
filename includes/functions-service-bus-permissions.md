---
author: mattchenderson
ms.service: azure-functions
ms.topic: include
ms.date: 10/08/2021
ms.author: mahender
ms.openlocfilehash: b4ce7e128304aa1befd7029ed2a692b1d7f391f3
ms.sourcegitcommit: 5361d9fe40d5c00f19409649e5e8fed660ba4800
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/18/2021
ms.locfileid: "130137405"
---
런타임에 토픽 및 큐에 대 한 액세스를 제공 하는 역할 할당을 만들어야 합니다. 역할 할당의 범위는 특정 큐 또는 토픽으로 제한 되지 않고 Service Bus 네임 스페이스에 대 한 것 이어야 합니다. [Owner](../articles/role-based-access-control/built-in-roles.md#owner) 와 같은 관리 역할은 충분 하지 않습니다. 다음 표에서는 정상적인 작업에서 Service Bus 확장을 사용할 때 권장 되는 기본 제공 역할을 보여 줍니다. 응용 프로그램에는 사용자가 작성 하는 코드에 따라 추가 사용 권한이 필요할 수 있습니다.

| 바인딩 유형   | 기본 제공 역할 예                                            |
|----------------|-------------------------------------------------------------------|
| 트리거        | [azure Service Bus 데이터 수신기], [azure Service Bus 데이터 소유자] |
| 출력 바인딩 | [Azure Service Bus 데이터 보내는 사람]                                   |

[Azure Service Bus 데이터 받는 사람]: ../articles/role-based-access-control/built-in-roles.md#azure-service-bus-data-receiver
[Azure Service Bus 데이터 보내는 사람]: ../articles/role-based-access-control/built-in-roles.md#azure-service-bus-data-sender
[Azure Service Bus 데이터 소유자]: ../articles/role-based-access-control/built-in-roles.md#azure-service-bus-data-owner
