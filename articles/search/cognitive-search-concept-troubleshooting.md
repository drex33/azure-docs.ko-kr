---
title: AI 보강 디자인을 위한 팁
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search에서 AI 보강 파이프라인을 설정하기 위한 팁 및 문제 해결.
author: LiamCavanagh
ms.author: liamca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/16/2021
ms.openlocfilehash: 2c8cbc3496b0a7b12f8df114ed2aa993ee5cb1a7
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128610867"
---
# <a name="tips-for-ai-enrichment-in-azure-cognitive-search"></a>Azure Cognitive Search의 AI 보강을 위한 팁

이 문서에는 Azure Cognitive Search에서 AI 보강 기능을 시작할 때 계속 진행하기 위한 팁과 요령 목록이 포함되어 있습니다. 

아직 그렇게 하지 않은 경우 Blob 데이터 원본에 AI 보강을 적용하려면 [자습서: AI 보강 API를 호출하는 방법 알아보기](cognitive-search-quickstart-blob.md)를 단계별로 실행합니다.

## <a name="tip-1-start-with-a-small-dataset"></a>팁 1: 작은 데이터 세트로 시작
문제를 신속하게 찾는 최상의 방법은 문제를 해결할 수 있는 속도를 높이는 것입니다. 인덱싱 시간을 줄이는 최상의 방법은 인덱싱할 문서 수를 줄이는 것입니다. 

약간의 문서/레코드를 사용하여 데이터 원본을 만들어 시작합니다. 문서 샘플에는 인덱싱할 다양한 문서가 잘 표현되어 있어야 합니다. 

엔드투엔드 파이프라인을 통해 문서 샘플을 실행하고 결과가 사용자의 요구를 충족하는지 확인합니다. 결과에 만족하면 데이터 원본에 더 많은 파일을 추가할 수 있습니다.

