---
title: Windows Server 노드 풀 FAQ
titleSuffix: Azure Kubernetes Service
description: AKS(Azure Kubernetes Service)에서 Windows Server 노드 풀 및 애플리케이션 워크로드를 실행할 때 자주 묻는 질문을 참조하세요.
ms.topic: article
ms.date: 10/12/2020
ms.openlocfilehash: dd83803069f83233915c0baae0656346008a3814
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129354636"
---
# <a name="frequently-asked-questions-for-windows-server-node-pools-in-aks"></a>AKS의 Windows Server 노드 풀에 대해 자주 묻는 질문과 대답

AKS(Azure Kubernetes Service)에서는 노드에서 Windows Server를 게스트 OS로 실행하는 노드 풀을 만들 수 있습니다. 이러한 노드는 .NET Framework 빌드된 것과 같은 네이티브 Windows 컨테이너 애플리케이션을 실행할 수 있습니다. Linux 및 Windows OS에서 컨테이너 지원을 제공하는 방법에는 차이점이 있습니다. 일반적인 Linux Kubernetes 및 Pod 관련 기능은 현재 Windows 노드 풀에서 사용할 수 없습니다.

이 문서에서는 AKS의 Windows Server 노드에 대해 자주 묻는 질문과 그 답변 및 OS 개념에 대해 간략하게 설명합니다.

## <a name="which-windows-operating-systems-are-supported"></a>지원되는 Windows 운영 체제는 무엇인가요?

AKS는 Windows Server 2019를 컨테이너 호스트 OS 버전으로 사용하고 프로세스 격리만 지원합니다. 기타 Windows Server 버전을 사용하여 빌드된 컨테이너 이미지는 지원되지 않습니다. 자세한 정보는 [Windows 컨테이너 버전 호환성][windows-container-compat]을 참조하세요.

## <a name="is-kubernetes-different-on-windows-and-linux"></a>Windows와 Linux에서 Kubernetes가 다른가요?

Windows Server 노드 풀 지원에는 Kubernetes 프로젝트의 업스트림 Windows Server에 적용되는 몇 가지 제한 사항이 있습니다. 이러한 제한 사항은 AKS에만 국한되지 않습니다. Kubernetes의 Windows Server에 대한 업스트림 지원에 대한 자세한 정보는 [Kubernetes에서 Windows 지원 소개][intro-windows] 문서의 Kubernetes 프로젝트의 [지원되는 기능 및 제한 사항][upstream-limitations] 섹션을 참조하세요.

Kubernetes는 주로 Linux에 중점을 두고 있습니다. 업스트림 [Kubernetes.io][kubernetes] 웹 사이트에 사용되는 많은 예제는 Linux 노드에서 사용하기 위한 것입니다. Windows Server 컨테이너를 사용하는 배포를 만드는 경우 OS 수준에서 다음 사항을 고려해야 합니다.

- **ID** - Linux는 사용자를 정수 UID(사용자 ID)로 식별합니다. 또한 사용자는 로그온 하기 위한 영숫자 사용자 이름이 있으며, Linux는 이를 사용자의 UID로 변환합니다. 마찬가지로 Linux는 GID(정수 그룹 식별자)로 사용자 그룹을 식별하고 그룹 이름을 해당 GID로 변환합니다.
    - Windows Server는 Windows SAM(보안 액세스 관리자) 데이터베이스에 저장되어 있는 더 큰 SID(이진 보안 식별자)를 사용합니다. 이 데이터베이스는 호스트와 컨테이너 간이나 컨테이너 간에 공유되지 않습니다.
- **파일 사용 권한** - Windows Server는 사용 권한의 비트 마스크 및 UID + GID가 아닌 SID 기반의 액세스 제어 목록을 사용합니다
- **파일 경로** - Windows Server의 규칙은/ 대신 \를 사용합니다.
    - 볼륨을 탑재하는 Pod 사양에서 Windows Server 컨테이너 경로를 올바르게 지정합니다. 예를 들어 Linux 컨테이너에서는 탑재 지점을 */mnt/volume* 대신 드라이브 문자 및 위치로 지정합니다. 예를 들어 */K/Volume* 대신 *K:* 드라이브로 탑재합니다.

## <a name="what-kind-of-disks-are-supported-for-windows"></a>Windows에서 지원되는 디스크 종류는 무엇인가요?

지원되는 볼륨 유형으로는 Azure Disk와 Azure Files가 있습니다. 이들은 Windows Server 컨테이너에서 NTFS 볼륨으로 액세스됩니다.

