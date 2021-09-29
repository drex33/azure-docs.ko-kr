---
title: PowerShell을 사용하여 AKS 클러스터에 Windows Server 컨테이너 만들기
description: PowerShell을 사용하여 Kubernetes 클러스터를 빠르게 만들고 AKS(Azure Kubernetes Service)의 Windows Server 컨테이너에 응용 프로그램을 배포하는 방법을 알아봅니다.
services: container-service
ms.topic: article
ms.date: 03/12/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: bd419902055758a089772a2ee99c40c4e66ff750
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129235215"
---
# <a name="create-a-windows-server-container-on-an-azure-kubernetes-service-aks-cluster-using-powershell"></a>PowerShell을 사용하여 AKS(Azure Kubernetes Service) 클러스터에 Windows Server 컨테이너 만들기

AKS(Azure Kubernetes Service)는 클러스터를 빠르게 배포하고 관리할 수 있는 관리형 Kubernetes 서비스입니다. 이 문서에서는 PowerShell을 사용하여 AKS 클러스터를 배포합니다. 또한 Windows Server 컨테이너의 `ASP.NET` 샘플 응용 프로그램을 클러스터에 배포합니다.

![ASP.NET 샘플 응용 프로그램을 탐색하는 이미지](media/windows-container-powershell/asp-net-sample-app.png)

이 문서에서는 Kubernetes 개념에 대한 기본 지식이 있다고 가정합니다. 자세한 내용은 [AKS(Azure Kubernetes Service)의 Kubernetes 핵심 개념][kubernetes-concepts]을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

PowerShell을 로컬로 사용하도록 선택하는 경우 이 문서에서는 Az PowerShell 모듈을 설치하고 [Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount) cmdlet을 사용하여 Azure 계정에 연결해야 합니다. Az PowerShell 모듈을 설치하는 방법에 대한 자세한 내용은 [Azure PowerShell 설치][install-azure-powershell]를 참조하세요. 또한 Az.Aks PowerShell 모듈을 설치해야 합니다.

```azurepowershell-interactive
Install-Module Az.Aks
```

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

여러 Azure 구독이 있는 경우 리소스에 대한 요금이 청구되는 적절한 구독을 선택합니다. [Set-AzContext](/powershell/module/az.accounts/set-azcontext) cmdlet을 사용하여 특정 구독 ID를 선택합니다.

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="limitations"></a>제한 사항

여러 노드 풀을 지원하는 AKS 클러스터를 만들고 관리하는 경우 다음과 같은 제한 사항이 적용됩니다.

* 첫 번째 노드 풀은 삭제할 수 없습니다.

Windows Server 노드 풀에는 다음과 같은 추가 제한 사항이 적용됩니다.

* AKS 클러스터에는 최대 10개의 노드 풀을 포함할 수 있습니다.
* AKS 클러스터에는 각 노드 풀에 최대 100개의 노드를 포함할 수 있습니다.
* Windows Server 노드 풀 이름은 6자로 제한됩니다.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

[Azure 리소스 그룹](../azure-resource-manager/management/overview.md)은 Azure 리소스가 배포되고 관리되는 논리 그룹입니다. 리소스 그룹을 만들 때 위치를 지정하라는 메시지가 나타납니다. 이 위치는 리소스 그룹 메타데이터가 저장되는 위치이며 리소스를 만드는 동안 다른 지역을 지정하지 않으면 리소스가 Azure에서 실행되는 위치입니다. [New-AzResourceGroup][new-azresourcegroup] cmdlet을 사용하여 리소스 그룹을 만듭니다.

다음 예제에서는 **eastus** 위치에 **myResourceGroup** 이라는 리소스 그룹을 만듭니다.

