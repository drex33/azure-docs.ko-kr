---
title: DNS 영역 및 레코드 보호 - Azure DNS
description: 이 학습 경로에서 Microsoft Azure DNS의 DNS 영역과 레코드 집합 보호를 시작합니다.
services: dns
author: duongau
ms.service: dns
ms.topic: how-to
ms.date: 05/05/2021
ms.author: duau
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: d6006d13e0c7d596c659700adab1c171d1d760a0
ms.sourcegitcommit: 331a5c3ad498061511383b80760349ff2a966bcf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/29/2021
ms.locfileid: "133218879"
---
# <a name="how-to-protect-dns-zones-and-records"></a>DNS 영역 및 레코드를 보호하는 방법

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

DNS 영역 및 레코드는 중요한 리소스입니다. DNS 영역 또는 단일 DNS 레코드를 삭제하면 서비스가 중단될 수 있습니다. 따라서 DNS 영역 및 레코드를 무단 변경하거나 실수로 변경하지 않아야 합니다.

이 문서에서는 Azure DNS를 사용하여 프라이빗 DNS 영역 및 레코드의 변경을 방지하는 방법을 설명합니다.  Azure Resource Manager에서 제공하는 두 가지 강력한 보안 기능, 즉 [Azure RBAC(역할 기반 액세스 제어)](../role-based-access-control/overview.md) 및 [리소스 잠금](../azure-resource-manager/management/lock-resources.md)을 적용합니다.

## <a name="azure-role-based-access-control"></a>Azure 역할 기반 액세스 제어

Azure RBAC(역할 기반 액세스 제어)를 사용하면 Azure 사용자, 그룹 및 리소스에 대해 세분화된 액세스 관리가 가능합니다. Azure RBAC를 사용하면 사용자에게 필요한 액세스 수준을 부여할 수 있습니다. Azure RBAC를 사용하여 액세스를 관리하는 방법은 [Azure RBAC(역할 기반 액세스 제어)란?](../role-based-access-control/overview.md)을 참조하세요.

### <a name="the-dns-zone-contributor-role"></a>DNS 영역 기여자 역할

DNS 영역 기여자 역할은 프라이빗 DNS 리소스를 관리하기 위해 기본으로 제공되는 역할입니다. 사용자나 그룹에 적용되는 이 역할을 통해 DNS 리소스 관리가 가능합니다.

리소스 그룹 *myResourceGroup* 에는 Contoso Corporation에 대한 5개의 영역이 포함되어 있습니다. 해당 리소스 그룹에 DNS 영역 기여자 DNS 관리자 권한을 부여할 경우 해당 DNS 영역을 완전히 관리할 수 있습니다. 불필요한 권한 부여를 방지합니다. DNS 관리자가 가상 머신을 만들거나 중지할 수 없습니다.

Azure RBAC 권한을 할당하는 가장 간단한 방법은 [Azure Portal을 사용](../role-based-access-control/role-assignments-portal.md)하는 것입니다.  

리소스 그룹에 대한 **Access control(IAM)** 을 열고 **+ 추가** 를 선택한 다음, **DNS 영역 기여자** 역할을 선택합니다. 필요한 사용자 또는 그룹을 선택하여 권한을 부여합니다.

:::image type="content" source="./media/dns-protect-zones-recordsets/resource-group-rbac.png" alt-text="리소스 그룹에 대한 액세스 제어 페이지의 스크린샷":::

권한은 [Azure PowerShell을 사용하여 부여](../role-based-access-control/role-assignments-powershell.md)할 수도 있습니다.

```azurepowershell-interactive
# Grant 'DNS Zone Contributor' permissions to all zones in a resource group

$usr = "<user email address>"
$rol = "DNS Zone Contributor"
$rsg = "<resource group name>"

New-AzRoleAssignment -SignInName $usr -RoleDefinitionName $rol -ResourceGroupName $rsg
```

동일한 명령을 [Azure CLI를 통해 사용](../role-based-access-control/role-assignments-cli.md)할 수도 있습니다.

```azurecli-interactive
# Grant 'DNS Zone Contributor' permissions to all zones in a resource group

az role assignment create \
--assignee "<user email address>" \
--role "DNS Zone Contributor" \
--resource-group "<resource group name>"
```

### <a name="zone-level-azure-rbac"></a>영역 수준 Azure RBAC

구독, 리소스 그룹 또는 개별 리소스에 Azure RBAC 규칙을 적용할 수 있습니다. 해당 리소스는 개별 DNS 영역 또는 개별 레코드 집합이 될 수 있습니다.

