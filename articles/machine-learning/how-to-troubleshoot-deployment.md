---
title: 원격 모델 배포 문제 해결
titleSuffix: Azure Machine Learning
description: Azure Kubernetes Service 및 Azure Container Instances와 관련된 일반적인 일부 Docker 배포 오류를 해결하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: mlops
ms.date: 11/25/2020
ms.topic: troubleshooting
ms.custom: contperf-fy20q4, devx-track-python, deploy, contperf-fy21q2
ms.openlocfilehash: c71aaeea4282ebf265776e7f974841e7385fa75c
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/04/2021
ms.locfileid: "129425186"
---
# <a name="troubleshooting-remote-model-deployment"></a>원격 모델 배포 문제 해결 

Azure Machine Learning을 사용하여 ACI(Azure Container Instances) 및 AKS(Azure Kubernetes Service)에 모델을 배포할 때 발생할 수 있는 일반적인 오류를 해결하거나 방법을 알아봅니다.

> [!NOTE]
> AKS(Azure Kubernetes Service)에 모델을 배포하는 경우 해당 클러스터에 대해 [Azure Monitor](../azure-monitor/containers/container-insights-enable-existing-clusters.md)를 사용하도록 설정하는 것이 좋습니다. 이렇게 하면 전반적인 클러스터 상태와 리소스 사용을 해석할 수 있습니다. 다음 리소스도 유용할 수 있습니다.
>
> * [AKS 클러스터에 영향을 주는 Resource Health 이벤트 확인](../aks/aks-resource-health.md)
> * [Azure Kubernetes Service 진단](../aks/concepts-diagnostics.md)
>
> 비정상 또는 오버로드 상태인 클러스터에 모델 배포를 시도하면 문제가 발생하게 됩니다. AKS 클러스터 문제 해결에 도움이 필요한 경우 AKS 고객 지원팀에 문의하세요.

## <a name="prerequisites"></a>필수 구성 요소

