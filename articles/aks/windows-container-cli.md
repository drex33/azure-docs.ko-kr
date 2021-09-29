---
title: Azure CLI를 사용하여 AKS 클러스터에 Windows Server 컨테이너 만들기
description: Azure CLI를 사용하여 Kubernetes 클러스터를 빠르게 만들고 AKS(Azure Kubernetes Service)의 Windows Server 컨테이너에 애플리케이션을 배포하는 방법을 알아봅니다.
services: container-service
ms.topic: article
ms.date: 08/06/2021
ms.openlocfilehash: 284114ef1f9c8759eaec061b32282154fe72859c
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129235310"
---
# <a name="create-a-windows-server-container-on-an-azure-kubernetes-service-aks-cluster-using-the-azure-cli"></a>Azure CLI를 사용하여 AKS(Azure Kubernetes Service) 클러스터에 Windows Server 컨테이너 만들기

AKS(Azure Kubernetes Service)는 클러스터를 빠르게 배포하고 관리할 수 있는 관리형 Kubernetes 서비스입니다. 이 문서에서는 Azure CLI를 사용하여 AKS 클러스터를 배포합니다. 또한 Windows Server 컨테이너의 ASP.NET 샘플 애플리케이션을 클러스터에 배포합니다.

![ASP.NET 샘플 응용 프로그램을 탐색하는 이미지](media/windows-container/asp-net-sample-app.png)

이 문서에서는 Kubernetes 개념에 대한 기본 지식이 있다고 가정합니다. 자세한 내용은 [AKS(Azure Kubernetes Service)의 Kubernetes 핵심 개념][kubernetes-concepts]을 참조하세요.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

### <a name="limitations"></a>제한 사항

여러 노드 풀을 지원하는 AKS 클러스터를 만들고 관리하는 경우 다음과 같은 제한 사항이 적용됩니다.

* 첫 번째 노드 풀은 삭제할 수 없습니다.

Windows Server 노드 풀에는 다음과 같은 추가 제한 사항이 적용됩니다.

* AKS 클러스터에는 최대 10개의 노드 풀을 포함할 수 있습니다.
* AKS 클러스터에는 각 노드 풀에 최대 100개의 노드를 포함할 수 있습니다.
* Windows Server 노드 풀 이름은 6자로 제한됩니다.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

Azure 리소스 그룹은 Azure 리소스가 배포되고 관리되는 논리 그룹입니다. 리소스 그룹을 만들 때 위치를 지정하라는 메시지가 나타납니다. 이 위치는 리소스 그룹 메타데이터가 저장되는 위치이며 리소스를 만드는 동안 다른 지역을 지정하지 않으면 리소스가 Azure에서 실행되는 위치입니다. [az group create][az-group-create] 명령을 사용하여 리소스 그룹을 만듭니다.

다음 예제에서는 *eastus* 위치에 *myResourceGroup* 이라는 리소스 그룹을 만듭니다.

> [!NOTE]
> 이 문서에서는 이 자습서의 명령에 대해 Bash 구문을 사용합니다.
> Azure Cloud Shell을 사용하는 경우 Cloud Shell 창의 왼쪽 상단에 있는 드롭다운 메뉴가 **Bash** 로 설정되어 있어야 합니다.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

다음 예제 출력에서는 성공적으로 만들어진 리소스 그룹이 나와 있습니다.

```json
{
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup",
  "location": "eastus",
  "managedBy": null,
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null,
  "type": null
}
```

## <a name="create-an-aks-cluster"></a>AKS 클러스터 만들기

Windows Server 컨테이너의 노드 풀을 지원하는 AKS 클러스터를 실행하려면 클러스터에서 [Azure CNI][azure-cni-about](고급) 네트워크 플러그인을 사용하는 네트워크 정책을 사용해야 합니다. 필요한 서브넷 범위 및 네트워크 고려 사항을 계획하는 데 도움이 되는 자세한 내용은 [Azure CNI 네트워킹 구성][use-advanced-networking]을 참조하세요. [az aks create][az-aks-create] 명령을 사용하여 *myAKSCluster* 라는 AKS 클러스터를 만듭니다. 이 명령은 필요한 네트워크 리소스를 만듭니다(없는 경우).

