---
title: Azure 구독에서 미리 보기 기능 설정
description: Azure 구독에서 리소스 공급자의 미리 보기 기능을 나열, 등록 또는 등록 취소하는 방법을 설명합니다.
ms.topic: how-to
ms.date: 08/18/2021
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 9d7b705e4db7c6556eea4b9fd3cbe1916ec257a4
ms.sourcegitcommit: 1deb51bc3de58afdd9871bc7d2558ee5916a3e89
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2021
ms.locfileid: "122531039"
---
# <a name="set-up-preview-features-in-azure-subscription"></a>Azure 구독에서 미리 보기 기능 설정

이 문서에서는 Azure 구독에서 미리 보기 기능을 관리하는 방법을 보여줍니다. 미리 보기 기능을 사용하면 새 기능이 릴리스되기 전에 옵트인할 수 있습니다. 일부 미리 보기 기능은 옵트인하려는 모든 사용자에게 제공됩니다. 다른 미리 보기 기능을 사용하려면 제품 팀의 승인이 필요합니다.

AFEC(Azure Feature Exposure Control)는 [Microsoft.Features](/rest/api/resources/features) 네임스페이스를 통해 제공됩니다. 리소스 ID에 대한 미리 보기 기능의 형식은 다음과 같습니다.

`Microsoft.Features/providers/{resourceProviderNamespace}/features/{featureName}`

## <a name="list-preview-features"></a>미리 보기 기능 나열

Azure 구독의 모든 미리 보기 기능과 등록 상태를 나열할 수 있습니다.

# <a name="portal"></a>[포털](#tab/azure-portal)

기능을 소유하는 서비스에서 미리 보기 기능 관리 환경을 명시적으로 옵트인한 경우에만 포털에 미리 보기 기능이 표시됩니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. 검색 상자에 _subscriptions_ 를 입력하고 **구독** 을 선택합니다.

    :::image type="content" source="./media/preview-features/search.png" alt-text="Azure Portal 검색":::

1. 구독 이름의 링크를 선택합니다.

    :::image type="content" source="./media/preview-features/subscriptions.png" alt-text="Azure 구독 선택":::

1. 왼쪽 메뉴의 **설정** 에서 **미리 보기 기능** 을 선택합니다.

    :::image type="content" source="./media/preview-features/preview-features-menu.png" alt-text="Azure 미리 보기 기능 메뉴":::

1. 사용 가능한 미리 보기 기능 목록과 현재 등록 상태가 표시됩니다.

    :::image type="content" source="./media/preview-features/preview-features-list.png" alt-text="미리 보기 기능의 Azure Portal 목록":::

1. **미리 보기 기능** 에서 **이름**, **상태** 또는 **형식** 을 기준으로 목록을 필터링할 수 있습니다.

    - **이름을 기준으로 필터링**: **표시 이름** 이 아닌 미리 보기 기능 이름의 텍스트가 포함되어야 합니다.
    - **상태**: 드롭다운 메뉴를 선택하여 상태를 선택합니다. 포털은 **등록 취소** 를 기준으로 필터링되지 않습니다.
    - **형식**: 드롭다운 메뉴를 선택하여 형식을 선택합니다.

    :::image type="content" source="./media/preview-features/filter.png" alt-text="Azure Portal 필터 미리 보기 기능":::

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

