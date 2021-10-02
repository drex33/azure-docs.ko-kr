---
title: AKS(Azure Kubernetes Service) 클러스터 업그레이드
description: AKS(Azure Kubernetes Service) 클러스터를 업그레이드하여 최신 기능 및 보안 업데이트를 가져오는 방법에 대해 알아봅니다.
services: container-service
ms.topic: article
ms.date: 12/17/2020
ms.openlocfilehash: 0f4e364cd3de9093b84e3ae02c4337361985959a
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129350980"
---
# <a name="upgrade-an-azure-kubernetes-service-aks-cluster"></a>AKS(Azure Kubernetes Service) 클러스터 업그레이드

AKS 클러스터 수명 주기 중 일부에는 최신 Kubernetes 버전으로 정기적으로 업그레이드하는 작업이 포함됩니다. 최신 보안 릴리스를 적용하거나 업그레이드하여 최신 기능을 받는 것은 중요합니다. 이 문서에서는 AKS 클러스터에서 마스터 구성 요소 또는 단일 기본 노드 풀을 업그레이드하는 방법을 보여 줍니다.

여러 노드 풀 또는 Windows Server 노드를 사용하는 AKS 클러스터의 경우 [AKS에서 노드 풀 업그레이드][nodepool-upgrade]를 참조하세요.

## <a name="before-you-begin"></a>시작하기 전에

이 문서를 진행하려면 Azure CLI 버전 2.0.65 이상을 실행하고 있어야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][azure-cli-install]를 참조하세요.

> [!WARNING]
> AKS 클러스터 업그레이드는 노드의 차단 및 드레이닝을 트리거합니다. 사용 가능한 계산 할당량이 낮은 경우 업그레이드가 실패할 수 있습니다. 자세한 내용은 [할당량 늘리기](../azure-portal/supportability/regional-quota-requests.md)를 참조하세요.

## <a name="check-for-available-aks-cluster-upgrades"></a>사용할 수 있는 AKS 클러스터 업그레이드를 위한 확인

