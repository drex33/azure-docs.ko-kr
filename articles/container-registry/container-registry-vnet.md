---
title: 서비스 엔드포인트를 사용한 액세스 제한
description: Azure Virtual Network의 서비스 엔드포인트를 사용하여 Azure Container Registry에 대한 액세스를 제한합니다. 서비스 엔드포인트 액세스는 프리미엄 서비스 계층의 기능입니다.
ms.topic: article
ms.date: 05/04/2020
ms.openlocfilehash: 6c76e7e98f049a46c161c5f4ff00b5282ba52c1a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131043474"
---
# <a name="restrict-access-to-a-container-registry-using-a-service-endpoint-in-an-azure-virtual-network"></a>Azure Virtual Network의 서비스 엔드포인트를 사용하여 컨테이너 레지스트리에 대한 액세스 제한

[Azure Virtual Network](../virtual-network/virtual-networks-overview.md)는 Azure 및 온-프레미스 리소스를 위한 안전한 프라이빗 네트워킹 기능을 제공합니다. [서비스 엔드포인트](../virtual-network/virtual-network-service-endpoints-overview.md)를 사용하면 컨테이너 레지스트리의 공용 IP 주소를 사용자의 가상 네트워크로만 제한하여 보호할 수 있습니다. 이 엔드포인트는 Azure 백본 네트워크를 통해 트래픽에 최적의 리소스 경로를 제공합니다. 가상 네트워크 및 서브넷의 ID 또한 각 요청과 함께 전송됩니다.

이 문서에서는 가상 네트워크에서 컨테이너 레지스트리 서비스 엔드포인트(미리 보기)를 구성하는 방법을 보여 줍니다. 

각 레지스트리는 최대 100 개의 가상 네트워크 규칙을 지원 합니다.

> [!IMPORTANT]
> Azure Container Registry에서 이제 [Azure Private Link](container-registry-private-link.md)를 지원하므로 레지스트리에 가상 네트워크의 프라이빗 엔드포인트를 배치할 수 있습니다. 프라이빗 엔드포인트는 개인 IP 주소를 사용하여 가상 네트워크 안에서 액세스할 수 있습니다. 대부분의 네트워크 시나리오에서 서비스 엔드포인트 대신 프라이빗 엔드포인트를 사용하는 것이 좋습니다.

레지스트리 서비스 엔드포인트 구성은 **프리미엄** 컨테이너 레지스트리 서비스 계층에서 가능합니다. 레지스트리 서비스 계층 및 제한에 대한 자세한 내용은 [Azure Container Registry 서비스 계층](container-registry-skus.md)을 참조하세요.

## <a name="preview-limitations"></a>미리 보기 제한 사항

* Azure Container Registry용 서비스 엔드포인트의 향후 개발은 현재는 계획에 없습니다. 그 대신 [프라이빗 엔드포인트](container-registry-private-link.md)를 사용하는 것이 좋습니다.
* Azure Portal를 사용하여 레지스트리에 서비스 엔드포인트를 구성할 수 없습니다.
* [Azure Kubernetes Service](../aks/intro-kubernetes.md) 클러스터 또는 Azure [가상 머신](../virtual-machines/linux/overview.md)만 호스트로 사용하여 서비스 엔드포인트를 사용하는 컨테이너 레지스트리에 액세스할 수 있습니다. *Azure Container Instances를 비롯한 다른 Azure 서비스는 지원되지 않습니다.*
* Azure 미국 정부 클라우드 또는 Azure 중국 클라우드에서는 Azure Container Registry에 대한 서비스 엔드포인트가 지원되지 않습니다.

[!INCLUDE [container-registry-scanning-limitation](../../includes/container-registry-scanning-limitation.md)]

## <a name="prerequisites"></a>사전 요구 사항

* 이 문서의 Azure CLI 단계를 사용하려면 Azure CLI 버전 2.0.58 이상이 필요합니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][azure-cli]를 참조하세요.

* 컨테이너 레지스트리가 아직 없는 경우, 하나(프리미엄 계층 필요)를 만들고 Docker Hub에서 `hello-world`와 같은 샘플 이미지를 밀어넣습니다. 예를 들어 [Azure Portal][quickstart-portal] 또는 [Azure CLI][quickstart-cli]를 사용하여 레지스트리를 만듭니다. 

