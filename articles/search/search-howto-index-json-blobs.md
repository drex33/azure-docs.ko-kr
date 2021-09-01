---
title: JSON blob 검색
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search Blob 인덱서를 사용하여 텍스트 콘텐츠에 대해 Azure JSON Blob을 크롤링합니다. 인덱서는 Azure Blob Storage와 같은 선택된 데이터 원본에 대해 데이터 수집을 자동화합니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/01/2021
ms.openlocfilehash: ba6df6b29c9d7b9b388b34dab163a132272c6200
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2021
ms.locfileid: "123223903"
---
# <a name="how-to-index-json-blobs-using-a-blob-indexer-in-azure-cognitive-search"></a>Azure Cognitive Search에서 Blob 인덱서를 사용하여 JSON Blob을 인덱싱하는 방법

이 문서에서는 JSON 문서로 구성된 Blob에 대해 [Blob 인덱서를 구성](search-howto-indexing-azure-blob-storage.md)하는 방법을 보여 줍니다. Azure Blob Storage의 JSON Blob은 일반적으로 다음과 같은 형식으로 간주됩니다.

+ 단일 JSON 문서
+ 올바른 형식의 JSON 요소 배열을 포함하는 JSON 문서
+ 줄 바꿈으로 구분된 여러 엔터티를 포함하는 JSON 문서

Blob 인덱서는 다음 옵션으로 구성된 구조 구문 분석 모드에 따라 검색 문서의 출력을 최적화하는 **`parsingMode`** 매개 변수를 제공합니다.

| parsingMode | JSON 문서 | Description |
|--------------|-------------|--------------|
| **`json`** | Blob 당 하나 | (기본값) JSON Blob을 텍스트의 단일 청크로 구문 분석합니다. 각 JSON Blob은 단일 검색 문서가 됩니다. |
| **`jsonArray`** | Blob 당 여러 개 | Blob에서 JSON 배열을 구문 분석합니다. 여기서 배열의 각 요소는 별도의 검색 문서가 됩니다.  |
| **`jsonLines`** | Blob 당 여러 개 | 개별 요소가 줄 바꿈으로 구분된 여러 JSON 엔터티(또한 배열)를 포함하는 Blob을 구문 분석합니다. 인덱서는 새 줄마다 새 검색 문서를 시작합니다. |

**`jsonArray`** 및 **`jsonLines`** 둘 다에 대해 [하나의 Blob 인덱싱을 검토하여 여러 검색 문서 생성](search-howto-index-one-to-many-blobs.md)을 검토하여 Blob 인덱서가 동일한 Blob에서 생성된 여러 검색 문서에 대한 문서 키 명확성을 처리하는 방법을 이해해야 합니다.

인덱서 정의 내에서 [필드 매핑](search-indexer-field-mappings.md)을 설정하여 대상 검색 인덱스를 채우는 데 사용되는 원본 JSON 문서의 속성을 선택할 수도 있습니다. 예를 들어 **`jsonArray`** 구문 분석 모드를 사용할 경우 배열이 하위 수준 속성으로 존재하면 Blob 내에서 배열의 위치를 나타내는 **`document root`** 속성을 설정할 수 있습니다.

다음 섹션에서는 각 모드에 대해 자세히 설명합니다. 인덱서 클라이언트 및 개념을 잘 모르는 경우 [검색 인덱서 만들기](search-howto-create-indexers.md)를 참조하세요. [기본 Blob 인덱서 구성](search-howto-indexing-azure-blob-storage.md)의 세부 정보에 대해서도 잘 알고 있어야 합니다. 이 내용은 여기에서 반복해서 설명하지 않습니다.

<a name="parsing-single-blobs"></a>

## <a name="index-single-json-documents-one-per-blob"></a>단일 JSON 문서(Blob당 하나) 인덱싱

기본적으로 Blob 인덱서는 JSON Blob을 컨테이너의 각 Blob에 대해 하나의 검색 문서에 해당하는 단일 텍스트 청크로 구문 분석합니다. JSON이 구조화된 경우 검색 문서는 개별 요소가 개별 필드로 표시되는 해당 구조를 반영할 수 있습니다. 예를 들어 Azure Blob Storage에 다음 JSON 문서가 있다고 가정합니다.

```http
{
    "article" : {
        "text" : "A hopefully useful article explaining how to parse JSON blobs",
        "datePublished" : "2020-04-13",
        "tags" : [ "search", "storage", "howto" ]    
    }
}
```

