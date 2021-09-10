---
title: Azure AD 역할 정의 나열 - Azure AD
description: Azure 기본 제공 및 사용자 지정 역할을 나열하는 방법을 알아봅니다.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: how-to
ms.date: 07/23/2021
ms.author: rolyon
ms.reviewer: absinh
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3c5131cb56ff65b6c559186cf491c4367ecbc7d5
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122529297"
---
# <a name="list-azure-ad-role-definitions"></a>Azure AD 역할 정의 나열

역할 정의는 읽기, 쓰기 및 삭제와 같이 수행할 수 있는 작업 집합입니다. 일반적으로 단지 역할이라고 합니다. Azure Active Directory에는 60개 이상의 기본 제공 역할이 있거나 고유한 사용자 지정 역할을 만들 수 있습니다. "이 역할이 실제로 무엇을 수행할까?"가 궁금하다면 각 역할에 대한 자세한 권한 목록을 볼 수 있습니다.

이 문서에서는 권한과 함께 Azure AD 기본 제공 역할 및 사용자 지정 역할을 나열하는 방법을 설명합니다.

## <a name="prerequisites"></a>필수 구성 요소

- PowerShell 사용 시 AzureADPreview 모듈
- Microsoft Graph API용 Graph 탐색기 사용 시 관리자 동의

자세한 내용은 [PowerShell 또는 Graph 탐색기를 사용하기 위한 필수 구성 요소](prerequisites.md)를 참조하세요.

## <a name="azure-portal"></a>Azure portal

1. [Azure Portal](https://portal.azure.com) 또는 [Azure AD 관리 센터](https://aad.portal.azure.com)에 로그인합니다.

1. **Azure Active Directory** > **역할 및 관리자** 를 선택하여 사용 가능한 역할 목록을 봅니다.

    ![Azure Portal의 역할 목록](./media/role-definitions-list/view-roles-in-azure-active-directory.png)

1. 오른쪽에서 줄임표를 선택한 후 **설명** 을 선택하여 역할에 대한 전체 권한 목록을 확인합니다.

    이 페이지에는 디렉터리 역할을 관리하는 데 도움이 되는 관련 설명서의 링크가 포함되어 있습니다.

    !["전역 관리자 - 설명" 페이지를 보여주는 스크린샷](./media/role-definitions-list/role-description-updated.png)

## <a name="powershell"></a>PowerShell

PowerShell을 사용하여 Azure AD 역할을 나열하려면 다음 단계를 수행합니다.

1. PowerShell 창을 열고 [Import-Module](/powershell/module/microsoft.powershell.core/import-module)을 사용하여 AzureADPreview 모듈을 가져옵니다. 자세한 내용은 [PowerShell 또는 Graph Explorer를 사용하기 위한 필수 구성 요소](prerequisites.md)를 참조하세요.

    ```powershell
    Import-Module -Name AzureADPreview -Force
    ```

2. PowerShell 창에서 [Connect-AzureAD](/powershell/module/azuread/connect-azuread)를 사용하여 테넌트에 로그인합니다.

    ```powershell
    Connect-AzureAD
    ```
3. [Get-AzureADMSRoleDefinition](/powershell/module/azuread/get-azureadmsroledefinition)을 사용하여 모든 역할을 가져옵니다.

    ```powershell
    Get-AzureADMSRoleDefinition
    ```

4. 역할의 사용 권한 목록을 보려면 다음 cmdlet을 사용합니다.
    
    ```powershell
    # Do this avoid truncation of the list of permissions
    $FormatEnumerationLimit = -1
    
    (Get-AzureADMSRoleDefinition -Filter "displayName eq 'Conditional Access Administrator'").RolePermissions | Format-list
    ```

## <a name="microsoft-graph-api"></a>Microsoft Graph API

다음 지침에 따라 [Graph Explorer](https://aka.ms/ge)에서 Microsoft Graph API를 사용하여 Azure AD 역할을 할당합니다.

1. [Graph Explorer](https://aka.ms/ge)에 로그인합니다.
2. 드롭다운에서 HTTP 메서드로 **GET** 를 선택합니다. 
3. API 버전을 **베타** 로 선택합니다.
4. [List roleDefinitions](/graph/api/rbacapplication-list-roledefinitions) API를 사용하려면 다음 쿼리를 추가합니다.

   ```HTTP
   GET https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitions
   ```

5. **쿼리 실행** 을 선택하여 역할을 나열합니다.
6. 역할의 권한을 보려면 다음 API를 사용합니다.

   ```HTTP
   GET https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitions?$filter=DisplayName eq 'Conditional Access Administrator'&$select=rolePermissions
   ```

## <a name="next-steps"></a>다음 단계

* [Azure AD 역할 할당을 나열](view-assignments.md)합니다.
* [사용자에게 Azure AD 역할을 할당](manage-roles-portal.md)합니다.
* [Azure AD 기본 제공 역할](permissions-reference.md).
