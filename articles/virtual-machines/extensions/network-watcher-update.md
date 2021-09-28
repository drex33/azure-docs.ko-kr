---
title: Network Watcher 확장을 최신 버전으로 업데이트
description: Azure Network Watcher 확장을 최신 버전으로 업데이트하는 방법을 알아봅니다.
services: virtual-machines
documentationcenter: ''
author: damendo
manager: balar
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.subservice: extensions
ms.collection: windows
ms.topic: article
ms.workload: infrastructure-services
ms.date: 09/23/2020
ms.author: damendo
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 8efe40fd4a60da7fa77e642d8ad95b3495d9771e
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/28/2021
ms.locfileid: "129094598"
---
# <a name="update-the-network-watcher-extension-to-the-latest-version"></a>Network Watcher 확장을 최신 버전으로 업데이트

## <a name="overview"></a>개요

[Azure Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md)는 Azure 네트워크를 모니터링하는 네트워크 성능 모니터링, 진단 및 분석 서비스입니다. Network Watcher 에이전트 VM(가상 머신) 확장은 Azure VM에서 요청 시 네트워크 트래픽 캡처 및 기타 고급 기능을 위한 요구 사항입니다. Network Watcher 확장은 연결 모니터, 연결 모니터(미리 보기), 연결 문제 해결 및 패킷 캡처와 같은 기능에서 사용됩니다.

## <a name="prerequisites"></a>필수 구성 요소

이 문서에서는 VM에 Network Watcher 확장 프로그램을 설치했다고 가정합니다.

## <a name="latest-version"></a>최신 버전

Network Watcher 확장의 최신 버전은 현재 `1.4.1974.1`입니다.

## <a name="update-your-extension-using-a-powershell-script"></a>PowerShell 스크립트를 사용하여 확장 업데이트
여러 VM을 한 번에 업데이트해야 하는 대량 배포를 사용하는 고객 선택한 VM을 수동으로 업데이트하려면 다음 섹션을 참조하세요. 

```powershell
<#
    .SYNOPSIS
    This script will scan all VMs in the provided subscription and upgrade any out of date AzureNetworkWatcherExtensions

    .DESCRIPTION
    This script should be no-op if AzureNetworkWatcherExtensions are up to date
    Requires Azure PowerShell 4.2 or higher to be installed (e.g. Install-Module AzureRM).

    .EXAMPLE
    .\UpdateVMAgentsInSub.ps1 -SubID F4BC4873-5DAB-491E-B713-1358EF4992F2 -NoUpdate

#>
[CmdletBinding()]
param(
    [Parameter(Mandatory=$true)]
    [string] $SubID,
    [Parameter(Mandatory=$false)]
    [Switch] $NoUpdate = $false,
    [Parameter(Mandatory=$false)]
    [string] $MinVersion = "1.4.1974.1"
)


function NeedsUpdate($version)
{
    if ($version -eq $MinVersion)
    {
        return $false
    }

    $lessThan = $true;
    $versionParts = $version -split '\.';
    $minVersionParts = $MinVersion -split '\.';
    for ($i = 0; $i -lt $versionParts.Length; $i++)
    {
        if ([int]$versionParts[$i] -gt [int]$minVersionParts[$i])
        {
            $lessThan = $false;
            break;
        }
    }

    return $lessThan
}

Write-Host "Scanning all VMs in the subscription: $($SubID)"
Select-AzSubscription -SubscriptionId $SubID;
$vms = Get-AzVM;
$foundVMs = $false;
Write-Host "Starting VM search, this may take a while"

foreach ($vmName in $vms)
{
    # Get Detailed VM info
    $vm = Get-AzVM -ResourceGroupName $vmName.ResourceGroupName -Name $vmName.name -Status;
    $isWindows = $vm.OsVersion -match "Windows";
    foreach ($extension in $vm.Extensions)
    {
        if ($extension.Name -eq "AzureNetworkWatcherExtension")
        {
            if (NeedsUpdate($extension.TypeHandlerVersion))
            {
                $foundVMs = $true;
                if (-not ($NoUpdate))
                {
                    Write-Host "Found VM that needs to be updated: subscriptions/$($SubID)/resourceGroups/$($vm.ResourceGroupName)/providers/Microsoft.Compute/virtualMachines/$($vm.Name) -> Updating " -NoNewline
                    Remove-AzVMExtension -ResourceGroupName $vm.ResourceGroupName -VMName $vm.Name -Name "AzureNetworkWatcherExtension" -Force
                    Write-Host "... " -NoNewline
                    $type = if ($isWindows) { "NetworkWatcherAgentWindows" } else { "NetworkWatcherAgentLinux" };
                    Set-AzVMExtension -ResourceGroupName $vm.ResourceGroupName -Location $vmName.Location -VMName $vm.Name -Name "AzureNetworkWatcherExtension" -Publisher "Microsoft.Azure.NetworkWatcher" -Type $type -typeHandlerVersion "1.4"
                    Write-Host "Done"
                }
                else
                {
                    Write-Host "Found $(if ($isWindows) {"Windows"} else {"Linux"}) VM that needs to be updated: subscriptions/$($SubID)/resourceGroups/$($vm.ResourceGroupName)/providers/Microsoft.Compute/virtualMachines/$($vm.Name)"
                }
            }
        }
    }
}

if ($foundVMs)
{
    Write-Host "Finished $(if ($NoUpdate) {"searching"} else {"updating"}) out of date AzureNetworkWatcherExtension on VMs"
}
else
{
    Write-Host "All AzureNetworkWatcherExtensions up to date"
}

```

