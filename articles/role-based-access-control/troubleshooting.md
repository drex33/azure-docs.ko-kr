---
title: Azure RBAC 문제 해결
description: Azure RBAC(역할 기반 액세스 제어)관련 문제를 해결합니다.
services: azure-portal
author: rolyon
manager: mtillman
ms.assetid: df42cca2-02d6-4f3c-9d56-260e1eb7dc44
ms.service: role-based-access-control
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 11/12/2021
ms.author: rolyon
ms.custom: seohack1, devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: e0ce4f4b0408b63c12c023ad56d473cc50a44027
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132398310"
---
# <a name="troubleshoot-azure-rbac"></a>Azure RBAC 문제 해결

이 문서에서는 Azure RBAC(Azure 역할 기반 액세스 제어)에 대한 일반적인 질문과 답변을 제공함으로써, 역할 사용 시 예상되는 상황을 이해하고 액세스 문제를 해결하는 데 도움을 줍니다.

## <a name="azure-role-assignments-limit"></a>Azure 역할 할당 제한

Azure는 구독당 최대 **2000** 개의 역할 할당을 지원합니다. 이 제한에는 구독, 리소스 그룹 및 리소스 범위의 역할 할당이 포함되지만 관리 그룹 범위에는 포함되지 않습니다. 역할을 할당 시 ‘더 이상의 역할 할당을 만들 수 없습니다(코드: RoleAssignmentLimitExceeded)’라는 오류 메시지가 표시되면, 구독에 역할을 할당하여 역할 할당 수를 줄여 보세요.

> [!NOTE]
> 2021년 11월부터 구독에 대한 역할 할당 제한은 향후 몇 개월 동안 **2000에서** **4000으로** 증가됩니다. 한도에 가까운 구독의 우선 순위가 먼저 정해지게 됩니다. 나머지 구독에 대한 제한은 시간이 지남에 따라 증가합니다.

해당 한도에 근접하는 경우 다음 몇 가지 방법으로 역할 할당 수를 줄일 수 있습니다.

- 그룹에 사용자를 추가한 후 그룹에 역할을 할당합니다. 
- 여러 기본 제공 역할을 사용자 지정 역할과 결합합니다. 
- 구독 또는 관리 그룹과 같은 상위 범위에서 일반적인 역할 할당을 수행합니다.
- Azure AD Premium P2를 사용하는 경우 역할을 영구적으로 할당하는 대신 [Azure AD Privileged Identity Management](../active-directory/privileged-identity-management/pim-configure.md)에서 적격하게 만듭니다. 
- 추가 구독 추가하기 

