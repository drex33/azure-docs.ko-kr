---
title: 프라이빗 엔드포인트 구성
titleSuffix: Azure Machine Learning
description: 프라이빗 엔드포인트를 사용하여 가상 네트워크에서 Azure Machine Learning 작업 영역에 안전하게 액세스할 수 있습니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.custom: devx-track-azurecli
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 07/01/2021
ms.openlocfilehash: 1c07e96a82814e59c635a592313e461d06a6fcc3
ms.sourcegitcommit: 6bd31ec35ac44d79debfe98a3ef32fb3522e3934
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2021
ms.locfileid: "113217470"
---
# <a name="configure-a-private-endpoint-for-an-azure-machine-learning-workspace"></a>Azure Machine Learning 작업 영역용 프라이빗 엔드포인트 구성

이 문서에서는 Azure Machine Learning 작업 영역용 프라이빗 엔드포인트를 구성하는 방법을 알아봅니다. Azure Machine Learning용 가상 네트워크를 만드는 방법에 대한 자세한 내용은 [가상 네트워크 격리 및 개인 정보 개요](how-to-network-security-overview.md)를 참조하세요.

Azure Private Link를 사용하면 프라이빗 엔드포인트를 사용하여 작업 영역에 연결할 수 있습니다. 프라이빗 엔드포인트는 가상 네트워크 내에 있는 일련의 개인 IP 주소입니다. 그런 다음, 작업 영역에 대한 액세스를 개인 IP 주소를 통해서만 발생하도록 제한할 수 있습니다. 프라이빗 엔드포인트를 사용하면 데이터 반출 위험을 줄이는 데 도움이 됩니다. 프라이빗 엔드포인트에 대한 자세한 내용은 [Azure Private Link](../private-link/private-link-overview.md) 문서를 참조하세요.

> [!WARNING]
> 프라이빗 엔드포인트를 사용하여 작업 영역을 보호해도 완전한 보안이 보장되지는 않습니다. 솔루션의 개별 구성 요소를 모두 보호해야 합니다. 예를 들어 작업 영역에 프라이빗 엔드포인트를 사용하지만 Azure Storage 계정이 VNet 뒤에 있지 않은 경우 작업 영역과 스토리지 간 트래픽은 보안을 위해 VNet을 사용하지 않습니다.
>
> Azure Machine Learning에서 사용하는 리소스를 보호하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.
>
> * [가상 네트워크 격리 및 개인 정보 개요](how-to-network-security-overview.md)
> * [작업 영역 리소스 보호](how-to-secure-workspace-vnet.md)
> * [학습 환경 보호](how-to-secure-training-vnet.md)
> * [유추 환경 보호](how-to-secure-inferencing-vnet.md)
> * [VNet에서 Azure Machine Learning 스튜디오 사용](how-to-enable-studio-virtual-network.md)

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [cli-version-info](../../includes/machine-learning-cli-version-1-only.md)]

* 프라이빗 엔드포인트를 만들 기존 가상 네트워크가 있어야 합니다. 또한 프라이빗 엔드포인트를 추가하기 전에 [프라이빗 엔드포인트에 대한 네트워크 정책을 사용하지 않도록 설정](../private-link/disable-private-endpoint-network-policy.md)해야 합니다.

## <a name="limitations"></a>제한 사항

