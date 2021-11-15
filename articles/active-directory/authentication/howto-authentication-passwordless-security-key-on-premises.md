---
title: 온-프레미스 리소스에 대한 암호 없는 보안 키 로그인 - Azure Active Directory
description: Azure Active Directory를 사용하여 온-프레미스 리소스에 대한 암호 없는 보안 키 로그인을 사용하도록 설정하는 방법 알아보기
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 02/22/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: librown, aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: ce8869a15a1c94ab2f3227780de8eb37bbf52b6f
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131505425"
---
# <a name="enable-passwordless-security-key-sign-in-to-on-premises-resources-by-using-azure-ad"></a>Azure AD를 사용하여 온-프레미스 리소스에 대한 암호 없는 보안 키 로그인을 사용하도록 설정 

이 문서에서는 *Azure AD(Azure Active Directory) 조인* 및 *하이브리드 Azure AD 조인* Windows 10 디바이스를 모두 사용하는 환경에서 온-프레미스 리소스에 대해 암호 없는 인증을 사용하는 방법을 설명합니다. 이러한 암호 없는 인증 기능은 Microsoft 호환 보안 키를 사용하는 경우 온-프레미스 리소스에 대한 원활한 SSO(Single Sign-On)를 제공합니다.

## <a name="use-sso-to-sign-in-to-on-premises-resources-by-using-fido2-keys"></a>SSO를 사용하여 FIDO2 키를 통해 온-프레미스 리소스에 로그인

Azure AD는 하나 이상의 Active Directory 도메인에 대해 Kerberos TGT(티켓 부여 티켓)를 발급할 수 있습니다. 이 기능을 사용하면 사용자는 FIDO2 보안 키와 같은 최신 자격 증명으로 Windows에 로그인한 다음, 기존 Active Directory 기반 리소스에 액세스할 수 있습니다. Kerberos 서비스 티켓 및 권한 부여는 온-프레미스 Active Directory DC(도메인 컨트롤러)에 의해 계속 제어됩니다.

Azure AD Kerberos 서버 개체는 온-프레미스 Active Directory 인스턴스에서 생성된 다음, Azure Active Directory에 안전하게 게시됩니다. 개체는 물리적 서버와 연결되어 있지 않습니다. Azure Active Directory에서 Active Directory 도메인에 대한 Kerberos TGT를 생성하는 데 사용할 수 있는 리소스입니다.

:::image type="Image" source="./media/howto-authentication-passwordless-on-premises/fido2-ticket-granting-ticket-exchange-process.png" alt-text="Azure AD 및 Active Directory Domain Services에서 TGT를 가져오는 방법을 보여 주는 다이어그램" lightbox="./media/howto-authentication-passwordless-on-premises/fido2-ticket-granting-ticket-exchange-process.png":::

1. 사용자가 FIDO2 보안 키를 사용하여 Windows 10 디바이스에 로그인하고 Azure AD에 인증합니다.
1. Azure AD는 디렉터리에서 사용자의 온-프레미스 Active Directory 도메인과 일치하는 Kerberos 서버 키를 확인합니다.

   Azure AD는 사용자의 온-프레미스 Active Directory 도메인에 대한 Kerberos TGT를 생성합니다. TGT에는 사용자의 SID만 포함되며 권한 부여 데이터는 포함되지 않습니다.

1. TGT는 사용자의 Azure AD PRT(기본 새로 고침 토큰)와 함께 클라이언트에 반환됩니다.
1. 클라이언트 머신은 온-프레미스 Azure AD DC에 연결하고 완전히 구성된 TGT의 부분 TGT를 조정합니다.
1. 클라이언트 머신에는 이제 Azure AD PRT와 전체 Active Directory TGT가 있으며 클라우드 및 온-프레미스 리소스 모두에 액세스할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

이 문서의 절차를 시작하기 전에 조직에서 [Windows 10 디바이스에 대해 암호 없는 보안 키 로그인 사용](howto-authentication-passwordless-security-key.md)의 지침을 완료해야 합니다.

또한 다음과 같은 시스템 요구 사항을 충족해야 합니다.

- 디바이스는 Windows 10 버전 2004 이상을 실행해야 합니다.

