---
title: Azure VM 확장 및 Windows용 기능
description: 확장이 제공하거나 개선하는 기능별로 그룹화하여 Azure 가상 머신에 사용할 수 있는 확장을 알아봅니다.
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: amjads1
ms.author: amjads
ms.collection: windows
ms.date: 03/30/2018
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 8ab6b3d00f748fb5b3935988522191c749fa4fd9
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129275281"
---
# <a name="virtual-machine-extensions-and-features-for-windows"></a>Windows용 가상 머신 확장 및 기능

Azure VM(가상 머신) 확장은 Azure VM에서 배포 후 구성 및 자동화 태스크를 제공하는 작은 애플리케이션입니다. 예를 들어 가상 컴퓨터에 소프트웨어 설치, 바이러스 백신 보호가 필요한 경우, 또는 가상 컴퓨터 내부에서 스크립트를 실행하려면 VM 확장을 사용하면 됩니다. Azure VM 확장은 Azure CLI, PowerShell, Azure Resource Manager 템플릿 및 Azure Portal을 사용하여 실행할 수 있습니다. 확장을 새 VM 배포와 번들로 제공하거나, 기존 시스템에 대해 실행할 수 있습니다.

이 아티클에서는 VM 확장의 개요, Azure VM 확장 사용을 위한 필수 구성 요소, VM 확장을 검색, 관리 및 제거하는 방법에 대한 지침을 제공합니다. 많은 VM 확장 각각을 고유한 구성으로 사용할 수 있으므로 여기서는 일반적인 정보를 제공합니다. 확장 관련 세부 정보는 개별 확장과 관련된 각 문서에서 찾을 수 있습니다.

 

## <a name="use-cases-and-samples"></a>사용 사례 및 샘플

각각 특정 사용 사례가 있는 몇 가지 다른 Azure VM 확장을 사용할 수 있습니다. 일부 사례:

