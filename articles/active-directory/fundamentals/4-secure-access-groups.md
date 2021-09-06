---
title: Azure Active Directory 및 Microsoft 365의 그룹으로 외부 액세스 보안
description: Azure Active Directory 및 Microsoft 365 그룹을 사용하면 외부 사용자가 리소스에 액세스할 때 보안을 향상시킬 수 있습니다.
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
ms.openlocfilehash: 2500d1872c2c70c8d39afa0b564c35a8bb9bcb9b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122536754"
---
# <a name="securing-external-access-with-groups"></a>그룹으로 외부 액세스 보안 

그룹은 액세스 제어 전략에 필수적인 부분입니다. Azure AD(Azure Active Directory) 보안 그룹 및 Microsoft 365 그룹은 리소스에 대한 액세스 보안의 기초로 사용될 수 있습니다.

그룹은 다음 액세스 제어 메커니즘의 기초로 사용되는 최고의 방법입니다.

* 조건부 액세스 정책

* 권한 관리 액세스 패키지 

* Microsoft 365 리소스, Microsoft Teams 및 SharePoint 사이트에 액세스

그룹에는 다음 역할이 포함됩니다.

* 소유자 - 그룹 소유자는 그룹 설정 및 멤버 자격을 관리합니다.

* 멤버 - 그룹에 할당된 권한 및 액세스를 상속하는 멤버입니다.

* 게스트 - 게스트는 조직 외부의 멤버입니다. 

## <a name="determine-your-group-strategy"></a>그룹 전략 결정

리소스에 대한 외부 액세스를 안전하게 처리하기 위해 그룹 전략을 개발할 때는 [원하는 보안 태세](1-secure-access-posture.md)를 고려해서 다음 옵션을 결정해야 합니다.

* **그룹을 만들 수 있는 사람은 누구인가요?** 관리자만 그룹을 만들도록 허용할지 아니면 직원 및/또는 외부 사용자도 그룹을 만들도록 허용할지 선택해야 합니다.

   * *기본적으로 모든 테넌트 멤버가 Azure AD 보안 그룹을 만들 수 있습니다.* . 

      * [관리자가 아닌 사용자에 대해 포털 액세스를 제한](../develop/howto-restrict-your-app-to-a-set-of-users.md)하고 [PowerShell](../enterprise-users/groups-troubleshooting.md)에서 그룹 만들기 기능을 사용하지 않도록 설정할 수 있습니다. 

      * 또한 [Azure Active Directory에서 셀프 서비스 그룹 관리를 설정](../enterprise-users/groups-self-service-management.md)할 수도 있습니다. 

   * *기본적으로 모든 사용자가 Microsoft 365 그룹을 만들 수 있고 테넌트에 있는 모든(내부 및 외부) 사용자가 조인할 수 있도록 그룹이 열려 있습니다*. 

      * [Microsoft 365 그룹 만들기](/microsoft-365/solutions/manage-creation-of-groups)를 특정 보안 그룹의 멤버로 제한할 수 있습니다. Windows PowerShell을 사용하여 이 설정을 구성합니다. 

* **그룹에 사용자를 초대할 수 있는 사람은 누구인가요?** 모든 그룹 멤버가 다른 멤버를 추가할 수 있는지 아니면 그룹 소유자만 멤버를 추가할 수 있는지 선택해야 합니다.

* **그룹에 초대될 수 있는 사람은 누구인가요?** 기본적으로 외부 사용자를 그룹에 추가할 수 있습니다. 

### <a name="assign-users-to-groups"></a>그룹에 사용자 할당

사용자 개체에 있는 사용자 특성 또는 다른 기준에 따라 수동으로 사용자를 그룹에 할당할 수 있습니다. 해당 특성에 따라 동적으로만 사용자를 그룹에 할당할 수 있습니다.

예를 들어 다음 항목을 기준으로 사용자를 그룹에 할당할 수 있습니다.

* 특정 직위 또는 부서

* 사용자가 속한 파트너 조직(수동 또는 연결된 조직 사용)

* 사용자 유형(멤버 또는 게스트)

* 특정 프로젝트에의 참여(수동)

* 위치

동적 그룹은 사용자 또는 디바이스를 포함할 수 있지만 둘 다 포함할 수는 없습니다. 사용자 특성을 기준으로 쿼리를 추가하여 동적 그룹에 사용자를 할당할 수 있습니다. 아래 예제에서는 사용자가 멤버(게스트 아님)이고 재무 부서에 포함된 경우 사용자를 그룹에 추가하는 쿼리를 보여줍니다.

![동적 멤버 관리 규칙을 구성하는 스크린샷입니다.](media/secure-external-access/4-dynamic-membership-rules.png)

