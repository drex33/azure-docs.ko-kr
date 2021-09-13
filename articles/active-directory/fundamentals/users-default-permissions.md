---
title: 기본 사용자 권한 - Azure Active Directory | Microsoft Docs
description: Azure Active Directory에서 사용할 수 있는 다른 사용자 권한에 대해 알아봅니다.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 08/04/2021
ms.author: ajburnle
ms.reviewer: vincesm
ms.custom: it-pro, seodec18, contperf-fy21q1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 45d03495d0866674352de35c337303a92ff3263b
ms.sourcegitcommit: d858083348844b7cf854b1a0f01e3a2583809649
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122835190"
---
# <a name="what-are-the-default-user-permissions-in-azure-active-directory"></a>Azure Active Directory의 기본 사용자 권한이란?
Azure AD(Azure Active Directory)의 모든 사용자에게는 기본 권한 집합이 부여됩니다. 사용자 액세스는 사용자 유형, [역할 할당](active-directory-users-assign-role-azure-portal.md) 및 개별 개체의 소유권으로 구성됩니다. 이 문서에서는 이 기본 권한을 설명하며 멤버와 게스트 사용자 기본값을 비교합니다. 기본 사용자 권한은 Azure AD의 사용자 설정에서만 변경할 수 있습니다.

## <a name="member-and-guest-users"></a>멤버 및 게스트 사용자
제공되는 기본 권한 세트는 사용자가 테넌트의 기본 멤버(멤버 사용자)인지 아니면 B2B 협업 게스트(게스트 사용자)로 다른 디렉터리에서 전달되었는지에 따라 달라집니다. 게스트 사용자 추가에 대한 자세한 내용은 [Azure AD B2B 협업이란?](../external-identities/what-is-b2b.md)을 참조하세요.
* 멤버 사용자는 애플리케이션을 등록하고, 자신의 프로필 사진과 휴대전화 번호를 관리하며, 자신의 암호를 변경하고, B2B 게스트를 초대할 수 있습니다. 또한 몇 가지 예외가 있지만 모든 디렉터리 정보를 읽을 수 있습니다. 
* 게스트 사용자에게는 제한된 디렉터리 권한이 있습니다. 자신의 프로필을 관리하고, 자신의 암호를 변경하고, 다른 사용자, 그룹, 앱에 대한 일부 정보를 검색할 수 있습니다. 그러나 디렉터리 정보를 모두 읽을 수는 없습니다. 예를 들어 게스트 사용자는 모든 사용자, 그룹, 기타 디렉터리 개체의 목록을 열거할 수 없습니다. 게스트를 관리자 역할에 추가할 수 있고, 이 경우 해당 역할에 포함된 전체 읽기 쓰기 권한이 부여됩니다. 게스트가 다른 게스트를 초대할 수도 있습니다.

## <a name="compare-member-and-guest-default-permissions"></a>멤버 및 게스트 기본 권한 비교

