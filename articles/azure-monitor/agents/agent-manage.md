---
title: Azure Log Analytics 에이전트 관리
description: 이 문서에서는 컴퓨터에 배포된 Log Analytics Windows 또는 Linux 에이전트의 수명 주기 동안 일반적으로 수행할 다양한 관리 작업을 설명합니다.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/14/2019
ms.openlocfilehash: 5feef92aebae7c4179891f012d00bd1d23bf2413
ms.sourcegitcommit: 5c1cd21464e8165b16eb8d63ab31ab7b1a8f3675
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/29/2021
ms.locfileid: "133206747"
---
# <a name="managing-and-maintaining-the-log-analytics-agent-for-windows-and-linux"></a>Windows 및 Linux용 Log Analytics 에이전트 관리 및 유지 관리

Azure Monitor Log Analytics Windows 또는 Linux 에이전트를 처음 배포한 후 수명 주기에서 사용 중지 단계에 도달한 경우 에이전트를 다시 구성하거나 업그레이드하거나 컴퓨터에서 제거해야 할 수 있습니다. 이러한 일상적인 유지 관리 작업을 수동으로 또는 자동화를 통해 간단히 관리하여 조작 오류와 비용을 모두 줄일 수 있습니다.

## <a name="upgrading-agent"></a>에이전트 업그레이드

Windows 및 Linux용 Log Analytics 에이전트는 VM이 실행되는 배포 시나리오 및 환경에 따라 수동으로 또는 자동으로 최신 릴리스로 업그레이드할 수 있습니다. 다음 방법을 사용하여 에이전트를 업그레이드할 수 있습니다.