- Windows용 DSC 확장을 사용하여 VM에 PowerShell의 필요한 상태 구성을 적용합니다. 자세한 내용은 [Azure 필요한 상태 구성 확장](dsc-overview.md)을 참조하세요.
- 로그 분석 에이전트 VM 확장을 사용하여 VM의 모니터링을 구성합니다. 자세한 내용은 [Azure Monitor 로그에 Azure VM 연결](../../azure-monitor/vm/monitor-virtual-machine.md)을 참조하세요.
- Chef를 사용하여 Azure VM을 구성합니다. 자세한 내용은 [Chef를 사용하여 Azure VM 배포 자동화](/azure/developer/chef/windows-vm-configure)를 참조하세요.
- Datadog 확장으로 Azure 인프라의 모니터링을 구성합니다. 자세한 내용은 [Datadog 블로그](https://www.datadoghq.com/blog/introducing-azure-monitoring-with-one-click-datadog-deployment/)를 참조하세요.


프로세스 관련 확장 외에도 Windows 및 Linux 가상 머신에 대해 사용자 지정 스크립트 확장을 사용할 수 있습니다. Windows용 사용자 지정 스크립트 확장을 사용하면 PowerShell 스크립트를 VM에서 실행할 수 있습니다. 사용자 지정 스크립트는 네이티브 Azure 도구로 제공할 수 있는 것 이상의 구성이 필요한 Azure 배포를 디자인할 때 유용합니다. 자세한 내용은 [Windows VM 사용자 지정 스크립트 확장](custom-script-windows.md)을 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

VM에서 확장을 처리하려면 Azure Windows 에이전트를 설치해야 합니다. 일부 개별 확장에는 리소스에 대한 액세스 권한 또는 종속성 같은 필수 구성 요소가 있습니다.

### <a name="azure-vm-agent"></a>Azure VM 에이전트

Azure VM 에이전트는 Azure VM과 Azure 패브릭 컨트롤러 간 상호 작용을 관리합니다. VM 에이전트는 VM 확장 실행을 포함하여 Azure VM 배포 및 관리의 다양한 기능적 측면을 담당합니다. Azure VM 에이전트는 Azure Marketplace 이미지에 미리 설치되며 지원되는 운영 체제에 수동으로 설치될 수 있습니다. Windows용 Azure VM 에이전트는 Windows 게스트 에이전트라고 합니다.

지원되는 운영 체제 및 설치 지침에 대한 자세한 내용은 [Azure Virtual Machines 에이전트](agent-windows.md)를 참조하세요.

#### <a name="supported-agent-versions"></a>지원되는 에이전트 버전

가능한 최상의 환경을 제공하기 위해 에이전트의 최소 버전이 있습니다. 자세한 내용은 [이 문서](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support)를 참조하세요.

#### <a name="supported-oses"></a>지원되는 OS

Windows 게스트 에이전트는 여러 OS에서 실행되지만 확장 프레임워크는 OS 확장에 대한 제한이 있습니다. 자세한 내용은 [이 문서](https://support.microsoft.com/help/4078134/azure-extension-supported-operating-systems
)를 참조하세요.

일부 확장은 모든 OS에서 지원되지 않으며 *오류 코드 51, ‘지원되지 않는 OS’* 를 내보낼 수 있습니다. 지원 가능성에 대한 개별 확장 설명서를 확인합니다.

#### <a name="network-access"></a>네트워크 액세스

확장 패키지는 Azure Storage 확장 리포지토리에서 다운로드되고, 확장 상태 업로드는 Azure Storage에 게시됩니다. 에이전트의 [지원](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support) 버전을 사용하는 경우 에이전트를 사용하여 에이전트 통신을 위한 Azure 패브릭 컨트롤러에 통신을 리디렉션할 수 있는 것처럼 VM 지역에서 Azure Storage에 대한 액세스 권한을 허용하지 않아도 됩니다(개인 IP [168.63.129.16](../../virtual-network/what-is-ip-address-168-63-129-16.md)에서 권한 있는 채널을 통한 HostGAPlugin 기능). 지원되지 않는 버전의 에이전트가 설치된 경우 VM의 해당 지역에서 Azure Storage에 대한 아웃바운드 액세스 권한을 허용해야 합니다.

> [!IMPORTANT]
> 게스트 방화벽이나 프록시를 사용하여 *168.63.129.16* 에 대한 액세스를 차단한 경우 확장은 위와 관계없이 실패합니다. 포트 80, 443 및 32526가 필요합니다.

에이전트는 확장 패키지 및 보고 상태를 다운로드하는 데 사용할 수 있습니다. 예를 들어 확장을 설치하는 데 GitHub에서 스크립트(사용자 지정 스크립트)를 다운로드해야 하거나 Azure Storage(Azure Backup)에 대한 액세스 권한이 필요한 경우 방화벽/네트워크 보안 그룹 포트를 열어야 합니다. 확장마다 고유한 권한의 애플리케이션이므로 요구 사항이 다릅니다. Azure Storage 또는 Azure Active Directory에 대한 액세스 권한이 필요한 확장의 경우 Storage 또는 AzureActiveDirectory에 [Azure NSG 서비스 태그](../../virtual-network/network-security-groups-overview.md#service-tags)를 사용하여 액세스를 허용할 수 있습니다.

Windows 게스트 에이전트에는 에이전트 트래픽 요청을 리디렉션할 수 있는 프록시 서버를 지원하지 않습니다. 즉, Windows 게스트 에이전트는 사용자 지정 프록시(있는 경우)를 사용하여 인터넷 또는 IP 168.63.129.16을 통해 호스트에 있는 리소스에 액세스합니다.

## <a name="discover-vm-extensions"></a>VM 확장 검색

Azure VM에서 여러 다양한 VM 확장을 사용할 수 있습니다. 전체 목록을 보려면 [Get-AzVMExtensionImage](/powershell/module/az.compute/get-azvmextensionimage)를 사용합니다. 다음 예제에서는 *WestUS* 위치에서 모든 사용 가능한 확장을 나열합니다.

```powershell
Get-AzVmImagePublisher -Location "WestUS" |
Get-AzVMExtensionImageType |
Get-AzVMExtensionImage | Select Type, Version
```

## <a name="run-vm-extensions"></a>VM 확장 실행

Azure VM 확장은 기존 VM에서 실행됩니다. 이러한 기능은 이미 배포한 VM의 구성을 변경하거나 연결을 복구해야 할 때 유용합니다. 또한 VM 확장을 Azure Resource Manager 템플릿 배포와 번들로 묶을 수도 있습니다. Resource Manager 템플릿에서 확장을 사용하면 Azure VM을 배포하고, 배포 후 개입 없이 구성할 수 있습니다.

다음 방법을 사용하여 기존 VM에 대해 확장을 실행할 수 있습니다.

### <a name="powershell"></a>PowerShell

개별 확장을 실행하기 위한 몇 가지 PowerShell 명령이 존재합니다. 목록을 보려면 [Get-command](/powershell/module/microsoft.powershell.core/get-command)를 사용하고 *확장* 을 필터링합니다.

```powershell
Get-Command Set-Az*Extension* -Module Az.Compute
```

다음과 유사한 출력이 표시됩니다.

```powershell
CommandType     Name                                          Version    Source
-----------     ----                                          -------    ------
Cmdlet          Set-AzVMAccessExtension                       4.5.0      Az.Compute
Cmdlet          Set-AzVMADDomainExtension                     4.5.0      Az.Compute
Cmdlet          Set-AzVMAEMExtension                          4.5.0      Az.Compute
Cmdlet          Set-AzVMBackupExtension                       4.5.0      Az.Compute
Cmdlet          Set-AzVMBginfoExtension                       4.5.0      Az.Compute
Cmdlet          Set-AzVMChefExtension                         4.5.0      Az.Compute
Cmdlet          Set-AzVMCustomScriptExtension                 4.5.0      Az.Compute
Cmdlet          Set-AzVMDiagnosticsExtension                  4.5.0      Az.Compute
Cmdlet          Set-AzVMDiskEncryptionExtension               4.5.0      Az.Compute
Cmdlet          Set-AzVMDscExtension                          4.5.0      Az.Compute
Cmdlet          Set-AzVMExtension                             4.5.0      Az.Compute
Cmdlet          Set-AzVMSqlServerExtension                    4.5.0      Az.Compute
Cmdlet          Set-AzVmssDiskEncryptionExtension             4.5.0      Az.Compute
```

다음 예제에서는 사용자 지정 스크립트 확장을 사용하여 GitHub 리포지토리에서 대상 가상 머신에 스크립트를 다운로드한 후 스크립트를 실행합니다. 사용자 지정 스크립트 확장에 대한 자세한 내용은 [사용자 지정 스크립트 확장 개요](custom-script-windows.md)를 참조하세요.

```powershell
Set-AzVMCustomScriptExtension -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" -Name "myCustomScript" `
    -FileUri "https://raw.githubusercontent.com/neilpeterson/nepeters-azure-templates/master/windows-custom-script-simple/support-scripts/Create-File.ps1" `
    -Run "Create-File.ps1" -Location "West US"
```

다음 예제에서는VM 액세스 확장을 사용하여 Windows VM의 관리자 암호를 임시 암호로 다시 설정합니다. VM 액세스 확장에 대한 자세한 내용은 [Windows VM에서 원격 데스크톱 서비스 다시 설정](/troubleshoot/azure/virtual-machines/reset-rdp)을 참조하세요. 일단 실행하면 첫 번째 로그인 시 암호를 다시 설정해야 합니다.

```powershell
$cred=Get-Credential

Set-AzVMAccessExtension -ResourceGroupName "myResourceGroup" -VMName "myVM" -Name "myVMAccess" `
    -Location WestUS -UserName $cred.GetNetworkCredential().Username `
    -Password $cred.GetNetworkCredential().Password -typeHandlerVersion "2.0"
```

`Set-AzVMExtension` 명령을 사용하여 VM 확장을 시작할 수 있습니다. 자세한 내용은 [Set-AzVMExtension 참조](/powershell/module/az.compute/set-azvmextension)를 참조하세요.


### <a name="azure-portal"></a>Azure portal

Azure Portal을 통해 기존 VM에 VM 확장을 적용할 수 있습니다. 포털에서 VM을 선택하고, **확장** 을 선택한 다음, **추가** 를 선택합니다. 사용 가능한 확장 목록에서 원하는 확장을 선택하고 마법사의 지시를 따릅니다.

다음 예제에서는 Azure Portal에서 Microsoft 맬웨어 방지 확장을 설치하는 경우를 보여줍니다.

![Install antimalware extension](./media/features-windows/installantimalwareextension.png)

### <a name="azure-resource-manager-templates"></a>Azure 리소스 관리자 템플릿

Azure Resource Manager 템플릿에 VM 확장을 추가하고 템플릿 배포를 통해 실행할 수 있습니다. 템플릿을 사용하여 확장을 배포할 때 완전히 구성된 Azure 배포를 만들 수 있습니다. 예를 들어 다음 JSON은 부하 분산된 VM 집합 및 Azure SQL 데이터베이스를 배포한 후 각 VM에 .NET Core 애플리케이션을 설치하는 Resource Manager 템플릿에서 가져옵니다. VM 확장은 소프트웨어 설치를 관리합니다.

자세한 내용은 전체 [Resource Manager 템플릿](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows)을 참조하세요.

```json
{
    "apiVersion": "2015-06-15",
    "type": "extensions",
    "name": "config-app",
    "location": "[resourceGroup().location]",
    "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'),copyindex())]",
    "[variables('musicstoresqlName')]"
    ],
    "tags": {
    "displayName": "config-app"
    },
    "properties": {
    "publisher": "Microsoft.Compute",
    "type": "CustomScriptExtension",
    "typeHandlerVersion": "1.9",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1"
        ]
    },
    "protectedSettings": {
        "commandToExecute": "[concat('powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 -user ',parameters('adminUsername'),' -password ',parameters('adminPassword'),' -sqlserver ',variables('musicstoresqlName'),'.database.windows.net')]"
    }
    }
}
```

Resource Manager 템플릿을 만드는 방법에 대한 자세한 내용은 [Windows VM 확장을 사용하여 Azure Resource Manager 템플릿 작성](../windows/template-description.md#extensions)을 참조하세요.

## <a name="secure-vm-extension-data"></a>VM 확장 데이터 보호

VM 확장을 실행하는 경우 자격 증명, 스토리지 계정 이름 및 스토리지 계정 액세스 키와 같은 중요한 정보를 포함해야 할 수도 있습니다. 많은 VM 확장에는 데이터를 암호화하고 대상 VM 내에서만 해독하는 보호된 구성이 포함되어 있습니다. 각 확장에는 보호되는 특정 구성 스키마가 있으며 각각은 확장 관련 설명서에 자세히 나와 있습니다.

다음 예제에서는 Windows용 사용자 지정 스크립트 확장의 인스턴스를 보여 줍니다. 실행할 명령에는 자격 증명 집합이 포함됩니다. 이 예제에서는 실행할 명령이 암호화되지 않습니다.

```json
{
    "apiVersion": "2015-06-15",
    "type": "extensions",
    "name": "config-app",
    "location": "[resourceGroup().location]",
    "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'),copyindex())]",
    "[variables('musicstoresqlName')]"
    ],
    "tags": {
    "displayName": "config-app"
    },
    "properties": {
    "publisher": "Microsoft.Compute",
    "type": "CustomScriptExtension",
    "typeHandlerVersion": "1.9",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1"
        ],
        "commandToExecute": "[concat('powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 -user ',parameters('adminUsername'),' -password ',parameters('adminPassword'),' -sqlserver ',variables('musicstoresqlName'),'.database.windows.net')]"
    }
    }
}
```

다음 예제에서 보여준 대로 **실행할 명령** 속성을 **보호됨** 구성으로 이동하면 실행 문자열이 보호됩니다.

```json
{
    "apiVersion": "2015-06-15",
    "type": "extensions",
    "name": "config-app",
    "location": "[resourceGroup().location]",
    "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'),copyindex())]",
    "[variables('musicstoresqlName')]"
    ],
    "tags": {
    "displayName": "config-app"
    },
    "properties": {
    "publisher": "Microsoft.Compute",
    "type": "CustomScriptExtension",
    "typeHandlerVersion": "1.9",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1"
        ]
    },
    "protectedSettings": {
        "commandToExecute": "[concat('powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 -user ',parameters('adminUsername'),' -password ',parameters('adminPassword'),' -sqlserver ',variables('musicstoresqlName'),'.database.windows.net')]"
    }
    }
}
```

확장을 사용하는 Azure IaaS VM의 인증서 콘솔에는 **_Windows Azure CRP 인증서 생성기_** 주체가 있는 인증서가 표시될 수 있습니다. 클래식 RDFE VM의 이러한 인증서에는 **_Windows Azure Service Management for Extensions_** 주체 이름이 있습니다.

이러한 인증서는 확장에서 사용하는 보호된 설정(암호, 기타 자격 증명)을 전송하는 동안 VM과 해당 호스트 간의 통신을 보호합니다. 인증서는 Azure 패브릭 컨트롤러에 의해 빌드되고 VM 에이전트로 전달됩니다. 매일 VM을 중지하고 시작하는 경우 패브릭 컨트롤러에서 새 인증서를 만들 수 있습니다. 인증서는 컴퓨터의 개인 인증서 저장소에 저장됩니다. 이러한 인증서는 삭제할 수 있습니다. 필요한 경우 VM 에이전트가 인증서를 다시 만듭니다.

### <a name="how-do-agents-and-extensions-get-updated"></a>에이전트 및 확장을 업데이트하는 방법

에이전트 및 확장은 동일한 업데이트 메커니즘을 공유합니다. 일부 업데이트는 추가 방화벽 규칙을 필요로 하지 않습니다.

업데이트를 사용할 수 있는 경우 확장에 대한 변경 내용이 있는 경우에만 VM에 설치되고 다른 VM 모델이 변경됩니다.

- 데이터 디스크
- 확장
- 부팅 진단 컨테이너
- 게스트 OS 암호
- VM 크기
- 네트워크 프로필

게시자는 다른 시간에 지역에서 업데이트를 사용할 수 있도록 하므로 다른 지역에는 다른 버전의 VM이 있을 수 있습니다.

#### <a name="listing-extensions-deployed-to-a-vm"></a>VM에 배포된 확장 나열

```powershell
$vm = Get-AzVM -ResourceGroupName "myResourceGroup" -VMName "myVM"
$vm.Extensions | select Publisher, VirtualMachineExtensionType, TypeHandlerVersion
```

```powershell
Publisher             VirtualMachineExtensionType          TypeHandlerVersion
---------             ---------------------------          ------------------
Microsoft.Compute     CustomScriptExtension                1.9
```

#### <a name="agent-updates"></a>에이전트 업데이트

Windows 게스트 에이전트에는 *확장 처리 코드* 만 포함됩니다. *Windows 프로비전 코드* 는 별도입니다. Windows 게스트 에이전트를 제거할 수 있습니다. Windows 게스트 에이전트의 자동 업데이트를 비활성화할 수 없습니다.

*확장 처리 코드* 는 Azure 패브릭과 통신하고 설치, 상태 보고, 개별 확장 업데이트 및 제거 등 VM 확장 작업을 처리하는 역할을 담당합니다. 업데이트에는 보안 수정, 버그 수정 및 *확장 처리 코드* 에 대한 향상된 기능이 포함됩니다.

실행 중인 버전을 확인하려면 [설치된 Windows 게스트 에이전트 검색](agent-windows.md#detect-the-vm-agent)을 참조하세요.

#### <a name="extension-updates"></a>확장 업데이트

확장 업데이트를 사용할 수 있는 경우 Windows 게스트 에이전트는 확장을 다운로드하고 업그레이드합니다. 자동 확장 업데이트는 *부 버전* 또는 *핫픽스* 중 하나입니다. 확장을 프로비전할 때 *부 버전* 업데이트를 옵트인하거나 옵트아웃할 수 있습니다. 다음 예제에서는 *autoUpgradeMinorVersion": true,'* 를 사용하여 Resource Manager 템플릿에서 부 버전을 자동으로 업그레이드하는 방법을 보여줍니다.

```json
    "properties": {
    "publisher": "Microsoft.Compute",
    "type": "CustomScriptExtension",
    "typeHandlerVersion": "1.9",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1"
        ]
    },
```

최신 부 릴리스 버그를 수정하려면 확장 배포에서 자동 업데이트를 선택하는 것이 좋습니다. 보안 또는 주요 버그 수정을 제공하는 핫픽스 업데이트는 옵트아웃될 수 없습니다.

### <a name="how-to-identify-extension-updates"></a>확장 업데이트를 식별하는 방법

#### <a name="identifying-if-the-extension-is-set-with-autoupgrademinorversion-on-a-vm"></a>확장이 VM에서 autoUpgradeMinorVersion로 설정되었는지를 식별합니다.

확장이 ‘autoUpgradeMinorVersion’을 사용하여 프로비전된 경우 VM 모델에서 볼 수 있습니다. 확인하려면 [Get-AzVm](/powershell/module/az.compute/get-azvm)을 사용하고 다음과 같이 리소스 그룹 및 VM 이름을 제공합니다.

```powerShell
 $vm = Get-AzVm -ResourceGroupName "myResourceGroup" -VMName "myVM"
 $vm.Extensions
```

다음 예제 출력에서는 *autoUpgradeMinorVersion* 이 *true* 로 설정되었다고 보여줍니다.

```powershell
ForceUpdateTag              :
Publisher                   : Microsoft.Compute
VirtualMachineExtensionType : CustomScriptExtension
TypeHandlerVersion          : 1.9
AutoUpgradeMinorVersion     : True
```

#### <a name="identifying-when-an-autoupgrademinorversion-occurred"></a>autoUpgradeMinorVersion이 발생한 경우 식별

확장에 대한 업데이트가 발생한 경우 확인하려면 *C:\WindowsAzure\Logs\WaAppAgent.log* 에서 VM의 에이전트 로그를 검토합니다.

다음 예제에서 VM에는 *Microsoft.Compute.CustomScriptExtension 1.8* 이 설치되었습니다. 핫픽스는 *1.9* 버전에 지원되었습니다.

```powershell
[INFO]  Getting plugin locations for plugin 'Microsoft.Compute.CustomScriptExtension'. Current Version: '1.8', Requested Version: '1.9'
[INFO]  Auto-Upgrade mode. Highest public version for plugin 'Microsoft.Compute.CustomScriptExtension' with requested version: '1.9', is: '1.9'
```

## <a name="agent-permissions"></a>에이전트 사용 권한

해당 작업을 수행하려면 에이전트는 *로컬 시스템* 권한으로 실행해야합니다.

## <a name="troubleshoot-vm-extensions"></a>VM 확장 문제 해결

각 VM 확장에는 확장 관련 문제 해결 단계가 있을 수 있습니다. 예를 들어 사용자 지정 스크립트 확장을 사용하는 경우 확장이 실행된 VM에서 로컬로 스크립트 실행 세부 정보를 확인할 수 있습니다. 확장별 문제 해결 단계는 확장 관련 설명서에 자세히 나와 있습니다.

다음 문제 해결 단계는 모든 VM 확장에 적용됩니다.

1. Windows 게스트 에이전트 로그를 확인하려면 *C:\WindowsAzure\Logs\WaAppAgent.log* 에서 확장이 프로비저닝된 경우 작업을 확인합니다.

2. 의 자세한 내용은 실제 확장 로그를 확인하세요. `C:\WindowsAzure\Logs\Plugins\<extensionName>`

3. 오류 코드, 알려진 문제 등에 대한 확장 특정 설명서 문제 해결 섹션을 확인합니다.

4. 시스템 로그를 확인합니다. 단독 패키지 관리자 액세스가 필요한 다른 애플리케이션의 장기 실행 설치 등 확장을 방해할 수 있는 다른 작업을 확인합니다.

### <a name="common-reasons-for-extension-failures"></a>확장 오류에 대한 일반적인 이유

1. 확장은 실행하는 데 20분이 걸립니다(예외는 90분이 걸리는 CustomScript 확장, Chef 및 DSC임). 배포가 이 시간을 초과하는 경우 시간 제한으로 표시됩니다. 확장이 프로비전하려는 동안 낮은 리소스 VM, 리소스를 많이 사용하는 다른 VM 구성/시작 작업으로 인해 발생할 수 있습니다.

2. 최소 필수 조건이 충족되지 않습니다. 일부 확장은 HPC 이미지와 같은 VM SKU에 대해 종속됩니다. 확장에는 Azure Storage 또는 공용 서비스와 통신하는 등 특정 네트워킹 액세스 요구 사항이 필요할 수 있습니다. 다른 예제는 패키지 리포지토리에 대한 액세스, 디스크 공간 부족 또는 보안 제한과 관련될 수 있습니다.

3. 단독 패키지 관리자 액세스입니다. 일부 경우에 장기 실행 VM 구성 및 확장 설치 충돌이 발생할 수 있습니다. 이 경우에 패키지 관리자에 대한 단독 액세스가 필요합니다.

### <a name="view-extension-status"></a>확장 상태 보기

VM 확장이 VM에 대해 실행된 후에 [Get-AzVM](/powershell/module/az.compute/get-azvm)을 사용하여 확장 상태를 반환합니다. *하위 상태[0]* 에서는 확장 프로비전이 성공했음을 보여줍니다. 즉, VM에 배포에 성공했지만 VM 내에서 확장의 실행에 실패했습니다. *하위 상태[1]*

```powershell
Get-AzVM -ResourceGroupName "myResourceGroup" -VMName "myVM" -Status
```

다음 예제 출력과 유사하게 출력됩니다.

```powershell
Extensions[0]           :
  Name                  : CustomScriptExtension
  Type                  : Microsoft.Compute.CustomScriptExtension
  TypeHandlerVersion    : 1.9
  Substatuses[0]        :
    Code                : ComponentStatus/StdOut/succeeded
    Level               : Info
    DisplayStatus       : Provisioning succeeded
    Message             : Windows PowerShell \nCopyright (C) Microsoft Corporation. All rights reserved.\n
  Substatuses[1]        :
    Code                : ComponentStatus/StdErr/succeeded
    Level               : Info
    DisplayStatus       : Provisioning succeeded
    Message             : The argument 'cseTest%20Scriptparam1.ps1' to the -File parameter does not exist. Provide the path to an existing '.ps1' file as an argument to the

-File parameter.
  Statuses[0]           :
    Code                : ProvisioningState/failed/-196608
    Level               : Error
    DisplayStatus       : Provisioning failed
    Message             : Finished executing command
```

Azure Portal에서 확장 실행 상태를 찾을 수도 있습니다. 확장의 상태를 확인하려면 VM을 선택하고, **확장** 을 선택한 다음, 원하는 확장을 선택합니다.

### <a name="rerun-vm-extensions"></a>VM 확장 다시 실행

VM 확장을 다시 실행해야 하는 경우가 있을 수 있습니다. 확장을 다시 실행하려면 확장을 제거한 다음 원하는 실행 방법으로 확장을 다시 실행하면 됩니다. 확장을 제거하려면 다음과 같이 [Remove-AzVMExtension](/powershell/module/az.compute/remove-azvmextension)을 사용합니다.

```powershell
Remove-AzVMExtension -ResourceGroupName "myResourceGroup" -VMName "myVM" -Name "myExtensionName"
```

다음과 같이 Azure Portal에서 확장을 제거할 수도 있습니다.

1. VM을 선택합니다.
2. **확장** 을 선택합니다.
3. 원하는 확장을 선택합니다.
4. **제거** 를 선택합니다.

## <a name="common-vm-extensions-reference"></a>일반 VM 확장 참조
| 확장 이름 | Description | 자세한 정보 |
| --- | --- | --- |
| Windows용 사용자 지정 스크립트 확장 |Azure Virtual Machine에 대해 스크립트 실행 |[Windows용 사용자 지정 스크립트 확장](custom-script-windows.md) |
| Windows용 DSC 확장 |PowerShell DSC(Desired State Configuration) 확장 |[Windows용 DSC 확장](dsc-overview.md) |
| Azure Diagnostics 확장 |Azure Diagnostics 관리 |[Azure Diagnostics 확장](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/) |
| Azure VM 액세스 확장 |사용자 및 자격 증명 관리 |[Linux용 VM 액세스 확장](https://azure.microsoft.com/blog/using-vmaccess-extension-to-reset-login-credentials-for-linux-vm/) |

## <a name="next-steps"></a>다음 단계

VM 확장에 대한 자세한 내용은 [Azure 가상 머신 확장 및 기능 개요](overview.md)를 참조하세요.
