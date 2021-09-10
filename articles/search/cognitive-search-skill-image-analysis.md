---
title: 이미지 분석 인식 기술
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search의 AI 보강 파이프라인에서 이미지 분석 인식 기술을 사용하여 이미지 분석을 통해 의미 체계 텍스트를 추출합니다.
author: LiamCavanagh
ms.author: liamca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/12/2021
ms.openlocfilehash: d6b32dfedcb5ad5322a32c519084eac3858225ba
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122537351"
---
# <a name="image-analysis-cognitive-skill"></a>이미지 분석 인식 기술

**이미지 분석** 기술은 이미지 콘텐츠를 기준으로 다양한 시각적 기능 집합을 추출합니다. 예를 들어, 이미지에서 캡션을 생성하거나, 태그를 생성하거나, 유명인과 랜드마크를 식별할 수 있습니다. 이 기술은 Cognitive Services의 [Computer Vision](../cognitive-services/computer-vision/overview.md)에서 제공하는 기계 학습 모델을 사용합니다. 

**이미지 분석** 은 다음 요구 사항을 충족하는 이미지에서 작동합니다.

+ 이미지가 JPEG, PNG, GIF 또는 BMP 형식으로 제공되어야 합니다.
+ 이미지의 파일 크기가 4MB보다 작아야 합니다.
+ 이미지의 크기가 50 x 50 픽셀보다 커야 합니다.

