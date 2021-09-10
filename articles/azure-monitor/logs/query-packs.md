---
title: Azure Monitor의 쿼리 팩
description: Azure Monitor의 쿼리 팩은 여러 Log Analytics 작업 영역에서 로그 쿼리 컬렉션을 공유하는 방법을 제공 합니다.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/20/2021
ms.openlocfilehash: ef34ec8531242dfdb20e984c4c6d8f86d08b8fcf
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122566286"
---
# <a name="query-packs-in-azure-monitor-logs-preview"></a>Azure Monitor 로그의 쿼리 팩(미리 보기)
쿼리 팩은 로그 쿼리를 저장하고 여러 작업 영역 및 다른 컨텍스트를 Log Analytics에서 공유하는 방법을 제공하는 Azure Monitor의 로그 쿼리 컨테이너 역할을 하는 리소스 관리자 개체입니다. 

## <a name="view-query-packs"></a>쿼리 팩 보기
Azure Portal 쿼리 팩을 보고 관리하려면 **Log Analytics 쿼리 팩** 메뉴를 선택하세요. 보고자 하는 쿼리 팩을 선택하여 해당 사용 권한을 편집합니다. API를 사용하여 쿼리 팩을 만드는 방법에 대한 세부 정보는 아래를 참조하세요.

