---
title: Azure Virtual Machine Scale Sets에 사용자 지정 스케일 인 정책 사용
description: 자동 크기 조정 구성을 사용하여 인스턴스 수를 관리하는 Azure Virtual Machine Scale Sets에서 사용자 지정 스케일 인 정책을 사용하는 방법을 알아봅니다.
services: virtual-machine-scale-sets
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: scale-in-policy
ms.date: 02/26/2020
ms.reviewer: avverma
ms.custom: avverma, devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: af1aa7ceb0784b58f9878befeae7c6ee26742061
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122696638"
---
# <a name="use-custom-scale-in-policies-with-azure-virtual-machine-scale-sets"></a>Azure Virtual Machine Scale Sets에 사용자 지정 스케일 인 정책 사용

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 균일 확장 집합

가상 머신 확장 집합 배포는 플랫폼 및 사용자 정의 사용자 지정 메트릭을 포함하여 메트릭 배열에 따라 확장 또는 축소할 수 있습니다. 스케일 아웃은 확장 집합 모델을 기반으로 새 가상 머신을 만들지만 실행 중인 가상 머신에는 스케일 인이 적용되며 확장 집합의 워크로드가 진화함에 따라 구성 및/또는 기능이 다를 수 있습니다. 

스케일 인 정책 기능을 통해 사용자는 다음과 같은 세 가지 스케일 인 구성으로 가상 머신을 스케일 인하는 순서를 구성할 수 있습니다. 

1. 기본값
2. NewestVM
3. OldestVM

### <a name="default-scale-in-policy"></a>기본 스케일 인 정책

기본적으로 가상 머신 확장 집합은 이 정책을 적용하여 스케일 인할 인스턴스를 결정합니다. *기본* 정책을 사용하면 다음과 같은 순서로 스케일 인을 위한 VM을 선택할 수 있습니다.

1. 가용성 영역 간 가상 머신 균형 조정(확장 집합이 영역 구성에 배포된 경우)
2. 장애 도메인 간 가상 머신 균형 조정(최상의 노력)
3. 가장 높은 인스턴스 ID로 가상 머신 삭제

사용자는 기본 순서만 따르기를 원하는 경우 스케일 인 정책을 지정할 필요가 없습니다.

가용성 영역 또는 장애 도메인에서의 균형 조정은 가용성 영역 또는 장애 도메인 간에 인스턴스를 이동하지 않습니다. 균형 조정은 가상 머신의 배포가 균형을 이룰 때까지 불균형 가용성 영역 또는 장애 도메인에서 가상 머신을 삭제하여 수행됩니다.

### <a name="newestvm-scale-in-policy"></a>NewestVM 스케일 인 정책

이 정책은 가용성 영역(영역 배포의 경우)에서 VM의 균형을 맞춘 후 확장 집합에서 가장 최근에 생성된 가상 머신을 삭제합니다. 이 정책을 사용하려면 가상 머신 확장 집합 모델에 대한 구성을 변경해야 합니다.

### <a name="oldestvm-scale-in-policy"></a>OldestVM 스케일 인 정책

이 정책은 가용성 영역(영역 배포의 경우)에서 VM의 균형을 맞춘 후 확장 집합에서 가장 오래 전에 생성된 가상 머신을 삭제합니다. 이 정책을 사용하려면 가상 머신 확장 집합 모델에 대한 구성을 변경해야 합니다.

## <a name="enabling-scale-in-policy"></a>스케일 인 정책 사용

스케일 인 정책은 가상 머신 확장 집합 모델에서 정의됩니다. 위의 섹션에서 설명한 것처럼 'NewestVM' 및 'OldestVM' 정책을 사용할 때 스케일 인 정책 정의가 필요합니다. 확장 집합 모델에 스케일 인 정책 정의가 없는 경우 가상 머신 확장 집합은 '기본' 스케일 인 정책을 자동으로 사용합니다. 

다음과 같은 방법으로 가상 머신 확장 집합 모델에 대한 스케일 인 정책을 정의할 수 있습니다.

### <a name="azure-portal"></a>Azure portal
 
다음 단계에서는 새 확장 집합을 만들 때 스케일 인 정책을 정의합니다. 
 
