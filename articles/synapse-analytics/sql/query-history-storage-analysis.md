---
title: Azure Synapse Analytics 기록 쿼리 스토리지 및 분석
description: 기록 쿼리 분석은 데이터 엔지니어의 중요한 요구 사항 중 하나입니다. Azure Synapse Analytics 쿼리 기록 및 성능을 분석하는 네 가지 주요 방법을 지원합니다. 여기에는 쿼리 저장소, DMV, Azure Log Analytics 및 Azure Data Explorer 포함됩니다.
author: mariyaali
ms.author: mariyaali
ms.reviewer: wiassaf
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 10/28/2021
ms.custom: template-concept
ms.openlocfilehash: 866ca9439b5fc06f1e869cef6eef4c4adf979558
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132064453"
---
# <a name="historical-query-storage-and-analysis-in-azure-synapse-analytics"></a>Azure Synapse Analytics 기록 쿼리 스토리지 및 분석

기록 쿼리 분석은 데이터 엔지니어의 중요한 요구 사항 중 하나입니다. Azure Synapse Analytics 쿼리 기록 및 성능을 분석하는 네 가지 주요 방법을 지원합니다. 여기에는 쿼리 저장소, DMV, Azure Log Analytics 및 Azure Data Explorer 포함됩니다. 

이 문서에서는 요구 사항에 맞게 이러한 각 옵션을 사용하는 방법을 보여 줍니다. 쿼리 기록 분석과 관련된 사용 사례와 각각에 가장 적합한 방법을 검토합니다.

| **고객 필요** |  **쿼리 저장소** |  **DMV**    | **Azure Log Analytics** | **Azure Data Explorer** |
|------------- | --- | ----- | ------------- |-------------------|
|**첫 번째 솔루션** | 사용하도록 설정해야 합니다. | :heavy_check_mark: | 추가 서비스 필요 |    추가 서비스 필요|
|**더 긴 분석 기간** | 30일 |    최대 10000개의 기록 행     | 사용자 지정 가능 | 사용자 지정 가능|
|**중요한 메트릭 가용성** |    제한됨    | :heavy_check_mark: |    제한됨    | 사용자 지정 가능|
|**분석에 SQL 사용** | :heavy_check_mark: | :heavy_check_mark:| KQL 필요 | SQL 지원이 제한됩니다.|
|||||

## <a name="query-store"></a>쿼리 저장소

쿼리 저장소 기능은 쿼리 계획 선택 및 성능에 대한 인사이트를 제공합니다. 쿼리 계획 변경으로 인해 발생하는 성능 차이를 신속하게 찾을 수 있도록 하여 성능 문제 해결을 간소화합니다. 

쿼리 저장소 새 Azure Synapse Analytics 데이터베이스에 대해 기본적으로 사용되지 않습니다. 쿼리 저장소 다음 T-SQL 명령을 실행하도록 설정하려면 다음을 수행합니다.

```sql
ALTER DATABASE <database_name>
SET QUERY_STORE = ON;
```

예를 들어:

```sql
ALTER DATABASE [SQLPOOL1]
SET QUERY_STORE = ON;
```

