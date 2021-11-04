---
title: 자동화된 Machine Learning 설명(미리 보기)
titleSuffix: Azure Machine Learning
description: 자동화된 Machine Learning 모델이 기능 중요도를 결정하고 Azure Machine Learning SDK를 사용할 때 예측하는 방법에 대한 설명을 가져오는 방법에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: automl
ms.topic: how-to
ms.custom: automl, responsible-ml
ms.author: mithigpe
author: minthigpen
ms.date: 10/21/2021
ms.openlocfilehash: 6538ebfbb7aae0a8e2b97c4b41a124568db858c7
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131562307"
---
# <a name="interpretability-model-explanations-in-automated-machine-learning-preview"></a>해석력: 자동화된 Machine Learning의 모델 설명(미리 보기)


이 글에서는 Python SDK를 사용하여 Azure Machine Learning에서 자동화된 Machine Learning(자동화된 ML)에 대한 설명을 가져오는 방법에 대해 알아봅니다. 자동화된 ML은 생성된 모델의 기능 중요도를 이해하는 데 도움이 됩니다. 

1\.0.85 이후의 모든 SDK 버전은 기본적으로 `model_explainability=True`로 설정됩니다. SDK 버전 1.0.85 및 이전 버전에서 사용자는 모델 해석력을 사용하기 위해 `AutoMLConfig` 개체에 `model_explainability=True`을 설정해야 합니다. 


이 문서에서는 다음 방법을 설명합니다.

- 최적 모델 또는 모든 모델에 대한 학습 중에 해석력을 수행합니다.
- 시각화를 사용하면 데이터 및 설명에서 패턴을 확인할 수 있습니다.
- 유추 또는 채점 중 해석력을 구현합니다.

## <a name="prerequisites"></a>사전 요구 사항

- 해석력 기능입니다. `pip install azureml-interpret`을 실행하여 필요한 패키지를 가져옵니다.
- 자동화된 ML 실험 빌드에 대한 지식입니다. Azure Machine Learning SDK 사용 방법에 대한 자세한 내용은 이 [회귀 모델 자습서](tutorial-auto-train-models.md)를 완료하거나 [자동화된 ML 실험 구성 방법](how-to-configure-auto-train.md)을 참조하세요.

## <a name="interpretability-during-training-for-the-best-model"></a>최적 모델에 대한 학습 중의 해석력

원시 및 엔지니어링된 기능에 대한 설명이 포함된 `best_run`에서 설명을 검색합니다.

> [!NOTE]
> 해석력, 모델 설명은 자동화된 ML 예측 실험에서 권장하는 TCNForecaster 모델에 사용할 수 없습니다.

### <a name="download-the-engineered-feature-importances-from-the-best-run"></a>최적 실행에서 엔지니어링된 기능 중요도 다운로드

`ExplanationClient`을 사용하여 `best_run`의 아티팩트 저장소에서 엔지니어링된 기능 설명을 다운로드 할 수 있습니다. 

```python
from azureml.interpret import ExplanationClient

client = ExplanationClient.from_run(best_run)
engineered_explanations = client.download_model_explanation(raw=False)
print(engineered_explanations.get_feature_importance_dict())
```

### <a name="download-the-raw-feature-importances-from-the-best-run"></a>최적 실행에서 원시 기능 중요도 다운로드

`ExplanationClient`을 사용하여 `best_run`의 아티팩트 저장소에서 원시 기능 설명을 다운로드 할 수 있습니다.

```python
from azureml.interpret import ExplanationClient

client = ExplanationClient.from_run(best_run)
raw_explanations = client.download_model_explanation(raw=True)
print(raw_explanations.get_feature_importance_dict())
```

## <a name="interpretability-during-training-for-any-model"></a>모든 모델에 대한 학습 중의 해석력 

모델 설명을 계산하고 시각화 할 때 자동화된 Machine Learning 모델에 대한 기존 모델의 설명에만 국한되지 않습니다. 다른 테스트 데이터로 모델에 대한 설명을 볼 수도 있습니다. 이 섹션의 단계에서는 테스트 데이터를 기반으로 엔지니어링된 기능 중요도를 계산하고 시각화 하는 방법을 보여줍니다.

### <a name="retrieve-any-other-automl-model-from-training"></a>학습에서 다른 자동화된 Machine Learning 모델 검색

```python
automl_run, fitted_model = local_run.get_output(metric='accuracy')
```

