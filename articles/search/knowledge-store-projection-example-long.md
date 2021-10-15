---
title: 프로젝션 예제
titleSuffix: Azure Cognitive Search
description: 지식 저장소의 콘텐츠 구조와 구성을 알려주는 복잡한 모양으로 풍부한 기술 자료의 출력을 투영하는 자세한 예제를 살펴봅니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/15/2021
ms.openlocfilehash: 5c5489e4e5edcfdb49c8edf4e795ce96bb8ada7d
ms.sourcegitcommit: 4abfec23f50a164ab4dd9db446eb778b61e22578
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130067911"
---
# <a name="detailed-example-of-shapes-and-projections-in-a-knowledge-store"></a>지식 저장소의 도형 및 프로젝션에 대한 자세한 예제

이 문서에서는 [지식 저장소](knowledge-store-concept-intro.md)에서 풍부한 기술 항목의 출력을 완전히 표현하는 데 필요한 셰이핑 및 프로젝션 단계를 안내하여 [개략적인 개념](knowledge-store-projection-overview.md) 및 [구문 기반 문서를](knowledge-store-projections-examples.md) 보완하는 자세한 예제를 제공합니다.

애플리케이션 요구 사항이 여러 기술 및 프로젝션을 요구하는 경우 이 예제를 통해 콘텐츠를 셰이프하고 프로젝션하는 방법을 더 잘 이해할 수 있습니다.

## <a name="example-skillset"></a>기술 예제

도형과 프로젝션 간의 교집합을 이해하려면 보강된 콘텐츠를 만드는 다음 기술집합을 검토합니다. 이 기술 세트는 원시 이미지와 텍스트를 모두 처리하여 모양과 투영에서 참조할 출력을 생성합니다.

기술 출력(targetNames)에 주의해야 합니다. 보강된 문서 트리에 기록된 출력은 프로젝션 및 쉐이퍼 기술에서 참조됩니다.

