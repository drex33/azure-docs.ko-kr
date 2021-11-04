---
title: Azure의 가상 머신 확장 집합에 대한 유연한 오케스트레이션
description: Azure의 가상 머신 확장 집합에 대해 유연한 오케스트레이션 모드를 사용하는 방법을 알아봅니다.
author: fitzgeraldsteele
ms.author: fisteele
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: flexible-scale-sets
ms.date: 10/13/2021
ms.reviewer: jushiman
ms.custom: mimckitt, devx-track-azurecli, vmss-flex
ms.openlocfilehash: 56ba42b3aca7d48a62edd264a8ec867fcda5107c
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131074520"
---
# <a name="flexible-orchestration-for-virtual-machine-scale-sets-in-azure"></a>Azure의 가상 머신 확장 집합에 대한 유연한 오케스트레이션

**적용 대상:** :heavy_check_mark: 유연한 확장 집합

유연한 오케스트레이션이 있는 가상 머신 확장 집합을 사용하면 [균일한 오케스트레이션 모드에서 가상 머신 확장 집합의](../virtual-machine-scale-sets/overview.md) 확장성을 [가용성 집합의](availability-set-overview.md)지역 가용성 보장과 결합할 수 있습니다.

Azure 가상 머신 확장 집합을 사용하면 부하 분산된 VM의 그룹을 만들고 관리할 수 있습니다. VM 인스턴스의 수는 요구 또는 정의된 일정에 따라 자동으로 늘리거나 줄일 수 있습니다. 확장 집합에서 제공하는 주요 이점은 다음과 같습니다.
- 손쉬운 여러 VM 만들기 및 관리
- 가용성 영역 또는 장애 도메인에 VM을 분산하여 고가용성 및 애플리케이션 복원력을 제공합니다.
- 리소스 수요 변화에 따라 자동으로 애플리케이션 크기 조정
- 대규모 작업

Flexible 오케스트레이션을 사용하면 Azure는 Azure VM 에코시스템에서 통합된 환경을 제공합니다. Flexible 오케스트레이션은 지역 또는 가용성 영역 내의 장애 도메인에 VM을 분산하여 고가용성 보증(최대 1000개 VM)을 제공합니다. 이렇게 하면 다음을 비롯한 쿼럼 기반 또는 상태 저장 워크로드를 실행하는 데 필수적인 장애 도메인 격리를 유지하면서 애플리케이션을 스케일 아웃할 수 있습니다.
- 쿼럼 기반 워크로드
- 오픈 소스 데이터베이스
- 상태 저장 애플리케이션
- 고가용성 및 대규모를 필요로 하는 서비스
- 가상 머신 유형을 혼합하거나 스폿을 활용하고 온-디맨드 VM을 함께 활용하려는 서비스
- 기존 가용성 집합 애플리케이션

> [!IMPORTANT]
> 이 문서에서는 모든 새 확장 집합 배포에 를 사용하는 것이 좋습니다. 유연한 오케스트레이션 모드의 가상 머신 확장 집합에 대해 자세히 알아보겠습니다. 균일한 확장 집합에 대한 정보에 액세스하려면 [균일 오케스트레이션 모드 설명서의 가상 머신 확장 집합으로](../virtual-machine-scale-sets/overview.md) 이동합니다.

단일 확장 집합과 [오케스트레이션 모드](../virtual-machine-scale-sets/virtual-machine-scale-sets-orchestration-modes.md)의 유연한 확장 집합 간의 차이점에 대해 자세히 알아보세요.

> [!CAUTION]
> 오케스트레이션 모드는 확장 집합을 만들 때 정의되며 나중에 변경하거나 업데이트할 수 없습니다.


## <a name="why-use-virtual-machine-scale-sets"></a>가상 머신 확장 집합을 사용하는 이유는?
중복성과 향상된 성능을 제공하기 위해 애플리케이션은 일반적으로 여러 인스턴스 간에 분산됩니다. 고객은 애플리케이션 인스턴스 중 하나에 요청을 배포하는 부하 분산 장치를 통해 애플리케이션에 액세스할 수 있습니다. 유지 관리를 수행하거나 애플리케이션 인스턴스를 업데이트해야 하는 경우, 고객은 사용 가능한 다른 애플리케이션 인스턴스에 배포되어야 합니다. 추가 고객 요구에 맞추려면 애플리케이션을 실행하는 애플리케이션 인스턴스의 수를 늘려야 할 수도 있습니다.

Azure 가상 머신 확장 집합은 많은 VM에서 실행되는 애플리케이션, 리소스의 자동 크기 조정 및 트래픽 부하 분산에 대한 관리 기능을 제공합니다. 확장 집합에서 제공하는 주요 이점은 다음과 같습니다.

