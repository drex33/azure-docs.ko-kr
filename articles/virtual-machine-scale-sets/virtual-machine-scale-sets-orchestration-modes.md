---
title: Azure 가상 머신 확장 집합에 대한 오케스트레이션 모드
description: Azure의 가상 머신 확장 집합에 대해 Flexible 및 Uniform 오케스트레이션 모드를 사용하는 방법을 알아봅니다.
author: fitzgeraldsteele
ms.author: fisteele
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.date: 08/05/2021
ms.reviewer: jushiman
ms.custom: mimckitt, devx-track-azurecli, vmss-flex, devx-track-azurepowershell
ms.openlocfilehash: 7983ae912d29f2a27d35b261d1654205fe503651
ms.sourcegitcommit: 851b75d0936bc7c2f8ada72834cb2d15779aeb69
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123305073"
---
# <a name="preview-orchestration-modes-for-virtual-machine-scale-sets-in-azure"></a>미리 보기: Azure의 가상 머신 확장 집합에 대한 오케스트레이션 모드


**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 유연한 확장 집합 :heavy_check_mark: 단일 확장 집합

Virtual Machine Scale Sets는 플랫폼 관리형 가상 컴퓨터의 논리적 그룹화를 제공합니다. 확장 집합을 사용하여 가상 머신 구성 모델을 만들고, CPU 또는 메모리 부하에 따라 추가 인스턴스를 자동으로 추가 또는 제거하고, 최신 OS 버전으로 자동으로 업그레이드합니다. 일반적으로 확장 집합을 사용하면 확장 집합을 만들 때 제공되는 VM 구성 모델을 사용하여 가상 머신을 만들 수 있으며, 확장 집합은 구성 모델에 따라 암시적으로 만들어진 가상 머신만 관리할 수 있습니다. 

확장 집합 오케스트레이션 모드를 사용하면 확장 집합에서 가상 머신 인스턴스를 관리하는 방법을 보다 강력하게 제어할 수 있습니다.

> [!IMPORTANT]
> 오케스트레이션 모드는 확장 집합을 만들 때 정의되며 나중에 변경하거나 업데이트할 수 없습니다.


## <a name="scale-sets-with-uniform-orchestration"></a>Uniform 오케스트레이션이 포함된 확장 집합
같은 인스턴스가 있는 대규모 상태 비저장 워크로드에 최적화됨

Uniform 오케스트레이션이 있는 가상 머신 확장 집합은 가상 머신 프로필 또는 템플릿을 사용하여 원하는 용량까지 스케일 업합니다. 개별 가상 머신 인스턴스를 관리하거나 사용자 지정하는 기능이 있지만 Uniform은 동일한 VM 인스턴스를 사용합니다. 개별 Uniform VM 인스턴스는 가상 머신 확장 집합 VM API 명령을 통해 노출됩니다. 개별 인스턴스는 표준 Azure IaaS VM API 명령, Azure Resource Manager 리소스 태그 지정 RBAC 권한, Azure Backup 또는 Azure Site Recovery와 같은 Azure 관리 기능과 호환되지 않습니다. Uniform 오케스트레이션은 100개 미만의 인스턴스로 구성된 경우 장애 도메인 고가용성을 보장합니다. Uniform 오케스트레이션은 일반적으로 사용할 수 있으며 메트릭 기반 자동 크기 조정, 인스턴스 보호 및 자동 OS 업그레이드를 포함하여 모든 범위의 확장 집합 관리 및 오케스트레이션을 지원합니다.


## <a name="scale-sets-with-flexible-orchestration"></a>Flexible 오케스트레이션이 포함된 확장 집합
같은 가상 머신 유형이나 여러 가상 머신 유형을 사용하여 대규모로 고가용성을 달성합니다.

Flexible 오케스트레이션을 사용하면 Azure는 Azure VM 에코시스템에서 통합된 환경을 제공합니다. Flexible 오케스트레이션은 지역 또는 가용성 영역 내의 장애 도메인에 VM을 분산하여 고가용성 보증(최대 1000개 VM)을 제공합니다. 이렇게 하면 다음을 비롯한 쿼럼 기반 또는 상태 저장 워크로드를 실행하는 데 필수적인 장애 도메인 격리를 유지하면서 애플리케이션을 스케일 아웃할 수 있습니다.
- 쿼럼 기반 워크로드
- 오픈 소스 데이터베이스
- 상태 저장 애플리케이션
- 고가용성 및 대규모를 필요로 하는 서비스
- 가상 머신 유형을 혼합하거나 스폿을 활용하고 온-디맨드 VM을 함께 활용하려는 서비스
- 기존 가용성 집합 애플리케이션