**영역** | **멤버 사용자 권한** | **기본 게스트 사용자 권한** | **제한된 게스트 사용자 권한(미리 보기)**
------------ | --------- | ---------- | ----------
사용자 및 연락처 | <ul><li>모든 사용자 및 연락처 목록 열거<li>사용자 및 연락처의 모든 공용 속성 읽기</li><li>게스트 초대<li>자신의 암호 변경<li>휴대전화 번호 관리<li>자신의 사진 관리<li>자신의 새로 고침 토큰 무효화</li></ul> | <ul><li>자신의 속성 읽기<li>표시 이름, 이메일, 로그인 이름, 사진, 사용자 계정 이름 및 다른 사용자 및 연락처의 사용자 형식 속성 읽기<li>자신의 암호 변경<li>ObjectId별로 다른 사용자 검색(허용된 경우)<li>다른 사용자의 관리자 및 직접 보고서 정보 읽기</li></ul> | <ul><li>자신의 속성 읽기<li>자신의 암호 변경</li><li>휴대전화 번호 관리</li></ul>
그룹 | <ul><li>보안 그룹 만들기<li>Microsoft 365 그룹 만들기<li>모든 그룹 목록 열거<li>모든 그룹 속성 읽기<li>숨겨지지 않은 그룹 멤버 자격 읽기<li>조인된 그룹의 숨겨진 Microsoft 365의 그룹 멤버 자격 읽기<li>사용자가 소유한 그룹의 속성, 소유권 및 멤버 자격 관리<li>게스트를 소유 그룹에 추가<li>동적 멤버 자격 설정 관리<li>소유 그룹 삭제<li>소유한 Microsoft 365 그룹 복원</li></ul> | <ul><li>멤버 자격 및 소유권(조인되지 않은 그룹도 포함)을 포함하여 숨겨지지 않은 그룹의 속성을 읽기<li>조인된 그룹의 숨겨진 Microsoft 365 그룹 멤버 자격 읽기<li>표시 이름 또는 ObjectId를 기준으로 그룹 검색(허용되는 경우)</li></ul> | <ul><li>조인된 그룹의 개체 ID 읽기<li>일부 Microsoft 365 앱에서 조인된 그룹의 멤버 자격 및 소유권 읽기(허용된 경우)</li></ul>
애플리케이션 | <ul><li>새 애플리케이션 등록(만들기)<li>모든 애플리케이션 목록 열거<li>등록 및 엔터프라이즈 애플리케이션의 속성 읽기<li>소유 애플리케이션의 애플리케이션 속성, 할당 및 자격 증명 관리<li>사용자에 대한 애플리케이션 암호 만들기 또는 삭제<li>소유 애플리케이션 삭제<li>소유 애플리케이션 복원</li></ul> | <ul><li>등록 및 엔터프라이즈 애플리케이션의 속성 읽기</li></ul> | <ul><li>등록 및 엔터프라이즈 애플리케이션의 속성 읽기
디바이스</li></ul> | <ul><li>모든 디바이스 목록 열거<li>모든 디바이스 속성 읽기<li>소유한 디바이스의 모든 속성 읽기</li></ul> | 사용 권한 없음 | 사용 권한 없음
디렉터리 | <ul><li>모든 회사 정보 읽기<li>모든 도메인 읽기<li>모든 파트너 계약 읽기</li></ul> | <ul><li>회사 표시 이름 읽기<li>모든 도메인 읽기</li></ul> | <ul><li>회사 표시 이름 읽기<li>모든 도메인 읽기</li></ul>
역할 및 범위 | <ul><li>모든 관리 역할 및 멤버 자격 읽기<li>관리 단위의 모든 속성 및 멤버 자격 읽기</li></ul> | 사용 권한 없음 | 사용 권한 없음
Subscriptions | <ul><li>모든 구독 읽기<li>서비스 계획 멤버를 사용하도록 설정</li></ul> | 사용 권한 없음 | 사용 권한 없음
정책 | <ul><li>모든 정책 속성 읽기<li>소유한 정책의 모든 속성 읽기</li></ul> | 사용 권한 없음 | 사용 권한 없음

## <a name="restrict-member-users-default-permissions"></a>멤버 사용자 기본 권한 제한 

사용자의 기본 권한에 제한 사항을 추가할 수 있습니다. 일부 조직에서는 포털에 대한 사용자 액세스를 제한해야 할 수 있습니다. 이 기능은 디렉터리의 일부 사용자가 Azure AD 관리 포털/디렉터리에 액세스할 수 없게 하려는 경우에 사용됩니다. 

예를 들어, 한 대학교의 디렉터리 내에 많은 사용자가 있고 관리자는 디렉터리의 일부 학생이 전체 디렉터리를 보고 다른 학생의 프라이버시를 위반하는 경우를 원하지 않을 수 있습니다. 이 기능 사용은 선택 사항이며 Azure AD 관리자의 재량에 따릅니다. 멤버 사용자의 기본 권한은 다음 방식으로 제한할 수 있습니다.

