---
title: AKS(Azure Kubernetes Service)에 대한 질문과 대답
description: AKS(Azure Kubernetes Service)에 대한 일반적인 질문에 대한 답변을 찾아보세요.
ms.topic: conceptual
ms.date: 05/23/2021
ms.custom: references_regions
ms.openlocfilehash: fdccee2795a4e1b2c967c53dc17d15a6520f4402
ms.sourcegitcommit: 57b7356981803f933cbf75e2d5285db73383947f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2021
ms.locfileid: "129546523"
---
# <a name="frequently-asked-questions-about-azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Service)에 대한 질문과 대답

이 문서에서는 AKS(Azure Kubernetes Service)에 대한 질문과 대답을 제공합니다.

## <a name="which-azure-regions-currently-provide-aks"></a>현재 어떤 Azure 지역에서 AKS를 제공하나요?

사용 가능한 지역의 전체 목록은 [AKS 지역 및 가용성][aks-regions]을 참조하세요.

## <a name="can-i-spread-an-aks-cluster-across-regions"></a>여러 지역에 AKS 클러스터를 분산할 수 있나요?

아니요. AKS 클러스터는 지역 리소스로, 여러 지역에 걸쳐 있을 수 없습니다. 여러 영역을 포함하는 아키텍처를 만드는 방법에 대한 지침은 [비즈니스 연속성 및 재해 복구에 대한 모범 사례][bcdr-bestpractices]를 참조하세요.

## <a name="can-i-spread-an-aks-cluster-across-availability-zones"></a>여러 가용성 영역에 AKS 클러스터를 분산할 수 있나요?

예. [지원하는 지역][az-regions]에 있는 하나 이상의 [가용성 영역][availability-zones]에서 AKS 클러스터를 배포할 수 있습니다.

## <a name="can-i-limit-who-has-access-to-the-kubernetes-api-server"></a>Kubernetes API 서버에 액세스할 수 있는 사용자를 제한할 수 있나요?

예. API 서버에 대한 액세스를 제한하는 옵션에는 다음 두 가지가 있습니다.

- API 서버에 대한 퍼블릭 엔드포인트를 유지하지만 신뢰할 수 있는 IP 범위 세트로 액세스를 제한하려는 경우 [API Server 권한 있는 IP 범위][api-server-authorized-ip-ranges]를 사용합니다.
- API 서버를 가상 네트워크 내에서 *만* 액세스할 수 있도록 제한하려면 [프라이빗 클러스터][private-clusters]를 사용합니다.

## <a name="can-i-have-different-vm-sizes-in-a-single-cluster"></a>단일 클러스터에서 다른 크기의 VM을 사용할 수 있나요?

예, [여러 노드 풀][multi-node-pools]을 만들어 AKS 클러스터에서 다른 크기의 가상 머신을 사용할 수 있습니다.

## <a name="are-security-updates-applied-to-aks-agent-nodes"></a>보안 업데이트가 AKS 에이전트 노드에 적용되나요?

Azure는 자동으로 야간 일정에 따라 클러스터의 Linux 노드에 보안 패치를 적용합니다. 그러나 필요에 따라 해당 Linux 노드를 다시 부팅해야 합니다. 노드를 다시 부팅하기 위한 몇 가지 옵션은 다음과 같습니다.

- Azure Portal 또는 Azure CLI를 통해 수동으로.
- AKS 클러스터를 업그레이드하여. 클러스터 업그레이드는 자동으로 [노드를 차단 및 드레이닝][cordon-drain]한 후 최신 Ubuntu 이미지와 새 패치 버전 또는 부 Kubernetes 버전을 사용하여 새 노드를 다시 온라인 상태로 전환합니다. 자세한 내용은 [AKS 클러스터 업그레이드][aks-upgrade]를 참조하세요.
- [노드 이미지 업그레이드](node-image-upgrade.md)를 사용합니다.

### <a name="windows-server-nodes"></a>Windows Server 노드

Windows Server 노드의 경우 Windows 업데이트가 자동으로 실행되고 최신 업데이트를 적용하지 않습니다. Windows 업데이트 릴리스 주기와 사용자 고유의 유효성 검사 프로세스에 대한 정기적인 일정에 따라, 클러스터에서 업그레이드를 수행하고 AKS 클러스터에서 Windows Server 노드 풀을 업그레이드해야 합니다. 이 업그레이드 프로세스는 최신 Windows Server 이미지 및 패치를 실행하는 노드를 만든 다음, 이전 노드를 제거합니다. 이 프로세스에 대한 자세한 내용은 [AKS에서 노드 풀 업그레이드][nodepool-upgrade]를 참조하세요.