역할 할당 수를 얻으려면 Azure Portal의 [IAM(액세스 제어) 페이지의 차트](role-assignments-list-portal.md#list-number-of-role-assignments)를 참고할 수 있습니다. 다음 Azure PowerShell 명령을 사용할 수도 있습니다.

```azurepowershell
$scope = "/subscriptions/<subscriptionId>"
$ras = Get-AzRoleAssignment -Scope $scope | Where-Object {$_.scope.StartsWith($scope)}
$ras.Count
```

## <a name="azure-role-assignments-limit-for-management-groups"></a>관리 그룹에 대한 Azure 역할 할당 제한

Azure는 관리 그룹당 최대 **500개의** 역할 할당을 지원합니다. 이 제한은 구독당 역할 할당 제한과 다릅니다.

> [!NOTE]
> 관리 그룹당 **500개의** 역할 할당 제한은 고정되어 있으며 늘릴 수 없습니다.

## <a name="problems-with-azure-role-assignments"></a>Azure 역할 할당 관련 문제

- **추가** > **역할 할당 추가** 옵션이 비활성화되었거나 ‘이 개체 ID를 가진 클라이언트는 작업을 수행할 수 있는 권한이 없습니다’라는 권한 오류가 발생하여 **IAM**(액세스 제어)에 대한 Azure Portal에서 역할을 할당할 수 없는 경우, 역할을 할당하려는 범위에서 `Microsoft.Authorization/roleAssignments/write` 권한을 가진 역할이 할당된 사용자(예: [Owner](built-in-roles.md#owner) 또는 [User Access Administrator](built-in-roles.md#user-access-administrator))로 로그인했는지 확인합니다.
- 서비스 주체를 사용하여 역할을 할당하는 경우 ‘작업을 완료하는 데 필요한 권한이 없습니다’라는 오류가 발생할 수 있습니다. 예를 들어, Azure CLI를 사용하여 다음과 같이 역할 할당을 하려는 Owner 역할이 할당된 서비스 주체가 있다고 가정해 보겠습니다.

    ```azurecli
    az login --service-principal --username "SPNid" --password "password" --tenant "tenantid"
    az role assignment create --assignee "userupn" --role "Contributor"  --scope "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}"
    ```

    ‘작업을 완료하는 데 필요한 권한이 없습니다’라는 오류가 발생하는 경우, Azure CLI가 Azure AD에서 담당자 ID를 조회하려고 시도하거나 서비스 주체가 기본적으로 Azure AD를 읽을 수 없기 때문일 수 있습니다.

    해당 오류를 해결하는 방법은 두 가지가 있습니다. 첫 번째는 디렉터리의 데이터를 읽을 수 있도록 서비스 주체에게 [Directory Readers](../active-directory/roles/permissions-reference.md#directory-readers) 역할을 할당하는 것입니다.

    오류를 해결하는 두 번째 방법은`--assignee` 대체 매개 변수`--assignee-object-id`를 사용하여 역할을 할당하는 것입니다. `--assignee-object-id`을 사용하여 Azure CLI는 Azure AD 조회를 건너뛸 수 있습니다. 역할을 할당하려면 역할을 할당하려는 사용자나 그룹 또는 애플리케이션의 개체 ID를 가져와야 합니다. 자세한 내용은 [Azure CLI를 사용하여 Azure 역할 할당](role-assignments-cli.md#assign-a-role-for-a-new-service-principal-at-a-resource-group-scope)을 참조하세요.

    ```azurecli
    az role assignment create --assignee-object-id 11111111-1111-1111-1111-111111111111  --role "Contributor" --scope "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}"
    ```

- 새 서비스 주체를 만들고 해당 서비스 주체에 역할을 즉시 할당하려고 하면 경우에 따라 해당 역할 할당이 실패할 수 있습니다.

    이 시나리오를 해결하려면 역할 할당을 만들 때 `principalType` 속성을 `ServicePrincipal`로 설정해야 합니다. 또한 역할 할당의 `apiVersion`을 `2018-09-01-preview` 이상으로 설정해야 합니다. 자세한 내용은 [REST API를 사용하여 새 서비스 주체에 Azure 역할 할당](role-assignments-rest.md#new-service-principal) 또는 [Azure Resource Manager 템플릿을 사용하여 새 서비스 주체에 Azure 역할 할당](role-assignments-template.md#new-service-principal)을 참조하세요

- 구독에 대한 마지막 Owner 역할 할당을 제거하려면 ‘마지막 RBAC 관리자 할당을 삭제할 수 없습니다’라는 오류가 표시될 수 있습니다. 구독에 대한 분리를 방지하기 위해 구독의 마지막 Owner 역할 할당은 제거할 수 없습니다. Azure 구독을 취소하려는 경우 [Azure 구독 취소](../cost-management-billing/manage/cancel-azure-subscription.md)를 참조하세요.

## <a name="problems-with-custom-roles"></a>사용자 지정 역할의 문제

- 사용자 지정 역할을 만드는 방법은 [Azure Portal](custom-roles-portal.md), [Azure PowerShell](tutorial-custom-role-powershell.md), 또는 [Azure CLI](tutorial-custom-role-cli.md)를 사용하는 사용자 지정 역할 자습서를 참조하세요.
- 기존 사용자 지정 역할을 업데이트할 수 없는 경우 현재 `Microsoft.Authorization/roleDefinition/write` 권한이 있는 역할이 할당된 사용자(예: [Owner](built-in-roles.md#owner) 또는 [User Access Administrator](built-in-roles.md#user-access-administrator))로 로그인했는지 확인합니다.
- 사용자 지정 역할을 삭제할 수 없고 "역할을 참조하는 기존 역할 할당이 있습니다(코드: RoleDefinitionHasAssignments)" 오류 메시지가 표시되면 사용자 지정 역할을 사용하는 역할 할당이 여전히 있는 것입니다. 이 경우 해당 역할 할당을 제거하고 다시 삭제해 봅니다.
- 새 사용자 지정 역할을 만들려고 할 때 "역할 정의 제한을 초과했습니다. 더 이상 역할 정의를 만들 수 없습니다(코드: RoleDefinitionLimitExceeded)"라는 오류 메시지가 표시되면 사용하지 않는 모든 사용자 지정 역할을 삭제합니다. Azure는 디렉터리에서 최대 **5000** 개의 사용자 지정 역할을 지원합니다. (Azure 독일 및 Azure 중국 21Vianet에는 사용자 지정 역할을 2000개까지 사용할 수 있습니다.)
- 사용자 지정 역할을 업데이트하려고 할 때 “클라이언트가 ‘Microsoft.Authorization/roleDefinitions/write' on scope '/subscriptions/{subscriptionid}’ 작업을 수행할 권한이 있지만, 연결된 구독을 찾을 수 없습니다”와 비슷한 오류가 발생하는 경우 하나 이상의 [할당 가능한 범위](role-definitions.md#assignablescopes)가 디렉터리에서 삭제되었는지 확인합니다. 범위가 삭제되었으면 지원 티켓을 만듭니다. 현재는 사용 가능한 셀프 서비스 솔루션이 없기 때문입니다.

## <a name="custom-roles-and-management-groups"></a>사용자 지정 역할 및 관리 그룹

- 사용자 지정 역할 `AssignableScopes`에서 하나의 관리 그룹만 정의할 수 있습니다. `AssignableScopes`에 관리 그룹을 추가하는 것은 현재 미리 보기로 제공됩니다.
- `DataActions`의 사용자 지정 역할은 관리 그룹 범위에서 할당할 수 없습니다.
- Azure Resource Manager는 관리 그룹이 역할 정의의 할당 가능한 범위에 있는지 확인하지 않습니다.
- 사용자 지정 역할 및 관리 그룹에 대한 자세한 내용은 [Azure 관리 그룹으로 리소스 구성](../governance/management-groups/overview.md#azure-custom-role-definition-and-assignment)을 참조하세요.

## <a name="transferring-a-subscription-to-a-different-directory"></a>다른 디렉터리로 구독 이전

- 다른 Azure AD 디렉터리로 구독을 이전하는 방법은 [Azure 구독을 다른 Azure AD 디렉터리로 이전](transfer-subscription.md)을 참조하세요.
- 구독을 다른 Azure AD 디렉터리로 이전하는 경우 모든 역할 할당이 원본 Azure AD 디렉터리에서 **영구적으로** 삭제되고 대상 Azure AD 디렉터리로 마이그레이션되지 않습니다. 대상 디렉터리에서 역할 할당을 다시 만들어야 합니다. 또한 Azure 리소스에 대한 관리 ID를 수동으로 다시 만들어야 합니다. 자세한 내용은 [관리 ID에 대한 FAQ 및 알려진 문제](../active-directory/managed-identities-azure-resources/known-issues.md)를 참조하세요.
- Azure AD 글로벌 관리자인데 디렉터리 간에 구독이 이전된 후 구독에 액세스할 수 없는 경우 **Azure 리소스에 대한 액세스 관리** 토글을 사용하여 일시적으로 [액세스 권한을 상승](elevate-access-global-admin.md)하여 구독에 대한 액세스 권한을 얻으면 됩니다.

## <a name="issues-with-service-admins-or-co-admins"></a>서비스 관리자 또는 공동 관리자 관련 문제

- 서비스 관리자 또는 공동 관리자에게 문제가 있는 경우 [Azure 구독 관리자 추가 또는 변경](../cost-management-billing/manage/add-change-subscription-administrator.md) 및 [클래식 구독 관리자 역할, Azure 역할, Azure AD 역할](rbac-and-directory-admin-roles.md)을 참조하세요.

## <a name="access-denied-or-permission-errors"></a>액세스 거부 또는 권한 오류

- 리소스를 만들려고 할 때 "이 개체 ID를 가진 클라이언트는 이 범위에서 작업을 수행할 수 있는 권한이 없습니다(코드: AuthorizationFailed)"라는 권한 오류가 발생하면 현재 선택한 범위에서 리소스에 쓰기 권한이 있는 역할이 할당된 사용자로 로그인했는지 확인합니다. 예를 들어 리소스 그룹의 가상 머신을 관리하려면 리소스 그룹(또는 부모 범위)에 대한 [가상 머신 기여자](built-in-roles.md#virtual-machine-contributor) 역할이 필요합니다. 각 기본 제공 역할의 권한 목록은 [Azure 기본 제공 역할](built-in-roles.md)을 참조하세요.
- 지원 티켓을 만들거나 업데이트하려고 할 때 ‘지원 요청을 만들 수 있는 권한 없음’이라는 권한 오류가 발생하면 현재 `Microsoft.Support/supportTickets/write` 권한이 있는 역할이 할당된 사용자(예: [Support Request Contributor](built-in-roles.md#support-request-contributor))로 로그인했는지 확인합니다.

## <a name="move-resources-with-role-assignments"></a>역할 할당을 사용하여 리소스 이동

Azure 역할이 할당된 리소스를 리소스(또는 자식 리소스)로 직접 이동시키는 경우 역할 할당은 이동되지 않고 분리됩니다. 이동 후에 역할 할당을 다시 만들어야 합니다. 결국 분리된 역할 할당은 자동으로 제거되지만 리소스를 이동하기 전에 역할 할당을 제거하는 것이 좋습니다.

리소스를 이동하는 방법은 [새 리소스 그룹 또는 구독으로 리소스 이동](../azure-resource-manager/management/move-resource-group-and-subscription.md)을 참조하세요.

## <a name="role-assignments-with-identity-not-found"></a>ID의 역할 할당을 찾을 수 없음

Azure Portal의 역할 할당 목록에서 보안 주체(사용자, 그룹, 서비스 주체 또는 관리 ID)가 **알 수 없음** 형식의 **ID를 찾을 수 없음** 으로 표시된다는 것을 알 수 있습니다.

![Azure 역할 할당에 ID를 찾을 수 없음](./media/troubleshooting/unknown-security-principal.png)

다음 두 가지 이유로 ID를 찾지 못할 수 있습니다.

- 최근에 사용자의 역할 할당을 만들고 초대함
- 역할 할당이 있는 보안 주체를 삭제함

최근에 사용자의 역할 할당을 만들고 초대한 경우 해당 보안 주체는 지역 간 복제 프로세스에 있을 수 있습니다. 그런 경우 몇 분 정도 기다렸다가 역할 할당 목록을 새로 고칩니다.

그러나 해당 보안 주체가 최근에 초대된 사용자가 아닌 경우 보안 사용자가 삭제되었을 수 있습니다. 보안 주체의 역할 할당을 먼저 제거하지 않고 역할을 할당한 뒤 나중에 삭제하면 보안 주체가 **알 수 없음** 형식의 **ID를 찾을 수 없음** 으로 표시됩니다.

Azure PowerShell를 사용하여 해당 역할 할당을 나열하는 경우 빈 `DisplayName` 및 **알 수 없음** 으로 `ObjectType` 설정되는 것을 볼 수 있습니다. 예를 들어 [AzRoleAssignment 가져오기](/powershell/module/az.resources/get-azroleassignment)는 다음 출력과 유사한 역할 할당을 반환합니다.

```
RoleAssignmentId   : /subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleAssignments/22222222-2222-2222-2222-222222222222
Scope              : /subscriptions/11111111-1111-1111-1111-111111111111
DisplayName        :
SignInName         :
RoleDefinitionName : Storage Blob Data Contributor
RoleDefinitionId   : ba92f5b4-2d11-453d-a403-e96b0029c9fe
ObjectId           : 33333333-3333-3333-3333-333333333333
ObjectType         : Unknown
CanDelegate        : False
```

마찬가지로 Azure CLI를 사용하여 해당 역할 할당을 나열하는 경우 빈 `principalName`가 표시될 수 있습니다. 예를 들어 [az role 할당 목록](/cli/azure/role/assignment#az_role_assignment_list)은 다음 출력과 유사한 역할 할당을 반환합니다.

```
{
    "canDelegate": null,
    "id": "/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleAssignments/22222222-2222-2222-2222-222222222222",
    "name": "22222222-2222-2222-2222-222222222222",
    "principalId": "33333333-3333-3333-3333-333333333333",
    "principalName": "",
    "roleDefinitionId": "/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleDefinitions/ba92f5b4-2d11-453d-a403-e96b0029c9fe",
    "roleDefinitionName": "Storage Blob Data Contributor",
    "scope": "/subscriptions/11111111-1111-1111-1111-111111111111",
    "type": "Microsoft.Authorization/roleAssignments"
}
```

보안 주체가 삭제된 위치에서 해당 역할 할당을 유지하는 것은 문제가 되지 않습니다. 원하는 경우 다른 역할 할당과 비슷한 단계로 해당 역할 할당을 제거할 수 있습니다. 역할 할당을 제거하는 방법에 대한 자세한 내용은 [Azure 역할 할당 제거하기](role-assignments-remove.md)를 참조하세요.

PowerShell에서 개체 ID 및 역할 정의 이름을 사용하여 역할 할당을 제거하고자 하며 둘 이상의 역할 할당이 매개 변수와 일치하는 경우 ‘제공된 정보가 역할 할당에 매핑되지 않습니다’라는 오류 메시지가 표시됩니다. 다음 출력은 오류 메시지의 예시입니다.

```
PS C:\> Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 -RoleDefinitionName "Storage Blob Data Contributor"

Remove-AzRoleAssignment : The provided information does not map to a role assignment.
At line:1 char:1
+ Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
+ CategoryInfo          : CloseError: (:) [Remove-AzRoleAssignment], KeyNotFoundException
+ FullyQualifiedErrorId : Microsoft.Azure.Commands.Resources.RemoveAzureRoleAssignmentCommand
```

해당 오류 메시지가 나타나면 `-Scope` 또는 `-ResourceGroupName` 매개 변수를 지정하는 것을 명심하세요.

```
PS C:\> Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 -RoleDefinitionName "Storage Blob Data Contributor" - Scope /subscriptions/11111111-1111-1111-1111-111111111111
```

## <a name="role-assignment-changes-are-not-being-detected"></a>역할 할당 변경 내용이 인식되지 않음

Azure Resource Manager는 경우에 따라 성능 향상을 위해 구성 및 데이터를 캐시합니다. 역할을 할당하거나 역할 할당을 제거하는 경우 변경 내용이 적용되는 데 최대 30분이 걸릴 수 있습니다. Azure Portal, Azure PowerShell 또는 Azure CLI를 사용하는 경우 로그아웃 및 로그인하여 역할 할당 변경 내용을 강제로 새로 고칠 수 있습니다. REST API 호출을 사용하여 역할 할당을 변경하는 경우 액세스 토큰을 새로 고쳐 강제로 새로 고칠 수 있습니다.

관리 그룹 범위에서 역할 할당을 추가 또는 제거하는 경우 역할에 `DataActions`가 있으면 데이터 평면에 대한 액세스를 몇 시간 동안 업데이트하지 못할 수 있습니다. 이는 관리 그룹 범위 및 데이터 평면에만 적용됩니다.

## <a name="web-app-features-that-require-write-access"></a>쓰기 액세스 권한이 필요한 웹앱 기능

사용자에게 단일 웹앱에 대한 읽기 전용 액세스를 부여하면 예상치 않게 일부 기능을 사용하지 못할 수 있습니다. 다음 관리 기능을 사용하려면 참여자나 소유자에게 제공되는 웹앱에 대한 **쓰기** 권한이 필요하며, 읽기 전용 시나리오에서는 이러한 기능을 사용할 수 없습니다.

* 시작, 중지 등의 명령
* 일반 구성, 규모 설정, 백업 설정, 모니터링 설정 등의 설정 변경
* 게시 자격 증명과 앱 설정, 연결 문자열 등의 기타 암호 액세스
* 스트리밍 로그
* 리소스 로그 구성
* 콘솔(명령 프롬프트)
* 활성 및 최근 배포(로컬 Git 연속 배포의 경우)
* 예상 소요 시간
* 웹 테스트
* 가상 네트워크(쓰기 권한이 있는 사용자가 이전에 가상 네트워크를 구성한 경우에만 읽기 권한자에게 표시됨)

이러한 타일에 액세스할 수 없는 경우 관리자에게 웹앱에 대한 참가자 권한을 요청해야 합니다.

## <a name="web-app-resources-that-require-write-access"></a>쓰기 액세스 권한이 필요한 웹앱 리소스

웹앱에서 몇 가지 리소스가 상호 연동되는 경우 웹앱의 구조가 복잡해집니다. 아래에는 웹 사이트 몇 개가 포함된 일반적인 리소스 그룹이 나와 있습니다.

![웹앱 리소스 그룹](./media/troubleshooting/website-resource-model.png)

따라서 사용자에게 웹앱에 대한 권한만 부여하면 Azure Portal에서 웹 사이트 블레이드의 기능을 대부분 사용할 수 없게 됩니다.

다음 항목을 사용하려면 웹 사이트에 해당하는 **App Service 계획** 에 대한 **쓰기** 권한이 필요합니다.  

* 웹앱의 가격 책정 계층 보기(무료 또는 표준)  
* 크기 조정 구성(인스턴스 수, 가상 머신 크기, 자동 크기 조정 설정)  
* 할당량(스토리지, 대역폭, CPU)  

다음 항목을 사용하려면 웹 사이트를 포함하는 전체 **리소스 그룹** 에 대한 **쓰기** 권한이 필요합니다.  

* TLS/SSL 인증서 및 바인딩(TLS/SSL 인증서는 같은 리소스 그룹과 지리적 위치의 사이트 간에 공유될 수 있음)  
* 경고 규칙  
* 자동 크기 조정 설정  
* Application Insights 구성 요소  
* 웹 테스트  

## <a name="virtual-machine-features-that-require-write-access"></a>쓰기 액세스 권한이 필요한 가상 머신 기능

웹앱과 마찬가지로 가상 머신 블레이드의 일부 기능 역시 가상 머신 또는 리소스 그룹의 기타 리소스에 대한 쓰기 권한이 있어야 사용할 수 있습니다.

가상 머신은 도메인 이름, 가상 네트워크, 스토리지 계정 및 경고 규칙과 관련이 있습니다.

다음 항목을 사용하려면 **가상 머신** 에 대한 **쓰기** 권한이 필요합니다.

* 엔드포인트  
* IP 주소  
* 디스크  
* 확장  

다음 항목을 사용하려면 **가상 머신** 와 가상 머신이 속한 **리소스 그룹**(도메인 이름 포함) 둘 다에 대한 **쓰기** 권한이 필요합니다.  

* 가용성 집합  
* 부하 분산된 집합  
* 경고 규칙  

이러한 타일에 액세스할 수 없는 경우 관리자에게 리소스 그룹에 대한 참가자 권한을 요청합니다.

## <a name="azure-functions-and-write-access"></a>Azure Functions 및 쓰기 액세스

[Azure Functions](../azure-functions/functions-overview.md)의 일부 기능에는 쓰기 액세스 권한이 있어야 합니다. 예를 들어, 사용자가 [Reader](built-in-roles.md#reader) 역할에 할당된 경우 함수 앱 내에서 함수를 볼 수 없습니다. 포털에서는 **(액세스 권한 없음)** 을 표시합니다.

![함수 앱 액세스 권한 없음](./media/troubleshooting/functionapps-noaccess.png)

판독기는 **플랫폼 기능** 탭을 클릭한 다음, **모든 설정** 을 클릭하여 함수 앱(웹앱과 유사)에 관련된 일부 설정을 볼 수 있지만 이러한 설정을 수정할 수 없습니다. 해당 기능에 액세스하려면 [Contributor](built-in-roles.md#contributor) 역할이 필요합니다.

## <a name="next-steps"></a>다음 단계

- [게스트 사용자에 대한 문제 해결](role-assignments-external-users.md#troubleshoot)
- [Azure Portal을 사용하여 Azure 역할 할당](role-assignments-portal.md)
- [Azure RBAC 변경 사항에 대한 활동 로그 보기](change-history-report.md)
