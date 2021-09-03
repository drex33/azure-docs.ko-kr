---
title: PowerShell을 사용하여 Azure에 하이브리드 머신 연결
description: 해당 문서에서는 Azure Arc 지원 서버를 사용하여 에이전트를 설치하고 머신을 Azure에 연결하는 방법을 설명합니다. 이 작업은 PowerShell을 사용하여 수행할 수 있습니다.
ms.date: 07/16/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e1ca9528af5b529dd844e566905b6aa7f92429d2
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122530730"
---
# <a name="connect-hybrid-machines-to-azure-by-using-powershell"></a>PowerShell을 사용하여 Azure에 하이브리드 머신 연결

Azure Arc에서 지원하는 서버의 경우 수동 단계를 수행하여 환경에서 하나 이상의 Windows 또는 Linux 머신에서 사용하도록 설정할 수 있습니다. 또는 PowerShell cmdlet [Connect-AzConnectedMachine](/powershell/module/az.connectedmachine/remove-azconnectedmachine)을 사용하여 Connected Machine 에이전트를 다운로드하고, 에이전트를 설치하여, Azure Arc에 머신을 등록할 수 있습니다. 해당 cmdlet은 Microsoft 다운로드 센터에서 Windows 에이전트 패키지(Windows Installer)를 다운로드하고 Microsoft 패키지 리포지토리에서 Linux 에이전트 패키지를 다운로드합니다.

이 방법을 사용하려면 머신에 대한 관리자 권한으로 에이전트를 설치하고 구성할 수 있어야 합니다. Linux에서는 루트 계정을 사용하여 수행하고, Windows에서는 로컬 관리자 그룹의 멤버로 수행해야 합니다. [PowerShell 원격 기능](/powershell/scripting/learn/ps101/08-powershell-remoting)을 사용하여 Windows 서버에서 대화형 또는 원격으로 해당 프로세스를 완료할 수 있습니다.