* 다른 Azure 구독에서 서비스 엔드포인트를 사용하여 레지스트리 액세스를 제한하려면 해당 구독에서 Azure Container Registry에 대한 리소스 공급자를 등록합니다. 다음은 그 예입니다.

  ```azurecli
  az account set --subscription <Name or ID of subscription of virtual network>

  az provider register --namespace Microsoft.ContainerRegistry
  ``` 

[!INCLUDE [Set up Docker-enabled VM](../../includes/container-registry-docker-vm-setup.md)]

## <a name="configure-network-access-for-registry"></a>레지스트리에 대한 네트워크 액세스 구성

이 섹션에서는 Azure Virtual Network에서 서브넷으로부터의 액세스를 허용하도록 컨테이너 레지스트리를 구성합니다. Azure CLI를 사용하여 단계를 제공합니다.

### <a name="add-a-service-endpoint-to-a-subnet"></a>서브넷에 서비스 엔드포인트 추가

VM을 만들 때 Azure는 기본적으로 동일한 리소스 그룹에 가상 네트워크를 만듭니다. 가상 네트워크의 이름은 가상 머신의 이름을 기반으로 합니다. 예를 들어 가상 머신의 이름을 *myDockerVM* 으로 지정하는 경우, 기본 가상 네트워크 이름은 *myDockerVMVNET* 으로 설정되며 서브넷 이름은 *myDockerVMSubnet* 으로 지정됩니다. [az network vnet list][az-network-vnet-list] 명령을 사용하여 이 항목을 확인합니다.

```azurecli
az network vnet list \
  --resource-group myResourceGroup \
  --query "[].{Name: name, Subnet: subnets[0].name}"
```

출력:

```console
[
  {
    "Name": "myDockerVMVNET",
    "Subnet": "myDockerVMSubnet"
  }
]
```

[az network vnet subnet update][az-network-vnet-subnet-update] 명령을 사용하여 **Microsoft.ContainerRegistry** 서비스 엔드포인트를 서브넷에 추가합니다. 다음 명령에서 가상 네트워크와 서브넷의 이름을 바꿉니다.

```azurecli
az network vnet subnet update \
  --name myDockerVMSubnet \
  --vnet-name myDockerVMVNET \
  --resource-group myResourceGroup \
  --service-endpoints Microsoft.ContainerRegistry
```

[az network vnet subnet show][az-network-vnet-subnet-show] 명령을 사용하여 서브넷의 리소스 ID를 검색합니다. 나중에 네트워크 액세스 규칙을 필요할 때 이 항목이 필요합니다.

```azurecli
az network vnet subnet show \
  --name myDockerVMSubnet \
  --vnet-name myDockerVMVNET \
  --resource-group myResourceGroup \
  --query "id"
  --output tsv
``` 

출력:

```
/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myDockerVMVNET/subnets/myDockerVMSubnet
```

### <a name="change-default-network-access-to-registry"></a>레지스트리에 대한 기본 네트워크 액세스 변경

기본적으로 Azure Container Registry는 모든 네트워크에 있는 호스트로부터의 연결을 허용합니다. 선택한 네트워크로 액세스를 제한하려면 기본 작업을 액세스 거부로 변경합니다. 다음 [az acr update][az-acr-update] 명령에서 레지스트리 이름을 바꿉니다.

```azurecli
az acr update --name myContainerRegistry --default-action Deny
```

### <a name="add-network-rule-to-registry"></a>레지스트리에 네트워크 규칙 추가

[az acr network-rule add][az-acr-network-rule-add] 명령을 사용하여 VM 서브넷으로부터의 액세스를 허용하는 네트워크 규칙을 레지스트리에 추가합니다. 다음 명령에서 컨테이너 레지스트리 이름과 서브넷 리소스 ID를 바꿉니다. 

```azurecli
az acr network-rule add \
  --name mycontainerregistry \
  --subnet <subnet-resource-id>
```

## <a name="verify-access-to-the-registry"></a>레지스트리에 대한 액세스 확인

구성이 업데이트되도록 몇 분 정도 기다린 후 VM이 컨테이너 레지스트리에 액세스할 수 있는지 확인합니다. VM에 대한 SSH 연결을 설정하고 [az acr login][az-acr-login] 명령을 실행하여 레지스트리에 로그인합니다. 

```bash
az acr login --name mycontainerregistry
```

`docker pull`과 같은 레지스트리 작업을 수행하여 레지스트리에서 샘플 이미지를 끌어올 수 있습니다. 레지스트리에 적합한 이미지 및 태그 값으로 바꾸고 레지스트리 로그인 서버 이름(모두 소문자)을 접두사로 사용합니다.

