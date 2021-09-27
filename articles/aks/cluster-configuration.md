---
title: AKS(Azure Kubernetes Service)의 클러스터 구성
description: AKS(Azure Kubernetes Service)에서 클러스터를 구성하는 방법 알아보기
services: container-service
ms.topic: article
ms.date: 02/09/2020
ms.author: jpalma
author: palma21
ms.openlocfilehash: 6c454c23eec0bb5b0fef1ceca3ad8f8e4c52493d
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124812600"
---
# <a name="configure-an-aks-cluster"></a>AKS 클러스터 구성

AKS 클러스터를 만드는 과정에서 필요에 따라 클러스터 구성을 사용자 지정해야 할 수 있습니다. 이 문서에서는 AKS 클러스터를 사용자 지정하기 위한 몇 가지 옵션을 소개합니다.

## <a name="os-configuration"></a>OS 구성

AKS는 클러스터에 대 한 GA (일반 공급)의 기본 노드 OS (운영 체제)로 Ubuntu 18.04를 지원 합니다.

## <a name="container-runtime-configuration"></a>컨테이너 런타임 구성

컨테이너 런타임은 컨테이너를 실행하고 노드에서 컨테이너 이미지를 관리하는 소프트웨어입니다. 런타임은 Linux 또는 Windows에서 컨테이너를 실행하기 위해 sys-calls 또는 OS(운영 체제) 특정 기능을 추상화하는 데 도움이 됩니다. Linux 노드 풀의 경우 `containerd` 는 Kubernetes 버전 1.19 이상을 사용 하는 노드 풀에 사용 됩니다. Windows Server 2019 노드 풀의 경우 `containerd`는 미리 보기에서 사용할 수 있으며 Kubernetes 1.20 이상을 사용하는 노드 풀에서 사용할 수 있지만, 기본적으로는 Docker가 계속 사용됩니다.

