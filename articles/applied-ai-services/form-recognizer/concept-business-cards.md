---
title: 명함 - 양식 인식기
titleSuffix: Azure Applied AI Services
description: Form Recognizer API - 사용 및 제한으로 명함 분석과 관련된 개념을 알아봅니다.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 08/09/2021
ms.author: lajanuar
ms.openlocfilehash: 8210380fefe6b7f781175fc603a9529e2fd23710
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122530606"
---
# <a name="form-recognizer-prebuilt-business-cards-model"></a>양식 인식기로 미리 작성된 명함 모델

Azure Form Recognizer는 미리 작성된 명함 모델을 사용하여 명함에서 연락처 정보를 분석하고 추출할 수 있습니다. 강력한 OCR(광학 문자 인식) 기능을 명함 이해 모델과 결합하여 영어로 된 명함의 주요 정보를 추출합니다. 개인 연락처 정보, 회사 이름, 직위 등을 추출합니다. 미리 작성된 비즈니스 카드 API는 Form Recognizer v2.1에서 공개적으로 사용할 수 있습니다.

## <a name="customer-scenarios"></a>고객 시나리오

비즈니스 카드 API를 사용하여 추출된 데이터를 사용하여 다양한 작업을 수행할 수 있습니다. 이 연락처 정보를 추출하면 클라이언트 관련 역할을 하는 사용자의 시간이 자동으로 절감됩니다. 다음은 고객이 비즈니스 카드 API를 통해 달성한 몇 가지 예입니다.

* 명함에서 연락처 정보를 추출하고 신속하게 전화 연락처를 만듭니다.
* CRM과 통합하여 명함 이미지를 사용해 연락처를 자동으로 만듭니다.
* 판매 잠재 고객을 계속 추적합니다.
* 기존 명함 이미지에서 연락처 정보를 대량으로 추출합니다.

또한 비즈니스 카드 API는 [AI 빌더 명함 처리 기능](/ai-builder/prebuilt-business-card)을 강화합니다.

## <a name="try-it"></a>시도

Form Recognizer 영수증 서비스를 사용해보려면 다음과 같은 온라인 샘플 UI 도구로 이동합니다.