## <a name="update-your-extension-manually"></a>수동으로 확장 업데이트

확장을 업데이트하려면 확장 버전을 알아야 합니다.

### <a name="check-your-extension-version"></a>확장 버전 확인

Azure Portal, Azure CLI 또는 PowerShell을 사용하여 확장 버전을 확인할 수 있습니다.

#### <a name="usetheazureportal"></a>Azure Portal 사용

1. Azure Portal에서 VM의 **확장** 창으로 이동합니다.
1. **AzureNetworkWatcher** 확장을 선택하여 세부 정보 창을 표시합니다.
1. **버전** 필드에서 버전 번호를 찾습니다.  

#### <a name="use-the-azure-cli"></a>Azure CLI 사용

Azure CLI 프롬프트에서 다음 명령을 실행합니다.

```azurecli
az vm get-instance-view --resource-group  "SampleRG" --name "Sample-VM"
```
출력에서 **"AzureNetworkWatcherExtension"** 을 찾고 출력의 “TypeHandlerVersion” 필드에서 버전 번호를 확인합니다.  참고: 확장에 대한 정보는 JSON 출력에 여러 번 나타납니다. "확장" 블록 아래에서 확장의 전체 버전 번호를 확인해야 합니다. 

아래와 같은 내용이 표시됩니다. ![Azure CLI 스크린샷](./media/network-watcher/azure-cli-screenshot.png)

#### <a name="usepowershell"></a>PowerShell 사용

PowerShell 프롬프트에서 다음 명령을 실행합니다.

```powershell
Get-AzVM -ResourceGroupName "SampleRG" -Name "Sample-VM" -Status
```
출력에서 Azure Network Watcher 확장을 찾아 출력의 "TypeHandlerVersion" 필드에서 버전 번호를 확인합니다.   

다음과 같은 내용이 표시됩니다. ![PowerShell 스크린샷](./media/network-watcher/powershell-screenshot.png)

### <a name="update-your-extension"></a>확장 업데이트

버전이 위에서 언급한 최신 버전보다 낮은 경우 다음 옵션 중 하나를 사용하여 확장을 업데이트합니다.

#### <a name="option-1-use-powershell"></a>옵션 1: PowerShell 사용

다음 명령을 실행합니다.