## <a name="can-i-run-windows-only-clusters-in-aks"></a>AKS에서 Windows 전용 클러스터를 실행할 수 있나요?

AKS 클러스터의 마스터 노드(컨트롤 플레인)는 AKS 서비스에서 호스트되므로 마스터 구성 요소를 호스트하는 노드의 운영 체제에 노출되지 않습니다. 모든 AKS 클러스터는 기본 첫 번째 노드 풀로 만들어지며, Linux 기반입니다. 이 노드 풀에는 클러스터가 작동하는 데 필요한 시스템 서비스가 포함되어 있습니다. 클러스터의 안정성 및 클러스터 작업을 수행하는 기능을 보장하기 위해 첫 번째 노드 풀에서 두 개 이상의 노드를 실행하는 것을 권장합니다. AKS 클러스터 자체를 삭제하지 않는 한 첫 번째 Linux 기반 노드 풀을 삭제할 수 없습니다.

## <a name="how-do-i-patch-my-windows-nodes"></a>Windows 노드를 어떻게 패치하나요?

Windows 노드의 최신 패치를 얻으려면 [노드 풀을 업그레이드][nodepool-upgrade] 하거나 [노드 이미지를 업그레이드][upgrade-node-image]하면 됩니다. AKS에 있는 노드에서는 Windows 업데이트를 사용할 수 없습니다. AKS는 패치가 가능해지는 즉시 새 노드 풀 이미지를 릴리스하며, 패치 및 핫픽스의 최신 상태를 유지하기 위해 노드 풀을 업그레이드하는 것은 사용자의 책임입니다. Kubernetes 버전이 사용되는 경우에도 마찬가지입니다. [AKS 릴리스 정보][aks-release-notes]는 새 버전을 사용할 수 있는 시기를 나타냅니다. Windows Server 노드 풀을 업그레이드하는 방법에 대한 자세한 정보는 [AKS에서 노드 풀 업그레이드][nodepool-upgrade]를 참조하세요. 노드 이미지 업데이트에만 관심이 있는 경우 [AKS 노드 이미지 업그레이드][upgrade-node-image]를 참조하세요.

> [!NOTE]
> 업데이트된 Windows Server 이미지는 노드 풀을 업그레이드 하기 전에 클러스터 업그레이드(컨트롤 플레인 업그레이드)를 수행한 경우에만 사용됩니다.
>

## <a name="what-network-plug-ins-are-supported"></a>어떤 네트워크 플러그인이 지원되나요?

Windows 노드 풀이 있는 AKS 클러스터는 Azure CNI(고급) 네트워킹 모델을 사용해야 합니다. Kubenet(기본) 네트워킹은 지원되지 않습니다. 네트워크 모델의 차이점에 대한 자세한 정보는 [AKS의 애플리케이션에 대한 네트워크 개념][azure-network-models]을 참조하세요. Azure CNI 네트워크 모델은 IP 주소 관리에 대해 추가적으로 계획하고 고려해야 합니다. Azure CNI를 계획하고 구현하는 방법에 대한 자세한 정보는 [AKS에서 Azure CNI 네트워킹 구성][configure-azure-cni]을 참조하세요.

또한 AKS 클러스터의 Windows 노드는 Calico를 사용하는 경우 기본값으로 [DSR(Direct Server Return)을][dsr] 사용하도록 설정됩니다.

## <a name="is-preserving-the-client-source-ip-supported"></a>클라이언트 원본 IP 유지가 지원되나요?

현재 클라이언트 [원본 IP 유지][client-source-ip]는 Windows 노드에서 지원되지 않습니다.

## <a name="can-i-change-the-max--of-pods-per-node"></a>노드당 최대 Pod 수를 변경할 수 있나요?

예. 사용할 수 있는 의미 및 옵션은 [최대 Pod 수][maximum-number-of-pods]를 참조하세요.

## <a name="why-am-i-seeing-an-error-when-i-try-to-create-a-new-windows-agent-pool"></a>새 Windows 에이전트 풀을 만들려고 할 때 오류가 표시되는 이유는 무엇인가요?

2020년 2월 이전에 클러스터를 만들었고 클러스터 업그레이드 작업을 한 번도 수행하지 않은 경우 클러스터는 여전히 이전 Windows 이미지를 사용합니다. 다음과 유사한 오류가 나타날 수 있습니다.