구독의 모든 미리 보기 기능을 나열하려면 [az feature list](/cli/azure/feature#az_feature_list) 명령을 사용합니다.

Azure CLI 기본 출력은 JSON입니다. 다른 출력 형식에 대한 자세한 내용은 [Azure CLI 명령에 대한 출력 형식](/cli/azure/format-output-azure-cli)을 참조하세요.

```azurecli-interactive
az feature list
```

```json
{
  "id": "/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Features/providers/
    Microsoft.Compute/features/InGuestPatchVMPreview",
  "name": "Microsoft.Compute/InGuestPatchVMPreview",
  "properties": {
    "state": "NotRegistered"
  },
  "type": "Microsoft.Features/providers/features"
}
```

특정 리소스 공급자 출력을 필터링하려면 `namespace` 매개 변수를 사용합니다. 이 예에서 `output` 매개 변수는 테이블 형식을 지정합니다.

```azurecli-interactive
az feature list --namespace Microsoft.Compute --output table
```

```Output
Name                                                RegistrationState
-------------------------------------------------   -------------------
Microsoft.Compute/AHUB                              Unregistered
Microsoft.Compute/AllowManagedDisksReplaceOSDisk    Registered
Microsoft.Compute/AllowPreReleaseRegions            Pending
Microsoft.Compute/InGuestPatchVMPreview             NotRegistered
```

특정 미리 보기 기능 출력을 필터링하려면 [az feature show](/cli/azure/feature#az_feature_show) 명령을 사용합니다.

```azurecli-interactive
az feature show --name InGuestPatchVMPreview --namespace Microsoft.Compute --output table
```

```Output
Name                                     RegistrationState
---------------------------------------  -------------------
Microsoft.Compute/InGuestPatchVMPreview  NotRegistered
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

구독의 모든 미리 보기 기능을 나열하려면 [Get-AzProviderFeature](/powershell/module/az.resources/get-azproviderfeature) cmdlet를 사용합니다.

```azurepowershell-interactive
Get-AzProviderFeature -ListAvailable
```

```Output
FeatureName      ProviderName     RegistrationState
-----------      ------------     -----------------
betaAccess       Microsoft.AAD    NotRegistered
previewAccess    Microsoft.AAD    Registered
tipAccess        Microsoft.AAD    Pending
testAccess       Microsoft.AAD    Unregistered
```

특정 리소스 공급자 출력을 필터링하려면 `ProviderNamespace` 매개 변수를 사용합니다. 기본 출력에는 등록된 기능만 표시됩니다. 리소스 공급자의 모든 미리 보기 기능을 표시하려면 `ProviderNamespace` 매개 변수와 함께 `ListAvailable` 매개 변수를 사용합니다.

```azurepowershell-interactive
Get-AzProviderFeature -ProviderNamespace "Microsoft.Compute" -ListAvailable
```

```Output
FeatureName                          ProviderName        RegistrationState
-----------                          ------------        -----------------
AHUB                                 Microsoft.Compute   Unregistered
AllowManagedDisksReplaceOSDisk       Microsoft.Compute   Registered
AllowPreReleaseRegions               Microsoft.Compute   Pending
InGuestPatchVMPreview                Microsoft.Compute   NotRegistered
```

`FeatureName` 매개 변수를 사용하여 특정 미리 보기 기능 출력을 필터링할 수 있습니다.

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName "InGuestPatchVMPreview" -ProviderNamespace "Microsoft.Compute"
```

```Output
FeatureName             ProviderName        RegistrationState
-----------             ------------        -----------------
InGuestPatchVMPreview   Microsoft.Compute   NotRegistered
```

---

## <a name="register-preview-feature"></a>미리 보기 기능 등록

Azure 구독에 미리 보기 기능을 등록하여 리소스 공급자의 추가 기능을 노출합니다. 일부 미리 보기 기능에는 승인이 필요합니다.

미리 보기 기능이 구독에 등록되면 **등록됨** 또는 **보류 중** 등 두 가지 상태 중 하나가 표시됩니다.

- 승인이 필요하지 않은 미리 보기 기능의 경우 상태는 **등록됨** 입니다.
- 미리 보기 기능에 승인이 필요한 경우 등록 상태는 **보류 중** 입니다.
  - 승인을 요청하려면 [Azure 지원 요청](../../azure-portal/supportability/how-to-create-azure-support-request.md)을 제출합니다.
  - 등록이 승인되면 미리 보기 기능 상태가 **등록됨** 으로 변경됩니다.

# <a name="portal"></a>[포털](#tab/azure-portal)

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. 검색 상자에 _subscriptions_ 를 입력하고 **구독** 을 선택합니다.
1. 구독 이름의 링크를 선택합니다.
1. 왼쪽 메뉴의 **설정** 에서 **미리 보기 기능** 을 선택합니다.
1. 등록하려는 미리 보기 기능의 링크를 선택합니다.
1. **등록** 을 선택합니다.

    :::image type="content" source="./media/preview-features/register.png" alt-text="Azure Portal에서 미리 보기 기능을 등록합니다.":::

1. **확인** 을 선택합니다.

**미리 보기 기능** 화면이 새로 고쳐지고 미리 보기 기능 **상태** 가 표시됩니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

미리 보기 기능을 등록하려면 [az feature register](/cli/azure/feature#az_feature_register) 명령을 사용합니다.

```azurecli-interactive
az feature register --name InGuestPatchVMPreview --namespace Microsoft.Compute
```

```json
{
  "id": "/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Features/providers/
    Microsoft.Compute/features/InGuestPatchVMPreview",
  "name": "Microsoft.Compute/InGuestPatchVMPreview",
  "properties": {
    "state": "Registering"
  },
  "type": "Microsoft.Features/providers/features"
}
```

등록 상태를 보려면 `az feature show` 명령을 사용합니다.

```azurecli-interactive
az feature show --name InGuestPatchVMPreview --namespace Microsoft.Compute --output table
```

```Output
Name                                     RegistrationState
---------------------------------------  -------------------
Microsoft.Compute/InGuestPatchVMPreview  Registered
```

> [!NOTE]
> register 명령을 실행하면 기능이 등록된 후 `az provider register --namespace <provider-name>`을 실행하여 변경 사항을 전파하라는 메시지가 표시됩니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

미리 보기 기능을 등록하려면 [Register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) cmdlet를 사용합니다.

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName "InGuestPatchVMPreview" -ProviderNamespace "Microsoft.Compute"
```

```Output
FeatureName             ProviderName        RegistrationState
-----------             ------------        -----------------
InGuestPatchVMPreview   Microsoft.Compute   Registering
```

등록 상태를 보려면 `Get-AzProviderFeature` cmdlet를 사용합니다.

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName "InGuestPatchVMPreview" -ProviderNamespace "Microsoft.Compute"
```

```Output
FeatureName             ProviderName        RegistrationState
-----------             ------------        -----------------
InGuestPatchVMPreview   Microsoft.Compute   Registered
```

---

## <a name="unregister-preview-feature"></a>미리 보기 기능 등록 취소

미리 보기 기능 사용을 마쳤으면 Azure 구독에서 등록 취소합니다. 기능을 등록 취소하면 두 가지 상태가 나타날 수 있습니다. 포털을 통해 등록 취소하면 상태가 **등록되지 않음** 으로 설정됩니다. Azure CLI, PowerShell 또는 REST API 통해 등록 취소하면 상태가 **등록 취소됨** 으로 설정됩니다. 포털에서는 기능 등록을 삭제하지만 명령에서는 기능을 등록 취소하므로 상태가 다릅니다. 두 경우 모두 구독에서 더 이상 이 기능을 사용할 수 없습니다. 두 경우 모두 기능을 다시 등록하여 다시 옵트인할 수 있습니다.

# <a name="portal"></a>[포털](#tab/azure-portal)

**미리 보기 기능** 에서 미리 보기 기능을 등록 취소할 수 있습니다. **상태** 는 **등록되지 않음** 으로 변경됩니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. 검색 상자에 _subscriptions_ 를 입력하고 **구독** 을 선택합니다.
1. 구독 이름의 링크를 선택합니다.
1. 왼쪽 메뉴의 **설정** 에서 **미리 보기 기능** 을 선택합니다.
1. 등록 취소하려는 미리 보기 기능의 링크를 선택합니다.
1. **등록 취소** 를 선택합니다.

    :::image type="content" source="./media/preview-features/unregister.png" alt-text="Azure Portal에서 미리 보기 기능을 등록 취소합니다.":::

1. **확인** 을 선택합니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

미리 보기 기능을 등록 취소하려면 [az feature unregister](/cli/azure/feature#az_feature_unregister) 명령을 사용합니다. `RegistrationState` 상태는 **등록 취소됨** 으로 변경됩니다.

```azurecli-interactive
az feature unregister --name InGuestPatchVMPreview --namespace Microsoft.Compute
```

```json
{
  "id": "/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Features/providers/
    Microsoft.Compute/features/InGuestPatchVMPreview",
  "name": "Microsoft.Compute/InGuestPatchVMPreview",
  "properties": {
    "state": "Unregistering"
  },
  "type": "Microsoft.Features/providers/features"
}
```

등록 취소 상태를 보려면 `az feature show` 명령을 사용합니다.

```azurecli-interactive
az feature show --name InGuestPatchVMPreview --namespace Microsoft.Compute --output table
```

```Output
Name                                     RegistrationState
---------------------------------------  -------------------
Microsoft.Compute/InGuestPatchVMPreview  Unregistered
```

> [!NOTE]
> unregister 명령을 실행하면 기능이 등록 취소된 후 `az provider register --namespace <provider-name>`을 실행하여 변경 사항을 전파하라는 메시지가 표시됩니다.

**등록 취소된** 미리 보기 기능을 찾으려면 다음 명령을 사용합니다. `<ResourceProvider.Name>`을 `Microsoft.Compute`과 같은 공급자 이름을 바꿉니다.

다음 예에서는 `Microsoft.Compute` 리소스 공급자의 **등록 취소된** 미리 보기 기능이 표시됩니다.

```azurecli-interactive
az feature list --namespace <ResourceProvider.Name> --query "[?properties.state=='Unregistered'].{Name:name, RegistrationState:properties.state}" --output table
```

```Output
Name                                     RegistrationState
---------------------------------------  -------------------
Microsoft.Compute/InGuestPatchVMPreview  Unregistered
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

미리 보기 기능을 등록 취소하려면 [Unregister-AzProviderFeature](/powershell/module/az.resources/unregister-azproviderfeature) cmdlet를 사용합니다. `RegistrationState` 상태는 **등록 취소됨** 으로 변경됩니다.

```azurepowershell-interactive
Unregister-AzProviderFeature -FeatureName "InGuestPatchVMPreview" -ProviderNamespace "Microsoft.Compute"
```

```Output
FeatureName             ProviderName        RegistrationState
-----------             ------------        -----------------
InGuestPatchVMPreview   Microsoft.Compute   Unregistering
```

등록 취소 상태를 보려면 `Get-AzProviderFeature` cmdlet를 사용합니다.

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName "InGuestPatchVMPreview" -ProviderNamespace "Microsoft.Compute"
```

```Output
FeatureName             ProviderName        RegistrationState
-----------             ------------        -----------------
InGuestPatchVMPreview   Microsoft.Compute   Unregistered
```

다음 예에서는 `Microsoft.Compute` 리소스 공급자의 **등록 취소된** 미리 보기 기능이 표시됩니다.

```azurepowershell-interactive
Get-AzProviderFeature  -ProviderNamespace "Microsoft.Compute" -ListAvailable | Where-Object { $_.RegistrationState -eq "Unregistered" }
```

```Output
FeatureName             ProviderName        RegistrationState
-----------             ------------        -----------------
InGuestPatchVMPreview   Microsoft.Compute   Unregistered
```

---

## <a name="next-steps"></a>다음 단계

- REST API 호출을 사용하여 미리 보기 기능을 나열, 등록 또는 등록 취소하려면 [기능](/rest/api/resources/features) 문서를 참조하세요.
- 리소스 공급자 등록 방법에 대한 자세한 내용은 [Azure 리소스 공급자 및 종류](resource-providers-and-types.md)를 참조하세요.
- 리소스 공급자를 Azure 서비스로 매핑하는 목록은 [Azure 서비스용 리소스 공급자](azure-services-resource-providers.md)를 참조하세요.
