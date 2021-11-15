---
title: 온-프레미스 워크로드용 Azure AD 클라우드 제어 관리 - Azure
description: 이 항목에서는 온-프레미스 워크로드용 클라우드 제어 관리에 대해 설명합니다.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3510851f311e57173d5fd023db948c7f71597f26
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130047383"
---
# <a name="how-azure-ad-delivers-cloud-governed-management-for-on-premises-workloads"></a>Azure AD가 온-프레미스 워크로드용 클라우드 제어 관리를 제공하는 방법

Azure AD(Azure Active Directory)는 많은 조직들이 사용하는 ID, 액세스 관리 및 보안의 모든 측면에 걸친 포괄적 IDaaS(Identity as a Service) 솔루션입니다. Azure AD는 10억 개 이상의 사용자 ID를 보유하고 있으며 사용자가 로그인하고 안전하게 액세스할 수 있도록 지원합니다.

* Microsoft 365, Azure Portal, 수천 개의 기타 SaaS(Software as a Service) 애플리케이션을 비롯한 외부 리소스
* 조직의 회사 네트워크 및 인트라넷에 있는 애플리케이션과 같은 내부 리소스와 해당 조직에서 개발한 모든 클라우드 애플리케이션

조직은 '순수 클라우드'인 경우 Azure AD를 사용하고, 온-프레미스 워크로드가 있는 경우 '하이브리드' 배포로 사용할 수 있습니다. Azure AD의 하이브리드 배포는 조직에서 IT 자산을 클라우드로 마이그레이션하거나 기존 온-프레미스 인프라를 새 클라우드 서비스와 함께 계속 통합하기 위한 전략의 일부가 될 수 있습니다.

지금까지 '하이브리드' 조직은 Azure AD를 기존 온-프레미스 인프라의 확장으로 보았습니다. 이러한 배포에서 온-프레미스 ID 거버넌스 관리, Windows Server Active Directory 또는 기타 사내 디렉터리 시스템은 제어 지점이며, 사용자 및 그룹은 해당 시스템에서 Azure AD와 같은 클라우드 디렉터리로 동기화됩니다. 이러한 ID가 클라우드에 있으면 Microsoft 365, Azure 및 기타 애플리케이션에서 사용할 수 있습니다.

![ID 수명 주기](media/cloud-governed-management-for-on-premises//image1.png)

조직에서 애플리케이션과 함께 더 많은 IT 인프라를 클라우드로 이동하면서, 많은 조직이 ID 관리 서비스의 향상된 보안 및 단순화된 관리 기능을 찾고 있습니다. Azure AD의 클라우드 제공 IDaaS 기능은 조직이 기존의 온-프레미스 시스템에서 Azure AD로 더 많은 ID 관리를 신속하게 도입하고 이전하도록 하는 솔루션과 기능을 제공함으로써 클라우드 제어 관리로의 전환을 가속화하는 동시에 새로운 애플리케이션과 기존 애플리케이션도 계속 지원합니다.

이 문서에서는 하이브리드 IDaaS의 Microsoft 전략에 대해 설명하고 조직에서 기존 애플리케이션에 Azure AD를 사용하는 방법을 설명합니다.

## <a name="the-azure-ad-approach-to-cloud-governed-identity-management"></a>클라우드 제어 ID 관리에 대한 Azure AD 접근 방법

조직은 클라우드로 전환함에 따라 자동화 및 사전 인사이트에서 지원되는 활동에 대한 보안과 가시성을 높여 전체 환경을 제어할 수 있다는 보증이 필요합니다. "**클라우드 제어 관리**"는 조직이 클라우드에서 사용자, 애플리케이션, 그룹 및 디바이스를 제어하고 관리하는 방법을 설명합니다.

이 현대 사회에서 조직은 SaaS 애플리케이션의 확산과 협업 및 외부 ID의 역할 증가 때문에 대규모를 효과적으로 관리할 수 있어야 합니다. 클라우드의 새로운 위험 환경은 조직이 보다 신속하게 대응해야 합니다. 클라우드 사용자를 손상시키는 악의적인 행위자는 클라우드 및 온-프레미스 애플리케이션에 영향을 미칠 수 있습니다.

특히 하이브리드 조직은 지금까지 IT에서 수동으로 수행했던 작업을 위임하고 자동화할 수 있어야 합니다. 작업을 자동화하려면 다양한 ID 관련 리소스(사용자, 그룹, 애플리케이션, 디바이스)의 수명 주기를 오케스트레이션하는 API 및 프로세스가 필요하므로 핵심 IT 직원이 아닌 더 많은 개인에게 해당 리소스의 일상적인 관리를 위임할 수 있습니다. Azure AD는 온-프레미스 ID 인프라를 요구하지 않고 사용자 계정 관리 및 사용자에 대한 네이티브 인증을 통해 이러한 요구 사항을 해결합니다. 온프레미스 인프라를 구축하지 않으면 온-프레미스 디렉터리에서 시작되지 않았지만 액세스 관리가 비즈니스 성과를 달성하는 데 중요한 비즈니스 파트너와 같이 새로운 사용자 커뮤니티가 있는 조직에 혜택을 줄 수 있습니다.

또한 거버넌스 없이는 관리가 완료되지 않으며, 이 새로운 세계의 거버넌스는 추가 항목이 아닌 이 ID 시스템에 통합된 일부분입니다. ID 거버넌스를 통해 조직은 직원, 비즈니스 파트너 및 공급업체, 서비스 및 애플리케이션 전반에 걸쳐 ID와 액세스 수명 주기를 관리할 수 있습니다.

ID 거버넌스를 통합하면 조직에서 클라우드 제어 관리로 쉽게 전환할 수 있고, IT를 확장할 수 있으며, 게스트와 관련된 새로운 문제를 해결하고, 고객이 온-프레미스 인프라에서 사용하던 것보다 더 심층적인 인사이트와 자동화를 제공할 수 있습니다. 이 새로운 세계의 거버넌스는 조직에서 조직의 리소스에 대한 액세스 투명도, 가시성 및 적절한 제어를 제공하는 기능을 의미합니다. Azure AD를 사용하면 보안 운영 및 감사 팀이 조직의 디바이스 리소스에 대한 액세스 권한이 있는 사용자와 액세스 권한이 있어야 하는 사용자, 그 사용자들이 액세스로 수행하는 활동을 볼 수 있으며, 조직은 적절한 제어를 확보하고 행사하여 회사나 규정 정책에 따라 액세스를 제거하거나 차단합니다.

새 관리 모델은 이러한 애플리케이션에 대한 액세스를 보다 쉽게 관리하고 보호할 수 있으므로 SaaS 및 LOB(사업 부문) 애플리케이션을 모두 사용하는 조직에 유용합니다. 조직에서는 애플리케이션을 Azure AD와 통합하여 클라우드 및 온-프레미스에서 발생하는 ID의 액세스를 일관되게 사용하고 관리할 수 있습니다. 애플리케이션 수명 주기 관리는 더욱 자동화되며, Azure AD는 온-프레미스 ID 관리에서 쉽게 달성할 수 없는 애플리케이션 사용에 대한 풍부한 인사이트를 제공합니다. 조직에서는 Azure AD, Microsoft 365 그룹 및 팀 셀프 서비스 기능을 통해 액세스 관리 및 공동 작업을 위한 그룹을 쉽게 만들고, 클라우드에서 사용자를 추가하거나 제거하여 공동 작업 및 액세스 관리 요구 사항을 설정할 수 있습니다.

클라우드 제어 관리에 적합한 Azure AD 기능을 선택하는 것은 사용할 애플리케이션 및 Azure AD와 이러한 애플리케이션을 통합하는 방법에 따라 달라집니다. 다음 섹션에서는 AD 통합 애플리케이션에 대해 수행할 방법과 페더레이션 프로토콜을 사용하는 애플리케이션(예: SAML, OAuth 또는 OpenID Connect)에 대해 간략하게 설명합니다.

## <a name="cloud-governed-management-for-ad-integrated-applications"></a>AD 통합 애플리케이션에 대한 클라우드 제어 관리

Azure AD는 보안 원격 액세스 및 해당 애플리케이션에 대한 조건부 액세스를 통해 조직의 온-프레미스 Active Directory 통합 애플리케이션에 대한 관리를 개선합니다. 또한 Azure AD는 다음을 포함하여 사용자의 기존 AD 계정에 대한 계정 수명 주기 관리 및 자격 증명 관리도 제공합니다.

* **온-프레미스 애플리케이션에 대한 보안 원격 액세스 및 조건부 액세스**

많은 조직의 경우 온-프레미스 AD 통합 웹 및 원격 데스크톱 기반 애플리케이션을 위해 클라우드에서 액세스를 관리하는 첫 번째 단계는 안전한 원격 액세스를 제공하기 위해 해당 애플리케이션 앞에 [애플리케이션 프록시](../app-proxy/application-proxy.md)를 배포하는 것입니다.

Azure AD에 Single Sign-On 되면 사용자는 외부 URL 또는 내부 애플리케이션 포털을 통해 클라우드와 온-프레미스 애플리케이션에 모두 액세스할 수 있습니다. 예를 들어 애플리케이션 프록시는 원격 데스크톱, SharePoint, Tableau 및 Qlik 같은 앱, LOB(사업 부문) 애플리케이션에 대한 원격 액세스 및 Single Sign-On을 제공합니다. 뿐만 아니라 조건부 액세스 정책에는 애플리케이션에 액세스하기 전에 [사용 약관](../conditional-access/terms-of-use.md)을 표시하고 [사용자가 해당 약관에 동의했는지 확인](../conditional-access/require-tou.md)하는 작업이 포함될 수 있습니다.

![앱 프록시 아키텍처](media/cloud-governed-management-for-on-premises/image2.png)

* **Active Directory 계정에 대한 자동 수명 주기 관리**

조직에서는 ID 거버넌스를 통해 *생산성*(사용자가 조직에 가입할 때처럼 개인이 필요한 리소스에 액세스하는 속도) 및 *보안*(개인의 고용 상태가 변경되었을 때처럼 시간 경과에 따라 액세스 권한이 변경되어야 하는 상황) 사이에서 균형을 잡을 수 있습니다. ID 수명 주기 관리는 ID 거버넌스의 기반이며 대규모의 효과적인 거버넌스에는 애플리케이션에 대한 ID 수명 주기 관리 인프라를 현대화해야 합니다.

많은 조직에서 직원의 ID 수명 주기는 HCM(인적 자본 관리) 시스템에서 해당 사용자의 표현과 연결되어 있습니다. Workday를 HCM 시스템으로 사용하는 조직의 경우 Azure AD는 AD의 사용자 계정이 [Workday의 작업자에 대해 자동으로 프로비전하거나 프로비전이 해제](../saas-apps/workday-inbound-tutorial.md)되도록 할 수 있습니다. 이렇게 하면 기본(birthright) 계정 자동화를 통해 사용자 생산성이 향상되고 사용자의 역할이 변경되거나 조직을 떠났을 때 애플리케이션 액세스 권한이 자동으로 업데이트되어 위험을 관리합니다. Workday 기반 사용자 프로비전 [배포 계획](https://aka.ms/WorkdayDeploymentPlan)은 조직에서 5단계 프로세스를 통해 Workday에서 Active Directory 사용자 프로비전 솔루션으로 구현하는 모범 사례를 안내하는 단계별 가이드입니다.

또한 Azure AD Premium에는 SAP, Oracle eBusiness 및 Oracle PeopleSoft 같은 기타 온-프레미스 HCM 시스템에서 레코드를 가져올 수 있는 Microsoft Identity Manager가 포함됩니다.

B2B 협업을 위해서는 조직 외부의 사람에게 액세스 권한을 부여해야 합니다. [Azure AD B2B](/azure/active-directory/b2b/) 협업을 사용하면 조직의 애플리케이션과 서비스를 게스트 사용자 및 모든 조직의 외부 파트너와 안전하게 공유하면서 고유한 회사 데이터에 대한 제어를 유지합니다.

Azure AD는 필요에 따라 [게스트 사용자를 위해 AD에서 계정을 자동으로 만들 수 있으므로](../external-identities/hybrid-cloud-to-on-premises.md) 비즈니스 게스트가 다른 암호 없이 온-프레미스 AD 통합 애플리케이션에 액세스할 수 있습니다. 조직에서는 [게스트 사용자에 대해 MFA(다단계 인증) 정책](../external-identities/conditional-access.md)을 설정할 수 있으므로 애플리케이션 프록시 인증 중에 MFA 검사가 수행됩니다. 또한 클라우드 B2B 사용자에 대해 수행되는 모든 [액세스 검토](../governance/manage-guest-access-with-access-reviews.md)는 온-프레미스 사용자에게 적용됩니다. 예를 들어 수명 주기 관리 정책을 통해 클라우드 사용자가 삭제된 경우 온-프레미스 사용자도 삭제됩니다.

**Active Directory 계정에 대한 자격 증명 관리** Azure AD의 셀프 서비스 암호 재설정을 사용하면 암호를 잊어버린 사용자가 다시 인증을 받을 수 있으며, [온-프레미스 Active Directory에 기록](../authentication/concept-sspr-writeback.md)된 변경된 암호를 사용하여 암호를 다시 설정할 수 있습니다. 암호 재설정 프로세스는 온-프레미스 Active Directory 암호 정책을 사용할 수도 있습니다. 사용자가 암호를 다시 설정하는 경우 해당 디렉터리에 커밋하기 전에 온-프레미스 Active Directory 정책을 충족하는지 확인합니다. 셀프 서비스 암호 재설정 [배포 계획](../authentication/howto-sspr-deployment.md)은 웹 및 Windows 통합 환경을 통해 사용자에게 셀프 서비스 암호 재설정을 배포하는 모범 사례를 간략하게 설명합니다.

![Azure AD SSPR 아키텍처](media/cloud-governed-management-for-on-premises/image3.png)

마지막으로, 사용자가 AD에서 자신의 암호를 변경할 수 있도록 허용하는 조직의 경우, 현재 공개 미리 보기로 제공되는 [Azure AD 암호 보호 기능](../authentication/concept-password-ban-bad-on-premises.md)을 통해 조직이 Azure AD에서 사용하는 것과 동일한 암호 정책을 사용하도록 AD를 구성할 수 있습니다.

조직이 애플리케이션을 호스트하는 운영 체제를 Azure로 이동하여 AD 통합 애플리케이션을 클라우드로 이동할 준비가 되면, [Azure AD Domain Services](../../active-directory-domain-services/overview.md)는 AD 호환 도메인 서비스(예: 도메인 가입, 그룹 정책, LDAP, Kerberos/NTLM 인증)를 제공합니다. Azure AD Domain Services는 조직의 기존 Azure AD 테넌트와 통합하므로 사용자가 회사 자격 증명을 사용하여 로그인할 수 있도록 합니다. 또한 기존 그룹 및 사용자 계정을 사용하여 리소스에 대한 액세스를 보호하므로 Azure 인프라 서비스에 온-프레미스 리소스를 원활하게 '리프트 앤 시프트'할 수 있습니다.

![Azure AD Domain Services](media/cloud-governed-management-for-on-premises/image4.png)

## <a name="cloud-governed-management-for-on-premises-federation-based-applications"></a>온-프레미스 페더레이션 기반 애플리케이션에 대한 클라우드 제어 관리

이미 온-프레미스 ID 공급자를 사용하는 조직의 경우 Azure AD로 애플리케이션을 이동하면 더 안전한 액세스와 페더레이션 관리를 위한 보다 쉬운 관리 환경이 가능합니다. Azure AD에서는 Azure AD 조건부 액세스를 사용하여 Azure AD 다단계 인증을 비롯한 세부적인 애플리케이션별 액세스 제어를 구성할 수 있습니다. Azure AD는 애플리케이션 관련 토큰 서명 인증서 및 구성 가능한 인증서 만료 날짜를 포함하여 더 많은 기능을 지원합니다. 이러한 기능, 도구 및 참고 자료를 통해 조직은 온-프레미스 ID 공급자를 사용 중지할 수 있습니다. 예를 들어 Microsoft의 자체 IT는 17,987개의 애플리케이션을 Microsoft의 내부 AD FS(Active Directory Federation Services)에서 Azure AD로 이동했습니다.

![Azure AD 진화](media/cloud-governed-management-for-on-premises/image5.png)

페더레이션된 애플리케이션을 ID 공급자로 Azure AD로 마이그레이션하기 시작하려면 다음 링크가 포함된 https://aka.ms/migrateapps 을 참조하세요.

* [Azure Active Directory로 애플리케이션 마이그레이션](https://aka.ms/migrateapps/whitepaper)백서는 마이그레이션의 이점을 제시하고 명확하게 설명된 네 가지 단계인 검색, 분류, 마이그레이션 및 지속적인 관리를 통해 마이그레이션을 계획하는 방법을 설명합니다. 프로세스에 대해 생각하고 프로젝트를 이용하기 쉬운 조각으로 나누는 방법을 안내합니다. 문서 전체에는 과정에 도움이 되는 중요한 리소스에 대한 링크가 있습니다.

* 솔루션 가이드 [Active Directory Federation Services 애플리케이션 인증을 Azure Active Directory로 마이그레이션](../manage-apps/migrate-adfs-apps-to-azure.md)에서는 애플리케이션 마이그레이션 프로젝트를 계획하고 실행하는 동일한 4단계의 세부 정보를 살펴봅니다. 이 가이드에서는 AD FS(Active Directory Federation Services)에서 Azure AD로 애플리케이션을 이동하는 특정 목적에 해당 단계를 적용하는 방법을 알아봅니다.

* [Active Directory Federation Services 마이그레이션 준비 스크립트](https://aka.ms/migrateapps/adfstools)를 기존 온-프레미스 AD FS(Active Directory Federation Services) 서버에서 실행하여 Azure AD로 마이그레이션할 애플리케이션의 준비 상태를 확인할 수 있습니다.

## <a name="ongoing-access-management-across-cloud-and-on-premises-applications"></a>클라우드 및 온-프레미스 애플리케이션에서 지속적인 액세스 관리

조직에는 확장성 있는 액세스를 관리하는 프로세스가 필요합니다. 사용자는 계속해서 액세스 권한을 누적하며 처음에 프로비전된 것을 한참 초과하게 됩니다. 또한 기업 조직은 지속적으로 액세스 정책 및 제어를 개발하고 시행하기 위해 효율적으로 확장할 수 있어야 합니다.

IT 대리자는 일반적으로 비즈니스 의사 결정권자에 대한 승인 결정에 액세스합니다. 또한 IT에는 사용자가 스스로가 포함될 수 있습니다. 예를 들어 유럽에 있는 회사의 마케팅 애플리케이션에서 기밀 고객 데이터에 액세스하는 사용자는 회사의 정책을 알아야 합니다. 게스트 사용자는 초대된 조직의 데이터에 대해 처리 중인 요구 사항을 인식하지 못할 수도 있습니다.

조직은 [동적 그룹](../enterprise-users/groups-dynamic-membership.md), [SaaS 애플리케이션](../saas-apps/tutorial-list.md)에 대한 사용자 프로비저닝 또는 [SCIM(System for Cross-Domain Identity Management) 표준을 사용하여 통합된 애플리케이션](../app-provisioning/use-scim-to-provision-users-and-groups.md)과 같은 기술을 통해 액세스 수명 주기 프로세스를 자동화할 수 있습니다. 조직은 [온-프레미스 애플리케이션에 액세스할 수 있는 게스트 사용자](../external-identities/hybrid-cloud-to-on-premises.md)를 제어할 수도 있습니다. 그런 다음, 정기 [Azure AD 액세스 검토](../governance/access-reviews-overview.md)를 사용하여 이러한 액세스 권한을 정기적으로 검토할 수 있습니다.

## <a name="future-directions"></a>향후 방향

하이브리드 환경에서 Microsoft의 전략은 **클라우드가 ID의 컨트롤 플레인** 인 배포를 사용하는 것이며, 온-프레미스 디렉터리와 Active Directory 및 기타 온-프레미스 애플리케이션과 같은 기타 ID 시스템은 액세스 권한이 있는 사용자를 프로비전하는 대상입니다. 이 전략은 해당 애플리케이션 및 해당 애플리케이션에 의존하는 작업에서 권한, ID 및 액세스를 지속적으로 보장합니다. 이 최종 상태에서 조직은 클라우드에서 최종 사용자의 생산성을 완전히 높일 수 있습니다.

![Azure AD 아키텍처](media/cloud-governed-management-for-on-premises/image6.png)

## <a name="next-steps"></a>다음 단계

이 경험을 시작하는 방법에 대한 자세한 정보는 <https://aka.ms/deploymentplans>에 있는 Azure AD 배포 계획을 참조하세요. Azure AD(Azure Active Directory) 기능을 배포하는 방법에 대한 엔드투엔드 참고 자료를 제공합니다. 각 계획은 일반적인 Azure AD 기능을 성공적으로 배포하는 데 필요한 비즈니스 가치, 계획 고려 사항, 디자인 및 운영 절차를 설명합니다. Microsoft는 Azure AD를 사용하여 클라우드에서 관리할 새로운 기능을 추가할 때 고객 배포 및 기타 피드백에서 학습한 모범 사례로 배포 계획을 지속적으로 업데이트합니다.