> [!NOTE]
> 이 문서에서는 이 자습서의 명령에 대해 PowerShell 구문을 사용합니다. Azure Cloud Shell을 사용하는 경우 Cloud Shell 창의 왼쪽 상단에 있는 드롭다운 메뉴가 **PowerShell** 로 설정되어 있어야 합니다.

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location eastus
```

다음 예제 출력에서는 성공적으로 만들어진 리소스 그룹이 나와 있습니다.

```plaintext
ResourceGroupName : myResourceGroup
Location          : eastus
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup
```

## <a name="create-an-aks-cluster"></a>AKS 클러스터 만들기

`ssh-keygen` 명령줄 유틸리티를 사용하여 SSH 키 쌍을 생성합니다. 자세한 내용은 [빠른 단계: Azure에서 Linux VM용 SSH 퍼블릭-프라이빗 키 쌍 만들기 및 사용](../virtual-machines/linux/mac-create-ssh-keys.md)을 참조하세요.

Windows Server 컨테이너의 노드 풀을 지원하는 AKS 클러스터를 실행하려면 클러스터에서 [Azure CNI][azure-cni-about](고급) 네트워크 플러그인을 사용하는 네트워크 정책을 사용해야 합니다. 필요한 서브넷 범위 및 네트워크 고려 사항을 계획하는 데 도움이 되는 자세한 내용은 [Azure CNI 네트워킹 구성][use-advanced-networking]을 참조하세요. 아래의 [New-AzAksCluster][new-azakscluster] cmdlet을 사용하여 **myAKSCluster** 라는 AKS 클러스터를 만듭니다. 다음 예제에서는 필요한 네트워크 리소스(존재하지 않는 경우)를 만듭니다.

> [!NOTE]
> 클러스터를 안정적으로 작동하도록 하려면 기본 노드 풀에서 2개 이상의 노드를 실행해야 합니다.

```azurepowershell-interactive
$Username = Read-Host -Prompt 'Please create a username for the administrator credentials on your Windows Server containers: '
$Password = Read-Host -Prompt 'Please create a password for the administrator credentials on your Windows Server containers: ' -AsSecureString
New-AzAksCluster -ResourceGroupName myResourceGroup -Name myAKSCluster -NodeCount 2 -NetworkPlugin azure -NodeVmSetType VirtualMachineScaleSets -WindowsProfileAdminUserName $Username -WindowsProfileAdminUserPassword $Password
```

> [!Note]
> 이 지역에 버전이 지원되지 않아 AKS 클러스터를 만들 수 없는 경우에는 `Get-AzAksVersion -Location eastus` 명령을 사용하여 이 지역에 지원되는 버전 목록을 찾을 수 있습니다.

몇 분 후 명령이 완료되면 클러스터에 대한 정보가 반환됩니다. 경우에 따라 클러스터를 프로비저닝하는 데 몇 분 이상 걸릴 수 있습니다. 이 경우 최대 10분이 허용됩니다.

## <a name="add-a-windows-server-node-pool"></a>Windows Server 노드 풀 추가

기본적으로 AKS 클러스터는 Linux 컨테이너를 실행할 수 있는 노드 풀로 생성됩니다. `New-AzAksNodePool` cmdlet을 사용하여 Linux 노드 풀과 함께 Windows Server 컨테이너를 실행할 수 있는 노드 풀을 추가합니다.

```azurepowershell-interactive
New-AzAksNodePool -ResourceGroupName myResourceGroup -ClusterName myAKSCluster -VmSetType VirtualMachineScaleSets -OsType Windows -Name npwin
```

위의 명령은 **npwin** 이라는 새 노드 풀을 만들어 **myAKSCluster** 에 추가합니다. Windows Server 컨테이너를 실행하기 위해 노드 풀을 만들 때 **VmSize** 의 기본값은 **Standard_D2s_v3** 입니다. **VmSize** 매개 변수를 설정하도록 선택하는 경우 [제한된 VM 크기][restricted-vm-sizes] 목록을 확인합니다. 권장되는 최소 크기는 **Standard_D2s_v3** 입니다. 또한 이전 명령은 `New-AzAksCluster`를 실행할 때 생성되는 기본 vnet의 기본 서브넷을 사용합니다.

## <a name="connect-to-the-cluster"></a>클러스터에 연결

Kubernetes 클러스터를 관리하려면 [kubectl][kubectl] Kubernetes 명령줄 클라이언트를 사용합니다. Azure Cloud Shell을 사용하는 경우 `kubectl`이 이미 설치되어 있습니다. `kubectl`을 로컬로 설치하려면 `Install-AzAksKubectl` cmdlet을 사용합니다.

```azurepowershell-interactive
Install-AzAksKubectl
```

Kubernetes 클러스터에 연결하도록 `kubectl`을 구성하려면 [Import-AzAksCredential][import-azakscredential] cmdlet을 사용합니다. 이 명령은 자격 증명을 다운로드하고 Kubernetes CLI가 해당 자격 증명을 사용하도록 구성합니다.

```azurepowershell-interactive
Import-AzAksCredential -ResourceGroupName myResourceGroup -Name myAKSCluster
```

클러스터에 대한 연결을 확인하려면 [kubectl get][kubectl-get] 명령을 사용하여 클러스터 노드 목록을 반환합니다.

```azurepowershell-interactive
kubectl get nodes
```

다음 예제 출력은 클러스터의 모든 노드를 보여 줍니다. 모든 노드는 **준비** 상태여야 합니다.

```plaintext
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmssfedcba   Ready    agent   13m    v1.16.7
aksnpwin987654                      Ready    agent   108s   v1.16.7
```

## <a name="run-the-application"></a>애플리케이션 실행

Kubernetes 매니페스트 파일은 어떤 컨테이너 이미지가 실행되는지 등과 같은 클러스터에 대해 원하는 상태를 정의합니다. 이 문서에서는 매니페스트를 사용하여 Windows Server 컨테이너에서 ASP.NET 샘플 응용 프로그램을 실행하는 데 필요한 모든 개체를 만듭니다. 이 매니페스트에는 ASP.NET 샘플 응용 프로그램에 대한 [Kubernetes 배포][kubernetes-deployment] 및 인터넷에서 응용 프로그램에 액세스하는 외부 [Kubernetes 서비스][kubernetes-service]가 포함되어 있습니다.

ASP.NET 샘플 응용 프로그램은 [.NET Framework 샘플][dotnet-samples]의 일부로 제공되며 Windows Server 컨테이너에서 실행됩니다. AKS를 사용하려면 Windows Server 컨테이너가 **Windows Server 2019** 이상의 이미지를 기반으로 해야 합니다. 또한 Kubernetes 매니페스트 파일은 Windows Server 컨테이너를 실행할 수 있는 노드에서 ASP.NET 샘플 응용 프로그램의 Pod를 실행하도록 AKS 클러스터에 지시하는 [노드 선택기][node-selector]를 정의해야 합니다.

`sample.yaml`이라는 파일을 만들고 다음 YAML 정의에 복사합니다. Azure Cloud Shell을 사용하는 경우 이 파일은 가상 또는 실제 시스템에서 작업하고 있는 것처럼 `vi` 또는 `nano`를 사용하여 만들 수 있습니다.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: sample
  labels:
    app: sample
spec:
  replicas: 1
  template:
    metadata:
      name: sample
      labels:
        app: sample
    spec:
      nodeSelector:
        "kubernetes.io/os": windows
      containers:
      - name: sample
        image: mcr.microsoft.com/dotnet/framework/samples:aspnetapp
        resources:
          limits:
            cpu: 1
            memory: 800M
          requests:
            cpu: .1
            memory: 300M
        ports:
          - containerPort: 80
  selector:
    matchLabels:
      app: sample
---
apiVersion: v1
kind: Service
metadata:
  name: sample
spec:
  type: LoadBalancer
  ports:
  - protocol: TCP
    port: 80
  selector:
    app: sample
```

