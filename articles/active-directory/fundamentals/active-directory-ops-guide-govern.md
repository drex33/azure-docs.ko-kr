---
title: Azure Active Directory 거버넌스 작업 참조 가이드
description: 이 작업 참조 가이드에서는 거버넌스 관리를 보호하기 위해 수행해야 하는 검사 및 작업에 관해 설명함
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
ms.openlocfilehash: 065b0ca66b2e8d253ae798ce672638877ddceef8
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124732668"
---
# <a name="azure-active-directory-governance-operations-reference-guide"></a>Azure Active Directory 거버넌스 작업 참조 가이드

[Azure AD 작업 참조 가이드](active-directory-ops-guide-intro.md)의 이 섹션에서는 권한 없는 ID 및 권한 있는 ID, 감사 및 환경에 대한 제어 변경 사항을 평가하고 증명하기 위해 수행해야 하는 검사 및 작업에 대해 설명합니다.

> [!NOTE]
> 이 권장 사항은 게시 날짜를 기준으로 최신이지만 시간이 지나면 변경될 수 있습니다. 시간이 지남에 따라 Microsoft 제품 및 서비스가 발전하므로 조직은 거버넌스 사용 관행을 지속해서 평가해야 합니다.

## <a name="key-operational-processes"></a>주요 운영 프로세스

### <a name="assign-owners-to-key-tasks"></a>주요 작업에 소유자 할당

Azure Active Directory를 관리하려면 출시 프로젝트의 일부가 아닐 수 있는 주요 운영 작업 및 프로세스를 지속해서 실행해야 합니다. 환경을 최적화하기 위해 이 작업을 설정하는 것도 여전히 중요합니다. 핵심 작업과 권장 소유자는 다음과 같습니다.

| Task | 소유자 |
| :- | :- |
| SIEM 시스템에서 Azure AD 감사 로그 보관 | InfoSec 운영 팀 |
| 규정 비준수로 관리되는 애플리케이션 검색 | IAM 운영 팀 |
| 애플리케이션에 대한 액세스의 정기적 검토 | InfoSec 아키텍처 팀 |
| 외부 ID에 대한 액세스의 정기적 검토 | InfoSec 아키텍처 팀 |
| 권한 있는 역할이 있는 사용자의 정기적 검토 | InfoSec 아키텍처 팀 |
| 보안 게이트를 정의하여 권한 있는 역할 활성화 | InfoSec 아키텍처 팀 |
| 동의 권한 부여의 정기적 검토 | InfoSec 아키텍처 팀 |
| 조직의 직원을 기반으로 한 애플리케이션 및 리소스에 대한 카탈로그 및 액세스 패키지 디자인 | 앱 소유자 |
| 보안 정책을 정의하여 사용자를 패키지 액세스에 할당 | InfoSec 팀 + 앱 소유자 |
| 정책에 승인 워크플로가 포함되어 있는 경우 워크플로 승인의 정기적 검토 | 앱 소유자 |
| 액세스 검토를 사용하여 조건부 액세스 정책과 같은 보안 정책의 예외 검토 | InfoSec 운영 팀 |

목록을 검토할 때 소유자가 누락된 작업에 대해 소유자를 할당하거나 위의 권장 사항에 맞지 않는 소유자가 있는 작업에 대한 소유권을 조정해야 할 수도 있습니다.

#### <a name="owner-recommended-reading"></a>소유자 권장 읽기

- [Azure Active Directory에서 관리자 역할 할당](../roles/permissions-reference.md)
- [Azure에서 거버넌스](../../governance/index.yml)

### <a name="configuration-changes-testing"></a>구성 변경 테스트

사용자의 대상 하위 집합을 롤아웃하는 것과 같은 간단한 기술에서 병렬 테스트 테넌트에 변경 사항을 배포하는 방법에 이르기까지 테스트 시 특별히 고려해야 할 변경 사항이 있습니다. 테스트 전략을 구현하지 않은 경우 다음 표의 지침에 따라 테스트 방법을 정의해야 합니다.

