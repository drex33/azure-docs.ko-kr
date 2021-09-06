---
title: 개념 - AKS(Azure Kubernetes Service)의 보안
description: 마스터 및 노드 통신, 네트워크 정책 및 Kubernetes 비밀을 비롯한 AKS(Azure Kubernetes Service)의 보안에 대해 알아봅니다.
services: container-service
author: mlearned
ms.topic: conceptual
ms.date: 03/11/2021
ms.author: mlearned
ms.openlocfilehash: bf589591ae1c4f9fa3dca2b16cc5382def0740e7
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122537338"
---
# <a name="security-concepts-for-applications-and-clusters-in-azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Service)의 애플리케이션 및 클러스터에 대한 보안 개념

클러스터 보안은 AKS(Azure Kubernetes Service)에서 애플리케이션 워크로드를 실행할 때 고객 데이터를 보호합니다. 

Kubernetes에는 ‘네트워크 정책’ 및 ‘비밀’과 같은 보안 구성 요소가 포함됩니다.  한편, Azure에는 네트워크 보안 그룹 및 오케스트레이션된 클러스터 업그레이드와 같은 구성 요소가 포함됩니다. AKS는 이러한 보안 구성 요소를 결합하여 다음을 수행합니다.
* AKS 클러스터에서 최신 OS 보안 업데이트 및 Kubernetes 릴리스를 실행하도록 합니다.
* 보안 Pod 트래픽 및 중요한 자격 증명에 대한 액세스 권한을 제공합니다.

이 문서에서는 AKS에서 애플리케이션을 보호하는 핵심 개념을 소개합니다.

