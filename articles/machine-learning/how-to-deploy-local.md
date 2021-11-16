---
title: 로컬로 실행하고 배포하는 방법
titleSuffix: Azure Machine Learning
description: 이 문서에서는 Azure Machine Learning에서 만든 모델의 학습, 디버깅 또는 배포를 위해 로컬 컴퓨터를 대상으로 사용하는 방법을 설명합니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: laobri
author: lobrien
ms.date: 11/20/2020
ms.topic: how-to
ms.custom: deploy
ms.openlocfilehash: 97b8d6f32b2e59dd2e19b1d41b4fa01f97158325
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132547689"
---
# <a name="deploy-models-trained-with-azure-machine-learning-on-your-local-machines"></a>로컬 컴퓨터에서 Azure Machine Learning을 사용하여 학습된 모델 배포 

이 문서에서는 Azure Machine Learning에서 만든 모델의 학습 또는 배포를 위해 로컬 컴퓨터를 대상으로 사용하는 방법을 설명합니다. Azure Machine Learning은 대부분의 Python 기계 학습 프레임워크에서 작동할 수 있을 만큼 유연합니다. 기계 학습 솔루션에는 일반적으로 복제하기 어려울 수 있는 복잡한 종속성이 있습니다. 이 문서에서는 사용 편의성과 전체 제어의 균형을 조정하는 방법을 보여 줍니다.

로컬 배포에 대한 시나리오는 다음과 같습니다.

* 프로젝트 초기에 데이터, 스크립트 및 모델을 빠르게 반복
* 이후 단계에서 디버깅 및 문제 해결
* 사용자 관리 하드웨어에 최종 배포

## <a name="prerequisites"></a>사전 요구 사항

- Azure Machine Learning 작업 영역 자세한 내용은 [Azure Machine Learning 작업 영역 만들기](how-to-manage-workspace.md)를 참조하세요.
- 모델 및 환경. 학습된 모델이 없는 경우 [이 자습서](tutorial-train-models-with-aml.md)에서 제공하는 모델 및 종속성 파일을 사용할 수 있습니다.
- [Python용 Azure Machine Learning SDK](/python/api/overview/azure/ml/intro).
- Anaconda 또는 Miniconda와 같은 conda 관리자(Azure Machine Learning 패키지 종속성을 미러링하려는 경우).
- Docker(Azure Machine Learning 환경의 컨테이너화된 버전을 사용하려는 경우).

## <a name="prepare-your-local-machine"></a>로컬 머신 준비

Azure Machine Learning 모델을 로컬로 실행하는 가장 안정적인 방법은 Docker 이미지를 사용하는 것입니다. Docker 이미지는 하드웨어 문제를 제외하고 Azure 실행 환경을 복제하는 격리 및 컨테이너화된 환경을 제공합니다. 개발 시나리오를 위해 Docker를 설치하고 구성하는 방법에 대한 자세한 내용은 [Windows에서 Docker 원격 개발 개요](/windows/dev-environment/docker/overview)를 참조하세요.

