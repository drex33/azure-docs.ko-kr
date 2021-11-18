---
title: Azure Service Bus - 메시지 만료
description: 이 문서에서는 Azure Service Bus 메시지의 만료 및 TTL(Time to Live)에 대해 설명합니다. 해당 최종 기한 이후에는 메시지가 더 이상 전달되지 않습니다.
ms.topic: conceptual
ms.date: 11/17/2021
ms.openlocfilehash: 8d9e9a1f19c69ee7951b914c182e87f4a1dde18f
ms.sourcegitcommit: 1244a72dbec39ac8cf16bb1799d8c46bde749d47
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2021
ms.locfileid: "132759234"
---
# <a name="azure-service-bus---message-expiration-time-to-live"></a>Azure Service Bus - 메시지 만료(Time to Live)
메시지의 페이로드 또는 메시지가 수신기에 전달하는 명령이나 조회는 거의 항상 특정 형식의 애플리케이션 수준 만료 기한을 따릅니다. 이러한 기간 후에는 콘텐츠가 더 이상 배달되지 않거나 요청된 작업이 더 이상 실행되지 않습니다.

큐 및 토픽이 주로 애플리케이션 또는 애플리케이션 부분의 부분 실행 컨텍스트에서 사용되는 개발 및 테스트 환경에서는 다음 테스트 실행이 새로 시작될 수 있게 표준 테스트 메시지를 자동으로 가비지 수집되도록 할 수 있습니다.

