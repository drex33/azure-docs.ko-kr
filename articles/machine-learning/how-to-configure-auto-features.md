---
title: 자동화된 Machine Learning 기능화
titleSuffix: Azure Machine Learning
description: Azure Machine Learning의 데이터 기능화 설정과 자동화된 ML 실험을 위해 해당 기능을 사용자 지정하는 방법을 알아봅니다.
author: nibaccam
ms.author: nibaccam
ms.reviewer: nibaccam
services: machine-learning
ms.service: machine-learning
ms.subservice: automl
ms.topic: how-to
ms.custom: automl,contperf-fy21q2
ms.date: 10/21/2021
ms.openlocfilehash: 5dcd0f5fffd485f49225bae31ad81a74bdb1cde3
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131559108"
---
# <a name="data-featurization-in-automated-machine-learning"></a>자동화된 Machine Learning 데이터 기능화

Azure Machine Learning의 데이터 기능화 설정과 [자동화된 Machine Learning 실험](concept-automated-ml.md)을 위해 해당 기능을 사용자 지정하는 방법을 알아봅니다.

## <a name="feature-engineering-and-featurization"></a>기능 엔지니어링 및 기능화

학습 데이터는 행과 열로 이루어져 있습니다. 각 행은 관찰 또는 레코드이며 각 행의 열은 각 레코드를 설명하는 기능입니다. 일반적으로 데이터의 패턴을 가장 잘 특징화 하는 기능을 선택하여 예측 모델을 만듭니다.

많은 원시 데이터 필드를 직접 사용하여 모델을 학습할 수 있지만 데이터 패턴을 더 잘 구분하는 정보를 제공하는 추가 기능(엔지니어링됨)을 만들어야 하는 경우가 많습니다. 이 프로세스를 **기능 엔지니어링** 이라고 하며 이를 통해 데이터의 도메인 지식을 활용하여 기계 학습 알고리즘이 더 잘 학습하게 하는 기능을 만듭니다. 

Azure Machine Learning에서는 데이터 크기 조정 및 정규화 기술이 적용되어 기능 엔지니어링이 더 쉬워집니다. 해당 기술과 이 기능 엔지니어링을 자동화된 Machine Learning 실험에서의 **기능화** 로 통칭합니다.

## <a name="prerequisites"></a>사전 요구 사항

이 문서에서는 자동화된 Machine Learning 실험을 구성하는 방법을 이미 알고 있다고 가정합니다. 

[!INCLUDE [automl-sdk-version](../../includes/machine-learning-automl-sdk-version.md)]

구성에 대한 자세한 내용은 다음 문서를 참조하세요:

- 코드 우선 환경: [Python용 Azure Machine Learning SDK를 사용하여 자동화된 Machine Learning 실험을 구성합니다](how-to-configure-auto-train.md).
- 하위 코드 또는 코드 없는 환경: [Azure Machine Learning Studio를 사용하여 자동화된 기계 학습 모델을 만들고, 검토 및 배포합니다](how-to-use-automated-ml-for-ml-models.md).

## <a name="configure-featurization"></a>기능화 구성

