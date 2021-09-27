---
title: Azure Key Vault 로깅을 사용하도록 설정
description: 제공한 Azure Storage 계정에 정보를 저장하는 Azure Key Vault에서 로깅을 사용하도록 설정하는 방법입니다.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 10/01/2020
ms.author: mbaldwin
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: c537e9a9dd733d84161e59999ff17b8b0251f623
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/26/2021
ms.locfileid: "129061184"
---
# <a name="enable-key-vault-logging"></a>Key Vault 로깅을 사용하도록 설정

하나 이상의 키 자격 증명 모음을 만든 후에는 키 자격 증명 모음에 액세스하는 방법, 시기 및 사용자를 모니터링하려고 할 수도 있습니다. 기능에 대한 자세한 내용은 [Azure Key Vault 로깅](logging.md)을 참조하세요.

기록되는 내용:

* 인증된 모든 REST API 요청(예: 액세스 권한, 시스템 오류 또는 잘못된 요청으로 인해 실패한 요청)
* 키 자격 증명 모음 자체에 대한 작업(예: 키 자격 증명 모음 액세스 정책 만들기, 삭제, 설정 및 태그와 같은 키 자격 증명 모음 특성 업데이트)
* 다음을 포함하여 키 자격 증명 모음의 키 및 비밀에 대한 작업
  * 이러한 키 또는 비밀 만들기, 수정 또는 삭제
  * 서명, 확인, 암호화, 암호 해독, 키 래핑 및 래핑 해제, 비밀 가져오기, 키 및 비밀(및 해당 버전) 나열
* 401 응답이 발생하는 인증되지 않은 요청. 예를 들어 전달자 토큰이 없거나, 형식이 잘못되었거나 만료되었거나, 잘못된 토큰이 있는 요청입니다.  
* 다음 조건에 대한 Azure Event Grid 알림 이벤트: 만료됨, 만료 임박, 자격 증명 모음 액세스 정책 변경됨(새 버전 이벤트가 기록되지 않음). 키 자격 증명 모음에 생성된 이벤트 구독이 있더라도 이벤트가 기록됩니다. 자세한 내용은 [Event Grid 원본으로 사용되는 Azure Key Vault](../../event-grid/event-schema-key-vault.md)를 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

이 자습서를 완료하려면 다음 항목이 필요합니다.

