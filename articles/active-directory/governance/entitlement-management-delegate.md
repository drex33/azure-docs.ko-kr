---
title: 권한 관리의 위임 및 역할 - Azure AD
description: IT 관리자의 액세스 거버넌스를 부서 관리자 및 프로젝트 관리자에게 위임하여 자체적으로 액세스를 관리할 수 있도록 하는 방법을 알아봅니다.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 7/6/2021
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: af0269d2ae698a025682929818b601cdebec06ad
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114286521"
---
# <a name="delegation-and-roles-in-azure-ad-entitlement-management"></a>Azure AD 권한 관리의 위임 및 역할

기본적으로 전역 관리자 및 ID 거버넌스 관리자는 Azure AD 권한 관리의 모든 측면을 만들고 관리할 수 있습니다. 그러나 이러한 역할의 사용자는 액세스 패키지가 필요한 모든 상황을 모를 수 있습니다. 일반적으로 각 부서, 팀 또는 프로젝트 내의 사용자가 누구와 협력하고, 어떤 리소스를 사용하고, 얼마나 오래 협력하는지 알고 있습니다. 관리자가 아닌 사용자에게 무제한 권한을 부여하는 대신 사용자에게 작업을 수행하는 데 필요한 최소 권한을 부여하고 충돌하거나 부적절한 액세스 권한을 만들지 않도록 할 수 있습니다.

이 비디오는 IT 관리자가 관리자가 아닌 사용자에게 액세스 거버넌스를 위임하는 방법에 대한 개요를 제공합니다.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE3Lq00]

## <a name="delegate-example"></a>대리자 예제

권한 관리에서 액세스 거버넌스를 위임하는 방법을 이해하기 위해 예제를 사용하는 것이 좋습니다. 조직에 다음과 같은 관리자(administrator) 및 관리자(manager)가 있다고 가정합니다.

![IT 관리자에서 관리자로 위임](./media/entitlement-management-delegate/delegate-admin-dept-managers.png)

IT 관리자의 경우 Hana에는 각 부서(해당 부서의 리소스 및 중요 비즈니스용 콘텐츠를 담당하는 마케팅의 Mamta, 재무의 Mark 및 법무의 Joe)의 연락처가 있습니다.

권한 관리를 통해 이러한 비관리자에게 액세스 거버넌스를 위임할 수 있습니다. 이러한 비관리자는 액세스 권한이 필요한 사용자, 기간 및 리소스를 알 수 있기 때문입니다. 비관리자에게 위임하면 적절한 사용자가 해당 부서에 대한 액세스를 관리할 수 있습니다.

Hana가 마케팅, 재무 및 법무 부서에 액세스 거버넌스를 위임할 수 있는 한 가지 방법은 다음과 같습니다.

1. Hana는 새 Azure AD 보안 그룹을 만들고 그룹의 멤버로 Mamta, Mark 및 Joe를 추가합니다.

1. Hana는 해당 그룹을 카탈로그 작성자 역할에 추가합니다.

    이제 Mamta, Mark 및 Joe가 해당 부서의 카탈로그를 만들고, 해당 부서에 필요한 리소스를 추가하고, 카탈로그 내에서 추가 위임 작업을 수행할 수 있습니다. 서로의 카탈로그는 볼 수 없습니다.

1. Mamta는 리소스의 컨테이너인 **마케팅** 카탈로그를 만듭니다.

1. Mamta는 마케팅 부서가 소유하고 있는 리소스를 이 카탈로그에 추가합니다.

1. Mamta는 자신의 부서에서 다른 사람을 이 카탈로그에 대한 카탈로그 소유자로 추가하여 카탈로그 관리 책임을 공유할 수 있습니다.

1. Mamta는 마케팅 부서의 프로젝트 관리자에게 마케팅 카탈로그의 액세스 패키지 만들기 및 관리를 추가로 위임할 수 있습니다. 액세스 패키지 관리자 역할에 할당하여 이 작업을 수행할 수 있습니다. 액세스 패키지 관리자는 액세스 패키지를 만들고 관리할 수 있습니다. 

