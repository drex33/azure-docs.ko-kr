---
title: PowerShell을 사용한 Azure Service Bus 리소스 관리 | Microsoft Docs
description: 이 문서에서는 Azure PowerShell 모듈을 사용하여 Service Bus 엔터티(네임스페이스, 큐, 토픽, 구독)를 만들고 관리하는 방법을 설명합니다.
ms.topic: article
ms.date: 09/20/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 41fe10c13c77871845cadf93890768064f6dc143
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128627979"
---
# <a name="use-powershell-to-manage-service-bus-resources"></a>PowerShell을 사용하여 Service Bus 리소스 관리

Microsoft Azure PowerShell은 Azure 서비스의 배포와 관리를 제어하고 자동화하는 데 사용할 수 있는 스크립팅 환경입니다. 이 문서에서는 [Service Bus Resource Manager PowerShell 모듈](/powershell/module/az.servicebus)을 사용하여 로컬 Azure PowerShell 콘솔 또는 스크립트를 통해 Service Bus 엔터티(네임스페이스, 쿼리, 토픽 및 구독)를 프로비전하고 관리하는 방법을 설명합니다.

Azure Resource Manager 템플릿을 사용하여 Service Bus 엔터티를 관리할 수도 있습니다. 자세한 내용은 [Azure Resource Manager 템플릿을 사용하여 Service Bus 리소스를 만드는 방법](service-bus-resource-manager-overview.md) 문서를 참조하세요.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>필수 구성 요소

시작하려면 다음과 같은 필수 조건을 갖추어야 합니다.

* Azure 구독 구독을 얻는 방법에 대한 자세한 내용은 [구매 옵션][purchase options], [구성원 제안][member offers] 또는 [무료 계정][free account]을 참조하세요.
* Azure PowerShell이 설치된 컴퓨터 관련 지침은 [Azure PowerShell Cmdlet 시작](/powershell/azure/get-started-azureps)을 참조하세요.
* PowerShell 스크립트, NuGet 패키지 및 .NET Framework 전반에 대한 지식

## <a name="get-started"></a>시작

첫 번째 단계는 PowerShell을 사용하여 Azure 계정 및 Azure 구독에 로그인하는 것입니다. [Azure PowerShell cmdlet 시작](/powershell/azure/get-started-azureps)의 지침에 따라 Azure 계정에 로그인하고, Azure 구독에서 리소스를 검색하고 액세스합니다.

## <a name="provision-a-service-bus-namespace"></a>Service Bus 네임스페이스 프로비전

Service Bus 네임스페이스를 사용하는 경우 [Get-AzServiceBusNamespace](/powershell/module/az.servicebus/get-azservicebusnamespace), [New-AzServiceBusNamespace](/powershell/module/az.servicebus/new-azservicebusnamespace), [Remove-AzServiceBusNamespace](/powershell/module/az.servicebus/remove-azservicebusnamespace), [Set-AzServiceBusNamespace](/powershell/module/az.servicebus/set-azservicebusnamespace) cmdlet을 사용할 수 있습니다.

이 예제에서는 스크립트에 `$Namespace`과(와) `$Location`(이)라는 몇 가지 로컬 변수를 만듭니다.

* `$Namespace`은(는) 사용하려는 Service Bus 네임스페이스의 이름입니다.
* `$Location`은(는) 네임스페이스를 프로비전할 데이터 센터를 식별합니다.
* `$CurrentNamespace`에는 검색하거나 만드는 참조 네임스페이스가 저장됩니다.

실제 스크립트에서 `$Namespace` 및 `$Location`은(는) 매개 변수로 전달할 수 있습니다.

스크립트의 이 부분은 다음을 수행합니다.

1. 지정된 이름의 Service Bus 네임스페이스 검색을 시도합니다.
2. 네임스페이스가 있으면 발견된 항목을 보고합니다.
3. 네임스페이스가 없으면 만든 다음 새로 만든 네임스페이스를 검색합니다.
   
    ``` powershell
    # Query to see if the namespace currently exists
    $CurrentNamespace = Get-AzServiceBusNamespace -ResourceGroup $ResGrpName -NamespaceName $Namespace
   
    # Check if the namespace already exists or needs to be created
    if ($CurrentNamespace)
    {
        Write-Host "The namespace $Namespace already exists in the $Location region:"
        # Report what was found
        Get-AzServiceBusNamespace -ResourceGroup $ResGrpName -NamespaceName $Namespace
    }
    else
    {
        Write-Host "The $Namespace namespace does not exist."
        Write-Host "Creating the $Namespace namespace in the $Location region..."
        New-AzServiceBusNamespace -ResourceGroup $ResGrpName -NamespaceName $Namespace -Location $Location
        $CurrentNamespace = Get-AzServiceBusNamespace -ResourceGroup $ResGrpName -NamespaceName $Namespace
        Write-Host "The $Namespace namespace in Resource Group $ResGrpName in the $Location region has been successfully created."
                
    }
    ```

### <a name="create-a-namespace-authorization-rule"></a>네임스페이스 권한 부여 규칙 만들기

다음 예제에서는 [New-AzServiceBusAuthorizationRule](/powershell/module/az.servicebus/new-azservicebusauthorizationrule), [Get-AzServiceBusAuthorizationRule](/powershell/module/az.servicebus/get-azservicebusauthorizationrule), [Set-AzServiceBusAuthorizationRule](/powershell/module/az.servicebus/set-azservicebusauthorizationrule), [Remove-AzServiceBusAuthorizationRule](/powershell/module/az.servicebus/remove-azservicebusauthorizationrule) cmdlet을 사용하여 네임스페이스 권한 부여 규칙을 관리하는 방법을 보여 줍니다.

