---
title: 관리 되는 NAT 게이트웨이 (미리 보기)
description: 관리 되는 NAT 통합을 사용 하 여 AKS 클러스터를 만드는 방법을 알아봅니다.
services: container-service
ms.topic: article
ms.date: 10/26/2021
ms.author: juda
ms.openlocfilehash: fa93f4d0f72e2c8060a934f177db2dd53b96c7c5
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131482971"
---
# <a name="managed-nat-gateway-preview"></a>관리 되는 NAT 게이트웨이 (미리 보기)

AKS 고객은 Azure Load Balancer를 통해 송신 트래픽을 라우팅할 수 있는 반면 가능한 트래픽 아웃 바운드 흐름의 양에는 제한이 있습니다. 

Azure NAT 게이트웨이는 최대 16 개의 IP 주소를 사용 하 여 IP 주소 당 최대 64000 아웃 바운드 UDP 및 TCP 트래픽 흐름을 허용 합니다.

이 문서에서는 송신 트래픽에 대 한 관리 되는 NAT 게이트웨이를 사용 하 여 AKS 클러스터를 만드는 방법을 보여 줍니다.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="before-you-begin"></a>시작하기 전에

관리 되는 NAT 게이트웨이를 사용 하려면 다음이 있어야 합니다.

* 최신 버전의 Azure CLI
* `aks-preview`확장 버전 0.5.31 이상
* Kubernetes 버전 1.20 이상


### <a name="register-the-aks-natgatewaypreview-feature-flag"></a>`AKS-NATGatewayPreview`기능 플래그 등록

NAT 게이트웨이 기능을 사용 하려면 `AKS-NATGatewayPreview` 구독에서 기능 플래그를 사용 하도록 설정 해야 합니다. 

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


## <a name="create-an-aks-cluster-with-a-managed-nat-gateway"></a>관리 되는 NAT 게이트웨이를 사용 하 여 AKS 클러스터 만들기
새 관리 되는 NAT 게이트웨이를 사용 하 여 AKS 클러스터를 만들려면를 실행 하 고 및를 실행 하는 경우를 사용 `--outbound-type managedNATGateway` `--nat-gateway-managed-outbound-ip-count` `--nat-gateway-idle-timeout` `az aks create` 합니다. 다음 예에서는 *myresourcegroup* 리소스 그룹을 만든 다음 관리 되는 NAT 게이트웨이를 사용 하 여 *myresourcegroup* 에 *natcluster* AKS 클러스터를 만들고, 두 개의 아웃 바운드 ip를 사용 하 고, 유휴 시간 제한이 30 초입니다.


```azurecli-interactive
az group create --name myresourcegroup --location southcentralus
```

```azurecli-interactive
az aks create --resource-group myresourcegroup 
    --name natcluster  \
    --node-count 3 \
    --outbound-type managedNATGateway \ 
    --nat-gateway-managed-outbound-ip-count 2 \
    --nat-gateway-idle-timeout 30
```

> [!IMPORTANT]
> 아웃 바운드 IP 주소를 지정 하는 값이 없는 경우 기본값은 1입니다.

### <a name="update-the-number-of-outbound-ip-addresses"></a>아웃 바운드 IP 주소 수 업데이트
아웃 바운드 IP 주소 또는 유휴 시간 제한을 업데이트 하려면를 `--nat-gateway-managed-outbound-ip-count` 실행 하는 경우 또는를 사용 `--nat-gateway-idle-timeout` `az aks update` 합니다. 예를 들면 다음과 같습니다.

```azurecli-interactive
az aks update \ 
    --resource-group myresourcegroup \
    --name natcluster\
    --nat-gateway-managed-outbound-ip-count 5
```


## <a name="next-steps"></a>다음 단계
- Azure NAT 게이트웨이에 대 한 자세한 내용은 [AZURE Nat 게이트웨이][nat-docs]를 참조 하세요.

<!-- LINKS - internal -->


<!-- LINKS - external-->
[nat-docs]: ../virtual-network/nat-gateway/nat-overview.md
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
