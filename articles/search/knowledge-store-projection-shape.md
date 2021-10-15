---
title: 지식 저장소를 위한 데이터 셰이핑
titleSuffix: Azure Cognitive Search
description: 데이터 셰이프를 만들고 이를 프로젝션에 전달하여 지식 저장소에서 데이터 구조를 정의합니다.
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/15/2021
ms.openlocfilehash: 26f70e4750d29231b3f139ecd617b43071e369bb
ms.sourcegitcommit: 4abfec23f50a164ab4dd9db446eb778b61e22578
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130066094"
---
# <a name="shaping-data-for-projection-into-a-knowledge-store"></a>지식 저장소에 프로젝션할 데이터 셰이핑

Azure Cognitive Search에서 “데이터 셰이핑”은 Azure Storage에서 테이블, 개체 및 파일에 프로젝션하려는 콘텐츠의 데이터 표현을 만드는 [지식 저장소 워크플로](knowledge-store-concept-intro.md)의 한 단계를 설명합니다.

기술이 실행됨에 따라 노드 계층 구조에서 보강 트리에 출력이 기록됩니다. 보강 트리 전체를 보고 사용해야 할 수도 있지만 번역된 텍스트 또는 추출된 엔티티와 관련된 노드를 특정 테이블에 배치할 때와 같이 여러 시나리오에 따라 노드 하위 집합을 만드는 경우처럼 더 세부적인 조정이 필요한 경우가 더 많습니다.

자체적으로 보강 트리는 지식 저장소에 해당 콘텐츠가 표시된 방식을 알려주는 논리를 포함하지 않습니다. 데이터 셰이프는 각 테이블, 개체, 파일 프로젝션에 저장되는 항목에 대한 스키마를 제공하여 이러한 격차를 메워줍니다. 데이터 셰이프는 보강된 데이터에 대한 사용자 지정 정의 또는 보기와 같습니다. 필요한 만큼 셰이프를 만들고 이를 지식 저장소 정의에서 프로젝션에 할당할 수 있습니다. 

## <a name="approaches-for-creating-shapes"></a>셰이프 만들기에 대한 접근 방법

지식 저장소에 프로젝션될 수 있도록 보강된 콘텐츠를 셰이핑하는 데에는 두 가지 방법이 있습니다.

+ [셰이퍼 기술](cognitive-search-skill-shaper.md)을 사용하여 보강 트리에서 프로젝션에 대해 명시적으로 사용되는 노드를 만듭니다. 대부분의 기술은 새 콘텐츠를 만듭니다. 반면에 셰이퍼 기술은 기존 노드에서 작동하며 일반적으로 여러 노드를 하나의 복잡한 개체로 병합합니다. 이 기술은 여러 노드의 출력을 물리적으로 테이블의 열로 표현하려는 테이블에 유용합니다. 

+ 프로젝션 정의 자체 내에서 인라인 셰이프를 사용합니다.

셰이퍼 기술을 사용하면 여러 프로젝션 또는 심지어 다른 기술에서도 사용될 수 있도록 셰이프가 외부화됩니다. 또한 보강 트리의 모든 변형이 해당 기술 내에 포함되고 출력이 재사용 가능한 개체가 되도록 보장합니다. 반대로, 인라인 셰이핑을 사용하면 필요한 셰이프를 만들 수 있지만 이는 익명 개체이며 정의된 프로젝션에만 사용할 수 있습니다.

두 방식을 함께 사용할 수도 있고 따로 사용할 수도 있습니다. 이 문서에서는 테이블 프로젝션을 위한 셰이퍼 기술과 핵심 문구 테이블 프로젝션을 사용한 인라인 셰이핑의 두 방식을 모두 보여줍니다.

## <a name="use-a-shaper-skill"></a>셰이퍼 기술 사용

