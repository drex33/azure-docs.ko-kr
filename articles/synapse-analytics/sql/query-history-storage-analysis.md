---
title: Azure Synapse Analytics의 기록 쿼리 저장소 및 분석
description: 기록 쿼리 분석은 데이터 엔지니어의 중요 한 요구 중 하나입니다. Azure Synapse Analytics는 쿼리 기록과 성능을 분석 하는 네 가지 주요 방법을 지원 합니다. 여기에는 쿼리 저장소, Dmv, Azure Log Analytics, Azure 데이터 탐색기가 포함 됩니다.
author: mariyaali
ms.author: mariyaali
ms.reviewer: wiassaf
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 10/28/2021
ms.custom: template-concept
ms.openlocfilehash: 2e018b87caee86616a12d817d8c628eff93c7a3d
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132720153"
---
# <a name="historical-query-storage-and-analysis-in-azure-synapse-analytics"></a>Azure Synapse Analytics의 기록 쿼리 저장소 및 분석

기록 쿼리 분석은 데이터 엔지니어의 중요 한 요구 중 하나입니다. Azure Synapse Analytics는 쿼리 기록과 성능을 분석 하는 네 가지 주요 방법을 지원 합니다. 여기에는 쿼리 저장소, Dmv, Azure Log Analytics, Azure 데이터 탐색기가 포함 됩니다. 

이 문서에서는 필요에 따라 이러한 각 옵션을 사용 하는 방법을 보여 줍니다. 쿼리 기록을 분석할 때 사용 사례를 검토 하 고 각각에 대해 가장 적합 한 방법을 검토 합니다.

| **고객 요구** |  **쿼리 저장소** |  **DMV**    | **Azure Log Analytics** | **Azure Data Explorer** |
|------------- | --- | ----- | ------------- |-------------------|
|**기본 솔루션** | 활성화 필요 | :heavy_check_mark: | 추가 서비스가 필요 합니다. |    추가 서비스가 필요 합니다.|
|**더 긴 분석 기간** | 30일 |    최대 1만 행의 기록     | 사용자 지정 가능 | 사용자 지정 가능|
|**중요 한 메트릭 가용성** |    제한됨    | :heavy_check_mark: |    제한됨    | 사용자 지정 가능|
|**분석에 SQL 사용** | :heavy_check_mark: | :heavy_check_mark:| KQL 필요 | SQL 지원이 제한 됩니다.|
|||||

## <a name="query-store"></a>쿼리 저장소

쿼리 저장소 기능은 쿼리 계획 선택 및 성능에 대 한 통찰력을 제공 합니다. 쿼리 계획 변경으로 인해 발생하는 성능 차이를 신속하게 찾을 수 있도록 하여 성능 문제 해결을 간소화합니다. 

쿼리 저장소 새 Azure Synapse Analytics 데이터베이스에 대해 기본적으로 사용 하도록 설정 되어 있지 않습니다. 쿼리 저장소를 사용 하도록 설정 하려면 다음 T SQL 명령을 실행 합니다.

```sql
ALTER DATABASE <database_name>
SET QUERY_STORE = ON;
```

예를 들어 다음과 같습니다.

```sql
ALTER DATABASE [SQLPOOL1]
SET QUERY_STORE = ON;
```