> [!IMPORTANT]
> Flexible 오케스트레이션 모드의 가상 머신 확장 집합은 현재 공개 미리 보기로 제공됩니다. 아래에서 자세하게 설명하는 공개 미리 보기 기능을 사용 하려면 옵트인 프로시저를 사용해야 합니다.
> 이 미리 보기 버전은 서비스 수준 약정 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.


## <a name="what-has-changed-with-flexible-orchestration-mode"></a>Flexible 오케스트레이션 모드에서 변경된 사항은 무엇인가요?
Flexible 오케스트레이션의 주요 장점 중 하나는 확장 집합 자식 가상 머신 대신 표준 Azure IaaS VM을 통해 오케스트레이션 기능을 제공한다는 것입니다. 즉, Uniform 오케스트레이션에서 사용하는 가상 머신 확장 집합 VM API 대신 Flexible 오케스트레이션 인스턴스를 관리할 때 모든 표준 VM API를 사용할 수 있습니다. 미리 보기 기간 중에는 Flexible 오케스트레이션과 Uniform 오케스트레이션에서 인스턴스를 관리하는 것 사이에 몇 가지 차이점이 있습니다. 일반적으로 가능한 경우 표준 Azure IaaS VM API를 사용하는 것이 좋습니다. 이 섹션에서는 Flexible 오케스트레이션을 사용하여 VM 인스턴스를 관리하기 위한 모범 사례 예제를 강조합니다.

### <a name="assign-fault-domain-during-vm-creation"></a>VM을 만드는 동안 장애 도메인 할당
Flexible 오케스트레이션 확장 집합에 대한 장애 도메인 수를 선택할 수 있습니다. 기본값으로 Flexible 확장 집합에 VM을 추가하는 경우 Azure는 장애 도메인 간에 인스턴스를 균등하게 분산합니다. Azure에서 장애 도메인을 할당하는 것이 좋지만 고급 또는 문제 해결 시나리오의 경우 이 기본 동작을 재정의하고 인스턴스가 배치될 장애 도메인을 지정할 수 있습니다.

```azurecli-interactive
az vm create –vmss "myVMSS"  –-platform_fault_domain 1
```

### <a name="instance-naming"></a>인스턴스 이름 지정
VM을 만들어 Flexible 확장 집합에 추가하는 경우 Azure 명명 규칙 내에서 인스턴스 이름을 완전히 제어할 수 있습니다. 자동 크기 조정을 통해 VM이 확장 집합에 자동으로 추가되는 경우 접두사를 제공하고, Azure는 이름 끝에 고유 번호를 추가합니다.

### <a name="query-instances-for-power-state"></a>전원 상태에 대한 쿼리 인스턴스
선호되는 방법은 Azure Resource Graph를 사용하여 Virtual Machine Scale Set의 모든 VM에 대해 쿼리하는 것입니다. Azure Resource Graph는 구독에서 대규모로 Azure 리소스에 대한 효율적인 쿼리 기능을 제공합니다.

```
| where type =~ 'Microsoft.Compute/virtualMachines'
| where properties.virtualMachineScaleSet contains "demo"
| extend powerState = properties.extended.instanceView.powerState.code
| project name, resourceGroup, location, powerState
| order by resourceGroup desc, name desc
```

[Azure Resource Graph](../governance/resource-graph/overview.md)를 사용하여 리소스를 쿼리하는 것은 Azure 리소스를 쿼리하고 리소스 공급자에 대한 API 호출을 최소화하는 쉽고 효율적인 방법입니다. Azure Resource Graph는 새 리소스 또는 업데이트된 리소스가 최대 60초 동안 반영되지 않을 수 있는 궁극적으로 일관된 캐시입니다. 다음을 할 수 있습니다.
- 리소스 그룹 또는 구독 내 VM을 나열합니다.
- 확장 옵션을 사용하여 구독에 있는 모든 VM에 대한 인스턴스 보기(장애 도메인 할당, 전원 및 프로비저닝 상태)를 검색합니다.
- VM API 가져오기 및 명령을 사용하여 단일 인스턴스에 대한 모델 및 인스턴스 보기를 가져옵니다.

