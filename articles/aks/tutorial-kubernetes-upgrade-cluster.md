---
title: Azure의 Kubernetes 자습서 - 클러스터 업그레이드
description: 이 AKS(Azure Kubernetes Service) 자습서에서는 기존 AKS 클러스터를 최신 Kubernetes 버전으로 업그레이드하는 방법을 알아봅니다.
services: container-service
ms.topic: tutorial
ms.date: 05/24/2021
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 4751081d628f3ea1bff411b5e391377e8b771939
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2021
ms.locfileid: "110697753"
---
# <a name="tutorial-upgrade-kubernetes-in-azure-kubernetes-service-aks"></a>자습서: AKS(Azure Kubernetes Service)에서 Kubernetes 업그레이드

애플리케이션 및 클러스터 수명 주기의 일부로, Kubernetes 최신 버전으로 업그레이드하여 새 기능을 사용할 수 있습니다. Azure CLI를 사용하여 AKS(Azure Kubernetes Service) 클러스터를 업그레이드할 수 있습니다.

총 7부 중 7부인 이 자습서에서는 Kubernetes 클러스터가 업그레이드됩니다. 다음 방법을 알아봅니다.

> [!div class="checklist"]
> * 현재 및 사용 가능한 Kubernetes 버전 확인
> * Kubernetes 노드 업그레이드
> * 성공적인 업그레이드의 유효성 검사

## <a name="before-you-begin"></a>시작하기 전에

이전 자습서에서 애플리케이션은 컨테이너 이미지로 패키징되었습니다. 이 이미지는 Azure Container Registry로 업로드되었고, AKS 클러스터를 만들었습니다. 그런 다음, 애플리케이션은 AKS 클러스터에 배포되었습니다. 이러한 단계를 아직 수행하지 않았으나 수행하려는 경우 [자습서 1 - 컨테이너 이미지 만들기][aks-tutorial-prepare-app]로 시작합니다.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

이 자습서의 작업을 수행하려면 Azure CLI 버전 2.0.53 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][azure-cli-install]를 참조하세요.

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

이 자습서를 사용하려면 Azure PowerShell 버전 5.9.0 이상을 실행해야 합니다. `Get-InstalledModule -Name Az`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure PowerShell 설치][azure-powershell-install]를 참조하세요.

---

## <a name="get-available-cluster-versions"></a>사용 가능한 클러스터 버전 가져오기

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

클러스터를 업그레이드하려면 먼저 [az aks get-upgrades][] 명령을 사용하여 업그레이드할 수 있는 Kubernetes 릴리스를 확인합니다.

```azurecli
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster
```

다음 예제에서는 현재 버전이 *1.18.10* 이며 사용 가능한 버전이 *업그레이드* 아래에 표시됩니다.

```json
{
  "agentPoolProfiles": null,
  "controlPlaneProfile": {
    "kubernetesVersion": "1.18.10",
    ...
    "upgrades": [
      {
        "isPreview": null,
        "kubernetesVersion": "1.19.1"
      },
      {
        "isPreview": null,
        "kubernetesVersion": "1.19.3"
      }
    ]
  },
  ...
}
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

클러스터를 업그레이드 하기 전에 [Get-AzAksCluster][get-azakscluster] cmdlet을 사용하여 실행 중인 Kubernetes 버전과 해당 버전이 있는 지역을 확인합니다.

```azurepowershell
Get-AzAksCluster -ResourceGroupName myResourceGroup -Name myAKSCluster |
  Select-Object -Property Name, KubernetesVersion, Location
```

다음 예제에서 현재 버전은 *1.19.9* 입니다.

```output
Name         KubernetesVersion Location
----         ----------------- --------
myAKSCluster 1.19.9            eastus
```

[Get-AzAksVersion][get-azaksversion] cmdlet을 사용하여 AKS 클러스터가 있는 지역에서 사용할 수 있는 Kubernetes 업그레이드 릴리스를 확인합니다.

```azurepowershell
Get-AzAksVersion -Location eastus | Where-Object OrchestratorVersion -gt 1.19.9
```

사용 가능한 버전은 *OrchestratorVersion* 아래에 표시됩니다.

```output
OrchestratorType    : Kubernetes
OrchestratorVersion : 1.20.2
DefaultProperty     :
IsPreview           :
Upgrades            : {Microsoft.Azure.Commands.Aks.Models.PSOrchestratorProfile}

