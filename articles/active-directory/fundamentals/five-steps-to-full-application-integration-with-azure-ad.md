---
title: 모든 앱을 Azure AD와 통합하는 5단계
description: 이 가이드에서는 모든 애플리케이션을 Azure AD와 통합하는 방법을 설명합니다. 각 단계에서 값을 설명하고 기술 세부 정보를 설명하는 리소스에 대한 링크를 제공합니다.
services: active-directory
author: knicholasa
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 08/05/2020
ms.author: nichola
ms.openlocfilehash: 7c3e4441289b2b17cf772df73f3c53caa465813e
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124732578"
---
# <a name="five-steps-for-integrating-all-your-apps-with-azure-ad"></a>모든 앱을 Azure AD와 통합하는 5단계

Azure AD(Azure Active Directory)는 Microsoft의 클라우드 기반 ID 및 액세스 관리 서비스입니다. Azure AD는 고객, 파트너 및 직원이 필요한 애플리케이션에 액세스할 수 있도록 보안 인증 및 권한 부여 솔루션을 제공합니다. Azure AD를 사용하면 [조건부 액세스](../conditional-access/overview.md), [다단계 인증](../authentication/concept-mfa-howitworks.md), [single sign-on](../hybrid/how-to-connect-sso.md)및 [자동 사용자 프로비전](../app-provisioning/user-provisioning.md)을 통해 ID 및 액세스 관리가 쉽고 안전해집니다.

회사가 Microsoft 365를 구독하는 경우 Azure AD를 [이미 사용](/office365/enterprise/about-office-365-identity)하고 있을 것입니다. 그러나 Azure AD는 모든 애플리케이션에 사용할 수 있으며, [애플리케이션 관리의 중앙 집중화](../manage-apps/common-scenarios.md)를 통해 전체 앱 포트폴리오에서 동일한 ID 관리 기능, 도구 및 정책을 사용할 수 있습니다. 이렇게 함으로써 보안을 강화하고, 비용을 절감하고, 생산성을 높이고, 규정 준수를 보장하는 통합 솔루션이 제공됩니다. 또한, 온-프레미스 앱에 대한 원격 액세스 권한을 얻게 됩니다.

이 가이드에서는 모든 애플리케이션을 Azure AD와 통합하는 방법을 설명합니다. 각 단계에서 값을 설명하고 기술 세부 정보를 설명하는 리소스에 대한 링크를 제공합니다. 단계는 권장 순서대로 소개합니다. 그러나 사용자에게 가장 가치 있는 방식으로 시작하기 위해 과정을 순서에 상관 없이 진행해도 괜찮습니다.

심층 비즈니스 프로세스 백서를 비롯해 본 토픽의 다른 리소스는 [Azure Active Directory로 애플리케이션을 마이그레이션하기 위한 리소스](../manage-apps/migration-resources.md) 페이지에서 찾을 수 있습니다.

## <a name="1-use-azure-ad-for-new-applications"></a>1. 새 애플리케이션에 Azure AD 사용

먼저, 새로 획득한 애플리케이션에 집중합니다. 비즈니스에서 새 애플리케이션을 사용하기 시작하면 즉시 [Azure AD 테넌트에 추가](../manage-apps/add-application-portal.md)합니다. Azure AD에 새 앱을 추가하는 것이 조직의 표준 관행이 되도록 회사 정책을 설정합니다. 이를 통해 기존 비즈니스 프로세스에 대한 중단을 최소화하고, 사용자의 현재 환경에서 사람들이 비즈니스하는 방식을 변경하지 않고 앱을 통합하여 얻게 되는 가치를 조사하고 입증할 수 있습니다.

Azure AD(Azure Active Directory)에는 수천 개의 사전 통합 애플리케이션이 포함된 갤러리가 있어서 쉽게 시작할 수 있습니다. 다음과 같이 인기 있는 앱과 통합하기 위한 단계별 [자습서](../saas-apps/tutorial-list.md)를 사용하여 [Azure AD 조직에 갤러리 앱을 추가](../manage-apps/add-application-portal.md)할 수 있습니다.

- [ServiceNow](../saas-apps/servicenow-tutorial.md)
- [Workday](../saas-apps/workday-tutorial.md)
- [Salesforce](../saas-apps/salesforce-tutorial.md)
- [AWS](../saas-apps/amazon-web-service-tutorial.md)
- [Slack](../saas-apps/slack-tutorial.md)

