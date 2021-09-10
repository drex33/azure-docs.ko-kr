---
title: 페더레이션을 사용하여 Azure AD MFA로 마이그레이션 - Azure Active Directory
description: 온-프레미스의 Azure MFA 서버에서 페더레이션을 사용하여 Azure AD MFA로 전환하는 단계별 지침
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 06/22/2021
ms.author: BaSelden
author: BarbaraSelden
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3b34538fb4d0c9dc7beb0defd22f0aa78207f0a3
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122566517"
---
# <a name="migrate-to-azure-ad-mfa-with-federation"></a>페더레이션을 사용하여 Azure AD MFA로 마이그레이션

MFA(Multi-Factor Authentication) 솔루션을 Azure AD(Azure Active Directory)로 전환하는 것은 클라우드로 전환 과정의 첫 단계입니다. 향후 사용자 인증을 위해서도 Azure AD로 전환하는 것이 좋습니다. 자세한 내용은 클라우드 인증을 사용하여 Azure AD MFA로 마이그레이션하는 프로세스를 참조하세요.

페더레이션을 사용하여 Azure AD MFA로 마이그레이션하려면 Azure MFA 인증 공급자가 AD FS에 설치되어 있어야 합니다. Azure AD 신뢰 당사자 트러스트 및 기타 신뢰 당사자 트러스트는 마이그레이션된 사용자에 대해 Azure MFA를 사용하도록 구성됩니다.

다음 다이어그램에서는 이 마이그레이션의 프로세스를 보여 줍니다.

![프로세스의 단계를 보여 주는 흐름 차트. 이러한 단계는 이 문서의 제목과 동일한 순서로 정렬됨](./media/how-to-migrate-mfa-server-to-azure-mfa-with-federation/mfa-federation-flow.png)

## <a name="create-migration-groups"></a>마이그레이션 그룹 만들기

새 조건부 액세스 정책을 만들려면 그룹에 해당 정책을 할당해야 합니다. 이렇게 하려면 기존 Azure AD 보안 그룹 또는 Microsoft 365 그룹을 사용할 수 있습니다. 새 그룹을 만들거나 동기화할 수도 있습니다.

또한 Azure AD MFA로 사용자를 반복적으로 마이그레이션하려면 Azure AD 보안 그룹이 필요합니다. 이러한 그룹은 클레임 규칙에서 사용됩니다.

보안에 사용되는 그룹은 다시 사용하지 마세요. 조건부 액세스 정책을 통해 고가치 앱 그룹을 보호하는 데 보안 그룹을 사용하고 있는 경우에는 해당 그룹만 사용해야 합니다.

## <a name="prepare-ad-fs"></a>AD FS 준비

### <a name="upgrade-ad-fs-server-farm-to-2019-fbl-4"></a>AD FS 서버 팜을 2019, FBL 4로 업그레이드

AD FS 2019에서는 애플리케이션과 같은 신뢰 당사자에 대한 추가 인증 방법을 지정할 수 있습니다. 그룹 멤버 자격을 사용하여 인증 공급자를 확인합니다. 추가 인증 방법을 지정하면 전환하는 동안 다른 인증을 그대로 유지하면서 Azure AD MFA로 전환할 수 있습니다. 자세한 내용은 [WID 데이터베이스를 사용하여 Windows Server 2016에서 AD FS로 업그레이드](/windows-server/identity/ad-fs/deployment/upgrading-to-ad-fs-in-windows-server)를 참조하세요. 이 문서에서는 팜을 AD FS 2019로 업그레이드하는 방법과 FBL을 4로 업그레이드하는 방법을 모두 설명합니다.

### <a name="configure-claims-rules-to-invoke-azure-ad-mfa"></a>Azure AD MFA를 호출하도록 클레임 규칙 구성

Azure AD MFA는 추가 인증 방법이므로 이 방법을 사용할 사용자 그룹을 할당할 수 있습니다. 이렇게 하려면 신뢰 당사자 트러스트라고도 하는 클레임 규칙을 구성합니다. 그룹을 사용하여 전역적으로 또는 애플리케이션에서 호출되는 인증 공급자를 제어할 수 있습니다. 예를 들어 통합된 보안 정보에 대해 등록했거나 전화 번호가 마이그레이션된 사용자에 대해 Azure AD MFA를 호출하고, 그렇지 않은 사용자에 대해 MFA 서버를 호출할 수 있습니다.

