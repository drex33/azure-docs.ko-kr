---
title: 의료 분야 Text Analytics의 관계 추출
titleSuffix: Azure Cognitive Services
description: 관계 추출에 대해 알아보기
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: overview
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-health, ignite-fall-2021
ms.openlocfilehash: 6fd838471387d7ef1b54beb9ead7b802f6e041e2
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131029749"
---
# <a name="relation-extraction"></a>관계 추출

관계 추출은 텍스트에 언급된 개념 간의 의미 있는 연결을 식별합니다. 예를 들어, "조건 시간" 관계는 조건 이름을 시간에 연결하여 찾거나 약어와 전체 설명 사이에서 찾습니다.  


## <a name="relation-extraction-output"></a>관계 추출 출력

의료 분야 Text Analytics는 특성과 엔터티 간의 관계(예: 신체 구조 방향, 약물 투여량) 및 엔터티 간의 관계(예: 약어 검색)를 포함하여 서로 다른 개념 간의 관계를 인식합니다.

> [!NOTE]
> * CONDITION을 참조하는 관계는 DIAGNOSIS 엔터티 유형 또는 SYMPTOM_OR_SIGN 엔터티 유형을 참조할 수 있습니다.
> * MEDICATION을 참조하는 관계는 MEDICATION_NAME 엔터티 유형 또는 MEDICATION_CLASS 엔터티 유형을 참조할 수 있습니다.
> * TIME을 참조하는 관계는 TIME 엔터티 유형 또는 DATE 엔터티 유형을 참조할 수 있습니다.

관계 추출 출력에는 URI 참조와 관계 유형 엔터티의 지정된 역할이 포함됩니다. 예를 들어 다음 JSON에서는 다음을 수행합니다.

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

## <a name="recognized-relations"></a>인식된 관계

API에서 다음 관계를 반환할 수 있습니다. 

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
