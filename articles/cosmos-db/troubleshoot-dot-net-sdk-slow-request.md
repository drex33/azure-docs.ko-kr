---
title: .NET SDK를 사용하여 Azure Cosmos DB 느린 요청 문제 해결
description: Azure Cosmos DB .NET SDK를 사용할 때 느린 요청 문제를 진단하고 수정하는 방법을 알아봅니다.
author: j82w
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 06/15/2021
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: abee066553c4ee338000efd39ecd3b0bfd11d171
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122567123"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-net-sdk-slow-requests"></a>Azure Cosmos DB .NET SDK 느린 요청 진단 및 문제 해결
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

요청 제한, 애플리케이션이 설계된 방식 등의 여러 가지 이유로 Azure Cosmos DB 요청이 느려질 수 있습니다. 이 문서에서는 이 문제의 여러 가지 근본 원인을 설명합니다. 

## <a name="request-rate-too-large-429-throttles"></a>너무 많은 요청 빈도(429개 제한)

요청 제한은 느린 요청 문제의 가장 일반적인 원인입니다. Azure Cosmos DB는 요청이 데이터베이스 또는 컨테이너에 할당된 RU를 초과하면 요청을 제한합니다. SDK에는 이러한 요청을 다시 시도하는 기본 제공 논리가 포함되어 있습니다. [너무 많은 요청 빈도](troubleshoot-request-rate-too-large.md#how-to-investigate) 문제 해결 문서에서는 요청이 제한되는지 확인하는 방법과 나중에 이러한 문제를 방지하기 위해 계정을 스케일링하는 방법을 설명합니다.

## <a name="application-design"></a>애플리케이션 설계

애플리케이션이 SDK 모범 사례를 따르지 않으면 요청이 느려지거나 실패하는 다른 문제가 발생할 수 있습니다. 최적의 성능을 얻을 수 있도록 [.NET SDK 모범 사례](performance-tips-dotnet-sdk-v3-sql.md)를 준수하세요.

애플리케이션을 개발할 때 다음 사항을 고려해야 합니다.
* 애플리케이션은 Azure Cosmos DB 계정과 동일한 지역에 있어야 합니다.
* SDK 인스턴스의 싱글톤 인스턴스입니다. SDK에는 초기화해야 하는 여러 캐시가 포함되어 있으며 이로 인해 처음 몇 개 요청이 느려질 수 있습니다. 
* 직접 + TCP 연결 모드를 사용합니다.
* CPU 사용량이 너무 높아지지 않도록 합니다. 대부분의 로깅 시스템에서는 기본적으로 평균 사용량이 아닌 최대 CPU 사용량을 확인해야 합니다. 약 40%를 초과하면 대기 시간이 길어질 수 있습니다.


## <a name="capture-the-diagnostics"></a><a name="capture-diagnostics"></a>진단 캡처

`CosmosException`을 포함하여 SDK의 모든 응답에는 Diagnostics 속성이 있습니다. 이 속성은 재시도 또는 일시적 오류가 있었는지 여부를 포함하여 단일 요청과 관련된 모든 정보를 기록합니다. 

Diagnostics 속성은 문자열로 반환합니다. 여러 시나리오를 보다 잘 해결하도록 개선 버전이 나올 때마다 문자열이 변경됩니다. 각 SDK 버전에서 문자열의 서식 지정에 대한 호환성이 손상되는 변경이 발생합니다. 호환성이 손상되는 변경이 발생하지 않도록 문자열을 구문 분석하지 마세요. 다음 코드 샘플은 .NET SDK를 사용하여 진단 로그를 읽는 방법을 보여줍니다.

```c#
try
{
    ItemResponse<Book> response = await this.Container.CreateItemAsync<Book>(item: testItem);
    if (response.Diagnostics.GetClientElapsedTime() > ConfigurableSlowRequestTimeSpan)
    {
        // Log the diagnostics and add any additional info necessary to correlate to other logs 
        Console.Write(response.Diagnostics.ToString());
    }
}catch(CosmosException cosmosException){
    // Log the full exception including the stack trace 
    Console.Write(cosmosException.ToString());
    // The Diagnostics can be logged separately if required.
    Console.Write(cosmosException.Diagnostics.ToString());
}

ResponseMessage response = await this.Container.CreateItemStreamAsync(partitionKey, stream);
if (response.Diagnostics.GetClientElapsedTime() > ConfigurableSlowRequestTimeSpan || IsFailureStatusCode(response.StatusCode))
{
    // Log the diagnostics and add any additional info necessary to correlate to other logs 
    Console.Write(response.Diagnostics.ToString());
}
```


## <a name="diagnostics-in-version-319-and-higher"></a>버전 3.19 이상의 진단
SDK 버전마다 JSON 구조에는 호환성이 손상되는 변경이 포함되어 있습니다. 따라서 안전하게 구문 분석할 수 없습니다. JSON은 SDK를 통해 이동하는 요청의 트리 구조를 나타냅니다. 여기서는 다음과 같은 몇 가지 주요 사항에 대해 살펴봅니다.

### <a name="cpu-history"></a><a name="cpu-history"></a>CPU 기록
높은 CPU 사용률은 요청 속도가 느려지는 가장 일반적인 원인입니다. 최적 대기 시간을 위해 CPU 사용량은 약 40%가 되어야 합니다. 최대(평균 아님) CPU 사용률을 모니터링하려면 간격으로 10초를 사용합니다. CPU 급증은 요청이 단일 쿼리에 대해 여러 연결을 수행할 수 있는 파티션 간 쿼리에서 더 자주 나타나는 현상입니다.

오류에 `TransportException` 정보가 포함된 경우 `CPU History`도 포함될 수 있습니다.

```
CPU history: 
(2020-08-28T00:40:09.1769900Z 0.114), 
(2020-08-28T00:40:19.1763818Z 1.732), 
(2020-08-28T00:40:29.1759235Z 0.000), 
(2020-08-28T00:40:39.1763208Z 0.063), 
(2020-08-28T00:40:49.1767057Z 0.648), 
(2020-08-28T00:40:59.1689401Z 0.137), 
CPU count: 8)
```

* CPU 사용률이 70%를 초과하면 CPU 고갈로 인해 시간 초과가 발생할 수 있습니다. 이 경우 해결 방법은 높은 CPU 사용률의 원인을 조사하여 사용률을 낮추거나 머신의 리소스 크기를 늘리는 것입니다.
* CPU 측정이 10초마다 발생하지 않는 경우 격차 또는 측정 시간은 측정 간격이 더 크다는 것을 나타냅니다. 원인은 스레드 부족입니다. 해결 방법은 스레드 부족(잠재적으로 잠긴 스레드)의 원인을 조사하거나 머신의 리소스 크기를 늘리는 것입니다.

#### <a name="solution"></a>해결책:
SDK를 사용하는 클라이언트 애플리케이션을 스케일 업하거나 스케일 아웃해야 합니다.


### <a name="httpresponsestats"></a><a name="httpResponseStats"></a>HttpResponseStats
HttpResponseStats는 [게이트웨이](sql-sdk-connection-modes.md)로 이동하는 요청입니다. 심지어 직접 모드에서도 SDK는 게이트웨이에서 모든 메타데이터 정보를 가져옵니다.

요청 속도가 느리면 먼저 위의 권장 사항을 시도하여 문제를 해결해 봅니다.

여전히 속도가 느린 경우 다음과 같은 패턴으로 문제가 발생합니다.

단일 요청에 대한 단일 저장소 결과

| 요청 수 | 시나리오 | 설명 | 
|----------|-------------|-------------|
| 하나-모두 | 요청 시간 제한 또는 HttpRequestExceptions | 머신의 [SNAT 포트 고갈](troubleshoot-dot-net-sdk.md#snat) 또는 리소스 부족으로 인해 요청을 시간 내에 처리할 수 없습니다. |
| 하나 또는 적은 비율(SLA를 위반하지 않음) | 모두 | 여러 가지 일시적 문제로 인해 요청 중 하나 또는 적은 비율이 느려질 수 있으며 이는 예상할 수 있는 일입니다. | 
| 모두 | 모두 | 인프라 또는 네트워킹에 문제가 있음을 나타냅니다. |
| SLA 위반 | 애플리케이션이 변경되지 않고 SLA가 하락 | Azure Cosmos DB 서비스에 문제가 있음을 나타냅니다. |

```json
"HttpResponseStats": [
    {
        "StartTimeUTC": "2021-06-15T13:53:09.7961124Z",
        "EndTimeUTC": "2021-06-15T13:53:09.7961127Z",
        "RequestUri": "https://127.0.0.1:8081/dbs/347a8e44-a550-493e-88ee-29a19c070ecc/colls/4f72e752-fa91-455a-82c1-bf253a5a3c4e",
        "ResourceType": "Collection",
        "HttpMethod": "GET",
        "ActivityId": "e16e98ec-f2e3-430c-b9e9-7d99e58a4f72",
        "StatusCode": "OK"
    }
]
```

### <a name="storeresult"></a><a name="storeResult"></a>StoreResult
StoreResult는 TCP 프로토콜을 통해 직접 모드를 사용하는 Azure Cosmos DB에 대한 단일 요청을 나타냅니다.

여전히 속도가 느린 경우 다음과 같은 패턴으로 문제가 발생합니다.

단일 요청에 대한 단일 저장소 결과

| 요청 수 | 시나리오 | 설명 | 
|----------|-------------|-------------|
| 하나-모두 | StoreResult에 TransportException 포함 | 머신의 [SNAT 포트 고갈](troubleshoot-dot-net-sdk.md#snat) 또는 리소스 부족으로 인해 요청을 시간 내에 처리할 수 없습니다. |
| 하나 또는 적은 비율(SLA를 위반하지 않음) | 모두 | 여러 가지 일시적 문제로 인해 요청 중 하나 또는 적은 비율이 느려질 수 있으며 이는 예상할 수 있는 일입니다. | 
| 모두 | 모두 | 인프라 또는 네트워킹에 문제가 있습니다. |
| SLA 위반 | 요청에 410 및 IsValid is true와 같은 여러 오류 코드가 포함되어 있음 | Cosmos DB 서비스에 문제가 있음을 나타냅니다. |
| SLA 위반 | 요청에 410 및 IsValid is false와 같은 여러 오류 코드가 포함되어 있음 | 머신에 문제가 있음을 나타냅니다. |
| SLA 위반 | StorePhysicalAddress가 동일하고 오류 없음 상태 코드 | Cosmos DB 서비스 문제일 가능성이 높습니다. |
| SLA 위반 | StorePhysicalAddress의 파티션 ID는 같지만 복제본 ID가 서로 다르고 오류 없음 상태 코드 | Cosmos DB 서비스 문제일 가능성이 높습니다. |
| SLA 위반 | StorePhysicalAddress가 임의적이고 오류 없음 상태 코드 | 머신에 문제가 있음을 나타냅니다. |

단일 요청에 대한 여러 StoreResults:

* 강력하고 제한된 부실 일관성에는 항상 2개 이상의 저장소 결과가 있습니다.
* 각 StoreResult의 상태 코드를 확인합니다. SDK는 여러 [일시적 오류](troubleshoot-dot-net-sdk-request-timeout.md) 발생 시 자동으로 다시 시도합니다. SDK는 더 많은 시나리오를 해결하도록 지속적으로 개선되고 있습니다. 

### <a name="rntbdrequeststats"></a><a name="rntbdRequestStats"></a>RntbdRequestStats 
전송 계층에서 요청을 보내고 받는 여러 스테이지의 시간을 표시합니다.

* ChannelAcquisitionStarted: 새 연결을 획득하거나 만드는 시간입니다. 다양한 지역에 대해 새 연결을 만들 수 있습니다. 예를 들어 연결이 예기치 않게 닫혔거나 기존 연결을 통해 너무 많은 요청이 전송되면 새 연결이 생성됩니다. 
* 파이프라인 시간이 크면 요청이 큰 것일 수 있습니다.
* 전송 시간이 크면 네트워킹 문제가 발생합니다. 이 숫자를 `BELatencyInMs`와 비교합니다. BELatencyInMs가 작으면 Azure Cosmos DB 서비스가 아닌 네트워크에서 시간이 소요된 것입니다.
* 수신된 시간이 크면 스레드 고갈 문제가 있는 것입니다. 이 시간은 응답을 받는 시점과 결과를 반환하는 시점 사이의 시간입니다.

```json
"StoreResult": {
    "ActivityId": "a3d325c1-f4e9-405b-820c-bab4d329ee4c",
    "StatusCode": "Created",
    "SubStatusCode": "Unknown",
    "LSN": 1766,
    "PartitionKeyRangeId": "0",
    "GlobalCommittedLSN": -1,
    "ItemLSN": -1,
    "UsingLocalLSN": false,
    "QuorumAckedLSN": 1765,
    "SessionToken": "-1#1766",
    "CurrentWriteQuorum": 1,
    "CurrentReplicaSetSize": 1,
    "NumberOfReadRegions": 0,
    "IsClientCpuOverloaded": false,
    "IsValid": true,
    "StorePhysicalAddress": "rntbd://127.0.0.1:10253/apps/DocDbApp/services/DocDbServer92/partitions/a4cb49a8-38c8-11e6-8106-8cdcd42c33be/replicas/1p/",
    "RequestCharge": 11.05,
    "BELatencyInMs": "7.954",
    "RntbdRequestStats": [
        {
            "EventName": "Created",
            "StartTime": "2021-06-15T13:53:10.1302477Z",
            "DurationInMicroSec": "6383"
        },
        {
            "EventName": "ChannelAcquisitionStarted",
            "StartTime": "2021-06-15T13:53:10.1366314Z",
            "DurationInMicroSec": "96511"
        },
        {
            "EventName": "Pipelined",
            "StartTime": "2021-06-15T13:53:10.2331431Z",
            "DurationInMicroSec": "50834"
        },
        {
            "EventName": "Transit Time",
            "StartTime": "2021-06-15T13:53:10.2839774Z",
            "DurationInMicroSec": "17677"
        },
        {
            "EventName": "Received",
            "StartTime": "2021-06-15T13:53:10.3016546Z",
            "DurationInMicroSec": "7079"
        },
        {
            "EventName": "Completed",
            "StartTime": "2021-06-15T13:53:10.3087338Z",
            "DurationInMicroSec": "0"
        }
    ],
    "TransportException": null
}
```

### <a name="failure-rate-violates-the-azure-cosmos-db-sla"></a>실패율이 Azure Cosmos DB SLA를 위반합니다.
[Azure 지원](https://aka.ms/azure-support)에 문의하세요.

## <a name="next-steps"></a>다음 단계
* Azure Cosmos DB .NET SDK를 사용하고 있는 경우 문제를 [진단 및 해결](troubleshoot-dot-net-sdk.md)합니다.
* [.NET v3](performance-tips-dotnet-sdk-v3-sql.md) 및 [.NET v2](performance-tips.md)의 성능 지침에 대해 알아봅니다.
