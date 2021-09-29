---
title: 이벤트 복제 작업 패턴-Azure Event Hubs | Microsoft Docs
description: 이 문서에서는 특정 이벤트 복제 작업 패턴을 구현하기 위한 자세한 지침을 제공합니다.
ms.topic: article
ms.date: 09/28/2021
ms.openlocfilehash: f4fb2a166a5dfd50f3035e5efe5462313716daef
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129217413"
---
# <a name="event-replication-tasks-patterns"></a>이벤트 복제 작업 패턴

[페더레이션 개요](event-hubs-federation-overview.md) 및 [복제기 함수 개요](event-hubs-federation-replicator-functions.md)에서는 복제 작업의 이유 및 기본 요소에 대해 설명하며, 이 문서를 계속하기 전에 해당 내용을 숙지하는 것이 좋습니다.

이 문서에서는 개요 섹션에 강조 표시된 몇 가지 패턴에 대한 구현 참고 자료를 자세히 설명합니다.

## <a name="replication"></a>복제

복제 패턴은 이벤트 허브 간에 이벤트를 복사하거나 이벤트 허브에서 Service Bus 큐와 같은 다른 대상으로 이벤트를 복사합니다. 이벤트 페이로드를 수정하지 않고 이벤트를 전달합니다.

이 패턴의 구현은 [Event Hubs 간의 이벤트 복제](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/EventHubCopy)와 [Event Hubs 및 Service Bus](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/EventHubCopyToServiceBus) 샘플 간의 이벤트 복제, Apache Kafka broker에서 Event Hubs로 데이터를 복제하는 특정 사례에 대한 [Event Hubs를 사용하는 Apache Kafka MirrorMaker 사용](event-hubs-kafka-mirror-maker-tutorial.md) 자습서에서 다룹니다.

### <a name="streams-and-order-preservation"></a>스트림 및 순서 유지

