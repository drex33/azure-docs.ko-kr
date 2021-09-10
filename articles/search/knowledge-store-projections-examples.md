---
title: 프로젝션 정의
titleSuffix: Azure Cognitive Search
description: 지식 저장소에 테이블, 개체 및 파일 프로젝션을 만들어 인덱서 및 기술 자료 보강 파이프라인에서 보강된 콘텐츠를 저장합니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/10/2021
ms.openlocfilehash: aeb236b0b016cd86b492ce5bf0f64b14d1443fca
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122536183"
---
# <a name="define-projections-in-a-knowledge-store"></a>지식 저장소에서 프로젝션 정의

[프로젝션](knowledge-store-projection-overview.md)은 [지식 저장소](knowledge-store-concept-intro.md)에 있는 강화 문서의 물리적 표현이며 Azure Storage의 테이블, 개체 또는 파일 형식을 취합니다. 보강 문서를 효과적으로 사용하려면 구조가 필요합니다. 이 문서에서는 구조와 관계를 모두 살펴보고, 프로젝션 속성을 구축하는 방법과 생성된 프로젝션 유형 간에 데이터를 연결하는 방법을 알아봅니다.

[Shaper 기술 또는 인라인 모양](knowledge-store-projection-shape.md)을 사용하여 보강을 정의하고 데이터를 형성한 후 투영은 [knowledgeStore 정의](knowledge-store-concept-intro.md)에 지정됩니다. 컨텍스트의 경우 이 문서에서는 프로젝션에 적합한 도형을 생성하는 쉐이퍼 기술을 포함하여 보강을 정의하는 기술 예제를 참조합니다.

## <a name="enable-caching"></a>캐싱 설정

프로젝션을 개발할 때 비용 제어를 보장하도록 [인덱서 캐시 속성을 설정](search-howto-incremental-index.md)하는 것이 유용합니다. 인덱서 캐시가 설정되지 않은 경우 프로젝션을 편집하면 전체 문서가 다시 보강됩니다. 캐시가 설정되고 프로젝션만 업데이트되면 이전에 보강된 문서에 대한 기술 세트 실행으로 인해 새로운 Cognitive Services 요금이 발생하지 않습니다.

## <a name="example-enrichments"></a>보강 예제

데이터 셰이핑과 프로젝션의 교차를 이해하기 위해 다양한 구성을 탐색하기 위한 기준으로 다음 기술 세트를 사용합니다. 이 기술 세트는 원시 이미지와 텍스트를 모두 처리하여 모양과 투영에서 참조할 출력을 생성합니다.

```json
{
    "name": "azureblob-skillset",
    "description": "Skillset that enriches blob data found in "merged_content". The enrichment granularity is a document.",
    "skills": [
        {
            "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
            "name": "#1",
            "description": null,
            "context": "/document/merged_content",
            "categories": [
                "Person",
                "Quantity",
                "Organization",
                "URL",
                "Email",
                "Location",
                "DateTime"
            ],
            "defaultLanguageCode": "en",
            "minimumPrecision": null,
            "includeTypelessEntities": null,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/merged_content"
                },
                {
                    "name": "languageCode",
                    "source": "/document/language"
                }
            ],
            "outputs": [
                {
                    "name": "persons",
                    "targetName": "people"
                },
                {
                    "name": "organizations",
                    "targetName": "organizations"
                },
                {
                    "name": "locations",
                    "targetName": "locations"
                },
                {
                    "name": "entities",
                    "targetName": "entities"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.KeyPhraseExtractionSkill",
            "name": "#2",
            "description": null,
            "context": "/document/merged_content",
            "defaultLanguageCode": "en",
            "maxKeyPhraseCount": null,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/merged_content"
                },
                {
                    "name": "languageCode",
                    "source": "/document/language"
                }
            ],
            "outputs": [
                {
                    "name": "keyPhrases",
                    "targetName": "keyphrases"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
            "name": "#3",
            "description": null,
            "context": "/document",
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/merged_content"
                }
            ],
            "outputs": [
                {
                    "name": "languageCode",
                    "targetName": "language"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.MergeSkill",
            "name": "#4",
            "description": null,
            "context": "/document",
            "insertPreTag": " ",
            "insertPostTag": " ",
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/content"
                },
                {
                    "name": "itemsToInsert",
                    "source": "/document/normalized_images/*/text"
                },
                {
                    "name": "offsets",
                    "source": "/document/normalized_images/*/contentOffset"
                }
            ],
            "outputs": [
                {
                    "name": "mergedText",
                    "targetName": "merged_content"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Vision.OcrSkill",
            "name": "#5",
            "description": null,
            "context": "/document/normalized_images/*",
            "textExtractionAlgorithm": "printed",
            "lineEnding": "Space",
            "defaultLanguageCode": "en",
            "detectOrientation": true,
            "inputs": [
                {
                    "name": "image",
                    "source": "/document/normalized_images/*"
                }
            ],
            "outputs": [
                {
                    "name": "text",
                    "targetName": "text"
                },
                {
                    "name": "layoutText",
                    "targetName": "layoutText"
                }
            ]
        }
    ],
    "cognitiveServices": {
        "@odata.type": "#Microsoft.Azure.Search.CognitiveServicesByKey",
        "description": "A Cognitive Services resource in the same region as Search.",
        "key": "<COGNITIVE SERVICES All-in-ONE KEY>"
    },
    "knowledgeStore": null
}
```

