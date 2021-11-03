---
title: REST API를 사용하여 Azure Sentinel에서 관심 목록 관리 | Microsoft Docs
description: 이 문서에서는 관심 목록 및 해당 항목을 만들고, 수정하 고, 삭제하기 위해 Log Analytics의 REST API를 사용하여 Azure Sentinel 관심 목록을 관리하는 방법을 설명합니다.
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
ms.date: 01/11/2021
ms.author: yelevin
ms.openlocfilehash: 4204cf1680c9af70576226ce1da5aed464728318
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131075147"
---
# <a name="manage-watchlists-in-azure-sentinel-using-rest-api"></a>REST API를 사용하여 Azure Sentinel에서 관심 목록 관리

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Azure Monitor Log Analytics에 부분적으로 빌드된 Azure Sentinel을 사용하면 Log Analytics의 REST API를 사용하여 관심 목록을 관리할 수 있습니다. 이 문서에서는 REST API를 사용하여 관심 목록 및 해당 항목을 만들고 수정하고 삭제하는 방법을 보여줍니다.  이러한 방식으로 만들어진 관심 목록은 Azure Sentinel UI에 표시됩니다.

## <a name="common-uri-parameters"></a>공통 URI 매개 변수

다음은 모든 관심 목록 API 명령에 대한 일반적인 URI 매개 변수입니다.

| 이름 | In(다음 안에) | 필수 | Type | Description |
|-|-|-|-|-|
| **{subscriptionId}** | path | 예 | GUID | Azure 구독 ID |
| **{resourceGroupName}** | path | 예 | 문자열 | 구독 내의 리소스 그룹 이름 |
| **{workspaceName}** | path | 예 | 문자열 | Log Analytics 작업 영역의 이름 |
| **{watchlistAlias}** | path | 예* | 문자열 | 지정된 관심 목록의 이름<br>\* 모든 관심 목록을 검색할 때 필요하지 않음 |
| **{watchlistItemId}** | path | 예** | GUID | 관심 목록에서 만들거나, 추가하거나, 삭제할 항목의 ID입니다<br>\*\*는 관심 목록 항목 명령에만 필요합니다 |
| **{api-version}** | Query | 예 | 문자열 | 이 요청에 사용되는 프로토콜의 버전. 2020 년 10월 29일, 관심 목록 API 버전은 *2019-01-01-미리 보기* 입니다 |
| **{bearerToken}** | 권한 부여 | 예 | token | 전달자 권한 부여 토큰 |
|  

## <a name="retrieve-all-watchlists"></a>모든 관심 목록 검색

이 명령은 해당 항목 없이 작업 영역과 연결된 모든 관심 목록을 검색합니다.

### <a name="request-uri"></a>요청 URI
(URI는 한 줄이며 가독성을 높이기 위해 분리됨)

| 방법 | 요청 URI |
|-|-|
| GET | `https://management.azure.com/subscriptions/{{subscriptionId}}/`<br>`resourceGroups/{{resourceGroupName}}/`<br>`providers/Microsoft.OperationalInsights/`<br>`workspaces/{{workspaceName}}/`<br>`providers/Microsoft.SecurityInsights/`<br>`watchlists?api-version={{api-version}}` |
|

### <a name="responses"></a>응답

| 상태 코드 | 응답 본문 | Description |
|-|-|-|
| 200 / OK | 기존 관심 목록의 목록입니다. 관심 목록을 찾을 수 없는 경우 비어 있습니다 |  |
| 400 / 잘못된 요청 |  | 요청 구문 형식이 잘못되었습니다. 요청 매개 변수가 잘못되었습니다. |
|

## <a name="look-up-a-watchlist-by-name"></a>이름으로 관심 목록 조회

이 명령은 해당 항목을 제외하고 작업 영역과 연결된 특정 관심 목록을 검색합니다.

### <a name="request-uri"></a>요청 URI
(URI는 한 줄이며 가독성을 높이기 위해 분리됨)

| 방법 | 요청 URI |
|-|-|
| GET | `https://management.azure.com/subscriptions/{{subscriptionId}}/`<br>`resourceGroups/{{resourceGroupName}}/`<br>`providers/Microsoft.OperationalInsights/`<br>`workspaces/{{workspaceName}}/`<br>`providers/Microsoft.SecurityInsights/`<br>`watchlists/{{watchlistAlias}}?api-version={{api-version}}` |
|

### <a name="responses"></a>응답

| 상태 코드 | 응답 본문 | Description |
|-|-|-|
| 200 / OK | 요청된 관심 목록 |  |
| 400 / 잘못된 요청 |  | 요청 구문 형식이 잘못되었습니다. 요청 매개 변수가 잘못되었습니다. |
| 404 / 찾을 수 없음 |  | 요청된 이름의 관심 목록을 찾을 수 없습니다 |
|

## <a name="create-a-watchlist"></a>관심 목록 생성

이 명령은 관심 목록을 만들고 여기에 항목을 추가합니다. 관심 목록 및 해당 항목을 만들기 위해 이 엔드포인트에 대한 두 호출을 수행합니다. 첫 번째는 관심 목록(parent)을 만들고 두 번째는 항목을 추가합니다.

### <a name="request-uri"></a>요청 URI
(URI는 한 줄이며 가독성을 높이기 위해 분리됨)

| 방법 | 요청 URI |
|-|-|
| PUT | `https://management.azure.com/subscriptions/{{subscriptionId}}/`<br>`resourceGroups/{{resourceGroupName}}/`<br>`providers/Microsoft.OperationalInsights/`<br>`workspaces/{{workspaceName}}/`<br>`providers/Microsoft.SecurityInsights/`<br>`watchlists/{{watchlistAlias}}?api-version={{api-version}}` |
|