[kubectl apply][kubectl-apply] 명령을 사용하여 애플리케이션을 배포하고 YAML 매니페스트의 이름을 지정합니다.

```azurepowershell-interactive
kubectl apply -f sample.yaml
```

다음 예제 출력에는 성공적으로 생성된 배포 및 서비스가 나와 있습니다.

```plaintext
deployment.apps/sample created
service/sample created
```

## <a name="test-the-application"></a>애플리케이션 테스트

애플리케이션이 실행되면 애플리케이션 프런트 엔드를 인터넷에 공개하는 Kubernetes 서비스가 만들어집니다.
이 프로세스를 완료하는 데 몇 분이 걸릴 수 있습니다. 경우에 따라 서비스를 프로비저닝하는 데 몇 분 이상 걸릴 수 있습니다. 이 경우 최대 10분이 허용됩니다.

진행 상태를 모니터링하려면 `--watch` 인수와 함께 [kubectl get service][kubectl-get] 명령을 사용합니다.

```azurepowershell-interactive
kubectl get service sample --watch
```

처음에는 **샘플** 서비스에 대한 **EXTERNAL-IP** 가 **보류 중** 으로 표시됩니다.

```plaintext
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
sample             LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

**EXTERNAL-IP** 주소가 **보류 중** 에서 실제 공용 IP 주소로 변경되면 `CTRL-C`를 사용하여 `kubectl` 조사식 프로세스를 중지합니다. 다음 예제 출력은 서비스에 할당된 유효한 공용 IP 주소를 보여줍니다.

```plaintext
sample  LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

