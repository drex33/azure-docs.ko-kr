---
title: 새로운 기능은 무엇입니까? 릴리스 정보 - Azure Active Directory | Microsoft Docs
description: Azure Active Directory의 최신 릴리스 정보, 알려진 문제, 버그 수정, 사용되지 않는 기능, 예정된 변경 내용 등을 알아봅니다.
services: active-directory
author: ajburnle
manager: daveba
featureFlags:
- clicktale
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 7/30/2021
ms.author: ajburnle
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: d723423fdb57ad60f5665be2d5fa0a760c9d770d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122536484"
---
# <a name="whats-new-in-azure-active-directory"></a>Azure Active Directory의 새로운 기능

>이 URL `https://docs.microsoft.com/api/search/rss?search=%22Release+notes+-+Azure+Active+Directory%22&locale=en-us`를 ![RSS 피드 판독기 아이콘](./media/whats-new/feed-icon-16x16.png) 피드 판독기에 복사하고 붙어넣어 업데이트를 위해 이 페이지를 다시 방문해야 할 때 알림을 받습니다.

Azure AD는 지속적인 향상되고 있습니다. 최신 개발 정보를 확인할 수 있도록 이 문서에서는 다음에 대한 정보를 제공합니다.

- 최신 릴리스
- 알려진 문제
- 버그 수정
- 사용되지 않는 기능
- 변경 계획

이 페이지는 매월 업데이트되므로 정기적으로 다시 방문해 주세요. 6개월이 넘은 항목을 찾으려는 경우 [Azure Active Directory의 새로운 기능 아카이브](whats-new-archive.md)에서 찾을 수 있습니다.

---
## <a name="july-2021"></a>2021년 7월

### <a name="new-google-sign-in-integration-for-azure-ad-b2c-and-b2b-self-service-sign-up-and-invited-external-users-will-stop-working-starting-july-12-2021"></a>Azure AD B2C 및 B2B 셀프 서비스 가입 및 초대된 외부 사용자에 대한 새 Google 로그인 통합은 2021년 7월 12일부터 작동을 중지합니다.

**유형:** 변경 계획  
**서비스 범주:** B2B  
**제품 기능:** B2B/B2C
 