> [!NOTE]
> 클레임 규칙에는 온-프레미스 보안 그룹이 필요합니다. 클레임 규칙을 변경하려면 먼저 백업하세요.


#### <a name="back-up-existing-rules"></a>기존 규칙 백업

새 클레임 규칙을 구성하려면 먼저 기존 규칙을 백업하세요. 정리 단계의 일부로 이러한 규칙을 복원해야 합니다. 

구성에 따라 기존 규칙을 복사하고 마이그레이션을 위해 만드는 새 규칙을 추가해야 할 수도 있습니다.  

기존 전역 규칙을 보려면 다음을 실행합니다.  

```powershell
Get-AdfsAdditionalAuthenticationRule
```

기존 신뢰 당사자 트러스트를 보려면 다음 명령을 실행하고 RPTrustName을 신뢰 당사자 트러스트 클레임 규칙의 이름으로 바꿉니다. 

```powershell
(Get-AdfsRelyingPartyTrust -Name “RPTrustName”).AdditionalAuthenticationRules 
```

#### <a name="access-control-policies"></a>액세스 제어 정책

> [!NOTE]
> 그룹 멤버 자격에 따라 특정 인증 공급자를 호출하도록 액세스 제어 정책을 구성할 수 없습니다. 

 
액세스 제어 정책에서 추가 인증 규칙으로 전환하려면 MFA 서버 인증 공급자를 사용하여 각 신뢰 당사자 트러스트에 대해 다음 명령을 실행합니다.


```powershell
Set-AdfsRelyingPartyTrust -TargetName AppA -AccessControlPolicyName $Null
```

 

이 명령은 현재 액세스 제어 정책에서 추가 인증 규칙으로 논리를 이동합니다.


#### <a name="set-up-the-group-and-find-the-sid"></a>그룹을 설정하고 SID 찾기

Azure AD MFA를 호출하려는 사용자를 배치할 특정 그룹이 있어야 합니다. 해당 그룹에 대한 SID(보안 식별자)가 필요합니다.

그룹 SID를 찾으려면 그룹 이름에서 다음 명령을 사용합니다.

`Get-ADGroup “GroupName”`

![Get-ADGroup 스크립트의 결과를 보여 주는 스크린샷 이미지](./media/how-to-migrate-mfa-server-to-azure-mfa-user-authentication/find-the-sid.png)

#### <a name="setting-the-claims-rules-to-call-azure-mfa"></a>Azure MFA를 호출하도록 클레임 규칙 설정

다음 PowerShell cmdlet은 회사 네트워크에 있지 않은 경우 그룹의 사용자에 대해 Azure AD MFA를 호출합니다. “YourGroupSid”를 위의 cmdlet을 실행하여 찾은 SID로 바꿉니다.

[2019에서 추가 인증 공급자를 선택하는 방법](/windows-server/identity/ad-fs/overview/whats-new-active-directory-federation-services-windows-server)을 검토해야 합니다. 

 > [!IMPORTANT]
> 기존 클레임 규칙 백업

 

#### <a name="set-global-claims-rule"></a>전역 클레임 규칙 설정 

다음 PowerShell cmdlet를 실행합니다. 

```powershell
(Get-AdfsRelyingPartyTrust -Name “RPTrustName”).AdditionalAuthenticationRules
```

 

이 명령은 신뢰 당사자 트러스트에 대한 현재 추가 인증 규칙을 반환합니다. 현재 클레임 규칙에 다음 규칙을 추가합니다.

```console
c:[Type == "https://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value == 
"YourGroupSID"] => issue(Type = "https://schemas.microsoft.com/claims/authnmethodsproviders", 
Value = "AzureMfaAuthentication");
not exists([Type == "https://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
Value=="YourGroupSid"]) => issue(Type = 
"https://schemas.microsoft.com/claims/authnmethodsproviders", Value = 
"AzureMfaServerAuthentication");’
```

다음 예제에서는 사용자가 네트워크 외부에서 연결하는 경우 MFA를 요구하도록 현재 클레임 규칙을 구성했다고 가정합니다. 이 예제에는 추가해야 하는 추가 규칙이 포함되어 있습니다.