Azure Functions 또는 Azure Stream Analytics를 통한 복제는 대상 이벤트 허브에 대한 정확한 1:1 클론 생성을 보장하는 것이 아니라 애플리케이션에서 필요로 하는 이벤트의 상대적 순서를 유지하는 데 중점을 두고 있습니다. 애플리케이션은 동일한 파티션 키로 관련 이벤트를 그룹화하여 이를 전달하고 [Event Hubs는 동일한 파티션에서 동일한 파티션 키가 있는 메시지를 순차적으로 정렬합니다](event-hubs-features.md#partitions).

> [!IMPORTANT] 
> "오프셋" 정보는 각 이벤트 허브마다 고유하며 동일한 이벤트에 대한 오프셋은 이벤트 허브 인스턴스 간에 차이가 있습니다. 복사된 이벤트 스트림에서 위치를 찾으려면 시간 기반 오프셋을 사용하고 [전파된 서비스 할당 메타데이터](#service-assigned-metadata)를 참조합니다.
>
> 시간 기반 오프셋은 특정 시점에서 수신기를 시작합니다.
> - *Eventposition.FromStart()* - 보존된 모든 데이터를 다시 읽습니다.
> - *EventPosition.FromEnd()* - 연결된 시점부터 모든 새로운 데이터를 읽습니다.
> - *EventPosition.FromEnqueuedTime(dateTime)* - 주어진 날짜 및 시간부터 시작되는 모든 데이터입니다.
>
> EventProcessor에서 EventProcessorOptions의 InitialOffsetProvider를 통해 위치를 설정합니다. 다른 수신기 API를 사용하면 위치는 생성자를 통해 전달됩니다. 


Azure Functions 기반 지침에 사용되며 [샘플로 제공](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/src/Azure.Messaging.Replication)되는 미리 빌드된 복제 함수 도우미는 원본 파티션에서 검색된 동일한 파티션 키를 가진 이벤트 스트림이 동일한 파티션 키가 있는 원본 스트림의 일괄 처리 형태로 대상 이벤트 허브에 제출되도록 합니다.

원본 및 대상 이벤트 허브의 파티션 수가 동일한 경우 대상의 모든 스트림은 원본에서와 동일한 파티션에 매핑됩니다.
다음에 설명된 몇 가지 추가 패턴에서 중요한 파티션 수가 다른 경우 매핑은 다르지만 스트림은 항상 함께 순서대로 유지됩니다.

대상 파티션에서 파티션 키가 없는 다른 스트림 또는 독립 이벤트에 속하는 이벤트의 상대적인 순서는 항상 원본 파티션과 다를 수 있습니다.

### <a name="service-assigned-metadata"></a>서비스 할당된 메타데이터

원본 이벤트 허브, 원래 큐 시간, 시퀀스 번호 및 오프셋에서 가져온 이벤트의 서비스 할당된 메타데이터가 대상 이벤트 허브에서 새로 서비스 할당된 값으로 대체되지만 원래 값은 [도우미 기능](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/src/Azure.Messaging.Replication)인 샘플에 제공된 기본 복제 작업을 통해 사용자 속성 `repl-enqueue-time`(ISO8601 문자열), `repl-sequence`, `repl-offset`에 보존됩니다.

이러한 속성은 문자열 형식이며 해당 원래 속성의 문자열로 변환된 값을 포함합니다. 이벤트가 여러 번 전달되는 경우에는 직접적인 원본의 서비스 할당된 메타데이터가 이미 존재하는 속성에 추가되고 값은 세미콜론으로 구분됩니다.

### <a name="failover"></a>장애 조치 

재해 복구를 위해 복제를 사용하는 경우 Event Hubs 서비스의 지역 가용성 이벤트 또는 네트워크 중단에 대한 보호를 위해 이러한 모든 오류 시나리오에서는 하나의 이벤트 허브에서 다음으로 장애 조치(failover)를 수행하여 생산자 및/또는 소비자에게 보조 엔드포인트를 사용하도록 지시해야 합니다.

모든 장애 조치(failover) 시나리오에서는 네임스페이스의 사전 요구 사항이 구조적으로 동일하다고 가정합니다. 즉, Event Hubs 및 소비자 그룹의 이름이 동일하고 공유 액세스 서명 규칙 및/또는 역할 기반 액세스 제어 규칙이 동일한 방식으로 설정됩니다. [네임 스페이스를 이동하고 정리 단계를 생략하는 ](move-across-regions.md)지침에 따라 보조 네임스페이스를 만들고 업데이트할 수 있습니다.

생산자와 소비자를 강제로 전환하려면 쉽게 연결하고 업데이트할 수 있는 위치에서 조회할 수 있는 네임스페이스에 대한 정보를 제공해야 합니다. 생산자 또는 소비자가 자주 또는 지속적으로 오류를 겪는 경우 해당 위치를 참조하고 구성을 조정해야 합니다. 이 구성을 공유하는 방법에는 여러 가지가 있지만 DNS 및 파일 공유의 두 가지에 대해 설명합니다.

#### <a name="dns-based-failover-configuration"></a>DNS 기반 장애 조치(failover) 구성

한 가지 후보 접근 방식은 사용자가 제어하는 DNS의 DNS SRV 레코드에 있는 정보를 보관하여 이벤트 허브 엔드포인트를 가리키는 것입니다. 

> [!IMPORTANT] 
> Event Hubs는 엔드포인트가 CNAME 레코드와 직접 별칭 관계가 되도록 허용하지 않으므로 DNS를 엔드포인트 주소에 대한 복원력 있는 조회 메커니즘으로 사용하고 IP 주소 정보를 직접 확인하지 않습니다.

도메인 `example.com` 및 영역 `test.example.com`(사용자의 애플리케이션의 경우)을 소유한다고 가정합니다. 두 개의 대체 Event Hubs에 대해 이제 두 개의 추가 중첩 영역과 각각에 SRV 레코드를 만듭니다.

SRV 레코드는 일반적인 규칙에 따라 `_azure_eventhubs._amqp`로 접두사가 오고 두 개의 엔드포인트 레코드를 보유합니다. 하나는 포트 5671의 TLS를 통한 AMQP용이고 다른 하나는 포트 443의 WebSocket을 통한 AMQP용이며, 둘 다 영역에 해당하는 네임스페이스의 Event Hubs 엔드포인트를 가리킵니다.

| 영역                   | SRV 레코드                                                                                                                                                            |
| ---------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `eh1.test.example.com` | **`_azure_servicebus._amqp.eh1.test.example.com`**<br>`1 1 5671 eh1-test-example-com.servicebus.windows.net`<br>`2 2 443 eh1-test-example-com.servicebus.windows.net` |
| `eh2.test.example.com` | **`_azure_servicebus._amqp.eh2.test.example.com`**<br>`1 1 5671 eh2-test-example-com.servicebus.windows.net`<br>`2 2 443 eh2-test-example-com.servicebus.windows.net` |

그런 다음, 애플리케이션의 영역에서 기본 이벤트 허브에 해당하는 하위 영역을 가리키는 CNAME 항목을 만듭니다.

| CNAME 레코드                | Alias                    |
| --------------------------- | ------------------------ |
| `eventhub.test.example.com` | `eh1.test.example.com`   |

CNAME 및 SRV 레코드를 명시적으로 쿼리할 수 있도록 허용하는 DNS 클라이언트를 사용하는 경우(Java 및 .NET의 기본 제공 클라이언트는 IP 주소로 간단한 이름 확인만 허용) 원하는 엔드포인트를 확인할 수 있습니다. 예를 들어 [DnsClient.NET](https://dnsclient.michaco.net/)를 사용하는 경우 조회 함수는 다음과 같습니다.

```C#
static string GetEventHubName(string aliasName)
{
    const string SrvRecordPrefix = "_azure_eventhub._amqp.";
    LookupClient lookup = new LookupClient();

    return (from CNameRecord alias in (lookup.Query(aliasName, QueryType.CNAME).Answers)
            from SrvRecord srv in lookup.Query(SrvRecordPrefix + alias.CanonicalName, QueryType.SRV).Answers
            where srv.Port == 5671
            select srv.Target).FirstOrDefault()?.Value.TrimEnd('.');
}
```

함수는 위에 표시된 대로 현재 CNAME로 별칭이 지정된 영역의 포트 5671에 대해 등록된 대상 호스트 이름을 반환합니다.

장애 조치(failover)를 수행하려면 CNAME 레코드를 편집하고 대체 영역을 가리켜야 합니다.

DNS, 특히 [Azure DNS](../dns/dns-overview.md)를 사용할 경우 장점은 Azure DNS 정보가 전역으로 복제되므로 단일 지역 가동 중단에 대해 복원된다는 것입니다.

이 절차는 [Event Hubs Geo-DR](event-hubs-geo-dr.md)이 작동하는 방식과 유사하지만 완전히 사용자가 제어할 수 있으며 활성/활성 시나리오에서도 작동합니다.

#### <a name="file-share-based-failover-configuration"></a>파일 공유 기반 장애 조치(failover) 구성

엔드포인트 정보를 공유하기 위해 DNS를 사용하는 가장 간단한 방법은 기본 엔드포인트의 이름을 일반 텍스트 파일에 넣고 중단에 대해 강력하면서도 업데이트를 허용하는 인프라의 파일을 제공하는 것입니다.

전체 가용성 및 콘텐츠 복제를 사용하여 고가용성 웹 사이트 인프라를 이미 실행한 경우 이러한 파일을 여기에 추가하고 전환이 필요한 경우 파일을 다시 게시합니다.

> [!CAUTION]
> 비밀을 포함하는 전체 연결 문자열이 아니라 이러한 방식으로 엔드포인트 이름만 게시해야 합니다.

#### <a name="extra-considerations-for-failing-over-consumers"></a>소비자 장애 조치(failover)에 대한 추가 고려 사항

이벤트 허브 소비자의 경우 장애 조치(failover) 전략에 대한 추가 고려 사항은 이벤트 프로세서의 요구 사항에 따라 달라집니다.

백업 데이터에서 데이터베이스를 포함하여 시스템을 다시 빌드해야 하는 재해가 발생하고 해당 데이터베이스가 이벤트 허브에 있는 이벤트에서 직접 또는 중간 처리를 통해 생성되는 경우에는 백업을 복원한 다음 원래 시스템이 제거된 순간이 아니라 데이터베이스 백업이 만들어진 순간부터 시스템으로 이벤트를 재생하기 시작합니다.

실패가 시스템 조각 또는 더 이상 연결할 수 없는 단일 이벤트 허브에만 영향을 주는 경우, 처리가 중단된 위치와 거의 동일한 위치에서 이벤트를 계속 처리할 수 있습니다.

이러한 시나리오를 실현하고 각 Azure SDK의 이벤트 프로세서를 사용하려면 처리를 계속하려는 _타임스탬프_ 를 기반으로 [새 검사점 저장소를 만들고](event-processor-balance-partition-load.md#checkpointing) 초기 파티션 위치를 제공합니다.

외부로 전환하는 이벤트 허브의 검사점 저장소에 대한 액세스 권한이 있는 경우, 위에서 설명한 [전파된 메타데이터](#service-assigned-metadata)를 사용하여 이미 처리된 이벤트를 건너뛰고 마지막으로 중단한 위치에서 정확하게 계속하는 데 도움이 됩니다.

## <a name="merge"></a>Merge

병합 패턴에는 한 대상을 가리키는 하나 이상의 복제 작업이 있습니다. 일반 생산자와 동시에 동일한 대상으로 이벤트를 보낼 수도 있습니다.

이러한 패턴의 변형은 다음과 같습니다.

- 두 개 이상의 복제 함수는 서로 다른 원본에서 이벤트를 동시에 가져오고 동일한 대상으로 전송합니다.
- 원본에서 이벤트를 가져오는 복제 함수가 하나 이상 있는 반면 대상은 생산자가 직접 사용합니다.
- 이전 패턴이지만 두 개 이상의 Event Hubs 간에 미러링된 경우에는 이벤트가 생성되는 위치에 관계없이 동일한 스트림을 포함하는 Event Hubs를 생성합니다.

처음 두 가지 패턴 변형은 간단하며 일반 복제 작업과는 다르지 않습니다.

마지막 시나리오에서는 이미 복제된 이벤트를 다시 복제하지 않도록 제외해야 합니다. 이 기법은 [EventHubToEventHubMerge](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/code/EventHubMerge) 샘플에 시연 및 설명되어 있습니다.

## <a name="editor"></a>편집기

편집기 패턴은 [복제](#replication) 패턴을 기반으로 하지만 메시지를 전달하기 전에 수정합니다. 

이러한 수정의 예제는 다음과 같습니다.

- **_코드 변환_** - 이벤트 콘텐츠("바디" 또는 "페이로드"라고도 함)가 _Apache Avro_ 형식 또는 일부 독점 serialization 형식을 사용하여 인코딩된 원본에서 도착하지만 대상을 소유하는 시스템에서 콘텐츠를 _JSON_ 으로 인코딩해야 할 경우, 코드 변환 복제 작업은 먼저 _Apache Avro_ 에서 메모리 내 개체 그래프로 페이로드를 역직렬화한 다음 전달되는 메시지의 _JSON_ 형식으로 해당 그래프를 직렬화합니다. 코드 변환에는 **콘텐츠 압축** 및 압축 풀기 작업도 포함됩니다.
- **_변환_** - 구조화된 데이터를 포함하는 이벤트는 다운스트림 소비자가 더 쉽게 사용할 수 있도록 해당 데이터를 변경해야 할 수 있습니다. 이렇게 하려면 중첩된 구조를 평면화하거나, 불필요한 데이터 요소를 정리하거나, 지정된 스키마에 정확히 맞도록 페이로드를 변경하는 등의 작업이 필요할 수 있습니다.
- **_일괄 처리_** - 이벤트를 원본에서 일괄 처리(한 번의 전송으로 여러 이벤트)로 받을 수 있지만 대상으로만 전달하거나 그 반대의 경우도 가능해야 합니다. 따라서 작업은 단일 입력 이벤트 전송을 기반으로 여러 이벤트를 전달하거나 함께 전송되는 일련의 이벤트를 집계할 수 있습니다.
- **_유효성 검사_** - 외부 원본의 이벤트 데이터를 전달하기 전에 일련의 규칙을 준수하는지 여부를 확인해야 하는 경우가 종종 있습니다. 규칙은 스키마 또는 코드를 사용하여 나타낼 수 있습니다. 규정을 준수하지 않는 것으로 확인된 이벤트는 로그에 표시된 문제를 사용하여 삭제될 수도 있고, 추가 처리를 위해 특수 대상으로 전달될 수도 있습니다.
- **_보강_** - 일부 원본에서 가져온 이벤트 데이터는 대상 시스템에서 사용할 수 있도록 추가 컨텍스트가 있는 보강이 필요할 수 있습니다. 여기에는 참조 데이터를 조회하고 해당 데이터를 이벤트에 포함하거나, 복제 작업에 알려져 있지만 이벤트에는 포함되지 않은 원본에 대한 정보를 추가하는 작업이 포함될 수 있습니다.
- **_필터링_** - 원본에서 도착하는 일부 이벤트는 일부 규칙에 따라 대상에서 보류되어야 할 수도 있습니다. 필터는 규칙에 따라 이벤트를 테스트하고 이벤트가 규칙과 일치하지 않는 경우 이벤트를 삭제합니다. 특정 기준을 관찰하고 동일한 값을 가진 후속 이벤트를 삭제하여 중복 이벤트를 필터링하는 것은 필터링의 한 형태입니다.
- **_암호화_** - 복제 작업은 원본에서 도착한 콘텐츠의 암호를 해독하거나 대상으로 전달되는 콘텐츠를 암호화하거나, 이벤트에 전달된 서명을 기준으로 콘텐츠 및 메타데이터의 무결성을 확인하거나 이러한 서명을 첨부해야 할 수 있습니다.
- **_증명_** - 복제 작업은 잠재적으로 디지털 서명에 의해 보호되는 메타데이터를 특정 채널 또는 특정 시간에 수신되었음을 증명하는 이벤트에 첨부할 수 있습니다.
- **_연결_** - 복제 작업은 스트림의 무결성이 보호되고 누락된 이벤트를 검색할 수 있도록 이벤트 스트림에 서명을 적용할 수 있습니다.

변환, 일괄 처리 및 보강 패턴은 일반적으로 [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) 작업으로 구현하는 것이 가장 좋습니다. 

이러한 모든 패턴은 이벤트를 가져오는 데 [이벤트 허브 트리거](../azure-functions/functions-bindings-event-hubs-trigger.md)를 사용하고, 이벤트를 전달하는 데 [이벤트 허브 출력 바인딩](../azure-functions/functions-bindings-event-hubs-output.md)을 사용하는 Azure Functions를 사용하여 구현할 수 있습니다.

## <a name="routing"></a>라우팅

라우팅 패턴은 [복제](#replication) 패턴을 기반으로 하지만 원본 및 대상을 하나씩 포함하는 대신, 복제 작업에 여러 대상이 있으며 여기 C#에 다음과 같이 설명되어 있습니다.

```csharp
[FunctionName("EH2EH")]
public static async Task Run(
    [EventHubTrigger("source", Connection = "EventHubConnectionAppSetting")] EventData[] events,
    [EventHub("dest1", Connection = "EventHubConnectionAppSetting")] EventHubClient output1,
    [EventHub("dest2", Connection = "EventHubConnectionAppSetting")] EventHubClient output2,
    ILogger log)
{
    foreach (EventData eventData in events)
    {
        // send to output1 and/or output2 based on criteria
        EventHubReplicationTasks.ConditionalForwardToEventHub(input, output1, log, (eventData) => {
            return ( inputEvent.SystemProperties.SequenceNumber%2==0 ) ? inputEvent : null;
        });
        EventHubReplicationTasks.ConditionalForwardToEventHub(input, output2, log, (eventData) => {
            return ( inputEvent.SystemProperties.SequenceNumber%2!=0 ) ? inputEvent : null;
        });
    }
}
```

라우팅 함수는 메시지 메타데이터 및/또는 메시지 페이로드를 고려한 다음, 보낼 사용 가능한 대상 중 하나를 선택합니다.

Azure Stream Analytics에서는 여러 출력을 정의한 다음 출력당 쿼리를 실행하여 동일한 결과를 달성할 수 있습니다.

```sql
select * into dest1Output from inputSource where Info = 1
select * into dest2Output from inputSource where Info = 2
```


## <a name="log-projection"></a>로그 프로젝션

로그 프로젝션 패턴은 이벤트 스트림을 데이터베이스의 레코드가 되는 인덱싱된 데이터베이스로 평면화합니다. 일반적으로 이벤트는 동일한 컬렉션 또는 테이블에 추가되고, 이벤트 허브 파티션 키는 레코드를 고유하게 만드는 기본 키의 파티가 됩니다.

로그 프로젝션은 이벤트 데이터의 시계열 historian 또는 압축된 보기를 생성하여 각 파티션 키에 대한 최신 이벤트만 보존할 수 있습니다. 대상 데이터베이스의 모양은 궁극적으로 사용자와 애플리케이션 요구 사항에 따라 결정됩니다. 이 패턴을 "이벤트 소싱"이라고도 합니다.

> [!TIP]
> Azure Stream Analytics에서 [Azure SQL Database](../stream-analytics/sql-database-output.md) 및 [Azure Cosmos DB](../stream-analytics/azure-cosmos-db-output.md)에 대한 로그 프로젝션을 쉽게 만들 수 있으며, 이 옵션을 선호해야 합니다.

다음 Azure 함수는 Azure CosmosDB 컬렉션에 압축된 이벤트 허브의 콘텐츠를 반영합니다.

```C#
[FunctionName("Eh1ToCosmosDb1Json")]
[ExponentialBackoffRetry(-1, "00:00:05", "00:05:00")]
public static async Task Eh1ToCosmosDb1Json(
    [EventHubTrigger("eh1", ConsumerGroup = "Eh1ToCosmosDb1", Connection = "Eh1ToCosmosDb1-source-connection")] EventData[] input,
    [CosmosDB(databaseName: "SampleDb", collectionName: "foo", ConnectionStringSetting = "CosmosDBConnection")] IAsyncCollector<object> output,
    ILogger log)
{
    foreach (var ev in input)
    {
        if (!string.IsNullOrEmpty(ev.SystemProperties.PartitionKey))
        {
            var record = new
            {
                id = ev.SystemProperties.PartitionKey,
                data = JsonDocument.Parse(ev.Body),
                properties = ev.Properties
            };
            await output.AddAsync(record);
        }
    }
}
```

## <a name="next-steps"></a>다음 단계

- [Azure Functions의 이벤트 복제기 애플리케이션][1]
- [Event Hubs 간 이벤트 복제][2]
- [Azure Service Bus에 이벤트 복제][3]

[1]: event-hubs-federation-replicator-functions.md
[2]: https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/EventHubCopy
[3]: https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/EventHubCopyToServiceBus
