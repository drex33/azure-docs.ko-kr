---
title: 사용자의 Azure AD 역할 할당 나열 - Azure Active Directory
description: 사용자의 Azure AD 역할 할당을 나열하는 방법 알아보기
services: active-directory
author: abhijeetsinha
manager: vincesm
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: how-to
ms.date: 08/12/2021
ms.author: absinh
ms.reviewer: rolyon
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 58647d37da29066c14457ae6624763c800e6f123
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124776421"
---
# <a name="list-azure-ad-role-assignments-for-a-user"></a>사용자의 Azure AD 역할 할당 나열

역할은 그룹을 통해 사용자에게 직접 또는 전이적으로 할당할 수 있습니다. 이 문서에서는 사용자에게 할당된 Azure AD 역할을 나열하는 방법을 설명합니다. 그룹에 역할을 할당하는 것에 관한 정보는 [Azure AD 그룹을 사용하여 역할 할당 관리](groups-concept.md)를 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

- PowerShell 사용 시 AzureADPreview 모듈
- PowerShell 사용 시 Microsoft.Graph 모듈
- Microsoft Graph API용 Graph Explorer 사용 시 관리자 동의

자세한 내용은 [PowerShell 또는 Graph 탐색기를 사용하기 위한 필수 구성 요소](prerequisites.md)를 참조하세요.

## <a name="azure-portal"></a>Azure portal
Azure Portal을 사용하여 사용자의 Azure AD 역할을 나열하려면 다음 단계를 수행합니다. [Azure AD PIM(Privileged Identity Management)](../privileged-identity-management/pim-configure.md)을 사용하도록 설정했는지 여부에 따라 환경이 달라집니다.

