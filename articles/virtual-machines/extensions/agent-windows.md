---
title: Azure Virtual Machines 에이전트 개요
description: Azure Virtual Machines 에이전트 개요
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
ms.author: amjads
author: amjads1
ms.collection: windows
ms.date: 07/20/2019
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 8e5118b25238a9483eba3e74a65fe97186d32168
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130236849"
---
# <a name="azure-virtual-machine-agent-overview"></a>Azure Virtual Machines 에이전트 개요
Microsoft Azure VM 에이전트(가상 머신 에이전트)는 Azure 패브릭 컨트롤러와 VM(가상 머신)의 상호 작용을 관리하는 안전하고 간단한 프로세스입니다. VM 에이전트는 Azure 가상 머신 확장을 설정하고 실행하는 데 기본적인 역할을 수행합니다. VM 확장을 사용하면 소프트웨어 설치 및 구성과 같은 VM의 배포 후 구성을 설정할 수 있습니다. 또한 VM 확장을 사용하면 VM의 관리 암호를 다시 설정하는 등의 복구 기능도 사용할 수 있습니다. Azure VM 에이전트가 없으면 VM 확장을 실행할 수 없습니다.

이 문서에서는 Azure Virtual Machines 에이전트의 설치 및 검색에 대해 자세히 설명합니다.

## <a name="install-the-vm-agent"></a>VM 에이전트 설치

### <a name="azure-marketplace-image"></a>Azure Marketplace 이미지

Azure VM 에이전트는 Azure Marketplace 이미지에서 배포된 모든 Windows VM에 기본적으로 설치됩니다. 포털, PowerShell, 명령줄 인터페이스 또는 Azure Resource Manager 템플릿에서 Azure Marketplace 이미지를 배포하면 Azure VM 에이전트도 설치됩니다.

Windows 게스트 에이전트 패키지는 두 부분으로 나뉘어 있습니다.

- PA(프로비전 에이전트)
- WinGA(Windows 게스트 에이전트)

VM을 부팅하려면 VM에 PA가 설치되어 있어야 하지만 WinGA는 설치할 필요가 없습니다. VM 배포 시 WinGA를 설치하지 않도록 선택할 수 있습니다. 다음 예제에서는 Azure Resource Manager 템플릿으로 *provisionVmAgent* 옵션을 선택하는 방법을 보여 줍니다.

```json
"resources": [{
"name": "[parameters('virtualMachineName')]",
"type": "Microsoft.Compute/virtualMachines",
"apiVersion": "2016-04-30-preview",
"location": "[parameters('location')]",
"dependsOn": ["[concat('Microsoft.Network/networkInterfaces/', parameters('networkInterfaceName'))]"],
"properties": {
    "osProfile": {
    "computerName": "[parameters('virtualMachineName')]",
    "adminUsername": "[parameters('adminUsername')]",
    "adminPassword": "[parameters('adminPassword')]",
    "windowsConfiguration": {
        "provisionVmAgent": "false"
}
```

에이전트가 설치되어 있지 않으면 Azure Backup 또는 Azure Security와 같은 일부 Azure 서비스를 사용할 수 없습니다. 이러한 서비스를 사용하려면 확장을 설치해야 합니다. WinGA 없이 VM을 배포한 경우 나중에 최신 버전의 에이전트를 설치할 수 있습니다.