"배포 템플릿에서 참조되는 다음 이미지 목록을 찾을 수 없습니다. 게시자: MicrosoftWindowsServer, 제안: WindowsServer, Sku: 2019-datacenter-core-smalldisk-2004, 버전: latest. 사용 가능한 이미지를 찾는 방법에 대한 지침은 [Azure PowerShell Azure Marketplace VM](../virtual-machines/windows/cli-ps-findimage.md) 이미지 찾기 및 사용을 참조하세요.

이 오류를 해결하려면

1. [클러스터 컨트롤 플레인][upgrade-cluster-cp]을 업그레이드하여 이미지 제품 및 게시자를 업데이트합니다.
1. 새로운 Windows 에이전트 풀을 만듭니다.
1. 기존 Windows 에이전트 풀에서 새로운 Windows 에이전트 풀로 Windows Pod를 이동합니다.
1. 이전의 Windows 에이전트 풀을 삭제합니다.

## <a name="how-do-i-rotate-the-service-principal-for-my-windows-node-pool"></a>내 Windows 노드 풀에 대한 서비스 주체를 어떻게 회전하나요?

Windows 노드 풀은 서비스 주체 회전을 지원하지 않습니다. 서비스 주체를 업데이트하려면 새로운 Windows 노드 풀을 만들고 Pod를 이전 풀에서 새로운 풀로 마이그레이션합니다. 이 작업이 완료되면 이전 노드 풀을 삭제합니다.

대신, 기본적으로 서비스 사용자와 관련된 래퍼로 관리 되는 ID를 사용합니다. 자세한 내용은 [Azure Kubernetes Service에서 관리 ID 사용][managed-identity]을 참조하세요.

## <a name="how-do-i-change-the-administrator-password-for-windows-server-nodes-on-my-cluster"></a>내 클러스터에서 Windows Server 노드의 관리자 암호를 어떻게 변경하나요?

AKS 클러스터를 만들 때 `--windows-admin-password` 및 `--windows-admin-username` 매개 변수를 지정하여 클러스터의 모든 Windows Server 노드에 대한 관리자 자격 증명을 설정합니다. 관리자 자격 증명을 지정하지 않은 경우, 가령 Azure Portal을 사용해 클러스터를 만들었거나 Azure CLI를 사용해 `--vm-set-type VirtualMachineScaleSets` 및 `--network-plugin azure`를 설정한 경우, 사용자 이름은 기본값으로 *azureuser* 로 설정되고 임의 암호가 생성됩니다.

관리자 암호를 변경하려면 `az aks update` 명령을 사용합니다.

```azurecli
az aks update \
    --resource-group $RESOURCE_GROUP \
    --name $CLUSTER_NAME \
    --windows-admin-password $NEW_PW
```

> [!IMPORTANT]
> 이 작업을 수행하면 모든 Windows Server 노드 풀이 업그레이드됩니다. Linux 노드 풀에는 영향을 주지 않습니다.
> 
> `--windows-admin-password`를 변경하는 경우, 새 암호는 14자 이상이어야 하며 [Windows Server 암호 요구 사항][windows-server-password]을 충족해야 합니다.

## <a name="how-many-node-pools-can-i-create"></a>얼마나 많은 노드 풀을 만들 수 있습니까?

AKS 클러스터에는 최대 100개의 노드 풀을 포함할 수 있습니다. 노드 풀에 최대 1,000개의 노드를 만들 수 있습니다. [노드 풀 제한][nodepool-limitations]

## <a name="what-can-i-name-my-windows-node-pools"></a>Windows 노드 풀에 어떤 이름을 붙일 수 있나요?

이름은 최대 6(여섯)자까지 쓸 수 있습니다. 이는 현재 AKS의 제한 사항입니다.

## <a name="are-all-features-supported-with-windows-nodes"></a>Windows 노드에서 모든 기능이 지원되나요?

Kubenet는 현재 Windows 노드에서 지원되지 않습니다.

## <a name="can-i-run-ingress-controllers-on-windows-nodes"></a>Windows 노드에서 수신 컨트롤러를 실행할 수 있나요?

예, Windows Server 컨테이너를 지원하는 수신 컨트롤러는 AKS의 Windows 노드에서 실행할 수 있습니다.

## <a name="can-my-windows-server-containers-use-gmsa"></a>내 Windows Server 컨테이너에서 gMSA를 사용할 수 있나요?

gMSA(그룹 관리되는 서비스 계정)은 현재 AKS에서 지원되지 않습니다.