### <a name="set-up-the-model-explanations"></a>모델 설명 설정

`automl_setup_model_explanations`을 사용하여 엔지니어링된 설명 및 원시 설명을 가져옵니다. `fitted_model`은 다음과 같은 항목을 생성할 수 있습니다:

- 학습된 샘플 또는 테스트 샘플의 기능 데이터
- 엔지니어링된 기능 이름 목록
- 분류 시나리오의 레이블이 지정된 열에서 찾을 수 있는 클래스

`automl_explainer_setup_obj`은 위 목록의 모든 구조를 포함합니다.

```python
from azureml.train.automl.runtime.automl_explain_utilities import automl_setup_model_explanations

automl_explainer_setup_obj = automl_setup_model_explanations(fitted_model, X=X_train, 
                                                             X_test=X_test, y=y_train, 
                                                             task='classification')
```

### <a name="initialize-the-mimic-explainer-for-feature-importance"></a>기능 중요도에 대한 모방 설명자 초기화

자동화된 ML 모델에 대한 설명을 생성하려면 `MimicWrapper` 클래스를 사용합니다. 다음 매개 변수를 사용하여 MimicWrapper를 초기화할 수 있습니다:

- 설명자 설정 개체
- 작업 영역
- 자동화된 ML 모델 `fitted_model`을 설명하는 서로게이트 모델

MimicWrapper는 엔지니어링된 설명이 업로드 될 `automl_run` 개체도 가져옵니다.

```python
from azureml.interpret import MimicWrapper

# Initialize the Mimic Explainer
explainer = MimicWrapper(ws, automl_explainer_setup_obj.automl_estimator,
                         explainable_model=automl_explainer_setup_obj.surrogate_model, 
                         init_dataset=automl_explainer_setup_obj.X_transform, run=automl_run,
                         features=automl_explainer_setup_obj.engineered_feature_names, 
                         feature_maps=[automl_explainer_setup_obj.feature_map],
                         classes=automl_explainer_setup_obj.classes,
                         explainer_kwargs=automl_explainer_setup_obj.surrogate_model_params)
```

### <a name="use-mimic-explainer-for-computing-and-visualizing-engineered-feature-importance"></a>모방 설명자를 사용하여 엔지니어링된 기능 중요도를 계산 및 시각화

