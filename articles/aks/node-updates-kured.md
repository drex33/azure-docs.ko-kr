---
title: kured를 사용하여 Linux 노드 다시 부팅 처리
titleSuffix: Azure Kubernetes Service
description: AKS(Azure Kubernetes Service)에서 kured를 사용하여 Linux 노드를 업데이트하고 자동으로 다시 부팅하는 방법을 알아봅니다.
services: container-service
ms.topic: article
ms.date: 02/28/2019
ms.openlocfilehash: a81d778b8346a03622ef837b6732e7d50e807652
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122567227"
---
# <a name="apply-security-and-kernel-updates-to-linux-nodes-in-azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Service)에서 Linux 노드에 보안 및 커널 업데이트 적용

클러스터를 보호하도록 보안 업데이트가 AKS의 Linux 노드에 자동으로 적용됩니다. 이러한 업데이트는 OS 보안 수정 사항 또는 커널 업데이트를 포함합니다. 이러한 업데이트의 일부는 프로세스를 완료하도록 노드를 다시 부팅해야 합니다. AKS는 업데이트 프로세스를 완료하기 위해 이러한 Linux 노드를 자동으로 다시 부팅하지 않습니다.

Windows Server 노드를 최신 상태로 유지하는 프로세스는 약간 다릅니다. Windows Server 노드는 일일 업데이트를 수신하지 않습니다. 대신 최신 기본 Windows Server 이미지 및 패치를 사용하여 새 노드를 배포하는 AKS 업그레이드를 수행합니다. Windows Server 노드를 사용하는 AKS 클러스터는 [AKS에서 노드 풀 업그레이드][nodepool-upgrade]를 참조하세요.

이 문서에서는 오픈 소스 [kured(KUbernetes REboot Daemon)][kured]를 사용하여 다시 부팅이 필요한 Linux 노드를 살펴본 다음, 실행 중인 Pod의 다시 예약 및 노드 다시 부팅 프로세스를 자동으로 처리하는 방법을 보여줍니다.

> [!NOTE]
> `Kured`는 Weaveworks에서 제공되는 오픈 소스 프로젝트입니다. AKS에서 이 프로젝트에 대한 지원은 최상의 노력을 기준으로 제공됩니다. 추가 지원은 #weave-community Slack 채널에서 찾을 수 있습니다.

## <a name="before-you-begin"></a>시작하기 전에

이 문서에서는 기존 AKS 클러스터가 있다고 가정합니다. AKS 클러스터가 필요한 경우 AKS 빠른 시작 [Azure CLI 사용][aks-quickstart-cli] 또는 [Azure Portal 사용][aks-quickstart-portal]을 참조하세요.

또한 Azure CLI 버전 2.0.59 이상이 설치되고 구성되어 있어야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][install-azure-cli]를 참조하세요.

## <a name="understand-the-aks-node-update-experience"></a>AKS 노드 업데이트 환경 이해

AKS 클러스터에서 Kubernetes 노드는 Azure VM(가상 머신)으로 실행됩니다. 이러한 Linux 기반 VM은 매일 밤 업데이트를 자동으로 확인하도록 구성된 OS와 함께 Ubuntu 이미지를 사용합니다. 보안 또는 커널 업데이트를 사용할 수 있는 경우 자동으로 다운로드되고 설치됩니다.

![kured를 사용하여 AKS 노드 업데이트 및 프로세스 다시 부팅](media/node-updates-kured/node-reboot-process.png)

커널 업데이트와 같은 일부 보안 업데이트에서는 프로세스를 완료하기 위해 노드를 다시 부팅해야 합니다. 다시 부팅해야 하는 Linux 노드에서 */var/run/reboot-required* 파일을 만듭니다. 이 다시 부팅 프로세스는 자동으로 발생하지 않습니다.

사용자 고유의 워크플로 및 프로세스를 사용하여 노드 다시 부팅을 처리하거나 `kured`를 사용하여 프로세스를 오케스트레이션할 수 있습니다. `kured`를 사용하여 클러스터의 각 Linux 노드에서 Pod를 실행하는 [DaemonSet][DaemonSet]이 배포됩니다. DaemonSet의 이 Pod는 */var/run/reboot-required* 파일이 있는지 살펴본 다음 프로세스를 시작하여 노드를 다시 부팅합니다.

### <a name="node-image-upgrades"></a>노드 이미지 업그레이드

자동 업그레이드는 Linux 노드 OS에 업데이트를 적용하지만 클러스터에 대한 노드를 만드는 데 사용되는 이미지는 변경되지 않습니다. 새 Linux 노드가 클러스터에 추가되면 원래 이미지를 사용하여 노드를 만듭니다. 이 새 노드는 매일 밤 자동 검사 중에 사용 가능한 모든 보안 및 커널 업데이트를 받지만 모든 검사와 다시 시작이 완료될 때까지 패치되지 않습니다.

또는 노드 이미지 업그레이드를 사용하여 클러스터에서 사용하는 노드 이미지를 검사하고 업데이트할 수 있습니다. 노드 이미지 업그레이드에 관한 자세한 내용은 [AKS(Azure Kubernetes Service) 노드 이미지 업그레이드][node-image-upgrade]를 참조하세요.

### <a name="node-upgrades"></a>노드 업그레이드

AKS에 클러스터를 *업그레이드* 할 수 있는 추가 프로세스가 있습니다. 업그레이드는 일반적으로 노드 보안 업데이트를 적용하는 것 뿐만 아니라 Kubernetes의 최신 버전으로 이동하는 것입니다. AKS 업그레이드는 다음 작업을 수행합니다.