```PowerShell
Set-AdfsAdditionalAuthenticationRule -AdditionalAuthenticationRules 'c:[type == 
"https://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", value == "false"] => issue(type = 
"https://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod", value = 
"https://schemas.microsoft.com/claims/multipleauthn" );
 c:[Type == "https://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value == 
“YourGroupSID"] => issue(Type = "https://schemas.microsoft.com/claims/authnmethodsproviders", 
Value = "AzureMfaAuthentication");
not exists([Type == "https://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
Value==“YourGroupSid"]) => issue(Type = 
"https://schemas.microsoft.com/claims/authnmethodsproviders", Value = 
"AzureMfaServerAuthentication");’
```


#### <a name="set-per-application-claims-rule"></a>애플리케이션별 클레임 규칙 설정

이 예제에서는 특정 신뢰 당사자 트러스트(애플리케이션)에 대한 클레임 규칙을 수정하고 추가해야 하는 정보를 포함합니다.

```PowerShell
Set-AdfsRelyingPartyTrust -TargetName AppA -AdditionalAuthenticationRules 'c:[type == 
"https://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", value == "false"] => issue(type = 
"https://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod", value = 
"https://schemas.microsoft.com/claims/multipleauthn" );
c:[Type == "https://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value == 
“YourGroupSID"] => issue(Type = "https://schemas.microsoft.com/claims/authnmethodsproviders", 
Value = "AzureMfaAuthentication");
not exists([Type == "https://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
Value==“YourGroupSid"]) => issue(Type = 
"https://schemas.microsoft.com/claims/authnmethodsproviders", Value = 
"AzureMfaServerAuthentication");’
```


### <a name="configure-azure-ad-mfa-as-an-authentication-provider-in-ad-fs"></a>AD FS에서 Azure AD MFA를 인증 공급자로 구성

AD FS에 대해 Azure AD MFA를 구성하려면 각 AD FS 서버를 구성해야 합니다. 팜에 여러 AD FS 서버가 있는 경우 Azure AD PowerShell을 사용하여 원격으로 구성할 수 있습니다.

이 프로세스에 대한 단계별 지침은 [AD FS를 사용하여 Azure AD MFA를 인증 공급자로 구성](/windows-server/identity/ad-fs/operations/configure-ad-fs-and-azure-mfa) 문서에서 [AD FS 서버 구성](/windows-server/identity/ad-fs/operations/configure-ad-fs-and-azure-mfa)을 참조하세요.

서버를 구성한 후에는 Azure AD MFA를 추가 인증 방법으로 추가할 수 있습니다. 

![Azure MFA 및 Azure Multi-Factor Authentication 서버가 선택된 인증 방법 편집 화면을 보여 주는 스크린샷](./media/how-to-migrate-mfa-server-to-azure-mfa-user-authentication/edit-authentication-methods.png)

## <a name="prepare-azure-ad-and-implement"></a>Azure AD 준비 및 구현

### <a name="ensure-supportsmfa-is-set-to-true"></a>SupportsMFA가 True로 설정되어 있는지 확인합니다.

페더레이션된 도메인의 경우 Azure AD 조건부 액세스 또는 온-프레미스 페더레이션 공급자에서 MFA를 적용할 수 있습니다. Azure AD의 각 페더레이션된 도메인에는 SupportsMFA 플래그가 있습니다. SupportsMFA 플래그가 True로 설정된 경우 Azure AD는 AD FS 또는 다른 페더레이션 공급자의 MFA로 사용자를 리디렉션합니다. 예를 들어 MFA가 필요한 조건부 액세스 정책이 구성된 애플리케이션에 사용자가 액세스하는 경우 사용자는 AD FS로 리디렉션됩니다. AD FS에서 Azure AD MFA를 인증 방법으로 추가하면 구성이 완료된 후 Azure AD MFA를 호출할 수 있습니다.

SupportsMFA 플래그가 False로 설정된 경우 Azure MFA를 사용하지 않고 AD FS 신뢰 당사자에 대한 클레임 규칙을 사용하여 MFA를 호출할 수 있습니다.