변환된 테스트 샘플을 사용하여 MimicWrapper에서 `explain()` 메서드를 호출하여 만들어진 엔지니어링된 기능에 대한 기능 중요도를 가져올 수 있습니다. 또한 [Azure Machine Learning Studio](https://ml.azure.com/)에 로그인하여 자동화된 ML 기능화를 통해 생성한 엔지니어링된 기능의 기능 중요도 값에 대한 설명 대시보드 시각화를 확인할 수 있습니다.

```python
engineered_explanations = explainer.explain(['local', 'global'], eval_dataset=automl_explainer_setup_obj.X_test_transform)
print(engineered_explanations.get_feature_importance_dict())
```
자동화된 Machine Learning을 사용하여 학습된 모델의 경우 `get_output()` 메서드를 사용하여 최적의 모델을 가져오고 로컬에서 설명을 계산할 수 있습니다.  `raiwidgets` 패키지의 `ExplanationDashboard`로 설명 결과를 시각화할 수 있습니다.

```python
best_run, fitted_model = remote_run.get_output()

from azureml.train.automl.runtime.automl_explain_utilities import AutoMLExplainerSetupClass, automl_setup_model_explanations
automl_explainer_setup_obj = automl_setup_model_explanations(fitted_model, X=X_train,
                                                             X_test=X_test, y=y_train,
                                                             task='regression')

from interpret.ext.glassbox import LGBMExplainableModel
from azureml.interpret.mimic_wrapper import MimicWrapper

explainer = MimicWrapper(ws, automl_explainer_setup_obj.automl_estimator, LGBMExplainableModel,
                         init_dataset=automl_explainer_setup_obj.X_transform, run=best_run,
                         features=automl_explainer_setup_obj.engineered_feature_names,
                         feature_maps=[automl_explainer_setup_obj.feature_map],
                         classes=automl_explainer_setup_obj.classes)
                         
pip install interpret-community[visualization]

engineered_explanations = explainer.explain(['local', 'global'], eval_dataset=automl_explainer_setup_obj.X_test_transform)
print(engineered_explanations.get_feature_importance_dict()),
from raiwidgets import ExplanationDashboard
ExplanationDashboard(engineered_explanations, automl_explainer_setup_obj.automl_estimator, datasetX=automl_explainer_setup_obj.X_test_transform)

 

raw_explanations = explainer.explain(['local', 'global'], get_raw=True,
                                     raw_feature_names=automl_explainer_setup_obj.raw_feature_names,
                                     eval_dataset=automl_explainer_setup_obj.X_test_transform)
print(raw_explanations.get_feature_importance_dict()),
from raiwidgets import ExplanationDashboard
ExplanationDashboard(raw_explanations, automl_explainer_setup_obj.automl_pipeline, datasetX=automl_explainer_setup_obj.X_test_raw)
```

### <a name="use-mimic-explainer-for-computing-and-visualizing-raw-feature-importance"></a>모방 설명자를 사용하여 원시 기능 중요도 계산 및 시각화

변환된 테스트 샘플을 사용하여 MimicWrapper에서 `explain()` 메서드를 호출하여 원시 기능에 대한 기능 중요도를 가져올 수 있습니다. [Machine Learning Studio](https://ml.azure.com/)에서 원시 기능의 기능 중요도 값에 대한 대시보드 시각화를 볼 수 있습니다.

```python
raw_explanations = explainer.explain(['local', 'global'], get_raw=True,
                                     raw_feature_names=automl_explainer_setup_obj.raw_feature_names,
                                     eval_dataset=automl_explainer_setup_obj.X_test_transform,
                                     raw_eval_dataset=automl_explainer_setup_obj.X_test_raw)
print(raw_explanations.get_feature_importance_dict())
```

## <a name="interpretability-during-inference"></a>유추 중의 해석력

이 섹션에서는 이전 섹션의 설명을 계산하는 데 사용된 설명자로 자동화된 ML 모델을 운영하는 방법을 알아봅니다.

### <a name="register-the-model-and-the-scoring-explainer"></a>모델 및 채점 설명자 등록

`TreeScoringExplainer`을 사용하여 유추 시 엔지니어링된 기능 중요도 값을 계산할 채점 설명자를 만듭니다. 이전에 계산된 `feature_map`로 채점 설명자를 초기화합니다. 

채점 설명자를 저장한 후 모델 및 채점 설명자를 모델 관리 서비스에 등록합니다. 다음 코드를 실행하세요.

```python
from azureml.interpret.scoring.scoring_explainer import TreeScoringExplainer, save

# Initialize the ScoringExplainer
scoring_explainer = TreeScoringExplainer(explainer.explainer, feature_maps=[automl_explainer_setup_obj.feature_map])

# Pickle scoring explainer locally
save(scoring_explainer, exist_ok=True)

# Register trained automl model present in the 'outputs' folder in the artifacts
original_model = automl_run.register_model(model_name='automl_model', 
                                           model_path='outputs/model.pkl')

# Register scoring explainer
automl_run.upload_file('scoring_explainer.pkl', 'scoring_explainer.pkl')
scoring_explainer_model = automl_run.register_model(model_name='scoring_explainer', model_path='scoring_explainer.pkl')
```

### <a name="create-the-conda-dependencies-for-setting-up-the-service"></a>서비스 설정을 위한 Conda 종속성 만들기

그런 다음 배포된 모델 컨테이너에 필요한 환경 종속성을 만듭니다. 1\.0.45 버전 이상의 azureml-defaults는 모델을 웹 서비스로 호스트하는데 필요한 기능을 포함하므로 pip 종속성으로 나열되어야 합니다.

```python
from azureml.core.conda_dependencies import CondaDependencies

azureml_pip_packages = [
    'azureml-interpret', 'azureml-train-automl', 'azureml-defaults'
]

myenv = CondaDependencies.create(conda_packages=['scikit-learn', 'pandas', 'numpy', 'py-xgboost<=0.80'],
                                 pip_packages=azureml_pip_packages,
                                 pin_sdk_version=True)

with open("myenv.yml","w") as f:
    f.write(myenv.serialize_to_string())

with open("myenv.yml","r") as f:
    print(f.read())

```

### <a name="create-the-scoring-script"></a>채점 스크립트 만들기

모델을 로드하고 새 일괄 처리 데이터를 기반으로 예측과 설명을 생성하는 스크립트를 작성합니다.

```python
%%writefile score.py
import joblib
import pandas as pd
from azureml.core.model import Model
from azureml.train.automl.runtime.automl_explain_utilities import automl_setup_model_explanations


def init():
    global automl_model
    global scoring_explainer

    # Retrieve the path to the model file using the model name
    # Assume original model is named automl_model
    automl_model_path = Model.get_model_path('automl_model')
    scoring_explainer_path = Model.get_model_path('scoring_explainer')

    automl_model = joblib.load(automl_model_path)
    scoring_explainer = joblib.load(scoring_explainer_path)


def run(raw_data):
    data = pd.read_json(raw_data, orient='records')
    # Make prediction
    predictions = automl_model.predict(data)
    # Setup for inferencing explanations
    automl_explainer_setup_obj = automl_setup_model_explanations(automl_model,
                                                                 X_test=data, task='classification')
    # Retrieve model explanations for engineered explanations
    engineered_local_importance_values = scoring_explainer.explain(automl_explainer_setup_obj.X_test_transform)
    # Retrieve model explanations for raw explanations
    raw_local_importance_values = scoring_explainer.explain(automl_explainer_setup_obj.X_test_transform, get_raw=True)
    # You can return any data type as long as it is JSON-serializable
    return {'predictions': predictions.tolist(),
            'engineered_local_importance_values': engineered_local_importance_values,
            'raw_local_importance_values': raw_local_importance_values}
```

### <a name="deploy-the-service"></a>서비스 배포

Conda 파일 및 이전 단계의 채점 파일을 사용하여 서비스를 배포합니다.

```python
from azureml.core.webservice import Webservice
from azureml.core.webservice import AciWebservice
from azureml.core.model import Model, InferenceConfig
from azureml.core.environment import Environment

aciconfig = AciWebservice.deploy_configuration(cpu_cores=1,
                                               memory_gb=1,
                                               tags={"data": "Bank Marketing",  
                                                     "method" : "local_explanation"},
                                               description='Get local explanations for Bank marketing test data')
myenv = Environment.from_conda_specification(name="myenv", file_path="myenv.yml")
inference_config = InferenceConfig(entry_script="score_local_explain.py", environment=myenv)

# Use configs and models generated above
service = Model.deploy(ws,
                       'model-scoring',
                       [scoring_explainer_model, original_model],
                       inference_config,
                       aciconfig)
service.wait_for_deployment(show_output=True)
```

### <a name="inference-with-test-data"></a>테스트 데이터를 사용한 유추

일부 테스트 데이터로 유추하여 현재 Azure Machine Learning SDK에서만 지원되는 자동화된 Machine Learning 모델의 예측 값을 확인합니다. 예측 값에 기여하는 기능 중요도를 확인합니다. 

```python
if service.state == 'Healthy':
    # Serialize the first row of the test data into json
    X_test_json = X_test[:1].to_json(orient='records')
    print(X_test_json)
    # Call the service to get the predictions and the engineered explanations
    output = service.run(X_test_json)
    # Print the predicted value
    print(output['predictions'])
    # Print the engineered feature importances for the predicted value
    print(output['engineered_local_importance_values'])
    # Print the raw feature importances for the predicted value
    print('raw_local_importance_values:\n{}\n'.format(output['raw_local_importance_values']))
```

## <a name="visualize-to-discover-patterns-in-data-and-explanations-at-training-time"></a>학습 시간에 데이터 및 설명의 패턴을 발견하도록 시각화

[Azure Machine LearningStudio](https://ml.azure.com)의 작업 영역에서 기능 중요도 차트를 시각화 할 수 있습니다. 자동화된 Machine Learning 실행이 완료되면 **모델 세부 정보 보기** 를 선택하여 특정 실행을 확인할 수 있습니다. 설명 대시보드에서 시각화를 보려면 **설명** 탭을 선택합니다.

[![Machine Learning 해석력 아키텍처](./media/how-to-machine-learning-interpretability-automl/automl-explanation.png)](./media/how-to-machine-learning-interpretability-automl/automl-explanation.png#lightbox)

설명 대시보드 시각화 및 특정 플롯에 대한 자세한 내용은 [해석력에 대한 방법 문서](how-to-machine-learning-interpretability-aml.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

자동화된 Machine Learning 이외의 Azure Machine Learning SDK 영역에서 모델 설명 및 기능 중요도를 사용하도록 설정하는 방법에 대한 자세한 내용은 [해석력에 대한 개념 문서](how-to-machine-learning-interpretability.md)를 참조하세요.