- **손쉬운 여러 VM 만들기 및 관리**
    - 애플리케이션을 실행하는 VM을 많이 사용하는 경우 환경 전체에서 일관된 구성을 유지해야 합니다. 신뢰할 수 있는 애플리케이션 성능을 위해 VM 크기, 디스크 구성 및 애플리케이션 설치가 모든 VM에서 일치해야 합니다.
    - 확장 집합을 사용하면 동일한 기본 OS 이미지 및 구성에 모든 VM 인스턴스가 만들어집니다. 이 방식을 사용하면 추가 구성 작업 또는 네트워크 관리 없이 수백 개의 VM을 쉽게 관리할 수 있습니다.
    - 확장 집합은 기본 4계층 트래픽 분산에는 [Azure 부하 분산 장치](../load-balancer/load-balancer-overview.md)를 사용하고, 고급 7계층 트래픽 분산 및 TLS 종료에는 [Azure Application Gateway](../application-gateway/overview.md)를 사용하도록 지원합니다.

- **고가용성 및 애플리케이션 복원력 제공**
    - 확장 집합은 애플리케이션의 여러 인스턴스를 실행하는 데 사용됩니다. 이러한 VM 인스턴스 중 하나에 문제가 있는 경우, 고객은 중단을 최소화하면서 다른 VM 인스턴스 중 하나를 통해 애플리케이션에 계속 액세스합니다.
    - 추가 가용성을 위해 [가용성 영역](../availability-zones/az-overview.md)을 사용하여 단일 데이터 센터 또는 여러 데이터 센터 내에서 VM 인스턴스를 확장 집합에 자동으로 배포할 수 있습니다.

- **리소스 수요 변화에 따라 자동으로 애플리케이션 크기 조정**
    - 애플리케이션에 대한 고객의 요구는 하루 또는 일주일 내내 변할 수 있습니다. 고객 요구를 맞추기 위해 확장 집합은 애플리케이션 수요가 증가함에 따라 VM 인스턴스 수를 자동으로 늘린 다음, 수요가 감소함에 따라 VM 인스턴스 수를 줄일 수 있습니다.
    - 또한 자동 크기 조정은 수요가 낮을 때 애플리케이션을 실행하는 불필요한 VM 인스턴스의 수를 최소화하는 한편, 수요가 증가하고 추가 VM 인스턴스가 자동으로 추가될 때 고객이 허용 가능한 수준의 성능을 계속 확보할 수 있습니다. 이 기능은 필요에 따라 비용을 절감하고 Azure 리소스를 효율적으로 만드는 데 도움이 됩니다.

- **대규모 작업**
    - 확장 집합은 공유 이미지 갤러리를 통해 표준 마켓플레이스 이미지 및 사용자 지정 이미지에 대해 최대 1,000개의 VM 인스턴스를 지원합니다. 관리되는 이미지를 사용하여 확장 집합을 만드는 경우에는 VM 인스턴스가 600개로 제한됩니다.
    - 프로덕션 워크로드에서 최상의 성능을 얻으려면 [Azure Managed Disks](../virtual-machines/managed-disks-overview.md)를 사용합니다.



## <a name="get-started-with-flexible-orchestration-mode"></a>Flexible 오케스트레이션 모드 시작하기

[Azure Portal](flexible-virtual-machine-scale-sets-portal.md), [Azure CLI](flexible-virtual-machine-scale-sets-cli.md), [Azure PowerShell](flexible-virtual-machine-scale-sets-powershell.md), [ARM Template](flexible-virtual-machine-scale-sets-rest-api.md)을 통해 확장 집합에 대한 유연한 오케스트레이션 모드를 시작하세요. 

> [!IMPORTANT]
> 명시적 아웃바운드 네트워크 연결이 있는지 확인합니다. [Azure의 가상 네트워크 및 가상 머신에서](../virtual-network/network-overview.md) 이에 대해 자세히 알아보고 Azure의 네트워킹 모범 사례를 따르고 있는지 [확인합니다.](../virtual-network/concepts-and-best-practices.md)


## <a name="add-instances-with-autoscaling-or-manually"></a>자동 크기 조정 또는 수동으로 인스턴스
유연한 오케스트레이션이 있는 가상 머신 확장 집합은 여러 VM을 관리하기 위한 씬 오케스트레이션 계층으로 작동합니다. 여러 가지 방법으로 확장 집합을 통해 관리할 VM을 추가할 수 있습니다.

