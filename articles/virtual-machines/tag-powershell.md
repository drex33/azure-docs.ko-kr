---
title: PowerShell을 사용하여 VM에 태그를 지정하는 방법
description: PowerShell을 사용하여 가상 머신에 태그를 지정하는 방법에 대해 알아보기
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 11/11/2020
ms.author: cynthn
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 78edd5c62c4f7abe78e70ae90e1b1e7ce3a5dc65
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122695337"
---
# <a name="how-to-tag-a-virtual-machine-in-azure-using-powershell"></a>Azure에서 PowerShell을 사용하여 가상 머신에 태그를 지정하는 방법

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 유연한 확장 집합 :heavy_check_mark: 단일 확장 집합

이 문서에서는 Azure에서 PowerShell을 사용하여 VM에 태그를 지정하는 방법을 설명합니다. 태그는 리소스 또는 리소스 그룹에 직접 배치할 수 있는 사용자 정의 키/값 쌍입니다. Azure는 현재 리소스 및 리소스 그룹당 최대 50개의 태그를 지원합니다. 태그를 만들 때 리소스에 배치하거나 기존 리소스에 추가할 수 있습니다. Azure CLI를 사용하여 가상 머신에 태그를 지정하려면 [Azure에서 Azure CLI를 사용하여 가상 머신에 태그를 지정하는 방법](tag-cli.md)을 참조하세요.

`Get-AzVM` cmdlet을 사용하여 VM의 현재 태그 목록을 볼 수 있습니다.

```azurepowershell-interactive
Get-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM" | Format-List -Property Tags
```

가상 컴퓨터에 이미 태그가 포함된 경우 모든 태그가 목록 형식으로 표시됩니다.

태그를 추가하려면 `Set-AzResource` 명령을 사용합니다. PowerShell을 통해 태그를 업데이트하는 경우 태그가 전체적으로 업데이트됩니다. 이미 태그가 있는 리소스에 태그를 하나 추가하는 경우 리소스에 배치하려는 모든 태그를 포함해야 합니다. 다음은 PowerShell Cmdlet을 통해 리소스에 태그를 더 추가하는 방법의 예입니다.

`Get-AzResource` 및 `Tags` 속성을 사용하여 VM의 모든 현재 태그를 `$tags` 변수에 할당합니다.

```azurepowershell-interactive
$tags = (Get-AzResource -ResourceGroupName myResourceGroup -Name myVM).Tags
```

현재 태그를 보려면 변수를 입력합니다.

```azurepowershell-interactive
$tags
```

출력은 다음과 같습니다.

```output
Key           Value
----          -----
Department    MyDepartment
Application   MyApp1
Created By    MyName
Environment   Production
```

다음 예에서는 값이 `myLocation`인 `Location`이라는 태그를 추가합니다. `+=`를 사용하여 `$tags` 목록에 새 키/값 쌍을 추가합니다.

```azurepowershell-interactive
$tags += @{Location="myLocation"}
```

`Set-AzResource`를 사용하여 VM의 *$tags* 변수에 정의된 모든 태그를 설정합니다.

```azurepowershell-interactive
Set-AzResource -ResourceGroupName myResourceGroup -Name myVM -ResourceType "Microsoft.Compute/VirtualMachines" -Tag $tags
```

`Get-AzResource`를 사용하여 리소스의 모든 태그를 표시합니다.

```azurepowershell-interactive
(Get-AzResource -ResourceGroupName myResourceGroup -Name myVM).Tags

```

출력은 이제 새 태그를 포함하는 다음과 유사해야 합니다.

```output

Key           Value
----          -----
Department    MyDepartment
Application   MyApp1
Created By    MyName
Environment   Production
Location      MyLocation
```

### <a name="next-steps"></a>다음 단계

- Azure 리소스에 태그를 지정하는 방법에 대한 자세한 내용은 [Azure Resource Manager 개요](../azure-resource-manager/management/overview.md) 및 [태그를 사용하여 Azure 리소스 구성](../azure-resource-manager/management/tag-resources.md)을 참조하세요.
- 태그를 사용하여 Azure 리소스의 사용을 관리하는 방법을 알아보려면 [Azure 청구서 이해](../cost-management-billing/understand/review-individual-bill.md)를 참조하세요.