사용 권한 | 설정 설명
---------- | ------------
사용자가 애플리케이션을 등록할 수 있음 | 이 옵션을 아니요로 설정하면 사용자가 애플리케이션을 등록할 수 없습니다. 그런 경우 특정 개인을 애플리케이션 개발자 역할에 추가하여 이 기능을 다시 부여할 수 있습니다.
사용자가 LinkedIn으로 회사 또는 학교 계정에 연결하도록 허용 | 이 옵션을 ‘아니요’로 설정하면 사용자가 자신의 회사 또는 학교 계정을 LinkedIn 계정에 연결할 수 없습니다. 자세한 내용은 [LinkedIn 계정 연결 데이터 공유 및 동의](../enterprise-users/linkedin-user-consent.md)를 참조하세요.
보안 그룹 만들기 기능 | 이 옵션을 아니요로 설정하면 사용자가 보안 그룹을 만들 수 없습니다. 전역 관리자 및 사용자 관리자는 계속 보안 그룹을 만들 수 있습니다. 자세한 내용은 [그룹 설정 구성을 위한 Azure Active Directory cmdlet](../enterprise-users/groups-settings-cmdlets.md)을 참조하세요.
Microsoft 365 그룹 만들기 기능 | 이 옵션을 ‘아니요’로 설정하면 사용자가 Microsoft 365 그룹을 만들 수 없습니다. 이 옵션을 ‘일부’로 설정하면 Microsoft 365 그룹을 만들 수 있는 사용자 집합을 선택할 수 있습니다. 전역 관리자 및 사용자 관리자는 계속 Microsoft 365 그룹을 만들 수 있습니다. 자세한 내용은 [그룹 설정 구성을 위한 Azure Active Directory cmdlet](../enterprise-users/groups-settings-cmdlets.md)을 참조하세요.
Azure AD 관리 포털에 대한 액세스 제한 | <p>이 옵션을 아니요로 설정하면 비관리자가 Azure AD 관리 포털을 사용하여 Azure AD 리소스를 읽고 관리할 수 있습니다. 예로 설정하면 모든 비관리자가 관리 포털의 Azure AD 데이터에 액세스하지 못하도록 제한합니다.</p><p>**참고**: 이 설정은 PowerShell 또는 Visual Studio와 같은 다른 클라이언트를 사용하여 Azure AD 데이터에 액세스하는 것을 제한하지 않습니다. ‘예’로 설정된 경우, 관리자가 아닌 특정 사용자에게 Azure AD 관리 포털 사용 권한을 부여하려면 디렉터리 읽기 권한자 역할과 같은 관리 역할을 할당합니다.</p><p>**참고**: 이 설정은 그룹 또는 애플리케이션의 소유자인 관리자가 아닌 사용자가 소유한 리소스를 관리하는 데 Azure Portal을 사용하지 못하도록 차단합니다.</p><p>이 역할을 통해 기본 디렉터리 정보를 읽을 수 있습니다. 이 정보는 멤버 사용자에게 기본적으로 제공됩니다(게스트 및 서비스 주체에게는 제공되지 않음).</p>
다른 사용자를 읽을 수 있는 기능 | 이 설정은 PowerShell에서만 사용할 수 있습니다. 이 플래그를 $false로 설정하면 관리자가 아닌 모든 사용자가 디렉터리에서 사용자 정보를 읽지 못하게 됩니다. 단, Exchange Online과 같은 다른 Microsoft 서비스에서 사용자 정보를 읽지 못하게 되지는 않습니다. 이 설정은 특수한 상황에서만 사용해야 하며, 이 플래그를 $false로 설정하는 것은 권장되지 않습니다.

>[!NOTE]
>평균 사용자는 Azure AD에 액세스하는 데 포털만 사용하고 리소스에 액세스하는 데 PowerShell 또는 CLI를 사용하지 않는다고 가정합니다. 현재는 사용자가 Azure Portal 내의 디렉터리에 액세스하려고 할 경우에만 사용자의 기본 권한에 대한 액세스를 제한합니다.

## <a name="restrict-guest-users-default-permissions"></a>게스트 사용자 기본 권한 제한

게스트 사용자의 기본 권한은 다음 방식으로 제한할 수 있습니다.