Blob 인덱서는 JSON 문서를 단일 검색 문서로 구문 분석하고, 동일한 이름 및 형식의 대상 인덱스 필드에서 원본의 "text", "datePublished" 및 "tags"를 검색하여 인덱스를 로드합니다. "text", "datePublished" 및 "tags" 필드를 포함하는 인덱스가 지정된 Blob 인덱서는 요청에서 필드 매핑 없이 올바른 매핑을 유추할 수 있습니다.

기본 동작은 JSON Blob당 하나의 검색 문서이지만 'json' 구문 분석 모드를 설정하면 콘텐츠에 대한 내부 필드 매핑이 변경되고 `content` 내부 필드가 검색 인덱스의 실제 필드로 승격됩니다. **`json`** 구문 분석 모드의 예제 인덱서 정의는 다음과 같습니다.

```http
POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "my-json-indexer",
    "dataSourceName" : "my-blob-datasource",
    "targetIndexName" : "my-target-index",
    "parameters" : { "configuration" : { "parsingMode" : "json" } }
}
```

> [!NOTE]
> 모든 인덱서와 마찬가지로 필드가 명확하게 일치하지 않으면 [기본 Blob 인덱서 구성](search-howto-indexing-azure-blob-storage.md)에 설명된 대로 Blob 콘텐츠 및 메타데이터에 사용할 수 있는 암시적 필드 매핑을 사용하지 않는 한, 개별 [필드 매핑](search-indexer-field-mappings.md)을 명시적으로 지정해야 합니다.

### <a name="json-example-single-hotel-json-files"></a>json 예제(단일 호텔 JSON 파일)

GitHub의 [호텔 JSON 문서 데이터 세트](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/hotels/hotel-json-documents)는 JSON 구문 분석을 테스트하는 데 유용합니다. 여기서 각 Blob은 구조화된 JSON 파일을 나타냅니다. 데이터 파일을 Blob Storage에 업로드하고 **데이터 가져오기** 마법사를 사용하여 이 콘텐츠를 개별 검색 문서로 구문 분석하는 방법을 신속하게 평가할 수 있습니다. 

데이터 세트는 5개의 Blob으로 구성되며, 각 Blob에는 주소 컬렉션과 객실 컬렉션이 포함된 호텔 문서가 있습니다. Blob 인덱서는 두 컬렉션을 모두 검색하고 인덱스 스키마에 입력 문서 구조를 반영합니다.

<a name="parsing-arrays"></a>

## <a name="parse-json-arrays"></a>JSON 배열 구문 분석

또는 JSON 배열 옵션을 사용할 수 있습니다. 이 옵션은 Blob이 올바른 형식의 JSON 개체 배열을 포함하고 각 배열 요소를 별도의 Azure Search 문서로 나타내려고 하는 경우에 유용합니다. **`jsonArrays`** 를 사용할 경우 다음 JSON Blob은 각각 `"id"` 및 `"text"` 필드를 포함하는 세 개의 개별 문서를 생성합니다.  

```text
[
    { "id" : "1", "text" : "example 1" },
    { "id" : "2", "text" : "example 2" },
    { "id" : "3", "text" : "example 3" }
]
```

인덱서에 대한 **`parameters`** 속성은 구문 분석 모드 값을 포함합니다. JSON 배열의 경우 인덱서 정의는 다음 예제와 비슷해야 합니다.

```http
POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "my-json-indexer",
    "dataSourceName" : "my-blob-datasource",
    "targetIndexName" : "my-target-index",
    "parameters" : { "configuration" : { "parsingMode" : "jsonArray" } }
}
```

### <a name="jsonarrays-example-clinical-trials-sample-data"></a>jsonArrays 예제(임상 체험용 샘플 데이터)

GitHub의 [임상 체험용 json 데이터 세트](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/clinical-trials/clinical-trials-json)는 JSON 배열 구문 분석을 테스트하는 데 유용합니다. 데이터 파일을 Blob Storage에 업로드하고 **데이터 가져오기** 마법사를 사용하여 이 콘텐츠를 개별 검색 문서로 구문 분석하는 방법을 신속하게 평가할 수 있습니다. 

데이터 세트는 총 100개의 엔터티에 대해 각각 엔터티의 JSON 배열을 포함하는 8개의 Blob으로 구성됩니다. 엔터티의 채워지는 필드는 다를 수 있지만 최종 결과는 모든 Blob의 모든 배열에서 엔터티마다 하나의 검색 문서가 됩니다.

<a name="nested-json-arrays"></a>

### <a name="parsing-nested-json-arrays"></a>중첩된 JSON 배열 구문 분석

중첩된 요소가 있는 JSON 배열의 경우 **`documentRoot`** 를 지정하여 다중 수준 구조를 나타낼 수 있습니다. 예를 들어 blob은 다음과 같습니다.

```http
{
    "level1" : {
        "level2" : [
            { "id" : "1", "text" : "Use the documentRoot property" },
            { "id" : "2", "text" : "to pluck the array you want to index" },
            { "id" : "3", "text" : "even if it's nested inside the document" }  
        ]
    }
}
```

다음 구성을 사용하여 `level2` 속성에 포함된 배열을 인덱싱합니다.

```http
{
    "name" : "my-json-array-indexer",
    ... other indexer properties
    "parameters" : { "configuration" : { "parsingMode" : "jsonArray", "documentRoot" : "/level1/level2" } }
}
```

## <a name="parse-json-entities-separated-by-newlines"></a>줄바꿈으로 구분된 JSON 엔터티 구문 분석

Blob이 줄 바꿈으로 구분된 여러 JSON 엔터티를 포함하고 각 요소가 별도의 검색 문서가 되도록 하려면 **`jsonLines`** 를 사용합니다.

```text
{ "id" : "1", "text" : "example 1" }
{ "id" : "2", "text" : "example 2" }
{ "id" : "3", "text" : "example 3" }
```

JSON 배열의 경우 인덱서 정의는 다음 예제와 비슷해야 합니다.

```http
POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "my-json-indexer",
    "dataSourceName" : "my-blob-datasource",
    "targetIndexName" : "my-target-index",
    "parameters" : { "configuration" : { "parsingMode" : "jsonLines" } }
}
```

## <a name="map-json-fields-to-search-fields"></a>JSON 필드를 검색 필드에 매핑

필드 매핑은 필드 이름과 형식이 동일하지 않은 경우 원본 필드를 대상 필드와 연결하는 데 사용됩니다. 그러나 필드 매핑을 사용하여 JSON 문서의 일부를 일치시키고 검색 문서의 최상위 필드로 "리프트"할 수도 있습니다.

다음 예제에서는 이 시나리오를 보여 줍니다. 일반적인 필드 매핑에 대한 자세한 내용은 [필드 매핑](search-indexer-field-mappings.md)을 참조하세요.

```http
{
    "article" : {
        "text" : "A hopefully useful article explaining how to parse JSON blobs",
        "datePublished" : "2016-04-13"
        "tags" : [ "search", "storage", "howto" ]    
    }
}
```

`Edm.String` 형식의 `text`, `Edm.DateTimeOffset` 형식의 `date` 및 `Collection(Edm.String)` 형식의 `tags` 필드를 포함하는 검색 인덱스가 있다고 가정해 봅니다. 원본의 "datePublished"와 인덱스의 `date` 필드 간에 불일치를 확인합니다. JSON을 원하는 모양으로 매핑하려면 다음 필드 매핑을 사용합니다.

```http
"fieldMappings" : [
    { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
    { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
    { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
    ]
```

원본 필드는 [JSON 포인터](https://tools.ietf.org/html/rfc6901) 표기법을 사용하여 지정됩니다. JSON 문서의 루트를 참조하도록 슬래시로 시작한 다음 슬래시로 구분된 경로를 사용하여 (임의의 중첩 수준에서) 원하는 속성을 선택합니다.

또한 0부터 시작하는 인덱스를 사용하여 개별 배열 요소를 참조할 수 있습니다. 예를 들어 위의 예제에서 "tags" 배열의 첫 번째 요소를 선택하려면 다음과 같은 필드 매핑을 사용합니다.

```http
{ "sourceFieldName" : "/article/tags/0", "targetFieldName" : "firstTag" }
```

> [!NOTE]
> **`sourceFieldName`** 이 JSON에 없는 속성을 참조하는 경우 해당 매핑은 오류 없이 건너뜁니다. 이 동작을 사용하면 다른 스키마가 있는 JSON Blob(일반적인 사용 사례)에 대해 인덱싱이 계속 진행될 수 있습니다. 유효성 검사가 수행되지 않기 때문에 잘못된 이유로 문서를 잃는 일이 없도록 매핑을 신중히 확인해야 합니다.
>

## <a name="next-steps"></a>다음 단계

+ [Blob 인덱서 구성](search-howto-indexing-azure-blob-storage.md)
+ [필드 매핑 정의](search-indexer-field-mappings.md)
+ [인덱서 개요](search-indexer-overview.md)
+ [Blob 인덱서를 사용하여 CSV Blob을 인덱싱하는 방법](search-howto-index-csv-blobs.md)
+ [자습서: Azure Blob Storage에서 반구조화된 데이터 검색](search-semi-structured-data.md)