해당 프로세스를 시작하려면 먼저 [사전 요구 사항](agent-overview.md#prerequisites)을 검토하고 구독 및 리소스에서 요구 사항을 충족하는지 확인해야 합니다. 지원되는 지역 및 기타 관련 고려 사항에 대한 자세한 내용은 [Azure 지원 지역](overview.md#supported-regions)을 참조하세요.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>필수 구성 요소

- Azure PowerShell이 설치된 머신 자세한 내용은 [Azure PowerShell 설치 및 구성](/powershell/azure/)을 참조하세요.

PowerShell을 사용하여 Azure Arc 지원 서버에서 관리하는 하이브리드 서버에서 VM 확장을 관리합니다. PowerShell을 사용하기 전에 먼저 `Az.ConnectedMachine` 모듈을 설치합니다. Azure Arc에서 지원하는 서버에서 다음 명령을 실행합니다.

```powershell
Install-Module -Name Az.ConnectedMachine
```

설치가 완료되고 나면 다음 메시지가 표시됩니다.

`The installed extension ``Az.ConnectedMachine`` is experimental and not covered by customer support. Please use with discretion.`

## <a name="install-the-agent-and-connect-to-azure"></a>에이전트를 설치하고 Azure에 연결

1. 상승된 권한으로 PowerShell 콘솔을 엽니다.

2. 명령 `Connect-AzAccount`를 실행하여 Azure에 로그인합니다.

3. Connected Machine 에이전트를 설치하려면 `Connect-AzConnectedMachine`을 `-Name`, `-ResourceGroupName` 및 `-Location` 매개 변수와 함께 사용합니다. 로그인 후 만들어진 Azure 컨텍스트로 설정된 기본 구독을 재정의하려면 `-SubscriptionId` 매개 변수를 사용합니다. 다음 명령 중 하나를 실행합니다.

    * Azure와 직접 통신할 수 있는 대상 머신에 Connected Machine 에이전트를 설치하려면 다음을 실행합니다.

        ```azurepowershell
        Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Name myMachineName -Location <region>
        ```
    
    * 프록시 서버를 통해 통신하는 대상 머신에 Connected Machine 에이전트를 설치하려면 다음을 실행합니다.
        
        ```azurepowershell
        Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Name myMachineName -Location <region> -Proxy http://<proxyURL>:<proxyport>
        ```

      이 구성을 사용하여 에이전트는 HTTP 프로토콜을 사용하는 프록시 서버를 통해 통신합니다.

설치가 완료된 후 에이전트가 시작되지 않으면 자세한 오류 정보를 로그에서 확인합니다. Windows에서 다음 파일을 확인합니다. *%ProgramData%\AzureConnectedMachineAgent\Log\himds.log*. Linux에서 다음 파일을 확인합니다. */var/opt/azcmagent/log/himds.log*.

## <a name="install-and-connect-by-using-powershell-remoting"></a>PowerShell 원격 기능을 사용하여 설치 및 연결

Azure Arc에서 지원 서버를 사용하여 하나 이상의 Windows server를 구성하는 방법은 다음과 같습니다. 원격 머신에서 PowerShell 원격 기능을 사용하도록 설정해야 합니다. `Enable-PSRemoting` cmdlet을 사용하여 이를 수행하십시오.

1. 관리자 권한으로 PowerShell 콘솔을 엽니다.

2. 명령 `Connect-AzAccount`를 실행하여 Azure에 로그인합니다.

3. Connected Machine 에이전트를 설치하려면 `Connect-AzConnectedMachine`을 `-ResourceGroupName` 및 `-Location` 매개 변수와 함께 사용합니다. Azure 리소스 이름에는 각 서버의 호스트 이름이 자동으로 사용됩니다. 로그인 후 만들어진 Azure 컨텍스트로 설정된 기본 구독을 재정의하려면 `-SubscriptionId` 매개 변수를 사용합니다.

    * Azure와 직접 통신할 수 있는 대상 머신에 Connected Machine 에이전트를 설치하려면 다음 명령을 실행합니다.
    
        ```azurepowershell
        $sessions = New-PSSession -ComputerName myMachineName
        Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Location <region> -PSSession $sessions
        ```
    
    * 여러 원격 머신에 Connected Machine 에이전트를 동시에 설치하려면 각 원격 머신 이름의 목록을 쉼표로 구분하여 추가합니다.

        ```azurepowershell
        $sessions = New-PSSession -ComputerName myMachineName1, myMachineName2, myMachineName3
        Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Location <region> -PSSession $sessions
        ```

    다음 예에서는 단일 머신을 대상으로 하는 명령의 결과를 보여 줍니다.
    
    ```azurepowershell
    time="2020-08-07T13:13:25-07:00" level=info msg="Onboarding Machine. It usually takes a few minutes to complete. Sometimes it may take longer depending on network and server load status."
    time="2020-08-07T13:13:25-07:00" level=info msg="Check network connectivity to all endpoints..."
    time="2020-08-07T13:13:29-07:00" level=info msg="All endpoints are available... continue onboarding"
    time="2020-08-07T13:13:50-07:00" level=info msg="Successfully Onboarded Resource to Azure" VM Id=f65bffc7-4734-483e-b3ca-3164bfa42941
    
    Name           Location OSName   Status     ProvisioningState
    ----           -------- ------   ------     -----------------
    myMachineName  eastus   windows  Connected  Succeeded
    ```

## <a name="verify-the-connection-with-azure-arc"></a>Azure Arc 연결 확인

에이전트가 설치되고 Azure Arc 지원 서버를 등록하도록 구성되고 나면 Azure Portal로 이동하여 서버가 성공적으로 연결되었는지 확인합니다. [Azure Portal](https://portal.azure.com)에서 머신을 확인합니다.

![성공적인 서버 연결을 보여 주는 서버 대시보드의 스크린샷](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="next-steps"></a>다음 단계

* 필요한 경우 [Connected Machine 에이전트 문제 해결 가이드](troubleshoot-agent-onboard.md)를 참조하세요.

* [계획 및 배포 가이드](plan-at-scale-deployment.md)를 검토하여 모든 규모의 Azure Arc 지원 서버 배포를 계획하고 중앙 집중식 관리와 모니터링을 구현합니다.

* [Azure Policy](../../governance/policy/overview.md)를 사용하여 머신을 관리하는 방법을 알아봅니다. VM [게스트 구성](../../governance/policy/concepts/guest-configuration.md)을 사용하여 머신이 예상 Log Analytics 작업 영역에 보고를 하는지 확인하고 [VM 인사이트](../../azure-monitor/vm/vminsights-enable-policy.md)로 모니터링 기능을 사용하도록 설정할 수 있습니다.