## <a name="example-shaper-skill"></a>쉐이퍼 기술 예제

쉐이퍼 기술은 콘텐츠를 만드는 대신 보강된 콘텐츠로 작업하기 위한 유틸리티입니다. 기술 테이블에 쉐이퍼를 추가하면 Table Storage에 프로젝션할 수 있는 사용자 지정 셰이프를 만들 수 있습니다. 사용자 지정 셰이프가 없으면 프로젝션은 테이블에 적합하지 않은 단일 노드(출력당 하나의 프로젝션)를 참조하는 것으로 제한됩니다. 사용자 지정 셰이프를 만들면 다양한 요소가 단일 테이블로 프로젝션되거나 테이블 컬렉션에 분할 및 분산될 수 있는 새 논리적 전체로 집계됩니다. 

이 예에서 사용자 지정 셰이프는 Blob 메타데이터와 식별된 엔터티 및 핵심 구를 결합합니다. 사용자 지정 셰이프는 `projectionShape`라고 하며 `/document` 아래에서 부모로 지정됩니다. 

셰이핑의 한 가지 용도는 모든 보강 노드가 잘 구성된 JSON으로 표현되도록 하는 것입니다. 이는 지식 저장소로 프로젝션하는 데 필요합니다. 보강 트리에 잘 구성된 JSON이 아닌 노드가 포함된 경우(예: 보강이 문자열과 같은 기본 요소의 부모로 지정된 경우) 특히 그렇습니다.

