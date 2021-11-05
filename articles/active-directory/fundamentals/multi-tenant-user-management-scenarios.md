---
title: Azure Active Directory의 다중 테넌트 사용자 관리를 사용하는 일반적인 시나리오
description: 게스트 계정을 사용하여 Azure Active Directory 테넌트에서 사용자 액세스를 구성할 수 있는 일반적인 시나리오에 대해 알아봅니다.
services: active-directory
author: BarbaraSelden
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 09/25/2021
ms.author: baselden
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: fde715f688d7e19477894f3239d174d7925edb78
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131059398"
---
# <a name="multi-tenant-user-management-scenarios"></a>다중 테넌트 사용자 관리 시나리오

## <a name="end-user-initiated-scenario"></a>최종 사용자가 시작한 시나리오
최종 사용자가 시작한 시나리오의 경우 리소스 테넌트 관리자는 테넌트 내 사용자에게 특정 기능을 위임합니다. 관리자는 최종 사용자가 게스트 사용자를 테넌트, 앱 또는 리소스에 초대할 수 있도록 설정합니다. 홈 테넌트에서 사용자가 개별적으로 초대되거나 등록됩니다.

사용 사례의 예는 프로젝트에서 하도급 업자와 함께 작업하는 전역 전문 서비스 회사에 대한 것입니다. 하도급 업자 사용자는 회사의 애플리케이션 및 문서에 액세스해야 합니다. 회사의 관리자는 하도급 업자를 초대하거나 하도급 업자 리소스 액세스를 위한 셀프 서비스를 구성할 수 있는 기능을 회사 최종 사용자에게 위임할 수 있습니다.

### <a name="provision-accounts"></a>계정 프로비전

최종 사용자가 리소스 테넌트 리소스에 액세스하도록 초대받을 수 있는 여러 가지 방법이 있습니다. 가장 널리 사용되는 다섯 가지 방법은 다음과 같습니다. 

* [애플리케이션 기반 초대](../external-identities/o365-external-user.md). Microsoft 애플리케이션은 게스트 사용자의 초대를 사용하도록 설정할 수 있습니다. B2B 초대 설정은 Azure AD B2B와 관련 애플리케이션 또는 애플리케이션 모두에서 구성해야 합니다.

* [MyApps]../user-help/my-apps-portal-end-user-access). 사용자는 MyApps를 사용하여 게스트 사용자를 애플리케이션에 초대하고 할당합니다. 사용자 계정에는 [애플리케이션 셀프 서비스 등록](../manage-apps/manage-self-service-access.md) 승인자 권한이 있어야 합니다. 그룹 소유자인 경우 게스트 사용자를 그룹에 초대할 수 있습니다.

* [권한 관리](../governance/entitlement-management-overview.md): 관리자 또는 리소스 소유자가 리소스, 허용된 외부 조직, 게스트 사용자 만료 및 액세스 정책을 액세스 패키지에서 함께 연결할 수 있도록 설정합니다. 게스트 사용자는 액세스 패키지를 게시하여 리소스 액세스를 위해 셀프 서비스 등록을 사용하도록 설정할 수 있습니다.

* [게스트 초대자 역할](../external-identities/delegate-invitations.md)이 지정된 [Azure Portal](../external-identities/add-users-administrator.md) 최종 사용자는 Azure Portal에 로그인하고 Azure Active Directory의 사용자 메뉴에서 게스트 사용자를 초대할 수 있습니다.

* [게스트 초대자 역할](../external-identities/delegate-invitations.md)이 지정된 [프로그래밍 방식(PowerShell, Graph API)](../external-identities/customize-invitation-api.md) 최종 사용자는 PowerShell 또는 Graph API를 통해 게스트 사용자를 초대할 수 있습니다. 

### <a name="redeem-invitations"></a>초대 사용

리소스에 액세스하기 위한 프로비저닝 계정의 일부로 초대된 사용자 이메일 주소로 이메일 초대가 전송됩니다. 초대된 사용자가 초대를 받으면 다음을 수행할 수 있습니다.
* 이메일에 포함된 링크를 따라 사용 URL에 연결합니다.
* 리소스에 직접 액세스해 보세요. 

