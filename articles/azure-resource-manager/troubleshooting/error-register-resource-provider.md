---
title: 리소스 공급자 등록 오류
description: Azure Resource Manager를 사용 하 여 배포 된 리소스에 대 한 Azure 리소스 공급자 등록 오류를 해결 하는 방법을 설명 합니다.
ms.topic: troubleshooting
ms.date: 11/18/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 3a4a5f55a711e31c62ab574117eebdd2affe26e3
ms.sourcegitcommit: b00a2d931b0d6f1d4ea5d4127f74fc831fb0bca9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2021
ms.locfileid: "132862455"
---
# <a name="resolve-errors-for-resource-provider-registration"></a>리소스 공급자 등록 오류 해결

이 문서에서는 이전에 구독에서 사용 하지 않은 리소스 공급자를 사용 하는 경우 발생할 수 있는 오류에 대해 설명 합니다.

## <a name="symptom"></a>증상

리소스를 배포 하면 다음과 같은 오류 코드 및 메시지가 표시 될 수 있습니다.

```Output
Code: NoRegisteredProviderFound
Message: No registered resource provider found for location {location}
and API version {api-version} for type {resource-type}.
```

또는 다음과 유사한 메시지를 받을 수 있습니다.

```Output
Code: MissingSubscriptionRegistration
Message: The subscription is not registered to use namespace {resource-provider-namespace}
```

오류 메시지는 지원되는 위치 및 API 버전에 대해 제안을 제공해야 합니다. 제안 된 값을 사용 하도록 템플릿을 변경할 수 있습니다. 대부분의 공급자는 Microsoft Azure 포털 또는 명령줄 인터페이스에 의해 자동으로 등록 되지만 모두는 자동으로 등록 되지 않습니다. 이전에 특정 리소스 공급자를 사용 하지 않은 경우 해당 공급자를 등록 해야 할 수 있습니다.

VM (가상 컴퓨터) 자동 종료가 사용 하지 않도록 설정 된 경우 다음과 유사한 오류 메시지가 표시 될 수 있습니다.

```Output
Code: AuthorizationFailed
Message: The client '<identifier>' with object id '<identifier>' does not have authorization to perform
action 'Microsoft.Compute/virtualMachines/read' over scope ...
```

ARM 템플릿 또는 Bicep 파일에 없는 리소스 공급자에 대해 예기치 않은 오류가 발생할 수 있습니다. 이 오류는 다른 지원 리소스를 만드는 리소스를 배포할 때 발생할 수 있습니다. 예를 들어 템플릿의 리소스는 모니터링 또는 보안 리소스를 추가 합니다. 오류 메시지는 지원 리소스에 대 한 등록 해야 하는 리소스 공급자 네임 스페이스를 나타냅니다.

## <a name="cause"></a>원인

이러한 오류가 표시되는 이유는 다음과 같습니다.

- 필요한 리소스 공급자가 구독에 등록 되지 않았습니다.
- 리소스 종류에 대해 지원 되지 않는 API 버전입니다.
- 리소스 종류에 대해 위치가 지원 되지 않습니다.
- VM 자동 종료의 경우 `Microsoft.DevTestLab` 리소스 공급자를 등록 해야 합니다.

## <a name="solution"></a>솔루션

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI를 사용 하 여 리소스 공급자의 등록 상태에 대 한 정보를 가져오고 리소스 공급자를 등록할 수 있습니다.

