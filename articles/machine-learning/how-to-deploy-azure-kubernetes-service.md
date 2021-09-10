---
title: Kubernetes 서비스에 ML 모델 배포
titleSuffix: Azure Machine Learning
description: Azure Kubernetes Service를 사용하여 Azure Machine Learning 모델을 웹 서비스로 배포하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.custom: contperf-fy21q1, deploy
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 07/28/2021
ms.openlocfilehash: a620d1cbd9ae0f9a4f03e6bf744cf2febd8ac240
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122566795"
---
# <a name="deploy-a-model-to-an-azure-kubernetes-service-cluster"></a>Azure Kubernetes Service 클러스터에 모델 배포

Azure Machine Learning을 사용하여 AKS(Azure Kubernetes Service)에서 모델을 웹 서비스로 배포하는 방법을 알아봅니다. Azure Kubernetes Service는 대규모 프로덕션 배포에 적합합니다. 다음 기능 중 하나 이상이 필요한 경우 Azure Kubernetes 서비스를 사용하세요.

- __빠른 응답 시간__
- 배포 서비스 __자동 스케일링__
- __Logging__
- __모델 데이터 수집__
- __인증__
- __TLS 종료__
- __하드웨어 가속__ 옵션, 예: GPU 및 FPGA(필드 프로그래머블 게이트 어레이)

Azure Kubernetes Service에 배포하는 경우 __작업 영역에 연결__ 된 AKS 클러스터에 배포합니다. AKS 클러스터를 작업 영역에 연결하는 방법에 대한 자세한 내용은 [Azure Kubernetes Service 클러스터 만들기 및 연결](how-to-create-attach-kubernetes.md)을 참조하세요.

> [!IMPORTANT]
> 웹 서비스에 배포하기 전에 로컬에서 디버그하는 것이 좋습니다. 자세한 내용은 [로컬에서 디버그](./how-to-troubleshoot-deployment-local.md)를 참조하세요.
>
> 또한 Azure Machine Learning - [로컬 Notebook에 배포](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/deployment/deploy-to-local)를 참조할 수 있습니다.

[!INCLUDE [endpoints-option](../../includes/machine-learning-endpoints-preview-note.md)]

## <a name="prerequisites"></a>사전 요구 사항

- Azure Machine Learning 작업 영역 자세한 내용은 [Azure Machine Learning 작업 영역 만들기](how-to-manage-workspace.md)를 참조하세요.

- 작업 영역에 등록된 기계 학습 모델. 등록된 모델이 없는 경우 [모델을 배포하는 방법 및 위치](how-to-deploy-and-where.md)를 참조하세요.

- [Machine Learning Service용 Azure CLI 확장](reference-azure-machine-learning-cli.md), [Azure Machine Learning Python SDK](/python/api/overview/azure/ml/intro) 또는 [Azure Machine Learning Visual Studio Code 확장](how-to-setup-vs-code.md).

- 이 문서의 __Python__ 코드 조각에서는 다음 변수가 설정되었다고 가정합니다.

    * `ws` - 작업 영역으로 설정.
    * `model` - 등록된 모델로 설정.
    * `inference_config` - 모델에 대한 유추 구성으로 설정.

    이러한 변수를 설정하는 방법에 대한 자세한 내용은 [모델을 배포하는 방법 및 위치](how-to-deploy-and-where.md)를 참조하세요.

- 이 문서의 __CLI__ 코드 조각에서는 `inferenceconfig.json` 문서를 만들었다고 가정합니다. 이 문서 만들기에 대한 자세한 내용은 [모델을 배포하는 방법 및 위치](how-to-deploy-and-where.md)를 참조하세요.

- 작업 영역에 연결된 Azure Kubernetes Service 클러스터입니다. 자세한 내용은 [Azure Kubernetes Service 클러스터 만들기 및 연결](how-to-create-attach-kubernetes.md)을 참조하세요.

    - GPU 노드나 FPGA 노드(또는 특정 SKU)에 모델을 배포하려면 특정 SKU를 사용하여 클러스터를 만들어야 합니다. 기존 클러스터에 보조 노드 풀을 만들고 보조 노드 풀에 모델을 배포하는 것은 지원되지 않습니다.

## <a name="understand-the-deployment-processes"></a>배포 프로세스 이해