### <a name="are-there-additional-security-threats-relevant-to-aks-that-customers-should-be-aware-of"></a>고객이 알고 있어야 하는 AKS와 관련된 추가 보안 위협이 있나요?

Microsoft는 [Azure Security Center](https://azure.microsoft.com/services/security-center/)와 같은 서비스를 통해 워크로드를 보호하기 위해 수행할 수 있는 추가 작업에 대한 지침을 제공합니다. 다음은 고객이 알고 있어야 하는 AKS 및 Kubernetes와 관련된 추가 보안 위협 목록입니다.

* [새 대규모 캠페인 대상 Kubeflow](https://techcommunity.microsoft.com/t5/azure-security-center/new-large-scale-campaign-targets-kubeflow/ba-p/2425750) - 2021년 6월 8일

## <a name="why-are-two-resource-groups-created-with-aks"></a>AKS를 통해 2개의 리소스 그룹이 생성되는 이유는 무엇인가요?

AKS는 가상 머신 확장 집합, 가상 네트워크 및 Managed Disks를 포함하는 다양 한 Azure 인프라 리소스를 기준으로 합니다. 따라서 AKS에서 제공하는 관리형 Kubernetes 환경 내에서 Azure 플랫폼의 여러 핵심 기능을 활용할 수 있습니다. 예를 들어, 대부분의 Azure Virtual Machine 유형은 AKS에서 직접 사용할 수 있으며, Azure Reservations를 사용하여 이러한 리소스에 대해 자동으로 할인 혜택을 받을 수 있습니다.

이 아키텍처를 지원하기 위해 각 AKS 배포는 다음 두 리소스 그룹에 걸쳐 있습니다.

1. 첫 번째 리소스 그룹을 만듭니다. 이 그룹에는 Kubernetes 서비스 리소스만 포함됩니다. AKS 리소스 공급자는 배포하는 동안 두 번째 리소스 그룹을 자동으로 만듭니다. 두 번째 리소스 그룹의 예는 *MC_myResourceGroup_myAKSCluster_eastus* 입니다. 이 두 번째 리소스 그룹의 이름을 지정하는 방법에 대한 자세한 내용은 다음 섹션을 참조하세요.
1. *노드 리소스 그룹* 으로 알려져 있는 두 번째 리소스 그룹에는 클러스터와 연결된 인프라 리소스의 모든 항목이 포함됩니다. 이러한 리소스에는 Kubernetes 노드 VM, 가상 네트워킹 및 스토리지가 포함됩니다. 기본적으로 노드 리소스 그룹에는 *MC_myResourceGroup_myAKSCluster_eastus* 와 같은 이름이 지정됩니다. AKS는 클러스터가 삭제될 때마다 노드 리소스 그룹을 자동으로 삭제하므로, 클러스터의 수명 주기를 공유하는 리소스에만 사용해야 합니다.

## <a name="can-i-provide-my-own-name-for-the-aks-node-resource-group"></a>AKS 노드 리소스 그룹에 고유한 이름을 지정할 수 있나요?

예. 기본적으로 AKS는 노드 리소스 그룹 이름을 *MC_resourcegroupname_clustername_location* 으로 지정하지만 사용자 고유의 이름을 제공할 수도 있습니다.

고유한 리소스 그룹 이름을 지정하려면 [aks-preview][aks-preview-cli] Azure CLI 확장 버전 0.3.2 이상을 설치합니다. [az aks create][az-aks-create] 명령을 사용하여 AKS 클러스터를 만들 때 `--node-resource-group` 매개 변수를 사용하고 리소스 그룹의 이름을 지정합니다. [Azure Resource Manager 템플릿을 사용][aks-rm-template]하여 AKS 클러스터를 배포하는 경우 *nodeResourceGroup* 속성을 사용하여 리소스 그룹 이름을 정의할 수 있습니다.

* 자체 구독에서 Azure 리소스 공급자가 자동으로 보조 리소스 그룹을 만듭니다.
* 클러스터를 만들 때만 사용자 지정 리소스 그룹 이름을 지정할 수 있습니다.

노드 리소스 그룹으로 작업할 때는 다음을 수행할 수 없습니다.

* 노드 리소스 그룹에 기존 리소스 그룹을 지정합니다.
* 노드 리소스 그룹에 다른 구독을 지정합니다.
* 클러스터를 만든 후 노드 리소스 그룹 이름을 변경합니다.
* 노드 리소스 그룹 내에서 관리 리소스의 이름을 지정합니다.
* 노드 리소스 그룹 내에서 관리 리소스의 Azure에서 만든 태그를 수정하거나 삭제합니다. 다음 섹션에서 추가 정보를 참조하세요.

## <a name="can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-node-resource-group"></a>노드 리소스 그룹에서 태그 및 AKS 리소스의 다른 속성을 수정할 수 있나요?

노드 리소스 그룹에서 Azure에서 만든 태그 및 기타 리소스 속성을 수정하거나 삭제하는 경우 크기 조정 및 업그레이드 오류와 같은 예기치 않은 결과가 발생할 수 있습니다. AKS를 사용하면 최종 사용자가 만든 사용자 지정 태그를 만들고 수정할 수 있으며 [노드 풀을 만들 때](use-multiple-node-pools.md#specify-a-taint-label-or-tag-for-a-node-pool) 이러한 태그를 추가할 수 있습니다. 예를 들어 사업부 또는 비용 센터를 할당하기 위해 사용자 지정 태그를 만들거나 수정할 수 있습니다. 이는 관리되는 리소스 그룹의 범위를 사용하여 Azure 정책을 만들어 수행할 수도 있습니다.

그러나 AKS 클러스터의 노드 리소스 그룹에서 리소스에 대해 **Azure로 만든 태그** 를 수정하는 작업은 지원되지 않으며, 이러한 작업은 SLO(서비스 수준 목표)를 중단하게 됩니다. 자세한 내용은 [AKS는 서비스 수준 계약을 제공합니까?](#does-aks-offer-a-service-level-agreement)를 참조하세요.

## <a name="what-kubernetes-admission-controllers-does-aks-support-can-admission-controllers-be-added-or-removed"></a>AKS가 지원하는 Kubernetes 허용 컨트롤러는 무엇인가요? 허용 컨트롤러를 추가하거나 제거할 수 있나요?

AKS는 다음과 같은 [허용 컨트롤러][admission-controllers]를 지원합니다.

- *NamespaceLifecycle*
- *LimitRanger*
- *ServiceAccount*
- *DefaultStorageClass*
- *DefaultTolerationSeconds*
- *MutatingAdmissionWebhook*
- *ValidatingAdmissionWebhook*
- *ResourceQuota*
- *PodNodeSelector*
- *PodTolerationRestriction*
- *ExtendedResourceToleration*

현재, AKS에서 허용 컨트롤러 목록을 수정할 수 없습니다.

## <a name="can-i-use-admission-controller-webhooks-on-aks"></a>AKS에서 허용 컨트롤러 Webhook를 사용할 수 있나요?

예, AKS에서 허용 컨트롤러 Webhook를 사용할 수 있습니다. **제어 평면 레이블** 로 표시된 내부 AKS 네임스페이스를 제외하는 것이 좋습니다. 예를 들어, Webhook 구성에 아래 내용을 추가합니다.

```
namespaceSelector:
    matchExpressions:
    - key: control-plane
      operator: DoesNotExist
```

AKS는 API 서버 송신을 방화벽으로 처리하므로 클러스터 내에서 허용 컨트롤러 웹후크에 액세스할 수 있어야 합니다.

## <a name="can-admission-controller-webhooks-impact-kube-system-and-internal-aks-namespaces"></a>허용 컨트롤러 Webhook가 kube-system 및 내부 AKS 네임스페이스에 영향을 미칠 수 있나요?

시스템의 안정성을 보호하고 사용자 지정 허용 컨트롤러가 kube-system의 내부 서비스에 영향을 주지 않도록 하기 위해 네임스페이스 AKS에는 kube-system 및 AKS 내부 네임스페이스를 자동으로 제외하는 **Admissions Enforcer** 가 있습니다. 이 서비스는 사용자 지정 허용 컨트롤러가 kube-system에서 실행되는 서비스에 영향을 주지 않도록 합니다.

사용자 지정 허용 webhook를 통해 적용해야 하는 기능을 kube-system에 배포하기(권장하지 않음) 위한 중요한 사용 사례가 있는 경우 Admissions Enforcer에서 무시하도록 아래 레이블 또는 주석을 추가할 수 있습니다.

레이블: ```"admissions.enforcer/disabled": "true"``` 또는 주석: ```"admissions.enforcer/disabled": true```

## <a name="is-azure-key-vault-integrated-with-aks"></a>Azure Key Vault는 AKS와 통합되나요?

현재, AKS는 기본적으로 Azure Key Vault와 통합되지 않습니다. 그러나 [CSI 비밀 저장소용 Azure Key Vault 공급자][csi-driver]를 통해 Kubernetes Pod에서 KeyVault 비밀로 직접 통합할 수 있습니다.

## <a name="can-i-run-windows-server-containers-on-aks"></a>Windows Server 컨테이너를 AKS에서 실행할 수 있습니까?

예, Windows Server 컨테이너는 AKS에서 사용할 수 있습니다. AKS에서 Windows Server 컨테이너를 실행하려면 Windows Server를 게스트 OS로 실행하는 노드 풀을 만듭니다. Windows Server 컨테이너는 Windows Server 2019만 사용할 수 있습니다. 시작하려면 [Windows Server 노드 풀을 사용하여 AKS 클러스터 만들기][aks-windows-cli]를 참조하세요.

노드 풀에 대한 Windows Server 지원에는 Kubernetes 프로젝트의 업스트림 Windows Server에 적용되는 몇 가지 제한 사항이 포함됩니다. 이러한 제한 사항에 대한 자세한 내용은 [AKS의 Windows Server 컨테이너 제한 사항][aks-windows-limitations]을 참조하세요.

## <a name="does-aks-offer-a-service-level-agreement"></a>AKS는 서비스 수준 계약을 제공합니까?

AKS는 [작동 시간 SLA][uptime-sla]를 통해 SLA를 선택적 추가 기능으로 제공합니다. 

기본적으로 제공되는 무료 SKU에는 연결된 서비스 수준 계약이 없지만, 99.5%의 서비스 수준 목표가 있습니다. 업그레이드, 비정상 언더레이 노드, 플랫폼 유지 관리, 요청으로 인한 API 서버 과부하가 발생하는 애플리케이션 등의 경우 일시적인 연결 문제가 관측될 수 있습니다. 워크로드가 API 서버 다시 시작을 허용하지 않는 경우 작동 시간 SLA를 사용하는 것이 좋습니다.

## <a name="can-i-apply-azure-reservation-discounts-to-my-aks-agent-nodes"></a>AKS 에이전트 노드에 Azure 예약 할인을 적용할 수 있나요?

AKS 에이전트 노드는 표준 Azure Virtual Machines로 청구되므로 AKS에서 사용하는 VM 크기에 대해 [Azure Reservations][reservation-discounts]를 구매한 경우 해당 할인이 자동으로 적용됩니다.

## <a name="can-i-movemigrate-my-cluster-between-azure-tenants"></a>Azure 테넌트 간에 클러스터를 이동/마이그레이션할 수 있나요?

테넌트 간에 AKS 클러스터를 이동하는 것은 현재 지원되지 않습니다.

## <a name="can-i-movemigrate-my-cluster-between-subscriptions"></a>내 클러스터를 구독 간에 이동/마이그레이션할 수 있나요?

현재 구독 간의 클러스터 이동은 지원되지 않습니다.

## <a name="can-i-move-my-aks-clusters-from-the-current-azure-subscription-to-another"></a>AKS 클러스터를 현재 Azure 구독에서 다른 구독으로 이동할 수 있나요?

AKS 클러스터와 연결된 리소스를 Azure 구독 간에 이동하는 것은 지원되지 않습니다.

## <a name="can-i-move-my-aks-cluster-or-aks-infrastructure-resources-to-other-resource-groups-or-rename-them"></a>AKS 클러스터 또는 AKS 인프라 리소스를 다른 리소스 그룹으로 이동하거나 이름을 바꿀 수 있나요?

AKS 클러스터 및 연결된 리소스를 이동하거나 이름을 바꾸는 것은 지원되지 않습니다.

## <a name="why-is-my-cluster-delete-taking-so-long"></a>클러스터 삭제가 오래 걸리는 이유는 무엇인가요?

사용자 요청 시 대부분의 클러스터가 삭제됩니다. 일부 경우, 특히 고객이 자신의 리소스 그룹을 가져오거나 RG 간 태스크를 수행할 때 삭제에 추가 시간이 발생하거나 삭제가 실패할 수 있습니다. 삭제 시 문제가 발생하면 RG에 잠금이 없는지와 RG 외부의 모든 리소스와 RG의 연결이 끊어져 있는지를 다시 확인합니다.

## <a name="if-i-have-pod--deployments-in-state-nodelost-or-unknown-can-i-still-upgrade-my-cluster"></a>'NodeLost' 또는 '알 수 없음' 상태의 pod/배포가 있는 경우 클러스터를 여전히 업그레이드할 수 있나요?

가능은 하지만 AKS에서는 이러한 업그레이드를 권장하지 않습니다. 클러스터의 상태가 알려져 있고 정상일 때 업그레이드를 수행하는 것이 가장 좋습니다.

## <a name="if-i-have-a-cluster-with-one-or-more-nodes-in-an-unhealthy-state-or-shut-down-can-i-perform-an-upgrade"></a>하나 이상의 노드가 있는 클러스터가 비정상 상태이거나 종료된 경우 업그레이드를 수행할 수 있나요?

아니요. 실패한 상태의 모든 노드를 삭제/제거합니다. 그렇지 않으면 업그레이드 전에 클러스터에서 해당 노드가 제거됩니다.

## <a name="i-ran-a-cluster-delete-but-see-the-error-errno-11001-getaddrinfo-failed"></a>클러스터 삭제를 실행했지만 오류가 표시됩니다. `[Errno 11001] getaddrinfo failed`

가장 일반적으로 이 문제는 하나 이상의 NSG(네트워크 보안 그룹)가 여전히 사용 중이고 클러스터와 연결된 경우에 발생합니다.  이러한 NSG를 제거한 후 다시 시도하세요.

## <a name="i-ran-an-upgrade-but-now-my-pods-are-in-crash-loops-and-readiness-probes-fail"></a>업그레이드를 실행했지만 pod이 충돌 루프에 있습니다. 준비 상태 프로브가 실패하나요?

서비스 주체가 만료되지 않았는지 확인합니다.  참조 항목: [AKS 서비스 주체](./kubernetes-service-principal.md) 및 [AKS 업데이트 자격 증명](./update-credentials.md).

## <a name="my-cluster-was-working-but-suddenly-cant-provision-loadbalancers-mount-pvcs-etc"></a>클러스터가 작동하지만 갑자기 LoadBalancer를 프로비저닝하거나 PVC를 탑재할 수 없습니다.

서비스 주체가 만료되지 않았는지 확인합니다.  참조 항목: [AKS 서비스 주체](./kubernetes-service-principal.md) 및 [AKS 업데이트 자격 증명](./update-credentials.md).

## <a name="can-i-scale-my-aks-cluster-to-zero"></a>AKS 클러스터를 0으로 크기를 조정할 수 있나요?
[실행 중인 AKS 클러스터](start-stop-cluster.md)를 완전히 중지하고 각각의 컴퓨팅 비용을 절감할 수 있습니다. 또한 [모든 또는 특정 `User`노드 풀](scale-cluster.md#scale-user-node-pools-to-0)의 크기를 0으로 조정하거나 크기를 자동으로 조정하여 필요한 클러스터 구성만 유지하도록 선택할 수도 있습니다.
[시스템 노드 풀](use-system-pools.md)을 0으로 직접 조정할 수 없습니다.

## <a name="can-i-use-the-virtual-machine-scale-set-apis-to-scale-manually"></a>가상 머신 확장 집합 API를 사용하여 수동으로 크기를 조정할 수 있나요?

아니요, 가상 머신 확장 집합 API를 사용한 크기 조정 작업은 지원되지 않습니다. AKS API(`az aks scale`)를 사용하세요.

## <a name="can-i-use-virtual-machine-scale-sets-to-manually-scale-to-zero-nodes"></a>가상 머신 확장 집합을 사용하여 크기를 0개 노드로 수동 조정할 수 있나요?

아니요, 가상 머신 확장 집합 API를 사용한 크기 조정 작업은 지원되지 않습니다. AKS API를 사용하여 0개의 비시스템 노드 풀로 확장하거나 클러스터를 대신 [중지](start-stop-cluster.md)할 수 있습니다.

## <a name="can-i-stop-or-de-allocate-all-my-vms"></a>모든 VM을 중지하거나 할당 취소할 수 있나요?

AKS에는 이러한 구성을 견디고 복구할 수 있는 복원 메커니즘이 있지만 지원되는 구성은 아닙니다. 대신 [클러스터를 중지](start-stop-cluster.md)하세요.

## <a name="can-i-use-custom-vm-extensions"></a>사용자 지정 VM 확장을 사용할 수 있나요?

AKS는 관리형 서비스이며 IaaS 리소스 조작은 지원되지 않습니다. 사용자 지정 구성 요소를 설치하려면 Kubernetes API 및 메커니즘을 사용하세요. 예를 들어 DaemonSets를 활용하여 필수 구성 요소를 설치할 수 있습니다.

## <a name="does-aks-store-any-customer-data-outside-of-the-clusters-region"></a>AKS는 고객 데이터를 클러스터 지역 외부에 저장하나요?

단일 지역에 고객 데이터를 저장할 수 있는 기능은 현재 아시아 태평양 지역의 동남 아시아 지역(싱가포르) 및 브라질 지역의 브라질 남부(상파울루 주)에서만 사용할 수 있습니다. 다른 모든 지역의 경우 고객 데이터는 지역에 저장됩니다.

## <a name="are-aks-images-required-to-run-as-root"></a>AKS 이미지가 루트로 실행되어야 하나요?

다음 두 이미지를 제외하면 AKS 이미지를 루트로 실행하지 않아도 됩니다.

- *mcr.microsoft.com/oss/kubernetes/coredns*
- *mcr.microsoft.com/azuremonitor/containerinsights/ciprod*

## <a name="what-is-azure-cni-transparent-mode-vs-bridge-mode"></a>Azure CNI 투명 모드와 브리지 모드는 무엇인가요?

v1.2.0부터 Azure CNI에서는 단일 테넌시 Linux CNI 배포에 대해 기본적으로 투명 모드를 사용할 수 있습니다. 브리지 모드는 투명 모드로 교체되는 중입니다. 이 섹션에서는 두 모드의 차이점 및 Azure CNI에서 투명 모드를 사용하는 경우의 이점/제한 사항에 대해 자세히 설명합니다.

### <a name="bridge-mode"></a>브리지 모드

이름에서 알 수 있듯이, 브리지 모드 Azure CNI는 ‘just in time’ 방식으로 ‘azure0’라는 L2 브리지를 만듭니다. 모든 호스트 측 Pod `veth` 쌍 인터페이스는 이 브리지에 연결됩니다. 따라서 VM 통신 내 Pod-Pod 및 나머지 트래픽은 이 브리지를 통과합니다. 해당 브리지는 하나 이상의 실제 디바이스에 바인딩하지 않는 한 자체적으로 수신하거나 전송할 수 없는 레이어 2 가상 디바이스입니다. 이러한 이유로 Linux VM의 eth0를 ‘azure0’ 브리지에 대한 종속 항목으로 변환해야 합니다. 이로 인해 Linux VM 내에 복잡한 네트워크 토폴로지가 생성되고 CNI가 DNS 서버 업데이트 등과 같은 다른 네트워킹 기능을 처리해야 하는 문제가 발생합니다.

:::image type="content" source="media/faq/bridge-mode.png" alt-text="브리지 모드 토폴로지":::

다음은 브리지 모드에서 IP 경로를 설정하는 방법의 예입니다. 노드에 있는 Pod 수에 관계없이 두 개의 경로만 있을 것입니다. 첫 번째는 azure0의 로컬을 제외한 모든 트래픽이 ip ‘src 10.240.0.4’(노드 기본 IP)를 사용하는 인터페이스를 통해 서브넷의 기본 게이트웨이로 이동하고, 두 번째는 커널이 결정하는 커널에 대해 ‘10.20.x.x’ Pod 공간을 사용하는 것입니다.

```bash
default via 10.240.0.1 dev azure0 proto dhcp src 10.240.0.4 metric 100
10.240.0.0/12 dev azure0 proto kernel scope link src 10.240.0.4
172.17.0.0/16 dev docker0 proto kernel scope link src 172.17.0.1 linkdown
root@k8s-agentpool1-20465682-1:/#
```

### <a name="transparent-mode"></a>투명 모드
투명 모드는 Linux 네트워킹을 설정하는 데 직접적인 접근 방식을 사용합니다. 이 모드에서 Azure CNI는 Linux VM에서 eth0 인터페이스의 속성을 변경하지 않습니다. Linux 네트워킹 속성을 변경하는 최소한의 방식은 클러스터가 브리지 모드에서 직면할 수 있는 복잡한 예상 외의 문제를 줄이는 데 도움이 됩니다. 투명 모드에서 Azure CNI는 호스트 네트워크에 추가될 호스트 측 Pod `veth` 쌍 인터페이스를 만들고 추가합니다. VM Pod-Pod 간 통신은 CNI가 추가할 ip 경로를 지납니다. 기본적으로 Pod-Pod 통신은 레이어 3을 지나며, Pod 트래픽은 L3 회람 규칙에 의해 라우팅됩니다.

:::image type="content" source="media/faq/transparent-mode.png" alt-text="투명 모드 토폴로지":::

다음은 투명 모드의 ip 경로 설정 예입니다. 각 Pod의 인터페이스는 고정 경로를 연결하여 Pod가 대상 IP인 트래픽이 Pod의 호스트 측 `veth` 쌍 인터페이스로 직접 전송되도록 합니다.

```bash
10.240.0.216 dev azv79d05038592 proto static
10.240.0.218 dev azv8184320e2bf proto static
10.240.0.219 dev azvc0339d223b9 proto static
10.240.0.222 dev azv722a6b28449 proto static
10.240.0.223 dev azve7f326f1507 proto static
10.240.0.224 dev azvb3bfccdd75a proto static
168.63.129.16 via 10.240.0.1 dev eth0 proto dhcp src 10.240.0.4 metric 100
169.254.169.254 via 10.240.0.1 dev eth0 proto dhcp src 10.240.0.4 metric 100
172.17.0.0/16 dev docker0 proto kernel scope link src 172.17.0.1 linkdown
```

### <a name="benefits-of-transparent-mode"></a>투명 모드의 이점

- `conntrack` DNS 병렬 경합 상태를 완화하고 노드 로컬 DNS를 설정할 필요 없이 5초 DNS 대기 시간 문제를 방지합니다(성능상의 이유로 노드 로컬 DNS를 계속 사용할 수 있음).
- 초기 5초 DNS 대기 시간 CNI 브리지 모드는 현재 ‘just-in-time’ 브리지 설정으로 인해 도입됩니다.
- 브리지 모드의 코너 케이스 중 하나는 Azure CNI에서 사용자가 VNET 또는 NIC에 추가하는 사용자 지정 DNS 서버 목록을 계속 업데이트할 수 없다는 것입니다. 이로 인해 CNI는 DNS 서버 목록의 첫 번째 인스턴스만 선택합니다. CNI가 eth0 속성을 변경하지 않으면서 투명 모드에서 해결됩니다. [여기](https://github.com/Azure/azure-container-networking/issues/713)에서 자세한 내용을 알아보세요.
- UDP 트래픽의 효과적인 처리 및 ARP 시간이 초과될 때 UDP 플러드 폭풍 완화 기능을 제공합니다. 브리지 모드에서는 브리지가 VM 내 Pod 간 통신에서 대상 Pod의 MAC 주소를 인식하지 못하는 경우 기본적으로 모든 포트에 대한 패킷 폭풍이 발생합니다. 경로에 L2 디바이스가 없으므로 투명 모드로 해결됩니다. [여기](https://github.com/Azure/azure-container-networking/issues/704)에서 자세한 내용을 알아보세요.
- 투명 모드는 브리지 모드와 비교할 때 처리량 및 대기 시간 측면에서 VM Pod 간 통신에 더 나은 성능을 보입니다.

## <a name="how-to-avoid-permission-ownership-setting-slow-issues-when-the-volume-has-a-lot-of-files"></a>볼륨에 많은 파일이 있는 경우 권한 소유권 설정이 느려지는 문제를 방지하는 방법은 무엇인가요?

일반적으로 Pod가 루트가 아닌 사용자로 실행되는 경우(필요한 경우) Pod에서 볼륨을 읽고 쓸 수 있도록 Pod의 보안 컨텍스트 내부에 `fsGroup`을 지정해야 합니다. 이 요구 사항은 [여기](https://kubernetes.io/docs/tasks/configure-pod-container/security-context/)에 자세히 설명되어 있습니다.

그러나 `fsGroup` 설정의 한 가지 부작용은 볼륨이 탑재될 때마다 Kubernetes가 볼륨 내부의 모든 파일과 디렉터리에 대해 재귀적으로 `chown()` 및 `chmod()`를 사용해야 한다는 것입니다. 아래에 몇 가지 예외가 있습니다. 이는 볼륨의 그룹 소유권이 이미 요청된 `fsGroup`과 일치하는 경우에도 발생하며, 작은 파일이 많은 큰 볼륨의 경우 비용이 많이 들 수 있으므로 Pod 시작에 오랜 시간이 걸리게 됩니다. 이 시나리오는 v1.20 이전에 알려진 문제이며 해결 방법은 Pod 실행을 루트로 설정하는 것입니다.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: security-context-demo
spec:
  securityContext:
    runAsUser: 0
    fsGroup: 0
```

해당 문제는 Kubernetes v1.20에서 해결되었습니다. 자세한 내용은 [Kubernetes 1.20: 볼륨 권한 변경의 세부적인 제어](https://kubernetes.io/blog/2020/12/14/kubernetes-release-1.20-fsgroupchangepolicy-fsgrouppolicy/)를 참조하세요.

## <a name="can-i-use-fips-cryptographic-libraries-with-deployments-on-aks"></a>AKS에서 배포에 FIPS 암호화 라이브러리를 사용할 수 있나요?

FIPS 사용 노드는 현재 Linux 기반 노드 풀에서 미리 보기로 제공됩니다. 자세한 내용은 [FIPS 사용 노드 풀 추가(미리 보기)](use-multiple-node-pools.md#add-a-fips-enabled-node-pool-preview)를 참조하세요.

## <a name="can-i-configure-nsgs-with-aks"></a>AKS를 통해 NSG를 구성할 수 있나요?

AKS는 서브넷에 NSGs (네트워크 보안 그룹)를 적용 하지 않으며 해당 서브넷과 연결 된 NSGs를 수정 하지 않습니다. AKS는 NIC 수준에서 NSGs만 수정 합니다. CNI를 사용하는 경우 NSG의 보안 규칙이 노드와 Pod CIDR 범위 간의 트래픽을 허용하는지 확인해야 합니다. kubenet을 사용하는 경우 NSG의 보안 규칙이 노드와 Pod CIDR 간의 트래픽을 허용하는지 확인해야 합니다. 자세한 내용은 [네트워크 보안 그룹](concepts-network.md#network-security-groups)을 참조 하세요.

<!-- LINKS - internal -->

[aks-upgrade]: ./upgrade-cluster.md
[aks-cluster-autoscale]: ./cluster-autoscaler.md
[aks-advanced-networking]: ./configure-azure-cni.md
[aks-rbac-aad]: ./azure-ad-integration-cli.md
[node-updates-kured]: node-updates-kured.md
[aks-preview-cli]: /cli/azure/aks
[az-aks-create]: /cli/azure/aks#az_aks_create
[aks-rm-template]: /azure/templates/microsoft.containerservice/2019-06-01/managedclusters
[aks-cluster-autoscaler]: cluster-autoscaler.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[aks-windows-cli]: windows-container-cli.md
[aks-windows-limitations]: ./windows-faq.md
[reservation-discounts]:../cost-management-billing/reservations/save-compute-costs-reservations.md
[api-server-authorized-ip-ranges]: ./api-server-authorized-ip-ranges.md
[multi-node-pools]: ./use-multiple-node-pools.md
[availability-zones]: ./availability-zones.md
[private-clusters]: ./private-clusters.md
[bcdr-bestpractices]: ./operator-best-practices-multi-region.md#plan-for-multiregion-deployment
[availability-zones]: ./availability-zones.md
[az-regions]: ../availability-zones/az-region.md
[uptime-sla]: ./uptime-sla.md

<!-- LINKS - external -->
[aks-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[auto-scaler]: https://github.com/kubernetes/autoscaler
[cordon-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[admission-controllers]: https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/
[private-clusters-github-issue]: https://github.com/Azure/AKS/issues/948
[csi-driver]: https://github.com/Azure/secrets-store-csi-driver-provider-azure
[vm-sla]: https://azure.microsoft.com/support/legal/sla/virtual-machines/