| 시나리오| 권장 |
|-|-|
|인증 형식을 페더레이션된 형식에서 PHS/PTA로 변경하거나 반대로 변경| [단계적 롤아웃](../hybrid/how-to-connect-staged-rollout.md)을 사용하여 인증 형식 변경의 영향을 테스트합니다.|
|새 CA(조건부 액세스) 정책 또는 ID 보호 정책 롤아웃|새 조건부 액세스 정책을 만들고 테스트 사용자에게 할당합니다.|
|애플리케이션의 테스트 환경 온보딩|애플리케이션을 프로덕션 환경에 추가하고 MyApps 패널에서 숨긴 다음 QA(품질 보증) 단계에서 테스트 사용자에게 할당합니다.|
|동기화 규칙 변경|현재 프로덕션에 있는 동일한 구성(스테이징 모드라고도 함)을 사용하여 테스트 Azure AD Connect 변경을 수행하고 CSExport 결과를 분석합니다. 만족스러운 경우 준비가 되면 프로덕션으로 교환합니다.|
|브랜딩 변경|별도의 테스트 테넌트에서 테스트합니다.|
|새 기능 롤아웃|기능이 대상 사용자 집합에 대한 롤아웃을 지원하는 경우 파일럿 사용자를 식별하고 빌드합니다. 예를 들어 셀프 서비스 암호 재설정 및 다단계 인증은 특정 사용자 또는 그룹을 대상으로 할 수 있습니다.|
|애플리케이션을 온-프레미스 IdP(ID 공급자)(예: Active Directory)에서 Azure AD로 전환|애플리케이션이 여러 IdP 구성을 지원하는 경우(예: Salesforce) 변경 기간 동안 모두 구성하고 Azure AD를 테스트합니다(애플리케이션이 HRD 페이지를 도입하는 경우). 애플리케이션이 여러 IdP를 지원하지 않는 경우 변경 제어 기간 및 프로그램 가동 중지 시간 동안 테스트를 예약합니다.|
|동적 그룹 규칙 업데이트|새 규칙을 사용하여 병렬 동적 그룹을 만듭니다. 계산된 결과와 비교합니다. 예를 들어 동일한 조건으로 PowerShell을 실행합니다.<br>테스트를 통과하는 경우 이전 그룹이 사용된 위치(가능한 경우)로 교환합니다.|
|제품 라이선스 마이그레이션|[Azure Active Directory의 라이선스 그룹에서 단일 사용자에 대한 라이선스 변경](../enterprise-users/licensing-groups-change-licenses.md)을 참조하세요.|
|권한 부여, 발급, MFA 등의 AD FS 규칙 변경|그룹 클레임을 사용하여 사용자의 하위 집합을 대상으로 합니다.|
|AD FS 인증 환경 또는 이와 유사한 팜 전체 내용 변경|동일한 호스트 이름을 사용하여 병렬 팜을 만들고, 구성 변경 사항을 구현하고, HOSTS 파일, NLB 라우팅 규칙 또는 유사한 라우팅을 사용하여 클라이언트에서 테스트합니다.<br>대상 플랫폼에서 HOSTS 파일(예: 모바일 디바이스)을 지원하지 않는 경우 변경 사항을 제어합니다.|

## <a name="access-reviews"></a>액세스 검토

### <a name="access-reviews-to-applications"></a>애플리케이션에 대한 액세스 검토

시간이 지남에 따라 사용자는 리소스에 대한 액세스를 여러 팀과 위치 간에 이동하면서 누적할 수 있습니다. 리소스 소유자가 애플리케이션에 대한 액세스를 정기적으로 검토하고 사용자의 수명 주기 동안 더 이상 필요하지 않은 권한을 제거하는 것이 중요합니다. Azure AD [액세스 검토](../governance/access-reviews-overview.md)를 사용하면 조직에서 그룹 멤버 자격을 효율적으로 관리하고 엔터프라이즈 애플리케이션에 액세스하고 권한 있는 역할을 할당할 수 있습니다. 리소스 소유자는 사용자의 액세스를 정기적으로 검토하여 필요한 사람만 계속 액세스할 수 있게 합니다. 이상적으로 이 작업에는 Azure AD 액세스 검토를 사용하는 것이 좋습니다.

