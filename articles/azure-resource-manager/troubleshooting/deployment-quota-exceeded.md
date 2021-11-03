---
title: 배포 할당량 초과
description: 리소스 그룹 기록에 배포 수가 800개를 초과하여 발생한 오류를 해결하는 방법을 설명합니다.
ms.topic: troubleshooting
ms.date: 08/07/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 06383a47f2fc7603bdc112d1fe0cd0ca53930bd6
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131053643"
---
# <a name="resolve-error-when-deployment-count-exceeds-800"></a>배포 수가 800을 초과하는 경우 오류 해결

각 리소스 그룹의 배포 기록에서 배포는 800개로 제한됩니다. 이 문서에서는 허용되는 800개의 배포를 초과하여 배포가 실패하는 경우 수신하는 오류를 설명합니다. 이 오류를 해결하려면 리소스 그룹 기록에서 배포를 삭제하세요. 기록에서 배포를 삭제해도 배포된 리소스에는 영향을 주지 않습니다.

Azure Resource Manager는 한도에 가까워지면 자동으로 기록에서 배포를 삭제합니다. 하지만 다음 이유 중 하나로 인해 이 오류가 계속 표시될 수 있습니다.

1. 배포 기록에서 삭제를 방지하는 CanNotDelete 잠금이 리소스 그룹에 설정되어 있습니다.
1. 자동 삭제를 옵트아웃했습니다.
1. 매우 많은 수의 배포를 동시에 실행하고 있어 자동 삭제 처리 속도가 총 배포 수를 줄일만큼 충분히 빠르지 않습니다.

잠금을 제거하거나 자동 삭제를 옵트인하는 방법에 대한 자세한 내용은 [배포 기록에서 자동 삭제](../templates/deployment-history-deletions.md)를 참조하세요.

이 문서에서는 기록에서 배포를 수동으로 삭제하는 방법을 설명합니다.

## <a name="symptom"></a>증상

배포 중에 현재 배포가 할당량인 800개의 배포를 초과한다는 오류를 수신합니다.

## <a name="solution"></a>솔루션

### <a name="azure-cli"></a>Azure CLI

[az deployment group delete](/cli/azure/group/deployment) 명령을 사용하여 기록에서 배포를 삭제합니다.

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

다음 명령을 사용하여 배포 기록의 현재 개수를 가져올 수 있습니다.

```azurecli-interactive
az deployment group list --resource-group exampleGroup --query "length(@)"
```

### <a name="azure-powershell"></a>Azure PowerShell

[Remove-AzResourceGroupDeployment](/powershell/module/az.resources/remove-azresourcegroupdeployment) 명령을 사용하여 기록에서 배포를 삭제합니다.

```azurepowershell-interactive
Remove-AzResourceGroupDeployment -ResourceGroupName exampleGroup -Name deploymentName
```

5일이 경과한 배포는 모두 삭제하려면 다음을 사용합니다.

```azurepowershell-interactive
$deployments = Get-AzResourceGroupDeployment -ResourceGroupName exampleGroup | Where-Object Timestamp -lt ((Get-Date).AddDays(-5))

foreach ($deployment in $deployments) {
  Remove-AzResourceGroupDeployment -ResourceGroupName exampleGroup -Name $deployment.DeploymentName
}
```

다음 명령을 사용하여 배포 기록의 현재 개수를 가져올 수 있습니다.

```azurepowershell-interactive
(Get-AzResourceGroupDeployment -ResourceGroupName exampleGroup).Count
```

## <a name="third-party-solutions"></a>타사 솔루션

다음 외부 솔루션은 특정 시나리오를 처리합니다.

* [Azure Logic Apps 및 PowerShell 솔루션](https://devkimchi.com/2018/05/30/managing-excessive-arm-deployment-histories-with-logic-apps/)
* [AzureDevOpsExtensionCleanRG](https://github.com/christianwaha/AzureDevOpsExtensionCleanRG)
