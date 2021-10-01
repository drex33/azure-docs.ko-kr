---
title: 하이브리드 FIDO2 보안 키에 대한 알려진 문제 및 문제 해결 - Azure Active Directory
description: Azure Active Directory를 사용하는 암호 없는 하이브리드 FIDO2 보안 키 로그인에서 알려진 몇 가지 문제 및 문제 해결 방법을 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 02/22/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 70230b5324d94065e414e0bc76d7e2f092369ba9
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123431985"
---
# <a name="troubleshooting-for-hybrid-deployments-of-fido2-security-keys-in-azure-ad"></a>Azure AD에서 FIDO2 보안 키 하이브리드 배포에 대한 문제 해결 

이 문서에서는 하이브리드 Azure AD 조인 디바이스 및 온-프레미스 리소스에 대한 암호 없는 로그인에 대한 질문과 대답을 다룹니다. 이 암호 없는 기능을 사용하면 FIDO2 보안 키를 사용하여 하이브리드 Azure AD 조인 디바이스에 대해 Windows 10 디바이스에서 Azure AD 인증을 사용하도록 설정할 수 있습니다. 사용자는 FIDO2 키와 같은 최신 자격 증명을 사용하여 디바이스에서 Windows에 로그인하고, 온-프레미스 리소스에 대해 원활한 SSO(Single Sign-On) 환경을 사용하여 기존 Active Directory Domain Services(AD DS) 기반 리소스에 액세스할 수 있습니다.

하이브리드 환경의 사용자를 위해 다음 시나리오가 지원됩니다.

* FIDO2 보안 키를 사용하여 하이브리드 Azure AD 조인 디바이스에 로그인하고 온-프레미스 리소스에 대한 SSO 액세스를 가져옵니다.
* FIDO2 보안 키를 사용하여 Azure AD 조인 디바이스에 로그인하고 온-프레미스 리소스에 대한 SSO 액세스를 가져옵니다.

FIDO2 보안 키 및 온-프레미스 리소스에 대한 하이브리드 액세스를 시작하려면 다음 문서를 참조하세요.

* [암호 없는 보안 키](howto-authentication-passwordless-security-key.md)
* [암호 없는 Windows 10](howto-authentication-passwordless-security-key-windows.md)
* [암호 없는 온-프레미스](howto-authentication-passwordless-security-key-on-premises.md)

## <a name="known-issues"></a>알려진 문제

