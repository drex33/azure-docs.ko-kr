---
title: '자습서: Notebook을 사용하여 예측 모델 만들기(2-1부)'
titleSuffix: Azure Machine Learning
description: Jupyter Notebook의 코드를 사용하여 기계 학습 모델을 빌드하고 배포하는 방법을 알아봅니다. 또한 Microsoft Power BI에 쉽게 통합할 수 있도록 입력 및 출력을 정의하는 채점 스크립트를 만듭니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: samkemp
author: samuel100
ms.reviewer: sdgilley
ms.date: 12/11/2020
ms.openlocfilehash: 6e71116d79496340c306628a29504aa140e9d5f5
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/04/2021
ms.locfileid: "129429911"
---
# <a name="tutorial-power-bi-integration---create-the-predictive-model-with-a-jupyter-notebook-part-1-of-2"></a>자습서: Power BI 통합 - Jupyter Notebook을 사용하여 예측 모델 만들기(2-1부)

이 자습서의 1부에서는 Jupyter Notebook의 코드를 사용하여 기계 학습 예측 모델을 학습시키고 배포합니다. 또한 Power BI에 통합할 모델의 입력 및 출력 스키마를 정의하는 채점 스크립트를 만듭니다.  2부에서는 Microsoft Power BI에서 모델을 사용하여 결과를 예측합니다.

이 자습서에서는 다음과 같은 작업을 수행했습니다.

> [!div class="checklist"]
> * Jupyter Notebook 만들기
> * Azure Machine Learning 컴퓨팅 인스턴스를 만듭니다.
> * scikit-learn을 사용하여 회귀 모델을 학습시킵니다.
> * Microsoft Power BI에 쉽게 통합할 수 있도록 입력 및 출력을 정의하는 채점 스크립트를 작성합니다.
> * 실시간 채점 엔드포인트에 모델을 배포합니다.

Power BI에서 사용할 모델을 만들고 배포하는 방법은 세 가지가 있습니다.  이 문서에서는 "옵션 A: Notebook을 사용하여 모델 학습 및 배포"에 대해 설명합니다.  이 옵션은 코드 우선 작성 환경입니다. 이는 Azure Machine Learning 스튜디오에서 호스팅되는 Jupyter Notebook을 사용합니다. 

그러나 다른 옵션 중 하나를 대신 사용할 수 있습니다.

* [옵션 B: Azure Machine Learning 디자이너를 사용하여 모델 학습 및 배포](tutorial-power-bi-designer-model.md) 이는 끌어서 놓기 사용자 인터페이스를 사용하는 로우 코드 작성 환경입니다.
* [옵션 C: 자동화된 Machine Learning을 사용하여 모델을 학습시키고 배포합니다](tutorial-power-bi-automated-model.md). 이는 데이터 준비 및 모델 학습을 완전히 자동화하는 코드 없음 제작 환경입니다.


## <a name="prerequisites"></a>필수 구성 요소

