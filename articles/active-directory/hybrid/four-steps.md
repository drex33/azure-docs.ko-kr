---
title: 강력한 ID 기반을 위한 4단계 - Azure AD
description: 이 항목에서는 하이브리드 ID 고객이 강력한 ID 기반을 구축하기 위해 수행할 수 있는 네 가지 단계에 대해 설명합니다.
services: active-directory
author: martincoetzer
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/20/2019
ms.subservice: hybrid
ms.author: martinco
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6828f1c02bbcccebcd6b96305b051ebfc996c1a7
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130046224"
---
# <a name="four-steps-to-a-strong-identity-foundation-with-azure-active-directory"></a>Azure Active Directory를 사용하여 강력한 ID 기반을 구축하는 4단계

클라우드로 앱이 빠르게 전환하게 되면서 앱과 데이터에 대한 액세스 관리 작업은 경계 네트워크 및 방화벽과 같은 기존 네트워크 보안 경계 전략에 더 이상 의존할 수 없습니다. 이제 조직에서는 조직의 앱 및 데이터에 액세스할 수 있는 사용자와 대상을 제어하기 위해 ID 솔루션을 신뢰해야 합니다. 점점 더 많은 조직에서 직원들이 자신의 디바이스를 회사로 가져오고 인터넷에 연결할 수 있는 모든 곳에서 디바이스를 사용할 수 있도록 하고 있습니다. 이러한 디바이스가 규정을 준수하고 안전하게 보호되도록 하는 것은 조직이 구현할 ID 솔루션을 선택할 때 중요한 고려 사항이 되었습니다. 오늘날의 디지털 작업 공간에서 ID는 클라우드로 전환하는 모든 조직의 [기본 제어 평면입니다](https://www.microsoft.com/security/technology/identity-access-management?rtc=1).

Azure AD(Azure Active Directory) 하이브리드 ID 솔루션을 채택할 때 조직은 자동화, 위임, 셀프 서비스 및 Single Sign-On 기능을 통해 생산성을 높이는 프리미엄 기능에 액세스할 수 있습니다. 이를 통해 작업자는 작업을 수행해야 하는 어디에서든지 회사 리소스에 액세스할 수 있으며, IT 팀은 적절한 사람이 적절한 리소스에 대한 적절한 액세스 권한을 가지고, 보안 생산성을 설정할 수 있도록 하여 액세스를 제어할 수 있습니다.

학습한 내용을 토대로 작성한 이 모범 사례 검사 목록은 권장 작업을 신속하게 배포하여 조직에서 *강력한* ID 기반을 구축하는 데 도움이 됩니다.

* 앱에 쉽게 연결
* 모든 사용자에 대해 자동으로 하나의 ID 설정
* 보안을 유지하면서 사용자에게 권한 부여
* 인사이트 운용

## <a name="step-1---connect-to-apps-easily"></a>1단계 - 앱에 쉽게 연결

앱을 Azure AD와 연결함으로써 SSO(Single Sign-On)를 사용하도록 설정하여 최종 사용자 생산성 및 보안을 개선하고 사용자 프로비저닝을 수행할 수 있습니다. 단일 위치인 Azure AD에서 앱을 관리함으로써 관리 오버헤드를 최소화하고 보안 및 규정 준수 정책을 단일 지점에서 제어할 수 있습니다.

이 섹션에서는 앱에 대한 사용자 액세스를 관리하는 옵션, 내부 앱에 대한 보안 원격 액세스를 설정하는 옵션, 앱을 Azure AD로 마이그레이션할 때의 이점에 대해 설명합니다.

### <a name="make-apps-available-to-your-users-seamlessly"></a>사용자가 원활하게 앱을 사용할 수 있도록 하기

Azure AD를 사용하여 관리자는 [Azure Portal](https://portal.azure.com/)의 엔터프라이즈 애플리케이션 갤러리에 [애플리케이션을 추가](../manage-apps/add-application-portal.md)할 수 있습니다. 엔터프라이즈 애플리케이션 갤러리에 애플리케이션을 추가하면 Azure AD를 ID 공급자로 사용하도록 애플리케이션을 좀 더 쉽게 구성할 수 있습니다. 또한 조건부 액세스 정책을 사용하여 애플리케이션에 대한 사용자 액세스를 관리하고, 사용자가 암호를 반복적으로 입력할 필요가 없고 온-프레미스 및 클라우드 기반 애플리케이션 둘 다에 자동으로 로그인되도록 애플리케이션에 대한 SSO(Single Sign-On)를 구성할 수 있습니다.

애플리케이션이 Azure AD 갤러리에 추가되면 사용자는 할당된 앱을 보고, 필요에 따라 다른 앱을 검색하고 요청할 수 있습니다. Azure AD는 사용자가 앱에 액세스할 수 있는 [몇 가지 방법](../manage-apps/end-user-experiences.md)을 제공합니다.

* 액세스 패널/내 앱
* Microsoft 365 앱 시작 관리자
* 페더레이션된 앱에 직접 로그온
* 직접 로그온 링크

앱에 대한 사용자 액세스를 보다 자세히 알아보려면 이 문서의 **3단계 - 사용자에게 권한 부여** 를 참조하세요.

### <a name="migrate-apps-from-active-directory-federation-services-to-azure-ad"></a>Active Directory Federation Services에서 Azure AD로 앱 마이그레이션

ADFS(Active Directory Federation Services)에서 Azure AD로 Single Sign-On 구성을 마이그레이션하면 보안, 보다 일관된 관리 효율성 및 협업에 대한 추가 기능을 사용할 수 있습니다. 최적의 결과를 위해 AD FS에서 Azure AD로 앱을 마이그레이션하는 것이 좋습니다. 애플리케이션 인증 및 권한 부여를 Azure AD로 가져오면 다음과 같은 이점이 제공됩니다.

* 비용 관리
* 위험 관리
* 생산성 향상
* 규정 준수 및 거버넌스 처리

자세한 내용은 [Azure Active Directory로 애플리케이션 마이그레이션](https://aka.ms/migrateapps/whitepaper) 백서를 참조하세요.

### <a name="enable-secure-remote-access-to-apps"></a>앱에 대한 보안 원격 액세스 사용

[Azure AD 애플리케이션 프록시](../app-proxy/what-is-application-proxy.md)는 조직에서 내부 앱에 안전하게 액세스해야 하는 원격 사용자를 위해 클라우드에 온-프레미스 앱을 게시할 수 있는 간단한 솔루션을 제공합니다. Azure AD에 Single Sign-On 되면 사용자는 외부 URL 또는 내부 애플리케이션 포털을 통해 클라우드와 온-프레미스 애플리케이션에 모두 액세스할 수 있습니다.

Azure AD 애플리케이션 프록시는 다음과 같은 이점을 제공합니다.

* Azure AD를 온-프레미스 리소스로 확장
  * 클라우드 규모 보안 및 보호
  * 조건부 액세스 및 Multi-Factor Authentication과 같이 쉽게 사용할 수 있는 기능
* 경계 네트워크에 VPN 및 기존 역방향 프록시 솔루션과 같은 구성 요소가 없음
* 필요한 인바운드 연결 없음
* 클라우드 및 온-프레미스의 디바이스, 리소스 및 앱에 SSO(Single Sign-On) 지원
* 최종 사용자가 언제 어디서나 생산성을 높일 수 있도록 지원

### <a name="discover-shadow-it-with-microsoft-cloud-app-security"></a>Microsoft Cloud App Security로 섀도 IT 검색

오늘날의 기업에서 IT 부서는 종종 작업을 수행하는 사용자가 사용하는 일부 클라우드 응용 프로그램을 인식하지 못합니다. IT 관리자가 직원들이 사용하는 클라우드 앱의 수에 대한 질문을 받으면 평균적으로 30 또는 40개라고 말합니다. 실제로는 평균적으로 1,000개가 넘는 개별 앱을 조직의 직원이 사용하고 있습니다. 직원 중 80%는 누구도 검토한 적이 없고 보안 및 규정 준수 정책을 준수하지 않을 수도 있는 앱을 사용합니다.

[MCAS(Microsoft Cloud App Security)](/cloud-app-security/what-is-cloud-app-security)는 사용자에게 인기 있는 유용한 앱을 식별하여 IT 부서가 승인한 후 엔터프라이즈 애플리케이션 갤러리에 추가할 수 있도록 지원할 수 있습니다. 이를 통해 사용자들은 SSO 및 조건부 액세스와 같은 기능을 활용할 수 있습니다.

<em>"**Cloud App Security** 는 사람들이 Accenture를 보호하는 데 도움이 되는 기본 보안 정책을 지원하는 방식으로, 클라우드 및 SaaS 애플리케이션을 제대로 사용하고 있는지 확인하는 데 유용합니다."</em> --- [John Blasi, Accenture의 정보 보안 담당 관리 부장](https://customers.microsoft.com/story/accenture-professional-services-cloud-app-security)

섀도 IT를 검색하는 것 외에도 MCAS는 앱의 위험 수준을 결정하고, 회사 데이터에 대한 무단 액세스를 방지하고, 가능한 데이터 유출 및 애플리케이션에 내재된 기타 보안 위험을 방지할 수 있습니다.

## <a name="step-2---establish-one-identity-for-every-user-automatically"></a>2단계 - 모든 사용자에 대해 자동으로 하나의 ID 설정

온-프레미스 및 클라우드 기반 디렉터리를 Azure AD 하이브리드 ID 솔루션에 통합하면 클라우드에서 기존 ID를 프로비저닝하여 기존 온-프레미스 Active Directory 투자를 재활용할 수 있습니다. IT 부서에서 온-프레미스 Active Directory를 ID의 기본 데이터 소스로서 기존 거버넌스 솔루션과 함께 계속 실행하는 동안 이 솔루션은 온-프레미스 ID를 Azure AD와 동기화합니다. Microsoft의 Azure ID 하이브리드 솔루션은 온-프레미스 및 클라우드 기반 기능을 확장하며 이는 위치에 관계 없이 모든 리소스에 인증 및 권한 부여에 대한 공통 사용자 ID를 만듭니다.

Azure AD와 온-프레미스 디렉터리를 통합하면 온-프레미스 및 클라우드 리소스 모두에 액세스하기 위한 일반적인 ID를 제공하므로 사용자가 생산성을 더 높일 수 있으며, 여러 앱 및 서비스에서 여러 계정을 사용할 수 없게 됩니다. 여러 계정을 사용하는 것은 최종 사용자와 IT 부서 모두에 어려움을 초래합니다. 최종 사용자 관점에서 여러 계정을 사용한다는 것은 여러 암호를 기억해야 한다는 것을 의미합니다. 이를 방지하기 위해 많은 사용자가 각 계정에 대해 동일한 암호를 재사용하며, 이러한 방식이 보안 측면에서는 좋지 않습니다. IT 관점에서 재사용은 종종 더 많은 암호 재설정을 야기하며 최종 사용자 불만에 따른 지원 비용 증가도 초래합니다.

Azure AD Connect는 온-프레미스 ID를 Azure AD와 동기화하는 데 사용되는 도구로, 이러한 ID는 클라우드 애플리케이션에 액세스하는 데 사용할 수 있습니다. ID가 Azure AD에 있으면 Salesforce 또는 Concur와 같은 SaaS 애플리케이션에 프로비저닝할 수 있습니다.

이 섹션에서는 클라우드에 대해 고가용성, 최신 인증을 제공하고 온-프레미스 공간을 줄이기 위한 권장 사항을 나열합니다.

> [!NOTE]
> Azure AD Connect 대해 자세히 알아보려면 [Azure AD Connect 동기화란?](./how-to-connect-sync-whatis.md)을 참조하세요.

### <a name="set-up-a-staging-server-for-azure-ad-connect-and-keep-it-up-to-date"></a>Azure AD Connect를 위한 준비 서버를 설치하고 최신 상태로 유지

Azure AD Connect는 프로비저닝 프로세스에서 주요 역할을 수행합니다. 어떤 이유로든 동기화 서버가 오프라인 상태가 되면 온-프레미스에 대한 변경 내용이 클라우드에서 업데이트되지 않으며 사용자에 대한 액세스 문제가 발생할 수 있습니다. 동기화 서버가 오프라인 상태가 된 후 관리자가 신속하게 동기화를 다시 시작할 수 있도록 장애 조치(failover) 전략을 정의하는 것이 중요합니다.

기본 Azure AD Connect 서버가 오프라인 상태가 될 경우 고가용성을 제공하려면 Azure AD Connect를 위한 별도의 [준비 서버](./how-to-connect-sync-staging-server.md)를 배포하는 것이 좋습니다. 서버를 배포하면 관리자가 간단한 구성 전환을 통해 준비 서버를 프로덕션으로 "승격"할 수 있습니다. 또한 준비 모드에서 대기 서버를 구성하면 새 구성 변경 내용을 테스트 및 배포할 수 있고, 이전 구성 변경 내용을 서비스 해제하는 경우 새 서버를 도입할 수 있습니다.

> [!TIP]
> Azure AD Connect는 정기적으로 업데이트됩니다. 따라서 각 새 버전에서 제공하는 성능 향상, 버그 수정 및 새로운 기능을 활용하려면 스테이징 서버를 최신 상태로 유지하는 것이 좋습니다.

### <a name="enable-cloud-authentication"></a>클라우드 인증 사용

온-프레미스 Active Directory를 사용하는 조직은 Azure AD Connect를 사용하여 해당 디렉터리를 Azure AD로 확장하고 적절한 인증 방법을 구성해야 합니다. 조직에 대한 [올바른 인증 방법을 선택](./choose-ad-authn.md)하는 것은 앱을 클라우드로 전환하는 첫 번째 단계입니다. 모든 클라우드 데이터 및 리소스에 대한 액세스를 제어하기 때문에 중요한 구성 요소입니다.

Azure AD에서 온-프레미스 디렉터리 개체에 대해 클라우드 인증을 사용하도록 설정하는 가장 간단하고 권장되는 방법은 PHS([암호 해시 동기화](./how-to-connect-password-hash-synchronization.md))를 사용하도록 설정하는 것입니다. 또는 일부 조직에서는 PTA([통과 인증](./how-to-connect-pta-quick-start.md))를 사용하도록 설정하는 것을 고려할 수 있습니다.

PHS 또는 PTA 중 어떤 기능을 선택하든, 회사 네트워크에서 Windows 7 및 8 디바이스를 사용할 때 사용자가 앱에 사용자 이름과 암호를 지속적으로 입력하지 않고도 클라우드 앱에 액세스할 수 있도록 [Seamless Single Sign-on](./how-to-connect-sso.md)을 사용하도록 설정해야 합니다. Single Sign-On을 사용하지 않으면 사용자는 애플리케이션별 암호를 기억하고 각 애플리케이션마다 로그인해야 합니다. 마찬가지로, IT 직원은 Microsoft 365, Box 및 Salesforce와 같은 각 애플리케이션에 대한 사용자 계정을 만들고 업데이트해야 합니다. 사용자가 암호를 기억해야 하는 것과 동시에 각 애플리케이션에 로그인하는 시간도 소비됩니다. 최상의 사용자 환경, 위험 완화, 보고 기능 및 거버넌스를 위해 전체 엔터프라이즈에 표준화된 Single Sign-On 메커니즘을 제공하는 것이 중요합니다.

이미 AD FS 또는 다른 온-프레미스 인증 공급자를 사용하는 조직의 경우 Azure AD로 전환하면 ID 공급자가 복잡성을 줄이고 가용성을 향상시킬 수 있습니다. 페더레이션 사용에 대한 특정 사용 사례가 없는 경우 페더레이션 인증에서 PHS 및 Seamless SSO 또는 PTA 및 Seamless SSO로 마이그레이션하여 온-프레미스 공간을 줄이고 클라우드가 향상된 사용자 환경을 통해 제공하는 유연성을 활용하는 것이 좋습니다. 자세한 내용은 [Azure Active Directory를 페더레이션에서 암호 해시 동기화로 마이그레이션](./migrate-from-federation-to-cloud-authentication.md)을 참조하세요.

### <a name="enable-automatic-deprovisioning-of-accounts"></a>계정의 자동 프로비저닝 해제 사용

애플리케이션에 대한 자동화된 프로비저닝 및 프로비저닝 해제를 사용하도록 설정하는 것이 여러 시스템에서 ID의 수명 주기를 제어하는 가장 좋은 전략입니다. Azure AD는 ServiceNow 및 Salesforce와 같은 인기 있는 다양한 SaaS 애플리케이션과 [SCIM 2.0 프로토콜](../app-provisioning/use-scim-to-provision-users-and-groups.md)을 구현하는 다른 애플리케이션에 대해 사용자 계정의 [자동화된 정책 기반 프로비저닝 및 프로비저닝 해제](../app-provisioning/configure-automatic-user-provisioning-portal.md)를 지원합니다. 사용자 지정 코드 또는 CSV 파일의 수동 업로드가 필요한 기존 프로비저닝 솔루션과 달리, 이 프로비저닝 서비스는 클라우드에서 호스트되며 Azure Portal을 사용하여 설정 및 관리될 수 있는 사전 통합 커넥터를 제공합니다. 자동 프로비저닝 해제의 주요 이점은 사용자가 조직을 떠날 때 주요 SaaS 앱에서 사용자의 ID를 즉시 제거하여 조직을 보호할 수 있도록 한다는 것입니다.

자동 사용자 계정 프로비전 및 작동 방식에 대한 자세한 내용은 [Azure Active Directory를 사용하여 SaaS 애플리케이션에 사용자 프로비전 및 프로비전 해제 자동화](../app-provisioning/user-provisioning.md)를 참조하세요.

## <a name="step-3---empower-your-users-securely"></a>3단계 - 보안을 유지하면서 사용자에게 권한 부여

오늘날의 디지털 작업 공간에서는 보안과 생산성의 균형을 유지하는 것이 중요합니다. 그러나 최종 사용자는 생산성을 낮추면서 클라우드 앱에 액세스하는 보안 조치는 거부하는 경우가 많습니다. 이를 해결하기 위해 Azure AD는 관리 오버헤드를 최소화하면서 사용자가 생산성을 유지할 수 있도록 하는 셀프 서비스 기능을 제공합니다.

이 섹션에서는 보안을 유지하면서 사용자에게 권한을 부여하여 조직의 마찰을 제거하기 위한 권장 사항을 나열합니다.

### <a name="enable-self-service-password-reset-for-all-users"></a>모든 사용자에 대해 암호 재설정 셀프 서비스 사용

IT 관리자는 Azure의 [SSPR(셀프 서비스 암호 재설정)](../authentication/tutorial-enable-sspr.md)을 사용하여 사용자에게 해당 암호 또는 계정을 관리자 작업 없이 재설정하고 잠금 해제할 수 있도록 할 수 있습니다. 이 시스템에는 오용 또는 남용에 대해 경고하는 알림과 함께 사용자가 언제 시스템에 액세스하는지 추적하는 구체적인 보고서가 포함되어 있습니다.

기본적으로 Azure AD는 암호 재설정을 수행할 때 계정을 잠금 해제합니다. 그러나 [온-프레미스에서 Azure AD Connect 통합](../authentication/concept-sspr-howitworks.md#on-premises-integration)을 사용하도록 설정하는 경우 두 작업을 구분하여 사용자가 암호를 재설정하지 않고도 계정의 잠금을 해제할 수 있도록 하는 옵션도 있습니다.

### <a name="ensure-all-users-are-registered-for-mfa-and-sspr"></a>모든 사용자가 MFA 및 SSPR에 등록되었는지 확인

Azure는 사용자와 조직이 사용할 수 있는 보고서를 제공하여 사용자가 MFA 및 SSPR에 등록되었는지 확인하도록 합니다. 등록하지 않은 사용자는 프로세스에 대해 교육을 받아야 할 수 있습니다.

MFA [로그인 보고서](../authentication/howto-mfa-reporting.md)에는 MFA 사용에 대한 정보가 포함되어 있으며, 조직에서 MFA가 작동하는 방식에 대한 인사이트를 제공합니다. Azure AD에 대한 로그인 작업(및 감사와 위험 감지)에 액세스하는 것은 문제 해결, 사용량 현황 분석 및 법정 분석 조사에 중요합니다.

마찬가지로, [셀프 서비스 암호 관리 보고서](../authentication/howto-sspr-reporting.md)를 사용하여 SSPR에 등록하거나 등록하지 않은 사용자를 확인할 수 있습니다.

### <a name="self-service-app-management"></a>셀프 서비스 앱 관리

사용자가 액세스 패널에서 애플리케이션을 셀프 검색할 수 있도록 하려면 먼저 사용자가 셀프 검색을 수행하고 액세스 권한을 요청할 수 있게 하려는 모든 애플리케이션에 대해 [셀프 서비스 애플리케이션 액세스](../manage-apps/access-panel-manage-self-service-access.md)를 사용하도록 설정해야 합니다. 셀프 서비스 애플리케이션 액세스는 사용자가 애플리케이션을 직접 검색하도록 하고 경우에 따라 비즈니스 그룹이 해당 애플리케이션에 대한 액세스를 승인하도록 허용하는 유용한 방법입니다. 비즈니스 그룹이 해당 액세스 패널에서 [암호 Single Sign-On 애플리케이션](../manage-apps/troubleshoot-password-based-sso.md#automatically-capture-sign-in-fields-for-an-app) 권한에 대해 해당 사용자에게 할당된 자격 증명을 관리하도록 허용할 수 있습니다.

### <a name="self-service-group-management"></a>셀프 서비스 그룹 관리

사용자를 애플리케이션에 할당할 때 그룹을 사용하면 최적으로 매핑됩니다. 그룹을 사용하면 유연성이 향상되고 대규모로 관리할 수 있기 때문입니다.

* 동적 그룹 멤버 자격을 사용한 특성 기반
* 앱 소유자에게 위임

Azure AD는 보안 그룹 및 Microsoft 365 그룹을 사용하여 리소스에 대한 액세스를 관리하는 기능을 제공합니다. 이러한 그룹은 멤버 자격 요청을 승인 또는 거부하고 그룹 멤버 자격에 대한 제어를 위임할 수 있는 그룹 소유자가 관리할 수 있습니다. [셀프 서비스 그룹 관리](../enterprise-users/groups-self-service-management.md)로 알려진 이 기능을 사용하면 관리자가 해당 요청을 처리하길 의존하지 않고도 관리 역할에 할당되지 않은 그룹 소유자가 그룹을 만들고 관리할 수 있으므로 시간이 절감됩니다.

## <a name="step-4---operationalize-your-insights"></a>4단계: 인사이트 운용

보안 관련 이벤트 및 관련 경고의 감사 및 로깅은 사용자가 생산성을 유지하고 조직의 보안을 유지하기 위한 효율적인 전략의 필수 구성 요소입니다. 보안 로그 및 보고서를 통해 다음과 같은 질문에 대답할 수 있습니다.

* 비용을 지불하고 있는 기능을 사용하고 있나요?
* 내 테넌트에 의심스러운 상황이나 악의적인 문제가 발생하고 있나요?
* 보안 인시던트 중에 영향을 받는 사람은 누구인가요?

보안 로그 및 보고서는 의심스러운 활동에 대한 전자 기록을 제공하며, 네트워크의 외부 침투와 내부 공격의 시도 또는 성공을 나타낼 수 있는 패턴을 검색할 수 있도록 도움을 줍니다. 감사를 사용하여 사용자 활동을 모니터링하고, 규정 준수를 문서화하며, 법정 분석 등을 수행할 수 있습니다. 경고는 보안 이벤트의 알림을 제공합니다.

### <a name="assign-least-privileged-admin-roles-for-operations"></a>작업에 대해 최소 권한 관리자 역할 할당

작업 접근 방식과 관련해서 고려해야 할 몇 가지 관리 수준이 있습니다. 첫 번째 수준은 전역 관리자에게 관리 부담을 부과합니다. 항상 전역 관리자 역할을 사용하는 방식은 소규모 회사에 적합할 수 있습니다. 그러나 지원 센터 담당자와 특정 작업을 담당하는 관리자가 별도로 상주하는 대규모 조직의 경우 전역 관리자의 역할을 할당하면 해당 사용자에게 수행할 수 있어야 하는 작업을 초과하는 작업을 관리할 수 있는 능력이 제공되므로 보안 위험이 발생할 수 있습니다.

이 경우 다음 수준의 관리를 고려해야 합니다. Azure AD를 사용하면 최종 사용자를 낮은 권한 역할의 작업을 관리할 수 있는 "제한된 관리자"로 지정할 수 있습니다. 예를 들어, 읽기 전용 액세스를 통해 보안 관련 기능을 관리할 수 있도록 하기 위해 지원 센터 담당자에게 [보안 읽기 권한자](../roles/permissions-reference.md#security-reader) 역할을 할당할 수 있습니다. 또는 개인에게 [인증 관리자](../roles/permissions-reference.md#authentication-administrator) 역할을 할당하여 비암호 자격 증명을 다시 설정하거나 Azure Service Health를 읽고 구성하는 능력을 부여하는 것이 좋습니다.

자세히 알아보려면 [Azure Active Directory의 관리자 역할 권한](../roles/permissions-reference.md)을 참조하세요.

### <a name="monitor-hybrid-components-azure-ad-connect-sync-ad-fs-using-azure-ad-connect-health"></a>Azure AD Connect Health를 사용하여 하이브리드 구성 요소(Azure AD Connect 동기화, AD FS) 모니터링

Azure AD Connect 및 AD FS는 수명 주기 관리 및 인증을 중단하여 궁극적으로 작동 중단을 야기할 수 있는 중요한 구성 요소입니다. 따라서 이러한 구성 요소에 대한 모니터링 및 보고를 위해 Azure AD Connect Health를 배포해야 합니다.

자세히 알아보려면 [Azure AD Connect Health를 사용하여 AD FS 모니터링](./how-to-connect-health-adfs.md)을 읽어보세요.

### <a name="use-azure-monitor-to-collect-data-logs-for-analytics"></a>Azure Monitor를 사용하여 분석을 위한 데이터 로그 수집

[Azure Monitor](../../azure-monitor/overview.md)는 심층 인사이트, 고급 분석 및 스마트 기계 학습을 제공하는 모든 Azure AD 로그에 대한 통합 모니터링 포털입니다. Azure Monitor를 사용하여 포털 내에서 API를 통해 메트릭과 로그를 사용하고 리소스의 상태와 성능을 보다 자세히 파악할 수 있습니다. 이를 통해 기존 타사 SIEM 시스템을 지원하는 API 및 데이터 내보내기 옵션을 통해 다양한 제품 통합을 사용하도록 지원하면서 포털 내에서 단일 창 환경을 구현할 수 있습니다. 또한 Azure Monitor는 리소스에 영향을 주는 이슈에 대해 알림을 받거나 자동화된 작업을 수행하도록 경고 규칙을 구성하는 기능도 제공합니다.

![Azure Monitor](./media/four-steps/image1.png)

### <a name="create-custom-dashboards-for-your-leadership-and-your-day-to-day"></a>리더십 및 일상 업무에 대한 사용자 지정 대시보드 만들기

SIEM 솔루션이 없는 조직은 Azure AD용 [Power BI 콘텐츠 팩](../reports-monitoring/howto-use-azure-monitor-workbooks.md)을 다운로드할 수 있습니다. Power BI 콘텐츠 팩에는 사용자가 Azure AD 기능을 채택하고 사용하는 방법을 이해하는 데 도움이 되는 미리 작성된 보고서가 포함되어 있으므로 디렉터리 내의 모든 활동에 대한 정보를 얻는 데 유용합니다 사용자 고유의 [사용자 지정 대시보드](/power-bi/service-dashboards)를 만들고 리더십 팀과 공유하여 일상적인 활동을 보고할 수도 있습니다. 대시보드는 비즈니스를 모니터링하고, 가장 중요한 모든 메트릭을 한 눈에 볼 수 있는 유용한 방법입니다. 대시보드의 시각화는 하나 또는 여러 개의 기본 데이터 세트, 하나 또는 여러 개의 기본 보고서에서 제공될 수 있습니다. 대시보드는 온-프레미스 및 클라우드 데이터를 결합하여 데이터의 현재 위치에 관계없이 통합된 보기를 제공합니다.

![Power BI 사용자 지정 대시보드](./media/four-steps/image2.png)

### <a name="understand-your-support-call-drivers"></a>지원 전화 발생 이유 이해

이 문서에 설명된 대로 하이브리드 ID 솔루션을 구현하는 경우에는 궁극적으로 지원 전화 문의가 감소하는 것을 알 수 있습니다. 잊어버린 암호 및 계정 잠금과 같은 일반적인 이슈는 Azure의 셀프 서비스 암호 재설정을 구현하여 완화되지만, 셀프 서비스 애플리케이션 액세스를 사용하도록 설정하면 사용자가 IT 직원에 의존하지 않고 애플리케이션을 자동으로 검색하고 액세스를 요청할 수 있습니다.

지원 전화 문의가 감소하지 않는 경우 지원 전화 발생 이유를 분석하여 SSPR 또는 셀프 서비스 애플리케이션 액세스 권한이 올바르게 구성되었는지 또는 체계적으로 해결할 수 있는 다른 새로운 이슈가 있는지 확인하는 것이 좋습니다.

*"디지털 전환 과정에서 우리는 효과적인 에코시스템을 위한 당사, 파트너 및 클라우드 서비스 공급자 간의 원활하면서도 안전한 통합을 지원하기 위해 신뢰할 수 있는 ID 및 액세스 관리 공급자가 필요했습니다. Azure AD는 위험을 감지하고 대응할 수 있도록 하는 데 필요한 기능 및 가시성을 제공하는 가장 좋은 옵션입니다."* --- [Yazan Almasri, Aramex 글로벌 정보 보안 책임자](https://customers.microsoft.com/story/aramex-azure-active-directory-travel-transportation-united-arab-emirates-en)

### <a name="monitor-your-usage-of-apps-to-drive-insights"></a>앱 사용을 모니터링하여 인사이트 추진

섀도 IT를 검색하는 것 외에도 [Microsoft Cloud App Security](/cloud-app-security/what-is-cloud-app-security)를 사용하여 조직 전체의 앱 사용을 모니터링할 경우 클라우드 애플리케이션의 이점을 최대한 활용하기 위해 클라우드로 전환하는 조직에 큰 도움이 될 수 있습니다. 이를 통해 작업 상황을 보다 명확히 파악하면서 자산을 지속적으로 관리하고, 클라우드 애플리케이션 전반에 걸쳐 중요한 데이터를 안전하게 보호할 수 있습니다. MCAS를 사용하여 조직의 앱 사용 현황을 모니터링하면 다음 질문에 답변할 수 있습니다.

* 직원들은 데이터를 저장하기 위해 승인되지 않은 어떤 앱을 사용하고 있나요?
* 클라우드에서 중요한 데이터는 어디에서, 어떤 경우에 저장되나요?
* 클라우드에서 중요한 데이터에 액세스하는 사람은 누구인가요?

*"Cloud App Security를 사용하면 의심되는 상황을 신속하게 파악하고 조치를 취할 수 있습니다."* --- [Eric LePenske, Accenture 정보 보안 담당 수석 관리자](https://customers.microsoft.com/story/accenture-professional-services-cloud-app-security)

## <a name="summary"></a>요약

하이브리드 ID 솔루션 구현에는 여러 다양한 측면이 있지만 이 4단계 검사 목록은 사용자가 생산성을 높이고 보안을 유지할 수 있도록 하는 ID 인프라를 신속하게 구현하는 데 도움이 됩니다.

* 앱에 쉽게 연결
* 모든 사용자에 대해 자동으로 하나의 ID 설정
* 보안을 유지하면서 사용자에게 권한 부여
* 인사이트 운용

이 문서는 조직을 위한 강력한 ID 기반을 설정하는 데 유용한 로드맵입니다.

## <a name="identity-checklist"></a>ID 검사 목록

조직에서 보다 견고한 ID 토대로 전환을 시작하는 경우 참조를 위해 다음 검사 목록을 출력하는 것이 좋습니다.

### <a name="today"></a>오늘

|등록했나요?|항목|
|:-|:-|
||그룹에 대한 파일럿 SSPR(셀프 서비스 암호 재설정)|
||Azure AD Connect Health를 사용하여 하이브리드 구성 요소 모니터링|
||작업에 대해 최소 권한 관리자 역할 할당|
||Microsoft Cloud App Security로 섀도 IT 검색|
||Azure Monitor를 사용하여 분석을 위해 데이터 로그 수집|

### <a name="next-two-weeks"></a>다음 2주

|등록했나요?|항목|
|:-|:-|
||사용자가 앱을 사용할 수 있도록 만들기|
||선택한 SaaS 앱에 대한 Azure AD 프로비저닝 파일럿|
||Azure AD Connect를 위한 준비 서버를 설치하고 최신 상태로 유지|
||ADFS에서 Azure AD로 앱 마이그레이션 시작|
||리더십 및 일상 업무에 대한 사용자 지정 대시보드 만들기|

### <a name="next-month"></a>다음 달

|등록했나요?|항목|
|:-|:-|
||앱 사용을 모니터링하여 인사이트 추진|
||앱에 대한 보안 원격 액세스 파일럿|
||모든 사용자가 MFA 및 SSPR에 등록되었는지 확인|
||클라우드 인증 사용|

### <a name="next-three-months"></a>다음 3개월

|등록했나요?|항목|
|:-|:-|
||셀프 서비스 앱 관리 사용|
||셀프 서비스 그룹 관리 사용|
||앱 사용을 모니터링하여 인사이트 추진|
||지원 전화 발생 이유 이해|

## <a name="next-steps"></a>다음 단계

Azure Active Directory의 기능과 이 5단계 검사 목록을 사용하여 보안 태세를 높이는 방법을 알아봅니다. [ID 인프라를 보호하기 위한 5단계](../../security/fundamentals/steps-secure-identity.md)를 참조하세요.

Azure AD의 ID 기능을 통해 조직이 기존 온-프레미스 시스템에서 Azure AD로의 ID 관리 전환 방식을 빠르게 채택하고 전환을 가속화하도록 지원하는 방법을 알아봅니다. [Azure AD가 온-프레미스 워크로드에 대한 클라우드 통제 관리를 제공하는 방법](./cloud-governed-management-for-on-premises.md)을 참조하세요.