> [!NOTE]
> Service Bus 개념에 아직 익숙하지 않은 경우 Service Bus [개념](service-bus-messaging-overview.md#concepts)및 [Service Bus 큐, 토픽 및 구독을 참조하세요.](service-bus-queues-topics-subscriptions.md)

모든 개별 메시지에 대한 만료는 상대적 기간을 지정하는 **time-to-live** 시스템 속성을 설정하여 제어할 수 있습니다. 만료는 메시지가 엔터티 큐에 추가될 때 절대 인스턴스가 됩니다. 이때 **expires-at-utc** 속성은 **enqueued-time-utc** + **time-to-live** 값을 취합니다. broker 저장 메시지의 TTL(Time to Live) 설정은 현재 수신 중인 클라이언트가 없는 경우 강제 적용되지 않습니다.

**expires-at-utc** 인스턴트 후에 메시지는 검색할 수 없게 됩니다. 전달이 잠겨 있는 메시지는 만료의 영향을 받지 않습니다. 이러한 메시지는 여전히 정상적으로 처리됩니다. 잠금이 만료되거나 메시지가 중단되면 만료가 즉시 적용됩니다. 메시지가 잠금 상태인 경우 애플리케이션이 만료된 메시지를 소유하고 있을 수 있습니다. 애플리케이션이 메시지를 계속 처리하려고 할지 또는 메시지를 중단하도록 선택할지는 구현자가 결정합니다.

밀리초 또는 초 순서가 매우 낮은 TTL은 수신자 애플리케이션이 메시지를 받기 전에 만료될 수 있습니다. 애플리케이션에서 작동하는 가장 높은 TTL을 고려합니다.

## <a name="entity-level-expiration"></a>엔터티 수준 만료
큐 또는 토픽으로 전송되는 모든 메시지는 엔터티 수준에서 설정된 기본 만료 대상입니다. 만드는 동안 포털에서 설정하고 나중에 조정할 수 있습니다. time-to-live가 명시적으로 설정되지 않은 엔터티로 보낸 모든 메시지에는 기본 만료가 사용됩니다. 기본 만료는 time-to-live 값의 최대값으로도 사용됩니다. time-to-live 만료가 기본값보다 더 긴 메시지는 큐에 추가되기 전에 자동으로 메시지 time-to-live 기본값으로 조정됩니다.

> [!NOTE]
> - 조정된 메시지에 대한 기본 time-to-live 값은 달리 지정되지 않은 경우 서명된 64비트 정수에 사용할 수 있는 최대값입니다.
> - 메시징 엔터티(큐 및 토픽)의 경우 기본 만료 시간은 [Service Bus 표준 및 프리미엄](service-bus-premium-messaging.md) 계층의 부호 있는 64비트 정수에 대해 가능한 가장 큰 값입니다. **Basic 계층의** 경우 기본(최대) 만료 시간은 **14일** 입니다.
> - 항목이 구독보다 작은 TTL을 지정하면 항목 TTL이 적용됩니다.

만료된 메시지는 [배달하지 못한 편지 큐](service-bus-dead-letter-queues.md)로 이동하는 옵션이 있습니다. 프로그래매틱 방식이나 Azure Portal를 사용하여 이 설정을 구성할 수 있습니다. 이 옵션을 사용하지 않도록 설정하면 만료된 메시지가 삭제됩니다. 배달 못한 메시지 큐로 이동되는 만료된 메시지는 브로커가 사용자 속성 섹션에 저장하는 [dead-letter reason](service-bus-dead-letter-queues.md#moving-messages-to-the-dlq) 속성을 평가하여 다른 배달 못한 메시지와 구분할 수 있습니다. 

잠금 상태에서 메시지가 만료되지 않도록 보호되고 엔터티에 플래그가 설정된 경우 잠금이 중단되거나 만료될 때 메시지가 데드 문자 큐로 이동됩니다. 그러나 명목상으로 만료되었더라도 메시지가 성공적으로 확인되고 애플리케이션이 성공적으로 처리했다고 가정되면 이 위치로 이동되지 않습니다. 메시지 잠금 및 확인에 대한 자세한 내용은 [메시지 전송, 잠금 및 해결을 참조하세요.](message-transfers-locks-settlement.md)

time-to-live 및 만료 시 자동(및 트랜잭션) 배달 못한 메시지 큐는 마감일 내에 처리기 또는 처리기 그룹에 지정된 작업이 마감일 도달 시 처리를 위해 검색될지 여부를 확실히 설정할 수 있는 유용한 도구입니다.

예를 들어 확장이 제한된 백 엔드에서 작업을 안정적으로 실행해야 하며, 경우에 따라 트래픽 급증이 발생하거나 해당 백 엔드 가용성의 영향을 받지 않도록 하려는 웹 사이트가 있다고 가정해보겠습니다. 일반적인 경우 제출된 사용자 데이터에 대한 서버 쪽 처리기가 해당 정보를 큐에 밀어 넣고, 이후에 트랜잭션을 회신 큐로 성공적으로 처리했음을 확인하는 응답을 수신하게 됩니다. 트래픽이 급증하고 백 엔드 처리기가 시간 안에 해당 백로그 항목을 처리할 수 없는 경우 만료된 작업은 배달 못한 메시지 큐에 반환됩니다. 대화형 사용자에게 요청된 작업이 평소보다 약간 더 오래 걸린다고 알릴 수 있으며, 요청은 처리 경로의 다른 큐에 배치될 수 있습니다. 이 경우 최종 처리 결과는 전자 메일로 사용자에게 전송됩니다. 


## <a name="temporary-entities"></a>임시 엔터티

Service Bus 큐, 토픽 및 구독을 임시 엔터티로 생성할 수 있습니다. 이러한 항목은 지정된 기간 동안 사용되지 않으면 자동으로 제거됩니다.
 
자동 정리 기능은 테스트 또는 디버깅 실행이 중단되기 때문에 엔터티가 동적으로 생성되고 사용 후에 정리되지 않는 개발 및 테스트 시나리오에 유용합니다. 또한 애플리케이션이 응답 큐와 같은 동적 엔터티를 만들어 응답을 웹 서버 프로세스로 다시 받거나 개체 인스턴스가 사라질 때 해당 엔터티를 안정적으로 정리하기 어려운 비교적 수명이 짧은 다른 개체로 보낼 때 유용합니다.

이 기능은 네임스페이스의 **유휴 상태에서 자동 삭제** 속성을 사용하여 설정됩니다. 이 속성은 엔터티가 자동으로 삭제되기 전에 유휴(사용되지 않은) 기간으로 설정됩니다. 이 속성에 대한 최소값은 5분입니다.
 
## <a name="idleness"></a>끊김

엔터티(큐, 토픽 및 구독) 끊김 시 고려할 사항입니다.

| 엔터티 | 유휴 상태로 간주되는 내용 | 
| ------ | ---------------------- | 
| 큐 | <ul><li>전송 없음</li><li>수신 없음</li><li>큐 업데이트 없음</li><li>예약된 메시지 없음</li><li>Browse/Peek 없음</li> |
| 항목 | <ul><li>전송 없음</li><li>토픽 업데이트 없음</li><li>예약된 메시지 없음</li><li>토픽의 구독에 대한 작업 없음(다음 행 참조)</li></ul> |
| 구독 | <ul><li>수신 없음</li><li>구독 업데이트 없음</li><li>구독에 추가된 새 규칙 없음</li><li>Browse/Peek 없음</li></ul> |

## <a name="sdks"></a>SDK

- 메시지에서 Time-to-Live를 [설정하려면. .NET,](/dotnet/api/azure.messaging.servicebus.servicebusmessage.timetolive) [Java,](/java/api/com.azure.messaging.servicebus.servicebusmessage.settimetolive) [Python](/python/api/azure-servicebus/azure.servicebus.servicebusmessage), [JavaScript](/javascript/api/@azure/service-bus/servicebusmessage#timeToLive)
- 큐에서 기본 Time-to-Live를 [설정하려면.NET,](/dotnet/api/azure.messaging.servicebus.administration.createqueueoptions.defaultmessagetimetolive) [Java,](/java/api/com.azure.messaging.servicebus.administration.models.createqueueoptions.setdefaultmessagetimetolive) [Python](/python/api/azure-servicebus/azure.servicebus.management.queueproperties), [JavaScript](/javascript/api/@azure/service-bus/queueproperties#defaultMessageTimeToLive)
- 토픽에서 기본 Time-to-Live를 [설정하려면. .NET,](/dotnet/api/azure.messaging.servicebus.administration.createtopicoptions.defaultmessagetimetolive) [Java,](/java/api/com.azure.messaging.servicebus.administration.models.createtopicoptions.setdefaultmessagetimetolive) [Python](/python/api/azure-servicebus/azure.servicebus.management.topicproperties), [JavaScript](/javascript/api/@azure/service-bus/topicproperties#defaultMessageTimeToLive)
- 구독에서 기본 Time-to-Live를 설정하려면 [.NET,](/dotnet/api/azure.messaging.servicebus.administration.createsubscriptionoptions.defaultmessagetimetolive) [Java,]() [Python,]() [JavaScript,](/java/api/com.azure.messaging.servicebus.administration.models.createsubscriptionoptions.setdefaultmessagetimetolive) [Python](/python/api/azure-servicebus/azure.servicebus.management.subscriptionproperties), [JavaScript](/javascript/api/@azure/service-bus/subscriptionproperties)
 


## <a name="next-steps"></a>다음 단계

Service Bus 메시징에 대해 자세히 알아보려면 다음 문서를 참조하세요. 

- [메시지 시퀀싱 및 타임스탬프를](message-sequencing.md)
- [메시지, 페이로드 및 serialization](service-bus-messages-payloads.md)
- [메시지 세션](message-sessions.md)
- [중복 메시지 검색](duplicate-detection.md)
- [토픽 필터](topic-filters.md)
- [메시지 찾아보기](message-browsing.md)
- [메시지 전송, 잠금 및 확인](message-transfers-locks-settlement.md)
- [배달하지 못한 편지 큐](service-bus-dead-letter-queues.md)
- [메시지 지연](message-deferral.md)
- [메시지 프리 페치](service-bus-prefetch.md)
- [자동 전달 메시지](service-bus-auto-forwarding.md)
- [트랜잭션 지원](service-bus-transactions.md)
- [지리적 재해 복구](service-bus-geo-dr.md)
- [비동기 메시징 패턴 및 고가용성](service-bus-async-messaging.md)
- [중단 및 재해 처리](service-bus-outages-disasters.md)
- [제한](service-bus-throttling.md)