---
title: '빠른 시작: Azure Form Recognizer REST API'
description: Form Recognizer REST API를 사용하여 사용자 지정 문서에서 키/값 쌍 및 표 데이터를 추출하는 양식 처리 앱을 만듭니다.
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 05/25/2021
ms.author: lajanuar
ms.openlocfilehash: 927711935c6aaea56ca2e2a26d38c5d619b7320c
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130287562"
---
<!-- markdownlint-disable MD001 -->
<!-- markdownlint-disable MD024 -->
<!-- markdownlint-disable MD033 -->
<!-- markdownlint-disable MD034 -->

> [!NOTE]
>
> * 이 빠른 시작은 cURL을 사용하여 REST API 호출을 실행하는 Azure Form Recognizer API 버전 **2.1** 을 대상으로 합니다.

| [Form Recognizer REST API](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeWithCustomForm) | [Azure REST API 참조](/rest/api/azure/) |

## <a name="prerequisites"></a>필수 구성 요소

* [cURL](https://curl.haxx.se/windows/) 설치
* [PowerShell 버전 6.0 이상](/powershell/scripting/install/installing-powershell-core-on-windows) 또는 유사한 명령줄 애플리케이션.
* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/cognitive-services/)
* 학습 데이터 세트가 포함된 Azure Storage Blob. 학습 데이터 세트를 결합하는 옵션 및 팁에 대한 자세한 내용은 [사용자 지정 모델에 대한 학습 데이터 세트 빌드](../../build-training-data-set.md)를 참조하세요. 이 빠른 시작에서는 [샘플 데이터 세트](https://go.microsoft.com/fwlink/?linkid=2090451)의 **Train** 폴더에 있는 파일을 사용할 수 있습니다(*sample_data.zip* 다운로드 및 추출).
* Azure 구독을 보유한 후에는 Azure Portal에서 <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title="Form Recognizer 리소스 만들기"  target="_blank">Form Recognizer 리소스 </a>를 만들어 키와 엔드포인트를 가져옵니다. 배포 후 **리소스로 이동** 을 선택합니다.
  * 애플리케이션을 Form Recognizer API에 연결하려면 만든 리소스의 키와 엔드포인트가 필요합니다. 빠른 시작의 뒷부분에 나오는 코드에 키와 엔드포인트를 붙여넣습니다.
  * 평가판 가격 책정 계층(`F0`)을 통해 서비스를 사용해보고, 나중에 프로덕션용 유료 계층으로 업그레이드할 수 있습니다.
* **영수증 이미지** 의 URL입니다. 이 빠른 시작에서는 [샘플 이미지](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/contoso-allinone.jpg)를 사용할 수 있습니다.
* **명함 이미지** 의 URL입니다. 이 빠른 시작에서는 [샘플 이미지](https://raw.githubusercontent.com/Azure/azure-sdk-for-python/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms/business_cards/business-card-english.jpg)를 사용할 수 있습니다.
* **청구서 이미지** 의 URL입니다. 이 빠른 시작에서는 [샘플 문서](https://raw.githubusercontent.com/Azure/azure-sdk-for-python/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms/forms/Invoice_1.pdf)를 사용할 수 있습니다.
* **ID 문서 이미지** 의 URL입니다. [샘플 이미지](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/DriverLicense.png)를 사용할 수 있습니다.

## <a name="analyze-layout"></a>레이아웃 분석

Form Recognizer를 사용하면 모델을 학습시킬 필요 없이 문서의 표, 선택 표시, 텍스트 및 구조체를 분석하고 추출할 수 있습니다. 레이아웃 추출에 대한 자세한 내용은 [레이아웃 개념 가이드](../../concept-layout.md)를 참조하세요. 명령을 실행하기 전에 다음과 같이 변경합니다.

1. `{endpoint}`를 Form Recognizer 구독에서 얻은 엔드포인트로 바꿉니다.
1. `{subscription key}`를 이전 단계에서 복사한 구독 키로 바꿉니다.
1. `\"{your-document-url}`을 예제 URL 중 하나로 바꿉니다.

#### <a name="request"></a>요청

```bash
curl -v -i POST "https://{endpoint}/formrecognizer/v2.1/layout/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{'source': '{your-document-url}'}"
```

#### <a name="operation-location"></a>Operation-Location

**Operation-Location** 헤더를 포함하는 `202 (Success)` 응답을 받게 됩니다. 이 헤더 값에는 비동기 작업 상태를 쿼리하고 결과를 가져오는 데 사용할 수 있는 결과 ID가 포함되어 있습니다.

https://<span></span>cognitiveservice/formrecognizer/v2.1/layout/analyzeResults/ **{resultId}** . 

다음 예제에서 URL의 일부로 `analyzeResults/` 뒤에 있는 문자열이 결과 ID입니다.

```console
https://cognitiveservice/formrecognizer/v2/layout/analyzeResults/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

### <a name="get-layout-results"></a>레이아웃 결과 가져오기

**[Analyze Layout](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeLayoutAsync)** API를 호출한 후 **[Get Analyze Layout Result](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/GetAnalyzeLayoutResult)** API를 호출하여 작업의 상태와 추출된 데이터를 가져옵니다. 명령을 실행하기 전에 다음과 같이 변경합니다.

1. `{endpoint}`를 Form Recognizer 구독에서 얻은 엔드포인트로 바꿉니다.
1. `{subscription key}`를 이전 단계에서 복사한 구독 키로 바꿉니다.
1. `{resultId}`를 이전 단계의 결과 ID로 바꿉니다.
<!-- markdownlint-disable MD024 -->

#### <a name="request"></a>요청

```bash
curl -v -X GET "https://{endpoint}/formrecognizer/v2.1/layout/analyzeResults/{resultId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="examine-the-results"></a>결과 검사

JSON 콘텐츠가 포함된 `200 (success)` 응답을 받게 됩니다.

다음 청구서 이미지와 해당 JSON 출력을 참조하세요.

* `"readResults"` 노드에는 페이지의 각 경계 상자 배치가 있는 모든 텍스트 줄이 포함되어 있습니다.
* `selectionMarks` 노드는 모든 선택 표시(확인란, 라디오 표시) 및 상태가 “선택됨” 또는 “선택되지 않음”인지 여부를 표시합니다.
* `"pageResults"` 섹션에는 추출된 표가 포함됩니다. 각 표에서 텍스트, 행 및 열 인덱스, 행 및 열 스패닝, 경계 상자 등이 추출됩니다.

:::image type="content" source="../../media/contoso-invoice.png" alt-text="테이블이 포함된 Contoso 프로젝트 문 문서.":::

#### <a name="response-body"></a>응답 본문

이 출력은 편의상 간소화되었습니다. [GitHub의 전체 샘플 출력](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/sample-layout-output.json)을 참조하세요.

```json
{
    "status": "succeeded",
    "createdDateTime": "2020-08-20T20:40:50Z",
    "lastUpdatedDateTime": "2020-08-20T20:40:55Z",
    "analyzeResult": {
        "version": "2.1.0",
        "readResults": [
            {
                "page": 1,
                "angle": 0,
                "width": 8.5,
                "height": 11,
                "unit": "inch",
                "lines": [
                    {
                        "boundingBox": [
                            0.5826,
                            0.4411,
                            2.3387,
                            0.4411,
                            2.3387,
                            0.7969,
                            0.5826,
                            0.7969
                        ],
                        "text": "Contoso, Ltd.",
                        "words": [
                            {
                                "boundingBox": [
                                    0.5826,
                                    0.4411,
                                    1.744,
                                    0.4411,
                                    1.744,
                                    0.7969,
                                    0.5826,
                                    0.7969
                                ],
                                "text": "Contoso,",
                                "confidence": 1
                            },
                            {
                                "boundingBox": [
                                    1.8448,
                                    0.4446,
                                    2.3387,
                                    0.4446,
                                    2.3387,
                                    0.7631,
                                    1.8448,
                                    0.7631
                                ],
                                "text": "Ltd.",
                                "confidence": 1
                            }
                        ]
                    },
                    ...
                        ]
                    }
                ],
                "selectionMarks": [
                    {
                        "boundingBox": [
                            3.9737,
                            3.7475,
                            4.1693,
                            3.7475,
                            4.1693,
                            3.9428,
                            3.9737,
                            3.9428
                        ],
                        "confidence": 0.989,
                        "state": "selected"
                    },
                    ...
                ]
            }
        ],
        "pageResults": [
            {
                "page": 1,
                "tables": [
                    {
                        "rows": 5,
                        "columns": 5,
                        "cells": [
                            {
                                "rowIndex": 0,
                                "columnIndex": 0,
                                "text": "Training Date",
                                "boundingBox": [
                                    0.5133,
                                    4.2167,
                                    1.7567,
                                    4.2167,
                                    1.7567,
                                    4.4492,
                                    0.5133,
                                    4.4492
                                ],
                                "elements": [
                                    "#/readResults/0/lines/12/words/0",
                                    "#/readResults/0/lines/12/words/1"
                                ]
                            },
                            ...
                        ]
                    },
                    ...
                ]
            }
        ]
    }
}
```

## <a name="analyze-receipts"></a>영수증 분석

이 섹션에서는 사전 학습된 영수증 모델을 사용하여 미국 영수증의 공통 필드를 분석하고 추출하는 방법을 보여 줍니다. 영수증 분석에 대한 자세한 내용은 [영수증 개념 가이드](../../concept-receipt.md)를 참조하세요. 영수증 분석을 시작하기 위해, 아래 cURL 명령을 사용하여 **[Analyze Receipt](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeReceiptAsync)** API를 호출합니다. 명령을 실행하기 전에 다음과 같이 변경합니다.

1. `{endpoint}`를 Form Recognizer 구독에서 얻은 엔드포인트로 바꿉니다.
1. `{your receipt URL}`을 영수증 이미지의 URL 주소로 바꿉니다.
1. `{subscription key>`를 이전 단계에서 복사한 구독 키로 바꿉니다.

#### <a name="request"></a>요청

```bash
curl -i -X POST "https://{endpoint}/formrecognizer/v2.1/prebuilt/receipt/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ 'source': '{your receipt URL}'}"
```

#### <a name="operation-location"></a>Operation-Location

**Operation-Location** 헤더를 포함하는 `202 (Success)` 응답을 받게 됩니다. 이 헤더 값에는 비동기 작업 상태를 쿼리하고 결과를 가져오는 데 사용할 수 있는 결과 ID가 포함되어 있습니다.

*https://<span></span>cognitiveservice/formrecognizer/v2.1/prebuilt/receipt/analyzeResults/**{resultId}* **

다음 예제에서 `operations/` 뒤에 있는 문자열이 결과 ID입니다.

```console
https://cognitiveservice/formrecognizer/v2.1/prebuilt/receipt/operations/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

### <a name="get-receipt-results"></a>영수증 결과 가져오기

**Analyze Receipt** API를 호출한 후 **[Get Analyze Receipt Result](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/GetAnalyzeReceiptResult)** API를 호출하여 작업의 상태와 추출된 데이터를 가져옵니다. 명령을 실행하기 전에 다음과 같이 변경합니다.

1. `{endpoint}`를 Form Recognizer 구독 키에서 얻은 엔드포인트로 바꿉니다. Form Recognizer 리소스 **개요** 탭에서 찾을 수 있습니다.
1. `{resultId}`를 이전 단계의 결과 ID로 바꿉니다.
1. `{subscription key}`를 구독 키로 바꿉니다.

#### <a name="request"></a>요청

```bash
curl -X GET "https://{endpoint}/formrecognizer/v2.1/prebuilt/receipt/analyzeResults/{resultId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="examine-the-response"></a>응답 검사

JSON 출력이 포함된 `200 (Success)` 응답을 받게 됩니다. 작업의 상태를 나타내는 첫 번째 필드, `"status"`입니다. 작업이 완료되지 않는 경우 `"status"`의 값은 `"running"` 또는 `"notStarted"`가 되며, 수동으로 또는 스크립트를 통해 API를 다시 호출해야 합니다. 호출 간에 1초 이상의 간격을 사용하는 것이 좋습니다.

`"readResults"` 노드에는 인식된 모든 텍스트가 포함됩니다(선택적 *includeTextDetails* 매개 변수를 `true`로 설정한 경우). 텍스트는 페이지별로, 그 다음에는 줄별로, 그 다음에는 개별 단어별로 정리됩니다. `"documentResults"` 노드에는 모델이 검색한 수신 특정 값이 포함됩니다. 여기서는 세금, 합계, 판매자 주소 등과 같은 유용한 키/값 쌍을 찾을 수 있습니다.

다음 영수증 이미지와 해당 JSON 출력을 참조하세요.

![Contoso 매장의 영수증](../../media/contoso-allinone.jpg)

#### <a name="response-body"></a>응답 본문

이 출력은 가독성을 위해 줄였습니다. [GitHub의 전체 샘플 출력](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/receipt-result.json)을 참조하세요.

```json
{
  "status":"succeeded",
  "createdDateTime":"2019-12-17T04:11:24Z",
  "lastUpdatedDateTime":"2019-12-17T04:11:32Z",
  "analyzeResult":{
    "version":"2.1.0",
    "readResults":[
      {
        "page":1,
        "angle":0.6893,
        "width":1688,
        "height":3000,
        "unit":"pixel",
        "language":"en",
        "lines":[
          {
            "text":"Contoso",
            "boundingBox":[
              635,
              510,
              1086,
              461,
              1098,
              558,
              643,
              604
            ],
            "words":[
              {
                "text":"Contoso",
                "boundingBox":[
                  639,
                  510,
                  1087,
                  461,
                  1098,
                  551,
                  646,
                  604
                ],
                "confidence":0.955
              }
            ]
          },
          ...
        ]
      }
    ],
    "documentResults":[
      {
        "docType":"prebuilt:receipt",
        "pageRange":[
          1,
          1
        ],
        "fields":{
          "ReceiptType":{
            "type":"string",
            "valueString":"Itemized",
            "confidence":0.692
          },
          "MerchantName":{
            "type":"string",
            "valueString":"Contoso Contoso",
            "text":"Contoso Contoso",
            "boundingBox":[
              378.2,
              292.4,
              1117.7,
              468.3,
              1035.7,
              812.7,
              296.3,
              636.8
            ],
            "page":1,
            "confidence":0.613,
            "elements":[
              "#/readResults/0/lines/0/words/0",
              "#/readResults/0/lines/1/words/0"
            ]
          },
          "MerchantAddress":{
            "type":"string",
            "valueString":"123 Main Street Redmond, WA 98052",
            "text":"123 Main Street Redmond, WA 98052",
            "boundingBox":[
              302,
              675.8,
              848.1,
              793.7,
              809.9,
              970.4,
              263.9,
              852.5
            ],
            "page":1,
            "confidence":0.99,
            "elements":[
              "#/readResults/0/lines/2/words/0",
              "#/readResults/0/lines/2/words/1",
              "#/readResults/0/lines/2/words/2",
              "#/readResults/0/lines/3/words/0",
              "#/readResults/0/lines/3/words/1",
              "#/readResults/0/lines/3/words/2"
            ]
          },
          "MerchantPhoneNumber":{
            "type":"phoneNumber",
            "valuePhoneNumber":"+19876543210",
            "text":"987-654-3210",
            "boundingBox":[
              278,
              1004,
              656.3,
              1054.7,
              646.8,
              1125.3,
              268.5,
              1074.7
            ],
            "page":1,
            "confidence":0.99,
            "elements":[
              "#/readResults/0/lines/4/words/0"
            ]
          },
          "TransactionDate":{
            "type":"date",
            "valueDate":"2019-06-10",
            "text":"6/10/2019",
            "boundingBox":[
              265.1,
              1228.4,
              525,
              1247,
              518.9,
              1332.1,
              259,
              1313.5
            ],
            "page":1,
            "confidence":0.99,
            "elements":[
              "#/readResults/0/lines/5/words/0"
            ]
          },
          "TransactionTime":{
            "type":"time",
            "valueTime":"13:59:00",
            "text":"13:59",
            "boundingBox":[
              541,
              1248,
              677.3,
              1261.5,
              668.9,
              1346.5,
              532.6,
              1333
            ],
            "page":1,
            "confidence":0.977,
            "elements":[
              "#/readResults/0/lines/5/words/1"
            ]
          },
          "Items":{
            "type":"array",
            "valueArray":[
              {
                "type":"object",
                "valueObject":{
                  "Quantity":{
                    "type":"number",
                    "text":"1",
                    "boundingBox":[
                      245.1,
                      1581.5,
                      300.9,
                      1585.1,
                      295,
                      1676,
                      239.2,
                      1672.4
                    ],
                    "page":1,
                    "confidence":0.92,
                    "elements":[
                      "#/readResults/0/lines/7/words/0"
                    ]
                  },
                  "Name":{
                    "type":"string",
                    "valueString":"Cappuccino",
                    "text":"Cappuccino",
                    "boundingBox":[
                      322,
                      1586,
                      654.2,
                      1601.1,
                      650,
                      1693,
                      317.8,
                      1678
                    ],
                    "page":1,
                    "confidence":0.923,
                    "elements":[
                      "#/readResults/0/lines/7/words/1"
                    ]
                  },
                  "TotalPrice":{
                    "type":"number",
                    "valueNumber":2.2,
                    "text":"$2.20",
                    "boundingBox":[
                      1107.7,
                      1584,
                      1263,
                      1574,
                      1268.3,
                      1656,
                      1113,
                      1666
                    ],
                    "page":1,
                    "confidence":0.918,
                    "elements":[
                      "#/readResults/0/lines/8/words/0"
                    ]
                  }
                }
              },
              ...
            ]
          },
          "Subtotal":{
            "type":"number",
            "valueNumber":11.7,
            "text":"11.70",
            "boundingBox":[
              1146,
              2221,
              1297.3,
              2223,
              1296,
              2319,
              1144.7,
              2317
            ],
            "page":1,
            "confidence":0.955,
            "elements":[
              "#/readResults/0/lines/13/words/1"
            ]
          },
          "Tax":{
            "type":"number",
            "valueNumber":1.17,
            "text":"1.17",
            "boundingBox":[
              1190,
              2359,
              1304,
              2359,
              1304,
              2456,
              1190,
              2456
            ],
            "page":1,
            "confidence":0.979,
            "elements":[
              "#/readResults/0/lines/15/words/1"
            ]
          },
          "Tip":{
            "type":"number",
            "valueNumber":1.63,
            "text":"1.63",
            "boundingBox":[
              1094,
              2479,
              1267.7,
              2485,
              1264,
              2591,
              1090.3,
              2585
            ],
            "page":1,
            "confidence":0.941,
            "elements":[
              "#/readResults/0/lines/17/words/1"
            ]
          },
          "Total":{
            "type":"number",
            "valueNumber":14.5,
            "text":"$14.50",
            "boundingBox":[
              1034.2,
              2617,
              1387.5,
              2638.2,
              1380,
              2763,
              1026.7,
              2741.8
            ],
            "page":1,
            "confidence":0.985,
            "elements":[
              "#/readResults/0/lines/19/words/0"
            ]
          }
        }
      }
    ]
  }
}
```

## <a name="analyze-business-cards"></a>명함 분석

이 섹션에서는 사전 학습된 모델을 사용하여 영문 명함의 공통 필드를 분석하고 추출하는 방법을 보여 줍니다. 명함 분석에 대한 자세한 내용은 [명함 개념 가이드](../../concept-business-card.md)를 참조하세요. 명함 분석을 시작하려면 아래 cURL 명령을 사용하여 **[Analyze Business Card](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeBusinessCardAsync)** API를 호출합니다. 명령을 실행하기 전에 다음과 같이 변경합니다.

1. `{endpoint}`를 Form Recognizer 구독에서 얻은 엔드포인트로 바꿉니다.
1. `{your business card URL}`을 영수증 이미지의 URL 주소로 바꿉니다.
1. `{subscription key}`를 이전 단계에서 복사한 구독 키로 바꿉니다.

#### <a name="request"></a>요청

```bash
curl -i -X POST "https://{endpoint}/formrecognizer/v2.1/prebuilt/businessCard/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ 'source': '{your receipt URL}'}"
```

#### <a name="operation-location"></a>Operation-Location

**Operation-Location** 헤더를 포함하는 `202 (Success)` 응답을 받게 됩니다. 이 헤더 값에는 비동기 작업 상태를 쿼리하고 결과를 가져오는 데 사용할 수 있는 결과 ID가 포함되어 있습니다.

_https://<span></span>cognitiveservice/formrecognizer/v2.1/prebuilt/businessCard/analyzeResults/ **{resultId}**_

다음 예제에서 URL의 일부로 `analyzeResults/` 뒤에 있는 문자열이 결과 ID입니다.

```console
https://cognitiveservice/formrecognizer/v2.1/prebuilt/businessCard/analyzeResults/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

### <a name="get-business-card-results"></a>명함 결과 가져오기

**Analyze Business Card** API를 호출한 후 **[Get Analyze Layout Result](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/GetAnalyzeBusinessCardResult)** API를 호출하여 작업의 상태와 추출된 데이터를 가져옵니다. 명령을 실행하기 전에 다음과 같이 변경합니다.

1. `{endpoint}`를 Form Recognizer 구독 키에서 얻은 엔드포인트로 바꿉니다. Form Recognizer 리소스 **개요** 탭에서 찾을 수 있습니다.
1. `{resultId}`를 이전 단계의 결과 ID로 바꿉니다.
1. `{subscription key}`를 구독 키로 바꿉니다.

```bash
curl -v -X GET https://{endpoint}/formrecognizer/v2.1/prebuilt/businessCard/analyzeResults/{resultId}"
-H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="examine-the-response"></a>응답 검사

JSON 출력이 포함된 `200 (Success)` 응답을 받게 됩니다.

`"readResults"` 노드에는 인식된 모든 텍스트가 포함됩니다. 텍스트는 페이지별로, 그 다음에는 줄별로, 그 다음에는 개별 단어별로 정리됩니다. `"documentResults"` 노드에는 모델이 검색한 명함 특정 값이 포함됩니다. 여기서 회사 이름, 이름, 성, 전화 등과 같은 유용한 연락처 정보를 찾을 수 있습니다.

![Contoso 회사의 명함](../../media/business-card-english.jpg)

이 샘플 JSON 출력은 가독성을 위해 줄였습니다. [GitHub의 전체 샘플 출력](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/business-card-result.json)을 참조하세요.

```json
{
    "status": "succeeded",
    "createdDateTime":"2021-02-09T18:14:05Z",
    "lastUpdatedDateTime":"2021-02-09T18:14:10Z",
    "analyzeResult": {
        "version": "2.1.0",
        "readResults": [
            {
             "page":1,
             "angle":-16.6836,
             "width":4032,
             "height":3024,
             "unit":"pixel"
          }
        ],
        "documentResults": [
            {
                "docType": "prebuilt:businesscard",
                "pageRange": [
                    1,
                    1
                ],
                "fields": {
                    "ContactNames": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "object",
                                "valueObject": {
                                    "FirstName": {
                                        "type": "string",
                                        "valueString": "Avery",
                                        "text": "Avery",
                                        "boundingBox": [
                                            703,
                                            1096,
                                            1134,
                                            989,
                                            1165,
                                            1109,
                                            733,
                                            1206
                                        ],
                                        "page": 1
                                },
                                "text": "Dr. Avery Smith",
                                "boundingBox": [
                                    419.3,
                                    1154.6,
                                    1589.6,
                                    877.9,
                                    1618.9,
                                    1001.7,
                                    448.6,
                                    1278.4
                                ],
                                "confidence": 0.993
                            }
                        ]
                    },
                    "Emails": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "avery.smith@contoso.com",
                                "text": "avery.smith@contoso.com",
                                "boundingBox": [
                                    2107,
                                    934,
                                    2917,
                                    696,
                                    2935,
                                    764,
                                    2126,
                                    995
                                ],
                                "page": 1,
                                "confidence": 0.99
                            }
                        ]
                    },
                    "Websites": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "https://www.contoso.com/",
                                "text": "https://www.contoso.com/",
                                "boundingBox": [
                                    2121,
                                    1002,
                                    2992,
                                    755,
                                    3014,
                                    826,
                                    2143,
                                    1077
                                ],
                                "page": 1,
                                "confidence": 0.995
                            }
                        ]
                    }
                }
            }
        ]
    }
}
```

스크립트는 **명함 분석** 작업이 완료될 때까지 콘솔에 응답을 출력합니다.

## <a name="analyze-invoices"></a>송장 분석

Form Recognizer를 사용하여 지정된 청구서 문서에서 필드 텍스트 및 의미 체계 값을 추출할 수 있습니다.  청구서 분석을 시작하려면 아래의 cURL 명령을 사용합니다. 청구서 분석에 대한 자세한 내용은 [청구서 개념 가이드](../../concept-invoice.md)를 참조하세요. 청구서 분석을 시작하기 위해 아래 cURL 명령을 사용하여 **[Analyze Invoice](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/5ed8c9843c2794cbb1a96291)** API를 호출합니다. 명령을 실행하기 전에 다음과 같이 변경합니다.

1. `{endpoint}`를 Form Recognizer 구독에서 얻은 엔드포인트로 바꿉니다.
1. `{your invoice URL}`은 영수증 문서의 URL 주소로 바꿉니다.
1. `{subscription key}`를 구독 키로 바꿉니다.

#### <a name="request"></a>요청

```bash
curl -v -i POST https://{endpoint}/formrecognizer/v2.1/prebuilt/invoice/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key:  {subscription key}" --data-ascii "{'source': '{your invoice URL}'}"
```

#### <a name="operation-location"></a>Operation-Location

**Operation-Location** 헤더를 포함하는 `202 (Success)` 응답을 받게 됩니다. 이 헤더 값에는 비동기 작업 상태를 쿼리하고 결과를 가져오는 데 사용할 수 있는 결과 ID가 포함되어 있습니다.

 _https://<span></span>cognitiveservice/formrecognizer/v2.1/prebuilt/receipt/analyzeResults/ **{resultId}**_

다음 예제에서 URL의 일부로 `analyzeResults/` 뒤에 있는 문자열이 결과 ID입니다.

```console
https://cognitiveservice/formrecognizer/v2.1/prebuilt/invoice/analyzeResults/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

### <a name="get-invoice-results"></a>청구서 결과 가져오기

**Analyze Invoice** API를 호출한 후 **[Get Analyze Invoice Result](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/5ed8c9acb78c40a2533aee83)** API를 호출하여 작업의 상태와 추출된 데이터를 가져옵니다. 명령을 실행하기 전에 다음과 같이 변경합니다.

1. `{endpoint}`를 Form Recognizer 구독 키에서 얻은 엔드포인트로 바꿉니다. Form Recognizer 리소스 **개요** 탭에서 찾을 수 있습니다.
1. `{resultId}`를 이전 단계의 결과 ID로 바꿉니다.
1. `{subscription key}`를 구독 키로 바꿉니다.

#### <a name="request"></a>요청

```bash
curl -v -X GET "https://{endpoint}/formrecognizer/v2.1/prebuilt/invoice/analyzeResults/{resultId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="examine-the-response"></a>응답 검사

JSON 출력이 포함된 `200 (Success)` 응답을 받게 됩니다.

* `"readResults"` 필드는 청구서에서 추출된 모든 텍스트 줄이 포함되어 있습니다.
* `"pageResults"`는 청구서에서 추출된 표 및 선택 표시를 포함합니다.
* `"documentResults"` 필드에는 청구서의 가장 관련성이 높은 부분에 대한 키/값 정보가 포함되어 있습니다.

다음 청구서 문서와 해당 JSON 출력을 참조하세요.

* [샘플 청구서](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/curl/form-recognizer/sample-invoice.pdf)

#### <a name="response-body"></a>응답 본문

이 JSON 콘텐츠는 가독성을 위해 줄였습니다. [GitHub의 전체 샘플 출력](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/sample-invoice-output.json)을 참조하세요.

```json
{
    "status": "succeeded",
    "createdDateTime": "2020-11-06T23:32:11Z",
    "lastUpdatedDateTime": "2020-11-06T23:32:20Z",
    "analyzeResult": {
        "version": "2.1.0",
        "readResults": [{
            "page": 1,
            "angle": 0,
            "width": 8.5,
            "height": 11,
            "unit": "inch"
        }],
        "pageResults": [{
            "page": 1,
            "tables": [{
                "rows": 3,
                "columns": 4,
                "cells": [{
                    "rowIndex": 0,
                    "columnIndex": 0,
                    "text": "QUANTITY",
                    "boundingBox": [0.4953,
                    5.7306,
                    1.8097,
                    5.7306,
                    1.7942,
                    6.0122,
                    0.4953,
                    6.0122]
                },
                {
                    "rowIndex": 0,
                    "columnIndex": 1,
                    "text": "DESCRIPTION",
                    "boundingBox": [1.8097,
                    5.7306,
                    5.7529,
                    5.7306,
                    5.7452,
                    6.0122,
                    1.7942,
                    6.0122]
                },
                ...
                ],
                "boundingBox": [0.4794,
                5.7132,
                8.0158,
                5.714,
                8.0118,
                6.5627,
                0.4757,
                6.5619]
            },
            {
                "rows": 2,
                "columns": 6,
                "cells": [{
                    "rowIndex": 0,
                    "columnIndex": 0,
                    "text": "SALESPERSON",
                    "boundingBox": [0.4979,
                    4.963,
                    1.8051,
                    4.963,
                    1.7975,
                    5.2398,
                    0.5056,
                    5.2398]
                },
                {
                    "rowIndex": 0,
                    "columnIndex": 1,
                    "text": "P.O. NUMBER",
                    "boundingBox": [1.8051,
                    4.963,
                    3.3047,
                    4.963,
                    3.3124,
                    5.2398,
                    1.7975,
                    5.2398]
                },
                ...
                ],
                "boundingBox": [0.4976,
                4.961,
                7.9959,
                4.9606,
                7.9959,
                5.5204,
                0.4972,
                5.5209]
            }]
        }],
        "documentResults": [{
            "docType": "prebuilt:invoice",
            "pageRange": [1,
            1],
            "fields": {
                "AmountDue": {
                    "type": "number",
                    "valueNumber": 610,
                    "text": "$610.00",
                    "boundingBox": [7.3809,
                    7.8153,
                    7.9167,
                    7.8153,
                    7.9167,
                    7.9591,
                    7.3809,
                    7.9591],
                    "page": 1,
                    "confidence": 0.875
                },
                "BillingAddress": {
                    "type": "string",
                    "valueString": "123 Bill St, Redmond WA, 98052",
                    "text": "123 Bill St, Redmond WA, 98052",
                    "boundingBox": [0.594,
                    4.3724,
                    2.0125,
                    4.3724,
                    2.0125,
                    4.7125,
                    0.594,
                    4.7125],
                    "page": 1,
                    "confidence": 0.997
                },
                "BillingAddressRecipient": {
                    "type": "string",
                    "valueString": "Microsoft Finance",
                    "text": "Microsoft Finance",
                    "boundingBox": [0.594,
                    4.1684,
                    1.7907,
                    4.1684,
                    1.7907,
                    4.2837,
                    0.594,
                    4.2837],
                    "page": 1,
                    "confidence": 0.998
                },
                ...
            }
        }]
    }
}
```

## <a name="analyze-identity-id-documents"></a>ID 문서 분석

ID 문서 분석을 시작하려면 다음과 같은 cURL 명령을 사용합니다. ID 문서 분석에 대한 자세한 내용은 [ID 문서 개념 가이드](../../concept-id-document.md)를 참조하세요. ID 문서 분석을 시작하려면 다음과 같은 cURL 명령을 사용하여 **[ID 문서 분석](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/5f74a7738978e467c5fb8707)** API를 호출합니다. 명령을 실행하기 전에 다음과 같이 변경합니다.

1. `{endpoint}`를 Form Recognizer 구독에서 얻은 엔드포인트로 바꿉니다.
1. `{your ID document URL}`을 영수증 이미지의 URL 주소로 바꿉니다.
1. `{subscription key}`를 이전 단계에서 복사한 구독 키로 바꿉니다.

#### <a name="request"></a>요청

```bash
curl -i -X POST "https://{endpoint}/formrecognizer/v2.1/prebuilt/idDocument/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ 'source': '{your ID document URL}'}"
```

#### <a name="operation-location"></a>Operation-Location

**Operation-Location** 헤더를 포함하는 `202 (Success)` 응답을 받게 됩니다. 이 헤더 값에는 비동기 작업 상태를 쿼리하고 결과를 가져오는 데 사용할 수 있는 결과 ID가 포함되어 있습니다.

_https://<span></span>cognitiveservice/formrecognizer/v2.1/prebuilt/documentId/analyzeResults/ **{resultId}**_

다음 예제에서 `analyzeResults/` 뒤에 있는 문자열이 결과 ID입니다.

```console
https://westus.api.cognitive.microsoft.com/formrecognizer/v2.1/prebuilt/idDocument/analyzeResults/83d0137b-28e1-4051-98ce-42bd21f77ae0
```

### <a name="get-the-analyze-id-document-result"></a>ID 문서 분석 결과 가져오기

**ID 문서 분석** API를 호출한 후 **[ID 문서 분석 결과 가져오기](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/GetAnalyzeBusinessCardResult)** API를 호출하여 작업 상태와 추출된 데이터를 가져옵니다.  명령을 실행하기 전에 다음과 같이 변경합니다.

1. `{endpoint}`를 Form Recognizer 구독 키에서 얻은 엔드포인트로 바꿉니다. Form Recognizer 리소스 **개요** 탭에서 찾을 수 있습니다.
1. `{resultId}`를 이전 단계의 결과 ID로 바꿉니다.
1. `{subscription key}`를 구독 키로 바꿉니다.

#### <a name="request"></a>요청

```bash
curl -X GET "https://{endpoint}/formrecognizer/v2.1/prebuilt/businessCard/analyzeResults/{resultId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="examine-the-response"></a>응답 검사

JSON 출력이 포함된 `200 (Success)` 응답을 받게 됩니다. 작업의 상태를 나타내는 첫 번째 필드, `"status"`입니다. 작업이 완료되지 않으면 `"status"` 값은 `"running"` 또는 `"notStarted"`가 되며 `succeeded` 값이 제공될 때까지 수동으로 또는 스크립트를 통해 API를 다시 호출해야 합니다. 호출 간에 1초 이상의 간격을 사용하는 것이 좋습니다.

* `"readResults"` 필드에는 ID 문서에서 추출된 모든 텍스트 줄이 포함되어 있습니다.
* `"documentResults"` 필드에는 개체 배열이 포함되어 있으며 각 개체 배열은 입력 문서에서 검색된 ID 문서를 나타냅니다.

다음은 샘플 ID 문서 및 해당 JSON 출력입니다.

* :::image type="content" source="https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/DriverLicense.png" alt-text="샘플 운전 면허증":::

#### <a name="response-body"></a>응답 본문

```json
{
    "status": "succeeded",
    "createdDateTime": "2021-04-13T17:24:52Z",
    "lastUpdatedDateTime": "2021-04-13T17:24:55Z",
    "analyzeResult": {
      "version": "2.1.0",
      "readResults": [
        {
          "page": 1,
          "angle": -0.2823,
          "width": 450,
          "height": 294,
          "unit": "pixel"
        }
      ],
      "documentResults": [
        {
          "docType": "prebuilt:idDocument:driverLicense",
          "docTypeConfidence": 0.995,
          "pageRange": [
            1,
            1
          ],
          "fields": {
            "Address": {
              "type": "string",
              "valueString": "123 STREET ADDRESS YOUR CITY WA 99999-1234",
              "text": "123 STREET ADDRESS YOUR CITY WA 99999-1234",
              "boundingBox": [
                158,
                151,
                326,
                151,
                326,
                177,
                158,
                177
              ],
              "page": 1,
              "confidence": 0.965
            },
            "CountryRegion": {
              "type": "countryRegion",
              "valueCountryRegion": "USA",
              "confidence": 0.99
            },
            "DateOfBirth": {
              "type": "date",
              "valueDate": "1958-01-06",
              "text": "01/06/1958",
              "boundingBox": [
                187,
                133,
                272,
                132,
                272,
                148,
                187,
                149
              ],
              "page": 1,
              "confidence": 0.99
            },
            "DateOfExpiration": {
              "type": "date",
              "valueDate": "2020-08-12",
              "text": "08/12/2020",
              "boundingBox": [
                332,
                230,
                414,
                228,
                414,
                244,
                332,
                245
              ],
              "page": 1,
              "confidence": 0.99
            },
            "DocumentNumber": {
              "type": "string",
              "valueString": "LICWDLACD5DG",
              "text": "LIC#WDLABCD456DG",
              "boundingBox": [
                162,
                70,
                307,
                68,
                307,
                84,
                163,
                85
              ],
              "page": 1,
              "confidence": 0.99
            },
            "FirstName": {
              "type": "string",
              "valueString": "LIAM R.",
              "text": "LIAM R.",
              "boundingBox": [
                158,
                102,
                216,
                102,
                216,
                116,
                158,
                116
              ],
              "page": 1,
              "confidence": 0.985
            },
            "LastName": {
              "type": "string",
              "valueString": "TALBOT",
              "text": "TALBOT",
              "boundingBox": [
                160,
                86,
                213,
                85,
                213,
                99,
                160,
                100
              ],
              "page": 1,
              "confidence": 0.987
            },
            "Region": {
              "type": "string",
              "valueString": "Washington",
              "confidence": 0.99
            },
            "Sex": {
              "type": "string",
              "valueString": "M",
              "text": "M",
              "boundingBox": [
                226,
                190,
                232,
                190,
                233,
                201,
                226,
                201
              ],
              "page": 1,
              "confidence": 0.99
            }
          }
        }
      ]
    }
  }
```

## <a name="train-a-custom-model"></a>사용자 지정 모델 학습

사용자 지정 모델을 학습시키려면 Azure Storage Blob의 학습 데이터 세트가 필요합니다. 동일한 형식/구조의 채워진 양식(PDF 문서 및/또는 이미지)이 5개 이상 필요합니다. 학습 데이터를 결합하는 옵션 및 팁에 대한 자세한 내용은 [사용자 지정 모델에 대한 학습 데이터 집합 빌드](../../build-training-data-set.md)를 참조하세요.

레이블이 지정된 데이터를 사용하지 않는 학습은 기본 작업이므로 더 간단합니다. 또는 학습 데이터의 일부 또는 전체에 레이블을 미리 수동으로 지정할 수 있습니다. 이는 좀 더 복잡한 프로세스지만 학습된 모델을 더 효율적으로 만듭니다.

> [!NOTE]
> [Form Recognizer 샘플 레이블 지정 도구](../../label-tool.md)와 같은 그래픽 사용자 인터페이스를 사용하여 모델을 학습시킬 수도 있습니다.

### <a name="train-a-model-without-labels"></a>레이블 없이 모델 학습

Azure Blob 컨테이너의 문서를 사용하여 Form Recognizer 모델을 학습시키려면 다음 cURL 명령을 실행하여 **[Train Custom Model](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/TrainCustomModelAsync)** API를 호출합니다. 명령을 실행하기 전에 다음과 같이 변경합니다.

1. `{endpoint}`를 Form Recognizer 구독에서 얻은 엔드포인트로 바꿉니다.
1. `{subscription key}`를 이전 단계에서 복사한 구독 키로 바꿉니다.
1. `{SAS URL}`을 Azure Blob Storage 컨테이너의 SAS(공유 액세스 서명) URL로 바꿉니다. [!INCLUDE [get SAS URL](../../includes/sas-instructions.md)]

   :::image type="content" source="../../media/quickstarts/get-sas-url.png" alt-text="SAS URL 검색":::

#### <a name="request"></a>요청

```bash
curl -i -X POST "https://{endpoint}/formrecognizer/v2.1/custom/models" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ 'source': '{SAS URL}'}"
```

#### <a name="location"></a>Location

**위치** 헤더가 포함된 `201 (Success)` 응답을 받게 됩니다. 이 헤더의 값에는 작업 상태를 쿼리하고 결과를 가져오는 데 사용할 수 있는 새로 학습된 모델의 모델 ID가 포함됩니다.

_https://{endpoint}/formrecognizer/v2.1/custom/models/ **{modelId}**_

다음 예제에서 URL의 일부로 `models/` 뒤에 있는 문자열이 모델 ID입니다.

```console
https://westus.api.cognitive.microsoft.com/formrecognizer/v2.1/custom/models/77d8ecad-b8c1-427e-ac20-a3fe4af503e9
```

### <a name="train-a-model-with-labels"></a>레이블을 사용하여 모델 학습

레이블을 사용하여 학습하려면 학습 문서와 별도로 Blob 스토리지 컨테이너에 특별한 레이블 정보 파일(`\<filename\>.pdf.labels.json`)이 있어야 합니다. [Form Recognizer 샘플 레이블 지정 도구](../../label-tool.md)는 이러한 레이블 파일을 만드는 데 도움이 되는 UI를 제공합니다. 이 파일을 만든 후에는 JSON 본문에 `"useLabelFile"` 매개 변수를 `true`로 설정하여 **[사용자 지정 모델 학습](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/TrainCustomModelAsync)** API를 호출할 수 있습니다.

명령을 실행하기 전에 다음과 같이 변경합니다.

1. `{endpoint}`를 Form Recognizer 구독에서 얻은 엔드포인트로 바꿉니다.
1. `{subscription key}`를 이전 단계에서 복사한 구독 키로 바꿉니다.
1. `{SAS URL}`을 Azure Blob Storage 컨테이너의 SAS(공유 액세스 서명) URL로 바꿉니다. [!INCLUDE [get SAS URL](../../includes/sas-instructions.md)]

   :::image type="content" source="../../media/quickstarts/get-sas-url.png" alt-text="SAS URL 검색":::

#### <a name="request"></a>요청

```bash
curl -i -X POST "https://{endpoint}/formrecognizer/v2.1/custom/models" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ 'source': '{SAS URL}', 'useLabelFile':true}"
```

#### <a name="location"></a>Location

**위치** 헤더가 포함된 `201 (Success)` 응답을 받게 됩니다. 이 헤더의 값에는 작업 상태를 쿼리하고 결과를 가져오는 데 사용할 수 있는 새로 학습된 모델의 모델 ID가 포함됩니다.

_https://{endpoint}/formrecognizer/v2.1/custom/models/ **{modelId}**_

다음 예제에서 URL의 일부로 `models/` 뒤에 있는 문자열이 모델 ID입니다.

```console
https://westus.api.cognitive.microsoft.com/formrecognizer/v2.1/custom/models/4da0bf8e-5325-467c-93bb-9ff13d5f72a2
```

### <a name="get-training-results"></a>학습 결과 가져오기

학습 작업을 시작한 후에는 **[사용자 지정 모델 가져오기](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/GetCustomModel)** 를 사용하여 학습 상태를 확인합니다. 모델 ID를 이 API 요청에 전달하여 학습 상태를 확인합니다.

1. `{endpoint}`를 Form Recognizer 구독 키에서 얻은 엔드포인트로 바꿉니다.
1. `{subscription key}`를 구독 키로 바꿉니다.
1. `{model ID}`를 이전 단계에서 받은 모델 ID로 바꿉니다.

#### <a name="request"></a>요청

```bash
curl -X GET "https://{endpoint}/formrecognizer/v2.1/custom/models/{modelId}" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

## <a name="analyze-forms-with-a-custom-model"></a>사용자 지정 모델을 사용하여 양식 분석

다음으로, 새로 학습된 모델을 사용하여 문서를 분석하고 키-값 쌍 및 테이블을 추출합니다. 다음 cURL 명령을 실행하여 **[Analyze Form](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeWithCustomForm)** API를 호출합니다. 명령을 실행하기 전에 다음과 같이 변경합니다.

1. `{endpoint}`를 Form Recognizer 구독 키에서 가져온 엔드포인트로 바꿉니다. Form Recognizer 리소스 **개요** 탭에서 찾을 수 있습니다.
1. `{model ID}`를 이전 섹션에서 받은 모델 ID로 바꿉니다.
1. `{SAS URL}`을 Azure 스토리지의 파일을 잇는 SAS URL로 바꿉니다. 학습 섹션의 단계를 따르되 전체 Blob 컨테이너에 대한 SAS URL을 가져오는 대신 분석하려는 특정 파일에 대한 SAS URL을 가져옵니다.
1. `{subscription key}`를 구독 키로 바꿉니다.

#### <a name="request"></a>요청

```bash
curl -v "https://{endpoint}/formrecognizer/v2.1/custom/models/{modelId}/analyze?includeTextDetails=true" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" -d "{ 'source': '{SAS URL}' } "
```

**Operation-Location** 헤더를 포함하는 `202 (Success)` 응답을 받게 됩니다. 이 헤더의 값에는 분석 작업의 결과를 추적하는 데 사용할 결과 ID가 포함됩니다.

_https://<span></span>cognitiveservice/formrecognizer/v2.1/custom/models/{modelId}/analyzeResults/ **{resultId}**_

다음 예제에서 URL의 일부로 `analyzeResults/` 뒤에 있는 문자열이 결과 ID입니다.

```console
https://cognitiveservice/formrecognizer/v2/layout/analyzeResults/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

다음 단계를 위해 이 결과 ID를 저장합니다.

### <a name="get-the-analyze-results"></a>분석 결과 가져오기

**[양식 분석 결과 가져오기](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/GetAnalyzeFormResult)** API를 사용하여 분석 작업의 결과를 쿼리합니다.

1. `{endpoint}`를 Form Recognizer 구독 키에서 가져온 엔드포인트로 바꿉니다. Form Recognizer 리소스 **개요** 탭에서 찾을 수 있습니다.
1. `{result ID}`를 이전 섹션에서 받은 ID로 바꿉니다.
1. `{subscription key}`를 구독 키로 바꿉니다.

#### <a name="request"></a>요청

```bash
curl -X GET "https://{endpoint}/formrecognizer/v2.1/custom/models/{modelId}/analyzeResults/{resultId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

다음 형식의 JSON 본문이 포함된 `200 (Success)` 응답을 받게 됩니다. 출력은 편의상 간소화되었습니다. 아래쪽에 `"status"` 필드를 유의하세요. 분석 작업이 완료되면 여기에 `"succeeded"` 값이 포함됩니다. 분석 작업이 완료되지 않은 경우 명령을 다시 실행하여 서비스를 다시 쿼리해야 합니다. 호출 간에 1초 이상의 간격을 사용하는 것이 좋습니다.

레이블을 사용하지 않고 학습된 사용자 지정 모델에서는 키/값 쌍 연결 및 표가 JSON 출력의 `"pageResults"` 노드에 있습니다. 레이블을 사용하여 학습된 사용자 지정 모델에서는 키/값 쌍 연결이 `"documentResults"` 노드에 있습니다. *includeTextDetails* URL 매개 변수를 통해 일반 텍스트 추출도 지정한 경우 `"readResults"` 노드에는 문서에 있는 모든 텍스트의 콘텐츠와 위치가 표시됩니다.

이 샘플 JSON 출력은 편의상 간소화되었습니다. [GitHub의 전체 샘플 출력](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/analyze-result-invoice-6.pdf.json)을 참조하세요.

#### <a name="response-body"></a>응답 본문

```JSON
{
  "status": "succeeded",
  "createdDateTime": "2020-08-21T01:13:28Z",
  "lastUpdatedDateTime": "2020-08-21T01:13:42Z",
  "analyzeResult": {
    "version": "2.1.0",
    "readResults": [
      {
        "page": 1,
        "angle": 0,
        "width": 8.5,
        "height": 11,
        "unit": "inch",
        "lines": [
          {
            "text": "Project Statement",
            "boundingBox": [
              5.0444,
              0.3613,
              8.0917,
              0.3613,
              8.0917,
              0.6718,
              5.0444,
              0.6718
            ],
            "words": [
              {
                "text": "Project",
                "boundingBox": [
                  5.0444,
                  0.3587,
                  6.2264,
                  0.3587,
                  6.2264,
                  0.708,
                  5.0444,
                  0.708
                ]
              },
              {
                "text": "Statement",
                "boundingBox": [
                  6.3361,
                  0.3635,
                  8.0917,
                  0.3635,
                  8.0917,
                  0.6396,
                  6.3361,
                  0.6396
                ]
              }
            ]
          },
          ...
        ]
      }
    ],
    "pageResults": [
      {
        "page": 1,
        "keyValuePairs": [
          {
            "key": {
              "text": "Date:",
              "boundingBox": [
                6.9833,
                1.0615,
                7.3333,
                1.0615,
                7.3333,
                1.1649,
                6.9833,
                1.1649
              ],
              "elements": [
                "#/readResults/0/lines/2/words/0"
              ]
            },
            "value": {
              "text": "9/10/2020",
              "boundingBox": [
                7.3833,
                1.0802,
                7.925,
                1.0802,
                7.925,
                1.174,
                7.3833,
                1.174
              ],
              "elements": [
                "#/readResults/0/lines/3/words/0"
              ]
            },
            "confidence": 1
          },
          ...
        ],
        "tables": [
          {
            "rows": 5,
            "columns": 5,
            "cells": [
              {
                "text": "Training Date",
                "rowIndex": 0,
                "columnIndex": 0,
                "boundingBox": [
                  0.6944,
                  4.2779,
                  1.5625,
                  4.2779,
                  1.5625,
                  4.4005,
                  0.6944,
                  4.4005
                ],
                "confidence": 1,
                "rowSpan": 1,
                "columnSpan": 1,
                "elements": [
                  "#/readResults/0/lines/15/words/0",
                  "#/readResults/0/lines/15/words/1"
                ],
                "isHeader": true,
                "isFooter": false
              },
              ...
            ]
          }
        ],
        "clusterId": 0
      }
    ],
    "documentResults": [],
    "errors": []
  }
}
```

### <a name="improve-results"></a>결과 개선

[!INCLUDE [improve results](../../includes/improve-results-unlabeled.md)]

## <a name="manage-custom-models"></a>사용자 지정 모델 관리

### <a name="get-a-list-of-custom-models"></a>사용자 지정 모델 목록 가져오기

다음 명령에 **[사용자 지정 모델 나열](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/GetCustomModels)** API를 사용하여 구독에 속하는 모든 사용자 지정 모델의 목록을 반환합니다.

1. `{endpoint}`를 Form Recognizer 구독에서 얻은 엔드포인트로 바꿉니다.
1. `{subscription key}`를 이전 단계에서 복사한 구독 키로 바꿉니다.

#### <a name="request"></a>요청

```bash
curl -v -X GET "https://{endpoint}/formrecognizer/v2.1/custom/models?op=full"
-H "Ocp-Apim-Subscription-Key: {subscription key}"
```

#### <a name="response-body"></a>응답 본문

다음과 같은 JSON 데이터가 포함된 `200` 성공 응답을 받게 됩니다. `"modelList"` 요소는 생성된 모든 모델 및 해당 정보를 포함합니다.

```json
{
  "summary": {
    "count": 0,
    "limit": 0,
    "lastUpdatedDateTime": "string"
  },
  "modelList": [
    {
      "modelId": "string",
      "status": "creating",
      "createdDateTime": "string",
      "lastUpdatedDateTime": "string"
    }
  ],
  "nextLink": "string"
}
```

### <a name="get-a-specific-model"></a>특정 모델 가져오기

특정 사용자 지정 모델에 대한 자세한 정보를 검색하려면 다음 명령에 **[사용자 지정 모델 가져오기](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/GetCustomModel)** API를 사용합니다.

1. `{endpoint}`를 Form Recognizer 구독에서 얻은 엔드포인트로 바꿉니다.
1. `{subscription key}`를 이전 단계에서 복사한 구독 키로 바꿉니다.
1. `{modelId}`는 조회하려는 사용자 지정 모델의 ID로 바꿉니다.

### <a name="request"></a>요청

```bash
curl -v -X GET "https://{endpoint}/formrecognizer/v2.1/custom/models/{modelId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

#### <a name="request-body"></a>요청 본문

다음과 같은 JSON 데이터가 포함된 `200` 성공 응답을 받게 됩니다.

```json
{
  "modelInfo": {
    "modelId": "string",
    "status": "creating",
    "createdDateTime": "string",
    "lastUpdatedDateTime": "string"
  },
  "keys": {
    "clusters": {}
  },
  "trainResult": {
    "trainingDocuments": [
      {
        "documentName": "string",
        "pages": 0,
        "errors": [
          "string"
        ],
        "status": "succeeded"
      }
    ],
    "fields": [
      {
        "fieldName": "string",
        "accuracy": 0.0
      }
    ],
    "averageModelAccuracy": 0.0,
    "errors": [
      {
        "message": "string"
      }
    ]
  }
}
```

### <a name="delete-a-model-from-the-resource-account"></a>리소스 계정에서 모델 삭제

해당 ID를 참조하여 계정에서 모델을 삭제할 수도 있습니다. 이 명령은 **[사용자 지정 모델 삭제](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/DeleteCustomModel)** API를 호출하여 이전 섹션에서 사용된 모델을 삭제합니다.

1. `{endpoint}`를 Form Recognizer 구독에서 얻은 엔드포인트로 바꿉니다.
1. `{subscription key}`를 이전 단계에서 복사한 구독 키로 바꿉니다.
1. `{modelId}`는 조회하려는 사용자 지정 모델의 ID로 바꿉니다.

### <a name="request"></a>요청

```bash
curl -v -X DELETE "https://{endpoint}/formrecognizer/v2.1/custom/models/{modelId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

모델이 삭제 대상으로 표시되었음을 나타내는 `204` 성공 응답을 받게 됩니다. 모델 아티팩트는 48시간 이내에 제거됩니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Form Recognizer REST API를 사용하여 다양한 방식으로 양식을 분석했습니다. 다음으로, 참조 설명서를 탐색하여 Form Recognizer API에 관해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [REST API 참조 설명서](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeWithCustomForm)