---
title: Azure Monitor 통합 문서 리소스 매개 변수
description: 리소스 매개 변수를 사용하여 통합 문서에서 리소스 선택을 허용하는 방법을 알아봅니다. 리소스 매개 변수를 사용하여 데이터를 가져올 범위를 설정합니다.
services: azure-monitor
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.openlocfilehash: dba84320bb753fa162d1c4eced5de5ae79de6f48
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123436161"
---
# <a name="workbook-resource-parameters"></a>통합 문서 리소스 매개 변수

리소스 매개 변수를 사용하여 통합 문서에서 리소스를 선택할 수 있습니다. 이 기능은 데이터를 가져올 범위를 설정하는 데 유용합니다. 사용자가 나중에 데이터를 표시할 때 차트에서 사용할 VM 세트를 선택할 수 있도록 허용하는 경우를 예로 들 수 있습니다.

리소스 선택기의 값은 통합 문서 컨텍스트, 정적 목록 또는 Azure Resource Graph 쿼리에서 가져올 수 있습니다.

## <a name="creating-a-resource-parameter-workbook-resources"></a>리소스 매개 변수 만들기(통합 문서 리소스)
1. 편집 모드에서 빈 통합 문서로 시작합니다.
2. 통합 문서 내의 링크에서 _매개 변수 추가_ 를 선택합니다.
3. 파란색의 _매개 변수 추가_ 단추를 클릭합니다.
4. 새 매개 변수 창이 나타나면 다음과 같이 입력합니다.
    1. 매개 변수 이름: `Applications`
    2. 매개 변수 유형: `Resource picker`
    3. 필수: `checked`
    4. 복수 선택 허용: `checked`
5. 데이터를 가져오는 출처: `Workbook Resources`
6. 리소스 종류만 포함: `Application Insights`
7. 도구 모음에서 ‘저장’을 선택하여 매개 변수를 만듭니다.

![통합 문서 리소스를 사용한 리소스 매개 변수 만들기를 보여 주는 이미지](./media/workbooks-resources/resource-create.png)

## <a name="creating-a-resource-parameter-azure-resource-graph"></a>리소스 매개 변수 만들기(Azure Resource Graph)
1. 편집 모드에서 빈 통합 문서로 시작합니다.
2. 통합 문서 내의 링크에서 _매개 변수 추가_ 를 선택합니다.
3. 파란색의 _매개 변수 추가_ 단추를 클릭합니다.
4. 새 매개 변수 창이 나타나면 다음과 같이 입력합니다.
    1. 매개 변수 이름: `Applications`
    2. 매개 변수 유형: `Resource picker`
    3. 필수: `checked`
    4. 복수 선택 허용: `checked`
5. 데이터를 가져오는 출처: `Query`
    1. 쿼리 유형: `Azure Resource Graph`
    2. 구독: `Use default subscriptions`
    3. 쿼리 컨트롤에서 이 코드 조각 추가
    ```kusto
    where type == 'microsoft.insights/components'
    | project value = id, label = name, selected = false, group = resourceGroup
    ```
7. 도구 모음에서 ‘저장’을 선택하여 매개 변수를 만듭니다.

![Azure Resource Graph를 사용한 리소스 매개 변수 만들기를 보여 주는 이미지](./media/workbooks-resources/resource-query.png)

> [!NOTE]
> 아직 모든 클라우드에서 Azure Resource Graph를 사용할 수 있는 것은 아닙니다. 이 접근 방식을 선택하는 경우 대상 클라우드에서 지원되는지 확인합니다.

[Azure Resource Graph 설명서](../../governance/resource-graph/overview.md)

## <a name="creating-a-resource-parameter--json-list"></a>리소스 매개 변수 만들기(JSON 목록)
1. 편집 모드에서 빈 통합 문서로 시작합니다.
2. 통합 문서 내의 링크에서 _매개 변수 추가_ 를 선택합니다.
3. 파란색의 _매개 변수 추가_ 단추를 클릭합니다.
4. 새 매개 변수 창이 나타나면 다음과 같이 입력합니다.
    1. 매개 변수 이름: `Applications`
    2. 매개 변수 유형: `Resource picker`
    3. 필수: `checked`
    4. 복수 선택 허용: `checked`
5. 데이터를 가져오는 출처: `JSON`
    1. 콘텐츠 컨트롤에서 이 JSON 코드 조각 추가
    ```json
    [
        { "value":"/subscriptions/<sub-id>/resourceGroups/<resource-group>/providers/<resource-type>/acmeauthentication", "label": "acmeauthentication", "selected":true, "group":"Acme Backend" },
        { "value":"/subscriptions/<sub-id>/resourceGroups/<resource-group>/providers/<resource-type>/acmeweb", "label": "acmeweb", "selected":false, "group":"Acme Frontend" }
    ]
    ```
    2. 파란색 ‘업데이트’ 단추를 누릅니다.
6. 필요에 따라 `Include only resource types`를 _Application Insights_ 로 설정
7. 도구 모음에서 ‘저장’을 선택하여 매개 변수를 만듭니다.

## <a name="referencing-a-resource-parameter"></a>리소스 매개 변수 참조
1. 통합 문서에 쿼리 컨트롤을 추가하고 Application Insights 리소스를 선택합니다.
2. _Application Insights_ 드롭다운을 사용하여 매개 변수를 컨트롤에 바인딩합니다. 이렇게 하면 쿼리 범위가 런타임에 매개 변수에 의해 반환되는 리소스로 설정됩니다.
4. KQL 컨트롤에서 이 코드 조각 추가
    ```kusto
    requests
    | summarize Requests = count() by appName, name
    | order by Requests desc
    ```
5. 쿼리를 실행하여 결과를 확인합니다. 

![쿼리 컨트롤에서 참조된 리소스 매개 변수를 보여 주는 이미지](./media/workbooks-resources/resource-reference.png)

> 이 접근 방식은 리소스를 메트릭과 같은 다른 컨트롤에 바인딩하는 데 사용할 수 있습니다.

## <a name="resource-parameter-options"></a>리소스 매개 변수 옵션
| 매개 변수 | 설명 | 예제 |
| ------------- |:-------------|:-------------|
| `{Applications}` | 선택한 리소스 ID | _/subscriptions/ \<sub-id\> /resourceGroups/ \<resource-group\> /providers/ \<resource-type\> /acmukathentication_ |
| `{Applications:label}` | 선택한 리소스의 레이블 | `acmefrontend` |
| `{Applications:value}` | 선택한 리소스의 값 | _'/subscriptions/ \<sub-id\> /resourceGroups/ \<resource-group\> /providers/ \<resource-type\> /acmukathentication'_ |
| `{Applications:name}` | 선택한 리소스의 이름 | `acmefrontend` |
| `{Applications:resourceGroup}` | 선택한 리소스의 리소스 그룹 | `acmegroup` |
| `{Applications:resourceType}` | 선택한 리소스의 종류 | _microsoft.insights/components_ |
| `{Applications:subscription}` | 선택한 리소스의 구독 |  |
| `{Applications:grid}` | 리소스 속성을 보여 주는 그리드 디버그하는 동안 텍스트 블록에서 렌더링하는 데 유용함  |  |

## <a name="next-steps"></a>다음 단계

* 시작 다양한 시각화 옵션을 갖춘 통합 문서에 대해 자세히 알아보기를 [시작하세요](./workbooks-overview.md#visualizations).
* 통합 문서 리소스에 대한 액세스를 [제어](./workbooks-access-control.md) 및 공유합니다.