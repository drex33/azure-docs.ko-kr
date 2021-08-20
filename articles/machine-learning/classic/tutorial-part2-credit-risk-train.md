---
title: 'ML Studio(클래식) 자습서 2: 신용 위험 모델 학습 - Azure'
description: 이 자습서는 Machine Learning Studio(클래식)에 관한 자습서 시리즈로서 세 부분으로 구성된 내용 중 2부에 해당됩니다. 이 자습서에서는 모델을 학습하고 평가하는 방법을 보여줍니다.
keywords: 신용 위험, 예측 분석 솔루션, 위험 평가
author: sdgilley
ms.author: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: tutorial
ms.date: 02/11/2019
ms.openlocfilehash: 0d0cdab6529f95de2936b32dda590f1f0f75e53c
ms.sourcegitcommit: 54d8b979b7de84aa979327bdf251daf9a3b72964
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/24/2021
ms.locfileid: "112580749"
---
# <a name="tutorial-2-train-credit-risk-models---machine-learning-studio-classic"></a>자습서 2: 신용 위험 모델 학습 - Machine Learning Studio(클래식)

**적용 대상:**  ![이는 이 문서가 Machine Learning Studio(클래식)에 적용됨을 의미하는 확인 표시입니다.](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio(클래식)  ![이는 이 문서가 Azure Machine Learning에 적용되지 않음을 의미하는 X입니다. ](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)

이 자습서에서는 예측 분석 솔루션을 개발하는 프로세스를 자세히 살펴봅니다. Machine Learning Studio(클래식)에서 간단한 모델을 개발합니다.  그런 다음, 모델을 Machine Learning 웹 서비스로 배포합니다.  이 배포된 모델은 새 데이터를 사용하여 예측을 수행할 수 있습니다. 이 자습서는 **3부로 구성된 자습서 시리즈 중 제2부** 입니다.

신용대출 지원 시 애플리케이션에서 제공한 정보를 기반으로 개인의 신용 위험을 예측해야 한다고 가정합니다.  

신용 위험 평가는 복잡한 문제이지만, 이 자습서에서는 약간 간소화하여 살펴보겠습니다. 이 신용 위험 평가는 Machine Learning Studio(클래식)를 사용하는 예측 분석 솔루션을 만드는 방법의 예로 사용합니다. 이 솔루션에는 Machine Learning Studio(클래식) 및 Machine Learning 웹 서비스가 사용됩니다.  

이 3부로 구성된 자습서에서는 공개적으로 사용 가능한 신용 위험 데이터부터 시작합니다.  그런 다음, 예측 모델을 개발하고 학습합니다.  마지막으로 모델을 웹 서비스로 배포합니다.

[이 자습서의 제1부](tutorial-part1-credit-risk.md)에서는 Machine Learning Studio(클래식) 작업 영역을 만들고, 데이터를 업로드하고, 실험을 만들었습니다.

1부 자습서에서 수행하는 작업은 다음과 같습니다.
 
> [!div class="checklist"]
> * 여러 모델 학습시키기
> * 모델 점수 매기기 및 평가


[이 자습서의 제3부](tutorial-part3-credit-risk-deploy.md)에서는 모델을 웹 서비스로 배포할 것입니다.

## <a name="prerequisites"></a>필수 구성 요소

[이 자습서의 제1부](tutorial-part1-credit-risk.md)를 완료합니다.

## <a name="train-multiple-models"></a><a name="train"></a>여러 모델 학습시키기

Machine Learning Studio(클래식)를 사용하여 기계 학습 모델을 만들 때의 이점 중 하나는 단일 실험에서 한 번에 두 개 이상의 모델 유형을 시도하고 결과를 비교할 수 있다는 점입니다. 이 유형의 실험을 사용하면 문제에 대한 최상의 솔루션을 찾을 수 있습니다.

이 자습서에서 개발하고 있는 실험에서는 두 가지 유형의 모델을 만들고 채점 결과를 비교하여 최종 실험에서 사용할 알고리즘을 결정할 것입니다.  

다양한 모델 중에 선택할 수 있습니다. 사용할 수 있는 모델을 보려면 모듈 팔레트에서 **Machine Learning** 노드를 확장하고 **모델 초기화** 및 그 아래 노드를 확장합니다. 이 실험에서는 [2클래스 SVM(Support Vector Machine)][two-class-support-vector-machine] 및 [2클래스 향상된 의사 결정 트리][two-class-boosted-decision-tree] 모듈을 선택할 것입니다.


이 실험에서는 [2클래스 향상된 의사 결정 트리][two-class-boosted-decision-tree] 모듈 및 [2클래스 지원 벡터 머신][two-class-support-vector-machine] 모듈을 추가할 것입니다.

### <a name="two-class-boosted-decision-tree"></a>2클래스 향상된 의사 결정 트리

먼저 향상된 의사 결정 트리 모델을 설정합니다.

1. 모듈 팔레트에서 [2클래스 향상된 의사 결정 트리][two-class-boosted-decision-tree] 모듈을 찾고 캔버스로 끌어서 놓습니다.

1. [모델 학습][train-model] 모듈을 찾고 캔버스로 끌고 나서 [2클래스 향상된 의사 결정 트리][two-class-boosted-decision-tree] 모듈의 출력을 [모델 학습][train-model] 모듈의 왼쪽 입력 포트에 연결합니다.
   
   [2클래스 향상된 의사 결정 트리][two-class-boosted-decision-tree] 모듈은 일반 모델을 초기화하고 [모델 학습][train-model]에서는 학습 데이터를 사용하여 모델을 학습합니다. 

1. 왼쪽 [R 스크립트 실행][execute-r-script] 모듈의 왼쪽 출력을 [모델 학습][train-model] 모듈의 오른쪽 입력 포트에 연결합니다(이 자습서에서는 학습할 데이터 분할 모듈의 [왼쪽에서 오는 데이터를 사용함](#train)).
   
   > [!TIP]
   > 이 실험에서는 [R 스크립트 실행][execute-r-script] 모듈의 입력 중 두 개와 출력 중 하나가 필요 없으므로 연결되지 않은 상태로 두면 됩니다. 
   > 
   > 

실험의 이 부분은 이제 다음과 같이 표시됩니다.  

![모델 학습](./media/tutorial-part2-credit-risk-train/experiment-with-train-model.png)

이제 모델을 통해 신용 위험 값을 예측할 것임을 [모델 학습][train-model] 모듈에 전달해야 합니다.

1. [모델 학습][train-model] 모듈을 선택합니다. **속성** 창에서 **열 선택기 시작** 을 클릭합니다.

1. **단일 열 선택** 대화 상자에서 **사용 가능한 열** 아래의 검색 필드에 "신용 위험"을 입력하고 오른쪽 화살표 단추( **>** )를 클릭하여 "신용 위험"을 **선택한 열** 로 이동합니다. 

    ![모델 학습 모듈에 대한 신용 위험 열 선택](./media/tutorial-part2-credit-risk-train/train-model-select-column.png)

1. **확인** 확인 표시를 클릭합니다.

### <a name="two-class-support-vector-machine"></a>2클래스 Support Vector Machine

다음으로 SVM 모델을 설정합니다.  

먼저 SVM에 대한 간단한 설명입니다. 향상된 의사 결정 트리는 모든 기능 유형에서 제대로 작동합니다. 그러나 SVM 모듈은 선형 분류자를 생성하므로 여기서 생성하는 모델에는 모든 숫자 기능의 크기가 같을 때 최적의 테스트 오류가 포함됩니다. 모든 숫자 기능을 같은 크기로 변환하려면 "Tanh" 변환을 사용합니다([데이터 정규화][normalize-data] 모듈과 함께). 그러면 숫자가 [0,1] 범위로 변환됩니다. SVM 모듈은 문자열 기능을 범주 기능으로 변환한 후 이진 0/1 기능으로 변환하므로 문자열 기능을 수동으로 변환할 필요가 없습니다. 그리고 신용 위험 열(열 21)을 변환하지 않을 것입니다. 이 열은 숫자 열이지만 모델을 학습하여 예측할 값이므로 단독으로 유지해야 합니다.  

SVM 모델을 설정하려면 다음을 수행합니다.

1. 모듈 팔레트에서 [2클래스 Support Vector Machine][two-class-support-vector-machine] 모듈을 찾고 캔버스로 끌어서 놓습니다.

1. [모델 학습][train-model] 모듈을 마우스 오른쪽 단추로 클릭하고 **복사** 를 선택하고 나서 캔버스를 마우스 오른쪽 단추로 클릭하고 **붙여넣기** 를 선택합니다. [모델 학습][train-model] 모듈의 복사본에는 원래 모듈과 같은 열이 선택되어 있습니다.

1. [2클래스 Support Vector Machine][two-class-support-vector-machine] 모듈의 출력을 두 번째 [모델 학습][train-model] 모듈의 왼쪽 입력 포트에 연결합니다.

1. [데이터 정규화][normalize-data] 모듈을 찾고 캔버스로 끌어옵니다.

1. 이 모듈의 입력에 왼쪽 [R 스크립트 실행][execute-r-script] 모듈의 왼쪽 출력을 연결합니다. 모듈의 출력 포트는 두 개 이상의 다른 모듈에 연결할 수 있습니다.

1. [데이터 정규화][normalize-data] 모듈의 왼쪽 출력 포트를 두 번째 [모델 학습][train-model] 모듈의 오른쪽 입력 포트에 연결합니다.

실험의 이 부분은 이제 다음과 같이 표시됩니다.  

![두 번째 모델 학습](./media/tutorial-part2-credit-risk-train/svm-model-added.png)

이제 [데이터 정규화][normalize-data] 모듈을 구성합니다.

1. [데이터 정규화][normalize-data] 모듈을 클릭하여 선택합니다. **속성** 창에서 **변환 메서드** 매개 변수에 대해 **Tanh** 를 선택합니다.

1. **열 선택기 시작** 을 클릭하고 **시작 문자** 에 대해 "열 없음"을 선택한 후, 첫 번째 드롭다운에서 **포함** 을, 두 번째 드롭다운에서 **열 형식** 을, 세 번째 드롭다운에서 **숫자** 를 선택합니다. 이렇게 하면 모든 숫자 열(및 숫자만)이 변환되도록 지정됩니다.

1. 이 행 오른쪽에 있는 더하기 기호(+)를 클릭하면 새 드롭다운 행이 만들어집니다. 첫 번째 드롭다운에서 **제외** 를 선택하고, 두 번째 드롭다운에서 **열 이름** 을 선택하고, 텍스트 필드에 "신용 위험"을 입력합니다. 이렇게 하면 신용 위험 열이 무시됩니다. 이 열은 숫자이므로 제외하지 않을 경우 변환되기 때문에 이 작업이 필요합니다.

1. **확인** 확인 표시를 클릭합니다.  

    ![데이터 정규화 모듈의 열 선택](./media/tutorial-part2-credit-risk-train/normalize-data-select-column.png)


이제 [데이터 정규화][normalize-data] 모듈은 신용 위험 열을 제외한 모든 숫자 열에서 Tanh 변환을 수행하도록 설정됩니다.  

## <a name="score-and-evaluate-the-models"></a>모델 점수 매기기 및 평가

[데이터 분할][split] 모듈을 통해 구분된 테스트 데이터를 사용하여 학습된 모듈을 채점합니다. 그 후 두 모델의 결과를 비교하여 더 나은 결과를 생성한 모듈을 확인할 수 있습니다.  

### <a name="add-the-score-model-modules"></a>모델 점수 매기기 모듈 추가

1. [모델 점수 매기기][score-model] 모듈을 찾아 캔버스로 끌어서 놓습니다.

1. [2클래스 향상된 의사 결정 트리][score-model] 모듈에 연결되어 있는 [모델 학습l][train-model] 모듈을 [모델 점수 매기기][two-class-boosted-decision-tree] 모듈의 왼쪽 입력 포트에 연결합니다.

1. [모델 점수 매기기][score-model] 모듈의 오른쪽 입력 포트에 오른쪽 [R 스크립트 실행][execute-r-script] 모듈(테스트 데이터)을 연결합니다.

    ![모델 점수 매기기 모듈이 연결됨](./media/tutorial-part2-credit-risk-train/score-model-connected.png)

   
   [모델 점수 매기기][score-model] 모듈에서 테스트 데이터의 신용 정보를 사용하고, 모듈을 통해 실행하고, 모델이 생성하는 예측을 테스트 데이터의 실제 신용 위험 열과 비교할 수 있습니다.

1. [모델 점수 매기기][score-model] 모듈을 복사하고 붙여넣어 두 번째 복사본을 만듭니다.

1. SVM 모델의 출력(즉, [2클래스 Support Vector Machine][two-class-support-vector-machine] 모듈에 연결된 [모델 학습][train-model] 모듈의 출력 포트)을 두 번째 [모델 점수 매기기][score-model] 모듈의 입력 부분에 연결합니다.

1. SVM 모델의 경우 학습 데이터에서 했던 것과 똑같이 테스트 데이터를 변환해야 합니다. 따라서 [데이터 정규화][normalize-data] 모듈을 복사하고 붙여넣고 두 번째 복사본을 만들고 오른쪽 [R 스크립트 실행][execute-r-script] 모듈에 연결합니다.

1. 두 번째 [모델 점수 매기기][score-model] 모듈의 오른쪽 입력 포트에 두 번째 [데이터 정규화][normalize-data] 모듈의 왼쪽 출력을 연결합니다.

    ![두 모델 점수 매기기 모듈이 연결됨](./media/tutorial-part2-credit-risk-train/both-score-models-added.png)


### <a name="add-the-evaluate-model-module"></a>모델 평가 모듈 추가

두 채점 결과를 평가하고 비교하기 위해 [모델 평가][evaluate-model] 모듈을 사용합니다.  

1. [모델 평가][evaluate-model] 모듈을 찾아 캔버스로 끌어서 놓습니다.

1. [모델 평가][evaluate-model] 모듈의 왼쪽 입력 포트에 향상된 의사 결정 트리 모델과 연결된 [모델 점수 매기기][score-model] 모듈의 출력 포트를 연결합니다.

1. 다른 [모델 점수 매기기][score-model] 모듈을 오른쪽 입력 포트에 연결합니다.  

    ![모델 평가 모듈이 연결됨](./media/tutorial-part2-credit-risk-train/evaluate-model-added.png)


### <a name="run-the-experiment-and-check-the-results"></a>실험 실행 및 결과 확인

실험을 실행하려면 캔버스 아래에서 **실행** 단추를 클릭합니다. 몇 분이 걸릴 수 있습니다. 각 모듈에 실행 중임을 나타내는 회전 표시기가 표시되고 나서 모듈이 완료되면 녹색 확인 표시가 표시됩니다. 모든 모듈에 확인 표시가 있으면 실험 실행이 완료된 것입니다.

이제 실험이 다음과 같이 표시됩니다.  

![두 모델 모두 평가](./media/tutorial-part2-credit-risk-train/final-experiment.png)


결과를 확인하려면 [모델 평가][evaluate-model] 모듈의 출력 포트를 클릭하고 **시각화** 를 선택합니다.  

[모델 평가][evaluate-model] 모듈에서는 점수를 매긴 모델 두 개의 결과를 비교할 수 있는 곡선 및 메트릭 쌍을 생성합니다. 결과를 ROC(Receiver Operator Characteristic) 곡선, 정밀도/리콜 곡선 또는 리프트 곡선으로 볼 수 있습니다. 표시된 추가 데이터에는 혼동 행렬, AUC(Area Under the Curve)에 대한 누적 값 및 기타 메트릭이 포함됩니다. 슬라이더를 왼쪽이나 오른쪽으로 이동하여 임계값을 변경하고 메트릭 집합에 어떤 영향을 미치는 확인할 수 있습니다.  

그래프 오른쪽에 있는 **점수를 매긴 데이터 세트** 또는 **비교할 점수를 매긴 데이터 세트** 를 클릭하여 관련 곡선을 강조 표시하고 아래에 관련 메트릭을 표시합니다. 곡선 범례에서 "점수를 매긴 데이터 세트"는 [모델 평가][evaluate-model] 모듈의 왼쪽 입력 포트(이 경우 향상된 의사 결정 트리 모델)에 해당합니다. "비교할 점수를 매긴 데이터 세트"는 오른쪽 입력 포트(이 경우 SVM 모델)에 해당합니다. 이러한 레이블 중 하나를 클릭하면 다음 그래픽에 표시된 것처럼 해당 모델의 곡선이 강조 표시되고 해당 메트릭이 표시됩니다.  

![모델에 대한 ROC 곡선](./media/tutorial-part2-credit-risk-train/roc-curves.png)

이러한 값을 검토하면 찾고 있는 결과에 가장 근접한 모델을 결정할 수 있습니다. 돌아가서 다른 모델에서 매개 변수 값을 변경하여 실험을 반복할 수 있습니다. 

이러한 결과를 해석하고 모델 성능을 조정하는 과학 및 기술은 이 연습의 범위를 벗어납니다. 추가 도움말을 보려면 다음 문서를 확인할 수 있습니다.
- [Machine Learning Studio(클래식)에서 모델 성능을 평가하는 방법](evaluate-model-performance.md)
- [Machine Learning Studio(클래식)에서 알고리즘을 최적화하는 매개 변수 선택](algorithm-parameters-optimize.md)
- [Machine Learning Studio(클래식)에서 모델 결과 해석](interpret-model-results.md)

> [!TIP]
> 실험을 실행할 때마다 해당 반복에 대한 레코드가 실행 기록에서 유지됩니다. 이러한 반복을 확인하고 캔버스 아래에서 **실행 기록 보기** 를 클릭하여 원하는 반복으로 돌아갈 수 있습니다. **속성** 창에서 **이전 실행** 을 클릭하여 열었던 반복의 바로 이전 반복으로 돌아갈 수도 있습니다.
> 
> 캔버스 아래에서 **다른 이름으로 저장** 을 클릭하여 실험을 반복하도록 복사본을 만들 수 있습니다. 
> 실험의 **요약** 및 **설명** 속성을 사용하면 실험 반복에서 시도한 항목을 기록할 수 있습니다.
> 
> 자세한 내용은 [Machine Learning Studio(클래식)에서 반복 실험 관리](manage-experiment-iterations.md)를 참조하세요.  
> 
> 

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [machine-learning-studio-clean-up](../../../includes/machine-learning-studio-clean-up.md)]

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음과 같은 단계를 완료했습니다. 
 
> [!div class="checklist"]
> * 실험 만들기
> * 여러 모델 학습시키기
> * 모델 점수 매기기 및 평가

이제 이 데이터에 사용할 모델 배포 준비가 완료되었습니다.

> [!div class="nextstepaction"]
> [자습서 3 - 모델 배포](tutorial-part3-credit-risk-deploy.md)


<!-- Module References -->
[execute-r-script]: /azure/machine-learning/studio-module-reference/execute-r-script
[edit-metadata]: /azure/machine-learning/studio-module-reference/edit-metadata
[split]: /azure/machine-learning/studio-module-reference/split-data
[evaluate-model]: /azure/machine-learning/studio-module-reference/evaluate-model
[execute-r-script]: /azure/machine-learning/studio-module-reference/execute-r-script
[normalize-data]: /azure/machine-learning/studio-module-reference/normalize-data
[score-model]: /azure/machine-learning/studio-module-reference/score-model
[train-model]: /azure/machine-learning/studio-module-reference/train-model
[two-class-boosted-decision-tree]: /azure/machine-learning/studio-module-reference/two-class-boosted-decision-tree
[two-class-support-vector-machine]: /azure/machine-learning/studio-module-reference/two-class-support-vector-machine
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/