---
title: Azure VM용 자동 VM 게스트 패치
description: Azure에서 가상 머신을 자동으로 패치하는 방법에 대해 알아봅니다.
author: mayanknayar
ms.service: virtual-machines
ms.subservice: maintenance
ms.workload: infrastructure
ms.topic: how-to
ms.date: 07/29/2021
ms.author: manayar
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: a120914051f7da0ccbfffb2a7fda9ab8404e7a76
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129216475"
---
# <a name="preview-automatic-vm-guest-patching-for-azure-vms"></a>미리 보기: Azure VM용 자동 VM 게스트 패치

**적용 대상:** :heavy_check_mark: Linux VMs :heavy_check_mark: Windows VMs :heavy_check_mark: 유연한 확장 집합 :heavy_check_mark: 균일한 확장 집합

자동 VM 게스트 패치를 Azure VM에 사용하도록 설정하면 보안 규정 준수를 유지하기 위해 가상 머신을 안전하게 자동으로 패치하여 업데이트를 쉽게 관리할 수 있습니다.

자동 VM 게스트 패치의 특징은 다음과 같습니다.
- *중요* 또는 *보안* 으로 분류된 패치가 자동으로 다운로드되어 VM에 적용됩니다.
- 패치는 VM의 표준 시간대에서 사용량이 적은 시간에 적용됩니다.
- 패치 오케스트레이션은 Azure에서 관리되며 [가용성 우선 원칙](#availability-first-updates)에 따라 패치가 적용됩니다.
- 패치 실패를 검색하기 위해 플랫폼 상태 신호를 통해 결정되는 가상 머신 상태를 모니터링합니다.
- 모든 VM 크기에 사용할 수 있습니다.

> [!IMPORTANT]
> 자동 VM 게스트 패치는 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며, 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="how-does-automatic-vm-guest-patching-work"></a>자동 VM 게스트 패치는 어떻게 작동하나요?

VM에서 자동 VM 게스트 패치를 사용하도록 설정하면 사용 가능한 *중요* 및 *보안* 패치가 자동으로 VM에 다운로드되고 적용됩니다. 이 프로세스는 새 패치가 릴리스될 때마다 매월 자동으로 시작됩니다. 패치 평가 및 설치는 자동으로 이루어지며, 필요에 따라 이 프로세스 중 VM을 다시 부팅해야 합니다.

VM은 해당 VM에 대해 적용 가능한 패치를 결정하기 위해 며칠마다 주기적으로, 30일 동안 여러 번 평가됩니다. 패치는 VM에서 어느 날이든 VM에 대한 사용량이 적은 시간에 설치할 수 있습니다. 이러한 자동 평가를 통해 가능한 가장 빠른 기회에 누락된 패치를 검색할 수 있습니다.

패치는 아래 설명된 가용성 우선 오케스트레이션 원칙에 따라 월별 패치 릴리스 30일 이내에 설치됩니다. 패치는 VM의 표준 시간대에 따라 VM 사용률이 낮은 시간에만 설치됩니다. 패치를 자동으로 설치하려면 사용량이 많지 않은 시간 중 VM이 실행 중이어야 합니다. 정기적으로 평가하는 동안 VM의 전원이 꺼져 있는 경우 VM이 자동으로 평가되고, VM의 전원이 켜지면 다음 주기 평가 중(일반적으로 며칠 이내) 적용 가능한 패치가 자동으로 설치됩니다.

*중요* 또는 *보안* 으로 분류되지 않은 정의 업데이트 및 기타 패치는 자동 VM 게스트 패치를 통해 설치되지 않습니다. 다른 패치 분류를 사용하여 패치를 설치하거나 사용자 자신의 사용자 지정 유지 관리 기간 내에 패치 설치를 예약하려면 [업데이트 관리](./windows/tutorial-config-management.md#manage-windows-updates)를 사용할 수 있습니다.

### <a name="availability-first-updates"></a>가용성 우선 업데이트

패치 설치 프로세스는 자동 VM 게스트 패치가 활성화된 모든 VM에 대해 Azure에서 전역적으로 오케스트레이션합니다. 이 오케스트레이션은 Azure에서 제공하는 다양한 가용성 수준에서 가용성 우선 원칙을 따릅니다.

Azure 플랫폼은 업데이트를 진행 중인 가상 머신 그룹을 대상으로 업데이트를 오케스트레이션합니다.

**지역 간:**
- 월간 업데이트는 글로벌 배포 실패를 방지하기 위해 전역적으로 단계별 방식으로 Azure 전체에 걸쳐 오케스트레이션됩니다.
- 단계에는 지역이 하나 이상 존재할 수 있으며, 업데이트는 한 단계의 적격 VM의 업데이트가 성공했을 때만 다른 단계로 이동합니다.
- 지리적으로 쌍을 이루는 지역은 동시에 업데이트되지 않으며 동일한 지역적 단계에 존재할 수 없습니다.
- 업데이트 성공 여부는 VM 사후 업데이트 상태를 추적하여 결정합니다. VM 상태는 VM에 대한 플랫폼 상태 표시기를 통해 추적합니다.

**지역 내에서:**
- 다른 가용성 영역에 있는 VM은 동시에 동일한 업데이트로 업데이트되지 않습니다.
- 가용성 집합에 속하지 않는 VM은 최대한 일괄 처리되어 구독에 있는 모든 VM이 동시에 업데이트되지 않게 합니다.

**가용성 집합 내:**
- 공통 가용성 집합에 있는 모든 VM은 동시에 업데이트되지 않습니다.
-   공통 가용성 집합의 VM은 업데이트 도메인 경계 내에서 업데이트되며, 여러 업데이트 도메인의 VM은 동시에 업데이트되지 않습니다.

특정 VM은 월별 패치 주기 사이에 다른 일괄 처리에서 선택될 수 있으므로 지정된 VM의 패치 설치 날짜는 월별로 다를 수 있습니다.

### <a name="which-patches-are-installed"></a>어떤 패치가 설치됩니까?
설치되는 패치는 VM의 출시 단계에 따라 달라집니다. 매월 새 글로벌 출시가 시작되며 여기에서 개별 VM에 대해 평가된 모든 보안 및 중요 패치가 해당 VM에 설치됩니다. 출시는 일괄 처리에서 모든 Azure 지역에 걸쳐 오케스트레이션됩니다(위의 가용성 우선 패치 섹션에서 설명).

설치할 정확한 패치 집합은 OS 유형 및 평가 타이밍을 포함하여 VM 구성에 따라 달라집니다. 패치 오케스트레이션이 서로 다른 시간에 다른 지역에 도달할 때 사용 가능한 패치가 어느 정도 있는 경우 다른 지역에 있는 두 개의 동일한 VM에 다른 패치가 설치될 수 있습니다. 마찬가지로 하지만 빈번하지 않게 동일한 지역 내에 있지만 다른 시간에 평가된(가용성 영역 또는 가용성 집합 일괄 처리로 인해) VM은 서로 다른 패치를 얻을 수 있습니다.

자동 VM 게스트 패치는 패치 원본을 구성하지 않으므로 퍼블릭 리포지토리와 프라이빗 리포지토리와 같은 다른 패치 원본에 구성된 두 개의 유사한 VM은 설치된 정확한 패치 집합에서 차이를 보일 수 있습니다.

고정된 주기에서 패치를 해제하는 OS 유형의 경우 OS에 대한 퍼블릭 리포지토리에 구성된 VM은 한 달에 다양한 출시 단계에서 동일한 패치 집합을 받을 수 있습니다. 예를 들어, 퍼블릭 Windows 업데이트 리포지토리에 구성된 Windows VM이 있습니다.

매월 새 출시가 트리거되면 VM이 전원이 켜진 경우 사용량이 적은 시간에 매월 하나 이상의 패치 출시를 수신합니다. 이렇게 되면 월 기준으로 사용 가능한 최신 보안 및 중요 패치가 VM에 패치됩니다. 설치된 패치 집합의 일관성을 보장하기 위해 사용자 고유의 프라이빗 리포지토리에서 패치를 평가하고 다운로드하도록 VM을 구성할 수 있습니다.

## <a name="supported-os-images"></a>지원되는 OS 이미지
특정 OS 플랫폼 이미지에서 만든 VM만 현재 미리 보기에서 지원됩니다. 사용자 지정 이미지는 현재 미리 보기에서 지원되지 않습니다.

현재 지원되는 플랫폼 SKU는 다음과 같습니다(주기적으로 더 추가될 예정임).

| Publisher               | OS 제품      |  SKU               |
|-------------------------|---------------|--------------------|
| Canonical  | UbuntuServer | 18.04-LTS |
| Canonical  | 0001-com-ubuntu-pro-bionic | pro-18_04-lts |
| Canonical  | 0001-com-ubuntu-server-focal | 20_04-lts |
| Canonical  | 0001-com-ubuntu-pro-focal | pro-20_04-lts |
| Redhat  | RHEL | 7.2, 7.3, 7.4, 7.5, 7.6, 7.7, 7.8, 7_9, 7-RAW, 7-LVM |
| Redhat  | RHEL | 8, 8.1, 8.2, 8_3, 8_4, 8-LVM |
| Redhat  | RHEL-RAW | 8-raw |
| OpenLogic  | Centos | 7.2, 7.3, 7.4, 7.5, 7.6, 7.7, 7_8, 7_9, 7-LVM |
| OpenLogic  | Centos | 8.0, 8_1, 8_2, 8_3, 8-lvm |
| SUSE  | sles-12-sp5 | gen1, gen2 |
| SUSE  | sles-15-sp2 | gen1, gen2 |
| MicrosoftWindowsServer  | WindowsServer | 2008-R2-SP1 |
| MicrosoftWindowsServer  | WindowsServer | 2012-R2-Datacenter |
| MicrosoftWindowsServer  | WindowsServer | 2016-Datacenter    |
| MicrosoftWindowsServer  | WindowsServer | 2016-Datacenter-Server-Core |
| MicrosoftWindowsServer  | WindowsServer | 2019-Datacenter |
| MicrosoftWindowsServer  | WindowsServer | 2019-Datacenter-Core |

> [!NOTE]
>자동 VM 게스트 패치, 주문형 패치 평가 및 주문형 패치 설치는 지원되는 OS 이미지 목록에서 게시자, 제품 및 SKU의 정확한 조합을 사용하여 이미지에서 만든 VM에서만 지원됩니다. 사용자 지정 이미지 또는 기타 게시자, 제품, SKU 조합은 지원되지 않습니다.

## <a name="patch-orchestration-modes"></a>패치 오케스트레이션 모드
Azure의 VM은 이제 다음과 같은 패치 오케스트레이션 모드를 지원합니다.

**AutomaticByPlatform:**
- 이 모드는 Linux 및 Windows VM 모두에서 지원됩니다.
- 이 모드에서는 가상 머신에 대해 자동 VM 게스트 패치를 사용하도록 설정하고 후속 패치 설치는 Azure에 의해 오케스트레이션됩니다.
- 이 모드는 가용성 우선 패치를 적용하는 데 필요합니다.
- 이 모드는 위의 지원되는 OS 플랫폼 이미지를 사용하여 만든 VM에 대해서만 지원됩니다.
- Windows VM의 경우 이 모드를 설정하면 중복을 방지하기 위해 Windows 가상 머신의 기본 자동 업데이트도 사용하지 않도록 설정됩니다.
- Linux VM에서 이 모드를 사용하려면 VM 템플릿에서 `osProfile.linuxConfiguration.patchSettings.patchMode=AutomaticByPlatform` 속성을 설정합니다.
- Windows VM에서 이 모드를 사용하려면 VM 템플릿에서 `osProfile.windowsConfiguration.patchSettings.patchMode=AutomaticByPlatform` 속성을 설정합니다.

**AutomaticByOS:**
- 이 모드는 Windows VM에 대해서만 지원됩니다.
- 이 모드는 Windows 가상 머신에서 자동 업데이트를 사용하도록 설정하며, 패치는 자동 업데이트를 통해 VM에 설치됩니다.
- 이 모드는 가용성 우선 패치를 지원하지 않습니다.
- Windows VM에 다른 패치 모드가 지정되지 않은 경우 이 모드가 기본적으로 설정됩니다.
- Windows VM에서 이 모드를 사용하려면 속성 `osProfile.windowsConfiguration.enableAutomaticUpdates=true`를 설정하고 VM 템플릿에서 `osProfile.windowsConfiguration.patchSettings.patchMode=AutomaticByOS` 속성을 설정합니다.

**수동:**
- 이 모드는 Windows VM에 대해서만 지원됩니다.
- 이 모드는 Windows 가상 머신에서 자동 업데이트를 사용하지 않도록 설정합니다.
- 이 모드는 가용성 우선 패치를 지원하지 않습니다.
- 사용자 지정 패치 솔루션을 사용하는 경우 이 모드를 설정해야 합니다.
- Windows VM에서 이 모드를 사용하려면 `osProfile.windowsConfiguration.enableAutomaticUpdates=false` 속성을 설정하고 VM 템플릿에서 `osProfile.windowsConfiguration.patchSettings.patchMode=Manual` 속성을 설정합니다.

**ImageDefault:**
- 이 모드는 Linux VM에 대해서만 지원됩니다.
- 이 모드는 가용성 우선 패치를 지원하지 않습니다.
- 이 모드는 VM을 만드는 데 사용되는 이미지에 기본 패치 구성을 적용합니다.
- Linux VM에 다른 패치 모드가 지정되지 않은 경우 이 모드가 기본적으로 설정됩니다.
- Linux VM에서 이 모드를 사용하려면 VM 템플릿에서 `osProfile.linuxConfiguration.patchSettings.patchMode=ImageDefault` 속성을 설정합니다.

> [!NOTE]
>Windows VM의 경우에는 VM을 처음 만들 때만 `osProfile.windowsConfiguration.enableAutomaticUpdates` 속성을 설정할 수 있습니다. 이렇게 하면 특정 패치 모드 전환에 영향을 줍니다. AutomaticByPlatform 및 Manual 모드 간의 전환은 `osProfile.windowsConfiguration.enableAutomaticUpdates=false`가 있는 VM에서 지원됩니다. 마찬가지로 AutomaticByPlatform 및 AutomaticByOS 모드 간의 전환은 `osProfile.windowsConfiguration.enableAutomaticUpdates=true`가 있는 VM에서 지원됩니다. AutomaticByOS와 Manual 모드 간의 전환은 지원되지 않습니다.

## <a name="requirements-for-enabling-automatic-vm-guest-patching"></a>자동 VM 게스트 패치를 사용하기 위한 요구 사항

- 가상 머신에는 [Windows](./extensions/agent-windows.md) 또는 [Linux](./extensions/agent-linux.md)용 Azure VM 에이전트가 설치되어 있어야 합니다.
- Linux VM의 경우 Azure Linux 에이전트는 2.2.53.1 이상 버전이어야 합니다. 현재 버전이 필요한 버전보다 낮은 경우 [Linux 에이전트를 업데이트](./extensions/update-linux-agent.md)합니다.
- Windows VM의 경우 가상 머신에서 Windows 업데이트 서비스가 실행 중이어야 합니다.
- 가상 머신은 구성된 업데이트 엔드포인트에 액세스할 수 있어야 합니다. 가상 머신이 Linux용 프라이빗 리포지토리를 사용하도록 구성되어 있거나 Windows VM에 대해 WSUS(Windows Server Update Services)를 사용하도록 구성된 경우 관련 업데이트 엔드포인트에 액세스할 수 있어야 합니다.
- 컴퓨팅 API 버전 2021-03-01 이상을 사용하여 주문형 평가 및 주문형 패치를 비롯한 모든 기능에 액세스합니다.
- 사용자 지정 이미지는 현재 지원되지 않습니다.

## <a name="enable-automatic-vm-guest-patching"></a>자동 VM 게스트 패치 사용
자동 VM 게스트 패치는 지원되는 플랫폼 이미지에서 만든 Windows 또는 Linux VM에서만 사용할 수 있습니다. Windows VM에서 자동 VM 게스트 패치를 사용하도록 설정하려면 VM 템플릿 정의에서 *osProfile.windowsConfiguration.enableAutomaticUpdates* 속성을 *true* 로 설정해야 합니다. 이제 VM을 만들 때 이 속성만 설정할 수 있습니다. Linux VM에는 이 추가 속성을 적용할 수 없습니다.

### <a name="rest-api-for-linux-vms"></a>Linux VM용 REST API
다음 예제에서는 자동 VM 게스트 패치를 사용하도록 설정하는 방법을 설명합니다.

```
PUT on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVirtualMachine?api-version=2020-12-01`
```

```json
{
  "location": "<location>",
  "properties": {
    "osProfile": {
      "linuxConfiguration": {
        "provisionVMAgent": true,
        "patchSettings": {
          "patchMode": "AutomaticByPlatform"
        }
      }
    }
  }
}
```

### <a name="rest-api-for-windows-vms"></a>Windows VM용 REST API
다음 예제에서는 자동 VM 게스트 패치를 사용하도록 설정하는 방법을 설명합니다.

```
PUT on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVirtualMachine?api-version=2020-12-01`
```

```json
{
  "location": "<location>",
  "properties": {
    "osProfile": {
      "windowsConfiguration": {
        "provisionVMAgent": true,
        "enableAutomaticUpdates": true,
        "patchSettings": {
          "patchMode": "AutomaticByPlatform"
        }
      }
    }
  }
}
```

### <a name="azure-powershell-for-windows-vms"></a>Windows VM용 Azure PowerShell 샘플
VM을 만들거나 업데이트할 때 [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem) cmdlet를 사용하여 자동 VM 게스트 패치를 사용하도록 설정합니다.

```azurepowershell-interactive
Set-AzVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName $ComputerName -Credential $Credential -ProvisionVMAgent -EnableAutoUpdate -PatchMode "AutomaticByPlatform"
```

### <a name="azure-cli-for-windows-vms"></a>Windows VM용 Azure CLI
새 VM을 만들 때 [az vm create](/cli/azure/vm#az_vm_create)를 사용하여 자동 VM 게스트 패치를 사용하도록 설정합니다. 다음 예제에서는 *myResourceGroup* 이라는 리소스 그룹에서 *myVM* 이라는 VM에 대한 자동 VM 게스트 패치를 구성합니다.

```azurecli-interactive
az vm create --resource-group myResourceGroup --name myVM --image Win2019Datacenter --enable-agent --enable-auto-update --patch-mode AutomaticByPlatform
```

기존 VM을 수정하려면 [az vm update](/cli/azure/vm#az_vm_update)를 사용합니다.

```azurecli-interactive
az vm update --resource-group myResourceGroup --name myVM --set osProfile.windowsConfiguration.enableAutomaticUpdates=true osProfile.windowsConfiguration.patchSettings.patchMode=AutomaticByPlatform
```

## <a name="enablement-and-assessment"></a>활성화 및 평가

> [!NOTE]
>VM의 사용량이 적은 시간에 활성화가 완료되므로 VM에서 자동 VM 게스트 업데이트를 사용하도록 설정하는 데 3시간 이상 걸릴 수 있습니다. 평가 및 패치 설치는 사용량이 적은 시간에만 발생하므로 패치를 적용하려면 VM이 사용량이 적은 시간에도 실행하고 있어야 합니다.

VM에 대해 자동 VM 게스트 패치를 사용하도록 설정하는 경우 `Microsoft.CPlat.Core.LinuxPatchExtension` 형식의 VM 확장은 Linux VM에 설치되고 `Microsoft.CPlat.Core.WindowsPatchExtension` 형식의 VM 확장은 Windows VM에 설치됩니다. 이 확장은 자동 VM 게스트 패치 프로세스의 일부로 Azure 플랫폼에서 관리되므로 수동으로 설치하거나 업데이트할 필요가 없습니다.

VM의 사용량이 적은 시간에 활성화가 완료되므로 VM에서 자동 VM 게스트 업데이트를 사용하도록 설정하는 데 3시간 이상 걸릴 수 있습니다. 확장은 또한 VM의 사용량이 적은 시간에 설치되고 업데이트됩니다. VM의 사용량이 적은 시간이 끝난 다음 활성화를 완료할 수 있는 경우 활성화 프로세스는 다음 사용 가능한 사용량이 적은 시간에 다시 시작됩니다.

자동 업데이트는 대부분의 시나리오에서 사용하지 않으며, 패치 설치는 앞으로 확장을 통해 수행됩니다. 다음 조건이 적용 됩니다.
- Windows VM에서 이전에 AutomaticByOS 패치 모드를 통해 자동 Windows 업데이트가 켜진 경우 확장을 설치할 때 VM에 대해 자동 Windows 업데이트가 꺼집니다.
- Ubuntu VM의 경우 자동 VM 게스트 패치가 활성화 완료되면 기본 자동 업데이트가 자동으로 사용하지 않도록 설정됩니다.
- RHEL의 경우 자동 업데이트를 미리 보기에서 수동으로 사용하지 않도록 설정해야 합니다. 다음 코드를 실행합니다.

```
systemctl stop packagekit
```

```
systemctl mask packagekit
```

자동 VM 게스트 패치가 완료되었고 패치 확장이 VM에 설치되었는지 확인하려면 VM의 인스턴스 보기를 검토하면 됩니다. 활성화 프로세스가 완료되면 확장이 설치되고 VM에 대한 평가 결과는 `patchStatus`에서 확인 가능하게 됩니다. VM의 인스턴스 보기는 아래에 설명된 대로 여러 가지 방법으로 액세스할 수 있습니다.

### <a name="rest-api"></a>REST API

```
GET on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVirtualMachine/instanceView?api-version=2020-12-01`
```
### <a name="azure-powershell"></a>Azure PowerShell
`-Status` 매개 변수와 함께 [Get-AzVM](/powershell/module/az.compute/get-azvm) cmdlet를 사용하여 VM의 인스턴스 보기에 액세스합니다.

```azurepowershell-interactive
Get-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM" -Status
```

PowerShell은 현재 패치 확장에 대한 정보만 제공합니다. `patchStatus`에 대한 정보도 PowerShell을 통해 곧 제공될 예정입니다.

### <a name="azure-cli"></a>Azure CLI
[az vm get-instance-view](/cli/azure/vm#az_vm_get_instance_view)를 사용하여 VM의 인스턴스 보기에 액세스할 수 있습니다.

```azurecli-interactive
az vm get-instance-view --resource-group myResourceGroup --name myVM
```

### <a name="understanding-the-patch-status-for-your-vm"></a>VM에 대한 패치 상태 이해

인스턴스 보기 응답의 `patchStatus` 섹션에서는 VM에 대한 최신 평가 및 마지막 패치 설치에 대한 세부 정보를 제공합니다.

VM에 대한 평가 결과는 `availablePatchSummary` 섹션에서 검토할 수 있습니다. 자동 VM 게스트 패치를 사용하는 VM에 대한 평가는 주기적으로 수행됩니다. 평가 후 사용 가능한 패치 수는 `criticalAndSecurityPatchCount` 및 `otherPatchCount` 결과 아래에서 제공됩니다. 자동 VM 게스트 패치는 *중요* 및 *보안* 패치 분류에서 평가된 모든 패치를 설치합니다. 평가된 다른 패치는 모두 건너뜁니다.

VM에 대한 패치 설치 결과는 `lastPatchInstallationSummary` 섹션에서 검토할 수 있습니다 . 이 섹션에서는 설치, 보류, 실패 또는 건너뛴 패치 수를 비롯하여 VM의 마지막 패치 설치 시도에 대한 자세한 정보를 제공합니다. 패치는 VM의 사용량이 적은 유지 관리 기간 동안에만 설치됩니다. 보류 및 실패한 패치는 다음 사용량이 적은 유지 관리 기간 동안 자동으로 다시 시도됩니다.

## <a name="disable-automatic-vm-guest-patching"></a>자동 VM 게스트 패치를 사용하지 않도록 설정
VM에 [패치 오케스트레이션 모드](#patch-orchestration-modes)를 변경하여 자동 VM 게스트 패치를 사용하지 않도록 설정할 수 있습니다.

Linux VM에서 자동 VM 게스트 패치를 사용하지 않도록 설정하려면 패치 모드를 `ImageDefault`로 변경합니다.

Windows VM에서 자동 VM 게스트 패치를 사용하도록 설정하기 위해 `osProfile.windowsConfiguration.enableAutomaticUpdates` 속성은 VM에 설정할 수 있는 패치 모드를 결정하고 VM을 처음 만들 때만 이 속성을 설정할 수 있습니다. 이렇게 하면 특정 패치 모드 전환에 영향을 줍니다.
- `osProfile.windowsConfiguration.enableAutomaticUpdates=false`가 있는 VM의 경우 패치 모드를 `Manual`로 변경하여 자동 VM 게스트 패치를 사용하지 않도록 설정합니다.
- `osProfile.windowsConfiguration.enableAutomaticUpdates=true`가 있는 VM의 경우 패치 모드를 `AutomaticByOS`로 변경하여 자동 VM 게스트 패치를 사용하지 않도록 설정합니다.
- AutomaticByOS와 Manual 모드 간의 전환은 지원되지 않습니다.

API, PowerShell 및 CLI 사용 예에서 필요한 패치 모드를 설정하려면 이 문서에 있는 [사용 설정](#enable-automatic-vm-guest-patching) 섹션에 있는 예를 사용하세요.

## <a name="on-demand-patch-assessment"></a>주문형 패치 평가
VM에 대해 자동 VM 게스트 패치를 이미 사용하도록 설정한 경우 VM의 사용량이 적은 시간에 VM에 대한 주기적인 패치 평가가 수행됩니다. 이 프로세스는 자동으로 진행되며 이 문서의 앞부분에서 설명한 대로 VM의 인스턴스 보기를 통해 최신 평가 결과를 검토할 수 있습니다. 언제든지 VM에 대한 주문형 패치 평가를 트리거할 수도 있습니다. 패치 평가를 완료하는 데 몇 분 정도 걸릴 수 있으며 VM의 인스턴스 보기에서 최신 평가의 상태가 업데이트됩니다.

> [!NOTE]
>주문형 패치 평가는 자동으로 패치 설치를 트리거하지 않습니다. 자동 VM 게스트 패치를 사용하도록 설정한 경우 이 문서의 앞부분에서 설명한 가용성 우선 패치 프로세스에 따라 VM의 사용량이 적은 시간에 VM에 대해 평가되고 적용 가능한 패치가 설치됩니다.

### <a name="rest-api"></a>REST API
[패치 평가](/rest/api/compute/virtual-machines/assess-patches) API를 사용하여 가상 머신에 대해 사용 가능한 패치를 평가합니다.
```
POST on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVirtualMachine/assessPatches?api-version=2020-12-01`
```

### <a name="azure-powershell"></a>Azure PowerShell
[Invoke-AzVmPatchAssessment](/powershell/module/az.compute/invoke-azvmpatchassessment) cmdlet를 사용하여 가상 머신에 대한 사용 가능한 패치를 평가합니다.

```azurepowershell-interactive
Invoke-AzVmPatchAssessment -ResourceGroupName "myResourceGroup" -VMName "myVM"
```

### <a name="azure-cli"></a>Azure CLI
[az vm assess-patches](/cli/azure/vm#az_vm_assess_patches)를 사용하여 가상 머신에 대한 사용 가능한 패치를 평가합니다.

```azurecli-interactive
az vm assess-patches --resource-group myResourceGroup --name myVM
```

## <a name="on-demand-patch-installation"></a>주문형 패치 설치
VM에 대해 자동 VM 게스트 패치를 이미 사용하도록 설정한 경우 VM의 사용량이 적은 시간에 VM에 대한 주기적인 보안 및 중요 패치 설치가 수행됩니다. 이 프로세스는 자동으로 진행되며 이 문서의 앞부분에서 설명한 대로 VM의 인스턴스 보기를 통해 최신 설치 결과를 검토할 수 있습니다.

언제든지 VM에 대한 주문형 패치 설치를 트리거할 수도 있습니다. 패치 설치를 완료하는 데 몇 분 정도 걸릴 수 있으며 VM의 인스턴스 보기에서 최신 설치의 상태가 업데이트됩니다.

주문형 패치 설치를 사용하여 하나 이상의 패치 분류의 모든 패치를 설치할 수 있습니다. Linux에 대한 특정 패키지 또는 특정 Windows에 대한 KB ID를 포함하거나 제외하도록 선택할 수 있습니다. 주문형 패치 설치를 트리거할 때 포함 목록에서 하나 이상의 패치 분류 또는 하나 이상의 패치(Linux에 대한 패키지, Windows에 대한 KB ID)를 지정해야 합니다.

### <a name="rest-api"></a>REST API
[패치 설치](/rest/api/compute/virtual-machines/install-patches) API를 사용하여 가상 머신에 패치를 설치합니다.

```
POST on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVirtualMachine/installPatches?api-version=2020-12-01`
```

Linux에 대한 요청 본문 예제:
```json
{
  "maximumDuration": "PT1H",
  "rebootSetting": "IfRequired",
  "linuxParameters": {
    "classificationsToInclude": [
      "Critical",
      "Security"
    ]
  }
}
```

Windows에 대한 요청 본문 예제:
```json
{
  "maximumDuration": "PT1H",
  "rebootSetting": "IfRequired",
  "windowsParameters": {
    "classificationsToInclude": [
      "Critical",
      "Security"
    ]
  }
}
```

### <a name="azure-powershell"></a>Azure PowerShell
[Invoke-AzVMInstallPatch](/powershell/module/az.compute/invoke-azvminstallpatch) cmdlet을 사용하여 가상 머신에 패치를 설치합니다.

Linux VM에 특정 패키지 설치 예제:
```azurepowershell-interactive
Invoke-AzVmInstallPatch -ResourceGroupName "myResourceGroup" -VMName "myVM" -MaximumDuration "PT90M" -RebootSetting "Always" -Linux -ClassificationToIncludeForLinux "Security" -PackageNameMaskToInclude ["package123"] -PackageNameMaskToExclude ["package567"]
```

Windows VM에 모든 중요 패치 설치 예제:
```azurepowershell-interactive
Invoke-AzVmInstallPatch -ResourceGroupName "myResourceGroup" -VMName "myVM" -MaximumDuration "PT2H" -RebootSetting "Never" -Windows   -ClassificationToIncludeForWindows Critical
```

Windows VM에 모든 보안 패치 설치 예제(특정 KB ID를 포함한 패치 포함 및 제외, 재부팅이 필요한 패치 제외):
```azurepowershell-interactive
Invoke-AzVmInstallPatch -ResourceGroupName "myResourceGroup" -VMName "myVM" -MaximumDuration "PT90M" -RebootSetting "Always" -Windows -ClassificationToIncludeForWindows "Security" -KBNumberToInclude ["KB1234567", "KB123567"] -KBNumberToExclude ["KB1234702", "KB1234802"] -ExcludeKBsRequiringReboot
```

### <a name="azure-cli"></a>Azure CLI
[az vm install-patches](/cli/azure/vm#az_vm_install_patches)를 사용하여 가상 머신에 패치를 설치합니다.

Linux VM에 모든 중요 패치 설치 예제:
```azurecli-interactive
az vm install-patches --resource-group myResourceGroup --name myVM --maximum-duration PT2H --reboot-setting IfRequired --classifications-to-include-linux Critical
```

Windows VM에 모든 중요 및 보안 패치 설치 예제(재부팅이 필요한 패치 제외):
```azurecli-interactive
az vm install-patches --resource-group myResourceGroup --name myVM --maximum-duration PT2H --reboot-setting IfRequired --classifications-to-include-win Critical Security --exclude-kbs-requiring-reboot true
```

## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"]
> [Windows 가상 머신 만들기 및 관리에 대한 자세한 정보](./windows/tutorial-manage-vm.md)
