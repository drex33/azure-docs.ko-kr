---
title: RDP와 AKS Windows Server 노드 연결
titleSuffix: Azure Kubernetes Service
description: 문제 해결 및 유지 관리 작업을 위해 AKS(Azure Kubernetes Service) 클러스터 Windows Server와 RDP 연결을 만드는 방법을 알아봅니다.
services: container-service
ms.topic: article
ms.date: 06/04/2019
ms.openlocfilehash: 2aa7b947b547a5cfa10afac7b29bce342d49fe0e
ms.sourcegitcommit: 3a063c59bb9396ce1d4b9a3565b194edf30393a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2021
ms.locfileid: "132964171"
---
# <a name="connect-with-rdp-to-azure-kubernetes-service-aks-cluster-windows-server-nodes-for-maintenance-or-troubleshooting"></a>유지 관리 또는 문제 해결을 위해 AKS(Azure Kubernetes Service) 클러스터 Windows Server 노드와 RDP와 연결

AKS(Azure Kubernetes Service) 클러스터의 수명 주기 내내 AKS Windows Server 노드에 액세스해야 합니다. 유지 관리, 로그 수집 또는 기타 문제 해결 작업을 위해 이 액세스를 사용할 수 있습니다. RDP를 사용하여 AKS Windows Server 노드에 액세스할 수 있습니다. 또는 AKS Windows Server 노드에 액세스하는 데 SSH를 사용하고자 하고 클러스터를 만드는 동안 사용된 것과 동일한 키 쌍에 대한 액세스 권한이 있는 경우 [AKS(Azure Kubernetes Service) 클러스터 노드에 대한 SSH 연결 만들기][ssh-steps]의 단계를 따라 연결할 수 있습니다. 보안을 위해 AKS 노드는 인터넷에 노출되지 않습니다.

이 문서에서는 개인 IP 주소를 사용하여 AKS 노드와 RDP를 연결하는 방법을 보여 줍니다.

## <a name="before-you-begin"></a>시작하기 전에

이 문서에서는 Windows Server 노드가 포함된 기존 AKS 클러스터가 있다고 가정합니다. AKS 클러스터가 필요한 경우 [Azure CLI를 사용하여 Windows 컨테이너가 있는 AKS 클러스터 만들기][aks-windows-cli] 문서를 참조하세요. 문제를 해결하려는 Windows Server 노드에 대한 Windows 관리자 사용자 이름과 암호가 필요합니다. 또한 [Microsoft 원격 데스크톱][rdp-mac]과 같은 RDP 클라이언트가 필요합니다.

암호를 재설정해야 하는 경우 를 사용하여 암호를 변경할 수 `az aks update` 있습니다.

```azurecli-interactive
az aks update -g myResourceGroup -n myAKSCluster --windows-admin-password $WINDOWS_ADMIN_PASSWORD
```

사용자 이름과 암호를 모두 다시 설정해야 하는 경우 [Windows VM에서 원격 데스크톱 서비스 또는 관리자 암호 재설정을 참조하세요. ](/troubleshoot/azure/virtual-machines/reset-rdp)

또한 Azure CLI 버전 2.0.61 이상이 설치되고 구성되어 있어야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][install-azure-cli]를 참조하세요.

## <a name="deploy-a-virtual-machine-to-the-same-subnet-as-your-cluster"></a>클러스터와 동일한 서브넷에 가상 머신 배포

AKS 클러스터의 Windows Server 노드에 외부에서 액세스 가능한 IP 주소가 없습니다. RDP 연결을 만들려면 공개적으로 액세스할 수 있는 IP 주소를 사용하는 가상 머신을 Windows Server 노드와 동일한 서브넷에 배포할 수 있습니다.

다음 예제에서는 *myResourceGroup* 리소스 그룹에 *myVM* 이라는 가상 머신을 만듭니다.

먼저 Windows Server 노드 풀에서 사용하는 서브넷을 가져옵니다. 서브넷 ID를 가져오려면 서브넷 이름이 필요합니다. 서브넷 이름을 가져오려면 VNet 이름이 필요합니다. 클러스터에 네트워크 목록을 쿼리하여 vnet 이름을 가져옵니다. 클러스터를 쿼리하려면 클러스터 이름이 필요합니다. Azure Cloud Shell에서 다음을 실행하여 모든 관련 항목을 가져올 수 있습니다.

```azurecli-interactive
CLUSTER_RG=$(az aks show -g myResourceGroup -n myAKSCluster --query nodeResourceGroup -o tsv)
VNET_NAME=$(az network vnet list -g $CLUSTER_RG --query [0].name -o tsv)
SUBNET_NAME=$(az network vnet subnet list -g $CLUSTER_RG --vnet-name $VNET_NAME --query [0].name -o tsv)
SUBNET_ID=$(az network vnet subnet show -g $CLUSTER_RG --vnet-name $VNET_NAME --name $SUBNET_NAME --query id -o tsv)
```

SUBNET_ID가 있으므로 이제 동일한 Azure Cloud Shell 창에서 다음 명령을 실행하여 VM을 만듭니다.

```azurecli-interactive
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image win2019datacenter \
    --admin-username azureuser \
    --admin-password myP@ssw0rd12 \
    --subnet $SUBNET_ID \
    --query publicIpAddress -o tsv
```

다음 예제 출력에서는 VM이 성공적으로 만들어졌음을 보여 주고 가상 머신의 공용 IP 주소를 표시합니다.

```console
13.62.204.18
```

가상 머신의 공용 IP 주소를 기록해 둡니다. 이후 단계에서 해당 주소가 필요합니다.

## <a name="allow-access-to-the-virtual-machine"></a>가상 머신에 대한 액세스 허용