```powershell
#Linux command
Set-AzVMExtension -ResourceGroupName "myResourceGroup1" -Location "WestUS" -VMName "myVM1" -Name "AzureNetworkWatcherExtension" -Publisher "Microsoft.Azure.NetworkWatcher" -Type "NetworkWatcherAgentLinux"

#Windows command
Set-AzVMExtension -ResourceGroupName "myResourceGroup1" -Location "WestUS" -VMName "myVM1" -Name " AzureNetworkWatcherExtension" -Publisher "Microsoft.Azure.NetworkWatcher" -Type "NetworkWatcherAgentWindows" -ForceRerun "True"

```

이 명령이 작동하지 않으면 아래 단계를 사용하여 확장을 제거했다가 다시 설치합니다. 그러면 최신 버전이 자동으로 추가됩니다.

확장 제거 

```powershell
#Same command for Linux and Windows
Remove-AzVMExtension -ResourceGroupName "SampleRG" -VMName "Sample-VM" -Name "AzureNetworkWatcherExtension"
``` 

확장 다시 설치

```powershell
#Linux command
Set-AzVMExtension -ResourceGroupName "SampleRG" -Location "centralus" -VMName "Sample-VM" -Name "AzureNetworkWatcherExtension" -Publisher "Microsoft.Azure.NetworkWatcher" -Type "NetworkWatcherAgentLinux" -typeHandlerVersion "1.4"

#Windows command
Set-AzVMExtension -ResourceGroupName "SampleRG" -Location "centralus" -VMName "Sample-VM" -Name "AzureNetworkWatcherExtension" -Publisher "Microsoft.Azure.NetworkWatcher" -Type "NetworkWatcherAgentWindows" -typeHandlerVersion "1.4"
```

#### <a name="option-2-use-the-azure-cli"></a>옵션 2: Azure CLI 사용

강제로 업그레이드합니다.

```azurecli
#Linux command
az vm extension set --resource-group "myResourceGroup1" --vm-name "myVM1" --name "NetworkWatcherAgentLinux" --publisher "Microsoft.Azure.NetworkWatcher" --force-update

#Windows command
az vm extension set --resource-group "myResourceGroup1" --vm-name "myVM1" --name "NetworkWatcherAgentWindows" --publisher "Microsoft.Azure.NetworkWatcher" --force-update
```

그래도 업데이트가 진행되지 않으면 확장을 제거했다가 다시 설치하고 다음 단계를 수행하여 최신 버전을 자동으로 추가합니다.

확장을 제거합니다.

```azurecli
#Same for Linux and Windows
az vm extension delete --resource-group "myResourceGroup1" --vm-name "myVM1" -n "AzureNetworkWatcherExtension"

```

확장을 다시 설치합니다.

```azurecli
#Linux command
az vm extension set --resource-group "DALANDEMO" --vm-name "Linux-01" --name "NetworkWatcherAgentLinux" --publisher "Microsoft.Azure.NetworkWatcher"

#Windows command
az vm extension set --resource-group "DALANDEMO" --vm-name "Linux-01" --name "NetworkWatcherAgentWindows" --publisher "Microsoft.Azure.NetworkWatcher"

```

#### <a name="option-3-reboot-your-vms"></a>옵션 3: VM 다시 부팅

Network Watcher 확장에 대해 자동 업그레이드를 true로 설정한 경우 VM 설치를 최신 확장으로 다시 부팅합니다.

## <a name="support"></a>지원

이 문서를 진행하다가 도움이 필요한 경우 [Linux](./network-watcher-linux.md) 또는 [Windows](./network-watcher-windows.md)에 대한 Network Watcher 확장 설명서를 참조하세요. [MSDN Azure 및 Stack Overflow 포럼](https://azure.microsoft.com/support/forums/)의 Azure 전문가에게 문의할 수도 있습니다. 또는 Azure 기술 지원 인시던트를 제출하세요. [Azure 지원 사이트](https://azure.microsoft.com/support/options/)로 가서 **지원 받기** 를 선택합니다. Azure 지원을 사용하는 방법에 대한 자세한 내용은 [Microsoft Azure 지원 FAQ](https://azure.microsoft.com/support/faq/)를 참조하세요.
