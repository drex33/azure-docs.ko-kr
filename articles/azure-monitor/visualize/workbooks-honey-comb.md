---
title: Azure Monitor 통합 문서 허니 수십억 시각화
description: Azure Monitor 통합 문서 허니 수십억 시각화에 대해 알아봅니다.
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/18/2020
ms.openlocfilehash: ba24669f04966d9ee3cfa5af4cd677e83ad3ea7a
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130256613"
---
# <a name="honey-comb-visualizations"></a>허니 수십억 시각화

허니 combs는 선택적으로 클러스터로 그룹화 할 수 있는 메트릭 또는 범주의 고밀도 보기를 허용 합니다. 이러한 기능은 핫스폿을 시각적으로 식별 하는 데 유용 합니다.

아래 이미지는 두 구독의 가상 컴퓨터 CPU 사용률을 보여 줍니다. 각 셀은 가상 컴퓨터를 나타내고, 색/레이블은 평균 CPU 사용률 (reds은 hot machines)을 나타냅니다. 가상 머신은 구독에 의해 클러스터 됩니다.

[![두 구독의 가상 컴퓨터 CPU 사용률을 보여 주는 스크린샷](.\media\workbooks-honey-comb\cpu-example.png)](.\media\workbooks-honey-comb\cpu-example.png#lightbox)

## <a name="adding-a-honey-comb"></a>허니 수십억 추가

1. 편집 도구 모음 항목을 클릭하여 통합 문서를 편집 모드로 전환합니다.
2. 아래에서 **추가**  를 사용 하 여 쿼리를 추가 하 고 *쿼리를 추가* 하 여 통합 문서에 로그 쿼리 컨트롤을 추가 합니다.
3. *데이터 원본* 으로 "로그"를 선택 하 고, *리소스 유형* 으로 "Log Analytics"를 선택 하 고, 가상 머신 성능 로그가 있는 작업 영역에 대 한 *리소스* 지점을 선택 합니다.
4. 쿼리 편집기를 사용 하 여 분석을 위한 KQL를 입력 합니다.

```kusto
    Perf
| where CounterName == 'Available MBytes'
| summarize CounterValue = avg(CounterValue) by Computer, _ResourceId
| extend ResourceGroup = extract(@'/subscriptions/.+/resourcegroups/(.+)/providers/microsoft.compute/virtualmachines/.+', 1, _ResourceId)
| extend ResourceGroup = iff(ResourceGroup == '', 'On-premise computers', ResourceGroup), Id = strcat(_ResourceId, '::', Computer)
```

5. 쿼리를 실행합니다.
6. *시각화* 를 "Graph"로 설정 합니다.
7. **그래프 설정** 을 선택합니다.
    1. 아래쪽의 레이아웃 필드에서 다음을 설정합니다.
        1. Graph 형식: **Hive 클러스터**.
        2. 노드 Id: `Id` .
        3. 그룹화 `None` 방법:
        4. 노드 크기: 100.
        5. 육각형 사이 여백: `5` .
        6. 색 지정 유형: **열 지도**.
        7. 노드 색 필드: `CouterValue` .
        8. 색상표: `Red to Green`
        9. 최솟값: `100`.
        10. 최 댓 값: `2000` .
    2. 위쪽의 노드 형식 설정에서 다음을 설정합니다.
        1. 주요 내용:
            1. 다음 열 사용: `Computer`
            2. 열 렌더러 " `Text` .
        9. 콘텐츠 가운데 맞춤:
            1. 다음 열 사용: `CounterValue`
            2. 열 `Big Number` 렌더러:
            3. 색상표: `None` .
            4. *사용자 지정 숫자 서식* 상자를 선택 합니다.
            5. 단위: `Megabytes` .
            6. 최대 소수 자릿수: `1` .
8. 창 맨 아래에 있는 **저장 및 닫기** 단추를 선택 합니다.

[![위의 쿼리 및 설정을 사용 하는 쿼리 컨트롤, 그래프 설정 및 허니 수십억의 스크린샷](.\media\workbooks-honey-comb\available-memory.png)](.\media\workbooks-honey-comb\available-memory.png#lightbox)

## <a name="honey-comb-layout-settings"></a>허니 수십억 layout 설정

| 설정 | 설명 |
|:------------- |:-------------|
| `Node Id` | 노드의 고유 ID를 제공 하는 열을 선택 합니다. 열 값은 문자열 또는 숫자가 될 수 있습니다. |
| `Group By Field` | 노드를 클러스터링 할 열을 선택 합니다. |
| `Node Size` | 육각형 셀의 크기를 설정 합니다. 속성과 함께를 사용 `Margin between hexagons` 하 여 허니 수십억 차트의 모양을 사용자 지정할 수 있습니다. |
| `Margin between hexagons` | 육각형 셀 사이의 간격을 설정 합니다. 속성과 함께를 사용 `Node size` 하 여 허니 수십억 차트의 모양을 사용자 지정할 수 있습니다. |
| `Coloring type` | 노드의 색을 하는 데 사용할 체계를 선택 합니다. |
| `Node Color Field` | 노드 영역의 기반이 되는 메트릭을 제공하는 열을 선택합니다. |

## <a name="node-coloring-types"></a>노드 색 지정 유형

| 색 지정 유형 | 설명 |
|:------------- |:-------------|
| `None` | 모든 노드는 같은 색입니다. |
| `Categorical` | 노드에는 결과 집합의 열에 있는 값 또는 범주를 기반으로 색이 할당됩니다. 위의 예제에서 색 지정은 결과 집합의 열 '종류'를 기준으로 합니다. 지원되는 색상표는 `Default`, `Pastel` 및 `Cool tone`입니다.  |
| `Heatmap` | 이 형식에서 셀은 메트릭 열과 색상표를 기준으로 색이 지정 됩니다. 이를 통해 셀 간에 분산 된 메트릭을 강조 표시 하는 간단한 방법을 제공 합니다. |
| `Thresholds` | 이 형식에서 셀 색은 임계값 규칙에 의해 설정 됩니다 (예: _cpu > 90% => Red, 60% > cpu > 90% => 노랑, cpu < 60% => Green_). |
| `Field Based` | 이 유형에서 열은 노드에 사용할 특정 RGB 값을 제공합니다. 가장 뛰어난 유연성을 제공하지만 일반적으로 사용하려면 더 많은 작업이 필요합니다.  |
      
## <a name="node-format-settings"></a>노드 형식 설정

허니 수십억 작성자는 노드의 다른 부분으로 이동 하는 콘텐츠 (위쪽, 왼쪽, 가운데, 오른쪽 및 아래쪽)를 지정할 수 있습니다. 저자는 통합 문서에서 지 원하는 모든 렌더러 (텍스트, 큰 숫자, spark 선, 아이콘 등)를 무료로 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [통합 문서에서 복합 막대 렌더러](workbooks-composite-bar.md)를 만드는 방법에 대해 알아봅니다.
- [Azure Monitor 로그 데이터를 Power BI으로 가져오는](../logs/log-powerbi.md)방법을 알아봅니다.