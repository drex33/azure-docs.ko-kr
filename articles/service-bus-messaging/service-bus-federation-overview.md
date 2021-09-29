---
title: 메시지 복제 및 지역 간 페더레이션 - Azure Service Bus | Microsoft Docs
description: 이 문서에서는 Azure Service Bus를 사용하여 이벤트 복제 및 지역 간 페더레이션에 대한 개요를 제공합니다.
ms.topic: article
ms.date: 09/28/2021
ms.openlocfilehash: 0f0d2b5f96ea65a6d0fd0f5664ef98948dc46c19
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129211870"
---
# <a name="message-replication-and-cross-region-federation"></a>메시지 복제 및 지역 간 페더레이션

네임스페이스 내에서 Azure Service Bus는 [자동 전달을 사용하여 체인 큐 및 항목 구독의 토폴로지 만들기](service-bus-auto-forwarding.md)를 지원하여 다양한 라우팅 패턴 구현이 허용됩니다. 예를 들어, 파트너에게 사용 권한을 주거나 받고 필요한 경우 일시 중단할 수 있는 전용 큐를 제공할 수 있으며, 애플리케이션에 전용으로 사용되는 다른 엔터티에 유연하게 연결할 수 있습니다. 또한 복잡한 다단계 라우팅 토폴로지를 만들거나 사서함 스타일 큐를 만들어 큐와 같은 항목의 구독을 드레이닝하고 구독자당 추가 스토리지 용량을 허용할 수 있습니다. 

또한 많은 정교한 솔루션에서는 이러한 패턴과 기타 패턴을 구현하기 위해 네임스페이스 경계를 넘어 메시지를 복제해야 합니다. 여러 다른 애플리케이션 테넌트와 연결된 네임스페이스 간에 또는 여러 다른 Azure 지역 간에 메시지를 전달해야 할 수 있습니다. 

