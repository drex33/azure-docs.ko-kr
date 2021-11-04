---
title: Azure Container Instances에 모델 배포하는 방법
titleSuffix: Azure Machine Learning
description: Azure Container Instances를 사용하여 Azure Machine Learning 모델을 웹 서비스로 배포하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: mlops
ms.topic: how-to
ms.custom: deploy
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 10/21/2021
ms.openlocfilehash: 148cce3452bdf27a2a8962aa5f7b58d924a8eb8f
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131553807"
---
# <a name="deploy-a-model-to-azure-container-instances"></a>Azure Container Instances에 모델 배포

Azure Machine Learning을 사용하여 ACI(Azure Container Instances)에서 모델을 웹 서비스로 배포하는 방법을 알아봅니다. 다음과 같은 경우 Azure Container Instances를 사용합니다.

- 자체 Kubernetes 클러스터를 관리하지 않는 것을 선호하는 경우
- 가동 시간에 영향을 줄 수 있는 서비스 복제본이 하나만 있어도 괜찮은 경우

ACI에 대한 할당량 및 지역 가용성에 대한 정보는 [Azure Container Instances에 대한 할당량 및 지역 가용성](../container-instances/container-instances-quotas.md) 문서를 참조하세요.

> [!IMPORTANT]
> 웹 서비스로 배포하기 전에 로컬로 디버그하는 것이 좋습니다. 자세한 내용은 [로컬로 디버그](./how-to-troubleshoot-deployment-local.md)를 참조하세요.
>
> 또한 Azure Machine Learning - [로컬 Notebook에 배포](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/deployment/deploy-to-local)를 참조할 수 있습니다.

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

## <a name="limitations"></a>제한 사항

* 가상 네트워크에서 Azure Container Instances를 사용하는 경우 가상 네트워크는 Azure Machine Learning 작업 영역과 동일한 리소스 그룹에 있어야 합니다.
* 가상 네트워크 내에서 Azure Container Instances를 사용하는 경우 작업 영역에 대한 ACR(Azure Container Registry)은 가상 네트워크에 있을 수 없습니다.

자세한 내용은 [가상 네트워크를 통한 유추 보안 방법](how-to-secure-inferencing-vnet.md#enable-azure-container-instances-aci)을 참조하세요.

## <a name="deploy-to-aci"></a>ACI에 배포

Azure Container Instances에 모델을 배포하려면 필요한 컴퓨팅 리소스를 설명하는 __배포 구성__ 을 만듭니다. 예를 들어 코어 수 및 메모리입니다. 모델 및 웹 서비스를 호스트하는 데 필요한 환경을 설명하는 __유추 구성__ 도 필요합니다. 유추 구성을 만드는 방법에 대한 자세한 내용은 [모델을 배포하는 방법 및 위치](how-to-deploy-and-where.md)를 참조하세요.

> [!NOTE]
> * ACI는 크기가 1GB 미만인 작은 모델에만 적합합니다. 
> * 더 큰 개발/테스트 모델에는 단일 노드 AKS를 사용하는 것이 좋습니다.
> * 배포할 모델 수는 배포당(컨테이너당) 모델 1,000개로 제한됩니다. 

### <a name="using-the-sdk"></a>SDK 사용

```python
from azureml.core.webservice import AciWebservice, Webservice
from azureml.core.model import Model

deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)
service = Model.deploy(ws, "aciservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

이 예제에 사용된 클래스, 메서드 및 매개 변수에 대한 자세한 내용은 다음 참조 문서를 확인하세요.

* [AciWebservice.deploy_configuration](/python/api/azureml-core/azureml.core.webservice.aciwebservice#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none-)
* [Model.deploy](/python/api/azureml-core/azureml.core.model.model#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)
* [Webservice.wait_for_deployment](/python/api/azureml-core/azureml.core.webservice%28class%29#wait-for-deployment-show-output-false-)

### <a name="using-the-azure-cli"></a>Azure CLI 사용

CLI를 사용하여 배포하려면 다음 명령을 사용합니다. `mymodel:1`은 등록된 모델의 이름과 버전으로 바꿉니다. `myservice`는 이 서비스를 제공할 이름으로 바꿉니다.

```azurecli-interactive
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json
```

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aci-deploy-config.md)]

자세한 내용은 [az ml model deploy](/cli/azure/ml/model#az_ml_model_deploy) 참조를 확인하세요. 

## <a name="using-vs-code"></a>VS Code 사용

[VS Code에서 리소스를 관리하는 방법](how-to-manage-resources-vscode.md)을 참조하세요.

> [!IMPORTANT]
> 테스트를 위해 미리 ACI 컨테이너를 만들 필요가 없습니다. ACI 컨테이너는 필요에 따라 만들어집니다.

> [!IMPORTANT]
> 생성된 모든 기본 ACI 리소스에 해시된 작업 영역 ID를 추가했습니다. 동일한 작업 영역의 모든 ACI 이름에는 동일한 접미사가 사용됩니다. Azure Machine Learning 서비스 이름은 "service_name"을 제공하는 동일한 고객이며 Azure Machine Learning SDK API를 사용하는 모든 사용자는 변경할 필요가 없습니다. 생성되는 기본 리소스의 이름에 대해서는 어떠한 보증도 하지 않습니다.

## <a name="next-steps"></a>다음 단계

* [사용자 지정 Docker 이미지를 사용하여 모델을 배포하는 방법](./how-to-deploy-custom-container.md)
* [배포 문제 해결](how-to-troubleshoot-deployment.md)
* [웹 서비스 업데이트](how-to-deploy-update-web-service.md)
* [TLS를 사용하여 Azure Machine Learning을 통해 웹 서비스 보호](how-to-secure-web-service.md)
* [웹 서비스로 배포된 ML 모델 사용](how-to-consume-web-service.md)
* [Application Insights를 사용하여 Azure Machine Learning 모델 모니터링](how-to-enable-app-insights.md)
* [프로덕션 환경에서 모델용 데이터 수집](how-to-enable-data-collection.md)