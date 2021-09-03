---
title: 새 구독 또는 리소스 그룹으로 리소스 이동
description: Azure Resource Manager를 사용하여 리소스를 새 리소스 그룹 또는 구독으로 이동합니다.
ms.topic: conceptual
ms.date: 06/03/2021
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: ec23b4306f088328bfb72f3cf9071a70f8eb2307
ms.sourcegitcommit: b5508e1b38758472cecdd876a2118aedf8089fec
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/09/2021
ms.locfileid: "113586782"
---
# <a name="move-resources-to-a-new-resource-group-or-subscription"></a>리소스를 새 리소스 그룹 또는 구독으로 이동

이 문서에서는 Azure 리소스를 다른 Azure 구독 또는 동일한 구독의 다른 리소스 그룹으로 이동하는 방법을 보여 줍니다. Azure Portal, Azure PowerShell, Azure CLI 또는 REST API를 사용하여 리소스를 이동할 수 있습니다.

이동 작업 동안 원본 그룹과 대상 그룹 모두 잠겨 있습니다. 쓰기 및 삭제 작업은 이동이 완료될 때까지 리소스 그룹에서 차단됩니다. 이 잠금은 리소스 그룹에서 리소스를 추가, 업데이트 또는 삭제할 수 없음을 의미합니다. 리소스가 고정된 것을 의미하지는 않습니다. 예를 들어 Azure SQL 논리 서버 및 해당 데이터베이스를 새 리소스 그룹 또는 구독으로 이동할 경우 이 데이터베이스를 사용하는 애플리케이션에 가동 중지 시간이 발생하지 않습니다. 여전히 데이터베이스에 대해 읽기 및 쓰기를 수행할 수 있습니다. 잠금은 최대 4시간까지 지속될 수 있지만 대부분의 이동 작업이 훨씬 적은 시간에 완료됩니다.

리소스를 이동할 때는 새 리소스 그룹 또는 구독으로만 이동됩니다. 리소스의 위치는 변경하지 않습니다.

## <a name="changed-resource-id"></a>변경된 리소스 ID

리소스를 이동하면 해당 리소스 ID가 변경됩니다. 리소스 ID의 표준 형식은 `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}`입니다. 리소스를 새 리소스 그룹 또는 구독으로 이동할 때는 해당 경로에서 하나 이상의 값을 변경합니다.

리소스 ID를 모든 곳에 사용할 경우 해당 값을 변경해야 합니다. 예를 들어 포터에서 리소스 ID를 참조하는 [사용자 지정 대시보드](../../azure-portal/quickstart-portal-dashboard-azure-cli.md)가 있는 경우 해당 값을 업데이트해야 합니다. 새 리소스 ID에 대해 업데이트해야 하는 스크립트 또는 템플릿을 찾아봅니다.

## <a name="checklist-before-moving-resources"></a>리소스를 이동하기 전의 검사 목록

리소스를 이동하기 전에 몇 가지 중요한 단계가 있습니다. 이러한 조건을 확인하면 오류를 방지할 수 있습니다.

1. 이동하려는 리소스는 이동 작업을 지원해야 합니다. 이동을 지원하는 리소스 목록은 [리소스에 대한 이동 작업 지원](move-support-resources.md)을 참조하세요.