예를 들어 리소스 그룹 *myResourceGroup* 에는 *contoso.com* 영역과 *customers.contoso.com* 하위 영역이 포함됩니다. CNAME 레코드는 각 고객 계정에 대해 생성됩니다. CNAME 레코드를 관리하는 데 사용되는 관리자 계정에는 *customers.contoso.com* 영역에 레코드를 만들 수 있는 권한이 할당됩니다. 이 계정은 *customers.contoso.com* 만 관리할 수 있습니다.

영역 수준 Azure RBAC 권한은 Azure Portal을 통해 부여할 수 있습니다.  해당 영역의 **Access Control(IAM)** 을 열고, **+ 추가** 를 선택한 다음, **DNS 영역 기여자** 역할을 선택하고 권한을 부여할 사용자 또는 그룹을 선택합니다.

:::image type="content" source="./media/dns-protect-zones-recordsets/zone-rbac.png" alt-text="DNS 영역에 대한 액세스 제어 페이지의 스크린샷":::

권한은 [Azure PowerShell을 사용하여 부여](../role-based-access-control/role-assignments-powershell.md)할 수도 있습니다.

```azurepowershell-interactive
# Grant 'DNS Zone Contributor' permissions to a specific zone

$usr = "<user email address>"
$rol = "DNS Zone Contributor"
$rsg = "<resource group name>"
$zon = "<zone name>"
$typ = "Microsoft.Network/DNSZones"

New-AzRoleAssignment -SignInName $usr -RoleDefinitionName $rol -ResourceGroupName $rsg -ResourceName $zon -ResourceType $typ
```

동일한 명령을 [Azure CLI를 통해 사용](../role-based-access-control/role-assignments-cli.md)할 수도 있습니다.

```azurecli-interactive
# Grant 'DNS Zone Contributor' permissions to a specific zone

az role assignment create \
--assignee <user email address> \
--role "DNS Zone Contributor" \
--scope "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/DnsZones/<zone name>/"
```

### <a name="record-set-level-azure-rbac"></a>레코드 집합 수준 Azure RBAC

권한은 레코드 집합 수준에서 적용됩니다.  사용자에게 필요한 항목에 대한 제어 권한이 부여되고 다른 사항은 변경할 수 없습니다.

Azure Portal에서 레코드 집합 페이지의 **사용자** 단추를 사용하여 레코드 집합 수준 Azure RBAC 권한을 구성할 수 있습니다.

:::image type="content" source="./media/dns-protect-zones-recordsets/record-set-rbac-1.png" alt-text="레코드 집합에 있는 사용자 단추의 스크린샷":::

레코드 집합 수준 Azure RBAC 권한은 [Azure PowerShell을 사용하여 부여](../role-based-access-control/role-assignments-powershell.md)할 수도 있습니다.

```azurepowershell-interactive
# Grant permissions to a specific record set

$usr = "<user email address>"
$rol = "DNS Zone Contributor"
$sco = 
"/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/dnszones/<zone name>/<record type>/<record name>"

New-AzRoleAssignment -SignInName $usr -RoleDefinitionName $rol -Scope $sco
```

동일한 명령을 [Azure CLI를 통해 사용](../role-based-access-control/role-assignments-cli.md)할 수도 있습니다.

```azurecli-interactive
# Grant permissions to a specific record set

az role assignment create \
--assignee "<user email address>" \
--role "DNS Zone Contributor" \
--scope "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/dnszones/<zone name>/<record type>/<record name>"
```

### <a name="custom-roles"></a>사용자 지정 역할

기본 제공 DNS 영역 기여자 역할을 사용하면 DNS 리소스를 완벽히 관리할 수 있습니다. 세분화된 제어를 제공하기 위해 자체적인 사용자 지정 Azure 역할을 빌드할 수 있습니다.

CNAME을 관리하는 데 사용되는 계정에는 CNAME 레코드만 관리할 수 있는 권한이 부여됩니다. 계정은 다른 형식의 레코드를 수정할 수 없습니다. 계정은 영역 삭제와 같은 영역 수준 작업을 수행할 수 없습니다.

다음 예는 CNAME 레코드 관리에 대한 사용자 지정 역할 정의를 보여줍니다.

```json
{
    "Name": "DNS CNAME Contributor",
    "Id": "",
    "IsCustom": true,
    "Description": "Can manage DNS CNAME records only.",
    "Actions": [
        "Microsoft.Network/dnsZones/CNAME/*",
        "Microsoft.Network/dnsZones/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
    ],
    "NotActions": [
    ],
    "AssignableScopes": [
        "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ]
}
```

작업 속성은 다음과 같은 DNS별 권한을 정의합니다.

