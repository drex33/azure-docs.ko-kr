---
title: 의료 분야 Text Analytics의 어설션 검색
titleSuffix: Azure Cognitive Services
description: 어설션 검색에 대해 알아봅니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: overview
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-health, ignite-fall-2021
ms.openlocfilehash: 0f9d24e43bd4525a703027cff6a5ee4bec7bd431
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131029757"
---
# <a name="assertion-detection"></a>어설션 검색

의료 콘텐츠의 의미는 부정적 또는 조건적 어설션과 같은 한정자에 의해 크게 영향을 받습니다. 이러한 어설션이 잘못 표현될 경우 중대한 영향을 미칠 수 있습니다. 의료 분야 Text Analytics는 텍스트의 엔터티에 대한 세 가지 범주의 어설션 검색을 지원합니다. 

* 확신도
* 조건부
* 연결

## <a name="assertion-output"></a>어설션 출력

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
}
```

## <a name="next-steps"></a>다음 단계

[의료 분야 Text Analytics를 호출하는 방법](../how-to/call-api.md)