마지막으로 실행된 쿼리, 실행 횟수, 가장 오래 실행되는 쿼리, 최대 물리적 I/O 리드가 있는 쿼리를 찾아 성능 감사 및 문제 해결 관련 작업을 실행할 수 있습니다. 샘플 쿼리는 [쿼리 저장소 사용하여 성능 모니터링을](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store#performance) 참조하세요.

장점
* 스토리지 쿼리 데이터에 대한 30일 임계값입니다.
* 쿼리를 실행하는 것과 동일한 도구에서 데이터를 사용할 수 있습니다.

알려진 제한 사항:
* 분석 시나리오는 DMV 사용과 비교할 때 Azure Synapse 쿼리 저장소 제한됩니다.

## <a name="dmvs"></a>DMV

DMV(동적 관리 뷰)는 쿼리 대기 시간, 실행 계획, 메모리 등에 대한 정보를 수집하는 데 매우 유용합니다. 나중에 추적하려면 관심 있는 쿼리에 레이블을 지정하는 것이 좋습니다. 예를 들어:

```sql
-- Query with Label
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query');
```

Azure Synapse SQL 쿼리 레이블 지정에 대한 자세한 내용은 [Synapse SQL 쿼리 레이블 사용을](develop-label.md)참조하세요.

DMV를 사용하여 Azure Synapse Analytics 워크로드를 모니터링하는 자세한 내용은 [DMV를 사용하여 전용 SQL 풀 워크로드 모니터링을 참조하세요.](../sql-data-warehouse/sql-data-warehouse-manage-monitor.md?context=/azure/synapse-analytics/context/context) Azure Synapse Analytics 관련 카탈로그 뷰에 대한 설명서는 카탈로그 뷰 [Azure Synapse Analytics 참조하세요.](/sql/relational-databases/system-catalog-views/sql-data-warehouse-and-parallel-data-warehouse-catalog-views)

장점
* 동일한 쿼리 도구에서 데이터를 사용할 수 있습니다.
* DMV는 분석을 위한 광범위한 옵션을 제공합니다.

알려진 제한 사항:
* DMV는 기록 항목의 행 10,000개로 제한됩니다. 
* 풀이 일시 중지/다시 시작되면 뷰가 다시 설정됩니다.

## <a name="log-analytics"></a>Log Analytics
Log Analytics 작업 영역은 Azure Portal 쉽게 만들 수 있습니다. Log Analytics와 Synapse를 연결하는 방법에 대한 자세한 내용은 [워크로드 모니터링 - Azure Portal.](../sql-data-warehouse/sql-data-warehouse-monitor-workload-portal.md)

Azure Data Explorer 마찬가지로 Log Analytics는 KQL(Kusto 쿼리 언어)을 사용합니다. Kusto 구문에 대한 자세한 내용은 [Kusto 쿼리 개요 를](/data-explorer/kusto/query/index.md)참조하세요. 

구성 가능한 보존 기간과 함께 Log Analytics에서 쿼리하도록 특별히 대상으로 하는 작업 영역을 선택합니다. Log Analytics는 데이터를 저장하고 쿼리를 실행하고 저장할 수 있는 유연성을 제공합니다.

장점
* Azure Log Analytics에는 사용자 지정 가능한 로그 보존 정책이 있습니다.

알려진 제한 사항:
* KQL을 사용하면 학습 곡선에 추가합니다.
* 제한된 보기를 상자에서 로그아웃할 수 있습니다.

## <a name="azure-data-explorer-adx"></a>Azure Data Explorer(ADX)

ADX(Azure Data Explorer)는 최고의 데이터 탐색 서비스입니다. 이 서비스는 Azure Synapse Analytics 기록 쿼리를 분석하는 데 사용할 수 있습니다. ADX에 로그를 복사하고 저장하는 ADF(Azure Data Factory) 파이프라인을 설정하려면 [Azure Data Explorer 데이터 복사를](/data-factory/connector-azure-data-explorer.md)참조하세요. ADX에서 Kusto 쿼리를 실행하여 로그를 분석할 수 있습니다. 여기에서 다른 전략을 결합하여 ADF를 통해 DMV 출력을 쿼리하고 ADX에 로드할 수 있습니다.
  
장점
* ADX는 사용자 지정 가능한 로그 보존 정책을 제공합니다.
* 많은 양의 데이터, 특히 문자열 검색과 관련된 쿼리에 대한 쿼리 실행이 성공적입니다.

알려진 제한 사항:
* KQL을 사용하면 학습 곡선에 추가합니다.

## <a name="next-steps"></a>다음 단계

 - [Azure Data Explorer](/azure/data-explorer/)
 - [Azure Data Factory](/azure/data-factory/)
 - [Log Analytics in Azure Monitor](/azure/azure-monitor/logs/log-analytics-overview)
 - [Azure Synapse SQL 아키텍처](overview-architecture.md)
 - [Azure Synapse Analytics 시작](../get-started.md)