---
title: 리소스 공급자 및 종류
description: Azure Resource Manager를 지원하는 리소스 공급자에 대해 설명합니다. 리소스를 호스트할 수 있는 스키마, 사용 가능한 API 버전 및 지역에 대해 설명합니다.
ms.topic: conceptual
ms.date: 11/15/2021
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: c048da5d7027885bf30b512d9e16e5851697c8b4
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132522653"
---
# <a name="azure-resource-providers-and-types"></a>Azure 리소스 공급자 및 종류

리소스를 배포할 때는 리소스 공급자 및 형식에 대한 정보를 자주 검색하게 됩니다. 예를 들어 키와 암호를 저장하려는 경우 Microsoft.KeyVault 리소스 공급자로 작업합니다. 이 리소스 공급자는 키 자격 증명 모음을 만드는 데 자격 증명 모음이라는 리소스 유형을 제공합니다.

리소스 종류의 이름은 **{resource-provider}/{resource-type}** 양식입니다. 키 자격 증명 모음의 리소스 유형은 **Microsoft.KeyVault/vaults** 입니다.

이 문서에서는 다음 방법을 설명합니다.

* Azure의 모든 리소스 공급자 보기
* 리소스 공급자의 등록 상태 확인
* 리소스 공급자 등록
* 리소스 공급자에 대한 리소스 종류 보기
* 리소스 종류에 대한 유효한 위치 보기
* 리소스 종류에 대한 유효한 API 버전 보기

Azure Portal, Azure PowerShell 또는 Azure CLI를 통해 해당 단계를 수행할 수 있습니다.

리소스 공급자를 Azure 서비스로 매핑하는 목록은 [Azure 서비스용 리소스 공급자](azure-services-resource-providers.md)를 참조하세요.

## <a name="register-resource-provider"></a>리소스 공급자 등록

리소스 공급자를 사용하기 전에 Azure 구독을 리소스 공급자에 대해 등록해야 합니다. 등록을 통해 구독이 리소스 공급자에서 작동하도록 구성합니다. 

> [!IMPORTANT]
> 리소스 공급자는 사용할 준비가 되었을 경우에만 등록합니다. 등록 단계를 통해 구독 내에서 최소 권한을 유지할 수 있습니다. 악의적인 사용자는 등록되지 않은 리소스 공급자를 사용할 수 없습니다.

일부 리소스 공급자는 기본적으로 등록됩니다. 기본적으로 등록된 리소스 공급자 목록은 [Azure 서비스의 리소스 공급자](azure-services-resource-providers.md)를 참조하세요.

다른 리소스 공급자는 특정 작업을 수행하면 자동으로 등록됩니다. 포털을 통해 리소스를 만드는 경우 일반적으로 리소스 공급자가 사용자에게 등록됩니다. Azure Resource Manager 템플릿 또는 Bicep 파일을 배포하면 템플릿에 정의된 리소스 공급자가 자동으로 등록됩니다. 그러나 템플릿의 리소스가 템플릿에 없는 지원 리소스(예: 모니터링 또는 보안 리소스)를 만드는 경우 해당 리소스 공급자를 수동으로 등록해야 합니다.

다른 시나리오의 경우 리소스 공급자를 수동으로 등록해야 할 수 있습니다.

> [!IMPORTANT]
> 애플리케이션 코드는 **등록** 상태에 있는 리소스 공급자에 대한 **리소스 생성을 차단하지 않아야 합니다**. 리소스 공급자를 등록하면 지원되는 각 지역에 대해 작업이 개별적으로 수행됩니다. 지역에 리소스를 생성하려면 해당 지역에서만 등록을 완료하면 됩니다. 등록 상태에서 리소스 공급자를 차단하지 않으면 모든 지역이 완료될 때까지 기다리는 것보다 훨씬 빨리 애플리케이션을 계속할 수 있습니다.

리소스 공급자에 대한 `/register/action` 작업을 수행하기 위해서는 권한이 있어야 합니다. 해당 권한은 기여자 및 소유자 역할에 포함되어 있습니다.

구독에 해당 리소스 공급자의 리소스 종류가 아직 포함되어 있으면 해당 리소스 공급자를 등록 취소할 수 없습니다.

## <a name="azure-portal"></a>Azure portal

### <a name="register-resource-provider"></a>리소스 공급자 등록

모든 리소스 공급자와 구독 등록 상태를 보려면 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. Azure Portal 메뉴에서 **구독** 을 검색합니다. 사용 가능한 옵션에서 선택합니다.

   :::image type="content" source="./media/resource-providers-and-types/search-subscriptions.png" alt-text="구독을 검색":::합니다.

1. 보려는 구독을 선택합니다.

   :::image type="content" source="./media/resource-providers-and-types/select-subscription.png" alt-text="구독을 선택":::합니다.

1. 왼쪽 메뉴의 **설정** 에서 **리소스 공급자** 를 선택합니다.

   :::image type="content" source="./media/resource-providers-and-types/select-resource-providers.png" alt-text="리소스 공급자를 선택":::합니다.

