---
title: 메시지 복제 작업 패턴 - Azure Service Bus | Microsoft Docs
description: 이 문서에서는 특정 메시지 복제 작업 패턴을 구현하기 위한 자세한 지침을 제공합니다.
ms.topic: article
ms.date: 09/28/2021
ms.openlocfilehash: 4effcb9f51532cb2ef87b18b264789c526b57585
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129211794"
---
# <a name="message-replication-tasks-patterns"></a>메시지 복제 작업 패턴

[페더레이션 개요](service-bus-federation-overview.md) 및 [복제기 함수 개요](service-bus-federation-replicator-functions.md)에서는 복제 작업의 이유 및 기본 요소에 대해 설명하며, 이 문서를 계속하기 전에 해당 내용을 숙지하는 것이 좋습니다.

이 문서에서는 개요 섹션에 강조 표시된 몇 가지 패턴에 대한 구현 참고 자료를 자세히 설명합니다. 

## <a name="replication"></a>복제 

복제 패턴은 한 큐 또는 토픽에서 다음으로, 또는 큐 또는 토픽에서 이벤트 허브와 같은 다른 대상으로 메시지를 복사합니다. 메시지 페이로드를 수정하지 않고 메시지를 전달합니다. 

이 패턴의 구현에는 [Azure Service Bus 샘플에 대한 메시지 복제](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/ServiceBusCopy)가 적용됩니다.

### <a name="sequences-and-order-preservation"></a>시퀀스 및 순서 유지

복제 모델은 원본 큐 또는 토픽 메시지의 절대적인 순서를 대상 큐 또는 토픽으로 유지하는 것을 목표로 하지는 않지만, 필요할 때마다 애플리케이션에서 필요로 하는 메시지의 상대적인 순서를 유지하는 데 중점을 두고 있습니다. 애플리케이션에서는 원본 엔터티에 대한 세션 지원을 사용하도록 설정하고 동일한 [세션 키](message-sessions.md)를 사용하여 관련 메시지를 그룹화하여 이 기능을 사용하도록 설정합니다.

세션 인식 이미 작성된 복제 함수는 원본 엔터티에서 검색된 동일한 세션 ID를 가진 메시지 시퀀스가 원래 시퀀스의 일괄 처리로 대상 큐 또는 토픽으로 제출되도록 보장합니다. 

### <a name="service-assigned-metadata"></a>서비스 할당된 메타데이터 

원본 큐 또는 토픽, 원래 큐 시간 및 시퀀스 번호에서 가져온 메시지의 서비스 할당된 메타데이터가 대상 큐 또는 토픽에서 새로 서비스 할당된 값으로 대체되지만 샘플에 제공된 기본 복제 작업을 통해 원래 값은 사용자 속성 `repl-enqueue-time` (ISO8601 문자열) 및 `repl-sequence`에 보존됩니다.

이러한 속성은 문자열 형식이며 해당 원래 속성의 문자열로 변환된 값을 포함합니다.  메시지가 여러 번 전달되는 경우에는 직접적인 원본의 서비스 할당된 메타데이터가 이미 존재하는 속성에 추가되고 값은 세미콜론으로 구분됩니다. 

### <a name="failover"></a>장애 조치 

재해 복구를 위해 복제를 사용하는 경우 Service Bus 서비스의 지역 가용성 메시지 또는 네트워크 중단에 대한 보호를 위해 이러한 모든 오류 시나리오에서는 하나의 큐 또는 토픽에서 다음으로 장애 조치(failover)를 수행하여 생산자 및/또는 소비자에게 보조 엔드포인트를 사용하도록 지시해야 합니다.

모든 장애 조치(failover) 시나리오에서는 네임스페이스의 사전 요구 사항이 구조적으로 동일하다고 가정합니다. 즉, 큐와 토픽의 이름이 동일하고 공유 액세스 서명 규칙 및/또는 역할 기반 액세스 제어 규칙이 동일한 방식으로 설정됩니다. [네임 스페이스를 이동](move-across-regions.md)하고 정리 단계를 생략하는 지침에 따라 보조 네임스페이스를 만들고 업데이트할 수 있습니다.

