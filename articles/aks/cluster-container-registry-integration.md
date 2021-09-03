---
title: Azure Container Registry와 Azure Kubernetes Service 통합
description: AKS(Azure Kubernetes Service)와 ACR(Azure Container Registry)을 통합하는 방법 알아보기
services: container-service
manager: gwallace
ms.topic: article
ms.date: 06/10/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 62dd73e216b8b1ff1d9bb61210c90f8457b5a4fe
ms.sourcegitcommit: 98308c4b775a049a4a035ccf60c8b163f86f04ca
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/30/2021
ms.locfileid: "113105708"
---
# <a name="authenticate-with-azure-container-registry-from-azure-kubernetes-service"></a>Azure Kubernetes Service의 Azure Container Registry를 사용하여 인증

AKS(Azure Kubernetes Service)에서 ACR(Azure Container Registry)을 사용할 때는 인증 메커니즘을 설정해야 합니다. 이 작업은 ACR에 필요한 권한을 부여하여 CLI, PowerShell 및 포털 환경의 일부로 구현됩니다. 이 문서에서는 두 Azure 서비스 간의 인증을 구성하는 예제를 제공합니다.

Azure CLI 또는 Azure PowerShell을 사용하면 몇 가지 간단한 명령으로 AKS와 ACR의 통합을 설정할 수 있습니다. 이 통합은 AKS 클러스터에 연결된 관리 ID에 AcrPull 역할을 할당합니다.

> [!NOTE]
> 이 문서에서는 AKS와 ACR 간의 자동 인증을 다룹니다. 프라이빗 외부 레지스트리에서 이미지를 풀해야 하는 경우 [이미지 풀 비밀][Image Pull Secret]을 사용합니다.

## <a name="before-you-begin"></a>시작하기 전에

이러한 예제에는 다음이 필요합니다.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

* **소유자**, **Azure 계정 관리자**, 또는 **Azure 공동 관리자** 역할을 **Azure 구독에서 수행하는 경우**
* Azure CLI 버전 2.7.0 이상

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

* **소유자**, **Azure 계정 관리자**, 또는 **Azure 공동 관리자** 역할을 **Azure 구독에서 수행하는 경우**
* Azure PowerShell 버전 5.9.0 이상.

---

**소유자**, **Azure 계정 관리자**, 또는 **Azure 공동 관리자** 역할이 필요하지 않도록 하려면 기존 관리 ID를 사용하여 AKS에서 ACR을 인증할 수 있습니다. 자세한 내용은 [Azure Container Registry에 인증하기 위해 Azure 관리 ID 사용](../container-registry/container-registry-authentication-managed-identity.md)을 참조하세요.

## <a name="create-a-new-aks-cluster-with-acr-integration"></a>ACR 통합을 사용하여 새 AKS 클러스터 만들기

AKS 클러스터를 처음 만들 때 AKS 및 ACR 통합을 설정할 수 있습니다.  AKS 클러스터가 ACR과 상호 작용하도록 Azure Active Directory **관리 ID** 가 사용됩니다. 다음 명령을 사용하여 구독의 기존 ACR에 권한을 부여하고 관리 ID에 적절한 **ACRPull** 역할을 구성할 수 있습니다. 아래 매개 변수에 대한 유효한 값을 제공합니다.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
# set this to the name of your Azure Container Registry.  It must be globally unique
MYACR=myContainerRegistry

# Run the following line to create an Azure Container Registry if you do not already have one
az acr create -n $MYACR -g myContainerRegistryResourceGroup --sku basic

# Create an AKS cluster with ACR integration
az aks create -n myAKSCluster -g myResourceGroup --generate-ssh-keys --attach-acr $MYACR
```

또는 다음 형식의 ACR 리소스 ID를 사용하여 ACR 이름을 지정할 수 있습니다.

`/subscriptions/\<subscription-id\>/resourceGroups/\<resource-group-name\>/providers/Microsoft.ContainerRegistry/registries/\<name\>`

> [!NOTE]
> AKS 클러스터와는 다른 구독에 있는 ACR을 사용하는 경우 AKS 클러스터에서 연결하거나 분리할 때 ACR 리소스 ID를 사용합니다.

```azurecli
az aks create -n myAKSCluster -g myResourceGroup --generate-ssh-keys --attach-acr /subscriptions/<subscription-id>/resourceGroups/myContainerRegistryResourceGroup/providers/Microsoft.ContainerRegistry/registries/myContainerRegistry
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell
# set this to the name of your Azure Container Registry.  It must be globally unique
$MYACR = 'myContainerRegistry'

# Run the following line to create an Azure Container Registry if you do not already have one
New-AzContainerRegistry -Name $MYACR -ResourceGroupName myContainerRegistryResourceGroup -Sku Basic

