---
title: Azure Active Directory를 사용하여 ID 보안에 신속하게 대응
description: Azure AD 클라우드 기반 ID에서의 위협에 신속하게 대응
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 04/27/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: davidspo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 52c82b01b9c6bc45779e9bed04bb04e8ac3fcab7
ms.sourcegitcommit: 10029520c69258ad4be29146ffc139ae62ccddc7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/27/2021
ms.locfileid: "129083660"
---
# <a name="rapidly-respond-to-secure-identities-with-azure-ad"></a>Azure AD를 사용하여 ID 보안에 신속하게 대응

특히 신속하게 대응하고 많은 서비스에 액세스를 신속하게 제공해야 하는 경우, 오늘날 전 세계에서 작업자를 보호하는 것이 어려울 수 있습니다. 이 문서에서는 모든 작업에 대한 간략한 목록을 제공합니다. 이를 통해 사용자가 소유하는 라이선스 유형에 따라 Azure AD 기능을 배포하는 순서를 식별하고 우선 순위를 지정할 수 있습니다. Azure AD는 다양한 기능을 제공하고 ID에 다양한 보안 레이어를 제공 하며, 관련된 기능을 탐색하는 경우도 있습니다. 많은 조직이 이미 클라우드에 있거나 빠르게 클라우드로 이동하고 있습니다. 이 문서는 서비스를 신속하게 배포하여 기본 고려 사항으로 ID를 보호할 수 있도록 하기 위한 것입니다. 

각 표에서는 사용자 영향을 최소화하고 사용자 환경을 개선하는 동시에 주 보안 공격(위반 재생, 피싱 및 암호 스프레이)에서 관리자 및 사용자 ID를 모두 보호하는 일관된 보안 권장 사항을 제공합니다. 

또한 이 지침을 통해 관리자는 안전하고 보호된 방식으로 SaaS 및 온-프레미스 애플리케이션에 대한 액세스를 구성할 수 있고, 클라우드 또는 하이브리드(동기화된) ID에 적용되며, 원격으로 또는 사무실에서 작업하는 사용자에게 적용됩니다.

이 검사 목록은 다음 사항의 수행 방법 설명을 통해 중요한 권장 작업을 빠르게 배포하여 조직을 즉시 보호하는 데 도움이 됩니다.

- 자격 증명을 강화합니다.
- 공격 노출 영역을 줄입니다.
- 위협 응답을 자동화합니다.
- 클라우드 인텔리전스를 활용합니다.
- 최종 사용자 셀프 서비스를 사용하도록 설정합니다.

## <a name="prerequisites"></a>사전 요구 사항

이 가이드에서는 Azure AD에서 클라우드 전용 또는 하이브리드 ID가 이미 설정되어 있다고 가정합니다. ID 유형 선택에 대한 도움말은 [Azure Active Directory 하이브리드 ID 솔루션에 적합한 인증 방법 선택](../hybrid/choose-ad-authn.md) 문서를 참조하세요. 

## <a name="summary"></a>요약

보안 ID 인프라에는 많은 측면이 있지만, 이 검사 목록은 사용자가 원격으로 작업할 수 있도록 하는 안전하고 보호되는 ID 인프라에 집중합니다. ID를 보호하는 것은 보안 절차의 단지 일부이며, 데이터, 애플리케이션 및 디바이스를 보호하는 것도 고려해야 합니다.

### <a name="guidance-for-azure-ad-free-office-365-or-microsoft-365-customers"></a>Azure AD Free, Office 365 또는 Microsoft 365 고객에 대한 지침.

Azure AD Free, Office 365 또는 Microsoft 365 앱 고객이 사용자 ID를 보호하기 위해 수행해야 하는 여러 권장 사항이 있습니다. 다음 표에서는 다음 라이선스 구독에 대한 주요 작업을 강조 표시합니다.

- Office 365 (Office 365 E1, E3, E5, F1, A1, A3, A5)
- Microsoft 365(비즈니스 기본, 비즈니스용 앱, 비즈니스 표준, 비즈니스 프리미엄, A1)
- Azure AD Free (Azure, Dynamics 365, Intune 및 Power Platform 포함)

