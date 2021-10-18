---
title: Azure Monitor 통합 문서 복합 막대 렌더러
description: 모든 Azure Monitor 통합 문서 복합 막대 렌더러 시각화에 관해 알아봅니다.
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 9/04/2020
ms.openlocfilehash: 1b2946d4def2004d9d620eb253c733fdf31c39cf
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/18/2021
ms.locfileid: "130132534"
---
# <a name="composite-bar-renderer"></a>복합 막대 렌더러

통합 문서를 사용하면 여러 막대로 구성된 막대인 복합 막대를 사용하여 데이터를 렌더링할 수 있습니다.

아래 이미지는 온라인, 오프라인 및 복구 상태에 있는 서버 수를 나타내는 데이터베이스 상태에 대한 복합 막대를 보여 줍니다.

![데이터베이스 상태에 대한 복합 막대의 스크린샷](./media/workbooks-composite-bar/database-status.png)

복합 막대 렌더러는 그리드, 타일 및 그래프 시각화에서 지원됩니다.

## <a name="adding-composite-bar-renderer"></a>복합 막대 렌더러 추가

1. ‘편집’ 도구 모음 항목을 선택하여 통합 문서를 편집 모드로 전환합니다.
2. ‘추가’를 선택하고 ‘쿼리 추가’를 선택합니다. 
3. ‘데이터 원본’을 “JSON”으로 설정하고 ‘시각화’를 “그리드”로 설정합니다. 
4. 다음 JSON 데이터를 추가합니다.

```json
[
    {"sub":"X", "server": "A", "online": 20, "recovering": 3, "offline": 4, "total": 27},
    {"sub":"X", "server": "B", "online": 15, "recovering": 8, "offline": 5, "total": 28},
    {"sub":"Y", "server": "C", "online": 25, "recovering": 4, "offline": 5, "total": 34},
    {"sub":"Y", "server": "D", "online": 18, "recovering": 6, "offline": 9, "total": 33}
]
```

5. 쿼리를 실행합니다.
6. **열 설정** 을 선택하여 설정을 엽니다.
7. ‘열’에서 “합계”를 선택하고 ‘열 렌더러’에 대해 “복합 막대”를 선택합니다. 
8. ‘복합 막대 설정’에서 다음 설정을 지정합니다.

| 열 이름 | 색상        |
|-------------|--------------|
| 온라인      | 녹색        |
| 복구  | 노란색       |
| 오프라인     | 빨간색(밝은) |

9. 레이블 추가: `["online"] of ["total"] are healthy`
10. 온라인, 오프라인 및 복구에 대한 열 설정에서 열 렌더러를 “숨김”으로 설정할 수 있습니다(선택 사항).
11. 위쪽에서 ‘레이블’을 선택하고 합계 열의 레이블을 “데이터베이스 상태”로 업데이트합니다(선택 사항).
12. **적용** 선택

복합 막대 설정은 아래 스크린샷과 같이 표시됩니다.

![위에 설명된 설정이 있는 복합 막대 열 설정의 스크린샷](./media/workbooks-composite-bar/composite-bar-settings.png)

위 설정이 있는 복합 막대:

![복합 막대의 스크린샷](./media/workbooks-composite-bar/composite-bar.png)

## <a name="composite-bar-settings"></a>복합 막대 설정

열 이름과 해당하는 색을 선택하여 해당 색의 열을 복합 막대의 일부로 렌더링합니다. 행을 위쪽 및 아래쪽으로 삽입, 삭제, 이동할 수 있습니다.

### <a name="label"></a>레이블

복합 막대 레이블은 복합 막대 위쪽에 표시됩니다. 정적 텍스트, 열 및 매개 변수를 함께 사용할 수 있습니다.  레이블이 비어 있으면 현재 열의 값이 레이블로 표시됩니다. 이전 예제에서는 레이블 필드를 비워 둔 경우 합계 열의 값이 표시됩니다.

`["columnName"]`이 있는 열을 참조합니다.

`{paramName}`이 있는 매개 변수를 참조합니다.

열 이름과 매개 변수 이름은 둘 다 대/소문자를 구분합니다. “이 항목을 링크로 설정”을 선택하여 레이블을 링크로 만든 다음, 링크 설정을 추가할 수도 있습니다.

### <a name="aggregation"></a>집계

집계는 시각화별 트리/그룹에 유용합니다. 그룹 행의 열에 대한 데이터는 해당 열에 대해 설정된 집계에 따라 결정됩니다. 복합 막대에 적용할 수 있는 집계에는 없음, 합계, 상속의 세 가지 형식이 있습니다.

그룹화 기준 설정을 추가하려면:

1. 열 설정에서 설정을 추가하려는 열로 이동합니다.
2. ‘트리/그룹화 기준 설정’의 ‘트리 형식’에서 **그룹화 기준** 을 선택합니다. 
3. 그룹화할 필드를 선택합니다.

![그룹화 기준 설정의 스크린샷](./media/workbooks-composite-bar/group-by-settings.png)

#### <a name="none"></a>없음

없음 집계는 그룹 행에 대해 해당 열의 결과를 표시하지 않음을 의미합니다.

![없음 집계가 있는 복합 막대의 스크린샷](./media/workbooks-composite-bar/none.png)

#### <a name="sum"></a>합계

집계가 합계로 설정된 경우 그룹 행의 열은 렌더링하는 데 사용되는 열의 합계를 사용하여 복합 막대를 표시합니다. 레이블도 참조된 열의 합계를 사용합니다.