다음 [Windows PowerShell cmdlet](/powershell/module/msonline/get-msoldomainfederationsettings?view=azureadps-1.0)을 사용하여 SupportsMFA 플래그의 상태를 확인할 수 있습니다.

```powershell
Get-MsolDomainFederationSettings –DomainName yourdomain.com
```

SupportsMFA 플래그가 false로 설정되거나 페더레이션된 도메인에 대해 비어 있는 경우 다음 Windows PowerShell cmdlet을 사용하여 true로 설정합니다.

```powershell
Set-MsolDomainFederationSettings -DomainName contoso.com -SupportsMFA $true
```

이 구성을 사용하면 AD FS에서 MFA 서버를 사용할지 Azure MFA를 사용할지를 결정할 수 있습니다.

### <a name="configure-conditional-access-policies-if-needed"></a>필요한 경우 조건부 액세스 정책 구성

조건부 액세스를 사용하여 사용자에게 MFA가 요구되는 시기를 결정하는 경우에는 정책을 변경할 필요가 없습니다.

페더레이션된 도메인의 SupportsMFA가 false로 설정된 경우 Azure AD 신뢰 당사자 트러스트에서 클레임 규칙을 분석하고 동일한 보안 목표를 지원하는 조건부 액세스 정책을 만듭니다.

AD FS와 동일한 제어를 적용하는 조건부 액세스 정책을 만든 후에는 Azure AD 신뢰 당사자에 대한 클레임 규칙 사용자 지정 항목을 백업하고 제거할 수 있습니다.

자세한 내용은 다음 자료를 참조하세요.

* [조건부 액세스 배포 계획](../conditional-access/plan-conditional-access.md)

* [일반 조건부 액세스 정책](../conditional-access/concept-conditional-access-policy-common.md)


## <a name="register-users-for-azure-mfa"></a>Azure MFA에 사용자 등록

Azure MFA에 사용자를 등록하는 방법에는 다음 두 가지가 있습니다. 

* 결합된 보안 등록(MFA 및 셀프 서비스 암호 재설정) 

* MFA 서버에서 전화 번호 마이그레이션

Microsoft Authenticator 앱은 암호 없는 모드에서 사용할 수 있습니다. 또한 두 등록 방법 중 하나에서 MFA의 두 번째 단계로 사용할 수 있습니다.

### <a name="register-for-combined-security-registration-recommended"></a>결합된 보안 등록(권장)

사용자가 MFA 및 SSPR 모두에 대해 인증 방법 및 디바이스를 등록하는 단일 위치인 결합된 보안 정보에 등록하도록 합니다. MFA 서버에서 Azure AD MFA로 데이터를 마이그레이션할 수 있지만 다음과 같은 문제가 발생합니다.

* 전화 번호만 마이그레이션될 수 있습니다.

* Authenticator 앱을 다시 등록해야 합니다.

* 부실 데이터가 마이그레이션될 수 있습니다.

Microsoft는 결합된 등록 프로세스를 안내하기 위해 사용자에게 제공할 수 있는 통신 템플릿을 제공합니다. 여기에는 메일, 포스터, 테이블 텐트 및 기타 자산에 대한 템플릿이 포함됩니다. 사용자는 결합된 보안 등록 화면으로 연결되는 [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo)에서 해당 정보를 등록합니다. 

신뢰할 수 있는 디바이스 또는 위치에서 등록을 수행해야 하는 [조건부 액세스를 사용하여 보안 등록 프로세스를 보호](../conditional-access/howto-conditional-access-policy-registration.md)하는 것이 좋습니다.

> [!NOTE]
> 신뢰할 수 없는 위치 또는 디바이스에서 결합된 보안 정보를 등록해야 하는 사용자에게는 임시 액세스 패스를 발급하거나 정책에서 일시적으로 제외할 수 있습니다.

### <a name="migrate-phone-numbers-from-mfa-server"></a>MFA 서버에서 전화 번호 마이그레이션

전화 번호를 마이그레이션하면 부실 번호가 마이그레이션되고 사용자는 [Microsoft Authenticator 앱으로 암호 없는 로그인](howto-authentication-passwordless-phone.md)과 같은 좀 더 안전한 방법을 설정하는 대신 전화 기반 MFA를 유지할 가능성이 커질 수 있습니다. Microsoft Authenticator 앱 설정과 같은 디바이스 등록은 마이그레이션할 수 없습니다. 모든 사용자가 [결합된 보안 정보](howto-registration-mfa-sspr-combined.md)에 등록하는 것이 좋습니다. 결합된 보안 정보를 통해 사용자는 셀프 서비스 암호 재설정에도 등록할 수 있습니다.

