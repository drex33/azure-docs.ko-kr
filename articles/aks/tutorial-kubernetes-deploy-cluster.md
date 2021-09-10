---
title: Azure의 Kubernertes 자습서 - 클러스터 배포
description: 이 AKS(Azure Kubernetes Service) 자습서에서는 AKS 클러스터를 만들고 kubectl을 사용하여 Kubernetes 마스터 노드에 연결합니다.
services: container-service
ms.topic: tutorial
ms.date: 05/24/2021
ms.custom: mvc, devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 731c656020629b857a19a215027b5c48dca435e4
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121741627"
---
# <a name="tutorial-deploy-an-azure-kubernetes-service-aks-cluster"></a>자습서: AKS(Azure Kubernetes Service) 클러스터 배포

Kubernetes는 컨테이너화된 애플리케이션용 분산 플랫폼을 제공합니다. AKS를 사용하면 프로덕션 준비 Kubernetes 클러스터를 신속하게 만들 수 있습니다. 총 7부 중 3부인 이 자습서에서는 Kubernetes 클러스터가 AKS에 배포됩니다. 다음 방법을 알아봅니다.

> [!div class="checklist"]
> * Azure container registry에 인증할 수 있는 Kubernetes AKS 클러스터 배포
> * Kubernetes CLI(kubectl) 설치
> * AKS 클러스터에 연결하도록 kubectl 구성

후속 자습서에서 Azure Vote 애플리케이션은 클러스터에 배포되고, 크기가 조정되며, 업데이트됩니다.

## <a name="before-you-begin"></a>시작하기 전에

이전 자습서에서는 컨테이너 이미지를 만들어 Azure Container Registry 인스턴스에 업로드했습니다. 이러한 단계를 아직 수행하지 않았으나 수행하려는 경우 [자습서 1 - 컨테이너 이미지 만들기][aks-tutorial-prepare-app]에서 시작합니다.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

이 자습서의 작업을 수행하려면 Azure CLI 버전 2.0.53 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][azure-cli-install]를 참조하세요.

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

이 자습서를 사용하려면 Azure PowerShell 버전 5.9.0 이상을 실행해야 합니다. `Get-InstalledModule -Name Az`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure PowerShell 설치][azure-powershell-install]를 참조하세요.

---

## <a name="create-a-kubernetes-cluster"></a>Kubernetes 클러스터 만들기

AKS 클러스터는 Kubernetes RBAC(Kubernetes 역할 기반 액세스 제어)를 사용할 수 있습니다. 이러한 컨트롤을 통해 사용자에게 할당된 역할에 따라 리소스에 대한 액세스를 정의할 수 있습니다. 사용자에게 여러 역할이 할당된 경우 권한을 결합할 수 있으며, 권한의 범위를 단일 네임스페이스 또는 전체 클러스터로 지정할 수 있습니다. 기본적으로 Azure CLI는 사용자가 AKS 클러스터를 만들면 자동으로 Kubernetes RBAC를 사용하도록 설정합니다.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[az aks create][] 명령을 사용하여 AKS 클러스터를 만듭니다. 다음 예제에서는 *myResourceGroup* 리소스 그룹에 *myAKSCluster* 라는 클러스터를 만듭니다. 이 리소스 그룹은 *eastus* 지역의 [이전 자습서][aks-tutorial-prepare-acr]에서 만들었습니다. 다음 예제에서는 지역을 지정하지 않으므로 AKS 클러스터도 *eastus* 지역에서 생성됩니다. AKS에 대한 리소스 제한 및 지역 가용성에 대한 자세한 내용은 [AKS(Azure Kubernetes Service)의 할당량, 가상 머신 크기 제한 및 지역 가용성][quotas-skus-regions]을 참조하세요.

AKS 클러스터가 다른 Azure 리소스와 상호 작용할 수 있도록 클러스터 ID를 지정하지 않았으므로 자동으로 생성됩니다. 여기에서 이 클러스터 ID에는 이전 자습서에서 만든 ACR(Azure Container Registry) 인스턴스에서 [이미지를 끌어올 수 있는 권한이 부여][container-registry-integration]됩니다. 명령을 성공적으로 실행하려면 Azure 구독에 대한 **소유자** 또는 **Azure 계정 관리자** 역할이 있어야 합니다.

