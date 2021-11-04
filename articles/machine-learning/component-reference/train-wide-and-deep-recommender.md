---
title: 전체 & 심층 추천 구성 요소를 사용 합니다.
titleSuffix: Azure Machine Learning
description: Azure Machine Learning designer에서 전체 & 심층적 추천 구성 요소를 사용 하 여 권장 사항 모델을 학습 하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 03/19/2021
ms.openlocfilehash: 36d83468faaf5b3df5d8516fd788773a9f8187d4
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131570152"
---
# <a name="train-wide--deep-recommender"></a>Wide & Deep Recommender 학습
이 문서에서는 Azure Machine Learning designer에서 **전체 & 심층 추천** 구성 요소를 사용 하 여 권장 사항 모델을 학습 하는 방법을 설명 합니다. 이 구성 요소는 Google에서 제안 하는 넓은 & 심층 학습을 기반으로 합니다.

**열차 Wide & Deep 추천** 구성 요소는 사용자-항목-등급 삼중 쌍의 데이터 집합을 읽고, 선택적으로 일부 사용자 및 항목 기능을 읽습니다. 이는 학습된 Wide & Deep Recommender 추천을 반환합니다.  그런 다음, 학습 된 모델을 사용 하 여 [전체 점수 및 심층 추천](score-wide-and-deep-recommender.md) 구성 요소를 사용 하 여 등급 예측 또는 권장 사항을 생성할 수 있습니다.  

<!-- Currently, **Train Wide & Deep Recommender** component supports both single node and distributed training. -->

## <a name="more-about-recommendation-models-and-the-wide--deep-recommender"></a>추천 모델 및 Wide & Deep Recommender에 대한 자세한 내용  

추천 시스템은 기본적으로 시스템의 *사용자* 에게 *항목* 을 하나 이상 추천하는 데 사용됩니다. 항목에는 영화, 식당, 책, 노래 등이 포함될 수 있습니다. 그리고 사용자는 사람, 사람 그룹 또는 항목 기본 설정이 지정된 기타 엔터티일 수 있습니다.  

추천 시스템에는 두 가지 기본 접근 방식이 사용됩니다. 

+ 그 중 첫 번째는 사용자와 항목 둘 다에 대해 기능을 사용하는 **콘텐츠 기반** 방식입니다. 사용자는 연령/성별 등의 속성으로 설명할 수 있고 항목은 작성자/제조업체 등의 속성으로 설명할 수 있습니다. 콘텐츠 기반 추천 시스템의 일반적인 예는 소셜 매치메이킹 사이트에서 확인할 수 있습니다. 
+ 두 번째 방식은 **공동 작업 필터링** 입니다. 이 방식에서는 사용자와 항목의 식별자만을 사용하며, 사용자가 항목에 지정한 등급의 스파스 행렬에서 이러한 엔터티와 관련된 암시적 정보를 가져옵니다. 특정 사용자에 대한 정보는 해당 사용자가 등급을 지정한 항목과 같은 항목에 등급을 지정한 다른 사용자로부터 파악할 수 있습니다.  

Wide & Deep Recommender는 콘텐츠 기반 접근 방식으로 공동 작업 필터링을 사용하여 이러한 접근 방식을 결합합니다. **하이브리드 추천** 이라고 할 수 있습니다. 

작동 방식: 시스템을 사용한 지 얼마 되지 않은 사용자의 경우에는 해당 사용자에 대한 특징 정보를 활용하여 예측 성능을 개선합니다. 따라서 일반적으로 발생하는 "콜드 부팅" 문제를 방지할 수 있습니다. 그러나 특정 사용자의 등급 정보가 충분히 수집되면 기능뿐이 아니라 사용자의 특정 등급에 따라 해당 사용자에 대해 완전히 개인 설정된 예측을 할 수 있습니다. 따라서 추천 방식이 콘텐츠 기반 추천에서 공동 작업 필터링 기반 추천으로 원활하게 전환됩니다. 사용자 또는 항목 특징이 제공되지 않더라도 Wide & Deep Recommender는 공동 작업 필터링 모드에서 계속 작동합니다.  

