---
title: Windows PowerShell DSC를 사용하여 Connected Machine 에이전트 설치
description: 이 문서에서는 Windows PowerShell DSC로 Azure Arc 사용 서버를 사용하여 컴퓨터를 Azure에 연결하는 방법을 알아봅니다.
ms.date: 08/17/2021
ms.topic: conceptual
ms.openlocfilehash: 0a6e955df43e3589c97091cb111699ce402723d0
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122538738"
---
# <a name="how-to-install-the-connected-machine-agent-using-windows-powershell-dsc"></a>Windows PowerShell DSC를 사용하여 Connected Machine 에이전트 설치 방법

[Windows PowerShell DSC(Desired State Configuration)](/powershell/scripting/dsc/getting-started/winGettingStarted)를 사용하여 Windows 컴퓨터의 소프트웨어 설치 및 구성을 자동화할 수 있습니다. 이 문서에서는 DSC를 사용하여 하이브리드 Windows 컴퓨터에 Azure Arc 사용 서버 Connected Machine 에이전트를 설치하는 방법을 설명합니다.

## <a name="requirements"></a>요구 사항

- Windows PowerShell 버전 4.0 이상

- [AzureConnectedMachineDsc](https://www.powershellgallery.com/packages/AzureConnectedMachineDsc) DSC 모듈

- 컴퓨터를 비대화형으로 Azure Arc 사용 서버에 연결할 서비스 주체. Arc 사용 서버의 서비스 주체를 아직 만들지 않은 경우 [규모에 맞는 온보딩을 위해 서비스 주체 만들기](onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale) 섹션의 단계를 수행합니다.

## <a name="install-the-connectedmachine-dsc-module"></a>ConnectedMachine DSC 모듈 설치

1. 모듈을 수동으로 설치하려면 소스 코드를 다운로드하고 프로젝트 디렉터리의 콘텐츠를 `$env:ProgramFiles\WindowsPowerShell\Modules folder`에 압축 해제합니다. 또는 다음 명령을 실행하여 PowerShellGet(PowerShell 5.0에 있음)을 사용하여 PowerShell 갤러리에서 설치합니다.

    ```powershell
    Find-Module -Name AzureConnectedMachineDsc -Repository PSGallery | Install-Module
    ```

2. 설치를 확인하려면 다음 명령을 실행하고 Azure Connected Machine DSC 리소스가 사용 가능한지 확인합니다.

    ```powershell
    Get-DscResource -Module AzureConnectedMachineDsc
    ```

   다음 출력과 비슷하게 표시됩니다.

   ![Connected Machine DSC 모듈 설치 확인 예제](./media/onboard-dsc/confirm-module-installation.png)

## <a name="install-the-agent-and-connect-to-azure"></a>에이전트를 설치하고 Azure에 연결

이 모듈의 리소스는 Azure Connected Machine 에이전트 구성을 관리하도록 설계되었습니다. PowerShell 스크립트 `AzureConnectedMachineAgent.ps1`도 포함되어 있습니다(`AzureConnectedMachineDsc\examples` 폴더에 있음). 이 스크립트는 커뮤니티 리소스를 사용하여 다운로드 및 설치를 자동화하고, Azure Arc와의 연결을 설정합니다. 이 스크립트는 [Azure Portal에서 Azure에 하이브리드 컴퓨터 연결](onboard-portal.md) 문서에 설명된 것과 유사한 단계를 수행합니다.

컴퓨터가 프록시 서버를 통해 서비스와 통신해야 하는 경우 에이전트를 설치한 후 [여기](manage-agent.md#update-or-remove-proxy-settings)에서 설명하는 명령을 실행해야 합니다. 그러면 `https_proxy` 프록시 서버 시스템 환경 변수가 설정됩니다. 명령을 수동으로 실행하는 대신 [ComputeManagementDsc](https://www.powershellgallery.com/packages/ComputerManagementDsc) 모듈을 사용하여 DSC로 이 단계를 수행할 수 있습니다. 이 구성을 사용하여 에이전트는 HTTP 프로토콜을 사용하는 프록시 서버를 통해 통신합니다.

>[!NOTE]
>DSC를 실행할 수 있도록 하려면 localhost 구성을 실행하는 경우에도 PowerShell 원격 명령을 수신하도록 Windows를 구성해야 합니다. 환경을 올바르게 구성하려면 관리자 권한 PowerShell 터미널에서 `Set-WsManQuickConfig -Force`를 실행하면 됩니다.
>

`Start-DscConfiguration` cmdlet을 사용하여 구성 문서(MOF 파일)를 컴퓨터에 적용할 수 있습니다.

다음은 사용할 PowerShell 스크립트에 전달할 매개 변수입니다.

- `TenantId`: Azure AD의 전용 인스턴스를 나타내는 고유 식별자(GUID)입니다.

- `SubscriptionId`: 컴퓨터가 속할 Azure 구독의 구독 ID(GUID)입니다.

- `ResourceGroup`: 연결된 컴퓨터가 속할 리소스 그룹 이름입니다.

- `Location`: [지원되는 Azure 지역](overview.md#supported-regions)을 참조하세요. 이 위치는 리소스 그룹의 위치와 같을 수도 있고 다를 수도 있습니다.

- `Tags`: 연결된 컴퓨터 리소스에 적용해야 하는 태그의 문자열 배열입니다.

- `Credential`: [서비스 주체](onboard-service-principal.md)를 사용하여 컴퓨터를 규모에 맞게 등록하는 데 사용되는 **ApplicationId** 및 **암호** 가 포함된 PowerShell 자격 증명 개체입니다.

1. PowerShell 콘솔에서 `.ps1` 파일을 저장한 폴더로 이동합니다.

2. 다음 PowerShell 명령을 실행하여 MOF 문서를 컴파일합니다(DSC 구성 컴파일에 대한 자세한 내용은 [DSC 구성](/powershell/scripting/dsc/configurations/configurations) 참조).

    ```powershell
    .\`AzureConnectedMachineAgent.ps1 -TenantId <TenantId GUID> -SubscriptionId <SubscriptionId GUID> -ResourceGroup '<ResourceGroupName>' -Location '<LocationName>' -Tags '<Tag>' -Credential <psCredential>
    ```

3. 그러면 `C:\dsc`라는 새 폴더에 `localhost.mof file`이 만들어집니다.

에이전트가 설치되고 Azure Arc 사용 서버에 연결하도록 구성되면 Azure Portal로 이동하여 서버가 성공적으로 연결되었는지 확인합니다. [Azure Portal](https://aka.ms/hybridmachineportal)에서 해당 머신을 확인합니다.

## <a name="adding-to-existing-configurations"></a>기존 구성에 추가

이 리소스는 기존 DSC 구성에 추가하여 컴퓨터의 엔드투엔드 구성을 나타낼 수 있습니다. 예를 들어 보안 운영 체제 설정을 설정하는 구성에 이 리소스를 추가할 수 있습니다.

PowerShell 갤러리의 [CompositeResource](https://www.powershellgallery.com/packages/compositeresource) 모듈을 사용하여 예제 구성의 [복합 리소스](/powershell/scripting/dsc/resources/authoringResourceComposite)를 만들면 구성 조합을 더욱 간소화할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* 문제 해결 정보는 [Connected Machine 에이전트 문제 해결 가이드](troubleshoot-agent-onboard.md)에서 찾을 수 있습니다.

* [계획 및 배포 가이드](plan-at-scale-deployment.md)를 검토하여 모든 규모의 Azure Arc 지원 서버 배포를 계획하고 중앙 집중식 관리와 모니터링을 구현합니다.

* [Azure Policy](../../governance/policy/overview.md)를 사용하여 머신을 관리하는 방법을 알아봅니다(예: VM [게스트 구성](../../governance/policy/concepts/guest-configuration.md), 머신이 예상되는 Log Analytics 작업 영역에 보고되는지 확인, [VM 인사이트](../../azure-monitor/vm/vminsights-enable-policy.md)로 모니터링 등).