사용자가 리소스에 직접 액세스하려고 하면 JIT 사용으로 명명됩니다. 다음은 각 사용 방법에 대한 사용자 환경입니다. 

#### <a name="redemption-url"></a>사용 URL

이메일의 [상환 URL](../external-identities/redemption-experience.md)에 액세스하면 초대된 사용자가 초대를 승인하거나 거부할 수 있습니다(필요한 경우 게스트 사용자 계정 만들기). 

#### <a name="just-in-time-redemption"></a>JIT 사용

사용자는 다음과 같은 경우 JIT 사용을 위해 리소스 URL에 직접 액세스할 수 있습니다.

* 초대된 사용자에게 이미 Azure AD 또는 Microsoft 계정이 있음  
또는

* [이메일 일회용 암호](../external-identities/one-time-passcode.md)를 사용하도록 설정

JIT 사용 중 몇 가지 포인트:

* 관리자가 개인정보처리방침 동의를 표시하지 않은 경우 사용자는 리소스에 액세스하기 전에 개인정보처리방침 계약 페이지에 동의해야 합니다.

* PowerShell을 사용하면 [PowerShell을 통해](/powershell/module/azuread/new-azureadmsinvitation?view=azureadps-2.0&preserve-view=true) 초대할 때 이메일을 보낼지 여부를 제어할 수 있습니다.

* [허용 목록 또는 차단 목록](../external-identities/allow-deny-list.md)을 사용하여 특정 조직의 게스트 사용자에게 초대를 허용하거나 차단할 수 있습니다.

자세한 내용은 [Azure Active Directory B2B 협업 초대 상환](../external-identities/redemption-experience.md)을 참조하세요.

#### <a name="important--enable-one-time-passcode-authentication"></a>중요 – 일회용 암호 인증을 사용하도록 설정 

[이메일 일회용 암호 인증](../external-identities/one-time-passcode.md)을 사용하도록 설정하는 것이 좋습니다. 이 기능은 다음과 같은 다른 수단을 통해 인증할 수 없는 경우 게스트 사용자를 인증합니다.

* Azure AD

* MSA(Microsoft 계정)

* Google 페더레이션을 통한 Gmail 계정

* 직접 페더레이션을 통한 SAML/WS-Fed IDP의 계정

일회성 암호 인증을 사용하면 Microsoft 계정을 만들 필요가 없습니다. 게스트 사용자가 초대를 사용하거나 공유 리소스에 액세스하면 임시 코드를 받습니다. 코드가 이메일 주소로 전송되면 코드를 입력하여 계속 로그인합니다. 

이메일 일회용 암호 인증을 사용하도록 설정하지 않으면 Microsoft 계정 또는 JIT "관리되지 않는" Azure AD 테넌트를 만들 수 있습니다.

>**중요**: Microsoft는 각 클라우드 환경에서 이 기능이 GA(일반 공급)가 됨에 따라 관리되지 않는 테넌트 및 해당 사용자를 만드는 작업을 더 이상 사용하지 않습니다. 

### <a name="manage-accounts"></a>계정 관리

리소스 테넌트 관리자는 리소스 테넌트에서 게스트 사용자 계정을 관리합니다. 게스트 사용자 계정은 홈 테넌트의 업데이트된 값에 따라 업데이트되지 않습니다. 실제로 수신된 유일하게 표시되는 특성에는 이메일 주소와 표시 이름이 포함됩니다.

게스트 사용자 개체에 더 많은 특성을 구성하여 시나리오를 용이하게 할 수 있습니다. 예를 들어, 주소록을 연락처 세부 정보로 채우거나 권한 시나리오에 포함할 수 있습니다. 예를 들어 다음을 고려하세요.

* HiddenFromAddressListsEnabled

* GivenName

* Surname

* 제목

* 부서

* TelephoneNumber

이러한 특성은 [전역 주소 목록에 게스트 추가](/microsoft-365/admin/create-groups/manage-guest-access-in-groups?view=o365-worldwide&preserve-view=true)로 설정될 수 있습니다. 다른 시나리오에는 액세스 패키지, 동적 그룹 멤버 자격, SAML 클레임 등에 대한 권한 및 사용 권한 설정과 같은 다른 특성이 필요할 수 있습니다.

