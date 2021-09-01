---
title: Azure PowerShell을 사용하여 SAP 솔루션을 위한 Azure Monitor 배포
description: Azure PowerShell을 사용하여 SAP 솔루션을 위한 Azure Monitor 배포
author: sameeksha91
ms.author: sakhare
ms.topic: quickstart
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.date: 07/08/2021
ms.devlang: azurepowershell
ms.custom: devx-track-azurepowershell - devx-track-azurepowershell - mode-api
ms.openlocfilehash: 572c2f76e315baf916adaab8cc31d154bce32ff0
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2021
ms.locfileid: "122969754"
---
# <a name="quickstart-deploy-azure-monitor-for-sap-solutions-with-azure-powershell"></a>빠른 시작: Azure PowerShell을 사용하여 SAP 솔루션을 위한 Azure Monitor 배포

이 문서에서는 [Az.HanaOnAzure](/powershell/module/az.hanaonazure/#sap-hana-on-azure) PowerShell 모듈을 사용하여 SAP 솔루션을 위한 Azure Monitor를 만드는 방법을 설명합니다.

> [!CAUTION]
> SAP 솔루션을 위한 Azure Monitor는 현재 공개 미리 보기로 제공됩니다. 이 미리 보기 버전은 서비스 수준 계약 없이 제공됩니다. 프로덕션 워크로드에는 권장되지 않습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="requirements"></a>요구 사항

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

PowerShell을 로컬로 사용하도록 선택하는 경우 이 문서에는 Azure PowerShell 모듈을 설치해야 합니다. 또한 [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) cmdlet을 사용하여 Azure 계정에 연결해야 합니다. Az PowerShell 모듈을 설치하는 방법에 대한 자세한 내용은 [Azure PowerShell 설치](/powershell/azure/install-az-ps)를 참조하세요. 또는 Cloud Shell을 사용하도록 선택할 수 있습니다. Cloud Shell에 관한 자세한 내용은 [Azure Cloud Shell 개요](../../../cloud-shell/overview.md)를 참조하세요.

> [!IMPORTANT]
> **Az.HanaOnAzure** PowerShell 모듈은 미리 보기 상태이지만 `Install-Module` cmdlet을 사용하여 별도로 설치해야 합니다. 이 PowerShell 모듈이 일반 공급되면 이후 Az PowerShell 모듈 릴리스의 기능으로 포함되어 Azure Cloud Shell 내에서 기본적으로 사용할 수 있습니다.

```azurepowershell-interactive
Install-Module -Name Az.HanaOnAzure
```

여러 Azure 구독이 있는 경우 리소스에 대한 요금이 청구되는 적절한 구독을 선택합니다. [Set-AzContext](/powershell/module/az.accounts/set-azcontext) cmdlet을 사용하여 특정 구독을 선택합니다.

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) cmdlet을 사용하여 [Azure 리소스 그룹](../../../azure-resource-manager/management/overview.md)을 만듭니다. 리소스 그룹은 Azure 리소스가 그룹으로 배포되고 관리되는 논리 컨테이너입니다.

다음 예제에서는 지정된 이름으로 지정된 위치에 리소스 그룹을 만듭니다.

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location westus2
```

## <a name="sap-monitor"></a>SAP 모니터

SAP 모니터를 만들려면 [New-AzSapMonitor](/powershell/module/az.hanaonazure/new-azsapmonitor) cmdlet을 사용합니다. 다음 예제에서는 지정된 구독, 리소스 그룹, 리소스 이름에 대한 SAP 모니터를 만듭니다.

```azurepowershell-interactive
$Workspace = New-AzOperationalInsightsWorkspace -ResourceGroupName myResourceGroup -Name sapmonitor-test -Location westus2 -Sku Standard

$WorkspaceKey = Get-AzOperationalInsightsWorkspaceSharedKey -ResourceGroupName myResourceGroup -Name sapmonitor-test

