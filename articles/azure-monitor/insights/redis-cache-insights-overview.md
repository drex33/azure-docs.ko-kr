---
title: Azure Cache for Redis용 Azure Monitor | Microsoft Docs
description: 이 문서에서는 캐시 소유자가 성능 및 사용 문제를 신속하게 이해하도록 도와주는 Azure Cache for Redis용 Azure Monitor에 대해 설명합니다.
ms.topic: conceptual
ms.date: 09/10/2020
ms.openlocfilehash: 922909d3c66d53168188257063f943385db0a3d0
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/18/2021
ms.locfileid: "130132135"
---
# <a name="explore-azure-monitor-for-azure-cache-for-redis"></a>Azure Cache for Redis용 Azure Monitor 살펴보기

모든 Azure Cache for Redis 리소스에 대해 Azure Cache for Redis용 Azure Monitor는 다음과 같은 통합된 대화형 보기를 제공합니다.

- 전반적인 성능
- 오류
- 용량
- 작동 상태

이 문서는 이 새로운 모니터링 환경의 이점을 이해하는 데 도움이 됩니다. 또한 조직의 고유한 요구 사항에 맞게 환경을 수정하고 조정하는 방법을 보여 줍니다.

## <a name="introduction"></a>소개

작업 환경을 시작하기 전에 Azure Cache for Redis용 Azure Monitor에서 정보를 시각적으로 표시하는 방법을 이해해야 합니다.

다음을 제공합니다.

- 모든 구독에서 단일 위치에 표시되는 Azure Cache for Redis 리소스에 대한 **전반적인 보기**. 범위를 평가하려는 구독 및 리소스로만 선택적으로 지정할 수 있습니다.

- 특정 Azure Cache for Redis 리소스에 대한 **드릴다운 분석**. 문제를 진단하고 사용률, 실패, 용량 및 작업에 대한 자세한 분석을 확인할 수 있습니다. 이러한 범주 중 하나를 선택하여 관련 정보에 대한 자세한 보기를 표시합니다.  

- Azure Monitor 통합 문서 템플릿을 기준으로 구축된 이 환경의 **사용자 지정** 이 환경에서는 표시되는 메트릭을 변경하고, 한도에 맞게 임계값을 수정하거나 설정할 수 있습니다. 사용자 지정 통합 문서에 변경 내용을 저장한 다음, Azure 대시보드에 통합 문서 차트를 고정할 수 있습니다.

이 기능을 사용하기 위해 설정하거나 구성해야 할 사항은 없습니다. Azure Cache for Redis 정보는 기본적으로 수집됩니다.