또한, 조직에 이미 있는 애플리케이션을 비롯해 [갤러리에 존재하지 않는 애플리케이션을 통합](../manage-apps/view-applications-portal.md)할 수 있고, 이미 Azure AD 갤러리의 일부가 아닌 공급업체의 타사 애플리케이션도 통합할 수 있습니다. [사용자 앱을 갤러리에 추가](../develop/v2-howto-app-gallery-listing.md)할 수도 있습니다.

마지막으로, 사내에서 개발하는 앱의 통합도 가능합니다. 이 내용은 본 가이드의 5 단계에서 다룹니다.

## <a name="2-determine-existing-application-usage-and-prioritize-work"></a>2. 기존 애플리케이션 사용 결정 및 작업 우선 순위 지정

다음으로는 직원들이 자주 사용하는 애플리케이션을 발견하여 Azure AD와 통합하기 위한 작업의 우선 순위를 지정합니다.

그렇게 하려면 먼저, Microsoft Cloud App Security의 [클라우드 검색 도구](/cloud-app-security/tutorial-shadow-it)를 사용하여 네트워크의 "섀도" IT(IT 부서에서 관리하지 않는 앱)를 검색하고 관리합니다. [Microsoft DEFENDER ATP(Advanced Threat Protection)를 사용](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection)하여 검색 프로세스를 간소화하고 확장할 수 있습니다.

또한 Azure Portal의 [AD FS 애플리케이션 작업 보고서](../manage-apps/migrate-adfs-application-activity.md)를 사용하여 조직의 모든 AD FS 앱, AD FS 앱에 로그인한 고유 사용자 수, AD FS 앱을 Azure AD와 통합하기 위한 호환성 등을 검색할 수 있습니다.

기존 환경을 발견한 후에는 [계획 만들기](../manage-apps/migration-resources.md)를 하고, 우선 순위가 가장 높은 통합 대상 앱들의 우선 순위를 지정할 수 있습니다. 이 프로세스를 안내하기 위한 몇 가지 질문의 예는 다음과 같습니다.

- 가장 많이 사용되는 앱은 무엇인가요?
- 위험성이 가장 높은 앱은 무엇인가요?
- 향후에 서비스 해제되어 이동이 불필요한 앱은 무엇인가요?
- 온-프레미스 상태를 유지해야 하고 클라우드로 이동이 불가능한 앱은 무엇인가요?

모든 앱이 통합되고 더 이상 여러 ID 솔루션을 사용하지 않게 될 때 가장 많은 혜택과 비용 절감 효과를 보게 될 것입니다. 그러나 이 목표를 달성하는 과정에서 보다 수월해진 ID 관리와 보안 강화를 경험할 수 있습니다. 이 시간을 활용하면 작업의 우선 순위를 지정하고 상황에 맞는 결정을 내릴 수 있습니다.

## <a name="3-integrate-apps-that-rely-on-other-identity-providers"></a>3. 다른 ID 공급자에 의존하는 앱의 통합

검색 프로세스 중에 IT 부서에서 추적되지 않는 애플리케이션을 발견하는 경우가 있습니다. 이러한 애플리케이션은 데이터와 리소스를 취약한 상태에 노출시킬 수 있습니다. 또한 ADFS(Active Directory Federation Services)나 다른 ID 공급자를 비롯한 대체 ID 솔루션을 사용하는 애플리케이션을 갖고 있는 경우도 있습니다. 그렇다면 과연 어떻게 ID와 액세스 관리를 통합해 비용을 절감하고 보안을 강화할 수 있는지 생각해야 합니다. 보유하고 있는 ID 솔루션을 줄일 때 나타나는 효과는 다음과 같습니다.

- 온-프레미스 사용자 프로비전 및 인증에 대한 필요성뿐만 아니라 동일한 서비스에 대하여 다른 클라우드 ID 공급자에게 지불되는 라이선스 요금을 제거하므로 비용이 절감됩니다.
- ID 및 액세스 관리 프로세스에 중복이 감소해 관리비가 줄어들고 보안이 향상됩니다.
- [MyApps 포털](../manage-apps/access-panel-collections.md)을 통해 직원은 필요한 모든 애플리케이션에 대하여 안전한 Single Sign-On 액세스 권한을 얻을 수 있습니다.
- 앱 사용 과정에서 Azure AD가 얻게 되는 데이터 양이 증가하여 조건부 액세스 같은 Azure AD의 [ID 보호](../identity-protection/overview-identity-protection.md) 관련 서비스의 인텔리전스가 향상되고, 새로 추가된 앱으로 Azure AD의 혜택이 확장됩니다.

