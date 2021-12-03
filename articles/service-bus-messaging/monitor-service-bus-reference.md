---
title: Azure Service Bus 모니터링 데이터 참조
description: Azure Service Bus를 모니터링할 때 필요한 중요 참조 자료
ms.topic: reference
ms.custom: subject-monitoring
ms.date: 10/29/2021
ms.openlocfilehash: cc32c5a18612ff988fcf27e28cdf35e3bb855039
ms.sourcegitcommit: 5b25f76d0fd0ffb6784a2afab808fa55b3eac07b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/03/2021
ms.locfileid: "133519563"
---
# <a name="monitoring-azure-service-bus-data-reference"></a>Azure Service Bus 모니터링 데이터 참조
Azure Service Bus 모니터링 데이터를 수집 및 분석하는 방법에 대한 자세한 내용은 [Azure Service Bus 모니터링](monitor-service-bus.md)을 참조하세요.

## <a name="metrics"></a>메트릭
이 섹션에는 Azure Service Bus에 대해 수집되는 자동 수집 플랫폼 메트릭이 모두 나열되어 있습니다. 이러한 메트릭에 대한 리소스 공급자는 **Microsoft.ServiceBus/namespaces** 입니다.

### <a name="request-metrics"></a>요청 메트릭
데이터 및 관리 작업 요청 수를 계산합니다.

| 메트릭 이름 |  진단 설정을 통해 내보내기 가능 | 단위 | 집계 유형 |  설명 | 차원 | 
| ---------- | ---------- | ----- | --- | --- | --- | 
| 들어오는 요청| Yes | 개수 | 합계 | 지정된 기간 동안 Service Bus 서비스에 대한 요청 수입니다. | EntityName | 
|성공한 요청| 아니요 | 개수 | 합계 | 지정된 기간 동안 Service Bus 서비스에 대한 성공한 요청 수입니다. | 엔터티 이름<br/>OperationResult|
|서버 오류| 아니요 | 개수 | 합계 | 지정된 기간 동안 Service Bus 서비스에서 오류로 인해 처리되지 않은 요청 수입니다. | 엔터티 이름<br/>OperationResult|
|사용자 오류 | 아니요 | 개수 | 합계 | 지정된 기간 동안 사용자 오류로 인해 처리되지 않은 요청 수입니다. | 엔터티 이름|
|제한된 요청| 아니요 | 개수 | 합계 | <p>사용량 초과로 인해 제한된 요청 수입니다.</p><p>MessagingErrorSubCode 코드 차원에 사용할 수 있는 값은 다음과 같습니다. <br/><ul><li><b>CPU:</b> CPU 제한</li><li><b>Storage:</b>보류 중인 검사점 작업으로 인해 스로틀 됨을 나타냅니다.</li><li><b>네임 스페이스:</b> 네임 스페이스 작업 제한.</li><li><b>알 수 없음:</b> 기타 리소스 제한.</li></p> |  엔터티 이름<br/>MessagingErrorSubCode 코드 |
| 보류 중인 검사점 작업 수 | No | count | 평균 | 네임 스페이스에 대 한 보류 중인 검사점 작업 수입니다. 보류 중인 검사점 수가 제한인 제한인 (50만 + (50만 * messaging unit)) 작업을 초과 하는 경우 서비스가 스로틀 되기 시작 합니다. 이 메트릭은 **프리미엄** 계층을 사용 하는 네임 스페이스에만 적용 됩니다. | MessagingErrorSubCode 코드 | 
| 서버 전송 대기 시간 | No | 밀리초 | 평균 | Service Bus 서비스에서 요청을 완료 하는 데 걸린 시간입니다. | 엔터티 이름 |


다음 두 가지 유형의 오류는 **사용자 오류** 로 분류됩니다.

1. 클라이언트 쪽 오류(HTTP의 경우 400 오류)
2. [MessageLockLostException](/dotnet/api/microsoft.azure.servicebus.messagelocklostexception)과 같은 메시지를 처리하는 동안 발생하는 오류입니다.


### <a name="message-metrics"></a>메시지 메트릭