생산자와 소비자를 강제로 전환하려면 쉽게 연결하고 업데이트할 수 있는 위치에서 조회할 수 있는 네임스페이스에 대한 정보를 제공해야 합니다. 생산자 또는 소비자가 자주 또는 지속적으로 오류를 겪는 경우 해당 위치를 참조하고 구성을 조정해야 합니다. 이 구성을 공유하는 방법에는 여러 가지가 있지만 DNS 및 파일 공유의 두 가지에 대해 설명합니다.

#### <a name="dns-based-failover-configuration"></a>DNS 기반 장애 조치(failover) 구성

한 가지 후보 접근 방식은 사용자가 제어하는 DNS의 DNS SRV 레코드에 있는 정보를 보관하여 해당 큐 또는 토픽 엔드포인트를 가리키는 것입니다. Hubs라는 메시지는 엔드포인트가 CNAME 레코드와 직접 별칭 관계가 되도록 허용하지 않으므로 DNS를 엔드포인트 주소에 대한 복원력 있는 조회 메커니즘으로 사용하고 IP 주소 정보를 직접 확인하지 않습니다. 

도메인 `example.com` 및 영역 `test.example.com`(사용자의 애플리케이션의 경우)을 소유한다고 가정합니다. 두 개의 대체 Service Bus에 대해 이제 두 개의 추가 중첩 영역과 각각에 SRV 레코드를 만듭니다. 

SRV 레코드는 일반적인 규칙에 따라 `_azure_servicebus._amqp`로 접두사가 오고 두 개의 엔드포인트 레코드를 보유합니다. 하나는 포트 5671의 TLS를 통한 AMQP용이고 다른 하나는 포트 443의 WebSocket을 통한 AMQP용이며, 둘 다 영역에 해당하는 네임스페이스의 Service Bus 엔드포인트를 가리킵니다.

| 영역                 | SRV 레코드
|----------------------|-------------------------------------------------------------
| `sb1.test.example.com` | **`_azure_servicebus._amqp.sb1.test.example.com`**<br>`1 1 5671 sb1-test-example-com.servicebus.windows.net`<br>`2 2 443 sb1-test-example-com.servicebus.windows.net`
| `sb2.test.example.com` | **`_azure_servicebus._amqp.sb1.test.example.com`**<br>`1 1 5671 sb2-test-example-com.servicebus.windows.net`<br>`2 2 443 sb2-test-example-com.servicebus.windows.net`

그런 다음, 애플리케이션의 영역에서 기본 큐 또는 토픽에 해당하는 하위 영역을 가리키는 CNAME 항목을 만듭니다.

| CNAME 레코드                 | Alias
|------------------------------|-------------------------------------------------------------
| `servicebus.test.example.com`  | `sb1.test.example.com`