[`Containerd`](https://containerd.io/)는 노드에서 컨테이너를 실행하고 이미지를 관리하는 데 필요한 최소한의 기능 세트를 제공하는 [OCI](https://opencontainers.org/)(Open Container Initiative) 준수 핵심 컨테이너 런타임입니다. 이는 2017년 3월의 CNCF(Cloud Native Compute Foundation)에 [기증](https://www.cncf.io/announcement/2017/03/29/containerd-joins-cloud-native-computing-foundation/)되었습니다. AKS가 사용하는 현재 Moby(업스트림 Docker) 버전은 위에 표시된 대로 이미 활용되고 `containerd`를 기반으로 빌드됩니다.

`containerd` 기반 노드 및 노드 풀을 사용하면 kubelet은 `dockershim`과 대화하는 대신 CRI(컨테이너 런타임 인터페이스) 플러그 인을 통해 `containerd`와 직접 대화하여 Docker CRI 구현과 비교할 때 흐름의 추가 홉을 제거합니다. 따라서 Pod 시작 대기 시간이 단축되고 리소스(CPU 및 메모리) 사용량이 줄어듭니다.

AKS 노드에 `containerd`를 사용하면 Pod 시작 대기 시간이 개선되고 컨테이너 런타임에 의한 노드 리소스 사용량이 감소합니다. 이러한 개선은 Moby/docker 아키텍처에서 Kubelet이 `containerd`에 도달하기 전에 `dockershim` 및 Docker 엔진과 대화하는 동안 CRI 플러그 인을 통해 `containerd`에 직접 대화하는 이 새로운 아키텍처에 의해 사용되므로, 흐름에 홉이 추가됩니다.

![Docker CRI 2](media/cluster-configuration/containerd-cri.png)

`Containerd`는 AKS의 모든 GA 버전과 v1.19 이상의 모든 업스트림 Kubernets 버전에서 작동하며 모든 Kubernetes 및 AKS 기능을 지원합니다.

> [!IMPORTANT]
> Kubernetes v1.19 이상에서 생성된 Linux 노드 풀이 있는 클러스터는 컨테이너 런타임에 대해 기본적으로 `containerd`로 설정됩니다. 이전에 지원되던 Kubernetes 버전의 노드 풀이 포함된 클러스터는 해당하는 컨테이너 런타임에 대한 Docker를 수신합니다. Linux 노드 풀은 노드 풀 Kubernetes 버전이 `containerd`를 지원하는 버전으로 업데이트되고 나면 `containerd`로 업데이트됩니다. 지원이 중단될 때까지 이전 지원 버전에서 Docker 노드 풀 및 클러스터를 계속 사용할 수 있습니다.
> 
> Windows Server 2019 노드 풀을 통한 `containerd` 사용은 현재 미리 보기로 제공됩니다. 자세한 내용은 [`containerd`를 사용하여 Windows Server 노드 풀 추가][aks-add-np-containerd]를 참조하세요.
> 
> 노드 풀에 대해 `containerd`를 지원하는 Kubernetes 버전으로 클러스터를 사용하기 전에 AKS 노드 풀에서 `containerd`를 사용하여 워크로드를 테스트하는 것이 좋습니다.

### <a name="containerd-limitationsdifferences"></a>`Containerd` 제한 사항/차이점

* `containerd`의 경우, Kubernetes 노드에서 Pod, 컨테이너, 컨테이너 이미지의 **문제 해결** 을 위해 Docker CLI 대신 [`crictl`](https://kubernetes.io/docs/tasks/debug-application-cluster/crictl)를 대체 CLI로 사용하는 것이 좋습니다(예: `crictl ps`). 
   * Docker CLI의 전체 기능을 제공하지 않습니다. 문제 해결을 위한 용도로만 사용됩니다.
   * `crictl`은 Pod 등과 같은 개념이 있는 컨테이너에 대한 Kubernetes 친화적 보기를 제공합니다.
* `Containerd`는 표준화된 `cri` 로깅 형식을 사용하여 로깅을 설정합니다(현재 Docker의 json 드라이버에서 가져오는 것과는 다름). 로깅 솔루션은 `cri` 로깅 형식(예: [Azure Monitor for Containers](../azure-monitor/containers/container-insights-enable-new-cluster.md))을 지원해야 합니다.
* 더 이상 Docker 엔진 `/var/run/docker.sock`에 액세스하거나 DinD(Docker-in-Docker)를 사용할 수 없습니다.
  * 현재 Docker 엔진에서 애플리케이션 로그 또는 모니터링 데이터를 추출하는 경우 [Azure Monitor for Containers ](../azure-monitor/containers/container-insights-enable-new-cluster.md)와 같은 것을 대신 사용하세요. 또한 AKS는 불안정성을 유발할 수 있는 에이전트 노드에서 대역 외 명령 실행을 지원하지 않습니다.
  * Docker를 사용하는 경우에도 위의 방법을 통해 이미지를 빌드하고 Docker 엔진을 직접 활용하는 것은 권장되지 않습니다. Kubernetes는 사용된 리소스를 완전히 인식하지 못하며 이러한 접근 방식은 예를 들어 [여기](https://jpetazzo.github.io/2015/09/03/do-not-use-docker-in-docker-for-ci/)와 [여기](https://securityboulevard.com/2018/05/escaping-the-whale-things-you-probably-shouldnt-do-with-docker-part-1/)에 자세히 설명된 수많은 문제를 야기합니다.
* 이미지 빌드 - AKS 클러스터 내에서 이미지를 빌드하지 않는 한 현재 Docker 빌드 워크플로를 정상적으로 계속 사용할 수 있습니다. 이 경우 [ACR 작업](../container-registry/container-registry-quickstart-task-cli.md) 또는 [docker buildx](https://github.com/docker/buildx)와 같은 보다 안전한 클러스터 내 옵션을 사용하여 이미지를 빌드하는 데 권장되는 접근 방식으로 전환하는 것이 좋습니다.

## <a name="generation-2-virtual-machines"></a>2세대 가상 컴퓨터

Azure는 [Gen2(2세대) VM(가상 머신)](../virtual-machines/generation-2.md)을 지원합니다. 2세대 VM은 1세대 VM(Gen1)에서 지원되지 않는 주요 기능을 지원합니다. 이러한 기능에는 메모리 증가, Intel SGX(Software Guard Extensions) 및 vPMEM(가상화된 영구 메모리)이 포함됩니다.

2세대 VM은 1세대 VM에서 사용되는 BIOS 기반 아키텍처 대신 새 UEFI 기반 부팅 아키텍처를 사용합니다.
특정 SKU 및 크기만 Gen2 VM을 지원합니다. SKU가 Gen2를 지원하거나 요구하는지 알아보려면 [지원되는 크기 목록](../virtual-machines/generation-2.md#generation-2-vm-sizes)을 확인하세요.

또한 모든 VM 이미지가 Gen2를 지원하는 것은 아닙니다. AKS Gen2 VM에서는 새로운 [AKS Ubuntu 18.04 이미지](#os-configuration)를 사용합니다. 이 이미지는 모든 Gen2 SKU 및 크기를 지원합니다.

## <a name="ephemeral-os"></a>임시 OS

기본적으로 Azure는 VM을 다른 호스트로 재배치해야 하는 경우 데이터 손실을 방지하기 위해 가상 머신의 운영 체제 디스크를 Azure 스토리지에 자동으로 복제합니다. 그러나 컨테이너는 로컬 상태를 유지하도록 설계되지 않았기 때문에 이 동작은 제한된 가치를 제공하는 동시에 노드 프로비저닝이 느려지고 읽기/쓰기 지연 시간이 길어지는 몇 가지 단점을 제공합니다.

이와 반대로 임시 OS 디스크는 임시 디스크처럼 호스트 머신에만 저장됩니다. 이를 통해 더 빠른 노드 크기 조정 및 클러스터 업그레이드와 함께 읽기/쓰기 대기 시간이 줄어듭니다.

임시 디스크와 마찬가지로 임시 OS 디스크가 가상 머신 가격에 포함되어 있으므로 추가 스토리지 비용이 발생하지 않습니다.

> [!IMPORTANT]
>사용자가 OS에 대한 관리 디스크를 명시적으로 요청하지 않으면 AKS는 지정된 노드 풀 구성에 대해 가능한 경우 임시 OS로 기본 설정됩니다.

임시 OS를 사용하는 경우 OS 디스크는 VM 캐시에 맞아야 합니다. VM 캐시의 크기는 IO 처리량("GiB의 캐시 크기") 옆의 괄호 안에 있는 [Azure 설명서](../virtual-machines/dv3-dsv3-series.md)에서 확인할 수 있습니다.

예를 들어, 기본 OS 디스크 크기가 100GB인 AKS 기본 VM 크기 Standard_DS2_v2를 사용하는 경우 이 VM 크기는 임시 OS를 지원하지만 캐시 크기는 86GB입니다. 이 구성은 사용자가 명시적으로 지정하지 않으면 기본적으로 관리 디스크로 설정됩니다. 사용자가 임시 OS를 명시적으로 요청하면 유효성 검사 오류가 발생합니다.

사용자가 60GB OS 디스크로 동일한 Standard_DS2_v2를 요청하면 이 구성은 기본적으로 임시 OS로 설정됩니다. 요청된 크기인 60GB는 최대 캐시 크기인 86GB보다 작습니다.

Standard_D8s_v3을 100GB OS 디스크와 함께 사용하면 이 VM 크기는 임시 OS를 지원하고 200GB의 캐시 공간을 갖습니다. 사용자가 OS 디스크 유형을 지정하지 않으면 노드 풀은 기본적으로 임시 OS를 수신합니다. 

임시 OS에는 Azure CLI 버전 2.15.0 이상이 필요합니다.

### <a name="use-ephemeral-os-on-new-clusters"></a>새 클러스터에서 임시 OS 사용

클러스터가 생성될 때 임시 OS 디스크를 사용하도록 클러스터를 구성합니다. `--node-osdisk-type` 플래그를 사용하여 임시 OS를 새 클러스터의 OS 디스크 유형으로 설정합니다.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup -s Standard_DS3_v2 --node-osdisk-type Ephemeral
```

네트워크에 연결된 OS 디스크를 사용하여 일반 클러스터를 만들려면 `--node-osdisk-type=Managed`를 지정하면 됩니다. 아래에 따라 더 많은 임시 OS 노드 풀을 추가하도록 선택할 수도 있습니다.

### <a name="use-ephemeral-os-on-existing-clusters"></a>기존 클러스터에서 임시 OS 사용
새 노드 풀을 구성하여 임시 OS 디스크를 사용합니다. `--node-osdisk-type` 플래그를 사용하여 OS 디스크 유형을 해당 노드 풀의 OS 디스크 유형으로 설정합니다.

```azurecli
az aks nodepool add --name ephemeral --cluster-name myAKSCluster --resource-group myResourceGroup -s Standard_DS3_v2 --node-osdisk-type Ephemeral
```

> [!IMPORTANT]
> 임시 OS를 사용하면 VM 캐시 크기까지 VM 및 인스턴스 이미지를 배포할 수 있습니다. AKS의 경우 기본 노드 OS 디스크 구성은 128GB를 사용하므로 캐시가 128GB보다 큰 VM 크기가 필요합니다. 기본 Standard_DS2_v2의 캐시 크기는 86GB로 충분하지 않습니다. Standard_DS3_v2의 캐시 크기는 172GB로 충분합니다. `--node-osdisk-size`를 사용하여 OS 디스크의 기본 크기를 줄일 수도 있습니다. AKS 이미지의 최소 크기는 30GB입니다. 

네트워크에 연결된 OS 디스크로 노드 풀을 생성하려면 `--node-osdisk-type Managed`를 지정하면 됩니다.

## <a name="custom-resource-group-name"></a>사용자 지정 리소스 그룹 이름

Azure에서 Azure Kubernetes Service 클러스터를 배포하면 작업자 노드에 대한 두 번째 리소스 그룹이 생성됩니다. 기본적으로 AKS는 노드 리소스 그룹 이름을 `MC_resourcegroupname_clustername_location`으로 지정하지만 사용자 고유의 이름을 제공할 수도 있습니다.

고유한 리소스 그룹 이름을 지정하려면 aks-preview Azure CLI 확장 버전 0.3.2 이상을 설치합니다. Azure CLI 사용하여 `az aks create` 명령의 `--node-resource-group` 매개 변수를 사용하여 리소스 그룹의 사용자 지정 이름을 지정합니다. Azure Resource Manager 템플릿을 사용하여 AKS 클러스터를 배포하는 경우 `nodeResourceGroup` 속성을 사용하여 리소스 그룹 이름을 정의할 수 있습니다.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --node-resource-group myNodeResourceGroup
```

자체 구독에서 Azure 리소스 공급자가 자동으로 보조 리소스 그룹을 만듭니다. 클러스터가 생성될 때만 사용자 지정 리소스 그룹 이름을 지정할 수 있습니다. 

노드 리소스 그룹으로 작업할 때는 다음을 수행할 수 없습니다.

- 노드 리소스 그룹에 기존 리소스 그룹을 지정합니다.
- 노드 리소스 그룹에 다른 구독을 지정합니다.
- 클러스터를 만든 후 노드 리소스 그룹 이름을 변경합니다.
- 노드 리소스 그룹 내에서 관리 리소스의 이름을 지정합니다.
- 노드 리소스 그룹 내에서 관리 리소스의 Azure에서 만든 태그를 수정하거나 삭제합니다.

## <a name="next-steps"></a>다음 단계

- 클러스터에서 [노드 이미지를 업그레이드](node-image-upgrade.md)하는 방법을 알아보세요.
- 클러스터를 최신 버전의 Kubernetes로 업그레이드하는 방법을 알아보려면 [AKS(Azure Kubernetes Service) 클러스터 업그레이드](upgrade-cluster.md)를 참조하세요.
- [`containerd` 및 Kubernetes](https://kubernetes.io/blog/2018/05/24/kubernetes-containerd-integration-goes-ga/)에 대해 자세히 알아보세요.
- 몇 가지 일반적인 AKS 질문에 대한 답변을 확인하려면 [AKS에 대한 질문과 대답](faq.md) 목록을 참조하세요.
- [임시 OS 디스크](../virtual-machines/ephemeral-os-disks.md)에 대해 자세히 알아보세요.


<!-- LINKS - internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[aks-add-np-containerd]: windows-container-cli.md#add-a-windows-server-node-pool-with-containerd-preview