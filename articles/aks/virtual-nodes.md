---
title: 가상 노드 사용
titleSuffix: Azure Kubernetes Service
description: AKS(Azure Kubernetes Services)에 가상 노드를 사용하는 방법의 개요
services: container-service
ms.topic: conceptual
ms.date: 02/17/2021
ms.custom: references_regions
ms.openlocfilehash: 78be2edacdcf72a6274755413810fe1308510a79
ms.sourcegitcommit: d9a2b122a6fb7c406e19e2af30a47643122c04da
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/24/2021
ms.locfileid: "114669127"
---
# <a name="create-and-configure-an-azure-kubernetes-services-aks-cluster-to-use-virtual-nodes"></a>AKS(Azure Kubernetes Service) 클러스터 만들기 및 가상 노드를 사용하도록 구성

AKS 클러스터에서 애플리케이션 워크로드를 신속하게 스케일링하기 위해 가상 노드를 사용할 수 있습니다. 가상 노드를 사용하면 Pod를 신속하게 프로비전할 수 있으며, 실행 시간(초) 단위로 요금이 청구됩니다. Kubernetes 클러스터 자동 크기 조정기가 추가 Pod를 실행하는 VM 컴퓨팅 노드를 배포할 때까지 기다릴 필요가 없습니다. 가상 노드는 Linux Pod 및 노드에서만 지원됩니다.

AKS용 가상 노드 추가 항목은 오픈 소스 프로젝트인 [Virtual Kubelet][virtual-kubelet-repo]을 기반으로 합니다.

이 문서에서는 가상 노드 사용을 위한 지역 가용성 및 네트워킹 요구 사항의 개요와 알려진 제한 사항을 제공합니다.

## <a name="regional-availability"></a>국가별 가용성

ACI가 VNET SKU를 지원하는 모든 지역은 가상 노드 배포를 지원합니다. 자세한 내용은 [Azure 지역의 Azure Container Instances에 대한 리소스 가용성](../container-instances/container-instances-region-availability.md)을 참조하세요.

각 지역에서 사용 가능한 CPU 및 메모리 SKU는 [Azure 지역 Azure Container Instances의 Azure Container Instances 리소스 가용성 - Linux 컨테이너 그룹](../container-instances/container-instances-region-availability.md#linux-container-groups)을 확인하세요.

## <a name="network-requirements"></a>네트워크 요구 사항

가상 노드는 ACI(Azure Container Instances)에서 실행되는 Pod와 AKS 클러스터 간의 네트워크 통신을 활성화합니다. 이 통신을 제공하기 위해 가상 네트워크 서브넷이 만들어지고 위임된 사용 권한이 할당됩니다. 가상 노드는 고급 네트워킹(Azure CNI)을 사용하여 만든 AKS 클러스터에서만 작동합니다. 기본적으로 AKS 클러스터는 기본 네트워킹(kubenet)을 사용하여 생성됩니다.

ACI(Azure Container Instances)에서 실행되는 Pod는 네트워킹을 구성하기 위해 AKS API 서버 엔드포인트에 액세스해야 합니다.

## <a name="known-limitations"></a>알려진 제한 사항

가상 노드 기능은 ACI의 기능 집합에 따라 크게 달라집니다. [Azure Container Instances 할당량 및 한도](../container-instances/container-instances-quotas.md) 외에도 다음 시나리오는 가상 노드에서 아직 지원되지 않습니다.

* 서비스 주체를 사용하여 ACR 이미지를 끌어옵니다. [해결 방법](https://github.com/virtual-kubelet/azure-aci/blob/master/README.md#private-registry)은 [Kubernetes 비밀](https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/#create-a-secret-by-providing-credentials-on-the-command-line)을 사용하는 것입니다.
* [Virtual Network 제한 사항](../container-instances/container-instances-vnet.md)에는 VNet 피어링, Kubernetes 네트워크 정책 및 네트워크 보안 그룹이 있는 인터넷으로의 아웃바운드 트래픽이 포함됩니다.
* 초기화 컨테이너
* [호스트 별칭](https://kubernetes.io/docs/concepts/services-networking/add-entries-to-pod-etc-hosts-with-host-aliases/)
* ACI의 exec에 대한 [인수](../container-instances/container-instances-exec.md#restrictions)
* [DaemonSets](concepts-clusters-workloads.md#statefulsets-and-daemonsets)는 가상 노드에 Pod를 배포하지 않습니다.
* 가상 노드는 Linux Pod 예약을 지원합니다. 오픈 소스 [Virtual Kubelet ACI](https://github.com/virtual-kubelet/azure-aci) 공급자를 수동으로 설치하여 Windows Server 컨테이너를 ACI로 예약할 수 있습니다.
* 가상 노드에는 Azure CNI 네트워킹을 사용하는 AKS 클러스터가 필요합니다.
* AKS에 API 서버 권한이 부여된 IP 범위 사용.
* 볼륨 탑재 Azure Files 공유는 [범용 V1](../storage/common/storage-account-overview.md#types-of-storage-accounts)을 지원합니다. [Azure Files 공유를 사용한 볼륨](azure-files-volume.md) 탑재 지침을 따르세요.
* IPv6 사용은 지원되지 않습니다.

## <a name="next-steps"></a>다음 단계

클러스터용 가상 노드 구성:

- [Azure CLI를 사용하여 가상 노드 만들기](virtual-nodes-cli.md)
- [AKS(Azure Kubernetes Service)에서 포털을 사용하여 가상 노드 만들기](virtual-nodes-portal.md)

가상 노드는 종종 AKS에서 크기 조정 솔루션의 구성 요소 중 하나입니다. 크기 조정 솔루션에 대한 자세한 내용은 다음 문서를 참조하세요.

- [Kubernetes 수평 방향 Pod 자동 크기 조정기 사용][aks-hpa]
- [Kubernetes 클러스터 자동 크기 조정기 사용][aks-cluster-autoscaler]
- [가상 노드에 대한 자동 크기 조정 샘플 체크 아웃][virtual-node-autoscale]
- [Virtual Kubelet 오픈 소스 라이브러리에 대해 자세히 알아보기][virtual-kubelet-repo]

<!-- LINKS - external -->
[aks-hpa]: tutorial-kubernetes-scale.md
[aks-cluster-autoscaler]: ./cluster-autoscaler.md
[virtual-node-autoscale]: https://github.com/Azure-Samples/virtual-node-autoscale
[virtual-kubelet-repo]: https://github.com/virtual-kubelet/virtual-kubelet
