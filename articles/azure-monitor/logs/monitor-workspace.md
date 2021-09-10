---
title: Azure Monitor에서 Log Analytics 작업 영역의 상태 모니터링
description: Operation 테이블의 데이터를 사용하여 Log Analytics 작업 영역의 상태를 모니터링하는 방법을 설명합니다.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/20/2020
ms.openlocfilehash: ca35dc76107ca63dec885d34ebac9c9798334edb
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122690648"
---
# <a name="monitor-health-of-log-analytics-workspace-in-azure-monitor"></a>Azure Monitor에서 Log Analytics 작업 영역의 상태 모니터링
Azure Monitor에서 Log Analytics 작업 영역의 성능 및 가용성을 유지 관리하려면 발생하는 모든 문제를 사전에 검색할 수 있어야 합니다. 이 문서에서는 [Operation](/azure/azure-monitor/reference/tables/operation) 테이블의 데이터를 사용하여 Log Analytics 작업 영역의 상태를 모니터링하는 방법을 설명합니다. 이 테이블은 모든 Log Analytics 작업 영역에 포함되며 작업 영역에서 발생하는 오류 및 경고를 포함합니다. "경고" 및 "오류" 수준의 문제에 대한 경고를 생성하는 것이 좋습니다.

## <a name="_logoperation-function"></a>_LogOperation 함수

Azure Monitor 로그는 문제가 발생한 작업 영역의 [Operation](/azure/azure-monitor/reference/tables/operation) 테이블에 모든 문제에 관한 세부 정보를 보냅니다. **_LogOperation** 시스템 함수는 **Operation** 테이블을 기반으로 하며 분석 및 경고에 관한 간단한 정보 세트를 제공합니다.

## <a name="columns"></a>열

**_LogOperation** 함수는 다음 테이블의 열을 반환합니다.

| 열 | Description |
|:---|:---|
| TimeGenerated | 인시던트가 발생한 시간(UTC)입니다. |
| 범주  | 작업 범주 그룹입니다. 작업 유형을 필터링하는 데 사용되고 더 정확한 시스템 감사 및 경고를 만드는 데 도움이 될 수 있습니다. 범주 목록은 아래 섹션을 참조하세요. |
| 작업(Operation)  | 작업 유형의 설명입니다. 이 작업은 Log Analytics 제한 중 하나에 도달함, 백 엔드 프로세스 관련 문제 또는 기타 서비스 메시지를 나타낼 수 있습니다. |
| Level | 문제의 심각도 수준입니다.<br>- 정보: 특정 주의가 필요하지 않습니다.<br>- 경고: 프로세스가 예상대로 완료되지 않았으므로 주의가 필요합니다.<br>- 오류: 프로세스가 실패했습니다.주의가 필요합니다. 
| 세부 정보 | 작업의 자세한 설명에는 특정 오류 메시지가 포함됩니다. |
| _ResourceId | 작업과 관련된 Azure 리소스의 리소스 ID입니다.  |
| Computer | 작업이 Azure Monitor 에이전트와 관련된 경우 컴퓨터 이름입니다. |
| CorrelationId | 연속 관련 작업을 그룹화하는 데 사용됩니다. |


## <a name="categories"></a>범주

다음 표에서는 _LogOperation 함수의 범주를 설명합니다. 

| 범주 | Description |
|:---|:---|
| 수집           | 데이터 수집 프로세스의 일부인 작업입니다. |
| 에이전트               | 에이전트 설치와 관련된 문제를 나타냅니다. |
| 데이터 수집     | 데이터 수집 프로세스와 관련된 작업입니다. |
| 솔루션 대상 지정  | *ConfigurationScope* 유형의 작업이 처리되었습니다. |
| 평가 솔루션 | 평가 프로세스가 실행되었습니다. |