> [!NOTE]
> 이 기술은 Cognitive Services에 바인딩되며, 하루에 인덱서당 20개의 문서를 초과하는 트랜잭션에 대해 [청구 가능한 리소스](cognitive-search-attach-cognitive-services.md)를 필요로 합니다. 기본 제공 기술을 실행하는 요금은 기존 [Cognitive Services 종량제 가격](https://azure.microsoft.com/pricing/details/cognitive-services/)으로 청구됩니다.
> 
> 또한 이미지 추출은 [Azure Cognitive Search를 통해 청구](https://azure.microsoft.com/pricing/details/search/)할 수 있습니다.
>

## <a name="odatatype"></a>@odata.type 

Microsoft.Skills.Vision.ImageAnalysisSkill 

## <a name="skill-parameters"></a>기술 매개 변수

매개 변수는 대/소문자를 구분합니다.

| 매개 변수 이름     | Description |
|--------------------|-------------|
| `defaultLanguageCode` |  반환할 언어를 나타내는 문자열입니다. 서비스는 지정된 언어로 인식 결과를 반환합니다. 이 매개 변수를 지정하지 않을 경우 기본값은 “en”입니다. <br/><br/>지원되는 언어는 다음과 같습니다. <br/>*en* - 영어(기본값) <br/> *es* - 스페인어 <br/> *ja* - 일본어 <br/> *pt* - 포르투갈어 <br/> *zh* - 중국어 간체|
| `visualFeatures` |    반환할 시각적 기능 유형을 나타내는 문자열 배열입니다. 유효한 시각적 기능 유형은 다음과 같습니다.  <ul><li>*성인* - 이미지가 본질적으로 음란물(노출 또는 성적 행위 묘사)인지, 잔혹물(지나친 폭력 또는 유혈 묘사)인지 감지합니다. 선정적인 콘텐츠(외설 콘텐츠라고도 함)도 감지됩니다.</li><li>*브랜드* - 이미지 내에서 대략적인 위치를 포함한 다양한 브랜드를 감지합니다. *브랜드* 시각적 기능은 영어로만 제공됩니다.</li><li> *범주* - Cognitive Services [Computer Vision 설명서](../cognitive-services/computer-vision/category-taxonomy.md)에 정의된 분류에 따라 이미지 콘텐츠를 분류합니다. </li><li>*설명* - 지원되는 언어의 완전한 문장으로 이미지 콘텐츠를 설명합니다.</li><li>*얼굴* - 얼굴이 있는지 여부를 감지합니다. 얼굴이 있으면 좌표, 성별 및 나이를 생성합니다.</li><li>  *개체* - 이미지 내에서 대략적인 위치를 포함한 다양한 개체를 감지합니다. *개체* 시각적 기능은 영어로만 제공됩니다.</li><li> ‘태그’ - 이미지 콘텐츠와 관련된 단어의 자세한 목록으로 이미지에 태그를 지정합니다.</li></ul> 시각적 기능의 이름은 대/소문자를 구분합니다. *Color* 및 *imageType* 시각적 기능은 더 이상 사용되지 않지만 [사용자 지정 기술](./cognitive-search-custom-skill-interface.md)을 통해 이 기능에 계속 액세스할 수 있습니다.|
| `details` | 반환할 도메인 특정 세부 정보를 나타내는 문자열 배열입니다. 유효한 시각적 기능 유형은 다음과 같습니다. <ul><li>*유명인* - 이미지에서 검색된 경우 유명인을 식별합니다.</li><li>*랜드마크* - 이미지에서 검색된 경우 랜드마크를 식별합니다. </li></ul> |

## <a name="skill-inputs"></a>기술 입력

| 입력 이름      | 설명                                          |
|---------------|------------------------------------------------------|
| `image`         | 복합 형식입니다. ```imageAction```이 ```none``` 이외의 값으로 설정된 경우 현재 Azure Blob 인덱서에서 생성된 “/document/normalized_images” 필드에만 작동합니다. 자세한 내용은 [샘플](#sample-output)을 참조하세요.|

##  <a name="sample-skill-definition"></a>샘플 기술 정의

```json
        {
            "description": "Extract image analysis.",
            "@odata.type": "#Microsoft.Skills.Vision.ImageAnalysisSkill",
            "context": "/document/normalized_images/*",
            "defaultLanguageCode": "en",
            "visualFeatures": [
                "tags",
                "categories",
                "description",
                "faces",
                "brands"
            ],
            "inputs": [
                {
                    "name": "image",
                    "source": "/document/normalized_images/*"
                }
            ],
            "outputs": [
                {
                    "name": "categories"
                },
                {
                    "name": "tags"
                },
                {
                    "name": "description"
                },
                {
                    "name": "faces"
                },
                {
                    "name": "brands"
                }
            ]
        }
```

### <a name="sample-index-for-only-the-categories-description-faces-and-tags-fields"></a>샘플 인덱스(범주, 설명, 얼굴 및 태그 필드에만 해당)

```json
{
    "fields": [
        {
            "name": "id",
            "type": "Edm.String",
            "key": true,
            "searchable": true,
            "filterable": false,
            "facetable": false,
            "sortable": true
        },
        {
            "name": "blob_uri",
            "type": "Edm.String",
            "searchable": true,
            "filterable": false,
            "facetable": false,
            "sortable": true
        },
        {
            "name": "content",
            "type": "Edm.String",
            "sortable": false,
            "searchable": true,
            "filterable": false,
            "facetable": false
        },
        {
            "name": "categories",
            "type": "Collection(Edm.ComplexType)",
            "fields": [
                {
                    "name": "name",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": false,
                    "facetable": false
                },
                {
                    "name": "score",
                    "type": "Edm.Double",
                    "searchable": false,
                    "filterable": false,
                    "facetable": false
                },
                {
                    "name": "detail",
                    "type": "Edm.ComplexType",
                    "fields": [
                        {
                            "name": "celebrities",
                            "type": "Collection(Edm.ComplexType)",
                            "fields": [
                                {
                                    "name": "name",
                                    "type": "Edm.String",
                                    "searchable": true,
                                    "filterable": false,
                                    "facetable": false
                                },
                                {
                                    "name": "faceBoundingBox",
                                    "type": "Collection(Edm.ComplexType)",
                                    "fields": [
                                        {
                                            "name": "x",
                                            "type": "Edm.Int32",
                                            "searchable": false,
                                            "filterable": false,
                                            "facetable": false
                                        },
                                        {
                                            "name": "y",
                                            "type": "Edm.Int32",
                                            "searchable": false,
                                            "filterable": false,
                                            "facetable": false
                                        }
                                    ]
                                },
                                {
                                    "name": "confidence",
                                    "type": "Edm.Double",
                                    "searchable": false,
                                    "filterable": false,
                                    "facetable": false
                                }
                            ]
                        },
                        {
                            "name": "landmarks",
                            "type": "Collection(Edm.ComplexType)",
                            "fields": [
                                {
                                    "name": "name",
                                    "type": "Edm.String",
                                    "searchable": true,
                                    "filterable": false,
                                    "facetable": false
                                },
                                {
                                    "name": "confidence",
                                    "type": "Edm.Double",
                                    "searchable": false,
                                    "filterable": false,
                                    "facetable": false
                                }
                            ]
                        }
                    ]
                }
            ]
        },
        {
            "name": "description",
            "type": "Collection(Edm.ComplexType)",
            "fields": [
                {
                    "name": "tags",
                    "type": "Collection(Edm.String)",
                    "searchable": true,
                    "filterable": false,
                    "facetable": false
                },
                {
                    "name": "captions",
                    "type": "Collection(Edm.ComplexType)",
                    "fields": [
                        {
                            "name": "text",
                            "type": "Edm.String",
                            "searchable": true,
                            "filterable": false,
                            "facetable": false
                        },
                        {
                            "name": "confidence",
                            "type": "Edm.Double",
                            "searchable": false,
                            "filterable": false,
                            "facetable": false
                        }
                    ]
                }
            ]
        },
        {
            "name": "faces",
            "type": "Collection(Edm.ComplexType)",
            "fields": [
                {
                    "name": "age",
                    "type": "Edm.Int32",
                    "searchable": false,
                    "filterable": false,
                    "facetable": false
                },
                {
                    "name": "gender",
                    "type": "Edm.String",
                    "searchable": false,
                    "filterable": false,
                    "facetable": false
                },
                {
                    "name": "faceBoundingBox",
                    "type": "Collection(Edm.ComplexType)",
                    "fields": [
                        {
                            "name": "x",
                            "type": "Edm.Int32",
                            "searchable": false,
                            "filterable": false,
                            "facetable": false
                        },
                        {
                            "name": "y",
                            "type": "Edm.Int32",
                            "searchable": false,
                            "filterable": false,
                            "facetable": false
                        }
                    ]
                }
            ]
        },
        {
            "name": "tags",
            "type": "Collection(Edm.ComplexType)",
            "fields": [
                {
                    "name": "name",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": false,
                    "facetable": false
                },
                {
                    "name": "confidence",
                    "type": "Edm.Double",
                    "searchable": false,
                    "filterable": false,
                    "facetable": false
                }
            ]
        }
    ]
}

```

### <a name="sample-output-field-mapping-for-the-above-index"></a>샘플 출력 필드 매핑(위 인덱스의 경우)

```json
    "outputFieldMappings": [
        {
            "sourceFieldName": "/document/normalized_images/*/categories/*",
            "targetFieldName": "categories"
        },
        {
            "sourceFieldName": "/document/normalized_images/*/tags/*",
            "targetFieldName": "tags"
        },
        {
            "sourceFieldName": "/document/normalized_images/*/description",
            "targetFieldName": "description"
        },
        {
            "sourceFieldName": "/document/normalized_images/*/faces/*",
            "targetFieldName": "faces"
        },
        {
            "sourceFieldName": "/document/normalized_images/*/brands/*/name",
            "targetFieldName": "brands"
        }
```

### <a name="variation-on-output-field-mappings-nested-properties"></a>출력 필드 매핑의 변형(중첩된 속성)

랜드마크 또는 유명인와 같은 하위 수준 속성에 대한 출력 필드 매핑을 정의할 수 있습니다. 이 경우 인덱스 스키마에 특별히 랜드마크를 포함하는 필드가 포함되어 있는지 확인합니다.

```json
    "outputFieldMappings": [
        {
            "sourceFieldName": "/document/normalized_images/*/categories/detail/celebrities/*",
            "targetFieldName": "celebrities"
        }
```

##  <a name="sample-input"></a>샘플 입력

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "image": {
                    "data": "BASE64 ENCODED STRING OF A JPEG IMAGE",
                    "width": 500,
                    "height": 300,
                    "originalWidth": 5000,
                    "originalHeight": 3000,
                    "rotationFromOriginal": 90,
                    "contentOffset": 500,
                    "pageNumber": 2
                }
            }
        }
    ]
}
```

##  <a name="sample-output"></a>샘플 출력

```json
{
  "values": [
    {
      "recordId": "1",
      "data": {
        "categories": [
          {
            "name": "abstract_",
            "score": 0.00390625
          },
          {
            "name": "people_",
            "score": 0.83984375,
            "detail": {
              "celebrities": [
                {
                  "name": "Satya Nadella",
                  "faceBoundingBox": [
                        {
                            "x": 273,
                            "y": 309
                        },
                        {
                            "x": 395,
                            "y": 309
                        },
                        {
                            "x": 395,
                            "y": 431
                        },
                        {
                            "x": 273,
                            "y": 431
                        }
                    ],
                  "confidence": 0.999028444
                }
              ],
              "landmarks": [
                {
                  "name": "Forbidden City",
                  "confidence": 0.9978346
                }
              ]
            }
          }
        ],
        "adult": {
          "isAdultContent": false,
          "isRacyContent": false,
          "isGoryContent": false,
          "adultScore": 0.0934349000453949,
          "racyScore": 0.068613491952419281,
          "goreScore": 0.08928389008070282
        },
        "tags": [
          {
            "name": "person",
            "confidence": 0.98979085683822632
          },
          {
            "name": "man",
            "confidence": 0.94493889808654785
          },
          {
            "name": "outdoor",
            "confidence": 0.938492476940155
          },
          {
            "name": "window",
            "confidence": 0.89513939619064331
          }
        ],
        "description": {
          "tags": [
            "person",
            "man",
            "outdoor",
            "window",
            "glasses"
          ],
          "captions": [
            {
              "text": "Satya Nadella sitting on a bench",
              "confidence": 0.48293603002174407
            }
          ]
        },
        "requestId": "0dbec5ad-a3d3-4f7e-96b4-dfd57efe967d",
        "metadata": {
          "width": 1500,
          "height": 1000,
          "format": "Jpeg"
        },
        "faces": [
          {
            "age": 44,
            "gender": "Male",
            "faceBoundingBox": [
                {
                    "x": 1601,
                    "y": 395
                },
                {
                    "x": 1653,
                    "y": 395
                },
                {
                    "x": 1653,
                    "y": 447
                },
                {
                    "x": 1601,
                    "y": 447
                }
            ]
          }
        ],
        "objects": [
          {
            "rectangle": {
              "x": 25,
              "y": 43,
              "w": 172,
              "h": 140
            },
            "object": "person",
            "confidence": 0.931
          }
        ],
        "brands":[  
           {  
              "name":"Microsoft",
              "confidence": 0.903,
              "rectangle":{  
                 "x":20,
                 "y":97,
                 "w":62,
                 "h":52
              }
           }
        ]
      }
    }
  ]
}
```


## <a name="error-cases"></a>오류 사례
다음 오류 사례에서는 요소가 추출되지 않습니다.

| 오류 코드 | Description |
|------------|-------------|
| `NotSupportedLanguage` | 제공한 언어가 지원되지 않습니다. |
| `InvalidImageUrl` | 이미지 URL의 형식이 잘못되었거나 액세스할 수 없습니다.|
| `InvalidImageFormat` | 입력 데이터가 유효한 이미지가 아닙니다. |
| `InvalidImageSize` | 입력 이미지가 너무 큽니다. |
| `NotSupportedVisualFeature`  | 지정한 기능 유형이 잘못되었습니다. |
| `NotSupportedImage` | 지원되지 않는 이미지(예: 아동 음란물)입니다. |
| `InvalidDetails` | 지원되지 않는 도메인 특정 모델입니다. |

`"One or more skills are invalid. Details: Error in skill #<num>: Outputs are not supported by skill: Landmarks"`와 유사한 오류가 발생하면 경로를 확인합니다. 유명인와 랜드마크는 모두 `detail`의 속성입니다.

```json
"categories":[  
      {  
         "name":"building_",
         "score":0.97265625,
         "detail":{  
            "landmarks":[  
               {  
                  "name":"Forbidden City",
                  "confidence":0.92013400793075562
               }
            ]
```

## <a name="see-also"></a>참고 항목

+ [이미지 분석이란?](../cognitive-services/computer-vision/overview-image-analysis.md)
+ [기본 제공 기술](cognitive-search-predefined-skills.md)
+ [기술 집합을 정의하는 방법](cognitive-search-defining-skillset.md)
+ [인덱서 만들기(REST)](/rest/api/searchservice/create-indexer)