---
title: 사용자 지정 NER 평가 메트릭
titleSuffix: Azure Cognitive Services
description: 사용자 지정 NER(명명된 엔터티 인식)의 평가 메트릭에 대해 알아봅니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: overview
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-ner, ignite-fall-2021
ms.openlocfilehash: 3f8373670f967b993541b9744e44c1accc7c07d4
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131053561"
---
# <a name="evaluation-metrics-for-custom-ner-models"></a>사용자 지정 NER 모델에 대한 평가 메트릭

사용자 지정 엔터티 추출의 모델 평가에서 사용하는 메트릭은 다음과 같습니다.


|메트릭 |Description  |계산  |
|---------|---------|---------|
|자릿수     |  시도된 모든 인식에서 성공한 인식의 비율입니다. 이는 모델의 엔터티 인식이 실제로 적합한 인식이었던 횟수를 보여 줍니다.       | `Precision = #True_Positive / (#True_Positive + #False_Positive)`        |
|회차     | 존재하는 실제 엔터티 수에서 성공한 인식의 비율입니다.        | `Recall = #True_Positive / (#True_Positive + #False_Negatives)`        |
|F1 점수    |  정밀도와 재현율의 조합입니다.       |  `F1 Score = 2 * Precision * Recall / (Precision + Recall)`       |

## <a name="model-level-and-entity-level-evaluation-metrics"></a>모델 수준 및 엔터티 수준 평가 메트릭

정밀도, 재현율 및 F1 점수는 각 엔터티에 대해 개별적으로(엔터티 수준 평가) 및 모델에 대해 집합적으로(모델 수준 평가) 계산됩니다.

정밀도, 재현율 및 평가의 정의는 엔터티 수준 및 모델 수준 평가 모두에서 동일합니다. 그러나 *진양성*, *가양성* 및 *가음성* 의 개수는 다를 수 있습니다. 예를 들어 다음 텍스트를 고려해 보겠습니다.

### <a name="example"></a>예제

*이 계약의 첫 번째 당사자는 네브라스카 주의 5678 Main Rd., City of Frederick에 거주하는 John Smith이며, 두 번째 당사자는 뉴멕시코 주의 123-345 Integer Rd., City of Corona에 거주하는 Forrest Ray입니다. 또한 Fannie Thomas는 콜로라도 주의 7890 River Road, city of Colorado Springs에 거주하고 있습니다.*

이 텍스트로부터 엔터티를 추출하는 모델에서 다음과 같이 예측할 수 있습니다.

| 엔터티 | 예측 형식 | 실제 형식 |
|--|--|--|
| John Smith | 사람 | 사람 |
| Frederick | 사람 | City |
| Forrest | City | 사람 |
| Fannie Thomas | 사람 | 사람 |
| Colorado Springs | City | City |

### <a name="entity-level-evaluation-for-the-person-entity"></a>*사람* 엔터티에 대한 엔터티 수준 평가 

모델의 *사람* 엔터티에 대한 엔터티 수준 평가는 다음과 같습니다.

| 키 | 개수 | 설명 |
|--|--|--|
| True Positive(TP) | 2 | *John Smith* 와 *Fannie Thomas* 는 *사람* 으로 올바르게 예측되었습니다. |
| False Positive(FP) | 1 | *Frederick* 은 *도시* 여야 하는데 *사람* 으로 잘못 예측되었습니다. |
| False Negative(FN) | 1 | *Forrest* 는 *사람* 이어야 하는데 *도시* 로 잘못 예측되었습니다. |

* **정밀도**: `#True_Positive / (#True_Positive + #False_Positive)` = `2 / (2 + 1) = 0.67`
* **재현율**: `#True_Positive / (#True_Positive + #False_Negatives)` = `2 / (2 + 1) = 0.67`
* **F1 점수**: `2 * Precision * Recall / (Precision + Recall)` = `(2 * 0.67 * 0.67) / (0.67 + 0.67) = 0.67`

### <a name="entity-level-evaluation-for-the-city-entity"></a>*도시* 엔터티에 대한 엔터티 수준 평가

모델의 *도시* 엔터티에 대한 엔터티 수준 평가는 다음과 같습니다.

| 키 | 개수 | 설명 |
|--|--|--|
| True Positive(TP) | 1 | *Colorado Springs* 는 *도시* 로 올바르게 예측되었습니다. |
| False Positive(FP) | 1 | *Forrest* 는 *사람* 이어야 하는데 *도시* 로 잘못 예측되었습니다. |
| False Negative(FN) | 1 | *Frederick* 은 *도시* 여야 하는데 *사람* 으로 잘못 예측되었습니다. |

