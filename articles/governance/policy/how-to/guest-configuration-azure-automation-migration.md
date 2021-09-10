---
title: 게스트 구성 마이그레이션 계획에 대한 Azure Automation State Configuration
description: 이 문서에서는 Azure Automation의 DSC 버전 2에서 Azure Policy 버전 3으로 이동하는 데 관심이 있는 고객을 위한 프로세스 및 기술 지침을 제공합니다.
ms.date: 07/1/2021
ms.topic: how-to
ms.openlocfilehash: ae387e59abe5cbff335b43cdd78030f32f6e6f8c
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/24/2021
ms.locfileid: "122773169"
---
# <a name="azure-automation-state-configuration-to-guest-configuration-migration-planning"></a>게스트 구성 마이그레이션 계획에 대한 Azure Automation State Configuration

게스트 구성은 Azure Automation State Configuration(Azure Automation Desired State Configuration 또는 AADSC 라고도 함)에서 제공하는 기능의 최신 구현입니다.
가능하면 콘텐츠 및 머신을 새 서비스로 이동하는 계획을 세워야 합니다.
이 문서에서는 Azure Automation에서 게스트 구성으로의 마이그레이션 전략 개발에 대한 지침을 제공합니다.

게스트 구성 주소의 새 기능은 다음과 같은 고객의 요청 사항을 반영하였습니다.

- 구성에 대한 크기 제한 확대(100MB)
- 리소스 ID 및 상태를 포함하여 Azure Resource Graph를 통한 고급 보고
- 동일한 머신에 대한 여러 구성 관리
- 머신이 원하는 상태를 벗어날 때 수정 시기를 제어합니다.
- Linux 및 Windows 모두 PowerShell 기반 DSC 리소스를 사용합니다.

시작하기 전에 [Azure Policy 게스트 구성](../concepts/guest-configuration.md) 페이지에서 개념 개요 정보를 읽는 것이 좋습니다.

## <a name="understand-migration"></a>마이그레이션 이해

마이그레이션에 접근하는 가장 좋은 방법은 먼저 콘텐츠를 재배포한 후 머신을 마이그레이션하는 것입니다. 마이그레이션에 필요한 단계를 간략하게 설명합니다.

- Azure Automation에서 구성 내보내기
- 모듈 요구 사항을 검색하고 사용자 환경에서 로드
- 구성 컴파일
- 게스트 구성 패키지 게시
- 게스트 구성 패키지 테스트
- 하이브리드 머신을 Azure Arc에 온보딩
- Azure Automation State Configuration에서 서버 등록 취소
- 게스트 구성을 사용하여 서버에 구성 할당

게스트 구성은 PowerShell 버전 7과 함께 DSC 버전 3을 사용합니다. DSC 버전 3은 [Windows ](/powershell/scripting/dsc/getting-started/wingettingstarted) 및 [Linux](/powershell/scripting/dsc/getting-started/lnxgettingstarted)의 이전 DSC 버전과 함께 사용할 수 있습니다.
구현은 별개입니다. 그러나 충돌 검색은 없습니다.

게스트 구성에서는 서비스에 모듈 또는 구성을 게시하거나 서비스에서 컴파일할 필요가 없습니다. 대신 콘텐츠는 특별히 빌드된 도구로 개발 및 테스트되고 머신이 HTTPS를 통해 도달할 수 있는 모든 위치(대개 Azure Blob Storage)에 게시됩니다.

마이그레이션에 대한 올바른 계획은 일정 기간 두 서비스 모두에 머신을 포함하는 것이라 결정하면, 관리할 때 혼란스러울 수 있지만 기술적인 장애물은 없습니다. 두 서비스는 독립적입니다.

## <a name="export-content-from-azure-automation"></a>Azure Automation에서 콘텐츠 내보내기

먼저 Azure Automation State Configuration에서 콘텐츠를 검색하고, 게스트 구성을 위한 콘텐츠 패키지를 만들고 테스트하고 게시하는 개발 환경으로 내보냅니다.

### <a name="configurations"></a>구성

구성 스크립트만 Azure Automation에서 내보낼 수 있습니다. “노드 구성” 또는 컴파일된 MOF 파일은 내보낼 수 없습니다.
Automation 계정에 직접 MOF 파일을 게시했고, 더 이상 원래 파일에 액세스할 수 없는 경우에는 개인 프라이빗 스크립트에서 다시 컴파일하거나 원본을 찾을 수 없는 경우 구성을 다시 작성해야 합니다.