* 클러스터는 두 개의 노드로 구성됩니다.
* `--windows-admin-password` 및 `--windows-admin-username` 매개 변수는 클러스터의 모든 Windows Server 노드에 대한 관리자 자격 증명을 설정하며 [Windows Server 암호 요구 사항][windows-server-password]을 충족해야 합니다. *windows-admin-password* 매개 변수를 지정하지 않으면 값을 제공하라는 메시지가 표시됩니다.
* 노드 풀은 `VirtualMachineScaleSets`를 사용합니다.

> [!NOTE]
> 클러스터를 안정적으로 작동하도록 하려면 기본 노드 풀에서 2개 이상의 노드를 실행해야 합니다.

클러스터에서 Windows Server 노드의 관리자 자격 증명으로 사용할 사용자 이름을 만듭니다. 다음 명령은 사용자 이름을 묻는 프롬프트를 표시하고 나중에 사용할 수 있도록 사용자 이름을 WINDOWS_USERNAME으로 설정합니다(이 문서의 명령은 BASH 셸에 입력됨).

```azurecli-interactive
echo "Please enter the username to use as administrator credentials for Windows Server nodes on your cluster: " && read WINDOWS_USERNAME
```

`--windows-admin-username` 매개 변수를 지정하여 클러스터를 만듭니다. 다음 예제 명령은 이전 명령에서 설정한 *WINDOWS_USERNAME* 의 값을 사용하여 클러스터를 만듭니다. 또는 *WINDOWS_USERNAME* 을 사용하는 대신 매개 변수에 다른 사용자 이름을 직접 제공할 수 있습니다. 다음 명령도 클러스터에서 Windows Server 노드의 관리자 자격 증명에 대한 암호를 만들라는 프롬프트를 표시합니다. 또는 *windows-admin-password* 매개 변수를 사용하여 해당 값을 직접 지정할 수 있습니다.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 2 \
    --enable-addons monitoring \
    --generate-ssh-keys \
    --windows-admin-username $WINDOWS_USERNAME \
    --vm-set-type VirtualMachineScaleSets \
    --kubernetes-version 1.20.7 \
    --network-plugin azure
```

> [!NOTE]
> 암호 유효성 검사 오류가 발생하는 경우 설정된 암호가 [Windows Server 암호 요구 사항][windows-server-password]을 충족하는지 확인합니다. 암호가 요구 사항을 충족하는 경우 다른 지역에 리소스 그룹을 만들어 봅니다. 그런 다음 새 리소스 그룹을 사용하여 클러스터를 만들어 봅니다.
>
> `--vm-set-type VirtualMachineScaleSets` 및 `--network-plugin azure`를 설정할 때 관리자 사용자 이름 및 암호를 지정하지 않으면 사용자 이름은 *azureuser* 로 설정되고 암호는 임의 값으로 설정됩니다.
> 
> 관리자 사용자 이름은 변경할 수 없지만 `az aks update`를 사용하여 AKS 클러스터에서 Windows Server 노드에 사용하는 관리자 암호를 변경할 수 있습니다. 자세한 내용은 [Windows Server 노드 풀 FAQ][win-faq-change-admin-creds]를 참조하세요.

몇 분 후 명령이 완료되면 클러스터에 대한 JSON 형식 정보가 반환됩니다. 경우에 따라 클러스터를 프로비저닝하는 데 몇 분 이상 걸릴 수 있습니다. 이 경우 최대 10분이 허용됩니다.

## <a name="add-a-windows-server-node-pool"></a>Windows Server 노드 풀 추가

기본적으로 AKS 클러스터는 Linux 컨테이너를 실행할 수 있는 노드 풀로 생성됩니다. `az aks nodepool add` 명령을 사용하여 Linux 노드 풀과 함께 Windows Server 컨테이너를 실행할 수 있는 노드 풀을 추가합니다.

```azurecli
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --os-type Windows \
    --name npwin \
    --node-count 1