사용자가 결합된 보안 정보를 등록할 수 없는 경우 MFA 서버에서 사용자와 전화 번호를 내보낸 후 전화 번호를 Azure AD로 가져올 수 있습니다.
 

#### <a name="export-user-phone-numbers-from-mfa-server"></a>MFA 서버에서 사용자 전화 번호 내보내기 

1. MFA 서버에서 Multi-Factor Authentication 서버 관리 콘솔을 엽니다. 

1. **파일** 을 선택한 다음, **사용자 내보내기** 를 선택합니다.

1. CSV 파일을 **저장** 합니다. 기본 이름은 Multi-Factor Authentication Users.csv입니다.

#### <a name="interpret-and-format-the-csv--file"></a>.csv 파일 해석 및 서식 지정

.csv 파일에는 마이그레이션에 필요하지 않은 많은 필드가 포함되어 있습니다. 전화 번호를 Azure AD로 가져오기 전에 편집하고 서식을 지정합니다. 

.csv 파일을 열 때 다음 열을 확인합니다.

* 사용자 이름
* 기본 전화
* 기본 국가 코드
* 국가 코드 백업
* 백업 폰
* 내선 번호 백업

데이터를 해석하고, 정리하고, 서식을 지정해야 합니다.

#### <a name="tips-to-avoid-errors-during-import"></a>가져오는 동안 오류를 방지하기 위한 팁

* 인증 방법 API를 사용하여 전화 번호를 Azure AD로 가져오기 전에 CSV 파일을 수정합니다. 

* .csv를 UPN, PhoneType, PhoneNumber의 세 열로 단순화합니다. 

* 내보낸 MFA 서버 사용자 이름이 Azure AD UserPrincipalName과 일치하는지 확인합니다. 일치하지 않으면 CSV 파일의 사용자 이름을 Azure AD의 사용자 이름과 일치하도록 업데이트합니다. 업데이트하면 사용자를 찾을 수 있습니다.

사용자가 이미 Azure AD에 전화 번호를 등록했을 수 있습니다. 인증 방법 API를 사용하여 전화 번호를 가져올 때 기존 전화 번호를 덮어쓸지 또는 가져온 번호를 대체 전화 번호로 추가할지를 결정해야 합니다.

다음 PowerShell cmdlet은 사용자가 제공하는 CSV 파일을 사용하고 내보낸 전화 번호를 인증 방법 API에서 각 UPN의 전화 번호로 추가합니다. “myPhones”를 CSV 파일의 이름으로 바꿉니다.

```powershell

$csv = import-csv myPhones.csv

$csv|% { New-MgUserAuthenticationPhoneMethod -UserId $_.UPN -phoneType $_.PhoneType -phoneNumber $_.PhoneNumber} 

```

### <a name="add-users-to-the-appropriate-groups"></a>적절한 그룹에 사용자 추가

* 새 조건부 액세스 정책을 만든 경우 해당 그룹에 적절한 사용자를 추가합니다. 

* 클레임 규칙에 대한 온-프레미스 보안 그룹을 만든 경우 해당 그룹에 적절한 사용자를 추가합니다.

보안에 사용되는 그룹은 다시 사용하지 않는 것이 좋습니다. 따라서 조건부 액세스 정책을 통해 고가치 앱 그룹을 보호하는 데 보안 그룹을 사용하고 있는 경우에는 해당 그룹만 사용해야 합니다.

## <a name="monitoring"></a>모니터링

