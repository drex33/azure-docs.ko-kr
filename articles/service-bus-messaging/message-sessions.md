---
title: Azure Service Bus 메시지 세션 | Microsoft Docs
description: 이 문서에서는 세션을 사용하여 관련 메시지의 무제한 시퀀스를 공동으로 순서를 지정하여 처리하는 방법을 설명합니다.
ms.topic: article
ms.date: 04/19/2021
ms.openlocfilehash: f3b6eae7b7f4d609df5067187595230aa6b86dba
ms.sourcegitcommit: aba63ab15a1a10f6456c16cd382952df4fd7c3ff
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/25/2021
ms.locfileid: "107987167"
---
# <a name="message-sessions"></a>메시지 세션
Microsoft Azure Service Bus 세션을 사용하면 관련 메시지의 무제한 시퀀스를 공동으로 순서를 지정하여 처리할 수 있습니다. 세션은 **FIFO(선입 선출)** 및 **요청-응답** 패턴으로 사용할 수 있습니다. 이 문서에서는 Service Bus를 사용할 때 세션을 사용하여 이러한 패턴을 구현하는 방법을 보여 줍니다. 

> [!NOTE]
> Service Bus의 기본 계층에서는 세션을 지원하지 않습니다. 표준 및 프리미엄 계층은 세션을 지원합니다. 이러한 계층 간의 차이점은 [Service Bus 가격 책정](https://azure.microsoft.com/pricing/details/service-bus/)을 참조하세요.

## <a name="first-in-first-out-fifo-pattern"></a>FIFO(선입 선출) 패턴
Service Bus에서 FIFO 보장을 실현하려면 세션을 사용합니다. Service Bus는 메시지 간 관계의 특징에 대한 규범이 아니며 메시지 시퀀스가 시작되거나 끝나는 위치를 결정하는 특정 모델을 정의하지 않습니다.

발신자는 메시지를 토픽이나 큐에 제출할 때 **세션 ID** 속성을 세션에 고유한 애플리케이션 정의 식별자로 설정하여 세션을 만들 수 있습니다. AMQP 1.0 프로토콜 수준에서 이 값은 *group-id* 속성에 매핑됩니다.

세션 인식 큐 또는 구독에서 세션 ID가 있는 메시지가 하나 이상일 때 해당 세션이 존재하게 됩니다. 세션이 존재하면 세션이 언제 만료되거나 사라지는지에 대해 정의된 시간이나 API가 없습니다. 이론적으로 메시지는 오늘 세션에서 수신되고 1년 후 다음 메시지가 수신될 수 있으며 세션 ID가 일치하면 세션은 Service Bus 측면에서 동일합니다.

하지만 일반적으로 애플리케이션에는 관련된 메시지 집합이 시작되고 끝나는 위치에 대한 명확한 개념이 있지만 Service Bus는 특정 규칙을 설정하지 않습니다. 예를 들어 애플리케이션은 첫 번째 메시지의 **Label** 속성을 **start** 로, 중간 메시지의 경우 **content** 로, 마지막 메시지의 경우 **end** 로 설정할 수 있습니다. 콘텐츠 메시지의 상대적인 위치는 **start** 메시지의 *SequenceNumber* 에서 현재 메시지의 *SequenceNumber* 델타로 계산될 수 있습니다.

> [!IMPORTANT]
> 큐 또는 구독에서 세션을 사용하는 경우 클라이언트 애플리케이션은 ***더 이상 일반 메시지를 주고받을 수 없습니다***. 모든 메시지는 세션 ID를 설정하여 세션의 일부로 전송되고 세션을 수락하여 수신되어야 합니다.

세션용 API는 큐 및 구독 클라이언트에 있습니다. 세션 및 메시지가 수신되는 시간을 제어하는 필수 모델과 수신 루프 관리의 복잡성을 숨기는 처리기 기반 모델이 있습니다. 

샘플은 [다음 단계](#next-steps) 섹션의 링크를 참조하세요. 

### <a name="session-features"></a>세션 기능

세션은 순차적 전달을 보존하고 보장하면서 인터리브된 메시지 스트림에 대한 동시 역멀티플렉싱을 제공합니다.

![세션 기능이 순차적 전달을 보존하는 방법을 보여 주는 다이어그램][1]

세션 수신기는 세션을 허용하는 클라이언트에서 생성됩니다. 클라이언트가 세션을 수락하고 보유하면 클라이언트는 큐 또는 구독에서 해당 세션의 **세션 ID** 를 사용하는 모든 메시지에 대해 배타적 잠금을 보유합니다. 또한 나중에 도착할 **세션 ID** 를 가진 모든 메시지에 대해 배타적 잠금을 유지합니다.

수신기에서 닫기 관련 메서드를 호출하거나 잠금이 만료되면 잠금이 해제됩니다. 수신기가 잠금을 갱신하는 메서드도 있습니다. 대신 자동 잠금 갱신 기능을 사용하여 잠금을 계속 갱신할 기간을 지정할 수 있습니다. 세션 잠금은 파일에 대한 배타적인 잠금으로 처리되어야 합니다. 즉, 애플리케이션은 세션이 더 이상 필요하지 않거나 더 이상 메시지를 기대하지 않는 경우 세션을 닫아야 합니다.

다수의 동시 수신자가 큐에서 풀링되면 특정 세션에 속하는 메시지는 해당 세션에 대한 잠금을 보유하고 있는 특정 수신자에게 전달됩니다. 이러한 작업을 통해 하나의 큐 또는 구독에 상주하는 인터리브된 메시지 스트림이 다른 수신자로 완전히 역멀티플렉싱되며 잠금 관리가 Service Bus 내의 서비스 쪽에서 발생하기 때문에 해당 수신자는 다른 클라이언트 시스템에서도 작동할 수 있습니다.

앞의 그림에는 3개의 동시 세션 수신기가 나와 있습니다. `SessionId` = 4인 한 세션에 활성, 소유 클라이언트가 없습니다. 즉, 이 특정 세션에서 전달된 메시지가 없습니다. 세션은 하위 큐와 같이 여러 가지 방법으로 작동합니다.

세션 수신자가 보유한 세션 잠금은 *peek-lock* 정산 모드에서 사용되는 메시지 잠금에 대한 우산입니다. 하나의 수신기자만 세션에 대해 잠금을 설정할 수 있습니다. 수신자에게는 진행 중인 많은 메시지가 있지만 메시지는 순서대로 수신됩니다. 메시지를 버리면 다음 수신 작업으로 동일한 메시지가 다시 제공됩니다.

### <a name="message-session-state"></a>메시지 세션 상태

워크플로가 대규모의 고가용성 클라우드 시스템에서 처리되는 경우 특정 세션과 연관된 워크플로 처리기는 예상치 못한 장애를 복구할 수 있어야 하며 작업이 시작된 다른 프로세스 또는 컴퓨터에서 부분적으로 완료된 작업을 재개할 수 있어야 합니다.

세션 상태 기능을 통해 broker 내부의 메시지 세션에 대한 애플리케이션 정의 주석을 사용할 수 있기 때문에 세션을 새 프로세서로 가져올 때 해당 세션에 대해 기록된 처리 상태를 즉시 사용할 수 있습니다.

Service Bus 측면에서 메시지 세션 상태는 한 개 메시지 크기의 데이터(Service Bus Standard의 경우 256KB 및 Service Bus Premium의 경우 1MB)를 보유할 수 있는 불투명한 이진 개체입니다. 세션과 관련된 처리 상태는 세션 상태 내에 유지되거나 이러한 정보를 보유하는 스토리지 위치 또는 데이터베이스 레코드를 세션 상태가 가리킬 수 있습니다.

세션 상태를 관리하는 메서드인 SetState 및 GetState는 세션 수신기 개체에서 찾을 수 있습니다. 이전에 세션 상태가 설정되지 않은 세션은 GetState에 대한 null 참조를 반환합니다. 이전에 설정된 세션 상태는 수신기의 SetState 메서드에 null을 전달하여 지울 수 있습니다.

세션의 모든 메시지를 사용한 경우에도 지우지 않는 한, 세션 상태는 유지됩니다(**null** 반환).

큐 또는 구독에 보관된 세션 상태는 해당 엔터티의 스토리지 할당량에 포함됩니다. 애플리케이션이 세션을 끝내면 외부 관리 비용을 피하기 위해 애플리케이션이 보유 상태를 정리하는 것이 좋습니다.

### <a name="impact-of-delivery-count"></a>배달 횟수가 미치는 영향

세션의 컨텍스트에서 메시지당 배달 횟수는 세션 부재 시와 약간 다르게 정의됩니다. 다음은 배달 횟수가 증가할 경우를 요약한 표입니다.

| 시나리오 | 메시지의 배달 횟수가 증가됩니다. |
|----------|---------------------------------------------|
| 세션이 수락되었지만 시간 초과로 인해 세션 잠금이 만료됩니다. | 예 |
| 세션이 수락되고, 세션이 잠겨 있는 경우에도 세션 내의 메시지가 완료되지 않으며 세션이 닫힙니다. | 예 |
| 세션이 수락되고 메시지가 완료된 후 세션이 명시적으로 닫힙니다. | 해당 없음(표준 흐름입니다. 이러한 메시지는 세션에서 제거되는 메시지입니다.) |

## <a name="request-response-pattern"></a>요청-응답 패턴
[요청-회신 패턴](https://www.enterpriseintegrationpatterns.com/patterns/messaging/RequestReply.html)은 발신자 애플리케이션에서 요청을 보내고 수신자가 발신자 애플리케이션으로 다시 응답을 보낼 수 있도록 하는 잘 설정된 통합 패턴입니다. 일반적으로 이 패턴에는 애플리케이션에서 응답을 보낼 수 있는 수명이 짧은 큐 또는 토픽이 필요합니다. 이 시나리오에서 세션은 유사한 의미 체계를 사용하는 간단한 대체 솔루션을 제공합니다. 

발신자 애플리케이션을 고유하게 식별하도록 특정 헤더 매개 변수가 설정된 상태로 여러 애플리케이션에서 단일 요청 큐로 요청을 보낼 수 있습니다. 수신자 애플리케이션은 세션 ID를 발신자가 요청 메시지에서 전송된 고유한 식별자로 설정하여 큐에 들어오는 요청을 처리하고 세션 지원 큐에서 회신을 보낼 수 있습니다. 그러면 요청을 보낸 애플리케이션이 특정 세션 ID에서 메시지를 수신하고 회신을 올바르게 처리할 수 있습니다.

> [!NOTE]
> 처음 요청을 보내는 애플리케이션은 세션 ID에 대해 알고 있어야 하고 이를 사용하여 응답이 예상되는 세션이 잠기도록 수락해야 합니다. 애플리케이션 인스턴스를 세션 ID로 고유하게 식별하는 GUID를 사용하는 것이 좋습니다. 특정 수신자가 응답을 잠그고 처리할 수 있도록 큐에 대한 세션 수신기에 지정된 시간 제한 또는 세션 처리기가 없어야 합니다.

## <a name="next-steps"></a>다음 단계
Azure portal, PowerShell, CLI, Resource Manager 템플릿, .NET, Java, Python 및 JavaScript를 사용하여 큐를 만드는 동안 메시지 세션을 사용하도록 설정할 수 있습니다. 자세한 내용은 [메시지 세션 사용](enable-message-sessions.md)을 참조하세요. 

선택한 언어로 샘플을 사용하여 Azure Service Bus 기능을 살펴봅니다. 

- [.NET용 Azure Service Bus 클라이언트 라이브러리 샘플(최신)](/samples/azure/azure-sdk-for-net/azuremessagingservicebus-samples/)
- [Java용 Azure Service Bus 클라이언트 라이브러리 샘플(최신)](/samples/azure/azure-sdk-for-java/servicebus-samples/)
- [Python용 Azure Service Bus 클라이언트 라이브러리 샘플](/samples/azure/azure-sdk-for-python/servicebus-samples/)
- [JavaScript용 Azure Service Bus 클라이언트 라이브러리 샘플](/samples/azure/azure-sdk-for-js/service-bus-javascript/)
- [TypeScript용 Azure Service Bus 클라이언트 라이브러리 샘플](/samples/azure/azure-sdk-for-js/service-bus-typescript/)

이전 .NET 및 Java 클라이언트 라이브러리에 대한 샘플은 다음을 참조하세요.
- [.NET용 Azure Service Bus 클라이언트 라이브러리 샘플(레거시)](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/)
- [Java용 Azure Service Bus 클라이언트 라이브러리 샘플(레거시)](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/azure-servicebus/MessageBrowse)

[1]: ./media/message-sessions/sessions.png

