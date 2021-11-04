---
title: CSV Blob 검색
titleSuffix: Azure Cognitive Search
description: delimitedText 구문 분석 모드를 사용하여 Azure Blob Storage에서 CSV를 추출하고 가져옵니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/01/2021
ms.openlocfilehash: f3db72dacc67b4e0235c2b982d547475542bc49f
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131475277"
---
# <a name="how-to-index-csv-blobs-using-delimitedtext-parsing-mode-and-blob-indexers-in-azure-cognitive-search"></a>Azure Cognitive Search에서 delimitedText 구문 분석 모드 및 Blob 인덱서를 사용하여 CSV Blob을 인덱싱하는 방법

Azure Cognitive Search [blob 인덱서](search-howto-indexing-azure-blob-storage.md)는 CSV의 각 줄을 별도의 검색 문서로 처리하는 CSV 파일에 대한 `delimitedText` 구문 분석 모드를 제공합니다. 예를 들어, 쉼표로 구분된 다음 텍스트가 주어지면 `delimitedText`는 검색 색인에 두 개의 문서가 됩니다. 

```text
id, datePublished, tags
1, 2016-01-12, "azure-search,azure,cloud"
2, 2016-07-07, "cloud,mobile"
```

`delimitedText` 구문 분석 모드가 없으면 CSV 파일의 전체 내용이 하나의 검색 문서로 처리됩니다.

단일 Blob에서 여러 검색 문서를 만들 때마다 [여러 검색 문서를 생성하기 위한 Blob 인덱싱](search-howto-index-one-to-many-blobs.md)을 검토하여 문서 키 할당이 작동하는 방식을 이해합니다. Blob 인덱서는 각각의 새 문서를 고유하게 정의하는 값을 찾거나 생성할 수 있습니다. 특히, Blob이 더 작은 부분으로 구문 분석될 때 생성되는 일시적인 `AzureSearch_DocumentKey`를 생성할 수 있습니다. 그러면 값이 인덱스에서 검색 문서의 키로 사용됩니다.

## <a name="setting-up-csv-indexing"></a>CSV 인덱싱 설정

CSV blob을 인덱싱하려면 [인덱서 만들기](/rest/api/searchservice/create-indexer) 요청에서 `delimitedText` 구문 분석 모드로 인덱서 정의를 만들거나 업데이트합니다.

```http
{
  "name" : "my-csv-indexer",
  ... other indexer properties
  "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "firstLineContainsHeaders" : true } }
}
```

`firstLineContainsHeaders` 은(는) 각 Blob의 첫 번째(비어 있지 않은) 줄이 헤더를 포함하는 것을 나타냅니다.
Blob이 초기 헤더 줄을 포함하지 않는 경우 헤더는 인덱서 구성에서 지정되어야 합니다. 

```http
"parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } } 
```

`delimitedTextDelimiter` 구성 설정을 사용하여 구분 기호 문자를 사용자 지정할 수 있습니다. 예를 들면 다음과 같습니다.

```http
"parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextDelimiter" : "|" } }
```

> [!NOTE]
> 현재는 UTF-8 인코딩만 지원됩니다. 다른 인코딩을 지원해야 하는 경우 [UserVoice](https://feedback.azure.com/d365community/forum/9325d19e-0225-ec11-b6e6-000d3a4f07b8)에서 투표하세요.

> [!IMPORTANT]
> 구분된 텍스트 구문 분석 모드를 사용하는 경우 Azure Cognitive Search는 데이터 원본의 모든 Blob이 CSV라고 가정합니다. 동일한 데이터 원본에서 CSV 및 비 CSV Blob을 지원해야 하는 경우 [UserVoice](https://feedback.azure.com/d365community/forum/9325d19e-0225-ec11-b6e6-000d3a4f07b8)에서 투표하세요.
>

## <a name="request-examples"></a>요청 예제

다음은 이를 모두 포함한 전체 페이로드 예제입니다. 

데이터 원본: 

```http
POST https://[service name].search.windows.net/datasources?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "my-blob-datasource",
    "type" : "azureblob",
    "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
    "container" : { "name" : "my-container", "query" : "<optional, my-folder>" }
}   
```

인덱서:

```http
POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  "name" : "my-csv-indexer",
  "dataSourceName" : "my-blob-datasource",
  "targetIndexName" : "my-target-index",
  "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } }
}
```