* **정밀도** = `#True_Positive / (#True_Positive + #False_Positive)` = `2 / (2 + 1) = 0.67`
* **재현율** = `#True_Positive / (#True_Positive + #False_Negatives)` = `2 / (2 + 1) = 0.67`
* **F1 점수** = `2 * Precision * Recall / (Precision + Recall)` =  `(2 * 0.67 * 0.67) / (0.67 + 0.67) = 0.67`

### <a name="model-level-evaluation-for-the-collective-model"></a>집합적 모델에 대한 모델 수준 평가

모델의 전체 평가는 다음과 같습니다.

| 키 | 개수 | 설명 |
|--|--|--|
| True Positive(TP) | 3 | *John Smith* 와 *Fannie Thomas* 는 *사람* 으로 올바르게 예측되었습니다. *Colorado Springs* 는 *도시* 로 올바르게 예측되었습니다. 이는 모든 엔터티에 대한 진양성의 합계입니다. |
| False Positive(FP) | 2 | *Forrest* 는 *사람* 이어야 하는데 *도시* 로 잘못 예측되었습니다. *Frederick* 은 *도시* 여야 하는데 *사람* 으로 잘못 예측되었습니다. 이는 모든 엔터티에 대한 가양성의 합계입니다. |
| False Negative(FN) | 2 | *Forrest* 는 *사람* 이어야 하는데 *도시* 로 잘못 예측되었습니다. *Frederick* 은 *도시* 여야 하는데 *사람* 으로 잘못 예측되었습니다. 이는 모든 엔터티에 대한 가음성의 합계입니다. |

* **정밀도** = `#True_Positive / (#True_Positive + #False_Positive)` = `3 / (3 + 2) = 0.6`
* **재현율** = `#True_Positive / (#True_Positive + #False_Negatives)` = `3 / (3 + 2) = 0.6`
* **F1 점수** = `2 * Precision * Recall / (Precision + Recall)` =  `(2 * 0.6 * 0.6) / (0.6 + 0.6) = 0.6`

## <a name="interpreting-entity-level-evaluation-metrics"></a>엔터티 수준 평가 메트릭 해석

그렇다면 특정 엔터티에 대해 높은 정밀도 또는 높은 재현율을 사용하는 것은 실제로 무엇을 의미할까요?

| 회차 | 자릿수 | 해석 |
|--|--|--|
| 높음 | 높음 | 모델에서 이 엔터티를 효율적으로 처리합니다. |
| 낮음 | 높음 | 모델에서 항상 이 엔터티를 추출할 수는 없지만 추출 결과에 대한 신뢰도가 높습니다. |
| 높음 | 낮음 | 모델에서 이 엔터티를 효율적으로 추출하지만, 다른 형식으로 추출되는 경우가 있으므로 신뢰도가 낮습니다. |
| 낮음 | 낮음 | 이 엔터티 형식은 일반적으로 추출되지 않으므로 모델에서 제대로 처리하지 않습니다. 이 경우 신뢰도가 높지 않습니다. |

## <a name="confusion-matrix"></a>혼동 행렬

혼동 행렬은 모델 성능 평가에 사용되는 N x N 행렬입니다. 여기서 N은 엔터티의 수입니다.
행렬은 실제 태그를 모델에서 예측한 태그와 비교합니다.
이를 통해 모델의 효율적인 작동 및 발생하는 오류의 종류에 대한 전체적인 보기를 제공합니다.

혼동 행렬을 사용하여 서로 너무 가까이 있고 자주 혼동되는 엔터티(모호성)를 식별할 수 있습니다. 이 경우 이러한 엔터티 형식을 모두 병합하는 것이 좋습니다. 이처럼 병합할 수 없는 경우 모델에서 두 엔터티를 구분할 수 있도록 두 엔터티 모두에 대한 태그가 지정된 예제를 추가하는 것이 좋습니다.

아래 이미지에서 강조 표시된 대각선은 올바르게 예측된 엔터티이며, 여기서 예측된 태그는 실제 태그와 동일합니다.

:::image type="content" source="../media/confusion-matrix-example.png" alt-text="혼동 행렬 예제의 스크린샷" lightbox="../media/confusion-matrix-example.png":::

혼동 행렬에서는 엔터티 수준 및 모델 수준 평가 메트릭을 계산할 수 있습니다.

* 대각선의 값은 각 엔터티의 *진양성* 값입니다.
* 엔터티 행의 값 합계(대각선 제외)는 모델의 *가양성* 입니다.
* 엔터티 열의 값 합계(대각선 제외)는 모델의 *가음성* 입니다.

마찬가지로,

* 모델의 *진양성* 은 모든 엔터티에 대한 *진양성* 의 합계입니다.
* 모델의 *가양성* 은 모든 엔터티에 대한 *가양성* 의 합계입니다.
* 모델의 *가음성* 은 모든 엔터티에 대한 *가음성* 의 합계입니다.

## <a name="next-steps"></a>다음 단계

[언어 스튜디오에서 모델 평가 보기](../how-to/view-model-evaluation.md)
