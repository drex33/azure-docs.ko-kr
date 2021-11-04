---
title: 관리 그룹을 사용하는 방법 - Azure 거버넌스
description: 관리 그룹 계층 구조를 살펴보고, 유지 관리하고, 업데이트하고, 삭제하는 방법을 알아봅니다.
ms.date: 08/17/2021
ms.topic: conceptual
ms.openlocfilehash: d9d783ffad2d99f1030994c292ea6c4c276bc1e5
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131469087"
---
# <a name="manage-your-resources-with-management-groups"></a>관리 그룹으로 리소스 관리

조직에 구독이 많으면 구독에 대한 액세스, 정책 및 준수를 효율적으로 관리하는 방법이 필요할 수 있습니다. Azure 관리 그룹은 구독 상위 수준의 범위를 제공합니다. "관리 그룹"이라는 컨테이너에 구독을 구성하고 거버넌스 조건을 관리 그룹에 적용합니다. 관리 그룹에 속하는 모든 구독은 관리 그룹에 적용되는 조건을 자동으로 상속합니다.

관리 그룹은 어떤 형식의 구독을 사용하든 관계 없이 대규모의 엔터프라이즈급 관리를 제공합니다. 관리 그룹에 대해 자세히 알아보려면 [Azure 관리 그룹으로 리소스 구성](./overview.md)을 참조하세요.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

> [!IMPORTANT]
> Azure Resource Manager 사용자 토큰 및 관리 그룹 캐시는 30분 동안 지속된 후 강제로 새로 고쳐집니다. 관리 그룹 또는 구독 이동과 같은 작업을 수행한 후에는 표시하는 데 최대 30분이 걸릴 수 있습니다. 업데이트를 더 빨리 보려면 브라우저를 새로 고치거나, 로그인하고 로그아웃하거나, 새 토큰을 요청하여 토큰을 업데이트해야 합니다.

> [!IMPORTANT]
> AzManagementGroup 관련 Az PowerShell cmdlet은 **-GroupId** 가 **-GroupName** 매개 변수의 별칭인 것을 언급 하므로이 중 하나를 사용 하 여 관리 그룹 Id를 문자열 값으로 제공할 수 있습니다. 

## <a name="change-the-name-of-a-management-group"></a>관리 그룹의 이름 변경

포털, PowerShell 또는 Azure CLI를 사용하여 관리 그룹의 이름을 변경할 수 있습니다.

### <a name="change-the-name-in-the-portal"></a>포털에서 이름 변경

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. **모든 서비스** > **관리 그룹** 을 선택합니다.

1. 이름을 바꾸려는 관리 그룹을 선택합니다.

1. **세부 정보** 를 선택합니다.

1. 페이지 맨 위에서 **그룹 이름 바꾸기** 옵션을 선택합니다.

   :::image type="content" source="./media/detail_action_small.png" alt-text="관리 그룹 페이지의 작업 모음 및 '그룹 이름 바꾸기' 단추의 스크린샷" border="false":::

1. 메뉴가 열릴 때 표시하려는 새 이름을 입력합니다.

   :::image type="content" source="./media/rename_context.png" alt-text="그룹 이름 바꾸기 창 및 관리 그룹의 이름을 바꾸는 옵션의 스크린샷" border="false":::

1. **저장** 을 선택합니다.

### <a name="change-the-name-in-powershell"></a>PowerShell에서 이름 변경

표시 이름을 업데이트하려면 **Update-AzManagementGroup** 을 사용합니다. 예를 들어 “Contoso IT”에서 “Contoso Group”으로 관리 그룹 표시 이름을 변경하려면 다음 명령을 실행합니다.

```azurepowershell-interactive
Update-AzManagementGroup -GroupId 'ContosoIt' -DisplayName 'Contoso Group'
```

### <a name="change-the-name-in-azure-cli"></a>Azure CLI에서 이름 변경

Azure CLI의 경우 update 명령을 사용합니다.

```azurecli-interactive
az account management-group update --name 'Contoso' --display-name 'Contoso Group'
```

## <a name="delete-a-management-group"></a>관리 그룹 삭제

