---
title: 관리형 NAT Gateway(미리 보기)
description: 관리형 NAT 통합을 사용하여 AKS 클러스터를 만드는 방법 알아보기
services: container-service
ms.topic: article
ms.date: 10/26/2021
ms.author: juda
ms.openlocfilehash: 87edce54391661fe986365ad45d72bfeee41c761
ms.sourcegitcommit: c434baa76153142256d17c3c51f04d902e29a92e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132179610"
---
# <a name="managed-nat-gateway-preview"></a>관리형 NAT Gateway(미리 보기)

AKS 고객은 Azure Load Balancer 통해 송신 트래픽을 라우팅할 수 있지만 가능한 트래픽의 아웃바운드 흐름 양에는 제한이 있습니다. 

Azure NAT Gateway 최대 16개의 IP 주소를 사용하여 IP 주소당 최대 64,000개의 아웃바운드 UDP 및 TCP 트래픽 흐름을 허용합니다.

이 문서에서는 송신 트래픽에 대한 관리되는 NAT Gateway 사용하여 AKS 클러스터를 만드는 방법을 보여줍니다.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="before-you-begin"></a>시작하기 전에

관리 NAT 게이트웨이를 사용하려면 다음이 있어야 합니다.

* 최신 버전의 Azure CLI
* `aks-preview`확장 버전 0.5.31 이상
* Kubernetes 버전 1.20.x 이상


### <a name="register-the-aks-natgatewaypreview-feature-flag"></a>기능 `AKS-NATGatewayPreview` 플래그 등록

NAT Gateway 기능을 사용하려면 구독에서 기능 플래그를 사용하도록 설정해야 `AKS-NATGatewayPreview` 합니다. 

```azurecli
az feature register --namespace "Microsoft.ContainerService" --name "AKS-NATGatewayPreview"
```
[az feature list][az-feature-list] 명령을 사용하여 등록 상태를 확인할 수 있습니다.

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKS-NATGatewayPreview')].{Name:name,State:properties.state}"
```

준비가 되면 [az provider register][az-provider-register] 명령을 사용하여 *ContainerService* 리소스 공급자의 등록을 새로 고칩니다.

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```


## <a name="create-an-aks-cluster-with-a-managed-nat-gateway"></a>관리되는 NAT Gateway 사용하여 AKS 클러스터 만들기
새 관리되는 NAT Gateway 사용하여 AKS 클러스터를 만들려면 를 `--outbound-type managedNATGateway` 실행할 때 및 를 `--nat-gateway-managed-outbound-ip-count` `--nat-gateway-idle-timeout` `az aks create` 사용합니다. 다음 예제에서는 *myresourcegroup* 리소스 그룹을 만든 다음, 관리되는 NAT Gateway, 두 개의 아웃바운드 IP 및 30초의 유휴 시간 제한으로 *myresourcegroup에* *natcluster* AKS 클러스터를 만듭니다.


```azurecli-interactive
az group create --name myresourcegroup --location southcentralus
```

```azurecli-interactive
az aks create \
    --resource-group myresourcegroup \
    --name natcluster \
    --node-count 3 \
    --outbound-type managedNATGateway \ 
    --nat-gateway-managed-outbound-ip-count 2 \
    --nat-gateway-idle-timeout 30
```

> [!IMPORTANT]
> 아웃바운드 IP 주소가 지정된 값이 없으면 기본값은 1입니다.

### <a name="update-the-number-of-outbound-ip-addresses"></a>아웃바운드 IP 주소 수 업데이트
아웃바운드 IP 주소 또는 유휴 시간 초과를 업데이트하려면 를 실행할 때 또는 를 `--nat-gateway-managed-outbound-ip-count` `--nat-gateway-idle-timeout` `az aks update` 사용합니다. 예를 들어:

```azurecli-interactive
az aks update \ 
    --resource-group myresourcegroup \
    --name natcluster\
    --nat-gateway-managed-outbound-ip-count 5
```

## <a name="create-an-aks-cluster-with-a-user-assigned-nat-gateway"></a>사용자 할당 NAT Gateway 사용하여 AKS 클러스터 만들기
사용자 할당 NAT Gateway 사용하여 AKS 클러스터를 만들려면 를 실행할 때 를 `--outbound-type userAssignedNATGateway` `az aks create` 사용합니다. 이 구성에는 [Kubenet][byo-vnet-kubenet] 또는 [Azure CNI][byo-vnet-azure-cni]통해 사용자 고유의 네트워킹이 필요하며 NAT Gateway 서브넷에 미리 구성되어 있어야 합니다. 다음 명령은 이 시나리오에 필요한 리소스를 만듭니다. 변수에 저장된 값을 명령에 계속 사용할 수 있도록 동일한 세션에서 모두 실행해야 `az aks create` 합니다.

1. 리소스 그룹을 만듭니다.
    ```azurecli-interactive
    az group create --name myresourcegroup \
        --location southcentralus
    ```

2. 네트워크 권한에 대한 관리 ID를 만들고 나중에 사용할 수 있도록 에 ID를 저장합니다. `$IDENTITY_ID`
    ```azurecli-interactive
    IDENTITY_ID=$(az identity create \
        --resource-group myresourcegroup \
        --name natclusterid \
        --location southcentralus \
        --query id \
        --output tsv)
    ```

3. NAT 게이트웨이에 대한 공용 IP를 만듭니다.
    ```azurecli-interactive
    az network public-ip create \
        --resource-group myresourcegroup \
        --name mynatgatewaypip \
        --location southcentralus \
        --sku standard
    ```

4. NAT 게이트웨이를 만듭니다.
    ```azurecli-interactive
    az network nat gateway create \
        --resource-group myresourcegroup \
        --name mynatgateway \
        --location southcentralus \
        --public-ip-addresses mynatgatewaypip
    ```

5. 가상 네트워크 만들기:
    ```azurecli-interactive
    az network vnet create \
        --resource-group myresourcegroup \
        --name myvnet \
        --location southcentralus \
        --address-prefixes 172.16.0.0/20 
    ```

6. NAT 게이트웨이를 사용하여 가상 네트워크에 서브넷을 만들고 나중에 사용할 수 있도록 에 ID를 저장합니다. `$SUBNET_ID`
    ```azurecli-interactive
    SUBNET_ID=$(az network vnet subnet create \
        --resource-group myresourcegroup \
        --vnet-name myvnet \
        --name natcluster \
        --address-prefixes 172.16.0.0/22 \
        --nat-gateway mynatgateway \
        --query id \
        --output tsv)
    ```

7. NAT 게이트웨이 및 관리 ID와 함께 서브넷을 사용하여 AKS 클러스터를 만듭니다.
    ```azurecli-interactive
    az aks create \
        --resource-group myresourcegroup \
        --name natcluster \
        --location southcentralus \
        --network-plugin azure \
        --vnet-subnet-id $SUBNET_ID \
        --outbound-type userAssignedNATGateway \
        --enable-managed-identity \
        --assign-identity $IDENTITY_ID
    ```

## <a name="next-steps"></a>다음 단계
- Azure NAT Gateway 대한 자세한 내용은 [Azure NAT Gateway][nat-docs]를 참조하세요.

<!-- LINKS - internal -->


<!-- LINKS - external-->
[nat-docs]: ../virtual-network/nat-gateway/nat-overview.md
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[byo-vnet-azure-cni]: configure-azure-cni.md
[byo-vnet-kubenet]: configure-kubenet.md