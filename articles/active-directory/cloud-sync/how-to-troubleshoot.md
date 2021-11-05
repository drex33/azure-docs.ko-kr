---
title: Azure AD Connect 클라우드 동기화 관련 문제 해결
description: 이 문서에서는 클라우드 프로비저닝 에이전트에서 발생할 수 있는 문제를 해결하는 방법을 설명합니다.
author: billmath
ms.author: billmath
manager: daveba
ms.date: 01/19/2021
ms.topic: how-to
ms.prod: windows-server-threshold
ms.technology: identity-adfs
ms.openlocfilehash: 863d043bc3185b5fd7f44056ba13bca5ed700f30
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131018246"
---
# <a name="cloud-sync-troubleshooting"></a>클라우드 동기화 관련 문제 해결

클라우드 동기화는 여러 가지에 영향을 미치며 다양한 종속성을 포함합니다. 따라서 광범위한 범위에서 다양한 문제가 발생할 수 있습니다. 이 문서는 해당 문제를 해결하는 데 도움이 됩니다. 여기서는 중점적으로 살펴볼 일반적인 영역, 추가 정보를 수집하는 방법 및 문제를 추적하는 데 사용할 수 있는 다양한 기술을 소개합니다.


## <a name="common-troubleshooting-areas"></a>일반적인 문제 해결 영역