```azurecli
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 2 \
    --generate-ssh-keys \
    --attach-acr <acrName>
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

[New-AzAksCluster][new-azakscluster]를 사용하여 AKS 클러스터를 만듭니다. 다음 예제에서는 *myResourceGroup* 리소스 그룹에 *myAKSCluster* 라는 클러스터를 만듭니다. 이 리소스 그룹은 *eastus* 지역의 [이전 자습서][aks-tutorial-prepare-acr]에서 만들었습니다. 다음 예제에서는 지역을 지정하지 않으므로 AKS 클러스터도 *eastus* 지역에서 생성됩니다. AKS에 대한 리소스 제한 및 지역 가용성에 대한 자세한 내용은 [AKS(Azure Kubernetes Service)의 할당량, 가상 머신 크기 제한 및 지역 가용성][quotas-skus-regions]을 참조하세요.

AKS 클러스터가 다른 Azure 리소스와 상호 작용할 수 있도록 클러스터 ID를 지정하지 않았으므로 자동으로 생성됩니다. 여기에서 이 클러스터 ID에는 이전 자습서에서 만든 ACR(Azure Container Registry) 인스턴스에서 [이미지를 끌어올 수 있는 권한이 부여][container-registry-integration]됩니다. 명령을 성공적으로 실행하려면 Azure 구독에 대한 **소유자** 또는 **Azure 계정 관리자** 역할이 있어야 합니다.

```azurepowershell
New-AzAksCluster -ResourceGroupName myResourceGroup -Name myAKSCluster -NodeCount 2 -GenerateSshKey -AcrNameToAttach <acrName>
```

---

**소유자** 또는 **Azure 계정 관리자** 역할이 필요하지 않도록 하기 위해 ACR에서 이미지를 가져오도록 서비스 주체를 수동으로 구성할 수도 있습니다. 자세한 내용은 [서비스 주체를 사용하여 ACR 인증](../container-registry/container-registry-auth-service-principal.md) 또는 [끌어오기 비밀을 사용하여 Kubernetes에서 인증](../container-registry/container-registry-auth-kubernetes.md)을 참조하세요. 또는 관리를 용이하게 하기 위해 서비스 주체 대신 [관리 ID](use-managed-identity.md)를 사용할 수 있습니다.

몇 분 후에 배포가 완료되고 JSON 형식의 AKS 배포 관련 정보가 반환됩니다.

> [!NOTE]
> 클러스터를 안정적으로 작동하도록 하려면 2개 이상의 노드를 실행해야 합니다.

## <a name="install-the-kubernetes-cli"></a>Kubernetes CLI 설치

로컬 컴퓨터에서 Kubernetes 클러스터에 연결하려면 Kubernetes 명령줄 클라이언트인 [kubectl][kubectl]을 사용합니다.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure Cloud Shell을 사용하는 경우 `kubectl`이 이미 설치되어 있습니다. [az aks install-cli][] 명령을 사용하여 kubectl을 로컬로 설치할 수도 있습니다.

```azurecli
az aks install-cli
```
### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Azure Cloud Shell을 사용하는 경우 `kubectl`이 이미 설치되어 있습니다. [Install-AzAksKubectl][install-azakskubectl] cmdlet을 사용하여 로컬로 설치할 수도 있습니다.

```azurepowershell
Install-AzAksKubectl
```

---

## <a name="connect-to-cluster-using-kubectl"></a>kubectl을 사용하여 클러스터에 연결

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Kubernetes 클러스터에 연결하도록 `kubectl`을 구성하려면 [az aks get-credentials][] 명령을 사용합니다. 다음 예제에서는 *myResourceGroup* 에서 AKS 클러스터 이름 *myAKSCluster* 에 대한 자격 증명을 가져옵니다.

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Kubernetes 클러스터에 연결하도록 `kubectl`을 구성하려면 [Import-AzAksCredential][import-azakscredential] cmdlet을 사용합니다. 다음 예제에서는 *myResourceGroup* 에서 AKS 클러스터 이름 *myAKSCluster* 에 대한 자격 증명을 가져옵니다.

```azurepowershell
Import-AzAksCredential -ResourceGroupName myResourceGroup -Name myAKSCluster
```

---

클러스터에 대한 연결을 확인하려면 [kubectl get nodes][kubectl-get] 명령을 실행하여 클러스터 노드 목록을 반환합니다.

```azurecli-interactive
kubectl get nodes
```

다음 예제 출력에서는 클러스터 노드 목록을 보여 줍니다.

```
$ kubectl get nodes

NAME                                STATUS   ROLES   AGE     VERSION
aks-nodepool1-37463671-vmss000000   Ready    agent   2m37s   v1.18.10
aks-nodepool1-37463671-vmss000001   Ready    agent   2m28s   v1.18.10
```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 AKS에 Kubernetes 클러스터를 배포하고, 이 클러스터에 연결하도록 `kubectl`을 구성했습니다. 구체적으로 다음 작업 방법을 알아보았습니다.

> [!div class="checklist"]
> * Azure container registry에 인증할 수 있는 Kubernetes AKS 클러스터 배포
> * Kubernetes CLI(kubectl) 설치
> * AKS 클러스터에 연결하도록 kubectl 구성

그 다음 자습서를 계속 진행하여 클러스터에 애플리케이션을 배포하는 방법을 알아보세요.

> [!div class="nextstepaction"]
> [Kubernetes에서 애플리케이션 배포][aks-tutorial-deploy-app]

<!-- LINKS - external -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- LINKS - internal -->
[aks-tutorial-deploy-app]: ./tutorial-kubernetes-deploy-application.md
[aks-tutorial-prepare-acr]: ./tutorial-kubernetes-prepare-acr.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[az ad sp create-for-rbac]: /cli/azure/ad/sp#az_ad_sp_create_for_rbac
[az acr show]: /cli/azure/acr#az_acr_show
[az role assignment create]: /cli/azure/role/assignment#az_role_assignment_create
[az aks create]: /cli/azure/aks#az_aks_create
[az aks install-cli]: /cli/azure/aks#az_aks_install_cli
[az aks get-credentials]: /cli/azure/aks#az_aks_get_credentials
[azure-cli-install]: /cli/azure/install-azure-cli
[container-registry-integration]: ./cluster-container-registry-integration.md
[quotas-skus-regions]: quotas-skus-regions.md
[azure-powershell-install]: /powershell/azure/install-az-ps
[new-azakscluster]: /powershell/module/az.aks/new-azakscluster
[install-azakskubectl]: /powershell/module/az.aks/install-azakskubectl
[import-azakscredential]: /powershell/module/az.aks/import-azakscredential