### <a name="scale-sets-vm-batch-operations"></a>확장 집합 VM Batch 작업
표준 VM 명령을 사용하여 Virtual Machine Scale Set VM API 대신 인스턴스를 시작, 중지, 다시 시작, 삭제합니다. Virtual Machine Scale Set VM Batch 작업(모두 시작, 모두 중지, 이미지로 다시 설치 등)은 Flexible 오케스트레이션 모드에서 사용되지 않습니다.

### <a name="monitor-application-health"></a>애플리케이션 상태 모니터링
애플리케이션 상태 모니터링을 통해 애플리케이션은 Azure에 하트비트를 제공하여 애플리케이션이 정상 상태인지 비정상 상태인지 확인할 수 있습니다. Azure는 비정상 상태인 VM 인스턴스를 자동으로 바꿀 수 있습니다. Flexible 확장 집합 인스턴스의 경우 가상 머신에서 애플리케이션 상태 확장을 설치하고 구성해야 합니다. Uniform 확장 집합 인스턴스의 경우 애플리케이션 상태 확장을 사용하거나 Azure Load Balancer 사용자 지정 상태 프로브를 사용하여 상태를 측정할 수 있습니다.

### <a name="list-scale-sets-vm-api-changes"></a>확장 집합 VM API 변경 목록
Virtual Machine Scale Sets를 사용하여 확장 집합에 속한 인스턴스를 나열할 수 있습니다. LIST Virtual Machine Scale Sets VM 명령은 Flexible 오케스트레이션을 사용하여 확장 집합 VM ID의 목록을 제공합니다. 그런 다음 GET Virtual Machine Scale Sets VM 명령을 호출하여 확장 집합에서 VM 인스턴스로 작업하는 방법에 대한 자세한 정보를 얻을 수 있습니다. VM에 대한 전체 세부 정보를 얻으려면 표준 GET VM 명령 또는 [Azure Resource Graph](../governance/resource-graph/overview.md)를 사용합니다.

### <a name="retrieve-boot-diagnostics-data"></a>부팅 진단 데이터 검색
표준 VM API 및 명령을 사용하여 인스턴스 부팅 진단 데이터 및 스크린샷을 검색합니다. Virtual Machine Scale Sets VM 부팅 진단 API 및 명령은 Flexible 오케스트레이션 모드 인스턴스와 함께 사용되지 않습니다.

### <a name="vm-extensions"></a>VM 확장
Uniform 오케스트레이션 모드 인스턴스를 대상으로 하는 확장 대신 표준 가상 머신을 대상으로 하는 확장을 사용합니다.


## <a name="a-comparison-of-flexible-uniform-and-availability-sets"></a>Flexible, Uniform, 가용성 집합의 비교
다음 표에서는 Flexible 오케스트레이션 모드, Uniform 오케스트레이션 모드 및 해당 기능별 가용성 집합을 비교합니다.

