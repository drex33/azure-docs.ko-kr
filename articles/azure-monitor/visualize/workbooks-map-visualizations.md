---
title: Azure Monitor 통합 문서 맵 시각화
description: Azure Monitor 통합 문서 맵 시각화에 대해 알아봅니다.
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 11/25/2020
ms.openlocfilehash: a90723754440191bcc92c6e01341cecee7869520
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/18/2021
ms.locfileid: "130132344"
---
# <a name="map-visualization"></a>맵 시각화

맵 시각화는 각 위치/국가/지역에 매핑된 모든 데이터를 집계하는 기능을 제공하여, 특정 지역의 문제를 정확히 찾아내고 높은 수준의 모니터링 데이터 집계 보기를 표시하는 데 도움을 줍니다.

아래 스크린샷에서는 여러 스토리지 계정에 대한 총 트랜잭션과 엔드투엔드 대기 시간을 보여 줍니다. 여기에서 크기는 총 트랜잭션 수에 따라 결정되며 맵 아래의 색 메트릭은 엔드투엔드 대기 시간을 보여 줍니다. 첫 번째 관찰에서 **미국 서부** 지역의 트랜잭션 수는 **미국 동부** 지역에 비해 적지만, **미국 서부** 지역의 엔드투엔드 대기 시간은 **미국 동부** 지역보다 깁니다. 이는 **미국 서부** 에 문제가 있다는 즉각적인 인사이트를 제공합니다.

![Azure 위치 맵의 스크린샷](./media/workbooks-map-visualizations/map-performance-example.png)

## <a name="adding-a-map"></a>맵 추가

기본 데이터/메트릭에 위도/경도 정보, Azure 리소스 정보, Azure 위치 정보, 국가/지역, 이름 또는 국가/지역 코드가 있는 경우 맵을 시각화할 수 있습니다.

### <a name="using-azure-location"></a>Azure 위치 사용

1. **편집** 도구 모음 항목을 선택하여 통합 문서를 편집 모드로 전환합니다.
2. **추가** 를 선택하고 *쿼리 추가* 를 선택합니다.
3. *데이터 원본* 을 `Azure Resource Graph`로 변경하여 스토리지 계정이 있는 구독을 선택합니다.
4. 분석에 아래 쿼리를 입력하고 **쿼리 실행** 을 선택합니다.

    ```kusto
    where  type =~ 'microsoft.storage/storageaccounts'
    | summarize count() by location
    ```

5. *크기* 를 `Large`로 설정합니다.
6. *시각화* 를 `Map`로 설정합니다.
7. 모든 설정이 자동으로 채워집니다. 사용자 지정 설정에서 **지도 설정** 단추를 선택하여 설정 창을 엽니다.
8. 다음은 선택한 구독에 대한 각 Azure 지역의 스토리지 계정을 보여 주는 맵 시각화의 스크린샷입니다.

![위의 쿼리를 사용하는 Azure 위치 맵의 스크린샷](./media/workbooks-map-visualizations/map-azure-location-example.png)

### <a name="using-azure-resource"></a>Azure 리소스 사용

1. **편집** 도구 모음 항목을 선택하여 통합 문서를 편집 모드로 전환합니다.
2. **추가** 를 선택 하고 *메트릭 추가* 를 선택합니다.
3. 스토리지 계정이 있는 구독을 사용합니다.
4. *리소스 종류* 를 `storage account`로 변경하고 *리소스* 에서 여러 스토리지 계정을 선택합니다.
5. **메트릭 추가** 를 선택하고 트랜잭션 메트릭을 추가합니다.
    1. 네임스페이스: `Account`
    2. 메트릭: `Transactions`
    3. 집계: `Sum`
    
    ![트랜잭션 메트릭의 스크린샷](./media/workbooks-map-visualizations/map-transaction-metric.png)
1. **메트릭 추가** 를 선택하고 Success E2E Latency 메트릭을 추가합니다.
    1. 네임스페이스: `Account`
    1. 메트릭: `Success E2E Latency`
    1. 집계: `Average`
    
    ![엔드투엔드 대기 시간 메트릭 스크린샷](./media/workbooks-map-visualizations/map-e2e-latency-metric.png)
1. *크기* 를 `Large`로 설정합니다.
1. *시각화* 크기를 `Map`로 설정합니다.
1. **맵 설정** 에서 다음 설정을 설정합니다.
    1. 사용하는 위치 정보: `Azure Resource`
    1. Azure 리소스 필드: `Name`
    1. 크기 기준: `microsoft.storage/storageaccounts-Transaction-Transactions`
    1. 위치 집계: `Sum of values`
    1. 색 지정 유형: `Heatmap`
    1. 색 기준: `microsoft.storage/storageaccounts-Transaction-SuccessE2ELatency`
    1. 색 집계: `Sum of values`
    1. 색상표: `Green to Red`
    1. 최솟값: `0`
    1. 메트릭 값: `microsoft.storage/storageaccounts-Transaction-SuccessE2ELatency`
    1. 다른 메트릭 집계 기준: `Sum of values`
    1. **사용자 지정 서식** 상자 선택
    1. 단위: `Milliseconds`
    1. 스타일: `Decimal`
    1. 최대 소수 자릿수: `2`