Azure Automation에서 구성 스크립트를 내보내려면 먼저 Automation 계정이 배포되는 리소스 그룹의 구성 및 이름이 포함된 Azure Automation 계정을 확인합니다.

“Az. Automation” PowerShell 모듈을 설치합니다.

```powershell
Install-Module Az.Automation
```

그런 다음, “Get-AzAutomationAccount” 명령을 사용하여 Automation 계정 및 해당 계정이 배포된 리소스 그룹을 확인합니다.
“ResourceGroupName” 및 “AutomationAccountName” 속성은 다음 단계에서 중요합니다.

```powershell
Get-AzAutomationAccount

SubscriptionId        : <your subscription id>
ResourceGroupName     : <your resource group name>
AutomationAccountName : <your automation account name>
Location              : centralus
State                 :
Plan                  :
CreationTime          : 6/30/2021 11:56:17 AM -05:00
LastModifiedTime      : 6/30/2021 11:56:17 AM -05:00
LastModifiedBy        :
Tags                  : {}
```

Automation 계정에서 구성을 검색합니다. 출력은 구성 당 하나의 항목을 포함합니다. 많은 경우에는 정보를 변수로 저장하여 쉽게 작업할 수 있도록 합니다.

```powershell
Get-AzAutomationDscConfiguration -ResourceGroupName <your resource group name> -AutomationAccountName <your automation account name>

ResourceGroupName     : <your resource group name>
AutomationAccountName : <your automation account name>
Location              : centralus
State                 : Published
Name                  : <your configuration name>
Tags                  : {}
CreationTime          : 6/30/2021 12:18:26 PM -05:00
LastModifiedTime      : 6/30/2021 12:18:26 PM -05:00
Description           :
Parameters            : {}
LogVerbose            : False
```

마지막으로 “Export-AzAutomationDscConfiguration” 명령을 사용하여 각 구성을 로컬 스크립트 파일로 내보냅니다. 결과 파일 이름에는 `\ConfigurationName.ps1` 패턴이 사용됩니다.

```powershell
Export-AzAutomationDscConfiguration -OutputFolder /<location on your machine> -ResourceGroupName <your resource group name> -AutomationAccountName <your automation account name> -name <your configuration name>

UnixMode   User             Group                 LastWriteTime           Size Name
--------   ----             -----                 -------------           ---- ----
                                               12/31/1600 18:09
```

#### <a name="export-configurations-using-the-powershell-pipeline"></a>PowerShell 파이프라인을 사용하여 구성 내보내기

계정 및 구성 수를 검색한 후에는 머신의 로컬 폴더로 모든 구성을 내보낼 수 있습니다.
이 프로세스를 자동화하려면 위의 각 명령에 대한 출력을 다음으로 파이프합니다.

이 예제에서는 5개의 구성을 내보냅니다. 출력 패턴은 성공만 표시합니다.

```powershell
Get-AzAutomationAccount | Get-AzAutomationDscConfiguration | Export-AzAutomationDSCConfiguration -OutputFolder /<location on your machine>

UnixMode   User             Group                 LastWriteTime           Size Name
--------   ----             -----                 -------------           ---- ----
                                               12/31/1600 18:09
                                               12/31/1600 18:09
                                               12/31/1600 18:09
                                               12/31/1600 18:09
                                               12/31/1600 18:09
```

#### <a name="consider-decomposing-complex-configuration-files"></a>복합 구성 파일 분해에 대한 고려

게스트 구성은 각 머신에 여러 구성을 관리할 수 있습니다.
Azure Automation State Configuration에 대해 작성된 많은 구성에서는 각 머신에 단일 구성을 관리하는 제한이 있었습니다. 게스트 구성에서 제공하는 확장된 기능을 활용하고자 큰 구성 파일은 여러 개의 작은 구성으로 나누어 각각 특정 시나리오를 처리하게 할 있습니다.

게스트 구성에는 구성이 정렬되는 순서를 제어하는 오케스트레이션이 없으므로 순서대로 수행해야 하는 경우 구성의 단계를 모두 단일 패키지에 모읍니다.

### <a name="modules"></a>모듈

Azure Automation에서 모듈을 내보내거나 모듈/버전이 있어야 하는 구성의 상관 관계를 자동으로 지정할 수 없습니다. 새 게스트 구성 패키지를 만들려면 로컬 환경에 모듈이 있어야 합니다. 마이그레이션에 필요한 모듈 목록을 만들려면 PowerShell을 사용하여 모듈의 이름 및 버전에 대한 Azure Automation을 쿼리합니다.

