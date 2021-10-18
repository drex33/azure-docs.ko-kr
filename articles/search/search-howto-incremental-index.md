---
title: 증분 보강 캐싱을 사용 하도록 설정 (미리 보기)
titleSuffix: Azure Cognitive Search
description: AI 보강 파이프라인에서 다운스트림 기술 및 프로젝션을 수정할 때 보강 콘텐츠 캐싱을 사용 하도록 설정 합니다.
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/15/2021
ms.openlocfilehash: 3972c73813b0921245ec9490d45aa16331b71b21
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/18/2021
ms.locfileid: "130131147"
---
# <a name="enable-caching-for-incremental-enrichment-in-azure-cognitive-search"></a>Azure Cognitive Search에서 증분 보강 캐싱을 사용 하도록 설정

> [!IMPORTANT] 
> 이 기능은 [추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에 따라 퍼블릭 미리 보기로 제공됩니다. [미리 보기 REST API](/rest/api/searchservice/index-preview)에서 이 기능을 지원합니다.

이 문서에서는 보강 파이프라인에 캐싱을 추가 하는 방법에 대해 설명 합니다. 그러면 항상 완전히 다시 빌드하지 않고도 다운스트림 보강 단계를 수정할 수 있습니다. 기본적으로 기술 세트는 상태 비저장이며 해당 컴포지션의 어떤 부분이든 변경하려면 인덱서 전체를 다시 실행해야 합니다. 증분 보강을 사용 하면 인덱서는 기술 또는 인덱서 정의에서 검색 된 변경 내용에 따라 문서 트리 부분을 새로 고쳐야 하는지 확인할 수 있습니다. 처리된 기존 출력은 유지되고 가능한 모든 곳에서 재사용됩니다. 

캐시된 콘텐츠는 제공된 계정 정보를 사용하여 Azure Storage에 보관됩니다. 인덱서를 실행하면 `ms-az-search-indexercache-<alpha-numerc-string>`이라는 컨테이너가 생성됩니다. 이 컨테이너는 검색 서비스에서 관리하는 내부 구성 요소로 간주해야 하며 수정할 수 없습니다.

인덱서를 설정하는 방법을 잘 모르겠으면 [인덱서 개요](search-indexer-overview.md)에서 시작하고 [기술 세트](cognitive-search-working-with-skillsets.md)를 계속 진행하여 보강 파이프라인에 대해 알아보세요. 주요 개념에 대한 자세한 배경 정보는 [증분 보강](cognitive-search-incremental-indexing-conceptual.md)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

Azure Storage는 캐시 된 강화를 저장 하는 데 사용 됩니다. 저장소 계정은 [범용 v2](../storage/common/storage-account-overview.md#types-of-storage-accounts)여야 합니다.

인덱서에 캐시를 사용 하려면 미리 보기 Api 또는 베타 Azure Sdk가 필요 합니다. 포털은 현재 보강 캐싱을 위한 옵션을 제공 하지 않습니다.

## <a name="enable-on-new-indexers"></a>새 인덱서에 사용

[Create 또는 Update 인덱서 (2021-04-30-Preview)](/rest/api/searchservice/preview-api/create-or-update-indexer)를 호출할 때 새 인덱서에서 인덱서 정의 페이로드에 "cache" 속성을 추가 합니다. 이전 미리 보기 API 버전인 2020-06-30-Preview를 사용할 수도 있습니다.

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

## <a name="enable-on-existing-indexers"></a>기존 인덱서에 사용

이미 기술가 있는 기존 인덱서의 경우 다음 단계를 사용 하 여 캐싱을 추가 합니다. 일회성 작업으로 인덱서를 전체로 다시 설정 하 고 다시 실행 하 여 캐시를 로드 합니다.

### <a name="step-1-get-the-indexer-definition"></a>1단계: 인덱서 정의 가져오기

데이터 원본, 기술, 인덱스 등의 구성 요소가 있는 유효한 작업 인덱서를 사용 하 여 시작 합니다. API 클라이언트를 사용 하 여 인덱서를 검색 하는 [GET 인덱서](/rest/api/searchservice/get-indexer) 요청을 보냅니다. Preview API 버전을 사용 하 여 인덱서를 가져오는 경우 "cache" 속성이 null로 설정 되 면 자동으로 정의에 추가 됩니다.

```http
GET https://[YOUR-SEARCH-SERVICE].search.windows.net/indexers/[YOUR-INDEXER-NAME]?api-version=2021-04-30-Preview
    Content-Type: application/json
    api-key: [YOUR-ADMIN-KEY]
```

### <a name="step-2-set-the-cache-property"></a>2 단계: cache 속성 설정

인덱스 정의에서 다음과 같은 필수 및 선택적 속성을 포함 하도록 "cache"를 수정 합니다.

+ (필수) `storageConnectionString` Azure Storage 연결 문자열로 설정 해야 합니다.
+ (선택 사항) `enableReprocessing` 부울 속성 ( `true` 기본값)은 증분 보강 사용 하도록 설정 되어 있음을 나타냅니다. `false`새 문서 인덱싱과 같은 다른 리소스를 많이 사용 하는 작업이 진행 되는 동안 증분 처리를 일시 중단 하려면로 설정 하 고 나중에 다시 전환 하려면로 설정 `true` 합니다.

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

모든 문서가 일관 된 상태가 되도록 기존 인덱서에 대 한 증분 보강을 설정할 때 [인덱서가 다시 설정](/rest/api/searchservice/reset-indexer) 되어야 합니다. 이 작업에는 포털 또는 API 클라이언트를 사용할 수 있습니다.

```https
POST https://[YOUR-SEARCH-SERVICE].search.windows.net/indexers/[YOUR-INDEXER-NAME]/reset?api-version=2021-04-30-Preview
    Content-Type: application/json
    api-key: [YOUR-ADMIN-KEY]
```

### <a name="step-4-save-the-indexer"></a>4 단계: 인덱서 저장

요청 본문에 "cache"가 포함 된 PUT 요청으로 [인덱서 (2021-04-30-Preview)를 업데이트](/rest/api/searchservice/preview-api/create-or-update-indexer) 합니다.

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
> 인덱서를 다시 설정한 후 다시 실행하면 콘텐츠를 캐시할 수 있도록 인덱서 전체가 다시 빌드됩니다. 모든 문서에서 모든 인식 보강이 다시 실행됩니다. 캐시를 로드 한 후 캐시에서 보강 콘텐츠를 다시 사용 하기 시작 합니다.
>

## <a name="check-for-cached-output"></a>캐시 된 출력 확인

Blob 컨테이너 아래에서 Azure Storage 캐시를 찾습니다. 컨테이너 이름은  `ms-az-search-indexercache-<some-alphanumeric-string>` 입니다.

캐시는 인덱서를 사용 하 여 만들고 사용 합니다. 해당 콘텐츠는 사람이 읽을 수 없습니다.

캐시가 작동 하는지 여부를 확인 하려면 기술를 수정 하 고 인덱서를 실행 한 다음 실행 시간 및 문서 개수에 대해 이전 및 이후 메트릭을 비교 합니다. 

스캔 한 문서의 이미지 분석과 OCR (광학 문자 인식)을 포함 하는 기술력과은 좋은 테스트 사례를 만듭니다. 다운스트림 텍스트 기술 또는 이미지와 관련 없는 모든 기술을 수정 하는 경우 인덱서는 이전에 처리 된 모든 이미지 및 OCR 콘텐츠를 캐시에서 검색할 수 있으며, 편집으로 표시 된 텍스트 관련 변경 내용만 업데이트 및 처리할 수 있습니다.  이러한 문서는 인덱서 실행 문서 수, 짧은 실행 시간 및 청구서 청구 금액에 대 한 더 작은 문서를 볼 수 있습니다. 

[코그 자습서](cognitive-search-tutorial-blob.md) 에서 사용 되는 [파일 집합](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/ai-enrichment-mixed-media) 은 JPG, PNG, HTML, .docx, .pptx 및 기타 형식의 14 개 파일이 포함 되어 있으므로 유용한 테스트 사례입니다. `en` `es` 증분 보강의 개념 증명 테스트를 위해 텍스트 번역 기술에서 또는 다른 언어로 변경 합니다.

## <a name="common-errors"></a>일반 오류

요청에서 preview API 버전을 지정 하는 것을 잊은 경우 다음 오류가 발생 합니다.

`"The request is invalid. Details: indexer : A resource without a type name was found, but no expected type was specified. To allow entries without type information, the expected type must also be specified when the model is specified."`

400 잘못 된 요청 오류는 인덱서 요구 사항이 누락 된 경우에도 발생 합니다. 오류 메시지는 누락 된 종속성을 지정 합니다.

## <a name="next-steps"></a>다음 단계

증분 보강는 기술력과를 포함 하는 인덱서에 적용 되며 인덱스와 기술 자료 저장소 모두에 다시 사용할 수 있는 콘텐츠를 제공 합니다. 다음 링크는 기술력과에 대 한 자세한 정보를 제공 합니다.

+ [기술 세트 개념 및 컴퍼지션](cognitive-search-working-with-skillsets.md)
+ [기술 세트 만들기](cognitive-search-defining-skillset.md)
+ [자습서: REST 및 AI를 사용하여 Azure Blob에서 검색 가능한 콘텐츠 생성](cognitive-search-tutorial-blob.md)