마지막 두 개의 노드인 `KeyPhrases` 및 `Entities`를 확인합니다. 이러한 노드는 `sourceContext`를 사용하여 유효한 JSON 개체에 래핑됩니다. `keyphrases` 및 `entities`는 기본 요소에 대한 보강이며 프로젝션되기 전에 유효한 JSON으로 변환되어야 하므로 필수 항목입니다.

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "name": "ShaperForTables",
    "description": null,
    "context": "/document",
    "inputs": [
        {
            "name": "metadata_storage_content_type",
            "source": "/document/metadata_storage_content_type",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "metadata_storage_name",
            "source": "/document/metadata_storage_name",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "metadata_storage_path",
            "source": "/document/metadata_storage_path",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "metadata_content_type",
            "source": "/document/metadata_content_type",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "keyPhrases",
            "source": null,
            "sourceContext": "/document/merged_content/keyphrases/*",
            "inputs": [
                {
                    "name": "KeyPhrases",
                    "source": "/document/merged_content/keyphrases/*"
                }

            ]
        },
        {
            "name": "Entities",
            "source": null,
            "sourceContext": "/document/merged_content/entities/*",
            "inputs": [
                {
                    "name": "Entities",
                    "source": "/document/merged_content/entities/*/name"
                }

            ]
        }
    ],
    "outputs": [
        {
            "name": "output",
            "targetName": "projectionShape"
        }
    ]
}
```

위의 셰이퍼 기술을 기술 세트에 추가합니다. 

```json
    "name": "azureblob-skillset",
    "description": "A friendly description of the skillset goes here.",
    "skills": [
        {
            Shaper skill goes here
            }
        ],
    "cognitiveServices":  "A key goes here",
    "knowledgeStore": []
}  
```

## <a name="projecting-to-tables"></a>테이블에 프로젝션

Azure Storage의 테이블에 프로젝션은 테이블에서 읽고 프로젝션 중에 생성된 키를 기반으로 관계를 검색할 수 있는 Power BI와 같은 도구를 사용하여 보고 및 분석하는 데 유용합니다. 대시보드를 빌드하려는 경우 관련 데이터가 있으면 해당 작업이 간소화됩니다.

테이블의 스키마는 부분적으로 프로젝션(테이블 이름 및 키)과 테이블의 모양(열)을 제공하는 소스에 의해 지정됩니다.

> [!NOTE] 
> 테이블 프로젝션은 Azure Storage에서 적용하는 스토리지 제한에 따라 관리되는 Azure Storage 테이블입니다. 자세한 내용은 [테이블 스토리지 제한](/rest/api/storageservices/understanding-the-table-service-data-model)을 참조하세요. 엔터티 크기는 1MB를 초과할 수 없으며 단일 속성은 64KB를 초과할 수 없습니다. 이러한 제약 조건으로 인해 테이블은 많은 수의 작은 엔터티를 저장하는 데 좋은 솔루션이 됩니다.

위의 예를 참조하면 테이블 프로젝션에서 참조할 수 있는 보강 및 데이터 모양의 알려진 양이 있습니다. 아래 테이블 프로젝션에서 `tableName`, `source` 및 `generatedKeyName` 속성을 설정하여 세 개의 테이블을 정의합니다.

| 속성 | 설명 |
|----------|-------------|
| tableName | (필수) Azure Table Storage 만든 새 테이블의 이름을 결정합니다. 테이블은 partitionKey 및 rowKey 열로 만들어집니다. |
| source | 보강 트리의 노드에 대한 경로입니다. 테이블 프로젝션은 복잡하기 때문에(여러 열을 채우는 여러 노드가 있는 경우) 경로는 노드를 포함하는 데이터 셰이프로 확인되어야 합니다. 쉐이퍼 기술의 출력은 이 속성의 가장 일반적인 값이지만 프로젝션 내에서 인라인 셰이핑을 사용하여 도형을 만들 수도 있습니다. |
| generatedKeyName | 모든 행은 시스템에서 생성된 값으로 고유하게 식별됩니다. 이 속성은 해당 값을 포함하는 열의 이름을 결정합니다. 이 속성을 생략하면 테이블 이름과 ‘키’를 명명 규칙으로 사용하는 열이 자동으로 만들어집니다. |

이러한 세 테이블은 모두 생성된 키와 공유 부모`/document/projectionShape`를 통해 관련됩니다.

```json
"knowledgeStore" : {
    "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
    "projections": [
        {
            "tables": [
                {
                    "tableName": "tblDocument",
                    "generatedKeyName": "Documentid",
                    "source": "/document/projectionShape"
                },
                {
                    "tableName": "tblKeyPhrases",
                    "generatedKeyName": "KeyPhraseid",
                    "source": "/document/projectionShape/keyPhrases/*"
                },
                {
                    "tableName": "tblEntities",
                    "generatedKeyName": "Entityid",
                    "source": "/document/projectionShape/Entities/*"
                }
            ],
            "objects": [],
            "files": []
        }
    ]
}
```

다음 단계를 수행하여 작업을 처리할 수 있습니다.

1. 지식 저장소의 `storageConnectionString` 속성을 유효한 V2 범용 저장소 계정 연결 문자열로 설정합니다.  

1. PUT 요청을 실행하여 기술 세트를 업데이트합니다.

1. 기술 세트를 업데이트한 후 인덱서를 실행합니다. 

이제 세 개의 테이블이 있는 작업 프로젝션이 있습니다. [이러한 테이블을 Power BI로 가져오면](knowledge-store-connect-power-bi.md) Power BI가 관계를 자동으로 검색해야 합니다.

다음 예제로 이동하기 전에 테이블 프로젝션의 측면을 다시 살펴보고 데이터 조각화 및 관련 메커니즘을 이해해 보겠습니다.

### <a name="slicing-a-table-into-multiple-child-tables"></a>테이블을 여러 자식 테이블로 자르기

조각화는 전체 통합 셰이프를 구성 부분으로 세분화하는 기술입니다. 결과는 개별적으로 작업할 수 있는 별도의 관련 테이블로 구성됩니다.

예제에서 `projectionShape`는 통합 셰이프(또는 보강 노드)입니다. 프로젝션 정의에서 `projectionShape`는 추가 테이블로 분리되며, 이를 통해 도형(`keyPhrases` 및 `Entities`)의 일부를 끌어올 수 있습니다. Power BI에서 이 기능은 여러 엔터티와 keyPhrases가 각 문서에 연결되어 있으므로 유용하며, 엔터티와 keyPhrases를 범주화된 데이터로 볼 수 있으면 더 많은 정보를 얻을 수 있습니다.

조각화는 부모 테이블과 자식 테이블 간의 관계를 암시적으로 생성하며, 부모 테이블의 `generatedKeyName`을 사용하여 자식 테이블에 동일한 이름의 열을 생성합니다. 

### <a name="naming-relationships"></a>관계 이름 지정

`generatedKeyName`및 `referenceKeyName` 속성은 여러 테이블 또는 프로젝션 형식 간에 데이터를 연관하는 데 사용됩니다. 자식 테이블/프로젝션의 각 행에는 부모를 가리키는 속성이 있습니다. 자식의 열 또는 속성 이름은 부모에서 `referenceKeyName`입니다. `referenceKeyName`이 제공되지 않은 경우 서비스에서 기본값을 부모의 `generatedKeyName`으로 지정합니다. 

Power BI는 이러한 생성된 키를 사용하여 테이블 내의 관계를 검색합니다. 자식 테이블의 열이 다른 이름으로 지정 되어야 하는 경우 부모 테이블에서 `referenceKeyName` 속성을 설정합니다. 한 가지 예를 들면 `generatedKeyName`을 pbiDocument 테이블의 ID로 설정하고 `referenceKeyName`을 DocumentID로 설정하는 것입니다. 그러면 문서 ID가 포함된 pbiEntities 및 pbiKeyPhrases 테이블의 열이 DocumentID로 명명됩니다.

## <a name="projecting-to-objects"></a>개체에 프로젝션

개체 프로젝션은 더 간단하게 정의할 수 있으며 전체 문서를 프로젝션할 때 사용됩니다. 개체 프로젝션은 컨테이너의 단일 프로젝션으로 제한되며 조각화할 수 없습니다.

객체 투영을 정의하려면 `projections`에서 `objects` 배열을 사용하십시오. 쉐이퍼 기술을 사용하여 새 도형을 생성하거나 개체 프로젝션의 인라인 셰이핑을 사용할 수 있습니다. 테이블 예제에서는 도형을 만들고 자르는 방법을 보여주지만, 이 예제에서는 인라인 셰이핑을 사용하는 방법을 보여줍니다. 

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

## <a name="projecting-to-file"></a>파일에 프로젝션

파일 프로젝션은 원본 문서에서 추출된 이미지이거나 보강 프로세스에서 프로젝션될 수 있는 보강의 출력입니다. 개체 프로젝션과 비슷한 파일 프로젝션은 Azure Storage에서 Blob으로 구현되고 이미지를 포함합니다. 

파일 프로젝션을 생성하려면 프로젝션 개체에서 `files` 배열을 사용합니다. 이 예제에서는 문서에서 추출한 모든 이미지를 `myImages`라는 컨테이너로 프로젝션합니다.

```json
"knowledgeStore" : {
    "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
    "projections": [
        {
            "tables": [ ],
            "objects": [ ],
            "files": [
                {
                    "storageContainer": "myImages",
                    "source": "/document/normalized_images/*"
                }
            ]
        }
    ]
}
```

## <a name="projecting-to-multiple-types"></a>여러 형식으로 프로젝션

복잡한 시나리오에서는 프로젝션 형식 간에 콘텐츠를 프로젝션해야 할 수 있습니다. 예를 들어 핵심 문구 및 엔터티와 같은 일부 데이터를 테이블에 프로젝션해야 하는 경우 텍스트 및 레이아웃 텍스트의 OCR 결과를 개체로 저장한 후에 이미지를 파일로 프로젝션합니다. 

여러 프로젝션 형식에 대한 단계:

1. 각 문서에 행이 있는 테이블을 만듭니다.
1. 이 테이블에서 행으로 식별되는 각 키 구문을 사용하여 문서 테이블과 관련된 테이블을 만듭니다.
1. 이 테이블에서 행으로 식별되는 각 엔터티를 사용하여 문서 테이블과 관련된 테이블을 만듭니다.
1. 각 이미지에 대한 레이아웃 텍스트를 사용하여 개체 프로젝션을 만듭니다.
1. 추출된 각 이미지를 프로젝션하는 파일 프로젝션을 만듭니다.
1. 문서 테이블에 대한 참조, 레이아웃 텍스트가 있는 개체 투영 및 파일 투영을 포함하는 상호 참조 테이블을 만듭니다.

### <a name="shape-data-for-cross-projection"></a>교차 프로젝션을 위한 셰이프 데이터

이러한 프로젝션에 필요한 셰이프를 가져오려면 먼저 `crossProjection`이라는 모양의 개체를 만드는 새로운 셰이퍼 기술을 추가합니다. 

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "name": "ShaperForCrossProjection",
    "description": null,
    "context": "/document",
    "inputs": [
        {
            "name": "metadata_storage_name",
            "source": "/document/metadata_storage_name",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "keyPhrases",
            "source": null,
            "sourceContext": "/document/merged_content/keyphrases/*",
            "inputs": [
                {
                    "name": "KeyPhrases",
                    "source": "/document/merged_content/keyphrases/*"
                }

            ]
        },
        {
            "name": "entities",
            "source": null,
            "sourceContext": "/document/merged_content/entities/*",
            "inputs": [
                {
                    "name": "Entities",
                    "source": "/document/merged_content/entities/*/name"
                }

            ]
        },
        {
            "name": "images",
            "source": null,
            "sourceContext": "/document/normalized_images/*",
            "inputs": [
                {
                    "name": "image",
                    "source": "/document/normalized_images/*"
                },
                {
                    "name": "layoutText",
                    "source": "/document/normalized_images/*/layoutText"
                },
                {
                    "name": "ocrText",
                    "source": "/document/normalized_images/*/text"
                }
                ]
        }
 
    ],
    "outputs": [
        {
            "name": "output",
            "targetName": "crossProjection"
        }
    ]
}
```

