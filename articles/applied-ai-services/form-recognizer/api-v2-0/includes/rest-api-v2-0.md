---
title: '참조: Form Recognizer 2.0 REST API'
description: Form Recognizer REST API를 사용하여 사용자 지정 문서에서 키/값 쌍 및 표 데이터를 추출하는 양식 처리 앱을 만듭니다.
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 05/25/2021
ms.author: lajanuar
ms.openlocfilehash: 482710246b8d1755d63568f1cdc897e73e50c50c
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130265881"
---
<!-- markdownlint-disable MD001 -->
<!-- markdownlint-disable MD024 -->
<!-- markdownlint-disable MD033 -->
<!-- markdownlint-disable MD034 -->
> [!NOTE]
> 이 가이드에서는 cURL을 사용하여 REST API 호출을 실행합니다.

|[Form Recognizer REST API](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm)|[Azure REST API 참조](/rest/api/azure/)|

## <a name="prerequisites"></a>필수 구성 요소

* [cURL](https://curl.haxx.se/windows/) 설치
* [PowerShell 버전 6.0 이상](/powershell/scripting/install/installing-powershell-core-on-windows) 또는 유사한 명령줄 애플리케이션.
* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/cognitive-services/)
* 학습 데이터 세트가 포함된 Azure Storage Blob. 학습 데이터 세트를 결합하는 옵션 및 팁에 대한 자세한 내용은 [사용자 지정 모델에 대한 학습 데이터 세트 빌드](../../build-training-data-set.md)를 참조하세요. 이 빠른 시작에서는 [샘플 데이터 세트](https://go.microsoft.com/fwlink/?linkid=2090451)의 **Train** 폴더에 있는 파일을 사용할 수 있습니다(*sample_data.zip* 다운로드 및 추출).
* Azure 구독을 보유한 후에는 Azure Portal에서 <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title="Form Recognizer 리소스 만들기"  target="_blank">Form Recognizer 리소스 </a>를 만들어 키와 엔드포인트를 가져옵니다. 배포 후 **리소스로 이동** 을 클릭합니다.
  * 애플리케이션을 Form Recognizer API에 연결하려면 만든 리소스의 키와 엔드포인트가 필요합니다. 이 빠른 시작의 뒷부분에 나오는 코드에 키와 엔드포인트를 붙여넣습니다.
  * 평가판 가격 책정 계층(`F0`)을 통해 서비스를 사용해보고, 나중에 프로덕션용 유료 계층으로 업그레이드할 수 있습니다.
* 영수증 이미지의 URL. 이 빠른 시작에서는 [샘플 이미지](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/contoso-allinone.jpg)를 사용할 수 있습니다.
* 명함 이미지의 URL입니다. 이 빠른 시작에서는 [샘플 이미지](https://raw.githubusercontent.com/Azure/azure-sdk-for-python/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms/business_cards/business-card-english.jpg)를 사용할 수 있습니다.
* 청구서 이미지의 URL입니다. 이 빠른 시작에서는 [샘플 문서](https://raw.githubusercontent.com/Azure/azure-sdk-for-python/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms/forms/Invoice_1.pdf)를 사용할 수 있습니다.

## <a name="code-examples"></a>코드 예제

이러한 코드 조각에서는 Form Recognizer REST API를 사용하여 다음 작업을 수행하는 방법을 보여줍니다.
<!-- markdownlint-disable MD001 -->

* [레이아웃 분석](#analyze-layout)
* [영수증 분석](#analyze-receipts)
* [사용자 지정 모델 학습](#train-a-custom-model)
* [사용자 지정 모델을 사용하여 양식 분석](#analyze-forms-with-a-custom-model)
* [사용자 지정 모델 관리](#manage-custom-models)



## <a name="analyze-layout"></a>레이아웃 분석

Form Recognizer를 사용하면 모델을 학습시킬 필요 없이 문서의 표, 선택 표시, 텍스트 및 구조체를 분석하고 추출할 수 있습니다. 레이아웃 추출에 대한 자세한 내용은 [레이아웃 개념 가이드](../../concept-layout.md)를 참조하세요. 명령을 실행하기 전에 다음과 같이 변경합니다.

1. `{Endpoint}`를 Form Recognizer 구독에서 얻은 엔드포인트로 바꿉니다.
1. `{subscription key}`를 이전 단계에서 복사한 구독 키로 바꿉니다.
1. `\"{your-document-url}`을 예제 URL 중 하나로 바꿉니다.

```bash
curl -v -i POST "https://{Endpoint}/formrecognizer/v2.0/layout/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{'source': '{your-document-url}'}"
```

**Operation-Location** 헤더를 포함하는 `202 (Success)` 응답을 받게 됩니다. 이 헤더의 값에는 비동기 작업 상태를 쿼리하고 결과를 가져오는 데 사용할 수 있는 작업 ID가 포함됩니다. 다음 예제에서 `analyzeResults/` 다음 문자열은 작업 ID입니다.

```console
https://cognitiveservice/formrecognizer/v2/layout/analyzeResults/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

### <a name="get-layout-results"></a>레이아웃 결과 가져오기

**[https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeLayoutAsync)** API를 호출한 후에는 **[https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/GetAnalyzeLayoutResult)** API를 호출하여 작업 상태와 추출된 데이터를 가져옵니다. 명령을 실행하기 전에 다음과 같이 변경합니다.

1. `{Endpoint}`를 Form Recognizer 구독에서 얻은 엔드포인트로 바꿉니다.
1. `{subscription key}`를 이전 단계에서 복사한 구독 키로 바꿉니다.
1. `{resultId}`를 이전 단계의 작업 ID로 바꿉니다.
<!-- markdownlint-disable MD024 -->

```bash
curl -v -X GET "https://{Endpoint}/formrecognizer/v2.0/layout/analyzeResults/{resultId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="examine-the-results"></a>결과 검사

JSON 콘텐츠가 포함된 `200 (success)` 응답을 받게 됩니다.

다음 청구서 이미지와 해당 JSON 출력을 참조하세요.

* `"readResults"` 노드에는 페이지의 각 경계 상자 배치가 있는 모든 텍스트 줄이 포함되어 있습니다.
* `"pageResults"` 섹션에는 추출된 표가 포함됩니다. 각 표에서 텍스트, 행 및 열 인덱스, 행 및 열 스패닝, 경계 상자 등이 추출됩니다.

:::image type="content" source="../../media/contoso-invoice.png" alt-text="테이블이 포함된 Contoso 프로젝트 문 문서.":::

이 출력은 편의상 간소화되었습니다. [GitHub의 전체 샘플 출력](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/sample-layout-output.json)을 참조하세요.

```json
{
    "status": "succeeded",
    "createdDateTime": "2020-08-20T20:36:52Z",
    "lastUpdatedDateTime": "2020-08-20T20:36:58Z",
    "analyzeResult": {
        "version": "2.0.0",
        "readResults": [
            {
                "page": 1,
                "language": "en",
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
                                    "#/readResults/0/lines/14/words/0",
                                    "#/readResults/0/lines/14/words/1"
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

이 섹션에서는 사전 학습된 영수증 모델을 사용하여 미국 영수증의 공통 필드를 분석하고 추출하는 방법을 보여 줍니다. 영수증 분석에 대한 자세한 내용은 [영수증 개념 가이드](../../concept-receipt.md)를 참조하세요. 영수증 분석을 시작하기 위해, 아래 cURL 명령을 사용하여 **[Analyze Receipt](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeReceiptAsync)** API를 호출합니다. 명령을 실행하기 전에 다음과 같이 변경합니다.

1. `{Endpoint}`를 Form Recognizer 구독에서 얻은 엔드포인트로 바꿉니다.
1. `{your receipt URL}`을 영수증 이미지의 URL 주소로 바꿉니다.
1. `{subscription key>`를 이전 단계에서 복사한 구독 키로 바꿉니다.

```bash
curl -i -X POST "https://{Endpoint}/formrecognizer/v2.0/prebuilt/receipt/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ 'source': '{your receipt URL}'}"
```

**Operation-Location** 헤더를 포함하는 `202 (Success)` 응답을 받게 됩니다. 이 헤더의 값에는 비동기 작업 상태를 쿼리하고 결과를 가져오는 데 사용할 수 있는 작업 ID가 포함됩니다. 다음 예제에서 `operations/` 다음 문자열은 작업 ID입니다.

```console
https://cognitiveservice/formrecognizer/v2.0/prebuilt/receipt/operations/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

### <a name="get-the-receipt-results"></a>영수증 결과 가져오기

**Analyze Receipt** API를 호출한 후 **[Get Analyze Receipt Result](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/GetAnalyzeReceiptResult)** API를 호출하여 작업의 상태와 추출된 데이터를 가져옵니다. 명령을 실행하기 전에 다음과 같이 변경합니다.

1. `{Endpoint}`를 Form Recognizer 구독 키에서 얻은 엔드포인트로 바꿉니다. Form Recognizer 리소스 **개요** 탭에서 찾을 수 있습니다.
1. `{operationId}`를 이전 단계의 작업 ID로 바꿉니다.
1. `{subscription key}`를 구독 키로 바꿉니다.

```bash
curl -X GET "https://{Endpoint}/formrecognizer/v2.0/prebuilt/receipt/analyzeResults/{operationId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="examine-the-response"></a>응답 검사

JSON 출력이 포함된 `200 (Success)` 응답을 받게 됩니다. 작업의 상태를 나타내는 첫 번째 필드, `"status"`입니다. 작업이 완료되지 않는 경우 `"status"`의 값은 `"running"` 또는 `"notStarted"`가 되며, 수동으로 또는 스크립트를 통해 API를 다시 호출해야 합니다. 호출 간에 1초 이상의 간격을 사용하는 것이 좋습니다.

`"readResults"` 노드에는 인식된 모든 텍스트가 포함됩니다(선택적 *includeTextDetails* 매개 변수를 `true`로 설정한 경우). 텍스트는 페이지별로, 그 다음에는 줄별로, 그 다음에는 개별 단어별로 정리됩니다. `"documentResults"` 노드에는 모델이 검색한 수신 특정 값이 포함됩니다. 여기서는 세금, 합계, 판매자 주소 등과 같은 유용한 키/값 쌍을 찾을 수 있습니다.

다음 영수증 이미지와 해당 JSON 출력을 참조하세요.

![Contoso 매장의 영수증](../../media/contoso-allinone.jpg)

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

## <a name="train-a-custom-model"></a>사용자 지정 모델 학습

사용자 지정 모델을 학습시키려면 Azure Storage Blob의 학습 데이터 세트가 필요합니다. 동일한 형식/구조의 채워진 양식(PDF 문서 및/또는 이미지)이 5개 이상 필요합니다. 학습 데이터를 결합하는 옵션 및 팁에 대한 자세한 내용은 [사용자 지정 모델에 대한 학습 데이터 집합 빌드](../../build-training-data-set.md)를 참조하세요.

레이블이 지정된 데이터를 사용하지 않는 학습은 기본 작업이므로 더 간단합니다. 또는 학습 데이터의 일부 또는 전체에 레이블을 미리 수동으로 지정할 수 있습니다. 이는 좀 더 복잡한 프로세스지만 학습된 모델을 더 효율적으로 만듭니다.

> [!NOTE]
> [Form Recognizer 샘플 레이블 지정 도구](../../label-tool.md)와 같은 그래픽 사용자 인터페이스를 사용하여 모델을 학습시킬 수도 있습니다.

### <a name="train-a-model-without-labels"></a>레이블 없이 모델 학습

Azure Blob 컨테이너의 문서를 사용하여 Form Recognizer 모델을 학습시키려면 다음 cURL 명령을 실행하여 **[Train Custom Model]https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/TrainCustomModelAsync)** API를 호출합니다. 명령을 실행하기 전에 다음과 같이 변경합니다.

1. `{Endpoint}`를 Form Recognizer 구독에서 얻은 엔드포인트로 바꿉니다.
1. `{subscription key}`를 이전 단계에서 복사한 구독 키로 바꿉니다.
1. `{SAS URL}`을 Azure Blob Storage 컨테이너의 SAS(공유 액세스 서명) URL로 바꿉니다. 

[!INCLUDE [get SAS URL](sas-instruction.md)]

   :::image type="content" source="../../media/quickstarts/get-sas-url.png" alt-text="SAS URL 검색":::

```bash
curl -i -X POST "https://{Endpoint}/formrecognizer/v2.0/custom/models" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ 'source': '{SAS URL}'}"
```

**위치** 헤더가 포함된 `201 (Success)` 응답을 받게 됩니다. 이 헤더의 값은 학습 중인 새 모델의 ID입니다.

### <a name="train-a-model-with-labels"></a>레이블을 사용하여 모델 학습

레이블을 사용하여 학습하려면 학습 문서와 별도로 Blob 스토리지 컨테이너에 특별한 레이블 정보 파일(`\<filename\>.pdf.labels.json`)이 있어야 합니다. [Form Recognizer 샘플 레이블 지정 도구](../../label-tool.md)는 이러한 레이블 파일을 만드는 데 도움이 되는 UI를 제공합니다. 이 파일을 만든 후에는 JSON 본문에 `"useLabelFile"` 매개 변수를 `true`로 설정하여 **[사용자 지정 모델 학습](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/TrainCustomModelAsync)** API를 호출할 수 있습니다.

명령을 실행하기 전에 다음과 같이 변경합니다.

1. `{Endpoint}`를 Form Recognizer 구독에서 얻은 엔드포인트로 바꿉니다.
1. `{subscription key}`를 이전 단계에서 복사한 구독 키로 바꿉니다.
1. `{SAS URL}`을 Azure Blob Storage 컨테이너의 SAS(공유 액세스 서명) URL로 바꿉니다. [!INCLUDE [get SAS URL](sas-instruction.md)]

   :::image type="content" source="../../media/quickstarts/get-sas-url.png" alt-text="SAS URL 검색":::

```bash
curl -i -X POST "https://{Endpoint}/formrecognizer/v2.0/custom/models" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ 'source': '{SAS URL}', 'useLabelFile':true }"
```

**위치** 헤더가 포함된 `201 (Success)` 응답을 받게 됩니다. 이 헤더의 값은 학습 중인 새 모델의 ID입니다.

### <a name="get-training-results"></a>학습 결과 가져오기

학습 작업을 시작한 후에는 새 작업인 **[사용자 지정 모델 가져오기](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/GetCustomModel)** 를 사용하여 학습 상태를 확인합니다. 모델 ID를 이 API 호출에 전달하여 학습 상태를 확인합니다.

1. `{Endpoint}`를 Form Recognizer 구독 키에서 얻은 엔드포인트로 바꿉니다.
1. `{subscription key}`를 구독 키로 바꿉니다.
1. `{model ID}`를 이전 단계에서 받은 모델 ID로 바꿉니다.

```bash
curl -X GET "https://{Endpoint}/formrecognizer/v2.0/custom/models/{model ID}" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

다음 형식의 JSON 본문이 포함된 `200 (Success)` 응답을 받게 됩니다. `"status"` 필드를 유의하세요. 학습이 완료되면 `"ready"` 값이 포함됩니다. 모델이 학습을 완료하지 않은 경우 명령을 다시 실행하여 서비스를 다시 쿼리해야 합니다. 호출 간에 1초 이상의 간격을 사용하는 것이 좋습니다.

`"modelId"` 필드에는 학습 중인 모델의 ID가 포함됩니다. 이 값은 다음 단계에서 필요합니다.

```json
{
  "modelInfo":{
    "status":"ready",
    "createdDateTime":"2019-10-08T10:20:31.957784",
    "lastUpdatedDateTime":"2019-10-08T14:20:41+00:00",
    "modelId":"1cfb372bab404ba3aa59481ab2c63da5"
  },
  "trainResult":{
    "trainingDocuments":[
      {
        "documentName":"invoices\\Invoice_1.pdf",
        "pages":1,
        "errors":[

        ],
        "status":"succeeded"
      },
      {
        "documentName":"invoices\\Invoice_2.pdf",
        "pages":1,
        "errors":[

        ],
        "status":"succeeded"
      },
      {
        "documentName":"invoices\\Invoice_3.pdf",
        "pages":1,
        "errors":[

        ],
        "status":"succeeded"
      },
      {
        "documentName":"invoices\\Invoice_4.pdf",
        "pages":1,
        "errors":[

        ],
        "status":"succeeded"
      },
      {
        "documentName":"invoices\\Invoice_5.pdf",
        "pages":1,
        "errors":[

        ],
        "status":"succeeded"
      }
    ],
    "errors":[

    ]
  },
  "keys":{
    "0":[
      "Address:",
      "Invoice For:",
      "Microsoft",
      "Page"
    ]
  }
}
```

## <a name="analyze-forms-with-a-custom-model"></a>사용자 지정 모델을 사용하여 양식 분석

다음으로, 새로 학습된 모델을 사용하여 문서를 분석하고 키-값 쌍 및 테이블을 추출합니다. 다음 cURL 명령을 실행하여 **[Analyze Form](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm)** API를 호출합니다. 명령을 실행하기 전에 다음과 같이 변경합니다.

1. `{Endpoint}`를 Form Recognizer 구독 키에서 가져온 엔드포인트로 바꿉니다. Form Recognizer 리소스 **개요** 탭에서 찾을 수 있습니다.
1. `{model ID}`를 이전 섹션에서 받은 모델 ID로 바꿉니다.
1. `{SAS URL}`을 Azure 스토리지의 파일을 잇는 SAS URL로 바꿉니다. 학습 섹션의 단계를 따르되 전체 Blob 컨테이너에 대한 SAS URL을 가져오는 대신 분석하려는 특정 파일에 대한 SAS URL을 가져옵니다.
1. `{subscription key}`를 구독 키로 바꿉니다.

```bash
curl -v "https://{Endpoint}/formrecognizer/v2.0/custom/models/{model ID}/analyze?includeTextDetails=true" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" -d "{ 'source': '{SAS URL}' } "
```

**Operation-Location** 헤더를 포함하는 `202 (Success)` 응답을 받게 됩니다. 이 헤더의 값은 분석 작업의 결과를 추적하는 데 사용하는 결과 ID를 포함합니다. 다음 단계를 위해 이 결과 ID를 저장합니다.

### <a name="get-the-analyze-results"></a>분석 결과 가져오기

**[양식 분석 결과 가져오기](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/GetAnalyzeFormResult)** API를 사용하여 분석 작업의 결과를 쿼리합니다.

1. `{Endpoint}`를 Form Recognizer 구독 키에서 가져온 엔드포인트로 바꿉니다. Form Recognizer 리소스 **개요** 탭에서 찾을 수 있습니다.
1. `{result ID}`를 이전 섹션에서 받은 ID로 바꿉니다.
1. `{subscription key}`를 구독 키로 바꿉니다.

```bash
curl -X GET "https://{Endpoint}/formrecognizer/v2.0/custom/models/{model ID}/analyzeResults/{result ID}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

다음 형식의 JSON 본문이 포함된 `200 (Success)` 응답을 받게 됩니다. 출력은 편의상 간소화되었습니다. 아래쪽에 `"status"` 필드를 유의하세요. 분석 작업이 완료되면 여기에 `"succeeded"` 값이 포함됩니다. 분석 작업이 완료되지 않은 경우 명령을 다시 실행하여 서비스를 다시 쿼리해야 합니다. 호출 간에 1초 이상의 간격을 사용하는 것이 좋습니다.

레이블을 사용하지 않고 학습된 사용자 지정 모델에서는 키/값 쌍 연결 및 표가 JSON 출력의 `"pageResults"` 노드에 있습니다. 레이블을 사용하여 학습된 사용자 지정 모델에서는 키/값 쌍 연결이 `"documentResults"` 노드에 있습니다. *includeTextDetails* URL 매개 변수를 통해 일반 텍스트 추출도 지정한 경우 `"readResults"` 노드에는 문서에 있는 모든 텍스트의 콘텐츠와 위치가 표시됩니다.

이 샘플 JSON 출력은 편의상 간소화되었습니다. [GitHub의 전체 샘플 출력](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/analyze-result-invoice-6.pdf.json)을 참조하세요.

```JSON
{
  "status": "succeeded",
  "createdDateTime": "2020-08-21T00:46:25Z",
  "lastUpdatedDateTime": "2020-08-21T00:46:32Z",
  "analyzeResult": {
    "version": "2.0.0",
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
              5.0153,
              0.275,
              8.0944,
              0.275,
              8.0944,
              0.7125,
              5.0153,
              0.7125
            ],
            "words": [
              {
                "text": "Project",
                "boundingBox": [
                  5.0153,
                  0.275,
                  6.2278,
                  0.275,
                  6.2278,
                  0.7125,
                  5.0153,
                  0.7125
                ]
              },
              {
                "text": "Statement",
                "boundingBox": [
                  6.3292,
                  0.275,
                  8.0944,
                  0.275,
                  8.0944,
                  0.7125,
                  6.3292,
                  0.7125
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
                6.9722,
                1.0264,
                7.3417,
                1.0264,
                7.3417,
                1.1931,
                6.9722,
                1.1931
              ],
              "elements": [
                "#/readResults/0/lines/2/words/0"
              ]
            },
            "confidence": 1
          },
         ...
        ],
        "tables": [
          {
            "rows": 4,
            "columns": 5,
            "cells": [
              {
                "text": "Training Date",
                "rowIndex": 0,
                "columnIndex": 0,
                "boundingBox": [
                  0.6931,
                  4.2444,
                  1.5681,
                  4.2444,
                  1.5681,
                  4.4125,
                  0.6931,
                  4.4125
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

[!INCLUDE [improve results](improve-result-unlabeled.md)]

## <a name="manage-custom-models"></a>사용자 지정 모델 관리

### <a name="get-a-list-of-custom-models"></a>사용자 지정 모델 목록 가져오기

다음 명령에 **[사용자 지정 모델 나열](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/GetCustomModels)** API를 사용하여 구독에 속하는 모든 사용자 지정 모델의 목록을 반환합니다.

1. `{Endpoint}`를 Form Recognizer 구독에서 얻은 엔드포인트로 바꿉니다.
1. `{subscription key}`를 이전 단계에서 복사한 구독 키로 바꿉니다.

```bash
curl -v -X GET "https://{Endpoint}/formrecognizer/v2.0/custom/models?op=full"
-H "Ocp-Apim-Subscription-Key: {subscription key}"
```

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

특정 사용자 지정 모델에 대한 자세한 정보를 검색하려면 다음 명령에 **[사용자 지정 모델 가져오기](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/GetCustomModel)** API를 사용합니다.

1. `{Endpoint}`를 Form Recognizer 구독에서 얻은 엔드포인트로 바꿉니다.
1. `{subscription key}`를 이전 단계에서 복사한 구독 키로 바꿉니다.
1. `{modelId}`는 조회하려는 사용자 지정 모델의 ID로 바꿉니다.

```bash
curl -v -X GET "https://{Endpoint}/formrecognizer/v2.0/custom/models/{modelId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

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

해당 ID를 참조하여 계정에서 모델을 삭제할 수도 있습니다. 이 명령은 **[사용자 지정 모델 삭제](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/DeleteCustomModel)** API를 호출하여 이전 섹션에서 사용된 모델을 삭제합니다.

1. `{Endpoint}`를 Form Recognizer 구독에서 얻은 엔드포인트로 바꿉니다.
1. `{subscription key}`를 이전 단계에서 복사한 구독 키로 바꿉니다.
1. `{modelId}`는 조회하려는 사용자 지정 모델의 ID로 바꿉니다.

```bash
curl -v -X DELETE "https://{Endpoint}/formrecognizer/v2.0/custom/models/{modelId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

모델이 삭제 대상으로 표시되었음을 나타내는 `204` 성공 응답을 받게 됩니다. 모델 아티팩트는 48시간 이내에 제거됩니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Form Recognizer REST API를 사용하여 다양한 방식으로 모델을 학습시키고 양식을 분석했습니다. 다음으로, 참조 설명서를 통해 Form Recognizer API에 대해 자세히 알아보세요.

> [!div class="nextstepaction"]
> [REST API 참조 설명서](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm)