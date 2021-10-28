---
title: Azure AD 배포 검사 목록
description: Azure Active Directory 기능 배포 검사 목록
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/29/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: martinco
ms.collection: M365-identity-device-management
ms.openlocfilehash: e2411ef1ba4878e43c7f6432b6aa3bd032f2842a
ms.sourcegitcommit: 10029520c69258ad4be29146ffc139ae62ccddc7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/27/2021
ms.locfileid: "129083717"
---
# <a name="azure-active-directory-feature-deployment-guide"></a>Azure Active Directory 기능 배포 가이드

Azure AD(Azure Active Directory)를 조직에 배포하고 보안을 유지하는 것은 어려울 수 있습니다. 이 문서에서는 보안 태세를 향상시키기 30일, 60일, 90일 또는 그 이상의 과정을 단계별로 완료하는 데 고객에게 도움이 되는 일반적인 작업을 확인합니다. Azure AD를 이미 배포한 조직이더라도 이 가이드를 사용하여 투자를 최대한 활용할 수 있습니다.

ID 인프라를 잘 계획하고 실행해 두면 알려진 사용자와 디바이스를 통해서만 생산성 워크로드와 데이터에 안전하게 액세스할 수 있습니다.

또한 고객은 해당 [ID 보안 점수](identity-secure-score.md)를 확인하여 Microsoft 모범 사례에 얼마나 잘 맞는지 알 수 있습니다. 이러한 권장 사항을 구현하기 전과 후에 보안 점수를 확인하여 업계의 다른 기업 및 같은 규모의 다른 조직과 비교할 때 얼마나 잘 하고 있는지 파악합니다.

## <a name="prerequisites"></a>사전 요구 사항

이 가이드의 많은 권장 사항을 Azure AD Free 또는 라이선스 없이 구현할 수 있습니다. 필요한 라이선스는 작업 완료를 위해 최소한으로 필요한 라이선스를 나타냅니다.

라이선스에 대한 자세한 내용은 다음 페이지에서 확인할 수 있습니다.

* [Azure AD 라이선스](https://www.microsoft.com/security/business/identity-access-management/azure-ad-pricing)
* [Microsoft 365 Enterprise](https://www.microsoft.com/en-us/licensing/product-licensing/microsoft-365-enterprise)
* [Enterprise Mobility + Security](https://www.microsoft.com/en-us/licensing/product-licensing/enterprise-mobility-security)
* [Azure AD External Identities 가격 책정](../external-identities/external-identities-pricing.md)

## <a name="phase-1-build-a-foundation-of-security"></a>1단계: 보안 기반 구축

이 단계에서 관리자는 일반 사용자 계정을 가져오거나 만들기 전에 Azure AD에서 보다 안전하고 사용하기 쉬운 기반을 만들기 위한 기본 보안 기능을 사용하도록 설정합니다. 이와 같은 기반 단계는 처음부터 보다 안전한 상태에서 작업할 수 있도록 하고 최종 사용자가 새 개념들을 한 번만 받아들이면 되도록 해줍니다.

| Task | 세부 정보 | 필수 라이선스 |
| ---- | ------ | ---------------- |
| [둘 이상의 전역 관리자 지정](../roles/security-emergency-access.md) | 비상 시 사용하기 위해 둘 이상의 클라우드 전용 영구 전역 관리자 계정을 할당합니다. 이러한 계정은 매일 사용되지는 않으며 길고 복잡한 암호가 있어야 합니다. | Azure AD Free |
| [가능한 경우 비전역 관리 역할 사용](../roles/permissions-reference.md) | 관리자에게 액세스해야 하는 영역에 대해 필요한 액세스 권한만 제공합니다. 모든 관리자가 전역 관리자일 필요는 없습니다. | Azure AD Free |
| [관리자 역할 사용을 추적하기 위해 Privileged Identity Management 사용](../privileged-identity-management/pim-getting-started.md) | 관리자 역할 사용을 추적하려면 Privileged Identity Management를 사용하도록 설정합니다. | Azure AD Premium P2 |
| [셀프 서비스 암호 재설정 롤아웃](../authentication/howto-sspr-deployment.md) | 직원들이 정책에 따라 자신의 암호를 재설정할 수 있도록 하여 암호 재설정과 관련된 기술 지원팀 문의 전화 횟수를 줄입니다. | |
| [조직별 사용자 지정 금지된 암호 목록 만들기](../authentication/tutorial-configure-custom-password-protection.md) | 사용자가 조직이나 영역에서 사용하는 일반적인 단어 또는 구를 포함하여 암호를 만들지 못하도록 합니다. | |
| [Azure AD 암호 보호에 대한 온-프레미스 통합 사용](../authentication/concept-password-ban-bad-on-premises.md) | 금지된 암호 목록을 온-프레미스 디렉터리로 확장하여 온-프레미스에서 설정한 암호가 전역 및 테넌트별 금지된 암호 목록도 준수하도록 합니다. | Azure AD Premium P1 |
| [Microsoft의 암호 지침 사용](https://www.microsoft.com/research/publication/password-guidance/) | 사용자에게 설정된 일정에 따라 자신의 암호를 변경하도록 더 이상 요구하지 않고, 복잡성 요구를 사용하지 않도록 설정합니다. 그러면 사용자는 암호를 기억하고 안전하게 유지하려고 노력합니다. | Azure AD Free |
| [클라우드 기반 사용자 계정에 대한 정기적인 암호 재설정 사용 안 함](../authentication/concept-sspr-policy.md#set-a-password-to-never-expire) | 정기적인 암호 재설정은 사용자가 기존 암호를 강화하도록 촉구합니다. Microsoft의 암호 지침 문서에 포함된 지침을 사용하고 클라우드 전용 사용자에게도 온-프레미스 정책을 적용합니다. | Azure AD Free |
| [Azure Active Directory 스마트 잠금 사용자 지정](../authentication/howto-password-smart-lockout.md) | 클라우드 기반 사용자의 잠금이 온-프레미스 Active Directory 사용자에게 복제되지 않도록 방지 | |
| [AD FS에 대해 엑스트라넷 스마트 잠금 사용](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection) | AD FS 엑스트라넷 잠금은 유효한 AD FS 사용자가 해당 계정을 계속 사용할 수 있도록 하면서 무차별 암호 추측 공격으로부터 보호합니다. | |
| [조건부 액세스를 사용하여 Azure AD에 대한 레거시 인증 차단](../conditional-access/block-legacy-authentication.md) | Multi-Factor Authentication을 적용할 수 없는 POP, SMTP, IMAP 및 MAPI와 같이 악의적 사용자가 선호하는 진입점이 되는 레거시 인증 프로토콜을 차단합니다. | Azure AD Premium P1 |
| [조건부 액세스 정책을 사용하여 Azure AD Multi-Factor Authentication 배포](../authentication/howto-mfa-getstarted.md) | 사용자가 조건부 액세스 정책을 사용하여 중요한 애플리케이션에 액세스할 때 2단계 인증을 수행하도록 합니다. | Azure AD Premium P1 |
| [Azure Active Directory Identity Protection 사용](../identity-protection/overview-identity-protection.md) | 조직의 사용자를 위해 위험한 로그인 및 손상된 자격 증명을 추적할 수 있도록 설정합니다. | Azure AD Premium P2 |
| [위험 탐지를 사용하여 Multi-Factor Authentication 및 암호 변경 트리거](../authentication/tutorial-risk-based-sspr-mfa.md) | Multi-Factor Authentication, 암호 재설정 및 위험 기반 로그인 차단 같은 이벤트를 트리거할 수 있는 자동화를 사용하도록 설정합니다. | Azure AD Premium P2 |
| [결합된 셀프 서비스 암호 재설정 및 Azure AD Multi-Factor Authentication 등록 사용](../authentication/concept-registration-mfa-sspr-combined.md) | 사용자가 Azure AD Multi-Factor Authentication 및 셀프 서비스 암호 재설정을 위해 하나의 공통된 환경에서 등록할 수 있도록 합니다. | Azure AD Premium P1 |

## <a name="phase-2-import-users-enable-synchronization-and-manage-devices"></a>2단계: 사용자 가져오기, 동기화 사용 및 디바이스 관리

다음으로, 사용자를 가져오고 동기화를 사용하고, 게스트 액세스를 계획하고, 추가 기능을 지원하도록 준비하여 1단계에서 설정한 기반을 강화합니다.

| Task | 세부 정보 | 필수 라이선스 |
| ---- | ------ | ---------------- |
| [Azure AD Connect 설치](../hybrid/how-to-connect-install-select-installation.md) | 기존 온-프레미스 디렉터리에서 클라우드로 사용자를 동기화하기 위해 준비합니다. | Azure AD Free |
| [암호 해시 동기화 구현](../hybrid/how-to-connect-password-hash-synchronization.md) | 암호 해시를 동기화하여 암호 변경 내용 복제, 잘못된 암호 검색 및 수정, 누출된 자격 증명 보고를 허용합니다. | Azure AD Free |
| [비밀번호 쓰기 저장 구현](../authentication/tutorial-enable-sspr-writeback.md) | 클라우드에서 암호 변경 내용을 온-프레미스 Windows Server Active Directory 환경에 다시 쓸 수 있도록 합니다. | Azure AD Premium P1 |
| [Azure AD Connect Health 구현](../hybrid/whatis-azure-ad-connect.md#what-is-azure-ad-connect-health) | Azure AD Connect 서버, AD FS 서버 및 도메인 컨트롤러의 주요 상태 통계를 모니터링할 수 있도록 합니다. | Azure AD Premium P1 |
| [Azure Active Directory에서 그룹 멤버 자격별로 사용자에게 라이선스 할당](../enterprise-users/licensing-groups-assign.md) | 사용자 기준 설정 대신, 그룹별로 기능을 사용하거나 사용하지 않도록 설정하는 라이선스 그룹을 만들어 시간과 노력을 절약합니다. | |
| [게스트 사용자 액세스에 대한 계획 만들기](../external-identities/what-is-b2b.md) | 게스트 사용자가 자신의 회사, 학교 또는 소셜 ID로 앱 및 서비스에 로그인할 수 있도록 하여 공동으로 작업합니다. | [Azure AD External Identities 가격 책정](../external-identities/external-identities-pricing.md) |
| [디바이스 관리 전략 결정](../devices/overview.md) | 디바이스에 대해 조직이 허용하는 작업을 결정합니다. 등록 및 참가, BYOD(Bring Your Own Device) 및 회사 제공 | |
| [조직의 비즈니스용 Windows Hello 배포](/windows/security/identity-protection/hello-for-business/hello-manage-in-organization) | Windows Hello를 사용하여 암호 없는 인증 준비 | |
| [사용자를 위한 암호 없는 인증 방법 배포](../authentication/concept-authentication-passwordless.md) | 사용자에게 편리한 암호 없는 인증 방법 제공 | Azure AD Premium P1 |

## <a name="phase-3-manage-applications"></a>3단계: 애플리케이션 관리

이전 단계에서 수행한 작업에 이어, 마이그레이션하고 Azure AD와 통합할 후보 애플리케이션을 식별하고 해당 애플리케이션의 설치를 완료합니다.

| Task | 세부 정보 | 필수 라이선스 |
| ---- | ------ | ---------------- |
| 애플리케이션 식별 | 조직에서 사용 중인 애플리케이션 식별: 온-프레미스, 클라우드의 SaaS 애플리케이션 및 기타 LOB(기간 업무) 애플리케이션 이러한 애플리케이션을 Azure AD로 관리할 수 있는지와 관리할 필요가 있는지 파악합니다. | 필요한 라이선스 없음 |
| [지원되는 SaaS 애플리케이션을 갤러리에 통합](../manage-apps/add-application-portal.md) | Azure AD에는 수천 개의 사전 통합 애플리케이션이 들어 있는 갤러리가 있습니다. 조직에서 사용하는 애플리케이션 중 일부는 Azure Portal에서 직접 액세스할 수 있는 갤러리에 있을 것입니다. | Azure AD Free |
| [애플리케이션 프록시를 사용하여 온-프레미스 애플리케이션 통합](../app-proxy/application-proxy-add-on-premises-application.md) | 사용자는 애플리케이션 프록시를 사용하여 해당 Azure AD 계정으로 로그인하여 온-프레미스 애플리케이션에 액세스할 수 있습니다. | |

## <a name="phase-4-audit-privileged-identities-complete-an-access-review-and-manage-user-lifecycle"></a>4단계: 권한 있는 ID 감사, 액세스 검토 완료 및 사용자 수명 주기 관리

4단계에서는 관리자가 관리를 위해 최소 권한 원칙을 적용하고, 첫 번째 액세스 검토를 완료하고, 일반 사용자 수명 주기 작업의 자동화를 사용하도록 설정하는 과정을 보여 줍니다.

| Task | 세부 정보 | 필수 라이선스 |
| ---- | ------ | ---------------- |
| [Privileged Identity Management 사용 적용](../privileged-identity-management/pim-security-wizard.md) | 매일 사용하는 일반 사용자 계정에서 관리자 역할을 제거합니다. Multi-Factor Authentication 검사를 성공하고, 비즈니스 근거를 제공하거나 지정된 승인자의 승인을 요청한 후에 관리자가 해당 역할을 사용할 수 있도록 지정합니다. | Azure AD Premium P2 |
| [PIM에서 Azure AD 디렉터리 역할에 대한 액세스 검토 완료](../privileged-identity-management/pim-create-azure-ad-roles-and-resource-roles-review.md) | 보안 및 리더십 팀과 협의하여 조직의 정책을 기준으로 관리 액세스 권한을 검토하는 액세스 검토 정책을 만듭니다. | Azure AD Premium P2 |
| [동적 그룹 멤버 자격 정책 구현](../enterprise-users/groups-dynamic-membership.md) | 동적 그룹을 사용하여 부서, 직함, 지역 및 기타 속성과 같은 HR(또는 진실의 근원)의 특성을 기반으로 사용자를 그룹에 자동으로 할당합니다. |  |
| [그룹 기반 애플리케이션 프로비전 구현](../manage-apps/what-is-access-management.md) | 그룹 기반 액세스 관리 프로비전을 사용하여 SaaS 애플리케이션에 사용자를 자동으로 프로비전합니다. |  |
| [사용자 프로비전 및 프로비전 해제 자동화](../app-provisioning/user-provisioning.md) | 무단 액세스를 방지하려면 직원 계정 수명 주기에서 수동 단계를 제거합니다. HR 시스템(진실의 근원)의 ID를 Azure AD에 동기화합니다. |  |

## <a name="next-steps"></a>다음 단계

[Azure AD 라이선스 및 가격 책정 세부 정보](https://www.microsoft.com/security/business/identity-access-management/azure-ad-pricing)

[ID 및 디바이스 액세스 구성](/microsoft-365/enterprise/microsoft-365-policies-configurations)

[일반적으로 권장되는 ID 및 디바이스 액세스 정책](/microsoft-365/enterprise/identity-access-policies)