---
title: Azure Active Directory에 대한 자동 사용자 프로비저닝 배포 계획
description: Azure Active Directory에서 자동 사용자 프로비저닝 계획 및 실행을 위한 지침
services: active-directory
author: kenwith
manager: karenh444
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: conceptual
ms.workload: identity
ms.date: 07/13/2021
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: e4be8f3bac712136fc2cbfea5a6a6febbd81e0e1
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129991093"
---
# <a name="plan-an-automatic-user-provisioning-deployment-in-azure-active-directory"></a>Azure Active Directory에서 자동 사용자 프로비저닝 배포 계획

많은 조직이 최종 사용자 생산성을 위해 ServiceNow, Zscaler 및 Slack과 같은 SaaS(Software as a Service) 애플리케이션에 의존합니다. 지금까지 IT 직원은 각 SaaS 애플리케이션에서 사용자 ID를 안전하게 관리하기 위해 CSV 파일 업로드 또는 사용자 지정 스크립트 사용과 같은 수동 프로비저닝 방법에 의존해왔습니다. 이러한 프로세스는 오류가 발생하기 쉽고, 안전하지 않으며, 관리하기가 어렵습니다.

Azure AD(Azure Active Directory) 자동 사용자 프로비저닝은 비즈니스 규칙에 따라 SaaS 애플리케이션에서 사용자 ID 생성, 유지 관리 및 제거를 안전하게 자동화하여 프로세스를 간소화합니다. 자동화를 통해 클라우드 기반 솔루션에 대한 종속성을 확장하면서 클라우드 전용 및 하이브리드 환경 모두에서 ID 관리 시스템을 효과적으로 스케일링할 수 있습니다.

기능을 더 잘 이해하려면 [Azure Active Directory를 사용하여 SaaS 애플리케이션에 대한 사용자 프로비저닝 및 프로비저닝 해제 자동화](../app-provisioning/user-provisioning.md)를 참조하세요.

## <a name="learn"></a>배우기

사용자 프로비저닝은 지속적인 ID 거버넌스를 위한 기반을 만들고, 신뢰할 수 있는 ID 데이터에 의존하는 비즈니스 프로세스의 품질을 향상시킵니다.

### <a name="key-benefits"></a>주요 이점

자동 사용자 프로비저닝을 사용하는 주요 이점은 다음과 같습니다.

* **생산성 향상**. 단일 사용자 프로비저닝 관리 인터페이스를 사용하여 SaaS 애플리케이션에서 사용자 ID를 관리할 수 있습니다. 이 인터페이스에는 단일 프로비저닝 정책 집합이 있습니다.

* **위험 관리**. 역할 및/또는 액세스를 정의하는 그룹 멤버 자격 또는 직원 상태를 기반으로 변경을 자동화하여 보안을 강화할 수 있습니다.

* **주소 규정 준수 및 거버넌스**. Azure AD는 모든 사용자 프로비저닝 요청에 대한 원시 감사 로그를 지원합니다. 요청은 원본 및 대상 시스템에서 실행됩니다. 따라서 단일 화면에서 애플리케이션에 액세스할 수 있는 사용자를 추적할 수 있습니다.

* **비용 절감**. 자동 사용자 프로비저닝은 수동 프로비저닝과 관련된 비효율성 및 인적 오류를 방지하여 비용을 절감합니다. 맞춤형으로 개발된 사용자 프로비저닝 솔루션, 스크립트 및 감사 로그의 필요성을 줄여줍니다.

### <a name="licensing"></a>라이선스

