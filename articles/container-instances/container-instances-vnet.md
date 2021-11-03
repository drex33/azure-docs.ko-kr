---
title: Azure 가상 네트워크에 컨테이너 그룹 배포
description: Azure 명령줄 인터페이스를 사용하여 새 또는 기존 Azure 가상 네트워크에 컨테이너 그룹을 배포하는 방법을 알아봅니다.
ms.topic: article
ms.date: 07/02/2020
ms.custom: devx-track-js, devx-track-azurecli
ms.openlocfilehash: da24d64ba4615ff6d06ca2617e8e9c45a4dbff9f
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131080216"
---
# <a name="deploy-container-instances-into-an-azure-virtual-network"></a>Azure Virtual Network에 컨테이너 인스턴스 배포

[Azure Virtual Network](../virtual-network/virtual-networks-overview.md)는 Azure 및 온-프레미스 리소스를 위한 안전한 프라이빗 네트워킹 기능을 제공합니다. 컨테이너 그룹을 Azure Virtual Network에 배포하면 컨테이너가 가상 네트워크의 다른 리소스와 안전하게 통신할 수 있습니다.

이 문서는 Azure CLI에서 [az container create][az-container-create] 명령을 사용하여 새 가상 네트워크 또는 기존 가상 네트워크에 컨테이너 그룹을 배포하는 방법을 보여줍니다. 

네트워킹 시나리오 및 제한 사항은 [Azure Container Instances의 가상 네트워크 시나리오 및 리소스](container-instances-virtual-network-concepts.md)를 참조하세요.

> [!IMPORTANT]
> 가상 네트워크에 대한 컨테이너 그룹 배포는 일반적으로 Azure Container Instances 이용이 가능한 대부분의 지역에서 Linux 컨테이너에 사용할 수 있습니다. 자세한 내용은 [지역 및 리소스 가용성][container-regions]을 참조하세요. 

이 문서의 예제는 Bash 셸용으로 서식이 지정되어 있습니다. PowerShell이나 명령 프롬프트 등의 다른 셸을 사용하려는 경우 해당 셸에 따라 줄 연속 문자를 조정하세요.


## <a name="deploy-to-new-virtual-network"></a>새 가상 네트워크에 배포

새 가상 네트워크에 배포하고 Azure에서 네트워크 리소스를 자동으로 생성하도록 하려는 경우 [az container create][az-container-create] 실행 시에 다음 항목을 지정합니다.

* 가상 네트워크 이름
* CIDR 형식의 가상 네트워크 주소 접두사
* 서브넷 이름
* CIDR 형식의 서브넷 주소 접두사

가상 네트워크 및 서브넷 주소 접두사는 각각 가상 네트워크와 서브넷의 주소 공간을 지정합니다. 이러한 값은 `10.0.0.0/16`과 같이 CIDR(Classless Inter-Domain Routing) 표기법으로 표시됩니다. 서브넷 사용 방법에 대한 자세한 내용은 [가상 네트워크 서브넷 추가, 변경 또는 삭제](../virtual-network/virtual-network-manage-subnet.md)를 참조하세요.

이 방법으로 첫 번째 컨테이너 그룹을 배포한 후에는 가상 네트워크 및 서브넷 이름이나 Azure에서 자동으로 생성한 네트워크 프로필을 지정하여 같은 서브넷에 컨테이너 그룹을 배포할 수 있습니다. 서브넷은 Azure Container Instances에 위임되므로 서브넷에는 컨테이너 그룹 *만* 배포할 수 있습니다.

### <a name="example"></a>예제

다음 [az container create][az-container-create] 명령은 새 가상 네트워크와 서브넷의 설정을 지정합니다. 가상 네트워크의 컨테이너 그룹 배포를 [사용할 수](container-instances-region-availability.md) 있는 지역에서 만든 리소스 그룹의 이름을 제공합니다. 이 명령은 정적 웹 페이지를 제공하는 소규모 Node.js 웹 서버가 실행되는 공용 Microsoft [aci-helloworld][aci-helloworld] 컨테이너를 배포합니다. 다음 섹션에서는 같은 서브넷에 두 번째 컨테이너 그룹을 배포하고 두 컨테이너 인스턴스 간의 통신을 테스트합니다.

```azurecli
az container create \
  --name appcontainer \
  --resource-group myResourceGroup \
  --image mcr.microsoft.com/azuredocs/aci-helloworld \
  --vnet aci-vnet \
  --vnet-address-prefix 10.0.0.0/16 \
  --subnet aci-subnet \
  --subnet-address-prefix 10.0.0.0/24
```

이 방법을 사용하여 새 가상 네트워크에 배포할 때는 네트워크 리소스가 생성되는 과정에서 배포가 몇 분 정도 소요될 수 있습니다. 초기 배포 후에는 동일한 서브넷에 대한 추가 컨테이너 그룹 배포가 더 빨리 완료됩니다.