6. 등록할 리소스 공급자를 찾고 **등록** 을 선택합니다. 구독에서 최소 권한을 유지하려면 사용할 준비가 된 리소스 공급자만 등록합니다.

   :::image type="content" source="./media/resource-providers-and-types/register-resource-provider.png" alt-text="리소스 공급자를 등록":::합니다.

> [!IMPORTANT]
> [앞서 언급한](#register-resource-provider) 것처럼 **등록** 상태에 있는 리소스 공급자에 대해 **리소스 생성을 차단하지 않아야 합니다**. 등록 상태에서 리소스 공급자를 차단하지 않으면 모든 지역이 완료될 때까지 기다리는 것보다 훨씬 빨리 애플리케이션을 계속할 수 있습니다.


### <a name="view-resource-provider"></a>리소스 공급자 보기

특정 리소스 공급자에 대한 정보를 보려면 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. Azure Portal 메뉴에서 **모든 서비스** 를 선택합니다.
3. **모든 서비스** 상자에서 **리소스 탐색기** 를 입력한 다음, **리소스 탐색기** 를 선택합니다.

    ![모든 서비스 선택](./media/resource-providers-and-types/select-resource-explorer.png)

4. 오른쪽 화살표를 선택하여 **공급자** 를 확장합니다.

    ![공급자 선택](./media/resource-providers-and-types/select-providers.png)

5. 보려는 리소스 공급자 및 리소스 종류를 펼칩니다.

    ![리소스 종류 선택](./media/resource-providers-and-types/select-resource-type.png)

6. 리소스 관리자는 모든 지역에서 지원되지만 배포한 리소스는 모든 지역에서 지원되지 않을 수 있습니다. 또한 구독에 리소스를 지원하는 일부 지역을 사용하지 못하도록 하는 제한 사항이 있을 수 있습니다. 리소스 탐색기에는 리소스 종류에 대한 유효한 위치가 표시됩니다.

    ![위치 표시](./media/resource-providers-and-types/show-locations.png)

7. API 버전은 리소스 공급자가 릴리스하는 REST API 작업의 버전에 해당합니다. 리소스 공급자는 새 기능을 사용하도록 설정할 때 새 버전의 REST API를 릴리스합니다. 리소스 탐색기에는 리소스 종류에 대한 유효한 API 버전이 표시됩니다.

    ![API 버전 표시](./media/resource-providers-and-types/show-api-versions.png)

## <a name="azure-powershell"></a>Azure PowerShell

Azure의 모든 리소스 공급자와 구독에 대한 등록 상태를 보려면 다음을 사용합니다.

```azurepowershell-interactive
Get-AzResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState
```

이 명령은 다음을 반환합니다.

```output
ProviderNamespace                RegistrationState
-------------------------------- ------------------
Microsoft.ClassicCompute         Registered
Microsoft.ClassicNetwork         Registered
Microsoft.ClassicStorage         Registered
Microsoft.CognitiveServices      Registered
...
```

구독에 대해 등록된 모든 리소스 공급자를 보려면 다음을 사용합니다.

```azurepowershell-interactive
 Get-AzResourceProvider -ListAvailable | Where-Object RegistrationState -eq "Registered" | Select-Object ProviderNamespace, RegistrationState | Sort-Object ProviderNamespace
```

구독에서 최소 권한을 유지하려면 사용할 준비가 된 리소스 공급자만 등록합니다. 리소스 공급자를 등록하려면 다음을 사용합니다.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
```

이 명령은 다음을 반환합니다.

```output
ProviderNamespace : Microsoft.Batch
RegistrationState : Registering
ResourceTypes     : {batchAccounts, operations, locations, locations/quotas}
Locations         : {West Europe, East US, East US 2, West US...}
```

> [!IMPORTANT]
> [앞서 언급한](#register-resource-provider) 것처럼 **등록** 상태에 있는 리소스 공급자에 대해 **리소스 생성을 차단하지 않아야 합니다**. 등록 상태에서 리소스 공급자를 차단하지 않으면 모든 지역이 완료될 때까지 기다리는 것보다 훨씬 빨리 애플리케이션을 계속할 수 있습니다.

특정 리소스 공급자에 대한 정보를 보려면 다음을 사용합니다.

```azurepowershell-interactive
Get-AzResourceProvider -ProviderNamespace Microsoft.Batch
```

이 명령은 다음을 반환합니다.

```output
{ProviderNamespace : Microsoft.Batch
RegistrationState : Registered
ResourceTypes     : {batchAccounts}
Locations         : {West Europe, East US, East US 2, West US...}

...
```

리소스 공급자에 대한 리소스 종류를 보려면 다음을 사용합니다.

```azurepowershell-interactive
(Get-AzResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes.ResourceTypeName
```

이 명령은 다음을 반환합니다.

```output
batchAccounts
operations
locations
locations/quotas
```

API 버전은 리소스 공급자가 릴리스하는 REST API 작업의 버전에 해당합니다. 리소스 공급자는 새 기능을 사용하도록 설정할 때 새 버전의 REST API를 릴리스합니다.

리소스 종류의 사용 가능한 API 버전을 가져오려면 다음을 사용합니다.

```azurepowershell-interactive
((Get-AzResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes | Where-Object ResourceTypeName -eq batchAccounts).ApiVersions
```

이 명령은 다음을 반환합니다.

```output
2017-05-01
2017-01-01
2015-12-01
2015-09-01
2015-07-01
```

리소스 관리자는 모든 지역에서 지원되지만 배포한 리소스는 모든 지역에서 지원되지 않을 수 있습니다. 또한 구독에 리소스를 지원하는 일부 지역을 사용하지 못하도록 하는 제한 사항이 있을 수 있습니다.

리소스 종류의 지원되는 위치를 가져오려면 다음을 사용합니다.

```azurepowershell-interactive
((Get-AzResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes | Where-Object ResourceTypeName -eq batchAccounts).Locations
```

이 명령은 다음을 반환합니다.

```output
West Europe
East US
East US 2
West US
...
```

## <a name="azure-cli"></a>Azure CLI

Azure의 모든 리소스 공급자와 구독에 대한 등록 상태를 보려면 다음을 사용합니다.

```azurecli-interactive
az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
```

이 명령은 다음을 반환합니다.

```output
Provider                         Status
-------------------------------- ----------------
Microsoft.ClassicCompute         Registered
Microsoft.ClassicNetwork         Registered
Microsoft.ClassicStorage         Registered
Microsoft.CognitiveServices      Registered
...
```

구독에 대해 등록된 모든 리소스 공급자를 보려면 다음을 사용합니다.

```azurecli-interactive
az provider list --query "sort_by([?registrationState=='Registered'].{Provider:namespace, Status:registrationState}, &Provider)" --out table
```

구독에서 최소 권한을 유지하려면 사용할 준비가 된 리소스 공급자만 등록합니다. 리소스 공급자를 등록하려면 다음을 사용합니다.

```azurecli-interactive
az provider register --namespace Microsoft.Batch
```

이 명령은 등록이 진행 중이라는 메시지를 반환합니다.

특정 리소스 공급자에 대한 정보를 보려면 다음을 사용합니다.

```azurecli-interactive
az provider show --namespace Microsoft.Batch
```

이 명령은 다음을 반환합니다.

```output
{
    "id": "/subscriptions/####-####/providers/Microsoft.Batch",
    "namespace": "Microsoft.Batch",
    "registrationsState": "Registering",
    "resourceTypes:" [
        ...
    ]
}
```

> [!IMPORTANT]
> [앞서 언급한](#register-resource-provider) 것처럼 **등록** 상태에 있는 리소스 공급자에 대해 **리소스 생성을 차단하지 않아야 합니다**. 등록 상태에서 리소스 공급자를 차단하지 않으면 모든 지역이 완료될 때까지 기다리는 것보다 훨씬 빨리 애플리케이션을 계속할 수 있습니다.

리소스 공급자에 대한 리소스 종류를 보려면 다음을 사용합니다.

```azurecli-interactive
az provider show --namespace Microsoft.Batch --query "resourceTypes[*].resourceType" --out table
```

이 명령은 다음을 반환합니다.

```output
Result
---------------
batchAccounts
operations
locations
locations/quotas
```

API 버전은 리소스 공급자가 릴리스하는 REST API 작업의 버전에 해당합니다. 리소스 공급자는 새 기능을 사용하도록 설정할 때 새 버전의 REST API를 릴리스합니다.

리소스 종류의 사용 가능한 API 버전을 가져오려면 다음을 사용합니다.

```azurecli-interactive
az provider show --namespace Microsoft.Batch --query "resourceTypes[?resourceType=='batchAccounts'].apiVersions | [0]" --out table
```

이 명령은 다음을 반환합니다.

```output
Result
---------------
2017-05-01
2017-01-01
2015-12-01
2015-09-01
2015-07-01
```

리소스 관리자는 모든 지역에서 지원되지만 배포한 리소스는 모든 지역에서 지원되지 않을 수 있습니다. 또한 구독에 리소스를 지원하는 일부 지역을 사용하지 못하도록 하는 제한 사항이 있을 수 있습니다.

리소스 종류의 지원되는 위치를 가져오려면 다음을 사용합니다.

```azurecli-interactive
az provider show --namespace Microsoft.Batch --query "resourceTypes[?resourceType=='batchAccounts'].locations | [0]" --out table
```

이 명령은 다음을 반환합니다.

```output
Result
---------------
West Europe
East US
East US 2
West US
...
```

## <a name="next-steps"></a>다음 단계

* 리소스 관리자 템플릿을 만드는 방법에 대한 자세한 내용은 [Azure 리소스 관리자 템플릿 작성](../templates/syntax.md)을 참조하세요. 
* 리소스 공급자 템플릿 스키마를 보려면 [템플릿 참조](/azure/templates/)를 참조하세요.
* 리소스 공급자를 Azure 서비스로 매핑하는 목록은 [Azure 서비스용 리소스 공급자](azure-services-resource-providers.md)를 참조하세요.
* 리소스 공급자에 대한 작업을 보려면 [Azure REST API](/rest/api/)를 참조하세요.