---
title: Azure Monitor 로그 쿼리의 감사 쿼리
description: Azure Monitor에서 실행 되는 로그 쿼리에 대 한 원격 분석을 제공 하는 로그 쿼리 감사 로그의 세부 정보입니다.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/20/2021
ms.openlocfilehash: f08137c49b9f84fd50f7481f698956a70e5e3b45
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130233334"
---
# <a name="audit-queries-in-azure-monitor-logs-preview"></a>Azure Monitor 로그의 감사 쿼리 (미리 보기)
로그 쿼리 감사 로그는 Azure Monitor에서 실행 되는 로그 쿼리에 대 한 원격 분석을 제공 합니다. 여기에는 쿼리가 실행 된 시간, 실행 한 사람, 사용 된 도구, 쿼리 텍스트, 쿼리 실행을 설명 하는 성능 통계 등의 정보가 포함 됩니다.


## <a name="configure-query-auditing"></a>쿼리 감사 구성
쿼리 감사는 Log Analytics 작업 영역에서 [진단 설정을](../essentials/diagnostic-settings.md) 사용 하 여 사용 하도록 설정 됩니다. 이를 통해 현재 작업 영역 또는 구독의 다른 작업 영역에 감사 데이터를 전송 하거나, azure Event Hubs에 azure 외부에서 보내거나 보관을 위해 Azure Storage으로 보낼 수 있습니다. 

### <a name="azure-portal"></a>Azure Portal
다음 위치 중 하나에서 Azure Portal의 Log Analytics 작업 영역에 대 한 진단 설정에 액세스 합니다.

