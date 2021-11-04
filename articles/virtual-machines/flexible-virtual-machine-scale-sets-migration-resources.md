---
title: 유연한 오케스트레이션에 서 가상 머신 확장 집합으로 배포 및 리소스 마이그레이션
description: 유연한 오케스트레이션에 서 가상 머신 확장 집합으로 배포 및 리소스를 마이그레이션하는 방법에 대해 알아봅니다.
author: fitzgeraldsteele
ms.author: fisteele
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: flexible-scale-sets
ms.date: 10/14/2021
ms.reviewer: jushiman
ms.custom: mimckitt, devx-track-azurecli, vmss-flex
ms.openlocfilehash: b4662c2fd6c5a0950bc3b2b6f336fabab12fc1aa
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131008483"
---
# <a name="migrate-deployments-and-resources-to-virtual-machine-scale-sets-in-flexible-orchestration"></a>유연한 오케스트레이션에 서 가상 머신 확장 집합으로 배포 및 리소스 마이그레이션 

**적용 대상:** :heavy_check_mark: 유연한 확장 집합

가용성 집합 처럼 가상 머신 확장 집합을 사용 하면 여러 장애 도메인에 가상 머신을 분산할 수 있습니다. 유연한 오케스트레이션을 사용 하는 가상 머신 확장 집합을 사용 하면 [단일 오케스트레이션 모드에서 가상 머신 확장 집합](../virtual-machine-scale-sets/overview.md) 의 확장성을 [가용성 집합](availability-set-overview.md)의 지역별 가용성 보증과 결합할 수 있습니다. 이 문서에서는 가상 머신 확장 집합에 대 한 유연한 오케스트레이션 모드로 전환할 때의 마이그레이션 고려 사항을 설명 합니다. 

## <a name="update-availability-set-deployments-templates-and-scripts"></a>가용성 집합 배포 템플릿 및 스크립트 업데이트

먼저 [Azure CLI](flexible-virtual-machine-scale-sets-cli.md), [Azure PowerShell](flexible-virtual-machine-scale-sets-powershell.md)또는 [ARM 템플릿을](flexible-virtual-machine-scale-sets-rest-api.md)통해 자동 크기 조정 프로필을 사용 하 여 가상 머신 확장 집합을 만들어야 합니다. Azure Portal 자동 크기 조정 프로필을 사용 하 여 가상 머신 확장 집합을 만들 수 있습니다. 크기 자동 조정 프로필이 필요 하지 않거나 필요 하지 않고 [Azure Portal](flexible-virtual-machine-scale-sets-portal.md)를 사용 하 여 크기 집합을 만들려는 경우 초기 용량을 0으로 설정할 수 있습니다. 
 
가상 머신 확장 집합에 대 한 장애 도메인 수를 지정 해야 합니다. 지역 (비 영역) 배포의 경우 가상 머신 확장 집합은 가용성 집합과 동일한 장애 도메인 보증을 제공 합니다. 그러나 최대 1000 개의 인스턴스로 확장할 수 있습니다. 여러 가용성 영역에서 인스턴스를 분산 하는 영역 배포의 경우 장애 도메인 수를 1로 설정 해야 합니다.

업데이트 도메인은 유연한 오케스트레이션 모드에서 더 이상 사용 되지 않습니다. 범용 Sku를 사용 하는 대부분의 플랫폼 업데이트는 실시간 마이그레이션를 통해 수행 되며 인스턴스를 다시 부팅할 필요가 없습니다. 플랫폼 유지 관리를 위해 인스턴스를 다시 부팅 해야 하는 경우 업데이트는 장애 도메인에 의해 장애 도메인에 적용 됩니다.  
    
가상 머신 확장 집합에 대 한 유연한 오케스트레이션은 여러 가용성 영역에 인스턴스를 배포 하는 것도 지원 합니다. 여러 가용성 영역에 걸쳐 분산 되도록 VM 배포를 업데이트 하는 것이 좋습니다. 

이 프로세스의 마지막 단계는 가상 머신을 만드는 것입니다. 가용성 집합을 지정 하는 대신 가상 머신 확장 집합을 지정 합니다. 필요에 따라 VM을 추가 하려는 가용성 영역 또는 장애 도메인을 지정할 수 있습니다. 


