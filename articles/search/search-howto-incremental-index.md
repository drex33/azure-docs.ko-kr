---
title: 증분 보강에 캐싱 사용(미리 보기)
titleSuffix: Azure Cognitive Search
description: AI 보강 파이프라인에서 다운스트림 기술 및 프로젝션을 수정할 때 재사용할 수 있도록 보강된 콘텐츠의 캐싱을 사용하도록 설정합니다.
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/15/2021
ms.openlocfilehash: b905517a2558eb6bc01b4ba218f0ecde7e830ebf
ms.sourcegitcommit: 5361d9fe40d5c00f19409649e5e8fed660ba4800
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/18/2021
ms.locfileid: "130138596"
---
# <a name="enable-caching-for-incremental-enrichment-in-azure-cognitive-search"></a>Azure Cognitive Search 증분 보강에 캐싱 사용

> [!IMPORTANT] 
> 이 기능은 [추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에 따라 퍼블릭 미리 보기로 제공됩니다. [미리 보기 REST API](/rest/api/searchservice/index-preview)에서 이 기능을 지원합니다.

이 문서에서는 매번 전체로 다시 빌드하지 않고도 다운스트림 보강 단계를 수정할 수 있도록 보강 파이프라인에 캐싱을 추가하는 방법을 설명합니다. 기본적으로 기술 세트는 상태 비저장이며 해당 컴포지션의 어떤 부분이든 변경하려면 인덱서 전체를 다시 실행해야 합니다. [**증분 보강을**](cognitive-search-incremental-indexing-conceptual.md)사용하면 인덱서는 기술 자료 또는 인덱서 정의에서 검색된 변경 내용에 따라 새로 고쳐야 하는 문서 트리 부분을 결정할 수 있습니다. 처리된 기존 출력은 유지되고 가능한 모든 곳에서 재사용됩니다. 

캐시된 콘텐츠는 제공된 계정 정보를 사용하여 Azure Storage에 보관됩니다. 인덱서를 실행하면 `ms-az-search-indexercache-<alpha-numerc-string>`이라는 컨테이너가 생성됩니다. 이 컨테이너는 검색 서비스에서 관리하는 내부 구성 요소로 간주해야 하며 수정할 수 없습니다.

인덱서를 설정하는 방법을 잘 모르겠으면 [인덱서 개요](search-indexer-overview.md)에서 시작하고 [기술 세트](cognitive-search-working-with-skillsets.md)를 계속 진행하여 보강 파이프라인에 대해 알아보세요. 주요 개념에 대한 자세한 배경 정보는 [증분 보강](cognitive-search-incremental-indexing-conceptual.md)을 참조하세요.

## <a name="prerequisites"></a>필수 조건

Azure Storage 캐시된 보강을 저장하는 데 사용됩니다. 스토리지 계정은 [범용 v2](../storage/common/storage-account-overview.md#types-of-storage-accounts)여야 합니다.

인덱서에서 캐시를 사용하도록 설정하려면 미리 보기 API 또는 베타 Azure SDK가 필요합니다. 포털은 현재 보강 캐싱 옵션을 제공하지 않습니다.

## <a name="enable-on-new-indexers"></a>새 인덱서에서 사용

새 인덱서에서 [Create 또는 Update Indexer(2021-04-30-Preview)를](/rest/api/searchservice/preview-api/create-or-update-indexer)호출할 때 인덱서 정의 페이로드에 "cache" 속성을 추가합니다. 이전 미리 보기 API 버전인 2020-06-30-Preview를 사용할 수도 있습니다.

```https
POST https://[service name].search.windows.net/indexers?api-version=2021-04-30-Preview
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

## <a name="enable-on-existing-indexers"></a>기존 인덱서에서 사용

이미 기술 항목이 있는 기존 인덱서의 경우 다음 단계를 사용하여 캐싱을 추가합니다. 일회성 작업으로 인덱서 전체를 다시 설정했다가 다시 실행하여 캐시를 로드합니다.

### <a name="step-1-get-the-indexer-definition"></a>1단계: 인덱서 정의 가져오기

데이터 원본, 기술 집합, 인덱스 구성 요소가 있는 유효한 작업 인덱서로 시작합니다. API 클라이언트를 사용하여 [GET 인덱서](/rest/api/searchservice/get-indexer) 요청을 보내 인덱서를 검색합니다. 미리 보기 API 버전을 사용하여 인덱서 GET을 사용하는 경우 null로 설정된 "캐시" 속성이 정의에 자동으로 추가됩니다.

```http
GET https://[YOUR-SEARCH-SERVICE].search.windows.net/indexers/[YOUR-INDEXER-NAME]?api-version=2021-04-30-Preview
    Content-Type: application/json
    api-key: [YOUR-ADMIN-KEY]
```

### <a name="step-2-set-the-cache-property"></a>2단계: 캐시 속성 설정

인덱스 정의에서 다음과 같은 필수 및 선택적 속성을 포함하도록 "cache"를 수정합니다.

+ (필수) `storageConnectionString` 는 Azure Storage 연결 문자열로 설정해야 합니다.
+ (선택 사항) `enableReprocessing` 부울 `true` 속성(기본적으로 )은 증분 보강이 사용됨을 나타냅니다. 새 `false` 문서 인덱싱과 같은 다른 리소스 집약적 작업이 진행되는 동안 증분 처리를 일시 중단하려는 경우 로 설정한 다음 나중에 다시 `true` 전환합니다.

```http
POST https://[service name].search.windows.net/indexers?api-version=2021-04-30-Preview
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

### <a name="step-3-reset-the-indexer"></a>3단계: 인덱서 다시 설정

모든 문서가 일관된 상태인지 확인하려면 기존 [인덱서에](/rest/api/searchservice/reset-indexer) 대한 증분 보강을 설정할 때 인덱서 다시 설정이 필요합니다. 이 작업에 포털 또는 API 클라이언트를 사용할 수 있습니다.

```https
POST https://[YOUR-SEARCH-SERVICE].search.windows.net/indexers/[YOUR-INDEXER-NAME]/reset?api-version=2021-04-30-Preview
    Content-Type: application/json
    api-key: [YOUR-ADMIN-KEY]
```

### <a name="step-4-save-the-indexer"></a>4단계: 인덱서 저장

[인덱서(2021-04-30-Preview)를](/rest/api/searchservice/preview-api/create-or-update-indexer) PUT 요청으로 업데이트합니다. 여기서 요청 본문에는 "cache"가 포함됩니다.

```http
PUT https://[YOUR-SEARCH-SERVICE].search.windows.net/indexers/[YOUR-INDEXER-NAME]?api-version=2021-04-30-Preview
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

> [!NOTE]
> 인덱서를 다시 설정한 후 다시 실행하면 콘텐츠를 캐시할 수 있도록 인덱서 전체가 다시 빌드됩니다. 모든 문서에서 모든 인식 보강이 다시 실행됩니다. 캐시에서 보강된 콘텐츠 다시 사용은 캐시가 로드된 후에 시작됩니다.
>

## <a name="check-for-cached-output"></a>캐시된 출력 확인

blob 컨테이너 아래의 Azure Storage 캐시를 찾습니다. 컨테이너 이름은  `ms-az-search-indexercache-<some-alphanumeric-string>` 입니다.

캐시는 인덱서에서 만들어지고 사용됩니다. 해당 콘텐츠는 사람이 읽을 수 없습니다.

캐시가 작동하는지 확인하려면 기술 도구를 수정하고 인덱서를 실행한 다음 실행 시간 및 문서 수에 대한 전후 메트릭을 비교합니다. 

이미지 분석 및 스캔된 문서의 OCR(광학 문자 인식)을 포함하는 기술 세트는 좋은 테스트 사례를 만듭니다. 다운스트림 텍스트 기술 또는 이미지와 관련이 없는 기술을 수정하는 경우 인덱서가 캐시에서 이전에 처리한 이미지 및 OCR 콘텐츠를 모두 검색하여 편집 내용에 표시된 텍스트 관련 변경 내용만 업데이트하고 처리할 수 있습니다.  인덱서 실행 문서 수에서 더 적은 문서, 더 짧은 실행 시간 및 청구서에 대한 더 적은 요금을 볼 수 있습니다. 

[cog-search-demo 자습서에](cognitive-search-tutorial-blob.md) 사용되는 [파일 집합은](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/ai-enrichment-mixed-media) JPG, PNG, HTML, DOCX, PPTX 및 기타 형식의 14개 파일을 포함하기 때문에 유용한 테스트 사례입니다. `en` `es` 증분 보강의 개념 증명 테스트를 위해 텍스트 번역 기술에서 또는 다른 언어로 변경합니다.

## <a name="common-errors"></a>일반 오류

요청에 미리 보기 API 버전을 지정하지 않으면 다음 오류가 발생합니다.

`"The request is invalid. Details: indexer : A resource without a type name was found, but no expected type was specified. To allow entries without type information, the expected type must also be specified when the model is specified."`

인덱서 요구 사항이 누락된 경우에도 400 잘못된 요청 오류가 발생합니다. 오류 메시지는 누락된 모든 dependencies를 지정합니다.

## <a name="next-steps"></a>다음 단계

증분 보강은 기술 세트가 포함된 인덱서에 적용되어 인덱스와 지식 저장소 모두에 재사용 가능한 콘텐츠를 제공합니다. 다음 링크는 캐싱 및 기술 세트에 대한 자세한 정보를 제공합니다.

+ [증분 보강(수명 주기 및 관리)](cognitive-search-incremental-indexing-conceptual.md)
+ [기술 세트 개념 및 컴퍼지션](cognitive-search-working-with-skillsets.md)
+ [기술 세트 만들기](cognitive-search-defining-skillset.md)
+ [자습서: REST 및 AI를 사용하여 Azure Blob에서 검색 가능한 콘텐츠 생성](cognitive-search-tutorial-blob.md)