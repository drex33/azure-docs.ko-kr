---
title: Azure Event Hubs - .NET 예외
description: 이 문서에서는 Azure Event Hubs .NET 메시징 예외 및 제안된 작업의 목록을 제공합니다.
services: event-hubs
documentationcenter: na
author: spelluru
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.custom: seodec18
ms.date: 09/23/2021
ms.author: spelluru
ms.openlocfilehash: e3f37267bcf5c4dd6248cc9498ac0cc84ea91a75
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129210310"
---
# <a name="eventhubsexception---net"></a>EventHubsException - .NET
**EventHubsException** 은 서비스 내의 오류와 클라이언트 관련 오류를 포함하여 Event Hubs 관련 작업에 문제가 발생한 경우에 트리거됩니다. 

## <a name="exception-information"></a>예외 정보
예외에는 오류 컨텍스트 및 상대적 심각도를 이해하는 데 도움이 되는 다음과 같은 컨텍스트 정보가 포함됩니다. 

- **IsTransient**: 예외가 복구 가능한 것으로 간주되는지 여부를 식별합니다. 일시적인 것으로 간주되는 경우에는 적절한 재시도 정책이 이미 적용되었으며 재시도에 실패한 것입니다.
- **이유**: 근본 원인을 범주화하고 명확하게 설명하는 데 도움이 되는 잘 알려진 실패 이유를 제공합니다. 이러한 이유는 예외 메시지의 텍스트를 검사하는 것이 적합하지 않을 때 예외 필터링 및 기타 논리를 적용할 수 있도록 하기 위한 것입니다. 몇 가지 주요 실패 이유는 다음과 같습니다.
    - **클라이언트 종료**: Event Hubs 클라이언트가 이미 종료되었거나 삭제된 경우 발생합니다. Event Hubs 클라이언트 라이브러리의 개체가 원하는 범위에서 만들어지고 닫히도록 애플리케이션 코드를 확인하는 것이 좋습니다.
    - **서비스 시간 초과**: Event Hubs 서비스가 예상된 시간 내에 작업에 응답하지 않았음을 나타냅니다. 이 문제는 일시적인 네트워크 문제 또는 서비스 문제로 인해 발생했을 수 있습니다. Event Hubs 서비스가 요청을 성공적으로 완료했거나 완료하지 못했을 수 있습니다. 상태는 알려져 있지 않습니다. 현재 상태를 확인하고 필요한 경우 다시 시도하는 것이 좋습니다.
    - **할당량 초과**: 단일 소비자 그룹에 대한 활성 읽기 작업이 너무 많음을 나타냅니다. 이 제한은 Event Hubs 네임스페이스의 계층에 따라 달라지며 상위 계층으로 이동하는 것이 필요할 수 있습니다. 또 다른 방법은 추가 소비자 그룹을 만들고 모든 그룹에 대한 소비자 클라이언트 읽기 수가 제한 내에 있는지 확인하는 것입니다. 자세한 내용은 [Azure Event Hubs 할당량 및 제한](event-hubs-quotas.md)을 참조하세요.
    - **메시지 크기 초과**: 개별 이벤트와 이벤트 배치 모두에 허용되는 최대 크기인 이벤트 데이터입니다. 여기에는 이벤트의 데이터와 연결된 메타데이터 및 시스템 오버헤드가 포함됩니다. 이 오류를 해결하려면 일괄적으로 전송된 이벤트 수를 줄이거나 메시지에 포함된 데이터의 크기를 줄이십시오. 크기 제한은 변경될 수 있으므로 세부 사항은 [Azure Event Hubs 할당량 및 제한](event-hubs-quotas.md)을 참조하세요.
    - **소비자 연결 끊김**: Event Hubs 서비스에 의해 Event Hubs 인스턴스에서 소비자 클라이언트의 연결이 끊어졌습니다. 일반적으로 소유자 수준이 높은 소비자가 파티션 및 소비자 그룹 페어링에 대한 소유권을 어설션하는 경우에 발생합니다.
    - **리소스 찾을 수 없음**: Event Hubs 서비스에서 이벤트 허브, 소비자 그룹, 파티션 등의 리소스를 찾을 수 없습니다. 삭제되었거나 Event Hubs 서비스 자체에 문제가 있는 것일 수 있습니다.

## <a name="handling-exceptions"></a>예외 처리
여러 가지 방법으로 **EventHubException** 에 대한 특정 실패 이유에 대응할 수 있습니다. 한 가지 방법은 catch 블록의 일부로 예외 필터 절을 적용하는 것입니다.

```csharp
try
{
    // Read events using the consumer client
}
catch (EventHubsException ex) where 
    (ex.Reason == EventHubsException.FailureReason.ConsumerDisconnected)
{
    // Take action based on a consumer being disconnected
}
```

## <a name="next-steps"></a>다음 단계
[레거시 문서](event-hubs-messaging-exceptions.md)에서 설명하는 기타 예외가 있습니다. 그 중 일부는 레거시 Event Hubs .NET 클라이언트 라이브러리에만 적용됩니다.