- **인스턴스 수 설정**

    유연한 오케스트레이션을 사용하여 확장 집합을 만들 때, 확장에 사용할 템플릿을 설명하는 VM 프로필 또는 템플릿을 정의합니다. 그런 다음 용량 매개 변수를 설정하여 확장 집합에서 관리하는 VM 인스턴스 수를 늘리거나 줄일 수 있습니다. 

- **메트릭 또는 일정에 따라 자동 크기 조정** 

    자동 크기 조정 규칙을 설정하여 메트릭 또는 일정에 따라 용량을 늘리거나 줄일 수 있습니다. [자동 크기 조정 지원 가상 머신 확장 집합](..\virtual-machine-scale-sets\virtual-machine-scale-sets-autoscale-overview.md)을 참조하세요. 

- **VM을 만들 때 확장 집합 지정**

    VM을 만들 때 가상 머신 확장 집합에 추가되도록 선택적으로 지정할 수 있습니다. VM은 VM을 만들 때만 확장 집합에 추가할 수 있습니다. 새로 만든 VM은 배포 방법에 관계없이 유연한 확장 집합과 동일한 리소스 그룹에 있어야 합니다.

유연한 오케스트레이션 모드는 Azure에 배포된 모든 IaaS VM의 90%를 포함하는 [메모리 보존 업데이트 또는 실시간 마이그레이션을](../virtual-machines/maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot)지원하는 VM S SK와 함께 사용할 수 있습니다. 여기에는 B-, D-, E- 및 F 시리즈 VM과 같은 범용 제품군이 광범위하게 포함됩니다. 현재 유연한 모드는 G-, H-, L-, M-, N 시리즈 VM을 포함하여 메모리 유지 업데이트를 지원하지 않는 VM S SK 또는 제품군을 오케스트레이션할 수 없습니다. 컴퓨팅 리소스 [SKU API를](/rest/api/compute/resource-skus/list) 사용하여 특정 VM SKU가 지원되는지 여부를 확인할 수 있습니다.

```azurecli-interactive
az vm list-skus -l eastus --size standard_d2s_v3 --query "[].capabilities[].[name, value]" -o table
```

