---
title: 인덱서 오류 및 경고
titleSuffix: Azure Cognitive Search
description: 이 문서에서는 Azure Cognitive Search에서 AI 보강 중에 발생할 수 있는 일반적인 오류 및 경고에 대한 정보와 솔루션을 제공합니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/16/2021
ms.openlocfilehash: abb67c59c32fe1eb41f9fe4c31a03d67fcfafa85
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128622515"
---
# <a name="troubleshooting-common-indexer-errors-and-warnings-in-azure-cognitive-search"></a>Azure Cognitive Search의 일반적인 인덱서 오류 및 경고 문제 해결

이 문서에서는 Azure Cognitive Search에서 인덱싱 및 AI 보강 중에 발생할 수 있는 일반적인 오류 및 경고에 대한 정보와 솔루션을 제공합니다.

오류 수가 ['maxFailedItems'](cognitive-search-concept-troubleshooting.md#tip-3-see-what-works-even-if-there-are-some-failures)를 초과하면 인덱싱이 중지됩니다. 

인덱서가 이러한 오류를 무시하고 "실패한 문서"를 건너뛰도록 하려면 [여기](/rest/api/searchservice/create-indexer#general-parameters-for-all-indexers)에 설명된 대로 `maxFailedItems` 및 `maxFailedItemsPerBatch`를 업데이트하는 것이 좋습니다.

> [!NOTE]
> 실패한 각 문서는 문서 키(사용 가능한 경우)와 함께 인덱서 실행 상태에서 오류로 표시됩니다. 오류를 허용하도록 인덱서를 설정한 경우 [인덱스 api](/rest/api/searchservice/addupdate-or-delete-documents)를 활용하여 나중에 수동으로 문서를 업로드할 수 있습니다.

이 문서의 오류 정보는 오류를 해결하여 인덱싱을 계속하는 데 도움이 될 수 있습니다.

경고는 인덱싱을 중지하지 않지만 예기치 않은 결과가 발생할 수 있는 조건을 표시합니다. 조치 수행 여부는 데이터와 시나리오에 따라 달라집니다.

API 버전 `2019-05-06`부터 항목 수준 인덱서 오류 및 경고는 원인 및 다음 단계를 보다 명확하게 이해할 수 있도록 구조화되어 있습니다. 다음과 같은 속성을 포함하고 있습니다.

| 속성 | Description | 예제 |
| --- | --- | --- |
| key | 오류 또는 경고의 영향을 받는 문서의 문서 ID입니다. | https:\//coromsearch.blob.core.windows.net/jfk-1k/docid-32112954.pdf |
| name | 오류 또는 경고가 발생한 위치를 설명하는 작업 이름입니다. 일반적으로 [category].[subcategory].[resourceType].[resourceName] 구조로 생성됩니다. | DocumentExtraction.azureblob.myBlobContainerName Enrichment.WebApiSkill.mySkillName Projection.SearchIndex.OutputFieldMapping.myOutputFieldName Projection.SearchIndex.MergeOrUpload.myIndexName Projection.KnowledgeStore.Table.myTableName |
| message | 오류 또는 경고에 대한 개략적인 설명입니다. | 웹 Api 요청이 실패했으므로 기술을 실행할 수 없습니다. |
| 자세히 | 사용자 지정 기술을 실행하지 못한 경우 WebApi 응답과 같은 문제를 진단하는 데 도움이 될 수 있는 추가 세부 정보입니다. | `link-cryptonyms-list - Error processing the request record : System.ArgumentNullException: Value cannot be null. Parameter name: source at System.Linq.Enumerable.All[TSource](IEnumerable`1개 원본, 함수`2 predicate) at Microsoft.CognitiveSearch.WebApiSkills.JfkWebApiSkills.` ...스택 추적의 나머지... |
| documentationLink | 문제를 디버그하고 해결하기 위한 자세한 정보가 포함된 관련 설명서의 링크입니다. 이 링크는 종종 이 페이지에서 아래 섹션 중 하나를 가리킵니다. | https://go.microsoft.com/fwlink/?linkid=2106475 |

<a name="could-not-read-document"></a>

## <a name="error-could-not-read-document"></a>오류: 문서를 읽을 수 없음

인덱서가 데이터 원본의 문서를 읽을 수 없습니다. 이 문제는 다음과 같은 경우에 발생할 수 있습니다.

| 이유 | 세부 정보/예제 | 해결 방법 |
| --- | --- | --- |
| 여러 문서에서 일치하지 않는 필드 형식 | "값의 유형이 열 유형과 일치하지 않습니다. `'{47.6,-122.1}'`을 작성자 열에 저장할 수 없습니다.  필요한 형식은 JArray입니다."  "데이터 형식 nvarchar를 float로 변환하는 동안 오류가 발생했습니다."  "nvarchar 값 '12개월'을 데이터 형식 int로 변환하지 못했습니다."  "식을(를) 데이터 형식 int(으)로 변환하는 중 산술 오버플로 오류가 발생했습니다." | 각 필드의 형식이 여러 문서에서 동일한지 확인합니다. 예를 들어 첫 번째 문서 `'startTime'` 필드가 날짜/시간이고 두 번째 문서에서는 문자열인 경우 이 오류가 발생합니다. |
| 데이터 원본의 기본 서비스에서 발생하는 오류 | (Cosmos DB) `{"Errors":["Request rate is large"]}` | 스토리지 인스턴스를 확인하여 정상 상태인지 확인합니다. 스케일링/분할을 조정해야 할 수도 있습니다. |
| 일시적인 문제 | 서버에서 결과를 받을 때 전송 수준 오류가 발생했습니다. (공급자: TCP 공급자, 오류: 0 - 현재 연결이 원격 호스트에 의해 강제로 끊겼습니다.) | 예기치 않은 연결 문제가 가끔 발생합니다. 나중에 인덱서를 통해 문서를 다시 실행해 보세요. |

<a name="could-not-extract-document-content"></a>

## <a name="error-could-not-extract-content-or-metadata-from-your-document"></a>오류: 문서에서 콘텐츠 또는 메타데이터를 추출할 수 없습니다.
Blob 데이터 원본이 있는 인덱서가 문서(예: PDF 파일)에서 콘텐츠 또는 메타데이터를 추출할 수 없습니다. 이 문제는 다음과 같은 경우에 발생할 수 있습니다.

| 이유 | 세부 정보/예제 | 해결 방법 |
| --- | --- | --- |
| Blob이 크기 제한을 초과합니다. | 문서 크기는 `'150441598'`바이트인데, 이것은 현재 서비스 계층의 최대 문서 추출 크기인 `'134217728'`바이트를 초과합니다. | [Blob 인덱싱 오류](search-howto-indexing-azure-blob-storage.md#DealingWithErrors) |
| Blob에 지원되지 않는 콘텐츠 형식이 있습니다. | 문서에 지원되지 않는 콘텐츠 형식 `'image/png'`가 있습니다. | [Blob 인덱싱 오류](search-howto-indexing-azure-blob-storage.md#DealingWithErrors) |
| 파일이 암호화되었습니다. | 문서를 처리할 수 없습니다. 문서가 암호화되었거나 암호로 보호된 것일 수 있습니다. | [Blob 설정](search-howto-indexing-azure-blob-storage.md#PartsOfBlobToIndex)을 통해 Blob을 건너뛸 수 있습니다. |
| 일시적인 문제 | "Blob을 처리하는 동안 오류가 발생했습니다. 요청이 중단되었습니다. 요청이 취소되었습니다." "처리하는 동안 문서가 시간 초과되었습니다." | 예기치 않은 연결 문제가 가끔 발생합니다. 나중에 인덱서를 통해 문서를 다시 실행해 보세요. |

<a name="could-not-parse-document"></a>

## <a name="error-could-not-parse-document"></a>오류: 문서를 구문 분석할 수 없습니다.
인덱서가 데이터 원본에서 문서를 읽었지만, 문서 콘텐츠를 지정된 필드 매핑 스키마로 변환하는 동안 문제가 발생 했습니다. 이 문제는 다음과 같은 경우에 발생할 수 있습니다.

| 이유 | 세부 정보/예제 | 해결 방법 |
| --- | --- | --- |
| 문서 키가 없습니다. | 문서 키가 없거나 비어 있으면 안 됩니다. | 모든 문서에 유효한 문서 키가 있는지 확인합니다. 문서 키는 [인덱스 정의](/rest/api/searchservice/create-index#request-body)에서 'key' 속성을 설정할 때 결정됩니다. 특정 문서에서 '키'로 플래그가 지정된 속성을 찾을 수 없으면 인덱서가 이 오류를 내보냅니다. |
| 문서 키가 잘못되었습니다. | 문서 키는 1024자를 초과할 수 없습니다. | 유효성 검사 요구 사항에 맞게 문서 키를 수정합니다. |
| 필드에 필드 매핑을 적용할 수 없습니다. | 매핑 함수 `'functionName'`을 `'fieldName'` 필드에 적용할 수 없습니다. 배열은 null일 수 없습니다. 매개 변수 이름: 바이트 | 인덱서에 정의된 [필드 매핑](search-indexer-field-mappings.md)을 두 번 확인하고, 실패한 문서의 지정된 필드 데이터와 비교합니다. 필드 매핑 또는 문서 데이터를 수정해야 할 수도 있습니다. |
| 필드 값을 읽을 수 없습니다. | `'fieldIndex'` 인덱스에서 `'fieldName'` 열 값을 읽을 수 없습니다. 서버에서 결과를 받을 때 전송 수준 오류가 발생했습니다. (공급자: TCP 공급자, 오류: 0 - 현재 현결이 원격 호스트에 의해 강제로 끊겼습니다.) | 이러한 오류는 일반적으로 데이터 원본의 기본 서비스와 예기치 않은 연결 문제로 인해 발생합니다. 나중에 인덱서를 통해 문서를 다시 실행해 보세요. |

<a name="Could not map output field '`xyz`' to search index due to deserialization problem while applying mapping function '`abc`'"></a>

## <a name="error-could-not-map-output-field-xyz-to-search-index-due-to-deserialization-problem-while-applying-mapping-function-abc"></a>오류: '`xyz`' 매핑 함수를 적용하는 동안 deserialization 문제로 인해 '`abc`' 출력 필드를 검색 인덱스에 매핑할 수 없습니다.
출력 데이터가 현재 사용 중인 매핑 함수에 맞지 않는 형식이어서 출력 매핑이 실패한 것일 수 있습니다. 예를 들어 이진 데이터에 Base64Encode 매핑 함수를 적용하면 이 오류가 생성됩니다. 이 문제를 해결하려면 매핑 함수를 지정하지 않고 인덱서를 다시 실행하거나 매핑 함수가 출력 필드 데이터 형식과 호환되는지 확인합니다. 자세한 내용은 [출력 필드 매핑](cognitive-search-output-field-mapping.md)을 참조하세요.

<a name="could-not-execute-skill"></a>

## <a name="error-could-not-execute-skill"></a>오류: 기술을 실행할 수 없습니다.
인덱서가 기술 세트의 기술을 실행할 수 없습니다.

| 이유 | 세부 정보/예제 | 해결 방법 |
| --- | --- | --- |
| 임시 연결 문제 | 일시적인 오류가 발생했습니다. 나중에 다시 시도하세요. | 예기치 않은 연결 문제가 가끔 발생합니다. 나중에 인덱서를 통해 문서를 다시 실행해 보세요. |
| 제품 버그 가능성 | 예기치 않은 오류가 발생했습니다. | 알 수 없는 오류 클래스를 나타내며 제품 버그일 가능성이 있습니다. 도움을 받으려면 [지원 티켓](https://ms.portal.azure.com/#create/Microsoft.Support)을 제출하세요. |
| 실행 중에 기술에서 오류가 발생했습니다. | (병합 기술) 하나 이상의 오프셋 값이 잘못되었으며 구문 분석할 수 없습니다. 항목이 텍스트 끝에 삽입되었습니다. | 오류 메시지의 정보를 사용하여 문제를 해결합니다. 이러한 종류의 오류를 해결하려면 조치를 취해야 합니다. |

<a name="could-not-execute-skill-because-the-web-api-request-failed"></a>

## <a name="error-could-not-execute-skill-because-the-web-api-request-failed"></a>오류: 웹 API 요청이 실패하여 기술을 실행할 수 없습니다.
웹 API 호출이 실패하여 기술 실행이 실패했습니다. 일반적으로 이 오류 클래스는 사용자 지정 기술을 사용할 때 발생합니다. 이 경우 사용자 지정 코드를 디버그하여 문제를 해결해야 합니다. 기본 제공 기술에서 오류가 발생한 경우 문제 해결에 도움이 되는 오류 메시지를 참조하세요.

이 문제를 디버그할 때 이 기술에 대한 [기술 입력 경고](#warning-skill-input-was-invalid)에 주의해야 합니다. 인덱서가 예기치 않은 입력을 전달하고 있어서 웹 API 엔드포인트가 실패하는 것일 수 있습니다.

<a name="could-not-execute-skill-because-web-api-skill-response-is-invalid"></a>

## <a name="error-could-not-execute-skill-because-web-api-skill-response-is-invalid"></a>오류: Web API 기술 응답이 잘못되어 기술을 실행할 수 없습니다.
웹 API 호출에서 잘못된 응답을 반환하여 기술 실행에 실패했습니다. 일반적으로 이 오류 클래스는 사용자 지정 기술을 사용할 때 발생합니다. 이 경우 사용자 지정 코드를 디버그하여 문제를 해결해야 합니다. 기본 제공 기술에서 오류가 발생한 경우 [지원 티켓](https://ms.portal.azure.com/#create/Microsoft.Support)을 제출하여 지원을 받으세요.

<a name="skill-did-not-execute-within-the-time-limit"></a>

## <a name="error-skill-did-not-execute-within-the-time-limit"></a>오류: 기술이 시간 제한 내에 실행되지 않았습니다.
이 오류 메시지가 표시될 수 있는 두 가지 상황이 있으며 각각 다른 방법으로 처리해야 합니다. 이 오류를 반환한 기술에 따라 아래 지침을 따르세요.

### <a name="built-in-cognitive-service-skills"></a>기본 제공 Cognitive Service 기술
언어 감지, 엔터티 인식, OCR 등의 여러 기본 제공 인식 기술은 Cognitive Service API 엔드포인트를 통해 지원됩니다. 이러한 엔드포인트에 일시적인 문제가 있어 요청 시간이 초과되는 경우가 있습니다. 일시적인 문제인 경우 잠시 기다렸다가 다시 시도하는 방법 외에는 다른 해결 방법이 없습니다. 완화 조치로 [일정에 따라 실행](search-howto-schedule-indexers.md)되도록 인덱서를 설정하는 방법이 있습니다. 예약된 인덱싱은 이전에 중단되었던 위치를 선택합니다. 일시적인 문제가 해결되면 인덱싱 및 인식 기술 처리를 다음 예약 실행에서 계속 진행할 수 있습니다.

기본 제공 인식 기술에 대한 동일한 문서에서 이 오류가 계속 발생하는 경우 [지원 티켓](https://ms.portal.azure.com/#create/Microsoft.Support)을 제출하여 지원을 받으세요.

### <a name="custom-skills"></a>사용자 지정 기술
직접 만든 사용자 지정 기술에 시간 초과 오류가 발생하는 경우 몇 가지 해결 방법을 시도해 볼 수 있습니다. 먼저 사용자 지정 기술을 검토하여 무한 루프에서 중단되지 않으며 결과를 일관적으로 반환하는지 확인합니다. 이를 확인한 후에는 기술 실행 시간을 결정합니다. 사용자 지정 기술 정의에서 `timeout` 값을 명시적으로 설정하지 않았다면 기본 `timeout`은 30초입니다. 30초가 기술을 실행하기에 부족한 경우 사용자 지정 기술 정의에서 `timeout` 값을 더 높게 지정할 수 있습니다. 다음은 시간 제한이 90초로 설정된 사용자 지정 기술 정의의 예입니다.

```json
  {
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "uri": "<your custom skill uri>",
        "batchSize": 1,
        "timeout": "PT90S",
        "context": "/document",
        "inputs": [
          {
            "name": "input",
            "source": "/document/content"
          }
        ],
        "outputs": [
          {
            "name": "output",
            "targetName": "output"
          }
        ]
      }
```

`timeout` 매개 변수에 대해 설정할 수 있는 최댓값은 230초입니다.  사용자 지정 기술을 일관적으로 230초 내에 실행할 수 없는 경우 사용자 지정 기술의 `batchSize`를 낮추면 도움이 될 수 있습니다. 이렇게 하면 단일 실행 내에서 처리할 문서 수가 감소합니다.  이미 `batchSize`를 1로 설정한 경우에는 230초 이내에 실행할 수 있도록 기술을 다시 작성하거나, 단일 사용자 지정 기술의 실행 시간이 230초를 넘지 않도록 여러 사용자 지정 기술로 분할해야 합니다. 자세한 내용은 [사용자 지정 기술 설명서](cognitive-search-custom-skill-web-api.md)를 참조하세요.

<a name="could-not-mergeorupload--delete-document-to-the-search-index"></a>

## <a name="error-could-not-mergeorupload--delete-document-to-the-search-index"></a>오류: 문서를 검색 인덱스에 '`MergeOrUpload`' | '`Delete`'할 수 없습니다.

문서를 읽고 처리했지만 인덱서가 문서를 검색 인덱스에 추가할 수 없습니다. 이 문제는 다음과 같은 경우에 발생할 수 있습니다.

| 이유 | 세부 정보/예제 | 해결 방법 |
| --- | --- | --- |
| 필드에 너무 큰 기간이 있습니다. | 문서의 기간이 [32KB 제한](search-limits-quotas-capacity.md#api-request-limits)을 초과합니다. | 필드가 필터링 가능, 패싯 가능 또는 정렬 가능으로 구성되지 않도록 하여 이 제한을 피할 수 있습니다.
| 문서가 너무 커서 인덱싱할 수 있습니다. | 문서가 [최대 api 요청 크기](search-limits-quotas-capacity.md#api-request-limits)를 초과합니다. | [큰 데이터 세트를 인덱싱하는 방법](search-howto-large-index.md)
| 문서의 컬렉션에 너무 많은 개체가 포함되어 있습니다. | 문서의 컬렉션이 [모든 복합 컬렉션의 최대 요소 제한](search-limits-quotas-capacity.md#index-limits)을 초과합니다. "키가 `'1000052'`인 문서의 컬렉션(JSON 배열)에 `'4303'`개의 개체가 있습니다. 전체 문서에서 컬렉션에 허용되는 최대 개체 수는 `'3000'`개입니다. 컬렉션에서 개체를 제거하고 문서를 다시 인덱싱하세요." | 문서의 복합 컬렉션 크기를 제한 아래로 줄여서 스토리지 사용률을 낮추는 것이 좋습니다.
| 서비스에서 쿼리, 인덱싱 등의 다른 부하를 처리하고 있으므로 대상 인덱스에 연결하는 데 문제가 있습니다(재시도 후에도 문제가 지속됨). | 인덱스 업데이트를 위한 연결을 설정하지 못했습니다. Search Service의 부하가 높습니다. | [Search Service 스케일 업](search-capacity-planning.md)
| 서비스 업데이트를 위해 Search Service가 패치 중이거나 토폴로지를 다시 구성하는 중입니다. | 인덱스 업데이트를 위한 연결을 설정하지 못했습니다. Search Service가 현재 다운되었습니다/검색 서비스를 전환 중입니다. | [SLA 설명서](https://azure.microsoft.com/support/legal/sla/search/v1_0/)에 따라 99.9%의 가용성을 제공하도록 3개 이상의 복제본을 사용하여 서비스를 구성합니다.
| 기본 컴퓨팅/네트워킹 리소스에서 오류가 발생합니다(드문 경우). | 인덱스 업데이트를 위한 연결을 설정하지 못했습니다. 알 수 없는 오류가 발생했습니다. | [일정에 따라 실행](search-howto-schedule-indexers.md)하여 실패 상태에서 선택할 수 있도록 인덱서를 구성합니다.
| 네트워크 문제로 인해 대상 인덱스에 대해 수행된 인덱싱 요청이 시간 제한 기간 내에 승인되지 않았습니다. | 검색 인덱스에 대한 연결을 적시에 설정할 수 없습니다. | [일정에 따라 실행](search-howto-schedule-indexers.md)하여 실패 상태에서 선택할 수 있도록 인덱서를 구성합니다. 또한 이 오류 조건이 지속되면 인덱서 [일괄 처리 크기](/rest/api/searchservice/create-indexer#parameters)를 줄여 보세요.

<a name="could-not-index-document-because-the-indexer-data-to-index-was-invalid"></a>

## <a name="error-could-not-index-document-because-some-of-the-documents-data-was-not-valid"></a>오류: 문서의 데이터 중 일부가 잘못되어 문서를 인덱싱할 수 없습니다.

인덱서가 문서를 읽고 처리했지만, 인덱스 필드의 구성과 인덱서가 추출하고 처리한 데이터가 일치하지 않아 검색 인덱스에 추가할 수 없습니다. 이 문제는 다음과 같은 경우에 발생할 수 있습니다.

| 이유 | 세부 정보/예제
| --- | ---
| 인덱서가 추출한 필드의 데이터 형식이 해당하는 대상 인덱스 필드의 데이터 모델과 호환되지 않습니다. | 키가 '888'인 문서의 '_data_' 데이터 필드에 있는 값이 '잘못된 'Edm.String' 형식'입니다. 필요한 형식은 'Collection(Edm.String)'입니다. |
| 문자열 값에서 JSON 엔터티를 추출하지 못했습니다. | '_data_' 필드의 ''Edm.String' 형식' 값을 JSON 개체로 구문 분석할 수 없습니다. 오류: '값을 구문 분석한 후 예기치 않은 '' 문자가 경로 '_path_', 줄 1, 위치 3162에서 발견되었습니다.' |
| 문자열 값에서 JSON 엔터티 컬렉션을 추출하지 못했습니다.  | '_data_' 필드의 ''Edm.String' 형식' 값을 JSON 배열로 구문 분석할 수 없습니다. 오류: '값을 구문 분석한 후 예기치 않은 '' 문자가 경로 '[0]', 줄 1, 위치 27에서 발견되었습니다.' |
| 원본 문서에서 알 수 없는 형식이 검색되었습니다. | 알 수 없는 '_unknown_' 형식은 인덱싱할 수 없습니다. |
| 지리적 위치에 대한 호환되지 않는 표기법이 원본 문서에 사용되었습니다. | WKT POINT 문자열 리터럴은 지원되지 않습니다. GeoJson 지점 리터럴을 대신 사용하세요. |

위의 문제가 발생하면 [지원되는 데이터 형식](/rest/api/searchservice/supported-data-types) 및 [인덱서의 데이터 형식 맵](/rest/api/searchservice/data-type-map-for-indexers-in-azure-search)을 참조하여 인덱스 스키마를 올바르게 빌드하고 적절한 [인덱서 필드 매핑](search-indexer-field-mappings.md)을 설정했는지 확인합니다. 오류 메시지에는 불일치의 원인을 추적하는 데 도움이 되는 세부 정보가 포함됩니다.

## <a name="error-integrated-change-tracking-policy-cannot-be-used-because-table-has-a-composite-primary-key"></a>오류: 테이블에 복합 기본 키가 있으므로 통합 변경 내용 추적 정책을 사용할 수 없습니다.

이는 SQL 테이블에 적용되며, 일반적으로 키가 복합 키로 정의되거나 테이블에서 Azure Search 인덱스가 아닌 SQL 인덱스처럼 고유 클러스터형 인덱스를 정의한 경우에 발생합니다. 주된 이유는 [고유 클러스터형 인덱스](/sql/relational-databases/indexes/clustered-and-nonclustered-indexes-described)인 경우 키 특성이 복합 기본 키로 수정되기 때문입니다. 이 경우에는 SQL 테이블에 고유 클러스터형 인덱스가 없도록 하거나 키 필드를 중복 값이 없다고 보장할 수 있는 필드에 매핑해야 합니다.

<a name="could-not-process-document-within-indexer-max-run-time"></a>

## <a name="error-could-not-process-document-within-indexer-max-run-time"></a>오류: 인덱서 최대 실행 시간 내에 문서를 처리할 수 없습니다.

이 오류는 인덱서가 허용된 실행 시간 내에 데이터 원본의 단일 문서 처리를 완료할 수 없는 경우에 발생합니다. 기술 세트를 사용하는 경우 [최대 실행 시간](search-limits-quotas-capacity.md#indexer-limits)이 짧아집니다. 이 오류가 발생하는 경우 maxFailedItems가 0이 아닌 값으로 설정되었으면 인덱서는 인덱싱이 계속 진행될 수 있도록 이후 실행에서 문서를 우회합니다. 문서를 건너뛸 수 없는 경우 또는 이 오류가 지속적으로 발생하는 경우에는 단일 인덱서 실행 내에서 부분적으로 진행할 수 있도록 문서를 더 작은 여러 문서로 분할하는 방법이 있습니다.

<a name="could-not-project-document></a>

## <a name="error-could-not-project-document"></a>오류: 문서를 프로젝션할 수 없습니다.

이 오류는 인덱서가 [데이터를 지식 저장소에 프로젝션](knowledge-store-projection-overview.md)하려는 동안 오류가 발생했을 때 발생합니다.  이 오류가 일관적이고 수정 가능한 오류일 수도 있고, 해결하려면 기다렸다가 다시 시도해야 하는 프로젝션 출력 싱크가 있는 일시적 오류일 수도 있습니다.  다음은 알려진 오류 상태와 가능한 해결 방법입니다.

| 이유 | 세부 정보/예제 | 해결 방법 |
| --- | --- | --- |
| `'containerName'` 컨테이너의 프로젝션 Blob `'blobUri'`를 업데이트할 수 없습니다. |지정한 컨테이너가 없습니다. | 인덱서는 지정한 컨테이너가 이전에 생성되었는지 여부를 확인하고 필요한 경우 이 컨테이너를 만들지만, 인덱서 실행당 한 번만 이 검사를 수행합니다. 이 오류는 이 단계 후에 어떤 이유로 컨테이너가 삭제되었음을 의미합니다.  이 오류를 해결하려면 스토리지 계정 정보를 그대로 두고 인덱서가 완료될 때까지 기다렸다가 인덱서를 다시 실행합니다. |
| `'containerName'` 컨테이너의 프로젝션 Blob `'blobUri'`를 업데이트할 수 없습니다. |전송 연결에 데이터를 쓸 수 없습니다. 원격 호스트가 기존 연결을 강제로 닫았습니다. | Azure Storage의 일시적인 오류로 예상되며 인덱서를 다시 실행하여 해결할 수 있습니다. 이 오류가 지속적으로 발생하면 추가 조사를 진행할 수 있도록 [지원 티켓](https://ms.portal.azure.com/#create/Microsoft.Support)을 제출하세요.  |
| `'tableName'` 테이블의 `'projectionRow'` 행을 업데이트할 수 없습니다. | 서버가 사용 중입니다. | Azure Storage의 일시적인 오류로 예상되며 인덱서를 다시 실행하여 해결할 수 있습니다. 이 오류가 지속적으로 발생하면 추가 조사를 진행할 수 있도록 [지원 티켓](https://ms.portal.azure.com/#create/Microsoft.Support)을 제출하세요.  |

<a name="could-not-execute-skill-because-a-skill-input-was-invalid"></a>

## <a name="warning-skill-input-was-invalid"></a>경고: 기술 입력이 잘못되었습니다.
기술에 대한 입력이 누락되었거나, 형식이 잘못되었거나, 유효하지 않습니다. 경고 메시지는 다음과 같은 영향을 알립니다.
1) 기술을 실행할 수 없습니다.
2) 기술이 실행되었지만 예기치 않은 결과가 발생할 수 있습니다.

인식 기술에는 필수 입력과 선택적 입력이 있습니다. 예를 들어 [핵심 구 추출 기술](cognitive-search-skill-keyphrases.md)에는 두 가지 필수 입력 `text` 및 `languageCode`가 있고 선택적 입력은 없습니다. 사용자 지정 기술 입력은 모두 선택적 입력으로 간주됩니다.

필수 입력이 없거나 입력이 잘못된 형식이면 기술을 건너뛰고 경고가 생성됩니다. 건너뛴 기술은 출력을 생성하지 않으므로 다른 기술에서 건너뛴 기술의 출력을 사용하는 경우 추가 경고를 생성할 수 있습니다.

선택적 입력이 없으면 기술은 계속 실행되지만 입력이 없으므로 예기치 않은 출력이 생성될 수 있습니다.

두 경우 모두 데이터의 셰이프 때문에 경고가 발생할 수 있습니다. 예를 들어 `firstName`, `middleName` 및 `lastName` 필드를 사용하는 사람에 대한 정보가 포함된 문서가 있는 경우 `middleName`에 대한 항목이 없는 문서가 있을 수 있습니다. `middleName`을 파이프라인의 기술에 대한 입력으로 전달하면 이 기술 입력에는 일부 시간이 누락될 수 있습니다. 데이터 및 시나리오를 평가하여 이 경고의 결과로 조치가 필요한지 여부를 확인해야 합니다.

입력이 누락된 경우 기본값을 제공하려면 [조건부 기술](cognitive-search-skill-conditional.md)을 사용하여 기본값을 생성한 다음, [조건부 기술](cognitive-search-skill-conditional.md)의 출력을 기술 입력으로 사용하면 됩니다.


```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document",
    "inputs": [
        { "name": "condition", "source": "= $(/document/language) == null" },
        { "name": "whenTrue", "source": "= 'en'" },
        { "name": "whenFalse", "source": "= $(/document/language)" }
    ],
    "outputs": [ { "name": "output", "targetName": "languageWithDefault" } ]
}
```

| 이유 | 세부 정보/예제 | 해결 방법 |
| --- | --- | --- |
| 기술 입력의 형식이 잘못되었습니다. | "필요한 기술 입력이 예상한 `String` 형식과 다릅니다. 이름: `text`, 원본: `/document/merged_content`."  "필요한 기술 입력이 예상한 형식과 다릅니다. 이름: `text`, 원본: `/document/merged_content`."  "배열이 아닌 `/document/normalized_images/0/imageCelebrities/0/detail/celebrities`에 대해 반복할 수 없습니다."  "배열이 아닌 `/document/normalized_images/0/imageCelebrities/0/detail/celebrities`에서 `0`을 선택할 수 없습니다." | 특정 기술에는 특정 형식의 입력이 필요합니다. 예를 들어 [감정 기술](cognitive-search-skill-sentiment-v3.md)은 `text`가 문자열이 될 것으로 예상합니다. 입력에서 문자열이 아닌 값을 지정하면 기술이 실행되지 않고 출력을 생성하지 않습니다. 데이터 세트의 입력 값 형식이 균일한지 확인하거나 [사용자 지정 웹 API 기술](cognitive-search-custom-skill-web-api.md)을 사용하여 입력을 전처리합니다. 배열에 대해 기술을 반복하는 경우 기술 컨텍스트와 입력의 `*`가 올바른 위치에 있는지 확인합니다. 일반적으로 컨텍스트와 입력 원본 모두 배열에 대한 `*`로 끝나야 합니다. |
| 기술 입력이 없습니다. | "필수 기술 입력이 없습니다. 이름: `text`, 원본: `/document/merged_content`"  "누락된 값 `/document/normalized_images/0/imageTags`."  "길이 `0`의 `/document/pages` 배열에서 `0`을 선택할 수 없습니다." | 모든 문서에서 이 경고가 발생하는 경우 입력 경로에 오타가 있을 가능성이 높습니다. 속성 이름의 대/소문자와 경로에서 누락 또는 추가된 `*`를 확인하고, 데이터 원본의 문서에서 필요한 입력을 제공해야 합니다. |
| 기술 언어 코드 입력이 잘못되었습니다. | 기술 입력 `languageCode`에 언어 코드 `X,Y,Z`가 있으며, 그 중 하나 이상이 잘못되었습니다. | 자세한 내용은 [아래](cognitive-search-common-errors-warnings.md#skill-input-languagecode-has-the-following-language-codes-xyz-at-least-one-of-which-is-invalid)를 참조하세요. |

<a name="skill-input-languagecode-has-the-following-language-codes-xyz-at-least-one-of-which-is-invalid"></a>

## <a name="warning--skill-input-languagecode-has-the-following-language-codes-xyz-at-least-one-of-which-is-invalid"></a>경고: 기술 입력 'languageCode'에 언어 코드 'X,Y,Z'가 있으며, 그 중 하나 이상이 잘못되었습니다.
다운스트림 기술의 선택적 `languageCode` 입력에 전달된 값 중 하나 이상이 지원되지 않습니다. 이 문제는 [LanguageDetectionSkill](cognitive-search-skill-language-detection.md)의 출력을 후속 기술에 전달하고, 출력이 해당 다운스트림 기술에서 지원되는 것보다 많은 언어로 구성된 경우에 발생할 수 있습니다.

LanguageDetectionSkill에 잘못된 `countryHint` 입력이 전달되는 경우에도 이와 유사한 경고가 표시될 수 있습니다. 이 경우 데이터 원본에서 해당 입력에 사용하는 필드에 유효한 ISO 3166-1 알파-2 두 자리 국가 번호가 포함되어 있는지 확인하세요. 일부는 유효하고 일부는 잘못된 경우 다음 지침을 계속 진행하되, 사용 사례와 일치하도록 `languageCode`를 `countryHint`로 바꾸고 `defaultLanguageCode`를 `defaultCountryHint`로 바꿉니다.

데이터 세트가 전부 한 가지 언어로 작성되는 경우 해당 기술에 대한 언어가 지원된다는 가정 하에 [LanguageDetectionSkill](cognitive-search-skill-language-detection.md) 및 `languageCode` 기술 입력을 제거하고 해당 기술에 대한 `defaultLanguageCode` 기술 매개 변수를 대신 사용해야 합니다.

데이터 세트에 여러 언어가 포함되어 있다는 것을 알고 있기 때문에 [LanguageDetectionSkill](cognitive-search-skill-language-detection.md) 및 `languageCode` 입력이 필요한 경우 텍스트를 다운스트림 기술로 전달하기 전에는 지원되지 않는 언어로 텍스트를 필터링하도록 [ConditionalSkill](cognitive-search-skill-conditional.md)을 추가하는 것이 좋습니다.  이 방법을 EntityRecognitionSkill에 사용하면 아래와 같습니다.

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document",
    "inputs": [
        { "name": "condition", "source": "= $(/document/language) == 'de' || $(/document/language) == 'en' || $(/document/language) == 'es' || $(/document/language) == 'fr' || $(/document/language) == 'it'" },
        { "name": "whenTrue", "source": "/document/content" },
        { "name": "whenFalse", "source": "= null" }
    ],
    "outputs": [ { "name": "output", "targetName": "supportedByEntityRecognitionSkill" } ]
}
```

다음은 이 오류 메시지를 생성할 수 있는 각 기술에 대해 현재 지원되는 언어의 참고 자료입니다.
* [Text Analytics 지원 언어](../cognitive-services/text-analytics/language-support.md)([KeyPhraseExtractionSkill](cognitive-search-skill-keyphrases.md), [EntityRecognitionSkill](cognitive-search-skill-entity-recognition-v3.md), [EntityLinkingSkill](cognitive-search-skill-entity-linking-v3.md), [SentimentSkill](cognitive-search-skill-sentiment-v3.md) 및 [PIIDetectionSkill](cognitive-search-skill-pii-detection.md))
* [Translator 지원 언어](../cognitive-services/translator/language-support.md)([텍스트 TranslationSkill](cognitive-search-skill-text-translation.md))
* [텍스트 SplitSkill](cognitive-search-skill-textsplit.md) 지원 언어: `da, de, en, es, fi, fr, it, ko, pt`

<a name="skill-input-was-truncated"></a>

## <a name="warning-skill-input-was-truncated"></a>경고: 기술 입력이 잘렸습니다.
인식 기술은 한 번에 분석할 수 있는 텍스트 길이에 제한이 있습니다. 이러한 기술의 텍스트 입력이 이 한도를 초과하면 한도에 맞게 텍스트를 자르고 잘린 텍스트에 대한 보강이 수행됩니다. 즉, 기술이 실행되지만 일부 데이터에 대해서는 실행되지 않습니다.

아래 LanguageDetectionSkill 예제에서 문자 제한을 초과하면 `'text'` 입력 필드가 이 경고를 트리거할 수 있습니다. 기술 입력 한도는 [기술 문서](cognitive-search-predefined-skills.md)에서 찾을 수 있습니다.

```json
 {
    "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
    "inputs": [
      {
        "name": "text",
        "source": "/document/text"
      }
    ],
    "outputs": [...]
  }
```

모든 텍스트를 분석하려면 [분할 기술](cognitive-search-skill-textsplit.md)을 사용하는 것이 좋습니다.

<a name="web-api-skill-response-contains-warnings"></a>

## <a name="warning-web-api-skill-response-contains-warnings"></a>경고: 웹 API 기술 응답에 경고가 포함되어 있습니다.
인덱서가 기술 세트의 기술을 실행할 수 있었지만, 웹 API 요청의 응답을 보면 실행 중에 경고가 발생했습니다. 경고를 검토하여 데이터에 미치는 영향과 조치가 필요한지 여부를 파악합니다.

<a name="the-current-indexer-configuration-does-not-support-incremental-progress"></a>

## <a name="warning-the-current-indexer-configuration-does-not-support-incremental-progress"></a>경고: 현재 인덱서 구성은 점진적 진행을 지원하지 않습니다.

이 경고는 Cosmos DB 데이터 원본에 대해서만 발생합니다.

인덱싱하는 동안 증분 진행률은 인덱서 실행이 일시적 오류 또는 실행 시간 제한에 의해 중단되었는지를 확인합니다. 인덱서는 처음부터 전체 컬렉션을 다시 인덱스하는 대신 다음으로 실행할 위치를 선택할 수 있습니다. 대규모 컬렉션을 인덱싱할 때 특히 유용합니다.

완료되지 않은 인덱싱 작업을 다시 시작하는 기능은 문서가 `_ts` 열을 기준으로 정렬된다고 전제합니다. 인덱서는 타임스탬프를 사용하여 다음에 선택할 문서를 결정합니다. `_ts` 열이 없거나 사용자 지정 쿼리가 이 열을 기준으로 정렬되는지 여부를 인덱서가 확인할 수 없는 경우 인덱서는 맨 앞에서 시작하고 이 경고가 표시됩니다.

점진적 진행을 활성화하고 이 경고를 표시하지 않도록 `assumeOrderByHighWatermarkColumn` 구성 속성을 사용하여 이 동작을 재정의할 수 있습니다.

자세한 내용은 [점진적 진행 및 사용자 지정 쿼리](search-howto-index-cosmosdb.md#IncrementalProgress)를 참조하세요.

<a name="some-data-was-lost-during projection-row-x-in-table-y-has-string-property-z-which-was-too-long"></a>

## <a name="warning-some-data-was-lost-during-projection-row-x-in-table-y-has-string-property-z-which-was-too-long"></a>경고: 프로젝션 중 일부 데이터가 손실되었습니다. 'Y' 테이블의 'X' 행에 너무 긴 문자열 속성 'Z'가 있습니다.

[Table Storage 서비스](https://azure.microsoft.com/services/storage/tables)의 [엔터티 속성](/rest/api/storageservices/understanding-the-table-service-data-model#property-types)은 크기가 제한되어 있습니다. 문자열은 32,000자 이하여야 합니다. 문자열 속성이 32,000자보다 긴 행을 프로젝션하면 처음 32,000자만 보존됩니다. 이 문제를 해결하려면 문자열 속성이 32,000자보다 긴 행을 프로젝션하지 마세요.

<a name="truncated-extracted-text-to-x-characters"></a>

## <a name="warning-truncated-extracted-text-to-x-characters"></a>경고: 추출된 텍스트가 X자로 잘렸습니다.
인덱서는 한 문서에서 추출할 수 있는 텍스트 크기를 제한합니다. 이 제한은 가격 책정 계층에 따라 다릅니다. 체험 계층은 32,000, 기본 계층은 64,000, 표준 계층은 400만, 표준 S2 계층은 800만, 표준 S3 계층은 1,600만입니다. 잘린 텍스트는 인덱싱되지 않습니다. 이 경고를 방지하려면 많은 텍스트가 포함된 문서를 여러 개의 작은 문서로 분할합니다. 

자세한 내용은 [인덱서 한도](search-limits-quotas-capacity.md#indexer-limits)를 참조하세요.

<a name="could-not-map-output-field-x-to-search-index"></a>

## <a name="warning-could-not-map-output-field-x-to-search-index"></a>경고: 출력 필드 'X'를 검색 인덱스에 매핑할 수 없습니다.
존재하지 않는/null 데이터를 참조하는 출력 필드 매핑은 각 문서에 대한 경고를 생성하고 빈 인덱스 필드를 생성합니다. 이 문제를 해결하려면 출력 필드 매핑 원본 경로의 오타를 철저하게 검사하거나 [조건부 기술](cognitive-search-skill-conditional.md#sample-skill-definition-2-set-a-default-value-for-a-value-that-doesnt-exist)을 사용하여 기본값을 설정합니다. 자세한 내용은 [출력 필드 매핑](cognitive-search-output-field-mapping.md)을 참조하세요.

| 이유 | 세부 정보/예제 | 해결 방법 |
| --- | --- | --- |
| 비 배열에 대해 반복할 수 없습니다. | "배열이 아닌 `/document/normalized_images/0/imageCelebrities/0/detail/celebrities`에 대해 반복할 수 없습니다." | 이 오류는 출력이 배열이 아닌 경우에 발생합니다. 출력이 배열이 맞다고 생각되면 표시된 출력 원본 필드 경로에서 오류를 확인하세요. 예를 들어 원본 필드 이름에서 `*`가 누락되었거나 추가되었을 수 있습니다. 이 기술에 대한 입력이 null이어서 빈 배열이 생성될 수도 있습니다. [기술 입력이 잘못되었습니다.](cognitive-search-common-errors-warnings.md#warning-skill-input-was-invalid) 섹션에서 비슷한 세부 정보를 찾아 보세요.    |
| 비 배열에서 `0`을 선택할 수 없습니다. | "배열이 아닌 `/document/pages`에서 `0`을 선택할 수 없습니다." | 이 문제는 기술 출력이 배열을 생성하지 않고 출력 원본 필드 이름의 경로에 배열 인덱스 또는 `*`가 있는 경우에 발생할 수 있습니다. 출력 원본 필드 이름에 제공된 경로와 표시된 필드 이름의 필드 값을 다시 확인하세요. [기술 입력이 잘못되었습니다.](cognitive-search-common-errors-warnings.md#warning-skill-input-was-invalid) 섹션에서 비슷한 세부 정보를 찾아 보세요.  |

<a name="the-data-change-detection-policy-is-configured-to-use-key-column-x"></a>

## <a name="warning-the-data-change-detection-policy-is-configured-to-use-key-column-x"></a>경고: 키 열 'X'를 사용하도록 데이터 변경 내용 검색 정책이 구성되었습니다.
[데이터 변경 내용 검색 정책](/rest/api/searchservice/create-data-source#data-change-detection-policies)은 변경 내용 검색에 사용하는 열에 대한 요구 사항이 있습니다. 이러한 요구 사항 중 하나는 원본 항목이 변경될 때마다 이 열을 업데이트해야 합니다. 또 다른 요구 사항은 이 열의 새 값이 이전 값보다 커야 합니다. 키 열은 업데이트마다 변경되지 않으므로 이 요구 사항을 충족하지 않습니다. 이 문제를 해결하려면 변경 내용 검색 정책에 사용할 다른 열을 선택합니다.

<a name="document-text-appears-to-be-utf-16-encoded-but-is-missing-a-byte-order-mark"></a>

## <a name="warning-document-text-appears-to-be-utf-16-encoded-but-is-missing-a-byte-order-mark"></a>경고: 문서 텍스트가 UTF-16으로 인코딩된 것으로 나타나지만 바이트 순서 표시가 없습니다.

[인덱서 구문 분석 모드](/rest/api/searchservice/create-indexer#blob-configuration-parameters)는 텍스트를 구문 분석하려면 텍스트를 인코딩하는 방법을 알고 있어야 합니다. 텍스트를 인코딩하는 가장 일반적인 두 가지 방법은 UTF-16 및 UTF-8입니다. UTF-8은 각 문자의 길이가 1~4바이트인 가변 길이 인코딩입니다. UTF-16은 각 문자의 길이가 2바이트인 고정 길이 인코딩입니다. UTF-16에는 "big endian" 및 "little endian"의 두 가지 변형이 있습니다. 텍스트 인코딩은 텍스트 앞에 있는 일련의 바이트인 "바이트 순서 표시"에 의해 결정됩니다.

| Encoding | 바이트 순서 표시 |
| --- | --- |
| UTF-16 Big Endian | 0xFE 0xFF |
| UTF-16 Little Endian | 0xFF 0xFE |
| UTF-8 | 0xEF 0xBB 0xBF |

바이트 순서 표시가 없는 경우 텍스트는 UTF-8로 인코딩되는 것으로 간주합니다.

이 경고를 해결하려면 이 Blob의 텍스트 인코딩을 결정하고 적절한 바이트 순서 표시를 추가합니다.

<a name="cosmos-db-collection-has-a-lazy-indexing-policy"></a>

## <a name="warning-cosmos-db-collection-x-has-a-lazy-indexing-policy-some-data-may-be-lost"></a>경고: Cosmos DB 컬렉션 'X'에 지연 인덱싱 정책이 있습니다. 일부 데이터가 손실될 수 있습니다.

[지연](../cosmos-db/index-policy.md#indexing-mode) 인덱싱 정책을 사용하는 컬렉션은 일관적으로 쿼리할 수 없으므로 인덱서에서 데이터가 누락될 수 있습니다. 이 경고를 해결하려면 인덱싱 정책을 [일관적]으로 변경합니다.

## <a name="warning-the-document-contains-very-long-words-longer-than-64-characters-these-words-may-result-in-truncated-andor-unreliable-model-predictions"></a>경고: 문서에 매우 긴(64자 초과) 단어가 포함되어 있습니다. 이러한 단어 때문에 모델 예측이 잘리고/잘리거나 신뢰할 수 없게 됩니다.

이 경고는 Text Analytics 서비스에서 전달합니다.  문서에 긴 URL(핵심 구가 아니거나 감정을 움직이지 않는)이 포함된 경우처럼 이 경고를 무시하는 것이 안전할 때도 있습니다.  단어가 64자를 초과하면 모델 예측에 영향을 줄 수 있는 64자로 잘립니다.