* `Microsoft.Network/dnsZones/CNAME/*`은 CNAME 레코드에 대한 모든 권한을 부여합니다.
* `Microsoft.Network/dnsZones/read`는 DNS 영역을 읽을 권한을 부여하지만 수정 권한은 부여하지 않아 CNAME이 생성되는 영역을 확인할 수 있습니다.

나머지 작업은 [DNS 영역 참가자의 기본 역할](../role-based-access-control/built-in-roles.md#dns-zone-contributor)에서 복사됩니다.

> [!NOTE]
> 레코드 집합 삭제를 방지하기 위해 Azure 사용자 지정 역할을 사용하면서 업데이트를 허용하는 것은 효과적인 제어가 아닙니다. 이 방식에서는 레코드 집합을 삭제할 수 없지만 수정은 방지할 수 없습니다.  허용되는 수정 작업에는 레코드 집합을 비우기 위해 레코드를 모두 제거하는 작업을 포함하여 레코드 집합에서 레코드를 추가 및 제거하는 작업이 포함됩니다. 이 경우 DNS 확인 관점에서 레코드 집합을 삭제하는 것과 같은 효과를 가집니다.

사용자 지정 역할 정의는 현재 Azure Portal을 통해 정의할 수 없습니다. 이 역할 정의를 기준으로 하는 사용자 지정 역할은 Azure PowerShell을 사용하여 만들 수 있습니다.

```azurepowershell-interactive
# Create new role definition based on input file
New-AzRoleDefinition -InputFile <file path>
```

Azure CLI를 통해 만들 수도 있습니다.

```azurecli-interactive
# Create new role definition based on input file
az role defination create --role-definition <file path>
```

그런 다음 이 문서의 앞부분에서 설명한 대로 역할을 기본 역할과 동일한 방식으로 할당할 수 있습니다.

사용자 지정 역할의 생성, 관리, 할당에 대한 자세한 내용은 [Azure 사용자 지정 역할](../role-based-access-control/custom-roles.md)을 참조하세요.

## <a name="resource-locks"></a>리소스 잠금

Azure Resource Manager는 리소스 잠금 기능과 같은 다른 유형의 보안 제어를 지원합니다. 리소스 잠금은 리소스에 적용되고 모든 사용자 및 역할에서 효과적입니다. 자세한 내용은 [Azure 리소스 관리자를 사용하여 리소스 잠그기](../azure-resource-manager/management/lock-resources.md)를 참조하세요.

리소스 잠금에는 **CanNotDelete** 및 **ReadOnly** 의 두 가지 유형이 있습니다. 이러한 잠금 유형은 프라이빗 DNS 영역 또는 개별 레코드 집합에 적용할 수 있습니다. 다음 섹션에서는 몇 가지 일반적인 시나리오와 리소스 잠금을 사용하여 지원하는 방법에 대해 설명합니다.

### <a name="protecting-against-all-changes"></a>모든 변경으로부터 보호

변경을 방지하려면 해당 영역에 ReadOnly 잠금을 적용합니다. 잠금 기능을 적용하면 새 레코드 집합의 생성을 방지하고 기존 레코드 집합의 수정이나 삭제를 방지합니다.

Azure Portal을 통해 영역 수준의 리소스 잠금을 만들 수 있습니다. DNS 영역 페이지에서 **잠금** 을 선택한 다음, **+ 추가** 를 선택합니다.

:::image type="content" source="./media/dns-protect-zones-recordsets/zone-locks.png" alt-text="영역 수준 리소스 잠금의 스크린샷":::

또한 [Azure PowerShell](/powershell/module/az.resources/new-azresourcelock)을 통해 영역 수준 리소스 잠금이 가능합니다.

```azurepowershell-interactive
# Lock a DNS zone

$lvl = "<lock level>"
$lnm = "<lock name>"
$rsc = "<zone name>"
$rty = "Microsoft.Network/DNSZones"
$rsg = "<resource group name>"

New-AzResourceLock -LockLevel $lvl -LockName $lnm -ResourceName $rsc -ResourceType $rty -ResourceGroupName $rsg
```

동일한 명령을 [Azure CLI를 통해 사용](/cli/azure/lock#az_lock_create)할 수도 있습니다.

```azurecli-interactive
# Lock a DNS zone

az lock create \
--lock-type "<lock level>" \
--name "<lock name>" \
--resource-name "<zone name>" \
--namespace "Microsoft.Network" \
--resource-type "DnsZones" \
--resource-group "<resource group name>"
```

### <a name="protecting-individual-records"></a>개별 레코드 보호

기존 DNS 레코드 집합이 수정되지 않도록 하려면 레코드 집합에 ReadOnly 잠금을 적용합니다.

> [!NOTE]
> 레코드 집합에 CanNotDelete 잠금을 적용하는 것은 효과적인 관리 방법이 아닙니다. 이 방식에서는 레코드 집합을 삭제할 수 없지만 수정은 방지할 수 없습니다.  허용되는 수정 작업에는 레코드 집합을 비우기 위해 레코드를 모두 제거하는 작업을 포함하여 레코드 집합에서 레코드를 추가 및 제거하는 작업이 포함됩니다. 이 경우 DNS 확인 관점에서 레코드 집합을 삭제하는 것과 같은 효과를 가집니다.

레코드 집합 수준 리소스 잠금은 현재 Azure PowerShell을 사용해야 구성할 수 있으며  Azure Portal 또는 Azure CLI에서 지원되지 않습니다.

```azurepowershell-interactive
# Lock a DNS record set

$lvl = "<lock level>"
$lnm = "<lock name>"
$rsc = "<zone name>/<record set name>"
$rty = "Microsoft.Network/DNSZones/<record type>"
$rsg = "<resource group name>"

New-AzResourceLock -LockLevel $lvl -LockName $lnm -ResourceName $rsc -ResourceType $rty -ResourceGroupName $rsg
```

### <a name="protecting-against-zone-deletion"></a>영역 삭제로부터 보호

Azure DNS에서 영역이 삭제되면 해당 영역에 있는 모든 레코드 집합이 삭제됩니다.  이 작업은 실행 취소할 수 없습니다. 중요 영역을 실수로 삭제할 경우 비즈니스에 심각한 영향을 미칩니다.  따라서 실수로 영역을 삭제하지 않도록 보호하는 것이 중요합니다.

영역에 CanNotDelete 잠금을 적용할 경우 영역이 삭제되지 않습니다. 잠금은 자식 리소스에 의해 상속됩니다. 잠금은 해당 영역에 있는 레코드 집합이 삭제되는 것을 방지합니다. 위 참고 사항에서 설명한 바와 같이 기존 레코드 집합에서 여전히 레코드가 제거될 수 있기 때문에 이는 효과적이지 않습니다.

이를 대신하여 해당 영역의 레코드 집합(예: SOA 레코드 집합)에 CanNotDelete 잠금을 적용합니다. 레코드 집합을 삭제하지 않으면 영역이 삭제되지 않습니다. 이 잠금은 영역이 삭제되지 않도록 보호하는 동시에 영역 내의 레코드 집합을 자유롭게 수정할 수 있도록 합니다. 영역을 삭제하려고 시도하면 Azure Resource Manager가 이 제거를 감지합니다. 또한 제거하면 SOA 레코드 집합도 삭제될 수 있으며, SOA가 잠겨 있으므로 Azure Resource Manager가 호출을 차단합니다.  삭제되는 레코드 집합이 없습니다.

다음 PowerShell 명령은 지정된 영역의 SOA 레코드에 대해 CanNotDelete 잠금을 만듭니다.

```azurepowershell-interactive
# Protect against zone delete with CanNotDelete lock on the record set

$lvl = "CanNotDelete"
$lnm = "<lock name>"
$rsc = "<zone name>/@"
$rty = "Microsoft.Network/DNSZones/SOA"
$rsg = "<resource group name>"

New-AzResourceLock -LockLevel $lvl -LockName $lnm -ResourceName $rsc -ResourceType $rty -ResourceGroupName $rsg
```

실수로 인한 영역 삭제를 방지하는 또 다른 옵션은 사용자 지정 역할을 사용하는 것입니다. 이 역할은 영역을 관리하는 데 사용되는 계정이 영역 삭제 권한을 가지지 않도록 합니다. 

영역을 삭제해야 하는 경우 다음과 같이 2단계 삭제를 적용할 수 있습니다.

 - 먼저 영역 삭제 권한을 부여합니다.
 - 그런 다음, 영역을 삭제할 수 있는 권한을 부여합니다.

사용자 지정 역할은 해당 계정에서 액세스하는 모든 영역에 대해 작동합니다. 구독 소유자와 같이 영역 삭제 권한이 있는 계정은 여전히 영역을 실수로 삭제할 수 있습니다.

DNS 영역 보호에 대한 심층 방어 접근 방식으로 두 가지 접근 방식인 리소스 잠금과 사용자 지정 역할을 동시에 사용할 수도 있습니다.

## <a name="next-steps"></a>다음 단계

* Azure RBAC 사용에 대한 자세한 내용은 [Azure RBAC(역할 기반 액세스 제어)란?](../role-based-access-control/overview.md)을 참조하세요.
* 리소스 잠금에 대한 자세한 내용은[ Azure Resource Manager를 사용하여 리소스 잠그기](../azure-resource-manager/management/lock-resources.md)를 참조하세요.
