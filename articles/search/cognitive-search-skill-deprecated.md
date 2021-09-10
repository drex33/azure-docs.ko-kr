---
title: 사용되지 않는 인식 기술
titleSuffix: Azure Cognitive Search
description: 이 페이지에는 사용되지 않는 것으로 간주되어 조만간 Azure Cognitive Search 기술 세트에서 지원되지 않을 인식 기술 목록이 나와 있습니다.
author: LiamCavanagh
ms.author: liamca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: b44ba49fea555652bdf513e40c3e030ec5862cf5
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122567402"
---
# <a name="deprecated-cognitive-skills-in-azure-cognitive-search"></a>Azure Cognitive Search에서 사용되지 않는 인식 기술

이 문서에서는 사용되지 않는 것으로 간주되는 기술을 설명합니다. 내용에 대해서는 다음 가이드를 사용합니다.

* 기술 이름: 사용되지 않는 기술 이름이 @odata.type 특성에 매핑됩니다.
* 사용 가능한 마지막 API 버전: 사용되지 않는 해당 기술이 포함된 기술 세트를 만들고 업데이트할 수 있는 Azure Cognitive Search 퍼블릭 API의 마지막 버전입니다. 이러한 기술이 있는 연결된 기술 세트가 있는 인덱서는 "지원 종료" 날짜까지 향후 API 버전에서도 계속 실행되며 이 시점에서 실패하기 시작합니다.
* 지원 종료: 종료일 이후로 해당 기술은 지원되지 않는 것으로 간주되어 작동을 중지합니다. 이전에 만든 기술 세트는 계속 작동해야 하지만 사용자는 사용되지 않는 기술에서 마이그레이션하는 것이 좋습니다.
* 권장 사항: 지원되는 기술을 사용하기 위한 마이그레이션 경로 전달. 사용자는 지원을 계속 받을 수 있도록 권장 사항을 따르는 것이 좋습니다.