Docker에서 실행 중인 프로세스에 디버거를 연결할 수 있습니다. ([실행 중인 컨테이너에 연결](https://code.visualstudio.com/docs/remote/attach-container)을 참조하세요.) 그러나 Docker를 사용하지 않고 Python 코드를 디버그하고 반복하는 것이 좋습니다. 이 시나리오에서 로컬 컴퓨터는 Azure Machine Learning에서 실험을 실행할 때 사용되는 것과 동일한 라이브러리를 사용하는 것이 중요합니다. Azure는 Python 종속성을 관리하기 위해 [conda](https://docs.conda.io/)를 사용합니다. 다른 패키지 관리자를 사용하여 환경을 다시 만들 수 있지만, 로컬 컴퓨터에 conda를 설치하고 구성하는 것이 가장 쉬운 동기화 방법입니다. 

## <a name="prepare-your-entry-script"></a>항목 스크립트 준비

Docker를 사용하여 모델 및 종속성을 관리하는 경우에도 Python 채점 스크립트는 로컬이어야 합니다. 스크립트에는 두 가지 메서드가 있어야 합니다.

- 인수를 사용하지 않고 아무것도 반환하지 않는 `init()` 메서드 
- JSON 형식의 문자열을 사용하고 JSON 직렬화 가능 개체를 반환하는 `run()` 메서드

`run()` 메서드에 대한 인수는 다음과 같은 형식입니다. 

```json
{
    "data": <model-specific-data-structure>
}
```

`run()` 메서드에서 반환하는 개체는 `toJSON() -> string`을 구현해야 합니다.

다음 예제에서는 등록된 scikit-learn 모델을 로드하고 NumPy 데이터를 사용하여 채점하는 방법을 보여 줍니다. 이 예제는 [이 자습서](tutorial-train-models-with-aml.md)의 모델 및 종속성을 기반으로 합니다.

```python
import json
import numpy as np
import os
import pickle
import joblib

def init():
    global model
    # AZUREML_MODEL_DIR is an environment variable created during deployment.
    # It's the path to the model folder (./azureml-models/$MODEL_NAME/$VERSION).
    # For multiple models, it points to the folder containing all deployed models (./azureml-models).
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_mnist_model.pkl')
    model = joblib.load(model_path)

def run(raw_data):
    data = np.array(json.loads(raw_data)['data'])
    # Make prediction.
    y_hat = model.predict(data)
    # You can return any data type as long as it's JSON-serializable.
    return y_hat.tolist()
```

자동 Swagger 스키마 생성 및 채점 이진 데이터(예: 이미지)를 포함한 고급 예제는 [고급 항목 스크립트 작성](how-to-deploy-advanced-entry-script.md)을 참조하세요. 

## <a name="deploy-as-a-local-web-service-by-using-docker"></a>Docker를 사용하여 로컬 웹 서비스로 배포

Azure Machine Learning에서 사용하는 환경을 복제하는 가장 쉬운 방법은 Docker를 사용하여 웹 서비스를 배포하는 것입니다. 로컬 컴퓨터에서 실행되는 Docker를 사용하면 다음을 수행할 수 있습니다.

1. 모델이 등록된 Azure Machine Learning 작업 영역에 연결합니다.
1. 매개 변수를 나타내는 `Model` 개체를 만듭니다.
1. 종속성을 포함하는 `Environment` 개체를 만들고 코드가 실행될 소프트웨어 환경을 정의합니다.
1. 항목 스크립트를 `Environment`와 연결하는 `InferenceConfig` 개체를 만듭니다.
1. 하위 클래스 `LocalWebserviceDeploymentConfiguration`의 `DeploymentConfiguration` 개체를 만듭니다.
1. `Model.deploy()`를 사용하여 `Webservice` 개체를 만듭니다. 이 메서드는 Docker 이미지를 다운로드하고 `Model`, `InferenceConfig` 및 `DeploymentConfiguration`에 연결합니다.
1. `Webservice`를 사용하여 `Webservice.wait_for_deployment()`를 활성화합니다.

다음은 이러한 단계를 보여 주는 코드입니다.

```python
from azureml.core.webservice import Webservice
from azureml.core.model import InferenceConfig
from azureml.core.environment import Environment
from azureml.core import Workspace
from azureml.core.model import Model

ws = Workspace.from_config()
model = Model(ws, 'sklearn_mnist')


myenv = Environment.get(workspace=ws, name="tutorial-env", version="1")
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)

deployment_config = LocalWebservice.deploy_configuration(port=6789)

local_service = Model.deploy(workspace=ws, 
                       name='sklearn-mnist-local', 
                       models=[model], 
                       inference_config=inference_config, 
                       deployment_config = deployment_config)

local_service.wait_for_deployment(show_output=True)
print(f"Scoring URI is : {local_service.scoring_uri}")
```

`Model.deploy()`를 호출하는 데 몇 분 정도 걸릴 수 있습니다. 웹 서비스를 처음 배포한 후에는 처음부터 다시 시작하는 것보다 `update()` 메서드를 사용하는 것이 더 효율적입니다. [배포된 웹 서비스 업데이트](how-to-deploy-update-web-service.md)를 참조하세요.

### <a name="test-your-local-deployment"></a>로컬 배포 테스트

이전 배포 스크립트를 실행하면 채점을 위해 데이터를 게시할 수 있는 URI가 출력됩니다(예: `http://localhost:6789/score`). 다음 샘플에서는 로컬로 배포된 모델 `"sklearn-mnist-local"`을 사용하여 샘플 데이터를 채점하는 스크립트를 보여 줍니다. 모델이 제대로 학습된 경우 `normalized_pixel_values`가 "2"로 해석되어야 한다는 것을 유추합니다. 

```python
import requests

normalized_pixel_values = "[\
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.5, 0.5, 0.7, 1.0, 1.0, 0.6, 0.4, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.7, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 0.9, 0.1, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.7, 1.0, 1.0, 1.0, 0.8, 0.6, 0.7, 1.0, 1.0, 0.5, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.2, 1.0, 1.0, 0.8, 0.1, 0.0, 0.0, 0.0, 0.8, 1.0, 0.5, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.3, 1.0, 0.8, 0.1, 0.0, 0.0, 0.0, 0.5, 1.0, 1.0, 0.3, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.1, 0.1, 0.0, 0.0, 0.0, 0.0, 0.8, 1.0, 1.0, 0.3, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.5, 1.0, 1.0, 0.8, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.3, 1.0, 1.0, 0.9, 0.2, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.5, 1.0, 1.0, 0.6, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.7, 1.0, 1.0, 0.6, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.1, 0.9, 1.0, 0.9, 0.1, \
0.1, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.8, 1.0, 1.0, 0.6, \
0.6, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.3, 1.0, 1.0, 0.7, \
0.7, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.1, 0.8, 1.0, 1.0, \
1.0, 0.6, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.5, 1.0, 1.0, \
1.0, 0.7, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 1.0, 1.0, \
1.0, 1.0, 0.1, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 1.0, \
1.0, 1.0, 1.0, 0.2, 0.1, 0.1, 0.1, 0.1, 0.0, 0.0, 0.0, 0.1, 0.1, 0.1, 0.6, 0.6, 0.6, 0.6, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 0.7, 0.6, 0.7, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 0.5, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.7, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 0.7, 0.5, 0.5, 0.2, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.5, 0.5, 0.5, 0.5, 0.7, 1.0, 1.0, 1.0, 0.6, 0.5, 0.5, 0.2, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0]"

input_data = "{\"data\": [" + normalized_pixel_values + "]}"

headers = {'Content-Type': 'application/json'}

scoring_uri = "http://localhost:6789/score"
resp = requests.post(scoring_uri, input_data, headers=headers)

print("Should be predicted as '2'")
print("prediction:", resp.text)
```

## <a name="download-and-run-your-model-directly"></a>모델을 직접 다운로드하고 실행

Docker를 사용하여 모델을 웹 서비스로 배포하는 것이 가장 일반적인 옵션입니다. 그러나 로컬 Python 스크립트를 사용하여 코드를 직접 실행할 수 있습니다. 다음과 같은 두 가지 중요한 구성 요소가 필요합니다. 

- 모델 자체
- 모델이 의존하는 종속성 

다음과 같은 방법으로 모델을 다운로드할 수 있습니다.  

- 포털에서 **모델** 탭을 선택하고 원하는 모델을 선택한 다음, **세부 정보** 페이지에서 **다운로드** 를 선택합니다.
- 명령줄에서 `az ml model download`를 사용합니다. ([모델 다운로드](/cli/azure/ml/model#az_ml_model_download) 참조)
- Python SDK `Model.download()` 메서드를 사용합니다. ([모델 클래스](/python/api/azureml-core/azureml.core.model.model#download-target-dir------exist-ok-false--exists-ok-none-) 참조)

Azure 모델은 프레임워크에서 사용하는 모든 형식일 수 있지만 일반적으로 Python pickle 파일(.pkl 확장명)로 패키지된 하나 이상의 직렬화된 Python 개체입니다. pickle 파일의 내용은 기계 학습 라이브러리나 모델 학습에 사용되는 기법에 따라 달라집니다. 예를 들어 자습서에서 모델을 사용하는 경우 다음을 사용하여 모델을 로드할 수 있습니다.

```python
import pickle

with open('sklearn_mnist_model.pkl', 'rb') as f : 
    logistic_model = pickle.load(f, encoding='latin1')
```

종속성은 항상 바로잡기가 까다롭습니다. 특히 기계 학습을 사용하는 경우에는 특정 버전 요구 사항이 복잡하게 얽혀 있는 경우가 많기 때문에 더 그렇습니다. `Environment` 클래스의 `build_local()` 메서드를 사용하여 로컬 컴퓨터의 Azure Machine Learning 환경을 전체 conda 환경 또는 Docker 이미지로 다시 만들 수 있습니다. 

```python
ws = Workspace.from_config()
myenv = Environment.get(workspace=ws, name="tutorial-env", version="1")
myenv.build_local(workspace=ws, useDocker=False) #Creates conda environment.
```

`build_local()` `useDocker` 인수를 `True`로 설정하면 이 함수가 conda 환경이 아닌 Docker 이미지를 만듭니다. 더 세부적인 제어를 원하는 경우 `Environment`의 `save_to_directory()` 메서드를 사용할 수 있습니다. 이 메서드는 미세 조정하고 확장 기준으로 사용할 수 있는 conda_dependencies.yml 및 azureml_environment.json 정의 파일을 작성합니다. 

`Environment` 클래스에는 컴퓨팅 하드웨어, Azure 작업 영역 및 Docker 이미지에서 환경을 동기화하는 데 사용할 수 있는 다른 여러 메서드가 있습니다. 자세한 내용은 [환경 클래스](/python/api/azureml-core/azureml.core.environment(class))를 참조하세요.

모델을 다운로드하고 해당 종속성을 해결한 후에는 채점 수행, 모델 미세 조정, 전이 학습 사용 방법 등에 대한 Azure 정의 제한 사항이 없습니다. 

## <a name="upload-a-retrained-model-to-azure-machine-learning"></a>Azure Machine Learning에 학습된 모델 업로드

로컬로 학습되거나 재학습된 모델이 있는 경우 Azure에 등록할 수 있습니다. 등록한 후에는 Azure 컴퓨팅을 사용하여 계속 튜닝하거나 [Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md) 또는 [Triton Inference Server(미리 보기)](how-to-deploy-with-triton.md)와 같은 Azure 기능을 사용하여 배포할 수 있습니다.

Azure Machine Learning Python SDK와 함께 사용하려면 모델을 pickle 형식(.pkl 파일)으로 직렬화된 Python 개체로 저장해야 합니다. 또한 JSON 직렬화 가능 개체를 반환하는 `predict(data)` 메서드를 구현해야 합니다. 예를 들어 다음을 사용하여 로컬로 학습된 scikit-learn 당뇨병 모델을 저장할 수 있습니다. 

```python
import joblib

from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge

dataset_x, dataset_y = load_diabetes(return_X_y=True)

sk_model = Ridge().fit(dataset_x, dataset_y)

joblib.dump(sk_model, "sklearn_regression_model.pkl")
```

Azure에서 모델을 사용할 수 있도록 하려면 `Model` 클래스의 `register()` 메서드를 사용합니다.

```python
from azureml.core.model import Model

model = Model.register(model_path="sklearn_regression_model.pkl",
                       model_name="sklearn_regression_model",
                       tags={'area': "diabetes", 'type': "regression"},
                       description="Ridge regression model to predict diabetes",
                       workspace=ws)
```

그런 다음, Azure Machine Learning **모델** 탭에서 새로 등록된 모델을 찾을 수 있습니다.

:::image type="content" source="media/how-to-deploy-local/registered-model.png" alt-text="업로드된 모델을 보여 주는 Azure Machine Learning 모델 탭의 스크린샷.":::

모델 및 환경을 업로드하고 업데이트하는 방법에 대한 자세한 내용은 [모델을 등록하고 고급 사용법으로 로컬에 배포](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/deploy-to-local/register-model-deploy-local-advanced.ipynb)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- Azure Machine Learning VS Code 사용에 대한 자세한 내용은 Visual Studio Code 컴퓨팅 [인스턴스에 커넥트(미리 보기)를 참조하세요.](how-to-set-up-vs-code-remote.md)
- 환경을 관리하는 방법에 대한 자세한 내용은 [Azure Machine Learning에서 소프트웨어 환경 만들기 및 사용](how-to-use-environments.md)을 참조하세요.
- 데이터 저장소에서 데이터에 액세스하는 방법에 대해 알아보려면 [Azure에서 스토리지 서비스에 연결](how-to-access-data.md)을 참조하세요.