"배포"라는 단어는 Kubernetes와 Azure Machine Learning 모두에 사용됩니다. 이러한 두 가지 컨텍스트에서 "배포"의 의미는 다릅니다. Kubernetes에서 `Deployment`는 구체적인 엔터티이며, 선언적 YAML 파일을 사용하여 지정됩니다. Kubernetes `Deployment`에는 정의된 수명 주기 및 다른 Kubernetes 엔터티(예: `Pods` 및 `ReplicaSets`)와의 구체적인 관계가 있습니다. [Kubernetes란?](https://aka.ms/k8slearning)의 문서와 비디오를 통해 Kubernetes에 대해 자세히 알아볼 수 있습니다.

Azure Machine Learning에서 "배포"는 프로젝트 리소스를 제공하고 정리하는 보다 일반적인 의미로 사용됩니다. Azure Machine Learning에서 배포의 일부로 간주되는 단계는 다음과 같습니다.

1. 프로젝트 폴더에 파일 압축(.amlignore 또는 .gitignore에 지정된 항목 무시)
1. 컴퓨팅 클러스터 스케일 업(Kubernetes 관련)
1. dockerfile을 컴퓨팅 노드로 다운로드 또는 빌드(Kubernetes 관련)
    1. 시스템에서 다음 해시가 계산됩니다. 
        - 기본 이미지 
        - 사용자 지정 docker 단계([사용자 지정 Docker 기본 이미지를 사용하여 모델 배포](./how-to-deploy-custom-container.md) 참조)
        - conda 정의 YAML ([Azure Machine Learning에서 소프트웨어 환경 만들기 및 사용](./how-to-use-environments.md) 참조)
    1. 시스템은 작업 영역 ACR(Azure Container Registry) 조회에서 이 해시를 키로 사용합니다.
    1. 찾을 수 없는 경우 전역 ACR에서 일치하는 항목을 찾습니다.
    1. 찾을 수 없는 경우 시스템은 새 이미지(캐시되고 작업 영역 ACR로 푸시될)를 작성합니다.
1. 압축된 프로젝트 파일을 컴퓨팅 노드의 임시 스토리지에 다운로드
1. 프로젝트 파일 압축 풀기
1. `python <entry script> <arguments>`를 실행하는 컴퓨팅 노드
1. `./outputs`에 기록된 로그, 모델 파일 및 기타 파일을 작업 영역과 연결된 스토리지 계정에 저장
1. 컴퓨팅 스케일 다운(임시 스토리지 제거 포함)(Kubernetes 관련)

### <a name="azure-ml-router"></a>Azure ML 라우터

들어오는 유추 요청을 배포된 서비스로 라우팅하는 프런트 엔드 구성 요소(azureml-fe)는 필요에 따라 자동으로 스케일링됩니다. azureml-fe의 스케일링은 AKS 클러스터 용도 및 크기(노드 수)에 기반합니다. 클러스터 용도 및 노드는 [AKS 클러스터를 만들거나 연결](how-to-create-attach-kubernetes.md)할 때 구성됩니다. azureml-fe 서비스는 클러스터당 하나이며, 여러 Pod에서 실행될 수 있습니다.

> [!IMPORTANT]
> __dev-test__ 로 구성된 클러스터를 사용하는 경우 자체 스케일러를 **사용하지 않도록 설정** 됩니다.

Azureml-fe는 더 많은 코어를 사용하도록 스케일 업(세로)하고 더 많은 포드를 사용하도록 스케일 아웃(가로)합니다. 스케일 업을 결정할 때는 들어오는 유추 요청을 라우팅하는 데 걸리는 시간이 사용됩니다. 이 시간이 임계값을 초과하면 스케일 업이 발생합니다. 들어오는 요청을 라우팅하는 데 걸리는 시간이 임계값을 계속 초과하면 스케일 아웃이 발생합니다.

스케일 다운 및 스케일 인하는 경우에는 CPU 사용량이 사용됩니다. CPU 사용량 임계값에 도달하면 프런트 엔드가 먼저 스케일 다운됩니다. CPU 사용량이 스케일 인 임계값으로 떨어지면 스케일 인 작업이 발생합니다. 스케일 업 및 스케일 아웃은 사용 가능한 클러스터 리소스가 충분한 경우에만 발생합니다.

<a id="connectivity"></a>

## <a name="understand-connectivity-requirements-for-aks-inferencing-cluster"></a>AKS 추론 클러스터에 대한 연결 요구 사항 이해

Azure Machine Learning이 AKS 클러스터를 만들거나 연결할 때 AKS 클러스터는 다음 두 가지 네트워크 모델 중 하나로 배포됩니다.
* Kubenet 네트워킹 - 네트워크 리소스는 일반적으로 AKS 클러스터가 배포될 때 만들어지고 구성됩니다.
* Azure CNI(Container Networking Interface) 네트워킹 - AKS 클러스터가 기존 가상 네트워크 리소스 및 구성에 연결됩니다.

Kubenet 네트워킹의 경우 Azure Machine Learning 서비스에 적절하게 네트워크를 만들고 구성합니다. CNI 네트워킹의 경우 연결 요구 사항을 이해하고 AKS 추론에 대한 DNS 확인 및 아웃바운드 연결을 확인해야 합니다. 예를 들어 방화벽을 사용하여 네트워크 트래픽을 차단할 수 있습니다.

다음 다이어그램에서는 AKS 추론에 대한 연결 요구 사항을 보여 줍니다. 검은색 화살표는 실제 통신을 나타내고 파란색 화살표는 도메인 이름을 나타냅니다. 이러한 호스트에 대한 항목을 방화벽 또는 사용자 지정 DNS 서버에 추가해야 할 수 있습니다.

 ![AKS 유추에 대한 연결 요구 사항](./media/how-to-deploy-aks/aks-network.png)

일반적인 AKS 연결 요구 사항은 [Azure Kubernetes Service에서 클러스터 노드의 송신 트래픽 제어](../aks/limit-egress-traffic.md)를 참조하세요.

### <a name="overall-dns-resolution-requirements"></a>전체 DNS 확인 요구 사항

기존 VNet 내에서 DNS 확인은 사용자가 제어합니다. 예를 들면 방화벽 또는 사용자 지정 DNS 서버가 있습니다. 다음 호스트에 연결할 수 있어야 합니다.

| 호스트 이름 | 사용 대상 |
| ----- | ----- |
| `<cluster>.hcp.<region>.azmk8s.io` | AKS API 서버 |
| `mcr.microsoft.com` | MCR(Microsoft Container Registry) |
| `<ACR name>.azurecr.io` | ACR(Azure Container Registry) |
| `<account>.table.core.windows.net` | Azure Storage 계정(테이블 스토리지) |
| `<account>.blob.core.windows.net` | Azure Storage 계정(Blob 스토리지) |
| `api.azureml.ms` | AAD(Azure Active Directory) 인증 |
| `<leaf-domain-label + auto-generated suffix>.<region>.cloudapp.azure.com` | 엔드포인트 도메인 이름(Azure Machine Learning에서 자동 생성된 경우). 사용자 지정 도메인 이름을 사용한 경우에는 이 항목이 필요하지 않습니다. |

### <a name="connectivity-requirements-in-chronological-order-from-cluster-creation-to-model-deployment"></a>연결 요구 사항(시간순): 클러스터 만들기부터 모델 배포까지

AKS 만들기 또는 연결 과정에서 Azure ML 라우터(azureml-fe)는 AKS 클러스터에 배포됩니다. Azure ML 라우터를 배포하려면 AKS 노드가 다음을 수행할 수 있어야 합니다.
* AKS API 서버에 대한 DNS 확인
* MCR에 대한 DNS 확인(Azure ML 라우터용 docker 이미지를 다운로드하는 데 필요)
* 아웃바운드 연결이 필요한 MCR에서 이미지 다운로드

azureml-fe가 배포된 직후 시작이 시도되며, 그러려면 다음 작업이 필요합니다.
* AKS API 서버에 대한 DNS 확인
* AKS API 서버를 쿼리하여 다른 인스턴스 검색(여러 Pod 서비스임)
* 다른 인스턴스에 연결

azureml-fe가 시작된 후 제대로 작동하려면 다음 연결이 필요합니다.
* Azure Storage에 연결하여 동적 구성 다운로드
* AAD 인증 서버 api.azureml.ms에 대한 DNS를 확인하고 배포된 서비스가 AAD 인증을 사용할 때 통신합니다.
* AKS API 서버를 쿼리하여 배포된 모델 검색
* 배포된 모델 POD와 통신

모델 배포 시 성공적인 모델 배포를 위해 AKS 노드는 다음을 수행할 수 있어야 합니다. 
* 고객의 ACR에 대한 DNS 확인
* 고객의 ACR에서 이미지 다운로드
* 모델이 저장된 Azure Blob에 대한 DNS 확인
* Azure Blob에서 모델 다운로드

모델이 배포되고 서비스가 시작되면 azureml-fe는 AKS API를 사용하여 모델을 자동으로 검색하고 요청을 라우팅할 준비가 됩니다. 모델 POD와 통신할 수 있어야 합니다.
>[!Note]
>배포된 모델에 연결이 필요한 경우(예: 외부 데이터베이스 또는 기타 REST 서비스 쿼리, BLOB 다운로드 등) 해당 서비스에 대한 DNS 확인 및 아웃바운드 통신을 모두 사용하도록 설정해야 합니다.

## <a name="deploy-to-aks"></a>AKS에 배포

Azure Kubernetes Service에 모델을 배포하려면 필요한 컴퓨팅 리소스를 설명하는 __배포 구성__ 을 만듭니다. 예를 들어 코어 수 및 메모리입니다. 모델 및 웹 서비스를 호스트하는 데 필요한 환경을 설명하는 __유추 구성__ 도 필요합니다. 유추 구성을 만드는 방법에 대한 자세한 내용은 [모델을 배포하는 방법 및 위치](how-to-deploy-and-where.md)를 참조하세요.

> [!NOTE]
> 배포할 모델 수는 배포당(컨테이너당) 모델 1,000개로 제한됩니다.

<a id="using-the-cli"></a>

# <a name="python"></a>[Python](#tab/python)

```python
from azureml.core.webservice import AksWebservice, Webservice
from azureml.core.model import Model

aks_target = AksCompute(ws,"myaks")
# If deploying to a cluster configured for dev/test, ensure that it was created with enough
# cores and memory to handle this deployment configuration. Note that memory is also used by
# things such as dependencies and AML components.
deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config, aks_target)
service.wait_for_deployment(show_output = True)
print(service.state)
print(service.get_logs())
```

이 예제에 사용된 클래스, 메서드 및 매개 변수에 대한 자세한 내용은 다음 참조 문서를 확인하세요.

* [AksCompute](/python/api/azureml-core/azureml.core.compute.aks.akscompute)
* [AksWebservice.deploy_configuration](/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration)
* [Model.deploy](/python/api/azureml-core/azureml.core.model.model#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)
* [Webservice.wait_for_deployment](/python/api/azureml-core/azureml.core.webservice%28class%29#wait-for-deployment-show-output-false-)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

CLI를 사용하여 배포하려면 다음 명령을 사용합니다. `myaks`는 AKS 컴퓨팅 대상의 이름으로 바꿉니다. `mymodel:1`은 등록된 모델의 이름과 버전으로 바꿉니다. `myservice`는 이 서비스를 제공할 이름으로 바꿉니다.

```azurecli-interactive
az ml model deploy --ct myaks -m mymodel:1 -n myservice --ic inferenceconfig.json --dc deploymentconfig.json
```

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aks-deploy-config.md)]

자세한 내용은 [az ml model deploy](/cli/azure/ml/model#az_ml_model_deploy) 참조를 확인하세요.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

VS Code 사용에 대한 자세한 내용은 [VS Code 확장을 통해 AKS에 배포](how-to-manage-resources-vscode.md)를 참조하세요.

> [!IMPORTANT]
> VS Code를 통해 배포하려면 AKS 클러스터를 미리 만들거나 작업 영역에 미리 연결해야 합니다.

---

### <a name="autoscaling"></a>자동 확장

Azure ML 모델 배포에 대한 자동 스케일링을 처리하는 구성 요소는 azureml-fe이며, 이것은 스마트 요청 라우터입니다. 모든 유추 요청이 여기를 거치기 때문에, 여기에는 배포된 모델을 자동으로 스케일링하는 데 필요한 데이터가 있습니다.

> [!IMPORTANT]
> * **모델 배포에 Kubernetes HPA(Horizontal Pod Autoscaler)를 사용하도록 설정하지 마십시오**. 그러면 두 가지 자동 스케일링 구성 요소가 서로 경쟁하게 됩니다. Azureml-fe는 Azure ML에 의해 배포된 모델을 자동 스케일링하도록 설계되었으며, HPA는 CPU 사용량 또는 사용자 지정 메트릭 구성과 같은 일반 메트릭에서 모델 사용률을 추측하거나 대략적으로 추정해야 합니다.
> 
> * **Azureml-fe는 AKS 클러스터의 노드 수를 스케일링하지 않습니다.** 예상치 못한 비용 증가로 이어질 수 있기 때문입니다. 대신 물리적 클러스터 경계 내에서 **모델의 복제본 수를 스케일링** 합니다. 클러스터 내 노드 수를 스케일링해야 하는 경우 클러스터를 수동으로 스케일링하거나 [AKS 클러스터 자동 스케일러를 구성](../aks/cluster-autoscaler.md)할 수 있습니다.

자동 스케일링은 AKS 웹 서비스에 대해 `autoscale_target_utilization`, `autoscale_min_replicas`, `autoscale_max_replicas`를 설정하여 제어할 수 있습니다. 다음 예는 자동 스케일링을 사용하도록 설정하는 방법을 보여줍니다.

```python
aks_config = AksWebservice.deploy_configuration(autoscale_enabled=True, 
                                                autoscale_target_utilization=30,
                                                autoscale_min_replicas=1,
                                                autoscale_max_replicas=4)
```

스케일 업/다운 결정은 현재 컨테이너 복제본의 사용률에 기반합니다. 사용 중인(요청을 처리하는) 복제본 수를 현재 복제본의 총 수로 나눈 값이 현재 사용률입니다. 이 수가 `autoscale_target_utilization`을 초과하면 더 많은 복제본이 생성됩니다. 이보다 낮으면 복제본이 감소됩니다. 기본적으로 목표 사용률은 70%입니다.

복제본 추가에 대한 결정은 열성적이고 빠릅니다(약 1초). 복제본 제거에 대한 결정은 보수적입니다(약 1분).

필요한 복제본은 다음 코드를 사용하여 계산할 수 있습니다.

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

`autoscale_target_utilization`, `autoscale_max_replicas`, `autoscale_min_replicas` 설정에 대한 자세한 내용은 [AksWebservice](/python/api/azureml-core/azureml.core.webservice.akswebservice) 모듈 참조를 확인하세요.

## <a name="deploy-models-to-aks-using-controlled-rollout-preview"></a>제어된 롤아웃을 사용하여 AKS에 모델 배포(미리 보기)

엔드포인트를 사용하여 제어된 방식으로 모델 버전을 분석하고 승격합니다. 단일 엔드포인트 뒤에 최대 6개의 버전을 배포할 수 있습니다. 엔드포인트는 다음과 같은 기능을 제공합니다.

* __각 엔드포인트로 전송되는 채점 트래픽의 백분율__ 을 구성합니다. 예를 들어 트래픽의 20%는 엔드포인트 '테스트'로, 80%는 '프로덕션'으로 라우팅합니다.

    > [!NOTE]
    > 트래픽의 100%를 차지하지 않는 경우 나머지 비율은 __기본__ 엔드포인트 버전으로 라우팅됩니다. 예를 들어 엔드포인트 버전 'test'가 트래픽의 10%를 차지하도록 구성하고 'prod'에는 30%를 구성하면 나머지 60%는 기본 엔드포인트 버전으로 보내집니다.
    >
    > 생성된 첫 번째 엔드포인트 버전이 자동으로 기본값으로 구성됩니다. 이 내용은 엔드포인트 버전을 만들거나 업데이트할 때 `is_default=True`를 설정하여 변경할 수 있습니다.
     
* 엔드포인트 버전에 __control__ 또는 __treatment__ 중 하나로 태그를 지정합니다. 예를 들어 현재 프로덕션 엔드포인트 버전은 control이 될 수 있지만 잠재적 새 모델은 treatment 버전으로 배포됩니다. treatment 버전의 성능을 평가한 후 현재 control보다 성능이 뛰어나면 새 프로덕션/control로 승격될 수 있습니다.

    > [!NOTE]
    > control은 __하나__ 만 있을 수 있습니다. treatment는 여러 개 있을 수 있습니다.

App insights를 사용하도록 설정하여 엔드포인트 및 배포된 버전의 운영 메트릭을 볼 수 있습니다.

### <a name="create-an-endpoint"></a>엔드포인트 만들기
모델을 배포할 준비가 되면 채점 엔드포인트를 만들고 첫 번째 버전을 배포합니다. 다음 예제는 SDK를 사용하여 엔드포인트를 배포하고 만드는 방법을 보여줍니다. 첫 번째 배포는 기본 버전으로 정의됩니다. 즉, 모든 버전에서 지정되지 않은 트래픽 백분위 수가 기본 버전으로 이동합니다.  

> [!TIP]
> 다음 예제에서 구성은 초기 엔드포인트 버전이 트래픽의 20%를 처리하도록 설정합니다. 이 엔드포인트는 첫 번째 엔드포인트이므로 기본 버전이기도 합니다. 그리고 나머지 80%의 트래픽에 대한 다른 버전은 없기 때문에 기본값으로 라우팅됩니다. 트래픽의 일정 비율을 차지하는 다른 버전이 배포될 때까지 이 버전이 사실상 트래픽의 100%를 수신합니다.

```python
import azureml.core,
from azureml.core.webservice import AksEndpoint
from azureml.core.compute import AksCompute
from azureml.core.compute import ComputeTarget
# select a created compute
compute = ComputeTarget(ws, 'myaks')

# define the endpoint and version name
endpoint_name = "mynewendpoint"
version_name= "versiona"
# create the deployment config and define the scoring traffic percentile for the first deployment
endpoint_deployment_config = AksEndpoint.deploy_configuration(cpu_cores = 0.1, memory_gb = 0.2,
                                                              enable_app_insights = True,
                                                              tags = {'sckitlearn':'demo'},
                                                              description = "testing versions",
                                                              version_name = version_name,
                                                              traffic_percentile = 20)
 # deploy the model and endpoint
 endpoint = Model.deploy(ws, endpoint_name, [model], inference_config, endpoint_deployment_config, compute)
 # Wait for he process to complete
 endpoint.wait_for_deployment(True)
 ```

### <a name="update-and-add-versions-to-an-endpoint"></a>업데이트 및 엔드포인트에 버전 추가

엔드포인트에 다른 버전을 추가하고 그 버전으로 이동하는 채점 트래픽 백분위 수를 구성합니다. 버전의 유형에는 두 가지, 즉 control과 treatment 버전이 있습니다. control 버전 하나와 비교하는 데 도움이 되는 여러 treatment 버전이 있을 수 있습니다.

> [!TIP]
> 다음 코드 조각으로 생성된 두 번째 버전은 트래픽의 10%를 받습니다. 첫 번째 버전은 20%로 구성되어 있기 때문에 트래픽의 30%만 특정 버전에 대해 구성됩니다. 나머지 70%는 첫 번째 엔드포인트 버전으로 보내집니다. 첫 번째 엔드포인트가 기본 버전이기도 하기 때문입니다.

 ```python
from azureml.core.webservice import AksEndpoint

# add another model deployment to the same endpoint as above
version_name_add = "versionb"
endpoint.create_version(version_name = version_name_add,
                        inference_config=inference_config,
                        models=[model],
                        tags = {'modelVersion':'b'},
                        description = "my second version",
                        traffic_percentile = 10)
endpoint.wait_for_deployment(True)
```

기존 버전을 업데이트하거나 엔드포인트에서 삭제합니다. 버전의 기본 유형, 컨트롤 형식 및 트래픽 백분위 수를 변경할 수 있습니다. 다음 예제에서는 두 번째 버전의 트래픽이 40%로 증가하여 이제는 기본값입니다.

> [!TIP]
> 다음 코드 조각 후에는 이제 두 번째 버전이 기본값입니다. 이제 40%로 구성되어 있지만 원래 버전은 아직 20%로 구성되어 있습니다. 즉, 버전 구성에서는 트래픽의 40%를 차지하지 않습니다. 남은 트래픽은 두 번째 버전으로 라우팅됩니다. 이제 기본값이기 때문입니다. 사실상 트래픽의 80%를 받습니다.

 ```python
from azureml.core.webservice import AksEndpoint

# update the version's scoring traffic percentage and if it is a default or control type
endpoint.update_version(version_name=endpoint.versions["versionb"].name,
                        description="my second version update",
                        traffic_percentile=40,
                        is_default=True,
                        is_control_version_type=True)
# Wait for the process to complete before deleting
endpoint.wait_for_deployment(true)
# delete a version in an endpoint
endpoint.delete_version(version_name="versionb")

```

## <a name="web-service-authentication"></a>웹 서비스 인증

Azure Kubernetes Service에 배포할 때 __키 기반__ 인증이 기본적으로 사용하도록 설정되어 있습니다. __토큰 기반__ 인증을 사용하도록 설정할 수도 있습니다. 토큰 기반 인증을 사용하려면 클라이언트가 Azure Active Directory 계정을 사용하여 인증 토큰(배포된 서비스에 대한 요청을 수행하는 데 사용됨)을 요청해야 합니다.

인증을 __사용하지 않도록 설정__ 하려면 배포 구성을 만들 때 `auth_enabled=False` 매개 변수를 설정합니다. 다음 예제에서는 SDK를 사용하여 인증을 사용하지 않도록 설정합니다.

```python
deployment_config = AksWebservice.deploy_configuration(cpu_cores=1, memory_gb=1, auth_enabled=False)
```

클라이언트 애플리케이션에서 인증하는 방법에 대한 정보는 [웹 서비스로 배포된 Azure Machine Learning 모델 사용](how-to-consume-web-service.md)을 참조하세요.

### <a name="authentication-with-keys"></a>키로 인증

키 인증을 사용하도록 설정한 경우 `get_keys` 메서드를 사용하여 기본 및 보조 인증 키를 검색할 수 있습니다.

```python
primary, secondary = service.get_keys()
print(primary)
```

> [!IMPORTANT]
> 키를 다시 생성해야 하는 경우 [`service.regen_key`](/python/api/azureml-core/azureml.core.webservice%28class%29)를 사용합니다.

### <a name="authentication-with-tokens"></a>토큰으로 인증

토큰 인증을 사용하도록 설정하려면 배포를 만들거나 업데이트할 때 `token_auth_enabled=True` 매개 변수를 설정합니다. 다음 예제에서는 SDK를 사용하여 토큰 인증을 사용하도록 설정합니다.

```python
deployment_config = AksWebservice.deploy_configuration(cpu_cores=1, memory_gb=1, token_auth_enabled=True)
```

토큰 인증을 사용하도록 설정하면 `get_token` 메서드를 사용하여 JWT 토큰 및 이 토큰의 만료 시간을 검색할 수 있습니다.

```python
token, refresh_by = service.get_token()
print(token)
```

> [!IMPORTANT]
> 토큰의 `refresh_by` 시간 후 새 토큰을 요청해야 합니다.
>
> Azure Machine Learning 작업 영역은 Azure Kubernetes Service 클러스터와 동일한 지역에 만드는 것이 좋습니다. 토큰으로 인증하기 위해 웹 서비스는 Azure Machine Learning 작업 영역이 생성되는 지역을 호출합니다. 작업 영역의 지역을 사용할 수 없는 경우에는 클러스터가 작업 영역과 다른 지역에 있더라도 웹 서비스에 대한 토큰을 가져올 수 없습니다. 따라서 작업 영역의 지역을 다시 사용할 수 있을 때까지 사실상 토큰 기반 인증을 사용할 수 없습니다. 또한 클러스터 지역과 작업 영역의 지역 간 거리가 멀수록 토큰을 가져오는 데 시간이 오래 걸립니다.
>
> 토큰을 검색하려면 Azure Machine Learning SDK 또는 [az ml service get-access-token](/cli/azure/ml(v1)/computetarget/create#az_ml_service_get_access_token) 명령을 사용해야 합니다.


### <a name="vulnerability-scanning"></a>취약성 검색

Azure Security Center는 하이브리드 클라우드 워크로드에 통합 보안 관리 및 고급 위협 방지를 제공합니다. Azure Security Center가 리소스를 검색할 수 있게 허용하고 권장 사항에 따라야 합니다. 자세한 내용은 [Security Center와 Azure Kubernetes Services 통합](../security-center/defender-for-kubernetes-introduction.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

* [Kubernetes 권한 부여에 Azure RBAC 사용](../aks/manage-azure-rbac.md)
* [Azure Virtual Network를 사용한 보안 추론 환경](how-to-secure-inferencing-vnet.md)
* [사용자 지정 Docker 이미지를 사용하여 모델을 배포하는 방법](./how-to-deploy-custom-container.md)
* [배포 문제 해결](how-to-troubleshoot-deployment.md)
* [웹 서비스 업데이트](how-to-deploy-update-web-service.md)
* [TLS를 사용하여 Azure Machine Learning을 통해 웹 서비스 보호](how-to-secure-web-service.md)
* [웹 서비스로 배포된 ML 모델 사용](how-to-consume-web-service.md)
* [Application Insights를 사용하여 Azure Machine Learning 모델 모니터링](how-to-enable-app-insights.md)
* [프로덕션 환경에서 모델용 데이터 수집](how-to-enable-data-collection.md)