Azure AD는 애플리케이션 갤러리 메뉴에 제공된 템플릿을 사용하여 모든 애플리케이션의 셀프 서비스 통합을 제공합니다. 라이선스 요구 사항의 전체 목록은 [Azure AD 가격 책정 페이지](https://www.microsoft.com/security/business/identity-access-management/azure-ad-pricing)를 참조하세요.

#### <a name="application-licensing"></a>애플리케이션 라이선스

자동으로 프로비저닝할 애플리케이션에 적합한 라이선스가 필요합니다. 애플리케이션에 할당된 사용자에게 애플리케이션 역할에 적합한 라이선스가 있는지 여부를 애플리케이션 소유자와 논의합니다. Azure AD가 역할을 기반으로 자동 프로비저닝을 관리하는 경우에는 Azure AD에 할당된 역할을 애플리케이션 라이선스에 맞게 조정해야 합니다. 애플리케이션에서 소유한 라이선스가 맞지 않으면 사용자 프로비저닝/업데이트 중에 오류가 발생할 수 있습니다.

### <a name="terms"></a>사용 약관

이 문서에서 사용하는 용어는 다음과 같습니다.

* CRUD 작업 - 사용자 계정에 수행된 작업: 만들기, 읽기, 업데이트, 삭제

* SSO(Single Sign-On) - 사용자가 한 번 로그인하여 모든 SSO 사용 애플리케이션에 액세스할 수 있는 기능입니다. 사용자 프로비저닝 컨텍스트에서, SSO는 사용자가 자동 사용자 프로비저닝을 사용하는 모든 시스템에 액세스할 수 있는 단일 계정을 가진 결과입니다.

* 원본 시스템 - Azure AD가 프로비저닝하는 사용자의 원본 리포지토리입니다. Azure AD는 미리 통합된 대부분의 프로비저닝 커넥터의 원본 시스템입니다. 단, SAP, Workday 및 AWS와 같은 클라우드 애플리케이션에는 몇 가지 예외가 있습니다. 예를 들어 [Workday에서 AD로 사용자 프로비저닝](../saas-apps/workday-inbound-tutorial.md)을 참조하세요.

* 대상 시스템 - Azure AD가 프로비저닝하는 사용자의 대상 리포지토리입니다. 대상 시스템은 일반적으로 ServiceNow, Zscaler 및 Slack과 같은 SaaS 애플리케이션입니다. 대상 시스템은 AD와 같은 온-프레미스 시스템일 수도 있습니다.

* [SCIM(System for Cross-domain Identity Management)](https://aka.ms/scimoverview) -사용자 프로비저닝을 자동화하는 데 사용할 수 있는 개방형 표준입니다. SCIM은 Microsoft와 같은 ID 공급자와 사용자 ID 정보가 필요한 Salesforce 또는 기타 SaaS 앱과 같은 서비스 공급자 간에 사용자 ID 데이터를 전달합니다.

### <a name="training-resources"></a>교육 리소스

| 리소스| 링크 및 설명 |
| - | - |
| 주문형 웹 세미나| [Azure AD를 사용하여 엔터프라이즈 애플리케이션 관리](https://info.microsoft.com/CO-AZUREPLAT-WBNR-FY18-03Mar-06-ManageYourEnterpriseApplicationsOption1-MCW0004438_02OnDemandRegistration-ForminBody.html)<br>‎Azure AD를 사용하여 엔터프라이즈 SaaS 애플리케이션에 대한 SSO를 구현하는 방법과 액세스 제어를 위한 모범 사례를 알아봅니다. |
| 동영상| [Active Azure Directory에서 사용자 프로비저닝이란?](https://youtu.be/_ZjARPpI6NI) <br> [Active Azure Directory에서 사용자 프로비저닝을 배포하는 방법](https://youtu.be/pKzyts6kfrw) <br> [Salesforce를 Azure AD와 통합: 사용자 프로비저닝을 자동화하는 방법](https://azure.microsoft.com/resources/videos/integrating-salesforce-with-azure-ad-how-to-automate-user-provisioning/) |
| 온라인 과정| SkillUp Online: [Managing Identities](https://skillup.online/courses/course-v1:Microsoft+AZ-100.5+2018_T3/about) <br> Azure AD를 여러 SaaS 애플리케이션과 통합하고 이러한 애플리케이션에 대한 사용자 액세스를 보호하는 방법을 알아봅니다. |
| 책| [Modern Authentication with Azure Active Directory for Web Applications (Developer Reference) 1st Edition](https://www.amazon.com/Authentication-Directory-Applications-Developer-Reference/dp/0735696942/ref=sr_1_fkmr0_1?keywords=Azure+multifactor+authentication&qid=1550168894&s=gateway&sr=8-1-fkmr0).  <br> ‎새로운 환경에 맞는 Active Directory 인증 솔루션 구축을 위한 권위 있고 심층적인 가이드입니다. |
| 자습서| [SaaS 앱을 Azure AD와 통합하는 방법에 대한 자습서 목록](../saas-apps/tutorial-list.md)을 참조하세요. |
| FAQ| 자동화된 사용자 프로비저닝에 대한 [질문과 대답](../app-provisioning/user-provisioning.md) |

### <a name="solution-architectures"></a>솔루션 아키텍처

Azure AD 프로비저닝 서비스는 각 애플리케이션 공급업체가 제공하는 사용자 관리 API 엔드포인트에 연결하여 SaaS 앱 및 기타 시스템에 사용자를 프로비저닝합니다. 이러한 사용자 관리 API 엔드포인트를 사용하면 Azure AD에서 프로그래밍 방식으로 사용자를 만들고, 업데이트하고, 제거할 수 있습니다.

#### <a name="automatic-user-provisioning-for-hybrid-enterprises"></a>하이브리드 기업을 위한 자동 사용자 프로비저닝

이 예에서 사용자 및/또는 그룹은 온-프레미스 디렉터리에 연결된 HR 데이터베이스에 생성됩니다. Azure AD 프로비저닝 서비스는 대상 SaaS 애플리케이션에 대한 자동 사용자 프로비저닝을 관리합니다.

 ![사용자 프로비저닝](./media/plan-auto-user-provisioning/hybridprovisioning.png)

**워크플로에 대한 설명:**

1. 사용자/그룹은 SAP와 같은 온-프레미스 HR 애플리케이션/시스템에서 만들어집니다. 

1. **Azure AD Connect 에이전트** 는 예약된 ID(사용자 및 그룹)에 대한 동기화를 로컬 AD에서 Azure AD로 실행합니다.

1. **Azure AD 프로비저닝 서비스** 는 원본 시스템 및 대상 시스템에 대해 [초기 주기](../app-provisioning/user-provisioning.md)를 시작합니다. 

1. **Azure AD 프로비저닝 서비스** 는 초기 주기 이후에 원본 시스템에서 사용자나 그룹이 변경되었는지 쿼리하고 변경된 내용은 [증분 주기](../app-provisioning/user-provisioning.md)에 푸시합니다.

#### <a name="automatic-user-provisioning-for-cloud-only-enterprises"></a>클라우드 전용 기업을 위한 자동 사용자 프로비저닝

이 예제에서 사용자 생성은 Azure AD에서 발생하고 Azure AD 프로비저닝 서비스는 대상(SaaS) 애플리케이션에 대한 자동 사용자 프로비저닝을 관리합니다.

![Azure A D 프로비저닝 서비스를 통해 온-프레미스 HR 애플리케이션에서 대상 S a S 애플리케이션에 사용자/그룹을 만드는 프로세스를 보여주는 다이어그램입니다.](./media/plan-auto-user-provisioning/cloudprovisioning.png)

**워크플로에 대한 설명:**

1. 사용자/그룹은 Azure AD에서 생성됩니다.

1. **Azure AD 프로비저닝 서비스** 는 원본 시스템 및 대상 시스템에 대해 [초기 주기](../app-provisioning/user-provisioning.md)를 시작합니다. 

1. **Azure AD 프로비저닝 서비스** 는 초기 주기 이후에 원본 시스템에서 사용자나 그룹이 업데이트되었는지 쿼리하고 [증분 주기](../app-provisioning/user-provisioning.md)를 수행합니다.

#### <a name="automatic-user-provisioning-for-cloud-hr-applications"></a>클라우드 HR 애플리케이션에 대한 자동 사용자 프로비저닝 

이 예제에서는 사용자나 그룹이 Workday 및 SuccessFactors와 같은 클라우드 HR 애플리케이션에서 생성됩니다. Azure AD 프로비저닝 서비스 및 Azure AD Connect 프로비저닝 에이전트는 클라우드 HR 앱 테넌트의 사용자 데이터를 AD로 프로비저닝합니다. AD에서 계정이 업데이트되면 Azure AD Connect를 통해 Azure AD와 동기화되고 이메일 주소와 사용자 이름 특성을 클라우드 HR 앱 테넌트에 다시 쓸 수 있습니다.

![그림 2](./media/plan-auto-user-provisioning/workdayprovisioning.png)

1.  **HR 팀** 은 클라우드 HR 앱 테넌트에서 트랜잭션을 수행합니다.
2.  **Azure AD 프로비저닝 서비스** 는 클라우드 HR 앱 테넌트에서 예약된 주기를 실행하고 AD와 동기화하기 위해 처리해야 하는 변경 사항을 식별합니다.
3.  **Azure AD 프로비저닝 서비스** 는 AD 계정 만들기/업데이트/사용 설정/사용 안 함 설정 작업이 포함된 요청 페이로드를 사용하여 Azure AD Connect 프로비저닝 에이전트를 호출합니다.
4.  **Azure AD Connect 프로비저닝 에이전트** 는 서비스 계정을 사용하여 AD 계정 데이터를 관리합니다.
5.  **Azure AD Connect** 는 델타 동기화를 실행하여 AD에서 업데이트를 가져옵니다.
6.  **AD** 업데이트는 Azure AD와 동기화됩니다. 
7.  **Azure AD 프로비저닝 서비스** 는 Azure AD의 이메일 특성 및 사용자 이름을 클라우드 HR 앱 테넌트에 쓰기 저장(writeback)합니다.

## <a name="plan-the-deployment-project"></a>배포 프로젝트 계획

사용자 환경에서 사용자 프로비저닝을 배포하기 위한 전략을 결정하려면 조직의 요구 사항을 고려해야 합니다.

### <a name="engage-the-right-stakeholders"></a>올바른 관련자 참여

기술 프로젝트가 실패하는 이유는 일반적으로 영향, 결과 및 책임에 대한 기대 수준이 일치하지 않기 때문입니다. 이러한 문제를 방지하려면 [올바른 관련자를 참여시키고](../fundamentals/active-directory-deployment-plans.md) 관련자 및 이들의 프로젝트 입력과 책임을 문서화하여 프로젝트 내에서 관련자의 역할이 잘 이해될 수 있도록 해야 합니다.

### <a name="plan-communications"></a>통신 계획

통신은 새 서비스의 성공에 대단히 중요합니다. 사용자의 경험이 어떻게 변화하고 언제 변할 것인지, 문제가 발생할 경우 지원을 받는 방법에 대해 사용자와 사전에 소통합니다.

### <a name="plan-a-pilot"></a>파일럿 계획

자동 사용자 프로비저닝의 초기 구성은 프로덕션의 모든 사용자에 맞춰 스케일링하기 전에 사용자 수가 적은 테스트 환경에서 수행하는 것이 좋습니다. 파일럿 실행에 대한 [모범 사례](../fundamentals/active-directory-deployment-plans.md#best-practices-for-a-pilot)를 참조하세요.

#### <a name="best-practices-for-a-pilot"></a>파일럿에 대한 모범 사례  

파일럿을 활용하면 모든 사용자에게 기능을 배포하기 전에 소규모 그룹으로 테스트할 수 있습니다. 테스트의 일환으로 조직 내 각 사용 사례를 철저하게 테스트해야 합니다.

첫 번째 단계에서는 테스트를 수행하고 피드백을 제공할 수 있는 IT, 편리성 및 기타 적절한 사용자를 대상으로 합니다. 이 피드백을 사용하여 사용자에게 보내는 통신 및 지침을 추가로 개발하고 지원 담당자가 볼 수 있는 문제 유형에 대한 인사이트를 제공합니다.

대상 그룹의 범위를 늘려서 더 큰 사용자 그룹으로 롤아웃을 확장합니다. 이 작업은 [동적 그룹 멤버 자격](../enterprise-users/groups-dynamic-membership.md)을 통해 또는 대상 그룹에 사용자를 수동으로 추가하여 수행할 수 있습니다.

## <a name="plan-application-connections-and-administration"></a>애플리케이션 연결 및 관리 계획

Azure AD 포털을 사용하여 프로비저닝을 지원하는 모든 애플리케이션을 보고 관리합니다. [포털에서 앱 찾기](../app-provisioning/configure-automatic-user-provisioning-portal.md)를 참조하세요.

### <a name="determine-the-type-of-connector-to-use"></a>사용할 커넥터 유형 결정

자동 프로비전을 사용하도록 설정하고 구성하는 데 필요한 실제 단계는 애플리케이션에 따라 달라집니다. 자동으로 프로비저닝하려는 애플리케이션이 [Azure AD SaaS 앱 갤러리](../saas-apps/tutorial-list.md)에 나열되어 있으면 [앱 관련 통합 자습서](../saas-apps/tutorial-list.md)를 선택하여 사전 통합된 사용자 프로비저닝 커넥터를 구성해야 합니다.

그렇지 않으면 아래 단계를 따르세요.

1. 사전 통합된 사용자 프로비저닝 커넥터에 대한 [요청을 만듭니다](../develop/v2-howto-app-gallery-listing.md). 당사의 팀은 귀사 및 애플리케이션 개발자와 협력하여 귀사의 애플리케이션을 당사 플랫폼(SCIM을 지원하는 경우)에 온보딩합니다.

1. 앱에 대한 [BYOA SCIM](../app-provisioning/use-scim-to-provision-users-and-groups.md) 일반 사용자 프로비저닝 지원을 사용합니다. Azure AD에서 사전 통합된 프로비저닝 커넥터 없이 앱에 사용자를 프로비저닝하기 위한 요구 사항입니다.

1. 애플리케이션이 BYOA SCIM 커넥터를 사용할 수 있는 경우 [BYOA SCIM 통합 자습서](../app-provisioning/use-scim-to-provision-users-and-groups.md)를 참조하여 애플리케이션에 대한 BYOA SCIM 커넥터를 구성합니다.

자세한 내용은 [Azure AD 자동 사용자 프로비저닝에 사용할 수 있는 애플리케이션과 시스템은 무엇입니까?](../app-provisioning/user-provisioning.md)를 참조하세요.

### <a name="collect-information-to-authorize-application-access"></a>애플리케이션 액세스를 승인하기 위한 정보 수집

자동 사용자 프로비저닝 설정은 애플리케이션별 프로세스입니다. 각 애플리케이션에 대해, 대상 시스템의 사용자 관리 엔드포인트에 연결하려면 [관리자 자격 증명](../app-provisioning/configure-automatic-user-provisioning-portal.md)을 제공해야 합니다.

아래 이미지는 필수 관리자 자격 증명의 한 가지 버전을 보여줍니다.

![사용자 계정 프로비저닝 설정을 관리하기 위한 프로비저닝 화면](./media/plan-auto-user-provisioning/userprovisioning-admincredentials.png)

어떤 애플리케이션에는 관리자용 사용자 이름과 암호가 필요하지만 다른 애플리케이션에는 전달자 토큰이 필요할 수 있습니다.

## <a name="plan-user-and-group-provisioning"></a>사용자 및 그룹 프로비저닝 계획

엔터프라이즈 앱에 대한 사용자 프로비저닝을 사용하도록 설정하면 [Azure Portal](https://portal.azure.com/)은 특성 매핑을 통해 특성 값을 제어합니다.

### <a name="determine-operations-for-each-saas-app"></a>각 SaaS 앱에 대한 작업 결정

각 애플리케이션에는 Azure AD의 특성에 매핑되어야 하는 고유한 사용자 또는 그룹 특성이 있을 수 있습니다. 애플리케이션에는 사용 가능한 CRUD 작업의 하위 집합만 있을 수 있습니다.

각 애플리케이션에 대해 다음 정보를 문서화합니다.

* 대상 시스템의 사용자나 그룹 개체에 대해 수행할 CRUD 프로비저닝 작업. 예를 들어 각 SaaS 앱 비즈니스 소유자는 가능한 작업 중 일부만 원할 수 있습니다.

* 원본 시스템에서 사용할 수 있는 특성

* 대상 시스템에서 사용할 수 있는 특성

* 시스템 간 특성 매핑

### <a name="choose-which-users-and-groups-to-provision"></a>프로비저닝할 사용자 및 그룹 선택

자동 사용자 프로비저닝을 구현하기 전에 애플리케이션에 프로비저닝할 사용자 및 그룹을 결정해야 합니다.

* [범위 지정 필터](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)를 사용하면 어떤 사용자를 애플리케이션에 프로비저닝할지 결정하는 특성 기반 규칙을 정의할 수 있습니다.

* 다음으로 [사용자 및 그룹 할당](../manage-apps/assign-user-or-group-access-portal.md)을 사용합니다. 추가 필터링에 필요합니다.

### <a name="define-user-and-group-attribute-mapping"></a>사용자 및 그룹 특성 매핑 정의

자동 사용자 프로비저닝을 구현하려면 애플리케이션에 필요한 사용자 및 그룹 속성을 정의해야 합니다. Azure AD 사용자 개체와 각 SaaS 애플리케이션의 사용자 개체 사이에는 미리 구성된 특성 및 [특성 매핑](../app-provisioning/configure-automatic-user-provisioning-portal.md) 세트가 있습니다. 모든 SaaS 앱에서 그룹 특성을 사용하도록 설정하는 것은 아닙니다.

Azure AD는 직접 특성 대 특성 매핑, 상수 값 제공 또는 [특성 매핑에 대한 식 작성](../app-provisioning/functions-for-customizing-application-data.md)을 통해 지원합니다. 이러한 유연성을 통해 대상 시스템의 특성에 채워질 항목을 세밀하게 제어할 수 있습니다. [Microsoft Graph API](../app-provisioning/export-import-provisioning-configuration.md) 및 그래프 탐색기를 사용하여 사용자 프로비저닝 특성 매핑 및 스키마를 JSON 파일로 내보내고 Azure AD로 다시 가져올 수 있습니다.

자세한 내용은 [Azure Active Directory에서 SaaS 애플리케이션에 대한 사용자 프로비저닝 특성 매핑 사용자 지정](../app-provisioning/customize-application-attributes.md)을 참조하세요.

### <a name="special-considerations-for-user-provisioning"></a>사용자 프로비저닝에 대한 특별 고려 사항

배포 후 문제를 줄이려면 다음 사항을 고려하세요.

* 원본 및 대상 애플리케이션 간에 사용자/그룹 개체를 매핑하는 데 사용되는 특성은 복원력이 있어야 합니다. 특성이 변경되는 경우(예: 사용자가 회사의 다른 부분으로 이동하는 경우) 사용자/그룹이 잘못 프로비저닝되도록 해서는 안 됩니다.

* 애플리케이션에는 사용자 프로비저닝이 제대로 작동하기 위해 충족해야 하는 특정 제한 및/또는 요구 사항이 있을 수 있습니다. 예를 들어, Slack은 특정한 특성의 값을 자릅니다. 각 애플리케이션에 대한 [자동 사용자 프로비저닝 자습서](../saas-apps/tutorial-list.md)를 참조하세요.

* 원본 시스템과 대상 시스템 간의 스키마 일관성을 확인합니다. 일반적인 문제에는 UPN 또는 일치하지 않는 메일 등의 특성이 포함됩니다. 예를 들어 Azure AD의 UPN은 *john_smith@contoso.com* 으로 설정되고 앱에서는 *jsmith@contoso.com* 입니다. 자세한 내용은 [사용자 및 그룹 스키마 참조](../app-provisioning/use-scim-to-provision-users-and-groups.md)를 확인하세요.

## <a name="plan-testing-and-security"></a>테스트 및 보안 계획

배포의 각 단계에서 결과가 예상과 같은지 테스트하고 프로비저닝 주기를 감사해야 합니다.

### <a name="plan-testing"></a>테스트 계획

애플리케이션에 대한 자동 사용자 프로비저닝을 구성한 후에는 테스트 사례를 실행하여 솔루션이 조직의 요구 사항을 충족하는지 확인해야 합니다.

| 시나리오| 예상 결과 |
| - | - |
| 대상 시스템에 할당된 그룹에 사용자가 추가됨 | 사용자 개체가 대상 시스템에 프로비저닝됩니다. <br>사용자가 대상 시스템에 로그인하고 원하는 작업을 수행할 수 있습니다. |
| 대상 시스템에 할당된 그룹에서 사용자가 제거됨 | 사용자 개체가 대상 시스템에서 프로비저닝 해제됩니다.<br>사용자가 대상 시스템에 로그인할 수 없습니다. |
| 사용자 정보가 어떤 메서드로든 Azure AD에서 업데이트됨 | 업데이트된 사용자 특성은 증분 주기 후 대상 시스템에 반영됩니다. |
| 사용자가 범위를 벗어남 | 사용자 개체가 사용하지 않도록 설정되거나 삭제됩니다. <br>참고:이 동작은 [Workday 프로비저닝](skip-out-of-scope-deletions.md)을 위해 재정의됩니다. |

### <a name="plan-security"></a>보안 계획

배포의 일부로 보안 검토가 필요한 경우가 많습니다. 보안 검토가 필요한 경우 IDaaS(Identity as a Service)에 대한 개요를 제공하는 여러 Azure AD [백서](https://www.microsoft.com/download/details.aspx?id=36391)를 참조하세요.

### <a name="plan-rollback"></a>롤백 계획

자동 사용자 프로비저닝 구현이 프로덕션 환경에서 원하는 대로 작동하지 않을 경우 양호한 것으로 알려진 이전 상태로 되돌리는 데 아래 롤백 단계가 도움이 될 수 있습니다.

1. [프로비저닝 요약 보고서](../app-provisioning/check-status-user-account-provisioning.md) 및 [프로비저닝 로그](../app-provisioning/check-status-user-account-provisioning.md#provisioning-logs-preview)를 검토하여 영향을 받는 사용자 및/또는 그룹에 대해 발생한 잘못된 작업을 확인합니다.

1. 프로비저닝 감사 로그를 사용하여 영향을 받는 사용자 및/또는 그룹의 양호한 것으로 알려진 마지막 상태를 확인합니다. 원본 시스템(Azure AD 또는 AD)도 검토합니다.

1. 애플리케이션 소유자와 협력하여 양호한 것으로 알려진 마지막 상태 값을 사용하여 애플리케이션에서 직접 영향을 받는 사용자 및/또는 그룹을 업데이트합니다.

## <a name="deploy-automatic-user-provisioning-service"></a>자동 사용자 프로비저닝 서비스 배포

솔루션 요구 사항에 부합하는 단계를 선택합니다.

### <a name="prepare-for-the-initial-cycle"></a>초기 주기 준비

Azure AD 프로비저닝 서비스가 처음으로 실행되면 원본 시스템 및 대상 시스템에 대한 초기 주기를 통해 각 대상 시스템에 대한 모든 사용자 개체의 스냅샷이 생성됩니다.

애플리케이션에 대한 자동 프로비저닝을 사용하도록 설정하는 경우 초기 주기에는 20분에서 몇 시간까지 걸릴 수 있습니다. 지속 시간은 Azure AD 디렉터리의 크기와 프로비저닝 범위에 있는 사용자 수에 따라 다릅니다.

프로비저닝 서비스는 초기 주기 이후 두 시스템의 상태를 저장하여 후속 증분 주기의 성능을 향상시킵니다.

### <a name="configure-automatic-user-provisioning"></a>자동 사용자 프로비저닝 구성

[Azure Portal](https://portal.azure.com/)을 사용하여 자동 사용자 계정 프로비저닝 및 이것이 지원되는 애플리케이션에 대한 프로비저닝 해제를 관리합니다. [애플리케이션에 대한 자동 프로비저닝을 설정하려면 어떻게 해야 하나요?](../app-provisioning/user-provisioning.md)의 단계를 따르세요.

[Microsoft Graph API](/graph/api/resources/synchronization-overview)를 사용하여 Azure AD 사용자 프로비저닝 서비스를 구성 및 관리할 수도 있습니다.

## <a name="manage-automatic-user-provisioning"></a>자동 사용자 프로비저닝 관리

배포가 되었으면 솔루션을 관리해야 합니다.

### <a name="monitor-user-provisioning-operation-health"></a>사용자 프로비저닝 작업 상태 모니터링

[초기 주기](../app-provisioning/user-provisioning.md)에 성공한 후 Azure AD 프로비저닝 서비스는 다음 이벤트 중 하나가 발생할 때까지 각 애플리케이션에 특정한 간격으로 증분 업데이트를 무기한 실행합니다.

* 서비스가 수동으로 중지되고 [Azure Portal](https://portal.azure.com/)을 사용하거나 적절한 [Microsoft Graph API](/graph/api/resources/synchronization-overview) 명령을 사용하여 새로운 초기 주기가 트리거됩니다.

* 특성 매핑 또는 범위 지정 필터가 변경되어 새로운 초기 주기가 트리거됩니다.

* 높은 오류 비율로 인해 프로비저닝 프로세스가 격리되고 4주 이상 격리 상태로 유지되어 자동으로 사용하지 않도록 설정됩니다.

이러한 이벤트와 프로비저닝 서비스에서 수행하는 기타 모든 활동을 검토하려면 Azure AD [프로비저닝 로그](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)를 참조하세요.

프로비저닝 주기에 걸리는 시간을 파악하고 프로비저닝 작업의 진행 상황을 모니터링하려면 [사용자 프로비저닝 상태를 확인](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)하면 됩니다.

### <a name="gain-insights-from-reports"></a>보고서에서 인사이트 얻기

Azure AD는 감사 로그 및 보고서를 통해 조직의 사용자 프로비저닝 사용량 및 작동 상태에 대한 [추가 인사이트](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)를 제공할 수 있습니다.

관리자는 프로비저닝 요약 보고서를 확인하여 프로비저닝 작업의 작동 상태를 모니터링해야 합니다. 프로비저닝 서비스에서 실행하는 모든 활동은 Azure AD 감사 로그에 기록됩니다. [자습서: 자동 사용자 계정 프로비저닝에 대한 보고](../app-provisioning/check-status-user-account-provisioning.md)를 참조하세요.

조직의 요구 사항을 충족하는 주기로 이러한 보고서의 소유권을 가정하고 사용하는 것이 좋습니다. Azure AD는 대부분의 감사 데이터를 30일 동안 유지합니다.

### <a name="troubleshoot"></a>문제 해결

프로비저닝 중에 발생할 수 있는 문제를 해결하려면 다음 링크를 참조하세요.

* [Azure AD 갤러리 애플리케이션에 대해 사용자 프로비전 구성 문제](../app-provisioning/application-provisioning-config-problem.md)

* [애플리케이션에 프로비저닝하기 위해 온-프레미스 Active Directory의 특성을 Azure AD로 동기화](../app-provisioning/user-provisioning-sync-attributes-for-mapping.md)

* [사용자가 Azure Active Directory 갤러리 애플리케이션에 프로비전을 구성하는 동안 관리자 자격 증명을 저장하는 문제](./user-provisioning.md)

* [사용자가 Azure AD 갤러리 애플리케이션에 프로비전되지 않음](../app-provisioning/application-provisioning-config-problem-no-users-provisioned.md)

* [Azure AD 갤러리 애플리케이션에 잘못된 사용자 집합이 프로비전됨](../manage-apps/add-application-portal-assign-users.md)

### <a name="helpful-documentation"></a>유용한 설명서

* [특성 매핑에 대한 식 작성](../app-provisioning/functions-for-customizing-application-data.md)

* [Azure AD 동기화 API 개요](/graph/api/resources/synchronization-overview)

* [범위를 벗어난 사용자 계정 삭제 건너뛰기](skip-out-of-scope-deletions.md)

* [Azure AD Connect 프로비저닝 에이전트: 버전 릴리스 기록](provisioning-agent-release-version-history.md)

#### <a name="resources"></a>리소스

* [제품 사용자 의견 제공](https://feedback.azure.com/forums/169401-azure-active-directory)

* [Azure AD의 새로운 기능에 대한 최신 정보 받기](https://azure.microsoft.com/updates/?product=active-directory)

* [Microsoft Q&A Azure AD 포럼](/answers/topics/azure-active-directory.html)

## <a name="next-steps"></a>다음 단계
* [자동 사용자 프로비저닝 구성](../app-provisioning/configure-automatic-user-provisioning-portal.md)

* [Microsoft Graph API를 사용하여 프로비저닝 구성 내보내기 또는 가져오기](../app-provisioning/export-import-provisioning-configuration.md)

* [Azure Active Directory에서 특성 매핑에 대한 식 작성](../app-provisioning/functions-for-customizing-application-data.md)