* [Windows Hello 얼굴이 너무 빠른 기본 로그인 메커니즘이기 때문에 사용자가 FIDO2 보안 키를 사용할 수 없습니다.](#users-are-unable-to-sign-in-using-fido2-security-keys-as-windows-hello-face-is-too-quick-and-is-the-default-sign-in-mechanism)
* [사용자가 하이브리드 Azure AD 조인 컴퓨터를 만든 직후에 FIDO2 보안 키를 사용할 수 없습니다.](#users-arent-able-to-use-fido2-security-keys-immediately-after-they-create-a-hybrid-azure-ad-joined-machine)
* [사용자가 FIDO2 보안 키로 로그인하고 자격 증명 프롬프트를 받은 후에 내 NTLM 네트워크 리소스에 대한 SSO를 가져올 수 없습니다.](#users-are-unable-to-get-sso-to-my-ntlm-network-resource-after-signing-in-with-a-fido2-security-key-and-receiving-a-credential-prompt)

### <a name="users-are-unable-to-sign-in-using-fido2-security-keys-as-windows-hello-face-is-too-quick-and-is-the-default-sign-in-mechanism"></a>Windows Hello 얼굴이 너무 빠른 기본 로그인 메커니즘이기 때문에 사용자가 FIDO2 보안 키를 사용할 수 없습니다.

Windows Hello 얼굴은 사용자가 등록되는 디바이스에서 최상의 환경입니다. FIDO2 보안 키는 공유 디바이스에서 또는 비즈니스용 Windows Hello 등록이 불가능한 경우 사용하기 위한 것입니다.

Windows Hello 얼굴에서 FIDO2 보안 키 로그인 시나리오를 허용하지 않는 경우 사용자는 **설정 > 로그인 옵션** 에서 얼굴 등록을 제거하여 Hello 얼굴 로그인을 해제할 수 있습니다.

### <a name="users-arent-able-to-use-fido2-security-keys-immediately-after-they-create-a-hybrid-azure-ad-joined-machine"></a>사용자가 하이브리드 Azure AD 조인 컴퓨터를 만든 직후에 FIDO2 보안 키를 사용할 수 없습니다.

하이브리드 Azure AD 조인 컴퓨터를 새로 설치할 때 도메인 조인 및 다시 시작 프로세스 후에는 암호를 사용하여 로그인하고 정책이 동기화될 때까지 기다려야 FIDO2 보안 키를 사용하여 로그인할 수 있습니다.

이 동작은 도메인 조인 디바이스에 대한 알려진 제한 사항이며 FIDO2 보안 키에 국한되지 않습니다.

현재 상태를 확인하려면 `dsregcmd /status` 명령을 사용합니다. *AzureAdJoined* 및 *DomainJoined* 모두 예로 표시되는지 확인합니다.

### <a name="users-are-unable-to-get-sso-to-my-ntlm-network-resource-after-signing-in-with-a-fido2-security-key-and-receiving-a-credential-prompt"></a>사용자가 FIDO2 보안 키로 로그인하고 자격 증명 프롬프트를 받은 후에 내 NTLM 네트워크 리소스에 대한 SSO를 가져올 수 없습니다.

제시간에 응답하여 리소스 요청을 처리하는 데 충분한 DC가 패치되었는지 확인합니다. 기능을 실행하는 서버를 볼 수 있는지 확인하려면 `nltest /dsgetdc:<dc name> /keylist /kdc`의 출력을 검토합니다.

이 기능을 사용하여 DC를 볼 수 있는 경우 사용자의 암호가 로그인 이후에 변경되었거나 다른 문제가 있을 수 있습니다. Microsoft 지원 팀이 디버그할 수 있도록 다음 섹션에서 설명하는 대로 로그를 수집합니다.

## <a name="troubleshoot"></a>문제 해결

문제 해결에는 [Window 클라이언트 문제](#windows-client-issues) 및 [배포 문제](#deployment-issues)의 두 가지 영역이 있습니다.

### <a name="windows-client-issues"></a>Windows 클라이언트 문제

Windows에 로그인하거나 Windows 10 디바이스에서 온-프레미스 리소스에 액세스하는 것과 관련된 문제를 해결하는 데 도움이 되는 데이터를 수집하려면 다음 단계를 완료합니다.

1. **피드백 허브** 앱을 엽니다. 앱의 왼쪽 아래에 이름이 있는지 확인하고 **새 피드백 항목 만들기** 를 선택합니다.

    피드백 항목 유형으로 문제를 선택합니다.

1. 보안 및 개인 정보 범주, FIDO 하위 범주를 차례로 선택합니다.
1. 내 피드백과 함께 첨부 파일 및 진단을 Microsoft에 보내기 확인란을 전환합니다.
1. 내 문제 다시 만들기를 선택한 다음, 캡처 시작을 선택합니다.
1. FIDO2 보안 키를 사용하여 컴퓨터를 잠갔다가 잠금 해제합니다. 문제가 발생하는 경우 다른 자격 증명을 사용하여 잠금 해제를 시도해보세요.
1. **피드백 허브** 로 돌아가서 **캡처 중지** 를 선택하고 피드백을 제출합니다.
1. 피드백 페이지의 내 피드백 탭으로 이동합니다. 최근에 제출한 피드백을 선택합니다.
1. 오른쪽 위 모서리에서 공유 단추를 선택하여 피드백 링크를 가져옵니다. 지원 사례를 개설하거나 기존 지원 사례에 할당된 엔지니어에게 회신하는 경우 이 링크를 포함합니다.

다음 이벤트 로그 및 레지스트리 키 정보가 수집됩니다.

**레지스트리 키**

* *HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\FIDO [\*]*
* *HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\PasswordForWork\* [\*]*
* *HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Policies\PasswordForWork\* [\*]*

**진단 정보**

* 라이브 커널 덤프
* AppX 패키지 정보 수집
* UIF 컨텍스트 파일

**이벤트 로그**

* *%SystemRoot%\System32\winevt\Logs\Microsoft-Windows-AAD%40Operational.evtx*
* *%SystemRoot%\System32\winevt\Logs\Microsoft-Windows-WebAuthN%40Operational.evtx*
* *%SystemRoot%\System32\winevt\Logs\Microsoft-Windows-HelloForBusiness%40Operational.evtx*

### <a name="deployment-issues"></a>배포 문제

Azure AD Kerberos 서버 배포 문제를 해결하려면 Azure AD Connect에 포함된 새 PowerShell 모듈을 사용합니다.

#### <a name="viewing-the-logs"></a>로그 보기

Azure AD Kerberos 서버 PowerShell cmdlet은 표준 Azure AD Connect 마법사와 동일한 로깅을 사용합니다. 이러한 cmdlet에서 정보 또는 오류 세부 정보를 보려면 다음 단계를 완료합니다.

1. Azure AD Connect 서버에서 `C:\ProgramData\AADConnect\`로 이동합니다. 이 폴더는 기본적으로 숨겨져 있습니다.
1. 디렉터리에 있는 가장 최근의 `trace-*.log` 파일을 열고 확인합니다.

#### <a name="viewing-the-azure-ad-kerberos-server-objects"></a>Azure AD Kerberos 서버 개체 보기

Azure AD Kerberos 서버 개체가 올바른지 확인하려면 다음 단계를 완료합니다.

1. Azure AD Connect 서버에서 PowerShell을 열고 `C:\Program Files\Microsoft Azure Active Directory Connect\AzureADKerberos\`로 이동합니다.
1. 다음 PowerShell 명령을 실행하여 Azure AD와 온-프레미스 AD DS 모두에서 Azure AD Kerberos 서버를 확인합니다.

    *corp.contoso.com* 을 온-프레미스 AD DS 도메인의 이름으로 바꿉니다.

    ```powershell
    Import-Module ".\AzureAdKerberos.psd1"

    # Specify the on-premises AD DS domain.
    $domain = "corp.contoso.com"

    # Enter an Azure Active Directory Global Admin username and password.
    $cloudCred = Get-Credential

    # Enter a Domain Admin username and password.
    $domainCred = Get-Credential

    # Get the Azure AD Kerberos Server Object
    Get-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential
    $domainCred
    ```

이 명령은 Azure AD 및 온-프레미스 AD DS 모두에서 Azure AD Kerberos 서버의 속성을 출력합니다. 속성을 검토하여 모든 항목이 올바른지 확인할 수 있습니다. 아래 표를 사용하여 속성을 확인합니다.

첫 번째 속성 집합은 온-프레미스 AD DS 환경의 개체에서 가져온 것입니다. 두 번째 절반(*Cloud**로 시작하는 속성)은 Azure AD의 Kerberos 서버 개체에서 가져온 것입니다.

| 속성           | 설명  |
|--------------------|--------------|
| Id                 | AD DS 도메인 컨트롤러 개체의 고유한 *ID* 입니다. |
| DomainDnsName      | AD DS 도메인의 DNS 도메인 이름입니다. |
| ComputerAccount    | Azure AD Kerberos 서버 개체(DC)의 머신 계정 개체입니다. |
| UserAccount        | Azure AD Kerberos 서버 TGT 암호화 키를 보유한 사용하지 않도록 설정된 사용자 계정 개체입니다. 이 계정의 DN은 *CN=krbtgt_AzureAD,CN=Users,\<Domain-DN\>* 입니다. |
| KeyVersion         | Azure AD Kerberos 서버 TGT 암호화 키의 키 버전입니다. 버전은 키를 만들 때 할당됩니다. 그런 다음, 키를 회전할 때마다 버전이 증가합니다. 증분은 복제 메타데이터를 기반으로 하며 1보다 클 수 있습니다.<br /><br /> 예를 들어 초기 *KeyVersion* 은 *192272* 일 수 있습니다. 키를 처음 회전할 때 버전은 *212621* 로 이동될 수 있습니다.<br /><br /> 확인해야 하는 중요한 점은 온-프레미스 개체의 *KeyVersion* 과 클라우드 개체의 *CloudKeyVersion* 이 동일한지 확인하는 것입니다. |
| KeyUpdatedOn       | Azure AD Kerberos 서버 TGT 암호화 키가 업데이트 또는 생성된 날짜와 시간입니다. |
| KeyUpdatedFrom     | Azure AD Kerberos 서버 TGT 암호화 키가 마지막으로 업데이트된 DC입니다. |
| CloudId            | Azure AD 개체의 *ID* 입니다. 위의 *ID* 와 일치해야 합니다. |
| CloudDomainDnsName | Azure AD 개체의 *DomainDnsName* 입니다. 위의 *DomainDnsName* 과 일치해야 합니다. |
| CloudKeyVersion    | Azure AD 개체의 *KeyVersion* 입니다. 위의 *KeyVersion* 과 일치해야 합니다. |
| CloudKeyUpdatedOn  | Azure AD 개체의 *KeyUpdatedOn* 입니다. 위의 *KeyUpdatedOn* 과 일치해야 합니다. |

## <a name="next-steps"></a>다음 단계

FIDO2 보안 키 및 온-프레미스 리소스에 대한 하이브리드 액세스를 시작하려면 다음 문서를 참조하세요.

* [암호 없는 FIDO2 보안 키](howto-authentication-passwordless-security-key.md)
* [암호 없는 Windows 10](howto-authentication-passwordless-security-key-windows.md)
* [암호 없는 온-프레미스](howto-authentication-passwordless-security-key-on-premises.md)
