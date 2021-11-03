---
title: Azure Stack Edge 디바이스에 대한 원격 지원, 진단 및 수정 사용
description: Azure Stack Edge 디바이스에서 원격 지원을 사용하도록 설정하는 방법을 설명합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: troubleshooting
ms.date: 10/26/2021
ms.author: alkohli
ms.openlocfilehash: 364845dc046664a7af4d9f7ac6fbb965a818430a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131103738"
---
# <a name="remote-support-and-diagnostics-for-azure-stack-edge"></a>Azure Stack Edge 대한 원격 지원 및 진단 

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]


> [!IMPORTANT]
> 원격 지원은 공개 미리 보기로 있으며 Azure Stack Edge 버전 2110 이상에 적용됩니다.

Azure Stack Edge 디바이스에서 원격 지원을 사용하도록 설정하여 Microsoft 엔지니어가 디바이스에 원격으로 액세스하여 문제를 진단하고 수정할 수 있습니다. 이 기능을 사용하도록 설정하면 액세스 수준 및 액세스 기간에 대한 동의를 제공합니다. 

이 문서에서는 이 기능을 사용하는 시기, 기능을 사용하도록 설정하는 방법 등 원격 지원 기능에 대해 설명하고 Microsoft 엔지니어가 디바이스에서 원격으로 실행할 수 있는 허용된 작업 목록을 제공합니다.


## <a name="about-remote-support"></a>원격 지원 정보

일반적으로 Azure Stack Edge 문제가 발생하는 경우 지원 패키지를 수집하고 Microsoft 지원 제공해야 합니다. 경우에 따라 로그에 문제를 진단하고 수정하는 데 충분한 정보가 없습니다. Microsoft 지원 원격 지원에 대한 동의를 얻기 위해 연락합니다.

원격 지원의 몇 가지 이점은 다음과 같습니다.

- Microsoft 지원 직접 회의를 준비할 필요가 없으면 문제를 더 빠르게 해결할 수 있습니다.
- 디바이스에서 실행된 모든 작업을 포함하여 원격 지원 세션의 기록을 볼 수 있습니다.
- 사용자는 데이터에 대한 모든 권한을 가지며 언제든지 동의를 철회할 수 있습니다. 세션을 종료하지 않으면 액세스 기간이 만료되면 세션에 대한 액세스가 자동으로 비활성화됩니다.

## <a name="how-remote-support-works"></a>원격 지원 작동 방식

다음 그림에서는 원격 지원의 작동 방식을 보여 드립니다.

![Azure Stack Edge 원격 지원 그림](media/azure-stack-edge-gpu-remote-support-diagnostics-repair/remote-support-flow-1.png)

1. Azure Portal 통해 디바이스에 대한 원격 지원에 대한 동의를 제공합니다. 또한 디바이스에 대한 액세스 수준 및 기간을 구성합니다.
1. 원격 지원에 대한 동의 및 요청은 Azure Portal Azure Stack Edge RP(리소스 공급자)로 전송되며, 이 공급자는 이를 디바이스로 보냅니다. 디바이스 에이전트는 연결할 수 있는 하이브리드 연결 자격 증명을 RP에 요청합니다.
1. Azure 하이브리드 연결을 설정하는 데 사용되는 하이브리드 연결 자격 증명이 만들어집니다. 이 연결은 다음과 같습니다.
    
    - 디바이스별. 각 디바이스에는 자체 연결이 있고 연결은 공유되지 않습니다.
    - Microsoft 지원 안전하고 감사된 규격 채널을 통해 디바이스에 JIT(Just-In-Time) 액세스를 허용합니다.  
    - 이 연결은 포트 443을 통해 *https* 프로토콜을 사용하고 트래픽은 TLS 1.2로 암호화됩니다.
     
1. 디바이스 에이전트는 Azure 지원 센터의 브라우저 인터페이스를 통해 들어오는 모든 요청에 대해 해당 하이브리드 연결을 수신 대기하기 시작합니다. 
1. 브라우저는 하이브리드 연결에 대한 연결을 요청하고 요청은 제한된 PowerShell 세션을 열기 위해 디바이스로 전송됩니다. 동의가 있으면 요청이 수락됩니다. 디바이스에 동의가 없으면 해당 연결만 거부합니다. 

연결이 설정되면 이 보안 연결을 통해 모든 통신이 발생합니다. 

