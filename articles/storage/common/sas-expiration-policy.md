---
title: 공유 액세스 서명에 대한 만료 정책 만들기
titleSuffix: Azure Storage
description: SAS(공유 액세스 서명)가 유효한 기간을 정의하는 정책을 스토리지 계정에 만듭니다. 정책 위반을 모니터링하여 보안 위험을 해결하는 방법을 알아봅니다.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 09/14/2021
ms.author: tamram
ms.reviewer: dineshm
ms.subservice: common
ms.openlocfilehash: 8d9a381e80c829acc2f87aa6a856a651b19315ae
ms.sourcegitcommit: 3ef5a4eed1c98ce76739cfcd114d492ff284305b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128709461"
---
# <a name="create-an-expiration-policy-for-shared-access-signatures"></a>공유 액세스 서명에 대한 만료 정책 만들기

SAS(공유 액세스 서명)를 사용하여 Azure Storage 계정의 리소스에 대한 액세스를 위임할 수 있습니다. SAS 토큰에는 대상 리소스, 부여된 권한 및 액세스가 허용되는 간격이 포함됩니다. SAS가 손상된 경우 SAS의 간격을 제한하는 것이 좋습니다. 스토리지 계정에 대한 SAS 만료 정책을 설정하여 사용자가 SAS를 만들 때 권장되는 상한 만료 제한을 제공할 수 있습니다.

SAS 만료 정책은 사용자가 정책에서 권장하는 제한을 초과하는 만료로 SAS를 만드는 것을 방지하지 않습니다. 사용자가 정책을 위반하는 SAS를 만들면 권장되는 최대 간격과 함께 경고가 표시됩니다. Azure Monitor 사용하여 로깅하기 위한 진단 설정을 구성한 경우 Azure Storage 사용자가 권장 간격 후에 만료되는 SAS를 만들 때마다 로그의 속성에 기록합니다.

공유 액세스 서명에 대한 자세한 내용은 [SAS(공유 액세스 서명)를 사용하여 Azure Storage 리소스에 대한 제한된 액세스 권한 부여](storage-sas-overview.md)를 참조하세요.

## <a name="create-a-sas-expiration-policy"></a>SAS 만료 정책 만들기

스토리지 계정에서 SAS 만료 정책을 만들 때 정책은 서비스 SAS, 사용자 위임 SAS 또는 계정 SAS를 포함하여 해당 스토리지 계정에 만들 수 있는 SAS의 각 유형에 적용됩니다.

스토리지 계정에 대한 SAS 만료 정책을 구성하려면 Azure Portal, PowerShell 또는 Azure CLI 사용합니다.

### <a name="azure-portal"></a>[Azure Portal](#tab/azure-portal)

Azure Portal SAS 만료 정책을 만들려면 다음 단계를 수행합니다.

1. Azure Portal의 스토리지 계정으로 이동합니다.
1. **설정** 에서 **구성** 을 선택합니다.
1. **SAS(공유 액세스 서명) 만료 간격에 권장되는 상한** 허용 설정을 찾아 **사용으로** 설정합니다.
1. 이 스토리지 계정의 리소스에 생성된 새 공유 액세스 서명에 권장되는 간격을 지정합니다.

    :::image type="content" source="media/sas-expiration-policy/configure-sas-expiration-policy-portal.png" alt-text="Azure Portal SAS 만료 정책을 구성하는 방법을 보여주는 스크린샷":::

1. **저장** 단추를 선택하여 변경 내용을 저장합니다.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

SAS 만료 정책을 만들려면 [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) 명령을 사용한 다음 매개 `-SasExpirationPeriod` 변수를 SAS가 서명된 시간부터 SAS 토큰이 활성화될 수 있는 일, 시간, 분 및 초로 설정합니다. 매개 변수를 제공하는 문자열은 `-SasExpirationPeriod` 형식을 `<days>.<hours>:<minutes>:<seconds>` 사용합니다. 예를 들어 SAS가 서명된 후 1일, 12시간, 5분 및 6초 후에 만료하도록 하려면 문자열을 `1.12:05:06` 사용합니다.