- Azure 구독 구독이 아직 없는 경우 [평가판](https://azure.microsoft.com/free/)을 사용할 수 있습니다. 
- Azure Machine Learning 작업 영역 작업 영역이 아직 없는 경우 [Azure Machine Learning 작업 영역 만들기 및 관리](./how-to-manage-workspace.md#create-a-workspace)를 참조하세요.
- Python 언어 및 기계 학습 워크플로에 대한 입문 지식

## <a name="create-a-notebook-and-compute"></a>Notebook 및 컴퓨팅 만들기

[**Azure Machine Learning 스튜디오**](https://ml.azure.com) 홈페이지에서 **새로 만들기** > **Notebook** 을 선택합니다.

:::image type="content" source="media/tutorial-power-bi/create-new-notebook.png" alt-text="Notebook을 만드는 방법을 보여 주는 스크린샷":::
 
**새 파일 만들기** 페이지에서 다음을 수행합니다.

1. Notebook 이름을 지정합니다(예: *my_model_notebook*).
1. **파일 형식** 을 **Notebook** 으로 변경합니다.
1. **만들기** 를 선택합니다. 
 
그런 다음, 코드 셀을 실행하려면 컴퓨팅 인스턴스를 만들고 Notebook에 연결합니다. 먼저 Notebook의 위쪽에서 더하기 아이콘을 선택합니다.

:::image type="content" source="media/tutorial-power-bi/create-compute.png" alt-text="컴퓨팅 인스턴스를 만드는 방법을 보여 주는 스크린샷":::

**컴퓨팅 인스턴스 만들기** 페이지에서 다음을 수행합니다.

1. CPU 가상 머신 크기를 선택합니다. 이 자습서에서는 2개 코어와 14GB RAM을 사용 하는 **Standard_D11_v2** 를 선택할 수 있습니다.
1. **다음** 을 선택합니다. 
1. **설정 구성** 페이지에서 유효한 **컴퓨팅 이름** 을 지정합니다. 유효한 문자는 대문자와 소문자, 숫자 및 하이픈(-)입니다.
1. **만들기** 를 선택합니다.

Notebook에서 **컴퓨팅** 옆에 있는 원이 녹청색으로 표시될 수 있습니다. 이러한 색상 변경은 컴퓨팅 인스턴스가 생성 중임을 나타냅니다.

:::image type="content" source="media/tutorial-power-bi/creating.png" alt-text="컴퓨팅이 생성 중임을 보여 주는 스크린샷":::

> [!NOTE]
> 컴퓨팅 인스턴스를 프로비저닝하는 데 2~4분 정도 걸릴 수 있습니다.

컴퓨팅이 프로비저닝되면 Notebook을 사용하여 코드 셀을 실행할 수 있습니다. 예를 들어 셀에 다음 코드를 입력할 수 있습니다.

```python
import numpy as np

np.sin(3)
```

그런 다음, Shift+Enter를 누릅니다(또는 Ctrl+Enter를 선택하거나 셀 옆의 **재생** 단추를 선택함). 다음과 같은 출력이 표시됩니다.

:::image type="content" source="media/tutorial-power-bi/simple-sin.png" alt-text="셀 출력을 보여 주는 스크린샷":::

이제 기계 학습 모델을 빌드할 준비가 되었습니다.

## <a name="build-a-model-by-using-scikit-learn"></a>scikit-learn을 사용하여 모델 빌드

이 자습서에서는 [당뇨병 데이터 세트](https://www4.stat.ncsu.edu/~boos/var.select/diabetes.html)를 사용합니다. 이 데이터 세트는 [Azure Open Datasets](https://azure.microsoft.com/services/open-datasets/)에 있습니다.


### <a name="import-data"></a>데이터 가져오기

데이터를 가져오려면 다음 코드를 복사하여 Notebook의 새 *코드 셀* 에 붙여넣습니다.

```python
from azureml.opendatasets import Diabetes

diabetes = Diabetes.get_tabular_dataset()
X = diabetes.drop_columns("Y")
y = diabetes.keep_columns("Y")
X_df = X.to_pandas_dataframe()
y_df = y.to_pandas_dataframe()
X_df.info()
```

`X_df` pandas 데이터 프레임에는 기준 입력 변수 10개가 포함되어 있습니다. 이러한 변수에는 연령, 성별, 체질량 지수, 평균 혈압 및 6개 혈청 측정값이 포함됩니다. `y_df` pandas 데이터 프레임은 대상 변수입니다. 여기에는 기준 1년 후 당뇨병의 진행 과정을 정량적으로 측정한 값이 포함됩니다. 이 데이터 프레임에는 442개 레코드가 포함되어 있습니다.

### <a name="train-the-model"></a>모델 학습

Notebook에 새 *코드 셀* 을 만듭니다. 그런 다음, 아래 코드를 복사해서 셀에 붙여넣습니다. 이 코드 조각은 Python pickle 형식을 사용하여 릿지 회귀 모델을 생성하고 모델을 직렬화합니다.

```python
import joblib
from sklearn.linear_model import Ridge

model = Ridge().fit(X_df,y_df)
joblib.dump(model, 'sklearn_regression_model.pkl')
```

### <a name="register-the-model"></a>모델 등록

등록된 모델은 모델 파일 자체의 내용 외에 메타데이터도 저장합니다. 메타데이터에는 모델 설명, 태그 및 프레임워크 정보가 포함됩니다. 

메타데이터는 작업 영역에서 모델을 관리하고 배포하는 경우에 유용합니다. 예를 들어 태그를 사용하면 모델을 작업 영역에 나열할 때 모델을 분류하고 필터를 적용할 수 있습니다. 또한 scikit-learn 프레임워크를 사용하여 이 모델을 표시하면 웹 서비스로 배포하는 작업이 간단해집니다.

다음 코드를 복사한 후 Notebook의 새 *코드 셀* 에 붙여넣습니다.

```python
import sklearn

from azureml.core import Workspace
from azureml.core import Model
from azureml.core.resource_configuration import ResourceConfiguration

ws = Workspace.from_config()

model = Model.register(workspace=ws,
                       model_name='my-sklearn-model',                # Name of the registered model in your workspace.
                       model_path='./sklearn_regression_model.pkl',  # Local file to upload and register as a model.
                       model_framework=Model.Framework.SCIKITLEARN,  # Framework used to create the model.
                       model_framework_version=sklearn.__version__,  # Version of scikit-learn used to create the model.
                       sample_input_dataset=X,
                       sample_output_dataset=y,
                       resource_configuration=ResourceConfiguration(cpu=2, memory_in_gb=4),
                       description='Ridge regression model to predict diabetes progression.',
                       tags={'area': 'diabetes', 'type': 'regression'})

print('Name:', model.name)
print('Version:', model.version)
```

Azure Machine Learning 스튜디오에서도 모델을 볼 수 있습니다. 왼쪽 메뉴에서 **모델** 을 선택합니다.

:::image type="content" source="media/tutorial-power-bi/model.png" alt-text="모델을 보는 방법을 보여 주는 스크린샷":::

## <a name="define-the-scoring-script"></a>채점 스크립트 정의

Power BI에 통합할 모델을 배포하는 경우 Python *채점 스크립트* 및 사용자 지정 환경을 정의해야 합니다. 채점 스크립트에는 다음 두 가지 함수가 있습니다.

- `init()` 함수는 서비스가 시작될 때 실행됩니다. 이 함수는 모델을 로드하고(모델 레지스트리에서 자동으로 다운로드됨) 역직렬화합니다.
- `run(data)` 함수는 서비스에 대한 호출에 채점이 필요한 입력 데이터가 포함되어 있을 때 실행됩니다. 

>[!NOTE]
> 아래 코드의 Python 데코레이터는 입력 및 출력 데이터의 스키마를 정의합니다. 이는 Power BI로의 통합에 중요합니다.

다음 코드를 복사한 후 Notebook의 새 *코드 셀* 에 붙여넣습니다. 다음 코드 조각에는 *score.py* 이라는 파일에 코드를 기록하는 셀 매직이 있습니다.

```python
%%writefile score.py

import json
import pickle
import numpy as np
import pandas as pd
import os
import joblib
from azureml.core.model import Model

from inference_schema.schema_decorators import input_schema, output_schema
from inference_schema.parameter_types.numpy_parameter_type import NumpyParameterType
from inference_schema.parameter_types.pandas_parameter_type import PandasParameterType


def init():
    global model
    # Replace filename if needed.
    path = os.getenv('AZUREML_MODEL_DIR') 
    model_path = os.path.join(path, 'sklearn_regression_model.pkl')
    # Deserialize the model file back into a sklearn model.
    model = joblib.load(model_path)


input_sample = pd.DataFrame(data=[{
    "AGE": 5,
    "SEX": 2,
    "BMI": 3.1,
    "BP": 3.1,
    "S1": 3.1,
    "S2": 3.1,
    "S3": 3.1,
    "S4": 3.1,
    "S5": 3.1,
    "S6": 3.1
}])

# This is an integer type sample. Use the data type that reflects the expected result.
output_sample = np.array([0])

# To indicate that we support a variable length of data input,
# set enforce_shape=False
@input_schema('data', PandasParameterType(input_sample))
@output_schema(NumpyParameterType(output_sample))
def run(data):
    try:
        print("input_data....")
        print(data.columns)
        print(type(data))
        result = model.predict(data)
        print("result.....")
        print(result)
    # You can return any data type, as long as it can be serialized by JSON.
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```

### <a name="define-the-custom-environment"></a>사용자 지정 환경 정의

다음으로, 모델을 채점하는 환경을 정의합니다. 이 환경에서는 채점 스크립트(*score.py*)에 필요한 pandas 및 scikit-learn과 같은 Python 패키지를 정의합니다.

환경을 정의하려면 다음 코드를 복사하여 Notebook의 새 *코드 셀* 에 붙여넣습니다.

```python
from azureml.core.model import InferenceConfig
from azureml.core import Environment
from azureml.core.conda_dependencies import CondaDependencies

environment = Environment('my-sklearn-environment')
environment.python.conda_dependencies = CondaDependencies.create(pip_packages=[
    'azureml-defaults',
    'inference-schema[numpy-support]',
    'joblib',
    'numpy',
    'pandas',
    'scikit-learn=={}'.format(sklearn.__version__)
])

inference_config = InferenceConfig(entry_script='./score.py',environment=environment)
```

### <a name="deploy-the-model"></a>모델 배포

모델을 배포하려면 다음 코드를 복사하여 Notebook의 새 *코드 셀* 에 붙여넣습니다.

```python
service_name = 'my-diabetes-model'

service = Model.deploy(ws, service_name, [model], inference_config, overwrite=True)
service.wait_for_deployment(show_output=True)
```

>[!NOTE]
> 서비스를 배포하는 데 2~4분 정도 걸릴 수 있습니다.

서비스가 성공적으로 배포되면 다음과 같은 출력이 표시됩니다.

```txt
Tips: You can try get_logs(): https://aka.ms/debugimage#dockerlog or local deployment: https://aka.ms/debugimage#debug-locally to debug if deployment takes longer than 10 minutes.
Running......................................................................................
Succeeded
ACI service creation operation finished, operation "Succeeded"
```

Azure Machine Learning 스튜디오에서도 서비스를 볼 수 있습니다. 왼쪽 메뉴에서 **엔드포인트** 를 선택합니다.

:::image type="content" source="media/tutorial-power-bi/endpoint.png" alt-text="서비스를 보는 방법을 보여 주는 스크린샷":::

웹 서비스를 테스트하여 예상대로 작동하는지 확인하는 것이 좋습니다. Notebook을 반환하려면 Azure Machine Learning 스튜디오의 왼쪽 메뉴에서 **Notebook** 을 선택합니다. 그런 다음, 아래 코드를 복사하고 Notebook의 새 *코드 셀* 에 붙여넣어 서비스를 테스트합니다.

```python
import json

input_payload = json.dumps({
    'data': X_df[0:2].values.tolist()
})

output = service.run(input_payload)

print(output)
```

출력은 JSON 구조(`{'predict': [[205.59], [68.84]]}`)와 유사합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Microsoft Power BI에서 사용할 수 있도록 모델을 빌드하고 배포하는 방법을 살펴보았습니다. 다음 2부에서는 Power BI 보고서에서 이 모델을 사용하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [자습서: Power BI에서 모델 사용](/power-bi/connect-data/service-aml-integrate?context=azure/machine-learning/context/ml-context)