클러스터에 사용할 수 있는 Kubernetes 릴리스를 확인하려면 [az aks get-upgrades][az-aks-get-upgrades] 명령을 사용합니다. 다음 예제에서는 *myResourceGroup* 의 *myAKSCluster* 에 가능한 업그레이드를 확인합니다.

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster --output table
```

> [!NOTE]
> 지원되는 AKS 클러스터를 업그레이드하는 경우 Kubernetes 부 버전은 건너뛸 수 없습니다. 모든 업그레이드는 주 버전 번호로 순차적으로 수행되어야 합니다. 예를 들어, *1.14.x* -> *1.15.x* 또는 *1.15.x* -> *1.16.x* 업그레이드는 가능하지만 *1.14.x* -> *1.16.x* 업그레이드는 가능하지 않습니다. 
> > 지원되지 않는 버전에서 지원되는 버전으로 다시 업그레이드하는 경우에만 여러 버전을 건너뛸 수 있습니다. 예를 들어, 지원되지 않는 *1.10.x* 버전에서 지원되는 *1.15.x* 버전으로 업그레이드를 완료할 수 있습니다.

다음 예제 출력에서는 *1.19.1* 또는 *1.19.3* 버전으로 클러스터를 업그레이드할 수 있음을 보여줍니다.

```console
Name     ResourceGroup    MasterVersion    Upgrades
-------  ---------------  ---------------  --------------
default  myResourceGroup  1.18.10          1.19.1, 1.19.3
```

업그레이드를 사용할 수 없는 경우 다음 메시지가 표시됩니다.

```console
ERROR: Table output unavailable. Use the --query option to specify an appropriate query. Use --debug for more info.
```

## <a name="customize-node-surge-upgrade"></a>노드 서지 업그레이드 사용자 지정

> [!Important]
> 노드 서지에는 각 업그레이드 작업에 요청된 최대 서지 수에 대한 구독 할당량이 필요합니다. 예를 들어 각각 4개의 노드가 있는 5개의 노드 풀을 가진 클러스터에는 총 20개의 노드가 있습니다. 각 노드 풀의 최대 서지 값이 50%일 경우, 업그레이드를 완료하려면 10개 노드의 추가 계산 및 IP 할당량(노드 2개 * 풀 5개)이 필요합니다.
>
> Azure CNI를 사용하는 경우 서브넷에 사용할 수 있는 IP가 있는지 확인하여 [Azure CNI의 IP 요구 사항을 충족](configure-azure-cni.md)할 수 있습니다.

기본적으로 AKS는 하나의 추가 노드를 사용하여 서지로 업그레이드를 구성합니다. 최대 서지 설정에 기본값 1을 사용하면 AKS가 기존 애플리케이션의 연결/드레인 전에 추가 노드를 만들어 이전 버전의 노드를 대체함으로써 워크로드가 중단되는 것을 최소화할 수 있습니다. 업그레이드 속도와 업그레이드가 중단되는 현상을 절충하기 위해 최대 서지 값을 노드 풀별로 맞춤 설정할 수 있습니다. 최대 서지 값을 늘리면 업그레이드 프로세스가 더 빠르게 완료되지만 최대 서지 값을 크게 설정하면 업그레이드 프로세스가 중단될 수 있습니다. 

예를 들어 최대 서지 값이 100%면 가능한 가장 빠른 업그레이드 프로세스(노드 수 두 배)를 제공하지만 노드 풀의 모든 노드가 동시에 드레이닝됩니다. 테스트 환경에 이처럼 더 높은 값을 사용하기를 원할 수도 있습니다. 프로덕션 노드 풀의 경우 33%의 max_surge 설정을 하는 것이 좋습니다.

AKS는 정수 값과 최대 서지의 백분율 값을 모두 허용합니다. "5"와 같은 정수는 서지할 5개의 추가 노드를 나타냅니다. "50%"값은 풀에서 현재 노드 수의 절반에 해당하는 서지 값을 나타냅니다. 최소 서지 값은 1%이고 최대 값은 100%가 될 수 있습니다. 백분율 값은 가장 가까운 노드 수로 반올림 됩니다. 업그레이드 시 최대 서지 값이 현재 노드 수보다 적으면 현재 노드 수가 최대 서지 값으로 사용됩니다.

업그레이드하는 동안 최소 서지 값은 1이고 최대 값은 노드 풀의 노드 수와 같을 수 있습니다. 더 큰 값을 설정할 수 있지만, 최대 서지 수에 사용되는 최대 노드 수는 업그레이드 시 풀의 노드 수보다 높을 수 없습니다.

> [!Important]
> 노드 풀의 최대 서지 설정은 영구적입니다.  이후 Kubernetes 업그레이드 또는 노드 버전 업그레이드 시 이 설정이 사용됩니다. 언제든지 노드 풀의 최대 서지 값을 변경할 수 있습니다. 프로덕션 노드 풀의 경우 33%의 최대 서지 설정을 하는 것이 좋습니다.

다음 명령을 사용하여 새 노드 풀 또는 기존 노드 풀의 최대 서지 값을 설정할 수 있습니다.

```azurecli-interactive
# Set max surge for a new node pool
az aks nodepool add -n mynodepool -g MyResourceGroup --cluster-name MyManagedCluster --max-surge 33%
```

```azurecli-interactive
# Update max surge for an existing node pool 
az aks nodepool update -n mynodepool -g MyResourceGroup --cluster-name MyManagedCluster --max-surge 5
```

## <a name="upgrade-an-aks-cluster"></a>AKS 클러스터 업그레이드

AKS 클러스터에 대한 사용 가능한 버전 목록을 통해 [az aks upgrade][az-aks-upgrade] 명령을 사용하여 업그레이드합니다. 업그레이드 프로세스 중 AKS는 다음을 수행합니다. 
- 지정된 Kubernetes 버전을 실행하는 클러스터에 새 버퍼 노드(또는 [최대 서지](#customize-node-surge-upgrade)로 구성된 노드)를 추가합니다. 
- 이전 노드 중 하나를 [차단 및 드레이닝][kubernetes-drain]하여 실행 중인 애플리케이션의 중단을 최소화합니다. 최대 서지를 사용하는 경우, 지정된 버퍼 노드 수만큼 동시에 [차단 및 드레이닝][kubernetes-drain]합니다. 
- 이전 노드가 완전히 드레이닝되면 새 버전을 받기 위해 이미지로 다시 설치되고 업그레이드할 다음 노드의 버퍼 노드가 됩니다. 
- 이 프로세스는 클러스터의 모든 노드가 업그레이드될 때까지 반복됩니다. 
- 프로세스가 끝나면 마지막 버퍼 노드가 삭제되어 기존 에이전트 노드 수와 영역 잔량이 유지됩니다.

```azurecli-interactive
az aks upgrade \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --kubernetes-version KUBERNETES_VERSION
```

노드 수에 따라 클러스터를 업그레이드하는 데 몇 분 정도 걸립니다.

> [!IMPORTANT]
> `PodDisruptionBudgets`(PDB)가 한 번에 1개 이상의 Pod 복제본을 이동하도록 허용해야 합니다. 그렇지 않으면 드레이닝/제거 작업이 실패합니다.
> 드레d이닝 작업이 실패하면 애플리케이션이 중단되지 않도록 업그레이드 작업이 의도적으로 실패합니다. 작업 중지(잘못된 PDB, 할당량 부족 등)의 원인을 수정한 후 작업을 다시 시도하세요.

업그레이드가 성공적이었는지 확인하려면 [az aks show][az-aks-show] 명령을 사용합니다.

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --output table
```