$SapMonitorParams = @{
  Name = 'ps-sapmonitor-t01'
  ResourceGroupName = 'myResourceGroup'
  Location = 'westus2'
  EnableCustomerAnalytic = $true
  MonitorSubnet = '/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/vnet-sap/subnets/mysubnet'
  LogAnalyticsWorkspaceSharedKey = $WorkspaceKey.PrimarySharedKey
  LogAnalyticsWorkspaceId = $Workspace.CustomerId
  LogAnalyticsWorkspaceResourceId = $Workspace.ResourceId
}
New-AzSapMonitor @SapMonitorParams
```

SAP 모니터의 속성을 검색하려면 [Get-AzSapMonitor](/powershell/module/az.hanaonazure/get-azsapmonitor) cmdlet을 사용합니다. 다음 예제에서는 지정된 구독, 리소스 그룹, 리소스 이름에 대한 SAP 모니터의 속성을 가져옵니다.

```azurepowershell-interactive
Get-AzSapMonitor -ResourceGroupName myResourceGroup -Name ps-spamonitor-t01
```

## <a name="provider-instance"></a>공급자 인스턴스

공급자 인스턴스를 만들려면 [New-AzSapMonitorProviderInstance](/powershell/module/az.hanaonazure/new-azsapmonitorproviderinstance) cmdlet을 사용합니다. 다음 예에서는 지정된 구독, 리소스 그룹 및 리소스 이름에 대한 공급자 인스턴스를 만듭니다.

```azurepowershell-interactive
$SapProviderParams = @{
  ResourceGroupName = 'myResourceGroup'
  Name = 'ps-sapmonitorins-t01'
  SapMonitorName = 'yemingmonitor'
  ProviderType = 'SapHana'
  HanaHostname = 'hdb1-0'
  HanaDatabaseName = 'SYSTEMDB'
  HanaDatabaseSqlPort = '30015'
  HanaDatabaseUsername = 'SYSTEM'
  HanaDatabasePassword = (ConvertTo-SecureString 'Manager1' -AsPlainText -Force)
}
New-AzSapMonitorProviderInstance @SapProviderParams
```

공급자 인스턴스의 속성을 검색하려면 [Get-AzSapMonitorProviderInstance](/powershell/module/az.hanaonazure/get-azsapmonitorproviderinstance) cmdlet을 사용합니다. 다음 예제에서는 다음의 속성을 가져옵니다. 
- 지정된 구독의 공급자 인스턴스
- 리소스 그룹
- SapMonitor 이름
- 리소스 이름

```azurepowershell-interactive
Get-AzSapMonitorProviderInstance -ResourceGroupName myResourceGroup -SapMonitorName ps-spamonitor-t01
```

## <a name="clean-up-resources"></a>리소스 정리

이 문서에서 만든 리소스가 필요하지 않은 경우 다음 예제를 실행하여 삭제할 수 있습니다.

### <a name="delete-the-provider-instance"></a>공급자 인스턴스 삭제

공급자 인스턴스를 제거하려면 [Remove-AzSapMonitorProviderInstance](/powershell/module/az.hanaonazure/remove-azsapmonitorproviderinstance) cmdlet을 사용합니다. 다음 예에서는 지정된 구독, 리소스 그룹, SapMonitor 이름 및 리소스 이름에 대한 공급자 인스턴스를 삭제합니다.

```azurepowershell-interactive
Remove-AzSapMonitorProviderInstance -ResourceGroupName myResourceGroup -SapMonitorName ps-spamonitor-t01 -Name ps-sapmonitorins-t02
```

### <a name="delete-the-sap-monitor"></a>SAP 모니터 삭제

SAP 모니터를 제거하려면 [Remove-AzSapMonitor](/powershell/module/az.hanaonazure/remove-azsapmonitor) cmdlet을 사용합니다. 다음 예제에서는 지정된 구독, 리소스 그룹, 모니터 이름에 대한 SAP 모니터를 삭제합니다.

```azurepowershell
Remove-AzSapMonitor -ResourceGroupName myResourceGroup -Name ps-sapmonitor-t02
```

### <a name="delete-the-resource-group"></a>리소스 그룹 삭제

> [!CAUTION]
> 다음 예제에서는 지정된 리소스 그룹과 해당 그룹에 포함된 모든 리소스를 삭제합니다.
> 지정된 리소스 그룹에 이 문서의 범위에 속하지 않는 리소스가 포함된 경우에도 해당 리소스가 삭제됩니다.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>다음 단계

SAP 솔루션을 위한 Azure Monitor에 관해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [Azure의 SAP 모니터링](monitor-sap-on-azure.md)