다음 다이어그램은 마케팅, 재무 및 법무 부서를 위한 리소스가 포함된 카탈로그를 보여 줍니다. 프로젝트 관리자는 이러한 카탈로그를 사용하여 팀 또는 프로젝트에 대한 액세스 패키지를 만들 수 있습니다.

![권한 관리 위임 예제](./media/entitlement-management-delegate/elm-delegate.png)

위임 후 마케팅 부서에는 다음 표와 비슷한 역할이 있을 수 있습니다.

| 사용자 | 직무 | Azure AD 역할 | 권한 관리 역할 |
| --- | --- | --- | --- |
| Hana | IT 관리자 | 전역 관리자 또는 ID 거버넌스 관리자  |  |
| Mamta | 마케팅 관리자 | 사용자 | 카탈로그 작성자 및 카탈로그 소유자 |
| Bob | 마케팅 잠재 고객 | 사용자 | 카탈로그 소유자 |
| Jessica | 마케팅 프로젝트 관리자 | 사용자 | 액세스 패키지 관리자 |

## <a name="entitlement-management-roles"></a>권한 관리 역할

권한 관리에는 권한 관리와 관련된 다음과 같은 역할이 있습니다.

| 권한 관리 역할 | 역할 정의 ID | 설명 |
| --- | --- | -- |
| 카탈로그 작성자 | `ba92d953-d8e0-4e39-a797-0cbedb0a89e8` | 카탈로그를 만들고 관리합니다. 일반적으로 전역 관리자가 아닌 IT 관리자 또는 리소스 컬렉션에 대한 리소스 소유자입니다. 카탈로그를 만드는 사람은 자동으로 카탈로그의 첫 번째 카탈로그 소유자가 되며 카탈로그 소유자를 더 추가할 수 있습니다. 카탈로그 작성자는 소유하지 않은 카탈로그를 관리하거나 볼 수 없으며 소유하지 않은 리소스를 카탈로그에 추가할 수 없습니다. 카탈로그 작성자가 다른 카탈로그를 관리하거나 소유하지 않은 리소스를 추가해야 하는 경우 해당 카탈로그 또는 리소스의 공동 소유자가 되도록 요청할 수 있습니다. |
| 카탈로그 소유자 | `ae79f266-94d4-4dab-b730-feca7e132178` | 기존 카탈로그를 편집하고 관리합니다. 일반적으로 IT 관리자 또는 리소스 소유자이거나 카탈로그 소유자가 선택한 사용자입니다. |
| 카탈로그 읽기 권한자 | `44272f93-9762-48e8-af59-1b5351b1d6b3` | 카탈로그 내의 기존 액세스 패키지를 봅니다. |
| 액세스 패키지 관리자 | `7f480852-ebdc-47d4-87de-0d8498384a83` | 카탈로그 내 모든 기존 액세스 패키지를 편집하고 관리합니다. |
| 액세스 패키지 할당 관리자 | `e2182095-804a-4656-ae11-64734e9b7ae5` | 모든 기존 액세스 패키지의 할당을 편집하고 관리합니다. |

또한 선택된 승인자와 액세스 패키지의 요청자는 역할이 아니라 권한이 있습니다.

| 오른쪽 | 설명 |
| --- | --- |
| 승인자 | 액세스 패키지에 대한 요청을 승인하거나 거부하기 위해 정책에서 권한을 부여합니다. 단, 액세스 패키지 정의는 변경할 수 없습니다. |
| Requestor | 액세스 패키지를 요청하는 액세스 패키지의 정책에 의해 권한이 부여됩니다. |

다음 표에는 권한 관리 역할이 수행할 수 있는 작업이 나열되어 있습니다.