| 기능  | Flexible 오케스트레이션에서 지원됨(미리 보기)  | Uniform 오케스트레이션에서 지원됨(일반 공급)  | AvSets에서 지원됨(일반 공급)  |
|-|-|-|-|
| 가상 머신 유형  | 표준 Azure IaaS VM (Microsoft.compute /virtualmachines)  | 확장 집합 특정 VM(Microsoft.compute /virtualmachinescalesets/virtualmachines)  | 표준 Azure IaaS VM (Microsoft.compute /virtualmachines)  |
| 지원되는 SKU  | D 시리즈, E 시리즈, F 시리즈, A 시리즈, B 시리즈, Intel, AMD  | 모든 SKU  | 모든 SKU  |
| 가용성 영역  | 필요에 따라 단일 가용성 영역에 모든 인스턴스를 지정합니다  | 1, 2 또는 3개의 가용성 영역에 인스턴스를 지정합니다  | 지원되지 않음  |
| 장애 도메인 – 최대 분산(Azure는 최대 확산 인스턴스)  | 예  | 예  | 아니요  |
| 장애 도메인 – 고정 분산  | 2-3 FD(지역별 최대값에 따라 다름), 영역 배포의 경우 1  | 2, 3, 5 FD, 영역 배포의 경우 1, 5  | 2-3 FD(지역별 최대값에 따라 다름)  |
| 업데이트 도메인  | 사용되지 않으며 FD별로 수행되는 플랫폼 유지 관리 | 5 업데이트 도메인  | 최대 20개의 업데이트 도메인  |
| 가용성 SLA  | 지금은 아님  | 단일 배치 그룹에서 FD가 1을 초과하는 경우 99.95%, 여러 영역에 분산된 인스턴스의 경우 99.99%  | 99.95%  |
| VM, NIC, 디스크에 대한 모든 컨트롤  | 예  | 가상 머신 확장 집합 VM API를 사용한 제한된 컨트롤  | Yes  |
| 자동 크기 조정(수동, 메트릭 기반, 일정 기반)  | 예  | 예  | 아니요  |
| 특정 장애 도메인에 VM 할당  | 예  | 아니요  | 아니요  |
| VM 인스턴스를 삭제할 때 NIC 및 디스크 자동 제거  | 예  | 예  | 예  |
| 업그레이드 정책(VM 확장 집합)  | 아니요, 업그레이드 정책은 생성 중에 null 또는 []이어야 합니다  | 자동, 롤링, 수동  | 해당 없음  |
| 자동 이미지 기반 OS 업데이트  | 아니요  | 예  | 해당 없음  |
| 게스트 보안 패치 중  | 예  | 아니요  | 예  |
| 알림 종료(VM 확장 집합)  | 예  | 예  | 해당 없음  |
| 인스턴스 복구(VM 확장 집합)  | 예  | 예  | 해당 없음  |
| 가속화된 네트워킹  | 예  | 예  | 예  |
| 스폿 인스턴스 및 가격 책정   | 예, 스폿 및 일반 우선 순위 인스턴스를 모두 사용할 수 있습니다  | 예, 인스턴스는 모두 스폿이거나 모두 정상이어야 합니다  | 아니요, 일반 우선 순위 인스턴스만 해당됩니다.  |
| 혼합 운영 체제  | 예, Linux 및 Windows는 동일한 Flexible 확장 집합에 상주할 수 있습니다.  | 아니요, 인스턴스는 동일한 운영 체제입니다  | 예, Linux 및 Windows는 동일한 Flexible 확장 집합에 상주할 수 있습니다.  |
| 애플리케이션 상태 모니터링  | 애플리케이션 상태 확장  | 애플리케이션 상태 확장 또는 Azure Load Balancer 프로브  | 애플리케이션 상태 확장  |
| UltraSSD 디스크   | 예  | 예, 영역 배포에만 해당됩니다.  | 예  |
| Infiniband   | 예  | 예, 단일 배치 그룹만 해당됩니다.  | 예  |
| 쓰기 가속기   | 예  | 예  | 예  |
| 근접 배치 그룹   | 예  | 예  | 예  |
| Azure 전용 호스트   | 예  | 예  | 예  |
| Basic SLB   | 예  | 예  | 예  |
| Azure Load Balancer 표준 SKU  | 예  | 예  | 예  |
| Application Gateway  | 예  | 예  | 예  |
| 유지 관리 컨트롤   | 예  | 예  | 예  |
| 집합의 VM 나열  | 예  | 예  | 예, AvSet의 VM을 나열합니다.  |
| Azure Alerts  | 예  | 예  | 예  |
| VM 인사이트  | 예  | 예  | 예  |
| Azure Backup  | 예  | 아니요  | 예  |
| Azure Site Recovery  | 예(PowerShell을 통해)  | 아니요  | 예  |
| Service Fabric  | 예  | 예  | 아니요  |
| Azure Kubernetes Service(AKS) / AKE  | 아니요  | 예  | 아니요  |


## <a name="get-started-with-flexible-orchestration-mode"></a>Flexible 오케스트레이션 모드 시작하기

가상 머신 확장 집합에 대한 [유연한 오케스트레이션 모드](..\virtual-machines\flexible-virtual-machine-scale-sets.md)를 등록하고 시작합니다. 


## <a name="frequently-asked-questions"></a>질문과 대답

