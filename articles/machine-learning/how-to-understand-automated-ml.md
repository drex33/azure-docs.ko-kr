---
title: AutoML 실험 결과 평가
titleSuffix: Azure Machine Learning
description: 각각의 자동화된 Machine Learning 실험 실행에 대해 차트 및 메트릭을 확인하고 평가하는 방법에 대해 알아봅니다.
services: machine-learning
author: gregorybchris
ms.author: chgrego
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: automl
ms.date: 12/09/2020
ms.topic: how-to
ms.custom: contperf-fy21q2, automl
ms.openlocfilehash: 91c620a68d375084f8a4be6c5a8bf30b92d016c1
ms.sourcegitcommit: 54e7b2e036f4732276adcace73e6261b02f96343
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/12/2021
ms.locfileid: "129812202"
---
# <a name="evaluate-automated-machine-learning-experiment-results"></a>자동화된 Machine Learning 실험 결과 평가

이 문서에서는 자동화된 ML(자동화된 Machine Learning) 실험으로 학습한 모델을 평가하고 비교하는 방법에 대해 알아봅니다. 자동화된 ML 실험을 수행하는 과정에서 많은 실행이 만들어지고 각 실행은 모델을 만듭니다. 자동화된 ML은 각 모델에 대해 모델의 성능을 측정하는 데 도움이 되는 평가 메트릭과 차트를 생성합니다. 

예를 들어 자동화된 ML은 실험 유형에 따라 다음과 같은 차트를 생성합니다.

