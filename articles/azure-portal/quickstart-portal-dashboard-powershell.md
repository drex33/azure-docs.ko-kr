---
title: PowerShell을 사용하여 Azure Portal 대시보드 만들기
description: Azure Portal에서 Azure PowerShell을 사용하여 대시보드를 만드는 방법을 알아봅니다.
ms.topic: quickstart
ms.custom: devx-track-azurepowershell, mode-api
ms.date: 03/25/2021
ms.openlocfilehash: 5a86133d0c1066c306b76c2a5578c9bb84808762
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133051536"
---
# <a name="quickstart-create-an-azure-portal-dashboard-with-powershell"></a>빠른 시작: PowerShell을 사용하여 Azure Portal 대시보드 만들기

Azure Portal의 대시보드는 클라우드 리소스에 대해 집중적으로 구성된 보기입니다. 이 문서에서는 Az.Portal PowerShell 모듈을 사용하여 대시보드를 만드는 과정을 중점적으로 설명합니다.
대시보드는 VM(가상 머신)의 성능뿐만 아니라 일부 정적 정보 및 링크도 보여 줍니다.

## <a name="requirements"></a>요구 사항

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

PowerShell을 로컬로 사용하도록 선택하는 경우 이 문서에서는 Az PowerShell 모듈을 설치하고 [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) cmdlet을 사용하여 Azure 계정에 연결해야 합니다. Az PowerShell 모듈을 설치하는 방법에 대한 자세한 내용은 [Azure PowerShell 설치](/powershell/azure/install-az-ps)를 참조하세요.

> [!IMPORTANT]
> **Az.Portal** PowerShell 모듈은 현재 미리 보기로 제공되며, Az PowerShell 모듈에서 `Install-Module` cmdlet을 사용하여 별도로 설치해야 합니다. 이 PowerShell 모듈이 일반 공급되면 이후 Az PowerShell 모듈 릴리스의 기능으로 포함되어 Azure Cloud Shell 내에서 기본적으로 사용할 수 있습니다.

```azurepowershell-interactive
Install-Module -Name Az.Portal
```

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

## <a name="choose-a-specific-azure-subscription"></a>특정 Azure 구독 선택

여러 Azure 구독이 있는 경우 리소스에 대한 요금이 청구되는 적절한 구독을 선택합니다. [Set-AzContext](/powershell/module/az.accounts/set-azcontext) cmdlet을 사용하여 특정 구독을 선택합니다.

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="define-variables"></a>변수 정의

여러 가지 정보를 반복해서 사용하게 됩니다. 정보를 저장할 변수를 만듭니다.

```azurepowershell-interactive
# Name of resource group used throughout this article
$resourceGroupName = 'myResourceGroup'

# Azure region
$location = 'centralus'

# Dashboard Title
$dashboardTitle = 'Simple VM Dashboard'

# Dashboard Name
$dashboardName = $dashboardTitle -replace '\s'

# Your Azure Subscription ID
$subscriptionID = (Get-AzContext).Subscription.Id

# Name of test VM
$vmName = 'SimpleWinVM'
```

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) cmdlet을 사용하여 [Azure 리소스 그룹](../azure-resource-manager/management/overview.md)을 만듭니다. 리소스 그룹은 Azure 리소스가 그룹으로 배포되고 관리되는 논리 컨테이너입니다.

다음 예제에서는 `$location` 변수에 지정된 지역의 `$resourceGroupName` 변수 이름을 기반으로 리소스 그룹을 만듭니다.

```azurepowershell-interactive
New-AzResourceGroup -Name $resourceGroupName -Location $location
```

## <a name="create-a-virtual-machine"></a>가상 머신 만들기

이 빠른 시작의 다음 부분에서 만드는 대시보드에는 기존 VM이 필요합니다. 다음 단계에 따라 VM을 만듭니다.

