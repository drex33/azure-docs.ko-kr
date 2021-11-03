---
title: Azure PowerShell을 사용하여 Azure 사용자 지정 리소스 공급자 만들기
description: Azure PowerShell을 사용하여 Azure 사용자 지정 리소스 공급자를 만드는 방법을 설명합니다.
author: MSEvanhi
ms.author: evanhi
ms.date: 09/22/2020
ms.topic: quickstart
ms.devlang: azurepowershell
ms.custom: devx-track-azurepowershell, mode-api
ms.openlocfilehash: 910a586b15aef0d88409f5756fd340d5cb7c06d2
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131087346"
---
# <a name="quickstart-create-an-azure-custom-resource-provider-with-azure-powershell"></a>빠른 시작: Azure PowerShell을 사용하여 Azure 사용자 지정 리소스 공급자 만들기

이 빠른 시작에서는 [Az.CustomProviders](/powershell/module/az.customproviders) PowerShell 모듈을 사용하여 사용자 고유의 Azure 사용자 지정 리소스 공급자를 만드는 방법에 대해 알아봅니다.

> [!CAUTION]
> Azure 사용자 지정 공급자는 현재 공개 미리 보기로 제공됩니다. 이 미리 보기 버전은 서비스 수준 계약 없이 제공됩니다. 프로덕션 워크로드에는 권장되지 않습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="requirements"></a>요구 사항

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

PowerShell을 로컬로 사용하도록 선택하는 경우 이 문서에서는 Az PowerShell 모듈을 설치하고 [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) cmdlet을 사용하여 Azure 계정에 연결해야 합니다. Az PowerShell 모듈을 설치하는 방법에 대한 자세한 내용은 [Azure PowerShell 설치](/powershell/azure/install-az-ps)를 참조하세요. Cloud Shell을 사용하도록 선택하는 경우 자세한 내용은 [Azure Cloud Shell 개요](../../cloud-shell/overview.md)를 참조하세요.

> [!IMPORTANT]
> **Az.CustomProviders** PowerShell 모듈은 미리 보기 상태이지만 `Install-Module` cmdlet을 사용하여 별도로 설치해야 합니다. 이 PowerShell 모듈이 일반 공급되면 이후 Az PowerShell 모듈 릴리스의 기능으로 포함되어 Azure Cloud Shell 내에서 기본적으로 사용할 수 있습니다.

```azurepowershell-interactive
Install-Module -Name Az.CustomProviders
```

여러 Azure 구독이 있는 경우 리소스에 대한 요금이 청구되는 적절한 구독을 선택합니다. [Set-AzContext](/powershell/module/az.accounts/set-azcontext) cmdlet을 사용하여 특정 구독을 선택합니다.

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) cmdlet을 사용하여 [Azure 리소스 그룹](../../azure-resource-manager/management/overview.md)을 만듭니다. 리소스 그룹은 Azure 리소스가 그룹으로 배포되고 관리되는 논리 컨테이너입니다.

다음 예제에서는 지정된 이름으로 지정된 위치에 리소스 그룹을 만듭니다.

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location westus2
```

## <a name="create-a-custom-resource-provider"></a>사용자 지정 리소스 공급자 만들기

사용자 지정 리소스 공급자를 만들거나 업데이트하려면 다음 예제와 같이 [New-AzCustomProvider](/powershell/module/az.customproviders/new-azcustomprovider) cmdlet을 사용합니다.

```azurepowershell-interactive
New-AzCustomProvider -ResourceGroupName myResourceGroup -Name Namespace.Type -Location westus2 -ResourceType @{Name='CustomRoute1'; Endpoint='https://www.contoso.com/'}
```

## <a name="get-the-custom-resource-provider-manifest"></a>사용자 지정 리소스 공급자 매니페스트 가져오기

사용자 지정 리소스 공급자 매니페스트에 대한 정보를 검색하려면 다음 예제와 같이 [Get-AzCustomProvider](/powershell/module/az.customproviders/get-azcustomprovider) cmdlet을 사용합니다.

```azurepowershell-interactive
Get-AzCustomProvider -ResourceGroupName myResourceGroup -Name Namespace.Type | Format-List
```

## <a name="create-an-association"></a>연결 만들기

연결을 만들거나 업데이트하려면 다음 예제와 같이 [New-AzCustomProviderAssociation](/powershell/module/az.customproviders/new-azcustomproviderassociation) cmdlet을 사용합니다.

```azurepowershell-interactive
$provider = Get-AzCustomProvider -ResourceGroupName myResourceGroup -Name Namespace.Type
New-AzCustomProviderAssociation -Scope $resourceId -Name MyAssoc -TargetResourceId $provider.Id
```

## <a name="get-an-association"></a>연결 가져오기

연결에 대한 정보를 검색하려면 다음 예제와 같이 [Get-AzCustomProviderAssociation](/powershell/module/az.customproviders/get-azcustomproviderassociation) cmdlet을 사용합니다.

```azurepowershell-interactive
Get-AzCustomProviderAssociation -Scope $resourceId -Name MyAssoc
```

## <a name="clean-up-resources"></a>리소스 정리

이 문서에서 만든 리소스가 필요하지 않은 경우 다음 예제를 실행하여 삭제할 수 있습니다.

### <a name="delete-an-association"></a>연결 삭제

연결을 제거하려면 [Remove-AzCustomProviderAssociation](/powershell/module/az.customproviders/remove-azcustomproviderassociation) cmdlet을 사용합니다. 다음 예제에서는 연결을 삭제합니다.

```azurepowershell
Remove-AzCustomProviderAssociation -Scope $id -Name Namespace.Type
```

### <a name="delete-a-custom-resource-provider"></a>사용자 지정 리소스 공급자 삭제

사용자 지정 리소스 공급자를 제거하려면 [Remove-AzCustomProvider](/powershell/module/az.customproviders/remove-azcustomprovider) cmdlet을 사용합니다. 다음 예제에서는 사용자 지정 리소스 공급자를 삭제합니다.

```azurepowershell-interactive
Remove-AzCustomProvider -ResourceGroupName myResourceGroup -Name Namespace.Type
```

### <a name="delete-the-resource-group"></a>리소스 그룹 삭제

> [!CAUTION]
> 다음 예제에서는 지정된 리소스 그룹과 해당 그룹에 포함된 모든 리소스를 삭제합니다.
> 지정된 리소스 그룹에 이 문서의 범위에 속하지 않는 리소스가 포함된 경우에도 해당 리소스가 삭제됩니다.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>다음 단계

[Azure 사용자 지정 리소스 공급자](overview.md)에 대해 자세히 알아봅니다.