CNAME 및 SRV 레코드를 명시적으로 쿼리할 수 있도록 허용하는 DNS 클라이언트를 사용하는 경우(Java 및 .NET의 기본 제공 클라이언트는 IP 주소로 간단한 이름 확인만 허용) 원하는 엔드포인트를 확인할 수 있습니다. 예를 들어 [DnsClient.NET](https://dnsclient.michaco.net/)를 사용하는 경우 조회 함수는 다음과 같습니다.

``` C#
static string GetServiceBusName(string aliasName)
{
    const string SrvRecordPrefix = "_azure_servicebus._amqp.";
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

이 절차는 [Service Bus Geo-DR](service-bus-geo-dr.md)이 작동하는 방식과 유사하지만 완전히 사용자가 제어할 수 있으며 활성/활성 시나리오에서도 작동합니다.

#### <a name="file-share-based-failover-configuration"></a>파일 공유 기반 장애 조치(failover) 구성

엔드포인트 정보를 공유하기 위해 DNS를 사용하는 가장 간단한 방법은 기본 엔드포인트의 이름을 일반 텍스트 파일에 넣고 중단에 대해 강력하면서도 업데이트를 허용하는 인프라의 파일을 제공하는 것입니다. 

전체 가용성 및 콘텐츠 복제를 사용하여 고가용성 웹 사이트 인프라를 이미 실행한 경우 이러한 파일을 여기에 추가하고 전환이 필요한 경우 파일을 다시 게시합니다.

## <a name="merge"></a>Merge

병합 패턴에는 한 대상을 가리키는 하나 이상의 복제 작업이 있습니다. 일반 생산자와 동시에 동일한 대상으로 메시지를 보낼 수도 있습니다. 

이 패턴의 변형은 다음과 같습니다.
- 두 개 이상의 복제 함수는 서로 다른 원본에서 메시지를 동시에 가져오고 동일한 대상으로 전송합니다.
- 원본에서 메시지를 가져오는 복제 함수가 하나 이상 있는 반면 대상은 생산자가 직접 사용합니다. 
- 이전 패턴이지만 메시지가 두 개 이상의 토픽 간에 미러링된 경우 메시지가 생성되는 위치에 관계없이 동일한 메시지가 포함된 토픽이 생성됩니다.

처음 두 가지 패턴 변형은 간단하며 일반 복제 작업과는 다르지 않습니다.

마지막 시나리오에서는 이미 복제된 메시지를 다시 복제하지 않도록 제외해야 합니다. 해당 기술은 활성/활성 샘플에서 시연되고 설명되어 있습니다.

## <a name="editor"></a>편집기

편집기 패턴은 [복제](#replication) 패턴을 기반으로 하지만 메시지를 전달하기 전에 수정합니다. 이러한 수정의 예는 다음과 같습니다.

- ***코드 변환*** - 메시지 콘텐츠("바디" 또는 "페이로드"라고도 함)가 *Apache Avro* 형식 또는 일부 독점 serialization 형식을 사용하여 인코딩된 원본에서 도착하지만 대상을 소유하는 시스템에서 콘텐츠를 *JSON* 으로 인코딩해야 할 경우, 코드 변환 복제 작업은 먼저 *Apache Avro* 에서 메모리 내 개체 그래프로 페이로드를 역직렬화한 다음 전달되는 메시지의 *JSON* 형식으로 해당 그래프를 직렬화합니다. 코드 변환에는 **콘텐츠 압축** 및 압축 풀기 작업도 포함됩니다.
- ***변환*** - 구조화된 데이터를 포함하는 메시지는 다운스트림 소비자가 더 쉽게 사용할 수 있도록 해당 데이터를 변경해야 할 수 있습니다. 이렇게 하려면 중첩된 구조를 평면화하거나, 불필요한 데이터 요소를 정리하거나, 지정된 스키마에 정확히 맞도록 페이로드를 변경하는 등의 작업이 필요할 수 있습니다.
- ***일괄 처리*** - 메시지를 원본에서 일괄 처리(한 번의 전송으로 여러 메시지)로 받을 수 있지만 대상으로만 전달하거나 그 반대의 경우도 가능해야 합니다. 따라서 작업은 단일 입력 메시지 전송을 기반으로 여러 메시지를 전달하거나 함께 전송되는 일련의 메시지를 집계할 수 있습니다. 
- ***유효성 검사*** - 외부 원본의 메시지 데이터를 전달하기 전에 일련의 규칙을 준수하는지 여부를 확인해야 하는 경우가 종종 있습니다. 규칙은 스키마 또는 코드를 사용하여 나타낼 수 있습니다. 규정을 준수하지 않는 것으로 확인된 메시지는 로그에 표시된 문제를 사용하여 삭제될 수도 있고, 추가 처리를 위해 특수 대상으로 전달될 수도 있습니다.   
- ***보강*** - 일부 원본에서 가져온 메시지 데이터는 대상 시스템에서 사용할 수 있도록 추가 컨텍스트가 있는 보강이 필요할 수 있습니다. 여기에는 참조 데이터를 조회하고 해당 데이터를 메시지에 포함하거나, 복제 작업에 알려져 있지만 메시지에는 포함되지 않은 원본에 대한 정보를 추가하는 작업이 포함될 수 있습니다. 
- ***필터링*** - 원본에서 도착하는 일부 메시지는 일부 규칙에 따라 대상에서 보류되어야 할 수도 있습니다. 필터는 규칙에 대해 메시지를 테스트하고 메시지가 규칙과 일치하지 않는 경우 메시지를 삭제합니다. 특정 기준을 관찰하고 동일한 값을 가진 후속 메시지를 삭제하여 중복 메시지를 필터링하는 것은 필터링의 한 형태입니다.
- ***라우팅 및 분할*** - 일부 복제 작업은 두 개 이상의 대체 대상에 대해 허용하고, 메시지의 메타데이터 또는 콘텐츠에 따라 특정 메시지에 대해 복제 대상을 선택하는 규칙을 정의할 수 있습니다. 특수한 형태의 라우팅은 분할이며, 이 경우 작업은 규칙에 따라 하나의 복제 대상에 파티션을 명시적으로 할당합니다.
- ***암호화*** - 복제 작업은 원본에서 도착한 콘텐츠의 암호를 해독하거나 대상으로 전달되는 콘텐츠를 암호화하거나, 메시지에 전달된 서명을 기준으로 콘텐츠 및 메타데이터의 무결성을 확인하거나 이러한 서명을 첨부해야 할 수 있습니다. 
- ***증명*** - 복제 작업은 잠재적으로 디지털 서명에 의해 보호되는 메타데이터를 특정 채널 또는 특정 시간에 수신되었음을 증명하는 메시지에 첨부할 수 있습니다.     
- ***연결*** - 복제 작업은 시퀀스의 무결성이 보호되고 누락된 메시지를 검색할 수 있도록 메시지 시퀀스에 서명을 적용할 수 있습니다.

이러한 모든 패턴은 메시지를 가져오는 데 [메시지 허브 트리거](../azure-functions/functions-bindings-service-bus-trigger.md)를 사용하고, 메시지를 전달하는 데 [큐 또는 토픽 출력 바인딩](../azure-functions/functions-bindings-service-bus-output.md)을 사용하는 Azure Functions을 사용하여 구현할 수 있습니다. 

## <a name="routing"></a>라우팅

라우팅 패턴은 [복제](#replication) 패턴을 기반으로 하지만 원본 및 대상을 하나씩 포함하는 대신, 복제 작업에 여러 대상이 있으며 여기 C#에 다음과 같이 설명되어 있습니다.

``` csharp
[FunctionName("SBRouter")]
public static async Task Run(
    [ServiceBusTrigger("source", Connection = "serviceBusConnectionAppSetting")] Message[] messages,
    [ServiceBus("dest1", Connection = "serviceBusConnectionAppSetting")] QueueClient output1,
    [ServiceBus("dest2", Connection = "serviceBusConnectionAppSetting")] QueueClient output2,
    ILogger log)
{
    foreach (Message messageData in messages)
    {
        // send to output1 or output2 based on criteria 
    }
}
```

라우팅 함수는 메시지 메타데이터 및/또는 메시지 페이로드를 고려한 다음, 보낼 사용 가능한 대상 중 하나를 선택합니다.

## <a name="next-steps"></a>다음 단계

- [Azure Functions의 메시지 복제기 애플리케이션][1]
- [Service Bus 간에 메시지 복제][2]
- [Azure Event Hubs에 메시지 복제][3]

[1]: service-bus-federation-replicator-functions.md
[2]: https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/ServiceBusCopy
[3]: https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/ServiceBusCopyToEventHub
