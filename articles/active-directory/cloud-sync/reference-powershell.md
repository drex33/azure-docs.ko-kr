---
title: Azure AD Connect 클라우드 동기화를 위한 AADCloudSyncTools PowerShell 모듈
description: 이 문서에서는 Azure AD Connect 클라우드 프로비저닝 에이전트를 설치하는 방법을 설명합니다.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/03/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: d45c331c8af6dda32b7a47dbdb517adf1d8a4d93
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131500301"
---
# <a name="aadcloudsynctools-powershell-module-for-azure-ad-connect-cloud-sync"></a>Azure AD Connect 클라우드 동기화를 위한 AADCloudSyncTools PowerShell 모듈

AADCloudSyncTools 모듈은 Azure AD Connect 클라우드 동기화 배포를 관리하는 데 사용할 수 있는 유용한 도구 집합을 제공합니다.

## <a name="prerequisites"></a>사전 요구 사항
필요한 필수 구성 요소는 다음과 같습니다.

- 이 모듈의 모든 필수 구성 요소는 `Install-AADCloudSyncToolsPrerequisites`를 사용하여 자동으로 설치할 수 있습니다.
- 이 모듈은 MSAL 인증을 사용하므로 MSAL.PS 모듈이 설치되어 있어야 합니다. PowerShell 창에서 확인하려면 `Get-module MSAL.PS -ListAvailable`을 실행합니다. 모듈이 올바르게 설치된 경우 응답을 받게 됩니다. `Install-AADCloudSyncToolsPrerequisites`를 사용하여 최신 버전의 MSAL.PS를 설치할 수 있습니다.
- AzureAD PowerShell 모듈은 이 모듈의 기능에 대한 필수 구성 요소는 아니지만, 있는 것이 유용함으로 `Install-AADCloudSyncToolsPrerequisites`를 사용할 때도 자동으로 설치됩니다. 
- PowerShell 갤러리에서 모듈을 설치하려면 TLS 1.2를 적용해야 합니다. Cmdlet `Install-AADCloudSyncToolsPrerequisites`는 모든 필수 구성 요소를 설치하기 전에 TLS 1.2 적용을 설정합니다. 모듈을 수동으로 설치할 수 있도록 하려면 `Install-Module`을 사용하기 전에 PowerShell 세션에서 다음을 설정합니다.
  ```
  [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12 
  ```


## <a name="install-the-aadcloudsynctools-powershell-module"></a>AADCloudSyncTools PowerShell 모듈 설치
AADCloudSyncTools 모듈을 설치하고 사용하려면 다음 단계를 수행합니다.

1. 관리자 권한으로 Windows PowerShell을 엽니다.
2. `Import-module -Name "C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\Utility\AADCloudSyncTools"`를 입력하거나 복사하여 붙여넣습니다.
3. Enter 키를 누릅니다.
4. 모듈을 가져왔는지 확인하려면 `Get-module AADCloudSyncTools`를 입력하거나 복사하여 붙여넣습니다.
5. 이제 모듈에 대한 정보가 표시됩니다.
6. 다음으로, AADCloudSyncTools 모듈 필수 구성 요소를 설치하려면 `Install-AADCloudSyncToolsPrerequisites`를 실행합니다.
7. 첫 번째 실행에서는 PoweShellGet 모듈이 없는 경우 설치됩니다. 새 PowershellGet 모듈을 로드하려면 PowerShell 창을 닫고 관리자 권한으로 새 PowerShell 세션을 엽니다. 
8. 2단계를 사용하여 모듈을 다시 가져옵니다.
9. `Install-AADCloudSyncToolsPrerequisites`를 실행하여 MSAL 및 AzureAD 모듈을 설치합니다.
11. 모든 필수 구성 요소가 성공적으로 설치되어야 합니다. ![모듈 설치](media/reference-powershell/install-1.png)
12. 새 PowerShell 세션에서 AADCloudSyncTools 모듈을 사용할 때마다 다음을 입력하거나 복사하여 붙여넣습니다.
```
Import-module "C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\Utility\AADCloudSyncTools"
```


## <a name="aadcloudsynctools--cmdlets"></a>AADCloudSyncTools Cmdlets
### <a name="connect-aadcloudsynctools"></a>Connect-AADCloudSyncTools
MSAL.PS 모듈을 사용하여 Azure AD 관리자가 Microsoft Graph에 액세스할 수 있는 토큰을 요청합니다. 


### <a name="export-aadcloudsynctoolslogs"></a>Export-AADCloudSyncToolsLogs
다음과 같이 모든 문제 해결 데이터를 압축 파일에 내보내고 패키지합니다.
 1. 자세한 추적을 설정하고 프로비저닝 에이전트에서 데이터 수집을 시작합니다(`Start-AADCloudSyncToolsVerboseLogs`와 같음).
 <br>이러한 추적 로그는 폴더 `C:\ProgramData\Microsoft\Azure AD Connect Provisioning Agent\Trace`에서 찾을 수 있습니다. </br>
 2. 3분 후에 데이터 수집을 중지하고 자세한 추적을 사용하지 않도록 설정합니다(`Stop-AADCloudSyncToolsVerboseLogs`와 같음).
 <br>`-TracingDurationMins`를 사용하여 다른 기간을 지정하거나 `-SkipVerboseTrace`를 사용하여 자세한 정보 추적을 건너뛸 수 있습니다. </br>
 3. 지난 24시간 동안 이벤트 뷰어 로그를 수집합니다.
 4. 모든 에이전트 로그, 자세한 정보 로그 및 이벤트 뷰어 로그를 사용자의 문서 폴더에 있는 압축된 zip 파일로 압축합니다.
 <br>`-OutputPath <folder path>`를 사용하여 다른 출력 폴더를 지정할 수 있습니다. </br>

