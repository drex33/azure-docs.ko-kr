---
title: Chocolatey를 사용한 Azure Automation 지속적인 배포 설정
description: 이 문서에서는 State Configuration 및 Chocolatey 패키지 관리자를 사용하여 지속적인 배포를 설정하는 방법을 설명합니다.
services: automation
ms.subservice: dsc
ms.date: 08/08/2018
ms.topic: conceptual
ms.custom: references_regions, devx-track-azurepowershell
ms.openlocfilehash: e602ed172aa5bb222b2480fb89896e8877e3d713
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132494309"
---
# <a name="set-up-continuous-deployment-with-chocolatey"></a>Chocolatey를 사용한 지속적인 배포 설정

DevOps 업계에는 연속 통합 파이프라인의 여러 시점에서 개발자를 지원하는 여러 도구가 있습니다. Azure Automation [State Configuration](automation-dsc-overview.md)이 DevOps 팀이 채택할 수 있는 새로운 옵션으로 추가되었습니다. 

Azure Automation은 사용자가 자격 증명, 일정 및 글로벌 변수와 같은 Runbook, 노드 및 공유 리소스를 사용하여 다양한 작업을 자동화할 수 있도록 하는 Microsoft Azure의 관리형 서비스입니다. Azure Automation State Configuration은 PowerShell DSC(Desired State Configuration) 도구를 포함하도록 자동화 기능을 확장합니다. 여기서 알맞은 [개요](automation-dsc-overview.md)를 제공합니다.

이 문서에서는 Windows 컴퓨터에 대해 CD(지속적인 배포)를 설정하는 방법을 보여 줍니다. 이 기술을 간편하게 확장하여 역할(예: 웹 사이트)에서 필요한 만큼의 Windows 컴퓨터를 포함하고 해당 위치에서 추가적인 역할로 이동할 수 있습니다.

![IaaS VM에 대한 연속 배포](./media/automation-dsc-cd-chocolatey/cdforiaasvm.png)

## <a name="at-a-high-level"></a>높은 수준

이 단계는 상당 부분 진행 중이지만 다행스럽게도 두 가지 기본 프로세스로 나눌 수 있습니다.

- 즉 코드를 작성하여 테스트한 다음 시스템의 주 버전과 부 버전에 대해 설치 패키지를 만들고 게시하는 것입니다.
- 패키지에서 코드를 설치 및 실행할 VM을 만들고 관리합니다.  

이 핵심 프로세스가 둘 다 배치되어 있으면 새 버전을 만들어 배포할 때 VM에서 패키지를 자동으로 업데이트하기 쉽습니다.

## <a name="component-overview"></a>구성 요소 개요