[Microsoft.Skills.Text.EntityRecognitionSkill](#microsoftskillstextentityrecognitionskill)을 사용하는 경우 이 문서는 일반 공급되고 새 기능을 소개하는 [Microsoft.Skills.Text.V3.EntityRecognitionSkill](cognitive-search-skill-entity-recognition-v3.md)을 사용하도록 기술 세트를 업그레이드하는 데 도움이 됩니다. 

[Microsoft.Skills.Text.SentimentSkill](#microsoftskillstextsentimentskill)을 사용하는 경우 이 문서는 일반 공급되고 새 기능을 소개하는 [Microsoft.Skills.Text.V3.SentimentSkill](cognitive-search-skill-sentiment-v3.md)을 사용하도록 기술 세트를 업그레이드하는 데 도움이 됩니다. 

[Microsoft.Skills.Text.NamedEntityRecognitionSkill](#microsoftskillstextnamedentityrecognitionskill)을 사용하는 경우 이 문서는 일반 공급되고 새 기능을 소개하는 [Microsoft.Skills.Text.V3.EntityRecognitionSkill](cognitive-search-skill-entity-recognition-v3.md)을 사용하도록 기술 세트를 업그레이드하는 데 도움이 됩니다.

## <a name="microsoftskillstextentityrecognitionskill"></a>Microsoft.Skills.Text.EntityRecognitionSkill

### <a name="last-available-api-version"></a>사용 가능한 마지막 api 버전

2021-04-30-미리 보기

### <a name="end-of-support"></a>지원 종료

2024년 8월 31일

### <a name="recommendations"></a>권장 사항 

대신 [Microsoft.Skills.Text.V3.EntityRecognitionSkill](cognitive-search-skill-entity-recognition-v3.md)을 사용합니다. EntityRecognitionSkill의 대다수 기능을 더 높은 품질로 제공합니다. 또한 복잡한 출력 필드에 다양한 정보가 있습니다.

[Microsoft.Skills.Text.V3.EntityRecognitionSkill](cognitive-search-skill-entity-recognition-v3.md)로 마이그레이션하려면 기술 정의에 다음 변경 사항 중 하나 이상을 수행해야 합니다. [기술 세트 API 업데이트](/rest/api/searchservice/update-skillset)를 사용하여 기술 정의를 업데이트할 수 있습니다.

1. *(필수)*`@odata.type`을 `"#Microsoft.Skills.Text.EntityRecognitionSkill"`에서 `"#Microsoft.Skills.Text.V3.EntityRecognitionSkill"`로 변경

2. *(선택 사항)* 새 기술은 알려진 형식의 엔터티만 반환하므로 `includeTypelessEntities` 매개 변수는 더 이상 지원되지 않습니다. 따라서 이전 기술 정의가 이를 참조했다면 이제 제거해야 합니다.

3. *(선택 사항)* `namedEntities` 출력을 사용하는 경우 속성 이름이 약간 변경됩니다.
    1. `value`이 `text`로 이름이 바뀌었습니다.
    2. `confidence`가 `confidenceScore`으로 이름이 바뀌었습니다.

    정확히 동일한 속성 이름을 생성해야 하는 경우에는 [ShaperSkill](cognitive-search-skill-shaper.md)을 추가하여 필요한 이름을 가진 출력의 모양을 변경해야 합니다. 예를 들어 이 ShaperSkill은 속성 이름을 이전 값으로 바꿉니다.

    ```json
    {
        "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
        "name": "NamedEntitiesShaper",
        "description": "NamedEntitiesShaper",
        "context": "/document/namedEntitiesV3",
        "inputs": [
            {
                "name": "old_format",
                "sourceContext": "/document/namedEntitiesV3/*",
                "inputs": [
                    {
                        "name": "value",
                        "source": "/document/namedEntitiesV3/*/text"
                    },
                    {
                        "name": "offset",
                        "source": "/document/namedEntitiesV3/*/offset"
                    },
                    {
                        "name": "category",
                        "source": "/document/namedEntitiesV3/*/category"
                    },
                    {
                        "name": "confidence",
                        "source": "/document/namedEntitiesV3/*/confidenceScore"
                    }
                ]
            }
        ],
        "outputs": [
            {
                "name": "output",
                "targetName": "namedEntities"
            }
        ]
    }
    ```

4. *(선택 사항)* `entities` 출력을 사용하여 엔터티를 잘 알려진 엔터티에 연결하는 경우 이 기능은 이제 새로운 기술인 [Microsoft.Skills.Text.V3.EntityLinkingSkill ](cognitive-search-skill-entity-linking-v3.md)입니다. 연결된 엔터티를 생성하려면 엔터티 연결 기술을 기술 세트에 추가합니다. 또한 `EntityRecognitionSkill`과 새 `EntityLinkingSkill` 사이에 `entities` 출력의 속성 이름이 약간 변경되었습니다.
    1. `wikipediaId`이 `id`로 이름이 바뀌었습니다.
    2. `wikipediaLanguage`이 `language`로 이름이 바뀌었습니다.
    3. `wikipediaUrl`가 `url`으로 이름이 바뀌었습니다.
    4. `type` 및 `subtype` 속성은 더 이상 반환되지 않습니다.

    정확히 동일한 속성 이름을 생성해야 하는 경우에는 [ShaperSkill](cognitive-search-skill-shaper.md)을 추가하여 필요한 이름을 가진 출력의 모양을 변경해야 합니다. 예를 들어 이 ShaperSkill은 속성 이름을 이전 값으로 바꿉니다.

    ```json
    {
        "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
        "name": "LinkedEntitiesShaper",
        "description": "LinkedEntitiesShaper",
        "context": "/document/linkedEntitiesV3",
        "inputs": [
            {
                "name": "old_format",
                "sourceContext": "/document/linkedEntitiesV3/*",
                "inputs": [
                    {
                        "name": "name",
                        "source": "/document/linkedEntitiesV3/*/name"
                    },
                    {
                        "name": "wikipediaId",
                        "source": "/document/linkedEntitiesV3/*/id"
                    },
                    {
                        "name": "wikipediaLanguage",
                        "source": "/document/linkedEntitiesV3/*/language"
                    },
                    {
                        "name": "wikipediaUrl",
                        "source": "/document/linkedEntitiesV3/*/url"
                    },
                    {
                        "name": "bingId",
                        "source": "/document/linkedEntitiesV3/*/bingId"
                    },
                    {
                        "name": "matches",
                        "source": "/document/linkedEntitiesV3/*/matches"
                    }
                ]
            }
        ],
        "outputs": [
            {
                "name": "output",
                "targetName": "entities"
            }
        ]
    }
    ```

5. *(선택 사항)* 명시적으로 `categories`를 지정하지 않는 경우 `EntityRecognitionSkill V3`은 `EntityRecognitionSkill`에서 지원된 카테고리 외에 다른 형식의 카테고리를 반환할 수 있습니다. 이 동작이 바람직하지 않은 경우 명시적으로 `categories` 매개 변수를 `["Person", "Location", "Organization", "Quantity", "Datetime", "URL", "Email"]`으로 설정해야 합니다.

    _샘플 마이그레이션 정의_

    * 간단한 마이그레이션

        _(이전) EntityRecognition 기술 정의_

        ```json
        {   
            "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
            "categories": [ "Person" ],
            "defaultLanguageCode": "en",
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/content"
                }
            ],
            "outputs": [
                {
                    "name": "persons",
                    "targetName": "people"
                }
            ]
        }
        ```

        _(이후) EntityRecognition 기술 V3 정의_

        ```json
        {
            "@odata.type": "#Microsoft.Skills.Text.V3.EntityRecognitionSkill",
            "categories": [ "Person" ],
            "defaultLanguageCode": "en",
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/content"
                }
            ],
            "outputs": [
                {
                    "name": "persons",
                    "targetName": "people"
                }
            ]
        }
        ```
    
    * 복잡한 마이그레이션

        _(이전) EntityRecognition 기술 정의_
    
        ```json
        {
            "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
            "categories": [ "Person", "Location", "Organization" ],
            "defaultLanguageCode": "en",
            "minimumPrecision": 0.1,
            "includeTypelessEntities": true,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/content"
                }
            ],
            "outputs": [
                {
                    "name": "persons",
                    "targetName": "people"
                },
                {
                    "name": "namedEntities",
                    "targetName": "namedEntities"
                },
                {
                    "name": "entities",
                    "targetName": "entities"
                }
            ]
        }
        ```
    
        _(이후) EntityRecognition 기술 V3 정의_
    
        ```json
        {
            "@odata.type": "#Microsoft.Skills.Text.V3.EntityRecognitionSkill",
            "categories": [ "Person", "Location", "Organization" ],
            "defaultLanguageCode": "en",
            "minimumPrecision": 0.1,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/content"
                }
            ],
            "outputs": [
                {
                    "name": "persons",
                    "targetName": "people"
                },
                {
                    "name": "namedEntities",
                    "targetName": "namedEntitiesV3"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
            "name": "NamedEntitiesShaper",
            "description": "NamedEntitiesShaper",
            "context": "/document/namedEntitiesV3",
            "inputs": [
                {
                    "name": "old_format",
                    "sourceContext": "/document/namedEntitiesV3/*",
                    "inputs": [
                        {
                            "name": "value",
                            "source": "/document/namedEntitiesV3/*/text"
                        },
                        {
                            "name": "offset",
                            "source": "/document/namedEntitiesV3/*/offset"
                        },
                        {
                            "name": "category",
                            "source": "/document/namedEntitiesV3/*/category"
                        },
                        {
                            "name": "confidence",
                            "source": "/document/namedEntitiesV3/*/confidenceScore"
                        }
                    ]
                }
            ],
            "outputs": [
                {
                    "name": "output",
                    "targetName": "namedEntities"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.V3.EntityLinkingSkill",
            "defaultLanguageCode": "en",
            "minimumPrecision": 0.1,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/content"
                }
            ],
            "outputs": [
                {
                    "name": "entities",
                    "targetName": "linkedEntities"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
            "name": "LinkedEntitiesShaper",
            "description": "LinkedEntitiesShaper",
            "context": "/document/linkedEntitiesV3",
            "inputs": [
                {
                    "name": "old_format",
                    "sourceContext": "/document/linkedEntitiesV3/*",
                    "inputs": [
                        {
                            "name": "name",
                            "source": "/document/linkedEntitiesV3/*/name"
                        },
                        {
                            "name": "wikipediaId",
                            "source": "/document/linkedEntitiesV3/*/id"
                        },
                        {
                            "name": "wikipediaLanguage",
                            "source": "/document/linkedEntitiesV3/*/language"
                        },
                        {
                            "name": "wikipediaUrl",
                            "source": "/document/linkedEntitiesV3/*/url"
                        },
                        {
                            "name": "bingId",
                            "source": "/document/linkedEntitiesV3/*/bingId"
                        },
                        {
                            "name": "matches",
                            "source": "/document/linkedEntitiesV3/*/matches"
                        }
                    ]
                }
            ],
            "outputs": [
                {
                    "name": "output",
                    "targetName": "entities"
                }
            ]
        }
        ```

## <a name="microsoftskillstextsentimentskill"></a>Microsoft.Skills.Text.SentimentSkill

### <a name="last-available-api-version"></a>사용 가능한 마지막 api 버전

2021-04-30-미리 보기

### <a name="end-of-support"></a>지원 종료

2024년 8월 31일

### <a name="recommendations"></a>권장 사항 

대신 [Microsoft.Skills.Text.V3.SentimentSkill](cognitive-search-skill-sentiment-v3.md)을 사용합니다. 개선된 모델을 제공하며. 의견 마이닝 또는 측면 기반 감정을 추가하는 옵션을 포함합니다. 기술이 훨씬 더 복잡해지면 출력도 매우 다릅니다.

[Microsoft.Skills.Text.V3.SentimentSkill](cognitive-search-skill-sentiment-v3.md)로 마이그레이션하려면 기술 정의에 다음 변경 사항 중 하나 이상을 수행해야 합니다. [기술 세트 API 업데이트](/rest/api/searchservice/update-skillset)를 사용하여 기술 정의를 업데이트할 수 있습니다.

> [!NOTE]
> Sentiment Skill V3의 기술 출력은 SentimentSkill 기반의 인덱스 정의와 호환되지 않습니다. 감정 기술을 새 버전으로 바꾸려면 인덱스 정의, 기술 세트(나중에 기술 입력 및/또는 지식 저장소 예측) 및 인덱서 출력 필드 매핑을 변경해야 합니다.

1. *(필수)*`@odata.type`을 `"#Microsoft.Skills.Text.SentimentSkill"`에서 `"#Microsoft.Skills.Text.V3.SentimentSkill"`로 변경

2. *(필수)* Sentiment Skill V3은 전체 텍스트에 대해 `positive`, `neutral` 및 `negative` 점수를 제공하고 전체 텍스트의 각 문장에 대해 동일한 점수를 제공하지만, 이전 SentimentSkill은 전체 텍스트에 대해 0.0(음수)~1.0(양수) 범위의 단일 이중만 제공했습니다. 단일 점수 대신 세 개의 이중 값을 허용하도록 인덱스 정의를 업데이트하고, 모든 다운스트림 기술 입력, 지식 저장소 예측 및 출력 필드 매핑이 이름 변경과 일치하는지 확인해야 합니다.

기존 SentimentSkill을 SentimentSkill V3로 완전히 교체하고 다운스트림 기술 입력, 지식 저장소 예측, 인덱서 출력 필드 매핑 및 인덱스 정의를 업데이트하여 새 출력 형식과 일치하도록 하고, 모든 문서가 앞으로 일관된 감정 결과를 갖도록 인덱서를 다시 설정합니다.

> [!NOTE]
> 최신 버전의 감정 기술을 사용하도록 보강 파이프라인을 업데이트 하는 추가 도움이 필요하거나 인덱서를 다시 설정하는 것이 옵션이 아닌 경우, 직접 작업할 수 있는 [새로운 지원 요청](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)을 여세요.

## <a name="microsoftskillstextnamedentityrecognitionskill"></a>Microsoft.Skills.Text.NamedEntityRecognitionSkill

### <a name="last-available-api-version"></a>사용 가능한 마지막 api 버전

\2017-11-11-Preview

### <a name="end-of-support"></a>지원 종료

2024년 8월 31일

### <a name="recommendations"></a>권장 사항 

대신 [Microsoft.Skills.Text.V3.EntityRecognitionSkill](cognitive-search-skill-entity-recognition-v3.md)을 사용합니다. NamedEntityRecognitionSkill의 대부분의 기능을 더 높은 품질로 제공합니다. 또한 복잡한 출력 필드에 다양한 정보가 있습니다.

[Microsoft.Skills.Text.V3.EntityRecognitionSkill](cognitive-search-skill-entity-recognition-v3.md)로 마이그레이션하려면 기술 정의에 다음 변경 사항 중 하나 이상을 수행해야 합니다. [기술 세트 API 업데이트](/rest/api/searchservice/update-skillset)를 사용하여 기술 정의를 업데이트할 수 있습니다.

1. *(필수)*`@odata.type`을 `"#Microsoft.Skills.Text.NamedEntityRecognitionSkill"`에서 `"#Microsoft.Skills.Text.V3.EntityRecognitionSkill"`로 변경

2. *(선택 사항)*`entities` 출력을 사용하는 경우 대신 `EntityRecognitionSkill V3`에서 `namedEntities` 복잡한 컬렉션 출력을 사용합니다. 새 `namedEntities` 복합 출력의 속성 이름이 약간 변경되었습니다.
    1. `value`이 `text`로 이름이 바뀌었습니다.
    2. `confidence`가 `confidenceScore`으로 이름이 바뀌었습니다.
    
    정확히 동일한 속성 이름을 생성해야 하는 경우에는 [ShaperSkill](cognitive-search-skill-shaper.md)을 추가하여 필요한 이름을 가진 출력의 모양을 변경해야 합니다. 예를 들어 이 ShaperSkill은 속성 이름을 이전 값으로 바꿉니다.

    ```json
    {
        "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
        "name": "NamedEntitiesShaper",
        "description": "NamedEntitiesShaper",
        "context": "/document/namedEntities",
        "inputs": [
            {
                "name": "old_format",
                "sourceContext": "/document/namedEntities/*",
                "inputs": [
                    {
                        "name": "value",
                        "source": "/document/namedEntities/*/text"
                    },
                    {
                        "name": "offset",
                        "source": "/document/namedEntities/*/offset"
                    },
                    {
                        "name": "category",
                        "source": "/document/namedEntities/*/category"
                    },
                    {
                        "name": "confidence",
                        "source": "/document/namedEntities/*/confidenceScore"
                    }
                ]
            }
        ],
        "outputs": [
            {
                "name": "output",
                "targetName": "entities"
            }
        ]
    }
    ```

3. *(선택 사항)* 명시적으로 `categories`를 지정하지 않는 경우 `EntityRecognitionSkill V3`은 `NamedEntityRecognitionSkill`에서 지원된 카테고리 외에 다른 형식의 카테고리를 반환할 수 있습니다. 이 동작이 바람직하지 않은 경우 명시적으로 `categories` 매개 변수를 `["Person", "Location", "Organization"]`으로 설정해야 합니다.

    _샘플 마이그레이션 정의_

    * 간단한 마이그레이션

        _(이전) NamedEntityRecognition 기술 정의_

        ```json
        {
            "@odata.type": "#Microsoft.Skills.Text.NamedEntityRecognitionSkill",
            "categories": [ "Person" ],
            "defaultLanguageCode": "en",
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/content"
                }
            ],
            "outputs": [
                {
                    "name": "persons",
                    "targetName": "people"
                }
            ]
        }
        ```

        _(이후) EntityRecognition 기술 V3 정의_

        ```json
        {
            "@odata.type": "#Microsoft.Skills.Text.V3.EntityRecognitionSkill",
            "categories": [ "Person" ],
            "defaultLanguageCode": "en",
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/content"
                }
            ],
            "outputs": [
                {
                    "name": "persons",
                    "targetName": "people"
                }
            ]
        }
        ```
    
    * 약간 복잡한 마이그레이션

        _(이전) NamedEntityRecognition 기술 정의_

        ```json
        {
            "@odata.type": "#Microsoft.Skills.Text.NamedEntityRecognitionSkill",
            "defaultLanguageCode": "en",
            "minimumPrecision": 0.1,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/content"
                }
            ],
            "outputs": [
                {
                    "name": "persons",
                    "targetName": "people"
                },
                {
                    "name": "entities"
                }
            ]
        }
        ```

        _(이후) EntityRecognition 기술 V3 정의_

        ```json
        {
            "@odata.type": "#Microsoft.Skills.Text.V3.EntityRecognitionSkill",
            "categories": [ "Person", "Location", "Organization" ],
            "defaultLanguageCode": "en",
            "minimumPrecision": 0.1,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/content"
                }
            ],
            "outputs": [
                {
                    "name": "persons",
                    "targetName": "people"
                },
                {
                    "name": "namedEntities"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
            "name": "NamedEntitiesShaper",
            "description": "NamedEntitiesShaper",
            "context": "/document/namedEntities",
            "inputs": [
                {
                    "name": "old_format",
                    "sourceContext": "/document/namedEntities/*",
                    "inputs": [
                        {
                            "name": "value",
                            "source": "/document/namedEntities/*/text"
                        },
                        {
                            "name": "offset",
                            "source": "/document/namedEntities/*/offset"
                        },
                        {
                            "name": "category",
                            "source": "/document/namedEntities/*/category"
                        },
                        {
                            "name": "confidence",
                            "source": "/document/namedEntities/*/confidenceScore"
                        }
                    ]
                }
            ],
            "outputs": [
                {
                    "name": "output",
                    "targetName": "entities"
                }
            ]
        }
        ```

## <a name="see-also"></a>참고 항목

+ [기본 제공 기술](cognitive-search-predefined-skills.md)
+ [기술 집합을 정의하는 방법](cognitive-search-defining-skillset.md)
+ [엔터티 인식 기술(V3)](cognitive-search-skill-entity-recognition-v3.md)
+ [감정 기술(V3)](cognitive-search-skill-sentiment-v3.md)