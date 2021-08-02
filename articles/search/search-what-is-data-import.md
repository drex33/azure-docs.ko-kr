---
title: 검색 인덱스의 가져오기 및 데이터 수집
titleSuffix: Azure Cognitive Search
description: 외부 데이터 원본에서 Azure Cognitive Search의 인덱스로 데이터를 채우고 업로드합니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/05/2020
ms.openlocfilehash: 99cfe23cab5e9a61f548e78c914cafb1f6f63f7c
ms.sourcegitcommit: e39ad7e8db27c97c8fb0d6afa322d4d135fd2066
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111985871"
---
# <a name="data-import-overview---azure-cognitive-search"></a>데이터 가져오기 개요 - Azure Cognitive Search

Azure Cognitive Search에서는 [검색 인덱스](search-what-is-an-index.md)에 로드되어 저장된 콘텐츠에 대해 쿼리가 실행됩니다. 이 문서에서는 인덱스를 채우는 두 가지 기본 방법인 프로그래밍 방식으로 데이터를 인덱스에 *푸시* 하거나 [Azure Cognitive Search 인덱서](search-indexer-overview.md)에서 지원되는 데이터 원본을 가리켜 데이터를 *풀* 하는 방법을 알아보겠습니다.

두 방법 중 하나를 사용하여 외부 데이터 원본에서 Azure Cognitive Search 인덱스로 데이터를 로드하는 것이 목표입니다. Azure Cognitive Search를 사용하여 빈 인덱스 만들 수 있지만 데이터를 푸시하거나 끌어올 때까지는 쿼리할 수 없습니다.

> [!NOTE]
> [AI 보강](cognitive-search-concept-intro.md)이 솔루션 요구 사항인 경우 끌어오기 모델(인덱서)을 사용하여 인덱스를 로드해야 합니다. 외부 처리는 인덱서에 연결된 기술 세트를 통해서만 지원됩니다.

## <a name="pushing-data-to-an-index"></a>인덱스에 데이터 푸시

프로그래밍 방식으로 데이터를 Azure Cognitive Search로 보내는 데 사용되는 푸시 모델이 가장 유연한 방법입니다. 첫째, 데이터 원본 형식의 제한이 없습니다. JSON 문서로 구성된 모든 데이터 세트를 Azure Cognitive Search 인덱스로 푸시할 수 있으며, 데이터 세트의 각 문서에는 인덱스 스키마에 정의된 필드로 매핑되는 필드가 있습니다. 둘째, 실행 빈도의 제한이 없습니다. 원하는 만큼 자주 변경 내용을 인덱스에 푸시할 수 있습니다. 대기 시간 요구 사항이 매우 낮은 애플리케이션의 경우(예: 검색 작업이 동적 재고 데이터베이스와 동기화되어야 하는 경우) 푸시 모델이 유일한 옵션입니다.

개별적으로 또는 배치에서 문서를 업로드할 수 있기 때문에 이 접근 방식은 끌어오기 모델 보다 더 유연합니다(최대 배치 당 1000 또는 16MB, 모두 제한됨). 또한 밀어넣기 모델을 사용하면 데이터의 위치에 관계 없이 Azure Cognitive Search에 문서를 업로드할 수 있습니다.

### <a name="how-to-push-data-to-an-azure-cognitive-search-index"></a>Azure Cognitive Search 인덱스에 데이터를 푸시하는 방법

다음 API를 사용하여 하나 또는 여러 문서를 인덱스에 로드할 수 있습니다.

+ [문서 추가, 업데이트 또는 삭제(REST API)](/rest/api/searchservice/AddUpdate-or-Delete-Documents)
+ [IndexDocumentsAction 클래스](/dotnet/api/azure.search.documents.models.indexdocumentsaction) 또는 [IndexDocumentsBatch 클래스](/dotnet/api/azure.search.documents.models.indexdocumentsbatch) 

현재 포털을 통해 데이터를 푸시할 수 있는 도구가 없습니다.

