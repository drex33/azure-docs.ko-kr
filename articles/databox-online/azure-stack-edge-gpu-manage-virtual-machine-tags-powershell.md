---
title: Azure PowerShell을 통해 Azure Stack Edge Pro GPU 디바이스에서 VM 태그 관리
description: Azure PowerShell을 사용하여 Azure Stack Edge에서 실행되는 가상 머신에 대한 가상 머신 태그를 만들고 관리하는 방법을 설명합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 07/12/2021
ms.author: alkohli
ms.openlocfilehash: 605a438e44220bc4eb97cc3718188d0458ea24d7
ms.sourcegitcommit: aaaa6ee55f5843ed69944f5c3869368e54793b48
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/13/2021
ms.locfileid: "113664506"
---
# <a name="tag-vms-on-azure-stack-edge-via-azure-powershell"></a>Azure PowerShell을 통해 Azure Stack Edge에서 VM 태그 지정

이 문서에서는 Azure PowerShell을 사용하여 Azure Stack Edge Pro GPU 디바이스에서 실행되는 VM(가상 머신)에 태그를 지정하는 방법을 설명합니다. 

## <a name="about-tags"></a>태그 정보

태그는 리소스 또는 리소스 그룹에 할당할 수 있는 사용자 정의 키-값 쌍입니다. 디바이스에서 실행되는 VM에 태그를 적용하여 논리적으로 VM을 분류로 구성할 수 있습니다. 작성 시 리소스에 태그를 배치하거나 기존 리소스에 추가할 수 있습니다. 예를 들어 조직의 엔지니어링 부서에서 사용하는 모든 VM에 이름 `Organization`과 값 `Engineering`을 적용할 수 있습니다.

태그에 대한 자세한 내용은 [AzureRM PowerShell을 통한 태그 관리](/powershell/module/azurerm.tags/?view=azurermps-6.13.0&preserve-view=true) 방법을 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

PowerShell을 통해 디바이스에 VM을 배포하려면 먼저 다음을 확인합니다.

- 디바이스에 연결하는 데 사용할 클라이언트에 액세스할 수 있습니다.
    - 클라이언트가 [지원되는 OS](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device)를 실행합니다.
    - [디바이스의 Azure Resource Manager에 연결](azure-stack-edge-gpu-connect-resource-manager.md) 지침에 따라 디바이스의 로컬 Azure Resource Manager에 연결하도록 클라이언트가 구성되어 있습니다.


## <a name="verify-connection-to-local-azure-resource-manager"></a>로컬 Azure Resource Manager에 대한 연결 확인

[!INCLUDE [azure-stack-edge-gateway-verify-azure-resource-manager-connection](../../includes/azure-stack-edge-gateway-verify-azure-resource-manager-connection.md)]


## <a name="add-a-tag-to-a-vm"></a>VM에 태그 추가

### <a name="az"></a>[Az](#tab/az)

1. 일부 매개 변수를 설정합니다.

    ```powershell
    $VMName = <VM Name>
    $VMRG = <VM Resource Group>
    $TagName = <Tag Name>
    $TagValue = <Tag Value>   
    ```

    출력의 예제는 다음과 같습니다.

    ```output
    PS C:\WINDOWS\system32> $VMName = "myazvm"
    PS C:\WINDOWS\system32> $VMRG = "myaseazrg"
    PS C:\WINDOWS\system32> $TagName = "Organization"
    PS C:\WINDOWS\system32> $TagValue = "Sales"
    ```

2. VM 개체와 해당 태그를 가져옵니다.

   ```powershell
   $VirtualMachine = Get-AzVM -ResourceGroupName $VMRG -Name $VMName
   $tags = $VirtualMachine.Tags
   ```

