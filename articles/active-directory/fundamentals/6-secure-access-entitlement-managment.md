---
title: Azure Active Directory 권한 관리를 사용하여 외부 액세스 관리
description: 전체 외부 액세스 보안 계획의 일부로 Azure Active Directory 권한 관리를 사용하는 방법입니다.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: ccd033ec224762fef984e98971334ec93bff931f
ms.sourcegitcommit: 285d5c48a03fcda7c27828236edb079f39aaaebf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2021
ms.locfileid: "113231677"
---
# <a name="manage-external-access-with-entitlement-management"></a>권한 관리를 사용하여 외부 액세스 관리 


[권한 관리](../governance/entitlement-management-overview.md)는 조직에서 액세스 요청 워크플로, 액세스 할당, 검토 및 만료를 자동화하여 ID 및 액세스 수명 주기를 대규모로 관리할 수 있는 ID 거버넌스 기능입니다. 권한 관리를 통해 위임된 비관리자는 다른 조직의 외부 사용자가 액세스를 요청할 수 있는 [액세스 패키지](../governance/entitlement-management-overview.md)를 만들 수 있습니다. 요청을 평가하고 되풀이 검토를 통해 시간이 제한된 액세스에 대해 사용자를 [프로비저닝](../governance/what-is-provisioning.md)하도록 단일 및 다중 스테이지 승인 워크플로를 구성할 수 있습니다. 권한 관리를 통해 외부 계정의 정책 기반 프로비저닝 및 프로비저닝 해제를 수행할 수 있습니다.

## <a name="key-concepts-for-enabling-entitlement-management"></a>권한 관리를 사용하기 위한 주요 개념

다음 주요 개념은 권한 관리를 이해하는 데 중요합니다.

### <a name="access-packages"></a>액세스 패키지

[액세스 패키지](../governance/entitlement-management-overview.md)는 권한 관리의 기초입니다. 액세스 패키지는 사용자가 프로젝트에서 협업하거나 다른 작업을 수행하는 데 필요한 정책으로 관리되는 리소스의 그룹입니다. 예를 들어, 액세스 패키지에는 다음이 포함될 수 있습니다.

* 특정 SharePoint 사이트에 대한 액세스

* Salesforce 같은 사용자 지정 사내 및 SaaS 앱을 포함하는 엔터프라이즈 애플리케이션

* Microsoft Teams.

* Microsoft 365 그룹 

### <a name="catalogs"></a>Catalogs

액세스 패키지는 [카탈로그](../governance/entitlement-management-catalog-create.md)에 있습니다. 관련 리소스 및 액세스 패키지를 그룹화하고 관리 기능을 위임하려는 경우 카탈로그를 만듭니다. 먼저 카탈로그에 리소스를 추가하면 액세스 패키지에 해당 리소스를 추가할 수 있습니다. 예를 들어, “재무” 카탈로그를 만들고 재무 팀원에게 [카탈로그 관리를 위임](../governance/entitlement-management-delegate.md)할 수 있습니다. 이후 해당 팀원은 [리소스를 추가](../governance/entitlement-management-catalog-create.md)하고, 액세스 패키지를 만들고, 해당 패키지에 대한 액세스 승인을 관리할 수 있습니다.

다음 다이어그램에서는 만료일이 있는 액세스 패키지에 대한 액세스 권한을 얻는 외부 사용자의 일반적인 거버넌스 수명 주기를 보여 줍니다.

![외부 사용자 거버넌스 주기의 다이어그램](media/secure-external-access/6-governance-lifecycle.png)

### <a name="self-service-external-access"></a>셀프 서비스 외부 액세스

[Azure AD 내 액세스 포털](../governance/entitlement-management-request-access.md)을 통해 액세스 패키지를 표시하여 외부 사용자가 액세스를 요청하도록 할 수 있습니다. 정책에 따라 액세스 패키지를 요청할 수 있는 사용자가 결정됩니다. 액세스 패키지를 요청할 수 있는 사용자를 지정합니다.