| 메트릭 이름 |  진단 설정을 통해 내보내기 가능 | 단위 | 집계 유형 |  설명 | 차원 | 
| ---------- | ---------- | ----- | --- | --- | --- | 
|들어오는 메시지| Yes | 개수 | 합계 | 지정된 기간 동안 Service Bus에 전송된 이벤트 또는 메시지 수입니다. 해당 메트릭은 자동 전송되는 메시지를 포함하지 않습니다. | 엔터티 이름|
|보내는 메시지| Yes | 개수 | 합계 | 지정된 기간 동안 Service Bus에서 수신한 이벤트 또는 메시지 수입니다. | 엔터티 이름|
| 메시지| 예 | 개수 | 평균 | 큐/토픽에 있는 메시지 수 | 엔터티 이름 |
| 활성 메시지| 아니요 | 개수 | 평균 | 큐/토픽에 있는 활성 메시지 수 | 엔터티 이름 |
| 배달 못한 메시지| 아니요 | 개수 | 평균 | 큐/토픽에서 배달 못한 메시지 수입니다.  | 엔터티 이름 |
| 예약된 메시지| 아니요 | 개수 | 평균 | 큐/토픽에서 예약된 메시지 수입니다. | 엔터티 이름 |
|완료된 메시지| Yes | 개수 | 합계 | 지정 된 기간 동안 완료 된 메시지 수입니다. | 엔터티 이름|
| 중단된 메시지| Yes | 개수 | 합계 | 지정 된 기간 동안 중단 된 메시지 수입니다. | 엔터티 이름|
| 크기 | 예 | 바이트 | 평균 | 엔터티(큐 또는 토픽)의 크기(바이트)입니다. | 엔터티 이름 | 

> [!NOTE]
> 메시지, 활성, 배달 못한 편지, 예약, 완료 및 중단된 메시지에 대한 값은 지정 시간 값입니다. 해당 지점 시간 직후에 사용된 들어오는 메시지는 이러한 메트릭에 반영되지 않을 수 있습니다. 

### <a name="connection-metrics"></a>연결 메트릭

| 메트릭 이름 |  진단 설정을 통해 내보내기 가능 | 단위 | 집계 유형 |  설명 | 차원 | 
| ---------- | ---------- | ----- | --- | --- | --- | 
|활성 연결 수| 아니요 | 개수 | 합계 | 네임스페이스와 엔터티의 활성 연결 수입니다. 해당 메트릭의 값은 지정 시간 값입니다. 해당 시점 직후에 활성화된 연결은 메트릭에 반영되지 않을 수 있습니다. | |
|열린 연결 | 아니요 | 개수 | 평균 | 열린 연결 수입니다. | 엔터티 이름|
|닫힌 연결 | 아니요 | 개수 | 평균 | 닫힌 연결 수입니다. | 엔터티 이름|

### <a name="resource-usage-metrics"></a>리소스 사용량 메트릭