## <a name="tip-2-make-sure-your-data-source-credentials-are-correct"></a>팁 2: 데이터 원본 자격 증명이 올바른지 확인
데이터 원본 연결을 사용하는 인덱서를 정의하기까지 연결의 유효성은 검사되지 않습니다. 인덱서가 데이터에 액세스할 수 없다는 오류가 표시되면 다음을 확인합니다.
- 연결 문자열이 올바릅니다. 특히 SAS 토큰을 만드는 경우 Azure Cognitive Search에서 예상한 형식을 사용하는지 확인합니다. 지원되는 다른 형식에 대해 알려면 [자격 증명을 지정하는 방법 섹션](search-howto-indexing-azure-blob-storage.md#credentials)을 참조합니다.
- 인덱서의 사용자 컨테이너 이름은 올바릅니다.

## <a name="tip-3-see-what-works-even-if-there-are-some-failures"></a>팁 3: 일부 오류가 있는 경우라도 작동 조건 참조
경우에 따라 작은 오류가 해당 트랙에서 인덱서를 중지합니다. 차례로 문제를 해결하려는 경우 괜찮습니다. 그러나 특정 유형의 오류를 무시하고 어떤 흐름이 실제로 작동하는지 확인할 수 있도록 인덱서를 계속 허용하려 할 수 있습니다.

이 경우 인덱서에게 오류를 무시하라고 명령하고 싶을 수 있습니다. *maxFailedItems* 및 *maxFailedItemsPerBatch* 를 -1로 인덱서 정의의 일부로 설정하여 그렇게 합니다.

```
{
  "// rest of your indexer definition
   "parameters":
   {
      "maxFailedItems":-1,
      "maxFailedItemsPerBatch":-1
   }
}
```
> [!NOTE]
> 프로덕션 작업에 대해 maxFailedItems, maxFailedItemsPerBatch를 0으로 설정하는 것이 가장 좋습니다.

## <a name="tip-4-use-debug-sessions-to-identify-and-resolve-issues-with-your-skillset"></a>팁 4: 디버그 세션을 사용하여 기술 세트의 문제 식별 및 해결 

디버그 세션은 Azure Portal의 기존 기술 세트와 작동하는 비주얼 편집기입니다. 디버그 세션 내에서 오류를 식별 및 해결하고, 변경 내용의 유효성을 검사하고, 변경 내용을 AI 보강 파이프라인의 기술 세트에 커밋할 수 있습니다. 이는 [설명서를 읽는](./cognitive-search-debug-session.md) 미리 보기 기능입니다. 개념 및 시작에 대한 자세한 내용은 [디버그 세션](./cognitive-search-tutorial-debug-sessions.md)을 참조하세요.

단일 문서에 대한 디버그 세션 작업은 보다 복잡한 보강 파이프라인을 반복적으로 빌드할 수 있는 좋은 방법입니다.

## <a name="tip-5-looking-at-enriched-documents-under-the-hood"></a>팁 5: 내부적으로 보강된 문서 살펴보기 
보강된 문서는 보강 중에 만들어진 임시 구조체로, 프로세싱이 완료되면 삭제됩니다.

인덱싱 중에 만들어진 보강된 문서의 스냅샷을 캡처하려면 ```enriched```라는 필드를 인덱스에 추가합니다. 인덱서는 해당 문서의 모든 보강 문자열 표현에 대한 필드에 자동으로 덤프됩니다.

```enriched``` 필드는 JSON으로 보강된 메모리 내 문서의 논리적 표현인 문자열을 포함하게 됩니다.  그러나 필드 값은 유효한 JSON 문서입니다. 따옴표가 이스케이프되므로 문서를 JSON 형식으로 보려면 `\"`를 `"`로 바꿔야 합니다. 

보강된 필드는 디버깅용이며, 식을 평가하는 콘텐츠의 논리적 형식을 쉽게 이해하도록 도와주는 역할만 합니다. 인덱싱 목적으로 이 필드에 의존해서는 안 됩니다.

디버깅 목적에서 인덱스 정의의 일부로 ```enriched``` 필드를 추가합니다.

#### <a name="request-body-syntax"></a>요청 본문 구문
```json
{
  "fields": [
    // other fields go here.
    {
      "name": "enriched",
      "type": "Edm.String",
      "searchable": false,
      "sortable": false,
      "filterable": false,
      "facetable": false
    }
  ]
}
```

## <a name="tip-6-expected-content-fails-to-appear"></a>팁 6: 예상 콘텐츠가 표시되지 않음

누락된 콘텐츠는 인덱싱 동안 삭제되는 문서의 결과물일 수 있습니다. 무료 및 기본 계층은 문서 크기에 대한 제한이 낮습니다. 제한을 초과하는 모든 파일은 인덱싱 동안 삭제됩니다. Azure Portal에서 삭제된 문서를 확인할 수 있습니다. 검색 서비스 대시보드에서 인덱서 타일을 두 번 클릭합니다. 성공적으로 인덱싱된 문서의 비율을 검토합니다. 100%가 아닌 경우 자세한 내용을 보려면 비율을 클릭하면 됩니다. 

문제가 파일 크기와 관련된 경우 "Blob \<file-name>은 현재 서비스 계층에 대해 문서 추출을 위한 최대 크기를 초과하는 \<file-size> 바이트 크기입니다."라는 오류가 표시될 수 있습니다. 인덱서 제한에 대한 자세한 내용은 [서비스 제한](search-limits-quotas-capacity.md)을 참조하세요.

콘텐츠를 표시하는 데 실패한 두 번째 이유는 관련된 입/출력 매핑 오류일 수 있습니다. 예를 들어 출력 대상 이름은 "People"이지만 인덱스 필드 이름은 소문자 "people"입니다. 실제로 필드가 비어있을 때 인덱싱이 성공한 것으로 생각하도록 시스템이 전체 파이프라인에 대한 201 성공 메시지를 반환할 수 있습니다. 

## <a name="tip-7-extend-processing-beyond-maximum-run-time-24-hour-window"></a>팁 7: 최대 실행 시간(24시간 범위)을 초과하여 처리 확장

이미지 분석은 단순한 사례라도 계산 집약적이므로 이미지가 특히 크거나 복잡한 경우 처리 시간이 최대 허용 시간을 초과할 수 있습니다. 

최대 실행 시간은 무료 계층에는 몇 분 인덱싱, 청구 가능 계층에는 24시간 인덱싱 등 계층에 따라 다릅니다. 요청 시 처리를 위해 처리를 24시간 내에 완료하지 못하면 인덱서가 중단된 경우 처리를 선택할 수 있도록 일정을 전환합니다. 

예약된 인덱서의 경우 인덱싱은 마지막으로 알려진 좋은 문서에서 일정에 따라 다시 시작합니다. 처리되지 않은 이미지를 모두 처리할 때까지, 인덱서는 반복 일정을 사용하여 일련의 시간 또는 일이 지나는 동안 이미지 백로그를 통해 자기 방식대로 작동할 수 있습니다. 일정 구문에 대한 자세한 내용은 [인덱서 예약](search-howto-schedule-indexers.md)을 참조합니다.

> [!NOTE]
> 인덱서가 특정 일정으로 설정되어 있지만 실행될 때마다 같은 문서를 반복적으로 실패하는 경우 인덱서는 성공적으로 다시 진행될 때까지 빈도가 낮은 간격(최대 24시간마다 한 번 이상)으로 실행을 시작합니다.  인덱서가 특정 지점에서 고착되는 문제를 해결한 것으로 판단되는 경우 인덱서의 요청 시 실행을 수행할 수 있으며, 성공적으로 진행되면 인덱서는 설정된 일정 간격으로 다시 돌아갑니다.

포털 기반 인덱싱의 경우(빠른 시작에서 설명한 대로) "한 번 실행" 인덱서 옵션을 선택하면 처리 시간을 1시간(`"maxRunTime": "PT1H"`)으로 제한합니다. 처리 창을 조금 더 길게 확장하고 싶을 수 있습니다.

## <a name="tip-8-increase-indexing-throughput"></a>팁 8: 인덱싱 처리량 증가

[병렬 인덱싱](search-howto-large-index.md)의 경우 여러 컨테이너에 데이터를 또는 동일한 컨테이너 내부에 여러 가상 폴더를 저장합니다. 그런 다음, 여러 데이터 원본 및 인덱서 쌍을 만듭니다. 모든 인덱서는 동일한 기술 집합을 사용하며 동일한 대상 검색 인덱스에 쓸 수 있으므로 검색 앱이 이 분할을 알 필요가 없습니다.

## <a name="see-also"></a>참고 항목

+ [빠른 시작: 포털에서 AI 보강 파이프라인 만들기](cognitive-search-quickstart-blob.md)
+ [자습서: AI 보강 REST API 학습](cognitive-search-tutorial-blob.md)
+ [Blob 인덱서를 구성하는 방법](search-howto-indexing-azure-blob-storage.md)
+ [기술 집합을 정의하는 방법](cognitive-search-defining-skillset.md)
+ [인덱스에 보강 필드를 매핑하는 방법](cognitive-search-output-field-mapping.md)
