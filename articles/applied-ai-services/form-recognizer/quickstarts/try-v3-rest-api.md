---
title: '빠른 시작: Form Recognizer REST API | 미리 보기'
titleSuffix: Azure Applied AI Services
description: Form Recognizer REST API v3.0(미리 보기)을 사용하여 양식 및 문서 처리, 데이터 추출 및 분석
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 10/07/2021
ms.author: lajanuar
ms.openlocfilehash: 6ee2aca6eb48b87a1d773d8d713b954eeb08beca
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130240436"
---
# <a name="get-started-form-recognizer-rest-api---preview"></a>시작: Form Recognizer REST API | 미리 보기

>[!NOTE]
> Form Recognizer v3.0은 현재 공개 미리 보기에 있습니다. 일부 기능은 지원되지 않거나 기능이 제한될 수 있습니다. 

| [Form Recognizer REST API](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument) | [Azure REST API 참조](/rest/api/azure/) |

Azure Cognitive Services Form Recognizer는 기계 학습을 사용하여 문서에서 양식 필드, 텍스트 및 테이블을 추출하고 분석하는 클라우드 서비스입니다. 클라이언트 라이브러리 SDK를 워크플로 및 애플리케이션에 통합하여 Form Recognizer 모델을 쉽게 호출할 수 있습니다.

### <a name="form-recognizer-models"></a>Form Recognize 모델

 REST API는 다음 모델 및 기능을 지원합니다.

* 🆕일반 문서 - 텍스트, 테이블, 구조, 키-값 쌍 및 명명된 엔터티를 분석하고 추출합니다.|
* 레이아웃 - 모델을 학습시킬 필요 없이 양식 문서의 라디오 단추 및 확인란과 같은 테이블, 선, 단어 및 선택 표시를 분석하고 추출합니다.
* 사용자 지정 - 사용자 고유의 양식 유형으로 학습된 모델을 사용하여 사용자 지정 양식에서 양식 필드 및 기타 콘텐츠를 분석하고 추출합니다.
* 청구서 - 미리 학습된 모델을 사용하여 청구서에서 공통 필드를 분석하고 추출합니다.
* 영수증 - 미리 학습된 영수증 모델을 사용하여 영수증에서 공통 필드를 분석하고 추출합니다.
* ID 문서 - 미리 학습된 ID 문서 모델을 사용하여 여권 또는 운전면허증과 같은 ID 문서에서 공통 필드를 분석하고 추출합니다.
* 명함 - 미리 학습된 명함 모델을 사용하여 명함에서 공통 필드를 분석하고 추출합니다.

이 빠른 시작에서는 다음 기능을 사용하여 양식 및 문서에서 데이터와 값을 분석하고 추출합니다.

