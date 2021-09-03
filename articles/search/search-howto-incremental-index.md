---
title: 캐시 및 증분 보강 구성(미리 보기)
titleSuffix: Azure Cognitive Search
description: 인식 기술 세트에서 제어형 처리를 위해 캐싱을 사용하도록 설정하고 보강된 콘텐츠의 상태를 유지합니다. 이 기능은 현재 공개 미리 보기로 제공됩니다.
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: 4165768837f590690a39226b983b4d32361957e3
ms.sourcegitcommit: f2eb1bc583962ea0b616577f47b325d548fd0efa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2021
ms.locfileid: "114730562"
---
# <a name="how-to-configure-caching-for-incremental-enrichment-in-azure-cognitive-search"></a>Azure Cognitive Search에서 증분 보강을 위해 캐싱을 구성하는 방법

> [!IMPORTANT] 
> 이 기능은 [추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에 따라 퍼블릭 미리 보기로 제공됩니다. [미리 보기 REST API](/rest/api/searchservice/index-preview)에서 이 기능을 지원합니다.

이 문서에서는 매번 다시 빌드하지 않고도 단계를 증분 방식으로 수정할 수 있도록 보강 파이프라인에 캐싱을 추가하는 방법을 보여 줍니다. 기본적으로 기술 세트는 상태 비저장이며 해당 컴포지션의 어떤 부분이든 변경하려면 인덱서 전체를 다시 실행해야 합니다. 증분 보강을 사용하면 인덱서가 기술 세트 또는 인덱서 정의에서 검색된 변경 내용을 기준으로 새로 고쳐야 하는 문서 트리 부분을 확인할 수 있습니다. 처리된 기존 출력은 유지되고 가능한 모든 곳에서 재사용됩니다. 

캐시된 콘텐츠는 제공된 계정 정보를 사용하여 Azure Storage에 보관됩니다. 인덱서를 실행하면 `ms-az-search-indexercache-<alpha-numerc-string>`이라는 컨테이너가 생성됩니다. 이 컨테이너는 검색 서비스에서 관리하는 내부 구성 요소로 간주해야 하며 수정할 수 없습니다.

인덱서를 설정하는 방법을 잘 모르겠으면 [인덱서 개요](search-indexer-overview.md)에서 시작하고 [기술 세트](cognitive-search-working-with-skillsets.md)를 계속 진행하여 보강 파이프라인에 대해 알아보세요. 주요 개념에 대한 자세한 배경 정보는 [증분 보강](cognitive-search-incremental-indexing-conceptual.md)을 참조하세요.

## <a name="enable-caching-on-an-existing-indexer"></a>기존 인덱서에서 캐싱 사용

이미 기술 세트를 포함하는 기존 인덱서가 있는 경우 이 섹션의 단계에 따라 캐싱을 추가합니다. 일회성 작업으로 인덱서 전체를 다시 설정한 후 다시 실행해야 증분 처리가 적용됩니다.

> [!TIP]
> 이 [포털 빠른 시작](cognitive-search-quickstart-blob.md)을 개념 증명으로 실행하여 필요한 개체를 만든 다음, Postman 또는 포털을 사용하여 업데이트를 수행할 수 있습니다. 청구 가능한 Cognitive Services 리소스를 연결하는 것이 좋습니다. 인덱서를 여러 번 실행하면 모든 단계를 완료하기 전에 일일 무료 할당이 소모됩니다.

### <a name="step-1-get-the-indexer-definition"></a>1단계: 인덱서 정의 가져오기

데이터 원본, 기술 세트, 인덱스 등의 구성 요소가 있는 유효한 기존 인덱서로 시작합니다. 인덱서를 실행할 수 있어야 합니다. 

API 클라이언트를 사용하여 인덱서의 현재 구성을 가져오는 [인덱서 가져오기 요청](/rest/api/searchservice/get-indexer)을 생성합니다. 미리 보기 API 버전을 사용하여 인덱서를 가져오는 경우 Null로 설정된 `cache` 속성이 정의에 추가됩니다.

```http
GET https://[YOUR-SEARCH-SERVICE].search.windows.net/indexers/[YOUR-INDEXER-NAME]?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [YOUR-ADMIN-KEY]
```

응답에서 인덱서 정의를 복사합니다.

### <a name="step-2-modify-the-cache-property-in-the-indexer-definition"></a>2단계: 인덱서 정의의 cache 속성 수정

기본적으로 `cache` 속성은 Null입니다. API 클라이언트를 사용하여 캐시 구성을 설정합니다(포털에서는 이 세분화된 업데이트를 지원하지 않음). 

다음 필수 및 선택적 속성을 포함하도록 캐시 개체를 수정합니다. 

+ `storageConnectionString`은 필수이며, Azure Storage 연결 문자열로 설정해야 합니다. 
+ `enableReprocessing` 부울 속성은 선택 사항(기본적으로 `true`)이며 증분 보강을 사용할 수 있음을 나타냅니다. 필요한 경우 `false`로 설정하여 새 문서 인덱싱과 같은 다른 리소스 집약적 작업이 진행되는 동안 증분 처리를 일시 중단했다가 나중에 다시 `true`로 전환할 수 있습니다.

```json
{
    "name": "<YOUR-INDEXER-NAME>",
    "targetIndexName": "<YOUR-INDEX-NAME>",
    "dataSourceName": "<YOUR-DATASOURCE-NAME>",
    "skillsetName": "<YOUR-SKILLSET-NAME>",
    "cache" : {
        "storageConnectionString" : "<YOUR-STORAGE-ACCOUNT-CONNECTION-STRING>",
        "enableReprocessing": true
    },
    "fieldMappings" : [],
    "outputFieldMappings": [],
    "parameters": []
}
```

> [!NOTE]
> 인덱서 캐시에는 범용 v2 스토리지 계정이 필요합니다. 자세한 내용은 [다양한 유형의 스토리지 계정](../storage/common/storage-account-overview.md#types-of-storage-accounts)을 참조하세요.

### <a name="step-3-reset-the-indexer"></a>3단계: 인덱서 다시 설정

모든 문서를 일관된 상태로 유지하려면 기존 인덱서에 대해 증분 보강을 설정할 때 인덱서를 다시 설정해야 합니다. 이 작업을 위해 포털 또는 API 클라이언트와 [인덱서 다시 설정 REST API](/rest/api/searchservice/reset-indexer)를 사용할 수 있습니다.

```http
POST https://[YOUR-SEARCH-SERVICE].search.windows.net/indexers/[YOUR-INDEXER-NAME]/reset?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [YOUR-ADMIN-KEY]
```

### <a name="step-4-save-the-updated-definition"></a>4단계: 업데이트된 정의 저장

PUT 요청을 사용하여 [인덱서를 업데이트](/rest/api/searchservice/preview-api/update-indexer)합니다. 요청 본문에는 cache 속성이 있는 업데이트된 인덱서 정의가 포함되어야 합니다. 400이 표시되는 경우 인덱서 정의를 검사하여 모든 요구 사항(데이터 원본, 기술 세트, 인덱스)이 충족되었는지 확인합니다.

```http
PUT https://[YOUR-SEARCH-SERVICE].search.windows.net/indexers/[YOUR-INDEXER-NAME]?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [YOUR-ADMIN-KEY]
{
    "name" : "<YOUR-INDEXER-NAME>",
    ...
    "cache": {
        "storageConnectionString": "<YOUR-STORAGE-ACCOUNT-CONNECTION-STRING>",
        "enableReprocessing": true
    }
}
```

이제 인덱서에서 다른 GET 요청을 실행하면 서비스의 응답에 캐시 개체의 `ID` 속성이 포함됩니다. 캐시된 모든 결과와 이 인덱서에서 처리된 각 문서의 중간 상태를 포함하는 컨테이너의 이름 뒤에 영숫자 문자열이 추가됩니다. 이 ID는 Blob 스토리지에서 캐시의 고유 이름으로 사용됩니다.

```http
    "cache": {
        "ID": "<ALPHA-NUMERIC STRING>",
        "enableReprocessing": true,
        "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<YOUR-STORAGE-ACCOUNT>;AccountKey=<YOUR-STORAGE-KEY>;EndpointSuffix=core.windows.net"
    }
```

### <a name="step-5-run-the-indexer"></a>5단계: 인덱서 실행

인덱서를 실행하려면 포털 또는 API를 사용할 수 있습니다. 포털의 인덱서 목록에서 인덱서를 선택하고 **실행** 을 클릭합니다. 포털을 사용하는 경우의 장점 중 하나는 인덱서 상태, 작업 기간, 처리된 문서 수를 모니터링할 수 있다는 것입니다. 포털 페이지는 몇 분마다 새로 고쳐집니다.

또는 REST를 사용하여 [인덱서를 실행](/rest/api/searchservice/run-indexer)할 수 있습니다.

```http
POST https://[YOUR-SEARCH-SERVICE].search.windows.net/indexers/[YOUR-INDEXER-NAME]/run?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [YOUR-ADMIN-KEY]
```

인덱서를 실행한 후에는 Azure Blob Storage에서 캐시를 찾을 수 있습니다. 컨테이너 이름의 형식은 `ms-az-search-indexercache-<YOUR-CACHE-ID>`입니다.

> [!NOTE]
> 인덱서를 다시 설정한 후 다시 실행하면 콘텐츠를 캐시할 수 있도록 인덱서 전체가 다시 빌드됩니다. 모든 문서에서 모든 인식 보강이 다시 실행됩니다.
>

### <a name="step-6-modify-a-skillset-and-confirm-incremental-enrichment"></a>6단계: 기술 세트 수정 및 증분 보강 확인

기술 세트를 수정하려면 포털 또는 API를 사용할 수 있습니다. 예를 들어 텍스트 번역을 사용하는 경우 `en`에서 `es` 또는 다른 언어로 간단한 인라인 변경을 수행하면 증분 보강의 개념 증명 테스트로 충분합니다.

인덱서를 다시 실행합니다. 보강 문서 트리의 해당 부분만 업데이트됩니다. [포털 빠른 시작](cognitive-search-quickstart-blob.md)을 개념 증명으로 사용하고 텍스트 번역 기술을 ‘es’로 수정한 경우 원래의 14개가 아니라 8개 문서만 업데이트됩니다. 변환 프로세스의 영향을 받지 않는 이미지 파일은 캐시에서 재사용됩니다.

## <a name="enable-caching-on-new-indexers"></a>새 인덱서에서 캐싱 사용

새 인덱서에 대해 증분 보강을 설정하려면 [인덱서 만들기(2020-06-30-Preview)](/rest/api/searchservice/preview-api/create-indexer)를 호출할 때 인덱서 정의 페이로드에 `cache` 속성을 포함하기만 하면 됩니다. 


```json
{
    "name": "<YOUR-INDEXER-NAME>",
    "targetIndexName": "<YOUR-INDEX-NAME>",
    "dataSourceName": "<YOUR-DATASOURCE-NAME>",
    "skillsetName": "<YOUR-SKILLSET-NAME>",
    "cache" : {
        "storageConnectionString" : "<YOUR-STORAGE-ACCOUNT-CONNECTION-STRING>",
        "enableReprocessing": true
    },
    "fieldMappings" : [],
    "outputFieldMappings": [],
    "parameters": []
    }
}
```

## <a name="checking-for-cached-output"></a>캐시된 출력 확인

캐시는 인덱서를 통해 생성, 사용, 관리되며 해당 콘텐츠는 사람이 읽을 수 있는 형식으로 표시되지 않습니다. 캐시가 사용되는지 여부를 확인하는 가장 좋은 방법은 인덱서를 실행하고 실행 시간 및 문서 수에 대한 이전 및 이후 메트릭을 비교하는 것입니다. 

예를 들어 스캔한 문서의 이미지 분석 및 OCR(광학 인식)로 시작하여 결과 텍스트의 다운스트림 분석을 수행하는 기술 세트를 가정합니다. 다운스트림 텍스트 기술을 수정하면 인덱서가 이전에 처리된 이미지 및 OCR 콘텐츠를 캐시에서 모두 검색하고 사용자 편집으로 표시된 텍스트 관련 변경 내용만 업데이트하고 처리할 수 있습니다. 문서 수에 더 적은 문서가 표시되고(예: 원래 실행 시 14/14 대비 8/8), 실행 시간이 단축되며, 청구서의 요금이 줄어들 수 있습니다.

## <a name="working-with-the-cache"></a>캐시 작업

캐시가 작동하면 [인덱서 실행](/rest/api/searchservice/run-indexer)을 호출할 때마다 인덱서가 캐시를 검사하여 기존 출력에서 사용할 수 있는 부분을 확인합니다. 

다음 표에는 다양한 API와 캐시 간 관계가 요약되어 있습니다.

| API           | 캐시 영향     |
|---------------|------------------|
| [인덱서 만들기(2020-06-30-Preview)](/rest/api/searchservice/preview-api/create-indexer) | 인덱서 정의에서 지정한 경우 캐시 만들기를 포함하여 처음 사용할 때 인덱서를 만들고 실행합니다. |
| [인덱서 실행](/rest/api/searchservice/run-indexer) | 요청 시 보강 파이프라인을 실행합니다. 이 API는 캐시가 있는 경우 캐시를 읽고, 업데이트된 인덱서 정의에 캐싱을 추가한 경우 캐시를 만듭니다. 캐싱이 사용하도록 설정된 인덱서를 실행하면 캐시된 출력을 사용할 수 있는 경우 인덱서에서 단계를 생략합니다. 이 API의 일반 공급 또는 미리 보기 API 버전을 사용할 수 있습니다.|
| [인덱서 다시 설정](/rest/api/searchservice/reset-indexer)| 인덱서에서 모든 증분 인덱싱 정보를 지웁니다. 다음 인덱서 실행(요청 시 또는 예약)은 모든 기술을 다시 실행하고 캐시를 다시 빌드하는 것을 포함하여 처음부터 완전히 다시 처리됩니다. 인덱서를 삭제하고 다시 만드는 것과 기능적으로 동일합니다. 이 API의 일반 공급 또는 미리 보기 API 버전을 사용할 수 있습니다.|
| [기술 다시 설정](/rest/api/searchservice/preview-api/reset-skills) | 기술을 수정하지 않은 경우에도 다음 인덱서 실행 시 다시 실행할 기술을 지정합니다. 캐시는 그에 따라 업데이트됩니다. 지식 저장소 또는 검색 인덱스와 같은 출력은 캐시의 재사용 가능한 데이터와 업데이트된 기술별 새 콘텐츠를 사용하여 새로 고쳐집니다. |

캐시에서 수행되는 작업을 제어하는 방법에 대한 자세한 내용은 [캐시 관리](cognitive-search-incremental-indexing-conceptual.md#cache-management)를 참조하세요.

## <a name="next-steps"></a>다음 단계

증분 보강은 기술 세트가 포함된 인덱서에서 적용할 수 있습니다. 다음 단계로 기술 세트 설명서를 참조하여 개념과 컴퍼지션을 파악합니다. 

또한 캐시를 사용하도록 설정한 후에는 특정 동작을 재정의하거나 강제 적용하는 방법을 포함하여 캐싱과 관련된 매개 변수 및 API에 대해 알아보는 것이 좋습니다. 자세한 내용은 다음 링크를 참조하세요.

+ [기술 세트 개념 및 컴퍼지션](cognitive-search-working-with-skillsets.md)
+ [기술 세트를 만드는 방법](cognitive-search-defining-skillset.md)
+ [증분 보강 및 캐싱 소개](cognitive-search-incremental-indexing-conceptual.md)