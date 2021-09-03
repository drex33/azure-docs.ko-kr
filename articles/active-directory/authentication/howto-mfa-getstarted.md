---
title: Azure AD Multi-Factor Authentication을 위한 배포 고려 사항
description: Azure AD Multi-Factor Authentication의 성공적인 구현을 위한 배포 고려 사항 및 전략에 대해 알아봅니다.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/22/2021
ms.author: BaSelden
author: BarbaraSelden
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 120e97e62980427fb83c6bf7884da92dd9c5e1f7
ms.sourcegitcommit: 3941df51ce4fca760797fa4e09216fcfb5d2d8f0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2021
ms.locfileid: "114602318"
---
# <a name="plan-an-azure-active-directory-multi-factor-authentication-deployment"></a>Azure Active Directory Multi-Factor Authentication 배포 계획 

Azure AD(Azure Active Directory) MFA(Multi-Factor Authentication)는 데이터와 애플리케이션에 대한 액세스를 보호하고, 두 번째 형태의 인증을 사용하여 다른 보안 계층을 제공하는 데 도움이 됩니다. 조직은 [조건부 액세스](../conditional-access/overview.md)로 다단계 인증을 사용하도록 설정하여 솔루션을 특정 요구 사항에 맞게 만들 수 있습니다.

이 배포 가이드에서는 [Azure AD MFA](concept-mfa-howitworks.md) 롤아웃을 계획하고 구현하는 방법을 보여 줍니다.

## <a name="prerequisites-for-deploying-azure-ad-mfa"></a>Azure AD MFA를 배포하기 위한 필수 조건

배포를 시작하기 전에 관련 시나리오에 대한 다음 필수 조건을 충족해야 합니다.

| 시나리오 | 필수 요소 |
|----------|--------------|
|최신 인증을 사용하는 **클라우드 전용** ID 환경 | **필수 선행 작업 없음** |
|**하이브리드 ID** 시나리오 | [Azure AD Connect](../hybrid/whatis-hybrid-identity.md)를 배포하고 온-프레미스 AD DS(Active Directory Domain Services)와 Azure AD 간에 사용자 ID를 동기화합니다. |
| 클라우드 액세스용으로 게시된 **온-프레미스 레거시 애플리케이션**| [Azure AD 애플리케이션 프록시](../app-proxy/application-proxy-deployment-plan.md) 배포 |

## <a name="choose-authentication-methods-for-mfa"></a>MFA 인증 방법 선택

2단계 인증에 사용할 수 있는 여러 가지 방법이 있습니다. 사용 가능한 인증 방법 목록에서 선택하여 보안, 유용성, 가용성 측면에서 각각 평가할 수 있습니다.

>[!IMPORTANT]
>사용자가 기본 방법을 사용할 수 없는 경우 백업 방법을 사용할 수 있도록 둘 이상의 MFA 방법을 사용하도록 설정합니다. 메서드에는 다음이 포함됩니다.

