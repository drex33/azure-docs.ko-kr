---
title: Python을 사용하여 AutoML 설정
titleSuffix: Azure Machine Learning
description: Azure Machine Learning 자동화된 ML을 사용하여 Azure Machine Learning Python SDK로 AutoML 학습 실행을 설정하는 방법을 알아봅니다.
author: cartacioS
ms.author: sacartac
ms.reviewer: nibaccam
services: machine-learning
ms.service: machine-learning
ms.subservice: automl
ms.date: 09/27/2021
ms.topic: how-to
ms.custom: devx-track-python,contperf-fy21q1, automl, contperf-fy21q4, FY21Q4-aml-seo-hack, contperf-fy22q1
ms.openlocfilehash: 159452c18418508cb0640e909a49474e77ca3ce9
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130246419"
---
# <a name="set-up-automl-training-with-python"></a>Python을 사용하여 AutoML 학습 설정

이 가이드에서는 Azure Machine Learning 자동화된 ML 사용하여 [Azure Machine Learning Python SDK를](/python/api/overview/azure/ml/intro) 사용하여 자동화된 Machine Learning, AutoML, 학습 실행을 설정하는 방법을 알아봅니다. 자동화된 ML은 알고리즘과 하이퍼 매개 변수를 자동으로 선택하고 배포할 준비가 된 모델을 생성합니다. 이 가이드에서는 자동화된 ML 실험을 구성하는 데 사용할 수 있는 다양한 옵션에 대해 자세히 설명합니다.

엔드투엔드 예제는 [자습서: AutoML - 회귀 모델 학습](tutorial-auto-train-models.md)을 참조하세요.

코드 없는 환경을 선호하는 경우 [Azure Machine Learning 스튜디오에서 코드 없는 AutoML 학습을 설정](how-to-use-automated-ml-for-ml-models.md)할 수도 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

이 문서의 내용을 진행하려면 다음 항목이 필요합니다. 
* Azure Machine Learning 작업 영역 작업 영역을 만들려면 [Azure Machine Learning 작업 영역 만들기](how-to-manage-workspace.md)를 참조하세요.