### <a name="define-table-object-and-file-projections"></a>테이블, 개체 및 파일 프로젝션 정의

통합된 crossProjection 개체에서 개체를 여러 테이블로 분할하고 OCR 출력을 Blob으로 캡처한 다음, 이미지를 파일(Blob Storage에도 있음)로 저장합니다.

```json
"knowledgeStore" : {
    "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
    "projections": [
            {
            "tables": [
                {
                    "tableName": "crossDocument",
                    "generatedKeyName": "Id",
                    "source": "/document/crossProjection"
                },
                {
                    "tableName": "crossEntities",
                    "generatedKeyName": "EntityId",
                    "source": "/document/crossProjection/entities/*"
                },
                {
                    "tableName": "crossKeyPhrases",
                    "generatedKeyName": "KeyPhraseId",
                    "source": "/document/crossProjection/keyPhrases/*"
                },
                {
                    "tableName": "crossReference",
                    "generatedKeyName": "CrossId",
                    "source": "/document/crossProjection/images/*"
                }
                    
            ],
            "objects": [
                {
                    "storageContainer": "crossobject",
                    "generatedKeyName": "crosslayout",
                    "source": null,
                    "sourceContext": "/document/crossProjection/images/*/layoutText",
                    "inputs": [
                        {
                            "name": "OcrLayoutText",
                            "source": "/document/crossProjection/images/*/layoutText"
                        }
                    ]
                }
            ],
            "files": [
                {
                    "storageContainer": "crossimages",
                    "generatedKeyName": "crossimages",
                    "source": "/document/crossProjection/images/*/image"
                }
            ]
        }
    ]
}
```

