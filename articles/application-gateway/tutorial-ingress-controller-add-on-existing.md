---
title: '자습서: 기존 Azure Application Gateway를 사용하여 기존 AKS 클러스터에 수신 컨트롤러 추가 기능을 사용하도록 설정'
description: 이 자습서의 설명에 따라 기존 Application Gateway를 사용하여 기존 AKS 클러스터에 수신 컨트롤러 추가 기능을 사용하도록 설정합니다.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: tutorial
ms.date: 03/02/2021
ms.author: caya
ms.openlocfilehash: 8dba1e6536ce2d3533daf97f8af5e17ef5ce40fa
ms.sourcegitcommit: 331a5c3ad498061511383b80760349ff2a966bcf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/29/2021
ms.locfileid: "133218922"
---
# <a name="tutorial-enable-application-gateway-ingress-controller-add-on-for-an-existing-aks-cluster-with-an-existing-application-gateway"></a>자습서: 기존 Application Gateway를 사용하여 기존 AKS 클러스터에 Application Gateway 수신 컨트롤러 추가 기능을 사용하도록 설정(미리 보기)

Azure CLI 또는 포털을 사용하여 기존 [AKS(Azure Kubernetes Services)](https://azure.microsoft.com/services/kubernetes-service/) 클러스터에 대한 [AGIC(Application Gateway 수신 컨트롤러)](ingress-controller-overview.md) 추가 기능을 사용하도록 설정할 수 있습니다. 이 자습서에서는 AGIC 추가 기능을 사용하여 별도의 가상 네트워크에 배포된 기존 Application Gateway를 통해 Kubernetes 애플리케이션을 기존 AKS 클러스터에 공개하는 방법을 알아봅니다. 먼저 한 가상 네트워크에 AKS 클러스터를 만든 다음, 별도의 가상 네트워크에 Application Gateway를 만들어 기존 리소스를 시뮬레이션합니다. 그런 다음, AGIC 추가 기능을 사용하도록 설정하고, 두 가상 네트워크를 피어링하고, AGIC 추가 기능을 사용하여 Application Gateway를 통해 공개할 샘플 애플리케이션을 배포합니다. 동일한 가상 네트워크의 기존 Application Gateway 및 기존 AKS 클러스터에 AGIC 추가 기능을 사용하도록 설정하는 경우 아래의 피어링 단계를 건너뛸 수 있습니다. 이 추가 기능은 [이전의 Helm](ingress-controller-overview.md#difference-between-helm-deployment-and-aks-add-on)보다 훨씬 빠르게 AKS 클러스터에 대한 AGIC를 배포하는 방법을 제공하며, 완전 관리형 환경도 제공합니다.  

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * 리소스 그룹 만들기 
> * 새 AKS 클러스터 만들기 
> * 새 Application Gateway 만들기 
> * Azure CLI를 통해 기존 AKS 클러스터에서 AGIC 추가 기능 사용 
> * 포털을 통해 기존 AKS 클러스터에서 AGIC 추가 기능 사용 
> * Application Gateway 가상 네트워크를 AKS 클러스터 가상 네트워크와 피어링
> * 수신용 AGIC를 사용하여 AKS 클러스터에 샘플 애플리케이션 배포
> * Application Gateway를 통해 애플리케이션에 연결할 수 있는지 확인

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

Azure에서 관련 리소스를 리소스 그룹에 할당합니다. [az group create](/cli/azure/group#az_group_create)를 사용하여 리소스 그룹을 만듭니다. 다음 예제에서는 *canadacentral* 위치(지역)에 *myResourceGroup* 이라는 리소스 그룹을 만듭니다. 

```azurecli-interactive
az group create --name myResourceGroup --location canadacentral
```

## <a name="deploy-a-new-aks-cluster"></a>새 AKS 클러스터 배포

이제 새 AKS 클러스터를 배포하고, AGIC 추가 기능을 사용하도록 설정하려는 기존 AKS 클러스터를 시뮬레이션합니다.  

다음 예제에서는 [Azure CNI](../aks/concepts-network.md#azure-cni-advanced-networking) 및 [관리 ID](../aks/use-managed-identity.md)를 사용하여 앞에서 만든 리소스 그룹 *myResourceGroup* 에 새 AKS 클러스터 *myCluster* 를 배포합니다.

```azurecli-interactive
az aks create -n myCluster -g myResourceGroup --network-plugin azure --enable-managed-identity 
```

`az aks create` 명령에 대한 추가 매개 변수를 구성하려면 [여기](/cli/azure/aks#az_aks_create)를 참조하세요. 

## <a name="deploy-a-new-application-gateway"></a>새 Application Gateway 배포 

이제 새 Application Gateway를 배포하고, AKS 클러스터 *myCluster* 에 대한 트래픽 부하를 분산하는 데 사용하려는 기존 Application Gateway를 시뮬레이션합니다. Application Gateway 이름은 *myApplicationGateway* 이지만, 먼저 *myPublicIp* 라는 공용 IP 리소스, 주소 공간이 11.0.0.0/8인 *myVnet* 이라는 새 가상 네트워크, 주소 공간이 11.1.0.0/16인 *mySubnet* 이라는 서브넷을 만들고 *myPublicIp* 를 사용하여 *mySubnet* 에 Application Gateway를 배포해야 합니다. 

별도의 가상 네트워크에서 AKS 클러스터 및 Application Gateway를 사용하는 경우 두 가상 네트워크의 주소 공간이 겹치지 않아야 합니다. AKS 클러스터가 배포하는 기본 주소 공간은 10.0.0.0/8이므로 Application Gateway 가상 네트워크 주소 접두사를 11.0.0.0/8로 설정합니다. 

```azurecli-interactive
az network public-ip create -n myPublicIp -g myResourceGroup --allocation-method Static --sku Standard
az network vnet create -n myVnet -g myResourceGroup --address-prefix 11.0.0.0/8 --subnet-name mySubnet --subnet-prefix 11.1.0.0/16 
az network application-gateway create -n myApplicationGateway -l canadacentral -g myResourceGroup --sku Standard_v2 --public-ip-address myPublicIp --vnet-name myVnet --subnet mySubnet
```

> [!NOTE]
> AGIC(Application Gateway 수신 컨트롤러) 추가 기능은 Application Gateway v2 SKU(표준 및 WAF)**만** 지원하고 Application Gateway v1 SKU를 지원하지 **않습니다**. 

## <a name="enable-the-agic-add-on-in-existing-aks-cluster-through-azure-cli"></a>Azure CLI를 통해 기존 AKS 클러스터에서 AGIC 추가 기능 사용 

Azure CLI를 계속 사용하려면 만든 AKS 클러스터 *myCluster* 에서 AGIC 추가 기능을 계속 사용하도록 설정하고, 앞에서 만든 기존 Application Gateway *myApplicationGateway* 를 사용하도록 AGIC 추가 기능을 지정합니다.

```azurecli-interactive
appgwId=$(az network application-gateway show -n myApplicationGateway -g myResourceGroup -o tsv --query "id") 
az aks enable-addons -n myCluster -g myResourceGroup -a ingress-appgw --appgw-id $appgwId
```

## <a name="enable-the-agic-add-on-in-existing-aks-cluster-through-portal"></a>포털을 통해 기존 AKS 클러스터에서 AGIC 추가 기능 사용 

Azure Portal을 사용하여 AGIC 추가 기능을 사용하도록 설정하려면 [(https://aka.ms/azure/portal/aks/agic)](https://aka.ms/azure/portal/aks/agic)으로 이동하고 포털 링크를 통해 AKS 클러스터로 이동합니다. 여기에서 AKS 클러스터 내의 네트워킹 탭으로 이동합니다. 포털 UI를 사용하여 수신 컨트롤러 추가 기능을 사용하거나 사용하지 않도록 설정할 수 있는 Application Gateway 수신 컨트롤러 섹션이 표시됩니다. "수신 컨트롤러 사용" 옆의 상자를 선택하고 드롭다운 메뉴에서 만든 Application Gateway *myApplicationGateway* 를 선택합니다. 

![Application Gateway 수신 컨트롤러 포털](./media/tutorial-ingress-controller-add-on-existing/portal-ingress-controller-add-on.png)

## <a name="peer-the-two-virtual-networks-together"></a>두 가상 네트워크를 피어링

한 가상 네트워크에 AKS 클러스터를 배포하고 다른 가상 네트워크에 Application Gateway를 배포했으므로, 이제 트래픽이 Application Gateway에서 클러스터의 Pod로 흐르도록 두 가상 네트워크를 피어링해야 합니다. 두 가상 네트워크를 피어링하려면 Azure CLI 명령을 두 번 실행하여 양방향으로 연결해야 합니다. 첫 번째 명령은 Application Gateway 가상 네트워크에서 AKS 가상 네트워크로 피어링 연결을 만들고, 두 번째 명령은 반대 방향으로 피어링 연결을 만듭니다.

```azurecli-interactive
nodeResourceGroup=$(az aks show -n myCluster -g myResourceGroup -o tsv --query "nodeResourceGroup")
aksVnetName=$(az network vnet list -g $nodeResourceGroup -o tsv --query "[0].name")

aksVnetId=$(az network vnet show -n $aksVnetName -g $nodeResourceGroup -o tsv --query "id")
az network vnet peering create -n AppGWtoAKSVnetPeering -g myResourceGroup --vnet-name myVnet --remote-vnet $aksVnetId --allow-vnet-access

appGWVnetId=$(az network vnet show -n myVnet -g myResourceGroup -o tsv --query "id")
az network vnet peering create -n AKStoAppGWVnetPeering -g $nodeResourceGroup --vnet-name $aksVnetName --remote-vnet $appGWVnetId --allow-vnet-access
```

## <a name="deploy-a-sample-application-using-agic"></a>AGIC를 사용하여 샘플 애플리케이션 배포 

이제 수신용 AGIC 추가 기능을 사용할 샘플 애플리케이션을 앞에서 만든 AKS 클러스터에 배포하고, Application Gateway를 AKS 클러스터에 연결합니다. 먼저 `az aks get-credentials` 명령을 실행하여 앞에서 배포한 AKS 클러스터에 대한 자격 증명을 가져옵니다. 

```azurecli-interactive
az aks get-credentials -n myCluster -g myResourceGroup
```

앞에서 만든 클러스터에 대한 자격 증명이 있으므로, 다음 명령을 실행하여 클러스터에 수신용 AGIC를 사용하는 샘플 애플리케이션을 설정합니다. AGIC는 이전에 해당 회람 규칙을 사용하여 설정한 Application Gateway를 앞에서 배포한 새 샘플 애플리케이션으로 업데이트합니다.  

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/aspnetapp.yaml 
```

## <a name="check-that-the-application-is-reachable"></a>애플리케이션에 연결할 수 있는지 확인

Application Gateway가 AKS 클러스터에 대한 트래픽을 제공하도록 설정되었으므로 이제 애플리케이션에 연결할 수 있는지 확인합니다. 먼저 수신 IP 주소를 가져옵니다. 

```azurecli-interactive
kubectl get ingress
```

위의 명령을 실행하여 얻은 Application Gateway의 IP 주소를 방문하거나 `curl` 명령을 실행하여 앞에서 만든 샘플 애플리케이션이 실행 중인지 확인합니다. Application Gateway가 업데이트를 가져오는 데 1분 정도 걸릴 수 있으므로, 포털에서 Application Gateway 상태가 "업데이트 중"이면 완료될 때까지 기다렸다가 IP 주소에 연결하세요. 

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요 없는 리소스 그룹, 애플리케이션 게이트웨이 및 모든 관련 리소스를 제거합니다.

```azurecli-interactive
az group delete --name myResourceGroup 
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [AGIC 추가 기능을 사용하지 않도록 설정하는 방법에 대한 자세한 정보](./ingress-controller-disable-addon.md)