사용자 지정으로 작성되었으며 전용 프라이빗 개발 환경에만 존재하는 모듈을 사용하는 경우 Azure Automation에서 내보낼 수 없습니다.

사용자 지정 모듈이 구성에 필요하고 계정에 존재하지만 사용자 환경에서 찾을 수 없는 경우에는 구성을 컴파일할 수 없습니다. 즉, 구성을 마이그레이션할 수 없습니다.

#### <a name="list-modules-imported-in-azure-automation"></a>Azure Automation에서 가져온 모듈 나열

Automation 계정에 설치된 모든 모듈 목록을 검색하려면 `Get-AzAutomationModule` 명령을 사용합니다. “IsGlobal” 속성은 모듈이 항상 Azure Automation에 기본 제공되는지 아니면 계정에 게시되었는지를 알려줍니다.

예를 들어 계정에 게시된 모든 모듈 목록을 만들려 하는 경우입니다.

```powershell
Get-AzAutomationAccount | Get-AzAutomationModule | ? IsGlobal -eq $false
```

또한 PowerShell 갤러리를 사용하여 공개적으로 사용할 수 있는 모듈에 대한 세부 정보를 쉽게 찾을 수 있습니다. 예를 들어 새 Automation 계정에 기본 제공되며 DSC 리소스를 포함하는 모듈의 목록은 다음 예제에 의해 생성됩니다.

```powershell
Get-AzAutomationAccount | Get-AzAutomationModule | ? IsGlobal -eq $true | Find-Module -erroraction silentlycontinue | ? {'' -ne $_.Includes.DscResource} | Select Name, Version -Unique | format-table -AutoSize

Name                       Version
----                       -------
AuditPolicyDsc             1.4.0
ComputerManagementDsc      8.4.0
PSDscResources             2.12.0
SecurityPolicyDsc          2.10.0
xDSCDomainjoin             1.2.23
xPowerShellExecutionPolicy 3.1.0.0
xRemoteDesktopAdmin        1.1.0.0
```

#### <a name="download-modules-from-powershell-gallery-or-powershellget-repository"></a>PowerShell 갤러리 또는 PowerShellGet 리포지토리에서 모듈 다운로드

PowerShell 갤러리에서 모듈을 가져온 경우에는 `Find-Module`에서 직접 출력을 `Install-Module`으로부터 파이프할 수 있습니다. 명령 간 출력 파이프는 현재 PowerShell 갤러리에서 공개적으로 사용할 수 있는 Automation 계정의 모듈이 모두 포함된 개발자 환경을 로드하기 위한 솔루션을 제공합니다.

피드가 로컬 환경에서 [PowerShellGet 리포지토리](/powershell/scripting/gallery/how-to/working-with-local-psrepositories)로 등록된 경우 동일한 접근 방식을 사용하여 사용자 지정 NuGet 피드에서 모듈을 가져올 수 있습니다.

예제의 `Find-Module` 명령은 오류를 제거하지 않습니다. 즉, 갤러리에서 찾을 수 없는 모듈은 오류 메시지를 반환합니다.

```powershell
Get-AzAutomationAccount | Get-AzAutomationModule | ? IsGlobal -eq $false | Find-Module | ? {'' -ne $_.Includes.DscResource} | Install-Module

  Installing package xWebAdministration'

    [                                                                                        ]
```

#### <a name="inspecting-configuration-scripts-for-module-requirements"></a>모듈 요구 사항에 대한 구성 스크립트 검사

Azure Automation에서 구성 스크립트를 내보낸 경우, 각 구성을 MOF 파일로 컴파일하는 데 필요한 모듈에 대한 상세 내용을 검토할 수도 있습니다. 이 방법은 모듈이 제거된 Automation 계정에서 구성을 찾는 경우에만 필요합니다.
이러한 구성은 더 이상 머신에는 유용하지 않지만, 아직도 계정에 있을 수 있습니다.

각 파일의 맨 위를 향해 이동하면서 ‘Import-DscResource’가 포함된 줄을 찾습니다.
이 명령은 구성 내에서만 적용되며 컴파일 시 모듈을 로드하는 데 사용됩니다.

예를 들어 PowerShell 갤러리의 “WindowsIISServerConfig” 구성에는 이 예제의 줄이 포함되어 있습니다.

```powershell
configuration WindowsIISServerConfig
{

Import-DscResource -ModuleName @{ModuleName = 'xWebAdministration';ModuleVersion = '1.19.0.0'}
Import-DscResource -ModuleName 'PSDesiredStateConfiguration'
```

