---
title: 영수증 - Form Recognizer
titleSuffix: Azure Applied AI Services
description: Form Recognizer API 사용 및 제한으로 영수증 분석과 관련된 개념을 알아봅니다.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 08/09/2021
ms.author: lajanuar
ms.openlocfilehash: 1efe839a1344f28d2f3033d67de2dd7035320f08
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122568001"
---
# <a name="form-recognizer-prebuilt-receipt-model"></a>Form Recognizer 미리 빌드된 영수증 모델

많은 기업과 개인은 여전히 판매 영수증에서 수동으로 추출된 데이터를 사용합니다. 이러한 영수증에서 데이터를 자동으로 추출하는 것은 복잡할 수 있습니다. 영수증은 구겨져서 내용을 이해하기 어려울 수 있고, 손으로 필기된 부분이 있을 수 있으며, 품질이 낮은 스마트폰 이미지일 수 있습니다. 또한 영수증의 템플릿 및 필드는 시장, 지역 및 판매자에 따라 크게 다를 수 있습니다. 이러한 데이터 추출 및 현장 검색과 관련된 문제는 영수증 처리를 고유한 문제로 만듭니다.

Azure Form Recognizer는 미리 빌드된 영수증 모델을 사용하여 판매 영수증에서 정보를 분석하고 추출할 수 있습니다. 또한 강력한 [OCR(광학 문자 인식)](../../cognitive-services/computer-vision/overview-ocr.md) 기능을 딥 러닝 모델과 결합하여 영어로 작성된 판매자 이름, 판매자 전화 번호, 거래 날짜, 거래 총액, 영수증 내용 등에서 주요 정보를 추출합니다.

## <a name="customer-scenarios"></a>고객 시나리오

### <a name="business-expense-reporting"></a>비즈니스 경비 보고

비즈니스 경비를 확인하는 데에는 종종 영수증 이미지의 데이터를 수동으로 입력하는 시간이 필요합니다. Receipt API를 사용하면 추출된 필드를 사용하여 이 프로세스를 부분적으로 자동화하고 영수증을 신속하게 분석할 수 있습니다.