[apt-get](https://en.wikipedia.org/wiki/Advanced_Packaging_Tool) 같은 패키지 관리자는 Linux 분야에서 잘 알려져 있지만 Windows 분야에서는 그렇지 않습니다.
[Chocolatey](https://chocolatey.org/)도 마찬가지입니다. Scott Hanselman의 [블로그](https://www.hanselman.com/blog/IsTheWindowsUserReadyForAptget.aspx)에서 이 항목에 대해 잘 소개하고 있습니다. 간단히 말해 Chocolatey를 사용하면 명령줄을 사용하여 중앙 리포지토리의 패키지를 Winodws 운영 체제에 설치할 수 있습니다. 자체 리포지토리를 만들어 관리할 수 있고 Chocolatey가 사용자가 지정한 수 만큼의 리포지토리에서 패키지를 설치할 수 있습니다.

[PowerShell DSC](/powershell/scripting/dsc/overview/overview)는 머신에 원하는 구성을 선언할 수 있는 PowerShell 도구입니다. 예를 들어 Chocolatey를 설치하고, IIS를 설치하고, 포트 80을 열고, 웹 사이트의 버전 1.0.0을 설치하려는 경우 DSC LCM(로컬 구성 관리자)은 해당 구성을 구현합니다. DSC 풀 서버가 머신에 대한 구성 리포지토리를 담고 있습니다. 각 컴퓨터의 LCM이 주기적인 검사를 통해 자신의 구성이 저장된 구성과 일치하는지 확인합니다. 상태를 보고하거나, 컴퓨터를 저장된 구성과 일치하는 상태로 되돌리는 작업을 수행할 수 있습니다. 풀 서버에서 저장된 구성을 편집하여 컴퓨터나 컴퓨터 집합이 변경된 구성과 일치하게 만들 수 있습니다.

DSC 리소스는 네트워크 관리, Active Directory 또는 SQL Server 등과 같은 특정 기능을 갖는 코드 모듈입니다. Chocolatey DSC 리소스는 NuGet 서버 액세스(서로 간), 패키지 다운로드, 패키지 설치 방법 등을 이해하고 있습니다. 여러 다른 DSC 리소스는 [PowerShell 갤러리](https://www.powershellgallery.com/packages?q=dsc+resources&prerelease=&sortOrder=package-title)에 있습니다. 이러한 모듈을 사용자의 구성에서 사용할 수 있도록 Azure Automation State Configuration 풀 서버에 설치합니다.

Resource Manager 템플릿은 네트워크, 서브넷, 네트워크 보안, 라우팅, 부하 분산 장치, NIC, VM 등의 인프라를 생성하는 선언적인 방법을 제공합니다. 다음은 Resource Manager 배포 모델(선언적)과 Azure 서비스 관리(ASM 또는 기본) 배포 모델(명령적)을 비교하는 [문서](../azure-resource-manager/management/deployment-models.md)입니다. 이 문서에는 컴퓨팅, 스토리지 및 네트워크와 같은 핵심 리소스 공급자에 대한 설명이 포함되어 있습니다.

Resource Manager 템플릿의 핵심 기능은 프로비저닝되었을 때 VM에 VM 확장을 설치하는 것입니다. VM 확장에는 사용자 지정 스크립트 실행, 바이러스 백신 소프트웨어 설치, DSC 구성 스크립트 실행 등과 같은 특정 기능이 있습니다. VM 확장에는 여러 다른 형식이 있습니다.

## <a name="quick-trip-around-the-diagram"></a>간략히 다이어그램 둘러보기

맨 위쪽부터 코드를 작성하고 빌드 및 테스트한 다음, 설치 패키지를 만듭니다. Chocolatey는 MSI, MSU, ZIP 등과 같은 다양한 형태의 설치 패키지를 처리할 수 있습니다. 또한 Chocolatey의 기본 기능이 부족할 경우 PowerShell을 완전히 활용하여 실제 설치를 수행할 수 있습니다. 패키지를 읽을 수 있는 특정 위치인 패키지 리포지토리에 넣습니다. 이 사용 예는 Auzre Blob Storage 계정의 공용 폴더를 사용하지만 어디에나 있을 수 있습니다. Chocolatey는 패키지 메타데이터의 관리를 위해 NuGet 서버 및 기타 제품과 기본적으로 연동됩니다. [이 문서](https://github.com/chocolatey/choco/wiki/How-To-Host-Feed) 에서는 이러한 옵션에 대해 설명합니다. 사용 예제는 NuGet을 사용합니다. Nuspec은 패키지에 관한 메타데이터입니다. Nuspec 정보는 NuPkg로 컴파일되고 NuGet 서버에 저장됩니다. 구성에서 이름으로 패키지를 요청하고 NuGet 서버를 참조할 경우 VM의 Chocolatey DSC 리소스가 패키지를 포착하여 설치합니다. 특정 버전의 패키지를 요청할 수도 있습니다.

그림의 왼쪽 하단에는 Azure Resource Manager 템플릿이 있습니다. 이 사용 예제에서는 VM 확장이 VM을 Azure Automation State Configuration 풀 서버에 노드로 등록합니다. 구성은 풀 서버에 두 번(일반 텍스트로 한 번, MOF 파일로 컴파일되어 한 번) 저장됩니다. Azure Portal에서 MOF는 단순 구성과 반대로 노드 구성을 나타냅니다. 노드와 관련한 아티팩트이므로 노드가 그 구성을 알고 있습니다. 아래의 세부 정보는 노드 구성을 노드에 할당하는 방법을 보여줍니다.

Nuspec를 생성, 컴파일 및 NuGet 서버에 저장하는 작업은 간단합니다. 이미 VM을 관리하고 있습니다. 

지속적인 배포의 다음 단계로 이동하려면 풀 서버를 한 번 설정하고, 노드를 풀 서버에 한 번 등록하고, 서버에서 초기 구성을 만들어 저장해야 합니다. 패키지가 업그레이드되어 리포지토리에 배포되면 필요에 따라 풀 서버에서 구성 및 노드 구성을 새로 고쳐야 합니다.

Resource Manager 템플릿으로 시작하지 않아도 괜찮습니다. VM을 풀 서버에 등록하는 데 도움이 되는 PowerShell 명령이 있습니다. 자세한 내용은 [Azure Automation State Configuration을 통해 관리를 위한 머신 온보드](automation-dsc-onboarding.md)를 참조하세요.

## <a name="about-the-usage-example"></a>사용 예제 정보

이 문서의 사용 예제는 Azure 갤러리의 일반 Windows Server 2012 R2 이미지에서 VM으로 시작합니다. 아무 저장된 이미지에서나 시작하고 DSC 구성을 통해 조정을 진행할 수 있습니다.
그러나 이미지에 반영되는 구성을 변경하는 작업은 DSC를 사용하여 구성을 동적으로 업데이트하는 작업보다 훨씬 어렵습니다.

이 방법을 VM에 사용하기 위해 Resource Manager 템플릿이나 VM 확장을 사용할 필요는 없습니다. 또한 VM이 CD 관리를 위해 Azure에 있을 필요도 없습니다. 오직 Chocolatey을 설치하고 풀 서버 위치를 알 수 있게 VM에 LCM을 구성하기만 하면 됩니다.

프로덕션 상태인 VM에서 패키지를 업데이트할 때는 업데이트가 설치되는 동안 VM을 로테이션에서 배제해야 합니다. 이를 수행하는 방법은 크게 다릅니다. 예를 들어, Azure 부하 분산 장치 뒤에 있는 VM에서는 사용자 지정 프로브를 추가할 수 있습니다. VM을 업데이트하는 동안 프로브 엔드포인트가 400을 반환하게 합니다. 이 변경을 위해 필요한 조정은 구성 안에 있을 수 있습니다. 업데이트가 완료되면 조정에서 200을 반환하도록 복귀되기 때문입니다.

이 사용 예의 전체 원본은 GitHub의 [이 Visual Studio 프로젝트](https://github.com/sebastus/ARM/tree/master/CDIaaSVM) 에 있습니다.

## <a name="step-1-set-up-the-pull-server-and-automation-account"></a>1단계: 풀 서버 및 Automation 계정 설정

인증된(`Connect-AzAccount`) PowerShell 명령줄에서(풀 서버를 설정하는 데 몇 분 정도 소요될 수 있음):

```azurepowershell-interactive
New-AzResourceGroup -Name MY-AUTOMATION-RG -Location MY-RG-LOCATION-IN-QUOTES
New-AzAutomationAccount -ResourceGroupName MY-AUTOMATION-RG -Location MY-RG-LOCATION-IN-QUOTES -Name MY-AUTOMATION-ACCOUNT
```

Automation 계정을 다음 지역(위치라고도 함) 중 하나에 넣을 수 있습니다. 미국 동부 2, 미국 중남부, US Gov 버지니아, 서유럽, 동남 아시아, 일본 동부, 인도 중부 및 오스트레일리아 남동부, 캐나다 중부, 북유럽 등의 지역(즉, 위치) 중 하나에 놓을 수 있습니다.

## <a name="step-2-make-vm-extension-tweaks-to-the-resource-manager-template"></a>2단계: Resource Manager 템플릿에 대한 VM 확장 조정

VM 등록에 대한 세부 정보(PowerShell DSC VM 확장 사용)는 이 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/blog/automating-vm-configuration-using-powershell-dsc-extension/)에서 제공합니다.
이 단계는 새 VM을 상태 구성 노드 목록의 풀 서버에 등록합니다. 이 등록의 일부는 노드에 적용할 노드 구성을 지정하고 있습니다. 이 노드 구성은 풀 서버에 있을 필요가 없으므로 4단계에서 이 작업을 처음으로 수행해도 됩니다. 하지만 여기 2단계에서 노드의 이름 및 구성의 이름을 결정해야 합니다. 이 사용 예에서 노드는 'isvbox'이고 구성은 'ISVBoxConfig'입니다. 따라서 (DeploymentTemplate.json 지정할) 노드 구성 이름은 'ISVBoxConfig.isvbox'입니다.

## <a name="step-3-add-required-dsc-resources-to-the-pull-server"></a>3단계: 필요한 DSC 리소스를 풀 서버에 추가

Azure Automation 계정에 DSC 리소스를 설치하기 위해 PowerShell 갤러리가 계측됩니다.
원하는 리소스로 이동하고 "Azure Automation에 배포" 단추를 클릭합니다.

![PowerShell 갤러리 예](./media/automation-dsc-cd-chocolatey/xNetworking.PNG)

Azure Portal에 최근에 추가된 또 다른 방법을 사용하면 새 모듈을 당겨오거나 기존 모듈을 업데이트할 수 있습니다. Automation 계정 리소스, 자산 타일 및 모듈 타일을 차례로 클릭합니다. 갤러리 찾아보기 아이콘을 사용하면 갤러리에서 모듈의 목록을 보고 세부 정보로 드릴다운하고 궁극적으로 Automation 계정으로 가져올 수 있습니다. 이는 모듈을 최신 상태로 유지할 수 있는 좋은 방법입니다. 그리고 가져오기 기능은 다른 모듈의 종속성을 확인하여 동기화에서 빠져 나가지 않도록 합니다.

또한 나중에 업그레이드하려는 경우가 아니면 리소스당 한 번만 사용되는 수동 방법이 있습니다. PowerShell 통합 모듈 제작에 대한 자세한 내용은 [Azure Automation에 대한 통합 모듈 제작](https://azure.microsoft.com/blog/authoring-integration-modules-for-azure-automation/)을 참조하세요.

>[!NOTE]
>Windows 컴퓨터용 PowerShell 통합 모듈의 폴더 구조는 Azure Automation에서의 예상 폴더 구조와 다소 차이가 있습니다. 

1. [Windows Management Framework, v5](https://aka.ms/wmf5latest)를 설치합니다(Windows 10에는 필요 없음).

2. 통합 모듈을 설치합니다.

    ```azurepowershell-interactive
    Install-Module -Name MODULE-NAME`    <—grabs the module from the PowerShell Gallery
    ```

3. **c:\Program Files\WindowsPowerShell\Modules\MODULE-NAME** 의 모듈 폴더를 임시 폴더로 복사합니다.

4. 주 폴더에서 샘플 및 설명서를 삭제합니다.

5. 주 폴더를 압축하고 ZIP 파일 이름을 폴더의 이름으로 지정합니다.

6. ZIP 파일을 Azure Storage 계정의 Blob 스토리지와 같은 연결할 수 있는 HTTP 위치에 배치합니다.

7. 다음 명령을 실행합니다.

    ```azurepowershell-interactive
    New-AzAutomationModule `
      -ResourceGroupName MY-AUTOMATION-RG -AutomationAccountName MY-AUTOMATION-ACCOUNT `
      -Name MODULE-NAME -ContentLinkUri 'https://STORAGE-URI/CONTAINERNAME/MODULE-NAME.zip'
    ```

포함된 예제는 cChoco 및xNetworking에 대해 이러한 단계를 구현합니다. 

## <a name="step-4-add-the-node-configuration-to-the-pull-server"></a>4단계: 노드 구성을 풀 서버에 추가

구성을 풀 서버로 처음 가져와 컴파일하는 데는 별다른 사항이 없습니다. 동일한 구성의 모든 가져오기 또는 컴파일은 동일하게 보입니다. 패키지를 업데이트하고 프로덕션으로 푸시해야 할 때마다 패키지의 새로운 버전을 포함하여 구성 파일이 올바른지 확인하고 해당 단계를 수행합니다. 다음은 구성 파일 **ISVBoxConfig.ps1** 입니다.

```powershell
Configuration ISVBoxConfig
{
    Import-DscResource -ModuleName cChoco
    Import-DscResource -ModuleName xNetworking

    Node 'isvbox' {

        cChocoInstaller installChoco
        {
            InstallDir = 'C:\choco'
        }

        WindowsFeature installIIS
        {
            Ensure = 'Present'
            Name   = 'Web-Server'
        }

        xFirewall WebFirewallRule
        {
            Direction    = 'Inbound'
            Name         = 'Web-Server-TCP-In'
            DisplayName  = 'Web Server (TCP-In)'
            Description  = 'IIS allow incoming web site traffic.'
            Enabled       = 'True'
            Action       = 'Allow'
            Protocol     = 'TCP'
            LocalPort    = '80'
            Ensure       = 'Present'
        }

        cChocoPackageInstaller trivialWeb
        {
            Name      = 'trivialweb'
            Version   = '1.0.0'
            Source    = 'MY-NUGET-V2-SERVER-ADDRESS'
            DependsOn = '[cChocoInstaller]installChoco','[WindowsFeature]installIIS'
        }
    }
}
```

다음은 **New-ConfigurationScript.ps1** 스크립트입니다(Az 모듈을 사용하도록 수정됨).

```powershell
Import-AzAutomationDscConfiguration `
    -ResourceGroupName MY-AUTOMATION-RG -AutomationAccountName MY-AUTOMATION-ACCOUNT `
    -SourcePath C:\temp\AzureAutomationDsc\ISVBoxConfig.ps1 `
    -Published -Force

$jobData = Start-AzAutomationDscCompilationJob `
    -ResourceGroupName MY-AUTOMATION-RG -AutomationAccountName MY-AUTOMATION-ACCOUNT `
    -ConfigurationName ISVBoxConfig

$compilationJobId = $jobData.Id

Get-AzAutomationDscCompilationJob `
    -ResourceGroupName MY-AUTOMATION-RG -AutomationAccountName MY-AUTOMATION-ACCOUNT `
    -Id $compilationJobId
```

이러한 단계에서는 풀 서버에 위치하며 이름이 **ISVBoxConfig.isvbox** 인 새 노드 구성이 생깁니다. 노드 구성 이름은 `configurationName.nodeName`으로 빌드됩니다.

## <a name="step-5-create-and-maintain-package-metadata"></a>5단계: 패키지 메타데이터 만들기 및 유지 관리

패키지 리포지토리에 넣는 각 패키지에 대해 이를 설명하는 Nuspec이 필요합니다. NuGet 서버에서 컴파일 및 저장해야 합니다. 이 프로세스는 [여기](https://docs.nuget.org/create/creating-and-publishing-a-package)에서 설명합니다. 

**MyGet.org** 를 NuGet 서버로 사용할 수 있습니다. 이 서비스를 구매할 수 있지만 무료 스타터 SKU입니다. [NuGet](https://www.nuget.org/)에서 프라이빗 패키지에 대한 자체 NuGet 서버 설치 관련 지침을 찾을 수 있습니다.

## <a name="step-6-tie-it-all-together"></a>6단계: 모두 함께 연결

버전이 QA를 전달하고 배포를 승인할 때마다 패키지를 만들고 nuspec 및 nupkg를 NuGet 서버에 업데이트하고 배포합니다. 구성(4단계)은 새 버전 번호와 일치하도록 업데이트되어야 합니다. 그런 다음, 풀 서버로 전송되고 컴파일되어야 합니다.

해당 지점부터 업데이트를 끌어오고 설치하는 작업은 해당 구성에 종속되는 VM의 역할입니다. 이러한 각각의 업데이트는 하나 또는 두 줄의 PowerShell로 간단합니다. Azure DevOps의 경우 일부는 빌드에서 서로 연결될 수 있는 빌드 작업에 캡슐화됩니다. 이 [문서](https://www.visualstudio.com/docs/alm-devops-feature-index#continuous-delivery)에 자세한 내용이 나와 있습니다. 이 [GitHub 리포지토리](https://github.com/Microsoft/vso-agent-tasks)는 사용 가능한 빌드 작업을 자세히 설명합니다.

## <a name="related-articles"></a>관련 문서
* [Azure Automation DSC 개요](automation-dsc-overview.md)
* [Azure Automation DSC를 통한 관리를 위한 컴퓨터 온보드](automation-dsc-onboarding.md)

## <a name="next-steps"></a>다음 단계

- 개요는 [Azure Automation State Configuration 개요](automation-dsc-overview.md)를 참조하세요.
- 기능 사용을 시작하려면 [Azure Automation State Configuration 시작하기](automation-dsc-getting-started.md)를 참조하세요.
- DSC 구성을 대상 노드에 할당할 수 있도록 DSC 구성을 컴파일하는 방법에 대해 알아보려면 [Azure Automation State Configuration에서 구성 컴파일](automation-dsc-compile.md)을 참조하세요.
- PowerShell cmdlet 참조는 [Az.Automation](/powershell/module/az.automation)을 참조하세요.
- 가격 책정 정보는 [Azure Automation State Configuration 가격 책정](https://azure.microsoft.com/pricing/details/automation/)을 참조하세요.