* 특정 [연결된 조직](../governance/entitlement-management-organization.md)

* 구성된 모든 연결된 조직

* 모든 조직의 모든 사용자

* 테넌트에 이미 있는 멤버 또는 게스트 사용자

### <a name="approvals"></a>승인   
‎액세스 패키지에는 액세스에 대한 필수 승인이 포함될 수 있습니다. **항상 외부 사용자의 승인 프로세스를 구현합니다**. 승인은 단일 또는 다중 스테이지 승인일 수 있습니다. 승인은 정책에 따라 결정됩니다. 내부 및 외부 사용자가 둘 다 동일한 패키지에 액세스해야 하는 경우 연결된 조직의 여러 가지 범주 및 내부 사용자에 대해 서로 다른 액세스 정책을 설정할 수 있습니다.

### <a name="expiration"></a>만료  
‎액세스 패키지에는 만료 날짜가 포함될 수 있습니다. 만료는 특정 날짜로 설정하거나 사용자에게 액세스를 위한 특정 기간(일)을 제공할 수 있습니다. 액세스 패키지가 만료되고 사용자에게 다른 액세스 권한이 없는 경우 사용자를 나타내는 B2B 게스트 사용자 개체를 삭제하거나 로그인을 차단할 수 있습니다. 외부 사용자의 액세스 패키지에 대해 만료를 적용하는 것이 좋습니다. 일부 액세스 패키지에는 만료가 없습니다. 만료되지 않는 패키지의 경우 액세스 검토를 수행해야 합니다.

### <a name="access-reviews"></a>액세스 검토

액세스 패키지에는 패키지 소유자 또는 지정된 사용자가 사용자 액세스가 계속 필요함을 증명해야 하는 주기적인 [액세스 검토](../governance/manage-guest-access-with-access-reviews.md)가 필요할 수 있습니다. 

검토를 설정하기 전에 다음을 결정합니다.

* 대상

   * 계속 액세스의 기준은 무엇인가요?

   * 지정된 검토자는 누구인가요?

* 예약된 검토는 얼마나 자주 수행해야 하나요?

   * 기본적으로 매월, 매분기, 매반기 또는 매년 옵션이 제공됩니다. 

   * 외부 액세스를 지원하는 패키지의 경우 매분기 또는 더 자주 수행하는 것이 좋습니다. 

 

> [!IMPORTANT]
> 액세스 패키지의 액세스 검토는 권한 관리를 통해 부여된 액세스만 검토합니다. 따라서 권한 관리가 적용되지 않는 외부 사용자에게 제공되는 액세스를 검토하는 다른 프로세스를 설정해야 합니다.

액세스 검토에 관한 자세한 내용은 [Azure AD 액세스 검토 배포 계획](../governance/deploy-access-reviews.md)을 참조하세요.

## <a name="using-automation-in-entitlement-management"></a>권한 관리에서 자동화 사용

다음을 포함하여 [Microsoft Graph를 사용하여 권한 관리 기능](/graph/tutorial-access-package-api)을 수행할 수 있습니다.

* [액세스 패키지 관리](/graph/api/resources/accesspackage?view=graph-rest-beta&preserve-view=true)

* [액세스 검토 관리](/graph/api/resources/accessreviewsv2-root?view=graph-rest-beta&preserve-view=true)

* [연결된 조직 관리](/graph/api/resources/connectedorganization?view=graph-rest-beta&preserve-view=true)

* [권한 관리 설정 관리](/graph/api/resources/entitlementmanagementsettings?view=graph-rest-beta&preserve-view=true)

## <a name="recommendations"></a>권장 사항 

권한 관리를 사용하여 외부 액세스를 관리하는 것이 좋습니다.

**하나 이상의 비즈니스 파트너를 포함하는 프로젝트의 경우 [액세스 패키지를 만들고 사용](../governance/entitlement-management-access-package-create.md)하여 해당 파트너의 사용자에게 리소스에 대한 액세스를 온보딩하고 프로비저닝합니다**. 