1. **가상 머신 확장 집합** 으로 이동합니다.
1. **+ 추가** 를 선택하여 새 확장 집합을 만듭니다.
1. **크기 조정** 탭으로 이동합니다. 
1. **스케일 인 정책** 섹션을 찾습니다.
1. 드롭다운에서 스케일 인 정책을 선택합니다.
1. 새 확장 집합 만들기를 완료한 후 **검토 + 만들기** 단추를 선택합니다.

### <a name="using-api"></a>API 사용

API 2019-03-01을 사용하여 가상 머신 확장 집합에서 PUT을 실행합니다.

```
PUT
https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<myRG>/providers/Microsoft.Compute/virtualMachineScaleSets/<myVMSS>?api-version=2019-03-01

{ 
"location": "<VMSS location>", 
    "properties": { 
        "scaleInPolicy": {  
            "rules": ["OldestVM"]  
        } 
    }    
} 
```
### <a name="azure-powershell"></a>Azure PowerShell

리소스 그룹을 만든 다음, 스케일 인 정책을 *OldestVM* 으로 설정하여 새 확장 집합을 만듭니다.

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName "myResourceGroup" -Location "<VMSS location>"
New-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Location "<VMSS location>" `
  -VMScaleSetName "myScaleSet" `
  -ScaleInPolicy “OldestVM”
```

### <a name="azure-cli-20"></a>Azure CLI 2.0

다음 예제에서는 새 확장 집합을 만드는 동안 스케일 인 정책을 추가합니다. 먼저 리소스 그룹을 만든 다음, 스케일 인 정책을 사용하여 새 확장 집합을 *OldestVM* 으로 만듭니다. 

```azurecli-interactive
az group create --name <myResourceGroup> --location <VMSSLocation>
az vmss create \
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --image UbuntuLTS \
  --admin-username <azureuser> \
  --generate-ssh-keys \
  --scale-in-policy OldestVM
```

### <a name="using-template"></a>템플릿 사용

템플릿에서 "속성" 아래에 다음을 추가합니다.

```json
"scaleInPolicy": {  
      "rules": ["OldestVM"]  
}
```

위의 블록은 스케일 인이 트리거될 때(자동 확장 또는 수동 삭제를 통해) 가상 머신 확장 집합이 영역 균형 확장 집합에서 가장 오래된 VM을 삭제하도록 지정합니다.

가상 머신 확장 집합의 영역 균형이 맞지 않으면 확장 집합은 먼저 불균형 영역에서 VM을 삭제합니다. 불균형 영역 내에서 확장 집합은 위에서 지정된 스케일 인 정책을 사용하여 스케일 인할 VM을 결정합니다. 이 경우 불균형 영역 내에서 확장 집합은 삭제할 해당 영역에서 가장 오래된 VM을 선택합니다.

비영역 가상 머신 확장 집합의 경우 정책은 삭제를 위해 확장 집합에서 가장 오래된 VM을 선택합니다.

위의 스케일 인 정책에서 'NewestVM'을 사용하는 경우에도 동일한 프로세스가 적용됩니다.

## <a name="modifying-scale-in-policies"></a>스케일 인 정책 수정

스케일 인 정책을 수정하는 작업은 스케일 인 정책을 적용하는 것과 동일한 프로세스를 따릅니다. 예를 들어 위의 예제에서 'OldestVM'의 정책을 'NewestVM'으로 변경하려면 다음을 수행합니다.

### <a name="azure-portal"></a>Azure portal

Azure Portal을 통해 기존 확장 집합의 스케일 인 정책을 수정할 수 있습니다. 
 
1. 기존 가상 머신 확장 집합의 왼쪽에 있는 메뉴에서 **크기 조정** 을 선택합니다.
1. **스케일 인 정책** 탭을 선택합니다.
1. 드롭다운에서 스케일 인 정책을 선택합니다.
1. 작업이 완료되면 **저장** 을 선택합니다. 

### <a name="using-api"></a>API 사용

API 2019-03-01을 사용하여 가상 머신 확장 집합에서 PUT을 실행합니다.

```
PUT
https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<myRG>/providers/Microsoft.Compute/virtualMachineScaleSets/<myVMSS>?api-version=2019-03-01 