> [!IMPORTANT]
> 네트워킹 동작은 확장 집합 내에서 가상 머신을 만드는 방법에 따라 달라집니다. 자세한 내용은 [확장 가능한 네트워크 연결을 참조하세요.](../virtual-machines/flexible-virtual-machine-scale-sets-migration-resources.md#create-scalable-network-connectivity)


## <a name="features"></a>기능
다음 표에는 유연한 오케스트레이션 모드 기능 및 해당 설명서에 대한 링크가 나와 있습니다.

### <a name="basic-setup"></a>기본 설정

| 기능 | 확장 집합에 대한 유연한 오케스트레이션에서 지원 |
|---|---|
| 가상 머신 유형  | 표준 Azure IaaS VM(Microsoft.compute/virtualmachines)  |
| 최대 인스턴스 수(FD 보장 포함)  | 1000  |
| 지원되는 SKU  | D 시리즈, E 시리즈, F 시리즈, A 시리즈, B 시리즈, Intel, AMD; 특수 S SKU(G, H, L, M, N)는 지원되지 않습니다. |
| VM, NIC, 디스크에 대한 모든 컨트롤  | 예  |
| RBAC 권한 필요  | 컴퓨팅 VMSS 쓰기, 컴퓨팅 VM 쓰기, 네트워크 |
| 가속화된 네트워킹  | 예  |
| 스폿 인스턴스 및 가격 책정   | 예, 스폿 및 일반 우선 순위 인스턴스를 모두 사용할 수 있습니다  |
| 혼합 운영 체제  | 예, Linux 및 Windows는 동일한 Flexible 확장 집합에 상주할 수 있습니다.  |
| 디스크 유형  | 관리 디스크만, 모든 스토리지 유형  |
| 쓰기 가속기   | 예  |
| 근접 배치 그룹   | 예, [근접 배치 그룹 설명서](../virtual-machine-scale-sets/proximity-placement-groups.md)를 참조하세요 |
| Azure 전용 호스트   | 예  |
| 관리 ID  | 사용자 할당 Id만  |
| 그룹에 기존 VM 추가/제거  | 예  |
| Service Fabric  | 아니요  |
| Azure Kubernetes Service(AKS) / AKE  | 아니요  |
| UserData  | 부분, 개별 Vm에 대해 UserData를 지정할 수 있습니다. |


### <a name="autoscaling-and-instance-orchestration"></a>자동 크기 조정 및 인스턴스 오케스트레이션

| 기능 | 확장 집합에 대 한 유연한 오케스트레이션에 서 지원 됨 |
|---|---|
| 집합의 VM 나열 | Yes |
| 자동 크기 조정(수동, 메트릭 기반, 일정 기반) | 예 |
| VM 인스턴스를 삭제할 때 NIC 및 디스크 자동 제거 | Yes |
| 업그레이드 정책(VM 확장 집합) | 아니요, 업그레이드 정책은 생성 중에 null 또는 []이어야 합니다 |
| 자동 OS 업데이트(VM 확장 집합) | 예 |
| 게스트 보안 패치 중 | 예 |
| 알림 종료(VM 확장 집합) | 예, [종료 알림 설명서](../virtual-machine-scale-sets/virtual-machine-scale-sets-terminate-notification.md)를 참조하세요 |
| 애플리케이션 상태 모니터링 | 애플리케이션 상태 확장 |
| 인스턴스 복구(VM 확장 집합) | 예, [인스턴스 복구 설명서](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-instance-repairs.md)를 참조하세요 |
| 인스턴스 보호 | 아니요, [Azure 리소스 잠금을](../azure-resource-manager/management/lock-resources.md) 사용 합니다. |
| 정책에서 크기 조정 | 아니요 |
| VMSS 인스턴스 뷰 가져오기 | 아니요 |
| VM 일괄 처리 작업 (모두 시작, 모두 중지, 하위 집합 삭제 등) | 아니요 (VM API를 사용 하 여 각 인스턴스에 대 한 작업을 트리거할 수 있음) |

### <a name="high-availability"></a>고가용성 

| 기능 | 확장 집합에 대 한 유연한 오케스트레이션에 서 지원 됨 |
|---|---|
| 가용성 SLA | 장애 도메인에 분산 되는 인스턴스의 경우 99.95% 여러 영역에 걸쳐 있는 인스턴스의 경우 99.99% |
| 가용성 영역 | 1, 2 또는 3개의 가용성 영역에 인스턴스를 지정합니다 |
| 특정 가용성 영역에 VM 할당 | Yes |
| 장애 도메인 – 최대 분산(Azure는 최대 확산 인스턴스) | 예 |
| 장애 도메인 – 고정 분산 | 2-3 FDs (지역별 최대 FD 수에 따라 다름) 영역 배포의 경우 1 |
| 특정 장애 도메인에 VM 할당 | 예 |
| 업데이트 도메인 | 사용 (플랫폼 유지 관리에서 FD로 FD가 수행) |
| 유지 보수 수행 | VM API를 사용 하 여 각 인스턴스에서 유지 관리 트리거 |

### <a name="networking"></a>네트워킹 

| 기능 | 확장 집합에 대 한 유연한 오케스트레이션에 서 지원 됨 |
|---|---|
| 기본 아웃 바운드 연결 | 아니요, [명시적 아웃 바운드 연결이](../virtual-network/ip-services/default-outbound-access.md) 있어야 합니다. |
| Azure Load Balancer 표준 SKU | 예 |
| Application Gateway | 예 |
| Infiniband 네트워킹 | 예 |
| Basic SLB | 예 |
| 네트워크 포트 전달 | 예 (개별 인스턴스에 대 한 NAT 규칙) |

### <a name="backup-and-recovery"></a>Backup 및 복구 

| 기능 | 확장 집합에 대 한 유연한 오케스트레이션에 서 지원 됨 |
|---|---|
| Azure Backup  | 예 |
| Azure Site Recovery | 예 (PowerShell을 통해) |
| Azure Alerts  | 예 |
| VM 인사이트  | 개별 Vm에 설치할 수 있습니다. |

### <a name="unsupported-parameters"></a>지원되지 않는 매개 변수

다음 가상 머신 확장 집합 매개 변수는 현재 유연한 오케스트레이션 모드의 가상 머신 확장 집합에서 지원 되지 않습니다.
- 단일 배치 그룹-선택 해야 합니다. `singlePlacementGroup=False`
- 특수 SKU를 사용하여 배포: G, H, L, M, N 시리즈 VM 제품군
- Ultra disk 구성: `diskIOPSReadWrite` , `diskMBpsReadWrite`
- VMSS 오버프로비전
- 이미지 기반 자동 OS 업그레이드
- SLB 상태 프로브를 통한 애플리케이션 상태 - 인스턴스에서 애플리케이션 상태 확장 사용
- 가상 머신 확장 집합 업그레이드 정책 - null이거나 비어 있어야 합니다
- Azure 전용 호스트에 배포
- 관리되지 않는 디스크
- 정책의 가상 머신 확장 집합 크기 조정
- 가상 머신 확장 집합 인스턴스 보호
- 기본 Load Balancer
- 표준 Load Balancer NAT 풀을 통한 포트 전달 - 특정 인스턴스에 대한 NAT 규칙을 구성할 수 있습니다


## <a name="troubleshoot-scale-sets-with-flexible-orchestration"></a>Flexible 오케스트레이션을 사용하여 확장 집합 문제 해결
문제 해결 시나리오에 적합한 솔루션을 찾습니다.

<!-- error -->
### <a name="invalidparameter-the-specified-fault-domain-count-3-must-fall-in-the-range-1-to-2"></a>InvalidParameter. 지정 된 장애 도메인 수 3은 1 ~ 2 범위에 속해야 합니다.

```
InvalidParameter. The specified fault domain count 3 must fall in the range 1 to 2.
```

**원인:** `platformFaultDomainCount` 선택한 지역 또는 영역에 대한 매개 변수가 잘못되었습니다.

**해결 방법:** 유효한 `platformFaultDomainCount` 값을 선택해야 합니다. 영역 배포의 경우 최대 `platformFaultDomainCount` 값은 1입니다. 영역을 지정하지 않는 지역 배포의 경우 최대 `platformFaultDomainCount`는 지역에 따라 다릅니다. 지역별 최대 장애 도메인 수를 확인하는 [스크립트에 대한 VM의 가용성 관리](../virtual-machines/availability.md)를 참조하세요.


<!-- error -->
### <a name="operationnotallowed-deletion-of-virtual-machine-scale-set-is-not-allowed-as-it-contains-one-or-more-vms-please-delete-or-detach-the-vms-before-deleting-the-virtual-machine-scale-set"></a>OperationNotAllowed. 하나 이상의 VM이 포함되어 있으므로, 가상 머신 확장 집합을 삭제할 수 없습니다. 가상 머신 확장 집합을 삭제하기 전에 VM을 삭제하거나 분리하세요.

```
OperationNotAllowed. Deletion of Virtual Machine Scale Set is not allowed as it contains one or more VMs. Please delete or detach the VM(s) before deleting the Virtual Machine Scale Set.
```

**원인:** 하나 이상의 가상 머신과 연결된 Flexible 오케스트레이션 모드에서 확장 집합을 삭제하려고 합니다.

**해결 방법:** Flexible 오케스트레이션 모드에서 확장 집합에 연결된 모든 가상 머신을 삭제한 다음 확장 집합을 삭제할 수 있습니다.


<!-- error -->
### <a name="invalidparameter-the-value-true-of-parameter-singleplacementgroup-is-not-allowed-allowed-values-are-false"></a>InvalidParameter. 'singlePlacementGroup' 매개 변수의 'True' 값은 허용되지 않습니다. 허용되는 값은 False입니다.

```
InvalidParameter. The value 'True' of parameter 'singlePlacementGroup' is not allowed. Allowed values are: False.
```
**원인:** `singlePlacementGroup` 매개 변수가 *True* 로 설정 된 경우

**해결 방법:** `singlePlacementGroup`을 *False* 로 설정해야 합니다.


<!-- error -->
### <a name="outboundconnectivitynotenabledonvm-no-outbound-connectivity-configured-for-virtual-machine"></a>OutboundConnectivityNotEnabledOnVM. 가상 컴퓨터에 대해 아웃 바운드 연결이 구성 되지 않았습니다.

```
OutboundConnectivityNotEnabledOnVM. No outbound connectivity configured for virtual machine.
```
**원인:** 아웃 바운드 인터넷 연결이 없는 유연한 오케스트레이션 모드로 가상 머신 확장 집합을 만들려고 합니다.

**해결 방법:** 응용 프로그램에 가장 적합 한 방식으로 가상 머신 확장 집합에 대 한 보안 아웃 바운드 액세스를 사용 하도록 설정 합니다. 아웃 바운드 액세스는 서브넷의 NAT 게이트웨이와 함께 사용 하도록 설정 하거나 Load Balancer 백 엔드 풀에 인스턴스를 추가 하거나 인스턴스당 명시적인 공용 IP를 추가할 수 있습니다. 보안 수준이 높은 응용 프로그램의 경우 방화벽 또는 가상 네트워크 응용 프로그램을 통해 사용자 정의 경로를 지정할 수 있습니다. 자세한 내용은 [기본 아웃 바운드 액세스](../virtual-network/ip-services/default-outbound-access.md) 를 참조 하세요.

## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"]
> [Azure Portal를 사용하는 확장 집합에 대한 유연한 오케스트레이션 모드.](flexible-virtual-machine-scale-sets-portal.md)