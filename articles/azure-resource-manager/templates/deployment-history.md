---
title: 배포 기록
description: 포털, PowerShell, Azure CLI 및 REST API를 사용하여 Azure Resource Manager 배포 작업을 확인하는 방법을 설명합니다.
tags: top-support-issue
ms.topic: conceptual
ms.date: 11/10/2021
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 60bc06ab15e7f36a5c67ff5fc4de9da8bad28f88
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132310288"
---
# <a name="view-deployment-history-with-azure-resource-manager"></a>Azure Resource Manager를 사용한 배포 기록 보기

Azure Resource Manager를 사용하면 배포 기록을 볼 수 있습니다. 이전 배포의 특정 작업을 검사하고 어떤 리소스가 배포되었는지 확인할 수 있습니다. 이 기록에는 오류에 대한 정보가 포함됩니다.

리소스 그룹의 배포 기록은 800회 배포로 제한됩니다. 한도에 가까워지면 배포가 기록에서 자동으로 삭제됩니다. 자세한 내용은 [배포 기록에서 자동 삭제](deployment-history-deletions.md)를 참조하세요.

특정 배포 오류를 해결 하는 방법에 대 한 도움말은 [일반적인 Azure 배포 오류 해결](common-deployment-errors.md)을 참조 하세요.

## <a name="get-deployments-and-correlation-id"></a>배포 및 상관 관계 ID 가져오기

Azure Portal, PowerShell, Azure CLI 또는 REST API를 통해 배포에 대한 세부 정보를 볼 수 있습니다. 각 배포에는 관련 이벤트를 추적하는 데 사용되는 상관 관계 ID가 있습니다. [Azure 지원 요청을 만드는](../../azure-portal/supportability/how-to-create-azure-support-request.md)경우 고객 지원팀에서 상관 관계 ID를 요청할 수 있습니다. 고객 지원팀에서 상관 관계 ID를 사용하여 실패한 배포에 관한 작업을 식별합니다.

# <a name="portal"></a>[포털](#tab/azure-portal)

1. 검사하려는 리소스 그룹을 선택합니다.

1. **배포** 에서 링크를 선택합니다.

   :::image type="content" source="media/deployment-history/select-deployment-history.png" alt-text="성공적인 배포를 보여 주는 리소스 그룹 개요의 스크린샷":::

1. 배포 기록에서 배포 중 하나를 선택합니다.

   :::image type="content" source="media/deployment-history/select-details.png" alt-text="리소스 배포에 대해 강조 표시 된 링크의 스크린샷":::

1. 상관 관계 ID를 포함하여 배포 요약이 표시됩니다.

   :::image type="content" source="media/deployment-history/show-correlation-id.png" alt-text="상관 관계 ID를 강조 표시 하는 배포 기록의 스크린샷":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

리소스 그룹에 대한 모든 배포를 나열하려면 [Get-AzResourceGroupDeployment](/powershell/module/az.resources/Get-AzResourceGroupDeployment) 명령을 사용합니다.

```azurepowershell-interactive
Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup
```

리소스 그룹에서 특정 배포를 가져오려면 `DeploymentName` 매개 변수를 추가 합니다.

```azurepowershell-interactive
Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -DeploymentName ExampleDeployment
```

상관 관계 ID를 가져오려면 다음 코드를 사용합니다.

```azurepowershell-interactive
(Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -DeploymentName ExampleDeployment).CorrelationId
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

리소스 그룹에 대 한 모든 배포를 나열 하려면 [az deployment group list](/cli/azure/deployment/group#az_deployment_group_list)를 사용 합니다.

```azurecli-interactive
az deployment group list --resource-group ExampleGroup
```

특정 배포를 가져오려면 [az deployment group show](/cli/azure/deployment/group#az_deployment_group_show)를 사용합니다.

```azurecli-interactive
az deployment group show --resource-group ExampleGroup --name ExampleDeployment
```

상관 관계 ID를 가져오려면 다음 코드를 사용합니다.

```azurecli-interactive
az deployment group show --resource-group ExampleGroup --name ExampleDeployment --query properties.correlationId
```

# <a name="http"></a>[HTTP](#tab/http)

리소스 그룹에 대한 배포를 나열하려면 다음 작업을 사용합니다. 요청에 사용할 최신 API 버전 번호는 [배포 - 리소스 그룹별 목록](/rest/api/resources/deployments/listbyresourcegroup)을 참조하세요.

```
GET https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Resources/deployments/?api-version={api-version}
```

특정 배포를 가져오려면 다음 작업을 사용 합니다. 요청에 사용할 최신 API 버전 번호는 [배포 - 가져오기](/rest/api/resources/deployments/get)를 참조하세요.

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}?api-version={api-version}
```

응답에는 상관 관계 ID가 포함됩니다.