- **Flexible 오케스트레이션에서 지원하는 규모는 얼마나 되나요?**

    Flexible 오케스트레이션 모드에서 확장 집합에 최대 1000개의 VM을 추가할 수 있습니다.

- **가용성 집합 또는 Uniform 오케스트레이션과 Flexible 오케스트레이션이 있는 가용성의 차이점은 무엇인가요?**

    | 가용성 특성  | Flexible 오케스트레이션  | Uniform 오케스트레이션  | 가용성 집합  |
    |-|-|-|-|
    | 가용성 영역 간 배포  | 예  | 예  | 예  |
    | 지역 내에서 장애 도메인 가용성 보장  | 예, 최대 1000개 인스턴스는 해당 지역에서 최대 3개의 장애 도메인에 걸쳐 분산될 수 있습니다. 최대 장애 도메인 수는 지역에 따라 다릅니다.  | 예, 최대 100 인스턴스  | 예, 최대 200 인스턴스  |
    | 배치 그룹  | Flexible 모드는 항상 여러 배치 그룹을 사용합니다(Singleementgroup = false).  | 단일 배치 그룹 또는 여러 배치 그룹을 선택할 수 있습니다. | 해당 없음  |
    | 업데이트 도메인  | 없음, 유지 관리 또는 호스트 업데이트가 장애 도메인에 의해 수행되지 않음  | 최대 5개의 업데이트 도메인  | 최대 20개의 업데이트 도메인  |

- **장애 도메인 가용성이 보장되는 절대 최대 인스턴스 수는 어떻게 되나요?**

    | 기능  | Flexible 오케스트레이션에서 지원됨(미리 보기)  | Uniform 오케스트레이션에서 지원됨(일반 공급)  | AvSets에서 지원됨(일반 공급)  |
    |-|-|-|-|
    | 최대 인스턴스 수(FD 가용성 보장 포함)  | 1000  | 3000  | 200  |


## <a name="troubleshoot-scale-sets-with-flexible-orchestration"></a>Flexible 오케스트레이션을 사용하여 확장 집합 문제 해결
문제 해결 시나리오에 적합한 솔루션을 찾습니다.

```
InvalidParameter. The value 'False' of parameter 'singlePlacementGroup' is not allowed. Allowed values are: True
```

**원인:** 구독이 Flexible 오케스트레이션 모드 공개 미리 보기에 등록되어 있지 않습니다.

**해결 방법:** 위의 지침에 따라 Flexible 오케스트레이션 모드 공개 미리 보기에 등록합니다.

```
InvalidParameter. The specified fault domain count 2 must fall in the range 1 to 1.
```

**원인:** `platformFaultDomainCount` 선택한 지역 또는 영역에 대한 매개 변수가 잘못되었습니다.

**해결 방법:** 유효한 `platformFaultDomainCount` 값을 선택해야 합니다. 영역 배포의 경우 최대 `platformFaultDomainCount` 값은 1입니다. 영역을 지정하지 않는 지역 배포의 경우 최대 `platformFaultDomainCount`는 지역에 따라 다릅니다. 지역별 최대 장애 도메인 수를 확인하는 [스크립트에 대한 VM의 가용성 관리](../virtual-machines/availability.md)를 참조하세요.

```
OperationNotAllowed. Deletion of Virtual Machine Scale Set is not allowed as it contains one or more VMs. Please delete or detach the VM(s) before deleting the Virtual Machine Scale Set.
```

**원인:** 하나 이상의 가상 머신과 연결된 Flexible 오케스트레이션 모드에서 확장 집합을 삭제하려고 합니다.

**해결 방법:** Flexible 오케스트레이션 모드에서 확장 집합에 연결된 모든 가상 머신을 삭제한 다음 확장 집합을 삭제할 수 있습니다.

```
InvalidParameter. The value 'True' of parameter 'singlePlacementGroup' is not allowed. Allowed values are: False.
```
**원인:** 구독이 Flexible 오케스트레이션 모드 미리 보기에 등록되어 있습니다. 그러나 `singlePlacementGroup` 매개 변수는 *True* 로 설정됩니다.

**해결 방법:** `singlePlacementGroup`을 *False* 로 설정해야 합니다.


## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"]
> [확장 집합에 애플리케이션을 배포하는 방법을 알아봅니다.](virtual-machine-scale-sets-deploy-app.md)