3. 태그를 추가하고 VM을 업데이트합니다. VM을 업데이트하는 데 몇 분 정도 걸릴 수 있습니다.

    선택적 **-Force** 플래그로 사용자 확인 없이 명령을 실행할 수 있습니다.

    ```powershell
    $tags.Add($TagName, $TagValue)
    Set-AzResource -ResourceId $VirtualMachine.Id -Tag $tags -Force
    ```

    출력의 예제는 다음과 같습니다.

    ```output
    PS C:\WINDOWS\system32> $VirtualMachine = Get-AzVM -ResourceGroupName $VMRG -Name $VMName
    PS C:\WINDOWS\system32> $tags = $VirtualMachine.Tags
    PS C:\WINDOWS\system32> $tags.Add($TagName, $TagValue)
    PS C:\WINDOWS\system32> Set-AzResource -ResourceID $VirtualMachine.ID -Tag $tags -Force   

    Name              : myazvm
    ResourceId        : /subscriptions/d64617ad-6266-4b19-45af-81112d213322/resourceGroups/myas
                        eazrg/providers/Microsoft.Compute/virtualMachines/myazvm
    ResourceName      : myazvm
    ResourceType      : Microsoft.Compute/virtualMachines
    ResourceGroupName : myaseazrg
    Location          : dbelocal
    SubscriptionId    : d64617ad-6266-4b19-45af-81112d213322
    Tags              : {Organization}
    Properties        : @{vmId=568a264f-c5d3-477f-a16c-4c5549eafa8c; hardwareProfile=;
                        storageProfile=; osProfile=; networkProfile=; diagnosticsProfile=;
                        provisioningState=Succeeded}
    ```



### <a name="azurerm"></a>[AzureRM](#tab/azurerm)

1. 일부 매개 변수를 설정합니다.

    ```powershell
    $VMName = <VM Name>
    $VMRG = <VM Resource Group>
    $TagName = <Tag Name>
    $TagValue = <Tag Value>   
    ```

    출력의 예제는 다음과 같습니다.

    ```output
    PS C:\WINDOWS\system32> $VMName = "myasetestvm1"
    PS C:\WINDOWS\system32> $VMRG = "myaserg2"
    PS C:\WINDOWS\system32> $TagName = "Organization"
    PS C:\WINDOWS\system32> $TagValue = "Sales"
    ```

2. VM 개체와 해당 태그를 가져옵니다.

   ```powershell
   $VirtualMachine = Get-AzureRmVM -ResourceGroupName $VMRG -Name $VMName
   $tags = $VirtualMachine.Tags
   ```

3. 태그를 추가하고 VM을 업데이트합니다. VM을 업데이트하는 데 몇 분 정도 걸릴 수 있습니다.

    선택적 **-Force** 플래그로 사용자 확인 없이 명령을 실행할 수 있습니다.

    ```powershell
    $tags.Add($TagName, $TagValue)
    Set-AzureRmResource -ResourceId $VirtualMachine.Id -Tag $tags -Force
    ```

    출력의 예제는 다음과 같습니다.

    ```output
    PS C:\WINDOWS\system32> $VirtualMachine = Get-AzureRMVM -ResourceGroupName $VMRG -Name $VMName
    PS C:\WINDOWS\system32> $tags = $VirtualMachine.Tags
    PS C:\WINDOWS\system32> $tags.Add($TagName, $TagValue)
    PS C:\WINDOWS\system32> Set-AzureRmResource -ResourceID $VirtualMachine.ID -Tag $tags -Force
    
    Name              : myasetestvm1
    ResourceId        : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGroups/myaserg2/providers/Microsoft.Compute/virtua
                        lMachines/myasetestvm1
    ResourceName      : myasetestvm1
    ResourceType      : Microsoft.Compute/virtualMachines
    ResourceGroupName : myaserg2
    Location          : dbelocal
    SubscriptionId    : 992601bc-b03d-4d72-598e-d24eac232122
    Tags              : {Organization}
    Properties        : @{vmId=958c0baa-e143-4d8a-82bd-9c6b1ba45e86; hardwareProfile=; storageProfile=; osProfile=; networkProfile=;
                        provisioningState=Succeeded}
    
    PS C:\WINDOWS\system32>
    ```

자세한 내용은 [Add-AzureRMTag](/powershell/module/azurerm.tags/remove-azurermtag?view=azurermps-6.13.0&preserve-view=true)를 참조하세요.

---

## <a name="view-tags-of-a-vm"></a>VM의 태그 보기

### <a name="az"></a>[Az](#tab/az)

디바이스에서 실행되는 특정 가상 머신에 적용된 태그를 볼 수 있습니다. 