>[!NOTE]
>게스트 사용자 액세스 제한 설정이 **게스트 사용자 권한이 제한됨** 설정을 대체했습니다. 이 기능을 사용하는 방법에 대한 지침은 [Azure Active Directory의 게스트 사용자 액세스 제한(미리 보기)](../enterprise-users/users-restrict-guest-permissions.md)을 참조하세요.

사용 권한 | 설정 설명
---------- | ------------
게스트 사용자 액세스 제한(미리 보기) | 이 옵션을 **게스트 사용자가 멤버와 동일한 액세스를 보유합니다** 로 설정하면 기본적으로 모든 멤버 사용자 권한을 게스트 사용자에게 부여합니다.<p>이 옵션을 **게스트 사용자 액세스가 자신의 디렉터리 개체의 속성 및 구성원 자격으로 제한됩니다** 로 설정하면 기본적으로 게스트 액세스가 자신의 사용자 프로필로만 제한됩니다. 사용자 계정 이름, ObjectId 또는 표시 이름으로 검색하는 경우에도 다른 사용자에 대한 액세스는 더 이상 허용되지 않습니다. 그룹 멤버 자격을 비롯한 그룹 정보에 대한 액세스도 더 이상 허용되지 않습니다.<p>**참고**: 이 설정은 Microsoft Teams와 같은 일부 Microsoft 365 서비스에서 조인된 그룹에 대한 액세스를 차단하지 않습니다. 자세한 내용은 [Microsoft Teams 게스트 액세스](/MicrosoftTeams/guest-access)를 참조하세요.<p>이 권한 설정과 무관하게 게스트 사용자를 관리자 역할에 계속 추가할 수 있습니다.
게스트가 초대할 수 있음 | 이 옵션을 예로 설정하면 게스트가 다른 게스트를 초대할 수 있습니다. 자세한 내용은 [B2B 협업에 대한 초대 위임](../external-identities/delegate-invitations.md#configure-b2b-external-collaboration-settings)을 참조하세요.
구성원이 초대할 수 있음 | 이 옵션을 예로 설정하면 디렉터리의 관리자가 아닌 멤버가 게스트를 초대할 수 있습니다. 자세한 내용은 [B2B 협업에 대한 초대 위임](../external-identities/delegate-invitations.md#configure-b2b-external-collaboration-settings)을 참조하세요.
관리자 및 게스트 초대자 역할의 사용자가 초대할 수 있음 | 이 옵션을 예로 설정하면 관리자와 "게스트 초대자" 역할의 사용자가 게스트를 초대할 수 있습니다. 예로 설정된 경우, 게스트 초대자 역할의 사용자는 구성원이 초대할 수 있음 설정과 무관하게 게스트를 초대할 수 있습니다. 자세한 내용은 [B2B 협업에 대한 초대 위임](../external-identities/delegate-invitations.md#assign-the-guest-inviter-role-to-a-user)을 참조하세요.

## <a name="object-ownership"></a>개체 소유권

### <a name="application-registration-owner-permissions"></a>애플리케이션 등록 소유자 권한
사용자가 애플리케이션을 등록하면 해당 사용자가 자동으로 해당 애플리케이션의 소유자로 추가됩니다. 소유자는 앱이 요청하는 이름 및 권한 등, 애플리케이션의 메타데이터를 관리할 수 있습니다. 또한 SSO 구성 및 사용자 할당 등, 애플리케이션의 테넌트 특정 구성도 관리할 수 있습니다. 소유자는 다른 소유자를 추가하거나 제거할 수도 있습니다. 전역 관리자와 달리 소유자는 자신의 소유한 애플리케이션만 관리할 수 있습니다.

### <a name="enterprise-application-owner-permissions"></a>엔터프라이즈 애플리케이션 소유자 권한
사용자가 새 엔터프라이즈 애플리케이션을 추가하면 자동으로 소유자로 추가됩니다. 소유자로서 SSO 구성, 프로비전, 사용자 할당 등, 애플리케이션의 테넌트 특정 구성도 관리할 수 있습니다. 소유자는 다른 소유자를 추가하거나 제거할 수도 있습니다. 전역 관리자와는 달리 소유자는 자신이 소유한 애플리케이션만 관리할 수 있습니다.

### <a name="group-owner-permissions"></a>그룹 소유자 권한
사용자가 그룹을 만들면 자동으로 해당 그룹의 소유자로 추가됩니다. 소유자는 이름 등의 그룹 속성과 그룹 멤버 자격을 관리할 수 있습니다. 소유자는 다른 소유자를 추가하거나 제거할 수도 있습니다. 전역 관리자 및 사용자 관리자와 달리 소유자는 자신이 소유한 그룹만 관리할 수 있습니다. 그룹 소유자를 할당하려면 [그룹 소유자 관리](active-directory-accessmanagement-managing-group-owners.md)를 참조하세요.

### <a name="ownership-permissions"></a>소유권 권한
다음 표에서는 소유하고 있는 개체에 대한 Azure Active Directory 멤버 사용자의 특정 권한을 설명합니다. 사용자는 자신이 소유한 개체에 대해서만 이러한 권한을 보유합니다.

#### <a name="owned-application-registrations"></a>소유한 애플리케이션 등록
사용자는 소유한 애플리케이션에 대해 다음 작업을 수행할 수 있습니다.

| **actions** | **설명** |
| --- | --- |
| microsoft.directory/applications/audience/update | Azure Active Directory에서 applications.audience 속성을 업데이트합니다. |
| microsoft.directory/applications/authentication/update | Azure Active Directory에서 applications.authentication 속성을 업데이트합니다. |
| microsoft.directory/applications/basic/update | Azure Active Directory에서 애플리케이션의 기본 속성을 업데이트합니다. |
| microsoft.directory/applications/credentials/update | Azure Active Directory에서 applications.credentials 속성을 업데이트합니다. |
| microsoft.directory/applications/delete | Azure Active Directory에서 애플리케이션을 삭제합니다. |
| microsoft.directory/applications/owners/update | Azure Active Directory에서 applications.owners 속성을 업데이트합니다. |
| microsoft.directory/applications/permissions/update | Azure Active Directory에서 applications.permissions 속성을 업데이트합니다. |
| microsoft.directory/applications/policies/update | Azure Active Directory에서 applications.policies 속성을 업데이트합니다. |
| microsoft.directory/applications/restore | Azure Active Directory에서 애플리케이션을 복원합니다. |

#### <a name="owned-enterprise-applications"></a>소유한 엔터프라이즈 애플리케이션
사용자는 소유한 엔터프라이즈 애플리케이션에 대해 다음 작업을 수행할 수 있습니다. 엔터프라이즈 애플리케이션은 서비스 주체, 하나 이상의 애플리케이션 정책, 그리고 경우에 따라 서비스 주체로서 동일한 테넌트에 있는 애플리케이션 개체로 구성됩니다.

| **actions** | **설명** |
| --- | --- |
| microsoft.directory/auditLogs/allProperties/read | Azure Active Directory에서 auditLogs에 대한 모든 속성(권한 있는 속성 포함)을 읽습니다. |
| microsoft.directory/policies/basic/update | Azure Active Directory에서 정책의 기본 속성을 업데이트합니다. |
| microsoft.directory/policies/delete | Azure Active Directory에서 정책을 삭제합니다. |
| microsoft.directory/policies/owners/update | Azure Active Directory에서 policies.owners 속성을 업데이트합니다. |
| microsoft.directory/servicePrincipals/appRoleAssignedTo/update | Azure Active Directory에서 servicePrincipals.appRoleAssignedTo 속성을 업데이트합니다. |
| microsoft.directory/servicePrincipals/appRoleAssignments/update | Azure Active Directory에서 users.appRoleAssignments 속성을 업데이트합니다. |
| microsoft.directory/servicePrincipals/audience/update | Azure Active Directory에서 servicePrincipals.audience 속성을 업데이트합니다. |
| microsoft.directory/servicePrincipals/authentication/update | Azure Active Directory에서 servicePrincipals.authentication 속성을 업데이트합니다. |
| microsoft.directory/servicePrincipals/basic/update | Azure Active Directory에서 servicePrincipals의 기본 속성을 업데이트합니다. |
| microsoft.directory/servicePrincipals/credentials/update | Azure Active Directory에서 servicePrincipals.credentials 속성을 업데이트합니다. |
| microsoft.directory/servicePrincipals/delete | Azure Active Directory에서 servicePrincipals를 삭제합니다. |
| microsoft.directory/servicePrincipals/owners/update | Azure Active Directory에서 servicePrincipals.owners 속성을 업데이트합니다. |
| microsoft.directory/servicePrincipals/permissions/update | Azure Active Directory에서 servicePrincipals.permissions 속성을 업데이트합니다. |
| microsoft.directory/servicePrincipals/policies/update | Azure Active Directory에서 servicePrincipals.policies 속성을 업데이트합니다. |
| microsoft.directory/signInReports/allProperties/read | Azure Active Directory에서 signInReports에 대한 모든 속성(권한 있는 속성 포함)을 읽습니다. |

#### <a name="owned-devices"></a>소유 디바이스
사용자는 소유 디바이스에서 다음 작업을 수행할 수 있습니다.

| **actions** | **설명** |
| --- | --- |
| microsoft.directory/devices/bitLockerRecoveryKeys/read | Azure Active Directory에서 devices.bitLockerRecoveryKeys 속성을 읽습니다. |
| microsoft.directory/devices/disable | Azure Active Directory에서 디바이스를 사용하지 않도록 설정합니다. |

#### <a name="owned-groups"></a>소유 그룹
사용자는 소유 그룹에 대해 다음 작업을 수행할 수 있습니다.

> [!NOTE]
> 그룹 멤버 자격 규칙을 편집하려면 동적 그룹의 소유자에게 전역 관리자, 그룹 관리자, Intune 관리자 또는 사용자 관리자 역할이 있어야 합니다. 자세한 내용은 [Azure Active Directory에서 동적 그룹 만들기 또는 업데이트](../enterprise-users/groups-create-rule.md)를 참조하세요.

| **actions** | **설명** |
| --- | --- |
| microsoft.directory/groups/appRoleAssignments/update | Azure Active Directory에서 groups.appRoleAssignments 속성을 업데이트합니다. |
| microsoft.directory/groups/basic/update | Azure Active Directory에서 그룹의 기본 속성을 업데이트합니다. |
| microsoft.directory/groups/delete | Azure Active Directory에서 그룹을 삭제합니다. |
| microsoft.directory/groups/members/update | Azure Active Directory에서 groups.members 속성을 업데이트합니다. |
| microsoft.directory/groups/owners/update | Azure Active Directory에서 groups.members 속성을 업데이트합니다. |
| microsoft.directory/groups/restore | Azure Active Directory에서 그룹을 복원합니다. |
| microsoft.directory/groups/settings/update | Azure Active Directory에서 groups.settings 속성을 업데이트합니다. |

## <a name="next-steps"></a>다음 단계

* 게스트 사용자 액세스 제한 설정에 대한 자세한 내용은 [Azure Active Directory에서 게스트 액세스 권한 제한(미리 보기)](../enterprise-users/users-restrict-guest-permissions.md)을 참조하세요.
* Azure AD 관리자 역할을 할당하는 방법에 대한 자세한 내용은 [Azure Active Directory에서 관리자 역할에 사용자 할당](active-directory-users-assign-role-azure-portal.md) 참조
* Microsoft Azure에서 리소스 액세스를 제어하는 방법을 자세히 알아보려면 [Azure에서 리소스 액세스 이해](../../role-based-access-control/rbac-and-directory-admin-roles.md)
* Azure 구독에 Azure Active Directory가 연결되는 방법에 대한 자세한 내용은 [Azure 구독을 Azure Active Directory에 연결하는 방법](active-directory-how-subscriptions-associated-directory.md)
* [사용자 관리](add-users-azure-active-directory.md)