> [!div class="nextstepaction"]
> [비즈니스 카드 모델 시도](https://aka.ms/fott-2.1-ga "사전 구축된 모델로 시작하여 비즈니스 카드에서 데이터 추출")

## <a name="what-does-the-business-card-service-do"></a>비즈니스 카드 서비스란 무엇인가요?

미리 작성된 비즈니스 카드 API는 명함에서 키 필드를 추출하여 구성된 JSON 응답으로 반환합니다.

![샘플 레이블 지정 도구의 Contoso 항목별 이미지 + JSON 출력](./media/business-card-example.jpg)

### <a name="fields-extracted"></a>추출된 필드

|이름| 유형 | 설명 | 텍스트 | 값(표준화된 출력) |
|:-----|:----|:----|:----|:----|
| ContactNames | 개체의 배열 | 명함에서 추출 된 연락처 이름 | [{ "FirstName": "Chris", "LastName": "Smith" }] | |
| FirstName | 문자열 | 연락처의 이름 | "Chris" | "Chris" |
| LastName | 문자열 | 연락처의 성 |     "Smith" | "Smith" |
| CompanyNames | 문자열 배열 | 명함에서 추출된 회사 이름 | ["CONTOSO"] | CONTOSO |
| Departments | 문자열 배열 | 연락처의 부서 또는 조직 | ["Cloud 및 Al Department"] | Cloud 및 Al Department |
| JobTitles | 문자열 배열 | 연락처의 나열된 직함 | ["선임 연구원"] | 선임 연구원 |
| 전자 메일 | 문자열 배열 | 명함에서 추출된 연락처 이메일 | ["chris.smith@contoso.com"] | chris.smith@contoso.com |
| Websites | 문자열 배열 | 명함에서 추출된 웹사이트 | ["https://www.contoso.com"] | https://www.contoso.com |
| 주소 | 문자열 배열 | 명함에서 추출된 주소 | ["4001 1st Ave NE Redmond, WA 98052"] | 4001 1st Ave NE Redmond, WA 98052 |
| MobilePhones | 전화 번호 배열 | 명함에서 추출된 휴대폰 번호 | ["+1 (987) 123-4567"] | +19871234567 |
| Faxes | 전화 번호 배열 | 명함에서 추출된 팩스 번호 | ["+1 (987) 312-6745"] | +19873126745 |
| WorkPhones | 전화 번호 배열 | 명함에서 추출된 회사 전화 번호 | ["+1 (987) 213-5674"] | +19872135674 | 
| OtherPhones     | 전화 번호 배열 | 명함에서 추출된 기타 전화 번호 | ["+1 (987) 213-5673"] | +19872135673 |

비즈니스 카드 API는 명함에서 인식된 모든 텍스트를 반환할 수도 있습니다. 이 OCR 출력은 JSON 응답에 포함됩니다.

### <a name="input-requirements"></a>입력 요구 사항

[!INCLUDE [input requirements](./includes/input-requirements-receipts.md)]

## <a name="supported-locales"></a>지원되는 로캘

**미리 빌드된 명함 v2.1** 은 **en-us**, **en-au**, **en-ca**, **en-gb**, **en-in** 로캘을 지원합니다.

## <a name="analyze-business-card"></a>명함 분석

[명함 분석](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeBusinessCardAsync)은 명함의 이미지 또는 PDF를 입력으로 사용하여 관심 있는 값을 추출합니다. 호출은 `Operation-Location`이라는 응답 헤더 필드를 반환합니다. `Operation-Location` 값은 다음 단계에서 사용할 결과 ID를 포함하는 URL입니다.

|응답 헤더| 결과 URL |
|:-----|:----|
|Operation-Location | `https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeBusinessCardAsync` |

## <a name="get-analyze-business-card-result"></a>Get Analyze Business Card Result

두 번째 단계는 [명함 분석 결과 가져오기](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/GetAnalyzeBusinessCardResult) 작업을 호출하는 것입니다. 이 작업은 명함 분석 작업으로 만들어진 결과 ID를 입력으로 사용합니다. 다음과 같은 가능한 값을 가진 **상태** 필드가 포함된 JSON 응답을 반환합니다. **succeeded** 값이 반환될 때까지 이 작업을 반복적으로 호출합니다. 3~5초의 간격을 사용하여 RPS(초당 요청 수) 속도를 초과하지 않도록 합니다.

|필드| 형식 | 가능한 값 |
|:-----|:----:|:----|
|상태 | 문자열 | notStarted: 분석 작업이 시작되지 않았습니다.<br /><br />running: 분석 작업이 진행 중입니다.<br /><br />failed: 분석 작업이 실패했습니다.<br /><br />succeeded: 분석 작업이 성공했습니다.|

**상태** 필드의 값이 **succeeded** 이면 JSON 응답에는 요청된 경우 명함 해석 및 선택적 텍스트 인식 결과가 포함됩니다. 명함 해석 결과는 명명된 필드 값의 사전으로 구성됩니다. 여기서 각 값에는 추출된 텍스트, 정규화된 값, 경계 상자, 신뢰도 및 해당 단어 요소가 포함됩니다. 텍스트 인식 결과는 텍스트, 경계 상자 및 신뢰도 정보와 함께 줄과 단어의 계층 구조로 구성됩니다.

![명함 출력 샘플](./media/business-card-results.png)

### <a name="sample-json-output"></a>JSON 출력 샘플

명함 분석 결과 가져오기 작업에 대한 응답은 모든 정보가 추출된 명함을 구조화된 방식으로 표현한 것입니다.  [명함 파일 샘플](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/business-card-english.jpg) 및 구조화된 출력 [명함 출력 샘플](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/business-card-result.json)은 여기를 참조하세요.

성공적인 JSON 응답에 대한 다음 예제를 참조하세요(간단한 설명을 위해 출력이 단축됨).
* `"readResults"` 노드에는 인식된 모든 텍스트가 포함됩니다. 텍스트는 페이지별로, 그 다음에는 줄별로, 그 다음에는 개별 단어별로 정리됩니다.
* `"documentResults"` 노드에는 모델이 검색한 명함 특정 값이 포함됩니다. 여기서 이름, 성, 회사 이름 등과 같은 유용한 연락처 정보를 볼 수 있습니다.

```json
{
    "status": "succeeded",
    "createdDateTime": "2021-05-27T02:18:35Z",
    "lastUpdatedDateTime": "2021-05-27T02:18:37Z",
    "analyzeResult": {
        "version": "2.1.0",
        "readResults": [
            {
                "page": 1,
                "angle": 0.0255,
                "width": 2592,
                "height": 4608,
                "unit": "pixel",
                "lines": [
                    {
                        "text": "CONTOSO",
                        "boundingBox": [
                            533,
                            1570,
                            1334,
                            1570,
                            1333,
                            1721,
                            533,
                            1720
                        ],
                        "words": [
                            {
                                "text": "CONTOSO",
                                "boundingBox": [
                                    535,
                                    1571,
                                    1278,
                                    1571,
                                    1279,
                                    1722,
                                    534,
                                    1719
                                ],
                                "confidence": 0.994
                            }
                        ],
                        "appearance": {
                            "style": {
                                "name": "other",
                                "confidence": 0.878
                            }
                        }
                    },
                    ...
                ]
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
                    "Addresses": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "4001 1st Ave NE Redmond, WA 98052",
                                "text": "4001 1st Ave NE Redmond, WA 98052",
                                "boundingBox": [
                                    400,
                                    2789,
                                    1514,
                                    2789,
                                    1514,
                                    2857,
                                    400,
                                    2857
                                ],
                                "page": 1,
                                "confidence": 0.986,
                                "elements": [
                                    "#/readResults/0/lines/9/words/0",
                                    "#/readResults/0/lines/9/words/1",
                                    "#/readResults/0/lines/9/words/2",
                                    "#/readResults/0/lines/9/words/3",
                                    "#/readResults/0/lines/9/words/4",
                                    "#/readResults/0/lines/9/words/5",
                                    "#/readResults/0/lines/9/words/6"
                                ]
                            }
                        ]
                    },
                    "CompanyNames": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "CONTOSO",
                                "text": "CONTOSO",
                                "boundingBox": [
                                    535,
                                    1571,
                                    1278,
                                    1571,
                                    1279,
                                    1722,
                                    534,
                                    1719
                                ],
                                "page": 1,
                                "confidence": 0.985,
                                "elements": [
                                    "#/readResults/0/lines/0/words/0"
                                ]
                            }
                        ]
                    },
                    "ContactNames": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "object",
                                "valueObject": {
                                    "FirstName": {
                                        "type": "string",
                                        "valueString": "Chris",
                                        "text": "Chris",
                                        "boundingBox": [
                                            1556,
                                            2018,
                                            1915,
                                            2021,
                                            1915,
                                            2156,
                                            1558,
                                            2154
                                        ],
                                        "page": 1,
                                        "elements": [
                                            "#/readResults/0/lines/1/words/0"
                                        ]
                                    },
                                    "LastName": {
                                        "type": "string",
                                        "valueString": "Smith",
                                        "text": "Smith",
                                        "boundingBox": [
                                            1944,
                                            2021,
                                            2368,
                                            2016,
                                            2364,
                                            2156,
                                            1944,
                                            2156
                                        ],
                                        "page": 1,
                                        "elements": [
                                            "#/readResults/0/lines/1/words/1"
                                        ]
                                    }
                                },
                                "text": "Chris Smith",
                                "boundingBox": [
                                    1556.1,
                                    2010.3,
                                    2368,
                                    2016,
                                    2367,
                                    2159.6,
                                    1555.1,
                                    2154
                                ],
                                "page": 1,
                                "confidence": 0.99,
                                "elements": [
                                    "#/readResults/0/lines/1/words/0",
                                    "#/readResults/0/lines/1/words/1"
                                ]
                            }
                        ]
                    },
                    "Departments": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "Cloud & Al Department",
                                "text": "Cloud & Al Department",
                                "boundingBox": [
                                    1578,
                                    2288.8,
                                    2277,
                                    2295.1,
                                    2276.3,
                                    2367.8,
                                    1577.3,
                                    2361.5
                                ],
                                "page": 1,
                                "confidence": 0.989,
                                "elements": [
                                    "#/readResults/0/lines/3/words/0",
                                    "#/readResults/0/lines/3/words/1",
                                    "#/readResults/0/lines/3/words/2",
                                    "#/readResults/0/lines/3/words/3"
                                ]
                            }
                        ]
                    },
                    "Emails": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "chris.smith@contoso.com",
                                "text": "chris.smith@contoso.com",
                                "boundingBox": [
                                    1583,
                                    2381,
                                    2309,
                                    2382,
                                    2308,
                                    2445,
                                    1584,
                                    2447
                                ],
                                "page": 1,
                                "confidence": 0.99,
                                "elements": [
                                    "#/readResults/0/lines/4/words/0"
                                ]
                            }
                        ]
                    },
                    "Faxes": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "phoneNumber",
                                "valuePhoneNumber": "+19873126745",
                                "text": "+1 (987) 312-6745",
                                "boundingBox": [
                                    740,
                                    2703.8,
                                    1273,
                                    2702.1,
                                    1273.2,
                                    2774.1,
                                    740.2,
                                    2775.8
                                ],
                                "page": 1,
                                "confidence": 0.99,
                                "elements": [
                                    "#/readResults/0/lines/8/words/1",
                                    "#/readResults/0/lines/8/words/2",
                                    "#/readResults/0/lines/8/words/3"
                                ]
                            }
                        ]
                    },
                    "JobTitles": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "Senior Researcher",
                                "text": "Senior Researcher",
                                "boundingBox": [
                                    1578,
                                    2206,
                                    2117,
                                    2207.6,
                                    2116.8,
                                    2272.6,
                                    1577.8,
                                    2271
                                ],
                                "page": 1,
                                "confidence": 0.99,
                                "elements": [
                                    "#/readResults/0/lines/2/words/0",
                                    "#/readResults/0/lines/2/words/1"
                                ]
                            }
                        ]
                    },
                    "MobilePhones": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "phoneNumber",
                                "valuePhoneNumber": "+19871234567",
                                "text": "+1 (987) 123-4567",
                                "boundingBox": [
                                    744,
                                    2529,
                                    1281,
                                    2529,
                                    1281,
                                    2603,
                                    744,
                                    2603
                                ],
                                "page": 1,
                                "confidence": 0.99,
                                "elements": [
                                    "#/readResults/0/lines/5/words/1",
                                    "#/readResults/0/lines/5/words/2",
                                    "#/readResults/0/lines/5/words/3"
                                ]
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
                                    1576,
                                    2462,
                                    2383,
                                    2462,
                                    2380,
                                    2535,
                                    1576,
                                    2535
                                ],
                                "page": 1,
                                "confidence": 0.99,
                                "elements": [
                                    "#/readResults/0/lines/6/words/0"
                                ]
                            }
                        ]
                    },
                    "WorkPhones": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "phoneNumber",
                                "valuePhoneNumber": "+19872135674",
                                "text": "+1 (987) 213-5674",
                                "boundingBox": [
                                    736,
                                    2617.6,
                                    1267.1,
                                    2618.5,
                                    1267,
                                    2687.5,
                                    735.9,
                                    2686.6
                                ],
                                "page": 1,
                                "confidence": 0.984,
                                "elements": [
                                    "#/readResults/0/lines/7/words/1",
                                    "#/readResults/0/lines/7/words/2",
                                    "#/readResults/0/lines/7/words/3"
                                ]
                            }
                        ]
                    }
                }
            }
        ]
    }
}
```

## <a name="next-steps"></a>다음 단계

* 사용자 고유의 명함과 샘플을 [Form Recognizer 샘플 UI](https://fott-preview.azurewebsites.net/)에서 사용해 보세요.
* 사용자가 선택한 개발 언어로 Form Recognizer를 사용하여 명함 처리 앱 작성을 시작하려면 [Form Recognizer 빠른 시작](quickstarts/client-library.md)을 완료하세요.
