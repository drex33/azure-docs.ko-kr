---
title: 작업 및 활동 모니터링
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search 서비스에서 로깅을 사용하도록 설정하고, 쿼리 작업 메트릭, 리소스 사용량 및 기타 시스템 데이터를 가져옵니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/27/2021
ms.openlocfilehash: 5e9e09508591e2b6e58b6bae17df281c23a5129d
ms.sourcegitcommit: 5163ebd8257281e7e724c072f169d4165441c326
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/21/2021
ms.locfileid: "112414794"
---
# <a name="monitor-operations-and-activity-of-azure-cognitive-search"></a>Azure Cognitive Search의 작업 및 활동 모니터링

이 문서에서는 Azure Cognitive Search의 모니터링 개념과 도구에 대한 개요가 설명됩니다. 전체적인 모니터링을 위해 기본 제공 기능과 Azure Monitor와 같은 추가 서비스의 조합을 사용해야 합니다.

다음을 추적할 수 있습니다.

* 검색 서비스: 상태 및 서비스 구성 변경
* 스토리지 사용: 사용 및 사용 가능
* 인덱스, 인덱서 및 기타 개체에 대한 개체 제한으로, 서비스 계층에 [허용되는 최대값](search-limits-quotas-capacity.md)을 기준으로 각 유형에 대한 개수가 포함됩니다.
* 쿼리 작업: 볼륨, 대기 시간, 제한되거나 삭제된 쿼리. 로그된 쿼리 요청에는 [Azure Monitor](#add-azure-monitor)가 필요합니다.
* 인덱싱 활동: Azure Monitor와 [진단 로깅](#add-azure-monitor)이 필요합니다.

검색 서비스는 사용자별 인증을 지원하지 않으므로 로그에서 사용자 ID 정보를 찾을 수 없습니다.

## <a name="built-in-monitoring"></a>기본 제공 모니터링

기본 제공 모니터링은 검색 서비스에서 로그한 활동을 나타냅니다. 진단을 제외하고 이 모니터링 수준에는 구성이 필요하지 않습니다.

Azure Cognitive Search는 포털에서 또는 이러한 [REST API](#monitoring-apis)를 통해 찾을 수 있는 서비스 상태 및 쿼리 메트릭에 대한 보고를 위해 30일 롤링 일정으로 내부 데이터를 유지합니다.

다음 스크린샷은 포털에서 모니터링 정보를 찾는 데 도움이 됩니다. 서비스 사용을 시작하는 즉시 데이터를 사용할 수 있습니다. 포털 페이지는 몇 분 마다 새로고침됩니다.

* 기본 개요 페이지에서 **모니터링** 탭은 쿼리 볼륨, 대기 시간 및 서비스가 압력하에 있는지 여부를 보여줍니다.
* 왼쪽 탐색 창의 **활동 로그** 는 Azure Resource Manager에 연결됩니다. Resource Manager가 수행한 작업에 대한 활동 로그 보고서: 서비스 가용성 및 상태, 용량 변경(복제본 및 파티션) 및 API 키 관련 활동.
* 더 아래쪽의 **모니터링** 설정은 구성 가능한 경고, 메트릭 시각화 및 진단 로그를 제공합니다. 필요할 때 이러한 항목을 만듭니다. 데이터가 수집되어 저장되면 인사이트에 대한 정보를 쿼리하거나 시각화할 수 있습니다.

  ![검색 서비스에 Azure Monitor 통합](./media/search-monitor-usage/azure-monitor-search.png
 "검색 서비스에 Azure Monitor 통합")

> [!NOTE]
> 포털 페이지는 몇 분 마다 새로고침되고, 보고된 숫자는 근사값이며, 시스템에서 요청을 처리하는 정도에 대한 일반적 개념을 제공하기 위한 것입니다. 초당 쿼리 수(QPS)와 같은 실제 메트릭은 페이지에 표시되는 숫자보다 크거나 작을 수 있습니다. 정밀도가 요구 사항인 경우 API를 사용하는 것이 좋습니다.

<a name="monitoring-apis"> </a>

### <a name="rest-apis-useful-for-monitoring"></a>모니터링에 유용한 REST API

[Postman](search-get-started-rest.md) 및 다음 API를 사용하여 포털의 모니터링 및 사용 탭에 있는 것과 동일한 정보를 검색할 수 있습니다. 시스템 정보를 얻으려면 [관리자 API 키](search-security-api-keys.md)를 제공해야 합니다.

* [서비스 통계 가져오기](/rest/api/searchservice/get-service-statistics)
* [인덱스 통계 가져오기](/rest/api/searchservice/get-index-statistics)
* [문서 개수 가져오기](/rest/api/searchservice/count-documents)
* [인덱서 상태 가져오기](/rest/api/searchservice/get-indexer-status)

### <a name="activity-logs-and-service-health"></a>활동 로그 및 서비스 상태

포털의 [**활동 로그**](../azure-monitor/essentials/activity-log.md#view-the-activity-log) 페이지는 Azure Resource Manager에서 정보를 수집하고, 서비스 상태의 변경 내용을 보고합니다. 서비스 상태와 관련된 위험, 오류 및 경고 조건에 대한 활동 로그를 모니터링할 수 있습니다.

공통 항목에는 API 키에 대한 참조(*관리자 키 가져오기* 및 *쿼리 키 가져오기* 와 같은 일반 정보 알림)가 포함됩니다. 이러한 활동은 관리자 키(개체 만들기 또는 삭제) 또는 쿼리 키를 사용하여 수행된 요청을 나타내지만 요청 자체를 표시하지는 않습니다. 이러한 시간 조직에 대한 자세한 내용은 진단 로깅을 구성해야 합니다.

**활성 로그** 는 왼쪽 탐색 창, 위쪽 창 명령 모음의 알림 또는 **진단 및 문제 해결** 페이지에서 액세스할 수 있습니다.

### <a name="monitor-storage-in-the-usage-tab"></a>사용량 탭에서 스토리지 모니터링

포털의 시각적 모니터링을 위해 **사용량** 탭에는 서비스 계층에 의해 적용되는 현재 [한도](search-limits-quotas-capacity.md)를 기준으로 리소스 가용성이 표시됩니다. [프로덕션 워크로드에 사용할 계층](search-sku-tier.md) 또는 [활성 복제본 및 파티션의 수를 조정할지 여부](search-capacity-planning.md)를 최종적으로 결정하는 경우 이러한 메트릭은 리소스 사용 속도 및 현재 구성의 기존 부하 처리에 대한 효율성을 보여줌으로써 이러한 결정을 내리는 데 도움이 됩니다.

다음 그림에서는 각 형식의 3개 개체와 50MB의 스토리지로 제한된 체험 서비스를 보여 줍니다. 기본 또는 표준 서비스의 제한이 더 높으며, 파티션 수를 늘리면 최대 스토리지 크기도 비례하여 증가합니다.

![계층 제한과 관련된 사용량 상태](./media/search-monitor-usage/usage-tab.png
 "계층 제한과 관련된 사용량 상태")

> [!NOTE]
> 현재 스토리지와 관련된 경고를 사용할 수 없습니다. 스토리지 소비는 집계되지 않거나 Azure Monitor의 **AzureMetrics** 테이블에 로그인되지 않습니다. 스토리지 경고를 가져오려면, 코드에서 스토리지 크기를 확인하고 응답을 처리하는 리소스 관련 알림을 내보내는 [사용자 지정 솔루션을 빌드](../azure-monitor/insights/solutions.md)해야 합니다.

<a name="add-azure-monitor"></a>

## <a name="add-on-monitoring-with-azure-monitor"></a>Azure Monitor를 사용하는 추가 모니터링

Azure Cognitive Search을 비롯한 많은 서비스는 추가 경고, 메트릭 및 로깅 진단 데이터를 위해 [Azure Monitor](../azure-monitor/index.yml)와 통합됩니다. 

데이터 수집 및 저장에 대한 제어를 원하는 경우, 검색 서비스에 대한 [진단 로깅을 사용하도록 설정](search-monitor-logs.md)합니다. Azure Monitor에서 캡처한 로그 이벤트는 **AzureDiagnostics** 테이블에 저장되고, 쿼리 및 인덱싱과 관련된 작업 데이터로 구성됩니다.

Azure Monitor에서 몇 가지 스토리지 옵션을 제공하며, 선택에 따라 데이터를 사용할 수 있는 방법이 결정됩니다.

* Power BI 보고서에서 [로그 데이터를 시각화](search-monitor-logs-powerbi.md)하려면 Azure Blob Storage를 선택합니다.
* Kusto 쿼리를 통해 데이터를 탐색하려면 Log Analytics을 선택합니다.

Azure Monitor에는 자체 청구 구조가 있으며, 이 섹션에서 참조하는 진단 로그에는 관련 비용이 포함됩니다. 자세한 내용은 [Azure Monitor의 사용량 및 예상 비용](../azure-monitor//usage-estimated-costs.md)을 참조하세요.

## <a name="monitor-user-access"></a>사용자 액세스 모니터링

검색 인덱스는 대규모 클라이언트 애플리케이션의 구성 요소 이므로, 인덱스에 대한 사용자별 액세스를 제어하거나 모니터링하는 기본 제공 방법이 없습니다. 요청은 관리자 또는 쿼리 요청을 포함하는 클라이언트 애플리케이션에서 온 것으로 간주됩니다. 관리자 읽기/쓰기 작업에는 전체 서비스에서 개체를 만들고, 업데이트하며, 삭제하는 작업이 포함됩니다. 읽기 전용 작업은 단일 인덱스로 범위가 지정된 문서 컬렉션에 대한 쿼리입니다. 

따라서 활동 로그에 표시되는 항목은 관리자 키 또는 쿼리 키를 사용하는 호출에 대한 참조입니다. 적절한 키가 클라이언트 코드에서 시작되는 요청에 포함됩니다. 서비스는 ID 토큰 또는 가장을 처리할 수 없습니다.

사용자별 권한 부여에 대한 비즈니스 요구 사항이 있는 경우, Azure Active Directory와의 통합이 권장됩니다. $filter 및 사용자 ID를 사용하여 사용자가 볼 수 없는 문서의 [검색 결과를 자를](search-security-trimming-for-azure-search-with-aad.md) 수 있습니다. 

$filter 매개 변수를 포함하는 쿼리 문자열 말고는 이러한 정보를 로그할 수 있는 방법은 없습니다. 보고 쿼리 문자열에 대한 자세한 내용은 [쿼리 모니터링](search-monitor-queries.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

Azure Cognitive Search와 같은 리소스를 포함하여 Azure 서비스를 감독하는 데 Azure Monitor를 능숙하게 다루는 것이 필요합니다. Azure Monitor에 익숙하지 않은 경우, 리소스와 관련된 문서를 검토하는 시간을 가지십시오. 자습서 외에도, 다음 문서로 시작하는 것이 좋습니다.

> [!div class="nextstepaction"]
> [Azure Monitor를 사용하여 Azure 리소스 모니터링](../azure-monitor/essentials/monitor-azure-resource.md)