---
title: 배포 할당량 초과
description: 리소스 그룹 기록에 배포 수가 800개를 초과하여 발생한 오류를 해결하는 방법을 설명합니다.
ms.topic: troubleshooting
ms.date: 11/30/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 20f149a11a444a2e5315bdd4353a9c3b1fb8a11d
ms.sourcegitcommit: 845eb7b0ed05ef2f0dfa6f054eaf5f32c780567c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2021
ms.locfileid: "133302318"
---
# <a name="resolve-error-when-deployment-count-exceeds-800"></a>배포 수가 800을 초과하는 경우 오류 해결

각 리소스 그룹의 배포 기록에서 배포는 800개로 제한됩니다. 이 문서에서는 허용되는 800개의 배포를 초과하여 배포가 실패하는 경우 수신하는 오류를 설명합니다. 이 오류를 해결하려면 리소스 그룹 기록에서 배포를 삭제하세요. 기록에서 배포를 삭제해도 배포된 리소스에는 영향을 주지 않습니다.

Azure Resource Manager는 한도에 가까워지면 자동으로 기록에서 배포를 삭제합니다. 하지만 다음 이유 중 하나로 인해 이 오류가 계속 표시될 수 있습니다.

1. 배포 기록에서 삭제를 방지 하는 [Cannotdelete](../management/lock-resources.md) 잠금이 리소스 그룹에 있습니다.
1. 자동 삭제를 옵트아웃 했습니다.
1. 매우 많은 수의 배포를 동시에 실행하고 있어 자동 삭제 처리 속도가 총 배포 수를 줄일만큼 충분히 빠르지 않습니다.

잠금을 제거 하거나 자동 삭제를 옵트인 하는 방법에 대 한 자세한 내용은 [배포 기록에서 자동 삭제](../templates/deployment-history-deletions.md)를 참조 하세요.

이 문서에서는 기록에서 배포를 수동으로 삭제하는 방법을 설명합니다.

## <a name="symptom"></a>증상

배포 하는 동안 현재 배포가 800 배포의 할당량을 초과 한다는 오류가 표시 됩니다.

## <a name="solution"></a>해결 방법

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[az deployment group delete](/cli/azure/deployment/group#az_deployment_group_delete) 명령을 사용하여 기록에서 배포를 삭제합니다.

```azurecli-interactive
az deployment group delete --resource-group exampleGroup --name deploymentName
```

5일이 경과한 배포는 모두 삭제하려면 다음을 사용합니다.

```azurecli-interactive
startdate=$(date +%F -d "-5days")
deployments=$(az deployment group list --resource-group exampleGroup --query "[?properties.timestamp<'$startdate'].name" --output tsv)

for deployment in $deployments
do
  az deployment group delete --resource-group exampleGroup --name $deployment
done
```

다음 명령을 사용 하 여 배포 기록에서 현재 개수를 가져올 수 있습니다. 이 예에는 Bash 환경이 필요 합니다.

```azurecli-interactive
az deployment group list --resource-group exampleGroup --query "length(@)"
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[Remove-AzResourceGroupDeployment](/powershell/module/az.resources/remove-azresourcegroupdeployment) 명령을 사용하여 기록에서 배포를 삭제합니다.

```azurepowershell-interactive
Remove-AzResourceGroupDeployment -ResourceGroupName exampleGroup -Name deploymentName
```

5일이 경과한 배포는 모두 삭제하려면 다음을 사용합니다.

```azurepowershell-interactive
$deployments = Get-AzResourceGroupDeployment -ResourceGroupName exampleGroup | Where-Object -Property Timestamp -LT -Value ((Get-Date).AddDays(-5))

foreach ($deployment in $deployments) {
  Remove-AzResourceGroupDeployment -ResourceGroupName exampleGroup -Name $deployment.DeploymentName
}
```

다음 명령을 사용하여 배포 기록의 현재 개수를 가져올 수 있습니다.

```azurepowershell-interactive
(Get-AzResourceGroupDeployment -ResourceGroupName exampleGroup).Count
```

---