![액세스 검토 시작 페이지](./media/active-directory-ops-guide/active-directory-ops-img15.png)

> [!NOTE]
> 액세스 검토와 상호 작용하는 각 사용자에게는 유료 Azure AD Premium P2 라이선스가 있어야 합니다.

### <a name="access-reviews-to-external-identities"></a>외부 ID에 대한 액세스 검토

필요한 시간 동안 필요한 리소스로만 제한되는 외부 ID에 대한 액세스를 유지하는 것이 중요합니다. Azure AD [액세스 검토](../governance/access-reviews-overview.md)를 사용하여 모든 외부 ID 및 애플리케이션 액세스에 대한 일반적인 자동화된 액세스 검토 프로세스를 설정합니다. 프로세스가 이미 온-프레미스에 있는 경우 Azure AD 액세스 검토를 사용하는 것이 좋습니다. 애플리케이션이 사용 중지되거나 더 이상 사용되지 않으면 애플리케이션에 액세스할 수 있는 모든 외부 ID를 제거합니다.

> [!NOTE]
> 액세스 검토와 상호 작용하는 각 사용자에게는 유료 Azure AD Premium P2 라이선스가 있어야 합니다.

## <a name="privileged-account-management"></a>권한 있는 계정 관리

### <a name="privileged-account-usage"></a>권한 있는 계정 사용

해커는 중요한 데이터 및 시스템에 대해 신속하게 액세스하는 권한 있는 액세스의 관리자 계정 및 기타 요소를 대상으로 지정하는 경우가 많습니다. 권한 있는 역할을 가진 사용자는 시간이 지남에 따라 누적되기 때문에 관리자 액세스를 정기적으로 검토 및 관리하고 Azure AD 및 Azure 리소스에 대한 Just-In-Time 권한 액세스를 제공하는 것이 중요합니다.

