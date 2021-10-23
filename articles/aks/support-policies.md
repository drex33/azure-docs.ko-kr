---
title: AKS(Azure Kubernetes Service)에 대한 지원 정책
description: AKS(Azure Kubernetes Service) 지원 정책, 공유 책임 및 미리 보기(또는 알파 또는 베타) 상태인 기능에 대해 자세히 알아봅니다.
services: container-service
ms.topic: article
ms.date: 09/18/2020
ms.openlocfilehash: 759d2714f755c39e0b189d7578e90cd268cd53c5
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130222460"
---
# <a name="support-policies-for-azure-kubernetes-service"></a>Azure Kubernetes Service에 대한 지원 정책

이 문서에서는 AKS(Azure Kubernetes Service)에 대한 기술 지원 정책 및 제한 사항에 대해 자세히 설명합니다. 또한 이 문서에서는 에이전트 노드 관리, 관리 컨트롤 플레인 구성 요소, 타사 오픈 소스 구성 요소 및 보안 또는 패치 관리에 대해 자세히 설명합니다.

## <a name="service-updates-and-releases"></a>서비스 업데이트 및 릴리스

* 릴리스 정보는 [AKS 릴리스 정보](https://github.com/Azure/AKS/releases)를 참조하세요.
* 미리 보기의 기능에 대한 자세한 내용은 [AKS 로드맵](https://github.com/Azure/AKS/projects/1)을 참조하세요.

## <a name="managed-features-in-aks"></a>AKS의 관리 기능

기본 IaaS(Infrastructure as a Service) 클라우드 구성 요소(예: 컴퓨팅 또는 네트워킹 구성 요소)는 사용자에게 낮은 수준의 컨트롤과 사용자 지정 옵션에 대한 액세스를 허용합니다. 이와 대조적으로 AKS는 클러스터에 필요한 구성 및 기능의 일반적인 세트를 사용자에게 제공하는 턴키 Kubernetes 배포를 제공합니다. AKS 사용자는 제한된 사용자 지정 및 배포 옵션을 사용할 수 있습니다. 그 대신 Kubernetes 클러스터에 대해 걱정하거나 직접 관리할 필요가 없습니다.

AKS로 사용자는 완전 관리형 *컨트롤 플레인* 을 사용할 수 있습니다. 컨트롤 플레인은 사용자가 운영하는 데 필요한 모든 구성 요소 및 서비스를 포함하고 Kubernetes 클러스터를 최종 사용자에게 제공합니다. 모든 Kubernetes 구성 요소는 Microsoft에서 유지 관리하고 운영합니다.

Microsoft는 제어 창에서 다음 구성 요소를 관리하고 모니터링합니다.

* Kubelet 또는 Kubernetes API 서버
* QoS(서비스 품질), 확장성 및 런타임을 제공하는 etcd 또는 호환되는 키-값 저장소
* DNS 서비스(예: kube-dns 또는 CoreDNS)
* Kubernetes 프록시 또는 네트워킹
* kube-system 네임스페이스에서 실행되는 부가적인 추가 기능 또는 시스템 구성 요소

AKS는 PaaS(Platform-as-a-Service) 솔루션이 아닙니다. 에이전트 노드와 같은 일부 구성 요소에는 사용자가 AKS 클러스터를 유지 관리하는 데 도움을 주어야 하는 *공유 책임* 이 있습니다. 예를 들어, 에이전트 노드 OS(운영 체제) 보안 패치를 적용하는 데 사용자 입력이 필요합니다.

Microsoft와 AKS 팀이 서비스 가용성 및 기능을 배포하고 운영하고 이에 대한 책임을 진다는 점에서 서비스는 ‘관리형’입니다. 고객은 이러한 관리형 구성 요소를 변경할 수 없습니다. Microsoft는 일관되고 확장성 있는 사용자 환경을 보장하기 위해 사용자 지정을 제한합니다. 완전히 사용자 지정 가능한 솔루션은 [AKS 엔진](https://github.com/Azure/aks-engine)을 참조하세요.

## <a name="shared-responsibility"></a>공동 책임

클러스터가 생성되면 사용자는 AKS에서 만든 Kubernetes 에이전트 노드를 정의합니다. 이러한 노드에서 사용자 워크로드가 실행됩니다.

에이전트 노드는 프라이빗 코드를 실행하고 중요한 데이터를 저장하기 때문에 Microsoft 지원에서도 매우 제한적으로만 액세스할 수 있습니다. Microsoft 지원은 사용자의 명시적인 허가 또는 도움 없이 이러한 노드에 로그인하거나 명령을 실행하거나 로그를 볼 수 없습니다.

IaaS API를 사용하여 에이전트 노드를 직접 수정하면 클러스터가 지원을 받지 못하게 될 수 있습니다. 에이전트 노드를 수정하는 경우 `Daemon Sets` 같은 kubernetes 기반 메커니즘을 사용해야 합니다.

마찬가지로, 클러스터 및 노드에 태그 및 레이블과 같은 메타데이터를 추가할 수 있지만 시스템에서 만든 메타데이터를 변경하면 클러스터가 지원을 받지 못하게 될 수 있습니다.

## <a name="aks-support-coverage"></a>AKS 지원 범위

Microsoft는 다음과 같은 사례에 대한 기술 지원을 제공합니다.

* Kubernetes 서비스가 제공하고 지원하는 모든 Kubernetes 구성 요소(예 API 서버)에 대한 연결
* Kubernetes 컨트롤 플레인 서비스(예: Kubernetes 컨트롤 플레인, API 서버, etcd 및 coreDNS)의 관리, 작동 시간, QoS 및 작업
* etcd 데이터 저장소. 지원에는 재해 계획 및 클러스터 상태 복원에 대해 30분마다 모든 etcd 데이터의 자동화된 투명 백업이 포함됩니다. 이러한 백업은 고객이나 사용자에게 직접 제공되지 않습니다. 데이터 안정성 및 일관성을 보장합니다. Etcd. 주문형 롤백 또는 복원은 기능으로 지원되지 않습니다.
* Kubernetes용 Azure 클라우드 공급자 드라이버의 모든 통합 요소. 여기에는 부하 분산 장치, 일관된 볼륨 또는 네트워킹(Kubernetes 및 Azure CNI)과 같은 다른 Azure 서비스에 대한 통합이 포함됩니다.
* Kubernetes API 서버, etcd, coreDNS 등 컨트롤 플레인 구성 요소의 사용자 지정에 대한 질문이나 문제
* 네트워킹(예: Azure CNI, kubenet 또는 기타 네트워크 액세스)에 관한 문제 및 기능 문제 문제에는 DNS 확인, 패킷 손실, 라우팅 등이 포함될 수 있습니다. Microsoft는 다음과 같은 다양한 네트워킹 시나리오를 지원합니다.
  * 관리형 VNET 또는 사용자 지정(사용자 고유의) 서브넷을 사용하는 Kubenet 및 Azure CNI
  * 다른 Azure 서비스 및 애플리케이션에 대한 연결
  * 수신 컨트롤러 및 수신 또는 부하 분산 장치 구성
  * 네트워크 성능 및 대기 시간
  * [네트워크 정책](use-network-policies.md#differences-between-azure-and-calico-policies-and-their-capabilities)


> [!NOTE]
> Microsoft/AKS에서 수행하는 모든 클러스터 작업은 기본 제공되는 Kubernetes 역할 `aks-service`및 기본 제공 역할 바인딩`aks-service-rolebinding` 하에서 사용자 동의에 따라 수행됩니다. 이 역할을 통해 AKS는 클러스터 문제를 해결하고 진단할 수 있지만, 권한을 수정하거나 역할 또는 역할 바인딩, 혹은 기타 높은 권한 작업을 생성할 수 없습니다. 역할 액세스는 JIT(Just-In-Time) 액세스를 사용하는 활성 지원 티켓에서만 허용됩니다.

Microsoft는 다음 사례에 대한 기술 지원을 제공하지 않습니다.

* Kubernetes 사용 방법에 대한 질문. 예를 들어 Microsoft 지원은 사용자 지정 수신 컨트롤러를 만들거나, 애플리케이션 워크로드를 사용하거나, 타사 또는 오픈 소스 소프트웨어 패키지 또는 도구를 적용하는 방법에 대한 조언을 제공하지 않습니다.
  > [!NOTE]
  > Microsoft 지원은 AKS 클러스터 기능, 사용자 지정 및 튜닝(예: Kubernetes 작업 문제 및 절차)에 대해 조언할 수 있습니다.
* Kubernetes 컨트롤 플레인의 일부로 제공되거나 AKS 클러스터와 함께 배포되지 않은 타사 오픈 소스 프로젝트. 이러한 프로젝트에는 Istio, Helm, Envoy 등이 포함될 수 있습니다.
  > [!NOTE]
  > Microsoft는 Helm과 같은 타사 오픈 소스 프로젝트에 대한 지원을 제공하도록 최상의 노력을 기울일 수 있습니다. 타사 오픈 소스 도구가 Kubernetes Azure 클라우드 공급자 또는 다른 AKS 관련 버그와 통합되는 경우 microsoft는 Microsoft 설명서의 예제 및 애플리케이션을 지원합니다.
* 타사의 비공개 소스 소프트웨어. 이 소프트웨어에는 보안 검사 도구와 네트워킹 디바이스 또는 소프트웨어가 포함될 수 있습니다.
* [AKS 설명서](./index.yml)에 나열된 것 이외의 네트워크 사용자 지정.


## <a name="aks-support-coverage-for-agent-nodes"></a>에이전트 노드에 대한 AKS 지원 범위

### <a name="microsoft-responsibilities-for-aks-agent-nodes"></a>AKS 에이전트 노드에 대한 Microsoft 책임

Microsoft와 사용자는 다음과 같은 Kubernetes 에이전트 노드에 대한 책임을 공유합니다.

* 기본 OS 이미지에는 모니터링 및 네트워킹 에이전트와 같은 추가 요구 사항이 있습니다.
* 에이전트 노드는 자동으로 OS 패치를 수신합니다.
* 에이전트 노드에서 실행되는 Kubernetes 컨트롤 플레인 구성 요소와 관련된 문제는 자동으로 수정됩니다. 이러한 구성 요소에는 다음이 포함됩니다.
  * `Kube-proxy`
  * Kubernetes 마스터 구성 요소에 대한 통신 경로를 제공하는 네트워킹 터널
  * `Kubelet`
  * Docker 또는 `containerd`

> [!NOTE]
> 에이전트 노드가 작동하지 않는 경우 AKS는 개별 구성 요소나 전체 에이전트 노드를 다시 시작할 수 있습니다. 이러한 다시 시작 작업은 자동으로 이루어지며 일반적인 문제를 자동으로 수정합니다. 자동 수정 메커니즘에 대해 자세히 알아보려면 [노드 자동 복구](node-auto-repair.md)를 참조하세요.

### <a name="customer-responsibilities-for-aks-agent-nodes"></a>AKS 에이전트 노드에 대한 고객 책임

Microsoft는 이미지 노드에 대한 패치 및 새 이미지를 기본적으로 제공하지만 기본적으로 패치를 자동으로 적용하지는 않습니다. 에이전트 노드 OS 및 런타임 구성 요소에 패치가 계속 적용되도록 하려면 정기적인 [노드 이미지 업그레이드](node-image-upgrade.md) 일정을 유지하거나 자동화해야 합니다.

마찬가지로 AKS는 새로운 kubernetes 패치와 부 버전을 정기적으로 릴리스합니다. 이러한 업데이트에는 Kubernetes에 대한 보안 또는 기능 향상이 포함될 수 있습니다. 사용자는 클러스터의 kubernetes 버전을 계속 업데이트하고 [AKS Kubernetes 지원 버전 정책](supported-kubernetes-versions.md)을 따를 책임이 있습니다.

#### <a name="user-customization-of-agent-nodes"></a>에이전트 노드 사용자 지정
> [!NOTE]
> AKS 에이전트 노드는 Azure Portal에서 일반 Azure IaaS 리소스로 표시됩니다. 그러나 이러한 가상 머신은 사용자 지정 Azure 리소스 그룹(일반적으로 MC\* 접두사 포함)에 배포됩니다. IaaS API 또는 리소스를 사용하여 기본 OS 이미지를 변경하거나 이러한 노드에 직접 사용자 지정을 수행할 수는 없습니다. AKS API를 사용하지 않고 적용된 모든 사용자 지정 변경 내용은 업그레이드, 스케일링, 업데이트 또는 다시 부팅 후 지속되지 않습니다. Microsoft 지원팀에서 변경 작업을 지시하지 않는 한, 에이전트 노드에 대한 변경 작업을 수행하지 마세요.

AKS는 사용자를 대신하여 에이전트 노드의 수명 주기 및 작업을 관리합니다. 에이전트 노드와 연결된 IaaS 리소스에 대한 수정은 **지원되지 않습니다**. 지원되지 않는 작업의 예는 가상 머신 확장 집합 포털 또는 API를 통해 수동으로 구성을 변경하여 노드 풀 가상 머신 확장 집합을 사용자 지정하는 것입니다.
 
워크로드 관련 구성 또는 패키지의 경우 AKS는 [Kubernetes `daemon sets`](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/)를 사용할 것을 권장합니다.

Kubernetes 전용 `daemon sets` 및 init 컨테이너를 사용하면 사용자가 클러스터 에이전트 노드에서 타사 소프트웨어를 튜닝/수정하거나 설치할 수 있습니다. 이러한 사용자 지정의 예로는 사용자 지정 보안 검사 소프트웨어 추가 또는 sysctl 설정 업데이트가 포함됩니다.

위의 요구 사항이 적용되는 경우에는 권장되는 경로이지만, AKS 엔지니어링 및 지원에서는 고객이 배포한 `daemon set`으로 인해 노드가 사용할 수 없게 된 경우에 대한 문제 해결 또는 진단을 지원할 수 없습니다.

### <a name="security-issues-and-patching"></a>보안 문제 및 패치

AKS의 관리되는 구성 요소 중 하나 이상에 보안 결함이 발견되면 AKS 팀은 영향을 받는 모든 클러스터에 패치를 적용하여 문제를 완화합니다. 또는 팀에서 사용자에게 업그레이드 지침을 제공합니다.

보안 결함의 영향을 받는 에이전트 노드의 경우 Microsoft는 그러한 영향에 대한 세부 정보와 보안 문제를 해결 또는 완화하기 위한 조치(일반적으로 노드 이미지 업그레이드 또는 클러스터 패치 업그레이드)를 사용자에게 알려 줍니다.

### <a name="node-maintenance-and-access"></a>노드 유지 관리 및 액세스

사용자가 에이전트 노드에 로그인하고 이를 변경할 수는 있지만 변경으로 인해 클러스터가 지원 불가능해질 수 있으므로 이 작업은 수행하지 않는 것이 좋습니다.

## <a name="network-ports-access-and-nsgs"></a>네트워크 포트, 액세스 및 NSG

사용자 지정 서브넷에서만 NSG를 사용자 지정할 수 있습니다. 관리되는 서브넷의 NSG 또는 에이전트 노드의 NIC 수준에 있는 NSG는 사용자 지정할 수 없습니다. AKS는 특정 엔드포인트에 대한 송신 요구 사항이 있으며, 송신을 제어하고 필요한 연결을 보장하려면 [송신 트래픽 제한](limit-egress-traffic.md)을 참조하세요. 수신의 경우 요구 사항은 클러스터에 배포한 애플리케이션을 기반으로 합니다.

## <a name="stopped-or-de-allocated-clusters"></a>중지 또는 할당 취소된 클러스터

앞서 설명한 것처럼 IaaS API/CLI/포털을 통해 모든 클러스터 노드를 수동으로 할당 취소하면 클러스터가 지원을 받지 못하게 됩니다. 모든 노드를 중지/할당 취소할 때 지원되는 유일한 방법은 [AKS 클러스터를 중지](start-stop-cluster.md#stop-an-aks-cluster)하는 것입니다. 그러면 최대 12개월 동안 클러스터 상태가 유지됩니다.

12개월 넘게 중지된 클러스터는 더 이상 상태를 유지하지 않습니다. 

AKS API 외부에서 할당 취소된 클러스터는 상태 유지가 보장되지 않습니다. 이 상태의 클러스터에 대한 컨트롤 플레인은 30일 후에 보관되고 12개월 후에 삭제됩니다.

AKS는 30일 및 그 이상의 연장된 기간 동안 지원 지침과 다르게 구성된 컨트롤 플레인을 보관할 권리를 보유합니다. AKS는 클러스터 etcd 메타데이터의 백업을 유지 관리하고 클러스터를 언제든 다시 할당할 수 있습니다. 이 재할당은 클러스터를 다시 지원(예: 활성 에이전트 노드에 대한 업그레이드 또는 스케일링) 상태로 돌리는 PUT 작업에 의해 시작될 수 있습니다.

구독이 일시 중단되거나 삭제되면 클러스터의 컨트롤 플레인과 상태가 90일 후에 삭제됩니다.

## <a name="unsupported-alpha-and-beta-kubernetes-features"></a>지원되지 않는 알파 및 베타 Kubernetes 기능

AKS는 업스트림 Kubernetes 프로젝트 내에서 안정적인 기능과 베타 기능만 지원합니다. 다른 설명이 없는 한 AKS는 업스트림 Kubernetes 프로젝트에서 사용할 수 있는 알파 기능을 지원하지 않습니다.

## <a name="preview-features-or-feature-flags"></a>미리 보기 기능 또는 기능 플래그

확장된 테스트 및 사용자 피드백이 필요한 기능에 대해 Microsoft는 새로운 미리 보기 기능 또는 기능 플래그 뒤의 기능을 릴리스합니다. 이러한 기능을 시험판 또는 베타 기능으로 간주합니다.

미리 보기 기능 또는 기능 플래그 기능은 프로덕션용이 아닙니다. API 및 동작의 지속적인 변경, 버그 수정 및 기타 변경 사항은 클러스터 및 가동 중지 시간을 불안정하게 만들 수 있습니다.

공개 미리 보기 상태의 기능은 해당 기능이 미리 보기 상태이고 프로덕션용이 아니기 때문에 ‘최선의 노력을 기울이는’ 지원에 속하며 업무 시간에만 AKS 기술 지원 팀에서 지원합니다. 자세한 내용은 다음을 참조하세요.

* [Azure 지원 FAQ](https://azure.microsoft.com/support/faq/)

## <a name="upstream-bugs-and-issues"></a>업스트림 버그 및 문제

업스트림 Kubernetes 프로젝트에서는 개발 속도 때문에 버그가 항상 발생합니다. 이러한 버그 중 일부는 AKS 시스템 내에서 패치되거나 해결되지 못할 수 있습니다. 대신 버그 수정에는 업스트림 프로젝트(예: Kubernetes, 노드 또는 에이전트 운영 체제 및 커널)에 대한 더 큰 패치가 필요합니다. Microsoft에서 소유하는 구성 요소(예: Azure 클라우드 공급자)의 경우 AKS 및 Azure 담당자는 커뮤니티에서 업스트림 문제를 해결하기 위해 최선을 다하고 있습니다.

하나 이상의 업스트림 버그가 기술 지원 문제에 대한 근본 원인이 되는 경우 AKS 지원 및 엔지니어링 팀은 다음을 수행합니다.

* 업스트림 버그를 식별하고 이 문제가 클러스터 또는 워크로드에 영향을 주는 이유를 설명하는 뒷받침되는 세부 정보를 연결합니다. 고객은 필요한 리포지토리에 대한 링크를 받아서 문제를 감시하고 새 릴리스가 픽스를 제공할 시기를 확인할 수 있습니다.
* 잠재적 해결 방법 또는 완화 방법을 제공합니다. 문제를 완화할 수 있는 경우 [알려진 문제](https://github.com/Azure/AKS/issues?q=is%3Aissue+is%3Aopen+label%3Aknown-issue)는 AKS 리포지토리에 기록됩니다. 알려진 문제 기록은 다음을 설명합니다.
  * 업스트림 버그에 대한 링크를 포함한 문제
  * 업그레이드 또는 솔루션의 다른 지속성에 대한 해결 방법 및 세부 정보
  * 업스트림 릴리스 흐름을 기준으로 할 때 문제가 포함되는 대략적인 타임라인
