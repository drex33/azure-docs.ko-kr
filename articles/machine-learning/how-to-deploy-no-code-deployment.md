---
title: 코드 배포 없음(미리 보기)
titleSuffix: Azure Machine Learning
description: 코드를 배포하지 않으면 수동으로 항목 스크립트를 만들지 않고도 모델을 웹 서비스로 배포할 수 있습니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.date: 07/31/2020
ms.topic: how-to
ms.custom: deploy
ms.reviewer: larryfr
ms.openlocfilehash: 8735898a462d9488aec95bf9f5840bf9b9be2bb6
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123428437"
---
# <a name="no-code-model-deployment-preview"></a>코드 없는 모델 배포(미리 보기)

코드 없는 모델 배포는 현재 미리 보기로 제공되며 다음과 같은 기계 학습 프레임워크를 지원합니다.

## <a name="tensorflow-savedmodel-format"></a>TensorFlow SavedModel 형식
코드 없는 모델 배포와 함께 작동하려면 TensorFlow 모델을 **SavedModel 형식** 으로 등록해야 합니다.

SavedModel을 만드는 방법에 대한 정보는 [이 링크](https://www.tensorflow.org/guide/saved_model)를 참조하세요.

[TensorFlow Serving DockerHub](https://registry.hub.docker.com/r/tensorflow/serving/tags)의 "Tags" 아래에 나열된 모든 TensorFlow 버전을 지원합니다.

```python
from azureml.core import Model

model = Model.register(workspace=ws,
                       model_name='flowers',                        # Name of the registered model in your workspace.
                       model_path='./flowers_model',                # Local Tensorflow SavedModel folder to upload and register as a model.
                       model_framework=Model.Framework.TENSORFLOW,  # Framework used to create the model.
                       model_framework_version='1.14.0',            # Version of Tensorflow used to create the model.
                       description='Flowers model')

service_name = 'tensorflow-flower-service'
service = Model.deploy(ws, service_name, [model])
```

## <a name="onnx-models"></a>ONNX 모델

ONNX 모델 등록 및 배포는 모든 ONNX 유추 그래프에 대해 지원됩니다. 전처리 및 후처리 단계는 현재 지원되지 않습니다.

다음은 MNIST ONNX 모델을 등록하고 배포하는 방법에 대한 예입니다.

```python
from azureml.core import Model

model = Model.register(workspace=ws,
                       model_name='mnist-sample',                  # Name of the registered model in your workspace.
                       model_path='mnist-model.onnx',              # Local ONNX model to upload and register as a model.
                       model_framework=Model.Framework.ONNX ,      # Framework used to create the model.
                       model_framework_version='1.3',              # Version of ONNX used to create the model.
                       description='Onnx MNIST model')

service_name = 'onnx-mnist-service'
service = Model.deploy(ws, service_name, [model])
```

모델에 점수를 매기려면 [웹 서비스로 배포된 Azure Machine Learning 모델 사용](./how-to-consume-web-service.md)을 참조하세요. 많은 ONNX 프로젝트에서는 학습 및 유효성 검사 데이터를 조밀하게 저장하기 위해 protobuf 파일을 사용하며, 이 때문에 서비스에서 예상하는 데이터 형식을 파악하기 어려울 수 있습니다. 모델 개발자는 개발자를 위한 문서를 만들어야 합니다.

* 입력 형식(JSON 또는 이진)
* 입력 데이터 모양 및 유형(예: 모양이 [100,100,3]인 부동 소수점 배열)
* 도메인 정보(예: 이미지, 색 공간, 구성 요소 순서 및 값의 정규화 여부)

Pytorch를 사용하는 경우 [PyTorch에서 ONNX로 모델 내보내기](https://github.com/onnx/tutorials/blob/master/tutorials/PytorchOnnxExport.ipynb)에서 변환 및 제한 사항에 대한 세부 정보를 확인할 수 있습니다. 

## <a name="scikit-learn-models"></a>Scikit-learn 모델

코드가 없는 모델 배포는 기본 제공되는 모든 scikit-learn 모델 형식에 지원됩니다.

다음은 추가 코드 없이 sklearn 모델을 등록하고 배포하는 방법의 예입니다.

```python
from azureml.core import Model
from azureml.core.resource_configuration import ResourceConfiguration

model = Model.register(workspace=ws,
                       model_name='my-sklearn-model',                # Name of the registered model in your workspace.
                       model_path='./sklearn_regression_model.pkl',  # Local file to upload and register as a model.
                       model_framework=Model.Framework.SCIKITLEARN,  # Framework used to create the model.
                       model_framework_version='0.19.1',             # Version of scikit-learn used to create the model.
                       resource_configuration=ResourceConfiguration(cpu=1, memory_in_gb=0.5),
                       description='Ridge regression model to predict diabetes progression.',
                       tags={'area': 'diabetes', 'type': 'regression'})
                       
service_name = 'my-sklearn-service'
service = Model.deploy(ws, service_name, [model])
```

> [!NOTE]
> predict_proba를 지원하는 모델은 기본적으로 해당 메서드를 사용합니다. 예측을 사용하기 위해 이를 재정의하려면 아래와 같이 POST 본문을 수정하면 됩니다.

```python
import json


input_payload = json.dumps({
    'data': [
        [ 0.03807591,  0.05068012,  0.06169621, 0.02187235, -0.0442235,
         -0.03482076, -0.04340085, -0.00259226, 0.01990842, -0.01764613]
    ],
    'method': 'predict'  # If you have a classification model, the default behavior is to run 'predict_proba'.
})

output = service.run(input_payload)

print(output)
```

> [!NOTE]
> 이러한 종속성은 미리 작성된 scikit-learn 유추 컨테이너에 포함되어 있습니다.

```yaml
    - dill
    - azureml-defaults
    - inference-schema[numpy-support]
    - scikit-learn
    - numpy
    - joblib
    - pandas
    - scipy
    - sklearn_pandas
```
## <a name="next-steps"></a>다음 단계

* [실패한 배포 문제 해결](how-to-troubleshoot-deployment.md)
* [Azure Kubernetes Service로 배포](how-to-deploy-azure-kubernetes-service.md)
* [웹 서비스를 사용하는 클라이언트 애플리케이션 만들기](how-to-consume-web-service.md)
* [웹 서비스 업데이트](how-to-deploy-update-web-service.md)
* [사용자 지정 Docker 이미지를 사용하여 모델을 배포하는 방법](./how-to-deploy-custom-container.md)
* [TLS를 사용하여 Azure Machine Learning을 통해 웹 서비스 보호](how-to-secure-web-service.md)
* [Application Insights를 사용하여 Azure Machine Learning 모델 모니터링](how-to-enable-app-insights.md)
* [프로덕션 환경에서 모델용 데이터 수집](how-to-enable-data-collection.md)
* [모델 배포에 대한 이벤트 경고 및 트리거 만들기](how-to-use-event-grid.md)