[Az provider list](/cli/azure/provider#az_provider_list) 를 사용 하 여 구독의 리소스 공급자에 대 한 등록 상태를 표시 합니다. 이 예에서는 `--output table` 매개 변수를 사용 하 여 가독성을 위해 출력을 필터링 합니다. 매개 변수를 생략 하 여 모든 속성을 볼 수 있습니다.

다음 명령은 구독의 모든 리소스 공급자를 나열 하 고, 또는 인지 여부를 나열 합니다 `Registered` `NotRegistered` .

```azurecli-interactive
az provider list --output table
```

등록 상태를 기준으로 출력을 필터링 할 수 있습니다. 쿼리 값을 또는로 `Registered` 바꿉니다 `NotRegistered` .

```azurecli-interactive
az provider list --query "[?registrationState=='Registered']" --output table
```

특정 리소스 공급자에 대 한 등록 상태를 가져옵니다.

```azurecli-interactive
az provider list --query "[?namespace=='Microsoft.Compute']" --output table
```

리소스 공급자를 등록 하려면 [az provider register](/cli/azure/provider#az_provider_register) 명령을 사용 하 고 등록할 _네임 스페이스_ 를 지정 합니다.

```azurecli-interactive
az provider register --namespace Microsoft.Cdn
```

리소스 형식의 지원 되는 위치를 가져오려면 [az provider show](/cli/azure/provider#az_provider_show)를 사용 합니다.

```azurecli-interactive
az provider show --namespace Microsoft.Web --query "resourceTypes[?resourceType=='sites'].locations"
```

리소스 형식의 지원 되는 API 버전을 가져옵니다.

```azurecli-interactive
az provider show --namespace Microsoft.Web --query "resourceTypes[?resourceType=='sites'].apiVersions"
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Azure PowerShell를 사용 하 여 리소스 공급자의 등록 상태에 대 한 정보를 가져오고 리소스 공급자를 등록할 수 있습니다.

[AzResourceProvider](/powershell/module/az.resources/get-azresourceprovider) 를 사용 하 여 구독의 리소스 공급자에 대 한 등록 상태를 표시 합니다.

다음 명령은 구독의 모든 리소스 공급자를 나열 하 고, 또는 인지 여부를 나열 합니다 `Registered` `NotRegistered` .

```azurepowershell-interactive
Get-AzResourceProvider -ListAvailable
```

리소스 공급자만 나열 하려면 `Registered` `ListAvailable` 매개 변수를 생략 합니다. 등록 상태별로 출력을 필터링 할 수도 있습니다. 값을 또는로 `Registered` 바꿉니다 `NotRegistered` .

```azurepowershell-interactive
Get-AzResourceProvider -ListAvailable |
  Where-Object -Property RegistrationState -EQ -Value "Registered"
```

특정 리소스 공급자에 대 한 등록 상태를 가져옵니다.

```azurepowershell-interactive
Get-AzResourceProvider -ListAvailable |
  Where-Object -Property ProviderNamespace -Like -Value "Microsoft.Compute"
```

공급자를 등록 하려면 [AzResourceProvider](/powershell/module/az.resources/register-azresourceprovider) 를 사용 하 고 리소스 공급자의 이름을 제공 합니다.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace "Microsoft.Cdn"
```

리소스 형식의 지원 되는 위치를 가져옵니다.

```azurepowershell-interactive
((Get-AzResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes |
  Where-Object -Property ResourceTypeName -EQ -Value "sites").Locations
```

리소스 형식의 지원 되는 API 버전을 가져옵니다.

```azurepowershell-interactive
((Get-AzResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes |
  Where-Object -Property ResourceTypeName -EQ -Value "sites").ApiVersions
```

# <a name="portal"></a>[포털](#tab/azure-portal)

등록 상태를 볼 수 있으며 포털을 통해 리소스 공급자 네임스페이스를 등록할 수 있습니다.

1. [Azure 포털](https://portal.azure.com/)에 로그인합니다.

1. 검색 상자에 _구독_ 을 입력 합니다. 또는 구독을 최근 본 경우 **구독** 을 선택 합니다.

    :::image type="content" source="media/error-register-resource-provider/select-subscriptions.png" alt-text="구독을 선택 하는 방법을 보여 주는 스크린샷":::


1. 리소스 공급자를 등록 하는 데 사용 하려는 구독을 선택 합니다.

    :::image type="content" source="media/error-register-resource-provider/select-subscription-to-register.png" alt-text="리소스 공급자를 등록 하는 데 사용 되는 구독에 대 한 링크의 스크린샷":::

1. 리소스 공급자 목록을 보려면 **설정** **리소스 공급자** 를 선택 합니다.

    :::image type="content" source="media/error-register-resource-provider/select-resource-providers.png" alt-text="구독의 리소스 공급자 목록 스크린샷":::

1. 리소스 공급자를 등록 하려면 리소스 공급자를 선택 하 고 **등록** 을 선택 합니다.

    :::image type="content" source="media/error-register-resource-provider/select-register.png" alt-text="선택한 리소스 공급자를 등록 하는 단추의 스크린샷":::

---