```powershell
# Query to see if rule exists
$CurrentRule = Get-AzServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthorizationRuleName $AuthRule

# Check if the rule already exists or needs to be created
if ($CurrentRule)
{
    Write-Host "The $AuthRule rule already exists for the namespace $Namespace."
}
else
{
    Write-Host "The $AuthRule rule does not exist."
    Write-Host "Creating the $AuthRule rule for the $Namespace namespace..."
    New-AzServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthorizationRuleName $AuthRule -Rights @("Listen","Send")
    $CurrentRule = Get-AzServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthorizationRuleName $AuthRule
    Write-Host "The $AuthRule rule for the $Namespace namespace has been successfully created."

    Write-Host "Setting rights on the namespace"
    $authRuleObj = Get-AzServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthorizationRuleName $AuthRule

    Write-Host "Remove Send rights"
    $authRuleObj.Rights.Remove("Send")
    Set-AzServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthRuleObj $authRuleObj

    Write-Host "Add Send and Manage rights to the namespace"
    $authRuleObj.Rights.Add("Send")
    Set-AzServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthRuleObj $authRuleObj
    $authRuleObj.Rights.Add("Manage")
    Set-AzServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthRuleObj $authRuleObj

    Write-Host "Show value of primary key"
    $CurrentKey = Get-AzServiceBusKey -ResourceGroup $ResGrpName -NamespaceName $Namespace -Name $AuthRule
        
    Write-Host "Remove this authorization rule"
    Remove-AzServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -Name $AuthRule
}
```

## <a name="create-a-queue"></a>큐 만들기

큐 또는 토픽을 만들려면 이전 섹션에서와 같이 스크립트를 사용해서 네임스페이스를 확인합니다. 그런 후 큐를 만듭니다.

```powershell
# Check if queue already exists
$CurrentQ = Get-AzServiceBusQueue -ResourceGroup $ResGrpName -NamespaceName $Namespace -QueueName $QueueName

if($CurrentQ)
{
    Write-Host "The queue $QueueName already exists in the $Location region:"
}
else
{
    Write-Host "The $QueueName queue does not exist."
    Write-Host "Creating the $QueueName queue in the $Location region..."
    New-AzServiceBusQueue -ResourceGroup $ResGrpName -NamespaceName $Namespace -QueueName $QueueName -EnablePartitioning $True
    $CurrentQ = Get-AzServiceBusQueue -ResourceGroup $ResGrpName -NamespaceName $Namespace -QueueName $QueueName
    Write-Host "The $QueueName queue in Resource Group $ResGrpName in the $Location region has been successfully created."
}
```

### <a name="modify-queue-properties"></a>큐 속성 수정

이전 섹션의 스크립트를 실행한 후 [Set-AzServiceBusQueue](/powershell/module/az.servicebus/set-azservicebusqueue) cmdlet을 사용하여 다음 예제와 같이 큐의 속성을 업데이트할 수 있습니다.

```powershell
$CurrentQ.DeadLetteringOnMessageExpiration = $True
$CurrentQ.MaxDeliveryCount = 7
$CurrentQ.MaxSizeInMegabytes = 2048
$CurrentQ.EnableExpress = $True

Set-AzServiceBusQueue -ResourceGroup $ResGrpName -NamespaceName $Namespace -QueueName $QueueName -QueueObj $CurrentQ
```

## <a name="provisioning-other-service-bus-entities"></a>다른 Service Bus 엔터티 프로비전

[Service Bus PowerShell 모듈](/powershell/module/az.servicebus)을 사용하여 토픽 및 구독 같은 다른 엔터티를 프로비전할 수 있습니다. 이러한 cmdlet은 이전 섹션에 설명된 큐 만들기 cmdlet과 구문상 유사합니다.

## <a name="next-steps"></a>다음 단계

- [여기](/powershell/module/az.servicebus)에서 전체 Service Bus Resource Manager PowerShell 모듈 설명서를 참조하세요. 이 페이지에는 사용 가능한 모든 cmdlet이 표시됩니다.
- Azure Resource Manager 템플릿 사용에 대한 자세한 내용은 [Azure Resource Manager 템플릿을 사용하여 Service Bus 리소스를 만드는 방법](service-bus-resource-manager-overview.md) 문서를 참조하세요.
- [Service Bus .NET 관리 라이브러리](service-bus-management-libraries.md)에 대한 정보

다음 블로그 게시물에 설명된 것처럼 Service Bus 엔터티를 관리하는 몇 가지 다른 방법이 있습니다.

* [PowerShell 스크립트를 사용하여 Service Bus 큐, 토픽 및 구독을 만드는 방법](/archive/blogs/paolos/how-to-create-service-bus-queues-topics-and-subscriptions-using-a-powershell-script)
* [PowerShell 스크립트를 사용하여 Service Bus 네임스페이스 및 Event Hub를 만드는 방법](/archive/blogs/paolos/how-to-create-a-service-bus-namespace-and-an-event-hub-using-a-powershell-script)

<!--Anchors-->

[purchase options]: https://azure.microsoft.com/pricing/purchase-options/
[member offers]: https://azure.microsoft.com/pricing/member-offers/
[free account]: https://azure.microsoft.com/pricing/free-trial/