## <a name="can-i-use-azure-monitor-for-containers-with-windows-nodes-and-containers"></a>Windows 노드 및 컨테이너가 있는 컨테이너에 Azure Monitor를 사용할 수 있나요?

예, 하지만 Azure Monitor는 Windows 컨테이너에서 로그(stdout, stderr) 및 메트릭을 수집하기 위한 공개 미리 보기로 제공됩니다. Windows 컨테이너에서 stdout 로그의 라이브 스트림에 연결할 수도 있습니다.

## <a name="are-there-any-limitations-on-the-number-of-services-on-a-cluster-with-windows-nodes"></a>Windows 노드가 있는 클러스터의 서비스 수에 제한이 있나요?

Windows 노드가 있는 클러스터는 포트 소모가 발생하기 전까지 약 500개의 서비스를 사용할 수 있습니다.

## <a name="can-i-use-azure-hybrid-benefit-with-windows-nodes"></a>Windows 노드에서 Azure 하이브리드 혜택을 사용할 수 있나요?

예. Windows Server용 Azure 하이브리드 혜택은 온-프레미스 Windows Server 라이선스를 AKS Windows 노드로 가져올 수 있도록 하여 운영 비용을 절감합니다.

Azure 하이브리드 혜택은 전체 AKS 클러스터, 또는 개별 노드에서 사용할 수 있습니다. 개별 노드의 경우 [노드 리소스 그룹][resource-groups]으로 이동하여 노드에 Azure 하이브리드 혜택을 직접 적용해야 합니다. 개별 노드에 Azure 하이브리드 혜택을 적용하는 자세한 정보는 [Windows Server용 Azure 하이브리드 혜택][hybrid-vms]을 참조하세요. 

새로운 AKS 클러스터에서 Azure 하이브리드 혜택을 사용하려면 `--enable-ahub` 인수를 사용합니다.

```azurecli
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-sku Standard \
    --windows-admin-password 'Password1234$' \
    --windows-admin-username azure \
    --network-plugin azure
    --enable-ahub
```

기존 AKS 클러스터에서 Azure 하이브리드 혜택을 사용하려면 `--enable-ahub` 인수를 사용하여 클러스터를 업데이트합니다.

```azurecli
az aks update \
    --resource-group myResourceGroup
    --name myAKSCluster
    --enable-ahub
```

클러스터에 Azure 하이브리드 혜택이 설정되어 있는지 확인하려면 다음 명령을 사용합니다.

```azurecli
az vmss show --name myAKSCluster --resource-group MC_CLUSTERNAME
```

클러스터가 Azure 하이브리드 혜택을 사용하도록 설정된 경우 `az vmss show`의 출력은 다음과 유사합니다.

```console
"platformFaultDomainCount": 1,
  "provisioningState": "Succeeded",
  "proximityPlacementGroup": null,
  "resourceGroup": "MC_CLUSTERNAME"
```

## <a name="can-i-use-the-kubernetes-web-dashboard-with-windows-containers"></a>Windows 컨테이너에서 Kubernetes 웹 대시보드를 사용할 수 있나요?

예, [Kubernetes 웹 대시보드][kubernetes-dashboard]를 사용하여 Windows 컨테이너에 대한 정보에 액세스할 수 있지만, 현재 Kubernetes 웹 대시보드에서 직접 실행 중인 Windows 컨테이너로 *kubectl exec* 를 실행할 수는 없습니다. 실행 중인 Windows 컨테이너에 연결하는 세부 정보는 [유지 관리 또는 문제 해결을 위해 RDP를 AKS(Azure Kubernetes Service) 클러스터 Windows Server 노드에 연결][windows-rdp]을 참조하세요.

## <a name="how-do-i-change-the-time-zone-of-a-running-container"></a>실행 중인 컨테이너의 표준 시간대를 어떻게 변경하나요?

실행 중인 Windows Server 컨테이너의 표준 시간대를 변경하려면 PowerShell 세션을 사용하여 실행 중인 컨테이너에 연결합니다. 예를 들면 다음과 같습니다.
    
```azurecli-interactive
kubectl exec -it CONTAINER-NAME -- powershell
```

실행 중인 컨테이너에서 [Set-TimeZone](/powershell/module/microsoft.powershell.management/set-timezone)을 사용하여 실행 중인 컨테이너의 표준 시간대를 설정합니다. 예를 들면 다음과 같습니다.

```powershell
Set-TimeZone -Id "Russian Standard Time"
```