마지막으로 실행 한 쿼리, 실행 횟수, 가장 오래 실행 되는 쿼리, 최대 물리적 i/o 잠재 고객이 있는 쿼리를 찾아 성능 감사 및 문제 해결 관련 작업을 실행할 수 있습니다. 예제 쿼리 [는 쿼리 저장소을 사용 하 여 성능 모니터링](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store#performance) 을 참조 하세요.

장점
* 저장소 쿼리 데이터에 대 한 30 일의 임계값입니다.
* 데이터는에서 쿼리를 실행 하는 것과 동일한 도구에서 사용 될 수 있습니다.

알려진 제한 사항:
* Dmv를 사용 하는 것과 비교 하 여 Azure Synapse에 대 한 쿼리 저장소에서는 분석 시나리오가 제한 됩니다.

## <a name="dmvs"></a>DMV

Dmv (동적 관리 뷰)는 쿼리 대기 시간, 실행 계획, 메모리 등에 대 한 정보를 수집 하는 데 매우 유용 합니다. 나중에 추적 하는 데 관심이 있는 쿼리에 레이블을 기록 하는 것이 좋습니다. 예를 들어 다음과 같습니다.

```sql
-- Query with Label
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query');
```

Azure Synapse SQL에서 쿼리를 레이블 지정 하는 방법에 대 한 자세한 내용은 [Synapse SQL에서 쿼리 레이블 사용](develop-label.md)을 참조 하세요.

dmv를 사용 하 여 Azure Synapse Analytics 작업을 모니터링 하는 방법에 대 한 자세한 내용은 [dmv를 사용 하 여 전용 SQL 풀 워크 로드 모니터링](../sql-data-warehouse/sql-data-warehouse-manage-monitor.md?context=/azure/synapse-analytics/context/context)을 참조 하세요. Azure Synapse Analytics와 관련 한 카탈로그 뷰에 대 한 설명서는 [Azure Synapse Analytics 카탈로그 뷰](/sql/relational-databases/system-catalog-views/sql-data-warehouse-and-parallel-data-warehouse-catalog-views)를 참조 하세요.

장점
* 데이터는 동일한 쿼리 도구에서 사용 될 수 있습니다.
* Dmv는 분석에 대 한 광범위 한 옵션을 제공 합니다.

알려진 제한 사항:
* Dmv는 기록 항목의 1만 행으로 제한 됩니다. 
* 풀이 일시 중지/다시 시작 되 면 보기가 다시 설정 됩니다.

## <a name="log-analytics"></a>Log Analytics
Azure Portal에서 Log Analytics 작업 영역을 쉽게 만들 수 있습니다. Synapse를 사용 하 Log Analytics 여를 연결 하는 방법에 대 한 자세한 지침은  [워크 로드 모니터링-Azure Portal](../sql-data-warehouse/sql-data-warehouse-monitor-workload-portal.md)을 참조 하세요.

Azure 데이터 탐색기와 마찬가지로 Log Analytics는 Kusto 쿼리 언어 (KQL)를 사용 합니다. Kusto 구문에 대 한 자세한 내용은 [kusto 쿼리 개요](/azure/data-explorer/kusto/query/)를 참조 하세요. 

구성 가능한 보존 기간을 사용 하 여 Log Analytics 쿼리를 위해 구체적으로 지정 하는 작업 영역을 선택 합니다. Log Analytics는 데이터를 저장 하 고 실행 하 고 쿼리를 저장 하는 유연성을 제공 합니다.

장점
* Azure Log Analytics에 사용자 지정 가능한 로그 보존 정책이 있습니다.

알려진 제한 사항:
* KQL를 사용 하면 학습 곡선에가 추가 됩니다.
* 제한 된 보기는 즉시 로그 아웃할 수 있습니다.

## <a name="azure-data-explorer-adx"></a>Azure 데이터 탐색기 (ADX)

ADX (Azure 데이터 탐색기)는 최고의 데이터 탐색 서비스입니다. 이 서비스는 Azure Synapse Analytics에서 기록 쿼리를 분석 하는 데 사용할 수 있습니다. ADX에 로그를 복사 하 여 저장 하도록 ADF (Azure Data Factory) 파이프라인을 설정 하려면 [Azure 데이터 탐색기 간에 데이터 복사](../../data-factory/connector-azure-data-explorer.md)를 참조 하세요. ADX에서는 성능 Kusto 쿼리를 실행 하 여 로그를 분석할 수 있습니다. 예를 들어 ADF를 통해 ADX로 DMV 출력을 쿼리하고 로드 하기 위해 다른 전략을 여기에서 결합할 수 있습니다.
  
장점
* ADX는 사용자 지정 가능한 로그 보존 정책을 제공 합니다.
* 많은 양의 데이터에 대해 쿼리를 실행 합니다. 특히 문자열 검색을 포함 하는 쿼리입니다.

알려진 제한 사항:
* KQL를 사용 하면 학습 곡선에가 추가 됩니다.

## <a name="next-steps"></a>다음 단계

 - [Azure Data Explorer](/azure/data-explorer/)
 - [Azure Data Factory](../../data-factory/index.yml)
 - [Azure Monitor Log Analytics](../../azure-monitor/logs/log-analytics-overview.md)
 - [Azure Synapse SQL 아키텍처](overview-architecture.md)
 - [Azure Synapse Analytics 시작](../get-started.md)