> [!NOTE] 
> 다음 메트릭은 **프리미엄** 계층에서만 제공됩니다. 
> 
> 프리미엄 계층 네임스페이스의 작동 중단을 모니터링하는 중요 메트릭은 **네임스페이스당 CPU 사용량** 및 **네임스페이스당 메모리 크기** 입니다. Azure Monitor를 사용하여 이러한 메트릭에 대한 [경고를 설정](../azure-monitor/alerts/alerts-metric.md)합니다.
> 
> 모니터링할 수 있는 다른 메트릭은 **제한된 요청** 입니다. 그렇지만 네임스페이스가 해당 메모리, CPU 및 조정된 연결 제한을 벗어나지 않으면 문제가 되지 않습니다. 자세한 내용은 [Azure Service Bus 프리미엄 계층의 제한](service-bus-throttling.md#throttling-in-azure-service-bus-premium-tier)을 참조하세요.

| 메트릭 이름 |  진단 설정을 통해 내보내기 가능 | 단위 | 집계 유형 |  설명 | 차원 | 
| ---------- | ---------- | ----- | --- | --- | --- | 
|네임스페이스당 CPU 사용량| 예 | CPU | 백분율 | 네임스페이스의 CPU 사용량 비율입니다. | 복제본 |
|네임스페이스당 메모리 크기 사용량| 예 | 메모리 사용량 | 백분율 | 네임스페이스의 메모리 사용량 비율입니다. | 복제본 |

### <a name="error-metrics"></a>오류 메트릭
| 메트릭 이름 |  진단 설정을 통해 내보내기 가능 | 단위 | 집계 유형 |  설명 | 차원 |
| ------------------- | ----------------- | --- | --- | --- | --- | 
|서버 오류| 아니요 | 개수 | 합계 | 지정된 기간 동안 Service Bus 서비스에서 오류로 인해 처리되지 않은 요청 수입니다. | 엔터티 이름<br/><br/>작업 결과 |
|사용자 오류 | 아니요 | 개수 | 합계 | 지정된 기간 동안 사용자 오류로 인해 처리되지 않은 요청 수입니다. | 엔터티 이름<br/><br/>작업 결과|

## <a name="metric-dimensions"></a>메트릭 차원

Azure Service Bus는 Azure Monitor의 메트릭에 대해 다음과 같은 차원을 지원합니다. 메트릭에 차원을 추가하는 것은 선택 사항입니다. 차원을 추가하지 않을 경우, 메트릭은 네임스페이스 수준에서 지정됩니다. 

|차원 이름|Description|
| ------------------- | ----------------- |
|엔터티 이름| Service Bus는 네임스페이스에서 메시징 엔터티를 지원합니다.|

## <a name="resource-logs"></a>리소스 로그
이 섹션에는 Azure Service Bus에 대해 수집할 수 있는 리소스 로그 유형이 나열되어 있습니다.

- 작업 로그
- 가상 네트워크 및 IP 필터링 로그

### <a name="operational-logs"></a>작업 로그
작업 로그 항목에는 다음 표에 나열된 요소가 포함됩니다.

| 속성 | Description |
| ------- | ------- |
| ActivityId | 지정된 작업을 식별하는 데 사용되는 내부 ID |
| EventName | 작업 이름 |
| ResourceId | Azure Resource Manager 리소스 ID |
| SubscriptionId | 구독 ID |
| EventTimeString | 작업 시간 |
| EventProperties | 작업 속성 |
| 상태 | 작업 상태 |
| Caller | 작업 호출자(Azure Portal 또는 관리 클라이언트) |
| 범주 | OperationalLogs |

작업 로그 JSON 문자열 예제는 다음과 같습니다.

```json
{
  "ActivityId": "0000000000-0000-0000-0000-00000000000000",
  "EventName": "Create Queue",
  "resourceId": "/SUBSCRIPTIONS/<AZURE SUBSCRPTION ID>/RESOURCEGROUPS/<RESOURCE GROUP NAME>/PROVIDERS/MICROSOFT.SERVICEBUS/NAMESPACES/<SERVICE BUS NAMESPACE NAME>",
  "SubscriptionId": "0000000000-0000-0000-0000-00000000000000",
  "EventTimeString": "9/28/2016 8:40:06 PM +00:00",
  "EventProperties": "{\"SubscriptionId\":\"0000000000-0000-0000-0000-00000000000000\",\"Namespace\":\"mynamespace\",\"Via\":\"https://mynamespace.servicebus.windows.net/f8096791adb448579ee83d30e006a13e/?api-version=2016-07\",\"TrackingId\":\"5ee74c9e-72b5-4e98-97c4-08a62e56e221_G1\"}",
  "Status": "Succeeded",
  "Caller": "ServiceBus Client",
  "category": "OperationalLogs"
}
```

### <a name="events-and-operations-captured-in-operational-logs"></a>작업 로그에 캡처된 이벤트 및 작업
작업 로그는 Azure Service Bus 네임스페이스에서 수행되는 모든 관리 작업을 캡처합니다. 데이터 작업은 Azure Service Bus에서 수행되는 작업의 볼륨이 많기 때문에 캡처되지 않습니다.

> [!NOTE]
> 데이터 작업을 보다 효율적으로 추적할 수 있도록 클라이언트 사이드 트레이싱을 사용하는 것이 좋습니다.

다음 관리 작업은 작업 로그에 캡처됩니다. 

| 범위 | 작업(Operation)|
|-------| -------- |
| 네임스페이스 | <ul> <li> 네임스페이스 만들기</li> <li> 네임스페이스 업데이트 </li> <li> 네임스페이스 삭제 </li> <li> 네임스페이스 SharedAccess 정책 업데이트 </li> </ul> | 
| 큐 | <ul> <li> 큐 만들기</li> <li> 큐 업데이트</li> <li> 큐 삭제 </li> <li> AutoDelete 큐 삭제 </li> </ul> | 
| 항목 | <ul> <li> 토픽 만들기 </li> <li> 토픽 업데이트 </li> <li> 토픽 삭제 </li> <li> AutoDelete 토픽 삭제 </li> </ul> |
| Subscription | <ul> <li> 구독 만들기 </li> <li> 구독 업데이트 </li> <li> 구독 삭제 </li> <li> AutoDelete 구독 삭제 </li> </ul> |

> [!NOTE]
> 현재 *읽기* 작업은 작업 로그에서 추적되지 않습니다.

## <a name="azure-monitor-logs-tables"></a>Azure Monitor Logs 테이블
Azure Service Bus는 Azure Monitor 로그의 Kusto 테이블을 사용합니다. Log Analytics를 사용하여 이러한 테이블을 쿼리할 수 있습니다. 서비스에서 사용하는 Kusto 테이블 목록은 [Azure Monitor 로그 테이블 참조](/azure/azure-monitor/reference/tables/tables-resourcetype#service-bus)를 참조하세요.


## <a name="next-steps"></a>다음 단계
- Azure Service Bus 모니터링에 대한 자세한 내용은 [Azure Service Bus 모니터링](monitor-service-bus.md)을 참조하세요.
- Azure 리소스 모니터링에 대한 자세한 내용은 [Azure Monitor를 사용하여 Azure 리소스 모니터링](../azure-monitor/essentials/monitor-azure-resource.md)을 참조하세요.