조직에 권한 있는 계정을 관리하기 위한 프로세스가 없거나, 현재 일반 사용자 계정을 사용하여 서비스 및 리소스를 관리하는 관리자가 있는 경우, 계정을 즉시 분리해야 합니다(예: 하나는 일상적인 활동을 위한 계정, 다른 하나는 권한 있는 액세스 및 MFA를 사용한 구성이 포함된 계정). 조직에 Azure AD Premium P2 구독이 있는 경우 Azure PIM([Privileged Identity Management](../privileged-identity-management/pim-configure.md#license-requirements))을 즉시 배포하면 더 좋습니다. 동일한 토큰에서 권한 있는 계정도 검토하여 해당하는 경우 [권한이 더 적은 역할을 할당](../roles/security-planning.md)해야 합니다.

구현해야 하는 권한 있는 계정 관리의 또 다른 측면은 [PIM을 통해 자동화된](../privileged-identity-management/pim-perform-azure-ad-roles-and-resource-roles-review.md) 계정 또는 수동 계정에 대한 [액세스 검토](../governance/access-reviews-overview.md)를 정의하는 것입니다.

#### <a name="privileged-account-management-recommended-reading"></a>권한 있는 계정 관리 권장 참조 항목

- [Azure AD Privileged Identity Management의 역할](../privileged-identity-management/pim-roles.md)

### <a name="emergency-access-accounts"></a>응급 액세스 계정

조직은 다음과 같은 인증 중단 등의 경우 Azure AD를 관리하기 위해 준비할 [응급 계정](../roles/security-emergency-access.md)을 만들어야 합니다.

- 인증 인프라의 중단 구성 요소(AD FS, 온-프레미스 AD, MFA 서비스)
- 관리 직원 전직률

관리자 권한으로 기존 개인 사용자의 계정에 로그인하거나 활성화할 수 없어 테넌트가 잠기는 경우를 방지하려면 두 개 이상의 응급 계정을 만들어 [Microsoft의 모범 사례](../roles/security-planning.md) 및 [응급 상황 프로시저](../roles/security-planning.md#break-glass-what-to-do-in-an-emergency)에 맞게 구현하고 맞춰야 합니다.

### <a name="privileged-access-to-azure-ea-portal"></a>Azure EA 포털에 대한 권한 있는 액세스

[Azure EA (Azure 기업계약) 포털](https://azure.microsoft.com/blog/create-enterprise-subscription-experience-in-azure-portal-public-preview/)을 사용하면 엔터프라이즈 내에서 강력한 역할을 수행하는 마스터 기업계약에 대한 Azure 구독을 만들 수 있습니다. Azure AD를 사용하기 전에 이 포털의 생성을 부트스트랩하는 것이 일반적입니다. 따라서 Azure AD ID를 사용하여 이를 잠그고, 포털에서 개인 계정을 제거하고, 적절한 위임이 이루어졌는지 확인하고, 잠금 위험을 완화해야 합니다.

확실하게 하려면 EA 포털 권한 부여 수준이 현재 "혼합 모드"로 설정되어 있는 경우 EA 포털의 모든 권한 있는 액세스에서 모든 [Microsoft 계정을](https://support.skype.com/en/faq/FA12059/what-is-a-microsoft-account) 제거하고 Azure AD 계정만 사용하도록 EA 포털을 구성해야 합니다. EA 포털 위임된 역할이 구성되지 않은 경우 부서와 계정에 대한 위임된 역할도 직접 찾아서 구현해야 합니다.

#### <a name="privileged-access-recommended-reading"></a>권한 있는 액세스 권장 참조 항목

- [Azure Active Directory의 관리자 역할 사용 권한](../roles/permissions-reference.md)

## <a name="entitlement-management"></a>권한 관리

[EM(권한 관리)](../governance/entitlement-management-overview.md)을 통해 앱 소유자는 리소스를 번들로 묶어 조직의 특정 가상 사용자(내부 및 외부 모두)에게 할당할 수 있습니다. EM을 사용하면 거버넌스 정책을 통해 액세스 권한을 부여하고, 액세스 기간을 설정하고, 승인 워크플로를 허용하는 동시에 셀프 서비스 등록 및 비즈니스 소유자에 대한 위임이 가능합니다. 

> [!NOTE]
> Azure AD 권한 관리에 Azure AD Premium P2 라이선스가 필요합니다.

## <a name="summary"></a>요약

보안 ID 거버넌스에는 8가지 양상이 있습니다. 이 목록은 권한 없는 ID 및 권한 있는 ID에 부여된 액세스를 평가하고 증명하며, 감사를 수행하고, 환경에 대한 변경 사항을 제어하기 위해 수행해야 하는 작업을 식별하는 데 도움이 됩니다.

- 주요 작업에 소유자를 할당합니다.
- 테스트 전략을 구현합니다.
- Azure AD 액세스 검토를 사용하여 그룹 멤버 자격, 엔터프라이즈 애플리케이션에 대한 액세스 및 역할 할당을 효율적으로 관리합니다.
- 모든 형식의 외부 ID 및 애플리케이션 액세스에 대한 일반적으로 자동화된 액세스 검토 프로세스를 설정합니다.
- 액세스 검토 프로세스를 설정하여 정기적으로 관리자 액세스를 검토 및 관리하고 Azure AD 및 Azure 리소스에 대한 Just-In-Time 권한 액세스를 제공합니다.
- 예기치 않은 작동 중단이 발생할 경우 Azure AD를 관리하기 위해 준비할 응급 계정을 프로비전합니다.
- Azure EA 포털에 대한 액세스를 잠급니다.
- 권한 관리를 구현하여 리소스 컬렉션에 대한 관리되는 액세스를 제공합니다.

## <a name="next-steps"></a>다음 단계

[AZURE AD 작업 검사 및 작업](active-directory-ops-guide-ops.md)을 시작합니다.