실행 중인 컨테이너 또는 사용 가능한 표준 시간대 목록의 현재 표준 시간대를 확인하려면 [Get-TimeZone](/powershell/module/microsoft.powershell.management/get-timezone)을 사용합니다.

## <a name="can-i-maintain-session-affinity-from-client-connections-to-pods-with-windows-containers"></a>Windows 컨테이너를 사용하여 클라이언트 연결에서 Pod에 대한 세션 선호도를 유지할 수 있나요?
WS2022 OS 버전에서 지원되지만, 클라이언트 IP로 세션 선호도를 달성하는 현재 방법은 노드당 단일 인스턴스를 실행하도록 원하는 Pod를 제한하고 로컬 노드의 Pod로 트래픽을 이동하도록 Kubernetes 서비스를 구성하여 수행됩니다. 이를 위해 다음 구성을 사용할 수 있습니다.
1. 최소 버전 1.20을 실행하는 AKS 클러스터
1. Windows 노드당 하나의 인스턴스만 허용하도록 Pod를 제한합니다. 배포 구성에서 선호도 방지를 사용하여 이를 달성할 수 있습니다.
1. Kubernetes 서비스 구성에서 "externalTrafficPolicy=Local"을 설정합니다. 이렇게 하면 Kubernetes 서비스가 트래픽을 로컬 노드 내의 Pod로만 안내합니다.
1. Kubernetes 서비스 구성에서 "sessionAffinity: ClientIP"를 설정합니다. 이렇게 하면 Azure Load Balancer 세션 선호도로 구성됩니다.

## <a name="what-if-i-need-a-feature-thats-not-supported"></a>지원되지 않는 기능이 필요한 경우 어떻게 하나요?

AKS에서 Windows에 필요한 모든 기능을 제공하기 위해 노력하고 있지만, 더 필요한 기능이 있으시다면 오픈 소스인 업스트림 [aks-engine][aks-engine] 프로젝트가 Windows 지원을 포함하여 Azure에서 Kubernetes를 실행하는 완전히 사용자 지정할 수 있고 손쉬운 방법을 제공합니다. 최신 기능 로드맵을 다음 [AKS 로드맵][aks-roadmap]에서 확인하세요.

## <a name="next-steps"></a>다음 단계

AKS에서 Windows Server 컨테이너를 시작하려면 [AKS에서 Windows server를 실행 하는 노드 풀을 만듭니다][windows-node-cli].

<!-- LINKS - external -->
[kubernetes]: https://kubernetes.io
[aks-engine]: https://github.com/azure/aks-engine
[upstream-limitations]: https://kubernetes.io/docs/setup/production-environment/windows/intro-windows-in-kubernetes/#supported-functionality-and-limitations
[intro-windows]: https://kubernetes.io/docs/setup/production-environment/windows/intro-windows-in-kubernetes/
[aks-roadmap]: https://github.com/Azure/AKS/projects/1
[aks-release-notes]: https://github.com/Azure/AKS/releases

<!-- LINKS - internal -->
[azure-network-models]: concepts-network.md#azure-virtual-networks
[configure-azure-cni]: configure-azure-cni.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[windows-node-cli]: windows-container-cli.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[upgrade-cluster]: upgrade-cluster.md
[upgrade-cluster-cp]: use-multiple-node-pools.md#upgrade-a-cluster-control-plane-with-multiple-node-pools
[azure-outbound-traffic]: ../load-balancer/load-balancer-outbound-connections.md#defaultsnat
[nodepool-limitations]: use-multiple-node-pools.md#limitations
[windows-container-compat]: /virtualization/windowscontainers/deploy-containers/version-compatibility?tabs=windows-server-2019%2Cwindows-10-1909
[maximum-number-of-pods]: configure-azure-cni.md#maximum-pods-per-node
[azure-monitor]: ../azure-monitor/containers/container-insights-overview.md#what-does-azure-monitor-for-containers-provide
[client-source-ip]: concepts-network.md#ingress-controllers
[kubernetes-dashboard]: kubernetes-dashboard.md
[windows-rdp]: rdp.md
[upgrade-node-image]: node-image-upgrade.md
[managed-identity]: use-managed-identity.md
[hybrid-vms]: ../virtual-machines/windows/hybrid-use-benefit-licensing.md
[resource-groups]: faq.md#why-are-two-resource-groups-created-with-aks
[dsr]: ../load-balancer/load-balancer-multivip-overview.md#rule-type-2-backend-port-reuse-by-using-floating-ip
[windows-server-password]: /windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements#reference
