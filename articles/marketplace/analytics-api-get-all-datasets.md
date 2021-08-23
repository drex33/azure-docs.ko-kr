---
title: 모든 데이터 세트 API 가져오기
description: 이 API를 사용하여 상업적 마켓플레이스 분석에 사용할 수 있는 모든 데이터 세트를 가져옵니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: smannepalle
ms.author: smannepalle
ms.reviewer: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 487c1b4ad58eb17fc90bb78f0dbc4346de8a62d3
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122567190"
---
# <a name="get-all-datasets-api"></a>모든 데이터 세트 API 가져오기

모든 데이터 세트 API 가져오기는 사용 가능한 데이터 세트를 모두 가져옵니다. 데이터 세트는 테이블, 열, 메트릭, 시간 범위를 나열합니다.

**요청 구문**

| **방법** | **요청 URI** |
| --- | --- |
| GET | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledDataset?datasetName={Dataset Name}` |

**요청 헤더**

| **Header** | **형식** | **설명** |
| --- | --- | --- |
| 권한 부여 | 문자열 | 필수 요소. `Bearer <token>` 양식의 Azure AD(Azure Active Directory) 액세스 토큰 |
| 콘텐츠 형식 | 문자열 | `Application/JSON` |

**경로 매개 변수**

None

**쿼리 매개 변수**

| **매개 변수 이름** | **형식** | **필수** | **설명** |
| --- | --- | --- | --- |
| `datasetName` | 문자열 | No | 하나의 데이터 세트에 대한 세부 정보만 가져오도록 필터링 |

**요청 페이로드**

없음

**용어 설명**

없음

**응답**

응답 페이로드는 다음과 같이 구성됩니다.

응답 코드: 200, 400, 401, 403, 404, 500

응답 페이로드 예제:

```json
{
   "Value":[
      {
         "DatasetName ":"string",
         "SelectableColumns":[
            "string"
         ],
         "AvailableMetrics":[
            "string"
         ],
         "AvailableDateRanges ":[
            "string"
         ]
      }
   ],
   "TotalCount":int,
   "Message":"<Error Message>",
   "StatusCode": int
}
```

**용어 설명**

다음 표에서는 응답의 주요 요소를 정의합니다.

| **매개 변수** | **설명** |
| --- | --- |
| `DatasetName` | 이 배열 개체가 정의하는 데이터 세트의 이름 |
| `SelectableColumns` | 선택 열에서 지정할 수 있는 원시 열 |
| `AvailableMetrics` | 선택 열에서 지정할 수 있는 집계/메트릭 열 이름 |
| `AvailableDateRanges` | 데이터 세트에 대한 보고서 쿼리에서 사용할 수 있는 날짜 범위 |
| `NextLink` | 데이터에 페이지가 매겨진 경우, 다음 페이지로 연결 |
| `TotalCount` | 값 배열의 데이터 세트 수 |
| `StatusCode` | 결과 코드입니다. 가능한 값은 200, 400, 401, 403, 500입니다 |
