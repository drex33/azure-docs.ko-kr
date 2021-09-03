---
title: Azure Event Hubs 데이터 참조 모니터링
description: Azure Event Hubs를 모니터링할 때 필요한 중요 참조 자료입니다.
ms.topic: reference
ms.custom: subject-monitoring
ms.date: 06/11/2021
ms.openlocfilehash: 4dc40cef074a223a2cc34e3a87c9acae840f8dbf
ms.sourcegitcommit: 0beea0b1d8475672456da0b3a4485d133283c5ea
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/28/2021
ms.locfileid: "112992048"
---
# <a name="monitoring-azure-event-hubs-data-reference"></a>Azure Event Hubs 데이터 참조 모니터링
Azure Event Hubs에 대한 모니터링 데이터의 수집 및 분석에 대한 자세한 내용은 [Azure Event Hubs 모니터링](monitor-event-hubs.md)을 참조하세요.

## <a name="metrics"></a>메트릭
해당 섹션에서는 Azure Event Hubs에 대해 수집되는 모든 자동 수집 플랫폼 메트릭을 나열합니다. 이러한 메트릭에 대한 리소스 공급자는 **Microsoft.EventHub/clusters** 또는 **Microsoft.EventHub/clusters** 입니다.

### <a name="request-metrics"></a>요청 메트릭
데이터 및 관리 작업 요청 수를 계산합니다.

| 메트릭 이름 |  진단 설정을 통해 내보내기 가능 | 단위 | 집계 유형 |  설명 | 차원 | 
| ---------- | ---------- | ----- | --- | --- | --- | 
| 들어오는 요청| Yes | 개수 | 합계 | 지정된 기간 동안 Event Hubs 서비스에 대한 요청 수입니다. | 엔터티 이름| 
| 성공한 요청| 아니요 | 개수 | 합계 | 지정된 기간 동안 Event Hubs 서비스에 대한 성공한 요청 수입니다. |  엔터티 이름<br/><br/>작업 결과 | 
| 제한된 요청| 아니요 | 개수 | 합계 |  사용량 초과로 인해 제한된 요청 수입니다. | 엔터티 이름<br/><br/>작업 결과 |

다음 두 가지 유형의 오류는 **사용자 오류** 로 분류됩니다.

1. 클라이언트 쪽 오류(HTTP의 경우 400 오류)
2. 메시지를 처리하는 동안 발생하는 오류입니다.


### <a name="message-metrics"></a>메시지 메트릭
| 메트릭 이름 |  진단 설정을 통해 내보내기 가능 | 단위 | 집계 유형 |  설명 | 차원 | 
| ---------- | ---------- | ----- | --- | --- | --- | 
|들어오는 메시지|  Yes | 개수 | 합계 | 지정된 기간 동안 Event Hubs에 전송된 이벤트 또는 메시지 수입니다. | 엔터티 이름|
|보내는 메시지| Yes | 개수 | 합계 | 지정된 기간 동안 Event Hubs에서 받은 이벤트 또는 메시지 수입니다. | 엔터티 이름 | 
| 캡처된 메시지| 아니요 | 개수| 합계 | 캡처된 메시지 수입니다.  |  엔터티 이름 | 
|들어오는 바이트 | 예 |  바이트 | 합계 | 지정된 기간 동안 이벤트 허브의 들어오는 바이트입니다.  | 엔터티 이름| 
|보내는 바이트 | 예 |  바이트 | 합계 |지정된 기간 동안 이벤트 허브의 나가는 바이트입니다.  | 엔터티 이름 | 
| 크기 | 예 |  바이트 | 평균 |  이벤트 허브의 크기(바이트)입니다.|엔터티 이름 |


> [!NOTE]
> 이러한 특정 시점 값입니다. 해당 지점 시간 직후에 사용된 들어오는 메시지는 이러한 메트릭에 반영되지 않을 수 있습니다. 

### <a name="capture-metrics"></a>메트릭 캡처
| 메트릭 이름 |  진단 설정을 통해 내보내기 가능 | 단위 | 집계 유형 |  설명 | 차원 | 
| ------------------- | ----------------- | --- | --- | --- | --- | 
| 캡처된 메시지| 아니요 | 개수| 합계 | 캡처된 메시지 수입니다.  | 엔터티 이름 |
| 캡처된 바이트 | 아니요 | 바이트 | 합계 | 이벤트 허브에 대해 캡처된 바이트 | 엔터티 이름 | 
| 캡처 백로그 | 아니요 | 개수| 합계 | 이벤트에 대한 백로그 캡처 | 엔터티 이름 | 