```json
{
    "name": "azureblob-skillset",
    "description": "Skillset that enriches blob data found in "merged_content". The enrichment granularity is a document.",
    "skills": [
        {
            "@odata.type": "#Microsoft.Skills.Text.V3.EntityRecognitionSkill",
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

[쉐이퍼 기술은](cognitive-search-skill-shaper.md) 새 보강 콘텐츠를 만드는 대신 기존 보강 콘텐츠를 작업하기 위한 유틸리티입니다. 기술 테이블에 쉐이퍼를 추가하면 테이블 또는 Blob Storage에 프로젝션할 수 있는 사용자 지정 셰이프를 만들 수 있습니다. 사용자 지정 셰이프가 없으면 프로젝션은 테이블에 적합하지 않은 단일 노드(출력당 하나의 프로젝션)를 참조하는 것으로 제한됩니다. 사용자 지정 셰이프를 만들면 다양한 요소가 단일 테이블로 프로젝션되거나 테이블 컬렉션에 분할 및 분산될 수 있는 새 논리적 전체로 집계됩니다. 

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

### <a name="add-shapers-to-a-skillset"></a>기술 기술에 쉐이퍼 추가

이 문서의 시작 부분에 도입된 기술 예제에는 쉐이퍼 기술이 포함되지 않았지만 쉐이퍼 기술은 기술 기술에 속하며 끝에 배치되는 경우가 많습니다.

기술 기술 내에서 쉐이퍼 기술은 다음과 같이 보일 수 있습니다.

```json
    "name": "azureblob-skillset",
    "description": "A friendly description of the skillset goes here.",
    "skills": [
        {
            <Shaper skill goes here>
            }
        ],
    "cognitiveServices":  "A key goes here",
    "knowledgeStore": []
}  
```

## <a name="projecting-to-tables"></a>테이블에 프로젝션

위의 예를 참조하면 테이블 프로젝션에서 참조할 수 있는 보강 및 데이터 모양의 알려진 양이 있습니다. 아래 테이블 프로젝션에서 `tableName`, `source` 및 `generatedKeyName` 속성을 설정하여 세 개의 테이블을 정의합니다.

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

### <a name="test-your-work"></a>작업 테스트

다음 단계에 따라 프로젝션 정의를 확인할 수 있습니다.

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

## <a name="projecting-blob-documents"></a>Blob 문서 프로젝팅

개체 프로젝션은 모든 노드에서 원본으로 사용할 수 있는 보강 트리의 JSON 표현입니다. 테이블 프로젝션과 비교할 때 개체 프로젝션은 정의하기 더 간단하며 전체 문서를 프로젝션할 때 사용됩니다. 개체 프로젝션은 컨테이너의 단일 프로젝션으로 제한되며 조각화할 수 없습니다.

개체 프로젝션을 정의하려면 `objects` 프로젝션 속성에 배열을 사용합니다.

원본은 프로젝션의 루트인 보강 트리의 노드에 대한 경로입니다. 필수는 아니지만 노드 경로는 일반적으로 쉐이퍼 기술의 출력입니다. 대부분의 기술은 유효한 JSON 개체를 자체 출력하지 않기 때문입니다. 즉, 일부 형태의 셰이핑이 필요합니다. 대부분의 경우 테이블 프로젝션을 만드는 동일한 쉐이퍼 기술을 사용하여 개체 프로젝션을 생성할 수 있습니다. 또는 원본을 [인라인 셰이핑이 있는](knowledge-store-projection-shape.md#inline-shape) 노드로 설정하여 구조를 제공할 수도 있습니다.

대상은 항상 Blob 컨테이너입니다.

다음 예제에서는 개별 호텔 문서(Blob당 하나의 호텔 문서)를 라는 컨테이너에 `hotels` 투영합니다.

```json
"knowledgeStore": {
  "storageConnectionString": "an Azure storage connection string",
  "projections" : [
    {
      "tables": [ ]
    },
    {
      "objects": [
        {
        "storageContainer": "hotels",
        "source": "/document/objectprojection",
        }
      ]
    },
    {
        "files": [ ]
    }
  ]
}
```

원본은 "objectprojection"이라는 쉐이퍼 기술의 출력입니다. 각 Blob에는 각 필드 입력의 JSON 표현이 있습니다.

```json
    {
      "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
      "name": "#3",
      "description": null,
      "context": "/document",
      "inputs": [
        {
          "name": "HotelId",
          "source": "/document/HotelId"
        },
        {
          "name": "HotelName",
          "source": "/document/HotelName"
        },
        {
          "name": "Category",
          "source": "/document/Category"
        },
        {
          "name": "keyPhrases",
          "source": "/document/HotelId/keyphrases/*"
        },
      ],
      "outputs": [
        {
          "name": "output",
          "targetName": "objectprojection"
        }
      ]
    }
```

## <a name="projecting-an-image-file"></a>이미지 파일 프로젝팅

파일 프로젝션은 항상 정규화된 이진 이미지로, 정규화는 기술 영역 실행에 사용할 수 있는 잠재적인 크기 조정 및 회전을 나타냅니다. 개체 프로젝션과 유사한 파일 프로젝션은 Azure Storage Blob으로 만들어지고 이미지를 포함합니다.

파일 프로젝션을 정의하려면 `files` 프로젝션 속성에 배열을 사용합니다.

원본은 항상 `/document/normalized_images/*` 입니다. 파일 프로젝션은 `normalized_images` 컬렉션에서만 작동합니다. 인덱서와 기술셋은 모두 정규화되지 않은 원래 이미지를 통과하지 않습니다.

대상은 항상 문서 ID의 base64로 인코딩된 값의 폴더 접두사로 Blob 컨테이너입니다. 파일 프로젝션은 개체 프로젝션과 동일한 컨테이너를 공유할 수 없으며, 다른 컨테이너에 프로젝션되어야 합니다. 

다음 예제에서는 보강된 문서의 문서 노드에서 추출된 모든 정규화된 이미지를 라는 컨테이너에 `myImages` 투영합니다.

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


## <a name="next-steps"></a>다음 단계

이 문서의 예제에서는 프로젝션을 만드는 방법에 대 한 일반적인 패턴을 보여 줍니다. 개념을 이해했으므로 이제 특정 시나리오에 대한 프로젝션을 빌드할 준비가 되었습니다.

> [!div class="nextstepaction"]
> [증분 보강을 위한 캐싱 구성](search-howto-incremental-index.md)