## <a name="migrate-existing-availability-set-vms"></a>기존 가용성 집합 Vm 마이그레이션 

현재 가용성 집합의 기존 인스턴스를 가상 머신 확장 집합으로 직접 이동 하는 자동화 된 도구가 없습니다. 그러나 기존 인스턴스를 유연한 확장 집합으로 마이그레이션하기 위해 활용할 수 있는 몇 가지 전략이 있습니다. 

### <a name="bluegreen-or-side-by-side-migration"></a>파랑/녹색 또는 병렬 마이그레이션 

1. 유사한 구성을 사용 하는 새 확장 집합 가상 머신 인스턴스를 가용성의 Vm과 동일한 리소스 그룹, 가상 네트워크, 부하 분산 장치 등으로 가져옵니다. 
1. 새 확장 집합 인스턴스를 사용 하기 위해 데이터, 네트워크 트래픽 등 마이그레이션 
1. 응용 프로그램에 대 한 확장 집합 Vm이 실행 되는 상태에서 원래 가용성 집합 가상 컴퓨터의 할당을 취소 하거나 제거 합니다.

### <a name="replace-vm-instances"></a>VM 인스턴스 바꾸기 

1. 가상 컴퓨터에서 유지 하려는 매개 변수 (이름, NIC ID, OS 및 데이터 디스크 Id, VM 구성 설정, 장애 도메인 배치 등)를 확인 합니다. 
1. 가용성 집합 가상 머신을 삭제 합니다. VM에 대 한 Nic 및 디스크는 삭제 되지 않습니다.  
1. 원래 VM의 매개 변수를 사용 하 여 새 가상 머신 개체를 만듭니다. 
    - NIC ID 
    - OS 및 데이터 디스크 
    - 장애 도메인 배치 
    - 기타 VM 설정 


## <a name="update-uniform-scale-sets-deployment-templates-and-scripts"></a>단일 확장 집합 배포 템플릿 및 스크립트 업데이트

유연한 오케스트레이션을 사용 하도록 Uniform virtual machine scale sets 배포 템플릿 및 스크립트를 업데이트 합니다. 프로세스를 성공적으로 완료 하려면 템플릿에서 다음 요소를 변경 합니다. 

- Remove `LoadBalancerNATPool` (flex의 경우 유효 하지 않음) 
- 오버 프로 비전 매개 변수 제거 (flex의 경우 유효 하지 않음) 
- 제거 `upgradePolicy` (flex의 경우 유효 하지 않음) 
- 계산 API 버전을 **2021-03-01** 로 업데이트 
- 오케스트레이션 모드 추가 `flexible` 
- `platformFaultDomainCount` 필수 
- `singlePlacementGroup`= false 필수 
- **2021-11-01** 이상에 네트워크 API 버전 추가 
- IP `configuration.properties.primary` 를 *true* 로 설정 합니다 (아웃 바운드 규칙에 필요).


## <a name="migrate-existing-uniform-scale-sets"></a>기존 균일 확장 집합 마이그레이션 

현재는 기존 인스턴스를 직접 이동 하거나 유연한 가상 머신 확장 집합으로 균일 한 확장 집합을 업그레이드 하는 자동화 된 도구가 없습니다. 그러나 기존 인스턴스를 유연한 확장 집합으로 마이그레이션하기 위해 활용할 수 있는 전략은 다음과 같습니다.

### <a name="bluegreen-or-side-by-side-migration"></a>파랑/녹색 또는 병렬 마이그레이션 

1. 동일한 리소스 그룹, 가상 네트워크, 부하 분산 장치 등으로 구성 된 새 유연한 오케스트레이션 모드 확장 집합을 균일 한 오케스트레이션 모드의 원래 크기 집합으로
1. 새 확장 집합 인스턴스를 사용 하기 위해 데이터, 네트워크 트래픽 등 마이그레이션 
1. 응용 프로그램에 대 한 확장 집합 가상 머신을 실행 하는 원래의 균일 한 확장 집합 가상 머신 규모를 축소 또는 제거 합니다.


## <a name="flexible-scale-sets-considerations"></a>유연한 확장 집합 고려 사항 

유연한 오케스트레이션을 사용 하는 가상 머신 확장 집합을 사용 하면 가용성 집합의 지역별 가용성을 보장 하는 [단일 오케스트레이션에 서 가상 머신 확장 집합](../virtual-machine-scale-sets/overview.md) 의 확장성을 결합할 수 있습니다. 다음은 유연한 오케스트레이션 모드로 작업할 때 고려해 야 할 주요 사항입니다. 

### <a name="create-scalable-network-connectivity"></a>확장 가능한 네트워크 연결 만들기 
<!-- the following is an important link to use in FLEX documentation to reference this section:
/virtual-machines/flexible-virtual-machine-scale-sets-migration-resources.md#create-scalable-network-connectivity
-->

네트워킹 아웃 바운드 액세스 동작은 확장 집합 내에서 가상 컴퓨터를 만드는 방법에 따라 달라 집니다. **수동으로 추가 된 VM 인스턴스에** 는 기본 아웃 바운드 연결 액세스 권한이 있습니다. **암시적으로 생성 된 VM 인스턴스에** 는 기본 액세스가 없습니다. 

기본 네트워크 보안을 향상 시키기 위해 자동 크기 조정 **프로필을 통해 암시적으로 생성 된 가상 머신 인스턴스에는 기본 아웃 바운드 액세스가 없습니다**. 암시적으로 생성 된 VM 인스턴스와 함께 가상 머신 확장 집합을 사용 하려면 다음 방법 중 하나를 사용 하 여 아웃 바운드 액세스를 명시적으로 정의 해야 합니다. 

- 대부분의 시나리오에서는 [NAT Gateway를 서브넷에 연결](../virtual-network/nat-gateway/tutorial-create-nat-gateway-portal.md)하는 것이 좋습니다.
- 보안 요구 사항이 높은 시나리오, Azure Firewall이나 NVA(네트워크 가상 어플라이언스)를 사용할 때는 방화벽을 통해 사용자 지정 사용자 정의 경로를 다음 홉으로 지정할 수 있습니다. 
- 인스턴스는 표준 SKU Azure Load Balancer의 백 엔드 풀에 있습니다. 
- 인스턴스 네트워크 인터페이스에 공용 IP 주소를 연결합니다. 

명시적 아웃바운드 연결을 필요로 하는 일반적인 시나리오는 다음과 같습니다. 

- Windows VM을 활성화하려면 VM 인스턴스에서 Windows 활성화 키 관리 서비스(KMS)로의 아웃바운드 연결을 정의해야 합니다. 더 많은 정보는 [Azure Windows VM 활성화 문제 해결](/troubleshoot/azure/virtual-machines/troubleshoot-activation-problems)을 참조하세요.  
- 스토리지 계정 또는 Key Vault에 액세스합니다. Azure 서비스 연결은 [프라이빗 링크](../private-link/private-link-overview.md)를 통해 설정할 수도 있습니다. 
- 업데이트를 Windows 합니다.
- Linux 패키지 관리자에 대 한 액세스. 

아웃 바운드 연결 정의에 대 한 자세한 내용은 [Azure의 기본 아웃 바운드 액세스](../virtual-network/ip-services/default-outbound-access.md) 를 참조 하세요.

NIC를 명시적으로 만드는 단일 인스턴스 Vm을 사용 하는 경우 기본 아웃 바운드 액세스가 제공 됩니다. 균일 한 오케스트레이션 모드의 가상 머신 확장 집합에는 기본 아웃 바운드 연결도 있습니다. 


> [!IMPORTANT]
> 명시적인 아웃 바운드 네트워크 연결이 있는지 확인 합니다. [Azure의 가상 네트워크 및 가상 머신에서](../virtual-network/network-overview.md) 이에 대해 자세히 알아보고 azure의 네트워킹 [모범 사례를 준수](../virtual-network/concepts-and-best-practices.md)하는지 확인 합니다. 


### <a name="assign-fault-domain-during-vm-creation"></a>VM을 만드는 동안 장애 도메인 할당
Flexible 오케스트레이션 확장 집합에 대한 장애 도메인 수를 선택할 수 있습니다. 기본값으로 Flexible 확장 집합에 VM을 추가하는 경우 Azure는 장애 도메인 간에 인스턴스를 균등하게 분산합니다. Azure에서 장애 도메인을 할당하는 것이 좋지만 고급 또는 문제 해결 시나리오의 경우 이 기본 동작을 재정의하고 인스턴스가 배치될 장애 도메인을 지정할 수 있습니다.