이 연결을 통해 수행할 수 Microsoft 지원 작업은 JEA(충분한 [관리)를](/powershell/scripting/learn/remoting/jea/overview) 사용하여 부여된 액세스 수준에 따라 *제한됩니다.* 원격 지원 세션 중에 Microsoft 지원 실행할 수 있는 cmdlet에 대한 자세한 내용은 이 문서의 [허용되는 Microsoft 지원 작업 목록을 참조하세요.](#operations-allowed-in-remote-support)


## <a name="enable-remote-support"></a>원격 지원 사용

Azure Stack Edge 디바이스에 대한 원격 지원을 구성하려면 다음 단계를 수행합니다.

1. Azure Portal 디바이스에 대한 Azure Stack Edge 리소스로 이동한 다음 **진단** 으로 이동합니다.
1. 기본적으로 Microsoft는 디바이스에 대한 원격 액세스 권한이 없으며 원격 지원 상태가 **사용 안** 됨으로 표시됩니다. 이 기능을 사용하도록 설정하려면 **원격 지원 구성을** 선택합니다.

    ![원격 지원 구성이 강조 표시된 Azure Stack Edge 원격 지원을 사용하도록 설정하는 스크린샷](media/azure-stack-edge-gpu-remote-support-diagnostics-repair/enable-remote-support-1.png)

1. **진단으로** **액세스 수준을** 선택하여 진단 정보를 원격으로 수집하는 Microsoft 지원 대한 JIT 액세스를 제공합니다. 필요한 경우 지원 팀에서 **진단 및 복구를** 선택하여 원격 수정 작업을 허용하는 것이 좋습니다. 

    각 액세스 수준은 디바이스에서 서로 다른 원격 명령 집합을 사용하도록 설정하므로 다른 작업 집합을 사용할 수 있습니다. 

    - 진단은 대부분 읽기 작업을 허용하므로 대부분 `Get` cmdlet을 사용할 수 있습니다.
    - 진단 및 복구는 읽기/쓰기 액세스를 허용하므로 `Get` cmdlet 외에도 `Set` , , , , `Add` `Start` `Restart` `Stop` `Invoke` `Remove` cmdlet도 사용할 수 있습니다.
    
    자세한 내용은 각 [액세스 수준에서 허용되는 모든 지원 작업 목록을 참조하세요.](#operations-allowed-in-remote-support)

1. 원격 액세스를 제공할 기간을 선택합니다. 기간은 7, 15, 21 또는 30일이 될 수 있습니다. 이 기간이 끝나면 디바이스에 대한 원격 액세스가 자동으로 비활성화됩니다. 

1. 언제든지 액세스를 취소하려면 액세스를 **허용하지 않음으로** 설정합니다. 이 작업은 기존 세션이 새 세션을 설정할 수 없도록 종료합니다.

1. 원격 지원을 구성한 후 **적용을** 선택하여 설정을 적용합니다. **적용을** 선택하면 안전하고 제어된 환경에서 진단 정보를 원격으로 수집하도록 Microsoft에 동의하게 됩니다.

    ![액세스 수준, 기간이 강조 표시된 Azure Stack Edge 원격 지원 설정의 스크린샷](media/azure-stack-edge-gpu-remote-support-diagnostics-repair/enable-remote-support-2.png)    

## <a name="remote-support-examples"></a>원격 지원 예제

다음 예제 시나리오에서는 디바이스에서 원격 지원을 사용하는 경우 다양한 작업을 수행하는 방법을 보여 줍니다. 

원격 지원 작업을 수행하려면 먼저 디바이스의 [PowerShell 인터페이스에 커넥트](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface) 다음 cmdlet을 실행해야 합니다.

### <a name="list-existing-remote-sessions"></a>기존 원격 세션 나열

`Get-HcsRemoteSupportSession`cmdlet을 사용하여 지정된 일 수 내에 디바이스에 대해 만들어진 모든 원격 세션을 나열합니다.

```powershell
Get-HcsRemoteSupportSession -IncludeTranscriptContents $false -NumberOfDays <Number of days>
```
다음은 지난 10일 동안 구성된 모든 원격 지원 세션에 대한 출력 예제입니다. 

```output
[10.126.76.20]: PS>Get-HcsRemoteSupportSession -IncludeTranscriptContents $false -NumberOfDays 10

SessionId                : 3c135cba-f479-4fef-8dbb-a2b52b744504
RemoteApplication        : Powershell
AccessLevel              : ReadWrite
ControlSession           : False
SessionStartTime         : 8/19/2021 10:41:03 PM +00:00
SessionEndTime           : 8/19/2021 10:44:31 PM +00:00
SessionTranscriptPath    : C:\ProgramData\JEAConfiguration\Transcripts\RemoteRepair\PowerShell_transcript.HW4J1T2.Lp+Myohb.20210
                           819154101.txt
SessionTranscriptContent :

SessionId                : c0f2d002-66a0-4d54-87e4-4b1b949ad686
RemoteApplication        : Powershell
AccessLevel              : ReadWrite
ControlSession           : False
SessionStartTime         : 8/19/2021 7:41:19 PM +00:00
SessionEndTime           : 8/19/2021 7:58:20 PM +00:00
SessionTranscriptPath    : C:\ProgramData\JEAConfiguration\Transcripts\RemoteRepair\PowerShell_transcript.HW4J1T2.j0lCd5Tm.20210
                           819124117.txt
SessionTranscriptContent :

SessionId                : ca038e58-5344-4377-ab9c-c857a27c8b73
RemoteApplication        : Powershell
AccessLevel              : ReadOnly
ControlSession           : False
SessionStartTime         : 8/19/2021 10:49:39 PM +00:00
SessionEndTime           : 8/20/2021 12:49:40 AM +00:00
SessionTranscriptPath    : C:\ProgramData\JEAConfiguration\Transcripts\RemoteDiagnostics\PowerShell_transcript.HW4J1T2.YHmes94q.
                           20210819154937.txt
SessionTranscriptContent :

[10.126.76.20]: PS>
```


### <a name="get-details-on-a-specific-remote-session"></a>특정 원격 세션에 대한 세부 정보 얻기

`Get-HcsRemoteSupportSession`cmdlet을 사용하여 ID *SessionID* 를 사용하여 원격 세션에 대한 세부 정보를 확인합니다.

```powershell
Get-HcsRemoteSupportSession -SessionId <SessionId> -IncludeSessionTranscript $true
```

다음은 **진단** 옵션을 사용하여 원격 지원을 구성한 특정 세션에 대한 예제 출력입니다. 이 경우 는 `AccessLevel` `ReadOnly` 입니다.

```output
[10.126.76.20]: PS>Get-HcsRemoteSupportSession -SessionId 360706a2-c530-419f-932b-55403e19502e -IncludeTranscriptContents $true

SessionId                : 360706a2-c530-419f-932b-55403e19502e
RemoteApplication        : Powershell
AccessLevel              : ReadOnly
ControlSession           : False
SessionStartTime         : 8/26/2021 2:35:37 PM +00:00
SessionEndTime           :
SessionTranscriptPath    : C:\ProgramData\JEAConfiguration\Transcripts\RemoteDiagnostics\PowerShell_transcript.HW4J1T2.u7qF2J2d.
                           20210826073536.txt
SessionTranscriptContent : **********************
                           Windows PowerShell transcript start
                           Start time: 20210826073536
                           Username: WORKGROUP\HW4J1T2$
                           RunAs User: WORKGROUP\SYSTEM
                           Configuration Name: RemoteDiagnostics
                           Machine: HW4J1T2 (Microsoft Windows NT 10.0.17763.0)
                           Host Application: C:\Windows\system32\wsmprovhost.exe -Embedding
                           Process ID: 10976
                           PSVersion: 5.1.17763.10520
                           PSEdition: Desktop
                           PSCompatibleVersions: 1.0, 2.0, 3.0, 4.0, 5.0, 5.1.17763.10520
                           BuildVersion: 10.0.17763.10520
                           CLRVersion: 4.0.30319.42000
                           WSManStackVersion: 3.0
                           PSRemotingProtocolVersion: 2.3
                           SerializationVersion: 1.1.0.1
                           **********************
                           PS>CommandInvocation(Get-Command): "Get-Command"
                           >> ParameterBinding(Get-Command): name="Name"; value="Out-Default, Exit-PSSession"
                           >> ParameterBinding(Get-Command): name="CommandType"; value="Alias, Function, Filter, Cmdlet,
                           Configuration"
                           >> ParameterBinding(Get-Command): name="Module"; value=""
                           >> ParameterBinding(Get-Command): name="ListImported"; value="True"
                           >> ParameterBinding(Get-Command): name="ErrorAction"; value="SilentlyContinue"
                           >> CommandInvocation(Measure-Object): "Measure-Object"
                           >> CommandInvocation(Select-Object): "Select-Object"
                           >> ParameterBinding(Select-Object): name="Property"; value="Count"
                           >> ParameterBinding(Measure-Object): name="InputObject"; value="Out-Default"
                           >> ParameterBinding(Measure-Object): name="InputObject"; value="Exit-PSSession"
                           PS>ParameterBinding(Select-Object): name="InputObject";
                           value="Microsoft.PowerShell.Commands.GenericMeasureInfo"

                           Count
                           -----
                               2

                           PS>CommandInvocation(Get-Command): "Get-Command"
                           >> ParameterBinding(Get-Command): name="Name"; value="Out-Default, Exit-PSSession"
                           >> ParameterBinding(Get-Command): name="CommandType"; value="Alias, Function, Filter, Cmdlet,
                           Configuration"
                           >> ParameterBinding(Get-Command): name="Module"; value=""
                           >> ParameterBinding(Get-Command): name="ListImported"; value="True"
                           >> CommandInvocation(Select-Object): "Select-Object"
                           >> ParameterBinding(Select-Object): name="Property"; value="Name, Namespace, HelpUri, CommandType,
                           ResolvedCommandName, OutputType, Parameters"
                           >> ParameterBinding(Select-Object): name="InputObject"; value="Out-Default"

                           Name                : Out-Default
                           Namespace           : Microsoft.PowerShell.Core
                           HelpUri             : https://go.microsoft.com/fwlink/?LinkID=113362
                           CommandType         : Cmdlet
                           ResolvedCommandName :
                           OutputType          : {}
                           Parameters          : {[Transcript, System.Management.Automation.ParameterMetadata], [InputObject,
                                                 System.Management.Automation.ParameterMetadata], [Verbose,
                                                 System.Management.Automation.ParameterMetadata], [Debug,
                                                 System.Management.Automation.ParameterMetadata]...}

                           >> ParameterBinding(Select-Object): name="InputObject"; value="Exit-PSSession"
                           Name                : Exit-PSSession
                           Namespace           :
                           HelpUri             :
                           CommandType         : Function
                           ResolvedCommandName :
                           OutputType          : {}
                           Parameters          : {}



                           PS>CommandInvocation(Get-Command): "Get-Command"
                           CommandInvocation(Get-HcsApplianceInfo): "Get-HcsApplianceInfo"

[10.126.76.20]: PS>
```
다음은 **진단 및 복구** 옵션이 원격 지원을 위해 구성된 경우의 예제 샘플 출력입니다. `AccessLevel`원격 지원 세션의 는 입니다. `ReadWrite`

```output
[10.126.76.20]: PS>Get-HcsRemoteSupportSession -SessionId add360db-4593-4026-93d5-6d6d05d39046 -IncludeTranscriptContents $true

SessionId                : add360db-4593-4026-93d5-6d6d05d39046
RemoteApplication        : Powershell
AccessLevel              : ReadWrite
ControlSession           : False
SessionStartTime         : 8/26/2021 2:57:08 PM +00:00
SessionEndTime           :
SessionTranscriptPath    : C:\ProgramData\JEAConfiguration\Transcripts\RemoteRepair\PowerShell_transcript.HW4J1T2.ZroHb8Un.20210
                           826075705.txt
SessionTranscriptContent : **********************
                           Windows PowerShell transcript start
                           Start time: 20210826075705
                           Username: WORKGROUP\HW4J1T2$
                           RunAs User: WORKGROUP\SYSTEM
                           Configuration Name: RemoteRepair
                           Machine: HW4J1T2 (Microsoft Windows NT 10.0.17763.0)
                           Host Application: C:\Windows\system32\wsmprovhost.exe -Embedding
                           Process ID: 21832
                           PSVersion: 5.1.17763.10520
                           PSEdition: Desktop
                           PSCompatibleVersions: 1.0, 2.0, 3.0, 4.0, 5.0, 5.1.17763.10520
                           BuildVersion: 10.0.17763.10520
                           CLRVersion: 4.0.30319.42000
                           WSManStackVersion: 3.0
                           PSRemotingProtocolVersion: 2.3
                           SerializationVersion: 1.1.0.1
                           **********************
                           PS>CommandInvocation(Get-Command): "Get-Command"
                           >> ParameterBinding(Get-Command): name="Name"; value="Out-Default, Exit-PSSession"
                           >> ParameterBinding(Get-Command): name="CommandType"; value="Alias, Function, Filter, Cmdlet,
                           Configuration"
                           >> ParameterBinding(Get-Command): name="Module"; value=""
                           >> ParameterBinding(Get-Command): name="ListImported"; value="True"
                           >> ParameterBinding(Get-Command): name="ErrorAction"; value="SilentlyContinue"
                           >> CommandInvocation(Measure-Object): "Measure-Object"
                           >> CommandInvocation(Select-Object): "Select-Object"
                           >> ParameterBinding(Select-Object): name="Property"; value="Count"
                           >> ParameterBinding(Measure-Object): name="InputObject"; value="Out-Default"
                           >> ParameterBinding(Measure-Object): name="InputObject"; value="Exit-PSSession"
                           PS>ParameterBinding(Select-Object): name="InputObject";
                           value="Microsoft.PowerShell.Commands.GenericMeasureInfo"

                           Count
                           -----
                               2

                           PS>CommandInvocation(Get-Command): "Get-Command"
                           >> ParameterBinding(Get-Command): name="Name"; value="Out-Default, Exit-PSSession"
                           >> ParameterBinding(Get-Command): name="CommandType"; value="Alias, Function, Filter, Cmdlet,
                           Configuration"
                           >> ParameterBinding(Get-Command): name="Module"; value=""
                           >> ParameterBinding(Get-Command): name="ListImported"; value="True"
                           >> CommandInvocation(Select-Object): "Select-Object"
                           >> ParameterBinding(Select-Object): name="Property"; value="Name, Namespace, HelpUri, CommandType,
                           ResolvedCommandName, OutputType, Parameters"
                           >> ParameterBinding(Select-Object): name="InputObject"; value="Out-Default"

                           Name                : Out-Default
                           Namespace           : Microsoft.PowerShell.Core
                           HelpUri             : https://go.microsoft.com/fwlink/?LinkID=113362
                           CommandType         : Cmdlet
                           ResolvedCommandName :
                           OutputType          : {}
                           Parameters          : {[Transcript, System.Management.Automation.ParameterMetadata], [InputObject,
                                                 System.Management.Automation.ParameterMetadata], [Verbose,
                                                 System.Management.Automation.ParameterMetadata], [Debug,
                                                 System.Management.Automation.ParameterMetadata]...}

                           >> ParameterBinding(Select-Object): name="InputObject"; value="Exit-PSSession"
                           Name                : Exit-PSSession
                           Namespace           :
                           HelpUri             :
                           CommandType         : Function
                           ResolvedCommandName :
                           OutputType          : {}
                           Parameters          : {}

                           PS>CommandInvocation(Get-Command): "Get-Command"
[10.126.76.20]: PS>
```
 
### <a name="collect-remote-session-transcripts"></a>원격 세션 기록 수집

감사 요구 사항에 따라 기록을 확인해야 할 수 있습니다. 원격 세션 기록을 수집하려면 다음 단계를 수행합니다.

1. 로컬 UI에서 문제 **해결 > 지원으로** 이동합니다. 지원 패키지를 수집합니다.
1. 지원 패키지가 수집되면 지원 패키지를 다운로드합니다. 압축된 폴더를 추출합니다. 대본은 지원 패키지의 **SupportTranscripts** 폴더에 있습니다. 

    ![지원 패키지에 강조 표시된 SupportTranscripts 폴더의 스크린샷](media/azure-stack-edge-gpu-remote-support-diagnostics-repair/transcript-folder-support-package-1.png)


 
## <a name="operations-allowed-in-remote-support"></a>원격 지원에서 허용되는 작업

다음 섹션에서는 원격 지원 세션 중에 Microsoft 지원 실행할 수 있는 허용된 cmdlet을 나열합니다. cmdlet 가용성은 진단 또는 **진단으로** 설정된 액세스 수준으로 세분화되고 **를 복구합니다.**

#### <a name="azure-stack-edge-cmdlets"></a>Azure Stack Edge cmdlet

| Cmdlet                      | 진단 | 진단 & 복구 | 설명 |
|-----------------------------------------------|-------------|---------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Add-HcsExternalVirtualSwitch                  |             | Y                   | 디바이스에서 Kubernetes를 구성하는 새 외부 가상 스위치를 만듭니다.  |
| Add-HcsVirtualNetwork                         |             | Y                   | 지정된 네트워크 인터페이스에 새 가상 스위치를 만듭니다.|
| Get-AcsHealthStatus                           | Y           | Y                   | Azure Consistent Service Providers의 상태를 가져옵니다. |
| Get-AzureDataBoxEdgeRole                      | Y           | Y                   | 디바이스에서 컴퓨팅 역할이 구성된 경우 PowerShell 인터페이스를 통해 컴퓨팅 로그를 가져옵니다.|
| Get-HcsApplianceInfo                          | Y           | Y                   | ID, 이름, 소프트웨어 버전 또는 시스템 상태와 같은 디바이스에 대한 정보를 가져옵니다.|
| Get-HcsApplianceSupportPackage                | Y           | Y                   | 디바이스에 대한 지원 패키지를 수집합니다.     |
| Get-HcsArpResponse                            | Y           | Y                   |   |
| Get-HcsControllerSetupInformation             | Y           | Y                   | Microsoft.Hcs.Setup.ControllerInfo 개체를 가져옵니다. |
| Get-HcsDataBoxAccount                         | Y           | Y                   ||
| Get-HcsExternalVirtualSwitch                  | Y           | Y                   | Kubernetes를 구성해야 하는 스위치를 가져옵니다.   |
| Get-HcsGpuNvidiaSmi                           | Y           | Y                   | 디바이스에 대한 GPU 드라이버 정보를 가져옵니다.  |
| Get-HcsIPAddress                              | Y           | Y                   | 데이터 저장소 또는 시스템에서 네트워크 어댑터 구성을 가져옵니다.   |
| Get-HcsIPAddressPool                          |             | Y                   | |
| Get-HcsKubeClusterInfo                        | Y           | Y                   | Kubernetes 클러스터 구성 정보를 가져옵니다.|
| Get-HcsKubeClusterNetworkInfo                 | Y           | Y                   | Kubernetes 서비스 및 Pod 서브넷을 가져옵니다.                                                                                                                                                   |
| Get-HcsKubernetesAzureMonitorConfiguration    | Y           | Y                   | 디바이스의 Kubernetes 클러스터에서 실행되는 Azure Monitor 대한 정보를 가져옵니다.                                                                                                        |
| Get-HcsKubernetesContainerRegistryInfo        | Y           | Y                   | 디바이스의 에지 컨테이너 레지스트리에 대한 세부 정보를 가져옵니다.                                                                                                                               |
| Get-HcsKubernetesDashboardToken               | Y           | Y                   | 대시보드를 볼 Kubernetes 대시보드 토큰을 가져옵니다(로컬 UI를 통해 동일한 작업을 수행할 수 있습니다.)                                                                                               |
| Get-HcsKubernetesNamespaces                   | Y           | Y                   | 구성한 Kubernetes 네임스페이스를 가져옵니다.  |
| Get-HcsKubernetesUserConfig                   | Y           | Y                   | `kubeconfig`구성한 특정 네임스페이스에 해당하는 를 가져옵니다.  |
| Get-HcsLocalWebUICertificateHash              | Y           | Y                   | 구성된 로컬 웹 UI 인증서의 지문을 가져옵니다. |
| Get-HcsMacAddressPool                         |             | Y                   | Kubernetes VM Mac 주소 풀을 가져옵니다.     |
| Get-HcsNetBmcInterface                        | Y           | Y                   | IPv4 주소, IPv4 게이트웨이, IPv4 서브넷 마스크 및 디바이스에서 DHCP가 사용되는지 여부와 같은 BMC(베이스보드 관리 컨트롤러)의 네트워크 구성 속성을 가져옵니다. |
| Get-HcsNetInterface                           | Y           | Y                   | 디바이스의 원하는 네트워크 구성을 가져옵니다.    |
| Get-HcsNetRoute                               | Y           | Y                   | 디바이스에 추가한 모든 사용자 지정 경로 구성을 확인합니다. 이러한 경로에는 디바이스에 이미 있는 모든 시스템 경로 또는 기본 경로가 포함되지 않습니다.            |
| Get-HcsNodeSecureEraseLogs                    | Y           | Y                   | 제거하려면 Er도그에서 확인합니다. - 이전에 공장 기본값으로 다시 설정하는 동안 디바이스의 드라이브가 안전하게 지워졌는 것을 확인하는 로그를 검색합니다.     |
| Get-HcsNodeSupportPackage                     | Y           | Y                   | 디바이스에서 로그를 수집하고 지원 패키지의 형태로 지정된 네트워크 또는 로컬 공유에 해당 로그를 복사합니다.   |
| Get-HcsRemoteSupportConsent                   | Y           | Y                   | 디바이스에서 원격 지원을 사용하도록 설정하는 고객의 동의를 가져옵니다.   |
| Get-HcsRestEndPoint                           | Y           | Y                   |  |
| Get-HcsSetupDesiredStateResult                | Y           | Y                   | 디바이스에 대한 일부 DSC 실행의 DSC(Desired State Configuration) 결과 개체를 가져옵니다. |
| Get-HcsSmbConfiguration                       | Y           | Y                   | <!-- to be removed--> |
| Get-HcsSupportedVpnRegions                    | Y           | Y                   |   |
| Get-HcsSupportPackageUploadJob                | Y           | Y                   | 실행 중인 지원 패키지 업로드 작업의 상태를 가져옵니다.  |
| Get-HcsUpdateConfiguration                    | Y           | Y                   | 서버 유형, 서버에 대 한 URI 경로와 같은 Azure Stack Edge 장치에 대해 구성 된 업데이트 서버 설정을 가져옵니다.  |
| Get-HcsUpdateJob                              | Y           | Y                   | 장치에서 실행 되는 모든 업데이트 작업을 페치 하거나, 작업 ID가 전달 될 때 지정 된 업데이트 작업의 상태를 가져옵니다. |
| Get-HcsVirtualNetwork                         |             | Y                   | 장치에서 만든 스위치와 연결 된 가상 네트워크 및 서브넷을 식별 합니다.   |
| Get-HcsVirtualSwitch                          | Y           | Y                   | 지정 된 네트워크 인터페이스와 연결 된 가상 스위치를 가져옵니다.  |
| Get-VMInGuestLogs                             |             | Y                   | 장치에서 실패 한 Vm에 대 한 게스트 내 로그를 수집 합니다.  |
| Invoke-AzureDataBoxEdgeRoleReconcile          |             | Y                   | Kubernetes 클러스터 구성을 최신 상태로 만드는 데 사용 됩니다. |
| Invoke-AzureDataBoxEdgeRoleReconfigure        |             | Y                   | Kubernetes 클러스터의 구성을 변경 하는 데 사용 됩니다.  |
| Remove-HcsIPAddressPool                       |             |                     | Kubernetes VM Mac 주소 풀을 제거 합니다. |
| Remove-HcsKubeClusterNetworkInfo              |             | Y                   | Kubernetes 서비스 서브넷 또는 pod를 변경 합니다.|
| Remove-HcsKubernetesAzureArcAgent             |             | Y                   | 장치의 Kubernetes 클러스터에서 Azure Arc 에이전트를 제거 합니다.  |
| Remove-HcsKubernetesAzureMonitorConfiguration |             | Y                   | 장치의 Kubernetes 클러스터에서 Azure Monitor를 제거 하 고 Kubernetes 클러스터에서 컨테이너 로그 및 프로세서 메트릭을 수집할 수 있습니다.  |
| Remove-HcsKubernetesContainerRegistry         |             | Y                   | 장치에서 edge 컨테이너 레지스트리를 삭제 합니다.   |
| Remove-HcsKubernetesNamespace                 |             | Y                   | 지정 된 Kubernetes 네임 스페이스를 삭제 합니다. |
| Remove-HcsMacAddressPool                      |             | Y                   | Kubernetes VM Mac 주소 풀을 제거 합니다.|
| Remove-HcsNetRoute                            |             | Y                   | 장치에 추가한 경로 구성을 제거 합니다. |
| Remove-HcsVirtualNetwork                      |             | Y                   | 장치에서 만든 스위치와 연결 된 가상 네트워크 및 서브넷을 제거 합니다. |
| Remove-HcsVirtualSwitch                       |             | Y                   | 장치의 포트와 연결 된 가상 스위치를 제거 합니다.  |
| Restart-HcsNode                               |             | Y                   | 장치에서 노드를 다시 시작 합니다. 단일 노드 장치의 경우 장치를 다시 시작 하 고 가동 중지 시간이 발생 합니다. |
| Set-AzureDataBoxEdgeRoleCompute               |             | Y                   | Azure Portal를 통해 계산을 구성 하 고 Kubernetes 클러스터를 만들고 구성 하는 데 사용 됩니다.   |
| Set-HcsCertificate                            |             | Y                   | 에서 사용자 고유의 인증서를 가져올 수 있습니다. |
| Set-HcsClusterLevelSecurity                   |             | Y                   | 클러스터 트래픽의 보안 수준을 구성 합니다. 여기에는 노드 간 클러스터 트래픽 및 CSV (클러스터 공유 볼륨)의 트래픽이 포함 됩니다.    |
| Set-HcsClusterWitness                         |             | Y                   | Windows 클러스터 감시를 만들거나 구성 합니다. 클러스터 감시는 노드가 2 개 노드 장치에서 중단 될 때 쿼럼을 설정 하는 데 도움이 됩니다.     |
| Set-HcsEastWestCsvEncryption                  |             | Y                   | <!--can be removed, confirmed with Vibha/Deepan. Talk to Phani. -->  |
| Set-HcsExternalVirtualSwitch                  |             | Y                   | 장치에서 계산을 사용 하도록 설정한 포트에서 외부 가상 스위치를 구성 합니다.  |
| Set-HcsIPAddress                              |             | Y                   | 네트워크 인터페이스 속성을 업데이트 합니다.                 |
| Set-HcsIPAddressPool                          |             |                     | Kubernetes VM Mac 주소 풀을 설정 합니다.       |
| Set-HcsKubeClusterNetworkInfo                 |             | Y                   | 장치의 Azure Portal에서 계산을 구성 하기 전에 Kubernetes pod 및 서비스 서브넷을 변경 합니다.                                                                                  |
| Set-HcsKubernetesAzureArcAgent                |             | Y                   | 장치에 Arc 에이전트를 설치 하 여 Kubernetes 클러스터에서 Azure Arc를 사용 하도록 설정 합니다. |
| Set-HcsKubernetesAzureArcDataController       |             | Y                   | 장치에서 Kubernetes 클러스터에 데이터 컨트롤러를 만듭니다. |
| Set-HcsKubernetesAzureMonitorConfiguration    |             | Y                   | 장치에서 Kubernetes 클러스터에 대 한 Azure Monitor를 사용 하도록 설정 하 고 Kubernetes 클러스터에서 컨테이너 로그 및 프로세서 메트릭을 수집할 수 있습니다.|
| Set-HcsKubernetesContainerRegistry            |             | Y                   | 장치에 대 한 추가 기능으로 edge 컨테이너 레지스트리를 사용 하도록 설정 합니다.                                                                                                                                  |
| Set-HcsMacAddressPool                         |             | Y                   | Kubernetes VM Mac 주소 풀을 설정 합니다. |
| Set-HcsNetBmcInterface                        |             | Y                   | BMC에 대해 DHCP 구성을 사용 하거나 사용 하지 않도록 설정 합니다. |
| Set-HcsNetInterface                           |             | Y                   | Azure Stack Edge 장치의 네트워크 인터페이스에 대 한 IP 주소, 서브넷 마스크 및 게이트웨이를 구성 합니다.|
| Set-HcsSmbServerEncryptionConfiguration       |             | Y                   | <!--to be removed, Ernie confirmed--> |
| Set-HcsSmbSigningConfiguration                |             | Y                   | <!--to be removed, Ernie confirmed--> |
| Set-HcsUpdateConfiguration                    |             | Y                   | 서버 유형 및 서버에 대 한 URI 경로를 포함 하 여 장치에 대 한 업데이트 서버 설정을 설정 합니다. Microsoft 업데이트 서버 또는 WSUS (Windows Server Update Services) 서버에서 업데이트를 설치 하도록 선택할 수 있습니다.   |
| Set-HcsVirtualNetwork                         |             | Y                   |장치에서 만든 스위치와 연결 된 가상 네트워크 및 서브넷을 만듭니다. |
| Set-HcsVpnS2SInterface                        |             | Y                   | <!--VPN should be P2S-->  |
| Start-HcsGpuMPS                               |             | Y                   | 장치에서 MP (다중 프로세서 서비스)를 사용 하도록 설정 합니다.|
| Start-HcsSupportPackageUploadJob              | Y           | Y                   | 모든 로그가 포함 된 지원 패키지를 수집 하 고 패키지를 업로드 하 여 Microsoft에서 장치 관련 문제를 디버그 하는 데 사용할 수 있도록 합니다.|
| Start-HcsUpdateJob                            |             | Y                   | 업데이트 작업을 시작 합니다.|
| Stop-HcsGpuMPS                                |             | Y                   | 장치에서 MP (다중 프로세서 서비스)를 사용 하지 않도록 설정 합니다. |
| Test-HcsKubernetesStatus                      | Y           | Y                   | Kubernetes 진단 컨테이너를 실행 합니다. |

#### <a name="generic-network-cmdlets"></a>일반 네트워크 cmdlet

| Cmdlet   | 진단 | 진단 & 복구 | 설명                                                                                                                            |
|---------------------------|-------------|----------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| Find-NetRoute             | Y           | Y                    | 가장 적절 한 로컬 IP 주소와 원격 주소에 도달할 최적의 경로를 찾습니다.                                                          |
| Get-NetAdapter            | Y           | Y                    | 기본 네트워크 어댑터 속성을 가져옵니다.                                                                                             |
| Get-NetIPAddress          | Y           | Y                    | 컴퓨터의 전체 IP 주소 구성을 가져옵니다.                                                                             |
| Get-NetNat                | Y           | Y                    | 컴퓨터에 구성 된 NAT (네트워크 주소 변환) 개체를 가져옵니다.                                                               |
| Get-NetNatExternalAddress | Y           | Y                    | NAT (network address translation) 인스턴스에 구성 된 외부 주소 목록을 가져옵니다.                                          |
| Get-NetRoute              | Y           | Y                    | 대상 네트워크 접두사, 다음 홉 IP 주소 및 경로 메트릭을 포함 하 여 IP 라우팅 테이블에서 IP 경로 정보를 가져옵니다. |
| Get-NetCompartment        | Y           | Y                    | 프로토콜 스택의 네트워크 구획을 가져옵니다.                                                                                        |
| Get-NetNeighbor           | Y           | Y                    | 인접 캐시 항목을 가져옵니다.                                                                                                           |
| Get-NetAdapterSriov       | Y           | Y                    | 네트워크 어댑터의 SR-IOV 속성을 가져옵니다.                                                                                     |
| Resolve-DnsName           |             | Y                    | 지정 된 이름에 대 한 DNS 이름 쿼리 확인을 수행 합니다.                                                                           |


####  <a name="multi-access-edge-computing-mec-cmdlets"></a>MEC (다중 액세스에 지 컴퓨팅) cmdlet

| Cmdlet | 진단 | 진단 & 복구 | 설명                                                                                                       |
|-------------------------------------------|-------------|----------------------|-------------------------------------------------------------------------------------------------------------------|
| Get-MecVnf                                | Y           | Y                    | Azure Stack Edge에 배포 된 모든 다중 액세스에 지 계산 가상 네트워크 함수 Vm의 목록을 가져옵니다.       |
| Get-MecVirtualMachine                     | Y           | Y                    | 다중 액세스에 지 계산 가상 네트워크 기능을 사용 하 여 만든 가상 컴퓨터의 목록을 가져옵니다. 함수.                    |
| Get-MecServiceConfig                      | Y           | Y                    | 가상 네트워크 기능에 영향을 주는 다중 액세스 에지 컴퓨팅 서비스 구성을 가져옵니다. 수명 주기 워크플로. |
| Get-MecInformation                        | Y           | Y                    | 다중 액세스 에지 컴퓨팅 정보를 가져옵니다. 예를 들어 Azure Stack Edge 디바이스가 Azure 네트워크 함수 관리자에 등록되었는지 여부입니다.

####  <a name="general-purpose-os-cmdlets"></a>범용 OS cmdlet

| Cmdlet | 진단 | 진단 & 복구 | 설명                                                                                                                  |
|----------------------------|-------------|----------------------|------------------------------------------------------------------------------------------------------------------------------|
| Select-String              | Y           | Y                    | 문자열 및 파일에서 텍스트를 찾습니다.                                                                                             |
| Write-Progress             | Y           | Y                    | PowerShell 명령 창 내에 진행률 표시줄을 표시합니다.                                                                  |
| Get-Command                | Y           | Y                    | 세션에서 실행할 수 있는 명령 목록을 가져옵니다.                                                                        |
| Measure-Object             | Y           | Y                    | 개체의 숫자 속성과 텍스트 파일 등 문자열 개체의 문자, 단어 및 줄을 계산합니다. |
| Select-Object              | Y           | Y                    | 개체 또는 개체 속성을 선택합니다.                                                                                        |
| Out-Default                |             | Y                    | 출력을 기본 포맷터와 기본 출력 cmdlet으로 보냅니다.                                                  |
| Get-WinEvent               | Y           | Y                    | 이벤트 로그 및 이벤트 추적 로그 파일에서 이벤트를 가져옵니다.                                                                     |
| Get-Counter                | Y           | Y                    | 성능 카운터 데이터를 가져옵니다.                                                                                               |
| Get-Volume                 | Y           | Y                    | 필터가 제공되지 않은 경우 지정된 볼륨 개체 또는 모든 볼륨 개체를 가져옵니다.                                            |
| Get-Service                | Y           | Y                    | 서비스를 나타내는 개체를 가져옵니다.                                                                                    |

#### <a name="cluster-cmdlets"></a>클러스터 cmdlet 

| Cmdlet       | 진단 | 진단 & 복구 | 설명                                                                            |
|-----------------------|-------------|----------------------|----------------------------------------------------------------------------------------|
| Get-ClusterResource   | Y           | Y                    | 장애 조치(failover) 클러스터에 있는 하나 이상의 리소스에 대한 정보를 가져옵니다.                    |
| Get-Cluster           | Y           | Y                    | 장애 조치(failover) 클러스터에 대한 정보를 가져옵니다.                                               |
| Get-ClusterNode       | Y           | Y                    | 장애 조치(failover) 클러스터에 있는 하나 이상의 노드 또는 서버에 대한 정보를 가져옵니다.           |
| Start-Cluster         |             | Y                    | 아직 시작되지 않은 클러스터의 모든 노드에서 클러스터 서비스 시작합니다. |
| Start-ClusterResource |             | Y                    | 장애 조치(failover) 클러스터에서 리소스를 온라인으로 전환합니다.                                        |
| Stop-ClusterResource  |             | Y                    | 장애 조치(failover) 클러스터에서 리소스를 오프라인으로 전환합니다.                                        |


#### <a name="hyper-v-cmdlets"></a>Hyper-V cmdlet

| Cmdlet      | 진단 | 진단 & 복구 | 설명                                                                                                                                             |
|----------------------|-------------|----------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------|
| Get-Vm               | Y           | Y                    | 컴퓨터의 가상 머신을 가져옵니다.                                                                                                              |
| Get-VMNetworkAdapter | Y           | Y                    | 가상 머신, 스냅샷, 관리 운영 체제 또는 가상 머신 및 관리 운영 체제의 가상 네트워크 어댑터를 가져옵니다. |
| Get-VMHardDiskDrive  | Y           | Y                    | 하나 이상의 가상 컴퓨터에 연결된 가상 하드 디스크 드라이브를 가져옵니다.                                                                             |
| Get-VMSwitch         | Y           | Y                    |  가상 스위치의 목록을 가져옵니다.                                                                                                                     |

## <a name="next-steps"></a>다음 단계

[Microsoft 지원에 문의 하세요](azure-stack-edge-contact-microsoft-support.md).