* 사용하고 있는 기존 키 자격 증명 모음  
* [Azure Cloud Shell](https://shell.azure.com) - Bash 환경
* 키 자격 증명 모음 로그에 대한 Azure의 충분한 스토리지.

이 문서에서는 Bash를 환경으로 사용하여 [Cloud Shell](https://shell.azure.com)용으로 명령 형식이 지정됩니다.

## <a name="connect-to-your-key-vault-subscription"></a>Key Vault 구독에 연결

키 로깅을 설정하는 첫 번째 단계는 키 자격 증명 모음이 포함된 구독에 연결하는 것입니다. 사용자 계정에 여러 구독이 연결된 경우 특히 중요합니다.

Azure CLI를 사용하면 [az account list](/cli/azure/account#az_account_list) 명령을 사용하여 모든 구독을 볼 수 있습니다. 그런 다음, [az account set](/cli/azure/account#az_account_set) 명령을 사용하여 연결할 수 있습니다.

```azurecli-interactive
az account list

az account set --subscription "<subscriptionID>"
```

Azure PowerShell을 사용하면 먼저 [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription) cmdlet을 사용하여 구독을 나열할 수 있습니다. 그런 다음, [Set-AzContext](/powershell/module/az.accounts/set-azcontext) cmdlet을 사용하여 연결합니다. 

```powershell-interactive
Get-AzSubscription

Set-AzContext -SubscriptionId "<subscriptionID>"
```

## <a name="create-a-storage-account-for-your-logs"></a>로그에 대한 스토리지 계정 만들기

로그에 기존 스토리지 계정을 사용할 수 있지만, 여기에서 Key Vault 로그 전용 새 스토리지 계정을 만듭니다. 

추가로 쉽게 관리하기 위해 키 자격 증명 모음이 포함된 것과 동일한 리소스 그룹을 사용합니다. [Azure CLI 빠른 시작](quick-create-cli.md) 및 [Azure PowerShell 빠른 시작](quick-create-powershell.md)에서 해당 리소스 그룹의 이름은 **myResourceGroup** 이며 위치는 *eastus* 입니다. 해당하는 경우 해당 값을 사용자 고유의 값으로 바꿉니다. 

스토리지 계정 이름도 제공해야 합니다. 스토리지 계정 이름은 3자에서 24자 사이의 고유한 이름이어야 하고 숫자와 소문자만 사용해야 합니다. 마지막으로 `Standard_LRS` SKU의 스토리지 계정을 만듭니다.

Azure CLI를 사용하는 경우 [az storage account create](/cli/azure/storage/account#az_storage_account_create) 명령을 사용합니다. 

```azurecli-interactive
az storage account create --name "<your-unique-storage-account-name>" -g "myResourceGroup" --sku "Standard_LRS"
```

Azure PowerShell을 사용하는 경우 [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) cmdlet을 사용합니다. 리소스 그룹에 해당하는 위치를 제공해야 합니다.

```powershell
 New-AzStorageAccount -ResourceGroupName myResourceGroup -Name "<your-unique-storage-account-name>" -Type "Standard_LRS" -Location "eastus"
```

두 경우 모두 스토리지 계정의 ID를 확인합니다. Azure CLI 작업은 출력값에 ID를 반환합니다. Azure PowerShell에서 ID를 얻으려면 [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount)를 사용하고 `$sa` 변수에 출력값을 할당합니다. 그러면 `$sa.id`가 있는 스토리지 계정을 볼 수 있습니다. (`$sa.Context` 속성은 이 문서의 뒷부분에서도 사용됩니다.)

```powershell-interactive
$sa = Get-AzStorageAccount -Name "<your-unique-storage-account-name>" -ResourceGroup "myResourceGroup"
$sa.id
```

스토리지 계정의 ID 형식은 "/subscriptions/*your-subscription-ID*/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/*your-unique-storage-account-name*"입니다.

> [!NOTE]
> 기존 스토리지 계정을 사용하려면 키 자격 증명 모음과 동일한 구독을 사용해야 합니다. 클래식 배포 모델이 아닌 Azure Resource Manager 배포 모델을 사용해야 합니다.

## <a name="obtain-your-key-vault-resource-id"></a>키 자격 증명 모음 리소스 ID 가져오기

[CLI 빠른 시작](quick-create-cli.md) 및 [PowerShell 빠른 시작](quick-create-powershell.md)에서 고유 이름으로 키를 만들었습니다. 이 이름은 다음 단계에서 다시 사용합니다. 키 자격 증명 모음의 이름이 기억나지 않는 경우, Azure CLI [az keyvault list](/cli/azure/keyvault#az_keyvault_list) 명령이나 Azure PowerShell [Get-AzKeyVault](/powershell/module/az.keyvault/get-azkeyvault) cmdlet을 사용하여 이름을 나열할 수 있습니다.

키 자격 증명 모음 이름을 사용하여 리소스 ID를 찾습니다. Azure CLI를 사용하는 경우 [az keyvault show](/cli/azure/keyvault#az_keyvault_show) 명령을 사용합니다.

```azurecli-interactive
az keyvault show --name "<your-unique-keyvault-name>"
```

Azure PowerShell을 사용하는 경우 [Get-AzKeyVault](/powershell/module/az.keyvault/get-azkeyvault) cmdlet을 사용합니다.

```powershell-interactive
Get-AzKeyVault -VaultName "<your-unique-keyvault-name>"
```

키 자격 증명 모음의 리소스 ID 형식은 "/subscriptions/*your-subscription-ID*/resourceGroups/myResourceGroup/providers/Microsoft.KeyVault/vaults/*your-unique-keyvault-name* 입니다. 다음 단계를 위해 기록해둡니다.

## <a name="enable-logging"></a>로깅 사용

Azure CLI, Azure PowerShell, Azure Portal을 사용하여 Key Vault에 대한 로깅을 사용하도록 설정할 수 있습니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

### <a name="azure-cli"></a>Azure CLI

Azure CLI [az monitor diagnostic-settings create](/cli/azure/monitor/diagnostic-settings) 명령, 스토리지 계정 ID 및 키 자격 증명 모음 리소스 ID를 다음과 같이 사용합니다.

```azurecli-interactive
az monitor diagnostic-settings create --storage-account "<storage-account-id>" --resource "<key-vault-resource-id>" --name "Key vault logs" --logs '[{"category": "AuditEvent","enabled": true}]' --metrics '[{"category": "AllMetrics","enabled": true}]'
```

필요에 따라 지정된 시간이 지나면 오래된 로그가 자동으로 삭제되도록 로그 보존 정책을 설정할 수 있습니다. 예를 들어 90일이 지난 로그를 자동으로 삭제하는 보존 정책을 설정할 수 있습니다.

Azure CLI를 사용하는 경우 [az monitor diagnostic-settings update](/cli/azure/monitor/diagnostic-settings#az_monitor_diagnostic_settings_update) 명령을 사용합니다. 

```azurecli-interactive
az monitor diagnostic-settings update --name "Key vault retention policy" --resource "<key-vault-resource-id>" --set retentionPolicy.days=90
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

[Set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting) cmdlet을 `-Enabled` 플래그는 `$true`로 설정하고 `category`는 `AuditEvent`(Key Vault 로깅의 유일한 범주)로 설정하여 사용합니다.

```powershell-interactive
Set-AzDiagnosticSetting -ResourceId "<key-vault-resource-id>" -StorageAccountId $sa.id -Enabled $true -Category "AuditEvent"
```

필요에 따라 지정된 시간이 지나면 오래된 로그가 자동으로 삭제되도록 로그 보존 정책을 설정할 수 있습니다. 예를 들어 90일이 지난 로그를 자동으로 삭제하는 보존 정책을 설정할 수 있습니다.

Azure PowerShell을 사용하는 경우 [Set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting) cmdlet을 사용합니다.

```powershell-interactive
Set-AzDiagnosticSetting "<key-vault-resource-id>" -StorageAccountId $sa.id -Enabled $true -Category AuditEvent -RetentionEnabled $true -RetentionInDays 90
```

# <a name="azure-portal"></a>[Azure Portal](#tab/azure-portal)

Azure Portal에서 진단 설정을 구성하려면 다음 단계를 수행합니다.

1. **리소스** 창 메뉴에서 **진단 설정** 을 선택합니다.

   :::image type="content" source="../media/diagnostics-portal-1.png" alt-text="진단 설정을 선택하는 방법을 보여주는 스크린샷":::

1. **+ 진단 설정 추가** 를 선택합니다.

    :::image type="content" source="../media/diagnostics-portal-2.png" alt-text="진단 설정 추가를 보여주는 스크린샷":::
 
1. 진단 설정의 이름을 선택합니다. Key Vault용 Azure Monitor에 대한 로깅을 구성하려면 **AuditEvent** 및 **Log Analytics 작업 영역으로 보내기** 를 선택합니다. 그런 다음, 로그를 보낼 구독 및 Log Analytics 작업 영역을 선택합니다.

    :::image type="content" source="../media/diagnostics-portal-3.png" alt-text="진단 설정 옵션의 스크린샷":::

    그렇지 않으면 선택하려는 로그와 관련된 옵션을 선택합니다.

1. 원하는 옵션을 선택했으면 **저장** 을 선택합니다.

    :::image type="content" source="../media/diagnostics-portal-4.png" alt-text="선택한 옵션을 저장하는 방법을 보여주는 스크린샷":::

---

## <a name="access-your-logs"></a>로그에 액세스

Key Vault 로그는 제공한 스토리지 계정의 *insights-logs-auditevent* 컨테이너에 있습니다. 로그를 보려면 Blob을 다운로드해야 합니다.

먼저 컨테이너의 모든 Blob을 나열합니다.  Azure CLI를 사용하는 경우 [az storage blob list](/cli/azure/storage/blob#az_storage_blob_list) 명령을 사용합니다.

```azurecli-interactive
az storage blob list --account-name "<your-unique-storage-account-name>" --container-name "insights-logs-auditevent"
```

Azure PowerShell에서는 [Get-AzStorageBlob](/powershell/module/az.storage/get-azstorageblob)을 사용합니다. 이 컨테이너의 모든 Blob을 나열하려면 다음을 입력합니다.

```powershell
Get-AzStorageBlob -Container "insights-logs-auditevent" -Context $sa.Context
```

Azure CLI 명령 또는 Azure PowerShell cmdlet의 출력에서 Blob의 이름이 다음 형식임을 확인할 수 있습니다. `resourceId=<ARM resource ID>/y=<year>/m=<month>/d=<day of month>/h=<hour>/m=<minute>/filename.json`. 날짜 및 시간 값은 협정 세계시를 사용합니다.

동일한 스토리지 계정을 사용하여 여러 리소스에 대한 로그를 수집할 수 있으므로 Blob 이름의 전체 리소스 ID는 필요한 Blob에 액세스하거나 다운로드하는 데 유용합니다.

먼저 모든 Blob을 다운로드합니다. Azure CLI를 사용하는 경우 [az storage blob download](/cli/azure/storage/blob#az_storage_blob_download) 명령을 사용하여 Blob의 이름과 결과를 저장하려는 파일의 경로를 전달합니다.

```azurecli-interactive
az storage blob download --container-name "insights-logs-auditevent" --file <path-to-file> --name "<blob-name>" --account-name "<your-unique-storage-account-name>"
```

Azure PowerShell에서는 [Gt-AzStorageBlobs](/powershell/module/az.storage/get-azstorageblob) cmdlet을 사용하여 Blob 목록을 가져옵니다. 그런 다음, 이 목록을 [Get-AzStorageBlobContent](/powershell/module/az.storage/get-azstorageblobcontent) cmdlet에 파이프하여 선택한 경로에 로그를 다운로드합니다.

```powershell-interactive
$blobs = Get-AzStorageBlob -Container "insights-logs-auditevent" -Context $sa.Context | Get-AzStorageBlobContent -Destination "<path-to-file>"
```

PowerShell에서 이 두 번째 cmdlet을 실행할 때 Blob 이름의 `/` 구분 기호는 대상 폴더 아래에 전체 폴더 구조를 만듭니다. 이 구조를 사용하여 Blob을 다운로드하고 파일로 저장합니다.

선택적으로 Blob을 다운로드하려면 와일드카드를 사용합니다. 예를 들면 다음과 같습니다.

* 여러 키 자격 증명 모음이 있고 CONTOSOKEYVAULT3이라는 하나의 키 자격 증명 모음에 대한 로그를 다운로드하려는 경우:

  ```powershell
  Get-AzStorageBlob -Container "insights-logs-auditevent" -Context $sa.Context -Blob '*/VAULTS/CONTOSOKEYVAULT3
  ```

* 리소스 그룹이 여러 개이고 하나의 리소스 그룹에 대한 로그를 다운로드하려는 경우 `-Blob '*/RESOURCEGROUPS/<resource group name>/*'`을(를) 사용합니다.

  ```powershell
  Get-AzStorageBlob -Container "insights-logs-auditevent" -Context $sa.Context -Blob '*/RESOURCEGROUPS/CONTOSORESOURCEGROUP3/*'
  ```

* 2019년 1월의 모든 로그를 다운로드하려면 `-Blob '*/year=2019/m=01/*'`을 사용합니다.

  ```powershell
  Get-AzStorageBlob -Container "insights-logs-auditevent" -Context $sa.Context -Blob '*/year=2016/m=01/*'
  ```

## <a name="use-azure-monitor-logs"></a>Azure Monitor 로그 사용

Azure Monitor 로그에서 Key Vault 솔루션을 사용하여 Key Vault `AuditEvent` 로그를 검토할 수 있습니다. Azure Monitor 로그에서 로그 쿼리를 사용하여 데이터를 분석하고 필요한 정보를 가져옵니다.  자세한 내용은 [Key Vault 모니터링을](monitor-key-vault.md)참조하세요.

## <a name="next-steps"></a>다음 단계

- Key Vault 로그 해석 방법을 비롯한 개념 정보는 [Key Vault 로깅](logging.md)을 참조하세요.
- 키 자격 증명 모음에서 Azure Monitor 사용하는 방법에 대한 자세한 내용은 [Key Vault 모니터링을](monitor-key-vault.md)참조하세요.
