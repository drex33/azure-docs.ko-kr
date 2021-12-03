---
title: 를 사용하여 `command invoke` AKS(프라이빗 Azure Kubernetes Service) 클러스터에 액세스
description: 를 사용하여 `command invoke` AKS(프라이빗 Azure Kubernetes Service) 클러스터에 액세스하는 방법을 알아봅니다.
services: container-service
ms.topic: article
ms.date: 11/30/2021
ms.openlocfilehash: d94d9f2775af7e43077668e26688ad5508906db2
ms.sourcegitcommit: 5b25f76d0fd0ffb6784a2afab808fa55b3eac07b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/03/2021
ms.locfileid: "133519794"
---
# <a name="use-command-invoke-to-access-a-private-azure-kubernetes-service-aks-cluster"></a>를 사용하여 `command invoke` AKS(프라이빗 Azure Kubernetes Service) 클러스터에 액세스

프라이빗 AKS 클러스터에 액세스하려면 클러스터 가상 네트워크 또는 피어링된 네트워크에서 해당 클러스터에 연결해야 합니다. 이러한 접근 방식을 사용하려면 VPN, Express Route를 구성하거나 클러스터 가상 네트워크 내에서 *jumpbox를* 배포해야 합니다. 또는 를 사용하여 `command invoke` VPN 또는 Express Route를 구성하지 않고도 프라이빗 클러스터에 액세스할 수 있습니다. 를 사용하면 `command invoke` `kubectl` `helm` 클러스터에 직접 연결하지 않고도 Azure API를 통해 프라이빗 클러스터에서 및 같은 명령을 원격으로 호출할 수 있습니다. 사용 `command invoke` 권한은 및 역할을 통해 제어됩니다. `Microsoft.ContainerService/managedClusters/runcommand/action` `Microsoft.ContainerService/managedclusters/commandResults/read`

## <a name="prerequisites"></a>필수 구성 요소

* 기존 프라이빗 클러스터.
* Azure CLI 버전 2.24.0 이상
* 클러스터의 및 역할에 대한 액세스 `Microsoft.ContainerService/managedClusters/runcommand/action` `Microsoft.ContainerService/managedclusters/commandResults/read` 권한.

### <a name="limitations"></a>제한 사항

명령으로 만든 Pod는 `run` 다음 이진을 제공합니다.

* 가 있는 클러스터에 대해 호환되는 최신 버전의 `kubectl` `kustomize` 입니다.
* `helm`

또한 는 `command invoke` 클러스터에서 명령을 실행하므로 이러한 방식으로 실행되는 모든 명령은 클러스터에서 구성한 네트워킹 및 기타 제한 사항이 적용됩니다.

## <a name="use-invoke-commnad-to-run-a-single-command"></a>를 사용하여 `invoke commnad` 단일 명령 실행

를 사용하여 `az aks command invoke --command` 클러스터에서 명령을 실행합니다. 예를 들면 다음과 같습니다.

```azurecli-interactive
az aks command invoke \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --command "kubectl get pods -n kube-system"
```

위의 예제에서는 `kubectl get pods -n kube-system` *myResourceGroup* 의 *myAKSCluster* 클러스터에서 명령을 실행합니다.

## <a name="use-command-invoke-to-run-multiple-commands"></a>를 사용하여 `command invoke` 여러 명령 실행

를 사용하여 `az aks command invoke --command` 클러스터에서 여러 명령을 실행합니다. 예를 들면 다음과 같습니다.

```azurecli-interactive
az aks command invoke \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --command "helm repo add bitnami https://charts.bitnami.com/bitnami && helm repo update && helm install my-release bitnami/nginx"
```

위의 예제에서는 `helm` *myResourceGroup* 의 *myAKSCluster* 클러스터에서 세 개의 명령을 실행합니다.

## <a name="use-command-invoke-to-run-commands-an-with-attached-file-or-directory"></a>`command invoke`를 사용하여 연결된 파일 또는 디렉터리를 사용하여 명령을 실행합니다.

를 사용하여 `az aks command invoke --command` 클러스터에서 명령을 실행하고 `--file` 해당 명령에서 사용할 파일 또는 디렉터리를 연결합니다. 예를 들면 다음과 같습니다.

```azurecli-interactive
az aks command invoke \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --command "kubectl apply -f deployment.yaml -n default" \
  --file deployment.yaml
```

위의 내용은 `kubectl apply -f deployment.yaml -n default` *myResourceGroup* 의 *myAKSCluster* 클러스터에서 실행됩니다. `deployment.yaml`해당 명령에서 사용하는 파일은 가 실행된 개발 컴퓨터의 현재 디렉터리에서 `az aks command invoke` 연결됩니다.

현재 디렉터리에 있는 모든 파일을 첨부할 수도 있습니다. 예를 들면 다음과 같습니다.

```azurecli-interactive
az aks command invoke \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --command "kubectl apply -f deployment.yaml configmap.yaml -n default" \
  --file .
```

위의 내용은 `kubectl apply -f deployment.yaml configmap.yaml -n default` *myResourceGroup* 의 *myAKSCluster* 클러스터에서 실행됩니다. `deployment.yaml`해당 `configmap.yaml` 명령에서 사용하는 및 파일은 가 실행된 개발 컴퓨터의 현재 디렉터리에 `az aks command invoke` 속합니다.