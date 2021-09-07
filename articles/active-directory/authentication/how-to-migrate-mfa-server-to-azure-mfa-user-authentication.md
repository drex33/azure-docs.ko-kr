---
title: Azure AD MFA 및 Azure AD 사용자 인증으로 마이그레이션 - Azure Active Directory
description: Azure MFA 서버 온-프레미스에서 Azure AD MFA 및 Azure AD 사용자 인증으로 전환하기 위한 단계별 지침
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
ms.openlocfilehash: 1f10052f6bcb458b98535d54a9b2fa5deb749c38
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122528481"
---
# <a name="migrate-to-azure-ad-mfa-and-azure-ad-user-authentication"></a>Azure AD MFA 및 Azure AD 사용자 인증으로 마이그레이션

MFA(Multi-Factor Authentication)는 잘못된 행위자의 인프라와 자산을 보호하는 데 도움이 됩니다. Microsoft의 MFA 서버(Multi-Factor Authentication 서버)는 새 배포에 더 이상 제공되지 않습니다. MFA 서버를 사용하는 고객은 Azure AD MFA(Azure AD Multi-Factor Authentication)로 이동해야 합니다. 

MFA(Multi-Factor Authentication)를 MFA 서버에서 Azure AD(Azure Active Directory)로 마이그레이션하기 위한 몇 가지 옵션이 있습니다. 여기에는 다음이 포함됩니다.

* 양호: [MFA 서버에서 Azure AD](how-to-migrate-mfa-server-to-azure-mfa.md)로만 이동 
* 좋음: MFA 서비스 및 사용자 인증을 Azure AD로 이동(이 문서에 설명되어 있음)
* 최상: 모든 애플리케이션, MFA 서비스 및 사용자 인증을 Azure AD로 이동 이 문서에서 설명하는 애플리케이션을 이동하려는 경우 이 문서의 Azure AD로 애플리케이션 이동 섹션을 참조하세요. 

조직에 적절한 MFA 마이그레이션 옵션을 선택하려면 [MFA 서버에서 Azure Active Directory MFA로 마이그레이션](how-to-migrate-mfa-server-to-azure-mfa.md)의 고려 사항을 참조하세요. 

다음 다이어그램에서는 애플리케이션의 일부를 AD FS에 유지하면서 Azure AD MFA 및 클라우드 인증으로 마이그레이션하는 프로세스를 보여 줍니다. 이 프로세스를 통해 그룹 구성원 자격에 따라 MFA 서버에서 Azure MFA로 사용자를 반복적으로 마이그레이션할 수 있습니다.

각 단계는 이 문서의 후속 섹션에 설명되어 있습니다.

>[!NOTE]
>이 마이그레이션의 일부로 애플리케이션을 Azure Active Directory로 이동하려는 경우 MFA 마이그레이션 전에 이 작업을 수행해야 합니다. 모든 앱을 이동하는 경우 MFA 마이그레이션 프로세스 섹션을 건너뛸 수 있습니다. 이 문서 끝부분에서 애플리케이션 이동에 대한 섹션을 참조하세요.

## <a name="process-to-migrate-to-azure-ad-and-user-authentication"></a>Azure AD 및 사용자 인증으로 마이그레이션하는 프로세스

![Azure AD 및 사용자 인증으로 마이그레이션하는 프로세스입니다.](media/how-to-migrate-mfa-server-to-azure-mfa-user-authentication/mfa-cloud-authentication-flow.png)

## <a name="prepare-groups-and-conditional-access"></a>그룹 및 조건부 액세스 준비