개체 투영에는 각 투영에 대한 컨테이너 이름이 필요합니다. 개체 프로젝션 및 파일 프로젝션에서는 컨테이너를 공유할 수 없습니다. 

### <a name="relationships-among-table-object-and-file-projections"></a>테이블, 개체 및 파일 프로젝션 간의 관계

또한 이 예제에서는 프로젝션의 또 다른 기능을 강조합니다. 동일한 프로젝션 개체 내에서 여러 유형의 프로젝션을 정의하면 서로 다른 형식(테이블, 개체, 파일) 내부 및 전체에서 표현되는 관계가 있습니다. 이를 통해 문서의 테이블 행으로 시작하고 개체 프로젝션에서 해당 문서 내의 이미지에 대한 모든 OCR 텍스트를 찾을 수 있습니다. 

관련 데이터를 원하지 않는 경우 다른 프로젝션 개체에서 프로젝션을 정의합니다. 예를 들어 다음 코드 조각은 테이블과 개체(OCR 텍스트) 프로젝션 간의 관계를 제외하고 테이블의 관련 관계를 생성합니다. 

프로젝션 그룹은 서로 다른 요구에 대해 동일한 데이터를 서로 다른 모양으로 프로젝션하려는 경우에 유용합니다. 예를 들어 Power BI 대시보드에 대한 프로젝션 그룹과 사용자 지정 기술에 래핑된 기계 학습 모델을 교육하는 데 사용되는 데이터를 캡처하기 위한 또 다른 프로젝션 그룹이 있습니다.