- [AKS(Azure Kubernetes Service)의 애플리케이션 및 클러스터에 대한 보안 개념](#security-concepts-for-applications-and-clusters-in-azure-kubernetes-service-aks)
  - [마스터 보안](#master-security)
  - [노드 보안](#node-security)
    - [컴퓨팅 격리](#compute-isolation)
  - [클러스터 업그레이드](#cluster-upgrades)
    - [차단 및 드레이닝](#cordon-and-drain)
  - [네트워크 보안](#network-security)
    - [Azure 네트워크 보안 그룹](#azure-network-security-groups)
  - [Kubernetes 비밀](#kubernetes-secrets)
  - [다음 단계](#next-steps)

## <a name="master-security"></a>마스터 보안

AKS에서 Kubernetes 마스터 구성 요소는 Microsoft에서 제공하고 관리하며 유지하는 관리형 서비스의 일부입니다. 각 AKS 클러스터에는 API 서버, 스케줄러 등을 제공하는 고유한 단일 테넌트의 전용 Kubernetes 마스터가 있습니다.

기본적으로 Kubernetes API 서버는 공용 IP 주소 및 FQDN(정규화된 도메인 이름)을 사용합니다. [권한 있는 IP 범위][authorized-ip-ranges]를 사용하여 API 서버 엔드포인트에 대한 액세스를 제한할 수 있습니다. 또한 완전한 [프라이빗 클러스터][private-clusters]를 만들어 가상 네트워크에 대한 API 서버 액세스를 제한할 수 있습니다.

Kubernetes RBAC(역할 기반 액세스 제어) 및 Azure RBAC를 사용하여 API 서버에 대한 액세스를 제어할 수 있습니다. 자세한 내용은 [AKS와 Azure AD 통합][aks-aad]을 참조하세요.

## <a name="node-security"></a>노드 보안

AKS 노드는 사용자가 관리하고 유지하는 Azure VM(가상 머신)입니다. 
* Linux 노드는 `containerd` 또는 Docker 컨테이너 런타임을 사용하여 최적화된 Ubuntu 배포를 실행합니다. 
* Windows Server 노드는 `containerd` 또는 Docker 컨테이너 런타임을 사용하여 최적화된 Windows Server 2019 릴리스를 실행합니다.

AKS 클러스터가 생성되거나 강화되면 노드는 최신 OS 보안 업데이트 및 구성을 사용하여 자동으로 배포됩니다.

> [!NOTE]
> AKS 클러스터는 다음과 같이 사용됩니다.
> * Linux 노드 풀에 대해 Kubernetes 버전 1.19 이상을 사용하는 AKS 클러스터는 `containerd`를 컨테이너 런타임으로 사용합니다. Windows Server 2019 노드 풀을 통한 `containerd` 사용은 현재 미리 보기로 제공됩니다. 자세한 내용은 [`containerd`를 사용하여 Windows Server 노드 풀 추가][aks-add-np-containerd]를 참조하세요.
> * Linux 노드 풀에 대한 Kubernetes 버전이 1.19 이전일 경우에는 Docker를 컨테이너 런타임으로 사용합니다. Windows Server 2019 노드 풀의 경우 Docker가 기본 컨테이너 런타임입니다.

### <a name="node-security-patches"></a>노드 보안 패치

#### <a name="linux-nodes"></a>Linux 노드
매일 저녁 AKS의 Linux 노드는 배포판 보안 업데이트 채널을 통해 보안 패치를 받습니다. 이 동작은 노드가 AKS 클러스터에 배포되면 자동으로 구성됩니다. 실행 중인 워크로드 중단 및 잠재적인 영향을 최소화하기 위해 보안 패치 또는 커널 업데이트에 필요한 경우에도 노드가 자동으로 다시 부팅되지 않습니다. 노드 다시 부팅을 처리하는 방법에 대한 자세한 내용은 [AKS에서 노드에 보안 및 커널 업데이트 적용][aks-kured]을 참조하세요.

야간 업데이트는 노드의 OS에 보안 업데이트를 적용하지만 클러스터에 대한 노드를 만드는 데 사용되는 노드 이미지는 변경되지 않습니다. 새 Linux 노드가 클러스터에 추가되면 원래 이미지를 사용하여 노드를 만듭니다. 이 새 노드는 매일 밤 자동 검사 중에 사용 가능한 모든 보안 및 커널 업데이트를 받지만 모든 검사와 다시 시작이 완료될 때까지 패치되지 않습니다. 노드 이미지 업그레이드를 사용하여 클러스터에서 사용하는 노드 이미지를 검사하고 업데이트할 수 있습니다. 노드 이미지 업그레이드에 관한 자세한 내용은 [AKS(Azure Kubernetes Service) 노드 이미지 업그레이드][node-image-upgrade]를 참조하세요.

#### <a name="windows-server-nodes"></a>Windows Server 노드

Windows Server 노드의 경우 Windows 업데이트가 자동으로 실행되어 최신 업데이트를 적용하지 않습니다. 일반 Windows 업데이트 릴리스 주기 및 사용자 고유의 유효성 검사 프로세스를 기준으로 AKS 클러스터에서 Windows Server 노드 풀 업그레이드를 예약합니다. 이 업그레이드 프로세스는 최신 Windows Server 이미지 및 패치를 실행하는 노드를 만든 다음, 이전 노드를 제거합니다. 이 프로세스에 대한 자세한 내용은 [AKS에서 노드 풀 업그레이드][nodepool-upgrade]를 참조하세요.

### <a name="node-deployment"></a>노드 배포
노드는 공용 IP 주소가 할당되지 않은 상태에서 프라이빗 가상 네트워크 서브넷에 배포됩니다. 문제 해결 및 관리를 위해 SSH는 기본적으로 사용하도록 설정되어 있으며 내부 IP 주소를 사용해서만 액세스할 수 있습니다.

### <a name="node-storage"></a>노드 스토리지
스토리지를 제공하기 위해 노드는 Azure Managed Disks를 사용합니다. 대부분의 VM 노드 크기에서 Azure Managed Disks는 고성능 SSD로 지원되는 프리미엄 디스크입니다. 관리 디스크에 저장된 데이터는 미사용 시 Azure 플랫폼에서 자동으로 저장 데이터 암호화됩니다. 중복을 개선하기 위해 Azure Managed Disks는 Azure 데이터 센터 내에서 안전하게 복제됩니다.

### <a name="hostile-multi-tenant-workloads"></a>악의적인 다중 테넌트 워크로드

현재 Kubernetes 환경은 악의적인 다중 테넌트 사용에 대해 안전하지 않습니다. 노드에 대한 ‘Pod 보안 정책’이나 Kubernetes RBAC 같은 추가 보안 기능을 통해 악용을 효율적으로 차단할 수 있습니다. 악의적인 다중 테넌트 워크로드를 실행할 때의 진정한 보안을 위해서는 하이퍼바이저만 신뢰하세요. Kubernetes의 보안 도메인은 개별 노드가 아닌 전체 클러스터가 됩니다. 

이러한 유형의 악의적인 다중 테넌트 워크로드의 경우 물리적으로 격리된 클러스터를 사용해야 합니다. 워크로드를 격리하는 방법에 대한 자세한 내용은 [AKS의 클러스터 격리에 대한 모범 사례][cluster-isolation]를 참조하세요.

### <a name="compute-isolation"></a>컴퓨팅 격리

규정 준수 또는 규정 요구 사항으로 인해 특정 워크로드에서는 다른 고객 워크로드와 높은 수준의 격리를 요구할 수 있습니다. 이러한 워크로드에 대해 Azure는 AKS 클러스터에서 에이전트 노드로 사용할 [격리된 VM](../virtual-machines/isolation.md)을 제공합니다. 이러한 VM은 특정 하드웨어 종류로 격리되며 단일 고객 전용입니다. 

AKS 클러스터를 만들거나 노드 풀을 추가할 때 [격리된 VM 크기 중 하나](../virtual-machines/isolation.md)를 **노드 크기** 로 선택합니다.

## <a name="cluster-upgrades"></a>클러스터 업그레이드

Azure는 AKS 클러스터 및 구성 요소를 업그레이드하고, 보안 및 규정 준수를 유지하고, 최신 기능에 액세스하는 업그레이드 오케스트레이션 도구를 제공합니다. 업그레이드 오케스트레이션에는 Kubernetes 마스터 및 에이전트 구성 요소가 모두 포함됩니다. 

업그레이드 프로세스를 시작하려면 [나열된 사용 가능한 Kubernetes 버전](supported-kubernetes-versions.md) 중 하나를 지정합니다. 그러면 Azure에서 각 AKS 노드를 안전하게 통제하고 드레이닝하여 업그레이드합니다.

### <a name="cordon-and-drain"></a>차단 및 드레이닝

업그레이드 프로세스 중에 AKS 노드는 클러스터에서 개별적으로 통제되어 새 Pod가 노드에서 예약되지 않도록 합니다. 그런 다음, 노드가 다음과 같이 드레이닝되고 업그레이드됩니다.

1.  새 노드가 노드 풀에 배포됩니다. 
    * 이 노드는 최신 OS 이미지 및 패치를 실행합니다.
1. 기존 노드 중 하나가 업그레이드용으로 식별됩니다. 
1. 식별된 노드의 Pod가 정상적으로 종료되고 노드 풀의 다른 노드에서 예약됩니다.
1. 비워진 노드는 AKS 클러스터에서 삭제됩니다.
1. 모든 노드가 업그레이드 프로세스의 일부로 성공적으로 대체될 때까지 1~4단계가 반복됩니다.

자세한 내용은 [AKS 클러스터 업그레이드][aks-upgrade-cluster]를 참조하세요.

## <a name="network-security"></a>네트워크 보안

온-프레미스 네트워크와의 연결 및 보안을 위해 AKS 클러스터를 기존 Azure 가상 네트워크 서브넷에 배포할 수 있습니다. 이러한 가상 네트워크는 Azure 사이트 간 VPN 또는 Express 경로를 사용하여 온-프레미스 네트워크에 다시 연결됩니다. 서비스 액세스를 내부 네트워크 연결로 제한하도록 개인, 내부 IP 주소를 사용하여 Kubernetes 수신 컨트롤러를 정의합니다.

### <a name="azure-network-security-groups"></a>Azure 네트워크 보안 그룹

가상 네트워크 트래픽 흐름을 필터링하기 위해 Azure는 네트워크 보안 그룹 규칙을 사용합니다. 이러한 규칙은 리소스에 대한 액세스가 허용되거나 거부된 원본 및 대상 IP 범위, 포트 및 프로토콜을 정의합니다. 기본 규칙은 Kubernetes API 서버에 대한 TLS 트래픽을 허용하도록 생성됩니다. 부하 분산 장치, 포트 매핑 또는 수신 경로를 사용하여 서비스를 만듭니다. AKS는 트래픽 흐름에 대한 네트워크 보안 그룹을 자동으로 수정합니다.

AKS 클러스터에 고유한 서브넷을 제공하는 경우(Azure CNI 또는 Kubenet 사용 여부와 관계없이) AKS에서 관리하는 NIC 수준 네트워크 보안 그룹을 수정하지 **마세요**. 대신 트래픽 흐름을 수정하는 서브넷 수준 네트워크 보안 그룹을 더 많이 만듭니다. 부하 분산 장치 액세스, 컨트롤 플레인과의 통신, [송신][aks-limit-egress-traffic] 등 클러스터를 관리하는 데 필요한 트래픽을 방해하지 않는지 확인합니다.

### <a name="kubernetes-network-policy"></a>Kubernetes 네트워크 정책

클러스터의 Pod 간 네트워크 트래픽을 제한하기 위해 AKS는 [Kubernetes 네트워크 정책][network-policy]을 지원합니다. 네트워크 정책을 사용하면 네임스페이스 및 레이블 선택기에 따라 클러스터 내의 특정 네트워크 경로를 허용하거나 거부할 수 있습니다.

## <a name="kubernetes-secrets"></a>Kubernetes 비밀

Kubernetes ‘비밀’을 사용하여 액세스 자격 증명이나 키와 같은 중요한 데이터를 Pod에 삽입합니다. 
1. Kubernetes API를 사용하여 비밀을 만듭니다. 
1. Pod 또는 배포를 정의하고 특정 비밀을 요청합니다. 
    * 비밀은 비밀을 요구하는 예약된 pod가 있는 노드에만 제공됩니다.
    * 비밀은 디스크에 기록되지 않고 *tmpfs* 에 저장됩니다. 
1. 비밀을 요구하는 노드에서 마지막 Pod를 삭제하면 해당 비밀이 노드의 tmpfs에서 삭제됩니다. 
   * 비밀은 지정된 네임스페이스 내에 저장되며 동일한 네임스페이스 내의 Pod에서만 액세스할 수 있습니다.

비밀을 사용하면 Pod 또는 서비스 YAML 매니페스트에 정의되는 중요한 정보가 줄어듭니다. 대신 Kubernetes API 서버에 저장된 비밀을 YAML 매니페스트의 일부로 요청합니다. 이 방법은 비밀에 대한 특정 Pod 액세스만 제공합니다. 

> [!NOTE]
> 원시 비밀 매니페스트 파일에는 base64 형식의 비밀 데이터가 포함되어 있습니다(자세한 내용은 [공식 설명서][secret-risks] 참조). 이러한 파일은 중요한 정보로 처리해야 하며 소스 제어로 커밋해서는 안 됩니다.

Kubernetes 비밀은 분산 키-값 저장소인 etcd에 저장됩니다. etcd 저장소는 AKS에서 완전히 관리되며 [데이터는 Azure 플랫폼 내에서 미사용 시 암호화][encryption-atrest]됩니다. 

## <a name="next-steps"></a>다음 단계

AKS 클러스터의 보안을 유지하려면 [AKS 클러스터 업그레이드][aks-upgrade-cluster]를 참조하세요.

관련 모범 사례는 [AKS의 클러스터 보안 및 업그레이드 모범 사례][operator-best-practices-cluster-security] 및 [AKS의 Pod 보안 모범 사례][developer-best-practices-pod-security]를 참조하세요.

핵심 Kubernetes 및 AKS 개념에 대한 자세한 내용은 다음을 참조하세요.

- [Kubernetes/AKS 클러스터 및 워크로드][aks-concepts-clusters-workloads]
- [Kubernetes/AKS ID][aks-concepts-identity]
- [Kubernetes/AKS 가상 네트워크][aks-concepts-network]
- [Kubernetes/AKS 스토리지][aks-concepts-storage]
- [Kubernetes/AKS 크기 조정][aks-concepts-scale]

<!-- LINKS - External -->
[kured]: https://github.com/weaveworks/kured
[kubernetes-network-policies]: https://kubernetes.io/docs/concepts/services-networking/network-policies/
[secret-risks]: https://kubernetes.io/docs/concepts/configuration/secret/#risks
[encryption-atrest]: ../security/fundamentals/encryption-atrest.md

<!-- LINKS - Internal -->
[aks-daemonsets]: concepts-clusters-workloads.md#daemonsets
[aks-upgrade-cluster]: upgrade-cluster.md
[aks-aad]: ./managed-aad.md
[aks-add-np-containerd]: windows-container-cli.md#add-a-windows-server-node-pool-with-containerd-preview
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-identity]: concepts-identity.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-network]: concepts-network.md
[aks-kured]: node-updates-kured.md
[aks-limit-egress-traffic]: limit-egress-traffic.md
[cluster-isolation]: operator-best-practices-cluster-isolation.md
[operator-best-practices-cluster-security]: operator-best-practices-cluster-security.md
[developer-best-practices-pod-security]:developer-best-practices-pod-security.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[authorized-ip-ranges]: api-server-authorized-ip-ranges.md
[private-clusters]: private-clusters.md
[network-policy]: use-network-policies.md
[node-image-upgrade]: node-image-upgrade.md