아래 예제에서 온라인, 오프라인, 복구에서는 모두 최대 집계가 해당 열로 설정되고 total 열의 집계는 합계입니다.

![합계 집계가 있는 복합 막대의 스크린샷](./media/workbooks-composite-bar/sum.png)

#### <a name="inherit"></a>상속

집계가 상속으로 설정된 경우 그룹 행의 열은 렌더링하는 데 사용되는 열에 대해 사용자가 설정한 집계를 사용하여 복합 막대를 표시합니다. 레이블에 사용되는 열은 사용자가 설정한 집계도 사용합니다. 현재 열 렌더러가 복합 막대이고 레이블(예: 위 예제의 “total”)에 있는 경우 합계는 해당 열의 집계로 사용됩니다.

아래 예제에서 온라인, 오프라인, 복구에서는 모두 최대 집계가 해당 열로 설정되고 total 열의 집계는 상속입니다.

![상속 집계가 있는 복합 막대의 스크린샷](./media/workbooks-composite-bar/inherit.png)

## <a name="sorting"></a>정렬

그리드 시각화의 경우 복합 막대 렌더러가 있는 열에 대한 행 정렬은 복합 막대 컴퓨터를 동적으로 렌더링하는 데 사용되는 열의 합계인 값에 따라 작동합니다. 이전 예제에서 정렬에 사용된 값은 해당 특정 행에 대한 온라인, 복구, 오프라인 열의 합계입니다.

## <a name="tiles-visualization"></a>타일 시각화

1. **추가** 를 선택하고 ‘쿼리를 추가’합니다.
2. 데이터 원본을 JSON으로 변경하고 [이전 예제](#adding-composite-bar-renderer)의 데이터를 입력합니다.
3. 시각화를 ‘타일’로 변경합니다.
4. 쿼리를 실행합니다.
5. **타일 설정** 을 선택합니다.
6. 타일 필드에서 ‘왼쪽’을 선택합니다.
7. ‘필드 설정’ 아래에 다음 설정을 입력합니다.
    1. 열 사용: “server”.
    2. 열 렌더러: “Text”.
8. 타일 필드에서 ‘아래쪽’을 선택합니다.
9. ‘필드 설정’ 아래에 다음 설정을 입력합니다.
    1. 열 사용: “total”.
    2. 열 렌더러: “Composite Bar”.
    3. “복합 막대 설정”에서 다음 설정을 입력합니다.

    | 열 이름 | 색상        |
    |-------------|--------------|
    | 온라인      | 녹색        |
    | 복구  | 노란색       |
    | 오프라인     | 빨간색(밝은) |

    4. 레이블 추가: `["online"] of ["total"] are healthy`
10. **적용** 을 선택합니다.

타일의 복합 막대 설정:

![위에 설명된 설정이 있는 복합 막대 타일 설정의 스크린샷](./media/workbooks-composite-bar/tiles-settings.png)

위 설정이 있는 타일의 복합 막대 보기는 다음과 같이 표시됩니다.

![복합 막대 타일의 스크린샷](./media/workbooks-composite-bar/composite-bar-tiles.png)

## <a name="graphs-visualization"></a>그래프 시각화

그래프 시각화를 위한 복합 막대 렌더러를 만들려면(Hive 클러스터 입력) 아래 지침을 따릅니다.

1. **추가** 를 선택하고 ‘쿼리를 추가’합니다.
2. 데이터 원본을 JSON으로 변경하고 [이전 예제](#adding-composite-bar-renderer)의 데이터를 입력합니다.
3. 시각화를 ‘그래프’로 변경합니다.
4. 쿼리를 실행합니다.
5. **그래프 설정** 을 선택합니다.
6. 노드 형식 설정에서 ‘가운데 콘텐츠’를 선택합니다.
7. ‘필드 설정’ 아래에 다음 설정을 입력합니다.
    1. 열 사용: “total”.
    2. 열 렌더러: “Composite Bar”.
    3. ‘복합 막대 설정’에서 다음 설정을 입력합니다.

    |열 이름  |     색상    |
    |-------------|--------------|
    | 온라인      | 녹색        |
    | 복구  | 노란색       |
    | 오프라인     | 빨간색(밝은) |

   4. 레이블 추가: `["online"] of ["total"] are healthy`
9. ‘레이아웃 설정’ 아래에 다음 설정을 입력합니다.
    1. 그래프 형식: **Hive Clusters**.
    2. 노드 ID 선택: “server”.
    3. 그룹화 기준 필드: “None”.
    4. 노드 크기: 100.
    5. 육각형 사이의 여백: 5.
    6. 색 지정 유형 형식: **None**.
1. **적용** 을 선택합니다.
    
그래프의 복합 막대 설정:

![위에 설명된 설정이 있는 복합 막대 그래프 설정의 스크린샷](./media/workbooks-composite-bar/graphs-settings.png)

위 설정이 있는 그래프의 복합 막대 보기는 다음과 같이 표시됩니다.

![Hive 클러스터가 있는 복합 막대 그래프의 스크린샷](./media/workbooks-composite-bar/composite-bar-graphs.png)

## <a name="next-steps"></a>다음 단계

* Azure Resource Manager를 사용하여 통합 문서를 [배포](../visualize/workbooks-automate.md)합니다.
* 통합 문서 리소스에 대한 액세스를 [제어](./workbooks-access-control.md) 및 공유합니다.