```json
{
 ...
 "properties": {
   "mode": "Incremental",
   "provisioningState": "Failed",
   "timestamp": "2019-11-26T14:18:36.4518358Z",
   "duration": "PT26.2091817S",
   "correlationId": "11111111-1111-1111-1111-111111111111",
   ...
 }
}
```

---

## <a name="get-deployment-operations-and-error-message"></a>배포 작업 및 오류 메시지 가져오기

각 배포에는 여러 작업이 포함될 수 있습니다. 배포에 대한 세부 정보를 보려면 배포 작업을 확인하세요. 배포에 실패하면 배포 작업에 오류 메시지가 포함됩니다.

# <a name="portal"></a>[포털](#tab/azure-portal)

1. 배포 요약에서 **작업 세부 정보** 를 선택합니다.

   :::image type="content" source="media/deployment-history/get-operation-details.png" alt-text="작업 세부 정보에 대 한 링크를 강조 표시 하는 실패 한 배포의 스크린샷":::

1. 배포 단계에 대한 세부 정보가 표시됩니다. 오류가 발생하면 세부 정보에 오류 메시지가 포함됩니다.

   :::image type="content" source="media/deployment-history/see-operation-details.png" alt-text="실패 한 배포 작업 세부 정보의 스크린샷":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

리소스 그룹에 배포하기 위한 배포 작업을 보려면 [Get-AzResourceGroupDeploymentOperation](/powershell/module/az.resources/get-azresourcegroupdeploymentoperation) 명령을 사용합니다.

```azurepowershell-interactive
Get-AzResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName ExampleDeployment
```

실패한 작업을 보려면 **실패한** 상태로 작업을 필터링합니다.

```azurepowershell-interactive
Get-AzResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -Name ExampleDeployment | Where-Object { $_.ProvisioningState -eq "Failed" }
```

실패한 작업에 대한 상태 메시지를 가져오려면 다음 명령을 사용합니다.

```azurepowershell-interactive
(Get-AzResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -Name ExampleDeployment | Where-Object { $_.ProvisioningState -eq "Failed" }).StatusMessage
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

리소스 그룹에 배포하기 위한 배포 작업을 보려면 [az deployment operation group list](/cli/azure/deployment/operation/group#az_deployment_operation_group_list) 명령을 사용합니다. Azure CLI 2.6.0 이상이 있어야 합니다.

```azurecli-interactive
az deployment operation group list --resource-group ExampleGroup --name ExampleDeployment
```

실패한 작업을 보려면 **실패한** 상태로 작업을 필터링합니다.

```azurecli-interactive
az deployment operation group list --resource-group ExampleGroup --name ExampleDeployment --query "[?properties.provisioningState=='Failed']"
```

실패한 작업에 대한 상태 메시지를 가져오려면 다음 명령을 사용합니다.

```azurecli-interactive
az deployment operation group list --resource-group ExampleGroup --name ExampleDeployment --query "[?properties.provisioningState=='Failed'].properties.statusMessage.error"
```

# <a name="http"></a>[HTTP](#tab/http)

배포 작업을 가져오려면 다음 작업을 사용합니다. 요청에 사용할 최신 API 버전 번호는 [배포 작업 - 목록](/rest/api/resources/deploymentoperations/list)을 참조하세요.

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}/operations?$skiptoken={skiptoken}&api-version={api-version}
```

응답에 오류 메시지가 포함되어 있습니다.

```json
{
  "value": [
    {
      "id": "/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/examplegroup/providers/Microsoft.Resources/deployments/exampledeployment/operations/1234567890ABCDEF",
      "operationId": "1234567890ABCDEF",
      "properties": {
        "provisioningOperation": "Create",
        "provisioningState": "Failed",
        "timestamp": "2019-11-26T14:18:36.3177613Z",
        "duration": "PT21.0580179S",
        "trackingId": "11111111-1111-1111-1111-111111111111",
        "serviceRequestId": "11111111-1111-1111-1111-111111111111",
        "statusCode": "BadRequest",
        "statusMessage": {
          "error": {
            "code": "InvalidAccountType",
            "message": "The AccountType Standard_LRS1 is invalid. For more information, see - https://aka.ms/storageaccountskus"
          }
        },
        "targetResource": {
          "id": "/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/examplegroup/providers/Microsoft.Storage/storageAccounts/storage",
          "resourceType": "Microsoft.Storage/storageAccounts",
          "resourceName": "storage"
        }
      }
    },
    ...
  ]
}
```

---

## <a name="next-steps"></a>다음 단계

- 특정 배포 오류 해결에 대 한 도움말은 [일반적인 Azure 배포 오류 해결](common-deployment-errors.md)을 참조 하세요.
- 기록에서 배포를 관리하는 방법에 관해 알아보려면 [배포 기록에서 자동 삭제](deployment-history-deletions.md)를 참조하세요.
- 를 배포 하기 전에 템플릿이 수행 하는 변경 내용을 미리 보려면 [ARM 템플릿 배포 가상 작업](deploy-what-if.md)을 참조 하세요.
