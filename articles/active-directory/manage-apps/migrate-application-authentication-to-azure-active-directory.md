---
title: Azure Active Directory에 애플리케이션 인증 마이그레이션
description: 이 백서에서는 애플리케이션 인증을 Azure AD로 마이그레이션하는 계획 및 혜택에 대해 자세히 설명합니다.
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 02/05/2021
ms.author: davidmu
ms.reviewer: alamaral
ms.collection: M365-identity-device-management
ms.openlocfilehash: 365283ebc45c0f85a56d27d4cc90807de9a88fb4
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122529010"
---
# <a name="migrate-application-authentication-to-azure-active-directory"></a>Azure Active Directory에 애플리케이션 인증 마이그레이션

## <a name="about-this-paper"></a>이 백서 정보

이 백서에서는 애플리케이션 인증을 Azure AD로 마이그레이션하는 계획 및 혜택에 대해 자세히 설명합니다. Azure 관리자 및 ID 전문가를 위해 설계되었습니다.

프로세스를 각각 세부적인 계획 및 종료 기준이 있는 4단계로 나누어 마이그레이션 전략을 계획하고 Azure AD 인증이 조직의 목표를 어떻게 지원하는지 이해하는 데 도움이 되도록 설계되었습니다.

## <a name="introduction"></a>소개

오늘날 조직에서는 사용자가 작업을 완료하기까지 굉장히 많은 수의 애플리케이션(앱)이 요구됩니다. 매일 앱을 계속 추가, 개발 또는 사용 중지할 가능성이 높습니다. 사용자는 다양한 회사와 개인 디바이스 및 위치에서 이러한 애플리케이션에 액세스합니다. 다음을 비롯한 여러 가지 방법으로 앱을 엽니다.

- 회사 홈페이지 또는 포털

- 브라우저에서 책갈피 설정

- SaaS(Software as a Service) 앱에 대한 공급 업체의 URL

- MDM/MAM(모바일 디바이스/애플리케이션 관리) 솔루션을 통해 사용자의 데스크톱 또는 모바일 디바이스에 직접 푸시된 링크

애플리케이션은 다음과 같은 유형의 인증을 사용할 가능성이 높습니다.

- 온-프레미스 페더레이션 솔루션(예: ADFS(Active Directory Federation Services) 및 Ping)

- Active Directory(예: Kerberos 인증 및 Windows 통합 인증)

- 기타 클라우드 기반 IAM(ID 및 액세스 관리) 솔루션(예: Okta 또는 Oracle)

- 온-프레미스 웹 인프라(예: IIS 및 Apache)

- 클라우드 호스팅 인프라(예: Azure 및 AWS)

**사용자가 애플리케이션을 쉽고 안전하게 액세스할 수 있도록 온-프레미스 및 클라우드 환경에서 액세스 제어 및 정책의 단일 집합을 보유하는 것이 목표입니다.**

[Azure AD(Azure Active Directory)](../fundamentals/active-directory-whatis.md)는 사용자, 파트너, 고객에게 원하는 애플리케이션에 액세스하고 모든 플랫폼과 디바이스에서 협업할 수 있는 단일 ID를 제공하는 범용 ID 플랫폼을 제공합니다.

![Azure Active Directory 연결 다이어그램](media/migrating-application-authentication-to-azure-active-directory-1.jpg)

