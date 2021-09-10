---
title: 배포 계획 - Azure Active Directory | Microsoft Docs
description: 많은 Azure Active Directory 기능을 배포하는 방법에 대한 지침입니다.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 12/01/2020
ms.author: baselden
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8d2a64ec05fb3f6d353374684c14733dda9b302d
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114443053"
---
# <a name="azure-active-directory-deployment-plans"></a>Azure Active Directory 배포 계획
Azure AD(Azure Active Directory) 기능 배포에 대한 완전한 지침을 찾고 계신가요? Azure AD 배포 플랜은 보다 일반적인 Azure AD 기능을 성공적으로 배포하기 위해 필요한 비즈니스 가치, 계획 고려 사항 및 운영 절차를 안내합니다.

플랜 페이지에서 브라우저의 PDF로 인쇄 기능을 사용하여 설명서의 최신 오프라인 버전을 만듭니다.


## <a name="deploy-authentication"></a>인증 배포

| 기능 | 설명|
| -| -|
| [Azure AD 다단계 인증](../authentication/howto-mfa-getstarted.md)| Azure AD MFA(Multi-Factor Authentication)는 Microsoft의 2단계 인증 솔루션입니다. 관리자가 승인한 인증 방법을 사용하는 Azure AD MFA는 간단한 로그인 프로세스에 대한 요구 사항을 충족하는 동시에 데이터와 애플리케이션에 대한 액세스를 보호합니다. [테넌트에서 다단계 인증을 구성하고 적용하는 방법](https://www.youtube.com/watch?v=qNndxl7gqVM)을 알아보려면 이 비디오를 시청하세요.|
| [조건부 액세스](../conditional-access/plan-conditional-access.md)| 조건부 액세스를 사용하면 조건에 따라 클라우드 앱에 액세스할 수 있는 사용자를 결정하는 자동 액세스 제어 결정 시스템을 구현할 수 있습니다. |
| [셀프 서비스 암호 재설정](../authentication/howto-sspr-deployment.md)| 셀프 서비스 암호 재설정을 사용하면 사용자가 필요할 때 필요한 장소에서 관리자 개입 없이 자신의 암호를 재설정할 수 있습니다. |
| [암호 없음](../authentication/howto-authentication-passwordless-deployment.md) | 조직의 Microsoft Authenticator 앱 또는 FIDO2 보안 키를 사용하여 암호 없는 인증 구현 |

## <a name="deploy-application-and-device-management"></a>애플리케이션 및 디바이스 관리 배포

| 기능 | Description|
| -| - |
| [Single Sign-On](../manage-apps/plan-sso-deployment.md)| Single Sign-On은 한 번만 로그인하면 비즈니스 수행에 필요한 모든 앱과 리소스에 액세스할 수 있게 해줍니다. 로그인한 후에는 자격 증명을 다시 입력할 필요 없이 Microsoft Office에서 SalesForce, Box 또는 내부 애플리케이션으로 이동할 수 있습니다. |
| [내 앱](../manage-apps/my-apps-deployment-plan.md)| 사용자에게 모든 애플리케이션을 검색 및 액세스할 수 있는 간단한 허브를 제공합니다. 앱 및 그룹에 대한 액세스를 요청하거나 다른 사용자를 대신하여 리소스에 대한 액세스를 관리하는 등의 셀프 서비스 기능을 통해 사용자 생산성을 높일 수 있습니다. |
| [디바이스](../devices/plan-device-deployment.md) | 이 문서를 통해 디바이스를 Azure AD와 통합하는 방법을 평가하고, 구현 계획을 선택하고, 지원되는 디바이스 관리 도구에 대한 주요 링크를 제공할 수 있습니다. |


## <a name="deploy-hybrid-scenarios"></a>하이브리드 시나리오 배포  

| 기능 | 설명|
| -| -|
| [AD FS에서 클라우드로의 사용자 인증](../hybrid/migrate-from-federation-to-cloud-authentication.md)| 패스스루 인증 또는 암호 해시 동기화를 사용하여 페더레이션에서 클라우드 인증으로 사용자 인증을 마이그레이션하는 방법을 알아봅니다.
| [Azure AD 애플리케이션 프록시](../app-proxy/application-proxy-deployment-plan.md) |요즈음 직원은 어디서나 언제든지 어느 디바이스에서나 생산성을 높이기를 원합니다. 클라우드 및 회사 앱 온-프레미스에서 SaaS 앱에 액세스해야 합니다. Azure AD 애플리케이션 프록시를 사용하면 비싸고 복잡한 VPN(가상 사설망) 또는 DMZ(경계 네트워크) 없이 견고한 액세스가 가능합니다. |
| [매끄러운 SSO](../hybrid/how-to-connect-sso-quick-start.md)| Azure AD Seamless SSO(Azure Active Directory Seamless Single Sign-On)는 회사 네트워크에 연결된 회사 디바이스에 있을 때 사용자를 자동으로 서명합니다. 이 기능을 사용하면 사용자가 Azure AD에 로그인하기 위해 암호를 입력할 필요가 없으며, 일반적으로 사용자 이름도 입력할 필요가 있습니다. 추가 온-프레미스 구성 요소가 없어도 인증된 사용자가 클라우드 기반 애플리케이션에 쉽게 액세스할 수 있습니다. |

## <a name="deploy-user-provisioning"></a>사용자 프로비저닝 배포

| 기능 | Description|
| -| -|
| [사용자 프로비전](../app-provisioning/plan-auto-user-provisioning.md)| Azure AD를 사용하면 Dropbox, Salesforce, ServiceNow 등과 같은 클라우드 애플리케이션(SaaS)에서 사용자 ID 만들기, 유지 관리 및 제거를 자동화할 수 있습니다. |
| [클라우드 HR 사용자 프로비저닝](../app-provisioning/plan-cloud-hr-provision.md)| 클라우드 HR 사용자를 Active Directory에 프로비저닝하면 지속적인 ID 거버넌스를 위한 기반을 만들고, 신뢰할 수 있는 ID 데이터를 사용하는 비즈니스 프로세스의 품질을 향상시킬 수 있습니다. Workday 또는 Successfactors와 같은 클라우드 HR 제품에 이 기능을 사용하면 연결기-이동기-이탈기(Joiner-Mover-Leaver) 프로세스(예: 새 고용, 해지, 이전)를 IT 프로비전 작업(예: 계정 만들기, 사용, 사용 안 함)에 매핑하는 규칙을 구성하여 직원 및 비정규 작업자의 ID 수명 주기를 원활하게 관리할 수 있습니다. |

## <a name="deploy-governance-and-reporting"></a>거버넌스 및 보고 배포

| 기능 | Description|
| -| -|
| [Privileged Identity Management](../privileged-identity-management/pim-deployment-plan.md)| Azure AD PIM(Privileged Identity Management)을 사용하면 Azure AD, Azure 리소스 및 기타 Microsoft 온라인 서비스 간에 권한 있는 관리자 역할을 관리할 수 있습니다. PIM은 권한 있는 역할의 악성 활동을 실시간으로 식별, 발견 및 방지할 수 있도록 Just-In-Time 액세스, 요청 승인 워크플로 및 완전히 통합된 액세스 검토와 같은 솔루션을 제공합니다. |
| [보고 및 모니터링](../reports-monitoring/plan-monitoring-and-reporting.md)| Azure AD 보고 및 모니터링 솔루션의 디자인은 법적, 보안 및 운영 요구 사항뿐만 아니라 기존 환경 및 프로세스에 따라 달라집니다. 이 문서에서는 다양한 설계 옵션을 제시하고 올바른 배포 전략을 안내합니다. |
| [액세스 검토](../governance/deploy-access-reviews.md) | 액세스 검토는 거버넌스 전략의 중요한 부분으로, 액세스 권한이 있는 사용자와 보유하는 액세스 권한을 파악하고 관리할 수 있습니다. 이 문서는 원하는 보안 및 협업 태세를 달성하기 위해 액세스 검토를 계획하고 배포하는 데 도움이 됩니다. |

## <a name="include-the-right-stakeholders"></a>올바른 관련자 포함

새 기능에 대한 배포 계획을 시작할 때는 조직 전체의 주요 관련자를 포함하는 것이 중요합니다. 다음의 각 역할을 수행하는 사람을 식별 및 문서화하고, 함께 협의하여 프로젝트에 대한 참여를 결정하는 것이 좋습니다.  

역할에는 다음이 포함될 수 있습니다. 

|역할 |Description |
|-|-|
|최종 사용자|기능을 구현할 대표적인 사용자 그룹입니다. 종종 파일럿 프로그램의 변경 내용을 미리 봅니다.
|IT 지원 관리자|고객 지원팀 관점에서 이 변경의 지원 가능성에 대한 입력을 제공할 수 있는 IT 지원 조직 출신 담당자입니다.  
|설계자 또는 Azure 글로벌 관리자 식별|이 변경을 조직의 핵심 ID 관리 인프라와 일치시키는 방법을 정의할 책임이 있는 ID 관리 팀 출신 담당자입니다.|
|애플리케이션 비즈니스 소유자 |액세스 관리가 포함되는 영향을 받는 애플리케이션의 전체 비즈니스 소유자입니다.또한 최종 사용자 관점에서 사용자 환경 및 이러한 변경의 유용성에 대한 의견을 제시할 수 있습니다.
|보안 소유자|계획이 조직의 보안 요구 사항을 충족하는지 확인할 수 있는 보안 팀의 담당자입니다.|
|규정 준수 관리자|회사, 산업 또는 정부 요구 사항을 준수할 책임이 있는 조직 내의 담당자입니다.|

**참여 수준:**

- 프로젝트 계획 및 결과를 구현할 **R** esponsible(책임이 있음) 

- 프로젝트 계획 및 결과를 **A** pproval(승인) 

- 프로젝트 플랜 및 결과에 대한 **C** contributor(기여자) 

- 프로젝트 계획 및 결과에 대해 **I** formed(알림을 받음)

## <a name="best-practices-for-a-pilot"></a>파일럿에 대한 모범 사례
파일럿을 활용하면 모든 사용자를 위해 기능을 설정하기 전에 소규모 그룹으로 테스트할 수 있습니다. 테스트의 일환으로 조직 내 각 사용 사례를 철저하게 테스트해야 합니다. 이 배포를 조직 전체에 롤아웃하기 전에 특정 파일럿 사용자 그룹을 대상으로 지정하는 것이 가장 좋습니다.

첫 번째 단계에서는 테스트를 수행하고 피드백을 제공할 수 있는 IT, 편리성 및 기타 적절한 사용자를 대상으로 합니다. 이 피드백을 사용하여 사용자에게 보내는 통신 및 지침을 추가로 개발하고 지원 담당자가 볼 수 있는 문제 유형에 대한 인사이트를 제공합니다. 

대상 그룹의 범위를 늘려서 더 큰 사용자 그룹으로 롤아웃을 확장해야 합니다. 이 작업은 [동적 그룹 멤버 자격](../enterprise-users/groups-dynamic-membership.md)을 통해 또는 대상 그룹에 사용자를 수동으로 추가하여 수행할 수 있습니다.