* 설치된 Azure Machine Learning Python SDK.
    SDK를 설치하려면 다음 중 하나를 수행할 수 있습니다. 
    * SDK를 자동으로 설치하고 ML 워크플로에 대해 미리 구성된 컴퓨팅 인스턴스를 만듭니다. 자세한 내용은 [Azure Machine Learning 컴퓨팅 인스턴스 만들기 및 관리](how-to-create-manage-compute-instance.md)를 참조하세요. 

    * [`automl` 패키지를 직접 설치](https://github.com/Azure/azureml-examples/blob/main/python-sdk/tutorials/automl-with-azureml/README.md#setup-using-a-local-conda-environment)합니다. 여기에는 SDK의 [기본 설치](/python/api/overview/azure/ml/install#default-install)가 포함됩니다.

    [!INCLUDE [automl-sdk-version](../../includes/machine-learning-automl-sdk-version.md)]
    
    > [!WARNING]
    > Python 3.8은 `automl`과 호환되지 않습니다. 

## <a name="select-your-experiment-type"></a>실험 유형 선택

실험을 시작하기 전에 해결하려는 기계 학습 문제의 종류를 결정해야 합니다. 자동화된 Machine Learning은 `classification`, `regression` 및 `forecasting`의 작업 유형을 지원합니다. [작업 유형](concept-automated-ml.md#when-to-use-automl-classification-regression-forecasting--computer-vision)에 대해 자세히 알아보세요.

다음 코드는 `AutoMLConfig` 생성자에서 `task` 매개 변수를 사용하여 실험 형식을 `classification`으로 지정합니다.

```python
from azureml.train.automl import AutoMLConfig

# task can be one of classification, regression, forecasting
automl_config = AutoMLConfig(task = "classification")
```

## <a name="data-source-and-format"></a>데이터 원본 및 형식

자동화된 Machine Learning은 로컬 데스크톱 또는 Azure Blob Storage와 같은 클라우드의 데이터를 지원합니다. 데이터를 **Pandas 데이터 프레임** 또는 **Azure Machine Learning TabularDataset** 로 읽어 들일 수 있습니다. [데이터 세트](how-to-create-register-datasets.md)에 대해 자세히 알아보세요.

기계 학습에서 데이터 학습을 위한 요구 사항:
- 데이터는 테이블 형식이어야 합니다.
- 예측하려는 값(대상 열)이 데이터에 있어야 합니다.

**원격 실험의 경우** 원격 컴퓨팅에서 학습 데이터에 액세스할 수 있어야 합니다. 자동화된 ML은 원격 컴퓨팅에서 작업하는 경우에만 [Azure Machine Learning TabularDatasets](/python/api/azureml-core/azureml.data.tabulardataset)를 허용합니다. 

Azure Machine Learning 데이터 세트는 다음과 같은 기능을 보여줍니다.

* 정적 파일 또는 URL 원본에서 작업 영역으로 데이터를 손쉽게 전송합니다.
* 클라우드 컴퓨팅 리소스에서 실행할 때 데이터를 학습 스크립트에 사용 가능 `Dataset` 클래스를 사용하여 원격 컴퓨팅 대상에 데이터를 탑재하는 예제는 [데이터 세트를 사용하여 학습하는 방법](how-to-train-with-datasets.md#mount-files-to-remote-compute-targets)을 참조하세요.

다음 코드는 웹 URL에서 TabularDataset을 만듭니다. 로컬 파일 및 데이터 저장소와 같은 다른 원본에서 데이터 세트를 만드는 방법에 대한 코드 예제는 [TabularDataset 만들기](how-to-create-register-datasets.md#create-a-tabulardataset)를 참조하세요.

```python
from azureml.core.dataset import Dataset
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"
dataset = Dataset.Tabular.from_delimited_files(data)
```

**로컬 컴퓨팅 실험의 경우** 처리 시간을 단축하기 위해 pandas 데이터 프레임을 권장합니다.

  ```python
  import pandas as pd
  from sklearn.model_selection import train_test_split

  df = pd.read_csv("your-local-file.csv")
  train_data, test_data = train_test_split(df, test_size=0.1, random_state=42)
  label = "label-col-name"
  ```

## <a name="training-validation-and-test-data"></a>학습, 유효성 검사 및 테스트 데이터

별도의 **학습 데이터 및 유효성 검사 데이터 세트** 를 `AutoMLConfig` 생성자에서 직접 지정할 수 있습니다. AutoML 실험에서 [데이터 분할 및 교차 유효성 검사를 구성하는 방법](how-to-configure-cross-validation-data-splits.md)에 대해 자세히 알아보세요. 

`validation_data` 또는 `n_cross_validation` 매개 변수를 명시적으로 지정하지 않는 경우 자동화된 ML은 유효성 검사가 수행되는 방식을 결정하는 기본 기술을 적용합니다. 이러한 결정은 `training_data` 매개 변수에 할당된 데이터 세트의 행 수에 따라 달라집니다. 

|학습&nbsp;데이터&nbsp;크기| 유효성 검사 기법 |
|---|-----|
|**20,000개&nbsp;행보다&nbsp;더&nbsp;큼**| 학습/유효성 검사 데이터 분할이 적용됩니다. 기본값은 초기 학습 데이터 세트의 10%를 유효성 검사 세트로 사용하는 것입니다. 그러면 해당 유효성 검사 집합이 메트릭 계산에 사용됩니다.
|**20,000개&nbsp;행보다&nbsp;더&nbsp;작음**| 교차 유효성 검사 방법이 적용됩니다. 기본 접기 횟수는 행의 수에 따라 달라집니다. <br> **데이터 세트의 행이 1,000개 이하인 경우** 10겹 접기가 사용됩니다. <br> **행이 1,000개~20,000개 사이인 경우** 3겹 접기가 사용됩니다.

지금은 모델 평가를 위한 사용자 고유의 **테스트 데이터** 를 제공해야 합니다. 모델 평가를 위한 고유한 테스트 데이터를 가져오는 코드 예제는 [이 Jupyter Notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-credit-card-fraud/auto-ml-classification-credit-card-fraud.ipynb)의 **테스트** 섹션을 참조하세요.

### <a name="large-data"></a>큰 데이터 

자동화된 ML은 작은 가상 머신에서 빅 데이터에 대한 모델을 성공적으로 빌드할 수 있는 큰 데이터 학습에 대해 제한된 수의 알고리즘을 지원합니다. 자동화된 ML 추론은 데이터 크기, 가상 머신 메모리 크기, 실험 시간 제한, 기능화 설정과 같은 속성에 따라 이러한 큰 데이터 알고리즘을 적용해야 하는지 여부를 결정합니다. [자동화된 ML에서 지원되는 모델에 대해 자세히 알아보세요](#supported-models). 

* 회귀의 경우 [온라인 그라데이션 하강 회귀 변수](/python/api/nimbusml/nimbusml.linear_model.onlinegradientdescentregressor?preserve-view=true&view=nimbusml-py-latest) 및 [고속 선형 회귀 변수](/python/api/nimbusml/nimbusml.linear_model.fastlinearregressor?preserve-view=true&view=nimbusml-py-latest)가 지원됩니다.

* 분류의 경우 [평균 퍼셉트론 분류자](/python/api/nimbusml/nimbusml.linear_model.averagedperceptronbinaryclassifier?preserve-view=true&view=nimbusml-py-latest) 및 [선형 SVM 분류자](/python/api/nimbusml/nimbusml.linear_model.linearsvmbinaryclassifier?preserve-view=true&view=nimbusml-py-latest)가 지원됩니다. 여기서 선형 SVM 분류자에는 큰 데이터 및 작은 데이터 버전이 있습니다.

이러한 추론을 재정의하려면 다음 설정을 적용하세요. 

작업 | 설정 | 참고
|---|---|---
데이터&nbsp;스트리밍 알고리즘 차단 | `AutoMLConfig` 개체의 `blocked_models`이며 사용하지 않으려는 모델을 나열합니다. | 실행이 실패하거나 실행 시간이 길어집니다.
데이터&nbsp;스트리밍&nbsp;알고리즘&nbsp;사용| `AutoMLConfig` 개체의 `allowed_models`이며 사용하려는 모델을 나열합니다.| 
데이터&nbsp;스트리밍&nbsp;알고리즘&nbsp;사용 <br> [(Studio UI 실험)](how-to-use-automated-ml-for-ml-models.md#create-and-run-experiment)|사용하려는 빅 데이터 알고리즘을 제외한 모든 모델을 차단합니다. |

## <a name="compute-to-run-experiment"></a>실험 실행 컴퓨팅

다음으로, 모델을 학습할 위치를 결정합니다. 자동화된 ML 학습 실험은 다음과 같은 컴퓨팅 옵션에서 실행할 수 있습니다. [로컬 및 원격 컴퓨팅의 장단점](concept-automated-ml.md#local-remote) 옵션에 대해 알아봅니다. 

* 데스크톱이나 랩톱 같은 사용자의 **로컬** 머신 - 일반적으로 데이터 세트가 작은 경우 및 아직 탐색 단계에 있는 경우입니다. 로컬 계산 예는 [이 Notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/local-run-classification-credit-card-fraud/auto-ml-classification-credit-card-fraud-local.ipynb) 을 참조하세요. 
 
* 클라우드의 **원격 머신** - [Azure Machine Learning Managed Compute](concept-compute-target.md#amlcompute)는 Azure Virtual Machines 클러스터에서 기계 학습 모델을 학습할 수 있도록 하는 관리형 서비스입니다. 컴퓨팅 인스턴스는 컴퓨팅 대상으로도 지원됩니다.

    Azure Machine Learning Managed Compute를 사용하는 원격 예는 [이 Notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb)을 참조하세요. 

* Azure 구독의 **Azure Databricks 클러스터**. 자세한 내용은 [자동화된 ML에 대한 Azure Databricks 클러스터 설정](how-to-configure-databricks-automl-environment.md)에서 확인할 수 있습니다. Azure Databricks를 사용하는 Notebook의 예제는 [이 GitHub 사이트](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/azure-databricks/automl)를 참조하세요.

<a name='configure-experiment'></a>

## <a name="configure-your-experiment-settings"></a>실험 설정 구성

자동화된 ML 실험을 구성하는 데 사용할 수 있는 몇 가지 옵션이 있습니다. 이러한 매개 변수는 `AutoMLConfig` 개체를 인스턴스화하여 설정됩니다. 매개 변수의 전체 목록은 [AutoMLConfig 클래스](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig)를 참조하세요.

다음 예제는 분류 작업에 대한 것입니다. 이 실험에서는 [기본 메트릭으로](#primary-metric) AUC 가중치를 사용하며 실험 시간 초과가 30분 및 교차 유효성 검사 접기 2개로 설정됩니다.

```python
    automl_classifier=AutoMLConfig(task='classification',
                                   primary_metric='AUC_weighted',
                                   experiment_timeout_minutes=30,
                                   blocked_models=['XGBoostClassifier'],
                                   training_data=train_data,
                                   label_column_name=label,
                                   n_cross_validations=2)
```
추가 설정이 필요한 예측 작업을 구성할 수도 있습니다. 자세한 내용은 [Time-Series 예측에 AutoML 설정](how-to-auto-train-forecast.md) 문서를 참조하세요. 

```python
    time_series_settings = {
                            'time_column_name': time_column_name,
                            'time_series_id_column_names': time_series_id_column_names,
                            'forecast_horizon': n_test_periods
                           }
    
    automl_config = AutoMLConfig(
                                 task = 'forecasting',
                                 debug_log='automl_oj_sales_errors.log',
                                 primary_metric='normalized_root_mean_squared_error',
                                 experiment_timeout_minutes=20,
                                 training_data=train_data,
                                 label_column_name=label,
                                 n_cross_validations=5,
                                 path=project_folder,
                                 verbosity=logging.INFO,
                                 **time_series_settings
                                )
```
    
### <a name="supported-models"></a>지원되는 모델

자동화된 Machine Learning은 자동화 및 튜닝 프로세스 동안 다양한 모델 및 알고리즘을 지원합니다. 사용자는 알고리즘을 지정할 필요가 없습니다. 

적용할 알고리즘 또는 모델의 목록을 결정하는 세 가지 서로 다른 `task` 매개 변수 값이 있습니다. `allowed_models` 또는 `blocked_models` 매개 변수를 사용하여 가용 모델을 포함 또는 제외하도록 반복을 추가로 수정합니다. 

다음 표에는 작업 유형별로 지원되는 모델이 요약되어 나와 있습니다. 

> [!NOTE]
> 자동화된 ML에서 만든 모델을 [ONNX 모델](concept-onnx.md)로 내보내려는 경우 별표(*)가 표시된 알고리즘만 ONNX 형식으로 변환할 수 있습니다. [모델을 ONNX로 변환](concept-automated-ml.md#use-with-onnx)하는 방법에 대해 자세히 알아보세요. <br> <br> ONNX는 현재 분류 및 회귀 작업만 지원한다는 사실도 기억하세요. 

분류 | 회귀 | 시계열 예측
|-- |-- |--
[로지스틱 회귀](https://scikit-learn.org/stable/modules/linear_model.html#logistic-regression)* | [Elastic Net](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)* | [AutoARIMA](https://www.alkaline-ml.com/pmdarima/modules/generated/pmdarima.arima.auto_arima.html#pmdarima.arima.auto_arima)
[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)* | [Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)* | [Prophet](https://facebook.github.io/prophet/docs/quick_start.html)
[경사 부스팅](https://scikit-learn.org/stable/modules/ensemble.html#classification)* | [경사 부스팅](https://scikit-learn.org/stable/modules/ensemble.html#regression)* | [Elastic Net](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)
[의사 결정 트리](https://scikit-learn.org/stable/modules/tree.html#decision-trees)* |[의사 결정 트리](https://scikit-learn.org/stable/modules/tree.html#regression)* |[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)
[K Nearest Neighbors](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)* |[K Nearest Neighbors](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)* | [경사 부스팅](https://scikit-learn.org/stable/modules/ensemble.html#regression)
[선형 SVC](https://scikit-learn.org/stable/modules/svm.html#classification)* |[LARS Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)* | [Decision Tree](https://scikit-learn.org/stable/modules/tree.html#regression)
[SVC(Support Vector Classification)](https://scikit-learn.org/stable/modules/svm.html#classification)* |[SGD(Stochastic Gradient Descent)](https://scikit-learn.org/stable/modules/sgd.html#regression)* | Arimax
[임의 포리스트](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)* | [Random Forest](https://scikit-learn.org/stable/modules/ensemble.html#random-forests) | [LARS Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)
[Extremely Randomized Trees](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)* | [Extremely Randomized Trees](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)* | [SGD(Stochastic Gradient Descent)](https://scikit-learn.org/stable/modules/sgd.html#regression)
[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)* |[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)* | [Random Forest](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)
[평균 퍼셉트론 분류자](/python/api/nimbusml/nimbusml.linear_model.averagedperceptronbinaryclassifier?preserve-view=true&view=nimbusml-py-latest)| [온라인 그라데이션 하강 회귀 변수](/python/api/nimbusml/nimbusml.linear_model.onlinegradientdescentregressor?preserve-view=true&view=nimbusml-py-latest) | [Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)
[Naive Bayes](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)* |[고속 선형 회귀 변수](/python/api/nimbusml/nimbusml.linear_model.fastlinearregressor?preserve-view=true&view=nimbusml-py-latest)| ForecastTCN
[SGD(Stochastic Gradient Descent)](https://scikit-learn.org/stable/modules/sgd.html#sgd)* || Naive
[선형 SVM 분류자](/python/api/nimbusml/nimbusml.linear_model.linearsvmbinaryclassifier?preserve-view=true&view=nimbusml-py-latest)* || SeasonalNaive
||| 평균
||| SeasonalAverage
||| [ExponentialSmoothing](https://www.statsmodels.org/v0.10.2/generated/statsmodels.tsa.holtwinters.ExponentialSmoothing.html)

### <a name="primary-metric"></a>기본 메트릭

`primary_metric` 매개 변수는 최적화를 위해 모델을 학습시키는 동안 사용할 메트릭을 결정합니다. 선택할 수 있는 메트릭은 선택한 작업 유형에 따라 결정 됩니다.

자동화된 ML을 통해 최적화할 기본 메트릭 선택은 다양한 요인에 따라 달라집니다. 비즈니스 요구를 가장 잘 표현 하는 메트릭을 선택 하는 것이 중요 한 고려 사항입니다. 그런 다음, 메트릭이 데이터 세트 프로필(데이터 크기, 범위, 클래스 분포 등)에 적합한지를 고려합니다. 다음 섹션에서는 작업 유형 및 비즈니스 시나리오에 따라 권장 되는 기본 메트릭을 요약 합니다. 

[자동화된 Machine Learning 결과 이해](how-to-understand-automated-ml.md)에서 이러한 메트릭의 정의에 대해 알아보세요.

#### <a name="metrics-for-classification-scenarios"></a>분류 시나리오에 대한 메트릭 

Thresholded 메트릭 (예:, `accuracy` , `average_precision_score_weighted` `norm_macro_recall` 및)은 `precision_score_weighted` 작은 데이터 집합에 대해 최적화 되지 않을 수도 있습니다. 즉, 매우 큰 클래스 기울이기 (클래스 불균형)가 있거나 예상 메트릭 값이 0.0 또는 1.0에 매우 근접 한 경우입니다. 이러한 경우 기본 메트릭에 대해 `AUC_weighted`를 선택하는 것이 더 적합할 수 있습니다. 자동화된 ML이 완료되면 비즈니스 요구에 가장 적합한 메트릭을 기준으로 최적 모델을 선택할 수 있습니다.

| 메트릭 | 사용 사례 예제 |
| ------ | ------- |
| `accuracy` | 이미지 분류, 정서 분석, 변동 예측 |
| `AUC_weighted` | 부정 행위 탐지, 이미지 분류, 변칙 탐지/스팸 탐지 |
| `average_precision_score_weighted` | 정서 분석 |
| `norm_macro_recall` | 변동 예측 |
| `precision_score_weighted` |  |

#### <a name="metrics-for-regression-scenarios"></a>회귀 시나리오에 대한 메트릭
 
`r2_score` 및 `spearman_correlation`과 같은 메트릭은 예측할 값의 스케일이 많은 크기의 정도를 포함하는 경우 모델의 품질을 더 잘 나타낼 수 있습니다. 예를 들어 많은 사람의 급여가 2만 달러에서 10만 달러인 급여 추정의 경우 1억 달러 범위에 있는 일부 급여로 인해 스케일이 매우 높아집니다. 

이 경우 `normalized_mean_absolute_error` 및 `normalized_root_mean_squared_error`는 급여가 3만 달러인 작업자에 대해 2천만 달러 버는 작업자와 같이 2만 달러 예측 오차를 처리합니다. 실제로는 3만 달러에서 2만 달러를 예측하는 것은 근접하지 않은 반면(상대 오차가 67%로 큼), 2천만 달러 급여에서 2만 달러를 예측하는 것은 매우 근접합니다(상대 오차가 0.1%로 적음). `normalized_mean_absolute_error` 및 `normalized_root_mean_squared_error`는 예측할 값이 비슷한 범위에 있는 경우에 유용 합니다.

| 메트릭 | 사용 사례 예제 |
| ------ | ------- |
| `spearman_correlation` | |
| `normalized_root_mean_squared_error` | 가격 예측(집/제품/팁), 점수 예측 검토 |
| `r2_score` | 항공 지연, 급여 추정, 버그 해결 시간 |
| `normalized_mean_absolute_error` |  |

#### <a name="metrics-for-time-series-forecasting-scenarios"></a>시계열 예측 시나리오에 대한 메트릭

권장 사항은 회귀 시나리오에서 언급된 사항과 유사합니다. 

| 메트릭 | 사용 사례 예제 |
| ------ | ------- |
| `normalized_root_mean_squared_error` | 가격 예측(예측), 재고 최적화, 수요 예측 | 
| `r2_score` | 가격 예측(예측), 재고 최적화, 수요 예측 |
| `normalized_mean_absolute_error` | |

### <a name="data-featurization"></a>데이터 기능화

모든 자동화된 ML 실험에서는 규모가 서로 다른 기능에 중요한 ‘특정’ 알고리즘을 지원할 수 있도록 데이터가 자동으로 스케일링 및 정규화됩니다. 이러한 스케일링 및 정규화를 기능화라고 합니다. 자세한 내용 및 코드 예제는 [AutoML의 기능화](how-to-configure-auto-features.md#)를 참조하세요. 

`AutoMLConfig` 개체에서 실험을 구성할 때 `featurization` 설정을 사용하거나 사용하지 않도록 설정할 수 있습니다. 다음 표는 [AutoMLConfig 개체](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig)에서 기능화에 허용되는 설정을 보여 줍니다. 

|기능화 구성 | Description |
| ------------- | ------------- |
|`"featurization": 'auto'`| 전처리의 일부로 [데이터 가드 레일 및 기능화 단계](how-to-configure-auto-features.md#featurization)가 자동으로 수행된다는 것을 나타냅니다. **기본 설정**.|
|`"featurization": 'off'`| 기능화 단계를 자동으로 수행하면 안 된다는 것을 나타냅니다.|
|`"featurization":`&nbsp;`'FeaturizationConfig'`| 사용자 지정된 기능화 단계를 사용해야 한다는 것을 나타냅니다. [기능화를 사용자 지정하는 방법을 알아보세요](how-to-configure-auto-features.md#customize-featurization).|

> [!NOTE]
> 자동화된 Machine Learning 기능화 단계(기능 정규화, 누락된 데이터 처리, 텍스트를 숫자로 변환 등)는 기본 모델의 일부가 됩니다. 예측에 모델을 사용하는 경우 학습 중에 적용되는 동일한 기능화 단계가 입력 데이터에 자동으로 적용됩니다.

<a name="ensemble"></a>

### <a name="ensemble-configuration"></a> 앙상블 구성

앙상블 모델은 기본적으로 사용하도록 설정되며, AutoML 실행에서 최종 실행 반복으로 표시됩니다. 현재 **VotingEnsemble** 및 **StackEnsemble** 이 지원됩니다. 

투표는 가중치 평균을 사용하는 소프트 투표를 구현합니다. 스택 구현은 2계층 구현을 사용하는데, 여기서 첫 번째 계층은 투표 앙상블과 동일한 모델을 사용하고, 두 번째 계층 모델은 첫 번째 계층에서 최적의 모델 조합을 찾는 데 사용됩니다. 

ONNX 모델을 사용하는 경우 **또는** 모델 설명 가능성을 사용하도록 설정한 경우 스택이 사용되지 않고 투표만 사용됩니다.

앙상블 학습은 `enable_voting_ensemble` 및 `enable_stack_ensemble` 부울 매개 변수를 사용하여 비활성화할 수 있습니다.

```python
automl_classifier = AutoMLConfig(
                                 task='classification',
                                 primary_metric='AUC_weighted',
                                 experiment_timeout_minutes=30,
                                 training_data=data_train,
                                 label_column_name=label,
                                 n_cross_validations=5,
                                 enable_voting_ensemble=False,
                                 enable_stack_ensemble=False
                                )
```

기본 앙상블 동작을 변경하기 위해 `AutoMLConfig` 개체에 `kwargs`로 제공할 수 있는 여러 기본 인수가 있습니다.

> [!IMPORTANT]
>  다음 매개 변수는 AutoMLConfig 클래스의 명시적 매개 변수가 아닙니다. 

* `ensemble_download_models_timeout_sec`: **VotingEnsemble** 및 **StackEnsemble** 모델이 생성되는 동안 이전 자식 실행에서 여러 맞춤 모델이 다운로드됩니다. `AutoMLEnsembleException: Could not find any models for running ensembling` 오류가 발생할 경우 모델 다운로드 시간을 더 길게 해야 할 수도 있습니다. 모델을 병렬로 다운로드하는 기본값은 300초이고 최대 시간 제한은 없습니다. 시간이 더 필요한 경우 이 매개 변수를 300초보다 큰 값으로 구성합니다. 

  > [!NOTE]
  >  시간 제한에 도달했을 때 다운로드된 모델이 있으면 그때까지 다운로드된 모델을 사용하여 앙상블이 진행됩니다. 시간 제한 내에 완료하려면 반드시 모든 모델을 다운로드해야 하는 것은 아닙니다.

다음 매개 변수는 **StackEnsemble** 모델에만 적용됩니다. 

* `stack_meta_learner_type`: 메타 학습자은 개별 이기종 모델의 출력에 대해 학습된 모델입니다. 기본 메타 학습자는 분류 작업인 경우 `LogisticRegression`(또는 교차 유효성 검사를 사용하는 경우 `LogisticRegressionCV`)이고, 회귀/예측 작업인 경우 `ElasticNet`(교차 유효성 검사를 사용하는 경우 `ElasticNetCV`)입니다. 이 매개 변수는 문자열 `LogisticRegression`, `LogisticRegressionCV`, `LightGBMClassifier`, `ElasticNet`, `ElasticNetCV`, `LightGBMRegressor` 또는 `LinearRegression` 중 하나입니다.

* `stack_meta_learner_train_percentage`: (학습의 학습 및 유효성 검사 유형을 선택할 때) 메타 학습자의 학습을 위해 예약할 학습 세트의 비율을 지정합니다. 기본값은 `0.2`입니다. 

* `stack_meta_learner_kwargs`: 메타 학습자의 이니셜라이저에 전달할 선택적 매개 변수입니다. 이러한 매개 변수 및 매개 변수 형식은 해당 모델 생성자의 매개 변수 및 매개 변수 형식을 미러링하며, 모델 생성자에 전달됩니다.

다음 코드는 `AutoMLConfig` 개체에서 사용자 지정 앙상블 동작을 지정하는 예를 보여줍니다.

```python
ensemble_settings = {
                     "ensemble_download_models_timeout_sec": 600
                     "stack_meta_learner_type": "LogisticRegressionCV",
                     "stack_meta_learner_train_percentage": 0.3,
                     "stack_meta_learner_kwargs": {
                                                    "refit": True,
                                                    "fit_intercept": False,
                                                    "class_weight": "balanced",
                                                    "multi_class": "auto",
                                                    "n_jobs": -1
                                                  }
                    }

automl_classifier = AutoMLConfig(
                                 task='classification',
                                 primary_metric='AUC_weighted',
                                 experiment_timeout_minutes=30,
                                 training_data=train_data,
                                 label_column_name=label,
                                 n_cross_validations=5,
                                 **ensemble_settings
                                )
```

<a name="exit"></a> 

### <a name="exit-criteria"></a>종료 기준

실험을 종료하도록 AutoMLConfig에서 정의할 수 있는 몇 가지 옵션이 있습니다.

|조건| description
|----|----
조건&nbsp;없음 | 종료 매개 변수를 정의하지 않으면 기본 메트릭에서 더 이상 진행되지 않을 때까지 실험이 계속됩니다.
&nbsp;시간이&nbsp;지난&nbsp;후&nbsp;| 설정에서 `experiment_timeout_minutes`를 사용하여 실험을 계속 실행해야 하는 시간(분)을 정의합니다. <br><br> 실험 시간 초과 실패를 방지하기 위해 최소 15분, 또는 행 * 열 크기가 1000만을 초과할 경우 60분으로 설정됩니다.
&nbsp;점수에&nbsp;도달함&nbsp;&nbsp;| `experiment_exit_score`를 사용하면 지정한 기본 메트릭 점수에 도달한 후 실험이 완료됩니다.

## <a name="run-experiment"></a>실험 실행

> [!WARNING]
> 동일한 구성 설정과 기본 메트릭을 사용하여 여러 번 실험을 실행한 경우 각 실험의 최종 메트릭 점수 및 생성된 모델에 변동이 있을 가능성이 높습니다. 자동화된 ML이 채택하는 알고리즘에는 내재된 무작위성이 있어서 실험에 의한 모델 출력 및 권장된 모델의 최종 메트릭 점수(예: 정확도)에 약간의 변동을 일으킬 수 있습니다. 모델 이름이 동일한 결과가 표시될 수도 있지만 다른 하이퍼 매개 변수가 사용되었습니다. 

자동화된 ML의 경우 실험을 실행하는 데 사용되는 `Workspace`의 명명된 개체인 `Experiment` 개체를 만들 수 있습니다.

```python
from azureml.core.experiment import Experiment

ws = Workspace.from_config()

# Choose a name for the experiment and specify the project folder.
experiment_name = 'Tutorial-automl'
project_folder = './sample_projects/automl-classification'

experiment = Experiment(ws, experiment_name)
```

실행하려는 실험을 제출하고 모델을 생성합니다. `AutoMLConfig`를 `submit` 메서드에 전달하여 모델을 생성합니다.

```python
run = experiment.submit(automl_config, show_output=True)
```

>[!NOTE]
>새 머신에 먼저 종속성이 설치됩니다.  출력이 표시되는 데 최대 10분이 걸릴 수 있습니다.
>`show_output`을 `True`로 설정하면 출력이 콘솔에 표시됩니다.

### <a name="multiple-child-runs-on-clusters"></a>클러스터에서 여러 자식 실행

자동화된 ML 실험 자식 실행은 이미 다른 실험을 실행하고 있는 클러스터에서 수행할 수 있습니다. 그러나 타이밍은 클러스터에 포함된 노드 수와 해당 노드를 사용하여 다른 실험을 실행할 수 있는지 여부에 따라 달라집니다.

클러스터의 각 노드는 단일 학습 실행을 달성할 수 있는 개별 VM(가상 머신)의 역할을 합니다. 자동화된 ML의 경우 이는 자식 실행을 의미합니다. 모든 노드가 사용 중이면 새 실험은 큐에 추가됩니다. 그러나 무료 노드가 있는 경우 새 실험은 사용 가능한 노드/VM에서 병렬로 자동화된 ML 자식 실행을 실행합니다.

자식 실행 및 수행 가능한 시기를 관리하는 데 도움이 되도록 실험당 전용 클러스터를 만들고 실험의 `max_concurrent_iterations` 수를 클러스터의 노드 수와 일치시키는 것이 좋습니다. 이러한 방식으로 클러스터의 모든 노드를 동시에 사용하여 원하는 동시 자식 실행/반복 횟수를 지정할 수 있습니다.

`AutoMLConfig` 개체에서 `max_concurrent_iterations`를 구성합니다. 구성되지 않은 경우에는 기본적으로 실험당 하나의 동시 자식 실행/반복만 허용됩니다.
컴퓨팅 인스턴스의 경우 을 `max_concurrent_iterations` 컴퓨팅 인스턴스 VM의 코어 수와 동일하게 설정할 수 있습니다.

## <a name="explore-models-and-metrics"></a>모델 및 메트릭 탐색

자동화된 ML은 학습 결과를 모니터링하고 평가할 수 있는 옵션을 제공합니다. 

* Notebook을 사용 중이면 위젯 또는 인라인에서 결과를 볼 수 있습니다. 자세한 내용은 [자동화된 Machine Learning 실행 모니터링](#monitor)을 참조하세요.

* 각 실행에 대해 제공되는 성능 차트 및 메트릭의 정의와 예제는 [자동화된 Machine Learning 실험 결과 평가](how-to-understand-automated-ml.md)를 참조하세요. 

* 기능화 요약을 얻고 특정 모델에 추가된 기능을 이해하려면 [기능화 투명도](how-to-configure-auto-features.md#featurization-transparency)를 참조하세요. 

다음 사용자 지정 코드 솔루션을 사용하여 특정 자동화된 ML 실행에 적용되는 하이퍼 매개 변수, 스케일링 및 정규화 기술 및 알고리즘을 볼 수 있습니다. 

다음은 자동화된 ML 학습 파이프라인의 각 단계에 대한 하이퍼 매개 변수를 출력하는 사용자 지정 메서드인 `print_model()`을 정의합니다.
 
```python
from pprint import pprint

def print_model(model, prefix=""):
    for step in model.steps:
        print(prefix + step[0])
        if hasattr(step[1], 'estimators') and hasattr(step[1], 'weights'):
            pprint({'estimators': list(e[0] for e in step[1].estimators), 'weights': step[1].weights})
            print()
            for estimator in step[1].estimators:
                print_model(estimator[1], estimator[0]+ ' - ')
        elif hasattr(step[1], '_base_learners') and hasattr(step[1], '_meta_learner'):
            print("\nMeta Learner")
            pprint(step[1]._meta_learner)
            print()
            for estimator in step[1]._base_learners:
                print_model(estimator[1], estimator[0]+ ' - ')
        else:
            pprint(step[1].get_params())
            print()   
```

동일한 실험 Notebook 내에서 제출되고 학습된 로컬 또는 원격 실행의 경우 메서드를 사용하여 최상의 모델을 전달할 수 `get_output()` 있습니다. 

```python
best_run, fitted_model = run.get_output()
print(best_run)
         
print_model(fitted_model)
```

다음 출력은 다음을 나타냅니다.
 
* StandardScalerWrapper 기술이 학습 전에 데이터를 스케일링하고 정규화하는 데 사용되었습니다.

* XGBoostClassifier 알고리즘은 최상의 실행으로 식별되었으며 하이퍼 매개 변수 값도 표시됩니다. 

```python
StandardScalerWrapper
{'class_name': 'StandardScaler',
 'copy': True,
 'module_name': 'sklearn.preprocessing.data',
 'with_mean': False,
 'with_std': False}

XGBoostClassifier
{'base_score': 0.5,
 'booster': 'gbtree',
 'colsample_bylevel': 1,
 'colsample_bynode': 1,
 'colsample_bytree': 0.6,
 'eta': 0.4,
 'gamma': 0,
 'learning_rate': 0.1,
 'max_delta_step': 0,
 'max_depth': 8,
 'max_leaves': 0,
 'min_child_weight': 1,
 'missing': nan,
 'n_estimators': 400,
 'n_jobs': 1,
 'nthread': None,
 'objective': 'multi:softprob',
 'random_state': 0,
 'reg_alpha': 0,
 'reg_lambda': 1.6666666666666667,
 'scale_pos_weight': 1,
 'seed': None,
 'silent': None,
 'subsample': 0.8,
 'tree_method': 'auto',
 'verbose': -10,
 'verbosity': 1}
```

작업 영역에서 다른 실험의 기존 실행의 경우 탐색하려는 특정 실행 ID를 가져와서 `print_model()` 메서드로 전달합니다. 

```python
from azureml.train.automl.run import AutoMLRun

ws = Workspace.from_config()
experiment = ws.experiments['automl-classification']
automl_run = AutoMLRun(experiment, run_id = 'AutoML_xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxx')

automl_run
best_run, model_from_aml = automl_run.get_output()

print_model(model_from_aml)

```

## <a name="monitor-automated-machine-learning-runs"></a><a name="monitor"></a> 자동화된 Machine Learning 실행 모니터링

자동화된 ML 실행에서 이전 실행의 차트에 액세스하려면 `<<experiment_name>>`을 적절한 실험 이름으로 바꿉니다.

```python
from azureml.widgets import RunDetails
from azureml.core.run import Run

experiment = Experiment (workspace, <<experiment_name>>)
run_id = 'autoML_my_runID' #replace with run_ID
run = Run(experiment, run_id)
RunDetails(run).show()
```

![자동화된 기계 학습을 위한 Jupyter Notebook 위젯](./media/how-to-configure-auto-train/azure-machine-learning-auto-ml-widget.png)

## <a name="register-and-deploy-models"></a>모델 등록 및 배포

모델을 등록하면 나중에 사용할 수 있도록 다시 돌아올 수 있습니다. 

자동화된 ML 실행에서 모델을 등록하려면 [`register_model()`](/python/api/azureml-train-automl-client/azureml.train.automl.run.automlrun#register-model-model-name-none--description-none--tags-none--iteration-none--metric-none-) 메서드를 사용합니다. 

```Python

best_run = run.get_best_child()
print(fitted_model.steps)

model_name = best_run.properties['model_name']
description = 'AutoML forecast example'
tags = None

model = run.register_model(model_name = model_name, 
                                  description = description, 
                                  tags = tags)
```


배포 구성을 만들고 웹 서비스에 등록된 모델을 배포하는 방법에 대한 자세한 내용은 [모델을 배포하는 방법 및 위치](how-to-deploy-and-where.md?tabs=python#define-a-deployment-configuration)를 참조하세요.

> [!TIP]
> 등록된 모델의 경우 [Azure Machine Learning 스튜디오](https://ml.azure.com)를 통해 한 번 클릭으로 배포를 사용할 수 있습니다. [스튜디오에서 등록된 모델을 배포하는 방법](how-to-use-automated-ml-for-ml-models.md#deploy-your-model)을 참조하세요. 
<a name="explain"></a>

## <a name="model-interpretability"></a>모델 해석력

모델 해석력을 통해 모델이 예측을 수행한 이유와 기본 기능 중요도 값을 이해할 수 있습니다. SDK에는 로컬 모델과 배포된 모델의 학습 및 유추 시간에 모델 해석력 기능을 사용할 수 있도록 다양한 패키지가 포함되어 있습니다.

특히 자동화된 ML 실험 내에서 [해석 기능 기능을 사용하도록 설정하는](how-to-machine-learning-interpretability-automl.md) 방법을 참조하세요.

자동화된 기계 학습 이외의 SDK의 다른 영역에서 모델 설명 및 기능 중요도를 사용하도록 설정하는 방법에 대한 일반적인 내용은 [해석력에 대한 개념 문서를 참조하세요.](how-to-machine-learning-interpretability.md)

> [!NOTE]
> ForecastTCN 모델은 현재 설명 클라이언트에서 지원되지 않습니다. 이 모델은 최상의 모델로 반환되고 주문형 설명 실행을 지원하지 않는 경우 설명 대시보드를 반환하지 않습니다.

## <a name="next-steps"></a>다음 단계

+ [모델 배포 방법 및 위치](how-to-deploy-and-where.md)에 대해 자세히 알아봅니다.

+ [자동화된 Machine Learning을 사용하여 회귀 모델을 학습시키는 방법](tutorial-auto-train-models.md)을 자세히 알아봅니다.

+ [자동화된 ML 실험 문제 해결](how-to-troubleshoot-auto-ml.md) 
