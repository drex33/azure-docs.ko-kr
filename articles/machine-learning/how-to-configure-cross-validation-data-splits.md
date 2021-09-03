---
title: 자동화된 Machine Learning의 데이터 분할 및 교차 유효성 검사
titleSuffix: Azure Machine Learning
description: 자동화된 Machine Learning 실험의 데이터 세트 분할 및 교차 유효성 검사를 구성하는 방법 알아보기
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.custom: automl
ms.author: cesardl
author: CESARDELATORRE
ms.reviewer: nibaccam
ms.date: 02/23/2021
ms.openlocfilehash: 82ea26a5522d44bb39adc30f388f996688cb1ad5
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122529931"
---
# <a name="configure-data-splits-and-cross-validation-in-automated-machine-learning"></a>자동화된 Machine Learning에서 데이터 분할 및 교차 유효성 검사 구성

이 문서에서는 자동화된 Machine Learning, 자동화된 ML, 실험에 대한 교차 유효성 검사 설정과 함께 학습 데이터 및 유효성 검사 데이터 분할을 구성하는 다양한 옵션을 알아봅니다.

Azure Machine Learning에서 자동화된 ML을 사용하여 여러 ML 모델을 빌드하는 경우, 각 자식 실행은 정확도 또는 AUC 가중치와 같은 해당 모델의 품질 메트릭을 계산하여 관련 모델의 유효성을 검사해야 합니다. 이러한 메트릭은 각 모델의 예측 결과를 유효성 검사 데이터의 과거 관찰에서 얻은 실제 레이블과 비교하여 계산합니다. [유효성 검사 유형에 따라 메트릭이 계산되는 방법에 대해 자세히 알아보세요](#metric-calculation-for-cross-validation-in-machine-learning). 

자동화된 ML 실험은 모델 유효성 검사를 자동으로 수행합니다. 다음 섹션에서는 [Azure Machine Learning Python SDK](/python/api/overview/azure/ml/)를 사용하여 유효성 검사 설정을 추가로 사용자 지정할 수 있는 방법을 설명합니다. 

하위 코드 또는 코드가 없는 환경은 [Azure Machine Learning 스튜디오에서 자동화된 Machine Learning 실험 만들기](how-to-use-automated-ml-for-ml-models.md#create-and-run-experiment)를 참조하세요. 

> [!NOTE]
> 이 스튜디오는 현재 학습 및 유효성 검사 데이터 분할뿐만 아니라 교차 유효성 검사 옵션도 지원하지만, 유효성 검사 집합에 개별 데이터 파일을 지정하는 것은 지원하지 않습니다. 

## <a name="prerequisites"></a>필수 구성 요소

이 문서의 내용을 진행하려면 다음 항목이 필요합니다.

* Azure Machine Learning 작업 영역 작업 영역을 만들려면 [Azure Machine Learning 작업 영역 만들기](how-to-manage-workspace.md)를 참조하세요.

* Azure Machine Learning SDK를 사용하여 자동화된 Machine Learning 실험을 설정하는 방법에 대해 잘 알고 있어야 합니다. [자습서](tutorial-auto-train-models.md) 또는 [방법](how-to-configure-auto-train.md)에 따라 기본적인 자동화된 Machine Learning 실험 디자인 패턴을 확인합니다.

* 기계 학습 개념인 학습/유효성 검사 데이터 분할 및 교차 유효성 검사를 이해하고 있어야 합니다. 개략적인 설명은 다음을 참조하세요.

    * [기계 학습의 학습, 유효성 검사 및 테스트 데이터 정보](https://towardsdatascience.com/train-validation-and-test-sets-72cb40cba9e7)

    * [기계 학습의 교차 유효성 검사 이해](https://towardsdatascience.com/understanding-cross-validation-419dbd47e9bd) 

[!INCLUDE [automl-sdk-version](../../includes/machine-learning-automl-sdk-version.md)]

## <a name="default-data-splits-and-cross-validation-in-machine-learning"></a>기계 학습의 기본 데이터 분할 및 교차 유효성 검사

[AutoMLConfig](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) 개체를 사용하여 실험 및 학습 설정을 정의합니다. 다음 코드 조각에는 필요한 매개 변수만 정의되어 있습니다. 즉, `n_cross_validations` 또는 `validation_data`에 대한 매개 변수는 포함되어 있지 **않습니다**.

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = dataset,
                             label_column_name = 'Class'
                            )
```

`validation_data` 또는 `n_cross_validations` 매개 변수를 명시적으로 지정하지 않으면 자동화된 ML이 단일 데이터 세트 `training_data`에 제공된 행 수에 따라 기본 기법을 적용합니다.

|학습&nbsp;데이터&nbsp;크기| 유효성 검사 기법 |
|---|-----|
|**20,000개&nbsp;행보다&nbsp;더&nbsp;큼**| 학습/유효성 검사 데이터 분할이 적용됩니다. 기본값은 초기 학습 데이터 세트의 10%를 유효성 검사 세트로 사용하는 것입니다. 그러면 해당 유효성 검사 집합이 메트릭 계산에 사용됩니다.
|**20,000개&nbsp;행보다&nbsp;더&nbsp;작음**| 교차 유효성 검사 방법이 적용됩니다. 기본 접기 횟수는 행의 수에 따라 달라집니다. <br> **데이터 세트의 행이 1,000개 이하인 경우** 10겹 접기가 사용됩니다. <br> **행이 1,000개~20,000개 사이인 경우** 3겹 접기가 사용됩니다.

## <a name="provide-validation-data"></a>유효성 검사 데이터 제공

이 경우 단일 데이터 파일로 시작하고 학습 데이터 및 유효성 검사 데이터 세트로 분할하거나 유효성 검사 집합을 위한 별도의 데이터 파일을 제공할 수 있습니다. 어느 방법을 사용하든 `AutoMLConfig` 개체의 `validation_data` 매개 변수가 유효성 검사 집합으로 사용할 데이터를 할당합니다. 이 매개 변수는 [Azure Machine Learning 데이터 세트](how-to-create-register-datasets.md) 또는 pandas 데이터 프레임 형식의 데이터 세트만 허용합니다.   

> [!NOTE]
> `validation_data` 매개 변수를 사용하려면 `training_data` 및 `label_column_name` 매개 변수도 설정해야 합니다. 유효성 검사 매개 변수는 하나만 설정할 수 있습니다. 즉, `validation_data` 또는 `n_cross_validations` 중 하나만 지정할 수 있고 둘 다 지정할 수는 없습니다.

다음 코드 예제에서는 `dataset`의 제공된 데이터 중에서 학습 및 유효성 검사에 사용할 부분을 명시적으로 정의합니다.

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

training_data, validation_data = dataset.random_split(percentage=0.8, seed=1)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = training_data,
                             validation_data = validation_data,
                             label_column_name = 'Class'
                            )
```

## <a name="provide-validation-set-size"></a>유효성 검사 집합 크기 제공

이 경우 실험에 단일 데이터 세트만 제공됩니다. 즉, `validation_data` 매개 변수가 지정되지 **않고**, 제공 된 데이터 세트가 `training_data` 매개 변수에 할당됩니다.  

`AutoMLConfig` 개체에서 유효성 검사를 위해 학습 데이터의 일부를 포함하도록 `validation_size` 매개 변수를 설정할 수 있습니다. 즉, 유효성 검사 집합은 제공된 초기 `training_data`에서 자동화된 ML에 의해 분할됩니다. 이 값은 0.0에서 1.0(제외) 사이여야 합니다. 예를 들어 0.2는 20%의 데이터가 유효성 검사 데이터용으로 홀드아웃된다는 의미입니다.

> [!NOTE]
> `validation_size` 매개 변수는 예측 시나리오에서 지원되지 않습니다. 

다음 코드 예제를 참조하세요.

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = dataset,
                             validation_size = 0.2,
                             label_column_name = 'Class'
                            )
```

## <a name="k-fold-cross-validation"></a>K-폴드 교차 유효성 검사

k겹 교차 유효성 검사를 수행하려면 `n_cross_validations` 매개 변수를 포함하고 특정 값으로 설정합니다. 이 매개 변수는 동일한 접기 수에 따라 수행할 교차 유효성 검사의 수를 설정합니다.

> [!NOTE]
> `n_cross_validations` 매개 변수는 심층 신경망을 사용하는 분류 시나리오에서 지원되지 않습니다.
 
다음 코드에서는 교차 유효성 검사에 대한 5겹 접기가 정의됩니다. 따라서 5가지의 각 학습은 데이터의 4/5를 사용하고, 각 유효성 검사는 매번 다른 홀드아웃 접기로 데이터의 1/5을 사용합니다.

따라서 메트릭은 5가지 유효성 검사 메트릭의 평균을 사용하여 계산됩니다.

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = dataset,
                             n_cross_validations = 5
                             label_column_name = 'Class'
                            )
```
## <a name="monte-carlo-cross-validation"></a>몬테카를로 교차 유효성 검사

몬테카를로 교차 유효성 검사를 수행하려면 `AutoMLConfig` 개체에 `validation_size` 및 `n_cross_validations` 매개 변수를 모두 포함합니다. 

몬테카를로 교차 유효성 검사에서는 자동화된 ML이 `validation_size` 매개 변수에 지정된 유효성 검사용 학습 데이터의 일부를 확보한 다음, 나머지 데이터를 학습용으로 할당합니다. 그런 다음, `n_cross_validations` 매개 변수에 지정된 값을 기준으로 이 프로세스가 반복되며, 매번 무작위로 새 학습 및 유효성 검사 분할이 생성됩니다.

> [!NOTE]
> 몬테카를로 교차 유효성 검사는 예측 시나리오에서 지원되지 않습니다.

다음 코드는 교차 유효성 검사에 대한 7겹 접기와 학습 데이터의 20%가 유효성 검사에 사용되어야 함을 정의합니다. 따라서 7가지의 각 학습은 데이터의 80%를 사용하고, 각 유효성 검사는 매번 다른 홀드아웃 접기로 데이터의 20%를 사용합니다.

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = dataset,
                             n_cross_validations = 7
                             validation_size = 0.2,
                             label_column_name = 'Class'
                            )
```

## <a name="specify-custom-cross-validation-data-folds"></a>사용자 지정 교차 유효성 검사 데이터 접기 지정

또한 사용자 고유의 CV(교차 유효성 검사) 데이터 접기를 제공할 수 있습니다. 이는 어떤 열을 분할하고 유효성 검사에 사용할지 지정하기 때문에 더 고급 시나리오로 간주됩니다.  사용자 지정 CV 분할 열을 학습 데이터에 포함하고, `cv_split_column_names` 매개 변수에 열 이름을 채워서 열을 지정합니다. 각 열은 단일 교차 유효성 검사 분할을 나타내며, 정수 값 1 또는 0으로 채워집니다. 여기서 1은 해당 행이 학습에 사용되어야 함을 나타내며, 0은 해당 행이 유효성 검사에 사용되어야 함을 나타냅니다.

다음 코드 조각에는 'cv1' 및 'cv2'라는 두 개의 CV 분할 열이 포함된 은행 마케팅 데이터가 있습니다.

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_with_cv.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = dataset,
                             label_column_name = 'y',
                             cv_split_column_names = ['cv1', 'cv2']
                            )
```

> [!NOTE]
> `cv_split_column_names`를 `training_data` 및 `label_column_name`과 함께 사용하려면 Azure Machine Learning Python SDK를 1.6.0 이상 버전으로 업그레이드하세요. 이전 SDK 버전의 경우 `cv_splits_indices` 사용법을 참조하세요. 단, `X` 및 `y` 데이터 세트 입력에만 사용된다는 사실을 유의하세요. 


## <a name="metric-calculation-for-cross-validation-in-machine-learning"></a>기계 학습에서 교차 유효성 검사에 대한 메트릭 계산

K겹 또는 몬테카를로 교차 유효성 검사를 사용하는 경우 각 유효성 검사 접기에 대해 메트릭이 계산된 후 집계됩니다. 집계 연산은 스칼라 메트릭의 평균과 차트의 합계입니다. 교차 유효성 검사 중에 계산된 메트릭은 모든 접기를 기반으로 하므로 학습 집합의 모든 샘플을 기반으로 합니다. [자동화된 Machine Learning의 메트릭에 대해 자세히 알아보세요](how-to-understand-automated-ml.md).

사용자 지정 유효성 검사 집합 또는 자동으로 선택된 유효성 검사 집합을 사용하는 경우, 모델 평가 메트릭이 학습 데이터가 아니라 해당 유효성 검사 집합에서만 계산됩니다.

## <a name="next-steps"></a>다음 단계

* [불균형 데이터 및 과잉 맞춤 방지](concept-manage-ml-pitfalls.md)
* [자습서: 자동화된 Machine Learning을 사용하여 택시 요금 예측 - 분할 데이터 섹션](tutorial-auto-train-models.md#split-the-data-into-train-and-test-sets)
* [시계열 예측 모델 자동 학습](how-to-auto-train-forecast.md) 방법