## <a name="deploy-to-existing-virtual-network"></a>기존 가상 네트워크에 배포

기존 가상 네트워크에 컨테이너 그룹을 배포하려면

1. 기존 가상 네트워크 내에서 서브넷을 만들거나, 컨테이너 그룹이 이미 배포된 기존 서브넷을 사용하거나, 다른 모든 리소스를 비운 기존 서브넷을 사용합니다.
1. [az container create][az-container-create] 명령을 사용하여 컨테이너 그룹을 배포하고 다음 중 하나를 지정합니다.
   * 가상 네트워크 이름 및 서브넷 이름
   * 다른 리소스 그룹의 가상 네트워크를 사용할 수 있는 가상 네트워크 리소스 ID 및 서브넷 리소스 ID
   * [az network profile list][az-network-profile-list]를 사용하여 가져올 수 있는 네트워크 프로필 이름 또는 ID

### <a name="example"></a>예제

다음 예에서는 이전에 만든 동일한 서브넷에 두 번째 컨테이너 그룹을 배포하고 두 컨테이너 인스턴스 간의 통신을 확인합니다.

먼저 첫 번째로 배포한 컨테이너 그룹인 *appcontainer* 의 IP 주소를 가져옵니다.

```azurecli
az container show --resource-group myResourceGroup \
  --name appcontainer \
  --query ipAddress.ip --output tsv
```

출력에는 프라이빗 서브넷의 컨테이너 그룹 IP 주소가 표시됩니다. 예를 들면 다음과 같습니다.

```console
10.0.0.4
```

이제 `CONTAINER_GROUP_IP`를 `az container show` 명령으로 검색한 IP로 설정하고 다음 `az container create` 명령을 실행합니다. 두 번째 컨테이너인 *commchecker* 는 Alpine Linux 기반 이미지를 실행하며 첫 번째 컨테이너 그룹의 프라이빗 서브넷 IP 주소에 대해 `wget`을 실행합니다.

```azurecli
CONTAINER_GROUP_IP=<container-group-IP-address>

az container create \
  --resource-group myResourceGroup \
  --name commchecker \
  --image alpine:3.5 \
  --command-line "wget $CONTAINER_GROUP_IP" \
  --restart-policy never \
  --vnet aci-vnet \
  --subnet aci-subnet
```

이 두 번째 컨테이너 배포가 완료되면 실행된 `wget` 명령의 출력을 확인할 수 있도록 해당 로그를 끌어옵니다.

```azurecli
az container logs --resource-group myResourceGroup --name commchecker
```

두 번째 컨테이너가 첫 번째 컨테이너와 정상적으로 통신했다면 출력은 다음과 같이 표시됩니다.

```console
Connecting to 10.0.0.4 (10.0.0.4:80)
index.html           100% |*******************************|  1663   0:00:00 ETA
```

로그 출력에는 `wget`이 로컬 서브넷의 개인 IP 주소를 사용하여 첫 번째 컨테이너에 연결한 다음 해당 컨테이너에서 인덱스 파일을 다운로드할 수 있었다는 내용이 표시되어야 합니다. 두 컨테이너 그룹 간의 네트워크 트래픽은 가상 네트워크 내에서 그대로 유지됩니다.

### <a name="example---yaml"></a>예 - YAML

YAML 파일, [Resource Manager 템플릿](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.containerinstance/aci-vnet
) 또는 Python SDK를 사용하는 등의 다른 프로그래밍 메서드를 사용하여 기존 가상 네트워크에 컨테이너 그룹을 배포할 수도 있습니다. 

예를 들어 YAML 파일을 사용하는 경우 Azure Container Instances에 위임된 서브넷을 사용하여 가상 네트워크에 배포할 수 있습니다. 다음 속성을 지정합니다.

* `ipAddress`: 컨테이너 그룹의 프라이빗 IP 주소 설정입니다.
  * `ports`: 열려는 포트(있는 경우)입니다.
  * `protocol`: 여는 포트의 프로토콜(TCP 또는 UDP)입니다.
* `networkProfile`: 가상 네트워크 및 서브넷에 대한 네트워크 설정입니다.
  * `id`: `networkProfile`의 전체 Resource Manager 리소스 ID입니다.

네트워크 프로필의 ID를 가져오려면 가상 네트워크와 위임된 서브넷을 포함하는 리소스 그룹의 이름을 지정하여 [az network profile list][az-network-profile-list] 명령을 실행합니다.

``` azurecli
az network profile list --resource-group myResourceGroup \
  --query [0].id --output tsv
```

샘플 출력:

```console
/subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkProfiles/aci-network-profile-aci-vnet-aci-subnet
```

네트워크 프로필 ID를 가져온 후 다음 YAML을 새 파일 *vnet-deploy-aci.yaml* 에 복사합니다. `networkProfile` 아래의 `id` 값을 방금 검색한 ID로 바꾸고 파일을 저장합니다. 이 YAML은 가상 네트워크에 컨테이너 그룹 *appcontaineryaml* 을 만듭니다.

