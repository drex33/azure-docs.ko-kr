---
title: Azure Virtual Desktop 에이전트 문제 해결 - Azure
description: 일반적인 에이전트 및 연결 문제를 해결하는 방법입니다.
author: Sefriend
ms.topic: troubleshooting
ms.date: 12/16/2020
ms.author: sefriend
manager: clarkn
ms.openlocfilehash: 3bb81f3fd29e68e04607e4f0252ccd8f0308dc67
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131471804"
---
# <a name="troubleshoot-common-azure-virtual-desktop-agent-issues"></a>일반적인 Azure Virtual Desktop 에이전트 문제 해결

Azure Virtual Desktop 에이전트는 다음과 같은 여러 요인으로 인해 연결 문제가 발생할 수 있습니다.
   - 에이전트가 서비스를 중지하게 만드는 브로커의 오류
   - 업데이트 관련 문제
   - 에이전트 설치 중 설치 관련 문제(세션 호스트에 대한 연결이 중단됨)

이 문서에서는 이러한 일반적인 시나리오에 대한 솔루션과 연결 문제를 해결하는 방법을 안내합니다.

>[!NOTE]
>세션 연결 및 Azure Virtual Desktop 에이전트와 관련된 문제를 해결하려면 **이벤트 뷰어** > **windows 로그** > **애플리케이션** 에서 이벤트 로그를 검토하는 것이 좋습니다. 다음 원본 중 하나가 있는 이벤트를 찾아 문제를 확인하세요.
>
>- WVD-Agent
>- WVD-Agent-Updater
>- RDAgentBootLoader
>- MsiInstaller

## <a name="error-the-rdagentbootloader-andor-remote-desktop-agent-loader-has-stopped-running"></a>오류: RDAgentBootLoader 및/또는 원격 데스크톱 에이전트 로더 실행이 중지되었습니다.

다음과 같은 문제가 발생하면, 에이전트를 로드하는 부팅 로더가 에이전트를 제대로 설치할 수 없고 에이전트 서비스가 실행되고 있지 않음을 의미합니다.
- **RDAgentBootLoader** 가 중지되었거나 실행되고 있지 않습니다.
- **원격 데스크톱 에이전트 로더** 의 상태가 없습니다.

이 문제를 해결하려면 RDAgent 부팅 로더를 시작합니다.

