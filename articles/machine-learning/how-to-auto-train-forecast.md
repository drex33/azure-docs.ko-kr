---
title: 시계열 예측을 위한 AutoML 설정
titleSuffix: Azure Machine Learning
description: Azure Machine Learning Python SDK를 사용하여 시계열 예측 모델을 학습하도록 Azure Machine Learning 자동화된 ML을 설정합니다.
services: machine-learning
author: nibaccam
ms.author: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.custom: contperf-fy21q1, automl, FY21Q4-aml-seo-hack
ms.date: 06/11/2021
ms.openlocfilehash: 26a83b28fd6e1fdaded9884deb0d7197e0a59a6f
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124784908"
---
# <a name="set-up-automl-to-train-a-time-series-forecasting-model-with-python"></a>Python으로 시계열 예측 모델을 학습시키도록 AutoML 설정

이 문서에서는 [Azure Machine Learning Python SDK](/python/api/overview/azure/ml/)에서 Azure Machine Learning 자동화 ML을 사용하여 시계열 예측 모델에 AutoML 학습을 설정하는 방법을 알아봅니다.

이렇게 하려면 다음을 수행합니다. 

> [!div class="checklist"]
> * 시계열 모델링에 대한 데이터를 준비합니다.
> * [`AutoMLConfig`](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) 개체에서 특정 시계열 매개 변수를 구성합니다.
> * 시계열 데이터를 사용하여 예측을 실행합니다.