모든 자동화된 Machine Learning 실험에서 [자동 크기 조정 및 정규화 기술](#featurization)이 기본적으로 데이터에 적용됩니다. 이러한 기술은 기능화 유형으로서 다양한 규모에서의 기능에 민감한 *특정* 알고리즘을 지원합니다. *누락 값 대체*, *인코딩* 및 *변환* 과 같은 더 많은 기능화를 사용하도록 설정할 수 있습니다.

> [!NOTE]
> 자동화된 Machine Learning 기능화 단계(예: 기능 정규화, 누락 데이터 처리, 텍스트를 숫자로 변환)는 기본 모델의 일부가 됩니다. 예측에 모델을 사용하는 경우 학습 중에 적용되는 동일한 기능화 단계가 입력 데이터에 자동으로 적용됩니다.

Python SDK로 구성하는 실험의 경우 기능화 설정을 사용하도록 설정하거나 사용하지 않도록 설정하고 실험에 사용할 기능화 단계를 추가로 지정할 수 있습니다. Azure Machine Learning 스튜디오를 사용하는 경우 [기능화를 사용하도록 설정하는 단계](how-to-use-automated-ml-for-ml-models.md#customize-featurization)를 참조하세요.

다음 표는 [AutoMLConfig 클래스](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig)에서 `featurization`에 대해 허용되는 설정을 보여줍니다:

|기능화 구성 | Description|
------------- | ------------- |
|`"featurization": 'auto'`| 전처리의 일부로 [데이터 가드 레일](#data-guardrails) 및 [기능화 단계](#featurization)가 자동으로 수행되도록 지정합니다. 이 설정은 기본값입니다.|
|`"featurization": 'off'`| 기능화 단계가 자동으로 수행되지 않도록 지정합니다.|
|`"featurization":`&nbsp;`'FeaturizationConfig'`| 사용자 지정된 기능화 단계를 사용하도록 지정합니다. [기능화를 사용자 지정하는 방법을 알아보세요](#customize-featurization).|

<a name="featurization"></a>

## <a name="automatic-featurization"></a>자동 기능화

다음 표에는 데이터에 자동으로 적용되는 기술이 요약되어 있습니다. 해당 기술은 SDK 또는 스튜디오를 사용하여 구성된 실험에 적용됩니다. 이 동작을 사용하지 않도록 설정하려면 `AutoMLConfig` 개체에서 `"featurization": 'off'`을 설정합니다.

> [!NOTE]
> 자동화된 Machine Learning로 만든 모델을 [ONNX 모델](concept-onnx.md)로 내보내려는 경우 별표 ("*")로 표시된 기능화 옵션만 ONNX 형식으로 지원됩니다. [모델을 ONNX로 변환](how-to-use-automl-onnx-model-dotnet.md)하는 방법에 대해 자세히 알아보세요.

|기능화&nbsp;단계| Description |
| ------------- | ------------- |
|**높은 카디널리티 삭제 또는 분산 기능 없음*** |학습 및 유효성 검사 집합에서 해당 기능을 삭제합니다. 모든 값이 누락되었거나, 모든 행에서 동일한 값을 사용하거나, 카디널리티가 높은 기능(예: 해시, ID 또는 GUID)에 적용됩니다.|
|**누락 값 대체*** |숫자 기능의 경우, 열 값의 평균으로 대체합니다.<br/><br/>범주 별 기능의 경우 가장 자주 사용되는 값으로 대체합니다.|
|**더 많은 기능 생성** _ |DateTime 기능: 연도, 월, 일, 요일, 연간 일자, 분기, 연간 주, 시간, 분, 초<br><br> _예측 작업의 경우* 다음과 같은 추가 DateTime 기능이 생성됩니다: ISO 연도, 반년, 달력 개월 문자열, 주, 요일 문자열, 분기, 연간, AM/PM(시간이 정오(오후 12시) 이전인 경우 0, 그렇지 않으면 1), AM/PM 문자열, 시간(12시간 기준)<br/><br/>텍스트 기능: 유니그램, 바이그램 및 트라이그램 기반 용어 빈도입니다. [BERT를 사용하여 수행하는 방법](#bert-integration)에 대해 자세히 알아보세요.|
|**변환 및 인코딩***|고유 값이 거의 없는 숫자 특성을 범주 특성으로 변환합니다.<br/><br/>단일 핫 인코딩은 낮은 카디널리티 범주 기능에 사용됩니다. 단일 핫 해시 인코딩은 높은 카디널리티 범주 기능에 사용됩니다.|
|**단어 포함**|텍스트 기능화기는 미리 학습된 모델을 사용하여 텍스트 토큰의 벡터를 문장 벡터로 변환합니다. 문서에 포함된 각 단어의 벡터는 문서 기능 벡터를 생성하기 위해 나머지와 함께 집계됩니다.|
|**클러스터 거리**|모든 숫자 열에서 k-평균 클러스터링 모델을 학습시킵니다. 각 샘플에서 각 클러스터의 중심까지의 거리를 포함하는 *k* 개의 새 기능(클러스터당 하나의 새 숫자 기능)을 생성합니다.|

## <a name="data-guardrails"></a>데이터 가드 레일

*데이터 가드 레일* 을 사용하면 데이터의 잠재적 문제(예: 누락 값 또는 [클래스 불균형](concept-manage-ml-pitfalls.md#identify-models-with-imbalanced-data))를 식별할 수 있습니다. 또한 향상된 결과를 위해 정정 작업을 수행할 수 있습니다.

데이터 가드 레일이 적용됩니다:

- **SDK 실험용**: `AutoMLConfig` 개체에 `"featurization": 'auto'` 또는 `validation=auto` 매개 변수가 지정된 경우입니다.
- **스튜디오 실험용**: 자동 기능화를 사용하도록 설정한 경우입니다.

실험에 대한 데이터 가드 레일을 검토할 수 있습니다:

- SDK를 사용하여 실험을 제출할 때 `show_output=True`을 설정합니다.

- 스튜디오에서 자동화된 Machine Learning 실행의 **데이터 가드 레일** 탭입니다.

### <a name="data-guardrail-states"></a>데이터 가드 레일 상태

데이터 가드 레일에는 다음 세 가지 상태 중 하나가 표시됩니다:

|주| Description |
|----|---- |
|**통과**| 데이터 문제가 검색되지 않았으며 사용자 작업이 필요하지 않습니다. |
|**완료**| 변경 내용이 데이터에 적용되었습니다. 자동화된 Machine Learning에서 수행된 정정 작업을 검토하여 변경 내용이 예상 결과와 일치하는지 확인하기를 권장합니다. |
|**알림**| 데이터 문제가 검색되었지만 해결할 수 없습니다. 사용자가 문제를 수정하고 해결하기를 권장합니다.|

### <a name="supported-data-guardrails"></a>지원되는 데이터 가드 레일

다음 표에서는 현재 지원되는 데이터 가드 레일과 실험을 제출할 때 볼 수 있는 관련 상태를 설명합니다:

가드 레일|상태|트리거 조건
---|---|---
**누락된 기능 값 대체** |통과 <br><br><br> 완료| 학습 데이터에서 누락된 기능 값이 검색되지 않았습니다. [누락 값 대체](./how-to-use-automated-ml-for-ml-models.md#customize-featurization)에 대해 자세히 알아보세요. <br><br> 학습 데이터에서 누락된 기능 값이 검색되어 대체되었습니다.
**높은 카디널리티 기능 처리** |통과 <br><br><br> 완료| 입력이 분석되었으며, 높은 카디널리티 기능이 검색되지 않았습니다. <br><br> 입력에서 높은 카디널리티 기능이 검색되어 처리되었습니다.
**유효성 검사 분할 처리** |완료| 유효성 검사 구성이 `'auto'`로 설정되었으며, 학습 데이터에는 *20,000개 미만의 행* 이 포함되었습니다. <br> 교차 유효성 검사를 통해 학습된 모델의 각 반복을 검사했습니다. [유효성 검사 데이터](./how-to-configure-auto-train.md#training-validation-and-test-data)에 대해 자세히 알아보세요. <br><br> 유효성 검사 구성이 `'auto'`로 설정되었으며, 학습 데이터에는 *20,000개 이상의 행* 이 포함되었습니다. <br> 모델의 유효성 검사를 위해 입력 데이터가 학습 데이터 세트와 유효성 검사 데이터 세트로 분할되었습니다.
**클래스 분산 검색** |통과 <br><br><br><br>알림 <br><br><br>완료 | 입력이 분석되었으며, 학습 데이터에서 모든 클래스가 균형을 유지하고 있습니다. 샘플의 수와 비율로 측정할 때 각 클래스가 데이터 세트에서 올바르게 표현되어 있으면 데이터 세트가 균형을 유지하고 있는 것으로 간주됩니다. <br><br> 입력에서 불균형 클래스가 검색되었습니다. 모델 편차를 수정하려면 분산 문제를 수정합니다. [불균형 데이터](./concept-manage-ml-pitfalls.md#identify-models-with-imbalanced-data)에 대해 자세히 알아보세요.<br><br> 입력에서 불균형 클래스가 검색되었으며 비우기 논리가 분산을 적용하도록 결정되었습니다.
**메모리 문제 검색** |통과 <br><br><br><br> 완료 |<br> 선택한 값(예측 시간, 지연 시간 및 롤링 기간)이 분석되었으며, 잠재적인 메모리 부족 문제가 검색되지 않았습니다. 시계열 [예측 구성](./how-to-auto-train-forecast.md#configuration-settings)에 대해 자세히 알아보세요. <br><br><br>선택한 값(예측 시간, 지연 시간 및 롤링 기간)이 분석되었으며, 실험에서 메모리가 부족할 수 있습니다. 지연 시간 및 롤링 기간 구성이 해제되었습니다.
**빈도 검색** |통과 <br><br><br><br> 완료 |<br> 시계열이 분석되었으며, 모든 데이터 요소가 검색된 빈도로 정렬되어 있습니다. <br> <br> 시계열이 분석되었으며 검색된 빈도와 일치하지 않는 데이터 요소가 검색되었습니다. 데이터 세트에서 이러한 데이터 요소가 제거되었습니다. [시계열 예측을 위한 데이터 준비](./how-to-auto-train-forecast.md#preparing-data)에 대해 자세히 알아보세요.

## <a name="customize-featurization"></a>기능화 사용자 지정

또한 Machine Learning 모델을 학습시키는 데 사용되는 데이터와 기능이 관련 있는 예측 결과를 내도록 기능화 설정을 사용자 지정할 수 있습니다.

기능화를 사용자 지정하려면 `"featurization": FeaturizationConfig`를 `AutoMLConfig` 개체에 지정합니다. 실험에 Azure Machine Learning 스튜디오를 사용하는 경우 [방법 문서](how-to-use-automated-ml-for-ml-models.md#customize-featurization)를 참조합니다. 예측 작업 유형 기능화를 사용자 지정하려면 [예측 방법](how-to-auto-train-forecast.md#customize-featurization)을 참조합니다.

지원되는 사용자 지정은 다음과 같습니다.

|사용자 지정|정의|
|--|--|
|**열 용도 업데이트**|지정된 열에 대해 자동 감지된 기능 유형을 재정의합니다.|
|**변환기 매개 변수 업데이트** |지정된 변환기의 매개 변수를 업데이트합니다. 현재는 *Imputer*(평균, 가장 빈번 및 중앙값) 및 *HashOneHotEncoder* 를 지원합니다.|
|**삭제 열** |기능화에서 삭제할 열을 지정합니다.|
|**블록 변환기**| 기능화 프로세스에 사용할 블록 변환기를 지정합니다.|

>[!NOTE]
> **열 삭제** 기능은 SDK 버전 1.19부터 더 이상 사용되지 않습니다. 자동화된 ML 실험에서 사용하기 전에 데이터 정리의 일부로 데이터 세트에서 열을 삭제합니다. 

API 호출을 사용하여 `FeaturizationConfig`개체를 만듭니다:

```python
featurization_config = FeaturizationConfig()
featurization_config.blocked_transformers = ['LabelEncoder']
featurization_config.drop_columns = ['aspiration', 'stroke']
featurization_config.add_column_purpose('engine-size', 'Numeric')
featurization_config.add_column_purpose('body-style', 'CategoricalHash')
#default strategy mean, add transformer param for for 3 columns
featurization_config.add_transformer_params('Imputer', ['engine-size'], {"strategy": "median"})
featurization_config.add_transformer_params('Imputer', ['city-mpg'], {"strategy": "median"})
featurization_config.add_transformer_params('Imputer', ['bore'], {"strategy": "most_frequent"})
featurization_config.add_transformer_params('HashOneHotEncoder', [], {"number_of_bits": 3})
```

## <a name="featurization-transparency"></a>기능화 투명도

모든 자동화된 Machine Learning 모델은 기능화가 자동으로 적용됩니다.  기능화에는 자동화된 기능 엔지니어링(`"featurization": 'auto'`일 때)과 크기 조정 및 정규화가 포함되며 선택한 알고리즘과 해당 하이퍼 매개 변수 값에 영향을 줍니다. 자동화된 Machine Learning은 모델에 적용된 항목을 확인할 수 있도록 다양한 방법을 지원합니다.

다음 예측 예제를 고려합니다:

+ 입력 기능으로 A(숫자), B(숫자), C(숫자), D(날짜/시간)가 있습니다.
+ 숫자 기능 C는 모든 값이 고유한 ID 열 이므로 삭제됩니다.
+ 숫자 기능 A와 B에는 누락된 값이 있으므로 평균으로 귀속됩니다.
+ 날짜/시간 기능 D는 엔지니어링된 기능 11개로 기능화됩니다.

이 정보를 얻으려면 자동화된 Machine Learning 실험 실행의 `fitted_model` 출력을 사용합니다.

```python
automl_config = AutoMLConfig(…)
automl_run = experiment.submit(automl_config …)
best_run, fitted_model = automl_run.get_output()
```
### <a name="automated-feature-engineering"></a>자동화된 기능 엔지니어링 
`get_engineered_feature_names()`은 엔지니어링된 기능 이름 목록을 반환합니다.

  >[!Note]
  >'예측' 작업에는 'timeseriestransformer'를 사용하고, '회귀' 또는 '분류' 작업에는 'datatransformer'를 사용합니다.

  ```python
  fitted_model.named_steps['timeseriestransformer']. get_engineered_feature_names ()
  ```

이 목록에는 엔지니어링된 기능 이름이 모두 포함됩니다. 

  ```
  ['A', 'B', 'A_WASNULL', 'B_WASNULL', 'year', 'half', 'quarter', 'month', 'day', 'hour', 'am_pm', 'hour12', 'wday', 'qday', 'week']
  ```

`get_featurization_summary()`은 모든 입력 기능의 기능화 요약을 가져옵니다.

  ```python
  fitted_model.named_steps['timeseriestransformer'].get_featurization_summary()
  ```

출력

  ```
  [{'RawFeatureName': 'A',
    'TypeDetected': 'Numeric',
    'Dropped': 'No',
    'EngineeredFeatureCount': 2,
    'Tranformations': ['MeanImputer', 'ImputationMarker']},
   {'RawFeatureName': 'B',
    'TypeDetected': 'Numeric',
    'Dropped': 'No',
    'EngineeredFeatureCount': 2,
    'Tranformations': ['MeanImputer', 'ImputationMarker']},
   {'RawFeatureName': 'C',
    'TypeDetected': 'Numeric',
    'Dropped': 'Yes',
    'EngineeredFeatureCount': 0,
    'Tranformations': []},
   {'RawFeatureName': 'D',
    'TypeDetected': 'DateTime',
    'Dropped': 'No',
    'EngineeredFeatureCount': 11,
    'Tranformations': ['DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime']}]
  ```

   |출력|정의|
   |----|--------|
   |RawFeatureName|제공된 데이터 세트의 입력 기능/열 이름입니다.|
   |TypeDetected|입력 기능의 데이터 형식이 감지되었습니다.|
   |Dropped|입력 기능이 삭제되었는지 아니면 사용되었는지 여부를 나타냅니다.|
   |EngineeringFeatureCount|자동화된 기능 엔지니어링 변환을 통해 생성된 기능의 수입니다.|
   |변환|엔지니어링된 기능을 생성하기 위해 입력 기능에 적용된 변환 목록입니다.|

### <a name="scaling-and-normalization"></a>스케일링 및 정규화

크기 조정/정규화 및 하이퍼 매개 변수 값으로 선택한 알고리즘을 이해하려면 `fitted_model.steps`을 사용합니다. 

선택한 실행에 대해 `fitted_model.steps`을 실행하는 경우 샘플 출력은 다음과 같습니다:

```
[('RobustScaler', 
  RobustScaler(copy=True, 
  quantile_range=[10, 90], 
  with_centering=True, 
  with_scaling=True)), 

  ('LogisticRegression', 
  LogisticRegression(C=0.18420699693267145, class_weight='balanced', 
  dual=False, 
  fit_intercept=True, 
  intercept_scaling=1, 
  max_iter=100, 
  multi_class='multinomial', 
  n_jobs=1, penalty='l2', 
  random_state=None, 
  solver='newton-cg', 
  tol=0.0001, 
  verbose=0, 
  warm_start=False))
```

자세한 내용을 보려면 다음 도우미 함수를 사용하세요. 

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

이 도우미 함수는 `LogisticRegression with RobustScalar`를 특정 알고리즘으로 사용하는 특정 실행에 대해 다음 출력을 반환합니다.

```
RobustScaler
{'copy': True,
'quantile_range': [10, 90],
'with_centering': True,
'with_scaling': True}

LogisticRegression
{'C': 0.18420699693267145,
'class_weight': 'balanced',
'dual': False,
'fit_intercept': True,
'intercept_scaling': 1,
'max_iter': 100,
'multi_class': 'multinomial',
'n_jobs': 1,
'penalty': 'l2',
'random_state': None,
'solver': 'newton-cg',
'tol': 0.0001,
'verbose': 0,
'warm_start': False}
```

### <a name="predict-class-probability"></a>클래스 확률 예측

자동화된 ML을 사용하여 생성되는 모든 모델은 해당 오픈 소스 클래스의 기능을 미러링하는 래퍼 개체를 갖고 있습니다. 자동화된 ML에서 반환하는 대부분의 분류 모델 래퍼 개체는 기능(X 값)의 배열 형식 또는 스파스 행렬 데이터 샘플을 허용하는 `predict_proba()` 함수를 구현하고, 각 샘플의 n차원 배열 및 해당 클래스 확률을 반환합니다.

위에서 동일한 호출을 사용하여 가장 적합한 실행과 맞춤 모델을 검색했다면 맞춤 모델에서 직접 `predict_proba()`를 호출하고 모델 유형에 맞는 적절한 형식으로 `X_test`를 입력할 수 있습니다.

```python
best_run, fitted_model = automl_run.get_output()
class_prob = fitted_model.predict_proba(X_test)
```

기본 모델이 `predict_proba()` 함수를 지원하지 않거나 형식이 잘못된 경우 모델 클래스 관련 예외가 throw됩니다. 이 함수를 다른 모델 유형에 맞게 구현하는 방법의 예제는 [RandomForestClassifier](https://scikit-learn.org/stable/modules/generated/sklearn.ensemble.RandomForestClassifier.html#sklearn.ensemble.RandomForestClassifier.predict_proba) 및 [XGBoost](https://xgboost.readthedocs.io/en/latest/python/python_api.html) 참조 문서에서 확인할 수 있습니다.

<a name="bert-integration"></a>

## <a name="bert-integration-in-automated-ml"></a>자동화된 Machine Learning에 BERT 통합

[BERT](https://techcommunity.microsoft.com/t5/azure-ai/how-bert-is-integrated-into-azure-automated-machine-learning/ba-p/1194657)는 자동화된 Machine Learning의 기능화 레이어에서 사용됩니다. 이 레이어에서 열에 자유 텍스트 또는 타임 스탬프 또는 단순 숫자와 같은 다른 유형의 데이터가 포함된 경우 그에 따라 기능화가 적용됩니다.

BERT의 경우 모델은 사용자가 제공한 레이블을 사용하여 미세 조정되고 학습됩니다. 여기에서 문서 포함은 타임 스탬프 기반 기능, 요일 등 다른 기능과 함께 기능으로 출력됩니다. 


### <a name="steps-to-invoke-bert"></a>BERT 호출 단계

BERT를 호출하려면 automl_settings에서 `enable_dnn: True`을 설정하고 GPU 컴퓨팅(`vm_size = "STANDARD_NC6"` 또는 더 높은 GPU)을 사용합니다. CPU 계산을 사용하는 경우 BERT 대신 자동화된 Machine Learning이 BiLSTM DNN 기능화기를 사용하도록 설정합니다.

자동화된 Machine Learning은 BERT에 대해 다음 단계를 수행합니다. 

1. **모든 text 열의 전처리 및 토큰화** 입니다. 예를 들어 "StringCast" 변환기는 최종 모델의 기능화 요약에서 찾을 수 있습니다. 모델의 기능화 요약을 생성하는 방법에 대한 예제는 [이 Notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-text-dnn/auto-ml-classification-text-dnn.ipynb)에서 찾을 수 있습니다.

2. **모든 텍스트 열을 단일 텍스트 열** 로 연결하기 때문에 따라서 최종 모델의 `StringConcatTransformer`입니다. 

    BERT 구현에서는 학습 샘플의 총 텍스트 길이를 128토큰으로 제한합니다. 즉, 연결될 때 모든 텍스트 열 길이는 이상적으로 최대 128토큰이어야 합니다. 여러 열이 있는 경우 이 조건이 충족되도록 각 열을 정리해야 합니다. 그렇지 않으면 BERT의 토크나이저 레이어가 128토큰을 초과하는 길이의 연결된 열에 대하여 해당 입력을 128개 토큰으로 자릅니다.

3. **기능 비우기의 일환으로 자동화된 Machine Learning은 BERT를 데이터 샘플의 기준선(단어 모음 기능)과 비교합니다.** 이 비교는 BERT에서 정확성을 향상시킬 수 있는지 여부를 결정합니다. BERT가 기준선보다 더 나은 성능을 제공하는 경우 자동화된 Machine Learning은 전체 데이터의 텍스트 기능화에 BERT를 사용합니다. 이 경우 최종 모델에 `PretrainedTextDNNTransformer`이 표시됩니다.

BERT는 일반적으로 다른 기능화기보다 오래 실행됩니다. 성능을 향상시키려면 RDMA 기능에 "STANDARD_NC24r" or "STANDARD_NC24rs_V3"을 사용하기를 권장합니다. 

자동화된 Machine Learning은 BERT 학습을 사용할 수 있는 경우 여러 노드에 배포합니다(최대 8개 노드). `max_concurrent_iterations` 매개 변수를 1보다 높게 설정하여 `AutoMLConfig` 개체에서 수행할 수 있습니다. 

## <a name="supported-languages-for-bert-in-automl"></a>자동화된 Machine Learning에서 BERT에 지원되는 언어 

자동화된 Machine Learning은 현재 약 100개의 언어를 지원하며 데이터 세트의 언어에 따라 자동화된 Machine Learning은 적절한 BERT 모델을 선택합니다. 독일어 데이터의 경우 독일어 BERT 모델을 사용합니다. 영어의 경우 영어 BERT 모델을 사용합니다. 다른 모든 언어의 경우 다국어 BERT 모델을 사용합니다.

다음 코드에서는 데이터 세트 언어가 [ISO 분류](https://iso639-3.sil.org/code/deu)에 따라 독일어의 세 글자 언어 코드인 `deu`로 지정되었으므로 독일어 BERT 모델이 트리거됩니다:

```python
from azureml.automl.core.featurization import FeaturizationConfig

featurization_config = FeaturizationConfig(dataset_language='deu')

automl_settings = {
    "experiment_timeout_minutes": 120,
    "primary_metric": 'accuracy', 
# All other settings you want to use 
    "featurization": featurization_config,
    
  "enable_dnn": True, # This enables BERT DNN featurizer
    "enable_voting_ensemble": False,
    "enable_stack_ensemble": False
}
```

## <a name="next-steps"></a>다음 단계

* 자동화된 Machine Learning 실험을 설정하는 방법에 대해 알아봅니다:

    * 코드 우선 환경: [Azure Machine Learning SDK를 사용하여 자동화된 Machine Learning 실험을 구성합니다](how-to-configure-auto-train.md).
    * 하위 코드 또는 코드가 없는 환경: [Azure Machine Learning Studio에서 자동화된 Machine Learning 실험을 만듭니다](how-to-use-automated-ml-for-ml-models.md).

* [모델 배포 방법 및 위치](how-to-deploy-and-where.md)에 대해 자세히 알아봅니다.

* [자동화된 Machine Learning을 사용하여 회귀 모델 학습 방법](tutorial-auto-train-models.md) 또는 [원격 리소스에서 자동화된 Machine Learning을 사용한 학습 방법](concept-automated-ml.md#local-remote)에 대해 자세히 알아보세요.