여러 유형의 프로젝션을 빌드할 때 파일 및 개체 프로젝션이 먼저 생성되고 경로가 테이블에 추가됩니다.

```json
"knowledgeStore" : {
    "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
    "projections": [
        {
            "tables": [
                {
                    "tableName": "unrelatedDocument",
                    "generatedKeyName": "Documentid",
                    "source": "/document/projectionShape"
                },
                {
                    "tableName": "unrelatedKeyPhrases",
                    "generatedKeyName": "KeyPhraseid",
                    "source": "/document/projectionShape/keyPhrases"
                }
            ],
            "objects": [
                
            ],
            "files": []
        }, 
        {
            "tables": [],
            "objects": [
                {
                    "storageContainer": "unrelatedocrtext",
                    "source": null,
                    "sourceContext": "/document/normalized_images/*/text",
                    "inputs": [
                        {
                            "name": "ocrText",
                            "source": "/document/normalized_images/*/text"
                        }
                    ]
                },
                {
                    "storageContainer": "unrelatedocrlayout",
                    "source": null,
                    "sourceContext": "/document/normalized_images/*/layoutText",
                    "inputs": [
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

## <a name="common-issues"></a>일반적인 문제

프로젝션을 정의할 때 예상치 못한 결과를 초래할 수 있는 몇 가지 일반적인 문제가 있습니다. 지식 저장소의 출력이 예상과 다른 경우 이러한 문제를 확인합니다.

+ 문자열 보강을 유효한 JSON으로 셰이핑하지 않습니다. 예를 들어 키 구로 보강된 `merged_content` 문자열이 보강되면 보강된 속성은 보강 트리 내에서 `merged_content`의 자식으로 표시됩니다. 기본 표현은 잘 구성된 JSON이 아닙니다. 따라서 프로젝션 시 이름과 값이 있는 유효한 JSON 개체로 보강을 변환해야 합니다.

+ 원본 경로의 끝에 ```/*```를 생략합니다. 프로젝션의 원본이 `/document/projectionShape/keyPhrases`인 경우 키 구 배열은 단일 개체/행으로 프로젝션됩니다. 대신의 원본 경로를 `/document/projectionShape/keyPhrases/*`로 설정하여 각 키 구에 대해 단일 행 또는 개체를 생성합니다.

+ 경로 구문 오류입니다. 경로 선택기는 대/소문자를 구분 하며 선택기에 대해 정확한 대/소문자를 사용하지 않는 경우 입력 경고가 누락될 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 문서의 예제에서는 프로젝션을 만드는 방법에 대한 일반적인 패턴을 보여줍니다. 개념을 이해했으므로 이제 특정 시나리오에 대한 프로젝션을 빌드할 준비가 되었습니다.

새 기능을 탐색할 때 다음 단계로 증분 보강을 고려합니다. 증분 보강은 캐싱을 기반으로 하므로 모든 보강을 다시 사용할 수 있으며 기술 세트 수정의 영향을 받지 않습니다. 이는 OCR 및 이미지 분석이 포함된 파이프라인에 특히 유용합니다.

> [!div class="nextstepaction"]
> [증분 보강을 위한 캐싱 구성](search-howto-incremental-index.md)
