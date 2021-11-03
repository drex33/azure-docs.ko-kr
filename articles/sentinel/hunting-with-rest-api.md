---
title: REST API를 사용하여 Azure Sentinel에서 헌팅 및 실시간 스트리밍 쿼리 관리 | Microsoft Docs
description: 이 문서에서는 Azure Sentinel 헌팅 기능을 사용하여 Log Analytics의 REST API를 활용하여 헌팅 및 라이브 스트림 쿼리를 관리하는 방법을 설명합니다.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: reference
ms.custom: mvc, ignite-fall-2021
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/06/2020
ms.author: yelevin
ms.openlocfilehash: 264e99b15676807c54f400cb4431f53c06cae8bd
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131037115"
---
# <a name="manage-hunting-and-livestream-queries-in-azure-sentinel-using-rest-api"></a>REST API를 사용하여 Azure Sentinel에서 헌팅 및 실시간 스트리밍 쿼리 관리

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Azure Monitor Log Analytics에 부분적으로 빌드된 Azure Sentinel을 사용하면 Log Analytics의 REST API를 사용하여 헌팅 및 라이브 스트림 쿼리를 관리할 수 있습니다. 이 문서에서는 REST API를 사용하여 헌팅 쿼리를 만들고 관리하는 방법을 보여 줍니다.  이러한 방식으로 만들어진 쿼리는 Azure Sentinel UI에 표시됩니다.

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

이 예에서는 지정된 Azure Sentinel 작업 영역에 대한 헌팅 쿼리를 만들거나 업데이트하는 방법을 보여 줍니다.  라이브 스트림 쿼리의 경우 **요청 본문** 에서 *“Category”: “Hunting Queries”* 를 *“Category”: “Livestream Queries”* 로 바꿉니다. 

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

이 예에서는 지정된 Azure Sentinel 작업 영역에 대한 헌팅 또는 라이브 스트림 쿼리를 삭제하는 방법을 보여 줍니다.

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

이 문서에서는 Log Analytics API를 사용하여 Azure Sentinel에서 헌팅 및 라이브 스트림 쿼리를 관리하는 방법을 배웠습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.

- [위협 요소를 사전에 헌팅하기](hunting.md)
- [Notebook을 사용하여 자동 헌팅 캠페인 실행](notebooks.md)