* Azure Government 지역에서는 프라이빗 엔드포인트로 Azure Machine Learning 작업 영역을 사용할 수 없습니다.
* 프라이빗 엔드포인트로 보호된 작업 영역에 대한 퍼블릭 액세스를 사용하도록 설정하고 퍼블릭 인터넷을 통해 Azure Machine Learning 스튜디오를 사용하는 경우, 디자이너와 같은 일부 기능에서 데이터에 액세스하지 못할 수 있습니다. 이 문제는 VNet 뒤에서 보호되는 서비스에 데이터를 저장하는 경우에 발생합니다. 예를 들어 Azure Storage 계정이 있습니다.
* Mozilla Firefox를 사용하는 경우 작업 영역의 프라이빗 엔드포인트에 액세스를 시도할 때 문제가 발생할 수 있습니다. 이 문제는 Mozilla의 DNS over HTTPS와 관련된 것일 수 있습니다. 해결 방법으로 Microsoft Edge 또는 Google Chrome을 사용하는 것이 좋습니다.
* 프라이빗 엔드포인트를 사용해도 작업 영역을 삭제하거나 컴퓨팅 리소스를 관리하는 것과 같은 Azure 컨트롤 플레인(관리 작업)에는 영향을 주지 않습니다. 예를 들어 컴퓨팅 대상 생성, 업데이트 또는 삭제가 있습니다. 이러한 작업은 항상 그렇듯 공용 인터넷을 통해 수행됩니다. Azure Machine Learning 스튜디오, API(게시된 파이프라인 포함) 또는 SDK를 사용하는 등의 데이터 평면 작업은 프라이빗 엔드포인트를 사용합니다.

## <a name="create-a-workspace-that-uses-a-private-endpoint"></a>프라이빗 엔드포인트를 사용하는 작업 영역 만들기

다음 방법 중 하나를 사용하여 프라이빗 엔드포인트를 사용하는 작업 영역을 만듭니다. 이러한 각 방법을 사용하려면 __기존 가상 네트워크가 필요합니다__.

> [!TIP]
> 작업 영역, 프라이빗 엔드포인트 및 가상 네트워크를 동시에 만들려면 [Azure Resource Manager 템플릿을 사용하여 Azure Machine Learning의 작업 영역 만들기](how-to-create-workspace-template.md)를 참조하세요.

# <a name="python"></a>[Python](#tab/python)

