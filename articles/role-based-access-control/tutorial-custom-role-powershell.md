---
title: '자습서: Azure PowerShell을 사용하여 Azure 사용자 지정 역할 만들기 - Azure RBAC'
description: 이 자습서에서는 Azure PowerShell 및 Azure RBAC(Azure 역할 기반 액세스 제어)를 사용하여 Azure 사용자 지정 역할 만들기를 시작합니다.
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: tutorial
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 02/20/2019
ms.author: rolyon
ms.openlocfilehash: a4fac2cde6f18e504dc2866cc479ce51e3b70b2a
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129361996"
---
# <a name="tutorial-create-an-azure-custom-role-using-azure-powershell"></a>자습서: Azure PowerShell을 사용하여 Azure 사용자 지정 역할 만들기

Azure 기본 제공 역할이 조직의 특정 요구 사항을 충족하지 않는 경우 [사용자 지정 역할](built-in-roles.md)을 만들면 됩니다. 이 자습서에서는 Azure PowerShell을 사용하여 Reader 지원 티켓이라는 사용자 지정 역할을 만듭니다. 사용자는 사용자 지정 역할을 통해 구독의 컨트롤 플레인에서 모든 것을 살펴보고 지원 티켓을 열 수도 있습니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * 사용자 지정 역할 만들기
> * 사용자 지정 역할 나열
> * 사용자 지정 역할 업데이트
> * 사용자 지정 역할 삭제

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>사전 요구 사항

이 자습서를 완료하려면 다음 항목이 필요합니다.

