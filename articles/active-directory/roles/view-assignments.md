---
title: Azure AD 역할 할당 나열
description: 이제 Azure Active Directory 관리 센터에서 Azure Active Directory 관리자 역할의 멤버를 보고 관리할 수 있습니다.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: how-to
ms.date: 09/07/2021
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 09159db68050c10331ac2f4fcea5388cc3eb82d3
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131057081"
---
# <a name="list-azure-ad-role-assignments"></a>Azure AD 역할 할당 나열

이 문서에서는 Azure AD(Azure Active Directory)에서 할당한 역할을 나열하는 방법을 설명합니다. Azure AD(Azure Active Directory)에서 역할은 조직 차원의 범위 또는 단일 애플리케이션 범위로 할당될 수 있습니다.

- 조직 차원의 범위에서 이루어진 역할 할당은 단일 애플리케이션 역할 할당 목록에 추가되므로 여기에서 확인할 수 있습니다.
- 단일 애플리케이션 범위에서 이루어진 역할 할당은 조직 차원의 범위에서 이루어진 할당 목록에 추가되지 않으므로 여기에서 확인할 수 없습니다.

## <a name="prerequisites"></a>필수 조건

- PowerShell 사용 시 AzureADPreview 모듈
- Microsoft Graph API용 Graph 탐색기 사용 시 관리자 동의

자세한 내용은 [PowerShell 또는 Graph 탐색기를 사용하기 위한 필수 구성 요소](prerequisites.md)를 참조하세요.

## <a name="azure-portal"></a>Azure portal

이 절차에서는 조직 차원의 범위에서 이루어진 역할 할당을 나열하는 방법을 설명합니다.

1. [Azure Portal](https://portal.azure.com) 또는 [Azure AD 관리 센터](https://aad.portal.azure.com)에 로그인합니다.

1. **Azure Active Directory** > **역할 및 관리자** 를 선택한 다음, 역할을 선택해 열고 해당 속성을 확인합니다.

1. **할당** 을 선택하여 역할 할당을 나열합니다.

    ![목록에서 역할을 열 때 역할 할당 및 권한 나열](./media/view-assignments/role-assignments.png)

### <a name="list-my-role-assignments"></a>내 역할 할당 나열

자신의 권한도 쉽게 나열할 수 있습니다. **역할 및 관리자** 페이지에서 **내 역할** 을 선택하면 현재 나에게 할당된 역할을 볼 수 있습니다.

### <a name="download-role-assignments"></a>역할 할당 다운로드

특정 역할에 대한 모든 할당을 다운로드하려면 **역할 및 관리자** 페이지에서 역할을 선택한 다음 **역할 할당 다운로드** 를 선택합니다. 해당 역할의 모든 범위에서 이루어진 할당을 나열하는 CSV 파일이 다운로드됩니다.

![역할에 대한 모든 할당 다운로드](./media/view-assignments/download-role-assignments.png)

### <a name="list-role-assignments-with-single-application-scope"></a>단일 애플리케이션 범위에서 이루어진 역할 할당 나열

이 섹션에서는 단일 애플리케이션 범위에서 이루어진 역할 할당을 나열하는 방법을 설명합니다. 이 기능은 현재 공개 미리 보기로 제공됩니다.

1. [Azure Portal](https://portal.azure.com) 또는 [Azure AD 관리 센터](https://aad.portal.azure.com)에 로그인합니다.

1. **Azure Active Directory** > **앱 등록** 을 선택한 다음, 해당 앱 등록을 선택하여 관련 속성을 확인합니다. Azure AD 조직에서 앱 등록의 전체 목록을 보려면 **모든 애플리케이션** 을 선택해야 할 수도 있습니다.

    ![앱 등록 페이지에서 앱 등록 만들기 또는 편집](./media/view-assignments/app-reg-all-apps.png)

1. 앱 등록에서 **역할 및 관리자** 를 선택한 다음 해당 속성을 볼 수 있는 역할을 선택합니다.

    ![앱 등록 페이지에서 앱 등록 역할 할당 나열](./media/view-assignments/app-reg-assignments.png)

1. **할당** 을 선택하여 역할 할당을 나열합니다. 앱 등록 내에서 할당 페이지를 열면 이 Azure AD 리소스로 범위가 지정된 역할 할당이 표시됩니다.

    ![앱 등록 속성에서 앱 등록 역할 할당 나열](./media/view-assignments/app-reg-assignments-2.png)


## <a name="powershell"></a>PowerShell

이 섹션에서는 조직 차원의 범위에서 이루어진 역할 할당을 보는 방법을 설명합니다. 이 문서에서는 [Azure Active Directory PowerShell 버전 2](/powershell/module/azuread/#directory_roles) 모듈을 사용합니다. PowerShell을 사용하여 단일 애플리케이션 범위에서 이루어진 할당을 보기 위해 [PowerShell을 사용하여 사용자 지정 역할 할당](custom-assign-powershell.md)에 나오는 cmdlet을 사용할 수 있습니다.

역할 할당 나열 예제입니다.

``` PowerShell
# Fetch list of all directory roles with template ID
Get-AzureADMSRoleDefinition

# Fetch a specific directory role by ID
$role = Get-AzureADMSRoleDefinition -Id "5b3fe201-fa8b-4144-b6f1-875829ff7543"

# Fetch membership for a role
Get-AzureADMSRoleAssignment -Filter "roleDefinitionId eq '$($role.Id)'"
```

## <a name="microsoft-graph-api"></a>Microsoft Graph API

이 섹션에서는 조직 차원의 범위에서 이루어진 역할 할당을 나열하는 방법을 설명합니다.  Graph API를 사용하여 단일 애플리케이션 범위에서 이루어진 역할 할당을 나열하기 위해 [Graph API를 사용하여 사용자 지정 역할 할당](custom-assign-graph.md)에 나오는 작업을 사용할 수 있습니다.

지정된 역할 정의에 대한 역할 할당을 가져오기 위한 HTTP 요청입니다.

GET

``` HTTP
https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments&$filter=roleDefinitionId eq ‘<template-id-of-role-definition>’
```

응답

``` HTTP
HTTP/1.1 200 OK
{
    "id":"CtRxNqwabEKgwaOCHr2CGJIiSDKQoTVJrLE9etXyrY0-1",
    "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
    "roleDefinitionId":"3671d40a-1aac-426c-a0c1-a3821ebd8218",
    "directoryScopeId":"/"
}
```

## <a name="next-steps"></a>다음 단계

* 언제든지 [Azure AD 관리 역할 포럼](https://feedback.azure.com/d365community/forum/22920db1-ad25-ec11-b6e6-000d3a4f0789)에서 경험을 공유하세요.
* 역할 권한에 대한 자세한 내용은 [Azure AD 기본 제공 역할](permissions-reference.md)을 참조하세요.
* 기본 사용자 권한의 경우 [기본 게스트 및 멤버 사용자 권한 비교](../fundamentals/users-default-permissions.md)를 참조하세요.