### <a name="using-countryregion"></a>국가/지역 사용

1. **편집** 도구 모음 항목을 선택하여 통합 문서를 편집 모드로 전환합니다.
2. **추가* 를 선택한 다음 *쿼리 추가* 를 선택합니다.
3. *데이터 원본* 을 `Log`으로 변경하세요.
4. *리소스 종류* 를 `Application Insights`로 선택하고 pageViews 데이터를 포함한 Application Insights 리소스를 선택합니다.
5. 쿼리 편집기를 사용하여 분석을 위한 KQL을 입력하고 **쿼리 실행** 을 선택합니다.

    ```kusto
    pageViews
    | project duration, itemCount, client_CountryOrRegion
    | limit 20
    ```

6. 크기 값을 `Large`로 설정합니다.
7. 시각화를 `Map`로 설정합니다.
8. 모든 설정이 자동으로 채워집니다. 사용자 지정 설정에서 **맵 설정** 을 선택합니다.

### <a name="using-latitudelocation"></a>위도/위치 사용

1. **편집** 도구 모음 항목을 선택하여 통합 문서를 편집 모드로 전환합니다.
2. **추가* 를 선택한 다음 *쿼리 추가* 를 선택합니다.
3. *데이터 원본* 을 `JSON`으로 변경하세요.
1. 아래 쿼리 편집기에 JSON 데이터를 입력하고 **쿼리 실행** 을 선택하세요.
1. *크기* 값을 `Large`로 설정합니다.
1. *시각화* 를 `Map`로 설정합니다.
1. “메트릭 설정”의 **맵 설정** 에서 *메트릭 레이블* 을 `displayName`으로 설정한 다음 **저장 후 닫기** 를 선택합니다.

아래 맵 시각화는 메트릭에 대해 선택한 레이블이 있는 각 위도 및 경도 위치의 사용자를 보여 줍니다.

![메트릭에 대해 선택한 레이블을 사용하여 각 위도 및 경도 위치의 사용자를 보여 주는 맵 시각화의 스크린샷](./media/workbooks-map-visualizations/map-latitude-longitude-example.png)

## <a name="map-settings"></a>맵 설정

### <a name="layout-settings"></a>레이아웃 설정

