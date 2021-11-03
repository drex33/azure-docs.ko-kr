---
title: 미리 보기 기능 목록
titleSuffix: Azure Cognitive Search
description: 미리 보기 기능이 릴리스되어 고객이 디자인과 유틸리티에 대한 피드백을 제공할 수 있습니다. 이 문서는 현재 미리 보기에 있는 모든 기능의 포괄적인 목록입니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/21/2021
ms.openlocfilehash: ef420f52650ca037d8030fbd2e73d8e37d2153f7
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131023579"
---
# <a name="preview-features-in-azure-cognitive-search"></a>Azure Cognitive Search의 미리 보기 기능

이 문서는 퍼블릭 미리 보기에 있는 모든 기능의 포괄적인 목록입니다. 미리 보기 기능은 서비스 수준 계약 없이 제공되며, 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

일반 공급으로 전환되는 미리 보기 기능은 이 목록에서 제거됩니다. 아래에 나와 있지 않은 기능은 일반적으로 사용 가능하다고 가정할 수 있습니다. 일반 공급에 대한 공지는 [서비스 업데이트](https://azure.microsoft.com/updates/?product=search) 또는 [새로운 기능](whats-new.md)을 참조하세요.

|기능&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | 범주 | Description | 가용성  |
|---------|------------------|-------------|---------------|
| [**검색 REST API 2021-04-30-Preview**](/rest/api/searchservice/index-preview) | 보안 | 외부 데이터 원본에 연결하는 인덱서에 대해 Azure Active Directory에서 관리 ID를 지원하도록 [데이터 원본 만들기 또는 업데이트](/rest/api/searchservice/preview-api/create-or-update-data-source)를 수정합니다. | 공개 미리 보기, [Search REST API 2021-04-30-Preview](/rest/api/searchservice/index-preview) |
| [**Azure RBAC 지원**](search-security-rbac.md) | 보안 | 새로운 기본 제공 역할을 사용하여 인덱스 및 인덱싱에 대한 액세스를 제어하여 API 키에 대한 종속성을 제거하거나 줄입니다. | 공개 미리 보기([요청별](./search-security-rbac.md?tabs=config-svc-portal%2croles-portal%2ctest-portal#step-1-preview-sign-up)). 구독이 등록된 후 Azure Portal 또는 관리 REST API 버전 2021-04-01-Preview를 사용하여 데이터 평면 인증에 대한 검색 서비스를 구성합니다. |
| [**관리 REST API 2021-04-01-Preview**](/rest/api/searchmanagement/) | 보안 | 새 [DataPlaneAuthOptions](/rest/api/searchmanagement/2021-04-01-preview/services/create-or-update#dataplaneauthoptions)를 지원하도록 [서비스 만들기 또는 업데이트](/rest/api/searchmanagement/2021-04-01-preview/services/create-or-update)를 수정합니다. | 공개 미리 보기, [관리 REST API](/rest/api/searchmanagement/), API 버전 2021-04-01-Preview.|
| [**문서 다시 설정**](search-howto-run-reset-indexers.md) | 인덱서 | 인덱서 워크로드에서 개별적으로 선택한 검색 문서를 다시 처리합니다. | [문서 재설정 REST API](/rest/api/searchservice/preview-api/reset-documents), API 버전 2021-04-30-Preview 또는 2020-06-30-Preview를 사용합니다. |
|  [**파워 쿼리 커넥터**](search-how-to-index-power-query-data-sources.md) | 인덱서 데이터 원본 | 이제 인덱서는 다른 클라우드 플랫폼에서 인덱싱할 수 있습니다. 인덱서를 사용하여 인덱싱할 외부 데이터 원본을 크롤링하는 경우 이제 파워 쿼리 커넥터를 사용하여 Amazon Redshift, Elasticsearch, PostgreSQL, Salesforce Objects, Salesforce Reports, Smartsheet, Snowflake에 연결할 수 있습니다. | 백 엔드에서 구독을 지원하도록 설정하려면 [등록](https://aka.ms/azure-cognitive-search/indexer-preview)을 해야 합니다. [데이터 원본 만들기 또는 업데이트](/rest/api/searchservice/preview-api/create-or-update-data-source), API 버전 2021-04-30-Preview와 2020-06-30-Preview 또는 Azure Portal을 사용하여 이 데이터 원본을 구성합니다.|
| [**SharePoint 온라인 인덱서**](search-howto-index-sharepoint-online.md) | 인덱서 데이터 원본 | SharePoint 콘텐츠의 인덱서 기반 인덱싱을 위한 새 데이터 원본입니다. | 백 엔드에서 구독을 지원하도록 설정하려면 [등록](https://aka.ms/azure-cognitive-search/indexer-preview)을 해야 합니다. [데이터 원본 만들기 또는 업데이트](/rest/api/searchservice/preview-api/create-or-update-data-source), API 버전 2021-04-30-Preview와 2020-06-30-Preview 또는 Azure Portal을 사용하여 이 데이터 원본을 구성합니다. |
|  [**MySQL 인덱서 데이터 원본**](search-howto-index-mysql.md) | 인덱서 데이터 원본 | 콘텐츠 및 메타데이터를 Azure MySQL 데이터 원본에서 인덱싱합니다.| 백 엔드에서 구독을 지원하도록 설정하려면 [등록](https://aka.ms/azure-cognitive-search/indexer-preview)을 해야 합니다. [데이터 원본 만들기 또는 업데이트](/rest/api/searchservice/preview-api/create-or-update-data-source), API 버전 2021-04-30-Preview와 2020-06-30-Preview, [.NET SDK 11.2.1](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourcetype.mysql) 및 Azure Portal을 사용하여 이 데이터 원본을 구성합니다. |
| [**Cosmos DB 인덱서: MongoDB API, Gremlin API**](search-howto-index-cosmosdb.md) | 인덱서 데이터 원본 | Cosmos DB의 경우 SQL API는 일반적으로 사용할 수 있지만 MongoDB 및 Cassandra API는 미리 보기로 제공됩니다. | MongoDB 및 Gremlin의 경우 [먼저 등록](https://aka.ms/azure-cognitive-search/indexer-preview)하여 백 엔드에서 구독을 지원할 수 있도록 합니다. MongoDB 데이터 원본은 포털에서 구성할 수 있습니다. [데이터 원본 만들기 또는 업데이트](/rest/api/searchservice/preview-api/create-or-update-data-source), API 버전 2021-04-30-Preview와 2020-06-30-Preview를 사용하여 이 데이터 원본을 구성합니다. |
| [**원시 Blob 일시 삭제**](search-howto-index-changed-deleted-blobs.md) | 인덱서 데이터 원본 | Azure Cognitive Search의 Azure Blob Storage 인덱서가 일시 삭제된 상태의 Blob을 인식하고 인덱싱 중에 해당 검색 문서를 제거합니다. | [데이터 원본 만들기 또는 업데이트](/rest/api/searchservice/preview-api/create-or-update-data-source), API 버전 2021-04-30-Preview와 2020-06-30-Preview를 사용하여 이 데이터 원본을 구성합니다. |
| [**의미 체계 검색**](semantic-search-overview.md) | 관련성(채점) | 결과, 캡션 및 답변의 의미 체계 순위입니다. | [문서 검색](/rest/api/searchservice/preview-api/search-documents), API 버전 2021-04-30-Preview 또는 2020-06-30-Preview 및 Search Explorer(포털)를 사용하여 의미 체계 검색을 구성합니다. |
| [**맞춤법 검사기**](cognitive-search-aml-skill.md) | 쿼리 | 단순, 전체 및 의미 체계 쿼리를 위한 쿼리 용어 입력의 선택적 맞춤법 수정입니다. | [검색 미리 보기 REST API](/rest/api/searchservice/preview-api/search-documents), API 버전 2021-04-30-Preview 또는 2020-06-30-Preview 및 Search Explorer(포털). |
| [**Normalizers**](search-normalizers.md) | 쿼리 |  Normalizers는 전체 텍스트 분석 체인을 호출하지 않고 일관된 대/소문자 구분, 악센트 제거 및 ASCII 접기 등의 간단한 텍스트 전처리를 제공합니다.| [문서 검색](/rest/api/searchservice/preview-api/search-documents), API 버전 2021-04-30-Preview 또는 2020-06-30-Preview를 사용합니다.|
| [**featuresMode 매개 변수**](/rest/api/searchservice/preview-api/search-documents#query-parameters) | 관련성(채점) | 관련성 점수 확장의 세부 정보는 필드별 유사성 점수, 필드별 용어 빈도, 필드별 일치하는 고유 토큰 수를 포함합니다. 데이터 요소를 [사용자 지정 채점 솔루션](https://github.com/Azure-Samples/search-ranking-tutorial)에 사용할 수 있습니다. | [문서 검색](/rest/api/searchservice/preview-api/search-documents), API 버전 2021-04-30-Preview, 2020-06-30-Preview 또는 2019-05-06-Preview를 사용하여 이 쿼리 매개 변수를 추가합니다. |
| [**AML(Azure Machine Learning) 기술**](cognitive-search-aml-skill.md) | AI 보강(기술) | Azure Machine Learning의 추론 엔드포인트를 통합하는 새로운 기술 유형입니다. [이 자습서](cognitive-search-tutorial-aml-custom-skill.md)를 시작합니다. | [검색 미리 보기 REST API](/rest/api/searchservice/), API 버전 2021-04-30-Preview, 2020-06-30-Preview 또는 2019-05-06-Preview를 사용합니다. Cognitive Search 및 Azure ML 서비스가 동일한 구독으로 배포될 때 포털에서 기술 세트 디자인으로 사용 가능합니다. |
| [**증분 보강**](cognitive-search-incremental-indexing-conceptual.md) | AI 보강(기술) | 보강 파이프라인에 캐싱을 추가하여 기술 세트 또는 다른 개체 업데이트와 같은 대상 수정이 콘텐츠를 변경하지 않는 경우 기존 출력을 다시 사용할 수 있도록 합니다. 캐싱은 기술 세트에 의해 생성된 보강 문서에만 적용 됩니다.| [인덱서 미리 보기 REST API 만들기 또는 업데이트](/rest/api/searchservice/create-indexer), API 버전 2021-04-30-Preview, 2020-06-30-Preview 또는 2019-05-06-Preview를 사용하여 이 구성 설정을 추가합니다. |
| [**디버그 세션**](cognitive-search-debug-session.md) | 포털, AI 보강(기술) | 기술 세트와 관련된 문제를 조사하고 해결하는 세션 내 기술 세트 편집기입니다. 디버그 세션 중에 적용되는 픽스를 서비스의 기술 세트에 저장할 수 있습니다. | 포털 전용으로, 개요 페이지의 중간 페이지 링크를 사용하여 디버그 세션을 열 수 있습니다. |
| [**moreLikeThis**](search-more-like-this.md) | 쿼리 | 특정 문서와 관련된 문서를 찾습니다. 이 기능은 이전 미리 보기에 있었습니다. | API 버전 2021-04-30-Preview, 2020-06-30-Preview, 2019-05-06-Preview, 2016-09-01-Preview 또는 2017-11-11-Preview가 포함된 [문서 검색 미리 보기 REST API](/rest/api/searchservice/search-documents) 호출에 이 쿼리 매개 변수를 추가합니다. |

## <a name="how-to-call-a-preview-rest-api"></a>미리 보기 REST API를 호출하는 방법

Azure Cognitive Search는 항상 REST API를 통해 실험 기능을 미리 공개한 다음 .NET SDK의 시험판 버전을 통해 공개합니다.

미리 보기 기능은 기능 디자인 및 구현에 관한 사용자 의견을 수집하기 위해 테스트 및 실험에 사용할 수 있습니다. 이러한 이유로 미리 보기 기능은 시간에 따라 이전 버전과 호환되지 않는 방식으로 변경될 수 있습니다. 이는 이전 버전과 호환되는 일부 수정 및 향상된 기능 외에는 변경되지 않으며 안정적인 GA 버전의 기능과 대조됩니다. 또한 미리 보기 기능이 항상 GA로 릴리스되는 것은 아닙니다.

포털 및 .NET SDK에서 일부 미리 보기 기능을 사용할 수 있지만 REST API에는 항상 미리 보기 기능이 있습니다.

+ 검색 작업의 경우 [ **`2021-04-30-Preview`**](/rest/api/searchservice/index-preview)는 현재 미리 보기 버전입니다.

+ 관리 작업의 경우 [ **`2021-04-01-Preview`**](/rest/api/searchmanagement/management-api-versions)는 현재 미리 보기 버전입니다.

이전 미리 보기가 여전히 작동하지만 시간이 지남에 따라 부실해집니다. 코드가 `api-version=2019-05-06-Preview`, `api-version=2016-09-01-Preview` 또는 `api-version=2017-11-11-Preview`를 호출하는 경우 해당 호출은 여전히 유효하지만 해당 버전에는 새로운 기능이 포함되지 않으며 버그 수정도 보장되지 않습니다.

다음 예제 구문은 미리 보기 API 버전에 대한 호출을 설명합니다.

```HTTP
POST https://[service name].search.windows.net/indexes/hotels-idx/docs/search?api-version=2021-04-30-Preview  
  Content-Type: application/json  
  api-key: [admin key]
```

Azure Cognitive Search 서비스는 여러 버전 및 클라이언트 라이브러리에서 제공됩니다. 자세한 내용은 [API 버전](search-api-versions.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

검색 REST 미리 보기 API 참조 설명서를 검토하세요. 문제가 발생하는 경우 [Stack Overflow](https://stackoverflow.com/)에서 도움을 요청하거나 [고객 지원팀](https://azure.microsoft.com/support/community/?product=search)에 문의하세요.

> [!div class="nextstepaction"]
> [검색 서비스 REST API 참조(미리 보기)](/rest/api/searchservice/index-preview)