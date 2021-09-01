---
title: '자습서: 디버그 세션을 사용하여 기술 세트 수정'
titleSuffix: Azure Cognitive Search
description: 디버그 세션(미리 보기)은 기술 세트에서 문제를 찾고 진단하고 복구하는 데 사용되는 Azure Portal 도구입니다.
author: HeidiSteen
ms.author: heidist
manager: nitinme
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 02/02/2021
ms.openlocfilehash: ab1be78570ef784da6a51eb27d9d18d5c902f092
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2021
ms.locfileid: "123221652"
---
# <a name="tutorial-debug-a-skillset-using-debug-sessions"></a>자습서: 디버그 세션을 사용하여 기술 세트 디버그

기술 세트는 한 가지 기술의 출력이 다른 기술의 입력이 되는 콘텐츠를 분석하거나 변환하는 일련의 작업을 조율합니다. 입력이 출력에 종속되는 경우 기술 세트 정의 및 필드 연결에 실수가 있으면 작업 및 데이터가 누락될 수 있습니다.

Azure Portal의 **디버그 세션** 은 기술 세트의 전체적인 시각화를 제공합니다. 이 도구를 사용하면 특정 단계로 드릴다운하여 작업이 중단될 수 있는 위치를 쉽게 확인할 수 있습니다.

이 문서에서는 **디버그 세션** 을 사용하여 1) 누락된 입력 및 2) 출력 필드 매핑을 찾아서 수정합니다. 이 자습서는 포괄적입니다. 인덱싱할 수 있는 데이터(임상 시험 데이터), 개체를 생성하는 Postman 컬렉션, **디버그 세션** 을 사용하여 기술 세트의 문제를 찾아서 수정하는 지침을 제공합니다.

> [!Important]
> 디버그 세션은 서비스 수준 계약 없이 제공되는 미리 보기 기능으로, 프로덕션 워크로드에는 권장되지 않습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.
>

## <a name="prerequisites"></a>사전 요구 사항

시작하기 전에 다음과 같은 필수 구성 요소를 갖추어야 합니다.

+ 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/).

+ Azure Cognitive Search 서비스 [서비스를 만들거나](search-create-service-portal.md) 현재 구독에서 [기존 서비스를 찾습니다](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices). 이 빠른 시작에서는 체험 서비스를 사용할 수 있습니다. 

+ 샘플 데이터를 호스트하고 디버그 세션 중에 생성된 임시 데이터를 유지하는 데 사용되는 [Blob Storage](../storage/blobs/index.yml)가 있는 Azure Storage 계정