| 분류| 회귀/예측 |
| ----------------------------------------------------------- | ---------------------------------------- |
| [혼동 행렬](#confusion-matrix)                       | [잔차 히스토그램](#residuals)        |
| [ROC(수신자 조작 특성) 곡선](#roc-curve) | [예측 대 실제](#predicted-vs-true) |
| [PR(정밀도-재현율) 곡선](#precision-recall-curve)      |                                          |
| [리프트 곡선](#lift-curve)                                   |                                          |
| [누적 이득 곡선](#cumulative-gains-curve)           |                                          |
| [보정 곡선](#calibration-curve)                     |                     


## <a name="prerequisites"></a>필수 구성 요소

- Azure 구독 (Azure를 구독하고 있지 않은 경우 시작하기 전에 [체험 계정을 만듭니다](https://azure.microsoft.com/free/).)
- Azure Machine Learning 실험은 다음 중 하나를 사용하여 만듭니다.
  - [Azure Machine Learning 스튜디오](how-to-use-automated-ml-for-ml-models.md)(코드 필요 없음)
  - [Azure Machine Learning Python SDK](how-to-configure-auto-train.md)

## <a name="view-run-results"></a>실행 결과 보기

자동화된 ML 실험을 완료한 후 다음을 통해 실행 기록을 찾을 수 있습니다.
  - [Azure Machine Learning 스튜디오](overview-what-is-machine-learning-studio.md)를 사용하는 브라우저
  - [RunDetails Jupyter 위젯](/python/api/azureml-widgets/azureml.widgets.rundetails)을 사용하는 Jupyter Notebook

다음 단계와 비디오에서는 스튜디오에서 실행 기록과 모델 평가 메트릭 및 차트를 보는 방법을 알려줍니다.

1. [스튜디오에 로그인](https://ml.azure.com/)하고 작업 영역으로 이동합니다.
1. 왼쪽 메뉴에서 **실험** 을 선택합니다.
1. 실험 목록에서 해당 실험을 선택합니다.
1. 페이지 아래쪽의 테이블에서 자동 ML 실행을 선택합니다.
1. **모델** 탭에서 평가하려는 모델의 **알고리즘 이름** 을 선택합니다.
1. **메트릭** 탭에서 왼쪽의 확인란을 사용하여 메트릭 및 차트를 봅니다.

![스튜디오에서 메트릭을 확인하는 단계](./media/how-to-understand-automated-ml/how-to-studio-metrics.gif)

## <a name="classification-metrics"></a>분류 메트릭

자동화된 ML은 실험에 대해 생성된 각 분류 모델의 성능 메트릭을 계산합니다. 이러한 메트릭은 Scikit-learn 구현을 기반으로 합니다. 

많은 분류 메트릭이 두 클래스에서 이진 분류로 정의되고 클래스 전체에 대한 평균을 계산하여 다중 클래스 분류에 대한 하나의 점수를 구해야 합니다. Scikit-learn은 여러 가지 평균 계산 방법을 제공하며 여기에는 자동화된 ML에서 제공하는 **macro**, **micro** 및 **weighted** 의 세 가지가 포함됩니다.

- **Macro** - 각 클래스의 메트릭을 계산하여 가중치가 적용되지 않은 평균을 가져옵니다.
- **Micro** - 진양성, 가음성, 가양성의 총 개수를 세어 전역으로 메트릭을 계산합니다(클래스와 무관).
- **Weighted** - 각 클래스의 메트릭을 계산하여 클래스별 샘플 수에 기준한 가중 평균을 가져옵니다.

각 평균 계산 방법은 나름의 이점이 있지만 적절한 방법을 선택함에 있어 한 가지 공통 고려 사항은 클래스 불균형입니다. 클래스의 샘플 수가 각기 다른 경우 비중이 적은 클래스에 비중이 큰 클래스와 동일한 가중치를 부여하는 매크로 평균을 사용하는 것이 더 유용할 수가 있습니다. [자동화된 ML에서 이진 메트릭과 다중 클래스 메트릭 비교](#binary-vs-multiclass-classification-metrics)를 참조하여 자세히 알아봅니다. 

다음 표는 실험에 대해 생성된 각 분류 모델에 대해서 자동화된 ML이 계산하는 모델 성능 메트릭을 요약하여 보여 줍니다. 자세한 내용은 각 메트릭의 **계산** 필드에 연결된 Scikit-learn 설명서를 참조하세요. 

|메트릭|Description|계산|
|--|--|---|
|AUC | AUC는 [수신자 조작 특성 곡선](#roc-curve) 아래 면적입니다.<br><br> **목표:** 1에 가까울수록 좋음 <br> **범위:** [0, 1]<br> <br>지원되는 메트릭 이름에는 다음이 포함됩니다. <li>`AUC_macro`, 각 클래스에 대한 AUC의 산술 평균입니다.<li> `AUC_micro`다중 레이블 방식으로 계산 됩니다. 모든 샘플에 대해 서로 다른 각 클래스가 독립적인 예측으로 처리 됩니다 `0/1` . 올바른 클래스는 클래스가 되며 `true` 나머지 클래스는 `false` 클래스가 됩니다. 그런 다음 모든 샘플을 결합 하 여 새 이진 분류 작업에 대해 CC를 계산 합니다. <li> `AUC_weighted`, 각 클래스의 실제 인스턴스 수를 가중치로 적용하여 계산된 각 클래스에 대한 점수의 산술 평균입니다. <li> `AUC_binary`는 특정 클래스를 클래스로 처리 하 `true` 고 다른 모든 클래스를 클래스로 결합 하 여이 값입니다 `false` .<br><br>|[계산](https://scikit-learn.org/0.22/modules/generated/sklearn.metrics.roc_auc_score.html) | 
|accuracy| Accuracy(정확도)는 실제 클래스 레이블과 정확히 일치하는 예측 비율입니다. <br> <br>**목표:** 1에 가까울수록 좋음 <br> **범위:** [0, 1]|[계산](https://scikit-learn.org/0.22/modules/generated/sklearn.metrics.accuracy_score.html)|
|average_precision|Average precision(평균 정밀도)은 정밀도-재현율 곡선을 이전 임계값에서의 재현율 증가를 가중치로 사용하여 계산한 각 임계값에서 도달된 정밀도의 가중 평균으로 요약합니다. <br><br> **목표:** 1에 가까울수록 좋음 <br> **범위:** [0, 1]<br> <br>지원되는 메트릭 이름에는 다음이 포함됩니다.<li>`average_precision_score_macro`, 각 클래스의 평균 정밀도 점수의 산술 평균입니다.<li> `average_precision_score_micro`다중 레이블 방식으로 계산 됩니다. 모든 샘플에 대해 서로 다른 각 클래스가 독립적인 예측으로 처리 됩니다 `0/1` . 올바른 클래스는 클래스가 되며 `true` 나머지 클래스는 `false` 클래스가 됩니다. 그런 다음 모든 샘플을 결합 하 여 새 이진 분류 태스크에 대 한 평균 전체 자릿수를 계산 합니다.<li>`average_precision_score_weighted`, 각 클래스의 실제 인스턴스 수를 가중치로 적용하여 계산된 각 클래스의 평균 정밀도 점수의 산술 평균입니다. <li> `average_precision_score_binary`는 특정 클래스 하나를 클래스로 처리 하 `true` 고 다른 모든 클래스를 클래스로 결합 하 여 평균 전체 자릿수 값입니다 `false` .|[계산](https://scikit-learn.org/0.22/modules/generated/sklearn.metrics.average_precision_score.html)|
balanced_accuracy|Balanced accuracy(균형 정확도)는 각 클래스 재현율의 산술 평균입니다.<br> <br>**목표:** 1에 가까울수록 좋음 <br> **범위:** [0, 1]|[계산](https://scikit-learn.org/0.22/modules/generated/sklearn.metrics.recall_score.html)|
f1_score|F1 score(F1 점수)는 정밀도 및 재현율의 조화 평균입니다. 가양성 및 가음성 모두에 대해 적절한 균형 측정값입니다. 그러나 진음성은 고려하지 않습니다. <br> <br>**목표:** 1에 가까울수록 좋음 <br> **범위:** [0, 1]<br> <br>지원되는 메트릭 이름에는 다음이 포함됩니다.<li>  `f1_score_macro`: 각 클래스에 대한 F1 점수의 산술 평균입니다. <li> `f1_score_micro`: 진양성, 가음성, 가양성의 총 개수를 세어 계산됩니다. <li> `f1_score_weighted`: 각 클래스에 대한 F1 점수의 클래스 빈도에 따른 가중 평균입니다. <li> `f1_score_binary`는 특정 클래스 하나를 클래스로 처리 하 `true` 고 다른 모든 클래스를 클래스로 결합 하 여 f1 값입니다 `false` .|[계산](https://scikit-learn.org/0.22/modules/generated/sklearn.metrics.f1_score.html)|
log_loss|이것은 (다항) 로지스틱 회귀와 그 확장(예: 신경망)에 사용되는 손실 함수로, 확률적 분류자의 예측이 주어진 경우 실제 레이블의 음의 로그 유사도로 정의됩니다. <br><br> **목표:** 0에 가까울수록 좋음 <br> **범위:** [0, inf)|[계산](https://scikit-learn.org/0.22/modules/generated/sklearn.metrics.log_loss.html)|
norm_macro_recall| Normalized macro recall(정규화된 매크로 재현율)은 매크로 평균이면서 정규화되어 있는 재현율이므로, 임의 성능 점수는 0이고 완벽 성능 점수는 1이 됩니다. <br> <br>**목표:** 1에 가까울수록 좋음 <br> **범위:** [0, 1] |`(recall_score_macro - R)`&nbsp;/&nbsp;`(1 - R)` <br><br>여기서 `R`은 임의 예측에 대해 예상되는 `recall_score_macro`값입니다.<br><br>`R = 0.5`&nbsp;&nbsp;이진&nbsp;분류의 경우입니다. <br>`R = (1 / C)` C-클래스 분류 문제의 경우입니다.|
matthews_correlation | Matthews 상관(Matthews correlation) 계수는 정확도의 균형 측정값으로서, 한 클래스에 다른 클래스보다 훨씬 더 많은 샘플이 포함된 경우에도 사용할 수 있습니다. 계수 1은 완벽 예측, 0은 임의 예측, -1은 역예측을 나타냅니다.<br><br> **목표:** 1에 가까울수록 좋음 <br> **범위:** [-1, 1]|[계산](https://scikit-learn.org/0.22/modules/generated/sklearn.metrics.matthews_corrcoef.html)|
정밀도|Precision(정밀도)은 음성 샘플을 양성으로 레이블을 지정하지 않도록 하는 모델의 기능입니다. <br><br> **목표:** 1에 가까울수록 좋음 <br> **범위:** [0, 1]<br> <br>지원되는 메트릭 이름에는 다음이 포함됩니다. <li> `precision_score_macro`, 각 클래스에 대한 정밀도의 산술 평균입니다. <li> `precision_score_micro`, 진양성과 가양성의 총 개수를 세어 전역적으로 계산됩니다. <li> `precision_score_weighted`, 각 클래스의 실제 인스턴스 수를 가중치로 적용하여 계산된 각 클래스에 대한 정밀도의 산술 평균입니다. <li> `precision_score_binary`한 특정 클래스를 클래스로 처리하고 다른 모든 클래스를 클래스로 결합하여 전체 자릿수 `true` `false` 값입니다.|[계산](https://scikit-learn.org/0.22/modules/generated/sklearn.metrics.precision_score.html)|
재현율| Recall(재현율)은 양성 샘플 모두를 검색하는 모델의 기능입니다. <br><br> **목표:** 1에 가까울수록 좋음 <br> **범위:** [0, 1]<br> <br>지원되는 메트릭 이름에는 다음이 포함됩니다. <li>`recall_score_macro`: 각 클래스에 대한 재현율의 산술 평균입니다. <li> `recall_score_micro`: 진양성, 가음성, 가양성의 총 개수를 세어 전역적으로 계산됩니다.<li> `recall_score_weighted`: 각 클래스의 실제 인스턴스 수를 가중치로 적용하여 계산된 각 클래스에 대한 재현율의 산술 평균입니다. <li> `recall_score_binary`- 특정 클래스 하나를 클래스로 처리하고 다른 모든 클래스를 클래스로 결합하여 회수하는 `true` `false` 값입니다.|[계산](https://scikit-learn.org/0.22/modules/generated/sklearn.metrics.recall_score.html)|
weighted_accuracy|Weighted accuracy(가중치가 적용된 정확도)는 각 샘플에 동일한 클래스에 속하는 샘플의 총 수로 가중치가 적용된 정확도입니다. <br><br>**목표:** 1에 가까울수록 좋음 <br>**범위:** [0, 1]|[계산](https://scikit-learn.org/0.22/modules/generated/sklearn.metrics.accuracy_score.html)|

### <a name="binary-vs-multiclass-classification-metrics"></a>이진 및 다중 클래스 분류 메트릭

자동화된 ML 데이터가 이진인지 자동으로 검색하고, 클래스를 지정하여 데이터가 다중 클래스인 경우에도 사용자가 이진 분류 메트릭을 활성화할 수 있도록 `true` 합니다. 데이터 세트에 두 개의 클래스 또는 두 개 이상의 클래스가 있는 경우와 관계없이 다중 클래스 분류 메트릭이 보고됩니다. 이진 분류 메트릭은 데이터가 이진이거나 사용자가 옵션을 활성화할 때만 보고됩니다. 

다중 클래스 분류 메트릭은 다중 클래스 분류를 위한 것입니다. 예상대로 이러한 메트릭은 이진 데이터 세트에 적용될 때 클래스를 `true` 클래스로 처리하지 않습니다. 명확하게 다중 클래스용인 메트릭은 `micro`, `macro` 또는 `weighted`가 접미사로 붙습니다. 이러한 예로 `average_precision_score`, `f1_score`, `precision_score`, `recall_score` 및 `AUC`가 있습니다. 예를 들어, `tp / (tp + fn)`으로 재현율을 계산하는 대신 다중 클래스 평균 재현율(`micro`, `macro` 또는 `weighted`)은 이진 분류 데이터 세트의 두 클래스에 대한 평균입니다. 이는 `true` 클래스와 `false` 클래스에 대해 개별적으로 재현율을 계산하고 나서 둘의 평균을 구하는 것과 같습니다.

또한 이진 분류의 자동 검색이 지원되지만 항상 `true` 클래스를 수동으로 지정하여 올바른 클래스에 대해 이진 분류 메트릭이 계산되도록 하는 것이 좋습니다.

데이터 세트 자체가 다중 클래스일 때 이진 분류 데이터 세트에 대한 메트릭을 활성화하려면 사용자는 클래스로 처리할 클래스만 지정하면 `true` 됩니다. 그러면 이러한 메트릭이 계산됩니다.

## <a name="confusion-matrix"></a>혼동 행렬

혼동 행렬은 기계 학습 모델이 분류 모델에 대한 예측에서 체계적인 오차를 만드는 방법에 대한 시각적 정보를 제공합니다. 이름에서 "혼동"이라는 단어는 모델이 "혼동되거나" 잘못된 레이블이 붙은 샘플에서 유래합니다. 혼동 행렬의 행 `i` 및 열 `j`의 셀에는 `C_i` 클래스에 속하고 모델에 의해 `C_j` 클래스로 분류된 평가 데이터 세트의 샘플 수가 포함됩니다.

스튜디오에서 색이 더 진한 셀은 샘플 개수가 더 많음을 나타냅니다. 드롭다운에서 **정규화됨** 보기를 선택하면 각 행렬 행을 정규화하여 `C_j` 클래스로 예측되는 `C_i` 클래스의 백분율을 보여 줍니다. 기본 **행** 보기의 이점은 불균형 데이터 세트에서의 일반적인 문제인 실제 클래스 분포의 불균형으로 인해 모델이 비중이 적은 클래스의 샘플을 잘못 분류하였는지의 여부를 확인할 수 있다는 것입니다.

좋은 모델의 혼동 행렬은 대부분의 샘플이 대각선에 있습니다.

### <a name="confusion-matrix-for-a-good-model"></a>좋은 모델의 혼동 행렬 
![좋은 모델의 혼동 행렬 ](./media/how-to-understand-automated-ml/chart-confusion-matrix-good.png)

### <a name="confusion-matrix-for-a-bad-model"></a>나쁜 모델의 혼동 행렬
![나쁜 모델의 혼동 행렬](./media/how-to-understand-automated-ml/chart-confusion-matrix-bad.png)

## <a name="roc-curve"></a>ROC 곡선

ROC(수신자 조작 특성) 곡선은 의사 결정 임계값의 변화에 따른 TPR(진양성 비율)과 FPR(가양성 비율) 간의 관계를 표시합니다. 클래스 불균형이 심한 데이터 세트에 대해 모델을 학습시키는 경우에는 ROC 곡선이 덜 유용할 수 있는데 이는 비중이 큰 클래스가 비중이 적은 클래스의 기여를 무산시킬 수 있기 때문입니다.

AUC(곡선 아래 면적)는 올바르게 분류된 샘플의 비율로 해석할 수 있습니다. 좀 더 정확하게 말하자면, AUC는 분류자가 임의로 선택된 음성 샘플보다 임의로 선택된 양성 샘플을 더 높은 우선순위로 지정할 가능성입니다. 곡선의 모양은 분류 임계값 또는 의사 결정 경계의 함수로서 TPR와 FPR 간의 관계에 대한 직관적 해석을 제공합니다.

차트의 왼쪽 상단 모서리에 근접하는 곡선은 최상의 가능 모델인 100% TPR 및 0% FPR에 근접하고 있는 것입니다. 임의 모델은 왼쪽 아래 모서리에서 오른쪽 상단으로 `y = x` 선을 따라 ROC 곡선을 생성합니다. 임의 모델보다 나쁜 모델은 `y = x` 선 아래로 떨어지는 ROC 곡선을 갖습니다.
> [!TIP]
> 분류 실험의 경우 자동화된 ML 모델에 대해 생성된 각각의 꺾은선형 차트를 이용하여 클래스별로 모델을 평가하거나 모든 클래스에 대해 평균화할 수 있습니다. 차트의 오른쪽에 있는 범례에서 클래스 레이블을 클릭하여 이러한 여러 보기 간에 전환이 가능합니다.

### <a name="roc-curve-for-a-good-model"></a>좋은 모델의 ROC 곡선
![좋은 모델의 ROC 곡선](./media/how-to-understand-automated-ml/chart-roc-curve-good.png)

### <a name="roc-curve-for-a-bad-model"></a>나쁜 모델의 ROC 곡선
![나쁜 모델의 ROC 곡선](./media/how-to-understand-automated-ml/chart-roc-curve-bad.png)

## <a name="precision-recall-curve"></a>정밀도-재현율 곡선

정밀도-재현율 곡선은 의사 결정 임계값의 변화에 따른 정확도와 재현율 간의 관계를 표시합니다. 재현율은 양성 샘플 모두를 검색하는 모델의 기능이고 정밀도는 음성 샘플에 대해 양성으로 레이블을 지정하지 않도록 하는 모델의 기능입니다. 가음성 방지 또는 가양성 방지에 대한 상대적 중요도에 따라 재현율과 정밀도의 비중이 달라야 하므로 일부 비즈니스 문제가 발생할 수 있습니다.
> [!TIP]
> 분류 실험의 경우 자동화된 ML 모델에 대해 생성된 각각의 꺾은선형 차트를 이용하여 클래스별로 모델을 평가하거나 모든 클래스에 대해 평균화할 수 있습니다. 차트의 오른쪽에 있는 범례에서 클래스 레이블을 클릭하여 이러한 여러 보기 간에 전환이 가능합니다.
### <a name="precision-recall-curve-for-a-good-model"></a>좋은 모델의 정밀도-재현율 곡선
![좋은 모델의 정밀도-재현율 곡선](./media/how-to-understand-automated-ml/chart-precision-recall-curve-good.png)

### <a name="precision-recall-curve-for-a-bad-model"></a>나쁜 모델의 정밀도-재현율 곡선
![나쁜 모델의 정밀도-재현율 곡선](./media/how-to-understand-automated-ml/chart-precision-recall-curve-bad.png)

## <a name="cumulative-gains-curve"></a>누적 이득 곡선

누적 이득 곡선은 예측된 확률 순으로 샘플을 고려하는 경우 고려되는 샘플 백분율의 함수로 올바르게 분류된 양성 샘플의 백분율을 표시합니다.

이득을 계산하려면 먼저 모든 샘플을 모델이 예측한 최고 확률부터 최저 확률까지 정렬합니다. 그런 다음 최고 신뢰도 예측의 `x%`를 구합니다. 해당 `x%`로 검색된 양성 샘플 수를 양성 샘플의 총 수로 나누어 이득을 계산합니다. 누적 이득은 양성 클래스에 속할 가능성이 가장 높은 일부 데이터를 고려할 때 검색하게 되는 양성 샘플의 백분율입니다.

완벽 모델은 모든 양성 샘플을 모든 음성 샘플 위에 순위를 두어 두 개의 직선 세그먼트로 구성된 누적 이득 곡선을 제공합니다. 첫 번째는 기울기가 `1 / x`인 `(0, 0)`에서 `(x, 1)`을 잇는 선으로, 여기에서 `x`는 양성 클래스에 속하는 샘플의 비율입니다(클래스가 분산된 경우 `1 / num_classes`). 두 번째는 `(x, 1)`에서 `(1, 1)`을 잇는 가로선입니다. 첫 번째 세그먼트에서 모든 양성 샘플이 올바르게 분류되고 누적 이득은 고려되는 샘플의 첫 `x%` 내에서 `100%`로 이동합니다.

기준 임의 모델은 `y = x`을 따르는 누적 이득 곡선을 가지며, 고려된 샘플 중 `x%`의 경우 총 양성 샘플 중 약 `x%`만 검색되었습니다. 분산 된 데이터 집합에 대 한 완벽 한 모델에는 마이크로 평균 곡선 및 평균 증가율이 100%가 될 때까지 기울기가 있는 매크로 평균 줄이 포함 되 고, 그 다음에는 `num_classes` 데이터%가 100이 됩니다.
> [!TIP]
> 분류 실험의 경우 자동화된 ML 모델에 대해 생성된 각각의 꺾은선형 차트를 이용하여 클래스별로 모델을 평가하거나 모든 클래스에 대해 평균화할 수 있습니다. 차트의 오른쪽에 있는 범례에서 클래스 레이블을 클릭하여 이러한 여러 보기 간에 전환이 가능합니다.
### <a name="cumulative-gains-curve-for-a-good-model"></a>좋은 모델의 누적 이득 곡선
![좋은 모델의 누적 이득 곡선](./media/how-to-understand-automated-ml/chart-cumulative-gains-curve-good.png)

### <a name="cumulative-gains-curve-for-a-bad-model"></a>나쁜 모델의 누적 이득 곡선
![나쁜 모델의 누적 이득 곡선](./media/how-to-understand-automated-ml/chart-cumulative-gains-curve-bad.png)

## <a name="lift-curve"></a>리프트 곡선

리프트 곡선은 임의 모델에 비하여 모델의 성능이 몇 배나 더 우수한지를 보여 줍니다. 리프트는 임의 모델의 누적 증가율 (항상 이어야 함)에 대 한 누적 이득의 비율로 정의 됩니다 `1` .

이러한 상대적 성능은 클래스의 수를 늘릴 때 분류가 더 어려워지는 점을 반영합니다. (임의 모델은 두 클래스를 사용하는 데이터 세트에 비해 10개 클래스를 사용하는 데이터 세트에서 더 높은 비율의 샘플을 잘못 예측)

기준 리프트 곡선은 `y = 1`인 선으로, 모델 성능이 임의 모델의 성능과 일치합니다. 일반적으로 좋은 모델의 리프트 곡선은 해당 차트에서 더 높고 x축으로부터 더 멀어서 모델이 예측을 가장 신뢰할 수 있을 때 임의 예측보다 몇 배 더 성능이 좋다는 것을 보여 줍니다.

> [!TIP]
> 분류 실험의 경우 자동화된 ML 모델에 대해 생성된 각각의 꺾은선형 차트를 이용하여 클래스별로 모델을 평가하거나 모든 클래스에 대해 평균화할 수 있습니다. 차트의 오른쪽에 있는 범례에서 클래스 레이블을 클릭하여 이러한 여러 보기 간에 전환이 가능합니다.
### <a name="lift-curve-for-a-good-model"></a>좋은 모델의 리프트 곡선
![좋은 모델의 리프트 곡선](./media/how-to-understand-automated-ml/chart-lift-curve-good.png)
 
### <a name="lift-curve-for-a-bad-model"></a>나쁜 모델의 리프트 곡선
![나쁜 모델의 리프트 곡선](./media/how-to-understand-automated-ml/chart-lift-curve-bad.png)

## <a name="calibration-curve"></a>보정 곡선

보정 곡선은 각 신뢰도 수준에서 양성 샘플의 비율에 대한 모델의 예측 신뢰도를 표시합니다. 잘 보정된 모델은 100% 신뢰도를 할당하는 예측의 100%, 50% 신뢰도를 할당하는 예측의 50%, 20% 신뢰도를 할당하는 예측의 20% 등을 올바르게 분류합니다. 완벽하게 보정된 모델은 `y = x` 선을 따르는 보정 곡선을 가지며, 모델이 각 클래스에 속하는 확률을 완벽하게 예측합니다.

확신이 과도한 모델은 각 샘플의 클래스에 대해서는 불확실한 경우가 거의 없이 0과 1에 가깝게 확률을 과도하게 예측하고 보정 곡선은 뒤집어진 "S"와 유사하게 보입니다. 확신이 부족한 모델은 예측하는 클래스에 평균적으로 낮은 확률을 할당하고 관련된 보정 곡선은 "S"와 유사하게 보입니다. 보정 곡선은 올바르게 분류하는 모델의 기능이 아니라 예측에 대한 신뢰도를 올바르게 할당하는 기능을 나타냅니다. 모델이 낮은 신뢰도와 높은 불확실성을 올바르게 할당하면 나쁜 모델도 좋은 보정 곡선을 가질 수 있습니다.

> [!NOTE]
> 보정 곡선은 샘플 수에 민감하므로 작은 유효성 검사 집합은 해석하기 어려울 수 있는 노이즈가 있는 결과를 생성할 수 있습니다. 이것은 반드시 모델이 잘 보정되지 않았다는 의미는 아닙니다.

### <a name="calibration-curve-for-a-good-model"></a>좋은 모델의 보정 곡선
![좋은 모델의 보정 곡선](./media/how-to-understand-automated-ml/chart-calibration-curve-good.png)

### <a name="calibration-curve-for-a-bad-model"></a>나쁜 모델의 보정 곡선
![나쁜 모델의 보정 곡선](./media/how-to-understand-automated-ml/chart-calibration-curve-bad.png)

## <a name="regressionforecasting-metrics"></a>회귀/예측 메트릭

자동화된 ML은 회귀인지 예측 실험인지에 관계없이 생성된 각 모델에 대해 동일한 성능 메트릭을 계산합니다. 이러한 메트릭은 또한 정규화를 거쳐 다른 범위의 데이터에 대해 학습된 모델 간의 비교를 가능하게 합니다. 자세히 알아보려면 [메트릭 정규화](#metric-normalization)를 참조하세요.  

다음 표에는 회귀 및 예측 실험에 대해 생성된 모델 성능 메트릭이 요약되어 있습니다. 분류 메트릭과 마찬가지로 이러한 메트릭도 Scikit-learn 구현을 기반으로 합니다. 적절한 Scikit-learn 설명서가 **계산** 필드에 적절하게 링크되어 있습니다.

|메트릭|Description|계산|
--|--|--|
explained_variance|Explained variance(설명된 분산)는 모델이 대상 변수의 변동을 설명하는 정도를 측정합니다. 오차 분산에 대한 원래 데이터의 분산 감소율입니다. 오차의 평균이 0인 경우 결정 계수와 같습니다(아래 r2_score 참조). <br> <br> **목표:** 1에 가까울수록 좋음 <br> **범위:** (-inf, 1]|[계산](https://scikit-learn.org/0.22/modules/generated/sklearn.metrics.explained_variance_score.html)|
mean_absolute_error|평균 절대 오차는 대상과 예측 간 차이의 절대값에 대한 예상값입니다.<br><br> **목표:** 0에 가까울수록 좋음 <br> **범위:** [0, inf) <br><br> 유형: <br>`mean_absolute_error` <br>  `normalized_mean_absolute_error`, mean_absolute_error를 데이터의 범위로 나눈 값입니다. | [계산](https://scikit-learn.org/0.22/modules/generated/sklearn.metrics.mean_absolute_error.html)|
mean_absolute_percentage_error|MAPE(평균 절대 백분율 오차)는 예측 값과 실제 값 간의 평균 차이를 측정한 값입니다.<br><br> **목표:** 0에 가까울수록 좋음 <br> **범위:** [0, inf) ||
median_absolute_error|Median absolute error(중앙값 절대 오차)는 목표와 예측 간 모든 절대 차이의 중앙값입니다. 이 손실은 이상값보다 강력합니다.<br><br> **목표:** 0에 가까울수록 좋음 <br> **범위:** [0, inf)<br><br>유형: <br> `median_absolute_error`<br> `normalized_median_absolute_error`: mean_absolute_error를 데이터의 범위로 나눈 값입니다. |[계산](https://scikit-learn.org/0.22/modules/generated/sklearn.metrics.median_absolute_error.html)|
r2_score|R<sup>2</sup>(결정 계수)는 관찰된 데이터의 총 분산에 대한 MSE(평균 제곱 오차)의 비율 감소를 측정합니다. <br> <br> **목표:** 1에 가까울수록 좋음 <br> **범위:** [-1, 1]<br><br>참고: R<sup>2</sup>는 종종 (-inf, 1]의 범위를 가집니다. MSE는 관찰된 분산보다 클 수 있으므로 R<sup>2</sup>는 데이터 및 모델 예측에 따라 임의로 큰 음수 값을 가질 수 있습니다. 자동화된 ML은 -1에서 R<sup>2</sup> 점수를 보고했으므로 R<sup>2</sup>의 값이 -1이면 실제 R<sup>2</sup> 점수가 -1보다 작음을 의미할 수 있습니다. 음의 R<sup>2</sup> 점수를 해석할 때 다른 메트릭 값과 데이터의 속성을 고려합니다.|[계산](https://scikit-learn.org/0.22/modules/generated/sklearn.metrics.r2_score.html)|
root_mean_squared_error |RMSE(제곱 평균 오차)는 대상과 예측 간의 예상 제곱 차이의 제곱근입니다. 비편향 추정기의 경우 RMSE는 표준 편차와 같습니다.<br> <br> **목표:** 0에 가까울수록 좋음 <br> **범위:** [0, inf)<br><br>유형:<br> `root_mean_squared_error` <br> `normalized_root_mean_squared_error`: root_mean_squared_error를 데이터의 범위로 나눈 값입니다. |[계산](https://scikit-learn.org/0.22/modules/generated/sklearn.metrics.mean_squared_error.html)|
root_mean_squared_log_error|제곱 평균 로그 오차는 예상 로그 제곱 오차의 제곱근입니다.<br><br>**목표:** 0에 가까울수록 좋음 <br> **범위:** [0, inf) <br> <br>유형: <br>`root_mean_squared_log_error` <br> `normalized_root_mean_squared_log_error`: root_mean_squared_log_error를 데이터의 범위로 나눈 값입니다.  |[계산](https://scikit-learn.org/0.22/modules/generated/sklearn.metrics.mean_squared_log_error.html)|
spearman_correlation| Spearman correlation(Spearman 상관 관계)은 두 데이터 세트 간 관계의 비모수 단조성 측정입니다. Pearson 상관 관계와 달리 Spearman 상관 관계는 두 데이터 세트가 모두 정규적으로 분포된다고 가정하지 않습니다. 다른 상관 계수처럼 Spearman은 -1과 1 사이에서 변화하며 0은 상관 관계가 없음을 의미합니다. \- 1 또는 1의 상관 관계는 정확한 단조 관계를 의미합니다. <br><br> Spearman은 예측 값 또는 실제 값을 변경해도 예측 값 또는 실제 값의 우선순위 순서가 변경되지 않는 경우 Spearman 결과가 변경되지 않음을 의미하는 우선 순위 순서 상관 관계 메트릭입니다.<br> <br> **목표:** 1에 가까울수록 좋음 <br> **범위:** [-1, 1]|[계산](https://docs.scipy.org/doc/scipy-1.5.2/reference/generated/scipy.stats.spearmanr.html)|

### <a name="metric-normalization"></a>메트릭 정규화

자동화된 ML은 회귀 및 예측 메트릭을 정규화하여 다른 범위의 데이터에 대해 학습된 모델 간의 비교를 가능하게 합니다. 범위가 더 큰 데이터에 대해 학습된 모델은 해당 오차를 정규화하지 않으면 더 작은 범위의 데이터로 학습된 동일한 모델보다 높은 오차를 갖습니다.

오차 메트릭을 정규화하는 표준 방법은 없지만 자동화된 ML은 오차를 데이터 범위로 나누는 일반적인 접근 방식을 사용합니다. `normalized_error = error / (y_max - y_min)`

시계열 데이터에 대한 예측 모델을 평가할 때 자동화된 ML은 시계열 ID(그레인)별로 정규화가 이루어지도록 추가 단계를 수행하는데 이는 각 시계열은 대상 값의 분포가 다를 수 있기 때문입니다.
## <a name="residuals"></a>잔차

잔차 차트는 회귀 및 예측 실험에 대해 생성된 예측 오류(잔차)의 히스토그램입니다. 잔차는 모든 샘플에 대해 `y_predicted - y_true`로 계산된 다음 모델 바이어스를 보여 주는 히스토그램으로 표시됩니다.

이 예제에서는 두 모델 모두 실제 값보다 낮게 예측하도록 약간 바이어스되어 있습니다. 이는 실제 대상의 분포가 치우쳐 있는 데이터 세트에 대해서는 드문 일이 아니지만 모델 성능이 더 나쁘다는 것을 나타냅니다. 좋은 모델은 극단에서는 잔차가 거의 없이 0에서 정점을 이루는 잔차 분포를 갖습니다. 더 나쁜 모델은 0 근처에 더 적은 수의 샘플이 있는 분산된 잔차 분포를 갖습니다.

### <a name="residuals-chart-for-a-good-model"></a>좋은 모델의 잔차 차트
![좋은 모델의 잔차 차트](./media/how-to-understand-automated-ml/chart-residuals-good.png)

### <a name="residuals-chart-for-a-bad-model"></a>나쁜 모델의 잔차 차트
![나쁜 모델의 잔차 차트](./media/how-to-understand-automated-ml/chart-residuals-bad.png)

## <a name="predicted-vs-true"></a>예측 대 실제

회귀 및 예측 실험의 경우 예측 대 실제 차트는 대상 기능(실제/진짜 값)과 모델 예측 간의 관계를 표시합니다. 실제 값은 x축을 따라 계급구간이 나뉘어지고 각 계급구간에 대해 예측 값 평균이 오차 막대로 표시됩니다. 이를 통해 모델이 특정 값을 예측하는 쪽으로 바이어스되어 있는지 확인할 수 있습니다. 선은 평균 예측을 표시하고 음영 처리된 영역은 해당 평균 주변의 예측 분산을 나타냅니다.

종종 가장 일반적인 실제 값이 가장 낮은 분산으로 가장 정확한 예측을 갖습니다. 실제 값이 거의 없는 이상적인 `y = x` 선에서 추세선까지의 거리는 이상값에 대한 모델 성능을 측정하는 좋은 척도입니다. 차트 아래쪽의 히스토그램을 사용하여 실제 데이터 분포를 추론할 수 있습니다. 분포가 희박한 데이터 샘플을 더 많이 포함하면 보이지 않는 데이터에 대한 모델 성능을 향상시킬 수 있습니다.

이 예에서 더 나은 모델은 이상적인 `y = x` 선에 더 가까운 예측 대 실제 선을 가집니다.

### <a name="predicted-vs-true-chart-for-a-good-model"></a>좋은 모델의 예측 대 실제 차트
![좋은 모델의 예측 대 실제 차트](./media/how-to-understand-automated-ml/chart-predicted-true-good.png)

### <a name="predicted-vs-true-chart-for-a-bad-model"></a>나쁜 모델의 예측 대 실제 차트
![나쁜 모델의 예측 대 실제 차트](./media/how-to-understand-automated-ml/chart-predicted-true-bad.png)

## <a name="model-explanations-and-feature-importances"></a>모델 설명 및 기능 중요도

모델의 일반적인 품질을 측정하는 데에는 모델 평가 메트릭과 차트로도 충분하지만, 책임을 져야 하는 AI를 실습할 때는 모델이 예측에 사용한 데이터 세트 기능을 검사하는 것이 중요 합니다. 이것이 자동화된 ML이 데이터 세트 기능의 상대적 기여도를 측정하고 보고하는 모델 설명 대시보드를 제공하는 이유입니다. [Azure Machine Learning 스튜디오에서 설명 대시보드를 보는](how-to-use-automated-ml-for-ml-models.md#model-explanations-preview) 방법을 참조하세요.

Code First를 사용하는 경우 [Azure Machine Learning Python SDK를 사용하여 자동화된 ML 실험에 대한 모델 설명을](how-to-machine-learning-interpretability-automl.md) 설정하는 방법을 참조하세요.

> [!NOTE]
> 다음 알고리즘을 최적의 모델 또는 앙상블로 권장하는 자동화된 ML 예측 실험에는 해석력 및 최적의 모델 설명을 사용할 수 없습니다. 
> * TCNForecaster
> * AutoArima
> * ExponentialSmoothing
> * Prophet
> * 평균 
> * Naive
> * Seasonal Average 
> * Seasonal Naive

## <a name="next-steps"></a>다음 단계
* [자동화된 Machine Learning 모델 설명 샘플 Notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model)을 사용해 봅니다.
* 자동화된 ML 관련 세부 질문은 askautomatedml@microsoft.com으로 문의하세요.
