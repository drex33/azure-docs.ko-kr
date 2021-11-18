---
title: REST API |를 사용 하 여 Microsoft 센티널에서 구하기 및 라이브 스트림 쿼리 관리 Microsoft Docs
description: 이 문서에서는 Microsoft 센티널 구하기 기능을 사용 하 여 Log Analytics ' REST API를 활용 하 여 구하기 및 라이브 스트림 쿼리를 관리 하는 방법을 설명 합니다.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: reference
ms.custom: mvc, ignite-fall-2021
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: yelevin
ms.openlocfilehash: 4b479fbe2782cb7316cee565f51d7b6170c17579
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132721027"
---
# <a name="manage-hunting-and-livestream-queries-in-microsoft-sentinel-using-rest-api"></a>REST API를 사용 하 여 Microsoft 센티널에서 구하기 및 라이브 스트림 쿼리 관리

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Microsoft 센티널은 Azure Monitor Log Analytics에 포함 되어 있으므로 Log Analytics ' REST API를 사용 하 여 구하기 및 라이브 스트림 쿼리를 관리할 수 있습니다. 이 문서에서는 REST API를 사용하여 헌팅 쿼리를 만들고 관리하는 방법을 보여 줍니다.  이러한 방식으로 만들어진 쿼리는 Microsoft 센티널 UI에 표시 됩니다.

[저장된 검색 API](/rest/api/loganalytics/savedsearches)에 대한 자세한 내용은 최종 REST API 참조를 참조하세요.

## <a name="api-examples"></a>API 예제

다음 예에서 이러한 자리 표시자를 다음 표에 명시된 대체 항목으로 바꿉니다.

| 자리표시자 | 다음 항목으로 교체 |
|-|-|
| **{subscriptionId}** | 헌팅 또는 라이브 스트림 쿼리를 적용할 구독의 이름입니다. |
| **{resourceGroupName}** | 헌팅 또는 라이브 스트림 쿼리를 적용할 리소스 그룹의 이름입니다. |
| **{savedSearchId}** | 각 헌팅 쿼리의 고유 ID(GUID)입니다. |
| **{WorkspaceName}** | 쿼리의 대상인 Log Analytics 작업 영역의 이름입니다. |
| **{DisplayName}** | 쿼리에 대해 선택한 표시 이름입니다. |
| **{Description}** | 헌팅 또는 라이브 스트림 쿼리에 대한 설명입니다. |
| **{Tactics}** | 쿼리에 적용되는 관련 MITRE ATT&CK 전술입니다. |
| **{Query}** | 쿼리에 대한 쿼리 식입니다. |
|  

### <a name="example-1"></a>예 1

이 예에서는 지정 된 Microsoft 센티널 작업 영역에 대 한 구하기 쿼리를 만들거나 업데이트 하는 방법을 보여 줍니다.  라이브 스트림 쿼리의 경우 **요청 본문** 에서 *“Category”: “Hunting Queries”* 를 *“Category”: “Livestream Queries”* 로 바꿉니다. 

#### <a name="request-header"></a>요청 헤더

```http
PUT https://management.azure.com/subscriptions/{subscriptionId} _
    /resourcegroups/{resourceGroupName} _
    /providers/Microsoft.OperationalInsights/workspaces/{workspaceName} _
    /savedSearches/{savedSearchId}?api-version=2020-03-01-preview
```

#### <a name="request-body"></a>요청 본문

```json
{
"properties": {
    “Category”: “Hunting Queries”,
    "DisplayName": "HuntingRule02",
    "Query": "SecurityEvent | where EventID == \"4688\" | where CommandLine contains \"-noni -ep bypass $\"",
    “Tags”: [
        { 
        “Name”: “Description”,
        “Value”: “Test Hunting Query”
        },
        { 
        “Name”: “Tactics”,
        “Value”: “Execution, Discovery”
        }
        ]        
    }
}
```

### <a name="example-2"></a>예제 2

이 예제에서는 지정 된 Microsoft 센티널 작업 영역에 대 한 구하기 또는 라이브 스트림 쿼리를 삭제 하는 방법을 보여 줍니다.

```http
DELETE https://management.azure.com/subscriptions/{subscriptionId} _
       /resourcegroups/{resourceGroupName} _
       /providers/Microsoft.OperationalInsights/workspaces/{workspaceName} _
       /savedSearches/{savedSearchId}?api-version=2020-03-01-preview
```

### <a name="example-3"></a>예제 3

이 예에서는 지정된 작업 영역에 대한 헌팅 또는 라이브 스트림 쿼리를 검색하는 방법을 보여 줍니다.

```http
GET https://management.azure.com/subscriptions/{subscriptionId} _
    /resourcegroups/{resourceGroupName} _
    /providers/Microsoft.OperationalInsights/workspaces/{workspaceName} _
    /savedSearches/{savedSearchId}?api-version=2020-03-01-preview
```

## <a name="next-steps"></a>다음 단계

이 문서에서는 Log Analytics API를 사용 하 여 Microsoft 센티널에서 구하기 및 라이브 스트림 쿼리를 관리 하는 방법을 배웠습니다. Microsoft 센티널에 대해 자세히 알아보려면 다음 문서를 참조 하세요.

- [위협 요소를 사전에 헌팅하기](hunting.md)
- [Notebook을 사용하여 자동 헌팅 캠페인 실행](notebooks.md)
