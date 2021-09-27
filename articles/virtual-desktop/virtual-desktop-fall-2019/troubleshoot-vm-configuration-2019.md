---
title: Azure Virtual Desktop(클래식) 세션 호스트 문제 해결 - Azure
description: Azure Virtual Desktop(클래식) 세션 호스트 가상 머신을 구성할 때 발생하는 문제를 해결하는 방법입니다.
author: Heidilohr
ms.topic: troubleshooting
ms.date: 05/11/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: f867ad9d55f5e5dbaac7962dd8e4ae4daeafee6f
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124776845"
---
# <a name="azure-virtual-desktop-classic-session-host-virtual-machine-configuration"></a>Azure Virtual Desktop(클래식) 세션 호스트 가상 머신 구성

>[!IMPORTANT]
>이 내용은 Azure Resource Manager의 Azure Virtual Desktop 개체를 지원하지 않는 Azure Virtual Desktop(클래식)에 적용됩니다. Azure Resource Manager의 Azure Virtual Desktop 개체를 관리하려는 경우 [이 문서](../troubleshoot-vm-configuration.md)를 참조하세요.

이 문서를 사용하여 Azure Virtual Desktop 세션 호스트 VM(가상 머신)을 구성할 때 발생하는 문제를 해결할 수 있습니다.

## <a name="provide-feedback"></a>피드백 제공

제품 팀 및 활발하게 활동하는 커뮤니티 멤버들과 Azure Virtual Desktop 서비스에 대해 토론하려면 [Azure Virtual Desktop 기술 커뮤니티](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)를 방문하세요.

## <a name="vms-are-not-joined-to-the-domain"></a>VM이 도메인에 조인되어 있지 않음

VM을 도메인에 조인하는 데 문제가 있는 경우 다음 지침을 따릅니다.