그룹은 MFA 마이그레이션에 사용되는 세 가지 용량으로 사용됩니다.
* **단계적 롤아웃으로 사용자를 Azure AD MFA로 반복적으로 이동.**
클라우드 전용 그룹이라고도 하는 Azure AD에서 만든 그룹을 사용합니다. 사용자를 MFA로 이동하고 조건부 액세스 정책을 사용하려는 경우 Azure AD 보안 그룹 또는 Microsoft 365 그룹을 사용할 수 있습니다.  자세한 내용은 Azure AD 보안 그룹 만들기 및 관리자를 위한 Microsoft 365 그룹 개요를 참조하세요.
  >[!IMPORTANT]
  >중첩 그룹 및 동적 그룹은 단계적 롤아웃 프로세스에서 지원되지 않습니다. 단계적 롤아웃 작업에는 이러한 유형의 그룹을 사용하지 마세요.
* **조건부 액세스 정책**. 조건부 액세스를 위해 Azure AD 또는 온-프레미스 그룹을 사용할 수 있습니다.
* **클레임 규칙을 사용하여 AD FS 애플리케이션에 대해 Azure AD MFA 호출.**
AD FS에 애플리케이션이 있는 경우에만 적용됩니다.
온-프레미스 Active Directory 보안 그룹이어야 합니다. Azure AD MFA가 추가 인증 방법인 경우 사용자 그룹이 각 신뢰 당사자 트러스트에서 해당 방법을 사용하도록 지정할 수 있습니다. 예를 들어, 이미 마이그레이션한 사용자에 대해 Azure AD MFA를 호출하고 아직 마이그레이션되지 않은 사용자에 대해 MFA 서버를 호출할 수 있습니다. 이 방식은 테스트 및 마이그레이션 중에 모두 유용합니다. 

>[!NOTE] 
>보안에 사용되는 그룹은 다시 사용하지 않는 것이 좋습니다. 보안 그룹을 사용하여 조건부 액세스 정책을 통해 상위 값 앱 그룹을 보호하는 경우에는 해당 그룹만 사용해야 합니다.

### <a name="configure-conditional-access-policies"></a>조건부 액세스 정책 구성

이미 조건부 액세스를 사용하여 사용자에게 MFA에 대한 메시지가 표시되는 시기를 결정한 경우에는 정책을 변경할 필요가 없습니다. 사용자가 클라우드 인증으로 마이그레이션되면 기존 조건부 액세스 정책에 정의된 대로 Azure AD MFA를 사용하기 시작합니다. 이러한 사용자는 AD FS 및 MFA 서버로 더 이상 리디렉션되지 않습니다.

페더레이션된 도메인의 SupportsMFA가 false로 설정된 경우 클레임 규칙을 사용하여 AD FS에 MFA를 적용할 가능성이 높습니다. 이 경우 Azure AD 신뢰 당사자 트러스트에 대한 클레임 규칙을 분석하고 동일한 보안 목표를 지원하는 조건부 액세스 정책을 만들어야 합니다.

조건부 액세스 정책을 구성해야 하는 경우 단계적 롤아웃을 사용하도록 설정하기 전에 이러한 구성을 수행해야 합니다. 자세한 내용은 다음 자료를 참조하세요.
* [조건부 액세스 배포 계획](../conditional-access/plan-conditional-access.md)
* [일반 조건부 액세스 정책](../conditional-access/concept-conditional-access-policy-common.md)

## <a name="prepare-ad-fs"></a>AD FS 준비 

AD FS에 MFA가 필요한 애플리케이션이 없는 경우 이 섹션을 건너뛰고 단계적 롤아웃 준비 섹션으로 이동해도 됩니다.

### <a name="upgrade-ad-fs-server-farm-to-2019-fbl-4"></a>AD FS 서버 팜을 2019, FBL 4로 업그레이드

AD FS 2019에서 Microsoft는 애플리케이션과 같은 신뢰 당사자에 대한 추가 인증 방법을 지정하는 기능을 제공하는 새로운 기능을 출시했습니다. 이 작업은 그룹 구성원 자격을 사용하여 인증 공급자를 결정하는 방식으로 수행합니다. 추가 인증 방법을 지정하면 전환하는 동안 다른 인증을 그대로 유지하면서 Azure AD MFA로 전환할 수 있습니다. 