+ REST API를 사용하여 개체를 만들기 위한 [Postman 데스크톱 앱](https://www.getpostman.com/) 및 [Postman 컬렉션](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Debug-sessions)

+ [샘플 데이터(임상 시험)](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/clinical-trials/clinical-trials-pdf-19).

> [!NOTE]
> 또한 이 빠른 시작은 AI에 대한 [Azure Cognitive Services](https://azure.microsoft.com/services/cognitive-services/)를 사용합니다. 워크로드가 너무 작으므로 Cognitive Services는 최대 20개의 트랜잭션을 무료로 처리하기 위해 백그라운드에서 탭으로 처리됩니다. 즉, 추가 Cognitive Services 리소스를 만들지 않고도 이 연습을 완료할 수 있습니다.

## <a name="set-up-your-data"></a>데이터를 설정합니다.

이 섹션에서는 인덱서 및 기술 세트에 작업할 콘텐츠가 있도록 Azure Blob Storage에 샘플 데이터 세트를 만듭니다.

1. 19개 파일로 구성된 [샘플 데이터(clinical-trials-pdf-19)를 다운로드](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/clinical-trials/clinical-trials-pdf-19)합니다.

1. [Azure 스토리지 계정을 만들](../storage/common/storage-account-create.md?tabs=azure-portal)거나 [기존 계정을 찾습니다](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/). 

   + 대역폭 요금이 부과되지 않도록 Azure Cognitive Search와 동일한 지역을 선택합니다.

   + StorageV2(범용 V2) 계정 유형을 선택합니다.

1. 포털에서 Azure Storage 서비스 페이지로 이동하여 Blob 컨테이너를 만듭니다. 액세스 수준을 "프라이빗"으로 지정하는 것이 가장 좋습니다. 컨테이너 이름을 `clinicaltrialdataset`으로 지정합니다.

1. 컨테이너에서 **업로드** 를 클릭하여 첫 번째 단계에서 다운로드하고 압축을 푼 샘플 파일을 업로드합니다.

1. 포털에 있는 동안 Azure Storage에 대한 연결 문자열을 가져와서 저장합니다. 데이터를 인덱싱하는 REST API 호출에 필요합니다. 연결 문자열은 포털의 **설정** > **액세스 키** 에서 가져올 수 있습니다.

## <a name="get-a-key-and-url"></a>키 및 URL 가져오기

REST를 호출하려면 모든 요청에 대한 액세스 키와 서비스 URL이 필요합니다. 검색 서비스는 둘 모두를 사용하여 작성되므로 Azure Cognitive Search를 구독에 추가한 경우 다음 단계에 따라 필요한 정보를 가져옵니다.

1. [Azure Portal에 로그인](https://portal.azure.com/)하고, 검색 서비스 **개요** 페이지에서 URL을 가져옵니다. 엔드포인트의 예는 다음과 같습니다. `https://mydemo.search.windows.net`

1. **설정** > **키** 에서 서비스에 대한 모든 권한의 관리자 키를 가져옵니다. 교체 가능한 두 개의 관리자 키가 있으며, 하나를 롤오버해야 하는 경우 비즈니스 연속성을 위해 다른 하나가 제공됩니다. 개체 추가, 수정 및 삭제 요청 시 기본 또는 보조 키를 사용할 수 있습니다.

   :::image type="content" source="media/search-get-started-rest/get-url-key.png" alt-text="HTTP 엔드포인트 및 액세스 키 가져오기" border="false":::

모든 요청에서 서비스에 보내는 각 요청마다 API 키가 필요합니다. 유효한 키가 있다면 요청을 기반으로 요청을 보내는 애플리케이션과 이를 처리하는 서비스 사이에 신뢰가 쌓입니다.

## <a name="create-data-source-skillset-index-and-indexer"></a>데이터 원본, 기술 세트, 인덱스 및 인덱서 만들기

이 섹션에서는 Postman 및 제공된 컬렉션을 사용하여 Cognitive Search 데이터 원본, 기술 세트, 인덱스 및 인덱서를 만듭니다. Postman에 익숙하지 않은 경우 [이 빠른 시작](search-get-started-rest.md)을 참조하세요.

이 작업을 완료하려면 이 자습서용으로 만든 [Postman 컬렉션](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Debug-sessions)이 필요합니다. 

1. Postman을 시작하고 컬렉션을 가져옵니다. **파일** > **새로 만들기** 아래에서 가져올 컬렉션을 선택합니다.
1. 컬렉션을 가져온 후 작업 목록(...)을 펼칩니다.
1. **편집** 을 클릭합니다.
1. 현재 값에 `searchService`의 이름을 입력합니다(예: 엔드포인트가 `https://mydemo.search.windows.net`이면 서비스 이름은 "`mydemo`").
1. 검색 서비스의 기본 키 또는 보조 키를 사용하여 `apiKey`를 입력합니다.
1. Azure Storage 계정의 키 페이지에서 `storageConnectionString`을 입력합니다.
1. 스토리지 계정에서 만든 컨테이너의 `containerName`을 입력한 다음, **업데이트** 를 클릭합니다.

   :::image type="content" source="media/cognitive-search-debug/postman-enter-variables.png" alt-text="Postman의 변수 편집":::

컬렉션에는 이 자습서에서 사용되는 개체를 만드는 데 사용되는 서로 다른 네 개의 REST 호출이 포함되어 있습니다.

첫 번째 호출은 데이터 원본을 만듭니다. `clinical-trials-ds`. 두 번째 호출은 `clinical-trials-ss` 기술 세트를 만듭니다. 세 번째 호출은 `clinical-trials` 인덱스를 만듭니다. 마지막 네 번째 호출은 `clinical-trials-idxr` 인덱서를 만듭니다.

+ 각 요청을 차례로 열고 **보내기** 를 클릭하여 각 요청을 검색 서비스로 보냅니다. 

컬렉션의 모든 호출이 완료되면 Postman을 닫고 Azure Portal로 돌아갑니다.

## <a name="check-results-in-the-portal"></a>포털에서 결과 확인

샘플 코드는 기술 세트 실행 중에 발생한 문제의 결과로 버그가 있는 인덱스를 의도적으로 만듭니다. 문제는 데이터 누락입니다. 

1. Azure Portal의 검색 서비스 개요 페이지에서 **인덱스** 탭을 선택합니다. 
1. `clinical-trials` 인덱스를 선택합니다.
1. 쿼리 문자열: `$select=metadata_storage_path, organizations, locations&$count=true`를 입력하여 특정 문서에 대한 필드를 반환합니다(고유 `metadata_storage_path` 필드로 식별).
1. **검색** 을 클릭하여 쿼리를 실행하고 19개 문서를 모두 반환하고 "organizations" 및 "locations"에 대해 빈 값을 표시합니다.

이러한 필드는 Blob의 콘텐츠 내에서 조직과 위치를 찾는 데 사용되는 기술 세트의 [엔터티 인식 기술](cognitive-search-skill-entity-recognition-v3.md)을 통해 채워져야 합니다. 다음 연습에서는 디버그 세션을 사용하여 무엇이 잘못되었는지 확인합니다.

오류 및 경고를 조사하는 또 다른 방법은 Azure Portal을 통하는 것입니다.

1. **인덱서** 탭을 열고 `clinical-trials-idxr`을 선택합니다.
1. 인덱서 작업은 전반적으로 성공했지만 57개 경고가 발생했습니다.
1. **성공** 을 클릭하여 경고를 확인합니다(대부분 오류가 있으면 세부 정보 링크는 **실패함** 입니다). 인덱서에서 내보낸 모든 경고의 긴 목록이 표시됩니다.

   :::image type="content" source="media/cognitive-search-debug/portal-indexer-errors-warnings.png" alt-text="경고 보기":::

## <a name="start-your-debug-session"></a>디버그 세션 시작

:::image type="content" source="media/cognitive-search-debug/new-debug-session-screen-required.png" alt-text="새 디버그 세션 시작":::

1. 검색 개요 페이지에서 **디버그 세션** 탭을 클릭합니다.
1. **+ 새 디버그 세션** 을 선택합니다.
1. 세션 이름을 지정합니다. 
1. 세션을 스토리지 계정에 연결합니다. 
1. 인덱서 템플릿에서 인덱서 이름을 제공합니다. 인덱서에는 데이터 원본, 기술 세트 및 인덱스에 대한 참조가 있습니다.
1. 컬렉션의 첫 번째 문서에 대한 기본 문서 선택을 적용합니다. 
1. **세션** 을 저장합니다. 세션을 저장하면 기술 세트에서 정의한 AI 보강 파이프라인이 시작됩니다.

> [!Important]
> 디버그 세션은 단일 문서에서만 작동합니다. 디버그할 문서를 선택하거나 첫 번째 문서를 사용할 수 있습니다.

<!-- > :::image type="content" source="media/cognitive-search-debug/debug-execution-complete1.png" alt-text="New debug session started"::: -->

디버그 세션이 초기화를 마치면 세션은 기본적으로 **AI 보강** 탭으로 설정되고 **기술 그래프** 가 강조 표시됩니다. 기술 그래프에는 기술 세트의 시각적 계층 구조와 순차적 및 병렬 실행 순서가 제공됩니다.

## <a name="find-issues-with-the-skillset"></a>기술 세트 관련 문제 찾기

인덱서가 보고한 문제는 인접한 **오류/경고** 탭에서 찾을 수 있습니다. 

**오류/경고** 탭은 이전에 표시된 목록보다 훨씬 작은 목록을 제공합니다. 이 목록은 단일 문서에 대한 오류만 자세히 설명하기 때문입니다. 인덱서에서 표시하는 목록과 마찬가지로 경고 메시지를 클릭하면 이 경고의 세부 정보를 볼 수 있습니다.

**오류/경고** 를 선택하여 알림을 검토합니다. 세 가지가 표시됩니다.

   + "출력 필드 'locations'를 검색 인덱스에 매핑할 수 없습니다. 인덱서의 'outputFieldMappings' 속성을 확인하십시오.
'/document/merged_content/locations' 값이 없습니다."

   + "출력 필드 'organizations'를 검색 인덱스에 매핑할 수 없습니다. 인덱서의 'outputFieldMappings' 속성을 확인하십시오.
'/document/merged_content/organizations' 값이 없습니다."

   + "기술이 실행되었지만 하나 이상의 기술 입력이 잘못되었기 때문에 예기치 못한 결과가 발생할 수 있습니다.
선택적 기술 입력이 누락되었습니다. 이름: 'languageCode', 원본: '/document/languageCode'. 식 언어 구문 분석 문제: '/document/languageCode' 값이 누락되었습니다."

   많은 기술에 'languageCode' 매개 변수가 있습니다. 작업을 검사하면 언어 코드 입력이 `Enrichment.NerSkillV2.#1`('locations' 및 'organizations' 출력에 문제가 있는 동일한 엔터티 인식 기술임)에서 누락된 것을 알 수 있습니다. 

세 가지 알림이 모두 이 기술에 대한 것이므로 다음 단계에서는 이 기술을 디버그합니다. 가능하면 outputFieldMapping 문제로 이동하기 전에 먼저 입력 문제를 해결하는 것부터 시작하세요.

 :::image type="content" source="media/cognitive-search-debug/debug-session-errors-warnings.png" alt-text="시작된 새 디버그 세션":::

<!-- + The Skill Graph provides a visual hierarchy of the skillset and its order of execution from top to bottom. Skills that are side by side in the graph are executed in parallel. Color coding of skills in the graph indicate the types of skills that are being executed in the skillset. In the example, the green skills are text and the red skill is vision. Clicking on an individual skill in the graph will display the details of that instance of the skill in the right pane of the session window. The skill settings, a JSON editor, execution details, and errors/warnings are all available for review and editing.

+ The Enriched Data Structure details the nodes in the enrichment tree generated by the skills from the source document's contents. -->

## <a name="fix-missing-skill-input-value"></a>누락된 기술 입력 값 수정

**오류/경고** 탭에는 `Enrichment.NerSkillV2.#1`이라는 레이블이 지정된 작업에 대한 오류가 있습니다. 이 오류의 세부 정보는 기술 입력 값('/document/languageCode')에 문제가 있음을 설명합니다. 

1. **AI 보강** 탭으로 돌아갑니다.
1. **기술 그래프** 를 클릭합니다.
1. **#1** 이라는 레이블이 지정된 기술을 클릭하여 오른쪽 창에 세부 정보를 표시합니다.
1. "languageCode"에 대한 입력을 찾습니다.
1. 줄의 시작 부분에서 **</>** 기호를 선택하고, 식 계산기를 엽니다.
1. **평가** 단추를 클릭하여 이 식으로 인해 오류가 발생하는지 확인합니다. "languageCode" 속성이 유효한 입력이 아닌지 확인합니다.

   :::image type="content" source="media/cognitive-search-debug/expression-evaluator-language.png" alt-text="식 계산기":::

세션에서 이 오류를 조사하는 두 가지 방법이 있습니다. 첫 번째는 입력의 출처를 살펴보는 것입니다. 즉, 계층 구조의 어떤 기술에서 이러한 결과를 생성할까요? 기술 세부 정보 창의 [실행] 탭에는 입력 원본이 표시됩니다. 원본이 없는 경우 필드 매핑 오류가 표시됩니다.

1. **실행** 탭을 클릭합니다.
1. INPUTS를 살펴보고 "languageCode"를 찾습니다. 나열된 입력에 대한 원본이 없습니다. 
1. 왼쪽 창을 전환하여 보강된 데이터 구조를 표시합니다. "languageCode"에 해당하는 매핑된 경로가 없습니다.

   :::image type="content" source="media/cognitive-search-debug/enriched-data-structure-language.png" alt-text="보강된 데이터 구조":::

"language"에 대해 매핑된 경로가 있습니다. 따라서 기술 설정에 오타가 있습니다. 이 문제를 해결하려면 '/document/language' 식이 있는 #1 기술의 식을 업데이트해야 합니다.

1. "language" 경로에 대한 **</>** 식 계산기를 엽니다.
1. 식을 복사합니다. 창을 닫습니다.
1. #1 기술에 대한 기술 설정으로 이동하여 "languageCode" 입력에 대한 **</>** 식 계산기를 엽니다.
1. 새 '/document/language' 값을 식 상자에 붙여넣고 **평가** 를 클릭합니다.
1. 올바른 입력인 "en"이 표시됩니다. [적용]을 클릭하여 식을 업데이트합니다.
1. 오른쪽의 기술 세부 정보 창에서 **저장** 을 클릭합니다.
1. 세션의 창 메뉴에서 **실행** 을 클릭합니다. 그러면 문서를 사용하여 기술 세트의 다른 실행이 시작됩니다. 

디버그 세션 실행이 완료된 후 [오류/경고] 탭을 클릭하면 "Enrichment.NerSkillV2.#1"이라는 레이블이 지정된 오류가 사라졌음을 보여 줍니다. 그러나 서비스에서 organizations 및 locations의 출력 필드를 검색 인덱스에 매핑할 수 없다는 두 가지 경고가 여전히 있습니다. 누락된 값은 '/document/merged_content/organizations' 및 '/document/merged_content/locations'입니다.

## <a name="fix-missing-skill-output-values"></a>누락된 기술 출력 값 수정

:::image type="content" source="media/cognitive-search-debug/warnings-missing-value-locations-organizations.png" alt-text="오류 및 경고":::

기술에서 누락된 출력 값이 있습니다. 오류가 있는 기술을 식별하려면 보강된 데이터 구조로 이동하여 값 이름을 찾은 다음, 원래 원본을 살펴봅니다. 누락된 organizations 및 locations 값의 경우 #1 기술의 출력입니다. 각 경로에 대한 </> 식 계산기를 열면 각각 '/document/content/organizations' 및 '/document/content/locations'로 나열된 식이 표시됩니다.

:::image type="content" source="media/cognitive-search-debug/expression-eval-missing-value-locations-organizations.png" alt-text="식 계산기 organizations 엔터티":::

이러한 엔터티의 출력은 비어 있으며 비워 둘 수 없습니다. 이 결과를 생성하는 입력은 무엇일까요?

1. **기술 그래프** 로 이동하여 #1 기술을 선택합니다.
1. 오른쪽의 기술 세부 정보 창에서 **실행** 탭을 선택합니다.
1. INPUT "text"에 대한 **</>** 식 계산기를 엽니다.

   :::image type="content" source="media/cognitive-search-debug/input-skill-missing-value-locations-organizations.png" alt-text="텍스트 기술에 대한 입력":::

이 입력에 대해 표시된 결과는 텍스트 입력처럼 보이지 않습니다. 마치 새 줄로 둘러싸인 이미지처럼 보입니다. 텍스트가 없으면 엔터티를 식별할 수 없음을 의미합니다. 기술 세트의 계층 구조를 살펴보면 콘텐츠는 먼저 #6(OCR) 기술에서 처리된 다음, #5(병합) 기술에 전달됩니다. 

1. **기술 그래프** 에서 #5(병합) 기술을 선택합니다.
1. 오른쪽의 기술 세부 정보 창에서 **실행** 탭을 선택하고, OUTPUTS "mergedText"에 대한 **</>** 식 계산기를 엽니다.

   :::image type="content" source="media/cognitive-search-debug/merge-output-detail-missing-value-locations-organizations.png" alt-text="병합 기술에 대한 출력":::

여기서 텍스트는 이미지와 쌍을 이룹니다. '/document/merged_content' 식을 살펴보면 #1 기술에 대한 "organizations" 및 "locations" 경로의 오류가 표시됩니다. '/document/content'를 사용하는 대신 '/document/merged_content'를 "text" 입력에 사용해야 합니다.

1. "mergedText" 출력에 대한 식을 복사하고, 식 계산기 창을 닫습니다.
1. **기술 그래프** 에서 #1 기술을 선택합니다.
1. 오른쪽의 기술 세부 정보 창에서 **기술 설정** 탭을 선택합니다.
1. "text" 입력에 대한 **</>** 식 계산기를 엽니다.
1. 새 식을 상자에 붙여넣습니다. **평가** 를 클릭합니다.
1. 추가된 텍스트가 있는 올바른 입력이 표시됩니다. **적용** 을 클릭하여 기술 설정을 업데이트합니다.
1. 오른쪽의 기술 세부 정보 창에서 **저장** 을 클릭합니다.
1. 세션 창 메뉴에서 **실행** 을 클릭합니다. 그러면 문서를 사용하여 기술 세트의 다른 실행이 시작됩니다.

인덱서 실행이 완료된 후에도 오류가 계속 발생합니다. #1 기술로 돌아가서 조사합니다. 기술에 대한 입력은 'content'에서 'merged_content'로 수정되었습니다. 기술에서 이러한 엔터티에 대한 출력은 무엇일까요?

1. **AI 보강** 탭을 선택합니다.
1. **기술 그래프** 를 선택하고 #1 기술을 클릭합니다.
1. **기술 설정** 를 탐색하여 "outputs"를 찾습니다.
1. "organizations" 엔터티에 대한 **</>** 식 계산기를 엽니다.

   :::image type="content" source="media/cognitive-search-debug/skill-output-detail-missing-value-locations-organizations.png" alt-text="organizations 엔터티에 대한 출력":::

식 결과를 평가하면 올바른 결과가 제공됩니다. 기술은 "organizations" 엔터티에 대한 올바른 값을 식별하기 위해 노력하고 있습니다. 그러나 엔터티 경로의 출력 매핑은 여전히 오류를 throw하고 있습니다. 기술의 출력 경로를 오류의 출력 경로와 비교할 때 기술은 /document/content 노드 아래의 outputs, organizations 및 locations의 부모가 됩니다. 한편 출력 필드 매핑에서는 결과가 /document/merged_content 노드 아래의 부모가 될 것이라고 예상합니다. 이전 단계에서 입력이 '/document/content'에서 '/document/merged_content'로 변경되었습니다. 올바른 컨텍스트를 사용하여 출력이 생성되도록 하려면 기술 설정의 컨텍스트를 변경해야 합니다.

1. **AI 보강** 탭을 선택합니다.
1. **기술 그래프** 를 선택하고 #1 기술을 클릭합니다.
1. **기술 설정** 을 탐색하여 "context"를 찾습니다.
1. "context"에 대한 설정을 두 번 클릭하고, '/document/merged_content'로 읽을 수 있도록 편집합니다.
1. 오른쪽의 기술 세부 정보 창에서 **저장** 을 클릭합니다.
1. 세션 창 메뉴에서 **실행** 을 클릭합니다. 그러면 문서를 사용하여 기술 세트의 다른 실행이 시작됩니다.

   :::image type="content" source="media/cognitive-search-debug/skill-setting-context-correction-missing-value-locations-organizations.png" alt-text="기술 설정의 컨텍스트 수정":::

모든 오류가 해결되었습니다.

## <a name="commit-changes-to-the-skillset"></a>기술 세트에 대한 변경 내용 적용

디버그 세션이 시작되었을 때 검색 서비스에서 기술 세트의 복사본을 만들었습니다. 이 작업은 검색 서비스에서 원래 기술 세트를 보호하기 위해 수행되었습니다. 기술 세트 디버깅을 완료했으면 수정 사항을 커밋(원래 기술 세트 덮어쓰기)할 수 있습니다. 

또는 변경 내용을 커밋할 준비가 되지 않은 경우 디버그 세션을 저장하고 나중에 다시 열 수 있습니다.

1. 주 디버그 세션 메뉴에서 **변경 내용 적용** 을 클릭합니다.
1. **확인** 을 클릭하여 기술 세트를 업데이트할 것인지 확인합니다.
1. 디버그 세션을 닫고 **인덱서** 탭을 선택합니다.
1. 'clinical-trials-idxr'을 엽니다.
1. **재설정** 을 클릭합니다.
1. **실행** 을 클릭합니다. **확인** 을 클릭하여 확인합니다.

인덱서 실행이 완료되었으면 **실행 기록** 탭의 최신 실행 타임스탬프 옆에 녹색 확인 표시와 '성공'이라는 단어가 있어야 합니다. 변경 내용이 적용되었는지 확인하려면 다음을 수행합니다.

1. 검색 개요 페이지에서 **인덱스** 탭을 선택합니다.
1. 'clinical-trials' 인덱스를 열고 검색 탐색기 탭에서 특정 문서(고유 `metadata_storage_path` 필드로 식별됨)에 대한 필드를 반환하는 쿼리 문자열인 `$select=metadata_storage_path, organizations, locations&$count=true`를 입력합니다.
1. **검색** 을 클릭합니다.

결과에 organizations 및 locations가 예상 값으로 채워진 것이 보입니다.

## <a name="clean-up-resources"></a>리소스 정리

본인 소유의 구독으로 이 모듈을 진행하고 있는 경우에는 프로젝트가 끝날 때 여기서 만든 리소스가 계속 필요한지 확인하는 것이 좋습니다. 계속 실행되는 리소스에는 요금이 부과될 수 있습니다. 리소스를 개별적으로 삭제하거나 리소스 그룹을 삭제하여 전체 리소스 세트를 삭제할 수 있습니다.

왼쪽 탐색 창의 **모든 리소스** 또는 **리소스 그룹** 링크를 사용하여 포털에서 리소스를 찾고 관리할 수 있습니다.

무료 서비스를 사용하는 경우 인덱스, 인덱서, 데이터 원본 세 개로 제한됩니다. 포털에서 개별 항목을 삭제하여 제한 이하로 유지할 수 있습니다. 

## <a name="next-steps"></a>다음 단계

이 자습서에서는 기술 세트 정의 및 처리의 다양한 측면을 알아보았습니다. 개념과 워크플로에 대해 자세히 알아보려면 다음 문서를 참조하세요.

+ [기술 세트 출력 필드를 검색 인덱스의 필드에 매핑하는 방법](cognitive-search-output-field-mapping.md)

+ [Azure Cognitive Search의 기술 세트](cognitive-search-working-with-skillsets.md)

+ [증분 보강을 위해 캐싱을 구성하는 방법](cognitive-search-incremental-indexing-conceptual.md)