셰이퍼 기술은 일반적으로 기술 집합의 끝에 배치되며, 프로젝션에 전달하려는 데이터의 보기를 만듭니다. 이 예제에서는 "reviews_text", "reviews_title", "AzureSearch_DocumentKey" 노드와 페이징된 검토의 감정 점수와 핵심 문구가 포함된 “tableprojection”이라는 셰이프를 만듭니다. 

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "name": "#5",
    "description": null,
    "context": "/document",
    "inputs": [        
        {
            "name": "reviews_text",
            "source": "/document/reviews_text",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "reviews_title",
            "source": "/document/reviews_title",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "AzureSearch_DocumentKey",
            "source": "/document/AzureSearch_DocumentKey",
            "sourceContext": null,
            "inputs": []
        },  
        {
            "name": "pages",
            "source": null,
            "sourceContext": "/document/reviews_text/pages/*",
            "inputs": [
                {
                    "name": "Sentiment",
                    "source": "/document/reviews_text/pages/*/Sentiment",
                    "sourceContext": null,
                    "inputs": []
                },
                {
                    "name": "LanguageCode",
                    "source": "/document/Language",
                    "sourceContext": null,
                    "inputs": []
                },
                {
                    "name": "Page",
                    "source": "/document/reviews_text/pages/*",
                    "sourceContext": null,
                    "inputs": []
                },
                {
                    "name": "keyphrase",
                    "sourceContext": "/document/reviews_text/pages/*/Keyphrases/*",
                    "inputs": [
                        {
                            "source": "/document/reviews_text/pages/*/Keyphrases/*",
                            "name": "Keyphrases"
                        }
                    ]
                }
            ]
        }
    ],
    "outputs": [
        {
            "name": "output",
            "targetName": "tableprojection"
        }
    ]
}
```

### <a name="sourcecontext-property"></a>SourceContext 속성

셰이퍼 기술 내에서 입력은 `sourceContext` 요소를 포함할 수 있습니다. 이 동일한 속성은 프로젝션의 인라인 셰이프에도 사용될 수 있습니다. 

`sourceContext`는 보강 파이프라인에서 다중 수준의 중첩 개체를 생성하기 위해 사용됩니다. 입력이 기술 컨텍스트와 ‘다른’ 컨텍스트에 있는 경우 *sourceContext* 를 사용합니다. *sourceContext* 를 사용하려면 원본으로 지정된 특정 요소를 사용하여 중첩된 입력을 정의해야 합니다. 

위 예제에서 감정 분석 및 핵심 문구 추출은 보다 효율적인 분석을 위해 페이지로 분할된 텍스트에서 수행되었습니다. 점수 및 문구를 테이블에 프로젝션한다고 가정하면 이제 점수 및 문구를 제공하는 중첩된 입력으로 컨텍스트를 설정해야 합니다.

### <a name="projecting-a-shape-into-multiple-tables"></a>여러 테이블에 셰이프 프로젝션

위에서 `outputs` 섹션에 `tableprojection` 노드를 정의한 상태로 `tableprojection` 노드의 각 부분을 개별적인 관련된 테이블로 분할할 수 있습니다.

```json
"projections": [
    {
        "tables": [
            {
                "tableName": "hotelReviewsDocument",
                "generatedKeyName": "Documentid",
                "source": "/document/tableprojection"
            },
            {
                "tableName": "hotelReviewsPages",
                "generatedKeyName": "Pagesid",
                "source": "/document/tableprojection/pages/*"
            },
            {
                "tableName": "hotelReviewsKeyPhrases",
                "generatedKeyName": "KeyPhrasesid",
                "source": "/document/tableprojection/pages/*/keyphrase/*"
            }
        ]
    }
]
```

## <a name="inline-shape-for-table-projections"></a>테이블 프로젝션에 대한 인라인 셰이프

인라인 셰이핑은 프로젝션 정의 자체 내에서 새 셰이프를 형성하는 기능입니다. 인라인 셰이핑의 특성은 다음과 같습니다.

+ 셰이프는 셰이프가 포함된 프로젝션에서만 사용될 수 있습니다.
+ 셰이프는 셰이퍼 기술이 생성하는 것과 동일할 수 있습니다.

인라인 셰이프는 `sourceContext` 및 `inputs`를 사용하여 생성됩니다.

| 속성 | 설명 |
|----------|-------------|
| sourceContext | 프로젝션의 루트를 설정합니다.  |
| 입력 | 각 입력은 테이블의 열입니다. Name은 열 이름입니다. Source는 값을 제공하는 보강 노드입니다. |

이전 예제와 동일한 데이터를 프로젝션하기 위해, 인라인 프로젝션 옵션은 다음과 같습니다.

```json
"projections": [
    {
        "tables": [
            {
                "tableName": "hotelReviewsInlineDocument",
                "generatedKeyName": "Documentid",
                "sourceContext": "/document",     
                "inputs": [
                    {
                        "name": "reviews_text",
                        "source": "/document/reviews_text"
                    },
                    {
                        "name": "reviews_title",
                        "source": "/document/reviews_title"
                    },
                    {
                        "name": "AzureSearch_DocumentKey",
                        "source": "/document/AzureSearch_DocumentKey"
                    }                             
                ]
            },
            {
                "tableName": "hotelReviewsInlinePages",
                "generatedKeyName": "Pagesid",
                "sourceContext": "/document/reviews_text/pages/*",
                "inputs": [
                        {
                    "name": "Sentiment",
                    "source": "/document/reviews_text/pages/*/Sentiment"
                    },
                    {
                        "name": "LanguageCode",
                        "source": "/document/Language"
                    },
                    {
                        "name": "Page",
                        "source": "/document/reviews_text/pages/*"
                    }
                ]
            },
            {
                "tableName": "hotelReviewsInlineKeyPhrases",
                "generatedKeyName": "KeyPhraseId",
                "sourceContext": "/document/reviews_text/pages/*/Keyphrases/*",
                "inputs": [
                    {
                        "name": "Keyphrases",
                        "source": "/document/reviews_text/pages/*/Keyphrases/*"
                    }
                ]
            }
        ]
    }
]
```
  
두 접근 방식 모두에서 관찰되는 한 가지 항목은 “Keyphrases”의 값이 “sourceContext”를 사용하여 프로젝션되는 방법입니다. 문자열 컬렉션을 포함하는 “Keyphrases” 노드는 그 자체로 페이지 텍스트의 자식입니다. 하지만 프로젝션에 JSON 개체가 필요하고 페이지가 기본 형식(문자열)이기 때문에 이름이 지정된 속성을 사용하여 핵심 문구를 개체로 래핑하기 위해 “sourceContext”가 사용됩니다. 이 방법은 심지어 기본 형식도 독립적으로 프로젝션할 수 있습니다.

<a name="inline-shape"></a>

## <a name="inline-shape-for-object-projections"></a>개체 프로젝션에 대한 인라인 셰이프

쉐이퍼 기술을 사용하여 새 도형을 생성하거나 개체 프로젝션의 인라인 셰이핑을 사용할 수 있습니다. 테이블 예제에서는 도형을 만들고 자르는 방법을 보여주지만, 이 예제에서는 인라인 셰이핑을 사용하는 방법을 보여줍니다. 

인라인 셰이핑은 프로젝션에 대한 입력 정의에서 새 도형을 만드는 기능입니다. 인라인 셰이핑은 셰이퍼 기술이 생성하는 것과 동일한 익명 개체를 생성합니다(이 경우 `projectionShape`). 인라인 셰이핑은 다시 사용할 계획이 없는 도형을 정의하는 경우에 유용합니다.

프로젝션 속성은 배열입니다. 이 예제에서는 knowledgeStore 정의에 인라인 프로젝션이 포함된 새 프로젝션 인스턴스를 배열에 추가합니다. 인라인 프로젝션을 사용하는 경우 셰이퍼 기술을 생략할 수 있습니다.

```json
"knowledgeStore" : {
    "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
    "projections": [
            {
            "tables": [ ],
            "objects": [
                {
                    "storageContainer": "sampleobject",
                    "source": null,
                    "generatedKeyName": "myobject",
                    "sourceContext": "/document",
                    "inputs": [
                        {
                            "name": "metadata_storage_name",
                            "source": "/document/metadata_storage_name"
                        },
                        {
                            "name": "metadata_storage_path",
                            "source": "/document/metadata_storage_path"
                        },
                        {
                            "name": "content",
                            "source": "/document/content"
                        },
                        {
                            "name": "keyPhrases",
                            "source": "/document/merged_content/keyphrases/*"
                        },
                        {
                            "name": "entities",
                            "source": "/document/merged_content/entities/*/name"
                        },
                        {
                            "name": "ocrText",
                            "source": "/document/normalized_images/*/text"
                        },
                        {
                            "name": "ocrLayoutText",
                            "source": "/document/normalized_images/*/layoutText"
                        }
                    ]

                }
            ],
            "files": []
        }
    ]
}
```

## <a name="next-steps"></a>다음 단계

이 문서에서는 프로젝션 셰이프의 개념 및 원리에 대해 설명합니다. 다음 단계에서는 테이블, 개체 및 파일 프로젝션을 위해 셰이프가 어떻게 패턴으로 적용되는지 알아보세요.

> [!div class="nextstepaction"]
> [지식 저장소에서 프로젝션 정의](knowledge-store-projections-examples.md)