|속성|설명|
|-----|-----|
|[에이전트 문제](#agent-problems)|에이전트가 올바르게 설치되었는지와 Azure AD(Azure Active Directory)와 통신하는지 확인합니다.|
|[개체 동기화 문제](#object-synchronization-problems)|프로비저닝 로그를 사용하여 개체 동기화 문제를 해결합니다.|
|[프로비저닝 격리 문제](#provisioning-quarantined-problems)|프로비저닝 격리 문제와 해결 방법을 이해합니다.|


## <a name="agent-problems"></a>에이전트 문제
에이전트를 사용하여 먼저 확인해야 할 사항은 다음과 같습니다.

-  설치 여부
-  에이전트가 로컬에서 실행되고 있는지 여부
-  에이전트가 포털에 있는지 여부
-  에이전트가 정상으로 표시되는지 여부

해당 항목은 Azure Portal과 에이전트를 실행하는 로컬 서버에서 수행됩니다.

### <a name="azure-portal-agent-verification"></a>Azure Portal에서 에이전트 확인

에이전트가 Azure에 표시되고 정상인지 확인하려면 다음 단계를 수행합니다.

1. Azure Portal에 로그인합니다.
1. 왼쪽에서 **Azure Active Directory** > **Azure AD Connect** 를 선택합니다. 중앙에서 **동기화 관리** 를 선택합니다.
1. **Azure AD Connect 클라우드 동기화** 화면에서 **모든 에이전트 검토** 를 클릭합니다.

   ![모든 에이전트 검토](media/how-to-install/install-7.png)</br>
 
1. **온-프레미스 프로비저닝 에이전트** 화면에 설치한 에이전트가 표시됩니다. 해당 에이전트가 여기에 있고 *정상* 으로 표시되는지 확인합니다.

   ![온-프레미스 프로비저닝 에이전트 화면](media/how-to-install/install-8.png)</br>

### <a name="verify-the-port"></a>포트 확인

Azure가 포트 443에서 수신 대기 중이고 에이전트와 통신할 수 있는지 확인합니다. 

이 테스트는 에이전트가 포트 443을 통해 Azure와 통신할 수 있는지 확인합니다. 브라우저를 열고 에이전트가 설치된 서버에서 이전 URL로 이동합니다.

![포트 연결 가능성 확인](media/how-to-install/verify-2.png)

### <a name="on-the-local-server"></a>로컬 서버에서 에이전트 확인

에이전트가 실행되는지 확인하려면 다음 단계를 수행합니다.

1. 에이전트가 설치된 서버에서 서비스를 탐색하거나 **시작** > **실행** > **Services.msc** 로 이동하여 **서비스** 를 엽니다.
1. **서비스** 아래에서 **Microsoft Azure AD Connect 에이전트 업데이트 프로그램** 및 **Microsoft Azure AD Connect Provisioning Agent** 가 있고 상태가 *실행 중* 인지 확인합니다.

   ![서비스 화면](media/how-to-troubleshoot/troubleshoot-1.png)

### <a name="common-agent-installation-problems"></a>일반적인 에이전트 설치 문제

다음 섹션에서는 몇 가지 일반적인 에이전트 설치 문제와 일반적인 해결 방법에 대해 설명합니다.

#### <a name="agent-failed-to-start"></a>에이전트를 시작하지 못함

다음과 같은 오류 메시지가 표시될 수 있습니다.

**'Microsoft Azure AD Connect Provisioning Agent' 서비스를 시작하지 못했습니다. 시스템 서비스를 시작할 수 있는 충분한 권한이 있는지 확인하세요.** 

이 문제는 일반적으로 설치 관리자(NT SERVICE\AADConnectProvisioningAgent)에서 만든 로컬 NT 서비스 로그온 계정에 권한이 적용되지 않도록 하는 그룹 정책으로 인해 발생합니다. 이러한 사용 권한은 서비스를 시작하는 데 필요합니다.

이 문제를 해결하려면 다음 단계를 수행합니다.

1. 관리자 계정으로 서버에 로그인합니다.
1. **서비스** 를 탐색하거나 **시작** > **실행** > **Services.msc** 로 이동하여 서비스를 엽니다.
1. **서비스** 에서 **Microsoft Azure AD Connect Provisioning Agent** 를 두 번 클릭합니다.
1. **로그온** 탭에서 **이 계정** 을 도메인 관리자로 변경한 다음 서비스를 다시 시작합니다. 

   ![로그온 탭](media/how-to-troubleshoot/troubleshoot-3.png)

#### <a name="agent-times-out-or-certificate-is-invalid"></a>에이전트 시간이 초과하거나 인증서가 잘못됨

에이전트를 등록하려고 시도할 때 다음과 같은 오류 메시지가 나타날 수 있습니다.

![시간 제한 오류 메시지](media/how-to-troubleshoot/troubleshoot-4.png)

이 문제는 일반적으로 에이전트가 하이브리드 ID 서비스에 연결할 수 없기 때문에 발생하며 HTTP 프록시를 구성해야 합니다. 이 문제를 해결하려면 아웃바운드 프록시를 구성합니다. 

프로비저닝 에이전트는 아웃바운드 프록시 사용을 지원합니다. 에이전트 구성 파일(*C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\AADConnectProvisioningAgent.exe.config*)을 편집하여 구성할 수 있습니다. 해당 파일의 끝 부분에서 닫는 `</configuration>` 태그 바로 앞에 다음 행을 추가합니다.
`[proxy-server]` 및 `[proxy-port]` 변수를 프록시 서버 이름 및 포트 값으로 바꿉니다.

```xml
    <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
            <proxy
                usesystemdefault="true"
                proxyaddress="http://[proxy-server]:[proxy-port]"
                bypassonlocal="true"
            />
        </defaultProxy>
    </system.net>
```

#### <a name="agent-registration-fails-with-security-error"></a>보안 오류로 인해 에이전트를 등록하지 못함

클라우드 프로비저닝 에이전트를 설치할 때 오류 메시지가 나타날 수 있습니다.

이 문제는 일반적으로 에이전트가 로컬 PowerShell 실행 정책으로 인해 PowerShell 등록 스크립트를 실행할 수 없기 때문에 발생합니다.

이 문제를 해결하려면 서버에서 PowerShell 실행 정책을 변경합니다. 머신 및 사용자 정책이 *Undefined* 또는 *RemoteSigned* 로 설정되어 있어야 합니다. *Unrestricted* 으로 설정된 경우 이 오류가 표시됩니다. 자세한 내용은 [PowerShell 실행 정책](/powershell/module/microsoft.powershell.core/about/about_execution_policies)을 참조하세요. 

### <a name="log-files"></a>로그 파일

기본적으로 에이전트는 최소한의 오류 메시지 및 스택 추적 정보를 내보냅니다. 해당 추적 로그는 **C:\ProgramData\Microsoft\Azure AD Connect Provisioning Agent\Trace** 폴더에서 확인할 수 있습니다.

에이전트 관련 문제를 해결하는 데 필요한 추가 정보를 수집하려면 다음 단계를 수행합니다.

1.  [여기](reference-powershell.md#install-the-aadcloudsynctools-powershell-module)에 설명된 대로 AADCloudSyncTools PowerShell 모듈을 설치합니다.
2. `Export-AADCloudSyncToolsLogs` PowerShell cmdlet을 사용하여 정보를 캡처합니다.  다음 스위치를 사용하여 데이터 컬렉션을 세밀하게 조정할 수 있습니다.
      - SkipVerboseTrace(기본값 = false): 자세한 정보 로그를 캡처하지 않고 현재 로그만 내보내려는 경우
      - TracingDurationMins(기본값 = 3분): 다른 캡처 기간을 지정하려는 경우
      - OutputPath(기본값 = 사용자 문서): 다른 출력 경로를 지정하려는 경우


## <a name="object-synchronization-problems"></a>개체 동기화 문제

다음 섹션에는 개체 동기화 문제 해결에 대한 정보가 포함되어 있습니다.

### <a name="provisioning-logs"></a>프로비저닝 로그

Azure Portal에서 프로비저닝 로그를 사용하여 개체 동기화 문제를 추적하고 해결할 수 있습니다. 로그를 보려면 **로그** 를 선택합니다.

![로그 단추](media/how-to-troubleshoot/log-1.png)

프로비저닝 로그는 온-프레미스 Active Directory 환경과 Azure 간에 동기화되는 개체의 상태에 대한 다양한 정보를 제공합니다.

![프로비저닝 로그 화면](media/how-to-troubleshoot/log-2.png)

페이지 상단의 드롭다운 상자를 사용하여 날짜와 같은 특정 문제에 대한 보기를 0으로 필터링할 수 있습니다. 개별 이벤트를 두 번 클릭하면 추가 정보가 표시됩니다.

![프로비저닝 로그 드롭다운 상자 정보](media/how-to-troubleshoot/log-3.png)

이 정보는 자세한 단계와 동기화 문제가 발생한 위치를 제공합니다. 이렇게 하면 문제가 발생한 지점을 정확하게 파악할 수 있습니다.


## <a name="provisioning-quarantined-problems"></a>프로비저닝 격리 문제

클라우드 동기화는 구성의 상태를 모니터링하고 비정상 개체를 격리 상태에 배치합니다. 오류(예: 잘못된 관리자 자격 증명)로 인해 대상 시스템에 대해 수행된 대부분 또는 모든 호출이 일관되게 실패하는 경우 동기화 작업은 격리 상태로 표시됩니다.

![격리 상태](media/how-to-troubleshoot/quarantine-1.png)

상태를 선택하여 격리에 대한 추가 정보를 볼 수 있습니다. 오류 코드 및 메시지를 가져올 수도 있습니다.

![격리에 대한 추가 정보를 보여 주는 스크린샷.](media/how-to-troubleshoot/quarantine-2.png)

상태를 마우스 오른쪽 단추로 클릭하면 추가 옵션이 표시됩니다.

- 프로비저닝 로그 보기
- 에이전트 보기
- 격리 지우기

![마우스 오른쪽 단추 메뉴 옵션을 보여 주는 스크린샷.](media/how-to-troubleshoot/quarantine-4.png)

### <a name="resolve-a-quarantine"></a>격리 해결

격리를 해결하는 두 가지 다른 방법이 있습니다. 아래에 이 계정과 키의 예제가 나와 있습니다.

- 격리 지우기 - 워터마크를 지우고 델타 동기화를 실행합니다.
- 프로비저닝 작업 다시 시작 - 워터마크를 지우고 초기 동기화를 실행합니다.

#### <a name="clear-quarantine"></a>격리 지우기

워터마크를 지우고 프로비저닝 작업에서 델타 동기화를 실행하려면 상태를 마우스 오른쪽 단추로 클릭하고 **격리 지우기** 를 선택합니다.

격리가 지워진다는 알림이 표시되어야 합니다.

![격리가 지워진다는 알림을 보여 주는 스크린샷.](media/how-to-troubleshoot/quarantine-5.png)

그러면 에이전트의 상태가 정상으로 표시되어야 합니다.

![격리 상태 정보](media/how-to-troubleshoot/quarantine-6.png)

#### <a name="restart-the-provisioning-job"></a>프로비저닝 작업 다시 시작

Azure Portal를 사용하여 프로비저닝 작업을 다시 시작합니다. 에이전트 구성 페이지에서 **프로비저닝 다시 시작** 을 선택합니다.

  ![프로비저닝 다시 시작](media/how-to-troubleshoot/quarantine-3.png)

- Microsoft Graph를 사용하여 [프로비저닝 작업을 다시 시작](/graph/api/synchronization-synchronizationjob-restart?tabs=http&view=graph-rest-beta&preserve-view=true)합니다. 다시 시작하는 작업을 완전히 제어할 수 있습니다. 다음을 지우도록 선택할 수 있습니다.

  - 에스크로: 격리 상태에 도달할 때까지 누적되는 에스크로 카운터를 다시 시작하려는 경우.
  - 격리: 격리에서 애플리케이션을 제거하려는 경우.
  - 워터마크. 
  
  다음 요청을 사용합니다.
 
  `POST /servicePrincipals/{id}/synchronization/jobs/{jobId}/restart`

## <a name="repairing-the-the-cloud-sync-service-account"></a>클라우드 동기화 서비스 계정 복구

클라우드 동기화 서비스 계정을 복구해야 하는 경우를 `Repair-AADCloudSyncToolsAccount`를 사용할 수 있습니다.

   1. [여기](reference-powershell.md#install-the-aadcloudsynctools-powershell-module)에 설명된 설치 단계를 사용하여 시작한 다음 나머지 단계를 계속 진행합니다.

   2. 관리자 권한으로 Windows PowerShell 세션에서 다음을 입력하거나 복사하여 붙여넣습니다.

      ```powershell
      Connect-AADCloudSyncTools
      ```

   3. Azure AD 전역 관리자 자격 증명을 입력합니다.

   4. 다음을 입력하거나 복사하여 붙여넣습니다.

      ```powershell
      Repair-AADCloudSyncToolsAccount
      ```

   5. 이 작업이 완료되면 계정이 성공적으로 복구된 것입니다.

## <a name="next-steps"></a>다음 단계 

- [알려진 제한 사항](how-to-prerequisites.md#known-limitations)
- [오류 코드](reference-error-codes.md)
