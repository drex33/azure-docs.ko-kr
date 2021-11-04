---
title: 네트워크 정책을 사용하여 Pod 트래픽 보안
titleSuffix: Azure Kubernetes Service
description: AKS(Azure Kubernetes Service)에서 Kubernetes 네트워크 정책을 사용하여 pod 안팎으로 흐르는 트래픽을 보호하는 방법 알아보기
services: container-service
ms.topic: article
ms.date: 03/16/2021
ms.openlocfilehash: 4ceb9059456a4f5b20a346e1688b82320e5041fe
ms.sourcegitcommit: 96deccc7988fca3218378a92b3ab685a5123fb73
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131577653"
---
# <a name="secure-traffic-between-pods-using-network-policies-in-azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Service)에서 네트워크 정책을 사용하여 pod 간 트래픽 보호

Kubernetes에서 최신 마이크로 서비스 기반 애플리케이션을 실행할 경우 어느 구성 요소들이 서로 통신할 수 있는지 제어하고 싶을 때가 있습니다. Azure Kubernetes Service 클러스터의 pod 간에 트래픽이 흐르는 방법에 최소 권한 원칙을 적용해야 합니다. 예를 들어, 백 엔드 애플리케이션으로의 직접 트래픽을 차단할 수도 있습니다. Kubernetes의 *네트워크 정책* 기능을 사용하여 클러스터의 pod 간 수신 및 송신 트래픽에 대한 규칙을 정의할 수 있습니다.

이 문서에서는 네트워크 정책을 사용하여 Azure Kubernetes Service에서 pod 간 트래픽 흐름을 제어하는 방법을 보여줍니다. 네트워크 정책은 Linux 기반 노드와 Azure Kubernetes Service의 pod에만 사용해야 합니다.

## <a name="before-you-begin"></a>시작하기 전에

Azure CLI 버전 2.0.61 이상이 설치되고 구성되어 있어야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][install-azure-cli]를 참조하세요.