Azure MFA 등록은 [Authentication methods usage & insights report](https://portal.azure.com/)(인증 방법 사용량 및 인사이트 보고서)를 사용하여 모니터링할 수 있습니다. 이 보고서는 Azure AD에서 찾을 수 있습니다. **모니터링** 을 선택한 다음, **사용량 및 인사이트** 를 선택합니다. 

사용량 및 인사이트에서 **인증 방법** 을 선택합니다. 

자세한 Azure MFA 등록 정보는 등록 탭에서 찾을 수 있습니다. **Azure Multi-Factor Authentication을 사용할 수 있는 사용자** 하이퍼링크를 선택하고 드릴다운하여 등록된 사용자 목록을 볼 수 있습니다.

![MFA에 대한 사용자 등록을 보여 주는 인증 방법 작업 화면 이미지](./media/how-to-migrate-mfa-server-to-azure-mfa-with-federation/authentication-methods.png)

   

## <a name="clean-up-steps&quot;></a>정리 단계

Azure MFA로 마이그레이션을 완료하고 MFA 서버를 서비스 해제할 준비가 되면 다음 세 가지 작업을 수행합니다. 

1. AD FS에 대한 클레임 규칙을 마이그레이션 전 구성으로 되돌리고 MFA 서버 인증 공급자를 제거합니다.

1. AD FS에서 인증 공급자로 구성된 MFA 서버를 제거합니다. 그러면 모든 사용자가 유일한 추가 인증 방법으로 설정된 Azure MFA를 사용하게 됩니다. 

1. MFA 서버를 서비스 해제합니다.

### <a name=&quot;revert-claims-rules-on-ad-fs-and-remove-mfa-server-authentication-provider&quot;></a>AD FS에 대한 클레임 규칙을 되돌리고 MFA 서버 인증 공급자 제거

클레임 규칙 구성의 단계에 따라 Azure AD MFA를 호출하여 백업된 클레임 규칙으로 되돌리고 AzureMFAServerAuthentication 클레임 규칙을 제거합니다. 

예를 들어 규칙에서 다음을 제거합니다. 

 
```console
c:[Type == &quot;https://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid&quot;, Value ==
“**YourGroupSID**&quot;] => issue(Type = &quot;https://schemas.microsoft.com/claims/authnmethodsproviders&quot;,
Value = &quot;AzureMfaAuthentication");
not exists([Type == "https://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid",
Value=="YourGroupSid"]) => issue(Type =
"https://schemas.microsoft.com/claims/authnmethodsproviders", Value =
"AzureMfaServerAuthentication");’
```

### <a name="disable-mfa-server-as-an-authentication-provider-in-ad-fs"></a>AD FS에서 MFA 서버를 인증 공급자로 사용하지 않도록 설정

이렇게 변경하면 Azure MFA만 인증 공급자로 사용됩니다.

1. **AD FS 관리 콘솔** 을 엽니다.

1. **서비스** 에서 **인증 방법** 을 마우스 오른쪽 단추로 클릭하고 **Edit Multi-factor Authentication Methods**(다단계 인증 방법 편집)를 선택합니다. 

1. **Azure Multi-Factor Authentication 서버** 옆의 확인란을 선택 취소합니다. 

### <a name="decommission-the-mfa-server"></a>MFA 서버 서비스 해제

엔터프라이즈 서버 서비스 해제 프로세스에 따라 사용자 환경에서 MFA 서버를 제거합니다.

MFA 서버를 서비스 해제할 때 고려할 수 있는 사항은 다음과 같습니다. 

* 서버를 제거하기 전에 MFA 서버의 로그를 검토하여 서버를 사용하는 사용자 또는 애플리케이션이 없는지 확인합니다.

* 서버의 제어판에서 Multi-Factor Authentication 서버를 제거합니다.

* 필요에 따라 먼저 백업한 후 남아 있는 로그 및 데이터 디렉터리를 정리합니다. 

* etpub\wwwroot\MultiFactorAuthWebServiceSdk 또는 MultiFactorAuth 디렉터리에 남아 있는 파일을 포함하여 Multi-Factor Authentication 웹 서버 SDK를 제거합니다.

* MFA 서버 8.0 이전 버전의 경우 Multi-Factor Auth Phone 앱 웹 서비스를 제거해야 할 수도 있습니다.

## <a name="next-steps"></a>다음 단계

- [암호 해시 동기화 배포](../hybrid/whatis-phs.md)
- [조건부 액세스에 대해 자세히 알아보기](../conditional-access/overview.md)
- [Azure AD로 애플리케이션 마이그레이션](../manage-apps/migrate-application-authentication-to-azure-active-directory.md)

 

 

 