OrchestratorType    : Kubernetes
OrchestratorVersion : 1.20.5
DefaultProperty     :
IsPreview           :
Upgrades            : {}
```

---

## <a name="upgrade-a-cluster"></a>클러스터 업그레이드

실행 중인 애플리케이션의 중단을 최소화하기 위해 AKS 노드는 신중하게 차단되고 드레이닝됩니다. 이 프로세스에서 다음 단계가 수행됩니다.

1. Kubernetes 스케줄러는 업그레이드될 노드에서 추가 Pod가 예약되지 않도록 합니다.
1. 노드에서 실행 중인 Pod가 클러스터의 다른 노드에서 예약됩니다.
1. 최신 Kubernetes 구성 요소를 실행하는 노드가 만들어집니다.
1. 새 노드가 준비되고 클러스터에 조인될 때 Kubernetes 스케줄러에서 Pod를 실행하기 시작합니다.
1. 이전 노드가 삭제되고, 클러스터의 다음 노드가 차단 및 드레이닝 프로세스를 시작합니다.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[az aks upgrade][] 명령을 사용하여 AKS 클러스터를 업그레이드합니다.

```azurecli
az aks upgrade \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --kubernetes-version KUBERNETES_VERSION
```

> [!NOTE]
> 부 버전을 한 번에 하나씩 업그레이드할 수 있습니다. 예를 들어 *1.14.x* 에서 *1.15.x* 로 업그레이드할 수 있지만, *1.14.x* 에서 *1.16.x* 로 직접 업그레이드할 수는 없습니다. *1.14.x* 에서 *1.16.x* 로 업그레이드하려면 먼저 *1.14.x* 에서 *1.15.x* 로 업그레이드한 다음, *1.15.x* 에서 *1.16.x* 로 업그레이드합니다.

압축된 다음 예제 출력에서는 *1.19.1* 로 업그레이드한 결과를 보여줍니다. 이제 *kubernetesVersion* 에서 *1.19.1* 을 보고합니다.

```json
{
  "agentPoolProfiles": [
    {
      "count": 3,
      "maxPods": 110,
      "name": "nodepool1",
      "osType": "Linux",
      "storageProfile": "ManagedDisks",
      "vmSize": "Standard_DS1_v2",
    }
  ],
  "dnsPrefix": "myAKSClust-myResourceGroup-19da35",
  "enableRbac": false,
  "fqdn": "myaksclust-myresourcegroup-19da35-bd54a4be.hcp.eastus.azmk8s.io",
  "id": "/subscriptions/<Subscription ID>/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster",
  "kubernetesVersion": "1.19.1",
  "location": "eastus",
  "name": "myAKSCluster",
  "type": "Microsoft.ContainerService/ManagedClusters"
}
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

[Set-AzAksCluster][set-azakscluster] cmdlet을 사용하여 AKS 클러스터를 업그레이드합니다.

```azurepowershell
Set-AzAksCluster -ResourceGroupName myResourceGroup -Name myAKSCluster -KubernetesVersion <KUBERNETES_VERSION>
```

> [!NOTE]
> 부 버전을 한 번에 하나씩 업그레이드할 수 있습니다. 예를 들어 *1.14.x* 에서 *1.15.x* 로 업그레이드할 수 있지만, *1.14.x* 에서 *1.16.x* 로 직접 업그레이드할 수는 없습니다. *1.14.x* 에서 *1.16.x* 로 업그레이드하려면 먼저 *1.14.x* 에서 *1.15.x* 로 업그레이드한 다음, *1.15.x* 에서 *1.16.x* 로 업그레이드합니다.

압축된 다음 예제 출력에서는 *1.19.9* 로 업그레이드한 결과를 보여줍니다. 이제 *kubernetesVersion* 에서 *1.20.2* 를 보고합니다.

```output
ProvisioningState       : Succeeded
MaxAgentPools           : 100
KubernetesVersion       : 1.20.2
PrivateFQDN             :
AgentPoolProfiles       : {default}
Name                    : myAKSCluster
Type                    : Microsoft.ContainerService/ManagedClusters
Location                : eastus
Tags                    : {}
```

---