1. 태그를 보려는 VM과 관련된 매개 변수를 정의합니다.

   ```powershell
   $VMName = <VM Name>
   $VMRG = <VM Resource Group>
   ```
    출력의 예제는 다음과 같습니다.

    ```output
    PS C:\WINDOWS\system32> $VMName = "myazvm"
    PS C:\WINDOWS\system32> $VMRG = "myaseazrg"
    ```
1. VM 개체를 가져오고 해당 태그를 봅니다.

   ```powershell
   $VirtualMachine = Get-AzVM -ResourceGroupName $VMRG -Name $VMName
   $VirtualMachine.Tags
   ```
    출력의 예제는 다음과 같습니다.

    ```output
    PS C:\WINDOWS\system32>  $VirtualMachine = Get-AzVM -ResourceGroupName $VMRG -Name $VMName     
    PS C:\WINDOWS\system32> $VirtualMachine.Tags
    
    Key          Value
    ---          -----
    Organization Sales    
    
    PS C:\WINDOWS\system32>
    ```


### <a name="azurerm"></a>[AzureRM](#tab/azurerm)

디바이스에서 실행되는 특정 가상 머신에 적용된 태그를 볼 수 있습니다. 

1. 태그를 보려는 VM과 관련된 매개 변수를 정의합니다.

   ```powershell
   $VMName = <VM Name>
   $VMRG = <VM Resource Group>
   ```
    출력의 예제는 다음과 같습니다.

    ```output
    PS C:\WINDOWS\system32> $VMName = "myasetestvm1"
    PS C:\WINDOWS\system32> $VMRG = "myaserg2"
    ```
1. VM 개체를 가져오고 해당 태그를 봅니다.

   ```powershell
   $VirtualMachine = Get-AzureRmVM -ResourceGroupName $VMRG -Name $VMName
   $VirtualMachine.Tags
   ```
    출력의 예제는 다음과 같습니다.

    ```output
    PS C:\WINDOWS\system32> $VirtualMachine = Get-AzureRMVM -ResourceGroupName $VMRG -Name $VMName
    PS C:\WINDOWS\system32> $VirtualMachine

    ResourceGroupName : myaserg2
    Id                : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGroups/myaserg2/providers/Microsoft.Compute/virtua
    lMachines/myasetestvm1
    VmId              : 958c0baa-e143-4d8a-82bd-9c6b1ba45e86
    Name              : myasetestvm1
    Type              : Microsoft.Compute/virtualMachines
    Location          : dbelocal
    Tags              : {"Organization":"Sales"}
    HardwareProfile   : {VmSize}
    NetworkProfile    : {NetworkInterfaces}
    OSProfile         : {ComputerName, AdminUsername, LinuxConfiguration, Secrets}
    ProvisioningState : Succeeded
    StorageProfile    : {ImageReference, OsDisk, DataDisks}
    
    PS C:\WINDOWS\system32>
    ```
---

## <a name="view-tags-for-all-resources"></a>모든 리소스에 대한 태그 보기

### <a name="az"></a>[Az](#tab/az)

디바이스의 로컬 Azure Resource Manager 구독(Azure 구독과 다름)에있는 모든 리소스의 현재 태그 목록을 보려면 `Get-AzTag` 명령을 사용합니다.


다음은 여러 VM이 디바이스에서 실행 중이고 모든 VM의 태그를 모두 보려는 경우의 예제 출력입니다.

```output
PS C:\WINDOWS\system32> Get-AzTag

Name         Count
----         -----
Organization 2

PS C:\WINDOWS\system32>
```

위의 출력은 디바이스에서 실행되는 VM에 두 개의 `Organization` 태그가 있음을 나타냅니다.

자세한 정보를 보려면 `-Detailed` 매개 변수를 사용하세요.

```output
PS C:\WINDOWS\system32> Get-AzTag -Detailed |fl

Name        : Organization
ValuesTable :
              Name         Count
              ===========  =====
              Sales        1
              Engineering  1
Count       : 2
Values      : {Sales, Engineering}

PS C:\WINDOWS\system32>
```

위의 출력은 두 개의 태그 중에서 하나의 VM이 `Engineering`으로 태그가 지정되고 하나의 VM은 `Sales`에 속하는 것으로 태그가 지정되었음을 나타냅니다.

### <a name="azurerm"></a>[AzureRM](#tab/azurerm)