- 사용자 지정 역할을 만들 수 있는 권한(예: [소유자](built-in-roles.md#owner) 또는 [사용자 액세스 관리자](built-in-roles.md#user-access-administrator))
- [Azure Cloud Shell](../cloud-shell/overview.md) 또는 [Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="sign-in-to-azure-powershell"></a>Azure PowerShell에 로그인

[Azure PowerShell](/powershell/azure/authenticate-azureps)에 로그인합니다.

## <a name="create-a-custom-role"></a>사용자 지정 역할 만들기

사용자 지정 역할을 만드는 가장 쉬운 방법은 기본 제공 역할로 시작하여 편집한 다음, 새 역할을 만드는 것입니다.

1. PowerShell에서 [Get-AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation) 명령을 사용하여 Microsoft.Support 리소스 공급자의 작업 목록을 가져옵니다. 권한을 만드는 데 사용할 수 있는 작업을 파악하는 데 도움이 됩니다. [Azure 리소스 공급자 작업](resource-provider-operations.md#microsoftsupport)에서 모든 작업 목록을 볼 수도 있습니다.

    ```azurepowershell
    Get-AzProviderOperation "Microsoft.Support/*" | FT Operation, Description -AutoSize
    ```
    
    ```Output
    Operation                              Description
    ---------                              -----------
    Microsoft.Support/register/action      Registers to Support Resource Provider
    Microsoft.Support/supportTickets/read  Gets Support Ticket details (including status, severity, contact ...
    Microsoft.Support/supportTickets/write Creates or Updates a Support Ticket. You can create a Support Tic...
    ```

1. [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) 명령을 사용하여 [Reader](built-in-roles.md#reader) 역할을 JSON 형식으로 내보냅니다.

    ```azurepowershell
    Get-AzRoleDefinition -Name "Reader" | ConvertTo-Json | Out-File C:\CustomRoles\ReaderSupportRole.json
    ```

1. 편집기에서 **ReaderSupportRole.json** 파일을 엽니다.

    다음은 JSON 출력을 보여줍니다. 여러 속성에 대한 내용은 [Azure 사용자 지정 역할](custom-roles.md)을 참조하세요.

    ```json
    {
      "Name": "Reader",
      "Id": "acdd72a7-3385-48ef-bd42-f606fba81ae7",
      "IsCustom": false,
      "Description": "Lets you view everything, but not make any changes.",
      "Actions": [
        "*/read"
      ],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": [
        "/"
      ]
    }
    ```
    
1. JSON 파일을 편집하여 `"Microsoft.Support/*"` 작업을 `Actions` 속성에 추가합니다. 읽기 작업 뒤에는 꼭 쉼표를 추가해야 합니다. 이 작업을 통해 사용자가 지원 티켓을 만들 수 있게 됩니다.

1. [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription) 명령을 사용하여 구독 ID를 가져옵니다.

    ```azurepowershell
    Get-AzSubscription
    ```

1. `AssignableScopes`에서 구독 ID를 `"/subscriptions/00000000-0000-0000-0000-000000000000"` 형식으로 추가합니다.

    명시적 구독 ID를 추가해야 합니다. 그렇지 않으면 역할을 구독으로 가져올 수 없습니다.

1. `Id` 속성 줄을 삭제하고 `IsCustom` 속성을 `true`로 변경합니다.

1. `Name` 및 `Description` 속성을 "Reader 지원 티켓" 및 "구독의 모든 것을 살펴보고 지원 티켓 열기"로 변경합니다.

    JSON 파일은 다음과 비슷합니다.

    ```json
    {
      "Name": "Reader Support Tickets",
      "IsCustom": true,
      "Description": "View everything in the subscription and also open support tickets.",
      "Actions": [
        "*/read",
        "Microsoft.Support/*"
      ],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": [
        "/subscriptions/00000000-0000-0000-0000-000000000000"
      ]
    }
    ```
    
1. 새로운 사용자 지정 역할을 만들려면 [New-AzRoleDefinition](/powershell/module/az.resources/new-azroledefinition) 명령을 사용하여 JSON 역할 정의 파일을 지정합니다.

    ```azurepowershell
    New-AzRoleDefinition -InputFile "C:\CustomRoles\ReaderSupportRole.json"
    ```

    ```Output
    Name             : Reader Support Tickets
    Id               : 22222222-2222-2222-2222-222222222222
    IsCustom         : True
    Description      : View everything in the subscription and also open support tickets.
    Actions          : {*/read, Microsoft.Support/*}
    NotActions       : {}
    DataActions      : {}
    NotDataActions   : {}
    AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000}
    ```
        
    이제 Azure Portal에서 새로운 사용자 지정 역할을 사용할 수 있으며 기본 제공 역할과 마찬가지로 사용자, 그룹 또는 서비스 사용자에게 할당할 수 있습니다.

## <a name="list-custom-roles"></a>사용자 지정 역할 나열

- 모든 사용자 지정 역할을 나열하려면 [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) 명령을 사용합니다.

    ```azurepowershell
    Get-AzRoleDefinition | ? {$_.IsCustom -eq $true} | FT Name, IsCustom
    ```

    ```Output
    Name                   IsCustom
    ----                   --------
    Reader Support Tickets     True
    ```
    
    Azure Portal에서도 사용자 지정 역할을 볼 수 있습니다.

    ![Azure Portal에서 가져온 사용자 지정 역할의 스크린샷](./media/tutorial-custom-role-powershell/custom-role-reader-support-tickets.png)

## <a name="update-a-custom-role"></a>사용자 지정 역할 업데이트

사용자 지정 역할을 업데이트하려면 JSON 파일을 업데이트하거나 `PSRoleDefinition` 개체를 사용하면 됩니다.

1. JSON 파일을 업데이트하려면 [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) 명령을 사용하여 사용자 지정 역할을 JSON 형식으로 내보냅니다.

    ```azurepowershell
    Get-AzRoleDefinition -Name "Reader Support Tickets" | ConvertTo-Json | Out-File C:\CustomRoles\ReaderSupportRole2.json
    ```

1. 편집기에서 파일을 엽니다.

1. `Actions`에 리소스 그룹 배포 `"Microsoft.Resources/deployments/*"`을(를) 만들고 관리하는 작업을 추가합니다.

    업데이트된 JSON 파일은 다음과 비슷합니다.

    ```json
    {
      "Name": "Reader Support Tickets",
      "Id": "22222222-2222-2222-2222-222222222222",
      "IsCustom": true,
      "Description": "View everything in the subscription and also open support tickets.",
      "Actions": [
        "*/read",
        "Microsoft.Support/*",
        "Microsoft.Resources/deployments/*"
      ],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": [
        "/subscriptions/00000000-0000-0000-0000-000000000000"
      ]
    }
    ```
        
1. 사용자 지정 역할을 업데이트하려면 [Set-AzRoleDefinition](/powershell/module/az.resources/set-azroledefinition) 명령을 사용하여 업데이트된 JSON 파일을 지정합니다.

    ```azurepowershell
    Set-AzRoleDefinition -InputFile "C:\CustomRoles\ReaderSupportRole2.json"
    ```

    ```Output
    Name             : Reader Support Tickets
    Id               : 22222222-2222-2222-2222-222222222222
    IsCustom         : True
    Description      : View everything in the subscription and also open support tickets.
    Actions          : {*/read, Microsoft.Support/*, Microsoft.Resources/deployments/*}
    NotActions       : {}
    DataActions      : {}
    NotDataActions   : {}
    AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000}
    ```

1. `PSRoleDefintion` 개체를 사용하여 사용자 지정 역할을 업데이트하려면 먼저 [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) 명령을 사용하여 역할을 가져옵니다.

    ```azurepowershell
    $role = Get-AzRoleDefinition "Reader Support Tickets"
    ```
    
1. `Add` 메서드를 호출하여 읽기 진단 설정에 작업을 추가합니다.

    ```azurepowershell
    $role.Actions.Add("Microsoft.Insights/diagnosticSettings/*/read")
    ```

1. [Set-AzRoleDefinition](/powershell/module/az.resources/set-azroledefinition) 명령을 사용하여 역할을 업데이트합니다.

    ```azurepowershell
    Set-AzRoleDefinition -Role $role
    ```
    
    ```Output
    Name             : Reader Support Tickets
    Id               : 22222222-2222-2222-2222-222222222222
    IsCustom         : True
    Description      : View everything in the subscription and also open support tickets.
    Actions          : {*/read, Microsoft.Support/*, Microsoft.Resources/deployments/*,
                       Microsoft.Insights/diagnosticSettings/*/read}
    NotActions       : {}
    DataActions      : {}
    NotDataActions   : {}
    AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000}
    ```
    
## <a name="delete-a-custom-role"></a>사용자 지정 역할 삭제

1. [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) 명령을 사용하여 사용자 지정 역할 ID를 가져옵니다.

    ```azurepowershell
    Get-AzRoleDefinition "Reader Support Tickets"
    ```

1. [Remove-AzRoleDefinition](/powershell/module/az.resources/remove-azroledefinition) 명령을 사용하여 사용자 지정 역할을 삭제할 역할 ID를 지정합니다.

    ```azurepowershell
    Remove-AzRoleDefinition -Id "22222222-2222-2222-2222-222222222222"
    ```

    ```Output
    Confirm
    Are you sure you want to remove role definition with id '22222222-2222-2222-2222-222222222222'.
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
    ```

1. 확인을 묻는 메시지가 나타나면 **Y** 를 선택합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure PowerShell을 사용하여 사용자 지정 역할 만들기 또는 업데이트](custom-roles-powershell.md)