비즈니스 및 애플리케이션 소유자의 이해와 관심을 높이기 위해 사용할 수 있도록 [포스터](https://aka.ms/AppOnePager), [프레젠테이션](https://aka.ms/AppGuideline) 등 Azure AD와 앱을 통합하는 비즈니스 프로세스의 관리를 위한 지침을 게시했습니다. 사용자 자체 브랜딩을 적용해 게시된 샘플을 수정하고, 수정을 진행하는 동안 회사 포털, 뉴스레터, 기타 매체를 통해 조직에 샘플을 게시하십시오.

먼저 ADFS(Active Directory Federation Services)의 사용을 평가해 보시기 바랍니다. 많은 조직에서 ADFS를 사용하여 SaaS 앱, 사용자 지정 LOB(기간 업무) 앱, Microsoft 365, Azure AD 기반 앱 등을 인증하고 있습니다.

![다이어그램은 온-프레미스 앱, LOB(기간 업무) 앱, SaaS 앱, 그리고 Azure AD를 경유하는 Office 365 등이 모두 점선으로 Active Directory 및 AD FS에 연결된 모습을 보여줍니다.](\media\five-steps-to-full-application-integration-with-azure-ad\adfs-integration-1.png)

이 구성은 ID 관리 솔루션의 [중심을 ADFS에서 AZURE AD로 변경](../manage-apps/migrate-adfs-apps-to-azure.md)하면 업그레이드가 가능합니다. 이렇게 하면 직원들이 액세스하려고 하는 모든 앱에 로그온할 수 있으며, 위에서 언급한 기타 혜택 외에도 직원이 [MyApps 포털](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)을 통해 필요한 모든 비즈니스 애플리케이션을 쉽게 찾을 수 있습니다.

![다이어그램은 Active Directory 및 AD FS를 경유하는 온-프레미스 앱, LOB(기간 업무) 앱, SaaS 앱, Office 365 등이 모두 점선으로 Azure Active Directory에 연결된 모습을 보여줍니다.](\media\five-steps-to-full-application-integration-with-azure-ad\adfs-integration-2.png)

Azure AD가 중앙 ID 공급자가 되면 페더레이션 솔루션을 사용하지 않고 ADFS로부터 완전히 전환할 수 있습니다. 기존에 인증을 위해 ADFS를 사용한 앱은 이제 Azure AD만 사용하면 됩니다.

![다이어그램은 온-프레미스 앱, LOB(기간 업무) 앱, SaaS 앱, Office 365 등이 모두 점선으로 Azure Active Directory에 연결된 모습을 보여줍니다. Active Directory 및 AD FS는 존재하지 않습니다.](\media\five-steps-to-full-application-integration-with-azure-ad\adfs-integration-3.png)

다른 클라우드 기반 ID 공급자를 사용하는 앱을 Azure AD로 마이그레이션할 수도 있습니다. 조직에서 여러 IAM(ID 액세스 관리) 솔루션을 사용할 수 있습니다. 하나의 Azure AD 인프라로 마이그레이션하면 IAM 라이선스(온-프레미스 또는 클라우드)에 대한 종속성 및 인프라 비용을 줄일 수 있는 기회를 갖게 됩니다. Microsoft 365 라이선스를 통해 Azure AD 비용을 이미 지불했는데 또 다른 IAM 솔루션으로 인한 추가 비용을 지불할 이유가 없습니다.

## <a name="4-integrate-on-premises-applications"></a>4. 온-프레미스 애플리케이션 통합

기존에는 애플리케이션은 회사 네트워크에 연결된 동안에만 액세스를 허용하여 보안을 유지했습니다. 그러나 연결성이 확장되고 있는 세상에서 저희는 고객, 파트너, 직원들이 전 세계 어디에 있든 상관 없이 앱에 액세스할 수 있는 환경을 제공하고자 합니다. AppProxy([Azure AD 애플리케이션 프록시](../app-proxy/what-is-application-proxy.md))는 기존 온-프레미스 앱을 Azure AD에 연결하는 Azure AD의 기능으로서 에지 서버 또는 기타 추가 인프라를 유지 관리할 필요가 없습니다.

![다이어그램은 작동 중인 애플리케이션 프록시 서비스를 보여 줍니다. 사용자가 "https://sales.contoso.com"에 액세스하고 사용자 요청이 Azure Active Directory의 "https://sales-contoso.msappproxy.net"를 통해 온 프레미스 주소 "http://sales"로 리디렉션됩니다.](./media/five-steps-to-full-application-integration-with-azure-ad\app-proxy.png)

[자습서: Azure Active Directory의 애플리케이션 프록시를 통해 원격 액세스를 위한 온-프레미스 애플리케이션 추가](../app-proxy/application-proxy-add-on-premises-application.md)를 사용하여 애플리케이션 프록시를 활성화하고 Azure AD 테넌트에 온-프레미스 애플리케이션을 추가할 수 있습니다.

또한 F5 Big-IP APM 또는 Zscaler Private Access와 같은 애플리케이션 제공 컨트롤러의 통합이 가능합니다. Azure AD와 통합하면 파트너 제품의 트래픽 관리 및 보안 기능과 함께 Azure AD의 최신 인증 및 ID 관리가 제공됩니다. 이 솔루션은 [Secure Hybrid Access](../manage-apps/secure-hybrid-access.md)라고 합니다. 현재 다음 서비스 중 하나를 사용하고 있다면 Azure AD와 통합하는 방법을 단계별로 안내하는 자습서를 참고하세요.

- [Akamai EAA(엔터프라이즈 애플리케이션 액세스)](../saas-apps/akamai-tutorial.md)
- [Citrix ADC(애플리케이션 제공 컨트롤러)](../saas-apps/citrix-netscaler-tutorial.md) (구 Citrix Netscaler)
- [F5 Big-IP APM](../saas-apps/headerf5-tutorial.md)
- [ZPA(Zscaler Private Access)](../saas-apps/zscalerprivateaccess-tutorial.md)

## <a name="5-integrate-apps-your-developers-build"></a>5. 개발자가 빌드하는 앱의 통합

사내에서 빌드되는 앱의 경우 개발자는 [Microsoft ID 플랫폼](../develop/index.yml)을 사용하여 인증 및 권한 부여를 구현할 수 있습니다. 플랫폼과 통합된 애플리케이션은 포트폴리오의 다른 앱들처럼 [AZURE AD에 등록](../develop/quickstart-register-app.md)되고 관리됩니다.

개발자는 내부용 앱과 고객 대면 앱 모두에 플랫폼을 사용할 수 있습니다. 그 밖에도 플랫폼 사용의 이점은 다양합니다. 플랫폼의 일부인 [MSAL(Microsoft 인증 라이브러리)](../develop/msal-overview.md)을 사용하는 개발자는 다단계 인증과 같은 최신 환경을 사용하고 보안 키를 사용하여 앱을 직접 구현할 필요 없이 앱에 액세스할 수 있습니다. 또한 Microsoft ID 플랫폼과 통합된 앱은 [Microsoft Graph](../develop/microsoft-graph-intro.md)에 액세스할 수 있습니다. Microsoft Graph는 Microsoft 365 데이터를 제공하는 통합 API 엔드포인트이며 조직의 생산성, ID 및 보안의 패턴을 설명합니다. 개발자는 이 정보를 사용함으로써 사용자의 생산성을 제고하는 기능을 구현할 수 있습니다. 예를 들어 사용자가 최근 상호 작용한 사람을 식별하고 앱의 UI에 표면화시킵니다.

저희는 [동영상 시리즈](https://www.youtube.com/watch?v=zjezqZPPOfc&amp;list=PLLasX02E8BPBxGouWlJV-u-XZWOc2RkiX)를 통해 플랫폼을 포괄적으로 소개하고 지원되는 언어와 플랫폼의 [다양한 코드 샘플](../develop/sample-v2-code.md)을 제공하고 있습니다.

## <a name="next-steps"></a>다음 단계

- [애플리케이션을 Azure Active Directory로 마이그레이션하기 위한 리소스](../manage-apps/migration-resources.md)