```

위의 명령은 *npwin* 이라는 새 노드 풀을 만들어 *myAKSCluster* 에 추가합니다. 또한 위 명령은 `az aks create`를 실행할 때 생성되는 기본 vnet의 기본 서브넷을 사용합니다.

## <a name="optional-using-containerd-with-windows-server-node-pools-preview"></a>선택 사항: Windows Server 노드 풀과 함께 `containerd` 사용(미리 보기)

Kubernetes 버전 1.20 이상부터 `containerd`를 Windows Server 2019 노드 풀에 대한 컨테이너 런타임으로 지정할 수 있습니다.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

*aks-preview* Azure CLI 확장 버전 0.5.24 이상이 필요합니다. [Az extension add][az-extension-add] 명령을 사용하여 *aks-preview* Azure CLI 확장을 설치 합니다. 또는 [az extension update][az-extension-update] 명령을 사용하여 사용 가능한 업데이트를 설치 합니다.

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

> [!IMPORTANT]
> Windows Server 2019 노드 풀에서 `containerd`를 사용하는 경우:
> - 컨트롤 플레인과 Windows Server 2019 노드 풀 모두 Kubernetes 버전 1.20 이상을 사용해야 합니다.
> - Windows Server 컨테이너를 실행하는 노드 풀을 만들거나 업데이트할 때 *node-vm-size* 의 기본값은 *Standard_D2s_v3* 이며, 이는 Kubernetes 1.20 이전의 Windows Server 2019 노드 풀에 대한 최소 권장 크기입니다. `containerd`를 사용하는 Windows Server 2019 노드 풀에 대한 최소 권장 크기는 *Standard_D4s_v3* 입니다. *node-vm-size* 매개 변수를 설정하는 경우 [제한된 VM 크기][restricted-vm-sizes] 목록을 확인합니다.
> - 워크로드가 올바르게 예약되도록 `containerd`를 실행하는 Windows Server 2019 노드 풀에 [테인트 또는 레이블][aks-taints]을 사용하고 배포에 톨러레이션 또는 노드 선택기를 사용하는 것이 좋습니다.

다음 예제와 같이 [az feature register][az-feature-register] 명령을 사용하여 `UseCustomizedWindowsContainerRuntime` 기능 플래그를 등록합니다.

```azurecli
az feature register --namespace "Microsoft.ContainerService" --name "UseCustomizedWindowsContainerRuntime"
```

[az feature list][az-feature-list] 명령을 사용하여 등록 상태를 확인할 수 있습니다.

```azurecli
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/UseCustomizedWindowsContainerRuntime')].{Name:name,State:properties.state}"
```

준비가 되면 [az provider register][az-provider-register] 명령을 사용하여 Microsoft.ContainerService 리소스 공급자 등록을 새로 고칩니다.

```azurecli
az provider register --namespace Microsoft.ContainerService
```

### <a name="add-a-windows-server-node-pool-with-containerd-preview"></a>`containerd`가 포함된 Windows Server 노드 풀 추가(미리 보기)

`az aks nodepool add` 명령을 사용하여 `containerd` 런타임에서 Windows Server 컨테이너를 실행할 수 있는 노드 풀을 추가합니다.

> [!NOTE]
> *WindowsContainerRuntime=containerd* 사용자 지정 헤더를 지정하지 않은 경우 노드 풀은 컨테이너 런타임으로 Docker를 사용합니다.

```azurecli
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --os-type Windows \
    --name npwcd \
    --node-vm-size Standard_D4s_v3 \
    --kubernetes-version 1.20.5 \
    --aks-custom-headers WindowsContainerRuntime=containerd \
    --node-count 1
```

위 명령은 `containerd`를 *npwcd* 라는 런타임으로 사용하는 새로운 Windows Server 노드 풀을 만들고 이를 *myAKSCluster* 에 추가합니다. 또한 위 명령은 `az aks create`를 실행할 때 생성되는 기본 vnet의 기본 서브넷을 사용합니다.

### <a name="upgrade-an-existing-windows-server-node-pool-to-containerd-preview"></a>기존 Windows Server 노드 풀을 `containerd`로 업그레이드(미리 보기)

`az aks nodepool upgrade` 명령을 사용하여 특정 노드 풀을 Docker에서 `containerd`로 업그레이드합니다.

```azurecli
az aks nodepool upgrade \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name npwd \
    --kubernetes-version 1.20.7 \
    --aks-custom-headers WindowsContainerRuntime=containerd
```

위의 명령은 *npwd* 라는 노드 풀을 `containerd` 런타임으로 업그레이드합니다.

모든 Windows Server 노드 풀에 대해 `containerd` 런타임을 사용하도록 클러스터의 모든 기존 노드 풀을 업그레이드하려면 다음 안내를 따르세요.

```azurecli
az aks upgrade \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --kubernetes-version 1.20.7 \
    --aks-custom-headers WindowsContainerRuntime=containerd