이전에 [Gmaill 인증용 포함된 웹 보기에 대한 예외가 2021년 하반기에 만료된다](https://www.yammer.com/cepartners/threads/1188371962232832)고 발표했습니다.

2021년 7월 7일 Google에서 이러한 제한 사항 중 일부가 **2021년 7월 12일** 부터 적용된다는 것을 알게 되었습니다. 외부 사용자를 초대하거나 셀프 서비스 가입을 사용하도록 설정하기 위해 사용자 지정 또는 기간 업무 애플리케이션에서 새 Google ID 로그인을 설정한 Azure AD B2B 및 B2C 고객에게는 제한 사항이 즉시 적용됩니다. 따라서 인증을 시스템 웹 보기로 이동하지 않으면 최종 사용자에게 Gmail 로그인을 차단하는 오류 화면이 표시됩니다. 자세한 내용은 아래에 링크된 문서를 참조하세요. 

대부분의 앱은 기본적으로 시스템 웹 보기를 사용하며 이 변경의 영향을 받지 않습니다. 이는 포함된 웹 보기를 사용하는 고객에게만 적용됩니다(기본 설정이 아님). 새 Google 통합을 만들기 전에 대신 애플리케이션의 인증을 시스템 브라우저로 이동하는 것이 좋습니다. Gmail 인증을 위해 시스템 브라우저로 이동하는 방법을 알아보려면 [웹 브라우저 사용(MSAL.NET)](../develop/msal-net-web-browsers.md#embedded-vs-system-web-ui) 설명서의 '포함된 UI와 시스템 웹 UI 비교' 섹션을 참조하세요. 모든 MSAL SDK는 기본적으로 시스템 웹 보기를 사용합니다. [자세히 알아보기](../external-identities/google-federation.md#deprecation-of-web-view-sign-in-support).

---

### <a name="google-sign-in-on-embedded-web-views-expiring-september-30-2021"></a>2021년 9월 30일에 만료되는 포함된 웹 보기의 Google 로그인

**유형:** 변경 계획  
**서비스 범주:** B2B  
**제품 기능:** B2B/B2C
 

약 2개월 전에 Gmaill 인증용 포함된 웹 보기에 대한 예외가 2021년 하반기에 만료된다고 발표했습니다. 

최근 Google은 이 날짜를 **2021년 9월 30일** 로 지정했습니다. 

2021년 9월 30일부터 전 세계에 롤아웃되면 Gmail 계정으로 로그인하는 Azure AD B2B 게스트는 이제 코드를 별도의 브라우저 창에 입력하여 Microsoft Teams 모바일 및 데스크톱 클라이언트에서 로그인을 완료하라는 메시지가 표시됩니다. 이는 초대된 게스트뿐만 아니라 셀프 서비스 가입을 사용하여 가입한 게스트에도 적용됩니다. 

사용자 지정/기간 업무 앱에서 포함된 웹 보기 Gmail 인증을 설정했거나 기존 Google 통합이 있는 Azure AD B2C 고객은 더 이상 사용자가 Gmail 계정으로 로그인하도록 허용할 수 없습니다. 이를 완화하려면 시스템 브라우저를 로그인에 사용하도록 앱을 수정해야 합니다. 자세한 내용은 [웹 브라우저 사용(MSAL.NET)](../develop/msal-net-web-browsers.md#embedded-vs-system-web-ui) 설명서의 '포함된 UI와 시스템 웹 UI 비교' 섹션을 참조하세요. 모든 MSAL SDK는 기본적으로 시스템 웹 보기를 사용합니다. 

디바이스 로그인 흐름이 2021년 9월 30일에 롤아웃되기 시작하므로 해당 지역에는 아직 롤아웃되지 않을 수 있습니다(이 경우 해당 지역에 배포될 때까지 설명서에 표시된 오류 화면이 최종 사용자에게 표시됨). 

영향을 받는 알려진 시나리오와 사용자에게 필요한 환경에 대한 자세한 내용은 [Google을 B2B 게스트 사용자에 대한 ID 공급자로 추가](../external-identities/google-federation.md#deprecation-of-web-view-sign-in-support)를 참조하세요.

---

### <a name="bug-fixes-in-my-apps"></a>내 앱의 버그 수정

**유형:** 고정  
**서비스 범주:** 내 앱  
**제품 기능:** 최종 사용자 환경
 
- 이전에는 컬렉션을 사용하도록 권장하는 배너가 있어 콘텐츠가 헤더 뒤로 스크롤되었습니다. 이 문제가 해결되었습니다. 
- 이전에는 앱을 컬렉션에 추가할 때 모든 앱 컬렉션의 앱 순서가 임의로 다시 정렬되는 또 다른 문제가 있었습니다. 이 문제도 해결되었습니다. 

내 앱에 대한 자세한 내용은 [내 앱 포털에서 로그인하고 앱 시작](../user-help/my-apps-portal-end-user-access.md)을 참조하세요.

---

### <a name="public-preview----application-authentication-method-policies"></a>공개 미리 보기 - 애플리케이션 인증 방법 정책

**유형:** 새로운 기능  
**서비스 카테고리:** MS Graph  
**제품 기능:** 개발자 환경
 
MS Graph에서 애플리케이션 인증 방법 정책을 사용하면 IT 관리자가 애플리케이션 암호 비밀 자격 증명에 대한 수명을 적용하거나 비밀을 사용하지 못하도록 완전히 차단할 수 있습니다. 정책은 기본 구성으로 전체 테넌트에 적용할 수 있으며, 범위를 특정 애플리케이션 또는 서비스 주체로 지정할 수 있습니다. [자세히 알아보기](/graph/api/resources/policy-overview?view=graph-rest-1.0).
 
---

### <a name="public-preview----authentication-methods-nudge-to-download-microsoft-authenticator"></a>공개 미리 보기 - Microsoft Authenticator를 다운로드하도록 인증 방법을 조금 이동

**유형:** 새로운 기능  
**서비스 범주:** Microsoft Authenticator 앱  
**제품 기능:** 사용자 인증
 
Authenticator 조금 이동 정책은 사용자에게 Microsoft Authenticator 앱을 채택하라는 메시지를 표시하여 관리자가 조직을 더 안전한 상태로 전환하는 데 도움이 됩니다. 이 기능 이전에는 관리자가 사용자에게 Authenticator 앱을 설정하도록 푸시할 수 있는 방법이 없었습니다. 

조금 이동 기능은 조직 전체에서 원활하게 채택할 수 있도록 관리자가 조금 이동에서 사용자와 그룹을 포함하거나 제외하여 범위를 지정할 수 있는 기능과 함께 제공됩니다. [자세한 정보](../authentication/how-to-nudge-authenticator-app.md)
 
---

### <a name="public-preview----separation-of-duties-check"></a>공개 미리 보기 - 의무 분리 검사 

**유형:** 새로운 기능  
**서비스 카테고리:** 사용자 액세스 관리  
**제품 기능:** 권한 관리
 
Azure AD 권한 관리에서 관리자는 액세스 패키지가 다른 액세스 패키지 또는 그룹과 호환되지 않는다고 정의할 수 있습니다.  그러면 호환되지 않는 멤버 자격이 있는 사용자는 추가 액세스를 요청할 수 없습니다. [자세히 알아보기](../governance/entitlement-management-access-package-request-policy.md#prevent-requests-from-users-with-incompatible-access-preview).
 
---

### <a name="public-preview----identity-protection-logs-in-log-analytics-storage-accounts-and-event-hubs"></a>공개 미리 보기 - Log Analytics, Storage 계정 및 Event Hubs의 ID 보호 로그

**유형:** 새로운 기능  
**서비스 범주:** ID 보호  
**제품 기능:** ID 보안 및 보호
 
이제 Azure AD 블레이드의 진단 설정을 사용하여 위험 사용자 및 위험 검색 로그를 Azure Monitor, Storage 계정 또는 Log Analytics에 보낼 수 있습니다. [자세히 알아보기](../identity-protection/howto-export-risk-data.md).
 
---

### <a name="public-preview----application-proxy-api-addition-for-backend-ssl-certificate-validation"></a>공개 미리 보기 - 백 엔드 SSL 인증서 유효성 검사를 위한 애플리케이션 프록시 API 추가

**유형:** 새로운 기능  
**서비스 범주:** 응용 프로그램 프록시  
**제품 기능:** 액세스 제어
 
onPremisesPublishing 리소스 종류에는 이제 백 엔드 SSL 인증서 유효성 검사가 애플리케이션에 사용하도록 설정되었는지 여부를 나타내는 "isBackendCertificateValidationEnabled" 속성이 포함됩니다. 모든 새 애플리케이션 프록시 앱의 경우 속성은 기본적으로 true로 설정됩니다. 모든 기존 앱의 경우 속성은 false로 설정됩니다. 자세한 내용은 [onPremisesPublishing 리소스 종류](/graph/api/resources/onpremisespublishing?view=graph-rest-beta) API를 참조하세요.
 
---

### <a name="general-availability---improved-authenticator-setup-experience-for-add-azure-ad-account-in-microsoft-authenticator-app-by-directly-signing-into-the-app"></a>일반 공급 - Microsoft Authenticator 앱에 직접 로그인하여 이 앱에서 Azure AD 계정을 추가하도록 향상된 Authenticator 설정 환경

**유형:** 새로운 기능  
**서비스 범주:** Microsoft Authenticator 앱  
**제품 기능:** 사용자 인증
 
사용자는 이제 기존 인증 방법을 사용하여 Microsoft Authenticator 앱에 직접 로그인하여 자격 증명을 설정할 수 있습니다. 사용자는 더 이상 QR 코드를 스캔할 필요가 없으며, TAP(임시 액세스 패스) 또는 암호 + SMS(또는 기타 인증 방법)를 사용하여 Authenticator 앱에서 계정을 구성할 수 있습니다.

이렇게 하면 Microsoft Authenticator 앱에 대한 사용자 자격 증명 프로비전 프로세스가 향상되고 최종 사용자에게 앱을 프로비전할 수 있는 셀프 서비스 방법이 제공됩니다. [자세히 알아보기](../user-help/user-help-auth-app-add-work-school-account.md#sign-in-with-your-credentials).
 
---

### <a name="general-availability---set-manager-as-reviewer-in-azure-ad-entitlement-management-access-packages"></a>일반 공급 - Azure AD 권한 관리 액세스 패키지에서 관리자를 검토자로 설정

**유형:** 새로운 기능  
**서비스 카테고리:** 사용자 액세스 관리  
**제품 기능:** 권한 관리
 
Azure AD 권한 관리의 액세스 패키지는 이제 정기적으로 수행되는 액세스 검토를 위해 사용자의 관리자를 검토자로 설정할 수 있도록 지원합니다. [자세히 알아보기](../governance/entitlement-management-access-reviews-create.md).

---

### <a name="general-availability---enable-external-users-to-self-service-sign-up-in-aad-using-msa-accounts"></a>일반 공급 - 외부 사용자가 MSA 계정을 사용하여 AAD에서 셀프 서비스 가입을 사용하도록 설정

**유형:** 새로운 기능  
**서비스 범주:** B2B  
**제품 기능:** B2B/B2C
 
사용자는 이제 외부 사용자가 Microsoft 계정을 사용하여 Azure Active Directory에서 셀프 서비스 가입을 사용하도록 설정할 수 있습니다. [자세히 알아보기](../external-identities/microsoft-account.md).
 
---
 
### <a name="general-availability---external-identities-self-service-sign-up-with-email-one-time-passcode"></a>일반 공급 - 이메일 일회성 암호를 사용하여 External Identities 셀프 서비스 가입

**유형:** 새로운 기능  
**서비스 범주:** B2B  
**제품 기능:** B2B/B2C
 

사용자는 이제 외부 사용자가 자신의 이메일 및 일회성 암호를 사용하여 Azure Active Directory에서 셀프 서비스 가입을 사용하도록 설정할 수 있습니다. [자세히 알아보기](../external-identities/one-time-passcode.md).
 
---

### <a name="general-availability---anomalous-token"></a>일반 공급 - 비정상 토큰

**유형:** 새로운 기능  
**서비스 범주:** ID 보호  
**제품 기능:** ID 보안 및 보호
 
이제 ID 보호에서 비정상 토큰 검색을 사용할 수 있습니다. 이 기능은 시간 활성 및 익숙하지 않은 IP 주소의 인증과 같은 비정상적인 특성이 토큰에 있음을 검색할 수 있습니다. [자세히 알아보기](../identity-protection/concept-identity-protection-risks.md#sign-in-risk).
 
---

### <a name="general-availability---register-or-join-devices-in-conditional-access"></a>일반 공급 - 조건부 액세스에서 디바이스 등록 또는 조인

**유형:** 새로운 기능  
**서비스 범주:** 조건부 액세스  
**제품 기능:** ID 보안 및 보호
 
조건부 액세스의 디바이스 등록 또는 조인 사용자 작업이 이제 일반 공급됩니다. 이 사용자 작업을 통해 Azure AD 디바이스 등록에 대한 MFA(다단계 인증) 정책을 제어할 수 있습니다. 

현재 이 사용자 작업에서는 사용자가 Azure AD에 디바이스를 등록하거나 조인하는 경우 MFA를 시각적 개체로 사용하는 것만 가능합니다. Azure AD 디바이스 등록에 종속되거나 적용되지 않는 다른 제어는 이 사용자 작업을 통해 계속 사용하지 않도록 설정됩니다. [자세히 알아보기](../conditional-access/concept-conditional-access-cloud-apps.md#user-actions). 

---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---july-2021"></a>Azure AD 애플리케이션 갤러리의 새 프로비전 커넥터 - 2021년 7월

**유형:** 새로운 기능  
**서비스 범주:** 앱 프로비전  
**제품 기능**: 타사 통합
 
다음과 같은 통합된 새 앱에 대한 사용자 계정을 만들고, 업데이트하고, 삭제하는 작업을 자동화할 수 있습니다.

- [Clebex](../saas-apps/clebex-provisioning-tutorial.md)
- [Exium](../saas-apps/exium-provisioning-tutorial.md)
- [SoSafe](../saas-apps/sosafe-provisioning-tutorial.md)
- [Talentech](../saas-apps/talentech-provisioning-tutorial.md)
- [Thrive LXP](../saas-apps/thrive-lxp-provisioning-tutorial.md)
- [Vonage](../saas-apps/vonage-provisioning-tutorial.md)
- [Zip](../saas-apps/zip-provisioning-tutorial.md)
- [TimeClock 365](../saas-apps/timeclock-365-provisioning-tutorial.md)

자동화된 사용자 계정 프로비전을 사용하여 조직의 보안을 강화하는 방법에 대한 자세한 내용은 [Azure AD를 사용하여 SaaS 애플리케이션에 대한 사용자 프로비저닝 자동화](../app-provisioning/user-provisioning.md)를 참조하세요.

---

### <a name="changes-to-security-and-microsoft-365-group-settings-in-azure-portal"></a>Azure Portal의 보안 및 Microsoft 365 그룹 ​​설정 변경

**유형:** 변경된 기능  
**서비스 범주:** 그룹 관리  
**제품 기능:** 디렉터리
 

과거에는 사용자가 Azure Portal에서 보안 그룹과 Microsoft 365 그룹을 만들 수 있었습니다. 이제 사용자는 Azure Portal, PowerShell 및 API에서 그룹을 만들 수 있습니다. 고객은 조직에 대해 구성된 새 설정을 확인하고 업데이트해야 합니다. [자세한 정보](../enterprise-users/groups-self-service-management.md#group-settings).
 
---

### <a name="all-apps-collection-has-been-renamed-to-apps"></a>"모든 앱" 컬렉션의 이름이 "앱"으로 변경됨

**유형:** 변경된 기능  
**서비스 범주:** 내 앱  
**제품 기능:** 최종 사용자 환경
 
내 앱 포털에서 "모든 앱"이라는 컬렉션의 이름이 "앱"으로 변경되었습니다. 제품이 진화함에 따라 "앱"이 이 기본 컬렉션에 더 적합한 이름입니다. [자세히 알아보기](../manage-apps/my-apps-deployment-plan.md#plan-the-user-experience).
 
---
 
## <a name="june-2021"></a>2021년 6월

### <a name="context-panes-to-display-risk-details-in-identity-protection-reports"></a>ID 보호 보고서에 위험 세부 정보를 표시하는 컨텍스트 창

**유형:** 변경 계획  
**서비스 범주:** ID 보호  
**제품 기능:** ID 보안 및 보호
 
ID 보호의 위험 사용자, 위험한 로그인 및 위험 검색 보고서의 경우 2021년 7월부터 선택한 항목의 위험 세부 정보가 페이지의 오른쪽에 나타나는 컨텍스트 창에 표시됩니다. 변경 내용은 사용자 인터페이스에만 영향을 주며 기존 기능에는 영향을 주지 않습니다. 이러한 기능의 기능성에 대한 자세한 내용은 [방법: 위험 조사](../identity-protection/howto-identity-protection-investigate-risk.md)를 참조하세요.
 
---

### <a name="public-preview----create-azure-ad-access-reviews-of-service-principals-that-are-assigned-to-privileged-roles"></a>공개 미리 보기 - 권한 있는 역할에 할당된 서비스 주체의 Azure AD 액세스 검토 만들기

**유형:** 새로운 기능  
**서비스 범주:** 액세스 검토  
**제품 기능:** ID 거버넌스
 
 Azure AD 액세스 검토를 사용하여 Azure AD 및 Azure 리소스 역할에 대한 서비스 주체의 액세스를 검토할 수 있습니다. [자세히 알아보기](../privileged-identity-management/pim-how-to-start-security-review.md#open-access-reviews).
 
---

### <a name="public-preview----group-owners-in-azure-ad-can-create-and-manage-azure-ad-access-reviews-for-their-groups"></a>공개 미리 보기 - Azure AD의 그룹 소유자는 해당 그룹에 대한 Azure AD 액세스 검토를 만들고 관리할 수 있음

**유형:** 새로운 기능  
**서비스 범주:** 액세스 검토  
**제품 기능:** ID 거버넌스
 
Azure AD의 그룹 소유자는 이제 해당 그룹에 대한 Azure AD 액세스 검토를 만들고 관리할 수 있습니다. 이 기능은 테넌트 관리자가 Azure AD 액세스 검토 설정을 통해 사용하도록 설정할 수 있으며, 기본적으로 사용하지 않도록 설정됩니다. [자세히 알아보기](../governance/create-access-review.md#allow--group-owners-to-create-and-manage-access-reviews-preview).
 
---

### <a name="public-preview----customers-can-scope-access-reviews-of-privileged-roles-to-just-users-with-eligible-or-active-access"></a>공개 미리 보기 - 고객은 권한 있는 역할에 대한 액세스 검토 범위를 적격 또는 활성 액세스 권한이 있는 사용자로만 지정할 수 있음

**유형:** 새로운 기능  
**서비스 범주:** 액세스 검토  
**제품 기능:** ID 거버넌스
 
관리자는 권한 있는 역할의 할당에 대한 액세스 검토를 만들 때 검토 범위를 할당된 적격 또는 활성 사용자로만 지정할 수 있습니다. [자세히 알아보기](../privileged-identity-management/pim-how-to-start-security-review.md).
 
---

### <a name="public-preview----microsoft-graph-apis-for-mobility-mdmmam-management-policies"></a>공개 미리 보기 - 모바일(MDM/MAM)용 Microsoft Graph API 관리 정책

**유형:** 새로운 기능  
**서비스 범주:** 기타  
**제품 기능:** 디바이스 수명 주기 관리
 
Azure AD의 모바일(MDM/MAM) 구성에 대한 Microsoft Graph 지원은 공개 미리 보기에 있습니다. 관리자는 Microsoft Graph v1.0을 사용하여 Intune과 같은 MDM 애플리케이션에 대한 사용자 범위 및 URL을 구성할 수 있습니다. 자세한 내용은 [mobilityManagementPolicy 리소스 종류](/graph/api/resources/mobilitymanagementpolicy?view=graph-rest-beta)를 참조하세요.

---

### <a name="general-availability---custom-questions-in-access-package-request-flow-in-azure-active-directory-entitlement-management"></a>일반 공급 - Azure Active Directory 권한 관리의 액세스 패키지 요청 흐름에서 사용자 지정 질문

**유형:** 새로운 기능  
**서비스 카테고리:** 사용자 액세스 관리  
**제품 기능:** 권한 관리
 
Azure AD 권한 관리는 이제 액세스 패키지 요청 흐름에서 사용자 지정 질문을 만들도록 지원합니다. 이 기능을 사용하면 액세스 패키지 정책에서 사용자 지정 질문을 구성할 수 있습니다. 이러한 질문은 액세스 요청 프로세스의 일환으로 대답을 입력할 수 있는 요청자에게 표시됩니다. 이러한 대답은 승인자에게 표시되어 액세스 요청에 대해 더 나은 결정을 내릴 수 있는 유용한 정보를 제공합니다. [자세히 알아보기](../governance/entitlement-management-access-package-create.md#add-requestor-information-to-an-access-package).

---

### <a name="general-availability---multi-geo-sharepoint-sites-as-resources-in-entitlement-management-access-packages"></a>일반 공급 - 권한 관리 액세스 패키지의 리소스로 사용되는 다중 지역 SharePoint 사이트

**유형:** 새로운 기능  
**서비스 카테고리:** 사용자 액세스 관리  
**제품 기능:** 권한 관리
 
권한 관리의 액세스 패키지는 이제 SharePoint Online에서 다중 지역 기능을 사용하는 고객을 위해 다중 지역 SharePoint 사이트를 지원합니다. [자세히 알아보기](../governance/entitlement-management-catalog-create.md#add-a-multi-geo-sharepoint-site).
 
---

### <a name="general-availability---knowledge-admin-and-knowledge-manager-built-in-roles"></a>일반 공급 - 지식 수석 관리자 및 지식 관리자 기본 제공 역할

**유형:** 새로운 기능  
**서비스 카테고리:** RBAC  
**제품 기능:** 액세스 제어
 
지식 수석 관리자 및 지식 관리자라는 새로운 두 가지 역할이 이제 일반 공급됩니다.

- 지식 수석 관리자 역할의 사용자는 Microsoft 365 관리자 센터의 모든 조직 지식 설정에 대한 전체 액세스 권한을 갖습니다. 항목 및 머리글자어와 같은 콘텐츠를 만들고 관리할 수 있습니다. 또한 콘텐츠 센터를 만들고, 서비스 상태를 모니터링하고, 서비스 요청을 만들 수 있습니다. [자세한 정보](../roles/permissions-reference.md#knowledge-administrator)
- 지식 관리자 역할의 사용자는 콘텐츠를 만들고 관리할 수 있으며, 주로 지식의 품질과 구조를 담당합니다. 항목을 확인하거나, 편집 내용을 승인하거나, 항목을 삭제할 수 있는 항목 관리 작업에 대한 모든 권한을 갖습니다. 이 역할은 용어 저장소 관리 도구의 일부로 분류를 관리하고 콘텐츠 센터를 만들 수도 있습니다. [자세히 알아보기](../roles/permissions-reference.md#knowledge-manager).

---

### <a name="general-availability---cloud-app-security-administrator-built-in-role"></a>일반 공급 - Cloud App Security 관리자 기본 제공 역할

**유형:** 새로운 기능  
**서비스 카테고리:** RBAC  
**제품 기능:** 액세스 제어
 
 이 역할의 사용자는 Cloud App Security에서 모든 권한을 갖습니다. 관리자를 추가하고, MCAS(Microsoft Cloud App Security) 정책 및 설정을 추가하며, 로그를 업로드하고, 거버넌스 작업을 수행할 수 있습니다. [자세히 알아보기](../roles/permissions-reference.md#cloud-app-security-administrator).
 
---

### <a name="general-availability---windows-update-deployment-administrator"></a>일반 공급 - Windows 업데이트 배포 관리자

**유형:** 새로운 기능  
**서비스 카테고리:** RBAC  
**제품 기능:** 액세스 제어
 

 이 역할의 사용자는 비즈니스용 Windows 업데이트 배포 서비스를 통해 Windows 업데이트 배포의 모든 측면을 만들고 관리할 수 있습니다. 배포 서비스를 사용하면 사용자가 업데이트를 배포하는 시기와 방법에 대한 설정을 정의할 수 있습니다. 또한 사용자는 테넌트의 디바이스 그룹에 제공되는 업데이트를 지정할 수 있습니다. 사용자가 업데이트 진행 상황도 모니터링할 수 있도록 합니다. [자세히 알아보기](../roles/permissions-reference.md#windows-update-deployment-administrator).
 
---

### <a name="general-availability---multi-camera-support-for-windows-hello"></a>일반 공급 - Windows Hello에 대한 다중 카메라 지원

**유형:** 새로운 기능  
**서비스 범주:** 인증(로그인)  
**제품 기능:** 사용자 인증
 
이제 Windows 10 21H1 업데이트를 통해 Windows Hello에서 여러 대의 카메라를 지원합니다. 기본 제공 카메라와 외부 카메라가 모두 있는 경우 업데이트에는 외부 카메라를 사용할 수 있는 기본값이 포함됩니다. [자세히 알아보기](https://techcommunity.microsoft.com/t5/windows-it-pro-blog/it-tools-to-support-windows-10-version-21h1/ba-p/2365103).

---
 
### <a name="general-availability---access-reviews-ms-graph-apis-now-in-v10"></a>일반 공급 - 이제 v1.0에서 액세스 검토 MS Graph API를 지원함

**유형:** 새로운 기능  
**서비스 범주:** 액세스 검토  
**제품 기능:** ID 거버넌스
 
Azure Active Directory 액세스 검토 MS Graph API는 이제 v1.0에서 완전히 구성 가능한 액세스 검토 기능을 지원합니다. [자세히 알아보기](/graph/api/resources/accessreviewsv2-root?view=graph-rest-1.0).
 
---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---june-2021"></a>Azure AD 애플리케이션 갤러리의 새 프로비전 커넥터 - 2021년 6월

**유형:** 새로운 기능  
**서비스 범주:** 앱 프로비전  
**제품 기능**: 타사 통합
 
다음과 같은 통합된 새 앱에 대한 사용자 계정을 만들고, 업데이트하고, 삭제하는 작업을 자동화할 수 있습니다.

- [askSpoke](../saas-apps/askspoke-provisioning-tutorial.md)
- [Cloud Academy - SSO](../saas-apps/cloud-academy-sso-provisioning-tutorial.md)
- [CheckProof](../saas-apps/checkproof-provisioning-tutorial.md)
- [GoLinks](../saas-apps/golinks-provisioning-tutorial.md)
- [Holmes Cloud](../saas-apps/holmes-cloud-provisioning-tutorial.md)
- [H5mag](../saas-apps/h5mag-provisioning-tutorial.md)
- [LimbleCMMS](../saas-apps/limblecmms-provisioning-tutorial.md)
- [LogMeIn](../saas-apps/logmein-provisioning-tutorial.md)
- [SECURE DELIVER](../saas-apps/secure-deliver-provisioning-tutorial.md)
- [Sigma Computing](../saas-apps/sigma-computing-provisioning-tutorial.md)
- [Smallstep SSH](../saas-apps/smallstep-ssh-provisioning-tutorial.md)
- [Tribeloo](../saas-apps/tribeloo-provisioning-tutorial.md)
- [Twingate](../saas-apps/twingate-provisioning-tutorial.md)

자세한 내용은 [Azure AD를 사용하여 SaaS 애플리케이션의 사용자를 자동으로 프로비저닝](../app-provisioning/user-provisioning.md)을 참조하세요.
 
---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---june-2021"></a>Azure AD 애플리케이션 갤러리에서 사용할 수 있는 새 페더레이션된 앱 - 2021년 6월

**유형:** 새로운 기능  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능**: 타사 통합
 
2021년 6월에 페더레이션 지원을 통해 42개의 새 애플리케이션이 앱 갤러리에 추가되었습니다.

[Taksel](https://app.taksel.it/admin/integrations), [IDrive360](../saas-apps/idrive360-tutorial.md), [VIDA](../saas-apps/vida-tutorial.md), [ProProfs Classroom](../saas-apps/proprofs-classroom-tutorial.md), [WAN-Sign](../saas-apps/wan-sign-tutorial.md), [Citrix Cloud SAML SSO](../saas-apps/citrix-cloud-saml-sso-tutorial.md), [Fabric](../saas-apps/fabric-tutorial.md), [DssAD](https://cloudlicensing.deepseedsolutions.com/), [RICOH Creative Collaboration RICC](https://www.ricoh-europe.com/products/software-apps/collaboration-board-software/ricc/), [Styleflow](../saas-apps/styleflow-tutorial.md), [Chaos](https://accounts.chaosgroup.com/corporate_login), [Traced Connector](https://control.traced.app/signup), [Squarespace](https://account.squarespace.com/org/azure), [MX3 Diagnostics Connector](https://mx3www.playground.dynuddns.com/signin-oidc), [Ten Spot](https://tenspot.co/api/v1/sso/azure/login/), [Finvari](../saas-apps/finvari-tutorial.md), [Mobile4ERP](https://play.google.com/store/apps/details?id=com.negevsoft.mobile4erp), [WalkMe US OpenID Connect](https://www.walkme.com/), [Neustar UltraDNS](../saas-apps/neustar-ultradns-tutorial.md), [cloudtamer.io](../saas-apps/cloudtamer-io-tutorial.md), [A Cloud Guru](../saas-apps/a-cloud-guru-tutorial.md), [PetroVue](../saas-apps/petrovue-tutorial.md), [Postman](../saas-apps/postman-tutorial.md), [ReadCube Papers](../saas-apps/readcube-papers-tutorial.md), [Peklostroj](https://app.peklostroj.cz/), [SynCloud](https://onboard.syncloud.io/), [Polymerhq.io](https://www.polymerhq.io/), [Bonos](../saas-apps/bonos-tutorial.md), [Astra Schedule](../saas-apps/astra-schedule-tutorial.md), [Draup](../saas-apps/draup-inc-tutorial.md), [Inc](../saas-apps/draup-inc-tutorial.md), [Applied Mental Health](../saas-apps/applied-mental-health-tutorial.md), [iHASCO Training](../saas-apps/ihasco-training-tutorial.md), [Nexsure](../saas-apps/nexsure-tutorial.md), [XEOX](https://login.xeox.com/), [Plandisc](https://create.plandisc.com/account/logon), [foundU](../saas-apps/foundu-tutorial.md), [Standard for Success Accreditation](../saas-apps/standard-for-success-accreditation-tutorial.md), [Penji Teams](https://web.penjiapp.com/), [CheckPoint Infinity Portal](../saas-apps/checkpoint-infinity-portal-tutorial.md), [Teamgo](../saas-apps/teamgo-tutorial.md), [Hopsworks.ai](../saas-apps/hopsworks-ai-tutorial.md), [HoloMeeting 2](https://backend2.holomeeting.io/)

여기(https://aka.ms/AppsTutorial )에서 모든 애플리케이션의 설명서를 찾을 수도 있습니다.

Azure AD 앱 갤러리에서 애플리케이션을 나열하려면 여기에서 세부 정보를 참조하세요. https://aka.ms/AzureADAppRequest
 
---

### <a name="device-code-flow-now-includes-an-app-verification-prompt"></a>디바이스 코드 흐름에는 이제 앱 확인 프롬프트가 포함됨

**유형:** 변경된 기능  
**서비스 범주:** 인증(로그인)  
**제품 기능:** 사용자 인증
 
[디바이스 코드 흐름](../develop/v2-oauth2-device-code.md)이 하나의 추가 사용자 프롬프트를 포함하도록 업데이트되었습니다. 로그인하는 동안 로그인하는 앱의 유효성을 검사하라는 메시지가 사용자에게 표시됩니다.  이 프롬프트는 피싱 공격을 받지 않도록 합니다. [자세히 알아보기](../develop/reference-breaking-changes.md#the-device-code-flow-ux-will-now-include-an-app-confirmation-prompt).
 
---

### <a name="user-last-sign-in-date-and-time-is-now-available-on-azure-portal"></a>이제 Azure Portal에서 사용자의 마지막 로그인 날짜 및 시간을 사용할 수 있음

**유형:** 변경된 기능  
**서비스 카테고리:** 사용자 관리  
**제품 기능:** 사용자 관리
 
이제 Azure Portal에서 사용자의 마지막 로그인 날짜 및 타임스탬프를 볼 수 있습니다. 사용자 프로필 페이지에서 각 사용자에 대한 정보를 사용할 수 있습니다. 이 정보는 비활성 사용자를 식별하고 위험 이벤트를 효과적으로 관리하는 데 도움이 됩니다. [자세히 알아보기](./active-directory-users-profile-azure-portal.md?context=%2fazure%2factive-directory%2fenterprise-users%2fcontext%2fugr-context).
 
---

### <a name="mim-bhold-suite-impact-of-end-of-support-for-microsoft-silverlight"></a>Microsoft Silverlight 지원 종료는 MIM BHOLD 제품군에 영향을 줌

**유형:** 변경된 기능  
**서비스 범주:** Microsoft Identity Manager  
**제품 기능:** ID 거버넌스
 
Microsoft Silverlight 지원이 2021년 10월 12일에 종료됩니다. 이 변경은 Microsoft BHOLD 제품군을 사용하는 고객에게만 영향을 주며, 다른 Microsoft Identity Manager 시나리오에는 영향을 주지 않습니다. 자세한 내용은 [Silverlight 지원 종료](https://support.microsoft.com/windows/silverlight-end-of-support-0a3be3c7-bead-e203-2dfd-74f0a64f1788)를 참조하세요.  

Microsoft Silverlight를 브라우저에 설치하지 않은 사용자는 Silverlight가 필요한 BHOLD 제품군 모듈을 사용할 수 없습니다. 여기에는 BHOLD 모델 생성기, BHOLD FIM 셀프 서비스 통합 및 BHOLD 분석이 포함됩니다.  이러한 모듈 중 하나 이상의 기존 BHOLD 배포가 있는 고객은 2021년 10월까지 BHOLD 서버 컴퓨터에서 해당 모듈을 제거할 계획을 수립해야 합니다. 또한 이전에 해당 BHOLD 배포와 상호 작용한 모든 사용자 컴퓨터에서 Silverlight를 제거할 계획도 수립해야 합니다.
 
---

### <a name="my-experiences-end-of-support-for-internet-explorer-11"></a>내* 환경: Internet Explorer 11 지원 종료

**유형:** 사용되지 않음  
**서비스 범주:** 내 앱  
**제품 기능:** 최종 사용자 환경
 

Microsoft 365 및 기타 앱은 2021년 8월 21일에 Internet Explorer 11에 대한 지원을 종료하며, 여기에는 내* 환경이 포함됩니다. Internet Explorer를 통해 액세스하는 내*에서 버그 수정 또는 업데이트를 받지 못하므로 문제가 발생할 수 있습니다. 이러한 날짜는 Edge 팀에서 결정하고 있으며 변경될 수 있습니다. [자세히 알아보기](https://blogs.windows.com/windowsexperience/2021/05/19/the-future-of-internet-explorer-on-windows-10-is-in-microsoft-edge/).
 
---

### <a name="planned-deprecation---malware-linked-ip-address-detection-in-identity-protection"></a>예정된 사용 중단 - ID 보호의 맬웨어 연결 IP 주소 검색

**유형:** 사용되지 않음  
**서비스 범주:** ID 보호  
**제품 기능:** ID 보안 및 보호
 
2021년 10월 1일부터 Azure AD ID 보호는 더 이상 "맬웨어 연결 IP 주소" 검색을 생성하지 않습니다. 아무 작업도 필요하지 않으며, 고객은 ID 보호에서 제공하는 다른 검색으로 계속 보호됩니다. 보호 정책에 대한 자세한 내용은 [ID 보호 정책](../identity-protection/concept-identity-protection-policies.md)을 참조하세요.
 
---
 
## <a name="may-2021"></a>2021년 5월

### <a name="public-preview----azure-ad-verifiable-credentials"></a>공개 미리 보기 - Azure AD 확인 가능한 자격 증명

**유형:** 새로운 기능  
**서비스 범주:** 기타  
**제품 기능:** 사용자 인증
 
Azure AD 고객은 이제 확인 가능한 자격 증명을 쉽게 설계하고 발급할 수 있습니다. 확인 가능한 자격 증명을 사용하여 개인 정보 보호를 준수하면서 고용, 교육 또는 기타 클레임을 증명할 수 있습니다. 어떤 사람이든, 어떤 기업이든 해당 정보가 유효한지 디지털로 확인할 수 있습니다. [자세한 정보](../verifiable-credentials/index.yml).

---

### <a name="public-preview----device-code-flow-now-includes-an-app-verification-prompt"></a>공개 미리 보기 - 디바이스 코드 흐름에 앱 확인 프롬프트가 포함됨

**유형:** 새로운 기능  
**서비스 범주:** 사용자 인증  
**제품 기능:** 인증(로그인)
 
향상된 보안 기능인 [디바이스 코드 흐름](../develop/v2-oauth2-device-code.md)이 사용자가 필요한 앱에 로그인하고 있는지 확인하는 다른 프롬프트를 포함하도록 업데이트되었습니다. 롤아웃은 6월에 시작하여 6월 30일까지 완료될 예정입니다.

공격자가 사용자를 악의적인 애플리케이션에 로그인하도록 속이는 피싱 공격을 방지하기 위해 "[애플리케이션 표시 이름]에 로그인하려고 하시나요?"라는 메시지가 추가됩니다. 디바이스 코드 흐름을 사용하여 로그인하는 모든 사용자에게 이 메시지가 표시됩니다. 보안 조치이므로 제거하거나 무시할 수 없습니다. [자세한 정보](../develop/reference-breaking-changes.md#the-device-code-flow-ux-will-now-include-an-app-confirmation-prompt).

---

### <a name="public-preview----build-and-test-expressions-for-user-provisioning"></a>공개 미리 보기 - 사용자 프로비저닝을 위한 식 작성 및 테스트

**유형:** 새로운 기능  
**서비스 범주:** 앱 프로비전  
**제품 기능:** ID 수명 주기 관리
 
식 작성기를 사용하면 전체 동기화 주기를 기다리지 않고도 식을 만들고 테스트할 수 있습니다. [자세한 정보](../app-provisioning/functions-for-customizing-application-data.md). 

---

### <a name="public-preview----enhanced-audit-logs-for-conditional-access-policy-changes"></a>공개 미리 보기 - 조건부 액세스 정책 변경에 대한 향상된 감사 로그

**유형:** 새로운 기능  
**서비스 범주:** 조건부 액세스  
**제품 기능:** ID 보안 및 보호
 
조건부 액세스 관리의 중요한 측면은 시간에 따른 정책 변경을 이해하는 것입니다. 정책 변경으로 인해 최종 사용자에게 중단이 발생할 수 있으므로 변경 로그를 유지하고 관리자가 이전 정책 버전으로 되돌릴 수 있도록 지원하는 것이 중요합니다. 

정책을 변경한 사람과 시기를 보여 주는 것 외에도 감사 로그에는 이제 수정된 속성 값도 포함됩니다. 이렇게 변경하면 관리자가 변경된 할당, 조건 또는 제어를 더 쉽게 파악할 수 있습니다. 정책의 이전 버전으로 되돌리려면 이전 버전의 JSON 표현을 복사하고, 조건부 액세스 API를 사용하여 정책을 이전 상태로 변경할 수 있습니다. [자세히 알아보기](../conditional-access/concept-conditional-access-policies.md).

---

### <a name="public-preview----sign-in-logs-include-authentication-methods-used-during-sign-in"></a>공개 미리 보기 - 로그인 로그에는 로그인 중에 사용된 인증 방법이 포함됨

**유형:** 새로운 기능  
**서비스 범주:** MFA  
**제품 기능:** 모니터링 및 보고
 

이제 관리자는 로그인하는 동안 사용된 인증 방법을 비롯하여 사용자가 로그인하는 데 사용한 순차적 단계를 볼 수 있습니다. 

이러한 세부 정보에 액세스하려면 Azure AD 로그인 로그로 이동하여 로그인을 선택한 다음, 인증 방법 세부 정보 탭으로 이동합니다. 여기에는 사용된 방법, 방법에 대한 세부 정보(예: 전화 번호, 전화 이름), 충족된 인증 요구 사항 및 결과 세부 정보와 같은 정보가 포함되어 있습니다. [자세히 알아보기](../reports-monitoring/concept-sign-ins.md).

---

### <a name="public-preview----pim-adds-support-for-abac-conditions-in-azure-storage-roles"></a>공개 미리 보기 - PIM에서 Azure Storage 역할의 ABAC 조건에 대한 지원을 추가함

**유형:** 새로운 기능  
**서비스 범주:** Privileged Identity Management  
**제품 기능:** Privileged Identity Management
 
특정 Azure RBAC 역할에 대한 특성 기반 액세스 제어의 퍼블릭 미리 보기와 함께, 적격 할당을 위한 ABAC 조건을 Privileged Identity Management 내에서 추가할 수도 있습니다. [자세한 정보](../../role-based-access-control/conditions-overview.md#conditions-and-privileged-identity-management-pim).

---

### <a name="general-availability---conditional-access-and-identity-protection-reports-in-b2c"></a>일반 공급 - B2C의 조건부 액세스 및 ID 보호 보고서

**유형:** 새로운 기능  
**서비스 범주:** B2C - 소비자 ID 관리  
**제품 기능:** B2B/B2C

이제 B2C는 B2C(비즈니스 대 소비자) 앱 및 사용자를 위한 조건부 액세스 및 ID 보호를 지원합니다. 이를 통해 고객은 세분화된 위험 및 위치 기반 액세스 제어를 통해 사용자를 보호할 수 있습니다. 이러한 기능을 통해 고객은 이제 신호를 살펴보고 고객에게 더 많은 보안 및 액세스를 제공하는 정책을 만들 수 있습니다. [자세한 정보](../../active-directory-b2c/conditional-access-identity-protection-overview.md).

---

### <a name="general-availability---kmsi-and-password-reset-now-in-next-generation-of-user-flows"></a>일반 공급 - 차세대 사용자 흐름에서 KMSI 및 암호 재설정

**유형:** 새로운 기능  
**서비스 범주:** B2C - 소비자 ID 관리  
**제품 기능:** B2B/B2C

이제 차세대 B2C 사용자 흐름은 [KMSI(로그인 유지)](../../active-directory-b2c/session-behavior.md?pivots=b2c-custom-policy#enable-keep-me-signed-in-kmsi) 및 암호 재설정을 지원합니다. KMSI 기능을 사용하면 고객이 영구 쿠키를 사용하여 웹 및 네이티브 애플리케이션의 사용자의 세션 수명을 연장할 수 있습니다. 이 기능은 사용자가 브라우저를 닫았다가 다시 여는 경우에도 세션을 활성 상태로 유지합니다. 사용자가 로그아웃하면 세션이 취소됩니다. 암호 재설정을 사용하면 사용자가 "암호를 잊으셨나요?" 링크에서 암호를 다시 설정할 수 있습니다. 또한 관리자는 Azure AD B2C 디렉터리에서 사용자의 만료된 암호를 강제로 재설정할 수 있습니다. [자세한 정보](../../active-directory-b2c/add-password-reset-policy.md?pivots=b2c-user-flow).
 
---

### <a name="general-availability---new-log-analytics-workbook-application-role-assignment-activity"></a>일반 공급 - 새 Log Analytics 통합 문서 애플리케이션 역할 할당 작업

**유형:** 새로운 기능  
**서비스 카테고리:** 사용자 액세스 관리  
**제품 기능:** 권한 관리
 
애플리케이션 역할 할당 변경에 대한 감사 이벤트를 노출하기 위한 새 통합 문서가 추가되었습니다. [자세한 정보](../governance/entitlement-management-logs-and-reporting.md).

---

### <a name="general-availability---next-generation-azure-ad-b2c-user-flows"></a>일반 공급 - 차세대 Azure AD B2C 사용자 흐름 

**유형:** 새로운 기능  
**서비스 범주:** B2C - 소비자 ID 관리  
**제품 기능:** B2B/B2C
 
간소화된 새로운 사용자 흐름 환경은 미리 보기 기능이 있는 기능 패리티를 제공하며 모든 새로운 기능의 홈입니다. 사용자는 동일한 사용자 흐름 내에서 새 기능을 사용하도록 설정할 수 있으므로 새로운 기능이 릴리스될 때마다 여러 버전을 만들 필요가 줄어듭니다. 사용자에게 친숙한 새로운 UX는 사용자 흐름의 선택 및 생성도 간소화합니다. 이 기능 사용에 대한 지침은 [Azure AD B2C 사용자 흐름 만들기](../../active-directory-b2c/tutorial-create-user-flows.md?pivots=b2c-user-flow)를 참조하세요. [자세한 정보](../../active-directory-b2c/user-flow-versions.md).

---

### <a name="general-availability---azure-active-directory-threat-intelligence-for-sign-in-risk"></a>일반 공급 - 로그인 위험에 대한 Azure Active Directory 위협 인텔리전스

**유형:** 새로운 기능  
**서비스 범주:** ID 보호  
**제품 기능:** ID 보안 및 보호
 
이 새로운 검색은 보안 팀에서 공격이 발생하는 것을 관찰할 때 세션 위험을 높음 위험으로 승격하여 사용자에게 알리고 사용자를 보호할 수 있는 임시 방법으로 제공됩니다. 또한 검색은 연결된 로그인을 위험한 것으로 표시합니다. 이 검색은 사용자 위험 검색에 대한 기존 Azure Active Directory 위협 인텔리전스를 따라 Microsoft 보안 팀에서 관찰하는 다양한 공격의 전체 범위를 검색합니다. [자세한 정보](../identity-protection/concept-identity-protection-risks.md#user-risk).
 
---

### <a name="general-availability---conditional-access-named-locations-improvements"></a>일반 공급 - 조건부 액세스의 명명된 위치 개선

**유형:** 새로운 기능  
**서비스 범주:** 조건부 액세스  
**제품 기능:** ID 보안 및 보호
 
명명된 위치의 IPv6 지원이 이제 일반 공급됩니다. 업데이트에는 다음이 포함됩니다.

- IPv6 주소 범위를 정의하는 기능을 추가했습니다.
- 명명된 위치 제한을 90에서 195으로 늘렸습니다.
- 명명된 위치당 IP 범위 제한을 1200에서 2000으로 늘렸습니다.
- 명명된 위치를 검색 및 정렬하고 위치 유형 및 신뢰 유형별로 필터링하는 기능을 추가했습니다.
- 로그인 로그에서 로그인이 속한 명명된 위치를 추가했습니다.
 
또한 관리자가 문제가 있는 명명된 위치를 정의하지 못하도록 방지하기 위해 잘못된 구성의 가능성을 줄이는 추가 검사가 추가되었습니다. [자세히 알아보기](../conditional-access/location-condition.md).

---

### <a name="general-availability---restricted-guest-access-permissions-in-azure-ad"></a>일반 공급 - Azure AD의 제한된 게스트 액세스 권한

**유형:** 새로운 기능  
**서비스 카테고리:** 사용자 관리  
**제품 기능:** 디렉터리
 
게스트 사용자에 대한 디렉터리 수준 권한을 업데이트했습니다. 이러한 권한을 통해 관리자는 외부 게스트 사용자 액세스에 대한 추가 제한 및 제어를 요구할 수 있습니다.

이제 관리자는 사용자 및 그룹의 프로필 및 멤버 자격 정보에 대한 외부 게스트의 액세스에 대해 더 많은 제한을 추가할 수 있습니다. 또한 고객은 게스트 사용자가 자신이 속한 그룹의 멤버 자격을 볼 수 없도록 제한하는 것을 포함하여 난독 처리 그룹 멤버 자격을 통해 규모에 따라 외부 사용자 액세스를 관리할 수 있습니다. 자세한 내용은 [Azure Active Directory에서 게스트 액세스 권한 제한(미리 보기)](../enterprise-users/users-restrict-guest-permissions.md)를 참조하세요.
 
---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---may-2021"></a>Azure AD 앱 갤러리에서 사용할 수 있는 새로 페더레이션된 앱 - 2021년 5월

**유형:** 새로운 기능  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능**: 타사 통합
 
다음과 같은 통합된 새 앱에 대한 사용자 계정을 만들고, 업데이트하고, 삭제하는 작업을 자동화할 수 있습니다.

- [AuditBoard](../saas-apps/auditboard-provisioning-tutorial.md)
- [Cisco Umbrella 사용자 관리](../saas-apps/cisco-umbrella-user-management-provisioning-tutorial.md)
- [Insite LMS](../saas-apps/insite-lms-provisioning-tutorial.md)
- [kpifire](../saas-apps/kpifire-provisioning-tutorial.md)
- [UNIFI](../saas-apps/unifi-provisioning-tutorial.md)

자동화된 사용자 계정 프로비저닝을 사용하여 조직의 보안을 강화하는 방법에 대한 자세한 내용은 [Azure AD를 사용하여 SaaS 애플리케이션에 대한 사용자 프로비저닝 자동화](../app-provisioning/user-provisioning.md)를 참조하세요.

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---may-2021"></a>Azure AD 앱 갤러리에서 사용할 수 있는 새로 페더레이션된 앱 - 2021년 5월

**유형:** 새로운 기능  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능**: 타사 통합
 
2021년 5월에 페더레이션 지원을 통해 29개의 새 애플리케이션이 앱 갤러리에 추가되었습니다.

[InviteDesk](https://app.invitedesk.com/login), [Webrecruit ATS](https://id-test.webrecruit.co.uk/), [Workshop](../saas-apps/workshop-tutorial.md), [Gravity Sketch](https://landingpad.me/), [JustLogin](../saas-apps/justlogin-tutorial.md), [Custellence](https://custellence.com/sso/), [WEVO](https://hello.wevoconversion.com/login), [AppTec360 MDM](https://www.apptec360.com/ms/autopilot.html), [Filemail](https://www.filemail.com/login),[Ardoq](../saas-apps/ardoq-tutorial.md), [Leadfamly](../saas-apps/leadfamly-tutorial.md), [Documo](../saas-apps/documo-tutorial.md), [Autodesk SSO](../saas-apps/autodesk-sso-tutorial.md), [Check Point Harmony Connect](../saas-apps/check-point-harmony-connect-tutorial.md), [BrightHire](https://app.brighthire.ai/), [Rescana](../saas-apps/rescana-tutorial.md), [Bluewhale](https://cloud.bluewhale.dk/), [AlacrityLaw](../saas-apps/alacritylaw-tutorial.md), [Equisolve](../saas-apps/equisolve-tutorial.md), [Zip](../saas-apps/zip-tutorial.md), [Cognician](../saas-apps/cognician-tutorial.md), [Acra](https://www.acrasuite.com/), [VaultMe](https://app.vaultme.com/#/signIn), [TAP App Security](../saas-apps/tap-app-security-tutorial.md), [Cavelo Office365 Cloud Connector](https://dashboard.prod.cavelodata.com/), [Clebex](../saas-apps/clebex-tutorial.md), [Banyan Command Center](../saas-apps/banyan-command-center-tutorial.md), [Check Point Remote Access VPN](../saas-apps/check-point-remote-access-vpn-tutorial.md), [LogMeIn](../saas-apps/logmein-tutorial.md)

여기 https://aka.ms/AppsTutorial 에서 모든 애플리케이션의 설명서를 찾을 수도 있습니다

Microsoft Azure AD 앱 갤러리에서 애플리케이션을 나열하려면 여기에서 세부 정보 https://aka.ms/AzureADAppRequest 을 읽으세요.

---

### <a name="improved-conditional-access-messaging-for-android-and-ios"></a>Android 및 iOS에 대한 조건부 액세스 메시징 개선

**유형:** 변경된 기능  
**서비스 카테고리:** 디바이스 등록 및 관리  
**제품 기능:** 최종 사용자 환경
 

회사 리소스에 대한 액세스가 차단된 사용자에게 표시되는 조건부 액세스 화면의 표현이 업데이트되었습니다. 이러한 사용자는 디바이스를 모바일 디바이스 관리에 등록할 때까지 차단됩니다. 이러한 기능 개선은 Android 및 iOS/iPadOS 플랫폼에 적용됩니다. 다음 사항이 변경되었습니다.

- "디바이스를 안전하게 유지하는 데 기여"를 "액세스 권한을 받을 디바이스 설정"으로 변경했습니다.
- “정상적으로 로그인되었지만 관리자가 이 리소스에 액세스하려면 Microsoft에서 디바이스를 관리해야 한다고 요구합니다.”를 "[조직 이름]에서 [조직 이름] 메일, 파일 및 데이터에 액세스하려면 먼저 이 디바이스를 보호하도록 요구합니다."로 변경했습니다. 
- "지금 등록"을 "계속"으로 변경했습니다.

[Android 엔터프라이즈 디바이스 등록](https://support.microsoft.com/topic/enroll-your-android-enterprise-device-d661c82d-fa28-5dfd-b711-6dff41ae83bb)의 정보는 최신 정보가 아닙니다.

---

### <a name="azure-information-protection-service-will-begin-asking-for-consent"></a>Azure Information Protection 서비스에서 동의를 요청하기 시작합니다.

**유형:** 변경된 기능  
**서비스 범주:** 인증(로그인)  
**제품 기능:** 사용자 인증
 
Azure Information Protection 서비스는 문서에 대한 액세스를 제공하는 과정에서 문서를 암호화한 테넌트에 사용자를 로그인합니다.  6월부터 조직 전체에서 이 액세스 권한이 부여될 때 Azure AD는 사용자에게 동의를 요구하는 메시지를 표시하기 시작합니다.  이를 통해 사용자는 문서를 소유하는 조직에서 문서 액세스의 일환으로 사용자에 대한 일부 정보를 수집한다는 것을 이해할 수 있습니다. [자세한 정보](/azure/information-protection/known-issues#sharing-external-doc-types-across-tenants).
 
---

### <a name="provisioning-logs-schema-change-impacting-graph-api-and-azure-monitor-integration"></a>Graph API 및 Azure Monitor 통합에 영향을 미치는 프로비저닝 로그 스키마 변경

**유형:** 변경된 기능  
**서비스 범주:** 앱 프로비전  
**제품 기능:** 모니터링 및 보고
 

"Action" 및 "statusInfo" 특성은 "provisioningAction" 및 "provisoiningStatusInfo"로 변경됩니다. [프로비전 로그 Graph API](/graph/api/resources/provisioningobjectsummary?view=graph-rest-beta&preserve-view=true) 또는 [Azure Monitor 통합](../app-provisioning/application-provisioning-log-analytics.md)을 사용하여 만든 스크립트를 업데이트합니다. 
 

---

### <a name="new-arm-api-to-manage-pim-for-azure-resources-and-azure-ad-roles"></a>Azure 리소스 및 Azure AD 역할에 대한 PIM을 관리하는 새로운 ARM API

**유형:** 변경된 기능  
**서비스 범주:** Privileged Identity Management  
**제품 기능:** Privileged Identity Management
 
Azure 리소스 역할 및 Azure AD 역할에 대한 PIM API의 업데이트된 버전이 출시되었습니다. Azure 리소스 역할에 대한 PIM API는 이제 ARM API 표준에 따라 릴리스되며, 이는 일반 Azure 역할 할당에 대한 역할 관리 API와 일치합니다. 반면, Azure AD 역할에 대한 PIM API는 unifiedRoleManagement API에 부합되는 Graph API에 따라서도 출시됩니다. 이 변경의 일부 이점은 다음과 같습니다.

- 역할 관리를 위해 ARM 및 Graph의 개체에 맞게 PIM API가 조정됩니다. 새 Azure 리소스를 온보딩하기 위해 PIM을 호출할 필요가 줄어듭니다. 
- 모든 Azure 리소스는 새 PIM API에서 자동으로 작동합니다.
- 역할 정의에 대한 PIM 호출 필요성 줄이기 또는 PIM 리소스 ID 유지
- Azure AD 및 Azure 리소스 역할 모두에 대해 PIM에서 앱 전용 API 권한 지원

/privilegedaccess 아래에 있는 이전 버전의 PIM API는 계속 작동하지만 앞으로 이 새 API로 전환하는 것이 좋습니다. [자세한 정보](../privileged-identity-management/pim-apis.md).
 
---

### <a name="revision-of-roles-in-azure-ad-entitlement-management"></a>Azure AD 권한 관리의 역할 개정

**유형:** 변경된 기능  
**서비스 범주:** RBAC  
**제품 기능:** 권한 관리
 
새 역할인 ID 거버넌스 관리자가 최근에 도입되었습니다. 이 역할은 Azure AD 권한 관리에서 카탈로그 및 액세스 패키지를 관리하는 사용자 관리자 역할을 대체합니다. 관리자를 사용자 관리자 역할에 할당했거나 이 역할을 활성화하여 Azure AD 권한 관리에서 액세스 패키지를 관리하도록 한 경우 대신 ID 거버넌스 관리자 역할로 전환합니다. 사용자 관리자 역할은 더 이상 카탈로그 또는 액세스 패키지에 대한 관리 권한을 제공하지 않습니다. [자세한 정보](../governance/identity-governance-overview.md#appendix---least-privileged-roles-for-managing-in-identity-governance-features).

---

## <a name="april-2021"></a>2021년 4월

### <a name="bug-fixed---azure-ad-will-no-longer-double-encode-the-state-parameter-in-responses"></a>수정된 버그 - Azure AD는 응답에서 상태 매개 변수를 더 이상 이중으로 인코딩하지 않습니다.

**유형:** 고정  
**서비스 범주:** 인증(로그인)  
**제품 기능:** 사용자 인증
 
Azure AD는 클라이언트 애플리케이션에 대한 `/authorize` 응답에서 버그에 대한 픽스를 식별하고 테스트하고 릴리스했습니다.  Azure AD는 클라이언트에 응답을 보낼 때 잘못해서 `state` 매개 변수에 대해 URL 인코딩을 두 번 수행했습니다.  이 경우 상태 매개 변수 불일치로 인해 클라이언트 애플리케이션에서 요청을 거부할 수 있습니다. [자세한 정보](../develop/reference-breaking-changes.md#bug-fix-azure-ad-will-no-longer-url-encode-the-state-parameter-twice). 

---

### <a name="users-can-only-create-security-and-microsoft-365-groups-in-azure-portal-being-deprecated"></a>사용자는 Azure Portal에서 보안 및 Microsoft 365 그룹만 만들 수 있으며 이 제한은 더 이상 적용되지 않습니다.

**유형:** 변경 계획  
**서비스 범주:** 그룹 관리  
**제품 기능:** 디렉터리
 
사용자는 더 이상 Azure Portal에서 보안 및 Microsoft 365 그룹만 만들도록 제한되지 않습니다. 새 설정을 사용하여 Azure Portal, PowerShell 및 API에서 보안 그룹을 만들 수 있습니다. 사용자는 새 설정을 확인하고 업데이트해야 합니다. [자세한 정보를 알아보세요](../enterprise-users/groups-self-service-management.md).

---

### <a name="public-preview----external-identities-self-service-sign-up-in-aad-using-email-one-time-passcode-accounts"></a>공개 미리 보기 - 이메일 일회성 암호 계정을 사용하여 AAD에서 External Identities 셀프 서비스 가입

**유형:** 새로운 기능  
**서비스 범주:** B2B  
**제품 기능:** B2B/B2C
 
외부 사용자는 이제 메일 일회용 암호 계정을 사용하여 Azure AD 자사 및 LOB(기간 업무) 애플리케이션에 등록하거나 로그인할 수 있습니다. [자세한 정보](../external-identities/one-time-passcode.md).

---

### <a name="general-availability---external-identities-self-service-sign-up"></a>일반 공급 - External Identities 셀프 서비스 가입

**유형:** 새로운 기능  
**서비스 범주:** B2B  
**제품 기능:** B2B/B2C
 
외부 사용자에 대한 셀프 서비스 등록은 이제 일반 공급으로 제공됩니다. 이 새로운 기능을 사용하여 외부 사용자는 이제 애플리케이션에 셀프 서비스 등록을 수행할 수 있습니다. 

등록 프로세스 중 사용자에 대한 정보를 수집하고 Facebook 및 Google과 같은 외부 ID 공급자를 허용하는 등, 외부 사용자를 위한 사용자 지정 환경을 만들 수 있습니다. 또한 ID 확인 또는 사용자 승인 등의 다양한 기능을 위해 타사 클라우드 공급자와 통합할 수 있습니다. [자세한 정보](../external-identities/self-service-sign-up-overview.md).
 
---

### <a name="general-availability---azure-ad-b2c-phone-sign-up-and-sign-in-using-built-in-policy"></a>일반 공급 - 기본 제공 정책을 사용한 Azure AD B2C 전화 가입 및 로그인

**유형:** 새로운 기능  
**서비스 범주:** B2C - 소비자 ID 관리  
**제품 기능:** B2B/B2C
 
기본 제공 정책을 사용하는 B2C 전화 가입 및 로그인을 통해 조직의 IT 관리자 및 개발자는 최종 사용자가 사용자 흐름에서 전화 번호를 사용하여 로그인하고 가입하게 합니다. 이 기능을 사용하면 최종 사용자가 문자 메시지를 통해 일회용 암호를 받기 전에 개인 정보 취급 방침 및 사용 약관 등의 고지 사항 링크를 사용자 지정하고 페이지에 표시할 수 있습니다. [자세한 정보](../../active-directory-b2c/phone-authentication-user-flows.md).
 
---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---april-2021"></a>Azure AD 앱 갤러리에서 사용할 수 있는 새로 페더레이션된 앱 - 2021년 4월

**유형:** 새로운 기능  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능**: 타사 통합

2021년 4월에 앱 갤러리에 페더레이션 지원이 제공되는 다음과 같은 31개의 새 애플리케이션을 추가했습니다.

[Zii Travel Azure AD Connect](http://ziitravel.com/), [Cerby](../saas-apps/cerby-tutorial.md), [Selflessly](https://app.selflessly.io/sign-in), [Apollo CX](https://apollo.cxlabs.de/sso/aad), [Pedagoo](https://account.pedagoo.com/), [Measureup](https://account.measureup.com/), [Wistec Education](https://wisteceducation.fi/login/index.php), [ProcessUnity](../saas-apps/processunity-tutorial.md), [Cisco Intersight](../saas-apps/cisco-intersight-tutorial.md), [Codility](../saas-apps/codility-tutorial.md), [H5mag](https://account.h5mag.com/auth/request-access/ms365), [Check Point Identity Awareness](../saas-apps/check-point-identity-awareness-tutorial.md), [Jarvis](https://jarvis.live/login), [desknet's NEO](../saas-apps/desknets-neo-tutorial.md), [SDS & Chemical Information Management](../saas-apps/sds-chemical-information-management-tutorial.md), [Wúru App](../saas-apps/wuru-app-tutorial.md), [Holmes](../saas-apps/holmes-tutorial.md), [Tide Multi Tenant](https://gallery.tideapp.co.uk/), [Telenor](https://admin.smartansatt.telenor.no/), [Yooz US](https://us1.getyooz.com/?kc_idp_hint=microsoft), [Mooncamp](https://app.mooncamp.com/#/login), [inwise SSO](https://app.inwise.com/defaultsso.aspx), [Ecolab Digital Solutions](https://ecolabb2c.b2clogin.com/account.ecolab.com/oauth2/v2.0/authorize?p=B2C_1A_Connect_OIDC_SignIn&client_id=01281626-dbed-4405-a430-66457825d361&nonce=defaultNonce&redirect_uri=https://jwt.ms&scope=openid&response_type=id_token&prompt=login), [Taguchi Digital Marketing System](https://login.taguchi.com.au/), [XpressDox EU Cloud](https://test.xpressdox.com/Authentication/Login.aspx), [EZSSH](https://docs.keytos.io/getting-started/registering-a-new-tenant/registering_app_in_tenant/), [EZSSH Client](https://portal.ezssh.io/signup), [Verto 365](https://www.vertocloud.com/Login/), [KPN Grip](https://www.grip-on-it.com/), [AddressLook](https://portal.bbsonlineservices.net/Manage/AddressLook), [Cornerstone Single Sign-On](../saas-apps/cornerstone-ondemand-tutorial.md)

여기(https://aka.ms/AppsTutorial )에서 모든 애플리케이션의 설명서를 찾을 수도 있습니다.

Azure AD 앱 갤러리에서 애플리케이션을 나열하려면 여기에서 세부 정보를 참조하세요. https://aka.ms/AzureADAppRequest

---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---april-2021"></a>Azure AD 애플리케이션 갤러리의 새로운 프로비저닝 커넥터 - 2021년 4월

**유형:** 새로운 기능  
**서비스 범주:** 앱 프로비전  
**제품 기능**: 타사 통합
 
다음과 같은 통합된 새 앱에 대한 사용자 계정을 만들고, 업데이트하고, 삭제하는 작업을 자동화할 수 있습니다.

- [Bentley - 자동 사용자 프로비저닝](../saas-apps/bentley-automatic-user-provisioning-tutorial.md)
- [Boxcryptor](../saas-apps/boxcryptor-provisioning-tutorial.md)
- [BrowserStack Single Sign-on](../saas-apps/browserstack-single-sign-on-provisioning-tutorial.md)
- [Eletive](../saas-apps/eletive-provisioning-tutorial.md)
- [Jostle](../saas-apps/jostle-provisioning-tutorial.md)
- [Olfeo SAAS](../saas-apps/olfeo-saas-provisioning-tutorial.md)
- [Proware](../saas-apps/proware-provisioning-tutorial.md)
- [Segment](../saas-apps/segment-provisioning-tutorial.md)

자동화된 사용자 계정 프로비저닝을 통해 조직의 보안을 강화하는 방법에 대한 자세한 내용은 [Azure AD를 사용하여 SaaS 애플리케이션에 대한 사용자 프로비저닝 자동화](../app-provisioning/user-provisioning.md)를 참조하세요.
 
---

### <a name="introducing-new-versions-of-page-layouts-for-b2c"></a>B2C에 대한 새 버전의 페이지 레이아웃 소개

**유형:** 변경된 기능  
**서비스 범주:** B2C - 소비자 ID 관리  
**제품 기능:** B2B/B2C
 
새 버전의 jQuery 및 Handlebars JS를 도입하여 보안 위험을 줄이기 위해 Azure AD B2C에서 B2C 시나리오의 [페이지 레이아웃](../../active-directory-b2c/page-layout.md)을 업데이트했습니다.
 
---

### <a name="updates-to-sign-in-diagnostic"></a>로그인 진단 업데이트

**유형:** 변경된 기능  
**서비스 범주:** 보고  
**제품 기능:** 모니터링 및 보고
 
로그인 진단 도구의 시나리오 적용 범위가 늘어났습니다. 

이 업데이트를 사용하면 이제 다음과 같은 이벤트 관련 시나리오가 로그인 진단 결과에 포함됩니다. 
- 엔터프라이즈 애플리케이션 구성 문제 이벤트
- 엔터프라이즈 애플리케이션 서비스 공급자(애플리케이션 쪽) 이벤트
- 잘못된 자격 증명 이벤트 

이러한 결과는 이러한 문제를 해결하기 위해 수행해야 하는 이벤트 및 작업에 대한 상황별 및 관련 세부 정보를 표시합니다. 또한 심층 컨텍스트 진단이 없는 시나리오의 경우 로그인 진단에 오류 이벤트에 대한 자세한 설명이 표시됩니다.

자세한 내용은 [Azure AD의 로그인 진단이란?](../reports-monitoring/overview-sign-in-diagnostics.md)을 참조하세요.

---
### <a name="azure-ad-connect-cloud-sync-general-availability-refresh"></a>Azure AD Connect 클라우드 동기화 일반 공급 새로 고침 
**유형:** 변경된 기능  
**서비스 범주:** Azure AD Connect 클라우드 동기화 **제품 기능:** 디렉터리

이제 Azure AD Connect 클라우드 동기화에 업데이트된 에이전트(버전# - 1.1.359)가 있습니다. 버그 픽스를 비롯한 에이전트 업데이트에 대한 자세한 내용은 [버전 기록](../cloud-sync/reference-version-history.md)을 확인하세요. 업데이트된 에이전트를 사용하여 클라우드 동기화 고객은 GMSA cmdlet을 사용하여 세분화된 수준에서 gMSA 사용 권한을 설정 및 재설정할 수 있습니다. 또한 그룹 범위 필터링을 사용하여 동기화하는 구성원의 제한을 1499~5만(50K)명으로 변경했습니다. 

특성 매핑을 사용하여 AD에서 Azure AD로 특성 값을 변환하는 경우 간단한 식 뿐만 아니라 복잡한 식을 작성할 수 있도록 하는 새로운 클라우드 동기화용 [식 작성기](../cloud-sync/how-to-expression-builder.md#deploy-the-expression)를 확인하세요.

---

## <a name="march-2021"></a>2021년 3월

### <a name="guidance-on-how-to-enable-support-for-tls-12-in-your-environment-in-preparation-for-upcoming-azure-ad-tls-1011-deprecation"></a>예정된 Azure AD TLS 1.0/1.1 사용 중단을 준비하기 위해 사용자 환경에서 TLS 1.2 지원을 사용하는 방법에 관한 참고 자료

**유형:** 변경 계획  
**서비스 범주:** 해당 없음  
**제품 기능:** 기준

Azure Active Directory는 2021년 6월 30일부터 전 세계에서 다음 Azure Active Directory 프로토콜을 사용 중단합니다.


- TLS 1.0
- TLS 1.1
- 3DES 암호화 그룹(TLS_RSA_WITH_3DES_EDE_CBC_SHA)

영향을 받는 환경은 다음과 같습니다.

- Azure 상용 클라우드
- Office 365 GCC 및 WW

자세한 내용은 [Azure AD TLS 1.1 및 1.0 지원 중단을 위해 사용자 환경에서 TLS 1.2 지원 사용](/troubleshoot/azure/active-directory/enable-support-tls-environment)을 참조하세요.

---

### <a name="public-preview----azure-ad-entitlement-management-now-supports-multi-geo-sharepoint-online"></a>공개 미리 보기 - Azure AD 권한 관리는 이제 다중 지역 SharePoint Online을 지원함

**유형:** 새로운 기능  
**서비스 범주:** 기타  
**제품 기능:** 권한 관리
 
다중 지역 SharePoint Online을 사용하는 조직의 경우 이제 특정 다중 지역 환경의 사이트를 권한 관리 액세스 패키지에 포함할 수 있습니다. [자세한 정보를 알아보세요](../governance/entitlement-management-catalog-create.md#add-a-multi-geo-sharepoint-site).

---

### <a name="public-preview----restore-deleted-apps-from-app-registrations"></a>공개 미리 보기 - 앱 등록에서 삭제된 앱 복원

**유형:** 새로운 기능  
**서비스 범주:** 기타  
**제품 기능:** 개발자 환경
 
이제 고객은 Azure Portal에서 삭제된 앱 등록을 보고, 복원하고, 영구 제거할 수 있습니다. 이는 개인 Microsoft 계정의 애플리케이션이 아니라 디렉터리에 연결된 애플리케이션에만 적용됩니다. [자세한 정보를 알아보세요](../develop/howto-restore-app.md).
 
---

### <a name="public-preview----new-user-action-in-conditional-access-for-registering-or-joining-devices"></a>공개 미리 보기 - 디바이스를 등록하거나 조인하기 위한 조건부 액세스의 새 "사용자 작업"

**유형:** 새로운 기능  
**서비스 범주:** 조건부 액세스  
**제품 기능:** ID 보안 및 보호
 
 조건부 액세스에서 "디바이스 등록 또는 조인"이라는 새로운 사용자 작업을 사용할 수 있습니다. 이 사용자 작업을 통해 Azure AD 디바이스 등록에 대한 MFA(다단계 인증) 정책을 제어할 수 있습니다. 

현재 이 사용자 작업에서는 사용자가 Azure AD에 디바이스를 등록하거나 조인하는 경우 MFA를 시각적 개체로 사용하는 것만 가능합니다. Azure AD 디바이스 등록에 종속되거나 적용되지 않는 다른 시각적 개체는 해당 사용자 작업에는 사용할 수 없습니다. [자세한 정보를 알아보세요](../conditional-access/concept-conditional-access-cloud-apps.md#user-actions). 
 
---

### <a name="public-preview----optimize-connector-groups-to-use-the-closest-application-proxy-cloud-service"></a>공개 미리 보기 - 가장 가까운 애플리케이션 프록시 클라우드 서비스를 사용하도록 커넥터 그룹 최적화

**유형:** 새로운 기능  
**서비스 범주:** 응용 프로그램 프록시  
**제품 기능:** 액세스 제어
 
이 새로운 기능을 사용하면 애플리케이션이 호스트되는 가장 가까운 지역 애플리케이션 프록시 서비스에 커넥터 그룹을 할당할 수 있습니다. 이렇게 하면 홈 테넌트의 지역이 아닌, 앱이 지역에서 호스팅되는 시나리오에서 앱 성능을 향상할 수 있습니다. [자세한 정보를 알아보세요](../app-proxy/application-proxy-network-topology.md#optimize-connector-groups-to-use-closest-application-proxy-cloud-service-preview). 
 
---

### <a name="public-preview----external-identities-self-service-sign-up-in-aad-using-email-one-time-passcode-accounts"></a>공개 미리 보기 - 이메일 일회성 암호 계정을 사용하여 AAD에서 External Identities 셀프 서비스 가입

**유형:** 새로운 기능  
**서비스 범주:** B2B  
**제품 기능:** B2B/B2C

외부 사용자는 이제 메일 일회용 암호 계정을 사용하여 Azure AD의 자사 앱 및 LOB 앱에 가입할 수 있습니다. [자세한 정보를 알아보세요](../external-identities/one-time-passcode.md).

---

### <a name="public-preview----availability-of-ad-fs-sign-ins-in-azure-ad"></a>공개 미리 보기 - Azure AD의 AD FS 로그인 가용성

**유형:** 새로운 기능  
**서비스 범주:** 인증(로그인)  
**제품 기능:** 모니터링 및 보고
 
이제 AD FS 로그인 작업을 Azure AD 작업 보고와 통합하여 하이브리드 ID 인프라의 통합 보기를 제공할 수 있습니다. Azure AD 로그인 보고서, Log Analytics 및 Azure Monitor Workbooks를 사용하면 AD FS 계정 잠금, 잘못된 암호 시도, 예기치 않은 로그인 시도 급증 등 AAD 및 AD FS 로그인 시나리오에 관한 심층 분석을 수행할 수 있습니다.

자세한 내용은 [Connect Health를 사용하여 Azure AD에서 AD FS 로그인](../hybrid/how-to-connect-health-ad-fs-sign-in.md)을 참조하세요.

---

### <a name="general-availability---staged-rollout-to-cloud-authentication"></a>일반 공급 - 클라우드 인증을 위한 단계적 롤아웃

**유형:** 새로운 기능  
**서비스 범주:** AD Connect  
**제품 기능:** 사용자 인증
 
클라우드 인증을 위한 단계적 롤아웃이 이제 일반 공급됩니다. 단계적 롤아웃 기능을 사용하면 PTA(통과 인증) 또는 PHA(암호 해시 동기화)와 같은 클라우드 인증 방법을 사용하여 사용자 그룹을 선택적으로 테스트할 수 있습니다. 한편 페더레이션된 도메인의 다른 모든 사용자는 AD FS 또는 다른 페더레이션 서비스와 같은 페더레이션 서비스를 계속 사용하여 사용자를 인증합니다. [자세한 정보를 알아보세요](../hybrid/how-to-connect-staged-rollout.md).

---

### <a name="general-availability---user-type-attribute-can-now-be-updated-in-the-azure-admin-portal"></a>일반 공급 - 이제 Azure 관리 포털에서 사용자 형식 특성을 업데이트할 수 있음

**유형:** 새로운 기능  
**서비스 범주:** 사용자 환경 및 관리  
**제품 기능:** 사용자 관리
 
이제 고객은 Azure 관리 포털에서 사용자 프로필 정보를 업데이트할 때 Azure AD 사용자의 사용자 형식을 업데이트할 수 있습니다. Microsoft Graph에서 사용자 형식을 업데이트할 수도 있습니다. 자세히 알아보려면 [사용자 프로필 정보 추가 또는 업데이트](active-directory-users-profile-azure-portal.md)를 참조하세요.
 
---

### <a name="general-availability---replica-sets-for-azure-active-directory-domain-services"></a>일반 공급 - Azure Active Directory Domain Services에 대한 복제본 세트

**유형:** 새로운 기능  
**서비스 범주:** Azure AD Domain Services  
**제품 기능:** Azure AD Domain Services
 
Azure AD DS의 복제본 세트 기능이 이제 일반 공급됩니다. [자세한 정보를 알아보세요](../../active-directory-domain-services/concepts-replica-sets.md).
 
---

### <a name="general-availability---collaborate-with-your-partners-using-email-one-time-passcode-in-the-azure-government-cloud"></a>일반 공급 - Azure Government 클라우드의 메일 일회용 암호를 사용하여 파트너와 공동 작업

**유형:** 새로운 기능  
**서비스 범주:** B2B  
**제품 기능:** B2B/B2C
 
이제 Microsoft Azure Government 클라우드의 조직은 게스트가 메일 일회용 암호를 통해 초대를 사용할 수 있게 설정할 수 있습니다. 이렇게 하면 Azure Government 클라우드에서 Azure AD, Microsoft 또는 Gmail 계정이 없는 모든 게스트 사용자는 공유 리소스에 로그인할 임시 코드를 요청하고 입력하여 파트너와 공동 작업을 수행할 수 있습니다. [자세한 정보를 알아보세요](../external-identities/one-time-passcode.md#note-for-azure-us-government-customers).

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---march-2021"></a>Azure AD 앱 갤러리에서 사용할 수 있는 새로 페더레이션된 앱 - 2021년 3월

**유형:** 새로운 기능  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능**: 타사 통합
 
2021년 3월에 앱 갤러리에 페더레이션 지원이 제공되는 다음과 같은 37개의 새 앱을 추가했습니다.

[Bambuser Live Video Shopping](https://lcx.bambuser.com/), [DeepDyve Inc](https://www.deepdyve.com/azure-sso), [Moqups](../saas-apps/moqups-tutorial.md), [RICOH Spaces Mobile](https://ricohspaces.app/welcome), [Flipgrid](https://auth.flipgrid.com/), [hCaptcha Enterprise](../saas-apps/hcaptcha-enterprise-tutorial.md), [SchoolStream ASA](https://jsd.schoolstreamk12.com/ASA/ASAlogin.aspx), [TransPerfect GlobalLink Dashboard](../saas-apps/transperfect-globallink-dashboard-tutorial.md), [SimplificaCI](https://app.simplificaci.com.br/), [Thrive LXP](../saas-apps/thrive-lxp-tutorial.md), [Lexonis TalentScape](../saas-apps/lexonis-talentscape-tutorial.md), [Exium](../saas-apps/exium-tutorial.md), [Sapient](../saas-apps/sapient-tutorial.md), [TrueChoice](../saas-apps/truechoice-tutorial.md), [RICOH Spaces](https://ricohspaces.app/welcome), [Saba Cloud](../saas-apps/learning-at-work-tutorial.md), [Acunetix 360](../saas-apps/acunetix-360-tutorial.md), [Exceed.ai](../saas-apps/exceed-ai-tutorial.md), [GitHub Enterprise Managed User](../saas-apps/github-enterprise-managed-user-tutorial.md), [Enterprise Vault.cloud for Outlook](https://login.microsoftonline.com/common/oauth2/v2.0/authorize?response_type=id_token&scope=openid%20profile%20User.Read&client_id=7176efe5-e954-4aed-b5c8-f5c85a980d3a&nonce=4b9e1981-1bcb-4938-a283-86f6931dc8cb), [Smartlook](../saas-apps/smartlook-tutorial.md), [Accenture Academy](../saas-apps/accenture-academy-tutorial.md), [Onshape](../saas-apps/onshape-tutorial.md), [Tradeshift](../saas-apps/tradeshift-tutorial.md), [JuriBlox](../saas-apps/juriblox-tutorial.md), [SecurityStudio](../saas-apps/securitystudio-tutorial.md), [ClicData](https://app.clicdata.com/), [Evergreen](../saas-apps/evergreen-tutorial.md), [Patchdeck](https://patchdeck.com/ad_auth/authenticate/), [FAX.PLUS](../saas-apps/fax-plus-tutorial.md), [ValidSign](../saas-apps/validsign-tutorial.md), [AWS Single Sign-on](../saas-apps/aws-single-sign-on-tutorial.md), [Nura Space](https://dashboard.nuraspace.com/login), [Broadcom DX SaaS](../saas-apps/broadcom-dx-saas-tutorial.md), [Interplay Learning](https://skilledtrades.interplaylearning.com/#login), [SendPro Enterprise](../saas-apps/sendpro-enterprise-tutorial.md), [FortiSASE SIA](../saas-apps/fortisase-sia-tutorial.md)

여기(https://aka.ms/AppsTutorial )에서 모든 애플리케이션의 설명서를 찾을 수도 있습니다.

Azure AD 앱 갤러리에서 애플리케이션을 나열하려면 여기에서 세부 정보를 참조하세요. https://aka.ms/AzureADAppRequest

---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---march-2021"></a>Azure AD 앱 갤러리의 새로운 프로비저닝 커넥터 - 2021년 3월

**유형:** 새로운 기능  
**서비스 범주:** 앱 프로비전  
**제품 기능**: 타사 통합

다음과 같은 통합된 새 앱에 대한 사용자 계정을 만들고, 업데이트하고, 삭제하는 작업을 자동화할 수 있습니다.

- [AWS Single Sign-on](../saas-apps/aws-single-sign-on-provisioning-tutorial.md)
- [Bpanda](../saas-apps/bpanda-provisioning-tutorial.md)
- [Britive](../saas-apps/britive-provisioning-tutorial.md)
- [GitHub Enterprise Managed User](../saas-apps/github-enterprise-managed-user-provisioning-tutorial.md)
- [Grammarly](../saas-apps/grammarly-provisioning-tutorial.md)
- [LogicGate](../saas-apps/logicgate-provisioning-tutorial.md)
- [SecureLogin](../saas-apps/secure-login-provisioning-tutorial.md)
- [TravelPerk](../saas-apps/travelperk-provisioning-tutorial.md)

자동화된 사용자 계정 프로비저닝을 사용하여 조직의 보안을 강화하는 방법에 대한 자세한 내용은 [Azure AD를 사용하여 SaaS 애플리케이션에 대한 사용자 프로비저닝 자동화](../app-provisioning/user-provisioning.md)를 참조하세요.
 
---

### <a name="introducing-ms-graph-api-for-company-branding"></a>회사 브랜딩용 MS Graph API 소개

**유형:** 변경된 기능  
**서비스 범주:** MS Graph  
**제품 기능:** B2B/B2C

[회사 브랜딩용 MS Graph API](/graph/api/resources/organizationalbrandingproperties)는 Azure AD 또는 Microsoft 365 로그인 환경에서 사용할 수 있으며 프로그래밍 방식으로 브랜딩 매개 변수를 관리할 수 있습니다.

---

### <a name="general-availability---header-based-authentication-sso-with-application-proxy"></a>일반 공급 - 애플리케이션 프록시를 사용하는 헤더 기반 인증 SSO

**유형:** 변경된 기능  
**서비스 범주:** 응용 프로그램 프록시  
**제품 기능:** 액세스 제어
 
헤더 기반 인증을 위한 Azure AD 애플리케이션 프록시 기본 지원이 이제 일반 공급됩니다. 이 기능을 사용하면 배포하는 데 필요한 추가 구성 요소 없이 필요한 사용자 특성을 애플리케이션의 HTTP 헤더로 구성할 수 있습니다. [자세한 정보를 알아보세요](../app-proxy/application-proxy-configure-single-sign-on-with-headers.md).

---

### <a name="two-way-sms-for-mfa-server-is-no-longer-supported"></a>MFA 서버용 양방향 SMS는 이제 지원되지 않습니다.

**유형:** 사용되지 않음  
**서비스 범주:** MFA  
**제품 기능:** ID 보안 및 보호
 

MFA 서버용 양방향 SMS는 2018년부터 사용되지 않으며, 2021년 2월 24일 이후에는 지원되지 않습니다. 관리자는 아직도 양방향 SMS를 사용하는 사용자들이 다른 방법을 사용하게 합니다.

2020년 12월 8일 및 2021년 1월 28일에 영향을 받는 관리자에게 메일 알림 및 Azure Portal Service Health 알림이 전송되었습니다. 이 경고는 구독에 연결된 소유자, 공동소유자, 관리자, 서비스 관리자 RBAC 역할에 전달되었습니다. [자세한 정보를 알아보세요](../authentication/how-to-authentication-two-way-sms-unsupported.md).
 
---
 
## <a name="february-2021"></a>2021년 2월

### <a name="email-one-time-passcode-authentication-on-by-default-starting-october-2021"></a>2021년 10월부터 기본적으로 일회용 암호 인증을 메일로 전송

**유형:** 변경 계획  
**서비스 범주:** B2B  
**제품 기능:** B2B/B2C
 

2021년 10월 31일부터 Microsoft Azure Active Directory [메일 일회용 암호 인증](../external-identities/one-time-passcode.md)은 B2B 협업 시나리오를 위한 계정 및 테넌트를 초대하는 기본 방법이 됩니다. 현재 Microsoft는 관리되지 않는 Azure Active Directory 계정을 사용한 초대의 사용을 허용하지 않습니다. 

---

### <a name="unrequested-but-consented-permissions-will-no-longer-be-added-to-tokens-if-they-would-trigger-conditional-access"></a>조건부 액세스를 실행하는 경우 요청되지 않았지만 동의한 권한이 토큰에 더 추가되지 않습니다.

**유형:** 변경 계획  
**서비스 범주:** 인증(로그인)  
**제품 기능:** 플랫폼
 
현재 [동적 권한](../develop/v2-permissions-and-consent.md#requesting-individual-user-consent)을 사용하는 애플리케이션에는 동의한 모든 액세스 권한이 부여됩니다. 여기에는 사용자가 조건부 액세스를 실행하는 경우에도 요청되지 않은 애플리케이션이 포함됩니다. 예를 들어, 이로 인해 `user.read`만 요청하였지만 `files.read`에도 동의하는 앱이 `files.read` 사용 권한에 할당된 조건부 액세스를 강제로 통과하게 할 수 있습니다. 

불필요한 조건부 액세스 프롬프트 수를 줄이기 위해 Azure AD는 애플리케이션에 요청되지 않은 범위가 제공되는 방식을 변경합니다. 앱은 명시적으로 요청하는 권한에 대한 조건부 액세스만 트리거합니다. 자세한 내용은 [인증의 새로운 기능](../develop/reference-breaking-changes.md#conditional-access-will-only-trigger-for-explicitly-requested-scopes)을 참조하세요.
 
---
 
### <a name="public-preview----use-a-temporary-access-pass-to-register-passwordless-credentials"></a>공개 미리 보기 - 임시 액세스 패스를 사용하여 암호 없는 자격 증명 등록

**유형:** 새로운 기능  
**서비스 범주:** MFA  
**제품 기능:** ID 보안 및 보호

임시 액세스 패스는 강력한 자격 증명으로 사용되며, 사용자가 강력한 인증 요소(예: FIDO2 보안 키 또는 Microsoft Authenticator) 앱을 분실하거나 잊은 경우 암호 없는 자격 증명과 복구를 온보딩하고, 새 강력한 인증 방법을 등록하기 위해 로그인해야 하는 시간제한 암호입니다. [자세한 정보를 알아보세요](../authentication/howto-authentication-temporary-access-pass.md).

---

### <a name="public-preview----keep-me-signed-in-kmsi-in-next-generation-of-user-flows"></a>공개 미리 보기 - 차세대 사용자 흐름의 KMSI(로그인 유지)

**유형:** 새로운 기능  
**서비스 범주:** B2C - 소비자 ID 관리  
**제품 기능:** B2B/B2C

이제 차세대 B2C 사용자 흐름은 고객이 영구 쿠키를 사용하여 웹 애플리케이션 및 네이티브 애플리케이션의 사용자에게 세션 수명을 연장할 수 있는 [KMSI(로그인 유지)](../../active-directory-b2c/session-behavior.md?pivots=b2c-custom-policy#enable-keep-me-signed-in-kmsi) 기능을 지원합니다.  기능은 사용자가 브라우저를 닫았다가 다시 열 때도 세션을 활성 상태로 유지하고, 사용자가 로그아웃할 때 해지됩니다.

---

### <a name="public-preview----reset-redemption-status-for-a-guest-user"></a>공개 미리 보기 - 게스트 사용자에 대한 사용 상태 초기화

**유형:** 새로운 기능  
**서비스 범주:** B2B  
**제품 기능:** B2B/B2C
 
고객은 이제 기존 외부 게스트 사용자를 다시 초대하여 사용 상태를 초기화할 수 있습니다. 그러면 게스트 사용자 계정이 액세스를 잃지 않고 남아 있을 수 있습니다. [자세한 정보를 알아보세요](../external-identities/reset-redemption-status.md).
 
---

### <a name="public-preview----synchronization-provisioning-apis-now-support-application-permissions"></a>공개 미리 보기 - 이제 동기화(프로비저닝) API에서 애플리케이션 권한을 지원함

**유형:** 새로운 기능  
**서비스 범주:** 앱 프로비전  
**제품 기능:** ID 수명 주기 관리
 
이제 고객은 application.readwrite.ownedby를 애플리케이션 권한으로 사용하여 동기화 API를 호출할 수 있습니다. 참고로 이 기능은 Azure AD에서 타사 애플리케이션(예: AWS, Data Brick 등)으로 프로비저닝하는 경우에만 지원됩니다. 현재 HR-프로비저닝(Workday / Successfactors) 또는 클라우드 동기화(AD에서 Azure AD로)에는 지원되지 않습니다. [자세한 정보를 알아보세요](/graph/api/resources/provisioningobjectsummary?view=graph-rest-beta&preserve-view=true).
 
---

### <a name="general-availability---authentication-policy-administrator-built-in-role"></a>일반 공급 - 인증 정책 관리자 기본 제공 역할

**유형:** 새로운 기능  
**서비스 카테고리:** RBAC  
**제품 기능:** 액세스 제어
 
이 역할을 가진 사용자는 인증 방법 정책, 테넌트 전체 MFA 설정, 암호 보호 정책을 구성할 수 있습니다. 이 역할은 암호 보호 설정을 관리할 수 있는 권한( 스마트 잠금 구성 및 사용자 지정 금지 암호 목록 업데이트)을 부여합니다. [자세한 정보를 알아보세요](../roles/permissions-reference.md#authentication-policy-administrator).

---

### <a name="general-availability---user-collections-on-my-apps-are-available-now"></a>일반 공급 - 내 앱의 사용자 컬렉션을 지금 사용할 수 있습니다.

**유형:** 새로운 기능  
**서비스 범주:** 내 앱  
**제품 기능:** 최종 사용자 환경
 
이제 사용자는 내 앱의 앱 시작 관리자에서 자신의 앱 그룹을 만들 수 있습니다. 또한 관리자가 공유하는 컬렉션을 다시 정렬하고 숨길 수 있습니다. [자세한 정보를 알아보세요](../user-help/my-apps-portal-user-collections.md).

---

### <a name="general-availability---autofill-in-authenticator"></a>일반 공급 - Authenticator에서 자동 채우기

**유형:** 새로운 기능  
**서비스 범주:** Microsoft Authenticator 앱  
**제품 기능:** ID 보안 및 보호
 
Microsoft Authenticator는 MFA(다단계 인증) 및 계정 관리 기능을 제공하며, 이제 사용자가 모바일(iOS 및 Android)에서 방문하는 사이트 및 앱에서 암호도 자동으로 채웁니다. 

Authenticator에서 자동 채우기를 사용하려면 사용자가 Authenticator에 개인 Microsoft 계정을 추가하고 이를 통해 암호를 동기화해야 합니다. 지금은 회사 계정 또는 학교 계정을 사용하여 암호를 동기화할 수 없습니다. [자세한 정보를 알아보세요](../user-help/user-help-auth-app-faq.md#autofill-for-it-admins).

---

### <a name="general-availability---invite-internal-users-to-b2b-collaboration"></a>일반 공급 - 내부 사용자를 B2B 협업에 초대

**유형:** 새로운 기능  
**서비스 범주:** B2B  
**제품 기능:** B2B/B2C
 
고객은 이제 기존 내부 계정에 초대를 보내는 대신 내부 게스트를 초대하여 B2B 협업을 사용할 수 있습니다. 이를 통해 고객은 해당 사용자의 개체 ID, UPN, 그룹 멤버 자격, 앱 할당을 유지할 수 있습니다. [자세한 정보를 알아보세요](../external-identities/invite-internal-users.md).

---

### <a name="general-availability---domain-name-administrator-built-in-role"></a>일반 공급 - 도메인 이름 관리자 기본 제공 역할

**유형:** 새로운 기능  
**서비스 카테고리:** RBAC  
**제품 기능:** 액세스 제어
 
이 역할을 가진 사용자는 도메인 이름을 관리(읽기, 추가, 확인, 업데이트, 삭제)할 수 있습니다. 해당 개체에는 도메인 종속성이 있어서 사용자, 그룹, 애플리케이션에 관한 디렉터리 정보를 읽을 수도 있습니다. 

온-프레미스 환경의 경우, 해당 역할을 가진 사용자는 연결된 사용자가 항상 온-프레미스에서 인증되도록 페더레이션을 위한 도메인 이름을 구성할 수 있습니다. 해당 사용자는 Single Sign-On을 통해 온-프레미스 암호를 사용하여 Azure AD 기반 서비스에 로그인할 수 있습니다. 페더레이션 설정은 Azure AD Connect를 통해 동기화해야 하므로 사용자는 Azure AD Connect를 관리할 수 있는 권한도 있습니다. [자세한 정보를 알아보세요](../roles/permissions-reference.md#domain-name-administrator).
 
---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---february-2021"></a>Azure AD 앱 갤러리에서 사용할 수 있는 새 페더레이션된 앱 - 2021년 2월

**유형:** 새로운 기능  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능**: 타사 통합
 
2021년 2월에 앱 갤러리에 페더레이션 지원이 제공되는 다음과 같은 37개의 새 앱을 추가했습니다.

[Loop Messenger Extension](https://loopworks.com/loop-flow-messenger/), [Silverfort Azure AD Adapter](http://www.silverfort.com/), [Interplay Learning](https://skilledtrades.interplaylearning.com/#login), [Nura Space](https://dashboard.nuraspace.com/login), [Yooz EU](https://eu1.getyooz.com/?kc_idp_hint=microsoft), [UXPressia](https://uxpressia.com/users/sign-in), [introDus Pre- and Onboarding Platform](http://app.introdus.dk/login), [Happybot](https://login.microsoftonline.com/organizations/oauth2/v2.0/authorize?client_id=34353e1e-dfe5-4d2f-bb09-2a5e376270c8&response_type=code&redirect_uri=https://api.happyteams.io/microsoft/integrate&response_mode=query&scope=offline_access%20User.Read%20User.Read.All), [LeaksID](https://app.leaksid.com/), [ShiftWizard](http://www.shiftwizard.com/), [PingFlow SSO](https://app.pingview.io/), [Swiftlane](https://admin.swiftlane.com/login), [Quasydoc SSO](https://www.quasydoc.eu/login), [Fenwick Gold Account](https://businesscentral.dynamics.com/), [SeamlessDesk](https://www.seamlessdesk.com/login), [Learnsoft LMS & TMS](http://www.learnsoft.com/), [P-TH+](https://p-th.jp/), [myViewBoard](https://api.myviewboard.com/auth/microsoft/), [Tartabit IoT Bridge](https://bridge-us.tartabit.com/), [AKASHI](../saas-apps/akashi-tutorial.md), [Rewatch](../saas-apps/rewatch-tutorial.md), [Zuddl](../saas-apps/zuddl-tutorial.md), [Parkalot - Car park management](../saas-apps/parkalot-car-park-management-tutorial.md), [HSB ThoughtSpot](../saas-apps/hsb-thoughtspot-tutorial.md), [IBMid](../saas-apps/ibmid-tutorial.md), [SharingCloud](../saas-apps/sharingcloud-tutorial.md), [PoolParty Semantic Suite](../saas-apps/poolparty-semantic-suite-tutorial.md), [GlobeSmart](../saas-apps/globesmart-tutorial.md), [Samsung Knox 및 비즈니스 서비스](../saas-apps/samsung-knox-and-business-services-tutorial.md), [Penji](../saas-apps/penji-tutorial.md), [Kendis- Scaling Agile Platform](../saas-apps/kendis-scaling-agile-platform-tutorial.md), [Maptician](../saas-apps/maptician-tutorial.md), [Olfeo SAAS](../saas-apps/olfeo-saas-tutorial.md), [Sigma Computing](../saas-apps/sigma-computing-tutorial.md), [CloudKnox 권한 관리 플랫폼](../saas-apps/cloudknox-permissions-management-platform-tutorial.md), [Klaxoon SAML](../saas-apps/klaxoon-saml-tutorial.md), [Enablon](../saas-apps/enablon-tutorial.md)

여기(https://aka.ms/AppsTutorial )에서 모든 애플리케이션의 설명서를 찾을 수도 있습니다.

Azure AD 앱 갤러리에서 애플리케이션을 나열하려면 여기에서 세부 정보를 참조하세요. https://aka.ms/AzureADAppRequest

--- 

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---february-2021"></a>Azure AD 앱 갤러리의 새로운 프로비저닝 커넥터 - 2021년 2월

**유형:** 새로운 기능  
**서비스 범주:** 앱 프로비전  
**제품 기능**: 타사 통합
 

다음과 같은 통합된 새 앱에 대한 사용자 계정을 만들고, 업데이트하고, 삭제하는 작업을 자동화할 수 있습니다.

- [Atea](../saas-apps/atea-provisioning-tutorial.md)
- [Getabstract](../saas-apps/getabstract-provisioning-tutorial.md)
- [HelloID](../saas-apps/helloid-provisioning-tutorial.md)
- [Hoxhunt](../saas-apps/hoxhunt-provisioning-tutorial.md)
- [Iris Intranet](../saas-apps/iris-intranet-provisioning-tutorial.md)
- [Preciate](../saas-apps/preciate-provisioning-tutorial.md)

자세한 내용은 [Azure AD를 사용하여 SaaS 애플리케이션의 사용자를 자동으로 프로비저닝](../app-provisioning/user-provisioning.md)을 참조하세요.

---

### <a name="general-availability---10-azure-active-directory-roles-now-renamed"></a>일반 공급 - 이제 10개의 Azure Active Directory 역할 이름이 변경됨

**유형:** 변경된 기능  
**서비스 범주:** RBAC  
**제품 기능:** 액세스 제어
 
10 Azure AD 기본 제공 역할의 이름이 변경되어 [Microsoft 365 관리 센터](/microsoft-365/admin/microsoft-365-admin-center-preview), [Azure AD 포털](https://portal.azure.com/), [Microsoft Graph](https://developer.microsoft.com/graph/)에 정렬되었습니다. 새 역할에 관한 자세한 내용은 [Azure Active Directory의 관리자 역할](../roles/permissions-reference.md#all-roles)을 참조하세요.

![MS Graph API의 역할 이름과 Azure Portal 그리고 API, Azure Portal, Mac에서 제안된 최종 이름을 보여 주는 표입니다.](media/whats-new/roles-table-rbac.png)

---

### <a name="new-company-branding-in-mfasspr-combined-registration"></a>MFA/SSPR 결합 등록의 새로운 회사 브랜딩

**유형:** 변경된 기능  
**서비스 범주:** 사용자 환경 및 관리  
**제품 기능:** 최종 사용자 환경
 
이전에는 회사 로고가 Azure Active Directory 로그인 페이지에서 사용되지 않았습니다. 이제 회사 브랜딩은 MFA/SSPR 결합 등록의 왼쪽 위에 있습니다. 회사 브랜딩도 내 로그인 및 보안 정보 페이지에 포함되어 있습니다. [자세한 정보를 알아보세요](../fundamentals/customize-branding.md).

---

### <a name="general-availability---second-level-manager-can-be-set-as-alternate-approver"></a>일반 공급 - 두 번째 수준 관리자를 대체 승인자로 설정할 수 있음

**유형:** 변경된 기능  
**서비스 카테고리:** 사용자 액세스 관리  
**제품 기능:** 권한 관리
 
이제 승인자를 선택할 때 추가 옵션을 권한 관리에서 사용할 수 있습니다. 첫 번째 승인자를 "승인자로서 관리자"로 선택하는 경우, 대체 승인자 필드에서 선택할 수 있는 "두 번째 수준 관리자를 대체 승인자로"를 또 다른 옵션으로 가지게 됩니다. 이 옵션을 선택하는 경우 시스템에서 두 번째 수준 관리자를 찾을 수 없으면 요청을 전달할 대체 승인자를 추가해야 합니다. [자세한 정보를 알아보세요](../governance/entitlement-management-access-package-approval-policy.md#alternate-approvers).
 
---

### <a name="authentication-methods-activity-dashboard"></a>인증 방법 작업 대시보드

**유형:** 변경된 기능  
**서비스 범주:** 보고  
**제품 기능:** 모니터링 및 보고
 

새로 고쳐진 인증 방법 작업 대시보드는 관리자에게 테넌트의 인증 방법 등록 및 사용 작업에 관한 개요를 제공합니다. 이 보고서는 각 방법의 등록된 사용자 수를 요약하고 로그인 및 암호를 다시 설정할 때 사용하는 방법도 요약합니다. [자세한 정보를 알아보세요](../authentication/howto-authentication-methods-activity.md).
 
---

### <a name="refresh-and-session-token-lifetimes-configurability-in-configurable-token-lifetime-ctl-are-retired"></a>CTL(구성 가능한 토큰 수명)의 새로 고침 및 세션 토큰 수명 구성이 사용 중지됨

**유형:** 사용되지 않음  
**서비스 범주:** 기타  
**제품 기능:** 사용자 인증
 
CTL에서 새로 고침 및 세션 수명 구성이 사용 중지되었습니다. Azure Acti0ve Directory는 기존 정책에서 새로 고침 및 세션 토큰 구성을 더 적용하지 않습니다. [자세히 알아보기](../develop/active-directory-configurable-token-lifetimes.md#token-lifetime-policies-for-refresh-tokens-and-session-tokens).
 
---