Receipt API는 추출된 필드 값을 여러 가지 방법으로 사용할 수 있도록 하는 간단한 JSON 출력입니다. 내부 비용 애플리케이션과 통합하여 경비 보고서를 미리 작성합니다. 이 시나리오에 대한 자세한 내용은 Acumatica에서 Receipt API를 활용하여 [덜 힘든 방식으로 비용을 보고](https://customers.microsoft.com/story/762684-acumatica-partner-professional-services-azure)하는 프로세스를 참조하세요.

### <a name="auditing-and-accounting"></a>감사 및 회계

Receipt API 출력은 지출 보고 및 보상 프로세스의 다양한 지점에서 많은 비용을 분석하는 데 사용할 수도 있습니다. 영수증을 처리하여 수동 감사 또는 빠른 승인에 대비해 심사해 볼 수 있습니다.

Receipt 출력은 업무용 또는 개인용 사용을 위한 일반 부기에도 유용합니다. Receipt API를 사용하여 원시 영수증 이미지/PDF 데이터를 작업 가능한 디지털 출력으로 변환합니다.

### <a name="consumer-behavior"></a>소비자 행동

영수증에는 소비자 동작 및 쇼핑 추세 분석에 사용할 수 있는 유용한 데이터가 포함되어 있습니다.

또한 Receipt API는 [AI Builder 영수증 처리 기능](/ai-builder/prebuilt-receipt-processing)을 향상시킵니다.

## <a name="try-it"></a>시도

Form Recognizer 영수증 서비스를 사용해보려면 다음과 같은 온라인 샘플 UI 도구로 이동합니다.

> [!div class="nextstepaction"]
> [영수증 모델 사용해 보기](https://aka.ms/fott-2.1-ga "미리 빌드된 모델로 시작하여 영수증에서 데이터를 추출합니다.")

## <a name="what-does-the-receipt-service-do"></a>영수증 서비스에서 수행하는 작업은 무엇인가요?

미리 빌드된 영수증 서비스는 판매 영수증의 콘텐츠&mdash;식당, 소매점 또는 식료품 스토어에서 일반적으로 받게 되는 영수증의 유형을 추출합니다.

![샘플 영수증](./media/receipts-example.jpg)

### <a name="fields-extracted"></a>추출된 필드

|이름| 유형 | 설명 | 텍스트 | 값(표준화된 출력) |
|:-----|:----|:----|:----| :----|
| ReceiptType | 문자열 | 판매 영수증의 유형 |  | 항목화 |
| MerchantName | 문자열 | 영수증을 발급한 판매자의 이름 | Contoso |  |
| MerchantPhoneNumber | phoneNumber | 판매자 전화 번호 나열 | 987-654-3210 | +19876543210 |
| MerchantAddress | 문자열 | 판매자의 주소 나열 | 123 Main St Redmond WA 98052 |  |
| TransactionDate | date | 영수증이 발급된 날짜 | 2019년 6월 6일 | 2019년 6월 26일  |
| TransactionTime | time | 영수증이 발급된 시간 | 오후 4시 49분 | 16:49:00  |
| 합계 | number | 영수증의 총 트랜잭션 합계 | $14.34 | 14.34 |
| 소계 | number | 영수증의 소계(보통 세금이 적용되기 전) | $12.34 | 12.34 |
| 세금 | number | 영수증에 대한 세금(보통 판매 세금 또는 이에 상응하는 금액) | $2.00 | 2.00 |
| 팁 | number | 구매자에 의해 포함된 팁 | $1.00 | 1.00 |
| Items | 개체의 배열 | 추출된 품목(이름, 수량, 단가 및 총 가격) | |
| 이름 | string | 항목 이름 | Surface Pro 6 | |
| 수량 | number | 각 항목의 수량 | 1 | 1 |
| 가격 | number | 각 항목 단위의 개별 가격 | $999.00 | 999.00 |
| 총 가격 | number | 품목에 대한 총 가격 | $999.00 | 999.00 |

### <a name="additional-features"></a>추가 기능

또한 Receipt API는 다음 정보를 반환합니다.

* 필드 신뢰 수준(각 필드는 연결된 신뢰도의 값을 반환)
* OCR 원시 텍스트(영수증 전체에 대해 OCR 추출된 텍스트 출력)
* 각 값, 줄 및 단어에 대한 경계 상자

## <a name="input-requirements"></a>입력 요구 사항

[!INCLUDE [input requirements](./includes/input-requirements-receipts.md)]

## <a name="supported-locales"></a>지원되는 로캘

* **미리 빌드된 영수증 v2.1** 은 **en-au**, **en-ca**, **en-gb**, **en-in**, **en-us** 영어 로캘의 판매 영수증을 지원합니다.

  > [!NOTE]
  > 언어 입력
  >
  > 미리 빌드된 영수증 v2.1에는 추가적인 영어권 시장에서 영수증 로캘을 지정하는 선택적 요청 매개 변수가 있습니다. 오스트레일리아(en-au), 캐나다(en-ca), 영국(en-gb) 및 인도(en-in) 각국의 영어로 된 판매 영수증을 위해 로캘을 지정하여 향상된 결과를 얻을 수 있습니다. v2.1에 지정된 로캘이 없으면 모델이 자동으로 로캘을 검색합니다.

## <a name="analyze-receipt"></a>영수증 분석

[영수증 분석](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeReceiptAsync)은 영수증의 이미지나 PDF를 입력으로 사용하고 이율 및 텍스트의 값을 추출합니다. 호출은 `Operation-Location`이라는 응답 헤더 필드를 반환합니다. `Operation-Location` 값은 다음 단계에서 사용할 결과 ID를 포함하는 URL입니다.

|응답 헤더| 결과 URL |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/formrecognizer/v2.1/prebuilt/receipt/analyzeResults/56a36454-fc4d-4354-aa07-880cfbf0064f` |

## <a name="get-analyze-receipt-result"></a>Get Analyze Receipt Result

두 번째 단계는 [영수증 분석 결과 가져오기](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/GetAnalyzeReceiptResult) 작업을 호출하는 것입니다. 이 작업은 영수증 분석 작업으로 만들어진 결과 ID를 입력으로 사용합니다. 다음과 같은 가능한 값을 가진 **상태** 필드가 포함된 JSON 응답을 반환합니다. **succeeded** 값이 반환될 때까지 이 작업을 반복적으로 호출합니다. 3~5초의 간격을 사용하여 RPS(초당 요청 수) 속도를 초과하지 않도록 합니다.

|필드| 형식 | 가능한 값 |
|:-----|:----:|:----|
|상태 | 문자열 | notStarted: 작업이 시작되지 않았습니다. |
| |  | running: 분석 작업이 진행 중입니다. |
| |  | failed: 분석 작업이 실패했습니다. |
| |  | succeeded: 분석 작업이 성공했습니다. |

**상태** 필드의 값이 **성공** 이면 JSON 응답에는 영수증 해석 및 텍스트 인식 결과가 포함됩니다. 영수증 해석 결과는 명명된 필드 값의 사전으로 구성됩니다. 각 값에는 추출된 텍스트, 정규화된 값, 경계 상자, 신뢰도 및 해당하는 단어 요소가 포함됩니다. 텍스트 인식 결과는 텍스트, 경계 상자 및 신뢰도 정보를 사용하여 줄과 단어의 계층 구조로 정리됩니다.

![샘플 영수증 결과](./media/contoso-receipt-2-information.png)

### <a name="sample-json-output"></a>샘플 JSON 출력

영수증 분석 결과 가져오기 작업에 대한 응답은 모든 정보가 추출된 영수증을 구조화된 방식으로 표현한 것입니다.  [샘플 영수증 파일](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/contoso-allinone.jpg) 및 해당 구조화된 출력 [샘플 영수증 출력](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/receipt-result.json)은 여기를 참조하세요.

성공적인 JSON 응답에 대한 다음 예제를 참조하세요(간단한 설명을 위해 출력이 단축됨).
* `"readResults"` 노드에는 인식된 모든 텍스트가 포함됩니다. 텍스트는 페이지별로, 그 다음에는 줄별로, 그 다음에는 개별 단어별로 정리됩니다.
* `"documentResults"` 노드에는 모델이 검색한 명함 특정 값이 포함됩니다. 여기서 이름, 성, 회사 이름 등과 같은 유용한 키/값 쌍을 찾을 수 있습니다.

```json
{
  "status":"succeeded",
  "createdDateTime":"2019-12-17T04:11:24Z",
  "lastUpdatedDateTime":"2019-12-17T04:11:32Z",
  "analyzeResult":{
    "version":"2.0.0",
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

## <a name="next-steps"></a>다음 단계

* 사용자 고유의 영수증과 샘플을 [Form Recognizer 샘플 UI](https://fott-preview.azurewebsites.net/)에서 사용해 보세요.
* 사용자가 선택한 개발 언어로 Form Recognizer를 사용하여 영수증 처리 앱 작성을 시작하려면 [Form Recognizer 빠른 시작](quickstarts/client-library.md)을 완료하세요.
