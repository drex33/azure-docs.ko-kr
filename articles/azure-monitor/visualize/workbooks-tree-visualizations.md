---
title: Azure Monitor 통합 문서 트리 시각화
description: 모든 Azure Monitor 통합 문서 트리 시각화에 대해 알아봅니다.
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/04/2020
ms.openlocfilehash: c2c15eaac298795bfb96466959a77d6840e80936
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/18/2021
ms.locfileid: "130131603"
---
# <a name="tree-visualizations"></a>트리 시각화

통합 문서는 트리 그리드를 통해 계층 뷰를 지원합니다. 트리를 사용하면 드릴다운 환경에서 일부 행을 다음 수준으로 확장할 수 있습니다.

아래 예제에서는 트리 그리드로 시각화된 컨테이너 상태 메트릭(작업 집합 크기)을 보여 줍니다. 여기서 최상위 노드는 AKS(Azure Kubernetes Service) 노드이고, 다음 수준은 Pod이며, 최종 수준은 컨테이너입니다. 그리드(열 지도, 아이콘, 링크)에서와 같이 열의 서식을 지정할 수 있습니다. 이 경우 기본 데이터 원본은 AKS 로그가 있는 Log Analytics 작업 영역입니다.

[![타일 요약 뷰의 스크린샷](./media/workbooks-tree-visualizations/trees.png)](./media/workbooks-tree-visualizations/trees.png#lightbox)

## <a name="adding-a-tree-grid"></a>트리 그리드 추가
1. 편집 도구 모음 항목을 클릭하여 통합 문서를 편집 모드로 전환합니다.
2. **추가** 링크를 선택한 다음 쿼리 추가를 선택하여 통합 문서에 로그 쿼리 컨트롤을 추가합니다.
3. **로그**, 리소스 종류(예: Application Insights) 및 대상 리소스를 선택합니다.
4. 쿼리 편집기를 사용하여 분석을 위한 KQL을 입력합니다.
    ```kusto
    requests
    | summarize Requests = count() by ParentId = appName, Id = name
    | extend Kind = 'Request', Name = strcat('🌐 ', Id)
    | union (requests
    | summarize Requests = count() by Id = appName
    | extend Kind = 'Request', ParentId = '', Name = strcat('📱 ', Id))
    | project Name, Kind, Requests, Id, ParentId
    | order by Requests desc
    ```
5. 시각화를 **그리드** 로 설정합니다.
6. **열 설정** 단추를 선택하여 설정 창을 엽니다.
7. 아래쪽의 **트리/그룹화 기준 설정** 섹션에서 다음을 설정합니다.
    * 트리 형식: `Parent/Child`
    * ID 필드: `Id`
    * 부모 ID 필드: `ParentId`
    * 확장기 표시: `Name`
    * 트리의 최상위 수준 확장 확인란을 선택합니다.
8. 위쪽의 열 섹션에서 다음을 설정합니다.
    * _Id_ - 열 렌더러: `Hidden`
    * _Parent Id_ - 열 렌더러: `Hidden`
    * _Requests_ - 열 렌더러: `Bar`, 색상: `Blue`, 최솟값: `0`
9. 창 맨 아래에 있는 **저장 및 닫기** 단추를 선택합니다.

[![위의 쿼리 및 설정이 포함된 타일 요약 뷰의 스크린샷](./media/workbooks-tree-visualizations/tree-settings.png)](./media/workbooks-tree-visualizations/tree-settings.png#lightbox)

## <a name="tree-settings"></a>트리 설정

| 설정 | 설명 |
|:------------- |:-------------|
| `Id Field` | 그리드의 모든 행에 대한 고유 ID입니다. |
| `Parent Id Field` | 현재 행의 부모 ID입니다. |
| `Show the expander on` | 트리 확장기를 표시할 열입니다. 트리 그리드에서는 잘 읽을 수 없기 때문에 해당 ID 및 부모 ID 필드를 숨기는 것이 일반적입니다. 대신, 확장기가 엔터티의 이름과 같이 보다 읽기 쉬운 값이 있는 필드에 표시됩니다. |
| `Expand the top level of the tree` | 선택하는 경우 최상위 수준에서 트리 그리드가 확장됩니다. 기본적으로 추가 정보를 표시하려는 경우에 유용합니다. |

## <a name="grouping-in-a-grid"></a>그리드에서의 그룹화

그룹화를 사용하면 훨씬 간단한 쿼리를 사용하여 위와 유사한 계층 뷰를 작성할 수 있습니다. 트리의 내부 노드에서 집계가 손실되지만 일부 시나리오에서는 허용될 수 있습니다. 기본 결과 집합을 적절한 무료 형식으로 변환할 수 없는 경우(예: 경고, 상태 및 메트릭 데이터) 그룹화 기준을 사용하여 트리 뷰를 작성합니다.

## <a name="adding-a-tree-using-grouping"></a>그룹화를 사용하여 트리 추가

1. 편집 도구 모음 항목을 클릭하여 통합 문서를 편집 모드로 전환합니다.
2. **추가** 링크를 선택한 다음 쿼리 추가를 선택하여 통합 문서에 로그 쿼리 컨트롤을 추가합니다.
3. **로그**, 리소스 형식(예: Application Insights) 및 대상 리소스를 선택합니다.
4. 쿼리 편집기를 사용하여 분석을 위한 KQL을 입력합니다.
    ```kusto
    requests
    | summarize Requests = count() by App = appName, RequestName = name
    | order by Requests desc
    ```
1. 시각화를 그리드로 설정합니다.
2. **열 설정 단추** 를 선택하여 설정 창을 엽니다.
3. 아래쪽의 트리/그룹화 기준 설정 섹션에서 다음을 설정합니다.
    * 트리 형식: `Group By`
    * 그룹화 기준: `App`
    * 다음 기준: `None`
    * 트리의 최상위 수준 확장 확인란을 선택합니다.
4. 위쪽의 열 섹션에서 다음을 설정합니다.
    * *App* - 열 렌더러: `Hidden`
    * *Requests* - 열 렌더러: `Bar`, 색상: `Blue`, 최솟값: `0`
5. 창 맨 아래에 있는 **저장 및 닫기** 단추를 선택합니다.

[![통합 문서에서 트리 시각화 만들기를 보여 주는 스크린샷](./media/workbooks-tree-visualizations/tree-group-create.png)](./media/workbooks-tree-visualizations/tree-group-create.png#lightbox)

## <a name="next-steps"></a>다음 단계

* [통합 문서에서 그래프](workbooks-graph-visualizations.md)를 만드는 방법을 알아봅니다.
* [통합 문서에서 타일](workbooks-tile-visualizations.md)을 만드는 방법을 알아봅니다.