{ 
"location": "<VMSS location>", 
    "properties": { 
        "scaleInPolicy": {  
            "rules": ["NewestVM"]  
        } 
    }    
}
```
### <a name="azure-powershell"></a>Azure PowerShell

기존 확장 집합의 스케일 인 정책을 업데이트합니다.

```azurepowershell-interactive
Update-AzVmss `
 -ResourceGroupName "myResourceGroup" `
 -VMScaleSetName "myScaleSet" `
 -ScaleInPolicy “OldestVM”
```

### <a name="azure-cli-20"></a>Azure CLI 2.0

기존 확장 집합의 스케일 인 정책을 업데이트하는 예제는 다음과 같습니다. 

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --scale-in-policy OldestVM
```

### <a name="using-template"></a>템플릿 사용

템플릿의 "속성"에서 아래와 같이 템플릿을 수정하고 다시 배포합니다. 

```json
"scaleInPolicy": {  
      "rules": ["NewestVM"]  
} 
```

'NewestVM'을 'Default' 또는 'OldestVM'으로 변경하기로 결정한 경우에도 동일한 프로세스가 적용됩니다.

## <a name="instance-protection-and-scale-in-policy"></a>인스턴스 보호 및 스케일 인 정책

가상 머신 확장 집합은 다음과 같은 두 가지 유형의 [인스턴스 보호](./virtual-machine-scale-sets-instance-protection.md#types-of-instance-protection)를 제공합니다.

1. 규모 감축으로부터 보호
2. 확장 집합 작업으로부터 보호

보호된 가상 머신은 적용된 스케일 인 정책에 관계없이 스케일 인 작업을 통해 삭제되지 않습니다. 예를 들어 VM_0(확장 집합에서 가장 오래된 VM)이 스케일 인으로부터 보호되고 확장 집합에 'OldestVM' 스케일 인 정책이 사용하도록 설정된 경우, VM_0은 확장 집합에서 가장 오래된 VM이더라도 스케일 인을 하는 것으로 간주되지 않습니다. 

확장 집합에서 사용하도록 설정된 스케일 인 정책에 관계없이 사용자는 언제든지 보호된 가상 머신을 수동으로 삭제할 수 있습니다. 

## <a name="usage-examples"></a>사용 예 

아래 예제에서는 가상 머신 확장 집합이 스케일 인 이벤트를 트리거할 때 삭제할 VM을 선택하는 방법을 보여 줍니다. 인스턴스 ID가 가장 높은 가상 머신은 확장 집합의 최신 VM으로 간주되며, 인스턴스 ID가 가장 작은 VM은 확장 집합에서 가장 오래된 VM으로 간주됩니다. 

### <a name="oldestvm-scale-in-policy"></a>OldestVM 스케일 인 정책

| 이벤트                 | 영역 1의 인스턴스 ID  | 영역 2의 인스턴스 ID  | 영역 3의 인스턴스 ID  | 스케일 인 선택                                                                                                               |
|-----------------------|------------------------|------------------------|------------------------|----------------------------------------------------------------------------------------------------------------------------------|
| Initial               | 3, 4, 5, 10            | 2, 6, 9, 11            | 1, 7, 8                |                                                                                                                                  |
| 스케일 인              | 3, 4, 5, 10            | ***2***, 6, 9, 11      | 1, 7, 8                | 영역 3에 가장 오래된 VM이 있는 경우에도 영역 1과 2 중에서 선택합니다. 영역 2에서 VM 2는 해당 영역에서 가장 오래된 VM이므로 삭제합니다.   |
| 스케일 인              | ***3***, 4, 5, 10      | 6, 9, 11               | 1, 7, 8                | 영역 3에 가장 오래된 VM이 있는 경우에도 영역 1을 선택합니다. 영역 1에서 VM 3은 해당 영역에서 가장 오래된 VM이므로 삭제합니다.                  |
| 스케일 인              | 4, 5, 10               | 6, 9, 11               | ***1***, 7, 8          | 영역의 균형이 조정됩니다. 영역 3에서 VM1은 크기 집합에서 가장 오래된 VM이므로 삭제합니다.                                               |
| 스케일 인              | ***4***, 5, 10         | 6, 9, 11               | 7, 8                   | 영역 1과 영역 2 중에서 선택합니다. 영역 1에서 VM4는 두 영역에서 가장 오래된 VM이므로 삭제합니다.                              |
| 스케일 인              | 5, 10                  | ***6***, 9, 11         | 7, 8                   | 영역 1에 가장 오래된 VM이 있는 경우에도 영역 2를 선택합니다. 영역 1에서 VM6은 해당 영역에서 가장 오래된 VM이므로 삭제합니다.                    |
| 스케일 인              | ***5***, 10            | 9, 11                  | 7, 8                   | 영역의 균형이 조정됩니다. 영역 1에서 VM5는 크기 집합에서 가장 오래된 VM이므로 삭제합니다.                                                |

비영역 가상 머신 확장 집합의 경우 정책은 삭제를 위해 확장 집합에서 가장 오래된 VM을 선택합니다. "보호된" VM은 삭제를 위해 건너뜁니다.

### <a name="newestvm-scale-in-policy"></a>NewestVM 스케일 인 정책

| 이벤트                 | 영역 1의 인스턴스 ID  | 영역 2의 인스턴스 ID  | 영역 3의 인스턴스 ID  | 스케일 인 선택                                                                                                               |
|-----------------------|------------------------|------------------------|------------------------|----------------------------------------------------------------------------------------------------------------------------------|
| Initial               | 3, 4, 5, 10            | 2, 6, 9, 11            | 1, 7, 8                |                                                                                                                                  |
| 스케일 인              | 3, 4, 5, 10            | 2, 6, 9, ***11***      | 1, 7, 8                | 영역 1과 2 중에서 선택합니다. 영역 2에서 VM11은 두 영역에 있는 최신 VM이므로 삭제합니다.                                |
| 스케일 인              | 3, 4, 5, ***10***      | 2, 6, 9                | 1, 7, 8                | 영역 1에는 다른 두 영역보다 더 많은 VM이 있으므로 선택합니다. 영역 1에서 VM10은 해당 영역에서 최신 VM이므로 삭제합니다.          |
| 스케일 인              | 3, 4, 5                | 2, 6, ***9***          | 1, 7, 8                | 영역의 균형이 조정됩니다. 영역 2에서 VM9는 크기 집합에서 최신 VM이므로 삭제합니다.                                                |
| 스케일 인              | 3, 4, 5                | 2, 6                   | 1, 7, ***8***          | 영역 1과 영역 3 중에서 선택합니다. 영역 3에서 VM8은 해당 영역에서 최신 VM이므로 삭제합니다.                                      |
| 스케일 인              | 3, 4, ***5***          | 2, 6                   | 1, 7                   | 영역 3에 최신 VM이 있는 경우에도 영역 1을 선택합니다. 영역 1에서 VM5는 해당 영역에서 최신 VM이므로 삭제합니다.                    |
| 스케일 인              | 3, 4                   | 2, 6                   | 1, ***7***             | 영역의 균형이 조정됩니다. 영역 3에서 VM7은 크기 집합의 최신 VM이므로 삭제합니다.                                                |

비영역 가상 머신 확장 집합의 경우 정책은 삭제를 위해 확장 집합에서 최신 VM을 선택합니다. "보호된" VM은 삭제를 위해 건너뜁니다. 

## <a name="troubleshoot"></a>문제 해결

1. "'속성' 형식의 개체에서 'scaleInPolicy' 멤버를 찾을 수 없습니다."라는 오류 메시지와 함께 'BadRequest' 오류가 발생하는 경우 scaleInPolicy를 사용하도록 설정할 수 없습니다. 가상 머신 확장 집합에 사용되는 API 버전을 확인하세요. 이 기능을 수행 하려면 API 버전 2019-03-01 이상이 필요합니다.

2. 스케일 인을 위한 VM을 잘못 선택한 경우는 위의 예제를 참조하세요. 가상 머신 확장 집합이 영역 배포인 경우 스케일 인 정책은 먼저 불균형 영역에 적용되고 영역에 분산된 후에 확장 집합 전체에 적용됩니다. 스케일 인 순서가 위의 예제와 일치하지 않는 경우 문제 해결을 위해 가상 머신 확장 집합 팀에 쿼리를 제기하세요.

## <a name="next-steps"></a>다음 단계

가상 머신 확장 집합에 [애플리케이션을 배포하는 방법](virtual-machine-scale-sets-deploy-app.md)에 대해 알아봅니다.
