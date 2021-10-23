---
title: 자동화된 ML(AutoML)이란? AutoML
titleSuffix: Azure Machine Learning
description: Azure Machine Learning이 자동화된 기계 학습과 함께 제공하는 매개 변수와 기준을 사용하여 모델을 자동으로 생성할 수 있는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: automl
ms.topic: conceptual
author: cartacioS
ms.author: sacartac
ms.date: 07/01/2021
ms.custom: automl
ms.openlocfilehash: c08eae9654e01fda15889ac6fe65f99de2d73bb6
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130261808"
---
# <a name="what-is-automated-machine-learning-automl"></a>자동화된 Machine Learning(AutoML)이란?

자동화된 Machine Learning(자동화된 ML 또는 AutoML이라고도 함)은 시간 소모적이고 반복적인 기계 학습 모델 개발 작업을 자동화하는 프로세스입니다. 데이터 과학자, 분석가 및 개발자는 모델 품질을 유지하면서 확장성, 효율성 및 생산성이 높은 ML 모델을 빌드할 수 있습니다. Azure Machine Learning에서 자동화된 ML은 [Microsoft Research 부문](https://www.microsoft.com/research/project/automl/)의 혁신을 기반으로 합니다.

기존의 기계 학습 모델 개발은 리소스를 많이 사용하므로 수십 개의 모델을 생성하고 비교하는 데 상당한 도메인 지식과 시간이 필요합니다. 자동화된 Machine Learning을 사용하면 프로덕션 준비 ML 모델을 매우 쉽고 효율적으로 얻는 데 걸리는 시간을 단축할 수 있습니다.

## <a name="ways-to-use-automl-in-azure-machine-learning"></a>Azure Machine Learning에서 AutoML을 사용하는 방법

Azure Machine Learning은 자동화된 ML 작업을 위해 다음 두 가지 환경을 제공합니다. [각 환경에서 사용 가능한 기능](#parity)을 이해하려면 다음 섹션을 참조하세요.

* 코드 경험이 있는 고객의 경우 [Azure Machine Learning Python SDK](/python/api/overview/azure/ml/intro).  [자습서: 자동화된 Machine Learning을 사용하여 택시 요금 예측](tutorial-auto-train-models.md)을 시작합니다.

* 코드가 제한적이거나 코드가 없는 경험 고객의 경우 [https://ml.azure.com](https://ml.azure.com/)에 있는 Azure Machine Learning 스튜디오.  자습서 시작:
    * [자습서: Azure Machine Learning에서 자동화된 ML을 사용하여 분류 모델 만들기](tutorial-first-experiment-automated-ml.md).
    *  [자습서: 자동화된 기계 학습으로 수요 예측](tutorial-automated-ml-forecast.md)

<a name="parity"></a>

## <a name="automl-settings-and-configuration"></a>AutoML 설정 및 구성

### <a name="experiment-settings"></a>실험 설정 

자동화된 ML 실험은 다음 설정을 사용하여 구성할 수 있습니다. 

| |Python SDK|스튜디오 웹 환경|
|----|:----:|:----:|
|**학습/유효성 검사 세트로 데이터 분할**| ✓|✓
|**분류, 회귀 & 예측과 같은 ML 작업을 지원합니다.**| ✓| ✓
|**Computer Vision 작업 지원(미리 보기): 이미지 분류, 개체 감지 & 인스턴스 구분**| ✓| 
|**기본 메트릭 기반 최적화**| ✓| ✓
|**Azure ML 컴퓨팅을 컴퓨팅 대상으로 지원** | ✓|✓
|**예측 구간, 대상 지연 시간 및 롤링 기간 구성**|✓|✓
|**종료 조건 설정** |✓|✓ 
|**동시 반복 설정**| ✓|✓
|**열 삭제**| ✓|✓
|**알고리즘 차단**|✓|✓
|**교차 유효성 검사** |✓|✓
|**Azure Databricks 클러스터에 대한 학습 지원**| ✓|
|**엔지니어링된 기능 이름 보기**|✓|
|**기능화 요약**| ✓|
|**휴일 기능화**|✓|
|**로그 파일의 세부 정보 표시 수준**| ✓|

### <a name="model-settings"></a>모델 설정

다음 설정은 자동화된 ML 실험의 결과로 최상의 모델에 적용할 수 있습니다.

| |Python SDK|스튜디오 웹 환경|
|----|:----:|:----:|
|**최상의 모델 등록, 배포, 설명 가능성**| ✓|✓|
|**투표 앙상블 및 스택 앙상블 모델 사용**| ✓|✓|
|**기본이 아닌 메트릭에 따라 최상의 모델 표시**|✓||
|**ONNX 모델 호환성 사용/사용 안 함**|✓||
|**모델 테스트** | ✓| |

### <a name="run-control-settings"></a>실행 제어 설정

다음 설정을 사용하여 실험 실행 및 자식 실행을 검토하고 제어할 수 있습니다. 

| |Python SDK|스튜디오 웹 환경|
|----|:----:|:----:|
|**실행 요약 테이블**| ✓|✓|
|**실행 및 자식 실행 취소**| ✓|✓|
|**가드 레일 가져오기**| ✓|✓|
|**실행 일시 중지 및 다시 시작**| ✓| |

## <a name="when-to-use-automl-classification-regression-forecasting--computer-vision"></a>AutoML을 사용하는 경우: 분류, 회귀, 예측 & 컴퓨터 비전

Azure Machine Learning에서 지정한 대상 메트릭을 사용하여 모델을 학습시키고 튜닝하도록 하려면 자동화된 ML을 적용합니다. 자동화된 ML은 기계 학습 모델 개발 프로세스를 보편화하고, 데이터 과학 전문 지식과 관계없이 문제에 대한 엔드투엔드 기계 학습 파이프라인을 식별할 수 있도록 사용자의 역량을 강화합니다.

업계 전반의 데이터 과학자, 분석가 및 개발자는 자동화된 ML을 사용하여 다음을 수행할 수 있습니다.
+ 광범위한 프로그래밍 지식 없이 ML 솔루션 구현
+ 시간 및 리소스 절약
+ 데이터 과학 모범 사례 활용
+ 신속한 문제 해결 제공

### <a name="classification"></a>분류

분류는 일반적인 기계 학습 작업입니다. 분류는 모델에서 학습 데이터를 사용하여 학습하고, 이러한 학습을 새로운 데이터에 적용하는 지도 학습의 한 유형입니다. Azure Machine Learning은 특히 이러한 작업에 적합한 기능화를 제공합니다(예: 분류를 위한 심층 신경망 텍스트 피처라이저(featurizer)). [기능화 옵션](how-to-configure-auto-features.md#featurization)에 대해 자세히 알아보세요. 

분류 모델의 주요 목표는 학습 데이터로부터의 학습을 기반으로 하여 새 데이터가 분류될 범주를 예측하는 것입니다. 일반적인 분류 예로 사기 탐지, 필기 인식 및 개체 검색이 있습니다. [자동화된 ML을 사용하여 분류 모델 만들기](tutorial-first-experiment-automated-ml.md)에 대한 자세한 내용 및 예제를 참조하세요.

분류 및 자동화된 Machine Learning의 예제는 [사기 탐지](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-credit-card-fraud/auto-ml-classification-credit-card-fraud.ipynb), [마케팅 예측](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb) 및 [뉴스 그룹 데이터 분류](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-text-dnn/auto-ml-classification-text-dnn.ipynb) Python Notebook을 참조하세요.

### <a name="regression"></a>회귀

분류와 마찬가지로 회귀 작업도 일반적인 지도 학습 작업입니다. Azure Machine Learning은 특히 [이러한 작업에 대한 기능화](how-to-configure-auto-features.md#featurization)를 제공합니다.

범주별 예측 출력 값인 분류와는 달리 회귀 모델은 독립 예측 변수를 기반으로 하여 숫자 출력 값을 예측합니다. 회귀 분석이 목표는 한 변수가 다른 변수에 미치는 영향을 추정하여 이러한 독립 예측 변수 간의 관계를 설정하는 데 도움을 주는 것입니다. 예를 들어 가스 연비, 안전 등급 등과 같은 기능을 기반으로 하는 자동차 가격이 있습니다. [자동화된 Machine Learning을 사용한 회귀](tutorial-auto-train-models.md)의 예제를 참조하여 자세히 알아보세요.

예측에 대한 회귀 및 자동화된 Machine Learning의 예제는 [CPU 성능 예측](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/regression-explanation-featurization/auto-ml-regression-explanation-featurization.ipynb) Python Notebook을 참조하세요. 

### <a name="time-series-forecasting"></a>시계열 예측

예측 빌드는 수익, 재고, 판매 또는 고객 수요에 관계없이 모든 비즈니스의 필수적인 부분입니다. 자동화된 ML을 사용하여 기술과 방법을 결합하고 추천되는 고품질 시계열 예측을 구현할 수 있습니다. [시계열 예측에 대한 자동화된 Machine Learning](how-to-auto-train-forecast.md) 방법 문서를 사용하여 자세히 알아보세요. 

자동화된 시계열 실험은 다변량 회귀 분석 문제로 처리됩니다. 이전 시계열 값이 "피벗"되어 다른 예측 변수와 함께 회귀 변수의 추가 차원이 됩니다. 이 방법은 클래식 시계열 방법과 달리 학습 중에 여러 컨텍스트 변수 및 해당 변수 간의 관계를 자연스럽게 통합할 수 있는 장점이 있습니다. 자동화된 ML은 데이터 세트 및 예측 구간의 모든 항목에 대해 종종 내부적으로 분기된 단일 모델을 학습시킵니다. 따라서 모델 매개 변수를 추정하기 위해 더 많은 데이터를 사용할 수 있으며 미확인 계열에 대한 일반화가 가능합니다.

고급 예측 구성은 다음과 같습니다.
* 휴일 검색 및 기능화
* 시계열 및 DNN 학습자(Auto-ARIMA, Prophet, ForecastTCN)
* 그룹화를 통해 많은 모델 지원
* 롤링 원본 교차 유효성 검사
* 구성 가능한 지연
* 롤링 기간 집계 기능


예측에 대한 회귀 및 자동화된 Machine Learning의 예제는 [판매 예측](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-orange-juice-sales/auto-ml-forecasting-orange-juice-sales.ipynb), [수요 예측](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb) 및 [음료 생산 예측](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-beer-remote/auto-ml-forecasting-beer-remote.ipynb) Python Notebook을 참조하세요.

### <a name="computer-vision-preview"></a>컴퓨터 비전 (미리 보기)

> [!IMPORTANT]
> 이 기능은 현재 공개 미리 보기로 제공됩니다. 이 미리 보기 버전은 서비스 수준 계약 없이 제공됩니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

이미지에 대 한 자동화 된 ML (미리 보기)는 이미지 분류 및 개체 검색 등의 시나리오에서 이미지 데이터에 대해 학습 된 모델을 쉽게 생성할 수 있도록 하는 컴퓨터 비전 작업에 대 한 지원을 추가 합니다. 

이 기능을 사용하여 다음을 수행할 수 있습니다. 
 
* [Azure Machine Learning 데이터 레이블](./how-to-create-image-labeling-projects.md) 기능과 원활 하 게 통합
* 레이블이 지정 된 데이터를 사용 하 여 이미지 모델 생성
* 모델 알고리즘을 지정 하 고 하이퍼 매개 변수를 조정 하 여 모델 성능을 최적화 합니다. 
* 결과 모델을 Azure Machine Learning에서 웹 서비스로 다운로드 하거나 배포 합니다. 
* Azure Machine Learning [mlops](concept-model-management-and-deployment.md) 및 [ML Pipelines](concept-ml-pipelines.md) 기능을 활용 하 여 규모에 운영. 

비전 작업을 위한 automl 모델 작성은 Azure ML Python SDK를 통해 지원 됩니다. 결과 실험 실행, 모델 및 출력은 Azure Machine Learning studio UI에서 액세스할 수 있습니다.

[컴퓨터 비전 모델에 대해 AutoML 교육을 설정](how-to-auto-train-image-models.md)하는 방법에 대해 알아봅니다.

![컴퓨터 비전 작업 예. 이미지 원본: http://cs231n.stanford.edu/slides/2021/lecture_15.pdf ](./media/concept-automated-ml/automl-computer-vision-tasks.png) 이미지 원본: http://cs231n.stanford.edu/slides/2021/lecture_15.pdf

이미지에 대 한 자동화 된 ML은 다음과 같은 컴퓨터 비전 작업을 지원 합니다. 

작업 | 설명
----|----
다중 클래스 이미지 분류 | 이미지가 클래스 집합에서 단일 레이블만으로 분류 되는 작업-예를 들어 각 이미지는 ' cat ' 또는 ' dog ' 또는 ' 오리 '의 이미지로 분류 됩니다.
다중 레이블 이미지 분류 | 이미지에 레이블 집합에서 하나 이상의 레이블이 있을 수 있는 작업 (예: 이미지에 ' cat ' 및 ' dog ' 모두 표시)
개체 감지| 이미지의 개체를 식별 하 고 경계 상자를 사용 하 여 각 개체를 찾는 작업입니다. 예를 들어 이미지에서 모든 위치와 고양이를 찾고 각 개체 주위에 경계 상자를 그릴 수 있습니다.
인스턴스 조각화 | 픽셀 수준에서 이미지의 개체를 식별 하는 작업으로, 이미지의 각 개체 주위에 다각형을 그립니다.

## <a name="how-automated-ml-works"></a>자동화된 ML 작동 방법

Azure Machine Learning은 학습 중에 다양한 알고리즘과 매개 변수를 시도하는 많은 파이프라인을 동시에 만듭니다. 이 서비스는 기능 선택 항목과 쌍을 이루는 ML 알고리즘을 반복하며, 각 반복에서 학습 점수가 있는 모델이 생성됩니다. 점수가 높을수록 모델이 데이터에 더 "적합"하다고 간주됩니다.  실험에 정의된 종료 조건에 도달하면 중지됩니다. 

**Azure Machine Learning** 을 사용하면 다음 단계를 사용하여 자동화된 ML 학습 실험을 설계하고 실행할 수 있습니다.

1. 분류, 예측, 재발 또는 컴퓨터 비전 (미리 보기) **의 해결할 ML 문제를 식별** 합니다.

1. **Python SDK 또는 스튜디오 웹 환경을 사용할지 선택**: [Python SDK와 스튜디오 웹 환경](#parity) 간의 패리티에 대해 알아봅니다.

   * 제한된 코드 환경 또는 코드리스 환경인 경우 [https://ml.azure.com](https://ml.azure.com/)에서 Azure Machine Learning Studio 웹 환경을 사용해 봅니다.  
   * Python 개발자의 경우 [Azure Machine Learning Python SDK](how-to-configure-auto-train.md)를 확인합니다. 
    
1. **레이블이 지정된 학습 데이터의 원본 및 형식 지정**: numpy 배열 또는 pandas 데이터 프레임

1. **모델 학습을 위한 컴퓨팅 대상 구성**(예: [로컬 컴퓨터, Azure Machine Learning 컴퓨팅, 원격 VM 또는 Azure Databasericks](how-to-set-up-training-targets.md)).

1. **자동화된 Machine Learning 매개 변수 구성**: 여러 모델에 대한 반복 횟수, 서로 다른 모델, 하이퍼 매개 변수 설정, 고급 전처리/기능화 및 가장 적합한 모델을 결정할 때 살펴볼 메트릭을 결정하는 매개 변수입니다.  
1. **학습 실행 제출**

1. **결과 검토** 

다음 다이어그램에서는 이 프로세스를 보여 줍니다. 
![자동화된 Machine Learning](./media/concept-automated-ml/automl-concept-diagram2.png)


실행 중에 수집된 [메트릭을 포함](how-to-understand-automated-ml.md)한 기록된 실행 정보를 검사할 수도 있습니다. 학습 실행에서는 모델 및 데이터 전처리가 포함된 Python 직렬화 개체(`.pkl` 파일)가 생성됩니다.

모델 빌드가 자동화되는 동안 [중요하거나 관련된 기능이 생성된 모델에 있는 상태](how-to-configure-auto-train.md#explain)를 알아볼 수도 있습니다.


> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2Xc9t]


## <a name="feature-engineering"></a>특징 엔지니어링

기능 엔지니어링은 데이터에 대한 도메인 정보를 사용하여 ML 알고리즘의 기능을 개선하는 데 도움이 되는 기능을 만드는 프로세스입니다. Azure Machine Learning에서 확장 및 표준화 기술이 기능 엔지니어링을 용이하게 하기 위해 적용됩니다. 이러한 기술 및 기능 엔지니어링을 통칭하여 기능화라고 합니다.

자동화된 Machine Learning 실험의 경우 기능화가 자동으로 적용되지만 데이터에 따라 사용자 지정할 수도 있습니다. [포함된 기능화에 대해 자세히 알아보세요](how-to-configure-auto-features.md#featurization).  

> [!NOTE]
> 자동화된 Machine Learning 기능화 단계(기능 정규화, 누락된 데이터 처리, 텍스트를 숫자로 변환 등)는 기본 모델의 일부가 됩니다. 예측에 모델을 사용하는 경우 학습 중에 적용되는 동일한 기능화 단계가 입력 데이터에 자동으로 적용됩니다.

### <a name="automatic-featurization-standard"></a>자동 기능화(표준)

모든 자동화된 Machine Learning 실험에서 알고리즘 성능을 향상시킬 수 있도록 데이터가 자동으로 크기 조정되거나 정규화됩니다. 모델 학습 중에 다음 크기 조정 또는 정규화 기술 중 하나가 각 모델에 적용됩니다. AutoML을 통해 모델에서 [과잉 맞춤 및 불균형 데이터를 방지](concept-manage-ml-pitfalls.md)하는 방법을 알아봅니다.

|크기 조정&nbsp;&&nbsp;처리| Description |
| ------------- | ------------- |
| [StandardScaleWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.StandardScaler.html)  | 평균을 제거하고 단위 분산으로 크기 조정하여 기능을 표준화합니다.  |
| [MinMaxScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MinMaxScaler.html)  | 해당 열의 최솟값과 최댓값을 기준으로 각 기능의 크기를 조정하여 기능을 변환합니다.  |
| [MaxAbsScaler](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MaxAbsScaler.html#sklearn.preprocessing.MaxAbsScaler) |해당 최대 절대값을 기준으로 각 기능의 크기를 조정합니다. |
| [RobustScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.RobustScaler.html) | 분위수 범위에 따른 스케일링 기능 |
| [PCA](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.PCA.html) |데이터의 특이값 분해를 사용하여 데이터를 더 낮은 차원 공간으로 프로젝션하는 선형 차원 축소입니다. |
| [TruncatedSVDWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.TruncatedSVD.html) |이 변환기는 잘린 SVD(특이값)를 통해 선형 차원 축소를 수행합니다. 이 예측 도구는 PCA와 달리 특이값 분해를 계산하기 전에 데이터를 중앙 집중화하지 않습니다. 즉, scipy.sparse 행렬을 효율적으로 사용할 수 있습니다. |
| [SparseNormalizer](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.Normalizer.html) | 0이 아닌 성분이 하나 이상 있는 각 샘플(즉, 데이터 행렬의 각 행)의 크기는 다른 샘플과 독립적으로 다시 조정되어 해당 표준(l1 또는 l2)이 1이 됩니다. |

### <a name="customize-featurization"></a>기능화 사용자 지정

인코딩 및 변환과 같은 추가 기능 엔지니어링 기술을 사용할 수도 있습니다. 

다음을 사용하여 이 설정을 사용하도록 설정합니다.

+ Azure Machine Learning Studio: [이러한 단계](how-to-use-automated-ml-for-ml-models.md#customize-featurization)를 사용하여 **추가 구성 보기** 섹션에서 **자동 기능화** 를 사용하도록 설정합니다.

+ Python SDK: [AutoMLConfig](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) 개체에서 `"feauturization": 'auto' / 'off' / 'FeaturizationConfig'`를 지정합니다. [기능화 사용](how-to-configure-auto-features.md)에 대해 자세히 알아봅니다. 

## <a name="ensemble-models"></a><a name="ensemble"></a> 앙상블 모델

자동화된 Machine Learning은 기본적으로 사용하도록 설정되는 앙상블 모델을 지원합니다. 앙상블 학습은 단일 모델을 사용하는 대신 여러 모델을 결합하여 기계 학습 결과와 예측 성능을 향상시킵니다. 앙상블 반복은 실행의 최종 반복으로 나타납니다. 자동화된 Machine Learning은 모델 결합을 위해 투표 및 스택 앙상블 방법을 모두 사용합니다.

* **투표**: 예측된 클래스 확률(분류 작업의 경우) 또는 예측된 회귀 목표(회귀 작업의 경우)의 가중 평균을 기반으로 하여 예측합니다.
* **스택**: 스택은 다른 형식의 모델을 결합하고 개별 모델의 출력을 기반으로 하여 메타 모델을 학습시킵니다. 현재 기본 메타 모델은 분류 작업의 경우 LogisticRegression이고 회귀/예측 작업의 경우 ElasticNet입니다.

정렬된 앙상블 초기화를 사용하는 [Caruana 앙상블 선택 알고리즘](http://www.niculescu-mizil.org/papers/shotgun.icml04.revised.rev2.pdf)은 앙상블 내에서 사용할 모델을 결정하는 데 사용됩니다. 높은 수준에서 이 알고리즘은 개별 점수가 가장 높은 최대 5개의 모델을 사용하여 앙상블을 초기화하고, 초기 앙상블 저하를 방지하기 위해 이러한 모델이 가장 높은 점수의 5% 임계값 내에 있는지 확인합니다. 그런 다음, 각 앙상블 반복마다 새 모델이 기존 앙상블에 추가되고 결과 점수가 계산됩니다. 새 모델에서 기존 앙상블 점수가 향상되면 새 모델을 포함하도록 앙상블이 업데이트됩니다.

자동화된 Machine Learning에서 기본 앙상블 설정을 변경하는 방법은 [방법](how-to-configure-auto-train.md#ensemble) 문서를 참조하세요.

## <a name="guidance-on-local-vs-remote-managed-ml-compute-targets"></a><a name="local-remote"></a>로컬 및 원격 관리형 ML 컴퓨팅 대상에 대한 지침

자동화된 ML의 웹 인터페이스는 항상 원격 [컴퓨팅 대상](concept-compute-target.md)을 사용합니다.  그러나 Python SDK를 사용하는 경우 자동화된 ML 학습을 위해 로컬 컴퓨팅 또는 원격 컴퓨팅 대상을 선택합니다.

* **로컬 컴퓨팅**: 학습이 로컬 랩톱 또는 VM 컴퓨팅에서 수행됩니다. 
* **원격 컴퓨팅**: 학습이 Machine Learning 컴퓨팅 클러스터에서 수행됩니다.  

### <a name="choose-compute-target"></a>컴퓨팅 대상 선택
컴퓨팅 대상을 선택하는 경우 고려해야 하는 요소는 다음과 같습니다.

 * **로컬 컴퓨팅 선택**: 작은 데이터와 짧은 학습을 사용하는 초기 검색 또는 데모(예: 자식 실행당 몇 초 또는 몇 분)에 대한 시나리오인 경우 로컬 컴퓨터에 대한 학습이 더 적합할 수 있습니다.  설치 시간이 없으며, 인프라 리소스(PC 또는 VM)를 직접 사용할 수 있습니다.
 * **원격 ML 컴퓨팅 클러스터 선택**: 더 긴 학습이 필요한 모델을 생성하는 프로덕션 교육처럼 대규모 데이터세트를 사용하여 교육하는 경우, `AutoML`이 클러스터 노드 간에 학습을 병렬화하므로 원격 컴퓨팅은 엔드투엔드 시간 성능을 훨씬 더 향상할 수 있습니다. 원격 컴퓨팅에서 내부 인프라의 시작 시간은 자식 실행당 약 1.5분, VM이 아직 가동되지 않은 경우 클러스터 인프라의 시간이 추가됩니다.

### <a name="pros-and-cons"></a>장단점
로컬 및 원격을 사용하도록 선택하는 경우 다음과 같은 장단점을 고려합니다.

|  | 장점  |단점  |
|---------|---------|---------|
|**로컬 컴퓨팅 대상** |  <li> 환경 시작 시간 없음   | <li>  기능의 하위 세트<li>  실행을 병렬화할 수 없음 <li> 큰 데이터의 경우 더 심각함 <li>학습 중 데이터 스트리밍 없음 <li>  DNN 기반 기능화 없음 <li> Python SDK만 해당 |
|**원격 ML 컴퓨팅 클러스터**|  <li> 전체 기능 세트 <li> 자식 실행 병렬화 <li>   큰 데이터 지원<li>  DNN 기반 기능화 <li>  주문형 컴퓨팅 클러스터의 동적 확장성 <li> 코드리스 환경(웹 UI)도 사용할 수 있음  |  <li> 클러스터 노드의 시작 시간 <li> 각 자식 실행의 시작 시간    |

### <a name="feature-availability"></a>기능 가용성 

아래 표와 같이 원격 컴퓨팅을 사용하는 경우 더 많은 기능을 사용할 수 있습니다. 

| 기능                                                    | 원격 | 로컬 | 
|------------------------------------------------------------|--------|-------|
| 데이터 스트리밍(큰 데이터 지원, 최대 100GB)          | ✓      |       | 
| DNN-BERT 기반 텍스트 기능화 및 학습             | ✓      |       |
| 기본 제공 GPU 지원(학습 및 추론)        | ✓      |       |
| 이미지 분류 (미리 보기) 및 레이블 지정 지원        | ✓      |       |
| 예측을 위한 Auto-ARIMA, Prophet 및 ForecastTCN 모델 | ✓      |       | 
| 동시에 여러 실행/반복                       | ✓      |       |
| AutoML 스튜디오 웹 환경 UI에서 해석력 있는 모델 만들기      | ✓      |       |
| 스튜디오 웹 환경 UI에서 기능 엔지니어링 사용자 지정| ✓      |       |
| Azure ML 하이퍼 매개 변수 튜닝                             | ✓      |       |
| Azure ML 파이프라인 워크플로 지원                         | ✓      |       |
| 실행 계속                                             | ✓      |       |
| 예측                                                | ✓      | ✓     |
| 컴퓨터 비전 (미리 보기)                                  | ✓      |       |
| Notebook에서 실험 만들기 및 실행                    | ✓      | ✓     |
| UI에서 실험 정보와 메트릭 등록 및 시각화 | ✓      | ✓     |
| 데이터 가드 레일                                            | ✓      | ✓     |


<a name="use-with-onnx"></a>

## <a name="automl--onnx"></a>AutoML 및 ONNX

Azure Machine Learning을 사용하면 자동화된 ML을 사용하여 Python 모델을 빌드하고 ONNX 형식으로 변환할 수 있습니다. 모델이 ONNX 형식이면 다양한 플랫폼과 디바이스에서 실행할 수 있습니다. [ONNX를 사용하여 ML 모델을 가속화하는 방법](concept-onnx.md)에 대해 자세히 알아보세요.

[이 Jupyter Notebook 예제](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb)에서 ONNX 형식으로 변환하는 방법을 참조하세요. [ONNX에서 지원되는 알고리즘](how-to-configure-auto-train.md#supported-models)에 대해 알아보세요.

또한 ONNX 런타임은 C#을 지원하므로 REST 엔드포인트에서 도입하는 네트워크 대기 시간 또는 다시 코딩 없이도 C# 앱에서 자동으로 빌드되는 모델을 사용할 수 있습니다. [ML.NET을 사용하여 .NET 애플리케이션에서 AutoML ONNX 모델 사용](./how-to-use-automl-onnx-model-dotnet.md) 및 [ONNX 런타임 C# API를 사용하여 ONNX 모델 추론](https://onnxruntime.ai/docs/api/csharp-api.html)에 대해 자세히 알아봅니다. 

## <a name="next-steps"></a>다음 단계

AutoML로 시작하고 실행할 수 있는 여러 가지 리소스가 있습니다. 

### <a name="tutorials-how-tos"></a>자습서/방법
자습서는 AutoML 시나리오의 엔드투엔드 소개 예제입니다.
+ **코드를 처음 사용하는 경우** [가이드: AutoML 및 Python으로 회귀 모델 학습](tutorial-auto-train-models.md)을 따르세요.

+ **코드가 적거나 없는 환경** 은 [가이드: Azure Machine Learning 스튜디오에서 코드 없는 AutoML을 사용하여 분류 모델 학습](tutorial-first-experiment-automated-ml.md)을 참조하세요.

+ **AutoML을 사용 하 여 컴퓨터 비전 모델을 학습 하려면** [Automl 및 Python을 사용 하 여 개체 검색 모델 학습 (미리 보기) 자습서](tutorial-auto-train-image-models.md)를 참조 하세요.
   
방법 문서는 자동화된 ML이 제공하는 기능에 대한 추가 세부 정보를 제공합니다. 예제: 

+ 자동 학습 실험 설정 구성
    + [Azure Machine Learning Studio에서 코드 없이](how-to-use-automated-ml-for-ml-models.md). 
    + [Python SDK 사용](how-to-configure-auto-train.md).

+  [시계열 데이터로 예측 모델을 훈련](how-to-auto-train-forecast.md)하는 방법에 대해 알아보세요.

+  [Python을 사용 하 여 컴퓨터 비전 모델을 학습](how-to-auto-train-image-models.md)하는 방법을 알아봅니다.
   
### <a name="jupyter-notebook-samples"></a>Jupyter Notebook 샘플 

[자동화된 기계 학습 샘플에 대한 GitHub Notebook 리포지토리](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/)에서 자세한 코드 예제 및 사용 사례를 검토합니다.

### <a name="python-sdk-reference"></a>Python SDK 참조

[AutoML 클래스 참조 설명서](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig)를 사용하여 SDK 디자인 패턴 및 클래스 사양의 전문 지식을 활용합니다. 

> [!Note]
> 자동화된 Machine Learning은 다른 Microsoft 솔루션(예: [ML.NET](/dotnet/machine-learning/automl-overview), [HDInsight](../hdinsight/spark/apache-spark-run-machine-learning-automl.md), [Power BI](/power-bi/service-machine-learning-automated) 및 [SQL Server](https://cloudblogs.microsoft.com/sqlserver/2019/01/09/how-to-automate-machine-learning-on-sql-server-2019-big-data-clusters/))에서도 사용할 수 있습니다.