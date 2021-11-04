---
title: 인덱서 실행 또는 다시 설정
titleSuffix: Azure Cognitive Search
description: 인덱서를 전체로 실행 하거나 인덱서, 기술 또는 개별 문서를 다시 설정 하 여 검색 인덱스나 기술 자료 저장소의 전부 또는 일부를 새로 고칩니다.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/02/2021
ms.openlocfilehash: e29c511a59d8b446b497a8fd4ff393c9a9c683e9
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131449301"
---
# <a name="run-or-reset-indexers-skills-or-documents"></a>인덱서, 기술 또는 문서 실행 또는 다시 설정

인덱서는 요청 시, 일정에 따라 또는 [인덱서가 생성](/rest/api/searchservice/create-indexer)될 때 세 가지 방법으로 호출할 수 있습니다. 초기 실행 후, 인덱서는 내부의 “상위 워터 마크”를 통해 어떤 검색 문서를 인덱스하였는지 추적합니다. 마커가 API에 노출되는 것은 아니지만 내부적으로는 다음 실행 시 중단된 위치에서 다시 시작할 수 있도록 인덱서가 인덱싱이 중지된 위치를 파악합니다.

처음부터 프로세스를 다시 시작하려면 인덱서를 다시 설정하여 상위 워터마크를 정리할 수 있습니다. 다시 설정 API는 다음의 개체 계층 구조에서 감소 수준으로 사용할 수 있습니다.