### <a name="get-aadcloudsynctoolsinfo"></a>Get-AADCloudSyncToolsInfo
Azure AD Tenant 세부정보 및 내부 변수 상태를 표시합니다.

### <a name="get-aadcloudsynctoolsjob"></a>Get-AADCloudSyncToolsJob
그래프를 사용하여 AD2AAD 서비스 주체를 가져오고 동기화 작업 정보를 반환합니다.
특정 동기화 작업 ID를 매개 변수로 사용하여 호출할 수도 있습니다.

### <a name="get-aadcloudsynctoolsjobschedule"></a>Get-AADCloudSyncToolsJobSchedule
그래프를 사용하여 AD2AAD 서비스 주체를 가져오고 동기화 작업 일정을 반환합니다.
특정 동기화 작업 ID를 매개 변수로 사용하여 호출할 수도 있습니다.

### <a name="get-aadcloudsynctoolsjobschema"></a>Get-AADCloudSyncToolsJobSchema
그래프를 사용하여 AD2AAD 서비스 주체를 가져오고 동기화 작업 스키마를 반환합니다.

### <a name="get-aadcloudsynctoolsjobscope"></a>Get-AADCloudSyncToolsJobScope
그래프를 사용하여 제공된 동기화 작업 ID에 대한 동기화 작업 스키마를 가져오고 모든 필터 그룹의 범위를 출력합니다.

### <a name="get-aadcloudsynctoolsjobsettings"></a>Get-AADCloudSyncToolsJobSettings
그래프를 사용하여 AD2AAD 서비스 주체를 가져오고 동기화 작업 설정을 반환합니다.
특정 동기화 작업 ID를 매개 변수로 사용하여 호출할 수도 있습니다.

### <a name="get-aadcloudsynctoolsjobstatus"></a>Get-AADCloudSyncToolsJobStatus
그래프를 사용하여 AD2AAD 서비스 주체를 가져오고 동기화 작업 상태를 반환합니다.
특정 동기화 작업 ID를 매개 변수로 사용하여 호출할 수도 있습니다.

### <a name="get-aadcloudsynctoolsserviceprincipal"></a>Get-AADCloudSyncToolsServicePrincipal
그래프를 사용하여 AD2AAD 및/또는 SyncFabric에 대한 서비스 주체를 가져옵니다.
매개 변수가 없는 경우 AD2AAD는 서비스 주체만 반환합니다.

### <a name="install-aadcloudsynctoolsprerequisites"></a>Install-AADCloudSyncToolsPrerequisites
PowerShellGet v2.2.4.1 이상 및 Azure AD 및 MSAL.PS 모듈이 있는지 확인하고 누락된 경우 설치합니다.

### <a name="invoke-aadcloudsynctoolsgraphquery"></a>Invoke-AADCloudSyncToolsGraphQuery
매개 변수로 지정된 URI, 메서드 및 본문에 대한 웹 요청을 호출합니다.

### <a name="repair-aadcloudsynctoolsaccount"></a>Repair-AADCloudSyncToolsAccount
Azure AD PowerShell을 사용하여 현재 계정(있는 경우)을 삭제하고 Azure AD의 새 동기화 계정으로 동기화 계정 인증을 재설정합니다.

### <a name="restart-aadcloudsynctoolsjob"></a>Restart-AADCloudSyncToolsJob
전체 동기화를 다시 시작합니다.

### <a name="resume-aadcloudsynctoolsjob"></a>Resume-AADCloudSyncToolsJob
이전 워터마크에서 동기화를 계속합니다.

### <a name="start-aadcloudsynctoolsverboselogs"></a>Start-AADCloudSyncToolsVerboseLogs
자세한 정보 추적을 사용하도록 설정하고 AADConnectProvisioningAgent 서비스를 다시 시작하도록 'AADConnectProvisioningAgent.exe.config'를 수정합니다. -SkipServiceRestart를 사용하여 서비스 재시작을 방지할 수 있지만 구성 변경 내용은 적용되지 않습니다.  이러한 추적 로그는 C:\ProgramData\Microsoft\Azure AD Connect Provisioning Agent\Trace 폴더에서 확인할 수 있습니다.

### <a name="stop-aadcloudsynctoolsverboselogs"></a>Stop-AADCloudSyncToolsVerboseLogs
자세한 정보 추적을 사용하지 않도록 설정하고 AADConnectProvisioningAgent 서비스를 다시 시작하도록 'AADConnectProvisioningAgent.exe.config'를 수정합니다. -SkipServiceRestart를 사용하여 서비스 재시작을 방지할 수 있지만 구성 변경 내용은 적용되지 않습니다.

### <a name="suspend-aadcloudsynctoolsjob"></a>Suspend-AADCloudSyncToolsJob
동기화를 일시 중지합니다.

## <a name="next-steps"></a>다음 단계 

- [프로비저닝이란?](what-is-provisioning.md)
- [Azure AD Connect 클라우드 동기화란?](what-is-cloud-sync.md)
