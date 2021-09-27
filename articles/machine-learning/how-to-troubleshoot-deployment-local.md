---
title: 로컬 모델 배포 문제 해결
titleSuffix: Azure Machine Learning
description: 모델 배포 오류 문제 해결의 첫 단계로 로컬 모델 배포를 시도합니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: luquinta
ms.date: 11/25/2020
ms.topic: troubleshooting
ms.custom: devx-track-python, deploy, contperf-fy21q2
ms.openlocfilehash: ca06d475544cb45045d751ede1abe743a3a012ab
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128553445"
---
# <a name="troubleshooting-with-a-local-model-deployment"></a>로컬 모델 배포 문제 해결

ACI(Azure Container Instances) 또는 AKS(Azure Kubernetes Service)에 대한 배포 문제 해결의 첫 번째 단계로 로컬 모델 배포를 시도합니다.  로컬 웹 서비스를 사용하면 일반적인 Azure Machine Learning Docker 웹 서비스 배포 오류를 쉽게 찾아 수정할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

* **Azure 구독**. [Azure Machine Learning 평가판 또는 유료 버전](https://azure.microsoft.com/free/)을 사용해 보세요.
* 옵션 A(**권장**) - Azure Machine Learning 컴퓨팅 인스턴스에서 로컬로 디버그
   * [컴퓨팅 인스턴스](how-to-deploy-local-container-notebook-vm.md)가 실행 되는 Azure Machine Learning 작업 영역
* 옵션 B - 컴퓨팅에서 로컬로 디버그
   * [Azure Machine Learning SDK](/python/api/overview/azure/ml/install)
   * [Azure CLI](/cli/azure/install-azure-cli)
   * [Azure Machine Learning용 CLI 확장](reference-azure-machine-learning-cli.md)
   * 로컬 시스템에서 작동하는 Docker가 설치되어 있어야 합니다. 
   * Docker 설치를 확인하려면 터미널 또는 명령 프롬프트에서 `docker run hello-world` 명령을 사용합니다. Docker 설치 또는 Docker 오류 문제 해결에 대한 자세한 내용은 [Docker 설명서](https://docs.docker.com/)를 참조하세요.
* 옵션 C - Azure Machine Learning 유추 HTTP 서버를 사용하여 로컬 디버깅을 사용하도록 설정합니다.
    * Azure Machine Learning 유추 HTTP 서버[(미리 보기)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)는 로컬 개발 환경에서 입력 스크립트(`score.py`)의 유효성을 쉽게 검사할 수 있도록 하는 Python 패키지입니다. 점수 매기기 스크립트에 문제가 있으면 서버에서 오류를 반환합니다. 또한 오류가 발생한 위치도 반환합니다.
    * 연속 통합 및 배포 파이프라인에서 유효성 검사 게이트를 만들 때에도 서버를 사용할 수 있습니다. 예를 들어 후보 스크립트를 사용하여 서버를 시작하고 로컬 엔드포인트에 대해 테스트 도구 모음을 실행합니다.

## <a name="azure-machine-learning-inference-http-server"></a>Azure Machine Learning 유추 HTTP 서버

로컬 유추 서버를 사용하면 항목 스크립트(`score.py`)를 신속하게 디버그할 수 있습니다. 기본 점수 스크립트에 버그가 있는 경우 서버에서 모델을 초기화하거나 제공하지 못합니다. 대신 예외 및 문제가 발생한 위치를 throw합니다. [Azure Machine Learning 유추 HTTP 서버에 대한 자세한 정보](how-to-inference-server-http.md)

1. [pypi](https://pypi.org/) 피드에서 `azureml-inference-server-http` 패키지를 설치합니다.

    ```bash
    python -m pip install azureml-inference-server-http
    ```

2. 서버를 시작하고 항목 스크립트로 `score.py`를 설정합니다.

    ```bash
    azmlinfsrv --entry_script score.py
    ```

3. `curl`을 사용하여 서버에 점수 매기기 요청을 보냅니다.

    ```bash
    curl -p 127.0.0.1:5001/score
    ```

## <a name="debug-locally"></a>로컬에서 디버그

[MachineLearningNotebooks](https://github.com/Azure/MachineLearningNotebooks) 리포지토리에서 샘플 [로컬 배포 노트북](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/deploy-to-local/register-model-deploy-local.ipynb)을 찾아 실행 가능한 예제를 살펴볼 수 있습니다.

> [!WARNING]
> 프로덕션 시나리오에는 로컬 웹 서비스 배포가 지원되지 않습니다.

로컬로 배포하려면 `LocalWebservice.deploy_configuration()`을 사용하여 배포 구성을 만들도록 코드를 수정합니다. 그런 다음, `Model.deploy()`를 사용하여 서비스를 배포합니다. 다음 예제에서는 모델 변수에 포함된 모델을 로컬 웹 서비스로 배포합니다.

```python
from azureml.core.environment import Environment
from azureml.core.model import InferenceConfig, Model
from azureml.core.webservice import LocalWebservice


# Create inference configuration based on the environment definition and the entry script
myenv = Environment.from_conda_specification(name="env", file_path="myenv.yml")
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
# Create a local deployment, using port 8890 for the web service endpoint
deployment_config = LocalWebservice.deploy_configuration(port=8890)
# Deploy the service
service = Model.deploy(
    ws, "mymodel", [model], inference_config, deployment_config)
# Wait for the deployment to complete
service.wait_for_deployment(True)
# Display the port that the web service is available on
print(service.port)
```

사용자 고유의 conda 사양 YAML을 정의하는 경우 버전 1.0.45 이상을 pip 종속성으로 사용하여 azureml-defaults를 나열합니다. 이 패키지에는 모델을 웹 서비스로 호스팅하는 데 필요합니다.

이 시점에서 서비스를 정상적으로 사용할 수 있습니다. 다음 코드에서는 데이터를 서비스에 보내는 방법을 보여줍니다.

```python
import json

test_sample = json.dumps({'data': [
    [1, 2, 3, 4, 5, 6, 7, 8, 9, 10],
    [10, 9, 8, 7, 6, 5, 4, 3, 2, 1]
]})

test_sample = bytes(test_sample, encoding='utf8')

prediction = service.run(input_data=test_sample)
print(prediction)
```

Python 환경을 사용자 지정하는 방법에 대한 자세한 내용은 [학습 및 배포 환경 만들기 및 관리](how-to-use-environments.md)를 참조하세요. 

### <a name="update-the-service"></a>서비스 업데이트

로컬 테스트 중에 로깅을 추가하도록 `score.py` 파일을 업데이트하거나 검색한 문제를 해결해야 할 수도 있습니다. `score.py` 파일의 변경 내용을 다시 로드하려면 `reload()`를 사용합니다. 예를 들어 다음 코드에서는 서비스에 대한 스크립트를 다시 로드한 다음, 데이터를 이 스크립트에 보냅니다. 데이터는 업데이트된 `score.py` 파일을 사용하여 채점됩니다.

> [!IMPORTANT]
> `reload` 메서드는 로컬 배포에만 사용할 수 있습니다. 다른 컴퓨팅 대상으로 배포를 업데이트하는 방법에 대한 자세한 내용은 [웹 서비스를 업데이트하는 방법](how-to-deploy-update-web-service.md)을 참조하세요.

```python
service.reload()
print(service.run(input_data=test_sample))
```

> [!NOTE]
> 스크립트는 서비스에서 사용하는 `InferenceConfig` 개체로 지정된 위치에서 다시 로드됩니다.

모델, Conda 종속성 또는 배포 구성을 변경하려면 [update()](/python/api/azureml-core/azureml.core.webservice%28class%29#update--args-)를 사용합니다. 다음 예제에서는 서비스에서 사용하는 모델을 업데이트합니다.

```python
service.update([different_model], inference_config, deployment_config)
```

### <a name="delete-the-service"></a>서비스 삭제

서비스를 삭제하려면 [delete()](/python/api/azureml-core/azureml.core.webservice%28class%29#delete--)를 사용합니다.

### <a name="inspect-the-docker-log"></a><a id="dockerlog"></a> Docker 로그 검사

서비스 개체에서 자세한 Docker 엔진 로그 메시지를 인쇄할 수 있습니다. ACI, AKS 및 로컬 배포에 대한 로그를 볼 수 있습니다. 다음 예제에서는 로그를 출력하는 방법을 보여 줍니다.

```python
# if you already have the service object handy
print(service.get_logs())

# if you only know the name of the service (note there might be multiple services with the same name but different version number)
print(ws.webservices['mysvc'].get_logs())
```

로그에 `Booting worker with pid: <pid>` 줄이 여러 번 표시되면 작업자를 시작할 메모리가 부족한 것입니다.
`deployment_config`의 `memory_gb` 값을 늘려서 오류를 해결할 수 있습니다.

## <a name="next-steps"></a>다음 단계

배포에 대해 자세히 알아보세요.

* [원격 배포 문제를 해결하는 방법](how-to-troubleshoot-deployment.md)
* [Azure Machine Learning 유추 HTTP 서버](how-to-inference-server-http.md)
* [배포 방법 및 위치](how-to-deploy-and-where.md)
* [자습서: 모델 학습 및 배포](tutorial-train-models-with-aml.md)
* [로컬에서 실험을 실행하고 디버그하는 방법](./how-to-debug-visual-studio-code.md)