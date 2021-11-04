---
title: 세션 호스트 가상 머신에 Windows 라이선스 적용 - Azure
description: Azure Virtual Desktop VM에 Windows 라이선스를 적용하는 방법을 설명합니다.
author: Heidilohr
ms.topic: how-to
ms.date: 08/14/2019
ms.author: helohr
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 856cefa33948f530ee859ee1eeadfaf2cdf73de6
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131471785"
---
# <a name="apply-windows-license-to-session-host-virtual-machines"></a>세션 호스트 가상 머신에 Windows 라이선스 적용

Azure Virtual Desktop 워크로드를 실행하는 데 적합한 라이선스가 있는 고객은 세션 호스트 가상 머신에 Windows 라이선스를 적용하여 추가 라이선스 비용을 지불하지 않고도 해당 가상 머신을 실행할 수 있습니다. 자세한 내용은 [Azure Virtual Desktop 가격](https://azure.microsoft.com/pricing/details/virtual-desktop/)을 참조하세요.

## <a name="ways-to-use-your-azure-virtual-desktop-license"></a>Azure Virtual Desktop 라이선스를 사용하는 방법
Azure Virtual Desktop 라이선스를 사용하면 호스트 풀에 세션 호스트로 등록되어 있는 모든 Windows 또는 Windows Server 가상 머신에 라이선스를 적용하고 사용자 연결을 수신할 수 있습니다. 이 라이선스는 파일 공유 서버, 도메인 컨트롤러 등으로 실행되는 가상 머신에는 적용되지 않습니다.

Azure Virtual Desktop 라이선스를 사용하는 몇 가지 방법이 있습니다.
- [Azure Marketplace 제공 사항](./create-host-pools-azure-marketplace.md)을 사용하여 호스트 풀과 해당 세션 호스트 가상 머신을 만들 수 있습니다. 이 방법으로 만든 가상 머신에는 자동으로 라이선스가 적용됩니다.
- [GitHub Azure Resource Manager 템플릿](./virtual-desktop-fall-2019/create-host-pools-arm-template.md)을 사용하여 호스트 풀과 해당 세션 호스트 가상 머신을 만들 수 있습니다. 이 방법으로 만든 가상 머신에는 자동으로 라이선스가 적용됩니다.
- 기존 세션 호스트 가상 머신에 라이선스를 적용할 수 있습니다. 이렇게 하려면 먼저 [PowerShell을 사용하여 호스트 풀 만들기](./create-host-pools-powershell.md)의 지침에 따라 호스트 풀 및 연결된 VM을 만든 다음 이 문서로 돌아와서 라이선스 적용 방법을 알아보세요.

## <a name="apply-a-windows-license-to-a-session-host-vm"></a>세션 호스트 VM에 Windows 라이선스 적용
먼저 [최신 Azure PowerShell을 설치 및 구성](/powershell/azure/)했는지 확인합니다. 다음 PowerShell cmdlet을 실행하여 Windows 라이선스를 적용합니다.

```powershell
$vm = Get-AzVM -ResourceGroup <resourceGroupName> -Name <vmName>
$vm.LicenseType = "Windows_Client"
Update-AzVM -ResourceGroupName <resourceGroupName> -VM $vm
```

## <a name="verify-your-session-host-vm-is-utilizing-the-licensing-benefit"></a>세션 호스트 VM이 라이선싱 혜택을 사용하고 있는지 확인
VM을 배포한 후 이 cmdlet을 실행하여 라이선스 유형을 확인합니다.
```powershell
Get-AzVM -ResourceGroupName <resourceGroupName> -Name <vmName>
```

적용된 Windows 라이선스를 사용하는 세션 호스트 VM은 다음과 유사하게 표시됩니다.

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Client
```

적용된 Windows 라이선스를 사용하지 않는 VM은 다음과 유사하게 표시됩니다.

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              :
```

다음 cmdlet을 실행하여 Azure 구독에서 Windows 라이선스가 적용된 모든 세션 호스트 VM의 목록을 확인합니다.

```powershell
$vms = Get-AzVM
$vms | Where-Object {$_.LicenseType -like "Windows_Client"} | Select-Object ResourceGroupName, Name, LicenseType
```

## <a name="requirements-for-deploying-windows-server-remote-desktop-services"></a>Windows Server 원격 데스크톱 서비스 배포 요구 사항

배포에서 Azure 가상 데스크톱 호스트로 Windows 서버를 배포 하는 경우 해당 가상 머신에서 원격 데스크톱 서비스 라이선스 서버에 액세스할 수 있어야 합니다. 원격 데스크톱 서비스 라이선스 서버는 온-프레미스 또는 Azure에 있을 수 있습니다. 자세한 내용은 [원격 데스크톱 서비스 라이선스 서버 활성화](/windows-server/remote/remote-desktop-services/rds-activate-license-server)를 참조하세요.