Azure Machine Learning Python SDK는 프라이빗 엔드포인트를 사용하는 작업 영역을 만들 때 [Workspace.create()](/python/api/azureml-core/azureml.core.workspace.workspace#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---tags-none--friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--adb-workspace-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--private-endpoint-config-none--private-endpoint-auto-approval-true--exist-ok-false--show-output-true-)와 함께 사용할 수 있는 [PrivateEndpointConfig](/python/api/azureml-core/azureml.core.privateendpointconfig) 클래스를 제공합니다. 이 클래스를 사용하려면 기존 가상 네트워크가 필요합니다.

```python
from azureml.core import Workspace
from azureml.core import PrivateEndPointConfig

pe = PrivateEndPointConfig(name='myprivateendpoint', vnet_name='myvnet', vnet_subnet_name='default')
ws = Workspace.create(name='myworkspace',
    subscription_id='<my-subscription-id>',
    resource_group='myresourcegroup',
    location='eastus2',
    private_endpoint_config=pe,
    private_endpoint_auto_approval=True,
    show_output=True)
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[기계 학습용 Azure CLI 확장 1.0](reference-azure-machine-learning-cli.md)은 [az ml workspace create](/cli/azure/ml/workspace#az_ml_workspace_create) 명령을 제공합니다. 이 명령에 다음 매개 변수를 사용하여 프라이빗 네트워크를 사용하는 작업 영역을 만들 수 있지만 그러려면 기존 가상 네트워크가 필요합니다.

* `--pe-name`: 만들어진 프라이빗 엔드포인트의 이름입니다.
* `--pe-auto-approval`: 작업 영역에 대한 프라이빗 엔드포인트 연결을 자동으로 승인해야 하는지 여부입니다.
* `--pe-resource-group`: 프라이빗 엔드포인트를 만들 리소스 그룹입니다. 가상 네트워크를 포함하는 그룹과 같아야 합니다.
* `--pe-vnet-name`: 프라이빗 엔드포인트를 만들 기존 가상 네트워크입니다.
* `--pe-subnet-name`: 프라이빗 엔드포인트를 만들 서브넷의 이름입니다. 기본값은 `default`입니다.

이러한 매개 변수는 create 명령의 다른 필수 매개 변수와 함께 사용할 수 있습니다. 예를 들어 다음 명령은 기존 리소스 그룹 및 VNet을 사용하여 미국 서부 지역에 새 작업 영역을 만듭니다.

```azurecli
az ml workspace create -r myresourcegroup \
    -l westus \
    -n myworkspace \
    --pe-name myprivateendpoint \
    --pe-auto-approval \
    --pe-resource-group myresourcegroup \
    --pe-vnet-name myvnet \
    --pe-subnet-name mysubnet
```

# <a name="portal"></a>[포털](#tab/azure-portal)

Azure Machine Learning 스튜디오의 __네트워킹__ 탭에서 프라이빗 엔드포인트를 구성할 수 있습니다. 하지만 이렇게 하려면 기존 가상 네트워크가 필요합니다. 자세한 내용은 [포털에서 작업 영역 만들기](how-to-manage-workspace.md)를 참조하세요.

---

## <a name="add-a-private-endpoint-to-a-workspace"></a>작업 영역에 프라이빗 엔드포인트 추가

다음 방법 중 하나를 사용하여 기존 작업 영역에 프라이빗 엔드포인트를 추가합니다.

> [!WARNING]
>
> 이 작업 영역에 연결된 기존 컴퓨팅 대상이 있는 경우, 해당 컴퓨팅 대상은 프라이빗 엔드포인트가 생성된 가상 네트워크 뒤에 있어야만 작동합니다.

# <a name="python"></a>[Python](#tab/python)

```python
from azureml.core import Workspace
from azureml.core import PrivateEndPointConfig

pe = PrivateEndPointConfig(name='myprivateendpoint', vnet_name='myvnet', vnet_subnet_name='default')
ws = Workspace.from_config()
ws.add_private_endpoint(private_endpoint_config=pe, private_endpoint_auto_approval=True, show_output=True)
```

이 예제에 사용된 클래스 및 메서드에 대한 자세한 내용은 [PrivateEndpointConfig](/python/api/azureml-core/azureml.core.privateendpointconfig) 및 [Workspace.add_private_endpoint](/python/api/azureml-core/azureml.core.workspace(class)#add-private-endpoint-private-endpoint-config--private-endpoint-auto-approval-true--location-none--show-output-true--tags-none-)를 참조하세요.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[기계 학습용 Azure CLI 확장 1.0](reference-azure-machine-learning-cli.md)은 [az ml workspace private-endpoint add](/cli/azure/ml(v1)/workspace/private-endpoint#az_ml_workspace_private_endpoint_add) 명령을 제공합니다.

```azurecli
az ml workspace private-endpoint add -w myworkspace  --pe-name myprivateendpoint --pe-auto-approval --pe-vnet-name myvnet
```

# <a name="portal"></a>[포털](#tab/azure-portal)

포털의 Azure Machine Learning 작업 영역에서 __프라이빗 엔드포인트 연결__ 을 선택한 후 __+ 프라이빗 엔드포인트__ 를 선택합니다. 필드를 사용하여 새 프라이빗 엔드포인트를 만듭니다.

* __지역을__ 선택하는 경우 가상 네트워크와 동일한 지역을 선택합니다. 
* __리소스 종류__ 를 선택하는 경우 __Microsoft.MachineLearningServices/workspaces__ 를 사용합니다. 
* __리소스__ 를 작업 영역 이름으로 설정합니다.

마지막으로, __만들기__ 를 선택하여 프라이빗 엔드포인트를 만듭니다.

---

## <a name="remove-a-private-endpoint"></a>프라이빗 엔드포인트 제거

다음 방법 중 하나를 사용하여 작업 영역에서 프라이빗 엔드포인트를 제거합니다.

# <a name="python"></a>[Python](#tab/python)

[Workspace.delete_private_endpoint_connection](/python/api/azureml-core/azureml.core.workspace(class)#delete-private-endpoint-connection-private-endpoint-connection-name-)을 사용하여 프라이빗 엔드포인트를 제거합니다.

```python
from azureml.core import Workspace

ws = Workspace.from_config()
# get the connection name
_, _, connection_name = ws.get_details()['privateEndpointConnections'][0]['id'].rpartition('/')
ws.delete_private_endpoint_connection(private_endpoint_connection_name=connection_name)
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[기계 학습용 Azure CLI 확장 1.0](reference-azure-machine-learning-cli.md)은 [az ml workspace private-endpoint delete](/cli/azure/ml(v1)/workspace/private-endpoint#az_ml_workspace_private_endpoint_delete) 명령을 제공합니다.

# <a name="portal"></a>[포털](#tab/azure-portal)

포털의 Azure Machine Learning 작업 영역에서 __프라이빗 엔드포인트 연결__ 을 선택한 후 제거하려는 엔드포인트를 선택합니다. 마지막으로, __제거__ 를 선택합니다.

---

## <a name="using-a-workspace-over-a-private-endpoint"></a>프라이빗 엔드포인트를 통해 작업 영역 사용

작업 영역에 대한 통신은 가상 네트워크에서 들어오는 것만 허용되므로 작업 영역을 사용하는 모든 개발 환경은 가상 네트워크의 구성원이어야 합니다. 예를 들어 가상 네트워크의 가상 머신이 있습니다.

> [!IMPORTANT]
> 일시적인 연결 중단을 방지하려면 프라이빗 엔드포인트를 사용하도록 설정한 후 작업 영역에 연결하는 머신에서 DNS 캐시를 플러시하는 것이 좋습니다. 

Azure Virtual Machines에 대한 자세한 내용은 [Virtual Machines 설명서](../virtual-machines/index.yml)를 참조하세요.

## <a name="enable-public-access"></a>공용 액세스 사용

경우에 따라 VNet을 통해서가 아니라 퍼블릭 엔드포인트를 통해 다른 사람이 보안 작업 영역에 연결하도록 허용할 수 있습니다. 프라이빗 엔드포인트를 사용하여 작업 영역을 구성한 후 필요에 따라 작업 영역에 대한 공용 액세스를 사용하도록 설정할 수 있습니다. 이렇게 해도 프라이빗 엔드포인트는 제거되지 않습니다. VNet 뒤 구성 요소 간의 모든 통신은 여전히 보호됩니다. 따라서 VNet을 통한 비공개 액세스 외에 공용 액세스도 작업 영역에 사용하도록 설정됩니다.

> [!WARNING]
> 공용 엔드포인트를 통해 연결할 때:
> * __스튜디오의 일부 기능은 데이터에 액세스할 수 없습니다__. 이 문제는 _VNet 뒤에서 보호되는 서비스에 데이터를 저장_ 하는 경우에 발생합니다. 예를 들어 Azure Storage 계정이 있습니다. 
> * Notebook 실행을 포함하여 컴퓨팅 인스턴스에서 Jupyter, JupyterLab 및 RStudio를 사용하는 것은 __지원되지 않습니다__.

프라이빗 엔드포인트 지원 작업 영역에 대한 퍼블릭 액세스를 사용하도록 설정하려면 다음 단계를 수행합니다.

# <a name="python"></a>[Python](#tab/python)

[Workspace.delete_private_endpoint_connection](/python/api/azureml-core/azureml.core.workspace(class)#delete-private-endpoint-connection-private-endpoint-connection-name-)을 사용하여 프라이빗 엔드포인트를 제거합니다.

```python
from azureml.core import Workspace

ws = Workspace.from_config()
ws.update(allow_public_access_when_behind_vnet=True)
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[기계 학습용 Azure CLI 확장 1.0](reference-azure-machine-learning-cli.md)은 [az ml workspace update](/cli/azure/ml/workspace#az_ml_workspace_update) 명령을 제공합니다. 작업 영역에 대한 공용 액세스를 사용하도록 설정하려면 `--allow-public-access true` 매개 변수를 추가합니다.

# <a name="portal"></a>[포털](#tab/azure-portal)

현재 포털을 사용하여 이 기능을 사용하도록 설정하는 방법은 없습니다.

---


## <a name="next-steps"></a>다음 단계

* Azure Machine Learning 작업 영역을 보호하는 방법에 대한 자세한 내용은 [가상 네트워크 격리 및 개인 정보 개요](how-to-network-security-overview.md) 문서를 참조하세요.

* 가상 네트워크에서 사용자 지정 DNS 솔루션을 사용하려는 경우 [사용자 지정 DNS 서버에서 작업 영역을 사용하는 방법](how-to-custom-dns.md)을 참조하세요.