- [비즈니스용 Windows Hello](/windows/security/identity-protection/hello-for-business/hello-overview)
- [Microsoft Authenticator 앱](concept-authentication-authenticator-app.md)
- [FIDO2 보안 키(미리 보기)](concept-authentication-passwordless.md#fido2-security-keys)
- [OATH 하드웨어 토큰(미리 보기)](concept-authentication-oath-tokens.md#oath-hardware-tokens-preview)
- [OATH 소프트웨어 토큰](concept-authentication-oath-tokens.md#oath-software-tokens)
- [SMS 확인](concept-authentication-phone-options.md#mobile-phone-verification)
- [음성 통화 확인](concept-authentication-phone-options.md)

테넌트에서 사용할 인증 방법을 선택하는 경우 다음 방법의 보안과 유용성을 고려합니다.

![적합한 인증 방법 선택](media/concept-authentication-methods/authentication-methods.png)

각 방법의 강도와 보안, 작동 방식에 대한 자세한 내용은 다음 리소스를 참조하세요.

- [Azure Active Directory에서 사용할 수 있는 인증 및 확인 방법은 무엇인가요?](concept-authentication-methods.md)
- [비디오: 조직을 안전하게 유지하기 위한 올바른 인증 방법 선택](https://youtu.be/LB2yj4HSptc)

[PowerShell 스크립트](/samples/azure-samples/azure-mfa-authentication-method-analysis/azure-mfa-authentication-method-analysis/)를 사용하여 사용자의 MFA 구성을 분석하고 적절한 MFA 인증 방법을 제안할 수 있습니다. 

최상의 유연성과 유용성을 얻으려면 Microsoft Authenticator 앱을 사용합니다. 이 인증 방법은 암호 없음, MFA 푸시 알림 및 OATH 코드와 같은 최상의 사용자 환경과 여러 모드를 제공합니다. 또한 Microsoft Authenticator 앱은 NIST(미국 국립표준기술원) [인증자 보증 수준 2 요구 사항](../standards/nist-authenticator-assurance-level-2.md)을 충족합니다.

테넌트에서 사용 가능한 인증 방법을 제어할 수 있습니다. 예를 들어 SMS와 같은 가장 안전하지 않은 몇 가지 방법을 차단할 수 있습니다.

| 인증 방법 | 관리 위치 | 범위 지정 |
|-----------------------|-------------|---------|
| Microsoft Authenticator(푸시 알림 및 암호 없는 휴대폰 로그인)    | MFA 설정 또는
인증 방법 정책 | Authenticator 암호 없는 휴대폰 로그인의 범위를 사용자와 그룹으로 지정할 수 있음 |
| FIDO2 보안 키 | 인증 방법 정책 | 범위를 사용자와 그룹으로 지정할 수 있음 |
| 소프트웨어 또는 하드웨어 OATH 토큰 | MFA 설정 |     |
| SMS 확인 | MFA 설정 | 인증 정책에서 기본 인증을 위한 SMS 로그인을 관리합니다. SMS 로그인의 범위를 사용자와 그룹으로 지정할 수 있습니다. |
| 음성 통화 | 인증 방법 정책 |       |


## <a name="plan-conditional-access-policies"></a>조건부 액세스 정책 계획

Azure AD MFA는 조건부 액세스 정책으로 적용됩니다. 이 정책을 사용하면 보안이 필요한 경우 사용자에게 다단계 인증을 요청하는 메시지를 표시하고, 필요하지 않은 경우 사용자를 방해하지 않을 수 있습니다.

![개념적 조건부 액세스 프로세스 흐름](media/howto-mfa-getstarted/conditional-access-overview-how-it-works.png)

Azure Portal의 **Azure Active Directory** > **보안** > **조건부 액세스** 에서 조건부 액세스 정책을 구성합니다.

조건부 액세스 정책을 만드는 방법에 대한 자세한 내용은 [사용자가 Azure Portal에 로그인할 때 Azure AD MFA 확인 메시지를 표시하는 조건부 액세스 정책](tutorial-enable-azure-mfa.md)을 참조하세요. 이 문서는 다음 작업에 도움이 됩니다.

- 사용자 인터페이스를 숙지합니다.
- 조건부 액세스의 작동 방식을 체험합니다.

Azure AD 조건부 액세스 배포에 대한 엔드투엔드 지침은 [조건부 액세스 배포 계획](../conditional-access/plan-conditional-access.md)을 참조하세요.

### <a name="common-policies-for-azure-ad-mfa"></a>Azure AD MFA에 대한 일반 정책

Azure AD MFA를 요구하는 일반적인 사용 사례는 다음과 같습니다.

- [관리자](../conditional-access/howto-conditional-access-policy-admin-mfa.md)
- [특정 애플리케이션](tutorial-enable-azure-mfa.md)
- [모든 사용자](../conditional-access/howto-conditional-access-policy-all-users-mfa.md)
- [Azure 관리](../conditional-access/howto-conditional-access-policy-azure-management.md)
- [신뢰하지 않는 네트워크 위치](../conditional-access/untrusted-networks.md)

### <a name="named-locations"></a>명명된 위치

조건부 액세스 정책을 관리하기 위해 조건부 액세스 정책의 위치 조건을 사용하여 사용자의 네트워크 위치에 액세스 제어 설정을 연결할 수 있습니다. IP 주소 범위나 국가와 지역의 논리적 그룹을 만들 수 있도록 [명명된 위치](../conditional-access/location-condition.md)를 사용하는 것이 좋습니다. 이렇게 하면 명명된 위치의 로그인을 차단하는, 모든 앱에 대한 정책이 생성됩니다. 관리자는 이 정책에서 제외해야 합니다.

### <a name="risk-based-policies"></a>위험 기반 정책

조직에서 [Azure AD ID 보호](../identity-protection/overview-identity-protection.md)를 사용하여 위험 신호를 탐지하는 경우 명명된 위치 대신 [위험 기반 정책](../identity-protection/howto-identity-protection-configure-risk-policies.md)을 사용하는 것이 좋습니다. 손상된 ID 위협이 있을 때 암호 변경을 강제 적용하거나 로그인이 유출된 자격 증명, 익명 IP 주소에서의 로그인 등의 [이벤트로 인해 위험한 것](../identity-protection/overview-identity-protection.md#risk-detection-and-remediation)으로 간주될 때 다단계 인증을 요구하는 정책을 만들 수 있습니다. 

위험 정책에는 다음이 포함됩니다.

- [모든 사용자에게 Azure AD MFA에 등록하도록 요구](../identity-protection/howto-identity-protection-configure-mfa-policy.md)
- [위험 수준이 높은 사용자에게 암호 변경 요구](../identity-protection/howto-identity-protection-configure-risk-policies.md#enable-policies)
- [로그인 위험 수준이 중간이거나 높은 사용자에게 MFA 요구](../identity-protection/howto-identity-protection-configure-risk-policies.md#enable-policies)

### <a name="convert-users-from-per-user-mfa-to-conditional-access-based-mfa"></a>사용자별 MFA의 사용자를 조건부 액세스 기반 MFA로 변환

사용자가 사용자별로 적용된 Azure AD Multi-Factor Authentication을 사용하도록 설정했다면 다음 PowerShell을 사용하여 조건부 액세스 기반 Azure AD Multi-Factor Authentication으로 변환하는 데 도움을 받을 수 있습니다.

ISE 창에서 이 PowerShell을 실행하거나 로컬에 실행할 `.PS1` 파일로 저장합니다. [MSOnline 모듈](/powershell/module/msonline/?view=azureadps-1.0#msonline)을 통해서만 작업을 수행할 수 있습니다. 

```PowerShell
# Sets the MFA requirement state
function Set-MfaState {
    [CmdletBinding()]
    param(
        [Parameter(ValueFromPipelineByPropertyName=$True)]
        $ObjectId,
        [Parameter(ValueFromPipelineByPropertyName=$True)]
        $UserPrincipalName,
        [ValidateSet("Disabled","Enabled","Enforced")]
        $State
    )
    Process {
        Write-Verbose ("Setting MFA state for user '{0}' to '{1}'." -f $ObjectId, $State)
        $Requirements = @()
        if ($State -ne "Disabled") {
            $Requirement =
                [Microsoft.Online.Administration.StrongAuthenticationRequirement]::new()
            $Requirement.RelyingParty = "*"
            $Requirement.State = $State
            $Requirements += $Requirement
        }
        Set-MsolUser -ObjectId $ObjectId -UserPrincipalName $UserPrincipalName `
                     -StrongAuthenticationRequirements $Requirements
    }
}
# Disable MFA for all users
Get-MsolUser -All | Set-MfaState -State Disabled
```

## <a name="plan-user-session-lifetime"></a>사용자 세션 수명 계획

MFA 배포를 계획할 때는 사용자에게 메시지를 표시하는 빈도를 고려하는 것이 중요합니다. 사용자에게 자격 증명을 요청하는 것은 합리적으로 보이는 경우가 많지만, 역효과를 낳을 수도 있습니다. 사용자가 생각하지도 않고 자격 증명을 입력하도록 학습된 경우, 이를 실수로 악의적인 자격 증명 프롬프트에 제공할 수 있습니다.
Azure AD에는 사용자가 다시 인증해야 하는 빈도를 결정하는 여러 설정이 있습니다. 비즈니스 및 사용자의 요구 사항을 이해하고, 사용자 환경에 가장 적합한 균형을 제공하는 설정을 구성합니다.

최종 사용자 환경을 개선하기 위해 PRT(기본 새로 고침 토큰)가 있는 디바이스를 사용하고 특정 비즈니스 사용 사례에서만 로그인 빈도 정책을 사용하여 세션 수명을 줄이는 것이 좋습니다.

자세한 내용은 [Azure AD MFA의 재인증 프롬프트 최적화 및 세션 수명 이해](concepts-azure-multi-factor-authentication-prompts-session-lifetime.md)를 참조하세요.

## <a name="plan-user-registration"></a>사용자 등록 계획

모든 MFA 배포의 주요 단계는 MFA를 사용하도록 사용자를 등록하는 것입니다. 음성 및 SMS와 같은 인증 방법은 사전 등록을 허용하고 Authenticator 앱과 같은 다른 방법은 사용자 조작이 필요합니다. 관리자는 어떻게 사용자가 방법을 등록할지 결정해야 합니다. 

### <a name="combined-registration-for-sspr-and-azure-ad-mfa"></a>SSPR 및 Azure AD MFA를 위한 결합된 등록
Azure AD MFA와 [Azure AD SSPR(셀프 서비스 암호 재설정)](concept-sspr-howitworks.md)을 위해 [결합된 등록 환경](howto-registration-mfa-sspr-combined.md)을 사용하는 것이 좋습니다. SSPR를 사용하면 사용자가 Azure AD MFA에 사용하는 것과 동일한 방법으로 암호를 안전하게 재설정할 수 있습니다. 결합된 등록은 한 단계로 수행되는 최종 사용자용 작업입니다.

### <a name="registration-with-identity-protection"></a>ID 보호를 사용하여 등록
Azure AD ID 보호는 Azure AD MFA 사례에 대한 등록 정책과 자동화된 위험 검색 및 수정 정책에 모두 기여합니다. 손상된 ID 위협이 있을 때 암호 변경을 강제 적용하거나 로그인이 위험한 것으로 간주되는 경우 MFA를 요구하는 정책을 만들 수 있습니다.
Azure AD ID 보호를 사용하는 경우, 사용자가 다음에 대화형으로 로그인할 때 등록하라는 메시지를 표시하도록 [Azure AD MFA 등록 정책을 구성](../identity-protection/howto-identity-protection-configure-mfa-policy.md)합니다.

### <a name="registration-without-identity-protection"></a>ID 보호를 사용하지 않는 등록
Azure AD ID 보호를 사용할 수 있는 라이선스가 없는 경우 다음에 로그인 시 MFA가 필요할 때 MFA를 등록하라는 메시지가 사용자에게 표시됩니다. 사용자가 MFA를 사용하도록 요구하려면 조건부 액세스 정책을 사용하고 HR 시스템과 같은, 자주 사용하는 애플리케이션을 대상으로 지정할 수 있습니다. 사용자 암호가 손상된 경우 계정을 제어하고 MFA에 등록하는 데 사용할 수 있습니다. 따라서 신뢰할 수 있는 디바이스와 위치를 요구하는 [조건부 액세스 정책을 사용하여 보안 등록 프로세스를 보호](../conditional-access/howto-conditional-access-policy-registration.md)하는 것이 좋습니다. [임시 액세스 패스](howto-authentication-temporary-access-pass.md)도 요청하여 프로세스 보안을 강화할 수 있습니다. 강력한 인증 요구 사항을 충족하고 암호 없는 방법을 비롯한 다른 인증 방법을 온보딩하는 데 사용할 수 있는, 관리자가 발급한 시간 제한형 암호입니다.

### <a name="increase-the-security-of-registered-users"></a>등록된 사용자의 보안 강화
SMS 또는 음성 통화를 사용하여 MFA에 등록한 사용자가 있는 경우 Microsoft Authenticator 앱과 같은 더 안전한 방법으로 이동하는 것이 좋습니다. 이제 Microsoft는 로그인하는 동안 사용자에게 Microsoft Authenticator 앱을 설정하라는 메시지를 표시하는 기능을 퍼블릭 미리 보기로 제공합니다. 그룹으로 프롬프트를 설정하여 메시지가 표시되는 사용자를 제어하고 대상 캠페인에서 사용자를 더 안전한 방법으로 이동하도록 설정할 수 있습니다. 

### <a name="plan-recovery-scenarios"></a>복구 시나리오 계획 
앞서 언급했듯이, 한 방법을 사용할 수 없는 경우 백업을 이용할 수 있도록 사용자에 대해 둘 이상의 MFA 방법이 등록되어 있는지 확인합니다. 사용자에게 사용할 수 있는 백업 방법이 없는 경우 다음을 수행할 수 있습니다. 

- 사용자가 고유한 인증 방법을 관리할 수 있도록 임시 액세스 패스를 제공합니다. 임시 액세스 패스를 제공하여 리소스에 대한 임시 액세스를 허용할 수도 있습니다. 
- 관리자 권한으로 방법을 업데이트합니다. 이렇게 하려면 Azure Portal에서 사용자를 선택한 다음, 인증 방법을 선택하고 방법을 업데이트합니다.
사용자 통신

사용자에게 예정된 변경 내용, Azure AD MFA 등록 요구 사항, 필요한 모든 사용자 작업에 대해 알리는 것이 중요합니다. Microsoft는 [통신 템플릿](https://aka.ms/mfatemplates) 및 [최종 사용자 설명서](../user-help/security-info-setup-signin.md)를 제공하여 통신 초안을 간단하게 작성할 수 있도록 지원합니다. 사용자를 [https://myprofile.microsoft.com](https://myprofile.microsoft.com/)으로 보내 해당 페이지에서 **보안 정보** 링크를 선택하여 등록하게 합니다.

## <a name="plan-integration-with-on-premises-systems"></a>온-프레미스 시스템과 통합 계획

Azure AD에서 직접 인증하고 최신 인증(WS-Fed, SAML, OAuth, OpenID Connect)을 보유하는 애플리케이션은 조건부 액세스 정책을 사용할 수 있습니다.
Azure AD에 직접 인증하지 않는 일부 레거시 및 온-프레미스 애플리케이션에는 다음을 포함하여 Azure AD MFA를 사용하는 추가 단계가 필요합니다. Azure AD 애플리케이션 프록시나 [네트워크 정책 서비스](/windows-server/networking/core-network-guide/core-network-guide#BKMK_optionalfeatures)를 사용하여 통합할 수 있습니다.

### <a name="integrate-with-ad-fs-resources"></a>AD FS 리소스와 통합

AD FS(Active Directory Federation Services)로 보호되는 애플리케이션을 Azure AD로 마이그레이션하는 것이 좋습니다. 그러나 Azure AD로 마이그레이션할 준비가 되지 않은 경우 AD FS 2016 이상에서 Azure MFA 어댑터를 사용할 수 있습니다.
조직이 Azure AD와 페더레이션된 경우 온-프레미스와 클라우드 둘 다에서 [AD FS 리소스를 사용하여 Azure AD MFA를 인증 공급자로 구성](/windows-server/identity/ad-fs/operations/configure-ad-fs-and-azure-mfa)할 수 있습니다.  

### <a name="radius-clients-and-azure-ad-mfa"></a>RADIUS 클라이언트 및 Azure AD MFA

RADIUS 인증을 사용하는 애플리케이션의 경우 SAML, Open ID Connect 또는 Azure AD의 OAuth와 같은 최신 프로토콜로 클라이언트 애플리케이션을 이동하는 것이 좋습니다. 애플리케이션을 업데이트할 수 없는 경우 [Azure MFA 확장을 통해 NPS(네트워크 정책 서버)](howto-mfa-nps-extension.md)를 배포할 수 있습니다. NPS(네트워크 정책 서버) 확장은 RADIUS 기반 애플리케이션과 Azure AD MFA 간 어댑터 역할을 하여 두 번째 인증 요소를 제공합니다.

#### <a name="common-integrations"></a>일반적인 통합

이제 많은 공급업체가 해당 애플리케이션에서 SAML 인증을 지원합니다. 가능한 경우, Azure AD와 애플리케이션을 페더레이션하고 조건부 액세스를 통해 MFA를 적용하는 것이 좋습니다. 공급업체에서 최신 인증을 지원하지 않는 경우 NPS 확장을 사용할 수 있습니다.
일반적인 RADIUS 클라이언트 통합에는 [원격 데스크톱 게이트웨이](howto-mfa-nps-extension-rdg.md) 및 [VPN 서버](howto-mfa-nps-extension-vpn.md) 같은 애플리케이션이 포함됩니다. 

그 밖에 다음 항목도 포함할 수 있습니다.

- Citrix 게이트웨이

  [Citrix 게이트웨이](https://docs.citrix.com/en-us/advanced-concepts/implementation-guides/citrix-gateway-microsoft-azure.html#microsoft-azure-mfa-deployment-methods)는 RADIUS 및 NPS 확장 통합과 SAML 통합을 둘 다 지원합니다.

- Cisco VPN
  - Cisco VPN은 RADIUS와 [SSO용 SAML 인증](../saas-apps/cisco-anyconnect.md)을 둘 다 지원합니다.
  - RADIUS 인증에서 SAML로 전환하면 NPS 확장을 배포하지 않고 Cisco VPN을 통합할 수 있습니다.

- 모든 VPN

## <a name="deploy-azure-ad-mfa"></a>Azure AD MFA 배포

MFA 출시 계획에는 파일럿 배포와 지원 용량 내에 있는 배포 웨이브가 포함되어야 합니다. 소그룹의 파일럿 사용자에 조건부 액세스 정책을 적용하여 출시를 시작합니다. 파일럿 사용자, 사용된 프로세스 및 등록 동작에 미치는 영향을 평가한 후 정책에 그룹을 더 추가하거나 기존 그룹에 사용자를 더 추가할 수 있습니다.

아래 단계를 따릅니다.

1. 필요한 모든 필수 조건 충족
1. 선택한 인증 방법 구성
1. 조건부 액세스 정책 구성
1. 세션 수명 설정 구성
1. Azure AD MFA 등록 정책 구성 

## <a name="manage-azure-ad-mfa"></a>Azure AD MFA 관리
이 섹션에서는 Azure AD MFA에 대한 보고 및 문제 해결 정보를 제공합니다.

### <a name="reporting-and-monitoring"></a>보고 및 모니터링

Azure AD에는 기술 및 비즈니스 정보를 제공하고, 배포 진행 상황을 따르고, 사용자가 MFA를 사용하여 로그인에 성공했는지 확인하는 보고서가 있습니다. 비즈니스 및 기술 애플리케이션 소유자에게 조직의 요구 사항에 따라 이러한 보고서를 소유하고 사용하도록 합니다.

[인증 방법 작업 대시보드](howto-authentication-methods-activity.md)를 사용하여 조직 전체의 인증 방법 등록과 사용 현황을 모니터링할 수 있습니다. 이렇게 하면 등록되는 방법과 사용 방법을 파악하는 데 도움이 됩니다.

#### <a name="sign-in-report-to-review-mfa-events"></a>MFA 이벤트를 검토하기 위한 로그인 보고서

Azure AD 로그인 보고서에는 사용자에게 다단계 인증 메시지를 표시할 때, 그리고 조건부 액세스 정책이 사용 중인 경우 이벤트 인증 세부 정보가 포함됩니다. MFA에 등록된 사용자 보고를 위해 PowerShell을 사용할 수도 있습니다. 

NPS 확장과 AD FS 로그는 **보안** > **MFA** > **작업 보고서** 에서 볼 수 있습니다.

자세한 내용과 추가 MFA 보고서는 [Azure AD Multi-Factor Authentication 이벤트 검토](howto-mfa-reporting.md#view-the-azure-ad-sign-ins-report)를 참조하세요.

### <a name="troubleshoot-azure-ad-mfa"></a>Azure AD MFA 문제 해결
일반적인 문제는 [Azure AD MFA 문제 해결](https://support.microsoft.com/help/2937344/troubleshooting-azure-multi-factor-authentication-issues)을 참조하세요.

## <a name="next-steps"></a>다음 단계

[다른 ID 기능 배포](../fundamentals/active-directory-deployment-plans.md)
