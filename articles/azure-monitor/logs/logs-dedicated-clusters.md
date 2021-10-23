---
title: Azure Monitor 로그 전용 클러스터
description: 최소 약정 계층을 충족 하는 고객은 전용 클러스터를 사용할 수 있습니다.
ms.topic: conceptual
author: yossi-y
ms.author: yossiy
ms.date: 07/29/2021
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: c56b2a5f3cc601f2c66b6577d250eed813228271
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130248196"
---
# <a name="azure-monitor-logs-dedicated-clusters"></a>Azure Monitor 로그 전용 클러스터

Azure Monitor 로그 전용 클러스터는 Azure Monitor 로그 고객이 고급 기능을 사용할 수 있게 해주는 배포 옵션입니다. 고객은 전용 클러스터에서 호스트되어야 하는 Log Analytics 작업 영역을 선택할 수 있습니다.

전용 클러스터를 통해 고객은 하루에 최소 500 GB의 데이터 수집을 커밋할 수 있습니다. 데이터 손실이나 서비스 중단 없이 기존 작업 영역을 전용 클러스터로 마이그레이션할 수 있습니다. 

전용 클러스터가 필요한 기능:

- **[고객 관리형 키](../logs/customer-managed-keys.md)** - 고객이 제공하고 제어하는 키를 사용하여 클러스터 데이터를 암호화합니다.
- **[Lockbox](../logs/customer-managed-keys.md#customer-lockbox-preview)** - Microsoft 지원 엔지니어의 데이터 액세스 요청을 제어합니다.
- **[이중 암호화](../../storage/common/storage-service-encryption.md#doubly-encrypt-data-with-infrastructure-encryption)** - 암호화 알고리즘 또는 키 중 하나가 손상될 수 있는 상황으로부터 보호합니다. 이 경우 추가 암호화 계층은 계속해서 데이터를 보호합니다.
- **[가용성 영역](./availability-zones.md)** - 전용 클러스터의 가용성 영역과 관련된 데이터 센터 오류로부터 데이터를 보호합니다. 가용성 영역은 분리된 물리적 위치에 있는 데이터 센터이며 독립적인 전원, 냉각, 네트워킹 등을 갖추고 있습니다. 영역의 이 독립적인 인프라와 물리적 분리를 사용하면 작업 영역이 영역의 리소스를 사용할 수 있기 때문에 인시던트 발생 가능성이 훨씬 낮아집니다.
- **[다중 작업 영역](../logs/cross-workspace-query.md)** - 고객이 프로덕션에 둘 이상의 작업 영역을 사용하는 경우 전용 클러스터를 사용하는 것이 적합할 수 있습니다. 모든 작업 영역이 동일한 클러스터에 있으면 작업 영역 간 쿼리가 더 신속하게 실행됩니다. 또한 할당된 약정 계층이 모든 클러스터 수집을 고려하고, 그중 일부가 작고 약정 계층 할인 대상이 아니더라도 모든 작업 영역에 적용되므로 전용 클러스터를 사용하는 것이 더 비용 효율적일 수 있습니다.


## <a name="management"></a>관리 

전용 클러스터는 Azure Monitor 로그 클러스터를 나타내는 Azure 리소스를 사용하여 관리됩니다. 작업은 [CLI](/cli/azure/monitor/log-analytics/cluster), [PowerShell](/powershell/module/az.operationalinsights) 또는 [REST](/rest/api/loganalytics/clusters)를 사용 하 여 프로그래밍 방식으로 수행 됩니다.

클러스터가 만들어지면 작업 영역을 연결 하 고 새 수집 데이터를 클러스터에 저장할 수 있습니다. 언제 든 지 클러스터에서 작업 영역 연결을 끊을 수 있으며 새 데이터는 공유 Log Analytics 클러스터에 저장 됩니다. 링크 및 연결 해제 작업은 작업 영역에서 subjection를 사용 하는 작업 전후에 쿼리 및 데이터 액세스에 영향을 주지 않습니다. 클러스터 및 작업 영역은 연결을 허용 하기 위해 동일한 지역에 있어야 합니다.

클러스터 수준의 모든 작업에는 클러스터에 대한 `Microsoft.OperationalInsights/clusters/write` 작업 권한이 필요합니다. 이 권한은 `*/write` 작업이 포함된 소유자이거나 기여자 또는 `Microsoft.OperationalInsights/*` 작업이 포함된 Log Analytics 기여자 역할을 통해 부여될 수 있습니다. Log Analytics 권한에 대한 자세한 내용은 [Azure Monitor에서 로그 데이터 및 작업 영역에 대한 액세스 관리](./manage-access.md)를 참조하세요. 


## <a name="cluster-pricing-model"></a>클러스터 가격 책정 모델

Log Analytics 전용 클러스터는 최소 500 g b/일의 약정 계층 (이전에는 용량 예약 이라고 함) 가격 책정 모델을 사용 합니다. 계층 수준 이상의 사용량은 해당 약정 계층의 GB당 유효 요금으로 청구됩니다. 약정 계층 가격 책정 정보는 [Azure Monitor 가격 책정 페이지]( https://azure.microsoft.com/pricing/details/monitor/)에서 확인할 수 있습니다.  

클러스터 약정 계층 수준은 `Sku` 아래에서 `Capacity` 매개 변수를 사용하여 Azure Resource Manager를 통해 프로그래밍 방식으로 구성됩니다. `Capacity`는 GB 단위로 지정되며 500, 1,000, 2,000 또는 5,000GB/일 값을 가질 수 있습니다.

클러스터 사용량에 대한 두 가지 청구 모드가 있습니다. 이러한 청구 모드는 클러스터를 구성할 때 `billingType` 매개 변수를 통해 지정할 수 있습니다. 

1. **클러스터(기본값)** : 수집된 데이터에 대한 청구가 클러스터 수준에서 수행됩니다. 클러스터에 연결된 각 작업 영역에서 수집된 데이터 양을 집계하여 클러스터에 대한 일일 청구액을 계산합니다. 

2. **작업 영역**: 클러스터의 약정 계층 비용은 각 작업 영역에 대한 [Azure Security Center](../../security-center/index.yml)의 노드별 할당을 고려한 후 각 작업 영역의 데이터 수집 볼륨에 따라 클러스터의 작업 영역을 기준으로 비례적으로 계산됩니다. 이 가격 책정 모델의 전체 세부 정보는 [여기](./manage-cost-storage.md#log-analytics-dedicated-clusters)에 설명되어 있습니다. 

작업 영역이 레거시 노드당 가격 책정 계층을 사용 하는 경우 클러스터에 연결 된 경우 클러스터의 약정 계층에 대 한 데이터 수집 기준으로 청구 되며 노드당 더 이상 청구 되지 않습니다. Azure Security Center의 노드별 데이터 할당은 계속 적용됩니다.

Log Analytics 전용 클러스터 청구에 대한 자세한 내용은 [여기](./manage-cost-storage.md#log-analytics-dedicated-clusters)에서 확인할 수 있습니다.

## <a name="create-a-dedicated-cluster"></a>전용 클러스터 만들기

새 전용 클러스터를 만들 때 다음 속성을 지정해야 합니다.

- **ClusterName**
- **ResourceGroupName**: 일반적으로 클러스터는 조직의 많은 팀에서 공유 되기 때문에 중앙 IT 리소스 그룹을 사용 해야 합니다. 추가적인 디자인 고려 사항은 [Azure Monitor 로그 배포 디자인](../logs/design-logs-deployment.md)을 참조하세요.
- **위치**
- **SkuCapacity**: 약정 계층 (이전에는 용량 예약 이라고 함)을 500, 1000, 2000 또는 5000 g b/일로 설정할 수 있습니다. 클러스터 비용에 대한 자세한 내용은 [Log Analytics 클러스터의 비용 관리](./manage-cost-storage.md#log-analytics-dedicated-clusters)를 참조하세요. 

클러스터를 만드는 사용자 계정에는 표준 Azure 리소스 만들기 권한 `Microsoft.Resources/deployments/*` 및 클러스터 쓰기 권한`Microsoft.OperationalInsights/clusters/write`이 있어야 하며, 이들 권한은 역할 할당 시 이 특정 작업 또는 `Microsoft.OperationalInsights/*` 또는 `*/write`를 포함하여 부여할 수 있습니다.

cluster 리소스를 만든 후에는 *sku*, *keyVaultProperties 또는 *billingType* 과 같은 추가 속성을 편집할 수 있습니다. 자세한 내용은 아래를 참조하세요.

지역별로 구독당 최대 2개의 활성 클러스터를 사용할 수 있습니다. 클러스터가 삭제되어도 14일 동안 계속 예약되어 있습니다. 지역별로 구독당 최대 4개의 예약된 클러스터를 사용할 수 있습니다(활성 또는 최근 삭제).

> [!NOTE]
> 클러스터를 만들면 리소스 할당 및 프로비전이 트리거됩니다. 이 작업을 완료하는 데 몇 시간 정도 걸릴 수 있습니다.
> 전용 클러스터는 데이터 수집에 상관 없이 프로 비전 된 후에만 요금이 청구 되며, 배포를 준비 하 여 클러스터에 대 한 프로 비전 및 작업 영역 링크를 신속 하 게 배포 합니다 다음을 확인합니다.
> - 클러스터에 연결할 초기 작업 영역 목록이 식별 됨
> - 클러스터 및 연결할 작업 영역에 대 한 구독에 대 한 사용 권한이 있습니다.

**CLI**
```azurecli
az account set --subscription "cluster-subscription-id"

az monitor log-analytics cluster create --no-wait --resource-group "resource-group-name" --name "cluster-name" --location "region-name" --sku-capacity "daily-ingestion-gigabyte"

# Wait for job completion when `--no-wait` was used
$clusterResourceId = az monitor log-analytics cluster list --resource-group "resource-group-name" --query "[?contains(name, "cluster-name")].[id]" --output tsv
az resource wait --created --ids $clusterResourceId --include-response-body true
```

**PowerShell**

```powershell
Select-AzSubscription "cluster-subscription-id"

New-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name" -Location "region-name" -SkuCapacity "daily-ingestion-gigabyte" -AsJob

# Check when the job is done when `-AsJob` was used
Get-Job -Command "New-AzOperationalInsightsCluster*" | Format-List -Property *
```

**REST API**

*호출* 

```rest
PUT https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2021-06-01
Authorization: Bearer <token>
Content-type: application/json

{
  "identity": {
    "type": "systemAssigned"
    },
  "sku": {
    "name": "capacityReservation",
    "Capacity": 500
    },
  "properties": {
    "billingType": "Cluster",
    },
  "location": "<region>",
}
```

*응답*

202 (Accepted) 및 헤더여야 합니다.

### <a name="check-cluster-provisioning-status"></a>클러스터 프로비전 상태 확인

Log Analytics 클러스터 프로비전을 완료하는 데는 시간이 걸립니다. 다음 방법 중 하나를 사용하여 *ProvisioningState* 속성을 확인합니다. 값은 프로비전 중에는 *ProvisioningAccount* 이고 완료 시에는 *Succeeded* 입니다.

**CLI**

```azurecli
az account set --subscription "cluster-subscription-id"

az monitor log-analytics cluster show --resource-group "resource-group-name" --name "cluster-name"
```

**PowerShell**

```powershell
Select-AzSubscription "cluster-subscription-id"

Get-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name"
```
 
**REST API**

GET 요청을 클러스터 리소스에 보내고 *provisioningState* 값을 확인합니다. 값은 프로비전 중에는 *ProvisioningAccount* 이고 완료 시에는 *Succeeded* 입니다.

  ```rest
  GET https://management.azure.com/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name?api-version=2021-06-01
  Authorization: Bearer <token>
  ```

  **응답**

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
      "provisioningState": "ProvisioningAccount",
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

클러스터를 만들 때 관리 ID 서비스에서 *principalId* GUID가 생성됩니다.

---


## <a name="link-a-workspace-to-a-cluster"></a>클러스터에 작업 영역 연결

Log Analytics 작업 영역이 전용 클러스터에 연결 된 경우 기존 데이터가 기존 클러스터에 남아 있는 동안 작업 영역에 대 한 새 데이터 수집 새 클러스터로 라우팅됩니다. 전용 클러스터를 CMK(고객 관리형 키)로 암호화하는 경우 키로 새 데이터만 이 키로 암호화됩니다. 시스템은 이 차이를 추상화하므로 시스템이 백그라운드에서 클러스터 간 쿼리를 수행하는 동안 평소처럼 작업 영역을 쿼리할 수 있습니다.

하나의 클러스터에 최대 1,000개의 작업 영역을 연결할 수 있습니다. 연결된 작업 영역은 클러스터와 동일한 지역에 위치합니다. 시스템 백 엔드를 보호 하 고 데이터 조각화를 방지 하려면 작업 영역을 한 달에 두 번 이상 클러스터에 연결할 수 없습니다.

연결 작업을 수행하려면 작업 영역과 cluster 리소스 모두에 대한 '쓰기' 권한이 있어야 합니다.

- 작업 영역: *Microsoft.OperationalInsights/workspaces/write*
- cluster 리소스: *Microsoft.OperationalInsights/clusters/write*

청구 측면 외에도 연결된 작업 영역은 데이터 보존 기간과 같은 자체 설정을 유지합니다.

작업 영역과 클러스터는 서로 다른 구독에 포함될 수 있습니다. Azure Lighthouse를 사용하여 둘을 단일 테넌트에 매핑한다면 작업 영역과 클러스터가 서로 다른 테넌트에 포함될 수 있습니다.

작업 영역 연결은 Log Analytics 클러스터 프로비저닝이 완료된 후에만 수행할 수 있습니다.

> [!WARNING]
> 작업 영역을 클러스터에 연결하려면 여러 백 엔드 구성 요소를 동기화하고 캐시 하이드레이션을 보장해야 합니다. 이 작업을 완료하는 데 최대 2시간이 걸릴 수 있기 때문에 비동기적으로 실행해야 합니다.

다음 명령을 사용하여 작업 영역을 클러스터에 연결합니다.

**CLI**
```azurecli
# Find cluster resource ID
az account set --subscription "cluster-subscription-id"
$clusterResourceId = az monitor log-analytics cluster list --resource-group "resource-group-name" --query "[?contains(name, "cluster-name")].[id]" --output tsv

# Link workspace
az account set --subscription "workspace-subscription-id"
az monitor log-analytics workspace linked-service create --no-wait --name cluster --resource-group "resource-group-name" --workspace-name "workspace-name" --write-access-resource-id $clusterResourceId

# Wait for job completion when `--no-wait` was used
$workspaceResourceId = az monitor log-analytics workspace list --resource-group "resource-group-name" --query "[?contains(name, "workspace-name")].[id]" --output tsv
az resource wait --deleted --ids $workspaceResourceId --include-response-body true
```

**PowerShell**

```powershell
Select-AzSubscription "cluster-subscription-id"

# Find cluster resource ID
$clusterResourceId = (Get-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name").id

Select-AzSubscription "workspace-subscription-id"

# Link the workspace to the cluster
Set-AzOperationalInsightsLinkedService -ResourceGroupName "resource-group-name" -WorkspaceName "workspace-name" -LinkedServiceName cluster -WriteAccessResourceId $clusterResourceId -AsJob

# Check when the job is done
Get-Job -Command "Set-AzOperationalInsightsLinkedService" | Format-List -Property *
```

**REST API**

다음 REST 호출을 사용하여 클러스터에 연결합니다.

‘보내기’

```rest
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>/linkedservices/cluster?api-version=2021-06-01 
Authorization: Bearer <token>
Content-type: application/json

{
  "properties": {
    "WriteAccessResourceId": "/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/clusters/<cluster-name>"
    }
}
```

*응답*

202 (Accepted) 및 헤더.

---

### <a name="check-workspace-link-status"></a>작업 영역 연결 상태 확인
  
클러스터가 고객 관리형 키로 구성된 경우 연결 작업 완료 후 작업 영역에 수집된 데이터는 관리형 키로 암호화되어 저장됩니다. 작업 영역 링크 작업을 완료하는 데 최대 90분이 걸릴 수 있으며, Get request to workspace를 전송하여 상태를 확인하고 *기능* 아래의 응답에 *clusterResourceId* 속성이 있는지 확인할 수 있습니다.

**CLI**
```azurecli
az account set --subscription "workspace-subscription-id"

az monitor log-analytics workspace show --resource-group "resource-group-name" --workspace-name "workspace-name"
```

**PowerShell**

```powershell
Select-AzSubscription "workspace-subscription-id"

Get-AzOperationalInsightsWorkspace -ResourceGroupName "resource-group-name" -Name "workspace-name"
```

**REST API**

*호출*

```rest
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>?api-version=2021-06-01
Authorization: Bearer <token>
```

*응답*

```json
{
  "properties": {
    "source": "Azure",
    "customerId": "workspace-name",
    "provisioningState": "Succeeded",
    "sku": {
      "name": "pricing-tier-name",
      "lastSkuUpdate": "Tue, 28 Jan 2020 12:26:30 GMT"
    },
    "retentionInDays": 31,
    "features": {
      "legacy": 0,
      "searchVersion": 1,
      "enableLogAccessUsingOnlyResourcePermissions": true,
      "clusterResourceId": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name"
    },
    "workspaceCapping": {
      "dailyQuotaGb": -1.0,
      "quotaNextResetTime": "Tue, 28 Jan 2020 14:00:00 GMT",
      "dataIngestionStatus": "RespectQuota"
    }
  },
  "id": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/microsoft.operationalinsights/workspaces/workspace-name",
  "name": "workspace-name",
  "type": "Microsoft.OperationalInsights/workspaces",
  "location": "region"
}
```

---


## <a name="change-cluster-properties"></a>클러스터 속성 변경

클러스터 리소스를 만들고 완전히 프로비전한 후 CLI, PowerShell 또는 REST API 사용하여 추가 속성을 편집할 수 있습니다. 클러스터가 프로비저닝된 후 설정할 수 있는 추가 속성은 다음과 같습니다.

- **keyVaultProperties** - *KeyVaultUri*, *KeyName*, *KeyVersion* 매개 변수와 함께 Azure Key Vault의 키를 포함합니다. [키 식별자 세부 정보로 클러스터 업데이트](../logs/customer-managed-keys.md#update-cluster-with-key-identifier-details)를 참조하세요.
- **ID** - Key Vault에 인증하는 데 사용되는 ID입니다. 시스템 할당 또는 사용자 할당일 수 있습니다.
- **billingType** - 클러스터 리소스와 해당 데이터에 대한 청구 특성입니다. 다음 값을 포함합니다.
  - **클러스터(기본값)** - 클러스터 비용이 클러스터 리소스를 기준으로 계산됩니다.
  - **작업 영역** - 클러스터 비용이 클러스터의 작업 영역을 기준으로 비례적으로 계산되고, 해당 날짜에 수집된 총 데이터가 약정 계층 미만인 경우 클러스터 리소스에 일부 사용량이 청구됩니다. 클러스터 가격 책정 모델에 관한 자세한 내용은 [Log Analytics 전용 클러스터](./manage-cost-storage.md#log-analytics-dedicated-clusters)를 참조하세요.


>[!IMPORTANT]
>클러스터 업데이트 시 동일한 작업에 ID 및 키 식별자 세부 정보를 둘 다 포함해서는 안 됩니다. 둘 다 업데이트해야 하는 경우 두 번의 연속 작업으로 업데이트를 수행해야 합니다.

> [!NOTE]
> *billingType* 속성은 PowerShell에서 지원되지 않습니다.

## <a name="get-all-clusters-in-resource-group"></a>리소스 그룹의 모든 클러스터 가져오기

**CLI**

```azurecli
az account set --subscription "cluster-subscription-id"

az monitor log-analytics cluster list --resource-group "resource-group-name"
```

**PowerShell**

```powershell
Select-AzSubscription "cluster-subscription-id"

Get-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name"
```

**REST API**

*호출*

```rest
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters?api-version=2021-06-01
Authorization: Bearer <token>
```

*응답*
  
```json
{
  "value": [
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
  ]
}
```

---



## <a name="get-all-clusters-in-subscription"></a>구독의 모든 클러스터 가져오기

**CLI**

```azurecli
az account set --subscription "cluster-subscription-id"

az monitor log-analytics cluster list
```

**PowerShell**

```powershell
Select-AzSubscription "cluster-subscription-id"

Get-AzOperationalInsightsCluster
```
**REST API**

*호출*

```rest
GET https://management.azure.com/subscriptions/<subscription-id>/providers/Microsoft.OperationalInsights/clusters?api-version=2021-06-01
Authorization: Bearer <token>
```
    
*응답*
    
‘리소스 그룹의 클러스터’와 동일한 응답이지만 구독 범위에 있습니다.

---


## <a name="update-commitment-tier-in-cluster"></a>클러스터의 약정 계층 업데이트

연결된 작업 영역의 데이터 볼륨이 시간이 지남에 따라 변경되어 약정 계층 수준을 적절하게 업데이트하려는 경우입니다. 계층은 GB 단위로 지정되며 500, 1,000, 2,000 또는 5,000GB/일 값을 가질 수 있습니다. 전체 REST 요청 본문을 제공할 필요는 없지만 sku를 포함해야 합니다.

**CLI**

```azurecli
az account set --subscription "cluster-subscription-id"

az monitor log-analytics cluster update --resource-group "resource-group-name" --name "cluster-name"  --sku-capacity 500
```

### <a name="powershell"></a>PowerShell

```powershell
Select-AzSubscription "cluster-subscription-id"

Update-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name" -SkuCapacity 500
```

### <a name="rest-api"></a>REST API

*호출*

```rest
PATCH https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2021-06-01
Authorization: Bearer <token>
Content-type: application/json

{
  "sku": {
    "name": "capacityReservation",
    "Capacity": 2000
  }
}
```

---


### <a name="update-billingtype-in-cluster"></a>클러스터에서 billingType 업데이트

*billingType* 속성은 클러스터와 해당 데이터에 대한 청구 특성을 결정합니다.
- ‘클러스터’(기본값) - 청구는 클러스터 리소스를 기준으로 계산됩니다.
- ‘작업 영역’ - 청구는 연결된 작업 영역을 기준으로 비례적으로 계산됩니다. 모든 작업 영역의 데이터 볼륨이 약정 계층 수준 미만이면 나머지 볼륨은 클러스터를 기준으로 계산됩니다.

**REST (영문)**

*호출*

```rest
PATCH https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2021-06-01
Authorization: Bearer <token>
Content-type: application/json

{
  "properties": {
    "billingType": "Workspaces",
    }  
}
```

### <a name="unlink-a-workspace-from-cluster"></a>클러스터에서 작업 영역 연결 끊기

클러스터와 작업 영역의 연결을 해제할 수 있습니다. 클러스터와 작업 영역의 연결을 해제한 후에는 이 작업 영역과 연결된 새 데이터가 전용 클러스터로 전송되지 않습니다. 또한 작업 영역 청구가 더 이상 클러스터를 통해 수행되지 않습니다. 하지만 연결되지 않은 작업 영역의 이전 데이터가 클러스터에 남아 있을 수 있습니다. 이 데이터가 CMK(고객 관리형 키)로 암호화되어 있으면 Key Vault 비밀이 유지됩니다. 이러한 변경은 추상화되어 Log Analytics 사용자에게 노출되지 않습니다. 사용자는 평소처럼 작업 영역을 쿼리할 수 있습니다. 시스템은 필요에 따라 백 엔드에서 클러스터 간 쿼리를 수행하며, 이런 내용을 사용자에게 표시하지 않습니다.  

> [!WARNING] 
> 특정 작업 영역에 한 달 동안 수행 가능한 연결 작업 횟수는 2회로 제한됩니다. 따라서 연결을 해제할 때는 신중하게 고려하고 계획해야 합니다.

클러스터와 작업 영역의 연결을 해제하려면 다음 명령을 사용합니다.

**CLI**

```azurecli
az account set --subscription "workspace-subscription-id"

az monitor log-analytics workspace linked-service delete --resource-group "resource-group-name" --workspace-name "workspace-name" --name cluster
```

**PowerShell**

```powershell
Select-AzSubscription "workspace-subscription-id"

# Unlink a workspace from cluster
Remove-AzOperationalInsightsLinkedService -ResourceGroupName "resource-group-name" -WorkspaceName {workspace-name} -LinkedServiceName cluster
```

---


## <a name="delete-cluster"></a>클러스터 삭제

삭제하기 전에 전용 클러스터에서 모든 작업 영역의 연결을 해제하는 것이 좋습니다. 클러스터 리소스에 대한 *쓰기* 권한이 있어야 합니다. 클러스터를 삭제할 때 연결된 작업 영역 및 이전에 연결된 작업 영역에서 클러스터에 수집된 모든 데이터에 대한 액세스 권한이 손실됩니다. 이 작업은 되돌릴 수 없습니다. 작업 영역이 연결될 때 클러스터를 삭제하면 자동으로 연결되지 않고 새 데이터가 Log Analytics 스토리지에 수집됩니다.

지난 14일 동안 삭제된 클러스터 리소스는 일시 삭제 상태로 유지되고 해당 이름은 예약된 상태로 유지됩니다. 일시 삭제 기간이 지나면 클러스터가 영구적으로 삭제되고 이름을 사용할 수 있습니다.

> [!WARNING] 
> - 일시 삭제된 클러스터의 복구는 지원되지 않으며 삭제된 후에는 복구할 수 없습니다.
> - 구독당 4개의 클러스터로 제한됩니다. 활성 삭제 및 일시 삭제된 클러스터가 모두 여기에 계산됩니다. 고객은 클러스터를 만들고 삭제하는 되풀이 프로시저를 만들면 안 됩니다. 이렇게 하면 Log Analytics 백 엔드 시스템에 상당한 영향을 미치게 됩니다.

클러스터를 삭제하려면 다음 명령을 사용합니다.

**CLI**
```azurecli
az account set --subscription "cluster-subscription-id"

az monitor log-analytics cluster delete --resource-group "resource-group-name" --name $clusterName
```

**PowerShell**

```powershell
Select-AzSubscription "cluster-subscription-id"

Remove-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name"
```

**REST API**

클러스터를 삭제하려면 다음 REST 호출을 사용합니다.

```rest
DELETE https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2021-06-01
Authorization: Bearer <token>
```

  **응답**

  200 정상

---



## <a name="limits-and-constraints"></a>제한 및 제약 조건

- 각 지역과 구독에 최대 두 개의 활성 클러스터를 만들 수 있습니다.

- 각 지역과 구독에서 최대 네 개의 예약된 클러스터(활성 또는 최근에 삭제됨)를 만들 수 있습니다.

- 최대 1,000개의 Log Analytics 작업 영역을 클러스터에 연결할 수 있습니다.

- 특정 작업 영역에서 최대 두 개의 작업 영역 연결 작업이 30일 동안 허용됩니다.

- 클러스터를 다른 리소스 그룹 또는 구독으로 이동하는 것은 현재 지원되지 않습니다.

- 클러스터 업데이트 시 동일한 작업에 ID 및 키 식별자 세부 정보를 둘 다 포함해서는 안 됩니다. 둘 다 업데이트해야 하는 경우 업데이트 작업을 두 번 연속해서 수행해야 합니다.

- 현재 중국에서는 Lockbox를 사용할 수 없습니다. 

- [이중 암호화](../../storage/common/storage-service-encryption.md#doubly-encrypt-data-with-infrastructure-encryption)는 지원되는 지역에서 2020년 10월부터 만들어진 클러스터에 자동으로 구성됩니다. 클러스터에서 GET 요청을 보내고 이중 암호화가 사용 가능한 클러스터의 `isDoubleEncryptionEnabled` 값이 `true`인지 관찰하여 클러스터가 이중 암호화로 구성되었는지 확인할 수 있습니다. 
  - 클러스터를 만들고 "region-name은 클러스터에 이중 암호화를 지원하지 않습니다."라는 오류가 발생하면 REST 요청 본문에 를 추가하여 이중 암호화 없이 클러스터를 만들 수 `"properties": {"isDoubleEncryptionEnabled": false}` 있습니다.
  - 클러스터를 만든 후에는 이중 암호화 설정을 변경할 수 없습니다.

## <a name="troubleshooting"></a>문제 해결

- 클러스터를 만들 때 충돌 오류가 발생하면 지난 14일 동안 클러스터를 삭제했으며 일시 삭제 상태일 수 있습니다. 클러스터 이름은 일시 삭제 기간 동안 예약된 상태로 유지되며, 해당 이름을 사용하여 새 클러스터를 만들 수 없습니다. 클러스터가 영구적으로 삭제되면 일시 삭제 기간 후에 이름이 해제됩니다.

- 클러스터를 프로비저닝하거나 상태를 업데이트하는 동안 클러스터를 업데이트하는 경우 업데이트는 실패합니다.

- 일부 작업은 길이가 길고 완료하는 데 시간이 걸릴 수 있습니다. 해당 작업은 ‘클러스터 만들기’, ‘클러스터 키 업데이트’, ‘클러스터 삭제’입니다.   GET 요청을 클러스터 또는 작업 영역으로 전송하여 작업 상태를 확인하고 응답을 관찰할 수 있습니다. 예를 들어 연결되지 않은 작업 영역에는 *features* 아래에 *clusterResourceId* 가 없습니다.

- 다른 클러스터에 연결된 경우에는 클러스터에 대한 작업 영역 링크가 실패합니다.

## <a name="error-messages"></a>오류 메시지
  
### <a name="cluster-create"></a>클러스터 만들기

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

### <a name="cluster-update"></a>클러스터 업데이트

-  400 -- 클러스터가 삭제 중 상태입니다. 비동기 작업이 진행 중입니다. 업데이트 작업을 수행하기 전에 클러스터에서 해당 작업을 완료해야 합니다.
-  400 -- KeyVaultProperties가 비어 있지 않지만 형식이 잘못되었습니다. [키 식별자 업데이트](../logs/customer-managed-keys.md#update-cluster-with-key-identifier-details)를 참조하세요.
-  400 -- 키 자격 증명 모음의 키 유효성을 검사하지 못했습니다. 권한이 부족하거나 키가 없는 경우일 수 있습니다. 키 자격 증명 모음에서 [키 및 액세스 정책을 설정](../logs/customer-managed-keys.md#grant-key-vault-permissions)했는지 확인합니다.
-  400 -- 키를 복구할 수 없습니다. Key Vault를 일시 삭제 및 제거 보호로 설정해야 합니다. [Key Vault 설명서](../../key-vault/general/soft-delete-overview.md)를 참조하세요.
-  400 -- 지금은 작업을 실행할 수 없습니다. 비동기 작업이 완료될 때까지 기다렸다가 다시 시도하세요.
-  400 -- 클러스터가 삭제 중 상태입니다. 비동기 작업이 완료될 때까지 기다렸다가 다시 시도하세요.

### <a name="cluster-get"></a>클러스터 가져오기

 -  404 -- 클러스터가 없습니다. 클러스터가 삭제되었을 수 있습니다. 해당 이름을 사용하여 클러스터를 만들려고 하며, 충돌하는 경우 클러스터는 14일 동안 일시 삭제 상태가 됩니다. 지원 서비스에 문의하여 복구하거나 다른 이름을 사용하여 새 클러스터를 만들 수 있습니다. 

### <a name="cluster-delete"></a>클러스터 삭제

 -  409 -- 프로비저닝 상태에 있는 동안에는 클러스터를 삭제할 수 없습니다. 비동기 작업이 완료될 때까지 기다렸다가 다시 시도하세요.

### <a name="workspace-link"></a>작업 영역 연결

-  404 -- 작업 영역을 찾을 수 없습니다. 지정한 작업 영역이 없거나 삭제되었습니다.
-  409 -- 작업 영역 연결 또는 연결 해제 작업이 진행 중입니다.
-  400 -- 클러스터를 찾을 수 없거나 지정한 클러스터가 없거나 삭제되었습니다. 해당 이름을 사용하여 클러스터를 만들려고 하며, 충돌하는 경우 클러스터는 14일 동안 일시 삭제 상태가 됩니다. 지원 서비스에 문의하여 복구할 수 있습니다.

### <a name="workspace-unlink"></a>작업 영역 연결 해제
-  404 -- 작업 영역을 찾을 수 없습니다. 지정한 작업 영역이 없거나 삭제되었습니다.
-  409 -- 작업 영역 연결 또는 연결 해제 작업이 진행 중입니다.

## <a name="next-steps"></a>다음 단계

- [Log Analytics 전용 클러스터 청구](./manage-cost-storage.md#log-analytics-dedicated-clusters)에 대해 알아보기
- [Log Analytics 작업 영역의 적절한 디자인](../logs/design-logs-deployment.md)에 대해 알아보기