>[!NOTE]
>이 기능에는 무료로 액세스할 수 있습니다. [Azure Monitor 가격 책정 세부 정보](https://azure.microsoft.com/pricing/details/monitor/) 페이지에 설명된 대로 Azure Monitor 필수 기능을 구성하거나 설정하는 경우에만 요금이 부과됩니다.

## <a name="view-utilization-and-performance-metrics-for-azure-cache-for-redis"></a>Azure Cache for Redis의 사용률 및 성능 메트릭 보기

모든 구독의 스토리지 계정 사용률 및 성능을 보려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. **모니터** 를 검색하고 **모니터** 를 선택합니다.

    !["모니터"라는 단어가 포함된 검색 상자와 속도계 기호가 있는 "모니터"를 표시하는 서비스 검색 결과](./media/cosmosdb-insights-overview/search-monitor.png)

1. **Azure Cache for Redis** 를 선택합니다. 이 옵션이 표시되지 않는 경우 **더 보기** > **Azure Cache for Redis** 를 선택합니다.

### <a name="overview"></a>개요

**개요** 의 표에는 대화형 Azure Cache for Redis 메트릭이 표시됩니다. 다음 드롭다운 목록에서 선택하는 옵션을 기준으로 결과를 필터링할 수 있습니다.

- **구독**: Azure Cache for Redis 리소스가 있는 구독만 나열됩니다.  

- **Azure Cache for Redis**: 모두, 하위 세트 또는 단일 Azure Cache for Redis 리소스를 선택할 수 있습니다.

- **시간 범위**: 기본적으로는 표에 선택한 항목을 기준으로 지난 4시간의 정보가 표시됩니다.

드롭다운 목록 아래에는 카운터 타일이 있습니다. 이 타일에는 선택한 구독의 총 Azure Cache for Redis 리소스 수가 표시됩니다. 통합 문서 열의 조건부 색 코드 또는 열 지도는 트랜잭션 메트릭을 보고합니다. 가장 짙은 색이 가장 높은 값을 나타냅니다. 밝은 색은 낮은 값을 나타냅니다.

Azure Cache for Redis 리소스 중 하나의 옆에 있는 드롭다운 목록 화살표를 선택하면 다음과 같이 개별 리소스 수준에서 성능 메트릭 분석 결과가 표시됩니다.

![개요 환경 스크린샷](./media/redis-cache-insights-overview/overview.png)

파란색으로 강조 표시된 Azure Cache for Redis 리소스 이름을 선택하면 연결된 계정에 대한 기본 **개요** 표가 표시됩니다. 다음 열이 표시됩니다.

- **사용된 메모리**
- **사용된 메모리 비율**
- **서버 부하**
- **서버 부하 타임라인**
- **CPU**
- **연결된 클라이언트**
- **캐시 누락**
- **오류(최대)**

### <a name="operations"></a>작업

페이지 맨 위에서 **작업** 을 선택하면 통합 문서 템플릿의 **작업** 표가 열립니다. 다음 열이 표시됩니다.

- **총 작업**
- **총 작업 타임라인**
- **초당 작업**
- **가져오기**
- **설정**

![운영 환경 스크린샷](./media/redis-cache-insights-overview/operations.png)

### <a name="usage"></a>사용

페이지 맨 위에서 **사용량** 을 선택하면 통합 문서 템플릿의 **사용량** 표가 열립니다. 다음 열이 표시됩니다.

- **캐시 읽기**
- **캐시 읽기 타임라인**
- **캐시 쓰기**
- **캐시 적중 수**
- **캐시 누락**

![사용량 환경 스크린샷](./media/redis-cache-insights-overview/usage.png)

### <a name="failures"></a>오류

페이지 맨 위에서 **실패** 를 선택하면 통합 문서 템플릿의 **실패** 표가 열립니다. 다음 열이 표시됩니다.

- **총 오류**
- **장애 조치(Failover)/오류**
- **UnresponsiveClient/오류**
- **RDB/오류**
- **AOF/오류**
- **내보내기/오류**
- **데이터 손실/오류**
- **가져오기/오류**

![HTTP 요청 유형별 분석이 포함된 실패 스크린샷](./media/redis-cache-insights-overview/failures.png)

### <a name="metric-definitions"></a>메트릭 정의

이러한 통합 문서를 구성하는 메트릭 정의의 전체 목록은 [사용 가능한 메트릭 및 보고 간격에 대한 문서](../../azure-cache-for-redis/cache-how-to-monitor.md#available-metrics-and-reporting-intervals)를 확인하세요.

## <a name="view-from-an-azure-cache-for-redis-resource"></a>Azure Cache for Redis 리소스에서 보기

개별 리소스에서 직접 Azure Cache for Redis용 Azure Monitor에 액세스하려면

1. Azure Portal에서 Azure Cache for Redis를 선택합니다.

2. 목록에서 개별 Azure Cache for Redis 리소스를 선택합니다. 모니터링 섹션에서 인사이트를 선택합니다.

    ![빨간색 상자에 강조 표시된 "인사이트" 단어가 표시되는 메뉴 옵션 스크린샷](./media/redis-cache-insights-overview/insights.png)

Azure Monitor 수준 통합 문서에서 Azure Cache for Redis 리소스의 리소스 이름을 선택하여 이러한 보기에 액세스할 수도 있습니다.

### <a name="resource-level-overview"></a>리소스 수준 개요

Azure Redis Cache에 대한 **개요** 통합 문서에는 다음에 액세스할 수 있는 몇 가지 성능 메트릭이 표시됩니다.

- Azure Cache for Redis 성능과 관련된 가장 중요한 정보를 보여 주는 대화형 성능 차트

- 분할된 데이터베이스 성능, 연결된 총 클라이언트 수 및 전체 대기 시간을 강조 표시하는 메트릭 및 상태 타일

![CPU 성능, 사용된 메모리, 연결된 클라이언트, 오류, 만료된 키 및 제거된 키에 대한 정보를 표시하는 개요 대시보드 스크린샷](./media/redis-cache-insights-overview/resource-overview.png)

**성능** 또는 **작업** 에 대한 다른 탭을 선택하면 해당 통합 문서가 열립니다.

### <a name="resource-level-performance"></a>리소스 수준 성능

![리소스 성능 그래프 스크린샷](./media/redis-cache-insights-overview/resource-performance.png)

### <a name="resource-level-operations"></a>리소스 수준 작업

![리소스 작업 그래프 스크린샷](./media/redis-cache-insights-overview/resource-operations.png)

## <a name="pin-export-and-expand"></a>고정, 내보내기 및 확장

메트릭 섹션을 [Azure 대시보드](../../azure-portal/azure-portal-dashboards.md)에 고정하려면 섹션의 오른쪽 위에 있는 압정 기호를 선택합니다.

![압정 기호가 강조 표시된 메트릭 섹션](./media/cosmosdb-insights-overview/pin.png)

데이터를 Excel 형식으로 내보내려면 압정 기호 왼쪽에 있는 아래쪽 화살표 기호를 선택합니다.

![강조 표시된 통합 문서 내보내기 기호](./media/cosmosdb-insights-overview/export.png)

통합 문서의 모든 보기를 확장 또는 축소하려면 내보내기 기호 왼쪽에 있는 확장 기호를 선택합니다.

![강조 표시된 통합 문서 확장 기호](./media/cosmosdb-insights-overview/expand.png)

## <a name="customize-azure-monitor-for-azure-cache-for-redis"></a>Azure Cache for Redis용 Azure Monitor 사용자 지정

이 환경은 Azure Monitor 통합 문서 템플릿을 기준으로 구축되었으므로 **사용자 지정** > **편집** > **저장** 을 선택하여 수정된 버전의 복사본을 사용자 지정 통합 문서에 복사할 수 있습니다.

![사용자 지정이 강조 표시된 명령 모음](./media/cosmosdb-insights-overview/customize.png)

통합 문서는 **내 보고서** 섹션 또는 **공유 보고서** 섹션의 리소스 그룹 내에 저장됩니다. **내 보고서** 는 해당 사용자만 사용할 수 있습니다. **공유 보고서** 은 리소스 그룹에 액세스할 수 있는 모든 사용자가 사용할 수 있습니다.

사용자 지정 통합 문서를 저장한 후에는 통합 문서 갤러리로 이동하여 엽니다.

![갤러리가 강조 표시된 명령 모음](./media/cosmosdb-insights-overview/gallery.png)

## <a name="troubleshooting"></a>문제 해결

문제 해결 지침은 전용 통합 문서 기반 인사이트 [문제 해결 문서](troubleshoot-workbooks.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* [메트릭 경고](../alerts/alerts-metric.md) 및 [서비스 상태 알림](../../service-health/alerts-activity-log-service-notifications-portal.md)을 구성하여 문제 발견에 도움이 되는 자동 경고를 설정합니다.

* [Azure Monitor 통합 문서를 사용하여 대화형 보고서 만들기](../visualize/workbooks-overview.md)를 검토하여 통합 문서에서 지원하는 시나리오, 보고서를 작성하고 사용자 지정하는 방법 등을 알아보세요.