참고: 초대된 게스트 사용자는 기본적으로 GAL(전역 주소 목록)에서 숨겨집니다. 게스트 사용자 특성을 숨김으로 설정하여 통합 GAL에 포함시킵니다. 자세한 내용은 [Microsoft Exchange Online](multi-tenant-common-considerations.md#microsoft-exchange-online) 설명서를 참조하세요.

### <a name="deprovision-accounts"></a>계정 프로비전 해제

최종 사용자가 시작한 시나리오는 액세스 결정을 분산합니다. 그러나 액세스 결정을 분산하면 게스트 사용자 및 연결된 액세스를 제거할 시기를 결정하는 데 문제가 발생합니다. [권한 관리](../governance/entitlement-management-overview.md) 및 [액세스 검토](../governance/manage-guest-access-with-access-reviews.md)는 기존 게스트 사용자와 리소스에 대한 액세스를 검토하고 제거하는 방법을 제공합니다. 

참고: 사용자가 권한 관리 외부에서 초대되는 경우 해당 게스트 사용자의 액세스를 검토하고 관리하는 별도의 프로세스를 만들어야 합니다. 예를 들어, 게스트 사용자가 SharePoint Online을 통해 직접 초대되는 경우 권한 관리 프로세스에 포함되지 않습니다.

## <a name="scripted-scenario"></a>스크립팅된 시나리오

스크립팅된 시나리오의 경우 리소스 테넌트 관리자는 스크립팅된 끌어오기 프로세스를 배포하여 게스트 사용자의 검색 및 프로비저닝을 자동화합니다. 이 방법은 스크립팅된 메커니즘을 사용하는 고객에게 일반적입니다.

사용 사례의 예로는 경쟁 업체를 인수한 전역 배송 회사가 있습니다. 각 회사에는 단일 Azure AD 테넌트가 있습니다. 사용자가 초대 또는 사용 단계를 수행할 필요 없이 다음 "1일" 시나리오가 작동하기를 원합니다. 모든 사용자는 다음을 수행할 수 있어야 합니다. 

* 프로비전되는 모든 리소스에 단일 로그온 사용

* 서로를 찾고 통합 GAL에서 다른 리소스도 찾기

* 서로의 존재를 확인하고 인스턴트 메시지를 시작할 수 있음

* 동적 그룹 멤버 자격에 따라 애플리케이션에 액세스

이 경우 각 조직의 테넌트는 기존 직원에 대한 홈 테넌트이고 다른 조직의 직원에 대한 리소스 테넌트입니다.

### <a name="provision-accounts"></a>계정 프로비전

[델타 쿼리](/graph/delta-query-overview)를 사용하면 테넌트 관리자가 스크립팅된 끌어오기 프로세스를 배포하여 ID 검색 및 프로비저닝을 자동화하여 리소스 액세스를 지원할 수 있습니다. 이 프로세스는 홈 테넌트에서 새 사용자를 확인하고 B2B Graph API를 사용하여 해당 사용자를 리소스 테넌트에서 초대된 사용자로 프로비전합니다. 다음 다이어그램은 구성 요소를 보여줍니다. 
### <a name="multi-tenant-topology"></a>다중 테넌트 토폴로지

![다중 테넌트 시나리오](media\multi-tenant-user-management-scenarios\multi-tenant-scripted-scenario.png)

* 각 테넌트 관리자는 자격 증명을 미리 정렬하고 각 테넌트 읽기를 허용하는 데 동의합니다.

* 테넌트 관리자가 열거형을 자동화하고 범위가 지정된 사용자를 리소스 테넌트로 "끌어올" 수 있습니다.

* 동의한 사용 권한으로 MS Graph API를 사용하여 초대 API를 통해 사용자를 읽고 프로비전합니다.

* 초기 프로비저닝은 원본 특성을 읽고 대상 사용자 개체에 적용할 수 있습니다.

### <a name="manage-accounts"></a>계정 관리

리소스 조직은 리소스 테넌트에서 사용자의 메타데이터 특성을 업데이트하여 공유 시나리오를 지원하기 위해 프로필 데이터를 보강하도록 선택할 수 있습니다. 그러나 지속적인 동기화가 필요한 경우 동기화된 솔루션이 더 나은 옵션일 수 있습니다.

### <a name="deprovision-accounts"></a>계정 프로비전 해제

[델타 쿼리](/graph/delta-query-overview)는 게스트 사용자를 프로비전 해제해야 하는 경우 신호를 보낼 수 있습니다. [권한 관리](../governance/entitlement-management-overview.md) 및 [액세스 검토](../governance/manage-guest-access-with-access-reviews.md)는 기존 게스트 사용자와 리소스에 대한 액세스를 검토하고 제거하는 방법을 제공할 수도 있습니다. 

참고: 사용자가 권한 관리 외부에서 초대되는 경우 해당 게스트 사용자의 액세스를 검토하고 관리하는 별도의 프로세스를 만들어야 합니다. 예를 들어, 게스트 사용자가 SharePoint Online을 통해 직접 초대되는 경우 권한 관리 프로세스에 포함되지 않습니다.

## <a name="automated-scenario"></a>자동화된 시나리오

지금까지 가장 복잡한 패턴은 테넌트 간에 동기화된 공유입니다. 이 패턴은 사용자가 시작하거나 스크립팅하는 것보다 더 자동화된 관리 및 프로비전 해제 시나리오를 사용하도록 설정합니다. 자동화된 시나리오의 경우 리소스 테넌트 관리자는 ID 프로비저닝 시스템을 사용하여 프로비전 및 프로비전 해제 프로세스를 자동화합니다.

사용 사례의 예로는 여러 자회사가 있는 다국적 기업이 있습니다. 각 테넌트는 자체 Azure AD 테넌트가 있지만 함께 작동해야 합니다. 테넌트 간에 새 사용자를 동기화하는 것 외에도 특성 업데이트가 자동으로 동기화되어야 합니다. 프로비전 해제를 자동화해야 합니다. 예를 들어, 직원이 더 이상 자회사에 없는 경우 다음 동기화 중에 다른 모든 테넌트에서 해당 계정을 제거해야 합니다.

또는 다음과 같은 확장된 시나리오를 고려해해야 합니다. DIB(방위 산업 기지) 계약자는 방위 기반 및 상업 기반 자회사를 보유하고 있습니다. 여기에는 다음과 같은 경쟁 규정 요구 사항이 있습니다.

* 미국 방위 비즈니스는 미국 소버린 클라우드 테넌트에 상주합니다. 예를 들어, Microsoft 365 미국 정부 GCC High가 있습니다.

* 상업 비즈니스는 공용의 별도 Azure AD 테넌트에 상주합니다. 예를 들어, 전역 Azure 클라우드에서 실행되는 Azure AD 환경이 있습니다. 

 "교차 소버린 클라우드" 아키텍처에 배포된 단일 회사처럼 작동하려면 모든 사용자가 두 테넌트 모두에 동기화됩니다. 이렇게 하면 두 테넌트 모두에서 통합 GAL을 사용하도록 설정됩니다. 또한 두 테넌트 모두에 자동으로 동기화된 사용자에게 애플리케이션 및 콘텐츠에 대한 권한 및 제한 사항이 포함되는지 확인할 수 있습니다. 예:

* 미국 직원은 두 테넌트 모두에 대해 유비쿼터스 액세스 권한을 가질 수 있습니다.

* 미국 이외의 직원은 두 테넌트 모두의 통합 GAL에 표시되지만 GCC High 테넌트에서 보호된 콘텐츠에 액세스할 수 없습니다. 

이렇게 하려면 적절한 권한 및 데이터 보호 정책과 연결하는 동안 두 테넌트 모두에서 사용자를 구성하기 위해 자동 동기화 및 ID 관리가 필요합니다.

### <a name="provision-accounts"></a>계정 프로비전

이 고급 배포에서는 [MIM](/microsoft-identity-manager/microsoft-identity-manager-2016)(Microsoft Identity Manager)를 동기화 엔진으로 사용합니다. MIM은 [MS Graph API](/developer.microsoft.com/graph/)와 [Exchange Online PowerShell](/powershell/exchange/exchange-online/exchange-online-powershell?view=exchange-ps&preserve-view=true)을 호출합니다. 대체 구현에는 [Microsoft Consulting Services](/industry/services/consulting)에서 클라우드 호스트된 [ADSS](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview)(Active Directory Synchronization Services) 관리되는 서비스 제품이 포함될 수 있습니다. 다른 ID 관리 제품을 사용하여 처음부터 만들 수 있는 타사 제품도 있습니다.

이러한 시나리오는 복잡한 시나리오이며 계획하고 실행하는 동안 파트너, Microsoft 계정 팀 및 기타 사용 가능한 리소스와 함께 작업하는 것이 좋습니다. 

참고: 이 문서의 범위를 벗어나는 고려 사항이 있습니다. 예를 들어, [온-프레미스 애플리케이션의 통합](../app-proxy/what-is-application-proxy.md)아 있습니다. 

### <a name="choose-the-right-topology"></a>올바른 토폴로지 선택

대부분의 고객은 자동화된 시나리오에서 두 토폴로지 중 하나를 사용합니다. 

* 메시 토폴로지는 모든 테넌트에서 모든 리소스 공유를 사용하도록 설정합니다. 다른 테넌트 사용자는 각 리소스 테넌트에서 게스트 사용자로 만들어집니다.

* 단일 리소스 테넌트 토폴로지는 다른 회사의 사용자가 외부 게스트 사용자인 단일 테넌트(리소스 테넌트)를 사용합니다. 

다음 테이블에서는 솔루션을 디자인하는 동안 의사 결정 트리를 사용할 수 있습니다. 테이블 다음에 오는 두 토폴로지를 모두 보여 줍니다. 조직에 적합한 것을 결정하는 데 도움이 되려면 다음 사항을 고려하세요. 

메시와 단일 리소스 테넌트 토폴로지 비교

| 고려 사항| 메시 토폴로지| 단일 리소스 테넌트 |
| - | - |-|
| 각 회사에는 사용자 및 리소스가 있는 별도의 Azure AD 테넌트가 있음| 예| 예 |
| **리소스 위치 및 협업**| |  |
| 공유 앱 및 기타 리소스는 현재 홈 테넌트에 남아 있습니다.| Yes| 아니요 - 리소스 테넌트의 리소스만 공유됩니다. |
| 개별 회사의 GAL(통합 GAL)에서 모두 볼 수 있음| Yes| 아니요 |
| **리소스 액세스 및 관리**| |  |
| Azure AD에 연결된 모든 애플리케이션을 모든 회사 간에 공유할 수 있습니다.| Yes| 아니요 - 리소스 테넌트의 리소스만 공유됩니다. 다른 테넌트의 나머지 리소스는 그렇지 않습니다. |
| 전역 리소스 관리| 테넌트 수준에서 계속| 리소스 테넌트에서 통합 |
| 라이선스 – Office 365 <br>SharePoint Online, 통합 GAL, Teams 액세스는 모두 게스트를 지원하지만, 다른 Exchange Online 시나리오는 그렇지 않습니다.| 테넌트 수준에서 계속| 테넌트 수준에서 계속 |
| 라이선스 - [Azure AD(프리미엄)](../external-identities/external-identities-pricing.md)| 처음 50K 월간 활성 사용자는 무료입니다(테넌트당).| 처음 50K 월간 활성 사용자는 무료입니다. |
| 라이선스 – SaaS 앱| 개별 테넌트에 유지, 테넌트당 사용자당 라이선스 필요| 모든 공유 리소스는 단일 리소스 테넌트에서 상주합니다. 해당하는 경우 단일 테넌트로 라이선스 통합을 조사할 수 있습니다. |

#### <a name="mesh-topology"></a>메시 토폴로지

![메시 토폴로지](media/multi-tenant-user-management-scenarios/mesh.png)

메시 토폴로지에서 각 홈 테넌트의 모든 사용자는 리소스 테넌트가 되는 다른 테넌트 각각에 동기화됩니다. 

* 이렇게 하면 테넌트 내의 모든 리소스가 게스트 사용자와 공유할 수 있도록 설정됩니다. 

* 이렇게 하면 각 조직이 대기업의 모든 사용자를 볼 수 있도록 설정됩니다. 위의 그림에는 각각 홈 사용자와 다른 세 테넌트에서 온 게스트 사용자를 포함하는 4개의 통합 GAL이 있습니다.

이 시나리오에서 사용자를 프로비전, 관리 및 프로비전 해제하는 방법에 대한 자세한 내용은 이 문서의 [일반 고려 사항](multi-tenant-common-considerations.md#directory-object-considerations) 섹션을 참조하세요.

#### <a name="mesh-topology-for-cross-sovereign-cloud"></a>교차 소버린 클라우드용 메시 토폴로지

메시 토폴로지는 교차 소버린 클라우드 솔루션에 걸쳐 있는 DIB 방위 계약자 시나리오에서와 같이 2개 정도의 테넌트에서 사용할 수 있습니다. 메시 토폴로지와 마찬가지로 각 홈 테넌트의 모든 사용자는 다른 테넌트에 동기화되어 사실상 리소스 테넌트가 됩니다. 위 그림에서 공용 상업용 테넌트 멤버 사용자는 미국 소버린 GCC High 테넌트에 게스트 사용자 계정으로 동기화됩니다. 동시에 GCC High 멤버 사용자는 상업용 테넌트에 게스트 사용자 계정으로 동기화됩니다.

>**참고**: 이 그림에서는 데이터가 저장되는 위치도 설명합니다. 데이터 분류 및 규정 준수는 이 백서의 범위를 벗어나지만, 애플리케이션 및 콘텐츠에 대한 권한 및 제한 사항을 포함할 수 있음을 보여줍니다. 콘텐츠에는 멤버 사용자의 '개인 데이터'가 상주하는 위치가 포함될 수 있습니다. 예를 들어, Exchange Online 사서함 또는 비즈니스용 OneDrive에 저장된 데이터가 있습니다. 콘텐츠는 리소스 테넌트가 아니라 홈 테넌트에만 있을 수 있습니다. 공유 데이터는 두 테넌트 중 하나에 있을 수 있습니다. 액세스 제어 및 조건부 액세스 정책을 통해 콘텐츠에 대한 액세스를 제한할 수 있습니다.

#### <a name="single-resource-tenant-topology"></a>단일 리소스 테넌트 토폴로지

![단일 리소스 테넌트](media/multi-tenant-user-management-scenarios/single-resource-tenant.png)

단일 리소스 테넌트 토폴로지에서 사용자와 해당 특성은 리소스 테넌트 (위 그림의 회사 A)에 동기화됩니다. 

* 멤버 조직 간에 공유되는 모든 리소스는 단일 리소스 테넌트에 상주해 야 합니다.
  * 여러 자회사에 동일한 SaaS 앱에 대한 구독이 있는 경우 해당 구독을 통합할 수 있습니다.

* 리소스 테넌트의 GAL만 모든 회사의 사용자를 표시합니다.

### <a name="manage-accounts"></a>계정 관리

이 솔루션은 원본 테넌트 사용자의 특성 변경 내용을 검색하고 리소스 테넌트 게스트 사용자에 동기화합니다. 이러한 특성은 권한 부여 결정을 내리는 데 사용할 수 있습니다. 예를 들어, 동적 그룹을 사용하는 경우입니다.

### <a name="deprovision-accounts"></a>계정 프로비전 해제

자동화는 원본 환경에서 개체 삭제를 검색하고 대상 환경에서 연결된 게스트 사용자 개체를 삭제합니다.

이 시나리오에서 사용자를 프로비전, 관리 및 프로비전 해제하는 방법에 대한 자세한 내용은 이 문서의 [일반 고려 사항](multi-tenant-common-considerations.md) 섹션을 참조하세요.

## <a name="next-steps"></a>다음 단계
[다중 테넌트 사용자 관리 소개](multi-tenant-user-management-introduction.md)

[다중 테넌트 일반 고려 사항](multi-tenant-common-considerations.md)

[다중 테넌트 일반 솔루션](multi-tenant-common-solutions.md)
