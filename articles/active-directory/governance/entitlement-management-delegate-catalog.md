---
title: Azure AD 권한 관리에서 카탈로그 작성자에게 액세스 거버넌스 위임 - Azure Active Directory
description: IT 관리자의 액세스 거버넌스를 카탈로그 작성자 및 프로젝트 관리자에게 위임하여 자체적으로 액세스를 관리할 수 있도록 하는 방법을 알아봅니다.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 07/6/2021
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5afa05b8a529d8a9e9fceeb4a113f0b743acfc05
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114286576"
---
# <a name="delegate-access-governance-to-catalog-creators-in-azure-ad-entitlement-management"></a>Azure AD 권한 관리에서 카탈로그 작성자에게 액세스 거버넌스 위임

카탈로그는 리소스 및 액세스 패키지의 컨테이너입니다. 관련 리소스 및 액세스 패키지를 그룹화하려는 경우 카탈로그를 생성합니다. 기본적으로 전역 관리자 또는 ID 거버넌스 관리자는 [카탈로그를 만들 수](entitlement-management-catalog-create.md) 있으며 사용자를 카탈로그 소유자로 추가할 수 있습니다.

관리자가 아닌 사용자에게 위임하여 해당 사용자가 자체 카탈로그를 만들 수 있도록 하려면 해당 사용자를 Azure AD 권한 관리에 정의된 카탈로그 작성자 역할에 추가합니다. 개별 사용자를 추가할 수도 있고 그룹을 추가하여 해당 구성원이 카탈로그를 만들 수 있게 할 수도 있습니다.  카탈로그를 만든 후에는 소유한 리소스를 이 카탈로그에 추가할 수 있습니다.

## <a name="as-an-it-administrator-delegate-to-a-catalog-creator"></a>IT 관리자가 카탈로그 작성자에게 위임

카탈로그 작성자 역할에 사용자를 할당하려면 다음 단계를 수행합니다.

**필수 역할:** 전역 관리자, Identity Governance 관리자 또는 사용자 관리자

1. Azure Portal에서 **Azure Active Directory** 를 클릭한 다음, **Identity Governance** 를 클릭합니다.

1. 왼쪽 메뉴의 **권한 관리** 섹션에서 **설정** 을 클릭합니다.

1. **편집** 을 클릭합니다.

    ![카탈로그 작성자를 추가하는 설정](./media/entitlement-management-delegate-catalog/settings-delegate.png)

1. **권한 관리 위임** 섹션에서 **카탈로그 작성자 추가** 를 클릭하여 이 권한 관리 역할을 위임할 사용자 또는 그룹을 선택합니다.

1. **선택** 을 클릭합니다.

1. **저장** 을 클릭합니다.

## <a name="allow-delegated-roles-to-access-the-azure-portal"></a>위임된 역할이 Azure Portal에 액세스할 수 있도록 허용

카탈로그 작성자 및 액세스 패키지 관리자와 같이 위임된 역할이 Azure Portal에 액세스하여 액세스 패키지를 관리할 수 있도록 하려면 관리 포털 설정을 확인해야 합니다.

**필수 역할:** 전역 관리자 또는 사용자 관리자

1. Azure Portal에서 **Azure Active Directory** 를 클릭하고 **사용자** 를 클릭합니다.

1. 왼쪽 메뉴에서 **사용자 설정** 을 클릭합니다.

1. **Azure AD 관리 포털에 대한 액세스 제한** 이 **예** 로 설정되었는지 확인합니다.

    ![Azure AD 사용자 설정 - 관리 포털](./media/entitlement-management-delegate-catalog/user-settings.png)

## <a name="manage-role-assignments-programmatically-preview"></a>프로그래매틱 방식으로 역할 할당 관리(미리 보기)

Microsoft Graph를 사용하여 카탈로그 작성자 및 자격 관리 카탈로그별 역할 할당을 보고 업데이트할 수도 있습니다.  위임된 `EntitlementManagement.ReadWrite.All` 권한이 있는 애플리케이션을 사용하는 적절한 역할의 사용자는 Graph API를 호출하여 자격 관리의 [역할 정의를 나열](/graph/api/rbacapplication-list-roledefinitions?view=graph-rest-beta&preserve-view=true)하고 해당 역할 정의에 대한 [역할 할당을 나열](/graph/api/rbacapplication-list-roleassignments?view=graph-rest-beta&preserve-view=true)할 수 있습니다.

카탈로그 작성자 역할, 정의 ID가 `ba92d953-d8e0-4e39-a797-0cbedb0a89e8`인 역할에 할당된 사용자 및 그룹 목록을 검색하려면 Graph 쿼리를 사용합니다.

```http
GET https://graph.microsoft.com/beta/roleManagement/entitlementManagement/roleAssignments?$filter=roleDefinitionId eq 'ba92d953-d8e0-4e39-a797-0cbedb0a89e8'&$expand=principal
```


## <a name="next-steps"></a>다음 단계

- [리소스 카탈로그 만들기 및 관리](entitlement-management-catalog-create.md)
- [액세스 패키지 관리자에게 액세스 거버넌스 위임](entitlement-management-delegate-managers.md)
- [리소스 소유자에게 액세스 거버넌스 위임](entitlement-management-delegate.md)

