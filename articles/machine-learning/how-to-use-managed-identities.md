---
title: 액세스 제어에 대한 관리 ID 사용(미리 보기)
titleSuffix: Azure Machine Learning
description: 관리 ID를 사용하여 Azure Machine Learning 작업 영역에서 Azure 리소스에 대한 액세스를 제어하는 방법을 알아봅니다.
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: enterprise-readiness
ms.reviewer: larryfr
ms.topic: how-to
ms.date: 10/22/2020
ms.openlocfilehash: 37f0cff74b3918cc81b099149fff485dba405939
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/04/2021
ms.locfileid: "129424069"
---
# <a name="use-managed-identities-with-azure-machine-learning-preview"></a>Azure Machine Learning에서 관리 ID 사용(미리 보기)

[관리 ID](../active-directory/managed-identities-azure-resources/overview.md)를 사용하면 *리소스에 액세스하는 데 필요한 최소 권한* 으로 작업 영역을 구성할 수 있습니다. 

Azure Machine Learning 작업 영역을 신뢰할 수 있는 방식으로 구성하는 경우 작업 영역과 연결된 여러 서비스에 올바른 액세스 수준이 있는지 확인하는 것이 중요합니다. 예를 들어 기계 학습 워크플로 중 작업 영역에서 Docker 이미지에 대한 ACR(Azure Container Registry)에 액세스하고 학습 데이터에 대한 스토리지 계정을 사용해야 합니다. 

또한 관리 ID를 사용하면 사용 권한에 대한 세분화된 제어를 사용할 수 있습니다. 예를 들어 특정 컴퓨팅 리소스의 액세스 권한을 특정 ACR에 부여하거나 취소할 수 있습니다.

이 문서에서는 관리 ID를 사용하여 다음을 수행하는 방법을 알아봅니다.

 * ACR에 대한 관리 사용자 액세스를 사용하도록 설정하지 않고도 Azure Machine Learning 작업 영역에 대한 ACR을 구성하고 사용합니다.
 * 작업 영역 외부의 프라이빗 ACR에 액세스하여 학습 또는 유추를 위한 기본 이미지를 끌어옵니다.
 * 연결된 리소스에 액세스하기 위해 사용자가 할당한 관리 ID를 사용하여 작업 영역을 만듭니다.

> [!IMPORTANT]
> 관리 ID를 사용하여 Azure Machine Learning이 있는 리소스에 대한 액세스를 제어하는 기능은 현재 미리 보기로 제공됩니다. 미리 보기 기능은 지원 또는 서비스 수준 계약의 보증 없이 “있는 그대로” 제공됩니다. 자세한 내용은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.
 
## <a name="prerequisites"></a>사전 요구 사항