다음 예제 출력은 클러스터가 이제 *1.18.10* 을 실행하고 있음을 보여줍니다.

```json
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ----------------------------------------------
myAKSCluster  eastus      myResourceGroup  1.18.10              Succeeded            myakscluster-dns-379cbbb9.hcp.eastus.azmk8s.io
```

## <a name="set-auto-upgrade-channel"></a>자동 업그레이드 채널 설정

클러스터를 수동으로 업그레이드하는 것 외에도 클러스터에서 자동 업그레이드 채널을 설정할 수 있습니다. 다음과 같은 채널이 지원됩니다.

|채널| 작업 | 예제
|---|---|---|
| `none`| 자동 업그레이드를 비활성화하고 클러스터를 현재 버전의 Kubernetes로 유지합니다.| 변경되지 않은 상태로 유지되는 경우의 기본 설정|
| `patch`| 부 버전은 동일하게 유지하고 클러스터를 사용할 수 있게 되면 지원되는 최신 패치 버전으로 자동으로 업그레이드합니다.| 예를 들어, 클러스터가 *1.17.7* 버전을 실행하고 있으며, *1.17.9*, *1.18.4*, *1.18.6*, 및 *1.19.1* 버전을 이용할 수 있는 경우, 클러스터는 *1.17.9* 버전으로 업그레이드됩니다.|
| `stable`| 부 버전 *N-1* 에서 지원되는 최신 패치 릴리스로 클러스터를 자동 업그레이드합니다. 여기서 숫자 *N* 은 지원되는 최신 부 버전입니다.| 예를 들어, 클러스터가 *1.17.7* 버전을 실행하고 있으며, *1.17.9*, *1.18.4*, *1.18.6*, 및 *1.19.1* 버전을 이용할 수 있는 경우, 클러스터는 *1.18.6* 버전으로 업그레이드됩니다.
| `rapid`| 지원되는 최신 부 버전에서 지원되는 최신 패치 릴리스로 클러스터를 자동으로 업그레이드합니다.| Kubernetes 버전이 *N-2* 인(여기서 *N* 은 지원되는 최신 부 버전) 클러스터의 경우, 클러스터는 우선 *N-1* 부 버전의 지원되는 최신 패치 버전으로 업그레이드됩니다. 예를 들어, 클러스터가 *1.17.7* 버전을 실행하고 *1.17.9*, *1.18.4*, *1.18.6*, 및 *1.19.1* 버전을 이용할 수 있는 경우, 클러스터는 우선 *1.18.6* 버전으로 업그레이드되고 이후 *1.19.1* 버전으로 업그레이드됩니다.
| `node-image`| 노드 이미지를 사용 가능한 최신 버전으로 자동으로 업그레이드합니다.| Microsoft는 이미지 노드에 대한 패치 및 새 이미지를 자주(일반적으로 매주) 제공하지만 노드 이미지 업그레이드를 수행하지 않는 한 실행 중인 노드는 새 이미지를 얻지 못합니다. 노드 이미지 채널을 켜면 새 버전을 사용할 수 있을 때마다 노드 이미지가 자동으로 업데이트됩니다. |

> [!NOTE]
> 클러스터 자동 업그레이드는 Kubernetes의 GA 버전으로만 업데이트되며 미리 보기 버전으로 업데이트되지 않습니다.

클러스터를 자동으로 업그레이드하는 것은 클러스터를 수동으로 업그레이드하는 것과 동일한 프로세스를 따릅니다. 자세한 내용은 [AKS 클러스터 업그레이드][upgrade-cluster]를 참조하세요.

AKS 클러스터에 대한 클러스터 자동 업그레이드는 미리 보기 기능입니다.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