* 새 노드가 최신 보안 업데이트 및 적용된 Kubernetes 버전으로 배포됩니다.
* 이전 노드가 통제되고 드레이닝됩니다.
* Pod는 새 노드에서 예약됩니다.
* 이전 노드가 삭제됩니다.

업그레이드 이벤트 중 동일한 Kubernetes 버전에 있을 수 없습니다. Kubernetes의 최신 버전을 지정해야 합니다. Kubernetes의 최신 버전으로 업그레이드하려면 [AKS 클러스터를 업그레이드][aks-upgrade]할 수 있습니다.

## <a name="deploy-kured-in-an-aks-cluster"></a>AKS 클러스터에서 kured 배포

`kured` DaemonSet을 배포하려면 다음 공식 Kured Helm 차트를 설치합니다. 그러면 역할 및 클러스터 역할, 바인딩, 서비스 계정을 만든 다음 `kured`를 사용하여 DaemonSet을 배포합니다.

```console
# Add the Kured Helm repository
helm repo add kured https://weaveworks.github.io/kured

# Update your local Helm chart repository cache
helm repo update

# Create a dedicated namespace where you would like to deploy kured into
kubectl create namespace kured

# Install kured in that namespace with Helm 3 (only on Linux nodes, kured is not working on Windows nodes)
helm install kured kured/kured --namespace kured --set nodeSelector."kubernetes\.io/os"=linux
```

Prometheus 또는 Slack과 통합과 같은 `kured`에 대한 추가 매개 변수를 구성할 수도 있습니다. 추가 구성 매개 변수에 대한 자세한 내용은 [kured Helm 차트][kured-install]를 참조하세요.

## <a name="update-cluster-nodes"></a>클러스터 노드 업데이트

기본적으로 AKS의 Linux 노드에서는 매일 저녁 업데이트를 확인합니다. 기다리지 않으려는 경우 `kured`가 올바르게 실행되는지 확인하도록 업데이트를 수동으로 수행할 수 있습니다. 먼저 단계에 따라 [AKS 노드 중 하나에 SSH][aks-ssh]합니다. Linux 노드에 대한 SSH 연결을 설정한 후에는 업데이트를 확인하고 다음과 같이 적용합니다.

```console
sudo apt-get update && sudo apt-get upgrade -y
```

노드를 재부팅해야 하는 업데이트가 적용된 경우 파일은 */var/run/reboot-required* 에 작성됩니다. `Kured`는 다시 부팅해야 하는 노드를 기본적으로 60분마다 확인합니다.

## <a name="monitor-and-review-reboot-process"></a>다시 부팅 프로세스 모니터링 및 검토

DaemonSet의 복제본 중 하나가 노드 다시 부팅이 필요한 것을 감지한 경우 Kubernetes API를 통해 노드에 잠금이 배치됩니다. 이 잠금은 추가 Pod가 노드에서 예약되는 것을 방지합니다. 또한 잠금은 한 번에 하나의 노드만 다시 부팅되어야 함을 나타냅니다. 노드 통제가 꺼지면 실행 중인 Pod가 노드에서 드레이닝된 다음, 노드가 다시 부팅됩니다.

[kubectl get nodes][kubectl-get-nodes] 명령을 사용하여 노드의 상태를 모니터링할 수 있습니다. 다음 예제 출력은 노드가 다시 부팅 프로세스를 준비하는 동안 *SchedulingDisabled* 의 상태로 노드를 보여줍니다.

```
NAME                       STATUS                     ROLES     AGE       VERSION
aks-nodepool1-28993262-0   Ready,SchedulingDisabled   agent     1h        v1.11.7
```

업데이트 프로세스가 완료되면  매개 변수와 함께 [kubectl get nodes`--output wide`][kubectl-get-nodes] 명령을 사용하여 노드의 상태를 볼 수 있습니다. 이 추가 출력을 통해 다음 예제 출력에 표시된 것처럼 기본 노드의 *KERNEL-VERSION* 에서 차이점을 확인할 수 있습니다. *aks-nodepool1-28993262-0* 은 이전 단계에서 업데이트되었으며 커널 버전 *4.15.0-1039-azure* 를 보여 줍니다. 업데이트되지 않은 노드 *aks-nodepool1-28993262-1* 은 커널 버전 *4.15.0-1037-azure* 를 보여 줍니다.

```
NAME                       STATUS    ROLES     AGE       VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE             KERNEL-VERSION      CONTAINER-RUNTIME
aks-nodepool1-28993262-0   Ready     agent     1h        v1.11.7   10.240.0.4    <none>        Ubuntu 16.04.6 LTS   4.15.0-1039-azure   docker://3.0.4
aks-nodepool1-28993262-1   Ready     agent     1h        v1.11.7   10.240.0.5    <none>        Ubuntu 16.04.6 LTS   4.15.0-1037-azure   docker://3.0.4
```

## <a name="next-steps"></a>다음 단계

이 문서에서는 보안 업데이트 프로세스의 일부로 `kured`를 사용하여 Linux 노드를 자동으로 다시 부팅하는 방법을 자세히 설명했습니다. Kubernetes의 최신 버전으로 업그레이드하려면 [AKS 클러스터를 업그레이드][aks-upgrade]할 수 있습니다.

Windows Server 노드를 사용하는 AKS 클러스터는 [AKS에서 노드 풀 업그레이드][nodepool-upgrade]를 참조하세요.

<!-- LINKS - external -->
[kured]: https://github.com/weaveworks/kured
[kured-install]: https://github.com/weaveworks/kured/tree/master/charts/kured
[kubectl-get-nodes]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[DaemonSet]: concepts-clusters-workloads.md#statefulsets-and-daemonsets
[aks-ssh]: ssh.md
[aks-upgrade]: upgrade-cluster.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[node-image-upgrade]: node-image-upgrade.md