관리 그룹을 삭제하려면 다음 요구 사항이 충족되어야 합니다.

1. 관리 그룹 아래에 자식 관리 그룹 또는 구독이 없습니다. 구독 또는 관리 그룹을 다른 관리 그룹으로 이동하려면 [계층 구조에서 관리 그룹 및 구독 이동](#moving-management-groups-and-subscriptions)을 참조하세요.

1. 관리 그룹(“소유자”, “참가자” 또는 “관리 그룹 참가자”)에 대한 쓰기 권한이 필요합니다. 보유하고 있는 사용 권한을 보려면 관리 그룹을 선택하고 **IAM** 을 선택합니다. Azure 역할에 대해 자세히 알아보려면 [Azure RBAC(Azure 역할 기반 액세스 제어)](../../role-based-access-control/overview.md)를 참조하세요.

### <a name="delete-in-the-portal"></a>포털에서 삭제

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. **모든 서비스** > **관리 그룹** 을 선택합니다.

1. 삭제하려는 관리 그룹을 선택합니다.

1. **세부 정보** 를 선택합니다.

1. **삭제** 선택

   :::image type="content" source="./media/delete.png" alt-text="'삭제' 단추가 강조 표시된 관리 그룹 페이지의 스크린샷" border="false":::

   > [!TIP]
   > 아이콘이 사용되지 않도록 설정되면 아이콘 위로 마우스 선택기를 가져가면 이유가 표시됩니다.

1. 관리 그룹을 삭제할 것인지 확인하는 창이 열립니다.

   :::image type="content" source="./media/delete_confirm.png" alt-text="관리 그룹을 삭제하는 '그룹 삭제' 확인 대화 상자의 스크린샷" border="false":::

1. **예** 를 선택합니다.

### <a name="delete-in-powershell"></a>PowerShell에서 삭제

관리 그룹을 삭제하려면 PowerShell 내에서 **Remove-AzManagementGroup** 명령을 사용합니다.

```azurepowershell-interactive
Remove-AzManagementGroup -GroupId 'Contoso'
```

### <a name="delete-in-azure-cli"></a>Azure CLI에서 삭제

Azure CLI에서 az account management-group delete 명령을 사용합니다.

```azurecli-interactive
az account management-group delete --name 'Contoso'
```

## <a name="view-management-groups"></a>관리 그룹 보기

직접 또는 상속된 Azure 역할이 있는 모든 관리 그룹을 볼 수 있습니다.

### <a name="view-in-the-portal"></a>포털에서 보기

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. **모든 서비스** > **관리 그룹** 을 선택합니다.

1. 관리 그룹 계층 구조 페이지가 로드됩니다. 이 페이지에서는 액세스 권한이 있는 모든 관리 그룹 및 구독을 살펴볼 수 있습니다. 그룹 이름을 선택하면 계층에서 한 수준 아래로 이동합니다. 탐색은 파일 탐색기처럼 작동합니다.

1. 관리 그룹의 세부 정보를 보려면 관리 그룹의 제목 옆에 있는 **(세부 정보)** 링크를 선택합니다. 이 링크를 사용할 수 없으면 해당 관리 그룹을 볼 수 있는 권한이 없습니다.

   :::image type="content" source="./media/main.png" alt-text="자식 관리 그룹 및 구독을 보여주는 관리 그룹 페이지 스크린샷" border="false":::

### <a name="view-in-powershell"></a>PowerShell에서 보기

모든 그룹을 검색하려면 Get-AzManagementGroup 명령을 사용합니다. 관리 그룹 GET PowerShell 명령의 전체 목록은 [Az.Resources](/powershell/module/az.resources/Get-AzManagementGroup) 모듈을 참조하세요.

```azurepowershell-interactive
Get-AzManagementGroup
```

단일 관리 그룹의 정보를 보려면-GroupId 매개 변수를 사용 합니다.

```azurepowershell-interactive
Get-AzManagementGroup -GroupId 'Contoso'
```

특정 관리 그룹 및 그 아래에 있는 계층 구조의 모든 수준을 반환하려면 **-Expand** 및 **-Recurse** 매개 변수를 사용합니다.

```azurepowershell-interactive
PS C:\> $response = Get-AzManagementGroup -GroupId TestGroupParent -Expand -Recurse
PS C:\> $response

Id                : /providers/Microsoft.Management/managementGroups/TestGroupParent
Type              : /providers/Microsoft.Management/managementGroups
Name              : TestGroupParent
TenantId          : 00000000-0000-0000-0000-000000000000
DisplayName       : TestGroupParent
UpdatedTime       : 2/1/2018 11:15:46 AM
UpdatedBy         : 00000000-0000-0000-0000-000000000000
ParentId          : /providers/Microsoft.Management/managementGroups/00000000-0000-0000-0000-000000000000
ParentName        : 00000000-0000-0000-0000-000000000000
ParentDisplayName : 00000000-0000-0000-0000-000000000000
Children          : {TestGroup1DisplayName, TestGroup2DisplayName}

PS C:\> $response.Children[0]

Type        : /managementGroup
Id          : /providers/Microsoft.Management/managementGroups/TestGroup1
Name        : TestGroup1
DisplayName : TestGroup1DisplayName
Children    : {TestRecurseChild}

PS C:\> $response.Children[0].Children[0]

Type        : /managementGroup
Id          : /providers/Microsoft.Management/managementGroups/TestRecurseChild
Name        : TestRecurseChild
DisplayName : TestRecurseChild
Children    :
```

### <a name="view-in-azure-cli"></a>Azure CLI에서 보기

모든 그룹을 검색하려면 list 명령을 사용합니다.

```azurecli-interactive
az account management-group list
```

단일 관리 그룹의 정보를 보려면 show 명령을 사용합니다.

```azurecli-interactive
az account management-group show --name 'Contoso'
```

특정 관리 그룹 및 그 아래에 있는 계층 구조의 모든 수준을 반환하려면 **-Expand** 및 **-Recurse** 매개 변수를 사용합니다.

```azurecli-interactive
az account management-group show --name 'Contoso' -e -r
```

## <a name="moving-management-groups-and-subscriptions"></a>관리 그룹 및 구독 이동

관리 그룹을 만드는 한 가지 이유는 구독을 번들로 묶는 것입니다. 관리 그룹 및 구독만 다른 관리 그룹의 자식으로 만들 수 있습니다. 관리 그룹으로 이동되는 구독은 부모 관리 그룹에서 모든 사용자 액세스 및 정책을 상속합니다.

다른 관리 그룹의 자식이 되도록 관리 그룹 또는 구독을 이동하는 경우 세 가지 규칙이 true로 평가되어야 합니다.

이동 작업을 수행하는 경우 다음 각 계층에 대한 권한이 필요합니다.

- 자식 구독/관리 그룹
  - `Microsoft.management/managementgroups/write`
  - `Microsoft.management/managementgroups/subscription/write`(구독에만 해당)
  - `Microsoft.Authorization/roleassignment/write`
- 대상 부모 관리 그룹
  - `Microsoft.management/managementgroups/write`
- 현재 부모 관리 그룹
  - `Microsoft.management/managementgroups/write`

**예외**: 대상 또는 기존 부모 관리 그룹이 루트 관리 그룹인 경우 권한 요구 사항이 적용되지 않습니다. 루트 관리 그룹은 모든 새 관리 그룹 및 구독의 기본 랜딩 스팟이므로 루트 관리 그룹에 대한 권한이 없어도 항목을 이동할 수 있습니다.

구독의 소유자 역할이 현재 관리 그룹에서 상속되는 경우 이동 대상이 제한됩니다. 소유자 역할이 있는 다른 관리 그룹으로만 구독을 이동할 수 있습니다. 기여자 역할만 있는 관리 그룹으로는 구독을 이동할 수 없습니다. 구독의 소유권을 잃게 되기 때문입니다. 구독의 소유자 역할에 직접 할당되는 경우 기여자인 관리 그룹으로 이동할 수 있습니다.

Azure Portal에서 보유하고 있는 권한을 보려면 관리 그룹을 선택하고 **IAM** 을 선택합니다. Azure 역할에 대해 자세히 알아보려면 [Azure RBAC(Azure 역할 기반 액세스 제어)](../../role-based-access-control/overview.md)를 참조하세요.

## <a name="move-subscriptions"></a>구독 이동

### <a name="add-an-existing-subscription-to-a-management-group-in-the-portal"></a>포털에서 관리 그룹에 기존 구독 추가

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. **모든 서비스** > **관리 그룹** 을 선택합니다.

1. 부모로 지정할 관리 그룹을 선택합니다.

1. 페이지 맨 위에서 **구독 추가** 를 선택합니다.

1. 올바른 ID를 사용해서 목록의 구독을 선택합니다.

   :::image type="content" source="./media/add_context_sub.png" alt-text="관리 그룹에 추가할 기존 구독을 선택하기 위한 '구독 추가' 옵션의 스크린샷" border="false":::

1. “저장”을 선택합니다.

### <a name="remove-a-subscription-from-a-management-group-in-the-portal"></a>포털에서 관리 그룹의 구독 제거

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. **모든 서비스** > **관리 그룹** 을 선택합니다.

1. 현재 부모로 지정할 관리 그룹을 선택합니다.

1. 이동하려는 목록의 구독에 대해 행 끝에 있는 줄임표를 선택합니다.

   :::image type="content" source="./media/move_small.png" alt-text="구독 '이동' 옵션을 선택하는 대체 메뉴의 스크린샷" border="false":::

1. **이동** 을 선택합니다.

1. 열리는 메뉴에서 **부모 관리 그룹** 을 선택합니다.

   :::image type="content" source="./media/move_small_context.png" alt-text="다른 관리 그룹으로 구독을 이동하기 위한 '이동' 창 및 옵션의 스크린샷" border="false":::

1. **저장** 을 선택합니다.

### <a name="move-subscriptions-in-powershell"></a>PowerShell에서 구독 이동

PowerShell에서 구독을 이동하려면 New-AzManagementGroupSubscription 명령을 사용합니다.

```azurepowershell-interactive
New-AzManagementGroupSubscription -GroupId 'Contoso' -SubscriptionId '12345678-1234-1234-1234-123456789012'
```

구독 및 관리 그룹 간의 링크를 제거하려면 Remove-AzManagementGroupSubscription 명령을 사용합니다.

```azurepowershell-interactive
Remove-AzManagementGroupSubscription -GroupId 'Contoso' -SubscriptionId '12345678-1234-1234-1234-123456789012'
```

### <a name="move-subscriptions-in-azure-cli"></a>Azure CLI에서 구독 이동

CLI에서 구독을 이동하려면 add 명령을 사용합니다.

```azurecli-interactive
az account management-group subscription add --name 'Contoso' --subscription '12345678-1234-1234-1234-123456789012'
```

관리 그룹에서 구독을 제거하려면 구독 제거 명령을 사용합니다.

```azurecli-interactive
az account management-group subscription remove --name 'Contoso' --subscription '12345678-1234-1234-1234-123456789012'
```

### <a name="move-subscriptions-in-arm-template"></a>ARM 템플릿에서 구독 이동

ARM 템플릿(Azure Resource Manager 템플릿)에서 구독을 이동하려면 다음 템플릿을 사용하고 [테넌트 수준](../../azure-resource-manager/templates/deploy-to-tenant.md)에 배포합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "targetMgId": {
            "type": "string",
            "metadata": {
                "description": "Provide the ID of the management group that you want to move the subscription to."
            }
        },
        "subscriptionId": {
            "type": "string",
            "metadata": {
                "description": "Provide the ID of the existing subscription to move."
            }
        }
    },
    "resources": [
        {
            "scope": "/",
            "type": "Microsoft.Management/managementGroups/subscriptions",
            "apiVersion": "2020-05-01",
            "name": "[concat(parameters('targetMgId'), '/', parameters('subscriptionId'))]",
            "properties": {
            }
        }
    ],
    "outputs": {}
}
```

## <a name="move-management-groups"></a>관리 그룹 이동

### <a name="move-management-groups-in-the-portal"></a>포털에서 관리 그룹 이동

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. **모든 서비스** > **관리 그룹** 을 선택합니다.

1. 부모로 지정할 관리 그룹을 선택합니다.

1. 페이지 맨 위에서 **관리 그룹 추가** 를 선택합니다.

1. 열리는 메뉴에서 새 관리 그룹을 만들지 또는 기존 관리 그룹을 사용할지를 선택합니다.

   - 새 관리 그룹을 선택하면 새 관리 그룹이 생성됩니다.
   - 기존 관리 그룹을 선택하면 이 관리 그룹으로 이동할 수 있는 모든 관리 그룹의 드롭다운 목록이 표시됩니다.

   :::image type="content" source="./media/add_context_MG.png" alt-text="새 관리 그룹을 만들기 위한 '관리 그룹 추가' 옵션 스크린샷" border="false":::

1. **저장** 을 선택합니다.

### <a name="move-management-groups-in-powershell"></a>PowerShell에서 관리 그룹 이동

PowerShell에서 Update-AzManagementGroup 명령을 사용하여 관리 그룹을 다른 그룹 아래로 이동합니다.

```azurepowershell-interactive
$parentGroup = Get-AzManagementGroup -GroupId ContosoIT
Update-AzManagementGroup -GroupId 'Contoso' -ParentId $parentGroup.id
```

### <a name="move-management-groups-in-azure-cli"></a>Azure CLI에서 관리 그룹 이동

Azure CLI에서 update 명령을 사용하여 관리 그룹을 이동합니다.

```azurecli-interactive
az account management-group update --name 'Contoso' --parent ContosoIT
```

## <a name="audit-management-groups-using-activity-logs"></a>활동 로그를 사용하여 관리 그룹 감사

관리 그룹은 [Azure 활동 로그](../../azure-monitor/essentials/platform-logs-overview.md) 내에서 지원됩니다. 다른 Azure 리소스와 동일한 중앙 위치에서 관리 그룹에 발생하는 모든 이벤트를 쿼리할 수 있습니다. 예를 들어, 특정 관리 그룹에 이루어진 모든 역할 할당 또는 정책 할당 변경 내용을 볼 수 있습니다.

:::image type="content" source="./media/al-mg.png" alt-text="선택한 관리 그룹과 관련된 활동 로그 및 작업의 스크린샷" border="false":::

Azure Portal 외부에서 관리 그룹의 쿼리를 살펴보면 관리 그룹에 대한 대상 범위가 **"/providers/Microsoft.Management/managementGroups/{yourMgID}"** 와 같이 표시됩니다.

## <a name="referencing-management-groups-from-other-resource-providers"></a>다른 리소스 공급자의 관리 그룹 참조

다른 리소스 공급자 작업의 관리 그룹을 참조할 때 다음 경로를 범위로 사용합니다. 이 경로는 PowerShell, Azure CLI 및 REST API를 사용할 때 사용됩니다.

`/providers/Microsoft.Management/managementGroups/{yourMgID}`

예를 들어 PowerShell에서 관리 그룹에 새 역할 할당을 할당하는 경우 이 경로를 사용합니다.

```azurepowershell-interactive
New-AzRoleAssignment -Scope "/providers/Microsoft.Management/managementGroups/Contoso"
```

관리 그룹에서 정책 정의를 검색할 때 동일한 범위 경로를 사용합니다.

```http
GET https://management.azure.com/providers/Microsoft.Management/managementgroups/MyManagementGroup/providers/Microsoft.Authorization/policyDefinitions/ResourceNaming?api-version=2019-09-01
```

## <a name="next-steps"></a>다음 단계

관리 그룹에 대해 자세히 알아보려면 다음 항목을 참조하세요.

- [관리 그룹을 만들어 Azure 리소스 구성](./create-management-group-portal.md)
- [관리 그룹을 변경, 삭제 또는 관리하는 방법](./manage.md)
- [Azure PowerShell 리소스 모듈에서 관리 그룹 검토](/powershell/module/az.resources#resources)
- [REST API에서 관리 그룹 검토](/rest/api/managementgroups/managementgroups)
- [Azure CLI에서 관리 그룹 검토](/cli/azure/account/management-group)