디바이스의 로컬 Azure Resource Manager 구독(Azure 구독과 다름)에있는 모든 리소스의 현재 태그 목록을 보려면 `Get-AzureRMTag` 명령을 사용합니다.


다음은 여러 VM이 디바이스에서 실행 중이고 모든 VM의 태그를 모두 보려는 경우의 예제 출력입니다.

```powershell
PS C:\WINDOWS\system32> Get-AzureRMTag

Name         Count
----         -----
Organization 3

PS C:\WINDOWS\system32>
```

위의 출력은 디바이스에서 실행되는 VM에 3개의 `Organization` 태그가 있음을 나타냅니다.

자세한 정보를 보려면 `-Detailed` 매개 변수를 사용하세요.

```output
PS C:\WINDOWS\system32> Get-AzureRMTag -Detailed |fl

Name        : Organization
ValuesTable :
              Name         Count
              ===========  =====
              Engineering  2
              Sales        1

Count       : 3
Values      : {Engineering, Sales}

PS C:\WINDOWS\system32>
```

위의 출력은 3개의 태그 중에서 2개의 VM이 `Engineering`으로 태그가 지정되고 1개의 VM은 `Sales`에 속하는 것으로 태그가 지정되었음을 나타냅니다.

---

## <a name="remove-a-tag-from-a-vm"></a>VM에서 태그 제거

### <a name="az"></a>[Az](#tab/az)

1. 일부 매개 변수를 설정합니다. 

    ```powershell
    $VMName = <VM Name>
    $VMRG = <VM Resource Group>
    $TagName = <Tag Name>
   ``` 
    출력의 예제는 다음과 같습니다.

    ```output
    PS C:\WINDOWS\system32> $VMName = "myaselinuxvm1"
    PS C:\WINDOWS\system32> $VMRG = "myaserg1"
    PS C:\WINDOWS\system32> $TagName = "Organization" 
    ```
2. VM 개체를 가져옵니다.

    ```powershell
    $VirtualMachine = Get-AzVM -ResourceGroupName $VMRG -Name $VMName
    $VirtualMachine   
    ```   

    출력의 예제는 다음과 같습니다.

    ```output
    PS C:\WINDOWS\system32> $VirtualMachine = Get-AzVM -ResourceGroupName $VMRG -Name $VMName
    PS C:\WINDOWS\system32> $VirtualMachine
    
    ResourceGroupName  : myaseazrg
    Id                 : /subscriptions/d64617ad-6266-4b19-45af-81112d213322/resourceGroups/mya
    seazrg/providers/Microsoft.Compute/virtualMachines/myazvm
    VmId               : 568a264f-c5d3-477f-a16c-4c5549eafa8c
    Name               : myazvm
    Type               : Microsoft.Compute/virtualMachines
    Location           : dbelocal
    Tags               : {"Organization":"Sales"}
    DiagnosticsProfile : {BootDiagnostics}
    HardwareProfile    : {VmSize}
    NetworkProfile     : {NetworkInterfaces}
    OSProfile          : {ComputerName, AdminUsername, LinuxConfiguration, Secrets,
    AllowExtensionOperations, RequireGuestProvisionSignal}
    ProvisioningState  : Succeeded
    StorageProfile     : {ImageReference, OsDisk, DataDisks}
    
    PS C:\WINDOWS\system32>
    ```
3. 태그를 제거하고 VM을 업데이트합니다. 선택적 `-Force` 플래그로 사용자 확인 없이 명령을 실행합니다.

    ```powershell
    $tags = $VirtualMachine.Tags
    $tags.Remove($TagName)
    Set-AzResource -ResourceId $VirtualMachine.Id -Tag $tags -Force
    ```
    출력의 예제는 다음과 같습니다.

    ```output
    PS C:\WINDOWS\system32> $tags = $VirtualMachine.Tags
    PS C:\WINDOWS\system32> $tags.Remove($TagName)
    True
    PS C:\WINDOWS\system32> Set-AzResource -ResourceId $VirtualMachine.Id -Tag $tags -Force
    
    Name              : myazvm
    ResourceId        : /subscriptions/d64617ad-6266-4b19-45af-81112d213322/resourceGroups/myas
                        eazrg/providers/Microsoft.Compute/virtualMachines/myazvm
    ResourceName      : myazvm
    ResourceType      : Microsoft.Compute/virtualMachines
    ResourceGroupName : myaseazrg
    Location          : dbelocal
    SubscriptionId    : d64617ad-6266-4b19-45af-81112d213322
    Tags              : {}
    Properties        : @{vmId=568a264f-c5d3-477f-a16c-4c5549eafa8c; hardwareProfile=;
                        storageProfile=; osProfile=; networkProfile=; diagnosticsProfile=;
                        provisioningState=Succeeded}
    
    PS C:\WINDOWS\system32>
    ```

