---
title: 삭제된 Cognitive Services 리소스 복구
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 이미 삭제된 Cognitive Services 리소스를 복구하는 방법에 대한 지침을 제공합니다.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 07/02/2021
ms.author: nitinme
ms.openlocfilehash: 4dce35c1713a4dcb4880080d1f28ed124a2209be
ms.sourcegitcommit: 57b7356981803f933cbf75e2d5285db73383947f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2021
ms.locfileid: "129546080"
---
# <a name="recover-deleted-cognitive-services-resources"></a>삭제된 Cognitive Services 리소스 복구

이 문서에서는 이미 삭제된 Cognitive Services 리소스를 복구하는 방법에 대한 지침을 제공합니다. 이 문서에서는 삭제된 리소스를 제거하는 방법에 대한 지침도 제공합니다.

> [!NOTE]
> 이 문서의 지침은 다중 서비스 리소스와 단일 서비스 리소스 모두에 적용할 수 있습니다. 다중 서비스 리소스를 사용하면 단일 키 및 엔드포인트를 사용하여 여러 Cognitive Services에 액세스할 수 있습니다. 반면에 단일 서비스 리소스를 사용하면 리소스가 만들어진 특정 Cognitive Services에만 액세스할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

* 복구할 리소스는 지난 48시간 이내에 삭제되어야 합니다.
* 복구할 리소스는 아직 제거되지 않아야 합니다. 제거된 리소스는 복구할 수 없습니다.
* 삭제된 리소스를 복구하기 전에 해당 계정의 리소스 그룹이 있는지 확인하세요. 리소스 그룹을 삭제한 경우 다시 생성해야 합니다. 리소스 그룹을 복구할 수 없습니다. 자세한 내용은  [리소스 그룹 관리](../azure-resource-manager/management/manage-resource-groups-portal.md)를 참조하세요.
* 삭제된 리소스에서 Azure Key Vault와 함께 고객 관리형 키를 사용하고 키 자격 증명 모음도 삭제된 경우, Cognitive Services 리소스를 복원하기 전에 키 자격 증명 모음을 복원해야 합니다. 자세한 내용은 [Azure Key Vault 복구 관리](../key-vault/general/key-vault-recovery.md)를 참조하세요.
* 삭제된 리소스에서 고객 관리형 스토리지를 사용하고 스토리지 계정도 삭제된 경우 Cognitive Services 리소스를 복원하기 전에 스토리지 계정을 복원해야 합니다. 지침은 [삭제된 스토리지 계정 복구](../storage/common/storage-account-recover.md)를 참조하세요.

구독에는 [Cognitive Services 기여자](../role-based-access-control/built-in-roles.md#cognitive-services-contributor) 또는 [기여자](../role-based-access-control/built-in-roles.md#contributor)와 같은 리소스를 삭제할 수 있는 `Microsoft.CognitiveServices/locations/resourceGroups/deletedAccounts/delete` 권한이 있어야 합니다. 

## <a name="recover-a-deleted-resource"></a>삭제된 리소스 복구 

삭제된 Cognitive Service 리소스를 복구하려면 다음 명령을 사용합니다. 해당하는 경우 다음을 대체합니다.

* Azure 구독 ID가 있는 `{subscriptionID}`
* 리소스 그룹이 있는 `{resourceGroup}`
* 리소스 이름이 있는 `{resourceName}`
* 리소스 그룹의 위치가 있는 `{location}`

### <a name="using-the-rest-api"></a>REST API 사용

다음 `PUT` 명령을 사용합니다.

```rest-api
https://management.azure.com/subscriptions/{subscriptionID}/resourceGroups/{resourceGroup}/providers/Microsoft.CognitiveServices/accounts/{resourceName}?Api-Version=2021-04-30
```

요청 본문에서 다음 JSON 형식을 사용합니다.

```json
{ 
  "location": "{location}", 
   "properties": { 
        "restore": true 
    } 
} 
```

### <a name="using-powershell"></a>PowerShell 사용

다음 명령을 사용하려면 리소스를 복원합니다. 

```powershell
New-AzResource -Location {location} -Properties @{restore=$true} -ResourceId /subscriptions/{subscriptionID}/resourceGroups/{resourceGroup}/providers/Microsoft.CognitiveServices/accounts/{resourceName}   -ApiVersion 2021-04-30 
```

삭제된 리소스의 이름을 찾아야 하는 경우 다음 명령을 사용하여 삭제된 리소스 이름 목록을 가져올 수 있습니다. 

```powershell
Get-AzResource -ResourceId /subscriptions/{subscriptionId}/providers/Microsoft.CognitiveServices/deletedAccounts -ApiVersion 2021-04-30 
```

### <a name="using-the-azure-cli"></a>Azure CLI 사용

```azurecli-interactive
az resource create --subscription {subscriptionID} -g {resourceGroup} -n {resourceName} --location {location} --namespace Microsoft.CognitiveServices --resource-type accounts --properties "{\"restore\": true}"
```

## <a name="purge-a-deleted-resource"></a>삭제된 리소스 제거 

리소스를 삭제하면 48시간 동안 같은 이름으로 다른 리소스를 만들 수 없습니다. 동일한 이름의 리소스를 만들려면 삭제된 리소스를 제거해야 합니다.

삭제된 Cognitive Service 리소스를 제거하려면 다음 명령을 사용합니다. 해당하는 경우 다음을 대체합니다.

* Azure 구독 ID가 있는 `{subscriptionID}`
* 리소스 그룹이 있는 `{resourceGroup}`
* 리소스 이름이 있는 `{resourceName}`
* 리소스 그룹의 위치가 있는 `{location}`

> [!NOTE]
> 리소스가 제거되면 영구적으로 삭제되며, 복원할 수 없습니다. 리소스와 연결된 모든 데이터와 키가 손실됩니다.

### <a name="using-the-rest-api"></a>REST API 사용

다음 `DELETE` 명령을 사용합니다.

```rest-api
https://management.azure.com/subscriptions/{subscriptionID}/providers/Microsoft.CognitiveServices/locations/{location}/resourceGroups/{resourceGroup}/deletedAccounts/{resourceName}?Api-Version=2021-04-30`
```

### <a name="using-powershell"></a>PowerShell 사용

```powershell
Remove-AzResource -ResourceId /subscriptions/{subscriptionID}/providers/Microsoft.CognitiveServices/locations/{location}/resourceGroups/{resourceGroup}/deletedAccounts/{resourceName}  -ApiVersion 2021-04-30`
```

### <a name="using-the-azure-cli"></a>Azure CLI 사용

```azurecli-interactive
az resource delete --ids /subscriptions/{subscriptionId}/providers/Microsoft.CognitiveServices/locations/{location}/resourceGroups/{resourceGroup}/deletedAccounts/{resourceName}
```

## <a name="see-also"></a>참조
* [Azure Portal를 사용하여 새 리소스 만들기](cognitive-services-apis-create-account.md)
* [Azure CLI를 사용하여 새 리소스 만들기](cognitive-services-apis-create-account-cli.md)
* [클라이언트 라이브러리를 사용하여 새 리소스 만들기](cognitive-services-apis-create-account-client-library.md)
* [ARM 템플릿을 사용하여 새 리소스 만들기](create-account-resource-manager-template.md)