```

위 명령은 `containerd` 런타임을 사용하도록 *myAKSCluster* 의 모든 Windows Server 노드 풀을 업그레이드합니다.

> [!NOTE]
> `containerd` 런타임을 사용하도록 모든 기존 Windows Server 노드 풀을 업그레이드한 후에도 새 Windows Server 노드 풀을 추가할 때 Docker는 여전히 기본 런타임이 됩니다. 

## <a name="connect-to-the-cluster"></a>클러스터에 연결

Kubernetes 클러스터를 관리하려면 [kubectl][kubectl] Kubernetes 명령줄 클라이언트를 사용합니다. Azure Cloud Shell을 사용하는 경우 `kubectl`이 이미 설치되어 있습니다. `kubectl`을 로컬로 설치하려면 [az aks install-cli][az-aks-install-cli] 명령을 사용합니다.

```azurecli
az aks install-cli
```

Kubernetes 클러스터에 연결하도록 `kubectl`을 구성하려면 [az aks get-credentials][az-aks-get-credentials] 명령을 사용합니다. 이 명령은 자격 증명을 다운로드하고 Kubernetes CLI가 해당 자격 증명을 사용하도록 구성합니다.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

클러스터에 대한 연결을 확인하려면 [kubectl get][kubectl-get] 명령을 사용하여 클러스터 노드 목록을 반환합니다.

```console
kubectl get nodes -o wide
```

다음 예제 출력은 클러스터의 모든 노드를 보여 줍니다. 모든 노드는 *준비* 상태여야 합니다.

```output
NAME                                STATUS   ROLES   AGE    VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE                         KERNEL-VERSION     CONTAINER-RUNTIME
aks-nodepool1-12345678-vmss000000   Ready    agent   34m    v1.20.7   10.240.0.4    <none>        Ubuntu 18.04.5 LTS               5.4.0-1046-azure   containerd://1.4.4+azure
aks-nodepool1-12345678-vmss000001   Ready    agent   34m    v1.20.7   10.240.0.35   <none>        Ubuntu 18.04.5 LTS               5.4.0-1046-azure   containerd://1.4.4+azure
aksnpwcd123456                      Ready    agent   9m6s   v1.20.7   10.240.0.97   <none>        Windows Server 2019 Datacenter   10.0.17763.1879    containerd://1.4.4+unknown
aksnpwin987654                      Ready    agent   25m    v1.20.7   10.240.0.66   <none>        Windows Server 2019 Datacenter   10.0.17763.1879    docker://19.3.14
```

> [!NOTE]
> 각 노드 풀에 대한 컨테이너 런타임은 *CONTAINER-RUNTIME* 아래에 표시됩니다. *aksnpwin987654* 는 `docker://`로 시작되며, 이는 컨테이너 런타임에 대해 Docker를 사용함을 의미합니다. *aksnpwcd123456* 은 `containerd://`로 시작되며, 이는 컨테이너 런타임에 대해 `containerd`를 사용함을 의미합니다.

## <a name="run-the-application"></a>애플리케이션 실행

Kubernetes 매니페스트 파일은 어떤 컨테이너 이미지가 실행되는지 등과 같은 클러스터에 대해 원하는 상태를 정의합니다. 이 문서에서는 매니페스트를 사용하여 Windows Server 컨테이너에서 ASP.NET 샘플 응용 프로그램을 실행하는 데 필요한 모든 개체를 만듭니다. 이 매니페스트에는 ASP.NET 샘플 응용 프로그램에 대한 [Kubernetes 배포][kubernetes-deployment] 및 인터넷에서 응용 프로그램에 액세스하는 외부 [Kubernetes 서비스][kubernetes-service]가 포함되어 있습니다.

ASP.NET 샘플 응용 프로그램은 [.NET Framework 샘플][dotnet-samples]의 일부로 제공되며 Windows Server 컨테이너에서 실행됩니다. AKS를 사용하려면 Windows Server 컨테이너가 *Windows Server 2019* 이상의 이미지를 기반으로 해야 합니다. 또한 Kubernetes 매니페스트 파일은 Windows Server 컨테이너를 실행할 수 있는 노드에서 ASP.NET 샘플 응용 프로그램의 Pod를 실행하도록 AKS 클러스터에 지시하는 [노드 선택기][node-selector]를 정의해야 합니다.

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

```console
kubectl apply -f sample.yaml
```

다음 예제 출력에는 성공적으로 생성된 배포 및 서비스가 나와 있습니다.

```output
deployment.apps/sample created
service/sample created
```

## <a name="test-the-application"></a>애플리케이션 테스트