| Task | Admin | 카탈로그 작성자 | 카탈로그 소유자 | 액세스 패키지 관리자 | 액세스 패키지 할당 관리자 |
| --- | :---: | :---: | :---: | :---: | :---: |
| [카탈로그 작성자에게 위임](entitlement-management-delegate-catalog.md) | :heavy_check_mark: |  |  |  |  |
| [연결된 조직 추가](entitlement-management-organization.md) | :heavy_check_mark: |  |  |  |  |
| [새 카탈로그 만들기](entitlement-management-catalog-create.md) | :heavy_check_mark: | :heavy_check_mark: |  |  |  |
| [카탈로그에 리소스 추가](entitlement-management-catalog-create.md#add-resources-to-a-catalog) | :heavy_check_mark: |  | :heavy_check_mark: |  |  |
| [카탈로그 소유자 추가](entitlement-management-catalog-create.md#add-additional-catalog-owners) | :heavy_check_mark: |  | :heavy_check_mark: |  |  |
| [카탈로그 편집](entitlement-management-catalog-create.md#edit-a-catalog) | :heavy_check_mark: |  | :heavy_check_mark: |  |  |
| [카탈로그 삭제](entitlement-management-catalog-create.md#delete-a-catalog) | :heavy_check_mark: |  | :heavy_check_mark: |  |  |
| [액세스 패키지 관리자에 위임](entitlement-management-delegate-managers.md) | :heavy_check_mark: |  | :heavy_check_mark: |  |  |
| [액세스 패키지 관리자 제거](entitlement-management-delegate-managers.md#remove-an-access-package-manager) | :heavy_check_mark: |  | :heavy_check_mark: |  |  |
| [카탈로그에 새 액세스 패키지 만들기](entitlement-management-access-package-create.md) | :heavy_check_mark: |  | :heavy_check_mark:  | :heavy_check_mark:  |  |
| [액세스 패키지의 리소스 역할 변경](entitlement-management-access-package-resources.md) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [정책 만들기 및 편집](entitlement-management-access-package-request-policy.md) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [사용자를 액세스 패키지에 직접 할당](entitlement-management-access-package-assignments.md#directly-assign-a-user) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  :heavy_check_mark: |
| [액세스 패키지에서 사용자 직접 제거](entitlement-management-access-package-assignments.md#remove-an-assignment) | :heavy_check_mark:  |  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [액세스 패키지에 할당된 사용자 보기](entitlement-management-access-package-assignments.md#view-who-has-an-assignment) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  :heavy_check_mark: |
| [액세스 패키지의 요청 보기](entitlement-management-access-package-requests.md#view-requests) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:  |
| [요청의 배달 오류 보기](entitlement-management-troubleshoot.md#view-a-requests-delivery-errors) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  :heavy_check_mark: |
| [요청 재처리](entitlement-management-troubleshoot.md#reprocess-a-request) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:  |
| [보류 중인 요청 취소](entitlement-management-troubleshoot.md#cancel-a-pending-request) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [액세스 패키지 숨기기](entitlement-management-access-package-edit.md#change-the-hidden-setting) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [액세스 패키지 삭제](entitlement-management-access-package-edit.md#delete-an-access-package) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  |

## <a name="required-roles-to-add-resources-to-a-catalog"></a>카탈로그에 리소스를 추가하는 데 필요한 역할

전역 관리자는 카탈로그에서 그룹(클라우드 생성 보안 그룹 또는 클라우드 생성 Microsoft 365 그룹), 애플리케이션 또는 SharePoint Online 사이트를 추가하거나 제거할 수 있습니다. 사용자 관리자는 디렉터리 역할에 할당할 수 있도록 구성된 그룹을 제외하고 카탈로그에서 그룹 또는 애플리케이션을 추가하거나 제거할 수 있습니다.  역할 할당 가능 그룹에 대한 자세한 내용은 [Azure Active Directory에서 역할 할당 가능 그룹 만들기](../roles/groups-create-eligible.md)를 참조하세요.

> [!NOTE]
> 사용자 관리자 역할이 할당된 사용자는 더 이상 카탈로그를 만들거나 소유하지 않은 카탈로그에서 액세스 패키지를 관리할 수 없습니다. 조직의 사용자에게 자격 관리에서 카탈로그, 액세스 패키지 또는 정책을 구성할 수 있는 사용자 관리자 역할이 할당된 경우 대신 이러한 사용자에게 **Identity Governance 관리자** 역할을 할당해야 합니다.

전역 관리자가 아닌 사용자의 경우 카탈로그에 그룹, 애플리케이션 또는 SharePoint Online 사이트를 추가하려면 해당 사용자에게 필요한 Azure AD 디렉터리 역할 또는 리소스 소유권과 카탈로그에 대한 카탈로그 소유자 권한 관리 역할이 *모두* 있어야 합니다. 다음 표에는 카탈로그에 리소스를 추가하는 데 필요한 역할 조합이 나열되어 있습니다. 카탈로그에서 리소스를 제거하려면 동일한 역할이 있어야 합니다.

| Azure AD 디렉터리 역할 | 권한 관리 역할 | 보안 그룹 추가 가능 | Microsoft 365 그룹 추가 가능 | 앱 추가 가능 | SharePoint Online 사이트 추가 가능 |
| --- | :---: | :---: | :---: | :---: | :---: |
| [전역 관리자](../roles/permissions-reference.md) | 해당 없음 |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [사용자 관리자](../roles/permissions-reference.md) | 해당 없음 |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Intune 관리자](../roles/permissions-reference.md) | 카탈로그 소유자 | :heavy_check_mark: | :heavy_check_mark: |  |  |
| [Exchange 관리자](../roles/permissions-reference.md) | 카탈로그 소유자 |  | :heavy_check_mark: |  |  |
| [Teams 서비스 관리자](../roles/permissions-reference.md) | 카탈로그 소유자 |  | :heavy_check_mark: |  |  |
| [SharePoint 관리자](../roles/permissions-reference.md) | 카탈로그 소유자 |  | :heavy_check_mark: |  | :heavy_check_mark: |
| [애플리케이션 관리자](../roles/permissions-reference.md) | 카탈로그 소유자 |  |  | :heavy_check_mark: |  |
| [클라우드 애플리케이션 관리자](../roles/permissions-reference.md) | 카탈로그 소유자 |  |  | :heavy_check_mark: |  |
| 사용자 | 카탈로그 소유자 | 그룹 소유자인 경우에만 해당 | 그룹 소유자인 경우에만 해당 | 앱 소유자인 경우에만 해당 |  |

작업에 대한 최소 권한 있는 역할을 확인하려면 [Azure Active Directory에서 관리 작업별 관리자 역할](../roles/delegate-by-task.md#entitlement-management)을 참조할 수도 있습니다.

## <a name="manage-role-assignments-programmatically-preview"></a>프로그래매틱 방식으로 역할 할당 관리(미리 보기)

Microsoft Graph를 사용하여 카탈로그 작성자 및 자격 관리 카탈로그별 역할 할당을 보고 업데이트할 수도 있습니다.  위임된 `EntitlementManagement.ReadWrite.All` 권한이 있는 애플리케이션을 사용하는 적절한 역할의 사용자는 Graph API를 호출하여 자격 관리의 [역할 정의를 나열](/graph/api/rbacapplication-list-roledefinitions?view=graph-rest-beta&preserve-view=true)하고 해당 역할 정의에 대한 [역할 할당을 나열](/graph/api/rbacapplication-list-roleassignments?view=graph-rest-beta&preserve-view=true)할 수 있습니다.

예를 들어, 특정 사용자 또는 그룹에 할당된 권한 관리별 역할을 보려면 Graph 쿼리를 사용하여 역할 할당을 나열하고 다음과 같이 사용자 또는 그룹의 ID를 `principalId` 쿼리 필터의 값으로 제공합니다.

```http
GET https://graph.microsoft.com/beta/roleManagement/entitlementManagement/roleAssignments?$filter=principalId eq '10850a21-5283-41a6-9df3-3d90051dd111'&$expand=roleDefinition&$select=id,appScopeId,roleDefinition
```

카탈로그와 관련된 역할의 경우 응답의 `appScopeId`는 사용자에게 역할이 할당된 카탈로그를 나타냅니다.  이 응답은 권한 관리에서 역할에 대한 해당 주체의 명시적 할당만 검색하며 디렉터리 역할을 통해 또는 역할에 할당된 그룹의 구성원 자격을 통해 액세스 권한이 있는 사용자에 대한 결과는 반환하지 않습니다.


## <a name="next-steps"></a>다음 단계

- [액세스 거버넌스를 카탈로그 작성자에게 위임](entitlement-management-delegate-catalog.md)
- [액세스 패키지 관리자에 액세스 거버넌스 위임](entitlement-management-delegate-managers.md)
- [리소스 카탈로그 만들기 및 관리](entitlement-management-catalog-create.md)