```yaml
apiVersion: '2019-12-01'
location: westus
name: appcontaineryaml
properties:
  containers:
  - name: appcontaineryaml
    properties:
      image: mcr.microsoft.com/azuredocs/aci-helloworld
      ports:
      - port: 80
        protocol: TCP
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
  ipAddress:
    type: Private
    ports:
    - protocol: tcp
      port: '80'
  networkProfile:
    id: /subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkProfiles/aci-network-profile-aci-vnet-subnet
  osType: Linux
  restartPolicy: Always
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

 매개 변수로 YAML 파일 이름을 지정한 [az container create`--file`][az-container-create] 명령을 사용하여 컨테이너 그룹을 배포합니다.

```azurecli
az container create --resource-group myResourceGroup \
  --file vnet-deploy-aci.yaml
```

배포가 완료되면 [az container show][az-container-show] 명령을 실행하여 배포 상태를 표시합니다. 샘플 출력:

```console
Name              ResourceGroup    Status    Image                                       IP:ports     Network    CPU/Memory       OsType    Location
----------------  ---------------  --------  ------------------------------------------  -----------  ---------  ---------------  --------  ----------
appcontaineryaml  myResourceGroup  Running   mcr.microsoft.com/azuredocs/aci-helloworld  10.0.0.5:80  Private    1.0 core/1.5 gb  Linux     westus
```

## <a name="clean-up-resources"></a>리소스 정리

### <a name="delete-container-instances"></a>컨테이너 인스턴스 삭제

작성한 컨테이너를 사용하는 작업이 완료되었으면 다음 명령을 사용하여 컨테이너를 삭제합니다.

```azurecli
az container delete --resource-group myResourceGroup --name appcontainer -y
az container delete --resource-group myResourceGroup --name commchecker -y
az container delete --resource-group myResourceGroup --name appcontaineryaml -y
```

### <a name="delete-network-resources"></a>네트워크 리소스 삭제

현재 이 기능에서 앞에서 만든 네트워크 리소스를 삭제하려면 여러 명령을 추가로 실행해야 합니다. 이 문서의 이전 섹션에 나왔던 예제 명령을 사용하여 가상 네트워크와 서브넷을 만들었다면 아래 스크립트를 사용하여 해당 네트워크 리소스를 삭제할 수 있습니다. 이 스크립트는 단일 네트워크 프로필을 사용하는 단일 가상 네트워크가 리소스 그룹에 포함되어 있다고 가정합니다.

스크립트를 실행하기 전에 `RES_GROUP` 변수를 삭제해야 하는 가상 네트워크 및 서브넷이 포함된 리소스 그룹의 이름으로 설정합니다. 이전에 제안된 `aci-vnet` 이름을 사용하지 않은 경우 가상 네트워크의 이름을 업데이트합니다. 스크립트는 Bash 셸에 대해 서식이 지정됩니다. PowerShell이나 명령 프롬프트 등의 다른 셸을 사용하려는 경우 해당 셸에 따라 변수 할당과 접근자를 조정해야 합니다.

> [!WARNING]
> 이 스크립트는 리소스, 즉 가상 네트워크 및 해당 네트워크에 포함된 모든 서브넷을 삭제합니다. 그러므로 이 스크립트를 실행하기 전에 가상 네트워크에 포함된 서브넷을 비롯하여 가상 네트워크의 *모든* 리소스가 더 이상 필요하지 않은지 확인하세요. 삭제된 **리소스는 복구할 수 없습니다**.

```azurecli
# Replace <my-resource-group> with the name of your resource group
# Assumes one virtual network in resource group
RES_GROUP=<my-resource-group>

# Get network profile ID
# Assumes one profile in virtual network
NETWORK_PROFILE_ID=$(az network profile list --resource-group $RES_GROUP --query [0].id --output tsv)

# Delete the network profile
az network profile delete --id $NETWORK_PROFILE_ID -y

# Delete virtual network
az network vnet delete --resource-group $RES_GROUP --name aci-vnet
```

## <a name="next-steps"></a>다음 단계

Resource Manager 템플릿을 사용하여 새 가상 네트워크, 서브넷, 네트워크 프로필 및 컨테이너 그룹을 배포하려면 [VNet을 사용하여 Azure 컨테이너 그룹 만들기](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.containerinstance/aci-vnet
)를 참조하세요.

<!-- IMAGES -->
[aci-vnet-01]: ./media/container-instances-vnet/aci-vnet-01.png

<!-- LINKS - External -->
[aci-helloworld]: https://hub.docker.com/_/microsoft-azuredocs-aci-helloworld

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az_container_create
[az-container-show]: /cli/azure/container#az_container_show
[az-network-vnet-create]: /cli/azure/network/vnet#az_network_vnet_create
[az-network-profile-list]: /cli/azure/network/profile#az_network_profile_list
[container-regions]: container-instances-region-availability.md
