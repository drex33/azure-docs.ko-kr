---
title: Azure Monitor 고객 관리형 키
description: 고객 관리형 키를 구성하여 Azure Key Vault 키를 사용하여 Log Analytics 작업 영역의 데이터를 암호화하는 방법에 대한 정보 및 단계입니다.
ms.topic: conceptual
author: yossi-y
ms.author: yossiy
ms.date: 07/29/2021
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 2378daa38d1fba86bbb9194b7993cee9a862ad4c
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131461972"
---
# <a name="azure-monitor-customer-managed-key"></a>Azure Monitor 고객 관리형 키 

Azure Monitor의 데이터는 Microsoft 관리형 키를 사용하여 암호화됩니다. 사용자 고유의 암호화 키를 사용하여 작업 영역에서 데이터 및 저장된 쿼리를 보호할 수 있습니다. 고객 관리형 키를 지정하면 해당 키가 데이터에 대한 액세스를 보호하고 제어하는 데 사용되고, 구성된 후에는 작업 영역으로 전송되는 모든 데이터가 Azure Key Vault 키로 암호화됩니다. 고객 관리형 키를 사용하면 훨씬 더 유연하게 액세스 제어를 관리할 수 있습니다.

구성하기 전에 아래 [제한 사항 및 제약 조건](#limitationsandconstraints)을 검토하는 것이 좋습니다.

## <a name="customer-managed-key-overview"></a>고객 관리형 키 개요

[미사용 암호화](../../security/fundamentals/encryption-atrest.md)는 조직의 일반적인 개인 정보 보호 및 보안 요구 사항입니다. 암호화 및 암호화 키를 긴밀하게 관리하는 다양한 옵션을 사용하여 Azure에서 미사용 암호화를 완전히 관리할 수 있습니다.

Azure Monitor를 사용하면 모든 데이터 및 저장된 쿼리가 MMK(Microsoft 관리형 키)를 사용하여 미사용 상태로 암호화됩니다. 또한 Azure Monitor는 [Azure Key Vault](../../key-vault/general/overview.md)에 저장된 자체 키를 사용하여 암호화 옵션을 제공하며, 이를 통해 언제든지 데이터에 대한 액세스를 취소할 수 있는 제어 권한을 제공합니다. Azure Monitor의 암호화 사용은 [Azure Storage 암호화](../../storage/common/storage-service-encryption.md#about-azure-storage-encryption)가 작동하는 방식과 동일합니다.

고객 관리형 키는 더 높은 보호 수준과 제어를 제공하는 [전용 클러스터](./logs-dedicated-clusters.md)에서 전달됩니다. 전용 클러스터에 수집된 데이터는 두 번 암호화됩니다. 즉, Microsoft 관리형 키 또는 고객 관리형 키를 사용하는 서비스 수준에서 한 번, 두 가지 암호화 알고리즘과 두 가지 키를 사용하는 인프라 수준에서 한 번 암호화됩니다. [이중 암호화](../../storage/common/storage-service-encryption.md#doubly-encrypt-data-with-infrastructure-encryption)는 암호화 알고리즘 또는 키 중 하나가 손상될 수 있는 시나리오로부터 보호합니다. 이 경우 추가 암호화 계층은 계속해서 데이터를 보호합니다. 전용 클러스터를 사용하면 [Lockbox](#customer-lockbox-preview) 제어로 데이터를 보호할 수도 있습니다.

지난 14일 동안 수집된 데이터와 최근 쿼리에 사용된 데이터는 쿼리 효율성을 위해 핫 캐시(SSD 지원)에 보관되고 고객 관리형 키 구성에 관계없이 Microsoft 키로 암호화됩니다. 컨트롤 SSD 데이터 액세스가 적용되고 [키 해지](#key-revocation) 준수

Log Analytics 전용 클러스터 [가격 책정 모델](./logs-dedicated-clusters.md#cluster-pricing-model)에는 500GB/일부터 시작하는 약정 계층이 필요하며 500, 1000, 2000 또는 5000GB/일 값을 가질 수 있습니다.

## <a name="how-customer-managed-key-works-in-azure-monitor"></a>Azure Monitor에서 고객 관리형 키가 작동하는 방식

Azure Monitor는 관리 ID를 사용하여 Azure Key Vault에 대한 액세스 권한을 부여합니다. 클러스터 수준에서 Log Analytics 클러스터의 ID가 지원됩니다. 여러 작업 영역에서 고객 관리형 키 보호를 허용하기 위해 새 Log Analytics *클러스터* 리소스에서 Key Vault와 Log Analytics 작업 영역 간의 중간 ID 연결 역할을 수행합니다. 클러스터의 스토리지는 *클러스터* 리소스와 연결된 관리 ID를 사용하여 Azure Active Directory를 통해 Azure Key Vault에 인증합니다. 

고객 관리형 키 구성 후 전용 클러스터에 연결된 작업 영역에 대한 새 수집 데이터는 키로 암호화됩니다. 언제든지 클러스터에서 작업 영역의 링크를 해제할 수 있습니다. 그런 다음 새 데이터는 Log Analytics 스토리지에 수집되고 Microsoft 키를 사용하여 암호화되지만, 새 데이터와 기존 데이터를 원활하게 쿼리할 수 있습니다.

> [!IMPORTANT]
> 고객 관리형 키 기능은 지역에 따라 다를 수 있습니다. Azure Key Vault, 클러스터 및 연결된 Log Analytics 작업 영역은 동일한 지역에 있어야 하지만 서로 다른 구독에 있을 수 있습니다.

![고객 관리형 키 개요](media/customer-managed-keys/cmk-overview.png)

1. Key Vault
2. Key Vault에 대한 권한이 있는 관리 ID를 사용하는 Log Analytics *클러스터* 리소스 - 이 ID는 기본 전용 Log Analytics 클러스터 스토리지로 전파됩니다.
3. 전용 Log Analytics 클러스터
4. *클러스터* 리소스에 연결된 작업 영역 

### <a name="encryption-keys-operation"></a>암호화 키 작업

스토리지 데이터 암호화에는 세 가지 유형의 키가 있습니다.

- **KEK** - 키 암호화 키(고객 관리형 키)
- **AEK** - 계정 암호화 키
- **DEK** - 데이터 암호화 키

다음 규칙이 적용됩니다.

- Log Analytics 클러스터 스토리지 계정은 모든 스토리지 계정에 대해 AEK라고 하는 고유한 암호화 키를 생성합니다.
- AEK는 디스크에 기록되는 각 데이터 블록을 암호화하는 데 사용되는 키인 DEK를 파생하는 데 사용됩니다.
- Key Vault에서 키를 구성하고 클러스터에서 참조하는 경우 Azure Storage에서 요청을 Azure Key Vault에 보내 AEK를 래핑하고 이를 해제하여 데이터 암호화 및 암호 해독 작업을 수행합니다.
- KEK 사용자의 키 자격 증명 모음을 유지하지 않습니다.
- Azure Storage는 *클러스터* 리소스와 연결된 관리 ID를 사용하여 Azure Active Directory를 통해 Azure Key Vault를 인증하고 액세스합니다.

### <a name="customer-managed-key-provisioning-steps"></a>고객 관리형 키 프로비저닝 단계

1. Azure Key Vault 만들기 및 키 저장
1. 클러스터 만들기
1. Key Vault에 권한 부여
1. 키 식별자 세부 정보를 사용하여 클러스터 업데이트
1. Log Analytics 작업 영역 연결

고객 관리형 키 구성은 현재 Azure Portal에서 지원되지 않으며 [PowerShell](/powershell/module/az.operationalinsights/), [CLI](/cli/azure/monitor/log-analytics) 또는 [REST](/rest/api/loganalytics/) 요청을 통해 프로비저닝을 수행할 수 있습니다.

## <a name="storing-encryption-key-kek"></a>암호화 키(KEK) 저장

클러스터가 계획되는 지역에서 기존 Azure Key Vault를 만들거나 사용한 다음, 로그 암호화에 사용할 키를 생성하거나 가져옵니다. Azure Monitor에서 키와 데이터에 대한 액세스를 보호하기 위해 Azure Key Vault를 복구 가능으로 구성해야 합니다. 이 구성은 Key Vault의 속성에서 확인할 수 있습니다. *일시 삭제* 및 *제거 보호* 를 모두 사용하도록 설정되어 있어야 합니다.

![일시 삭제 및 제거 보호 설정](media/customer-managed-keys/soft-purge-protection.png)

이러한 설정은 CLI 및 PowerShell을 통해 키 자격 증명 모음에서 업데이트할 수 있습니다.

- [일시 삭제](../../key-vault/general/soft-delete-overview.md)
- [제거 보호](../../key-vault/general/soft-delete-overview.md#purge-protection)는 일시 삭제 후에도 비밀/자격 증명 모음을 강제로 삭제하지 않도록 방지합니다.

## <a name="create-cluster"></a>클러스터 만들기

클러스터는 Key Vault 데이터 암호화에 관리 ID를 사용합니다. `type`래핑 및 래핑 해제 작업을 위해 Key Vault 대한 액세스를 허용하도록 클러스터를 만들 때 ID 속성을 로 `SystemAssigned` 구성합니다. 
  
  시스템이 할당한 관리 ID에 대한 클러스터의 ID 설정
  ```json
  {
    "identity": {
      "type": "SystemAssigned"
      }
  }
  ```

[전용 클러스터 문서](./logs-dedicated-clusters.md#create-a-dedicated-cluster)에 설명된 절차를 따르세요. 

## <a name="grant-key-vault-permissions"></a>Key Vault 권한 부여

클러스터에 대한 사용 권한을 부여하는 키 자격 증명 모음에 대한 액세스 정책을 만듭니다. 이러한 권한은 기본 Azure Monitor 스토리지에서 사용됩니다. Azure Portal에서 Key Vault를 열고, *"액세스 정책"* 을 클릭한 다음, *"+ 액세스 정책 추가"* 를 클릭하여 다음 설정을 통해 정책을 만듭니다.

- 키 권한: *'가져오기'* , *'키 래핑'* 및 *'키 래핑 해제'* 를 선택합니다.
- 보안 주체 선택: 클러스터에 사용되는 ID 형식(시스템 또는 사용자가 할당한 관리 ID)에 따라 시스템 할당 관리 ID 또는 사용자 할당 관리 ID 이름에 대한 클러스터 이름 또는 클러스터 보안 주체 ID를 입력합니다.

![Key Vault 권한 부여](media/customer-managed-keys/grant-key-vault-permissions-8bit.png)

Azure Monitor 데이터에 대한 액세스와 키를 보호하기 위해 Key Vault가 복구 가능으로 구성되었는지 확인하려면 *가져오기* 권한이 필요합니다.

## <a name="update-cluster-with-key-identifier-details"></a>키 식별자 세부 정보를 사용하여 클러스터 업데이트

클러스터의 모든 작업에는 `Microsoft.OperationalInsights/clusters/write` 작업 권한이 필요합니다. 이 권한은 `*/write` 작업이 포함된 소유자이거나 기여자 또는 `Microsoft.OperationalInsights/*` 작업이 포함된 Log Analytics 기여자 역할을 통해 부여될 수 있습니다.

이 단계에서는 데이터 암호화에 사용할 키와 버전으로 Azure Monitor 스토리지를 업데이트합니다. 업데이트되면 새 키를 사용하여 AEK(스토리지 키)를 래핑 및 래핑 해제합니다.

>[!IMPORTANT]
>- 키 회전은 자동으로 수행되거나 명시적 키 업데이트가 필요할 수 있습니다. 클러스터에서 키 식별자 세부 정보를 업데이트하기 전에 사용자에게 적합한 접근 방식을 확인하려면 [키 회전](#key-rotation)을 참조하세요.
>- 클러스터 업데이트 시 동일한 작업에 ID 및 키 식별자 세부 정보를 둘 다 포함해서는 안 됩니다. 둘 다 업데이트해야 하는 경우 두 번의 연속 작업으로 업데이트를 수행해야 합니다.

![Key Vault 권한 부여](media/customer-managed-keys/key-identifier-8bit.png)

키 식별자 세부 정보를 사용하여 클러스터에서 KeyVaultProperties를 업데이트합니다.

작업은 비동기식이며 완료하는 데 시간이 걸릴 수 있습니다.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

해당 없음

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az account set --subscription "cluster-subscription-id"

az monitor log-analytics cluster update --no-wait --name "cluster-name" --resource-group "resource-group-name" --key-name "key-name" --key-vault-uri "key-uri" --key-version "key-version"

# Wait for job completion when `--no-wait` was used
$clusterResourceId = az monitor log-analytics cluster list --resource-group "resource-group-name" --query "[?contains(name, "cluster-name")].[id]" --output tsv
az resource wait --created --ids $clusterResourceId --include-response-body true

```
# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
Select-AzSubscription "cluster-subscription-id"

Update-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name" -KeyVaultUri "key-uri" -KeyName "key-name" -KeyVersion "key-version" -AsJob

# Check when the job is done when `-AsJob` was used
Get-Job -Command "New-AzOperationalInsightsCluster*" | Format-List -Property *
```

# <a name="rest"></a>[REST (영문)](#tab/rest)

```rst
PATCH https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/cluster-name?api-version=2021-06-01
Authorization: Bearer <token> 
Content-type: application/json
 
{
  "properties": {
    "keyVaultProperties": {
      "keyVaultUri": "https://key-vault-name.vault.azure.net",
      "keyName": "key-name",
      "keyVersion": "current-version"
  },
  "sku": {
    "name": "CapacityReservation",
    "capacity": 500
  }
}
```

**응답**

키 전파를 완료하는 데 시간이 걸립니다. 클러스터에서 GET 요청을 전송하여 업데이트 상태를 확인하고 *KeyVaultProperties* 속성을 확인할 수 있습니다. 최근 업데이트된 키가 응답에서 반환되어야 합니다.

키 업데이트가 완료되면 GET 요청에 대한 응답이 다음과 같이 표시됨: 202(수락됨) 및 헤더
```json
{
  "identity": {
    "type": "SystemAssigned",
    "tenantId": "tenant-id",
    "principalId": "principal-id"
  },
  "sku": {
    "name": "capacityreservation",
    "capacity": 500
  },
  "properties": {
    "keyVaultProperties": {
      "keyVaultUri": "https://key-vault-name.vault.azure.net",
      "keyName": "key-name",
      "keyVersion": "current-version"
      },
    "provisioningState": "Succeeded",
    "clusterId": "cluster-id",
    "billingType": "Cluster",
    "lastModifiedDate": "last-modified-date",
    "createdDate": "created-date",
    "isDoubleEncryptionEnabled": false,
    "isAvailabilityZonesEnabled": false,
    "capacityReservationProperties": {
      "lastSkuUpdate": "last-sku-modified-date",
      "minCapacity": 500
    }
  },
  "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name",
  "name": "cluster-name",
  "type": "Microsoft.OperationalInsights/clusters",
  "location": "cluster-region"
}
```

---

## <a name="link-workspace-to-cluster"></a>클러스터에 작업 영역 연결

> [!IMPORTANT]
> 이 단계는 Log Analytics 클러스터 프로비저닝이 완료된 후에만 수행해야 합니다. 프로비저닝하기 전에 작업 영역을 연결하고 데이터를 수집하면 수집된 데이터가 삭제되어 복구할 수 없습니다.

이 작업을 수행하려면 작업 영역 및 클러스터에 대한 '쓰기' 권한이 있어야 합니다. 여기에는 `Microsoft.OperationalInsights/workspaces/write` 및 `Microsoft.OperationalInsights/clusters/write`가 포함됩니다.

[전용 클러스터 문서](./logs-dedicated-clusters.md#link-a-workspace-to-a-cluster)에 설명된 절차를 따르세요.

## <a name="key-revocation"></a>키 해지

> [!IMPORTANT]
> - 데이터에 대한 액세스를 취소하는 권장 방법은 키를 사용하지 않도록 설정하거나 키 자격 증명 모음에서 액세스 정책을 삭제하는 것입니다.
> - 클러스터의 `identity` `type`을 `None`으로 설정하면 데이터에 대한 액세스도 철회되지만, 이 접근 방식은 지원 담당자에게 문의하지 않고 되돌릴 수 없기 때문에 권장되지 않습니다.

클러스터 스토리지는 키 권한의 변경 내용을 항상 1시간 이내에 적용합니다. 이로 인해 스토리지가 사용할 수 없게 됩니다. 클러스터와 연결된 작업 영역에 수집된 새 데이터는 삭제되고 복구할 수 없으며 이러한 작업 영역에서 데이터에 액세스할 수 없게 되고 쿼리가 실패합니다. 클러스터와 작업 영역을 삭제하지 않는 한 이전에 수집된 데이터는 스토리지에서 유지됩니다. 액세스할 수 없는 데이터는 데이터 보존 정책에 따라 제어되며 보존 기간에 도달하면 삭제됩니다. 지난 14일 동안 수집된 데이터와 최근 쿼리에 사용된 데이터도 쿼리 효율성을 위해 핫 캐시(SSD 기반)에 유지됩니다. SSD의 데이터는 키 해지 작업 시 삭제되고 액세스할 수 없게 됩니다. 클러스터의 스토리지는 Key Vault 사용하여 주기적으로 암호화를 래핑 해제하려고 시도하고, 해지가 되돌려지면 래핑 해제가 성공하고, SSD 데이터가 스토리지에서 다시 로드되고, 데이터 수집 및 쿼리가 30분 이내에 다시 시작됩니다.

## <a name="key-rotation"></a>키 회전

키 회전에는 두 가지 모드가 있습니다. 
- 자동 회전 - ```"keyVaultProperties"```로 클러스터를 업데이트하지만 ```"keyVersion"``` 속성을 생략하거나 ```""```으로 설정하면 스토리지가 자동으로 최신 버전을 사용합니다.
- 명시적 키 버전 업데이트 - 클러스터를 업데이트하고 ```"keyVersion"``` 속성에 키 버전을 제공할 때 모든 새 키 버전에는 클러스터에 명시적인 ```"keyVaultProperties"``` 업데이트가 필요합니다. [키 식별자 세부 정보로 클러스터 업데이트](#update-cluster-with-key-identifier-details)를 참조하세요. Key Vault에서 새 키 버전을 생성하지만 클러스터에서 업데이트하지 않는 경우 Log Analytics 클러스터 스토리지는 이전 키를 계속 사용합니다. 클러스터에서 새 키를 업데이트하기 전에 이전 키를 사용하지 않도록 설정하거나 삭제하면 [키 해지](#key-revocation) 상태가 됩니다.

AEK는 이제 Key Vault의 새 KEK(키 암호화 키) 버전을 사용하여 암호화되지만, 데이터는 항상 AEK(계정 암호화 키)를 사용하여 암호화되므로 키 회전 작업 후에도 모든 데이터에 계속 액세스할 수 있습니다.

## <a name="customer-managed-key-for-saved-queries-and-log-alerts"></a>저장된 쿼리 및 로그 경고에 대한 고객 관리형 키

Log Analytics에 사용되는 쿼리 언어는 표현적이며 쿼리에 추가하는 설명 또는 쿼리 구문에 중요한 정보를 포함할 수 있습니다. 일부 조직에서는 이러한 정보가 고객 관리형 키 정책에 따라 보호된 상태로 유지되어야 하며 암호화된 쿼리를 키로 저장해야 합니다. Azure Monitor를 사용하면 작업 영역에 연결될 때 키로 암호화된 *saved-searches* 및 *log alerts* 쿼리를 자체 스토리지 계정에 저장할 수 있습니다. 

> [!NOTE]
> Log Analytics 쿼리는 사용된 시나리오에 따라 다양한 저장소에 저장할 수 있습니다. 고객 관리형 키 구성(Azure Monitor, Azure 대시보드, Azure 논리 앱, Azure Notebooks 및 자동화 Runbook의 통합 문서)에 관계없이 다음 시나리오에서는 쿼리가 MMK(Microsoft key)로 암호화된 상태로 유지됩니다.

BYOS(Bring Your Own Storage)를 작업 영역에 연결하면 서비스에서 *saved-searches* 및 *log alerts* 쿼리를 스토리지 계정에 업로드합니다. 즉, Log Analytics 클러스터에서 데이터를 암호화하는 데 사용하는 것과 동일한 키 또는 다른 키를 사용하여 스토리지 계정과 [미사용 암호화 정책](../../storage/common/customer-managed-keys-overview.md)을 제어합니다. 그러나 해당 스토리지 계정과 관련된 비용은 사용자가 부담합니다. 

**검색어에 대해 고객 관리형 키를 설정하기 전 고려 사항**
* 작업 영역 및 스토리지 계정에 대한 '쓰기' 권한이 있어야 합니다.
* Log Analytics 작업 영역이 있는 동일한 지역에 스토리지 계정을 만들어야 합니다.
* 스토리지의 *검색 저장* 은 서비스 아티팩트로 간주되며 형식이 변경될 수 있습니다.
* 기존 *검색 저장* 이 작업 영역에서 제거됩니다. 구성 전에 필요한 모든 *검색을 복사하고 저장* 합니다. [PowerShell](/powershell/module/az.operationalinsights/get-azoperationalinsightssavedsearch)을 사용하여 *저장된 검색* 을 볼 수 있습니다.
* 쿼리 기록은 지원되지 않으므로 실행한 쿼리를 볼 수 없습니다.
* 쿼리를 저장하기 위해 단일 스토리지 계정을 작업 영역에 연결할 수 있지만, *saved-searches* 및 *log-alerts* 쿼리에 모두 사용할 수 있습니다.
* 대시보드에 고정은 지원되지 않습니다.
* 발생한 로그 경고에는 검색 결과 또는 경고 쿼리가 포함되지 않습니다. [경고 차원](../alerts/alerts-unified-log.md#split-by-alert-dimensions)을 사용하여 발생한 경고의 컨텍스트를 가져올 수 있습니다.

**saved-searches 쿼리를 위한 BYOS 구성**

*쿼리* 를 위해 스토리지 계정을 작업 영역에 연결합니다. *saved-searches* 쿼리는 스토리지 계정에 저장됩니다. 

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

해당 없음

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
$storageAccountId = '/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage name>'

az account set --subscription "workspace-subscription-id"

az monitor log-analytics workspace linked-storage create --type Query --resource-group "resource-group-name" --workspace-name "workspace-name" --storage-accounts $storageAccountId
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
$storageAccount.Id = Get-AzStorageAccount -ResourceGroupName "resource-group-name" -Name "storage-account-name"

Select-AzSubscription "workspace-subscription-id"

New-AzOperationalInsightsLinkedStorageAccount -ResourceGroupName "resource-group-name" -WorkspaceName "workspace-name" -DataSourceType Query -StorageAccountIds $storageAccount.Id
```

# <a name="rest"></a>[REST (영문)](#tab/rest)

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/workspaces/<workspace-name>/linkedStorageAccounts/Query?api-version=2021-06-01
Authorization: Bearer <token> 
Content-type: application/json
 
{
  "properties": {
    "dataSourceType": "Query", 
    "storageAccountIds": 
    [
      "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-account-name>"
    ]
  }
}
```

---

구성 후에는 새 *저장된 검색* 쿼리가 스토리지에 저장됩니다.

**log alerts 쿼리를 위한 BYOS 구성**

*Alerts* 스토리지 계정을 작업 영역에 연결 - *log alerts* 쿼리가 스토리지 계정에 저장됩니다. 

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

해당 없음

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
$storageAccountId = '/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage name>'

az account set --subscription "workspace-subscription-id"

az monitor log-analytics workspace linked-storage create --type ALerts --resource-group "resource-group-name" --workspace-name "workspace-name" --storage-accounts $storageAccountId
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
$storageAccount.Id = Get-AzStorageAccount -ResourceGroupName "resource-group-name" -Name "storage-account-name"

Select-AzSubscription "workspace-subscription-id"

New-AzOperationalInsightsLinkedStorageAccount -ResourceGroupName "resource-group-name" -WorkspaceName "workspace-name" -DataSourceType Alerts -StorageAccountIds $storageAccount.Id
```

# <a name="rest"></a>[REST (영문)](#tab/rest)

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/workspaces/<workspace-name>/linkedStorageAccounts/Alerts?api-version=2021-06-01
Authorization: Bearer <token> 
Content-type: application/json
 
{
  "properties": {
    "dataSourceType": "Alerts", 
    "storageAccountIds": 
    [
      "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-account-name>"
    ]
  }
}
```

---

구성 후에는 새 경고 쿼리가 스토리지에 저장됩니다.

## <a name="customer-lockbox-preview"></a>고객 Lockbox(미리 보기)

Lockbox는 지원 요청 중에 데이터에 액세스하는 Microsoft 엔지니어 요청을 승인하거나 거부할 수 있는 컨트롤을 제공합니다.

Azure Monitor에서 Log Analytics 전용 클러스터에 연결된 작업 영역의 데이터에 대해 이 컨트롤을 사용할 수 있습니다. Lockbox 컨트롤은 Lockbox로 보호된 구독의 클러스터 스토리지 계정에서 격리된 상태로 유지되는 Log Analytics 전용 클러스터에 저장된 데이터에 적용됩니다.  

[Microsoft Azure용 고객 Lockbox](../../security/fundamentals/customer-lockbox-overview.md)에 대해 자세히 알아보세요.

## <a name="customer-managed-key-operations"></a>고객 관리형 키 작업

고객 관리형 키는 전용 클러스터에서 제공되며 이러한 작업은 [전용 클러스터 문서](./logs-dedicated-clusters.md#change-cluster-properties)를 참조하세요.

- 리소스 그룹의 모든 클러스터 가져오기  
- 구독의 모든 클러스터 가져오기
- 클러스터의 *용량 예약* 업데이트
- 클러스터에서 *billingType* 업데이트
- 클러스터에서 작업 영역 연결 끊기
- 클러스터 삭제

## <a name="limitations-and-constraints"></a>제한 사항 및 제약 조건

- 지역 및 구독당 최대 클러스터 수는 2입니다.

- 클러스터에 연결할 수 있는 최대 작업 영역 수는 1000입니다.

- 작업 영역을 클러스터에 연결한 다음 연결을 끊을 수 있습니다. 특정 작업 영역에 대한 작업 영역 링크 작업 수는 30일 동안 2로 제한됩니다.

- 고객 관리형 키 암호화는 구성 시간 이후 새로 수집된 데이터에 적용됩니다. 구성 이전에 수집된 데이터는 Microsoft 키로 암호화된 상태로 유지됩니다. 고객 관리형 키 구성 전후에 수집된 데이터를 원활하게 쿼리할 수 있습니다.

- Azure Key Vault는 복구 가능으로 구성해야 합니다. 이러한 속성은 기본적으로 사용하도록 설정되지 않으므로 CLI 또는 PowerShell을 사용하여 구성해야 합니다.<br>
  - [일시 삭제](../../key-vault/general/soft-delete-overview.md)
  - 일시 삭제 후에도 비밀/자격 증명 모음의 강제 삭제로부터 보호하려면 [제거 보호](../../key-vault/general/soft-delete-overview.md#purge-protection)를 켜야 합니다.

- 다른 리소스 그룹 또는 구독으로의 클러스터 이동은 현재 지원되지 않습니다.

- Azure Key Vault, 클러스터 및 작업 영역은 동일한 지역 및 동일한 Azure AD(Azure Active Directory) 테넌트에 있어야 하지만 다른 구독에 있을 수 있습니다.

- 클러스터 업데이트 시 동일한 작업에 ID 및 키 식별자 세부 정보를 둘 다 포함해서는 안 됩니다. 둘 다 업데이트해야 하는 경우 업데이트 작업을 두 번 연속해서 수행해야 합니다.

- 현재 중국에서는 Lockbox를 사용할 수 없습니다. 

- [이중 암호화](../../storage/common/storage-service-encryption.md#doubly-encrypt-data-with-infrastructure-encryption)는 지원되는 지역에서 2020년 10월부터 만들어진 클러스터에 자동으로 구성됩니다. 클러스터에서 GET 요청을 보내고 이중 암호화가 사용 가능한 클러스터의 `isDoubleEncryptionEnabled` 값이 `true`인지 관찰하여 클러스터가 이중 암호화로 구성되었는지 확인할 수 있습니다. 
  - 클러스터를 만들 때 “region-name은 클러스터의 이중 암호화를 지원하지 않습니다.” 오류가 발생하는 경우 REST 요청 본문에 `"properties": {"isDoubleEncryptionEnabled": false}`를 추가하여 이중 암호화 없이 클러스터를 만들 수 있습니다.
  - 클러스터를 만든 후에는 이중 암호화 설정을 변경할 수 없습니다.

  - 클러스터의 `identity` `type`을 `None`으로 설정하면 데이터에 대한 액세스도 철회되지만, 이 접근 방식은 지원 담당자에게 문의하지 않고 되돌릴 수 없기 때문에 권장되지 않습니다. 데이터에 대한 액세스를 철회하는 데 권장되는 방법은 [키 해지](#key-revocation)입니다.

  - Key Vault가 Private-Link(vNet)에 있는 경우 사용자가 할당한 관리형 ID와 함께 고객 관리형 키를 사용할 수 없습니다. 이 시나리오에서는 시스템이 할당한 관리 ID를 사용할 수 있습니다.

## <a name="troubleshooting"></a>문제 해결

- Key Vault 가용성과 관련된 동작
  - 정상 작업에서 - 스토리지에서 AEK를 짧은 시간 동안 캐시하고, Key Vault로 돌아가서 래핑을 주기적으로 해제합니다.
    
  - Key Vault 연결 오류 -- 스토리지는 가용성 문제가 지속되는 동안 키가 캐시에 유지되도록 허용하여 일시적인 오류(시간 초과, 연결 실패, DNS 문제)를 처리하며 이는 일시적 오류 및 가용성 문제를 해결합니다. 쿼리 및 수집 기능은 중단 없이 계속됩니다.
    
- Key Vault 액세스 속도 - 래핑 및 래핑 해제 작업을 위해 Azure Monitor 스토리지에서 Key Vault에 액세스하는 빈도는 6-60초입니다.

- 클러스터를 프로비저닝하거나 상태를 업데이트하는 동안 클러스터를 업데이트하는 경우 업데이트는 실패합니다.

- 클러스터를 만들 때 충돌 오류가 발생하는 경우 – 일시 삭제 기간 내에 지난 14일 동안 클러스터를 삭제했을 수 있습니다. 클러스터 이름은 일시 삭제 기간 동안 예약된 상태로 유지되며, 해당 이름을 사용하여 새 클러스터를 만들 수 없습니다. 클러스터가 영구적으로 삭제되면 일시 삭제 기간 후에 이름이 해제됩니다.

- 다른 클러스터에 연결된 경우에는 클러스터에 대한 작업 영역 링크가 실패합니다.

- 클러스터를 만들고 KeyVaultProperties를 즉시 지정하면 시스템 ID가 클러스터에 할당될 때까지 액세스 정책을 정의할 수 없으므로 작업이 실패할 수 있습니다.

- 기존 클러스터를 KeyVaultProperties로 업데이트하고 키 '가져오기' 액세스 정책이 Key Vault에 없으면 작업이 실패합니다.

- 클러스터를 배포하지 못한 경우 Azure Key Vault, 클러스터 및 연결된 Log Analytics 작업 영역이 동일한 지역에 있는지 확인합니다. 서로 다른 구독에 있을 수 있습니다.

- Key Vault에서 키 버전을 업데이트하고 클러스터의 새 키 식별자 세부 정보를 업데이트하지 않으면 Log Analytics 클러스터에서 이전 키를 계속 사용하므로 데이터에 액세스할 수 없게 됩니다. 클러스터의 새 키 식별자 세부 정보를 업데이트하여 데이터 수집 및 데이터 쿼리 기능을 다시 시작합니다.

- 일부 작업은 완료하는 데 시간이 오래 걸릴 수 있습니다(클러스터 만들기, 클러스터 키 업데이트 및 클러스터 삭제). 클러스터 또는 작업 영역에 GET 요청을 전송 하 고 응답을 관찰 하 여 작업 상태를 확인할 수 있습니다. 예를 들어 연결되지 않은 작업 영역에는 *features* 아래에 *clusterResourceId* 가 없습니다.

- 오류 메시지
  
  **클러스터 만들기**
  -  400 -- 클러스터 이름이 잘못되었습니다. 클러스터 이름에는 문자 a-z, A-Z, 0-9를 3~63자 범위로 사용할 수 있습니다.
  -  400 -- 요청 본문이 null이거나 형식이 잘못되었습니다.
  -  400 -- SKU 이름이 잘못되었습니다. SKU 이름을 capacityReservation으로 설정합니다.
  -  400 -- 용량이 제공되었지만 SKU가 capacityReservation이 아닙니다. SKU 이름을 capacityReservation으로 설정합니다.
  -  400 -- SKU의 용량이 누락되었습니다. 용량 값을 500, 1,000, 2,000 또는 5,000GB/일로 설정합니다.
  -  400 -- 용량이 30일 동안 잠겨 있습니다. 업데이트 30일 후에는 용량을 줄일 수 있습니다.
  -  400 -- SKU가 설정되지 않았습니다. SKU 이름을 capacityReservation으로 설정하고 용량 값을 500, 1,000, 2,000 또는 5,000GB/일로 설정합니다.
  -  400 -- ID가 null이거나 비어 있습니다. SystemAssigned 유형으로 ID를 설정합니다.
  -  400 -- KeyVaultProperties는 만들 때 설정됩니다. 클러스터를 만든 후 KeyVaultProperties를 업데이트합니다.
  -  400 -- 지금은 작업을 실행할 수 없습니다. 비동기 작업이 성공함 이외의 상태에 있습니다. 업데이트 작업을 수행하기 전에 클러스터에서 해당 작업을 완료해야 합니다.

  **클러스터 업데이트**
  -  400 -- 클러스터가 삭제 중 상태입니다. 비동기 작업이 진행 중입니다. 업데이트 작업을 수행하기 전에 클러스터에서 해당 작업을 완료해야 합니다.
  -  400 -- KeyVaultProperties가 비어 있지 않지만 형식이 잘못되었습니다. [키 식별자 업데이트](#update-cluster-with-key-identifier-details)를 참조하세요.
  -  400 -- 키 자격 증명 모음의 키 유효성을 검사하지 못했습니다. 권한이 없거나 키가 존재하지 않기 때문일 수 있습니다. 키 자격 증명 모음에서 [키 및 액세스 정책을 설정](#grant-key-vault-permissions)했는지 확인합니다.
  -  400 -- 키를 복구할 수 없습니다. Key Vault를 일시 삭제 및 제거 보호로 설정해야 합니다. [Key Vault 설명서](../../key-vault/general/soft-delete-overview.md)를 참조하세요.
  -  400 -- 지금은 작업을 실행할 수 없습니다. 비동기 작업이 완료될 때까지 기다렸다가 다시 시도하세요.
  -  400 -- 클러스터가 삭제 중 상태입니다. 비동기 작업이 완료될 때까지 기다렸다가 다시 시도하세요.

  **클러스터 가져오기**
    -  404 -- 클러스터가 없습니다. 클러스터가 삭제되었을 수 있습니다. 해당 이름을 사용하여 클러스터를 만들려고 하며, 충돌하는 경우 클러스터는 14일 동안 일시 삭제 상태가 됩니다. 지원 서비스에 문의하여 복구하거나 다른 이름을 사용하여 새 클러스터를 만들 수 있습니다. 

  **클러스터 삭제**
    -  409 -- 프로비저닝 상태에 있는 동안에는 클러스터를 삭제할 수 없습니다. 비동기 작업이 완료될 때까지 기다렸다가 다시 시도하세요.

  **작업 영역 연결**
  -  404 -- 작업 영역을 찾을 수 없습니다. 지정한 작업 영역이 존재하지 않거나 삭제되었습니다.
  -  409 -- 작업 영역 연결 또는 연결 해제 작업이 진행 중입니다.
  -  400 -- 클러스터를 찾을 수 없습니다. 지정한 클러스터가 존재하지 않거나 삭제되었습니다. 해당 이름을 사용하여 클러스터를 만들려고 하며, 충돌하는 경우 클러스터는 14일 동안 일시 삭제 상태가 됩니다. 지원 서비스에 문의하여 복구할 수 있습니다.

  **작업 영역 연결 해제**
  -  404 -- 작업 영역을 찾을 수 없습니다. 지정한 작업 영역이 존재하지 않거나 삭제되었습니다.
  -  409 -- 작업 영역 연결 또는 연결 해제 작업이 진행 중입니다.
## <a name="next-steps"></a>다음 단계

- [Log Analytics 전용 클러스터 청구](./manage-cost-storage.md#log-analytics-dedicated-clusters)에 대해 알아보기
- [Log Analytics 작업 영역의 적절한 디자인](./design-logs-deployment.md)에 대해 알아보기