### <a name="manual-installation"></a>수동 설치
Windows VM 에이전트는 Windows 설치 관리자 패키지를 사용하여 수동으로 설치할 수 있습니다. Azure에 배포된 사용자 지정 VM 이미지를 만들 때 수동 설치가 필요할 수 있습니다. Windows VM 에이전트를 수동으로 설치하려면 [VM 에이전트 설치 관리자를 다운로드합니다](https://go.microsoft.com/fwlink/?LinkID=394789). [GitHub Windows IAAS VM 에이전트 릴리스](https://github.com/Azure/WindowsVMAgent/releases)에서 특정 버전을 검색할 수도 있습니다. VM 에이전트는 Windows Server 2008(64비트) 이상에서 지원됩니다.

> [!NOTE]
> ProvisionVMAgent를 사용하지 않고 이미지에서 배포된 VM에 VMAgent를 수동으로 설치한 후 AllowExtensionOperations 옵션을 업데이트해야 합니다.

```powershell
$vm.OSProfile.AllowExtensionOperations = $true
$vm | Update-AzVM
```

### <a name="prerequisites"></a>필수 구성 요소

- Windows VM 에이전트에는 .NET Framework 4.0을 사용하여 실행할 Windows Server 2008 SP2(64 비트) 이상이 필요합니다. [Azure의 가상 머신 에이전트에 대한 최소 버전 지원](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support)을 참조하세요.

- VM에 IP 주소 168.63.129.16에 대한 액세스 권한이 있는지 확인합니다. 자세한 내용은 [IP 주소 168.63.129.16이란?](../../virtual-network/what-is-ip-address-168-63-129-16.md) 페이지를 참조하세요.

- 게스트 VM 내에 DHCP가 사용하도록 설정되어 있는지 확인합니다. 이는 DHCP에서 호스트 또는 패브릭 주소를 가져와서 IaaS VM 에이전트 및 확장이 작동하도록 하기 위해 필요합니다. 정적 프라이빗 IP가 필요한 경우 Azure Portal 또는 PowerShell을 통해 구성해야 하며 VM 내 DHCP 옵션을 사용할 수 있는지 확인합니다. PowerShell을 사용하여 고정 IP 주소를 설정하는 방법에 대해 [자세히 알아봅니다](../../virtual-network/ip-services/virtual-networks-static-private-ip-arm-ps.md).


## <a name="detect-the-vm-agent"></a>VM 에이전트 검색

### <a name="powershell"></a>PowerShell

Azure Resource Manager PowerShell 모듈을 사용하여 Azure VM에 대한 정보를 검색할 수 있습니다. Azure VM 에이전트의 프로비전 상태와 같이 VM에 대한 정보를 보려면 [Get-AzVM](/powershell/module/az.compute/get-azvm)을 사용합니다.

```powershell
Get-AzVM
```

다음의 요약 예 출력에서는 `OSProfile` 내부에 중첩된 *ProvisionVMAgent* 속성을 보여줍니다. 이 속성을 사용하여 VM 에이전트가 VM에 배포되었는지 여부를 확인할 수 있습니다.

```powershell
OSProfile                  :
  ComputerName             : myVM
  AdminUsername            : myUserName
  WindowsConfiguration     :
    ProvisionVMAgent       : True
    EnableAutomaticUpdates : True
```

다음 스크립트는 VM 이름의 간단한 목록과 VM 에이전트의 상태를 반환하는 데 사용할 수 있습니다.

```powershell
$vms = Get-AzVM

foreach ($vm in $vms) {
    $agent = $vm | Select -ExpandProperty OSProfile | Select -ExpandProperty Windowsconfiguration | Select ProvisionVMAgent
    Write-Host $vm.Name $agent.ProvisionVMAgent
}
```

### <a name="manual-detection"></a>수동 검색

Microsoft VM에 로그인하면 작업 관리자를 사용하여 실행 중인 프로세스를 검사할 수 있습니다. Azure VM 에이전트를 확인하려면 작업 관리자를 열고 *세부 정보* 탭을 클릭하고 **WindowsAzureGuestAgent.exe** 라는 프로세스 이름을 찾습니다. 이 프로세스가 있으면 VM 에이전트가 설치되어 있는 것입니다.


## <a name="upgrade-the-vm-agent"></a>VM 에이전트 업그레이드
Windows용 Azure VM 에이전트는 Azure Marketplace에서 배포된 이미지에서 자동으로 업그레이드됩니다. 새 VM이 Azure에 배포되면 VM 프로비전 시 최신 VM 에이전트가 제공됩니다. 에이전트를 수동으로 설치하거나 사용자 지정 VM 이미지를 배포하는 경우 이미지를 만들 때 새 VM 에이전트를 포함하도록 수동으로 업데이트해야 합니다.

## <a name="windows-guest-agent-automatic-logs-collection"></a>Windows 게스트 에이전트 자동 로그 수집
Windows 게스트 에이전트에는 일부 로그를 자동으로 수집하는 기능이 있습니다. 이 기능은 CollectGuestLogs.exe 프로세스의 컨트롤러입니다. 이 기능은 PaaS 클라우드 서비스와 IaaS 가상 머신 모두에 있으며, 목표는 VM에서 일부 진단 로그를 빠르게 자동 수집하여 오프라인 분석에 사용할 수 있도록 하는 것입니다. 수집된 로그는 이벤트 로그, OS 로그, Azure 로그 및 일부 레지스트리 키입니다. 이 기능은 VM의 호스트로 전송되는 ZIP 파일을 생성합니다. 그러면 엔지니어링 팀 및 지원 전문가가 이 ZIP 파일을 보고 VM을 소유한 고객의 요청에 따라 문제를 조사할 수 있습니다.

## <a name="guest-agent-and-osprofile-certificates"></a>게스트 에이전트 및 OSProfile 인증서
Azure VM 에이전트는 VM 또는 가상 머신 확장 집합의 `OSProfile`에 참조된 인증서를 설치합니다. 이러한 인증서를 게스트 VM 내부의 인증서 MMC 콘솔에서 수동으로 제거하여도 게스트 에이전트가 다시 추가합니다.
인증서를 영구적으로 제거하려면 `OSProfile`에서 제거한 다음 게스트 운영 체제 내에서 제거해야 합니다.

가상 머신의 경우 [Remove-AzVMSecret]()를 사용하여 `OSProfile`에서 인증서를 제거합니다.

Virtual Machine Scale Set 인증서에 대한 자세한 내용은 [Virtual Machine Scale Sets - 사용되지 않는 인증서를 제거하려면 어떻게 해야 하나요?](../../virtual-machine-scale-sets/virtual-machine-scale-sets-faq.yml#how-do-i-remove-deprecated-certificates-)를 참조하세요.


## <a name="next-steps"></a>다음 단계
VM 확장에 대한 자세한 내용은 [Azure 가상 머신 확장 및 기능 개요](overview.md)를 참조하세요.