### <a name="azurerm"></a>[AzureRM](#tab/azurerm)

1. 일부 매개 변수를 설정합니다. 

    ```powershell
    $VMName = <VM Name>
    $VMRG = <VM Resource Group>
    $TagName = <Tag Name>
   ``` 
    출력의 예제는 다음과 같습니다.

    ```output
    PS C:\WINDOWS\system32> $VMName = "myaselinuxvm1"
    PS C:\WINDOWS\system32> $VMRG = "myaserg1"
    PS C:\WINDOWS\system32> $TagName = "Organization" 
    ```
2. VM 개체를 가져옵니다.

    ```powershell
    $VirtualMachine = Get-AzureRmVM -ResourceGroupName $VMRG -Name $VMName
    $VirtualMachine   
    ```   

    출력의 예제는 다음과 같습니다.

    ```output
    PS C:\WINDOWS\system32> $VirtualMachine = Get-AzureRMVM -ResourceGroupName $VMRG -Name $VMName
    ResourceGroupName : myaserg1
    Id                : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGroups/myaserg1/providers/Microsoft.Compute/virtualMachines/myaselinuxvm1
    VmId              : 290b3fdd-0c99-4905-9ea1-cf93cd6f25ee
    Name              : myaselinuxvm1
    Type              : Microsoft.Compute/virtualMachines
    Location          : dbelocal
    Tags              : {"Organization":"Engineering"}
    HardwareProfile   : {VmSize}
    NetworkProfile    : {NetworkInterfaces}
    OSProfile         : {ComputerName, AdminUsername, LinuxConfiguration, Secrets}
    ProvisioningState : Succeeded
    StorageProfile    : {ImageReference, OsDisk, DataDisks}
                                                                              PS C:\WINDOWS\system32> 
    ```
3. 태그를 제거하고 VM을 업데이트합니다. 선택적 `-Force` 플래그로 사용자 확인 없이 명령을 실행합니다.

    ```powershell
    $tags = $VirtualMachine.Tags
    $tags.Remove($TagName)
    Set-AzureRmResource -ResourceId $VirtualMachine.Id -Tag $tags [-Force]
    ```
    출력의 예제는 다음과 같습니다.

    ```output
    PS C:\WINDOWS\system32> $tags = $Virtualmachine.Tags
    PS C:\WINDOWS\system32> $tags
    Key          Value
    ---          -----
    Organization Engineering
    PS C:\WINDOWS\system32> $tags.Remove($TagName)
    True
    PS C:\WINDOWS\system32> Set-AzureRMResource -ResourceID $VirtualMachine.ID -Tag $tags -Force
    Name              : myaselinuxvm1
    ResourceId        : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGrou
                        ps/myaserg1/providers/Microsoft.Compute/virtualMachines/myaselin
                        uxvm1
    ResourceName      : myaselinuxvm1
    ResourceType      : Microsoft.Compute/virtualMachines
    ResourceGroupName : myaserg1
    Location          : dbelocal
    SubscriptionId    : 992601bc-b03d-4d72-598e-d24eac232122
    Tags              : {}
    Properties        : @{vmId=290b3fdd-0c99-4905-9ea1-cf93cd6f25ee; hardwareProfile=;
                        storageProfile=; osProfile=; networkProfile=;
                        provisioningState=Succeeded}
    PS C:\WINDOWS\system32>
    ```
---

## <a name="next-steps"></a>다음 단계

- [PowerShell에서 az cmdlet을 사용하여 Azure에서 가상 머신에 태그를 지정하는 방법](../virtual-machines/tag-powershell.md)을 알아봅니다.
- [PowerShell에서 AzureRM cmdlet을 통해 태그를 관리](/powershell/module/azurerm.tags/?view=azurermps-6.13.0&preserve-view=true)하는 방법을 알아봅니다.