Wide & Deep 추천 및 기본 확률 알고리즘에 대한 자세한 내용은 관련 연구 논문인 [Wide & Deep Learning for Recommender Systems](https://arxiv.org/pdf/1606.07792.pdf)에 있습니다.  

## <a name="how-to-configure-train-wide--deep-recommender"></a>Wide & Deep Recommender 추천 학습을 구성하는 방법  

+ [학습 데이터 준비](#prepare-data)
+ [모델 학습](#train-the-model)

### <a name="prepare-data"></a>데이터 준비

구성 요소를 사용 하기 전에 데이터가 권장 사항 모델에 대해 예상 되는 형식 인지 확인 합니다. **사용자-항목-등급 삼중 쌍** 의 학습 데이터 세트는 필수이지만 별도의 데이터 세트에 사용자 특징 및 항목 특징(사용 가능한 경우)을 포함할 수도 있습니다.

#### <a name="required-dataset-of-user-item-ratings"></a>사용자-항목-등급 데이터 세트 필요

학습에 사용되는 입력 데이터는 정확한 형식의 데이터를 정확한 형식으로 포함해야 합니다. 

+ 첫 번째 열에는 사용자 식별자가 포함되어야 합니다.
+ 두 번째 열에는 항목 식별자가 포함되어야 합니다.
+ 세 번째 열은 사용자-항목 쌍의 등급을 포함합니다. 등급 값은 숫자 형식이어야 합니다. 

예를 들어 일반적인 사용자-항목-등급 세트는 다음과 같습니다.

|UserId|MovieId|등급|
|------------|-------------|------------|
|1|68646|10|
|223|31381|10|

#### <a name="user-features-dataset-optional"></a>사용자 특징 데이터 세트(옵션)

**사용자 특징** 의 데이터 세트는 사용자에 대한 식별자를 포함하고 사용자-항목-등급 데이터 세트의 첫 번째 열에 제공된 것과 동일한 식별자를 사용해야 합니다. 나머지 열은 사용자에 대해 설명하는 다양한 특징을 포함할 수 있습니다.  

예를 들어 일반적인 사용자 특징 집합은 다음과 같습니다. 

|UserId|나이|성별|Interest|위치|
|------------|--------------|-----------------------|---------------|------------|
|1|25|male| 드라마    |유럽|
|223|40|female|로맨스|아시아|

#### <a name="item-features-dataset-optional"></a>항목 특징 데이터 세트(옵션)

항목 기능 데이터 집합의 첫 번째 열에는 항목 식별자가 포함되어 있어야 합니다. 나머지 열에는 항목에 대한 설명 특징이 개수에 제한 없이 포함될 수 있습니다.  

예를 들어 일반적인 항목 특징 집합은 다음과 같습니다.  

|MovieId|제목|원어|장르|Year|
|-------------|-------------|-------------------|-----------|---------------|
|68646|대부|영어|드라마|1972|
|31381|바람과 함께 사라지다|영어|기록|1939|

### <a name="train-the-model"></a>모델 학습

1.  Designer에서 **전체 실험 및 심층 추천** 구성 요소를 실험에 추가 하 고 학습 데이터 집합에 연결 합니다.  
  
2. 사용자 기능 및/또는 항목 기능 중 하나에 대 한 별도의 데이터 집합을 사용 하는 경우이를 **학습 와이드 및 심층 추천** 구성 요소에 연결 합니다.  
  
    - **사용자 특징 데이터 세트**: 사용자에 대해 설명하는 데이터 세트를 두 번째 입력에 연결합니다.
    - **항목 특징 데이터 세트**: 항목에 대해 설명하는 데이터 세트를 세 번째 입력에 연결합니다.  
    
3.  **Epoch**: 알고리즘이 전체 학습 데이터를 처리해야 하는 횟수를 표시합니다. 

    이 수가 높을수록 교육에 적합하지만 학습에 걸리는 시간이 늘어나고 과잉 맞춤이 발생할 수도 있습니다.

4. **일괄 처리 크기**: 한 학습 단계에서 사용한 학습 예의 수를 입력합니다. 

     이 하이퍼 매개 변수는 학습 속도에 영향을 줄 수 있습니다. 일괄 처리 크기가 높으면 epoch에 드는 시간 비용을 줄일 수 있지만 수렴 시간은 늘어날 수 있습니다. 일괄 처리 용량이 너무 커서 GPU/CPU에 맞지 않는 경우 메모리 오류가 발생할 수 있습니다.

5.  **범위 부분 최적화 프로그램**: 모델의 범위 부분에 경사를 적용할 최적화 프로그램 하나를 선택합니다.

6.  **범위 최적화 프로그램 학습 속도**: 범위 부분 최적화 프로그램의 학습 속도를 정의하는 0.0에서 2.0사이의 숫자를 입력합니다.

    이 하이퍼 매개 변수는 최소 손실 함수를 향해 이동하는 동안 각 학습 단계의 단계 크기를 결정합니다. 학습률이 크면 최소값을 넘는 학습 점프가 발생할 수 있으며, 학습률이 너무 작으면 수렴 문제가 발생할 수 있습니다.

7.  **교차 특징 차원**: 원하는 사용자 ID 및 항목 ID 특징을 입력하여 차원을 입력합니다. 

    Wide & Deep Recommender는 기본적으로 사용자 ID 및 항목 ID 특징에 대한 제품 간 변환을 수행합니다. 이 숫자에 따라 교차 결과가 해시되어 차원이 확인됩니다.

8.  **깊이 부분 최적화 프로그램**: 모델의 깊이 부분에 경사를 적용할 최적화 프로그램 하나를 선택합니다.

9.  **깊이 최적화 프로그램 학습 속도**: 깊이 부분 최적화 프로그램의 학습 속도를 정의하는 0.0에서 2.0사이의 숫자를 입력합니다.

10.  **사용자 포함 차원**: 사용자 ID 포함의 차원을 지정하는 정수를 입력합니다.

     Wide & Deep Recommender는 범위 부분과 깊이 부분에 대한 공유 사용자 ID 포함 및 항목 ID 포함을 만듭니다.

11.  **항목 포함 차원**: 항목 ID 포함의 차원을 지정하는 정수를 입력합니다.

12.  **범주 특징 포함 차원**: 범주 특징 포함의 차원을 지정하는 정수를 입력합니다.

     Wide & Deep Recommender의 깊이 구성 요소에 포함된 벡터는 각 범주 특징에 대해 학습됩니다. 이러한 포함 벡터는 동일한 차원을 공유합니다.

13.  **숨겨진 단위**: 깊이 구성 요소의 숨겨진 노드 수를 입력합니다. 각 계층의 노드 번호는 쉼표로 구분됩니다. 예를 들어 "1000,500,100"을 입력하면 깊이 구성 요소에는 세 개의 계층이 있고, 첫 번째부터 마지막 계층에는 각각 1000노드, 500노드, 100노드가 있습니다.

14.  **활성화 함수**: 각 계층에 적용되는 활성화 함수 하나를 선택합니다. 기본값은 ReLU입니다.

15.  **드롭아웃**: 학습 중에 각 계층에서 출력이 삭제될 확률을 결정하는 0.0과 1.0사이의 숫자를 입력합니다.

     드롭아웃은 신경망의 과잉 맞춤을 방지하는 정규화 메서드입니다. 이 값은 일반적으로 네트워크 및 작업에 가장 적합한 것으로 보이는 0.5부터 시작하여 결정합니다.

16.  **일괄 처리 정규화**: 깊이 구성 요소에서 숨겨진 각 계층 후에 일괄 처리 정규화를 사용하려면 이 옵션을 선택합니다.

     일괄 처리 정규화는 네트워크 학습 중에 내부 전환 문제를 해결하는 기술입니다. 일반적으로 네트워크의 속도, 성능 및 안정성을 향상시키는 데 도움이 될 수 있습니다. 

17.  파이프라인을 실행합니다.


<!-- ## Distributed training

In distributed training the workload to train a model is split up and shared among multiple mini processors, called worker nodes. These worker nodes work in parallel to speed up model training. Currently the designer support distributed training for **Train Wide & Deep Recommender** component.

### How to enable distributed training

To enable distributed training for **Train Wide & Deep Recommender** component, you can set in **Run settings** in the right pane of the component. Only **[AML Compute cluster](../how-to-create-attach-compute-cluster.md?tabs=python)** is supported for distributed training.

1. Select the component and open the right panel. Expand the **Run settings** section.

    [![Screenshot showing how to set distributed training in run setting](./media/module/distributed-training-run-setting.png)](./media/module/distributed-training-run-setting.png#lightbox)

1. Make sure you have select AML compute for the compute target.

1. In **Resource layout** section, you need to set the following values:

    - **Node count**: Number of nodes in the compute target used for training. It should be **less than or equal to** the **Maximum number of nodes** your compute cluster. By default it is 1, which means single node job.

    - **Process count per node**: Number of processes triggered per node. It should be **less than or equal to** the **Processing Unit** of your compute. By default it is 1, which means single node job.

    You can check the **Maximum number of nodes** and **Processing Unit** of your compute by clicking the compute name into the compute detail page.

    [![Screenshot showing how to check compute cluster](./media/module/compute-cluster-node.png)](./media/module/compute-cluster-node.png#lightbox)

You can learn more about distributed training in Azure Machine Learning [here](../concept-distributed-training.md).


### Troubleshooting for distributed training

If you enable distributed training for this component, there will be driver logs for each process. `70_driver_log_0` is for master process. You can check driver logs for error details of each process under **Outputs+logs** tab in the right pane.

[![Screenshot showing driver log](./media/module/distributed-training-error-driver-log.png)](./media/module/distributed-training-error-driver-log.png#lightbox) 

If the component enabled distributed training fails without any `70_driver` logs, you can check `70_mpi_log` for error details.

The following example shows a common error that is **Process count per node** is larger than **Processing Unit** of the compute.

[![Screenshot showing mpi log](./media/module/distributed-training-error-mpi-log.png)](./media/module/distributed-training-error-mpi-log.png#lightbox)

## Results

After pipeline run is completed, to use the model for scoring, connect the [Train Wide and Deep Recommender](train-wide-and-deep-recommender.md) to [Score Wide and Deep Recommender](score-wide-and-deep-recommender.md), to predict values for new input examples.
 -->

##  <a name="technical-notes"></a>기술 정보

Wide & Deep은 공통적으로 선형 모델 및 심층 신경망을 학습하여 기억 및 일반화의 장점을 결합합니다. 범위 구성 요소는 원시 특징 집합을 사용하고 특징 상호 작용을 기억하기 위한 변환을 제공합니다. 더 적은 기능 엔지니어링을 사용하여 심층 구성 요소는 낮은 차원의 밀도 높은 기능 포함을 통해 보이지 않는 기능 조합을 일반화합니다. 

넓은 & 심층 추천 구현에서 구성 요소는 기본 모델 구조를 사용 합니다. 범위 구성 요소는 사용자 포함, 항목 포함, 사용자 ID와 항목 ID의 제품 간 변환을 입력으로 사용합니다. 모델의 세부적인 부분에서 포함 벡터는 각 범주 기능에 대해 학습됩니다. 이러한 벡터는 다른 숫자 특징 벡터와 함께 전체 피드-전달 신경망에 제공됩니다. 최종 출력 로그의 합계를 예측으로 합산하여 범위 부분과 깊이 부분을 결합해 최종적으로 공동 학습을 위한 하나의 일반적인 손실 함수로 이동합니다.


## <a name="next-steps"></a>다음 단계

Azure Machine Learning [사용할 수 있는 구성 요소 집합](component-reference.md) 을 참조 하세요.