* 디렉터리에 B2B 사용자가 이미 있는 경우 해당 사용자를 적절한 액세스 패키지에 직접 할당할 수도 있습니다.

* [Azure Portal](../governance/entitlement-management-access-package-assignments.md) 또는 [Microsoft Graph](/graph/api/resources/accesspackageassignmentrequest?view=graph-rest-beta&preserve-view=true)를 통해 액세스 권한을 할당할 수 있습니다.

**Identity Governance 설정을 사용하여 액세스 패키지가 만료될 때 디렉터리에서 사용자를 제거합니다**.

![외부 사용자의 수명 주기 관리를 구성하는 스크린샷](media/secure-external-access/6-manage-external-lifecycle.png)

이 설정은 권한 관리를 통해 온보딩된 사용자에만 적용됩니다.

**누가 액세스해야 하는지에 관한 자세한 정보를 보유한 비즈니스 소유자에게 [카탈로그 및 액세스 패키지의 관리를 위임](../governance/entitlement-management-delegate.md)합니다**.

![카탈로그를 구성하는 스크린샷](media/secure-external-access/6-catalog-management.png)

**‎외부 사용자가 액세스할 수 있는 [액세스 패키지의 만료를 적용](../governance/entitlement-management-access-package-lifecycle-policy.md)합니다.**


![액세스 패키지 만료를 구성하는 스크린샷](media/secure-external-access/6-access-package-expiration.png)

* 프로젝트 기반 액세스 패키지의 종료 날짜를 알고 있는 경우 날짜를 사용하여 특정 날짜를 설정합니다. 

* 해당 날짜를 모르는 경우 복수 연도 계약으로 알려져 있지 않는 한 만료는 365일을 넘지 않는 것이 좋습니다.

* 사용자가 액세스 연장하도록 허용합니다.

* 연장 권한을 부여하려면 승인이 필요합니다.

**[패키지의 액세스 검토를 적용](../governance/manage-guest-access-with-access-reviews.md)하여 게스트의 부적절한 액세스를 방지합니다.**

![새 액세스 패키지를 만드는 스크린샷](media/secure-external-access/6-new-access-package.png)

* 매분기 검토를 적용합니다.

* 규정 준수가 중요한 프로젝트의 경우 외부 사용자의 직접 검토 대신 검토자를 특정 검토자로 설정합니다. 처음에는 액세스 패키지 관리자인 사용자를 검토자로 설정하는 것이 좋습니다. 

* 덜 중요한 프로젝트의 경우 사용자가 직접 검토하도록 하면 조직이 더 이상 홈 조직을 사용하지 않는 사용자의 액세스 권한을 제거해야 하는 부담이 감소합니다.

자세한 내용은 [Azure AD 권한 관리에서 외부 사용자에 대한 액세스 관리](../governance/entitlement-management-external-users.md)를 참조하세요. 

### <a name="next-steps"></a>다음 단계

리소스에 대한 외부 액세스 보안에 관해 다음 문서를 참조하세요. 나열된 순서대로 작업을 수행하는 것이 좋습니다.

1. [외부 액세스에 대한 보안 태세 결정](1-secure-access-posture.md)

2. [현재 상태 검색](2-secure-access-current-state.md)

3. [거버넌스 계획 만들기](3-secure-access-plan.md)

4. [보안을 위해 그룹 사용](4-secure-access-groups.md)

5. [Azure AD B2B로 전환](5-secure-access-b2b.md)

6. [권한 관리를 통한 보안 액세스](6-secure-access-entitlement-managment.md)(현재 위치)

7. [조건부 액세스 정책을 통한 보안 액세스](7-secure-access-conditional-access.md)

8. [민감도 레이블을 사용한 보안 액세스](8-secure-access-sensitivity-labels.md)

9. [Microsoft Teams, OneDrive 및 SharePoint에 대한 보안 액세스](9-secure-access-teams-sharepoint.md)

 