구성하려면 “xWebAdministration” 모듈 버전 “1.19.0.0” 및 “PSDesiredStateConfiguration” 모듈이 필요합니다.

### <a name="test-content-in-azure-guest-configuration"></a>Azure 게스트 구성에서 콘텐츠 테스트

게스트 구성으로 Azure Automation State Configuration의 콘텐츠를 사용할 수 있는지를 평가하는 가장 좋은 방법은 [사용자 지정 게스트 구성 패키지 아티팩트를 만드는 방법](./guest-configuration-create.md) 페이지의 단계별 자습서를 따르는 것입니다.

[구성 작성](./guest-configuration-create.md#author-a-configuration) 단계가 되면 MOF 파일을 생성하는 구성 스크립트는 Azure Automation State Configuration에서 내보낸 스크립트 중 하나여야 합니다. MOF 파일에 대한 구성을 컴파일하고 게스트 구성 패키지를 만들기 전에 사용자 환경에 필수 PowerShell 모듈이 설치되어 있어야 합니다.

#### <a name="what-if-a-module-does-not-work-with-guest-configuration"></a>모듈이 게스트 구성에서 작동하지 않으면 어떻게 되나요?

일부 모듈에는 게스트 구성과의 호환성 문제가 발생할 수 있습니다. 가장 일반적인 문제는 .NET Framework와 .NET Core의 차이와 관련된 것입니다. 자세한 기술 정보는 [Windows PowerShell 5.1과 PowerShell(core) 7.x의 차이점](/powershell/scripting/whats-new/differences-from-windows-powershell) 페이지에서 확인할 수 있습니다.

호환성 문제를 해결하는 한 가지 옵션은 `powershell.exe`를 실행하여 PowerShell 7에서 가져온 모듈 내에서 Windows PowerShell 명령을 실행하는 것입니다.
[Windows DSC 구성](https://github.com/Azure/azure-policy/blob/bbfc60104c2c5b7fa6dd5b784b5d4713ddd55218/samples/GuestConfiguration/package-samples/resource-modules/WindowsDscConfiguration/DscResources/WindowsDscConfiguration/WindowsDscConfiguration.psm1#L97)의 상태를 감사하는 데 이 기술을 사용하는 Azure-Policy 리포지토리에서 해당 기술을 사용하는 샘플 모듈을 검토할 수 있습니다.

또한 이 예제에서는 작은 개념 증명을 보여 줍니다.

```powershell
# example function that could be loaded from module
function New-TaskResolvedInPWSH7 {
  # runs the fictitious command 'Get-myNotCompatibleCommand' in Windows PowerShell
  $compatObject = & powershell.exe -noprofile -NonInteractive -command { Get-myNotCompatibleCommand }
  # resulting object can be used in PowerShell 7
  return $compatObject
}
```

#### <a name="will-i-have-to-add-reasons-property-to-get-targetresource-in-all-modules-i-migrate"></a>마이그레이션하는 모든 모듈에서 Get-TargetResource에 “Reasons” 속성을 추가해야 하나요?

[“Reasons” 속성](../concepts/guest-configuration-custom.md#special-requirements-for-get)을 구현하면 Azure Portal에서 구성 할당 결과를 볼 때 개선된 환경을 제공합니다. 모듈의 `Get` 메서드에 “Reasons”가 포함되지 않은 경우 `Get` 메서드에서 반환한 속성의 세부 정보와 함께 제네릭 출력이 반환됩니다. 따라서 마이그레이션할 때 이는 선택 사항입니다.

## <a name="machines"></a>머신

게스트 구성에서 Azure Automation State Configuration의 테스트 콘텐츠를 완료한 후에는 머신을 마이그레이션하기 위한 계획을 세웁니다.

Azure의 가상 머신과 Azure 외부에 있는 하이브리드 머신 모두에 Azure Automation State Configuration을 사용할 수 있습니다. 여러 단계를 사용하여 이러한 각 시나리오에 대한 계획을 세워야 합니다.

### <a name="azure-vms"></a>Azure VM

Azure 가상 머신에는 이미 Azure에 이미 [리소스](../../../azure-resource-manager/management/overview.md#terminology)가 있습니다. 즉, 구성에 연결하는 게스트 구성 할당을 위한 준비가 완료된 것입니다. Azure 가상 머신을 마이그레이션하기 위한 고차원 수준의 작업은 Azure Automation State Configuration에서 제거한 다음 게스트 구성을 사용하여 구성을 할당하는 것입니다.

Azure Automation State Configuration에서 머신을 제거하려면 [Automation State Configuration에서 구성과 노드를 제거하는 방법](../../../automation/state-configuration/remove-node-and-configuration-package.md) 페이지의 단계를 따르세요.

게스트 구성을 사용하여 구성을 할당하려면 [Quickstart: Create a policy assignment to identify non-compliant resources](../assign-policy-portal.md)(빠른 시작: 비준수 리소스를 식별하는 정책 할당 만들기)와 같은 Azure Policy 빠른 시작의 단계를 수행합니다.
정책 정의를 선택하는 6단계에서는 Azure Automation State Configuration에서 마이그레이션한 구성을 적용하는 정의를 선택합니다.

### <a name="hybrid-machines"></a>하이브리드 머신

Azure 외부의 머신은 [Azure Automation State Configuration에 등록할 수](../../../automation/automation-dsc-onboarding.md#enable-physicalvirtual-linux-machines) 있지만 Azure에는 머신 리소스가 없습니다. Azure Automation에 대한 연결은 머신 내의 로컬 Configuration Manager 서비스에 의해 처리되고 노드 레코드는 Azure Automation 공급자 형식의 리소스로 관리됩니다.

Azure Automation State Configuration에서 머신을 제거하기 전에 각 노드를 [Azure Arc 사용 서버](../../../azure-arc/servers/overview.md)로 온보딩합니다.
Azure Arc에 온보딩하면 Azure에서 머신 리소스를 만들어 Azure Policy로 머신을 관리할 수 있습니다. 머신은 언제든지 Azure Arc로 온보딩될 수 있지만, Azure Automation State Configuration을 사용하여 해당 프로세스를 자동화할 수 있습니다.

PowerShell DSC를 사용하여 Azure Arc 사용 서버에 머신을 등록할 수 있습니다.
자세한 내용은 [Windows PowerShell DSC를 사용하여 Connected Machine 에이전트 설치 방법](../../../azure-arc/servers/onboard-dsc.md) 페이지를 참조하세요.
그러나 Azure Automation State Configuration은 Automation 계정마다 머신 당 하나의 구성만 관리할 수 있습니다. 즉, 게스트 구성을 위한 콘텐츠를 내보내고 테스트하고 준비하는 옵션이 있고, Azure Automation에서 노드 구성을 “전환”하여 Azure Arc에 온보딩하는 옵션이 있습니다. 마지막 단계로, Azure Automation State Configuration에서 노드 등록을 제거한 뒤 게스트 구성을 통해서만 머신 상태를 관리합니다.

## <a name="troubleshooting-issues-when-exporting-content"></a>콘텐츠를 내보낼 때의 문제 해결

알려진 문제에 대한 세부 정보가 제공됩니다.

### <a name="exporting-configurations-results-in--character-in-file-name"></a>구성 결과를 내보내면 파일 이름에 “\\” 문자가 생김

MacOS/Linux에서 PowerShell을 사용하는 경우 `Export-AzAutomationDSCConfiguration`에서 출력하는 파일 이름을 처리할 때 문제가 발생합니다.

이 문제를 해결하기 위해 [AADSCConfigContent](https://www.powershellgallery.com/packages/AADSCConfigContent/)라는 PowerShell 갤러리에 모듈이 게시되었습니다.
모듈에는 서비스에 대한 REST 요청을 수행하여 Azure Automation에 저장된 구성의 콘텐츠를 내보내는 명령만 있습니다.

## <a name="next-steps"></a>다음 단계

- 게스트 구성을 위한 [패키지 아티팩트를 만듭니다](./guest-configuration-create.md).
- 사용자 개발 환경에서 [패키지 아티팩트 테스트](./guest-configuration-create-test.md)합니다.
- 머신에서 액세스할 수 있도록 [패키지 아티팩트를 게시](./guest-configuration-create-publish.md)합니다.
- `GuestConfiguration` 모듈을 사용하여 사용자 환경의 대규모 관리를 위한 [Azure Policy 정의를 생성](./guest-configuration-create-definition.md)합니다.
- Azure Portal을 사용하여 [사용자 지정 정책 정의를 할당](../assign-policy-portal.md)합니다.
- [게스트 구성 정책 할당에 대한 규정 준수 세부 정보](./determine-non-compliance.md#compliance-details-for-guest-configuration)를 보는 방법을 알아봅니다.
