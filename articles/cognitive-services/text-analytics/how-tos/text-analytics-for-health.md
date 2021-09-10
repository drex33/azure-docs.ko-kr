---
title: 의료 분야 Text Analytics 사용 방법
titleSuffix: Azure Cognitive Services
description: 의료 분야 Text Analytics를 사용하여 비구조적 임상 텍스트에서 의료 정보를 추출하고 레이블을 지정하는 방법을 알아봅니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 06/18/2021
ms.author: aahi
ms.openlocfilehash: 5b1883b06ae234ed8a4f9adf949cf26919f7b877
ms.sourcegitcommit: cc099517b76bf4b5421944bd1bfdaa54153458a0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/09/2021
ms.locfileid: "113550159"
---
# <a name="how-to-use-text-analytics-for-health"></a>의료분야 Text Analytics 사용 방법

> [!IMPORTANT] 
> 의료분야 Text Analytics는 ‘있는 그대로’ 및 ‘모든 오류 포함’으로 제공되는 미리 보기 기능입니다. 의료 분야 Text Analytics는 질병 또는 기타 상태의 진단, 치료, 완화, 처치 또는 예방에 사용하기 위한 의료 기기, 임상 지원, 진단 도구 또는 기타 기술로 사용하도록 의도되거나 제공되지 않습니다. 또한 Microsoft는 이러한 목적으로 이 기능을 사용할 수 있는 라이선스나 권한을 부여하지 않습니다. 이 기능은 전문적인 의학적 조언이나 의료 의견, 진단, 치료 또는 의료 전문가의 임상적 판단을 대체하기 위해 구현되거나, 배포되도록 설계되거나, 의도된 것이 아니므로 그렇게 사용해서는 안 됩니다. 의료 분야 Text Analytics 사용의 책임은 전적으로 고객에게 있습니다. 고객은 해당 [UMLS Metathesaurus 라이선스 계약 부록](https://www.nlm.nih.gov/research/umls/knowledge_sources/metathesaurus/release/license_agreement_appendix.html) 또는 향후 동등한 링크에 설정된 조건에 따라 사용하려는 모든 소스 어휘에 대해 별도로 라이선스를 취득해야 합니다. 고객은 지리적 또는 기타 적용 가능한 제한 사항을 포함하여 해당 사용 조건을 준수할 책임이 있습니다.


의료 분야 Text Analytics는 의사의 메모, 퇴원 요약, 임상 문서 및 전자 의료 레코드 같은 구조화되지 않은 텍스트에서 관련 의료 정보를 추출하고 레이블을 지정하는 Text Analytics API 서비스 기능입니다.  이 서비스를 활용하는 방법에는 두 가지가 있습니다. 

* [웹 기반 API(비동기)](#structure-the-api-request-for-the-hosted-asynchronous-web-api)
* [Docker 컨테이너(동기)](#hosted-asynchronous-web-api-response)   

> [!VIDEO https://channel9.msdn.com/Shows/AI-Show/Introducing-Text-Analytics-for-Health/player]

## <a name="features"></a>기능

의료 분야 Text Analytics는 NER(명명된 엔터티 인식), 관계 추출, 엔터티 부정 및 영어 텍스트에 대한 엔터티 연결을 수행하여 비구조적 임상 및 생명 공학 텍스트에서 정보를 파악합니다.

### <a name="named-entity-recognition"></a>[명명된 엔터티 인식](#tab/ner)

명명된 엔터티 인식은 진단, 처방약 이름, 증상/징후 또는 연령과 같은 하나 이상의 의미 체계와 연관될 수 있는 비구조적 텍스트에 언급된 단어 및 구를 검색합니다.

> [!div class="mx-imgBorder"]
> ![의료 분야 NER](../media/ta-for-health/health-named-entity-recognition.png)

### <a name="relation-extraction"></a>[관계 추출](#tab/relation-extraction)

관계 추출은 텍스트에 언급된 개념 간의 의미 있는 연결을 식별합니다. 예를 들어, "조건 시간" 관계는 조건 이름을 시간에 연결하여 찾거나 약어와 전체 설명 사이에서 찾습니다.  

> [!div class="mx-imgBorder"]
> ![의료 분야 RE](../media/ta-for-health/health-relation-extraction.png)


### <a name="entity-linking"></a>[엔터티 연결](#tab/entity-linking)

엔터티 연결은 텍스트에 언급된 명명된 엔터티를 UMLS(통합 의료 언어 시스템)를 포함하여 사전 정의된 개념 데이터베이스에 있는 개념에 연결하여 고유한 엔터티를 구분합니다. 의료 개념에는 추가 정규화 형식으로 선호하는 이름이 지정됩니다.

> [!div class="mx-imgBorder"]
> ![의료 분야 EL](../media/ta-for-health/health-entity-linking.png)

의료 분야 Text Analytics는 [UMLS](https://www.nlm.nih.gov/research/umls/sourcereleasedocs/index.html)(Unified Modeling Language) Metathesaurus 기술 자료에 있는 상태 및 생명 공학 어휘에 연결하는 것을 지원합니다.

### <a name="assertion-detection"></a>[어설션 검색](#tab/assertion-detection) 

의료 콘텐츠의 의미는 부정적 또는 조건적 어설션과 같은 한정자에 의해 크게 영향을 받습니다. 이러한 어설션이 잘못 표현될 경우 중대한 영향을 미칠 수 있습니다. 의료 분야 Text Analytics는 텍스트의 엔터티에 대한 세 가지 범주의 어설션 검색을 지원합니다. 

* 확신도
* 조건
* 연관성

> [!div class="mx-imgBorder"]
> ![의료 분야 NEG](../media/ta-for-health/assertions.png)

---

지원되는 엔터티의 전체 목록은 의료 분야 Text Analytics에서 반환하는 [엔터티 범주](../named-entity-types.md?tabs=health)를 참조하세요. 신뢰도 점수에 대한 자세한 내용은 [Text Analytics 투명도 메모](/legal/cognitive-services/text-analytics/transparency-note#general-guidelines-to-understand-and-improve-performance?context=/azure/cognitive-services/text-analytics/context/context)를 참조하세요. 

### <a name="supported-languages"></a>지원되는 언어

의료 분야 Text Analytics는 영어 문서만 지원합니다. 

## <a name="using-the-docker-container"></a>Docker 컨테이너 사용 

사용자 환경에서 의료 분야 Text Analytics 컨테이너를 실행하려면 다음 [컨테이너 다운로드 및 설치 지침](../how-tos/text-analytics-how-to-install-containers.md?tabs=healthcare)을 따르세요.

## <a name="using-the-client-library"></a>클라이언트 라이브러리 사용

Text Analytics 클라이언트 라이브러리의 최신 시험판을 사용하면 클라이언트 개체를 통해 의료 분야 Text Analytics를 호출할 수 있습니다. 참조 문서를 참조하고 GitHub의 예를 참조하세요.
* [C#](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics)
* [Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/)
* [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics)



## <a name="sending-a-rest-api-request"></a>REST API 요청 보내기 

### <a name="preparation"></a>준비

다음 형식의 JSON 문서가 있어야 합니다. ID, 텍스트 및 언어. 

문서 크기는 문서당 5,120자 미만이어야 합니다. 컬렉션에 허용되는 최대 문서 수는 개념 아래의 [데이터 제한](../concepts/data-limits.md?tabs=version-3) 문서를 참조하세요. 컬렉션은 요청 본문에 제출됩니다. 텍스트가 이 제한을 초과 하는 경우,텍스트를 별도의 요청으로 분할하는 것이 좋습니다. 최상의 결과를 위해서는 텍스트는 문장 사이에서 분할합니다.

### <a name="structure-the-api-request-for-the-hosted-asynchronous-web-api"></a>호스팅된 비동기 웹 API에 대한 API 요청 구성

컨테이너와 호스팅된 웹 API 모두에 대해 POST 요청을 만들어야 합니다. [의료 분야 Text Analytics 호스팅 API 참조](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1/operations/Health)에서 [Postman](text-analytics-how-to-call-api.md), cURL 명령 또는 **API 테스트 콘솔** 을 사용하여 원하는 지역의 호스팅된 웹 API에 POST 요청을 빠르게 구성하고 보낼 수 있습니다. API v3.1 엔드포인트에서 `loggingOptOut` 부울 쿼리 매개 변수를 사용하여 문제 해결을 위해 로깅을 사용할 수 있습니다.  요청 쿼리에 지정하지 않으면 기본값은 TRUE입니다.

아래 `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1/entities/health/jobs`에 POST 요청 전송은 상태 API 요청의 POST 본문에 첨부된 JSON 파일의 예입니다.

```json
example.json

{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "Subject was administered 100mg remdesivir intravenously over a period of 120 min"
    }
  ]
}
```

### <a name="hosted-asynchronous-web-api-response"></a>호스팅된 비동기 웹 API 응답 

이 POST 요청은 비동기 작업(operation)에 대한 작업(job)을 제출하는 데 사용되므로 응답 개체에 텍스트가 없습니다.  그러나 작업 및 출력의 상태를 확인하기 위해 GET 요청을 하려면 응답 헤더에 작업-위치 키 값이 필요합니다.  다음은 POST 요청의 응답 헤더에 있는 작업-위치 KEY 값의 예입니다.

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1/entities/health/jobs/<jobID>`

작업 상태를 확인하려면 POST 응답의 작업-위치 KEY 헤더 값에 있는 URL에 GET 요청을 수행합니다.  작업 상태를 반영하는 데는 `NotStarted`, `running`, `succeeded`, `failed`, `rejected`, `cancelling` 및 `cancelled` 등의 상태가 사용됩니다.  

GET 요청과 동일한 URL에 대한 DELETE HTTP 호출을 사용하여 `NotStarted` 또는 `running` 상태의 작업을 취소할 수 있습니다.  DELETE 호출에 대한 자세한 내용은 [의료 분야 Text Analytics 호스팅 API 참조](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1/operations/CancelHealthJob)에서 확인할 수 있습니다.

다음은 GET 요청 응답의 예입니다.  출력은 `expirationDateTime`(작업이 생성된 시간부터 24시간)이 경과되어 출력이 제거될 때까지 검색에 사용할 수 있습니다.

```json
{
    "jobId": "69081148-055b-4f92-977d-115df343de69",
    "lastUpdateDateTime": "2021-07-06T19:06:03Z",
    "createdDateTime": "2021-07-06T19:05:41Z",
    "expirationDateTime": "2021-07-07T19:05:41Z",
    "status": "succeeded",
    "errors": [],
    "results": {
        "documents": [
            {
                "id": "1",
                "entities": [
                    {
                        "offset": 25,
                        "length": 5,
                        "text": "100mg",
                        "category": "Dosage",
                        "confidenceScore": 1.0
                    },
                    {
                        "offset": 31,
                        "length": 10,
                        "text": "remdesivir",
                        "category": "MedicationName",
                        "confidenceScore": 1.0,
                        "name": "remdesivir",
                        "links": [
                            {
                                "dataSource": "UMLS",
                                "id": "C4726677"
                            },
                            {
                                "dataSource": "DRUGBANK",
                                "id": "DB14761"
                            },
                            {
                                "dataSource": "GS",
                                "id": "6192"
                            },
                            {
                                "dataSource": "MEDCIN",
                                "id": "398132"
                            },
                            {
                                "dataSource": "MMSL",
                                "id": "d09540"
                            },
                            {
                                "dataSource": "MSH",
                                "id": "C000606551"
                            },
                            {
                                "dataSource": "MTHSPL",
                                "id": "3QKI37EEHE"
                            },
                            {
                                "dataSource": "NCI",
                                "id": "C152185"
                            },
                            {
                                "dataSource": "NCI_FDA",
                                "id": "3QKI37EEHE"
                            },
                            {
                                "dataSource": "NDDF",
                                "id": "018308"
                            },
                            {
                                "dataSource": "RXNORM",
                                "id": "2284718"
                            },
                            {
                                "dataSource": "SNOMEDCT_US",
                                "id": "870592005"
                            },
                            {
                                "dataSource": "VANDF",
                                "id": "4039395"
                            }
                        ]
                    },
                    {
                        "offset": 42,
                        "length": 13,
                        "text": "intravenously",
                        "category": "MedicationRoute",
                        "confidenceScore": 0.99
                    },
                    {
                        "offset": 73,
                        "length": 7,
                        "text": "120 min",
                        "category": "Time",
                        "confidenceScore": 0.98
                    }
                ],
                "relations": [
                    {
                        "relationType": "DosageOfMedication",
                        "entities": [
                            {
                                "ref": "#/results/documents/0/entities/0",
                                "role": "Dosage"
                            },
                            {
                                "ref": "#/results/documents/0/entities/1",
                                "role": "Medication"
                            }
                        ]
                    },
                    {
                        "relationType": "RouteOfMedication",
                        "entities": [
                            {
                                "ref": "#/results/documents/0/entities/1",
                                "role": "Medication"
                            },
                            {
                                "ref": "#/results/documents/0/entities/2",
                                "role": "Route"
                            }
                        ]
                    },
                    {
                        "relationType": "TimeOfMedication",
                        "entities": [
                            {
                                "ref": "#/results/documents/0/entities/1",
                                "role": "Medication"
                            },
                            {
                                "ref": "#/results/documents/0/entities/3",
                                "role": "Time"
                            }
                        ]
                    }
                ],
                "warnings": []
            }
        ],
        "errors": [],
        "modelVersion": "2021-05-15"
    }
}
```


### <a name="structure-the-api-request-for-the-container"></a>컨테이너에 대한 API 요청 구성

[Postman을 사용](text-analytics-how-to-call-api.md)하거나 아래의 cURL 요청 예를 사용하여 배포한 컨테이너에 쿼리를 제출하고 `serverURL` 변수를 적절한 값으로 바꿀 수 있습니다.  컨테이너의 URL에 있는 API 버전은 호스팅된 API와 다릅니다.

```bash
curl -X POST 'http://<serverURL>:5000/text/analytics/v3.1/entities/health' --header 'Content-Type: application/json' --header 'accept: application/json' --data-binary @example.json

```

다음 JSON은 의료 분야 Text Analytics API 요청의 POST 본문에 첨부된 JSON 파일의 예입니다.

```json
example.json

{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "Patient reported itchy sores after swimming in the lake."
    },
    {
      "language": "en",
      "id": "2",
      "text": "Prescribed 50mg benadryl, taken twice daily."
    }
  ]
}
```

### <a name="container-response-body"></a>컨테이너 응답 본문

다음 JSON은 컨테이너화된 동기 호출의 의료 분야 Text Analytics API 응답 본문의 예입니다.

```json
{
    "documents": [
        {
            "id": "1",
            "entities": [
                {
                    "offset": 25,
                    "length": 5,
                    "text": "100mg",
                    "category": "Dosage",
                    "confidenceScore": 1.0
                },
                {
                    "offset": 31,
                    "length": 10,
                    "text": "remdesivir",
                    "category": "MedicationName",
                    "confidenceScore": 1.0,
                    "name": "remdesivir",
                    "links": [
                        {
                            "dataSource": "UMLS",
                            "id": "C4726677"
                        },
                        {
                            "dataSource": "DRUGBANK",
                            "id": "DB14761"
                        },
                        {
                            "dataSource": "GS",
                            "id": "6192"
                        },
                        {
                            "dataSource": "MEDCIN",
                            "id": "398132"
                        },
                        {
                            "dataSource": "MMSL",
                            "id": "d09540"
                        },
                        {
                            "dataSource": "MSH",
                            "id": "C000606551"
                        },
                        {
                            "dataSource": "MTHSPL",
                            "id": "3QKI37EEHE"
                        },
                        {
                            "dataSource": "NCI",
                            "id": "C152185"
                        },
                        {
                            "dataSource": "NCI_FDA",
                            "id": "3QKI37EEHE"
                        },
                        {
                            "dataSource": "NDDF",
                            "id": "018308"
                        },
                        {
                            "dataSource": "RXNORM",
                            "id": "2284718"
                        },
                        {
                            "dataSource": "SNOMEDCT_US",
                            "id": "870592005"
                        },
                        {
                            "dataSource": "VANDF",
                            "id": "4039395"
                        }
                    ]
                },
                {
                    "offset": 42,
                    "length": 13,
                    "text": "intravenously",
                    "category": "MedicationRoute",
                    "confidenceScore": 1.0
                },
                {
                    "offset": 73,
                    "length": 7,
                    "text": "120 min",
                    "category": "Time",
                    "confidenceScore": 0.94
                }
            ],
            "relations": [
                {
                    "relationType": "DosageOfMedication",
                    "entities": [
                        {
                            "ref": "#/documents/0/entities/0",
                            "role": "Dosage"
                        },
                        {
                            "ref": "#/documents/0/entities/1",
                            "role": "Medication"
                        }
                    ]
                },
                {
                    "relationType": "RouteOfMedication",
                    "entities": [
                        {
                            "ref": "#/documents/0/entities/1",
                            "role": "Medication"
                        },
                        {
                            "ref": "#/documents/0/entities/2",
                            "role": "Route"
                        }
                    ]
                },
                {
                    "relationType": "TimeOfMedication",
                    "entities": [
                        {
                            "ref": "#/documents/0/entities/1",
                            "role": "Medication"
                        },
                        {
                            "ref": "#/documents/0/entities/3",
                            "role": "Time"
                        }
                    ]
                }
            ],
            "warnings": []
        }
    ],
    "errors": [],
    "modelVersion": "2021-03-01"
}
```

### <a name="assertion-output"></a>어설션 출력

의료 분야 Text Analytics는 텍스트 내에서 개념의 컨텍스트를 더 깊이 이해할 수 있도록 의료 개념에 할당된 정보 특성인 어설션 한정자를 반환합니다. 이러한 한정자는 서로 다른 측면에 초점을 맞추고 상호 배타적인 값 집합을 포함하는 세 가지 범주로 나뉩니다. 범주당 하나의 값만 각 항목에 할당됩니다. 각 범주에 대한 가장 일반적인 값은 기본값입니다. 서비스의 출력 응답에는 기본값과 다른 어설션 한정자만 포함됩니다.

**확신도(CERTAINTY)**  – 개념의 존재 여부(존재 및 부재)에 대한 정보와 텍스트가 존재와 관련하여 얼마나 확실한지(확실 및 가능)에 대한 정보를 제공합니다.
*   **Positive**[기본값]: 개념이 존재하거나 발생했습니다.
* **Negative**: 개념이 현재 존재하지 않거나 발생하지 않았습니다.
* **Positive_Possible**: 개념이 존재할 가능성이 있지만 약간의 불확실성이 있습니다.
* **Negative_Possible**: 개념의 존재 가능성은 낮지만 약간의 불확실성이 있습니다.
* **Neutral_Possible**: 개념이 어느 한 쪽에 치우치는 경향이 없이 존재하거나 존재하지 않을 수 있습니다.

**조건부(CONDITIONALITY)** – 개념의 존재가 특정 조건에 따라 달라지는지 여부에 대한 정보를 제공합니다. 
*   **None**[기본값]: 개념이 팩트이고 가상이 아니며 특정 조건에 의존하지 않습니다.
*   **Hypothetical**: 개념이 미래에 개발되거나 발생할 수 있습니다.
*   **Conditional**: 개념이 존재하거나 특정 조건에서만 발생합니다.

**관련(ASSOCIATION)** – 개념이 텍스트의 주체 또는 다른 사람과 관련되어 있는지를 설명합니다.
*   **Subject**[기본값]: 개념이 텍스트의 주체, 일반적으로 환자와 관련됩니다.
*   **Someone_Else**: 개념이 텍스트의 주체가 아닌 사람과 관련이 있습니다.


어설션 검색은 부정된 엔터티를 확실성 범주의 음수 값으로 나타냅니다. 예를 들면 다음과 같습니다.

```json
{
                        "offset": 381,
                        "length": 3,
                        "text": "SOB",
                        "category": "SymptomOrSign",
                        "confidenceScore": 0.98,
                        "assertion": {
                            "certainty&quot;: &quot;negative"
                        },
                        "name": "Dyspnea",
                        "links": [
                            {
                                "dataSource": "UMLS",
                                "id&quot;: &quot;C0013404"
                            },
                            {
                                "dataSource": "AOD",
                                "id&quot;: &quot;0000005442"
                            },
    ...
```

### <a name="relation-extraction-output"></a>관계 추출 출력

의료 분야 Text Analytics는 특성과 엔터티 간의 관계(예: 신체 구조 방향, 약물 투여량) 및 엔터티 간의 관계(예: 약어 검색)를 포함하여 서로 다른 개념 간의 관계를 인식합니다.

**ABBREVIATION**

**BODY_SITE_OF_CONDITION**

**BODY_SITE_OF_TREATMENT**

**COURSE_OF_CONDITION**

**COURSE_OF_EXAMINATION**

**COURSE_OF_MEDICATION**

**COURSE_OF_TREATMENT**

**DIRECTION_OF_BODY_STRUCTURE**

**DIRECTION_OF_CONDITION**

**DIRECTION_OF_EXAMINATION**

**DIRECTION_OF_TREATMENT**

**DOSAGE_OF_MEDICATION**

**EXAMINATION_FINDS_CONDITION**

**EXPRESSION_OF_GENE**

**EXPRESSION_OF_VARIANT**

**FORM_OF_MEDICATION**

**FREQUENCY_OF_CONDITION**

**FREQUENCY_OF_MEDICATION**

**FREQUENCY_OF_TREATMENT**

**MUTATION_TYPE_OF_GENE**

**MUTATION_TYPE_OF_VARIANT**

**QUALIFIER_OF_CONDITION**

**RELATION_OF_EXAMINATION**

**ROUTE_OF_MEDICATION** 

**SCALE_OF_CONDITION**

**TIME_OF_CONDITION**

**TIME_OF_EVENT**

**TIME_OF_EXAMINATION**

**TIME_OF_MEDICATION**

**TIME_OF_TREATMENT**

**UNIT_OF_CONDITION**

**UNIT_OF_EXAMINATION**

**VALUE_OF_CONDITION**  

**VALUE_OF_EXAMINATION**

**VARIANT_OF_GENE**

> [!NOTE]
> * CONDITION을 참조하는 관계는 DIAGNOSIS 엔터티 유형 또는 SYMPTOM_OR_SIGN 엔터티 유형을 참조할 수 있습니다.
> * MEDICATION을 참조하는 관계는 MEDICATION_NAME 엔터티 유형 또는 MEDICATION_CLASS 엔터티 유형을 참조할 수 있습니다.
> * TIME을 참조하는 관계는 TIME 엔터티 유형 또는 DATE 엔터티 유형을 참조할 수 있습니다.

관계 추출 출력에는 URI 참조와 관계 유형 엔터티의 지정된 역할이 포함됩니다. 예를 들면 다음과 같습니다.

```json
                "relations": [
                    {
                        "relationType": "DosageOfMedication",
                        "entities": [
                            {
                                "ref": "#/results/documents/0/entities/0",
                                "role": "Dosage"
                            },
                            {
                                "ref": "#/results/documents/0/entities/1",
                                "role": "Medication"
                            }
                        ]
                    },
                    {
                        "relationType": "RouteOfMedication",
                        "entities": [
                            {
                                "ref": "#/results/documents/0/entities/1",
                                "role": "Medication"
                            },
                            {
                                "ref": "#/results/documents/0/entities/2",
                                "role": "Route"
                            }
                        ]
...
]
```

## <a name="see-also"></a>참고 항목

* [Text Analytics 개요](../overview.md)
* [명명된 엔터티 범주](../named-entity-types.md)
* [새로운 기능](../whats-new.md)