```powershell
$account = Set-AzStorageAccount -ResourceGroupName <resource-group> `
    -Name <storage-account-name> `
    -SasExpirationPeriod <days>.<hours>:<minutes>:<seconds>
```

> [!TIP]
> `-SasExpirationPeriod` [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) 명령의 매개 변수를 설정하여 스토리지 계정을 만들 때 SAS 만료 정책을 설정할 수도 있습니다.

정책이 적용되었는지 확인하려면, 이전 명령에서 `$account` 변수로 반환된 [PSStorageAccount](/dotnet/api/microsoft.azure.commands.management.storage.models.psstorageaccount)의 `SasPolicy` 속성을 사용합니다. 
  
```powershell
$account.SasPolicy
```

SAS 만료 기간이 콘솔 출력에 표시됩니다.

> [!div class="mx-imgBorder"]
> ![SAS 만료 기간](./media/storage-sas-expiration-policy/sas-policy-console-output.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

SAS 만료 정책을 만들려면 [az storage account update](/cli/azure/storage/account#az_storage_account_update) 명령을 사용한 다음 매개 `--key-exp-days` 변수를 SAS가 서명된 시간부터 SAS 토큰이 활성화될 수 있는 일, 시간, 분 및 초로 설정합니다. 매개 변수를 제공하는 문자열은 `--key-exp-days` 형식을 `<days>.<hours>:<minutes>:<seconds>` 사용합니다. 예를 들어 SAS가 서명된 후 1일, 12시간, 5분 및 6초 후에 만료하도록 하려면 문자열을 `1.12:05:06` 사용합니다.

```azurecli-interactive
az storage account update \
  --name <storage-account> \
  --resource-group <resource-group> \
  --sas-exp <days>.<hours>:<minutes>:<seconds>
```

> [!TIP]
> `--key-exp-days` [az storage account create](/cli/azure/storage/account#az_storage_account_create) 명령의 매개 변수를 설정하여 스토리지 계정을 만들 때 SAS 만료 정책을 설정할 수도 있습니다.

정책이 적용되었는지 확인하려면, [az storage account show](/cli/azure/storage/account#az_storage_account_show) 명령을 호출하고 `-query` 매개 변수에 `{SasPolicy:sasPolicy}` 문자열을 사용합니다.
  
```azurecli-interactive
az storage account show \
  --name <storage-account> \
  --resource-group <resource-group> \
  --query "{SasPolicy:sasPolicy}"
```

SAS 만료 기간이 콘솔 출력에 표시됩니다.

```json
{
  "SasPolicy": {
    "expirationAction": "Log",
    "sasExpirationPeriod": "1.12:05:06"
  }
}
```

---

## <a name="query-logs-for-policy-violations"></a>정책 위반에 대한 쿼리 로그

SAS 만료 정책이 권장하는 것보다 긴 간격 동안 유효한 SAS 생성을 기록하려면 먼저 Azure Log Analytics 작업 영역에 로그를 보내는 진단 설정을 만듭니다. 자세한 내용은 [Azure Log Analytics에 로그 보내기를 참조하세요.](../blobs/monitor-blob-storage.md#send-logs-to-azure-log-analytics)

다음으로 Azure Monitor 로그 쿼리를 사용하여 SAS가 만료되었는지 확인합니다. Log Analytics 작업 영역에서 새 쿼리를 만들고, 다음 쿼리 텍스트를 추가하고, **실행을** 누릅니다.

```kusto
StorageBlobLogs | where SasExpiryStatus startswith "Policy Violated" 
```

## <a name="see-also"></a>참고 항목

- [SAS(공유 액세스 서명)를 사용하여 Azure Storage 리소스에 대한 제한된 액세스 권한 부여](storage-sas-overview.md)
- [서비스 SAS 만들기](/rest/api/storageservices/create-service-sas)
- [사용자 위임 SAS 만들기](/rest/api/storageservices/create-user-delegation-sas)
- [계정 SAS 만들기](/rest/api/storageservices/create-account-sas)