샘플 앱이 실제로 작동하는 모습을 보려면 웹 브라우저를 서비스의 외부 IP 주소로 엽니다.

![ASP.NET 샘플 응용 프로그램을 탐색하는 이미지](media/windows-container-powershell/asp-net-sample-app.png)

> [!Note]
> 페이지를 로드하려고 할 때 연결 시간 초과가 수신되면 다음 `kubectl get pods --watch` 명령을 사용하여 샘플 앱이 준비되었는지 확인해야 합니다. 경우에 따라 외부 IP 주소를 사용할 수 있을 때까지 Windows 컨테이너가 시작되지 않습니다.

## <a name="delete-cluster"></a>클러스터 삭제

클러스터가 더 이상 필요하지 않은 경우 [Remove-AzResourceGroup][remove-azresourcegroup] cmdlet을 사용하여 리소스 그룹, 컨테이너 서비스 및 모든 관련 리소스를 제거합니다.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

> [!NOTE]
> 클러스터를 삭제할 때, AKS 클러스터에 사용되는 Azure Active Directory 서비스 주체는 제거되지 않습니다. 서비스 주체를 제거하는 방법에 대한 단계는 [AKS 서비스 주체 고려 사항 및 삭제][sp-delete]를 참조하세요. 관리 ID를 사용하는 경우 ID는 플랫폼에 의해 관리되며 제거할 필요가 없습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Kubernetes 클러스터를 배포하고 Windows Server 컨테이너에 `ASP.NET` 샘플 응용 프로그램을 배포했습니다. 직접 만든 클러스터에 대한 [Kubernetes 웹 대시보드에 액세스][kubernetes-dashboard]합니다.

AKS에 대해 자세히 알아보고 배포 예제에 대한 전체 코드를 연습해 보려면 Kubernetes 클러스터 자습서를 계속 진행합니다.

> [!div class="nextstepaction"]
> [AKS 자습서][aks-tutorial]

<!-- LINKS - external -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[dotnet-samples]: https://hub.docker.com/_/microsoft-dotnet-framework-samples/
[node-selector]: https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply

<!-- LINKS - internal -->
[kubernetes-concepts]: concepts-clusters-workloads.md
[install-azure-powershell]: /powershell/azure/install-az-ps
[new-azresourcegroup]: /powershell/module/az.resources/new-azresourcegroup
[azure-cni-about]: concepts-network.md#azure-cni-advanced-networking
[use-advanced-networking]: configure-azure-cni.md
[new-azakscluster]: /powershell/module/az.aks/new-azakscluster
[restricted-vm-sizes]: quotas-skus-regions.md#restricted-vm-sizes
[import-azakscredential]: /powershell/module/az.aks/import-azakscredential
[kubernetes-deployment]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[kubernetes-service]: concepts-network.md#services
[remove-azresourcegroup]: /powershell/module/az.resources/remove-azresourcegroup
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[kubernetes-dashboard]: kubernetes-dashboard.md
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