* **Azure 구독**. [Azure Machine Learning 평가판 또는 유료 버전](https://azure.microsoft.com/free/)을 사용해 보세요.
* [Azure Machine Learning SDK](/python/api/overview/azure/ml/install)
* [Azure CLI](/cli/azure/install-azure-cli)
* [Azure Machine Learning용 CLI 확장](reference-azure-machine-learning-cli.md)

## <a name="steps-for-docker-deployment-of-machine-learning-models"></a>기계 학습 모델의 Docker 배포 단계

Azure Machine Learning에서 로컬이 아닌 컴퓨팅에 모델을 배포하는 경우 다음 작업이 수행됩니다.

1. InferenceConfig의 환경 개체에서 지정한 Dockerfile은 원본 디렉터리의 내용과 함께 클라우드로 전송됩니다.
1. 이전에 빌드한 이미지를 컨테이너 레지스트리에서 사용할 수 없는 경우 새 Docker 이미지가 클라우드에 빌드되고 작업 영역의 기본 컨테이너 레지스트리에 저장됩니다.
1. 컨테이너 레지스트리의 Docker 이미지가 컴퓨팅 대상에 다운로드됩니다.
1. 작업 영역의 기본 Blob 저장소가 컴퓨팅 대상에 탑재되어 등록된 모델에 대한 액세스 권한을 부여합니다.
1. 웹 서버는 입력 스크립트의 `init()` 함수를 실행하여 초기화됩니다.
1. 배포된 모델이 요청을 받으면 `run()` 함수가 해당 요청을 처리합니다.

로컬 배포를 사용할 때의 주요 차이점은 컨테이너 이미지가 로컬 컴퓨터에 빌드된다는 것입니다. 따라서 로컬 배포를 위해 Docker를 설치해야 합니다.

이러한 상위 수준 단계를 이해하면 오류가 발생하는 위치를 이해하는 데 도움이 됩니다.

## <a name="get-deployment-logs"></a>배포 로그 가져오기

디버깅 오류에 대한 첫 번째 단계는 배포 로그를 가져오는 것입니다. 먼저 [여기의 지침](how-to-deploy-and-where.md#connect-to-your-workspace)에 따라 작업 영역에 연결합니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

배포된 웹 서비스에서 로그를 가져오려면 다음을 수행합니다.

```azurecli
az ml service get-logs --verbose --workspace-name <my workspace name> --name <service name>
```

# <a name="python"></a>[Python](#tab/python)


`ws`라는 `azureml.core.Workspace` 유형의 개체가 있다고 가정하면 다음을 수행할 수 있습니다.

```python
print(ws.webservices)

# Choose the webservice you are interested in

from azureml.core import Webservice

service = Webservice(ws, '<insert name of webservice>')
print(service.get_logs())
```

---

## <a name="debug-locally"></a>로컬에서 디버그

ACI 또는 AKS에 모델을 배포할 때 문제가 있는 경우 로컬 웹 서비스로 배포합니다. 로컬 웹 서비스를 사용하면 문제를 더 쉽게 해결할 수 있습니다. 로컬 배포 문제를 해결하려면 [로컬 문제 해결 문서](./how-to-troubleshoot-deployment-local.md)를 참조하세요.

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

## <a name="container-cannot-be-scheduled"></a>컨테이너를 예약할 수 없음

서비스를 Azure Kubernetes Service 컴퓨팅 대상에 배포하는 경우 Azure Machine Learning에서 서비스를 요청된 리소스의 양으로 예약하려고 합니다. 5분 후에 적절한 양의 리소스가 있는 클러스터에서 사용할 수 있는 노드가 없으면 배포가 실패합니다. 실패 메시지는 `Couldn't Schedule because the kubernetes cluster didn't have available resources after trying for 00:05:00`입니다. 이 오류는 더 많은 노드를 추가하거나 노드의 SKU를 변경하거나 서비스의 리소스 요구 사항을 변경하여 해결할 수 있습니다. 

오류 메시지는 일반적으로 더 필요한 리소스를 나타냅니다. 예를 들어 `0/3 nodes are available: 3 Insufficient nvidia.com/gpu`를 나타내는 오류 메시지가 표시되면 서비스에 GPU가 필요하며 사용 가능한 GPU가 없는 클러스터에 세 개의 노드가 있음을 의미합니다. 이는 GPU SKU를 사용하는 경우 더 많은 노드를 추가하거나, 사용하지 않는 경우 GPU 지원 SKU로 전환하거나, GPU가 필요하지 않도록 환경을 변경하여 해결할 수 있습니다.  

## <a name="service-launch-fails"></a>서비스 시작 실패

이미지가 성공적으로 빌드되면 시스템에서 배포 구성을 사용하여 컨테이너를 시작하려고 합니다. 컨테이너 시작 프로세스의 일부로, 시스템에서 채점 스크립트의 `init()` 함수를 호출합니다. `init()` 함수에 catch되지 않은 예외가 있는 경우 오류 메시지에 **CrashLoopBackOff** 오류가 표시될 수 있습니다.

[Docker 로그 검사](how-to-troubleshoot-deployment-local.md#dockerlog) 문서의 정보를 사용하세요.

## <a name="function-fails-get_model_path"></a>함수 실패: get_model_path()

채점 스크립트의 `init()` 함수에서 컨테이너의 모델 파일 또는 모델 파일의 폴더를 찾기 위해 [Model.get_model_path()](/python/api/azureml-core/azureml.core.model.model#get-model-path-model-name--version-none---workspace-none-) 함수가 호출되는 경우가 많습니다. 모델 파일 또는 폴더를 찾을 수 없으면 함수가 실패합니다. 이 오류를 디버그하는 가장 쉬운 방법은 컨테이너 셸에서 아래의 Python 코드를 실행하는 것입니다.

```python
from azureml.core.model import Model
import logging
logging.basicConfig(level=logging.DEBUG)
print(Model.get_model_path(model_name='my-best-model'))
```

이 예제에서는 채점 스크립트에서 모델 파일 또는 폴더를 찾을 것으로 예상되는 컨테이너의 로컬 경로(`/var/azureml-app`에 대한 상대 경로)를 출력합니다. 그러면 예상 위치에 파일 또는 폴더가 실제로 있는지 확인할 수 있습니다.

로깅 수준을 DEBUG로 설정하면 추가 정보가 기록될 수 있으며, 이는 오류를 식별하는 데 유용할 수 있습니다.

## <a name="function-fails-runinput_data"></a>함수 실패: run(input_data)

서비스가 성공적으로 배포되었지만 채점 엔드포인트에 데이터를 게시할 때 크래시가 발생하는 경우 오류를 catch하는 명령문을 `run(input_data)` 함수에 추가하면 구체적인 오류 메시지가 반환됩니다. 다음은 그 예입니다.

```python
def run(input_data):
    try:
        data = json.loads(input_data)['data']
        data = np.array(data)
        result = model.predict(data)
        return json.dumps({"result": result.tolist()})
    except Exception as e:
        result = str(e)
        # return error message back to the client
        return json.dumps({"error": result})
```

**참고**: `run(input_data)` 호출에서 오류 메시지를 반환하는 방법은 디버깅 용도로만 사용해야 합니다. 보안상의 이유로 프로덕션 환경에서는 오류 메시지를 이런 식으로 반환해서는 안 됩니다.

## <a name="http-status-code-502"></a>502 HTTP 상태 코드

502 상태 코드는 서비스에서 예외를 throw했거나 score.py 파일의 `run()` 메서드에서 작동을 중단했음을 나타냅니다. 이 문서의 정보를 사용하여 파일을 디버그하세요.

## <a name="http-status-code-503"></a>503 HTTP 상태 코드

Azure Kubernetes Service 배포는 자동 크기 조정을 지원하므로 추가 로드를 지원하기 위해 복제본을 추가할 수 있습니다. 자동 크기 조정기는 **점진적** 로드 변경을 처리하도록 설계되었습니다. 초당 요청 수가 크게 급증하면 클라이언트에서 503 HTTP 상태 코드를 받을 수 있습니다. 자동 크기 조정기가 빠르게 반응하더라도 추가 컨테이너를 만드는 데 상당한 시간이 소요됩니다.

스케일 업/다운 결정은 현재 컨테이너 복제본의 사용률에 기반합니다. 사용 중인(요청을 처리하는) 복제본 수를 현재 복제본의 총 수로 나눈 값이 현재 사용률입니다. 이 수가 `autoscale_target_utilization`을 초과하면 더 많은 복제본이 생성됩니다. 이보다 낮으면 복제본이 감소됩니다. 복제본 추가에 대한 결정은 열성적이고 빠릅니다(약 1초). 복제본 제거에 대한 결정은 보수적입니다(약 1분). 기본적으로 자동 크기 조정 목표 사용률은 **70%** 로 설정됩니다. 즉 서비스에서 RPS(초당 요청 수)의 급증을 **최대 30%** 까지 처리할 수 있습니다.

503 상태 코드를 방지하는 데 도움이 되는 두 가지 방법이 있습니다.

> [!TIP]
> 이러한 두 가지 접근 방식은 개별적으로 또는 함께 사용할 수 있습니다.

* 자동 크기 조정에서 새 복제본을 만드는 사용률 수준을 변경합니다. `autoscale_target_utilization`을 더 낮은 값으로 설정하여 사용률 목표를 조정할 수 있습니다.

    > [!IMPORTANT]
    > 이 변경으로 인해 복제본이 *더 빨리* 만들어지지 않습니다. 대신 낮은 사용률 임계값에서 만들어집니다. 서비스 사용률이 70%가 될 때까지 기다리는 대신 값을 30%로 변경하면 30%의 사용률이 발생할 때 복제본이 만들어집니다.
    
    웹 서비스에서 이미 현재 최대 복제본을 사용하고 있고 여전히 503 상태 코드가 표시되면 `autoscale_max_replicas` 값을 늘려 최대 복제본 수를 늘립니다.

* 최소 복제본 수를 변경합니다. 최소 복제본 수를 늘리면 들어오는 급증을 처리할 수 있는 더 큰 풀이 제공됩니다.

    최소 복제본 수를 늘리려면 `autoscale_min_replicas`를 더 높은 값으로 설정합니다. 다음 코드를 사용하여 값을 프로젝트와 관련된 값으로 바꿔 필요한 복제본을 계산할 수 있습니다.

    ```python
    from math import ceil
    # target requests per second
    targetRps = 20
    # time to process the request (in seconds)
    reqTime = 10
    # Maximum requests per container
    maxReqPerContainer = 1
    # target_utilization. 70% in this example
    targetUtilization = .7

    concurrentRequests = targetRps * reqTime / targetUtilization

    # Number of container replicas
    replicas = ceil(concurrentRequests / maxReqPerContainer)
    ```

    > [!NOTE]
    > 새 최소 복제본에서 처리할 수 있는 것보다 더 큰 요청 급증을 받으면 503을 다시 받을 수 있습니다. 예를 들어 서비스 트래픽이 증가하면 최소 복제본을 늘려야 할 수 있습니다.

`autoscale_target_utilization`, `autoscale_max_replicas` 및 `autoscale_min_replicas` 설정에 대한 자세한 내용은 [AksWebservice](/python/api/azureml-core/azureml.core.webservice.akswebservice) 모듈 참조를 참조하세요.

## <a name="http-status-code-504"></a>504 HTTP 상태 코드

504 상태 코드는 요청 시간이 초과되었음을 나타냅니다. 기본 시간 제한은 1분입니다.

불필요한 호출을 제거하도록 score.py를 수정하여 시간 제한을 늘리거나 서비스 속도를 높일 수 있습니다. 이러한 작업으로도 문제가 해결되지 않으면 이 문서의 정보를 사용하여 score.py 파일을 디버그합니다. 코드가 응답하지 않는 상태이거나 무한 루프일 수 있습니다.

## <a name="other-error-messages"></a>기타 오류 메시지

다음 오류에 대해 이 작업을 수행합니다.

|Error  | 해결 방법  |
|---------|---------|
| 409 충돌 오류| 작업이 이미 진행 중인 경우 동일한 웹 서비스에 대한 새 작업은 모두 409 충돌 오류로 응답합니다. 예를 들어, 웹 서비스 만들기 또는 업데이트 작업이 진행 중인데 새 삭제 작업을 트리거하면 오류가 발생합니다. |
|웹 서비스를 배포할 때 이미지 빌드가 실패했습니다.     |  이미지 구성을 위해 Conda 파일에 "pynacl == 1.2.1"을 pip 종속성으로 추가합니다.       |
|`['DaskOnBatch:context_managers.DaskOnBatch', 'setup.py']' died with <Signals.SIGKILL: 9>`     |   배포에 사용되는 VM의 SKU를 메모리를 더 많이 포함하는 SKU로 변경합니다. |
|FPGA 실패     |  요청을 하고 FPGA 할당량의 승인을 받을 때까지 FPGA에 모델을 배포할 수 없습니다. 액세스를 요청하려면 할당량 요청 양식 https://aka.ms/aml-real-time-ai를 작성합니다.       |


## <a name="advanced-debugging"></a>고급 디버깅

모델 배포에 포함된 Python 코드를 대화형으로 디버그해야 할 수도 있습니다. 예를 들어 항목 스크립트가 실패하고 추가 로깅으로 이유를 확인할 수 없는 경우입니다. Visual Studio Code 및 debugpy를 사용하여 Docker 컨테이너 내부에서 실행되는 코드에 연결할 수 있습니다.

자세한 내용은 [VS Code의 대화형 디버깅 가이드](how-to-debug-visual-studio-code.md#debug-and-troubleshoot-deployments)를 참조하세요.

## <a name="model-deployment-user-forum"></a>[모델 배포 사용자 포럼](/answers/topics/azure-machine-learning-inference.html)

## <a name="next-steps"></a>다음 단계

배포에 대해 자세히 알아보세요.

* [배포 방법 및 위치](how-to-deploy-and-where.md)
* [자습서: 모델 학습 및 배포](tutorial-train-models-with-aml.md)
* [로컬에서 실험을 실행하고 디버그하는 방법](./how-to-debug-visual-studio-code.md)