자세한 내용은 [WID 데이터베이스를 사용하여 Windows Server 2016에서 AD FS로 업그레이드](/windows-server/identity/ad-fs/deployment/upgrading-to-ad-fs-in-windows-server)를 참조하세요. 이 문서에서는 팜을 AD FS 2019로 업그레이드하고 FBL를 4로 업그레이드하는 방법을 설명합니다.

### <a name="configure-claims-rules-to-invoke-azure-ad-mfa"></a>Azure AD MFA를 호출하도록 클레임 규칙 구성

Azure AD MFA를 추가 인증 방법으로 지정했으므로 Azure AD MFA를 사용할 사용자 그룹을 할당할 수 있습니다. 이 작업은 *신뢰 당사자 트러스트* 라고도 하는 클레임 규칙을 구성하여 수행합니다. 그룹을 사용하여 전역적으로 또는 애플리케이션에 의해 호출되는 인증 공급자를 제어할 수 있습니다. 예를 들어, 통합된 보안 정보에 대해 등록했거나 전화 번호가 마이그레이션된 사용자에 대해 Azure AD MFA를 호출하고, 그렇지 않은 사용자에 대해 MFA 서버를 호출할 수 있습니다. 

>[!NOTE]
>클레임 규칙에는 온-프레미스 보안 그룹이 필요합니다. 

#### <a name="back-up-existing-rules"></a>기존 규칙 백업 

새 클레임 규칙을 구성하기 전에 기존 규칙을 백업합니다. 이러한 규칙을 정리 단계의 일부로 복원해야 합니다. 

구성에 따라 기존 규칙을 복사하고 마이그레이션에 대해 생성되는 새 규칙을 추가해야 할 수도 있습니다.

기존 전역 규칙을 보려면 다음을 실행합니다.  
```powershell
Get-AdfsAdditionalAuthenticationRule
```

기존 신뢰 당사자 트러스트를 보려면 다음 명령을 실행하고 RPTrustName을 신뢰 당사자 트러스트 클레임 규칙의 이름으로 바꿉니다. 

```powershell
(Get-AdfsRelyingPartyTrust -Name “RPTrustName”).AdditionalAuthenticationRules
```

#### <a name="access-control-policies"></a>액세스 제어 정책

>[!NOTE]
>그룹 구성원 자격에 따라 특정 인증 공급자를 호출하도록 액세스 제어 정책을 구성할 수 없습니다. 

액세스 제어 정책에서 추가 인증 규칙으로 전환하려면 MFA 서버 인증 공급자를 사용하여 각 신뢰 당사자 트러스트에 대해 다음 명령을 실행합니다.

```powershell
Set-AdfsRelyingPartyTrust -**TargetName AppA -AccessControlPolicyName $Null**
```

이 명령은 현재 액세스 제어 정책에서 추가 인증 규칙으로 논리를 이동합니다.

#### <a name="set-up-the-group-and-find-the-sid"></a>그룹을 설정하고 SID 찾기

Azure AD MFA를 호출하려는 사용자를 배치할 특정 그룹이 있어야 합니다. 해당 그룹에 대한 SID(보안 식별자)를 찾아야 합니다.
그룹 SID를 찾으려면 그룹 이름 `Get-ADGroup “GroupName”`을 사용하여 다음 명령을 사용합니다.

![그룹 SID를 가져오는 PowerShell 명령입니다.](media/how-to-migrate-mfa-server-to-azure-mfa-user-authentication/find-the-sid.png)

#### <a name="setting-the-claims-rules-to-call-azure-mfa"></a>Azure MFA를 호출하도록 클레임 규칙 설정

다음 PowerShell cmdlet은 회사 네트워크에 있지 않을 때 그룹에 있는 사용자에 대해 Azure AD MFA를 호출합니다. 앞의 cmdlet을 실행하여 "YourGroupSid"를 찾은 SID로 바꾸어야 합니다.