동적 그룹에 대한 자세한 내용은 [Azure Active Directory에서 동적 그룹 만들기 또는 업데이트](../enterprise-users/groups-create-rule.md)를 참조하세요.

### <a name="do-not-use-groups-for-multiple-purposes"></a>여러 용도로 그룹 사용 안 함

보안 또는 리소스 액세스 목적으로 그룹을 사용할 때 여기에 단일 기능을 지정하는 것이 중요합니다. 리소스에 대한 액세스 권한 부여를 위해 그룹이 사용된다면 이를 다른 목적으로 사용하지 않아야 합니다. 위치 또는 팀 멤버 자격 정의와 같은 일반적인 용도로 그룹이 사용될 경우에는 액세스 보안을 위한 용도로 사용하지 않아야 합니다. 

보안 그룹에 대해 해당 용도를 명확하게 표시할 수 있는 명명 규칙을 사용하는 것이 좋습니다. 예:

* *Secure_access_finance_apps*

* *Team_membership_finance_team*

* *Location_finance_building*



### <a name="types-of-groups"></a>그룹 유형

Azure AD 보안 그룹 및 Microsoft 365 그룹 모두 Azure AD 포털 또는 Microsoft 365 관리 포털에서 생성될 수 있습니다. 두 유형 모두 외부 액세스 보안을 위한 기초로 사용될 수 있습니다.

|고려 사항 | Azure AD 보안 그룹(수동 및 동적)| Microsoft 365 그룹 |
| - | - | - |
| 그룹에 포함 될 수 있는 항목은 무엇인가요?| 사용자<br>그룹<br>서비스 원칙<br>디바이스| 사용자만 |
| 그룹이 생성되는 위치는 어디인가요?| Azure AD 포털<br>Microsoft 365 포털(메일을 사용하도록 설정된 경우)<br>PowerShell<br>Microsoft Graph<br>최종 사용자 포털| Microsoft 365 포털<br>Azure AD 포털<br>PowerShell<br>Microsoft Graph<br>Microsoft 365 애플리케이션 |
| 기본적으로 생성되는 사람은 누구인가요?| 관리자 <br>최종 사용자| 관리자<br>최종 사용자 |
| 기본적으로 추가될 수 있는 사용자는 누구인가요?| 내부 사용자(멤버)| 모든 조직의 테넌트 멤버 및 게스트 |
| 액세스 권한을 부여하는 대상은 무엇인가요?| 할당된 리소스만| 모든 그룹 관련 리소스:<br>(그룹 사서함, 사이트, 팀, 채팅 및 기타 포함된 Microsoft 365 리소스)<br>그룹이 추가되는 다른 모든 리소스 |
| 함께 사용 가능한 항목| 조건부 액세스<br>권한 관리<br>그룹 라이선스| 조건부 액세스<br>권한 관리<br>민감도 레이블 |



Microsoft 365 그룹을 사용하여 팀 및 연관된 사이트와 콘텐츠와 같은 Microsoft 365 리소스 집합을 만들고 관리할 수 있습니다. 이러한 항목은 프로젝트 기반 작업에 적합합니다. 

 

## <a name="azure-ad-security-groups"></a>Azure AD 보안 그룹 

[Azure AD 보안 그룹](./active-directory-manage-groups.md)은 사용자 또는 디바이스를 포함할 수 있으며 액세스 관리에 사용될 수 있습니다. 

* Microsoft 365 앱과 같은 Azure 리소스, 사용자 지정 앱 및 Dropbox의 ServiceNow와 같은 SaaS(Software as a Service) 앱.

* Azure 데이터 및 구독.

* Azure 서비스.

다음을 위해 Azure AD 보안 그룹을 사용할 수도 있습니다.

* Microsoft 365, Dynamics 365와 Enterprise Mobility 및 Security와 같은 서비스의 라이선스를 할당합니다. 자세한 내용은 [그룹 기반 라이선스](./active-directory-licensing-whatis-azure-portal.md)를 참조하세요.

* 승격된 권한을 할당합니다. 자세한 내용은 [Azure AD 그룹을 사용하여 역할 할당 관리](../roles/groups-concept.md)를 참조하세요. 

[Azure Portal](./active-directory-groups-create-azure-portal.md)에서 그룹을 만들려면 Azure Active Directory로 이동한 후 그룹으로 이동합니다. [PowerShell cmdlet](../enterprise-users/groups-settings-v2-cmdlets.md)을 사용하여 Azure AD 보안 그룹을 만들 수도 있습니다. 

> [!NOTE]
> 보안 그룹을 사용하려면 최대 1500개까지만 애플리케이션을 할당할 수 있습니다. 

