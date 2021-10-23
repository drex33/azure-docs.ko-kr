---
title: Service Fabric 클러스터에서 Windows 운영 체제 패치
description: Windows에서 실행되는 Service Fabric 클러스터 노드를 패치하는 자동 OS 이미지 업그레이드를 사용하도록 설정하는 방법은 다음과 같습니다.
ms.topic: how-to
ms.date: 10/19/2021
ms.openlocfilehash: eb0f44772ef2e6d2337b333cad0640f440a28dda
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130225476"
---
# <a name="patch-the-windows-operating-system-in-your-service-fabric-cluster"></a>Service Fabric 클러스터에서 Windows 운영 체제 패치

Azure에서 운영 체제를 최신 패치로 유지하는 모범 사례는 [Virtual Machine Scale Sets에서 자동 OS 이미지 업그레이드](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md)를 구현하는 것입니다. Virtual Machine Scale Sets 기반 자동 OS 이미지 업그레이드를 사용하려면 확장 집합에서 Silver 이상의 내구성이 필요합니다.

## <a name="requirements-for-automatic-os-image-upgrades-by-virtual-machine-scale-sets"></a>Virtual Machine Scale Sets에서 자동 OS 이미지 업그레이드를 사용하기 위한 요구 사항

- Service Fabric [내구성 수준](service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster)은 Bronze가 아니라 Silver 또는 Gold입니다.
- 확장 집합 모델 정의의 Service Fabric 확장에는 TypeHandlerVersion 1.1 이상이 있어야 합니다.
- 내구성 수준은 확장 집합 모델 정의의 Service Fabric 클러스터와 Service Fabric 확장에서 동일해야 합니다.
- Virtual Machine Scale Sets에 대한 추가 상태 프로브 또는 애플리케이션 상태 확장을 사용할 필요는 없습니다.
- 상태 비저장 노드 형식은 Bronze로 내구성이 있는 유일한 예외이지만 자동 OS 이미지 업그레이드는 여전히 구성할 수 있습니다. 자세한 내용은 [상태 비지정 전용 노드 유형을 가진 Azure Service Fabric 클러스터 배포를 참조하세요.](service-fabric-stateless-node-types.md)

Service Fabric 클러스터와 Service Fabric 확장의 내구성 설정이 일치해야 합니다. 그렇지 않으면 불일치로 인해 업그레이드 오류가 발생합니다. 내구성 수준은 [이 페이지](service-fabric-cluster-capacity.md#changing-durability-levels)에 설명된 지침에 따라 수정할 수 있습니다.

브론즈 내구성이 있으면 자동 OS 이미지 업그레이드를 사용할 수 없습니다. 실버 이상의 내구성 수준에는 [패치 오케스트레이션 애플리케이션](service-fabric-patch-orchestration-application.md)(Azure가 아닌 호스트 클러스터에만 사용)이 *권장되지 않지만*, Service Fabric 업그레이드 도메인과 관련하여 Windows 업데이트를 자동화하는 유일한 옵션입니다.

패치 오케스트레이션 애플리케이션에서 자동 OS 이미지 업그레이드로 전환하려면 먼저 패치 오케스트레이션 애플리케이션의 사용을 중단해야 합니다.

## <a name="enable-auto-os-upgrades-and-disable-windows-update"></a>자동 OS 업그레이드 사용 및 Windows 업데이트 사용 안 함

자동 OS 업데이트를 사용하도록 설정하는 경우 배포 템플릿에서 Windows 업데이트를 사용하지 않도록 설정해야 합니다. 이러한 변경 내용을 배포하면 확장 집합의 모든 머신이 이미지로 다시 설치되고 확장 집합이 자동 업데이트에 대해 사용하도록 설정됩니다.

> [!IMPORTANT]
> Service Fabric은 OS 디스크를 바꾸지 않고 Windows 업데이트에서 운영 체제 패치를 적용하는 VM 내 업그레이드를 지원하지 않습니다.


1. 자동 OS 이미지 업그레이드를 사용하도록 설정하고 배포 템플릿에서 Windows 업데이트를 사용하지 않도록 설정합니다.
 
    ```json
    "virtualMachineProfile": { 
        "properties": {
          "upgradePolicy": {
            "automaticOSUpgradePolicy": {
              "enableAutomaticOSUpgrade":  true
            }
          }
        }
      }
    ```
    
    ```json
    "virtualMachineProfile": { 
        "osProfile": { 
            "windowsConfiguration": { 
                "enableAutomaticUpdates": false 
            }
        }
    }
    ```

    ```azurepowershell-interactive
    Update-AzVmss -ResourceGroupName $resourceGroupName -VMScaleSetName $scaleSetName -AutomaticOSUpgrade $true -EnableAutomaticUpdate $false
    ```

1. 확장 집합 모델을 업데이트합니다. 이 구성을 변경한 후에는 모든 머신의 이미지로 다시 설치를 수행하여 변경 내용을 적용하기 위해 확장 집합 모델을 업데이트해야 합니다.

    ```azurepowershell-interactive
    $scaleSet = Get-AzVmssVM -ResourceGroupName $resourceGroupName -VMScaleSetName $scaleSetName
    $instances = foreach($vm in $scaleSet)
    {
        Set-AzVmssVM -ResourceGroupName $resourceGroupName -VMScaleSetName $scaleSetName -InstanceId $vm.InstanceID -Reimage
    }
    ```

## <a name="next-steps"></a>다음 단계

[Virtual Machine Scale Sets에서 자동 OS 이미지 업그레이드](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md)를 사용하도록 설정하는 방법을 알아봅니다.