+ 전체 검색 목록([인덱서 다시 설정](#reset-indexers) 사용)
+ 특정 문서 또는 문서 목록([문서 다시 설정 - 미리 보기](#reset-docs)사용)
+ 특정 기술 또는 보강 ( [스킬 다시 설정 사용-미리 보기](#reset-skills))

다시 설정 API는 [AI 보강](cognitive-search-concept-intro.md) 시나리오에서 적용되는 캐시된 콘텐츠를 새로 고치거나 상위 워터마크를 정리하고 인덱스를 다시 빌드하는 경우에 사용합니다.

실행 후 다시 설정을 통해 기존 문서와 새 문서의 프로세스를 다시 시작할 수 있지만, 이전 실행 시 만들었던 검색 인덱스 내의 분리된 검색 문서를 제거하지는 않습니다. 삭제 관련 추가 정보는 [문서 추가, 업데이트 또는 삭제하기](/rest/api/searchservice/addupdate-or-delete-documents)를 참조하세요.

## <a name="how-to-run-indexers"></a>인덱서를 실행 하는 방법

[인덱서 만들기](/rest/api/searchservice/create-indexer)는 비활성화 상태("disabled": true)로 만들지 않는 한 인덱서를 만들어 실행합니다. 개체를 함께 만들어야 하므로 첫 번째 실행은 다소 오래 걸립니다.

[인덱서 실행](/rest/api/searchservice/run-indexer)은 검색 인덱스를 데이터 원본과 동기화하는 데 필요한 항목만 검색하여 처리합니다. Blob Storage에는 기본 제공된 변경 검색 기능이 있습니다. Azure SQL 또는 Cosmos DB 등의 기타 데이터 원본에서는 인덱서가 새로운 행과 업데이트된 행을 읽기 전에 변경 검색과 관련한 구성을 해 주어야 합니다.

다음 중 한 가지 방법을 통해 인덱서를 실행할 수 있습니다.

+ 인덱서 페이지의 **실행** 명령을 통한 Azure Portal
+ [인덱서 실행(REST)](/rest/api/searchservice/run-indexer)
+ Azure.NET SDK의 [RunIndexers 메서드](/dotnet/api/azure.search.documents.indexes.searchindexerclient.runindexer)(또는 다른 SDK의 동일한 RunIndexer 메서드 사용)

## <a name="indexer-execution"></a>인덱서 실행

인덱서를 실행할 때는 다음의 제한 사항이 적용됩니다.

+ 최대 인덱서 작업 수는 복제본 당 1입니다.

  인덱서 실행이 이미 용량에 있는 경우 "인덱서를 실행 하지 못했습니다." \<indexer-name\> 오류: "다른 인덱서 호출이 현재 진행 중입니다. 동시 호출은 허용 되지 않습니다. "

+ 기술를 사용 하는 경우 최대 실행 시간은 2 시간이 고, 그렇지 않으면 24 시간입니다. 

  [대량 데이터 집합을 인덱싱하](search-howto-large-index.md)는 경우 인덱서를 일정에 배치 하 여 처리를 확장할 수 있습니다. 무료 계층에는 런타임 한도가 낮습니다. 전체 목록은 [인덱서 제한](search-limits-quotas-capacity.md#indexer-limits)을 참조하세요.

<a name="reset-indexers"></a>

## <a name="reset-an-indexer"></a>인덱서 다시 설정

인덱서 다시 설정은 전체를 아우릅니다. 검색 인덱스 내에서 인덱서가 채운 검색 문서는 전체 처리를 위해 표시됩니다. 새 문서를 발견하면 기본 원본이 인덱스에 검색 문서로 추가됩니다. 인덱서가 기술 세트 및 [캐싱](search-howto-incremental-index.md)을 사용하도록 구성되어 있는 경우, 해당 기술 세트는 다시 실행되고 해당 캐시는 새로 고쳐집니다.

위의 방법 중 하나를 사용해 인덱서를 실행하기 전에 다음 방법 중 하나를 사용해 인덱서를 다시 설정할 수 있습니다.

+ 인덱서 페이지의 **다시 설정** 명령을 통한 Azure Portal
+ [인덱서 다시 설정(REST)](/rest/api/searchservice/reset-indexer)
+ Azure.NET SDK의 [ResetIndexers 메서드](/dotnet/api/azure.search.documents.indexes.searchindexerclient.resetindexer)(또는 다른 SDK의 동일한 RunIndexer 메서드 사용)

실행 완료 후에는 다시 설정 플래그가 지워집니다. 데이터 원본에 대해 작동하는 규칙적인 변경 검색 논리는 다음 실행 시 다시 시작되며 나머지 데이터 세트에 있는 기타 새로운 값이나 업데이트된 값을 선택합니다.

> [!NOTE]
> 다시 설정 요청은 재처리 대상(인덱서, 기술 또는 문서)을 결정하지만, 이외의 인덱서 런타임 동작에는 영향을 주지 않습니다. 인덱서에 런타임 매개 변수, 필드 매핑, 캐싱, 일괄 처리 옵션 등이 있는 경우, 이러한 설정은 인덱서 다시 설정 후 실행 시에 모두 적용됩니다.

<a name="reset-skills"></a>

## <a name="reset-skills-preview"></a>기술 다시 설정(미리 보기)

기술 세트가 있는 인덱서의 경우 특정 기술을 다시 설정하여 해당 기술 및 그 출력에 의해 좌우되는 모든 다운스트림 기술의 처리를 강제할 수 있습니다. [캐시 강화](search-howto-incremental-index.md)도 새로 고쳐집니다. 기술을 다시 설정하면 캐시 결과가 무효화되며, 이는 기술의 새로운 버전이 배포되어 있는 상태에서 인덱서가 해당 기술을 전체 문서에 다시 실행하고자 할 때 유용합니다. 

[다시 설정 기술은](/rest/api/searchservice/preview-api/reset-skills) REST 이상에서 사용할 수 있습니다 **`api-version=2020-06-30-Preview`** .

```http
POST https://[service name].search.windows.net/skillsets/[skillset name]/resetskills?api-version=2020-06-30-Preview
{
    "skillNames" : [
        "#1",
        "#5",
        "#6"
    ]
}
```

위 예시에 나온 것처럼 개별 기술을 지정할 수 있지만, 해당 기술 가운데 하나라도 목록에 없는 기술(2~4)의 출력이 필요한 경우, 캐시가 필요한 정보를 제공할 수 있는 경우가 아니라면 목록에 없는 기술이 실행됩니다. 이러한 경우를 true로 설정하려면 기술 2~4에 대한 캐시 강화는 기술 1에 대한 종속성이 없어야 합니다(다시 설정을 위해 나열됨).

기술을 지정하지 않은 경우에는 전체 기술 세트가 실행되며 캐싱이 설정되었다면 해당 캐시도 새로 고쳐집니다.

<a name="reset-docs"></a>

## <a name="reset-docs-preview"></a>문서 다시 설정(미리 보기)

[문서 API 다시 설정](/rest/api/searchservice/preview-api/reset-documents)은 문서 키 목록을 수락하여 특정 문서를 새로 고칠 수 있도록 합니다. 지정된 경우, 다시 설정된 매개 변수는 기본 데이터의 기타 변경 사항과 상관없이 처리 대상의 유일한 결정자가 됩니다. 예를 들어, 인덱서가 마지막으로 실행된 이후 Blob이 20개 추가 또는 업데이트되었지만 문서는 하나만 다시 설정했다면 해당 문서 하나만 처리됩니다.

문서별로 해당 검색 문서의 모든 필드가 데이터 원본 값으로 새로 고쳐집니다. 새로 고칠 필드를 골라 선택할 수 없습니다. 

문서가 기술 세트를 통해 강화되었으며 캐시된 데이터가 있는 경우, 해당 기술 세트는 지정된 문서만을 위해 호출되고 캐시는 재처리한 문서를 위해 업데이트됩니다.

최초로 해당 API를 테스트할 때 동작을 유효성 검사하고 테스트하는 데 도움이 되는 API는 다음과 같습니다.

+ API 버전 이상으로 [인덱서 상태를 가져와](/rest/api/searchservice/get-indexer-status) **`api-version=2020-06-30-Preview`** 다시 설정 상태 및 실행 상태를 확인 합니다. 해당 상태 응답의 마지막에서 다시 설정 요청에 대한 정보를 찾을 수 있습니다.
+ API 버전 이상을 사용 하 여 [문서를 다시 설정](/rest/api/searchservice/preview-api/reset-documents) **`api-version=2020-06-30-Preview`** 하 여 처리할 문서를 지정 합니다.
+ 인덱서를 실행하는 [인덱서 실행](/rest/api/searchservice/run-indexer)(모든 API 버전)
+ 업데이트된 값을 확인하고 값이 확실치 않을 경우 문서 키를 반환하는 [문서 검색](/rest/api/searchservice/search-documents). 응답으로 표시되는 필드를 제한하려는 경우에는 `"select": "<field names>"`를 사용합니다.

### <a name="formulate-and-send-the-reset-request"></a>다시 설정 요청 작성 및 보내기

```http
POST https://[service name].search.windows.net/indexers/[indexer name]/resetdocs?api-version=2020-06-30-Preview
{
    "documentKeys" : [
        "1001",
        "4452"
    ]
}
```

요청을 통해 제공된 문서 키는 검색 인덱스의 값으로, 데이터 원본에서 대응되는 필드와는 다를 수 있습니다. 해당 키 값이 확실치 않은 경우, [쿼리 보내기](search-query-create.md)를 통해 해당 값을 반환합니다. `select`를 사용하여 해당 문서의 키 필드만을 반환할 수 있습니다.

구문 분석 모드로 여러 검색 문서에 구문 분석된 Blob의 경우(예를 들어 [jsonLines 또는 jsonArrays](search-howto-index-json-blobs.md) 아니면 [delimitedText](search-howto-index-csv-blobs.md)를 사용한 경우), 인덱서가 해당 문서 키를 생성하며, 이 문서 키가 알 수 없는 문서 키일 수 있습니다. 이 경우, 올바른 값을 제공하는 데 해당 문서 키에 대한 쿼리를 사용할 수 있습니다.

각각의 키로 API를 여러 번 호출하면 새로운 키가 문서 키 다시 설정 목록에 추가됩니다. true로 설정된 **`overwrite`** 매개 변수로 API를 호출하면 해당 요청의 페이로드로 다시 설정하도록 현재 문서 키 목록을 덮어씁니다.

```http
POST https://[service name].search.windows.net/indexers/[indexer name]/resetdocs?api-version=2020-06-30-Preview
{
    "documentKeys" : [
        "200",
        "630"
    ],
    "overwrite": true
}
```

## <a name="check-reset-status"></a>다시 설정 상태 확인

다시 설정 상태를 확인 하 고 처리를 위해 큐에 대기 중인 문서 키를 확인 하려면 이상에서 [인덱서 상태 가져오기](/rest/api/searchservice/get-indexer-status) 를 사용 **`api-version=06-30-2020-Preview`** 합니다. 미리 보기 API는 인덱서 상태 가져오기 응답의 마지막에서 찾을 수 있는 **`currentState`** 섹션을 반환합니다.

AI 보강을 통해 채워지는 필드로 인해 잠재적으로는 모든 문서가 영향을 받게 되므로, "모드"는 다시 설정 기술에 대해 **`indexingAllDocs`** 와 같습니다.

문서 다시 설정의 경우, 해당 모드는 **`indexingResetDocs`** 로 설정됩니다. 인덱서는 문서 다시 설정 호출 시 제공되는 모든 문서 키가 처리될 때까지 현재 상태를 유지하며 이 작업이 진행되는 중에는 다른 인덱서 작업이 실행되지 않습니다. 문서 키 목록의 모든 문서를 찾기 위해서는 각 문서의 위치를 찾고 키와 일치하도록 이를 해독할 필요가 있으며 데이터 세트의 규모가 큰 경우 시간이 오래 걸릴 수 있습니다. Blob 컨테이너에 수백 개의 Blob이 포함되어 있고 다시 설정하려는 문서가 마지막에 있는 경우, 인덱서는 다른 모든 항목을 먼저 확인할 때까지 일치하는 Blob을 찾지 못합니다.

```json
"currentState": {
    "mode": "indexingResetDocs",
    "allDocsInitialTrackingState": "{\"LastFullEnumerationStartTime\":\"2021-02-06T19:02:07.0323764+00:00\",\"LastAttemptedEnumerationStartTime\":\"2021-02-06T19:02:07.0323764+00:00\",\"NameHighWaterMark\":null}",
    "allDocsFinalTrackingState": "{\"LastFullEnumerationStartTime\":\"2021-02-06T19:02:07.0323764+00:00\",\"LastAttemptedEnumerationStartTime\":\"2021-02-06T19:02:07.0323764+00:00\",\"NameHighWaterMark\":null}",
    "resetDocsInitialTrackingState": null,
    "resetDocsFinalTrackingState": null,
    "resetDocumentKeys": [
        "200",
        "630"
    ]
}
```

문서를 재처리한 후, 인덱서는 **`indexingAllDocs`** 모드로 복귀하여 다음 실행 시 다른 새 문서나 업데이트된 문서를 처리합니다.

## <a name="next-steps"></a>다음 단계

다시 설정 API는 다음 인덱서 실행의 범위를 알리는 데 사용됩니다. 실제 처리를 위해서는 주문형 인덱서 실행을 호출하거나 작업 완료를 위해 예약된 작업을 허용하도록 해야 합니다. 실행을 완료하면 인덱서는 예약을 통해 처리하든 주문형으로 처리하든 상관없이 정상 처리로 돌아갑니다.

인덱서 작업을 다시 설정하고 다시 실행한 다음에는 검색 서비스를 통해 상태를 모니터링하거나 진단 로깅을 통해 자세한 정보를 얻을 수 있습니다.

+ [인덱서 작업(REST)](/rest/api/searchservice/indexer-operations)
+ [검색 인덱서 상태 모니터](search-howto-monitor-indexers.md)
+ [로그 데이터 수집 및 분석](search-monitor-logs.md)
+ [인덱서 예약](search-howto-schedule-indexers.md)