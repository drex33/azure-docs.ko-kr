---
title: Azure Active Directory Authentication 관리 작업 참조 가이드
description: 이 작업 참조 가이드에서는 인증 관리를 보호하기 위해 수행해야 하는 검사 및 작업에 관해 설명함
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: fundamentals
ms.date: 10/31/2019
ms.author: martinco
ms.openlocfilehash: c1fa9d27bbdfe91eebb74186e69cf707166384fd
ms.sourcegitcommit: da9335cf42321b180757521e62c28f917f1b9a07
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/16/2021
ms.locfileid: "122530291"
---
# <a name="azure-active-directory-authentication-management-operations-reference-guide"></a>Azure Active Directory Authentication 관리 작업 참조 가이드

[Azure AD 작업 참조 가이드](active-directory-ops-guide-intro.md)의 이 섹션에서는 자격 증명을 보호하고 관리하며, 인증 환경을 정의하고, 할당을 위임하며, 사용량을 측정하고, 엔터프라이즈 보안 태세에 기반하여 액세스 정책을 정의하기 위해 수행해야 하는 검사 및 작업에 관해 설명합니다.

> [!NOTE]
> 이 권장 사항은 게시 날짜를 기준으로 최신이지만 시간이 지나면 변경될 수 있습니다. Microsoft 제품과 서비스는 계속해서 개선되므로 조직은 ID 사용 관행을 지속해서 평가해야 합니다.

## <a name="key-operational-processes"></a>주요 운영 프로세스

### <a name="assign-owners-to-key-tasks"></a>주요 작업에 소유자 할당

Azure Active Directory를 관리하려면 출시 프로젝트의 일부가 아닐 수 있는 주요 운영 작업 및 프로세스를 지속해서 실행해야 합니다. 환경을 최적화하기 위해 이 작업을 설정하는 것도 여전히 중요합니다. 핵심 작업과 권장 소유자는 다음과 같습니다.

| Task | 소유자 |
| :- | :- |
| Azure AD에서 SSO(Single Sign-On) 구성의 수명 주기 관리 | IAM 운영 팀 |
| Azure AD 애플리케이션의 조건부 액세스 정책 디자인 | InfoSec 아키텍처 팀 |
| SIEM 시스템에서 로그인 작업 보관 | InfoSec 운영 팀 |
| SIEM 시스템에서 위험 이벤트 보관 | InfoSec 운영 팀 |
| 보안 보고서 심사 및 조사 | InfoSec 운영 팀 |
| 위험 이벤트 심사 및 조사 | InfoSec 운영 팀 |
| Azure AD ID 보호에서 위험 및 취약성 보고서에 대해 플래그가 지정된 사용자를 심사하고 조사 | InfoSec 운영 팀 |