1. 일부 서비스에는 리소스를 이동할 때 특정 제한 또는 요구 사항이 있습니다. 다음 서비스를 이동할 경우 이동하기 전 안내를 확인하세요.

   * Azure Stack Hub를 사용하는 경우 그룹 간에 리소스를 이동할 수 없습니다.
   * [App Services 이동 지침](./move-limitations/app-service-move-limitations.md)
   * [Azure DevOps Services 이동 지침](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json)
   * [클래식 배포 모델 이동 지침](./move-limitations/classic-model-move-limitations.md) - 클래식 계산, 클래식 스토리지, 클래식 가상 네트워크 및 클라우드 서비스
   * [네트워킹 이동 지침](./move-limitations/networking-move-limitations.md)
   * [복구 서비스 이동 지침](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)
   * [가상 머신 이동 지침](./move-limitations/virtual-machines-move-limitations.md)
   * Azure 구독을 새 관리 그룹으로 이동하려면 [구독 이동](../../governance/management-groups/manage.md#move-subscriptions)을 참조하세요.

1. Azure 역할이 할당된 리소스를 리소스(또는 자식 리소스)로 직접 이동시키는 경우 역할 할당은 이동되지 않고 분리됩니다. 이동 후에 역할 할당을 다시 만들어야 합니다. 결국 분리된 역할 할당이 자동으로 제거되지만 이동하기 전 역할 할당을 제거하는 것이 좋습니다.

    역할 할당 관리 방법에 대한 자세한 내용은 [Azure 역할 할당 나열](../../role-based-access-control/role-assignments-list-portal.md#list-role-assignments-at-a-scope) 및 [Azure 역할 할당](../../role-based-access-control/role-assignments-portal.md)을 참조하세요.

1. 원본 및 대상 구독이 활성 상태여야 합니다. 사용하지 않도록 설정된 계정을 사용하도록 설정하는 과정에서 문제가 발생하면 [Azure 지원 요청을 작성](../../azure-portal/supportability/how-to-create-azure-support-request.md)하세요. 문제 유형으로 **구독 관리** 를 선택합니다.

1. 원본 및 대상 구독은 동일한 [Azure Active Directory 테넌트](../../active-directory/develop/quickstart-create-new-tenant.md) 내에 있어야 합니다. 두 구독이 모두 동일한 테넌트 ID를 갖는지 확인하려면 Azure PowerShell 또는 Azure CLI를 사용합니다.

   Azure PowerShell의 경우 다음을 사용합니다.

   ```azurepowershell-interactive
   (Get-AzSubscription -SubscriptionName <your-source-subscription>).TenantId
   (Get-AzSubscription -SubscriptionName <your-destination-subscription>).TenantId
   ```

   Azure CLI의 경우

   ```azurecli-interactive
   az account show --subscription <your-source-subscription> --query tenantId
   az account show --subscription <your-destination-subscription> --query tenantId
   ```

   원본 및 대상 구독에 대한 테넌트 ID가 다른 경우 다음 메서드를 사용하여 테넌트 ID를 조정합니다.

   * [Azure 구독의 소유권을 다른 계정으로 이전](../../cost-management-billing/manage/billing-subscription-transfer.md)
   * [Azure Active Directory에 Azure 구독을 연결하거나 추가하는 방법](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)

1. 이동되는 리소스의 리소스 공급자가 대상 구독에 등록되어야 합니다. 그러지 않으면 **구독이 리소스 형식에 대해 등록되지 않았음** 을 알리는 오류 메시지가 표시됩니다. 해당 리소스 종류와 함께 사용된 적이 없는 새 구독으로 리소스를 이동할 때 이 오류가 표시될 수 있습니다.

   PowerShell의 경우 다음 명령을 사용하여 등록 상태를 가져옵니다.

   ```azurepowershell-interactive
   Set-AzContext -Subscription <destination-subscription-name-or-id>
   Get-AzResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState
   ```

   리소스 공급자를 등록하려면 다음을 사용합니다.

   ```azurepowershell-interactive
   Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
   ```

   Azure CLI의 경우 다음 명령을 사용하여 등록 상태를 가져옵니다.

   ```azurecli-interactive
   az account set -s <destination-subscription-name-or-id>
   az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
   ```

   리소스 공급자를 등록하려면 다음을 사용합니다.

   ```azurecli-interactive
   az provider register --namespace Microsoft.Batch
   ```

1. 리소스를 이동시키는 계정에는 적어도 다음과 같은 권한이 있어야 합니다.

   * 원본 리소스 그룹에 대한 **Microsoft.Resources/subscriptions/resourceGroups/moveResources/action**
   * 대상 리소스 그룹에 대한 **Microsoft.Resources/subscriptions/resourceGroups/write**

1. 리소스를 이동하기 전에 리소스를 이동하려는 구독에 대한 구독 할당량을 확인합니다. 리소스 이동 시 구독이 해당 한계를 초과하는 경우 할당량 증가를 요청할 수 있는지 여부를 검토해야 합니다. 제한의 목록 및 증가 요청 방법은 [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](../../azure-resource-manager/management/azure-subscription-service-limits.md)을 참조하세요.

1. **구독 간 이동을 위해 리소스 및 해당 종속 리소스는 동일한 리소스 그룹에 있어야 하며, 함께 이동되어야 합니다.** 예를 들어 관리 디스크가 있는 VM은 다른 종속 리소스와 함께 VM 및 관리 디스크를 한 번에 이동해야 합니다.

   리소스를 새 구독으로 이동할 경우 리소스에 종속 리소스가 포함되었는지 여부 그리고 동일한 리소스 그룹에 있는지 여부를 확인합니다. 리소스가 동일한 리소스 그룹에 없으면 리소스를 동일한 리소스 그룹에 결합할 수 있는지 여부를 확인합니다. 결합할 수 있으면 리소스 그룹 간에 이동 작업을 수행하여 모든 리소스를 동일한 리소스 그룹으로 가져옵니다.

   자세한 내용은 [구독 간 이동 시나리오](#scenario-for-move-across-subscriptions)를 참조하세요.

## <a name="scenario-for-move-across-subscriptions"></a>구독 간 이동 시나리오

한 구독에서 다른 구독으로 리소스를 이동하는 과정은 3단계로 구성되는 프로세스입니다.

![구독 간 이동 시나리오](./media/move-resource-group-and-subscription/cross-subscription-move-scenario.png)

설명을 위해 여기에서는 하나의 종속 리소스만 포함되었습니다.

* 1단계: 종속 리소스가 서로 다른 리소스 그룹 간에 분산된 경우 먼저 이를 하나의 리소스 그룹으로 이동합니다.
* 2단계: 소스 구독에서 대상 구독으로 리소스 및 종속 리소스를 함께 이동합니다.
* 3단계: 선택적으로 대상 구독 내에서 종속 리소스를 여러 리소스 그룹으로 다시 배포합니다.

## <a name="use-the-portal"></a>포털 사용

리소스를 이동하려면 해당 리소스가 포함된 리소스 그룹을 선택합니다.

이동하려는 리소스 선택 모든 리소스를 이동하려면 목록 위쪽에서 확인란을 선택합니다. 또는 리소스를 개별적으로 선택합니다.

:::image type="content" source="./media/move-resource-group-and-subscription/select-resources-to-move.png" alt-text="리소스 선택":::

**이동** 단추를 선택합니다.

:::image type="content" source="./media/move-resource-group-and-subscription/select-move.png" alt-text="이동 옵션":::

이 단추는 세 가지 옵션을 제공합니다.

* 새 리소스 그룹으로 이동합니다.
* 새 구독으로 이동합니다.
* 새 지역으로 이동합니다. 지역을 변경하려면 [지역 간 리소스 이동(리소스 그룹에서)](../../resource-mover/move-region-within-resource-group.md?toc=/azure/azure-resource-manager/management/toc.json)을 참조하세요.

리소스를 새 리소스 그룹으로 이동할지 또는 새 구독으로 이동할지를 선택합니다.

원본 리소스 그룹이 자동으로 설정됩니다. 대상 리소스 그룹을 지정합니다. 새 구독으로 이동하는 경우 구독도 지정합니다. **다음** 을 선택합니다.

:::image type="content" source="./media/move-resource-group-and-subscription/select-destination-group.png" alt-text="대상 리소스 그룹 선택":::

포털에서 리소스를 이동할 수 있는지 유효성을 검사합니다. 유효성 검사가 완료될 때까지 기다립니다.

:::image type="content" source="./media/move-resource-group-and-subscription/validation.png" alt-text="이동 유효성 검사":::

유효성 검사가 성공적으로 완료되면 **다음** 을 선택합니다.

이 리소스에 대해 도구와 스크립트를 업데이트해야 함을 승인합니다. 리소스 이동을 시작하려면 **이동** 을 선택합니다.

:::image type="content" source="./media/move-resource-group-and-subscription/acknowledge-change.png" alt-text="대상 선택":::

이동이 완료되면 결과가 알림으로 제공됩니다.

:::image type="content" source="./media/move-resource-group-and-subscription/view-notification.png" alt-text="이동 결과 보기":::

## <a name="use-azure-powershell"></a>Azure PowerShell 사용

### <a name="validate"></a>유효성 검사

실제로 리소스를 이동하지 않고 이동 시나리오를 테스트하려면 [Invoke-AzResourceAction](/powershell/module/az.resources/invoke-azresourceaction) 명령을 사용합니다. 이 명령은 결과를 미리 확인해야 할 경우에만 사용합니다. 이 작업을 실행하려면 다음이 필요합니다.

* 원본 리소스 그룹의 리소스 ID
* 대상 리소스 그룹의 리소스 ID
* 이동할 각 리소스의 리소스 ID

```azurepowershell
Invoke-AzResourceAction -Action validateMoveResources `
-ResourceId "/subscriptions/{subscription-id}/resourceGroups/{source-rg}" `
-Parameters @{ resources= @("/subscriptions/{subscription-id}/resourceGroups/{source-rg}/providers/{resource-provider}/{resource-type}/{resource-name}", "/subscriptions/{subscription-id}/resourceGroups/{source-rg}/providers/{resource-provider}/{resource-type}/{resource-name}", "/subscriptions/{subscription-id}/resourceGroups/{source-rg}/providers/{resource-provider}/{resource-type}/{resource-name}");targetResourceGroup = '/subscriptions/{subscription-id}/resourceGroups/{destination-rg}' }  
```

유효성 검사를 통과하면 출력이 표시되지 않습니다.

유효성 검사에 실패하면 리소스를 이동할 수 없는 이유를 설명하는 오류 메시지가 표시됩니다.

### <a name="move"></a>이동

다른 리소스 그룹 또는 구독에 기존 리소스를 이동하려면 [Move-AzResource](/powershell/module/az.resources/move-azresource) 명령을 사용합니다. 다음 예제에서는 여러 리소스를 새 리소스 그룹으로 이동하는 방법을 보여 줍니다.

```azurepowershell-interactive
$webapp = Get-AzResource -ResourceGroupName OldRG -ResourceName ExampleSite
$plan = Get-AzResource -ResourceGroupName OldRG -ResourceName ExamplePlan
Move-AzResource -DestinationResourceGroupName NewRG -ResourceId $webapp.ResourceId, $plan.ResourceId
```

새 구독으로 이동하려면 `DestinationSubscriptionId` 매개 변수 값을 포함합니다.

## <a name="use-azure-cli"></a>Azure CLI 사용

### <a name="validate"></a>유효성 검사

실제로 리소스를 이동하지 않고 이동 시나리오를 테스트하려면 [az resource invoke-action](/cli/azure/resource#az_resource_invoke_action) 명령을 사용합니다. 이 명령은 결과를 미리 확인해야 할 경우에만 사용합니다. 이 작업을 실행하려면 다음이 필요합니다.

* 원본 리소스 그룹의 리소스 ID
* 대상 리소스 그룹의 리소스 ID
* 이동할 각 리소스의 리소스 ID

요청 본문에서 `\"`를 사용하여 큰따옴표를 이스케이프합니다.

```azurecli
az resource invoke-action --action validateMoveResources \
  --ids "/subscriptions/{subscription-id}/resourceGroups/{source-rg}" \
  --request-body "{  \"resources\": [\"/subscriptions/{subscription-id}/resourceGroups/{source-rg}/providers/{resource-provider}/{resource-type}/{resource-name}\", \"/subscriptions/{subscription-id}/resourceGroups/{source-rg}/providers/{resource-provider}/{resource-type}/{resource-name}\", \"/subscriptions/{subscription-id}/resourceGroups/{source-rg}/providers/{resource-provider}/{resource-type}/{resource-name}\"],\"targetResourceGroup\":\"/subscriptions/{subscription-id}/resourceGroups/{destination-rg}\" }" 
```

유효성 검사를 통과하면 다음이 표시됩니다.

```azurecli
{} Finished .. 
```

유효성 검사에 실패하면 리소스를 이동할 수 없는 이유를 설명하는 오류 메시지가 표시됩니다.

### <a name="move"></a>이동

기존 리소스를 다른 리소스 그룹 또는 구독으로 이동하려면 [az resource move](/cli/azure/resource#az_resource_move) 명령을 사용합니다. 이동할 리소스에 대한 리소스 ID를 제공합니다. 다음 예제에서는 여러 리소스를 새 리소스 그룹으로 이동하는 방법을 보여 줍니다. `--ids` 매개 변수에서 이동할 리소스 ID를 쉼표로 구분한 목록을 제공합니다.

```azurecli
webapp=$(az resource show -g OldRG -n ExampleSite --resource-type "Microsoft.Web/sites" --query id --output tsv)
plan=$(az resource show -g OldRG -n ExamplePlan --resource-type "Microsoft.Web/serverfarms" --query id --output tsv)
az resource move --destination-group newgroup --ids $webapp $plan
```

새 구독으로 이동하려면 `--destination-subscription-id` 매개 변수를 제공합니다.

## <a name="use-rest-api"></a>REST API 사용

### <a name="validate"></a>유효성 검사

[이동 작업 유효성 검사](/rest/api/resources/resources/validate-move-resources)를 수행하면 실제로 리소스를 이동하지 않고 이동 시나리오를 테스트할 수 있습니다. 이 작업을 사용하여 이동이 성공했는지 확인합니다. 이동 요청을 보내면 유효성 검사가 자동으로 호출됩니다. 이 작업은 결과를 미리 확인해야 할 경우에만 수행합니다. 이 작업을 실행하려면 다음이 필요합니다.

* 원본 리소스 그룹의 이름
* 대상 리소스 그룹의 리소스 ID
* 이동할 각 리소스의 리소스 ID
* 계정에 대한 [액세스 토큰](/rest/api/azure/#acquire-an-access-token)

다음 요청을 보냅니다.

```HTTP
POST https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<source-group>/validateMoveResources?api-version=2019-05-10
Authorization: Bearer <access-token>
Content-type: application/json
```

다음 요청 본문을 사용합니다.

```json
{
 "resources": ["<resource-id-1>", "<resource-id-2>"],
 "targetResourceGroup": "/subscriptions/<subscription-id>/resourceGroups/<target-group>"
}
```

요청 서식이 올바르게 지정되면 작업은 다음을 반환합니다.

```HTTP
Response Code: 202
cache-control: no-cache
pragma: no-cache
expires: -1
location: https://management.azure.com/subscriptions/<subscription-id>/operationresults/<operation-id>?api-version=2018-02-01
retry-after: 15
...
```

202 상태 코드는 유효성 검사 요청이 수락되었음을 나타내지만 이동 작업의 성공 여부는 아직 확인하지 않은 것입니다. `location` 값에는 장기 실행 작업의 상태를 확인하는 데 사용하는 URL이 포함되어 있습니다.

상태를 확인하려면 다음 요청을 보냅니다.

```HTTP
GET <location-url>
Authorization: Bearer <access-token>
```

작업이 실행되는 동안에는 202 상태 코드가 계속 수신됩니다. 다시 시도하기 전에 `retry-after` 값에 지정된 시간(초) 동안 대기합니다. 이동 작업 유효성 검사가 성공적으로 수행되면 204 상태 코드가 수신됩니다. 이동 유효성 검사가 실패할 경우 다음과 같은 오류 메시지가 수신됩니다.

```json
{"error":{"code":"ResourceMoveProviderValidationFailed","message":"<message>"...}}
```

### <a name="move"></a>이동

기존 리소스를 다른 리소스 그룹 또는 구독으로 이동하려면 [리소스 이동](/rest/api/resources/resources/moveresources) 작업을 수행합니다.

```HTTP
POST https://management.azure.com/subscriptions/{source-subscription-id}/resourcegroups/{source-resource-group-name}/moveResources?api-version={api-version}
```

요청 본문에서 대상 리소스 그룹 및 이동할 리소스를 지정합니다.

```json
{
 "resources": ["<resource-id-1>", "<resource-id-2>"],
 "targetResourceGroup": "/subscriptions/<subscription-id>/resourceGroups/<target-group>"
}
```

## <a name="frequently-asked-questions"></a>질문과 대답

**질문: 일반적으로 몇 분 정도만 걸리는 내 리소스 이동 작업이 거의 1시간 동안 실행되고 있습니다. 문제가 있나요?**

리소스 이동은 여러 단계가 포함된 복잡한 작업입니다. 이동하려는 리소스의 리소스 공급자보다 많은 것이 관련될 수 있습니다. 리소스 공급자 간의 종속성 때문에 Azure Resource Manager는 작업 완료까지 4시간을 허용합니다. 이 기간을 통해 리소스 공급자는 일시적인 문제로부터 복구할 수 있는 기회를 얻습니다. 이동을 요청하고 나서 아직 4시간이 지나지 않았으면 작업이 완료될 때까지 계속 실행되고 결국 성공할 수 있습니다. 이 시간 동안은 일관성 문제를 방지하기 위해 소스 및 대상 리소스 그룹이 잠깁니다.

**질문: 리소스 이동 중 내 리소스 그룹이 4시간 동안 잠기는 이유는 무엇인가요?**

이동 요청은 완료될 때까지 최대 4시간 동안 허용됩니다. 이동 중인 리소스가 수정되는 것을 방지하기 위해 리소스 이동 중 소스 및 대상 리소스 그룹이 모두 잠깁니다.

이동 요청에는 두 단계가 있습니다. 첫 번째 단계에서는 리소스가 이동됩니다. 두 번째 단계에서는 이동 중인 리소스에 종속된 다른 리소스 공급자로 알림이 전송됩니다. 리소스 공급자가 어느 단계에서든 실패하면 전체 4시간 동안 리소스 그룹이 잠길 수 있습니다. 허용된 시간 동안 Resource Manager는 실패한 단계를 다시 시도합니다.

4시간 내에 리소스를 이동할 수 없으면 Resource Manager가 리소스 그룹을 모두 잠금 해제합니다. 성공적으로 이동한 리소스는 대상 리소스 그룹에 포함됩니다. 이동하지 못한 리소스는 소스 리소스 그룹에 남아 있습니다.

**질문: 리소스 이동 중 소스 및 대상 리소스 그룹이 잠기면 어떤 영향이 있나요?**

이러한 잠금은 사용자가 어느 리소스 그룹을 삭제하지 않거나, 리소스 그룹에 새 리소스를 만들거나, 이동에 관여된 리소스를 삭제하지 않도록 방지합니다.

다음 이미지는 사용자가 이동 중인 리소스 그룹을 삭제하려고 시도할 때 Azure Portal에서 표시되는 오류 메시지를 보여줍니다.

![이동 중 삭제 시도 오류 메시지](./media/move-resource-group-and-subscription/move-error-delete.png)

**질문: "MissingMoveDependentResources" 오류 코드는 무엇을 의미하나요?**

리소스를 이동할 때 해당 종속 리소스가 대상 리소스 그룹 또는 구독에 존재하거나 이동 요청에 포함되어 있어야 합니다. 종속 리소스가 이 요구 사항을 충족하지 못하면 MissingMoveDependentResources 오류 코드가 표시됩니다. 이 오류 메시지에는 이동 요청에 포함되어야 하는 종속 리소스에 대한 세부 정보가 포함되어 있습니다.

예를 들어 가상 머신을 이동하려면 세 가지 서로 다른 리소스 공급자와 함께 7개의 리소스 유형을 이동해야 합니다. 이러한 리소스 공급자 및 유형은 다음과 같습니다.

* Microsoft.Compute

  * virtualMachines
  * disks
* Microsoft.Network
  * networkInterfaces
  * publicIPAddresses
  * networkSecurityGroups
  * virtualNetworks
* Microsoft.Storage
  * storageAccounts

또 다른 일반적인 예에는 가상 네트워크 이동이 포함되어 있습니다. 해당 가상 네트워크와 연관된 다른 여러 리소스를 이동해야 할 수 있습니다. 이동 요청은 공용 IP 주소, 경로 테이블, 가상 네트워크 게이트웨이, 네트워크 보안 그룹 등의 이동을 필요로 할 수 있습니다.

**질문: “RequestDisallowedByPolicy” 오류 코드는 무엇을 의미하나요?**

Resource Manager는 이동을 시도하기 전에 이동 요청의 유효성을 검사합니다. 이 유효성 검사에는 이동에 포함된 리소스에 정의된 정책 확인이 포함됩니다. 예를 들어, 키 자격 증명 모음을 이동하려고 하지만 대상 리소스 그룹에서 키 자격 증명 모음 만들기를 거부하는 정책이 조직에 있는 경우 유효성 검사에 실패하고 이동이 차단됩니다. 반환된 오류 코드는 **RequestDisallowedByPolicy** 입니다. 

정책에 관한 자세한 내용은 [Azure Policy란?](../../governance/policy/overview.md)을 참조하세요.

**질문: Azure에서 일부 리소스를 이동할 수 없는 이유는 무엇인가요?**

현재까지 Azure에서 모든 리소스가 이동을 지원하지는 않습니다. 이동을 지원하는 리소스 목록은 [리소스에 대한 이동 작업 지원](move-support-resources.md)을 참조하세요.

**질문: 단일 작업으로 이동할 수 있는 리소스는 몇 개인가요?**

가능한 경우 대용량 이동을 개별 이동 작업으로 나눕니다. 단일 작업에 800개가 넘는 리소스가 포함되면 Resource Manager가 즉시 오류를 반환합니다. 그러나 800개 미만의 리소스 이동도 시간 초과로 인해 실패할 수 있습니다.

**질문: 리소스가 성공 상태가 아니라는 오류의 의미는 무엇인가요?**

성공 상태가 아니므로 리소스를 이동할 수 없음을 나타내는 오류 메시지가 표시되는 경우 실제로 이동을 차단하는 것은 종속 리소스일 수 있습니다. 일반적으로 오류 코드는 **MoveCannotProceedWithResourcesNotInSucceededState** 입니다.

원본 또는 대상 리소스 그룹에 가상 네트워크가 포함된 경우 이동하는 동안 가상 네트워크에 종속된 모든 리소스의 상태를 검사합니다. 검사에는 가상 네트워크에 직접 및 간접적으로 종속된 리소스가 포함됩니다. 리소스 중 하나라도 실패 상태이면 이동이 차단됩니다. 예를 들어 가상 네트워크를 사용하는 가상 머신이 실패한 경우 이동이 차단됩니다. 가상 머신이 이동되는 리소스 중 하나가 아니고 이동에 사용되는 리소스 그룹 중 하나가 아닌 경우에도 이동이 차단됩니다.

이 오류가 표시되는 경우 두 가지 옵션을 사용할 수 있습니다. 가상 네트워크가 없는 리소스 그룹으로 리소스를 이동하거나 [고객 지원팀에 문의](../../azure-portal/supportability/how-to-create-azure-support-request.md)하세요.

## <a name="next-steps"></a>다음 단계

이동을 지원하는 리소스 목록은 [리소스에 대한 이동 작업 지원](move-support-resources.md)을 참조하세요.
