---
title: Azure Active Directory 내 앱 구성 계획
description: 조직에서 내 앱을 효과적으로 사용하기 위한 계획 가이드입니다.
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 07/25/2021
ms.author: davidmu
ms.reviewer: lenalepa
ms.openlocfilehash: 36da44521169c516888489bd0ac60015adcc432f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122566636"
---
# <a name="plan-azure-active-directory-my-apps-configuration"></a>Azure Active Directory 내 앱 구성 계획

> [!NOTE]
> 이 문서는 조직의 내 앱 포털의 구성을 계획해야 하는 IT 전문가를 위해 작성되었습니다.
>
> **최종 사용자 문서를 위해서는 [내 앱 포털에서 앱에 로그인하여 시작](../user-help/my-apps-portal-end-user-access.md)** 을 참조하세요.

Azure AD(Azure Active Directory) 내 앱은 앱을 시작하고 관리하기 위한 웹 기반 포털입니다. 내 앱 페이지를 사용하면 사용자가 작업을 시작하는 공간과 액세스 권한이 있는 모든 애플리케이션을 찾을 수 있습니다. 사용자는 [https://myapps.microsoft.com](https://myapps.microsoft.com/)에서 내 앱에 액세스합니다.

> [!VIDEO https://www.youtube.com/embed/atj6Ivn5m0k]

## <a name="why-configure-my-apps"></a>내 앱을 구성하는 이유

사용자는 내 앱 포털을 기본적으로 사용할 수 있으며 해제할 수 없습니다. 최상의 환경을 제공하고 포털이 유용하게 유지되도록 구성하는 것이 중요합니다.

다음 두 조건이 모두 충족되면 Azure Active Directory 엔터프라이즈 애플리케이션 목록에 있는 모든 애플리케이션이 표시됩니다.

* 앱의 가시성 설정은 true로 설정되어 있습니다.

* 앱은 모든 사용자 또는 그룹에 할당됩니다. 할당된 사용자에 대해 표시됩니다.

포털을 구성하면 적절한 사람들이 적절한 앱을 쉽게 찾을 수 있습니다.

### <a name="how-is-the-my-apps-portal-used"></a>내 앱 포털은 어떻게 사용되나요?

사용자는 다음을 위해 내 앱 포털에 액세스합니다.

* 액세스 권한이 있는 모든 조직의 Azure AD 연결 애플리케이션을 검색하고 액세스합니다.

   * 사용자에게 최상의 환경을 제공하기 위해 앱이 SSO(Single Sign-On)가 구성되었는지 확인하는 것이 가장 좋습니다.

* 셀프 서비스를 사용하도록 구성된 새 앱에 대한 액세스를 요청합니다.

* 앱의 개인 컬렉션을 만듭니다.

* 애플리케이션에 대한 액세스 권한을 부여하는 데 사용되는 그룹 소유자 또는 위임된 컨트롤의 역할을 할당받은 경우 다른 사용자를 위해 앱에 대한 액세스를 관리합니다.

관리자는 다음을 구성할 수 있습니다.

* 서비스 약관을 포함한 [승인 환경](../manage-apps/configure-user-consent.md).

* [셀프 서비스 애플리케이션 검색 및 액세스 요청](../manage-apps/access-panel-manage-self-service-access.md)

* [애플리케이션 컬렉션](../manage-apps/access-panel-collections.md).

* 애플리케이션에 아이콘 할당

* 사용자에게 친숙한 애플리케이션 이름

* 내 앱에 표시되는 회사 브랜딩

## <a name="plan-consent-configuration"></a>동의 구성 계획

### <a name="user-consent-for-applications"></a>애플리케이션에 대한 사용자 동의

사용자가 애플리케이션에 로그인하고 애플리케이션에서 조직의 데이터에 액세스할 수 있으려면 먼저 사용자 또는 관리자가 애플리케이션 권한을 부여해야 합니다. 사용자 동의가 허용되는지 여부와 조건에 대해 구성할 수 있습니다. **확인된 게시자의 애플리케이션에 대한 사용자 동의만 허용하는 것이 좋습니다.**

자세한 내용은 [애플리케이션에 최종 사용자가 동의하는 방법 구성](../manage-apps/configure-user-consent.md)을 참조하세요.

### <a name="group-owner-consent-for-apps-accessing-data"></a>데이터에 액세스하는 앱에 대한 그룹 소유자 동의

그룹 및 팀 소유자는 타사 공급업체에서 게시한 애플리케이션과 같은 애플리케이션에 권한을 부여하여 그룹과 연결된 조직의 데이터에 액세스하도록 할 수 있습니다. 자세히 알아보려면 [Microsoft Teams의 리소스별 동의](/microsoftteams/resource-specific-consent)를 참조하세요.

이 기능을 허용할지 여부를 구성할 수 있습니다.

자세한 내용은 [그룹 동의 권한 구성](../manage-apps/configure-user-consent-groups.md)을 참조하세요.

### <a name="plan-communications"></a>통신 계획

통신은 새 서비스의 성공에 대단히 중요합니다. 사용자에게 언제 어떻게 환경이 변경되며 필요한 경우 지원을 받는 방법에 대해 사전에 알려 줍니다.

앱은 일반적으로 사용자 문제를 만들지 않지만 그에 준비하는 것이 중요합니다. 시작하기 전에 지원 담당자의 모든 리소스 목록 및 가이드를 만듭니다.

#### <a name="communications-templates"></a>통신 템플릿

Microsoft는 [이메일에 대한 사용자 지정 가능한 템플릿과 내 앱에 대한 기타 통신](https://aka.ms/APTemplates)을 제공합니다. 회사 문화에 맞는 다른 통신 채널에서 사용하기 위해 이러한 자산을 조정할 수 있습니다.

## <a name="plan-your-sso-configuration"></a>SSO 구성 계획

사용자가 자격 증명을 입력할 필요 없이 원활한 환경을 사용할 수 있도록 내 앱 포털의 모든 앱에 대해 SSO를 사용하도록 설정하는 것이 가장 좋습니다.

Azure AD는 여러 SSO 옵션을 지원합니다.

* 자세히 알아보려면 [Azure AD의 Single Sign-On 옵션](sso-options.md)을 참조하세요.

* Azure AD를 앱의 ID 공급자로 사용하는 방법을 자세히 알아보려면 [애플리케이션 관리에 대한 빠른 시작 시리즈](../manage-apps/view-applications-portal.md)를 참조하세요.

### <a name="use-federated-sso-if-possible"></a>가능하면 페더레이션된 SSO 사용

내 앱 페이지에서 최상의 사용자 환경을 사용하려면 OpenID Connect 또는 SAML과 같은 페더레이션된 SSO(Single Sign-On)에 사용할 수 있는 클라우드 애플리케이션의 통합부터 시작합니다. 페더레이션된 SSO를 사용하면 사용자가 애플리케이션에 로그인할 때 원클릭 환경을 사용할 수 있으며, 이는 구성 제어에서 더욱 강력해집니다.

애플리케이션에 Single Sign-On을 구성하는 방법에 대한 자세한 내용은 [Single Sign-On 배포 계획](plan-sso-deployment.md)을 참조하세요.

### <a name="considerations-for-special-sso-circumstances"></a>특수 SSO 상황에 대한 고려 사항

> [!TIP]
> 사용자 환경을 개선하려면 암호 기반 SSO 및 ADFS 대신 애플리케이션에서 지원할 때 Azure AD에서 페더레이션된 SSO(OpenID Connect/SAML)를 사용합니다.

암호 기반 SSO 애플리케이션 또는 Azure AD 애플리케이션 프록시에서 액세스하는 애플리케이션에 로그인하려면 사용자가 내 앱 보안 로그인 확장을 설치하고 사용해야 합니다. 사용자가 암호 기반 SSO 또는 애플리케이션 프록시 애플리케이션을 처음 시작할 때 확장을 설치하라는 메시지가 표시됩니다.

![스크린샷](./media/my-apps-deployment-plan/ap-dp-install-myapps.png)

확장에 대한 자세한 내용은 [내 앱 브라우저 확장 설치](../user-help/my-apps-portal-end-user-access.md)를 참조하세요.

이러한 애플리케이션을 통합해야 하는 경우 [지원되는 브라우저](../user-help/my-apps-portal-end-user-access.md)를 사용하여 확장을 대규모로 배포하는 메커니즘을 정의해야 합니다. 다음 옵션을 사용할 수 있습니다.

* [Chrome, Firefox, Microsoft Edge 또는 IE에 대한 사용자 중심 다운로드 및 구성](../user-help/my-apps-portal-end-user-access.md)

* [Internet Explorer용 Configuration Manager](/mem/configmgr/core/clients/deploy/deploy-clients-to-windows-computers)

확장을 통해 사용자는 검색 표시줄에서 앱을 시작하고 최근에 사용한 애플리케이션에 대한 액세스를 찾고 내 앱 페이지에 대한 링크를 얻을 수 있습니다.

![내 앱 확장 검색의 스크린샷](./media/my-apps-deployment-plan/my-app-extsension.png)

#### <a name="plan-for-mobile-access"></a>모바일 액세스 계획

암호 기반 SSO를 사용하거나 [Microsoft Azure AD 애플리케이션 프록시](../app-proxy/application-proxy.md)를 사용하여 액세스하는 애플리케이션의 경우 Microsoft Edge 모바일을 사용해야 합니다. 다른 애플리케이션의 경우 모든 모바일 브라우저를 사용할 수 있습니다.

### <a name="linked-sso"></a>연결된 SSO

연결된 SSO 옵션을 사용하여 애플리케이션을 추가할 수 있습니다. 기존 웹 애플리케이션의 URL에 연결되는 애플리케이션 타일을 구성할 수 있습니다. 연결된 SSO를 사용하면 모든 애플리케이션을 Azure AD SSO로 마이그레이션하지 않고도 내 앱 포털로 사용자를 이끌 수 있습니다. 사용자 환경을 방해하지 않고 Azure AD SSO 구성 애플리케이션으로 점진적으로 이동할 수 있습니다.

## <a name="plan-the-user-experience"></a>사용자 환경 계획

기본적으로 사용자에게 액세스 권한이 있는 모든 애플리케이션 및 셀프 서비스 검색용으로 구성된 모든 애플리케이션이 사용자의 내 앱 패널에 표시됩니다. 많은 조직의 경우 이 목록은 방대할 수 있으며, 구성되지 않은 경우에는 부담일 수 있습니다.

### <a name="plan-my-apps-collections"></a>내 앱 컬렉션 계획

사용자가 액세스할 수 있는 모든 Azure AD 애플리케이션은 **앱** 컬렉션에 있는 내 앱에 표시됩니다. 컬렉션을 사용하여 관련 애플리케이션을 그룹화하고 별도의 탭에 표시하여 쉽게 찾을 수 있습니다. 예를 들어 컬렉션을 사용하여 특정 작업 역할, 작업, 프로젝트 등에 대해 애플리케이션의 논리적 그룹을 만들 수 있습니다.

최종 사용자는 다음을 통해 환경을 사용자 지정할 수도 있습니다.

* 자신의 앱 컬렉션을 만듭니다.

* [앱 컬렉션을 숨기고 다시 정렬](access-panel-collections.md)합니다.

![셀프 서비스 구성의 스크린샷](./media/my-apps-deployment-plan/collections.png)

내 앱 포털에서 앱을 숨기면서 Microsoft 365 포털과 같은 다른 위치에서 계속 액세스할 수 있는 옵션이 있습니다. 자세히 알아보기: [Azure Active Directory의 사용자 환경에서 애플리케이션 숨기기](hide-application-from-user-portal.md)

> [!IMPORTANT]
> 사용자에게 액세스 권한이 있는 950개 앱에만 내 앱을 통해 액세스할 수 있습니다. 여기에는 사용자 또는 관리자가 숨긴 앱이 포함됩니다.

### <a name="plan-self-service-group-management-membership"></a>셀프 서비스 그룹 관리 구성원 계획

사용자가 Azure AD에서 자신의 보안 그룹 또는 Microsoft 365 그룹을 만들고 관리할 수 있도록 설정할 수 있습니다. 그룹 소유자는 구성원 요청을 승인 또는 거부하고 그룹 구성원에 대한 제어를 위임할 수 있습니다. 셀프 서비스 그룹 관리 기능은 보안 그룹 및 Office 365 그룹에 대해서만 사용할 수 있지만 메일 사용 가능 보안 그룹 및 메일링 그룹에는 사용할 수 없습니다.

셀프 서비스 그룹 구성원에 대한 계획을 수립하려면 조직의 모든 사용자가 그룹을 만들고 관리할 수 있도록 허용할지 또는 사용자의 하위 집합만 허용할지 결정합니다. 사용자의 하위 집합을 허용하는 경우 해당 사용자가 추가될 그룹을 설정해야 합니다.

이러한 시나리오가 가능하게 하는 방법에 대한 자세한 내용은 [Azure Active Directory의 셀프 서비스 그룹 관리 설정](../enterprise-users/groups-self-service-management.md)을 참조하세요.

### <a name="plan-self-service-application-access"></a>셀프 서비스 애플리케이션 액세스 계획

사용자가 내 앱 패널을 통해 애플리케이션에 대한 액세스를 검색하고 요청하도록 설정할 수 있습니다. 그러기 위해서는 먼저 다음을 수행해야 합니다.

* 셀프 서비스 그룹 관리 사용

* SSO에 대해 앱 사용

* 애플리케이션 액세스를 위한 그룹 만들기

![내 앱 셀프 서비스 구성의 스크린샷](./media/my-apps-deployment-plan/my-apps-self-service.png)

사용자가 액세스를 요청하면 기본 그룹에 대한 액세스를 요청하는 것입니다. 그룹 소유자는 그룹 구성원을 관리하며 애플리케이션 액세스 권한을 위임받을 수 있습니다. 승인 워크플로를 사용하여 애플리케이션에 대한 액세스를 명시적으로 승인할 수 있습니다. 승인자는 애플리케이션에 대한 액세스 요청이 보류 중인 경우 내 앱 포털 내에서 알림을 받게 됩니다.

## <a name="plan-reporting-and-auditing"></a>보고 및 감사 계획

Azure AD는 [기술 및 비즈니스 정보를 제공하는 보고서]../reports-monitoring/overview-reports.md)를 제공합니다. 비즈니스 및 기술 애플리케이션 소유자와 협력하여 이러한 보고서의 소유권을 가정하고 정기적으로 사용합니다. 다음 표에서는 일반적인 보고 시나리오에 대한 몇 가지 예를 제공합니다.

| 예제| 위험 관리| 생산성 향상| 거버넌스 및 규정 준수 |
| - | - | - | -|
| 보고서 유형| 애플리케이션 사용 권한 및 사용| 계정 프로비전 활동| 애플리케이션에 액세스하는 사용자 검토 |
| 잠재적 작업| 감사 액세스; 권한 해지| 프로비저닝 오류 수정| 액세스 취소 |

Azure AD는 30일 동안 대부분의 감사 데이터를 유지합니다. Azure 관리 포털 또는 API를 통해 분석 시스템에 다운로드하여 데이터를 사용할 수 있습니다.

#### <a name="auditing"></a>감사

30일 동안 감사 로그를 사용할 수 있습니다. 조직에서 더 긴 보존을 요구하는 경우 Splunk 또는 ArcSight와 같은 SIEM(보안 정보 이벤트 및 관리) 도구로 로그를 내보냅니다.

감사, 보고 및 재해 복구 백업의 경우 필요한 다운로드 빈도, 대상 시스템 정의 및 각 백업 관리를 담당하는 사용자를 문서화합니다. 별도의 감사 및 보고 백업이 필요하지 않을 수 있습니다. 재해 복구 백업은 별도의 엔터티여야 합니다.

## <a name="validate-your-deployment"></a>배포 확인

내 앱 배포가 철저하게 테스트되었고 롤백 계획이 준비되어 있는지 확인합니다.

회사 소유의 디바이스와 개인 디바이스 모두에서 다음 테스트를 수행합니다. 이러한 테스트 사례에도 비즈니스 사용 사례가 반영되어야 합니다. 일반적인 기술 시나리오를 기반으로 하는 몇 가지 사례는 다음과 같습니다. 필요에 따라 다른 사용자를 추가합니다.

#### <a name="application-sso-access-test-case-examples"></a>애플리케이션 SSO 액세스 테스트 사례 예:

| 비즈니스 사례| 예상된 결과 |
| - | - |
| 사용자가 내 앱 포털에 로그인합니다.| 사용자가 로그인하여 애플리케이션을 볼 수 있습니다. |
| 사용자가 페더레이션된 SSO 애플리케이션을 시작합니다.| 사용자가 애플리케이션에 자동으로 로그인됩니다. |
| 사용자가 처음으로 암호 SSO 애플리케이션을 시작합니다.| 사용자는 내 앱 확장을 설치해야 합니다. |
| 사용자가 다음 번에 암호 SSO 애플리케이션을 시작합니다.| 사용자가 애플리케이션에 자동으로 로그인됩니다. |
| 사용자가 Microsoft 365 포털에서 앱을 시작합니다.| 사용자가 애플리케이션에 자동으로 로그인됩니다. |
| 사용자가 Managed Browser에서 앱을 시작합니다.| 사용자가 애플리케이션에 자동으로 로그인됩니다. |

#### <a name="application-self-service-capabilities-test-case-examples"></a>애플리케이션 셀프 서비스 기능 테스트 사례 예

| 비즈니스 사례| 예상된 결과 |
| - | - |
| 사용자는 애플리케이션에 대한 멤버 자격을 관리할 수 있습니다.| 사용자는 앱에 대한 액세스 권한이 있는 구성원을 추가/제거할 수 있습니다. |
| 사용자는 애플리케이션을 편집할 수 있습니다.| 사용자는 암호 SSO 애플리케이션에 대한 애플리케이션 설명 및 자격 증명을 편집할 수 있습니다. |

### <a name="rollback-steps"></a>롤백 단계

배포가 계획대로 진행되지 않는 경우 수행할 작업을 계획하는 것이 중요합니다. 배포하는 동안 SSO 구성이 실패할 경우 [SSO 문제를 해결](../hybrid/tshoot-connect-sso.md)하고 사용자가 받는 영향을 줄이는 방법을 이해하고 있어야 합니다. 불가피하게 [SSO를 롤백](plan-sso-deployment.md)해야 할 수 있습니다.

## <a name="manage-your-implementation"></a>구현 관리

최소 권한 역할을 사용하여 Azure Active Directory 내에서 필요한 작업을 수행합니다. [사용 가능한 다른 역할을 검토](../roles/permissions-reference.md)하고 올바른 역할을 선택하여 이 애플리케이션의 각 가상 사용자에 대한 요구 사항을 해결합니다. 일부 역할은 배포를 완료한 후 임시로 적용해야 할 수도 있습니다.

| 가상 사용자| 역할| Azure AD 역할 |
| - | - | - |
| 기술 지원팀 관리자| 계층 1 지원| 없음 |
| ID 관리자| 문제가 Azure AD에 영향을 주는 경우 구성 및 디버그| 글로벌 관리자 |
| 애플리케이션 관리자| 애플리케이션의 사용자 증명, 권한이 있는 사용자의 구성| 없음 |
| 인프라 관리자| 인증서 롤오버 소유자| 글로벌 관리자 |
| 비즈니스 소유자/관련자| 애플리케이션의 사용자 증명, 권한이 있는 사용자의 구성| 없음 |

[Privileged Identity Management](../privileged-identity-management/pim-configure.md)를 사용하여 디렉터리 권한이 있는 사용자에게 추가 감사, 제어 및 액세스 검토를 제공할 역할을 관리할 수 있습니다.

## <a name="next-steps"></a>다음 단계

[Azure AD Multi-Factor Authentication 배포 계획](../authentication/howto-mfa-getstarted.md)

[애플리케이션 프록시 배포 계획](../app-proxy/application-proxy-deployment-plan.md)