> [!NOTE]
> Azure AD ID 보호에는 Azure AD Premium P2 라이선스가 필요합니다. 요구 사항에 적합한 라이선스를 찾으려면 [Azure AD Free 및 Azure AD Premium 버전의 일반적으로 사용할 수 있는 기능 비교](https://www.microsoft.com/security/business/identity-access-management/azure-ad-pricing)를 참조하세요.

목록을 검토할 때 소유자가 누락된 작업에 대해 소유자를 할당하거나 위의 권장 사항에 맞지 않는 소유자가 있는 작업에 대한 소유권을 조정해야 할 수도 있습니다.

#### <a name="owner-recommended-reading"></a>소유자 권장 읽기

- [Azure Active Directory에서 관리자 역할 할당](../roles/permissions-reference.md)
- [Azure에서 거버넌스](../../governance/index.yml)

## <a name="credentials-management"></a>자격 증명 관리

### <a name="password-policies"></a>암호 정책

암호를 안전하게 관리하는 것은 ID 및 액세스 관리의 가장 중요한 부분 중 하나이며 가장 큰 공격의 대상이 되는 경우가 많습니다. Azure AD는 공격이 성공하지 못하게 하는 데 도움이 되는 몇 가지 기능을 지원합니다.

아래 표를 사용하여 해결해야 하는 문제를 완화하기 위한 권장 솔루션을 찾을 수 있습니다.

| 문제 | 권장 |
| :- | :- |
| 보안이 약한 암호로부터 보호하는 메커니즘이 없음 | Azure AD [SSPR(셀프 서비스 암호 재설정)](../authentication/concept-sspr-howitworks.md)과 [암호 보호](../authentication/concept-password-ban-bad-on-premises.md)를 사용하도록 설정 |
| 누출된 암호를 탐지하는 메커니즘이 없음 | 인사이트를 얻기 위해 PHS([ 암호 해시 동기화](../hybrid/how-to-connect-password-hash-synchronization.md)) 사용 |
| AD FS를 사용하여 관리형 인증으로 이동할 수 없음 | [AD FS 엑스트라넷 스마트 잠금](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection) 및/또는 [Azure AD 스마트 잠금](../authentication/howto-password-smart-lockout.md) 사용 |
| 암호 정책에서는 길이, 다중 문자 세트 또는 만료와 같은 복잡성 기반 규칙을 사용함 | [Microsoft 권장 사례](https://www.microsoft.com/research/publication/password-guidance/?from=http%3A%2F%2Fresearch.microsoft.com%2Fpubs%2F265143%2Fmicrosoft_password_guidance.pdf)를 고려하고, 암호 관리 접근 방식을 전환하며, [Azure AD 암호 보호](../authentication/concept-password-ban-bad.md)를 배포합니다. |
| 사용자가 MFA(다단계 인증)를 사용하도록 등록되지 않음 | 암호와 함께 사용자의 ID를 확인하는 메커니즘으로 사용할 수 있도록 [모든 사용자의 보안 정보 등록](../identity-protection/howto-identity-protection-configure-mfa-policy.md) |
| 사용자 위험을 기반으로 하는 암호 해지 없음 | SSPR을 사용하여 유출된 자격 증명의 암호를 강제로 변경하도록 Azure AD [ID 보호 사용자 위험 정책](../identity-protection/howto-identity-protection-configure-risk-policies.md) 배포 |
| 식별된 IP 주소에서 들어오는 악의적인 작업자로부터 악성 인증을 보호하는 스마트 잠금 메커니즘이 없음 | 암호 해시 동기화 또는 PTA([통과 인증](../hybrid/how-to-connect-pta-quick-start.md))를 사용하여 클라우드 관리형 인증 배포 |

#### <a name="password-policies-recommended-reading"></a>암호 정책 권장 읽기

- [Azure AD 및 AD FS 모범 사례: 암호 스프레이 공격으로부터 방어 - Enterprise Mobility + Security](https://cloudblogs.microsoft.com/enterprisemobility/2018/03/05/azure-ad-and-adfs-best-practices-defending-against-password-spray-attacks/)

### <a name="enable-self-service-password-reset-and-password-protection"></a>셀프 서비스 암호 재설정 및 암호 보호 사용

암호를 변경하거나 재설정해야 하는 사용자가 지원 센터 통화량과 비용의 가장 큰 원인 중 하나입니다. 비용 외에도 사용자 위험을 완화하기 위한 도구로서 암호를 변경하는 것은 조직의 보안 태세를 개선하는 기본적인 단계입니다.

최소한 Azure AD SSPR([셀프 서비스 암호 재설정](../authentication/concept-sspr-howitworks.md)) 및 온-프레미스 [암호 보호](../authentication/howto-password-ban-bad-on-premises-deploy.md)를 배포하여 다음을 수행하는 것이 좋습니다.

- 지원 센터 전화를 방지합니다.
- 임시 암호 사용을 바꿉니다.
- 온-프레미스 솔루션을 사용하는 기존 셀프 서비스 암호 관리 솔루션을 바꿉니다.
- 조직에서 [보안이 약한 암호를 제거](../authentication/concept-password-ban-bad.md)합니다.

> [!NOTE]
> Azure AD Premium P2 구독이 있는 조직의 경우 SSPR를 배포하고 [ID 보호 사용자 위험 정책](../identity-protection/howto-identity-protection-configure-risk-policies.md)의 일부로 사용하는 것이 좋습니다.

### <a name="strong-credential-management"></a>강력한 자격 증명 관리

암호만으로는 잘못된 작업자가 사용자 환경에 액세스하는 것을 방지할 만큼 충분히 안전하지 않습니다. 적어도 권한이 부여된 계정을 사용하는 모든 사용자는 MFA(다단계 인증)가 가능하도록 설정되어야 합니다. [결합 등록](../authentication/concept-registration-mfa-sspr-combined.md)을 사용으로 설정하고 모든 사용자가 [결합 등록 환경](../user-help/security-info-setup-signin.md)을 사용하여 MFA 및 SSPR에 등록하는 것이 가장 적합합니다. 결국 예상치 못한 상황으로 인한 잠금 위험을 줄이기 위해 [복원력 제공](../authentication/concept-resilient-controls.md) 전략을 채택하는 것이 좋습니다.

![결합된 사용자 환경 흐름](./media/active-directory-ops-guide/active-directory-ops-img4.png)

### <a name="on-premises-outage-authentication-resiliency"></a>온-프레미스 중단 인증 복원력

간편하게 유출된 자격 증명을 탐지하는 이점 외에도 Azure AD PHS(암호 해시 동기화) 및 Azure AD MFA를 사용하면 [NotPetya](https://www.microsoft.com/security/blog/2018/02/05/overview-of-petya-a-rapid-cyberattack/)와 같은 사이버 공격으로 인한 온-프레미스 중단에도 불구하고 SaaS 애플리케이션과 Microsoft 365에 액세스할 수 있습니다. 페더레이션과 함께 PHS를 사용으로 설정할 수도 있습니다. PHS를 사용으로 설정하면 페더레이션 서비스를 사용할 수 없을 때 인증을 대체할 수 있습니다.

온-프레미스 조직에 중단 복구 전략이 없거나 Azure AD와 통합되지 않은 전략이 있으면 Azure AD PHS를 배포하고 PHS를 포함하는 재해 복구 플랜을 정의해야 합니다. Azure AD PHS를 사용하도록 설정하면 사용자가 온-프레미스 Active Directory를 사용할 수 없는 경우 Azure AD에 대해 인증할 수 있습니다.

![PHS(암호 해시 동기화) 흐름](./media/active-directory-ops-guide/active-directory-ops-img5.png)

인증 옵션에 관한 이해를 돕도록 [Azure Active Directory 하이브리드 ID 솔루션용으로 올바른 인증 방법 선택](../hybrid/choose-ad-authn.md)을 참조하세요.

### <a name="programmatic-usage-of-credentials"></a>자격 증명의 프로그래매틱 사용

PowerShell을 사용하는 Azure AD 스크립트 또는 Microsoft Graph API를 사용하는 애플리케이션에는 보안 인증이 필요합니다. 해당 스크립트 및 도구를 실행하는 자격 증명 관리가 잘못되면 자격 증명이 탈취될 위험이 증가합니다. 하드 코드된 암호 또는 암호 프롬프트를 사용하는 스크립트 또는 애플리케이션을 사용하는 경우 먼저 구성 파일 또는 소스 코드에서 암호를 검토한 다음, 종속성을 바꾸고 가능한 경우 Azure 관리 ID, Windows 통합 인증 또는 [인증서](../reports-monitoring/tutorial-access-api-with-certificates.md)를 사용해야 합니다. 이전 솔루션을 사용할 수 없는 애플리케이션의 경우 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)를 사용하는 것이 좋습니다.

암호 자격 증명이 있는 서비스 주체가 있고 해당 암호 자격 증명이 스크립트 또는 애플리케이션을 통해 어떻게 보호되는지 확실하지 않으면 사용 패턴에 관한 이해를 돕도록 애플리케이션 소유자에게 문의하세요.

또한 암호 자격 증명을 사용하는 서비스 주체가 있는 경우 애플리케이션 소유자에게 문의하여 사용 패턴을 이해하는 것이 좋습니다.

## <a name="authentication-experience"></a>인증 환경

### <a name="on-premises-authentication"></a>온-프레미스 인증

IWA(Windows 통합 인증)를 사용하는 페더레이션 인증 또는 암호 해시 동기화나 통과 인증을 사용하는 Seamless SSO(Single Sign-On)는 온-프레미스 도메인 컨트롤러와 송수신이 가능한 기업 네트워크 내에서 가장 좋은 사용자 환경입니다. 자격 증명 프롬프트 확인으로 인한 피로를 최소화하고 사용자가 피싱 공격에 노출될 위험을 줄입니다. PHS 또는 PTA에서 이미 클라우드 관리형 인증을 사용하고 있지만, 사용자가 온-프레미스 인증 시 여전히 암호를 입력해야 하는 경우 즉시 [Seamless SSO를 배포](../hybrid/how-to-connect-sso.md)해야 합니다. 반면에 결국 클라우드 관리형 인증으로 마이그레이션될 계획과 현재 페더레이션된 경우 마이그레이션 프로젝트의 일부로 Seamless SSO를 구현해야 합니다.

### <a name="device-trust-access-policies"></a>디바이스 신뢰 액세스 정책

디바이스는 조직의 사용자처럼 보호해야 하는 핵심 ID입니다. 디바이스의 ID를 사용하여 언제 어디서든 리소스를 보호할 수 있습니다. 디바이스를 인증하고 신뢰 형식을 확인하면 다음과 같은 방법으로 보안 태세와 유용성이 향상됩니다.

- 예를 들어 디바이스를 신뢰할 수 있는 경우 MFA를 사용하여 마찰 방지
- 신뢰할 수 없는 디바이스에서의 액세스 차단
- Windows 10 디바이스의 경우 [온-프레미스 리소스에 대한 원활한 SSO(Single Sign-On)](../devices/azuread-join-sso.md)를 제공합니다.

다음 방법 중 하나를 사용하여 디바이스 ID를 Azure AD로 가져와서 관리하면 이 목표를 달성할 수 있습니다.

- 조직은 [Microsoft Intune](/intune/what-is-intune)을 사용하여 디바이스를 관리하고 규정 준수 정책을 시행하며 디바이스 상태를 증명하고 디바이스의 규정 준수 여부에 따라 조건부 액세스 정책을 설정할 수 있습니다. Microsoft Intune은 iOS 디바이스, Mac 데스크톱(JAMF 통합을 통함), Windows 데스크톱(기본적으로 Windows 10용 모바일 디바이스 관리 사용, Microsoft Endpoint Configuration Manager와 공동 관리) 및 Android 모바일 디바이스를 관리할 수 ​​있습니다.
- [하이브리드 Azure AD 조인](../devices/hybrid-azuread-join-managed-domains.md)은 Active Directory 도메인 조인 컴퓨터 디바이스가 있는 환경에서 그룹 정책 또는 Microsoft Endpoint Configuration Manager를 사용하여 관리 기능을 제공합니다. 조직은 Seamless SSO를 사용하는 PTA 또는 PHS를 통해 관리형 환경을 배포할 수 있습니다. 디바이스를 Azure AD로 가져오면 클라우드 및 온-프레미스 리소스에서 SSO를 통해 사용자 생산성을 극대화하는 동시에 [조건부 액세스](../conditional-access/overview.md)를 사용하여 클라우드 및 온-프레미스 리소스에 대한 액세스를 보호할 수 있습니다.

클라우드에 등록되지 않은 도메인 조인 Windows 디바이스 또는 클라우드에 등록되었지만 조건부 액세스 정책이 없는 도메인 조인 Windows 디바이스가 있으면 등록되지 않은 디바이스를 등록해야 하며, 두 경우 모두 조건부 액세스 정책에서 [Hybrid Azure AD 조인을 제어로 사용](../conditional-access/require-managed-devices.md)해야 합니다.

![하이브리드 디바이스가 필요한 조건부 액세스 정책의 권한 부여 스크린샷](./media/active-directory-ops-guide/active-directory-ops-img6.png)

MDM 또는 Microsoft Intune으로 디바이스를 관리하지만 조건부 액세스 정책에서 디바이스 제어를 사용하지 않는 경우 [디바이스를 준수로 표시해야 함](../conditional-access/require-managed-devices.md#require-device-to-be-marked-as-compliant)을 해당 정책의 제어로 사용하는 것이 좋습니다.

![디바이스 준수가 필요한 조건부 액세스 정책의 권한 부여 스크린샷](./media/active-directory-ops-guide/active-directory-ops-img7.png)

#### <a name="device-trust-access-policies-recommended-reading"></a>디바이스 신뢰 액세스 정책 권장 읽기

- [방법: 하이브리드 Azure Active Directory 조인 구현 계획](../devices/hybrid-azuread-join-plan.md)
- [ID 및 디바이스 액세스 구성](/microsoft-365/enterprise/microsoft-365-policies-configurations)

### <a name="windows-hello-for-business"></a>비즈니스용 Windows Hello

Windows 10에서 [비즈니스용 Windows Hello](/windows/security/identity-protection/hello-for-business/hello-identity-verification)는 PC에서 암호 대신 강력한 2단계 인증을 사용합니다. 비즈니스용 Windows Hello를 사용하면 더 간소화된 MFA 환경을 제공하고 암호에 대한 종속성이 완화됩니다. Windows 10 디바이스를 출시하지 않았거나 일부만 배포한 경우 Windows 10으로 업그레이드하고 모든 디바이스에서 [비즈니스용 Windows Hello를 사용](/windows/security/identity-protection/hello-for-business/hello-manage-in-organization)하는 것이 좋습니다.

암호 없는 인증에 관해 자세히 알아보려면 [Azure Active Directory를 사용하는 암호 없는 세계](../authentication/concept-authentication-passwordless.md)를 참조하세요.

## <a name="application-authentication-and-assignment"></a>애플리케이션 인증 및 할당

### <a name="single-sign-on-for-apps"></a>앱의 Single Sign-On

최상의 사용자 환경, 위험 완화, 보고 기능 및 거버넌스를 위해 전체 엔터프라이즈에 표준화된 Single Sign-On 메커니즘을 제공하는 것이 중요합니다. Azure AD에서 SSO를 지원하지만 현재 로컬 계정을 사용하도록 구성된 애플리케이션을 사용하는 경우 Azure AD에서 SSO를 사용하도록 해당 애플리케이션을 다시 구성해야 합니다. 마찬가지로 Azure AD에서 SSO를 지원하지만 또 다른 ID 공급자를 사용하는 애플리케이션을 사용하는 경우 Azure AD에서 SSO도 사용하도록 해당 애플리케이션을 다시 구성해야 합니다. 페더레이션 프로토콜을 지원하지 않지만 양식 기반 인증을 지원하는 애플리케이션의 경우 Azure AD 애플리케이션 프록시와 함께 [암호 보관](../app-proxy/application-proxy-configure-single-sign-on-password-vaulting.md)을 사용하도록 애플리케이션을 구성하는 것이 좋습니다.

![AppProxy 암호 기반 로그온](./media/active-directory-ops-guide/active-directory-ops-img8.png)

> [!NOTE]
> 조직에서 비관리형 애플리케이션을 검색할 수 있는 메커니즘이 없으면 [Microsoft Cloud App Security](https://www.microsoft.com/enterprise-mobility-security/cloud-app-security)와 같은 CASB(클라우드 액세스 보안 브로커 솔루션)를 사용하여 검색 프로세스를 구현하는 것이 좋습니다.

마지막으로 Azure AD 앱 갤러리가 있고 Azure AD에서 SSO를 지원하는 애플리케이션을 사용하는 경우 [앱 갤러리에 애플리케이션을 나열](../develop/v2-howto-app-gallery-listing.md)하는 것이 좋습니다.

#### <a name="single-sign-on-recommended-reading"></a>Single Sign-On 권장 읽기

- [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)

### <a name="migration-of-ad-fs-applications-to-azure-ad"></a>Azure AD로 AD FS 애플리케이션 마이그레이션

[앱을 AD FS에서 Azure AD로 마이그레이션](../manage-apps/migrate-adfs-apps-to-azure.md)하면 보안에 관한 추가 기능, 더 일관된 관리 효율성, 향상된 협업 환경을 사용할 수 있습니다. Azure AD에서 SSO를 지원하는 애플리케이션이 AD FS에 구성된 경우 Azure AD에서 SSO를 사용하도록 해당 애플리케이션을 다시 구성해야 합니다. Azure AD에서 지원하지 않는 일반적이지 않은 구성을 사용하여 AD FS에 구성된 애플리케이션이 있으면 앱 소유자에게 문의하여 특수 구성이 애플리케이션의 절대 요구 사항인지 파악해야 합니다. 필요하지 않으면 Azure AD에서 SSO를 사용하도록 애플리케이션을 다시 구성해야 합니다.

![Azure AD를 주 ID 공급자로 구성](./media/active-directory-ops-guide/active-directory-ops-img9.png)

> [!NOTE]
> [ADFS용 Azure AD Connect Health](../hybrid/how-to-connect-health-adfs.md)는 잠재적으로 Azure AD로 마이그레이션할 수 있는 각 애플리케이션에 관한 구성 세부 정보를 수집하는 데 사용할 수 있습니다.

### <a name="assign-users-to-applications"></a>애플리케이션에 사용자 할당

[사용자를 애플리케이션에 할당](../manage-apps/assign-user-or-group-access-portal.md)할 때 그룹을 사용하면 최적으로 매핑됩니다. 그룹을 사용하면 유연성이 향상되고 대규모로 관리할 수 있기 때문입니다. 그룹을 사용하면 [특성 기반 동적 그룹 구성원](../enterprise-users/groups-dynamic-membership.md) 및 [앱 소유자로 위임](../fundamentals/active-directory-accessmanagement-managing-group-owners.md)과 같은 이점이 있습니다. 따라서 이미 그룹을 사용하고 관리 중인 경우 다음 작업을 수행하여 대규모로 관리를 개선하는 것이 좋습니다.

- 그룹 관리 및 거버넌스를 애플리케이션 소유자에게 위임합니다.
- 애플리케이션에 대한 셀프 서비스 액세스를 허용합니다.
- 사용자 특성을 사용하여 애플리케이션에 대한 액세스 권한을 일관되게 판별할 수 있으면 동적 그룹을 정의합니다.
- [Azure AD 액세스 검토](../governance/access-reviews-overview.md)를 사용하여 애플리케이션 액세스에 사용되는 그룹에 대한 증명을 구현합니다.

반면에 개별 사용자에게 할당된 애플리케이션을 찾은 경우 해당 애플리케이션에 관한 [거버넌스](../governance/index.yml)를 구현해야 합니다.

#### <a name="assign-users-to-applications-recommended-reading"></a>애플리케이션에 사용자 할당 권장 읽기

- [Azure Active Directory에서 애플리케이션에 사용자 및 그룹 할당](../manage-apps/assign-user-or-group-access-portal.md)
- [Azure Active Directory에서 앱 등록 권한 위임](../roles/delegate-app-roles.md)
- [Azure Active Directory의 그룹에 대한 동적 멤버 자격 규칙](../enterprise-users/groups-dynamic-membership.md)

## <a name="access-policies"></a>액세스 정책

### <a name="named-locations"></a>명명된 위치

Azure AD에서 [명명된 위치](../conditional-access/location-condition.md)를 사용하여 조직에 신뢰할 수 있는 IP 주소 범위 레이블을 지정할 수 있습니다. Azure AD는 명명된 위치를 사용하여 다음 작업을 수행합니다.

- 위험 이벤트에서 가양성을 방지합니다. 신뢰할 수 있는 네트워크 위치에서 로그인하면 사용자의 로그인 위험이 줄어듭니다.
- [위치 기반 조건부 액세스](../conditional-access/location-condition.md)를 구성합니다.

![명명된 위치](./media/active-directory-ops-guide/active-directory-ops-img10.png)

우선 순위에 따라 아래 표를 사용하여 조직의 요구 사항에 가장 적합한 권장 솔루션을 찾습니다.

| **우선 순위** | **시나리오** | **권장** |
| ------------ | -------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------- |
| 1 | PHS 또는 PTA를 사용하고 명명된 위치가 정의되지 않은 경우 | 위험 이벤트 탐지를 향상하기 위해 명명된 위치 정의 |
| 2 | 페더레이션하고 “insideCorporateNetwork” 클레임을 사용하지 않으며 명명된 위치가 정의되지 않은 경우 | 위험 이벤트 탐지를 향상하기 위해 명명된 위치 정의 |
| 3 | 조건부 액세스 정책에서 명명된 위치를 사용하지 않고 조건부 액세스 정책에 위험 또는 디바이스 제어가 없는 경우 | 명명된 위치를 포함하도록 조건부 액세스 정책 구성 |
| 4 | 페더레이션하고 “insideCorporateNetwork” 클레임을 사용하며 명명된 위치가 정의되지 않은 경우 | 위험 이벤트 탐지를 향상하기 위해 명명된 위치 정의 |
| 5 | 명명된 위치가 아닌 MFA에서 신뢰할 수 있는 IP 주소를 사용하고 이를 신뢰할 수 있는 것으로 표시하는 경우 | 명명된 위치를 정의하고 신뢰할 수 있는 것으로 표시하여 위험 이벤트 탐지 향상 |

### <a name="risk-based-access-policies"></a>위험 기반 액세스 정책

Azure AD는 모든 로그인 및 모든 사용자에 대한 위험을 계산할 수 있습니다. 위험을 액세스 정책의 기준으로 사용하면 인증 프롬프트 수가 감소하고 보안이 강화되는 등 더 나은 사용자 환경을 제공할 수 있습니다. 예를 들어 필요한 경우에만 사용자에게 프롬프트를 표시하고 대응과 수정을 자동화할 수 있습니다.

![로그인 위험 정책](./media/active-directory-ops-guide/active-directory-ops-img11.png)

액세스 정책에서 위험 사용을 지원하는 Azure AD Premium P2 라이선스를 이미 소유하고 있지만 사용하지 않는 경우 보안 태세에 위험을 추가하는 것이 좋습니다.

#### <a name="risk-based-access-policies-recommended-reading"></a>위험 기반 액세스 정책 권장 읽기

- [방법: 로그인 위험 정책 구성](../identity-protection/howto-identity-protection-configure-risk-policies.md)
- [방법: 사용자 위험 정책 구성](../identity-protection/howto-identity-protection-configure-risk-policies.md)

### <a name="client-application-access-policies"></a>클라이언트 애플리케이션 액세스 정책

MAM(Microsoft Intune Application Management)은 스토리지 암호화, PIN, 원격 스토리지 정리 등과 같은 데이터 보호 제어를 Outlook Mobile과 같이 호환되는 클라이언트 모바일 애플리케이션에 푸시하는 기능을 제공합니다. 또한 조건부 액세스 정책을 만들어 승인되거나 호환되는 앱에서 Exchange Online과 같은 클라우드 서비스에 대한 [액세스를 제한](../conditional-access/app-based-conditional-access.md)할 수 있습니다.

BYOD(Bring Your Own Device)가 지원되는 환경에서 직원이 Office 모바일 앱과 같은 MAM 지원 애플리케이션을 설치하여 Exchange Online 또는 SharePoint Online과 같은 회사 리소스에 액세스하는 경우, MDM을 등록하지 않고 개인이 소유한 디바이스의 애플리케이션 구성을 관리할 애플리케이션 MAM 정책을 배포한 후에 MAM 지원 클라이언트에서만 액세스할 수 있도록 조건부 액세스 정책을 업데이트하는 것이 좋습니다.

![조건부 액세스 권한 부여 제어](./media/active-directory-ops-guide/active-directory-ops-img12.png)

직원이 회사 리소스에 대해 MAM 가능 애플리케이션을 설치하고 Intune Managed 디바이스에서 액세스가 제한되는 경우 개인 디바이스의 애플리케이션 구성을 관리하기 위해 애플리케이션 MAM 정책을 배포하고 MAM 가능 클라이언트의 액세스만 허용하도록 조건부 액세스 정책을 업데이트해야 합니다.

### <a name="conditional-access-implementation"></a>조건부 액세스 구현

조건부 액세스는 조직의 보안 태세를 개선하기 위한 필수 도구입니다. 따라서 다음과 같은 모범 사례를 따르는 것이 중요합니다.

- 모든 SaaS 애플리케이션에 하나 이상의 정책이 적용되어 있는지 확인
- 잠금 위험을 방지하려면 **모든 앱** 필터를 **차단** 제어와 결합하지 않음
- **모든 사용자** 를 필터로 사용하지 말고 실수로 **게스트** 를 추가하지 않음
- **모든 “레거시” 정책을 Azure Portal로 마이그레이션**
- 사용자, 디바이스 및 애플리케이션에 관한 모든 조건 파악
- 조건부 액세스 정책을 사용하여 **사용자당 MFA** 를 사용하는 대신 [MFA 구현](../conditional-access/plan-conditional-access.md)
- 여러 애플리케이션에 적용할 수 있는 작은 핵심 정책 세트가 있음
- 비어 있는 예외 그룹을 정의하고 정책에 추가하여 예외 전략 포함
- MFA 제어가 없는 [비상](../roles/security-planning.md#break-glass-what-to-do-in-an-emergency) 계정 플랜
- Exchange Online 및 SharePoint Online과 같은 서비스용으로 동일한 제어 세트를 구현하여 Microsoft 365 클라이언트 애플리케이션(예: Teams, OneDrive, Outlook 등)에서 일관된 환경을 보장합니다.
- 정책에 대한 할당은 개인이 아닌 그룹을 통해 구현해야 함
- 정책에 사용된 예외 그룹을 정기적으로 검토하여 사용자가 보안 태세를 벗어나는 시간을 제한합니다. Azure AD P2를 소유하는 경우 액세스 검토를 사용하여 프로세스를 자동화할 수 있음

#### <a name="conditional-access-recommended-reading"></a>조건부 액세스 권장 읽기

- [Azure Active Directory의 조건부 액세스 모범 사례](../conditional-access/overview.md)
- [ID 및 디바이스 액세스 구성](/microsoft-365/enterprise/microsoft-365-policies-configurations)
- [Azure Active Directory 조건부 액세스 설정 참조](../conditional-access/concept-conditional-access-conditions.md)
- [일반 조건부 액세스 정책](../conditional-access/concept-conditional-access-policy-common.md)

## <a name="access-surface-area"></a>액세스 노출 영역

### <a name="legacy-authentication"></a>레거시 인증

MFA와 같은 강력한 자격 증명은 레거시 인증 프로토콜을 사용하여 앱을 보호할 수 없으므로, 악의적인 작업자가 선호하는 공격 벡터가 될 수 있습니다. 액세스 보안 태세가 향상되려면 레거시 인증을 잠그는 것이 중요합니다.

레거시 인증은 다음과 같은 앱에서 사용하는 인증 프로토콜을 지칭하는 용어입니다.

- 최신 인증을 사용하지 않는 이전 Office 클라이언트(예: Office 2010 클라이언트)
- IMAP/SMTP/POP와 같은 메일 프로토콜을 사용하는 클라이언트

공격자는 해당 프로토콜을 매우 선호합니다. 실제로 거의 [100%의 암호 스프레이 공격](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984)에서 기존 인증 프로토콜을 사용합니다. 해커는 다단계 인증 및 디바이스 인증과 같은 추가 보안 문제에 필요한 대화형 로그인을 지원하지 않기 때문에 레거시 인증 프로토콜을 사용합니다.

레거시 인증이 사용자 환경에서 널리 사용되는 경우 최대한 빨리 레거시 클라이언트를 [최신 인증](/office365/enterprise/modern-auth-for-office-2013-and-2016)을 지원하는 클라이언트로 마이그레이션하도록 계획해야 합니다. 같은 토큰에서 일부 사용자는 이미 최신 인증을 사용하고 다른 사용자는 여전히 레거시 인증을 사용하는 경우 다음 단계를 수행하여 레거시 인증 클라이언트를 잠가야 합니다.

1. [로그인 작업 보고서](../reports-monitoring/concept-sign-ins.md)를 사용하여 여전히 레거시 인증 및 플랜 수정을 사용하는 사용자를 식별합니다.

   a. 영향을 받는 사용자를 위해 최신 인증 지원 클라이언트로 업그레이드합니다.
   
   b. 아래 단계에 따라 잠글 전환 시간 프레임을 계획합니다.
   
   다. 레거시 인증에 대한 강한 종속성이 있는 레거시 애플리케이션을 식별합니다. 아래 3단계를 참조하세요.

2. 더 많은 노출을 피하기 위해 레거시 인증을 사용하지 않는 사용자에 대해 원본(예: Exchange 사서함)에서 레거시 프로토콜을 사용하지 않게 설정합니다.
3. 나머지 계정(서비스 계정과 다른 사용자의 ID가 가장 적합)의 경우 [조건부 액세스를 사용하여 레거시 프로토콜](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Conditional-Access-support-for-blocking-legacy-auth-is/ba-p/245417) 사후 인증을 제한합니다.

#### <a name="legacy-authentication-recommended-reading"></a>레거시 인증 권장 읽기

- [Exchange Server의 사서함에 대한 POP3 또는 IMAP4 액세스를 사용하거나 사용하지 않음](/exchange/clients/pop3-and-imap4/configure-mailbox-access)

### <a name="consent-grants"></a>동의 권한 부여

불법 동의 권한 부여 공격에서 공격자는 연락처 정보, 메일 또는 문서와 같은 데이터에 대한 액세스를 요청하는 Azure AD 등록 애플리케이션을 만듭니다. 사용자는 악성 웹 사이트에 접속할 때 피싱 공격을 통해 악성 애플리케이션에 동의할 수 있습니다.

다음은 Microsoft 클라우드 서비스에 관해 확인할 수 있는 권한이 있는 앱 목록입니다.

- 앱 또는 위임된 \*.ReadWrite 권한이 있는 앱
- 위임된 권한이 있는 앱은 사용자 대신 메일을 읽거나 보내거나 관리할 수 있음
- 다음 권한을 사용하여 권한이 부여된 앱:

| 리소스 | 사용 권한 |
| :- | :- |
| Exchange Online | EAS.AccessAsUser.All |
| | EWS.AccessAsUser.All |
| | Mail.Read |
| Microsoft Graph API | Mail.Read |
| | Mail.Read.Shared |
| | Mail.ReadWrite |

- 로그인한 사용자의 전체 사용자 가장 권한을 부여한 앱입니다. 예를 들면 다음과 같습니다.

|리소스 | 사용 권한 |
| :- | :- |
| Microsoft Graph API| Directory.AccessAsUser.All |
| Azure REST API | user_impersonation |

이 시나리오를 방지하려면 [Office 365에서 불법 동의 권한 부여 탐지 및 수정](/office365/securitycompliance/detect-and-remediate-illicit-consent-grants)을 참조하여 불법 권한이 부여된 애플리케이션이나 필요한 것보다 많은 권한이 부여된 애플리케이션을 식별하여 수정합니다. 그런 다음 [셀프 서비스를 모두 제거](../manage-apps/configure-user-consent.md)하고 [거버넌스 프로시저를 설정](../manage-apps/configure-admin-consent-workflow.md)합니다. 마지막으로, 앱 권한 정기 검토를 예약하고 필요하지 않으면 제거합니다.

#### <a name="consent-grants-recommended-reading"></a>동의 권한 부여 권장 읽기

- [Microsoft Graph API 권한](/graph/permissions-reference)

### <a name="user-and-group-settings"></a>사용자 및 그룹 설정

다음은 명시적인 비즈니스 요구 사항이 없는 경우 잠글 수 있는 사용자 및 그룹 설정입니다.

#### <a name="user-settings"></a>사용자 설정

- **외부 사용자** - Teams, Power BI, SharePoint Online 및 Azure Information Protection과 같은 서비스를 사용하여 기업에서 외부 협업을 유기적으로 진행할 수 있습니다. 사용자가 시작한 외부 협업을 제어하는 명시적 제약 조건이 있으면 [Azure AD Entitlement 관리](../governance/entitlement-management-overview.md)를 사용하거나 지원 센터를 통하는 등의 제어된 작업을 사용하여 외부 사용자를 사용하도록 설정하는 것이 좋습니다. 서비스에 대한 유기적인 외부 협업을 허용하지 않으려면 [멤버가 외부 사용자를 초대하지 못하도록 완전히 차단](../external-identities/delegate-invitations.md)할 수 있습니다. 또는 외부 사용자 초대에서 [특정 도메인을 허용하거나 차단](../external-identities/allow-deny-list.md)할 수도 있습니다.
- **앱 등록** - 앱 등록이 사용되면 최종 사용자가 직접 애플리케이션을 온보딩하고 데이터에 대한 액세스 권한을 부여할 수 있습니다. 앱 등록의 일반적인 예는 Outlook 플러그인을 사용으로 설정하는 사용자 또는 Alexa 및 Siri와 같은 음성 도우미가 메일 및 일정을 읽거나, 대신 메일을 보내는 것입니다. 고객이 앱 등록을 해제하기로 결정한 경우 InfoSec 및 IAM 팀은 관리자 계정으로 애플리케이션을 등록해야 하기 때문에 예외(비즈니스 요구 사항에 따라 필요한 앱 등록) 관리에 참여해야 하며 프로세스를 운영하기 위한 프로세스를 디자인해야 합니다.
- **관리 포털** - 관리자가 아닌 사용자가 Azure Portal에서 Azure AD 관리에 액세스할 수 없어 혼동을 받을 수 있도록 조직에서 Azure Portal의 Azure AD 블레이드를 잠글 수 있습니다. Azure AD 관리 포털의 사용자 설정으로 이동하여 액세스를 제한합니다.

![관리 포털 제한된 액세스](./media/active-directory-ops-guide/active-directory-ops-img13.png)

> [!NOTE]
> 관리자가 아닌 사용자는 여전히 명령줄 및 기타 프로그래밍 인터페이스를 통해 Azure AD 관리 인터페이스에 액세스할 수 있습니다.

#### <a name="group-settings"></a>그룹 설정

**셀프 서비스 그룹 관리/사용자는 보안 그룹/Microsoft 365 그룹을 만들 수 있습니다.** 현재 클라우드 그룹의 셀프 서비스 이니셔티브가 없으면 고객은 이 기능을 사용할 준비가 될 때까지 이 기능을 끌 수 있습니다.

#### <a name="groups-recommended-reading"></a>그룹 권장 읽기

- [Azure Active Directory B2B 협업이란?](../external-identities/what-is-b2b.md)
- [Azure Active Directory와 애플리케이션 통합](../develop/quickstart-register-app.md)
- [Azure Active Directory에서 앱, 사용 권한 및 동의.](../develop/quickstart-register-app.md)
- [Azure Active Directory에서 그룹을 사용하여 리소스에 대한 액세스 관리](./active-directory-manage-groups.md)
- [Azure Active Directory에서 셀프 서비스 애플리케이션 액세스 관리 설정](../enterprise-users/groups-self-service-management.md)

### <a name="traffic-from-unexpected-locations"></a>예기치 않은 위치에서의 트래픽

공격자는 전 세계 여러 지역에 있습니다. 위치를 조건으로 하는 조건부 액세스 정책을 사용하여 이 위험을 관리합니다. 조건부 액세스 정책에서 [위치 조건](../conditional-access/location-condition.md)을 사용하면 로그인할 사업상의 이유가 없는 위치에 대한 액세스를 차단할 수 있습니다.

![명명된 새 위치 만들기](./media/active-directory-ops-guide/active-directory-ops-img14.png)

사용할 수 있는 경우 SIEM(보안 정보 및 이벤트 관리) 솔루션을 사용하여 지역 간 액세스 패턴을 분석하고 찾습니다. SIEM 제품을 사용하지 않거나 Azure AD에서 인증 정보를 수집하지 않는 경우 [Azure Monitor](../../azure-monitor/overview.md)를 사용하여 지역 간 액세스 패턴을 식별하는 것이 좋습니다.

## <a name="access-usage"></a>액세스 사용

### <a name="azure-ad-logs-archived-and-integrated-with-incident-response-plans"></a>인시던트 대응 플랜에 보관 및 통합된 Azure AD 로그

Azure AD에 대한 로그인 작업, 감사 및 위험 이벤트에 액세스하는 것은 문제 해결, 사용량 현황 분석 및 법정 분석 조사에 중요합니다. Azure AD는 보존 기간이 제한된 REST API를 통해 해당 원본에 대한 액세스 권한을 제공합니다. SIEM(보안 정보 및 이벤트 관리) 시스템 또는 상응하는 보관 기술은 감사 및 지원 가능성을 위한 장기 스토리지의 핵심입니다. Azure AD 로그의 장기 스토리지를 사용하려면 기존 SIEM 솔루션에 추가하거나 [Azure Monitor](../reports-monitoring/concept-activity-logs-azure-monitor.md)를 사용해야 합니다. 인시던트 대응 플랜 및 조사의 일부로 사용할 수 있는 보관 로그입니다.

#### <a name="logs-recommended-reading"></a>로그 권장 읽기

- [Azure Active Directory 감사 API 참조](/graph/api/resources/directoryaudit)
- [Azure Active Directory 로그인 활동 보고서 API 참조](/graph/api/resources/signin)
- [인증서와 함께 Azure AD Reporting API를 사용하여 데이터 가져오기](../reports-monitoring/tutorial-access-api-with-certificates.md)
- [Azure Active Directory Identity Protection를 위한 Microsoft Graph](../identity-protection/howto-identity-protection-graph-api.md)
- [Office 365 Management Activity API 참조](/office/office-365-management-api/office-365-management-activity-api-reference)
- [Azure Active Directory Power BI 콘텐츠 팩 사용 방법](../reports-monitoring/howto-use-azure-monitor-workbooks.md)

## <a name="summary"></a>요약

보안 ID 인프라에는 12가지 양상이 있습니다. 이 목록을 사용하면 자격 증명을 더욱 안전하게 보호하고 관리하며, 인증 경험을 정의하고, 할당을 위임하며, 사용량을 측정하고, 엔터프라이즈 보안 태세에 따라 액세스 정책을 정의할 수 있습니다.

- 주요 작업에 소유자를 할당합니다.
- 취약하거나 유출된 암호를 탐지하고, 암호 관리 및 보호를 개선하며, 리소스에 대한 사용자 액세스를 더욱 안전하게 보호하는 솔루션을 구현합니다.
- 디바이스의 ID를 관리하여 언제 어디서든 리소스를 보호합니다.
- 암호 없는 인증을 구현합니다.
- 조직 전체에서 표준화된 Single Sign-On 메커니즘을 제공합니다.
- AD FS에서 Azure AD로 앱을 마이그레이션하여 보안을 강화하고 더 일관된 관리 효율성을 제공합니다.
- 그룹을 사용하여 애플리케이션에 사용자를 할당하면 유연성이 향상되고 대규모 관리 기능을 사용할 수 있습니다.
- 위험 기반 액세스 정책을 구성합니다.
- 레거시 인증 프로토콜을 잠급니다.
- 불법 동의 권한 부여를 탐지하고 수정합니다.
- 사용자 및 그룹 설정을 잠급니다.
- 문제 해결, 사용량 현황 분석 및 법정 분석 조사를 위해 Azure AD 로그의 장기 스토리지를 사용하도록 설정합니다.

## <a name="next-steps"></a>다음 단계

[ID 거버넌스 운영 검사 및 작업](active-directory-ops-guide-govern.md)을 시작합니다.