### <a name="ingestion"></a>수집
수집 작업은 Azure Log Analytics 작업 영역 한도 도달에 관한 알림을 포함하여 데이터 수집 중에 발생한 문제입니다. 이 범주의 오류 조건은 데이터 손실을 나타낼 수 있으므로 모니터링하는 것이 중요합니다. 다음 표에서는 이 작업을 자세히 설명합니다. Log Analytics 작업 영역의 서비스 한도는 [Azure Monitor 서비스 한도](../service-limits.md#log-analytics-workspaces)를 참조하세요.

 
#### <a name="operation-data-collection-stopped"></a>작업: 데이터 수집이 중지되었습니다.  
“무료 데이터의 일일 한도에 도달하여 데이터 수집이 중지되었습니다. 수집 상태 = OverQuota”

지난 7일 동안 로그 수집이 일일 설정 제한에 도달했습니다. 작업 영역이 "무료 계층"으로 설정되었거나 이 작업 영역에 대해 일일 수집 제한이 구성되었으므로 제한이 설정됩니다.
설정된 제한에 도달한 후에는 해당 날짜에 대한 데이터 수집이 자동으로 중지되며 다음 수집일 동안에만 다시 시작됩니다. 
 
권장 작업: 
*   수집이 중지되었고 수집이 재개된 이벤트에 대해 _LogOperation 테이블을 확인합니다.</br>
`_LogOperation | where TimeGenerated >= ago(7d) | where Category == "Ingestion" | where Operation has "Data collection"`
*   "데이터 수집 중지됨" 작업 이벤트에 대해 [경고를 만듭니다](./manage-cost-storage.md#alert-when-daily-cap-is-reached). 이 경고를 통해 컬렉션 제한에 도달했을 때 경고를 받을 수 있습니다.
*   일일 수집 한도에 도달한 후 수집된 데이터는 손실됩니다. ‘작업 영역 통찰력’ 블레이드를 사용하여 각 원본의 사용률을 검토합니다. 또는 [최대 일일 데이터 볼륨 관리](./manage-cost-storage.md#manage-your-maximum-daily-data-volume) \ [가격 책정 계층 변경](./manage-cost-storage.md#changing-pricing-tier)에서 수집 비율 패턴에 맞는 계층으로 변경할 수 있습니다. 
* 데이터 수집 비율은 매일 계산되며 다음 날 시작 시 재설정됩니다. "데이터 수집 다시 시작됨" 작업 이벤트에 대한 [경고 만들기](./manage-cost-storage.md#alert-when-daily-cap-is-reached)를 통해 수집 다시 시작 이벤트를 모니터링할 수도 있습니다.

#### <a name="operation-ingestion-rate"></a>작업: 수집 비율
“데이터 수집 비율이 작업 영역에서 임계값인 1분당 {0:0.00}MB를 초과했고 데이터가 삭제되었습니다.” 

권장 작업:
*   수집 비율 이벤트 `_LogOperation | where TimeGenerated >= ago(7d) | where Category == "Ingestion" | where Operation has "Ingestion rate"`에 대해 _LogOperation 테이블을 확인합니다. 
  참고: 작업 영역의 작업 테이블은 임계값을 계속 초과하는 동안 6시간마다 표시됩니다. 
*   "데이터 수집 중지됨" 작업 이벤트에 대해 [경고를 만듭니다](./manage-cost-storage.md#alert-when-daily-cap-is-reached).이 경고를 통해 한도에 도달하면 알림을 받을 수 있습니다.
*   수집 비율이 100%에 도달하는 동안 수집된 데이터는 삭제되고 손실됩니다. 

'작업 영역 정보' 블레이드에서 사용 패턴을 검토하고 이를 줄이세요.</br>
자세한 내용은 다음을 참조하세요. </br>
[Azure Monitor 서비스 제한](../service-limits.md#data-ingestion-volume-rate) </br>
[Azure Monitor 로그의 사용량 및 비용 관리](./manage-cost-storage.md#alert-when-daily-cap-is-reached)  

 
#### <a name="operation-maximum-table-column-count"></a>작업: 최대 테이블 열 수
“필드 수(\<**new fields count**\>)가 데이터 형식당 사용자 지정 필드 \<**current field count limit**\>개의 한도를 초과하므로 \<**table name**\> 형식 데이터가 삭제되었습니다.” 

권장 작업: 사용자 지정 테이블의 경우 쿼리에서 [데이터 구문 분석](./parse-text.md)으로 이동할 수 있습니다.

#### <a name="operation-field-content-validation"></a>작업: 필드 콘텐츠 유효성 검사
“\<**table name**\> 형식의 다음 \<**field name**\> 필드 값이 최대 허용되는 크기(\<**field size limit**\>바이트)로 잘렸습니다. 이에 따라 입력을 조정하세요.” 

한도 크기보다 큰 필드는 Azure 로그에서 처리되었고, 필드가 허용된 필드 한도로 잘렸습니다. 허용된 한도보다 큰 필드를 보내면 데이터가 손실될 수 있으므로 해당 필드를 보내지 않는 것이 좋습니다. 

권장 작업: 영향을 받는 데이터 형식의 원본을 확인합니다.
*   데이터가 HTTP Data Collector API를 통해 전송되는 경우 데이터를 수집하기 전에 분할하도록 code\script를 변경해야 합니다.
*   Log Analytics 에이전트에서 수집하는 사용자 지정 로그의 경우 application\tool의 로깅 설정을 변경합니다.
*   다른 데이터 형식의 경우 지원 사례를 제기합니다. 
</br>자세히 보기: [Azure Monitor 서비스 제한](../service-limits.md#data-ingestion-volume-rate) 

### <a name="data-collection"></a>데이터 수집
#### <a name="operation-azure-activity-log-collection"></a>작업: Azure 활동 로그 수집
“구독에 대한 액세스가 손실되었습니다. \<**subscription id**\> 구독이 \<**tenant id**\> Azure Active Directory 테넌트에 있는지 확인합니다. 구독이 다른 테넌트로 전송되는 경우 서비스에는 영향을 주지 않지만 테넌트 정보를 전파하는 데 최대 1시간이 걸릴 수 있습니다. '"

설명: 구독을 다른 테넌트로 이동하는 경우와 같은 일부 상황에서는 Azure 활동 로그가 작업 영역으로 이동하는 것을 중지할 수 있습니다. 이러한 상황에서는 이 문서에 설명된 프로세스에 따라 구독을 다시 연결해야 합니다.

권장 작업: 
* 경고 메시지에 언급된 구독이 더 이상 존재하지 않는 경우 ‘작업 영역 데이터 원본’에서 Azure 활동 로그 블레이드로 이동하고 관련 구독을 선택한 다음 마지막으로 ‘연결 끊기’ 단추를 선택합니다.
* 경고 메시지에 언급된 구독에 더 이상 액세스할 수 없는 경우:
  * 1단계에 따라 구독 연결을 끊습니다. 
  * 이 구독에서 로그를 계속 수집하려면 구독 소유자에게 문의하여 사용 권한을 수정하고 활동 로그 수집을 다시 사용하도록 설정합니다.
* 활동 로그를 Log Analytics 작업 영역으로 전송하는 [진단 설정을 만듭니다](../essentials/activity-log.md#send-to-log-analytics-workspace). 

### <a name="agent"></a>에이전트
#### <a name="operation-linux-agent"></a>작업: Linux 에이전트
“OMS 설정의 연속 구성 애플리케이션 두 개가 실패했습니다.”

포털의 구성 설정이 변경되었습니다.

권장 작업 에이전트에서 새 구성 설정을 검색하는 데 문제가 있는 경우 이 문제가 발생합니다. 이 문제를 완화하려면 에이전트를 다시 설치해야 합니다. 에이전트 이벤트에 대해 _LogOperation 테이블을 확인합니다.</br>

 `_LogOperation | where TimeGenerated >= ago(6h) | where Category == "Agent" | where Operation == "Linux Agent"  | distinct _ResourceId`

목록에는 에이전트에 잘못된 구성이 있는 리소스 ID가 나열됩니다.
이 문제를 완화하려면 나열된 에이전트를 다시 설치해야 합니다.

   

## <a name="alert-rules"></a>경고 규칙
Azure Monitor의 [로그 쿼리 경고](../alerts/alerts-log-query.md)를 사용하여 Log Analytics 작업 영역에서 문제가 검색되는 경우 사전에 알림을 받습니다. 비용을 최소화하면서 문제에 시기 적절하게 대응할 수 있는 전략을 사용합니다. [Azure Monitor 가격 책정](https://azure.microsoft.com/pricing/details/monitor/#platform-logs)에 나열된 대로 각 경고 규칙에 대해 구독 요금이 청구됩니다.

권장되는 전략은 문제 수준에 따라 두 개의 경고 규칙을 시작하는 것입니다. 오류의 경우 5분마다 같은 짧은 빈도를 사용하고 경고의 경우 24시간 같은 더 긴 빈도를 사용합니다. 오류는 잠재적 데이터 손실을 나타내므로 신속하게 대응하여 손실을 최소화하려고 합니다. 경고는 일반적으로 즉각적인 주의가 필요하지 않은 문제를 나타내므로 매일 검토할 수 있습니다.

[Azure Monitor를 사용하여 로그 경고 만들기, 보기 및 관리](../alerts/alerts-log.md)의 프로세스를 사용하여 로그 경고 규칙을 만듭니다. 다음 섹션에서는 각 규칙의 세부 정보를 설명합니다.


| 쿼리 | 임계값 | 기간 | 빈도 |
|:---|:---|:---|:---|
| `_LogOperation | where Level == "Error"`   | 0 | 5 | 5 |
| `_LogOperation | where Level == "Warning"` | 0 | 1440 | 1440 |

이 경고 규칙은 오류 또는 경고가 있는 모든 작업에 동일하게 대응합니다. 경고를 생성하는 작업에 더 익숙해짐에 따라 특정 작업에 다르게 대응하려고 할 수 있습니다. 예를 들어, 특정 작업에 관한 알림을 다른 사용자에게 보내려고 할 수 있습니다. 

특정 작업의 경고 규칙을 만들려면 **범주** 및 **작업** 열을 포함하는 쿼리를 사용합니다. 

다음 예제에서는 수집 볼륨 비율이 한도의 80%에 도달했을 때 경고 알림을 만듭니다.

- 대상: Log Analytics 작업 영역 선택
- 조건:
  - 신호 이름: 사용자 지정 로그 검색
  - 검색 쿼리: `_LogOperation | where Category == "Ingestion" | where Operation == "Ingestion rate" | where Level == "Warning"`
  - 기준: 결과의 수
  - 조건: 초과
  - 임계값: 0
  - 기간: 5분
  - 빈도: 5분
- 경고 규칙 이름: 일일 데이터 한계 도달
- 심각도: 경고(심각도 1)


다음 예제에서는 데이터 수집이 일일 한도에 도달했을 때 경고 알림을 만듭니다. 

- 대상: Log Analytics 작업 영역 선택
- 조건:
  - 신호 이름: 사용자 지정 로그 검색
  - 검색 쿼리: `_LogOperation | where Category == "Ingestion" | where Operation == "Data collection Status" | where Level == "Warning"`
  - 기준: 결과의 수
  - 조건: 초과
  - 임계값: 0
  - 기간: 5분
  - 빈도: 5분
- 경고 규칙 이름: 일일 데이터 한계 도달
- 심각도: 경고(심각도 1)
 
## <a name="next-steps"></a>다음 단계

- [로그 경고](../alerts/alerts-log.md)에 관해 자세히 알아봅니다.
- 작업 영역의 [쿼리 감사 데이터를 수집](./query-audit.md)합니다.