애플리케이션이 실행되면 애플리케이션 프런트 엔드를 인터넷에 공개하는 Kubernetes 서비스가 만들어집니다. 이 프로세스를 완료하는 데 몇 분이 걸릴 수 있습니다. 경우에 따라 서비스를 프로비저닝하는 데 몇 분 이상 걸릴 수 있습니다. 이 경우 최대 10분이 허용됩니다.

진행 상태를 모니터링하려면 `--watch` 인수와 함께 [kubectl get service][kubectl-get] 명령을 사용합니다.

```console
kubectl get service sample --watch
```

처음에는 *샘플* 서비스에 대한 *EXTERNAL-IP* 가 *보류 중* 으로 표시됩니다.

```output
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
sample             LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

*EXTERNAL-IP* 주소가 *보류 중* 에서 실제 공용 IP 주소로 변경되면 `CTRL-C`를 사용하여 `kubectl` 조사식 프로세스를 중지합니다. 다음 예제 출력은 서비스에 할당된 유효한 공용 IP 주소를 보여줍니다.

```output
sample  LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

샘플 앱이 실제로 작동하는 모습을 보려면 웹 브라우저를 서비스의 외부 IP 주소로 엽니다.

![ASP.NET 샘플 응용 프로그램을 탐색하는 이미지](media/windows-container/asp-net-sample-app.png)

> [!Note]
> 페이지를 로드하려고 할 때 연결 시간 제한이 수신되면 다음 [kubectl get pods --watch] 명령을 사용하여 샘플 앱이 준비되었는지 확인해야 합니다. 외부 IP 주소를 사용할 수 있는 시간에 의해 Windows 컨테이너가 시작 되지 않을 수도 있습니다.

## <a name="delete-cluster"></a>클러스터 삭제

클러스터가 더 이상 필요하지 않은 경우 [az group delete][az-group-delete] 명령을 사용하여 리소스 그룹, 컨테이너 서비스 및 모든 관련 리소스를 제거합니다.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

> [!NOTE]
> 클러스터를 삭제할 때, AKS 클러스터에 사용되는 Azure Active Directory 서비스 주체는 제거되지 않습니다. 서비스 주체를 제거하는 방법에 대한 단계는 [AKS 서비스 주체 고려 사항 및 삭제][sp-delete]를 참조하세요. 관리 ID를 사용하는 경우 ID는 플랫폼에 의해 관리되며 제거할 필요가 없습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Kubernetes 클러스터를 배포하고 Windows Server 컨테이너에 ASP.NET 샘플 애플리케이션을 배포했습니다. 방금 만든 클러스터에 대한 [Kubernetes 웹 대시보드에 액세스][kubernetes-dashboard]합니다.

AKS에 대해 자세히 알아보고 배포 예제에 대한 전체 코드를 연습해 보려면 Kubernetes 클러스터 자습서를 계속 진행합니다.

> [!div class="nextstepaction"]
> [AKS 자습서][aks-tutorial]

<!-- LINKS - external -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[node-selector]: https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[dotnet-samples]: https://hub.docker.com/_/microsoft-dotnet-framework-samples/
[azure-cni]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md

<!-- LINKS - internal -->
[kubernetes-concepts]: concepts-clusters-workloads.md
[aks-monitor]: ../azure-monitor/containers/container-insights-onboard.md
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[aks-taints]:  use-multiple-node-pools.md#specify-a-taint-label-or-tag-for-a-node-pool
[az-aks-browse]: /cli/azure/aks#az_aks_browse
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[az-aks-install-cli]: /cli/azure/aks#az_aks_install_cli
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-group-create]: /cli/azure/group#az_group_create
[az-group-delete]: /cli/azure/group#az_group_delete
[az-provider-register]: /cli/azure/provider#az_provider_register
[azure-cli-install]: /cli/azure/install-azure-cli
[azure-cni-about]: concepts-network.md#azure-cni-advanced-networking
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[azure-portal]: https://portal.azure.com
[kubernetes-deployment]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[kubernetes-service]: concepts-network.md#services
[kubernetes-dashboard]: kubernetes-dashboard.md
[restricted-vm-sizes]: quotas-skus-regions.md#restricted-vm-sizes
[use-advanced-networking]: configure-azure-cni.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[windows-server-password]: /windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements#reference
[win-faq-change-admin-creds]: windows-faq.md#how-do-i-change-the-administrator-password-for-windows-server-nodes-on-my-cluster