AKS 노드 풀 서브넷은 기본적으로 NSG(네트워크 보안 그룹)로 보호됩니다. 가상 머신에 액세스하려면 NSG에서 액세스를 사용해야 합니다.

> [!NOTE]
> NSG는 AKS 서비스에 의해 제어됩니다. NSG에 대한 변경 내용은 언제든지 컨트롤 플레인에 의해 덮어 쓰여집니다.
>

먼저 규칙을 추가할 NSG의 리소스 그룹과 NSG 이름을 가져옵니다.

```azurecli-interactive
CLUSTER_RG=$(az aks show -g myResourceGroup -n myAKSCluster --query nodeResourceGroup -o tsv)
NSG_NAME=$(az network nsg list -g $CLUSTER_RG --query [].name -o tsv)
```

그런 다음, NSG 규칙을 만듭니다.

```azurecli-interactive
az network nsg rule create --name tempRDPAccess --resource-group $CLUSTER_RG --nsg-name $NSG_NAME --priority 100 --destination-port-range 3389 --protocol Tcp --description "Temporary RDP access to Windows nodes"
```

## <a name="get-the-node-address"></a>노드 주소 가져오기

Kubernetes 클러스터를 관리하려면 [kubectl][kubectl] Kubernetes 명령줄 클라이언트를 사용합니다. Azure Cloud Shell을 사용하는 경우 `kubectl`이 이미 설치되어 있습니다. `kubectl`을 로컬로 설치하려면 [az aks install-cli][az-aks-install-cli] 명령을 사용합니다.
    
```azurecli-interactive
az aks install-cli
```

Kubernetes 클러스터에 연결하도록 `kubectl`을 구성하려면 [az aks get-credentials][az-aks-get-credentials] 명령을 사용합니다. 이 명령은 자격 증명을 다운로드하고 Kubernetes CLI가 해당 자격 증명을 사용하도록 구성합니다.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

[kubectl get][kubectl-get] 명령을 사용하여 Windows Server 노드의 내부 IP 주소를 나열합니다.

```console
kubectl get nodes -o wide
```

다음 예제 출력에서는 Windows Server 노드를 비롯한 클러스터에 있는 모든 노드의 내부 IP 주소를 보여 줍니다.

```console
$ kubectl get nodes -o wide
NAME                                STATUS   ROLES   AGE   VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE                    KERNEL-VERSION      CONTAINER-RUNTIME
aks-nodepool1-42485177-vmss000000   Ready    agent   18h   v1.12.7   10.240.0.4    <none>        Ubuntu 16.04.6 LTS          4.15.0-1040-azure   docker://3.0.4
aksnpwin000000                      Ready    agent   13h   v1.12.7   10.240.0.67   <none>        Windows Server Datacenter   10.0.17763.437
```

문제를 해결하려는 Windows Server 노드의 내부 IP 주소를 기록해 둡니다. 이후 단계에서 해당 주소가 필요합니다.

## <a name="connect-to-the-virtual-machine-and-node"></a>가상 머신 및 노드에 연결

[Microsoft 원격 데스크톱][rdp-mac]과 같은 RDP 클라이언트를 사용하여 이전에 만든 가상 머신의 공용 IP 주소에 연결합니다.

![RDP 클라이언트를 사용하여 가상 머신에 연결하는 중인 이미지](media/rdp/vm-rdp.png)

가상 머신에 연결한 후 가상 머신 내에서 RDP 클라이언트를 사용하여 문제를 해결하려는 Windows Server 노드의 ‘내부 IP 주소’에 연결합니다.

![RDP 클라이언트를 사용하여 Windows Server 노드에 연결하는 중인 이미지](media/rdp/node-rdp.png)

이제 Windows Server 노드에 연결되었습니다.

![Windows Server 노드의 cmd 창 이미지](media/rdp/node-session.png)

이제 *cmd* 창에서 문제 해결 명령을 실행할 수 있습니다. Windows Server 노드는 Windows Server Core를 사용하기 때문에 RDP를 통해 Windows Server 노드에 연결할 때 전체 GUI나 다른 GUI 도구가 없습니다.

## <a name="remove-rdp-access"></a>RDP 액세스 제거

완료되면 Windows Server 노드에 대한 RDP 연결을 끝낸 후 가상 머신에 대한 RDP 세션을 종료합니다. 두 RDP 세션을 모두 끝낸 후 [az vm delete][az-vm-delete] 명령을 사용하여 가상 머신을 삭제합니다.

```azurecli-interactive
az vm delete --resource-group myResourceGroup --name myVM
```

그리고 NSG 규칙도 삭제합니다.

```azurecli-interactive
CLUSTER_RG=$(az aks show -g myResourceGroup -n myAKSCluster --query nodeResourceGroup -o tsv)
NSG_NAME=$(az network nsg list -g $CLUSTER_RG --query [].name -o tsv)
```

```azurecli-interactive
az network nsg rule delete --resource-group $CLUSTER_RG --nsg-name $NSG_NAME --name tempRDPAccess
```

## <a name="next-steps"></a>다음 단계

추가 문제 해결 데이터가 필요하면 [Kubernetes 마스터 노드 로그][view-master-logs]나 [Azure Monitor][azure-monitor-containers]를 확인하면 됩니다.

<!-- EXTERNAL LINKS -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[rdp-mac]: https://aka.ms/rdmac

<!-- INTERNAL LINKS -->
[aks-windows-cli]: windows-container-cli.md
[az-aks-install-cli]: /cli/azure/aks#az_aks_install_cli
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[az-vm-delete]: /cli/azure/vm#az_vm_delete
[azure-monitor-containers]: ../azure-monitor/containers/container-insights-overview.md
[install-azure-cli]: /cli/azure/install-azure-cli
[ssh-steps]: ssh.md
[view-master-logs]: view-master-logs.md