## <a name="validate-an-upgrade"></a>업그레이드 유효성 검사

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

다음과 같이 [az aks show][] 명령을 사용하여 업그레이드가 성공했는지 확인합니다.

```azurecli
az aks show --resource-group myResourceGroup --name myAKSCluster --output table
```

다음 예제 출력에서는 AKS 클러스터에서 *KubernetesVersion 1.19.1* 을 실행하는 것을 보여줍니다.

```output
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ----------------------------------------------------------------
myAKSCluster  eastus      myResourceGroup  1.19.1               Succeeded            myaksclust-myresourcegroup-19da35-bd54a4be.hcp.eastus.azmk8s.io
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

다음과 같이 [Get-AzAksCluster][get-azakscluster] cmdlet을 사용하여 업그레이드가 성공했는지 확인합니다.

```azurepowershell
Get-AzAksCluster -ResourceGroupName myResourceGroup -Name myAKSCluster |
  Select-Object -Property Name, Location, KubernetesVersion, ProvisioningState
```

다음 예제 출력에서는 AKS 클러스터에서 *KubernetesVersion 1.20.2* 를 실행하는 것을 보여 줍니다.

```output
Name         Location KubernetesVersion ProvisioningState
----         -------- ----------------- -----------------
myAKSCluster eastus   1.20.2            Succeeded
```

---

## <a name="delete-the-cluster"></a>클러스터 삭제

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

이 자습서는 시리즈의 마지막 부분이므로 AKS 클러스터를 삭제하는 것이 좋습니다. Kubernetes 노드가 Azure VM(가상 머신)에서 실행되므로 클러스터를 사용하지 않더라도 요금이 계속 부과됩니다. [az group delete][az-group-delete] 명령을 사용하여 리소스 그룹, 컨테이너 서비스 및 모든 관련 리소스를 제거합니다.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```
### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

이 자습서는 시리즈의 마지막 부분이므로 AKS 클러스터를 삭제하는 것이 좋습니다. Kubernetes 노드가 Azure VM(가상 머신)에서 실행되므로 클러스터를 사용하지 않더라도 요금이 계속 부과됩니다. [Remove-AzResourceGroup][remove-azresourcegroup] cmdlet을 사용하여 리소스 그룹, 컨테이너 서비스 및 모든 관련 리소스를 제거합니다.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

---

> [!NOTE]
> 클러스터를 삭제할 때, AKS 클러스터에 사용되는 Azure Active Directory 서비스 주체는 제거되지 않습니다. 서비스 주체를 제거하는 방법에 대한 단계는 [AKS 서비스 주체 고려 사항 및 삭제][sp-delete]를 참조하세요. 관리 ID를 사용하는 경우 플랫폼에서 ID를 관리하므로 비밀을 프로비저닝하거나 회전할 필요가 없습니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 AKS 클러스터에서 Kubernetes를 업그레이드했습니다. 구체적으로 다음 작업 방법을 알아보았습니다.

> [!div class="checklist"]
> * 현재 및 사용 가능한 Kubernetes 버전 확인
> * Kubernetes 노드 업그레이드
> * 성공적인 업그레이드의 유효성 검사

AKS에 대한 자세한 내용은 [AKS 개요][aks-intro]를 참조하세요. AKS를 사용하여 전체 솔루션을 만드는 방법에 대한 지침은 [AKS 솔루션 지침][aks-solution-guidance]을 참조하세요.

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-intro]: ./intro-kubernetes.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[az aks show]: /cli/azure/aks#az_aks_show
[az aks get-upgrades]: /cli/azure/aks#az_aks_get_upgrades
[az aks upgrade]: /cli/azure/aks#az_aks_upgrade
[azure-cli-install]: /cli/azure/install-azure-cli
[az-group-delete]: /cli/azure/group#az_group_delete
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[aks-solution-guidance]: /azure/architecture/reference-architectures/containers/aks-start-here?WT.mc_id=AKSDOCSPAGE
[azure-powershell-install]: /powershell/azure/install-az-ps
[get-azakscluster]: /powershell/module/az.aks/get-azakscluster
[get-azaksversion]: /powershell/module/az.aks/get-azaksversion
[set-azakscluster]: /powershell/module/az.aks/set-azakscluster
[remove-azresourcegroup]: /powershell/module/az.resources/remove-azresourcegroup
