---
title: 보고서 쿼리 API 가져오기
description: 이 API를 사용하여 상업적 마켓플레이스 분석 보고서에서 사용할 수 있는 모든 쿼리를 가져옵니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: smannepalle
ms.author: smannepalle
ms.reviewer: sroy
ms.date: 3/08/2021
ms.openlocfilehash: f3a6351f726a5b1bd408764280ad2487cf7ecd95
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122567189"
---
# <a name="get-report-queries-api"></a>보고서 쿼리 API 가져오기

보고서 쿼리 API 가져오기는 보고서에서 사용할 수 있는 모든 쿼리를 가져옵니다. 기본값으로 모든 시스템 및 사용자 정의 쿼리를 가져옵니다.

**요청 구문**

| **방법** | **요청 URI** |
| --- | --- |
| GET | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledQueries?queryId={QueryID}&queryName={QueryName}&includeSystemQueries={include_system_queries}&includeOnlySystemQueries={include_only_system_queries}` |
|||

**요청 헤더**

| **Header** | **형식** | **설명** |
| --- | --- | --- |
| 권한 부여 | 문자열 | 필수 요소. `Bearer <token>` 양식의 Azure AD(Azure Active Directory) 액세스 토큰 |
| 콘텐츠 형식 | 문자열 | `Application/JSON` |
||||

**경로 매개 변수**

없음

**쿼리 매개 변수**

| **매개 변수 이름** | **형식** | **필수** | **설명** |
| --- | --- | --- | --- |
| `queryId` | 문자열 | No | 인수에 지정된 ID를 가진 쿼리만 세부 정보를 가져오는 필터 |
| `queryName` | 문자열 | No | 인수에 지정된 이름을 가진 쿼리만 세부 정보를 가져오는 필터 |
| `IncludeSystemQueries` | boolean | 예 | 응답에 미리 정의된 시스템 쿼리 포함 |
| `IncludeOnlySystemQueries` | boolean | 예 | 응답에 시스템 쿼리만 포함 |
|||||

**요청 페이로드**

없음

**용어 설명**

없음

**응답**

응답 페이로드는 다음과 같이 구성됩니다.

응답 코드: 200, 400, 401, 403, 404, 500

응답 페이로드:

```json
{
  "Value": [
    {
      "QueryId": "string",
      "Name": "string",
      "Description": "string",
      "Query": "string",
      "Type": "string",
      "User": "string",
      "CreatedTime": "string",
      "ModifiedTime": "string"
    }
  ],
  "TotalCount": 0,
  "Message": "string",
  "StatusCode": 0
}
```

**용어 설명**

이 표에는 응답에 있는 요소의 주요 정의가 나와 있습니다.

| **매개 변수** | **설명** |
| --- | --- |
| `QueryId` | 쿼리의 고유 UUID |
| `Name` | 쿼리를 만들 때 쿼리에 지정된 이름 |
| `Description` | 쿼리를 만드는 동안 제공된 설명 |
| `Query` | 보고서 쿼리 문자열 |
| `Type` | 사용자가 만든 쿼리에는 `userDefined`로, 그리고 미리 정의된 시스템 쿼리에는 `system`로 설정합니다. |
| `User` | 쿼리를 만든 사용자 ID |
| `CreatedTime` | 쿼리를 만든 시간 |
| `TotalCount` | 값 배열의 데이터 세트 수 |
| `StatusCode` | 결과 코드입니다. 가능한 값은 200, 400, 401, 403, 500입니다 |
|||