![보안 그룹을 만드는 스크린샷입니다.](media/secure-external-access/4-create-security-group.png)

> [!IMPORTANT]
> **메일 사용이 가능한 보안 그룹을 만들려면 [Microsoft 365 관리 센터](https://admin.microsoft.com/)로 이동합니다**. Azure AD 포털에서는 만들 수 없습니다. 
<br>만들 때 보안 그룹에 메일을 사용하도록 설정해야 합니다. 이후에는 이를 사용하도록 설정할 수 없습니다.

### <a name="hybrid-organizations-and-azure-ad-security-groups"></a>하이브리드 조직 및 Azure AD 보안 그룹

하이브리드 조직은 온-프레미스 인프라와 Azure AD 클라우드 인프라를 모두 포함할 수 있습니다. Active Directory를 사용하는 많은 하이브리드 조직이 온-프레미스에 해당 보안 그룹을 만들고 이를 클라우드에 동기화합니다. 이 방법을 사용하면 온-프레미스 환경의 사용자만 보안 그룹에 추가할 수 있습니다.

**온-프레미스 위반을 사용해서 Microsoft 365 테넌트에 대해 액세스 권한을 획득할 수 있으므로, 온-프레미스 인프라가 손상되지 않도록 보호하세요**. 자세한 내용은 [온-프레미스 공격으로부터 Microsoft 365 보호](./protect-m365-from-on-premises-attacks.md)를 참조하세요.

## <a name="microsoft-365-groups"></a>Microsoft 365 그룹

[Microsoft 365 그룹](/microsoft-365/admin/create-groups/office-365-groups)은 Microsoft 365에서 모든 액세스를 구동하는 기본적인 멤버 자격 서비스입니다. 이러한 그룹은 [Azure Portal](https://portal.azure.com/) 또는 [Microsoft 365 포털](https://admin.microsoft.com/)에서 생성될 수 있습니다. Microsoft 365 그룹을 만들 때 협업에 사용되는 리소스 그룹에 대한 액세스 권한을 부여합니다. 이러한 리소스의 전체 목록은 [관리자를 위한 Microsoft 365 그룹 개요](/microsoft-365/admin/create-groups/office-365-groups)를 참조하세요.

Microsoft 365 그룹은 해당 역할에 대해 다음 의미를 갖습니다.

* **소유자** - 그룹 소유자가 멤버를 추가하거나 제거할 수 있고 공유 사서함에서 대화를 삭제하거나 그룹 설정을 변경하는 기능 등의 고유한 권한을 갖습니다. 그룹 소유자는 그룹 이름을 바꾸거나, 설명 또는 사진을 업데이트할 수 있습니다.

* **멤버** - 멤버는 그룹의 모든 것에 액세스할 수 있지만 그룹 설정을 변경할 수 없습니다. 기본적으로 그룹 멤버는 사용자의 그룹에 참가하도록 게스트를 초대할 수 있지만, [이러한 설정은 소유자가 제어할 수 있습니다](/microsoft-365/admin/create-groups/manage-guest-access-in-groups).

* **게스트** - 그룹 게스트는 조직 외부에 있는 멤버입니다. 기본적으로 게스트는 Teams에서 기능이 일부 제한됩니다.

 

### <a name="microsoft-365-group-settings"></a>Microsoft 365 그룹 설정

이메일 별칭, 개인 정보, 설정 시 팀의 그룹을 사용할지 여부를 선택할 수 있습니다. 

![Microsoft 365 그룹 설정을 편집하는 스크린샷](media/secure-external-access/4-edit-group-settings.png)

설정 후에는 멤버를 추가하고 이메일 사용 등의 설정을 구성할 수 있습니다.

### <a name="next-steps"></a>다음 단계

리소스에 대한 외부 액세스 보안에 관해 다음 문서를 참조하세요. 이러한 작업은 나열된 순서에 따라 수행하는 것이 좋습니다.

1. [외부 액세스에 대해 원하는 보안 태세 결정](1-secure-access-posture.md)

2. [현재 상태 검색](2-secure-access-current-state.md)

3. [거버넌스 계획 만들기](3-secure-access-plan.md)

4. [보안을 위해 그룹 사용](4-secure-access-groups.md)(현재 상태)

5. [Azure AD B2B로 전환](5-secure-access-b2b.md)

6. [권한 관리를 사용한 보안 액세스](6-secure-access-entitlement-managment.md)

7. [조건부 액세스 정책을 사용한 보안 액세스](7-secure-access-conditional-access.md)

8. [민감도 레이블을 사용한 보안 액세스](8-secure-access-sensitivity-labels.md)

9. [Microsoft Teams, OneDrive 및 SharePoint에 대한 보안 액세스](9-secure-access-teams-sharepoint.md)