`az cli`에 대해 다음 확장자를 추가합니다.

```azurecli-interactive
az extension add --name aks-preview
```

`AutoUpgradePreview`다음 예제와 같이 [az feature register][az-feature-register] 명령을 사용하여 기능 플래그를 등록 합니다.

```azurecli-interactive
az feature register --namespace Microsoft.ContainerService -n AutoUpgradePreview
```

상태를 *등록 된* 것으로 표시 하는 데 몇 분 정도 걸릴 수 있습니다. 등록이 완료 될 때까지 기다려 주세요. [Az feature list][az-feature-list] 명령을 사용하여 등록 상태를 확인 합니다.

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AutoUpgradePreview')].{Name:name,State:properties.state}"
```

준비가 되면 [az provider register][az-provider-register] 명령을 사용하여 *ContainerService* 리소스 공급자의 등록을 새로 고칩니다.

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

클러스터를 만들 때 자동 업그레이드 채널을 설정하려면 다음 예제와 유사하게 자동 업그레이드 채널 매개 변수를 사용합니다.

```azurecli-interactive
az aks create --resource-group myResourceGroup --name myAKSCluster --auto-upgrade-channel stable --generate-ssh-keys
```

기존 클러스터에서 자동 업그레이드 채널을 설정하려면 다음 예제와 유사하게 자동 업그레이드 채널 매개 변수를 업데이트합니다.

```azurecli-interactive
az aks update --resource-group myResourceGroup --name myAKSCluster --auto-upgrade-channel stable
```

## <a name="using-cluster-auto-upgrade-with-planned-maintenance"></a>계획된 유지 관리와 함께 클러스터 자동 업그레이드 사용

계획된 유지 관리 및 자동 업그레이드를 사용하는 경우 지정된 유지 관리 기간 동안 업그레이드가 시작됩니다. 계획된 유지 관리에 대한 자세한 내용은 [계획된 유지 관리를 사용하여 AKS(Azure Kubernetes Service) 클러스터의 유지 관리 기간 예약(미리 보기)][planned-maintenance]을 참조하세요.

## <a name="special-considerations-for-node-pools-that-span-multiple-availability-zones"></a>여러 가용성 영역 확장 된 노드 풀에 대 한 특별 고려 사항

AKS은 노드 그룹에서 최상의 영역 분산을 사용 합니다. 업그레이드 서 수 동안 VMSS의 서 수 노드에 대 한 영역을 미리 알 수 없습니다. 이로 인해 업그레이드 중에 불균형 영역 구성이 일시적으로 발생할 수 있습니다. 그러나 AKS는 업그레이드가 완료 되 면 서 수 노드를 삭제 하 고 원래 영역 잔액을 유지 합니다. 업그레이드 하는 동안 영역을 균형 있게 유지 하려면 3 개 노드의 배수로 서 수를 늘립니다. 그런 다음 VMSS는 최상의 영역 분산을 사용 하 여 가용성 영역에서 노드를 분산 시킵니다.

Azure LRS 디스크에 의해 지원 되는 Pvc가 있는 경우 특정 영역에 바인딩되고, 서 지 노드가 PVC 영역과 일치 하지 않는 경우 즉시 복구 되지 않을 수 있습니다. 이로 인해 업그레이드 작업에서 노드가 드레이닝 될 때 응용 프로그램이 가동 중지 될 수 있지만 PVs는 영역에 바인딩됩니다. 이러한 사례를 처리 하 고 고가용성을 유지 하려면 응용 프로그램에 대 한 [Pod 중단 예산을](https://kubernetes.io/docs/tasks/run-application/configure-pdb/) 구성 합니다. 이렇게 하면 업그레이드의 드레이닝 작업 중에 Kubernetes에서 가용성 요구 사항을 준수할 수 있습니다. 


## <a name="next-steps"></a>다음 단계

이 문서에서는 기존 AKS 클러스터를 업그레이드하는 방법을 설명했습니다. AKS 클러스터의 배포 및 관리에 대해 더 자세히 알아보려면 자습서 세트를 참조하세요.

> [!div class="nextstepaction"]
> [AKS 자습서][aks-tutorial-prepare-app]

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-upgrades]: /cli/azure/aks#az_aks_get_upgrades
[az-aks-upgrade]: /cli/azure/aks#az_aks_upgrade
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-provider-register]: /cli/azure/provider#az_provider_register
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[upgrade-cluster]:  #upgrade-an-aks-cluster
[planned-maintenance]: planned-maintenance.md