* [**일반 문서**](#try-it-general-document-model)

* [**Layout**](#try-it-layout-model)

* [**미리 빌드된 청구서**]#try-it-prebuilt-invoice-model)

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/cognitive-services)

* [cURL](https://curl.haxx.se/windows/) 설치

* [PowerShell 버전 6.0 이상](/powershell/scripting/install/installing-powershell-core-on-windows) 또는 유사한 명령줄 애플리케이션.

* Cognitive Services 또는 Form Recognizer 리소스. Azure 구독이 있으면 Azure Portal에서 [단일 서비스](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) 또는 [다중 서비스](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne) Form Recognizer 리소스를 만들어 키와 엔드포인트를 가져옵니다. 평가판 가격 책정 계층(`F0`)을 통해 서비스를 사용해보고, 나중에 프로덕션용 유료 계층으로 업그레이드할 수 있습니다.

> [!TIP]
> 단일 엔드포인트/키에서 여러 Cognitive Services에 액세스하려는 경우 Cognitive Services 리소스를 만듭니다. Form Recognizer 리소스를 Form Recognizer 액세스 전용으로 만듭니다. [Azure Active Directory 인증](../../../active-directory/authentication/overview-authentication.md)을 사용하려는 경우 단일 서비스 리소스가 필요합니다.

* 리소스가 배포되면 **리소스로 이동** 을 클릭합니다. 애플리케이션을 Form Recognizer API에 연결하려면 만든 리소스의 키와 엔드포인트가 필요합니다. 키와 엔드포인트는 이 빠른 시작의 뒷부분에서 코드에 붙여넣습니다.

  :::image type="content" source="../media/containers/keys-and-endpoint.png" alt-text="스크린샷: Azure Portal의 키 및 엔드포인트 위치.":::

## <a name="try-it-general-document-model"></a>**사용해 보기**: 일반 문서 모델

> [!div class="checklist"]
>
> * 이 예제에서는 **URI에 있는 양식 문서 파일** 이 필요합니다. 이 빠른 시작에는 [샘플 양식 문서](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-layout.pdf)를 사용할 수 있습니다. 명령을 실행하기 전에 다음과 같이 변경합니다.

1. `{endpoint}`를 Form Recognizer 구독에서 얻은 엔드포인트로 바꿉니다.
1. `{subscription key}`를 이전 단계에서 복사한 구독 키로 바꿉니다.
1. `{your-document-url}`을 샘플 양식 문서 URL로 바꿉니다.

#### <a name="request"></a>요청

```bash
 curl -v -i POST "https://{endpoint}/formrecognizer/documentModels/prebuilt-document:analyze?api-version=2021-09-30-preview&api-version=2021-09-30-preview HTTP/1.1" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{'source': '{your-document-url}'}"
```

#### <a name="operation-location"></a>Operation-Location

**Operation-Location** 헤더를 포함하는 `202 (Success)` 응답을 받게 됩니다. 이 헤더 값에는 비동기 작업 상태를 쿼리하고 결과를 가져오는 데 사용할 수 있는 결과 ID가 포함되어 있습니다.

https://{host}/formrecognizer/documentModels/{modelId}/analyzeResults/**{resultId}**?api-version=2021-07-30-preview

### <a name="get-general-document-results"></a>일반 문서 결과 가져오기

**[문서 분석](https://westus.api.cognitive.microsoft.com/formrecognizer/documentModels/prebuilt-document:analyze?api-version=2021-09-30-preview&stringIndexType=textElements)** API를 호출한 후 **[분석 결과 가져오기](https://westus.api.cognitive.microsoft.com/formrecognizer/documentModels/prebuilt-document/analyzeResults/{resultId}?api-version=2021-09-30-preview)** API를 호출하여 작업 상태와 추출된 데이터를 가져옵니다. 명령을 실행하기 전에 다음과 같이 변경합니다.

1. `{endpoint}`를 Form Recognizer 구독에서 얻은 엔드포인트로 바꿉니다.
1. `{subscription key}`를 이전 단계에서 복사한 구독 키로 바꿉니다.
1. `{resultId}`를 이전 단계의 결과 ID로 바꿉니다.
<!-- markdownlint-disable MD024 -->

#### <a name="request"></a>요청

```bash
curl -v -X GET "https://{endpoint}/formrecognizer/documentModels/prebuilt-document/analyzeResults/{resultId}?api-version=2021-09-30-preview&api-version=2021-09-30-preview"
```

### <a name="examine-the-response"></a>응답 검사

JSON 출력이 포함된 `200 (Success)` 응답을 받게 됩니다. 작업의 상태를 나타내는 첫 번째 필드, `"status"`입니다. 작업이 완료되지 않는 경우 `"status"`의 값은 `"running"` 또는 `"notStarted"`가 되며, 수동으로 또는 스크립트를 통해 API를 다시 호출해야 합니다. 호출 간에 1초 이상의 간격을 사용하는 것이 좋습니다.

`"analyzeResults"` 노드에는 인식된 모든 텍스트가 포함됩니다. 텍스트는 페이지, 선, 테이블, 키-값 쌍 및 엔터티로 구성됩니다.

#### <a name="sample-response"></a>샘플 응답

```json
{
    "status": "succeeded",
    "createdDateTime": "2021-09-28T16:52:51Z",
    "lastUpdatedDateTime": "2021-09-28T16:53:08Z",
    "analyzeResult": {
        "apiVersion": "2021-09-30-preview",
        "modelId": "prebuilt-document",
        "stringIndexType": "textElements",
        "content": "content extracted",
        "pages": [
            {
                "pageNumber": 1,
                "angle": 0,
                "width": 8.4722,
                "height": 11,
                "unit": "inch",
                "words": [
                    {
                        "content": "Case",
                        "boundingBox": [
                            1.3578,
                            0.2244,
                            1.7328,
                            0.2244,
                            1.7328,
                            0.3502,
                            1.3578,
                            0.3502
                        ],
                        "confidence": 1,
                        "span": {
                            "offset": 0,
                            "length": 4
                        }
                    }

                ],
                "lines": [
                    {
                        "content": "Case",
                        "boundingBox": [
                            1.3578,
                            0.2244,
                            3.2879,
                            0.2244,
                            3.2879,
                            0.3502,
                            1.3578,
                            0.3502
                        ],
                        "spans": [
                            {
                                "offset": 0,
                                "length": 22
                            }
                        ]
                    }
                ]
            }
        ],
        "tables": [
            {
                "rowCount": 8,
                "columnCount": 3,
                "cells": [
                    {
                        "kind": "columnHeader",
                        "rowIndex": 0,
                        "columnIndex": 0,
                        "rowSpan": 1,
                        "columnSpan": 1,
                        "content": "Applicant's Name:",
                        "boundingRegions": [
                            {
                                "pageNumber": 1,
                                "boundingBox": [
                                    1.9198,
                                    4.277,
                                    3.3621,
                                    4.2715,
                                    3.3621,
                                    4.5034,
                                    1.9198,
                                    4.5089
                                ]
                            }
                        ],
                        "spans": [
                            {
                                "offset": 578,
                                "length": 17
                            }
                        ]
                    }
                ],
                "spans": [
                    {
                        "offset": 578,
                        "length": 300
                    },
                    {
                        "offset": 1358,
                        "length": 10
                    }
                ]
            }
        ],
        "keyValuePairs": [
            {
                "key": {
                    "content": "Case",
                    "boundingRegions": [
                        {
                            "pageNumber": 1,
                            "boundingBox": [
                                1.3578,
                                0.2244,
                                1.7328,
                                0.2244,
                                1.7328,
                                0.3502,
                                1.3578,
                                0.3502
                            ]
                        }
                    ],
                    "spans": [
                        {
                            "offset": 0,
                            "length": 4
                        }
                    ]
                },
                "value": {
                    "content": "A Case",
                    "boundingRegions": [
                        {
                            "pageNumber": 1,
                            "boundingBox": [
                                1.8026,
                                0.2276,
                                3.2879,
                                0.2276,
                                3.2879,
                                0.3502,
                                1.8026,
                                0.3502
                            ]
                        }
                    ],
                    "spans": [
                        {
                            "offset": 5,
                            "length": 17
                        }
                    ]
                },
                "confidence": 0.867
            }
        ],
        "entities": [
            {
                "category": "Person",
                "content": "Jim Smith",
                "boundingRegions": [
                    {
                        "pageNumber": 1,
                        "boundingBox": [
                            3.4672,
                            4.3255,
                            5.7118,
                            4.3255,
                            5.7118,
                            4.4783,
                            3.4672,
                            4.4783
                        ]
                    }
                ],
                "confidence": 0.93,
                "spans": [
                    {
                        "offset": 596,
                        "length": 21
                    }
                ]
            }
        ],
        "styles": [
            {
                "isHandwritten": true,
                "confidence": 0.95,
                "spans": [
                    {
                        "offset": 565,
                        "length": 12
                    },
                    {
                        "offset": 3493,
                        "length": 1
                    }
                ]
            }
        ]
    }
}

```

## <a name="try-it-layout-model"></a>**사용해 보기**: 레이아웃 모델

> [!div class="checklist"]
>
> * 이 예제에서는 **URI에 있는 양식 문서 파일** 이 필요합니다. 이 빠른 시작에는 [샘플 양식 문서](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-layout.pdf)를 사용할 수 있습니다.

 명령을 실행하기 전에 다음과 같이 변경합니다.

1. `{endpoint}`를 Form Recognizer 구독에서 얻은 엔드포인트로 바꿉니다.
1. `{subscription key}`를 이전 단계에서 복사한 구독 키로 바꿉니다.
1. `"{your-document-url}`을 예제 URL 중 하나로 바꿉니다.

#### <a name="request"></a>요청

```bash
bash
 curl -v -i POST "https://{endpoint}/formrecognizer/documentModels/prebuilt-layout:analyze?api-version=2021-09-30-preview&api-version=2021-09-30-preview HTTP/1.1" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{'source': '{your-document-url}'}"
```

#### <a name="operation-location"></a>Operation-Location

**Operation-Location** 헤더를 포함하는 `202 (Success)` 응답을 받게 됩니다. 이 헤더 값에는 비동기 작업 상태를 쿼리하고 결과를 가져오는 데 사용할 수 있는 결과 ID가 포함되어 있습니다.

`https://{host}/formrecognizer/documentModels/{modelId}/analyzeResults/**{resultId}**?api-version=2021-07-30-preview`

### <a name="get-layout-results"></a>레이아웃 결과 가져오기

**[문서 분석](https://westus.api.cognitive.microsoft.com/formrecognizer/documentModels/prebuilt-layout:analyze?api-version=2021-09-30-preview&stringIndexType=textElements)** API를 호출한 후 **[분석 결과 가져오기](https://westus.api.cognitive.microsoft.com/formrecognizer/documentModels/prebuilt-layout/analyzeResults/{resultId}?api-version=2021-09-30-preview)** API를 호출하여 작업 상태와 추출된 데이터를 가져옵니다. 명령을 실행하기 전에 다음과 같이 변경합니다.

1. `{endpoint}`를 Form Recognizer 구독에서 얻은 엔드포인트로 바꿉니다.
1. `{subscription key}`를 이전 단계에서 복사한 구독 키로 바꿉니다.
1. `{resultId}`를 이전 단계의 결과 ID로 바꿉니다.
<!-- markdownlint-disable MD024 -->

#### <a name="request"></a>요청

```bash
curl -v -X GET "https://{endpoint}/formrecognizer/documentModels/prebuilt-layout/analyzeResults/{resultId}?api-version=2021-09-30-preview&api-version=2021-09-30-preview"
```

### <a name="examine-the-response"></a>응답 검사

JSON 출력이 포함된 `200 (Success)` 응답을 받게 됩니다. 작업의 상태를 나타내는 첫 번째 필드, `"status"`입니다. 작업이 완료되지 않는 경우 `"status"`의 값은 `"running"` 또는 `"notStarted"`가 되며, 수동으로 또는 스크립트를 통해 API를 다시 호출해야 합니다. 호출 간에 1초 이상의 간격을 사용하는 것이 좋습니다.

## <a name="try-it-prebuilt-invoice-model"></a>**사용해 보기**: 미리 빌드된 청구서 모델

> [!div class="checklist"]
>
> * 이 예제에서는 **URI에 있는 청구서 문서 파일** 이 필요합니다. 이 빠른 시작에는 [샘플 청구서 문서](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-invoice.pdf)를 사용할 수 있습니다.

### <a name="choose-the-invoice-prebuilt-model-id"></a>청구서가 미리 빌드된 모델 ID 선택

청구서에 국한되지 않습니다. 몇 가지 미리 빌드된 모델 중에서 선택할 수 있으며, 각 모델에는 자체의 지원 필드 세트가 있습니다. 분석 작업에 사용할 모델은 분석할 문서 유형에 따라 달라집니다. 현재 Form Recognizer 서비스에서 지원하는 미리 빌드된 모델에 대한 모델 ID는 다음과 같습니다.

* **prebuilt-invoice**: 청구서에서 텍스트, 선택 표시, 테이블, 키-값 쌍 및 키 정보를 추출합니다.
* **prebuilt-businessCard**: 명함에서 텍스트 및 키 정보를 추출합니다.
* **prebuilt-idDocument**: 운전 면허증과 국제 여권에서 텍스트 및 키 정보를 추출합니다.
* **prebuilt-receipt**: 영수증에서 텍스트 및 키 정보를 추출합니다.

명령을 실행하기 전에 다음과 같이 변경합니다.

1. `{endpoint}`를 Form Recognizer 구독에서 얻은 엔드포인트로 바꿉니다.
1. `{subscription key}`를 이전 단계에서 복사한 구독 키로 바꿉니다.
1. `\"{your-document-url}`을 예제 URL 중 하나로 바꿉니다.

#### <a name="request"></a>요청

```bash
bash
 curl -v -i POST "https://{endpoint}/formrecognizer/documentModels/prebuilt-invoice:analyze?api-version=2021-09-30-preview&api-version=2021-09-30-preview HTTP/1.1" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{'source': '{your-document-url}'}"
```

#### <a name="operation-location"></a>Operation-Location

**Operation-Location** 헤더를 포함하는 `202 (Success)` 응답을 받게 됩니다. 이 헤더 값에는 비동기 작업 상태를 쿼리하고 결과를 가져오는 데 사용할 수 있는 결과 ID가 포함되어 있습니다.

https:\//{host}/formrecognizer/documentModels/{modelId}/analyzeResults/ **{resultId}** ?api-version=2021-07-30-preview

### <a name="get-invoice-results"></a>청구서 결과 가져오기

**[문서 분석](https://westus.api.cognitive.microsoft.com/formrecognizer/documentModels/prebuilt-invoice:analyze?api-version=2021-09-30-preview&stringIndexType=textElements)** API를 호출한 후 **[분석 결과 가져오기](https://westus.api.cognitive.microsoft.com/formrecognizer/documentModels/prebuilt-invoice/analyzeResults/{resultId}?api-version=2021-09-30-preview)** API를 호출하여 작업 상태와 추출된 데이터를 가져옵니다. 명령을 실행하기 전에 다음과 같이 변경합니다.

1. `{endpoint}`를 Form Recognizer 구독에서 얻은 엔드포인트로 바꿉니다.
1. `{subscription key}`를 이전 단계에서 복사한 구독 키로 바꿉니다.
1. `{resultId}`를 이전 단계의 결과 ID로 바꿉니다.
<!-- markdownlint-disable MD024 -->

#### <a name="request"></a>요청

```bash
curl -v -X GET "https://{endpoint}/formrecognizer/documentModels/prebuilt-invoice/analyzeResults/{resultId}?api-version=2021-09-30-preview&api-version=2021-09-30-preview"
```

### <a name="examine-the-response"></a>응답 검사

JSON 출력이 포함된 `200 (Success)` 응답을 받게 됩니다. 작업의 상태를 나타내는 첫 번째 필드, `"status"`입니다. 작업이 완료되지 않는 경우 `"status"`의 값은 `"running"` 또는 `"notStarted"`가 되며, 수동으로 또는 스크립트를 통해 API를 다시 호출해야 합니다. 호출 간에 1초 이상의 간격을 사용하는 것이 좋습니다.

### <a name="improve-results"></a>결과 개선

[!INCLUDE [improve results](../includes/improve-results-unlabeled.md)]

## <a name="manage-custom-models"></a>사용자 지정 모델 관리

### <a name="get-a-list-of-models"></a>모델 목록 가져오기

미리 보기 v3.0   [목록 모델](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/GetModels) 요청은 사용자 지정 모델 외에도 미리 빌드된 모델의 페이지 단위 목록을 반환합니다. 성공 상태의 모델만 포함됩니다. [목록 작업](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/GetOperations) 요청을 통해 진행 중이거나 실패한 모델을 열거할 수 있습니다. nextLink 속성을 사용하여 모델의 다음 페이지(있는 경우)에 액세스합니다. 지원되는 문서 및 해당 필드의 목록을 포함하여 반환된 각 모델에 대한 자세한 정보를 보려면 modelId를  [모델 가져오기](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/GetOperations) 요청에 전달합니다. 

```bash
curl -v -X GET "https://{endpoint}/formrecognizer/documentModels?api-version=2021-07-30-preview"
```

### <a name="get-a-specific-model"></a>특정 모델 가져오기

미리 보기 v3.0 [모델 가져오기](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/GetModel)는 상태가 succeeded인 특정 모델에 대한 정보를 검색합니다. 실패한 상태 및 진행 중인 모델의 경우 [작업 가져오기](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/GetOperation)를 사용하여 모델 생성 작업 및 모든 결과 오류의 상태를 추적합니다.

```bash
curl -v -X GET "https://{endpoint}/formrecognizer/documentModels/{modelId}?api-version=2021-07-30-preview" 
```

### <a name="delete-a-model"></a>모델 삭제

미리 보기 v3.0 [모델 삭제](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/DeleteModel) 요청은 사용자 지정 모델을 제거하고 이후 작업에서 modelId에 더 이상 액세스할 수 없습니다.  충돌 없이 동일한 modelId를 사용하여 새 모델을 만들 수 있습니다.

```bash
curl -v -X DELETE "https://{endpoint}/formrecognizer/documentModels/{modelId}?api-version=2021-07-30-preview"
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Form Recognizer REST API 미리 보기(v3.0)를 사용하여 다양한 방식으로 양식을 분석했습니다. 다음으로, 참조 설명서를 탐색하여 Form Recognizer API에 관해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [REST API 미리 보기(v3.0) 참조 설명서](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument)