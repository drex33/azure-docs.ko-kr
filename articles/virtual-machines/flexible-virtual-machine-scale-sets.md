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
ms.openlocfilehash: 509b485abe9eca54354fa7d5ad7590ffe1a37794
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129995870"
---
# <a name="preview-flexible-orchestration-for-virtual-machine-scale-sets-in-azure"></a>미리 보기: 가상 머신 확장 집합에 대한 유연한 오케스트레이션

**적용 대상:** :heavy_check_mark: 유연한 확장 집합

유연한 오케스트레이션을 사용하는 가상 머신 확장 집합을 사용하면 [가상 머신 확장 집합](../virtual-machine-scale-sets/overview.md) 의 확장성과 [가용성 집합](availability-set-overview.md)의 지역별 가용성 보장을 결합할 수 있습니다.

Azure 가상 머신 확장 집합을 사용하면 부하 분산된 VM의 그룹을 만들고 관리할 수 있습니다. VM 인스턴스의 수는 요구 또는 정의된 일정에 따라 자동으로 늘리거나 줄일 수 있습니다. 확장 집합에서 제공하는 주요 이점은 다음과 같습니다.
- 손쉬운 여러 VM 만들기 및 관리
- 장애 도메인 간에 VM을 분산하여 고가용성 및 애플리케이션 복원력을 제공합니다
- 리소스 수요 변화에 따라 자동으로 애플리케이션 크기 조정
- 대규모 작업

Flexible 오케스트레이션을 사용하면 Azure는 Azure VM 에코시스템에서 통합된 환경을 제공합니다. Flexible 오케스트레이션은 지역 또는 가용성 영역 내의 장애 도메인에 VM을 분산하여 고가용성 보증(최대 1000개 VM)을 제공합니다. 이렇게 하면 다음을 비롯한 쿼럼 기반 또는 상태 저장 워크로드를 실행하는 데 필수적인 장애 도메인 격리를 유지하면서 애플리케이션을 스케일 아웃할 수 있습니다.
- 쿼럼 기반 워크로드
- 오픈 소스 데이터베이스
- 상태 저장 애플리케이션
- 고가용성 및 대규모를 필요로 하는 서비스
- 가상 머신 유형을 혼합하거나 스폿을 활용하고 온-디맨드 VM을 함께 활용하려는 서비스
- 기존 가용성 집합 애플리케이션

단일 확장 집합과 [오케스트레이션 모드](../virtual-machine-scale-sets/virtual-machine-scale-sets-orchestration-modes.md)의 유연한 확장 집합 간의 차이점에 대해 자세히 알아보세요.

> [!IMPORTANT]
> Flexible 오케스트레이션 모드의 가상 머신 확장 집합은 현재 공개 미리 보기로 제공됩니다. 아래에서 자세하게 설명하는 공개 미리 보기 기능을 사용 하려면 옵트인 프로시저를 사용해야 합니다. 이 미리 보기 버전은 서비스 수준 약정 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

> [!CAUTION]
> 오케스트레이션 모드는 확장 집합을 만들 때 정의되며 나중에 변경하거나 업데이트할 수 없습니다.


## <a name="register-for-flexible-orchestration-mode"></a>Flexible 오케스트레이션 모드 등록
Flexible 오케스트레이션 모드로 가상 머신 확장 집합을 배포하려면 먼저 미리 보기 기능에 대한 구독을 등록해야 합니다. 등록을 완료하는 데 몇 분 정도 걸릴 수 있습니다. Azure Portal, Azure PowerShell, Azure CLI를 사용하여 등록할 수 있습니다.

### <a name="azure-portal"></a>Azure portal

1. https://portal.azure.com 에서 Azure Portal에 로그인합니다.
1. **구독** 으로 이동합니다.
1. 구독의 이름을 선택함으로써 유연한 오케스트레이션 모드에서 확장 집합을 만들려는 구독에 대한 세부 정보 페이지로 이동합니다.
1. **설정** 아래 메뉴에서 **미리 보기 기능** 을 선택합니다.
1. 사용하도록 설정할 4개의 오케스트레이터 기능(*VMOrchestratorSingleFD*, *VMOrchestratorMultiFD*, *VMScaleSetFlexPreview*, *SkipPublicIpWriteRBACCheckForVMNetworkInterfaceConfigurationsPublicPreview*)을 선택합니다.
1. **등록** 을 선택합니다.

구독에 대한 기능을 등록한 후에는 컴퓨팅 리소스 공급자에 변경 내용을 전파하여 옵트인 프로세스를 완료합니다. 

1. **설정** 아래의 메뉴에서 **리소스 공급자** 를 선택합니다.
1. `Microsoft.compute`를 선택합니다.
1. **다시 등록** 을 선택합니다.


### <a name="azure-powershell"></a>Azure PowerShell
[Register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) cmdlet을 사용하여 구독에서 미리 보기를 사용하도록 설정합니다.

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName VMOrchestratorMultiFD -ProviderNamespace Microsoft.Compute `
Register-AzProviderFeature -FeatureName VMOrchestratorSingleFD -ProviderNamespace Microsoft.Compute `
Register-AzProviderFeature -FeatureName VMScaleSetFlexPreview -ProviderNamespace Microsoft.Compute `
Register-AzProviderFeature -FeatureName SkipPublicIpWriteRBACCheckForVMNetworkInterfaceConfigurationsPublicPreview -ProviderNamespace Microsoft.Compute
```

기능 등록에는 최대 15분이 걸립니다. 등록 상태를 확인하는 방법은 다음과 같습니다.

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName VMOrchestratorMultiFD -ProviderNamespace Microsoft.Compute
```

기능이 구독에 등록되면 변경 내용을 Compute 리소스 공급자로 전파하여 옵트인 프로세스를 완료합니다.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
[az feature register](/cli/azure/feature#az_feature_register)를 사용하여 구독에서 미리 보기를 사용하도록 설정합니다.

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name VMOrchestratorMultiFD
az feature register --namespace microsoft.compute --name VMOrchestratorSingleFD
az feature register --namespace Microsoft.Compute --name VMScaleSetFlexPreview 
az feature register --namespace Microsoft.Compute --name SkipPublicIpWriteRBACCheckForVMNetworkInterfaceConfigurationsPublicPreview
```

기능 등록에는 최대 15분이 걸립니다. 등록 상태를 확인하는 방법은 다음과 같습니다.

```azurecli-interactive
az feature show --namespace Microsoft.Compute --name VMOrchestratorMultiFD
```

기능이 구독에 등록되면 변경 내용을 Compute 리소스 공급자로 전파하여 옵트인 프로세스를 완료합니다.

```azurecli-interactive
az provider register --namespace Microsoft.Compute
```

## <a name="get-started-with-flexible-orchestration-mode"></a>Flexible 오케스트레이션 모드 시작하기

[Azure Portal](flexible-virtual-machine-scale-sets-portal.md), [Azure CLI](flexible-virtual-machine-scale-sets-cli.md), [Azure PowerShell](flexible-virtual-machine-scale-sets-powershell.md), [ARM Template](flexible-virtual-machine-scale-sets-rest-api.md)을 통해 확장 집합에 대한 유연한 오케스트레이션 모드를 시작하세요. 


## <a name="add-instances-with-autoscaling-or-manually"></a>자동 크기 조정 또는 수동으로 인스턴스
유연한 오케스트레이션이 있는 가상 머신 확장 집합은 여러 VM을 관리하기 위한 씬 오케스트레이션 계층으로 작동합니다. 여러 가지 방법으로 확장 집합을 통해 관리할 VM을 추가할 수 있습니다.

- **인스턴스 수 설정**

    유연한 오케스트레이션을 사용하여 확장 집합을 만들 때, 확장에 사용할 템플릿을 설명하는 VM 프로필 또는 템플릿을 정의합니다. 그런 다음 용량 매개 변수를 설정하여 확장 집합에서 관리하는 VM 인스턴스 수를 늘리거나 줄일 수 있습니다. 

- **메트릭 또는 일정에 따라 자동 크기 조정** 

    자동 크기 조정 규칙을 설정하여 메트릭 또는 일정에 따라 용량을 늘리거나 줄일 수 있습니다. [자동 크기 조정 지원 가상 머신 확장 집합](..\virtual-machine-scale-sets\virtual-machine-scale-sets-autoscale-overview.md)을 참조하세요. 

- **VM을 만들 때 확장 집합 지정**

    VM을 만들 때 가상 머신 확장 집합에 추가되도록 선택적으로 지정할 수 있습니다. VM은 VM을 만들 때만 확장 집합에 추가할 수 있습니다.

유연한 오케스트레이션 모드는 Azure에 배포 된 모든 IaaS Vm의 90%를 포함 하는 [메모리 보존 업데이트 또는 실시간 마이그레이션을](../virtual-machines/maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot)지 원하는 VM sku와 함께 사용할 수 있습니다. 여기에는 B-, D-, E-및 F 시리즈 Vm과 같은 일반적인 용도의 크기 패밀리가 포함 됩니다. 현재 유연한 모드는 G-, H-, L-, M, N 시리즈 Vm을 포함 하 여 메모리 보존 업데이트를 지원 하지 않는 VM Sku 또는 패밀리를 통해 오케스트레이션 할 수 없습니다. [계산 리소스 SKU API](/rest/api/compute/resource-skus/list) 를 사용 하 여 특정 VM SKU가 지원 되는지 여부를 확인할 수 있습니다.

```azurecli-interactive
az vm list-skus -l eastus --size standard_d2s_v3 --query "[].capabilities[].[name, value]" -o table
```

## <a name="explicit-network-outbound-connectivity-required"></a>명시적인 네트워크 아웃바운드 연결 필요 

기본 네트워크 보안을 향상하기 위해 유연한 오케스트레이션을 사용하는 가상 머신 확장 집합에는 자동 크기 조정 프로필을 통해 암시적으로 만들어진 인스턴스가 다음 방법 중 하나를 통해 명시적으로 정의된 아웃바운드 연결이 필요합니다. 

- 대부분의 시나리오에서는 [NAT Gateway를 서브넷에 연결](../virtual-network/nat-gateway/tutorial-create-nat-gateway-portal.md)하는 것이 좋습니다.
- 보안 요구 사항이 높은 시나리오, Azure Firewall이나 NVA(네트워크 가상 어플라이언스)를 사용할 때는 방화벽을 통해 사용자 지정 사용자 정의 경로를 다음 홉으로 지정할 수 있습니다. 
- 인스턴스는 표준 SKU Azure Load Balancer의 백 엔드 풀에 있습니다. 
- 인스턴스 네트워크 인터페이스에 공용 IP 주소를 연결합니다. 

단일 인스턴스 VM 및 균일한 오케스트레이션이 있는 가상 머신 확장 집합을 사용하는 경우, 아웃바운드 연결이 자동으로 제공됩니다. 

명시적 아웃바운드 연결을 필요로 하는 일반적인 시나리오는 다음과 같습니다. 

- Windows VM을 활성화하려면 VM 인스턴스에서 Windows 활성화 키 관리 서비스(KMS)로의 아웃바운드 연결을 정의해야 합니다. 더 많은 정보는 [Azure Windows VM 활성화 문제 해결](/troubleshoot/azure/virtual-machines/troubleshoot-activation-problems)을 참조하세요.  
- 스토리지 계정 또는 Key Vault에 액세스합니다. Azure 서비스 연결은 [프라이빗 링크](../private-link/private-link-overview.md)를 통해 설정할 수도 있습니다. 

안전한 아웃바운드 연결을 정의하는 방법에 대한 세부 정보는 [Azure에서 기본값 아웃바운드 액세스](https://aka.ms/defaultoutboundaccess)를 참조하세요.


## <a name="assign-fault-domain-during-vm-creation"></a>VM을 만드는 동안 장애 도메인 할당
Flexible 오케스트레이션 확장 집합에 대한 장애 도메인 수를 선택할 수 있습니다. 기본값으로 Flexible 확장 집합에 VM을 추가하는 경우 Azure는 장애 도메인 간에 인스턴스를 균등하게 분산합니다. Azure에서 장애 도메인을 할당하는 것이 좋지만 고급 또는 문제 해결 시나리오의 경우 이 기본 동작을 재정의하고 인스턴스가 배치될 장애 도메인을 지정할 수 있습니다.

```azurecli-interactive
az vm create –vmss "myVMSS"  –-platform_fault_domain 1
```

## <a name="instance-naming"></a>인스턴스 이름 지정
VM을 만들어 Flexible 확장 집합에 추가하는 경우 Azure 명명 규칙 내에서 인스턴스 이름을 완전히 제어할 수 있습니다. 자동 크기 조정을 통해 VM이 확장 집합에 자동으로 추가되는 경우 접두사를 제공하고, Azure는 이름 끝에 고유 번호를 추가합니다. 

## <a name="list-scale-sets-vm-api-changes"></a>확장 집합 VM API 변경 목록
Virtual Machine Scale Sets를 사용하여 확장 집합에 속한 인스턴스를 나열할 수 있습니다. LIST Virtual Machine Scale Sets VM 명령은 Flexible 오케스트레이션을 사용하여 확장 집합 VM ID의 목록을 제공합니다. 그런 다음 GET Virtual Machine Scale Sets VM 명령을 호출하여 확장 집합에서 VM 인스턴스로 작업하는 방법에 대한 자세한 정보를 얻을 수 있습니다. VM에 대한 전체 세부 정보를 얻으려면 표준 GET VM 명령 또는 [Azure Resource Graph](../governance/resource-graph/overview.md)를 사용합니다.


## <a name="query-instances-for-power-state"></a>전원 상태에 대한 쿼리 인스턴스
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


## <a name="scale-sets-vm-batch-operations"></a>확장 집합 VM Batch 작업
표준 VM 명령을 사용하여 Virtual Machine Scale Set VM API 대신 인스턴스를 시작, 중지, 다시 시작, 삭제합니다. Virtual Machine Scale Set VM Batch 작업(모두 시작, 모두 중지, 이미지로 다시 설치 등)은 Flexible 오케스트레이션 모드에서 사용되지 않습니다.


## <a name="monitor-application-health"></a>애플리케이션 상태 모니터링
애플리케이션 상태 모니터링을 통해 애플리케이션은 Azure에 하트비트를 제공하여 애플리케이션이 정상 상태인지 비정상 상태인지 확인할 수 있습니다. Azure는 비정상 상태인 VM 인스턴스를 자동으로 바꿀 수 있습니다. Flexible 확장 집합 인스턴스의 경우 가상 머신에서 애플리케이션 상태 확장을 설치하고 구성해야 합니다. Uniform 확장 집합 인스턴스의 경우 애플리케이션 상태 확장을 사용하거나 Azure Load Balancer 사용자 지정 상태 프로브를 사용하여 상태를 측정할 수 있습니다.


## <a name="retrieve-boot-diagnostics-data"></a>부팅 진단 데이터 검색
표준 VM API 및 명령을 사용하여 인스턴스 부팅 진단 데이터 및 스크린샷을 검색합니다. Virtual Machine Scale Sets VM 부팅 진단 API 및 명령은 Flexible 오케스트레이션 모드 인스턴스와 함께 사용되지 않습니다.


## <a name="vm-extensions"></a>VM 확장
Uniform 오케스트레이션 모드 인스턴스를 대상으로 하는 확장 대신 표준 가상 머신을 대상으로 하는 확장을 사용합니다.


## <a name="features"></a>기능
다음 표에는 유연한 오케스트레이션 모드 기능과 적절한 설명서에 대한 링크가 나와 있습니다.

| 기능 | Flexible 오케스트레이션에서 지원됨(미리 보기) |
|-|-|
| 가상 머신 유형 | 표준 Azure IaaS VM (Microsoft.compute /virtualmachines) |
| 최대 인스턴스 수(FD 가용성 보장 포함) | 1000 |
| 지원되는 SKU | D 시리즈, E 시리즈, F 시리즈, A 시리즈, B 시리즈, Intel, AMD |
| 가용성 영역 | 필요에 따라 단일 가용성 영역에 모든 인스턴스를 지정합니다. |
| 장애 도메인 – 최대 분산(Azure는 최대 확산 인스턴스) | Yes |
| 장애 도메인 – 고정 분산 | 2-3 FD(지역별 최대 FD 수에 따라), 영역 배포의 경우 1 FD |
| 업데이트 도메인 | 사용되지 않으며 FD별로 수행되는 플랫폼 유지 관리 |
| 가용성 SLA | 없음(미리 보기 중) |
| VM, NIC, 디스크에 대한 모든 컨트롤 | 예 |
| 특정 장애 도메인에 VM 할당 | 예 |
| 가속화된 네트워킹 | 아니요(미리 보기 중) |
| 게스트 보안 패치 중 | 예 |
| 스폿 인스턴스 및 가격 책정  | 예, 스폿 및 일반 우선 순위 인스턴스를 모두 사용할 수 있습니다 |
| 혼합 운영 체제 | 예, Linux 및 Windows는 동일한 Flexible 확장 집합에 상주할 수 있습니다. |
| 애플리케이션 상태 모니터링 | 애플리케이션 상태 확장 |
| UltraSSD 디스크  | 예 |
| 근접 배치 그룹  | 예, [근접 배치 그룹 설명서](../virtual-machine-scale-sets/proximity-placement-groups.md)를 참조하세요 |
| Azure Load Balancer 표준 SKU | 예 |
| 집합의 VM 나열 | 예 |
| Azure Backup | 예 |
| 알림 종료(VM 확장 집합) | 예, [종료 알림 설명서](../virtual-machine-scale-sets/virtual-machine-scale-sets-terminate-notification.md)를 참조하세요 |
| 인스턴스 복구(VM 확장 집합) | 예, [인스턴스 복구 설명서](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-instance-repairs.md)를 참조하세요 |
| 자동 크기 조정 | Yes |
| VM 인스턴스를 삭제할 때 NIC 및 디스크 제거 | Yes |
| 업그레이드 정책(VM 확장 집합) | 예 |
| 자동 이미지 기반 OS 업데이트 | 예 |
| Infiniband  | 예 |
| 쓰기 가속기  | 예 |
| Azure 전용 호스트  | 예 |
| Basic SLB  | 예 |
| Application Gateway | 예 |
| 유지 관리 컨트롤  | 예 |
| Azure Alerts | 예 |
| VM 인사이트 | 예 |
| Azure Site Recovery |  예, PowerShell을 통해 |
| Service Fabric | 예 |
| AKS(Azure Kubernetes Service) | No |
| 신뢰할 수 있는 가상 머신(미리 보기) | Yes |
| 자동 확장 업데이트 | Yes |


### <a name="unsupported-parameters"></a>지원되지 않는 매개 변수

현재, 가상 머신 확장 집합의 유연한 오케스트레이션 공개 미리 보기 중에는 다음 가상 머신 확장 집합 매개 변수가 지원되지 않습니다.
- 단일 배치 그룹 - `singlePlacementGroup=False`을 선택해야 합니다.
- 다중 영역 배포 - 배포는 지역적이거나 단일 영역의 모든 VM에 대한 것일 수 있습니다
- 특수 SKU를 사용하여 배포: G, H, L, M, N 시리즈 VM 제품군
- VMSS 오버프로비전
- 이미지 기반 자동 OS 업그레이드
- SLB 상태 프로브를 통한 애플리케이션 상태 - 인스턴스에서 애플리케이션 상태 확장 사용
- 가상 머신 확장 집합 업그레이드 정책 - null이거나 비어 있어야 합니다
- Azure 전용 호스트에 배포
- 관리되지 않는 디스크
- 정책의 가상 머신 확장 집합 크기 조정
- 가상 머신 확장 집합 인스턴스 보호
- 가속 네트워킹
- 기본 Load Balancer
- 표준 Load Balancer NAT 풀을 통한 포트 전달 - 특정 인스턴스에 대한 NAT 규칙을 구성할 수 있습니다


## <a name="troubleshoot-scale-sets-with-flexible-orchestration"></a>Flexible 오케스트레이션을 사용하여 확장 집합 문제 해결
문제 해결 시나리오에 적합한 솔루션을 찾습니다.

<!-- error -->
### <a name="invalidparameter-parameter-virtualmachineprofile-is-not-allowed"></a>InvalidParameter. 'virtualMachineProfile' 매개 변수를 사용할 수 없습니다.

```
InvalidParameter. Parameter 'virtualMachineProfile' is not allowed.
```

**원인:** 구독이 Flexible 오케스트레이션 모드 공개 미리 보기에 등록되어 있지 않습니다.

**해결 방법:** 구독에 대한 VMScaleSetPublicPreview 기능을 등록해야 합니다. 위의 지침에 따라 Flexible 오케스트레이션 모드 공개 미리 보기에 등록합니다.


<!-- error -->
### <a name="badrequest-creating-a-virtual-machine-with-a-public-ip-address-via-networkinterfaceconfigurations"></a>BadRequest. NetworkInterfaceConfigurations를 통해 공용 IP 주소를 사용하여 가상 머신 생성

```
BadRequest. Creating a Virtual Machine with a Public IP Address via NetworkInterfaceConfigurations during the Public Preview of VM NetworkInterfaceConfigurations initially requires the feature 'Microsoft.Compute/SkipPublicIpWriteRBACCheckForVMNetworkInterfaceConfigurationsPublicPreview'.
```

**원인:** 구독이 Flexible 오케스트레이션 모드 공개 미리 보기에 등록되어 있지 않습니다.

**해결 방법:** 구독에 대한 `SkipPublicIpWriteRBACCheckForVMNetworkInterfaceConfigurationsPublicPreview` 특성을 등록합니다. 위의 지침에 따라 Flexible 오케스트레이션 모드 공개 미리 보기에 등록합니다.


<!-- error -->
### <a name="invalidparameter-the-value-false-of-parameter-singleplacementgroup-is-not-allowed-allowed-values-are-true"></a>InvalidParameter. 'singlePlacementGroup' 매개 변수의 'False' 값은 허용되지 않습니다. 허용되는 값은 True입니다

```
InvalidParameter. The value 'False' of parameter 'singlePlacementGroup' is not allowed. Allowed values are: True
```

**원인:** 구독이 Flexible 오케스트레이션 모드 공개 미리 보기에 등록되어 있지 않습니다.

**해결 방법:** 위의 지침에 따라 Flexible 오케스트레이션 모드 공개 미리 보기에 등록합니다.


<!-- error -->
### <a name="invalidparameter-the-specified-fault-domain-count-2-must-fall-in-the-range-1-to-1"></a>InvalidParameter. 지정된 장애 도메인 수 2는 1~1 범위에 있어야 합니다.

```
InvalidParameter. The specified fault domain count 2 must fall in the range 1 to 1.
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
**원인:** 구독이 Flexible 오케스트레이션 모드 미리 보기에 등록되어 있습니다. 그러나 `singlePlacementGroup` 매개 변수는 *True* 로 설정됩니다.

**해결 방법:** `singlePlacementGroup`을 *False* 로 설정해야 합니다.


## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"]
> [Azure Portal를 사용하는 확장 집합에 대한 유연한 오케스트레이션 모드.](flexible-virtual-machine-scale-sets-portal.md)