- [Windows Server 가상 머신을 관리되는 도메인에 조인](../../active-directory-domain-services/join-windows-vm.md)의 프로세스를 사용하거나 [도메인 조인 템플릿](https://azure.microsoft.com/resources/templates/vm-domain-join-existing/)을 사용하여 VM을 수동으로 조인합니다.
- VM의 명령줄에서 도메인 이름에 대한 ping을 시도합니다.
- [도메인 조인 오류 메시지 문제 해결](https://social.technet.microsoft.com/wiki/contents/articles/1935.troubleshooting-domain-join-error-messages.aspx)에서 도메인 조인 오류 메시지 목록을 검토합니다.

### <a name="error-incorrect-credentials"></a>오류: 잘못된 자격 증명

**원인:** Azure Resource Manager 템플릿 인터페이스 수정에서 자격 증명을 입력할 때 오타가 발생했습니다.

**해결 방법:** 다음 작업 중 하나를 수행하여 해결합니다.

- 수동으로 VM을 도메인에 추가합니다.
- 자격 증명이 확인되면 템플릿을 다시 배포합니다. [PowerShell을 사용하여 호스트 풀 만들기](create-host-pools-powershell-2019.md)를 참조하세요.
- [기존 Windows VM을 AD 도메인에 조인](https://azure.microsoft.com/resources/templates/vm-domain-join-existing/)에서 템플릿을 사용하여 VM을 도메인에 조인합니다.

### <a name="error-timeout-waiting-for-user-input"></a>오류: 사용자 입력을 기다리는 동안 시간이 초과되었습니다.

**원인:** 도메인 조인을 완료하는 데 사용된 계정에 MFA(다단계 인증)가 있을 수 있습니다.

**해결 방법:** 다음 작업 중 하나를 수행하여 해결합니다.

- 계정에 대한 MFA를 일시적으로 제거합니다.
- 서비스 계정을 사용합니다.

### <a name="error-the-account-used-during-provisioning-doesnt-have-permissions-to-complete-the-operation"></a>오류: 프로비전하는 동안 사용된 계정에 작업을 완료할 수 있는 권한이 없습니다.

**원인:** 규정 준수 및 규정으로 인해 VM을 도메인에 조인할 수 있는 권한이 사용 중인 계정에 없습니다.

**해결 방법:** 다음 작업 중 하나를 수행하여 해결합니다.

- 관리자 그룹의 구성원인 계정을 사용합니다.
- 사용 중인 계정에 필요한 권한을 부여합니다.

### <a name="error-domain-name-doesnt-resolve"></a>오류: 도메인 이름이 확인되지 않습니다.

**원인 1:** 도메인이 있는 VNET(가상 네트워크)과 연결되지 않은 가상 네트워크에 VM이 있습니다.

**해결 방법 1:** VM이 프로비전된 VNET과 DC(도메인 컨트롤러)가 실행되는 VNET 간에 VNET 피어링을 만듭니다. [가상 네트워크 피어링 만들기 - Resource Manager, 서로 다른 구독](../../virtual-network/create-peering-different-subscriptions.md)을 참조하세요.

**원인 2:** Azure AD DS(Azure Active Directory Domain Services)를 사용하는 경우 관리되는 도메인 컨트롤러를 가리키도록 업데이트된 DNS 서버 설정이 가상 네트워크에 없습니다.

**해결 방법 2:** Azure AD DS가 포함된 가상 네트워크의 DNS 설정을 업데이트하려면 [Azure 가상 네트워크에 대한 DNS 설정 업데이트](../../active-directory-domain-services/tutorial-create-instance.md#update-dns-settings-for-the-azure-virtual-network)를 참조하세요.

**원인 3:** 네트워크 인터페이스의 DNS 서버 설정에서 가상 네트워크의 적절한 DNS 서버를 가리키지 않습니다.

**해결 방법 3:** [DNS 서버 변경]의 단계에 따라 다음 작업 중 하나를 수행하여 해결합니다.
- [DNS 서버 변경](../../virtual-network/virtual-network-network-interface.md#change-dns-servers) 단계에 따라 네트워크 인터페이스의 DNS 서버 설정을 **사용자 지정** 으로 변경하고, 가상 네트워크에 있는 DNS 서버의 개인 IP 주소를 지정합니다.
- [DNS 서버 변경](../../virtual-network/virtual-network-network-interface.md#change-dns-servers) 단계에 따라 네트워크 인터페이스의 DNS 서버 설정을 **가상 네트워크에서 상속** 으로 변경한 다음, [DNS 서버 변경](../../virtual-network/manage-virtual-network.md#change-dns-servers) 단계에 따라 가상 네트워크의 DNS 서버 설정을 변경합니다.

## <a name="azure-virtual-desktop-agent-and-azure-virtual-desktop-boot-loader-are-not-installed"></a>Azure Virtual Desktop 에이전트와 Azure Virtual Desktop 부팅 로더가 설치되어 있지 않음

VM을 프로비저닝하는 권장 방법은 Azure Resource Manager의 **Azure Virtual Desktop 호스트 풀 만들기 및 프로비저닝** 템플릿을 사용하는 것입니다. 이 템플릿은 Azure Virtual Desktop 에이전트와 Azure Virtual Desktop 에이전트 부팅 로더를 자동으로 설치합니다.

다음 지침에 따라 구성 요소가 설치되었는지 확인하고 오류 메시지를 확인합니다.

1. **제어판** > **프로그램** > **프로그램 및 기능** 에서 체크 인하여 두 구성 요소가 설치되어 있는지 확인합니다. **Azure Virtual Desktop 에이전트** 와 **Azure Virtual Desktop 에이전트 부팅 로더** 가 표시되지 않으면 VM에 설치되지 않은 것입니다.
2. **파일 탐색기** 를 열고, **C:\Windows\Temp\ScriptLog.log** 로 이동합니다. 파일이 없는 경우 제공된 두 구성 요소를 설치한 PowerShell DSC가 보안 컨텍스트에서 실행될 수 없음을 나타냅니다.
3. **C:\Windows\Temp\ScriptLog.log** 파일이 있는 경우 이 파일을 열고 오류 메시지를 확인합니다.

### <a name="error-azure-virtual-desktop-agent-and-azure-virtual-desktop-agent-boot-loader-are-missing-cwindowstempscriptloglog-is-also-missing"></a>오류: Azure Virtual Desktop 에이전트와 Azure Virtual Desktop 에이전트 부팅 로더가 없습니다. C:\Windows\Temp\ScriptLog.log도 없습니다.

**원인 1:** 입력 중에 제공된 Azure Resource Manager 템플릿에 대한 자격 증명이 잘못되었거나 권한이 없습니다.

**해결 방법 1:** [PowerShell을 사용하여 호스트 풀 만들기](create-host-pools-powershell-2019.md)를 사용하여 누락된 구성 요소를 VM에 수동으로 추가합니다.

**원인 2:** PowerShell DSC를 시작하고 실행할 수 있지만 Azure Virtual Desktop에 로그인하여 필요한 정보를 가져올 수 없어 완료하지 못했습니다.

**해결 방법 2:** 다음 목록의 항목을 확인합니다.

- 계정에 MFA가 없는지 확인합니다.
- 테넌트 이름이 정확하고 테넌트가 Azure Virtual Desktop에 있는지 확인합니다.
- 계정에 최소한 RDS 기여자 권한이 있는지 확인합니다.

### <a name="error-authentication-failed-error-in-cwindowstempscriptloglog"></a>오류: 인증에 실패했습니다. 오류는 C:\Windows\Temp\ScriptLog.log에 있습니다.

**원인:** PowerShell DSC를 실행할 수 있지만 Azure Virtual Desktop에 연결할 수 없습니다.

**해결 방법:** 다음 목록의 항목을 확인합니다.

- Azure Virtual Desktop 서비스에 VM을 수동으로 등록합니다.
- Azure Virtual Desktop에 연결하는 데 사용되는 계정에 테넌트에서 호스트 풀을 만들 수 있는 권한이 있는지 확인합니다.
- 계정에 MFA가 없는지 확인합니다.

## <a name="azure-virtual-desktop-agent-is-not-registering-with-the-azure-virtual-desktop-service"></a>Azure Virtual Desktop 에이전트가 Azure Virtual Desktop 서비스에 등록되지 않음

Azure Virtual Desktop 에이전트는 세션 호스트 VM에 처음 설치될 때(수동으로 또는 Azure Resource Manager 템플릿과 PowerShell DSC를 통해) 등록 토큰을 제공합니다. 다음 섹션에서는 Azure Virtual Desktop 에이전트와 토큰에 적용할 수 있는 문제 해결에 대해 설명합니다.

### <a name="error-the-status-filed-in-get-rdssessionhost-cmdlet-shows-status-as-unavailable"></a>오류: Get-RdsSessionHost cmdlet에서 제출된 상태가 ‘사용할 수 없음’으로 표시됩니다.

> [!div class="mx-imgBorder"]
> ![Get-RdsSessionHost cmdlet에서 상태를 ‘사용할 수 없음’으로 표시합니다.](../media/23b8e5f525bb4e24494ab7f159fa6b62.png)

**원인:** 에이전트에서 자체적으로 새 버전으로 업데이트할 수 없습니다.

**해결 방법:** 다음 지침에 따라 에이전트를 수동으로 업데이트합니다.

1. 세션 호스트 VM에서 새 버전의 에이전트를 다운로드합니다.
2. [작업 관리자]를 시작하고, [서비스] 탭에서 RDAgentBootLoader 서비스를 중지합니다.
3. 새 버전의 Azure Virtual Desktop 에이전트용 설치 관리자를 실행합니다.
4. 등록 토큰을 입력하라는 메시지가 표시되면 INVALID_TOKEN 항목을 제거하고, [다음]을 누릅니다(새 토큰이 필요하지 않음).
5. 설치 마법사를 완료합니다.
6. [작업 관리자]를 열고, RDAgentBootLoader 서비스를 시작합니다.

## <a name="error--azure-virtual-desktop-agent-registry-entry-isregistered-shows-a-value-of-0"></a>오류: Azure Virtual Desktop 에이전트 레지스트리 항목인 IsRegistered의 값이 0으로 표시됩니다.

**원인:** 등록 토큰이 만료되었거나 만료 값을 999999로 설정하여 생성되었습니다.

**해결 방법:** 다음 지침에 따라 에이전트 레지스트리 오류를 수정합니다.

1. 이미 등록 토큰이 있는 경우 Remove-RDSRegistrationInfo를 사용하여 제거합니다.
2. Rds-NewRegistrationInfo를 사용하여 새 토큰을 생성합니다.
3. -ExpriationHours 매개 변수가 72(최댓값은 99999)로 설정되어 있는지 확인합니다.

### <a name="error-azure-virtual-desktop-agent-isnt-reporting-a-heartbeat-when-running-get-rdssessionhost"></a>오류: Get-RdsSessionHost를 실행할 때 Azure Virtual Desktop 에이전트에서 하트비트를 보고하지 않습니다.

**원인 1:** RDAgentBootLoader 서비스가 중지되었습니다.

**해결 방법 1:** [작업 관리자]를 시작하고, [서비스] 탭에서 RDAgentBootLoader 서비스에 대해 '중지됨' 상태가 보고되면 서비스를 시작합니다.

**원인 2:** 443 포트가 닫혀 있을 수 있습니다.

**해결 방법 2:** 다음 지침에 따라 443 포트를 엽니다.

1. [Sysinternal 도구](/sysinternals/downloads/psping/)에서 PSPing 도구를 다운로드하여 443 포트가 열려 있는지 확인합니다.
2. 에이전트가 실행되는 세션 호스트 VM에 PSPing을 설치합니다.
3. 관리자 권한으로 명령 프롬프트를 열고, 아래 명령을 실행합니다.

    ```cmd
    psping rdbroker.wvdselfhost.microsoft.com:443
    ```

4. PSPing에서 RDBroker로부터 정보를 다시 받았는지 확인합니다.

    ```
    PsPing v2.10 - PsPing - ping, latency, bandwidth measurement utility
    Copyright (C) 2012-2016 Mark Russinovich
    Sysinternals - www.sysinternals.com
    TCP connect to 13.77.160.237:443:
    5 iterations (warmup 1) ping test:
    Connecting to 13.77.160.237:443 (warmup): from 172.20.17.140:60649: 2.00ms
    Connecting to 13.77.160.237:443: from 172.20.17.140:60650: 3.83ms
    Connecting to 13.77.160.237:443: from 172.20.17.140:60652: 2.21ms
    Connecting to 13.77.160.237:443: from 172.20.17.140:60653: 2.14ms
    Connecting to 13.77.160.237:443: from 172.20.17.140:60654: 2.12ms
    TCP connect statistics for 13.77.160.237:443:
    Sent = 4, Received = 4, Lost = 0 (0% loss),
    Minimum = 2.12ms, Maximum = 3.83ms, Average = 2.58ms
    ```

## <a name="troubleshooting-issues-with-the-azure-virtual-desktop-side-by-side-stack"></a>Azure Virtual Desktop 병렬 스택과 관련된 문제 해결

Azure Virtual Desktop 병렬 스택은 Windows Server 2019와 함께 자동으로 설치됩니다. MSI(Microsoft Installer)를 사용하여 병렬 스택을 Microsoft Windows Server 2016 또는 Windows Server 2012 R2에 설치합니다. Microsoft Windows 10에서는 **enablesxstackrs.ps1** 을 사용하여 Azure Virtual Desktop 병렬 스택을 사용하도록 설정합니다.

세션 호스트 풀 VM에서 병렬 스택을 설치하거나 사용하도록 설정하는 세 가지 주요 방법이 있습니다.

- Azure Resource Manager의 **새 Azure Virtual Desktop 호스트 풀 만들기 및 프로비저닝** 템플릿 사용
- 마스터 이미지에 포함 및 사용하도록 설정
- 각 VM에 수동으로 설치하거나 사용하도록 설정(또는 확장/PowerShell 사용)

Azure Virtual Desktop 병렬 스택과 관련된 문제가 있는 경우 명령 프롬프트에서 **qwinsta** 명령을 입력하여 병렬 스택이 설치되었거나 사용하도록 설정되었는지 확인합니다.

병렬 스택이 설치되고 사용하도록 설정된 경우 **qwinsta** 의 출력에 **rdp-sxs** 가 나열됩니다.

> [!div class="mx-imgBorder"]
> ![출력에서 rdp-sx로 나열된 qwinsta를 사용하여 병렬 스택을 설치하거나 사용하도록 설정함](../media/23b8e5f525bb4e24494ab7f159fa6b62.png)

아래에 나열된 레지스트리 항목을 검사하고, 해당 값이 일치하는지 확인합니다. 레지스트리 키가 없거나 값이 일치하지 않는 경우 병렬 스택을 다시 설치하는 방법에 대한 [PowerShell을 사용하여 호스트 풀 만들기](create-host-pools-powershell-2019.md)의 지침을 따릅니다.

```registry
    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal
    Server\WinStations\rds-sxs\"fEnableWinstation":DWORD=1

    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal
    Server\ClusterSettings\"SessionDirectoryListener":rdp-sxs
```

### <a name="error-o_reverse_connect_stack_failure"></a>오류: O_REVERSE_CONNECT_STACK_FAILURE

> [!div class="mx-imgBorder"]
> ![O_REVERSE_CONNECT_STACK_FAILURE 오류 코드](../media/23b8e5f525bb4e24494ab7f159fa6b62.png)

**원인:** 병렬 스택이 세션 호스트 VM에 설치되어 있지 않습니다.

**해결 방법:** 다음 지침에 따라 병렬 스택을 세션 호스트 VM에 설치합니다.

1. RDP(원격 데스크톱 프로토콜)를 사용하여 로컬 관리자 권한으로 세션 호스트 VM에 직접 액세스합니다.
2. 아직 수행하지 않은 경우 PowerShell 세션에서 사용할 [Azure Virtual Desktop PowerShell 모듈](/powershell/windows-virtual-desktop/overview/)을 다운로드하여 가져온 다음, 이 cmdlet을 실행하여 계정에 로그인합니다.

    ```powershell
    Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
    ```

3. [PowerShell을 사용하여 호스트 풀 만들기](create-host-pools-powershell-2019.md)를 사용하여 병렬 스택을 설치합니다.

## <a name="how-to-fix-a-azure-virtual-desktop-side-by-side-stack-that-malfunctions"></a>제대로 작동하지 않는 Azure Virtual Desktop 병렬 스택을 수정하는 방법

병렬 스택이 제대로 작동하지 않는 알려진 상황이 있습니다.

- 병렬 스택을 사용하도록 설정하는 단계의 올바른 순서를 따르지 않음
- Windows 10 EVD(Enhanced Versatile Disc)로 자동 업데이트
- RDSH(원격 데스크톱 세션 호스트) 역할이 없음
- enablesxsstackrc.ps1을 여러 번 실행
- 로컬 관리자 권한이 없는 계정에서 enablesxsstackrc.ps1 실행

이 섹션의 지침은 Azure Virtual Desktop 병렬 스택을 제거하는 데 도움이 될 수 있습니다. 병렬 스택을 제거한 후에는 [PowerShell을 사용하여 호스트 풀 만들기](create-host-pools-powershell-2019.md)의 “Azure Virtual Desktop 호스트 풀에 VM 등록”으로 이동하여 병렬 스택을 다시 설치합니다.

수정을 실행하는 데 사용되는 VM은 병렬 스택이 제대로 작동하지 않는 VM과 동일한 서브넷 및 도메인에 있어야 합니다.

다음 지침에 따라 동일한 서브넷 및 도메인에서 수정을 실행합니다.

1. 표준 RDP(원격 데스크톱 프로토콜)를 사용하여 수정이 적용될 VM에 연결합니다.
2. [https://docs.microsoft.com/sysinternals/downloads/psexec](/sysinternals/downloads/psexec)에서 PsExec을 다운로드합니다.
3. 다운로드한 파일의 압축을 풉니다.
4. 로컬 관리자 권한으로 명령 프롬프트를 시작합니다.
5. PsExec의 압축을 푼 폴더로 이동합니다.
6. 명령 프롬프트에서 다음 명령을 사용합니다.

    ```cmd
            psexec.exe \\<VMname> cmd
    ```

    >[!NOTE]
    >VMname은 제대로 작동하지 않는 병렬 스택이 있는 VM의 컴퓨터 이름입니다.

7. [동의]를 클릭하여 PsExec 사용권 계약에 동의합니다.

    > [!div class="mx-imgBorder"]
    > ![소프트웨어 사용권 계약 스크린샷](../media/SoftwareLicenseTerms.png)

    >[!NOTE]
    >이 대화 상자는 PsExec을 처음 실행할 때만 표시됩니다.

8. 제대로 작동하지 않는 병렬 스택이 있는 VM에서 명령 프롬프트 세션이 열리면 qwinsta를 실행하고 rdp-sx라는 항목을 사용할 수 있는지 확인합니다. 그렇지 않은 경우 병렬 스택이 VM에 없으므로 문제가 병렬 스택에 연결되지 않습니다.

    > [!div class="mx-imgBorder"]
    > ![관리자 명령 프롬프트](../media/AdministratorCommandPrompt.png)

9. 다음 명령을 실행합니다. 그러면 병렬 스택이 제대로 작동하지 않는 VM에 설치된 Microsoft 구성 요소가 나열됩니다.

    ```cmd
        wmic product get name
    ```

10. 위 단계의 제품 이름을 사용하여 아래 명령을 실행합니다.

    ```cmd
        wmic product where name="<Remote Desktop Services Infrastructure Agent>" call uninstall
    ```

11. "원격 데스크톱"으로 시작하는 모든 제품을 제거합니다.

12. 모든 Azure Virtual Desktop 구성 요소가 제거되면 해당 운영 체제에 대한 지침을 따릅니다.

13. 운영 체제가 Windows Server인 경우 Azure Portal을 사용하거나 PsExec 도구를 사용하여 제대로 작동하지 않는 병렬 스택이 있는 VM을 다시 시작합니다.

운영 체제가 Microsoft Windows 10인 경우 아래 지침을 계속 진행합니다.

14. PsExec를 실행하는 VM에서 파일 탐색기를 열고, 제대로 작동하지 않는 병렬 스택이 있는 VM의 시스템 드라이브에 disabledxstackrc.ps1을 복사합니다.

    ```cmd
        \\<VMname>\c$\
    ```

    >[!NOTE]
    >VMname은 제대로 작동하지 않는 병렬 스택이 있는 VM의 컴퓨터 이름입니다.

15. 권장 프로세스: PsExec 도구에서 PowerShell을 시작하고, 이전 단계의 폴더로 이동하여 disablesxsstackrc.ps1을 실행합니다. 또는 다음 cmdlet을 실행할 수 있습니다.

    ```PowerShell
    Remove-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\ClusterSettings" -Name "SessionDirectoryListener" -Force
    Remove-Item -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" -Recurse -Force
    Remove-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations" -Name "ReverseConnectionListener" -Force
    ```

16. cmdlet 실행이 완료되면 제대로 작동하지 않는 병렬 스택이 있는 VM을 다시 시작합니다.

## <a name="remote-desktop-licensing-mode-isnt-configured"></a>원격 데스크톱 라이선싱 모드가 구성되어 있지 않음

관리 계정을 사용하여 Windows 10 Enterprise 다중 세션에 로그인하는 경우 "원격 데스크톱 라이선싱 모드가 구성되어 있지 않습니다. 원격 데스크톱 서비스 작동이 X일 후에 중지됩니다. 연결 브로커 서버에서 서버 관리자를 사용하여 원격 데스크톱 라이선싱 모드 및 라이선스 서버를 지정하십시오."라는 알림을 받을 수 있습니다.

시간 제한이 만료되면 "이 컴퓨터에 대해 사용 가능한 원격 데스크톱 서버 클라이언트 액세스 라이선스가 없으므로 원격 세션 연결이 끊어졌습니다."라는 오류 메시지가 표시됩니다.

이러한 메시지 중 하나가 표시되면 최신 Windows 업데이트가 이미지에 설치되어 있지 않거나 그룹 정책을 통해 원격 데스크톱 라이선싱 모드를 설정하고 있음을 의미합니다. 다음 섹션의 단계에 따라 그룹 정책 설정을 확인하고, Windows 10 Enterprise 다중 세션의 버전을 식별하고, 해당 업데이트를 설치합니다.

>[!NOTE]
>호스트 풀에 Windows Server 세션 호스트가 포함된 경우에만 Azure Virtual Desktop에 RDS CAL(클라이언트 액세스 라이선스)이 필요합니다. RDS CAL을 구성하는 방법은 [클라이언트 액세스 라이선스를 사용하여 RDS 배포 라이선싱](/windows-server/remote/remote-desktop-services/rds-client-access-license/)을 참조하세요.

### <a name="disable-the-remote-desktop-licensing-mode-group-policy-setting"></a>원격 데스크톱 라이선싱 모드 그룹 정책 설정을 사용 안 함

VM에서 그룹 정책 편집기를 열고, **관리 템플릿** > **Windows 구성 요소** > **원격 데스크톱 서비스** > **원격 데스크톱 세션 호스트** > **라이선싱** > **터미널 서비스 라이선스 모드 설정** 으로 차례로 이동하여 그룹 정책 설정을 확인합니다. 그룹 정책 설정이 **사용** 인 경우 **사용 안 함** 으로 변경합니다. 이미 사용하지 않도록 설정된 경우 그대로 둡니다.

>[!NOTE]
>도메인을 통해 그룹 정책을 설정하는 경우 이러한 Windows 10 Enterprise 다중 세션 VM을 대상으로 하는 정책에서 이 설정을 사용하지 않도록 설정합니다.

### <a name="identify-which-version-of-windows-10-enterprise-multi-session-youre-using"></a>사용 중인 Windows 10 Enterprise 다중 세션 버전 확인

사용하는 Windows 10 Enterprise 다중 세션의 버전을 확인하려면 다음을 수행합니다.

1. 관리자 계정으로 로그인합니다.
2. 시작 메뉴 옆의 검색 창에서 "정보"를 입력합니다.
3. **PC 정보** 를 선택합니다.
4. "버전" 옆의 번호를 확인합니다. 번호는 다음 이미지와 같이 "1809" 또는 "1903"이어야 합니다.

    > [!div class="mx-imgBorder"]
    > ![Windows 사양 창의 스크린샷. 버전 번호가 파란색으로 강조 표시되어 있습니다.](../media/windows-specifications.png)

이제 버전 번호를 알고 있으므로 관련 섹션으로 건너뜁니다.

### <a name="version-1809"></a>버전 1809

버전 번호가 "1809"인 경우 [KB4516077 업데이트](https://support.microsoft.com/help/4516077)를 설치합니다.

### <a name="version-1903"></a>버전 1903

Azure Gallery에서 최신 버전의 Windows 10 버전 1903 이미지를 사용하여 호스트 운영 체제를 다시 배포합니다.

## <a name="we-couldnt-connect-to-the-remote-pc-because-of-a-security-error"></a>보안 오류로 인해 원격 PC에 연결할 수 없음

“보안 오류로 인해 원격 PC에 연결할 수 없습니다. 이 문제가 계속 발생하면 관리자 또는 기술 지원팀에 도움을 요청하세요.”라는 오류가 사용자에게 표시되는 경우 기본 RDP 권한을 변경하는 기존 정책의 유효성을 검사합니다. 이 오류가 발생할 수 있는 정책 중 하나는 “원격 데스크톱 서비스를 통한 로그온 허용 보안 정책”입니다.

이 정책에 대한 자세한 내용은 [원격 데스크톱 서비스를 통한 로그온 허용](/windows/security/threat-protection/security-policy-settings/allow-log-on-through-remote-desktop-services)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- Azure Virtual Desktop과 에스컬레이션 트랙 문제 해결에 대한 개요는 [문제 해결 개요, 피드백 및 지원](troubleshoot-set-up-overview-2019.md)을 참조하세요.
- Azure Virtual Desktop 환경에서 테넌트와 호스트 풀을 만드는 동안 발생하는 문제를 해결하려면 [테넌트 및 호스트 풀 만들기](troubleshoot-set-up-issues-2019.md)를 참조하세요.
- Azure Virtual Desktop에서 VM(가상 머신)을 구성하는 동안 문제를 해결하려면 [세션 호스트 가상 머신 구성](troubleshoot-vm-configuration-2019.md)을 참조하세요.
- Azure Virtual Desktop 클라이언트 연결 문제를 해결하려면 [Azure Virtual Desktop 서비스 연결](troubleshoot-service-connection-2019.md)을 참조하세요.
- 원격 데스크톱 클라이언트와 관련된 문제를 해결하려면 [원격 데스크톱 클라이언트 문제 해결](../troubleshoot-client.md)을 참조하세요.
- Azure Virtual Desktop과 함께 PowerShell을 사용할 때 발생하는 문제를 해결하려면 [Azure Virtual Desktop PowerShell](troubleshoot-powershell-2019.md)을 참조하세요.
- 서비스에 대한 자세한 내용은 [Azure Virtual Desktop 환경](environment-setup-2019.md)을 참조하세요.
- 문제 해결 자습서를 진행하려면 [자습서: Resource Manager 템플릿 배포 문제 해결](../../azure-resource-manager/templates/template-tutorial-troubleshoot.md)을 참조하세요.
- 감사 작업에 대해 알아보려면 [리소스 관리자로 작업 감사](../../azure-monitor/essentials/activity-log.md)를 참조하세요.
- 배포 중 오류를 확인하는 작업에 대해 알아보려면 [배포 작업 보기](../../azure-resource-manager/templates/deployment-history.md)를 참조하세요.