- **Azure Monitor** 메뉴에서 **진단 설정** 을 선택 하 고 작업 영역을 찾아 선택 합니다.

    [![진단 설정 Azure Monitor ](media/query-audit/diagnostic-setting-monitor.png) ](media/query-audit/diagnostic-setting-monitor.png#lightbox) 

- **Log Analytics 작업 영역** 메뉴에서 작업 영역을 선택한 다음 **진단 설정** 을 선택 합니다.

    [![진단 설정 Log Analytics 작업 영역 ](media/query-audit/diagnostic-setting-workspace.png) ](media/query-audit/diagnostic-setting-workspace.png#lightbox) 

### <a name="resource-manager-template"></a>Resource Manager 템플릿
[Log Analytics 작업 영역에 대 한 진단 설정](../essentials/resource-manager-diagnostic-settings.md#diagnostic-setting-for-log-analytics-workspace)에서 템플릿 리소스 관리자 예제를 가져올 수 있습니다.

## <a name="audit-data"></a>감사 데이터
쿼리가 실행 될 때마다 감사 레코드가 생성 됩니다. Log Analytics 작업 영역에 데이터를 보내는 경우이 데이터는 *Laquerylogs* 라는 테이블에 저장 됩니다. 다음 표에서는 감사 데이터의 각 레코드에 대 한 속성을 설명 합니다.

| 필드 | Description |
|:---|:---|
| TimeGenerated         | 쿼리가 제출 된 UTC 시간입니다. |
| CorrelationId         | 쿼리를 식별 하는 고유 ID입니다. Microsoft에 지원을 요청 하는 경우 문제 해결 시나리오에서 사용할 수 있습니다. |
| AADObjectId           | Azure Active Directory 쿼리를 시작한 사용자 계정의 ID입니다.  |
| AADTenantId           | 쿼리를 시작한 사용자 계정에 대 한 테 넌 트의 ID입니다.  |
| AADEmail              | 쿼리를 시작한 사용자 계정에 대 한 테 넌 트의 전자 메일입니다.  |
| AADClientId           | 쿼리를 시작 하는 데 사용 되는 응용 프로그램의 ID 및 확인 된 이름입니다. |
| RequestClientApp      | 쿼리를 시작 하는 데 사용 되는 응용 프로그램의 확인 된 이름입니다. |
| QueryTimeRangeStart   | 쿼리에 대해 선택한 시간 범위의 시작입니다. 이는 쿼리가 Log Analytics에서 시작 되는 경우와 같은 특정 시나리오에서 채워지지 않을 수 있으며 시간 범위는 시간 선택이 아닌 쿼리 내부에 지정 됩니다. |
| QueryTimeRangeEnd     | 쿼리에 대해 선택한 시간 범위의 끝입니다. 이는 쿼리가 Log Analytics에서 시작 되는 경우와 같은 특정 시나리오에서 채워지지 않을 수 있으며 시간 범위는 시간 선택이 아닌 쿼리 내부에 지정 됩니다.  |
| QueryText             | 실행된 쿼리의 텍스트입니다. |
| RequestTarget         | API URL을 사용 하 여 쿼리를 전송 했습니다.  |
| RequestContext        | 쿼리가 실행 되도록 요청한 리소스 목록입니다. 에는 작업 영역, 응용 프로그램 및 리소스의 세 가지 문자열 배열이 포함 되어 있습니다. 구독 또는 리소스 그룹-대상 쿼리는 *리소스로* 표시 됩니다. RequestTarget으로 암시 된 대상을 포함 합니다.<br>확인할 수 있는 경우 각 리소스의 리소스 ID가 포함 됩니다. 리소스에 액세스 하는 동안 오류가 반환 되 면이를 해결 하지 못할 수 있습니다. 이 경우 쿼리에서 특정 텍스트가 사용 됩니다.<br>쿼리에서 여러 구독에 존재 하는 작업 영역 이름과 같이 모호한 이름을 사용 하는 경우이 모호한 이름이 사용 됩니다. |
| RequestContextFilters | 쿼리 호출의 일부로 지정 된 필터 집합입니다. 최대 3 개의 가능한 문자열 배열을 포함 합니다.<br>-ResourceTypes-쿼리의 범위를 제한 하는 리소스의 유형입니다.<br>-작업 영역-쿼리를 제한 하는 작업 영역 목록<br>-WorkspaceRegions-쿼리를 제한 하는 작업 영역 영역 목록 |
| ResponseCode          | 쿼리가 제출 될 때 반환 되는 HTTP 응답 코드입니다. |
| ResponseDurationMs    | 응답이 반환 되는 시간입니다.  |
| ResponseRowCount     | 쿼리에서 반환한 총 이벤트 수입니다. |
| StatsCPUTimeMs       | 계산, 구문 분석 및 데이터 인출에 사용 되는 총 계산 시간입니다. 쿼리가 상태 코드 200와 함께 반환 되는 경우에만 채워집니다. |
| StatsDataProcessedKB | 쿼리를 처리 하기 위해 액세스 한 데이터의 양입니다. 대상 테이블의 크기, 사용된 시간 범위, 적용된 필터 및 참조되는 열 수의 영향을 받습니다. 쿼리가 상태 코드 200와 함께 반환 되는 경우에만 채워집니다. |
| StatsDataProcessedStart | 쿼리를 처리 하기 위해 액세스 한 가장 오래 된 데이터의 시간입니다. 쿼리 명시적 시간 범위 및 적용 된 필터의 영향을 받습니다. 데이터 분할으로 인해 명시적 시간 범위 보다 클 수 있습니다. 쿼리가 상태 코드 200와 함께 반환 되는 경우에만 채워집니다. |
| StatsDataProcessedEnd  |쿼리를 처리 하기 위해 액세스 된 최신 데이터의 시간입니다. 쿼리 명시적 시간 범위 및 적용 된 필터의 영향을 받습니다. 데이터 분할으로 인해 명시적 시간 범위 보다 클 수 있습니다. 쿼리가 상태 코드 200와 함께 반환 되는 경우에만 채워집니다. |
| StatsWorkspaceCount | 쿼리에서 액세스 하는 작업 영역 수입니다. 쿼리가 상태 코드 200와 함께 반환 되는 경우에만 채워집니다. |
| Stats지역 수 | 쿼리에서 액세스 하는 영역 수입니다. 쿼리가 상태 코드 200와 함께 반환 되는 경우에만 채워집니다. |

## <a name="considerations"></a>고려 사항

- 쿼리는 사용자 컨텍스트에서 실행 될 때만 로깅됩니다. Azure 내에서 서비스 간 서비스는 기록 되지 않습니다. 이 예외에 포함 된 두 가지 주요 쿼리 집합은 요금 청구 계산과 자동화 된 경고 실행입니다. 경고가 발생 하는 경우에는 예약 된 경고 쿼리 자체만 기록 되지 않습니다. 경고 만들기 화면에서 경고의 초기 실행은 사용자 컨텍스트에서 실행 되며 감사 용도로 사용할 수 있습니다. 
- Azure 데이터 탐색기 프록시에서 들어오는 쿼리에는 성능 통계를 사용할 수 없습니다. 이러한 쿼리에 대 한 다른 모든 데이터는 계속 채워집니다.
- [문자열 리터럴을 난독 처리](/azure/data-explorer/kusto/query/scalar-data-types/string#obfuscated-string-literals) 는 문자열에 대 한 *h* 힌트는 쿼리 감사 로그에 영향을 주지 않습니다. 쿼리는 난독 처리 되는 문자열 없이 전송 된 그대로 캡처됩니다. 이 데이터를 볼 수 있는 규정 준수 권한이 있는 사용자만 Log Analytics 작업 영역에서 사용할 수 있는 다양한 Kubernetes RBAC 또는 Azure RBAC 모드를 사용하여 이 작업을 수행할 수 있는지 확인해야 합니다.
- 여러 작업 영역의 데이터를 포함하는 쿼리의 경우 쿼리는 사용자가 액세스할 수 있는 작업 영역에서만 캡처됩니다.

## <a name="costs"></a>비용  
Azure Diagnostic 확장에 대한 비용은 없지만 데이터 수집에 대한 요금이 발생할 수 있습니다. 데이터를 수집하는 대상에 대한 [Azure Monitor 가격 책정](https://azure.microsoft.com/pricing/details/monitor/)를 확인하세요.

## <a name="next-steps"></a>다음 단계

- [진단 설정](../essentials/diagnostic-settings.md)에 대해 자세히 알아보세요.
- [로그 쿼리 최적화에](query-optimization.md)대해 자세히 알아보세요.