솔루션은 각기 다른 지역에서 여러 Service Bus 네임스페이스를 유지하고, 큐와 항목 간에 메시지를 복제하며, [Azure Event Hubs](../event-hubs/event-hubs-about.md), [Azure IoT Hub](../iot-fundamentals/iot-introduction.md) 또는 [Apache Kafka](https://kafka.apache.org)와 같은 원본 및 대상과 메시지를 교환합니다. 

이러한 시나리오는 이 문서에서 중점적으로 다룹니다. 

## <a name="federation-patterns"></a>페더레이션 패턴

큐 또는 항목과 같은 Service Bus 엔터티 간에 또는 Service Bus와 다른 원본 및 대상 간에 메시지를 이동해야 하는 이유에는 여러 잠재적인 동기가 있습니다. 

[Event Hubs](../service-bus-messaging/service-bus-federation-overview.md)에 대한 유사한 패턴 집합에 비해 메시지 큐는 단일 메시지에 대한 소비자 독점 소유권을 약속하고, 메시지 배달 시 도착 순서를 유지하며, 브로커가 [경쟁 소비자](/azure/architecture/patterns/competing-consumers) 간에 메시지 배포를 공정하게 조정하기 때문에 큐와 같은 엔터티에 대한 페더레이션이 더욱 복잡합니다. 

[CAP 정리](https://en.wikipedia.org/wiki/CAP_theorem)의 제약 조건을 비롯한 여러 지역에서 동시에 사용할 수 있는 큐의 통합된 뷰를 제공하기 어렵고, 지역적으로 분산된 [경쟁 소비자](/azure/architecture/patterns/competing-consumers)가 메시지의 독점 소유권을 가질 수 있도록 하는 실질적인 제한이 있습니다. 이렇듯 지리적으로 분산된 큐는 소비자가 메시지를 사용하려면 메시지뿐 아니라 모든 메시지의 배달 상태도 완전히 일관되게 복제해야 합니다. 지역별로 분산된 가상 큐에 대해 완전히 일관성을 지켜야 하는 목적은 사실상 모든 Azure Service Bus 고객이 페더레이션 시나리오를 고려할 때 가지는 핵심 목표, 즉 솔루션에 대한 최대 가용성 및 안정성과 직접적으로 상충됩니다. 

따라서 여기에 나와 있는 패턴은 가용성 및 안정성에 중점을 두면서도 정보 손실 및 중복 메시지 처리를 모두 방지하기 위한 것입니다. 

### <a name="resiliency-against-regional-availability-events"></a>지역적인 가용성 이벤트에 대한 복원력 

최대 가용성 및 안정성이 Service Bus의 운영 최우선 순위이지만, 그럼에도 불구하고 생산자 또는 소비자가 네트워킹 또는 이름 확인 문제로 인해 할당된 "기본" Service Bus와 통화하는 것을 막을 수 있는 방법이 많습니다. 또는 Service Bus 엔터티가 일시적으로 응답하지 않거나 오류를 반환할 수 있습니다. 지정된 메시지 프로세서를 사용할 수 없게 될 수도 있습니다.

이러한 조건은 재해 복구 상황에서 수행할 수 있는 것처럼 지역 배포를 완전히 포기해야 하는 "재앙"은 아니지만 일부 애플리케이션의 비즈니스 시나리오는 몇 분 또는 몇 초 이하로 지속되는 가용성 이벤트의 영향을 받을 수 있습니다. Azure Service Bus는 하이브리드 클라우드 환경에서 사용되고 네트워크 에지에 있는 클라이언트(예: 소매 매장, 식당, 은행 지점, 제조 현장, 물류 시설 및 공항)에 사용되는 경우도 있습니다. 다른 지역의 보조 엔드포인트에 연결할 수 있지만 네트워크 라우팅 또는 정체 문제가 한 사이트의 할당된 Service Bus 엔드포인트에 도달할 수 있는 기능에 영향을 미칠 수 있습니다. 동시에 이러한 사이트에서 도착하는 시스템 처리 메시지는 여전히 기본 및 보조 Service Bus 엔드포인트에 방해받지 않고 액세스할 수 있습니다. 

네트워크 라우팅 문제의 영향이나 Service Bus 기업의 일시적인 가용성 문제에 대한 비즈니스 허용 범위가 낮은 하이브리드 클라우드 및 에지 애플리케이션의 실제 사례가 많이 있습니다. 여기에는 소매 사이트에서의 결제 처리, 공항 게이트에서의 탑승, 레스토랑에서의 휴대전화 주문 등이 포함되며, 이 모든 사항은 즉시 이루어지고 신뢰할 수 있는 통신 경로를 이용할 수 없을 때마다 완전히 정지됩니다.

이 범주에서는 세 가지 고유한 분산 패턴인 "모든 활성" 복제, "활성-수동" 복제 및 "스필오버" 복제에 대해 설명합니다. 

#### <a name="all-active-replication"></a>모든 활성 복제

"모든 활성" 복제 패턴을 사용하여 동일한 논리 항목(또는 큐)의 활성 복제본을 여러 네임스페이스 및 지역에서 사용할 수 있으며 모든 메시지를 큐에 넣은 위치에 관계없이 모든 복제본에서 사용할 수 있습니다. 패턴은 일반적으로 모든 게시자에 관해 메시지의 순서를 유지합니다. 

![모든 활성 패턴](media/service-bus-federation-overview/mirrored-topics.jpg)

그림에 나타난 것처럼 패턴은 일반적으로 Service Bus 항목에 의존합니다. 네임스페이스마다 한 항목이 복제 스키마에 참여해야 합니다. 이러한 항목마다 메시지를 복제할 다른 항목에 대한 "복제 구독"이 하나씩 있습니다. 위의 그림에는 항목 쌍이 있으므로 해당하는 다른 항목에 대한 단일 복제 구독이 있습니다. 3개의 네임스페이스 *{n1, n2, n3}* 가 있는 시나리오에서 네임스페이스 *n1* 의 항목은 2개의 복제 구독이 있어야 하며, *n2* 에 해당하는 항목에 1개, *n3* 에 해당하는 항목에 1개가 있어야 합니다. 

각 복제 구독에는 SQL 필터 식(`replicated <> 1`) 및 SQL 작업(`set replicated = 1`)을 결합하는 규칙이 있습니다. 규칙 필터를 사용하면 사용자 지정 속성 `replication`이 설정되어 있지 않거나 값 `1`이 없는 메시지만 이 구독을 할 수 있는 자격이 생기며, 이후 작업은 선택된 각 메시지에 값 `1`으로 일치하는 속성을 설정합니다. 이렇게 되면 메시지가 해당하는 항목에 메시지가 복사될 때 더 이상 반대 방향으로 복제할 자격이 없으므로 복제본 간에 메시지가 반송되지 않도록 합니다.

각각의 규칙을 사용하는 구독은 다음과 같은 Azure CLI를 사용하여 항목에 쉽게 추가할 수 있습니다.

```azurecli

az servicebus topic subscription rule create --resource-group myresourcegroup \
   --namespace mynamespace --topic-name mytopic 
   --subscription-name replication --name replication \
   --action-sql-expression "set replication = 1" \
   --filter-sql-expression "replication IS NULL"
```

큐를 모델링하기 위해 각 항목은 모든 소비자가 공유하는 하나의 정기 구독(복제 구독 제외)으로만 제한됩니다.

> 모든 활성 복제 모델은 항목에 전송된 모든 메시지의 복사본을 각 항목에 넣습니다. 즉, 각 지역의 애플리케이션 코드에 모든 메시지가 표시되고 처리됩니다.
> 이 패턴은 데이터를 여러 지역으로 공유하거나 일반적으로 중복 처리가 필요한 시나리오에 적합합니다. 일반 큐와 마찬가지로 모든 메시지를 한 번만 처리해야 하는 경우 다음 두 가지 패턴 중 하나를 고려해야 합니다.  

#### <a name="active-passive-replication"></a>활성-수동 복제

"활성-수동" 복제 패턴은 애플리케이션에서 메시지를 보내고 받는 데 사용하는 항목("기본") 중 하나만 사용하는 이전 패턴의 변형입니다. 기본 항목을 사용할 수 없거나 연결할 수 없는 경우 메시지가 보조 항목으로 복제됩니다. 

![활성 수동 패턴](media/service-bus-federation-overview/mirrored-topics-passive.jpg)

이 패턴과 이전 패턴의 주요 차이점은 복제가 기본 항목에서 보조 항목으로의 단방향이라는 점입니다. 보조 항목은 기본 항목이 아니라 기본 항목을 일시적으로 사용할 수 없는 경우에 대한 백업 옵션입니다. 

이 패턴 사용의 이점은 중복 처리를 최소화하는 데 유용하다는 것입니다. 복제하는 동안 `TimeToLive` 메시지 속성은 기본 항목의 실패가 장애 조치(failover)될 때 예상되는 시간을 반영하는 복제 메시지에 대한 기간으로 설정됩니다. 예를 들어, 사용 사례 시나리오에서 기본에서 메시지 검색 시 문제를 보여줄 때 최대 1분 이내에 소비자가 보조로 전환해야 하는 경우, 보조는 기본에서 액세스할 수 없는 모든 메시지를 제공하되 문제가 나타나기 전에 기본에서 이미 처리한 메시지 수는 최소화하는 것이 좋습니다. 복제하는 동안(규칙 작업에서 `set sys.TimeToLive = '0:2:0'`) `TimeToLive`를 해당 기간의 2배인 2분으로 설정하면 보조에서 2분 동안만 메시지가 보존되고 이전 메시지는 삭제됩니다. 즉, 수신기가 보조로 전환되면 마지막으로 처리한 메시지보다 오래된 메시지를 빠르게 읽고 삭제한 다음 아직 확인하지 않은 첫 번째 메시지를 처리할 수 있습니다. 실제 보존 기간은 특정 사용 사례 및 원하는 속도에 따라 다르며 애플리케이션에서 보조로 전환할 수 있습니다. `TimeToLive` 설정은 몇 초에서 몇 일 사이에 적용됩니다. 

애플리케이션이 보조를 사용하는 동안 보조 항목에 직접 게시할 수도 있으며, 이는 일반 항목처럼 작동합니다. 따라서 보조로 전환된 후 소비자는 복제된 메시지와 보조에 직접 게시된 메시지를 혼합하여 볼 수 있습니다. 따라서 애플리케이션은 먼저 기본으로 다시 게시를 전환하고 소비자가 보조로 다시 전환하기 전에 로컬로 게시된 메시지의 드레이닝을 계속 허용해야 합니다. 기본을 다시 사용할 수 있게 되면 복제가 자동으로 다시 시작되기 때문에, 소비자는 대기 시간이 다소 길어질 수 있더라도 이 시간 동안 기본에 게시된 새 메시지를 받게 됩니다. 

> 이 패턴은 메시지를 한 번만 처리해야 하는 시나리오에 적합합니다. 애플리케이션은 보조에서 장애 조치(failover) 기간 동안 중복 항목을 찾고 다시 전환하는 동안 중복 항목을 다시 찾기 때문에 기본에서 처리된 메시지를 추적하는 데 협력해야 합니다.
> 중복 제거 기준은 애플리케이션에서 제공하는 `MessageId`가 가장 좋습니다. `EnqueuedTimeUtc` 값은 워터마크 표시기로도 적합하지만 애플리케이션은 분산된 시스템으로 기본과 보조 사이에서 얼마 간의 클록 드리프트(몇 초)를 허용해야 합니다.


#### <a name="spillover-replication"></a>스필오버 복제

"스필오버" 복제 패턴을 사용하면 여러 지역에 있는 여러 Service Bus 엔터티의 활성/활성 사용을 통해 Service Bus가 정상 상태이지만 *소비자* 가 보류 중인 메시지의 수가 너무 많아지거나 완전히 사용할 수 없게 되는 시나리오를 처리할 수 있습니다. 이로 인해 소비자 프로세스를 지원하는 데이터베이스가 느리거나 사용하지 못할 수 있습니다. 이 패턴은 일반 큐 및 항목 구독과 함께 작동합니다.  

![스필오버 복제](media/service-bus-federation-overview/spillover.jpg)  

그림에 나타난 것처럼 스필오버 복제 패턴은 큐 또는 구독의 연결된 [배달 못한 편지 큐](service-bus-dead-letter-queues.md)의 메시지를 다른 네임스페이스와 쌍으로 연결된 큐 또는 항목으로 복제합니다. 

오류 상황이 발생하지 않으면 두 개의 네임스페이스가 병렬로 사용되며 각각은 전체 메시지 트래픽의 일부 하위 집합을 수신하고 해당 하위 집합을 처리하는 관련 소비자를 수신합니다. 소비자 중 하나가 높은 실패율을 보이거나 완전히 중지되면 배달 수를 초과하거나 만료되기 때문에 해당 메시지는 결국 배달 못한 편지 큐에 있게 됩니다. 복제 작업에서 이를 선택하고 쌍으로 연결된 큐에서 다시 큐에 넣으면 예상되는 정상 상태인 소비자에게 제공됩니다. 

특정 기한 내에 처리가 이루어져야 하는 경우 스필오버 보조를 통해 제 때 처리가 이루어질 수 있도록 큐 및/또는 메시지에 대한 `TimeToLive`를 설정해야 합니다. 예를 들어, `TimeToLive`가 허용된 시간의 절반으로 설정될 수 있습니다.

[모든 활성 패턴](#all-active-replication)과 마찬가지로 애플리케이션은 메시지가 이미 한 번 복제되어 큐 간에 바운스되지 않고 오히려 복합 패턴의 배달 못한 편지 큐 역할을 하는 보조 큐에 게시되는 메시지에 표시기를 추가할 수 있습니다.

> 이 패턴은 소비자가 의존하는 소비자 또는 리소스의 가용성 문제를 피하는 것, 또한 쌍으로 연결된 큐 중 하나에 트래픽 증가를 재분배하는 것에 주안점을 둔 시나리오에 적합합니다. 또한 소비자가 두 큐에서 모두 읽는 경우 사용할 수 없게 되는 네임스페이스 중 하나에 대한 보호를 제공하지만 `TimeToLive` 만료로 인해 부과되는 복제 지연으로 해당 기간 내의 메시지가 사용할 수 없는 네임스페이스에서 고립될 수 있습니다. 

### <a name="latency-optimization"></a>대기 시간 최적화 

항목은 여러 소비자에게 정보를 배포하는 데 사용됩니다. 경우에 따라 특히 지리적으로 분포가 넓은 소비자는 항목에서 소비자에게 가까운 보조 네임스페이스의 항목으로 메시지를 복제하는 것이 유용할 수 있습니다.

![대기 시간 최적화](media/service-bus-federation-overview/latency-optimization.jpg)  

예를 들어, 지역별, 대륙별 허브 간에 데이터를 공유할 때 허브 사이의 한 번만 정보를 이전하고 소비자가 해당 허브에서 데이터 복사본을 가져오는 것이 더 효율적입니다. 

복제 전송은 일괄 처리로 수행할 수 있으며, 소비자는 이를 통해 메시지를 하나씩 받아 해결하는 경우가 많습니다. 북아메리카와 유럽 사이에서 100밀리초의 기본 네트워크 대기 시간이 있는 경우, 각 메시지는 동일한 지역의 엔터티와 비교하여 메시지를 획득하고 정착하기 위해 원격 엔터티에 대한 두 번의 라운드트립을 처리하는 데 200밀리초가 더 걸립니다. 

### <a name="validation-reduction-and-enrichment"></a>유효성 검사, 절감 및 보강

외부 클라이언트를 통해 사용자 고유의 솔루션에 Service Bus 큐 또는 항목에 메시지를 제출할 수 있습니다.

![유효성 검사, 절감, 보강](media/service-bus-federation-overview/validation.jpg)  

이러한 메시지는 지정된 스키마를 준수하는지 및 비 준수 메시지를 삭제하거나 배달 못한 편지로 처리해야 하는지 확인해야 할 수 있습니다. 일부 메시지는 데이터를 생략하여 복잡성을 줄여야 할 수 있으며 일부는 참조 데이터 조회에 기반하여 데이터를 추가하여 강화해야 할 수 있습니다. 복제 작업에서 사용자 지정 기능으로 작업을 수행할 수 있습니다. 

### <a name="stream-to-queue-replication"></a>큐 복제에 대한 스트림

Azure Event Hubs는 대량의 들어오는 이벤트를 처리하는 데 적합한 솔루션입니다. 하지만 Event Hubs나 Apache Kafka와 같은 유사한 엔진은 여러 소비자가 복제 처리의 위험성 없이 동일한 원본의 메시지를 동시에 처리할 수 있는 서비스 관리형 [경쟁 소비자](/azure/architecture/patterns/competing-consumers) 모델을 제공하지 않으며, 해당 메시지는 처리된 후 최종적으로 해결합니다. 

![통합](media/service-bus-federation-overview/hub-to-queue.jpg)

큐 복제를 위한 스트림은 단일 이벤트 허브 파티션의 콘텐츠 또는 전체 이벤트 허브의 콘텐츠를 Service Bus 큐에 전송합니다. 여기서 메시지를 안전하게, 트랜잭션 방식으로, 경쟁 소비자와 함께 처리할 수 있습니다. 또한 이 복제는 항목 및 세션 기반 디멀티플렉싱을 통한 라우팅을 비롯하여 해당 메시지에 다른 모든 Service Bus 기능을 사용할 수 있도록 합니다. 

### <a name="consolidation-and-normalization"></a>통합 및 정규화 

전체 솔루션은 자체 처리 기능을 포함하여 대부분 독립적인 지역 공간으로 구성되는 경우가 많지만, 초지역적이고 전체적인 관점에는 데이터 통합이 필요하므로 로컬 관점에 대한 각각의 지역 공간에서 평가되는 동일한 메시지 데이터의 중앙 통합이 필요합니다. 

![통합](media/service-bus-federation-overview/merge.jpg)

정규화는 둘 이상의 수신 메시지 시퀀스가 동일한 종류의 정보를 다른 구조 또는 다른 인코딩으로 전달하며 메시지가 사용되려면 트랜스코드 또는 변환되어야 합니다. 

정규화에는 엔드투엔드 암호화 페이로드를 해독하고 다운스트림 소비자 대상에게 다른 키와 알고리즘을 사용하여 다시 암호화하는 것과 같은 암호화 작업도 포함될 수 있습니다. 

### <a name="splitting-and-routing"></a>분할 및 라우팅

Service Bus 항목 및 해당 구독 규칙은 특정 대상을 위한 메시지 스트림을 필터하는 데 사용되는 경우가 많습니다. 대상은 구독에서 필터링된 집합을 얻습니다. 

![분할](media/service-bus-federation-overview/split.jpg)

이러한 메시지에 대한 대상이 전체적으로 분산되거나 다른 애플리케이션에 속하는 글로벌 시스템에서 복제를 사용하여 해당 구독의 메시지가 사용되는 곳과 다른 네임스페이스의 큐 또는 항목으로 메시지를 전송할 수 있습니다.

### <a name="replication-applications-in-azure-functions"></a>Azure Functions의 복제 애플리케이션

위의 패턴을 구현하려면 구성 및 실행하려는 복제 작업을 위한 확장 가능하고 안정적인 실행 환경이 필요합니다. Azure에서 상태 비저장 작업에 가장 적합한 런타임 환경은 [Azure Functions](../azure-functions/functions-overview.md)입니다. 

Azure Functions는 [Azure 관리 ID](../active-directory/managed-identities-azure-resources/overview.md)로 실행할 수 있으므로 복제 작업은 복제 경로를 따라 비밀을 관리할 필요 없이 원본 및 대상 서비스의 역할 기반 액세스 제어 규칙으로 통합할 수 있습니다. 명시적 자격 증명을 요구하는 복제 원본 및 대상의 경우 Azure Functions는 [Azure Key Vault](../key-vault/general/overview.md) 내의 긴밀한 액세스 제어 스토리지에서 해당 자격 증명의 구성 값을 보유할 수 있습니다.

또한 Azure Functions를 사용하면 복제 잡업을 통해 모든 Azure 메시지 서비스에 대해 Azure 가상 네트워크 및 [서비스 엔드포인트](../virtual-network/virtual-network-service-endpoints-overview.md)를 직접 통합할 수 있으며 [Azure Monitor](../azure-monitor/overview.md)에 순조롭게 통합됩니다.

무엇보다 중요한 것은 Azure Functions에는 [Azure Event Hubs](../azure-functions/functions-bindings-service-bus.md), [Azure IoT Hub](../azure-functions/functions-bindings-event-iot.md), [Azure Service Bus](../azure-functions/functions-bindings-service-bus.md), [Azure Event Grid](../azure-functions/functions-bindings-event-grid.md) 및 [Azure Queue Storage](../azure-functions/functions-bindings-storage-queue.md), [RabbitMQ](https://github.com/azure/azure-functions-rabbitmq-extension)에 대한 사용자 지정 확장 및 [Apache Kafka](https://github.com/azure/azure-functions-kafka-extension)에 대해 미리 빌드되고, 확장 가능한 트리거 및 출력 바인딩이 있습니다. 대부분의 트리거는 문서화된 메트릭을 기반으로 동시에 실행되는 인스턴스 수를 늘리거나 줄임으로써 처리량 요구 사항에 동적으로 적응합니다. 

Azure Functions 소비 계획을 사용하면 미리 빌드된 트리거는 복제에 사용할 수 있는 메시지가 없는 상태에서 0으로 축소될 수도 있습니다. 즉, 구성의 크기를 조정할 수 있도록 유지하는 비용이 들지 않습니다. 소비 계획 사용의 주요 단점은 이 상태에서 복제 작업 “깨우기"의 대기 시간이 인프라를 실행하는 호스팅 계획보다 현저히 높다는 점입니다.  

이와 달리, Apache Kafka의 [MirrorMaker](http://kafka.apache.org/documentation/#basic_ops_mirror_maker)와 같이 메시지 및 이벤트를 위한 가장 일반적인 복제 엔진은 호스팅 환경을 제공하고 복제 엔진을 직접 확장해야 합니다. 여기에는 보안 및 네트워킹 기능을 구성 및 통합하고 데이터를 모니터링하는 흐름을 원활히 하는 것이 포함되지만 사용자 지정 복제 작업을 흐름에 삽입할 기회가 여전히 없습니다. 

## <a name="next-steps"></a>다음 단계

이 문서에서 다양한 페더레이션 패턴을 탐색하고 Azure에서 이벤트 및 메시지 복제 런타임으로서 Azure Functions의 역할을 알아보았습니다. 

다음으로 Azure Functions를 사용하여 복제기 애플리케이션을 설정하는 방법과 Event Hubs와 다양한 기타 이벤트 및 메시지 시스템 간에 이벤트 흐름을 복제하는 방법을 확인할 수 있습니다.

- [Azure Functions의 복제 애플리케이션](service-bus-federation-replicator-functions.md)
- [Service Bus 엔터티 간에 이벤트 복제](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/ServiceBusCopy)
- [Azure Event Hubs에 이벤트 라우팅](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/ServiceBusCopyToEventHub)
- [Azure Event Hubs에서 이벤트 가져오기](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/EventHubCopyToServiceBus)

[1]: ./media/service-bus-auto-forwarding/IC628632.gif