| 설정 | 설명 |
|:-------------|:-------------|
| `Location info using` | 지도에 표시되는 항목의 위치를 가져올 방법을 선택합니다. <br> **위도/경도**: 위도 및 경도 정보를 포함하는 열이 있는 경우 이 옵션을 선택합니다. 위도 및 경도 데이터가 있는 각 행은 맵에 고유한 항목으로 표시됩니다. <br> **Azure 위치**: Azure 위치(eastus, westeurope, centralindia 등) 정보를 포함하는 열이 있는 경우 이 옵션을 선택합니다. 해당 열을 지정하면 각 Azure 위치에 대해 해당하는 위도 및 경도를 가져오고, 지정된 집계를 기준으로 동일한 위치 행을 함께 그룹화하여 맵에 위치를 표시합니다. <br> **Azure 리소스**: Azure 리소스 정보(스토리지 계정, cosmosdb 계정 등)를 포함하는 열이 있는 경우 이 옵션을 선택합니다. 해당 열을 지정하면 각 Azure 리소스에 대해 해당하는 위도 및 경도를 가져오고, 지정된 집계를 기준으로 동일한 위치(Azure 위치) 행을 함께 그룹화하여 맵에 위치를 표시합니다. <br> **국가/지역**: 국가/지역 이름/코드(US, 미국, IN, 인도, CN, 중국) 정보를 포함하는 열이 있는 경우 이 옵션을 선택합니다. 해당 열을 지정하면 각 국가/지역/코드에 대한 위도 및 경도를 가져오고, 동일한 국가-지역 코드/국가-지역 이름과 함께 행을 그룹화하여 맵에 위치를 표시합니다. 국가 이름 및 국가 코드는 맵에서 단일 엔터티로 함께 그룹화되지 않습니다.
| `Latitude/Longitude` | 위치 정보 필드 값이 위도/경도인 경우 이러한 두 옵션이 표시됩니다. 위도 필드에 위도를 포함하는 열과 경도 필드에 경도를 포함하는 열을 각각 선택합니다. |
| `Azure location field` | 위치 정보 필드 값이 Azure 위치인 경우 이 옵션이 표시됩니다. Azure 위치 정보가 있는 열을 선택합니다. |
| `Azure resource field` | 위치 정보 필드 값이 Azure 리소스인 경우 이 옵션이 표시됩니다. Azure 리소스 정보에 해당하는 열을 선택합니다. |
| `Country/Region field` | 위치 정보 필드 값이 국가 또는 지역인 경우 이 옵션이 표시됩니다. 국가/지역 정보를 나타내는 열을 선택합니다. |
| `Size by` | 이 옵션은 맵에 표시되는 항목의 크기를 제어합니다. 크기는 사용자가 지정한 열의 값에 따라 달라집니다. 현재 원의 반경은 열 값의 제곱근에 정비례합니다. ‘None...’을 선택하면 모든 원이 기본 지역 크기로 표시됩니다.|
| `Aggregation for location` | 이 필드는 동일한 Azure 위치/Azure 리소스/국가-지역이 있는 **크기 기준** 열을 집계하는 방법을 지정합니다. |
| `Minimum region size` | 이 필드는 맵에 표시되는 항목의 최소 반경을 지정합니다. 이는 크기 기준 열 값이 서로 크게 다를 때 사용되므로, 상대적으로 작은 항목은 맵에서 잘 보이지 않습니다. |
| `Maximum region size` | 이 필드는 맵에 표시되는 항목의 최대 반경을 지정합니다. 크기 기준 열 값이 매우 크고 맵의 광대한 영역을 커버하고 있는 경우에 사용됩니다.|
| `Default region size` | 이 필드는 맵에 표시되는 항목의 기본 반경을 지정합니다. 기본 반경은 크기 기준 열 값이 ‘None...’이거나 그 값이 0일 때 사용됩니다.|
| `Minimum value` | 영역 크기를 컴퓨팅하는 데 사용되는 최솟값입니다. 이를 지정하지 않으면 집계 후 가장 작은 값이 최솟값이 됩니다. |
| `Maximum value` | 영역 크기를 계산하는 데 사용되는 최댓값입니다. 지정하지 않으면 집계 후 가장 큰 값이 최댓값이 됩니다.|
| `Opacity of items on Map` | 이 필드는 맵에 표시되는 항목의 투명도를 지정합니다. 불투명도 1은 투명도가 없음을 의미하고, 불투명도 0이면 항목이 맵에서 보이지 않습니다. 맵에 항목이 너무 많은 경우 겹치는 항목을 모두 표시하도록 불투명도를 낮은 값으로 설정할 수 있습니다.|

### <a name="color-settings"></a>색 설정

| 색 지정 유형 | 설명 |
|:------------- |:-------------|
| `None` | 모든 노드는 같은 색입니다. |
| `Thresholds` | 이 형식에서 셀 색은 임계값 규칙에 의해 설정됩니다(예: _CPU > 90% => 빨간색, 60% > CPU > 90% => 노란색, CPU < 60% => 초록색_). <ul><li> **색 기준**: 이 열의 값은 임계값/열 지도 논리에 의해 사용됩니다.</li> <li>**색 집계**:이 필드는 동일한 Azure 위치/Azire 리소스/국가-지역이 있는 **색 기준** 열을 집계하는 방법을 지정합니다. </li> <ul> |
| `Heatmap` | 이 형식에서 셀은 색상표 및 색 기준 필드에 따라 색이 지정됩니다. 또한 같은 **색 기준** 과 **색 집계** 옵션을 임계값으로 갖게 됩니다. |

### <a name="metric-settings"></a>메트릭 설정
| 설정 | 설명 |
|:------------- |:-------------|
| `Metric Label` | 위치 정보 필드 값이 위도/경도인 경우 이 옵션이 표시됩니다. 이 기능을 사용하여 맵 아래에 표시되는 메트릭에 표시할 레이블을 선택할 수 있습니다. |
| `Metric Value` | 이 필드는 맵 아래에 표시할 메트릭 값을 지정합니다. |
| `Create 'Others' group after` | 이 필드는 “기타” 그룹이 만들어지기 전의 제한을 지정합니다. |
| `Aggregate 'Others' metrics by` | 이 필드는 표시되는 경우 “기타” 그룹에 사용되는 집계를 지정합니다. |
| `Custom formatting` | 이 필드를 사용하여 숫자 값의 단위, 스타일, 서식 옵션을 설정할 수 있습니다. 이는 [그리드의 사용자 지정 서식](../visualize/workbooks-grid-visualizations.md#custom-formatting)과 같습니다.|

## <a name="next-steps"></a>다음 단계

- [통합 문서에서 허니컴 시각화](../visualize/workbooks-honey-comb.md)를 만드는 방법에 대해 알아봅니다.