- Azure Machine Learning 작업 영역 자세한 내용은 [Azure Machine Learning 작업 영역 만들기](how-to-manage-workspace.md)를 참조하세요.
- [Machine Learning Service를 위한 Azure CLI 확장](reference-azure-machine-learning-cli.md)
- [Azure Machine Learning Python SDK](/python/api/overview/azure/ml/intro).
- 역할을 할당하려면 Azure 구독에 대한 로그인에 [관리 ID 운영자](../role-based-access-control/built-in-roles.md#managed-identity-operator) 역할 또는 필요한 작업을 부여하는 다른 역할(예: __소유자__)이 있어야 합니다.
- [관리 ID](../active-directory/managed-identities-azure-resources/overview.md)를 만들고 사용하는 방법에 대해 잘 알고 있어야 합니다.

## <a name="configure-managed-identities"></a>관리되는 ID 구성

일부 경우에는 Azure Container Registry에 대한 관리 사용자 액세스를 허용하지 않아야 합니다. 예를 들어 ACR을 공유하고 다른 사용자의 관리자 액세스를 허용하지 말아야 하는 경우가 있습니다. 또는 관리 사용자가 사용하도록 설정된 ACR 만들기는 구독 수준 정책에 의해 허용되지 않습니다.

> [!IMPORTANT]
> ACI(Azure Container Instance)에서 유추를 위해 Azure Machine Learning을 사용하는 경우 ACR에 대한 관리자 액세스 권한이 __필요__ 합니다. 유추를 위해 모델을 ACI에 배포하려는 경우에는 사용하지 않도록 설정합니다.

관리 사용자 액세스를 사용하도록 설정하지 않고 ACR을 만드는 경우 관리 ID를 사용하여 ACR에 액세스하여 Docker 이미지를 빌드하고 끌어옵니다.

작업 영역을 만들 때 관리 사용자를 사용하지 않도록 설정된 사용자 고유의 ACR를 가져올 수 있습니다. 또는 Azure Machine Learning에서 작업 영역 ACR을 만들고 나중에 관리 사용자를 사용하지 않도록 설정할 수 있습니다.

### <a name="bring-your-own-acr"></a>사용자 고유의 ACR 가져오기

ACR 관리 사용자가 구독 정책에 의해 허용되지 않는 경우 먼저 관리 사용자 없이 ACR를 만든 다음, 작업 영역에 연결해야 합니다. 또한 관리 사용자를 사용하지 않도록 설정된 기존 ACR이 있는 경우에는 이를 작업 영역에 연결할 수 있습니다.

```--admin-enabled``` 인수를 설정하지 않고 [Azure CLI에서 ACR을 만들거나](../container-registry/container-registry-get-started-azure-cli.md) 관리 사용자를 사용하도록 설정하지 않고 Azure Portal에서 ACR을 만듭니다. 그런 다음, Azure Machine Learning 작업 영역을 만들 때 ACR의 Azure 리소스 ID를 지정합니다. 다음 예제에서는 기존 ACR을 사용하는 새 Azure ML 작업 영역을 만드는 방법을 보여 줍니다.

> [!TIP]
> `--container-registry` 매개 변수에 대한 값을 가져오려면 [az acr show](/cli/azure/acr#az_acr_show) 명령을 사용하여 ACR에 대한 정보를 표시합니다. 이 `id` 필드에는 ACR의 리소스 ID가 포함되어 있습니다.

```azurecli-interactive
az ml workspace create -w <workspace name> \
-g <workspace resource group> \
-l <region> \
--container-registry /subscriptions/<subscription id>/resourceGroups/<acr resource group>/providers/Microsoft.ContainerRegistry/registries/<acr name>
```

### <a name="let-azure-machine-learning-service-create-workspace-acr"></a>Azure Machine Learning Service에서 작업 영역 ACR 만들기

사용자 고유의 ACR을 가져오지 않으면 ACR이 필요한 작업을 수행하는 경우 사용자를 위해 Azure Machine Learning Service에서 하나가 생성됩니다. 예를 들어 Machine Learning 컴퓨팅에 학습 실행을 제출하거나, 환경을 빌드하거나, 웹 서비스 엔드포인트를 배포합니다. 작업 영역에서 만든 ACR은 관리 사용자를 사용하도록 설정되며, 관리 사용자는 수동으로 사용하지 않도록 설정해야 합니다.

1. 새 작업 영역 만들기

    ```azurecli-interactive
    az ml workspace show -n <my workspace> -g <my resource group>
    ```

1. ACR이 필요한 작업을 수행합니다. 예제는 [모델을 학습하는 방법에 대한 자습서](tutorial-train-models-with-aml.md)를 참조하세요.

1. 클러스터에서 만든 ACR 이름을 가져옵니다.

    ```azurecli-interactive
    az ml workspace show -w <my workspace> \
    -g <my resource group>
    --query containerRegistry
    ```

    이 명령은 다음 텍스트와 유사한 값을 반환합니다. ACR 인스턴스 이름인 텍스트의 마지막 부분만 필요합니다.

    ```output
    /subscriptions/<subscription id>/resourceGroups/<my resource group>/providers/MicrosoftContainerReggistry/registries/<ACR instance name>
    ```

1. ACR을 업데이트하여 관리 사용자를 사용하지 않도록 설정합니다.

    ```azurecli-interactive
    az acr update --name <ACR instance name> --admin-enabled false
    ```

### <a name="create-compute-with-managed-identity-to-access-docker-images-for-training"></a>관리 ID를 사용하여 컴퓨팅을 만들어 학습용 Docker 이미지에 액세스

작업 영역 ACR에 액세스하려면 시스템 할당 관리 ID가 사용하도록 설정된 기계 학습 컴퓨팅 클러스터를 만듭니다. 컴퓨팅을 만들 때 Azure Portal 또는 스튜디오에서, 또는 아래를 사용하여 Azure CLI에서 ID를 사용하도록 설정할 수 있습니다. 자세한 내용은 [컴퓨팅 클러스터에서 관리 ID 사용](how-to-create-attach-compute-cluster.md#managed-identity)을 참조하세요.

# <a name="python"></a>[Python](#tab/python)

[AmlComputeProvisioningConfiguration](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcomputeprovisioningconfiguration)을 사용하여 컴퓨팅 클러스터를 만들 때 `identity_type` 매개 변수를 사용하여 관리 ID 유형을 설정합니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interaction
az ml computetarget create amlcompute --name cpucluster -w <workspace> -g <resource group> --vm-size <vm sku> --assign-identity '[system]'
```

# <a name="portal"></a>[포털](#tab/azure-portal)

스튜디오에서 컴퓨팅 클러스터를 만들 때 관리 ID를 구성하는 방법에 대한 자세한 내용은 [관리 ID 설정](how-to-create-attach-compute-studio.md#managed-identity)을 참조하세요.

---

관리 ID는 작업 영역 ACR에서 자동으로 ACRPull 역할이 부여되어 학습용 Docker 이미지를 끌어올 수 있습니다.

> [!NOTE]
> 컴퓨팅을 먼저 만든 경우 작업 영역 ACR을 만들기 전에 ACRPull 역할을 수동으로 할당해야 합니다.

## <a name="access-base-images-from-private-acr"></a>프라이빗 ACR에서 기본 이미지 액세스

기본적으로 Azure Machine Learning은 Microsoft에서 관리하는 공용 리포지토리에서 제공되는 Docker 기본 이미지를 사용합니다. 그런 다음, 해당 이미지에 대한 학습 또는 유추 환경을 빌드합니다. 자세한 내용은 [ML 환경이란?](concept-environments.md)을 참조하세요.

회사 내부의 사용자 지정 기본 이미지를 사용하려면 관리 ID를 사용하여 프라이빗 ACR에 액세스할 수 있습니다. 두 가지 사용 사례가 있습니다.

 * 기본 이미지를 학습에 그대로 사용합니다.
 * 사용자 지정 이미지를 기반으로 Azure Machine Learning 관리 이미지를 빌드합니다.

### <a name="pull-docker-base-image-to-machine-learning-compute-cluster-for-training-as-is"></a>학습용 Docker 기본 이미지를 기계 학습 컴퓨팅 클러스터로 그대로 끌어옵니다.

이전에 설명된 대로 시스템 할당 관리 ID가 사용하도록 설정된 기계 학습 컴퓨팅 클러스터를 만듭니다. 그런 다음, 관리 ID의 보안 주체 ID를 확인합니다.

```azurecli-interactive
az ml computetarget amlcompute identity show --name <cluster name> -w <workspace> -g <resource group>
```

필요에 따라 사용자가 할당한 관리 ID를 할당하도록 컴퓨팅 클러스터를 업데이트할 수 있습니다.

```azurecli-interactive
az ml computetarget amlcompute identity assign --name cpucluster \
-w $mlws -g $mlrg --identities <my-identity-id>
```

컴퓨팅 클러스터가 기본 이미지를 끌어올 수 있도록 하려면 프라이빗 ACR에서 관리되는 서비스 ID ACRPull 역할을 부여합니다.

```azurecli-interactive
az role assignment create --assignee <principal ID> \
--role acrpull \
--scope "/subscriptions/<subscription ID>/resourceGroups/<private ACR resource group>/providers/Microsoft.ContainerRegistry/registries/<private ACR name>"
```

마지막으로 학습 실행을 제출할 때 [환경 정의](how-to-use-environments.md#use-existing-environments)에서 기본 이미지 위치를 지정합니다.

```python
from azureml.core import Environment
env = Environment(name="private-acr")
env.docker.base_image = "<ACR name>.azurecr.io/<base image repository>/<base image version>"
env.python.user_managed_dependencies = True
```

> [!IMPORTANT]
> 기본 이미지를 컴퓨팅 리소스로 직접 끌어올 수 있도록 하려면 `user_managed_dependencies = True`를 설정하고 Dockerfile을 지정하지 않습니다. 그렇지 않으면 컴퓨팅 클러스터만 ACR에서 기본 이미지를 끌어올 수 있는 액세스 권한이 있기 때문에 Azure Machine Learning Service에서는 새 Docker 이미지를 빌드하려고 시도하고 실패합니다.

### <a name="build-azure-machine-learning-managed-environment-into-base-image-from-private-acr-for-training-or-inference"></a>학습 또는 유추를 위해 프라이빗 ACR의 기본 이미지에 Azure Machine Learning 관리되는 환경 빌드

이 시나리오에서 Azure Machine Learning Service는 프라이빗 ACR에서 제공하는 기본 이미지 위에 학습 또는 유추 환경을 빌드합니다. 이미지 빌드 작업은 ACR 작업을 사용하는 작업 영역 ACR에서 발생하기 때문에 액세스를 허용하려면 추가 단계를 수행해야 합니다.

1. __사용자가 할당한 관리 ID__ 를 만들고 __프라이빗 ACR__ 에 ID ACRPull 액세스 권한을 부여합니다.  
1. 작업 영역 __시스템 할당 관리 ID__ 를 이전 단계의 __사용자가 할당한 관리 ID__ 에 대한 관리 ID 운영자 역할에 부여합니다. 이 역할을 통해 작업 영역에서 관리되는 환경을 빌드하기 위한 ACR 작업에 사용자가 할당한 관리 ID를 할당할 수 있습니다. 

    1. 작업 영역 시스템 할당 관리 ID의 보안 주체 ID를 가져옵니다.

        ```azurecli-interactive
        az ml workspace show -w <workspace name> -g <resource group> --query identityPrincipalId
        ```

    1. 관리 ID 운영자 역할을 부여합니다.

        ```azurecli-interactive
        az role assignment create --assignee <principal ID> --role managedidentityoperator --scope <UAI resource ID>
        ```

        UAI 리소스 ID는 `/subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<UAI name>` 형식으로 사용자 할당 ID의 Azure 리소스 ID입니다.

1. [Workspace.set_connection 메서드](/python/api/azureml-core/azureml.core.workspace.workspace#set-connection-name--category--target--authtype--value-)를 사용하여 작업 영역 연결에서 __사용자가 할당한 관리 ID__ 의 외부 ACR 및 클라이언트 ID를 지정합니다.

    ```python
    workspace.set_connection(
        name="privateAcr", 
        category="ACR", 
        target = "<acr url>", 
        authType = "RegistryConnection", 
        value={"ResourceId": "<UAI resource id>", "ClientId": "<UAI client ID>"})
    ```

구성이 완료되면 학습 또는 유추를 위한 환경을 빌드할 때 프라이빗 ACR의 기본 이미지를 사용할 수 있습니다. 다음 코드 조각에서는 환경 정의에서 기본 이미지 ACR 및 이미지 이름을 지정하는 방법을 보여 줍니다.

```python
from azureml.core import Environment

env = Environment(name="my-env")
env.docker.base_image = "<acr url>/my-repo/my-image:latest"
```

필요에 따라 [RegistryIdentity](/python/api/azureml-core/azureml.core.container_registry.registryidentity)를 사용하여 환경 정의 자체에서 관리 ID 리소스 URL 및 클라이언트 ID를 지정할 수 있습니다. 명시적으로 레지스트리 ID를 사용하는 경우 이전에 지정된 작업 영역 연결을 재정의합니다.

```python
from azureml.core.container_registry import RegistryIdentity

identity = RegistryIdentity()
identity.resource_id= "<UAI resource ID>"
identity.client_id="<UAI client ID>"
env.docker.base_image_registry.registry_identity=identity
env.docker.base_image = "my-acr.azurecr.io/my-repo/my-image:latest"
```

## <a name="use-docker-images-for-inference"></a>유추를 위한 Docker 이미지 사용

앞서 설명한 대로 관리 사용자 없이 ACR을 구성하면 AKS(Azure Kubernetes Service)에서 관리 키 없이 유추를 위해 Docker 이미지에 액세스할 수 있습니다. AKS를 만들거나 작업 영역에 연결하면 클러스터의 서비스 주체에 작업 영역 ACR에 대한 ACRPull 액세스 권한이 자동으로 할당됩니다.

> [!NOTE]
> 사용자 고유의 AKS 클러스터를 가져오는 경우 클러스터에서 관리 ID 대신 서비스 주체를 사용하도록 설정해야 합니다.

## <a name="create-workspace-with-user-assigned-managed-identity"></a>사용자가 할당한 관리 ID를 사용하여 작업 영역 만들기

작업 영역을 만들 때 ACR, KeyVault, Storage 및 App Insights와 같은 연결된 리소스에 액세스하는 데 사용되는 사용자가 할당한 관리 ID를 지정할 수 있습니다.

먼저 [사용자가 할당한 관리 ID를 만들고](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md) 관리 ID의 ARM 리소스 ID를 기록해 둡니다.

그런 다음, Azure CLI 또는 Python SDK를 사용하여 작업 영역을 만듭니다. CLI를 사용하는 경우 `--primary-user-assigned-identity` 매개 변수를 사용하여 ID를 지정합니다. SDK를 사용하는 경우 `primary_user_assigned_identity`를 사용합니다. 다음은 Azure CLI 및 Python을 사용하여 다음과 같은 매개 변수를 사용하는 새 작업 영역을 만드는 예제입니다.

__Azure CLI__

```azurecli-interactive
az ml workspace create -w <workspace name> -g <resource group> --primary-user-assigned-identity <managed identity ARM ID>
```

__Python__

```python
from azureml.core import Workspace

ws = Workspace.create(name="workspace name", 
    subscription_id="subscription id", 
    resource_group="resource group name",
    primary_user_assigned_identity="managed identity ARM ID")
```

[ARM 템플릿](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.machinelearningservices/machine-learning-advanced)을 사용하여 사용자가 할당한 관리 ID가 있는 작업 영역을 만들 수도 있습니다.

> [!IMPORTANT]
> 사용자 고유의 연결된 리소스를 가져오는 경우 Azure Machine Learning Service에서 만드는 대신 해당 리소스에 대한 관리 ID 역할을 부여해야 합니다. [역할 할당 ARM 템플릿](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.machinelearningservices/machine-learning-dependencies-role-assignment)을 사용하여 할당을 만듭니다.

[암호화를 위한 사용자 관리 키](concept-data-encryption.md)를 사용하는 작업 영역의 경우 사용자가 할당한 관리 ID를 전달하여 저장소에서 Key Vault로 인증할 수 있습니다. __user-assigned-identity-for-cmk-encryption__(CLI) 또는 __user_assigned_identity_for_cmk_encryption__(SDK) 인수를 사용하여 관리 ID를 전달합니다. 이 관리 ID는 작업 영역 기본 사용자가 할당한 관리 ID와 동일하거나 다를 수 있습니다.

기존 작업 영역이 있는 경우 ```az ml workspace update``` CLI 명령 또는 ```Workspace.update``` Python SDK 메서드를 사용하여 시스템 할당에서 사용자가 할당한 관리 ID로 업데이트할 수 있습니다.


## <a name="next-steps"></a>다음 단계

* [Azure Machine Learning의 엔터프라이즈 보안](concept-enterprise-security.md)에 대해 자세히 알아봅니다.