### <a name="request-body"></a>요청 본문

다음은 관심 목록 만들기 요청의 요청 본문 샘플입니다.

```json
{
    "properties": {
        "displayName": "High Value Assets Watchlist",
        "source": "Local file",
        "provider": "Microsoft",
        "numberOfLinesToSkip":"1",
        "rawContent": "metadata line\nheader1, header2\nval1, val2",
        "contentType": "text/csv"
    }
}
```

### <a name="responses"></a>응답

| 상태 코드 | 응답 본문 | Description |
|-|-|-|
| 200 / OK | 항목 없이 요청에 의해 생성된 관심 목록 |  |
| 400 / 잘못된 요청 |  | 요청 구문 형식이 잘못되었습니다. 요청 매개 변수가 잘못되었습니다. |
| 409 / 충돌 |  | 작업이 실패했습니다. 별칭이 동일한 기존 관심 목록이 있습니다 |
|

## <a name="delete-a-watchlist"></a>관심 목록 삭제

이 명령은 관심 목록 및 해당 항목을 삭제합니다.

### <a name="request-uri"></a>요청 URI
(URI는 한 줄이며 가독성을 높이기 위해 분리됨)

| 방법 | 요청 URI |
|-|-|
| DELETE | `https://management.azure.com/subscriptions/{{subscriptionId}}/`<br>`resourceGroups/{{resourceGroupName}}/`<br>`providers/Microsoft.OperationalInsights/`<br>`workspaces/{{workspaceName}}/`<br>`providers/Microsoft.SecurityInsights/`<br>`watchlists/{{watchlistAlias}}?api-version={{api-version}}` |
|

### <a name="responses"></a>응답

| 상태 코드 | 응답 본문 | Description |
|-|-|-|
| 200 / OK | 빈 응답 본문입니다 |  |
| 204 / 콘텐츠 없음 | 빈 응답 본문입니다 | 삭제된 항목이 없음 |
| 400 / 잘못된 요청 |  | 요청 구문 형식이 잘못되었습니다. 요청 매개 변수가 잘못되었습니다. |
|

## <a name="add-or-update-a-watchlist-item"></a>관심 목록 항목 추가 또는 업데이트

이 명령이 기존 *watchlisItemId*(GUID)에서 실행되는 경우 요청 본문에 제공된 데이터를 사용하여 항목을 업데이트합니다. 그렇지 않은 경우, 새 항목을 만듭니다.

### <a name="request-uri"></a>요청 URI
(URI는 한 줄이며 가독성을 높이기 위해 분리됨)

| 방법 | 요청 URI |
|-|-|
| PUT | `https://management.azure.com/subscriptions/{{subscriptionId}}/`<br>`resourceGroups/{{resourceGroupName}}/`<br>`providers/Microsoft.OperationalInsights/`<br>`workspaces/{{workspaceName}}/`<br>`providers/Microsoft.SecurityInsights/`<br>`watchlists/{{watchlistAlias}}/`<br>`watchlistitems/{{watchlistItemId}}?api-version={{api-version}}` |
|

### <a name="request-body"></a>요청 본문

다음은 관심 목록 추가/업데이트 요청의 요청 본문 샘플입니다.

```json
{
    "properties": {
      "itemsKeyValue": {
           "Gateway subnet": "10.0.255.224/27",
           "Web Tier": "10.0.1.0/24",
           "Business tier": "10.0.2.0/24",
           "Private DMZ in": "10.0.0.0/27",
           "Public DMZ out": "10.0.0.96/27"
      }
}
}
```

### <a name="responses"></a>응답

| 상태 코드 | 응답 본문 | Description |
|-|-|-|
| 200 / OK | 요청에 의해 만들어지거나 업데이트된 관심 목록 항목입니다 |  |
| 400 / 잘못된 요청 |  | 요청 구문 형식이 잘못되었습니다. 요청 매개 변수가 잘못되었습니다. |
| 409 / 충돌 |  | 작업이 실패했습니다. 별칭이 동일한 기존 관심 목록이 있습니다 |
|

## <a name="delete-a-watchlist-item"></a>관심 목록 항목 삭제

이 명령은 기존 관심 목록 항목을 삭제합니다.

### <a name="request-uri"></a>요청 URI
(URI는 한 줄이며 가독성을 높이기 위해 분리됨)

| 방법 | 요청 URI |
|-|-|
| DELETE | `https://management.azure.com/subscriptions/{{subscriptionId}}/`<br>`resourceGroups/{{resourceGroupName}}/`<br>`providers/Microsoft.OperationalInsights/`<br>`workspaces/{{workspaceName}}/`<br>`providers/Microsoft.SecurityInsights/`<br>`watchlists/{{watchlistAlias}}/`<br>`watchlistitems/{{watchlistItemId}}?api-version={{api-version}}` |
|

### <a name="responses"></a>응답

| 상태 코드 | 응답 본문 | Description |
|-|-|-|
| 200 / OK | 빈 응답 본문입니다 |  |
| 204 / 콘텐츠 없음 | 빈 응답 본문입니다 | 삭제된 항목이 없음 |
| 400 / 잘못된 요청 |  | 요청 구문 형식이 잘못되었습니다. 요청 매개 변수가 잘못되었습니다. |
|

## <a name="next-steps"></a>다음 단계

이 문서에서는 Log Analytics API를 사용하여 Azure Sentinel에서 관심 목록 및 해당 항목을 관리하는 방법을 배웠습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.

- [관심 목록](watchlists.md)에 대해 자세히 알아보기
- [Azure Sentinel API](/rest/api/securityinsights/)의 다른 용도 살펴보기