```bash
docker pull mycontainerregistry.azurecr.io/hello-world:v1
``` 

Docker가 이미지를 VM으로 끌어옵니다.

이 예에서는 네트워크 액세스 규칙을 사용하여 프라이빗 컨테이너 레지스트리에 액세스할 수 있음을 보여 줍니다. 그러나 구성된 네트워크 액세스 규칙이 없는 로그인 호스트로부터는 레지스트리에 액세스할 수 없습니다. 다른 호스트에서 `az acr login` 명령 또는 `docker login` 명령을 사용하여 로그인할 경우 다음과 유사한 내용이 출력됩니다.

```Console
Error response from daemon: login attempt to https://xxxxxxx.azurecr.io/v2/ failed with status: 403 Forbidden
```

## <a name="restore-default-registry-access"></a>기본 레지스트리 액세스 복원

기본적으로 액세스를 허용하도록 레지스트리를 복원하려면 구성된 모든 네트워크 규칙을 제거합니다. 그런 다음, 기본 작업을 액세스 허용으로 설정합니다. 

### <a name="remove-network-rules"></a>네트워크 규칙 제거

레지스트리에 대해 구성된 네트워크 규칙 목록을 보려면 다음과 같이 [az acr network-rule list][az-acr-network-rule-list] 명령을 실행합니다.

```azurecli
az acr network-rule list --name mycontainerregistry 
```

구성된 각각의 규칙에 대해 [az acr network-rule remove][az-acr-network-rule-remove] 명령을 실행하여 제거합니다. 다음은 그 예입니다.

```azurecli
# Remove a rule that allows access for a subnet. Substitute the subnet resource ID.

az acr network-rule remove \
  --name mycontainerregistry \
  --subnet /subscriptions/ \
  xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myDockerVMVNET/subnets/myDockerVMSubnet
```

### <a name="allow-access"></a>액세스 허용

다음 [az acr update][az-acr-update] 명령에서 레지스트리 이름을 바꿉니다.
```azurecli
az acr update --name myContainerRegistry --default-action Allow
```

## <a name="clean-up-resources"></a>리소스 정리

동일한 리소스 그룹에서 모든 Azure 리소스를 만들었으며 이 리소스가 더 이상 필요하지 않은 경우, 단일한 [az group delete](/cli/azure/group) 명령을 사용하여 리소스를 선택적으로 삭제할 수 있습니다.

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>다음 단계

* 가상 네트워크에서 프라이빗 엔드포인트를 사용하여 레지스트리에 대한 액세스를 제한하려면 [Azure 컨테이너에 대한 Azure Private Link 구성](container-registry-private-link.md)을 참조하세요.
* 클라이언트 방화벽 뒤에서 레지스트리 액세스 규칙을 설정해야 할 경우 [방화벽 뒤의 Azure Container Registry에 액세스하기 위한 규칙 구성](container-registry-firewall-access-rules.md)을 참조하세요.


<!-- IMAGES -->

[acr-subnet-service-endpoint]: ./media/container-registry-vnet/acr-subnet-service-endpoint.png


<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-create]: /cli/azure/acr#az_acr_create
[az-acr-show]: /cli/azure/acr#az_acr_show
[az-acr-repository-show]: /cli/azure/acr/repository#az_acr_repository_show
[az-acr-repository-list]: /cli/azure/acr/repository#az_acr_repository_list
[az-acr-login]: /cli/azure/acr#az_acr_login
[az-acr-network-rule-add]: /cli/azure/acr/network-rule/#az_acr_network_rule_add
[az-acr-network-rule-remove]: /cli/azure/acr/network-rule/#az_acr_network_rule_remove
[az-acr-network-rule-list]: /cli/azure/acr/network-rule/#az_acr_network_rule_list
[az-acr-run]: /cli/azure/acr#az_acr_run
[az-acr-update]: /cli/azure/acr#az_acr_update
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az_ad_sp_create_for_rbac
[az-group-create]: /cli/azure/group
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
[az-vm-create]: /cli/azure/vm#az_vm_create
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet/#az_network_vnet_subnet_show
[az-network-vnet-subnet-update]: /cli/azure/network/vnet/subnet/#az_network_vnet_subnet_update
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet/#az_network_vnet_subnet_show
[az-network-vnet-list]: /cli/azure/network/vnet/#az_network_vnet_list
[quickstart-portal]: container-registry-get-started-portal.md
[quickstart-cli]: container-registry-get-started-azure-cli.md
[azure-portal]: https://portal.azure.com