### <a name="connection-metrics"></a>연결 메트릭
| 메트릭 이름 |  진단 설정을 통해 내보내기 가능 | 단위 | 집계 유형 |  설명 | 차원 | 
| ------------------- | ----------------- | --- | --- | --- | --- | 
|활성 연결 수| 아니요 | 개수 | 평균 | 네임스페이스와 네임스페이스의 엔터티(이벤트 허브)에 있는 활성 연결 수입니다. 해당 메트릭의 값은 지정 시간 값입니다. 해당 시점 직후에 활성화된 연결은 메트릭에 반영되지 않을 수 있습니다.| 엔터티 이름 | 
|열린 연결 | 아니요 | 개수 | 평균 |  열린 연결 수입니다. | 엔터티 이름 | 
|닫힌 연결 | 아니요 | 개수 | 평균|  닫힌 연결 수입니다. | 엔터티 이름 | 

### <a name="error-metrics"></a>오류 메트릭
| 메트릭 이름 |  진단 설정을 통해 내보내기 가능 | 단위 | 집계 유형 |  설명 | 차원 |
| ------------------- | ----------------- | --- | --- | --- | --- | 
|서버 오류| 아니요 | 개수 | 합계 | 지정된 기간 동안 Event Hubs 서비스에서 오류로 인해 처리되지 않은 요청 수입니다. | 엔터티 이름<br/><br/>작업 결과 |
|사용자 오류 | 아니요 | 개수 | 합계 | 지정된 기간 동안 사용자 오류로 인해 처리되지 않은 요청 수입니다. | 엔터티 이름<br/><br/>작업 결과|
|할당량 초과 오류 | 아니요 |개수 | 합계 | 지정된 기간 동안 할당량을 초과하여 발생한 오류 수입니다. | 엔터티 이름<br/><br/>작업 결과|

> [!NOTE]
> Logic Apps는 epoch 수신자를 만들고 수신자는 서비스 부하에 따라 노드 간에 이동할 수 있습니다. 이동 중에 `ReceiverDisconnection` 예외가 발생할 수 있습니다. 해당 예외는 Event Hubs 서비스 쪽에서 사용자 오류로 계산됩니다. Logic Apps가 Event Hubs 클라이언트에서 오류를 수집할 수 있으므로 사용자 로그에서 해당 오류를 볼 수 있습니다.

## <a name="metric-dimensions"></a>메트릭 차원

Azure Event Hubs는 Azure Monitor의 메트릭에 대해 다음과 같은 차원을 지원합니다. 메트릭에 차원을 추가하는 것은 선택 사항입니다. 차원을 추가하지 않을 경우, 메트릭은 네임스페이스 수준에서 지정됩니다. 

|차원 이름|Description|
| ------------------- | ----------------- |
|엔터티 이름| 이벤트 허브의 이름입니다.|

## <a name="resource-logs"></a>리소스 로그
[!INCLUDE [event-hubs-diagnostic-log-schema](./includes/event-hubs-diagnostic-log-schema.md)]



## <a name="azure-monitor-logs-tables"></a>Azure Monitor Logs 테이블
Azure Event Hubs는 Azure Monitor 로그에서 Kusto 테이블을 사용합니다. Log Analytics를 사용하여 해당 테이블을 쿼리할 수 있습니다. 서비스에서 사용하는 Kusto 테이블 목록은 [Azure Monitor 로그 테이블 참조](/azure/azure-monitor/reference/tables/tables-resourcetype#event-hubs)를 참조하세요.


## <a name="next-steps"></a>다음 단계
- Azure Event Hubs 모니터링에 대한 자세한 내용은 [Azure Event Hubs 모니터링](monitor-event-hubs.md)을 참조하세요.
- Azure 리소스 모니터링에 대한 자세한 내용은 [Azure Monitor를 사용하여 Azure 리소스 모니터링](../azure-monitor/essentials/monitor-azure-resource.md)을 참조하세요.
