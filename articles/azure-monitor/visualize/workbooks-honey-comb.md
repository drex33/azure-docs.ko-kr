---
title: 통합 문서 허니 빗 시각화 Azure Monitor
description: Azure Monitor 통합 문서 허니 빗 시각화에 대해 알아봅니다.
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/18/2020
ms.openlocfilehash: 51941e9b8ef7165e3d5756d8c4fa9ed3520bc460
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/18/2021
ms.locfileid: "130132363"
---
# <a name="honey-comb-visualizations"></a>허니 빗 시각화

허니 빗은 필요에 따라 클러스터로 그룹화할 수 있는 메트릭 또는 범주의 고밀도 보기를 허용합니다. 핫스폿을 시각적으로 식별하고 추가로 드릴하는 데 유용합니다.

아래 이미지는 두 구독에서 가상 머신의 CPU 사용률을 보여줍니다. 각 셀은 가상 머신을 나타내고 색/레이블은 평균 CPU 사용률을 나타냅니다(빨간색은 핫 머신임). 가상 머신은 구독별로 클러스터됩니다.

[![스크린샷은 두 구독에서 가상 머신의 CPU 사용률을 보여줍니다.](.\media\workbooks-honey-comb\cpu-example.png)](.\media\workbooks-honey-comb\cpu-example.png#lightbox)

## <a name="adding-a-honey-comb"></a>허니 빗 추가

1. 편집 도구 모음 항목을 클릭하여 통합 문서를 편집 모드로 전환합니다.
2. 맨 아래에 **추가를**  사용한 *다음, 쿼리 추가를* 사용하여 통합 문서에 로그 쿼리 컨트롤을 추가합니다.
3. 데이터 *원본으로*"로그"를 선택하고, *리소스 유형으로*"Log Analytics"를 *선택하고, 리소스에* 대해 가상 머신 성능 로그가 있는 작업 영역을 가리킵니다.
4. 쿼리 편집기를 사용하여 분석을 위한 KQL을 입력합니다.

```kusto
    Perf
| where CounterName == 'Available MBytes'
| summarize CounterValue = avg(CounterValue) by Computer, _ResourceId
| extend ResourceGroup = extract(@'/subscriptions/.+/resourcegroups/(.+)/providers/microsoft.compute/virtualmachines/.+', 1, _ResourceId)
| extend ResourceGroup = iff(ResourceGroup == '', 'On-premise computers', ResourceGroup), Id = strcat(_ResourceId, '::', Computer)
```

5. 쿼리를 실행합니다.
6. *시각화를* "Graph"로 설정합니다.
7. **그래프 설정** 을 선택합니다.
    1. 아래쪽의 레이아웃 필드에서 다음을 설정합니다.
        1. Graph 유형: **Hive 클러스터 .**
        2. 노드 ID: `Id` .
        3. 그룹화자: `None` .
        4. 노드 크기: 100.
        5. 16진수 사이의 여백: `5` .
        6. 색 지정 형식: **열 맵** 입니다.
        7. 노드 색 필드: `CouterValue` .
        8. 색상표: `Red to Green`
        9. 최솟값: `100`.
        10. 최대값: `2000` .
    2. 위쪽의 노드 형식 설정에서 다음을 설정합니다.
        1. 상위 콘텐츠:
            1. 다음 열 사용: `Computer`
            2. 열 렌더러" `Text` .
        9. 가운데 콘텐츠:
            1. 다음 열 사용: `CounterValue`
            2. 열 렌더러: `Big Number` .
            3. 색상표: `None` .
            4. 사용자 *지정 숫자 서식 지정* 상자를 선택합니다.
            5. 단위: `Megabytes` .
            6. 최대 소수 자릿수: `1` .
8. 창 아래쪽에서 **저장 후 닫기** 단추를 선택합니다.

[![위의 쿼리 및 설정이 있는 쿼리 제어, 그래프 설정 및 허니 빗의 스크린샷](.\media\workbooks-honey-comb\available-memory.png)](.\media\workbooks-honey-comb\available-memory.png#lightbox)

## <a name="honey-comb-layout-settings"></a>허니 빗 레이아웃 설정

| 설정 | 설명 |
|:------------- |:-------------|
| `Node Id` | 노드의 고유 ID를 제공하는 열을 선택합니다. 열 값은 문자열 또는 숫자가 될 수 있습니다. |
| `Group By Field` | 노드를 클러스터할 열을 선택합니다. |
| `Node Size` | 16진수 셀의 크기를 설정합니다. 사용 하 여는 `Margin between hexagons` 허니 빗 차트의 모양을 사용자 지정 하는 속성입니다. |
| `Margin between hexagons` | 16진수 셀 사이의 간격을 설정합니다. 사용 하 여는 `Node size` 허니 빗 차트의 모양을 사용자 지정 하는 속성입니다. |
| `Coloring type` | 노드에 색을 지정하는 데 사용할 체계를 선택합니다. |
| `Node Color Field` | 노드 영역의 기반이 되는 메트릭을 제공하는 열을 선택합니다. |

## <a name="node-coloring-types"></a>노드 색 지정 유형

| 색 지정 유형 | 설명 |
|:------------- |:-------------|
| `None` | 모든 노드는 같은 색입니다. |
| `Categorical` | 노드에는 결과 집합의 열에 있는 값 또는 범주를 기반으로 색이 할당됩니다. 위의 예제에서 색 지정은 결과 집합의 열 '종류'를 기준으로 합니다. 지원되는 색상표는 `Default`, `Pastel` 및 `Cool tone`입니다.  |
| `Heatmap` | 이 형식에서 셀은 메트릭 열 및 색상표에 따라 색이 지정됩니다. 이렇게 하면 셀에 분산된 메트릭을 간단히 강조 표시할 수 있습니다. |
| `Thresholds` | 이 형식에서 셀 색은 임계값 규칙에 따라 설정됩니다(예: _CPU > 90% => Red, 60% > CPU > 90% => 노랑, CPU < 60% = 녹색>_). |
| `Field Based` | 이 유형에서 열은 노드에 사용할 특정 RGB 값을 제공합니다. 가장 뛰어난 유연성을 제공하지만 일반적으로 사용하려면 더 많은 작업이 필요합니다.  |
      
## <a name="node-format-settings"></a>노드 형식 설정

허니 빗 작성자는 노드의 다른 부분(위쪽, 왼쪽, 가운데, 오른쪽 및 아래쪽)으로 가는 콘텐츠를 지정할 수 있습니다. 작성자는 지원되는 렌더러 통합 문서(텍스트, 큰 숫자, Spark 줄, 아이콘 등)를 자유롭게 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [통합 문서에서 복합 막대 렌더러를](workbooks-composite-bar.md)만드는 방법을 알아봅니다.
- [Azure Monitor 로그 데이터를 Power BI 가져오는](./powerbi.md)방법을 알아봅니다.