> [!TIP]
> 미리 보기 중에 네트워크 정책 기능을 사용한 경우 [새 클러스터를 만드는](#create-an-aks-cluster-and-enable-network-policy) 것이 좋습니다.
> 
> 미리 보기 중에 네트워크 정책을 사용한 기존 테스트 클러스터를 계속 사용하려면 클러스터를 최신 GA 릴리스에 대한 새 Kubernetes 버전으로 업그레이드한 후 다음 YAML 매니페스트를 배포하여 작동이 중단되는 메트릭 서버와 Kubernetes 대시보드를 수정합니다. 이 수정 작업은 Calico 네트워크 정책 엔진을 사용하는 클러스터에만 필요합니다.
>
> 최상의 보안을 위해 [이 YAML 매니페스트의 내용을 검토][calico-aks-cleanup]하여 Azure Kubernetes Service 클러스터에 무엇이 배포되는지 이해하는 것이 좋습니다.
>
> `kubectl delete -f https://raw.githubusercontent.com/Azure/aks-engine/master/docs/topics/calico-3.3.1-cleanup-after-upgrade.yaml`

## <a name="overview-of-network-policy"></a>네트워크 정책 개요

기본적으로 Azure Kubernetes Service 클러스터의 모든 pod는 트래픽을 제한 없이 송수신할 수 있습니다. 보안을 강화하기 위해 트래픽의 흐름을 제어하는 규칙을 정의할 수 있습니다. 예를 들어, 필요한 프런트 엔드 서비스에만 백 엔드 애플리케이션이 노출되는 경우가 많습니다. 또는 데이터베이스 구성 요소에 연결하는 애플리케이션 계층에서만 데이터베이스 구성 요소에 액세스할 수 있습니다.

네트워크 정책은 Pod 간의 통신에 대한 액세스 정책을 정의하는 Kubernetes 사양입니다. 네트워크 정책으로 트래픽을 송수신하는 정렬된 규칙 집합을 정의하여 하나 이상의 레이블 선택기와 일치하는 포드 컬렉션에 적용합니다.

이러한 네트워크 정책 규칙이 YAML 매니페스트로 정의됩니다. 배포 또는 서비스도 만드는 보다 광범위한 매니페스트의 일부로 네트워크 정책을 포함할 수 있습니다.

### <a name="network-policy-options-in-aks"></a>Azure Kubernetes Service의 네트워크 정책 옵션

Azure는 네트워크 정책을 구현하는 두 가지 방법을 제공합니다. Azure Kubernetes Service 클러스터를 만들 때 네트워크 정책 옵션을 선택합니다. 클러스터를 만든 후에는 정책 옵션을 변경할 수 없습니다.

* *Azure 네트워크 정책* - Azure 고유의 구현
* *Calico 네트워크 정책* - [Tigera][tigera]가 만든 오픈 소스 네트워크 및 네트워크 보안 솔루션

두 구현 모두 Linux *IPTables* 를 사용하여 지정된 정책을 적용합니다. 정책은 허용 및 허용되지 않는 IP 쌍 집합으로 변환됩니다. 이러한 쌍은 IPTable 필터 규칙으로 프로그래밍됩니다.

### <a name="differences-between-azure-and-calico-policies-and-their-capabilities"></a>Azure 및 Calico 정책과 해당 기능의 차이점

| 기능                               | Azure                      | Calico                      |
|------------------------------------------|----------------------------|-----------------------------|
| 지원 플랫폼                      | Linux                      | Linux, Windows Server 2019(미리 보기)  |
| 지원되는 네트워킹 옵션             | Azure CNI                  | Azure CNI(Windows Server 2019 및 Linux) 및 kubenet(Linux)  |
| Kubernetes 사양 준수 | 지원되는 모든 정책 유형 |  지원되는 모든 정책 유형 |
| 추가 기능                      | None                       | 글로벌 네트워크 정책, 글로벌 네트워크 집합 및 호스트 엔드포인트로 구성된 확장 정책 모델. `calicoctl`CLI를 사용하여 이러한 확장 기능을 관리하는 방법에 대한 자세한 내용은 [calicoctl 사용자 참조][calicoctl]를 참조하세요. |
| 지원                                  | Azure 지원 및 엔지니어링 팀에서 지원 | Calico 커뮤니티 지원. 추가 유료 지원에 대한 자세한 내용은 [프로젝트 Calico 지원 옵션][calico-support]을 참조하세요. |
| 로깅                                  | IPTables에서 추가/삭제된 규칙이 모든 호스트의 */var/log/azure-npm.log* 에 기록됨 | 자세한 내용은 [Calico 구성 요소 로그][calico-logs]를 참조하세요. |

## <a name="create-an-aks-cluster-and-enable-network-policy"></a>AKS 클러스터 만들기 및 네트워크 정책 사용

작동 중인 네트워크 정책을 보기 위해 트래픽 흐름을 정의하는 정책을 만든 후 확장해 보겠습니다.

* pod에 대한 모든 트래픽을 거부합니다.
* pod 레이블을 기준으로 트래픽을 허용합니다.
* 네임스페이스를 기준으로 트래픽을 허용합니다.

먼저, 네트워크 정책을 지원하는 Azure Kubernetes Service 클러스터를 만들겠습니다.

> [!IMPORTANT]
>
> 네트워크 정책 기능은 클러스터를 만든 경우에만 사용하도록 설정할 수 있습니다. 기존 AKS 클러스터에서는 네트워크 정책을 사용하도록 설정할 수 없습니다.

Azure 네트워크 정책을 사용하려면 [Azure CNI 플러그 인][azure-cni]을 사용하고 사용자 고유의 가상 네트워크 및 서브넷을 정의해야 합니다. 필요한 서브넷 범위를 계획하는 방법에 대한 자세한 내용은 [고급 네트워킹 구성][use-advanced-networking]을 참조하세요. Calico 네트워크 정책을 이 동일한 Azure CNI 플러그 인이나 Kubenet CNI 플러그 인과 함께 사용할 수 있습니다.

다음 예제 스크립트는 다음과 같은 작업을 수행합니다.

* 가상 네트워크 및 서브넷을 만듭니다.
* AKS 클러스터에서 사용할 Azure Active Directory(Azure AD) 서비스 주체를 만듭니다.
* 가상 네트워크에서 AKS 클러스터 서비스 주체에 대해 *참가자* 권한을 할당합니다.
* 정의된 가상 네트워크에서 AKS 클러스터를 만들고 네트워크 정책을 사용하도록 설정합니다.
    * _Azure 네트워크_ 정책 옵션이 사용됩니다. 대신 Calico를 네트워크 정책 옵션으로 사용하려면 `--network-policy calico` 매개 변수를 사용합니다. 참고: Calico는 `--network-plugin azure` 또는 `--network-plugin kubenet`과 함께 사용할 수 있습니다.

또는 사용 권한에 대해 서비스 주체 대신 관리 ID를 사용할 수 있습니다. 자세한 내용은 [관리 ID 사용](use-managed-identity.md)을 참조하세요.

사용자 고유의 보안 *SP_PASSWORD* 를 제공합니다. *RESOURCE_GROUP_NAME* 및 *CLUSTER_NAME* 변수를 다음과 같이 바꿉니다.

```azurecli-interactive
RESOURCE_GROUP_NAME=myResourceGroup-NP
CLUSTER_NAME=myAKSCluster
LOCATION=canadaeast

# Create a resource group
az group create --name $RESOURCE_GROUP_NAME --location $LOCATION

# Create a virtual network and subnet
az network vnet create \
    --resource-group $RESOURCE_GROUP_NAME \
    --name myVnet \
    --address-prefixes 10.0.0.0/8 \
    --subnet-name myAKSSubnet \
    --subnet-prefix 10.240.0.0/16

# Create a service principal and read in the application ID
SP=$(az ad sp create-for-rbac --output json)
SP_ID=$(echo $SP | jq -r .appId)
SP_PASSWORD=$(echo $SP | jq -r .password)

# Wait 15 seconds to make sure that service principal has propagated
echo "Waiting for service principal to propagate..."
sleep 15

# Get the virtual network resource ID
VNET_ID=$(az network vnet show --resource-group $RESOURCE_GROUP_NAME --name myVnet --query id -o tsv)

# Assign the service principal Contributor permissions to the virtual network resource
az role assignment create --assignee $SP_ID --scope $VNET_ID --role Contributor

# Get the virtual network subnet resource ID
SUBNET_ID=$(az network vnet subnet show --resource-group $RESOURCE_GROUP_NAME --vnet-name myVnet --name myAKSSubnet --query id -o tsv)
```

### <a name="create-an-aks-cluster-for-azure-network-policies"></a>Azure 네트워크 정책에 대한 AKS 클러스터 만들기

AKS 클러스터를 만들고 네트워크 플러그인 및 네트워크 정책에 대한 가상 네트워크, 서비스 주체 정보 및 *azure* 를 지정합니다.

```azurecli
az aks create \
    --resource-group $RESOURCE_GROUP_NAME \
    --name $CLUSTER_NAME \
    --node-count 1 \
    --generate-ssh-keys \
    --service-cidr 10.0.0.0/16 \
    --dns-service-ip 10.0.0.10 \
    --docker-bridge-address 172.17.0.1/16 \
    --vnet-subnet-id $SUBNET_ID \
    --service-principal $SP_ID \
    --client-secret $SP_PASSWORD \
    --network-plugin azure \
    --network-policy azure
```

클러스터를 만드는 데 몇 분이 걸립니다. 클러스터가 준비되면 [az aks get-credentials][az-aks-get-credentials] 명령을 사용하여 Kubernetes 클러스터에 연결하도록 `kubectl`을 구성합니다. 이 명령은 자격 증명을 다운로드하고 해당 자격 증명을 사용하도록 Kubernetes CLI를 구성합니다.

```azurecli-interactive
az aks get-credentials --resource-group $RESOURCE_GROUP_NAME --name $CLUSTER_NAME
```

### <a name="create-an-aks-cluster-for-calico-network-policies"></a>Calico 네트워크 정책에 대한 AKS 클러스터 만들기

AKS 클러스터를 만들고 네트워크 정책에 대한 가상 네트워크, 서비스 주체 정보, 네트워크 플러그인의 *azure* 및 *calico* 를 지정합니다. *Calico* 를 네트워크 정책으로 사용하면 Linux 및 Windows 노드 풀 모두에서 Calico 네트워킹을 사용할 수 있습니다.

Windows 노드 풀을 클러스터에 추가하려는 경우 [Windows Server 암호 요구 사항][windows-server-password]을 충족하는 클러스터에 `windows-admin-username` 및 `windows-admin-password` 매개 변수를 포함합니다. Windows 노드 풀에서 Calico를 사용하려면 `Microsoft.ContainerService/EnableAKSWindowsCalico`를 등록해야 합니다.

다음 예제와 같이 [az feature register][az-feature-register] 명령을 사용하여 `EnableAKSWindowsCalico` 기능 플래그를 등록합니다.

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "EnableAKSWindowsCalico"
```

 [az feature list][az-feature-list] 명령을 사용하여 등록 상태를 확인할 수 있습니다.

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/EnableAKSWindowsCalico')].{Name:name,State:properties.state}"
```

준비가 되면 [az provider register][az-provider-register] 명령을 사용하여 *Microsoft.ContainerService* 리소스 공급자의 등록을 새로 고칩니다.

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

> [!IMPORTANT]
> 현재 Windows 노드와 함께 Calico 네트워크 정책을 사용하는 것은 Calico 3.17.2가 설치된 Kubernetes 버전 1.20 이상을 사용하는 새 클러스터에서 제공되며, Azure CNI 네트워킹을 사용해야 합니다. Calico를 사용하는 AKS 클러스터의 Windows 노드에도 기본적으로 [DSR(Direct Server Return)][dsr] 사용이 설정되어 있습니다.
>
> 이전 버전의 Calico에서 Kubernetes 1.20을 실행하는 Linux 노드 풀만 사용하는 클러스터의 경우에는 Calico 버전이 자동으로 3.17.2로 업그레이드됩니다.

Windows 노드를 사용하는 Calico 네트워킹 정책은 현재 미리 보기 상태입니다.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

클러스터에서 Windows Server 컨테이너의 관리자 자격 증명으로 사용할 사용자 이름을 만듭니다. 다음 명령은 사용자 이름을 묻는 메시지를 표시하고 차후 명령에서 사용할 수 있도록 사용자 이름을 WINDOWS_USERNAME으로 설정합니다(이 문서의 명령은 BASH 셸에 입력됩니다).

```azurecli-interactive
echo "Please enter the username to use as administrator credentials for Windows Server containers on your cluster: " && read WINDOWS_USERNAME
```

```azurecli
az aks create \
    --resource-group $RESOURCE_GROUP_NAME \
    --name $CLUSTER_NAME \
    --node-count 1 \
    --generate-ssh-keys \
    --service-cidr 10.0.0.0/16 \
    --dns-service-ip 10.0.0.10 \
    --docker-bridge-address 172.17.0.1/16 \
    --vnet-subnet-id $SUBNET_ID \
    --service-principal $SP_ID \
    --client-secret $SP_PASSWORD \
    --windows-admin-username $WINDOWS_USERNAME \
    --vm-set-type VirtualMachineScaleSets \
    --kubernetes-version 1.20.2 \
    --network-plugin azure \
    --network-policy calico
```

클러스터를 만드는 데 몇 분이 걸립니다. 기본적으로 클러스터는 Linux 노드 풀만 사용하여 만들어집니다. Windows 노드 풀을 사용하려는 경우 노드 풀을 추가할 수 있습니다. 예를 들면 다음과 같습니다.

```azurecli
az aks nodepool add \
    --resource-group $RESOURCE_GROUP_NAME \
    --cluster-name $CLUSTER_NAME \
    --os-type Windows \
    --name npwin \
    --node-count 1
```

클러스터가 준비되면 [az aks get-credentials][az-aks-get-credentials] 명령을 사용하여 Kubernetes 클러스터에 연결하도록 `kubectl`을 구성합니다. 이 명령은 자격 증명을 다운로드하고 해당 자격 증명을 사용하도록 Kubernetes CLI를 구성합니다.

```azurecli-interactive
az aks get-credentials --resource-group $RESOURCE_GROUP_NAME --name $CLUSTER_NAME
```

## <a name="deny-all-inbound-traffic-to-a-pod"></a>pod에 대한 모든 인바운드 트래픽 거부

특정 네트워크 트래픽을 허용하는 규칙을 정의하기 전에 먼저 모든 트래픽을 거부하는 네트워크 정책을 만듭니다. 이 정책은 원하는 트래픽만을 위한 허용 목록을 만들기 위한 시작점을 제공합니다. 이 네트워크 정책을 적용하면 트래픽이 삭제되는 것을 명확하게 확인할 수 있습니다.

샘플 애플리케이션 환경 및 트래픽 규칙의 경우 *development* 라는 네임스페이스를 만들어 예제 pod를 실행하겠습니다.

```console
kubectl create namespace development
kubectl label namespace/development purpose=development
```

NGINX를 실행하는 예제 백 엔드 pod를 만듭니다. 이 백 엔드 pod를 사용하여 샘플 백 엔드 웹 기반 애플리케이션을 시뮬레이트할 수 있습니다. *development* 네임스페이스에서 이 pod를 만들고 포트 *80* 을 열어 웹 트래픽을 처리합니다. 다음 섹션에서 네트워크 정책의 대상으로 지정할 수 있도록 pod에 *app=webapp,role=backend* 레이블을 지정합니다.

```console
kubectl run backend --image=mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine --labels app=webapp,role=backend --namespace development --expose --port 80
```

다른 pod를 만들고 터미널 세션을 연결하여 기본 NGINX 웹 페이지에 성공적으로 연결할 수 있는지 테스트합니다.

```console
kubectl run --rm -it --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11 network-policy --namespace development
```

셸 프롬프트에서 `wget`을 사용하여 기본 NGINX 웹 페이지에 액세스할 수 있는지 확인합니다.

```console
wget -qO- http://backend
```

다음 샘플 출력은 기본 NGINX 웹 페이지가 반환되었음을 보여줍니다.

```output
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

연결된 터미널 세션을 종료합니다. 테스트 pod가 자동으로 삭제됩니다.

```console
exit
```

### <a name="create-and-apply-a-network-policy"></a>네트워크 정책 만들기 및 적용

샘플 백 엔드 pod에서 기본 NGINX 웹 페이지를 사용할 수 있는지 확인했으므로 이제 모든 트래픽을 거부하는 네트워크 정책을 만들겠습니다. `backend-policy.yaml`라는 파일을 만들고 다음 YAML 매니페스트를 붙여 넣습니다. 이 매니페스트는 *podSelector* 를 사용하여 *app:webapp,role:backend* 레이블이 있는 샘플 NGINX pod와 같은 pod에 해당 정책을 연결합니다. *ingress* 아래에 규칙이 정의되어 있지 않으므로 pod로의 모든 인바운드 트래픽이 거부됩니다.

```yaml
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: backend-policy
  namespace: development
spec:
  podSelector:
    matchLabels:
      app: webapp
      role: backend
  ingress: []
```

브라우저에서 [https://shell.azure.com](https://shell.azure.com)으로 이동하여 Azure Cloud Shell을 엽니다.

[kubectl apply][kubectl-apply]명령을 사용하여 네트워크 정책을 적용하고 YAML 매니페스트의 이름을 지정합니다.

```console
kubectl apply -f backend-policy.yaml
```

### <a name="test-the-network-policy"></a>네트워크 정책 테스트

백 엔드 pod에서 NGINX 웹 페이지를 사용할 수 있는지 다시 확인해 보겠습니다. 다음과 같이 다른 테스트 Pod를 만들고 터미널 세션을 연결합니다.

```console
kubectl run --rm -it --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11 network-policy --namespace development
```

셸 프롬프트에서 `wget`을 사용하여 기본 NGINX 웹 페이지에 액세스할 수 있는지 확인합니다. 이번에는 제한 시간 값을 *2* 초로 설정합니다. 이제 네트워크 정책은 모든 인바운드 트래픽을 차단하므로 다음 예제와 같이 페이지를 로드할 수 없습니다.

```console
wget -O- --timeout=2 --tries=1 http://backend
```

```output
wget: download timed out
```

연결된 터미널 세션을 종료합니다. 테스트 pod가 자동으로 삭제됩니다.

```console
exit
```

## <a name="allow-inbound-traffic-based-on-a-pod-label"></a>pod 레이블을 기준으로 인바운드 트래픽 허용

이전 섹션에서는 백 엔드 NGINX pod를 예약했으며 모든 트래픽을 거부하는 네트워크 정책을 만들었습니다. 프런트 엔드 pod를 만들고 프런트 엔드 pod의 트래픽을 허용하도록 네트워크 정책을 업데이트해 보겠습니다.

*app:webapp,role:frontend* 레이블을 사용하여 네임스페이스에서 pod의 트래픽을 허용하도록 네트워크 정책을 업데이트합니다. 이전의 *backend-policy.yaml* 파일을 편집하고 *matchLabels* 수신 규칙을 추가하여 매니페스트를 다음 예제와 같이 설정합니다.

```yaml
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: backend-policy
  namespace: development
spec:
  podSelector:
    matchLabels:
      app: webapp
      role: backend
  ingress:
  - from:
    - namespaceSelector: {}
      podSelector:
        matchLabels:
          app: webapp
          role: frontend
```

> [!NOTE]
> 이 네트워크 정책은 수신 규칙에 대해 *namespaceSelector* 및 *podSelector* 요소를 사용합니다. YAML 구문은 수신 규칙 추가에 중요합니다. 이 예제에서 수신 규칙이 적용되려면 두 요소가 모두 일치해야 합니다. Kubernetes *1.12* 이전 버전에서는 이러한 요소가 올바르게 해석되지 않아 네트워크 트래픽이 예상대로 제한되지 않을 수 있습니다. 이 동작에 대한 자세한 내용은 [to 및 from 선택기의 동작][policy-rules]을 참조하세요.

[kubectl apply][kubectl-apply] 명령을 사용하여 업데이트된 네트워크 정책을 적용하고 YAML 매니페스트의 이름을 지정합니다.

```console
kubectl apply -f backend-policy.yaml
```

*app=webapp,role=frontend* 로 레이블이 지정된 pod를 예약하고 터미널 세션을 연결합니다.

```console
kubectl run --rm -it frontend --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11 --labels app=webapp,role=frontend --namespace development
```

셸 프롬프트에서 `wget`을 사용하여 기본 NGINX 웹 페이지에 액세스할 수 있는지 확인합니다.

```console
wget -qO- http://backend
```

수신 규칙이 *app: webapp,role: frontend* 레이블이 지정된 pod의 트래픽을 허용하기 때문에 프런트 엔드 pod의 트래픽이 허용됩니다. 다음 예제 출력은 기본 NGINX 웹 페이지가 반환되었음을 보여줍니다.

```output
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

연결된 터미널 세션을 종료합니다. Pod가 자동으로 삭제됩니다.

```console
exit
```

### <a name="test-a-pod-without-a-matching-label"></a>일치하는 레이블이 없는 pod 테스트

네트워크 정책은 *app: webapp,role: frontend* 레이블이 지정된 pod의 트래픽을 허용하지만 다른 모든 트래픽을 거부합니다. 이러한 레이블이 없는 다른 pod가 백 엔드 NGINX pod에 액세스할 수 있는지 테스트해 보겠습니다. 다음과 같이 다른 테스트 Pod를 만들고 터미널 세션을 연결합니다.

```console
kubectl run --rm -it --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11 network-policy --namespace development
```

셸 프롬프트에서 `wget`을 사용하여 기본 NGINX 웹 페이지에 액세스할 수 있는지 확인합니다. 네트워크 정책은 인바운드 트래픽을 차단하므로 다음 예제와 같이 페이지를 로드할 수 없습니다.

```console
wget -O- --timeout=2 --tries=1 http://backend
```

```output
wget: download timed out
```

연결된 터미널 세션을 종료합니다. 테스트 pod가 자동으로 삭제됩니다.

```console
exit
```

## <a name="allow-traffic-only-from-within-a-defined-namespace"></a>정의된 네임스페이스 내의 트래픽만 허용

이전 예제에서는 모든 트래픽을 거부하는 네트워크 트래픽을 만든 후 특정 레이블이 있는 pod의 트래픽을 허용하도록 정책을 업데이트했습니다. 일반적으로 필요한 다른 요구 사항은 지정된 네임스페이스 이내로만 트래픽을 제한하는 것입니다. 이전 예제가 *development* 네임스페이스의 트래픽용인 경우 다른 네임스페이스(예: *production*)의 트래픽이 pod에 도달하지 않도록 하는 네트워크 정책을 만듭니다.

먼저, 프로덕션 네임스페이스를 시뮬레이트하는 새 네임스페이스를 만듭니다.

```console
kubectl create namespace production
kubectl label namespace/production purpose=production
```

*app=webapp,role=frontend* 레이블이 지정된 *production* 네임스페이스에서 테스트 pod를 예약합니다. 다음과 같이 터미널 세션을 연결합니다.

```console
kubectl run --rm -it frontend --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11 --labels app=webapp,role=frontend --namespace production
```

셸 프롬프트에서 `wget`을 사용하여 기본 NGINX 웹 페이지에 액세스할 수 있는지 확인합니다.

```console
wget -qO- http://backend.development
```

Pod에 대한 레이블을 현재 네트워크 정책에서 허용되는 것과 일치하기 때문에 트래픽이 허용됩니다. 네트워크 정책은 pod 레이블만 확인하고, 네임스페이스는 확인하지 않습니다. 다음 예제 출력은 기본 NGINX 웹 페이지가 반환되었음을 보여줍니다.

```output
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

연결된 터미널 세션을 종료합니다. 테스트 pod가 자동으로 삭제됩니다.

```console
exit
```

### <a name="update-the-network-policy"></a>네트워크 정책 업데이트

*development* 네임스페이스 내의 트래픽만 허용하도록 수신 규칙 *namespaceSelector* 섹션을 업데이트해 보겠습니다. 다음 예제와 같이 *backend-policy.yaml* 매니페스트 파일을 편집합니다.

```yaml
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: backend-policy
  namespace: development
spec:
  podSelector:
    matchLabels:
      app: webapp
      role: backend
  ingress:
  - from:
    - namespaceSelector:
        matchLabels:
          purpose: development
      podSelector:
        matchLabels:
          app: webapp
          role: frontend
```

좀 더 복잡한 예제에서 *namespaceSelector* 를 사용한 다음, *podSelector* 를 사용하는 경우처럼 여러 수신 규칙을 정의할 수 있습니다.

[kubectl apply][kubectl-apply] 명령을 사용하여 업데이트된 네트워크 정책을 적용하고 YAML 매니페스트의 이름을 지정합니다.

```console
kubectl apply -f backend-policy.yaml
```

### <a name="test-the-updated-network-policy"></a>업데이트된 네트워크 정책 테스트

*production* 네임스페이스에서 다른 pod를 예약하고 터미널 세션을 연결합니다.

```console
kubectl run --rm -it frontend --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11 --labels app=webapp,role=frontend --namespace production
```

셸 프롬프트에서 `wget`을 사용하여 네트워크 정책이 트래픽을 거부하는 것을 확인합니다.

```console
wget -O- --timeout=2 --tries=1 http://backend.development
```

```output
wget: download timed out
```

테스트 pod를 종료합니다.

```console
exit
```

*production* 네임스페이스의 트래픽이 거부되므로, *development* 네임스페이스에서 테스트 pod를 다시 예약하고 터미널 세션을 연결합니다.

```console
kubectl run --rm -it frontend --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11 --labels app=webapp,role=frontend --namespace development
```

셸 프롬프트에서 `wget`을 사용하여 네트워크 정책이 트래픽을 허용하는 것을 확인합니다.

```console
wget -qO- http://backend
```

네트워크 정책에서 허용되는 것과 일치하는 pod가 네임스페이스에서 예약되기 때문에 트래픽이 허용됩니다. 다음 샘플 출력은 기본 NGINX 웹 페이지가 반환되었음을 보여줍니다.

```output
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

연결된 터미널 세션을 종료합니다. 테스트 pod가 자동으로 삭제됩니다.

```console
exit
```

## <a name="clean-up-resources"></a>리소스 정리

이 문서에서는 두 네임스페이스를 만들고 네트워크 정책을 적용했습니다. 이러한 리소스를 정리하려면 [kubectl delete][kubectl-delete] 명령을 사용하고 리소스 이름을 지정합니다.

```console
kubectl delete namespace production
kubectl delete namespace development
```

## <a name="next-steps"></a>다음 단계

네트워크 리소스에 대한 자세한 내용은 [애플리케이션에 대한 AKS(Azure Kubernetes Service)의 네트워크 개념][concepts-network]을 참조하세요.

정책에 대한 자세한 내용은 [Kubernetes 네트워크 정책][kubernetes-network-policies]을 참조하세요.

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubernetes-network-policies]: https://kubernetes.io/docs/concepts/services-networking/network-policies/
[azure-cni]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[policy-rules]: https://kubernetes.io/docs/concepts/services-networking/network-policies/#behavior-of-to-and-from-selectors
[aks-github]: https://github.com/azure/aks/issues
[tigera]: https://www.tigera.io/
[calicoctl]: https://docs.projectcalico.org/reference/calicoctl/
[calico-support]: https://www.tigera.io/tigera-products/calico/
[calico-logs]: https://docs.projectcalico.org/maintenance/troubleshoot/component-logs
[calico-aks-cleanup]: https://github.com/Azure/aks-engine/blob/master/docs/topics/calico-3.3.1-cleanup-after-upgrade.yaml

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[use-advanced-networking]: configure-azure-cni.md
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[concepts-network]: concepts-network.md
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[windows-server-password]: /windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements#reference
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[dsr]: ../load-balancer/load-balancer-multivip-overview.md#rule-type-2-backend-port-reuse-by-using-floating-ip