Azure AD에는 [전체 ID 관리 기능 도구 모음](../fundamentals/active-directory-whatis.md#which-features-work-in-azure-ad)이 있습니다. 앱 인증 및 권한 부여를 Azure AD로 표준화하면 이러한 기능에서 제공하는 혜택을 얻을 수 있습니다.

[https://aka.ms/migrateapps](./migration-resources.md)에서 더 많은 마이그레이션 리소스를 찾을 수 있습니다.

## <a name="benefits-of-migrating-app-authentication-to-azure-ad"></a>앱 인증을 Azure AD로 마이그레이션할 수 있는 혜택

앱 인증을 Azure AD로 이동하면 위험과 비용을 관리하고, 생산성을 높이고, 규정 준수 및 거버넌스 요구 사항을 해결하는 데 도움이 됩니다.

### <a name="manage-risk"></a>위험 관리

앱을 보호하려면 모든 위험 요인을 완벽하게 파악해야 합니다. 앱을 Azure AD로 마이그레이션하면 보안 솔루션이 통합됩니다. 이를 통해 다음을 수행할 수 있습니다.

- [조건부 액세스 정책](../conditional-access/overview.md), [Multi-Factor Authentication](../authentication/concept-mfa-howitworks.md) 및 실시간 위험 기반 [ID 보호](../identity-protection/overview-identity-protection.md) 기술을 사용하여 애플리케이션 및 연결된 회사 데이터에 대한 사용자 액세스의 보안이 향상됩니다.

- [Just-In-Time](../../azure-resource-manager/managed-applications/request-just-in-time-access.md) 관리 액세스를 사용하여 사용자 환경에 대한 권한 있는 사용자의 액세스를 보호합니다.

- 가장 중요한 비즈니스 요구 사항에 맞게 [Azure AD의 다중 테넌트, 지리적으로 분산된 고가용성 디자인](https://cloudblogs.microsoft.com/enterprisemobility/2014/09/02/azure-ad-under-the-hood-of-our-geo-redundant-highly-available-distributed-cloud-directory/)을 사용합니다.

- 이미 배포했을 수 있는 [안전한 하이브리드 액세스 파트너 통합](https://aka.ms/secure-hybrid-access) 중 하나를 사용하여 레거시 애플리케이션을 보호합니다.

### <a name="manage-cost"></a>비용 관리

조직에서 여러 IAM(ID 액세스 관리) 솔루션을 사용할 수 있습니다. 하나의 Azure AD 인프라로 마이그레이션하면 IAM 라이선스(온-프레미스 또는 클라우드)에 대한 종속성 및 인프라 비용을 줄일 수 있는 기회를 갖게 됩니다. Microsoft 365 라이선스를 통해 Azure AD의 값을 지불한 경우 다른 IAM 솔루션의 추가 비용을 지불할 이유가 없습니다.

**Azure AD를 사용하여 다음과 같은 방법으로 인프라 비용을 줄일 수 있습니다.**

- [Azure AD 애플리케이션 프록시](../app-proxy/application-proxy.md)를 사용하여 온-프레미스 애플리케이션에 대한 안전한 원격 액세스를 제공합니다.

- [신뢰할 수 있는 유니버설 ID 공급자로 Azure AD를 설정](../hybrid/plan-connect-user-signin.md#choosing-the-user-sign-in-method-for-your-organization)하여 테넌트의 온-프레미스 자격 증명 접근 방식에서 앱을 분리합니다.

### <a name="increase-productivity"></a>생산성 향상

경제성 및 보안 혜택으로 조직은 Azure AD를 도입하게 되겠지만, 사용자도 혜택을 얻을 수 있으려면 전체 도입 및 규정 준수가 될 가능성이 높습니다. Azure AD를 통해 다음을 수행할 수 있습니다.

- 모든 디바이스 및 위치에서 모든 애플리케이션에 대한 원활한 보안 액세스를 통해 최종 사용자 [SSO(Single Sign-On)](./what-is-single-sign-on.md) 환경을 개선합니다.

- [셀프 서비스 암호 재설정](../authentication/concept-sspr-howitworks.md) 및 [셀프 서비스 그룹 관리](../enterprise-users/groups-self-service-management.md)와 같은 셀프 서비스 IAM 기능을 사용합니다.

- 클라우드 및 온-프레미스 환경에서 각 사용자당 하나의 ID만을 관리하여 관리 오버헤드를 줄입니다.

  - Azure AD ID를 기반으로 [Azure AD 갤러리](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps)에서 사용자 계정 [프로비저닝 자동화](../app-provisioning/user-provisioning.md)
  - [Azure Portal](https://portal.azure.com/)에서 MyApps 패널의 모든 앱에 액세스

- 개발자가 MSAL(Microsoft 인증 라이브러리)과 함께 [Microsoft ID 플랫폼](../develop/v2-overview.md)을 사용하여 앱에 대한 액세스를 보호하고 최종 사용자 환경을 개선할 수 있습니다.

- [Azure AD B2B Collaboration](../external-identities/what-is-b2b.md)을 사용하여 클라우드 리소스에 대한 액세스 권한을 파트너에게 부여합니다. 클라우드 리소스는 파트너와 지점 간 페더레이션을 구성해야 하는 오버헤드를 제거합니다.

### <a name="address-compliance-and-governance"></a>주소 규정 준수 및 거버넌스

통합 감사 도구 및 API를 사용하여 회사 액세스 정책을 적용하고 애플리케이션 및 관련 데이터에 대한 사용자 액세스를 모니터링하여 규정 요구 사항을 준수하는지 확인합니다. Azure AD를 사용하면 [SIEM(보안 인시던트 및 이벤트 관리)](../reports-monitoring/plan-monitoring-and-reporting.md)을 사용하는 보고서를 통해 애플리케이션 로그인을 모니터링할 수 있습니다. 포털 또는 API에서 보고서에 액세스하고, 애플리케이션에 대한 액세스 권한이 있는 사용자를 프로그래밍 방식으로 감사하고, 액세스 검토를 통해 비활성 사용자의 액세스 권한을 제거할 수 있습니다.

## <a name="plan-your-migration-phases-and-project-strategy"></a>마이그레이션 단계 및 프로젝트 전략 계획

기술 프로젝트에 오류가 발생하는 경우 이는 예상과 일치하지 않거나, 올바른 관련자가 관여하지 않았거나, 통신이 부족하기 때문일 수 있습니다. 프로젝트 자체를 계획하여 성공 여부를 확인합니다.

### <a name="the-phases-of-migration"></a>마이그레이션 단계

도구를 시작하기 전에 마이그레이션 프로세스를 진행하는 방법을 이해해야 합니다. 고객과 함께 하는 몇 번의 워크샵을 통해 다음 네 가지 단계를 수행하는 것이 좋습니다.

![마이그레이션 단계 다이어그램](media/migrating-application-authentication-to-azure-active-directory-2.jpg)

### <a name="assemble-the-project-team"></a>프로젝트 팀 조합

애플리케이션 마이그레이션은 팀으로써 수행하는 작업이며 모든 중요한 직책이 채워져 있는지 확인해야 합니다. 선임 비즈니스 리더의 지원이 중요합니다. 임원 스폰서, 비즈니스 의사 결정권자 및 실무 전문가의 올바른 집합(SME)이 포함되어 있는지 확인합니다.

마이그레이션 프로젝트의 과정에서 한 사람이 조직의 규모와 구조에 따라 여러 역할을 수행하거나 하나의 역할을 여러 사용자가 수행하는 경우가 있을 수 있습니다. 또한 보안 환경에서 주요 역할을 수행하는 다른 팀에 대한 종속성이 있을 수 있습니다.

다음 표는 주요 역할 및 해당 역할의 기여를 포함합니다.

| 역할          | Contributions                                              |
| ------------- | ---------------------------------------------------------- |
| **Project Manager** | 프로젝트 코치는 다음과 같은 일을 수행하며 프로젝트를 이끕니다.<br /> - 경영진 지원 얻기<br /> - 관련자 데려오기<br /> - 일정, 설명서 및 의사소통 관리 |
| **ID 설계자/Azure AD 앱 관리자** | 이러한 작업은 다음을 담당합니다.<br /> - 관련자와 협력하여 솔루션 디자인<br /> - 운영 팀에 전달하기 위한 솔루션 디자인 및 운영 절차 문서화<br /> - 사전 프로덕션 환경 및 프로덕션 환경 관리 |
| **온-프레미스 AD 운영 팀** | AD 포리스트, LDAP 디렉터리, HR 시스템 등의 다양한 온-프레미스 ID 원본을 관리하는 조직입니다.<br /> - 동기화하기 전에 필요한 모든 수정 작업 수행<br /> - 동기화에 필요한 서비스 계정 제공<br /> -Azure AD에 대한 페더레이션을 구성하기 위한 액세스 권한 제공 |
| **IT 지원 관리자** | 고객 지원팀 관점에서 이 변경의 지원 가능성에 대한 입력을 제공할 수 있는 IT 지원 조직 출신 담당자입니다. |
| **보안 소유자**  | 계획이 조직의 보안 요구 사항을 충족하는지 확인할 수 있는 보안 팀의 담당자입니다. |
| **애플리케이션 기술 소유자** | Azure AD와 통합될 앱 및 서비스의 기술 소유자를 포함합니다. 동기화 프로세스에 포함해야 하는 애플리케이션 ID 특성을 제공합니다. 일반적으로 CSV 담당자와 관계가 있습니다. |
| **애플리케이션 비즈니스 소유자** | 사용자 환경에 대한 입력을 제공하고 사용자의 관점에서 변경 사항의 유용성을 제공할 수 있으며 전반적인 비즈니스 측면의 애플리케이션(액세스 관리 포함)을 소유하는 담당자입니다. |
| **파일럿 사용자 그룹** | 일상 작업에서 파일럿 환경을 테스트하고 나머지 배포에 방향성을 제공하는 피드백을 주는 사용자입니다. |

### <a name="plan-communications"></a>통신 계획

효과적인 비즈니스 참여 및 의사소통은 성공에 중요합니다. 관련자와 최종 사용자에게 정보를 얻고 일정 업데이트에 대한 필요한 정보를 전달하는 통로를 제공하는 것이 중요합니다. 모든 사용자에게 마이그레이션의 가치, 예상되는 타임라인의 가치 및 임시 비즈니스 중단에 대비하는 방법에 대한 것을 교육합니다. 브리핑 세션, 이메일, 일대일 모임, 배너 및 타운홀 방식과 같은 여러 가지 방법을 사용합니다.

앱을 위해 선택한 통신 전략에 따라 보류 중인 가동 중지 시간을 사용자에게 알려 줄 수 있습니다. 또한 배포를 연기하도록 하는 최근의 변경 사항이나 비즈니스 영향이 없는지 확인해야 합니다.

다음 표에서는 관련자에게 계속적으로 정보를 제공하기 위한 최소한의 의사소통을 찾아볼 수 있습니다.

**단계 및 프로젝트 전략 계획**:

| 통신      | 사용자                                          |
| ------------------ | ------------------------------------------------- |
| 인식 및 비즈니스 / 프로젝트의 기술적 가치 | 최종 사용자를 제외한 모든 사용자 |
| 파일럿 앱에 대한 요청 | - 앱 비즈니스 소유자<br />- 앱 기술 소유자<br />- 설계자 및 ID 팀 |

**1단계 - 검색 및 범위 지정**:

| 통신      | 사용자                                          |
| ------------------ | ------------------------------------------------- |
| - 애플리케이션 정보에 대한 요청<br />- 범위 지정 연습의 결과 | - 앱 기술 소유자<br />- 앱 비즈니스 소유자 |

**2단계 - 앱 분류 및 파일럿 계획**:

| 통신      | 사용자                                          |
| ------------------ | ------------------------------------------------- |
| - 분류 결과 및 마이그레이션 일정에 미치는 영향<br />- 예비 마이그레이션 일정 | - 앱 기술 소유자<br /> - 앱 비즈니스 소유자 |

**3단계 - 마이그레이션 및 테스트 계획**:

| 통신      | 사용자                                          |
| ------------------ | ------------------------------------------------- |
| - 애플리케이션 마이그레이션 테스트의 결과 | - 앱 기술 소유자<br />- 앱 비즈니스 소유자 |
| - 마이그레이션이 곧 제공된다는 알림과 결과 최종 사용자 환경에 대한 설명<br />- 예상되는 가동 중지 시간 및 완전한 의사소통(현재 수행해야 하는 작업, 사용자 의견 및 도움을 받는 방법 등 포함) | -최종 사용자(및 기타 모든 사용자) |

**4단계 – 인사이트 관리 및 얻기**:

| 통신      | 사용자                                          |
| ------------------ | ------------------------------------------------- |
| 사용 가능한 분석 및 액세스 방법 | - 앱 기술 소유자<br />- 앱 비즈니스 소유자 |

### <a name="migration-states-communication-dashboard"></a>마이그레이션 상태 의사소통 대시보드

마이그레이션 프로젝트의 전체 상태를 전달하는 것은 진행 상황을 알리는 것이므로 매우 중요하며, 앱 소유자가 마이그레이션을 준비하기 위해 앱을 출시하는 데 도움이 됩니다. Power BI 또는 다른 보고 도구를 사용하여 간단한 대시보드를 만들어서 마이그레이션하는 동안 애플리케이션의 상태를 파악할 수 있습니다.

고려할 수 있는 마이그레이션 상태는 다음과 같습니다.

| 마이그레이션 상태       | 작업 계획                                   |
| ---------------------- | --------------------------------------------- |
| **초기 요청** | 자세히 알아보기 위해 앱을 찾아 소유자에게 문의합니다. |
| **평가 완료** | 앱 소유자는 앱 요구 사항을 평가하고 앱에 대한 질문에 답변합니다.</td>
| **구성 진행 중** | Azure AD 관련 인증을 관리하는 데 필요한 변경 사항을 개발합니다. |
| **테스트 구성 성공** | 테스트 환경에서 테스트용 Azure AD 테넌트와 비교하여 변경 사항을 평가하고 앱을 인증합니다. |
| **프로덕션 구성 성공** | 프로덕션 AD 테넌트에 대해 작동하도록 구성을 변경하고 테스트 환경에서 앱 인증을 평가합니다. |
| **완료/로그오프** | 앱에 대한 변경 사항을 프로덕션 환경에 배포하고 프로덕션 Azure AD 테넌트에 대해 실행합니다. |

이렇게 하면 앱 소유자가 마이그레이션할 준비가 된 앱의 마이그레이션 및 테스트 일정을 파악할 수 있으며, 이미 마이그레이션된 다른 앱의 결과를 확인할 수 있습니다. 또한 소유자가 마이그레이션할 앱에 대한 문제를 제출하고 확인할 수 있도록 버그 추적기 데이터베이스에 대한 링크를 제공하는 것이 좋습니다.

### <a name="best-practices"></a>모범 사례

다음은 고객 및 파트너의 성공 사례와 권장 모범 사례입니다.

- [Azure Active Directory로의 마이그레이션 프로세스를 개선하는 5가지 팁](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Five-tips-to-improve-the-migration-process-to-Azure-Active/ba-p/445364), Microsoft 클라우드 솔루션을 안전하게 배포하는 데 중점을 둔 파트너 네트워크의 구성원인 Patriot Consulting에서 제공합니다.

- [Azure AD 애플리케이션 마이그레이션에 대한 위험 관리 전략을 개발](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Develop-a-risk-management-strategy-for-your-Azure-AD-application/ba-p/566488), IAM 및 위험 관리 솔루션을 중심으로 하는 파트너인 Edgile에서 제공합니다.

## <a name="phase-1-discover-and-scope-apps"></a>1단계: 앱 검색 및 범위 지정

**애플리케이션 검색 및 분석은 효율적으로 시작하기 위해 필수적인 기능입니다.** 모든 상황을 파악할 수 없으므로 알 수 없는 앱을 수용하기 위해 준비해야 합니다.

### <a name="find-your-apps"></a>앱 찾기

애플리케이션 마이그레이션에서 우선 결정해야 할 사항은 마이그레이션할 앱(남아있는 경우)과 사용 중단할 앱입니다. 조직에서 사용하지 않을 앱을 사용 중단할 수 있는 기회는 항상 있습니다. 여러 가지 방법으로 조직에서 앱을 찾을 수 있습니다. **앱을 검색하는 동안 개발 중이거나 계획된 앱을 포함하고 있는지 확인합니다. 향후 모든 앱의 인증에 Azure AD를 사용하도록 합니다.**

**AD FS(Active Directory Federation Services)를 사용하여 올바른 앱 인벤토리 수집:**

- **Azure AD Connect Health 사용.** Azure AD Premium 라이선스를 사용하는 경우 온-프레미스 환경에서의 앱 사용을 분석하기 위해 [Azure AD Connect Health](../hybrid/how-to-connect-health-adfs.md)를 배포하는 것이 좋습니다. [ADFS 애플리케이션 보고서](./migrate-adfs-application-activity.md)(미리 보기)를 사용하여 마이그레이션할 수 있는 ADFS 애플리케이션을 검색하고 마이그레이션할 애플리케이션의 준비 상태를 평가할 수 있습니다. 마이그레이션을 완료한 후에는 클라우드 내에서 조직의 Shadow IT를 지속적으로 모니터링할 수 있는 [Cloud Discovery](/cloud-app-security/set-up-cloud-discovery)를 배포합니다.

- **AD FS 로그 구문 분석**. Azure AD Premium 라이선스가 없는 경우 [PowerShell](https://github.com/AzureAD/Deployment-Plans/tree/master/ADFS%20to%20AzureAD%20App%20Migration)을 기반으로 하는 Azure AD 앱 마이그레이션 도구에 ADFS를 사용하는 것이 좋습니다. [솔루션 가이드](./migrate-adfs-apps-to-azure.md)를 참조하세요.

[AD FS(Active Directory Federation Services)에서 Azure AD로 앱 마이그레이션](./migrate-adfs-apps-to-azure.md)

### <a name="using-other-identity-providers-idps"></a>다른 IdP(ID 공급자) 사용

다른 ID 공급자(예: Okta 또는 Ping)의 경우 해당 도구를 사용하여 애플리케이션 인벤토리를 내보낼 수 있습니다. 조직의 웹앱에 해당하는 Active Directory에 등록된 서비스 원칙을 살펴보는 것이 좋습니다.

### <a name="using-cloud-discovery-tools"></a>Cloud Discovery 도구 사용

클라우드 환경에서는 모든 클라우드 서비스에서 사이버 위협을 찾아 대처하는 데 필요한 다양한 가시성, 데이터 이동에 대한 제어 및 정교한 분석이 필요합니다. 다음 도구를 사용하여 클라우드 앱 인벤토리를 수집할 수 있습니다.

- **CASB(클라우드 액세스 보안 브로커**) – [CASB](/cloud-app-security/)는 일반적으로 방화벽과 함께 작동하여 직원의 클라우드 애플리케이션 사용에 대한 가시성을 제공하고 사이버 보안 위협으로부터 회사 데이터를 보호하는 데 도움이 됩니다. CASB 보고서는 조직에서 가장 많이 사용되는 앱과 Azure AD로 마이그레이션할 초기 대상을 결정하는 데 도움이 될 수 있습니다.

- **Cloud Discovery** - [Cloud Discovery](/cloud-app-security/set-up-cloud-discovery)를 구성하여 클라우드 앱 사용에 대한 가시성을 얻고 비사용 권한 앱 또는 Shadow IT 앱을 검색할 수 있습니다.

- **API** - 클라우드 인프라에 연결된 앱의 경우 해당 시스템의 API 및 도구를 사용하여 호스팅된 앱의 인벤토리를 시작할 수 있습니다. Azure 환경에서:

  - Azure 웹 사이트에 대한 정보를 가져오려면 [Get-AzureWebsite](/powershell/module/servicemanagement/azure.service/get-azurewebsite) cmdlet을 사용합니다.

  - Azure 웹앱에 대한 정보를 가져오려면 [Get-AzureRMWebApp](/powershell/module/azurerm.websites/get-azurermwebapp) cmdlet을 사용합니다.
D
  - [AppCmd.exe](/iis/get-started/getting-started-with-iis/getting-started-with-appcmdexe#working-with-sites-applications-virtual-directories-and-application-pools)를 사용하여 Windows 명령줄에서 Microsoft IIS에서 실행되는 모든 앱을 찾을 수 있습니다.

  - [애플리케이션](/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference#application-entity) 및 [서비스 주체](/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference#serviceprincipal-entity)를 사용하여 Azure AD의 디렉터리에 있는 앱 및 앱 인스턴스에 대한 정보를 얻습니다.

### <a name="using-manual-processes"></a>수동 프로세스 사용

위에서 설명한 자동화된 방법을 수행하면 애플리케이션을 잘 이해하고 다룰 수 있게 될 것입니다. 그러나 모든 사용자 액세스 영역으로 적용 범위를 넓히려면 다음을 수행하는 것이 좋을 수 있습니다.

- 조직의 여러 비즈니스 소유자에게 문의하여 조직에서 사용 중인 애플리케이션을 찾습니다.

- 프록시 서버에서 HTTP 검사 도구를 실행하거나 프록시 로그를 분석하여 트래픽이 일반적으로 라우팅되는 위치를 확인합니다.

- 자주 사용하는 회사 포털 사이트의 웹 로그를 검토하여 사용자가 가장 많이 액세스하는 링크를 확인합니다.

- 임원 또는 다른 주요 비즈니스 구성원에게 연락하여 업무상 중요한 앱을 파악했는지 확인합니다.

### <a name="type-of-apps-to-migrate"></a>마이그레이션할 앱 유형

앱을 찾았으면 조직에서 다음과 같은 유형의 앱을 식별합니다.

- 최신 인증 프로토콜을 이미 사용하는 앱

- 현대화하도록 선택한 레거시 인증 프로토콜을 사용하는 앱

- 현대화하지 않도록 선택한 레거시 인증 프로토콜을 사용하는 앱

- 새 LOB(기간 업무) 앱

### <a name="apps-that-use-modern-authentication-already"></a>이미 최신 인증을 사용하는 앱

이미 현대화된 앱은 Azure AD로 이동될 가능성이 높습니다. 이러한 앱은 이미 최신 인증 프로토콜(예: SAML 또는 OpenID Connect)을 사용하며 Azure AD를 통해 인증하도록 다시 구성할 수 있습니다.

[Azure AD 앱 갤러리](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps)에서의 선택 사항 외에도 이러한 앱은 이미 사용자 조직에 있거나 Azure AD 갤러리([비갤러리 애플리케이션)](./add-application-portal.md)의 일부가 아닌 공급 업체의 또는 타사 앱에 있는 앱일 수 있습니다.

현대화하도록 선택한 레거시 앱

현대화하려는 레거시 앱의 경우 핵심 인증 및 권한 부여에 대해 Azure AD로 이동하면 [Microsoft Graph](https://developer.microsoft.com/graph/gallery/?filterBy=Samples,SDKs) 및 [Intelligent Security Graph](https://www.microsoft.com/security/operations/intelligence?rtc=1)가 제공하는 모든 기능과 데이터에 액세스할 수 있습니다.

이러한 애플리케이션에 대해서는 레거시 프로토콜(예: Windows 통합 인증, Kerberos 제한 위임, HTTP 헤더 기반 인증)에서 최신 프로토콜(예: SAML 또는 OpenID Connect)로 **인증 스택 코드를 업데이트** 하는 것이 좋습니다.

### <a name="legacy-apps-that-you-choose-not-to-modernize"></a>현대화하지 않도록 선택한 레거시 앱

레거시 인증 프로토콜을 사용하는 특정 앱의 경우 비즈니스상의 이유로 현대화하지 않는 것이 좋은 경우도 있습니다. 다음과 같은 종류의 앱이 포함됩니다.

- 규정 준수 또는 제어 관련 이유로 온-프레미스에 유지되는 앱

- 변경하지 않으려는 온-프레미스 ID 또는 페더레이션 공급자에 연결된 앱

- 이동할 계획이 없이 온-프레미스 인증 표준을 사용하여 개발된 앱

Azure AD는 이러한 앱에 대해 앱을 전혀 건드리지 않고 최신 Azure AD 보안 및 거버넌스 기능(예: [Multi-Factor Authentication](../authentication/concept-mfa-howitworks.md), [조건부 액세스](../conditional-access/overview.md), [ID 보호](../identity-protection/index.yml), [위임된 애플리케이션 액세스](./access-panel-manage-self-service-access.md), [액세스 검토](../governance/manage-user-access-with-access-reviews.md#create-and-perform-an-access-review))을 사용하도록 할 수 있으므로 레거시 앱에 뛰어난 혜택을 제공할 수 있습니다.

사용자가 신속하게 마이그레이션되도록 간단한 인증(예: Password Vaulting) 방법을 사용하는 Azure AD [애플리케이션 프록시](../app-proxy/application-proxy-configure-single-sign-on-password-vaulting.md) 또는 이미 배포했을 수 있는 애플리케이션 배달 컨트롤러와 함께 [파트너 통합](https://azure.microsoft.com/services/active-directory/sso/secure-hybrid-access/)을 통해 **이러한 앱을 클라우드로 확장** 하여 시작합니다.

### <a name="new-line-of-business-lob-apps"></a>새 LOB(기간 업무) 앱

일반적으로 조직은 사내에서 사용하기 위한 LOB(기간 업무) 앱을 개발합니다. 파이프라인에 새 앱이 있는 경우 [Microsoft ID 플랫폼](../develop/v2-overview.md)을 사용하여 OpenID Connect를 구현하는 것이 좋습니다.

### <a name="apps-to-deprecate"></a>사용 중단할 앱

명확한 소유자가 없고 유지 관리 및 모니터링을 명확하게 수행하지 않는 앱은 조직에 보안 위험을 초래합니다. 다음 경우에는 애플리케이션 사용 중단을 고려합니다.

- 앱의 **기능이 다른 시스템과 매우 중복되거나** **비즈니스 소유자가 없는 경우**

- **사용량이 전혀 없는 경우**

**많은 영향을 미치는 중요 비즈니스용 애플리케이션은 사용 중단하지 않는 것이 좋습니다**. 이러한 경우 비즈니스 소유자에게 문의하여 올바른 전략을 세워야 합니다.

### <a name="exit-criteria"></a>종료 기준

이 단계에서는 다음을 통해 성공적으로 수행합니다.

- 마이그레이션 범위의 시스템에 대한 충분한 이해(Azure AD로 이동한 후에는 사용 중지할 수 있음).

- 다음을 포함하는 앱 목록:

  - 이러한 앱이 연결되는 시스템
  - 사용자가 이러한 앱을 액세스하는 위치 및 디바이스
  - 이러한 앱이 마이그레이션되는지, 사용되지 않는지, [Azure AD Connect](../hybrid/whatis-azure-ad-connect.md)에 연결되었는지 여부.

> [!NOTE]
> [애플리케이션 검색 워크시트](https://download.microsoft.com/download/2/8/3/283F995C-5169-43A0-B81D-B0ED539FB3DD/Application%20Discovery%20worksheet.xlsx)를 다운로드하여 Azure AD 인증으로 마이그레이션하려는 애플리케이션 및 [Azure AD Connect](../hybrid/whatis-azure-ad-connect.md)를 사용하여 관리하려는 애플리케이션을 기록할 수 있습니다.

## <a name="phase-2-classify-apps-and-plan-pilot"></a>2단계: 앱 분류 및 파일럿 계획

앱의 마이그레이션을 분류하는 것은 중요합니다. 모든 앱을 동시에 마이그레이션하고 전환해야 하는 것은 아닙니다. 각 앱에 대한 정보를 수집한 후에는 먼저 마이그레이션해야 하는 앱을 합리적으로 정할 수 있습니다.

### <a name="classify-in-scope-apps"></a>범위 내 앱 분류

각각 여러 요소에 따라 달라지는 비즈니스의 중요도, 사용량 및 수명의 축에 따라 고려해 보는 것도 방법입니다.

### <a name="business-criticality"></a>비즈니스 중요성

비즈니스 중요도는 각 비즈니스에 대해 서로 다른 차원을 사용하지만 고려해야 할 두 가지 기준은 **기능** 및 **사용자 프로필** 입니다. 중복되거나 사용되지 않는 기능보다 고유한 기능이 있는 앱을 할당합니다.

![기능 및 사용자 프로필에 대한 스펙트럼 다이어그램](media/migrating-application-authentication-to-azure-active-directory-3.jpg)

### <a name="usage"></a>사용량

**사용량이 많은** 애플리케이션은 사용량이 적은 앱보다 높은 값을 받아야 합니다. 외부, 임원 또는 보안 팀 사용자가 사용하는 앱에 더 높은 값 할당합니다. 마이그레이션 포트폴리오의 각 앱에 대해 이러한 평가를 완료합니다.

![사용자 볼륨 및 사용자 범위에 대한 스펙트럼 다이어그램](media/migrating-application-authentication-to-azure-active-directory-4.jpg)

비즈니스 중요도 및 사용에 대한 값을 결정한 후에는 **애플리케이션 수명** 을 결정하고 우선 순위 행렬을 만들 수 있습니다. 아래 행렬을 참조하세요.

![사용량, 예상되는 수명 및 비즈니스 중요도 간의 관계를 보여 주는 삼각형 다이어그램](media/migrating-application-authentication-to-azure-active-directory5.jpg)

### <a name="prioritize-apps-for-migration"></a>마이그레이션을 위해 앱의 우선 순위 지정

조직의 요구에 따른 우선 순위 순서대로 또는 역순으로 앱 마이그레이션을 시작하도록 선택할 수 있습니다.

Azure AD 및 ID 서비스를 사용한 경험이 없는 경우에는 **우선 순위가 가장 낮은 앱** 을 먼저 Azure AD로 이동하는 것이 좋습니다. 이렇게 하면 비즈니스 영향이 최소화되고 모멘텀을 빌드할 수 있습니다. 이러한 앱을 성공적으로 이동하고 관련자의 확신을 얻은 후에는 다른 앱을 계속 마이그레이션할 수 있습니다.

명확한 우선 순위가 없는 경우 [Azure AD 갤러리](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps)에 있는 앱을 먼저 이동하고 쉽게 통합할 수 있는 여러 ID 공급자(ADFS 또는 Okta)를 지원하는 것이 좋습니다. 이러한 앱은 조직에서 **우선 순위가 가장 높은 앱** 이 될 가능성이 있습니다. Azure AD와 SaaS 애플리케이션을 통합할 수 있도록 구성 과정을 안내하는 [자습서](../saas-apps/tutorial-list.md) 모음을 개발했습니다.

앱을 마이그레이션해야 하는 최종 기한이 있는 경우 우선 순위가 가장 높은 앱 버킷이 대부분의 워크로드를 수행합니다. 최종 기한을 이동한 경우에도 비용은 변경되지 않으므로 우선 순위가 낮은 앱을 선택할 수 있습니다. 라이선스를 갱신해야 하는 경우에도 적은 비용이 듭니다.

이 분류 외에도 마이그레이션의 긴급도에 따라 앱 소유자가 앱을 마이그레이션하기 위해 참여해야 하는 **마이그레이션 일정** 을 설정하는 것을 고려할 수 있습니다. 이 프로세스가 끝나면 마이그레이션에 대해 우선 순위가 지정된 버킷에 모든 애플리케이션의 목록이 표시됩니다.

### <a name="document-your-apps"></a>앱 문서화

먼저 애플리케이션에 대한 주요 정보를 수집하여 시작합니다. [애플리케이션 검색 워크시트](https://download.microsoft.com/download/2/8/3/283F995C-5169-43A0-B81D-B0ED539FB3DD/Application%20Discovery%20worksheet.xlsx)는 마이그레이션 결정을 신속하게 수행하고 비즈니스 그룹에 권장 사항을 신속히 제공하는 데 도움이 됩니다.

마이그레이션 결정을 내리는 데 있어 중요한 정보에는 다음이 포함됩니다.

- **앱 이름** – 이 앱은 비즈니스상으로 어떻게 알려져 있나요?

- **앱 유형** – 이 앱은 타사 SaaS 앱인가요? 사용자 지정 LOB(기간 업무) 웹앱인가요? API인가요?

- **비즈니스 중요도** – 높은 중요도의 앱인가요? 낮은 중요도의 앱인가요? 아니면 중간 정도 중요도의 앱인가요?

- **사용자 액세스 볼륨** - 이 앱은 모든 사용자가 액세스하나요? 아니면 몇 명의 사용자만 액세스하나요?

- **예상 수명** – 이 앱은 수명이 얼마나 길까요? 6개월 미만일까요? 2년 이상일까요?

- **현재 ID 공급자** – 이 앱에 대한 기본 IdP는 어떻게 되나요? 또는 로컬 스토리지를 사용하나요?

- **인증 방법** – 앱이 오픈 표준을 사용하여 인증하나요?

- **앱 코드 업데이트 계획 여부** – 앱 개발이 계획되어 있거나 현재 개발 중인가요?

- **앱을 온-프레미스로 유지할지 여부** – 데이터 센터에 앱을 장기간 유지하려고 하나요?

- **앱이 다른 앱 또는 API에 종속되는지 여부** – 앱이 현재 다른 앱 또는 API를 호출하나요?

- **앱이 Azure AD 갤러리에 있는지 여부** – 현재 앱이 [Azure AD 갤러리](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps)와 이미 통합되어 있나요?

나중에 도움이 되지만 즉각적인 마이그레이션 결정을 내리는 데 필요하지 않은 기타 데이터에는 다음이 포함됩니다.

- **앱 URL** – 사용자가 앱에 액세스하려면 어떻게 해야 하나요?

- **앱 설명** – 앱의 용도에 대한 간단한 설명은 어떻게 할 수 있을까요?

- **앱 소유자** – 비즈니스에서 앱에 대한 기본 POC인 사람은 누구인가요?

- **일반 설명 또는 메모** – 앱 또는 비즈니스 소유권에 대한 기타 일반 정보

애플리케이션을 분류하고 세부 정보를 문서화한 후에는 계획된 마이그레이션 전략에 대한 비즈니스 소유자의 구매를 확보해야 합니다.

### <a name="plan-a-pilot"></a>파일럿 계획

파일럿에 선택하는 앱은 조직의 주요 ID 및 보안 요구 사항을 나타내야 하며 애플리케이션 소유자의 구매가 명확하게 있어야 합니다. 파일럿은 일반적으로 별도의 테스트 환경에서 실행됩니다. 배포 계획 페이지의 [파일럿 모범 사례](../fundamentals/active-directory-deployment-plans.md#best-practices-for-a-pilot)를 참조하세요.

**외부 파트너를 잊지 마세요.** 그들이 마이그레이션 일정 및 테스트에 참여하는지 확인합니다. 마지막으로, 중단 문제가 있는 경우 기술 지원팀에 액세스할 수 있는 방법이 제공되는지 확인합니다.

### <a name="plan-for-limitations"></a>제한 사항에 대비한 계획

일부 앱은 쉽게 마이그레이션할 수 있지만 다른 앱은 여러 서버나 인스턴스로 인해 더 오래 걸릴 수 있습니다. 예를 들어 사용자 지정 로그인 페이지 때문에 SharePoint 마이그레이션이 더 오래 걸릴 수 있습니다.

많은 SaaS 앱 공급업체는 SSO 연결 변경에 대한 요금을 청구합니다. 해당 항목을 확인하고 이에 대비한 계획을 세워야 합니다.

또한 Azure AD에는 알고 있어야 하는 [서비스 제한 및 제한 사항](../enterprise-users/directory-service-limits-restrictions.md)이 있습니다.

### <a name="app-owner-sign-off"></a>앱 소유자 로그오프

널리 사용되는 중요 비즈니스용 애플리케이션은 파일럿 단계에서 앱을 테스트하기 위한 파일럿 사용자 그룹이 필요할 수 있습니다. 사전 프로덕션 또는 파일럿 환경에서 앱을 테스트한 후에는 앱을 마이그레이션하기 전에 앱 비즈니스 소유자가 로그오프하고 인증을 위해 Azure AD를 프로덕션으로 사용하도록 해야 합니다.

### <a name="plan-the-security-posture"></a>보안 상태 계획

마이그레이션 프로세스를 시작하기 전에 회사 ID 시스템에 대해 개발하려는 보안 환경을 완벽하게 고려하는 것이 좋습니다. 이는 **데이터에 액세스하는 ID, 디바이스 및 위치** 와 같은 중요한 정보 집합을 수집하는 것을 기반으로 합니다.

### <a name="identities-and-data"></a>ID 및 데이터

대부분의 조직에는 산업 부문 및 조직 내의 직무 기능에 따라 달라지는 ID 및 데이터 보호에 대한 특정 요구 사항이 있습니다. 지정된 [조건부 액세스 정책](../conditional-access/overview.md) 집합 및 관련 기능을 비롯한 권장 사항은 [ID 및 디바이스 액세스 구성](/microsoft-365/enterprise/microsoft-365-policies-configurations)을 참조하세요.

이 정보를 사용하여 Azure AD와 통합된 모든 서비스에 대한 액세스를 보호할 수 있습니다. 이러한 권장 사항은 [Azure AD의 Microsoft Secure Score](../fundamentals/identity-secure-score.md) 및 ID 점수와 함께 정렬됩니다. 점수는 다음에 도움이 됩니다.

- ID 보안 상태를 객관적으로 측정

- ID 보안 개선 계획

- 개선 계획의 성공 여부 검토

또한 이를 통해 [ID 인프라를 보호하는 5단계](../../security/fundamentals/steps-secure-identity.md)를 구현할 수도 있습니다. 조직의 특정한 요구 사항에 맞게 지침을 시작 지점으로 사용하고 정책을 조정합니다.

### <a name="who-is-accessing-your-data"></a>데이터를 누가 액세스하고 있나요?

Azure AD에서 지원하는 앱 및 리소스에는 다음 두 가지 주요 범주가 있습니다.

- **내부:** ID 공급자 내에 계정이 있는 직원, 계약자 및 공급 업체. 관리자나 리더십은 다른 직원에 대해 다른 규칙을 사용하며 추가 피벗이 필요할 수 있습니다.

- **외부:** [Azure AD B2B Collaboration](../external-identities/what-is-b2b.md)을 사용하여 일반적인 비즈니스 과정에서 조직과 상호 작용하는 공급 업체, 공급자, 배포자 또는 기타 비즈니스 파트너입니다.

이러한 사용자에 대한 그룹을 정의하고 이러한 그룹을 다양한 방식으로 채울 수 있습니다. 관리자가 구성원을 수동으로 그룹에 추가하도록 선택하거나 셀프 서비스 그룹 구성원을 사용하도록 설정할 수 있습니다. [동적 그룹](../enterprise-users/groups-dynamic-membership.md)을 사용하여 지정된 조건에 따라 그룹에 구성원을 자동으로 추가하는 규칙을 설정할 수 있습니다.

외부 사용자는 고객을 참조할 수도 있습니다. [Azure AD B2C](../../active-directory-b2c/overview.md), 별도의 제품은 고객 인증을 지원합니다. 그러나 이 내용은 이 문서가 다루는 범위를 벗어납니다.

### <a name="devicelocation-used-to-access-data"></a>데이터에 액세스하는 데 사용되는 디바이스/위치

사용자가 앱에 액세스하는 데 사용하는 디바이스 및 위치도 중요합니다. 회사 네트워크에 물리적으로 연결된 디바이스는 더 안전 합니다. VPN을 통해 네트워크 외부에서 연결하는 경우에는 정밀한 검사가 필요할 수 있습니다.

![사용자 위치와 데이터 액세스 간의 관계를 보여 주는 다이어그램](media/migrating-application-authentication-to-azure-active-directory-6.jpg)

이러한 측면에서 리소스, 사용자 및 디바이스를 고려하여 [Azure AD 조건부 액세스](../conditional-access/overview.md) 기능을 사용하도록 선택할 수 있습니다. 조건부 액세스는 사용자 권한을 초과함: 이는 사용자 또는 그룹의 ID, 사용자가 연결된 네트워크, 사용 중인 디바이스 및 애플리케이션, 액세스하려는 데이터 형식 등의 요소 조합을 기반으로 합니다. 사용자에게 부여된 액세스는 보다 광범위한 조건 집합에 적응합니다.

### <a name="exit-criteria"></a>종료 기준

이 단계에서는 다음과 같은 작업을 수행합니다.

- 앱 파악
  - 마이그레이션하려는 앱을 완벽하게 문서화함
  - 비즈니스 중요도, 사용 볼륨 및 수명에 따라 우선 순위가 지정된 앱 보유

- 파일럿에 대한 요구 사항을 나타내는 앱을 선택함

- 우선 순위 및 전략에 따라 비즈니스 소유자의 구매

- 보안 상태에 대한 요구 사항 및 구현 방법 이해

## <a name="phase-3-plan-migration-and-testing"></a>3단계: 마이그레이션 및 테스트 계획

비즈니스 구매를 얻은 이후 다음 단계는 이러한 앱을 Azure AD 인증으로 마이그레이션하기 시작하는 것입니다.

### <a name="migration-tools-and-guidance"></a>마이그레이션 도구 및 지침

아래 도구와 지침을 사용하여 애플리케이션을 Azure AD로 마이그레이션하는 데 필요한 정확한 단계를 따르세요.

- **일반 마이그레이션 지침** – [Azure AD 앱 마이그레이션 도구 키트](./migration-resources.md)의 백서, 도구, 이메일 템플릿 및 애플리케이션 질문을 사용하여 앱을 검색, 분류 및 마이그레이션합니다.

- **Saas 애플리케이션** – 엔드투엔드 프로세스를 안내하는 [수백 개의 SaaS 앱 자습서](../saas-apps/tutorial-list.md) 및 전체 [Azure AD SSO 배포 계획](https://aka.ms/ssodeploymentplan) 목록을 참조하세요.

- **온-프레미스에서 실행되는 애플리케이션** - [Azure AD 애플리케이션 프록시](../app-proxy/application-proxy.md)에 대해 알아보고 전체 [Azure AD 애플리케이션 프록시 배포 계획](https://aka.ms/AppProxyDPDownload)을 사용하여 빠르게 진행하세요.

- **개발 중인 앱** – 단계별 [통합](../develop/quickstart-register-app.md) 및 [등록](../develop/quickstart-register-app.md) 지침을 읽어 보세요.

마이그레이션 후 성공적인 배포를 사용자에게 알리는 통신을 보내도록 선택하고 수행해야 하는 새로운 단계에 대해 알릴 수 있습니다.

### <a name="plan-testing"></a>테스트 계획

마이그레이션을 진행하는 동안 앱은 정기 배포 중에 사용되는 테스트 환경을 이미 사용하고 있을 수 있습니다. 마이그레이션 테스트에 이 환경을 계속 사용할 수 있습니다. 테스트 환경을 현재 사용할 수 없는 경우 애플리케이션의 아키텍처에 따라 Azure App Service 또는 Azure Virtual Machines를 사용하여 설정할 수 있습니다. 앱 구성을 개발할 때 사용할 개별 테스트 Azure AD 테넌트를 설정하도록 선택할 수 있습니다. 이 테넌트는 정리된 상태로 시작되며 어떤 시스템과도 동기화하도록 구성되지 않습니다.

테스트 사용자로 로그인하여 각 애플리케이션을 테스트하고 모든 기능이 마이그레이션 이전과 동일한지 확인합니다. 테스트 중에 사용자가 [MFA](../authentication/howto-mfa-userstates.md) 또는 [SSPR](../authentication/tutorial-enable-sspr.md) 설정을 업데이트해야 하는지 또는 마이그레이션 중에 이 기능을 추가해야 하는지 확인하는 경우 최종 사용자 통신 계획에 이 기능을 추가해야 합니다. [MFA](https://aka.ms/mfatemplates) 및 [SSPR](https://aka.ms/ssprtemplates) 최종 사용자 통신 템플릿을 참조하세요.

앱을 마이그레이션한 후에는 [Azure Portal](https://aad.portal.azure.com/)로 이동하여 마이그레이션이 성공했는지 테스트합니다. 아래의 지침을 따르세요.

- **엔터프라이즈 애플리케이션 &gt; 모든 애플리케이션** 을 선택하고 목록에서 앱을 찾습니다.

- **관리 &gt; 사용자 및 그룹** 을 선택하여 앱에 하나 이상의 사용자 또는 그룹을 할당합니다.

- **관리 &gt; 조건부 액세스** 를 선택합니다. 정책 목록을 검토하고 [조건부 액세스 정책](../conditional-access/overview.md)을 사용하여 애플리케이션에 대한 액세스를 차단하고 있지 않는지 확인합니다.

앱을 구성하는 방법에 따라 SSO가 제대로 작동하는지 확인합니다.

| 인증 유형      | 테스트                                             |
| ------------------------ | --------------------------------------------------- |
| **OAuth / OpenID Connect** | **엔터프라이즈 애플리케이션&gt;권한** 을 선택하고 앱의 사용자 설정에서 애플리케이션에 동의했는지 확인합니다. |
| **SAML 기반 SSO** | **Single Sign-On** 에 있는 [SAML 설정 테스트](./debug-saml-sso-issues.md) 단추를 사용합니다. |
| **암호 기반 SSO** | [MyApps 보안 로그인 확장](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension)을 다운로드하고 설치합니다. 이 확장을 사용하면 SSO 프로세스를 사용해야 하는 조직의 클라우드 앱을 시작할 수 있습니다. |

|  **[애플리케이션 프록시](../app-proxy/application-proxy.md)** | 커넥터가 실행 중이고 애플리케이션에 할당되었는지 확인합니다. 자세한 내용은 [애플리케이션 프록시 문제 해결 가이드](../app-proxy/application-proxy-troubleshoot.md)를 참조하세요. |

### <a name="troubleshoot"></a>문제 해결

문제가 발생하면 [앱 문제 해결 가이드](../app-provisioning/isv-automatic-provisioning-multi-tenant-apps.md)를 확인하여 도움을 받으세요. 또한 문제 해결 문서를 확인하고 [SAML 기반 Single Sign-On이 구성된 앱에 로그인 관련 문제](/troubleshoot/azure/active-directory/troubleshoot-sign-in-saml-based-apps)를 확인할 수 있습니다.

### <a name="plan-rollback"></a>롤백 계획

마이그레이션이 실패하는 경우 가장 좋은 전략은 롤백하고 테스트하는 것입니다. 마이그레이션 문제를 완화하기 위해 수행할 수 있는 단계는 다음과 같습니다.

- 앱의 기존 구성에 대한 **스크린샷을 찍습니다**. 앱을 다시 구성해야 하는 경우 다시 확인할 수 있습니다.

- 또한 클라우드 인증에 문제가 있는 경우 **레거시 인증에 대한 링크를 제공** 하는 것을 고려할 수도 있습니다.

- 마이그레이션을 완료하기 전에 이전 ID 공급자를 사용하여 **기존 구성을 변경하지 않도록 합니다**.

- 먼저 **여러 IdP를 지원하는 앱** 을 마이그레이션합니다. 문제가 발생하는 경우 항상 기본 IdP의 구성으로 변경할 수 있습니다.

- 앱 환경에 **피드백 단추** 또는 **기술 지원팀** 문제에 대한 경로가 있는지 확인합니다.

### <a name="exit-criteria"></a>종료 기준

이 단계에서는 다음을 성공적으로 수행합니다.

- 각 앱을 마이그레이션하는 방법 결정

- 마이그레이션 도구 검토

- 테스트 환경 및 그룹을 포함하여 테스트 계획

- 계획된 롤백

## <a name="phase-4-plan-management-and-insights"></a>4단계: 관리 및 인사이트 계획

앱이 마이그레이션된 후 다음을 확인해야 합니다.

- 사용자가 안전하게 액세스하고 관리할 수 있음

- 사용량 및 앱 상태에 대한 적절한 인사이트를 얻을 수 있습니다.

다음 작업을 조직에 적절하게 수행하는 것이 좋습니다.

### <a name="manage-your-users-app-access"></a>사용자의 앱 액세스 관리

앱을 마이그레이션한 후에는 다양한 방식으로 사용자의 경험을 보강할 수 있습니다.

**검색할 수 있는 앱 만들기**

**사용자에게 [MyApps](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension) 포털 환경을 지정합니다**. 여기에서 모든 클라우드 기반 앱, [Azure AD Connect](../hybrid/whatis-azure-ad-connect.md)를 사용하여 사용 가능하도록 설정한 앱 및 [애플리케이션 프록시](../app-proxy/application-proxy.md)를 사용하는 앱에 액세스할 수 있는 권한이 제공된 앱에 액세스할 수 있습니다.

사용자에게 앱을 검색하는 방법에 대해 안내할 수 있습니다.

- [기존 Single Sign-On](./view-applications-portal.md) 기능을 사용하여 **사용자를 앱에 연결**

- 앱에 대한 [셀프 서비스 애플리케이션 액세스](./manage-self-service-access.md)를 사용하도록 설정하고 **사용자가 큐레이팅한 앱을 추가하도록 함**

- [최종 사용자의 애플리케이션](./hide-application-from-user-portal.md)(기본 Microsoft 앱 또는 다른 앱)을 숨겨 **더 필요한 앱을 더 발견하기 쉽도록 함**

### <a name="make-apps-accessible"></a>앱에 액세스할 수 있도록 설정

**사용자가 모바일 디바이스에서 앱에 액세스할 수 있도록** 합니다. 사용자는 [iOS](./hide-application-from-user-portal.md) 7.0 이상 또는 [Android](./hide-application-from-user-portal.md) 디바이스에서 Intune 관리 브라우저를 사용하여 MyApps 포털에 액세스할 수 있습니다.

사용자는 **Intune에서 관리되는 브라우저** 를 다운로드할 수 있습니다.

- **Android 디바이스의 경우** [Google play 스토어](https://play.google.com/store/apps/details?id=com.microsoft.intune)에서

- **Apple 디바이스의 경우** [Apple 앱 스토어](https://apps.apple.com/us/app/intune-company-portal/id719171358)에서 다운로드하거나 [iOS용 My Apps 모바일 앱](https://appadvice.com/app/my-apps-azure-active-directory/824048653)을 다운로드할 수 있습니다.

**사용자가 브라우저 확장에서 앱을 열 수 있도록 합니다.**

사용자는 [Chrome,](https://chrome.google.com/webstore/detail/my-apps-secure-sign-in-ex/ggjhpefgjjfobnfoldnjipclpcfbgbhl) [FireFox](https://addons.mozilla.org/firefox/addon/access-panel-extension/) 또는 [Microsoft Edge](https://www.microsoft.com/p/my-apps-secure-sign-in-extension/9pc9sckkzk84?rtc=1&activetab=pivot%3Aoverviewtab)에서 [MyApps 보안 로그인 확장을 다운로드](https://www.microsoft.com/p/my-apps-secure-sign-in-extension/9pc9sckkzk84?rtc=1&activetab=pivot%3Aoverviewtab)할 수 있으며 브라우저 표시줄에서 바로 앱을 시작할 수 있습니다.

- **앱을 검색하고 가장 최근에 사용된 앱을 표시하도록 합니다.**

- [애플리케이션 프록시에서](../app-proxy/application-proxy.md) 구성한 **내부 URL을 적절한 외부 URL로 자동으로 변환** 합니다. 이제 사용자는 어디에 있든 관계 없이 익숙한 링크를 사용할 수 있습니다.

**사용자가 Office.com에서 앱을 열 수 있도록 합니다.**

사용자는 [Office.com](https://www.office.com/)으로 이동하여 **앱을 검색하고 가장 최근에 사용한 앱** 이 실제 작동하는 위치에서 바로 표시되도록 할 수 있습니다.

### <a name="secure-app-access"></a>보안 앱 액세스

Azure AD는 마이그레이션된 앱을 관리하는 중앙 액세스 위치를 제공합니다. [Azure Portal](https://portal.azure.com/)로 이동하고 다음 기능을 사용하도록 설정합니다.

- **앱에 대한 사용자 액세스를 보호합니다.** 디바이스 상태, 위치 등을 기준으로 애플리케이션에 대한 사용자 액세스를 보호하기 위해 [조건부 액세스 정책](../conditional-access/overview.md) 또는 [ID 보호](../identity-protection/overview-identity-protection.md)를 사용하도록 설정합니다.

- **자동 프로비저닝** 사용자가 액세스해야 하는 다양한 타사 SaaS 앱을 사용하여 [사용자의 자동 프로비저닝](../app-provisioning/user-provisioning.md)을 설정합니다. 사용자 ID를 생성하는 것 외에도 상태 또는 역할이 변경될 때의 사용자 ID의 유지 관리 및 제거가 포함됩니다.

- **사용자 액세스** **관리** 를 위임합니다. 앱에 대한 셀프 서비스 애플리케이션 액세스를 적절히 사용하도록 설정하고 *비즈니스 승인자를 할당하여 해당 앱에 대한 액세스를 승인합니다*. 앱 컬렉션에 할당된 그룹에 대해 [셀프 서비스 그룹 관리](../enterprise-users/groups-self-service-management.md)를 사용합니다.

- **관리 액세스를 위임합니다.** **디렉터리 역할** 을 사용하여 관리자 역할(예: 애플리케이션 관리자, 클라우드 애플리케이션 관리자 또는 애플리케이션 개발자)을 사용자에게 할당합니다.

### <a name="audit-and-gain-insights-of-your-apps"></a>앱 감사 및 인사이트 얻기

[Azure Portal](https://portal.azure.com/)을 사용해서도 중앙 위치에서 모든 앱을 감사할 수 있습니다.

- **엔터프라이즈 애플리케이션, Audit** 을 사용하여 **앱을 감사** 하거나, [Azure AD Reporting API](../reports-monitoring/concept-reporting-api.md)에서 동일한 정보에 액세스하여 자주 사용하는 도구에 통합합니다.

- OAuth / OpenID Connect를 사용하는 앱에 대한 **엔터프라이즈 애플리케이션, 사용 권한** 를 사용하여 **앱에 대한 사용 권한을 확인합니다**.

- **엔터프라이즈 애플리케이션, 로그인** 을 사용하여 **로그인 인사이트를 가져옵니다**. [Azure AD Reporting API](../reports-monitoring/concept-reporting-api.md)에서 동일한 정보에 액세스합니다.

- [Azure AD Power BI 콘텐츠 팩](../reports-monitoring/howto-use-azure-monitor-workbooks.md)에서 **앱의 사용량 시각화**

### <a name="exit-criteria"></a>종료 기준

이 단계에서는 다음과 같은 작업을 수행합니다.

- 사용자에게 보안 앱 액세스 제공

- 마이그레이션된 앱을 감사하고 인사이트를 얻기 위한 관리

### <a name="do-even-more-with-deployment-plans"></a>배포 계획을 사용하여 더 많은 작업 수행

배포 계획은 앱 마이그레이션 시나리오를 비롯하여 Azure AD 솔루션의 비즈니스 가치, 계획, 구현 단계 및 관리를 안내합니다. Azure AD 기능에서 가치를 창출하고 배포를 시작하기 위해 필요한 모든 것을 함께 제공합니다. 배포 가이드에는 Microsoft 권장 모범 사례, 최종 사용자 통신, 계획 가이드, 구현 단계, 테스트 사례 등의 콘텐츠가 포함됩니다.

수많은 [배포 계획](../fundamentals/active-directory-deployment-plans.md)을 사용할 수 있으며, 점점 더 다양해질 것입니다.

### <a name="contact-support"></a>지원에 문의

지원 티켓을 만들거나 추적하고 상태를 모니터링하려면 다음 지원 링크를 방문하세요.

- **Azure 지원:** [Microsoft 지원](https://azure.microsoft.com/support)을 호출하고 모든 Azure에 대한 티켓을 열 수 있습니다.

Microsoft의 기업계약에 따른 ID 배포 문제입니다.

- **Fasttrack**: EMS(Enterprise Mobility and Security) 또는 Azure AD Premium 라이선스를 구매한 경우 [FastTrack 프로그램](/enterprise-mobility-security/solutions/enterprise-mobility-fasttrack-program)에서 배포 지원을 받을 수 있습니다.

- **제품 엔지니어링 팀 참여:** 수백만 명의 사용자가 있는 주요 고객 배포에서 작업하는 경우 Microsoft 계정 팀 또는 클라우드 솔루션 아키텍트의 지원을 받을 수 있습니다. 프로젝트의 배포 복잡도에 따라 [Azure ID 제품 엔지니어링 팀](https://aad.portal.azure.com/#blade/Microsoft_Azure_Marketplace/MarketplaceOffersBlade/selectedMenuItemId/solutionProviders)과 직접 작업할 수도 있습니다.

- **Azure AD ID 블로그:** [Azure AD ID 블로그](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/bg-p/Identity)에 구독하여 최신 제품 공지 사항, 심층 다이브 및 ID 엔지니어링 팀에서 직접 제공하는 로드맵 정보를 최신 상태로 유지하세요.