[2019에서 추가 인증 공급자를 선택하는 방법](/windows-server/identity/ad-fs/overview/whats-new-active-directory-federation-services-windows-server#how-to-choose-additional-auth-providers-in-2019)을 검토해야 합니다. 

>[!IMPORTANT] 
>계속하기 전에 기존 클레임 규칙을 백업합니다.

##### <a name="set-global-claims-rule"></a>전역 클레임 규칙 설정 

다음 명령을 실행하고 RPTrustName을 신뢰 당사자 트러스트 클레임 규칙의 이름으로 바꿉니다. 

```powershell
(Get-AdfsRelyingPartyTrust -Name “RPTrustName”).AdditionalAuthenticationRules
```

이 명령은 신뢰 당사자 트러스트에 대한 현재 추가 인증 규칙을 반환합니다.  
현재 클레임 규칙에 다음 규칙을 추가해야 합니다.

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

##### <a name="set-per-application-claims-rule"></a>애플리케이션별 클레임 규칙 설정

이 예제에서는 특정 신뢰 당사자 트러스트(애플리케이션)에 대한 클레임 규칙을 수정합니다. 추가해야 하는 추가 규칙이 포함됩니다.

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

이 프로세스에 대한 단계별 지침은 [AD FS 서버 구성](/windows-server/identity/ad-fs/operations/configure-ad-fs-and-azure-mfa#configure-the-ad-fs-servers)을 참조하세요.

서버를 구성한 후에는 Azure AD MFA를 추가 인증 방법으로 추가할 수 있습니다. 

![추가 인증 방법으로 Azure AD MFA를 추가하는 방법의 스크린샷](media/how-to-migrate-mfa-server-to-azure-mfa-user-authentication/edit-authentication-methods.png)


## <a name="prepare-staged-rollout"></a>단계별 롤아웃 준비 

이제 단계적 롤아웃 기능을 사용할 준비가 되었습니다. 단계적 롤아웃은 사용자를 PHS 또는 PTA로 반복적으로 이동하는 데 유용합니다. 

* [지원되는 시나리오](../hybrid/how-to-connect-staged-rollout.md#supported-scenarios)를 검토해야 합니다. 
* 먼저 [PHS에 대한 사전 작업](../hybrid/how-to-connect-staged-rollout.md#pre-work-for-password-hash-sync) 또는 [PTA에 대한 사전 작업](../hybrid/how-to-connect-staged-rollout.md#pre-work-for-pass-through-authentication)을 수행해야 합니다. PHS가 권장됩니다. 
* 다음으로, [Seamless SSO에 대한 사전 작업](../hybrid/how-to-connect-staged-rollout.md#pre-work-for-seamless-sso)을 수행합니다. 
* 선택한 인증 방법에 대해 [클라우드 인증의 단계적 롤아웃을 사용하도록 설정](../hybrid/how-to-connect-staged-rollout.md#enable-a-staged-rollout-of-a-specific-feature-on-your-tenant)합니다. 
* 단계적 롤아웃을 위해 만든 그룹을 추가합니다. 그룹에 사용자를 반복적으로 추가하며, 이러한 그룹은 동적 그룹 또는 중첩된 그룹이 될 수 없습니다. 

## <a name="register-users-for-azure-mfa"></a>Azure MFA에 사용자 등록

Azure MFA에 사용자를 등록하는 방법에는 다음 두 가지가 있습니다. 

* 결합된 보안 등록(MFA 및 셀프 서비스 암호 재설정) 
* MFA 서버에서 전화 번호 마이그레이션

Microsoft Authenticator 앱은 암호 없는 로그인 방법뿐만 아니라 위 두 가지 방법 중 하나를 사용한 MFA에서 두 번째 요소로 사용할 수 있습니다.

### <a name="register-for-combined-security-registration-recommended"></a>결합된 보안 등록(권장)

사용자가 MFA 및 SSPR 모두에 대한 인증 방법 및 디바이스를 등록하는 단일 위치인 결합된 보안 정보에 등록하도록 하는 것이 좋습니다. MFA 서버에서 Azure AD MFA로 데이터를 마이그레이션할 수 있지만 다음과 같은 문제가 발생합니다.

* 전화 번호만 마이그레이션할 수 있습니다.
* Authenticator 앱을 다시 등록해야 합니다.
* 부실 데이터를 마이그레이션할 수 있습니다.

Microsoft는 결합된 등록 프로세스를 안내하기 위해 사용자에게 제공할 수 있는 통신 템플릿을 제공합니다. 여기에는 메일, 포스터, 테이블 콘텐츠 및 기타 다양한 자산에 대한 템플릿이 포함됩니다. 사용자는 `https://aka.ms/mysecurityinfo`에 해당 정보를 등록합니다. 그러면 결합된 보안 등록 화면으로 이동됩니다. 

신뢰할 수 있는 디바이스 또는 위치에서 등록을 수행해야 하는 [조건부 액세스를 사용하여 보안 등록 프로세스를 보호](../conditional-access/howto-conditional-access-policy-registration.md)하는 것이 좋습니다. 등록 상태를 추적하는 방법에 대한 내용은 [Azure Active Directory의 인증 방법 작업](howto-authentication-methods-activity.md)을 참조하세요.
> [!NOTE]
> 신뢰할 수 없는 위치 또는 디바이스에서 결합된 보안 정보를 등록해야 하는 사용자에게는 임시 액세스 패스를 발급하거나 정책에서 일시적으로 제외할 수 있습니다.

### <a name="migrate-phone-numbers-from-mfa-server"></a>MFA 서버에서 전화 번호 마이그레이션

사용자의 등록된 MFA 전화 번호 및 하드웨어 토큰을 마이그레이션할 수 있지만 Microsoft Authenticator 앱 설정과 같은 디바이스 등록은 마이그레이션할 수 없습니다. 전화 번호를 마이그레이션하면 부실 번호가 마이그레이션되고 사용자는 [Microsoft Authenticator 앱으로 암호 없는 로그인](howto-authentication-passwordless-phone.md)과 같은 좀 더 안전한 방법을 설정하는 대신, 전화 기반 MFA를 유지할 가능성이 높아질 수 있습니다. 따라서 선택한 마이그레이션 경로에 관계없이 모든 사용자가 [결합된 보안 정보](howto-registration-mfa-sspr-combined.md)를 등록하도록 하는 것이 좋습니다. 결합된 보안 정보를 통해 사용자는 셀프 서비스 암호 재설정에도 등록할 수 있습니다.

사용자가 결합된 보안 정보를 등록하도록 할 수 없는 경우 MFA 서버에서 전화 번호와 함께 사용자를 내보낸 후 전화 번호를 Azure AD로 가져올 수 있습니다. 

#### <a name="export-user-phone-numbers-from-mfa-server"></a>MFA 서버에서 사용자 전화 번호 내보내기 

1. MFA 서버에서 Multi-Factor Authentication 서버 관리 콘솔을 엽니다. 
1. **파일** > **사용자 내보내기** 를 선택합니다.
3)  CSV 파일을 저장합니다. 기본 이름은 Multi-Factor Authentication Users.csv입니다.

#### <a name="interpret-and-format-the-csv-file"></a>.csv 파일 해석 및 서식 지정

.csv 파일에는 마이그레이션에 필요하지 않은 여러 필드가 포함되어 있으며, 전화 번호를 Azure AD로 가져오기 전에 편집하고 서식을 지정해야 합니다. 

.csv 파일을 열 때 관심 있는 열에는 사용자 이름, 기본 전화, 기본 국가 코드, 백업 국가 코드, 백업 전화, 백업 내선 번호가 포함됩니다. 필요에 따라 이 데이터를 해석하고 서식을 지정해야 합니다.

#### <a name="tips-to-avoid-errors-during-import"></a>가져오는 동안 오류를 방지하기 위한 팁

* 인증 방법 API를 사용하여 전화 번호를 Azure AD로 가져오기 전에 CSV 파일을 수정해야 합니다. 
* .csv를 UPN, PhoneType 및 PhoneNumber의 세 열로 단순화하는 것이 좋습니다. 

  ![csv 예제 스크린샷](media/how-to-migrate-mfa-server-to-azure-mfa-user-authentication/csv-example.png)

* 내보낸 MFA 서버 사용자 이름이 Azure AD UserPrincipalName과 일치하는지 확인합니다. 일치하지 않으면 Azure AD에 있는 것과 일치하도록 CSV 파일의 사용자 이름을 업데이트합니다. 그러지 않으면 사용자를 찾을 수 없습니다.

사용자가 이미 Azure AD에 전화 번호를 등록했을 수 있습니다. 인증 방법 API를 사용하여 전화 번호를 가져올 때 기존 전화 번호를 덮어쓸지 또는 가져온 번호를 대체 전화 번호로 추가할지를 결정해야 합니다.

다음 PowerShell cmdlet은 사용자가 제공하는 CSV 파일을 사용하고, 내보낸 전화 번호를 인증 방법 API에서 각 UPN의 전화 번호로 추가합니다. "myPhones"를 CSV 파일의 이름으로 바꾸어야 합니다.


```powershell
$csv = import-csv myPhones.csv
$csv|% { New-MgUserAuthenticationPhoneMethod -UserId $_.UPN -phoneType $_.PhoneType -phoneNumber $_.PhoneNumber} 
```

사용자의 인증 방법을 관리하는 방법에 대한 자세한 내용은 [Azure AD multi-factor authentication에 대 한 인증 방법 관리](howto-mfa-userdevicesettings.md)를 참조하세요.

### <a name="add-users-to-the-appropriate-groups"></a>적절한 그룹에 사용자 추가 

* 새 조건부 액세스 정책을 만든 경우 해당 그룹에 적절한 사용자를 추가합니다. 
* 클레임 규칙에 대한 온-프레미스 보안 그룹을 만든 경우 해당 그룹에 적절한 사용자를 추가합니다. 
* 적절한 조건부 액세스 규칙에 사용자를 추가한 후에만 단계적 롤아웃을 위해 만든 그룹에 사용자를 추가합니다. 다단계 인증을 수행해야 하는 경우 위 작업이 완료되면 선택한 Azure 인증 방법(PHS 또는 PTA) 및 Azure AD MFA가 사용됩니다.

> [!IMPORTANT] 
> 중첩 그룹 및 동적 그룹은 단계적 롤아웃 프로세스에서 지원되지 않습니다. 이러한 유형의 그룹은 사용하지 마세요. 

보안에 사용되는 그룹은 다시 사용하지 않는 것이 좋습니다. 따라서 보안 그룹을 사용하여 조건부 액세스 정책을 통해 상위 값 앱 그룹을 보호하는 경우에는 해당 그룹만 사용해야 합니다.

## <a name="monitoring"></a>모니터링

배포를 모니터링하는 데 다양한 [Azure Monitor 통합 문서](../reports-monitoring/howto-use-azure-monitor-workbooks.md)및 사용량/인사이트 보고서를 사용할 수 있습니다. 이러한 내용은 **모니터링** 아래의 탐색 창에 있는 Azure AD에서 찾을 수 있습니다. 

### <a name="monitoring-staged-rollout"></a>단계적 롤아웃 모니터링

[통합 문서](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Workbooks) 섹션에서 **퍼블릭 템플릿** 을 선택합니다. **하이브리드 인증** 섹션에서 **단계적 롤아웃의 그룹, 사용자 및 로그인** 통합 문서를 선택합니다.

이러한 통합 문서를 사용하여 다음을 모니터링할 수 있습니다. 
* 단계적 롤아웃에 추가된 사용자 및 그룹
* 스테이징된 롤아웃에서 제거된 사용자 및 그룹
* 단계적 롤아웃에서 사용자의 로그인 실패 및 실패 이유

### <a name="monitoring-azure-mfa-registration"></a>Azure MFA 등록 모니터링
[인증 방법 사용량 및 인사이트 보고서](https://portal.azure.com/#blade/Microsoft_AAD_IAM/AuthenticationMethodsMenuBlade/AuthMethodsActivity/menuId/AuthMethodsActivity)를 사용하여 Azure MFA 등록을 모니터링할 수 있습니다. 이 보고서는 Azure AD에서 찾을 수 있습니다. **모니터링** 을 선택한 다음, **사용량 및 인사이트** 를 선택합니다. 

![사용량 및 인사이트 보고서 찾는 방법의 스크린샷](media/how-to-migrate-mfa-server-to-azure-mfa-user-authentication/usage-report.png)

사용량 및 인사이트에서 **인증 방법** 을 선택합니다. 

자세한 Azure MFA 등록 정보는 등록 탭에서 찾을 수 있습니다. **Azure Multi-Factor Authentication에 등록된 사용자** 하이퍼링크를 선택하고 드릴다운하여 등록된 사용자 목록을 볼 수 있습니다.

![등록 탭 스크린샷](media/how-to-migrate-mfa-server-to-azure-mfa-user-authentication/registration-tab.png)

### <a name="monitoring-app-sign-in-health"></a>앱 로그인 상태 모니터링

앱 로그인 상태 통합 문서 또는 애플리케이션 작업 사용량 보고서를 사용하여 Azure AD로 이동한 애플리케이션을 모니터링합니다.

* **앱 로그인 상태 통합 문서**. 이 통합 문서 사용에 대한 자세한 지침은 [복원력을 위한 애플리케이션 로그인 상태 모니터링](../fundamentals/monitor-sign-in-health-for-resilience.md)을 참조하세요.
* **Azure AD 애플리케이션 작업 사용량 보고서**. 이 [보고서](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsageAndInsightsMenuBlade/Azure%20AD%20application%20activity)는 개별 애플리케이션에 대한 성공 및 실패 로그인뿐만 아니라 특정 애플리케이션에 대한 로그인 작업을 드릴다운하고 보는 기능을 확인하는 데 사용할 수 있습니다. 

## <a name="clean-up-tasks"></a>정리 태스크

모든 사용자를 Azure AD 클라우드 인증 및 Azure MFA로 이동하면 MFA 서버를 서비스 해제할 준비가 된 것입니다. 서버를 제거하기 전에 MFA 서버 로그를 검토하여 사용자 또는 애플리케이션이 이 로그를 사용하지 않는지 확인하는 것이 좋습니다.

### <a name="convert-your-domains-to-managed-authentication"></a>도메인을 관리형 인증으로 변환

이제 [Azure AD의 페더레이션된 도메인을 관리형으로 변환](../hybrid/migrate-from-federation-to-cloud-authentication.md#convert-domains-from-federated-to-managed)하고 단계적 롤아웃 구성을 제거해야 합니다. 이렇게 하면 새 사용자가 마이그레이션 그룹에 추가되지 않고도 클라우드 인증을 사용할 수 있게 됩니다.

### <a name="revert-claims-rules-on-ad-fs-and-remove-mfa-server-authentication-provider"></a>AD FS의 클레임 규칙 되돌리기 및 MFA 서버 인증 공급자 제거

[Azure AD MFA를 호출하도록 클레임 규칙 구성](#configure-claims-rules-to-invoke-azure-ad-mfa)의 단계에 따라 백업한 클레임 규칙으로 되돌리고 AzureMFAServerAuthentication 클레임 규칙을 제거합니다. 

예를 들어, 규칙에서 다음을 제거합니다. 

```console
c:[Type == "https://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value ==
“**YourGroupSID**"] => issue(Type = "https://schemas.microsoft.com/claims/authnmethodsproviders",
Value = "AzureMfaAuthentication");
not exists([Type == "https://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid",
Value=="YourGroupSid"]) => issue(Type =
"https://schemas.microsoft.com/claims/authnmethodsproviders", Value =
"AzureMfaServerAuthentication");’
```


### <a name="disable-mfa-server-as-an-authentication-provider-in-ad-fs"></a>AD FS에서 MFA 서버를 인증 공급자로 사용하지 않도록 설정

이렇게 변경하면 Azure MFA만 인증 공급자로 사용됩니다.

1. **AD FS 관리 콘솔** 을 엽니다.
1. **서비스** 에서 **인증 방법** 을 마우스 오른쪽 단추로 클릭하고 **Multi-Factor Authentication 편집** 을 선택합니다. 
1. **Azure Multi-Factor Authentication 서버** 확인란을 선택 취소합니다. 

### <a name="decommission-the-mfa-server"></a>MFA 서버 서비스 해제

엔터프라이즈 서버 서비스 해제 프로세스에 따라 사용자 환경에서 MFA 서버를 제거합니다.

MFA 서버를 서비스 해제할 때 고려할 수 있는 사항은 다음과 같습니다. 

* 서버를 제거하기 전에 MFA 서버 로그를 검토하여 사용자 또는 애플리케이션이 이 로그를 사용하지 않는지 확인하는 것이 좋습니다.
* 서버의 제어판 Multi-Factor Authentication 서버를 제거합니다.
* 필요에 따라 먼저 백업한 후 남아 있는 로그 및 데이터 디렉터리를 정리합니다. 
* inetpub\wwwroot\MultiFactorAuthWebServiceSdk 및/또는 MultiFactorAuth 디렉터리에 남아 있는 파일을 포함하여 Multi-Factor Authentication 웹 서버 SDK를 제거합니다.
* 8\.0.x 이전 버전의 MFA 서버에서는 Multi-Factor Auth 전화 앱 웹 서비스를 제거해야 할 수도 있습니다.

## <a name="move-application-authentication-to-azure-active-directory"></a>애플리케이션 인증을 Azure Active Directory로 이동

MFA 및 사용자 인증과 함께 모든 애플리케이션 인증을 마이그레이션하는 경우 온-프레미스 인프라의 상당 부분을 제거하여 비용과 위험을 줄일 수 있습니다. 모든 애플리케이션 인증을 이동하는 경우 [AD FS 준비](#prepare-ad-fs) 스테이지를 건너뛰고 MFA 마이그레이션을 간소화할 수 있습니다.

모든 애플리케이션 인증을 이동하는 프로세스는 다음 다이어그램에 표시됩니다.

![애플리케이션을 Azure AD MFA로 마이그레이션하는 프로세스입니다.](media/how-to-migrate-mfa-server-to-azure-mfa-user-authentication/mfa-app-migration-flow.png)

마이그레이션 전에 모든 애플리케이션을 이동할 수 없는 경우 시작하기 전에 가능한 애플리케이션을 먼저 이동합니다.
애플리케이션을 Azure로 마이그레이션하는 방법에 대한 자세한 내용은 [애플리케이션을 Azure Active Directory로 마이그레이션하기 위한 리소스](../manage-apps/migration-resources.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- [Microsoft MFA 서버에서 Azure Multi-Factor Authentication으로 마이그레이션(개요)](how-to-migrate-mfa-server-to-azure-mfa.md)
- [Windows Active Directory에서 Azure Active Directory로 애플리케이션 마이그레이션](../manage-apps/migrate-application-authentication-to-azure-active-directory.md)
- [클라우드 인증 전략 계획](../fundamentals/active-directory-deployment-plans.md)