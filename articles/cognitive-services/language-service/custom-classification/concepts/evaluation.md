---
title: 사용자 지정 분류 평가 메트릭
titleSuffix: Azure Cognitive Services
description: 사용자 지정 엔터티 추출의 평가 메트릭에 대해 알아봅니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: overview
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-classification, ignite-fall-2021
ms.openlocfilehash: 53765ebe1862b4c46a1b4ad8caf75f64f6203e37
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131053668"
---
# <a name="evaluation-metrics"></a>평가 메트릭

[데이터 세트는 학습용 세트와 테스트용 세트의 두 부분으로 나뉩니다](../how-to/train-model.md#data-splits). 모델을 구축하는 동안 학습 세트와 테스트 세트는 학습이 완료된 후 모델 성능을 평가하기 위한 블라인드 세트로 사용됩니다.

학습이 성공적으로 완료된 후 모델 평가가 트리거됩니다. 평가 프로세스는 학습된 모델을 사용하여 테스트 세트의 파일에 대한 사용자 정의 클래스를 예측하고 제공된 데이터 태그(진리 기준선 설정)와 비교하는 것으로 시작됩니다. 모델 성능을 검토할 수 있도록 결과가 반환됩니다. 평가를 위해 사용자 지정 텍스트 분류는 다음 메트릭을 사용합니다.

* **정밀도**: 모델이 얼마나 정확하고 정확한지를 측정합니다. 올바르게 식별된 긍정(진양성)과 식별된 모든 긍정 간의 비율입니다. 정밀도 메트릭은 예측된 클래스 중 얼마나 많은 태그가 올바르게 지정되었는지 보여 줍니다. 

    `Precision = #True_Positive / (#True_Positive + #False_Positive)`

* **재현율**: 실제 양성 클래스를 예측하는 모델의 능력을 측정합니다. 예측된 진양성과 실제로 태그된 것 간의 비율입니다. 재현율 메트릭은 예측된 클래스 중 얼마나 많은 것이 올바른지 보여 줍니다.

    `Recall = #True_Positive / (#True_Positive + #False_Negatives)`

* **F1 점수**: F1 점수는 정밀도와 재현율의 함수입니다. 정밀도와 재현율 사이의 균형을 추구할 때 필요합니다.

    `F1 Score = 2 * Precision * Recall / (Precision + Recall)` <br> 

>[!NOTE]
> 정밀도, 재현율 및 F1 점수는 각 클래스에 대해 개별적으로(*클래스 수준* 평가) 및 모델에 대해 집합적으로(*모델 수준* 평가) 계산됩니다.

## <a name="model-level-and-class-level-evaluation-metrics"></a>모델 수준 및 클래스 수준 평가 메트릭

정밀도, 재현율 및 평가의 정의는 클래스 수준 및 모델 수준 평가 모두에서 동일합니다. 그러나 *진양성*, *가양성* 및 *가음성* 의 개수는 다음 예와 같이 다릅니다.

아래 섹션에서는 다음 데이터 세트 예를 사용합니다.

| 파일 | 실제 수업 | 예상 클래스 |
|--|--|--|
| 1 | action, comedy | comedy|
| 2 | action | action |
| 3 | romance | romance |
| 4 | romance, comedy | romance |
| 5 | comedy | action |

### <a name="class-level-evaluation-for-the-action-class"></a>*action* 클래스에 대한 클래스 수준 평가 

| 키 | 개수 | 설명 |
|--|--|--|
| True Positive(TP) | 1 | 파일 2는 *action* 으로 올바르게 분류되었습니다. |
| False Positive(FP) | 1 | 파일 5가 *action* 으로 잘못 분류되었습니다. |
| False Negative(FN) | 1 | 파일 1은 있어야 하지만 *action* 으로 분류되지 않았습니다. |

**정밀도** = `#True_Positive / (#True_Positive + #False_Positive) = 1 / (1 + 1) = 0.5`

**재현율** = `#True_Positive / (#True_Positive + #False_Negatives) = 1 / (1 + 1) = 0.5`

**F1 점수** = `2 * Precision * Recall / (Precision + Recall) =  (2 * 0.5 * 0.5) / (0.5 + 0.5) = 0.5`

### <a name="class-level-evaluation-for-the-comedy-class"></a>*comedy* 수업에 대한 수업 수준 평가 

| 키 | 개수 | 설명 |
|--|--|--|
| 참 긍정 | 1 | 파일 1은 *comedy* 로 올바르게 분류되었습니다. |
| 거짓 긍정 | 0 | *comedy* 로 잘못 분류된 파일이 없습니다. |
| 거짓 부정 | 2 | 파일 5와 4는 있어야 하지만 *comedy* 로 분류되지 않았습니다. |

**정밀도** = `#True_Positive / (#True_Positive + #False_Positive) = 1 / (1 + 0) = 1`

**재현율** = `#True_Positive / (#True_Positive + #False_Negatives) = 1 / (1 + 2) = 0.67`

**F1 점수** = `2 * Precision * Recall / (Precision + Recall) =  (2 * 1 * 0.67) / (1 + 0.67) = 0.8`

### <a name="model-level-evaluation-for-the-collective-model"></a>집합적 모델에 대한 모델 수준 평가

| 키 | 개수 | 설명 |
|--|--|--|
| True Positive(TP) | 4 | 파일 1, 2, 3 및 4에는 예측 시 올바른 클래스가 지정되었습니다. |
| False Positive(FP) | 1 | 예측 시 파일 5에 잘못된 클래스가 지정되었습니다. |
| False Negative(FN) | 2 | 파일 1과 4에는 예측 시 모든 올바른 클래스가 지정되지 않았습니다. |

**정밀도** = `#True_Positive / (#True_Positive + #False_Positive) = 4 / (4 + 1) = 0.8`

**재현율** = `#True_Positive / (#True_Positive + #False_Negatives) = 4 / (4 + 2) = 0.67`

**F1 점수** = `2 * Precision * Recall / (Precision + Recall) =  (2 * 0.8 * 0.67) / (0.8 + 0.67) = 0.12`

> [!NOTE] 
> 단일 레이블 분류 모델의 경우 가음성 및 가양성의 개수는 항상 동일합니다. 사용자 지정 단일 레이블 분류 모델은 항상 각 파일에 대해 하나의 클래스를 예측합니다. 예측이 정확하지 않으면 예측된 클래스의 FP 수가 1씩 증가하고 실제 클래스의 FN이 1씩 증가하므로 모델에 대한 FP 및 FN의 전체 수는 항상 동일합니다. 이는 다중 레이블 분류의 경우가 아닙니다. 파일의 클래스 중 하나를 예측하는 데 실패하면 가음성으로 계산되기 때문입니다. 

## <a name="interpreting-class-level-evaluation-metrics"></a>클래스 수준 평가 메트릭 해석

그렇다면 특정 클래스에 대해 높은 정밀도 또는 높은 재현율을 갖는다는 것은 실제로 무엇을 의미하나요?

| 회차 | 자릿수 | 해석 |
|--|--|--|
| 높음 | 높음 | 이 클래스는 모델에 의해 완벽하게 처리됩니다. |
| 낮음 | 높음 | 모델이 항상 이 클래스를 예측할 수는 없지만 예측할 때 높은 확신을 가지고 있습니다. 이는 이 클래스가 데이터 세트에서 과소 대표되기 때문일 수 있으므로 데이터 분포의 균형을 고려합니다.|
| 높음 | 낮음 | 모델은 이 클래스를 잘 예측하지만 신뢰도는 낮습니다. 이는 이 클래스가 데이터 세트에 과도하게 표시되기 때문일 수 있으므로 데이터 분포의 균형을 고려합니다. |
| 낮음 | 낮음 | 이 클래스는 일반적으로 예측되지 않는 모델에 의해 제대로 처리되지 않고 예측될 때 신뢰도가 높지 않습니다. |

사용자 지정 분류 모델은 가음성 및 가양성을 모두 경험할 것으로 예상됩니다. 각각이 전체 시스템에 어떤 영향을 미칠지 고려하고 모델이 올바른 예측을 무시하고 잘못된 예측을 인식하는 시나리오를 신중하게 생각해야 합니다. 시나리오에 따라 *정밀도* 또는 *재현율* 이 모델 성능을 평가하는 데 더 적합할 수 있습니다.  

예를 들어 시나리오에 기술 지원 티켓 처리가 포함된 경우 잘못된 클래스를 예측하면 잘못된 부서/팀으로 전달될 수 있습니다. 이 예에서는 시스템을 가양성(false positive)에 더 민감하게 만드는 것을 고려해야 하며 정밀도는 평가에 더 관련성이 높은 메트릭이 될 것입니다. 

또 다른 예로, 시나리오에서 이메일을 "*중요*" 또는 "*스팸*"으로 분류하는 경우 잘못된 예측으로 인해 "*스팸*" 레이블이 지정된 경우 유용한 이메일을 놓칠 수 있습니다. 그러나 스팸 이메일에 *중요* 레이블이 지정되어 있으면 무시할 수 있습니다. 이 예에서 시스템을 가음성에 더 민감하게 만드는 것을 고려해야 하며 회수는 평가에 더 관련성이 높은 메트릭이 될 것입니다. 

범용 시나리오 또는 정밀도와 재현율이 모두 중요한 경우에 최적화하려는 경우 F1 점수를 활용할 수 있습니다. 평가 점수는 시나리오 및 수용 기준에 따라 주관적입니다. 모든 시나리오에 적용되는 절대적인 메트릭은 없습니다. 

## <a name="see-also"></a>참고 항목

* [모델 평가 보기](../how-to/view-model-evaluation.md)
* [모델 학습](../how-to/train-model.md)