1. 서비스 창에서 **원격 데스크톱 에이전트 로더** 를 마우스 오른쪽 단추로 클릭합니다.
2. **시작** 을 선택합니다. 이 옵션이 회색으로 표시되면 관리자 권한이 없는 것이며 서비스를 시작하려면 해당 권한을 부여 받아야 합니다.
3. 10초를 기다린 후 **원격 데스크톱 에이전트 로더** 를 마우스 오른쪽 단추로 클릭합니다.
4. **새로 고침** 을 선택합니다.
5. 서비스를 시작하고 새로 고친 후 서비스가 중지되면 등록에 실패에 실패할 수 있습니다. 자세한 내용은 [INVALID_REGISTRATION_TOKEN](#error-invalid_registration_token)을 참조하세요.

## <a name="error-invalid_registration_token"></a>오류: INVALID_REGISTRATION_TOKEN

**이벤트 뷰어** > **windows 로그** > **애플리케이션** 으로 이동 합니다. 설명에 **INVALID_REGISTRATION_TOKEN** 이 표시된 ID가 3277인 이벤트가 보이면 보유한 등록 토큰이 유효한 것으로 인식되지 않은 것입니다

이 문제를 해결하려면 유효한 등록 토큰을 만듭니다.

1. 새 등록 토큰을 만들려면 [VM에 대한 새 등록 키 생성](#step-3-generate-a-new-registration-key-for-the-vm) 섹션의 단계를 따르세요.
2. 레지스트리 편집기를 엽니다. 
3. **HKEY_LOCAL_MACHINE** > **SOFTWARE** > **Microsoft** > **RDInfraAgent** 로 이동합니다.
4. **IsRegistered** 를 선택합니다. 
5. **값 데이터:** 입력 상자에 **0** 을 입력하고 **확인** 을 선택합니다. 
6. **RegistrationToken** 을 선택합니다. 
7. **값 데이터:** 입력 상자에 1단계의 등록 토큰을 붙여넣습니다. 

   > [!div class="mx-imgBorder"]
   > ![IsRegistered 0의 스크린샷](media/isregistered-token.png)

8. 관리자 권한으로 명령 프롬프트를 엽니다.
9. **net stop RDAgentBootLoader** 를 입력합니다. 
10. **net start RDAgentBootLoader** 를 입력합니다. 
11. 레지스트리 편집기를 엽니다.
12. **HKEY_LOCAL_MACHINE** > **SOFTWARE** > **Microsoft** > **RDInfraAgent** 로 이동합니다.
13. **IsRegistered** 가 1로 설정되어 있고, **RegistrationToken** 의 데이터 열에 아무 것도 없는지 확인합니다. 

   > [!div class="mx-imgBorder"]
   > ![IsRegistered 1의 스크린샷](media/isregistered-registry.png)

## <a name="error-agent-cannot-connect-to-broker-with-invalid_form"></a>오류: 에이전트가 INVALID_FORM으로 broker에 연결할 수 없음

**이벤트 뷰어** > **windows 로그** > **애플리케이션** 으로 이동 합니다. 설명에 "INVALID_FORM"이라고 표시된 ID가 3277인 이벤트가 보이면 에이전트와 broker 간의 통신에 문제가 있는 것입니다. 특정 방화벽 또는 DNS 설정 때문에 에이전트가 broker에 연결하거나 특정 URL에 연결할 수 없습니다.

이 문제를 해결하려면 BrokerURI 및 BrokerURIGlobal에 도달할 수 있는지 확인합니다.
1. 레지스트리 편집기를 엽니다. 
2. **HKEY_LOCAL_MACHINE** > **SOFTWARE** > **Microsoft** > **RDInfraAgent** 로 이동합니다. 
3. **BrokerURI** 및 **BrokerURIGlobal** 값을 기록해 둡니다.

   > [!div class="mx-imgBorder"]
   > ![broker URI 및 broker URI 글로벌 스크린샷](media/broker-uri.png)

 
4. 웹 브라우저를 열고 *\<BrokerURI\>api/health* 로 이동합니다. 
   - **BrokerURI** 에 3단계의 값을 사용하는지 확인합니다. 이 섹션의 예에서는 <https://rdbroker-g-us-r0.wvd.microsoft.com/api/health>입니다.
5. 브라우저에서 다른 탭을 열고 *\<BrokerURIGlobal\>api/health* 로 이동합니다. 
   - **BrokerURIGlobal** 링크에 3단계의 값을 사용하는지 확인합니다. 이 섹션의 예에서는 <https://rdbroker.wvd.microsoft.com/api/health>입니다.
6. 네트워크에서 broker 연결이 차단되고 있지 않으면 두 페이지가 모두 로드되고 **"RD Broker is Healthy"** 라는 메시지가 표시됩니다. 다음 스크린샷을 참조하세요. 

   > [!div class="mx-imgBorder"]
   > ![로드에 성공한 broker URI 액세스의 스크린샷](media/broker-uri-web.png)

   > [!div class="mx-imgBorder"]
   > ![로드에 성공한 broker 글로벌 URI 액세스의 스크린샷](media/broker-global.png)
 

7. 네트워크에서 broker 연결을 차단하는 경우에는 페이지가 로드되지 않습니다. 다음 스크린샷을 참조하세요. 

   > [!div class="mx-imgBorder"]
   > ![로드에 성공하지 못한 broker 액세스의 스크린샷](media/unsuccessful-broker-uri.png)

   > [!div class="mx-imgBorder"]
   > ![로드에 성공하지 못한 broker 글로벌 액세스의 스크린샷](media/unsuccessful-broker-global.png)

8. 네트워크에서 이러한 URL을 차단하는 경우 필요한 URL을 차단 해제해야 합니다. 자세한 내용은 [필수 URL 목록](safe-url-list.md)를 참조하세요.
9. 그래도 문제가 해결되지 않으면 에이전트에서 broker로 연결하는 것을 차단하는 암호화를 사용하는 그룹 정책이 없는지 확인합니다. Azure Virtual Desktop은 [Azure Front Door](../frontdoor/concept-end-to-end-tls.md#supported-cipher-suites)와 동일한 TLS 1.2 암호화를 사용합니다. 자세한 내용은 [연결 보안](network-connectivity.md#connection-security)을 참조하세요.

## <a name="error-3703"></a>오류: 3703

**이벤트 뷰어** > **windows 로그** > **애플리케이션** 으로 이동 합니다. 설명에 "RD Gateway Url: is not accessible"이라고 표시된 ID가 3703인 이벤트가 보이면 에이전트가 게이트웨이 URL에 도달할 수 없는 것입니다. 세션 호스트에 성공적으로 연결하고 이러한 엔드포인트에 대한 네트워크 트래픽이 제한을 우회하도록 허용하려면 [필수 URL 목록](safe-url-list.md)의 URL을 차단 해제해야 합니다. 또한 방화벽 또는 프록시 설정이 이러한 URL을 차단하지 않는지 확인합니다. Azure Virtual Desktop을 사용하려면 이러한 URL 차단을 해제해야 합니다.

이 문제를 해결하려면 방화벽 및/또는 DNS 설정이 다음 URL을 차단하지 않는지 확인합니다.
1. [Azure Firewall을 사용하여 Azure Virtual Desktop 배포를 보호합니다](../firewall/protect-azure-virtual-desktop.md).
2. [Azure Firewall DNS 설정](../firewall/dns-settings.md)을 구성합니다.

## <a name="error-3019"></a>오류: 3019

**이벤트 뷰어** > **windows 로그** > **애플리케이션** 으로 이동 합니다. ID가 3019인 이벤트가 표시되면 에이전트가 웹 소켓 전송 URL에 연결할 수 없음을 의미합니다. 세션 호스트에 성공적으로 연결하고 네트워크 트래픽이 이러한 제한을 우회하도록 허용하려면 [필수 URL 목록](safe-url-list.md)에 나열된 URL을 차단 해제해야 합니다. Azure 네트워킹 팀과 협력하여 방화벽, 프록시 및 DNS 설정이 이러한 URL을 차단하지 않는지 확인하세요. 네트워크 추적 로그를 확인하여 Azure Virtual Desktop 서비스가 차단되는 위치를 확인할 수도 있습니다. 이런 특정 문제에 대한 지원 요청을 여는 경우에는 네트워크 추적 로그를 요청에 첨부해야 합니다.

## <a name="error-installationhealthcheckfailedexception"></a>오류: InstallationHealthCheckFailedException

**이벤트 뷰어** > **windows 로그** > **애플리케이션** 으로 이동 합니다. 설명에 "InstallationHealthCheckFailedException"라고 표시된 ID가 3277인 이벤트가 보이면, 터미널 서버가 스택 수신기의 레지스트리 키를 전환했기 때문에 스택 수신기가 작동하지 않는 것입니다.

이 문제를 해결하려면:
1. [스택 수신기가 작동](#error-stack-listener-isnt-working-on-windows-10-2004-vm)하는지 확인합니다.
2. 스택 수신기가 작동하지 않으면 [스택 구성 요소를 수동으로 제거하고 다시 설치](#error-vms-are-stuck-in-unavailable-or-upgrading-state)합니다.

## <a name="error-endpoint_not_found"></a>오류: ENDPOINT_NOT_FOUND

**이벤트 뷰어** > **windows 로그** > **애플리케이션** 으로 이동 합니다. 설명에 "ENDPOINT_NOT_FOUND" 라고 표시된 ID가 3277인 이벤트가 보이면, broker가 연결을 설정할 엔드포인트를 찾을 수 없다는 의미입니다. 이런 연결 문제가 발생하는 이유는 다음 중 하나입니다.

- 호스트 풀에 VM이 없습니다.
- 호스트 풀의 VM이 활성 상태가 아닙니다.
- 호스트 풀의 모든 VM이 최대 세션 제한을 초과했습니다.
- 호스트 풀의 VM 중 에이전트 서비스가 실행되고 있는 VM이 없습니다.

이 문제를 해결하려면:

1. VM의 전원이 켜져 있고 호스트 풀에서 제거되지 않았는지 확인합니다.
2. VM이 최대 세션 제한을 초과하지 않았는지 확인합니다.
3. [에이전트 서비스가 실행 중](#error-the-rdagentbootloader-andor-remote-desktop-agent-loader-has-stopped-running)이고 [스택 수신기가 작동 중](#error-stack-listener-isnt-working-on-windows-10-2004-vm)인지 확인합니다.
4. [에이전트가 broker에 연결할 수 있는지](#error-agent-cannot-connect-to-broker-with-invalid_form) 확인합니다.
5. [VM에 유효한 등록 토큰이 있는지](#error-invalid_registration_token) 확인합니다.
6. [VM 등록 토큰이 만료되지 않았는지](./faq.yml) 확인합니다. 

## <a name="error-installmsiexception"></a>오류: InstallMsiException

**이벤트 뷰어** > **windows 로그** > **애플리케이션** 으로 이동 합니다. 설명에서 **InstallMsiException** 이라고 표시된 ID가 3277인 이벤트가 보이면, 에이전트를 설치하려는 동안 설치 프로그램이 이미 다른 애플리케이션에 대해 실행 중이거나 정책이 msiexec.exe 프로그램이 실행되지 않도록 차단하고 있는 것입니다.

이 문제를 해결하려면 다음 정책을 사용하지 않도록 설정합니다.
   - Windows Installer 끄기  
      - 범주 경로: Computer Configuration\Administrative Templates\Windows Components\Windows Installer
   
>[!NOTE]
>포괄적인 정책 목록이 아니라 현재 알고 있는 정책 목록입니다.

정책을 사용하지 않도록 설정하려면 다음을 수행합니다.
1. 관리자 권한으로 명령 프롬프트를 엽니다.
2. **rsop.msc** 를 입력하고 실행합니다.
3. **정책 결과 집합** 창이 표시되면 범주 경로로 이동합니다.
4. 정책을 선택합니다.
5. **사용 안 함** 을 선택합니다.
6. **적용** 을 선택합니다.   

   > [!div class="mx-imgBorder"]
   > ![정책 결과 집합의 Windows Installer 정책 스크린샷](media/gpo-policy.png)

## <a name="error-win32exception"></a>오류: Win32Exception

**이벤트 뷰어** > **windows 로그** > **애플리케이션** 으로 이동 합니다. 설명에 **InstallMsiException** 이라고 표시된 ID가 3277인 이벤트가 보이면 정책이 cmd.exe가 실행되지 않도록 차단하고 있는 것입니다. 이 프로그램을 차단하면 에이전트가 업데이트될 때마다 서비스를 다시 시작하는 데 사용해야 하는 콘솔 창이 실행되지 않습니다.

이 문제를 해결하려면 다음 정책을 사용하지 않도록 설정합니다.
   - 명령 프롬프트에 대한 액세스 방지   
      - 범주 경로: User Configuration\Administrative Templates\System
    
>[!NOTE]
>포괄적인 정책 목록이 아니라 현재 알고 있는 정책 목록입니다.

정책을 사용하지 않도록 설정하려면 다음을 수행합니다.
1. 관리자 권한으로 명령 프롬프트를 엽니다.
2. **rsop.msc** 를 입력하고 실행합니다.
3. **정책 결과 집합** 창이 표시되면 범주 경로로 이동합니다.
4. 정책을 선택합니다.
5. **사용 안 함** 을 선택합니다.
6. **적용** 을 선택합니다.   

## <a name="error-stack-listener-isnt-working-on-windows-10-2004-vm"></a>오류: Windows 10 2004 VM에서 스택 수신기가 작동하지 않음

명령 프롬프트에서 **qwinsta** 를 실행하고 **rdp-sxs** 옆에 표시되는 버전 번호를 적어둡니다. **rdp-tcp** 및 **rdp-sxs** 구성 요소 옆에 **Listen** 이 보이지 않거나 **qwinsta** 를 실행한 후 전혀 보이지 않으면, 스택 문제가 있는 것입니다. 스택 업데이트는 에이전트 업데이트와 함께 설치되며, 설치가 잘못되면 Azure Virtual Desktop 수신기가 작동하지 않습니다.

이 문제를 해결하려면:
1. 레지스트리 편집기를 엽니다.
2. **HKEY_LOCAL_MACHINE** > **SYSTEM** > **CurrentControlSet** > **Control** > **Terminal Server** > **WinStations** 로 이동합니다.
3. **WinStations** 아래에 다른 스택 버전에 대해 여러 폴더가 표시될 수 있습니다. 명령 프롬프트에서 **qwinsta** 를 실행할 때 표시된 버전 정보와 일치하는 폴더를 선택합니다.
4. **fReverseConnectMode** 를 찾아서 데이터 값이 **1** 인지 확인합니다. 또한 **fEnableWinStation** 가 **1** 로 설정되어 있는지 확인합니다.

   > [!div class="mx-imgBorder"]
   > ![fReverseConnectMode 스크린샷](media/fenable-2.png)

5. **fReverseConnectMode** 가 **1** 로 설정되어 있지 않으면 **fReverseConnectMode** 를 선택하고 값 필드에 **1** 을 입력합니다. 
6. **fEnableWinStation** 이 **1** 로 설정되어 있지 않으면 **fEnableWinStation** 을 선택하고 값 필드에 **1** 을 입력합니다.
7. VM이 다시 시작됩니다. 

>[!NOTE]
>한 번에 여러 VM에 대해 **fReverseConnectMode** 또는 **fEnableWinStation** 모드를 변경하려면 다음 두 가지 중 하나를 수행하면 됩니다.
>
>- 이미 작업 중인 컴퓨터에서 레지스트리 키를 내보내고 변경이 필요한 다른 모든 컴퓨터로 가져옵니다.
>- 변경이 필요한 컴퓨터의 레지스트리 키 값을 설정하는 GPO(그룹 정책 개체)를 만듭니다.

7. **HKEY_LOCAL_MACHINE** > **SYSTEM** > **CurrentControlSet** > **Control** > **Terminal Server** > **ClusterSettings** 로 이동합니다.
8. **ClusterSettings** 에서 **SessionDirectoryListener** 를 찾아서 데이터 값이 **rdp-sxs...** 인지 확인합니다.
9. **SessionDirectoryListener** 가 **rdp-sxs...** 로 설정되어 있지 않으면, [에이전트 및 부팅 로더 제거](#step-1-uninstall-all-agent-boot-loader-and-stack-component-programs) 섹션의 단계에 따라 먼저 에이전트, 부팅 로더 및 스택 구성 요소를 제거한 다음, [에이전트와 부팅 로더를 다시 설치](#step-4-reinstall-the-agent-and-boot-loader)해야 합니다. 그러면 병렬 스택이 다시 설치됩니다.

## <a name="error-downloadmsiexception"></a>오류: DownloadMsiException

**이벤트 뷰어** > **windows 로그** > **애플리케이션** 으로 이동 합니다. 설명에 **DownloadMsiException** 이라고 표시된 ID가 3277인 이벤트가 보이면 RDAgent용 디스크 공간이 충분하지 않은 것입니다.

이 문제를 해결하려면 다음과 같이 디스크 공간을 확보합니다.
   - 더 이상 사용하지 않는 파일 삭제
   - VM의 스토리지 용량 늘리기

## <a name="error-agent-fails-to-update-with-missingmethodexception"></a>오류 : MissingMethodException으로 인해 에이전트를 업데이트하지 못함

**이벤트 뷰어** > **windows 로그** > **애플리케이션** 으로 이동 합니다. 설명에 "MissingMethodException: 메서드를 찾을 수 없음"이라고 표시된 ID가 3389인 이벤트가 보이면, Azure Virtual Desktop 에이전트가 정상적으로 업데이트되지 않았고 이전 버전으로 되돌려진 것을 의미합니다. 현재 VM에 설치된 .NET Framework의 버전 번호가 4.7.2보다 낮기 때문일 수 있습니다. 이 문제를 해결하려면 [.NET Framework 설명서](https://support.microsoft.com/topic/microsoft-net-framework-4-7-2-offline-installer-for-windows-05a72734-2127-a15d-50cf-daf56d5faec2)의 설치 지침에 따라 .NET을 버전 4.7.2 이상으로 업그레이드해야 합니다.


## <a name="error-vms-are-stuck-in-unavailable-or-upgrading-state"></a>오류: VM이 사용할 수 없음 또는 업그레이드 중 상태에서 멈춤

PowerShell 창을 관리자 권한으로 열고 다음 cmdlet을 실행합니다.

```powershell
Get-AzWvdSessionHost -ResourceGroupName <resourcegroupname> -HostPoolName <hostpoolname> | Select-Object *
```

호스트 풀의 세션 호스트에 대해 나열된 상태가 항상 "사용할 수 없음" 또는 "업그레이드 중"이라고 표시되면 에이전트 또는 스택이 성공적으로 설치되지 않은 것입니다.

이 문제를 해결하려면 병렬 스택을 다시 설치합니다.
1. 관리자 권한으로 명령 프롬프트를 엽니다.
2. **net stop RDAgentBootLoader** 를 입력합니다. 
3. **제어판** > **프로그램** > **프로그램 및 기능** 으로 이동합니다.
4. 최신 버전의 **원격 데스크톱 서비스 SxS 네트워크 스택** 또는 **HKEY_LOCAL_MACHINE** > **SYSTEM** > **CurrentControlSet** > **Control** > **Terminal Server** > **WinStations** 아래 **ReverseConnectListener** 에 나열된 버전을 제거합니다.
5. 관리자 권한으로 콘솔 창을 열고 **Program Files** > **Microsoft RDInfra** 로 이동합니다.
6. **SxSStack** 구성 요소를 선택 하거나 **`msiexec /i SxsStack-<version>.msi`** 명령을 실행 하 여 MSI를 설치 합니다.
8. VM이 다시 시작됩니다.
9. 명령 프롬프트로 돌아가서 **qwinsta** 명령을 실행합니다.
10. 6단계에서 설치한 스택 구성 요소 옆에 **Listen** 이 표시되는지 확인합니다.
   - 그렇다면 명령 프롬프트에서 **net start RDAgentBootLoader** 를 입력하고 VM을 다시 시작합니다.
   - 그렇지 않으면 [VM을 다시 등록하고 에이전트 구성 요소를 다시 설치](#your-issue-isnt-listed-here-or-wasnt-resolved)해야 합니다.

## <a name="error-connection-not-found-rdagent-does-not-have-an-active-connection-to-the-broker"></a>오류: 연결을 찾을 수 없음: RDAgent에 broker에 대한 활성 연결이 없음

VM이 연결 제한에 도달하여 VM이 새 연결을 수락하지 못할 수 있습니다.

이 문제를 해결하려면:
   - 최대 세션 제한을 줄입니다. 그러면 리소스가 세션 호스트에 더 균등하게 분산되고 리소스 고갈을 방지할 수 있습니다.
   - VM의 리소스 용량을 늘립니다.

## <a name="error-operating-a-pro-vm-or-other-unsupported-os"></a>오류: Pro VM 또는 기타 지원되지 않는 OS 작동

병렬 스택은 Windows Enterprise 또는 Windows Server SKU에서만 지원되며 Pro VM과 같은 운영 체제는 지원하지 않습니다. Enterprise 또는 Server SKU가 없는 경우 스택이 VM에 설치되지만 활성화되지 않으므로 명령줄에서 **qwinsta** 를 실행할 때 스택이 표시되지 않습니다.

이 문제를 해결하려면 Windows Enterprise 또는 Windows Server인 VM을 만듭니다.
1. [가상 머신 세부 정보](create-host-pools-azure-marketplace.md#virtual-machine-details)로 이동하고 1~12 단계를 수행하여 다음 권장 이미지 중 하나를 설정합니다.
   - Windows 10 Enterprise 다중 세션, 버전 1909
   - Windows 10 Enterprise 다중 세션, 버전 1909 + Microsoft 365 Apps
   - Windows Server 2019 Datacenter
   - Windows 10 Enterprise 다중 세션, 버전 2004
   - Windows 10 Enterprise 다중 세션, 버전 2004 + Microsoft 365 Apps
2. **검토 및 만들기** 를 선택합니다.

## <a name="error-name_already_registered"></a>오류: NAME_ALREADY_REGISTERED

VM의 이름이 이미 등록되었으며 중복되었을 수 있습니다.

이 문제를 해결하려면:
1. [호스트 풀에서 세션 호스트 제거](#step-2-remove-the-session-host-from-the-host-pool) 섹션의 단계를 따릅니다.
2. [다른 VM을 만듭니다](expand-existing-host-pool.md#add-virtual-machines-with-the-azure-portal). 이 VM에 대한 고유한 이름을 선택해야 합니다.
3. [Azure Portal](https://portal.azure.com)로 이동하여 VM이 있던 호스트 풀의 **개요** 페이지를 엽니다. 
4. **세션 호스트** 탭을 열고 모든 세션 호스트가 해당 호스트 풀에 있는지 확인합니다.
5. 세션 호스트 상태가 **사용 가능** 으로 표시될 때까지 5~10분 동안 기다립니다.

   > [!div class="mx-imgBorder"]
   > ![사용 가능한 세션 호스트의 스크린샷](media/hostpool-portal.png)

## <a name="your-issue-isnt-listed-here-or-wasnt-resolved"></a>문제가 여기에 없거나 해결되지 않은 경우

이 문서에서 문제를 찾을 수 없거나 지침이 도움이 되지 않았다면, Azure Virtual Desktop 에이전트를 제거, 다시 설치 및 다시 등록하는 것이 좋습니다. 이 섹션의 지침은 모든 에이전트, 부팅 로더 및 스택 구성 요소 제거하고, 호스트 풀에서 세션 호스트를 제거하고, VM에 대한 새 등록 키를 생성하고, 에이전트 및 부팅 로더를 다시 설치하여 Azure Virtual Desktop 서비스에 VM을 다시 등록하는 방법을 보여줍니다. 다음 시나리오 중 하나 이상에 해당하는 경우 다음 지침을 따르십시오.
- VM이 **업그레이드 중** 또는 **사용할 수 없음** 상태로 멈춤
- 스택 수신기가 작동하지 않고 Windows 10 1809, 1903 또는 1909에서 실행 중임
- **EXPIRED_REGISTRATION_TOKEN** 오류가 발생함
- 세션 호스트 목록에 VM이 표시되지 않음
- 서비스 창에 **원격 데스크톱 에이전트 로더** 가 표시되지 않음
- 작업 관리자에 **RdAgentBootLoader** 구성 요소가 표시되지 않음
- 사용자 지정 이미지 VM에서 **연결 브로커가 설정의 유효성을 검증하지 못함** 오류가 발생함
- 이 문서의 지침으로 문제가 해결되지 않았음

### <a name="step-1-uninstall-all-agent-boot-loader-and-stack-component-programs"></a>1단계: 모든 에이전트, 부팅 로더 및 스택 구성 요소 프로그램 제거

에이전트, 부팅 로더 및 스택을 다시 설치하기 전에 VM에서 기존 구성 요소 프로그램을 모두 제거해야 합니다. 모든 에이전트, 부팅 로더 및 스택 구성 요소 프로그램을 제거하려면 다음을 수행합니다.
1. VM에 관리자로 로그인합니다. 
2. **제어판** > **프로그램** > **프로그램 및 기능** 으로 이동합니다.
3. 다음 프로그램을 제거합니다.
   - 원격 데스크톱 에이전트 부팅 로더
   - 원격 데스크톱 서비스 인프라
   - 원격 데스크톱 서비스 인프라 Geneva 에이전트
   - 원격 데스크톱 서비스 SxS 네트워크 스택
   
>[!NOTE]
>이러한 프로그램의 여러 인스턴스가 표시될 수 있습니다. 모든 항목을 제거해야 합니다.

   > [!div class="mx-imgBorder"]
   > ![프로그램을 제거하는 스크린샷](media/uninstall-program.png)

### <a name="step-2-remove-the-session-host-from-the-host-pool"></a>2단계: 호스트 풀에서 세션 호스트 제거

호스트 풀에서 세션 호스트를 제거하면 해당 호스트 풀에 세션 호스트가 더 이상 등록되어 있지 않습니다. 그러면 세션 호스트 등록이 다시 설정되는 기능을 합니다. 호스트 풀에서 세션 호스트를 제거하려면 다음을 수행합니다.
1. [Azure Portal](https://portal.azure.com)에서 VM이 있는 호스트 풀의 **개요** 페이지로 이동합니다. 
2. **세션 호스트** 탭으로 이동하여 해당 호스트 풀의 모든 세션 호스트 목록을 확인합니다.
3. 세션 호스트 목록을 살펴보고 제거하려는 VM을 선택합니다.
4. **제거** 를 선택합니다.  

   > [!div class="mx-imgBorder"]
   > ![호스트 풀에서 VM을 제거하는 스크린샷](media/remove-sh.png)

### <a name="step-3-generate-a-new-registration-key-for-the-vm"></a>3단계: VM에 대한 새 등록 키 생성

VM을 호스트 풀 및 서비스에 다시 등록하는 데 사용되는 새 등록 키를 생성해야 합니다. VM에 대한 새 등록 키를 생성하려면 다음을 수행합니다.
1. [Azure Portal](https://portal.azure.com)을 열고 편집할 VM의 호스트 풀에 대한 **개요** 페이지로 이동합니다.
2. **등록 키** 를 선택합니다.

   > [!div class="mx-imgBorder"]
   > ![포털의 등록 키 스크린샷](media/reg-key.png)

3. **등록 키** 탭을 열고 **새 키 생성** 을 선택합니다.
4. 만료 날짜를 입력한 다음, **확인** 을 선택합니다.  

>[!NOTE]
>만료 날짜는 생성 시간 및 날짜로부터 1시간 이상 27일 이하일 수 있습니다. 만료 날짜는 최대 27일로 설정하는 것이 좋습니다.

5. 새로 생성된 키를 클립보드에 복사합니다. 이 키는 나중에 필요합니다.

### <a name="step-4-reinstall-the-agent-and-boot-loader"></a>4단계: 에이전트 및 부팅 로더 다시 설치

최신 버전의 에이전트와 부팅 로더를 다시 설치하면 병렬 스택 및 Geneva 모니터링 에이전트도 자동으로 설치됩니다. 에이전트 및 부팅 로더를 다시 설치하려면 다음을 수행합니다.
1. 관리자 권한으로 VM에 로그인하고 VM에서 실행 중인 Windows 버전에 따라 배포에 맞는 에이전트 설치 프로그램 버전을 사용합니다. Windows 10 VM이 있는 경우 [가상 머신 등록](create-host-pools-powershell.md#register-the-virtual-machines-to-the-azure-virtual-desktop-host-pool)의 지침에 따라 **Azure Virtual Desktop 에이전트** 및  **Azure Virtual Desktop 에이전트 부팅 로더** 를 다운로드합니다. Windows 7 VM이 있는 경우 [가상 머신 등록](deploy-windows-7-virtual-machine.md#configure-a-windows-7-virtual-machine)의 13~14 지침에 따라 **Azure Virtual Desktop 에이전트** 및 **Azure Virtual Desktop 에이전트 관리자** 를 다운로드합니다.

   > [!div class="mx-imgBorder"]
   > ![에이전트 및 부팅 로더 다운로드 페이지의 스크린샷](media/download-agent.png)

2. 다운로드한 에이전트 및 부팅 로더 설치 프로그램을 마우스 오른쪽 단추로 클릭합니다.
3. **속성** 을 선택합니다.
4. **차단 해제** 를 선택합니다.
5. **확인** 을 선택합니다.
6. 에이전트 설치 관리자를 실행합니다.
7. 설치 프로그램이 등록 토큰을 요청하면 클립보드의 등록 키를 붙여넣습니다. 

   > [!div class="mx-imgBorder"]
   > ![붙여넣은 등록 토큰의 스크린샷](media/pasted-agent-token.png)

8. 부팅 로더 설치 프로그램을 실행합니다.
9. VM이 다시 시작됩니다. 
10. [Azure Portal](https://portal.azure.com)로 이동하여 VM이 속한 호스트 풀의 **개요** 페이지를 엽니다.
11. **세션 호스트** 탭으로 이동하여 해당 호스트 풀의 모든 세션 호스트 목록을 확인합니다.
12. 이제 **사용 가능** 상태로 호스트 풀에 등록된 세션 호스트가 표시됩니다. 

   > [!div class="mx-imgBorder"]
   > ![사용 가능한 세션 호스트의 스크린샷](media/hostpool-portal.png)

## <a name="next-steps"></a>다음 단계

문제가 계속되면 지원 사례를 만들어서 현재 겪고 있는 문제와 문제를 해결하기 위해 취한 조치에 대한 자세한 정보를 포함하세요. 다음 목록에는 Azure Virtual Desktop 배포에서 발생하는 문제를 해결하는 데 사용할 수 있는 기타 리소스가 포함되어 있습니다.

- Azure Virtual Desktop 및 에스컬레이션 트랙 문제 해결에 대한 개요는 [문제 해결 개요, 피드백 및 지원](troubleshoot-set-up-overview.md)을 참조하세요.
- Azure Virtual Desktop 환경에서 호스트 풀을 만드는 동안 발생하는 문제를 해결하려면 [환경 및 호스트 풀 만들기](troubleshoot-set-up-issues.md)를 참조하세요.
- Azure Virtual Desktop에서 VM(가상 머신)을 구성하는 동안 문제를 해결하려면 [세션 호스트 가상 머신 구성](troubleshoot-vm-configuration.md)을 참조하세요.
- Azure Virtual Desktop 클라이언트 연결 문제를 해결하려면 [Azure Virtual Desktop 서비스 연결](troubleshoot-service-connection.md)을 참조하세요.
- 원격 데스크톱 클라이언트와 관련된 문제를 해결하려면 [원격 데스크톱 클라이언트 문제 해결](troubleshoot-client.md)을 참조하세요.
- Azure Virtual Desktop과 함께 PowerShell을 사용할 때 발생하는 문제를 해결하려면 [Azure Virtual Desktop PowerShell](troubleshoot-powershell.md)을 참조하세요.
- 서비스에 대한 자세한 내용은 [Azure Virtual Desktop 환경](environment-setup.md)을 참조하세요.
- 문제 해결 자습서를 진행하려면 [자습서: Resource Manager 템플릿 배포 문제 해결](../azure-resource-manager/templates/template-tutorial-troubleshoot.md)을 참조하세요.
- 감사 작업에 대해 알아보려면 [리소스 관리자로 작업 감사](../azure-monitor/essentials/activity-log.md)를 참조하세요.
- 배포 중 오류를 확인하는 작업에 대해 알아보려면 [배포 작업 보기](../azure-resource-manager/templates/deployment-history.md)를 참조하세요.