각 방법론에 대한 소개는 [빠른 시작: PowerShell을 사용하여 Azure Cognitive Search 인덱스 만들기](./search-get-started-powershell.md) 또는 [C# 빠른 시작: .NET SDK를 사용하여 Azure Cognitive Search 인덱스 만들기](search-get-started-dotnet.md)를 참조하세요.

<a name="indexing-actions"></a>

### <a name="indexing-actions-upload-merge-mergeorupload-delete"></a>인덱싱 작업: upload, merge, mergeOrUpload, delete

문서를 기존 문서 콘텐츠와 병합해서 완전히 업로드할지 또는 삭제할지를 지정하여 문서별로 인덱싱 작업 유형을 제어할 수 있습니다.

REST API에서 Azure Cognitive Search 인덱스의 엔드포인트 URL에 대한 JSON 요청 본문을 사용하여 HTTP POST 요청을 발급합니다. "value" 배열의 각 JSON 개체는 문서의 키를 포함하고 인덱싱 작업에서 문서 콘텐츠를 추가할지, 업데이트할지 또는 삭제할지를 지정합니다. 코드 예제는 [문서 로드](search-get-started-dotnet.md#load-documents)를 참조하세요.

.NET SDK에서 데이터를 `IndexBatch` 개체로 패키징합니다. `IndexBatch`는 `IndexAction` 개체의 컬렉션을 캡슐화하며 각각은 Azure Cognitive Search가 해당 문서에 대해 수행할 작업을 알려주는 문서 및 속성을 포함합니다. 코드 예제는 [C# 빠른 시작](search-get-started-dotnet.md)을 참조하세요.


| @search.action | Description | 각 문서에 대해 필요한 필드 | 메모 |
| -------------- | ----------- | ---------------------------------- | ----- |
| `upload` |`upload` 작업은 새 문서는 삽입하고 기존 문서는 업데이트/교체하는 "upsert"와 비슷합니다. |키, 더하기 정의하려는 기타 필드 |기존 문서를 업데이트/교체하는 경우 요청에 지정되지 않은 필드는 해당 필드를 `null`로 설정합니다. 필드가 이전에 null이 아닌 값으로 설정된 경우에 발생합니다. |
| `merge` |기존 문서를 지정한 필드로 업데이트합니다. 인덱스에 문서가 없으면 병합이 실패합니다. |키, 더하기 정의하려는 기타 필드 |문서의 기존 필드는 병합에서 지정하는 필드로 바뀝니다. .NET SDK에서는 여기에는 `DataType.Collection(DataType.String)` 형식의 필드가 포함됩니다. REST API에서는 여기에는 `Collection(Edm.String)` 형식의 필드가 포함됩니다. 예를 들어 값이 `["budget"]`인 `tags` 필드가 포함되어 있는 문서에서 `tags`에 대해 `["economy", "pool"]` 값과의 병합을 실행하면 `tags` 필드의 최종 값은 `["economy", "pool"]`이 됩니다. `["budget", "economy", "pool"]`이 아닙니다. |
| `mergeOrUpload` |이 작업은 지정된 키를 포함하는 문서가 인덱스에 이미 있는 경우 `merge`와 비슷하게 작동합니다. 문서가 없는 경우 새 문서가 있는 `upload` 와 비슷하게 작동합니다. |키, 더하기 정의하려는 기타 필드 |- |
| `delete` |삭제 시에는 지정된 문서를 인덱스에서 제거합니다. |키만 |키 필드 외의 지정한 모든 필드는 무시됩니다. 문서에서 개별 필드를 제거하려는 경우 대신 `merge` 를 사용하고 필드를 명시적으로 Null로 설정합니다. |

### <a name="formulate-your-query"></a>쿼리 작성

[REST API를 사용하여 인덱스를 검색](/rest/api/searchservice/Search-Documents)하는 두 가지 방법이 있습니다. 한 가지 방법은 쿼리 매개 변수가 요청 본문의 JSON 개체에 정의된 HTTP POST 요청을 발급하는 것입니다. 다른 방법은 쿼리 매개 변수가 요청 URL 내에 정의된 HTTP GET 요청을 발급하는 것입니다. 쿼리 매개 변수의 크기에 있어 POST는 GET보다 더 [큰 제한](/rest/api/searchservice/Search-Documents)이 있습니다. 따라서 GET을 사용하는 것이 보다 편리한 특별한 경우가 아니라면 게시를 사용하는 것이 좋습니다.

POST 및 GET의 경우 *서비스 이름*, *인덱스 이름* 및 *API 버전* 을 요청 URL에 제공해야 합니다. 

GET의 경우 URL 끝의 *쿼리 문자열* 은 쿼리 매개 변수를 제공하는 위치입니다. URL 형식은 아래를 참조하세요.

```http
    https://[service name].search.windows.net/indexes/[index name]/docs?[query string]&api-version=2019-05-06
```

POST에 대한 형식이 동일하지만 쿼리 문자열 매개 변수에서는 `api-version`입니다.

## <a name="pulling-data-into-an-index"></a>인덱스로 데이터 끌어오기

끌어오기 모델은 지원되는 데이터 원본을 탐색하고 인덱스에 데이터를 자동으로 업로드합니다. Azure Cognitive Search에서는 *인덱서* 를 통해 이 기능이 구현되며 현재 다음 플랫폼에서 사용할 수 있습니다.

+ [Blob Storage](search-howto-indexing-azure-blob-storage.md)
+ [Table Storage](search-howto-indexing-azure-tables.md)
+ [Azure Cosmos DB](search-howto-index-cosmosdb.md)
+ [Azure VM의 Azure SQL Database, SQL Managed Instance 및 SQL Server](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [SharePoint Online(미리 보기)](search-howto-index-sharepoint-online.md)
+ [Azure Data Lake Storage Gen2](search-howto-index-azure-data-lake-storage.md)

인덱서는 인덱스를 데이터 원본(일반적으로 테이블, 뷰 또는 이와 동등한 구조)에 연결하고 원본 필드를 인덱스에 해당하는 필드에 매핑합니다. 실행 중에 행 집합은 자동으로 JSON으로 변환되고 지정한 인덱스에 로드됩니다. 모든 인덱서는 데이터를 새로 고치는 빈도를 지정할 수 있도록 일정 예약을 지원합니다. 대부분의 인덱서는 데이터 원본에서 지원하는 경우 변경 내용 추적을 제공합니다. 새 문서를 인식하는 것 외에도 기존 문서에서 변경 사항 및 삭제를 추적하므로 인덱서는 인덱스에서 데이터를 적극적으로 관리해야 할 필요를 없애줍니다.

### <a name="how-to-pull-data-into-an-azure-cognitive-search-index"></a>Azure Cognitive Search 인덱스로 데이터를 끌어오는 방법

인덱서 기능은 [Azure Portal](search-import-data-portal.md), [REST API](/rest/api/searchservice/Indexer-operations) 및 [.NET SDK](/dotnet/api/azure.search.documents.indexes.searchindexerclient)에서 공개되어 있습니다.

포털을 사용하는 한 가지 장점으로 Azure Cognitive Search에서 일반적으로 원본 데이터 세트의 메타데이터를 읽음으로써 기본 인덱스 스키마를 생성할 수 있다는 것입니다. 인덱스를 처리할 때까지 생성된 인덱스를 수정할 수 있으며, 그 후에는 다시 인덱싱할 필요가 없는 스키마만 편집할 수 있습니다. 스키마에 직접 적용하려는 변경 내용이 있으면 인덱스를 다시 작성해야 합니다. 

## <a name="verify-data-import-with-search-explorer"></a>검색 탐색기를 사용하여 데이터 가져오기 확인

문서 업로드에 대한 사전 검사를 수행하는 빠른 방법은 포털에서 **검색 탐색기** 를 사용하는 것입니다. 탐색기를 사용하면 코드를 작성할 필요 없이 인덱스를 쿼리할 수 있습니다. 검색 환경은 [간단한 구문](/rest/api/searchservice/simple-query-syntax-in-azure-search) 및 기본 [searchMode 쿼리 매개 변수](/rest/api/searchservice/search-documents)를 기반으로 합니다. 결과는 JSON으로 반환되므로 전체 문서를 검사할 수 있습니다.

> [!TIP]
> 수많은 [Azure Cognitive Search 코드 샘플](https://github.com/Azure-Samples/?utf8=%E2%9C%93&query=search)에는 포함되거나 쉽게 사용할 수 있는 데이터 세트가 있어 손쉽게 시작할 수 있는 방법을 제공합니다. 또한 포털에서는 작은 부동산 데이터 세트("realestate-us-sample")로 구성된 샘플 인덱서 및 데이터 원본도 제공합니다. 샘플 데이터 원본에 미리 구성된 인덱서를 실행하면 인덱스가 검색 탐색기 또는 작성한 코드로 쿼리할 수 있는 문서로 만들어지고 로드됩니다.

## <a name="see-also"></a>참고 항목

+ [인덱서 개요](search-indexer-overview.md)
+ [포털 연습: 인덱스 만들기, 로드, 쿼리](search-get-started-portal.md)