VM의 로그인 자격 증명을 변수에 저장합니다. 암호는 복잡해야 합니다. 새 사용자 이름 및 암호입니다. 예를 들어 Azure에 로그인하는 데 사용하는 계정이 아닙니다. 자세한 내용은 [사용자 이름 요구 사항](../virtual-machines/windows/faq.yml#what-are-the-username-requirements-when-creating-a-vm-) 및 [암호 요구 사항](../virtual-machines/windows/faq.yml#what-are-the-password-requirements-when-creating-a-vm-)을 참조하세요.

```azurepowershell-interactive
$Cred = Get-Credential
```

VM을 만듭니다.

```azurepowershell-interactive
$AzVmParams = @{
  ResourceGroupName = $resourceGroupName
  Name = $vmName
  Location = $location
  Credential = $Cred
}
New-AzVm @AzVmParams
```

이제 VM 배포가 시작되고 완료하는 데 일반적으로 몇 분 정도 걸립니다. 배포가 완료되면 다음 섹션으로 이동합니다.

## <a name="download-the-dashboard-template"></a>대시보드 템플릿 다운로드

Azure 대시보드는 리소스이므로 JSON으로 표현할 수 있습니다. 다음 코드는 샘플 대시보드의 JSON 표현을 다운로드합니다. 자세한 내용은 [Azure 대시보드의 구조](./azure-portal-dashboards-structure.md)를 참조하세요.

```azurepowershell-interactive
$myPortalDashboardTemplateUrl = 'https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/azure-portal/portal-dashboard-template-testvm.json'

$myPortalDashboardTemplatePath = "$HOME\portal-dashboard-template-testvm.json"

Invoke-WebRequest -Uri $myPortalDashboardTemplateUrl -OutFile $myPortalDashboardTemplatePath -UseBasicParsing
```

## <a name="customize-the-template"></a>템플릿 사용자 지정

다음 코드를 실행하여 다운로드한 템플릿을 사용자 지정합니다.

```azurepowershell
$Content = Get-Content -Path $myPortalDashboardTemplatePath -Raw
$Content = $Content -replace '<subscriptionID>', $subscriptionID
$Content = $Content -replace '<rgName>', $resourceGroupName
$Content = $Content -replace '<vmName>', $vmName
$Content = $Content -replace '<dashboardTitle>', $dashboardTitle
$Content = $Content -replace '<location>', $location
$Content | Out-File -FilePath $myPortalDashboardTemplatePath -Force
```

자세한 내용은 [Microsoft 포털 대시보드 템플릿 참조](/azure/templates/microsoft.portal/dashboards)를 검토하세요.

## <a name="deploy-the-dashboard-template"></a>대시보드 템플릿 배포

Az.Portal 모듈에 포함된 `New-AzPortalDashboard` cmdlet을 사용하여 PowerShell에서 직접 템플릿을 배포할 수 있습니다.

```azurepowershell
$DashboardParams = @{
  DashboardPath = $myPortalDashboardTemplatePath
  ResourceGroupName = $resourceGroupName
  DashboardName = $dashboardName
}
New-AzPortalDashboard @DashboardParams
```

## <a name="review-the-deployed-resources"></a>배포된 리소스 검토

대시보드가 성공적으로 만들어졌는지 확인합니다.

```azurepowershell
Get-AzPortalDashboard -Name $dashboardName -ResourceGroupName $resourceGroupName
```

[!INCLUDE [azure-portal-review-deployed-resources](../../includes/azure-portal-review-deployed-resources.md)]

## <a name="clean-up-resources"></a>리소스 정리

VM 및 연결된 대시보드를 제거하려면 해당 VM이 속한 리소스 그룹을 삭제합니다.

> [!CAUTION]
> 다음 예제에서는 지정된 리소스 그룹과 해당 그룹에 포함된 모든 리소스를 삭제합니다.
> 지정된 리소스 그룹에 이 문서의 범위에 속하지 않는 리소스가 포함된 경우에도 해당 리소스가 삭제됩니다.

```azurepowershell-interactive
Remove-AzResourceGroup -Name $resourceGroupName
Remove-Item -Path "$HOME\portal-dashboard-template-testvm.json"
```

## <a name="next-steps"></a>다음 단계

Az.Portal PowerShell 모듈에 포함된 cmdlet에 대한 자세한 내용은 다음을 참조하세요.

> [!div class="nextstepaction"]
> [Microsoft Azure PowerShell: 포털 대시보드 cmdlet](/powershell/module/Az.Portal/)