| 환경 | 설치 방법 | 업그레이드 방법 |
|--------|----------|-------------|
| Azure VM | Windows/Linux용 Log Analytics 에이전트 VM 확장 | *autoUpgradeMinorVersion* 속성을 **false로** 설정하여 옵트아웃하도록 Azure Resource Manager 템플릿을 구성하지 않은 경우 [에이전트는 VM 모델이 변경된 후](../../virtual-machines/extensions/features-linux.md#how-do-agents-and-extensions-get-updated)기본적으로 자동으로 업그레이드됩니다. |
| 사용자 지정 Azure VM 이미지 | Windows/Linux용 Log Analytics 에이전트 수동 설치 | VM을 최신 버전의 에이전트로 업데이트하려면 Windows 설치 관리자 패키지 또는 Linux 자동 압축 풀기 및 설치 가능한 셸 스크립트 번들을 실행하는 명령줄에서 수행해야 합니다.|
| 비 Azure VM | Windows/Linux용 Log Analytics 에이전트 수동 설치 | VM을 최신 버전의 에이전트로 업데이트하려면 Windows 설치 관리자 패키지 또는 Linux 자동 압축 풀기 및 설치 가능한 셸 스크립트 번들을 실행하는 명령줄에서 수행해야 합니다. |

### <a name="upgrade-windows-agent"></a>Windows 에이전트 업그레이드 

Windows VM의 에이전트를 Log Analytics VM 확장을 사용하여 설치되지 않은 최신 버전으로 업데이트하려면 명령 프롬프트, 스크립트 또는 기타 자동화 솔루션에서 또는 MMASetup-.msi 설치 마법사 사용하여 를 \<platform\> 실행합니다.  

다음 단계를 수행하여 Log Analytics 작업 영역에서 최신 버전의 Windows 에이전트를 다운로드할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. Azure Portal에서 **모든 서비스** 를 클릭합니다. 리소스 목록에서 **Log Analytics** 를 입력합니다. 입력을 시작하면 입력한 내용을 바탕으로 목록이 필터링됩니다. **Log Analytics 작업 영역** 을 선택합니다.

3. Log Analytics 작업 영역 목록에서 작업 영역을 선택합니다.

4. Log Analytics 작업 영역에서 **에이전트 관리 타일을** 선택한 다음, **서버를 Windows.**

5. Windows **서버** 페이지에서 Windows 운영 체제의 프로세서 아키텍처에 따라 다운로드할 적절한 Windows **에이전트** 다운로드 버전을 선택합니다.

>[!NOTE]
>Windows Log Analytics 에이전트를 업그레이드하는 동안 보고할 작업 영역 구성 또는 다시 구성을 지원하지 않습니다. 에이전트를 구성하려면 작업 영역 추가 또는 제거 아래에 나열된 지원되는 방법 중 하나를 따라야 [합니다.](#adding-or-removing-a-workspace)
>

#### <a name="to-upgrade-using-the-setup-wizard"></a>설치 마법사를 사용하여 업그레이드하려면

1. 관리 권한이 있는 계정으로 컴퓨터에 로그인합니다.

2. **MMASetup- \<platform\>.exe** 실행하여 설치 마법사 시작합니다.

3. 설치 마법사 첫 번째 페이지에서 **다음을** 클릭합니다.

4. Microsoft Monitoring Agent **설치** 대화 상자에서 **동의함을** 클릭하여 사용권 계약에 동의합니다.

5. **Microsoft Monitoring Agent 설치** 대화 상자에서 **업그레이드** 를 클릭합니다. 상태 페이지에 업그레이드 진행률이 표시됩니다.

6. Microsoft Monitoring Agent **구성이 성공적으로 완료된 경우** 페이지가 나타나면 **마침을** 클릭합니다.

#### <a name="to-upgrade-from-the-command-line"></a>명령줄에서 업그레이드하려면

1. 관리 권한이 있는 계정으로 컴퓨터에 로그인합니다.

2. 에이전트 설치 파일을 추출하려면 관리자 권한 명령 프롬프트에서 `MMASetup-<platform>.exe /c`를 실행합니다. 그러면 파일을 추출할 경로를 묻은 메시지가 표시됩니다. `MMASetup-<platform>.exe /c /t:<Full Path>` 인수를 전달하여 경로를 지정할 수도 있습니다.

3. 다음 명령을 실행합니다. 여기에서 D:\는 업그레이드 로그 파일의 위치입니다.

    ```dos
    setup.exe /qn /l*v D:\logs\AgentUpgrade.log AcceptEndUserLicenseAgreement=1
    ```

### <a name="upgrade-linux-agent"></a>Linux 에이전트 업그레이드 

이전 버전에서 업그레이드(>1.0.0-47)가 지원됩니다. `--upgrade` 명령을 사용하여 설치를 수행하면 에이전트의 모든 구성 요소가 최신 버전으로 업그레이드됩니다.

다음 명령을 실행하여 에이전트를 업그레이드합니다.

`sudo sh ./omsagent-*.universal.x64.sh --upgrade`

## <a name="adding-or-removing-a-workspace"></a>작업 영역 추가 또는 제거

### <a name="windows-agent"></a>Windows 에이전트
이 섹션의 단계는 다른 작업 영역에 보고하거나 구성에서 작업 영역을 제거하도록 Windows 에이전트를 다시 구성할 뿐만 아니라 둘 이상의 작업 영역(일반적으로 멀티 호밍이라고 함)에 보고하도록 에이전트를 구성하려는 경우에도 필요합니다. 여러 작업 영역에 보고하도록 Windows 에이전트를 구성하는 작업은 에이전트를 처음 설치한 후 아래에 설명된 방법을 사용해야만 수행할 수 있습니다.    

#### <a name="update-settings-from-control-panel"></a>제어판에서 설정 업데이트

1. 관리 권한이 있는 계정으로 컴퓨터에 로그인합니다.

2. **제어판** 을 엽니다.

3. **Microsoft Monitoring Agent** 를 선택한 다음, **Azure Log Analytics** 탭을 클릭합니다.

4. 작업 영역을 제거하려면 작업 영역을 선택한 후 **제거** 를 클릭합니다. 에이전트에서 보고를 중지할 다른 작업 영역에 대해 이 단계를 반복합니다.

5. 작업 영역을 추가하려면 **추가** 를 클릭하고 **Log Analytics 작업 영역 추가** 대화 상자에서 작업 영역 ID 및 작업 영역 키(기본 키)를 붙여넣습니다. 컴퓨터가 Azure Government 클라우드에서 Log Analytics 작업 영역에 보고해야 하는 경우 Azure Cloud 드롭다운 목록에서 Azure 미국 정부를 선택합니다.

6. **확인** 을 클릭하여 변경 내용을 저장합니다.

#### <a name="remove-a-workspace-using-powershell"></a>PowerShell을 사용하여 작업 영역 제거

```powershell
$workspaceId = "<Your workspace Id>"
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.RemoveCloudWorkspace($workspaceId)
$mma.ReloadConfiguration()
```

#### <a name="add-a-workspace-in-azure-commercial-using-powershell"></a>PowerShell을 사용하여 Azure 상용에서 작업 영역 추가

```powershell
$workspaceId = "<Your workspace Id>"
$workspaceKey = "<Your workspace Key>"
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.AddCloudWorkspace($workspaceId, $workspaceKey)
$mma.ReloadConfiguration()
```

#### <a name="add-a-workspace-in-azure-for-us-government-using-powershell"></a>PowerShell을 사용하여 미국 정부를 위한 Azure에서 작업 영역 추가

```powershell
$workspaceId = "<Your workspace Id>"
$workspaceKey = "<Your workspace Key>"
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.AddCloudWorkspace($workspaceId, $workspaceKey, 1)
$mma.ReloadConfiguration()
```

>[!NOTE]
>이전에 명령줄 또는 스크립트를 사용하여 에이전트를 설치 또는 구성한 경우 `EnableAzureOperationalInsights`가 `AddCloudWorkspace` 및 `RemoveCloudWorkspace`로 바뀌었습니다.
>

### <a name="linux-agent"></a>Linux 에이전트
다음 단계에서는 다른 작업 영역에 등록하거나 구성에서 작업 영역을 제거하기로 결정한 경우 Linux 에이전트를 다시 구성하는 방법을 보여 줍니다.

1. 작업 영역에 등록되었는지 확인하려면 다음 명령을 실행합니다.

    `/opt/microsoft/omsagent/bin/omsadmin.sh -l`

    다음 예제와 유사한 결과가 반환됩니다.

    `Primary Workspace: <workspaceId>   Status: Onboarded(OMSAgent Running)`

    상태는 에이전트가 실행 중임을 보여주거나 그렇지 않으면 에이전트를 다시 구성하는 다음 단계가 성공적으로 완료되지 않는 것을 보여줍니다.

2. 작업 영역으로 이미 등록된 경우 다음 명령을 실행하여 등록된 작업 영역을 제거합니다. 그렇지 않으면 등록되지 않은 경우 다음 단계를 진행합니다.

    `/opt/microsoft/omsagent/bin/omsadmin.sh -X`

3. 다른 작업 영역으로 등록하려면 다음 명령을 실행합니다.

    `/opt/microsoft/omsagent/bin/omsadmin.sh -w <workspace id> -s <shared key> [-d <top level domain>]`
    
4. 변경 내용이 적용되었는지 확인하려면 다음 명령을 실행합니다.

    `/opt/microsoft/omsagent/bin/omsadmin.sh -l`

    다음 예제와 유사한 결과가 반환됩니다.

    `Primary Workspace: <workspaceId>   Status: Onboarded(OMSAgent Running)`

변경 내용 적용을 위해 에이전트 서비스를 다시 시작할 필요가 없습니다.

## <a name="update-proxy-settings"></a>프록시 설정 업데이트
배포 후 에이전트가 프록시 서버 또는 [Log Analytics 게이트웨이](./gateway.md)를 통해 서비스와 통신하도록 구성하려면 다음 방법 중 하나를 사용하여 이 작업을 완료합니다.

### <a name="windows-agent"></a>Windows 에이전트

#### <a name="update-settings-using-control-panel"></a>제어판을 사용하여 설정 업데이트

1. 관리 권한이 있는 계정으로 컴퓨터에 로그인합니다.

2. **제어판** 을 엽니다.

3. **Microsoft Monitoring Agent** 선택한 다음 **프록시 설정** 탭을 클릭합니다.

4. **프록시 서버 사용** 을 클릭하고 프록시 서버 또는 게이트웨이의 URL 및 포트 번호를 제공합니다. 프록시 서버 또는 Log Analytics 게이트웨이에 인증이 필요한 경우 인증할 사용자 이름과 암호를 입력한 다음, **확인** 을 클릭합니다.

#### <a name="update-settings-using-powershell"></a>PowerShell을 사용하여 설정 업데이트

다음 샘플 PowerShell 코드를 복사하고 자신의 환경 관련 정보로 업데이트한 후 PS1 파일 이름 확장명으로 저장합니다. Azure Monitor Log Analytics 작업 영역에 직접 연결하는 각 컴퓨터에서 스크립트를 실행합니다.

```powershell
param($ProxyDomainName="https://proxy.contoso.com:30443", $cred=(Get-Credential))

# First we get the Health Service configuration object. We need to determine if we
#have the right update rollup with the API we need. If not, no need to run the rest of the script.
$healthServiceSettings = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'

$proxyMethod = $healthServiceSettings | Get-Member -Name 'SetProxyInfo'

if (!$proxyMethod)
{
    Write-Output 'Health Service proxy API not present, will not update settings.'
    return
}

Write-Output "Clearing proxy settings."
$healthServiceSettings.SetProxyInfo('', '', '')

$ProxyUserName = $cred.username

Write-Output "Setting proxy to $ProxyDomainName with proxy username $ProxyUserName."
$healthServiceSettings.SetProxyInfo($ProxyDomainName, $ProxyUserName, $cred.GetNetworkCredential().password)
```

### <a name="linux-agent"></a>Linux 에이전트
Linux 컴퓨터가 프록시 서버 또는 Log Analytics 게이트웨이를 통해 통신해야 하는 경우 다음 단계를 수행합니다. 프록시 구성 값은 다음 구문 `[protocol://][user:password@]proxyhost[:port]`를 갖습니다. *proxyhost* 속성은 프록시 서버의 정규화된 도메인 이름 또는 IP 주소를 허용합니다.

1. 다음 명령을 실행하여 `/etc/opt/microsoft/omsagent/proxy.conf` 파일을 편집하고 특정 설정으로 값을 변경합니다.

    ```
    proxyconf="https://proxyuser:proxypassword@proxyserver01:30443"
    sudo echo $proxyconf >>/etc/opt/microsoft/omsagent/proxy.conf
    sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/proxy.conf
    ```

2. 다음 명령을 실행하여 에이전트를 다시 시작합니다.

    ```
    sudo /opt/microsoft/omsagent/bin/service_control restart [<workspace id>]
    ```

## <a name="uninstall-agent"></a>에이전트 제거
명령줄 또는 설치 마법사를 사용하여 Windows 또는 Linux 에이전트를 제거하려면 다음 절차 중 하나를 사용합니다.

### <a name="windows-agent"></a>Windows 에이전트

#### <a name="uninstall-from-control-panel"></a>제어판에서 제거
1. 관리 권한이 있는 계정으로 컴퓨터에 로그인합니다.

2. **제어판** 에서 **프로그램 및 기능** 을 클릭합니다.

3. **프로그램 및 기능** 에서 **Microsoft Monitoring Agent** 를 클릭하고 **제거** 를 클릭한 다음 **예** 를 클릭합니다.

>[!NOTE]
>Azure Portal의 작업 영역에서 다운로드할 수 있는 **MMASetup-\<platform\>.exe** 를 두 번 클릭하여 에이전트 설치 마법사를 실행할 수도 있습니다.

#### <a name="uninstall-from-the-command-line"></a>명령줄에서 제거
다운로드한 에이전트용 파일은 IExpress로 만든 자체 포함 설치 패키지입니다. 에이전트용 설치 프로그램과 지원 파일이 패키지에 포함되어 있으며 다음 예제에 나온 대로 명령줄을 사용하여 올바로 제거하려면 이러한 프로그램과 파일을 추출해야 합니다.

1. 관리 권한이 있는 계정으로 컴퓨터에 로그인합니다.

2. 에이전트 설치 파일을 추출하려면 관리자 권한 명령 프롬프트에서 `extract MMASetup-<platform>.exe`를 실행합니다. 그러면 파일을 추출할 경로를 묻은 메시지가 표시됩니다. `extract MMASetup-<platform>.exe /c:<Path> /t:<Path>` 인수를 전달하여 경로를 지정할 수도 있습니다. IExpress에서 지원하는 명령줄 스위치에 대한 자세한 내용은 [IExpress의 명령줄 스위치](https://www.betaarchive.com/wiki/index.php?title=Microsoft_KB_Archive/197147)를 참조하고 필요에 맞게 예제를 업데이트하세요.

3. 프롬프트에 `%WinDir%\System32\msiexec.exe /x <Path>:\MOMAgent.msi /qb`를 입력합니다.

### <a name="linux-agent"></a>Linux 에이전트
에이전트를 제거하려면 Linux 컴퓨터에서 다음 명령을 실행합니다. *--purge* 인수는 에이전트와 해당 구성을 완전히 제거합니다.

   `wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh --purge`

## <a name="configure-agent-to-report-to-an-operations-manager-management-group"></a>에이전트에서 Operations Manager 관리 그룹에 보고하도록 구성

### <a name="windows-agent"></a>Windows 에이전트
System Center Operations Manager 관리 그룹에 보고하도록 Windows용 Log Analytics 에이전트를 구성하려면 다음 단계를 수행합니다.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

1. 관리 권한이 있는 계정으로 컴퓨터에 로그인합니다.

2. **제어판** 을 엽니다.

3. **Microsoft Monitoring Agent** 를 클릭한 다음 **Operations Manager** 탭을 클릭합니다.

4. Operations Manager 서버가 Active Directory와 통합된 경우 **AD DS에서 관리 그룹 할당 자동 업데이트** 를 클릭합니다.

5. **추가** 를 클릭하여 **관리 그룹 추가** 대화 상자를 엽니다.

6. **관리 그룹 이름** 필드에 관리 그룹의 이름을 입력합니다.

7. **기본 관리 서버** 필드에 기본 관리 서버의 컴퓨터 이름을 입력합니다.

8. **관리 서버 포트** 필드에 TCP 포트 번호를 입력합니다.

9. **에이전트 작업 계정** 에서 로컬 시스템 계정 또는 로컬 도메인 계정을 선택합니다.

10. **확인** 을 클릭하여 **관리 그룹 추가** 대화 상자를 닫은 다음 **확인** 을 클릭하여 **Microsoft Monitoring Agent 속성** 대화 상자를 닫습니다.

### <a name="linux-agent"></a>Linux 에이전트
System Center Operations Manager 관리 그룹에 보고하도록 Linux용 Log Analytics 에이전트를 구성하려면 다음 단계를 수행합니다.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

1. 파일 `/etc/opt/omi/conf/omiserver.conf`

2. `httpsport=`로 시작되는 줄이 1270 포트를 지정하도록 합니다. 예: `httpsport=1270`

3. OMI 서버를 다시 시작합니다. `sudo /opt/omi/bin/service_control restart`

## <a name="next-steps"></a>다음 단계

- [Linux 에이전트를](agent-linux-troubleshoot.md) 설치하거나 관리하는 동안 문제가 발생하는 경우 Linux 에이전트 문제 해결을 검토합니다.

- Windows 에이전트를 설치하거나 관리하는 동안 문제가 발생하는 경우 Windows 에이전트 [문제 해결을](agent-windows-troubleshoot.md) 검토합니다.