[![쿼리 팩 보기](media/query-packs/view-query-pack.png)](media/query-packs/view-query-pack.png#lightbox)

## <a name="permissions"></a>권한
Azure Portal에서 볼 때 쿼리 팩의 사용 권한을 설정할 수 있습니다. 사용자가 쿼리 팩을 사용하려면 다음 사용 권한이 필요합니다.

- **읽기 권한자** - 사용자는 쿼리 팩의 모든 쿼리를 보고 실행할 수 있습니다.
- **Contributor** - 사용자는 기존 쿼리를 수정하고 쿼리 팩에 새 쿼리를 추가할 수 있습니다.

## <a name="default-query-pack"></a>기본 쿼리 팩
첫 번째 쿼리를 저장하면 **DefaultQueryPack** 쿼리 팩이 **LogAnalyticsDefaultResources** 리소스 그룹의 각 구독에 자동으로 생성됩니다. 요구 사항에 따라 이 쿼리 팩에 쿼리를 만들거나 추가 쿼리 팩을 만들 수 있습니다.

## <a name="using-multiple-query-packs"></a>여러 쿼리 팩 사용
대부분 사용자의 경우 쿼리를 저장하고 다시 사용하기 위해 단일 기본 쿼리 팩만 있으면 충분합니다. 하지만 다른 Log Analytics 세션에 다른 쿼리 집합을 로딩하고 다른 쿼리 컬렉션에 다른 사용 권한을 제공하는 등의 이유로 조직의 사용자가 사용할 여러 쿼리 팩을 만들려는 경우가 있습니다. 

API를 사용하여 새 쿼리 팩을 만드는 경우 비즈니스 요구 사항에 따라 쿼리를 분류하는 태그를 추가할 수 있습니다. 예를 들어 쿼리 팩에 태그를 표시하여 조직의 특정 부서나 포함된 쿼리가 해결해야 하는 문제의 심각도에 연관시킬 수 있습니다. 이렇게 하면 다양한 사용자 집합 및 다양한 상황에 적합한 서로 다른 쿼리 집합을 만들 수 있습니다.

## <a name="query-pack-definition"></a>쿼리 팩 정의
각 쿼리 팩은 하나 이상의 쿼리에 대한 정의를 포함하는 JSON 파일에 정의됩니다. 각 쿼리는 다음과 같이 블록으로 표시됩니다.

```json
{
    "properties":
       {
        "displayName": "Query name that will be displayed in the UI",
        "description": "Query description that will be displayed in the UI",
        "body": "<<query text, standard KQL code>>",
        "related": {
            "categories": [
                "workloads"
            ],
            "resourceTypes": [
                "microsoft.insights/components"
            ],
            "solutions": [
                "logmanagement"
            ]
        },
        "tags": {
            "Tag1": [
                "Value1",
                "Value2"
            ]
        },
   }
}
```


## <a name="query-properties"></a>쿼리 속성
쿼리 팩의 각 쿼리에는 다음과 같은 속성이 있습니다.


| 속성 | 설명 |
|:---|:---|
| displayName | 각 쿼리에 대한 Log Analytics에 나열된 표시 이름입니다. | 
| description | 각 쿼리에 대해 Log Analytics에 표시되는 쿼리에 대한 설명입니다. |
| 본문        | KQL로 작성된 쿼리입니다. |
| 관련     | 쿼리에 대한 관련 범주, 리소스 종류 및 솔루션입니다. 사용자가 쿼리를 찾을 수 있도록 Log Analytics에서 그룹화 및 필터링하는 데 사용됩니다. 각 쿼리는 각 형식을 최대 10개까지 가질 수 있습니다. 검색하는 값은 https://api.loganalytics.io/v1/metadata?select=resourceTypes, 솔루션, 범주에서 허용된 값입니다. |
| tags        | 사용자가 Log Analytics에서 정렬 및 필터링하는 데 사용하는 추가 태그입니다. 각 태그는 [쿼리를 그룹화하고 필터링](queries.md#finding-and-filtering-queries)할 때 범주, 리소스 종류 및 솔루션에 추가됩니다. |




## <a name="create-a-query-pack"></a>쿼리 팩 만들기
현재 쿼리 팩을 설치하는 유일한 방법은 REST API입니다. 

### <a name="create-token"></a>토큰 만들기
API 요청의 인증을 위해 토큰이 필요합니다. 토큰을 얻는 방법에는 **armclient** 사용을 포함하여 여러 가지가 있습니다.

먼저 다음 명령을 사용해 Azure에 로그인합니다.

```
armclient login
```

그 후 다음 명령을 사용하여 토큰을 만듭니다. 토큰은 클립보드에 자동으로 복사되므로 다른 도구에 붙여넣을 수 있습니다.

```
armclient token
```

### <a name="create-payload"></a>페이로드 만들기
요청의 페이로드는 하나 이상의 쿼리와 쿼리 팩을 저장해야 하는 위치를 정의하는 JSON입니다. 쿼리 팩의 이름은 다음 섹션에서 설명하는 API 요청에 지정됩니다.

```json
{
    "location": "eastus",
    "properties":
    {
        "displayName": "Query name that will be displayed in the UI",
        "description": "Query description that will be displayed in the UI",
        "body": "<<query text, standard KQL code>>",
        "related": {
            "categories": [
                "workloads"
            ],
            "resourceTypes": [
                "microsoft.insights/components"
            ],
            "solutions": [
                "logmanagement"
            ]
        },
        "tags": {
            "Tag1": [
                "Value1",
                "Value2"
            ]
        }
    }
}
```

### <a name="create-request"></a>요청 만들기
REST API를 사용하여 새로운 쿼리 팩을 만들려면 다음 요청을 사용합니다. 요청은 전달자 토큰 권한 부여를 사용해야 합니다. 콘텐츠 형식은 application/json이어야 합니다.

```rest
POST https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/my-resource-group/providers/Microsoft.Insights/querypacks/my-query-pack?api-version=2019-09-01-preview
```

Fiddler 또는 Postman과 같은 REST API 요청을 제출하여 이전 섹션에서 설명한 페이로드를 사용한 요청을 제출할 수 있는 도구를 사용합니다. 쿼리 ID가 생성되고 페이로드에 반환됩니다. 

## <a name="update-a-query-pack"></a>쿼리 팩 업데이트
쿼리 팩을 업데이트하려면 업데이트된 페이로드로 다음 요청을 제출합니다. 이 명령에는 쿼리 팩 ID가 필요합니다.

```rest
POST https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/my-resource-group/providers/Microsoft.Insights/querypacks/my-query-pack/queries/query-id/?api-version=2019-09-01-preview
```

## <a name="next-steps"></a>다음 단계

- 사용자가 Log Analytics에서 쿼리 팩과 상호 작용하는 방법을 보려면 [Azure Monitor Log Analytics에서 쿼리 사용](queries.md)을 참조하세요.