하위 코드 환경의 경우 [Azure Machine Learning Studio](https://ml.azure.com/)에서 자동화된 ML을 사용하는 시계열 예측 예제를 알아보려면 [자습서: 자동화된 Machine Learning으로 수요 예측](tutorial-automated-ml-forecast.md)을 참조하세요.

고전적인 시계열 방법과 달리, 자동화된 ML에서는 이전 시계열 값이 "피벗"되어 다른 예측 변수와 함께 회귀 변수의 추가 차원이 됩니다. 이 방법은 학습 중에 여러 컨텍스트 변수와 각 변수 간 관계를 통합합니다. 여러 요인이 예측에 영향을 줄 수 있으므로 이 방법은 실제 예측 시나리오에 적합합니다. 예를 들어 판매를 예측하는 경우 판매 결과에 과거 기록 추세의 상호 작용, 환율 및 가격이 모두 함께 영향을 미칩니다. 


## <a name="prerequisites"></a>필수 구성 요소

이 문서의 내용을 진행하려면 다음 항목이 필요합니다. 

* Azure Machine Learning 작업 영역 작업 영역을 만들려면 [Azure Machine Learning 작업 영역 만들기](how-to-manage-workspace.md)를 참조하세요.

* 이 문서에서는 자동화된 Machine Learning 실험 설정에 어느 정도 익숙한 것으로 가정합니다. [자습서](tutorial-auto-train-models.md) 또는 [방법](how-to-configure-auto-train.md)에 따라 기본적인 자동화된 Machine Learning 실험 디자인 패턴을 확인합니다.

    [!INCLUDE [automl-sdk-version](../../includes/machine-learning-automl-sdk-version.md)]
## <a name="preparing-data"></a>데이터 준비

자동화 ML에서 예측 회귀 작업 유형과 회귀 작업 유형 간의 가장 중요한 차이점은 유효한 시계열을 나타내는 기능을 데이터에 포함하는 것입니다. 정규 시계열에는 잘 정의되고 일관된 빈도가 있으며 연속 시간 범위의 모든 샘플 요소에 값이 있습니다. 

`sample.csv` 파일의 다음 스냅샷을 살펴보세요.
이 데이터 세트는 두 개의 다른 상점 A와 B가 있는 회사의 일일 판매 데이터입니다. 

또한 다음과 같은 기능이 있습니다.

 *  `week_of_year`: 모델이 주간 계절성을 검색할 수 있습니다.
* `day_datetime`: 일별 빈도로 정리 시계열을 나타냅니다.
* `sales_quantity`: 예측을 실행하기 위한 대상 열입니다. 

```output
day_datetime,store,sales_quantity,week_of_year
9/3/2018,A,2000,36
9/3/2018,B,600,36
9/4/2018,A,2300,36
9/4/2018,B,550,36
9/5/2018,A,2100,36
9/5/2018,B,650,36
9/6/2018,A,2400,36
9/6/2018,B,700,36
9/7/2018,A,2450,36
9/7/2018,B,650,36
```


Pandas 데이터 프레임으로 데이터를 읽어온 다음, `to_datetime` 함수를 사용하여 시계열이 `datetime` 형식인지 확인합니다.

```python
import pandas as pd
data = pd.read_csv("sample.csv")
data["day_datetime"] = pd.to_datetime(data["day_datetime"])
```

이 경우 데이터는 이미 시간 필드 `day_datetime`의 오름차순으로 정렬됩니다. 그러나 실험을 설정하는 경우 올바른 시계열을 작성하기 위해 원하는 시간 열이 오름차순으로 정렬되어 있는지 확인합니다. 

코드는 다음과 같습니다. 
* 데이터에 1,000개의 레코드가 포함되어 있다고 가정하고 데이터를 명확하게 분할하여 학습 및 테스트용 데이터 세트를 만듭니다. 
* 레이블 열을 `sales_quantity`로 식별합니다.
* 레이블 필드를 `test_data`에서 분리하여 `test_target` 세트를 만듭니다.

```python
train_data = data.iloc[:950]
test_data = data.iloc[-50:]

label =  "sales_quantity"
 
test_labels = test_data.pop(label).values
```

> [!IMPORTANT]
> 미래 가치를 예측하기 위해 모델을 학습하는 경우 학습에 사용된 모든 기능을 의도한 구간에 대해 예측을 실행할 때도 사용할 수 있는지 확인합니다. <br> <br>예를 들어, 수요 예측을 만들 때 현재 주가에 대한 기능을 포함하면 학습 정확도가 크게 높아질 수 있습니다. 그러나 장기 구간을 사용하여 예측하려는 경우 미래 시계열 요소에 해당하는 선물 주가를 정확하게 예측할 수 없으며 모델 정확성이 떨어질 수 있습니다.

<a name="config"></a>

## <a name="training-and-validation-data"></a>학습 및 유효성 검사 데이터

별도의 학습 및 유효성 검사 세트를 `AutoMLConfig` 개체에 직접 지정할 수 있습니다.   [AutoMLConfig](#configure-experiment)에 대해 자세히 알아보세요.

시계열 예측의 경우 기본적으로 **ROCV(이동 원본 교차 유효성 검사)** 만 유효성 검사에 사용됩니다. 학습 및 유효성 검사 데이터를 함께 전달하고 `n_cross_validations` 매개 변수를 사용한 교차 유효성 검사 접기 수를 `AutoMLConfig`에서 설정합니다. ROCV는 원본 시간 요소를 사용하여 계열을 학습 및 유효성 검사 데이터로 나눕니다. 원본을 시간에 따라 슬라이딩하여 교차 유효성 검사 겹을 생성합니다. 이 전략은 시계열 데이터의 무결성을 유지하고 데이터 누출의 위험을 제거합니다.

![이동 원본 교차 유효성 검사](./media/how-to-auto-train-forecast/ROCV.svg)

자체 유효성 검사 데이터를 가져올 수 있으며 [AutoML에서 데이터 분할 및 교차 유효성 검사 구성](how-to-configure-cross-validation-data-splits.md#provide-validation-data)에서 자세히 알아볼 수 있습니다.


```python
automl_config = AutoMLConfig(task='forecasting',
                             n_cross_validations=3,
                             ...
                             **time_series_settings)
```

AutoML이 교차 유효성 검사를 적용하여 [과잉 맞춤 모델을 방지](concept-manage-ml-pitfalls.md#prevent-overfitting)하는 방법에 대해 자세히 알아봅니다.

## <a name="configure-experiment"></a>실험 구성

[`AutoMLConfig`](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) 개체는 자동화된 기계 학습 태스크에 필요한 설정 및 데이터를 정의합니다. 예측 모델의 구성은 표준 회귀 모델의 설정과 유사하지만 특히 시계열 데이터에 대해서는 특정 모델, 구성 옵션 및 기능화 단계가 존재합니다. 

### <a name="supported-models"></a>지원되는 모델
자동화된 Machine Learning은 모델 생성 및 튜닝 프로세스의 일부로 다른 모델 및 알고리즘을 자동으로 시도합니다. 사용자는 알고리즘을 지정할 필요가 없습니다. 예측 실험의 경우 원시 시계열 및 딥 러닝 모델은 모두 권장 시스템의 일부입니다. 다음 표에는 이러한 모델의 하위 집합이 요약되어 있습니다. 

>[!Tip]
> 기존 회귀 모델도 예측 실험을 위한 권장 시스템의 일부로 테스트됩니다. 모델의 전체 목록은 [지원되는 모델 표](how-to-configure-auto-train.md#supported-models)를 참조하세요. 

모델| Description | 이점
----|----|---
Prophet(미리 보기)|Prophet은 강력한 계절적 효과와 여러 계절의 기록 데이터를 포함하는 시계열에서 가장 잘 작동합니다. 이 모델을 활용하려면 `pip install fbprophet`를 사용하여 로컬에 설치합니다. | 시계열의 이상값, 누락된 데이터 및 획기적인 변경에 정확하고 빠르고 강력하게 작동합니다.
Auto-ARIMA(미리 보기)|ARIMA(자동 회귀 통합 이동 평균)는 데이터가 고정되어 있을 때 가장 잘 작동합니다. 즉, 평균 및 편차와 같은 통계 속성이 전체 세트에서 일정하게 유지됩니다. 예를 들어 동전을 던질 때 앞면이 나올 확률은 오늘 던지든 내일 던지든 또는 내년에 던지든 항상 50%입니다.| 과거의 값을 사용하여 미래의 값을 예측하므로 단변량 계열에 적합합니다.
ForecastTCN(미리 보기)| ForecastTCN은 가장 까다로운 예측 작업을 처리하도록 설계된 신경망 모델입니다. 시계열 간의 관계와 데이터의 비선형 로컬 및 글로벌 추세를 캡처합니다.|데이터의 복잡한 추세를 활용하고 가장 큰 데이터 세트로 쉽게 확장할 수 있습니다.

### <a name="configuration-settings"></a>구성 설정

회귀 문제와 마찬가지로 태스크 유형, 반복 횟수, 학습 데이터 및 교차 유효성 검사 수와 같은 표준 학습 매개 변수를 정의합니다. 예측 작업의 경우 실험에 영향을 주는 추가 매개 변수를 설정해야 합니다. 

다음 표에는 이러한 추가 매개 변수가 요약되어 있습니다. 구문 디자인 패턴에 대해서는 [ForecastingParameter 클래스 참조 설명서](/python/api/azureml-automl-core/azureml.automl.core.forecasting_parameters.forecastingparameters)를 참조하세요.

| 매개 변수&nbsp;이름 | Description | 필수 |
|-------|-------|-------|
|`time_column_name`|시계열을 작성하고 해당 빈도를 유추하는 데 사용되는 날짜/시간 열을 입력 데이터에 지정하는 데 사용됩니다.|✓|
|`forecast_horizon`|앞으로 어느 정도의 기간에 대해 예측할 것인지 정의합니다. 구간은 시계열 빈도의 단위입니다. 단위는 예측자가 예측해야 하는 학습 데이터의 시간 간격(예: 매월, 매주)을 기준으로 합니다.|✓|
|`enable_dnn`|[예측 DNN을 사용하도록 설정]()합니다.||
|`time_series_id_column_names`|타임스탬프가 동일한 여러 행이 있는 데이터에서 시계열을 고유하게 식별하는 데 사용되는 열 이름입니다. 시계열 식별자가 정의되지 않은 경우 데이터 세트는 하나의 시계열로 간주됩니다. 단일 시계열에 대한 자세한 내용은 [energy_demand_notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand)을 참조하세요.||
|`freq`| 시계열 데이터 세트 빈도입니다. 이 매개 변수는 매일, 매주, 매년 등 이벤트가 발생할 것으로 예상되는 기간을 나타냅니다. 빈도는 [pandas 오프셋 별칭](https://pandas.pydata.org/pandas-docs/stable/user_guide/timeseries.html#dateoffset-objects)이어야 합니다. [frequency]에 대해 자세히 알아보세요. (#frequency-target-data-aggregation)||
|`target_lags`|데이터의 빈도에 따라 대상 값을 지연시킬 행 수입니다. 지연은 목록 또는 단일 정수로 표시됩니다. 지연은 독립 변수와 종속 변수 간 관계가 일치하지 않거나 기본적으로 상관 관계가 없는 경우에 사용해야 합니다. ||
|`feature_lags`| 지연에 대한 기능은 `target_lags`가 설정되고 `feature_lags`가 `auto`로 설정된 경우 자동화된 ML에 의해 자동으로 결정됩니다. 기능 지연을 사용하도록 설정하면 정확도를 향상시키는 데 도움이 될 수 있습니다. 기능 지연은 기본적으로 사용하지 않도록 설정되어 있습니다. ||
|`target_rolling_window_size`|예측 값(학습 세트 크기 이하)을 생성하는 데 사용할 *n* 개 기록 기간입니다. 생략하면 *n* 은 전체 학습 세트 크기입니다. 모델을 학습시킬 때 특정한 양의 기록만 고려하려는 경우 이 매개 변수를 지정합니다. [대상 이동 기간 집계](#target-rolling-window-aggregation)에 대해 자세히 알아봅니다.||
|`short_series_handling_config`| 짧은 시계열 처리를 사용하도록 설정하여 데이터 부족으로 인해 학습 중에 오류가 발생하는 것을 방지합니다. 짧은 계열 처리는 기본적으로 `auto`로 설정됩니다. [짧은 계열 처리](#short-series-handling)에 대해 자세히 알아봅니다.||
|`target_aggregation_function`| `freq` 매개 변수를 통해 지정된 빈도를 준수하도록 시계열 대상 열을 집계하는 데 사용되는 함수입니다. `freq` 매개 변수를 설정해야만 `target_aggregation_function`을 사용할 수 있습니다. 기본값은 `None`입니다. 대부분의 시나리오에서는 `sum`을 사용해도 충분합니다.<br> [대상 열 집계](#frequency--target-data-aggregation)에 대해 자세히 알아봅니다. 


코드는 다음과 같습니다. 
* [`ForecastingParameters`](/python/api/azureml-automl-core/azureml.automl.core.forecasting_parameters.forecastingparameters) 클래스를 활용하여 실험 학습에 대한 예측 매개 변수를 정의합니다.
* `time_column_name`을 데이터 세트의 `day_datetime` 필드에 설정합니다. 
* `time_series_id_column_names` 매개 변수를 `"store"`에 정의합니다. 이렇게 하면 데이터에 대해 **두 개의 개별 시계열 그룹** 이 만들어집니다. 상점 A와 B용입니다.
* 전체 테스트 세트를 예측하려면 `forecast_horizon`을 50으로 설정합니다. 
* `target_rolling_window_size`를 사용하여 예측 기간을 10개 기간으로 설정합니다.
* `target_lags` 매개 변수를 사용하여 두 기간 앞의 대상 값에 단일 지연을 지정합니다. 
* `target_lags`를 권장 "자동" 설정으로 설정하면 이 값을 자동으로 검색합니다.

```python
from azureml.automl.core.forecasting_parameters import ForecastingParameters

forecasting_parameters = ForecastingParameters(time_column_name='day_datetime', 
                                               forecast_horizon=50,
                                               time_series_id_column_names=["store"],
                                               freq='W',
                                               target_lags='auto',
                                               target_rolling_window_size=10)
                                              
```

다음으로 이들 `forecasting_parameters`가 표준 `AutoMLConfig` 개체로 전달되고 `forecasting` 작업 유형, 기본 메트릭, 종료 조건 및 학습 데이터도 함께 전달됩니다. 

```python
from azureml.core.workspace import Workspace
from azureml.core.experiment import Experiment
from azureml.train.automl import AutoMLConfig
import logging

automl_config = AutoMLConfig(task='forecasting',
                             primary_metric='normalized_root_mean_squared_error',
                             experiment_timeout_minutes=15,
                             enable_early_stopping=True,
                             training_data=train_data,
                             label_column_name=label,
                             n_cross_validations=5,
                             enable_ensembling=False,
                             verbosity=logging.INFO,
                             **forecasting_parameters)
```

자동화된 ML을 사용하여 예측 모델을 학습시키는 데 필요한 데이터 양은 `AutoMLConfig`를 구성할 때 지정된 `forecast_horizon`, `n_cross_validations` 및 `target_lags` 또는 `target_rolling_window_size` 값의 영향을 받습니다. 

다음 수식은 시계열 기능을 구성하는 데 필요한 기록 데이터의 양을 계산합니다.

필요한 최소 기록 데이터: (2x `forecast_horizon`) + #`n_cross_validations` + max(max(`target_lags` ), `target_rolling_window_size`)

지정된 관련 설정에 필요한 기록 데이터 양을 충족하지 않는 데이터 세트의 계열에 대해서는 예외 오류가 발생합니다. 

### <a name="featurization-steps"></a>기능화 단계

모든 자동화된 Machine Learning 실험에서 자동 크기 조정 및 정규화 기술이 기본적으로 데이터에 적용됩니다. 이러한 기술은 **기능화** 유형으로서 다양한 규모에서의 기능에 민감한 *특정* 알고리즘을 지원합니다. [AutoML의 기능화](how-to-configure-auto-features.md#automatic-featurization)에서 기본 기능화 단계에 대해 자세히 알아봅니다.

그러나 다음 단계는 `forecasting` 작업 유형에 대해서만 수행됩니다.

* 시계열 샘플 빈도(예: 매시간, 매일, 매주)를 검색하고 없는 시간 요소의 새 레코드를 만들어 계열이 연속되도록 합니다.
* 누락된 값을 대상(전방 채우기를 통해) 및 기능 열(중앙값 열 값 사용)에 귀속
* 시계열 식별자를 기반으로 기능을 만들어 여러 계열에서 고정 효과를 사용할 수 있도록 설정
* 계절적 패턴을 학습하는 데 도움이 되는 시간 기반 기능 만들기
* 범주 변수를 숫자 수량으로 인코딩

이러한 단계의 결과로 만들어지는 기능에 대한 요약을 보려면 [기능화 투명성](how-to-configure-auto-features.md#featurization-transparency)을 참조하세요.

> [!NOTE]
> 자동화된 Machine Learning 기능화 단계(기능 정규화, 누락된 데이터 처리, 텍스트를 숫자로 변환 등)는 기본 모델의 일부가 됩니다. 예측에 모델을 사용하는 경우 학습 중에 적용되는 동일한 기능화 단계가 입력 데이터에 자동으로 적용됩니다.

#### <a name="customize-featurization"></a>기능화 사용자 지정

또한 ML 모델을 학습시키는 데 사용되는 데이터와 기능이 관련 있는 예측 결과를 내도록 기능화 설정을 사용자 지정하는 옵션도 있습니다. 

`forecasting` 작업에 대해 지원되는 사용자 지정은 다음과 같습니다.

|사용자 지정|정의|
|--|--|
|**열 용도 업데이트**|지정된 열에 대해 자동 감지된 기능 유형을 재정의합니다.|
|**변환기 매개 변수 업데이트** |지정된 변환기의 매개 변수를 업데이트합니다. 현재 *Imputer*(fill_value 및 median)를 지원합니다.|
|**삭제 열** |기능화에서 삭제할 열을 지정합니다.|

SDK를 사용하여 기능화를 사용자 지정하려면 `"featurization": FeaturizationConfig`를 `AutoMLConfig` 개체에 지정합니다. [사용자 지정 기능화](how-to-configure-auto-features.md#customize-featurization)에 대해 자세히 알아봅니다.

>[!NOTE]
> **열 삭제** 기능은 SDK 버전 1.19부터 더 이상 사용되지 않습니다. 자동화된 ML 실험에서 사용하기 전에 데이터 정리의 일부로 데이터 세트에서 열을 삭제합니다. 

```python
featurization_config = FeaturizationConfig()

# `logQuantity` is a leaky feature, so we remove it.
featurization_config.drop_columns = ['logQuantitity']

# Force the CPWVOL5 feature to be of numeric type.
featurization_config.add_column_purpose('CPWVOL5', 'Numeric')

# Fill missing values in the target column, Quantity, with zeroes.
featurization_config.add_transformer_params('Imputer', ['Quantity'], {"strategy": "constant", "fill_value": 0})

# Fill mising values in the `INCOME` column with median value.
featurization_config.add_transformer_params('Imputer', ['INCOME'], {"strategy": "median"})
```

실험에 Azure Machine Learning 스튜디오를 사용하는 경우 [스튜디오에서 기능화를 사용자 지정하는 방법](how-to-use-automated-ml-for-ml-models.md#customize-featurization)을 참조하세요.

## <a name="optional-configurations"></a>선택적 구성

딥 러닝을 사용하도록 설정하고 대상 이동 기간 집계를 지정하는 등의 예측 작업에 대한 추가적인 옵션 구성을 사용할 수 있습니다. 

### <a name="frequency--target-data-aggregation"></a>빈도 및 대상 데이터 집계

빈도를 나타내는 `freq` 매개 변수를 활용하여 불규칙한 데이터, 즉 시간별 또는 일별 데이터와 같이 정해진 주기를 따르지 않는 데이터로 인한 오류를 방지할 수 있습니다. 

매우 불규칙한 데이터 또는 변화하는 비즈니스 요구에 대해 사용자는 원하는 예측 빈도인 `freq`를 선택적으로 설정하고 `target_aggregation_function`을 지정하여 시계열의 대상 열을 집계할 수 있습니다. `AutoMLConfig` 개체에서 이 두 가지 설정을 활용하면 데이터 준비 시간을 절약할 수 있습니다. 

`target_aggregation_function` 매개 변수가 사용되는 경우
* 대상 열 값은 지정된 작업을 기반으로 집계됩니다. 일반적으로 `sum`은 대부분의 시나리오에 적합합니다.

* 데이터의 숫자 예측 요소 열은 합계, 평균, 최솟값 및 최댓값으로 집계됩니다. 결과적으로 자동화된 ML은 집계 함수 이름이 접미사인 새 열을 생성하고 선택된 집계 작업을 적용합니다. 

* 범주 예측요소 열의 경우 데이터는 기간 내에 가장 두드러진 범주인 최빈값으로 집계됩니다.

* 날짜 예측 열은 최솟값, 최댓값 및 최빈값으로 집계됩니다. 

대상 열 값에 대해 지원되는 집계 작업은 다음과 같습니다.

|함수 | description
|---|---
|`sum`| 대상 값의 합계
|`mean`| 대상 값의 평균 또는 산술평균
|`min`| 대상의 최솟값  
|`max`| 대상의 최댓값  

### <a name="enable-deep-learning"></a>딥 러닝 사용

> [!NOTE]
> 자동화된 Machine Learning의 예측에 대한 DNN 지원은 **미리 보기** 로 제공되며, 로컬 실행 또는 Databricks에서 시작된 실행에는 지원되지 않습니다.

심층 신경망, 즉 DNN을 사용한 딥 러닝을 적용하여 모델의 점수를 개선할 수도 있습니다. 자동화된 ML의 딥 러닝을 사용하여 단변량 및 다변량 시계열 데이터를 예측할 수 있습니다.

딥 러닝 모델에는 다음과 같은 세 가지 기능이 내장되어 있습니다.
1. 입력에서 출력으로의 임의 매핑에서 학습할 수 있습니다.
1. 여러 입력 및 출력을 지원합니다.
1. 긴 시퀀스에 걸쳐 있는 입력 데이터의 패턴을 자동으로 추출할 수 있습니다. 

딥 러닝을 사용하도록 설정하려면 `AutoMLConfig` 개체에서 `enable_dnn=True`를 설정합니다.

```python
automl_config = AutoMLConfig(task='forecasting',
                             enable_dnn=True,
                             ...
                             **forecasting_parameters)
```
> [!Warning]
> SDK로 만든 실험에 DNN을 사용하도록 설정하면 [최상의 모델 설명](how-to-machine-learning-interpretability-automl.md)이 사용하지 않도록 설정됩니다.

Azure Machine Learning 스튜디오에서 만든 AutoML 실험에 DNN을 사용하도록 설정하려면 [스튜디오에서 작업 유형 설정 방법](how-to-use-automated-ml-for-ml-models.md#create-and-run-experiment)을 참조하세요.

DNN을 사용하는 상세한 코드 예제는 [음료 생산 예측 Notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-beer-remote/auto-ml-forecasting-beer-remote.ipynb)을 확인합니다.

### <a name="target-rolling-window-aggregation"></a>대상 이동 기간 집계
종종 예측자가 보유할 수 있는 최상의 정보는 대상의 최신 값입니다.  대상 이동 기간 집계를 사용하면 데이터 값의 이동 집계를 기능으로 추가할 수 있습니다. 이러한 기능을 추가적인 상황별 데이터로 생성하여 사용하면 학습 모델의 정확도에 도움이 됩니다.

예를 들어 에너지 수요를 예측하고자 한다고 가정해 봅니다. 가열된 공간의 열 변화를 설명하기 위해 이동 기간 기능에 3일을 추가하고자 할 수 있습니다. 이 예에서는 `AutoMLConfig` 생성자에서 `target_rolling_window_size= 3`을 설정하여 이 기간을 만듭니다. 

이 표는 기간 집계가 적용될 때 발생하는 기능 엔지니어링 결과를 보여 줍니다. **최소, 최대** 및 **합계** 열은 정의된 설정을 기반으로 3개의 슬라이딩 윈도우에서 생성됩니다. 각 행에는 새로 계산된 기능이 있는데 2017년 9월 8일 오전 4시 타임스탬프의 경우 최대, 최소 및 합계 값은 2017년 9월 8일 오전 1시 - 오전 3시의 **수요 값** 을 사용하여 계산됩니다. 이 3개의 기간은 함께 이동하면서 나머지 행의 데이터를 채웁니다.

![대상 이동 기간](./media/how-to-auto-train-forecast/target-roll.svg)

[대상 이동 기간 집계 기능](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)을 적용하는 Python 코드 예제를 확인합니다.

### <a name="short-series-handling"></a>짧은 계열 처리

자동화된 ML은 모델 개발의 학습 및 유효성 검사 단계를 수행하기에 충분한 데이터 요소가 없으면 시계열을 **짧은 계열** 로 간주합니다. 데이터 포인트의 수는 각 실험마다 다르며, max_horizon, 교차 유효성 검사 분할 수, 시계열 기능을 생성하는 데 필요한 기록의 최댓값인 모델 Lookback의 길이에 따라 달라집니다. 정확한 계산에 대해서는 [short_series_handling_configuration 참조 설명서](/python/api/azureml-automl-core/azureml.automl.core.forecasting_parameters.forecastingparameters#short-series-handling-configuration)를 참조하세요.

자동화된 ML은 `short_series_handling_configuration` 매개 변수를 사용하여 `ForecastingParameters` 개체에서 기본적으로 짧은 계열 처리를 제공합니다. 

짧은 계열 처리를 사용하도록 설정하려면 `freq` 매개 변수도 정의해야 합니다. 시간별 빈도를 정의하려면 `freq='H'`를 설정합니다. [여기](https://pandas.pydata.org/pandas-docs/stable/user_guide/timeseries.html#dateoffset-objects)에서 빈도 문자열 옵션을 확인합니다. `short_series_handling_configuration = 'auto'`인 기본 동작을 변경하려면 `short_series_handling_configuration` 매개 변수를 `ForecastingParameter` 개체에서 업데이트합니다.  

```python
from azureml.automl.core.forecasting_parameters import ForecastingParameters

forecast_parameters = ForecastingParameters(time_column_name='day_datetime', 
                                            forecast_horizon=50,
                                            short_series_handling_configuration='auto',
                                            freq = 'H',
                                            target_lags='auto')
```
다음 표에는 `short_series_handling_config`에 사용할 수 있는 설정이 요약되어 있습니다.
 
|설정|Description
|---|---
|`auto`| 다음은 짧은 계열 처리의 기본 동작입니다. <li> *모든 계열이 짧은 경우* 데이터를 패딩합니다. <br> <li> *모든 계열이 짧은 것은 아닌 경우* 짧은 계열을 삭제합니다. 
|`pad`| `short_series_handling_config = pad`인 경우 자동화된 ML은 찾아낸 각 짧은 계열에 임의 값을 추가합니다. 다음 목록에는 열 유형과 채워지는 항목이 나와 있습니다. <li>NaN으로 채워지는 개체 열 <li> 0으로 채워지는 숫자 열 <li> False로 채워지는 부울/논리 열 <li> 대상 열은 평균이 0이고 표준 편차가 1인 임의 값으로 채워집니다. 
|`drop`| `short_series_handling_config = drop`인 경우 자동화된 ML이 짧은 계열을 삭제하여 학습 또는 예측에 사용되지 않습니다. 이러한 계열에 대한 예측은 NaN을 반환합니다.
|`None`| 채워지거나 삭제된 계열이 없음

>[!WARNING]
>채우기는 모델 결과의 정확도에 영향을 줄 수 있는데 이는 이전 학습을 오류 없이 가져오려고 인공 데이터를 도입하고 있기 때문입니다. <br> <br> 많은 계열이 짧은 경우 설명 가능성 결과에 어느 정도 영향이 있을 수 있습니다.

## <a name="run-the-experiment"></a>실험 실행 

`AutoMLConfig` 개체가 준비되면 실험을 제출할 수 있습니다. 모델이 완료된 후 최상의 실행 반복을 검색합니다.

```python
ws = Workspace.from_config()
experiment = Experiment(ws, "Tutorial-automl-forecasting")
local_run = experiment.submit(automl_config, show_output=True)
best_run, fitted_model = local_run.get_output()
``` 
 
## <a name="forecasting-with-best-model"></a>최적 모델로 예측

최상의 모델 반복을 사용하여 테스트 데이터 세트의 값을 예측합니다.

[forecast_quantiles()](/python/api/azureml-train-automl-client/azureml.train.automl.model_proxy.modelproxy#forecast-quantiles-x-values--typing-any--y-values--typing-union-typing-any--nonetype----none--forecast-destination--typing-union-typing-any--nonetype----none--ignore-data-errors--bool---false-----azureml-data-abstract-dataset-abstractdataset) 함수는 일반적으로 분류 및 회귀 작업에 사용되는 `predict()` 메서드와 달리 예측이 시작되어야 하는 시기를 지정할 수 있습니다. 기본적으로 forecast_quantiles() 메서드는 요소 예측 또는 평균/중앙값 예측을 생성하며, 이 예측에서는 시간이 지나며 불확실성이 점차 감소하는 현상(Cone of Uncertainty)이 나타나지 않습니다. 

다음 예제에서는 먼저 `y_pred`의 모든 값을 `NaN`으로 바꿉니다. 이 경우에는 예측 원점이 학습 데이터의 끝에 오게 됩니다. 그러나 `y_pred`의 두 번째 절반을 `NaN`으로 바꾸면 이 함수는 처음 절반의 숫자 값을 수정하지 않은 상태로 유지하지만 두 번째 절반의 `NaN` 값을 예측합니다. 이 함수는 예측된 값과 정렬된 기능을 모두 반환합니다.

`forecast_quantiles()` 함수에서 `forecast_destination` 매개 변수를 사용하여 지정된 날짜까지의 값을 예측할 수도 있습니다.

```python
label_query = test_labels.copy().astype(np.float)
label_query.fill(np.nan)
label_fcst, data_trans = fitted_model.forecast_quantiles(
    test_data, label_query, forecast_destination=pd.Timestamp(2019, 1, 8))
```

종종 고객은 분포의 특정 분위수에서 예측을 이해하려고 합니다. 예를 들어 식료품 품목 또는 클라우드 서비스용 가상 머신과 같은 인벤토리를 제어하는 ​​데 예측을 사용하는 경우가 있습니다. 이 경우 제어점은 일반적으로 “품목이 재고가 있고 99%가 소진되지 않기를 원합니다”와 같은 것입니다. 다음은 50번째 백분위수 또는 95번째 백분위수와 같이 예측을 위해 보고 싶은 분위수를 지정하는 방법을 보여 줍니다. 앞서 언급한 코드 예제와 같이 분위수를 지정하지 않으면 50번째 백분위수 예측만 생성됩니다. 

```python
# specify which quantiles you would like 
fitted_model.quantiles = [0.05,0.5, 0.9]
fitted_model.forecast_quantiles(
    test_data, label_query, forecast_destination=pd.Timestamp(2019, 1, 8))
```
 
`actual_labels` 실제 값과 `predict_labels`의 예측 값 사이에서 RMSE(제곱 평균 오차)를 계산합니다.

```python
from sklearn.metrics import mean_squared_error
from math import sqrt

rmse = sqrt(mean_squared_error(actual_labels, predict_labels))
rmse
```
 
 
전체적인 모델 정확도를 확인했으므로 가장 현실적인 다음 단계는 모델을 사용하여 알 수 없는 미래 가치를 예측하는 것입니다. 

테스트 세트 `test_data`와 형식은 같지만 날짜/시간이 미래인 데이터 세트를 제공하면 결과 예측 세트는 각 시계열 단계에 대해 예측된 값입니다. 데이터 세트의 마지막 시계열 레코드가 2018년 12월 31일에 대한 것이라고 가정합니다. 다음 날(또는 예측해야 하는 `forecast_horizon` 이하 기간)의 수요를 예측하려면 2019년 1월 1일의 각 매장에 대한 단일 시계열 레코드를 만듭니다.

```output
day_datetime,store,week_of_year
01/01/2019,A,1
01/01/2019,A,1
```

필요한 단계를 반복하여 이 미래 데이터를 데이터 프레임에 로드하고 `best_run.forecast_quantiles(test_data)`를 실행하여 미래 가치를 예측합니다.

> [!NOTE]
> `target_lags` 및/또는 `target_rolling_window_size`를 사용하도록 설정한 경우 자동화된 ML을 사용한 예측에서 샘플 내 예측은 지원되지 않습니다.

## <a name="forecasting-at-scale"></a>대규모 예측 

단일 기계 학습 모델이 부족하고 여러 기계 학습 모델이 필요한 시나리오가 있습니다. 예를 들어 브랜드의 각 개별 매장에 대한 판매를 예측하거나 개별 사용자에게 환경을 조정합니다. 각 인스턴스에 대한 모델을 빌드하면 많은 기계 학습 문제에 대한 결과가 향상될 수 있습니다. 

그룹화는 그룹별 개별 모델을 학습하기 위해 결합할 수 있는 Time Series 예측의 개념입니다. 이 방법은 다른 엔터티의 기록 또는 추세를 활용할 수 있는 그룹의 다듬기, 채우기 또는 엔터티가 필요한 계열이 있는 경우에 특히 유용할 수 있습니다. 많은 모델 및 계층적 시간 계열 예측은 이러한 대규모 예측 시나리오에 대한 자동화된 기계 학습을 통해 구동되는 솔루션입니다. 

### <a name="many-models"></a>많은 모델

자동화된 기계 학습을 Azure Machine Learning 많은 모델 솔루션을 사용하면 수백만 개의 모델을 병렬로 학습시키고 관리할 수 있습니다. 많은 모델 솔루션 가속기는 Azure Machine Learning 파이프라인을 활용하여 모델을 [학습합니다.](concept-ml-pipelines.md) 특히 [Pipeline](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline%28class%29) 개체 및 [ParalleRunStep이](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.parallelrunstep) 사용되며 [ParallelRunConfig](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.parallelrunconfig)를 통해 설정된 특정 구성 매개 변수가 필요합니다. 


다음 다이어그램은 여러 모델 솔루션에 대한 워크플로를 보여 주는 다이어그램입니다. 

![많은 모델 개념 다이어그램](./media/how-to-auto-train-forecast/many-models.svg)

다음 코드에서는 사용자가 여러 모델을 실행하도록 설정해야 하는 주요 매개 변수를 보여 제공합니다.

```python
from azureml.train.automl.runtime._many_models.many_models_parameters import ManyModelsTrainParameters

partition_column_names = ['Store', 'Brand']
automl_settings = {"task" : 'forecasting',
                   "primary_metric" : 'normalized_root_mean_squared_error',
                   "iteration_timeout_minutes" : 10, #This needs to be changed based on the dataset. Explore how long training is taking before setting this value 
                   "iterations" : 15,
                   "experiment_timeout_hours" : 1,
                   "label_column_name" : 'Quantity',
                   "n_cross_validations" : 3,
                   "time_column_name": 'WeekStarting',
                   "max_horizon" : 6,
                   "track_child_runs": False,
                   "pipeline_fetch_max_batch_size": 15,}

mm_paramters = ManyModelsTrainParameters(automl_settings=automl_settings, partition_column_names=partition_column_names)

```

### <a name="hierarchical-time-series-forecasting"></a>계층적 시간 계열 예측

대부분의 애플리케이션에서 고객은 비즈니스의 매크로 및 마이크로 수준에서 예측을 이해해야 합니다. 서로 다른 지리적 위치에서 제품의 판매를 예측하는지 또는 회사의 여러 조직에 대한 예상 인력 수요를 이해하는지 여부입니다. 계층 데이터를 지능적으로 예측하도록 기계 학습 모델을 학습시키는 기능은 필수적입니다. 

계층적 계열은 각 고유 계열이 지리 또는 제품 유형과 같은 차원에 따라 계층 구조로 정렬되는 구조입니다. 다음 예제에서는 계층 구조를 형성하는 고유한 특성이 있는 데이터를 보여 주는 예제입니다. 계층 구조는 다음과 같은 제품 유형(예: 장난감 또는 태블릿, 제품 유형을 액세서리 및 디바이스로 분할하는 제품 범주, 제품이 판매되는 지역)으로 정의됩니다. 

![계층적 데이터에 대한 원시 데이터 테이블 예제](./media/how-to-auto-train-forecast/hierarchy-data-table.svg)
 
이를 추가로 시각화하기 위해 계층의 리프 수준에는 특성 값의 고유한 조합이 포함된 모든 계열이 포함됩니다. 계층 구조의 상위 수준 각각은 계열을 정의하기 위해 하나의 더 작은 차원을 고려하고 하위 수준에서 부모 노드로 각 자식 노드 집합을 집계합니다.
 
![데이터에 대한 계층 시각적 개체](./media/how-to-auto-train-forecast/data-tree.svg)

계층적 Time Series 솔루션은 다대다 모델 솔루션을 기반으로 하며 비슷한 구성 설정을 공유합니다.

다음 코드에서는 계층적 Time Series 예측 실행을 설정하는 주요 매개 변수를 보여 줍니다. 

```python

from azureml.train.automl.runtime._hts.hts_parameters import HTSTrainParameters

model_explainability = True

engineered_explanations = False # Define your hierarchy. Adjust the settings below based on your dataset.
hierarchy = ["state", "store_id", "product_category", "SKU"]
training_level = "SKU"# Set your forecast parameters. Adjust the settings below based on your dataset.
time_column_name = "date"
label_column_name = "quantity"
forecast_horizon = 7


automl_settings = {"task" : "forecasting",
                   "primary_metric" : "normalized_root_mean_squared_error",
                   "label_column_name": label_column_name,
                   "time_column_name": time_column_name,
                   "forecast_horizon": forecast_horizon,
                   "hierarchy_column_names": hierarchy,
                   "hierarchy_training_level": training_level,
                   "track_child_runs": False,
                   "pipeline_fetch_max_batch_size": 15,
                   "model_explainability": model_explainability,# The following settings are specific to this sample and should be adjusted according to your own needs.
                   "iteration_timeout_minutes" : 10,
                   "iterations" : 10,
                   "n_cross_validations": 2}

hts_parameters = HTSTrainParameters(
    automl_settings=automl_settings,
    hierarchy_column_names=hierarchy,
    training_level=training_level,
    enable_engineered_explanations=engineered_explanations
)
```

## <a name="example-notebooks"></a>노트북 예제

다음을 포함하는 고급 예측 구성의 자세한 코드 예제는 [예측 샘플 Notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning)을 참조하세요.

* [휴일 검색 및 기능화](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-bike-share/auto-ml-forecasting-bike-share.ipynb)
* [이동 원본 교차 유효성 검사](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)
* [구성 가능한 지연](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-bike-share/auto-ml-forecasting-bike-share.ipynb)
* [롤링 기간 집계 기능](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)
* [DNN](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-beer-remote/auto-ml-forecasting-beer-remote.ipynb)

## <a name="next-steps"></a>다음 단계

* [모델 배포 방법 및 위치](how-to-deploy-and-where.md)에 대해 자세히 알아봅니다.
* [해석력: 자동화된 Machine Learning의 모델 설명(미리 보기)](how-to-machine-learning-interpretability-automl.md)에 대해 알아봅니다. 
* 자동화된 Machine Learning으로 실험을 만드는 종단 간 예제는 [자습서: 학습 회귀 모델](tutorial-auto-train-models.md)를 참조하세요.
