---
title: Azure Monitor 통합 문서 그래프 시각화
description: 모든 Azure Monitor 통합 문서 그래프 시각화에 대해 알아봅니다.
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/04/2020
ms.openlocfilehash: ac1418ee2df60a79be104f1991908dcde0de6c07
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131072259"
---
# <a name="graph-visualizations"></a>그래프 시각화

통합 문서는 모니터링 엔터티 간의 관계를 표시하기 위해 로그 데이터를 기반으로 임의의 그래프를 시각화하도록 지원합니다.

아래 그래프는 컴퓨터가 다양한 포트를 통해 외부 컴퓨터와 주고받는 데이터의 흐름을 보여줍니다. 유형(컴퓨터 vs. 포트 vs. 외부 IP)에 따라 색이 지정되고 에지 크기는 그 사이에서 흐르는 데이터의 양에 해당합니다. 기본 데이터는 VM 연결을 대상으로 하는 KQL 쿼리에서 제공됩니다.

[![타일 요약 뷰의 스크린샷](./media/workbooks-graph-visualizations/graph.png)](./media/workbooks-graph-visualizations/graph.png#lightbox)

## <a name="adding-a-graph"></a>그래프 추가
1. 편집 도구 모음 항목을 클릭하여 통합 문서를 편집 모드로 전환합니다.
2. **쿼리 추가** 링크를 사용하여 통합 문서에 로그 쿼리 컨트롤을 추가합니다.
3. **로그**, 리소스 형식(예: Application Insights) 및 대상 리소스를 선택합니다.
4. 쿼리 편집기를 사용하여 분석을 위한 KQL을 입력합니다.

    ```kusto
    let data = dependencies
    | summarize Calls = count() by App = appName, Request = operation_Name, Dependency = name
    | extend RequestId = strcat(App, '::', Request);
    let links = data
    | summarize Calls = sum(Calls) by App, RequestId
    | project SourceId = App, TargetId = RequestId, Calls, Kind = 'App -> Request'
    | union (data
        | project SourceId = RequestId, TargetId = Dependency, Calls, Kind = 'Request -> Dependency');
    let nodes = data
    | summarize Calls = sum(Calls) by App
    | project Id = App, Name = App, Calls, Kind = 'App'
    | union (data
        | summarize Calls = sum(Calls) by RequestId, Request
        | project Id = RequestId, Name = Request, Calls, Kind = 'Request')
    | union (data
        | summarize Calls = sum(Calls) by Dependency
        | project Id = Dependency, Name = Dependency, Calls, Kind = 'Dependency');
    nodes
    | union (links)
    ```

5. 시각화를 그래프로 설정합니다.
6. **그래프 설정** 단추를 선택하여 설정 창을 엽니다.
7. 아래쪽의 레이아웃 필드에서 다음을 설정합니다.
    * 노드 ID: `Id`
    * 원본 ID: `SourceId`
    * 대상 ID: `TargetId`
    * 에지 레이블: `None`
    * 에지 크기: `Calls`
    * 노드 크기: `None`
    * 색 지정 유형: `Categorical`
    * 노드 색 필드: `Kind`
    * 색상표: `Pastel`
8. 위쪽의 노드 형식 설정에서 다음을 설정합니다.
    * 위쪽 콘텐츠 - 다음 열 사용: `Name`, 열 렌더러: `Text`
    * 가운데 콘텐츠 - 다음 열 사용: `Calls`, 열 렌더러: `Big Number`, 색상표: `None`
    * 아래쪽 콘텐츠 - 다음 열 사용: `Kind`, 열 렌더러: `Text`
9. 창 맨 아래에 있는 저장 및 닫기 단추를 선택합니다.

[![위의 쿼리 및 설정이 포함된 타일 요약 뷰의 스크린샷](./media/workbooks-graph-visualizations/graph-settings.png)](./media/workbooks-graph-visualizations/graph-settings.png#lightbox)

## <a name="graph-settings"></a>그래프 설정

| 설정         | 설명                                                                                                        |
|:----------------|:-------------------------------------------------------------------------------------------------------------------|
| `Node Id`       | 그래프에 표시되는 노드의 고유 ID를 제공하는 열을 선택합니다. 열 값은 문자열 또는 숫자가 될 수 있습니다. |
| `Source Id`     | 그래프에 표시되는 에지의 원본 노드 ID를 제공하는 열을 선택합니다. 값은 '노드 ID' 열의 값에 매핑되어야 합니다. |
| `Target Id`     | 그래프에 표시되는 에지의 대상 노드 ID를 제공하는 열을 선택합니다. 값은 '노드 ID' 열의 값에 매핑되어야 합니다. |
| `Edge Label`    | 그래프에 표시되는 에지 레이블을 제공하는 열을 선택합니다.                                                            |
| `Edge Size`     | 에지 너비의 기준이 되는 메트릭을 제공하는 열을 선택합니다.                                |
| `Node Size`     | 노드 영역의 기반이 되는 메트릭을 제공하는 열을 선택합니다.                                 |
| `Coloring Type` | 노드 색 지정 구성표를 선택하는 데 사용됩니다.                                                                            |

## <a name="node-coloring-types"></a>노드 색 지정 유형

| 색 지정 유형 | 설명 |
|:------------- |:------------|
| `None`        | 모든 노드는 같은 색입니다. |
| `Categorical` | 노드에는 결과 집합의 열에 있는 값 또는 범주를 기반으로 색이 할당됩니다. 위의 예제에서 색 지정은 결과 집합의 열 '종류'를 기준으로 합니다. 지원되는 색상표는 `Default`, `Pastel` 및 `Cool tone`입니다.  |
| `Field Based` | 이 유형에서 열은 노드에 사용할 특정 RGB 값을 제공합니다. 가장 뛰어난 유연성을 제공하지만 일반적으로 사용하려면 더 많은 작업이 필요합니다.  |

## <a name="node-format-settings"></a>노드 형식 설정

그래프 작성자는 노드의 각 부분(위쪽, 왼쪽, 가운데, 오른쪽, 아래쪽)에 배치되는 콘텐츠를 지정할 수 있습니다. 그래프는 통합 문서가 지원하는 모든 렌더러를 사용할 수 있습니다(텍스트, 큰 숫자, 스파크 라인, 아이콘 등)를 사용할 수 있습니다.

## <a name="field-based-node-coloring"></a>필드 기반 노드 색 지정

1. 편집 도구 모음 항목을 클릭하여 통합 문서를 편집 모드로 전환합니다.
2. **쿼리 추가** 링크를 사용하여 통합 문서에 로그 쿼리 컨트롤을 추가합니다.
3. **로그**, 리소스 종류(예: Application Insights) 및 대상 리소스를 선택합니다.
4. 쿼리 편집기를 사용하여 분석을 위한 KQL을 입력합니다.

    ```kusto
    let data = dependencies
    | summarize Calls = count() by App = appName, Request = operation_Name, Dependency = name
    | extend RequestId = strcat(App, '::', Request);
    let links = data
    | summarize Calls = sum(Calls) by App, RequestId
    | project SourceId = App, TargetId = RequestId, Calls, Kind = 'App -> Request'
    | union (data
        | project SourceId = RequestId, TargetId = Dependency, Calls, Kind = 'Request -> Dependency');
    let nodes = data
    | summarize Calls = sum(Calls) by App
    | project Id = App, Name = App, Calls, Color = 'FD7F23'
    | union (data
        | summarize Calls = sum(Calls) by RequestId, Request
        | project Id = RequestId, Name = Request, Calls, Color = 'B3DE8E')
    | union (data
        | summarize Calls = sum(Calls) by Dependency
        | project Id = Dependency, Name = Dependency, Calls, Color = 'C9B3D5');
    nodes
    | union (links)
    ```
5. 시각화를 그래프로 설정합니다.
6. **그래프 설정** 단추를 선택하여 설정 창을 엽니다.
7. 아래쪽의 레이아웃 필드에서 다음을 설정합니다.
    * 노드 ID:`Id`
    * 원본 ID: `SourceId`
    * 대상 ID: `TargetId`
    * 에지 레이블: `None`
    * 에지 크기: `Calls`
    * 노드 크기: `Node`
    * 색 지정 유형: `Field Based`
    * 노드 색 필드: `Color`
8. 위쪽의 노드 형식 설정에서 다음을 입력합니다.
    * 위쪽 콘텐츠에서 다음을 설정합니다.
        * 다음 열 사용: `Name`
        * 열 렌더러: `Text`
    * 가운데 콘텐츠에서 다음을 설정합니다.
        * 다음 열 사용: `Calls`
        * 열 렌더러: `Big Number`
        * 색상표: `None`
    * 아래쪽 콘텐츠에서 다음을 설정합니다.
        * 다음 열 사용: `Kind`
        * 열 렌더러: `Text`
9. 창 맨 아래에 있는 **저장 및 닫기** 단추를 선택합니다.

[![필드 기준 노드 색 지정을 사용하여 그래프 시각화를 만드는 방법을 보여 주는 스크린샷](./media/workbooks-graph-visualizations/graph-field-based.png)](./media/workbooks-graph-visualizations/graph-field-based.png#lightbox)

## <a name="next-steps"></a>다음 단계

* 그래프는 복합 막대 렌더러도 지원합니다. 자세히 알아보려면 [복합 막대 설명서](workbooks-composite-bar.md)를 참조하세요.
* 통합 문서에서 사용할 수 있는 [데이터 원본](workbooks-data-sources.md)에 대해 자세히 알아봅니다.