- [Azure AD Connect 버전 1.4.32.0 이상](../hybrid/how-to-connect-install-roadmap.md#install-azure-ad-connect)을 실행해야 합니다.
  - 사용 가능한 Azure AD 하이브리드 인증 옵션에 대한 자세한 내용은 다음 문서를 참조하세요.
    - [Azure AD 하이브리드 ID 솔루션에 적합한 인증 방법 선택](../hybrid/choose-ad-authn.md)
    - [Azure AD Connect에 사용할 설치 유형 선택](../hybrid/how-to-connect-install-select-installation.md)

- Windows Server 도메인 컨트롤러에는 다음 서버에 대한 패치가 설치되어 있어야 합니다.
    - [Windows Server 2016](https://support.microsoft.com/help/4534307/windows-10-update-kb4534307)
    - [Windows Server 2019](https://support.microsoft.com/help/4534321/windows-10-update-kb4534321)

### <a name="supported-scenarios"></a>지원되는 시나리오

이 문서의 시나리오에서는 다음 두 인스턴스의 SSO를 모두 지원합니다.

- Microsoft 365 및 기타 SAML(Security Assertion Markup Language) 지원 애플리케이션과 같은 클라우드 리소스
- 온-프레미스 리소스 및 웹 사이트에 대한 Windows 통합 인증 리소스에는 IIS 인증을 요구하는 웹 사이트와 SharePoint 사이트 및/또는 NTLM 인증을 사용하는 리소스가 포함될 수 있습니다.

### <a name="unsupported-scenarios"></a>지원되지 않는 시나리오

다음 시나리오는 지원되지 않습니다.

- Windows Server AD DS(Active Directory Domain Services) 조인(온-프레미스 전용 디바이스) 배포
- 보안 키를 사용하여 RDP(원격 데스크톱 프로토콜), VDI(가상 데스크톱 인프라) 및 Citrix 시나리오
- 보안 키를 사용하여 S/MIME
- 보안 키를 사용하여 *다음 계정으로 실행*
- 보안 키를 사용하여 서버에 로그인합니다.

## <a name="install-the-azure-ad-kerberos-powershell-module"></a>Azure AD Kerberos PowerShell 모듈 설치

[Azure AD Kerberos PowerShell 모듈](https://www.powershellgallery.com/packages/AzureADHybridAuthenticationManagement)은 관리자를 위한 FIDO2 관리 기능을 제공합니다.

1. 관리자 권한으로 실행 옵션을 사용하여 PowerShell 프롬프트를 엽니다.
1. Azure AD Kerberos PowerShell 모듈 설치:

   ```powershell
   # First, ensure TLS 1.2 for PowerShell gallery access.
   [Net.ServicePointManager]::SecurityProtocol = [Net.ServicePointManager]::SecurityProtocol -bor [Net.SecurityProtocolType]::Tls12

   # Install the Azure AD Kerberos PowerShell Module.
   Install-Module -Name AzureADHybridAuthenticationManagement -AllowClobber
   ```

> [!NOTE]
> - Azure AD Kerberos PowerShell 모듈은 [AzureADPreview PowerShell 모듈](https://www.powershellgallery.com/packages/AzureADPreview)을 사용하여 고급 Azure Active Directory 관리 기능을 제공합니다. [AzureAD PowerShell 모듈](https://www.powershellgallery.com/packages/AzureAD)이 로컬 컴퓨터에 이미 설치되어 있는 경우 여기에 설명된 설치는 충돌로 인해 실패할 수 있습니다. 설치하는 동안 충돌을 방지하려면 "-AllowClobber" 옵션 플래그를 포함해야 합니다.
> - Azure AD Connect 솔루션에 대한 종속성 없이 온-프레미스 Active Directory 도메인 컨트롤러에 액세스할 수 있는 모든 컴퓨터에 Azure AD Kerberos PowerShell 모듈을 설치할 수 있습니다.
> - Azure AD Kerberos PowerShell 모듈은 [PowerShell 갤러리](https://www.powershellgallery.com/)를 통해 배포됩니다. PowerShell 갤러리는 PowerShell 콘텐츠의 중앙 리포지토리입니다. PowerShell 갤러리에서 PowerShell 명령 및 DSC(Desired State Configuration) 리소스를 포함하는 유용한 PowerShell 모듈을 찾을 수 있습니다.

## <a name="create-a-kerberos-server-object"></a>Kerberos 서버 개체 만들기

관리자는 Azure AD Kerberos PowerShell 모듈을 사용하여 온-프레미스 디렉터리에 Azure AD Kerberos 서버 개체를 만듭니다.

Azure AD 사용자를 포함하는 조직의 각 도메인 및 포리스트에서 다음 단계를 실행합니다.

1. 관리자 권한으로 실행 옵션을 사용하여 PowerShell 프롬프트를 엽니다.
1. 다음 PowerShell 명령을 실행하여 온-프레미스 Active Directory 도메인 및 Azure Active Directory 테넌트 모두에서 새 Azure AD Kerberos 서버 개체를 만듭니다.

   > [!NOTE]
   > 다음 예제에서 `contoso.corp.com`을 온-프레미스 Active Directory 도메인 이름으로 바꿉니다.

   ```powershell
   # Specify the on-premises Active Directory domain. A new Azure AD
   # Kerberos Server object will be created in this Active Directory domain.
   $domain = "contoso.corp.com"

   # Enter an Azure Active Directory global administrator username and password.
   $cloudCred = Get-Credential

   # Enter a domain administrator username and password.
   $domainCred = Get-Credential

   # Create the new Azure AD Kerberos Server object in Active Directory
   # and then publish it to Azure Active Directory.
   Set-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred
   ```

   > [!NOTE]
   > 도메인 조인 머신에서 도메인 관리자 권한이 있는 계정을 사용하여 작업하는 경우 "-DomainCredential" 매개 변수를 건너뛸 수 있습니다. "-DomainCredential" 매개 변수를 제공하지 않으면 현재 Windows 로그인 자격 증명을 사용하여 온-프레미스 Active Directory 도메인 컨트롤러에 액세스합니다.

   ```powershell
   # Specify the on-premises Active Directory domain. A new Azure AD
   # Kerberos Server object will be created in this Active Directory domain.
   $domain = "contoso.corp.com"

   # Enter an Azure Active Directory global administrator username and password.
   $cloudCred = Get-Credential

   # Create the new Azure AD Kerberos Server object in Active Directory
   # and then publish it to Azure Active Directory.
   # Use the current windows login credential to access the on-prem AD.
   Set-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred
   ```

   > [!NOTE]
   > 조직에서 암호 기반 로그인을 보호하고 다단계 인증, FIDO2 또는 스마트 카드 기술과 같은 최신 인증 방법을 적용하는 경우 전역 관리자의 UPN(사용자 계정 이름)과 함께 `-UserPrincipalName` 매개 변수를 사용해야 합니다.
   > - 다음 예제에서 `contoso.corp.com`을 온-프레미스 Active Directory 도메인 이름으로 바꿉니다.
   > - 다음 예제의 `administrator@contoso.onmicrosoft.com`을 전역 관리자의 UPN으로 바꿉니다.

   ```powershell
   # Specify the on-premises Active Directory domain. A new Azure AD
   # Kerberos Server object will be created in this Active Directory domain.
   $domain = "contoso.corp.com"

   # Enter a UPN of an Azure Active Directory global administrator
   $userPrincipalName = "administrator@contoso.onmicrosoft.com"

   # Enter a domain administrator username and password.
   $domainCred = Get-Credential

   # Create the new Azure AD Kerberos Server object in Active Directory
   # and then publish it to Azure Active Directory.
   # Open an interactive sign-in prompt with given username to access the Azure AD.
   Set-AzureADKerberosServer -Domain $domain -UserPrincipalName $userPrincipalName -DomainCredential $domainCred
   ```

### <a name="view-and-verify-the-azure-ad-kerberos-server"></a>Azure AD Kerberos 서버 보기 및 확인

다음 명령을 사용하여 새로 만든 Azure AD Kerberos 서버를 보고 확인할 수 있습니다.

```powershell
Get-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred
```

이 명령은 Azure AD Kerberos 서버의 속성을 출력합니다. 속성을 검토하여 모든 것이 올바른 순서인지 확인할 수 있습니다.

> [!NOTE]
> 자격 증명을 제공하여 다른 도메인에 대해 실행하면 NTLM을 통해 연결된 후 실패합니다. 사용자가 Active Directory의 보호된 사용자 보안 그룹에 있는 경우 다음 단계를 수행하여 문제를 해결합니다. **ADConnect** 의 다른 도메인 사용자로 로그인하고 "-domainCredential"을 제공하지 않습니다. 현재 로그인되어 있는 사용자의 Kereberos 티켓이 사용됩니다. `whoami /groups`를 실행하여 Active Directory에서 사용자에게 이전 명령을 실행하는 데 필요한 권한이 있는지 여부를 확인하여 확인할 수 있습니다.
 
| 속성 | Description |
| --- | --- |
| ID | AD DS DC 개체의 고유 ID입니다. 이 ID는 *슬롯* 또는 *분기 ID* 라고도 합니다. |
| DomainDnsName | Active Directory 도메인의 DNS 도메인 이름입니다. |
| ComputerAccount | Azure AD Kerberos 서버 개체(DC)의 머신 계정 개체입니다. |
| UserAccount | Azure AD Kerberos 서버 TGT 암호화 키를 보유한 사용하지 않도록 설정된 사용자 계정 개체입니다. 이 계정의 도메인 이름은 `CN=krbtgt_AzureAD,CN=Users,<Domain-DN>`입니다. |
| KeyVersion | Azure AD Kerberos 서버 TGT 암호화 키의 키 버전입니다. 버전은 키를 만들 때 할당됩니다. 그런 다음, 키를 회전할 때마다 버전이 증가합니다. 증분은 복제 메타데이터를 기반으로 하며 1보다 클 수 있습니다. 예를 들어 초기 *KeyVersion* 은 *192272* 일 수 있습니다. 키를 처음 회전할 때 버전은 *212621* 로 이동될 수 있습니다. 확인해야 하는 중요한 점은 온-프레미스 개체의 *KeyVersion* 과 클라우드 개체의 *CloudKeyVersion* 이 동일한지 확인하는 것입니다. |
| KeyUpdatedOn | Azure AD Kerberos 서버 TGT 암호화 키가 업데이트 또는 생성된 날짜와 시간입니다. |
| KeyUpdatedFrom | Azure AD Kerberos 서버 TGT 암호화 키가 마지막으로 업데이트된 DC입니다. |
| CloudId | Azure AD 개체의 ID입니다. 테이블의 첫 번째 줄에 있는 ID와 일치해야 합니다. |
| CloudDomainDnsName | Azure AD 개체의 *DomainDnsName* 입니다. 테이블의 두 번째 줄에서 *DomainDnsName* 과 일치해야 합니다. |
| CloudKeyVersion | Azure AD 개체의 *KeyVersion* 입니다. 테이블의 다섯 번째 줄에서 *KeyVersion* 과 일치해야 합니다. |
| CloudKeyUpdatedOn | Azure AD 개체의 *KeyUpdatedOn* 입니다. 테이블의 여섯 번째 줄에서 *KeyUpdatedOn* 과 일치해야 합니다. |
| | |

### <a name="rotate-the-azure-ad-kerberos-server-key"></a>Azure AD Kerberos 서버 키 회전

Azure AD Kerberos 서버 암호화 *krbtgt* 키는 정기적으로 회전해야 합니다. 다른 모든 Active Directory DC *krbtgt* 키를 회전하는 데 사용하는 것과 동일한 일정을 따르는 것이 좋습니다.

> [!WARNING]
> *krbtgt* 키를 회전할 수 있는 다른 도구가 있습니다. 그러나 이 문서에 언급된 도구를 사용하여 Azure AD Kerberos 서버의 *krbtgt* 키를 회전해야 합니다. 이렇게 하면 키가 온-프레미스 Active Directory 및 Azure AD에서 모두 업데이트됩니다.

```powershell
Set-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred -RotateServerKey
```

### <a name="remove-the-azure-ad-kerberos-server"></a>Azure AD Kerberos 서버 제거

시나리오를 되돌리고 온-프레미스 Active Directory 및 Azure AD에서 Azure AD Kerberos 서버를 제거하려면 다음 명령을 실행합니다.

```powershell
Remove-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred
```

### <a name="multiforest-and-multidomain-scenarios"></a>다중 포리스트 및 다중 도메인 시나리오

Azure AD Kerberos 서버 개체는 Azure AD에서 *KerberosDomain* 개체로 표시됩니다. 각 온-프레미스 Active Directory 도메인은 Azure AD에서 단일 *KerberosDomain* 개체로 표현됩니다.

예를 들어 조직에 `contoso.com` 및 `fabrikam.com`이라는 두 개의 도메인이 있는 Active Directory 포리스트가 있다고 가정해 보겠습니다. Azure AD에서 전체 포리스트에 대해 Kerberos TGT를 발급하도록 선택하는 경우 Azure AD에는 두 개의 *KerberosDomain* 개체 및 `contoso.com`에 대한 한 개의 *KerberosDomain* 개체 및 `fabrikam.com`에 대한 다른 개체가 있습니다. Active Directory 포리스트가 여러 개인 경우 각 포리스트의 각 도메인에 대해 하나의 *KerberosDomain* 개체가 있습니다.

Azure AD 사용자를 포함하는 조직의 각 도메인 및 포리스트에서 [Kerberos 서버 개체 만들기](#create-a-kerberos-server-object)의 지침을 따릅니다.

## <a name="known-behavior"></a>알려진 동작

암호가 만료되면 FIDO를 통한 로그인이 차단됩니다. 사용자가 FIDO를 사용하여 로그인하기 전에 암호를 재설정할 것으로 기대됩니다.

## <a name="troubleshooting-and-feedback"></a>문제 해결 및 피드백

문제가 발생하거나 이 암호 없는 보안 키 로그인 기능에 대한 피드백을 공유하려면 다음을 수행하여 Windows 피드백 허브 앱을 통해 공유합니다.

1. **피드백 허브** 를 열고 로그인했는지 확인합니다.
1. 다음 범주를 선택하여 피드백을 제출합니다.
   - 범주: 보안 및 개인 정보
   - 하위 범주: FIDO
1. 로그를 캡처하려면 **문제 다시 만들기** 옵션을 사용합니다.

## <a name="passwordless-security-key-sign-in-faq"></a>암호 없는 보안 키 로그인 FAQ

암호 없는 로그인에 대한 일반적인 질문에 대한 몇 가지 답변은 다음과 같습니다.

### <a name="does-passwordless-security-key-sign-in-work-in-my-on-premises-environment"></a>내 온-프레미스 환경에서 암호 없는 보안 키 로그인이 작동하나요?

이 기능은 순수 온-프레미스 AD DS 환경에서 작동하지 않습니다.

### <a name="my-organization-requires-two-factor-authentication-to-access-resources-what-can-i-do-to-support-this-requirement"></a>조직에서 리소스에 액세스하려면 2단계 인증이 필요합니다. 이 요구 사항을 지원하기 위해 수행할 수 있는 작업은 무엇인가요?

보안 키는 다양한 폼 팩터를 제공합니다. 레코드의 디바이스 제조업체에 문의하여 PIN 또는 생체 인식을 두 번째 요소로 사용하여 디바이스를 활성화하는 방법을 알아봅니다.

### <a name="can-administrators-set-up-security-keys"></a>관리자가 보안 키를 설정할 수 있나요?

이 기능의 GA(일반 공급) 릴리스를 위해 최선을 다하고 있습니다.

### <a name="where-can-i-go-to-find-compliant-security-keys"></a>어디서 규격 보안 키를 찾을 수 있나요?

규격 보안 키에 대한 자세한 내용은 [FIDO2 보안 키](concept-authentication-passwordless.md#fido2-security-keys)를 참조하세요.

### <a name="what-can-i-do-if-i-lose-my-security-key"></a>내 보안 키를 분실한 경우 어떻게 해야 하나요?

보안 키를 검색하려면 Azure Portal에 로그인한 다음, **보안 정보** 페이지로 이동합니다.

### <a name="what-can-i-do-if-im-unable-to-use-the-fido-security-key-immediately-after-i-create-a-hybrid-azure-ad-joined-machine"></a>하이브리드 Azure AD 조인 머신을 만든 직후 FIDO 보안 키를 사용할 수 없는 경우 어떻게 해야 하나요?

하이브리드 Azure AD 조인 머신을 새로 설치하는 경우 도메인 조인 및 다시 시작 프로세스 후에 암호를 사용하여 로그인하고 정책이 동기화될 때까지 기다려야 FIDO 보안 키를 사용하여 로그인할 수 있습니다.

- 명령 프롬프트 창에서 `dsregcmd /status`를 실행하여 현재 상태를 확인하고 **AzureAdJoined** 및 **DomainJoined** 상태가 모두 *예* 로 표시되는지 확인합니다.
- 이 동기화 지연은 도메인 조인 디바이스에 대한 알려진 제한 사항이며 FIDO에 특정되지 않습니다.

### <a name="what-if-im-unable-to-get-single-sign-on-to-my-ntlm-network-resource-after-i-sign-in-with-fido-and-get-a-credential-prompt"></a>FIDO를 사용하여 로그인하고 자격 증명 프롬프트를 받은 후 NTLM 네트워크 리소스에 Single Sign-On을 가져올 수 없는 경우 어떻게 해야 하나요?

제시간에 응답하여 리소스 요청을 처리하는 데 충분한 DC가 패치되었는지 확인합니다. DC에서 기능을 실행하고 있는지 확인하려면 `nltest /dsgetdc:contoso /keylist /kdc`를 실행한 다음, 출력을 검토합니다.

> [!NOTE]
> `nltest` 명령의 `/keylist` 스위치는 클라이언트 Windows 10 v2004 이상에서 사용할 수 있습니다.


### <a name="do-fido2-security-keys-work-in-a-windows-login-with-rodc-present-in-the-hybrid-environment"></a>FIDO2 보안 키가 하이브리드 환경에 있는 RODC를 사용하여 Windows 로그인에서 작동하나요?

FIDO2 Windows 로그인은 사용자 TGT를 교환할 쓰기 가능한 DC를 찾습니다. 사이트당 쓰기 가능한 DC가 하나 이상 있는 한 로그인이 잘 작동합니다. 

## <a name="next-steps"></a>다음 단계

[암호 없는 인증에 대해 알아보기](concept-authentication-passwordless.md)
