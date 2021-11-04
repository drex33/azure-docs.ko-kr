---
title: '빠른 시작: Azure PowerShell을 사용하여 공유 쿼리 만들기'
description: 이 빠른 시작에서는 Azure PowerShell을 사용하여 Resource Graph 공유 쿼리를 만드는 단계를 수행합니다.
ms.date: 08/17/2021
ms.topic: quickstart
ms.custom: devx-track-azurepowershell, mode-api
ms.openlocfilehash: 8281e2bb2ff53fea28ff43a69e8b16cf9d1f7ddf
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131016129"
---
# <a name="quickstart-create-a-resource-graph-shared-query-using-azure-powershell"></a>빠른 시작: Azure PowerShell을 사용하여 Resource Graph 공유 쿼리 만들기

이 문서에서는 [Az.ResourceGraph](/powershell/module/az.resourcegraph) PowerShell 모듈을 사용하여 Azure Resource Graph 공유 쿼리를 만드는 방법을 설명합니다.

## <a name="prerequisites"></a>필수 구성 요소

- Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

[!INCLUDE [azure-powershell-requirements-no-header.md](../../../includes/azure-powershell-requirements-no-header.md)]

  > [!IMPORTANT]
  > **Az.ResourceGraph** PowerShell 모듈이 미리 보기로 제공되는 동안 `Install-Module` cmdlet을 사용하여 별도로 설치해야 합니다.

  ```azurepowershell-interactive
  Install-Module -Name Az.ResourceGraph
  ```

- 여러 Azure 구독이 있는 경우 리소스에 대한 요금이 청구되는 적절한 구독을 선택합니다. [Set-AzContext](/powershell/module/az.accounts/set-azcontext) cmdlet을 사용하여 특정 구독을 선택합니다.

  ```azurepowershell-interactive
  Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
  ```

## <a name="create-a-resource-graph-shared-query"></a>Resource Graph 공유 쿼리 만들기

**Az.ResourceGraph** PowerShell 모듈이 선택한 환경에 추가되었으므로 이제 Resource Graph 공유 쿼리를 만들 차례입니다. 공유 쿼리는 Azure Resource Graph Explorer에서 사용 권한을 부여하거나 실행할 수 있는 Azure Resource Manager 개체입니다. 이 쿼리는 _위치_ 로 그룹화된 모든 리소스의 수를 요약합니다.

1. [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)을 사용하여 리소스 그룹을 만들어 Azure Resource Graph 공유 쿼리를 저장합니다. 이 리소스 그룹의 이름은 `resource-graph-queries`이며 위치는 `westus2`입니다.

   ```azurepowershell-interactive
   # Login first with `Connect-AzAccount` if not using Cloud Shell

   # Create the resource group
   New-AzResourceGroup -Name resource-graph-queries -Location westus2
   ```

1. **Az.ResourceGraph** PowerShell 모듈 및 [New-AzResourceGraphQuery](/powershell/module/az.resourcegraph/new-azresourcegraphquery) cmdlet을 사용하여 Azure Resource Graph 공유 쿼리를 만듭니다.

   ```azurepowershell-interactive
   # Create the Azure Resource Graph shared query
   $Params = @{
     Name = 'Summarize resources by location'
     ResourceGroupName = 'resource-graph-queries'
     Location = 'westus2'
     Description = 'This shared query summarizes resources by location for a pinnable map graphic.'
     Query = 'Resources | summarize count() by location'
   }
   New-AzResourceGraphQuery @Params
   ```

1. 새 리소스 그룹에 공유 쿼리를 나열합니다. [Get-AzResourceGraphQuery](/powershell/module/az.resourcegraph/get-azresourcegraphquery) cmdlet은 값의 배열을 반환합니다.

   ```azurepowershell-interactive
   # List all the Azure Resource Graph shared queries in a resource group
   Get-AzResourceGraphQuery -ResourceGroupName resource-graph-queries
   ```

1. 단일 공유 쿼리 결과만 가져오려면 `Name` 매개 변수와 함께 `Get-AzResourceGraphQuery`를 사용합니다.

   ```azurepowershell-interactive
   # Show a specific Azure Resource Graph shared query
   Get-AzResourceGraphQuery -ResourceGroupName resource-graph-queries -Name 'Summarize resources by location'
   ```

## <a name="clean-up-resources"></a>리소스 정리

Azure 환경에서 Resource Graph 공유 쿼리 및 리소스 그룹을 제거하려면 다음 명령을 사용하여 이 작업을 수행할 수 있습니다.

- [Remove-AzResourceGraphQuery](/powershell/module/az.resourcegraph/remove-azresourcegraphquery)
- [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup)

```azurepowershell-interactive
# Delete the Azure Resource Graph shared query
Remove-AzResourceGraphQuery -ResourceGroupName resource-graph-queries -Name 'Summarize resources by location'

# Remove the resource group
# WARNING: This command deletes ALL resources you've added to this resource group
Remove-AzResourceGroup -Name resource-graph-queries
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Azure PowerShell을 사용하여 Resource Graph 공유 쿼리를 만들었습니다. 리소스 그래프 언어에 대한 자세한 내용을 보려면 쿼리 언어 정보 페이지로 이동하세요.

> [!div class="nextstepaction"]
> [쿼리 언어에 대한 자세한 정보 가져오기](./concepts/query-language.md)