1. [Azure Portal](https://portal.azure.com) 또는 [Azure AD 관리 센터](https://aad.portal.azure.com)에 로그인합니다.

2. **Azure Active Directory** > **사용자** > *사용자 이름* > **할당된 역할** 을 선택합니다.

    다른 범위에서 사용자에게 할당된 역할의 목록을 볼 수 있습니다. 또한 역할이 직접 할당되었는지 또는 그룹을 통해 할당되었는지 확인할 수 있습니다.
    
    ![Azure Portal에서 사용자에게 할당된 역할의 목록](./media/list-role-assignments-users/list-role-definition.png)

    프리미엄 P2 라이선스가 있는 경우 적격, 활성 및 만료된 역할 할당 세부 정보가 있는 PIM 환경이 표시됩니다.

    ![PIM에서 사용자에게 할당된 역할의 목록](./media/list-role-assignments-users/list-role-definition-pim.png)

## <a name="powershell"></a>PowerShell

PowerShell을 사용하여 사용자에게 할당된 Azure AD 역할을 나열하려면 다음 단계를 수행합니다.

1. [Install-module](/powershell/azure/active-directory/install-adv2)을 사용하여 AzureADPreview 및 Microsoft.Graph 모듈을 설치합니다.
  
    ```powershell
    Install-module -name AzureADPreview
    Install-module -name Microsoft.Graph
    ```
  
2. PowerShell 창을 열고 [Import-Module](/powershell/module/microsoft.powershell.core/import-module)을 사용하여 AzureADPreview 모듈을 가져옵니다. 자세한 내용은 [PowerShell 또는 Graph Explorer를 사용하기 위한 필수 구성 요소](prerequisites.md)를 참조하세요.

    ```powershell
    Import-Module -Name AzureADPreview -Force
    ```

3. PowerShell 창에서 [Connect-AzureAD](/powershell/module/azuread/connect-azuread)를 사용하여 사용자의 테넌트에 로그인합니다.

    ```powershell
    Connect-AzureAD
    ```
4. [Get-AzureADMSRoleAssignment](/powershell/module/azuread/get-azureadmsroleassignment)를 사용하여 사용자에 직접 할당된 역할을 가져옵니다.

    ```powershell
    #Get the user
    $userId = (Get-AzureADUser -Filter "userPrincipalName eq 'alice@contoso.com'").ObjectId

    #Get direct role assignments to the user
    $directRoles = (Get-AzureADMSRoleAssignment -Filter "principalId eq '$userId'").RoleDefinitionId
    ```

5. 사용자에게 전이적으로 할당된 역할을 가져오려면 다음 cmdlet을 사용합니다.
  
    a. [Get-AzureADMSGroup](/powershell/module/azuread/get-azureadmsgroup)을 사용하여 역할 할당이 가능한 모든 그룹의 목록을 가져옵니다.  
  
      ```powershell
      $roleAssignableGroups = (Get-AzureADMsGroup -All $true | Where-Object IsAssignableToRole -EQ 'True').Id
      ```

    b. [Connect-MgGraph](/graph/powershell/get-started)를 사용하여 로그인하고 Microsoft Graph PowerShell cmdlet을 사용합니다.
  
      ```powershell
      Connect-MgGraph -Scopes "User.Read.All”
      ```
  
    다. [checkMemberObjects](/graph/api/user-checkmemberobjects) API를 사용하여 사용자가 구성원인 역할 할당 가능 그룹을 파악합니다. 
    
      ```powershell
      $uri = "https://graph.microsoft.com/beta/directoryObjects/$userId/microsoft.graph.checkMemberObjects"

      $userRoleAssignableGroups = (Invoke-MgGraphRequest -Method POST -Uri $uri -Body @{"ids"= $roleAssignableGroups}).value
      ```
  
    d. [Get-AzureADMSRoleAssignment](/powershell/module/azuread/get-azureadmsroleassignment)를 사용하여 그룹을 루프하고 할당된 역할을 가져옵니다.
  
      ```powershell
      $transitiveRoles=@()
      foreach($item in $userRoleAssignableGroups){
          $transitiveRoles += (Get-AzureADMSRoleAssignment -Filter "principalId eq '$item'").RoleDefinitionId
      }
      ```

6. 사용자의 직접 및 전이적 역할 할당을 모두 결합합니다.
  
    ```powershell
    $allRoles = $directRoles + $transitiveRoles
    ```
  
## <a name="microsoft-graph-api"></a>Microsoft Graph API

[Graph Explorer](https://aka.ms/ge)에서 Microsoft Graph API를 사용하여 사용자에게 할당된 Azure AD 역할을 나열하려면 다음 단계를 수행합니다.

1. [Graph Explorer](https://aka.ms/ge)에 로그인합니다.

1. [List roleAssignments](/graph/api/rbacapplication-list-roleassignments) API를 사용하여 사용자에 직접 할당된 역할을 가져옵니다. URL에 다음 쿼리를 추가하고 **Run query** 를 선택합니다.

   ```HTTP
   GET https://graph.microsoft.com/beta/rolemanagement/directory/roleAssignments?$filter=principalId eq '55c07278-7109-4a46-ae60-4b644bc83a31'
   ```
  
3. 사용자에게 할당된 전이적 역할을 가져오려면 다음 단계를 수행합니다.

    a. [List groups](/graph/api/group-list)를 사용하여 역할 할당이 가능한 모든 그룹의 목록을 가져옵니다.
  
      ```HTTP
      GET https://graph.microsoft.com/beta/groups?$filter=isAssignableToRole eq true 
      ```
  
    b. [checkMemberObjects](/graph/api/user-checkmemberobjects) API에 이 목록을 전달하여 사용자가 구성원인 역할 할당이 가능한 그룹이 무엇인지 알아봅니다. 
    
      ```HTTP
      POST https://graph.microsoft.com/beta/users/55c07278-7109-4a46-ae60-4b644bc83a31/checkMemberObjects
      {
          "ids": [
              "936aec09-47d5-4a77-a708-db2ff1dae6f2",
              "5425a4a0-8998-45ca-b42c-4e00920a6382",
              "ca9631ad-2d2a-4a7c-88b7-e542bd8a7e12",
              "ea3cee12-360e-411d-b0ba-2173181daa76",
              "c3c263bb-b796-48ee-b4d2-3fbc5be5f944"
          ]
      }
      ```
  
    다. [List roleAssignments](/graph/api/rbacapplication-list-roleassignments) API를 사용하여 그룹을 루프하고 할당된 역할을 가져옵니다.
  
      ```HTTP
      GET https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments?$filter=principalId eq '5425a4a0-8998-45ca-b42c-4e00920a6382' 
      ```

## <a name="next-steps"></a>다음 단계

* [Azure AD 역할 할당을 나열](view-assignments.md)합니다.
* [사용자에게 Azure AD 역할 할당](manage-roles-portal.md).
* [그룹에 Azure AD 역할 할당](groups-assign-role.md)