| 권장 조치 | 세부 정보 |
| --- | --- |
| [보안 기본값 사용](concept-fundamentals-security-defaults.md) | MFA를 사용하도록 설정하고 레거시 인증을 차단하여 모든 사용자 ID 및 애플리케이션 보호 |
| [암호 해시 동기화 사용](../hybrid/how-to-connect-password-hash-synchronization.md)(하이브리드 ID를 사용하는 경우) | 인증에 중복성을 제공하고, 보안(스마트 잠금, IP 잠금 및 유출된 자격 증명을 검색하는 기능 포함)을 개선합니다. |
| [ADFS 스마트 잠금 사용](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection)(해당하는 경우) | 악의적인 활동으로부터 엑스트라넷 계정 잠금이 발생하지 않도록 사용자를 보호합니다. |
| [Azure Active Directory 스마트 잠금 사용](../authentication/howto-password-smart-lockout.md)(관리 ID를 사용하는 경우) | 스마트 잠금은 사용자의 암호를 추측하려거나 무차별 암호 대입 공격을 사용하여 침입하려는 불량 작업자를 차단하도록 도와줍니다. |
| [애플리케이션에 대한 최종 사용자 동의 사용 안함](../manage-apps/configure-user-consent.md) | 관리자 동의 워크플로는 관리자에게 관리자 승인이 필요한 애플리케이션에 대한 액세스 권한을 부여하는 안전한 방법을 제공합니다. 그래서 최종 사용자가 회사 데이터를 노출하지 않도록 할 수 있습니다. 노출 영역을 줄이고 위험을 완화하는 데 도움이 되도록 향후 모든 사용자 동의 작업을 사용하지 않도록 설정는 것이 좋습니다. |
| [갤러리에서 지원되는 SaaS 애플리케이션을 Azure AD에 통합하고, Single Sign On을 사용하도록 설정](../manage-apps/add-application-portal.md) | Azure AD에는 수천 개의 사전 통합 애플리케이션이 들어 있는 갤러리가 있습니다. 조직에서 사용하는 애플리케이션 중 일부는 Azure Portal에서 직접 액세스할 수 있는 갤러리에 있을 것입니다. 향상된 사용자 환경(SSO)을 사용하여 원격으로 그리고 안전하게 회사 SaaS 애플리케이션에 대한 액세스 제공 |
| [SaaS 애플리케이션에서 자동화된 사용자 프로비저닝 및 프로비저닝 해제](../app-provisioning/user-provisioning.md)(해당하는 경우) | 자동으로 사용자가 액세스해야 하는 클라우드(SaaS) 애플리케이션에서 사용자 ID와 역할을 만듭니다. 자동 프로비저닝에는 사용자 ID를 생성하는 것 외에도 상태 또는 역할이 변경될 때 사용자 ID의 유지 관리 및 제거가 포함되어, 조직의 보안을 강화합니다. |
| [보안 하이브리드 액세스 사용: 기존 앱 배달 컨트롤러 및 네트워크를 사용하여 레거시 앱 보호](../manage-apps/secure-hybrid-access.md)(해당하는 경우) | 기존 애플리케이션 배달 컨트롤러나 네트워크를 사용하여 온-프레미스 및 클라우드 레거시 인증 애플리케이션을 Azure AD에 연결하여 게시 및 보호할 수 있습니다. |
| [셀프 서비스 암호 재설정 사용](../authentication/tutorial-enable-sspr.md)(클라우드 전용 계정에 적용) | 사용자가 디바이스 또는 애플리케이션에 로그인할 수 없는 경우 이 기능을 통해 지원 센터 호출 및 생산성 저하를 줄일 수 있습니다. |
| [가능한 경우 비전역 관리 역할 사용](../roles/permissions-reference.md) | 관리자에게 액세스해야 하는 영역에 대해 필요한 액세스 권한만 제공합니다. 모든 관리자가 전역 관리자일 필요는 없습니다. |
| [Microsoft의 암호 지침 사용](https://www.microsoft.com/research/publication/password-guidance/) | 사용자에게 설정된 일정에 따라 자신의 암호를 변경하도록 더 이상 요구하지 않고, 복잡성 요구를 사용하지 않도록 설정합니다. 그러면 사용자는 암호를 기억하고 안전하게 유지하려고 노력합니다. |


### <a name="guidance-for-azure-ad-premium-plan-1-customers"></a>Azure AD Premium 플랜 1 고객에 대한 지침입니다.

다음 표에서는 다음 라이선스 구독에 대한 주요 작업을 강조 표시합니다.

- Azure Active Directory Premium P1 (Azure AD P1)
- EMS(Enterprise Mobility + Security) E3
- Microsoft 365 (M365 E3, A3, F1, F3)

| 권장 조치 | 세부 정보 |
| --- | --- |
| [사용자 등록 환경을 간소화하기 위해 Azure AD MFA 및 SSPR에 대해 결합된 등록 환경 사용](../authentication/howto-registration-mfa-sspr-combined.md) | 사용자가 Azure AD Multi-Factor Authentication 및 셀프 서비스 암호 재설정을 위해 하나의 공통된 환경에서 등록할 수 있도록 합니다. |
| [조직에 대한 MFA 설정 구성](../authentication/howto-mfa-getstarted.md) | 다단계 인증을 사용하여 계정이 손상되지 않도록 보호합니다. |
| [셀프 서비스 암호 재설정 사용](../authentication/tutorial-enable-sspr.md) | 사용자가 디바이스 또는 애플리케이션에 로그인할 수 없는 경우, 이 기능을 통해 지원 센터 호출 및 생산성 저하를 줄일 수 있습니다. |
| [비밀번호 쓰기 저장 구현](../authentication/tutorial-enable-sspr-writeback.md)(하이브리드 ID를 사용하는 경우) | 클라우드에서 암호 변경 내용을 온-프레미스 Windows Server Active Directory 환경에 다시 쓸 수 있도록 합니다. |
| 조건부 액세스 정책 만들기 및 사용 | [관리 권한이 할당된 계정을 보호하는 관리자용 MFA](../conditional-access/howto-conditional-access-policy-admin-mfa.md) <br><br> [레거시 인증 프로토콜과 관련된 위험이 증가하여 레거시 인증 프로토콜을 차단합니다.](../conditional-access/howto-conditional-access-policy-block-legacy.md) <br><br> [모든 사용자 및 애플리케이션이 환경에 맞는 균형 잡힌 MFA 정책을 만들 수 있도록 하는 MFA로서, 사용자 및 애플리케이션의 보안을 유지합니다.](../conditional-access/howto-conditional-access-policy-all-users-mfa.md) <br><br> [Azure 리소스에 액세스하는 모든 사용자에 대해 다단계 인증을 요구하여 권한 있는 리소스를 보호하기 위해 Azure 관리용 MFA 필요가 필요합니다.](../conditional-access/howto-conditional-access-policy-azure-management.md) |
| [암호 해시 동기화 사용](../hybrid/how-to-connect-password-hash-synchronization.md)(하이브리드 ID를 사용하는 경우) | 인증에 중복성을 제공하고, 보안(스마트 잠금, IP 잠금 및 유출된 자격 증명을 검색하는 기능 포함)을 개선합니다. |
| [ADFS 스마트 잠금 사용](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection)(해당하는 경우) | 악의적인 활동으로부터 엑스트라넷 계정 잠금이 발생하지 않도록 사용자를 보호합니다. |
| [Azure Active Directory 스마트 잠금 사용](../authentication/howto-password-smart-lockout.md)(관리 ID를 사용하는 경우) | 스마트 잠금은 사용자의 암호를 추측하려거나 무차별 암호 대입 공격을 사용하여 침입하려는 불량 작업자를 차단하도록 도와줍니다. |
| [애플리케이션에 대한 최종 사용자 동의 사용 안함](../manage-apps/configure-user-consent.md) | 관리자 동의 워크플로는 관리자에게 관리자 승인이 필요한 애플리케이션에 대한 액세스 권한을 부여하는 안전한 방법을 제공합니다. 그래서 최종 사용자가 회사 데이터를 노출하지 않도록 할 수 있습니다. 노출 영역을 줄이고 위험을 완화하는 데 도움이 되도록 향후 모든 사용자 동의 작업을 사용하지 않도록 설정는 것이 좋습니다. |
| [애플리케이션 프록시가 있는 온-프레미스 레거시 애플리케이션에 대한 원격 액세스 사용](../app-proxy/application-proxy-add-on-premises-application.md) | 사용자가 Azure AD 계정으로 로그인하여 온-프레미스 애플리케이션에 안전하게 액세스할 수 있도록 Azure AD 애플리케이션 프록시 사용하도록 설정하고 레거시 앱과 통합합니다. |
| [보안 하이브리드 액세스 사용: 기존 앱 배달 컨트롤러 및 네트워크를 사용하여 레거시 앱을 보호합니다](../manage-apps/secure-hybrid-access.md)(해당하는 경우). | 기존 애플리케이션 배달 컨트롤러나 네트워크를 사용하여 온-프레미스 및 클라우드 레거시 인증 애플리케이션을 Azure AD에 연결하여 게시 및 보호할 수 있습니다. |
| [갤러리에서 지원되는 SaaS 애플리케이션을 Azure AD에 통합하고, Single Sign On을 사용하도록 설정](../manage-apps/add-application-portal.md) | Azure AD에는 수천 개의 사전 통합 애플리케이션이 들어 있는 갤러리가 있습니다. 조직에서 사용하는 애플리케이션 중 일부는 Azure Portal에서 직접 액세스할 수 있는 갤러리에 있을 것입니다. 향상된 사용자 환경(SSO)을 사용하여 원격으로 그리고 안전하게 회사 SaaS 애플리케이션에 대한 액세스를 제공합니다. |
| [SaaS 애플리케이션에서 자동화된 사용자 프로비저닝 및 프로비저닝 해제](../app-provisioning/user-provisioning.md)(해당하는 경우) | 자동으로 사용자가 액세스해야 하는 클라우드(SaaS) 애플리케이션에서 사용자 ID와 역할을 만듭니다. 자동 프로비저닝에는 사용자 ID를 생성하는 것 외에도 상태 또는 역할이 변경될 때 사용자 ID의 유지 관리 및 제거가 포함되어, 조직의 보안을 강화합니다. |
| [조건부 액세스(디바이스 기반) 사용](../conditional-access/require-managed-devices.md) | 디바이스 기반 조건부 액세스를 통해 보안 및 사용자 환경을 개선합니다. 이 단계를 통해 사용자는 보안 및 규정 준수에 대한 귀하의 표준을 충족하는 디바이스에서만 액세스할 수 있습니다. 이러한 디바이스는 관리 디바이스라고도 합니다. 관리 디바이스에는 Intune 규격 또는 하이브리드 Azure AD 조인 디바이스가 포함될 수 있습니다. |
| [암호 보호 사용](../authentication/howto-password-ban-bad-on-premises-deploy.md) | 사용자가 허술하고 추측하기 쉬운 암호를 사용하지 못하도록 보호합니다. |
| [둘 이상의 전역 관리자 지정](../roles/security-emergency-access.md) | 비상 시 사용하기 위해 둘 이상의 클라우드 전용 영구 전역 관리자 계정을 할당합니다. 이러한 계정은 매일 사용되지는 않으며 길고 복잡한 암호가 있어야 합니다. Break Glass 계정은 긴급 상황에서 서비스에 액세스할 수 있도록 보장합니다. |
| [가능한 경우 비전역 관리 역할 사용](../roles/permissions-reference.md) | 관리자에게 액세스해야 하는 영역에 대해 필요한 액세스 권한만 제공합니다. 모든 관리자가 전역 관리자일 필요는 없습니다. |
| [Microsoft의 암호 지침 사용](https://www.microsoft.com/research/publication/password-guidance/) | 사용자에게 설정된 일정에 따라 자신의 암호를 변경하도록 더 이상 요구하지 않고, 복잡성 요구를 사용하지 않도록 설정합니다. 그러면 사용자는 암호를 기억하고 안전하게 유지하려고 노력합니다. |
| [게스트 사용자 액세스에 대한 계획 만들기](../external-identities/what-is-b2b.md) | 게스트 사용자가 자신의 회사, 학교 또는 소셜 ID로 앱 및 서비스에 로그인할 수 있도록 하여 공동으로 작업합니다. |

### <a name="guidance-for-azure-ad-premium-plan-2-customers"></a>Azure AD Premium 플랜 2 고객에 대한 지침입니다.

다음 표에서는 다음 라이선스 구독에 대한 주요 작업을 강조 표시합니다.

- Azure Active Directory Premium P2 (Azure AD P2)
- EMS (Enterprise Mobility + Security) E5
- Microsoft 365 (M365 E5, A5)

| 권장 조치 | 세부 정보 |
| --- | --- |
| [사용자 등록 환경을 간소화하기 위해 Azure AD MFA 및 SSPR에 대해 결합된 등록 환경 사용](../authentication/howto-registration-mfa-sspr-combined.md) | 사용자가 Azure AD Multi-Factor Authentication 및 셀프 서비스 암호 재설정을 위해 하나의 공통된 환경에서 등록할 수 있도록 합니다. |
| [조직에 대한 MFA 설정 구성](../authentication/howto-mfa-getstarted.md) | 다단계 인증을 사용하여 계정이 손상되지 않도록 보호합니다. |
| [셀프 서비스 암호 재설정 사용](../authentication/tutorial-enable-sspr.md) | 사용자가 디바이스 또는 애플리케이션에 로그인할 수 없는 경우, 이 기능을 통해 지원 센터 호출 및 생산성 저하를 줄일 수 있습니다. |
| [비밀번호 쓰기 저장 구현](../authentication/tutorial-enable-sspr-writeback.md)(하이브리드 ID를 사용하는 경우) | 클라우드에서 암호 변경 내용을 온-프레미스 Windows Server Active Directory 환경에 다시 쓸 수 있도록 합니다. |
| [ID 보호 정책을 사용하도록 설정하여 MFA 등록 적용](../identity-protection/howto-identity-protection-configure-mfa-policy.md) | Azure AD MFA(다단계 인증)의 롤아웃을 관리합니다. |
| [ID 보호 사용자 및 로그인 위험 정책 사용](../identity-protection/howto-identity-protection-configure-risk-policies.md) | ID 보호 사용자 및 로그인 위험 정책을 사용하도록 설정합니다. 권장되는 로그인 정책은 중간 위험 로그인을 대상으로 하고 MFA를 요구하는 것입니다. 사용자 정책의 경우, 암호 변경 작업을 필요로 하는 고위험 사용자를 대상으로 해야 합니다. |
| 조건부 액세스 정책 만들기 및 사용 | [관리 권한이 할당된 계정을 보호하는 관리자용 MFA](../conditional-access/howto-conditional-access-policy-admin-mfa.md) <br><br> [레거시 인증 프로토콜과 관련된 위험이 증가하여 레거시 인증 프로토콜을 차단합니다.](../conditional-access/howto-conditional-access-policy-block-legacy.md) <br><br> [Azure 리소스에 액세스하는 모든 사용자에 대해 다단계 인증을 요구하여 권한 있는 리소스를 보호하기 위해 Azure 관리용 MFA 필요가 필요합니다.](../conditional-access/howto-conditional-access-policy-azure-management.md) |
| [암호 해시 동기화 사용](../hybrid/how-to-connect-password-hash-synchronization.md)(하이브리드 ID를 사용하는 경우) | 인증에 중복성을 제공하고, 보안(스마트 잠금, IP 잠금 및 유출된 자격 증명을 검색하는 기능 포함)을 개선합니다. |
| [ADFS 스마트 잠금 사용](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection)(해당하는 경우) | 악의적인 활동으로부터 엑스트라넷 계정 잠금이 발생하지 않도록 사용자를 보호합니다. |
| [Azure Active Directory 스마트 잠금 사용](../authentication/howto-password-smart-lockout.md)(관리 ID를 사용하는 경우) | 스마트 잠금은 사용자의 암호를 추측하려거나 무차별 암호 대입 공격을 사용하여 침입하려는 불량 작업자를 차단하도록 도와줍니다. |
| [애플리케이션에 대한 최종 사용자 동의 사용 안함](../manage-apps/configure-user-consent.md) | 관리자 동의 워크플로는 관리자에게 관리자 승인이 필요한 애플리케이션에 대한 액세스 권한을 부여하는 안전한 방법을 제공합니다. 그래서 최종 사용자가 회사 데이터를 노출하지 않도록 할 수 있습니다. 노출 영역을 줄이고 위험을 완화하는 데 도움이 되도록 향후 모든 사용자 동의 작업을 사용하지 않도록 설정는 것이 좋습니다. |
| [애플리케이션 프록시가 있는 온-프레미스 레거시 애플리케이션에 대한 원격 액세스 사용](../app-proxy/application-proxy-add-on-premises-application.md) | 사용자가 Azure AD 계정으로 로그인하여 온-프레미스 애플리케이션에 안전하게 액세스할 수 있도록 Azure AD 애플리케이션 프록시 사용하도록 설정하고 레거시 앱과 통합합니다. |
| [보안 하이브리드 액세스 사용: 기존 앱 배달 컨트롤러 및 네트워크를 사용하여 레거시 앱을 보호합니다](../manage-apps/secure-hybrid-access.md)(해당하는 경우). | 기존 애플리케이션 배달 컨트롤러나 네트워크를 사용하여 온-프레미스 및 클라우드 레거시 인증 애플리케이션을 Azure AD에 연결하여 게시 및 보호할 수 있습니다. |
| [갤러리에서 지원되는 SaaS 애플리케이션을 Azure AD에 통합하고, Single Sign On을 사용하도록 설정](../manage-apps/add-application-portal.md) | Azure AD에는 수천 개의 사전 통합 애플리케이션이 들어 있는 갤러리가 있습니다. 조직에서 사용하는 애플리케이션 중 일부는 Azure Portal에서 직접 액세스할 수 있는 갤러리에 있을 것입니다. 향상된 사용자 환경(SSO)을 사용하여 원격으로 그리고 안전하게 회사 SaaS 애플리케이션에 대한 액세스를 제공합니다. |
| [SaaS 애플리케이션에서 자동화된 사용자 프로비저닝 및 프로비저닝 해제](../app-provisioning/user-provisioning.md)(해당하는 경우) | 자동으로 사용자가 액세스해야 하는 클라우드(SaaS) 애플리케이션에서 사용자 ID와 역할을 만듭니다. 자동 프로비저닝에는 사용자 ID를 생성하는 것 외에도 상태 또는 역할이 변경될 때 사용자 ID의 유지 관리 및 제거가 포함되어, 조직의 보안을 강화합니다. |
| [조건부 액세스(디바이스 기반) 사용](../conditional-access/require-managed-devices.md) | 디바이스 기반 조건부 액세스를 통해 보안 및 사용자 환경을 개선합니다. 이 단계를 통해 사용자는 보안 및 규정 준수에 대한 귀하의 표준을 충족하는 디바이스에서만 액세스할 수 있습니다. 이러한 디바이스는 관리 디바이스라고도 합니다. 관리 디바이스에는 Intune 규격 또는 하이브리드 Azure AD 조인 디바이스가 포함될 수 있습니다. |
| [암호 보호 사용](../authentication/howto-password-ban-bad-on-premises-deploy.md) | 사용자가 허술하고 추측하기 쉬운 암호를 사용하지 못하도록 보호합니다. |
| [둘 이상의 전역 관리자 지정](../roles/security-emergency-access.md) | 비상 시 사용하기 위해 둘 이상의 클라우드 전용 영구 전역 관리자 계정을 할당합니다. 이러한 계정은 매일 사용되지는 않으며 길고 복잡한 암호가 있어야 합니다. Break Glass 계정은 긴급 상황에서 서비스에 액세스할 수 있도록 보장합니다. |
| [가능한 경우 비전역 관리 역할 사용](../roles/permissions-reference.md) | 관리자에게 액세스해야 하는 영역에 대해 필요한 액세스 권한만 제공합니다. 모든 관리자가 전역 관리자일 필요는 없습니다. |
| [Microsoft의 암호 지침 사용](https://www.microsoft.com/research/publication/password-guidance/) | 사용자에게 설정된 일정에 따라 자신의 암호를 변경하도록 더 이상 요구하지 않고, 복잡성 요구를 사용하지 않도록 설정합니다. 그러면 사용자는 암호를 기억하고 안전하게 유지하려고 노력합니다. |
| [게스트 사용자 액세스에 대한 계획 만들기](../external-identities/what-is-b2b.md) | 게스트 사용자가 자신의 회사, 학교 또는 소셜 ID로 앱 및 서비스에 로그인할 수 있도록 하여 공동으로 작업합니다. |
| [Privileged Identity Management 사용](../privileged-identity-management/pim-configure.md) | 조직의 중요한 리소스에 대한 액세스를 관리, 제어 및 모니터링하여 관리자가 필요한 경우에만 액세스하고 승인을 받도록 할 수 있습니다. |

## <a name="next-steps"></a>다음 단계

- Azure AD의 개별 기능에 대한 자세한 배포 지침은 [Azure AD 프로젝트 배포 계획](active-directory-deployment-plans.md)을 검토하세요.

- 엔드투엔드 Azure AD 배포 검사 목록은 [Azure Active Directory 기능 배포 가이드](active-directory-deployment-checklist-p2.md) 문서를 참조하세요.