# Create an AKS cluster with ACR integration
New-AzAksCluster -Name myAKSCluster -ResourceGroupName myResourceGroup -GenerateSshKey -AcrNameToAttach $MYACR
```

---

이 단계를 완료하는 데 몇 분 정도 걸릴 수 있습니다.

## <a name="configure-acr-integration-for-existing-aks-clusters"></a>기존 AKS 클러스터에 대한 ACR 통합 구성

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

아래와 같이 **acr-name** 또는 **acr-resource-id** 에 유효한 값을 제공하여 기존 ACR을 기존 AKS 클러스터와 통합합니다.

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --attach-acr <acr-name>
```

또는

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --attach-acr <acr-resource-id>
```

> [!NOTE]
> `az aks update --attach-acr`을 실행하면 명령을 실행하는 사용자의 권한을 사용하여 역할 ACR 할당을 생성합니다. 이 역할은 kubelet 관리 ID에 할당됩니다. AKS 관리 ID에 대한 자세한 내용은 [관리 ID 요약][summary-msi]을 참조하세요.

다음을 사용하여 ACR과 AKS 클러스터 간의 통합을 제거할 수도 있습니다.

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --detach-acr <acr-name>
```

또는

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --detach-acr <acr-resource-id>
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

아래와 같이 **acr-name** 에 유효한 값을 제공하여 기존 ACR을 기존 AKS 클러스터와 통합합니다.

```azurepowershell
Set-AzAksCluster -Name myAKSCluster -ResourceGroupName myResourceGroup -AcrNameToAttach <acr-name>
```

> [!NOTE]
> `Set-AzAksCluster -AcrNameToAttach`을 실행하면 명령을 실행하는 사용자의 권한을 사용하여 역할 ACR 할당을 생성합니다. 이 역할은 kubelet 관리 ID에 할당됩니다. AKS 관리 ID에 대한 자세한 내용은 [관리 ID 요약][summary-msi]을 참조하세요.

다음을 사용하여 ACR과 AKS 클러스터 간의 통합을 제거할 수도 있습니다.

```azurepowershell
Set-AzAksCluster -Name myAKSCluster -ResourceGroupName myResourceGroup -AcrNameToDetach <acr-name>
```

---

## <a name="working-with-acr--aks"></a>ACR 및 AKS 사용

### <a name="import-an-image-into-your-acr"></a>ACR로 이미지 가져오기

다음을 실행하여 Docker 허브에서 ACR로 이미지를 가져옵니다.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az acr import  -n <acr-name> --source docker.io/library/nginx:latest --image nginx:v1
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell
Import-AzContainerRegistryImage -RegistryName <acr-name> -ResourceGroupName myResourceGroup -SourceRegistryUri docker.io -SourceImage library/nginx:latest
```

---

### <a name="deploy-the-sample-image-from-acr-to-aks"></a>ACR에서 AKS로 샘플 이미지 배포

적절한 AKS 자격 증명 보유

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az aks get-credentials -g myResourceGroup -n myAKSCluster
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell
Import-AzAksCredential -ResourceGroupName myResourceGroup -Name myAKSCluster
```

---

다음을 포함하는 **acr-nginx.yaml** 이라는 파일을 만듭니다. **acr-name** 을 레지스트리의 리소스 이름으로 바꿉니다. 예를 들면 *myContainerRegistry* 입니다.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx0-deployment
  labels:
    app: nginx0-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx0
  template:
    metadata:
      labels:
        app: nginx0
    spec:
      containers:
      - name: nginx
        image: <acr-name>.azurecr.io/nginx:v1
        ports:
        - containerPort: 80
```

다음으로 AKS 클러스터에서 이 배포를 실행합니다.

```console
kubectl apply -f acr-nginx.yaml
```

다음을 실행하여 배포를 모니터링할 수 있습니다.

```console
kubectl get pods
```

실행 중인 Pod가 두 개 있어야 합니다.

```output
NAME                                 READY   STATUS    RESTARTS   AGE
nginx0-deployment-669dfc4d4b-x74kr   1/1     Running   0          20s
nginx0-deployment-669dfc4d4b-xdpd6   1/1     Running   0          20s
```

### <a name="troubleshooting"></a>문제 해결
* [az aks check-acr](/cli/azure/aks#az_aks_check_acr) 명령을 실행하여 AKS 클러스터에서 레지스트리에 액세스할 수 있는지 확인합니다.
* [ACR 모니터링](../container-registry/monitor-service.md)에 대해 알아봅니다.
* [ACR 상태](../container-registry/container-registry-check-health.md)에 대한 자세한 정보

<!-- LINKS - external -->
[AKS AKS CLI]: /cli/azure/aks#az_aks_create
[Image Pull secret]: https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/

[summary-msi]: use-managed-identity.md#summary-of-managed-identities