```azurecli-interactive
az vm create –vmss "myVMSS"  –-platform_fault_domain 1
```

### <a name="instance-naming"></a>인스턴스 이름 지정
VM을 만들어 Flexible 확장 집합에 추가하는 경우 Azure 명명 규칙 내에서 인스턴스 이름을 완전히 제어할 수 있습니다. 자동 크기 조정을 통해 VM이 확장 집합에 자동으로 추가되는 경우 접두사를 제공하고, Azure는 이름 끝에 고유 번호를 추가합니다. 

### <a name="list-scale-sets-vm-api-changes"></a>확장 집합 VM API 변경 목록
Virtual Machine Scale Sets를 사용하여 확장 집합에 속한 인스턴스를 나열할 수 있습니다. LIST Virtual Machine Scale Sets VM 명령은 Flexible 오케스트레이션을 사용하여 확장 집합 VM ID의 목록을 제공합니다. 그런 다음 GET Virtual Machine Scale Sets VM 명령을 호출하여 확장 집합에서 VM 인스턴스로 작업하는 방법에 대한 자세한 정보를 얻을 수 있습니다. VM에 대한 전체 세부 정보를 얻으려면 표준 GET VM 명령 또는 [Azure Resource Graph](../governance/resource-graph/overview.md)를 사용합니다.


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


### <a name="scale-sets-vm-batch-operations"></a>확장 집합 VM 일괄 처리 작업
표준 VM 명령을 사용하여 Virtual Machine Scale Set VM API 대신 인스턴스를 시작, 중지, 다시 시작, 삭제합니다. Virtual Machine Scale Set VM Batch 작업(모두 시작, 모두 중지, 이미지로 다시 설치 등)은 Flexible 오케스트레이션 모드에서 사용되지 않습니다.


### <a name="monitor-application-health"></a>애플리케이션 상태 모니터링
애플리케이션 상태 모니터링을 통해 애플리케이션은 Azure에 하트비트를 제공하여 애플리케이션이 정상 상태인지 비정상 상태인지 확인할 수 있습니다. Azure는 비정상 상태인 VM 인스턴스를 자동으로 바꿀 수 있습니다. Flexible 확장 집합 인스턴스의 경우 가상 머신에서 애플리케이션 상태 확장을 설치하고 구성해야 합니다. Uniform 확장 집합 인스턴스의 경우 애플리케이션 상태 확장을 사용하거나 Azure Load Balancer 사용자 지정 상태 프로브를 사용하여 상태를 측정할 수 있습니다.


### <a name="retrieve-boot-diagnostics-data"></a>부팅 진단 데이터 검색
표준 VM API 및 명령을 사용하여 인스턴스 부팅 진단 데이터 및 스크린샷을 검색합니다. Virtual Machine Scale Sets VM 부팅 진단 API 및 명령은 Flexible 오케스트레이션 모드 인스턴스와 함께 사용되지 않습니다.


### <a name="vm-extensions"></a>VM 확장
Uniform 오케스트레이션 모드 인스턴스를 대상으로 하는 확장 대신 표준 가상 머신을 대상으로 하는 확장을 사용합니다.


### <a name="protect-instances-from-delete"></a>삭제로부터 인스턴스 보호

유연한 오케스트레이션 모드의 가상 머신 확장 집합에는 현재 인스턴스 보호 옵션이 없습니다. 가상 머신 확장 집합에서 자동 크기 조정을 사용하도록 설정한 경우 일부 VM은 크기 조정 프로세스 중에 삭제될 위험이 있습니다. 특정 VM 인스턴스를 삭제로부터 보호하려면 Azure Resource Manager [잠금을](../azure-resource-manager/management/lock-resources.md)사용합니다.



## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"]
> [균일한 오케스트레이션 모드와 유연한 오케스트레이션 모드 간의 API 차이점을 비교합니다.](../virtual-machine-scale-sets/orchestration-modes-api-comparison.md)