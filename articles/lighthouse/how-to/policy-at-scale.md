---
title: 대규모로 위임된 구독에 Azure Policy 배포
description: Azure Lighthouse를 사용하면 여러 테넌트에 정책 정의 및 정책 할당을 배포할 수 있습니다.
ms.date: 06/13/2021
ms.topic: how-to
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e45feb45da482aa960516abec8c339d61016c758
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128673581"
---
# <a name="deploy-azure-policy-to-delegated-subscriptions-at-scale"></a>대규모로 위임된 구독에 Azure Policy 배포

서비스 공급자는 여러 고객 테넌트를 [Azure Lighthouse](../overview.md)에 온보딩했을 수 있습니다. Azure Lighthouse를 사용하여 서비스 공급자는 여러 테넌트에 걸쳐 대규모로 작업을 한 번에 수행할 수 있으므로 관리 작업을 보다 효율적으로 수행할 수 있습니다.

이 항목에서는 [Azure Policy](../../governance/policy/index.yml)를 사용하여 PowerShell 명령을 통해 여러 테넌트에서 정책 정의 및 정책 할당을 배포하는 방법을 보여 줍니다. 이 예제에서는 정책 정의를 통해 HTTPS 트래픽만 허용하여 스토리지 계정을 보호합니다.

> [!TIP]
> 이 항목에서는 서비스 공급자 및 고객만 언급하지만 [여러 테넌트를 관리하는 기업](../concepts/enterprise.md)에서도 동일한 프로세스를 사용할 수 있습니다.

## <a name="use-azure-resource-graph-to-query-across-customer-tenants"></a>Azure Resource Graph를 사용하여 고객 테넌트에서 쿼리

[Azure 리소스 Graph](../../governance/resource-graph/overview.md) 를 사용 하 여 관리 하는 고객 테 넌 트의 모든 구독에서 쿼리할 수 있습니다. 이 예제에서는 이러한 구독에서 현재 HTTPS 트래픽을 요구하지 않는 스토리지 계정을 식별합니다.  

```powershell
$MspTenant = "insert your managing tenantId here"

$subs = Get-AzSubscription

$ManagedSubscriptions = Search-AzGraph -Query "ResourceContainers | where type == 'microsoft.resources/subscriptions' | where tenantId != '$($mspTenant)' | project name, subscriptionId, tenantId" -subscription $subs.subscriptionId

Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Storage/storageAccounts' | project name, location, subscriptionId, tenantId, properties.supportsHttpsTrafficOnly" -subscription $ManagedSubscriptions.subscriptionId | convertto-json
```

## <a name="deploy-a-policy-across-multiple-customer-tenants"></a>여러 고객 테넌트 간에 정책 배포

아래 예제에서는 [Azure Resource Manager 템플릿](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/policy-enforce-https-storage/enforceHttpsStorage.json)을 사용하여 여러 고객 테넌트의 위임된 구독에서 정책 정의 및 정책 할당을 배포하는 방법을 보여 줍니다. 이 정책 정의에서는 모든 스토리지 계정이 HTTPS 트래픽을 사용해야 하므로 규정을 준수하지 않는 새 스토리지 계정을 만들지 못하게 하고 비규격으로 설정할 필요 없이 기존 스토리지 계정에 표시합니다.

```powershell
Write-Output "In total, there are $($ManagedSubscriptions.Count) delegated customer subscriptions to be managed"

foreach ($ManagedSub in $ManagedSubscriptions)
{
    Select-AzSubscription -SubscriptionId $ManagedSub.subscriptionId

    New-AzSubscriptionDeployment -Name mgmt `
                     -Location eastus `
                     -TemplateUri "https://raw.githubusercontent.com/Azure/Azure-Lighthouse-samples/master/templates/policy-enforce-https-storage/enforceHttpsStorage.json" `
                     -AsJob
}
```

> [!NOTE]
> 여러 테넌트에 정책을 배포할 수 있지만 현재는 이러한 테넌트의 비준수 리소스에 대한 [규정 준수 세부정보를 볼 수](../../governance/policy/how-to/determine-non-compliance.md#compliance-details) 없습니다.

## <a name="validate-the-policy-deployment"></a>정책 배포의 유효성 검사

Azure Resource Manager 템플릿을 배포한 후에는 위임된 구독 중 하나에서 **EnableHttpsTrafficOnly** 를 **false** 로 설정한 스토리지 계정을 만들어 정책 정의가 성공적으로 적용되었는지 확인할 수 있습니다. 정책 할당으로 인해 이 스토리지 계정을 만들 수 없습니다.  

```powershell
New-AzStorageAccount -ResourceGroupName (New-AzResourceGroup -name policy-test -Location eastus -Force).ResourceGroupName `
                     -Name (get-random) `
                     -Location eastus `
                     -EnableHttpsTrafficOnly $false `
                     -SkuName Standard_LRS `
                     -Verbose                  
```

## <a name="clean-up-resources"></a>리소스 정리

작업이 완료되면 배포에 의해 생성된 정책 정의 및 할당을 제거합니다.

```powershell
foreach ($ManagedSub in $ManagedSubscriptions)
{
    select-azsubscription -subscriptionId $ManagedSub.subscriptionId

    Remove-AzSubscriptionDeployment -Name mgmt -AsJob

    $Assignment = Get-AzPolicyAssignment | where-object {$_.Name -like "enforce-https-storage-assignment"}

    if ([string]::IsNullOrEmpty($Assignment))
    {
        Write-Output "Nothing to clean up - we're done"
    }
    else
    {

    Remove-AzPolicyAssignment -Name 'enforce-https-storage-assignment' -Scope "/subscriptions/$($ManagedSub.subscriptionId)" -Verbose

    Write-Output "Deployment has been deleted - we're done"
    }
}
```

## <a name="next-steps"></a>다음 단계

- [Azure Policy](../../governance/policy/index.yml)에 대해 알아봅니다.
- [테넌트 간 관리 환경](../concepts/cross-tenant-management-experience.md)에 대해 알아봅니다.
- 위임된 구독 내에서 [수정할 수 있는 정책을 배포](deploy-policy-remediation.md)하는 방법을 알아봅니다.
